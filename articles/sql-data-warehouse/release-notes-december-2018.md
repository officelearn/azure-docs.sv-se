---
title: Azure SQL Data Warehouse viktig December 2018 | Microsoft Docs
description: Viktig information för Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 12/12/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: b897b50edf4d5a7eeabacc6da1505e165f2bb21a
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54431749"
---
# <a name="whats-new-in-azure-sql-data-warehouse-december-2018"></a>Vad är nytt i Azure SQL Data Warehouse? December 2018
Azure SQL Data Warehouse tar emot förbättringar kontinuerligt. Den här artikeln beskriver nya funktioner och ändringar som har införts i December 2018.

## <a name="virtual-network-service-endpoints-generally-available"></a>Tjänstslutpunkter i virtuella nätverk är nu allmänt tillgänglig
Den här versionen innehåller allmän tillgänglighet för tjänstslutpunkter i virtuella nätverk (VNet) för Azure SQL Data Warehouse i alla Azure-regioner. Tjänstslutpunkter i virtuella nätverk kan du isolera anslutning till din logiska server från en viss undernät eller uppsättningar av undernät i det virtuella nätverket. Trafik till Azure SQL Data Warehouse från ditt VNet alltid stannar kvar i Azure-stamnätverket. Den här vägen är prioriterade över några specifika vägar som tar Internet-trafik via virtuella installationer eller lokalt. Inga ytterligare fakturering debiteras för åtkomst till virtuella nätverket via tjänstslutpunkter. Aktuella Prismodell för [Azure SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/) gäller.

Med den här versionen har vi också aktiverat PolyBase anslutning till [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) (ADLS) via [Azure Blob-Filssystemet](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-abfs-driver) (ABFS)-drivrutinen. Azure Data Lake Storage Gen2 ger alla egenskaper som krävs för fullständig livscykeln för analytics-data till Azure Storage. Funktioner i två befintliga Azure storage-tjänster, Azure Blob Storage och Azure Data Lake Storage Gen1 går samman. Funktioner från [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), till exempel filsystemen, filen säkerhet på radnivå och skala kombineras med låg kostnad, nivåindelad lagring och hög tillgänglighet/haveriberedskap från [ Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). 

Med Polybase importera du även data till Azure SQL Data Warehouse från Azure Storage som skyddas till virtuellt nätverk. På samma sätt kan stöds exportera data från Azure SQL Data Warehouse till Azure Storage skyddade virtuella nätverket också via Polybase. 

Mer information om VNet-tjänstslutpunkter i Azure SQL Data Warehouse finns i den [blogginlägget](https://azure.microsoft.com/blog/general-availability-of-vnet-service-endpoints-for-azure-sql-data-warehouse/) eller [dokumentation](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json).

## <a name="automatic-performance-monitoring-preview"></a>Automatisk prestandaövervakning (förhandsversion)
[Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?view=sql-server-2017) är nu tillgängligt i förhandsversionen av Azure SQL Data Warehouse. Query Store har utformats för att hjälpa dig med frågeprestanda felsökning genom att spåra frågor, frågeplaner, körningsstatistik och fråga historik för att övervaka aktivitet och prestanda för ditt informationslager. Query Store är en uppsättning interna butiker och dynamiska hanteringsvyer (DMV) så att du kan:

- Identifiera och finjustera översta resurskrävande frågor
- Identifiera och förbättra ad hoc-arbetsbelastningar
- Utvärdera prestanda för frågor och att planen påverkas av ändringar i statistik eller index systemstorlek (DWU-inställning)
- Se fullständig frågetexten för alla frågor som körs

Query Store innehåller tre faktiska butiker:  
- En plan butik för att spara information om körning-plan för 
- En runtime stats butik för att spara informationen om körningen statistik för
- En vänta stats butik för att spara för vänta stats information. 

SQL Data Warehouse hanterar automatiskt dessa lager och ange ett obegränsat antal frågor som storied under de senaste sju dagarna utan extra kostnad. Att aktivera Query Store är lika enkelt som att köra ALTER DATABASE T-SQL-instruktion:

```sql
ALTER DATABASE [Database Name] SET QUERY_STORE = ON;
```
Mer information om Query Store i Azure SQL Data Warehouse finns i artikeln [övervakning av prestanda med hjälp av Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?view=sql-server-2017), och DMV: er för Query Store som [sys.query_store_query](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-query-transact-sql?view=sql-server-2017). Här är den [blogginlägget](https://azure.microsoft.com/blog/automatic-performance-monitoring-in-azure-sql-data-warehouse-with-query-store/) Vi presenterar versionen.

## <a name="lower-compute-tiers-for-azure-sql-data-warehouse-gen2"></a>Lägre Beräkningsnivåer för Azure SQL Data Warehouse Gen2
Azure SQL Data Warehouse Gen2 har nu stöd för lägre beräkningsnivåer. Kunder kan uppleva Azure SQL Data Warehouse branschledande prestanda, flexibiliteten och säkerhetsfunktioner som börjar med 100 cDWU ([Informationslagerenheter](https://docs.microsoft.com/azure/sql-data-warehouse/what-is-a-data-warehouse-unit-dwu-cdwu)) och skala till 30 000 cDWU på några minuter. Från mitten av December 2018, kan kunder dra nytta av Gen2 prestanda och flexibilitet med lägre compute nivåer i [regioner](https://docs.microsoft.com/azure/sql-data-warehouse/gen2-lower-tier-regions), med resten av regionerna som är tillgängliga under 2019.

Genom att släppa startpunkt för nästa generation av informationslager, öppnas Microsoft dörrar till värdet-drivna kunder som vill utvärdera alla fördelar med en säker, högpresterande data warehouse utan att gissa vilka utvärderingsmiljö är bäst för dessa. Kunder kan börja så lågt som 100 cDWU ned från den aktuella 500 cDWU startpunkten. SQL Data Warehouse Gen2 fortsätter att stödja pausa och återuppta drift- och går utöver bara flexibilitet vid beräkning. Gen2 också har stöd för obegränsad columnstore lagringskapacitet tillsammans med 2,5 gånger mer minne per fråga, upp till 128 samtidiga frågor och [Adaptiv cachelagring](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/) funktioner. Dessa funktioner i genomsnitt få fem gånger så mycket prestanda jämfört med samma Data Warehouse-enhet på Gen1 till samma pris. GEO-redundanta säkerhetskopieringar är standard för Gen2 med inbyggda garanterad dataskydd. Azure SQL Data Warehouse Gen2 är redo att skala när du är.

## <a name="columnstore-background-merge"></a>Columnstore bakgrund Merge
Som standard lagrar Azure SQL Data Warehouse (Azure SQL DW) data i kolumnformat, micro-partitioner kallas [radgrupper](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-memory-optimizations-for-columnstore-compression). Ibland, på grund av minne som avgränsar vid indexet bygge eller data kan du läsa in tid, i radgrupper kan komprimeras med mindre än optimala storleken på en miljon rader. Radgrupper kan också bli fragmenterad på grund av borttagningar. Små eller fragmenterade radgrupper leda till högre minnesförbrukning, samt ineffektiva Frågekörningen. Den här versionen av Azure SQL DW sammanfogar columnstore Underhåll bakgrundsaktiviteten små komprimerade radgrupper för att skapa större radgrupper för att bättre utnyttja minne och snabba upp Frågekörningen.

## <a name="next-steps"></a>Nästa steg
Nu när du vet lite om SQL Data Warehouse, lär du dig hur du snabbt [skapa ett SQL Data Warehouse][create a SQL Data Warehouse]. Om du är nybörjare på Azure kan du upptäcka det [Azure-ordlistan] [ Azure glossary] till hjälp när du lär dig hur ny terminologi. Eller så kan du se över några av de övriga SQL Data Warehouse-resurserna.  

* [Kundernas framgångsberättelser]
* [Bloggar]
* [Funktionsbegäranden]
* [Videoklipp]
* [Customer Advisory Team-bloggar]
* [Stack Overflow-forum]
* [Twitter]


[Bloggar]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team-bloggar]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Kundernas framgångsberättelser]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Funktionsbegäranden]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Stack Overflow-forum]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videoklipp]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
