---
title: Verksamhets kontinuitet & haveri beredskap – Azure-kopplade regioner
description: Lär dig mer om regional länkning i Azure för att säkerställa att programmen är elastiska vid data Center haverier.
author: rayne-wiselman
manager: carmon
ms.service: multiple
ms.topic: article
ms.date: 07/01/2019
ms.author: raynew
ms.openlocfilehash: b71048412f5715fd1b8ef3edf742716916672bd5
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718744"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Verksamhets kontinuitet och haveri beredskap (BCDR): Azure-kopplade regioner

## <a name="what-are-paired-regions"></a>Vad är kopplade regioner?

Azure fungerar i flera geografiska områden runtom i världen. En Azure-geografi är ett definierat område i världen som innehåller minst en Azure-region. En Azure-region är ett område inom ett geografiskt område som innehåller ett eller flera data Center.

Varje Azure-region är kopplad till en annan region inom samma geografi, tillsammans med ett regionalt par. Undantaget är södra Brasilien, som är länkat till en region utanför dess geografiska område. I region par i Azure serialiseras plattforms uppdateringar (planerat underhåll) så att endast en kopplad region uppdateras i taget. I händelse av ett avbrott som påverkar flera regioner prioriteras minst en region i varje par för återställning.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Bild 1 – regionala Azure-par

| Placering | Länkade regioner |  |
|:--- |:--- |:--- |
| Asien |Asien, östra |Sydostasien |
| Australien |Australien, östra |Australien, sydöstra |
| Australien |Australien, centrala |Australien, centrala 2 |
| Brasilien |Brasilien, södra |USA, södra centrala |
| Kanada |Kanada, centrala |Kanada, östra |
| Kina |Kina, norra |Kina, östra|
| Kina |Kina, norra 2 |Kina, östra 2|
| Europa |Europa, norra (Irland) |Europa, västra (Nederländerna) |
| Frankrike |Frankrike, centrala|Frankrike, södra|
| Tyskland |Tyskland, centrala |Tyskland, nordöstra |
| Indien |Indien, centrala |Indien, södra |
| Indien |Indien, västra |Indien, södra |
| Japan |Japan, östra |Japan, västra |
| Korea |Sydkorea, centrala |Sydkorea, södra |
| Nordamerika |USA, östra |USA, västra |
| Nordamerika |USA, östra 2 |USA, centrala |
| Nordamerika |USA, norra centrala |USA, södra centrala |
| Nordamerika |USA, västra 2 |USA, västra centrala 
| Sydafrika | Sydafrika, norra | Sydafrika, västra
| Storbritannien |Storbritannien, västra |Storbritannien, södra |
| Förenade Arabemiraten | Förenade Arabemiraten, norra | Förenade Arabemiraten, centrala
| OSS-försvars departement |USA DoD, östra |USA DoD, centrala |
| Amerikanska myndigheter |US Gov, Arizona |US Gov, Texas |
| Amerikanska myndigheter |USA Gov, Iowa |US Gov, Virginia |
| Amerikanska myndigheter |US Gov, Virginia |US Gov, Texas |

Tabell 1 – mappning av regionala Azure-par

- Västra Indien är bara kopplad till en riktning. Den sekundära regionen västra Indien är södra Indien, men den sekundära regionen i södra Indien är central Indien.
- Södra Brasilien är unikt eftersom det är länkat till en region utanför sin egen geografi. Södra Brasilien: s sekundära region är södra centrala USA. Södra centrala USA: s sekundära region är inte Brasilien, södra.
- US Gov, Iowaens sekundära region är US Gov, Virginia.
- US Gov, Virginiaens sekundära region är US Gov, Texas.
- US Gov, Texas sekundär region är US Gov, Arizona.


Vi rekommenderar att du konfigurerar haveri beredskap för affärs kontinuitet (BCDR) i regionala par för att dra nytta av Azures isolerings-och tillgänglighets principer. För program som har stöd för flera aktiva regioner rekommenderar vi att du använder båda regionerna i ett regions par där det är möjligt. Detta säkerställer optimal tillgänglighet för program och minimerad återställnings tid i händelse av en katastrof. 

## <a name="an-example-of-paired-regions"></a>Ett exempel på kopplade regioner
Bild 2 nedan visar ett hypotetiskt program som använder det regionala paret för haveri beredskap. De gröna siffrorna markerar de olika aktiviteterna i de tre Azure-tjänsterna (Azure Compute, Storage och Database) och hur de är konfigurerade att replikeras mellan regioner. De unika fördelarna med att distribuera över kopplade områden är markerade med de orange numren.

![Översikt över fördelar med kopplade regioner](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Bild 2 – hypotetiskt, regionalt par för Azure

## <a name="cross-region-activities"></a>Aktiviteter över flera regioner
Som det hänvisas till i bild 2.

![IaaS](./media/best-practices-availability-paired-regions/1Green.png) **Azure Compute (IaaS)** – du måste etablera ytterligare beräknings resurser i förväg för att säkerställa att resurserna är tillgängliga i en annan region under en katastrof. Mer information finns i [teknisk vägledning för Azure-återhämtning](https://github.com/uglide/azure-content/blob/master/articles/resiliency/resiliency-technical-guidance.md).

![lagrings](./media/best-practices-availability-paired-regions/2Green.png) **Azure Storage** – om du använder hanterade diskar kan du läsa mer om [säkerhets kopiering över flera regioner](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines) med Azure Backup och [Replikera virtuella datorer](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication) från en region till en annan med Azure Site Recovery. Om du använder lagrings konton konfigureras Geo-redundant lagring (GRS) som standard när ett Azure Storage-konto skapas. Med GRS replikeras dina data automatiskt tre gånger inom den primära regionen och tre gånger i den kopplade regionen. Mer information finns i [Azure Storage alternativ för redundans](storage/common/storage-redundancy.md).

![Azure SQL](./media/best-practices-availability-paired-regions/3Green.png) **Azure SQL Database** – med Azure SQL Database geo-replikering kan du konfigurera asynkron replikering av transaktioner till vilken region som helst i världen. Vi rekommenderar dock att du distribuerar dessa resurser i en kopplad region för de flesta katastrof återställnings scenarier. Mer information finns i [geo-replikering i Azure SQL Database](sql-database/sql-database-geo-replication-overview.md).

![Resource Manager](./media/best-practices-availability-paired-regions/4Green.png) **Azure Resource Manager** – Resource Manager tillhandahåller en logisk isolering av komponenter i flera regioner. Det innebär att logiska försök i en region är mindre sannolika att påverka ett annat.

## <a name="benefits-of-paired-regions"></a>Fördelar med kopplade regioner
Som det hänvisas till i bild 2.  

![isolering](./media/best-practices-availability-paired-regions/5Orange.png)
**fysisk isolering** – när det är möjligt föredrar Azure minst 300 miles av separering mellan data Center i ett regionalt par, även om detta inte är praktiskt eller möjligt i alla geografiska områden. Separation av fysiskt Data Center minskar sannolikheten för natur katastrofer, civila rester, strömavbrott eller fysiska nätverks avbrott som påverkar båda regionerna samtidigt. Isoleringen omfattas av begränsningarna inom geografin (geografisk storlek, tillgänglighet för ström-/nätverks infrastruktur, regler osv.).  

![replikering](./media/best-practices-availability-paired-regions/6Orange.png)
**plattforms oberoende replikering** – vissa tjänster som Geo-redundant lagring ger automatisk replikering till den kopplade regionen.

**återställnings ordning** för ![återställnings](./media/best-practices-availability-paired-regions/7Orange.png)
– i händelse av ett stort avbrott prioriteras återställning av en region av varje par. Program som distribueras över kopplade regioner garanterar att en av regionerna återställs med prioritet. Om ett program distribueras mellan regioner som inte paras ihop, kan återställningen bli fördröjd – i värsta fall kan de valda regionerna vara de två sista som ska återställas.

![uppdateringar](./media/best-practices-availability-paired-regions/8Orange.png)
**sekventiella uppdateringar** – planerade Azure-systemuppdateringar distribueras till kopplade regioner sekventiellt (inte på samma gång) för att minimera drift stopp, påverkan av buggar och logiska fel i sällsynta fall av en felaktig uppdatering.

![data](./media/best-practices-availability-paired-regions/9Orange.png)
**data placering** – en region finns inom samma geografi som dess par (med undantag för Brasilien, södra) för att uppfylla kraven på data placering för skatte-och rättsvårds myndigheter.
