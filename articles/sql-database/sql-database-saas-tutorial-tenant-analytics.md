---
title: "Köra analysfrågor över flera Azure SQL-databaser | Microsoft Docs"
description: "Extrahera data från klienten databaser till en analytics-databas för offlineanalys"
keywords: sql database tutorial
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: billgib; sstein
ms.openlocfilehash: 4a96efb15268c56e3625832b0b4d6dd8f6a78614
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="extract-data-from-tenant-databases-into-an-analytics-database-for-offline-analysis"></a>Extrahera data från klienten databaser till en analytics-databas för offlineanalys

I den här kursen använder du ett elastiska jobb för att köra frågor mot varje klient-databas. Jobbet extraherar biljett försäljningsdata och läser in den i en analytics-databas (eller ett datalager) för analys. Analytics-databasen sedan tillfrågas om du vill extrahera insikter från den här dagliga användningsdata för alla klienter.


I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en klientanalysdatabas
> * Skapa ett schemalagt jobb för att hämta data och stoppa in i analysdatabasen

Se till att följande förhandskrav är slutförda för att kunna slutföra den här guiden:

* Wingtip SaaS-appen har distribuerats. För att distribuera på mindre än fem minuter finns [distribuera och utforska Wingtip SaaS-program](sql-database-saas-tutorial.md)
* Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Den senaste versionen av SQL Server Management Studio (SSMS) ska vara installerad. [Ladda ned och installera SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

## <a name="tenant-operational-analytics-pattern"></a>Klientents användningsanalysmönster

En av fördelarna med SaaS-program är att du kan dra nytta av de stora mängderna klientdata lagras i molnet. Använd denna data för att få insikter i driften och användningen av dina program och dina klienter. Datan kan styra funktionsutveckling, förbättringar av användbarhet och andra investeringar i appen och plattformen. Det är lätt att komma åt denna data när den finns i en enda databas för flera klienter, men inte lika lätt när den är distribuerad i stor skala över potentiellt tusentals databaser. Ett sätt att komma åt dessa data är att använda elastiska jobb, vilket gör att du kan samla in frågeresultat som resulterar i returer från jobbkörningar i en databas och tabell med utdata.

## <a name="get-the-wingtip-application-scripts"></a>Hämta Wingtip-programskript

Wingtip SaaS-skript och programmets källkod är tillgängliga i den [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) github-lagringsplatsen. [Steg för att hämta Wingtip SaaS-skripten](sql-database-wtp-overview.md#download-and-unblock-the-wingtip-saas-scripts).

## <a name="deploy-a-database-for-tenant-analytics-results"></a>Distribuera en databas för klientanalysresultat

Den här guiden kräver att du har en databas distribuerad för att samla in resultaten från jobbkörning av skript som innehåller frågor som returnerar resultat. Vi skapar en databas som heter tenantanalytics för detta ändamål.

1. Öppna ...\\inlärningsmoduler\\operativ analys\\klientanalys\\*Demo-TenantAnalyticsDB.ps1* i *PowerShell ISE* och ange följande värde:
   * **$DemoScenario** = **2** *distribuera operativ analysdatabas*
1. Tryck **F5** för att köra demo-skriptet (som anropar skriptet *Deploy-TenantAnalyticsDB.ps1*) som skapar klientanalysdatabasen.

## <a name="create-some-data-for-the-demo"></a>Skapa data för presentationen

1. Öppna ...\\inlärningsmoduler\\operativ analys\\klientanalys\\*Demo-TenantAnalyticsDB.ps1* i *PowerShell ISE* och ange följande värde:
   * **$DemoScenario** = **1** *köp biljetter för evenemang på alla platser*
1. Tryck **F5** för att köra skriptet och skapa biljettköpshistorik.


## <a name="create-a-scheduled-job-to-retrieve-tenant-analytics-about-ticket-purchases"></a>Skapa ett schemalagt jobb för att hämta klientanalys om biljettköp

Det här skriptet skapar ett jobb för att hämta biljettköpsinformation från alla klienter. När det sammanställts i en enda tabell, kan du få omfattande och viktiga mått om biljettköpsmönster för klienterna.

1. Öppna SSMS och anslut till servern catalog-&lt;användare&gt;.database.windows.net
1. Öppna... \\inlärningsmoduler\\operativ analys\\klientanalys\\*TicketPurchasesfromAllTenants.sql*
1. Ändra &lt;användare&gt;, användarnamnet som används när du har distribuerat appen Wingtip SaaS längst upp i skriptet **sp\_lägga till\_mål\_grupp\_medlem** och **sp\_lägga till\_jobbsteg**
1. Högerklicka, Välj **anslutning**, och ansluta till katalogen -&lt;användaren&gt;. database.windows.net server, om inte redan är ansluten
1. Kontrollera att du är ansluten till **jobaccount**-databasen och tryck **F5** för att köra skriptet

* **sp\_add\_target\_group** skapar målgruppnamnet *TenantGroup*, nu behöver vi lägga till målmedlemmar.
* **SP\_lägga till\_mål\_grupp\_medlem** lägger till en *server* mål Medlemstyp som bedömer alla databaser på servern (Observera customer1 -&lt;användaren&gt; servern som innehåller klient-databaser) vid tiden för jobbet körningen ska inkluderas i jobbet.
* **sp\_add\_job** skapar ett nytt jobb som schemaläggs veckovis och heter ”biljettköp från alla klienter”
* **sp\_add\_jobstep** skapar ett jobb med T-SQL-kommandotext för att hämta all biljettköpsinformation från alla klienter och kopierar den returnerande resultatuppsättningen i en tabell som heter *AllTicketsPurchasesfromAllTenants*
* De återstående vyerna i skriptet visar att jobbet finns och övervakar jobbkörningen. Granska statusvärdet från **livscykel**-kolumnen för att övervaka status. När det lyckats så har jobbet slutförts på alla klientdatabaser och de två ytterligare databaserna som innehåller referenstabellen.

Om skriptet lyckas, bör det resultera i liknande resultat:

![resultat](media/sql-database-saas-tutorial-tenant-analytics/ticket-purchases-job.png)

## <a name="create-a-job-to-retrieve-a-summary-count-of-ticket-purchases-from-all-tenants"></a>Skapa ett jobb för att hämta ett sammanfattande antal biljettinköp från alla klienter

Skriptet skapar ett jobb för att hämta summan av alla biljettköp från alla klienter.

1. Öppna SSMS och anslut till servern *catalog-&lt;användare&gt;.database.windows.net*
1. Öppna filen... \\inlärningsmoduler\\etablera och katalogisera\\operativa analys\\klientanalys\\*Results-TicketPurchasesfromAllTenants.sql resultat*
1. Ändra &lt;användare&gt;, användarnamnet som används när du har distribuerat appen Wingtip SaaS i skriptet i den **sp\_lägga till\_jobbsteg** lagrad procedur
1. Högerklicka, Välj **anslutning**, och ansluta till katalogen -&lt;användaren&gt;. database.windows.net server, om inte redan är ansluten
1. Kontrollera att du är ansluten till **tenantanalytics**-databasen och tryck **F5** för att köra skriptet

Om skriptet lyckas, bör det resultera i liknande resultat:

![resultat](media/sql-database-saas-tutorial-tenant-analytics/total-sales.png)



* **sp\_add\_job** skapar en ny schemalagt jobb som heter “ResultsTicketsOrders”

* **sp\_add\_jobstep** skapar ett jobb med T-SQL-kommandotext för att hämta all biljettköpsinformation från alla klienter och kopiera den returnerande resultatuppsättningen i en tabell som heter CountofTicketOrders

* De återstående vyerna i skriptet visar att jobbet finns och övervakar jobbkörningen. Granska statusvärdet från **livscykel**-kolumnen för att övervaka status. När det lyckats så har jobbet slutförts på alla klientdatabaser och de två ytterligare databaserna som innehåller referenstabellen.


## <a name="next-steps"></a>Nästa steg

I den här guiden lärde du dig hur man:

> [!div class="checklist"]
> * Distribuera en klientanalysdatabas
> * Skapa ett schemalagt jobb för att hämta analysdata över innehavare

Grattis!

## <a name="additional-resources"></a>Ytterligare resurser

* Ytterligare [självstudier som bygger på Wingtip SaaS-program](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastiska jobb](sql-database-elastic-jobs-overview.md)
