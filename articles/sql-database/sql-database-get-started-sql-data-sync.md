---
title: "Kom igång med datasynkronisering för Azure SQL (förhandsversion) | Microsoft Docs"
description: "Den här kursen hjälper dig att komma igång med datasynkronisering för Azure SQL (förhandsgranskning)"
services: sql-database
documentationcenter: 
author: douglaslms
manager: craigg
editor: 
ms.assetid: a295a768-7ff2-4a86-a253-0090281c8efa
ms.service: sql-database
ms.custom: load & move data
ms.workload: Active
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: douglasl
ms.reviewer: douglasl
ms.openlocfilehash: ddcf6868a0fca88a52774e20623d25de31c063bb
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2017
---
# <a name="get-started-with-azure-sql-data-sync-preview"></a>Kom igång med datasynkronisering för Azure SQL (förhandsgranskning)
Lär dig hur du ställer in Azure SQL Data Sync genom att skapa en hybrid sync-grupp som innehåller både Azure SQL Database och SQL Server-instanser i den här självstudiekursen. Den nya gruppen sync helt har konfigurerats och synkroniserar enligt det schema du anger.

Den här kursen förutsätter att du har minst tidigare erfarenhet med SQL Database och SQL Server. 

En översikt över SQL datasynkronisering finns [synkronisera data över flera databaser i molnet och lokalt med Azure SQL-datasynkronisering (förhandsgranskning)](sql-database-sync-data.md).

Fullständig PowerShell-exempel som visar hur du konfigurerar SQL datasynkronisering, finns i följande artiklar:
-   [Använd PowerShell för att synkronisera mellan flera Azure SQL-databaser](scripts/sql-database-sync-data-between-sql-databases.md)
-   [Använd PowerShell för att synkronisera mellan en Azure SQL Database och en lokal SQL Server-databas](scripts/sql-database-sync-data-between-azure-onprem.md)

## <a name="step-1---create-sync-group"></a>Steg 1 – Skapa sync-grupp

### <a name="locate-the-data-sync-settings"></a>Hitta inställningarna för datasynkronisering

1.  Gå till Azure-portalen i webbläsaren.

2.  Leta upp din SQL-databaser från instrumentpanelen eller ikonen SQL-databaser i verktygsfältet i portalen.

    ![Lista över Azure SQL-databaser](media/sql-database-get-started-sql-data-sync/datasync-preview-sqldbs.png)

3.  På den **SQL-databaser** markerar du den befintliga SQL-databasen som du vill använda som databasen hubb för datasynkronisering. SQL-databas öppnas.

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

### <a name="add-an-on-premises-sql-server-database"></a>Lägg till en lokal SQL Server-databas

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

    10. I den **SQL Server-konfigurationsfilen** dialogrutan Välj om du vill ansluta med hjälp av SQL Server-autentisering eller Windows-autentisering. Om du väljer SQL Server-autentisering anger du de befintliga autentiseringsuppgifterna. Ange namnet på SQL Server och namnet på databasen som du vill synkronisera. Välj **Anslutningstestet** testa inställningarna. Välj sedan **spara**. Registrerade databasen visas i listan.

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

    ![Välj tabeller ska synkroniseras](media/sql-database-get-started-sql-data-sync/datasync-preview-tables.png)

3.  Alla kolumner i tabellen är markerad som standard. Om du inte vill synkronisera alla kolumner, inaktivera kryssrutan för de kolumner som du inte vill synkronisera. Se till att lämna primärnyckelkolumnen har valts.

    ![Välj fält som ska synkroniseras](media/sql-database-get-started-sql-data-sync/datasync-preview-tables2.png)

4.  Välj slutligen **spara**.

## <a name="next-steps"></a>Nästa steg
Grattis! Du har skapat en sync-grupp som innehåller både en instans av SQL Database och SQL Server-databasen.

För mer information om SQL-datasynkronisering, se:

-   [Synkronisera data över flera databaser i molnet och lokalt med Azure SQL-datasynkronisering](sql-database-sync-data.md)
-   [Metodtips för Azure SQL-datasynkronisering](sql-database-best-practices-data-sync.md)
-   [Felsökning av problem med Azure SQL-datasynkronisering](sql-database-troubleshoot-data-sync.md)

-   Slutför PowerShell-exempel som visar hur du konfigurerar SQL datasynkronisering:
    -   [Använd PowerShell för att synkronisera mellan flera Azure SQL-databaser](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Använd PowerShell för att synkronisera mellan en Azure SQL Database och en lokal SQL Server-databas](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Hämta SQL Data Sync REST API-dokumentation](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

För mer information om SQL-databasen, se:

-   [Översikt över SQL-databas](sql-database-technical-overview.md)
-   [Livscykelhantering för databasen](https://msdn.microsoft.com/library/jj907294.aspx)
