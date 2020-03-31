---
title: Api-översikt för Azure Event Hubs | Microsoft-dokument
description: Den här artikeln innehåller en översikt över tillgängliga API:er (körning och hantering) för att använda Azure Event Hubs-tjänsten.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162233"
---
# <a name="available-event-hubs-apis"></a>API:er för tillgängliga eventhubbar

I den här artikeln beskrivs den uppsättning tillgängliga API-klienter som du kan använda för att hantera resurser för eventhubbar.

## <a name="runtime-apis"></a>Körnings-API:er

I följande avsnitt beskrivs alla tillgängliga Azure Event Hubs-körningsklienter. Vissa av dessa bibliotek innehåller även begränsade hanteringsfunktioner, men det finns också [specifika bibliotek](#management-apis) som är avsedda för hanteringsåtgärder. Kärnfokus för dessa bibliotek är att skicka och ta emot meddelanden från en händelsehubb.

Mer information om aktuell status för varje körningsbibliotek finns i [ytterligare information](#additional-information).

| Språk/plattform | Klientpaketet | EventProcessorHost-paketet | Lagringsplats |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) | [GitHub](https://github.com/azure/azure-event-hubs-dotnet) |
| .NET Framework | [NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) | Ej tillämpligt |
| Java | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) | [GitHub](https://github.com/Azure/azure-event-hubs-java) |
| Node | [NPM (NPM)](https://www.npmjs.com/package/azure-event-hubs) | Ej tillämpligt | [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) |
| C | Ej tillämpligt | Ej tillämpligt | [GitHub](https://github.com/Azure/azure-event-hubs-c) |

### <a name="additional-information"></a>Ytterligare information

#### <a name="net"></a>.NET

.NET-ekosystemet har flera körningar, så det finns flera .NET-bibliotek för eventhubbar. .NET Standard-biblioteket kan köras med antingen .NET Core eller .NET Framework, medan .NET Framework-biblioteket bara kan köras i en .NET Framework-miljö. Mer information om .NET Framework-versioner finns i [ramversioner](https://docs.microsoft.com/dotnet/articles/standard/frameworks).

#### <a name="node"></a>Node

[JavaScript-biblioteket](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) är för närvarande i förhandsversion och underhålls som ett sidoprojekt av Microsoft-anställda och externa bidragsgivare. Alla bidrag inklusive källkod är välkomna och kommer att ses över.

## <a name="management-apis"></a>API:er för hantering

I följande tabell visas alla tillgängliga hanteringsspecifika bibliotek. Inget av dessa bibliotek innehåller körningsåtgärder och är endast avsett att hantera Event Hubs-entiteter.

| Språk/plattform | Hanteringspaket | Lagringsplats |
| --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) | [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/ResourceManagement/EventHub) |

## <a name="next-steps"></a>Nästa steg
Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Översikt över Event Hubs](event-hubs-what-is-event-hubs.md)
* [Skapa en händelsehubb](event-hubs-create.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
