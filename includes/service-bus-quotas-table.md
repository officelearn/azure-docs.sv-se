---
title: ta med fil
description: ta med fil
services: service-bus-messaging
author: sethmanheim
ms.service: service-bus-messaging
ms.topic: include
ms.date: 06/04/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 0ff1e31e52c7db5c41f92cb9e4cb1a17f28dea6f
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38756189"
---
I följande tabell visas kvotinformation som är specifika för Service Bus-meddelanden. Information om priser och andra kvoter för Service Bus finns i den [priser för Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) Översikt.

| Kvotnamn | Omfång | Anteckningar | Värde |
| --- | --- | --- | --- | --- |
| Maximalt antal basic / standard namnområden per Azure-prenumeration |Namnrymd |Efterföljande begäranden om ytterligare namnområden på basic / standard avvisas av portalen. |100|
| Maximalt antal premium-namnområden per Azure-prenumeration |Namnrymd |Efterföljande begäranden om ytterligare premium-namnområden avvisas av portalen. |10 |
| Storlek på kö/ämne |Entitet |Definierade när den har skapats för queue/topic. <br/><br/> Efterföljande inkommande meddelanden avvisas och ett undantag tas emot av den anropande koden. |1, 2, 3, 4 eller 5 GB.<br /><br />I Premium-SKU, samt Standard med [partitionering](/azure/service-bus-messaging/service-bus-partitioning) aktiverat, den maximala kö/ämne är 80 GB. |
| Antalet samtidiga anslutningar för ett namnområde |Namnrymd |Efterföljande begäranden om ytterligare anslutningar avvisas och ett undantag tas emot av den anropande koden. REST-åtgärder räknas inte mot samtidiga TCP-anslutningar. |NetMessaging: 1 000<br /><br />AMQP: 5 000 |
| Antalet parallella ta emot förfrågningar för en entitet i kö/ämne/prenumeration |Entitet |Efterföljande ta emot begäranden avvisas och ett undantag tas emot av den anropande koden. Den här kvoten gäller för det kombinerade antalet parallella får åtgärder för alla prenumerationer på ett ämne. |5 000 |
| Antal ämnen/köer per namnområde |Namnrymd |Efterföljande förfrågningar för att skapa ett nytt ämne eller en kö i namnområdet avvisas. Resultatet blir att, om konfigurerad via den [Azure-portalen][Azure portal], genereras ett felmeddelande. Om den anropas från ett hanteringsgränssnitt emot ett undantag av den anropande koden. |10 000<br /><br />Det totala antalet ämnen plus köer i ett namnområde måste vara mindre än eller lika med 10 000. |
| Antal [partitionerad ämnen/köer](/azure/service-bus-messaging/service-bus-partitioning) per namnområde |Namnrymd |Efterföljande förfrågningar för att skapa en ny partitionerad ämne eller en kö i namnområdet avvisas. Resultatet blir att, om konfigurerad via den [Azure-portalen][Azure portal], genereras ett felmeddelande. Om den anropas från hanterings-API, en **QuotaExceededException** undantag tas emot av den anropande koden. |Nivåerna Basic och Standard - 100<br/><br/>Partitionerade enheter stöds inte i den [Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md) nivå.<br/><br />Varje partitionerad kö eller ämne räknas mot kvoten på 10 000 enheter per namnområde. |
| Maximal storlek för alla meddelanden entitetssökväg: kö eller ett ämne |Entitet |- |260 tecken |
| Maximal storlek på alla meddelanden entitetsnamn: namnrymd, prenumeration eller prenumeration regel |Entitet |- |50 tecken |
| Maximal storlek på ett meddelande [sessions-ID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) | Entitet |- | 128 |
| Storlek för en entitet i kö/ämne/prenumeration |Entitet |Inkommande meddelanden som överskrider kvoterna avvisas och ett undantag tas emot av den anropande koden. |Maximal storlek: 256 KB ([standardnivån](../articles/service-bus-messaging/service-bus-premium-messaging.md)) / 1 MB ([premiumnivån](../articles/service-bus-messaging/service-bus-premium-messaging.md)). <br /><br />På grund av system som är mindre än värdena i den här gränsen.<br /><br />Maximal huvudstorlek: 64 KB<br /><br />Maximalt antal egenskaper för sidhuvud i egenskapsuppsättningen: **byte/int. MaxValue**<br /><br />Maximal storlek för egenskap i egenskapsuppsättningen: ingen explicit gräns. Begränsas av högsta huvudstorlek. |
| Egenskapsstorlek för en entitet i kö/ämne/prenumeration |Entitet |En **SerializationException** undantag genereras. |Maximal egenskapsstorlek för varje egenskap är 32 K. sammanlagda storleken för alla egenskaper inte får överskrida 64 K. Den här begränsningen gäller för hela rubriken för den [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), som har både användaregenskaper samt Systemegenskaper (till exempel [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [etikett](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label), [ MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid)och så vidare). |
| Antalet prenumerationer per ämne |Entitet |Efterföljande förfrågningar för att skapa ytterligare prenumerationer för ämnet avvisas. Om konfigureras via portalen, därför kan visas ett felmeddelande. Om den anropas från API för hantering av tas ett undantag emot av den anropande koden. |2,000 |
| Antal SQL-filter per ämne |Entitet |Efterföljande förfrågningar för att skapa ytterligare filter på ämnet avvisas och ett undantag tas emot av den anropande koden. |2,000 |
| Antal korrelationsfilter per ämne |Entitet |Efterföljande förfrågningar för att skapa ytterligare filter på ämnet avvisas och ett undantag tas emot av den anropande koden. |100,000 |
| Storleken på SQL-filter/en åtgärd |Namnrymd |Efterföljande förfrågningar för att skapa ytterligare filter avvisas och ett undantag tas emot av den anropande koden. |Maximal längd på villkoret Filtersträngen: 1024 (1 K).<br /><br />Maximal längd på regeln åtgärd sträng: 1024 (1 K).<br /><br />Maximalt antal uttryck per Regelåtgärd: 32. |
| Antal [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) regler per namnområde, kö eller ett ämne |Entiteten, namnområde |Efterföljande begäranden om skapande av ytterligare regler avvisas och ett undantag tas emot av den anropande koden. |Högsta antalet regler: 12. <br /><br /> Regler som är konfigurerade på en Service Bus-namnrymd gäller för alla köer och ämnen i det namnområdet. |
| Antal meddelanden per transaktion | Transaktionen | Ytterligare inkommande meddelanden avvisas och ett undantag om ”det går inte att skicka fler än 100 meddelanden i en enda transaktion” tas emot av den anropande koden. | 100 <br /><br /> För både **Send()** och **SendAsync()** åtgärder. |

[Azure portal]: https://portal.azure.com