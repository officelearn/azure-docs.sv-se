---
title: Migrering av SQL Server-databas till Azure SQL Database | Microsoft Docs
description: "Lär dig migrering av SQL Server-databas till Azure SQL Database i molnet."
keywords: databasmigrering, sql server-databasmigrering, databasmigreringsverktyg, migrera databas, migrera sql-databas
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: migrate
ms.topic: article
ms.date: 03/16/2018
ms.author: carlrab
ms.openlocfilehash: 59ee56e225623295dd63bf5ae303bfe1aa8e95cf
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="sql-server-database-migration-to-azure-sql-database"></a>Migrering av SQL Server-databas till Azure SQL Database

I den här artikeln får information du om metoder för att migrera en SQL Server 2005 eller senare databas till en enda eller grupperade databas i Azure SQL Database. Information om hur du migrerar till en hanterad instans finns [migrera till SQL Server-instansen till Azure SQL Database hanteras-instans (förhandsgranskning)](sql-database-managed-instance-migrate.md). 

## <a name="migrate-to-a-single-database-or-a-pooled-database"></a>Migrera till en enskild databas eller en delad databas
Det finns två metoder för att migrera en SQL Server 2005 eller senare databas till en enda eller grupperade databas i Azure SQL Database. Den första metoden är enklare, men kräver viss till omfattande stilleståndstid under migreringen. Den andra metoden är mer komplicerad, men kräver mycket kortare stilleståndstid under migreringen.

I båda fallen måste du säkerställa att källdatabasen är kompatibelt med Azure SQL Database med hjälp av den [Data migrering Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). SQL Database V12 närmar sig [har paritet](sql-database-features.md) med SQL Server än problem relaterade till servernivå och flera databaser. Databaser och program som förlitar sig på [funktioner som delvis eller inte stöds](sql-database-transact-sql-information.md) behöver viss [omkonstruktion för att åtgärda dessa inkompatibiliteter](sql-database-cloud-migrate.md#resolving-database-migration-compatibility-issues) innan SQL Server-databasen kan migreras.

> [!NOTE]
> För att migrera en icke-SQL Server-databas, inklusive Microsoft Access, Sybase, MySQL Oracle och DB2 till Azure SQL Database, se [SQL Server-migreringsassistent](https://blogs.msdn.microsoft.com/datamigration/2017/09/29/release-sql-server-migration-assistant-ssma-v7-6/).
> 

### <a name="method-1-migration-with-downtime-during-the-migration"></a>Metod 1: Migrering med stilleståndstid under migreringen

 Använd den här metoden för att migrera till en enda eller en delad databas om du har råd vissa avbrott eller om du utför en testmigrering av en produktionsdatabas för senare migrering. En självstudiekurs finns [migrera en SQL Server-databas](sql-database-migrate-your-sql-server-database.md).

Följande lista innehåller det allmänna arbetsflödet för migrering av en enda SQL Server-databas eller en delad databas med den här metoden. Migreringen till instans hanteras finns [migrering till en hanterad](sql-database-managed-instance-migrate.md).

  ![VSSSDT-migreringsdiagram](./media/sql-database-cloud-migrate/azure-sql-migration-sql-db.png)

1. [Utvärdera](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) databasen för kompatibilitet med hjälp av den senaste versionen av den [Data migrering Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).
2. Förbered nödvändiga korrigeringar som Transact-SQL-skript.
3. Källdatabasen migreras transaktionellt konsekvent kopiera eller stoppa nya transaktioner från att ske i källdatabasen medan migreringen pågår. Metoder för att göra det sista alternativet är inaktivering klientanslutning eller skapa en [databasögonblicksbilden](https://msdn.microsoft.com/library/ms175876.aspx). Efter migreringen du kan använda Transaktionsreplikering uppdatera migrerade databaserna med ändringar som inträffar efter den sista punkten för migreringen. Se [migrera med hjälp av Transaktionsreplikering migrering](sql-database-cloud-migrate.md#method-2-use-transactional-replication).  
4. Distribuera Transact-SQL-skripten för att tillämpa korrigeringar på databaskopian.
5. [Migrera](https://docs.microsoft.com/sql/dma/dma-migrateonpremsql) databaskopian till en ny Azure SQL-databas med hjälp av Data Migration Assistant.

> [!NOTE]
> Du kan också använda en BACPAC-fil i stället för med DMA. Se [importera en BACPAC-fil till en ny Azure SQL Database](sql-database-import.md).

### <a name="optimizing-data-transfer-performance-during-migration"></a>Optimera prestanda för dataöverföring under migreringen 

Följande lista innehåller rekommendationer för bästa prestanda under importen.

* Välj den högsta tjänstnivån och prestandanivån som din budget tillåter för att maximera överföringsprestanda. Du kan spara pengar genom att skala ned när migreringen är klar. 
* Minska avståndet mellan din BACPAC fil- och mål-datacenter.
* Inaktivera automatisk statistik under migreringen
* Partitionstabeller och index
* Ta bort indexerade vyer och återskapa dem när de är klara
* Ta bort historiska data som sällan efterfrågas till en annan databas och migrera historiska data till en separat Azure SQL Database. Du kan sedan söka i historiska data med [elastiska frågor](sql-database-elastic-query-overview.md).

### <a name="optimize-performance-after-the-migration-completes"></a>Optimera prestanda när migreringen är klar

[Uppdatera statistik](https://msdn.microsoft.com/library/ms187348.aspx) med fullständig sökning när migreringen har slutförts.

### <a name="method-2-use-transactional-replication"></a>Metod 2: Använd transaktionsreplikering

Om du inte har råd att ta bort SQL Server-databasen från produktionen medan migreringen genomförs kan du använda transaktionsreplikering i SQL Server som migreringslösning. För att kunna använda den här metoden måste källdatabasen uppfylla [kraven för transaktionsreplikering](https://msdn.microsoft.com/library/mt589530.aspx) och vara kompatibel med Azure SQL Database. Information om SQL-replikeringen med AlwaysOn finns [konfigurerar replikering för Always On-Tillgänglighetsgrupper (SQL Server)](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server).

När du använder den här lösningen konfigurerar du Azure SQL Database som prenumerant på den SQL Server-instans som du vill migrera. Distributören av transaktionsreplikeringen synkroniserar de data som ska synkroniseras från databasen (utgivaren) medan nya transaktioner fortsätter att göras. 

Vid en transaktionsreplikering visas alla ändringar i dina data eller i ditt schema i Azure SQL Database. När synkroniseringen är klar och du är redo att migrera ändrar du anslutningssträngen för dina program så att de pekar på din Azure SQL Database. När transaktionsreplikeringen tömt alla ändringar som finns kvar i källdatabasen och alla program pekar på Azure DB kan du avinstallera transaktionsreplikeringen. Nu är Azure SQL Database ditt produktionssystem.

 ![SeedCloudTR-diagram](./media/sql-database-cloud-migrate/SeedCloudTR.png)

> [!TIP]
> Du kan också använda transaktionsreplikering till att migrera en del av din källdatabas. Den publikation som du replikerar till Azure SQL Database kan begränsas till en del av tabellerna i databasen som replikeras. Du kan begränsa data till en del av raderna och/eller en del av kolumnerna för varje tabell som replikeras.
>

### <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>Migrera till SQL Database med arbetsflödet för transaktionsreplikering

> [!IMPORTANT]
> Använd den senaste versionen av SQL Server Management Studio för att behålla synkroniseringen med uppdateringar av Microsoft Azure och SQL Database. Äldre versioner av SQL Server Management Studio kan inte konfigurera SQL Database som en prenumerant. [Uppdatera SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 

1. Konfigurera distribution
   -  [Med SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_1)
   -  [Med Transact-SQL](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_2)
2. Skapa publikation
   -  [Med SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_1)
   -  [Med Transact-SQL](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_2)
3. Skapa en prenumeration
   -  [Med SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_0)
   -  [Med Transact-SQL](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_1)

Tips och skillnader vid migrering till SQL Database

- Använda en lokal distributör 
   - Detta gör att påverka prestanda på servern. 
   - Om denna påverkan inte är acceptabel kan du använda en annan server men det innebär en mer komplicerad hantering och administration.
- När du väljer en mapp för ögonblicksbilder måste du se till att mappen är tillräckligt stor för att innehålla en BCP för varje tabell som du vill replikera. 
- När en ögonblicksbild skapas låses de associerade tabellerna tills den är klar. Se därför till att schemalägga ögonblicksbilden vid en lämplig tidpunkt. 
- Endast push-prenumerationer stöds i Azure SQL Database. Du kan bara lägga till prenumeranter från källdatabasen.

### <a name="resolving-database-migration-compatibility-issues"></a>Åtgärda kompatibilitetsproblem vid databasmigrering
Det finns en mängd olika kompatibilitetsproblem som kan uppstå, beroende på både versionen av SQL Server i källdatabasen och komplexiteten i databasen som du migrerar. Äldre versioner av SQL Server har fler kompatibilitetsproblem. Använd följande resurser, utöver en riktad Internetsökning med hjälp av sökmotor:

* [SQL Server-databasfunktioner som inte stöds i Azure SQL Database](sql-database-transact-sql-information.md)
* [Utgångna databasmotorfunktioner i SQL Server 2016](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
* [Utgångna databasmotorfunktioner i SQL Server 2014](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
* [Utgångna databasmotorfunktioner i SQL Server 2012](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
* [Utgångna databasmotorfunktioner i SQL Server 2008 R2](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
* [Utgångna databasmotorfunktioner i SQL Server 2005](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Utöver att söka på Internet och använda dessa resurser kan du använda [MSDN SQL Server community-forumen](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) eller [StackOverflow](http://stackoverflow.com/).

> [!IMPORTANT]
> SQL-hanterade databasinstans kan du migrera en befintlig SQL Server-instans och dess databaser med minimal att inga kompatibilitetsproblem. Se [vad är en hanterad instans](sql-database-managed-instance.md).


## <a name="next-steps"></a>Nästa steg
* Använd skriptet i Azure SQL EMEA Engineers-bloggen för att [övervaka tempdb-användningen vid migrering](https://blogs.msdn.microsoft.com/azuresqlemea/2016/12/28/lesson-learned-10-monitoring-tempdb-usage/) (på engelska).
* Använd skriptet i Azure SQL EMEA Engineers-bloggen för att [övervaka transaktionsloggutrymmet i databasen medan migreringen pågår](https://blogs.msdn.microsoft.com/azuresqlemea/2016/10/31/lesson-learned-7-monitoring-the-transaction-log-space-of-my-database/0) (på engelska).
* En SQL Server Customer Advisory Team-blogg om migrering med BACPAC-filer finns i [Migrera från SQL Server till Azure SQL Database med BACPAC-filer](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (på engelska).
* Information om hur du arbetar med UTC-tid efter migreringen finns i [Ändra standardtidszon för den lokala tidszonen](https://blogs.msdn.microsoft.com/azuresqlemea/2016/07/27/lesson-learned-4-modifying-the-default-time-zone-for-your-local-time-zone/) (på engelska).
* Information om hur du ändrar standardspråk för en databas efter migreringen finns [Ändra standardspråk för Azure SQL Database](https://blogs.msdn.microsoft.com/azuresqlemea/2017/01/13/lesson-learned-16-how-to-change-the-default-language-of-azure-sql-database/) (på engelska).


