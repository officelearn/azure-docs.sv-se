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
ms.openlocfilehash: a5184b9980dd9f83764950445c10e8bdfea6d71a
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65203955"
---
# <a name="overview-of-event-hubs-dedicated"></a>Översikt över Event Hubs Dedicated

*Event Hubs-kluster* erbjuder en enda klient distributioner för kunder med strömmande behoven. Den här enda klient erbjudande har en garanterad serviceavtal på 99,99% och är endast tillgänglig på vår dedikerade prisnivå. Ett Event Hubs-kluster kan ingående miljontals händelser per sekund med garanterad kapacitet och subsekundära svarstider. Namnområden och event hubs som skapats i det dedikerade klustret omfattar alla funktioner på Standard-erbjudandet och mycket mer, men utan några ingående begränsningar. Den innehåller också populära [Event Hubs Capture](event-hubs-capture-overview.md) funktionen utan extra kostnad, så att du kan automatiskt batch- och log-dataströmmar till Azure Storage eller Azure Data Lake. 

Kluster etablerats och faktureras per **kapacitetsenheter (Cu: er)**, en förallokerad mängden Processortid och minnesresurser. Du kan köpa 1, 2, 4, 8, 12, 16 eller 20 Cu: er för varje kluster. Hur mycket du kan mata in och strömma per Kapacitetsenhet beror på en mängd olika faktorer, till exempel hur många producenter och konsumenter, nyttolast form utgående Betygsätt (se resultaten nedan för mer information). 

> [!NOTE]
> Alla Event Hubs-kluster är Kafka-aktiverade som standard och stöd för Kafka-slutpunkter som kan användas av din befintliga Kafka-baserade program. Med Kafka aktiverad på ditt kluster påverkar inte din av icke-Kafka-användningsfall. Det finns inga alternativ eller behöva inaktivera Kafka i ett kluster.

## <a name="why-dedicated"></a>Varför dedikerade?

Dedikerade Event Hubs erbjuder tre övertygande fördelar för kunder som behöver företagsnivå kapacitet:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Single-innehavare garanterar kapacitet för bättre prestanda

Ett dedikerat kluster garanterar kapacitet i full skala och kan skicka upp till strömmande data med fullständigt beständig lagring och subsekundära svarstid för någon gigabyte burst-trafiken. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Inklusiva och exklusivt tillgång till funktioner 
Dedikerad erbjudandet omfattar funktioner som att samla in vid utan extra kostnad, samt exklusiv åtkomst till kommande funktioner som BYOK. Tjänsten hanterar också Utjämning av nätverksbelastning, OS-uppdateringar, säkerhetsuppdateringar och partitionering för kunden, så att du kan ägna mindre tid åt infrastrukturunderhåll och mer tid på att skapa funktioner för klientsidan.  

#### <a name="cost-savings"></a>Kostnadsbesparingar
Vid hög inkommande volymer (> 100 Dataflödesenheter), ett kluster kostnader betydligt mindre per timme än köpa ett jämförbara antal dataflödesenheter i Standard-erbjudandet.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Event Hubs Dedicated kvoter och begränsningar

Event Hubs Dedicated erbjudandet debiteras enligt ett fast pris per månad, med minst 4 timmars användning. Nivån Dedicated innehåller alla funktioner i standardprenumerationen, men med enterprise skala kapacitet och begränsningar för kunder med krävande arbetsbelastningar. 

| Funktion | Standard | Dedikerad |
| --- |:---:|:---:|
| Bandbredd | 20 Dataflödesenheter (upp till 40 Dataflödesenheter) | 20 Cu: er |
| Namnområden |  1 | 50 per Kapacitetsenhet |
| Event Hubs |  10 | Obegränsad |
| Ingångshändelser | Betala per miljon händelser | Ingår |
| Meddelandestorlek | 1 miljon byte | 1 miljon byte |
| Partitioner | 40 per namnområde | 2000 per Kapacitetsenhet, 1024 per händelsehubb |
| Konsumentgrupper | 20 per Händelsehubb | Ingen gräns per Kapacitetsenhet, 1 000 per händelsehubb |
| Brokered Connections | 1 000 ingår | 100 K ingår |
| Meddelandelagring | 7 dagar, 84 GB som ingår per Dataflödesenheter | 90 dagar, 10 TB som ingår per Kapacitetsenhet |
| Capture | Betala per timme | Ingår |

## <a name="how-to-onboard"></a>Hur vill publicera

Självbetjäning-upplevelsen för registrering för dedikerad är i förhandsversion, som du kan skapa 1 CU-kluster i följande regioner:
  - Centrala Kanada
  - Västra Europa
  - USA, centrala
  - USA, Östra
  - USA, östra 2
  - USA, norra centrala
  - USA, Västra

Vi aktivt lägger till nya regioner, men under tiden om din önskade region inte finns i listan skicka en supportbegäran om att den [Event Hubs-teamet](https://ms.portal.azure.com/#create/Microsoft.Support) under *teknisk > Händelsehubbar > kvot > begära för Dedikerad SKU*. Dedikerad planen är unikt eftersom du får en mer praktiska onboarding från Event Hubs-produktteamet att få flexibel distribution som passar dig. 

## <a name="faqs"></a>Vanliga frågor och svar

#### <a name="what-can-i-achieve-with-a-cluster"></a>Vad kan jag få med ett kluster?

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

#### <a name="how-do-i-create-a-cluster-larger-than-1-cu"></a>Hur skapar jag ett kluster större än 1 Kapacitetsenhet?

I förhandsversionen av självbetjäning, kan du begära för att skala upp ditt kluster när du har skapat klustret. När du har skapat ett 1 CU kluster kontaktar du support för Event Hubs genom att skicka in en [supportförfrågan](https://ms.portal.azure.com/#create/Microsoft.Support) under *teknisk > kvot > begäran om att skala upp eller skala ned dedikerade kluster*. I vår GA-versionen kommer du att kunna skala upp din klustret direkt via portalen. 

#### <a name="can-i-scale-down-my-cluster"></a>Kan jag skala ned mitt kluster?

När du har skapat debiteras kluster för minst 4 timmars användning. I förhandsversionen av självbetjäning, kan du skicka en [supportförfrågan](https://ms.portal.azure.com/#create/Microsoft.Support) till Event Hubs-teamet under *teknisk > kvot > begäran om att skala upp eller skala ned dedikerade kluster*. Det kan ta upp till 7 dagar att slutföra förfrågan om att skala ned klustret. 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>Hur fungerar Geo-DR med mitt kluster?

Du kan geo-nyckelpar med en namnrymd under en dedikerad nivå-kluster med en annan namnrymd under en dedikerad nivå-kluster. Vi inte rekommenderar att para ihop en dedikerad nivå-namnområde med ett namnområde i våra Standard erbjudande, eftersom dataflödesgräns inte är kompatibel som leder till fel. 


#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Kan jag migrera min standardnamnområden för att tillhör en dedikerad nivå-kluster?
Vi stöder för närvarande inte en automatiserad migreringsprocessen för att migrera dina event hubs-data från en Standard-namnområdet till en dedikerad något. Om du vill migrera till ett kluster med dedikerad-nivå, rekommenderar vi tömmer alla meddelanden finns kvar i dina Standard-nivån event hubs och ersätta anslutningens slutpunkter med som dedikerade namnområdet.

## <a name="next-steps"></a>Nästa steg

Kontakta Microsoft-försäljare eller Microsoft Support om du vill ha mer information om Event Hubs Dedicated kapacitet. Du kan också läsa mer om Event Hubs prisnivåer genom att besöka följande länkar:

- [Event Hubs Dedicated priser](https://azure.microsoft.com/pricing/details/event-hubs/). Du kan även kontakta din Microsoft-försäljare eller Microsoft Support om du vill ha mer information om Event Hubs Dedicated kapacitet.
- Den [Event Hubs vanliga frågor och svar](event-hubs-faq.md) innehåller prisinformation och innehåller några vanliga frågor och svar om Event Hubs.
