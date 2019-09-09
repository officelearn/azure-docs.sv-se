---
title: Azure SQL Database resurs gränser-hanterad instans | Microsoft Docs
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
ms.date: 08/27/2019
ms.openlocfilehash: c0bfbbd8b85f0b3eadf468cdd1261f52bff26abe
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813382"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Översikt Azure SQL Database hanterade instans resurs gränser

Den här artikeln innehåller en översikt över resurs gränserna för Azure SQL Database Hanterad instans och ger information om hur du begär en ökning av dessa gränser.

> [!NOTE]
> Skillnader i funktioner som stöds och T-SQL-uttryck finns i [funktions skillnader](sql-database-features.md) och [stöd för t-SQL-uttryck](sql-database-managed-instance-transact-sql-information.md).

## <a name="instance-level-resource-limits"></a>Resurs gränser på instans nivå

Den hanterade instansen har egenskaper och resurs gränser som är beroende av den underliggande infrastrukturen och arkitekturen. Gränserna är beroende av maskin varu generering och tjänst nivå.

### <a name="hardware-generation-characteristics"></a>Egenskaper för maskin varu skapande

Azure SQL Database Hanterad instans kan distribueras på två maskin varu generationer: Gen4 och Gen5. Maskin varu generationer har olika egenskaper, enligt beskrivningen i följande tabell:

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| Maskinvara | Intel E5-2673 v3 (Haswell) 2,4 GHz-processorer, anslutna SSD vCore = 1 PP (fysisk kärna) | Intel E5-2673 v4 (Broadwell) 2,3-GHz-processorer, fast NVMe SSD, vCore = 1 LP (Hyper-Thread) |
| Antal virtuella kärnor | 8, 16, 24 virtuella kärnor | 4, 8, 16, 24, 32, 40, 64, 80 virtuella kärnor |
| Högsta mängd minne (minne/Core-förhållande) | 7 GB per vCore<br/>Lägg till fler virtuella kärnor för att få mer minne. | 5,1 GB per vCore<br/>Lägg till fler virtuella kärnor för att få mer minne. |
| Högsta minnes intern OLTP-minne | Instans gräns: 3 GB per vCore<br/>Databas gränser:<br/> – 8 kärnor: 8 GB per databas<br/> – 16 kärnor: 20 GB per databas<br/> – 24 kärnor: 36 GB per databas | Instans gräns: 2,5 GB per vCore<br/>Databas gränser:<br/> – 8 kärnor: 13 GB per databas<br/> – 16 kärnor: 32 GB per databas |
| Högsta reserverade instans lagring |  Generell användning: 8 TB<br/>Affärskritisk: 1 TB | Generell användning: 8 TB<br/> Affärskritisk 1 TB, 2 TB eller 4 TB beroende på antalet kärnor |

> [!IMPORTANT]
> - Gen4-maskinvaran har fasats ut. Vi rekommenderar att du distribuerar nya hanterade instanser på Gen5-maskinvara.
> - Gen4 maskin vara för närvarande är tillgänglig i följande regioner: Nord Europa, Västeuropa, östra USA, södra centrala USA, norra centrala USA, västra USA 2, centrala USA, centrala Kanada, södra Indien, Sydostasien och Korea, centrala.

### <a name="service-tier-characteristics"></a>Egenskaper för tjänst nivå

Den hanterade instansen har två tjänst nivåer: Generell användning och Affärskritisk. Dessa nivåer ger olika funktioner, enligt beskrivningen i tabellen nedan:

| **Funktion** | **Generell användning** | **Affärskritisk** |
| --- | --- | --- |
| Antal virtuella kärnor\* | Gen4 8, 16, 24<br/>Gen5 4, 8, 16, 24, 32, 40, 64, 80 | Gen4 8, 16, 24 <br/> Gen5 4, 8, 16, 24, 32, 40, 64, 80 |
| Högsta mängd minne | Gen4 56 GB-168 GB (7GB/vCore)<br/>Gen5 40,8 GB-408 GB (5,1 GB/vCore)<br/>Lägg till fler virtuella kärnor för att få mer minne. | Gen4 56 GB-168 GB (7GB/vCore)<br/>Gen5 40,8 GB-408 GB (5,1 GB/vCore)<br/>Lägg till fler virtuella kärnor för att få mer minne. |
| Maximal lagrings storlek för reserverad instans | -2 TB för 4 virtuella kärnor (endast Gen5)<br/>– 8 TB för andra storlekar | Gen4 1 TB <br/> Gen5 <br/>-1 TB för 4, 8, 16 virtuella kärnor<br/>– 2 TB för 24 virtuella kärnor<br/>– 4 TB för 32, 40, 64, 80 virtuella kärnor |
| Maximal databasstorlek | 8 TB | 4 TB |
| Maximalt antal databaser per instans | 100 | 100 |
| Maximalt antal databasfiler per instans | Upp till 280 | 32 767 filer per databas |
| Maximal fil storlek | 8 TB | 4 TB |
| Data/logga IOPS (ungefärligt) | 500 – 7 500 per fil<br/>\*[Öka fil storleken för att få mer IOPS](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)| 5,5 k-110 K (1375/vCore)<br/>Lägg till fler virtuella kärnor för att få bättre IO-prestanda. |
| Skriv gränsen för logg skrivnings data | 3 MB/s per vCore<br/>Högst 22 MB/s per instans | 4 MB/s per vCore<br/>Max 48 MB/s per instans|
| Data flöde (ungefärligt) | 100-250 MB/s per fil<br/>\*[Öka fil storleken för att få bättre IO-prestanda](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes) | Gäller inte |
| IO-latens för lagring (ungefärligt) | 5-10 MS | 1-2 MS |
| Maximal tempDB-storlek | 192 – 1 920 GB (24 GB per vCore)<br/>Lägg till fler virtuella kärnor för att få mer TempDB-utrymme. | Begränsas av maximal instans lagrings storlek. TempDB-logg fils storleken är för närvarande begränsad till 24GB/vCore. |
| Minnesintern OLTP | Stöds inte | Tillgängligt |
| Maximalt antal sessioner | 30000 | 30000 |
| Läsbara repliker | 0 | 1 (ingår i priset) |
| Priser/fakturering | vCore, reserverad lagring  <br/> IOPS debiteras inte, lagring av säkerhets kopior debiteras inte ännu. | vCore, reserverad lagring  <br/> IOPS debiteras inte, lagring av säkerhets kopior debiteras inte ännu. | 
| Rabatt modeller | [Reserverade instanser](sql-database-reserved-capacity.md)<br/>[Azure Hybrid-förmån](sql-database-service-tiers-vcore.md#azure-hybrid-benefit) (inte tillgängligt för utveckling/testning-prenumerationer) | [Reserverade instanser](sql-database-reserved-capacity.md)<br/>[Azure Hybrid-förmån](sql-database-service-tiers-vcore.md#azure-hybrid-benefit) (inte tillgängligt för utveckling/testning-prenumerationer)|

> [!NOTE]
> - Både data-och logg fils storleken i användar-och system databaserna ingår i instans lagrings storleken som jämförs med den maximala lagrings storleks gränsen. Använd <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys. master_files</a> system-vyn för att fastställa det totala använda utrymmet för databaser. Fel loggarna är inte bestående och ingår inte i storlek. Säkerhets kopieringar ingår inte i lagrings storleken.
> - Data flödet och IOPS är också beroende av den sid storlek som inte uttryckligen begränsas av den hanterade instansen.
> Du kan skapa en annan läsbar replik i olika Azure-regioner med hjälp av grupper för automatisk redundans.

## <a name="supported-regions"></a>Regioner som stöds

Hanterade instanser kan bara skapas i [regioner som stöds](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Om du vill skapa en hanterad instans i en region som för närvarande inte stöds kan du [skicka en supportbegäran via Azure Portal](#obtaining-a-larger-quota-for-sql-managed-instance).

## <a name="supported-subscription-types"></a>Prenumerations typer som stöds

Den hanterade instansen stöder för närvarande endast distribution av följande typer av prenumerationer:

- [Enterprise-avtal (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Betala per användning](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Cloud Service Provider (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Dev/Test – betala per användning](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Prenumerationer med månatlig Azure-kredit för Visual Studio-prenumeranter](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Begränsningar för regionala resurser

Prenumerations typer som stöds kan innehålla ett begränsat antal resurser per region. Den hanterade instansen har två standard gränser per Azure-region beroende på typ av prenumerations typ:

- **Under näts gräns**: Det maximala antalet undernät där hanterade instanser distribueras i en enda region.
- **vCore gräns**: Det maximala antalet virtuella kärnor som kan distribueras över alla instanser i en enda region.

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

\*Vid planering av distributioner bör du tänka på att Affärskritisk (BC) tjänst nivån kräver fyra (4) gånger mer vCore-kapacitet än Generell användning (GP). Exempel: 1 GP-vCore = 1 vCore-enhet och 1 BC-vCore = 4 vCore-enheter. För att förenkla förbruknings analysen mot standard gränserna ska du sammanfatta vCore-enheterna över alla undernät i den region där hanterade instanser distribueras och jämföra resultaten med instans enhets gränserna för din prenumerations typ. Gränsen **för antalet vCore-enheter** gäller för varje prenumeration i en region. Det finns ingen gräns per enskilda undernät förutom att summan av alla virtuella kärnor som distribueras över flera undernät måste vara lägre eller lika med **Max antalet vCore-enheter**.

\*\*Större gränser för undernät och vCore är tillgängliga i följande regioner: Östra Australien, östra USA, östra USA 2, norra Europa, södra centrala USA, Sydostasien, Storbritannien, södra, Västeuropa, västra USA 2.

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

     ![Uppgifter om problem](media/sql-database-managed-instance-resource-limits/problem-details.png)

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
