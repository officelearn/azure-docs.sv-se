---
title: "Hantera Azure SQL Database-schema i en app för flera klienter | Microsoft Docs"
description: "Hantera scheman för flera klienter i ett program för flera klienter som använder Azure SQL Database"
keywords: sql database tutorial
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2017
ms.author: billgib; sstein
ms.openlocfilehash: 14912df26074b525585594cc1b5d32c85ce9094f
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="manage-schema-for-multiple-tenants-in-a-multi-tenant-application-that-uses-azure-sql-database"></a>Hantera scheman för flera klienter i ett program för flera klienter som använder Azure SQL Database

Den [första Wingtip SaaS-självstudierna](sql-database-saas-tutorial.md) visar hur appen kan etablera en klient-databas och registrera den i katalogen. Precis som alla program Wingtip SaaS-appen kommer att utvecklas över tid och kräver ibland ändringar i databasen. Ändringarna kan inkludera nya eller ändrade scheman, nya eller ändrade referensdata och vanliga uppgifter för databasunderhåll för att säkerställa optimal apprestanda. Med ett SaaS-program, behöver de här ändringarna distribueras på ett samordnat sätt över en potentiellt massiv mängd klientdatabaser. För att ändringarna ska vara i framtiden klient databaser måste de införlivas i etableringsprocessen.

Den här guiden utforskar två scenarier. Distribuering av uppdateringar av referensdata för alla klienter och återställning av ett index i tabellen med referensdata. Den [elastiska jobb](sql-database-elastic-jobs-overview.md) funktionen används för att utföra dessa åtgärder på alla klienter och *gyllene* klient-databas som används som en mall för nya databaser.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]

> * Skapa ett konto för jobb
> * Fråga på flera innehavare
> * Uppdaterar data i alla klientdatabaser
> * Skapar ett index i en tabell i alla klientdatabaser


Se till att följande förhandskrav är slutförda för att kunna slutföra den här guiden:

* Wingtip SaaS-appen har distribuerats. För att distribuera på mindre än fem minuter finns [distribuera och utforska Wingtip SaaS-program](sql-database-saas-tutorial.md)
* Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Den senaste versionen av SQL Server Management Studio (SSMS) ska vara installerad. [Ladda ned och installera SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

*Den här guiden använder funktioner för SQL Database-tjänsten som är i en begränsad förhandsgranskning (elastiska databasjobb). Om du vill följa den här guiden, måste du ange ditt prenumerations-ID till SaaSFeedback@microsoft.com med ämnet = Förhandsgranskning av elastiska jobb. När du fått en bekräftelse att din prenumeration har aktiverats kan du, [ladda ned och installera den senaste förhandsversionen av jobs-cmdletarna](https://github.com/jaredmoo/azure-powershell/releases). Den här förhandsgranskningen är begränsad, så Kontakta SaaSFeedback@microsoft.com för frågor eller support.*


## <a name="introduction-to-saas-schema-management-patterns"></a>Introduktion till SaaS-schemahanteringsmönster

SaaS-mönstret för enskilda innehavare per databas har många fördelar av dataisoleringen som uppstår, men samtidigt introduceras den ytterligare komplexiteten med att underhålla och hantera flera databaser. [Elastiska jobb](sql-database-elastic-jobs-overview.md) underlättar administration och hantering av SQL-datanivån. Jobb låter dig säkert och tillförlitligt köra uppgifter (T-SQL-skript) oberoende av användarinteraktion eller indata, mot en grupp databaser. Den här metoden kan användas för att distribuera schema- och vanliga referensdataändringar för alla klienter i ett program. Elastiska jobb kan även användas för att underhålla en *gyllene* kopia av databasen som används för att skapa nya klienter och säkerställa att den alltid har det senaste schemat och data.

![skärmen](media/sql-database-saas-tutorial-schema-management/schema-management.png)


## <a name="elastic-jobs-limited-preview"></a>Elastiska jobb begränsad förhandsvisning

Det finns en ny version av elastiska jobb som nu är en inbyggd funktion i Azure SQL Database (som inte kräver några ytterligare tjänster eller komponenter). Den här nya versionen av elastiska jobb är för närvarande i begränsad förhandsvisning. Den här begränsade förhandsvisningen stöder för närvarande PowerShell för att skapa jobbkonton och T-SQL för att skapa och hantera jobb.

> [!NOTE]
> *Den här guiden använder funktioner för SQL Database-tjänsten som är i en begränsad förhandsgranskning (elastiska databasjobb). Om du vill följa den här guiden, måste du ange ditt prenumerations-ID till SaaSFeedback@microsoft.com med ämnet = Förhandsgranskning av elastiska jobb. När du fått en bekräftelse att din prenumeration har aktiverats kan du, [ladda ned och installera den senaste förhandsversionen av jobs-cmdletarna](https://github.com/jaredmoo/azure-powershell/releases). Den här förhandsgranskningen är begränsad, så Kontakta SaaSFeedback@microsoft.com för frågor eller support.*

## <a name="get-the-wingtip-application-scripts"></a>Hämta Wingtip-programskript

Wingtip SaaS-skript och programmets källkod är tillgängliga i den [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) github-lagringsplatsen. [Steg för att hämta Wingtip SaaS-skripten](sql-database-wtp-overview.md#download-and-unblock-the-wingtip-saas-scripts).

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
1. Ändra instruktionen: Ange @wtpUser = &lt;användaren&gt; och Ersätt det användar-värde som används när du har distribuerat appen Wingtip
1. Kontrollera att du är ansluten till jobaccount-databasen och tryck **F5** för att köra skriptet

* **sp\_add\_target\_group** skapar målgruppnamnet DemoServerGroup, nu behöver vi lägga till målmedlemmar.
* **SP\_lägga till\_mål\_grupp\_medlem** lägger till en *server* mål Medlemstyp som bedömer alla databaser på servern (Obs detta är tenants1-&lt; Användaren&gt; servern som innehåller klient-databaser) vid tiden för jobbet körningen ska inkluderas i jobbet, andra är att lägga till en *databasen* mål Medlemstyp, särskilt 'gyllene-databasen (basetenantdb) som finns i katalogen -&lt;användare&gt; server, och slutligen en annan *databasen* måltyp grupp medlem att inkludera adhocanalytics databasen som används i en senare vägledning.
* **sp\_add\_job** skapar ett jobb som heter referensdatadistribution
* **SP\_lägga till\_jobbsteg** skapar de steg som innehåller text för T-SQL-kommandot om du vill uppdatera referenstabellen VenueTypes
* De återstående vyerna i skriptet visar att jobbet finns och övervakar jobbkörningen. Använd de här frågorna för att granska statusvärde i den **livscykel** kolumnen att avgöra när jobbet har slutförts på alla klient-databaser och ytterligare två databaserna med referenstabellen.

1. I SSMS, bläddrar du till *contosoconcerthall*-databasen på *tenants1*-servern och frågar *VenueTypes*-tabellen för att bekräfta att *Motorcycle Racing* och *Swimming Club* **nu finns** i resultatlistan.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Skapa ett jobb för att hantera referenstabellindexet

På liknande vis som föregående övning så skapar den här ett jobb för att återskapa indexet på referentabellens primärnyckel, en vanlig åtgärd inom databashantering som en administratör kan utföra efter en stor datainläsning i en tabell.

Skapa ett jobb med samma jobbs systemlagrade procedurer.

1. Öppna SSMS och Anslut till katalogen -&lt;användaren&gt;. database.windows.net server
1. Öppna filen... \\inlärningsmoduler\\schemahantering\\OnlineReindex.sql
1. Högerklicka på anslutningen och välj ansluta till katalogen -&lt;användaren&gt;. database.windows.net server, om inte redan är ansluten
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

* [Ytterligare självstudier som bygger på Wingtip SaaS-programdistribution](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [Hantera utskalade molndatabaser](sql-database-elastic-jobs-overview.md)
* [Skapa och hantera utskalade molndatabaser](sql-database-elastic-jobs-create-and-manage.md)
