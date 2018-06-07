---
title: Hantera Azure SQL Database-schema i en app för flera klienter | Microsoft Docs
description: Hantera scheman för flera klienter i ett program för flera klienter som använder Azure SQL Database
keywords: sql database tutorial
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.reviewer: billgib
ms.openlocfilehash: 2e4af3e3e1ef1d9da7c66b929885e3ec749b462f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646280"
---
# <a name="manage-schema-in-a-saas-application-using-the-database-per-tenant-pattern-with-azure-sql-database"></a>Hantera schemat är i ett SaaS-program med hjälp av databasen per klient-mönstret med Azure SQL Database

Då ett databasprogram utvecklas, måste ändringar oundvikligen göras för databasdata schemat eller referens.  Uppgifter för databasunderhåll krävs också med jämna mellanrum. Hantera ett program som använder databasen per klient mönstret kräver att du tillämpar dessa ändringar eller underhållsaktiviteter över en flotta av klient-databaser.

Den här självstudiekursen utforskar två scenarier - distribution av referens datauppdateringar för alla klienter och när ett index på den tabell som innehåller referensdata. Den [elastiska jobb](sql-database-elastic-jobs-overview.md) funktionen används för att utföra dessa åtgärder på alla klient-databaser och på malldatabasen som används för att skapa ny klient databaser.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]

> * Skapa en jobbet-agent
> * Orsaka T-SQL-jobb kan köras på alla klient-databaser
> * Uppdatera referensdata i alla klient-databaser
> * Skapar ett index i en tabell i alla klientdatabaser


Se till att följande förhandskrav är slutförda för att kunna slutföra den här guiden:

* Wingtip biljetter SaaS databas Per klient appen har distribuerats. För att distribuera på mindre än fem minuter finns [distribuera och utforska Wingtip biljetter SaaS-databasen per klient program](saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell ska ha installerats. Mer information finns i [Komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Den senaste versionen av SQL Server Management Studio (SSMS) ska vara installerad. [Ladda ned och installera SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

> [!NOTE]
> Den här kursen använder funktioner för SQL Database-tjänsten som är i en begränsad förhandsgranskning (elastisk databas jobb). Om du vill göra den här självstudiekursen, ange ditt prenumerations-ID till SaaSFeedback@microsoft.com med ämnet = elastiska jobb förhandsgranskning. När du fått en bekräftelse att din prenumeration har aktiverats kan du, [ladda ned och installera den senaste förhandsversionen av jobs-cmdletarna](https://github.com/jaredmoo/azure-powershell/releases). Den här förhandsgranskningen är begränsad, så Kontakta SaaSFeedback@microsoft.com för frågor eller support.

## <a name="introduction-to-saas-schema-management-patterns"></a>Introduktion till SaaS-schemat management mönster

Databasen per klient mönster isolerar klientdata effektivt, men ökar antalet databaser för att hantera och underhålla. [Elastiska jobb](sql-database-elastic-jobs-overview.md) underlättar administrationen och hantering av SQL-databaser. Jobb kan du säkert och tillförlitligt, köra uppgifter (T-SQL-skript) mot en grupp databaser. Jobb kan distribuera schema och gemensamma data ändras i alla klient-databaser i ett program. Elastiska jobb kan också användas för att underhålla en *mallen* databas som används för att skapa nya klienter, att säkerställa alltid har den senaste informationen för schemat och referens.

![skärmen](media/saas-tenancy-schema-management/schema-management-dpt.png)


## <a name="elastic-jobs-limited-preview"></a>Elastiska jobb begränsad förhandsvisning

Det finns en ny version av elastiska jobb som nu är en integrerad funktion i Azure SQL Database. Den här nya versionen av elastiska jobb är för närvarande i begränsad förhandsvisning. Den här begränsade preview stöder för närvarande använder PowerShell för att skapa en agent för jobbet och T-SQL för att skapa och hantera jobb.

> [!NOTE]
> Den här kursen använder funktioner för SQL Database-tjänsten som är i en begränsad förhandsgranskning (elastisk databas jobb). Om du vill göra den här självstudiekursen, ange ditt prenumerations-ID till SaaSFeedback@microsoft.com med ämnet = elastiska jobb förhandsgranskning. När du fått en bekräftelse att din prenumeration har aktiverats kan du, [ladda ned och installera den senaste förhandsversionen av jobs-cmdletarna](https://github.com/jaredmoo/azure-powershell/releases). Den här förhandsgranskningen är begränsad, så Kontakta SaaSFeedback@microsoft.com för frågor eller support.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Hämta Wingtip biljetter SaaS-databasen per klient programskript

Programmet källa kod och hantering av skript är tillgängliga i den [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-lagringsplatsen. Kolla in den [allmänna riktlinjer](saas-tenancy-wingtip-app-guidance-tips.md) steg för att ladda ned och avblockera Wingtip biljetter SaaS-skript.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Skapa en jobbet agent databas och nya jobb agent

Den här kursen kräver att du använder PowerShell för att skapa en jobbet agent och dess stödjande jobbet agent databas. Jobbet agent databasen innehåller jobbdefinitioner, jobbstatus och tidigare. När jobbet agenten och dess databas har skapats kan du skapa och övervaka jobb omedelbart.

1. **I PowerShell ISE**öppnar... \\Learning moduler\\schemat Management\\*Demo-SchemaManagement.ps1*.
1. Tryck **F5** för att köra skriptet.

Den *Demo-SchemaManagement.ps1* skript anrop av *distribuera SchemaManagement.ps1* skript för att skapa en SQL-databas med namnet *osagent* på katalogservern. Sedan skapar den agenten jobb med hjälp av databasen som en parameter.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Skapa ett jobb för att distribuera nya referensdata till alla klienter

Varje klient-databasen innehåller en uppsättning stöds platsen typer i appen Wingtip biljetter. Varje plats är av typen lokal som definierar typ av händelser som kan finnas och anger bakgrundsbilden som används i appen. Den här referensdata måste vara uppdaterade och nya plats-typer som lagts till för programmet att stödja nya typer av händelser.  I den här övningen, distribuerar du en uppdatering till alla klientdatabaser för att lägga till två ytterligare platstyper: *Motorcycle Racing* och *Swimming Club*.

Först granska plats-typer som ingår i varje klient-databas. Anslut till en klient-databaser i SQL Server Management Studio (SSMS) och inspektera VenueTypes tabellen.  Du kan också fråga i den här tabellen i frågeredigeraren i Azure-portalen nås via databas. 

1. Öppna SSMS och ansluta till servern för klient: *tenants1-dpt -&lt;användaren&gt;. database.windows.net*
1. Kontrollera att *motorcykel tävling* och *simning en* **inte** för närvarande ingår, bläddra till den _contosoconcerthall_ databasen på den *tenants1-dpt -&lt;användare&gt;*  server och fråga den *VenueTypes* tabell.

Nu skapar vi ett jobb för att uppdatera den *VenueTypes* tabellen i alla klient-databaser att lägga till de nya platsen-typerna.

Om du vill skapa ett nytt jobb som du använder en uppsättning jobb system lagrade procedurer som skapats i den _jobagent_ databasen när agenten jobbet skapades.

1. Anslut till katalogservern i SSMS,: *katalog-dpt -&lt;användare&gt;. database.windows.net* server 
1. Öppna filen i SSMS... \\Learning moduler\\schemat Management\\DeployReferenceData.sql
1. Ändra instruktionen: Ange @wtpUser = &lt;användaren&gt; och Ersätt det värde för användaren som används när du har distribuerat appen Wingtip biljetter SaaS databas Per klient
1. Se till att du är ansluten till den _jobagent_ databasen och tryck på **F5** att köra skriptet

Observera följande element i den *DeployReferenceData.sql* skript:
* **SP\_lägga till\_mål\_grupp** skapar namnet på målgrupp DemoServerGroup.
* **SP\_lägga till\_mål\_grupp\_medlem** används för att definiera en uppsättning måldatabaserna.  Första den _tenants1-dpt -&lt;användare&gt;_  server har lagts till.  Att lägga till servern som mål medför databaserna i servern vid tidpunkten för jobbkörningen ska inkluderas i jobbet. Sedan _basetenantdb_ databasen och *adhocreporting* databasen (används i en senare kursen) läggs till som mål.
* **SP\_lägga till\_jobbet** skapas ett jobb med namnet _referens Data distribution_.
* **SP\_lägga till\_jobbsteg** skapar de steg som innehåller text för T-SQL-kommandot om du vill uppdatera referenstabellen VenueTypes.
* De återstående vyerna i skriptet visar att jobbet finns och övervakar jobbkörningen. Använd de här frågorna för att granska statusvärde i den **livscykel** kolumnen att avgöra när jobbet har slutförts på alla måldatabaserna.

När skriptet har slutförts kan du kontrollera referensdata har uppdaterats.  SSMS, bläddra till den *contosoconcerthall* databasen på den *tenants1-dpt -&lt;användare&gt;*  server och fråga den *VenueTypes* tabell.  Kontrollera att *motorcykel tävling* och *simning en* **är** nu finns.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Skapa ett jobb för att hantera referenstabellindexet

Den här övningen använder ett jobb för att återskapa indexet för referens tabellens primärnyckel.  Det här är en typisk Underhåll databasåtgärd som kan göras efter inläsning av stora mängder data.

Skapa ett jobb med samma jobbs systemlagrade procedurer.

1. Öppna SSMS och Anslut till den _katalog-dpt -&lt;användare&gt;. database.windows.net_ server
1. Öppna filen _... \\Learning moduler\\schemat Management\\OnlineReindex.sql_
1. Högerklicka, Välj anslutning och ansluta till den _katalog-dpt -&lt;användare&gt;. database.windows.net_ server, om inte redan är ansluten
1. Se till att du är ansluten till den _jobagent_ databasen och tryck på **F5** att köra skriptet

Observera följande element i den _OnlineReindex.sql_ skript:
* **SP\_lägga till\_jobbet** skapar ett nytt jobb som kallas ”Online omindexera PK\_\_VenueTyp\_\_265E44FD7FD4C885”
* **SP\_lägga till\_jobbsteg** skapar de steg som innehåller text för T-SQL-kommando för att uppdatera indexet
* De återstående vyerna i skriptet övervaka jobbkörning av. Använd de här frågorna för att granska statusvärde i den **livscykel** kolumnen att avgöra när jobbet har slutförts på alla mål-gruppmedlemmar.



## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]

> * Skapa en agent för jobbet om du vill köra flera databaser i T-SQL-jobb
> * Uppdatera referensdata i alla klient-databaser
> * Skapar ett index i en tabell i alla klientdatabaser

Försök med den [Ad hoc-rapportering kursen](saas-tenancy-cross-tenant-reporting.md) att utforska köra distribuerade frågor via klient databaser.


## <a name="additional-resources"></a>Ytterligare resurser

* [Ytterligare självstudier som bygger på programdistribution Wingtip biljetter SaaS databas Per klient](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Hantera utskalade molndatabaser](sql-database-elastic-jobs-overview.md)
* [Skapa och hantera utskalade molndatabaser](sql-database-elastic-jobs-create-and-manage.md)