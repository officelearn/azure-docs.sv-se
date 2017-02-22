---
title: Hantera Azure SQL Database i Azure Portal | Microsoft Docs
description: "Snabbreferens om hur du använder Azure Portal för att hantera en relationsdatabas i molnet med Azure Portal."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 3a56e9de-c21a-40ba-9a35-958172cb4e5b
ms.service: sql-database
ms.custom: overview
ms.devlang: NA
ms.workload: data-management
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.date: 01/10/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 420b2153f6115dd712d3033e30f11f79b18cd80f
ms.openlocfilehash: be89a2799af3bdc2938f73e3d54f00f81d9ab9cd


---
# <a name="manage-azure-sql-databases-using-the-azure-portal"></a>Hantera Azure SQL Database med Azure Portal
> [!div class="op_single_selector"]
> * [Azure Portal](sql-database-manage-portal.md)
> * [SSMS](sql-database-manage-azure-ssms.md)
> * [PowerShell](sql-database-manage-powershell.md)
> 
> 

Med [Azure Portal](https://portal.azure.com/) kan du skapa, övervaka och hantera Azure SQL-databaser och -servrar. Den här artikeln innehåller en snabbgenomgång av och länkar till information om vanliga aktiviteter.

> [!TIP]
> En genomgång som visar dig hur du skapar en server, skapar en serverbaserad brandvägg, visar egenskaper, ansluter med SQL Server Management Studio, frågar huvuddatabasen, skapar en exempeldatabas och en tom databas, frågar om databasegenskaper, ansluter med SQL Server Management Studio och frågar exempeldatabasen, finns i[	Komma igång - Självstudier](sql-database-get-started.md).

## <a name="view-your-azure-sql-databases-servers-and-pools"></a>Visa dina Azure SQL-databaser, -servrar och -pooler
Om du vill visa tjänsterna som är tillgängliga i SQL Database, klickar du på **Fler tjänster** och skriver **SQL** i sökrutan:

![SQL Database](./media/sql-database-manage-portal/sql-services.png)

## <a name="how-do-i-create-or-view-azure-sql-databases"></a>Hur skapar jag eller visar Azure SQL-databaser?
Om du vill öppna bladet **SQL-databaser**, klickar du på **SQL-databaser**, och klickar sedan på den databas du vill arbeta med, eller klicka på **+Lägg till** för att skapa en SQL-databas. Mer information finns i [Skapa en SQL-databas på bara några minuter med hjälp av Azure Portal](sql-database-get-started.md).

![SQL-databaser](./media/sql-database-manage-portal/sql-databases.png)

## <a name="how-do-i-create-or-view-azure-sql-servers"></a>Hur skapar eller visar jag Azure SQL-servrar?
Om du vill öppna bladet **SQL-servrar**, klickar du på **SQL-servrar**, och klickar sedan på den server du vill arbeta med, eller klickar på **+Lägg till** för att skapa en SQL-server. Mer information finns i [Skapa en SQL-databas på bara några minuter med hjälp av Azure Portal](sql-database-get-started.md).

![SQL-servrar](./media/sql-database-manage-portal/sql-servers.png)

## <a name="how-do-i-create-or-view-sql-elastic-pools"></a>Hur skapar eller visar jag elastiska SQL-pooler?
Om du vill öppna bladet **Elastiska SQL-pooler**, klickar du på **Elastiska SQL-pooler** och klickar sedan på den programpool som du vill arbeta med, eller klickar på **+Lägg till** för att skapa en pool. Mer information finns i [Skapa en elastisk pool med Azure Portal](sql-database-elastic-pool-create-portal.md).

![Elastiska SQL-pooler](./media/sql-database-manage-portal/elastic-pools.png)

## <a name="how-do-i-update-or-view-sql-database-settings"></a>Hur uppdaterar eller visar jag SQL Database-inställningarna?
Om du vill visa eller uppdatera inställningarna för din databas, klickar du på önskad inställning på bladet SQL Database:

![Inställningar för SQL Database](./media/sql-database-manage-portal/settings.png)

## <a name="how-do-i-find-a-sql-databases-fully-qualified-server-name"></a>Hur hittar jag en SQL Database fullständiga servernamn?
Om du vill visa dina databasers servernamn klickar du på **Översikt** på bladet **SQL Database** och noterar namnet på servern:

![Inställningar för SQL Database](./media/sql-database-manage-portal/server-name.png)

## <a name="how-do-i-manage-firewall-rules-to-control-access-to-my-sql-server-and-database"></a>Hur hanterar jag brandväggsregler för att styra åtkomsten till min SQL-server och -databas?
Om du vill visa, skapa, eller uppdatera brandväggsreglerna, klickar du på **Ange serverbrandvägg** på bladet **SQL Database**. Mer information finns i [Konfigurera en brandväggsregel på servernivå för Azure SQL Database via Azure Portal](sql-database-configure-firewall-settings.md).

![brandväggsregler](./media/sql-database-manage-portal/sql-database-firewall.png)

## <a name="how-do-i-change-my-sql-database-service-tier-or-performance-level"></a>Hur kan jag ändra tjänstenivå eller prestandanivå för min SQL Database?
Om du vill uppdatera tjänstenivå eller prestandanivå på en SQL Database, klickar du på **Prisnivå (skala DTU:er)** på bladet **SQL Database**. Mer information finns i [Ändra tjänstnivå och prestandanivå (prisnivå) för en SQL Database](sql-database-scale-up.md).

![prisnivåer](./media/sql-database-manage-portal/pricing-tier.png)

## <a name="how-do-i-configure-auditing-and-threat-detection-for-a-sql-database"></a>Hur konfigurerar jag granskning och identifiering av hot för en SQL Database?
Om du vill konfigurera granskning och identifiering av hot för en SQL Database, klickar du på **Granskning och identifiering av hot** på bladet **SQL Database**. Mer information finns i [Kom igång med granskning av SQL Database](sql-database-auditing-get-started.md) och [Kom igång med hotidentifiering för SQL Database](sql-database-threat-detection-get-started.md).

## <a name="how-do-i-configure-dynamic-data-masking-for-a-sql-database"></a>Hur konfigurerar jag dynamisk datamaskering för en SQL Database?
Om du vill konfigurera dynamisk datamaskning för en SQL Database, klickar du på **Dynamisk datamaskning** på bladet **SQL Database**. Mer information finns i [Kom igång med dynamisk datamaskering för SQL Database](sql-database-dynamic-data-masking-get-started.md).

## <a name="how-do-i-configure-transparent-data-encryption-tde-for-a-sql-database"></a>Hur konfigurerar jag transparent datakryptering (TDE) för en SQL Database?
Om du vill konfigurera transparent datakryptering för en SQL Database, klickar du på **Transparent datakryptering** på bladet **SQL Database**. Mer information finns i [Aktivera TDE på en databas som använder portalen](https://msdn.microsoft.com/library/dn948096#Anchor_1).

## <a name="how-do-i-view-or-change-the-max-size-of-a-sql-database"></a>Hur visar eller ändrar jag den maximala storleken på en SQL Database?
Om du vill visa eller ändra storleken på en SQL Database, klickar du på **Databasstorlek** på bladet **SQL Database**. Uppdatera den maximala storleken för en databas genom att ändra tjänstenivån eller prestandanivån. Mer information finns i [Ändra tjänstnivå och prestandanivå (prisnivå) för en SQL Database](sql-database-scale-up.md).

## <a name="how-do-i-monitor-and-improve-the-performance-of-a-sql-database"></a>Hur övervakar och förbättrar jag prestandan för en SQL Database?
Om du vill övervaka och förbättra prestandaegenskaperna för en SQL Database, klickar du på **Prestandaöversikt** på bladet **SQL Database**. Mer information finns i [Prestandainsikt för SQL Database](sql-database-performance.md).

## <a name="how-do-i-configure-geo-replication"></a>Hur konfigurerar jag geo-replikering?
Om du vill ställa in geo-replikering för en SQL Database, klickar du på **Geo-replikering** på bladet **SQL Database**. Mer information finns i [Konfigurera geo-replikering för Azure SQL Database via Azure Portal](sql-database-geo-replication-portal.md).

## <a name="how-do-i-fail-over-to-a-geo-replicated-sql-database"></a>Hur växlar jag över till en geo-replikerad SQL Database?
Om du vill växla över till en geo-replikerad sekundär, klickar du på **Geo-replikering** på bladet **SQL Database** och klickar sedan på **Redundans**. Mer information finns i [Starta en planerad eller oplanerad redundans för Azure SQL Database via Azure Portal](sql-database-geo-replication-failover-portal.md).

## <a name="how-do-i-copy-a-sql-database"></a>Hur kopierar jag en SQL Database?
Om du vill kopiera en SQL Database, klickar du på **Kopiera** på bladet **SQL Database**. Mer information finns i [Kopiera en Azure SQL Database via Azure Portal](sql-database-copy-portal.md).

![Inställningar för SQL Database](./media/sql-database-manage-portal/sql-database-copy.png)

## <a name="how-do-i-archive-an-azure-sql-database-to-a-bacpac-file"></a>Hur arkiverar jag en Azure SQL Database till en BACPAC-fil?
Om du vill skapa en BACPAC av en SQL Database, klickar du på **Exportera** på bladet **SQL Database**. Mer information finns i [Arkivera en Azure SQL Database till en BACPAC-fil med Azure Portal](sql-database-export.md).

![SQL Database-export](./media/sql-database-manage-portal/sql-database-export.png)

## <a name="how-do-i-restore-a-sql-database-to-a-previous-point-in-time"></a>Hur återställer jag en SQL Database till en tidigare tidpunkt?
Om du vill återställa en SQL Database, klickar du på **Återställ** på bladet **SQL Database**. Mer information finns i [Återställa en Azure SQL Database till en tidigare tidpunkt via Azure Portal](sql-database-point-in-time-restore.md).

![Inställningar för SQL Database](./media/sql-database-manage-portal/sql-database-restore.png)

## <a name="how-do-i-create-an-azure-sql-database-from-a-bacpac-file"></a>Hur skapar jag en Azure SQL Database från en BACPAC-fil?
Om du vill skapa en SQL Database från en BACPAC-fil, klickar du på **Importera databas** på bladet **SQL-server**. Mer information finns i [Importera en BACPAC-fil för att skapa en Azure SQL Database](sql-database-import.md).

![SQL-server](./media/sql-database-manage-portal/server-commands.png)

## <a name="how-do-i-restore-a-deleted-sql-database"></a>Hur återställer jag en borttagen SQL Database?
Om du vill återställa en borttagen SQL Database, klickar du på **Borttagna databaser** på bladet **SQL-server** (SQL-servern som innehåller den databas som har tagits bort). Mer information finns i [Återställa en borttagen Azure SQL Database via Azure Portal](sql-database-restore-deleted-database-portal.md).

## <a name="how-do-i-delete-a-sql-database"></a>Hur tar jag bort en SQL Database?
Om du vill ta bort en SQL Database, klickar du på **Ta bort** på bladet **SQL Database**. 

![Inställningar för SQL Database](./media/sql-database-manage-portal/sql-database-delete.png)

## <a name="additional-resources"></a>Ytterligare resurser
* [SQL Database](sql-database-technical-overview.md)
* [Övervaka och hantera en elastisk pool med Azure Portal](sql-database-elastic-pool-manage-portal.md)




<!--HONumber=Dec16_HO3-->


