---
title: Översikt över Azure Event Hubs API | Microsoft Docs
description: 'Översikt över tillgängliga Azure Event Hubs API: er'
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 3f221a0c-182d-4e39-9f3d-3a3c16c5c6ed
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2017
ms.author: sethm
ms.openlocfilehash: abd44fd0c9cbfab2365b1552e3cd90e84a5348d7
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
ms.locfileid: "25957535"
---
# <a name="available-event-hubs-apis"></a>Tillgängliga Event Hubs API: er

Den här artikeln beskriver uppsättningen tillgängliga API-klienter som du kan använda för att hantera Händelsehubbar resurser.

## <a name="runtime-apis"></a>Runtime-API: er

Följande är en beskrivning av alla tillgängliga Azure Event Hubs runtime-klienter. Några av dessa bibliotek är också begränsad hanteringsfunktioner, men det finns också [vissa bibliotek](#management-apis) dedikerad för hanteringsåtgärder. Core fokuserar dessa bibliotek på att skicka och ta emot meddelanden från en händelsehubb.

Se [ytterligare information](#additional-information) för mer information om den aktuella statusen för varje körningsbiblioteket.

| Språk-plattformen | Klientpaketet | Paketet EventProcessorHost | Lagringsplats |
| --- | --- | --- | --- |
| Standard för .NET | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) | [GitHub](https://github.com/azure/azure-event-hubs-dotnet) |
| .NET framework | [NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) | Saknas |
| Java | [Maven 3.](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) | [Maven 3.](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) | [GitHub](https://github.com/Azure/azure-event-hubs-java) |
| Node | [NPM](https://www.npmjs.com/package/azure-event-hubs) | Saknas | [GitHub](https://github.com/Azure/azure-event-hubs-node) |
| C | Saknas | Saknas | [GitHub](https://github.com/Azure/azure-event-hubs-c) |

### <a name="additional-information"></a>Ytterligare information

#### <a name="net"></a>.NET
.NET-ekosystemet har flera körningar, så det finns flera .NET-bibliotek för Händelsehubbar. .NET standardbibliotek kan köras med .NET Core eller .NET Framework, medan biblioteket för .NET Framework kan endast köras i en miljö med .NET Framework. Läs mer på .NET Frameworks [framework-versioner](https://docs.microsoft.com/dotnet/articles/standard/frameworks#framework-versions).

#### <a name="node"></a>Node

Node.js-bibliotek är för närvarande under förhandsgranskning och hanteras som ett sida-projekt av Microsofts anställda och externa deltagare. Alla bidrag inklusive källkoden Välkommen och kommer att granskas.

## <a name="management-apis"></a>Management-API: er

Följande är en lista över alla tillgängliga management specifika bibliotek. Ingen av dessa bibliotek innehåller runtime operations och gäller för det enda syftet att hantera Händelsehubbar entiteter.

| Språk-plattformen | Hanteringspaket | Lagringsplats |
| --- | --- | --- | --- |
| Standard för .NET | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) | [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/ResourceManagement/EventHub) |

## <a name="next-steps"></a>Nästa steg
Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Event Hubs-översikt](event-hubs-what-is-event-hubs.md)
* [Skapa en Event Hub](event-hubs-create.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)