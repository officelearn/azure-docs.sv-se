---
title: Resursgränser - hanterad instans
description: Den här artikeln innehåller en översikt över azure SQL Database-resursgränserna för hanterade instanser.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
ms.date: 02/25/2020
ms.openlocfilehash: b2871ec87e4d7f337c26b3ff3de83c1c3c88aea2
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365382"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Översikt Azure SQL Database-resursbegränsningar för hanterade instanser

Den här artikeln innehåller en översikt över de tekniska egenskaperna och resursgränserna för Azure SQL Database-hanterad instans och innehåller information om hur du begär en ökning av dessa gränser.

> [!NOTE]
> För skillnader i funktioner som stöds och T-SQL-uttryck finns [funktionsskillnader](sql-database-features.md) och [stöd för T-SQL-uttryck](sql-database-managed-instance-transact-sql-information.md). Allmänna skillnader mellan tjänstnivåer i en enda databas och hanterad instans finns [i Jämförelse på tjänstnivå](sql-database-service-tiers-general-purpose-business-critical.md#service-tier-comparison).

## <a name="hardware-generation-characteristics"></a>Egenskaper för maskinvarugenerering

Hanterad instans har egenskaper och resursgränser som är beroende av den underliggande infrastrukturen och arkitekturen. Azure SQL Database-hanterad instans kan distribueras på två maskinvarugenerationer: Gen4 och Gen5. Maskinvarugenerationer har olika egenskaper, som beskrivs i följande tabell:

|   | **Gen4 (På andra)** | **Gen5 (På andra)** |
| --- | --- | --- |
| Maskinvara | Intel E5-2673 v3 (Haswell) 2,4 GHz-processorer, anslutna SSD vCore = 1 PP (fysisk kärna) | Intel E5-2673 v4 (Broadwell) 2,3 GHz och Intel SP-8160 (Skylake) processorer, snabb NVMe SSD, vCore = 1 LP (hyper-tråd) |
| Antal v-kärnor | 8, 16, 24 virtuella kärnor | 4, 8, 16, 24, 32, 40, 64, 80 virtuella kärnor |
| Max minne (minnes-/kärnförhållande) | 7 GB per vCore<br/>Lägg till fler virtuella kärnor för att få mer minne. | 5,1 GB per vCore<br/>Lägg till fler virtuella kärnor för att få mer minne. |
| Maximalt OLTP-minne i minnet | Instansgräns: 1–1,5 GB per vCore| Instansgräns: 0,8 - 1,65 GB per vCore |
| Maximalt instans reserverat lagringsutrymme |  Allmänt syfte: 8 TB<br/>Affärskritisk: 1 TB | Allmänt syfte: 8 TB<br/> Affärskritisk 1 TB, 2 TB eller 4 TB beroende på antalet kärnor |

> [!IMPORTANT]
> - Gen4-maskinvaran håller på att fasas ut och är inte längre tillgänglig för de nya distributionerna. Alla nya hanterade instanser måste distribueras på Gen5-maskinvara.
> - Överväg [att flytta dina hanterade instanser till Gen 5-maskinvara](sql-database-service-tiers-vcore.md) för att uppleva ett bredare utbud av virtuella kärnor och lagringskalbarhet, snabbare nätverk, bästa IO-prestanda och minimal latens.

### <a name="in-memory-oltp-available-space"></a>Ledigt OLTP-ledigt utrymme i minnet 

Mängden OLTP-utrymme i in-memory i [business critical-tjänst](sql-database-service-tier-business-critical.md) beror på antalet virtuella kärnor och maskinvarugenerering. I följande tabell visas minnesgränser som kan användas för OLTP-objekt i minnet.

| OLTP-utrymme i minnet  | **Gen5 (På andra)** | **Gen4 (På andra)** |
| --- | --- | --- |
| 4 virtuella kärnor  | 3,14 GB | |   
| 8 virtuella kärnor  | 6,28 GB | 8 GB |
| 16 virtuella kärnor | 15,77 GB | 20 GB |
| 24 virtuella kärnor | 25,25 GB | 36 GB |
| 32 virtuella kärnor | 37,94 GB | |
| 40 virtuella kärnor | 52,23 GB | |
| 64 virtuella kärnor | 99,9 GB    | |
| 80 virtuella kärnor | 131,68 GB| |

## <a name="service-tier-characteristics"></a>Egenskaper på tjänstnivå

Hanterad instans har två tjänstnivåer: [Allmänt syfte](sql-database-service-tier-general-purpose.md) och [Affärskritisk](sql-database-service-tier-business-critical.md). Dessa nivåer ger [olika funktioner,](sql-database-service-tiers-general-purpose-business-critical.md)enligt beskrivningen i tabellen nedan.

> [!Important]
> Business Critical service-nivå ger ytterligare inbyggd kopia av instans (sekundär replik) som kan användas för skrivskyddad arbetsbelastning. Om du kan separera läs-skrivfrågor och skrivskyddade/analytiska/rapporteringsfrågor får du två gånger virtuella kärnor och minne för samma pris. Sekundär replik kan ligga några sekunder efter den primära instansen, så den är utformad för att avlasta rapportering/analytisk arbetsbelastning som inte behöver exakta aktuella datatillstånd. I tabellen nedan är **skrivskyddade frågor** de frågor som körs på sekundär replik.

| **Funktion** | **Allmänt syfte** | **Affärskritiskt** |
| --- | --- | --- |
| Antal v-kärnor\* | Gen4: 8, 16, 24<br/>Gen5: 4, 8, 16, 24, 32, 40, 64, 80 | Gen4: 8, 16, 24 <br/> Gen5: 4, 8, 16, 24, 32, 40, 64, 80 <br/>\*Samma antal virtuella kärnor är dedikerade till skrivskyddade frågor. |
| Maximalt minne | Gen4: 56 GB - 168 GB (7 GB/vCore)<br/>Gen5: 20,4 GB - 408 GB (5,1 GB/vCore)<br/>Lägg till fler virtuella kärnor för att få mer minne. | Gen4: 56 GB - 168 GB (7 GB/vCore)<br/>Gen5: 20,4 GB - 408 GB (5,1 GB/vCore) för läs-skriv-frågor<br/>+ ytterligare 20,4 GB - 408 GB (5,1 GB/vCore) för skrivskyddade frågor.<br/>Lägg till fler virtuella kärnor för att få mer minne. |
| Maximal instans lagringsstorlek (reserverad) | - 2 TB för 4 virtuella kärnor (endast Gen5)<br/>- 8 TB för andra storlekar | Gen4: 1 TB <br/> Gen5: <br/>- 1 TB för 4, 8, 16 vCores<br/>- 2 TB för 24 virtuella kärnor<br/>- 4 TB för 32, 40, 64, 80 virtuella kärnor |
| Maximal databasstorlek | Upp till tillgänglig instansstorlek (max 2 TB - 8 TB beroende på antalet virtuella kärnor). | Upp till tillgänglig instansstorlek (max 1 TB - 4 TB beroende på antalet virtuella kärnor). |
| Max tempDB-storlek | Begränsad till 24 GB/vCore (96 – 1 920 GB) och för närvarande tillgänglig instanslagringsstorlek.<br/>Lägg till fler virtuella kärnor för att få mer TempDB-utrymme.<br/> Loggfilsstorleken är begränsad till 120 GB.| Upp till den tillgängliga instanslagringsstorleken. |
| Max antal databaser per instans | 100, om inte instanslagringsstorleksgränsen har uppnåtts. | 100, om inte instanslagringsstorleksgränsen har uppnåtts. |
| Max antal databasfiler per instans | Upp till 280, såvida inte instanslagringsstorleken eller [Azure Premium Disk-lagringsallokeringsutrymmesgränsen](sql-database-release-notes.md#exceeding-storage-space-with-small-database-files) har uppnåtts. | 32 767 filer per databas, såvida inte instanslagringsstorleksgränsen har uppnåtts. |
| Max datafilstorlek | Begränsad till tillgänglig instanslagringsstorlek (max 2 TB – 8 TB) och [Azure Premium Disk-lagringsutrymme](sql-database-release-notes.md#exceeding-storage-space-with-small-database-files). | Begränsad till för närvarande tillgänglig instanslagringsstorlek (upp till 1 TB – 4 TB). |
| Max loggfilsstorlek | Begränsad till 2 TB och för närvarande tillgänglig instanslagringsstorlek. | Begränsad till 2 TB och för närvarande tillgänglig instanslagringsstorlek. |
| Data/Log IOPS (ungefärlig) | Upp till 30-40 K IOPS per instans*, 500 - 7500 per fil<br/>\*[Öka filstorleken för att få mer IOPS](#file-io-characteristics-in-general-purpose-tier)| 10 K - 200 K (2500 IOPS/vCore)<br/>Lägg till fler virtuella kärnor för att få bättre IO-prestanda. |
| Gränsen för loggdataflöde (per instans) | 3 MB/s per vCore<br/>Max 22 MB/s | 4 MB/s per vCore<br/>Max 48 MB/s |
| Datadataflöde (ungefärlig) | 100 - 250 MB/s per fil<br/>\*[Öka filstorleken för att få bättre IO-prestanda](#file-io-characteristics-in-general-purpose-tier) | Inte begränsat. |
| Lagrings-IO-svarstid (ungefärlig) | 5-10 ms | 1-2 ms |
| OLTP i minnet | Stöds inte | Tillgänglig, [storlek beror på antalet vCore](#in-memory-oltp-available-space) |
| Max sessioner | 30000 | 30000 |
| [Skrivskyddade repliker](sql-database-read-scale-out.md) | 0 | 1 (ingår i pris) |

> [!NOTE]
> - **För närvarande tillgänglig instans lagringsstorlek** är skillnaden mellan reserverad instansstorlek och använt lagringsutrymme.
> - Både data- och loggfilsstorlek i användar- och systemdatabaserna ingår i instanslagringsstorleken som jämförs med max-lagringsstorleksgränsen. Använd <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">systemvyn sys.master_files</a> för att fastställa det totala använda utrymmet av databaser. Felloggar sparas inte och ingår inte i storleken. Säkerhetskopior ingår inte i lagringsstorleken.
> - Dataflöde och IOPS på allmän nivå beror också på [filstorleken](#file-io-characteristics-in-general-purpose-tier) som inte uttryckligen begränsas av hanterad instans.
> - Du kan skapa en annan läsbar replik i olika Azure-regioner med hjälp av grupper för automatisk redundans.
> - Max instans IOPS beror på fillayout och delning av arbetsbelastning. Om du till exempel skapar 7 x 1 TB-filer med max 5K IOPS vardera och 7 små filer (mindre än 128 GB) med 500 IOPS vardera, kan du få 38500 IOPS per instans (7x5000+7x500) om din arbetsbelastning kan använda alla filer. Observera att en viss mängd IOPS också används för automatiska säkerhetskopior.

> [!NOTE]
> Mer information om [resursgränserna i hanterade instanspooler finns i den här artikeln](sql-database-instance-pools.md#instance-pools-resource-limitations).

### <a name="file-io-characteristics-in-general-purpose-tier"></a>Fil-IO-egenskaper på nivån Allmänt ändamål

I General Purpose-tjänstnivån får varje databasfil dedikerade IOPS och dataflöde som beror på filstorleken. Större filer får mer IOPS och dataflöde. IO-egenskaperna hos databasfilerna visas i följande tabell:

| Filstorlek | >=0 och <=128 GiB | >128 och <=256 GiB | >256 och <= 512 GiB | >0,5 och <=1 TiB    | >1 och <=2 TiB    | >2 och <=4 TiB | >4 och <=8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| IOPS per fil       | 500   | 1100 | 2 300              | 5000              | 7 500              | 7 500              | 12 500   |
| Dataflöde per fil | 100 MiB/s | 125 MiB/s | 150 MiB/s | 200 MiB/s | 250 MiB/s | 250 MiB/s | 480 MiB/s | 

Om du märker hög IO-latens i någon databasfil eller om du ser att IOPS/dataflöde når gränsen kan du förbättra prestanda genom [att öka filstorleken](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337).

Det finns också gränser på instansnivå som max loggskrivningsdataflöde 22 MB/s, så du kanske inte kan nå filen i loggfilen eftersom du når instansdataflödesgränsen.

## <a name="supported-regions"></a>Regioner som stöds

Hanterade instanser kan bara skapas i [regioner som stöds](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Om du vill skapa en hanterad instans i en region som för närvarande inte stöds kan du [skicka en supportbegäran via Azure-portalen](quota-increase-request.md).

## <a name="supported-subscription-types"></a>Prenumerationstyper som stöds

Hanterad instans stöder för närvarande distribution endast på följande typer av prenumerationer:

- [Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Pay-as-you-go](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Molntjänstleverantör (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Dev/Test – betala per användning](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Prenumerationer med månatlig Azure-kredit för Visual Studio-prenumeranter](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Regionala resursbegränsningar

Prenumerationstyper som stöds kan innehålla ett begränsat antal resurser per region. Hanterad instans har två standardgränser per Azure-region (som kan ökas på begäran genom att skapa en särskild [supportbegäran i Azure-portalen](quota-increase-request.md) beroende på vilken typ av prenumerationstyp:

- **Undernätsgräns**: Det maximala antalet undernät där hanterade instanser distribueras i en enda region.
- **vCore-enhetsgräns:** Det maximala antalet vCore-enheter som kan distribueras över alla instanser i en enda region. En GP vCore använder en vCore-enhet och en BC vCore tar 4 vCore-enheter. Det totala antalet instanser är inte begränsat så länge det ligger inom vCore-enhetsgränsen.

> [!Note]
> Dessa gränser är standardinställningar och inte tekniska begränsningar. Gränserna kan ökas på begäran genom att skapa en särskild [supportbegäran i Azure-portalen](quota-increase-request.md) om du behöver fler hanterade instanser i den aktuella regionen. Som ett alternativ kan du skapa nya hanterade instanser i en annan Azure-region utan att skicka supportbegäranden.

I följande tabell visas **standardregionala gränser** för prenumerationstyper som stöds (standardgränser kan utökas med supportbegäran som beskrivs nedan):

|Prenumerationstyp| Maximalt antal hanterade instansundernät | Max antal vCore-enheter* |
| :---| :--- | :--- |
|Pay-as-you-go|3|320|
|CSP |8 (15 i vissa regioner**)|960 (1440 i vissa regioner**)|
|Betala per du kör Dev/Test|3|320|
|Enterprise Dev/Test|3|320|
|Ea|8 (15 i vissa regioner**)|960 (1440 i vissa regioner**)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional och MSDN-plattformar|2|32|

\*Vid planering av distributioner bör du ta hänsyn till att tjänstnivån Business Critical (BC) kräver fyra (4) gånger mer vCore-kapacitet än gp-tjänstnivån (General Purpose). Till exempel: 1 GP vCore = 1 vCore enhet och 1 BC vCore = 4 vCore enheter. Om du vill förenkla förbrukningsanalysen mot standardgränserna sammanfattar du vCore-enheterna i alla undernät i regionen där hanterade instanser distribueras och jämför resultaten med instansenhetsgränserna för din prenumerationstyp. **Max antal vCore-enheter** gäller för varje prenumeration i en region. Det finns ingen gräns per enskild undernät förutom att summan av alla virtuella kärnor som distribueras över flera undernät måste vara lägre eller lika med **max antal vCore-enheter**.

\*\*Större undernäts- och vCore-gränser är tillgängliga i följande regioner: Östra Australien, Östra USA, Östra USA 2, Norra Europa, Södra centrala USA, Sydostasien, Storbritannien i södra, västra Europa, västra USA 2.

## <a name="request-a-quota-increase-for-sql-managed-instance"></a>Begär en kvotökning för SQL-hanterad instans

Om du behöver fler hanterade instanser i dina aktuella regioner skickar du en supportbegäran för att utöka kvoten med Azure-portalen. Mer information finns i [Begär kvotökningar för Azure SQL Database](quota-increase-request.md).

## <a name="next-steps"></a>Nästa steg

- Mer information om hanterad instans finns i [Vad är en hanterad instans?](sql-database-managed-instance.md).
- Prisinformation finns i [SQL Database-priser för hanterade instanser](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Mer information om hur du skapar den första hanterade instansen finns [i snabbstartsguiden](sql-database-managed-instance-get-started.md).
