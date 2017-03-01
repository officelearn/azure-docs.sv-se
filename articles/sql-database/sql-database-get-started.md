---
title: "Snabbstart: Din första Azure SQL Database | Microsoft Docs"
description: "Lär dig att skapa en logisk SQL Database-server, brandväggsregel på servernivå och databaser i Azure Portal. Du lär dig också att använda SQL Server Management Studio med Azure SQL Database."
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
ms.date: 02/17/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 166a9d7032bb75188a790bea1724aefd194dcefa
ms.openlocfilehash: 36afd5c8bccb080ae3aaf1b4975d317b9087a3b3


---
# <a name="create-connect-to-and-query-your-first-azure-sql-databases-in-the-azure-portal-and-using-ssms"></a>Skapa, anslut till och fråga din första Azure SQL Database i Azure Portal med hjälp av SSMS

I den här självstudien lär du dig hur du skapar, ansluter till och frågar Azure SQL-databaser i Azure Portal med SQL Server Management Studio. När du har slutfört den här kursen har du:

* Skapat en resursgrupp som innehåller en logisk server, en brandväggsregel på servernivå och två databaser.
* Lärt dig hur du ser server- och databasegenskaper i Azure Portal och med hjälp av SQL Server Management Studio.
* Lärt dig hur du frågar en databas i Azure Portal och med hjälp av SQL Server Management Studio.

**Uppskattad tidsåtgång**: Den här självstudiekursen tar cirka 30 minuter (förutsatt att du redan uppfyller kraven).

> [!TIP]
> Du kan även lära dig att skapa, ansluta till och fråga en Azure SQL-databas med antingen [PowerShell](sql-database-get-started-powershell.md) eller [C#](sql-database-get-started-csharp.md).
>

> [!NOTE]
> I den här självstudiekursen lär du dig mer om följande: [Översikt över SQL Database-server](sql-database-server-overview.md), [Översikt över SQL Databas](sql-database-overview.md) och [Översikt över Azure SQL Database-brandväggsregler](sql-database-firewall-configure.md). En översikt över SQL Database-tjänsten finns i [Vad är SQL Database?](sql-database-technical-overview.md).
>  

## <a name="prerequisites"></a>Krav

* **Ett Azure-konto**. Du kan [öppna ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) eller [aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/). 

* **Azure skapa behörigheter**. Du måste kunna ansluta till Azure Portal med ett konto som antingen har rollen prenumerationsägare eller deltagare. Mer information om rollbaserad åtkomstkontroll (RBAC) finns i [Getting started with access management in the Azure portal](../active-directory/role-based-access-control-what-is.md) (Komma igång med åtkomsthantering på Azure Portal).

* **SQL Server Management Studio**. Du kan hämta och installera den senaste versionen av SQL Server Management Studio (SSMS) på [Download SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (Hämta SQL Server Management Studio). Använd alltid den senaste versionen av SSMS när du ansluter till Azure SQL Database eftersom nya funktioner släpps kontinuerligt.

### <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Stegen i den här proceduren visar hur du ansluter till Azure Portal med ditt [Azure-konto](https://account.windowsazure.com/Home/Index).

1. Öppna din webbläsare och anslut till [Azure-portalen](https://portal.azure.com/).
2. Logga in på [Azure-portalen](https://portal.azure.com/).
3. Ange autentiseringsuppgifterna för din prenumeration på sidan **Logga in**.
   
   ![Logga in](./media/sql-database-get-started/login.png)


<a name="create-logical-server-bk"></a>

## <a name="create-a-new-logical-sql-server"></a>Skapa en ny logisk SQL-server

Stegen i den här proceduren visar hur du skapar en logisk server i Azure Portal i den region som du väljer. En logisk server är objektet som du skapar SQL-databaser i, och det objekt i vilket du skapar brandväggsregler för att tillåta användare att ansluta genom Azure SQL Database-brandväggen. 

1. Klicka på **Nytt**, skriv **sql server** och klicka sedan på **Retur**.

    ![Logisk SQL-server](./media/sql-database-get-started/logical-sql-server.png)
2. Klicka på **SQL-server (logisk server)**.
   
    ![Skapa logisk SQL-server](./media/sql-database-get-started/create-logical-sql-server.png)
3. Öppna endast bladet för den nya SQL-servern (logisk server) genom att klicka på **Skapa**.

    ![Ny logisk SQL-server](./media/sql-database-get-started/new-logical-sql-server.png)
3. Ange ett giltigt namn för den nya logiska servern i textrutan **Servernamn**. En grön kryssmarkering visar att du har angett ett giltigt namn.
    
    ![Namn på ny server](./media/sql-database-get-started/new-server-name.png)

    > [!IMPORTANT]
    > Det fullständigt kvalificerade namnet för den nya servern måste vara globalt unikt och i formatet: **<ditt_servernamn>.database.windows.net**. Du kommer att använda det här fullständiga servernamnet senare i den här självstudien för att ansluta till servern och databasen.
    >
    
4. I textrutan **Inloggning för serveradministratör** anger du ett användarnamn för SQL-autentiseringsinloggningen för den här servern. Den här inloggningen kallas den primära server-inloggningen. En grön kryssmarkering visar att du har angett ett giltigt namn.
    
    ![SQL-administratörsinloggning](./media/sql-database-get-started/sql-admin-login.png)
5. I textrutorna **Lösenord** och **Bekräfta lösenord** anger du ett lösenord för inloggningskontot för serverhuvudobjektet. En grön kryssmarkering visar att du har angett ett giltigt lösenord.
    
    ![SQL-administratörslösenord](./media/sql-database-get-started/sql-admin-password.png)
6. Öppna listrutan **Prenumeration** och välj en prenumeration som du har behörighet att skapa objekt i.

    ![prenumeration](./media/sql-database-get-started/subscription.png)
7. Välj **Skapa nytt** under textrutan **Resursgrupp** och ange sedan ett giltigt namn för den nya resursgruppen. En grön kryssmarkering visar att du har angett ett giltigt namn.

    ![Ny resursgrupp](./media/sql-database-get-started/new-resource-group.png)

8. I textrutan **Plats** väljer du ett datacenter där du skapar din logiska server.
    
    ![Serverplats](./media/sql-database-get-started/server-location.png)
    
    > [!TIP]
    > Du kan inte ändra kryssrutan för **Ge Azure-tjänster åtkomst till servern** på det här bladet. Du kan ändra den här inställningen på bladet Serverbrandvägg. Mer information finns i [Get started with security](sql-database-control-access-sql-authentication-get-started.md) (Komma igång med säkerhet).
    >
    
9. Markera kryssrutan **Fäst på instrumentpanelen**.

10. Klicka på **Skapa** för att distribuera det här skriptet till Azure och skapa den logiska servern.

    ![Knappen Skapa](./media/sql-database-get-started/create.png)

11. När servern har skapats kan du granska egenskaperna för den. Egenskaperna visas som standard. 

    ![Bladet SQL-server](./media/sql-database-get-started/sql-server-blade.png)
12. Klicka på **Egenskaper** för att se ytterligare egenskaper för den logiska SQL-servern.

    ![Egenskaper för SQL-server](./media/sql-database-get-started/sql-server-properties.png)
13. Kopiera det fullständigt kvalificerade servernamnet till Urklipp för användning lite senare i den här självstudiekursen.

    ![SQL-serverns fullständiga namn](./media/sql-database-get-started/sql-server-full-name.png)

## <a name="create-a-server-level-firewall-rule"></a>Skapa en brandväggsregel på servernivå

Stegen i den här proceduren visar hur du skapar en brandväggsregel på servernivån i Azure Portal. Som standard förhindrar en Azure SQL Database-brandvägg extern anslutning till den logiska servern och dess databaser. För att göra det möjligt att ansluta till servern måste du skapa en brandväggsregel för IP-adressen för datorn som du ansluter i nästa procedur. Mer information finns i [Översikt över Azure SQL Database-brandväggsregler](sql-database-firewall-configure.md).

1. Öppna bladet Brandvägg för servern genom att klicka på **Brandvägg** på bladet SQL-server. Observera att IP-adressen visas för klientdatorn.

    ![Brandvägg för SQL-server](./media/sql-database-get-started/sql-server-firewall.png)

2. Klicka på **Lägg till klient-IP** på verktygsfältet för att skapa en regel för din aktuella IP-adress.

    ![Lägg till klient-IP](./media/sql-database-get-started/add-client-ip.png)

    > [!NOTE]
    > Du kan skapa en brandväggsregel på servernivå för en enskild IP-adress eller för en hel adressintervall. Om du öppnar brandväggen kan SQL-administratörer och användare logga in i valfri databas på servern som de har giltiga autentiseringsuppgifter för.
    >

4. Klicka på **Spara** i verktygsfältet för att spara den här brandväggsregeln på servernivå och klicka sedan på **OK** för att stänga dialogrutan.

    ![lyckades](./media/sql-database-get-started/save-firewall-rule.png)

## <a name="connect-to-the-server-with-ssms"></a>Ansluta till servern med SSMS

Stegen i den här proceduren visar dig hur du ansluter till den logiska SQL-servern med SQL Server Management Studio. SSMS är det primära verktyget som DBA använder för att hantera SQL-servrar och databaser.

1. Öppna SQL Server Management Studio (Ange **Microsoft SQL Server Management Studio** i sökrutan i Windows och klicka på **Retur** för att öppna SSMS).

    ![SQL Server Management Studio](./media/sql-database-get-started/ssms.png)
3. I dialogrutan **Anslut till server** anger du det fullständiga kvalificerade servernamnet från den föregående proceduren, väljer SQL Server-autentisering och anger sedan inloggningsnamnet och lösenordet som du angav vid etablering av servern.

    ![Anslut till server](./media/sql-database-get-started/connect-to-server.png)
4. Klicka på **Anslut** att initiera anslutningen och öppna Object Explorer i SSMS.

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
    > Mer information om hur du kommer igång med SQL-säkerhet finns i [Get Started with SQL security](sql-database-control-access-sql-authentication-get-started.md) (Komma igång med SQL-säkerhet)
    >

## <a name="create-a-database-with-sample-data"></a>Skapa en databas med exempeldata

Stegen i den här proceduren visar hur du skapar en databas med exempeldata i Azure Portal som är kopplade till den logiska servern som du skapade tidigare. 

1. På Azure Portal klickar du på **SQL-databaser** på standardbladet.

    ![SQL-databaser](./media/sql-database-get-started/new-sql-database.png)
2. Klicka på **Lägg till** på bladet SQL-databaser. 

    ![Lägg till SQL-databas](./media/sql-database-get-started/add-sql-database.png)

    ![Bladet SQL-databas](./media/sql-database-get-started/sql-database-blade.png)
3. I textrutan **Databasnamn** anger du ett giltigt namn för en databas.

    ![SQL-databasens namn](./media/sql-database-get-started/sql-database-name.png)
4. Under **Välj källa** väljer du **Exempel (AdventureWorksLT)**.
   
    ![Adventure Works LT](./media/sql-database-get-started/adventureworkslt.png)
5. Kontrollera att servern har valts under **Server**. Observera att en databas kan läggas till som en enkel databas när den läggs till i en server (det här är standardinställningen) eller i en elastisk pool. Mer information om elastiska pooler finns i avsnittet om [elastiska pooler](sql-database-elastic-pool.md).

6. Ändra prisnivån till **Basic** under **Prisnivå** och klicka på **Välj**. Du kan öka prisnivån senare om du vill, men i utbildningssyfte rekommenderar vi att du använder den lägsta kostnadsnivån.

    ![prisnivå](./media/sql-database-get-started/pricing-tier.png)
7. Markera kryssrutan **Fäst på instrumentpanelen** och klicka sedan på **Skapa**.

    ![Knappen Skapa](./media/sql-database-get-started/create.png)

8. När databasen har skapats kan du se dess egenskaper i Azure Portal. I efterföljande självstudiekurser lär du dig mer om de tillgängliga alternativen på det här bladet. 

    ![Blad för ny exempeldatabas](./media/sql-database-get-started/new-sample-db-blade.png)

## <a name="query-the-database-in-the-azure-portal"></a>Fråga databasen i Azure Portal

Stegen i den här proceduren visar dig hur du frågar databasen direkt i Azure Portal. 

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
7. När du är autentiserad skriver du följande fråga i frågefönstret och klickar sedan på **Kör**.

   ```select * from sys.objects```

    ![fråga frågeredigeraren](./media/sql-database-get-started/query-editor-query.png)

8. Granska frågeresultaten i fönstret **Resultat**.

    ![resultat från frågeredigeraren](./media/sql-database-get-started/query-editor-results.png)

## <a name="query-the-database-with-ssms"></a>Fråga databasen med SSMS

Stegen i den här proceduren visar dig hur du ansluter till databasen med SQL Server Management Studio och söker efter exempeldata för att visa objekten i databasen.

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

Stegen i den här proceduren visar dig hur du skapar en ny databas med SQL Server Management Studio.

1. Högerklicka på **Databaser** i Object Explorer och klicka sedan på **Ny databas**.

    ![Ny tom databas med SSMS](./media/sql-database-get-started/new-blank-database-ssms.png)

2. Ange ett databasnamn i textrutan Databasnamn i dialogrutan **Ny databas**. 

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

Du får felmeddelanden när anslutningen till Azure SQL Database misslyckas. Anslutningsproblem kan orsakas av omkonfiguration av SQL Azure-databasen, brandväggsinställningar, timeout för anslutning eller felaktig inloggningsinformation. Ett felsökningsverktyg för anslutningar finns i [Felsöka anslutningsproblem med Microsoft Azure SQL Database](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database).

## <a name="delete-a-single-database-in-the-azure-portal"></a>Ta bort en enkel databas med Azure Portal

Stegen i den här proceduren visar dig hur du tar bort en enkel databas med Azure Portal.

1. På bladet SQL-databaser i Azure Portal klickar du på databasen som du vill ta bort. 
2.  Klicka på **Ta bort** för din SQL-databas.

    ![delete-database](./media/sql-database-get-started/delete-database.png)
2. Bekräfta att du vill ta bort databasen permanent genom att klicka på **Ja**.

    ![delete-database-yes](./media/sql-database-get-started/delete-database-yes.png)

> [!TIP]
> Under kvarhållningsperioden för databasen kan du återställa den från de tjänstinitierade automatiska säkerhetskopiorna (Förutsatt att du inte tar bort själva servern). Databaser med Basic-versionen kan återställas inom sju dagar. Du kan återställa alla andra versioner inom 35 dagar. Om du tar bort själva servern kan du inte återställa servern eller någon av dess borttagna databaser. Mer information om säkerhetskopiering av databaser finns i [Learn about SQL Database backups ](sql-database-automated-backups.md) (Läs om SQL Database-säkerhetskopior) och information om hur du återställer en databas från säkerhetskopior finns i [Database recovery](sql-database-recovery-using-backups.md) (Databasåterställning). En artikel om hur du återställer en borttagen databas finns i [Restore a deleted Azure SQL database – Azure Portal](sql-database-restore-deleted-database-portal.md) (Återställa en borttagen SQL Azure-databas – Azure Portal).
>


## <a name="next-steps"></a>Nästa steg
Nu när du är klar med den här självstudiekursen finns det ytterligare några självstudiekurser som du kanske vill utforska och som bygger vidare på det du har lärt dig i den här kursen. 

- En självstudiekurs som hjälper dig att komma igång med SQL Server-autentisering finns i [SQL-autensiering och -auktorisering](sql-database-control-access-sql-authentication-get-started.md)
- En självstudiekurs som hjälper dig att komma igång med Azure Active Directory-autentisering finns i [AAD-autensiering och -auktorisering](sql-database-control-access-aad-authentication-get-started.md)
* Om du vill fråga exempeldatabasen i Azure Portal kan du läsa [Public preview: Interactive query experience for SQL databases](https://azure.microsoft.com/updates/azure-sql-database-public-preview-t-sql-editor/) (Allmänt tillgänglig förhandsversion: Interaktiva frågor för SQL-databaser)
* Om du kan Excel, lär du dig hur man [Ansluter till en SQL Database i Azure med Excel](sql-database-connect-excel.md).
* Om du är redo att börja koda, väljer du programmeringsspråk på [Anslutningsbibliotek för SQL Database och SQL Server](sql-database-libraries.md).
* Om du vill flytta dina lokala SQL Server-databaser till Azure läser du [Migrera en databas till SQL Database](sql-database-cloud-migrate.md).
* Om du vill läsa in data i en ny tabell från en CSV-fil med hjälp av kommandoradsverktyget BCP, se [Läs in data i SQL Database från en CSV-fil med BCP](sql-database-load-from-csv-with-bcp.md).
* Om du vill börja skapa tabeller och andra objekt läser du avsnittet ”Så här skapar du en tabell” i [Skapa en tabell](https://msdn.microsoft.com/library/ms365315.aspx).

## <a name="additional-resources"></a>Ytterligare resurser

- En teknisk översikt finns i [Vad är SQL Database?](sql-database-technical-overview.md)
- För information om priser se [Prisinformation för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).




<!--HONumber=Feb17_HO3-->


