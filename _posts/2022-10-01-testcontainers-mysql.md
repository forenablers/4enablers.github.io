---
categories: Testing, dotnet, mysql
title: Integration Testing with TestContainers
date: 2022-10-01T00:00:00Z
author: Borys Generalov
---

<!-- 
TODO:
inform the readers what it is about
who is the audience?
explain what we will do in each section
walk through creating the integration tests and a sample application
summarize the learnings
use the 2nd form and make it as developer to developer conversation 
-->

Integration testing is a critical aspect of software development. It involves testing the interaction `between various components` of a system to ensure that they are working together as expected. In contrast, unit testing is a testing technique that involves testing `individual components of a system`, without considering how they interact with other components.

This blog post walks you through how to use [Testcontainers for .NET](https://dotnet.testcontainers.org/) to write the  integration tests for your .NET application that uses a MySQL database.

## Integration testing with databases

Databases often store critical data, such as customer data, legal records or financial information. If there is an error in the application's interaction with the database, it can have serious consequences. Although unit testing is a useful technique to cover the application and domain logic, it is often not practical or even possible for databases. Unlike application code, which can be tested using mock objects and stubs, it is difficult to create a mock database and mock behaviours that accurately simulates a real database. For example, we cannot simulate insert or update operations and we have no control over the database internals to setup mock responses. Moreover, databases are still prone to unexpected events like data type mismatches, insertion failures caused by duplicate keys, deadlocks, and many other things.

Integration testing can help: 
* catch issues related to database schema changes, query performance, and transaction management.
* identify issues related to database migrations

Integration testing can be challenging because it often requires setting up a complex environment that includes multiple components. 
One of the main pain points is setting up the environment for testing. 
This includes setting up the database, installing the database driver, and configuring the connection string. 
Additionally, running tests against a real database can be slow, unreliable, and difficult to reproduce.

This article will explore how to use testcontainers.net to implement integration tests with MySQL in a Docker container.

## Using Testcontainers.net

Testcontainers.net is an open-source library that helps to simplify integration testing. It enables developers to create disposable instances of containers for testing applications, including databases, message brokers, and other services. Using Testcontainers, developers can set up a complete environment for integration testing with minimal effort.

Testcontainers.net offers several benefits, including:

Automated and simplified setup of the environment
Repeatability and consistency of tests
Fast and reliable testing
Isolation of the test environment from the developer's machine

Using Testcontainers.net for Integration Testing with .NET and MySQL:

To use Testcontainers.net for integration testing with .NET and MySQL, you will need to follow these steps:

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

Integration testing is a critical aspect of software development. However, setting up the environment for testing can be complex and time-consuming. Testcontainers.net is an open-source library that simplifies integration testing by enabling developers to create disposable instances of containers for testing applications.

In this article, we explored how to use Testcontainers.net to implement integration tests with MySQL in a Docker container. We discussed the pain points of integration testing, and how Testcontainers.net solves them. We also provided examples of how to use Testcontainers.net for integration testing with .NET and MySQL.

Using Testcontainers.net can save developers time and effort in setting up integration testing environments, and enable fast, reliable, and repeatable testing. If you want to learn more about Testcontainers.net, you can visit

## Next steps

TBD:

* 
