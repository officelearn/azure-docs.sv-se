---
title: 'Självstudiekurs: Migrera RDS MySQL online till Azure Database för MySQL'
titleSuffix: Azure Database Migration Service
description: Lär dig att utföra en onlinemigrering från RDS MySQL till Azure Database for MySQL med hjälp av Migreringstjänsten för Azure Database.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: c34de48d0184057f42d1b779abee56e1fa9ac169
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255169"
---
# <a name="tutorial-migrate-rds-mysql-to-azure-database-for-mysql-online-using-dms"></a>Självstudiekurs: Migrera RDS MySQL till Azure Database för MySQL online med DMS

Du kan använda Azure Database Migration Service för att migrera databaser från en RDS MySQL-instans till [Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) medan källdatabasen är online under migreringen. Med andra ord kan migrering uppnås med minimal stilleståndstid till programmet. I den här självstudien migrerar du exempeldatabasen **Anställda** från en instans av RDS MySQL till Azure Database for MySQL med hjälp av onlinemigreringsaktiviteten i Azure Database Migration Service.

I den här självstudiekursen får du lära du dig att:
> [!div class="checklist"]
>
> * Migrera exempelschemat med hjälp av verktygen mysqldump och mysql.
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsprojekt med hjälp av Azure Database Migration Service.
> * Köra migreringen.
> * Övervaka migreringen.

> [!NOTE]
> För att använda Azure Database Migration Service för att utföra en onlinemigrering måste du skapa en instans baserat på premiumprisnivån. Mer information finns i på [prissättningssidan](https://azure.microsoft.com/pricing/details/database-migration/) för Azure Database Migration Service.

> [!IMPORTANT]
> För optimala migreringsfunktioner rekommenderar Microsoft att skapa en instans av Azure Database Migration Service i samma Azure-region som måldatabasen. Att flytta data mellan regioner eller geografiska områden kan göra migreringsprocessen långsammare och leda till fel.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

I den här artikeln beskrivs hur du utför en onlinemigrering från en instans av RDS MySQL till Azure Database for MySQL.

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

* Kontrollera att källan MySQL-servern kör en MySQL-community-utgåva som stöds. Om du vill ta reda på vilken version av MySQL-instansen du vill köra kommandot i mysql-verktyget eller MySQL Workbench:

    ```
    SELECT @@version;
    ```

    Mer information finns i artikeln [Azure Database for MySQL-versioner som stöds](https://docs.microsoft.com/azure/mysql/concepts-supported-versions).

* Ladda ner och installera [exempeldatabasen MySQL-anställda **Employees** ](https://dev.mysql.com/doc/employee/en/employees-installation.html).
* Skapa en instans av [Azure Database för MySQL](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal).
* Skapa en Microsoft Azure Virtual Network for Azure Database Migration Service med hjälp av distributionsmodellen för Azure Resource Manager, som tillhandahåller anslutning från plats till plats till dina lokala källservrar med hjälp av [Antingen ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Mer information om hur du skapar ett virtuellt nätverk finns i [dokumentationen](https://docs.microsoft.com/azure/virtual-network/)till det virtuella nätverket och särskilt snabbstartsartiklarna med steg-för-steg-information.
* Kontrollera att dina regler för nätverkssäkerhetsgrupp för virtuella nätverk inte blockerar följande inkommande kommunikationsportar till Azure Database Migration Service: 443, 53, 9354, 445 och 12000. Mer information om filtrering av NSG-trafik i det virtuella nätverket finns i artikeln [Filtrera nätverkstrafik med nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Konfigurera [Din Windows-brandvägg](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) (eller din Linux-brandvägg) så att databasmotoråtkomsten kan komma. För MySQL-server kan port 3306 för anslutning.

> [!NOTE]
> Azure Database for MySQL stöder endast InnoDB-tabeller. Om du vill konvertera MyISAM-tabeller till InnoDB läser du artikeln [Konvertera tabeller från MyISAM till InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) .

### <a name="set-up-aws-rds-mysql-for-replication"></a>Konfigurera AWS RDS MySQL för replikering

1. Om du vill skapa en ny parametergrupp följer du instruktionerna från AWS i artikeln [MySQL Database Log Files](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html)i avsnittet **Binärt loggningsformat.**
2. Skapa en ny parametergrupp med följande konfiguration:
    * binlog_format = rad
    * binlog_checksum = INGEN
3. Spara den nya parametergruppen.
4. Associera den nya parametergruppen med RDS MySQL-instansen. En omstart kan krävas.

## <a name="migrate-the-schema"></a>Migrera schemat

1. Extrahera schemat från källdatabasen och tillämpas på måldatabasen för att slutföra migreringen av alla databasobjekt, till exempel tabellscheman, index och lagrade procedurer.

    Det enklaste sättet att migrera endast schemat är att använda mysqldump med parametern --no-data. Kommandot för att migrera schemat är:

    ```
    mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
    ```
    
    Om du till exempel vill dumpa en schemafil för databasen **Anställda** använder du följande kommando:
    
    ```
    mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
    ```

2. Importera schemat till måltjänsten, som är Azure Database för MySQL. Om du vill återställa schemadumpfilen kör du följande kommando:

    ```
    mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
    ```

    Så här importerar du schemat för databasen **Anställda:**

    ```
    mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
    ```

3. Om du har sekundärnycklar i ditt schema misslyckas den första inläsningen och den kontinuerliga synkroniseringen av migreringen. Om du vill extrahera skriptet för att släppa sekundärnyckel och lägga till skript för sekundärnyckel vid målet (Azure Database for MySQL) kör du följande skript i MySQL Workbench:

    ```
    SET group_concat_max_len = 8192;
        SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
        FROM
        (SELECT
        KCU.REFERENCED_TABLE_SCHEMA as SchemaName,
                    KCU.TABLE_NAME,
                    KCU.COLUMN_NAME,
                    CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
                    FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
                    WHERE
                      KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
                      AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries
      GROUP BY SchemaName;
    ```

4. Kör den bortsläppa sekundärnyckeln (som är den andra kolumnen) i frågeresultatet för att släppa sekundärnyckeln.

5. Om du har utlösare (infoga eller uppdatera utlösare) i data, kommer det att framtvinga dataintegritet i målet innan replikera data från källan. Rekommendationen är att inaktivera utlösare i alla tabeller *vid målet* under migreringen och sedan aktivera utlösare när migreringen är klar.

    Så här inaktiverar du utlösare i måldatabasen:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. Om det finns instanser av ENUM-datatypen i alla tabeller rekommenderar vi att du tillfälligt uppdaterar datatypen "teckenvarierande" i måltabellen. WHen-datareplikeringen är klar och återställ sedan datatypen till ENUM.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration

1. Logga in på Azure Portal och välj **Alla tjänster** och sedan **Prenumerationer**.

   ![Visa portalprenumerationer](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-subscription1.png)

2. Välj den prenumeration där du vill skapa instansen av Azure Database Migration Service och välj sedan **Resursleverantörer**.

    ![Visa resursprovidrar](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-resource-provider.png)

3. Sök efter migrering och välj sedan **Registrera**till höger om **Microsoft.DataMigration**.

    ![Registrera resursprovider](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Skapa en instans av Azure Database Migration Service

1. I Azure Portal väljer du + **Skapa en resurs**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Service** i listrutan.

    ![Azure Marketplace](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-marketplace.png)

2. På sidan **Azure Database Migration Service** väljer du **Skapa**.

    ![Skapa Azure Database Migration Service-instans](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create1.png)
  
3. På sidan **Create Migration Service** anger du ett namn för tjänsten, prenumerationen och en ny eller befintlig resursgrupp.

4. Välj den plats där du vill skapa instansen av Azure Database Migration Service.

5. Välj ett befintligt virtuellt nätverk eller skapa ett nytt.

    Det virtuella nätverket ger Azure Database Migration Service åtkomst till källan MySQL-instans och målet Azure Database för MySQL-instans.

    Mer information om hur du skapar ett virtuellt nätverk i Azure-portalen finns i artikeln [Skapa ett virtuellt nätverk med Azure-portalen](https://aka.ms/DMSVnet).

6. Välj en prisnivå. för den här onlinemigreringen, se till att välja prisnivån Premium: 4vCores.

    ![Konfigurera Azure Database Migration Service-instansinställningar](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-settings3.png)

7. Välj **Skapa** för att skapa tjänsten.

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När tjänsten har skapats letar du reda på den i Azure Portal, öppnar den och skapar sedan ett nytt migreringsprojekt.

1. I Azure Portal väljer du **Alla tjänster**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Services**.

      ![Leta reda på alla instanser Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-search.png)

2. På sidan **Azure Database Migration Services** söker du efter namnet på Azure Database Migration Service-instansen du har skapat och väljer sedan instansen.

     ![Hitta din instans av Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-instance-search.png)

3. Välj + **Nytt migreringsprojekt**.
4. På skärmen **Nytt migreringsprojekt** anger du ett namn för projektet, i textrutan **Källservertyp,** väljer **MySQL**och väljer sedan **AzureDbForMySQL**i textrutan Källaservertyp och välj sedan AzureDbForMySQL i **textrutan** Källaserver.
5. I avsnittet **Välj aktivitetstyp** väljer du **Datamigrering online**.

    > [!IMPORTANT]
    > Var noga med att välja **Online datamigrering;** offlinemigreringar stöds inte för det här scenariot.

    ![Skapa Database Migration Service-projekt](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create-project6.png)

    > [!NOTE]
    > Alternativt kan du **välja Skapa projekt bara** för att skapa migreringsprojektet nu och köra migreringen senare.

6. Välj **Spara**.

7. Välj **Skapa och kör aktivitet** för att skapa projektet och köra migreringsaktiviteten.

    > [!NOTE]
    > Anteckna de förutsättningar som krävs för att ställa in onlinemigrering i bladet för att skapa projekt.

## <a name="specify-source-details"></a>Ange källinformation

* På informationsskärmen för **migreringskällan** anger du anslutningsinformation för källan MySQL-instans.

   ![Källinformation](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Ange målinformation

1. Välj **Spara**och ange **Target details** sedan anslutningsinformation för målinformationen för Azure-servern för MySQL-servern, som är företablerade och har schemat **Anställda** distribuerat med MySQLDump.

    ![Välja mål](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-select-target5.png)

2. Välj **Spara** och mappa sedan på sidan **Mappa till måldatabaser** käll- och måldatabasen för migrering.

    Om måldatabasen innehåller samma databasnamn som källdatabasen väljer Azure Database Migration Service måldatabasen som standard.

    ![Mappa till måldatabaser](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-map-targets-activity5.png)

3. Välj **Spara**. I rutan **Aktivitetsnamn** på skärmen **Migreringssammanfattning** anger du ett namn för migreringsaktiviteten och granskar sedan sammanfattningen för att se till att informationen för källa och mål matchar det du angav tidigare.

    ![Migreringssammanfattning](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-migration-summary2.png)

## <a name="run-the-migration"></a>Köra migreringen

* Välj **Kör migrering**.

    Migreringsaktivitetsfönstret visas och **Status** för aktiviteten är **Initieras**.

## <a name="monitor-the-migration"></a>Övervaka migreringen

1. På migreringsaktivitetssidan väljer du **Uppdatera** för att uppdatera visningen tills **Status** för migreringen är **Körs**.

    ![Aktivitetsstatus - körs](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-activity-status4.png)

2. Under **DATABASNAMN**väljer du en specifik databas för att komma åt migreringsstatus för **fullständiga datainläsningar** och **inkrementella datasynkroniseringsåtgärder.**

    **Fullständig datainläsning** visar den ursprungliga belastningsmigreringsstatusen, medan **inkrementell datasynkronisering** visar status för ändringsdatainsamling (CDC).

    ![Lagerskärm - fullständig databelastning](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-full-load.png)

    ![Lagerskärm - inkrementell datasynkronisering](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Utföra snabbmigrering

När den första fullständiga inläsningen är klar markeras databaserna **Redo att klippa över**.

1. När du är redo att slutföra databasmigreringen väljer du **Starta snabb**.

    ![Börja skära över](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-start-cutover.png)

2. Stoppa alla inkommande transaktioner till källdatabasen och vänta tills **Väntande ändringar** visar **0**.
3. Välj **Bekräfta** och sedan **Apply** (Använd).
4. När databasmigreringsstatusen visar **Slutförd**ansluter du dina program till den nya Azure-databasen för MySQL-databasen.

Din onlinemigrering av en lokal instans av MySQL till Azure Database för MySQL är nu klar.

## <a name="next-steps"></a>Nästa steg

* Mer information om Azure Database Migration Service finns i artikeln [What is the Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview) (Vad är Azure Database Migration Service?).
* Information om Azure Database för MySQL finns i artikeln [Vad är Azure Database för MySQL?](https://docs.microsoft.com/azure/mysql/overview).
* Om du vill ha andra frågor kan du skicka alias Ask [Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com) till e-post.
