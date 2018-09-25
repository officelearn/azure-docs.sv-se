---
title: Kör reporting frågor över flera Azure SQL-databaser | Microsoft Docs
description: Flera klienter, rapportering med hjälp av distribuerade frågor.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewers: billgib,AyoOlubeko
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 3bba0eb552338f1b436ea25306a84029d352f1f3
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47055296"
---
# <a name="cross-tenant-reporting-using-distributed-queries"></a>Flera klienter, rapportering med hjälp av distribuerade frågor

I den här självstudien får köra du distribuerade frågor över hela uppsättningen klient databaser för rapportering. De här frågorna kan extrahera insikter som legat dolda i daglig användningsdata för Wingtip biljetter SaaS-klienter. Om du vill göra detta måste du distribuera en ytterligare rapportdatabasen till katalogservern och Använd elastisk fråga för att aktivera distribuerade frågor.


I den här guiden lär du dig:

> [!div class="checklist"]

> * Så här distribuerar du en reporting-databas
> * Hur du kan köra distribuerade frågor över alla klientdatabaser
> * Hur globala vyer i varje databas kan aktivera effektiva frågor över klienter


Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:


* Wingtip biljetter SaaS databas Per klient-appen har distribuerats. Om du vill distribuera i mindre än fem minuter [distribuera och utforska Wingtip biljetter SaaS databas Per klient-programmet](saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell ska ha installerats. Mer information finns i [Komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* SQL Server Management Studio (SSMS) är installerad. Om du vill hämta och installera SSMS, se [ladda ned SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="cross-tenant-reporting-pattern"></a>Reporting mönster för flera klienter

![mönster för distribuerade frågor för flera klienter](media/saas-tenancy-cross-tenant-reporting/cross-tenant-distributed-query.png)

En affärsmöjlighet med SaaS-program är att använda de stora mängder av klientdata lagras i molnet och få insikter om användningen av ditt program. Dessa insikter hjälper funktionsutveckling, förbättringar av användbarhet och andra investeringar i dina appar och tjänster.

Det är lätt att komma åt en enkel databas med flera klienter, men inte så enkelt när du har distribuerat tusentals databaser. En metod är att använda [elastisk fråga](sql-database-elastic-query-overview.md), vilket gör att fråga över en distribuerad uppsättning databaser med ett gemensamt schema. Dessa databaser kan distribueras i olika resursgrupper och prenumerationer, men behöver dela en gemensam inloggning. Elastic Query använder en enda *head* databasen där definieras externa tabeller som speglar tabeller eller vyer i distribuerade (klient)-databaser. Frågorna som skickas till huvuddatabasen kompileras för att skapa en distribuerad frågeplan, och delar av frågan skickas ned till klientdatabaserna efter behov. Elastic Query använder shardkartan i katalogdatabasen för att fastställa platsen för alla klientdatabaser. Konfiguration och frågor på head-databasen är enkla med standarden [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference), och stöd för frågor från verktyg som Power BI och Excel.

Genom att distribuera frågor över klientdatabaser, ger elastisk fråga omedelbar insikt i live produktionsdata. När elastisk fråga hämtar data från potentiellt många databaser, kan frågesvarstiden vara högre än motsvarande frågor som skickas till en enda databas för flera innehavare. Utforma frågor för att minimera den information som returneras till head-databasen. Elastisk fråga är ofta bäst för att fråga små mängder realtidsdata, till skillnad från att skapa vanliga eller komplexa analytics-frågor eller rapporter. Om frågorna inte god, titta på den [Körningsplan](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) att se vilken del av frågan flyttas fram till fjärrdatabasen och hur mycket data returneras. Frågor som kräver komplex aggregering eller analytisk behandling kan vara bättre hanterar genom att extrahera klientdata till en databasen eller datalagret som är optimerade för analysfrågor. Det här mönstret förklaras i den [klientanalys](saas-tenancy-tenant-analytics.md). 

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Hämta programskript Wingtip biljetter SaaS databas Per klient

Databas för flera klienter i Wingtip biljetter SaaS-skript och programmets källkod finns tillgängliga i den [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-lagringsplatsen. Kolla in den [allmänna riktlinjer](saas-tenancy-wingtip-app-guidance-tips.md) steg att ladda ned och avblockera Wingtip biljetter SaaS-skript.

## <a name="create-ticket-sales-data"></a>Skapa biljett försäljningsdata

Skapa biljett försäljningsdata genom att köra biljett-generatorn för att köra frågor mot ett mer intressant data.

1. I den *PowerShell ISE*öppnar den... \\Inlärningsmoduler\\Verksamhetsanalyser\\ad hoc-rapportering\\*Demo-AdhocReporting.ps1* skript och ange följande värde:
   * **$DemoScenario** = 1, **köp biljetter för evenemang på alla platser**.
2. Tryck på **F5** att köra skriptet och generera biljettförsäljningar. När skriptet körs, fortsätter du stegen i den här självstudien. Biljett-data som efterfrågas i det *köra ad hoc distribuerade frågor* avsnittet, så vänta tills biljett-generator att slutföra.

## <a name="explore-the-global-views"></a>Utforska de globala vyerna

I programmet Wingtip biljetter SaaS databas Per klient får varje klient en databas. Därför är data i databastabellerna begränsad till en enda klient perspektiv. När frågor över alla databaser, är det dock viktigt att elastisk fråga kan hantera data som om det är en del av en enskild logisk databas shardade av klient. 

För att simulera det här mönstret, en uppsättning ”global” vyer läggs till klientdatabasen projektet ett klient-ID till var och en av de tabeller som efterfrågas globalt. Till exempel den *VenueEvents* visa lägger till en beräknad *VenueId* till kolumner projected från den *händelser* tabell. På samma sätt kan den *VenueTicketPurchases* och *VenueTickets* vyer lägga till en beräknad *VenueId* kolumnen projected från deras respektive tabell. Dessa vyer som används av elastisk fråga för att parallellisera frågor och push dem till lämplig fjärranslutna klienten databasen när en *VenueId* kolumnen finns. Detta minskar avsevärt mängden data som returneras och resulterar i en väsentlig ökning prestanda för många frågor. De här globala vyer har skapats i förväg i alla klientdatabaser.

1. Öppna SSMS och [Anslut till servern tenants1 -&lt;användaren&gt; server](saas-tenancy-wingtip-app-guidance-tips.md#explore-database-schema-and-execute-sql-queries-using-ssms).
1. Expandera **databaser**, högerklicka på _contosoconcerthall_, och välj **ny fråga**.
1. Kör följande frågor för att utforska skillnaden mellan de enda klient-tabellerna och de globala vyerna:

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

I dessa vyer i *VenueId* beräknas som en hash av namnet på plats, men alla metoden kan användas för att introducera ett unikt värde. Den här metoden är ungefär samma sätt som klientnyckeln beräknas för användning i katalogen.

Att granska definitionen av den *platser* vy:

1. I **Object Explorer**, expandera **contosoconcerthall** > **vyer**:

   ![visningar](media/saas-tenancy-cross-tenant-reporting/views.png)

2. Högerklicka på **dbo. Platser**.
3. Välj **skript vyn som** > **skapar till** > **nya Query Editor-fönstret**

Skriptet någon av de andra *jurisdiktionsort* vyer för att se hur de lägger till den *VenueId*.

## <a name="deploy-the-database-used-for-distributed-queries"></a>Distribuera databasen som används för distribuerade frågor

Den här övningen distribueras den _adhocreporting_ databas. Det här är head databasen som innehåller ett schema som används för att fråga över alla klientdatabaser. Databasen har distribuerats till den befintliga katalogservern, vilket är den server som används för alla datorhanteringsrelaterade databaser i exempelappen.

1. i *PowerShell ISE*öppnar... \\Inlärningsmoduler\\Verksamhetsanalyser\\ad hoc-rapportering\\*Demo-AdhocReporting.ps1*. 

1. Ange **$DemoScenario = 2**, _distribuera adhoc-rapportdatabasen_.

1. Tryck på **F5** att köra skriptet och skapa den *adhocreporting* databas.

I nästa avsnitt du till schemat till databasen så att den kan användas för att köra distribuerade frågor.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Konfigurera ”huvud”-databasen för att köra distribuerade frågor

Den här övningen lägger till schemat (extern datakälla och extern tabelldefinitioner) till den _adhocreporting_ databasen för att aktivera fråga över alla klientdatabaser.

1. Öppna SQL Server Management Studio och Anslut till ad hoc-rapportering databasen du skapade i föregående steg. Namnet på databasen är *adhocreporting*.
2. Öppna ...\Learning Modules\Operational Analytics\Adhoc Reporting\ _initiera AdhocReportingDB.sql_ i SSMS.
3. Granska SQL-skript och Observera:

   Elastic Query använder en databas-omfattande autentisering för att få åtkomst till varje klient. Den här autentiseringsuppgiften måste vara tillgängliga i alla databaser och bör normalt beviljas de minsta rättigheterna krävas för att aktivera de här frågorna.

    ![Skapa autentiseringsuppgifter](media/saas-tenancy-cross-tenant-reporting/create-credential.png)

   Med katalogdatabasen som den externa datakällan distribueras till alla databaser som har registrerats i katalogen när frågan körs. Eftersom servernamn är olika för varje distribution, det här skriptet hämtar platsen för katalogdatabasen från den aktuella servern (@@servername) där skriptet körs.

    ![Skapa extern datakälla](media/saas-tenancy-cross-tenant-reporting/create-external-data-source.png)

   De externa tabeller som refererar till de globala vyerna som beskrivs i föregående avsnitt och definierats med **DISTRIBUTION = SHARDED(VenueId)**. Eftersom varje *VenueId* mappas till en enskild databas detta förbättrar prestanda för många scenarier som du ser i nästa avsnitt.

    ![Skapa externa tabeller](media/saas-tenancy-cross-tenant-reporting/external-tables.png)

   Den lokala tabellen _VenueTypes_ som skapas och fylls i. Denna referenstabell data är vanligt i alla klientdatabaser, så att den kan visas här som en lokal tabell och fylls i automatiskt med de vanliga data. För vissa frågor minska med den här tabellen definieras i head databasen mängden data som behöver flyttas till head-databasen.

    ![Skapa tabell](media/saas-tenancy-cross-tenant-reporting/create-table.png)

   Om du inkluderar referenstabeller i det här sättet måste du uppdatera tabellschemat och data när du uppdaterar klientdatabaserna.

4. Tryck på **F5** att köra skriptet och initiera den *adhocreporting* databas. 

Du kan nu köra distribuerade frågor och samla information över alla klienter!

## <a name="run-distributed-queries"></a>Köra distribuerade frågor

Nu när den *adhocreporting* databasen är ställer in, gå vidare och köra några distribuerade frågor. Inkludera Körningsplan för en bättre förståelse för där frågebearbetningen sker. 

Vid kontroll körningsplanen, hovrar du över plan ikoner för information. 

Viktigt att notera, är den här inställningen **DISTRIBUTION = SHARDED(VenueId)** när den externa datakällan har definierats förbättrar prestandan för många scenarier. Eftersom varje *VenueId* mappas till en enskild databas enkelt görs via en fjärranslutning, returnerar bara de data som krävs.

1. Öppna... \\Inlärningsmoduler\\Verksamhetsanalyser\\ad hoc-rapportering\\*Demo-AdhocReportingQueries.sql* i SSMS.
2. Se till att du är ansluten till den **adhocreporting** databas.
3. Välj den **fråga** menyn och klickar på **innehåller faktiska Körningsplan**
4. Markera den *vilka platser är för närvarande registrerad?* fråge- och tryck på **F5**.

   Frågan returnerar listan över hela platsen, som illustrerar hur snabbt och enkelt det är att köra frågor över alla klienter och returnera data från varje klient.

   Inspektera planen och att hela kostnaden finns i den fjärranslutna frågan. Varje klientdatabas kör frågan via en fjärranslutning och returnerar platsen informationen till head-databasen.

   ![Välj * från dbo. Platser](media/saas-tenancy-cross-tenant-reporting/query1-plan.png)

5. Välj nästa fråga och tryck på **F5**.

   Den här frågan ansluter till data från klientdatabaser och lokalt *VenueTypes* tabell (local, eftersom det är en tabell den *adhocreporting* databas).

   Granska planen och se att flesta kostnaden är fjärransluten frågan. Varje klientdatabas returnerar platsen information och skapar en lokal koppling med lokalt *VenueTypes* tabell för att visa det egna namnet.

   ![Gå med på distans och lokalt data](media/saas-tenancy-cross-tenant-reporting/query2-plan.png)

6. Välj nu den *vilken dag mest biljetter såldes?* fråge- och tryck på **F5**.

   Den här frågan har lite mer komplexa ansluter till och aggregering. Största delen av bearbetningen sker via en fjärranslutning.  Enkel rader, som innehåller varje lokal antal per dag biljett försäljning per dag, returneras bara till head-databasen.

   ![DocumentDB](media/saas-tenancy-cross-tenant-reporting/query3-plan.png)


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]

> * Köra distribuerade frågor över alla klientdatabaser
> * Distribuera en rapportdatabasen och definiera schemat som krävs för att köra distribuerade frågor.


Prova den [Klientanalys självstudien](saas-tenancy-tenant-analytics.md) att utforska extrahering data till en separat analysdatabas för mer komplexa analysbehandling.

## <a name="additional-resources"></a>Ytterligare resurser

* Ytterligare [självstudier som bygger på programmet Wingtip biljetter SaaS databas Per klient](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastic Query](sql-database-elastic-query-overview.md)
