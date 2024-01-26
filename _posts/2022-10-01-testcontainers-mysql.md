---
categories: Testing, dotnet, sql
title: Integration Testing with TestContainers
date: 2022-10-01T00:00:00Z
author: Borys Generalov
---

Integration testing is a critical aspect of software development. It involves *testing the interaction between various components* of a system to ensure that they are working together as expected. In contrast, unit testing is a testing technique that involves *testing individual components of a system*, without considering how they interact with other components.

This blog post walks you through how to use [Testcontainers for .NET](https://dotnet.testcontainers.org/) to write the  integration tests for your .NET application that uses a SQL Server database. The article is particularly relevant for .NET developers who are looking for tools that can simplify writing the integration tests or are seeking to expand their knowledge on integration testing.

## Integration testing with databases

Databases often store critical data, such as customer data, legal records or financial information. Although unit testing is a useful technique to cover the application logic, it is often not practical or even possible for databases. Unlike application code, which can be tested using mock objects, it is difficult to create a mock database that accurately simulates a real database. Moreover, databases are still prone to unexpected events like data type mismatches, database schema changes, insertion failures, deadlocks, and many others.

Integration testing can be challenging because it often requires setting up a complex environment that includes multiple components. One of the primary challenges is setting up the database with test data, including creating the database, seeding the test data, and configuring the connection string. In addition, testing against a real database is slow and can be unreliable due to potential networking issues that may arise. Furthermore, running tests on a shared database can lead to noisy neighbor effects, where test run may interfere with other tasks, leading to inconsistent and unreliable results.

## Using Testcontainers.net

[Testcontainers for .NET](https://dotnet.testcontainers.org/) is an open-source library available in multiple programming languages, including Java, Go, and .NET. It simplifies integration testing by enabling developers to use Docker containers for each test, so that the tests are isolated from each other and there are no side effects between tests. Testcontainers supports the variety of applications, including databases, message brokers, and other services. Using Testcontainers, developers can quickly set up and tear down complete testing environments with minimal effort.

In the next section we will use a demo project to demonstrate creating the integration tests. The project is a ASP.NET  application that interacts with the database to store and retrieve the list of todo items.

### Prerequisites

To use Testcontainers.net for integration testing with .NET, you will need to follow these steps:

* Git
* VS Code
* Docker

### Setup demo application

Clone the repository [.NET Core MVC sample](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial).

Install the Testcontainers NuGet package:
Copy code
Install-Package Testcontainers

Create a class for your integration tests and inherit from the TestcontainersFixture class:
kotlin
Copy code
public class MyIntegrationTests : TestcontainersFixture
{
}

Define a Testcontainers configuration that specifies the container's image and port mapping:
csharp
Copy code
protected override string DockerImageName => "mysql:8.0.23";
protected override string ContainerName => "mysql-for-integration-tests";
protected override IEnumerable<int> ExposedPorts => new[] { 3306 };

Implement a method that starts the Testcontainers container:
csharp
Copy code
public async Task StartContainer()
{
    await WithDatabaseAsync(async (connectionString) =>
    {
        // Code to test the connection
    });
}

Use the connection string to connect to the database and run your integration tests:
csharp
Copy code
public async Task TestConnection()
{
    await WithDatabaseAsync(async (connectionString) =>
    {
        using (var connection = new MySqlConnection(connectionString))
        {
            await connection.OpenAsync();
            // Code to test the connection
        }
    });
}


In this example, we are using the MySQL Docker image version 8.0.23. We are also exposing port 3306, which is the default port for MySQL.

The WithDatabaseAsync method is a convenience method provided by Testcontainers.net. It starts the container, waits for it to be ready, and passes the connection string to the provided action. In this case, we are testing the connection to the database.

## Summary:

In this article, we explored how to use Testcontainers.net to implement integration tests with SQL Server database in a Docker container. We discussed the pain points of integration testing, and how Testcontainers.net solves them. We also provided examples of how to use Testcontainers.net for integration testing with .NET and SQL Server.

Testcontainers.net is an open-source library that simplifies integration testing by enabling developers to create disposable instances of containers for testing applications. Using Testcontainers.net can save developers time and effort in setting up integration testing environments, and enable fast, reliable, and repeatable testing. If you want to learn more about Testcontainers.net, you can visit

## Next steps

TBD:

* 
