---
title: "Företag affärskontinuitet och haveriberedskap återställning (BCDR): parad Azure-regioner | Microsoft Docs"
description: "Läs mer om Azure regionala länkar, för att säkerställa att programmen är flexibel under data center fel."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: c2d0a21c-2564-4d42-991a-bc31723f61a4
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2017
ms.author: raynew
ms.openlocfilehash: 27491e34ad9e47aec2f424cfc439fad614f0e435
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Företag affärskontinuitet och haveriberedskap återställning (BCDR): parad Azure-regioner

## <a name="what-are-paired-regions"></a>Vad är länkas regioner?

Azure körs i flera områden i världen. Ett Azure geografi är ett område som definierats av världen som innehåller minst en Azure-Region. En Azure-region är en del i en geografisk plats, som innehåller en eller flera datacenter.

Varje Azure-region paras ihop med en annan region inom samma geografiska, gör ett regionalt par tillsammans. Undantaget är södra paras ihop med en region utanför dess geografisk plats.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Bild 1 – Azure regionala par diagram

| geografisk plats | Parad regioner |  |
|:--- |:--- |:--- |
| Asien |Östasien |Sydostasien |
| Australien |Östra Australien |Sydöstra Australien |
| Kanada |Centrala Kanada |Östra Kanada |
| Kina |Norra Kina |Östra Kina|
| Indien |Indien, centrala |Södra Indien |
| Japan |Östra Japan |Västra Japan |
| Korea |Centrala Korea |Sydkorea |
| Nordamerika |Norra centrala USA |Södra centrala USA |
| Nordamerika |Östra USA |Västra USA |
| Nordamerika |Östra USA 2 |Centrala USA |
| Nordamerika |Västra USA 2 |Västra centrala USA |
| Europa |Nordeuropa |Västra Europa |
| Japan |Östra Japan |Västra Japan |
| Brasilien |Södra (1) |Södra centrala USA |
| Amerikanska myndigheter |Iowa (USA-förvaltad region) |Virginia (USA-förvaltad region) |
| Amerikanska myndigheter |Arizona (USA-förvaltad region) |Texas (USA-förvaltad region) |
| USA |US DoD, östra |US DoD, centrala |
| STORBRITANNIEN |Storbritannien, västra |Storbritannien, södra |
| Tyskland |Centrala Tyskland |Nordöstra Tyskland |

Tabell 1 - mappning av Azure regionala par

> (1) södra är unikt eftersom den är länkad till en region utanför sin egen geografisk plats. Brasilien söder sekundär region är södra centrala USA men södra centrala USA sekundära regionen är inte södra.


Vi rekommenderar att du replikera arbetsbelastningar över regionala par att dra fördel av Azures principer för isolering och tillgänglighet. Till exempel planerad Azure systemuppdateringar distribueras sekventiellt (inte på samma gång) över parad regioner. Det innebär att även i sällsynt händelse av en felaktig uppdatering båda regioner inte påverkas samtidigt. Osannolika för en bred strömavbrott dessutom prioriteras återställning av minst en region utanför varje par.

## <a name="an-example-of-paired-regions"></a>Ett exempel på en parad regioner
Bild 2 nedan visar ett hypotetiskt program som använder regionala paret för katastrofåterställning. Grön talen Markera mellan region aktiviteter tre Azure-tjänster (Azure compute-, lagring och databasen) och hur de konfigureras för att replikera mellan regioner. De unika fördelarna med att distribuera över parad regioner är markerade med orange siffror.

![Översikt över parad Region förmåner](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Bild 2 – hypotetiska Azure regionala par

## <a name="cross-region-activities"></a>Cross-region aktiviteter
Enligt figur 2.

![PaaS](./media/best-practices-availability-paired-regions/1Green.png) **Azure Compute (PaaS)** – måste du etablera ytterligare beräkningsresurser i förväg för att se till att resurser finns i en annan region under en katastrofåterställning. Mer information finns i [Azure återhämtning teknisk vägledning](resiliency/resiliency-technical-guidance.md).

![Lagring](./media/best-practices-availability-paired-regions/2Green.png) **Azure Storage** -Geo-Redundant lagring (GRS) är konfigurerat som standard när ett Azure Storage-konto har skapats. Med GRS replikeras dina data automatiskt tre gånger i den primära regionen och tre gånger i parad region. Mer information finns i [Azure lagringsalternativ för redundans](storage/common/storage-redundancy.md).

![Azure SQL](./media/best-practices-availability-paired-regions/3Green.png) **Azure SQL-databaser** – med Azure SQL Standard Geo-replikering kan du konfigurera asynkron replikering av transaktioner till en parad region. Med premium geo-replikering kan konfigurera du replikering till en region i världen; Vi rekommenderar dock att du distribuerar dessa resurser i en parad region för de flesta scenarier med haveriberedskap. Mer information finns i [Geo-replikering i Azure SQL Database](sql-database/sql-database-geo-replication-overview.md).

![Hanteraren för filserverresurser](./media/best-practices-availability-paired-regions/4Green.png) **Azure Resource Manager** -Resource Manager tillhandahåller natur logisk isolering av service management-komponenterna över regioner. Det innebär att logiska fel i en region som är mindre troligt påverkan på en annan.

## <a name="benefits-of-paired-regions"></a>Fördelarna med parad regioner
Enligt figur 2.  

![Isolering av](./media/best-practices-availability-paired-regions/5Orange.png)
**fysisk isolering** – när möjligt, Azure föredrar minst 300 miles uppdelning mellan Datacenter på ett regionalt par, även om detta inte är praktiskt eller möjligt i alla geografiska områden. Fysiska datacenter uppdelning minskar sannolikheten för naturkatastrof civil unrest, strömavbrott eller fysiska nätverksavbrott påverkar både regioner på samma gång. Isolering regleras av begränsningar i geografi (geography storlek, power/nätverkets infrastruktur tillgänglighet, förordningar, etc.).  

![Replikering](./media/best-practices-availability-paired-regions/6Orange.png)
**plattformen erbjuder replikering** -vissa tjänster, till exempel Geo-Redundant lagring ger automatisk replikering parad regionen.

![Återställning](./media/best-practices-availability-paired-regions/7Orange.png)
**Region recovery ordning** – i händelse av en bred avbrott, återställning av en region prioriteras utanför varje par. Program som distribueras över parad regioner är garanterat har någon av de regioner som har återställts med prioritet. Om ett program distribueras över regioner som inte har länkats kan återställning vara fördröjd – i värsta fall valda regioner kanske de sista två som ska återställas.

![Uppdateringar](./media/best-practices-availability-paired-regions/8Orange.png)
**sekventiella uppdateringar** – planerade Azure systemuppdateringar är distribuerat till parad regioner sekventiellt (inte på samma gång) för att minimera driftstopp, effekten av buggar och logiskt misslyckade begäranden om av en felaktig Uppdatera.

![Data](./media/best-practices-availability-paired-regions/9Orange.png)
**Data land** – en region befinner sig inom samma geografiska som dess par (med undantag för södra) för att uppfylla kraven för land för skatt och lag tvingande behörighet.
