---
title: Lathund för Azure Synapse Analytics (tidigare SQL DW)
description: Hitta länkar och metodtips för att snabbt skapa dina Azure Synapse Analytics-lösningar (tidigare SQL DW).
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 8e0515727c2155b91f18398bd9def700f4a15b34
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619414"
---
# <a name="cheat-sheet-for-azure-synapse-analytics-formerly-sql-dw"></a>Lathund för Azure Synapse Analytics (tidigare SQL DW)

Den här lathunden innehåller användbara tips och metodtips för att skapa Azure Synapse-lösningar. 

Följande bild visar hur du skapar ett informationslager:

![Skiss](./media/cheat-sheet/picture-flow.png)

## <a name="queries-and-operations-across-tables"></a>Frågor och åtgärder i tabeller

När du vet i förväg vilka primära åtgärder och frågor som ska köras i ditt informationslager kan du prioritera arkitekturen för ditt informationslager för de åtgärderna. Dessa frågor och åtgärder kan innehålla:
* Anslutning till en eller två faktatabeller med dimensionstabeller, filtrering av den kombinerade tabellen och sedan bifogning av resultatet i en data mart.
* Gör stora eller små uppdateringar i din försäljning.
* Lägger endast till data i dina tabeller.

När du vet åtgärdstyperna i förväg kan du optimera tabellernas design.

## <a name="data-migration"></a>Datamigrering

Läs först in dina data i [Azure Data Lake Storage](../../data-factory/connector-azure-data-lake-store.md) eller Azure Blob Storage. Använd sedan PolyBase för att läsa in data i mellanlagringstabeller. Använd följande konfiguration:

| Design | Rekommendation |
|:--- |:--- |
| Distribution | Resursallokering |
| Indexering | Heap |
| Partitionering | Inget |
| Resursklass | largerc eller xlargerc |

Läs mer om [datamigrering](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-data-to-azure-sql-data-warehouse-in-practice/), [datainläsning](design-elt-data-loading.md) och [ELT-processen (Extract, Load, and Transform)](design-elt-data-loading.md). 

## <a name="distributed-or-replicated-tables"></a>Distribuerade eller replikerade tabeller

Använd följande strategier, beroende på tabellens egenskaper:

| Typ | Passar bra för...| Se upp om...|
|:--- |:--- |:--- |
| Replikerad | * Små dimension tabeller i en stjärna schema med mindre än 2 GB lagringsutrymme efter komprimering (~ 5x komprimering) |* Många skriva transaktioner är på bordet (t.ex. infoga, upsert, ta bort, uppdatera)<br></br>* Du ändrar DWU-etablering (Data Warehouse Units) ofta<br></br>* Du använder bara 2-3 kolumner men tabellen har många kolumner<br></br>* Du indexerar en replikerad tabell |
| Resursallokering (standard) | * Tillfälligt/mellanlagringsbord<br></br> * Ingen uppenbar gå nyckel eller bra kandidat kolumn |* Prestanda är långsam på grund av datarörelser |
| Hash | * Fakta tabeller<br></br>* Tabeller med stor dimension |* Distributionsnyckeln kan inte uppdateras |

**Tips:**
* Börja med resursallokering, men försök att skapa en hashdistributionsstrategi för att dra nytta av en massiv parallell arkitektur.
* Se till att vanliga hash-nycklar har samma dataformat.
* Distribuera inte på varchar-format.
* Dimensionstabeller med en vanlig hash-nyckel för en faktatabell med många kopplingsåtgärder kan hash-distribueras.
* Använd *[sys.dm_pdw_nodes_db_partition_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql)* för att analysera eventuella snedställningar i dina data.
* Använd *[sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql)* för att analysera dataförflyttning bakom frågor, övervaka sändningstiden och blanda åtgärder. Det är praktiskt när du granskar din distributionsstrategi.

Läs mer om [replikerade tabeller](design-guidance-for-replicated-tables.md) och [distribuerade tabeller](sql-data-warehouse-tables-distribute.md).

## <a name="index-your-table"></a>Indexera tabellen

Indexering är bra när du vill läsa tabeller snabbt. Det finns en unik uppsättning tekniker som du kan använda utifrån dina behov:

| Typ | Passar bra för... | Se upp om...|
|:--- |:--- |:--- |
| Heap | * Mellanlagring/temporär tabell<br></br>* Små bord med små uppslag |* Alla sökningar skannar hela tabellen |
| Grupperat index | * Tabeller med upp till 100 miljoner rader<br></br>* Stora tabeller (mer än 100 miljoner rader) med endast 1-2 kolumner som används kraftigt |* Används på ett replikerat bord<br></br>* Du har komplexa frågor som involverar flera join och Group By-operationer<br></br>* Du gör uppdateringar på indexerade kolumner: det tar minne |
| Grupperade columnstore-index (kolumnlagringsindex) (standard) | * Stora tabeller (mer än 100 miljoner rader) | * Används på ett replikerat bord<br></br>* Du gör massiva uppdateringsåtgärder på ditt bord<br></br>* Du överpartition din tabell: radgrupper inte spänner över olika distributionsnoder och partitioner |

**Tips:**
* Förutom ett grupperat index kanske du vill lägga till ett icke-grupperat index till en kolumn som används ofta för filtrering. 
* Var försiktig med hur du hanterar minnet i en tabell med CCI. När du läser in data ska användaren (eller frågan) dra nytta av en stor resursklass. Undvik att trimma och skapa många små komprimerade radgrupper.
* På Gen2 cachelagras CCI-tabeller lokalt på datornoderna för att maximera prestanda.
* För CCI kan dåliga prestanda uppstå på grund av dålig komprimering av dina radgrupper. Om det sker ska du återskapa eller ordna om din CCI. Du bör ha minst 100 000 rader per komprimerad radgrupp. Det bästa är 1 miljon rader i en radgrupp.
* Baserat på den stegvisa frekvensen och storleken vill du automatisera när du ordnar om eller återskapar dina index. En vårstädning är aldrig fel.
* Var strategisk när du vill trimma en radgrupp. Hur stora är öppna radgrupper? Hur mycket data förväntar du dig att läsa in under de kommande dagarna?

Läs mer om [index](sql-data-warehouse-tables-index.md).

## <a name="partitioning"></a>Partitionering
Du kan partitionera tabellen när du har en stor faktatabell (fler än 1 miljard rader). I 99 procent av fallen ska partitionsnyckeln baseras på datum. Var noga så att du inte överpartitionerar, särskilt när du har ett grupperat kolumnlagringsindex.

Med mellanlagringstabeller som kräver ELT kan du dra nytta av partitionering. Det underlättar hanteringen av datalivscykeln.
Var noga så att du inte överpartitionerar dina data, särskilt när du har ett grupperat kolumnlagringsindex.

Läs mer om [partitioner](sql-data-warehouse-tables-partition.md).

## <a name="incremental-load"></a>Stegvis inläsning

Om du ska läsa in data stegvis ska du först kontrollera att du allokerar större resursklasser för att läsa in dina data.  Detta är särskilt viktigt när du läser in i tabeller med klustrade columnstore-index.  Se [resursklasser](resource-classes-for-workload-management.md) för mer information.  

Vi rekommenderar att du använder PolyBase och ADF V2 för att automatisera dina ELT-pipelines i ditt informationslager.

För en stor grupp uppdateringar i historiska data bör du överväga att använda en [CTAS](sql-data-warehouse-develop-ctas.md) för att skriva de data som du vill behålla i en tabell i stället för att använda INSERT, UPDATE och DELETE.

## <a name="maintain-statistics"></a>Underhålla statistik
 Fram till dess att automatisk statistik är allmänt tillgänglig krävs manuellt underhåll av statistik. Det är viktigt att uppdatera statistik när *betydande* ändringar sker i dina data. Detta hjälper till att optimera dina frågeplaner. Om du tycker att det tar för lång tid att behålla all statistik kan du vara mer selektiv med vilka kolumner som ska ha statistik. 

Du kan även definiera frekvensen för uppdateringarna. Du kanske till exempel vill uppdatera datumkolumner, där nya värden kan läggas till, varje dag. Du får ut mest genom att använda statistik med kolumner som ingår i kopplingar, kolumner som används i WHERE-satsen och kolumner som finns i GROUP BY.

Läs mer om [statistik](sql-data-warehouse-tables-statistics.md).

## <a name="resource-class"></a>Resursklass
Resursgrupper används som ett sätt att allokera minne till frågor. Om du behöver mer minne för att förbättra hastigheten för frågor eller inläsning ska du allokera högre resursklasser. Å andra sidan påverkar användning av större klasser samtidigheten. Du bör överväga det innan du flyttar alla dina användare till en stor resursklass.

Om du märker att frågor tar för lång tid kan du kontrollera att dina användare inte körs i stora resursklasser. Stora resursklasser förbrukar många samtidighetsfack. De kan orsaka att andra frågor placeras i kö.

Slutligen, genom att använda Gen2 i [SQL-pool](sql-data-warehouse-overview-what-is.md#synapse-sql-pool-in-azure-synapse), varje resursklass får 2,5 gånger mer minne än Gen1.

Lär dig mer om hur du arbetar med [resursklasser och samtidighet](resource-classes-for-workload-management.md).

## <a name="lower-your-cost"></a>Sänk kostnaderna
En viktig funktion i Azure Synapse är möjligheten att [hantera beräkningsresurser](sql-data-warehouse-manage-compute-overview.md). Du kan pausa SQL-poolen när du inte använder den, vilket stoppar faktureringen av beräkningsresurser. Du kan skala resurser för att uppfylla dina prestandakrav. Om du vill pausa använder du [Azure-portalen](../../synapse-analytics/sql-data-warehouse/pause-and-resume-compute-portal.md) eller [PowerShell](../../synapse-analytics/sql-data-warehouse/pause-and-resume-compute-powershell.md). Om du vill skala använder du [Azure-portalen](quickstart-scale-compute-portal.md), [Powershell](quickstart-scale-compute-powershell.md), [T-SQL](quickstart-scale-compute-tsql.md) eller en [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).


En viktig funktion i Azure Synapse är möjligheten att [hantera beräkningsresurser](sql-data-warehouse-manage-compute-overview.md). Du kan pausa SQL-poolen när du inte använder den, vilket stoppar faktureringen av beräkningsresurser. Du kan skala resurser för att uppfylla dina prestandakrav. Om du vill pausa använder du [Azure-portalen](../../sql-data-warehouse/pause-and-resume-compute-portal.md) eller [PowerShell](../../sql-data-warehouse/pause-and-resume-compute-powershell.md). Om du vill skala använder du [Azure-portalen](quickstart-scale-compute-portal.md), [Powershell](quickstart-scale-compute-powershell.md), [T-SQL](quickstart-scale-compute-tsql.md) eller en [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Autoskala nu för den tid du önskar med Azure Functions:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

## <a name="optimize-your-architecture-for-performance"></a>Optimera din arkitektur för prestanda

Vi rekommenderar att du överväger SQL Database och Azure Analysis Services i en nav-och-eker-arkitektur. Den här lösningen kan isolera arbetsbelastningen mellan olika grupper av användare när du även använder avancerade funktioner från SQL Database och Azure Analysis Services. Det här är också ett sätt att tillhandahålla obegränsad samtidighet till dina användare.

Läs mer om [vanliga arkitekturer som drar nytta av Azure Synapse](https://blogs.msdn.microsoft.com/sqlcat/20../../common-isv-application-patterns-using-azure-sql-data-warehouse/).

Distribuera med ett klick dina ekrar i SQL-databaser från SQL Pool:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwSpokeDbTemplate%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>