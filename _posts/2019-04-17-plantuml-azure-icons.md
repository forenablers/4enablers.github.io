---
categories: Architecture, PlantUML
title: Using PlantUML with custom images  
date: 2019-04-17T00:00:00Z
author: Borys Generalov
---

# Intro

Architectural diagrams help people understand a system, behaviour and interactions. However, to be effective, the diagrams must be clear and easy to understand. Utilizing icons that are both familiar and user-friendly can significantly enhance understanding, even for those unfamiliar with UML.

In this post, we explore how integrating custom icons into PlantUML diagrams can significantly enhance their effectiveness in representing cloud architectures. The default styling of PlantUML, while functional, often lacks the visual clarity needed for complex cloud systems. By incorporating custom icons, we can create diagrams that are not only more visually appealing but also provide a clearer representation of services, for instance Azure, making them invaluable tools for cloud architects and developers.

## PlantUML

PlantUML[https://plantuml.com/faq] is an excellent tool that allows you to make different types of diagrams with ease. It stands out for its simple and intuitive syntax, which makes it especially useful in the early stages of architectural design where changes are frequent. 

PlantUML's approach to 'diagrams as code' allows for quick updates and iterations, making it a highly efficient choice for dynamic projects. This method also ensures easy maintenance and updating of diagrams, as changes can be made quickly in the code without the need to manually adjust the visual elements.

## Getting started

Now that we've outlined PlantUML capabilities and its approach to creating diagrams, let's move to a hands-on example. We'll see how a diagram looks when rendered with the default settings. Navigate to the [PlantText WebSite](https://planttext.com) and enter the following code snippet:

``` bash
Client -> apim
apim -right-> backend
apim -right-> backend
```

![Default view]({{site.baseurl}}/assets/plantuml-azure-icons/default.png)

Next, we'll apply custom images to see how they compare with the default settings. By using this new snippet, we'll incorporate the familiar look and feel of Azure icons into our diagram, demonstrating a noticeable difference. Let's proceed with the following code snippet:

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

This will render the diagram as shown below, illustrating the visual impact of the changes we've made:

![Getting Started]({{site.baseurl}}/assets/plantuml-azure-icons/getting-started.png)

## Advanced example

In this more complex example, we define reusable components and the benefits of using custom icons become even more evident. With custom icons, such as those for Azure functions and API Management (APIM), the diagram becomes instantly recognizable. You can easily identify each element by its icon, reducing the need to rely on text labels for understanding.

![Advanced example]({{site.baseurl}}/assets/plantuml-azure-icons/advanced-example.png)

Below is the code corresponding to the diagram above:

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

## Summary

In this article, we explored how combining PlantUML with custom icons can significantly enhance architectural diagrams, especially for cloud systems. By using familiar Azure icons and embracing PlantUML's 'diagrams as code' approach, we saw how technical documentation can be transformed into visually appealing and easily comprehensible representations.

We discovered that custom icons not only make diagrams look better but also improve their clarity. This makes them valuable for presentations, understanding complex architectures, and collaboration among team members, regardless of their level of experience with UML.

As we move forward, you can further enhance your diagramming skills with the help of the following resources:

* [PlantUML guide](http://plantuml.com/guide)
* [Azure-PlantUML with sprites](https://github.com/RicardoNiepel/Azure-PlantUML)
* [PlantUML Azure Icons](https://github.com/bgener/plantuml-azure-icons)
