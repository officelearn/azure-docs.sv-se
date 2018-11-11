---
title: Konfigurera Azure SQL Data Sync | Microsoft Docs
description: Den här självstudiekursen visar hur du ställer in Azure SQL Data Sync
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.date: 11/07/2018
ms.openlocfilehash: 0a248ec5137a6de43910b1d11184dfeda18601f5
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51280355"
---
# <a name="tutorial-set-up-sql-data-sync-to-sync-data-between-azure-sql-database-and-sql-server-on-premises"></a>Självstudie: Ställa in SQL Data Sync för att synkronisera data mellan Azure SQL Database och SQL Server lokalt

I den här självstudien får du lära dig hur du ställer in Azure SQL Data Sync genom att skapa en hybrid sync-grupp som innehåller både Azure SQL Database och SQL Server-instanser. Ny synkroniseringsgrupp är fullständigt konfigurerad och synkroniserar enligt det schema som du anger.

Den här självstudien förutsätter att du har minst tidigare erfarenhet med SQL Database och SQL Server.

En översikt över SQL Data Sync finns i [Synkronisera data i flera moln och lokala databaser med Azure SQL Data Sync](sql-database-sync-data.md).

Fullständig PowerShell-exempel som visar hur du konfigurerar SQL Data Sync finns i följande artiklar:

- [Använda PowerShell för att synkronisera mellan flera Azure SQL-databaser](scripts/sql-database-sync-data-between-sql-databases.md)
- [Använd PowerShell för att synkronisera mellan en Azure SQL Database och en lokal SQL Server-databas](scripts/sql-database-sync-data-between-azure-onprem.md)

## <a name="step-1---create-sync-group"></a>Steg 1 – Skapa synkroniseringsgrupp

### <a name="locate-the-data-sync-settings"></a>Hitta inställningarna för datasynkronisering

1. Gå till Azure portal i webbläsaren.

2. Leta upp din SQL-databaser från din instrumentpanel eller från SQL-databaser-ikonen i verktygsfältet i portalen.

    ![Lista över Azure SQL-databaser](media/sql-database-get-started-sql-data-sync/datasync-preview-sqldbs.png)

3. På den **SQL-databaser** väljer du den befintliga SQL-databasen som du vill använda som hubbdatabasen för datasynkronisering. SQL database-sidan öppnas.

    Hubb-databasen är den centrala slutpunkten av sync-topologi, där en synkroniseringsgrupp har flera slutpunkter för databasen. Databasen slutpunkter i samma synkronisering grupp – det vill säga alla medlem databaser – synkroniseringen med hubbdatabasen.

4. På sidan för SQL-databasen för den valda databasen väljer **synkronisera till andra databaser**. Då öppnas sidan datasynkronisering.

    ![Synkronisera till andra databaser-alternativ](media/sql-database-get-started-sql-data-sync/datasync-preview-newsyncgroup.png)

### <a name="create-a-new-sync-group"></a>Skapa en ny Synkroniseringsgrupp

1. På sidan datasynkronisering väljer **ny Synkroniseringsgrupp**. Den **ny synkroniseringsgrupp** öppnas med steg 1, **skapa synkroniseringsgruppen**, markerade. Den **skapa Datasynkroniseringsgrupp** öppnas sidan också.

2. På den **skapa Datasynkroniseringsgrupp** gör följande:

   1. I den **Synkroniseringsgruppnamnet** fältet, anger du ett namn för ny synkroniseringsgrupp.

   2. I den **Metadatasynkroniserings-databasen** väljer om du vill skapa en ny databas (rekommenderas) eller använda en befintlig databas.

        > [!NOTE]
        > Microsoft rekommenderar att du skapar en ny, tom databas ska användas som den Metadatasynkroniserings-databasen. Datasynkronisering skapar tabeller i den här databasen och kör en återkommande arbetsbelastning. Den här databasen delas automatiskt som den Metadatasynkroniserings-databasen för alla dina synkroniseringsgrupper i den valda regionen. Du kan inte ändra den Metadatasynkroniserings-databasen eller dess namn utan att ta bort alla synkroniseringsgrupper och Synkroniseringsagenter i regionen.

        Om du har valt **ny databas**väljer **Skapa ny databas.** Den **SQL Database** öppnas. På den **SQL Database** sidan, namn och konfigurera den nya databasen. Välj sedan **OK**.

        Om du har valt **Använd befintlig databas**, Välj databasen i listan.

   3. I den **automatisk synkronisering** väljer du först **på** eller **av**.

        Om du har valt **på**i den **Synkroniseringsfrekvensen** , ange ett tal och väljer sekunder, minuter, timmar eller dagar.

        ![Ange synkroniseringsfrekvensen](media/sql-database-get-started-sql-data-sync/datasync-preview-syncfreq.png)

   4. I den **konfliktlösning** väljer du ”Hub wins” eller ”medlem wins”.

        ”Hub wins” innebär att när en konflikt uppstår kan data i hubbdatabasen skriver över den motstridiga data i medlemsdatabasen. ”Medlemmen wins” innebär att när en konflikt uppstår kan data i medlemsdatabasen skriver över den motstridiga data i hubbdatabasen.

       ![Ange hur konflikter löses](media/sql-database-get-started-sql-data-sync/datasync-preview-conflictres.png)

   5. Välj **OK** och vänta på ny synkroniseringsgrupp skapas och distribueras.

## <a name="step-2---add-sync-members"></a>Steg 2 – Lägg till synkroniseringsmedlemmar

När nya synkroniseringsgruppen skapas och distribueras, steg 2, **Lägg till synkroniseringsmedlemmar**, är markerat i den **ny synkroniseringsgrupp** sidan.

I den **Hubbdatabasen** anger de befintliga autentiseringsuppgifterna för SQL Database-servern där hubb-databasen finns. Ange inte *nya* autentiseringsuppgifter i det här avsnittet.

![Hubb-databasen har lagts till synkroniseringsgrupp](media/sql-database-get-started-sql-data-sync/datasync-preview-hubadded.png)

### <a name="add-an-azure-sql-database"></a>Lägg till en Azure SQL-databas

I den **medlemsdatabas** avsnittet om du vill lägga till en Azure SQL Database i synkroniseringsgruppen genom att välja **lägga till en Azure Database**. Den **konfigurera Azure Database** öppnas.

På den **konfigurera Azure Database** gör följande:

1. I den **Synkroniseringsmedlemsnamnet** fältet, ange ett namn för den nya synkroniseringsmedlemmen. Det här namnet skiljer sig från namnet på själva databasen.

2. I den **prenumeration** , markera den associera Azure-prenumerationen för fakturering.

3. I den **Azure SQL Server** , markera den befintliga SQL database-servern.

4. I den **Azure SQL Database** , markera den befintliga SQL-databasen.

5. I den **Synkroniseringsriktningar** fält, Välj dubbelriktad synkronisering, till Hub eller från hubben.

    ![Att lägga till en ny SQL Database-synkroniseringsmedlem](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadding.png)

6. I den **användarnamn** och **lösenord** fält, ange de befintliga autentiseringsuppgifterna för SQL Database-servern där medlemsdatabasen finns. Ange inte *nya* autentiseringsuppgifter i det här avsnittet.

7. Välj **OK** och vänta på ny synkroniseringsmedlemmen skapas och distribueras.

    ![Ny SQL Database-synkroniseringsmedlemmen har lagts till](media/sql-database-get-started-sql-data-sync/datasync-preview-memberadded.png)

### <a name="add-on-prem"></a> Lägg till en lokal SQL Server-databas

I den **medlemsdatabas** avsnittet om du vill lägga till en lokal SQL Server i synkroniseringsgruppen genom att välja **Lägg till en lokal databas**. Den **konfigurera lokala** öppnas.

På den **konfigurera lokala** gör följande:

1. Välj **Välj Synkroniseringsagentsgatewayen**. Den **Välj Synkroniseringsagent** öppnas.

    ![Välj synkroniseringsagentsgatewayen](media/sql-database-get-started-sql-data-sync/datasync-preview-choosegateway.png)

2. På den **Välj Synkroniseringsagentsgatewayen** väljer du om du vill använda en befintlig agent eller skapa en ny agent.

    Om du har valt **befintliga agenter**, Välj den befintliga agenten i listan.

    Om du har valt **skapa en ny agent**, gör du följande:

   1. Hämta klientprogrammet för sync-agenten från länken och installera den på den dator där SQL Server. Du kan också hämta data sync-agenten direkt från [SQL Azure Data Sync-agenten](https://www.microsoft.com/download/details.aspx?id=27693).

        > [!IMPORTANT]
        > Du måste öppna utgående TCP-port 1433 i brandväggen så att klientagenten kommunicera med servern.

   2. Ange ett namn för agenten.

   3. Välj **skapa och generera nyckel**.

   4. Kopiera agentnyckeln till Urklipp.

        ![Skapa en ny synkroniseringsagent](media/sql-database-get-started-sql-data-sync/datasync-preview-selectsyncagent.png)

   5. Välj **OK** att Stäng den **Välj Synkroniseringsagent** sidan.

   6. Hitta och kör appen Klientsynkroniseringsagenten på SQL Server-datorn.

        ![Data synkroniseras agent-klientappen](media/sql-database-get-started-sql-data-sync/datasync-preview-clientagent.png)

   7. Välj i appen sync-agenten **skicka Agentnyckeln**. Den **synkronisera Metadata databaskonfiguration** öppnas dialogrutan.

   8. I den **synkronisera Metadata databaskonfiguration** dialogrutan Klistra in agentnyckeln som kopieras från Azure-portalen. Ange även de befintliga autentiseringsuppgifterna för Azure SQL Database-servern där metadata-databasen finns. (Om du har skapat en ny databas för metadata är den här databasen på samma server som hubbdatabasen.) Välj **OK** och vänta tills konfigurationen har slutförts.

        ![Ange autentiseringsuppgifter för agenten nyckel och server](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-enterkey.png)

        > [!NOTE]
        > Om du får ett Brandväggsfel i det här läget kan behöva du skapa en brandväggsregel på Azure för att tillåta inkommande trafik från SQL Server-datorn. Du kan skapa regeln manuellt i portalen, men det kan vara lättare att skapa den i SQL Server Management Studio (SSMS). I SSMS, försök ansluta till hubb-databasen på Azure. Ange dess namn som < hub_database_name >. database.windows.net. Följ stegen i dialogrutan för att konfigurera Azure brandväggsregeln. Gå sedan tillbaka till appen Klientsynkroniseringsagenten.

   9. I appen Klientsynkroniseringsagenten klickar du på **registrera** att registrera en SQL Server-databas med agenten. Den **SQL Server-konfigurationsfilen** öppnas dialogrutan.

        ![Lägga till och konfigurera en SQL Server-databas](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-adddb.png)

   10. I den **SQL Server-konfigurationsfilen** dialogrutan väljer du om du vill ansluta med hjälp av SQL Server-autentisering eller Windows-autentisering. Om du väljer SQL Server-autentisering anger du de befintliga autentiseringsuppgifterna. Ange namnet på SQL Server och namnet på databasen som du vill synkronisera. Välj **Testanslutning** att testa dina inställningar. Välj sedan **Spara**. Den registrerade databasen visas i listan.

        ![SQL Server-databas är nu registrerad](media/sql-database-get-started-sql-data-sync/datasync-preview-agent-dbadded.png)

   11. Nu kan du stänga appen Klientsynkroniseringsagenten.

   12. I portalen på den **konfigurera lokala** väljer **väljer du databasen.** Den **Välj databas** öppnas.

   13. På den **Välj databas** sidan den **Synkroniseringsmedlemsnamnet** fältet, ange ett namn för den nya synkroniseringsmedlemmen. Det här namnet skiljer sig från namnet på själva databasen. Välj databasen i listan. I den **Synkroniseringsriktningar** fält, Välj dubbelriktad synkronisering, till Hub eller från hubben.

        ![Välj databasen på plats](media/sql-database-get-started-sql-data-sync/datasync-preview-selectdb.png)

   14. Välj **OK** att Stäng den **Välj databas** sidan. Välj sedan **OK** att Stäng den **konfigurera lokala** sidan och vänta på ny synkroniseringsmedlemmen skapas och distribueras. Klicka slutligen på **OK** att Stäng den **Välj synkroniseringsmedlemmar** sidan.

        ![För plats-databasen som lagts till i synkroniseringsgruppen](media/sql-database-get-started-sql-data-sync/datasync-preview-onpremadded.png)

3. Anslut till SQL Data Sync och lokal agent genom att lägga till ditt användarnamn till rollen `DataSync_Executor`. Datasynkronisering skapar den här rollen på SQL Server-instansen.

## <a name="step-3---configure-sync-group"></a>Steg 3 – Konfigurera synkroniseringsgrupp

När nya synkronisera gruppmedlemmarna skapas och distribueras, steg3 **konfigurera synkroniseringsgrupp**, är markerat i den **ny synkroniseringsgrupp** sidan.

1. På den **tabeller** , Välj en databas från listan över gruppmedlemmar för synkronisering och välj sedan **uppdatera schemat**.

2. Markera de tabeller som du vill synkronisera från listan över tillgängliga tabeller.

    ![Välj tabeller att synkronisera](media/sql-database-get-started-sql-data-sync/datasync-preview-tables.png)

3. Som standard markeras alla kolumner i tabellen. Om du inte vill synkronisera alla kolumner, inaktivera kryssrutan för de kolumner som du inte vill synkronisera. Glöm inte att lämna primärnyckelkolumnen valt.

    ![Välj de fält som ska synkroniseras](media/sql-database-get-started-sql-data-sync/datasync-preview-tables2.png)

4. Välj slutligen **spara**.

## <a name="faq-about-setup-and-configuration"></a>Vanliga frågor om installation och konfiguration

### <a name="how-frequently-can-data-sync-synchronize-my-data"></a>Hur ofta kan datasynkronisering synkronisera mina data

Den lägsta frekvensen är var femte minut.

### <a name="does-sql-data-sync-fully-create-and-provision-tables"></a>SQL Data Sync sker det fullständigt skapa och etablera tabeller

Om tabellerna synkronisering schema inte är redan har skapat i måldatabasen, skapar dem i SQL Data Sync med kolumner som du har valt. Det här beteendet resulterar dock inte i ett schema för fullständig återgivning, av följande skäl:

- Endast de kolumner som du har valt skapas i tabellen. Om vissa kolumner i källtabellerna inte är en del av synkroniseringsgruppen tillhandahålls inte dessa kolumner i måltabellerna.
- Index skapas endast för de markerade kolumnerna. Om käll-Tabellindex har kolumner som inte är en del av synkroniseringsgruppen, tillhandahålls inte dessa index i måltabellerna.
- Index för kolumner av typen XML inte har etablerats.
- Kontrollbegränsningar inte har etablerats.
- Befintliga utlösare källtabellerna inte har etablerats.
- Vyer och lagrade procedurer skapas inte till måldatabasen.

På grund av dessa begränsningar rekommenderar vi följande:

- För produktionsmiljöer, etablera fullständig exakthet schemat själv.
- För att testa tjänsten fungerar funktionen Automatisk etablering i SQL Data Sync bra.

### <a name="why-do-i-see-tables-that-i-did-not-create"></a>Varför ser jag tabeller som jag inte har skapat

Datasynkronisering skapar tabeller sida i databasen för ändringsspårning. Ta inte bort dem eller datasynkronisering slutar fungera.

### <a name="is-my-data-convergent-after-a-sync"></a>Är Mina data avsmalnande efter en synkronisering

Inte nödvändigtvis. I en synkroniseringsgrupp med en hubb och tre ekrar (A, B och C) har synkroniseringarna Hub till A, b-hubb och Hub till C. Om en ändring görs till databasen A *när* hubben till en synkronisering som ändra inte skrivs till B-databas eller databas C tills den nästa synkronisering.

### <a name="how-do-i-get-schema-changes-into-a-sync-group"></a>Hur får jag schemaändringar i en synkroniseringsgrupp

Du måste göra och sprida alla schemaändringar manuellt.

1. Replikera ändringar av entitetsschemat manuellt till hubben och för alla synkroniseringsmedlemmar.
2. Uppdatera synkroniseringsschemat.

**Att lägga till nya tabeller och kolumner**.

Nya tabeller och kolumner som påverkar inte den aktuella synkroniseringen. Datasynkronisering ignorerar de nya tabeller och kolumner tills du lägger till dem till i synkroniseringsschemat. När du lägger till nya databasobjekt är det här den bästa aktivitetssekvensen ska följa:

1. Lägga till nya tabeller eller kolumner till hubben och alla synkroniseringsmedlemmar.
2. Lägga till nya tabeller eller kolumner till i synkroniseringsschemat.
3. Börja infoga värden i de nya tabeller och kolumner.

**Ändra datatypen för en kolumn**.

När du ändrar datatypen för en befintlig kolumn, fortsätter Data Sync att fungera så länge de nya värdena passar den ursprungliga datatyp som definierats i synkroniseringsschemat. Exempel: Om du ändrar typen i källdatabasen från **int** till **bigint**, datasynkronisering fortsätter att fungera tills du infogar ett värde som är för stor för den **int** datatyp . Replikera en schemaändring manuellt till hubben och för alla synkroniseringsmedlemmar för att slutföra ändringen och uppdatera sedan synkroniseringsschemat.

### <a name="how-can-i-export-and-import-a-database-with-data-sync"></a>Hur kan jag exportera och importera en databas med Data Sync

När du exporterar en databas som en `.bacpac` filen och importera filen om du vill skapa en ny databas, du behöver göra följande två saker kan använda datasynkronisering i den nya databasen:

1. Rensa Data Sync-objekt och tabeller sida på den **ny databas** med hjälp av [det här skriptet](https://github.com/vitomaz-msft/DataSyncMetadataCleanup/blob/master/Data%20Sync%20complete%20cleanup.sql). Det här skriptet tar bort alla nödvändiga Data Sync objekt från databasen.
2. Återskapa synkroniseringsgrupp med den nya databasen. Om du behöver inte längre gamla synkroniseringsgruppen kan du ta bort den.

## <a name="faq-about-the-client-agent"></a>Vanliga frågor om klientens agent

Läs vanliga frågor om klientagenten [agenten vanliga frågor och svar](sql-database-data-sync-agent.md#agent-faq).

## <a name="next-steps"></a>Nästa steg

Grattis! Du har skapat en synkroniseringsgrupp som innehåller både en SQL-databasinstans och en SQL Server-databas.

Mer information om SQL Data Sync finns i:

- [Synkronisera data i flera moln och lokala databaser med Azure SQL Data Sync](sql-database-sync-data.md)
- [Metodtips för Azure SQL Data Sync](sql-database-best-practices-data-sync.md)
- [Övervaka Azure SQL Data Sync med Log Analytics](sql-database-sync-monitor-oms.md)
- [Felsöka problem med Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)

- Slutför PowerShell-exempel som visar hur du konfigurerar SQL Data Sync:
  - [Använda PowerShell för att synkronisera mellan flera Azure SQL-databaser](scripts/sql-database-sync-data-between-sql-databases.md)
  - [Använd PowerShell för att synkronisera mellan en Azure SQL Database och en lokal SQL Server-databas](scripts/sql-database-sync-data-between-azure-onprem.md)

- [Ladda ned REST API-dokumentation för SQL Data Sync](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Mer information om SQL Database finns i:

- [Översikt över SQL Database](sql-database-technical-overview.md)
- [Livscykelhantering för databas](https://msdn.microsoft.com/library/jj907294.aspx)
