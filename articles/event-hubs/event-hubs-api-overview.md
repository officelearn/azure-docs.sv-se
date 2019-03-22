---
title: Översikt över Azure Event Hubs API | Microsoft Docs
description: 'Översikt över tillgängliga Azure Event Hubs API: er'
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
ms.openlocfilehash: dcfc2480cd9a62b57255163132373998db328034
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58075807"
---
# <a name="available-event-hubs-apis"></a>Tillgängliga Event Hubs API: er

Den här artikeln beskriver uppsättningen tillgängliga API-klienter som du kan använda för att hantera Event Hubs-resurser.

## <a name="runtime-apis"></a>Runtime-API: er

I följande avsnitt beskrivs alla tillgängliga Azure Event Hubs runtime-klienter. Några av dessa bibliotek är också begränsad hanteringsfunktioner, men det finns också [specifika bibliotek](#management-apis) för hanteringsåtgärder. Dessa bibliotek core fokus är att skicka och ta emot meddelanden från en event hub.

Läs mer om aktuell status för varje runtime-bibliotek, [ytterligare information](#additional-information).

| Språk/plattform | Klientpaketet | EventProcessorHost paket | Lagringsplats |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) | [GitHub](https://github.com/azure/azure-event-hubs-dotnet) |
| .NET Framework | [NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) | Gäller inte |
| Java | [Maven 3.](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) | [Maven 3.](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) | [GitHub](https://github.com/Azure/azure-event-hubs-java) |
| Node | [NPM](https://www.npmjs.com/package/azure-event-hubs) | Gäller inte | [GitHub](https://github.com/Azure/azure-event-hubs-node) |
| C | Gäller inte | Gäller inte | [GitHub](https://github.com/Azure/azure-event-hubs-c) |

### <a name="additional-information"></a>Ytterligare information

#### <a name="net"></a>.NET

.NET-ekosystem har flera körningar, så det finns flera .NET-bibliotek för Event Hubs. .NET Standard-biblioteket kan köras med .NET Core eller .NET Framework, medan i .NET Framework-biblioteket kan bara köras i en miljö med .NET Framework. Mer information om .NET Framework-versioner finns i [ramverksversioner](https://docs.microsoft.com/dotnet/articles/standard/frameworks).

#### <a name="node"></a>Node

Den [Node.js-bibliotek](https://github.com/Azure/azure-event-hubs-node) är för närvarande i förhandsversion och underhålls som ett sida-projekt av Microsoft-anställda och externa deltagare. Alla bidrag inklusive källkoden är välkomna och kommer att granskas.

## <a name="management-apis"></a>API:er för hantering

I följande tabell visas alla tillgängliga bibliotek för management-specifika. Ingen av dessa bibliotek innehåller runtime operations och är för att hantera Händelsehubbar entiteter.

| Språk/plattform | Hanteringspaket | Lagringsplats |
| --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) | [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/ResourceManagement/EventHub) |

## <a name="next-steps"></a>Nästa steg
Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Event Hubs-översikt](event-hubs-what-is-event-hubs.md)
* [Skapa en Event Hub](event-hubs-create.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
