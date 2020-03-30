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
ms.openlocfilehash: 2aca4f2c236112b80e9fc985cf80ccad6d82bde3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75901708"
---
Följande tabeller innehåller kvoter och begränsningar som är specifika för [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Information om priser för eventhubbar finns i [priser för eventhubbar](https://azure.microsoft.com/pricing/details/event-hubs/).

Följande gränser är vanliga på grundläggande nivåer, standard och dedikerade nivåer. 

| Gräns | Omfång | Anteckningar | Värde |
| --- | --- | --- | --- |
| Antal namnområden för eventhubbar per prenumeration |Prenumeration |- |100 |
| Antal händelsehubbar per namnområde |Namnområde |Efterföljande begäranden om att skapa en ny händelsehubb avvisas. |10 |
| Antal partitioner per händelsenav |Entitet |- |32 |
| Maximal storlek för ett händelsenavnamn |Entitet |- |50 tecken |
| Antal icke-epoker per konsumentgrupp |Entitet |- |5 |
| Maximala dataflödesenheter |Namnområde |Om du överskrider gränsen för dataflödesenhet begränsas data och ett [server busy-undantag](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)genereras . Om du vill begära ett större antal dataflödesenheter för en standardnivå lämnar du in en [supportbegäran](/azure/azure-portal/supportability/how-to-create-azure-support-request). [Ytterligare dataflödesenheter](../articles/event-hubs/event-hubs-auto-inflate.md) finns i block om 20 på grundval av genomförda inköp. |20 |
| Antal auktoriseringsregler per namnområde |Namnområde|Efterföljande begäranden om att skapa auktoriseringsregeln avvisas.|12 |
| Antal anrop till metoden GetRuntimeInformation | Entitet | - | 50 per sekund | 
| Antal virtuella nätverk (VNet) och IP Config-regler | Entitet | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Grundläggande och standard för eventhubbar – kvoter och begränsningar
| Gräns | Omfång | Anteckningar | Basic | Standard |
| --- | --- | --- | -- | --- |
| Maximal storlek för eventhubbar-händelse|Entitet | &nbsp; | 256 kB | 1 MB |
| Antal konsumentgrupper per händelsenav |Entitet | &nbsp; |1 |20 |
| Antal AMQP-anslutningar per namnområde |Namnområde |Efterföljande begäranden om ytterligare anslutningar avvisas och ett undantag tas emot av den anropande koden. |100 |5 000|
| Maximal lagringsperiod för händelsedata |Entitet | &nbsp; |1 dag |1-7 dagar |
|Apache Kafka aktiverat namnområde|Namnområde |Namespace-namnområdesströmmar av Event Hubs med Kafka-protokollet |Inga | Ja |
|Capture |Entitet | När det är aktiverat, mikro-batchar på samma ström |Inga |Ja |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Dedikerade eventhubbar – kvoter och begränsningar
Event Hubs Dedikerad erbjudande faktureras till ett fast månadspris, med minst 4 timmars användning. Den dedikerade nivån erbjuder alla funktioner i standardplanen, men med företagets skalningskapacitet och begränsningar för kunder med krävande arbetsbelastningar. 

| Funktion | Begränsningar |
| --- | ---|
| Bandbredd |  20 CUs |
| Namnområden | 50 per cu |
| Händelsehubbar |  1000 per namnområde |
| Ingående händelser | Ingår |
| Meddelandestorlek | 1 MB |
| Partitioner | 2000 per cu |
| Konsumentgrupper | Ingen gräns per CU, 1000 per händelsenav |
| Förmedlade anslutningar | 100 K ingår |
| Lagring av meddelanden | 90 dagar, 10 TB ingår per CU |
| Capture | Ingår |
