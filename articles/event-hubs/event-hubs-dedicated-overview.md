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
ms.openlocfilehash: e8a2d8321a42e8b3d090c1ce1fdb3fd9a7ee3714
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138664"
---
# <a name="overview-of-event-hubs-dedicated"></a>Översikt över Event Hubs Dedicated

Azure Event Hubs-kluster ger en enda klient distributioner för kunder med strömmande behoven. Det här erbjudandet för enstaka klientorganisationer har en garanterad serviceavtal på 99,99%. Det är bara tillgängliga på dedikerad prisnivå.

Ett Event Hubs-kluster kan ingående miljontals händelser per sekund med garanterad kapacitet och subsecond svarstid. Namnområden och event hubs som skapats i det dedikerade klustret omfattar alla funktioner på Standard-erbjudandet och mycket mer, men utan några ingående begränsningar. Den innehåller också de [Event Hubs Capture](event-hubs-capture-overview.md) funktionen utan extra kostnad. Du kan använda den för att automatiskt batch- och log-dataströmmar till Azure Storage eller Azure Data Lake.

Dedikerade kluster är etablerad och faktureras per kapacitetsenheter (Cu: er). Cu: er är en förallokerade mängden Processortid och minnesresurser. Du kan köpa 1, 2, 4, 8, 12, 16 och 20 Cu: er för varje kluster. Hur mycket du kan mata in och stream per Kapacitetsenhet beror på faktorer som till exempel antal producenter och konsumenter, nyttolast-form och hastighet för utgående.

Mer information finns i tabellen med resultaten.

## <a name="why-use-event-hubs-dedicated"></a>Varför ska jag använda Event Hubs Dedicated?

Event Hubs Dedicated erbjuder tre fördelar för kunder som behöver företagsnivå kapacitet.

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Single-innehavare garanterar kapacitet för bättre prestanda

Ett dedikerat kluster garanterar kapacitet i full skala. Den kan skicka upp till gigabyte strömmande data med fullständigt beständig lagring och subsecond svarstid för att hantera toppar i trafiken.

#### <a name="inclusive-and-exclusive-access-to-features"></a>Inklusiva och exklusivt tillgång till funktioner 
Dedikerad erbjudandet omfattar funktioner som avbildning utan extra kostnad. Den erbjuder även exklusiv åtkomst till kommande funktioner som BYOK. Tjänsten hanterar också Utjämning av nätverksbelastning, OS-uppdateringar, säkerhetsuppdateringar och partitionering. Med dessa funktioner kan ägna du mindre tid åt infrastrukturunderhåll och mer tid på att skapa funktioner för klientsidan.

#### <a name="cost-savings"></a>Kostnadsbesparingar
Vid hög inkommande volymer, ett kluster kostnader betydligt mindre per timme än om du köper ett jämförbara antal dataflödesenheter (Dataflödesenheter) i Standard-erbjudandet. En stor volym är > 100 Dataflödesenheter.


## <a name="event-hubs-standard-vs-dedicated"></a>Event Hubs Standard vs. Dedikerad

I följande tabell jämförs tillgängliga tjänstnivåerna för Event Hubs. Event Hubs Dedicated erbjudandet debiteras utifrån ett fast månadspris jämfört med användningen priserna för de flesta funktioner i Standard. Nivån Dedicated innehåller alla funktioner i standardprenumerationen, men med stora kapacitet för kunder med krävande arbetsbelastningar.

| Funktion | Standard | Dedikerad |
| --- |:---:|:---:|
| Ingångshändelser | Betala per miljon händelser | Ingår |
| Throughput unit (1 MB/s ingångshändelser, 2 MB/s utgångshändelser) | Betala per timme | Ingår |
| Meddelandestorlek | 1 MB | 1 MB |
| Partitioner | 40 per namnområde | 2 000 per Kapacitetsenhet |
| Konsumentgrupper | 20 per händelsehubb | 1 000 per händelsehubb |
| Maximal bandbredd | 20 Dataflödesenheter (upp till 40 Dataflödesenheter) | 20 Cu: er |
| Brokered Connections | 1 000 ingår | 100 000 ingår |
| Meddelandelagring | En dag ingår | Upp till sju dagar ingår |
| Capture | Betala per timme | Ingår |

## <a name="what-can-i-achieve-with-a-cluster"></a>Vad kan jag få med ett kluster?

För ett Event Hubs-kluster beror hur mycket du kan mata in och strömma på din producenter, konsumenterna, den hastighet med vilken du mata in och bearbeta och mycket mer.

I följande tabell visas de resultaten som har uppnått under testningen.

| Nyttolasten form | Mottagare | Ingående bandbredd| Inkommande meddelanden | Utgående bandbredd | Utgående meddelanden | Totalt antal Dataflödesenheter | Dataflödesenheter per Kapacitetsenhet |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Batchar med 100x1KB | 2 | 400 MB/sek | 400 000 meddelanden per sekund | 800 MB/sek | 800 000 meddelanden per sekund | 400 Dataflödesenheter | 100 Dataflödesenheter | 
| Batchar med 10x10KB | 2 | 666 MB/sek | 66,600 meddelanden/sek | 1,33 GB/sek | 133,000 meddelanden/sek | 666 Dataflödesenheter | 166 Dataflödesenheter |
| Batchar med 6x32KB | 1 | 1,05 GB/sek | 34 000 meddelanden per sekund | 1,05 GB/sek | 34 000 meddelanden per sekund | 1 000 Dataflödesenheter | 250 Dataflödesenheter |

I testet användes följande kriterier:

- En dedikerad nivån Event Hubs-kluster med fyra kapacitetsenheter användes.
- Den händelsehubb som används för datainmatning hade 200 partitioner.
- De data som har samlats in togs emot av två mottagar-program. De data togs emot från alla partitioner.

## <a name="use-event-hubs-dedicated"></a>Use Event Hubs Dedicated

Du använder Event Hubs Dedicated [kontakta faktureringssupporten](https://ms.portal.azure.com/#create/Microsoft.Support) eller din Microsoft-representant. Du kan skala din kapacitet upp eller ned under månaden som uppfyller dina behov genom att lägga till eller ta bort Cu: er. Produktteamet Event Hubs hjälper dig att få flexibel distribution som passar dig.

## <a name="next-steps"></a>Nästa steg

Kontakta Microsoft-försäljare eller Microsoft Support om du vill ha mer information om Event Hubs Dedicated kapacitet. Om du vill veta mer om Event Hubs prisnivåer, Använd följande länkar:

- [Event Hubs Dedicated priser](https://azure.microsoft.com/pricing/details/event-hubs/). Du kan även kontakta din Microsoft-försäljare eller Microsoft Support om du vill ha mer information om Event Hubs Dedicated kapacitet.
- Den [Event Hubs vanliga frågor och svar](event-hubs-faq.md) innehåller prisinformation och innehåller några vanliga frågor och svar om Event Hubs.
