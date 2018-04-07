---
title: Konfigurera Azure SQL datasynkronisering (förhandsversion) | Microsoft Docs
description: Den här kursen visar hur du ställer in datasynkronisering för Azure SQL (förhandsgranskning)
services: sql-database
author: douglaslms
manager: craigg
ms.service: sql-database
ms.custom: load & move data
ms.topic: article
ms.date: 04/01/2018
ms.author: douglasl
ms.reviewer: douglasl
ms.openlocfilehash: 72e0ed535139c088c4235b43a12ea96da080dc8a
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="set-up-sql-data-sync-preview"></a>Konfigurera synkronisering för SQL-Data (förhandsgranskning)
Lär dig hur du ställer in Azure SQL Data Sync genom att skapa en hybrid sync-grupp som innehåller både Azure SQL Database och SQL Server-instanser i den här självstudiekursen. Den nya gruppen sync helt har konfigurerats och synkroniserar enligt det schema du anger.

Den här kursen förutsätter att du har minst tidigare erfarenhet med SQL Database och SQL Server. 

En översikt över SQL Data Sync finns i [Synkronisera data i flera moln och lokala databaser med Azure SQL Data Sync (förhandsversion)](sql-database-sync-data.md).

Fullständig PowerShell-exempel som visar hur du konfigurerar SQL datasynkronisering, finns i följande artiklar:
-   [Använd PowerShell för att synkronisera mellan flera Azure SQL-databaser](scripts/sql-database-sync-data-between-sql-databases.md)
-   [Använd PowerShell för att synkronisera mellan en Azure SQL Database och en lokal SQL Server-databas](scripts/sql-database-sync-data-between-azure-onprem.md)

## <a name="step-1---create-sync-group"></a>Steg 1 – Skapa sync-grupp

### <a name="locate-the-data-sync-settings"></a>Hitta inställningarna för datasynkronisering

1.  Gå till Azure-portalen i webbläsaren.

2.  Leta upp din SQL-databaser från instrumentpanelen eller ikonen SQL-databaser i verktygsfältet i portalen.

    ![Lista över Azure SQL-databaser](media/sql-database-get-started-sql-data-sync/datasync-preview-sqldbs.png)

3.  På den **SQL-databaser** markerar du den befintliga SQL-databasen som du vill använda som databasen hubb för datasynkronisering. SQL-databas öppnas.

    NAV-databasen är central slutpunkten för sync-topologi, där en sync-grupp har flera slutpunkter i databasen. Databasen slutpunkter i samma sync grupp – det vill säga alla medlem databaser - synkronisering med NAV-databasen.

4.  Välj på sidan för SQL-databasen för den valda databasen **synkronisering till andra databaser**. Sidan datasynkronisering öppnas.

    ![Synkronisera till andra databaser alternativ](media/sql-database-get-started-sql-data-sync/datasync-preview-newsyncgroup.png)

### <a name="create-a-new-sync-group"></a>Skapa en ny grupp för synkronisering

1.  Välj på sidan datasynkronisering **ny synkronisering grupp**. Den **ny synkronisering grupp** sidan öppnas med steg 1 **skapa sync grupp**, markerade. Den **skapa Sync datagruppen** också sidan öppnas.

2.  På den **skapa Sync datagruppen** gör följande:

    1.  I den **Sync gruppnamn** , ange ett namn för den nya gruppen för synkronisering.

    2.  I den **synkronisera Metadata-databasen** väljer du om du vill skapa en ny databas (rekommenderas) eller använda en befintlig databas.

        > [!NOTE]
        > Microsoft rekommenderar att du skapar en ny, tom databas som ska användas som synkronisera Metadata-databasen. Datasynkronisering skapar tabeller i databasen och kör en frekventa arbetsbelastning. Den här databasen delas automatiskt som synkronisera Metadata-databasen för alla dina synkroniseringsgrupper i den valda regionen. Du kan inte ändra synkronisera Metadata-databasen eller dess namn utan att släppa den.

        Om du väljer **ny databas**väljer **Skapa ny databas.** Den **SQL-databas** öppnas. På den **SQL-databas** sidan, namn och konfigurera den nya databasen. Välj sedan **OK**.

        Om du väljer **Använd befintlig databas**, markera databasen i listan.

    3.  I den **automatisk synkronisering** väljer du först **på** eller **av**.

        Om du väljer **på**i den **Synkroniseringsfrekvensen** , ange ett tal och välj sekunder, minuter, timmar eller dagar.

        ![Ange frekvens för synkronisering](media/sql-database-get-started-sql-data-sync/datasync-preview-syncfreq.png)

    4.  I den **konfliktlösning** väljer du ”hubb wins” eller ”medlem wins”.

        ”Hubb wins” innebär att när en konflikt uppstår data i databasen hubb skriver över motstridiga data i databasen medlem. ”Medlem wins” innebär att när en konflikt uppstår data i databasen medlem skriver över motstridiga data i databasen hubb. 

        ![Ange hur konflikter löses](media/sql-database-get-started-sql-data-sync/datasync-preview-conflictres.png)

    5.  Välj **OK** och vänta tills den nya gruppen synkronisering kan skapas och distribueras.

## <a name="step-2---add-sync-members"></a>Steg 2 – lägga till sync-medlemmar

När den nya gruppen sync skapas och distribueras, steg 2, **lägga till medlemmar i synkronisering**, är markerad i den **ny synkronisering grupp** sidan.

I den **hubb databasen** ange befintliga autentiseringsuppgifter för SQL Database-server där NAV-databasen finns. Ange inte *nya* autentiseringsuppgifter i det här avsnittet.

![NAV-databasen har lagts till synkronisera grupp](media/sql-database-get-started-sql-data-sync/datasync-preview-hubadded.png)

### <a name="add-an-azure-sql-database"></a>Lägg till en Azure SQL-databas

I den **medlem databasen** avsnittet om du vill lägga till en Azure SQL Database i gruppen synkronisering genom att välja **lägga till en Azure-databas**. Den **konfigurera Azure Database** öppnas.

På den **konfigurera Azure Database** gör följande:

1.  I den **Sync medlemsnamn** anger ett namn för den nya medlemmen synkronisering. Det här namnet skiljer sig från namnet på själva databasen.

2.  I den **prenumeration** , markera den tillhörande Azure-prenumerationen för fakturering.

3.  I den **Azure SQL Server** väljer du den befintliga SQL-databasservern.

4.  I den **Azure SQL Database** väljer du den befintliga SQL-databasen.

5.  I den **Sync anvisningarna** fält, Välj dubbelriktad synkronisering, till hubben eller från hubb.

    ![Lägga till en ny SQL-databas sync medlem](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadding.png)

6.  I den **användarnamn** och **lösenord** ange befintliga autentiseringsuppgifter för SQL Database-servern som medlem databasen finns. Ange inte *nya* autentiseringsuppgifter i det här avsnittet.

7.  Välj **OK** och vänta tills den nya medlemmen synkronisering kan skapas och distribueras.

    ![Ny SQL-databas sync medlem har lagts till](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadded.png)

### <a name="add-on-prem"></a> Lägg till en lokal SQL Server-databas

I den **medlem databasen** avsnittet om du vill lägga till en lokal SQL Server i gruppen synkronisering genom att välja **lägga till en lokal databas**. Den **konfigurera lokalt** öppnas.

På den **konfigurera lokalt** gör följande:

1.  Välj **Välj Sync Agent Gateway**. Den **Välj Agent för Sync** öppnas.

    ![Välj sync agent gateway](media/sql-database-get-started-sql-data-sync/datasync-preview-choosegateway.png)

2.  På den **Välj Sync Agent Gateway** väljer du om du vill använda en befintlig agent eller skapa en ny agent.

    Om du väljer **befintliga agenter**, Välj den befintliga agenten i listan.

    Om du väljer **skapa en ny agent**, göra följande:

    1.  Hämta klientprogrammet för sync-agenten från länken och installera den på den dator där SQL Server.
 
        > [!IMPORTANT]
        > Du måste öppna utgående TCP-port 1433 i brandväggen för att låta klientagenten kommunicera med servern.


    2.  Ange ett namn för agenten.

    3.  Välj **skapa och generera nyckel**.

    4.  Kopiera agentnyckeln till Urklipp.
        
        ![Skapa en ny agent för synkronisering](media/sql-database-get-started-sql-data-sync/datasync-preview-selectsyncagent.png)

    5.  Välj **OK** att stänga den **Välj Agent för Sync** sidan.

    6.  Leta upp på SQL Server-datorn och kör appen Sync-Klientagenten.

        ![Data synkroniseras agent-klientappen](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

    7.  I appen sync agent väljer **skicka Agentnyckeln**. Den **synkronisera Metadata databaskonfiguration** öppnas.

    8.  I den **synkronisera Metadata databaskonfiguration** dialogrutan Klistra in agentnyckeln kopieras från Azure-portalen. Dessutom befintliga autentiseringsuppgifter för Azure SQL Database-server som metadata-databasen finns. (Om du har skapat en ny databas för metadata är den här databasen på samma server som NAV-databasen.) Välj **OK** och vänta på att slutföra konfigurationen.

        ![Ange autentiseringsuppgifter för agenten nyckel och server](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        >   [!NOTE] 
        >   Om du får ett Brandväggsfel nu kan måste du skapa en brandväggsregel i Azure för att tillåta inkommande trafik från SQL Server-datorn. Du kan skapa regeln manuellt i portalen, men det kan vara lättare att skapa den i SQL Server Management Studio (SSMS). I SSMS, försök ansluta till NAV-databas på Azure. Ange namnet som \<hub_database_name\>. database.windows.net. Följ stegen i dialogrutan för att konfigurera Azure brandväggsregeln. Återgå sedan till appen Sync-Klientagenten.

    9.  Klicka på appen Sync-Klientagenten **registrera** att registrera en SQL Server-databas med agenten. Den **SQL Server-konfigurationsfilen** öppnas.

        ![Lägga till och konfigurera en SQL Server-databas](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

    10. I den **SQL Server-konfigurationsfilen** dialogrutan Välj om du vill ansluta med hjälp av SQL Server-autentisering eller Windows-autentisering. Om du väljer SQL Server-autentisering anger du de befintliga autentiseringsuppgifterna. Ange namnet på SQL Server och namnet på databasen som du vill synkronisera. Välj **Anslutningstestet** testa inställningarna. Välj sedan **Spara**. Registrerade databasen visas i listan.

        ![SQL Server-databasen är nu registrerad](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

    11. Nu kan du stänga appen Sync-Klientagenten.

    12. I portalen på den **konfigurera lokalt** väljer **Markera databasen.** Den **Välj databas** öppnas.

    13. På den **Välj databas** sidan den **Sync medlemsnamn** anger ett namn för den nya medlemmen synkronisering. Det här namnet skiljer sig från namnet på själva databasen. Välj databasen i listan. I den **Sync anvisningarna** fält, Välj dubbelriktad synkronisering, till hubben eller från hubb.

        ![Välj den på lokal databasen](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

    14. Välj **OK** att stänga den **Välj databas** sidan. Välj sedan **OK** att stänga den **konfigurera lokalt** sidan och vänta tills den nya medlemmen synkronisering kan skapas och distribueras. Klicka slutligen på **OK** att stänga den **Välj sync medlemmar** sidan.

        ![På lokal databas sync-grupp](media/sql-database-get-started-sql-data-sync/datasync-preview-onpremadded.png)

3.  Om du vill ansluta till SQL-datasynkronisering och lokal agent, Lägg till ditt användarnamn i rollen `DataSync_Executor`. Datasynkronisering skapar den här rollen på SQL Server-instansen.

## <a name="step-3---configure-sync-group"></a>Steg 3 – Konfigurera synkronisering grupp

När de nya sync gruppmedlemmarna skapas och distribueras, steg3 **Konfigurera synkronisering grupp**, är markerat i den **ny synkronisering grupp** sidan.

1.  På den **tabeller** , väljer du en databas i listan över gruppmedlemmar för synkronisering och väljer sedan **Uppdatera schema**.

2.  Välj de tabeller som du vill synkronisera från listan över tillgängliga tabeller.

    ![Välj tabeller att synkronisera](media/sql-database-get-started-sql-data-sync/datasync-preview-tables.png)

3.  Alla kolumner i tabellen är markerad som standard. Om du inte vill synkronisera alla kolumner, inaktivera kryssrutan för de kolumner som du inte vill synkronisera. Se till att lämna primärnyckelkolumnen har valts.

    ![Välj de fält som ska synkroniseras](media/sql-database-get-started-sql-data-sync/datasync-preview-tables2.png)

4.  Välj slutligen **spara**.

## <a name="faq-about-setup-and-configuration"></a>Vanliga frågor om installation och konfiguration

### <a name="how-frequently-can-data-sync-synchronize-my-data"></a>Hur ofta kan datasynkronisering synkroniserar Mina data? 
Minsta frekvens är var femte minut.

### <a name="does-sql-data-sync-fully-create-and-provision-tables"></a>SQL-datasynkronisering fullständigt skapa och etablera tabeller?

Om synkronisering schemat tabeller inte är redan har skapats i måldatabasen skapar SQL datasynkronisering (förhandsgranskning) dem med de kolumner som du har valt. Men resulterar det här beteendet inte i ett schema för fullständig återgivning, av följande skäl:

-   Endast de kolumner som du har valt skapas i måltabellen. Om vissa kolumner i källtabellerna inte är en del av gruppen sync tillhandahålls inte kolumnerna i mål-tabeller.

-   Index skapas endast för de markerade kolumnerna. Om käll-Tabellindex har kolumner som inte ingår i gruppen synkronisering, dessa index inte har etablerats i mål-tabeller.

-   Index för kolumner av typen XML etableras inte.

-   Kontrollbegränsningar etableras inte.

-   Befintliga utlösare på källtabellerna etableras inte.

-   Vyer och lagrade procedurer skapas inte till måldatabasen.

På grund av dessa begränsningar rekommenderar vi följande:
-   För produktionsmiljöer, etablera fullständig återgivning schemat själv.
-   För att testa tjänsten funktionen Automatisk etablering i SQL-datasynkronisering (förhandsgranskning) fungerar bra.

### <a name="why-do-i-see-tables-that-i-did-not-create"></a>Varför ser tabeller som jag inte kan skapa?  
Datasynkronisering skapar tabeller sida i databasen för ändringsspårning. Ta bort inte eller datasynkronisering slutar fungera.

### <a name="is-my-data-convergent-after-a-sync"></a>Är Mina data tillämpas enhetligt efter en synkronisering?

Inte nödvändigtvis. I en grupp för synkronisering med ett nav och tre ekrar (A, B och C) är synkroniseringar hubben till en, nav och B och hubb till C. Om en ändring görs till databasen A *när* hubben till en synkronisering som ändra inte skrivs till B-databas eller databas C tills aktiviteten nästa synkronisering.

### <a name="how-do-i-get-schema-changes-into-a-sync-group"></a>Hur skaffar jag schemaändringar i en grupp för synkronisering

Du måste utföra schemaändringar manuellt.

### <a name="how-can-i-export-and-import-a-database-with-data-sync"></a>Hur kan exportera och importera en databas med datasynkronisering?
När du exporterar en databas som en `.bacpac` filen och importera filen om du vill skapa en ny databas måste du göra följande två saker du kan använda datasynkronisering i den nya databasen:
1.  Rensa datasynkronisering objekt och tabeller sida på den **ny databas** med hjälp av [skriptet](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/clean_up_data_sync_objects.sql). Det här skriptet tar bort alla nödvändiga datasynkronisering objekt från databasen.
2.  Återskapa gruppen synkronisering med den nya databasen. Ta bort om du inte längre behöver den gamla sync-gruppen.

## <a name="faq-about-the-client-agent"></a>Vanliga frågor om klientagenten

### <a name="why-do-i-need-a-client-agent"></a>Varför behöver jag en klientagent

Tjänsten SQL-datasynkronisering (förhandsgranskning) kommunicerar med SQL Server-databaser via klientagenten. Den här säkerhetsfunktionen förhindrar direktkommunikation med databaser bakom en brandvägg. När tjänsten SQL-datasynkronisering (förhandsgranskning) kommunicerar med agenten den gör det genom att använda krypterade anslutningar och en unik token eller *agentnyckeln*. SQL Server-databaserna autentisera agenten med anslutningsnyckel sträng och agent. Den här designen ger en hög nivå av säkerhet för dina data.

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>Hur många instanser av den lokala agenten UI kan köras?

Endast en instans för gränssnitt kan köras.

### <a name="how-can-i-change-my-service-account"></a>Hur kan jag ändra Mina tjänstkontot?

När du har installerat en klientagent är det enda sättet att ändra tjänstkontot för att avinstallera den och installera en ny klientagent med det nya tjänstkontot.

### <a name="how-do-i-change-my-agent-key"></a>Hur kan jag ändra Mina agent-nyckeln?

En agent-nyckel kan bara användas en gång av en agent. Det går inte att återanvändas när du tar bort och sedan installera om en ny agent, och kan inte användas av flera agenter. Om du behöver skapa en ny nyckel för en befintlig agent måste du vara säker på att samma nyckel registreras med klientagenten och tjänsten SQL-datasynkronisering (förhandsversion).

### <a name="how-do-i-retire-a-client-agent"></a>Hur jag dra tillbaka en klientagent

Återskapa dess nyckel i portalen för att omedelbart ogiltigförklara eller dra tillbaka en agent, men inte skickar den i Användargränssnittet för agenten. Återskapande av en nyckel upphäver den tidigare nyckeln oavsett om motsvarande agenten är online eller offline.

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>Hur flytta en klientagent till en annan dator?

Om du vill köra lokal agent från en annan dator än den som för tillfället är gör du följande:

1. Installera agenten på önskad dator.

2. Logga in på portalen SQL datasynkronisering (förhandsversion) och återskapa en agentnyckeln för den nya agenten.

3. Använda den nya agenten Gränssnittet för att skicka den nya agentnyckeln.

4. Vänta medan klientagenten hämtar listan över lokala databaser som har registrerats tidigare.

5. Ange autentiseringsuppgifter på databasen för alla databaser som visas som kan nås. Dessa databaser måste kunna nås från den nya datorn där agenten är installerad.

## <a name="next-steps"></a>Nästa steg
Grattis! Du har skapat en sync-grupp som innehåller både en instans av SQL Database och SQL Server-databasen.

Mer information om SQL Data Sync finns i:

-   [Synkronisera data i flera moln och lokala databaser med Azure SQL Data Sync](sql-database-sync-data.md)
-   [Metodtips för Azure SQL Data Sync](sql-database-best-practices-data-sync.md)
-   [Övervaka Azure SQL-datasynkronisering med logganalys](sql-database-sync-monitor-oms.md)
-   [Felsöka problem med Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)

-   Slutför PowerShell-exempel som visar hur du konfigurerar SQL Data Sync:
    -   [Använd PowerShell för att synkronisera mellan flera Azure SQL-databaser](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Använd PowerShell för att synkronisera mellan en Azure SQL Database och en lokal SQL Server-databas](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Ladda ned REST API-dokumentation för SQL Data Sync](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Mer information om SQL Database finns i:

-   [Översikt över SQL Database](sql-database-technical-overview.md)
-   [Livscykelhantering för databas](https://msdn.microsoft.com/library/jj907294.aspx)
