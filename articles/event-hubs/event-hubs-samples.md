---
title: Azure Event Hubs-exempel | Microsoft Docs
description: Azure Event Hubs-exempel
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: sethm
ms.openlocfilehash: a581b7039a3631b7f1dc35816175242f892bd7dd
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
ms.locfileid: "29802795"
---
# <a name="event-hubs-samples"></a>Event Hubs-exempel 

Uppsättning Azure Event Hubs exempel visar viktiga funktioner i [Azure Event Hubs](/azure/event-hubs/). Den här artikeln kategoriserar och beskriver tillgängliga, med länkar till varje exemplen.

När detta skrivs finns Händelsehubbar exempel i flera olika platser:

- [Kodexempel för MSDN-utvecklare](https://code.msdn.microsoft.com/site/search?query=event%20hubs&f%5B0%5D.Value=event%20hubs&f%5B0%5D.Type=SearchText&ac=5)
- [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples)

Mer information om olika versioner av .NET Framework finns [ramverk och mål](/dotnet/articles/standard/frameworks).

Flera exempel kommer att läggas till med tiden, så kom tillbaka ofta efter uppdateringar.

## <a name="net-standard"></a>.NET Standard

Följande exempel visar hur du skickar och tar emot händelser med hjälp av den [händelsehubbklient](https://github.com/Azure/azure-event-hubs-dotnet/blob/master/readme.md) för den [.NET standardbibliotek](/dotnet/articles/standard/library).

### <a name="send-events"></a>Skicka händelser 

Den [börjar skicka](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) exempel visas hur du skriver ett .NET Core-konsolprogram som skickar händelser till en händelsehubb.

### <a name="receive-events"></a>Ta emot händelser 

Den [börja ta emot med den värd för händelsebearbetning](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) prov är ett .NET Core-konsolprogram som tar emot meddelanden från en händelsehubb med hjälp av den värd för händelsebearbetning.

## <a name="net-framework"></a>.NET Framework   

De här exemplen visar olika funktioner i Azure Event Hubs, riktad på [biblioteket för .NET Framework](/dotnet/framework/index).
 
### <a name="notify-users-of-events-received"></a>Meddela användare om händelser som tagits emot

Den [AppToNotifyUsers](https://github.com/Azure-Samples/event-hubs-dotnet-user-notifications) exempel meddelar användare av data från sensorer och andra system.

### <a name="get-started-with-event-hubs"></a>Kom igång med händelsehubbar 

Den [Event Hubs komma igång](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097) exempel visas de grundläggande funktionerna i Händelsehubbar, till exempel hur du skapar en händelsehubb, skicka händelser till en händelsehubb och använda händelser med hjälp av den [värd för händelsebearbetning](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) .

### <a name="scale-out-event-processing"></a>Skala ut händelsebearbetning 

Den [skala ut händelsebearbetning](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3) exempel visar hur du använder den [värd för händelsebearbetning](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) att fördela belastningen av Händelsehubbar dataströmmen förbrukning. Den visar hur du implementerar den **EventProcessor** och **EventProcessorFactory** objekt som ska hanteras händelseströmmen. 

## <a name="next-steps"></a>Nästa steg

Läs mer om .NET Framework-versioner genom att gå till följande länkar:

- [Ramverk och mål](/dotnet/articles/standard/frameworks)
- [.NET framework 4.6 och 4.5](/dotnet/framework/index)

Du kan lära dig mer om Händelsehubbar i följande artiklar:

- [Event Hubs-översikt](event-hubs-what-is-event-hubs.md)
- [Event Hubs-funktioner](event-hubs-features.md)
- [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)