<properties
    pageTitle="Skapa ett Service Bus-namnområde med Azure Portal | Microsoft Azure"
    description="För att komma igång med Service Bus, behöver du ett namnområde. Så här skapar du ett med hjälp av Azure Portal."
    services="service-bus"
    documentationCenter=".net"
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="08/22/2016"
    ms.author="jotaub"/>


# Skapa ett Service Bus-namnområde med Azure Portal

Ett namnområde är en gemensam behållare för alla dina meddelandekomponenter. Flera köer och ämnen kan finnas i ett enda namnområde och namnområden fungerar ofta som programbehållare. Det finns för närvarande två sätt att skapa ett Service Bus-namnområde.

1.  Azure Portal (den här artikeln)

2.  [Resource Manager-mallar][create-namespace-using-arm]

## Skapa ett namnområde i Azure Portal

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

Grattis! Du har nu skapat ett namnområde för meddelandetjänsten i Service Bus.

## Nästa steg

Kolla in våra [GitHub-databaser](https://github.com/Azure-Samples/azure-servicebus-messaging-samples][github-samples) med exempel som visar några av de mer avancerade funktionerna i meddelandetjänsten i Azure Service Bus.

[create-namespace-using-arm]: ../service-bus-messaging/service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples


<!--HONumber=Sep16_HO3-->


