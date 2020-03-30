---
title: 'Självstudiekurs: Migrera Oracle online till Azure Database för PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Lär dig att utföra en onlinemigrering från Oracle lokalt eller på virtuella datorer till Azure Database för PostgreSQL med hjälp av Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/24/2020
ms.openlocfilehash: 956523e2b51795a4bc97c653dab8b408b06061f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255568"
---
# <a name="tutorial-migrate-oracle-to-azure-database-for-postgresql-online-using-dms-preview"></a>Självstudiekurs: Migrera Oracle till Azure Database för PostgreSQL online med DMS (Preview)

Du kan använda Azure Database Migration Service för att migrera databaser från Oracle-databaser som finns lokalt eller på virtuella datorer till [Azure Database för PostgreSQL](https://docs.microsoft.com/azure/postgresql/) med minimal stilleståndstid. Med andra ord kan du slutföra migreringen med minimal stilleståndstid till programmet. I den här självstudien migrerar du **exempeldatabasen** för HR från en lokal eller virtuell datorinstans av Oracle 11g till Azure Database för PostgreSQL med hjälp av onlinemigreringsaktiviteten i Azure Database Migration Service.

I den här självstudiekursen får du lära du dig att:
> [!div class="checklist"]
>
> * Utvärdera migreringsinsatsen med verktyget ora2pg.
> * Migrera exempelschemat med verktyget ora2pg.
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsprojekt med hjälp av Azure Database Migration Service.
> * Köra migreringen.
> * Övervaka migreringen.

> [!NOTE]
> För att använda Azure Database Migration Service för att utföra en onlinemigrering måste du skapa en instans baserat på premiumprisnivån.

> [!IMPORTANT]
> För en optimal migreringsupplevelse rekommenderar Microsoft att du skapar en instans av Azure Database Migration Service i samma Azure-region som måldatabasen. Att flytta data mellan regioner eller geografiska områden kan göra migreringsprocessen långsammare och leda till fel.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

I den här artikeln beskrivs hur du utför en onlinemigrering från Oracle till Azure Database för PostgreSQL.

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

* Ladda ned och installera [Oracle 11g Release 2 (Standard Edition, Standard Edition One eller Enterprise Edition).](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html)
* Ladda ner exempel **HR-databasen** [härifrån](https://docs.oracle.com/database/121/COMSC/installation.htm#COMSC00002).
* Ladda ner och [installera ora2pg på antingen Windows eller Linux](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf).
* [Skapa en instans i Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal).
* Anslut till instansen och skapa en databas med hjälp av instruktionen i det här [dokumentet](https://docs.microsoft.com/azure/postgresql/tutorial-design-database-using-azure-portal).
* Skapa en Microsoft Azure Virtual Network for Azure Database Migration Service med hjälp av distributionsmodellen för Azure Resource Manager, som tillhandahåller anslutning från plats till plats till dina lokala källservrar med hjälp av [Antingen ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Mer information om hur du skapar ett virtuellt nätverk finns i [dokumentationen](https://docs.microsoft.com/azure/virtual-network/)till det virtuella nätverket och särskilt snabbstartsartiklarna med steg-för-steg-information.

  > [!NOTE]
  > Om du använder ExpressRoute med nätverks peering i Microsoft under installationen av virtuella nätverk lägger du till följande [tjänstslutpunkter](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) i undernätet där tjänsten ska etableras:
  >
  > * Slutpunkt för måldatabas (till exempel SQL-slutpunkt, Cosmos DB-slutpunkt och så vidare)
  > * Slutpunkt för lagring
  > * Ändpunkt för servicebuss
  >
  > Den här konfigurationen är nödvändig eftersom Azure Database Migration Service saknar internetanslutning.

* Kontrollera att dina NSG-regler (Virtual Network Network Security Group) inte blockerar följande inkommande kommunikationsportar till Azure Database Migration Service: 443, 53, 9354, 445, 12000. Mer information om filtrering av NSG-trafik i det virtuella nätverket finns i artikeln [Filtrera nätverkstrafik med nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Konfigurera din [Windows-brandvägg för databasmotoråtkomst](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Öppna Windows-brandväggen så att Azure Database Migration Service kan komma åt källan Oracle-servern, som som standard är TCP-port 1521.
* När du använder en brandväggsinstallation framför källdatabasen eller källdatabaserna kan du behöva lägga till brandväggsregler så att Azure Database Migration Service kan komma åt källdatabaserna för migrering.
* Skapa en [brandväggsregel](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) på servernivå för Azure Database för PostgreSQL för att tillåta Azure Database Migration Service åtkomst till måldatabaserna. Ange undernätsområdet för det virtuella nätverk som används för Azure Database Migration Service.
* Aktivera åtkomst till källan Oracle-databaser.

  > [!NOTE]
  > DBA-rollen krävs för att en användare ska kunna ansluta till Oracle-källan.

  * Arkivera Gör om loggar krävs för inkrementell synkronisering i Azure Database Migration Service för att samla in dataändring. Så här konfigurerar du Oracle-källan:
    * Logga in med SYSDBA-behörigheten genom att köra följande kommando:

      ```
      sqlplus (user)/(password) as sysdba
      ```

    * Stäng av databasinstansen genom att köra följande kommando.

      ```
      SHUTDOWN IMMEDIATE;
      ```

      Vänta på `'ORACLE instance shut down'`bekräftelsen .

    * Starta den nya instansen och montera (men öppna inte) databasen för att aktivera eller inaktivera arkivering bu kör följande kommando:

      ```
      STARTUP MOUNT;
      ```

      Databasen måste monteras. vänta på bekräftelse "Oracle instans startade".

    * Ändra databasarkiveringsläget genom att köra följande kommando:

      ```
      ALTER DATABASE ARCHIVELOG;
      ```

    * Öppna databasen för normala åtgärder genom att köra följande kommando:

      ```
      ALTER DATABASE OPEN;
      ```

      Du kan behöva starta om för att ARC-filen ska visas.

    * Kontrollera genom att köra följande kommando:

      ```
      SELECT log_mode FROM v$database;
      ```

      Du bör få `'ARCHIVELOG'`ett svar . Om svaret `'NOARCHIVELOG'`är , då kravet inte uppfylls.

  * Aktivera kompletterande loggning för replikering med något av följande alternativ.

    * **Alternativ 1**.
      Ändra kompletterande loggning på databasnivå för att täcka alla tabeller med PK och unikt index. Identifieringsfrågan returneras `'IMPLICIT'`.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY, UNIQUE) COLUMNS;
      ```

      Ändra extra loggning på tabellnivå. Kör endast för tabeller som har datamanipulering och inte har PK:er eller unika index.

      ```
      ALTER TABLE [TABLENAME] ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    * **Alternativ 2**.
      Ändra kompletterande loggning på databasnivå för att täcka `'YES'`alla tabeller och identifieringsfrågan returnerar .

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
      ```

      Ändra extra loggning på tabellnivå. Följ logiken nedan för att bara köra en sats för varje tabell.

      Om tabellen har en primärnyckel:

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

    Kontrollera genom att köra följande kommando:

      ```
      SELECT supplemental_log_data_min FROM v$database;
      ```

    Du bör få `'YES'`ett svar .

## <a name="assess-the-effort-for-an-oracle-to-azure-database-for-postgresql-migration"></a>Utvärdera arbetet för en Oracle-till Azure-databas för PostgreSQL-migrering

Vi rekommenderar att du använder ora2pg för att bedöma den ansträngning som krävs för att migrera från Oracle till Azure Database för PostgreSQL. Använd `ora2pg -t SHOW_REPORT` direktivet för att skapa en rapport med alla Oracle-objekt, den uppskattade migreringskostnaden (i utvecklardagar) och vissa databasobjekt som kan kräva särskild uppmärksamhet som en del av konverteringen.

De flesta kunder kommer att spendera en avsevärd tid på att granska bedömningsrapporten och överväga den automatiska och manuella konverteringsinsatsen.

Information om hur du konfigurerar och kör ora2pg för att skapa en utvärderingsrapport finns i avsnittet **Förmigration: Bedömning** i [Oracle till Azure Database för PostgreSQL-kokbok](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf). Här finns ett exempel på ora2pg-utvärderingsrapport. [here](https://ora2pg.darold.net/report.html)

## <a name="export-the-oracle-schema"></a>Exportera Oracle-schemat

Vi rekommenderar att du använder ora2pg för att konvertera Oracle-schemat och andra Oracle-objekt (typer, procedurer, funktioner osv.) till ett schema som är kompatibelt med Azure Database för PostgreSQL. ora2pg innehåller många direktiv som hjälper dig att fördefiniera vissa datatyper. Du kan till exempel `DATA_TYPE` använda direktivet för att ersätta alla NUMBER(*,0) med bigint i stället för NUMERISKT(38).

Du kan köra ora2pg för att exportera vart och ett av databasobjekten i SQL-filer. Du kan sedan granska SQL-filerna innan du importerar dem till Azure Database för PostgreSQL med psql eller så kan du köra . SQL-skript i PgAdmin.

```
psql -f [FILENAME] -h [AzurePostgreConnection] -p 5432 -U [AzurePostgreUser] -d database 
```

Ett exempel:

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database
```

Information om hur du konfigurerar och kör ora2pg för schemakonvertering finns i avsnittet **Migrering: Schema och data** i [Oracle till Azure Database for PostgreSQL Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf).

## <a name="set-up-the-schema-in-azure-database-for-postgresql"></a>Konfigurera schemat i Azure Database för PostgreSQL

Du kan välja att konvertera Oracle-tabellscheman, lagrade procedurer, paket och andra databasobjekt för att göra dem Postgres kompatibla med ora2pg innan du startar en migreringspipeline i Azure Database Migration Service. Se länkarna nedan för hur man arbetar med ora2pg:

* [Installera ora2pg i Windows](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)
* [Oracle till Azure PostgreSQL Migration Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)

Azure Database Migration Service kan också skapa PostgreSQL-tabellschemat. Tjänsten kommer åt tabellschemat i den anslutna Oracle-källan och skapar ett kompatibelt tabellschema i Azure Database för PostgreSQL. Var noga med att validera och kontrollera schemaformatet i Azure Database för PostgreSQL efter azure database migrationstjänst slutar skapa schemat och flytta data.

> [!IMPORTANT]
> Azure Database Migration Service skapar bara tabellschemat. andra databasobjekt som lagrade procedurer, paket, index osv., skapas inte.

Se också till att släppa sekundärnyckeln i måldatabasen för att hela belastningen ska köras. Se avsnittet **Migrera exempelschemat** i artikeln [här](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online) för ett skript som du kan använda för att släppa sekundärnyckeln. Använd Azure Database Migration Service för att köras för fullständig belastning och synkronisering.

### <a name="when-the-postgresql-table-schema-already-exists"></a>När postgreSQL-tabellschemat redan finns

Om du skapar ett PostgreSQL-schema med verktyg som ora2pg innan du startar dataförflyttningen med Azure Database Migration Service mappas källtabellerna till måltabellerna i Azure Database Migration Service.

1. När du skapar ett nytt Oracle till Azure Database för PostgreSQL-migreringsprojekt uppmanas du att välja måldatabas och målschema i steget Välj scheman. Fyll i måldatabasen och målschemat.

   ![Visa portalprenumerationer](media/tutorial-oracle-azure-postgresql-online/dms-map-to-target-databases.png)

2. På skärmen **Migreringsinställningar** visas en lista över tabeller i Oracle-källan. Azure Database Migration Service försöker matcha tabeller i källan och måltabellerna baserat på tabellnamn. Om det finns flera matchande måltabeller med olika höljen kan du välja vilken måltabell som ska mappas till.

    ![Visa portalprenumerationer](media/tutorial-oracle-azure-postgresql-online/dms-migration-settings.png)

> [!NOTE]
> Om du behöver mappa källtabellnamn till [dmsfeedback@microsoft.com](mailto:dmsfeedbac@microsoft.com) tabeller med olika namn kan vi skicka ett skript för att automatisera processen.

### <a name="when-the-postgresql-table-schema-doesnt-exist"></a>När tabellschemat för PostgreSQL inte finns

Om målet PostgreSQL-databasen inte innehåller någon tabellschemainformation konverterar Azure Database Migration Service källschemat och återskapar det i måldatabasen. Kom ihåg att Azure Database Migration Service bara skapar tabellschemat, inte andra databasobjekt som lagrade procedurer, paket och index.
Om du vill att Azure Database Migration Service ska skapa schemat åt dig kontrollerar du att målmiljön innehåller ett schema utan befintliga tabeller. Om Azure Database Migration Service identifierar en tabell förutsätter tjänsten att schemat skapades av ett externt verktyg som ora2pg.

> [!IMPORTANT]
> Azure Database Migration Service kräver att alla tabeller skapas på samma sätt, med hjälp av antingen Azure Database Migration Service eller ett verktyg som ora2pg, men inte båda.

Så här kommer du igång:

1. Skapa ett schema i måldatabasen baserat på dina programkrav. Som standard är PostgreSQL-tabellschema och kolumnnamn gemener. Oracle tabell schema och kolumner, å andra sidan, är som standard i alla versaler.
2. I steget Välj scheman anger du måldatabasen och målschemat.
3. Baserat på det schema du skapar i Azure Database för PostgreSQL använder Azure Database Migration Service följande omvandlingsregler:

    Om schemanamnet i Oracle-källan och matchar det i Azure Database för PostgreSQL, skapar Azure Database Migration Service *tabellschemat med samma ärende som i målet*.

    Ett exempel:

    | Källa Oracle schema | Mål postgresql database.schema | DMS skapade schema.table.column |
    | ------------- | ------------- | ------------- |
    | HR | targetHR.public | public.countries.country_id |
    | HR | targetHR.trgthr | trgthr.countries.country_id |
    | HR | targetHR.TARGETHR | "TARGETHR". LÄNDER"." COUNTRY_ID" |
    | HR | targetHR.HR | "HR"." LÄNDER"." COUNTRY_ID" |
    | HR | targetHR.Hr | *Det går inte att mappa blandade ärenden |

    *Om du vill skapa schema för blandade fall och [dmsfeedback@microsoft.com](mailto:dmsfeedback@microsoft.com)tabellnamn i målet PostgreSQL kontaktar du . Vi kan tillhandahålla ett skript för att ställa in mixad måltabell schema i målet PostgreSQL databas.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration

1. Logga in på Azure Portal och välj **Alla tjänster** och sedan **Prenumerationer**.

   ![Visa portalprenumerationer](media/tutorial-oracle-azure-postgresql-online/portal-select-subscriptions.png)

2. Välj den prenumeration där du vill skapa en instans av Azure Database Migration Service och välj sedan **Resursprovidrar**.

    ![Visa resursprovidrar](media/tutorial-oracle-azure-postgresql-online/portal-select-resource-provider.png)

3. Sök efter migrering och välj sedan **Registrera**till höger om **Microsoft.DataMigration**.

    ![Registrera resursprovider](media/tutorial-oracle-azure-postgresql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Skapa en DMS-instans

1. I Azure Portal väljer du + **Skapa en resurs**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Service** i listrutan.

    ![Azure Marketplace](media/tutorial-oracle-azure-postgresql-online/portal-marketplace.png)

2. På sidan **Azure Database Migration Service** väljer du **Skapa**.

    ![Skapa Azure Database Migration Service-instans](media/tutorial-oracle-azure-postgresql-online/dms-create2.png)
  
3. På sidan **Create Migration Service** anger du ett namn för tjänsten, prenumerationen och en ny eller befintlig resursgrupp.

4. Välj ett befintligt virtuellt nätverk eller skapa ett nytt.

    Det virtuella nätverket ger Azure Database Migration Service åtkomst till källan Oracle och målet Azure Database för PostgreSQL-instans.

    Mer information om hur du skapar ett virtuellt nätverk i Azure-portalen finns i artikeln [Skapa ett virtuellt nätverk med Azure-portalen](https://aka.ms/DMSVnet).

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
4. På skärmen **Nytt migreringsprojekt** anger du ett namn för projektet, i textrutan **Källservertyp,** väljer **Oracle**, i textrutan **Målservertyp,** väljer **Du Azure-databas för PostgreSQL**.
5. I avsnittet **Välj aktivitetstyp** väljer du **Datamigrering online**.

   ![Skapa Database Migration Service-projekt](media/tutorial-oracle-azure-postgresql-online/dms-create-project5.png)

   > [!NOTE]
   > Alternativt kan du **välja Skapa projekt bara** för att skapa migreringsprojektet nu och köra migreringen senare.

6. Välj **Spara**, notera kraven för att använda Azure Database Migration Service för att utföra en onlinemigrering och välj sedan **Skapa och köra aktivitet**.

## <a name="specify-source-details"></a>Ange källinformation

* På skärmen **Lägg till källinformation** anger du anslutningsinformation för källan Oracle-instansen.

  ![Skärmen Lägg till källinformation](media/tutorial-oracle-azure-postgresql-online/dms-add-source-details.png)

## <a name="upload-oracle-oci-driver"></a>Ladda upp Oracle OCI-drivrutin

1. Välj **Spara**och logga sedan in på ditt Oracle-konto på **oci-drivrutinen** och hämta drivrutinen **instantclient-basiclite-windows.x64-12.2.0.1.0.zip** (37 128 586 Byte(s)) (SHA1 Checksum: 865082268) [härifrån](https://www.oracle.com/technetwork/topics/winx64soft-089540.html#ic_winx64_inst).
2. Hämta drivrutinen till en delad mapp.

   Kontrollera att mappen delas med det användarnamn som du angav med minsta skrivskyddade åtkomst. Azure Database Migration Service kommer åt och läser från resursen för att ladda upp OCI-drivrutinen till Azure genom att personifiera det användarnamn du anger.

   Användarnamnet du anger måste vara ett Windows-användarkonto.

   ![Installation av OCI-drivrutin](media/tutorial-oracle-azure-postgresql-online/dms-oci-driver-install.png)

## <a name="specify-target-details"></a>Ange målinformation

1. Välj **Spara**och ange **Target details** sedan anslutningsinformation för målinformationen för Azure-servern för PostgreSQL-servern, som är den företablerade instansen av Azure Database för PostgreSQL som **HR-schemat** distribuerades till.

    ![Skärmen Målinformation](media/tutorial-oracle-azure-postgresql-online/dms-add-target-details1.png)

2. Välj **Spara** och mappa sedan på sidan **Mappa till måldatabaser** käll- och måldatabasen för migrering.

    Om måldatabasen innehåller samma databasnamn som källdatabasen väljer Azure Database Migration Service måldatabasen som standard.

    ![Mappa till måldatabaser](media/tutorial-oracle-azure-postgresql-online/dms-map-target-details.png)

3. Välj **Spara**. I rutan **Aktivitetsnamn** på skärmen **Migreringssammanfattning** anger du ett namn för migreringsaktiviteten och granskar sedan sammanfattningen för att se till att informationen för källa och mål matchar det du angav tidigare.

    ![Migreringssammanfattning](media/tutorial-oracle-azure-postgresql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Köra migreringen

* Välj **Kör migrering**.

  Fönstret migreringsaktivitet visas och **aktivitetens status** **initieras**.

## <a name="monitor-the-migration"></a>Övervaka migreringen

1. På migreringsaktivitetssidan väljer du **Uppdatera** för att uppdatera visningen tills **Status** för migreringen är **Körs**.

     ![Aktivitetsstatus - körs](media/tutorial-oracle-azure-postgresql-online/dms-activity-running.png)

2. Under **Databasnamn**väljer du en specifik databas för att komma åt migreringsstatus för **fullständiga datainläsningar** och **inkrementella datasynkroniseringsåtgärder.**

    Fullständig datainläsning visar den initiala inläsningsmigreringsstatus, och Inkrementell datasynkronisering visar CDC-status (change data capture).

     ![Aktivitetsstatus – Fullständig inläsning slutförd](media/tutorial-oracle-azure-postgresql-online/dms-activity-full-load-completed.png)

     ![Aktivitetsstatus – Inkrementell datasynkronisering](media/tutorial-oracle-azure-postgresql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Utföra snabbmigrering

När den fullständiga inläsningen är klar är databaserna märkta med **Klar för snabbmigrering**.

1. När du är redo att slutföra databasmigreringen väljer du **Starta snabb**.

2. Stoppa alla inkommande transaktioner till källdatabasen och vänta tills **Väntande ändringar** visar **0**.

   ![Starta snabb](media/tutorial-oracle-azure-postgresql-online/dms-start-cutover.png)

3. Välj **Bekräfta** och sedan **Använd**.
4. När databasmigreringsstatusen visar **Slutförd**ansluter du dina program till den nya Azure-måldatabasen för PostgreSQL-instans.

 > [!NOTE]
 > Eftersom PostgreSQL som standard har schema.table.column i gemener kan du återgå från versaler till gemener med hjälp av skriptet i avsnittet **Konfigurera schemat i Azure Database för PostgreSQL** tidigare i den här artikeln.

## <a name="next-steps"></a>Nästa steg

* Information om kända problem och begränsningar när du utför onlinemigreringar till Azure Database for PostgreSQL finns i artikeln [Kända problem och lösningar för Azure Database for PostgreSQL-onlinemigreringar](known-issues-azure-postgresql-online.md).
* Information om Migreringstjänsten för Azure Database finns i artikeln [Vad är Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Information om Azure Database för PostgreSQL finns i artikeln [Vad är Azure Database för PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview).
