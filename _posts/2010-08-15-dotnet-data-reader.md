---
categories: dotnet
title: .Net Custom IDataReader
date: "2010-08-15T00:00:00Z"
author: Borys Generalov
---

Recently I had an interesting task in the scope of the project. The requirements were to transfer the data from multiple ODBC sources into the destination database. One of the tasks states that we need to implement a way to distinguish the data source, i.e. DBA working with the destination database should easily see where the data came from, that is SQL Server, Oracle, Excel, or whatever else.

I will skip the rest of the unnecessary details as they are making things complicated. Keeping in mind that we were reading the source data through the ADO.NET data reader, I came up with the idea to implement a custom data reader which wraps the regular instance of IDataReader and adds some custom functionality like batching support and additional columns.

## Adding more columns

To add additional columns, we need to inherit from System.Data.IDataReader and implement the method which maintains extra columns:

```csharp
public class CustomDataReader : IDataReader
{
 private IDataReader _reader;
 private string _source;
 private DataTable _innerTable;
 
 public CustomDataReader(string source, IDataReader reader)
 {
  _source = source;
  _reader = reader;
  initInnerTable();
 }
 
 void initInnerTable()
 {
  _innerTable = new DataTable();

  //adding additional column
  column = new DataColumn { 
   ColumnName = "metaSource", 
   AllowDBNull = true,
   DataType = typeof(string)
  };
  _innerTable.Columns.Add(column);

  //adding a value for new column
  var row = _innerTable.NewRow();
  row[column] = _source;
  _innerTable.Rows.Add(row);
 }
 ...
}
```

Note that we encapsulated the original data reader as well. Now we need to override a few methods, like in the following code:

```csharp
public int FieldCount
{
 get
 {
  int count = _innerTable.Columns.Count;
  return _reader.FieldCount + count;
 }
}

public bool Read()
{
 return _reader.Read();
}

public object GetValue(int i)
{
 return IIfExtraColumn<object>(i, index =>
 {
  return _innerTable.Rows[0][index];
 },
 () =>
 {
  return _reader.GetValue(i);
 });
}

private T IIfExtraColumn<T>(int i, 
 Func<int, T> trueFunc, Func<T> falseFunc) 
{
 int fieldCount = _reader.FieldCount;
 return (i >= fieldCount) ? 
  trueFunc(i - fieldCount) : falseFunc();
}

And here is the excerpt from calling code:
var dr = serializer.Deserialize(message.DataStream);
using (var reader = new CustomDataReader(message.SourceName, dr))
{
 //push data into database
 DataHelper.BulkInsert(fullTableName, reader);
}
```

## Batching support

The simplest approach to implement the batching support is as follows:

```csharp
while(true)
{
 int count = 0;
 bool canRead = reader.Read();
 while(canRead)
 {
  canRead = reader.Read();
  count++;
  if (count >= batchSize)
   break;
 }
 
 if (!canRead)
  break;
}
```

However, we have already implemented a custom data reader. How about moving the batch support inside our custom data reader? The idea is similar, we need implement a new method and override a few more as well:

```csharp
class BatchDataReader : IDataReader
{
 private IDataReader _reader;
 private int _batchSize;
 private int _count;
 private bool _pendingRead;
 private int _rowsAffected;

 public int RowsAffected 
 { 
  get { return _rowsAffected; } 
 }
 
 public bool ReadBatch()
 {
  if (!_reader.IsClosed)
  {
   _pendingRead = _reader.Read();
   _count++;
  }
  return _pendingRead;
 }

 public bool Read()
 {
  if (_pendingRead)
  {
   _pendingRead = false;
   return true;
  }

  if (_batchSize > 0 && _count >= _batchSize)
  {
   _count = 0;
   return false;
  }

  _count++;
  _rowsAffected = _count;
  return _reader.Read();
 }

 public bool NextResult()
 {
  //multiple result sets are not supported
  return false;

  //NOTE: do not use commented out code, otherwise
  //_reader gets closed once first batch of rows has been read
  /*return _reader.NextResult();*/
 }

 public object this[int i]
 {
  get { return _reader[i]; }
 }
}
```

And the piece of code which loads and transfers the data in batches:

```csharp
var dr = GetOdbcDataReader();
using (var reader = new BatchDataReader(batchSize, dr))
{
 int batchCount = 0;
 while (true)
 {
  Stream stream = null;
  try
  {
   bool canRead = reader.ReadBatch();
   if (!canRead)
    break;

   //serialize the data
   stream = serializer.Serialize(entityName, reader);

   //upload the streamed data via WCF proxy
   _client.UploadEntityData(stream);
  }
  catch
  {
   reader.Close();

   //interrupt upload of current and next batches
   throw;
  }
  finally
  {
   //ensure the stream is released
   if (stream != null) 
    stream.Close();
  }
 }
}
```

## Final thoughts

That was it. How was it used? We've used it from within the WCF service to transfer a large amount of data, think of a table with the 500 million records where only the index data takes 30Gb. The transfer is resumable as we maintain the last records id of the data set persisted on the destination database. In total, it took us 3-4 days to transfer 30 databases, 200Gb each (on average).
