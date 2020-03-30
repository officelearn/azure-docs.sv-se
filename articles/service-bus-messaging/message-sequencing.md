---
title: Sekvensering av Azure Service Bus-meddelandesekvensering och tidsstämplar | Microsoft-dokument
description: I den här artikeln beskrivs hur du bevarar sekvensering och beställning (med tidsstämplar) för Azure Service Bus-meddelanden.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 54d774c00fa650cb9608f46cc07b9d899709eaa5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261663"
---
# <a name="message-sequencing-and-timestamps"></a>Ordningsföljd och tidsstämplar för meddelanden

Sekvensering och tidsstämpling är två funktioner som alltid är aktiverade på alla Service Bus-entiteter och yta via [egenskaperna SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) och [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) för mottagna eller bläddrade meddelanden.

För de fall där absolut ordning av meddelanden är betydande och / eller där en konsument behöver en pålitlig unik identifierare för meddelanden, mäklaren stämplar meddelanden med en gap-fri, ökande sekvensnummer i förhållande till kön eller ämne. För partitionerade entiteter utfärdas sekvensnumret i förhållande till partitionen.

**SequenceNumber-värdet** är ett unikt 64-bitars heltal som tilldelats ett meddelande när det accepteras och lagras av mäklaren och fungerar som dess interna identifierare. För partitionerade entiteter återspeglar de översta 16 bitarna partitionsidentifieraren. Sekvensnummer rulla över till noll när 48/64-bitarsintervallet är uttömt.

Sekvensnumret kan lita på som en unik identifierare eftersom det tilldelas av en central och neutral myndighet och inte av klienter. Det representerar också den sanna ordningen för ankomst, och är mer exakt än en tidsstämpel som ett orderkriterium, eftersom tidsstämplar kanske inte har en tillräckligt hög upplösning vid extrema meddelandehastigheter och kan bli föremål för (men minimal) klocka skeva i situationer där mäklaren inte kan ha en tillräckligt hög upplösning vid extrema meddelandehastigheter och kan vara föremål för (men minimal) klocka skeva i situationer där mäklaren inte kan ha en tillräckligt hög upplösning på extrema meddelandehastigheter och kan vara föremål för (men minimal) klocka skeva i situationer där mäklaren ägarövergångar mellan noder.

Den absoluta ankomstordern spelar till exempel roll i affärsscenarier där ett begränsat antal erbjudna varor serveras enligt först till kvarn-principen så länge lagret räcker. konsertbiljettförsäljning är ett exempel.

Tidsstämplingsfunktionen fungerar som en neutral och pålitlig myndighet som exakt fångar UTC-tiden för ett meddelande, vilket återspeglas i egenskapen **EnqueuedTimeUtc.** Värdet är användbart om ett affärsscenario beror på tidsgränser, till exempel om en arbetsuppgift har skickats på ett visst datum före midnatt, men bearbetningen ligger långt efter köeftersläpningen.

## <a name="scheduled-messages"></a>Schemalagda meddelanden

Du kan skicka meddelanden till en kö eller ett ämne för fördröjd bearbetning, till exempel för att schemalägga ett jobb som ska bli tillgänglig för bearbetning av ett system vid en viss tidpunkt. Den här funktionen realiserar en tillförlitlig distribuerad tidsbaserad schemaläggare.

Schemalagda meddelanden materialiseras inte i kön förrän den definierade kötiden. Innan dess kan schemalagda meddelanden avbrytas. Annullering tar bort meddelandet.

Du kan schemalägga meddelanden antingen genom att ange egenskapen [ScheduledEnqueueTimeUtc](/dotnet/api/microsoft.azure.servicebus.message.scheduledenqueuetimeutc) när du skickar ett meddelande via den vanliga skicka sökvägen, eller uttryckligen med [ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) API. Det senare returnerar omedelbart det schemalagda meddelandets **SequenceNumber**, som du senare kan använda för att avbryta det schemalagda meddelandet om det behövs. Schemalagda meddelanden och deras sekvensnummer kan också identifieras med hjälp av [meddelandebläddring](message-browsing.md).

**SequenceNumber** för ett schemalagt meddelande är bara giltigt medan meddelandet är i det här tillståndet. När meddelandet övergår till det aktiva tillståndet läggs meddelandet till i kön som om det hade köats i det aktuella ögonblicket, vilket inkluderar tilldelning av ett nytt **SequenceNumber**.

Eftersom funktionen är förankrad i enskilda meddelanden och meddelanden endast kan köas en gång, stöder Service Bus inte återkommande scheman för meddelanden.

## <a name="next-steps"></a>Nästa steg

Mer information om Service Bus-meddelanden finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med servicebussköer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)