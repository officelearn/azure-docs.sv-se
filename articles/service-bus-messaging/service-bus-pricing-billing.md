---
title: Service Bus priser och fakturering | Microsoft Docs
description: Översikt över Service Bus prissättningsstruktur.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 7c45b112-e911-45ab-9203-a2e5abccd6e0
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/21/2017
ms.author: sethm
ms.openlocfilehash: 4faf37394b8e4f6c4e463acb11aea898a29fef80
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448415"
---
# <a name="service-bus-pricing-and-billing"></a>Service Bus priser och fakturering

Azure Service Bus är tillgängligt i Standard och [Premium](service-bus-premium-messaging.md) nivåer. Du kan välja en tjänstnivå för varje namnområde för Service Bus-tjänsten som du skapar och den här valda gäller för alla entiteter som skapats i det här namnområdet.

> [!NOTE]
> Detaljerad information om aktuella priser för Service Bus finns i den [sidan med priser för Azure Service Bus](https://azure.microsoft.com/pricing/details/service-bus/), och [Service Bus vanliga frågor och svar](service-bus-faq.md#pricing).
>
>

Service Bus använder följande taxor för 2 för köer och ämnen/prenumerationer:

1. **Meddelandefunktionsåtgärder**: definieras som API-anrop mot köer eller ämnen/prenumerationer tjänstslutpunkter. Den här mätaren ersätter meddelanden skickas eller tas emot som den primära arbetsenheten fakturerbara användning för köer och ämnen/prenumerationer.
2. **Asynkrona anslutningar**: definierade som det högsta antalet beständiga anslutningar öppnas mot köer, ämnen eller prenumerationer under en viss insamlingsperioden i en timme. Den här mätaren gäller bara i Standard-nivån, där du kan öppna ytterligare anslutningar (tidigare anslutningar var begränsade till 100 per kö/ämne/prenumeration) mot en nominell anslutningsspecifika avgift.

Den **Standard** nivån introducerar graderad priserna för åtgärder som utförs med köer och ämnen/prenumerationer, vilket resulterar i volymbaserad rabatter på upp till 80% på högsta användning nivåer. Det finns också en Standard-nivån basavgift på $10 per månad, där du kan utföra upp till 12,5 miljoner åtgärder per månad utan extra kostnad.

Den **Premium** nivån ger resursisolering på processor-och minne så att varje kunds arbetsbelastning körs i isolering. Den här resursbehållaren kallas för en *meddelandefunktionsenhet*. Varje Premium-namnområde allokeras minst en meddelandefunktionsenhet. Du kan köpa 1, 2 eller 4 meddelandefunktionsenheter för varje Service Bus Premium-namnområde. En enda arbetsbelastning eller enhet kan spänna över flera meddelandefunktionsenheter, och antalet meddelandefunktionsenheter kan ändras när du vill, även om faktureringen är per 24 timmar eller daglig taxa. Resultatet är förutsägbara och repeterbara prestanda för Service Bus-lösningen. Prestanda är inte bara mer förutsägbara och tillgängliga, utan de är snabbare också.

> [!NOTE]
> Ämnen och prenumerationer är bara tillgängliga i Standard- eller Premium-prisnivåerna; Basic-nivån stöder endast köer.

Nivån Standard-basavgiften debiteras bara en gång per månad per Azure-prenumeration. Det innebär att du kan skapa så många ytterligare Standard-namnområde som du vill under samma Azure-prenumeration, utan att fakturera extra Grundavgifter när du har skapat en enda Standard-nivån Service Bus-namnområde.

Den [priser för Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) tabell sammanfattas de funktionella skillnaderna mellan nivåerna Standard och Premium.

## <a name="messaging-operations"></a>Meddelandeåtgärder

Köer och ämnen/prenumerationer debiteras per ”åtgärd”, inte per meddelande. En åtgärd som refererar till ett API-anrop som görs mot en tjänstslutpunkt för köer eller ämnen/prenumerationer. Det innefattar åtgärder för hantering, skicka/ta emot och sessionstillstånd.

| Åtgärdstyp | Beskrivning |
| --- | --- |
| Hantering |Skapa, läsa, uppdatera, ta bort (CRUD) mot köer eller ämnen/prenumerationer. |
| Meddelandetjänster |Skicka och ta emot meddelanden med köer eller ämnen/prenumerationer. |
| Sessionstillstånd |Hämta eller ange sessionstillstånd i en kö eller ämne/prenumeration. |

Kostnadsinformation finns i priserna som listas på den [priser för Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) sidan.

## <a name="brokered-connections"></a>Brokered Connections

*Asynkrona anslutningar* tillgodose användningsmönster som omfattar ett stort antal ”beständigt ansluten” avsändare/mottagare mot köer, ämnen eller prenumerationer. Beständigt anslutna avsändare/mottagare är de som ansluter via AMQP- eller HTTP med en noll får timeout (till exempel HTTP longpolling). HTTP-sändare och mottagare med en tidsgräns för omedelbar genererar inte asynkrona anslutningar.

Kvoter för anslutningen och andra tjänstebegränsningar finns i den [Service Bus-kvoter](service-bus-quotas.md) artikeln. Mer information om asynkrona anslutningar finns i den [vanliga frågor och svar](#faq) senare i den här artikeln.

Standard-nivån tar bort gränsen per namnområde asynkron anslutning och räknar sammanställd asynkron anslutning användning i Azure-prenumeration. Mer information finns i den [asynkrona anslutningar](https://azure.microsoft.com/pricing/details/service-bus/) tabell.

> [!NOTE]
> 1 000 brokered connections ingår i Standard messaging-nivån (genom basavgiften) och kan delas mellan alla köer, ämnen och prenumerationer inom samma Azure-prenumeration.
>
>

<br />

> [!NOTE]
> Fakturering baseras på det högsta antalet samtidiga anslutningar och beräknas procentuellt per timme baserat på 744 timmar per månad.
>
>

### <a name="premium-tier"></a>Premiumnivå

Brokered Connections debiteras inte på Premium-nivån.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="what-are-brokered-connections-and-how-do-i-get-charged-for-them"></a>Vad är asynkrona anslutningar och hur debiteras jag för dem?

En Brokered Connection definieras som något av följande:

1. En AMQP-anslutning från en klient till en Service Bus-kö eller ämne/prenumeration.
2. Ett HTTP-anrop för att ta emot ett meddelande från ett ämne eller en kö i Service Bus med ett mottagningstidsgränsvärde som är större än noll.

Service Bus-avgifter för det högsta antalet samtidiga brokered connections som överstiger antalet som ingår (1 000 i nivån Standard). Det högsta antalet mäts per timme. Detta beräknas proportionellt genom att dividera med 744 timmar per månad och summera över faktureringsperioden. Det antal som ingår (1 000 Brokered Connections per månad) räknas av mot summan av högsta antal per timme i slutet av faktureringsperioden.

Exempel:

1. Var och en av 10 000 enheter ansluts via en enkel AMQP-anslutning och tar emot kommandon från ett Service Bus-ämne. Enheterna skickar telemetrihändelser till ett Händelsenav. Om alla enheter är anslutna i 12 timmar varje dag, det gäller följande anslutningsavgifter (utöver andra Service Bus avsnittet-avgifter): 10 000 anslutningar * 12 timmar * 31 dagar / 744 = 5 000 brokered connections. Efter månadskvoten på 1 000 brokered connections skulle du debiteras för 4 000 brokered connections enligt taxan $0.03 per brokered connection, sammanlagt $120.
2. 10 000 enheter tar emot meddelanden från en Service Bus-kö via HTTP, med en tidsgräns som inte är noll. Om alla enheter är anslutna i 12 timmar varje dag, så ser du följande anslutningsavgifter (utöver andra Service Bus-avgifter): 10 000 HTTP tar emot anslutningar * 12 timmar per dag * 31 dagar / 744 timmar = 5 000 brokered connections.

### <a name="do-brokered-connection-charges-apply-to-queues-and-topicssubscriptions"></a>Gäller Brokered Connection-avgifter för köer och ämnen/prenumerationer?

Ja. Du debiteras inga anslutningsavgifter för att skicka händelser via HTTP, oavsett antalet sändande system eller enheter. Ta emot händelser via HTTP med en tidsgräns som är större än noll, vilket ibland kallas ”longpolling”, genererar brokered connection-avgifter. AMQP-anslutningar genererar Brokered Connection-avgifter oavsett om anslutningarna används till att skicka eller ta emot. De första 1 000 brokered connections i Standard-namnområde i en Azure-prenumeration ingår utan extra kostnad (utöver basavgiften). Eftersom dessa kvoter räcker för många scenarier för tjänst-till-tjänst-meddelanden, blir brokered connection-avgifter endast vanligen relevanta om du planerar att använda AMQP- eller HTTP-longpolling med ett stort antal klienter. till exempel för att få effektivare händelseströmning eller aktivera tvåvägskommunikation med många enheter eller instanser av programmet.

## <a name="next-steps"></a>Nästa steg

* Mer information om priser för Service Bus, finns det [Service Bus sidan med priser](https://azure.microsoft.com/pricing/details/service-bus/).
* Se den [Service Bus vanliga frågor och svar](service-bus-faq.md#pricing) för några vanliga frågor och svar om Service bus priser och fakturering.

[Azure portal]: https://portal.azure.com
