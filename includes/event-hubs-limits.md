---
title: ta med fil
description: ta med fil
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: a20481ea42a0772ab42322e912cfce67877734d6
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71830180"
---
Följande tabeller innehåller kvoter och begränsningar som är begränsade till [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Information om Event Hubs priser finns i [Event Hubs prissättning](https://azure.microsoft.com/pricing/details/event-hubs/).

Följande begränsningar är gemensamma för nivåerna Basic, standard och Dedicated. 

| Gräns | Omfång | Anteckningar | Value |
| --- | --- | --- | --- |
| Antal Event Hubs namn rymder per prenumeration |Subscription |- |100 |
| Antal Event Hub per namnrymd |Namnrymd |Efterföljande begär Anden om att skapa en ny händelsehubben avvisas. |10 |
| Antal partitioner per Event-hubb |Entitet |- |32 |
| Maximal storlek för ett Event Hub-namn |Entitet |- |50 tecken |
| Antal icke-epok mottagare per konsument grupp |Entitet |- |5 |
| Högsta antal Throughput Units |Namnrymd |Om gränsen överskrids kan data begränsas och ett undantag skapas för [servern](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Om du vill begära ett större antal data flödes enheter för en standard-nivå kan du skicka en [support förfrågan](/azure/azure-supportability/how-to-create-azure-support-request). [Ytterligare data flödes enheter](../articles/event-hubs/event-hubs-auto-inflate.md) är tillgängliga i block om 20 på ett dedikerat köp. |20 |
| Antal auktoriseringsregler per namnrymd |Namnrymd|Efterföljande begär Anden om att skapa auktoriseringsregler avvisas.|12 |
| Antal anrop till GetRuntimeInformation-metoden | Entitet | - | 50 per sekund | 
| Antal virtuella nätverk (VNet) och IP config-regler | Entitet | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Event Hubs Basic-och standard-kvoter och-gränser
| Gräns | Omfång | Anteckningar | Basic | Standard |
| --- | --- | --- | -- | --- |
| Maximal storlek för Event Hubs händelse|Entitet | &nbsp; | 256 kB | 1 MB |
| Antal konsument grupper per Event-hubb |Entitet | &nbsp; |1 |20 |
| Antal AMQP-anslutningar per namnrymd |Namnrymd |Efterföljande begär Anden om ytterligare anslutningar avvisas och ett undantag tas emot av den anropande koden. |100 |5,000|
| Högsta kvarhållningsperiod för händelse data |Entitet | &nbsp; |1 dag |1-7 dagar |
|Apache Kafka aktive rad namnrymd|Namnrymd |Event Hubs namespace strömmar program med Kafka-protokoll |Nej | Ja |
|Capture |Entitet | När aktive rad är mikrobatchar i samma ström |Nej |Ja |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Event Hubs Dedicated-kvoter och begränsningar
Event Hubs Dedicated erbjudandet debiteras till ett fast månads pris, med minst fyra timmars användning. Den dedikerade nivån innehåller alla funktioner i standard planen, men med kapacitet för företags skala och gränser för kunder med krävande arbets belastningar. 

| Funktion | Begränsningar |
| --- | ---|
| Bandbredd |  20 CUs |
| Namnområden | 50 per CU |
| Event Hubs |  1000 per namnrymd |
| Ingångshändelser | Ingår |
| Meddelandestorlek | 1 MB |
| Partitioner | 2000 per CU |
| Konsumentgrupper | Ingen gräns per CU, 1000 per Event-hubb |
| Brokered Connections | 100 K ingår |
| Meddelandelagring | Upp till 7 dagar, 10 TB inkluderat per CU |
| Capture | Ingår |
