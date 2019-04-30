---
title: Lathund för Azure SQL Data Warehouse | Microsoft Docs
description: Hitta länkar och bästa metoder för att snabbt skapa dina Azure SQL Data Warehouse-lösningar.
services: sql-data-warehouse
author: acomet
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: design
ms.date: 04/17/2018
ms.author: acomet
ms.reviewer: igorstan
ms.openlocfilehash: 795facc6148d33592ff8eac5083a273dc3d5cb26
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60732309"
---
# <a name="cheat-sheet-for-azure-sql-data-warehouse"></a>Lathund för Azure SQL Data Warehouse
Med den här lathunden får du praktiska tips och bästa metoder för att skapa dina Azure SQL Data Warehouse-lösningar. Innan du börjar bör du lära dig mer om varje steg genom att läsa om [mönster och antimönster i arbetsbelastningar i Azure SQL Data Warehouse](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-workload-patterns-and-anti-patterns), där det står vad SQL Data Warehouse är och vad det inte är.

Följande bild visar hur du skapar ett informationslager:

![Skiss]

## <a name="queries-and-operations-across-tables"></a>Frågor och åtgärder i tabeller

När du vet i förväg vilka primära åtgärder och frågor som ska köras i ditt informationslager kan du prioritera arkitekturen för ditt informationslager för de åtgärderna. Dessa frågor och åtgärder kan innehålla:
* Anslutning till en eller två faktatabeller med dimensionstabeller, filtrering av den kombinerade tabellen och sedan bifogning av resultatet i en data mart.
* Gör stora eller små uppdateringar i din försäljning.
* Lägger endast till data i dina tabeller.

När du vet åtgärdstyperna i förväg kan du optimera tabellernas design.

## <a name="data-migration"></a>Datamigrering

Läs först in dina data till [Azure Data Lake Store](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-store) eller Azure Blob Storage. Använd sedan PolyBase för att läsa in dina data i SQL Data Warehouse i en mellanlagringstabell. Använd följande konfiguration:

| Design | Rekommendation |
|:--- |:--- |
| Distribution | Resursallokering |
| Indexering | Heap |
| Partitionering | Ingen |
| Resursklass | largerc eller xlargerc |

Läs mer om [datamigrering], [datainläsning] och [ELT-processen (Extract, Load, and Transform)](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading). 

## <a name="distributed-or-replicated-tables"></a>Distribuerade eller replikerade tabeller

Använd följande strategier, beroende på tabellens egenskaper:

| Typ | Passar bra för...| Se upp om...|
|:--- |:--- |:--- |
| Replikerad | • Små dimensionstabeller i ett star-schema med mindre än 2 GB lagring efter komprimering (~5x komprimering) |•  Det finns många skrivtransaktioner i tabellen (som infoga, upsert, ta bort, uppdatera)<br></br>• Du ändrar DWU-etablering (Data Warehouse Units) ofta<br></br>• Du endast använder 2–3 kolumner men tabellen har många kolumner<br></br>•  Du indexerar en replikerad tabell |
| Resursallokering (standard) | • Tillfällig/mellanlagringstabell<br></br> • Ingen uppenbar kopplingsnyckel eller kolumn för bra kandidater |• Prestanda går långsamt på grund av dataförflyttning |
| Hash | • Faktatabeller<br></br>•    Stora dimensionstabeller |• Distributionsnyckeln kan inte uppdateras |

**Tips:**
* Börja med resursallokering, men försök att skapa en hashdistributionsstrategi för att dra nytta av en massiv parallell arkitektur.
* Se till att vanliga hash-nycklar har samma dataformat.
* Distribuera inte i varchar-format.
* Dimensionstabeller med en vanlig hash-nyckel för en faktatabell med många kopplingsåtgärder kan hash-distribueras.
* Använd *[sys.dm_pdw_nodes_db_partition_stats]* för att analysera eventuella snedställningar i dina data.
* Använd *[sys.dm_pdw_request_steps]* för att analysera dataförflyttning bakom frågor, övervaka sändningstiden och blanda åtgärder. Det är praktiskt när du granskar din distributionsstrategi.

Läs mer om [replikerade tabeller] och [distribuerade tabeller].

## <a name="index-your-table"></a>Indexera tabellen

Indexering är bra när du vill läsa tabeller snabbt. Det finns en unik uppsättning tekniker som du kan använda utifrån dina behov:

| Typ | Passar bra för... | Se upp om...|
|:--- |:--- |:--- |
| Heap | • Mellanlagrings-/temporär tabell<br></br>• Små tabeller med små sökningar |• Sökningar som genomsöker hela tabellen |
| Grupperat index | • Tabeller med upp till 100 miljoner rader<br></br>• Stora tabeller (över 100 miljoner rader) där endast 1–2 kolumner används mycket |•  Används på en replikerad tabell<br></br>•    Du har avancerade frågor som omfattar flera kopplingsåtgärder och Gruppera efter-åtgärder<br></br>•  Du gör uppdateringar för de indexerade kolumnerna: det kräver minne |
| Grupperade columnstore-index (kolumnlagringsindex) (standard) | •   Stora tabeller (över 100 miljoner rader) | •  Används på en replikerad tabell<br></br>•   Du gör omfattande uppdateringsåtgärder för tabellen<br></br>•  Du överpartitionerar din tabell: radgrupper sträcker sig inte över olika distributionsnoder och partitioner |

**Tips:**
* Förutom ett grupperat index kanske du vill lägga till ett icke-grupperat index till en kolumn som används ofta för filtrering. 
* Var försiktig med hur du hanterar minnet i en tabell med CCI. När du läser in data ska användaren (eller frågan) dra nytta av en stor resursklass. Undvik att trimma och skapa många små komprimerade radgrupper.
* På Gen2 cachelagras CCI-tabeller lokalt på datornoderna för att maximera prestanda.
* För CCI kan dåliga prestanda uppstå på grund av dålig komprimering av dina radgrupper. Om det sker ska du återskapa eller ordna om din CCI. Du bör ha minst 100 000 rader per komprimerad radgrupp. Det bästa är 1 miljon rader i en radgrupp.
* Baserat på den stegvisa frekvensen och storleken vill du automatisera när du ordnar om eller återskapar dina index. En vårstädning är aldrig fel.
* Var strategisk när du vill trimma en radgrupp. Hur stora är öppna radgrupper? Hur mycket data förväntar du dig att läsa in under de kommande dagarna?

Läs mer om [index].

## <a name="partitioning"></a>Partitionering
Du kan partitionera tabellen när du har en stor faktatabell (fler än 1 miljard rader). I 99 procent av fallen ska partitionsnyckeln baseras på datum. Var noga så att du inte överpartitionerar, särskilt när du har ett grupperat kolumnlagringsindex.

Med mellanlagringstabeller som kräver ELT kan du dra nytta av partitionering. Det underlättar hanteringen av datalivscykeln.
Var noga så att du inte överpartitionerar dina data, särskilt när du har ett grupperat kolumnlagringsindex.

Läs mer om [partitioner].

## <a name="incremental-load"></a>Stegvis inläsning

Om du ska läsa in data stegvis ska du först kontrollera att du allokerar större resursklasser för att läsa in dina data. Vi rekommenderar att du använder PolyBase och ADF V2 för att automatisera dina ELT-pipelines i SQL Data Warehouse.

För en stor grupp med uppdateringar i dina historiska data bör du först ta bort berörda data. Gör sedan en massinfogning av nya data. Den här metoden för tvåstegsverifiering är effektivare.

## <a name="maintain-statistics"></a>Underhålla statistik
 Tills automatisk statistik är allmänt tillgänglig kräver SQL Data Warehouse manuell underhåll av statistik. Det är viktigt att uppdatera statistik när viktiga dataändringar görs. Detta hjälper till att optimera dina frågeplaner. Om du tycker att det tar för lång tid att behålla all statistik kan du vara mer selektiv med vilka kolumner som ska ha statistik. 

Du kan även definiera frekvensen för uppdateringarna. Du kanske till exempel vill uppdatera datumkolumner, där nya värden kan läggas till, varje dag. Du får ut mest genom att använda statistik med kolumner som ingår i kopplingar, kolumner som används i WHERE-satsen och kolumner som finns i GROUP BY.

Läs mer om [statistik].

## <a name="resource-class"></a>Resursklass
SQL Data Warehouse använder resursgrupper som ett sätt att allokera minne till frågor. Om du behöver mer minne för att förbättra hastigheten för frågor eller inläsning ska du allokera högre resursklasser. Å andra sidan påverkar användning av större klasser samtidigheten. Du bör överväga det innan du flyttar alla dina användare till en stor resursklass.

Om du märker att frågor tar för lång tid kan du kontrollera att dina användare inte körs i stora resursklasser. Stora resursklasser förbrukar många samtidighetsfack. De kan orsaka att andra frågor placeras i kö.

När Gen2 används för SQL Data Warehouse får varje resursklass 2,5 gånger så mycket minne som Gen1.

Lär dig mer om hur du arbetar med [resursklasser och samtidighet].

## <a name="lower-your-cost"></a>Sänk kostnaderna
En viktig funktion i SQL Data Warehouse är möjligheten att [hantera beräkningsresurser](sql-data-warehouse-manage-compute-overview.md). Du kan pausa informationslagret när du inte använder det, vilket gör att faktureringen för beräkningsresurser stoppas. Du kan skala resurser för att uppfylla dina prestandakrav. Om du vill pausa använder du [Azure-portalen](pause-and-resume-compute-portal.md) eller [PowerShell](pause-and-resume-compute-powershell.md). Om du vill skala använder du [Azure-portalen](quickstart-scale-compute-portal.md), [Powershell](quickstart-scale-compute-powershell.md), [T-SQL](quickstart-scale-compute-tsql.md) eller en [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Autoskala nu för den tid du önskar med Azure Functions:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

## <a name="optimize-your-architecture-for-performance"></a>Optimera din arkitektur för prestanda

Vi rekommenderar att du överväger SQL Database och Azure Analysis Services i en nav-och-eker-arkitektur. Den här lösningen kan isolera arbetsbelastningen mellan olika grupper av användare när du även använder avancerade funktioner från SQL Database och Azure Analysis Services. Det här är också ett sätt att tillhandahålla obegränsad samtidighet till dina användare.

Läs mer om [vanliga arkitekturer som använder SQL Data Warehouse](https://blogs.msdn.microsoft.com/sqlcat/20../../common-isv-application-patterns-using-azure-sql-data-warehouse/).

Distribuera dina ekrar med ett klick i SQL-databaser från SQL Data Warehouse:

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
[replikerade tabeller]:design-guidance-for-replicated-tables.md
[distribuerade tabeller]:sql-data-warehouse-tables-distribute.md

<!--MSDN references-->


<!--Other Web references-->
[typical architectures that take advantage of SQL Data Warehouse]: https://blogs.msdn.microsoft.com/sqlcat/20../../common-isv-application-patterns-using-azure-sql-data-warehouse/
[is and is not]:https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-workload-patterns-and-anti-patterns/
[datamigrering]:https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-data-to-azure-sql-data-warehouse-in-practice/

[Azure Data Lake Store]: ../data-factory/connector-azure-data-lake-store.md
[sys.dm_pdw_nodes_db_partition_stats]: /sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql
[sys.dm_pdw_request_steps]:/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql
