---
title: 'Självstudier: Använda Azure Database Migration Service för att utföra en online migrering av Oracle till Azure Database för PostgreSQL | Microsoft Docs'
description: Lär dig att utföra en online-migrering från Oracle på plats eller på virtuella datorer till Azure Database för PostgreSQL med hjälp av Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 6f94fa8b5c0d972d9cdbe86c480a712f7e44c29f
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65158338"
---
# <a name="tutorial-migrate-oracle-to-azure-database-for-postgresql-online-using-dms-preview"></a>Självstudier: Migrera Oracle till Azure Database för PostgreSQL med hjälp av DMS (förhandsversion)

Du kan använda Azure Database Migration Service för att migrera databaser från Oracle-databaser som finns lokalt eller på virtuella datorer till [Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/) med minimal avbrottstid. Med andra ord kan du slutföra migreringen med minimalt avbrott för programmet. I den här självstudien får du migrera den **HR** exempeldatabasen från en lokal eller VM-instanser av Oracle 11 g till Azure Database för PostgreSQL med hjälp av aktiviteten onlinemigrering i Azure Database Migration Service.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Utvärdera det enklare för migrering med hjälp av verktyget ora2pg.
> * Migrera med hjälp av verktyget ora2pg exempel schemat.
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsprojekt med Azure Database Migration Service.
> * Köra migreringen.
> * Övervaka migreringen.

> [!NOTE]
> Med hjälp av Azure Database Migration Service för att utföra en online-migrering måste du skapa en instans baserat på premiumprisnivån.

> [!IMPORTANT]
> För en optimal migreringsupplevelse Microsoft rekommenderar att skapa en instans av Azure Database Migration Service i samma Azure-region som måldatabasen. Att flytta data mellan regioner eller geografiska områden kan göra migreringsprocessen långsammare och leda till fel.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Den här artikeln beskriver hur du utför en online-migrering från Oracle till Azure Database för PostgreSQL.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här kursen behöver du:

* Ladda ned och installera [Oracle 11g version 2 (Standard Edition, en Standard Edition eller Enterprise Edition)](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html).
* Ladda ned exemplet **HR** databasen från [här](https://docs.oracle.com/database/121/COMSC/installation.htm#COMSC00002).
* Hämta och installera ora2pg på antingen [Windows](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows.pdf) eller [Linux](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Linux.pdf).
* [Skapa en instans i Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal).
* Ansluta till instansen och skapa en databas med hjälp av anvisningarna i det här [dokumentet](https://docs.microsoft.com/azure/postgresql/tutorial-design-database-using-azure-portal).
* Skapa ett Azure-nätverk (VNet) för Azure Database Migration Service med hjälp av Azure Resource Manager-distributionsmodellen, som tillhandahåller plats-till-plats-anslutning till dina lokala källservrar genom att använda antingen [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Mer information om hur du skapar ett virtuellt nätverk finns i den [dokumentation om virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/), och särskilt artiklarna i snabbstarten, som innehåller stegvis information.

  > [!NOTE]
  > Under installationen av virtuellt nätverk, om du använder ExpressRoute med nätverks-peering till Microsoft, lägger du till följande tjänst [slutpunkter](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) till undernätet där tjänsten ska etableras:
  > * Mål-database-slutpunkten (till exempel SQL-slutpunkten och Cosmos DB-slutpunkt)
  > * Slutpunkt för lagring
  > * Service bus-slutpunkt
  >
  > Den här konfigurationen är nödvändigt eftersom Azure Database Migration Service saknar Internetanslutning.

* Se till att dina VNet Nätverkssäkerhetsgrupp (NSG)-regler inte blockerar följande portar för inkommande kommunikation till Azure Database Migration Service: 443, 53, 9354, 445, 12000. Mer information om Azure VNet NSG-trafikfiltrering finns i artikeln [filtrera nätverkstrafik med nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Konfigurera din [Windows-brandvägg för databasmotoråtkomst](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Öppna din Windows-brandväggen så att Azure Database Migration Service åtkomst till Oracle källservern, vilket som standard är TCP-port 1521.
* När du använder en brandväggsinstallation framför din källdatabaserna, kan du behöva lägga till brandväggsregler som tillåter Azure Database Migration Service åtkomst till källdatabaserna för migrering.
* Skapa en servernivå [brandväggsregel](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) för Azure Database for PostgreSQL som ger Azure Database Migration Service åtkomst till måldatabaserna. Ange undernätsintervallet för det virtuella nätverket som används för Azure Database Migration Service.
* Aktivera åtkomst till källan Oracle-databaser.

  > [!NOTE]
  > DBA-rollen krävs för en användare att ansluta till Oracle-källan.

  * Arkivet gör om loggarna måste anges för inkrementell synkronisering i Azure Database Migration Service för att samla in data ändras. Följ dessa steg om du vill konfigurera Oracle-källa:
    * Logga in med SYSDBA behörighet genom att köra följande kommando:

      ```
      sqlplus (user)/(password) as sysdba
      ```

    * Stäng av databasinstansen genom att köra följande kommando.

      ```
      SHUTDOWN IMMEDIATE;
      ```

      Vänta tills bekräftelsen `'ORACLE instance shut down'`.

    * Starta ny instans och montera (men inte öppna) databasen för att aktivera eller inaktivera arkivering bu köra följande kommando:

      ```
      STARTUP MOUNT;
      ```

      Databasen måste monteras; Vänta på bekräftelse 'Oracle instance igång'.

    * Ändra databasen Arkivera läge genom att köra följande kommando:

      ```
      ALTER DATABASE ARCHIVELOG;
      ```

    * Öppna databasen för normal drift genom att köra följande kommando:

      ```
      ALTER DATABASE OPEN;
      ```

      Du kan behöva starta om för filen BÅGE visas.

    * Du kan kontrollera genom att köra följande kommando:

      ```
      SELECT log_mode FROM v$database;
      ```

      Du bör få ett svar `'ARCHIVELOG'`. Om svaret är `'NOARCHIVELOG'`, och sedan kraven inte uppfylls.

  * Aktivera ytterligare loggning för replikering med någon av följande alternativ.

    * **Alternativ 1**.
      Ändra databas på kompletterande loggning för att täcka alla tabeller med PK och unika index. Identifiering av frågan returnerar `'IMPLICIT'`.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY, UNIQUE) COLUMNS;
      ```

      Ändra tabellnivå kompletterande loggning. Kör bara för tabeller som har datamanipulering och inte har PKs eller unika index.

      ```
      ALTER TABLE [TABLENAME] ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    * **Alternativ 2**.
      Ändra databas på kompletterande loggning för att täcka alla tabeller och identifiering av frågan returnerar `'YES'`.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
      ```

      Ändra tabellnivå kompletterande loggning. Följ logik nedan för att köra endast en instruktion för varje tabell.

      Om tabellen har en primär nyckel:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
      ```

      Om tabellen har ett unikt index:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG GROUP (first unique index columns) ALWAYS;
      ```

      Annars kör du följande kommando:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    Du kan kontrollera genom att köra följande kommando:

      ```
      SELECT supplemental_log_data_min FROM v$database;
      ```

    Du bör få ett svar `'YES'`.

> [!IMPORTANT]
> För den offentliga förhandsversionen av det här scenariot, Azure Database Migration Service har stöd för Oracle-version 10g eller 11g. Kunder som kör Oracle-version 12 c eller senare Observera att måste det lägsta autentiseringsprotokollet som tillåts för ODBC-drivrutinen att ansluta till Oracle vara 8. För en Oracle-källa som är version 12c eller senare, måste du konfigurera autentiseringsprotokollet på följande sätt:
>
> * Uppdatera SQLNET. ORA:
>
>    ```
>    SQLNET.ALLOWED_LOGON_VERSION_CLIENT = 8
>    SQLNET.ALLOWED_LOGON_VERSION_SERVER = 8
>    ```
>
> * Starta om datorn för de nya inställningarna ska börja gälla.
> * Ändra lösenordet för befintliga användare:
>
>    ```
>    ALTER USER system IDENTIFIED BY {pswd}
>    ```
>
>   Mer information finns på sidan [här](http://www.dba-oracle.com/t_allowed_login_version_server.htm).
>
> Slutligen, Kom ihåg att ändra autentiseringsprotokollet kan påverka klientautentisering.

## <a name="assess-the-effort-for-an-oracle-to-azure-database-for-postgresql-migration"></a>Utvärdera enklare för en Oracle till Azure Database för PostgreSQL-migrering

Vi rekommenderar att du använder ora2pg för att utvärdera det enklare att migrera från Oracle till Azure Database för PostgreSQL. Använd den `ora2pg -t SHOW_REPORT` direktiv för att skapa en rapport som visar alla Oracle-objekt, beräknade migrering kostnaden (i dagar för utvecklare) och vissa databasobjekt som kan kräva särskild uppmärksamhet som en del av konverteringen.

De flesta kunder kan ta mycket tid granska utvärderingsrapporten och funderar på att arbetet som automatisk och manuell konvertering.

Om du vill konfigurera och köra ora2pg för att skapa en rapport, se den **Premigration: Utvärdering av** delen av den [Oracle till Azure Database för PostgreSQL Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf). En exempelrapport ora2pg utvärdering är tillgänglig för referens [här](http://ora2pg.darold.net/report.html).

## <a name="export-the-oracle-schema"></a>Exportera Oracle-schema

Vi rekommenderar att du använder ora2pg för att konvertera Oracle-schemat och andra Oracle-objekt (typer, procedurer, funktioner, osv.) till ett schema som är kompatibel med Azure Database for PostgreSQL. ora2pg innehåller många direktiv hjälper dig att definiera inför vissa datatyper. Du kan till exempel använda den `DATA_TYPE` direktiv att ersätta alla NUMBER(*,0) med bigint i stället för NUMERIC(38).

Du kan köra ora2pg för att exportera varje objekt i databasen i SQL-filer. Du kan granska SQL-filer innan du importerar dem till Azure Database for PostgreSQL med psql eller du kan köra den. SQL-skript i PgAdmin.

```
psql -f [FILENAME] -h [AzurePostgreConnection] -p 5432 -U [AzurePostgreUser] -d database 
```

Exempel:

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database
```

Om du vill konfigurera och köra ora2pg för schemakonverteringen, se den **migrering: Schema och data** delen av den [Oracle till Azure Database för PostgreSQL Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf).

## <a name="set-up-the-schema-in-azure-database-for-postgresql"></a>Konfigurera schemat i Azure Database för PostgreSQL

Som standard sparas Oracle schema.table.column i samtliga fall för övre, medan PostgreSQL håller schema.table.column i gemener. Azure Database Migration Service att starta dataförflyttning från Oracle till Azure Database för PostgreSQL, måste schema.table.column vara samma ärende format som Oracle-källa.

Exempel: om Oracle-källan har som schemat för ”timme” ”. ANSTÄLLDA ””. EMPLOYEE_ID ”och PostgreSQL-schemat måste använda samma format.

För att säkerställa att schema.table.column case formatet är samma för Oracle- och Azure Database for PostgreSQL, rekommenderar vi att du använder följande steg.

> [!NOTE]
> Du kan använda en annan metod för att härleda versaler schemat. Vi arbetar för att förbättra och automatisera det här steget.

1. Exportera scheman med ora2pg med lägre fall. I tabellen skapa sql-skript skapar du ett schema med versal ”SCHEMA” manuellt.
2. Importera resten av Oracle-objekt, till exempel utlösare, sekvenser, procedurer, typer och funktioner, till Azure Database för PostgreSQL.
3. För att se tabellen och kolumnen övre, kör du följande skript:

   ```
   -- INPUT: schema name
   set schema.var = “HR”;

   -- Generate statements to rename tables and columns
   SELECT 1, 'SET search_path = "' ||current_setting('schema.var')||'";'
   UNION ALL 
   SELECT 2, 'alter table "'||c.relname||'" rename '||a.attname||' to "'||upper(a.attname)||'";'
   FROM pg_class c
   JOIN pg_attribute a ON a.attrelid = c.oid
   JOIN pg_type t ON a.atttypid = t.oid
   LEFT JOIN pg_catalog.pg_constraint r ON c.oid = r.conrelid
    AND r.conname = a.attname
   WHERE c.relnamespace = (select oid from pg_namespace where nspname=current_setting('schema.var')) AND a.attnum > 0 AND c.relkind ='r'
   UNION ALL
   SELECT 3, 'alter table '||c.relname||' rename to "'||upper(c.relname)||'";'
   FROM pg_catalog.pg_class c
    LEFT JOIN pg_catalog.pg_namespace n ON n.oid = c.relnamespace
   WHERE c.relkind ='r' AND n.nspname=current_setting('schema.var')
   ORDER BY 1;
   ```

* Ta bort sekundärnyckeln i måldatabasen att köra hela inläsningen. Referera till den **migrera exempelschema** i artikeln [här](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online) för ett skript som du kan använda för att släppa den externa nyckeln.
* Använd Azure Database Migration Service för att köra för full belastning och sync.
* När data i Azure-måldatabas för PostgreSQL-instans är uppdateringar om källan, utföra databas startpunkt i Azure Database Migration Service.
* Att göra schemat, TABLE och COLUMN gemen (om schemat för Azure Database for PostgreSQL bör vara det här sättet för application fråga), kör följande skript:

  ```
  -- INPUT: schema name
  set schema.var = hr;
  
  -- Generate statements to rename tables and columns
  SELECT 1, 'SET search_path = "' ||current_setting('schema.var')||'";'
  UNION ALL
  SELECT 2, 'alter table "'||c.relname||'" rename "'||a.attname||'" to '||lower(a.attname)||';'
  FROM pg_class c
  JOIN pg_attribute a ON a.attrelid = c.oid
  JOIN pg_type t ON a.atttypid = t.oid
  LEFT JOIN pg_catalog.pg_constraint r ON c.oid = r.conrelid
     AND r.conname = a.attname
  WHERE c.relnamespace = (select oid from pg_namespace where nspname=current_setting('schema.var')) AND a.attnum > 0 AND c.relkind ='r'
  UNION ALL
  SELECT 3, 'alter table "'||c.relname||'" rename to '||lower(c.relname)||';'
  FROM pg_catalog.pg_class c
     LEFT JOIN pg_catalog.pg_namespace n ON n.oid = c.relnamespace
  WHERE c.relkind ='r' AND n.nspname=current_setting('schema.var')
  ORDER BY 1;
  ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration

1. Logga in på Azure-portalen och välj **Alla tjänster** och sedan **Prenumerationer**.

   ![Visa portalprenumerationer](media/tutorial-oracle-azure-postgresql-online/portal-select-subscriptions.png)

2. Välj den prenumeration där du vill skapa en instans av Azure Database Migration Service och välj sedan **Resursprovidrar**.

    ![Visa resursprovidrar](media/tutorial-oracle-azure-postgresql-online/portal-select-resource-provider.png)

3. Sök efter migreringen och välj sedan **Registrera** till höger om **Microsoft.DataMigration**.

    ![Registrera resursprovider](media/tutorial-oracle-azure-postgresql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Skapa en DMS-instans

1. I Azure Portal väljer du + **Skapa en resurs**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Service** i listrutan.

    ![Azure Marketplace](media/tutorial-oracle-azure-postgresql-online/portal-marketplace.png)

2. På sidan **Azure Database Migration Service** väljer du **Skapa**.

    ![Skapa Azure Database Migration Service-instans](media/tutorial-oracle-azure-postgresql-online/dms-create2.png)
  
3. På sidan **Create Migration Service** anger du ett namn för tjänsten, prenumerationen och en ny eller befintlig resursgrupp.

4. Välj ett befintligt virtuellt nätverk eller skapa en ny.

    Det virtuella nätverket tillhandahåller Azure Database Migration Service med åtkomst till den Oracle-källan och målet Azure Database för PostgreSQL-instans.

    Mer information om hur du skapar ett virtuellt nätverk i Azure-portalen finns i artikeln [skapa ett virtuellt nätverk med Azure portal](https://aka.ms/DMSVnet).

5. Välj en prisnivå.

    Mer information om kostnader och prisnivåer finns på [sidan med priser](https://aka.ms/dms-pricing).

    ![Konfigurera Azure Database Migration Service-instansinställningar](media/tutorial-oracle-azure-postgresql-online/dms-settings5.png)

6. Välj **Skapa** för att skapa tjänsten.

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När tjänsten har skapats letar du reda på den i Azure Portal, öppnar den och skapar sedan ett nytt migreringsprojekt.

1. I Azure Portal väljer du **Alla tjänster**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Services**.

    ![Leta reda på alla instanser Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-search.png)

2. På sidan **Azure Database Migration Services** söker du efter namnet på Azure Database Migration Service-instansen du har skapat och väljer sedan instansen.

    ![Hitta din instans av Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-instance-search.png)

3. Välj + **Nytt migreringsprojekt**.
4. På den **nytt migreringsprojekt** skärmen, ange ett namn för projektet, i den **typ av källserver** textrutan **Oracle**i den **Målservertyp**  textrutan **Azure Database for PostgreSQL**.
5. I avsnittet **Välj aktivitetstyp** väljer du **Online-datamigrering**.

   ![Skapa Database Migration Service-projekt](media/tutorial-oracle-azure-postgresql-online/dms-create-project5.png)

   > [!NOTE]
   > Alternativt kan du välja **skapa projekt endast** att skapa migreringsprojektet nu och köra migreringen senare.

6. Välj **spara**, Tänk på kraven för att kunna använda Azure Database Migration Service för att utföra en online-migrering och välj sedan **skapa och kör aktiviteten**.

## <a name="specify-source-details"></a>Ange källinformation

* På den **Lägg till källinformation** skärmen, Ange anslutningsinformation för Oracle-instansen källa.

  ![Skärmen Lägg till källinformation](media/tutorial-oracle-azure-postgresql-online/dms-add-source-details.png)

## <a name="upload-oracle-oci-driver"></a>Ladda upp OCI Oracle-drivrutinen

1. Välj **spara**, och klicka sedan på den **installera OCI drivrutinen** skärmen, logga in på Oracle-konto och hämta drivrutinen **instantclient-basiclite-windows.x64-12.2.0.1.0.zip**(37,128,586 Byte(s)) (SHA1 kontrollsumma: 865082268) från [här](https://www.oracle.com/technetwork/topics/winx64soft-089540.html#ic_winx64_inst).
2. Hämta drivrutinen till en delad mapp.

   Kontrollera att mappen delas med det användarnamn som du angav med minsta skrivskyddad åtkomst. Azure Database Migration Service har åtkomst till och läser från resursen överför OCI drivrutinen till Azure genom att personifiera det användarnamn som du anger.

   Användarnamnet som du anger måste vara ett Windows-användarkonto.

   ![OCI drivrutinsinstallation](media/tutorial-oracle-azure-postgresql-online/dms-oci-driver-install.png)

## <a name="specify-target-details"></a>Ange målinformation

1. Välj **spara**, och klicka sedan på den **rikta information** skärmen, Ange anslutningsinformation för målet Azure Database for PostgreSQL-server som är färdig instansen av Azure Database for PostgreSQL som den **HR** schemat distribuerades.

    ![Skärmen Målinformation](media/tutorial-oracle-azure-postgresql-online/dms-add-target-details1.png)

2. Välj **Spara** och mappa sedan på sidan **Mappa till måldatabaser** käll- och måldatabasen för migrering.

    Om måldatabasen innehåller samma databasnamn som källdatabasen, väljer Azure Database Migration Service måldatabasen som standard.

    ![Mappa till måldatabaser](media/tutorial-oracle-azure-postgresql-online/dms-map-target-details.png)

3. Välj **Spara**. I rutan **Aktivitetsnamn** på skärmen **Migreringssammanfattning** anger du ett namn för migreringsaktiviteten och granskar sedan sammanfattningen för att se till att informationen för källa och mål matchar det du angav tidigare.

    ![Migreringssammanfattning](media/tutorial-oracle-azure-postgresql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Köra migreringen

* Välj **Kör migrering**.

  Migreringsaktivitetsfönstret visas och **Status** för aktiviteten är **Initieras**.

## <a name="monitor-the-migration"></a>Övervaka migreringen

1. På migreringsaktivitetssidan väljer du **Uppdatera** för att uppdatera visningen tills **Status** för migreringen är **Körs**.

     ![Aktivitetsstatus - körs](media/tutorial-oracle-azure-postgresql-online/dms-activity-running.png)

2. Under **databasnamn**, Välj en specifik databas att komma till Migreringsstatus för **fullständiga Datainläsningen** och **inkrementell datasynkronisering** åtgärder.

    Fullständig datainläsning visar den initiala inläsningsmigreringsstatus, och Inkrementell datasynkronisering visar CDC-status (change data capture).

     ![Aktivitetsstatus – Fullständig inläsning slutförd](media/tutorial-oracle-azure-postgresql-online/dms-activity-full-load-completed.png)

     ![Aktivitetsstatus – Inkrementell datasynkronisering](media/tutorial-oracle-azure-postgresql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Utföra snabbmigrering

När den fullständiga inläsningen är klar är databaserna märkta med **Klar för snabbmigrering**.

1. När du är redo att slutföra databasmigreringen väljer du **Starta snabb**.

2. Stoppa alla inkommande transaktioner till källdatabasen och vänta tills **Väntande ändringar** visar **0**.

   ![Starta snabb](media/tutorial-oracle-azure-postgresql-online/dms-start-cutover.png)

3. Välj **Bekräfta** och sedan **Använd**.
4. När databasmigreringsstatus visas **slutförd**, ansluta dina program till det nya målet Azure Database för PostgreSQL-instans.

 > [!NOTE]
 > Eftersom PostgreSQL som standard har schema.table.column i gemener, du kan återställa från versaler till gemener med skriptet i den **konfigurera schemat i Azure Database för PostgreSQL** tidigare i den här artikeln.

## <a name="next-steps"></a>Nästa steg

* Information om kända problem och begränsningar när du utför onlinemigreringar till Azure Database for PostgreSQL finns i artikeln [Kända problem och lösningar för Azure Database for PostgreSQL-onlinemigreringar](known-issues-azure-postgresql-online.md).
* Mer information om Azure Database Migration Service finns i artikeln [What is the Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview) (Vad är Azure Database Migration Service?).
* Information om Azure Database för PostgreSQL, finns i artikeln [vad är Azure Database för PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview).
