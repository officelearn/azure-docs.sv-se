---
title: Azure Service Bus dubblettmeddelandet identifiering | Microsoft Docs
description: Identifiera duplicerade Service Bus-meddelanden
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
ms.openlocfilehash: c419ee1eec9e451cad835d8b4a56818101dc853a
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856221"
---
# <a name="duplicate-detection"></a>Dubblettidentifiering

Om ett program misslyckas på grund av ett allvarligt fel omedelbart efter att den skickar ett meddelande och startats om programinstansen tror felaktigt att den tidigare meddelandeleverans inte förekommer, att skicka efterföljande visas samma meddelande visas två gånger i systemet.

Det är också möjligt för ett fel på klienten eller nätverket ska ske ett ögonblick tidigare och för en skickade meddelandet gick inte att vara allokerad till kön med bekräftelsen returneras till klienten. Det här scenariot gör klienten osäker på hur resultatet av åtgärden Skicka.

Identifiering av dubbletter tar osäkra utanför dessa situationer genom att aktivera avsändaren skicka om samma meddelande och kön eller ämnet tar du bort alla dubbletter.

När du aktiverar dubblettidentifiering hjälper dig att hålla reda på program-kontrollerade *MessageId* för alla meddelanden som skickas till en kö eller ämne under ett angivet tidsintervall. Om några nya meddelanden ska skickas med *MessageId* som loggades under tidsperioden för meddelandet har rapporterats som godkänt (skicka åtgärden lyckas), men det nyligen skickade meddelandet ignoreras och tas bort direkt. Inga andra delar av meddelandet än den *MessageId* betraktas.

Programkontroll på identifieraren är avgörande, eftersom endast som tillåter programmet att koppla den *MessageId* till en business processen kontext som det förutsägbart rekonstrueras när ett fel uppstår.

För en affärsprocess som flera meddelanden skickas under hantering av vissa Programkontext den *MessageId* kan vara en kombination av programnivå kontext-ID, till exempel ett inköpsordernummer och ämne för meddelandet, till exempel **12345.2017/betalning**.

Den *MessageId* alltid kan vara en GUID, men det inställningen identifierare som affärsprocessen ger förutsägbar repeterbarhet som önskas för att använda funktionen dubblettidentifiering effektivt.

## <a name="enable-duplicate-detection"></a>Aktivera dubblettidentifiering

I portalen, funktionen är aktiverad när entitet skapas med den **aktivera dubblettidentifiering** kryssrutan som är inaktiverat som standard. Inställningen för att skapa nya ämnen är likvärdiga.

![][1]

> [!IMPORTANT]
> Du kan inte aktivera/inaktivera identifiering av dubbletter när kön har skapats. Du kan bara göra det när kön skapades. 

Programmässigt, du anger flaggan med den [QueueDescription.requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) egenskapen på fullständiga framework .NET API. Värdet anges med API: et för Azure Resource Manager med den [queueProperties.requiresDuplicateDetection](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) egenskapen.

Som standard den dubblettidentifiering historik över 30 sekunder för köer och ämnen med ett högsta värde på sju dagar. Du kan ändra den här inställningen i egenskapsfönstret queue och topic i Azure-portalen.

![][2]

Programmässigt, du kan konfigurera storleken på fönstret dubblettidentifiering meddelande-ID: n som finns kvar, med hjälp av den [QueueDescription.DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) egenskap med det fullständiga .NET Framework-API . Värdet anges med API: et för Azure Resource Manager med den [queueProperties.duplicateDetectionHistoryTimeWindow](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) egenskapen.

Aktivera dubblettidentifiering och storleken på fönstret påverka direkt dataflödet kö (och avsnittet), eftersom alla inspelade meddelande-ID: n måste matchas mot den nyligen skickade meddelandeidentifieraren.

Att hålla den fönstret små innebär att färre meddelande-ID: n måste vara kvar och matchas och dataflöde är påverkas mindre. För stora dataflöden entiteter som kräver dubblettidentifiering, bör du behålla fönstret så mycket som möjligt.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om Service Bus-meddelanden, finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
