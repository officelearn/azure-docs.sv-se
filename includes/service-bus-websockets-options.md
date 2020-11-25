---
title: ta med fil
description: ta med fil
services: service-bus-messaging
author: clemensv
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/24/2020
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: fe205524c5aef6a43771cc09c810da217678d108
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022156"
---
Protokoll alternativet AMQP-över-WebSockets körs via port TCP 443 precis som HTTP/REST API, men är i övrigt identiskt med enkel AMQP. Det här alternativet har en högre första anslutnings fördröjning på grund av extra hand skakning och något mer som kompromisser för att dela HTTPS-porten. Om det här läget är valt räcker TCP-port 443 för kommunikation. Med följande alternativ kan du välja läget för enkel AMQP eller AMQP WebSockets:

| Språk | Alternativ   |
| -------- | ----- |
| .NET     | [ServiceBusConnection. TransportType](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype?view=azure-dotnet) -egenskapen med [TransportType. AMQP](/dotnet/api/microsoft.azure.servicebus.transporttype?view=azure-dotnet) eller [TransportType. AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype?view=azure-dotnet) |
| Java     | [com. Microsoft. Azure. Service Bus. ClientSettings](/java/api/com.microsoft.azure.servicebus.clientsettings.clientsettings?view=azure-java-stable) med [com. Microsoft. Azure. Service Bus. primitivs. TransportType. AMQP](/java/api/com.microsoft.azure.servicebus.primitives.transporttype?view=azure-java-stable) eller [com.Microsoft.Azure.ServiceBus.Primitives.TransportType.AMQP_WEB_SOCKETS](/java/api/com.microsoft.azure.servicebus.primitives.transporttype?view=azure-java-stable) |
| Node  | [ServiceBusClientOptions](/javascript/api/@azure/service-bus/servicebusclientoptions?view=azure-node-latest) har ett `webSocket` konstruktor argument. |
| Python | [ServiceBusClient.transport_type](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.ServiceBusClient) med [TransportType. AMQP](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) eller [TransportType. AmqpOverWebSocket](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) |