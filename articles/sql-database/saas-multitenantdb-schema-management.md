---
title: Hantera Azure SQL Database-schema i en app för flera klienter | Microsoft Docs
description: Hantera scheman för flera klienter i ett program för flera klienter som använder Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib
manager: craigg
ms.date: 09/19/2018
ms.openlocfilehash: 14183475fcca0e12c56f009f105e77aaf11b0c98
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53315224"
---
# <a name="manage-schema-in-a-saas-application-that-uses-sharded-multi-tenant-sql-databases"></a>Hantera schemat i ett SaaS-program som använder delat SQL-databaser för flera innehavare

Den här självstudien går igenom utmaningarna med att underhålla en flotta med databaser i en programvara som en tjänst (SaaS). Lösningar visas för fläkt ut schemaändringar hela flottan databaser.

Precis som alla program Wingtip biljetter SaaS-appen kommer att utvecklas med tiden och kräver ändringar i databasen. Ändringar kan påverka schemat eller referens data eller använda uppgifter för databasunderhåll. Med ett SaaS-program med hjälp av en databas per klient mönster, måste ändringarna samordnas över en potentiellt massiv mängd klientdatabaser. Dessutom måste du inkludera dessa ändringar i databasen etableringsprocessen för att säkerställa att de inkluderas i nya databaser som har skapats.

#### <a name="two-scenarios"></a>Två scenarier

Den här guiden går igenom följande två scenarier:
- Distribuera uppdateringar av referensdata för alla klienter.
- Bygga om index för tabellen med referensdata.

Den [elastiska jobb](sql-database-elastic-jobs-overview.md) funktion i Azure SQL Database används för att utföra de här åtgärderna över klientdatabaser. Jobben tillämpas även på klientdatabas ”mall”. I exempelappen Wingtip Tickets kopieras den här mallen-databasen för att etablera en ny klientdatabas.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en jobbagent.
> * Köra en T-SQL-fråga på flera klientdatabaser.
> * Uppdatera referensdata i alla klientdatabaser.
> * Skapa ett index för en tabell i alla klientdatabaser.

## <a name="prerequisites"></a>Förutsättningar

- Appen Wingtip biljetter databas för flera innehavare måste redan distribueras:
    - Instruktioner finns i den första självstudiekursen, vilket medför en Wingtip biljetter SaaS-databas för flera innehavare appen:<br />[Distribuera och utforska ett delat program för flera klienter som använder Azure SQL Database](saas-multitenantdb-get-started-deploy.md).
        - Under distribueringen körs i mindre än fem minuter.
    - Du måste ha den *shardade flera innehavare* version av Wingtip installerad. Versioner för *fristående* och *databas per klient* stöder inte den här självstudien.

- Den senaste versionen av SQL Server Management Studio (SSMS) måste installeras. [Ladda ned och installera SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

- Azure PowerShell måste vara installerad. Mer information finns i [komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

> [!NOTE]
> Den här guiden använder funktioner för Azure SQL Database-tjänsten som finns i en begränsad förhandsversion ([elastiska databasjobb](sql-database-elastic-database-client-library.md)). Om du vill följa den här guiden kan du ange ditt prenumerations-ID till *SaaSFeedback@microsoft.com* med ämnet = förhandsgranskning av elastiska jobb. När du fått en bekräftelse att din prenumeration har aktiverats kan du, [ladda ned och installera den senaste förhandsversionen av jobs-cmdletarna](https://github.com/jaredmoo/azure-powershell/releases). Den här förhandsversionen är begränsat, så Kontakta *SaaSFeedback@microsoft.com* för relaterade frågor eller support.

## <a name="introduction-to-saas-schema-management-patterns"></a>Introduktion till mönster för hantering av SaaS-schema

Fragmenterade (sharded) databas för flera innehavare modellen som används i det här exemplet gör det möjligt för en databas för klienter som innehåller en eller flera innehavare. Det här exemplet utforskar kan använda en kombination av en många-klient och en klient databaser, att aktivera en *hybrid* klient Hanteringsmodellen. Hantera ändringar till dessa databaser kan vara komplicerat. [Elastiska jobb](sql-database-elastic-jobs-overview.md) underlättar administration och hantering av stora mängder databas. Jobb låter dig säkert och tillförlitligt köra Transact-SQL-skript som aktiviteter, mot en grupp av klientdatabaser. Uppgifter som är oberoende av användarinteraktion eller indata. Den här metoden kan användas för att distribuera ändringar till schemat eller vanliga referensdata för alla klienter i ett program. Elastiska jobb kan också användas för att underhålla en gyllene mall kopia av databasen. Mallen används för att skapa nya klienter och se alltid till det senaste schemat och referensdata som används.

![skärmen](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Elastiska jobb begränsad förhandsvisning

Det finns en ny version av elastiska jobb som nu finns en inbyggd funktion i Azure SQL Database. Den här nya versionen av elastiska jobb är för närvarande i begränsad förhandsvisning. I begränsad förhandsversion för närvarande stöder som använder PowerShell för att skapa en jobbagent och T-SQL för att skapa och hantera jobb.
> [!NOTE]
> Den här guiden använder funktioner för SQL Database-tjänsten som finns i en begränsad förhandsgranskning (Elastic Database-jobb). Om du vill följa den här guiden kan du ange ditt prenumerations-ID till SaaSFeedback@microsoft.com med ämnet = förhandsgranskning av elastiska jobb. När ett meddelande bekräftar att din prenumeration har aktiverats, hämta och installera den senaste förhandsversionen av jobs-cmdletarna. Den här förhandsversionen är begränsat, så Kontakta SaaSFeedback@microsoft.com för relaterade frågor eller support.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Hämta källkoden för databas för flera klienter i Wingtip biljetter SaaS-program och skript

Databas för flera klienter i Wingtip biljetter SaaS-skript och programmets källkod finns tillgängliga i den [WingtipTicketsSaaS MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) arkivet på GitHub. Se den [allmänna riktlinjer](saas-tenancy-wingtip-app-guidance-tips.md) steg att ladda ned och avblockera Wingtip biljetter SaaS-skript.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Skapa en jobbagent databas och nya jobbagent

Den här självstudien kräver att du använder PowerShell för att skapa jobbagentdatabas och jobbagenten. Precis som MSDB-databasen används av SQL-agenten använder en jobbagent en Azure SQL-databas för att lagra jobbdefinitioner, jobbstatus och historik. När jobbagenten har skapats kan du skapa och övervaka jobb omedelbart.

1. I **PowerShell ISE**öppnar *... \\Inlärningsmoduler\\schemahantering\\Demo-SchemaManagement.ps1*.
2. Tryck **F5** för att köra skriptet.

Den *Demo-SchemaManagement.ps1* anropar skriptet den *Deploy-SchemaManagement.ps1* skript för att skapa en databas med namnet _jobagent_ på katalogservern. Skriptet skapar sedan jobbagenten, skicka den _jobagent_ databasen som en parameter.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Skapa ett jobb för att distribuera nya referensdata till alla klienter

#### <a name="prepare"></a>Förbereda

Varje klientdatabas inkluderar en uppsättning platstyper i den **VenueTypes** tabell. Varje platstypen definierar vilken typ av händelser som kan användas på en plats. De här platstyperna motsvarar bakgrundsbilder som du ser i klienternas evenemangsapp.  I den här övningen ska distribuera du en uppdatering till alla databaser att lägga till två ytterligare platstyper: *Motorcycle Racing* och *Swimming Club*.

Granska först platstyper som ingår i varje klientdatabas. Ansluta till en av klientdatabaser i SQL Server Management Studio (SSMS) och granska tabellen VenueTypes.  Du kan också fråga den här tabellen i frågeredigeraren i Azure-portalen kan nås via databas.

1. Öppna SSMS och Anslut till klientservern: *tenants1-dpt -&lt;användaren&gt;. database.windows.net*
1. Att bekräfta att *Motorcycle Racing* och *Swimming Club* **inte** för närvarande ingår, bläddra till den *contosoconcerthall* databasen på den *tenants1-dpt -&lt;användaren&gt;*  servern och frågar den *VenueTypes* tabell.



#### <a name="steps"></a>Steg

Nu du skapa ett jobb för att uppdatera den **VenueTypes** tabellen i databasen på varje klienter, genom att lägga till de två nya platstyperna.

Om du vill skapa ett nytt jobb, använder du uppsättning jobb systemlagrade procedurer som har skapats i den _jobagent_ databas. De lagrade procedurerna skapades när jobbagenten skapades.

1. I SSMS, som ansluter till klientservern: tenants1-mt -&lt;användaren&gt;. database.windows.net

2. Bläddra till den *tenants1* databas.

3. Fråga den *VenueTypes* tabell för att bekräfta att *Motorcycle Racing* och *Swimming Club* ännu inte i resultatlistan.

4. Anslut till katalogservern, vilket är *catalog-mt -&lt;användaren&gt;. database.windows.net*.

5. Ansluta till den _jobagent_ databas i katalogservern.

6. Öppna filen i SSMS, *... \\Inlärningsmoduler\\schemahantering\\DeployReferenceData.sql*.

7. Ändra instruktionen: Ange @User = &lt;användaren&gt; och ersätta det värde för användaren som används när du distribuerade databas för flera klienter i Wingtip biljetter SaaS-program.

8. Tryck **F5** för att köra skriptet.

#### <a name="observe"></a>Observera

Observera följande objekt i den *DeployReferenceData.sql* skript:

- **SP\_lägga till\_target\_grupp** skapar målgruppnamnet *DemoServerGroup*, och lägger till målmedlemmar i gruppen.

- **SP\_lägga till\_target\_grupp\_medlem** lägger du till följande objekt:
    - En *server* målmedlemstypen.
        - Det här är den *tenants1-mt -&lt;användaren&gt;*  server som innehåller databaserna som klienter.
        - Inklusive servern innehåller klientdatabaserna som finns då jobbet körs.
    - En *databasen* målmedlemstypen för mall-databasen (*basetenantdb*) som finns på *catalog-mt -&lt;användaren&gt;*  -servern
    - En *databasen* målmedlemstypen att inkludera den *adhocreporting* databas som används i en senare självstudiekurs.

- **SP\_lägga till\_jobbet** skapar ett jobb som heter *referens Data distribution*.

- **SP\_lägga till\_jobstep** skapar jobbsteget med T-SQL-Kommandotext för att uppdatera referenstabellen VenueTypes.

- De återstående vyerna i skriptet visar att jobbet finns och övervakar jobbkörningen. Använd de här frågorna för att granska statusvärdet i den **livscykel** kolumn att avgöra när jobbet har slutförts. Jobbet uppdaterar klienter databasen och uppdaterar de två ytterligare databaserna som innehåller referenstabellen.

I SSMS, bläddrar du till klientdatabasen på den *tenants1-mt -&lt;användaren&gt;*  server. Fråga den *VenueTypes* tabell för att bekräfta att *Motorcycle Racing* och *Swimming Club* läggs nu till i tabellen. Det totala antalet platstyperna bör har ökat med två.

## <a name="create-a-job-to-manage-the-reference-table-index"></a>Skapa ett jobb för att hantera referenstabellindexet

Den här övningen skapar ett jobb för att återskapa indexet på den referentabellens primärnyckeln på alla klientdatabaser. Ett index redundanshanteraren är en typisk databashanteringsåtgärden som en administratör kan köras efter inläsning av en stor mängd datainläsning, förbättra prestanda.

1. I SSMS, ansluter du till _jobagent_ databasen i *catalog-mt -&lt;användaren&gt;. database.windows.net* server.

2. I SSMS, öppna *... \\Inlärningsmoduler\\schemahantering\\OnlineReindex.sql*.

3. Tryck **F5** för att köra skriptet.

#### <a name="observe"></a>Observera

Observera följande objekt i den *OnlineReindex.sql* skript:

* **SP\_lägga till\_jobbet** skapar ett nytt jobb som heter *Online indexera PK\_\_VenueTyp\_\_265E44FD7FD4C885*.

* **SP\_lägga till\_jobstep** skapar jobbsteget med T-SQL-Kommandotext för att uppdatera indexet.

* De återstående vyerna i skriptet övervakar jobbkörningen. Använd de här frågorna för att granska statusvärdet i den **livscykel** kolumn att avgöra när jobbet har slutförts på alla medlemmar i gruppen mål.

## <a name="additional-resources"></a>Ytterligare resurser

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [Hantera utskalade molndatabaser](sql-database-elastic-jobs-overview.md)
* [Skapa och hantera utskalade molndatabaser](sql-database-elastic-jobs-create-and-manage.md)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en jobbagent för att köra T-SQL-jobb över flera databaser
> * Uppdatera referensdata i alla klientdatabaser
> * Skapar ett index i en tabell i alla klientdatabaser

Nu ska du prova den [Ad hoc-rapportering självstudien](saas-multitenantdb-adhoc-reporting.md) att utforska köra distribuerade frågor över klient databaser.

