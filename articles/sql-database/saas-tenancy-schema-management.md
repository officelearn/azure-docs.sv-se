---
title: Hantera schema i en app med en enda klientorganisation
description: Hantera schema för flera klienter i en app med en innehavare som använder Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 09/19/2018
ms.openlocfilehash: b6802d97b964b8863f6c2fce0cebfe16782b46fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269216"
---
# <a name="manage-schema-in-a-saas-application-using-the-database-per-tenant-pattern-with-azure-sql-database"></a>Hantera schema i ett SaaS-program med hjälp av mönstret databas per klient med Azure SQL Database
 
I takt med att ett databasprogram utvecklas måste ändringar oundvikligen göras i databasschemat eller referensdata.  Databasunderhållsuppgifter behövs också regelbundet. Hantera ett program som använder databasen per klientmönster kräver att du tillämpar dessa ändringar eller underhållsuppgifter i en flotta av klientdatabaser.

Den här självstudien utforskar två scenarier – distribuera referensdatauppdateringar för alla klienter och återskapa ett index i tabellen som innehåller referensdata. Funktionen [Elastiska jobb](elastic-jobs-overview.md) används för att köra dessa åtgärder på alla klientdatabaser och på malldatabasen som används för att skapa nya klientdatabaser.

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> 
> * Skapa en jobbagent
> * Orsaka att T-SQL-jobb körs på alla klientdatabaser
> * Uppdatera referensdata i alla klientdatabaser
> * Skapar ett index i en tabell i alla klientdatabaser


Se till att följande förhandskrav är slutförda för att kunna slutföra den här guiden:

* Den Wingtip Biljetter SaaS Databas per klient app distribueras. Information om hur du distribuerar på mindre än fem minuter finns i [Distribuera och utforska Wingtip Tickets SaaS-databasen per klientprogram](saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Den senaste versionen av SQL Server Management Studio (SSMS) ska vara installerad. [Ladda ned och installera SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

> [!NOTE]
> Den här guiden använder funktioner för SQL Database-tjänsten som är i en begränsad förhandsgranskning (Elastic Database-jobb). Om du vill göra den här självstudien, ge ditt prenumerations-ID till SaaSFeedback@microsoft.com subject=Elastic Jobs Preview. När du fått en bekräftelse att din prenumeration har aktiverats kan du, [ladda ned och installera den senaste förhandsversionen av jobs-cmdletarna](https://github.com/jaredmoo/azure-powershell/releases). Den här förhandsversionen SaaSFeedback@microsoft.com är begränsad, så kontakta för relaterade frågor eller support.

## <a name="introduction-to-saas-schema-management-patterns"></a>Introduktion till Mönster för hantering av SaaS-schema

Databasen per klientmönster isolerar klientdata effektivt, men ökar antalet databaser att hantera och underhålla. [Elastiska jobb](elastic-jobs-overview.md) underlättar administration och hantering av SQL-databaser. Med jobb kan du köra uppgifter (T-SQL-skript) på ett säkert och tillförlitligt sätt mot en grupp databaser. Jobb kan distribuera schema och vanliga referensdataändringar i alla klientdatabaser i ett program. Elastiska jobb kan också användas för att underhålla en *malldatabas* som används för att skapa nya klienter, vilket säkerställer att den alltid har de senaste schema- och referensdata.

![skärmen](media/saas-tenancy-schema-management/schema-management-dpt.png)


## <a name="elastic-jobs-limited-preview"></a>Elastiska jobb begränsad förhandsvisning

Det finns en ny version av elastiska jobb som nu är en integrerad funktion i Azure SQL Database. Den här nya versionen av elastiska jobb är för närvarande i begränsad förhandsvisning. Den här begränsade förhandsversionen stöder för närvarande att använda PowerShell för att skapa en jobbagent och T-SQL för att skapa och hantera jobb.

> [!NOTE]
> Den här guiden använder funktioner för SQL Database-tjänsten som är i en begränsad förhandsgranskning (Elastic Database-jobb). Om du vill göra den här självstudien, ge ditt prenumerations-ID till SaaSFeedback@microsoft.com subject=Elastic Jobs Preview. När du fått en bekräftelse att din prenumeration har aktiverats kan du, [ladda ned och installera den senaste förhandsversionen av jobs-cmdletarna](https://github.com/jaredmoo/azure-powershell/releases). Den här förhandsversionen SaaSFeedback@microsoft.com är begränsad, så kontakta för relaterade frågor eller support.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Hämta Wingtip Tickets SaaS-databasen per klientprogramskript

Programmets källkod och hanteringsskript finns i [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub repo. Kolla in den [allmänna vägledningen](saas-tenancy-wingtip-app-guidance-tips.md) för steg för att ladda ner och låsa upp Wingtip Tickets SaaS skript.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Skapa en jobbagentdatabas och ny jobbagent

Den här självstudien kräver att du använder PowerShell för att skapa en jobbagent och dess stödjobbagentdatabas. Jobbagentdatabasen innehåller jobbdefinitioner, jobbstatus och historik. När jobbagenten och dess databas har skapats kan du skapa och övervaka jobb direkt.

1. **I PowerShell ISE**, öppna ... \\Utbildningsmoduler\\Schemahantering\\*Demo-SchemaManagement.ps1*.
1. Tryck **F5** för att köra skriptet.

*Skriptet Demo-SchemaManagement.ps1* anropar skriptet *Deploy-SchemaManagement.ps1* för att skapa en SQL-databas med namnet *osagent* på katalogservern. Den skapar sedan jobbagenten med hjälp av databasen som en parameter.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Skapa ett jobb för att distribuera nya referensdata till alla klienter

I appen Wingtip-biljetter innehåller varje klientdatabas en uppsättning platstyper som stöds. Varje plats är av en specifik platstyp, som definierar vilken typ av händelser som kan vara värd och bestämmer bakgrundsbilden som används i appen. För att programmet ska stödja nya typer av händelser måste dessa referensdata uppdateras och nya platstyper läggas till.  I den här övningen, distribuerar du en uppdatering till alla klientdatabaser för att lägga till två ytterligare platstyper: *Motorcycle Racing* och *Swimming Club*.

Granska först de platstyper som ingår i varje klientdatabas. Anslut till en av klientdatabaserna i SQL Server Management Studio (SSMS) och kontrollera tabellen VenueTypes.  Du kan också fråga den här tabellen i Frågeredigeraren i Azure-portalen, som nås från databassidan. 

1. Öppna SSMS och anslut till klientservern: *tenants1-dpt-&lt;&gt;användare .database.windows.net*
1. För att bekräfta att *Motorcycle Racing* och *Swimming Club* för närvarande inte **ingår** bläddrar du till _contosoconcerthall-databasen_ på *klienterna1-dpt-användarservern&lt;&gt; * och frågar tabellen *VenueTypes.*

Nu ska vi skapa ett jobb för att uppdatera *tabellen VenueTypes* i alla klientdatabaser för att lägga till de nya platstyperna.

Om du vill skapa ett nytt jobb använder du en uppsättning jobbsystem lagrade procedurer som skapats i _jobagentdatabasen_ när jobbagenten skapades.

1. I SSMS ansluter du till katalogservern: *katalog-dpt-&lt;användare&gt;.database.windows.net-server* 
1. I SSMS, öppna filen ... \\Utbildningsmoduler\\Schemahantering\\DeployReferenceData.sql
1. Ändra uttrycket: @wtpUser &lt;SET&gt; = användare och ersätt användarvärdet som användes när du distribuerade SaaS-databasen För Wingtip-biljetterna Per klient
1. Se till att du är ansluten till _jobagent-databasen_ och tryck på **F5** för att köra skriptet

Observera följande element i *skriptet DeployReferenceData.sql:*
* **sp\_\_lägga\_till målgrupp** skapar målgruppen namnet DemoServerGroup.
* **sp\_\_lägga\_\_till målgruppsmedlem** används för att definiera uppsättningen måldatabaser.  Först läggs _&lt;&gt; klienterna1-dpt-användarservern_ till.  Om du lägger till servern som ett mål inkluderas databaserna på servern vid tidpunkten för jobbkörningen i jobbet. Sedan läggs _basetenantdb-databasen_ och *adhocreporting-databasen* (som används i en senare självstudiekurs) till som mål.
* **sp\_\_lägga till jobb** skapar ett jobb med namnet _Reference Data Deployment_.
* **sp\_\_lägga jobstep** skapar jobbsteget som innehåller T-SQL kommandotext för att uppdatera referenstabellen VenueTypes.
* De återstående vyerna i skriptet visar att jobbet finns och övervakar jobbkörningen. Använd dessa frågor för att granska statusvärdet i **livscykelkolumnen** för att avgöra när jobbet har slutförts på alla måldatabaser.

När skriptet har slutförts kan du kontrollera att referensdata har uppdaterats.  I SSMS bläddrar du till *contosoconcerthall-databasen* på *&lt;klienterna1-dpt-användarservern&gt; * och frågar tabellen *VenueTypes.*  Kontrollera att *Motorcycle Racing* och *Swimming Club* **är** nu närvarande.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Skapa ett jobb för att hantera referenstabellindexet

Den här övningen använder ett jobb för att återskapa indexet på referenstabellens primärnyckel.  Detta är en typisk databasunderhållsåtgärd som kan göras efter inläsning av stora mängder data.

Skapa ett jobb med samma jobbs systemlagrade procedurer.

1. Öppna SSMS och anslut till _katalog-dpt-användaren&lt;&gt;.database.windows.net-servern_
1. Öppna filen _... Utbildningsmoduler\\\\Schemahantering OnlineReindex.sql \\_
1. Högerklicka, välj Anslutning och anslut till _katalog-dpt-&lt;användaren&gt;.database.windows.net-servern,_ om den inte redan är ansluten
1. Se till att du är ansluten till _jobagent-databasen_ och tryck på **F5** för att köra skriptet

Observera följande element i _OnlineReindex.sql-skriptet:_
* **sp\_\_lägga jobb** skapar ett nytt jobb som\_\_heter "Online Reindex PK VenueTyp\_\_265E44FD7FD4C885"
* **sp\_\_lägga jobstep** skapar jobbsteget som innehåller T-SQL kommandotext för att uppdatera indexet
* De återstående vyerna i skriptövervakar jobbkörningen. Använd dessa frågor för att granska statusvärdet i **livscykelkolumnen** för att avgöra när jobbet har slutförts på alla målgruppsmedlemmar.



## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> 
> * Skapa en jobbagent för att köra över T-SQL-jobb flera databaser
> * Uppdatera referensdata i alla klientdatabaser
> * Skapar ett index i en tabell i alla klientdatabaser

Försök sedan med [ad hoc-rapporteringsstudien](saas-tenancy-cross-tenant-reporting.md) för att utforska löpande distribuerade frågor över klientdatabaser.


## <a name="additional-resources"></a>Ytterligare resurser

* [Ytterligare självstudier som bygger på Wingtip Tickets SaaS Database Per Tenant-programdistribution](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Hantera utskalade molndatabaser](elastic-jobs-overview.md)