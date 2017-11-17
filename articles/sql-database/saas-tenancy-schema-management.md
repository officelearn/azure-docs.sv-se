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
ms.openlocfilehash: 701a7296368cd8150eedf8cc50b989fdf6112101
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2017
---
# <a name="manage-schema-for-multiple-tenants-in-a-multi-tenant-application-that-uses-azure-sql-database"></a>Hantera scheman för flera klienter i ett program för flera klienter som använder Azure SQL Database

Den [första Wingtip biljetter SaaS databas Per klient självstudierna](saas-dbpertenant-get-started-deploy.md) visar hur appen kan etablera en klient-databas och registrera den i katalogen. Precis som alla program Wingtip biljetter SaaS databas Per klient appen kommer att utvecklas över tid och kräver ibland ändringar i databasen. Ändringarna kan inkludera nya eller ändrade scheman, nya eller ändrade referensdata och vanliga uppgifter för databasunderhåll för att säkerställa optimal apprestanda. Med ett SaaS-program, behöver de här ändringarna distribueras på ett samordnat sätt över en potentiellt massiv mängd klientdatabaser. För att ändringarna ska vara i framtiden klient databaser måste de införlivas i etableringsprocessen.

Den här guiden utforskar två scenarier. Distribuering av uppdateringar av referensdata för alla klienter och återställning av ett index i tabellen med referensdata. Den [elastiska jobb](sql-database-elastic-jobs-overview.md) funktionen används för att utföra dessa åtgärder på alla klienter och *gyllene* klient-databas som används som en mall för nya databaser.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]

> * Skapa ett konto för jobb
> * Fråga på flera innehavare
> * Uppdaterar data i alla klientdatabaser
> * Skapar ett index i en tabell i alla klientdatabaser


Se till att följande förhandskrav är slutförda för att kunna slutföra den här guiden:

* Wingtip biljetter SaaS databas Per klient appen har distribuerats. För att distribuera på mindre än fem minuter finns [distribuera och utforska programmet Wingtip biljetter SaaS databas Per klient](saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Den senaste versionen av SQL Server Management Studio (SSMS) ska vara installerad. [Ladda ned och installera SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

*Den här guiden använder funktioner för SQL Database-tjänsten som är i en begränsad förhandsgranskning (elastiska databasjobb). Om du vill följa den här guiden, måste du ange ditt prenumerations-ID till SaaSFeedback@microsoft.com med ämnet = Förhandsgranskning av elastiska jobb. När du fått en bekräftelse att din prenumeration har aktiverats kan du, [ladda ned och installera den senaste förhandsversionen av jobs-cmdletarna](https://github.com/jaredmoo/azure-powershell/releases). Den här förhandsgranskningen är begränsad, så Kontakta SaaSFeedback@microsoft.com för frågor eller support.*


## <a name="introduction-to-saas-schema-management-patterns"></a>Introduktion till SaaS-schemahanteringsmönster

SaaS-mönstret för enskilda innehavare per databas har många fördelar av dataisoleringen som uppstår, men samtidigt introduceras den ytterligare komplexiteten med att underhålla och hantera flera databaser. [Elastiska jobb](sql-database-elastic-jobs-overview.md) underlättar administration och hantering av SQL-datanivån. Jobb låter dig säkert och tillförlitligt köra uppgifter (T-SQL-skript) oberoende av användarinteraktion eller indata, mot en grupp databaser. Den här metoden kan användas för att distribuera schema- och vanliga referensdataändringar för alla klienter i ett program. Elastiska jobb kan även användas för att underhålla en *gyllene* kopia av databasen som används för att skapa nya klienter och säkerställa att den alltid har det senaste schemat och data.

![skärmen](media/saas-tenancy-schema-management/schema-management.png)


## <a name="elastic-jobs-limited-preview"></a>Elastiska jobb begränsad förhandsvisning

Det finns en ny version av elastiska jobb som nu är en inbyggd funktion i Azure SQL Database (som inte kräver några ytterligare tjänster eller komponenter). Den här nya versionen av elastiska jobb är för närvarande i begränsad förhandsvisning. Den här begränsade förhandsvisningen stöder för närvarande PowerShell för att skapa jobbkonton och T-SQL för att skapa och hantera jobb.

> [!NOTE]
> *Den här guiden använder funktioner för SQL Database-tjänsten som är i en begränsad förhandsgranskning (elastiska databasjobb). Om du vill följa den här guiden, måste du ange ditt prenumerations-ID till SaaSFeedback@microsoft.com med ämnet = Förhandsgranskning av elastiska jobb. När du fått en bekräftelse att din prenumeration har aktiverats kan du, [ladda ned och installera den senaste förhandsversionen av jobs-cmdletarna](https://github.com/jaredmoo/azure-powershell/releases). Den här förhandsgranskningen är begränsad, så Kontakta SaaSFeedback@microsoft.com för frågor eller support.*

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Hämta programskript Wingtip biljetter SaaS databas Per klient

Wingtip biljetter SaaS databas Per klient skript och programmets källkod är tillgängliga i den [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) github-lagringsplatsen. [Steg för att hämta Wingtip biljetter SaaS databas Per klient skripten](saas-dbpertenant-wingtip-app-guidance-tips.md#download-and-unblock-the-wingtip-tickets-saas-database-per-tenant-scripts).

## <a name="create-a-job-account-database-and-new-job-account"></a>Skapa en jobbkonto-databas och ett nytt jobbkonto

Den här guiden kräver att du använder PowerShell för att skapa jobbkonto-databasen och jobbkontot. Precis som MSDB och SQL Agent så använder elastiska jobb sig av en Azure SQL-databas för att lagra jobbdefinitioner, jobbstatus och historik. När jobbkontot väl skapats, kan du skapa och övervaka jobb omedelbart.

1. **I PowerShell ISE**öppnar... \\Learning moduler\\schemat Management\\*Demo-SchemaManagement.ps1*.
1. Tryck **F5** för att köra skriptet.

Skriptet *Demo-SchemaManagement.ps1* anropar skriptet *Deploy-SchemaManagement.ps1* för att skapa en *S2*-databas med namnet **jobaccount** på katalogservern. Därefter skapar den jobbkontot och skickar jobbkonto-databasen som en parameter vid anropet att skapa jobbkonto.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Skapa ett jobb för att distribuera nya referensdata till alla klienter

Varje klientdatabas inkluderar en uppsättning platstyper som definierar vilken typ av evenemang som hålls på en plats. I den här övningen, distribuerar du en uppdatering till alla klientdatabaser för att lägga till två ytterligare platstyper: *Motorcycle Racing* och *Swimming Club*. De här platstyperna motsvarar bakgrundsbilden du ser i klienternas evenemangsapp.

Klicka på listmenyn platstyp och validera att det bara finns 10 alternativ för platstyp och specifikt att ”Motorcycle Racing” och ”Swimming Club” inte finns med i listan.

Nu skapar vi ett jobb för att uppdatera *VenueTypes*-tabellen i alla klientdatabaser och lägga till de nya platstyperna.

För att skapa ett nytt jobb, använder vi oss av en uppsättning jobbsystemlagrade procedurer som skapats i jobbkonto-databasen när jobbkontot skapades.

1. Öppna SSMS och Anslut till katalogservern: katalog-dpt -\<användaren\>. database.windows.net server
1. Också ansluta till servern för klient: tenants1-dpt -\<användaren\>. database.windows.net
1. Bläddra till den *contosoconcerthall* databasen på den *tenants1-dpt -\<användare\>*  server och fråga den *VenueTypes* tabell för att bekräfta att *Motorcykel tävling* och *simning en* **inte** i resultatlistan.
1. Öppna filen i SSMS... \\Learning moduler\\schemat Management\\DeployReferenceData.sql
1. Ändra instruktionen: Ange @wtpUser = &lt;användaren&gt; och Ersätt det värde för användaren som används när du har distribuerat appen Wingtip biljetter SaaS databas Per klient
1. Kontrollera att du är ansluten till jobaccount-databasen och tryck **F5** för att köra skriptet

* **sp\_add\_target\_group** skapar målgruppnamnet DemoServerGroup, nu behöver vi lägga till målmedlemmar.
* **SP\_lägga till\_mål\_grupp\_medlem** lägger till en *server* mål Medlemstyp som bedömer alla databaser på servern (Observera att detta är tenants1 - dpt - &lt;Användare&gt; servern som innehåller klient-databaser) vid tiden för jobbet körningen ska inkluderas i jobbet, andra är att lägga till en *databasen* mål Medlemstyp, mer specifikt ”guld” databas (basetenantdb) som finns på katalogen-dpt -&lt;användare&gt; server, och slutligen en annan *databasen* måltyp grupp medlem att inkludera adhocanalytics databasen som används i en senare kursen.
* **sp\_add\_job** skapar ett jobb som heter referensdatadistribution
* **SP\_lägga till\_jobbsteg** skapar de steg som innehåller text för T-SQL-kommandot om du vill uppdatera referenstabellen VenueTypes
* De återstående vyerna i skriptet visar att jobbet finns och övervakar jobbkörningen. Använd de här frågorna för att granska statusvärde i den **livscykel** kolumnen att avgöra när jobbet har slutförts på alla klient-databaser och ytterligare två databaserna med referenstabellen.

1. SSMS, bläddra till den *contosoconcerthall* databasen på den *tenants1-dpt -\<användare\>*  server och fråga den *VenueTypes* tabellen Bekräfta att *motorcykel tävling* och *simning en* **är** nu i resultatlistan.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Skapa ett jobb för att hantera referenstabellindexet

På liknande vis som föregående övning så skapar den här ett jobb för att återskapa indexet på referentabellens primärnyckel, en vanlig åtgärd inom databashantering som en administratör kan utföra efter en stor datainläsning i en tabell.

Skapa ett jobb med samma jobbs systemlagrade procedurer.

1. Öppna SSMS och Anslut till katalogen-dpt -&lt;användaren&gt;. database.windows.net server
1. Öppna filen... \\inlärningsmoduler\\schemahantering\\OnlineReindex.sql
1. Högerklicka, Välj anslutning och ansluta till katalogen-dpt -&lt;användaren&gt;. database.windows.net server, om inte redan är ansluten
1. Kontrollera att du är ansluten till jobbkonto-databasen och tryck F5 för att köra skriptet

* sp\_add\_job skapar ett nytt jobb som heter “Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885”
* sp\_add\_jobstep skapar jobbsteget som innehåller T-SQL-kommandotexten för att uppdatera indexet




## <a name="next-steps"></a>Nästa steg

I den här guiden lärde du dig hur man:

> [!div class="checklist"]

> * Skapar ett jobbkonto för att fråga över flera klienter
> * Uppdaterar data i alla klientdatabaser
> * Skapar ett index i en tabell i alla klientdatabaser

[Ad-hoc analys-guide](saas-tenancy-adhoc-analytics.md)


## <a name="additional-resources"></a>Ytterligare resurser

* [Ytterligare självstudier som bygger på programdistribution Wingtip biljetter SaaS databas Per klient](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Hantera utskalade molndatabaser](sql-database-elastic-jobs-overview.md)
* [Skapa och hantera utskalade molndatabaser](sql-database-elastic-jobs-create-and-manage.md)