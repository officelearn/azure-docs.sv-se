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
ms.openlocfilehash: b105fb14608d53c5c2ef469ab44e211ccdf4d3c8
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812477"
---
I följande tabell visas kvoter och gränser specifika för [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Information om priser för Event Hubs finns i [priser för Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

| Gräns | Omfång | Anteckningar | Value |
| --- | --- | --- | --- |
| Antal Event Hubs-namnområden per prenumeration |Subscription |- |100 |
| Antal event-hubbar per namnområde |Namnrymd |Efterföljande förfrågningar för att skapa en ny händelsehubb avvisas. |10 |
| Antalet partitioner per händelsehubb |Entitet |- |32 |
| Antal konsumentgrupper per händelsehubb |Entitet |- |20 |
| Antal AMQP-anslutningar per namnområde |Namnrymd |Efterföljande begäranden om ytterligare anslutningar avvisas och ett undantag tas emot av den anropande koden. |5,000 |
| Maximal storlek för Event Hubs-händelse|Entitet |- |1 MB |
| Maximal storlek på en event hub-namn |Entitet |- |50 tecken |
| Antal icke-epoch mottagare per konsumentgrupp |Entitet |- |5 |
| Högsta bevarandeperioden av händelsedata |Entitet |- |1-7 dagar |
| Högsta antal Throughput Units |Namnrymd |Throughput unit överskrids gör att dina data till att begränsas och genererar en [upptagen server-undantag](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Om du vill begära ett större antal throughput units för en Standard-nivå, filen en [supportförfrågan](/azure/azure-supportability/how-to-create-azure-support-request). [Ytterligare throughput units](../articles/event-hubs/event-hubs-auto-inflate.md) är tillgängliga i block om 20 på åtagandeköpsbasis. |20 |
| Antalet regler per namnområde |Namnrymd|Efterföljande begäranden om skapande av auktorisering avvisas.|12 |
| Antal anrop till metoden GetRuntimeInformation | Entitet | - | 50 per sekund | 
| Antalet virtuella nätverk (VNet) och IP-Config-regler | Entitet | - | 128 | 

### <a name="event-hubs-dedicated---quotas-and-limits"></a>Event Hubs dedikerad - kvoter och begränsningar
Event Hubs Dedicated erbjudandet debiteras enligt ett fast pris per månad, med minst 4 timmars användning. Nivån Dedicated innehåller alla funktioner i standardprenumerationen, men med enterprise skala kapacitet och begränsningar för kunder med krävande arbetsbelastningar. 

| Funktion | Begränsningar |
| --- | ---|
| Bandbredd |  20 Cu: er |
| Namnområden | 50 per Kapacitetsenhet |
| Event Hubs |  1 000 per namnområde |
| Ingångshändelser | Ingår |
| Meddelandestorlek | 1 miljon byte |
| Partitioner | 2000 per Kapacitetsenhet |
| Konsumentgrupper | Ingen gräns per Kapacitetsenhet, 1 000 per händelsehubb |
| Brokered Connections | 100 K ingår |
| Meddelandelagring | Upp till 7 dagar (90-dagars kvarhållning kommer snart), ingår 10 TB per Kapacitetsenhet |
| Capture | Ingår |
