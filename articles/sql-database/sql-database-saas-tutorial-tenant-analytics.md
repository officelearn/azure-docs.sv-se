---
title: "Köra analysfrågor över flera Azure SQL-databaser | Microsoft Docs"
description: "Köra distribuerade frågor över flera Azure SQL-databaser"
keywords: sql database tutorial
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: a0742a004b618dda304618bca21ae715552c16e6
ms.contentlocale: sv-se
ms.lasthandoff: 05/12/2017


---
# <a name="run-distributed-queries-across-multiple-azure-sql-databases"></a>Köra distribuerade frågor över flera Azure SQL-databaser

I den här guiden kör du analysfrågor mot varje klient i katalogen. Ett elastiskt jobb skapas som kör frågorna. Jobbet hämtar data och läser in den i en separat analysdatabas som skapats på katalogservern. Den här databasen kan frågas för att få fram insikter som finns i daglig användningsdata för alla klienter. Som utdata för jobbet, skapas en tabell från de frågor som returnerar resultat inuti klientanalysdatabasen.


I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Skapa en klientanalysdatabas
> * Skapa ett schemalagt jobb för att hämta data och stoppa in i analysdatabasen

Se till att följande förhandskrav är slutförda för att kunna slutföra den här guiden:

* WTP-appen ska ha distribuerats. Du kan distribuera appen på mindre än fem minuter genom att följa anvisningarna i [Distribuera och utforska WTP SaaS-appen](sql-database-saas-tutorial.md)
* Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Den senaste versionen av SQL Server Management Studio (SSMS) ska vara installerad. [Ladda ned och installera SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

## <a name="tenant-operational-analytics-pattern"></a>Klientents användningsanalysmönster

En av fördelarna med SaaS-program är att du kan dra nytta av de stora mängderna klientdata lagras i molnet. Använd denna data för att få insikter i driften och användningen av dina program och dina klienter. Datan kan styra funktionsutveckling, förbättringar av användbarhet och andra investeringar i appen och plattformen. Det är lätt att komma åt denna data när den finns i en enda databas för flera klienter, men inte lika lätt när den är distribuerad i stor skala över potentiellt tusentals databaser. Ett sätt att komma åt dessa data är att använda elastiska jobb, vilket gör att du kan samla in frågeresultat som resulterar i returer från jobbkörningar i en databas och tabell med utdata.

## <a name="get-the-wingtip-application-scripts"></a>Hämta Wingtip-programskripten

Wingtip biljettskripten och programmets källkod finns tillgängliga från github-repon [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Skriptfilerna finns i [mappen inlärningsmoduler](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Ladda ner mappen **inlärningsmoduler** till din lokala dator med sin mappstruktur intakt.

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

1. Öppna SSMS och anslut till servern catalog-\<användare\>.database.windows.net
1. Öppna... \\inlärningsmoduler\\operativ analys\\klientanalys\\*TicketPurchasesfromAllTenants.sql*
1. Ändra \<WtpUser\> och använd det användarnamn du använde när du distribuerade WTP-appen längst upp i skriptet **sp\_add\_target\_group\_members** och **sp\_add\_jobstep**
1. Högerklicka, välj **Anslutning** och anslut till servern catalog-\<WtpUser\>.database.windows.net, om du inte redan är ansluten
1. Kontrollera att du är ansluten till **jobaccount**-databasen och tryck **F5** för att köra skriptet

* **sp\_add\_target\_group** skapar målgruppnamnet *TenantGroup*, nu behöver vi lägga till målmedlemmar.
* **sp\_add\_target\_group\_member** lägger till målmedlemstypen *server*, som bedömer att alla databaser inom den servern (observera att det är servern customer1-&lt;WtpUser&gt; som innehåller klientdatabaserna) vid tidpunkten för jobbkörningen ska inkluderas i jobbet.
* **sp\_add\_job** skapar ett nytt jobb som schemaläggs veckovis och heter ”biljettköp från alla klienter”
* **sp\_add\_jobstep** skapar ett jobb med T-SQL-kommandotext för att hämta all biljettköpsinformation från alla klienter och kopierar den returnerande resultatuppsättningen i en tabell som heter *AllTicketsPurchasesfromAllTenants*
* De återstående vyerna i skriptet visar att jobbet finns och övervakar jobbkörningen. Granska statusvärdet från **livscykel**-kolumnen för att övervaka status. När det lyckats så har jobbet slutförts på alla klientdatabaser och de två ytterligare databaserna som innehåller referenstabellen.

Om skriptet lyckas, bör det resultera i liknande resultat:

![resultat](media/sql-database-saas-tutorial-tenant-analytics/ticket-purchases-job.png)

## <a name="create-a-job-to-retrieve-a-summary-count-of-ticket-purchases-from-all-tenants"></a>Skapa ett jobb för att hämta ett sammanfattande antal biljettinköp från alla klienter

Skriptet skapar ett jobb för att hämta summan av alla biljettköp från alla klienter.

1. Öppna SSMS och anslut till servern *catalog-&lt;användare&gt;.database.windows.net*
1. Öppna filen... \\inlärningsmoduler\\etablera och katalogisera\\operativa analys\\klientanalys\\*Results-TicketPurchasesfromAllTenants.sql resultat*
1. Ändra &lt;WtpUser&gt;, använd det användarnamn som du använde när du distribuerade WTP-appen i skriptet, i den lagrade proceduren **sp\_add\_jobstep**
1. Högerklicka, välj **Anslutning** och anslut till servern catalog-\<WtpUser\>.database.windows.net, om du inte redan är ansluten
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

* [Ytterligare guider som bygger på den initiala programdistributionen av Wingtip biljettplattformen (WTP)](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [Elastiska jobb](sql-database-elastic-jobs-overview.md)
