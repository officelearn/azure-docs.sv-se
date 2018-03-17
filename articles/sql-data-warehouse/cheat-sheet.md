---
title: "Cheat blad för Azure SQL Data Warehouse | Microsoft Docs"
description: "Hitta länkar och bästa praxis att snabbt skapa din Azure SQL Data Warehouse-lösningar."
services: sql-data-warehouse
documentationcenter: NA
author: acomet
manager: jhubbard
editor: 
ms.assetid: 51f1e444-9ef7-4e30-9a88-598946c45196
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 02/20/2018
ms.author: acomet
ms.openlocfilehash: c67d56ff63f70baa052be17c119d943c558d398f
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="cheat-sheet-for-azure-sql-data-warehouse"></a>Cheat blad för Azure SQL Data Warehouse
Den här fusklapp ger nyttiga tips och bästa praxis för att skapa din Azure SQL Data Warehouse-lösningar. Innan du börjar, mer information om varje steg i detalj genom att läsa [Azure SQL Data Warehouse arbetsbelastning mönster och ett mönster](https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/azure-sql-data-warehouse-workload-patterns-and-anti-patterns), som beskriver SQL Data Warehouse är och vad det är inte.

Följande bild visar hur du skapar ett datalager:

![Skiss]

## <a name="queries-and-operations-across-tables"></a>Frågor och åtgärder mellan tabeller

När du vet i förväg primära operationer och frågor för att köras i datalagret kan prioritera du data warehouse-arkitektur för dessa åtgärder. Dessa frågor och åtgärder kan innehålla:
* Ansluta till en eller två faktatabeller med dimensionstabeller tabellen kombinerade-filtrering och att resultaten i ett dataarkiv.
* Gör stora eller små uppdateringar till fakta-försäljning.
* Lägger endast till tabeller.

Att veta vilka typer av åtgärder i förväg hjälper dig att optimera utformningen av tabeller.

## <a name="data-migration"></a>Datamigrering

Först läsa in data till [Azure Data Lake Store](https://docs.microsoft.com/en-us/azure/data-factory/connector-azure-data-lake-store) eller Azure Blob storage. Använd sedan PolyBase för att läsa in dina data till SQL Data Warehouse i en mellanlagringstabellen. Använd följande konfiguration:

| Designa | Rekommendation |
|:--- |:--- |
| Distribution | Resursallokering |
| Indexering | Heap |
| Partitionering | Ingen |
| Resursklass | largerc eller xlargerc |

Lär dig mer om [datamigrering], [datainläsning], och [extrahera, Load and Transform ELT ()-processen](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/design-elt-data-loading). 

## <a name="distributed-or-replicated-tables"></a>Distribuerade eller replikerade tabeller

Använd följande strategier, beroende på Tabellegenskaper:

| Typ | Bra val för...| Kontrollerar om...|
|:--- |:--- |:--- |
| Replikerade | • Små dimensionstabeller i ett stjärnschema med mindre än 2 GB lagringsutrymme efter komprimering (~ 5 x komprimering) |• Många skriva transaktioner är för tabellen (till exempel insert, upsert, ta bort, uppdateringen)<br></br>• Ändra Data Warehouse enheter (DWU) etablering ofta<br></br>• Du bara använda 2 – 3 kolumner men din tabell har många kolumner<br></br>• Du indexera en replikerad tabell |
| Resursallokering (standard) | • Tillfälligt/mellanlagringstabell<br></br> • Uppenbara inte ansluta till nyckeln eller så bra kandidat kolumn |• Prestanda går långsamt på grund av dataförflyttning |
| Hash | • Faktatabeller<br></br>• Stora dimensionstabeller |• Distributionsnyckeln kan inte uppdateras |

**Tips:**
* Börja med resursallokering, men vill en hash-distributionsstrategi dra nytta av en arkitektur med massivt parallell.
* Se till att vanliga hash-nycklar har samma format.
* Distribuera inte på varchar-format.
* Dimensionstabeller med en gemensam hash-nyckel till en faktatabell med frekventa kopplingsåtgärder kan vara hash som distribueras.
* Använd  *[sys.dm_pdw_nodes_db_partition_stats]*  att analysera alla snedheten i data.
* Använd  *[sys.dm_pdw_request_steps]*  förflyttningar bakom frågor, övervaka tiden broadcast för att analysera data och blanda operationer. Det är bra att granska din distributionsstrategi för.

Lär dig mer om [replikerade tabeller] och [distribuerade tabeller].

## <a name="index-your-table"></a>Index för tabellen

Indexering är användbart för att läsa in tabeller snabbt. Det finns en unik uppsättning tekniker som du kan använda baserat på dina behov:

| Typ | Bra val för... | Kontrollerar om...|
|:--- |:--- |:--- |
| Heap | • Mellanlagring/tillfällig tabell<br></br>• Små tabeller med små sökningar |• En sökning söker igenom hela tabellen |
| Grupperat index | • Tabeller med upp till 100 miljoner rader<br></br>• Stora tabeller (fler än 100 miljoner rader) med endast 1 – 2 för hög |• Som används i en replikerad tabell<br></br>• Du har avancerade frågor som omfattar flera koppling och Group By-åtgärder<br></br>• Du gör uppdateringar i de fulltextindexerade kolumnerna: det tar minne |
| Grupperade columnstore-indexet (CCI) (standard) | • Stora tabeller (fler än 100 miljoner rader) | • Som används i en replikerad tabell<br></br>• Du gör omfattande uppdateringsåtgärder på tabellen<br></br>• Du overpartition tabellen: radgrupper omfattar inte över en annan distributionsplats noder och partitioner |

**Tips:**
* Du kanske vill lägga till en icke-grupperat index i en kolumn som kraftigt används för att filtrera ovanpå ett grupperat index. 
* Var noga med hur du hanterar minne på en tabell med CCI. När du läser in data som du vill användaren (eller frågan) för att dra nytta av en stor resursklassen. Se till att undvika trimning och skapa många små komprimerade radgrupper.
* Optimerad för att beräkna nivån klippor med CCI.
* För CCI, kan dåliga prestanda inträffa på grund av dålig komprimering av din radgrupper. Om detta inträffar kan du återskapa eller ordna om din CCI. Vill du minst 100 000 rader per komprimerade radgrupper. Perfekt är 1 miljoner rader i en grupp.
* Utifrån inkrementell belastningen frekvens och storlek som du vill automatisera när du vill ordna om eller återskapa ditt index. Rensa värdet är alltid bra.
* Vara strategiska när du vill ta bort en grupp. Hur stora är öppna radgrupper? Hur mycket data du vill läsa in under de kommande dagarna?

Lär dig mer om [index].

## <a name="partitioning"></a>Partitionering
Du kan partitionera tabellen när du har en stor faktatabell (större än 1 miljard rader). Partitionsnyckeln ska baseras på datum i 99 procent av fall. Var noga med att inte overpartition, särskilt när du har ett grupperat columnstore-index.

Med mellanlagringstabeller som kräver ELT, kan du dra nytta partitionering. Det underlättar Livscykelhantering för data.
Var noga med att inte overpartition dina data, särskilt på ett grupperat columnstore-index.

Lär dig mer om [partitioner].

## <a name="incremental-load"></a>Inkrementell belastning

Om du kommer att läsa in dina data inkrementellt, måste du kontrollera att du allokera större resursklasser för att läsa in data. Vi rekommenderar att du använder PolyBase och ADF V2 för att automatisera dina ELT pipelines i SQL Data Warehouse.

För en stor grupp med uppdateringar i din historiska data du först ta bort de berörda data. Gör sedan en massinfogning av nya data. Den här metoden för tvåstegsverifiering är effektivare.

## <a name="maintain-statistics"></a>Underhålla statistik
 Tills automatiskt statistik är allmänt tillgänglig kräver SQL Data Warehouse manuellt underhåll av statistik. Det är viktigt att uppdatera statistik som *betydande* ändringar som sker till dina data. Detta hjälper till att optimera din frågeplaner. Om du tycker att det tar för lång tid att behålla alla dina statistik kan vara mer selektiv om vilka kolumner har statistik. 

Du kan också definiera frekvensen av uppdateringarna. Du kanske vill uppdatera datumkolumnerna, där nya värden kan läggas till, dagligen. Du får mest nytta av med statistik om kolumner som ingår i kopplingar, kolumner som används i WHERE-satsen och kolumner i GROUP BY.

Lär dig mer om [statistik].

## <a name="resource-class"></a>Resursklass
SQL Data Warehouse använder resursgrupper som ett sätt att allokera minne till frågor. Om du behöver mer minne för att förbättra frågan eller läsa in hastighet, bör du allokera högre resurs klasserna. På sidan Vänd med större resursklasser påverkar samtidighet. Vill du beakta som innan du flyttar alla användare till en stor resursklassen.

Om du märker att frågor tar för lång tid, kan du kontrollera att användarna inte kör i stora resursklasser. Stora resursklasser använda många samtidiga platser. De kan orsaka andra frågor till kö.

Med hjälp av Compute optimerade nivån hämtar slutligen varje resursklassen 2,5 gånger så mycket minne på den elastiska optimerade nivån.

Lär dig mer om hur du arbetar med [resursklasser och samtidighet].

## <a name="lower-your-cost"></a>Lägre dina kostnader
En nyckelfunktion i SQL Data Warehouse är möjligheten att [hantera beräkningsresurser](sql-data-warehouse-manage-compute-overview.md). Du kan pausa datalagret när du inte använder den, vilket avbryter faktureringen av beräkningsresurser. Du kan skala resurser för att möta dina krav på prestanda. Pausa genom att använda den [Azure-portalen](pause-and-resume-compute-portal.md) eller [PowerShell](pause-and-resume-compute-powershell.md). Om du vill skala, Använd den [Azure-portalen](quickstart-scale-compute-portal.md), [Powershell](quickstart-scale-compute-powershell.md), [T-SQL](quickstart-scale-compute-tsql.md), eller en [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Autoskala nu när du vill med Azure Functions:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

## <a name="optimize-your-architecture-for-performance"></a>Optimera din arkitektur för prestanda

Vi rekommenderar att SQL Database och Azure Analysis Services i en nav och eker-arkitektur. Den här lösningen kan isolera arbetsbelastningen mellan olika grupper av användare när du använder också funktioner för avancerad säkerhet från SQL Database och Azure Analysis Services. Detta är ett sätt att förse användarna obegränsad samtidighet.

Lär dig mer om [vanliga arkitekturer som utnyttjar SQL Data Warehouse](https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/common-isv-application-patterns-using-azure-sql-data-warehouse/).

Distribuera i en klickar du på din ekrar i SQL-databaser från SQL Data Warehouse:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwSpokeDbTemplate%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>


<!--Image references-->
[Skiss]:media/sql-data-warehouse-cheat-sheet/picture-flow.png

<!--Article references-->
[datainläsning]:design-elt-data-loading.md
[deeper guidance]:guidance-for-loading-data.md
[index]:sql-data-warehouse-tables-index.md
[partitioner]:sql-data-warehouse-tables-partition.md
[statistik]:sql-data-warehouse-tables-statistics.md
[resursklasser och samtidighet]:resource-classes-for-workload-management.md

<!--MSDN references-->


<!--Other Web references-->
[typical architectures that take advantage of SQL Data Warehouse]: https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/common-isv-application-patterns-using-azure-sql-data-warehouse/
[is and is not]:https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/azure-sql-data-warehouse-workload-patterns-and-anti-patterns/
[datamigrering]:https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/
[replikerade tabeller]:https://docs.microsoft.com/en-us/azure/sql-data-warehouse/design-guidance-for-replicated-tables
[distribuerade tabeller]:https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute
[Azure Data Lake Store]: https://docs.microsoft.com/en-us/azure/data-factory/connector-azure-data-lake-store
[sys.dm_pdw_nodes_db_partition_stats]: https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql
[sys.dm_pdw_request_steps]:https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql
