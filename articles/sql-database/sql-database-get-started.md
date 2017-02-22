---
title: "Snabbstart: Din första Azure SQL Database | Microsoft Docs"
description: "Lär dig att skapa en logisk SQL Database-server, brandväggsregel på servernivå och databaser med hjälp av Azure Portal. Du lär dig också att använda SQL Server Management Studio med Azure SQL Database."
keywords: "sql database-självstudier, skapa en sql-databas"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: aeb8c4c3-6ae2-45f7-b2c3-fa13e3752eed
ms.service: sql-database
ms.custom: single databases
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/04/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 535b16490bb601070c7d2a7135f3d69aa898be1f
ms.openlocfilehash: 10a128ea56ea014cf72036f71ec97202691bec94


---
# <a name="quick-start-tutorial-your-first-azure-sql-database"></a>Snabbstartssjälvstudier: Din första Azure SQL-databas

I den här snabbstartskursen lär du dig följande:

* [Skapa en ny logisk server](sql-database-get-started.md#create-a-new-logical-sql-server) 
* [Visa egenskaper för den logiska servern](sql-database-get-started.md#view-the-logical-server-properties) 
* [Skapa en brandväggsregel på servernivå](sql-database-get-started.md#create-a-server-level-firewall-rule) 
* [Ansluta till servern med SSMS](sql-database-get-started.md#connect-to-the-server-with-ssms) 
* [Skapa en databas med exempeldata](sql-database-get-started.md#create-a-database-with-sample-data) 
* [Visa egenskaperna för databasen](sql-database-get-started.md#view-the-database-properties) 
* [Fråga databasen i Azure Portal](sql-database-get-started.md#query-the-database-in-the-azure-portal) 
* [Anslut och fråga databasen med SSMS](sql-database-get-started.md#connect-and-query-the-database-with-ssms) 
* [Skapa en tom databas med SSMS](sql-database-get-started.md#create-a-blank-database-with-ssms) 
* [Felsökning av anslutningar](sql-database-get-started.md#troubleshoot-connectivity) 
* [Ta bort en databas](sql-database-get-started.md#delete-a-single-database) 


När du är klar med den här snabba självstudiekursen har du en exempeldatabas och en tom databas som körs i en Azure-resursgrupp och som är ansluten till en logisk server. Du har också två brandväggsregler på servernivå som har konfigurerats så att principen på servernivå kan logga in på servern från två angivna IP-adresser. 

**Uppskattad tidsåtgång**: Den här självstudiekursen tar cirka 30 minuter (förutsatt att du redan uppfyller kraven).

> [!TIP]
> Du kan utföra samma åtgärder, antingen med [C#](sql-database-get-started-csharp.md) eller [PowerShell](sql-database-get-started-powershell.md).
>

## <a name="prerequisites"></a>Krav

* Du behöver ett Azure-konto. Du kan [öppna ett kostnadsfritt Azure-konto](/pricing/free-trial/?WT.mc_id=A261C142F) eller [aktivera Visual Studio-prenumerantförmåner](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). 

* Du måste kunna ansluta till Azure Portal med ett konto som antingen har rollen prenumerationsägare eller deltagare. Mer information om rollbaserad åtkomstkontroll (RBAC) finns i [Getting started with access management in the Azure portal](../active-directory/role-based-access-control-what-is.md) (Komma igång med åtkomsthantering på Azure Portal).

> [!NOTE]
> I den här snabba självstudiekursen lär du dig mer om följande: [Översikt över SQL Database-server](sql-database-server-overview.md), [Översikt över SQL-databas](sql-database-overview.md) samt [Översikt över Azure SQL Database-brandväggsregler](sql-database-firewall-configure.md).
>  


### <a name="sign-in-to-the-azure-portal-with-your-azure-account"></a>Logga in på Azure Portal med ditt Azure-konto
använd ditt [Azuree-konto](https://account.windowsazure.com/Home/Index) och följ dessa steg för att ansluta till Azure Portal.

1. Öppna din webbläsare och anslut till [Azure-portalen](https://portal.azure.com/).
2. Logga in på [Azure-portalen](https://portal.azure.com/).
3. Ange autentiseringsuppgifterna för din prenumeration på sidan **Logga in**.
   
   ![Logga in](./media/sql-database-get-started/login.png)


<a name="create-logical-server-bk"></a>

## <a name="create-a-new-logical-sql-server"></a>Skapa en ny logisk SQL-server

Följ stegen i den här proceduren för att skapa en ny logisk server med Azure Portal i den region som du väljer.

1. Klicka på **Nytt**, skriv **sql server** och klicka sedan på **Retur**.

    ![Logisk SQL-server](./media/sql-database-get-started/logical-sql-server.png)
2. Klicka på **SQL-server (logisk server)**.
   
    ![Skapa logisk SQL-server](./media/sql-database-get-started/create-logical-sql-server.png)
3. Öppna bladet för den nya SQL-servern (logisk server) genom att klicka på **Skapa**.

    ![Ny logisk SQL-server](./media/sql-database-get-started/new-logical-sql-server.png)
3. Ange ett giltigt namn för den nya logiska servern i textrutan Servernamn. En grön kryssmarkering visar att du har angett ett giltigt namn.
    
    ![Namn på ny server](./media/sql-database-get-started/new-server-name.png)

    > [!IMPORTANT]
    > Det fullständigt kvalificerade namnet för den nya servern är <ditt_servernamn>. database.windows.net.
    >
    
4. I textrutan Inloggning för serveradministratör anger du ett användarnamn för SQL-autentiseringsinloggningen för den här servern. Den här inloggningen kallas den primära server-inloggningen. En grön kryssmarkering visar att du har angett ett giltigt namn.
    
    ![SQL-administratörsinloggning](./media/sql-database-get-started/sql-admin-login.png)
5. I textrutorna **Lösenord** och **Bekräfta lösenord** anger du ett lösenord för inloggningskontot för serverhuvudobjektet. En grön kryssmarkering visar att du har angett ett giltigt lösenord.
    
    ![SQL-administratörslösenord](./media/sql-database-get-started/sql-admin-password.png)
6. Välj en prenumeration som du har behörighet att skapa objekt i.

    ![prenumeration](./media/sql-database-get-started/subscription.png)
7. I textrutan Resursgrupp väljer du **Skapa nytt** och anger sedan ett giltigt namn för den nya resursgruppen i textrutan Resursgrupp. (Du kan också använda en befintlig resursgrupp om du redan har skapat en.) En grön kryssmarkering visar att du har angett ett giltigt namn.

    ![Ny resursgrupp](./media/sql-database-get-started/new-resource-group.png)

8. I textrutan **Plats** väljer du ett lämpligt datacenter för din plats, t.ex. ”Australien, östra”.
    
    ![Serverplats](./media/sql-database-get-started/server-location.png)
    
    > [!TIP]
    > Du kan inte ändra kryssrutan för **Ge Azure-tjänster åtkomst till servern** på det här bladet. Du kan ändra den här inställningen på bladet Serverbrandvägg. Mer information finns i [Get started with security](sql-database-control-access-sql-authentication-get-started.md) (Komma igång med säkerhet).
    >
    
9. Klicka på **Skapa**.

    ![Knappen Skapa](./media/sql-database-get-started/create.png)

## <a name="view-the-logical-server-properties"></a>Visa egenskaper för den logiska servern

Följ stegen i den här proceduren för att visa egenskaperna för servern med Azure Portal. Du behöver det fullständigt kvalificerade servernamnet för att ansluta till den här servern i en senare procedur. 

1. Klicka på **Fler tjänster** på Azure Portal.

    ![Fler tjänster](./media/sql-database-get-started/more-services.png)
2. I textrutan Filter skriver du **SQL** och klickar sedan på stjärnan för SQL-servrar om du vill ange SQL-servrar som favoriter i Azure. 

    ![Ange favorit](./media/sql-database-get-started/favorite.png)
3. På standardbladet klickar du på **SQL-servrar** för att öppna listan över SQL-servrar i din Azure-prenumeration. 

    ![Ny SQL-server](./media/sql-database-get-started/new-sql-server.png)

4. Klicka på den nya SQL-servern för att visa serverns egenskaper på Azure Portal. I efterföljande självstudiekurser lär du dig mer om de tillgängliga alternativen på det här bladet.

    ![Bladet SQL-server](./media/sql-database-get-started/sql-server-blade.png)
5. Under Inställningar klickar du på **Egenskaper** för att visa olika egenskaper för den logiska SQL-servern.

    ![Egenskaper för SQL-server](./media/sql-database-get-started/sql-server-properties.png)
6. Kopiera det fullständigt kvalificerade servernamnet till Urklipp för användning lite senare i den här självstudiekursen.

    ![SQL-serverns fullständiga namn](./media/sql-database-get-started/sql-server-full-name.png)

## <a name="create-a-server-level-firewall-rule"></a>Skapa en brandväggsregel på servernivå

Följ stegen i den här proceduren för att skapa en ny brandväggsregel på servernivå med Azure Portal så att du kan ansluta till servern med SQL Server Management Studio i nästa procedur.

1. Öppna bladet Brandvägg för SQL-servern genom att klicka på **Brandvägg** under Inställningar på bladet SQL-server.

    ![Brandvägg för SQL-server](./media/sql-database-get-started/sql-server-firewall.png)

2. Klicka på **Lägg till klient-IP** i verktygsfältet.

    ![Lägg till klient-IP](./media/sql-database-get-started/add-client-ip.png)

    > [!NOTE]
    > Du kan öppna SQL Database-brandväggen på servern för en enskild IP-adress eller ett helt adressintervall. Om du öppnar brandväggen kan SQL-administratörer och användare logga in i valfri databas på servern som de har giltiga autentiseringsuppgifter för.
    >

4. Klicka på **Spara** i verktygsfältet för att spara den här brandväggsregeln på servernivå och klicka sedan på **OK**.

    ![Lägg till klient-IP](./media/sql-database-get-started/save-firewall-rule.png)

## <a name="connect-to-the-server-with-ssms"></a>Ansluta till servern med SSMS

Följ stegen i den här proceduren för att ansluta till den logiska SQL-servern med SQL Server Management Studio.

1. Om du inte redan har gjort det hämtar du och installerar den senaste versionen av SSMS på [Download SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (Hämta SQL Server Management Studio). Om du vill hålla programmen uppdaterade, meddelar den senaste versionen av SSMS dig om när det finns en ny version tillgänglig för nedladdning.

2. När installationen är klar skriver du **Microsoft SQL Server Management Studio** i sökrutan i Windows och klickar på **Retur** för att öppna SSMS:

    ![SQL Server Management Studio](./media/sql-database-get-started/ssms.png)
3. I dialogrutan Anslut till server anger du nödvändig information för att ansluta till SQL-servern med hjälp av SQL Server-autentisering.

    ![Anslut till server](./media/sql-database-get-started/connect-to-server.png)
4. Klicka på **anslut**.

    ![Ansluten till server](./media/sql-database-get-started/connected-to-server.png)
5. I Object Explorer expanderar du **Databaser**, expanderar **Systemdatabaser** och expanderar sedan **Huvud** för att visa objekt i huvuddatabasen.

    ![Huvuddatabas](./media/sql-database-get-started/master-database.png)
6. Högerklicka på **Huvud** och klicka sedan på **Ny fråga**.

    ![Fråga huvuddatabasen](./media/sql-database-get-started/query-master-database.png)

8. Skriv följande fråga i frågefönstret:

   ```select * from sys.objects```

9.  Klicka på **Kör** i verktygsfältet för att returnera en lista över alla systemobjekt i huvuddatabasen.

    ![Fråga efter systemobjekt i huvuddatabasen](./media/sql-database-get-started/query-master-database-system-objects.png)

    > [!NOTE]
    > Mer information om säkerheten i SQL finns i [Get Started with SQL security](sql-database-control-access-sql-authentication-get-started.md) (Komma igång med SQL-säkerhet)
    >

## <a name="create-a-database-with-sample-data"></a>Skapa en databas med exempeldata

Följ stegen i den här proceduren för att skapa en databas med exempeldata med Azure Portal. Du skapar den här databasen med en anslutning till den logiska server som du skapade tidigare. Om tjänstnivån Grundläggande inte är tillgänglig i den region där du skapade servern tar du bort servern och återskapar den i en annan region. Steg för borttagning finns i föregående procedur i den här handledningen.

1. På Azure Portal klickar du på **SQL-databaser** på standardbladet.

    ![SQL-databaser](./media/sql-database-get-started/new-sql-database.png)
2. Klicka på **Lägg till** på bladet SQL-databaser.

    ![Lägg till SQL-databas](./media/sql-database-get-started/add-sql-database.png)
3. Granska informationen som fyllts i automatiskt på bladet SQL Database.

    ![Bladet SQL-databas](./media/sql-database-get-started/sql-database-blade.png)
4. Ange ett giltigt databasnamn.

    ![SQL-databasens namn](./media/sql-database-get-started/sql-database-name.png)
5. Klicka på **Exempel** under Välj källa och klicka sedan på **AdventureWorksLT [V12]** under Välj exempel.
   
    ![Adventure Works LT](./media/sql-database-get-started/adventureworkslt.png)
6. Under Server anger du användarnamnet och lösenordet för inloggning som serveradministratör.

    ![Serverautentiseringsuppgifter](./media/sql-database-get-started/server-credentials.png)

    > [!NOTE]
    > När du lägger till en databas till en server kan den läggs till som en enskild databas (det här är standardinställningen) eller i en elastisk pool. Mer information om elastiska pooler finns i avsnittet om [elastiska pooler](sql-database-elastic-pool.md).
    >

7. Under Prisnivå ändrar du prisnivån till **Basic**. (Du kan öka prisnivån senare om du vill, men i utbildningssyfte rekommenderar vi att du använder den lägsta kostnadsnivån.)

    ![prisnivå](./media/sql-database-get-started/pricing-tier.png)
8. Klicka på **Skapa**.

    ![Knappen Skapa](./media/sql-database-get-started/create.png)

## <a name="view-the-database-properties"></a>Visa egenskaperna för databasen

Följ stegen i den här proceduren för att fråga databasen med Azure Portal.

1. På bladet SQL-databaser klickar du på den nya databasen för att visa databasens egenskaper på Azure Portal. I efterföljande självstudiekurser lär du dig mer om de tillgängliga alternativen på det här bladet. 

    ![Blad för ny exempeldatabas](./media/sql-database-get-started/new-sample-db-blade.png)
2. Klicka på **Egenskaper** för att visa ytterligare information om databasen.

    ![Egenskaper för ny exempeldatabas](./media/sql-database-get-started/new-sample-db-properties.png)

3. Klicka på **Visa databasanslutningssträngar**.

    ![Anslutningssträngar för ny exempeldatabas](./media/sql-database-get-started/new-sample-db-connection-strings.png)
4. Klicka på **Översikt** och klicka sedan på namnet på din server i rutan Information.
    
    ![Informationsfönster för ny exempeldatabas](./media/sql-database-get-started/new-sample-db-essentials-pane.png)
5. I fönstret Information för servern ser du databasen som du lagt till.

    ![Ny exempeldatabas i fönstret för serverinformation](./media/sql-database-get-started/new-sample-db-server-essentials-pane.png)

## <a name="query-the-database-in-the-azure-portal"></a>Fråga databasen i Azure Portal

Följ stegen i den här proceduren för att söka i databasen med frågeredigeraren i Azure Portal. Frågan visar objekten i databasen.

1. Klicka på **Verktyg** i verktygsfältet på bladet SQL-databaser.

    ![verktyg](./media/sql-database-get-started/tools.png)
2. Gå till Verktyg-bladet och klicka på **Frågeredigeraren (förhandsgranskning)**.

    ![frågeredigerare](./media/sql-database-get-started/query-editor.png)
3. Klicka i kryssrutan för att bekräfta att frågeredigeraren är en förhandsvisningsfunktion och klicka sedan på **OK**.
4. På bladet **Frågeredigerare** klickar du på **Logga in**.

    ![bladet frågeredigerare](./media/sql-database-get-started/query-editor-blade.png)
5. Granska auktoriseringstypen och inloggningen och ange sedan lösenordet för inloggningen. 

    ![query editor-inloggning](./media/sql-database-get-started/query-editor-login.png)
6. Klicka på **OK** för att försöka logga in.
7. När det visas ett inloggningsfelmeddelande som anger att klienten inte har behörighet att logga in på grund av att det inte finns någon brandväggsregel för din klients IP-adress kan du kopiera din klients IP-adress i felfönstret och skapa en brandväggsregel på servernivå på SQL-serverbladet för den här databasen.

    ![fel i frågeredigerare](./media/sql-database-get-started/query-editor-error.png)
8. Upprepa föregående sex steg för att logga in till databasen.
9. När du är autentiserad skriver du följande fråga i frågefönstret:

   ```select * from sys.objects```

    ![query editor query](./media/sql-database-get-started/query-editor-query.png) 10 Klicka på **Kör**.
11. Granska frågeresultaten i fönstret **Resultat**.

    ![resultat från frågeredigeraren](./media/sql-database-get-started/query-editor-results.png)

## <a name="connect-and-query-the-database-with-ssms"></a>Anslut och fråga databasen med SSMS

Följ stegen i den här proceduren om du vill ansluta till databasen med SQL Server Management Studio och söka efter exempeldata och visa objekten i databasen.

1. Växla till SQL Server Management Studio och klicka på **Databaser** i Object Explorer och klicka sedan på **Uppdatera** i verktygsfältet för att visa exempeldatabasen.

    ![Ny exempeldatabas med SSMS](./media/sql-database-get-started/new-sample-db-ssms.png)
2. I Object Explorer expanderar du den nya databasen för att visa databasens objekt.

    ![Objekt i den nya exempeldatabasen med SSMS](./media/sql-database-get-started/new-sample-db-objects-ssms.png)
3. Högerklicka på exempeldatabasen och klicka sedan på **Ny fråga**.

    ![Fråga mot den nya exempeldatabasen med SSMS](./media/sql-database-get-started/new-sample-db-query-ssms.png)
4. Skriv följande fråga i frågefönstret:

   ```select * from sys.objects```
   
9.  Klicka på **Kör** i verktygsfältet för att returnera en lista över alla systemobjekt i exempeldatabasen.

    ![Fråga efter systemobjekt i den nya exempeldatabasen med hjälp av SSMS](./media/sql-database-get-started/new-sample-db-query-objects-ssms.png)

## <a name="create-a-blank-database-with-ssms"></a>Skapa en tom databas med SSMS

Följ stegen i den här proceduren för att skapa en ny databas på den logiska servern med SQL Server Management Studio.

1. Högerklicka på **Databaser** i Object Explorer och klicka sedan på **Ny databas**.

    ![Ny tom databas med SSMS](./media/sql-database-get-started/new-blank-database-ssms.png)

    > [!NOTE]
    > Du kan också ange att SSMS ska skapa ett databasgenereringsskript så att du kan skapa en ny databas med hjälp av Transact-SQL.
    >

2. Ange ett databasnamn i textrutan Databasnamn i dialogrutan Ny databas. 

    ![Namn på ny tom databas med SSMS](./media/sql-database-get-started/new-blank-database-name-ssms.png)

3. I dialogrutan Ny databas klickar du på **Alternativ** och ändrar sedan utgåvan till **Basic**.

    ![Alternativ för ny tom databas med SSMS](./media/sql-database-get-started/new-blank-database-options-ssms.png)

    > [!TIP]
    > Gå igenom de andra alternativen i den här dialogrutan som du kan ändra för Azure SQL Database. Mer information om dessa alternativ finns i [Skapa databas](https://msdn.microsoft.com/library/dn268335.aspx).
    >

4. Skapa den tomma databasen genom att klicka på **OK**.
5. När det är klart uppdaterar du noden Databas i Object Explorer så att den nya tomma databasen visas. 

    ![Ny tom databas i Object Explorer](./media/sql-database-get-started/new-blank-database-object-explorer.png)

## <a name="troubleshoot-connectivity"></a>Felsökning av anslutningar

> [!IMPORTANT]
> Om du har problem med nätverksanslutningen, se [Anslutningsproblem](sql-database-troubleshoot-common-connection-issues.md).
> 

## <a name="delete-a-single-database"></a>Ta bort en enkel databas

Följ stegen i den här proceduren om du vill ta bort en enkel databas med Azure Portal.

1. Gå till bladet i Azure Portal för SQL-databasen och klicka på **Ta bort**.

    ![delete-database](./media/sql-database-get-started/delete-database.png)
2. Bekräfta att du vill ta bort databasen permanent genom att klicka på **Ja**.

    ![delete-database-yes](./media/sql-database-get-started/delete-database-yes.png)

> [!TIP]
> Under kvarhållningsperioden för databasen kan du återställa den från de tjänstinitierade automatiska säkerhetskopiorna. Databaser med Basic-versionen kan återställas inom sju dagar. Men ta inte bort en server. Om du gör det kan du inte återställa servern eller någon av dess borttagna databaser. Mer information om säkerhetskopiering av databaser finns i [Learn about SQL Database backups ](sql-database-automated-backups.md) (Läs om SQL Database-säkerhetskopior) och information om hur du återställer en databas från säkerhetskopior finns i [Database recovery](sql-database-recovery-using-backups.md) (Databasåterställning). En artikel om hur du återställer en borttagen databas finns i [Restore a deleted Azure SQL database – Azure Porta](sql-database-restore-deleted-database-portal.md) (Återställa en borttagen SQL Azure-databas – Azure Portal).
>


## <a name="next-steps"></a>Nästa steg
Nu när du är klar med den här självstudiekursen finns det ytterligare några självstudiekurser som du kanske vill utforska och som bygger vidare på det du har lärt dig i den här kursen. 

- En självstudiekurs som hjälper dig att komma igång med SQL Server-autentisering finns i [SQL-autensiering och -auktorisering](sql-database-control-access-sql-authentication-get-started.md)
- En självstudiekurs som hjälper dig att komma igång med Azure Active Directory-autentisering finns i [AAD-autensiering och -auktorisering](sql-database-control-access-aad-authentication-get-started.md)
* Om du vill fråga exempeldatabasen i Azure Portal kan du läsa [Public preview: Interactive query experience for SQL databases](https://azure.microsoft.com/en-us/updates/azure-sql-database-public-preview-t-sql-editor/) (Allmänt tillgänglig förhandsversion: Interaktiva frågor för SQL-databaser)
* Om du kan Excel, lär du dig hur man [Ansluter till en SQL Database i Azure med Excel](sql-database-connect-excel.md).
* Om du är redo att börja koda, väljer du programmeringsspråk på [Anslutningsbibliotek för SQL Database och SQL Server](sql-database-libraries.md).
* Om du vill flytta dina lokala SQL Server-databaser till Azure läser du [Migrera en databas till SQL Database](sql-database-cloud-migrate.md).
* Om du vill läsa in data i en ny tabell från en CSV-fil med hjälp av kommandoradsverktyget BCP, se [Läs in data i SQL Database från en CSV-fil med BCP](sql-database-load-from-csv-with-bcp.md).
* Om du vill börja skapa tabeller och andra objekt läser du avsnittet ”Så här skapar du en tabell” i [Skapa en tabell](https://msdn.microsoft.com/library/ms365315.aspx).

## <a name="additional-resources"></a>Ytterligare resurser

- En teknisk översikt finns i [Vad är SQL Database?](sql-database-technical-overview.md).
- För information om priser se [Prisinformation för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).




<!--HONumber=Feb17_HO1-->


