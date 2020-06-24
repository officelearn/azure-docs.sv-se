---
title: Översikt över dedikerade Event Hub – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller en översikt över dedikerade Azure-Event Hubs, som erbjuder distributioner av enskilda innehavare av Event Hub.
services: event-hubs
documentationcenter: na
author: spelluru
ms.assetid: ''
ms.service: event-hubs
ms.topic: article
ms.date: 06/23/2020
ms.author: spelluru
ms.reviewer: shvija
ms.openlocfilehash: 145fc759b236019630b8942c677dbdd562331e4c
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2020
ms.locfileid: "85298743"
---
# <a name="overview-of-event-hubs-dedicated"></a>Översikt över Event Hubs Dedicated

*Event Hubs kluster* erbjuder distributioner för enskilda klienter för kunder med de mest krävande strömnings behoven. Det här erbjudandet för en enda klient organisation har ett garanterat 99,99% SLA och är bara tillgängligt på vår dedikerade pris nivå. Ett Event Hubs kluster kan intränga miljon tals händelser per sekund med garanterad kapacitet och under sekund svars tid. Namn områden och händelse nav som skapats i det dedikerade klustret inkluderar alla funktioner i standard erbjudandet och mer, men utan några ingångs gränser. Den innehåller också den populära [Event Hubs Capture](event-hubs-capture-overview.md) -funktionen utan extra kostnad, så att du automatiskt kan registrera och logga data strömmar till Azure Storage eller Azure Data Lake. 

Kluster etablerade och debiteras efter **kapacitets enheter (CUS)**, en förallokerad mängd processor-och minnes resurser. Du kan köpa 1, 2, 4, 8, 12, 16 eller 20 CUs för varje kluster. Hur mycket du kan mata in och strömma per CU beror på en mängd olika faktorer, till exempel antalet tillverkare och konsumenter, nytto lasts form, utgående taxa (se benchmark-resultat nedan för mer information). 

> [!NOTE]
> Alla Event Hubs-kluster är Kafka-aktiverade som standard och stöder Kafka-slutpunkter som kan användas av dina befintliga Kafka-baserade program. Att ha Kafka aktiverat i klustret påverkar inte dina användnings fall som inte är Kafka; Det finns inget alternativ eller behöver inaktivera Kafka på ett kluster.

## <a name="why-dedicated"></a>Varför dedikerat?

Dedikerade Event Hubs erbjuder tre övertygande förmåner för kunder som behöver kapacitet på företags nivå:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>En enda innehavare garanterar kapaciteten för bättre prestanda

Ett dedikerat kluster garanterar kapacitet i full skala och kan gå upp till gigabyte av strömmande data med fullständigt varaktiga lagrings utrymme och under andra svars tider för att hantera all burst i trafiken. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Inklusive och exklusiv åtkomst till funktioner 
Det dedikerade erbjudandet omfattar funktioner som att samla in utan extra kostnad, samt exklusiv åtkomst till kommande funktioner som Bring Your Own Key (BYOK). Tjänsten hanterar även belastnings utjämning, OS-uppdateringar, säkerhets korrigeringar och partitionering för kunden, så att du kan lägga mindre tid på infrastruktur underhåll och mer tid på att skapa funktioner på klient sidan.  

#### <a name="cost-savings"></a>Kostnads besparingar
Vid höga ingångs volymer (>100 antal) är ett kluster kostar betydligt mindre per timme än att köpa en jämförbar mängd data flödes enheter i standard erbjudandet.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Event Hubs Dedicated kvoter och begränsningar

Event Hubs Dedicated erbjudandet debiteras till ett fast månads pris, med minst fyra timmars användning. Den dedikerade nivån innehåller alla funktioner i standard planen, men med kapacitet för företags skala och gränser för kunder med krävande arbets belastningar. 

| Funktion | Standard | Dedikerad |
| --- |:---:|:---:|
| Bandbredd | 20 antal (upp till 40 antal) | 20 CUs |
| Namnrymder |  1 | 50 per CU |
| Event Hubs |  10 per namnrymd | 1000 per namnrymd |
| Ingress-händelser | Betala per miljon händelser | Ingår |
| Meddelande storlek | 1 000 000 byte | 1 000 000 byte |
| Partitioner | 32 per Event-hubb | 1024 per Event-hubb |
| Konsumentgrupper | 20 per Event-hubb | Ingen gräns per CU, 1000 per Event-hubb |
| Brokered Connections | 1 000 ingår, 5 000 max | 100 KB ingår och Max |
| Kvarhållning av meddelanden | 7 dagar, 84 GB ingår per data flödes enheter | 90 dagar, 10 TB inkluderat per CU |
| Capture | Betala per timme | Ingår |

## <a name="how-to-onboard"></a>Publicera

Den självbetjänings upplevelsen för att [skapa ett Event Hubs kluster](event-hubs-dedicated-cluster-create-portal.md) via [Azure Portal](https://aka.ms/eventhubsclusterquickstart) är nu i för hands version. Om du har några frågor eller behöver hjälp med att registrera dig Event Hubs Dedicated kan du kontakta [Event Hubs-teamet](mailto:askeventhubs@microsoft.com).

## <a name="faqs"></a>Vanliga frågor och svar

#### <a name="what-can-i-achieve-with-a-cluster"></a>Vad kan jag uppnå med ett kluster?

För ett Event Hubs kluster, hur mycket du kan mata in och strömma beror på olika faktorer, till exempel producenter, konsumenter, den hastighet som du matar in och bearbetar, och mycket mer. 

I följande tabell visas de benchmark-resultat som vi uppnått vid testningen:

| Nytto Last form | Mottagare | Ingress bandbredd| Ingress meddelanden | Utgående bandbredd | Utgående meddelanden | Totalt antal antal | Antal per CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Batchar av 100x1KB | 2 | 400 MB/SEK | 400k meddelanden/SEK | 800 MB/SEK | 800k meddelanden/SEK | 400 antal | 100 antal | 
| Batchar av 10x10KB | 2 | 666 MB/SEK | 66.6 k meddelanden/SEK | 1,33 GB/SEK | 133k meddelanden/SEK | 666 antal | 166 antal |
| Batchar av 6x32KB | 1 | 1,05 GB/SEK | 34k meddelanden/SEK | 1,05 GB/SEK | 34k meddelanden/SEK | 1000 antal | 250 antal |

I testningen användes följande kriterier:

- Ett dedikerat nivå Event Hubs kluster med fyra kapacitets enheter (CUs) användes. 
- Händelsehubben som används för inmatning hade 200 partitioner. 
- De data som matats in togs emot av två mottagar program som tar emot från alla partitioner.

#### <a name="can-i-scale-updown-my-cluster"></a>Kan jag skala upp/ned mitt kluster?

Efter skapandet faktureras kluster för minst 4 timmars användning. I för hands versionen av den självbetjänings upplevelsen kan du skicka en [supportbegäran](https://ms.portal.azure.com/#create/Microsoft.Support) till Event Hubss gruppen under *teknisk > kvot > begäran om att skala upp eller ned ett dedikerat kluster* för att skala upp eller ned ett dedikerat kluster. Det kan ta upp till 7 dagar att slutföra begäran om att skala upp klustret. 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>Hur fungerar geo-DR med mitt kluster?

Du kan geo-para ett namn område under ett dedikerat-nivå kluster med ett annat namn område under ett dedikerat skikt kluster. Vi uppmuntrar inte att para ihop ett dedikerat namn område med ett namn område i vårt standard erbjudande, eftersom data flödes gränsen kommer att vara inkompatibel, vilket leder till fel. 

#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Kan jag migrera mina standard namn rymder så att de tillhör ett dedikerat kluster?
Vi stöder för närvarande inte en automatiserad migreringsprocessen för att migrera dina Event Hub-data från ett standard namn område till en dedikerad. 

## <a name="next-steps"></a>Nästa steg

Kontakta din Microsoft-representant eller Microsoft Support för att få mer information om Event Hubs Dedicated. Du kan också skapa ett kluster eller lära dig mer om Event Hubs pris nivåer genom att gå till följande länkar:

- [Skapa ett Event Hubs-kluster via Azure Portal](https://aka.ms/eventhubsclusterquickstart) 
- [Event Hubs Dedicated prissättning](https://azure.microsoft.com/pricing/details/event-hubs/). Du kan också kontakta din Microsoft-representant eller Microsoft Support för att få mer information om Event Hubs Dedicated kapacitet.
- [Event HUBS vanliga](event-hubs-faq.md) frågor och svar innehåller pris information och svar på några vanliga frågor om Event Hubs.
