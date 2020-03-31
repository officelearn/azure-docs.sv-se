---
title: Förstå skillnaderna mellan Azure Cosmos DB NoSQL och relationsdatabaser
description: Den här artikeln räknar upp skillnaderna mellan NoSQL- och relationsdatabaser
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/16/2019
ms.reviewer: sngun
ms.openlocfilehash: 1cd80fee51565f2a2c1afa38ed883c10f51a5ee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75896628"
---
# <a name="understanding-the-differences-between-nosql-and-relational-databases"></a>Förstå skillnaderna mellan NoSQL- och relationsdatabaser

Den här artikeln räknar upp några av de viktigaste fördelarna med NoSQL-databaser över relationsdatabaser. Vi kommer också att diskutera några av utmaningarna i arbetet med NoSQL. För en djupgående titt på de olika datalager som finns, ta en titt på vår artikel om [att välja rätt datalager](https://docs.microsoft.com/azure/architecture/guide/technology-choices/data-store-overview).

## <a name="high-throughput"></a>Högt genomflöde

En av de mest uppenbara utmaningarna när man upprätthåller ett relationsdatabassystem är att de flesta relationsmotorer tillämpar lås och lås för att genomdriva strikta [ACID semantik](https://en.wikipedia.org/wiki/ACID). Den här metoden har fördelar när det gäller att säkerställa ett konsekvent datatillstånd i databasen. Det finns dock stora kompromisser när det gäller samtidighet, svarstid och tillgänglighet. På grund av dessa grundläggande arkitektoniska begränsningar kan höga transaktionsvolymer resultera i behovet av att manuellt fragmentera data. Genomföra manuell sharding kan vara en tidskrävande och smärtsam övning.

I dessa scenarier kan [distribuerade databaser](https://en.wikipedia.org/wiki/Distributed_database) erbjuda en mer skalbar lösning. Underhåll kan dock fortfarande vara en kostsam och tidskrävande övning. Administratörer kan behöva göra extra arbete för att säkerställa att systemets distribuerade karaktär är transparent. De kan också behöva ta hänsyn till databasens "frånkopplade" karaktär.

[Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction) förenklar dessa utmaningar genom att distribueras över hela världen i alla Azure-regioner. Partitionsintervall kan delas in dynamiskt för att sömlöst utöka databasen i linje med programmet, samtidigt som hög tillgänglighet bibehålls. Finkornig multi-arrende och hårt kontrollerad, moln-native resurs styrning underlättar [häpnadsväckande latens garantier](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-tradeoffs#consistency-levels-and-latency) och förutsägbarprestanda. Partitioneringen hanteras helt, så administratörer behöver inte skriva kod eller hantera partitioner.

Om transaktionsvolymerna når extrema nivåer, till exempel tusentals transaktioner per sekund, bör du överväga en distribuerad NoSQL-databas. Överväg Azure Cosmos DB för maximal effektivitet, enkel underhåll och minskad total ägandekostnad.

![Backend](./media/relational-or-nosql/backend-scaled.png)

## <a name="hierarchical-data"></a>Hierarkiska data

Det finns ett stort antal användningsfall där transaktioner i databasen kan innehålla många överordnade och underordnade relationer. Dessa relationer kan växa betydligt över tiden, och visa sig vara svårt att hantera. Former av [hierarkiska databaser](https://en.wikipedia.org/wiki/Hierarchical_database_model) framkom under 1980-talet, men var inte populära på grund av ineffektivitet i lagring. De förlorade också dragkraft som [Ted Codd relationella modell](https://en.wikipedia.org/wiki/Relational_model) blev de facto standard som används av praktiskt taget alla vanliga databashanteringssystem.

Men idag populariteten för dokument-stil databaser har ökat avsevärt. Dessa databaser kan betraktas som en återuppfinna av den hierarkiska databasen paradigm, nu hämningslös av oro kring kostnaden för att lagra data på disk. Därför kan det nu betraktas som ett antimönster att underhålla många komplexa överordnade och underordnade entitetsrelationer i en relationsdatabas jämfört med moderna dokumentorienterade metoder.

Framväxten av [objektorienterad design](https://en.wikipedia.org/wiki/Object-oriented_design), och [den impedans obalans](https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch) som uppstår när man kombinerar den med relationsmodeller, belyser också ett anti-mönster i relationsdatabaser för vissa användningsfall. Dolda men ofta betydande underhållskostnader kan uppstå som ett resultat. Även om [ORM-metoder](https://en.wikipedia.org/wiki/Object-relational_mapping) har utvecklats för att delvis mildra detta, smälter dokumentorienterade databaser ändå samman mycket bättre med objektorienterade metoder. Med den här metoden är utvecklare inte tvungna att vara engagerade i ORM-drivrutiner, eller skräddarsydda språkspecifika [OO Database motorer](https://en.wikipedia.org/wiki/Object_database). Om dina data innehåller många överordnade och underordnade relationer och djupa hierarkinivåer kan du överväga att använda en NoSQL-dokumentdatabas, till exempel [Azure Cosmos DB SQL API](https://docs.microsoft.com/azure/cosmos-db/introduction).

![OrderDetails](./media/relational-or-nosql/order-orderdetails.jpg)

## <a name="complex-networks-and-relationships"></a>Komplexa nätverk och relationer

Ironiskt nog, med tanke på deras namn, relationsdatabaser presentera en mindre än optimal lösning för modellering djupa och komplexa relationer. Anledningen till detta är att relationer mellan entiteter faktiskt inte finns i en relationsdatabas. De måste beräknas vid körning, med komplexa relationer som kräver cartesian kopplingar för att tillåta mappning med hjälp av frågor. Som ett resultat blir verksamheten exponentiellt dyrare när det gäller beräkning som relationer ökar. I vissa fall blir en relationsdatabas som försöker hantera sådana entiteter ouppnvärt.

Olika former av "Network" databaser uppstod under den tid som relationsdatabaser uppstod, men som med hierarkiska databaser, dessa system kämpade för att vinna popularitet. Långsam antagande berodde på brist på användningsfall vid den tidpunkten, och lagring ineffektivitet. Idag kan graf databasmotorer betraktas som en återkomst av nätverket databas paradigm. Den viktigaste fördelen med dessa system är att relationer lagras som "första klassens medborgare" i databasen. Således kan korsa relationer göras i konstant tid, snarare än att öka i tid komplexitet med varje ny koppling eller korsprodukt.

Om du underhåller ett komplext nätverk av relationer i databasen kan du överväga en diagramdatabas som [Azure Cosmos DB Gremlin API](https://docs.microsoft.com/azure/cosmos-db/graph-introduction) för att hantera dessa data.

![Graph](./media/relational-or-nosql/graph.png)

Azure Cosmos DB är en databastjänst med flera modeller som erbjuder en API-projektion för alla större NoSQL-modelltyper. Kolumnfamilj, dokument, diagram och nyckelvärde. [Api-lagren För Gremlin (diagram)](https://docs.microsoft.com/azure/cosmos-db/gremlin-support) och SQL (Core) är helt driftskompatibla. Detta har fördelar för att växla mellan olika modeller på programmerabilitetsnivå. Diagramarkiv kan efterfrågas både i fråga om komplexa nätverksgranster och transaktioner som modelleras som dokumentposter i samma butik.

## <a name="fluid-schema"></a>Flytande schema

En annan särskild egenskap hos relationsdatabaser är att scheman måste definieras vid designtillfället. Detta har fördelar när det gäller referensintegritet och överensstämmelse av uppgifter. Men det kan också vara restriktivt när programmet växer. Att svara på ändringar i schemat över logiskt separata modeller som delar samma tabell eller databasdefinition kan bli komplicerat med tiden. Sådana användningsfall drar ofta nytta av att schemat delegeras till programmet för att hantera per post. Detta kräver att databasen är "schemaagnostiker" och tillåter att poster är "självbeskrivande" när det gäller de data som finns i dem.

Om du hanterar data vars strukturer ständigt förändras i hög takt, särskilt om transaktioner kan komma från externa källor där det är svårt att genomdriva överensstämmelse i databasen, kanske du vill överväga en mer schema-agnostisk metod med hjälp av en hanterad NoSQL-databastjänst som Azure Cosmos DB.

## <a name="microservices"></a>Mikrotjänster

[Mikrotjänstmönstret](https://en.wikipedia.org/wiki/Microservices) har vuxit betydligt under de senaste åren. Detta mönster har sina rötter i [serviceorienterad arkitektur.](https://en.wikipedia.org/wiki/Service-oriented_architecture) De facto-standarden för dataöverföring i dessa moderna mikrotjänstarkitekturer är [JSON](https://en.wikipedia.org/wiki/JSON), som också råkar vara lagringsmediet för de allra flesta dokumentorienterade NoSQL-databaser. Detta gör NoSQL-dokument lagrar en mycket mer sömlös passform för både uthållighet och synkronisering (med hjälp av [händelseinköpsmönster)](https://en.wikipedia.org/wiki/Event-driven_architecture)över komplexa Microservice-implementeringar. Mer traditionella relationsdatabaser kan vara mycket mer komplexa att underhålla i dessa arkitekturer. Detta beror på den större mängd omvandling som krävs för både tillstånd och synkronisering mellan API:er. Azure Cosmos DB i synnerhet har ett antal funktioner som gör det ännu mer sömlöst passar för JSON-baserade Microservices Architectures än många NoSQL-databaser:

* ett urval av rena JSON-datatyper
* en JavaScript-motor och [fråge-API](https://docs.microsoft.com/azure/cosmos-db/javascript-query-api) som är inbyggt i databasen.
* ett toppmodernt [ändringsflöde](https://docs.microsoft.com/azure/cosmos-db/change-feed) som kunderna kan prenumerera på för att få meddelanden om ändringar i en behållare.

## <a name="some-challenges-with-nosql-databases"></a>Vissa utmaningar med NoSQL-databaser

Även om det finns några tydliga fördelar när du implementerar NoSQL-databaser, finns det också några utmaningar som du kanske vill ta hänsyn till. Dessa får inte vara närvarande i samma utsträckning när man arbetar med relationsmodellen:

* transaktioner med många relationer som pekar på samma entitet.
* transaktioner som kräver stark konsekvens i hela datauppsättningen.

Om man tittar på den första utmaningen, är tumregeln i NoSQL databaser i allmänhet denormalisering, som som artikulerade tidigare, producerar effektivare läser i ett distribuerat system. Det finns dock vissa designutmaningar som spelar in med detta tillvägagångssätt. Låt oss ta ett exempel på en produkt som är relaterad till en kategori och flera taggar:

![Kopplingar](./media/relational-or-nosql/many-joins.png)

En metod för bästa praxis i en NoSQL-dokumentdatabas är att denormalisera kategorinamnet och taggnamnen direkt i ett "produktdokument". Men för att hålla kategorier, taggar och produkter synkroniserade, har designalternativen för att underlätta detta lagt till underhållskomplexitet, eftersom data dupliceras över flera poster i produkten, snarare än att vara en enkel uppdatering i en "en-till-många" och en koppling för att hämta data. 

Avvägningen är att läsningar är mer effektiva i den denormaliserade posten, och blir allt effektivare när antalet konceptuellt förenade enheter ökar. Men precis som läseffektiviteten ökar med allt fler anslutna entiteter i en denormalisera post, så gör också underhållskomplexiteten att hålla entiteterna synkroniserade. Ett sätt att minska den här avvägningen är att skapa en [hybriddatamodell](https://docs.microsoft.com/azure/cosmos-db/modeling-data#hybrid-data-models).

Även om det finns mer flexibilitet i NoSQL-databaser för att hantera dessa kompromisser, kan ökad flexibilitet också producera fler designbeslut. Läs vår artikel [hur du modellerar och partitionerar data på Azure Cosmos DB med hjälp av ett verkligt exempel](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example), som innehåller en metod för att hålla [denormaliserade användardata synkroniserade](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example#denormalizing-usernames) där användarna inte bara sitter i olika partitioner, men i olika behållare.

När det gäller stark konsekvens är det ovanligt att detta kommer att krävas i hela datauppsättningen. I de fall då detta är nödvändigt kan det dock vara en utmaning i distribuerade databaser. För att säkerställa en stark konsekvens måste data synkroniseras över alla repliker och regioner innan klienterna kan läsa dem. Detta kan öka svarstiden för läsningar.

Återigen erbjuder Azure Cosmos DB mer flexibilitet än relationsdatabaser för de olika kompromisser som är relevanta här, men för småskaliga implementeringar kan den här metoden lägga till fler designöverväganden. Mer information om det här avsnittet finns i vår artikel om [konsekvens, tillgänglighet och prestandaavvägningar.](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-tradeoffs)

## <a name="next-steps"></a>Nästa steg

Läs om hur du hanterar ditt Azure Cosmos-konto och andra koncept:

* [Hantera ditt Azure Cosmos-konto](how-to-manage-database-account.md)
* [Global distribution](distribute-data-globally.md)
* [Konsekvensnivåer](consistency-levels.md)
* [Arbeta med Azure Cosmos-behållare och objekt](databases-containers-items.md)
* [Slutpunkt för VNET-tjänst för ditt Azure Cosmos-konto](vnet-service-endpoint.md)
* [IP-brandvägg för ditt Azure Cosmos-konto](firewall-support.md)
* [Så här lägger du till och tar bort Azure-regioner i ditt Azure Cosmos-konto](how-to-manage-database-account.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
