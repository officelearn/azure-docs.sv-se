---
title: Översikt över dedikerade event hubs – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller en översikt över dedciated Azure Event Hubs, som erbjuder en enda klient distributioner av händelsehubbar.
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
ms.openlocfilehash: 2a1785b9c749a8c413987974446190aafc08ed3a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58105598"
---
# <a name="overview-of-event-hubs-dedicated"></a>Översikt över Event Hubs Dedicated

*Event Hubs Dedicated* kapacitet erbjuder en enda klient distributioner för kunder med de mest krävande krav. I full skala, Azure Event Hubs kan ingående över 2 miljoner händelser per sekund eller upp till 2 GB per sekund telemetri med fullständigt varaktig lagring och subsekundära svarstider. Detta kan också integrerade lösningar av bearbetning i realtid och batch på samma system. Med [Event Hubs Capture](event-hubs-capture-overview.md) ingår i erbjudandet, kan du minska lösningens komplexitet genom att använda en enda dataström som stöder både i realtid och batch-baserade pipelines.

I följande tabell jämförs tillgängliga tjänstnivåerna för Event Hubs. Event Hubs Dedicated-erbjudandet är ett fast månadspris jämfört med användningen priserna för de flesta funktioner i Standard. Nivån Dedicated innehåller alla funktioner i standardprenumerationen, men med enterprise skala kapacitet för kunder med krävande arbetsbelastningar. 

| Funktion | Standard | Dedikerad |
| --- |:---:|:---:|
| Ingångshändelser | Betala per miljon händelser | Ingår |
| Throughput unit (1 MB/s ingångshändelser, 2 MB/s utgångshändelser) | Betala per timme | Ingår |
| Meddelandestorlek | 1 MB | 1 MB |
| Utgivarprinciper | Ja | Ja |   
| Konsumentgrupper | 20 | 20 |
| Återuppspelning av meddelande | Ja | Ja |
| Högsta antal Throughput Units | 20 (flexibla och 100)   | 1 Kapacitetsenhet (CU) ≈ 50 |
| Brokered Connections | 1 000 ingår | 100 K ingår |
| Fler Brokered Connections | Ja | Ja |
| Meddelandelagring | 1 dag ingår | Upp till sju dagar ingår |
| Capture | Betala per timme | Ingår |

## <a name="benefits-of-event-hubs-dedicated-capacity"></a>Fördelarna med Event Hubs Dedicated kapacitet

Följande fördelar är tillgängliga när du använder Event Hubs Dedicated:

* Enskild klient som är värd för har inga brus från andra klienter.
* Repeterbara prestanda varje gång.
* Garanterad kapacitet för att uppfylla dina burst-behov.
* Innehåller den [avbilda](event-hubs-capture-overview.md) funktionen i Event Hubs, att tillhandahålla integrering med mindre batchar och långsiktig kvarhållning.
* Noll Underhåll: Tjänsten hanterar belastningsutjämning, OS-uppdateringar, säkerhetsuppdateringar och partitionering.
* Fast prissättning per timme.
* Kvarhållning av meddelanden i upp till 7 dagar utan extra kostnad.

Event Hubs Dedicated tar också bort vissa begränsningar som dataflöde av Standard-erbjudandet. Dataflödesenheter på Standard-nivån ger dig rätt till 1000 händelser per sekund eller 1 MB per sekund för ingångshändelser per Dataflödesenheter och dubbla hälften av utgående trafik. Dedikerad skala erbjudandet har inga begränsningar på inkommande och utgående händelse räknas. Dessa gränser regleras endast av bearbetning av kapaciteten för köpta event hubs.

Den här reserverade, dedikerad miljö innehåller andra funktioner som är unika för den här nivån kan till exempel:

* Styr antalet namnområden i klustret.
* Anger dataflödesbegränsningar på var och en av dina namnområden.
* Konfigurerar antalet händelsehubbar under varje namnområde.
* Anger gränsen för antalet partitioner.

Den här tjänsten är avsett för de största telemetri och är tillgängligt för kunder med ett enterprise-avtal.

## <a name="how-to-onboard"></a>Hur vill publicera

Du kan skala din kapacitet upp eller ned under månaden som uppfyller dina behov genom att lägga till eller ta bort Cu: er. Dedikerad planen är unikt eftersom du får en mer praktiska onboarding från Event Hubs-produktteamet att få flexibel distribution som passar dig. Att publicera till denna SKU [kontakta faktureringssupporten](https://ms.portal.azure.com/#create/Microsoft.Support) eller din Microsoft-representant.

## <a name="next-steps"></a>Nästa steg

Kontakta Microsoft-försäljare eller Microsoft Support om du vill ha mer information om Event Hubs Dedicated kapacitet. Du kan också läsa mer om Event Hubs prisnivåer genom att besöka följande länkar:

- [Event Hubs Dedicated priser](https://azure.microsoft.com/pricing/details/event-hubs/). Du kan även kontakta din Microsoft-försäljare eller Microsoft Support om du vill ha mer information om Event Hubs Dedicated kapacitet.
- Den [Event Hubs vanliga frågor och svar](event-hubs-faq.md) innehåller prisinformation och innehåller några vanliga frågor och svar om Event Hubs. 
