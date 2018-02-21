---
title: ta med fil
description: ta med fil
services: service-bus-messaging
author: sethmanheim
ms.service: service-bus-messaging
ms.topic: include
ms.date: 02/12/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 0e89371720dd8bce1009ede922b2b5cd86a61363
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2018
---
I följande tabell visas information för kvot som är specifika för Service Bus-meddelanden. Mer information om priser och andra kvoter för Service Bus finns i [priser för Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) Översikt.

| Kvoten namn | Omfång | Typ | Beteende när överskridits | Värde |
| --- | --- | --- | --- | --- |
| Maximalt antal grundläggande / standard namnområden per Azure-prenumeration |Namnrymd |Statisk |Efterföljande begäranden om ytterligare grundläggande / standard namnområden avvisas av portalen. |100|
| Maximalt antal premium-namnområden per Azure-prenumeration |Namnrymd |Statisk |Efterföljande begäranden om ytterligare premium-namnområden avvisas av portalen. |10 |
| Kön/avsnittet storlek |Entitet |Definierats vid skapande av kö/ämnet. |Inkommande meddelanden avvisas och ett undantag tas emot av den anropande koden. |1, 2, 3, 4 och 5 GB.<br /><br />Om [partitionering](../articles/service-bus-messaging/service-bus-partitioning.md) är aktiverad, högsta kö/avsnittet storleken är 80 GB. |
| Antalet samtidiga anslutningar på ett namnområde |Namnrymd |Statisk |Efterföljande begäranden om ytterligare anslutningar avvisas och ett undantag tas emot av den anropande koden. REST-åtgärder räknas inte mot samtidiga TCP-anslutningar. |NetMessaging: 1 000<br /><br />AMQP: 5 000 |
| Antal samtidiga får begäranden på en entitet i kö-avsnittet-prenumeration |Entitet |Statisk |Efterföljande ta emot förfrågningar avvisas och ett undantag tas emot av den anropande koden. Den här kvoten gäller för det kombinerade antalet parallella få åtgärder för alla prenumerationer på ett ämne. |5,000 |
| Antal avsnitt/köer per namnområde för tjänsten |System-wide |Statisk |Efterföljande förfrågningar för att skapa en ny artikel eller en kö på namnområde för tjänsten nekas. Därför, om konfigurerad via den [Azure-portalen][Azure portal], genereras ett felmeddelande. Om den anropas från ett hanteringsgränssnitt emot ett undantag av den anropande koden. |10 000<br /><br />Det totala antalet avsnitt plus köer i ett namnområde för tjänsten måste vara mindre än eller lika med 10 000.<br/>Detta gäller inte för Premium som alla entiteter partitioneras. |
| Antal partitionerade avsnitt/köer per namnområde för tjänsten |System-wide |Statisk |Efterföljande förfrågningar för att skapa en ny partitionerade artikel eller en kö på namnområde för tjänsten nekas. Därför, om konfigurerad via den [Azure-portalen][Azure portal], genereras ett felmeddelande. Om den anropas från management API, en **QuotaExceededException** undantag tas emot av den anropande koden. |Nivåerna Basic och Standard - 100<br />[Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md) -1 000 (per meddelandefunktionsenhet)<br/><br />Varje partitionerade kö eller ett ämne räknar mot kvoten för 10 000 enheter per namnområde. |
| Maximal storlek för alla meddelanden enhet-sökvägar: kö eller ett ämne |Entitet |Statisk |- |260 tecken |
| Maximal storlek för alla meddelanden entitetsnamnet: namnområdet, prenumeration eller prenumeration regel |Entitet |Statisk |- |50 tecken |
| Meddelandelagringsstorlek för en entitet i kö-avsnittet-prenumeration |System-wide |Statisk |Inkommande meddelanden som överskrider dessa kvoter avvisas och ett undantag tas emot av den anropande koden. |Maximal meddelandestorlek: 256KB ([standardnivån](../articles/service-bus-messaging/service-bus-premium-messaging.md)) / 1MB ([premiumnivån](../articles/service-bus-messaging/service-bus-premium-messaging.md)). <br /><br />**Obs** på grund av system som den här gränsen är vanligtvis något mindre.<br /><br />Maximal huvudstorlek: 64KB<br /><br />Maximalt antal egenskaper för sidhuvud i egenskapsuppsättningen: **byte/int. MaxValue**<br /><br />Maximal storlek för egenskapen i egenskapsuppsättningen: ingen explicit gräns. Begränsas av högsta huvudstorlek. |
| Meddelandestorlek egenskap för en entitet i kö-avsnittet-prenumeration |System-wide |Statisk |En **SerializationException** undantag genereras. |Maximal egenskapen meddelandestorlek för varje egenskap är 32 kB. Totala storleken för alla egenskaper får inte överskrida 64 kB. Detta gäller för hela rubriken för den [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), som har både användaregenskaper samt Systemegenskaper (exempelvis [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [etikett](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label), [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid)och så vidare). |
| Antalet prenumerationer per avsnittet |System-wide |Statisk |Efterföljande förfrågningar för att skapa ytterligare prenumerationer för avsnittet avvisas. Därför visas ett felmeddelande om konfigureras via portalen Om den anropas från management-API: et emot ett undantag av den anropande koden. |2,000 |
| Antal SQL-filter per avsnittet |System-wide |Statisk |Efterföljande förfrågningar för att skapa ytterligare filter på avsnittet avvisas och ett undantag tas emot av den anropande koden. |2,000 |
| Antal korrelation filter per avsnittet |System-wide |Statisk |Efterföljande förfrågningar för att skapa ytterligare filter på avsnittet avvisas och ett undantag tas emot av den anropande koden. |100,000 |
| Storleken på SQL-filter /-åtgärder |System-wide |Statisk |Efterföljande förfrågningar för att skapa ytterligare filter avvisas och ett undantag tas emot av den anropande koden. |Maximal längd på villkoret Filtersträngen: 1024 (1K).<br /><br />Maximal längd på regeln åtgärd sträng: 1024 (1K).<br /><br />Maximalt antal uttryck per Regelåtgärd: 32. |
| Antal [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) regler per namnområdet, kö eller ett ämne |Entiteten namnområde |Statisk |Efterföljande begäranden om skapande av ytterligare regler avvisas och ett undantag tas emot av den anropande koden. |Maximalt antal regler: 12. <br /><br /> Regler som har konfigurerats på ett Service Bus-namnområde som gäller för alla köer och ämnen i detta namnområde. |
| Antal meddelanden per transaktion | System-wide | Statisk | Ytterligare inkommande meddelanden avvisas och inte ett undantag om ”kan skicka fler än 100 meddelanden i en enda transaktion” tas emot av den anropande koden. | 100 <br /><br /> För både **Send()** och **SendAsync()** åtgärder. |

[Azure portal]: https://portal.azure.com