---
title: Hantera Azure SQL Database-schema i en app för flera klienter | Microsoft Docs
description: Hantera scheman för flera klienter i ett program för flera klienter som använder Azure SQL Database
keywords: sql database tutorial
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 01/03/2018
ms.reviewers: billgib
ms.author: genemi
ms.openlocfilehash: 474b7842c8295be0dd30c2c03b92dc68ce40630d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2018
---
# <a name="manage-schema-in-a-saas-application-that-uses-sharded-multi-tenant-sql-databases"></a>Hantera schemat är i ett SaaS-program som använder delat SQL-databaser för flera innehavare

Den här självstudiekursen undersöker utmaningarna med att underhålla en flotta med databaser i en programvara som en tjänst (SaaS). Lösningar visas för fläkt ut schemaändringar över flottan av databaser.

Precis som alla program Wingtip biljetter SaaS-appen kommer att utvecklas över tid och kräver ändringar i databasen. Ändringar kan påverka schemat eller referens data eller tillämpa databasens underhållsuppgifter. Med en SaaS-program med hjälp av en databas per klient mönster vara ändringar koordineras via en potentiellt massiv flotta av klient-databaser. Dessutom måste du inkludera dessa ändringar till databasen etableringsprocessen för att säkerställa att de inkluderas i nya databaser som har skapats.

#### <a name="two-scenarios"></a>Två scenarier

Den här självstudiekursen utforskar följande två scenarier:
- Distribuera referens datauppdateringar för alla klienter.
- Bygga om index på den tabell som innehåller referensdata.

Den [elastiska jobb](sql-database-elastic-jobs-overview.md) funktion i Azure SQL-databas som används för att utför dessa åtgärder i klient-databaser. Jobben fungerar även med 'mall-klient-databasen. I exempelapp Wingtip biljetter kopieras malldatabasen för att etablera en ny klient-databas.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en agent för jobbet.
> * Köra ett T-SQL-fråga på flera innehavare databaser.
> * Uppdatera referensdata i alla klient-databaser.
> * Skapa ett index i en tabell i alla klient-databaser.

## <a name="prerequisites"></a>Förutsättningar

- Appen Wingtip biljetter flera innehavare databasen måste redan distribueras:
    - Instruktioner finns i första självstudierna, vilket ger Wingtip biljetter SaaS flera innehavare databasen appen:<br />[Distribuera och utforska ett delat flera innehavare program som använder Azure SQL Database](saas-multitenantdb-get-started-deploy.md).
        - Distribuera processen körs för mindre än fem minuter.
    - Du måste ha den *delat flera innehavare* version av Wingtip installerad. Versioner för *fristående* och *databas per klient* stöder inte den här kursen.

- Den senaste versionen av SQL Server Management Studio (SSMS) måste installeras. [Hämta och installera SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

- Azure PowerShell måste vara installerat. Mer information finns i [komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

> [!NOTE]
> Den här kursen använder funktioner för Azure SQL Database-tjänsten som är i en begränsad förhandsgranskning ([elastisk databas jobb](sql-database-elastic-database-client-library.md)). Om du vill göra den här självstudiekursen, ange ditt prenumerations-ID till *SaaSFeedback@microsoft.com* = elastiska jobb Preview med ämnet. När du fått en bekräftelse att din prenumeration har aktiverats kan du, [ladda ned och installera den senaste förhandsversionen av jobs-cmdletarna](https://github.com/jaredmoo/azure-powershell/releases). Den här förhandsgranskningen är begränsad, så Kontakta *SaaSFeedback@microsoft.com* för frågor eller support.

## <a name="introduction-to-saas-schema-management-patterns"></a>Introduktion till SaaS-schemat management mönster

Delat flera innehavare databasmodellen används i det här exemplet aktiverar en innehavare databas som innehåller en eller flera innehavare. Det här exemplet utforskar möjligt att använda en blandning av en många-klient och en klient databaser aktiverar en *hybrid* modell för hantering av klienter. Kan vara komplicerat att hantera ändringar i dessa databaser. [Elastiska jobb](sql-database-elastic-jobs-overview.md) underlättar administrationen och hanteringen av stora mängder databasen. Jobb kan du säkert och tillförlitligt sätt köra Transact-SQL-skript som aktiviteter mot en grupp av klient-databaser. Uppgifter som är oberoende av användarinteraktion eller indata. Den här metoden kan användas för att distribuera ändringarna till schemat eller vanliga referensdata över alla klienter i ett program. Elastiska jobb kan också användas för att underhålla en gyllene mallen kopia av databasen. Mallen används för att skapa nya klienter, se alltid till senaste schemat och referensdata som används.

![skärmen](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Elastiska jobb begränsad förhandsvisning

Det finns en ny version av elastiska jobb som nu är en integrerad funktion i Azure SQL Database. Den här nya versionen av elastiska jobb är för närvarande i begränsad förhandsvisning. Begränsat Förhandsgranska för närvarande stöder som använder PowerShell för att skapa en agent för jobbet och T-SQL för att skapa och hantera jobb.
> [!NOTE] 
> Den här kursen använder funktioner för SQL Database-tjänsten som är i en begränsad förhandsgranskning (elastisk databas jobb). Om du vill göra den här självstudiekursen, ange ditt prenumerations-ID till SaaSFeedback@microsoft.com med ämnet = elastiska jobb förhandsgranskning. När ett meddelande bekräftar att din prenumeration har aktiverats, hämta och installera den senaste förhandsversionen jobb cmdletar. Den här förhandsgranskningen är begränsad, så Kontakta SaaSFeedback@microsoft.com för frågor eller support.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Hämta Wingtip biljetter SaaS flera innehavare databasen programmets källkod och skript

Wingtip biljetter SaaS flera innehavare databasen skript och programmets källkod är tillgängliga i den [WingtipTicketsSaaS MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) databasen på Github. Finns det [allmänna riktlinjer](saas-tenancy-wingtip-app-guidance-tips.md) steg för att ladda ned och avblockera Wingtip biljetter SaaS-skript. 

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Skapa en jobbet agent databas och nya jobb agent

Den här kursen kräver att du använder PowerShell för att skapa jobb agent-databas och jobbet agent. Som MSDB-databas som används av SQL Agent, använder en agent för jobbet en Azure SQL database för att lagra jobbdefinitioner, jobbstatus och tidigare. När agenten jobbet har skapats kan du skapa och övervaka jobb omedelbart.

1. I **PowerShell ISE**öppnar *... \\Learning moduler\\schemat Management\\Demo-SchemaManagement.ps1*.
2. Tryck **F5** för att köra skriptet.

Den *Demo-SchemaManagement.ps1* skript anrop av *distribuera SchemaManagement.ps1* skript för att skapa en databas med namnet _jobagent_ på katalogservern. Skriptet skapar sedan jobbet agenten skickar den _jobagent_ databasen som en parameter.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Skapa ett jobb för att distribuera nya referensdata till alla klienter

#### <a name="prepare"></a>Förbereda

Varje klient databasen innehåller en uppsättning platsen typer i den **VenueTypes** tabell. Varje typ av plats definierar vilken typ av händelser som kan finnas på en plats. Dessa typer av platsen motsvarar bakgrund avbildningar som visas i appen klient händelser.  I den här övningen du distribuerar en uppdatering för alla databaser att lägga till två ytterligare plats typer: *motorcykel tävling* och *simning en*. 

Först granska plats-typer som ingår i varje klient-databas. Anslut till en klient-databaser i SQL Server Management Studio (SSMS) och inspektera VenueTypes tabellen.  Du kan också fråga i den här tabellen i frågeredigeraren i Azure-portalen nås via databas. 

1. Öppna SSMS och ansluta till servern för klient: *tenants1-dpt -&lt;användaren&gt;. database.windows.net*
1. Kontrollera att *motorcykel tävling* och *simning en* **inte** för närvarande ingår, bläddra till den *contosoconcerthall* databasen på den *tenants1-dpt -&lt;användare&gt;*  server och fråga den *VenueTypes* tabell.



#### <a name="steps"></a>Steg

Nu du skapar ett jobb för att uppdatera den **VenueTypes** tabell i varje databas för klienter, genom att lägga till två nya plats-typer.

Om du vill skapa ett nytt jobb du använder en uppsättning jobb systemets lagrade procedurer som har skapats i den _jobagent_ databas. Lagrade procedurer skapades när agenten jobbet skapades.

1. Anslut till klient-servern i SSMS,: tenants1-huvudmålservern -&lt;användaren&gt;. database.windows.net

2. Bläddra till den *tenants1* databas.

3. Frågan i *VenueTypes* tabell för att bekräfta att *motorcykel tävling* och *simning en* ännu inte i resultatlistan.

4. Ansluta till katalogserver, vilket är *katalog-huvudmålservern -&lt;användare&gt;. database.windows.net*.

5. Ansluta till den _jobagent_ databasen i katalogservern.

6. Öppna filen i SSMS, *... \\Learning moduler\\schemat Management\\DeployReferenceData.sql*.

7. Ändra instruktionen: Ange @User = &lt;användaren&gt; och Ersätt det användar-värde som används när du distribuerade databasprogram Wingtip biljetter SaaS flera innehavare.

8. Tryck **F5** för att köra skriptet.

#### <a name="observe"></a>Observera

Observera följande objekt i den *DeployReferenceData.sql* skript:

- **SP\_lägga till\_mål\_grupp** skapar namnet på målgrupp *DemoServerGroup*, och lägger till målet medlemmar i gruppen.

- **SP\_lägga till\_mål\_grupp\_medlem** lägger till följande objekt:
    - En *server* måltyp medlem.
        - Det här är den *tenants1-huvudmålservern -&lt;användare&gt;*  server som innehåller databaserna som klienter.
        - Inklusive servern innehåller klient-databaser som finns vid den tid som jobbet körs.
    - En *databasen* mål Medlemstyp för malldatabasen (*basetenantdb*) som finns på *katalog-huvudmålservern -&lt;användare&gt;*  server
    - En *databasen* måltyp medlem att inkludera den *adhocreporting* databas som används i en senare kursen.

- **SP\_lägga till\_jobbet** skapas ett jobb som kallas *referens Data distribution*.

- **SP\_lägga till\_jobbsteg** skapar de steg som innehåller text för T-SQL-kommandot om du vill uppdatera referenstabellen VenueTypes.

- De återstående vyerna i skriptet visar att jobbet finns och övervakar jobbkörningen. Använd de här frågorna för att granska statusvärde i den **livscykel** kolumnen att avgöra när jobbet har slutförts. Jobbet uppdateras databasen klienter, samt två nya databaser som innehåller referenstabellen.

SSMS, bläddra till klient-databasen på den *tenants1-huvudmålservern -&lt;användare&gt;*  server. Frågan i *VenueTypes* tabell för att bekräfta att *motorcykel tävling* och *simning en* nu lagts till i tabellen. Totalt antal typer av platsen bör har ökat med två.

## <a name="create-a-job-to-manage-the-reference-table-index"></a>Skapa ett jobb för att hantera referenstabellindexet

Den här övningen skapar ett jobb för att återskapa indexet för referens tabellens primärnyckel på alla klient-databaser. Återskapa ett index är en typisk management databasåtgärd som en administratör kan köras efter inläsning av stora mängder data belastning, att förbättra prestanda.

1. Anslut till i SSMS, _jobagent_ databasen i *katalog-huvudmålservern -&lt;användare&gt;. database.windows.net* server.

2. Öppna i SSMS, *... \\Learning moduler\\schemat Management\\OnlineReindex.sql*.

3. Tryck **F5** för att köra skriptet.

#### <a name="observe"></a>Observera

Observera följande objekt i den *OnlineReindex.sql* skript:

* **SP\_lägga till\_jobbet** skapar ett nytt jobb som kallas *Online indexera PK\_\_VenueTyp\_\_265E44FD7FD4C885*.

* **SP\_lägga till\_jobbsteg** skapar de steg som innehåller text för T-SQL-kommando för att uppdatera indexet.

* De återstående vyerna i skriptet övervaka jobbkörning av. Använd de här frågorna för att granska statusvärde i den **livscykel** kolumnen att avgöra när jobbet har slutförts på alla mål-gruppmedlemmar.

## <a name="additional-resources"></a>Ytterligare resurser

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [Hantera utskalade molndatabaser](sql-database-elastic-jobs-overview.md)
* [Skapa och hantera utskalade molndatabaser](sql-database-elastic-jobs-create-and-manage.md)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
.
> * Skapa en agent för jobbet om du vill köra T-SQL-jobb över flera databaser
> * Uppdatera referensdata i alla klient-databaser
> * Skapar ett index i en tabell i alla klientdatabaser

Försök [Ad hoc-rapportering kursen] (saas-multitenantdb-ad hoc-reporting.md) att utforska köra distribuerade frågor via klient databaser.

