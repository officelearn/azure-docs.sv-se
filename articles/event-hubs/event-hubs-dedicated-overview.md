---
title: Översikt över dedikerade händelsehubbar – Azure Event Hubs | Microsoft-dokument
description: Den här artikeln innehåller en översikt över dedikerade Azure Event Hubs, som erbjuder distributioner av en enda klient av händelsehubbar.
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
ms.openlocfilehash: f67be1d31125b21048deca4d9cafcc76f4ffc3b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72516740"
---
# <a name="overview-of-event-hubs-dedicated"></a>Översikt över dedikerade eventhubbar

*Event Hubs-kluster* erbjuder distributioner med en klientorganisation för kunder med de mest krävande streamingbehoven. Detta erbjudande med en enda klient har ett garanterat serviceavtal på 99,99 % och är endast tillgängligt på vår dedikerade prisnivå. Ett Event Hubs-kluster kan gå in i miljontals händelser per sekund med garanterad kapacitet och undersändningssvarstid. Namnområden och händelsehubbar som skapats i det dedikerade klustret innehåller alla funktioner i standarderbjudandet med mera, men utan några begränsningar för ingående. Den innehåller också den populära [funktionen Event Hubs Capture](event-hubs-capture-overview.md) utan extra kostnad, så att du automatiskt kan batch- och loggdataströmmar till Azure Storage eller Azure Data Lake. 

Kluster etableras och faktureras av **kapacitetsenheter ,** en förallokerad mängd processor- och minnesresurser. Du kan köpa 1, 2, 4, 8, 12, 16 eller 20 CUs för varje kluster. Hur mycket du kan inta och strömma per CU beror på en mängd olika faktorer, såsom antalet producenter och konsumenter, nyttolastform, utgående ränta (se referensresultat nedan för mer information). 

> [!NOTE]
> Alla Event Hubs-kluster är Kafka-aktiverade som standard och stöder Kafka-slutpunkter som kan användas av dina befintliga Kafka-baserade program. Att ha Kafka aktiverat i klustret påverkar inte dina icke-Kafka-användningsfall. Det finns inget alternativ eller behöver inaktivera Kafka på ett kluster.

## <a name="why-dedicated"></a>Varför dedikerad?

Dedikerade eventhubbar erbjuder tre övertygande fördelar för kunder som behöver kapacitet på företagsnivå:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>En hyresrätt garanterar kapacitet för bättre prestanda

Ett dedikerat kluster garanterar kapacitet i full skala och kan gå in på upp till gigabyte strömmande data med helt hållbar lagring och undersektorsfördröjning för att hantera eventuella fördröjningar i trafiken. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Inkluderande och exklusiv tillgång till funktioner 
Det dedikerade erbjudandet innehåller funktioner som Capture utan extra kostnad, samt exklusiv tillgång till kommande funktioner som Bring Your Own Key (BYOK). Tjänsten hanterar också belastningsutjämning, OS-uppdateringar, säkerhetskorrigeringar och partitionering för kunden, så att du kan lägga mindre tid på underhåll av infrastruktur och mer tid på att bygga funktioner på klientsidan.  

#### <a name="cost-savings"></a>Kostnadsbesparingar
Vid höga inträngningsvolymer (>100 RU) kostar ett kluster betydligt mindre per timme än att köpa en jämförbar mängd dataflödesenheter i standarderbjudandet.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Dedikerade kvoter och gränser för eventhubbar

Event Hubs Dedikerad erbjudande faktureras till ett fast månadspris, med minst 4 timmars användning. Den dedikerade nivån erbjuder alla funktioner i standardplanen, men med företagets skalningskapacitet och begränsningar för kunder med krävande arbetsbelastningar. 

| Funktion | Standard | Dedikerad |
| --- |:---:|:---:|
| Bandbredd | 20 TUs (upp till 40 RU) | 20 CUs |
| Namnområden |  1 | 50 per cu |
| Händelsehubbar |  10 per namnområde | 1000 per namnområde |
| Ingående händelser | Betala per miljon evenemang | Ingår |
| Meddelandestorlek | 1 miljon byte | 1 miljon byte |
| Partitioner | 32 per händelsehubb | 1024 per händelsehubben |
| Konsumentgrupper | 20 per händelsehubb | Ingen gräns per CU, 1000 per händelsenav |
| Förmedlade anslutningar | 1 000 ingår, 5 000 max | 100 K ingår och max |
| Lagring av meddelanden | 7 dagar, 84 GB ingår per TU | 90 dagar, 10 TB ingår per CU |
| Capture | Betala per timme | Ingår |

## <a name="how-to-onboard"></a>Så här ombord

Självbetjäningsupplevelsen för att [skapa ett eventhubbar-kluster](event-hubs-dedicated-cluster-create-portal.md) via [Azure Portal](https://aka.ms/eventhubsclusterquickstart) är nu i förhandsversion. Om du har några frågor eller behöver hjälp med introduktion till Event Hubs Dedicated kontaktar du [eventhubbar-teamet](mailto:askeventhubs@microsoft.com).

## <a name="faqs"></a>Vanliga frågor och svar

#### <a name="what-can-i-achieve-with-a-cluster"></a>Vad kan jag uppnå med ett kluster?

För ett eventhubbar-kluster beror hur mycket du kan få och strömma på olika faktorer som dina producenter, konsumenter, i vilken takt du får i första hand och bearbetar och mycket mer. 

Följande tabell visar de benchmark-resultat som vi uppnådde under vår testning:

| Nyttolastform | Mottagare | Bandbredd för inträngning| Inkommande meddelanden | Bandbredd för utgående inträngning | Utgående meddelanden | Totalt antal ru: er | TUs per CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Partier av 100x1KB | 2 | 400 MB/sek | 400k meddelanden/sek | 800 MB/sek | 800k meddelanden/sek | 400 TUs | 100 TUs | 
| Partier av 10x10KB | 2 | 666 MB/sek | 66.6k meddelanden/sek | 1,33 GB/sek | 133k meddelanden per sekund | 666 TUs | 166 TUs |
| Partier av 6x32KB | 1 | 1,05 GB/sek | 34k meddelanden / sek | 1,05 GB/sek | 34k meddelanden per sekund | 1000 TUs | 250 TUs |

I testet användes följande kriterier:

- Ett dedikerat eventhubbar-kluster med fyra kapacitetsenheter (CUs) användes. 
- Händelsehubben som användes för inmatning hade 200 partitioner. 
- De data som intas togs emot av två mottagarprogram som tog emot från alla partitioner.

#### <a name="can-i-scale-updown-my-cluster"></a>Kan jag skala upp/ned mitt kluster?

När kluster har skapats debiteras de minst 4 timmars användning. I förhandsversionen av självbetjäningsupplevelsen kan du skicka en [supportbegäran](https://ms.portal.azure.com/#create/Microsoft.Support) till Event Hubs-teamet under *Teknisk > Kvot > Begäran om att skala upp eller skala ned dedikerat kluster* för att skala upp eller ned ditt kluster för att skala upp eller ned ditt kluster. Det kan ta upp till 7 dagar att slutföra begäran om att skala ned klustret. 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>Hur fungerar Geo-DR med mitt kluster?

Du kan geopara ett namnområde under ett kluster på dedikerad nivå med ett annat namnområde under ett kluster på dedikerad nivå. Vi uppmuntrar inte parkoppling av ett dedikerat namnområde med ett namnområde i vårt standarderbjudande, eftersom dataflödesgränsen är inkompatibel vilket resulterar i fel. 

#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Kan jag migrera mina standardnamnområden till ett kluster på dedikerad nivå?
Vi stöder för närvarande inte en automatiserad migreringsprocess för att migrera dina händelsehubbardata från ett standardnamnområde till ett dedikerat. 

## <a name="next-steps"></a>Nästa steg

Kontakta din Microsoft-säljare eller Microsoft Support för att få ytterligare information om dedikerade eventhubbar. Du kan också skapa ett kluster eller lära dig mer om prisnivåer för Event Hubs genom att besöka följande länkar:

- [Skapa ett eventhubbar-kluster via Azure Portal](https://aka.ms/eventhubsclusterquickstart) 
- [Dedikerad prissättning för eventhubbar](https://azure.microsoft.com/pricing/details/event-hubs/). Du kan också kontakta din Microsoft-säljare eller Microsoft Support för att få ytterligare information om eventhubbar dedikerad kapacitet.
- [Vanliga frågor och svar om eventhubbar](event-hubs-faq.md) innehåller prisinformation och svarar på några vanliga frågor om eventhubbar.
