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
ms.date: 05/07/2018
ms.openlocfilehash: 86af0101d84fe9cd44211a931567a85d7b5166e0
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261618"
---
# <a name="migrate-sql-server-to-azure-sql-database-managed-instance-using-dms"></a>Migrera SQL Server till Azure SQL Database hanteras-instans med DMS
Du kan använda tjänsten Azure Database migrering för att migrera databaser från en lokal SQL Server-instans till en [Azure SQL-hanterade databasinstans](../sql-database/sql-database-managed-instance.md). Ytterligare metoder som kan kräva att vissa manuellt arbete finns i artikel [SQL Server-instansen migrering till Azure SQL-hanterade databasinstans](../sql-database/sql-database-managed-instance-migrate.md).

> [!IMPORTANT]
> Migrering projekt från SQL Server till hanterade Azure SQL Database-instans är i förhandsvisning och föremål för den [kompletterande användningsvillkor för Microsoft Azure-förhandsgranskningar](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I kursen får du migrerar den **Adventureworks2012** databasen från en lokal instans av SQL Server till en Azure SQL-hanterade databasinstans genom att använda tjänsten Azure Database migrering.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Skapa en instans av tjänsten Azure Database migrering.
> * Skapa ett migreringsprojekt genom att använda tjänsten Azure Database migrering.
> * Kör migreringen.
> * Övervaka migreringen.
> * Hämta en rapport migrering.

## <a name="prerequisites"></a>Förutsättningar
Den här kursen behöver du:

- Skapa ett virtuellt nätverk för tjänsten Azure Database migrering med hjälp av Azure Resource Manager distributionsmodell, som ger plats-till-plats-anslutning till din lokala källservrar genom att använda antingen [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Läs nätverkstopologier för hanterade Azure SQL DB-instans migreringar med tjänsten Azure Database migrering](https://aka.ms/dmsnetworkformi).
- Se till att din Azure virtuella nätverk (VNET) Nätverkssäkerhetsgruppen regler gör inte blockera följande meddelande portarna 443, 53, 9354, 445, 12000. Mer information om Azure VNET NSG trafikfiltrering finns i artikeln [filtrera nätverkstrafik med nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Konfigurera din [Windows-brandväggen för åtkomst till källan databasmotor](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Öppna Windows-brandväggen så att tjänsten Azure databas migrering att få åtkomst till källan SQL Server som är TCP-port 1433 som standard.
- Om du använder flera namngivna SQL Server-instanser som använder dynamiska portar, kan du aktivera tjänsten SQL Browser och ge åtkomst till UDP-port 1434 genom dina brandväggar så att tjänsten Azure Database migrering kan ansluta till en namngiven instans på datakällan Server.
- Om du använder en brandväggsinstallation framför källdatabaser som du kan behöva lägga till brandväggsregler som tillåter tjänsten Azure Database migrering till databaserna källa för migrering samt filer med hjälp av SMB-port 445.
- Skapa en Azure SQL Database hanteras-instans genom att följa detaljerat i artikeln [skapa en Azure SQL Database hanteras instans i Azure portal](https://aka.ms/sqldbmi).
- Kontrollera att de inloggningar som används för att ansluta källan SQL Server och hanterade målinstans är medlemmar i serverrollen sysadmin.
- Skapa en nätverksresurs som tjänsten Azure Database migrering kan använda för att säkerhetskopiera källdatabasen.
- Kontrollera att kontot kör källan SQL Server-instansen har skrivbehörighet för den nätverksresurs som du skapade och att datorkontot för källservern har läs-/ skrivåtkomst till samma resurs.
- Anteckna en Windows-användare (och lösenord) som har behörigheten Fullständig behörighet för den nätverksresurs som du skapade tidigare. Tjänsten Azure Database migrering personifierar användarens inloggningsuppgifter för att ladda upp säkerhetskopian till Azure storage-behållare för återställningen.
- Skapa en blob-behållare och hämta dess SAS-URI med hjälp av stegen i artikeln [hantera Azure Blob Storage-resurser med Lagringsutforskaren](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container), måste du markera alla behörigheter (läsa, skriva, ta bort, lista) i fönstret principen när Skapa SAS-URI. Denna information innehåller tjänsten Azure Database migrering med åtkomst till ditt konto lagringsbehållaren för uppladdning av de säkerhetskopiera filerna som används för att migrera databaser till hanterade Azure SQL Database-instans

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registerresursleverantören Microsoft.DataMigration

1. Logga in på Azure portal, Välj **alla tjänster**, och välj sedan **prenumerationer**.

    ![Visa portal prenumerationer](media\tutorial-sql-server-to-managed-instance\portal-select-subscriptions.png)        

2. Välj den prenumeration där du vill skapa en instans av Azure Database Migration Service och välj sedan **Resursprovidrar**.

    ![Visa resursprovidrar](media\tutorial-sql-server-to-managed-instance\portal-select-resource-provider.png)

3. Sök för migreringen och sedan till höger om **Microsoft.DataMigration**väljer **registrera**.

    ![Registrera resursprovider](media\tutorial-sql-server-to-managed-instance\portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>Skapa en Azure-databas migrering Service-instans

1. I Azure portal, väljer + **skapar du en resurs**, söka efter **Azure migrering databastjänsten**, och välj sedan **Azure databastjänsten migrering** från listrutan lista.

     ![Azure Marketplace](media\tutorial-sql-server-to-managed-instance\portal-marketplace.png)

2. På den **Azure migrering databastjänsten** väljer **skapa**.

    ![Skapa en instans av Azure databastjänst för migrering](media\tutorial-sql-server-to-managed-instance\dms-create1.png)

3. På den **skapa Migreringstjänst** skärmen, ange ett namn för tjänsten, prenumerationen och en ny eller befintlig resursgrupp.

4. Välj ett befintligt virtuellt nätverk (VNET) eller skapa en.
 
    VNET tillhandahåller tjänsten Azure Database migrering med åtkomst till SQL Server för källan och målet hanteras Azure SQL Database-instans.

    Mer information om hur du skapar ett VNET i Azure-portalen finns i artikeln [skapa ett virtuellt nätverk med Azure-portalen](https://aka.ms/DMSVnet).

    Mer information finns i artikeln [nätverk topologier för Azure SQL DB hanteras instans migreringar med tjänsten Azure Database migrering](https://aka.ms/dmsnetworkformi).

5. Välj en prisnivå.

    Mer information om kostnader och prisnivåer finns i [sida med priser](https://aka.ms/dms-pricing).
   
    ![Skapa DMS-tjänsten](media\tutorial-sql-server-to-managed-instance\dms-create-service1.png)

6.  Välj **skapa** att skapa tjänsten.

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När tjänsten har skapats kan hitta den i Azure-portalen, öppna den och sedan skapa ett nytt migreringsprojekt.

1. Välj i Azure-portalen **alla tjänster**, söka efter Migreringstjänst för Azure-databasen och välj sedan **Azure databastjänster migrering**.

    ![Leta upp alla instanser av tjänsten Azure Database migrering](media\tutorial-sql-server-to-azure-sql\dms-search.png)

2. På den **Azure migrering databastjänsten skärmen**, Sök efter namnet på den instans som du skapat och välj sedan instansen.
 
3. Välj + **nytt migreringsprojekt**.

4. På den **nytt migreringsprojekt** skärmen, ange ett namn för projektet i den **servertyp av datakälla** text markerar **SQL Server**, och klicka sedan på den **mål servertyp** text markerar **Azure SQL-hanterade databasinstans**.

   ![Skapa DMS-projekt](media\tutorial-sql-server-to-managed-instance\dms-create-project1.png)

5. Välj **skapa** att skapa projektet.

## <a name="specify-source-details"></a>Ange information om datakälla

1. På den **datakällan information** skärmen, ange anslutningsinformationen för SQL Server.

2. Om du inte har installerat ett betrott certifikat på servern, väljer du den **förtroende servercertifikat** kryssrutan.

    När ett betrott certifikat inte är installerat genererar SQL Server ett självsignerat certifikat när instansen har startats. Det här certifikatet används för att kryptera autentiseringsuppgifterna för klientanslutningar.

    > [!CAUTION]
    > SSL-anslutningar som är krypterade med ett självsignerat certifikat ger inte någon hög säkerhet. De kan man-in-the-middle-attacker. Du bör inte lita på SSL använder självsignerade certifikat i en produktionsmiljö eller servrar som är anslutna till internet.

   ![Information om datakälla](media\tutorial-sql-server-to-managed-instance\dms-source-details1.png)

3. Välj **Spara**.

4. På den **Välj källa databaser** väljer du den **Adventureworks2012** databas för migrering.

   ![Välj källa databaser](media\tutorial-sql-server-to-managed-instance\dms-source-database1.png)

5. Välj **Spara**.

## <a name="specify-target-details"></a>Ange information om mål

1.  På den **mål information** skärmen, ange anslutningsinformationen för mål, vilket är företablerad Azure SQL hanteras databasinstansen som den **AdventureWorks2012** databasen kommer att vara migreras.

    Om du inte redan har etablerat Azure SQL-hanterade databasinstans väljer **nr** för en länk som hjälper dig att etablera instansen. Du kan ändå fortsätta med skapa projektet och sedan när Azure SQL-hanterade databasinstans är klar, återgår till specifika projektet att utföra migreringen.   
 
       ![Välj mål](media\tutorial-sql-server-to-managed-instance\dms-target-details1.png)

2.  Välj **Spara**.

3.  På den **översikt** skärmen, granska och kontrollera informationen som är kopplade till projektet migrering.
 
    ![Översikt över migreringsprojekt](media\tutorial-sql-server-to-managed-instance\dms-project-summary1.png)

4.  Välj **Spara**.   

## <a name="run-the-migration"></a>Kör migreringen

1.  Välj senast sparade projektet + **ny aktivitet**, och välj sedan **kör migrering**.

    ![Skapa ny aktivitet](media\tutorial-sql-server-to-managed-instance\dms-create-new-activity1.png)

2.  När du uppmanas att ange autentiseringsuppgifter för käll- och målservrarna och välj sedan **spara**.

3.  På den **Välj källa databaser** , väljer källdatabasen som du vill migrera.

    ![Välj källa databaser](media\tutorial-sql-server-to-managed-instance\dms-select-source-databases1.png)

4.  Välj **spara**, och klicka sedan på den **konfigurera migreringsinställningarna** skärmen, innehåller följande information:

    | | |
    |--------|---------|
    |**Nätverksresurs för plats** | Det lokala nätverket dela att tjänsten Azure Database migrering kan ta källan säkerhetskopiorna av databasen på. Kontot kör källa SQL Server-instansen måste ha skrivbehörighet på den här nätverksresursen. Ange en FQDN eller IP-adresser för servern i nätverksresursen, till exempel '\\\servername.domainname.com\backupfolder' eller '\\\IP address\backupfolder'.|
    |**Användarnamn** | Windows-användarnamnet att tjänsten Azure Database migrering kan personifiera och ladda upp säkerhetskopian till Azure storage-behållare för återställningen. |
    |**Lösenord** | Lösenord för användaren. |
    |**Inställningarna för kontot** | SAS-URI som tillhandahåller tjänsten Azure Database migrering med åtkomst till ditt konto lagringsbehållare som tjänsten Överför säkerhetskopiera filer och som används för att migrera databaser som ska hanteras Azure SQL Database-instans. [Lär dig att hämta SAS-URI för blob-behållaren](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    
    ![Konfigurera migreringsinställningarna](media\tutorial-sql-server-to-managed-instance\dms-configure-migration-settings1.png)

5.  Välj **spara**, och klicka sedan på den **migrering sammanfattning** skärmen i den **aktivitetsnamn** text anger du ett namn för aktiviteten migrering.

6. Expandera den **verifieringsalternativ** avsnitt för att visa den **Välj verifieringsalternativ** skärmen, ange om du vill validera migrerade databasen frågan är korrekt och välj sedan **spara** .  

    ![Sammanfattning av migrering](media\tutorial-sql-server-to-managed-instance\dms-migration-summary1.png)

7. Välj **kör migrering**.

    Visas fönstret migrering aktivitet och status för aktiviteten är **väntande**.

   ![Migreringsaktiviteter väntande](media\tutorial-sql-server-to-managed-instance\dms-migration-activity-pending.png)

## <a name="monitor-the-migration"></a>Övervaka migreringen

1. På skärmen migrering aktivitet väljer **uppdatera** att uppdatera visningen tills du ser att status för migreringen visas som **slutförd**.
 
   ![Migrering aktiviteten är slutförd](media\tutorial-sql-server-to-managed-instance\dms-migration-activity-finished.png)

2. När migreringen är klar väljer du **hämta rapport** att hämta en rapport med information som är associerade med migreringen.
 
3. Kontrollera att måldatabasen på Azure SQL-hanterade databasinstans målmiljön.

## <a name="next-steps"></a>Nästa steg

- En självstudiekurs visar hur du migrerar en databas till en hanterad instans med T-SQL RESTORE-kommandot finns [återställa en säkerhetskopia till en hanterad instans med kommandot restore](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- Information om hanterade instansen finns [vad är en hanterad instans](../sql-database/sql-database-managed-instance.md).
- Information om hur du ansluter appar till en hanterad finns [ansluta program](../sql-database/sql-database-managed-instance-connect-app.md).
