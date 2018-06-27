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
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34806408"
---
I följande tabell visas information för kvot som är specifika för Service Bus-meddelanden. Mer information om priser och andra kvoter för Service Bus finns i [priser för Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) Översikt.

| Kvoten namn | Omfång | Anteckningar | Värde |
| --- | --- | --- | --- | --- |
| Maximalt antal grundläggande / standard namnområden per Azure-prenumeration |Namnrymd |Efterföljande begäranden om ytterligare grundläggande / standard namnområden avvisas av portalen. |100|
| Maximalt antal premium-namnområden per Azure-prenumeration |Namnrymd |Efterföljande begäranden om ytterligare premium-namnområden avvisas av portalen. |10 |
| Kön/avsnittet storlek |Entitet |Definierats vid skapande av kö/ämnet. <br/><br/> Efterföljande inkommande meddelanden avvisas och ett undantag tas emot av den anropande koden. |1, 2, 3, 4 GB eller 5 GB.<br /><br />I Premium-SKU samt Standard med [partitionering](/azure/service-bus-messaging/service-bus-partitioning) aktiverad maximala kö/avsnittet storleken är 80 GB. |
| Antalet samtidiga anslutningar på ett namnområde |Namnrymd |Efterföljande begäranden om ytterligare anslutningar avvisas och ett undantag tas emot av den anropande koden. REST-åtgärder räknas inte mot samtidiga TCP-anslutningar. |NetMessaging: 1 000<br /><br />AMQP: 5 000 |
| Antal samtidiga får begäranden på en entitet i kö-avsnittet-prenumeration |Entitet |Efterföljande ta emot förfrågningar avvisas och ett undantag tas emot av den anropande koden. Den här kvoten gäller för det kombinerade antalet parallella få åtgärder för alla prenumerationer på ett ämne. |5 000 |
| Antal avsnitt/köer per namnområde |Namnrymd |Efterföljande förfrågningar för att skapa en ny artikel eller en kö på namnområdet avvisas. Därför, om konfigurerad via den [Azure-portalen][Azure portal], genereras ett felmeddelande. Om den anropas från ett hanteringsgränssnitt emot ett undantag av den anropande koden. |10 000<br /><br />Det totala antalet avsnitt plus köer i ett namnområde måste vara mindre än eller lika med 10 000. |
| Antal [partitionerad avsnitt/köer](/azure/service-bus-messaging/service-bus-partitioning) per namnområde |Namnrymd |Efterföljande förfrågningar för att skapa ett nytt partitionerade avsnitt eller en kö på namnområdet avvisas. Därför, om konfigurerad via den [Azure-portalen][Azure portal], genereras ett felmeddelande. Om den anropas från management API, en **QuotaExceededException** undantag tas emot av den anropande koden. |Nivåerna Basic och Standard - 100<br/><br/>Partitionerade enheter stöds inte i den [Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md) nivå.<br/><br />Varje partitionerade kö eller ett ämne räknar mot kvoten för 10 000 enheter per namnområde. |
| Maximal storlek för alla meddelanden enhet-sökvägar: kö eller ett ämne |Entitet |- |260 tecken |
| Maximal storlek för alla meddelanden entitetsnamnet: namnområdet, prenumeration eller prenumeration regel |Entitet |- |50 tecken |
| Maximal storlek för ett meddelande [sessions-ID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) | Entitet |- | 128 |
| Meddelandelagringsstorlek för en entitet i kö-avsnittet-prenumeration |Entitet |Inkommande meddelanden som överskrider dessa kvoter avvisas och ett undantag tas emot av den anropande koden. |Maximal meddelandestorlek: 256 KB ([standardnivån](../articles/service-bus-messaging/service-bus-premium-messaging.md)) / 1 MB ([premiumnivån](../articles/service-bus-messaging/service-bus-premium-messaging.md)). <br /><br />På grund av system som är mindre än värdena i den här gränsen.<br /><br />Maximal huvudstorlek: 64 KB<br /><br />Maximalt antal egenskaper för sidhuvud i egenskapsuppsättningen: **byte/int. MaxValue**<br /><br />Maximal storlek för egenskapen i egenskapsuppsättningen: ingen explicit gräns. Begränsas av högsta huvudstorlek. |
| Meddelandestorlek egenskap för en entitet i kö-avsnittet-prenumeration |Entitet |En **SerializationException** undantag genereras. |Maximal egenskapen meddelandestorlek för varje egenskap är 32 K. sammanlagda storleken för alla egenskaper inte får överskrida 64 k Den här begränsningen gäller för hela rubriken för den [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), som har både användaregenskaper samt Systemegenskaper (exempelvis [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [etikett](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label), [ MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid)och så vidare). |
| Antalet prenumerationer per avsnittet |Entitet |Efterföljande förfrågningar för att skapa ytterligare prenumerationer för avsnittet avvisas. Därför visas ett felmeddelande om konfigureras via portalen Om den anropas från management-API: et emot ett undantag av den anropande koden. |2,000 |
| Antal SQL-filter per avsnittet |Entitet |Efterföljande förfrågningar för att skapa ytterligare filter på avsnittet avvisas och ett undantag tas emot av den anropande koden. |2,000 |
| Antal korrelation filter per avsnittet |Entitet |Efterföljande förfrågningar för att skapa ytterligare filter på avsnittet avvisas och ett undantag tas emot av den anropande koden. |100,000 |
| Storleken på SQL-filter /-åtgärder |Namnrymd |Efterföljande förfrågningar för att skapa ytterligare filter avvisas och ett undantag tas emot av den anropande koden. |Maximal längd på villkoret Filtersträngen: 1024 (1 K).<br /><br />Maximal längd på regeln åtgärd sträng: 1024 (1 K).<br /><br />Maximalt antal uttryck per Regelåtgärd: 32. |
| Antal [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) regler per namnområdet, kö eller ett ämne |Entiteten namnområde |Efterföljande begäranden om skapande av ytterligare regler avvisas och ett undantag tas emot av den anropande koden. |Maximalt antal regler: 12. <br /><br /> Regler som har konfigurerats på ett Service Bus-namnområde som gäller för alla köer och ämnen i detta namnområde. |
| Antal meddelanden per transaktion | Transaktionen | Ytterligare inkommande meddelanden avvisas och inte ett undantag om ”kan skicka fler än 100 meddelanden i en enda transaktion” tas emot av den anropande koden. | 100 <br /><br /> För både **Send()** och **SendAsync()** åtgärder. |

[Azure portal]: https://portal.azure.com