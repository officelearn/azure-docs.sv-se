---
title: Cheat blad Azure SQL Data Warehouse | Microsoft Docs
description: "Hitta länkar, bästa praxis att snabbt skapa din Azure SQL Data Warehouse-lösningar."
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
ms.date: 12/14/2017
ms.author: acomet
ms.openlocfilehash: dc55f4333bef1c609978887f293539bbee8b1c29
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="cheat-sheet-for-azure-sql-data-warehouse"></a>Cheat blad för Azure SQL Data Warehouse
Den här sidan bör hjälpa dig att utforma för används huvudsakligen fall din data warehouse-lösning. Den här fusklapp ska vara en bra stöd i din resa att skapa ett världsklass data warehouse men vi rekommenderar att lära dig mer om varje steg i information. Först bör läsa den här artikeln som är bra om vilka SQL Data Warehouse  **[är och inte]**.

Följande är en ritning av den process som du bör följa när du börjar utforma SQL Data Warehouse.

![Skiss]

## <a name="queries-and-operations-across-tables"></a>Frågor och åtgärder mellan tabeller

Det är verkligen är viktigt att veta på förhand viktigaste åtgärder och frågor som äger rum i ditt data warehouse. Data warehouse-arkitektur bör prioriteras för dessa åtgärder. Exempel på åtgärder kan vara:
* Ansluta till en eller två faktatabeller med dimension tabeller, filtrering av den här tabellen för en viss tidsperiod och Lägg till resultaten i ett dataarkiv
* Gör stora eller små uppdateringar till fakta-försäljning
* Lägger endast till tabeller

Om du vet vilken typ av åtgärder hjälper dig att optimera utformningen av tabeller.

## <a name="data-migration"></a>Datamigrering

Vi rekommenderar att du först läsa in dina data  **[till ADLS]**  eller Azure Blob Storage. Sedan kan använda du Polybase att läsa in dina data till SQL Data Warehouse i en mellanlagringstabellen. Vi rekommenderar följande konfiguration:

| Designa | Rekommendation |
|:--- |:--- |
| Distribution | Resursallokering |
| Indexering | Heap |
| Partitionering | Ingen |
| Resursklass | largerc eller xlargerc |

Lär dig mer om  **[datamigrering], [datainläsning]**  med  **[djupare vägledning] på inläsning**. 

## <a name="distributed-or-replicated-tables"></a>Distribuerade eller replikerade tabeller

Vi rekommenderar följande strategier beroende på Tabellegenskaper:

| Typ | Bra val för | Kontrollerar om...|
|:--- |:--- |:--- |
| Replikerade | • Små dimensionstabeller i ett stjärnschema med mindre än 2 GB lagringsutrymme efter komprimering (~ 5 x komprimering) |• Många skriva transaktioner för tabellen (t.ex.: Infoga, upsert, ta bort, uppdatera)<br></br>• Ändra Data Warehouse enheter (DWU) etablering ofta<br></br>• Du bara använda 2 – 3 kolumner och tabellen innehåller många kolumner<br></br>• Du indexera en replikerad tabell |
| Resursallokering (standard) | • Tillfälligt/mellanlagringstabell<br></br> • Uppenbara inte ansluta till nyckeln eller så bra kandidat kolumn |• Långsam prestanda på grund av dataförflyttning |
| Hash | • Faktatabeller<br></br>• Stora dimensionstabeller |• Distributionsnyckeln kan inte uppdateras |

**Tips:**
* Börja med resursallokering men aspire för en hash-distributionsstrategi dra nytta av en massiv parallell arkitektur
* Se till att vanliga hash-nycklar har samma format
* Inte distribuera på varchar-format
* Dimensionstabeller med vanliga hash-nyckeln till en faktatabell med frekventa kopplingsåtgärder kunde hash distribueras
* Använd  *[sys.dm_pdw_nodes_db_partition_stats]*  att analysera alla snedheten i data
* Använd  *[sys.dm_pdw_request_steps]*  för att analysera data förflyttningar bakom frågor kan övervaka sändningen tid och blanda operationer. Bra att granska din distributionsstrategi för.

Lär dig mer om  **[replikerade tabeller] och [distribuerade tabeller]**.

## <a name="indexing-your-table"></a>Indexera din tabell

Indexering är **bra** för att läsa snabbt tabeller. Det finns en unik uppsättning tekniker som du kan använda baserat på dina behov:

| Typ | Bra val för | Kontrollerar om...|
|:--- |:--- |:--- |
| Heap | • Mellanlagring/tillfällig tabell<br></br>• Små tabeller med små sökningar |• En sökning söker igenom hela tabellen |
| Grupperat Index | • Upp till 100 m rader tabell<br></br>• Stora tabeller (fler än 100-m rader) med endast 1 – 2 används kraftigt |• Som används i en replikerad tabell<br></br>• Komplexa frågor som rör sammanfogning, Group By operations<br></br>• Se uppdateringar i de fulltextindexerade kolumnerna, det tar minne |
| Grupperade Columnstore-indexet (CCI) (standard) | • Stora tabeller (fler än 100-m rader) | • Som används i en replikerad tabell<br></br>• Du gör omfattande uppdateringsåtgärder på tabellen<br></br>• Över partitionera tabellen: radgrupper omfattar inte över en annan distributionsplats noder och partitioner |

**Tips:**
* Du kanske vill lägga till icke-grupperat Index i en kolumn som kraftigt används för filter ovanpå ett grupperat Index. 
* Var noga med hur du hanterar minne på en tabell med CCI. När du läser in data som du vill användaren (eller frågan) för att dra nytta av en stor resursklassen. Du ser till att undvika trimning och skapa många små komprimerade radgrupper
* Optimerad för att beräkna nivån klippor med CCI
* CCI, dåliga prestanda kan inträffa på grund av dålig komprimering av din radgrupper du kanske vill återskapa eller ordna om din CCI. Vill du minst 100 k rader per komprimerade radgrupper. Perfekt är 1-m rader i en grupp.
* Utifrån inkrementell belastningen frekvens och storlek som du vill automatisera när du vill ordna om eller återskapa ditt index. Rensa värdet är alltid bra.
* Vara strategiska när du vill ta bort en grupp: hur stora är öppna radgrupper? Hur mycket data du vill läsa in under de kommande dagarna?

Lär dig mer om  **[index]**.

## <a name="partitioning"></a>Partitionering
Du kan också partitionera tabellen när du har en stor faktatabeller (> 1B raden tabell). 99% i fall Partitionsnyckeln ska baseras på datum. Var noga med att inte över partition, särskilt när du har ett grupperat Columnstore-Index.
Med mellanlagringstabeller som kräver ETL, kan du dra nytta partitionering. Det underlättar Livscykelhantering för data.
Var noga med att inte overpartition dina data, särskilt på ett grupperat Columnstore-Index.

Lär dig mer om  **[partitioner]**.

## <a name="incremental-load"></a>Inkrementell belastning

Först bör du kontrollera att du allokera större resursklasser för att läsa in data. Du rekommenderas att använda Polybase och ADF V2 för att automatisera ETL-pipelines till SQL DW.

För en stor grupp med uppdateringar i din historiska data rekommenderar vi ta först bort berörda data. Därefter kan du skapa en bulk insert av nya data. Den här metoden i steg 2 är effektivare.

## <a name="maintain-statistics"></a>Underhålla statistik
Automatisk statistik ska vara allmänt tillgänglig snart. Fram till dess kan kräver SQL Data Warehouse manuell underhåll av statistik. Det är viktigt att uppdatera statistik som **betydande** ändringar som sker till dina data. Detta hjälper till att optimera din frågeplaner. Om du hittar det tar för lång tid att behålla alla dina statistik kan du vill ska vara mer selektiv om vilka kolumner har statistik. Du kan också definiera frekvensen av uppdateringarna. Du kanske till exempel vill uppdatera datumkolumner, där nya värden kan läggas till, varje dag. Du får mest nytta av med statistik om kolumner som ingår i kopplingar, används i WHERE-satsen och kolumner i GROUP BY.

Lär dig mer om  **[statistik]**.

## <a name="resource-class"></a>Resursklass
SQL Data Warehouse använder resursgrupper som ett sätt att allokera minne till frågor. Om du behöver mer minne för att förbättra frågan eller läsa in hastighet, bör du allokera högre resurs klasserna. På sidan Vänd med större resursklasser påverkar samtidighet. Vill du ta med i beräkningen innan du flyttar alla användare till en stor resursklassen.

Om du märker att frågor tar för lång tid, kan du kontrollera att användarna inte kör i stora resursklasser. Stora resursklasser använda många samtidiga platser. De kan orsaka andra frågor till kö.

Slutligen om du använder Compute optimerade nivån hämtar varje resursklassen 2,5 x mer minne än på den elastiska optimerade nivån.

Lär dig mer om hur du arbetar med  **[resursklasser och samtidighet]**.

## <a name="lower-your-cost"></a>Lägre dina kostnader
En viktig funktion i SQL Data Warehouse är möjligheten att kunna pausa tjänsten när du inte använder den, vilket gör att faktureringen för beräkningsresurser stoppas. En annan viktig funktion är möjligheten att skala resurser. Du kan pausa och skala via Azure Portal eller med hjälp av PowerShell-kommandon.

Autoskala nu när du vill med Azure Functions:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

## <a name="optimize-you-architecture-for-performance"></a>Optimera arkitektur för prestanda

Vi rekommenderar att SQL database och Azure Analysis Services i en arkitektur med nav och ekrar. Lösningen kan isolera arbetsbelastningen mellan olika grupper av användare medan utnyttja även vissa avancerade säkerhetsfunktioner från SLQ DB och Azure Analysis Services. Detta är ett sätt att förse användarna obegränsad samtidighet.

Lär dig mer om  **[vanliga arkitekturer som utnyttjar SQL DW]**.

Distribuera i en klickar du på din ekrar i SQL DB-databaser från SQL DW:

<a href="https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwSpokeDbTemplate%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>


<!--Image references-->
[Skiss]:media/sql-data-warehouse-cheat-sheet/picture-flow.png

<!--Article references-->
[datainläsning]:./design-elt-data-loading.md
[djupare vägledning]: ./guidance-for-loading-data.md
[index]:./sql-data-warehouse-tables-index.md
[partitioner]:./sql-data-warehouse-tables-partition.md
[statistik]:./sql-data-warehouse-tables-statistics.md
[resursklasser och samtidighet]:./sql-data-warehouse-develop-concurrency.md

<!--MSDN references-->


<!--Other Web references-->
[vanliga arkitekturer som utnyttjar SQL DW]: https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/common-isv-application-patterns-using-azure-sql-data-warehouse/
[är och inte]:https://blogs.msdn.microsoft.com/sqlcat/2017/09/05/azure-sql-data-warehouse-workload-patterns-and-anti-patterns/
[datamigrering]:https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/
[replikerade tabeller]:https://docs.microsoft.com/en-us/azure/sql-data-warehouse/design-guidance-for-replicated-tables
[distribuerade tabeller]:https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute
[till ADLS]: https://docs.microsoft.com/en-us/azure/data-factory/connector-azure-data-lake-store
[sys.dm_pdw_nodes_db_partition_stats]: https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-db-partition-stats-transact-sql
[sys.dm_pdw_request_steps]:https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql
