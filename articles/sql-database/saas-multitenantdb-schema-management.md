---
title: Hantera schema i en app med flera innehavare
description: Hantera scheman för flera klienter i ett program för flera klienter som använder Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, sstein
ms.date: 12/18/2018
ms.openlocfilehash: 6f660426c41b37dd27438c28cbf603bdbf1e58b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269203"
---
# <a name="manage-schema-in-a-saas-application-that-uses-sharded-multi-tenant-sql-databases"></a>Hantera schema i ett SaaS-program som använder fragmenterade SQL-databaser med flera innehavare

Den här självstudien undersöker utmaningarna med att underhålla en flotta av databaser i ett SaaS-program (Software as a Service). Lösningar demonstreras för att fläkta ut schemaändringar i databasens flotta.

Precis som alla program kommer Wingtip Tickets SaaS-appen att utvecklas med tiden och kräver ändringar i databasen. Ändringar kan påverka schema- eller referensdata eller använda databasunderhållsuppgifter. Med ett SaaS-program som använder en databas per klientmönster måste ändringar samordnas över en potentiellt massiv flotta av klientdatabaser. Dessutom måste du infoga dessa ändringar i databasetableringsprocessen för att säkerställa att de inkluderas i nya databaser när de skapas.

#### <a name="two-scenarios"></a>Två scenarier

I den här självstudien beskrivs följande två scenarier:
- Distribuera referensdatauppdateringar för alla klienter.
- Återskapa ett index i tabellen som innehåller referensdata.

Funktionen [Elastiska jobb i](elastic-jobs-overview.md) Azure SQL Database används för att köra dessa åtgärder över klientdatabaser. Jobben fungerar också på klientdatabasen "mall". I exempelappen Wingtip Tickets kopieras den här malldatabasen för att etablera en ny klientdatabas.

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Skapa en jobbagent.
> * Kör en T-SQL-fråga på flera klientdatabaser.
> * Uppdatera referensdata i alla klientdatabaser.
> * Skapa ett index i en tabell i alla klientdatabaser.

## <a name="prerequisites"></a>Krav

- Databasappen Wingtip Tickets med flera innehavare måste redan distribueras:
    - Instruktioner finns i den första självstudien, som introducerar Wingtip Tickets SaaS databasapp för flera innehavare:<br />[Distribuera och utforska ett fragmenterat program för flera innehavare som använder Azure SQL Database](saas-multitenantdb-get-started-deploy.md).
        - Distributionsprocessen körs i mindre än fem minuter.
    - Du måste ha den *fragmenterade multi-tenant-versionen* av Wingtip installerad. Versionerna för *fristående* och *databas per klient stöder* inte den här självstudien.

- Den senaste versionen av SQL Server Management Studio (SSMS) måste installeras. [Ladda ner och installera SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

- Azure PowerShell måste installeras. Mer information finns i [Komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

> [!NOTE]
> Den här självstudien använder funktioner i Azure SQL Database-tjänsten som finns i en begränsad förhandsversion ([Elastiska databasjobb](sql-database-elastic-database-client-library.md)). Om du vill göra den här självstudien anger du ditt prenumerations-ID till *\@SaaSFeedback microsoft.com* med subject=Elastic Jobs Preview. När du fått en bekräftelse att din prenumeration har aktiverats kan du, [ladda ned och installera den senaste förhandsversionen av jobs-cmdletarna](https://github.com/jaredmoo/azure-powershell/releases). Den här förhandsversionen är begränsad, så kontakta *\@SaaSFeedback microsoft.com* för relaterade frågor eller support.

## <a name="introduction-to-saas-schema-management-patterns"></a>Introduktion till Mönster för hantering av SaaS-schema

Den fragmenterade databasmodellen för flera innehavare som används i det här exemplet gör att en klientdatabas kan innehålla en eller flera klienter. Det här exemplet utforskar möjligheten att använda en blandning av en databas *hybrid* med många innehavare och en klient, vilket möjliggör en hybridklienthanteringsmodell. Det kan vara komplicerat att hantera ändringar i dessa databaser. [Elastiska jobb](elastic-jobs-overview.md) underlättar administration och hantering av ett stort antal databaser. Med jobb kan du säkert och tillförlitligt köra Transact-SQL-skript som uppgifter, mot en grupp klientdatabaser. Uppgifterna är oberoende av användarinteraktion eller indata. Den här metoden kan användas för att distribuera ändringar i schemat eller till vanliga referensdata, för alla klienter i ett program. Elastiska jobb kan också användas för att underhålla en gyllene mallkopia av databasen. Mallen används för att skapa nya klienter, alltid se till att det senaste schemat och referensdata används.

![skärmen](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Elastiska jobb begränsad förhandsvisning

Det finns en ny version av elastiska jobb som nu är en integrerad funktion i Azure SQL Database. Den här nya versionen av elastiska jobb är för närvarande i begränsad förhandsvisning. Den begränsade förhandsversionen stöder för närvarande att använda PowerShell för att skapa en jobbagent och T-SQL för att skapa och hantera jobb.
> [!NOTE]
> Den här guiden använder funktioner för SQL Database-tjänsten som är i en begränsad förhandsgranskning (Elastic Database-jobb). Om du vill göra den här självstudien, ge ditt prenumerations-ID till SaaSFeedback@microsoft.com subject=Elastic Jobs Preview. När du fått en bekräftelse att din prenumeration har aktiverats kan du, ladda ned och installera den senaste förhandsversionen av jobs-cmdletarna. Den här förhandsversionen SaaSFeedback@microsoft.com är begränsad, så kontakta för relaterade frågor eller support.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Hämta källkoden och skripten för Wingtip-biljetterna SaaS Multi-tenant Database

Wingtip-biljetterna SaaS Databasskript för flera innehavare och programkällkod finns i [WingtipTicketsSSaaS-MultitenantDB-databasen](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) på GitHub. Se den [allmänna vägledningen](saas-tenancy-wingtip-app-guidance-tips.md) för steg för att ladda ner och låsa upp Wingtip Tickets SaaS-skript.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Skapa en jobbagentdatabas och ny jobbagent

Den här självstudien kräver att du använder PowerShell för att skapa jobbagentdatabasen och jobbagenten. Precis som MSDB-databasen som används av SQL Agent använder en jobbagent en Azure SQL-databas för att lagra jobbdefinitioner, jobbstatus och historik. När jobbagenten har skapats kan du skapa och övervaka jobb direkt.

1. I **PowerShell ISE**, öppna *... Utbildningsmoduler\\\\Schemahantering Demo-SchemaManagement.ps1 . \\*
2. Tryck **F5** för att köra skriptet.

*Skriptet Demo-SchemaManagement.ps1* anropar skriptet *Deploy-SchemaManagement.ps1* för att skapa en databas med namnet _jobagent_ på katalogservern. Skriptet skapar sedan jobbagenten och skickar _jobagentdatabasen_ som en parameter.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Skapa ett jobb för att distribuera nya referensdata till alla klienter

#### <a name="prepare"></a>Förbereda

Varje klientdatabas innehåller en uppsättning platstyper i tabellen **VenueTypes.** Varje platstyp definierar vilken typ av händelser som kan vara värd på en plats. Dessa platstyper motsvarar bakgrundsbilderna som visas i appen för klienthändelser.  I den här övningen distribuerar du en uppdatering till alla databaser för att lägga till ytterligare två platstyper: *Motorcycle Racing* och *Swimming Club*.

Granska först de platstyper som ingår i varje klientdatabas. Anslut till en av klientdatabaserna i SQL Server Management Studio (SSMS) och kontrollera tabellen VenueTypes.  Du kan också fråga den här tabellen i Frågeredigeraren i Azure-portalen, som nås från databassidan.

1. Öppna SSMS och anslut till klientservern: *tenants1-dpt-&lt;&gt;användare .database.windows.net*
1. För att bekräfta att *Motorcycle Racing* och *Swimming Club* för närvarande inte **ingår** bläddrar du till *contosoconcerthall-databasen* på *klienterna1-dpt-användarservern&lt;&gt; * och frågar tabellen *VenueTypes.*



#### <a name="steps"></a>Steg

Nu kan du skapa ett jobb för att uppdatera **tabellen VenueTypes** i varje klientdatabas genom att lägga till de två nya platstyperna.

Om du vill skapa ett nytt jobb använder du den uppsättning jobbsystem som lagrats i _jobbagentdatabasen._ De lagrade procedurerna skapades när jobbagenten skapades.

1. I SSMS ansluter du till klientservern:&lt;tenants1-mt- användare&gt;.database.windows.net

2. Bläddra till *databasen tenants1.*

3. Fråga tabellen *VenueTypes* för att bekräfta att *Motorcycle Racing* och *Swimming Club* ännu inte finns med i resultatlistan.

4. Anslut till katalogservern, som är *katalog-mt-&lt;&gt;användare .database.windows.net*.

5. Anslut till _jobagentdatabasen_ i katalogservern.

6. I SSMS, öppna filen *... Utbildningsmoduler\\\\Schemahantering DeployReferenceData.sql . \\*

7. Ändra uttrycket: @User &lt;ange&gt; = användare och ersätt det användarvärde som användes när du distribuerade Wingtip Tickets SaaS Multi-tenant Database-programmet.

8. Tryck **F5** för att köra skriptet.

#### <a name="observe"></a>Observera

Observera följande objekt i *skriptet DeployReferenceData.sql:*

- **sp\_\_lägga\_till målgrupp** skapar målgruppsnamnet *DemoServerGroup*och lägger till målmedlemmar i gruppen.

- **sp\_\_lägga\_\_till målgruppsmedlem** lägger till följande objekt:
    - En medlemstyp för *servermål.*
        - Det här är *&lt;&gt; klienten1-mt-användarservern* som innehåller klientdatabaserna.
        - Inklusive servern innehåller klientdatabaser som finns vid den tidpunkt då jobbet körs.
    - En *database* databasmålmedlemstyp för malldatabasen (*basetenantdb*) som finns på *katalog-mt-&lt;&gt; användarserver,*
    - En *database* databasmålmedlemstyp som ska inkludera databasen *adhocreporting* som används i en senare självstudiekurs.

- **sp\_\_lägga till jobb** skapar ett jobb som kallas *Reference Data Deployment*.

- **sp\_\_lägga jobstep** skapar jobbsteget som innehåller T-SQL kommandotext för att uppdatera referenstabellen VenueTypes.

- De återstående vyerna i skriptet visar att jobbet finns och övervakar jobbkörningen. Använd dessa frågor för att granska statusvärdet i **livscykelkolumnen** för att avgöra när jobbet är klart. Jobbet uppdaterar klientdatabasen och uppdaterar de två ytterligare databaser som innehåller referenstabellen.

I SSMS bläddrar du till klientdatabasen på *&lt;&gt; klienten1-mt-användarservern.* Fråga tabellen *VenueTypes* för att bekräfta att *Motorcycle Racing* och *Swimming Club* nu läggs till i tabellen. Det totala antalet platstyper borde ha ökat med två.

## <a name="create-a-job-to-manage-the-reference-table-index"></a>Skapa ett jobb för att hantera referenstabellindexet

Den här övningen skapar ett jobb för att återskapa indexet på referenstabellens primärnyckel på alla klientdatabaser. En indexrekläggning är en typisk databashanteringsåtgärd som en administratör kan köra efter att ha läst in en stor mängd datainläsning för att förbättra prestanda.

1. I SSMS ansluter du till _jobagentdatabasen_ i *katalog-mt-&lt;Användare&gt;.database.windows.net-server.*

2. I SSMS, öppna *... Utbildningsmoduler\\\\Schemahantering OnlineReindex.sql . \\*

3. Tryck **F5** för att köra skriptet.

#### <a name="observe"></a>Observera

Observera följande objekt i *onlinereindex.sql-skriptet:*

* **sp\_\_lägga jobb** skapar ett nytt jobb som heter *Online Reindex PK\_\_\_\_VenueTyp 265E44FD7FD4C885*.

* **sp\_\_lägga jobstep** skapar jobbsteget som innehåller T-SQL kommandotext för att uppdatera indexet.

* De återstående vyerna i skriptövervakar jobbkörningen. Använd dessa frågor för att granska statusvärdet i **livscykelkolumnen** för att avgöra när jobbet har slutförts på alla målgruppsmedlemmar.

## <a name="additional-resources"></a>Ytterligare resurser

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [Hantera utskalade molndatabaser](elastic-jobs-overview.md)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en jobbagent för att köra T-SQL-jobb i flera databaser
> * Uppdatera referensdata i alla klientdatabaser
> * Skapar ett index i en tabell i alla klientdatabaser

Försök sedan med [ad hoc-rapporteringsstudien](saas-multitenantdb-adhoc-reporting.md) för att utforska löpande distribuerade frågor över klientdatabaser.

