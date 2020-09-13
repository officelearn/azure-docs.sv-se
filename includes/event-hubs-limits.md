---
title: inkludera fil
description: inkludera fil
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 09/10/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 8700bbfe697a6b5fb81380831950d704fcb1f5ff
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2020
ms.locfileid: "90012900"
---
Följande tabeller innehåller kvoter och begränsningar som är begränsade till [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Information om Event Hubs priser finns i [Event Hubs prissättning](https://azure.microsoft.com/pricing/details/event-hubs/).

Följande begränsningar är gemensamma för nivåerna Basic och standard. 

| Gräns | Omfång | Kommentarer | Värde |
| --- | --- | --- | --- |
| Antal Event Hubs namn rymder per prenumeration |Prenumeration |- |100 |
| Antal Event Hub per namnrymd |Namnområde |Efterföljande begär Anden om att skapa en ny händelsehubben avvisas. |10 |
| Antal partitioner per Event-hubb |Entitet |- |32 |
| Maximal storlek för ett Event Hub-namn |Entitet |- | 256 tecken |
| Maximal storlek för ett konsument grupp namn |Entitet |- | 256 tecken |
| Antal icke-epok mottagare per konsument grupp |Entitet |- |5 |
| Maximalt antal data flödes enheter |Namnområde |Om gränsen överskrids kan data begränsas och ett undantag skapas för [servern](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Om du vill begära ett större antal data flödes enheter för en standard-nivå kan du skicka en [support förfrågan](/azure/azure-portal/supportability/how-to-create-azure-support-request). [Ytterligare data flödes enheter](../articles/event-hubs/event-hubs-auto-inflate.md) är tillgängliga i block om 20 på ett dedikerat köp. |20 |
| Antal auktoriseringsregler per namnrymd |Namnområde|Efterföljande begär Anden om att skapa auktoriseringsregler avvisas.|12 |
| Antal anrop till GetRuntimeInformation-metoden | Entitet | - | 50 per sekund | 
| Antal virtuella nätverk (VNet) och IP config-regler | Entitet | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Event Hubs Basic-och standard-kvoter och-gränser
| Gräns | Omfång | Kommentarer | Basic | Standard |
| --- | --- | --- | -- | --- |
| Maximal storlek för Event Hubs händelse|Entitet | &nbsp; | 256 kB | 1 MB |
| Antal konsument grupper per Event-hubb |Entitet | &nbsp; |1 |20 |
| Antal AMQP-anslutningar per namnrymd |Namnområde |Efterföljande begär Anden om ytterligare anslutningar avvisas och ett undantag tas emot av den anropande koden. |100 |5 000|
| Högsta kvarhållningsperiod för händelse data |Entitet | &nbsp; |1 dag |1-7 dagar |
|Apache Kafka aktive rad namnrymd|Namnområde |Event Hubs namespace strömmar program med Kafka-protokollet. Mer information finns i [använda Azure Event Hubs från Apache Kafka program](../articles/event-hubs/event-hubs-for-kafka-ecosystem-overview.md). |Inga | Ja |
|Capture |Entitet | När det är aktiverat är mikrobatchar i samma ström. Mer information finns i [avbilda händelser via azure Event Hubs i azure Blob Storage eller Azure Data Lake Storage](../articles/event-hubs/event-hubs-capture-overview.md). |Inga |Ja |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Event Hubs Dedicated-kvoter och begränsningar
Event Hubs Dedicated erbjudandet debiteras till ett fast månads pris, med minst fyra timmars användning. Den dedikerade nivån innehåller alla funktioner i standard planen, men med kapacitet för företags skala och gränser för kunder med krävande arbets belastningar. 

| Funktion | Begränsningar |
| --- | ---|
| Bandbredd |  20 CUs |
| Namnrymder | 50 per CU |
| Event Hubs |  1000 per namnrymd |
| Ingress-händelser | Ingår |
| Meddelande storlek | 1 MB |
| Partitioner | 2000 per CU |
| Konsumentgrupper | Ingen gräns per CU, 1000 per Event-hubb |
| Brokered Connections | 100 KB ingår |
| Kvarhållning av meddelanden | 90 dagar, 10 TB inkluderat per CU |
| Capture | Ingår |
