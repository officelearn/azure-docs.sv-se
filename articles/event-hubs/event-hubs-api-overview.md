---
title: Översikt över Azure Event Hubs API | Microsoft Docs
description: 'Den här artikeln innehåller en översikt över tillgängliga API: er (körning och hantering) för användning av Azure Event Hubs-tjänsten.'
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 3f221a0c-182d-4e39-9f3d-3a3c16c5c6ed
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/02/2018
ms.author: shvija
ms.openlocfilehash: 08e10996f633d35ffbf946b61937a0e9a76f7227
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162233"
---
# <a name="available-event-hubs-apis"></a>Tillgängliga Event Hubs-API: er

I den här artikeln beskrivs en uppsättning tillgängliga API-klienter som du kan använda för att hantera Event Hubs-resurser.

## <a name="runtime-apis"></a>Körnings-API: er

I följande avsnitt beskrivs alla Azure Event Hubs runtime-klienter som är tillgängliga för närvarande. Vissa av dessa bibliotek innehåller även begränsade hanterings funktioner, men det finns också [särskilda bibliotek](#management-apis) som är reserverade för hanterings åtgärder. Kärn fokus i dessa bibliotek är att skicka och ta emot meddelanden från en Event Hub.

Mer information om aktuell status för varje körnings bibliotek finns i [Ytterligare information](#additional-information).

| Språk/plattform | Klient paket | EventProcessorHost-paket | Databas |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) | [GitHub](https://github.com/azure/azure-event-hubs-dotnet) |
| .NET Framework | [NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) | Saknas |
| Java | [Maven 3.](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) | [Maven 3.](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) | [GitHub](https://github.com/Azure/azure-event-hubs-java) |
| Node | [NPM](https://www.npmjs.com/package/azure-event-hubs) | Saknas | [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) |
| C | Saknas | Saknas | [GitHub](https://github.com/Azure/azure-event-hubs-c) |

### <a name="additional-information"></a>Ytterligare information

#### <a name="net"></a>.NET

.NET-eko systemet har flera körningar, så det finns flera .NET-bibliotek för Event Hubs. Du kan köra .NET standard-biblioteket med hjälp av antingen .NET Core eller .NET Framework, medan .NET Frameworks biblioteket bara kan köras i en .NET Framework miljö. Mer information om .NET Framework-versioner finns i [Framework-versioner](https://docs.microsoft.com/dotnet/articles/standard/frameworks).

#### <a name="node"></a>Node

[JavaScript-biblioteket](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) är för närvarande en för hands version och underhålls som ett Side projekt av Microsofts anställda och externa bidrags givare. Alla bidrag inklusive käll koden är Välkomst och kommer att granskas.

## <a name="management-apis"></a>API:er för hantering

I följande tabell visas alla aktuella bibliotek som är tillgängliga för hantering. Inget av dessa bibliotek innehåller körnings åtgärder och är enbart för att hantera Event Hubs entiteter.

| Språk/plattform | Hanterings paket | Databas |
| --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) | [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/ResourceManagement/EventHub) |

## <a name="next-steps"></a>Nästa steg
Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Event Hubs-översikt](event-hubs-what-is-event-hubs.md)
* [Skapa en Event Hub](event-hubs-create.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
