---
title: Översikt över dedikerade event hubs – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller en översikt över dedikerade Azure Event Hubs, som erbjuder en enda klient distributioner av händelsehubbar.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 52d46009464c7417d5b525154fdac09c030aabe7
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64708009"
---
# <a name="overview-of-event-hubs-dedicated"></a>Översikt över Event Hubs Dedicated

*Event Hubs-kluster* erbjuder en enda klient distributioner för kunder med strömmande behoven. Den här enda klient erbjudande har en garanterad serviceavtal på 99,99% och är endast tillgänglig på vår dedikerade prisnivå. Ett Event Hubs-kluster kan ingående miljontals händelser per sekund med garanterad kapacitet och subsekundära svarstider. Namnområden och event hubs som skapats i det dedikerade klustret omfattar alla funktioner på Standard-erbjudandet och mycket mer, men utan några ingående begränsningar. Den innehåller också populära [Event Hubs Capture](event-hubs-capture-overview.md) funktionen utan extra kostnad, så att du kan automatiskt batch- och log-dataströmmar till Azure Storage eller Azure Data Lake. 

Dedikerade kluster etablerats och faktureras per **kapacitetsenheter (Cu: er)**, en förallokerad mängden Processortid och minnesresurser. Du kan köpa 1, 2, 4, 8, 12, 16 eller 20 Cu: er för varje kluster. Hur mycket du kan mata in och strömma per Kapacitetsenhet beror på en mängd olika faktorer, till exempel hur många producenter och konsumenter, nyttolast form utgående Betygsätt (se resultaten nedan för mer information). 

## <a name="why-dedicated"></a>Varför dedikerade?

Dedikerade Event Hubs erbjuder tre övertygande fördelar för kunder som behöver företagsnivå kapacitet:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Single-innehavare garanterar kapacitet för bättre prestanda

Ett dedikerat kluster garanterar kapacitet i full skala och kan skicka upp till strömmande data med fullständigt beständig lagring och subsekundära svarstid för någon gigabyte burst-trafiken. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Inklusiva och exklusivt tillgång till funktioner 
Dedikerad erbjudandet omfattar funktioner som att samla in vid utan extra kostnad, samt exklusiv åtkomst till kommande funktioner som BYOK. Tjänsten hanterar också Utjämning av nätverksbelastning, OS-uppdateringar, säkerhetsuppdateringar och partitionering för kunden, så att du kan ägna mindre tid åt infrastrukturunderhåll och mer tid på att skapa funktioner för klientsidan.  

#### <a name="cost-savings"></a>Kostnadsbesparingar
Vid hög inkommande volymer (> 100 Dataflödesenheter), ett kluster kostnader betydligt mindre per timme än köpa ett jämförbara antal dataflödesenheter i Standard-erbjudandet.


## <a name="event-hubs-standard-vs-dedicated"></a>Event Hubs Standard vs. Dedikerad

I följande tabell jämförs tillgängliga tjänstnivåerna för Event Hubs. Event Hubs Dedicated erbjudandet debiteras utifrån ett fast månadspris jämfört med användningen priserna för de flesta funktioner i Standard. Nivån Dedicated innehåller alla funktioner i standardprenumerationen, men med enterprise skala kapacitet för kunder med krävande arbetsbelastningar. 

| Funktion | Standard | Dedikerad |
| --- |:---:|:---:|
| Ingångshändelser | Betala per miljon händelser | Ingår |
| Throughput unit (1 MB/s ingångshändelser, 2 MB/s utgångshändelser) | Betala per timme | Ingår |
| Meddelandestorlek | 1 MB | 1 MB |
| Partitioner | 40 per namnområde | 2000 per Kapacitetsenhet |
| Konsumentgrupper | 20 per Händelsehubb | 1 000 per Händelsehubb |
| Max. Bandbredd | 20 Dataflödesenheter (upp till 40 Dataflödesenheter)    | 20 Cu: er |
| Brokered Connections | 1 000 ingår | 100 K ingår |
| Meddelandelagring | 1 dag ingår | Upp till sju dagar ingår |
| Capture | Betala per timme | Ingår |

## <a name="what-can-i-achieve-with-a-cluster"></a>Vad kan jag få med ett kluster?

För ett Event Hubs-kluster beror hur mycket du kan mata in och strömma på olika faktorer, till exempel din producenter, konsumenter, den hastighet med vilken du mata in och bearbetning av och mycket mer. 

Följande tabell visas resultaten att vi har uppnått under våra tester:

| Nyttolasten form | Mottagare | Ingående bandbredd| Inkommande meddelanden | Utgående bandbredd | Utgående meddelanden | Totalt antal Dataflödesenheter | Dataflödesenheter per Kapacitetsenhet |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Batchar med 100x1KB | 2 | 400 MB/sek | 400 k meddelanden/sek | 800 MB/sek | 800 k meddelanden/sek | 400 Dataflödesenheter | 100 Dataflödesenheter | 
| Batchar med 10x10KB | 2 | 666 MB/sek | 66.6 k meddelanden/sek | 1,33 GB/sek | 133 k meddelanden/sek | 666 Dataflödesenheter | 166 Dataflödesenheter |
| Batchar med 6x32KB | 1 | 1,05 GB/sek | 34 k meddelanden / sek | 1,05 GB/sek | 34 k meddelanden/sek | 1000 Dataflödesenheter | 250 Dataflödesenheter |

I testet användes följande kriterier:

- En dedikerad nivån Event Hubs-kluster med fyra kapacitetsenheter (Cu: er) har använts. 
- Den händelsehubb som används för datainmatning hade 200 partitioner. 
- De data som har samlats in togs emot av två mottagare program tar emot från alla partitioner.

## <a name="how-to-onboard"></a>Hur vill publicera

Att publicera till denna SKU [kontakta faktureringssupporten](https://ms.portal.azure.com/#create/Microsoft.Support) eller din Microsoft-representant. Du kan skala din kapacitet upp eller ned under månaden som uppfyller dina behov genom att lägga till eller ta bort Cu: er. Dedikerad planen är unikt eftersom du får en mer praktiska onboarding från Event Hubs-produktteamet att få flexibel distribution som passar dig. 

## <a name="next-steps"></a>Nästa steg

Kontakta Microsoft-försäljare eller Microsoft Support om du vill ha mer information om Event Hubs Dedicated kapacitet. Du kan också läsa mer om Event Hubs prisnivåer genom att besöka följande länkar:

- [Event Hubs Dedicated priser](https://azure.microsoft.com/pricing/details/event-hubs/). Du kan även kontakta din Microsoft-försäljare eller Microsoft Support om du vill ha mer information om Event Hubs Dedicated kapacitet.
- Den [Event Hubs vanliga frågor och svar](event-hubs-faq.md) innehåller prisinformation och innehåller några vanliga frågor och svar om Event Hubs.
