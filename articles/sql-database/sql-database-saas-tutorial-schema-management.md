---
title: "Hantera Azure SQL Database-schema i en app för flera klienter | Microsoft Docs"
description: "Hantera scheman för flera klienter i ett program för flera klienter som använder Azure SQL Database"
keywords: sql database tutorial
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 19d02229781186053a0063af1c7e1a3280179f46
ms.contentlocale: sv-se
ms.lasthandoff: 05/12/2017


---
# <a name="manage-schema-for-multiple-tenants-in-the-wtp-saas-application"></a>Hantera scheman för flera klienter i WTP SaaS-programmet

Guiden introduktion till WTP-programmet visar hur WTP-appen kan etablera en klientdatabas med sitt ursprungliga schema och registrera den i katalogen. Precis som alla program, utvecklas WTP-appen med tiden och kommer ibland att kräva ändringar i databasen. Ändringarna kan inkludera nya eller ändrade scheman, nya eller ändrade referensdata och vanliga uppgifter för databasunderhåll för att säkerställa optimal apprestanda. Med ett SaaS-program, behöver de här ändringarna distribueras på ett samordnat sätt över en potentiellt massiv mängd klientdatabaser. Ändringarna måste också inkluderas i etableringsprocessen för framtida klientdatabaser.

Den här guiden utforskar två scenarier. Distribuering av uppdateringar av referensdata för alla klienter och återställning av ett index i tabellen med referensdata. Funktionen [elastiska jobb](sql-database-elastic-jobs-overview.md) används för att utföra de här åtgärderna på alla klienter och en *gyllene* klientdatabas som används som mall för nya databaser.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]

> * Skapar ett jobbkonto för att fråga över flera klienter
> * Uppdaterar data i alla klientdatabaser
> * Skapar ett index i en tabell i alla klientdatabaser


Se till att följande förhandskrav är slutförda för att kunna slutföra den här guiden:

* WTP-appen ska ha distribuerats. Du kan distribuera appen på mindre än fem minuter genom att följa anvisningarna i [Distribuera och utforska WTP SaaS-appen](sql-database-saas-tutorial.md)
* Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Den senaste versionen av SQL Server Management Studio (SSMS) ska vara installerad. [Ladda ned och installera SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

*Den här guiden använder funktioner för SQL Database-tjänsten som är i en begränsad förhandsgranskning (elastiska databasjobb). Om du vill följa den här guiden, måste du ange ditt prenumerations-ID till SaaSFeedback@microsoft.com med ämnet = Förhandsgranskning av elastiska jobb. När du fått en bekräftelse att din prenumeration har aktiverats kan du, [ladda ned och installera den senaste förhandsversionen av jobs-cmdletarna](https://github.com/jaredmoo/azure-powershell/releases). Eftersom det här är en begränsad förhandsgranskning, bör du kontakta SaaSFeedback@microsoft.com för relaterade frågor eller support.*


## <a name="introduction-to-saas-schema-management-patterns"></a>Introduktion till SaaS-schemahanteringsmönster

SaaS-mönstret för enskilda innehavare per databas har många fördelar av dataisoleringen som uppstår, men samtidigt introduceras den ytterligare komplexiteten med att underhålla och hantera flera databaser. [Elastiska jobb](sql-database-elastic-jobs-overview.md) underlättar administration och hantering av SQL-datanivån. Jobb låter dig säkert och tillförlitligt köra uppgifter (T-SQL-skript) oberoende av användarinteraktion eller indata, mot en grupp databaser. Den här metoden kan användas för att distribuera schema- och vanliga referensdataändringar för alla klienter i ett program. Elastiska jobb kan även användas för att underhålla en *gyllene* kopia av databasen som används för att skapa nya klienter och säkerställa att den alltid har det senaste schemat och data.

![skärmen](media/sql-database-saas-tutorial-schema-management/schema-management.png)


## <a name="elastic-jobs-limited-preview"></a>Elastiska jobb begränsad förhandsvisning

Det finns en ny version av elastiska jobb som nu är en inbyggd funktion i Azure SQL Database (som inte kräver några ytterligare tjänster eller komponenter). Den här nya versionen av elastiska jobb är för närvarande i begränsad förhandsvisning. Den här begränsade förhandsvisningen stöder för närvarande PowerShell för att skapa jobbkonton och T-SQL för att skapa och hantera jobb.

> [!NOTE]
> *Den här guiden använder funktioner för SQL Database-tjänsten som är i en begränsad förhandsgranskning (elastiska databasjobb). Om du vill följa den här guiden, måste du ange ditt prenumerations-ID till SaaSFeedback@microsoft.com med ämnet = Förhandsgranskning av elastiska jobb. När du fått en bekräftelse att din prenumeration har aktiverats kan du, [ladda ned och installera den senaste förhandsversionen av jobs-cmdletarna](https://github.com/jaredmoo/azure-powershell/releases). Eftersom det här är en begränsad förhandsgranskning, bör du kontakta SaaSFeedback@microsoft.com för relaterade frågor eller support.*

## <a name="get-the-wingtip-application-scripts"></a>Hämta Wingtip-programskripten

Wingtip biljettskripten och programmets källkod finns tillgängliga från github-repon [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Skriptfilerna finns i [mappen inlärningsmoduler](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Ladda ner mappen **inlärningsmoduler** till din lokala dator med sin mappstruktur intakt.

## <a name="create-a-job-account-database-and-new-job-account"></a>Skapa en jobbkonto-databas och ett nytt jobbkonto

Den här guiden kräver att du använder PowerShell för att skapa jobbkonto-databasen och jobbkontot. Precis som MSDB och SQL Agent så använder elastiska jobb sig av en Azure SQL-databas för att lagra jobbdefinitioner, jobbstatus och historik. När jobbkontot väl skapats, kan du skapa och övervaka jobb omedelbart.

1. Öppna …\\inlärningsmoduler\\schemahantering\\*Demo-SchemaManagement.ps1* i **PowerShell ISE**.
1. Tryck **F5** för att köra skriptet.

Skriptet *Demo-SchemaManagement.ps1* anropar skriptet *Deploy-SchemaManagement.ps1* för att skapa en *S2*-databas med namnet **jobaccount** på katalogservern. Därefter skapar den jobbkontot och skickar jobbkonto-databasen som en parameter vid anropet att skapa jobbkonto.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Skapa ett jobb för att distribuera nya referensdata till alla klienter

Varje klientdatabas inkluderar en uppsättning platstyper som definierar vilken typ av evenemang som hålls på en plats. I den här övningen, distribuerar du en uppdatering till alla klientdatabaser för att lägga till två ytterligare platstyper: *Motorcycle Racing* och *Swimming Club*. De här platstyperna motsvarar bakgrundsbilden du ser i klienternas evenemangsapp.

Klicka på listmenyn platstyp och validera att det bara finns 10 alternativ för platstyp och specifikt att ”Motorcycle Racing” och ”Swimming Club” inte finns med i listan.

Nu skapar vi ett jobb för att uppdatera *VenueTypes*-tabellen i alla klientdatabaser och lägga till de nya platstyperna.

För att skapa ett nytt jobb, använder vi oss av en uppsättning jobbsystemlagrade procedurer som skapats i jobbkonto-databasen när jobbkontot skapades.

1. Öppna SSMS och anslut till katalogservern: catalog-\<användare\>.database.windows.net server
1. Anslut också till klientservern: tenants1-\<användare\>. database.windows.net
1. Bläddra till *contosoconcerthall*-databasen på *tenants1*-servern och fråga *VenueTypes*-tabellen för att bekräfta att *Motorcycle Racing* och *Swimming Club* **inte finns** i resultatlistan.
1. Öppna filen... \\inlärningsmoduler\\schemahantering\\DeployReferenceData.sql
1. Ändra \<användare\>, använd användarnamnet som du använde när du distribuerade WTP-appen på alla 3 platser i skriptet
1. Kontrollera att du är ansluten till jobaccount-databasen och tryck **F5** för att köra skriptet

* **sp\_add\_target\_group** skapar målgruppnamnet DemoServerGroup, nu behöver vi lägga till målmedlemmar.
* **sp\_add\_target\_group\_member** lägger till en målmedlemtyp som heter *server*, som bestämmer att alla databaser inom den servern (observera att det här är servern customer1-&lt;WtpUser&gt; som innehåller klientdatabasen) ska inkluderas i jobbet vid jobbkörningstillfället. Den andra lägger till en *databas*-målmedlemstyp, specifikt den ”gyllene” databasen, baseTenantDB som finns på servern catalog-&lt;WtpUser&gt;. Slutligen läggs en till *databas*-målmedlemstyp till för att inkludera adhocanalytics-databasen som används i en senare guide.
* **sp\_add\_job** skapar ett jobb som heter referensdatadistribution
* **sp\_add\_jobstep** skapar jobbsteget med T-SQL-kommandotext för att uppdatera till referenstabellen VenueTypes
* De återstående vyerna i skriptet visar att jobbet finns och övervakar jobbkörningen. Granska statusvärdet från kolumnen **livscykel**. Jobbet har slutförts på alla klientdatabaser och de två ytterligare databaserna som innehåller referenstabellen.

1. I SSMS, bläddrar du till *contosoconcerthall*-databasen på *tenants1*-servern och frågar *VenueTypes*-tabellen för att bekräfta att *Motorcycle Racing* och *Swimming Club* **nu finns** i resultatlistan.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Skapa ett jobb för att hantera referenstabellindexet

På liknande vis som föregående övning så skapar den här ett jobb för att återskapa indexet på referentabellens primärnyckel, en vanlig åtgärd inom databashantering som en administratör kan utföra efter en stor datainläsning i en tabell.

Skapa ett jobb med samma jobbs systemlagrade procedurer.

1. Öppna SSMS och anslut till servern catalog-&lt;användare&gt;.database.windows.net
1. Öppna filen... \\inlärningsmoduler\\schemahantering\\OnlineReindex.sql
1. Högerklicka, välj anslutning och anslut till servern catalog-&lt;WtpUser&gt;.database.windows.net, om du inte redan är ansluten
1. Kontrollera att du är ansluten till jobbkonto-databasen och tryck F5 för att köra skriptet

* sp\_add\_job skapar ett nytt jobb som heter “Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885”
* sp\_add\_jobstep skapar jobbsteget som innehåller T-SQL-kommandotexten för att uppdatera indexet




## <a name="next-steps"></a>Nästa steg

I den här guiden lärde du dig hur man:

> [!div class="checklist"]

> * Skapar ett jobbkonto för att fråga över flera klienter
> * Uppdaterar data i alla klientdatabaser
> * Skapar ett index i en tabell i alla klientdatabaser

[Ad-hoc analys-guide](sql-database-saas-tutorial-adhoc-analytics.md)


## <a name="additional-resources"></a>Ytterligare resurser

* [Ytterligare guider som bygger på den initiala programdistributionen av Wingtip biljettplattformen (WTP)](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [Hantera utskalade molndatabaser](sql-database-elastic-jobs-overview.md)
* [Skapa och hantera utskalade molndatabaser](sql-database-elastic-jobs-create-and-manage.md)
