---
title: "Köra frågor för ad hoc-rapportering över flera Azure SQL-databaser | Microsoft Docs"
description: "Köra frågor för ad hoc-rapportering över flera SQL-databaser i en app för flera innehavare exempel."
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
ms.date: 11/13/2017
ms.author: AyoOlubeko
ms.openlocfilehash: c0ed3eb344ea8ec7e2d3e86125d60c8cc28f723d
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-azure-sql-databases"></a>Köra frågor för ad hoc-analytics över flera Azure SQL-databaser

I kursen får köra du distribuerade frågor över hela uppsättningen av innehavaren databaser för att aktivera ad hoc-interaktiva rapporter. Dessa frågor kan extrahera insikter begravd i den dagliga användningsdata Wingtip biljetter SaaS-appen. Om du vill göra dessa extractions du distribuera en databas för ytterligare analys till katalog-server och Använd elastiska frågan för att aktivera distribuerade frågor.


I den här guiden lär du dig:

> [!div class="checklist"]

> * Så här distribuerar du en ad hoc-rapportdatabas
> * Så här kör du distribuerade frågor över alla klient-databaser


Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Wingtip biljetter SaaS flera innehavare databasen appen har distribuerats. För att distribuera på mindre än fem minuter finns [distribuera och utforska databasprogram Wingtip biljetter SaaS flera innehavare](saas-multitenantdb-get-started-deploy.md)
* Azure PowerShell ska ha installerats. Mer information finns i [Komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* SQL Server Management Studio (SSMS) har installerats. Om du vill hämta och installera SSMS finns [Hämta SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="ad-hoc-reporting-pattern"></a>Ad hoc-rapportering mönster

![Ad hoc reporting mönster](media/saas-multitenantdb-adhoc-reporting/adhocreportingpattern_shardedmultitenantDB.png)

SaaS-program kan analysera mängder med klientdata som lagras centralt i molnet. Analyserna avslöja insikter om drift och användning av programmet. Dessa insights hjälper funktionen utveckling, användbarhet förbättringar och andra investeringar i dina appar och tjänster.

Det är lätt att komma åt en enkel databas med flera klienter, men inte så enkelt när du har distribuerat tusentals databaser. En metod är att använda [elastisk frågan](sql-database-elastic-query-overview.md), vilket innebär att skicka en fråga i en distribuerad uppsättning databaser med gemensamma schemat. Dessa databaser kan fördelas på olika resursgrupper och prenumerationer. Ännu måste en gemensam inloggningen ha åtkomst till extrahera data från alla databaser. Elastisk frågan använder en enda *head* databas i vilken externa tabeller har definierats som spegling tabeller eller vyer i distribuerade (klient)-databaser. Frågorna som skickas till huvuddatabasen kompileras för att skapa en distribuerad frågeplan, och delar av frågan skickas ned till klientdatabaserna efter behov. Elastisk frågan använder Fragmentera kartan i katalogdatabasen för att avgöra var alla klient-databaser. Installation och fråga är enkla standarden [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference), och stöd för ad hoc-frågor från verktyg som Power BI och Excel.

Genom att distribuera frågor över klient-databaser innehåller elastisk frågan direkta insikter om live produktionsdata. Men eftersom elastisk fråga hämtar data från potentiellt många databaser, kan svarstid ibland vara högre än för motsvarande frågor skickas till en enskild databas för flera innehavare. Se till att designen frågor för att minska de data som returneras. Elastisk frågan är ofta bäst för frågor små mängder data i realtid, till skillnad från byggnad som används ofta eller komplexa analytics-frågor eller rapporter. Om frågorna inte utför också titta på den [åtgärdsplan](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) att se vilken del av frågan har aviserats till fjärrdatabasen. Och bedöma hur mycket data som returneras. Frågor som kräver komplexa analytisk bearbetning kan det vara bättre hanteras genom att spara extraherade klientdata i en databas som är optimerad för analytics-frågor. SQL Database och SQL Data Warehouse kan vara värd för sådana analytics databasen.

Det här mönstret för analytics beskrivs i den [klient analytics kursen](saas-multitenantdb-tenant-analytics.md).

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Hämta Wingtip biljetter SaaS flera innehavare databasen programmets källkod och skript

Wingtip biljetter SaaS flera innehavare databasen skript och programmets källkod är tillgängliga i den [WingtipTicketsSaaS MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub-lagringsplatsen. Kolla in den [allmänna riktlinjer](saas-tenancy-wingtip-app-guidance-tips.md) steg för att ladda ned och avblockera Wingtip biljetter SaaS-skript.

## <a name="create-ticket-sales-data"></a>Skapa biljett försäljningsdata

Skapa biljett försäljningsdata genom att köra biljett-generatorn för att köra frågor mot en mer intressant datauppsättning.

1. I den *PowerShell ISE*öppnar den... \\Learning moduler\\operativa Analytics\\ad hoc Reporting\\*Demo-AdhocReporting.ps1* skript och ange följande värden:
   * **$DemoScenario** = 1, **köpa biljetter för händelser på alla handelsplatser**.
2. Tryck på **F5** att köra skriptet och generera Biljettförsäljning. När skriptet körs, fortsätter du stegen i den här självstudiekursen. Biljett-data som efterfrågas i den *kör distribuerade ad hoc-frågor* avsnittet, så vänta biljett-generatorn att slutföra.

## <a name="explore-the-tenant-tables"></a>Utforska klienttabeller 

I databasprogram för Wingtip biljetter SaaS flera innehavare lagras hyresgäster i en hybrid klient Hanteringsmodellen - där klientdata är antingen lagras i en databas för flera innehavare eller en enskild klient-databasen och kan flyttas mellan två. När du frågar över alla klient-databaser är det viktigt att elastisk fråga kan hantera informationen som om det är en del av en enskild logisk databas delat av klient. 

För att uppnå det här mönstret alla klienttabeller innehåller en *VenueId* kolumn som identifierar som klient data som hör till. Den *VenueId* beräknas som en hash av vilken plats, men alla metoden kan användas för att införa ett unikt värde för den här kolumnen. Den här metoden är ungefär samma sätt som klientnyckeln beräknas för användning i katalogen. Tabeller som innehåller *VenueId* används av elastisk fråga att parallelize frågor och skicka dem till lämpliga fjärranslutna klientnätverk-databasen. Detta minskar avsevärt mängden data som returneras och resulterar i ökad prestanda, särskilt när det finns flera klienter vars data lagras i en organisation databaser.

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>Distribuera den databas som används för ad hoc-distribuerade frågor

Den här övningen distribuerar den *adhocreporting* databas. Detta är head-databas som innehåller schema som används för att fråga över alla klient-databaser. Databasen har distribuerats till befintliga katalogserver, vilket är den server som används för alla datorhanteringsrelaterade databaser i sample-appen.

1. Öppna... \\Learning moduler\\operativa Analytics\\ad hoc Reporting\\*Demo-AdhocReporting.ps1* i den *PowerShell ISE* och ange den följande värden:
   * **$DemoScenario** = 2, **distribuera Ad hoc-analytics databasen**.

2. Tryck på **F5** att köra skriptet och skapa den *adhocreporting* databas.

I nästa avsnitt, du lägger till schemat i databasen så att den kan användas för att köra distribuerade frågor.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Konfigurera 'head-databasen för att köra distribuerade frågor

Den här övningen lägger till schemat (extern datakälla och extern tabelldefinitioner) till ad hoc-rapportdatabasen som gör att fråga över alla klient-databaser.

1. Öppna SQL Server Management Studio och Anslut till ad hoc-rapportdatabasen som du skapade i föregående steg. Namnet på databasen är *adhocreporting*.
2. Öppna ...\Learning Modules\Operational Analytics\Adhoc Reporting\ *initiera AdhocReportingDB.sql* i SSMS.
3. Granska SQL-skript och Tänk på följande:

   Elastisk frågan använder en databas-omfattande autentisering för åtkomst till alla klient-databaser. Det här certifikatet måste vara tillgänglig i alla databaser och bör normalt beviljas minsta behörighet krävs för att aktivera dessa ad hoc-frågor.

    ![Skapa autentiseringsuppgifter](media/saas-multitenantdb-adhoc-reporting/create-credential.png)

   Med katalog-databas som den externa datakällan kan distribueras frågor för alla databaser som är registrerade i katalogen när frågan körs. Eftersom servernamn är olika för varje distribution, hämtar Initieringsskript för den här platsen för katalogdatabasen genom att hämta den aktuella servern (@@servername) där skriptet körs.

    ![Skapa extern datakälla](media/saas-multitenantdb-adhoc-reporting/create-external-data-source.png)

   Externa tabeller som refererar till klienttabeller definieras med **DISTRIBUTION = SHARDED(VenueId)**. Detta skickar en fråga för en viss *VenueId* till rätt databas och förbättrar prestandan för många scenarier som visas i nästa avsnitt.

    ![Skapa externa tabeller](media/saas-multitenantdb-adhoc-reporting/external-tables.png)

   Den lokala tabellen *VenueTypes* som skapas och fylls. Denna referenstabell data är vanligt i alla klient-databaser, så kan representeras som en lokal tabell och ifyllda med gemensamma data. Detta kan minska mängden data som flyttas mellan klient-databaser för några frågor och *adhocreporting* databas.

    ![Skapa tabell](media/saas-multitenantdb-adhoc-reporting/create-table.png)

   Om du inkluderar referenstabellerna i det här sättet måste du uppdatera tabellschemat och data när du uppdaterar klient-databaser.

4. Tryck på **F5** att köra skriptet och initiera den *adhocreporting* databas. 

Du kan nu köra distribuerade frågor och samla in insikter över alla klienter!

## <a name="run-ad-hoc-distributed-queries"></a>Köra ad hoc-distribuerade frågor

Nu när den *adhocreporting* databasen är ställa in, gå vidare och köra några distribuerade frågor. Är körningsplanen för en bättre förståelse för där frågebearbetningen sker. 

När undersöks körningsplanen, hovra över plan ikoner för information. 

1. I *SSMS*öppnar... \\Learning moduler\\operativa Analytics\\ad hoc Reporting\\*Demo-AdhocReportingQueries.sql*.
2. Se till att du är ansluten till den **adhocreporting** databas.
3. Välj den **frågan** -menyn och klicka på **innehåller faktiska körning som planera**
4. Markera den *vilka handelsplatser är registrerade?* fråga och tryck på **F5**.

   Frågan returnerar listan över hela platsen, som illustrerar hur snabbt och enkelt det är att fråga på alla klienter och returnera data från varje klient.

   Inspektera planen och se att för hela kostnaden är fjärransluten frågan eftersom vi bara gå till varje klient-databas och välja information för platsen.

   ![Välj * från dbo. Handelsplatser](media/saas-multitenantdb-adhoc-reporting/query1-plan.png)

5. Välj nästa fråga och tryck på **F5**.

   Den här frågan kopplar ihop data från klient-databaser och lokalt *VenueTypes* tabellen (lokal, eftersom den är en tabell den *adhocreporting* databas).

   Inspektera planen och se att flesta kostnaden är fjärransluten frågan eftersom vi söka varje klients plats info (dbo. Handelsplatser) och gör sedan en snabb lokalt join med lokalt *VenueTypes* tabell för att visa det egna namnet.

   ![Anslut på fjärrdatorn och den lokala data](media/saas-multitenantdb-adhoc-reporting/query2-plan.png)

6. Nu välja de *vilken dag mest biljetter såldes?* fråga och tryck på **F5**.

   Den här frågan har lite mer komplexa att ansluta och aggregering. Vad är viktigt att notera är att de flesta av bearbetningen sker externt och återigen vi hämta bara de rader som vi måste returnera en enda rad för varje plats sammanställd biljett Försäljning antal per dag.

   ![DocumentDB](media/saas-multitenantdb-adhoc-reporting/query3-plan.png)


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]

> * Köra distribuerade frågor över alla klientdatabaser
> * Distribuera en ad hoc-rapportdatabasen och lägga till schemat för att kunna köra distribuerade frågor.

Prova den [klient Analytics-självstudier](saas-multitenantdb-tenant-analytics.md) att utforska extrahera data till en separat analytics-databas för mer komplexa analyser bearbetning.

## <a name="additional-resources"></a>Ytterligare resurser

<!-- ??
* Additional [tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->

* [Elastic Query](sql-database-elastic-query-overview.md)
