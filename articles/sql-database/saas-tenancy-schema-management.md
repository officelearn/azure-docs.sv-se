---
title: Hantera schema i en app med en enda klient
description: Hantera schema för flera klienter i en app med en enda klient som använder Azure SQL Database
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78397167"
---
# <a name="manage-schema-in-a-saas-application-using-the-database-per-tenant-pattern-with-azure-sql-database"></a>Hantera schemat i ett SaaS-program med hjälp av mönstret för databas per klient med Azure SQL Database
 
När ett databas program utvecklas, behöver ändringar som oundvikligen göras i databasens schema eller referens data.  Databas underhålls aktiviteter behövs också regelbundet. Att hantera ett program som använder databasen per klient mönster kräver att du tillämpar dessa ändringar eller underhålls uppgifter i en flotta av klient databaser.

Den här självstudien utforskar två scenarier – distribution av referens data uppdateringar för alla klienter och återskapa ett index i tabellen som innehåller referens data. Funktionen [elastiska jobb](elastic-jobs-overview.md) används för att utföra dessa åtgärder på alla klient databaser och på den mall databas som används för att skapa nya klient databaser.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> 
> * Skapa en jobb agent
> * Gör så att T-SQL-jobb körs på alla klient databaser
> * Uppdatera referens data i alla klient databaser
> * Skapar ett index i en tabell i alla klientdatabaser


Se till att följande förhandskrav är slutförda för att kunna slutföra den här guiden:

* Wingtip biljetter SaaS-databasen per klient-app distribueras. Om du vill distribuera på mindre än fem minuter, se [distribuera och utforska Wingtip-biljetter SaaS-databas per klient program](saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell ska ha installerats. Mer information finns i [Komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Den senaste versionen av SQL Server Management Studio (SSMS) ska vara installerad. [Ladda ned och installera SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

> [!NOTE]
> I den här självstudien används funktioner i SQL Databases tjänsten som är i en begränsad förhands granskning (Elastic Database-jobb). Om du vill göra den här själv studie kursen anger du ditt prenumerations-ID för att SaaSFeedback@microsoft.com med ämnet = för hands version av elastiska jobb När du fått en bekräftelse att din prenumeration har aktiverats kan du, [ladda ned och installera den senaste förhandsversionen av jobs-cmdletarna](https://github.com/jaredmoo/azure-powershell/releases). Den här för hands versionen är begränsad, så kontakta SaaSFeedback@microsoft.com för relaterade frågor eller support.

## <a name="introduction-to-saas-schema-management-patterns"></a>Introduktion till SaaS schema hanterings mönster

I mönstret för databas per klient isoleras klient data effektivt, men det ökar antalet databaser som ska hanteras och underhållas. [Elastiska jobb](elastic-jobs-overview.md) underlättar administration och hantering av SQL-databaser. Med jobb kan du på ett säkert och tillförlitligt sätt köra uppgifter (T-SQL-skript) mot en grupp med databaser. Jobb kan distribuera schema-och gemensamma referens data ändringar i alla klient databaser i ett program. Elastiska jobb kan också användas för att underhålla en *mall* -databas som används för att skapa nya klienter, vilket säkerställer att den alltid har det senaste schema-och referens data.

![skärmen](media/saas-tenancy-schema-management/schema-management-dpt.png)


## <a name="elastic-jobs-limited-preview"></a>Elastiska jobb begränsad förhandsvisning

Det finns en ny version av elastiska jobb som nu är en integrerad funktion i Azure SQL Database. Den här nya versionen av elastiska jobb är för närvarande i begränsad förhandsvisning. Denna begränsade för hands version har stöd för att använda PowerShell för att skapa en jobb agent och T-SQL för att skapa och hantera jobb.

> [!NOTE]
> I den här självstudien används funktioner i SQL Databases tjänsten som är i en begränsad förhands granskning (Elastic Database-jobb). Om du vill göra den här själv studie kursen anger du ditt prenumerations-ID för att SaaSFeedback@microsoft.com med ämnet = för hands version av elastiska jobb När du fått en bekräftelse att din prenumeration har aktiverats kan du, [ladda ned och installera den senaste förhandsversionen av jobs-cmdletarna](https://github.com/jaredmoo/azure-powershell/releases). Den här för hands versionen är begränsad, så kontakta SaaSFeedback@microsoft.com för relaterade frågor eller support.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Hämta Wingtip-biljetterna SaaS-databas per klient program skript

Programmets käll kod och hanterings skript är tillgängliga i [WingtipTicketsSaaS-DbPerTenant GitHub-](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) lagrings platsen. Ta en titt på den [allmänna vägledningen](saas-tenancy-wingtip-app-guidance-tips.md) för steg för att ladda ned och avblockera Wingtip Ticket SaaS-skript.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Skapa en jobb agent databas och en ny jobb agent

I den här självstudien krävs att du använder PowerShell för att skapa en jobb agent och dess agent databas för återställning av jobb. Jobb Agent databasen innehåller jobb definitioner, jobb status och historik. När jobb agenten och databasen har skapats kan du skapa och övervaka jobb direkt.

1. **I POWERSHELL ISE**öppnar du...\\Learning-moduler\\Schema hantering\\*schemamanagement. ps1*.
1. Tryck **F5** för att köra skriptet.

Skriptet *schemamanagement. ps1* anropar skriptet *Deploy-SchemaManagement. ps1* för att skapa en SQL-databas med namnet *osagent* på katalog servern. Sedan skapas jobb agenten med hjälp av-databasen som en parameter.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Skapa ett jobb för att distribuera nya referensdata till alla klienter

I Wingtip biljetter-appen innehåller varje klient databas en uppsättning plats typer som stöds. Varje plats är av en viss platstyp, som definierar vilken typ av händelser som kan hanteras och anger bakgrunds bilden som används i appen. För att programmet ska stödja nya typer av händelser måste dessa referens data uppdateras och nya plats typer läggs till.  I den här övningen, distribuerar du en uppdatering till alla klientdatabaser för att lägga till två ytterligare platstyper: *Motorcycle Racing* och *Swimming Club*.

Börja med att granska de plats typer som ingår i varje klient databas. Anslut till en av klient databaserna i SQL Server Management Studio (SSMS) och granska VenueTypes-tabellen.  Du kan också fråga den här tabellen i Frågeredigeraren i Azure Portal, som öppnas från databas sidan. 

1. Öppna SSMS och Anslut till klient servern: *tenants1-DPT-&lt;user&gt;. Database.Windows.net*
1. För att bekräfta att *motorcykelns racing* -och *SIM-klubb* **inte** ingår för närvarande, bläddrar du till _contosoconcerthall_ -databasen på *tenants1-DPT-&lt;User&gt;-* servern och frågar *VenueTypes* -tabellen.

Nu ska vi skapa ett jobb för att uppdatera *VenueTypes* -tabellen i alla klient databaser för att lägga till nya typer av platser.

Om du vill skapa ett nytt jobb använder du en uppsättning jobb system lagrade procedurer som skapats i _JobAgent_ -databasen när jobb agenten skapades.

1. I SSMS ansluter du till katalog servern: *Catalog-DPT-&lt;user&gt;. Database.Windows.net* -Server 
1. I SSMS öppnar du filen...\\Learning-moduler\\schema hantering\\DeployReferenceData. SQL
1. Ändra instruktionen: SET @wtpUser = &lt;User&gt; och ersätt User-värdet som används när du distribuerade Wingtip-biljetter SaaS-databasen per klient program
1. Se till att du är ansluten till _JobAgent_ -databasen och tryck **F5** för att köra skriptet

Observera följande element i *DeployReferenceData. SQL* -skriptet:
* **sp\_Lägg till\_mål\_grupp** skapar mål grupp namnet DemoServerGroup.
* **sp\_lägga till\_mål\_grupp\_medlem** används för att definiera uppsättningen mål databaser.  Först _tenants1-DPT-&lt;user&gt;_ -servern läggs till.  Om du lägger till servern som ett mål kommer databaserna på den servern när jobb körningen ska inkluderas i jobbet. Sedan läggs _basetenantdb_ -databasen och *AdHocReporting* -databasen (används i en senare självstudie) som mål.
* **sp\_Lägg till\_jobb** skapar ett jobb med namnet _referens data distribution_.
* **sp\_Lägg till\_Jobstep** skapar jobb steget som innehåller t-SQL-kommando texten för att uppdatera referens tabellen, VenueTypes.
* De återstående vyerna i skriptet visar att jobbet finns och övervakar jobbkörningen. Använd de här frågorna för att granska status värdet i kolumnen **livs cykel** för att fastställa när jobbet har avslut ATS på alla mål databaser.

När skriptet har slutförts kan du kontrol lera att referens data har uppdaterats.  I SSMS, bläddrar du till *contosoconcerthall* -databasen på *användar&gt;s servern tenants1-DPT-&lt;* och frågar *VenueTypes* -tabellen.  Kontrol lera att *motorcykelns racing* -och *SIM-klubb* **nu finns** .


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Skapa ett jobb för att hantera referenstabellindexet

I den här övningen används ett jobb för att återskapa indexet för primär nyckeln för referens tabellen.  Detta är en typisk databas underhålls åtgärd som kan utföras efter att stora mängder data lästs in.

Skapa ett jobb med samma jobbs systemlagrade procedurer.

1. Öppna SSMS och Anslut till tjänsten _katalog-DPT-&lt;användare&gt;. Database.Windows.net-_ Server
1. Öppna filen _...\\Learning-moduler\\schema hantering\\OnlineReindex. SQL_
1. Högerklicka, Välj anslutning och Anslut till _katalogen-DPT-&lt;användar&gt;. Database.Windows.net-_ Server, om den inte redan är ansluten
1. Se till att du är ansluten till _JobAgent_ -databasen och tryck **F5** för att köra skriptet

Observera följande element i _OnlineReindex. SQL_ -skriptet:
* **sp\_Lägg till\_jobb** skapar ett nytt jobb med namnet "online Omindexera PK\_\_VenueTyp\_\_265E44FD7FD4C885"
* **sp\_Lägg till\_Jobstep** skapar jobb steget som innehåller t-SQL-kommando texten för att uppdatera indexet
* Återstående vyer i skript övervaknings jobb körningen. Använd de här frågorna för att granska status värdet i kolumnen **livs cykel** för att fastställa när jobbet har slutförts på alla mål grupps medlemmar.



## <a name="next-steps"></a>Nästa steg

I den här guiden lärde du dig hur man:

> [!div class="checklist"]
> 
> * Skapa en jobb agent för att köra över T-SQL-jobb flera databaser
> * Uppdatera referens data i alla klient databaser
> * Skapar ett index i en tabell i alla klientdatabaser

Prova sedan [själv studie kursen om ad hoc-rapportering](saas-tenancy-cross-tenant-reporting.md) för att utforska köra distribuerade frågor över klient databaser.


## <a name="additional-resources"></a>Ytterligare resurser

* [Ytterligare självstudier som bygger på Wingtip-biljetter SaaS-databas per klient program distribution](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Hantera utskalade molndatabaser](elastic-jobs-overview.md)