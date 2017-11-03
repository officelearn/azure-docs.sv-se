---
title: Service Bus priser och fakturering | Microsoft Docs
description: "Översikt över Service Bus priser struktur."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 7c45b112-e911-45ab-9203-a2e5abccd6e0
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/28/2017
ms.author: sethm
ms.openlocfilehash: 8f693bc51fc9635fae4376137e7e573bf74da7cb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="service-bus-pricing-and-billing"></a>Service Bus priser och fakturering
Service Bus erbjuds i Standard och [Premium](service-bus-premium-messaging.md) nivåer. Du kan välja en tjänstnivå för varje namnområde för Service Bus-tjänsten som du skapar och detta val av gäller över alla enheter som har skapats inom det här namnområdet.

> [!NOTE]
> Detaljerad information om aktuella Service Bus-priser finns i [Azure Service Bus sida med priser](https://azure.microsoft.com/pricing/details/service-bus/), och [Service Bus FAQ](service-bus-faq.md#pricing).
>
>

Service Bus använder följande 2 meter för köer och ämnen/prenumerationer:

1. **Messaging Operations**: definieras som API-anrop mot slutpunkter för kön eller ämne /-prenumeration. Den här mätaren ersätter meddelanden som skickas eller tas emot som den primära enheten av fakturerbar användning för köer och ämnen-prenumerationer.
2. **Asynkrona anslutningar**: definierade som det högsta antalet beständiga anslutningar öppnas mot köer, ämnen och prenumerationer under en viss insamlingsperioden en timme. Den här mätaren gäller endast på standardnivån, kan du öppna ytterligare anslutningar (tidigare anslutningar har begränsad till 100 per kö-avsnittet-prenumeration) avgift nominell per anslutning.

Den **Standard** nivå introducerar gradvisa priser för åtgärder som utförs med köer och ämnen i prenumerationer, vilket resulterar i volym-baserade rabatt på upp till 80% på den högsta nivån för användning. Det finns också en grundläggande kostnad standardnivån $ 10 per månad, där du kan utföra åtgärder för upp till 12,5 miljoner per månad utan extra kostnad.

Den **Premium** nivå ger resursisolering på processor- och minnesnivån så att varje kunds arbetsbelastning körs i isolering. Den här resursbehållaren kallas för en *meddelandefunktionsenhet*. Varje Premium-namnområde allokeras minst en meddelandefunktionsenhet. Du kan köpa 1, 2 eller 4 meddelandefunktionsenheter för varje Service Bus Premium-namnområde. En enda arbetsbelastning eller enhet kan spänna över flera meddelandefunktionsenheter, och antalet meddelandefunktionsenheter kan ändras när du vill, även om faktureringen är per 24 timmar eller daglig taxa. Resultatet är förutsägbara och repeterbara prestanda för Service Bus-lösningen. Prestanda är inte bara mer förutsägbara och tillgängliga, utan de är snabbare också.

Observera att standardnivån grundläggande kostnad debiteras en gång per månad per Azure-prenumeration. Det innebär att du kan skapa så många ytterligare Standard namnområden som du vill använda under den samma Azure-prenumerationen, utan att det medför ytterligare grundläggande avgifter när du har skapat en enda Standard nivå Service Bus-namnrymd.

Den [priser för Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) tabell sammanfattas funktionella skillnader mellan nivåerna Standard och Premium.

## <a name="messaging-operations"></a>Meddelandeåtgärder
Som en del av nya priserna ändrar fakturering för köer och ämnen-prenumerationer. Dessa enheter övergång från fakturering per meddelande till fakturering per åtgärd. ”Åtgärden” syftar på alla API-anrop som görs mot en tjänstslutpunkt kö eller ämne /-prenumeration. Detta omfattar åtgärder för hantering, skicka och ta emot och session tillstånd.

| Åtgärdstyp | Beskrivning |
| --- | --- |
| Hantering |Skapa, läsa, uppdatera, ta bort CRUD-mot köer och ämnen-prenumerationer. |
| Meddelandetjänster |Skicka och ta emot meddelanden med köer och ämnen-prenumerationer. |
| Sessionstillstånd |Hämtar eller anger sessionens tillstånd för en kö eller ett ämne /-prenumeration. |

Kostnadsinformation finns i de priser som visas på den [priser för Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) sidan.

## <a name="brokered-connections"></a>Brokered Connections
*Asynkrona anslutningar* hantera kunden användningsmönster som rör ett stort antal ”beständigt ansluten” avsändare/mottagare mot köer, ämnen och prenumerationer. Beständigt anslutna avsändare/mottagare är de som ansluter via AMQP eller HTTP med icke-noll får timeout (till exempel HTTP lång avsökning). HTTP-avsändare och mottagare med en omedelbar timeout genererar inte asynkrona anslutningar.

Anslutningen kvoter och andra tjänstbegränsningarna finns i [Service Bus-kvoter](service-bus-quotas.md) artikel. Mer information om asynkrona anslutningar finns i [vanliga frågor och svar](#faq) senare i den här artikeln.

Standardnivån om du tar bort den asynkrona anslutningsgränsen per namnområde och räknar sammanställd asynkrona anslutning användning över Azure-prenumerationen. Mer information finns i [asynkrona anslutningar](https://azure.microsoft.com/pricing/details/service-bus/) tabell.

> [!NOTE]
> 1 000 asynkrona anslutningar ingår i meddelanden standardnivån (via grundläggande tillägget) och kan delas mellan alla köer, ämnen och prenumerationer i tillhörande Azure-prenumeration.
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

### <a name="what-are-brokered-connections-and-how-do-i-get-charged-for-them"></a>Vad är asynkrona anslutningar och hur jag hämta debiteras för dem?
En Brokered Connection definieras som något av följande:

1. En AMQP anslutning från en klient till en Service Bus-kö eller ett ämne /-prenumeration.
2. Ett HTTP-anrop för att ta emot ett meddelande från ett ämne eller en kö i Service Bus med ett mottagningstidsgränsvärde som är större än noll.

Service Bus avgifter för högsta antal samtidiga asynkrona anslutningar som överskrider inkluderade (1 000 på standardnivån). Det högsta antalet mäts per timme. Detta beräknas proportionellt genom att dividera med 744 timmar per månad och summera över faktureringsperioden. Det antal som ingår (1 000 Brokered Connections per månad) räknas av mot summan av högsta antal per timme i slutet av faktureringsperioden.

Exempel:

1. Var och en av 10 000 enheter ansluter via en AMQP-anslutning och tar emot kommandon från en Service Bus-ämne. Enheterna skickar telemetriska händelser till en Händelsehubb. Om alla enheter ansluter 12 timmar varje dag, gäller följande anslutning avgifterna (utöver andra Service Bus avsnittet avgifter): 10 000 anslutningar * 12 timmar * 31 dagar / 744 = 5 000 asynkrona anslutningar. Efter månatlig ersättning av 1 000 asynkrona anslutningar, skulle du debiteras för 4 000 asynkrona anslutningar på frekvensen för $0.03 per asynkrona anslutning, för totalt $120.
2. 10 000 enheter ta emot meddelanden från en Service Bus-kö via HTTP, med en icke-noll-timeout. Om alla enheter ansluter 12 timmar varje dag, visas följande anslutning avgifterna (förutom eventuella övriga kostnader för Service Bus): 10 000 ta emot HTTP-anslutningar * 12 timmar per dag * 31 dagar / 744 timmar = 5 000 asynkrona anslutningar.

### <a name="do-brokered-connection-charges-apply-to-queues-and-topicssubscriptions"></a>Gäller Brokered Connection-avgifter för köer och ämnen/prenumerationer?
Ja. Det finns inga avgifter för anslutning för att skicka händelser med hjälp av HTTP, oavsett antalet skickar datorer eller enheter. Ta emot händelser med hjälp av en tidsgräns som är större än noll, kallas ibland ”länge avsökning”, HTTP genererar asynkrona anslutning avgifter. AMQP-anslutningar genererar Brokered Connection-avgifter oavsett om anslutningarna används till att skicka eller ta emot. 1 000 första asynkrona anslutningar över alla namnområden som Standard i en Azure-prenumeration ingår utan extra kostnad (utöver grundläggande tillägget). Eftersom dessa tillägg är tillräckligt för att täcka många meddelandehantering för tjänst-till-tjänst, blivit asynkrona anslutning avgifter vanligtvis bara relevant om du planerar att använda AMQP eller HTTP lång-avsökning med ett stort antal klienter. till exempel för att uppnå effektivare händelse strömning eller Aktivera dubbelriktad kommunikation med många enheter eller programinstanser.

## <a name="next-steps"></a>Nästa steg
* Mer information om Service Bus priser finns i [Service Bus sida med priser](https://azure.microsoft.com/pricing/details/service-bus/).
* Finns det [Service Bus FAQ](service-bus-faq.md#pricing) för några vanliga frågor och svar om Service bus priser och fakturering.

[Azure portal]: https://portal.azure.com
