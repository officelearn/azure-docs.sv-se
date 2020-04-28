---
title: 'Självstudie: Migrera fjärr skrivbords SQL Server online till SQL Database'
titleSuffix: Azure Database Migration Service
description: Lär dig att utföra en online-migrering från RDS-SQL Server för att Azure SQL Database en enskild databas eller hanterad instans med hjälp av Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 8d538deca610fd9981d401d28b6bea1c31c6d4c8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80298860"
---
# <a name="tutorial-migrate-rds-sql-server-to-azure-sql-database-or-an-azure-sql-database-managed-instance-online-using-dms"></a>Självstudie: Migrera RDS-SQL Server till Azure SQL Database eller en Azure SQL Database Hanterad instans online med DMS
Du kan använda Azure Database Migration Service för att migrera databaserna från en RDS SQL Server-instans till [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/) eller en [Azure SQL Database Hanterad instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) med minimal stillestånds tid. I den här självstudien migrerar du **Adventureworks2012** -databasen som återställs till en RDS-SQL Server instans av SQL Server 2012 (eller senare) till Azure SQL Database eller en Azure SQL Database Hanterad instans med hjälp av Azure Database migration service.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Skapa en instans av Azure SQL Database eller en Azure SQL Database Hanterad instans. 
> * Migrera exempelschemat med hjälp av Data Migration Assistant.
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsprojekt med hjälp av Azure Database Migration Service.
> * Köra migreringen.
> * Övervaka migreringen.
> * Ladda ned en migreringsrapport.

> [!NOTE]
> Användning av Azure Database Migration Service för att utföra en onlinemigrering kräver att en instans skapas baserat på prisnivån Premium. Mer information finns i på [prissättningssidan](https://azure.microsoft.com/pricing/details/database-migration/) för Azure Database Migration Service.

> [!IMPORTANT]
> För optimala migreringsfunktioner rekommenderar Microsoft att skapa en instans av Azure Database Migration Service i samma Azure-region som måldatabasen. Att flytta data mellan regioner eller geografiska områden kan göra migreringsprocessen långsammare och leda till fel.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

I den här artikeln beskrivs en online-migrering från RDS-SQL Server till Azure SQL Database eller en Azure SQL Database Hanterad instans.

## <a name="prerequisites"></a>Krav
För att slutföra den här kursen behöver du:

* Skapa en [RDS SQL Server-databas](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.SQLServer.html).
* Skapa en Azure SQL Database-instans, vilket du kan göra genom att följa anvisningarna i artikeln [Skapa en Azure SQL-databas i Azure-portalen](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).

    > [!NOTE]
    > Om du migrerar till en Azure SQL Database Hanterad instans följer du informationen i artikeln [skapa en Azure SQL Database Hanterad instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)och skapar sedan en tom databas med namnet **AdventureWorks2012**. 
 
* Ladda ned och installera [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) version 3.3 eller senare.
* Skapa en Microsoft Azure Virtual Network för Azure Database Migration Service med hjälp av Azure Resource Manager distributions modellen. Om du migrerar till en Azure SQL Database Hanterad instans, se till att skapa DMS-instansen i samma virtuella nätverk som används för den Azure SQL Database hanterade instansen, men i ett annat undernät.  Alternativt, om du använder ett annat virtuellt nätverk för DMS, måste du skapa en virtuell nätverks-peering mellan de två virtuella nätverken. Mer information om hur du skapar ett virtuellt nätverk finns i [Virtual Network-dokumentationen](https://docs.microsoft.com/azure/virtual-network/)och i synnerhet snabb starts artiklar med stegvisa anvisningar.

    > [!NOTE]
    > Om du använder ExpressRoute med nätverks-peering till Microsoft under installationen av det virtuella nätverket lägger du till följande tjänst [slut punkter](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) i under nätet där tjänsten ska tillhandahållas:
    >
    > * Slut punkt för mål databas (till exempel SQL-slutpunkt, Cosmos DB slut punkt och så vidare)
    > * Lagrings slut punkt
    > * Service Bus-slutpunkt
    >
    > Den här konfigurationen är nödvändig eftersom den Azure Database Migration Service saknar Internet anslutning. 

* Se till att de virtuella nätverkets säkerhets grupp regler inte blockerar följande portar för inkommande kommunikation till Azure Database Migration Service: 443, 53, 9354, 445, 12000. Mer information om NSG för trafik filtrering i virtuellt nätverk finns i artikeln [filtrera nätverks trafik med nätverks säkerhets grupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Konfigurera din [Windows-brandvägg för databasmotoråtkomst](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Öppna Windows-brandväggen så att Azure Database Migration Service kommer åt käll-SQL Server, som har standardinställningen TCP-port 1433.
* Skapa en [brandväggsregel](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) på servernivå för Azure SQL Database-servern för att tillåta åtkomst för Azure Database Migration Service till måldatabaserna. Ange under nätets intervall för det virtuella nätverk som används för Azure Database Migration Service.
* Se till att de autentiseringsuppgifter som används för att ansluta till RDS SQL Server-källinstansen är associerade med ett konto som är medlem i serverrollen för ”Processadmin” och medlem av databasrollerna ”db_owner” på alla databaser som ska migreras.
* Se till att de autentiseringsuppgifter som används för att ansluta till mål Azure SQL Databases instansen har kontroll databas behörighet för Azure SQL-databaser och en medlem i sysadmin-rollen om du migrerar till en Azure SQL Database Hanterad instans.
* Källversionen för RDS SQL Server måste vara SQL Server 2012 och senare. För att fastställa vilken version av SQL Server-instansen som körs kan du läsa artikeln [Hur man fastställer version, utgåva, och uppdateringsnivå för SQL Server och dess komponenter](https://support.microsoft.com/help/321185/how-to-determine-the-version-edition-and-update-level-of-sql-server-an).
* Aktivera sammanställning av ändringsdata (CDC) på RDS SQL Server-databasen och alla användartabeller som valts för migrering.
    > [!NOTE]
    > Du kan använda skriptet nedan för att aktivera CDC på en RDS SQL Server-databas.
    ```
    exec msdb.dbo.rds_cdc_enable_db 'AdventureWorks2012'
    ```
    > Du kan använda skriptet nedan för att aktivera CDC på alla tabeller.
    ```
    use <Database name>
    go
    exec sys.sp_cdc_enable_table 
    @source_schema = N'Schema name', 
    @source_name = N'table name', 
    @role_name = NULL, 
    @supports_net_changes = 1 --for PK table 1, non PK tables 0
    GO
    ```
* Inaktivera databasutlösare i målets Azure SQL Database.
    > [!NOTE]
    > Du hittar databasutlösarna i målets Azure SQL Database med följande fråga:
    ```
    Use <Database name>
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```
    Mer information finns i artikeln [om att inaktivera utlösare (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

## <a name="migrate-the-sample-schema"></a>Migrera exempelschemat
Använd DMA för att migrera schemat till Azure SQL Database.

> [!NOTE]
> Innan du skapar ett migreringsprojekt i DMA ska du redan ha etablerat en Azure SQL-databas så som anges i förutsättningarna. För den här självstudien antas namnet på Azure SQL Database vara **AdventureWorks2012** men du kan ange ett valfritt namn.

För att migrera **AdventureWorks2012**-schemat till Azure SQL Database gör du följande:

1. I Data Migration Assistant väljer du ikonen Nytt (+) och sedan under **Projekttyp** väljer du **Migrering**.
2. Ange ett projektnamn i textrutan **Typ av källserver**, välj **SQL Server**. I textrutan **Målservertyp** väljer du sedan **Azure SQL Database**.
3. Under **Migreringsomfång** väljer du **Endast schema**.

    När du har utfört de föregående stegen bör DMA-gränssnittet visas i följande bild:

    ![Skapa Data Migration Assistant-projekt](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-create-project.png)

4. Välj **Skapa** för att skapa projektet.
5. I DMA anger du källanslutningsinformationen för SQL Server. Välj **Anslut** och välj databasen **AdventureWorks2012**.

    ![Källanslutningsinformation för Data Migration Assistant](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-source-connect.png)

6. Välj **Next**, under **Anslut till målserver**, ange målanslutningsinformationen för Azure SQL-databasen, välj **Anslut** och välj sedan databasen **AdventureWorksAzure** som du har företablerat i Azure SQL Database.

    ![Målanslutningsinformation för Data Migration Assistant](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-target-connect.png)

7. Välj **Nästa** för att komma till sidan **Välj objekt**, där du kan ange schemaobjekten i databasen **AdventureWorks2012** som måste distribueras till Azure SQL Database.

    Som standard markeras alla objekt.

    ![Skapa SQL-skript](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-assessment-source.png)

8. Välj **Skapa SQL-skript** för att skapa SQL-skripten och granska sedan om det finns fel i skripten.

    ![Schemaskript](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-script.png)

9. Välj **Distribuera schema** för att distribuera schemat till Azure SQL Database, och när schemat har distribuerats kontrollerar du om det finns avvikelser på målservern.

    ![Distribuera schema](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration

1. Logga in på Azure Portal och välj **Alla tjänster** och sedan **Prenumerationer**.

   ![Visa portalprenumerationer](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-select-subscription1.png)

2. Välj den prenumeration där du vill skapa en instans av Azure Database Migration Service och välj sedan **Resursprovidrar**.

    ![Visa resursprovidrar](media/tutorial-sql-server-to-azure-sql-online/portal-select-resource-provider.png)

3. Sök efter migrering och välj sedan **Registrera**till höger om **Microsoft. data migration**.

    ![Registrera resursprovider](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Skapa en instans

1. I Azure Portal väljer du + **Skapa en resurs**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Service** i listrutan.

    ![Azure Marketplace](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-marketplace.png)

2. På sidan **Azure Database Migration Service** väljer du **Skapa**.

    ![Skapa Azure Database Migration Service-instans](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. På sidan **Create Migration Service** anger du ett namn för tjänsten, prenumerationen och en ny eller befintlig resursgrupp.

4. Välj den plats där du vill skapa instansen av Azure Database Migration Service. 

5. Välj ett befintligt virtuellt nätverk eller skapa ett nytt.

    Det virtuella nätverket ger Azure Database Migration Service åtkomst till käll SQL Server och mål Azure SQL Database instansen.

    Mer information om hur du skapar ett virtuellt nätverk i Azure Portal finns i artikeln [skapa ett virtuellt nätverk med hjälp av Azure Portal](https://aka.ms/DMSVnet).

6. Välj en prisnivå. För den här onlinemigreringen måste du markera Premium-prisnivån.

    Mer information om kostnader och prisnivåer finns på [sidan med priser](https://aka.ms/dms-pricing).

     ![Konfigurera Azure Database Migration Service-instansinställningar](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-settings3.png)

7. Välj **Skapa** för att skapa tjänsten.

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När tjänsten har skapats letar du reda på den i Azure Portal, öppnar den och skapar sedan ett nytt migreringsprojekt.

1. I Azure Portal väljer du **Alla tjänster**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Services**.

      ![Leta reda på alla instanser Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-search.png)

2. På sidan **Azure Database Migration Services** söker du efter namnet på Azure Database Migration Service-instansen du har skapat och väljer sedan instansen.

     ![Hitta din instans av Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-instance-search.png)

3. Välj + **Nytt migreringsprojekt**.
4. På sidan **Nytt migreringsprojekt** anger du namnet på projektet, i textrutan **Typ av källserver** väljer du **AWS Fjärrskrivbordstjänster för SQLServer** och i textrutan **Målservertyp** väljer du **Azure SQL Database**.

    > [!NOTE]
    > För mål server typ väljer du **Azure SQL Database** för att migrera till både en Azure SQL Database singleton-databas och en Azure SQL Database Hanterad instans.

5. I avsnittet **Välj typ av aktivitet** väljer du **migrering av data online**.

    > [!IMPORTANT]
    > Se till att välja **migrering av data online**. offline-migrering stöds inte för det här scenariot.

    ![Skapa Database Migration Service-projekt](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-project4.png)

    > [!NOTE]
    > Alternativt kan du välja **skapa endast projekt** för att skapa migreringsjobbet nu och utföra migreringen senare.

6. Välj **Spara**.

7. Välj **Skapa och kör aktivitet** för att skapa projektet och köra migreringsaktiviteten.

    ![Skapa och köra en Azure Database Migration Service-aktivitet](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-and-run-activity1.png)

## <a name="specify-source-details"></a>Ange källinformation

1. På sidan **Migreringskällinformation** anger du anslutningsinformationen för SQL Server-källinstansen.

    Se till att använda ett fullständigt domännamn (FQDN) för SQL Server-källinstansens namn.

2. Om du inte har installerat ett betrott certifikat på källservern markerar du kryssrutan **Lita på servercertifikatet**.

    När ett betrott certifikat inte har installerats genererar SQL Server ett självsignerat certifikat när instansen har startats. Detta certifikat används till att kryptera autentiseringsuppgifterna för klientanslutningar.

    > [!CAUTION]
    > TLS-anslutningar som krypteras med hjälp av ett självsignerat certifikat ger inte stark säkerhet. De är sårbara för man-in-the-middle-attacker. Du bör inte förlita dig på TLS med självsignerade certifikat i en produktions miljö eller på servrar som är anslutna till Internet.

   ![Källinformation](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-source-details3.png)

## <a name="specify-target-details"></a>Ange målinformation

1. Välj **Spara** och ange på sidan **Information om migreringsmål** anslutningsinformationen för Azure SQL Database-målservern, som är den företablerade Azure SQL Database som **AdventureWorks2012**-schemat distribuerades till med DMA.

    ![Välja mål](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-select-target3.png)

2. Välj **Spara** och mappa sedan på sidan **Mappa till måldatabaser** käll- och måldatabasen för migrering.

    Om måldatabasen innehåller samma databasnamn som källdatabasen väljer Azure Database Migration Service måldatabasen som standard.

    ![Mappa till måldatabaser](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-map-targets-activity4.png)

3. Välj **Spara**. Expandera tabellistan på sidan **Välj tabeller** och granska sedan listan över berörda fält.

    Azure Database Migration Service väljer automatiskt alla tomma källtabeller som finns på Azure SQL Database-målinstansen. Om du vill migrera tabeller på nytt som redan innehåller data måste du uttryckligen välja tabellerna på den här skärmen.

    ![Välj tabeller](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-configure-setting-activity4.png)

4. Välj **Spara** när du har angett följande **Avancerade inställningar för onlinemigrering**.

    | Inställning | Beskrivning |
    | ------------- | ------------- |
    | **Maximalt antal tabeller som kan läsas in parallellt** | Anger antalet tabeller som DMS kör parallellt under migreringen. Standardvärdet är 5, men det kan ställas in på ett optimalt värde för att uppfylla specifika migreringsbehov baserat på alla POC-migreringar. |
    | **När en källtabell trunkeras** | Anger om DMS trunkerar måltabellen under migreringen. Den här inställningen kan vara användbar om en eller flera tabeller trunkeras som en del av migreringsprocessen. |
    | **Konfigurera inställningar för data för stora objekt (LOB)** | Anger om DMS migrerar obegränsade LOB-data eller begränsar LOB-data som migrerats till en viss storlek.  När det finns en gräns för hur LOB-data migreras, trunkeras eventuella LOB-data utanför den gränsen. För produktionsmigrering rekommenderar vi att du väljer **Tillåt obegränsad LOB-storlek** för att förhindra dataförlust. När du anger att du vill tillåta obegränsad LOB-storlek markerar du kryssrutan **Migrate LOB data in a single block when the LOB size is less than (KB)** (Migrera LOB-data i ett enda block när LOB-storleken är mindre än (KB)) för att förbättra prestanda. |

    ![Ställa in avancerade inställningar för onlinemigrering](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-advanced-online-migration-settings.png)

5. Välj **Spara**. I rutan **Aktivitetsnamn** på skärmen **Migreringssammanfattning** anger du ett namn för migreringsaktiviteten och granskar sedan sammanfattningen för att se till att informationen för källa och mål matchar det du angav tidigare.

    ![Migreringssammanfattning](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-migration-summary.png)

## <a name="run-the-migration"></a>Köra migreringen

* Välj **Kör migrering**.

    Migreringsaktivitetsfönstret visas och **Status** för aktiviteten är **Initieras**.

    ![Aktivitetsstatus – initieras](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-status2.png)

## <a name="monitor-the-migration"></a>Övervaka migreringen

1. På migreringsaktivitetssidan väljer du **Uppdatera** för att uppdatera visningen tills **Status** för migreringen är **Körs**.

2. Klicka på en specifik databas för att komma till migreringsstatus för åtgärderna **Fullständig datainläsning** och **Inkrementell datasynkronisering**.

    ![Aktivitetsstatus – Pågår](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-in-progress.png)

## <a name="perform-migration-cutover"></a>Utföra snabbmigrering

När den fullständiga inläsningen är klar är databaserna märkta med **Klar för snabbmigrering**.

1. När du är redo att slutföra databasmigreringen väljer du **Starta snabb**.

    ![Starta snabb](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-start-cutover.png)

2. Stoppa alla inkommande transaktioner till källdatabasen och vänta tills **Väntande ändringar** visar **0**.
3. Välj **Bekräfta** och sedan **Apply** (Använd).
4. När status för databasmigreringen visar **Slutförd** ansluter du dina program till den nya Azure SQL-måldatabasen.

    ![Aktivitetsstatus – slutförd](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-completed.png)

## <a name="next-steps"></a>Nästa steg

* Information om kända problem och begränsningar när du utför onlinemigreringar till Azure SQL DatabaseL finns i artikeln [Known issues and workarounds with Azure SQL Database online migrations](known-issues-azure-sql-online.md) (Kända problem och lösningar med Azure SQL Database-onlinemigreringar).
* Mer information om Azure Database Migration Service finns i artikeln [What is the Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview) (Vad är Azure Database Migration Service?).
* Information om Azure SQL Database finns i artikeln [Vad är tjänsten SQL Database?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).
* Information om Azure SQL Database hanterade instanser finns på sidan [Azure SQL Database Hanterad instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index).
