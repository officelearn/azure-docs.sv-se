---
title: Resurs gränser-hanterad instans
description: Den här artikeln innehåller en översikt över Azure SQL Database resurs gränser för hanterade instanser.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
ms.date: 11/27/2019
ms.openlocfilehash: 816cf7cc78d3dfcb783b09f039f468ef3b23a06b
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2019
ms.locfileid: "74548372"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Översikt Azure SQL Database hanterade instans resurs gränser

Den här artikeln innehåller en översikt över de tekniska egenskaperna och resurs gränserna för Azure SQL Database Hanterad instans och ger information om hur du begär en ökning av dessa gränser.

> [!NOTE]
> Skillnader i funktioner som stöds och T-SQL-uttryck finns i [funktions skillnader](sql-database-features.md) och [stöd för t-SQL-uttryck](sql-database-managed-instance-transact-sql-information.md). Allmän skillnad mellan tjänst nivåerna i en enkel databas och en hanterad instans finns i [jämförelse av tjänst nivå](sql-database-service-tiers-general-purpose-business-critical.md#service-tier-comparison).

## <a name="hardware-generation-characteristics"></a>Egenskaper för maskin varu skapande

Den hanterade instansen har egenskaper och resurs gränser som är beroende av den underliggande infrastrukturen och arkitekturen. Azure SQL Database Hanterad instans kan distribueras på två maskin varu generationer: Gen4 och Gen5. Maskin varu generationer har olika egenskaper, enligt beskrivningen i följande tabell:

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| Maskinvara | Intel E5-2673 v3 (Haswell) 2,4 GHz-processorer, anslutna SSD vCore = 1 PP (fysisk kärna) | Intel E5-2673 v4 (Broadwell) 2,3-GHz-och Intel SP-8160-processorer (Skylake), fast NVMe SSD, vCore = 1 LP (Hyper-Thread) |
| Antal v-kärnor | 8, 16, 24 virtuella kärnor | 4, 8, 16, 24, 32, 40, 64, 80 virtuella kärnor |
| Högsta mängd minne (minne/Core-förhållande) | 7 GB per vCore<br/>Lägg till fler virtuella kärnor för att få mer minne. | 5,1 GB per vCore<br/>Lägg till fler virtuella kärnor för att få mer minne. |
| Högsta minnes intern OLTP-minne | Instans gräns: 1 – 1,5 GB per vCore| Instans gräns: 0,8 – 1,65 GB per vCore |
| Högsta reserverade instans lagring |  Generell användning: 8 TB<br/>Affärskritisk: 1 TB | Generell användning: 8 TB<br/> Affärskritisk 1 TB, 2 TB eller 4 TB beroende på antalet kärnor |

> [!IMPORTANT]
> - Gen4-maskinvaran har fasats ut. Vi rekommenderar att du distribuerar nya hanterade instanser på Gen5-maskinvara.
> - Gen4-maskinvara är för närvarande endast tillgängligt i följande regioner: Nord Europa, Västeuropa, östra USA, södra centrala USA, norra centrala USA, västra USA 2, centrala USA, centrala Kanada, södra Indien, Sydostasien och Korea, centrala.

### <a name="in-memory-oltp-available-space"></a>Tillgängligt utrymme i minnet för OLTP 

Mängden minnes intern OLTP-rymd i [affärskritisk](sql-database-service-tier-business-critical.md) tjänst nivå beror på antalet virtuella kärnor och maskin varu generering. I följande tabell visas gränser för minne som kan användas för InMemory OLTP-objekt.

| Minnes intern OLTP-rymd  | **Gen5** | **Gen4** |
| --- | --- | --- |
| 4 virtuella kärnor  | 3,14 GB | |   
| 8 virtuella kärnor  | 6,28 GB | 8 GB |
| 16 virtuella kärnor | 15,77 GB | 20 GB |
| 24 virtuella kärnor | 25,25 GB | 36 GB |
| 32 virtuella kärnor | 37,94 GB | |
| 40 virtuella kärnor | 52,23 GB | |
| 64 virtuella kärnor | 99,9 GB    | |
| 80 virtuella kärnor | 131,68 GB| |

## <a name="service-tier-characteristics"></a>Egenskaper för tjänst nivå

Den hanterade instansen har två tjänst nivåer: [generell användning](sql-database-service-tier-general-purpose.md) och [affärskritisk](sql-database-service-tier-business-critical.md). Dessa nivåer ger [olika funktioner](sql-database-service-tiers-general-purpose-business-critical.md), enligt beskrivningen i tabellen nedan.

> [!Important]
> Affärskritisk Service Tier tillhandahåller ytterligare inbyggd kopia av instans (sekundär replik) som kan användas för skrivskyddade arbets belastningar. Om du kan separera Skriv-och analys-/rapporterings frågor får du två gånger virtuella kärnor och minne för samma pris. Sekundär replik kan vänta några sekunder bakom den primära instansen, så den är utformad för att avlasta rapportering/analytiska arbets belastningar som inte behöver exakt det aktuella data läget. I tabellen nedan är **skrivskyddade frågor** de frågor som körs på den sekundära repliken.

| **Funktion** | **Generell användning** | **Affärskritisk** |
| --- | --- | --- |
| Antal virtuella kärnor-\* | Gen4:8, 16, 24<br/>Gen5:4, 8, 16, 24, 32, 40, 64, 80 | Gen4:8, 16, 24 <br/> Gen5:4, 8, 16, 24, 32, 40, 64, 80 <br/>\*samma antal virtuella kärnor är dedicerat för skrivskyddade frågor. |
| Högsta mängd minne | Gen4:56 GB-168 GB (7GB/vCore)<br/>Gen5:20,4 GB-408 GB (5,1 GB/vCore)<br/>Lägg till fler virtuella kärnor för att få mer minne. | Gen4:56 GB-168 GB (7GB/vCore)<br/>Gen5:20,4 GB-408 GB (5,1 GB/vCore) för Läs-och skriv frågor<br/>+ ytterligare 20,4 GB-408 GB (5,1 GB/vCore) för skrivskyddade frågor.<br/>Lägg till fler virtuella kärnor för att få mer minne. |
| Maximal instans lagrings storlek (reserverad) | -2 TB för 4 virtuella kärnor (endast Gen5)<br/>– 8 TB för andra storlekar | Gen4:1 TB <br/> Gen5 <br/>-1 TB för 4, 8, 16 virtuella kärnor<br/>– 2 TB för 24 virtuella kärnor<br/>– 4 TB för 32, 40, 64, 80 virtuella kärnor |
| Maximal databasstorlek | Upp till tillgänglig instans storlek (max 2 TB-8 TB beroende på antalet virtuella kärnor). | Upp till tillgänglig instans storlek (max 1 TB – 4 TB beroende på antalet virtuella kärnor). |
| Maximal tempDB-storlek | Begränsad till 24 GB/vCore (96-1 920 GB) och för tillfället tillgänglig instans lagrings storlek.<br/>Lägg till fler virtuella kärnor för att få mer TempDB-utrymme.<br/> Logg filens storlek är begränsad till 120 GB.| Upp till tillgänglig instans lagrings storlek för närvarande. |
| Maximalt antal databaser per instans | 100, om inte storleks gränsen för instans lagring har uppnåtts. | 100, om inte storleks gränsen för instans lagring har uppnåtts. |
| Maximalt antal databasfiler per instans | Upp till 280, om inte instansens lagrings storlek eller [Azure Premium disk](sql-database-managed-instance-transact-sql-information.md#exceeding-storage-space-with-small-database-files) utrymmes gräns har nåtts. | 32 767 filer per databas, om inte storleks gränsen för instans lagring har uppnåtts. |
| Maximal data fil storlek | Begränsad till tillgänglig instans lagrings storlek (högst 2 TB-8 TB) och [lagrings utrymme för Azure Premium-disk](sql-database-managed-instance-transact-sql-information.md#exceeding-storage-space-with-small-database-files). | Begränsad till tillfället tillgänglig instans lagrings storlek (upp till 1 TB-4 TB). |
| Största logg fils storlek | Begränsad till 2 TB och tillgänglig instans lagrings storlek för närvarande. | Begränsad till 2 TB och tillgänglig instans lagrings storlek för närvarande. |
| Data/logga IOPS (ungefärligt) | Upp till 30-40 kB IOPS per instans *, 500-7500 per fil<br/>\*[öka fil storleken för att få mer IOPS](#file-io-characteristics-in-general-purpose-tier)| 5,5 k-110 K (1375 IOPS/vCore)<br/>Lägg till fler virtuella kärnor för att få bättre IO-prestanda. |
| Logg Skriv data flödes gräns (per instans) | 3 MB/s per vCore<br/>Högst 22 MB/s | 4 MB/s per vCore<br/>Max 48 MB/s |
| Data flöde (ungefärligt) | 100-250 MB/s per fil<br/>\*[öka fil storleken för att få bättre IO-prestanda](#file-io-characteristics-in-general-purpose-tier) | Inte begränsat. |
| IO-latens för lagring (ungefärligt) | 5-10 MS | 1-2 MS |
| Minnesintern OLTP | Stöds inte | Tillgängligt, [storleken beror på antalet vCore](#in-memory-oltp-available-space) |
| Maximalt antal sessioner | 30000 | 30000 |
| [Skrivskyddade repliker](sql-database-read-scale-out.md) | 0 | 1 (ingår i priset) |

> [!NOTE]
> - **För närvarande är tillgänglig instans lagrings storlek** skillnaden mellan reserverad instans och använt lagrings utrymme.
> - Både data-och logg fils storleken i användar-och system databaserna ingår i instans lagrings storleken som jämförs med den maximala lagrings storleks gränsen. Använd <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys. master_files</a> system-vyn för att fastställa det totala använda utrymmet per databas. Fel loggarna är inte bestående och ingår inte i storlek. Säkerhets kopieringar ingår inte i lagrings storleken.
> - Data flödet och IOPS på Generell användning nivå beror också på den [fil storlek](#file-io-characteristics-in-general-purpose-tier) som inte uttryckligen begränsas av den hanterade instansen.
> - Du kan skapa en annan läsbar replik i olika Azure-regioner med hjälp av grupper för automatisk redundans.
> - Högsta instans-IOPS beror på fillayouten och distributionen av arbets belastningen. Exempel: om du skapar 7 x 1 GB filer med högst 5 K IOPS varje och 7 små filer (mindre än 128 GB) med 500 IOPS var, kan du hämta 38500 IOPS per instans (7x5000 + 7x500) om din arbets belastning kan använda alla filer. Observera att viss mängd IOPS också används för automatisk säkerhets kopiering.

> [!NOTE]
> Mer information om [resurs gränserna i hanterade instanser av pooler finns i den här artikeln](sql-database-instance-pools.md#instance-pools-resource-limitations).

### <a name="file-io-characteristics-in-general-purpose-tier"></a>Egenskaper för fil-i/o i Generell användning nivån

I Generell användning tjänst nivå varje databas fil får dedikerad IOPS och data flöde som är beroende av fil storleken. Större filer får mer IOPS och data flöde. I/o-egenskaperna för databasfilerna visas i följande tabell:

| Filstorlek           | 0-128 GiB | 128 – 256 GiB | 256 – 512 GiB | 0,5 – 1 TiB    | 1-2 TiB    | 2-4 TiB | 4-8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| IOPS per fil       | 500   | 1100 | 2 300              | 5000              | 7500              | 7500              | 12 500   |
| Data flöde per fil | 100 MiB/s | 125 MiB/s | 150 MiB/s | 200 MiB/s | 250 MiB/s | 250 MiB/s | 480 MiB/s | 

Om du märker en lång IO-fördröjning på en viss databas fil eller om du ser att IOPS/data flödet når gränsen kan du förbättra prestandan genom [att öka fil storleken](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337).

Det finns även gränser på instans nivå som maximalt logg skrivnings data flöde 22 MB/s, så du kanske inte kan nå filen i logg filen på grund av att du når instans data flödes gränsen.

## <a name="supported-regions"></a>Regioner som stöds

Hanterade instanser kan bara skapas i [regioner som stöds](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Om du vill skapa en hanterad instans i en region som för närvarande inte stöds kan du [skicka en supportbegäran via Azure Portal](#obtaining-a-larger-quota-for-sql-managed-instance).

## <a name="supported-subscription-types"></a>Prenumerationstyper som stöds

Den hanterade instansen stöder för närvarande endast distribution av följande typer av prenumerationer:

- [Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Cloud Service Provider (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Dev/Test – betala per användning](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Prenumerationer med månatlig Azure-kredit för Visual Studio-prenumeranter](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Begränsningar för regionala resurser

Prenumerations typer som stöds kan innehålla ett begränsat antal resurser per region. Den hanterade instansen har två standard gränser per Azure-region (som kan ökas på begäran genom att en särskild [supportbegäran skapas i Azure Portal](#obtaining-a-larger-quota-for-sql-managed-instance)) beroende på typ av prenumerations typ:

- **Under näts gräns**: det maximala antalet undernät där hanterade instanser distribueras i en enda region.
- **vCore-enhets gräns**: det maximala antalet vCore-enheter som kan distribueras över alla instanser i en enda region. En GP-vCore använder en vCore-enhet och en BC-vCore tar 4 vCore-enheter. Det totala antalet instanser är inte begränsat så länge det ligger inom gränsen för vCore-enheten.

> [!Note]
> Dessa gränser är standardinställningar och inte tekniska begränsningar. Gränserna kan ökas på begäran genom att en särskild [supportbegäran skapas i Azure Portal](#obtaining-a-larger-quota-for-sql-managed-instance) om du behöver fler hanterade instanser i den aktuella regionen. Alternativt kan du skapa nya hanterade instanser i en annan Azure-region utan att skicka support förfrågningar.

Följande tabell visar de **regionala standard gränserna** för prenumerations typer som stöds (standard gränser kan utökas med hjälp av en support förfrågan som beskrivs nedan):

|Prenumerationstyp| Maximalt antal hanterade instans under nät | Max antal vCore-enheter * |
| :---| :--- | :--- |
|Användningsbaserad betalning|3|320|
|CSP |8 (15 i vissa regioner * *)|960 (1440 i vissa regioner * *)|
|Betala per användning – utveckling/testning|3|320|
|Enterprise Dev/Test|3|320|
|EA|8 (15 i vissa regioner * *)|960 (1440 i vissa regioner * *)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional och MSDN-plattformar|2|32|

\* vid planering av distributioner bör du tänka på att Affärskritisk (BC) tjänst nivån kräver fyra (4) gånger mer vCore-kapacitet än Generell användning (GP). Exempel: 1 GP-vCore = 1 vCore-enhet och 1 BC-vCore = 4 vCore-enheter. För att förenkla förbruknings analysen mot standard gränserna ska du sammanfatta vCore-enheterna över alla undernät i den region där hanterade instanser distribueras och jämföra resultaten med instans enhets gränserna för din prenumerations typ. Gränsen **för antalet vCore-enheter** gäller för varje prenumeration i en region. Det finns ingen gräns per enskilda undernät förutom att summan av alla virtuella kärnor som distribueras över flera undernät måste vara lägre eller lika med **Max antalet vCore-enheter**.

\*\* större undernät och vCore-gränser är tillgängliga i följande regioner: östra Australien, östra USA, östra USA 2, norra Europa, södra centrala USA, Sydostasien, Storbritannien, södra, Västeuropa, västra USA 2.

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>Hämta en större kvot för SQL-hanterad instans

Om du behöver fler hanterade instanser i dina aktuella regioner skickar du en supportbegäran för att utöka kvoten med hjälp av Azure Portal.
Initiera processen för att erhålla en större kvot:

1. Öppna **Hjälp + Support**och klicka på **nytt support ärende**.

   ![Hjälp och support](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. På fliken grundläggande för den nya support förfrågan:
   - För **typ av problem**väljer du **begränsningar för tjänsten och prenumerationen (kvoter)** .
   - I fältet **Prenumeration** väljer du din prenumeration.
   - För **typ av kvot**väljer du **SQL Database Hanterad instans**.
   - Välj ditt support avtal för **support avtalet**.

     ![Typ kvot för utfärdande](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. Klicka på **Nästa**.
4. På **fliken problem** för den nya support förfrågan:
   - För **allvarlighets grad**väljer du problemets allvarlighets grad.
   - Om **du vill ha mer information anger**du ytterligare information om problemet, inklusive fel meddelanden.
   - Bifoga en fil med mer information (upp till 4 MB) för **fil uppladdning**.

     ![Probleminformation](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > En giltig begäran bör innehålla:
     > - Region där prenumerations gränsen behöver ökas.
     > - Antalet virtuella kärnor per tjänst nivå som krävs i befintliga undernät efter kvot ökningen (om något av de befintliga under näten måste expanderas.
     > - Antalet nya undernät och det totala antalet virtuella kärnor per tjänst nivå inom de nya under näten (om du behöver distribuera hanterade instanser i nya undernät).

5. Klicka på **Nästa**.
6. På fliken kontakt information för den nya support förfrågan anger du önskad kontakt metod (e-post eller telefon) och kontakt uppgifter.
7. Klicka på **Skapa**.

## <a name="next-steps"></a>Nästa steg

- Mer information om en hanterad instans finns i [Vad är en hanterad instans?](sql-database-managed-instance.md).
- Pris information finns i [priser för SQL Database Managed instance](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Information om hur du skapar din första hanterade instans finns i [snabb starts guiden](sql-database-managed-instance-get-started.md).
