---
title: Använd DMS för att migrera till Azure SQL-hanterade databasinstans | Microsoft Docs
description: Lär dig hur du migrerar från SQL Server lokalt till hanterade Azure SQL Database-instans med hjälp av tjänsten Azure Database migrering.
services: dms
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 04/10/2018
ms.openlocfilehash: 6628ea218c4c7a9aacc0c2899c1ea4e5b6169b51
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="migrate-sql-server-to-azure-sql-database-managed-instance-using-dms"></a>Migrera SQL Server till Azure SQL Database hanteras-instans med DMS
Du kan använda tjänsten Azure Database migrering för att migrera databaser från en lokal SQL Server-instans till en [Azure SQL-hanterade databasinstans](../sql-database/sql-database-managed-instance.md) med nära noll avbrottstid. Ytterligare metoder som kräver vissa avbrott, se [SQL Server-instansen migrering till Azure SQL-hanterade databasinstans](../sql-database/sql-database-managed-instance-migrate.md).

I kursen får du migrerar den **Adventureworks2012** databasen från en lokal instans av SQL Server till en Azure SQL Database med hjälp av tjänsten Azure Database migrering.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Skapa en Azure-databas migrering Service-instans.
> * Skapa ett migreringsprojekt genom att använda tjänsten Azure Database migrering.
> * Kör migreringen.
> * Övervaka migreringen.

## <a name="prerequisites"></a>Förutsättningar
Den här kursen behöver du:

- Skapa ett virtuellt nätverk för tjänsten Azure Database migrering med hjälp av Azure Resource Manager distributionsmodell, som ger plats-till-plats-anslutning till din lokala källservrar genom att använda antingen [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Läs nätverkstopologier för hanterade Azure SQL DB-instans migreringar med tjänsten Azure Database migrering](https://aka.ms/dmsnetworkformi).
- Se till att din Azure virtuella nätverk (VNET) Nätverkssäkerhetsgruppen regler gör inte blockera följande meddelande portarna 443, 53, 9354, 445, 12000. Mer information om Azure VNET NSG trafikfiltrering finns i artikeln [filtrera nätverkstrafik med nätverkssäkerhetsgrupper](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg).
- Konfigurera din [Windows-brandväggen för åtkomst till källan databasmotor](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Öppna Windows-brandväggen så att tjänsten Azure databas migrering att få åtkomst till källan SQL Server som är TCP-port 1433 som standard.
- Om du använder flera namngivna SQL Server-instanser som använder dynamiska portar, kan du aktivera tjänsten SQL Browser och ge åtkomst till UDP-port 1434 genom dina brandväggar så att tjänsten Azure Database migrering kan ansluta till en namngiven instans på datakällan Server.
- Om du använder en brandväggsinstallation framför källdatabaser som du kan behöva lägga till brandväggsregler som tillåter tjänsten Azure Database migrering till databaserna källa för migrering samt filer med hjälp av SMB-port 445.
- Skapa en Azure SQL Database hanteras-instans genom att följa detaljerat i artikeln [skapa en Azure SQL Database hanteras instans i Azure portal](https://aka.ms/sqldbmi).
- Kontrollera att de inloggningar som används för att ansluta källan SQL Server och hanterade målinstans är medlemmar i serverrollen sysadmin.
- Skapa en nätverksresurs som tjänsten Azure Database migrering kan använda för att säkerhetskopiera källdatabasen.
- Kontrollera att tjänstkontot som kör SQL Server-instansen källa har skrivbehörighet för den nätverksresurs som du skapade.
- Anteckna en Windows-användare (och lösenord) som har behörigheten Fullständig behörighet för den nätverksresurs som du skapade ovan. Tjänsten Azure Database migrering personifierar användarens inloggningsuppgifter för att ladda upp säkerhetskopian till Azure storage-behållare för återställningen.
- Skapa en blob-behållare och hämta dess SAS-URI med hjälp av stegen i artikeln [hantera Azure Blob Storage-resurser med Lagringsutforskaren](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container), måste du markera alla behörigheter (läsa, skriva, ta bort, lista) i fönstret principen när Skapa SAS-URI. Detta ger tillgång till ditt konto lagringsbehållaren för uppladdning av de säkerhetskopiera filerna som används för att migrera tjänsten Azure Database migrering databaser till hanterade Azure SQL Database-instans

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registerresursleverantören Microsoft.DataMigration

1.  Logga in på Azure portal, Välj **alla tjänster**, och välj sedan **prenumerationer**.
![Visa portal prenumerationer](media\tutorial-sql-server-to-managed-instance\portal-select-subscription.png)

1.  Välj den prenumeration där du vill skapa en instans av Azure Database Migration Service och välj sedan **Resursprovidrar**.
![Visa resursprovidrar](media\tutorial-sql-server-to-managed-instance\portal-select-resource-provider.png)

1.  Sök för migreringen och sedan till höger om **Microsoft.DataMigration**väljer **registrera**.
![registerresursleverantören](media\tutorial-sql-server-to-managed-instance\portal-register-resource-provider.png)    

## <a name="create-an-azure-database-migration-service-instance"></a>Skapa en Azure-databas migrering Service-instans

1.  I Azure portal, väljer **+ skapa en resurs**, söka efter **Azure databastjänsten migrering**, och välj sedan **Azure migrering databastjänsten** från listrutan lista.

     ![Azure Marketplace](media\tutorial-sql-server-to-managed-instance\portal-marketplace.png)

1.  På den **Migreringstjänst för Azure-databasen (förhandsgranskning)** väljer **skapa**.

    ![Skapa en instans av Azure databastjänst för migrering](media\tutorial-sql-server-to-managed-instance\dms-create.png)

1.  På den **migrering databastjänsten** skärmen, ange ett namn för tjänsten, prenumerationen, resurs-grupp, ett virtuellt nätverk och prisnivå.

    Mer information om kostnader och prisnivåer finns i [sida med priser](https://aka.ms/dms-pricing). *Tjänsten Azure Database migrering är för närvarande i förhandsvisning och du debiteras inte.*

    **Nätverk:** Välj en befintlig eller skapa ett nytt virtuellt nätverk, som tillhandahåller tjänsten Azure Database migrering med åtkomst till SQL Server för källan och målet hanteras Azure SQL Database-instans. [Läs nätverkstopologier för hanterade Azure SQL DB-instans migreringar med tjänsten Azure Database migrering](https://aka.ms/dmsnetworkformi).

    Mer information om hur du skapar VNET i Azure-portalen finns [skapa ett virtuellt nätverk med flera undernät med Azure-portalen](https://aka.ms/DMSVnet).

    ![Skapa DMS-tjänsten](media\tutorial-sql-server-to-managed-instance\dms-create-service.png)

1.  Välj **skapa** att skapa tjänsten.


## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När tjänsten har skapats kan hitta den i Azure-portalen och öppna den.

1.  Välj **+ nytt migreringsprojekt**.

1.  På den **nytt migreringsprojekt** skärmen, ange ett namn för projektet i den **servertyp av datakälla** text markerar **SQL Server**, och klicka sedan på den **mål servertyp** text markerar **Azure SQL-hanterade databasinstans**.

    ![Skapa DMS-projekt](media\tutorial-sql-server-to-managed-instance\dms-create-project.png)

1.  Välj **skapa** att skapa projektet.

## <a name="specify-source-details"></a>Ange information om datakälla

1.  På den **datakällan information** skärmen, ange anslutningsinformationen för SQL Server.

    ![Information om datakälla](media\tutorial-sql-server-to-managed-instance\dms-source-details.png)

1.  Välj **spara**, och välj sedan den **Adventureworks2012** databas för migrering.

    ![Välj källa databaser](media\tutorial-sql-server-to-managed-instance\dms-source-database.png)

## <a name="specify-target-details"></a>Ange information om mål

1.  Välj **spara**, och klicka sedan på den **mål information** skärmen, ange anslutningsinformationen för mål, vilket är företablerad Azure SQL hanteras databasinstansen som den  **AdventureWorks2012** databasen kommer att migreras.

    ![Välj mål](media\tutorial-sql-server-to-managed-instance\dms-target-details.png)

1.  Välj **Spara**.

1.  På den **översikt** skärmen, granska och kontrollera informationen som är kopplade till projektet migrering.

## <a name="run-the-migration"></a>Kör migreringen

1.  Välj senast sparade projektet, Välj **+ ny aktivitet**, och välj sedan **kör migrering**.

    ![Skapa ny aktivitet](media\tutorial-sql-server-to-managed-instance\dms-create-new-activity.png)

1.  När du uppmanas att ange autentiseringsuppgifter för käll- och målservrarna och välj sedan **spara**.

1.  På den **karta till måldatabaserna** väljer du databaserna källa som du vill migrera.

    ![Välj källa databaser](media\tutorial-sql-server-to-managed-instance\dms-select-source-databases.png)

1.  Välj **spara**på den **konfigurera migreringsinställningarna** skärmen, innehåller följande information:

    | | |
    |--------|---------|
    |**Plats för säkerhetskopiering av Server** | Det lokala nätverket dela att tjänsten Azure Database migrering kan ta källan säkerhetskopiorna av databasen på. Kontot kör källa SQL Server-instansen måste ha skrivbehörighet på den här nätverksresursen. |
    |**Användarnamn** | Windows-användarnamnet att tjänsten Azure Database migrering kan personifiera och ladda upp säkerhetskopian till Azure storage-behållare för återställningen. |
    |**Lösenord** | Lösenordet ovan. |
    |**SAS-URI för lagring** | SAS-URI som tillhandahåller tjänsten Azure Database migrering med åtkomst till ditt konto lagringsbehållare som tjänsten Överför säkerhetskopiera filer och som används för att migrera databaser som ska hanteras Azure SQL Database-instans. [Lär dig att hämta SAS-URI för blob-behållaren](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    
    ![Konfigurera migreringsinställningarna](media\tutorial-sql-server-to-managed-instance\dms-configure-migration-settings.png)

1.  Välj **spara**på migreringen sammanfattning skärmen i den **aktivitetsnamn** text anger du ett namn för aktiviteten migrering.

    ![Sammanfattning av migrering](media\tutorial-sql-server-to-managed-instance\dms-migration-summary.png)


## <a name="monitor-the-migration"></a>Övervaka migreringen

1.  Välj aktiviteten migrering för att granska statusen för aktiviteten.

1.  När migreringen är klar kontrollerar du måldatabaserna på mål-hanterad Azure SQL Database-instans.

    ![Övervaka migreringen](media\tutorial-sql-server-to-managed-instance\dms-monitor-migration.png)

## <a name="next-steps"></a>Nästa steg

- En självstudiekurs visar hur du migrerar en databas till en hanterad instans med T-SQL RESTORE-kommandot finns [återställa en säkerhetskopia till en hanterad instans med kommandot restore](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- Information om hur du importerar en databas från en BACPAC fil finns [importera en BACPAC-fil till en ny Azure SQL Database](../sql-database/sql-database-import.md).
- Information om hanterade instansen finns [vad är en hanterad instans](../sql-database/sql-database-managed-instance.md).
- Information om hur du ansluter appar till en hanterad finns [ansluta program](../sql-database/sql-database-managed-instance-connect-app.md).
