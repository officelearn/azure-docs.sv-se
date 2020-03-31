---
title: ta med fil
description: ta med fil
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 12/13/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b19dc7a85fafa1a4d875c84db9bbefabb3cd5a7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651517"
---
I följande tabell visas kvotinformation som är specifik för Azure Service Bus-meddelanden. Information om priser och andra kvoter för Service Bus finns i [Service Bus-priser](https://azure.microsoft.com/pricing/details/service-bus/).

| Kvotnamn | Omfång | Anteckningar | Värde |
| --- | --- | --- | --- |
| Maximalt antal grundläggande eller standardnamnområden per Azure-prenumeration |Namnområde |Efterföljande begäranden om ytterligare grundläggande eller standardnamnområden avvisas av Azure-portalen. |100|
| Maximalt antal Premium-namnområden per Azure-prenumeration |Namnområde |Efterföljande begäranden om ytterligare Premium-namnområden avvisas av portalen. |100 |
| Kö- eller ämnesstorlek |Entitet |Definieras när kön eller ämnet skapas. <br/><br/> Efterföljande inkommande meddelanden avvisas och ett undantag tas emot av den anropande koden. |1, 2, 3, 4 GB eller 5 GB.<br /><br />I Premium SKU och Standard SKU med [partitionering](/azure/service-bus-messaging/service-bus-partitioning) aktiverat är den maximala kö- eller ämnesstorleken 80 GB. |
| Antal samtidiga anslutningar på ett namnområde |Namnområde |Efterföljande begäranden om ytterligare anslutningar avvisas och ett undantag tas emot av den anropande koden. REST-åtgärder räknas inte mot samtidiga TCP-anslutningar. |NetMessaging: 1000.<br /><br />AMQP: 5000. |
| Antal samtidiga mottagningsbegäranden i en kö-, ämnes- eller prenumerationsenhet |Entitet |Efterföljande mottagningsbegäranden avvisas och ett undantag tas emot av den anropande koden. Den här kvoten gäller det kombinerade antalet samtidiga mottagningsåtgärder för alla prenumerationer i ett ämne. |5 000 |
| Antal ämnen eller köer per namnområde |Namnområde |Efterföljande begäranden om att skapa ett nytt ämne eller en ny kö i namnområdet avvisas. Om ett felmeddelande konfigureras via [Azure-portalen][Azure portal]genereras därför ett felmeddelande. Om anropas från hanterings-API:et tas ett undantag emot av den anropande koden. |10 000 för basic- eller standardnivån. Det totala antalet ämnen och köer i ett namnområde måste vara mindre än eller lika med 10 000. <br/><br/>För Premium-nivån är 1 000 per meddelandeenhet (MU). Maxgränsen är 4 000. |
| Antal [partitionerade ämnen eller köer](/azure/service-bus-messaging/service-bus-partitioning) per namnområde |Namnområde |Efterföljande begäranden om att skapa ett nytt partitionerat ämne eller kö på namnområdet avvisas. Om ett felmeddelande konfigureras via [Azure-portalen][Azure portal]genereras därför ett felmeddelande. Om det anropas från hanterings-API:et tas undantaget **QuotaExceedException** emot av anropskoden. |Grund- och standardnivåer: 100.<br/><br/>Partitionerade entiteter stöds inte på [Premium-nivån.](../articles/service-bus-messaging/service-bus-premium-messaging.md)<br/><br />Varje partitionerad kö eller ämne räknas mot kvoten på 1 000 entiteter per namnområde. |
| Maximal storlek för entitetssökvägen för meddelanden: kö eller ämne |Entitet |- |260 tecken. |
| Maximal storlek för ett meddelandeentitetsnamn: namnområde, prenumeration eller prenumerationsregel |Entitet |- |50 tecken. |
| Maximal storlek på ett [meddelande-ID](/dotnet/api/microsoft.azure.servicebus.message.messageid) | Entitet |- | 128 |
| Maximal storlek på ett [meddelandesessions-ID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) | Entitet |- | 128 |
| Meddelandestorlek för en kö-, ämnes- eller prenumerationsenhet |Entitet |Inkommande meddelanden som överskrider dessa kvoter avvisas och ett undantag tas emot av den anropande koden. |Maximal meddelandestorlek: 256 KB för [standardnivå](../articles/service-bus-messaging/service-bus-premium-messaging.md), 1 MB för [Premium-nivå](../articles/service-bus-messaging/service-bus-premium-messaging.md). <br /><br />På grund av systemets omkostnader är den här gränsen mindre än dessa värden.<br /><br />Maximal huvudstorlek: 64 KB.<br /><br />Maximalt antal rubrikegenskaper i egenskapspåse: **byte/int. MaxValue**.<br /><br />Maximal storlek på egendom i egenskapsväska: Ingen uttrycklig gräns. Begränsad av maximal huvudstorlek. |
| Meddelandeegenskapsstorlek för en kö,ett ämne eller entitet för prenumeration |Entitet | Undantaget **SerializationException** genereras. |Maximal storlek på meddelandeegenskapen för varje egenskap är 32 000. Den sammanlagda storleken på alla egenskaper får inte överstiga 64 000. Den här gränsen gäller för hela huvudet i [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), som har både användaregenskaper och systemegenskaper, till exempel [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label)och [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid). |
| Antal prenumerationer per ämne |Entitet |Efterföljande begäranden om att skapa ytterligare prenumerationer för ämnet avvisas. Om ett felmeddelande konfigureras via portalen visas därför ett felmeddelande. Om anropas från hanterings-API:et tas ett undantag emot av den anropande koden. |2 000 per ämne för standardnivån. |
| Antal SQL-filter per ämne |Entitet |Efterföljande begäranden om att skapa ytterligare filter i ämnet avvisas och ett undantag tas emot av den anropande koden. |2 000 |
| Antal korrelationsfilter per ämne |Entitet |Efterföljande begäranden om att skapa ytterligare filter i ämnet avvisas och ett undantag tas emot av den anropande koden. |100 000 |
| Storlek på SQL-filter eller -åtgärder |Namnområde |Efterföljande begäranden om att skapa ytterligare filter avvisas och ett undantag tas emot av den anropande koden. |Maximal längd på filtervillkorssträngen: 1 024 (1 K).<br /><br />Maximal längd på regelåtgärdssträngen: 1 024 (1 K).<br /><br />Maximalt antal uttryck per regelåtgärd: 32. |
| Antal [SharedAccessAuthorizationRule-regler](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) per namnområde, kö eller ämne |Entitet, namnområde |Efterföljande begäranden om att skapa ytterligare regler avvisas och ett undantag tas emot av den anropande koden. |Maximalt antal regler per entitetstyp: 12. <br /><br /> Regler som är konfigurerade på ett servicebussnamnområde gäller för alla typer: köer, ämnen. |
| Antal meddelanden per transaktion | Transaktion | Ytterligare inkommande meddelanden avvisas och ett undantag med texten "Det går inte att skicka fler än 100 meddelanden i en enda transaktion" tas emot av den anropande koden. | 100 <br /><br /> För både **Send()** och **SendAsync()** åtgärder. |
| Antal regler för virtuellt nätverk och IP-filter | Namnområde | &nbsp; | 128 | 

[Azure portal]: https://portal.azure.com
