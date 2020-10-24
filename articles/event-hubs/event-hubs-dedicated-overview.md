---
title: Översikt över dedikerade Event Hub – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller en översikt över dedikerade Azure-Event Hubs, som erbjuder distributioner av enskilda innehavare av Event Hub.
ms.topic: article
ms.date: 10/23/2020
ms.openlocfilehash: e6208a8d50e21766969dbe9d9739d5003958126a
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92495109"
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

[!INCLUDE [event-hubs-dedicated-clusters-faq](../../includes/event-hubs-dedicated-clusters-faq.md)]

## <a name="next-steps"></a>Nästa steg

Kontakta din Microsoft-representant eller Microsoft Support för att få mer information om Event Hubs Dedicated. Du kan också skapa ett kluster eller lära dig mer om Event Hubs pris nivåer genom att gå till följande länkar:

- [Skapa ett Event Hubs kluster via Azure Portal](https://aka.ms/eventhubsclusterquickstart) 
- [Event Hubs Dedicated prissättning](https://azure.microsoft.com/pricing/details/event-hubs/). Du kan också kontakta din Microsoft-representant eller Microsoft Support för att få mer information om Event Hubs Dedicated kapacitet.
- [Event HUBS vanliga](event-hubs-faq.md) frågor och svar innehåller pris information och svar på några vanliga frågor om Event Hubs.
