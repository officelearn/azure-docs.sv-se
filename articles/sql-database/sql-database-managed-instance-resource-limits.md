---
title: Resursgränser för Azure SQL Database - hanterad instans | Microsoft Docs
description: Den här artikeln innehåller en översikt över Azure SQL Database-resursgränser för hanterade instanser.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
manager: craigg
ms.date: 06/26/2019
ms.openlocfilehash: a0846a7d03cc2f63af6747c8b8514b563c1d4a5d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447807"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Översikt över Azure SQL Database managed instance resursbegränsningar

Den här artikeln innehåller en översikt över resursbegränsningar för Azure SQL Database-hanterad instans och innehåller information om hur du begär en ökning av dessa begränsningar.

> [!NOTE]
> Skillnaderna i funktioner som stöds och T-SQL-instruktioner finns i [funktionsskillnader](sql-database-features.md) och [T-SQL-instruktionen support](sql-database-managed-instance-transact-sql-information.md).

## <a name="instance-level-resource-limits"></a>På instansnivå resursbegränsningar

Hanterad instans har egenskaper och resursbegränsningar som beror på den underliggande infrastrukturen och arkitektur. Gränserna beror på maskinvara generation och service-nivån.

### <a name="hardware-generation-characteristics"></a>Generation maskinvaruegenskaper

Azure SQL Database-hanterad instans kan distribueras på två maskinvarugenerationer: Gen4 och Gen5. Maskinvarugenerationer har olika egenskaper, enligt beskrivningen i följande tabell:

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| Maskinvara | Intel E5-2673 v3 (Haswell) 2,4 GHz-processorer, anslutna SSD vCore = 1 PP (fysiska kärnor) | Intel E5-2673 v4 (Broadwell) 2.3-GHz-processorer, snabb NVMe SSD, vCore = 1 LP (hyper-tråd) |
| vCores | 8, 16, 24 virtuella kärnor | 4, 8, 16, 24, 32, 40, 64, 80 vCores |
| Minne (minne/kärna-kvot) | 7 GB per vCore | 5.1 GB per vCore |
| Maximal InMemory-OLTP-minne | Gräns för instans: 3 GB per vCore<br/>Databasgränser:<br/> – 8 kärnor: 8 GB per databas<br/> – 16 kärnor: 20 GB per databas<br/> -24 kärnor: 36 GB per databas | Gräns för instans: 2,5 GB per vCore<br/>Databasgränser:<br/> – 8 kärnor: 13 GB per databas<br/> – 16 kärnor: 32 GB per databas |
| Maximal instans lagring (generell användning) |  8 TB | 8 TB |
| Maximal instans lagring (affärskritisk) | 1 TB | 1 TB, 2 TB och 4 TB beroende på antalet kärnor |

> [!IMPORTANT]
> Nya Gen4 databaser stöds inte längre i regionen Australien.

### <a name="service-tier-characteristics"></a>Tjänstens nivån egenskaper

Hanterad instans har två tjänstnivåer: Generell användning och affärskritisk. Dessa nivåer ger olika funktioner som beskrivs i tabellen nedan:

| **Funktion** | **Generell användning** | **Affärskritisk** |
| --- | --- | --- |
| Antal virtuella kärnor\* | Gen4: 8, 16, 24<br/>Gen5: 4, 8, 16, 24, 32, 40, 64, 80 | Gen4: 8, 16, 24, 32 <br/> Gen5: 4, 8, 16, 24, 32, 40, 64, 80 |
| Minne | Gen4: 56 GB – 168 GB (7GB/vCore)<br/>Gen5: 40.8 GB – 408 GB (5.1 GB/vCore) | Gen4: 56 GB – 168 GB (7GB/vCore)<br/>Gen5: 40.8 GB – 408 GB (5.1 GB/vCore) |
| Maxstorlek för instans-lagring | – 2 TB för 4 virtuella kärnor (endast Gen5)<br/>– 8 TB för andra storlekar | Gen4: 1 TB <br/> Gen5: <br/>-1 TB för 4, 8, 16 virtuella kärnor<br/>– 2 TB för 24 virtuella kärnor<br/>-4 TB för 32, 40, 64, 80 virtuella kärnor |
| Maximalt lagringsutrymme per databas | Bestäms av den maximala lagringsstorleken per instans | Bestäms av den maximala lagringsstorleken per instans |
| Maximalt antal databaser per instans | 100 | 100 |
| Max databasfiler per instans | Upp till 280 | 32 767 filer per databas |
| Data/Log IOPS (ungefärlig) | 500 – 7500 per fil<br/>\*[Beror på filstorleken](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)| 11 K - 110 K (1375/vCore) |
| Log dataflöde | 3 MB/s per vCore<br/>Max 22 MB/s per instans | 4 MB/s per vCore<br/>Max 48 MB/s per instans|
| Dataflöde (ungefärlig) | 100 - 250 MB/s per fil<br/>\*[Beror på filstorleken](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes) | Gäller inte |
| I/o-svarstid (ungefärlig) | 5 – 10 ms | 1 – 2 ms |
| Maxstorlek för tempDB | 192 - 1,920 GB (24 GB per vCore) | Inga begränsningar – begränsas av de största instansstorleken för lagring |
| Maximalt antal sessioner | 30000 | 30000 |

> [!NOTE]
> - Både data och loggfiler filstorlek i användar- och systemdatabaser ingår i den instans lagringsstorleken som jämförs med den högsta gränsen. Använd <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> systemvy att fastställa det totala antalet använt utrymme i av databaser. Felloggar inte beständiga och inte ingår i storlek. Säkerhetskopior ingår inte i lagringsstorlek.
> - Dataflöde och allokerad IOPS beror också på den sidstorlek som inte uttryckligen begränsas av hanterad instans.

## <a name="supported-regions"></a>Regioner som stöds

Hanterade instanser kan bara skapas i [regioner som stöds](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Om du vill skapa en hanterad instans i en region som inte stöds för närvarande, kan du [skicka en supportförfrågan via Azure portal](#obtaining-a-larger-quota-for-sql-managed-instance).

## <a name="supported-subscription-types"></a>Prenumerationstyper som stöds

Hanterad instans stöder för närvarande distribution endast för följande typer av prenumerationer:

- [Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Cloud Service Provider (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise – utveckling/testning](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Betala per användning – utveckling/testning](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Prenumerationer med Azure-månadskrediten för Visual Studio-prenumeranter](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Regionala resursbegränsningar

Stöds prenumerationstyper kan innehålla ett begränsat antal resurser per region. Hanterad instans har två standardgränser per Azure-region beroende på en typ av prenumerationstyp:

- **Undernät gränsen**: Det maximala antalet undernät där hanterade instanser har distribuerats i en region.
- **vCore-gränsen**: Det maximala antalet virtuella kärnor som kan distribueras i alla instanser i en region.

> [!Note]
> Dessa gränser är standardinställningarna och inte tekniska begränsningar. Gränserna kan vara ökad på begäran genom att skapa en särskild [supportförfrågan i Azure-portalen](#obtaining-a-larger-quota-for-sql-managed-instance) om du behöver mer hanterade instanser i den aktuella regionen. Alternativt kan skapa du nya hanterade instanser i en annan Azure-region utan att skicka supportförfrågningar.

I följande tabell visas de regionala standardgränserna för stöds prenumerationer:

|Typ av prenumeration| Maxantal hanterade instansen undernät | Maxantal vCore enheter * |
| :---| :--- | :--- |
|Användningsbaserad betalning|3|320|
|CSP |8 (15 i vissa regioner **)|960 (1440 i vissa regioner **)|
|Betala per användning – utveckling/testning|3|320|
|Enterprise Dev/Test|3|320|
|EA|8 (15 i vissa regioner **)|960 (1440 i vissa regioner **)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional och MSDN-plattformar|2|32|

\* När du planerar distributionen bör du överväga att att en kritisk Business (BC) virtuell kärna (på grund av har lagts till redundans) använder 4 x mer kapacitet än en allmänt syfte (GP) virtuell kärna. Så för dina beräkningar, 1 GP vCore = 1 vCore-enhet och BC 1 vCore = 4 vCore-enheter. Sammanfatta vCore-enheter för att förenkla din förbrukning analys mot standardgränserna över alla undernät i den region där hanterade instanser har distribuerats och jämföra resultaten med enhet instansgränser för prenumerationstyp. **Maxantal vCore enheter** gränsen gäller för varje prenumeration i en region. Det finns ingen gräns per enskilda undernät, förutom att summan av alla virtuella kärnor som distribueras över flera undernät måste vara lägre eller lika med **max antal vCore enheter**.

\*\* Större undernät och vCore gränser är tillgängliga i följande regioner: Östra Australien, östra USA, östra USA 2, Nordeuropa, södra centrala USA, Sydostasien, Storbritannien, södra, Västeuropa, västra USA 2.

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>Hämta en större kvot för SQL-hanterad instans

Om du behöver mer hanterade instanser i din aktuella regioner kan du skicka en supportbegäran om att utöka kvoten med hjälp av Azure portal.
Så här initierar processen för att hämta en större kvot:

1. Öppna **hjälp + support**, och klicka på **ny supportbegäran**.

   ![Hjälp och Support](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. På fliken grunderna för ny supportbegäran:
   - För **typ av problem**väljer **begränsningar för tjänsten och -prenumeration (kvoter)** .
   - I fältet **Prenumeration** väljer du din prenumeration.
   - För **kvottypen**väljer **SQL Database Managed Instance**.
   - För **supportavtal**, Välj ditt supportavtal.

     ![Problemet typ av kvot](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. Klicka på **Nästa**.
4. På den **problemet fliken** för ny supportbegäran:
   - För **allvarlighetsgrad**, Välj problemets allvarlighetsgrad.
   - För **information**, ger ytterligare information om problemet, inklusive felmeddelanden.
   - För **filuppladdning**, bifoga en fil med mer information (upp till 4 MB).

     ![Probleminformation](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > En giltig begäran bör innehålla:
     > - Region i vilken prenumeration behöver gränsen ökas.
     > - Nödvändigt antal virtuella kärnor per tjänstnivå i befintliga undernät efter kvoten öka (om någon av de befintliga undernät måste utökas.
     > - Obligatoriskt antal nya undernät och Totalt antal virtuella kärnor per tjänstnivå inom de nya undernäten (om du behöver distribuera hanterade instanser i nya undernät).

5. Klicka på **Nästa**.
6. Ange önskad kontaktmetod (e-post eller telefon) och kontaktuppgifter på fliken kontaktuppgifter för ny supportbegäran.
7. Klicka på **Skapa**.

## <a name="next-steps"></a>Nästa steg

- Mer information om hanterad instans finns i [vad är en hanterad instans?](sql-database-managed-instance.md).
- Information om priser finns i [SQL Database managed instance priser](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Läs hur du skapar din första hanterad instans i [Snabbstartsguide för](sql-database-managed-instance-get-started.md).
