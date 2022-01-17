---
categories: Misc PlantUML
title: Using PlantUML with custom images  
date: 2019-04-17T00:00:00Z
author: Borys Generalov
---

The PlantUML offers a great way to speed up the creation of architectural diagrams. However, the default rendering might look overwhelming and noisy for non-technical people. Using familiar icons, helps them to understand the diagram without knowing the UML. Here I'd like to showcase how to use png icons rather than sprites.

## Getting started

Navigate to [PlantText WebSite](planttext.com) and use the following snippet:

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
