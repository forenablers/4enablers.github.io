---
categories: Architecture, PlantUML
title: Using PlantUML with custom images  
date: 2019-04-17T00:00:00Z
author: Borys Generalov
---

PlantUML[https://plantuml.com/faq] is an excellent tool that allows you to make different types of diagrams with ease. It uses a simple and understandable syntax, making it an ideal choice for rapidly creating architectural diagrams that are subject to change, especially during the initial design stages.

Architectural diagrams help people understand a system, behaviour and interactions. However, to be effective, the diagrams must be clear and easy to understand. The default rendering of PlantUML can often look overwhelming and noisy, especially for non-technical people. Using familiar and friendly icons, helps them to understand the diagram without knowing the UML. Here I'd like to showcase how to use png icons rather than sprites.

## Getting started

Let's use the sample code below and see the result rendered by default:

``` bash
Client -> apim
apim -right-> backend
apim -right-> backend
```

![Default view]({{site.baseurl}}/assets/plantuml-azure-icons/default.png)

Not too bad, but we can do better. Navigate to [PlantText WebSite](https://planttext.com) and use the following snippet:

``` bash
skinparam monochrome true
skinparam defaultTextAlignment center

!define Url https://raw.githubusercontent.com/bgener/plantuml-azure-icons/master/images
rectangle "<img Url/AzureAPIManagement.png>\nAPI Management" as apim
rectangle "<img Url/AzureFunctions.png>\n...\n<img Url/AzureFunctions.png>\nAzure Funcs" as backend
actor Client

Client -> apim
apim -right-> backend
apim -right-> backend
```

This will render the diagram as shown:

![Getting Started]({{site.baseurl}}/assets/plantuml-azure-icons/getting-started.png)

## Advanced example

Here is a more complex example where we define re-usable components

``` bash
skinparam monochrome true
skinparam defaultTextAlignment center

!define Url https://raw.githubusercontent.com/bgener/plantuml-azure-icons/master/images
rectangle "<img Url/LogicApps.png>\nOrchestration Layer" as orchestration
rectangle "<img Url/AzureAppService.png>\nMicroservice2" as service2

!definelong CreateAzureFuncs(alias, caption)
    rectangle "<img Url/AzureFunctions.png>\n...\n<img Url/AzureFunctions.png>\n caption" as alias
!enddefinelong

!definelong CreateAPIManagement(alias, caption)
    cloud "<img Url/AzureAPIManagement.png>\n caption" as alias
!enddefinelong

!definelong CreateAzureServiceBus(alias, caption)
    node "<img Url/AzureServiceBus.png>\n caption" as alias
!enddefinelong

CreateAPIManagement(apim, APIM)
CreateAzureFuncs(funcs, Backend)
CreateAzureServiceBus(servicebus, Service Bus)
CreateAPIManagement(service1, Microservice1)
CreateAzureFuncs(backend, Microservice1)


apim -right-> funcs
apim -right-> funcs
funcs -right-> servicebus

servicebus -down-> orchestration
orchestration -> service1
orchestration -down-> service2
service1 -> backend
```

![Advanced example]({{site.baseurl}}/assets/plantuml-azure-icons/advanced-example.png)

## Resources

* [PlantUML guide](http://plantuml.com/guide)
* [Azure-PlantUML with sprites](https://github.com/RicardoNiepel/Azure-PlantUML)
* [PlantUML Azure Icons](https://github.com/bgener/plantuml-azure-icons)
