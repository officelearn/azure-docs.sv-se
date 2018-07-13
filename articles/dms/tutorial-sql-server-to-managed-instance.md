---
title: Använd DMS för att migrera till Azure SQL Database Managed Instance | Microsoft Docs
description: Lär dig att migrera från SQL Server lokalt till Azure SQL Database Managed Instance med Azure Database Migration Service.
services: dms
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 07/12/2018
ms.openlocfilehash: c911b096af6662e11afb4c4262b92c239d252c36
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990235"
---
# <a name="migrate-sql-server-to-azure-sql-database-managed-instance-using-dms"></a>Migrera SQL Server till Azure SQL Database Managed Instance med DMS
Du kan använda Azure Database Migration Service för att migrera databaserna från en lokal SQL Server-instansen till en [Azure SQL Database Managed Instance](../sql-database/sql-database-managed-instance.md). För ytterligare metoder som kan kräva att vissa manuellt arbete, finns i artikeln [migrering av SQL Server-instans till Azure SQL Database Managed Instance](../sql-database/sql-database-managed-instance-migrate.md).

> [!IMPORTANT]
> Migreringsprojekt från SQL Server till Azure SQL Database Managed Instance är i förhandsversion och kan komma att den [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I den här självstudien får du migrera den **Adventureworks2012** databas från en lokal instans av SQL Server till en Azure SQL Database Managed Instance med Azure Database Migration Service.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsprojekt med Azure Database Migration Service.
> * Kör migreringen.
> * Övervaka migreringen.
> * Hämta en migreringsrapport.

## <a name="prerequisites"></a>Förutsättningar
Den här kursen måste du:

- Skapa ett virtuellt nätverk för Azure Database Migration Service med hjälp av Azure Resource Manager-distributionsmodellen, som tillhandahåller plats-till-plats-anslutning till dina lokala källservrar genom att använda antingen [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Lär dig nätverkstopologier för Azure SQL DB Managed Instance-migrering med hjälp av Azure Database Migration Service](https://aka.ms/dmsnetworkformi).
- Se till att din Azure virtuellt nätverk (VNET) Network Security Group regler gör inte blockera följande kommunikationen portarna 443, 53, 9354, 445, 12000. Mer information om Azure VNET NSG-trafikfiltrering finns i artikeln [filtrera nätverkstrafik med nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Konfigurera din [Windows brandvägg för databasmotoråtkomst för källan](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Öppna din Windows-brandväggen så att Azure Database Migration Service att få åtkomst till källan SQL Server, vilket som standard är TCP-port 1433.
- Om du kör flera namngivna SQL Server-instanser med dynamiska portar, kan du aktivera tjänsten SQL Browser och tillåta åtkomst till UDP-port 1434 genom dina brandväggar så att Azure Database Migration Service kan ansluta till en namngiven instans på källan Server.
- Om du använder en brandväggsinstallation framför din källdatabaser, kan du behöva lägga till brandväggsregler som tillåter Azure Database Migration Service att komma åt källdatabaserna för migrering samt filer via SMB-port 445.
- Skapa en Azure SQL Database Managed Instance genom att följa detaljerat i artikeln [skapa en Azure SQL Database Managed Instance i Azure-portalen](https://aka.ms/sqldbmi).
- Se till att inloggningar som används för att ansluta SQL Server-källans och rikta Managed Instance är medlemmar i rollen sysadmin.
- Skapa en nätverksresurs som Azure Database Migration Service kan använda för att säkerhetskopiera källdatabasen.
- Kontrollera att tjänstkontot som kör källan SQL Server-instansen har skrivbehörighet för den nätverksresurs som du skapade och att datorkontot för källservern har läs-/ skrivåtkomst till samma resurs.
- Anteckna en Windows-användare (och lösenord) som har fullständigt kontrollprivilegium för den nätverksresurs som du skapade tidigare. Azure Database Migration Service personifierar användarens autentiseringsuppgifter för att ladda upp de säkerhetskopiera filerna till Azure storage-behållare för att återställa.
- Skapa en blob-behållare och hämta dess SAS-URI med hjälp av stegen i artikeln [hantera Azure Blob Storage-resurser med Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container), se till att välja alla behörigheter (läsa, skriva, ta bort, lista) i fönstret principen när Skapa SAS-URI. Den här informationen ger Azure Database Migration Service åtkomst till din lagringskontobehållare för att ladda upp de säkerhetskopiera filerna som används för att migrera databaser till Azure SQL Database Managed Instance

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration

1. Logga in på Azure portal, Välj **alla tjänster**, och välj sedan **prenumerationer**.

    ![Visa portal prenumerationer](media\tutorial-sql-server-to-managed-instance\portal-select-subscriptions.png)        

2. Välj den prenumeration där du vill skapa en instans av Azure Database Migration Service och välj sedan **Resursprovidrar**.

    ![Visa resursprovidrar](media\tutorial-sql-server-to-managed-instance\portal-select-resource-provider.png)

3. Sök för migrering och sedan till höger om **Microsoft.DataMigration**väljer **registrera**.

    ![Registrera resursprovider](media\tutorial-sql-server-to-managed-instance\portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>Skapa en Azure Database Migration Service-instans

1. I Azure-portalen väljer du + **skapa en resurs**, Sök efter **Azure Database Migration Service**, och välj sedan **Azure Database Migration Service** från listrutan lista.

     ![Azure Marketplace](media\tutorial-sql-server-to-managed-instance\portal-marketplace.png)

2. På den **Azure Database Migration Service** väljer **skapa**.

    ![Skapa Azure Database Migration Service-instans](media\tutorial-sql-server-to-managed-instance\dms-create1.png)

3. På den **skapa migreringstjänsten** skärmen, ange ett namn för tjänsten, prenumerationen och en ny eller befintlig resursgrupp.

4. Välj ett befintligt virtuellt nätverk (VNET) eller skapa en.
 
    Det virtuella nätverket tillhandahåller Azure Database Migration Service åtkomst till SQL Server-källans och målets Azure SQL Database Managed Instance.

    Mer information om hur du skapar ett virtuellt nätverk i Azure-portalen finns i artikeln [skapa ett virtuellt nätverk med Azure portal](https://aka.ms/DMSVnet).

    Mer information finns i artikeln [nätverkstopologier för Azure SQL DB Managed Instance-migrering med hjälp av Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

5. Välj en prisnivå.

    Mer information om kostnader och prisnivåer finns i den [prissättningssidan](https://aka.ms/dms-pricing).
   
    ![Skapa DMS-tjänsten](media\tutorial-sql-server-to-managed-instance\dms-create-service1.png)

6.  Välj **skapa** att skapa tjänsten.

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När tjänsten har skapats kan hitta den i Azure-portalen, öppna den och sedan skapa ett nytt migreringsprojekt.

1. I Azure-portalen väljer du **alla tjänster**, Sök efter Azure Database Migration Service och välj sedan **Azure Databasmigreringstjänster**.

    ![Leta upp alla instanser av Azure Database Migration Service](media\tutorial-sql-server-to-azure-sql\dms-search.png)

2. På den **Azure Database Migration Service skärmen**, Sök efter namnet på den instans som du skapat och välj sedan instansen.
 
3. Välj + **nytt migreringsprojekt**.

4. På den **nytt migreringsprojekt** skärmen, ange ett namn för projektet, i den **typ av källserver** textrutan **SQL Server**, och sedan i den **mål servertyp** textrutan **Azure SQL Database Managed Instance**.

   ![Skapa DMS-projekt](media\tutorial-sql-server-to-managed-instance\dms-create-project1.png)

5. Välj **skapa** att skapa projektet.

## <a name="specify-source-details"></a>Ange information om datakälla

1. På den **Source information** skärmen, ange anslutningsinformationen för SQL Server-källans.

2. Om du inte har installerat ett betrott certifikat på servern, Välj den **lita på servercertifikatet** markerar du kryssrutan.

    Om ett betrott certifikat inte är installerad, genererar SQL Server ett självsignerat certifikat när instansen har startats. Det här certifikatet används för att kryptera autentiseringsuppgifterna för klientanslutningar.

    > [!CAUTION]
    > SSL-anslutningar som krypteras med hjälp av ett självsignerat certifikat ger inte hög säkerhet. De är sårbar för man-in-the-middle-attacker. Du bör inte förlita dig på SSL använder självsignerade certifikat i en produktionsmiljö eller servrar som är anslutna till internet.

   ![Information om datakälla](media\tutorial-sql-server-to-managed-instance\dms-source-details1.png)

3. Välj **Spara**.

4. På den **Välj källdatabaser** , väljer den **Adventureworks2012** databas för migrering.

   ![Välj källdatabaser](media\tutorial-sql-server-to-managed-instance\dms-source-database1.png)

5. Välj **Spara**.

## <a name="specify-target-details"></a>Ange målinformation

1.  På den **rikta information** skärmen, Ange anslutningsinformation för mål, vilket är den företablerade Azure SQL Database Managed Instance som den **AdventureWorks2012** databasen som ska vara migreras.

    Om du inte redan har etablerat Azure SQL Database Managed Instance väljer **nr** efter en länk för att etablera instansen. Du kan ändå gå vidare med att skapa projektet och sedan när Azure SQL Database Managed Instance är klar, tillbaka till det här specifika projektet att utföra migreringen.   
 
       ![Välj mål](media\tutorial-sql-server-to-managed-instance\dms-target-details1.png)

2.  Välj **Spara**.

3.  På den **projektsammanfattning** skärmen, granska och kontrollera information som är associerade med migration-projekt.
 
    ![Sammanfattning av migreringsprojekt](media\tutorial-sql-server-to-managed-instance\dms-project-summary1.png)

4.  Välj **Spara**.   

## <a name="run-the-migration"></a>Kör migreringen

1.  Välj senast sparade projektet, Välj + **ny aktivitet**, och välj sedan **kör migrering**.

    ![Skapa ny aktivitet](media\tutorial-sql-server-to-managed-instance\dms-create-new-activity1.png)

2.  När du uppmanas, ange autentiseringsuppgifter för käll- och målservrarna och välj sedan **spara**.

3.  På den **Välj källdatabaser** , väljer källdatabaserna som du vill migrera.

    ![Välj källdatabaser](media\tutorial-sql-server-to-managed-instance\dms-select-source-databases2.png)

4.  Välj **spara**, och klicka sedan på den **Välj inloggningar** , väljer de inloggningar som du vill migrera.

    Den aktuella versionen stöder endast migrera SQL-inloggningar.

    ![Välj inloggningar](media\tutorial-sql-server-to-managed-instance\dms-select-logins.png)

5. Välj **spara**, och klicka sedan på den **konfigurera migreringsinställningarna** skärmen, anger du följande information:

    | | |
    |--------|---------|
    |**Nätverksplatsresurs** | Det lokala nätverket delar att Azure Database Migration Service kan ta källan säkerhetskopiorna av databasen på. Tjänstkontot som kör SQL Server-instans måste ha skrivbehörighet på den här nätverksresursen. Ange ett fullständigt domännamn eller IP-adresser för servern i nätverksresursen, till exempel ”\\\servername.domainname.com\backupfolder' eller '\\\IP address\backupfolder'.|
    |**Användarnamn** | Windows-användarnamnet att Azure Database Migration Service kan personifiera och ladda upp de säkerhetskopiera filerna till Azure storage-behållare för att återställa. |
    |**Lösenord** | Lösenordet för användaren. |
    |**Inställningar för lagringskonto** | SAS-URI som innehåller Azure Database Migration Service åtkomst till din lagringskontobehållare som tjänsten Överför säkerhetskopiera filer och som används för att migrera databaser till Azure SQL Database Managed Instance. [Lär dig hur du SAS-URI för blob-behållare](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    
    ![Konfigurera Migreringsinställningar](media\tutorial-sql-server-to-managed-instance\dms-configure-migration-settings2.png)

5.  Välj **spara**, och klicka sedan på den **sammanfattning över Objektmigrering** skärmen i den **aktivitetsnamn** text, anger du ett namn för migreringsaktiviteten.

    ![Sammanfattning över objektmigrering](media\tutorial-sql-server-to-managed-instance\dms-migration-summary2.png)

6. Expandera den **verifieringsalternativ** avsnitt för att visa den **Välj verifieringsalternativ** skärmen, ange om du vill verifiera den migrerade databasen fråga är korrekt och välj sedan **spara** .  

7. Välj **kör migrering**.

    Aktivitetsfönstret migrering visas och status för aktiviteten är **väntande**.

## <a name="monitor-the-migration"></a>Övervaka migreringen

1. På skärmen aktivitet migrering väljer **uppdatera** att uppdatera visningen.
 
   ![Migreringsaktiviteter pågår](media\tutorial-sql-server-to-managed-instance\dms-migration-activity-in-progress.png)

2. Du kan ytterligare expandera databaser och inloggningar kategorier om du vill övervaka Migreringsstatus för på respektive server-objekt.

   ![Migreringsaktiviteter pågår](media\tutorial-sql-server-to-managed-instance\dms-migration-activity-monitor.png)

3. När migreringen är klar väljer du **ladda ned rapporten** att hämta en rapport med information som är associerade med migreringen.
 
4. Kontrollera att måldatabasen på målmiljö för Azure SQL Database Managed Instance.

## <a name="next-steps"></a>Nästa steg

- Se en självstudie som visar hur du migrerar en databas till en hanterad instans med hjälp av T-SQL RESTORE-kommandot, [återställa en säkerhetskopia till en hanterad instans med kommandot restore](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- Information om Managed Instance finns i [vad är en hanterad instans](../sql-database/sql-database-managed-instance.md).
- Information om hur du ansluter appar till en hanterad instans finns i [ansluta program](../sql-database/sql-database-managed-instance-connect-app.md).
