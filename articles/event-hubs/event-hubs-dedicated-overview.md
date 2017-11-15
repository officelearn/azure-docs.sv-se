---
title: "Översikt över Azure Event Hubs dedikerade kapacitet | Microsoft Docs"
description: "Översikt över Microsoft Azure Event Hubs dedikerade kapacitet."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2017
ms.author: sethm;babanisa
ms.openlocfilehash: 613ea691e38b6f0bcd8873fc2ec6bcafb3cc6c78
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2017
---
# <a name="overview-of-event-hubs-dedicated"></a>Översikt av Händelsehubbar dedikerad

*Event Hubs dedikerade* kapacitet ger stöd för en innehavare distributioner för kunder med de mest krävande krav. I full skala Azure Event Hubs kan ingång över 2 miljoner händelser per sekund eller upp till 2 GB per sekund telemetri med fullständigt varaktiga svarstid för lagring och sekund. Integrerad lösningar kan också av bearbetning i realtid och batch på samma system. Med [Event Hubs avbilda](event-hubs-capture-overview.md) ingår i erbjudandet kan du minska komplexiteten i din lösning genom att ha en enda dataström som stöder både i realtid och batch-baserade pipelines.

I följande tabell jämförs tillgängliga tjänstnivåer för Händelsehubbar. Erbjudande för Event Hubs dedikerad är ett fast månadspris jämfört med användningen priser för de flesta funktioner i Standard. Dedikerad nivån erbjuder funktioner av planen för Standard, men med enterprise skala kapacitet för kunder med större arbetsbelastning. 

| Funktion | Standard | Dedikerad |
| --- |:---:|:---:|:---:|
| Ingångshändelser | Betala per miljoner händelser | Ingår |
| Genomflödesenhet (1 MB per sekund ingång, utgång 2 MB per sekund) | Betala per timme | Ingår |
| Meddelandestorlek | 256 kB | 1 MB |
| Utgivarprinciper | Ja | Ja |   
| Konsumentgrupper | 20 | 20 |
| Återuppspelning av meddelande | Ja | Ja |
| Högsta antal Throughput Units | 20 (flexibel till 100)   | 1 CU≈50 |
| Brokered Connections | 1 000 ingår | 100 K ingår |
| Fler Brokered Connections | Ja | Ja |
| Meddelandelagring | 1 dag ingår | Upp till sju dagar ingår |
| Capture | Betala per timme | Ingår |

## <a name="benefits-of-event-hubs-dedicated-capacity"></a>Fördelarna med Event Hubs dedikerade kapacitet

När du använder Event Hubs dedikerad finns följande fördelar:

* En organisation som värd har inga brus från andra klienter.
* Meddelandestorlek ökas till 1 MB jämfört med 256 KB för Standard.
* Repeterbara prestanda varje gång.
* Garanterat kapacitet för att uppfylla dina burst-behov.
* Innehåller den [avbilda](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview) funktion i Azure Event Hubs, att ge integration med micro batch och långsiktig kvarhållning
* Noll Underhåll: vi hanterar belastningsutjämning, OS uppdateringar, säkerhetsuppdateringar och partitionering.
* Fast priser för varje timme.
* Meddelande kvarhållning upp till 7 dagar med utan extra kostnad

Event Hubs dedikerat tar också bort vissa begränsningar för Standard erbjudande genomflöde. Genomflödesenheter på standardnivån ha rätt att 1000 händelser per sekund eller 1 MB per sekund ingång per TU och dubbla denna mängd utgång. Dedikerad skala erbjudandet har inga begränsningar för ingång och utgång händelse räknar. Dessa gränser regleras bara av bearbetningskapacitet köpta händelsehubbar.

Den här reserverade, dedikerade miljön innehåller andra funktioner som är unika för det här skiktet som:

* Kontrollera antalet namnområden i klustret
* Ange dataflöde limitson varje din namnområden
* Konfigurera antalet Händelsehubbar under varje namnområde
* Fastställa gränsen för antalet partitioner

Den här tjänsten är riktad mot de största telemetri användarna och är tillgänglig för kunder med ett enterprise-avtal.

## <a name="how-to-onboard"></a>Hur du integrerar

Du kan skala din kapacitet upp eller ned under månaden efter behov genom att lägga till eller ta bort anpassad Communi. Dedikerad planen är unikt i att du får en mer praktiska onboarding från produktteamet Event Hubs för att hämta den flexibla distributionen som passar dig. Publicera till denna SKU kontakt (faktureringssupporten) [https://ms.portal.azure.com/#create/Microsoft.Support] eller din Microsoft-representant.

## <a name="next-steps"></a>Nästa steg
Kontakta Microsoft säljare eller Microsoft-supporten om du vill ha mer information om händelsen hubbar dedikerade kapacitet. Du kan också få mer information om Händelsehubbar genom att gå till följande länkar:

Detaljerad information om priser finns i följande länkar:

- [Event Hubs dedikerad priser](https://azure.microsoft.com/pricing/details/event-hubs/). Du kan också kontakta din Microsoft säljare eller Microsoft-supporten om du vill ha mer information om händelsen hubbar dedikerade kapacitet.
- Den [Event Hubs FAQ](event-hubs-faq.md) innehåller information om priser och besvarar några vanliga frågor om Händelsehubbar. 
