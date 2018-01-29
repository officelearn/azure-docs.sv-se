---
title: "Azure Service Bus meddelandet ordningsföljd och tidsstämplar | Microsoft Docs"
description: "Bevara meddelandesekvens för Service Bus och ordning med tidsstämplar"
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: sethm
ms.openlocfilehash: a67a9d01f686c6aa8a569239667ae14d7bf087a9
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
---
# <a name="message-sequencing-and-timestamps"></a>Meddelandet ordningsföljd och tidsstämplar

Sekvensering och tidsstämplar är två funktioner som alltid är aktiverade på alla Service Bus-entiteter och ansluta via den [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) och [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) mottagna eller visat egenskaper meddelanden.

För de fall där absolut ordningsföljden av meddelanden är viktig och/eller som behöver en konsument en tillförlitlig Unik identifierare för meddelanden, broker stämplar meddelanden med en lucka utan, öka sekvensnumret i förhållande till den kö eller ett ämne. För partitionerade enheter utfärdas sekvensnumret i förhållande till partitionen.

Den **SequenceNumber** värde är ett unikt 64-bitars heltal som tilldelats ett meddelande som accepteras och lagras av broker och funktioner som dess interna identifierare. De översta 16 bitarna avspeglar partitions-ID för partitionerade enheter. Sekvensnummer rulla över noll när 48/64-bitars-intervallet är slut.

Sekvensnumret kan vara betrott som en unik identifierare eftersom den är tilldelad av en central och neutral utfärdare och inte av klienter. Den också representerar ordningen som SANT för ankomst och exaktare än en tidsstämpel som ett ordningsvillkor eftersom tidsstämplar inte kanske har tillräckligt hög upplösning extrema Meddelandefrekvens och kan vara föremål för (men minimal) klockavvikelser i situationer där Service broker ägarskap övergångar mellan noder.

Absolut ankomst ordningen är viktig, till exempel i företag scenarier där ett begränsat antal erbjuds varor hanteras på grundval första komma-principen när leveranser senast; samklang Biljettförsäljning är ett exempel.

Tidsstämplar-funktionen fungerar som en neutral och betrodd utfärdare som korrekt samlar in ankomsten av ett meddelande som visas i UTC-tid i **EnqueuedTimeUtc** egenskapen. Värdet är användbart om en affärsscenario beror på tidsgränser, till exempel om ett arbetsobjekt har skickats för ett visst datum före midnatt, men bearbetningen är mycket eftersläpningen i kön.

## <a name="scheduled-messages"></a>Schemalagda meddelanden

Du kan skicka meddelanden till en kö eller ett ämne för fördröjd bearbetning. till exempel för att schemalägga ett jobb ska bli tillgänglig för bearbetning av ett system vid en viss tidpunkt. Den här funktionen inser en tillförlitlig distribuerade tidsbaserade Schemaläggaren.

Schemalagda meddelanden Materialisera inte i kö tills definierade sätta. Schemalagda meddelanden kan avbrytas innan som helst. Annulleringen tar bort meddelandet.

Du kan schemalägga meddelanden antingen genom att ange den [ScheduledEnqueueTimeUtc](/dotnet/api/microsoft.azure.servicebus.message.scheduledenqueuetimeutc) egenskapen när du skickar ett meddelande via vanlig sändande vägen eller explicit med den [ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) API. Denna returnerar omedelbart schemalagda meddelandet **SequenceNumber**, som du senare kan använda för att avbryta schemalagda meddelandet om det behövs. Schemalagda meddelanden och deras sekvensnummer kan också identifieras med hjälp av [meddelandet surfning](message-browsing.md).

Den **SequenceNumber** för meddelandet schemalagda är endast giltig när meddelandet finns i det här tillståndet. Som meddelande-övergår till tillståndet active meddelandet läggs till i kön som om hade inte varit i kö på det aktuella ögonblick som omfattar att tilldela en ny **SequenceNumber**.

Eftersom funktionen är fäst på enskilda meddelanden och meddelanden kan bara placeras i kö en gång, stöder inte återkommande scheman för meddelanden i Service Bus.

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig mer om Service Bus-meddelanden, finns i följande avsnitt:

* [Service Bus-grunder](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)