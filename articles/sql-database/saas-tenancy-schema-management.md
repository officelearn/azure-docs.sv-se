---
title: Hantera Azure SQL Database-schema i en enda klient-app | Microsoft Docs
description: Hantera scheman för flera klienter i en enda klient-app som använder Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
manager: craigg
ms.date: 09/19/2018
ms.openlocfilehash: 2f747eb09fd13647c4b6764ce3cc4fe72c00bcf0
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47054854"
---
# <a name="manage-schema-in-a-saas-application-using-the-database-per-tenant-pattern-with-azure-sql-database"></a>Hantera schemat i ett SaaS-program med hjälp av mönstret för databas-per-klient med Azure SQL Database
 
Som ett databasprogram utvecklas måste ändringar oundvikligen göras för databasdata för schema eller referens.  Uppgifter för databasunderhåll krävs också regelbundet. Hantera ett program som använder databas per klient mönstret kräver att du tillämpar dessa ändringar eller underhållsaktiviteter över en flotta med klientdatabaser.

Den här guiden utforskar två scenarier – distribuera uppdateringar av referensdata för alla klienter och när ett index i tabellen med referensdata. Den [elastiska jobb](sql-database-elastic-jobs-overview.md) används för att utföra dessa åtgärder på alla klientdatabaser och för mall-databasen som används för att skapa ny klient databaser.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]

> * Skapa en jobbagent
> * Orsaka T-SQL-jobb som ska köras på alla klientdatabaser
> * Uppdatera referensdata i alla klientdatabaser
> * Skapar ett index i en tabell i alla klientdatabaser


Se till att följande förhandskrav är slutförda för att kunna slutföra den här guiden:

* Wingtip biljetter SaaS databas Per klient-appen har distribuerats. Om du vill distribuera i mindre än fem minuter [distribuera och utforska Wingtip biljetter SaaS-databas per användare](saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell ska ha installerats. Mer information finns i [Komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Den senaste versionen av SQL Server Management Studio (SSMS) ska vara installerad. [Ladda ned och installera SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

> [!NOTE]
> Den här guiden använder funktioner för SQL Database-tjänsten som finns i en begränsad förhandsgranskning (Elastic Database-jobb). Om du vill följa den här guiden kan du ange ditt prenumerations-ID till SaaSFeedback@microsoft.com med ämnet = förhandsgranskning av elastiska jobb. När du fått en bekräftelse att din prenumeration har aktiverats kan du, [ladda ned och installera den senaste förhandsversionen av jobs-cmdletarna](https://github.com/jaredmoo/azure-powershell/releases). Den här förhandsversionen är begränsat, så Kontakta SaaSFeedback@microsoft.com för relaterade frågor eller support.

## <a name="introduction-to-saas-schema-management-patterns"></a>Introduktion till mönster för hantering av SaaS-schema

Databas per klient mönstret isolerar klientdata effektivt, men ökar antalet databaser för att hantera och underhålla. [Elastiska jobb](sql-database-elastic-jobs-overview.md) underlättar administration och hantering av SQL-databaser. Jobb låter dig säkert och tillförlitligt köra uppgifter (T-SQL-skript) mot en grupp med databaser. Jobb kan distribuera schema- och vanliga referensdataändringar för alla klientdatabaser i ett program. Elastiska jobb kan också användas för att underhålla en *mall* databasen som används för att skapa nya klienter och säkerställa att den alltid har den senaste schemat och data.

![skärmen](media/saas-tenancy-schema-management/schema-management-dpt.png)


## <a name="elastic-jobs-limited-preview"></a>Elastiska jobb begränsad förhandsvisning

Det finns en ny version av elastiska jobb som nu finns en inbyggd funktion i Azure SQL Database. Den här nya versionen av elastiska jobb är för närvarande i begränsad förhandsvisning. Den här begränsade förhandsvisningen stöder för närvarande använder PowerShell för att skapa en jobbagent och T-SQL för att skapa och hantera jobb.

> [!NOTE]
> Den här guiden använder funktioner för SQL Database-tjänsten som finns i en begränsad förhandsgranskning (Elastic Database-jobb). Om du vill följa den här guiden kan du ange ditt prenumerations-ID till SaaSFeedback@microsoft.com med ämnet = förhandsgranskning av elastiska jobb. När du fått en bekräftelse att din prenumeration har aktiverats kan du, [ladda ned och installera den senaste förhandsversionen av jobs-cmdletarna](https://github.com/jaredmoo/azure-powershell/releases). Den här förhandsversionen är begränsat, så Kontakta SaaSFeedback@microsoft.com för relaterade frågor eller support.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Hämta Wingtip biljetter SaaS-databas per klient programskript

Programmet källa kod- och skript är tillgängliga i den [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-lagringsplatsen. Kolla in den [allmänna riktlinjer](saas-tenancy-wingtip-app-guidance-tips.md) steg att ladda ned och avblockera Wingtip biljetter SaaS-skript.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Skapa en jobbagent databas och nya jobbagent

Den här självstudien krävs att du använder PowerShell för att skapa en jobbagent och dess jobbagentdatabas för säkerhetskopiering. Jobbagentdatabas innehåller jobbdefinitioner, jobbstatus och historik. När jobbagenten och dess databas har skapats, kan du skapa och övervaka jobb omedelbart.

1. **I PowerShell ISE**öppnar... \\Inlärningsmoduler\\schemahantering\\*Demo-SchemaManagement.ps1*.
1. Tryck **F5** för att köra skriptet.

Den *Demo-SchemaManagement.ps1* anropar skriptet den *Deploy-SchemaManagement.ps1* skript för att skapa en SQL-databas med namnet *osagent* på katalogservern. Sedan skapar den jobbagenten, med hjälp av databasen som en parameter.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Skapa ett jobb för att distribuera nya referensdata till alla klienter

Varje klientdatabas inkluderar en uppsättning platstyper som stöds i appen Wingtip biljetter. Varje plats är av en typ av lokal som definierar vilken typ av händelser som kan finnas och avgör bakgrundsbild som används i appen. För program som stöder nya typer av händelser, måste dessa referensdata vara uppdaterade och nya platstyperna har lagts till.  I den här övningen, distribuerar du en uppdatering till alla klientdatabaser för att lägga till två ytterligare platstyper: *Motorcycle Racing* och *Swimming Club*.

Granska först platstyper som ingår i varje klientdatabas. Ansluta till en av klientdatabaser i SQL Server Management Studio (SSMS) och granska tabellen VenueTypes.  Du kan också fråga den här tabellen i frågeredigeraren i Azure-portalen kan nås via databas. 

1. Öppna SSMS och Anslut till klientservern: *tenants1-dpt -&lt;användaren&gt;. database.windows.net*
1. Att bekräfta att *Motorcycle Racing* och *Swimming Club* **inte** för närvarande ingår, bläddra till den _contosoconcerthall_ databasen på den *tenants1-dpt -&lt;användaren&gt;*  servern och frågar den *VenueTypes* tabell.

Nu skapar vi ett jobb för att uppdatera den *VenueTypes* tabellen i alla klientdatabaser för att lägga till de nya platstyperna.

Om du vill skapa ett nytt jobb, använder du en uppsättning jobb system lagrade procedurer som skapats i den _jobagent_ databasen när jobbagenten skapades.

1. I SSMS, Anslut till katalogservern: *catalog-dpt -&lt;användaren&gt;. database.windows.net* server 
1. Öppna filen i SSMS... \\Inlärningsmoduler\\schemahantering\\DeployReferenceData.sql
1. Ändra instruktionen: Ange @wtpUser = &lt;användaren&gt; och ersätta det värde för användaren som används när du distribuerade appen Wingtip biljetter SaaS databas Per klient
1. Se till att du är ansluten till den _jobagent_ databasen och tryck **F5** att köra skriptet

Observera följande element i den *DeployReferenceData.sql* skript:
* **SP\_lägga till\_target\_grupp** skapar målgruppnamnet DemoServerGroup.
* **SP\_lägga till\_target\_grupp\_medlem** används för att definiera en uppsättning måldatabaserna.  Första den _tenants1-dpt -&lt;användaren&gt;_  servern har lagts till.  Att lägga till servern som mål leder till databaserna i servern vid tidpunkten för jobbkörningen ska inkluderas i jobbet. Sedan _basetenantdb_ databasen och *adhocreporting* databasen (används i en senare självstudiekurs) läggs till som mål.
* **SP\_lägga till\_jobbet** skapar ett jobb med namnet _referens Data distribution_.
* **SP\_lägga till\_jobstep** skapar jobbsteget med T-SQL-Kommandotext för att uppdatera referenstabellen VenueTypes.
* De återstående vyerna i skriptet visar att jobbet finns och övervakar jobbkörningen. Använd de här frågorna för att granska statusvärdet i den **livscykel** kolumn att avgöra när jobbet har slutförts på alla måldatabaserna som.

När skriptet har slutförts kan du kontrollera referensdata har uppdaterats.  I SSMS, bläddrar du till den *contosoconcerthall* databasen på den *tenants1-dpt -&lt;användaren&gt;*  servern och frågar den *VenueTypes* tabell.  Kontrollera att *Motorcycle Racing* och *Swimming Club* **är** nu finns.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Skapa ett jobb för att hantera referenstabellindexet

Den här övningen använder ett jobb för att återskapa indexet på den referentabellens primärnyckeln.  Det här är en vanlig underhållsåtgärd som kan göras efter inläsning av stora mängder data.

Skapa ett jobb med samma jobbs systemlagrade procedurer.

1. Öppna SSMS och Anslut till den _catalog-dpt -&lt;användaren&gt;. database.windows.net_ server
1. Öppna filen _... \\Inlärningsmoduler\\schemahantering\\OnlineReindex.sql_
1. Högerklicka, Välj anslutning och ansluta till den _catalog-dpt -&lt;användaren&gt;. database.windows.net_ servern, om inte redan är ansluten
1. Se till att du är ansluten till den _jobagent_ databasen och tryck **F5** att köra skriptet

Observera följande element i den _OnlineReindex.sql_ skript:
* **SP\_lägga till\_jobbet** skapar ett nytt jobb som heter ”Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885”
* **SP\_lägga till\_jobstep** skapar jobbsteget med T-SQL-Kommandotext för att uppdatera indexet
* De återstående vyerna i skriptet övervakar jobbkörningen. Använd de här frågorna för att granska statusvärdet i den **livscykel** kolumn att avgöra när jobbet har slutförts på alla medlemmar i gruppen mål.



## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]

> * Skapa en jobbagent för att köra flera databaser i T-SQL-jobb
> * Uppdatera referensdata i alla klientdatabaser
> * Skapar ett index i en tabell i alla klientdatabaser

Nu ska du prova den [Ad hoc-rapportering självstudien](saas-tenancy-cross-tenant-reporting.md) att utforska köra distribuerade frågor över klient databaser.


## <a name="additional-resources"></a>Ytterligare resurser

* [Ytterligare självstudier som bygger på programdistributionen Wingtip biljetter SaaS databas Per klient](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Hantera utskalade molndatabaser](sql-database-elastic-jobs-overview.md)
* [Skapa och hantera utskalade molndatabaser](sql-database-elastic-jobs-create-and-manage.md)