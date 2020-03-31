---
title: Lagra, hantera och spara programdata i molnet med Visual Studio App Center och Azure-tjänster
description: Lär dig mer om tjänster som Visual Studio App Center som gör att du kan lagra, hantera och spara mobilprogramdata i molnet.
author: codemillmatt
ms.assetid: 12344321-0123-4678-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 166847325fa9094136f1c2a143f1751420f05f66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240916"
---
# <a name="store-sync-and-query-mobile-application-data-from-the-cloud"></a>Lagra, synkronisera och fråga mobilprogramdata från molnet
Oavsett vilken typ av program du bygger, kommer du sannolikt generera och bearbeta data. Programmets användare har höga förväntningar. De vill att programmet ska fungera snabbt och sömlöst, under alla omständigheter. De flesta program fungerar också på flera enheter. Du kan komma åt ditt program från en stationär eller mobil enhet. Flera användare kan använda programmet samtidigt och dela data med förväntningar om att få omedelbar och realtidsåtkomst till data.

Dina programanvändare har inte alltid internetanslutning. Program är utformade och förväntas fungera med eller utan internetuppkoppling. Utvecklare måste välja rätt lösning för att lagra och synkronisera sina data till molnet för att ge en bra kundupplevelse för sitt program.

Microsoft tillhandahåller en mängd olika tjänster som eliminerar behovet av att snurra upp servrar, välja din databas eller oroa dig för skala eller säkerhet för att ge så rik upplevelse som möjligt. Dessa tjänster ger en bra utvecklarupplevelse som gör att du kan lagra programdata i molnet med hjälp av SQL- eller NoSQL API:er. Du kan också synkronisera data på alla enheter och göra det möjligt för programmet att fungera med eller utan nätverksanslutning för att skapa skalbara och robusta program.

Använd följande tjänster för att hantera och lagra data från mobila program i molnet.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Data](/appcenter/data/) är en datahanteringstjänst. Det gör det möjligt för program att hantera, bevara och synkronisera programdata i molnet på olika enheter och plattformar i online- och offlinescenarier. App Center Data bygger på Azure Cosmos DB när användarbasen och antalet program växer. Det säkerställer låg latens, hög tillgänglighet och hög skalbarhet för alla dina data.

**Huvudfunktioner**
- Enkel etablering till en ny Azure Cosmos DB-databas eller en befintlig Azure Cosmos DB-databas från Visual Studio App Center-portalen.
- NoSQL-databasstöd för att enkelt lagra, synkronisera och fråga programdata.
- Den här tjänsten bygger på Azure Cosmos DB och ärver de flesta av de viktigaste funktionerna som erbjuds av Azure Cosmos DB och kan skalas globalt för att uppfylla dina affärsbehov.
- Synkroniseringsfunktioner online och offline för att synkronisera data mellan enheter.
- Mobila klient-SDK:er som du kan använda för att enkelt hantera privata programdata.
- Plattformsstöd för iOS, Android, Xamarin och React Native.

**Referenser**
- [Registrera dig med Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Komma igång med App Center-data](/appcenter/data/getting-started)

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) är en globalt distribuerad databastjänst med flera modeller. Du kan använda den för att skapa program för planetskala. Med Azure Cosmos DB kan du elastiskt och självständigt skala dataflöde och lagring över valfritt antal Azure-regioner över hela världen. Du kan dra nytta av snabb, ensiffrig millisekund dataåtkomst med hjälp av dina favorit-API-ytor. Dessa ytor inkluderar SQL, MongoDB, Cassandra, Bord eller Gremlin. Azure Cosmos DB tillhandahåller unikt omfattande servicenivåavtal (SLA) för dataflöde, svarstid, tillgänglighet och konsekvens.

**Huvudfunktioner**
- Stöder ett brett utbud av API: er, som inkluderar SQL (Core) API, Cassandra API, MongoDB API, Gremlin API och Tabell API.
- Nyckelfärdig global distribution replikerar dina data var dina användare än är. Användarna kan interagera med en replik av de data som är närmast dem.
- Ingen schema- eller indexhantering eftersom databasmotorn är helt schemaoberoende.
- Allestädes närvarande regional närvaro eftersom Azure Cosmos DB är tillgängligt i alla Azure-regioner över hela världen, vilket inkluderar över 54 regioner i det offentliga molnet.
- Exakt definierade, flera konsekvensval eftersom Azure Cosmos DB:s replikeringsprotokoll för flera huvudhuvuden är noggrant utformat för att erbjuda fem väldefinierade konsekvensval. Dessa fem val är starka, avgränsade föråldring, session, konsekvent prefix och eventuellt.
- 99.999% tillgänglighet för både läsningar och skrivningar.
- Programmässigt (eller via Azure-portalen) anropar den regionala redundansen för ditt Azure Cosmos DB-konto för att säkerställa att ditt program är utformat för att klara en regional katastrof.
- Garanterad låg latens vid den 99:e percentilen i världen.

**Referenser**
- [Azure-portal](https://portal.azure.com) 
- [Azure Cosmos DB-dokumentation](/azure/cosmos-db/introduction)

## <a name="azure-sql-database"></a>Azure SQL Database
 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) är en relationell tjänst för relationell databas med allmänt syfte. Du kan använda den för att skapa ett högtillgängligt och högpresterande datalagringslager för program och lösningar i Azure-molnet.

**Huvudfunktioner**
- **Elastiska databasmodeller och verktyg:** Med en elastisk databas kan utvecklare slå samman resurser mellan en grupp databaser för skalning. Om du vill hantera dessa resurser administrativt skickar du in ett skript som ett jobb. SQL-databasen utför sedan skriptet över databaserna.
- **Hög prestanda:** Program med hög genomströmning kan dra nytta av den senaste versionen. Det ger 25% mer premium databaskraft.
- **Säkerhetskopior, replikering och hög tillgänglighet:** Inbyggd replikering och ett Microsoft-stödda serviceavtal på databasnivå ger programkontinuitet och skydd mot katastrofala händelser. Med aktiv georeplikering kan du konfigurera redundans- och självbetjäningsåterställning, vilket ger full kontroll över "oops recovery". Dataåterställning är tillgänglig från säkerhetskopiering av data på upp till 35 dagar.
- **Nära noll underhåll:** Automatisk programvara är en del av tjänsten. Inbyggda systemrepliker hjälper till att leverera inbyggt dataskydd, databastid och systemstabilitet. Systemrepliker flyttas automatiskt till nya datorer. De är etablerade i farten som gamla misslyckas.
- **Säkerhet:** Azure SQL Database erbjuder en portfölj med säkerhetsfunktioner för att uppfylla organisationens eller branschuppdrag för efterlevnadsprinciper:
    - Granskning ger utvecklare möjlighet att utföra efterlevnadsrelaterade uppgifter och få kunskap om aktiviteter.
    - Utvecklare och IT-enheter kan implementera principer på databasnivå för att begränsa åtkomsten till känsliga data med säkerhet på radnivå, dynamisk datamaskering och transparent datakryptering för Azure SQL Database.
    - Azure SQL Database verifieras av viktiga molnrevisorer som en del av omfattningen av viktiga Azure-efterlevnadscertifieringar och godkännanden, till exempel HIPAA BAA, ISO/IEC 27001:2005, FedRAMP och EU-modellklausuler.

**Referenser**
- [Azure-portal](https://portal.azure.com) 
- [Dokumentation om Azure SQL Database](/azure/sql-database/) 
