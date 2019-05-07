---
title: 'Självstudier: Använd Azure Database Migration Service för en online-migrering av RDS MySQL till Azure Database för MySQL | Microsoft Docs'
description: Lär dig att utföra en online-migrering från RDS MySQL till Azure Database för MySQL med hjälp av Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 05/01/2019
ms.openlocfilehash: 9aadfd1f3685466c8c0beb6dff3bb8d063f4bfd8
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65137408"
---
# <a name="tutorial-migrate-rds-mysql-to-azure-database-for-mysql-online-using-dms"></a>Självstudier: Migrera RDS MySQL till Azure Database för MySQL med hjälp av DMS

Du kan använda Azure Database Migration Service för att migrera databaser från en RDS MySQL-instans till [Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) medan källdatabasen förblir online under migreringen. Migrering kan med andra ord ske med minimalt avbrott för programmet. I den här självstudien får du migrera den **anställda** exempeldatabasen från en instans av RDS MySQL till Azure Database för MySQL med hjälp av aktiviteten onlinemigrering i Azure Database Migration Service.

I den här guiden får du lära dig att:
> [!div class="checklist"]
>
> * Migrera exempel schemat genom att använda verktygen mysqldump och mysql.
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsprojekt med Azure Database Migration Service.
> * Köra migreringen.
> * Övervaka migreringen.

> [!NOTE]
> Användning av Azure Database Migration Service för att utföra en onlinemigrering kräver att en instans skapas baserad på prisnivån Premium. Mer information finns i på [prissättningssidan](https://azure.microsoft.com/pricing/details/database-migration/) för Azure Database Migration Service.

> [!IMPORTANT]
> För optimala migreringsfunktioner rekommenderar Microsoft att skapa en instans av Azure Database Migration Service i samma Azure-region som måldatabasen. Att flytta data mellan regioner eller geografiska områden kan göra migreringsprocessen långsammare och leda till fel.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Den här artikeln beskriver hur du utför en online-migrering från en instans av RDS MySQL till Azure Database för MySQL.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här kursen behöver du:

* Kontrollera att käll-MySQL-servern kör en stöds MySQL community edition. Om du vill kontrollera vilken version av MySQL-instans i mysql-verktyget eller MySQL Workbench, kör du kommandot:

    ```
    SELECT @@version;
    ```

    Mer information finns i artikeln [stöds Azure Database för MySQL-versioner](https://docs.microsoft.com/azure/mysql/concepts-supported-versions).

* Ladda ned och installera den [MySQL **anställda** exempeldatabasen](https://dev.mysql.com/doc/employee/en/employees-installation.html).
* Skapa en instans av [Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal).
* Skapa ett Azure-nätverk (VNet) för Azure Database Migration Service med hjälp av Azure Resource Manager-distributionsmodellen, som tillhandahåller plats-till-plats-anslutning till dina lokala källservrar genom att använda antingen [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Se till att dina regler för Nätverkssäkerhetsgrupp kopplad till virtuella nätverk inte blockerar följande portar för inkommande kommunikation till Azure Database Migration Service: 443, 53, 9354, 445 och 12000. Mer information om Azure VNet NSG-trafikfiltrering finns i artikeln [filtrera nätverkstrafik med nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Konfigurera din [Windows-brandvägg för databasmotoråtkomst](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Öppna din Windows-brandväggen så att Azure Database Migration Service åtkomst till MySQL källservern, vilket som standard är TCP-port 3306.
* När du använder en brandväggsinstallation framför dina källdatabaser kanske du måste lägga till brandväggsregler för att tillåta Azure Database Migration Service att komma åt källdatabaserna för migrering.
* Skapa en servernivå [brandväggsregel](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) för Azure Database for MySQL-server att tillåta Azure Database Migration Service åtkomst till måldatabaserna. Ange undernätsintervallet för det virtuella nätverket som används för Azure Database Migration Service.

> [!NOTE]
> Azure Database för MySQL stöder bara InnoDB-tabeller. Om du vill konvertera MyISAM tabeller till InnoDB, finns i artikeln [Konvertera tabeller till InnoDB från MyISAM](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) .

### <a name="set-up-aws-rds-mysql-for-replication"></a>Konfigurera AWS RDS MySQL för replikering

1. Om du vill skapa en ny parametergrupp, följer du instruktionerna från AWS i artikeln [MySQL-databasloggfiler](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html)i den **binärt loggningsformat** avsnittet.
2. Skapa en ny parametergrupp med följande konfiguration:
    * binlog_format = rad
    * binlog_checksum = NONE
3. Spara den nya parametergruppen.

## <a name="migrate-the-schema"></a>Migrera schemat

1. Extrahera schemat från källdatabasen och tillämpa på måldatabasen att slutföra migreringen av alla databasobjekt som tabellscheman, index och lagrade procedurer.

    Det enklaste sättet att migrera endast schemat är att använda mysqldump med parametern--no-data. För att migrera schemat är:

    ```
    mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
    ```
    
    Till exempel för att dumpa en schemafil för den **anställda** databasen, använder du följande kommando:
    
    ```
    mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
    ```

2. Importera schemat till Måltjänsten, som är Azure Database for MySQL. Om du vill återställa dumpfilen schemat, kör du följande kommando:

    ```
    mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
    ```

    Till exempel för att importera schemat för den **anställda** databasen:

    ```
    mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
    ```

3. Om du har sekundärnycklar i ditt schema misslyckas den första inläsningen och den kontinuerliga synkroniseringen av migreringen. För att extrahera skriptet släpp främmande nycklar och Lägg till främmande nycklar skript vid målet (Azure Database för MySQL), kör du följande skript i MySQL Workbench:

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
      AND KCU.REFERENCED_TABLE_SCHEMA = ['SchemaName') Queries
      GROUP BY SchemaName;
    ```

4. Kör drop-sekundärnyckeln (vilket är den andra kolumnen) i frågeresultatet att släppa den externa nyckeln.

5. Om du har utlösare (insert eller update utlösa) i data framtvingar den dataintegriteten i målet innan du replikerar data från källan. Rekommendationen är att inaktivera utlösare i alla tabeller *på angivna* under migreringen och sedan aktivera utlösare när migreringen är slutföra.

    Inaktivera utlösare i måldatabasen:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. Om instanser av typen ENUM data i tabeller, rekommenderar vi uppdaterar tillfälligt till tecknet varierande datatypen i måltabellen. När replikering av data är klar återställer du datatypen som ENUM.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration

1. Logga in på Azure-portalen och välj **Alla tjänster** och sedan **Prenumerationer**.

   ![Visa portalprenumerationer](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-subscription1.png)

2. Välj den prenumeration som du vill skapa en instans av Azure Database Migration Service och välj sedan **resursprovidrar**.

    ![Visa resursprovidrar](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-resource-provider.png)

3. Sök efter migreringen och välj sedan **Registrera** till höger om **Microsoft.DataMigration**.

    ![Registrera resursprovider](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Skapa en instans av Azure Database Migration Service

1. I Azure Portal väljer du + **Skapa en resurs**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Service** i listrutan.

    ![Azure Marketplace](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-marketplace.png)

2. På sidan **Azure Database Migration Service** väljer du **Skapa**.

    ![Skapa Azure Database Migration Service-instans](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create1.png)
  
3. På sidan **Create Migration Service** anger du ett namn för tjänsten, prenumerationen och en ny eller befintlig resursgrupp.

4. Välj den plats där du vill skapa en instans av Azure Database Migration Service.

5. Välj ett befintligt virtuellt nätverk eller skapa en ny.

    Det virtuella nätverket ger Azure Database Migration Service åtkomst till källan MySQL-instans och Azure-måldatabas för MySQL-instans.

    Mer information om hur du skapar ett virtuellt nätverk i Azure-portalen finns i artikeln [skapa ett virtuellt nätverk med Azure portal](https://aka.ms/DMSVnet).

6. Välj en prisnivå; för den här onlinemigrering vara noga med att välja Premium: 4vCores prisnivå.

    ![Konfigurera Azure Database Migration Service-instansinställningar](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-settings3.png)

7. Välj **Skapa** för att skapa tjänsten.

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När tjänsten har skapats letar du reda på den i Azure Portal, öppnar den och skapar sedan ett nytt migreringsprojekt.

1. I Azure Portal väljer du **Alla tjänster**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Services**.

      ![Leta reda på alla instanser Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-search.png)

2. På sidan **Azure Database Migration Services** söker du efter namnet på Azure Database Migration Service-instansen du har skapat och väljer sedan instansen.

     ![Hitta din instans av Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-instance-search.png)

3. Välj + **Nytt migreringsprojekt**.
4. På den **nytt migreringsprojekt** skärmen, ange ett namn för projektet, i den **typ av källserver** textrutan **MySQL**, och sedan i den **mål servertyp** textrutan **AzureDbForMySQL**.
5. I avsnittet **Välj aktivitetstyp** väljer du **Online-datamigrering**.

    > [!IMPORTANT]
    > Se till att välja **Online datamigrering**; offline migreringar stöds inte för det här scenariot.

    ![Skapa Database Migration Service-projekt](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create-project6.png)

    > [!NOTE]
    > Alternativt kan du välja **skapa projekt endast** att skapa migreringsprojektet nu och köra migreringen senare.

6. Välj **Spara**.

7. Välj **Skapa och kör aktivitet** för att skapa projektet och köra migreringsaktiviteten.

    > [!NOTE]
    > Kontrollera en anteckning i de obligatoriska stegen som behövs för att ställa in onlinemigrering i bladet för att skapa projektet.

## <a name="specify-source-details"></a>Ange källinformation

* På den **migreringskällinformation** skärmen, Ange anslutningsinformation för MySQL-källinstansen.

   ![Källinformation](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Ange målinformation

1. Välj **spara**, och klicka sedan på den **rikta information** skärmen, Ange anslutningsinformation för Azure-måldatabas för MySQL-server som redan är etablerat och har den **anställda** schema distribuerat Använd MySQLDump.

    ![Välja mål](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-select-target5.png)

2. Välj **Spara** och mappa sedan på sidan **Mappa till måldatabaser** käll- och måldatabasen för migrering.

    Om måldatabasen innehåller samma databasnamn som källdatabasen, väljer Azure Database Migration Service måldatabasen som standard.

    ![Mappa till måldatabaser](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-map-targets-activity5.png)

3. Välj **Spara**. I rutan **Aktivitetsnamn** på skärmen **Migreringssammanfattning** anger du ett namn för migreringsaktiviteten och granskar sedan sammanfattningen för att se till att informationen för källa och mål matchar det du angav tidigare.

    ![Migreringssammanfattning](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-migration-summary2.png)

## <a name="run-the-migration"></a>Köra migreringen

* Välj **Kör migrering**.

    Migreringsaktivitetsfönstret visas och **Status** för aktiviteten är **Initieras**.

## <a name="monitor-the-migration"></a>Övervaka migreringen

1. På migreringsaktivitetssidan väljer du **Uppdatera** för att uppdatera visningen tills **Status** för migreringen är **Körs**.

    ![Aktivitetsstatus - körs](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-activity-status4.png)

2. Under **DATABASNAMN**, Välj en specifik databas att komma till Migreringsstatus för **fullständiga Datainläsningen** och **inkrementell datasynkronisering** åtgärder.

    **Fullständig datainläsning** visar Migreringsstatus första medan **inkrementell datasynkronisering** visar ändra status för data capture (CDC).

    ![Läsa in inventering skärm – fullständig](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-full-load.png)

    ![Inventering skärm – inkrementell datasynkronisering](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Utföra snabbmigrering

När inledande hela inläsningen är klar databaserna markeras **redo att Cutover**.

1. När du är redo att slutföra databasmigreringen väljer du **Starta snabb**.

    ![Klipp ut börja om från början](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-start-cutover.png)

2. Stoppa alla inkommande transaktioner till källdatabasen och vänta tills **Väntande ändringar** visar **0**.
3. Välj **Bekräfta** och sedan **Apply** (Använd).
4. När databasmigreringsstatus visas **slutförd**, ansluta dina program till nya Azure-måldatabas för MySQL-databas.

Online migreringen av en lokal instans av MySQL till Azure Database för MySQL är slutförd.

## <a name="next-steps"></a>Nästa steg

* Mer information om Azure Database Migration Service finns i artikeln [What is the Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview) (Vad är Azure Database Migration Service?).
* Information om Azure Database för MySQL, finns i artikeln [vad är Azure Database for MySQL?](https://docs.microsoft.com/azure/mysql/overview).
* Andra frågor kan du e-postmeddelande den [be Azure Databasmigreringar](mailto:AskAzureDatabaseMigrations@service.microsoft.com) alias.
