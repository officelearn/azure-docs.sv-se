---
title: Lagra, hantera och spara program data i molnet med Visual Studio App Center-och Azure-tjänster
description: Lär dig mer om tjänsterna, till exempel App Center som gör att du kan lagra, hantera och bevara mobil program data i molnet.
author: elamalani
ms.assetid: 12344321-0123-4678-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: b7043c7e9bd944bfb3633397edfa3c35609bd8ec
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795580"
---
# <a name="store-sync-and-query-mobile-application-data-from-the-cloud"></a>Lagra, synkronisera och fråga mobil program data från molnet
Oavsett vilken typ av program du skapar kommer du troligen att generera och bearbeta data. Användarnas förväntningar är höga och de vill att programmet ska fungera snabbt och smidigt, under alla omständigheter. De flesta program fungerar på flera enheter, så när du öppnar programmet kan du komma åt det från en stationär eller en mobil enhet. Flera användare kan använda programmet samtidigt och dela data med förväntad åtkomst till data som direkt och i real tid.

Utöver det kommer dina program användare inte alltid att ha Internet anslutning. program är utformade och förväntas fungera med eller utan Internet anslutning. Med alla dessa växande svårigheter är det en enorm uppgift för utvecklare att välja rätt lösning för att lagra och synkronisera data till molnet för att ge en bra kund upplevelse för sitt program.

Microsoft tillhandahåller en mängd olika tjänster som eliminerar behovet av att skapa servrar, välja din databas eller oroa dig för skalning eller säkerhet för att ge så omfattande erfarenheter som möjligt. Dessa tjänster ger en bra utvecklare som gör det möjligt att lagra program data i molnet med hjälp av SQL-eller NoSQL-API: er, synkronisera data på alla enheter och göra det möjligt för programmet att fungera med eller utan en nätverks anslutning för att hjälpa till att bygga skalbara och robusta ansökan.

Använd följande tjänster för att hantera och lagra data i mobila program i molnet.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center data](/appcenter/data/) är en data hanterings tjänst som gör det möjligt för program att hantera, bevara och synkronisera program data i molnet mellan olika enheter och plattformar i både online-och offline-scenarier. Tjänsten är **byggd ovanpå Cosmos DB** -tjänst som skalas när användar basen och antalet program växer, och den här tjänsten säkerställer låg latens, hög tillgänglighet och hög skalbarhet för alla dina data.

**Viktiga funktioner**
- **Etablera enkelt en ny Cosmos DB databas** eller **Anslut till en befintlig Cosmos DB-databas** från App Center-portalen.
- **NoSQL Database-stöd** för att enkelt lagra, synkronisera och fråga program data.
- Den här tjänsten är **byggd ovanpå Cosmos DB**och ärver de flesta viktiga funktioner som erbjuds av Azure Cosmos DB och kan **skalas globalt** för att möta dina affärs behov.
- **Synkronisering online och offline** för att synkronisera data mellan enheter.
- **Klient-SDK** : er för mobila klienter som gör att du enkelt kan hantera både privata program data.
- **Plattforms stöd** – iOS, Android, Xamarin, reagera internt.

**Reference**
- [Registrera dig med App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Kom igång med App Center data](/appcenter/data/getting-started)

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) är Microsofts globalt distribuerade databas tjänst för flera modeller som hjälper dig att bygga plan skalar program. Med ett klick på en knapp kan du Cosmos DB att elastiskt och oberoende skala data flöde och lagrings utrymme i valfritt antal Azure-regioner över hela världen. Du kan skala data flöde och lagrings utrymme elastiskt, och dra nytta av snabb, ensiffriga millisekunder för data åtkomst med dina favorit-API-ytor, inklusive SQL, MongoDB, Cassandra, tables eller Gremlin. Cosmos DB unikt tillhandahåller omfattande service avtal (service avtal) för data flöde, svars tid, tillgänglighet och konsekvens.

**Viktiga funktioner**
- Har stöd **för en mängd olika API: er** omfattar API för SQL (Core), API för Cassandra, MONGODB-API, GREMLIN-api och tabell-API.
- **Nyckel färdig global distribution** replikerar dina data oavsett var dina användare befinner sig, så att användarna kan interagera med en replik av de data som är närmast dem.
- **Ingen schema-eller index hantering** eftersom databas motorn är fullständigt schema-oberoende.
- **Allmänt förekommande regional närvaro** som Cosmos DB är tillgänglig i alla Azure-regioner över hela världen, inklusive 54 + regioner i det offentliga molnet.
- **Precis definierat är flera konsekvens val** som Cosmos DBS protokoll för multi-master-replikering är noggrant utformade för att erbjuda fem väldefinierade konsekvens val – stark, begränsad föråldrad, session, konsekvent prefix och eventuell.
- **99,999% tillgänglighet** för både läsning och skrivning.
- Program **mässigt (eller via portalen) anropar den regionala redundansväxlingen** av ditt Cosmos-konto för att säkerställa att ditt program är utformat för att motstå en regional katastrof.
- **Garanterad låg latens vid 99 percentil**, världen över.

**Reference**
- [Azure-portalen](https://portal.azure.com) 
- [Handlingar](/azure/cosmos-db/introduction)   

## <a name="azure-sql-database"></a>Azure SQL Database
 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) är en hanterad tjänst för allmän Relations databas som gör att du kan skapa ett data lagrings lager med hög tillgänglighet och hög prestanda för program och lösningar i Azure-molnet.

**Viktiga funktioner**
- **Elastic Database-modeller och-verktyg** – en elastisk databas ger utvecklare möjlighet att pool resurser som används i en grupp databaser för skalning, som sedan kan administreras genom att skicka ett skript som ett jobb och SQL Database utföra skript över databaserna.
- **Hög prestanda** – program med höga data flöden kan dra nytta av den senaste versionen, som ger 25% mer Premium databas kraft.
- **Säkerhets kopiering, replikering och hög tillgänglighet** – inbyggd replikering och ett Microsoft-baserat service avtal på databas nivå ger program kontinuitet och skydd mot katastrof händelser. Aktiv geo-replikering låter dig konfigurera redundans och självbetjänings återställning, vilket ger fullständig kontroll över "Hoppsan Recovery" (Data återställning från tillgängliga säkerhets kopior på upp till 35 dagar).
- **Underhåll i nära noll** – automatisk program vara är en del av tjänsten och inbyggda system repliker hjälper till att leverera inbyggda data skydd, databas drift tid och system stabilitet. Systemkopiorna flyttas automatiskt till nya datorer som tillhandahålls löpande vid fel på äldre datorer.
- **Säkerhets** -SQL Database erbjuder en portfölj med säkerhetsfunktioner för att uppfylla organisations-eller branschspecifika efterlevnadsprinciper
    - Med granskning får utvecklare möjlighet att utföra efterlevnad av aktiviteter och få kunskap om aktiviteter.
    - Utvecklare och kan implementera principer på databas nivå för att begränsa åtkomsten till känsliga data med säkerhet på radnivå, dynamisk data maskning och transparent data kryptering för Azure SQL Database.
    - SQL Database verifieras av nyckel moln granskare som en del av omfattningen av nyckel certifieringar och godkännanden för Azure-kompatibilitet, till exempel HIPAA BAA, ISO/IEC 27001:2005, FedRAMP och EU: s modell klausuler).

**Reference**
- [Azure-portalen](https://portal.azure.com) 
- [Handlingar](/azure/sql-database/)
   