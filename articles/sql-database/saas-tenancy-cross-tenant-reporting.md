---
title: "Kör rapportering frågor över flera Azure SQL-databaser | Microsoft Docs"
description: "Cross-klient med distribuerade frågor."
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
ms.topic: articles
ms.date: 11/13/2017
ms.author: billgib; sstein; AyoOlubeko
ms.openlocfilehash: 531d284798e455622faa1bfe7b0c8f178b3642fd
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2018
---
# <a name="cross-tenant-reporting-using-distributed-queries"></a>Cross-klient med distribuerade frågor

I kursen får köra du distribuerade frågor över hela uppsättningen av innehavaren databaser för rapportering. Dessa frågor kan extrahera insikter begravd i den dagliga användningsdata för Wingtip biljetter SaaS-klienter. Gör du genom att distribuera en ytterligare rapportdatabasen till katalog-server och Använd elastiska frågan för att aktivera distribuerade frågor.


I den här guiden lär du dig:

> [!div class="checklist"]

> * Så här distribuerar du en reporting-databas
> * Så här kör du distribuerade frågor över alla klient-databaser
> * Hur globala vyer i varje databas kan aktivera effektiva frågor över klienter


Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:


* Wingtip biljetter SaaS databas Per klient appen har distribuerats. För att distribuera på mindre än fem minuter finns [distribuera och utforska programmet Wingtip biljetter SaaS databas Per klient](saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell ska ha installerats. Mer information finns i [Komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* SQL Server Management Studio (SSMS) har installerats. Om du vill hämta och installera SSMS finns [Hämta SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="cross-tenant-reporting-pattern"></a>Mellan klient reporting mönster

![mellan klient distribuerade frågan mönster](media/saas-tenancy-cross-tenant-reporting/cross-tenant-distributed-query.png)

En affärsmöjlighet med SaaS-program är att använda mängder med klientdata som lagras i molnet och få insikter om drift och användning av programmet. Dessa insights hjälper funktionen utveckling, användbarhet förbättringar och andra investeringar i dina appar och tjänster.

Det är lätt att komma åt en enkel databas med flera klienter, men inte så enkelt när du har distribuerat tusentals databaser. En metod är att använda [elastisk frågan](sql-database-elastic-query-overview.md), vilket innebär att skicka en fråga i en distribuerad uppsättning databaser med gemensamma schemat. Dessa databaser kan fördelas på olika resursgrupper och prenumerationer, men behöver dela en gemensam inloggning. Elastisk frågan använder en enda *head* databas i vilken externa tabeller har definierats som spegling tabeller eller vyer i distribuerade (klient)-databaser. Frågorna som skickas till huvuddatabasen kompileras för att skapa en distribuerad frågeplan, och delar av frågan skickas ned till klientdatabaserna efter behov. Elastisk frågan använder Fragmentera kartan i katalogdatabasen för att avgöra var alla klient-databaser. Installation och fråga till head databasen är enkla standarden [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference), och stöd för frågor från verktyg som Power BI och Excel.

Genom att distribuera frågor över klient-databaser innehåller elastisk frågan direkta insikter om live produktionsdata. Eftersom elastisk fråga hämtar data från potentiellt många databaser, kan svarstid vara högre än motsvarande frågor skickas till en enskild databas för flera innehavare. Utforma frågor för att minska de data som returneras till head-databasen. Elastisk frågan är ofta bäst för frågor små mängder data i realtid, till skillnad från byggnad som används ofta eller komplexa analytics-frågor eller rapporter. Om frågorna inte utför väl kan titta på den [åtgärdsplan](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) att se vilken del av frågan flyttas fram till fjärrdatabasen och hur mycket data som returneras. Frågor som kräver komplex aggregering eller analytisk behandling kanske bättre handtag genom att extrahera klientdata till en databas eller data warehouse som optimerats för analytics-frågor. Det här mönstret förklaras i den [klient analytics kursen](saas-tenancy-tenant-analytics.md). 

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Hämta programskript Wingtip biljetter SaaS databas Per klient

Wingtip biljetter SaaS flera innehavare databasen skript och programmets källkod är tillgängliga i den [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-lagringsplatsen. Kolla in den [allmänna riktlinjer](saas-tenancy-wingtip-app-guidance-tips.md) steg för att ladda ned och avblockera Wingtip biljetter SaaS-skript.

## <a name="create-ticket-sales-data"></a>Skapa biljett försäljningsdata

Skapa biljett försäljningsdata genom att köra biljett-generatorn för att köra frågor mot en mer intressant datauppsättning.

1. I den *PowerShell ISE*öppnar den... \\Learning moduler\\operativa Analytics\\ad hoc Reporting\\*Demo-AdhocReporting.ps1* skript och anger följande värde:
   * **$DemoScenario** = 1, **köpa biljetter för händelser på alla handelsplatser**.
2. Tryck på **F5** att köra skriptet och generera Biljettförsäljning. När skriptet körs, fortsätter du stegen i den här självstudiekursen. Biljett-data som efterfrågas i den *köra ad hoc-distribuerade frågor* avsnittet, så vänta biljett-generatorn att slutföra.

## <a name="explore-the-global-views"></a>Utforska globala vyer

I programmet Wingtip biljetter SaaS databas Per klient ges varje klient en databas. Data i databastabellerna begränsas således perspektivet i en enskild klient. Men när du frågar över alla databaser är det viktigt att elastisk fråga kan hantera informationen som om det är en del av en enskild logisk databas delat av klient. 

För att simulera detta mönster, en uppsättning 'globala' vyer läggs till klient databasen projektet klient-ID till varje tabell som frågas globalt. Till exempel den *VenueEvents* visa lägger till en beräknad *VenueId* kolumnerna planerat från den *händelser* tabell. På liknande sätt den *VenueTicketPurchases* och *VenueTickets* vyer lägga till en beräknad *VenueId* kolumnen planerat från deras respektive tabeller. Dessa vyer används av elastisk fråga för att parallelize frågor och push dem till lämpliga fjärranslutna klientnätverk databasen när en *VenueId* finns. Detta minskar avsevärt mängden data som returneras och resulterar i en stor ökning av prestanda för många frågor. De här globala vyer har skapats i förväg i alla klient-databaser.

1. Öppna SSMS och [ansluta till tenants1 -&lt;användare&gt; server](saas-tenancy-wingtip-app-guidance-tips.md#explore-database-schema-and-execute-sql-queries-using-ssms).
1. Expandera **databaser**, högerklicka på _contosoconcerthall_, och välj **ny fråga**.
1. Kör följande frågor för att utforska skillnaden mellan en klient-tabeller och globala vyer:

   ```T-SQL
   -- The base Venue table, that has no VenueId associated.
   SELECT * FROM Venue

   -- Notice the plural name 'Venues'. This view projects a VenueId column.
   SELECT * FROM Venues

   -- The base Events table, which has no VenueId column.
   SELECT * FROM Events

   -- This view projects the VenueId retrieved from the Venues table.
   SELECT * FROM VenueEvents
   ```

I dessa vyer i *VenueId* beräknas som en hash av vilken plats, men alla metoden kan användas för att införa ett unikt värde. Den här metoden är ungefär samma sätt som klientnyckeln beräknas för användning i katalogen.

För att granska definitionen av den *handelsplatser* vy:

1. I **Object Explorer**, expandera **contosoconcerthall** > **vyer**:

   ![visningar](media/saas-tenancy-cross-tenant-reporting/views.png)

2. Högerklicka på **dbo. Handelsplatser**.
3. Välj **skript som** > **skapa till** > **nya Query Editor-fönstret**

Skript för någon av de andra *plats* vyer för att se hur de lägger till den *VenueId*.

## <a name="deploy-the-database-used-for-distributed-queries"></a>Distribuera den databas som används för distribuerade frågor

Den här övningen distribuerar den _adhocreporting_ databas. Detta är head-databas som innehåller schema som används för att fråga över alla klient-databaser. Databasen har distribuerats till befintliga katalogserver, vilket är den server som används för alla datorhanteringsrelaterade databaser i sample-appen.

1. i *PowerShell ISE*öppnar... \\Learning moduler\\operativa Analytics\\ad hoc Reporting\\*Demo-AdhocReporting.ps1*. 

1. Ange **$DemoScenario = 2**, _distribuera Ad hoc-rapportdatabasen_.

1. Tryck på **F5** att köra skriptet och skapa den *adhocreporting* databas.

I nästa avsnitt, du lägger till schemat i databasen så att den kan användas för att köra distribuerade frågor.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Konfigurera 'head-databasen för att köra distribuerade frågor

Den här övningen lägger till schemat (extern datakälla och extern tabelldefinitioner) till den _adhocreporting_ databasen för att aktivera frågor över alla klient-databaser.

1. Öppna SQL Server Management Studio och Anslut till ad hoc Reporting-databas som du skapade i föregående steg. Namnet på databasen är *adhocreporting*.
2. Öppna ...\Learning Modules\Operational Analytics\Adhoc Reporting\ _initiera AdhocReportingDB.sql_ i SSMS.
3. Granska SQL-skript och Observera:

   Elastisk frågan använder en databas-omfattande autentisering för åtkomst till alla klient-databaser. Det här certifikatet måste vara tillgänglig i alla databaser och bör normalt beviljas minsta behörighet krävs för att aktivera de här frågorna.

    ![Skapa autentiseringsuppgifter](media/saas-tenancy-cross-tenant-reporting/create-credential.png)

   Med katalog-databas som den externa datakällan distribueras frågor till alla databaser som har registrerats i katalogen när frågan körs. Eftersom servernamn är olika för varje distribution skriptet hämtar platsen för katalogdatabasen från den aktuella servern (@@servername) där skriptet körs.

    ![Skapa extern datakälla](media/saas-tenancy-cross-tenant-reporting/create-external-data-source.png)

   Externa tabeller som refererar till de globala vyerna som beskrivs i föregående avsnitt och definieras med **DISTRIBUTION = SHARDED(VenueId)**. Eftersom varje *VenueId* mappar till en enskild databas detta förbättrar prestanda för många scenarier som visas i nästa avsnitt.

    ![Skapa externa tabeller](media/saas-tenancy-cross-tenant-reporting/external-tables.png)

   Den lokala tabellen _VenueTypes_ som skapas och fylls. Denna referenstabell data är vanligt i alla klient-databaser, så kan representeras som en lokal tabell och ifyllda med gemensamma data. För vissa frågor minska med den här tabellen som definierats i head databasen mängden data som ska flyttas till head-databasen.

    ![Skapa tabell](media/saas-tenancy-cross-tenant-reporting/create-table.png)

   Om du inkluderar referenstabellerna i det här sättet måste du uppdatera tabellschemat och data när du uppdaterar klient-databaser.

4. Tryck på **F5** att köra skriptet och initiera den *adhocreporting* databas. 

Du kan nu köra distribuerade frågor och samla in insikter över alla klienter!

## <a name="run-distributed-queries"></a>Kör distribuerade frågor

Nu när den *adhocreporting* databasen är ställa in, gå vidare och köra några distribuerade frågor. Är körningsplanen för en bättre förståelse för där frågebearbetningen sker. 

När undersöks körningsplanen, hovra över plan ikoner för information. 

Viktigt att notera, är den här inställningen **DISTRIBUTION = SHARDED(VenueId)** när den externa datakällan definieras förbättrar prestandan för många scenarier. Som varje *VenueId* mappar till en enskild databas filtrering enkelt görs via fjärranslutning, returnerar de data som krävs.

1. Öppna... \\Learning moduler\\operativa Analytics\\ad hoc Reporting\\*Demo-AdhocReportingQueries.sql* i SSMS.
2. Se till att du är ansluten till den **adhocreporting** databas.
3. Välj den **frågan** -menyn och klicka på **innehåller faktiska körning som planera**
4. Markera den *vilka handelsplatser är registrerade?* fråga och tryck på **F5**.

   Frågan returnerar listan över hela platsen, som illustrerar hur snabbt och enkelt det är att fråga på alla klienter och returnera data från varje klient.

   Kontrollera planen och att det finns för hela kostnaden i fjärranslutna frågan. Varje klient databas kör frågan via fjärranslutning och returnerar informationen platsen till head-databasen.

   ![Välj * från dbo. Handelsplatser](media/saas-tenancy-cross-tenant-reporting/query1-plan.png)

5. Välj nästa fråga och tryck på **F5**.

   Den här frågan kopplar ihop data från klient-databaser och lokalt *VenueTypes* tabellen (lokal, eftersom den är en tabell den *adhocreporting* databas).

   Inspektera planen och se att flesta kostnaden är fjärransluten frågan. Varje klient-databas returnerar information om dess plats och utför en lokal sammanfogning med lokalt *VenueTypes* tabell för att visa det egna namnet.

   ![Anslut på fjärrdatorn och den lokala data](media/saas-tenancy-cross-tenant-reporting/query2-plan.png)

6. Nu välja de *vilken dag mest biljetter såldes?* fråga och tryck på **F5**.

   Den här frågan har lite mer komplexa att ansluta och aggregering. De flesta av bearbetningen sker via en fjärranslutning.  Endast returneras enstaka rader, som innehåller varje plats biljett Försäljning antal per dag per dag, till head-databasen.

   ![DocumentDB](media/saas-tenancy-cross-tenant-reporting/query3-plan.png)


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]

> * Köra distribuerade frågor över alla klientdatabaser
> * Distribuera en rapportdatabasen och definiera schemat som krävs för att köra distribuerade frågor.


Prova den [klient Analytics-självstudier](saas-tenancy-tenant-analytics.md) att utforska extrahera data till en separat analytics-databas för mer komplexa analyser bearbetning.

## <a name="additional-resources"></a>Ytterligare resurser

* Ytterligare [självstudier som bygger på programmet Wingtip biljetter SaaS databas Per klient](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastic Query](sql-database-elastic-query-overview.md)
