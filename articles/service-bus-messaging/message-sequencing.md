---
title: Azure Service Bus-ordningsföljd och tidsstämplar | Microsoft Docs
description: Bevara sekvensen för Service Bus-meddelande och ordning med tidsstämplar
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
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 8665d0a1fccecf5521a553a894e2a55e52384ec3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60402727"
---
# <a name="message-sequencing-and-timestamps"></a>Ordningsföljd och tidsstämplar för meddelanden

Ordningsföljd och tidsstämplar finns två funktioner som alltid är aktiverade på alla Service Bus-entiteter och lyfta fram via den [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) och [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) egenskaperna för mottagna eller visat meddelanden.

För de fall där absolut ordningen för meddelanden är betydande och/eller som behöver en konsument en tillförlitlig Unik identifierare för meddelanden, broker stämplar meddelanden med en lucka är kostnadsfria, öka sekvensnummer i förhållande till kön eller ämnet. Sekvensnumret utfärdas för partitionerade enheter i förhållande till partitionen.

Den **SequenceNumber** värdet är ett unikt 64-bitars heltal som tilldelas ett meddelande när den accepteras och lagras av broker och funktioner som dess interna ID: t. För partitionerade enheter visas de översta 16 bitarna partitions-ID. Sekvensnummer som förnyas till noll när 48/64-bitars-intervallet är slut.

Sekvensnumret kan vara betrodda som en unik identifierare eftersom den är tilldelad av en utfärdare av central och neutral, inte av klienter. Det också representerar SANT ordningen på ankomst och är bättre än en tidsstämpel som ett kriterium i ordning, eftersom tidsstämplar inte kanske har tillräckligt hög upplösning extrem Meddelandefrekvens och kan vara föremål för (men minimal) klockan skeva i situationer där den asynkrona meddelandekön ägarskap övergångar mellan noder.

Absolut ankomst ordningen är viktig, till exempel i företag scenarier där erbjuds ett begränsat antal varor hanteras på basis av in-först-turordning så länge lagret senast; samklang biljettförsäljningar är ett exempel.

Funktionen för tidsstämplar som fungerar som en neutral och betrodd utfärdare som korrekt samlar in exempel ankomsten av ett meddelande som visas i UTC-tid i **EnqueuedTimeUtc** egenskapen. Värdet är användbart om ett affärsscenario är beroende av tidsgränser, till exempel om ett arbetsobjekt skickades på ett visst datum innan midnatt, men bearbetningen är för långt bakom eftersläpningen i kön.

## <a name="scheduled-messages"></a>Schemalagda meddelanden

Du kan skicka meddelanden till en kö eller ämne för fördröjd bearbetning. till exempel för att schemalägga ett jobb ska bli tillgänglig för bearbetning av ett system vid en viss tidpunkt. Den här funktionen inser en tillförlitlig distribuerad tidsbaserade schemaläggare.

Schemalagda meddelanden Materialisera inte i kön tills definierade sätta. Schemalagda meddelanden kan avbrytas innan detta datum. Annulleringen meddelandet tas bort.

Du kan schemalägga meddelanden antingen genom att ange den [ScheduledEnqueueTimeUtc](/dotnet/api/microsoft.azure.servicebus.message.scheduledenqueuetimeutc) egenskapen när du skickar ett meddelande via den vanliga sändande vägen eller uttryckligen med den [ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) API. Det senare returnerar omedelbart schemalagt meddelande **SequenceNumber**, som du senare kan använda för att avbryta schemalagda meddelande om det behövs. Schemalagda meddelanden och deras sekvensnummer kan också identifieras med hjälp av [bläddring bland meddelanden](message-browsing.md).

Den **SequenceNumber** för ett schemalagt meddelande är endast giltigt när meddelandet är i det här tillståndet. Som meddelandet övergår till aktivt läge, meddelandet läggs till i kön som om hade varit i kö på den aktuella snabbmeddelanden, vilket omfattar att tilldela en ny **SequenceNumber**.

Eftersom funktionen är fäst på enskilda meddelanden och meddelanden kan bara placeras i kö en gång, stöder inte återkommande scheman för meddelanden i Service Bus.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om Service Bus-meddelanden, finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)