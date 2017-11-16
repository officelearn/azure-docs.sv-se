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
ms.date: 11/14/2017
ms.author: billgib
ms.openlocfilehash: 346177be29ec196464f4f441858222ac5d5eb8c3
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="manage-schema-for-multiple-tenants-in-a-multi-tenant-application-that-uses-azure-sql-database"></a>Hantera schemat för flera klienter i ett program för flera innehavare som använder Azure SQL Database

Den [första Wingtip biljetter SaaS flera innehavare Database-självstudier](saas-multitenantdb-get-started-deploy.md) visar hur appen kan etablera ett delat flera innehavare databasen och registrera den i katalogen. Precis som alla program Wingtip biljetter SaaS-appen kommer att utvecklas över tid och kräver ibland ändringar i databasen. Ändringarna kan inkludera nya eller ändrade scheman, nya eller ändrade referensdata och vanliga uppgifter för databasunderhåll för att säkerställa optimal apprestanda. Med ett SaaS-program, behöver de här ändringarna distribueras på ett samordnat sätt över en potentiellt massiv mängd klientdatabaser. För att ändringarna ska vara i framtiden klient databaser måste de införlivas i etableringsprocessen.

Den här guiden utforskar två scenarier. Distribuering av uppdateringar av referensdata för alla klienter och återställning av ett index i tabellen med referensdata. Den [elastiska jobb](sql-database-elastic-jobs-overview.md) funktionen används för att utföra dessa åtgärder mellan klient-databaser och *gyllene* klient-databas som används som en mall för nya databaser.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]

> * Skapa ett konto för jobb
> * Fråga på flera innehavare
> * Uppdaterar data i alla klientdatabaser
> * Skapar ett index i en tabell i alla klientdatabaser


Se till att följande förhandskrav är slutförda för att kunna slutföra den här guiden:

* Databasprogram Wingtip biljetter SaaS flera innehavare distribueras. För att distribuera på mindre än fem minuter finns [distribuera och utforska databasprogram Wingtip biljetter SaaS flera innehavare](saas-multitenantdb-get-started-deploy.md)
* Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Den senaste versionen av SQL Server Management Studio (SSMS) ska vara installerad. [Ladda ned och installera SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

> [!NOTE]
> *Den här guiden använder funktioner för SQL Database-tjänsten som är i en begränsad förhandsgranskning (elastiska databasjobb). Om du vill följa den här guiden, måste du ange ditt prenumerations-ID till SaaSFeedback@microsoft.com med ämnet = Förhandsgranskning av elastiska jobb. När du fått en bekräftelse att din prenumeration har aktiverats kan du, [ladda ned och installera den senaste förhandsversionen av jobs-cmdletarna](https://github.com/jaredmoo/azure-powershell/releases). Den här förhandsgranskningen är begränsad, så Kontakta SaaSFeedback@microsoft.com för frågor eller support.*


## <a name="introduction-to-saas-schema-management-patterns"></a>Introduktion till SaaS-schemahanteringsmönster

Delat flera innehavare databasmodellen används i det här exemplet gör det möjligt för en databas som klienter ska innehålla valfritt antal klienter. Det här exemplet utforskar möjligt att använda en blandning av flera innehavare och stöd för en innehavare databaser, aktivera en 'hybrid-klient-Hanteringsmodellen. Underhåll och hantering av databaserna är komplex. [Elastiska jobb](sql-database-elastic-jobs-overview.md) underlättar administration och hantering av SQL-datanivån. Jobb låter dig säkert och tillförlitligt köra uppgifter (T-SQL-skript) oberoende av användarinteraktion eller indata, mot en grupp databaser. Den här metoden kan användas för att distribuera schema- och vanliga referensdataändringar för alla klienter i ett program. Elastiska jobb kan även användas för att underhålla en *gyllene* kopia av databasen som används för att skapa nya klienter och säkerställa att den alltid har det senaste schemat och data.

![skärmen](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Elastiska jobb begränsad förhandsvisning

Det finns en ny version av elastiska jobb som nu är en inbyggd funktion i Azure SQL Database (som inte kräver några ytterligare tjänster eller komponenter). Den här nya versionen av elastiska jobb är för närvarande i begränsad förhandsvisning. Den här begränsade förhandsvisningen stöder för närvarande PowerShell för att skapa jobbkonton och T-SQL för att skapa och hantera jobb.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-scripts"></a>Hämta programskript Wingtip biljetter SaaS flera innehavare databas

Wingtip biljetter SaaS flera innehavare databasen skript och programmets källkod är tillgängliga i den [WingtipTicketsSaaS MultiTenantDB](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub-lagringsplatsen. <!-- [Steps to download the Wingtip Tickets SaaS Multi-tenant Database scripts](saas-multitenantdb-wingtip-app-guidance-tips.md#download-and-unblock-the-wingtip-saas-scripts)-->

## <a name="create-a-job-account-database-and-new-job-account"></a>Skapa en jobbkonto-databas och ett nytt jobbkonto

Den här guiden kräver att du använder PowerShell för att skapa jobbkonto-databasen och jobbkontot. Precis som MSDB och SQL Agent så använder elastiska jobb sig av en Azure SQL-databas för att lagra jobbdefinitioner, jobbstatus och historik. När jobbkontot väl skapats, kan du skapa och övervaka jobb omedelbart.

1. I **PowerShell ISE**öppnar *... \\Learning moduler\\schemat Management\\Demo-SchemaManagement.ps1*.
1. Tryck **F5** för att köra skriptet.

Skriptet *Demo-SchemaManagement.ps1* anropar skriptet *Deploy-SchemaManagement.ps1* för att skapa en *S2*-databas med namnet **jobaccount** på katalogservern. Därefter skapar den jobbkontot och skickar jobbkonto-databasen som en parameter vid anropet att skapa jobbkonto.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Skapa ett jobb för att distribuera nya referensdata till alla klienter

Varje innehavare databasen innehåller en uppsättning platsen typer i tabellen **VenueTypes** som definierar typ av händelser som finns på en plats. I den här övningen du distribuerar en uppdatering för alla databaser att lägga till två ytterligare plats typer: *motorcykel tävling* och *simning en*. De här platstyperna motsvarar bakgrundsbilden du ser i klienternas evenemangsapp.

Klicka på listmenyn platstyp och validera att det bara finns 10 alternativ för platstyp och specifikt att ”Motorcycle Racing” och ”Swimming Club” inte finns med i listan.

Nu skapar vi ett jobb för att uppdatera den **VenueTypes** tabellen i alla klienter databaserna och lägga till de nya platsen-typerna.

För att skapa ett nytt jobb, använder vi oss av en uppsättning jobbsystemlagrade procedurer som skapats i jobbkonto-databasen när jobbkontot skapades.

1. Anslut till klient-servern i SSMS,: tenants1-huvudmålservern -\<användaren\>. database.windows.net
2. Bläddra till den *tenants1* databasen på den *tenants1-huvudmålservern -\<användare\>. database.windows.net* server och fråga den *VenueTypes* tabellen Bekräfta att *motorcykel tävling* och *simning en* är **inte** i resultatlistan.
3. Ansluta till katalogservern: katalog-huvudmålservern -\<användaren\>. database.windows.net
4. Ansluta till databasen jobaccount i katalog-server.
5. Öppna filen i SSMS... \\Learning moduler\\schemat Management\\DeployReferenceData.sql
6. Ändra instruktionen: Ange @User = &lt;användaren&gt; och Ersätt det användar-värde som används när du distribuerade databasprogram Wingtip biljetter SaaS flera innehavare.
7. Tryck **F5** för att köra skriptet.

    * **SP\_lägga till\_mål\_grupp** skapar målgruppen namn DemoServerGroup, nu lägga till målet medlemmar i gruppen.
    * **SP\_lägga till\_mål\_grupp\_medlem** lägger till en *server* mål Medlemstyp som bedömer alla databaser på servern (Observera att detta är tenants1 - huvudmålservern - &lt;användare&gt; servern som innehåller klienter databasen) vid tidpunkten för jobbkörningen ska inkluderas i jobbet, en *databasen* mål Medlemstyp för ”gyllene'-databasen (basetenantdb) som finns på katalog-huvudmålservern -&lt;användare&gt; server, och slutligen en *databasen* måltyp medlem att inkludera adhocreporting databasen som används i en senare vägledning.
    * **SP\_lägga till\_jobbet** skapas ett jobb som kallas ”referens Data distribution”.
    * **SP\_lägga till\_jobbsteg** skapar de steg som innehåller text för T-SQL-kommandot om du vill uppdatera referenstabellen VenueTypes.
    * De återstående vyerna i skriptet visar att jobbet finns och övervakar jobbkörningen. Använd de här frågorna för att granska statusvärde i den **livscykel** kolumnen att avgöra när jobbet har slutförts på databasen för klienter och de två ytterligare databaser som innehåller referenstabellen.

1. SSMS, bläddra till klient-databasen på den *tenants1-huvudmålservern -&lt;användare&gt;*  server och fråga den *VenueTypes* tabell för att bekräfta att *motorcykel tävling* och *simning en* är nu **läggs* i tabellen.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Skapa ett jobb för att hantera referenstabellindexet

På liknande vis som föregående övning så skapar den här ett jobb för att återskapa indexet på referentabellens primärnyckel, en vanlig åtgärd inom databashantering som en administratör kan utföra efter en stor datainläsning i en tabell.


1. Anslut till jobaccount databas i katalogen i SSMS,-huvudmålservern -&lt;användaren&gt;. database.windows.net server.
2. Öppna i SSMS... \\Learning moduler\\schemat Management\\OnlineReindex.sql.
3. Tryck på **F5** att köra skriptet

    * **SP\_lägga till\_jobbet** skapar ett nytt jobb som kallas ”Online omindexera PK\_\_VenueTyp\_\_265E44FD7FD4C885”.
    * **SP\_lägga till\_jobbsteg** skapar de steg som innehåller text för T-SQL-kommando för att uppdatera indexet.
    * De återstående vyerna i skriptet övervaka jobbkörning av. Använd de här frågorna för att granska statusvärde i den **livscykel** kolumnen att avgöra när jobbet har slutförts på på alla mål-gruppmedlemmar.

## <a name="next-steps"></a>Nästa steg

I den här guiden lärde du dig hur man:

> [!div class="checklist"]

> * Skapar ett jobbkonto för att fråga över flera klienter
> * Uppdaterar data i alla klientdatabaser
> * Skapar ett index i en tabell i alla klientdatabaser

[Ad-hoc analys-guide](saas-multitenantdb-adhoc-reporting.md)


## <a name="additional-resources"></a>Ytterligare resurser

<!--* Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Hantera utskalade molndatabaser](sql-database-elastic-jobs-overview.md)
* [Skapa och hantera utskalade molndatabaser](sql-database-elastic-jobs-create-and-manage.md)
