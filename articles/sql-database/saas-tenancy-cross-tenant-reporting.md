---
title: Rapportera frågor i flera databaser
description: Rapportering mellan klienter med distribuerade frågor.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewers: billgib,ayolubek
ms.date: 01/25/2019
ms.openlocfilehash: c863946934df9990c14e49ef1a0a82bbc55b27c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822087"
---
# <a name="cross-tenant-reporting-using-distributed-queries"></a>Rapportering mellan innehavare med distribuerade frågor

I den här självstudien kör du distribuerade frågor över hela uppsättningen klientdatabaser för rapportering. Dessa frågor kan extrahera insikter begravda i den dagliga operativa data för Wingtip Biljetter SaaS hyresgäster. För att göra detta distribuerar du ytterligare en rapportdatabas till katalogservern och använder Elastisk fråga för att aktivera distribuerade frågor.


I den här guiden lär du dig:

> [!div class="checklist"]
> 
> * Distribuera en rapportdatabas
> * Så här kör du distribuerade frågor i alla klientdatabaser
> * Hur globala vyer i varje databas kan möjliggöra effektiv frågor mellan klienter


Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:


* Den Wingtip Biljetter SaaS Databas per klient app distribueras. Information om hur du distribuerar på mindre än fem minuter finns i [Distribuera och utforska Programmet Wingtip Tickets SaaS Database Per Tenant](saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* SQL Server Management Studio (SSMS) är installerat. Mer hÃ¤r hämtning och installation av SSMS finns i [HÃ¤m fÃ¤r HÃ¤mtningsstudio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="cross-tenant-reporting-pattern"></a>Rapporteringsmönster över klienter

![Distribuerat frågemönster för flera innehavare](media/saas-tenancy-cross-tenant-reporting/cross-tenant-distributed-query.png)

En affärsmöjlighet med SaaS-program är att använda den stora mängden klientdata som lagras i molnet för att få insikter om hur ditt program fungerar och används. Dessa insikter kan vägleda funktionsutveckling, förbättringar av användbarheten och andra investeringar i dina appar och tjänster.

Det är lätt att komma åt en enkel databas med flera klienter, men inte så enkelt när du har distribuerat tusentals databaser. En metod är att använda [Elastisk fråga](sql-database-elastic-query-overview.md), vilket möjliggör frågor över en distribuerad uppsättning databaser med gemensamt schema. Dessa databaser kan distribueras över olika resursgrupper och prenumerationer, men måste dela en gemensam inloggning. Elastisk fråga använder en databas med ett *enda huvud* där externa tabeller definieras som speglar tabeller eller vyer i de distribuerade (klientdatabaserna). Frågorna som skickas till huvuddatabasen kompileras för att skapa en distribuerad frågeplan, och delar av frågan skickas ned till klientdatabaserna efter behov. Elastisk fråga använder fragmentmappningen i katalogdatabasen för att fastställa platsen för alla klientdatabaser. Installation och fråga för huvuddatabasen är enkla med [standardTransact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)och stöder frågor från verktyg som Power BI och Excel.

Genom att distribuera frågor över klientdatabaserna ger Elastic Query omedelbar inblick i liveproduktionsdata. När Elastisk fråga hämtar data från potentiellt många databaser kan frågefördröjningen vara högre än motsvarande frågor som skickas till en enda databas med flera innehavare. Utforma frågor för att minimera data som returneras till huvuddatabasen. Elastisk fråga är ofta bäst lämpad för att fråga små mängder realtidsdata, i motsats till att skapa ofta använda eller komplexa analysfrågor eller rapporter. Om frågor inte fungerar bra kan du titta på [körningsplanen](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) för att se vilken del av frågan som skickas ned till fjärrdatabasen och hur mycket data som returneras. Frågor som kräver komplex aggregering eller analytisk bearbetning kan vara bättre hanterar genom att extrahera klientdata till en databas eller ett informationslager som är optimerat för analysfrågor. Det här mönstret förklaras i [självstudien för klientanalys](saas-tenancy-tenant-analytics.md). 

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Hämta Wingtip Tickets SaaS Database Per Tenant-programskript

Wingtip-biljetterna SaaS Multi-tenant Database skript och program källkod finns i [WingtipTicketsSSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub repo. Kolla in den [allmänna vägledningen](saas-tenancy-wingtip-app-guidance-tips.md) för steg för att ladda ner och låsa upp Wingtip Tickets SaaS skript.

## <a name="create-ticket-sales-data"></a>Skapa biljettförsäljningsdata

Om du vill köra frågor mot en mer intressant datauppsättning skapar du biljettförsäljningsdata genom att köra biljettgeneratorn.

1. I *PowerShell ISE*, öppna ... \\Utbildningsmoduler\\Operational\\Analytics Adhoc Reporting\\*Demo-AdhocReporting.ps1-skript* och ange följande värde:
   * **$DemoScenario** = 1, **Köp biljetter till evenemang på alla arenor.**
2. Tryck på **F5** för att köra skriptet och generera biljettförsäljning. Medan skriptet körs fortsätter du stegen i den här självstudien. Biljettdata efterfrågas i avsnittet *Kör ad hoc-distribuerade frågor,* så vänta tills biljettgeneratorn har slutförts.

## <a name="explore-the-global-views"></a>Utforska de globala vyerna

I Wingtip Tickets SaaS Database Per Tenant-programmet får varje klient en databas. Data som finns i databastabellerna begränsas således till perspektivet för en enda klient. När du frågar över alla databaser är det dock viktigt att Elastic Query kan behandla data som om de är en del av en enda logisk databas som är fragmenterad av klienten. 

För att simulera det här mönstret läggs en uppsättning "globala" vyer till i klientdatabasen som projicerar ett klient-ID i var och en av tabellerna som efterfrågas globalt. Vyn *VenueEvents* lägger till exempel till en beräknad *VenueId* i kolumnerna som projiceras från tabellen *Händelser.* På samma sätt lägger *vyerna VenueTicketPurchases* och *VenueTickets* till en beräknad *VenueId-kolumn* som projiceras från deras respektive tabeller. Dessa vyer används av Elastic Query för att parallellisera frågor och skicka dem till lämplig fjärrklientdatabas när en *VenueId-kolumn* finns. Detta minskar dramatiskt mängden data som returneras och resulterar i en betydande ökning av prestanda för många frågor. Dessa globala vyer har skapats i föra skapats i alla klientdatabaser.

1. Öppna SSMS och [anslut till&lt;&gt; klienten1- USER-servern](saas-tenancy-wingtip-app-guidance-tips.md#explore-database-schema-and-execute-sql-queries-using-ssms).
1. Expandera **databaser,** högerklicka på _contosoconcerthall_och välj **Ny fråga**.
1. Kör följande frågor för att utforska skillnaden mellan tabellerna med en klient och de globala vyerna:

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

I dessa vyer beräknas *VenueId* som ett hash-värde för platsnamnet, men alla tillvägagångssätt kan användas för att introducera ett unikt värde. Den här metoden liknar hur klientnyckeln beräknas för användning i katalogen.

Så här undersöker du definitionen av *platsvyn:*

1. Expandera **contosoconcerthall** > **Views**i **Objektutforskaren:**

   ![Visningar](media/saas-tenancy-cross-tenant-reporting/views.png)

2. Högerklicka **på dbo. Arenor**.
3. Välj **skriptvy som** > **fönstret SKAPA till** > **ny frågeredigerare**

Skripta någon av de andra *platsvyerna* för att se hur de lägger till *VenueId*.

## <a name="deploy-the-database-used-for-distributed-queries"></a>Distribuera databasen som används för distribuerade frågor

Den här övningen distribuerar _databasen för adorapportering._ Det här är huvuddatabasen som innehåller schemat som används för att fråga över alla klientdatabaser. Databasen distribueras till den befintliga katalogservern, som är den server som används för alla hanteringsrelaterade databaser i exempelappen.

1. i *PowerShell ISE*, öppna ... \\Utbildningsmoduler\\Operativa Analytics\\\\Adhoc Rapportering*Demo-AdhocReporting.ps1*. 

1. Ange **$DemoScenario = 2**, _Distribuera ad hoc-rapportdatabas_.

1. Tryck på **F5** för att köra skriptet och skapa *databasen för adhocreporting.*

I nästa avsnitt lägger du till schema i databasen så att den kan användas för att köra distribuerade frågor.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Konfigurera huvuddatabasen för att köra distribuerade frågor

Den här övningen lägger till schema (externa datakällor och externa tabelldefinitioner) i _adhocreporting-databasen_ för att möjliggöra frågor i alla klientdatabaser.

1. Öppna SQL Server Management Studio och anslut till Adhoc Reporting-databasen som du skapade i föregående steg. Namnet på databasen är *adhocreporting*.
2. Öppna ...\Utbildningsmoduler\Operativa analyser\Adhoc Reporting\ _Initialize-AdhocReportingDB.sql_ i SSMS.
3. Granska SQL-skriptet och anmärkning:

   Elastisk fråga använder en databasomfattad autentiseringsuppgifter för att komma åt var och en av klientdatabaserna. Den här autentiseringsinformationen måste vara tillgänglig i alla databaser och bör normalt beviljas de minimirättigheter som krävs för att aktivera dessa frågor.

    ![skapa autentiseringsuppgift](media/saas-tenancy-cross-tenant-reporting/create-credential.png)

   Med katalogdatabasen som extern datakälla distribueras frågor till alla databaser som registrerats i katalogen när frågan körs. Eftersom servernamn skiljer sig åt för varje distribution hämtar skriptet platsen@servernameför katalogdatabasen från den aktuella servern (@ ) där skriptet körs.

    ![skapa extern datakälla](media/saas-tenancy-cross-tenant-reporting/create-external-data-source.png)

   De externa tabeller som refererar till de globala vyer som beskrivs i föregående avsnitt och som definieras med **DISTRIBUTION = SHARDED(VenueId)**. Eftersom varje *VenueId* mappar till en enskild databas förbättrar detta prestanda för många scenarier som visas i nästa avsnitt.

    ![skapa externa tabeller](media/saas-tenancy-cross-tenant-reporting/external-tables.png)

   Den lokala tabellen _VenueTypes_ som skapas och fylls i. Den här referensdatatabellen är vanlig i alla klientdatabaser, så den kan representeras här som en lokal tabell och fyllas med vanliga data. För vissa frågor kan den här tabellen definieras i huvuddatabasen minska mängden data som måste flyttas till huvuddatabasen.

    ![skapa tabell](media/saas-tenancy-cross-tenant-reporting/create-table.png)

   Om du inkluderar referenstabeller på det här sättet måste du uppdatera tabellschemat och data när du uppdaterar klientdatabaserna.

4. Tryck på **F5** för att köra skriptet och initiera databasen för *adorapportering.* 

Nu kan du köra distribuerade frågor och samla in insikter över alla klienter!

## <a name="run-distributed-queries"></a>Kör distribuerade frågor

Nu när *adhocreporting-databasen* har konfigurerats, gå vidare och kör några distribuerade frågor. Inkludera körningsplanen för en bättre förståelse av var frågebearbetningen sker. 

När du inspekterar körningsplanen håller du muspekaren över planikonerna för mer information. 

Viktigt att notera är att inställning **AV DISTRIBUTION = SHARDED(VenueId)** när den externa datakällan har definierats förbättrar prestanda för många scenarier. När varje *VenueId* mappar till en enskild databas sker filtrering enkelt på distans och returnerar endast de data som behövs.

1. Öppna... \\Utbildningsmoduler\\Operational\\Analytics Adhoc Reporting\\*Demo-AdhocReportingQueries.sql* i SSMS.
2. Kontrollera att du är ansluten till **databasen för adhocreporting.**
3. Välj **frågemenyn** och klicka på **Inkludera faktisk körningsplan**
4. Markera frågan Vilka platser som för **F5**närvarande *är registrerade?*

   Frågan returnerar hela platslistan, vilket illustrerar hur snabbt och enkelt det är att fråga över alla klienter och returnera data från varje klient.

   Kontrollera planen och se till att hela kostnaden finns i fjärrfrågan. Varje klientdatabas kör frågan på distans och returnerar dess platsinformation till huvuddatabasen.

   ![VÄLJ * FRÅN DBO. Platser](media/saas-tenancy-cross-tenant-reporting/query1-plan.png)

5. Markera nästa fråga och tryck på **F5**.

   Den här frågan kopplar data från klientdatabaserna och den lokala *venueTypes-tabellen* (lokal, eftersom det är en tabell i *adhocreporting-databasen).*

   Kontrollera planen och se till att den största kostnaden är fjärrfrågan. Varje klientdatabas returnerar sin platsinformation och utför en lokal koppling till den lokala *venueTypes-tabellen* för att visa det egna namnet.

   ![Gå med på fjärrdata och lokala data](media/saas-tenancy-cross-tenant-reporting/query2-plan.png)

6. Välj nu frågan På vilken dag var de **F5** *mest sålda biljetterna?*

   Den här frågan gör lite mer komplex sammanfogning och aggregering. Den mesta bearbetningen sker på distans.  Endast enstaka rader, som innehåller varje plats dagliga biljettförsäljning räknas per dag, returneras till huvuddatabasen.

   ![DocumentDB](media/saas-tenancy-cross-tenant-reporting/query3-plan.png)


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> 
> * Köra distribuerade frågor över alla klientdatabaser
> * Distribuera en rapportdatabas och definiera det schema som krävs för att köra distribuerade frågor.


Prova nu [självstudien för Tenant Analytics](saas-tenancy-tenant-analytics.md) för att undersöka extrahera data till en separat analysdatabas för mer komplex analysbearbetning.

## <a name="additional-resources"></a>Ytterligare resurser

* Ytterligare [självstudier som bygger på Wingtip Biljetter SaaS Database Per Tenant ansökan](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastic Query](sql-database-elastic-query-overview.md)
