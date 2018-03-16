---
title: Azure SQL-databas Azure Fallstudie - Umbraco | Microsoft Docs
description: "Lär dig mer om hur Umbraco använder SQL-databas för att snabbt etablera och skala tjänster för tusentals hyresgäster i molnet"
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: article
ms.date: 01/10/2017
ms.author: carlrab
ms.openlocfilehash: af59c22279eeee4b3e5d699da4c98094df815c1f
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="umbraco-uses-azure-sql-database-to-quickly-provision-and-scale-services-for-thousands-of-tenants-in-the-cloud"></a>Umbraco använder Azure SQL Database till snabbt etablera och skala för tusentals hyresgäster i molnet
![Umbraco Logo](./media/sql-database-implementation-umbraco/umbracologo.png)

Umbraco är ett populärt öppen källkod innehållshantering system (CMS) som kan utföra allt från små kampanj eller broschyr platser för komplexa program för Fortune 500 företag och globala media webbplatser. 

> ”Har vi helt en stor community med utvecklare som använder systemet, med mer än 100 000 utvecklare på våra forum och mer än 350 000 platser som är aktiv, kör Umbraco”.
> 
> — Morten Christensen, Technical Lead, Umbraco
> 
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-Case-Study-Umbraco/player]
> 
> 

För att förenkla kunddistributioner, Umbraco läggs Umbraco-as-a-Service (UaaS): ett erbjudande för programvara som en-tjänst (SaaS) som eliminerar behovet av lokala distributioner innehåller inbyggda skalning och tar bort management omkostnader genom att aktivera utvecklare kan fokusera på produkten innovation i stället för lösningen. Umbraco kan ge dessa fördelar med hjälp av den flexibla plattform som en tjänst (PaaS) modellen som erbjuds av Microsoft Azure.

UaaS SaaS ger kunder möjlighet att använda Umbraco CMS-funktioner som tidigare fanns utanför deras räckvidden. Dessa kunder etableras med en fungerande CMS-miljö som innehåller en produktionsdatabas. Kunder kan lägga till upp till två nya databaser för utveckling och fristående miljöer, beroende på deras behov. När en ny miljö krävs tilldelas en automatiserad process kunden en databas automatiskt. Den nya databasen är klar i sekunder, eftersom databasen har redan etablerats före av Umbraco från ett Azure elastisk pool med tillgängliga databaser (se bild 1).

![Umbraco etablering livscykel](./media/sql-database-implementation-umbraco/figure1.png)

Bild 1. Etablering livscykel för Umbraco som en tjänst (UaaS)

## <a name="azure-elastic-pools-and-automation-simplify-deployments"></a>Azure elastiska pooler och automatisering förenkla distributioner
Med Azure SQL Database och andra Azure-tjänster, Umbraco kunder kan själv etablera sina miljöer och Umbraco enkelt kan övervaka och hantera databaser som en del av ett intuitivt arbetsflöde:

1. Etablera
   
   Umbraco upprätthåller en kapacitet på 200 företablerad tillgängliga databaser från elastiska pooler. När en ny kund registrerar sig för UaaS, ger Umbraco kunden med en ny CMS-miljö i nära realtid genom att tilldela dem en databas från poolen tillgänglighet.
   
   När en pool med tillgänglighet når sin tröskeln, skapas en ny elastisk pool och nya databaser är företablerad tilldelas kunder efter behov.
   
   Implementering automatiserad helt med C# bibliotek och Azure Service Bus-köer.
2. Använda
   
   Kunder kan du använda en till tre miljöer (för produktion, mellanlagring och/eller utveckling), varje med en egen databas. Kunddatabaserna är i elastiska pooler, vilket gör att Umbraco att tillhandahålla effektiv skalning utan att behöva etablera över.
   
   ![Översikt över Umbraco-projekt](./media/sql-database-implementation-umbraco/figure2.png)
   
   ![Umbraco projektinformation](./media/sql-database-implementation-umbraco/figure3.png)
   
   Figur 2. Umbraco-as-a-Service (UaaS) kunden webbplats, visar en översikt över projekt och information
   
   Azure SQL Database använder Database Transaction Units (Dtu) för att representera relativa styrka krävs för verkliga databastransaktioner. Databaser fungerar normalt med cirka 10 dtu: er för UaaS kunder, men varje har elasticitet för att skala på begäran. Det innebär att UaaS kan se till att kunderna alltid har nödvändiga resurser, även under tider med låg belastning. Exempelvis under en senaste söndag kväll sport händelse en UaaS erfarna kunddatabasen toppar som pekar åt upp till 100 dtu: er under spelet. Azure elastiska pooler är det möjligt för Umbraco att stödja det höga behovet utan prestandaförsämring.
3. Övervaka
   
   Övervakare för Umbraco databas aktiviteten med instrumentpaneler i Azure-portalen, tillsammans med anpassade e-postaviseringar.
4. Haveriberedskap
   
   Azure tillhandahåller två alternativ för katastrofåterställning (DR): aktiv geo-replikering och geo-återställning. DR-alternativ som företaget bör välja beror på dess [kontinuitet för företag mål](sql-database-business-continuity.md).
   
   aktiv geo-replikering ger det snabbaste svar vid avbrott. Du kan skapa upp till fyra läsbara sekundärservrar på servrar i olika regioner aktiv geo-replikering och du kan sedan starta redundans för sekundära vid fel.
   
   Umbraco kräver inte geo-replikering, men den dra nytta av Azure geo-återställning för att garantera minsta avbrottstid vid ett avbrott. GEO-återställning är beroende av säkerhetskopiorna av databasen i geo-redundant Azure-lagring. Gör att användarna kan återställa från en säkerhetskopia när det finns ett avbrott i den primära regionen.
5. Avinstallation etablera
   
   När en miljö med projektet tas bort tas alla associerade databaser (utveckling, mellanlagring och live) bort under rensning av Azure Service Bus-kö. Den här automatiserad process återställer oanvända databaserna Umbracos elastisk databas tillgänglighet poolen, gör dem tillgängliga för framtida etablering samtidigt maximal användning.

## <a name="elastic-pools-allow-uaas-to-scale-with-ease"></a>Elastiska pooler Tillåt UaaS att skala med enkel
Genom att utnyttja Azure elastiska pooler, optimera Umbraco prestanda för sina kunder utan att behöva över- eller under etablera. För närvarande har Umbraco nästan 3 000 databaser över 19 elastiska pooler med möjlighet att enkelt skala som behövs för att hantera några av sina befintliga 325,000 kunder eller nya kunder som är redo att distribuera en CMS i molnet.

I själva verket enligt Morten Christensen, tekniska leda till Umbraco, har ”UaaS nu tillväxt ca 30 nya kunder per dag. Våra kunder är glädje tjänsten med hjälp av att kunna etablera nya projekt i sekunder, publicera uppdateringar direkt till deras live platser från en utvecklingsmiljö med ett klick-distribution och göra ändringar precis som snabbt om de hitta fel ”.

Om en kund inte kräver en andra och tredje miljö längre, den helt enkelt ta bort dessa miljöer. Som frigör resurser som kan användas för andra kunder som en del av poolen Umbraco elastisk databas tillgänglighet.

![Arkitektur för Umbraco-distribution](./media/sql-database-implementation-umbraco/figure4.png)

Bild 3. Arkitektur för distribution av UaaS i Microsoft Azure

## <a name="the-path-from-datacenter-to-cloud"></a>Sökvägen från datacentret till molnet
När utvecklare Umbraco ursprungligen bestämt dig för att flytta till en SaaS-modell, känner de att de behöver en kostnadseffektiv och skalbar sätt att bygga ut tjänsten.

> ”elastiska pooler är ett perfekt val för våra SaaS-erbjudande eftersom vi kan reglerar kapacitet upp eller ner efter behov. Det är enkelt att etablera, och med vår installationen vi hålla användning maximala ”.
> 
> — Morten Christensen, Technical Lead, Umbraco
> 
> 

”Vi ville ägna tid på att lösa kundernas problem, inte hanterar infrastrukturen. Vi vill göra det lättare för kunderna att få ut största möjliga värde ”säger Niels Hartvig, grundare av Umbraco. ”Det anses vara ursprungligen värd för servrarna oss själva, men kapacitetsplanering skulle ha varit utmaning”. Tillfälligtvis, använder Umbraco inte av alla databasadministratörer som understreck en nyckel/värde-lösning för att använda UaaS.

Ett viktigt mål för Umbraco utvecklare har att erbjuda ett sätt för UaaS kunder att etablera miljöer snabbt och utan kapacitetsbegränsningar. Men att tillhandahålla en dedikerad värdbaserad tjänst i Umbraco Datacenter skulle ha krävt mängd outnyttjad kapacitet att hantera belastning i bearbetningen. Som skulle ha tänkt att lägga till betydande beräkningsinfrastrukturen som skulle ha varit regelbundet underutnyttjade.

Dessutom Utvecklingsteamet Umbraco ville ha en lösning som skulle låta dem återanvända så mycket av den befintliga koden som möjligt. Som Umbraco developer tillstånd Mikkel Madsen, ”vi är glada med Microsoft-utvecklingsverktyg som vi har redan känner till, t.ex. Microsoft SQL Server, Microsoft Azure SQL Database, ASP.net och Internet Information Services (IIS). Innan du investerar i en IaaS eller en PaaS molnet lösningen Vi ville se till att det skulle stöder våra Microsoft-verktyg och plattformar, så att vi kan göra omfattande ändringar i vår kod grundläggande ”.

För att uppfylla alla kriterierna sökt Umbraco efter en partner för molnet med följande kvalifikationer:

* Tillräcklig kapacitet och tillförlitlighet
* Stöd för Microsoft-utvecklingsverktyg, så att Umbraco engineers inte tvingas helt tanken utvecklarmiljön
* Förekomst i alla geografiska marknader där UaaS konkurrerar (företag behöver för att se till att de kan komma åt sina data snabbt och att deras data lagras på en plats som uppfyller de regionala regelkrav)

## <a name="why-umbraco-chose-azure-for-uaas"></a>Varför Umbraco valde Azure för UaaS
Enligt Morten Christensen ”efter att alla våra alternativ vi valt Azure eftersom den har uppfyllt alla våra villkor, från hantering och skalbarhet till förtrogenhet och kostnadseffektivitet. Vi konfigurerar miljöer på virtuella Azure-datorer och alla miljöer har sin egen Azure SQL Database-instans med alla instanser i elastiska pooler. Genom att skilja databaser mellan utveckling, mellanlagring och live miljöer vi erbjuder våra kunder robust prestandaisolering matchade skala – enorma win ”.

Morten fortsätter ”innan vi var tvungna att etablera servrar för webbdatabaser manuellt. Nu ska behöver vi göra det. Allt är automatisk, från etablering till rensning ”.

Morten är också Grattis med skalning funktionerna i Azure. ”elastiska pooler är ett perfekt val för våra SaaS-erbjudande eftersom vi kan reglerar kapacitet upp eller ner efter behov. Det är enkelt att etablera, och med vår installationen vi hålla användning maximala ”. Morten tillstånd, ”enkelhet för elastiska pooler, tillsammans med försäkran för service-nivå-baserade dtu: er ger oss möjlighet att etablera nya resurspooler på begäran. En av våra större kunder topp nyligen till 100 dtu: er i live-miljö. Vår elastiska pooler som med Azure kundens databaser med de resurser som de behövs i realtid utan att förutsäga DTU krav. Kort sagt, våra kunder få aktivera runt tiden de förväntar sig och vi kan uppfylla våra prestanda servicenivåavtal ”.

Mikkel Madsen summeras i den: ”Vi har tagit kraftfulla Azure algoritmen som ansluter SaaS ovanligt (onboarding nya kunder i realtid i skala) till vår programmönster (före etablering av databaser, både utveckling och live) på den underliggande tekniken (med Azure Service Bus-köer tillsammans med Azure SQL Database)”.

## <a name="with-azure-uaas-is-exceeding-customer-expectations"></a>Med Azure överstiger UaaS kundens förväntningar
Sedan väljer Azure som sin partner i molnet, har Umbraco kunnat erbjuda UaaS kunder optimal prestanda för innehållshantering, utan investeringar IT-resurs från en egen värdbaserade lösning. När Morten som säger ”vi älskar enklare utveckling och skalbarhet som Azure ger oss och våra kunder är mycket förtjusta i funktioner och tillförlitlighet. Generellt sett har en vinner på oss ”!

## <a name="more-information"></a>Mer information
* Läs mer om Azure elastiska pooler i [elastiska pooler](sql-database-elastic-pool.md).
* Läs mer om Azure Service Bus i [Azure Service Bus](https://azure.microsoft.com/services/service-bus/).
* Mer information om virtuella nätverk finns [virtuella nätverk](https://azure.microsoft.com/documentation/services/virtual-network/).    
* Mer information om säkerhetskopiering och återställning finns [företagskontinuitet](sql-database-business-continuity.md).    
* Mer information om hur du övervakar ppols finns [övervakning pooler](sql-database-elastic-pool-manage-portal.md).    
* Läs mer om Umbraco som en tjänst i [Umbraco](https://umbraco.com/cloud).

