---
title: "Designmönster för SaaS-program med flera klienter och Azure SQL Database | Microsoft Docs"
description: "Den här artikeln beskrivs kraven och vanliga arkitektur datamönster för flera innehavare databasen program som körs i en molnmiljö behöver tänka på och olika kompromisser som associeras med dessa mönster. Här förklaras också hur Azure SQL Database med elastiska pooler och elastiska verktyg hjälpa uppfylla dessa krav på ett sätt utan kompromisser."
keywords: 
services: sql-database
documentationcenter: 
author: srinia
manager: jhubbard
editor: 
ms.assetid: 1dd20c6b-ddbb-40ef-ad34-609d398d008a
ms.service: sql-database
ms.custom: scale out apps
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-design
ms.date: 02/01/2017
ms.author: srinia
ms.openlocfilehash: 0f6ba62a01f3211ccaae6b6c48f72e0de54aad78
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="design-patterns-for-multi-tenant-saas-applications-and-azure-sql-database"></a>Designmönster för SaaS-program med flera klienter och Azure SQL Database
I den här artikeln du kan lära dig om kraven och gemensamma data arkitektur mönster för flera innehavare programvara som en tjänst (SaaS) databasprogram som körs i en molnmiljö. Det beskrivs även faktorer som du behöver tänka på och avvägningarna av olika designmönster. Elastiska pooler och elastiska verktyg i Azure SQL Database kan hjälpa dig enligt dina särskilda behov utan att kompromissa med andra mål.

Utvecklare kan ibland göra alternativ som fungerar mot sina långsiktiga intressen när de skapar innehavare modeller för datanivåer av program med flera klienter. Inledningsvis på minst kan en utvecklare uppfattar enkel utveckling och lägre cloud service provider kostnader som är viktigare än klientisolering eller skalbarhet i ett program. Det här alternativet kan leda till kundernas tillfredsställande problem och kostsamma kursen-korrigering senare.

Ett program med flera innehavare är ett program som finns i en molnmiljö och som ger samma uppsättning hundratals eller tusentals klienter som inte delar eller se varandras data-tjänster. Ett exempel är en SaaS-program som tillhandahåller tjänster till klienter i en miljö med värd i molnet.

## <a name="multi-tenant-applications"></a>Program med flera klienter
I program med flera klienter, kan data och arbetsbelastningen enkelt partitioneras. Du kan partitionera data och arbetsbelastning, till exempel längs klient gränser, eftersom de flesta förfrågningar sker inom en klient. Denna egenskap är inbyggd i data och arbetsbelastningen och den prioriterar de mönster för programmet som beskrivs i den här artikeln.

Utvecklare kan du använda den här typen av program över hela spektrum av molnbaserade program, inklusive:

* Partner databasprogram som överförs till molnet som SaaS-program
* SaaS-program som skapats för molnet från grunden
* Direkt, kund-riktade program
* Medarbetare enterprise program

SaaS-program som är utformade för molnet och de med rötter finns som partner databasprogram normalt program med flera klienter. Dessa SaaS-program som ger ett särskilda program som en tjänst till sina klienter. Klienter kan komma åt tjänsten och ägas associerade data som lagras som en del av programmet. Men om du vill dra nytta av fördelarna med SaaS måste klienter överlämnande viss kontroll över sina egna data. De litar på SaaS-leverantör för att hålla sina data säkert och isolerade från andra klienter data. Exempel på den här typen av flera innehavare SaaS-program är MYOB, SnelStart och Salesforce.com. Var och en av dessa program kan partitioneras längs klient gränser och stöd för programmet designmönster diskuterar vi i den här artikeln.

Program som tillhandahåller en tjänst som direkt till kunder eller till anställda inom en organisation (kallas ofta användare i stället för klienter) är en annan kategori på flera innehavare programmet spektrumet. Kunder som prenumererar på tjänsten och äger inte de data som tjänstleverantör samlar in och lagrar. Leverantörer har mindre strikta krav att skydda sina kunders data isoleras från varandra utöver government obligatorisk sekretessbestämmelser. Exempel på den här typen av program som ansluter till en kund med flera innehavare är media innehållsleverantörer som Netflix, Spotify och Xbox LIVE. Andra exempel på enkelt partitionable program är kund-riktade, Internet-skala program eller Sakernas Internet (IoT)-program i vilken varje kund eller enheten kan fungera som en partition. Partitionsgränserna avgränsa användare och enheter.

Inte alla program partition enkelt längs en egenskap som innehavare, kunder, användare eller enhet. En komplex företagsresurser (ERP) programmet har till exempel produkter, order och kunder. Det har vanligtvis ett sammansatt schema med tusentals hög sammankopplade tabeller.

Ingen enskild partition strategi kan tillämpas på alla tabeller och fungerar över arbetsbelastningen för ett program. Den här artikeln fokuserar på program med flera klienter som har enkelt partitionable data och arbetsbelastningar.

## <a name="multi-tenant-application-design-trade-offs"></a>Flera innehavare program design avvägningarna
Designmönstret som en flera innehavare programutvecklaren väljer vanligtvis baserat på en faktor på följande faktorer:

* **Klientisolering**. Utvecklaren måste se till att ingen klient har oönskad åtkomst till data med andra klienter. Krav för isolering sträcker sig till andra egenskaper, som ger skydd från störningar grannar, att kunna återställa data för en klient och implementera klient-specifika anpassningar.
* **Molnet resurskostnader**. Ett SaaS-program måste vara konkurrenskraftig kostnaden. Flera innehavare programmet utvecklare kan välja att optimera för lägre kostnader för användning av molnresurser, till exempel lagring och beräkna kostnader.
* **Enkel DevOps**. En flera innehavare programutvecklaren måste innehålla isolering skydd, underhålla, och övervaka deras program- och databasschemat och felsökning av problem med klient. Komplexitet för utveckling och drift kan direktöversättas till ökade kostnader och utmaningar med klienten tillfredsställande.
* **Skalbarhet**. Möjligheten att stegvis lägga till flera klienter och kapacitet för klienter som behöver det är absolut nödvändigt för en lyckad SaaS-åtgärd.

Dessa faktorer har avvägningarna jämfört med en annan. Lägsta kostnad molnet erbjuder inte kanske kan erbjuda den enklaste utvecklingsarbetet. Det är viktigt för utvecklare för att fatta välgrundade beslut om de här alternativen och deras avvägningarna under designprocessen för programmet.

Ett mönster för populära utveckling är att packa flera klienter i en eller några databaser. Fördelar med den här metoden är en lägre kostnad eftersom du betalar för några databaser och enkelhet arbetar med ett begränsat antal databaser. Men över tiden, kommer en SaaS flera innehavare programutvecklaren inser att detta val har betydande downsides i klientisolering och skalbarhet. Om klientisolering blir viktigt krävs extra arbete att skydda klientdata i den delade lagringen från obehörig åtkomst och störningar grannar. Den här extra arbete kan avsevärt öka utvecklingsarbete och isolering underhållskostnaderna. På liknande sätt, om det inte krävs för att lägga till klienter, det här designmönstret kräver normalt expertis för att distribuera flyttas klientdata över databaser att korrekt skala datanivå för ett program.  

Klientisolering ofta är ett grundläggande krav i SaaS flera innehavare program som tillgodoser företag och organisationer. En utvecklare kan frestas av upplevd fördelar i enkelhet och kostnaden jämfört med klientisolering och skalbarhet. Kompromissen kan vara komplicerat och dyrt när tjänsten växer och krav för isolering av innehavare blir mer viktiga och hanterade på programnivån. I program med flera klienter som tillhandahåller en tjänst direkt, konsumentinriktade till kunder, men vara klientisolering lägre prioritet än Optimera för resurskostnader för molnet.

## <a name="multi-tenant-data-models"></a>Flera innehavare datamodeller
Vanliga design praxis för att placera klientdata följer tre olika modeller som illustreras i bild 1.

![flera innehavare appens datamodeller](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-multi-tenant-data-models.png)

Bild 1: Vanliga design praxis för flera innehavare datamodeller

* **Databasen per klient**. Varje innehavare har en egen databas. Alla klient-specifika data är begränsad till klientens databas och isoleras från andra klienter och deras data.
* **Delade databasen delat**. Flera innehavare dela någon av flera databaser. En specifik uppsättning klienter tilldelas varje databas med hjälp av en partitioneringsstrategi som hash, intervall eller lista partitionering. Den här strategin för distribution av data kallas ofta horisontell partitionering.
* **Delad enskild databas**. En enda ibland stora databas innehåller data för alla klienter som är skiljas åt i en klient-ID-kolumn.

> [!NOTE]
> En programutvecklare kan välja att placera olika klienter i en annan databas scheman och sedan använda schemanamnet att undvika tvetydigheten olika klienter. Vi rekommenderar inte den här metoden eftersom det kräver normalt användningen av dynamisk SQL och den kan inte vara effektiva i planen cachelagring. I resten av den här artikeln fokuserar vi på delat register-metoden för den här kategorin för flera innehavare program.
> 
> 

## <a name="popular-multi-tenant-data-models"></a>Populära datamodeller flera innehavare
Det är viktigt att utvärdera de olika typerna av flera innehavare datamodeller vad gäller program design avvägningarna vi redan har identifierats. Dessa faktorer hjälpa karakteriserar de tre vanligaste flera innehavare datamodeller ovan och deras databasanvändningen som visas i bild 2.

* **Isolering**. Grad av isolering mellan klienter kan vara ett mått på hur mycket klientisolering uppnår en datamodell.
* **Molnet resurskostnader**. Mängden resursdelning mellan klienter kan optimera resurskostnader för molnet. En resurs kan definieras som beräkning och lagring.
* **DevOps kostnaden**. Enkel programutveckling, distribution och hantering minskar totalkostnaden för SaaS-åtgärden.  

I bild 2 visar Y-axeln nivå av klientisolering. X-axeln visar andelen resursdelning. Grå diagonal pil i mitten anger riktningen på DevOps kostnader, tenderar att öka eller minska.

![Designmönster för populära program för flera innehavare](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-popular-application-patterns.png)

Bild 2: Populära flera innehavare datamodeller

Den nedre högra kvadranten i bild 2 visar ett programmönster som använder en potentiellt stora delad enskild databas, och den delade tabellen (eller separat schema) metod. Det är bra för resursdelning eftersom alla klienter använder samma databasresurser (processor, minne, i/o) i en enskild databas. Men klientisolering är begränsad. Du kan behöva vidta ytterligare åtgärder för att skydda klienter från varandra i program-lagret. Dessa ytterligare steg kan avsevärt öka DevOps kostnaden för utveckling och hantering av programmet. Skalbarhet är begränsad av skalan av den maskinvara som värd för databasen.

Den nedre vänstra kvadranten i bild 2 visar flera innehavare delat över flera databaser (vanligtvis olika typer av maskinvara skalningsenheter). Varje databas är värd för en delmängd av klienter, vilket löser skalbarhet problem av andra mönster. Du kan enkelt placera hyresgäster på nya databaser som allokerats till ny maskinvara skalenheter om det krävs mer kapacitet för flera innehavare. Men minskar mängden resursdelning. Endast klienter som placeras på samma skalenheter dela resurser. Den här metoden ger liten förbättring för klientisolering, eftersom många hyresgäster fortfarande är samordnad utan skyddas automatiskt från varandras åtgärder. Programmet komplexitet förblir hög.

Den övre vänstra kvadranten i bild 2 är den tredje metoden. Varje klient data placeras i en egen databas. Den här metoden har bra klientisolering egenskaper men begränsar resursdelning när varje databas har sin egen dedicerade resurser. Den här metoden är bra om alla klienter har förutsägbar arbetsbelastningar. Om klienternas arbetsbelastningar blir mindre förutsägbar inte kan providern optimera resursdelning. Unpredictability är vanligt för SaaS-program. Providern måste antingen över etablera uppfylla krav eller lägre resurser. Antingen åtgärden resulterar i högre kostnader eller lägre klient tillfredsställande sätt. En högre grad av resursdelning över klienter blir önskvärt att göra det mer kostnadseffektivt lösningen. Att öka antalet databaser som ökar även DevOps kostnaden för att distribuera och underhålla programmet. Den här metoden ger bästa och enklaste isolering för innehavare trots dessa problem.

Dessa faktorer påverkar också designmönstret en kund väljer:

* **Ägare till klientdata**. Ett program där klienter Behåll ägarskapet av sina egna data ökar mönstret för en enskild databas per klient.
* **Skalning**. Ett program som riktar sig till hundratals tusentals eller miljontals hyresgäster prioriterar Databasdelning metoder, till exempel horisontell partitionering. Isoleringskrav för kan fortfarande medföra utmaningar.
* **Värde och företag modellen**. Om ett program per klientorganisation intäkter om klokt att små (mindre än dollar), isoleringskrav blir mindre viktigt och en delad databas. Om intäkter per klient är några kronor eller mer, är en databas per klient modell mer möjligt. Det kan minska utvecklingskostnaderna för.

Design avvägningarna visas i figur 2 får måste en perfekt modell för flera klienter innehålla bra klient isolering egenskaper med optimala resursdelning mellan klienter. Den här modellen passar i kategorin som beskrivs i den övre högra kvadranten på bild 2.

## <a name="multi-tenancy-support-in-azure-sql-database"></a>Stöd för flera innehavare i Azure SQL Database
Azure SQL Database stöder alla program med flera innehavare mönster som beskrivs i bild 2. Det stöder också ett programmönster som kombinerar bra resursdelning med elastiska pooler och isolering fördelarna med databasen per klient närmar sig (se den övre högra kvadranten i bild 3). Elastisk Databasverktyg och funktioner i SQL-databas kan du minska kostnaden för att utveckla ett program som har många databaser (visas i det skuggade området i bild 3). Dessa verktyg kan hjälpa dig att skapa och hantera program som använder någon av flera databasen mönster.

![Mönster i Azure SQL-databas](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-patterns-sqldb.png)

Bild 3: Flera innehavare programmet mönster i Azure SQL Database

## <a name="database-per-tenant-model-with-elastic-pools-and-tools"></a>Databasen per klient modellen med elastiska pooler och verktyg
Elastiska pooler i SQL-databas kombinera klientisolering med resursdelning mellan klient-databaser som fungerar bättre med metoden databas per klient. SQL-databasen är en lösning för nivån för SaaS-providers som skapar program med flera klienter. För resursdelning mellan klienter flyttas från Applikationsnivån i databasen service-lagret. Komplexiteten för att hantera och skicka en fråga i större skala över databaser förenklas med elastiska jobb, elastisk frågan, elastisk transaktioner och klientbibliotek för elastisk databas.

| Programvarukrav | Funktioner för SQL-databas |
| --- | --- |
| Klientisolering och resursdelning |[Elastiska pooler](sql-database-elastic-pool.md): allokerar en pool av resurser för SQL-databas och dela resurser mellan olika databaser. Dessutom kan enskilda databaser Rita så mycket resurser från poolen som behövs för att hantera kapacitet efterfrågan ökar kraftigt och tillfälligt på grund av ändringar i klienternas arbetsbelastningar. Den elastiska poolen själva kan skalas upp eller ned efter behov. Elastiska pooler ger även enkel hantering och övervakning och felsökning på pool-nivå. |
| Enkel DevOps över databaser |[Elastiska pooler](sql-database-elastic-pool.md): som tidigare nämnts. |
| | [Elastisk frågan](sql-database-elastic-query-horizontal-partitioning.md): frågan över databaser för rapportering eller mellan klient analys. |
| | [Elastiska jobb](sql-database-elastic-jobs-overview.md): Paketera och distribuera på ett tillförlitligt sätt databasunderhåll eller databasen schemaändringar till flera databaser. |
| | [Elastisk transaktioner](sql-database-elastic-transactions-overview.md): ändras till flera databaser i ett atomiskt och isolerade sätt. Elastisk transaktioner behövs när program behöver ”allt eller inget” garantier över flera databasåtgärder. |
| | [Klientbibliotek för elastisk databas](sql-database-elastic-database-client-library.md): hantera distributioner av data och klienter till för databaser. |

## <a name="shared-models"></a>Delade modeller
Enligt beskrivningen tidigare, för de flesta SaaS-providers, kan en delad modell-metod medföra problem med klient isolering problem och svårigheter med programutveckling och underhåll. Dock för flera innehavare program som tillhandahåller en tjänst direkt till konsumenter, kanske krav för isolering av innehavare inte med hög prioritet som minimerar kostnaden. De kan eventuellt pack klienter i en eller flera databaser i en hög densitet att minska kostnaderna. Delade databasen modeller med hjälp av en enskild databas eller flera delat databaser kan resultera i ytterligare effektivitet i resurskostnader för fildelning och övergripande. Azure SQL Database tillhandahåller vissa funktioner som hjälper kunder att bygga isolering för ökad säkerhet och hantering i större skala på datanivå.

| Programvarukrav | Funktioner för SQL-databas |
| --- | --- |
| Säkerhetsfunktioner för isolering |[Säkerhet på radnivå](https://msdn.microsoft.com/library/dn765131.aspx) |
| [Databasschemat](https://msdn.microsoft.com/library/dd207005.aspx) | |
| Enkel DevOps över databaser |[Elastisk fråga](sql-database-elastic-query-horizontal-partitioning.md) |
| | [Elastiska jobb](sql-database-elastic-jobs-overview.md) |
| | [Elastisk transaktioner](sql-database-elastic-transactions-overview.md) |
| | [Klientbibliotek för elastiska databaser](sql-database-elastic-database-client-library.md) |
| | [Elastisk databas dela och slå samman](sql-database-elastic-scale-overview-split-and-merge.md) |

## <a name="summary"></a>Sammanfattning
Krav för isolering av innehavare är viktiga för de flesta SaaS flera innehavare program. Det bästa alternativet att isolera leans kraftigt mot databasen per klient-metoden. De två metoderna kräver investeringar i lager för komplexa program som kräver skicklig development personal för att tillhandahålla isolering, vilket ökar avsevärt kostnader och risker. Om isoleringskrav inte är klara för tidigt i tjänsten utveckling, kan vara onlineåterställningspunkter dem ännu mer kostsamma i de första två modellerna. Huvudsakliga nackdelarna som är kopplade till databas per klient modellen är relaterade till ökad molnet resurskostnader på grund av minskade delning och underhåll och hantering av flera databaser. SaaS-programutvecklare har ofta svårt när de gör dessa avvägningarna.

Även om avvägningarna kan vara större hinder med de flesta molntjänstleverantörer för databasen, eliminerar Azure SQL Database barriärer med elastisk pool och funktioner för elastisk databas. SaaS-utvecklare kan kombinera isolering egenskaperna för en databas per klient-modell och optimera resursdelning och förbättringar av många databaser med elastiska pooler och associerade verktyg.

Flera innehavare programleverantörer som har inga krav för isolering av innehavare och som kan packa klienter i en databas på en hög densitet kanske upptäcker att delade data modeller resultatet i ytterligare effektivitet för resursdelning och minska kostnaderna. Azure SQL Database elastisk Databasverktyg, horisontell partitionering bibliotek och säkerhetsfunktioner hjälpa SaaS-providers bygga och hantera program med flera klienter.

## <a name="next-steps"></a>Nästa steg
[Kom igång med elastiska Databasverktyg](sql-database-elastic-scale-get-started.md) med en exempelapp som visar klientbiblioteket.

Skapa en [elastisk pool anpassade instrumentpanel för SaaS](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) med en exempelapp som använder elastiska pooler för en kostnadseffektiv och skalbar databaslösning.

Använda Azure SQL Database-verktyg för att [migrera befintliga databaser att skala ut](sql-database-elastic-convert-to-use-elastic-tools.md).

För att skapa en elastisk pool med Azure-portalen, se [skapa en elastisk pool](sql-database-elastic-pool-manage-portal.md).  

Lär dig hur du [övervaka och hantera en elastisk pool](sql-database-elastic-pool-manage-portal.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Distribuera och utforska ett program med flera innehavare som använder Azure SQL Database - Wingtip SaaS](sql-database-saas-tutorial.md)
* [Vad är en Azure elastisk pool?](sql-database-elastic-pool.md)
* [Skala ut med Azure SQL Database](sql-database-elastic-scale-introduction.md)
* [program med flera klienter med elastiska Databasverktyg och säkerhet på radnivå](sql-database-elastic-tools-multi-tenant-row-level-security.md)
* [Autentisering i flera innehavare appar med hjälp av Azure Active Directory och OpenID Connect](../guidance/guidance-multitenant-identity-authenticate.md)
* [Tailspin Surveys-programmet](../guidance/guidance-multitenant-identity-tailspin.md)


## <a name="questions-and-feature-requests"></a>Frågor och funktionsbegäranden

Om du har frågor kan du hitta oss på den [SQL Database-forum](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted). Lägg till en funktionsbegäran i den [SQL-databas Feedbackforum](https://feedback.azure.com/forums/217321-sql-database/).

