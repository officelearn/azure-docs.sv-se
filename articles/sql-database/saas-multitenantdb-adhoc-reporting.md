---
title: Ad hoc-rapporteringsfrågor i flera databaser
description: Kör ad hoc-rapporteringsfrågor i flera SQL-databaser i ett appexempel för flera innehavare.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: craigg
ms.reviewer: sstein
ms.date: 10/30/2018
ms.openlocfilehash: c0d1829c52041446b4feb43d8af262265e2680fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822173"
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-azure-sql-databases"></a>Köra ad hoc-analysfrågor i flera Azure SQL-databaser

I den här självstudien kör du distribuerade frågor över hela uppsättningen klientdatabaser för att aktivera interaktiv ad hoc-rapportering. Dessa frågor kan extrahera insikter begravda i den dagliga operativa data i Wingtip Biljetter SaaS app. Om du vill göra dessa extraheringar distribuerar du ytterligare en analysdatabas till katalogservern och använder Elastisk fråga för att aktivera distribuerade frågor.


I den här guiden lär du dig:

> [!div class="checklist"]
> 
> * Distribuera en ad hoc-rapportdatabas
> * Så här kör du distribuerade frågor i alla klientdatabaser


Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Wingtip-biljetterna SaaS Databasapp för flera innehavare distribueras. Information om hur du distribuerar på mindre än fem minuter finns i [Distribuera och utforska Wingtip Tickets SaaS-databasprogrammet för flera innehavare](saas-multitenantdb-get-started-deploy.md)
* Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* SQL Server Management Studio (SSMS) är installerat. Mer hÃ¤r hämtning och installation av SSMS finns i [HÃ¤m fÃ¤r HÃ¤mtningsstudio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="ad-hoc-reporting-pattern"></a>Ad hoc-rapporteringsmönster

![adhoc rapporteringsmönster](media/saas-multitenantdb-adhoc-reporting/adhocreportingpattern_shardedmultitenantDB.png)

SaaS-program kan analysera den stora mängden klientdata som lagras centralt i molnet. Analyserna visar insikter om hur ditt program fungerar och används. Dessa insikter kan vägleda funktionsutveckling, förbättringar av användbarheten och andra investeringar i dina appar och tjänster.

Det är lätt att komma åt en enkel databas med flera klienter, men inte så enkelt när du har distribuerat tusentals databaser. En metod är att använda [Elastisk fråga](sql-database-elastic-query-overview.md), vilket möjliggör frågor över en distribuerad uppsättning databaser med gemensamt schema. Dessa databaser kan distribueras över olika resursgrupper och prenumerationer. Men en vanlig inloggning måste ha tillgång till extrahera data från alla databaser. Elastisk fråga använder en databas med ett *enda huvud* där externa tabeller definieras som speglar tabeller eller vyer i de distribuerade (klientdatabaserna). Frågorna som skickas till huvuddatabasen kompileras för att skapa en distribuerad frågeplan, och delar av frågan skickas ned till klientdatabaserna efter behov. Elastisk fråga använder fragmentmappningen i katalogdatabasen för att fastställa platsen för alla klientdatabaser. Installationsprogrammet och frågan är enkla med [standardtransakt-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)och stöder ad hoc-frågor från verktyg som Power BI och Excel.

Genom att distribuera frågor över klientdatabaserna ger Elastic Query omedelbar inblick i liveproduktionsdata. Men eftersom Elastisk fråga hämtar data från potentiellt många databaser kan frågefördröjningen ibland vara högre än för motsvarande frågor som skickas till en enda databas med flera innehavare. Var noga med att utforma frågor för att minimera de data som returneras. Elastisk fråga är ofta bäst lämpad för att fråga små mängder realtidsdata, i motsats till att skapa ofta använda eller komplexa analysfrågor eller rapporter. Om frågor inte fungerar bra kan du titta på [körningsplanen](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) för att se vilken del av frågan som har flyttats ned till fjärrdatabasen. Och bedöma hur mycket data som returneras. Frågor som kräver komplex analytisk bearbetning kan vara bättre betjänt genom att spara extraherade klientdata i en databas som är optimerad för analysfrågor. SQL Database och SQL Data Warehouse kan vara värd för en sådan analysdatabas.

Det här mönstret för analys förklaras i [självstudien](saas-multitenantdb-tenant-analytics.md)för klientanalys .

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Hämta källkoden och skripten för Wingtip-biljetterna SaaS Multi-tenant Database

Wingtip-biljetterna SaaS-databasskript för flera innehavare och programkällakod finns i [WingtipTicketsSAaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub-repo. Kolla in den [allmänna vägledningen](saas-tenancy-wingtip-app-guidance-tips.md) för steg för att ladda ner och låsa upp Wingtip Tickets SaaS skript.

## <a name="create-ticket-sales-data"></a>Skapa biljettförsäljningsdata

Om du vill köra frågor mot en mer intressant datauppsättning skapar du biljettförsäljningsdata genom att köra biljettgeneratorn.

1. I *PowerShell ISE*, öppna ... \\Utbildningsmoduler\\Operational\\Analytics Adhoc Reporting\\*Demo-AdhocReporting.ps1-skript* och ange följande värden:
   * **$DemoScenario** = 1, **Köp biljetter till evenemang på alla arenor.**
2. Tryck på **F5** för att köra skriptet och generera biljettförsäljning. Medan skriptet körs fortsätter du stegen i den här självstudien. Biljettdata efterfrågas i avsnittet *Kör ad hoc-distribuerade frågor,* så vänta tills biljettgeneratorn har slutförts.

## <a name="explore-the-tenant-tables"></a>Utforska klienttabellerna 

I Wingtip Tickets SaaS Multi-tenant Database-programmet lagras klienter i en hybridklienthanteringsmodell - där klientdata antingen lagras i en databas med flera innehavare eller en enda klientdatabas och kan flyttas mellan de två. När du frågar över alla klientdatabaser är det viktigt att Elastic Query kan behandla data som om de är en del av en enda logisk databas som är fragmenterad av klienten. 

För att uppnå det här mönstret innehåller alla klienttabeller en *VenueId-kolumn* som identifierar vilken klient data tillhör. *VenueId* beräknas som ett hash-värde för platsnamnet, men alla tillvägagångssätt kan användas för att introducera ett unikt värde för den här kolumnen. Den här metoden liknar hur klientnyckeln beräknas för användning i katalogen. Tabeller som innehåller *VenueId* används av Elastic Query för att parallellisera frågor och skicka dem till lämplig fjärrklientdatabas. Detta minskar dramatiskt mängden data som returneras och resulterar i en ökning av prestanda, särskilt när det finns flera klienter vars data lagras i enskilda klientdatabaser.

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>Distribuera databasen som används för ad hoc-distribuerade frågor

Den här övningen distribuerar *databasen för adorapportering.* Det här är huvuddatabasen som innehåller schemat som används för att fråga över alla klientdatabaser. Databasen distribueras till den befintliga katalogservern, som är den server som används för alla hanteringsrelaterade databaser i exempelappen.

1. Öppna... \\Learning\\Modules\\Operational Analytics\\Adhoc Reporting*Demo-AdhocReporting.ps1* i *PowerShell ISE* och ange följande värden:
   * **$DemoScenario** = 2, **Distribuera ad hoc-analysdatabas**.

2. Tryck på **F5** för att köra skriptet och skapa *databasen för adhocreporting.*

I nästa avsnitt lägger du till schema i databasen så att den kan användas för att köra distribuerade frågor.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>Konfigurera huvuddatabasen för att köra distribuerade frågor

Den här övningen lägger till schema (externa datakällor och externa tabelldefinitioner) i ad hoc-rapportdatabasen som gör det möjligt att fråga över alla klientdatabaser.

1. Öppna SQL Server Management Studio och anslut till Adhoc-rapportdatabasen som du skapade i föregående steg. Namnet på databasen är *adhocreporting*.
2. Öppna ...\Utbildningsmoduler\Operativa analyser\Adhoc Reporting\ *Initialize-AdhocReportingDB.sql* i SSMS.
3. Granska SQL-skriptet och notera följande:

   Elastisk fråga använder en databasomfattad autentiseringsuppgifter för att komma åt var och en av klientdatabaserna. Den här autentiseringsinformationen måste vara tillgänglig i alla databaser och bör normalt beviljas de minimirättigheter som krävs för att dessa ad hoc-frågor ska kunna aktiveras.

    ![skapa autentiseringsuppgift](media/saas-multitenantdb-adhoc-reporting/create-credential.png)

   Genom att använda katalogdatabasen som extern datakälla distribueras frågor till alla databaser som registreras i katalogen när frågan körs. Eftersom servernamn är olika för varje distribution hämtar det här initieringsskriptet platsen@servernameför katalogdatabasen genom att hämta den aktuella servern (@ ) där skriptet körs.

    ![skapa extern datakälla](media/saas-multitenantdb-adhoc-reporting/create-external-data-source.png)

   De externa tabeller som refererar till klienttabeller definieras med **DISTRIBUTION = SHARDED(VenueId)**. Detta dirigerar en fråga för en viss *VenueId* till lämplig databas och förbättrar prestanda för många scenarier som visas i nästa avsnitt.

    ![skapa externa tabeller](media/saas-multitenantdb-adhoc-reporting/external-tables.png)

   Den lokala tabellen *VenueTypes* som skapas och fylls i. Den här referensdatatabellen är vanlig i alla klientdatabaser, så den kan representeras här som en lokal tabell och fyllas med vanliga data. För vissa frågor kan detta minska mängden data som flyttas mellan klientdatabaserna och *adhocreporting-databasen.*

    ![skapa tabell](media/saas-multitenantdb-adhoc-reporting/create-table.png)

   Om du inkluderar referenstabeller på det här sättet måste du uppdatera tabellschemat och data när du uppdaterar klientdatabaserna.

4. Tryck på **F5** för att köra skriptet och initiera databasen för *adorapportering.* 

Nu kan du köra distribuerade frågor och samla in insikter över alla klienter!

## <a name="run-ad-hoc-distributed-queries"></a>Köra ad hoc-distribuerade frågor

Nu när *adhocreporting-databasen* har konfigurerats, gå vidare och kör några distribuerade frågor. Inkludera körningsplanen för en bättre förståelse av var frågebearbetningen sker. 

När du inspekterar körningsplanen håller du muspekaren över planikonerna för mer information. 

1. I *SSMS*, öppna ... \\Utbildningsmoduler\\Operational\\Analytics Adhoc Reporting\\*Demo-AdhocReportingQueries.sql*.
2. Kontrollera att du är ansluten till **databasen för adhocreporting.**
3. Välj **frågemenyn** och klicka på **Inkludera faktisk körningsplan**
4. Markera frågan Vilka platser som för **F5**närvarande *är registrerade?*

   Frågan returnerar hela platslistan, vilket illustrerar hur snabbt och enkelt det är att fråga över alla klienter och returnera data från varje klient.

   Kontrollera planen och se till att hela kostnaden är fjärrfrågan eftersom vi helt enkelt går till varje klientdatabas och väljer platsinformation.

   ![VÄLJ * FRÅN DBO. Platser](media/saas-multitenantdb-adhoc-reporting/query1-plan.png)

5. Markera nästa fråga och tryck på **F5**.

   Den här frågan kopplar data från klientdatabaserna och den lokala *venueTypes-tabellen* (lokal, eftersom det är en tabell i *adhocreporting-databasen).*

   Kontrollera planen och se till att majoriteten av kostnaden är fjärrfrågan eftersom vi frågar varje klientlokalsinformation (dbo. Arenor) och gör sedan en snabb lokal koppling till den lokala *venuetypes-tabellen* för att visa det egna namnet.

   ![Gå med på fjärrdata och lokala data](media/saas-multitenantdb-adhoc-reporting/query2-plan.png)

6. Välj nu frågan På vilken dag var de **F5** *mest sålda biljetterna?*

   Den här frågan gör lite mer komplex sammanfogning och aggregering. Vad som är viktigt att notera är att det mesta av behandlingen sker på distans, och återigen tar vi tillbaka bara de rader vi behöver, returnerar bara en enda rad för varje plats sammanlagda biljettförsäljning räknas per dag.

   ![DocumentDB](media/saas-multitenantdb-adhoc-reporting/query3-plan.png)


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> 
> * Köra distribuerade frågor över alla klientdatabaser
> * Distribuera en ad hoc-rapportdatabas och lägga till schema i den för att köra distribuerade frågor.

Prova nu [självstudien för Tenant Analytics](saas-multitenantdb-tenant-analytics.md) för att undersöka extrahera data till en separat analysdatabas för mer komplex analysbearbetning.

## <a name="additional-resources"></a>Ytterligare resurser

<!-- ??
* Additional [tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->

* [Elastic Query](sql-database-elastic-query-overview.md)
