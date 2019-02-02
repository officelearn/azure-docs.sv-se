---
title: Köra ad hoc-rapportering frågor över flera Azure SQL-databaser | Microsoft Docs
description: Köra ad hoc-rapportering frågor över flera SQL-databaser i en app för flera klienter-exemplet.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: ayolubek
ms.reviewer: sstein
manager: craigg
ms.date: 10/30/2018
ms.openlocfilehash: 2780215e409886be82ac70642dabe05c9257945e
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2019
ms.locfileid: "55568455"
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-azure-sql-databases"></a>Köra ad hoc-analysfrågor över flera Azure SQL-databaser

I den här självstudien får köra du distribuerade frågor över hela uppsättningen klient databaser för att aktivera ad hoc-interaktiva rapporter. De här frågorna kan extrahera insikter som legat dolda i daglig användningsdata för Wingtip biljetter SaaS-appen. För att göra dessa extraheringarna måste du distribuera en databas för ytterligare analys till katalogservern och Använd elastisk fråga för att aktivera distribuerade frågor.


I den här guiden lär du dig:

> [!div class="checklist"]

> * Så här distribuerar du en ad hoc-rapportdatabasen
> * Hur du kan köra distribuerade frågor över alla klientdatabaser


Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Databas för flera klienter i Wingtip biljetter SaaS-appen har distribuerats. Om du vill distribuera i mindre än fem minuter [distribuera och utforska databas för flera klienter i Wingtip biljetter SaaS-program](saas-multitenantdb-get-started-deploy.md)
* Azure PowerShell ska ha installerats. Mer information finns i [Komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* SQL Server Management Studio (SSMS) är installerad. Om du vill hämta och installera SSMS, se [ladda ned SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="ad-hoc-reporting-pattern"></a>Ad hoc-rapportering mönster

![mönster för ad hoc-rapportering](media/saas-multitenantdb-adhoc-reporting/adhocreportingpattern_shardedmultitenantDB.png)

SaaS-program kan analysera mängder med klientdata som lagras centralt i molnet. Analyser avslöja insikter i driften och användningen av ditt program. Dessa insikter hjälper funktionsutveckling, förbättringar av användbarhet och andra investeringar i dina appar och tjänster.

Det är lätt att komma åt en enkel databas med flera klienter, men inte så enkelt när du har distribuerat tusentals databaser. En metod är att använda [elastisk fråga](sql-database-elastic-query-overview.md), vilket gör att fråga över en distribuerad uppsättning databaser med ett gemensamt schema. Dessa databaser kan distribueras över olika resursgrupper och prenumerationer. Ännu måste en vanliga inloggning ha åtkomst till att extrahera data från alla databaser. Elastic Query använder en enda *head* databasen där definieras externa tabeller som speglar tabeller eller vyer i distribuerade (klient)-databaser. Frågorna som skickas till huvuddatabasen kompileras för att skapa en distribuerad frågeplan, och delar av frågan skickas ned till klientdatabaserna efter behov. Elastic Query använder shardkartan i katalogdatabasen för att fastställa platsen för alla klientdatabaser. Konfiguration och frågor som är enkla med standarden [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference), och stöd för ad hoc-frågor från verktyg som Power BI och Excel.

Genom att distribuera frågor över klientdatabaser, ger elastisk fråga omedelbar insikt i live produktionsdata. Men eftersom elastisk fråga hämtar data från potentiellt många databaser, kan frågesvarstiden ibland vara högre än för motsvarande frågor som skickas till en enda databas för flera innehavare. Tänk på att utforma frågor för att minimera den information som returneras. Elastisk fråga är ofta bäst för att fråga små mängder realtidsdata, till skillnad från att skapa vanliga eller komplexa analytics-frågor eller rapporter. Om frågorna inte utför även kan du titta på den [Körningsplan](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) att se vilken del av frågan har pushats ner till fjärrdatabasen. Och utvärdera hur mycket data returneras. Frågor som kräver komplexa analytisk behandling kan vara bättre hanteras genom att spara den extraherade klientdata till en databas som är optimerad för analysfrågor. SQL Database och SQL Data Warehouse kan vara värd för analysdatabas.

Det här mönstret för analys förklaras i den [klientanalys](saas-multitenantdb-tenant-analytics.md).

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Hämta källkoden för databas för flera klienter i Wingtip biljetter SaaS-program och skript

Databas för flera klienter i Wingtip biljetter SaaS-skript och programmets källkod finns tillgängliga i den [WingtipTicketsSaaS MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub-lagringsplatsen. Kolla in den [allmänna riktlinjer](saas-tenancy-wingtip-app-guidance-tips.md) steg att ladda ned och avblockera Wingtip biljetter SaaS-skript.

## <a name="create-ticket-sales-data"></a>Skapa biljett försäljningsdata

Skapa biljett försäljningsdata genom att köra biljett-generatorn för att köra frågor mot ett mer intressant data.

1. I den *PowerShell ISE*öppnar den... \\Inlärningsmoduler\\Verksamhetsanalyser\\ad hoc-rapportering\\*Demo-AdhocReporting.ps1* skript och ange följande värden:
   * **$DemoScenario** = 1, **köp biljetter för evenemang på alla platser**.
2. Tryck på **F5** att köra skriptet och generera biljettförsäljningar. När skriptet körs, fortsätter du stegen i den här självstudien. Biljett-data som efterfrågas i det *kör distribuerade ad hoc-frågor* avsnittet, så vänta tills biljett-generator att slutföra.

## <a name="explore-the-tenant-tables"></a>Utforska klienttabeller 

Databas för flera klienter i Wingtip biljetter SaaS-program lagras klienter i en hybrid klient management modell - där klientdata är antingen lagras i en databas för flera innehavare eller en enskild klient-databas och kan flyttas mellan två. När frågor över alla klientdatabaser, är det viktigt att elastisk fråga kan hantera data som om det är en del av en enskild logisk databas shardade av klient. 

För att uppnå det här mönstret kan alla klienttabeller inkluderar en *VenueId* kolumn som identifierar som klient-data som hör till. Den *VenueId* beräknas som en hash av namnet på plats, men alla metoden kan användas för att introducera ett unikt värde för den här kolumnen. Den här metoden är ungefär samma sätt som klientnyckeln beräknas för användning i katalogen. Tabeller som innehåller *VenueId* används av elastisk fråga att parallellisera frågor och skicka dem till lämplig remote klientdatabasen. Detta minskar avsevärt mängden data som returneras och resulterar i en ökning av prestanda, särskilt när det finns flera klienter vars data lagras i en enda klientdatabaser.

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>Distribuera databasen som används för ad hoc-distribuerade frågor

Den här övningen distribueras den *adhocreporting* databas. Det här är head databasen som innehåller ett schema som används för att fråga över alla klientdatabaser. Databasen har distribuerats till den befintliga katalogservern, vilket är den server som används för alla datorhanteringsrelaterade databaser i exempelappen.

1. Öppna... \\Inlärningsmoduler\\Verksamhetsanalyser\\ad hoc-rapportering\\*Demo-AdhocReporting.ps1* i den *PowerShell ISE* och ange den följande värden:
   * **$DemoScenario** = 2, **distribuera Ad hoc-analysdatabas**.

2. Tryck på **F5** att köra skriptet och skapa den *adhocreporting* databas.

I nästa avsnitt du till schemat till databasen så att den kan användas för att köra distribuerade frågor.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Konfigurera ”huvud”-databasen för att köra distribuerade frågor

Den här övningen lägger till schemat (extern datakälla och definitioner av extern tabell) i ad hoc-rapportdatabasen som möjliggör frågekörning för alla klientdatabaser.

1. Öppna SQL Server Management Studio och Anslut till ad hoc-rapportdatabasen som du skapade i föregående steg. Namnet på databasen är *adhocreporting*.
2. Öppna ...\Learning Modules\Operational Analytics\Adhoc Reporting\ *initiera AdhocReportingDB.sql* i SSMS.
3. Granska SQL-skriptet och Tänk på följande:

   Elastic Query använder en databas-omfattande autentisering för att få åtkomst till varje klient. Den här autentiseringsuppgiften måste vara tillgängliga i alla databaser och bör normalt beviljas de minsta rättigheterna krävas för att aktivera ad hoc-frågor.

    ![skapa autentiseringsuppgift](media/saas-multitenantdb-adhoc-reporting/create-credential.png)

   Med katalogdatabasen som den externa datakällan kan distribueras till alla databaser som har registrerats i katalogen när frågan körs. Eftersom servernamn är olika för varje distribution, hämtar Initieringsskript för den här platsen för katalogdatabasen genom att hämta den aktuella servern (@@servername) där skriptet körs.

    ![Skapa extern datakälla](media/saas-multitenantdb-adhoc-reporting/create-external-data-source.png)

   De externa tabeller som refererar till klienttabeller definieras med **DISTRIBUTION = SHARDED(VenueId)**. Detta skickar en fråga för en viss *VenueId* till rätt databas och förbättrar prestandan för många scenarier som du ser i nästa avsnitt.

    ![Skapa externa tabeller](media/saas-multitenantdb-adhoc-reporting/external-tables.png)

   Den lokala tabellen *VenueTypes* som skapas och fylls i. Denna referenstabell data är vanligt i alla klientdatabaser, så att den kan visas här som en lokal tabell och fylls i automatiskt med de vanliga data. För vissa frågor, kan det minska mängden data som flyttas mellan klientdatabaser och *adhocreporting* databas.

    ![Skapa tabell](media/saas-multitenantdb-adhoc-reporting/create-table.png)

   Om du inkluderar referenstabeller i det här sättet måste du uppdatera tabellschemat och data när du uppdaterar klientdatabaserna.

4. Tryck på **F5** att köra skriptet och initiera den *adhocreporting* databas. 

Du kan nu köra distribuerade frågor och samla information över alla klienter!

## <a name="run-ad-hoc-distributed-queries"></a>Köra ad hoc-distribuerade frågor

Nu när den *adhocreporting* databasen är ställer in, gå vidare och köra några distribuerade frågor. Inkludera Körningsplan för en bättre förståelse för där frågebearbetningen sker. 

Vid kontroll körningsplanen, hovrar du över plan ikoner för information. 

1. I *SSMS*öppnar... \\Inlärningsmoduler\\Verksamhetsanalyser\\ad hoc-rapportering\\*Demo-AdhocReportingQueries.sql*.
2. Se till att du är ansluten till den **adhocreporting** databas.
3. Välj den **fråga** menyn och klickar på **innehåller faktiska Körningsplan**
4. Markera den *vilka platser är för närvarande registrerad?* fråge- och tryck på **F5**.

   Frågan returnerar listan över hela platsen, som illustrerar hur snabbt och enkelt det är att köra frågor över alla klienter och returnera data från varje klient.

   Granska planen och se att för hela kostnaden är fjärransluten frågan eftersom vi bara kommer att varje klientdatabas och att välja platsen information.

   ![Välj * från dbo. Platser](media/saas-multitenantdb-adhoc-reporting/query1-plan.png)

5. Välj nästa fråga och tryck på **F5**.

   Den här frågan ansluter till data från klientdatabaser och lokalt *VenueTypes* tabell (local, eftersom det är en tabell den *adhocreporting* databas).

   Inspektera planen och se att flesta kostnaden är fjärransluten frågan eftersom vi frågar om varje klients plats information (dbo. Platser) och gör en snabb lokalt join med lokalt *VenueTypes* tabell för att visa det egna namnet.

   ![Gå med på distans och lokalt data](media/saas-multitenantdb-adhoc-reporting/query2-plan.png)

6. Välj nu den *vilken dag mest biljetter såldes?* fråge- och tryck på **F5**.

   Den här frågan har lite mer komplexa ansluter till och aggregering. Vad är viktigt att notera är att de flesta av bearbetningen sker externt och återigen, vi tar tillbaka endast de rader som vi behöver, returnerar en enskild rad för varje lokal sammanställd biljett Försäljning antal per dag.

   ![DocumentDB](media/saas-multitenantdb-adhoc-reporting/query3-plan.png)


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]

> * Köra distribuerade frågor över alla klientdatabaser
> * Distribuera en ad hoc-rapportdatabasen och Lägg till schema till att köra distribuerade frågor.

Prova den [Klientanalys självstudien](saas-multitenantdb-tenant-analytics.md) att utforska extrahering data till en separat analysdatabas för mer komplexa analysbehandling.

## <a name="additional-resources"></a>Ytterligare resurser

<!-- ??
* Additional [tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->

* [Elastic Query](sql-database-elastic-query-overview.md)
