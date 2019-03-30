---
title: 'Självstudier: Använd DMS för att migrera till en Azure SQL Database managed instance | Microsoft Docs'
description: Lär dig att migrera från SQL Server på en plats till en Azure SQL Database managed instance med Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: 78f19fcca51bb9c14fd05013834599c3e0f12779
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662116"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-offline-using-dms"></a>Självstudier: Migrera SQL Server till en Azure SQL Database managed instance med DMS

Du kan använda Azure Database Migration Service till att migrera databaserna från en lokal SQL Server-instans till en [hanterad Azure SQL Database-instans](../sql-database/sql-database-managed-instance.md). Information om ytterligare metoder som kan kräva manuella insatser finns i artikeln om [migrering av SQL Server-instans till en hanterad Azure SQL Database-instans](../sql-database/sql-database-managed-instance-migrate.md).

I den här självstudien migrerar du databasen **Adventureworks2012** från en lokal instans av SQL Server till en hanterad Azure SQL Database-instans genom att använda Azure Database Migration Service.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> - Skapa en instans av Azure Database Migration Service.
> - Skapa ett migreringsprojekt med hjälp av Azure Database Migration Service.
> - Köra migreringen.
> - Övervaka migreringen.
> - Ladda ned en migreringsrapport.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Den här artikeln beskrivs en offline-migrering från SQL Server till en Azure SQL Database managed instance. En onlinemigrering finns [migrera SQL Server till en Azure SQL Database-hanterad instans med hjälp av DMS](tutorial-sql-server-managed-instance-online.md).

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

- Skapa ett virtuellt Azure-nätverk för Azure Database Migration Service genom att använda Azure Resource Manager-distributionsmodellen, som ger plats-till-plats-anslutning för dina lokala källservrar genom att använda [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Lär dig nätverkstopologier för migreringar av hanterade Azure SQL-databashanterade instanser med Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

    > [!NOTE]
    > Under installationen av virtuellt nätverk, om du använder ExpressRoute med nätverks-peering till Microsoft, lägger du till följande tjänst [slutpunkter](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) till undernätet där tjänsten ska etableras:
    > - Mål-database-slutpunkten (till exempel SQL-slutpunkten och Cosmos DB-slutpunkt)
    > - Slutpunkt för lagring
    > - Service bus-slutpunkt
    >
    > Den här konfigurationen är nödvändigt eftersom Azure Database Migration Service saknar Internetanslutning.

- Se till att dina regler för Nätverkssäkerhetsgrupp kopplad till virtuella nätverk inte blockerar följande kommunikationsportar 443, 53, 9354, 445, 12000. Mer information om trafikfiltrering för Azure VNET NSG finns i artikeln om att [filtrera nätverkstrafik med nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Konfigurera din [Windows-brandvägg för källdatabasmotoråtkomst](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Öppna Windows-brandväggen så att Azure Database Migration Service kommer åt käll-SQL Server, som har standardinställningen TCP-port 1433.
- Om du kör flera namngivna SQL Server-instanser med dynamiska portar kan du vilja aktivera SQL Browser Service och tillåta åtkomst till UDP-port 1434 via dina brandväggar så att Azure Database Migration Service kan ansluta till en namngiven instans på källservern.
- Om du använder en brandväggsinstallation framför dina källdatabaser kanske du måste lägga till brandväggsregler för att tillåta Azure Database Migration Service att komma åt källdatabaserna för migrering samt filer via SMB-port 445.
- Skapa en hanterad Azure SQL Database-instans genom att följa informationen i artikeln om att [skapa en hanterad Azure SQL Database-instans i Azure-portalen](https://aka.ms/sqldbmi).
- Se till att inloggningarna som används för att ansluta SQL Server-källan och den hanterade målinstansen är medlemmar av sysadmin-serverrollen.
- Skapa en nätverksresurs som Azure Database Migration Service kan använda till att säkerhetskopiera källdatabasen.
- Se till att tjänstkontot som kör SQL Server-källinstansen har skrivbehörighet på nätverksresursen som du har skapat och att datorkontot för källservern har läs-/skrivåtkomst till samma resurs.
- Anteckna en Windows-användare (och lösenordet) som har fullständig kontrollbehörighet på nätverksresursen som du tidigare har skapat. Azure Database Migration Service personifierar användarens autentiseringsuppgifter för att ladda upp de säkerhetskopierade filerna till Azure Storage-container för återställning.
- Skapa en blobbcontainer och hämta dess SAS-URI med hjälp av stegen i artikeln om [hantering av Azure Blob Storage-resurser med Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container), se till att välja alla behörigheter (Läsa, Skriva, Ta bort, Lista) i principfönstret när du skapar SAS-URI:et. Den här informationen ger Azure Database Migration Service med åtkomst till din lagringskontocontainer för att ladda upp de säkerhetskopierade filerna för att migrera databaser till en hanterad Azure SQL Database-instans.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration

1. Logga in på Azure-portalen och välj **Alla tjänster** och sedan **Prenumerationer**.

    ![Visa portalprenumerationer](media/tutorial-sql-server-to-managed-instance/portal-select-subscriptions.png)        

2. Välj den prenumeration där du vill skapa en instans av Azure Database Migration Service och välj sedan **Resursprovidrar**.

    ![Visa resursprovidrar](media/tutorial-sql-server-to-managed-instance/portal-select-resource-provider.png)

3. Sök efter migreringen och välj sedan **Registrera** till höger om **Microsoft.DataMigration**.

    ![Registrera resursprovider](media/tutorial-sql-server-to-managed-instance/portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>Skapa en Azure Database Migration Service-instans

1. I Azure Portal väljer du + **Skapa en resurs**, söker efter **Azure Database Migration Service** och väljer sedan **Azure Database Migration Service** i listrutan.

     ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance/portal-marketplace.png)

2. På sidan **Azure Database Migration Service** väljer du **Skapa**.

    ![Skapa Azure Database Migration Service-instans](media/tutorial-sql-server-to-managed-instance/dms-create1.png)

3. På sidan **Create Migration Service** anger du ett namn för tjänsten, prenumerationen och en ny eller befintlig resursgrupp.

4. Välj den plats där du vill skapa instansen av DMS.

5. Välj ett befintligt virtuellt nätverk eller skapa ett.

    Det virtuella nätverket ger Azure Database Migration Service åtkomst till SQL Server-källan och den hanterade Azure SQL Database-målinstansen.

    Mer information om hur du skapar ett virtuellt nätverk i Azure Portal finns i artikeln [Skapa ett virtuellt nätverk med hjälp av Azure Portal](https://aka.ms/DMSVnet).

    Mer information finns i artikeln om [nätverkstopologier för migreringar av hanterade Azure SQL DB-instanser med Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

6. Välj en prisnivå.

    Mer information om kostnader och prisnivåer finns på [sidan med priser](https://aka.ms/dms-pricing).

    ![Skapa DMS-tjänst](media/tutorial-sql-server-to-managed-instance/dms-create-service2.png)

7. Välj **Skapa** för att skapa tjänsten.

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När en instans av tjänsten har skapats letar du reda på den i Azure Portal, öppnar den och skapar sedan ett nytt migreringsprojekt.

1. I Azure Portal väljer du **Alla tjänster**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Services**.

    ![Leta reda på alla instanser Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance/dms-search.png)

2. På **Azure Database Migration Service-sidan** söker du efter namnet på instansen som du har skapat och väljer sedan instansen.

3. Välj + **Nytt migreringsprojekt**.

4. På sidan **Nytt migreringsprojekt** anger du namnet på projektet, I textrutan **Typ av källserver** väljer du **SQL Server**, i textrutan **Målservertyp** väljer du **Hanterad Azure SQL Database-instans** och sedan för **Välj typ av aktivitet** väljer du **Offline-datamigrering**.projekt

   ![Skapa DMS-projekt](media/tutorial-sql-server-to-managed-instance/dms-create-project2.png)

5. Välj **Skapa** för att skapa projektet.

## <a name="specify-source-details"></a>Ange källinformation

1. På sidan **Migreringskällinformation** anger du anslutningsinformationen för SQL Server-källan.

2. Om du inte har installerat ett betrott certifikat på servern markerar du kryssrutan **Lita på servercertifikatet**.

    När ett betrott certifikat inte har installerats genererar SQL Server ett självsignerat certifikat när instansen har startats. Detta certifikat används till att kryptera autentiseringsuppgifterna för klientanslutningar.

    > [!CAUTION]
    > SSL-anslutningar som har krypterats med ett självsignerat certifikat ger inte stark säkerhet. De är sårbara för man-in-the-middle-attacker. Du bör inte lita på SSL som använder självsignerade certifikat i en produktionsmiljö eller på servrar som är anslutna till internet.

   ![Källinformation](media/tutorial-sql-server-to-managed-instance/dms-source-details1.png)

3. Välj **Spara**.

4. På sidan **Välj källdatabaser**, välj databsen **Adventureworks2012** för migrering.

   ![Välj källdatabaser](media/tutorial-sql-server-to-managed-instance/dms-source-database1.png)

    > [!IMPORTANT]
    > Om du använder SQL Server Integration Services (SSIS) stöder DMS för närvarande inte migrering av katalogdatabasen för dina SSIS-projekt/-paket (SSISDB) från SQL Server till en hanterad Azure SQL Database-instans. Du kan dock etablera SSIS i Azure Data Factory (ADF) och distribuera om dina SSIS-projekt/-paket till den mål-SSISDB som ligger i den hanterade Azure SQL Database-instansen. Mer information om att migrera SSIS-paket finns i artikeln [Migrate SQL Server Integration Services packages to Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages) (Migrera SQL Server Integration Services-paket till Azure).

5. Välj **Spara**.

## <a name="specify-target-details"></a>Ange målinformation

1. På sidan **Information om migreringsmål** anger du anslutningsinformationen för målet, som är den företablerade hanterade Azure SQL Database-instansen som du ska migrera databasen **AdventureWorks2012** till.

    Om du inte redan har etablerat den hanterade Azure SQL Database-instansen väljer du [länken](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started) som hjälper dig att etablera instansen. Du kan ändå fortsätta med att skapa projektet och sedan, när den hanterade Azure SQL Database-instansen är klar, gå tillbaka till specifika projektet att utföra migreringen.

     ![Välja mål](media/tutorial-sql-server-to-managed-instance/dms-target-details2.png)

2. Välj **Spara**.

## <a name="select-source-databases"></a>Välj källdatabaser

1. På sidan **Välj källdatabaser** väljer du de källdatabaser som du vill migrera.

    ![Välj källdatabaser](media/tutorial-sql-server-to-managed-instance/select-source-databases.png)

2. Välj **Spara**.

## <a name="select-logins"></a>Välj inloggningar

1. På sidan **Välj inloggningar** väljer du de inloggningar du vill migrera.

    >[!NOTE]
    >Den här versionen stöder bara migrering av SQL-inloggningar.

    ![Välj inloggningar](media/tutorial-sql-server-to-managed-instance/select-logins.png)

2. Välj **Spara**.

## <a name="configure-migration-settings"></a>Konfigurera migreringsinställningar

1. På sidan **Konfigurera migreringsinställningar** anger du följande information:

    | | |
    |--------|---------|
    |**Välj alternativ för att säkerhetskopiera källa** | Välj alternativet **Jag tillhandahåller de senaste säkerhetskopierade filerna** när du redan har fullständiga säkerhetskopior tillgängliga för DMS att använda för databasmigrering. Välj alternativet **Jag låter Azure Database Migration Service skapa säkerhetskopierade filer** när du vill att DMS ska ta källdatabasens fullständiga säkerhetskopia först och använda den för migrering. |
    |**Nätverksplatsresurs** | Den lokala SMB-nätverksresursen som Azure Database Migration Service kan ta säkerhetskopior av källdatabasen till. Tjänstkontot som kör en SQL Server-källinstans måste ha skrivbehörighet på den här nätverksresursen. Ange ett fullständigt domännamn eller IP-adresser för servern i nätverksresursen, till exempel \\\servernamn.domännamn.com\säkerhetskopieringsmapp eller \\\IP-adress\säkerhetskopieringsmapp.|
    |**Användarnamn** | Kontrollera att Windows-användaren har fullständig kontrollbehörighet på nätverksresursen du har angett ovan. Azure Database Migration Service personifierar användarens autentiseringsuppgifter för att ladda upp de säkerhetskopierade filerna till Azure Storage-container för återställning. Om TDE-aktiverade databaser har valts för migrering måste Windows-användaren ovan vara det inbyggda administratörskontot och [User Account Control](https://docs.microsoft.com/windows/security/identity-protection/user-account-control/user-account-control-overview) måste vara inaktiverat för Azure Database Migration Service för att ladda upp och ta bort certifikatfilerna.) |
    |**Lösenord** | Lösenordet för användaren. |
    |**Inställningar för lagringskonto** | SAS-URI:et som ger Azure Database Migration Service åtkomst till lagringskontocontainern som tjänsten laddar upp de säkerhetskopierade filerna till och som används för att migrera databaser till en hanterad Azure SQL Database-instans. [Lär dig hur du hämtar SAS-URI för blobbcontainer](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    |**TDE-inställningar** | Om du migrerar källdatabaserna med Transparent datakryptering (TDE) aktiverad, måste du ha skrivbehörighet på den hanterade instansen för mål-Azure SQL Database.  Välj den prenumeration där den hanterade Azure SQL Database-instansen har etablerats från den nedrullningsbara menyn.  Välj den hanterade **Azure SQL Database-målinstansen** på den nedrullningsbara menyn. |

    ![Konfigurera migreringsinställningar](media/tutorial-sql-server-to-managed-instance/dms-configure-migration-settings3.png)

2. Välj **Spara**.

## <a name="review-the-migration-summary"></a>Granska migreringssammanfattningen

1. På sidan **Migreringssammanfattning**, i textrutan **Aktivitetsnamn** anger du ett namn på migreringsaktiviteten.

2. Expandera avsnittet **Valideringsalternativ** för att visa sidan **Välj verifieringsalternativ**, ange om du vill verifiera att den migrerade databasen är frågeriktig och välj sedan **Spara**.

3. Granska och verifiera informationen som är kopplad till migreringsprojektet.

    ![Sammanfattning av migreringsprojekt](media/tutorial-sql-server-to-managed-instance/dms-project-summary2.png)

4. Välj **Spara**.

## <a name="run-the-migration"></a>Köra migreringen

- Välj **Kör migrering**.

  Migreringsaktivitetsfönstret visas och Status för aktiviteten är **Väntande**.

## <a name="monitor-the-migration"></a>Övervaka migreringen

1. På migreringsaktivitetssidan väljer du **Uppdatera** för att uppdatera visningen.
 
   ![Migreringsaktivitet pågår](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration1.png)

    Du kan expandera databaserna och inloggningskategorierna ytterligare för att övervaka migreringsstatusen för respektive serverobjekt.

   ![Migreringsaktivitet pågår](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration-extend.png)

2. När migreringen har slutförts väljer du **Ladda ned rapport** för att få en rapport som visar informationen som rör migreringsprocessen.

3. Verifiera måldatabasen i den hanterade Azure SQL Database-målinstansens miljö.

## <a name="next-steps"></a>Nästa steg

- En självstudie om hur du migrerar en databas till en hanterad instans med kommandot T-SQL RESTORE finns i [Återställa en säkerhetskopia till en hanterad instans med kommandot restore](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- Information om hanterade instanser finns i avsnittet [Vad är en hanterad instans?](../sql-database/sql-database-managed-instance.md).
- Information om hur du ansluter appar till en hanterad instans finns i [Ansluta program](../sql-database/sql-database-managed-instance-connect-app.md).
