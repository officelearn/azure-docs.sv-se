---
title: Introduktion till Azure Cosmos DB Gremlin API
description: Läs hur du kan använda Azure Cosmos DB för att lagra, fråga och bläddra igenom massiva diagram med låga svarstider med hjälp av Gremlin-diagramfrågespråket för Apache TinkerPop.
services: cosmos-db
author: LuisBosquez
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.topic: overview
ms.date: 09/05/2018
ms.author: lbosq
ms.openlocfilehash: e9e0d2f452a21f2da29568b953238318cb4477df
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53077459"
---
# <a name="introduction-to-azure-cosmos-db-gremlin-api"></a>Introduktion till Azure Cosmos DB: Gremlin-API

[Azure Cosmos DB](introduction.md) är den globalt distribuerade databastjänsten för flera datamodeller från Microsoft för verksamhetskritiska program. Den stöder flera databasmodeller och dokument, nyckelvärden, diagram och kolumndatamodeller. Azure Cosmos DB Gremlin API används för att lagra och bearbeta diagramdata. Gremlin-API:t har stöd för modellering av diagramdata och innehåller API:er för att bläddra i diagramdata.

Den här artikeln ger en översikt över Azure Cosmos DB Gremlin-API och förklarar hur du kan använda den för att lagra massiva diagram med miljarder brytpunkter och kanter. Du kan fråga diagrammen med svarstider på millisekunder och enkelt utveckla diagrammets struktur och schema. Du kan ställa frågor mot Azure Cosmos DB med diagramtraverseringsspråket [Apache TinkerPop](https://tinkerpop.apache.org) eller [Gremlin](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps).

## <a name="what-is-a-graph-database"></a>Vad är en diagramdatabas?
Data som de visas i verkligheten är naturligt sammanlänkade. Traditionell datamodellering fokuserar på entiteter. För många program finns det även ett behov av att modellera eller att modellera både entiteter och relationer på ett naturligt sätt.

Ett [diagram](https://mathworld.wolfram.com/Graph.html) är en struktur som består av [brytpunkter](http://mathworld.wolfram.com/GraphVertex.html) och [kanter](http://mathworld.wolfram.com/GraphEdge.html). Både brytpunkterna och kanterna kan ha ett godtyckligt antal egenskaper. 

* **Brytpunkter** – Brytpunkter betecknar diskreta objekt, till exempel en person, en plats eller en händelse. 

* **Kanter** – Kanter betecknar relationer mellan brytpunkter. En person kan till exempel känna till en annan person, vara inblandad i en händelse och nyligen ha varit på en plats. 

* **Egenskaper** – Egenskaper uttrycker information om brytpunkterna och kanterna. Exempelegenskaperna har en brytpunkt för namn och ålder. En kant med tidsstämpel och/eller en vikt. Mer formellt, kallas den här modellen för ett [egenskapsdiagram](https://tinkerpop.apache.org/docs/current/reference/#intro). Azure Cosmos DB stöder egenskapsdiagram-modellen.

Följande exempeldiagram visar till exempel relationerna mellan personer, mobila enheter, intressen och operativsystem:

![Exempeldatabas som visar personer, enheter och intressen](./media/graph-introduction/sample-graph.png)

Diagramdatabaser låter dig modellera och lagra diagram på ett naturligt och effektivt sätt, vilket gör dem användbara för många scenarier. Diagramdatabaser är vanligtvis NoSQL-databaser eftersom användningsfallen ofta också kräver schemaflexibilitet och snabba iterationer.

Du kan kombinera den snabba bläddringen som diagramdatabaser erbjuder med diagramalgoritmer som djup först-sökningar, bredd först-sökningar och Dijkstras algoritm för att lösa problem inom olika domäner som sociala nätverk, innehållshantering, geospatiala och rekommendationer.

## <a name="features-of-azure-cosmos-db-graph-database"></a>Funktioner i diagramdatabasen Azure Cosmos DB
 
Azure Cosmos DB är en fullständigt hanterad diagramdatabas som erbjuder global distribution, elastisk skalbarhet av lagring och dataflöde, automatisk indexering och frågor, justerbara konsekvensnivåer och stöd för TinkerPop-standarden.

![Azure Cosmos DB-diagramarkitektur](./media/graph-introduction/cosmosdb-graph-architecture.png)

Azure Cosmos DB erbjuder följande differentierade funktioner jämfört med andra diagramdatabaser på marknaden:

* Elastiskt skalbara dataflöden och lagring

 Diagram i verkligheten behöver skala utöver kapaciteten för en enskild server. Med Azure Cosmos DB kan du skala dina diagram sömlöst över flera servrar. Du kan även skala dataflödet för ditt diagram oberoende baserat på dina åtkomstmönster. Azure Cosmos DB stöder diagramdatabaser som kan skalas till praktiskt taget obegränsade lagringsstorlekar och etablerade dataflöden.

* Replikering för flera regioner

 Azure Cosmos DB replikerar transparent dina diagramdata till alla regioner som du har kopplat till ditt konto. Replikering låter dig utveckla program som kräver global åtkomst till data. Det finns kompromisser inom områden som konsekvens, tillgänglighet, prestanda och motsvarande garantier. Azure Cosmos DB erbjuder transparenta regionala redundansväxlingar med flera API:er. Du kan elastiskt skala dataflöde och lagring över hela världen.

* Snabba frågor och bläddringar med välbekant Gremlin-syntax

 Lagra heterogena brytpunkter och kanter och fråga de här dokumenten via ett bekant Gremlin-syntax. Azure Cosmos DB använder en höggradigt samtidig, låsfri, loggstrukturerad indexeringsteknik som automatiskt indexerar allt innehåll. Det här tillåter detaljrika frågor i realtid utan att behöva ange schematips, sekundärindex eller vyer. Läs mer i [Fråga diagram med hjälp av Gremlin](gremlin-support.md).

* Fullständigt hanterat

 Azure Cosmos DB eliminerar behovet av att hantera databasen och datorresurserna. Som en fullständigt hanterad Microsoft Azure-tjänst behöver du inte hantera virtuella datorer, distribuera och konfigurera programvara, hantera skalning eller hantera komplexa uppgraderingar av datanivåer. Varje databas säkerhetskopieras och skyddas automatiskt mot regionala fel. Du kan enkelt lägga till ett Azure Cosmos DB-konto och etablera kapacitet efter behov så att du kan fokusera på ditt program istället för att driva och hantera din databas.

* Automatisk indexering

 Som standard indexerar Azure Cosmos DB automatiskt alla egenskaperna inom noderna och kanterna i diagrammet och varken förväntar sig eller kräver något schema eller att sekundära index skapas.

* Kompatibilitet med Apache TinkerPop

 Azure Cosmos DB har ett inbyggt stöd för Apache TinkerPop-standarden med öppen källkod och kan integreras med andra TinkerPop-aktiverade diagramsystem. Du kan därför enkelt migrera från en annan diagramdatabas, som Titan eller Neo4j, eller använda Azure Cosmos DB med ramverk för diagramanalys som Apache Spark GraphX.

* Justerbara konsekvensnivåer

 Välj mellan fem väldefinierade konsekvensnivåer för bästa möjliga balans mellan konsekvens och prestanda. Azure Cosmos DB erbjuder fem olika konsekvensnivåer för frågor och läsåtgärder: stark, bunden utgång, session, enhetligt prefix och slutlig. Med de här detaljerade, väldefinierade konsekvensnivåerna kan du själv avgöra balansen mellan konsekvens, tillgänglighet och svarstid. Läs mer i [Justerbara datakonsekvensnivåer i Azure Cosmos DB](consistency-levels.md).

Azure Cosmos DB kan också använda flera modeller som dokument och diagram i samma containrar/databaser. Du kan använda en dokumentcontainer och lagra diagramdata sida vid sida med dokument. Du kan använda både SQL-frågor via JSON och Gremlin-frågor för att fråga efter samma data som ett diagram.

## <a name="get-started"></a>Kom igång

Du kan använda Azure-kommandoradsgränssnittet (CLI), Azure PowerShell eller Azure-portalen för att skapa och komma åt Azure Cosmos DB Gremlin API-konton. När du har skapat ett konto kommer du åt diagramdatabaserna inom kontot med hjälp av en Gremlin-API-tjänstslutpunkt `https://<youraccount>.gremlin.cosmosdb.azure.com`, som tillhandahåller en WebSocket-klientdel för Gremlin. Du kan konfigurera dina TinkerPop-kompatibla verktyg som [Gremlin konsolen](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console), för att ansluta till den här slutpunkten och skapa program i Java, Node.js och Gremlin-klientdrivrutiner.

Följande tabell visar populära Gremlin-drivrutiner som du kan använda mot Azure Cosmos DB:

| Ladda ned | Dokumentation | Komma igång | Version av anslutningsappen som stöds |
| --- | --- | --- | --- |
| [NET](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-DotNet) | [Gremlin.NET på GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-dotnet) | [Skapa diagram med .NET](create-graph-dotnet.md) | 3.4.0-RC2 |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) | [Gremlin JavaDoc](https://tinkerpop.apache.org/javadocs/current/full/) | [Skapa diagram med Java](create-graph-java.md) | 3.2.0+ |
| [Node.js](https://www.npmjs.com/package/gremlin) | [Gremlin-JavaScript på GitHub](https://github.com/jbmusso/gremlin-javascript) | [Skapa diagram med Node.js](create-graph-nodejs.md) | 2.6.0|
| [Python](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-python) | [Gremlin-Python på GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-python) | [Skapa diagram med Python](create-graph-python.md) | 3.2.7 |
| [PHP](https://packagist.org/packages/brightzone/gremlin-php) | [Gremlin-PHP på GitHub](https://github.com/PommeVerte/gremlin-php) | [Skapa diagram med PHP](create-graph-php.md) | 3.1.0 |
| [Gremlin-konsol](https://tinkerpop.apache.org/downloads.html) | [TinkerPop-dokument](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |  [Skapa diagram med Gremlin-konsolen](create-graph-gremlin-console.md) | 3.2.0 + |

## <a name="graph-database-design-considerations"></a>Designöverväganden för diagramdatabasen

Om det vid diagramutformning tas ett beslut att modellera en entitet som en egen brytpunkt, i stället för som en egenskap hos andra brytpunktsentiteter, påverkas både prestanda och kostnader. Huvuddrivrutinen för det här beslutet beror i första hand på hur data kommer att efterfrågas, samt skalbarheten i själva modellen.

Tänk igenom följande frågor innan du börjar planera hur entiteten ska modelleras:

* Vilka entiteter måste hämtas som brytpunkter för de flesta av mina frågor?

* Vilken information som jag lagt till i diagrammet används i filtreringssyfte?

* Vilka entiteter utgör kopplingar till andra entiteter som sedan hämtas för att man ska få tillgång till deras värden?

* Vilka uppgifter måste min fråga hämta och vilka RU-kostnader ska de generera?

Tänk dig följande diagramdesign:

![Exempel på designöverväganden för diagram](./media/graph-introduction/graph-design-considerations-example.png)

* Beroende på frågorna är det möjligt att relationen Distrikt -> Butik används unikt för filtrera brytpunkterna för butik. Ett exempel på detta är om frågorna är av typen ”hämta alla butiker som hör till ett visst distrikt”. Om så är fallet kanske du ska dölja entiteten Distrikt som självständig brytpunkt och istället låta den bli en egenskap i brytpunkten för Butik. 

* Den här metoden har fördelen att den sänker kostnaderna för att hämta varje brytpunkt för Butik genom att hämta tre diagramobjekt i taget (Distrikt, Distrikt -> Butik, Butik) till en enskild brytpunkt för Butik. Detta kan ge prestandaförbättringar och en lägre kostnad per fråga.

* Brytpunkten Butik länkas till två olika entiteter – Personal och Produkt. Detta gör Butik till en obligatorisk brytpunkt, eftersom den ger tillgång till fler bläddringsmöjligheter.  



## <a name="scenarios-that-can-use-gremlin-api"></a>Scenarier där Gremlin-API:t kan användas
Här följer några scenarier där diagramstödet för Azure Cosmos DB kan användas:

* Sociala nätverk

 Genom att kombinera data om dina kunder och deras interaktioner med andra personer kan du utveckla anpassade upplevelser, förutsäga kundbeteende eller ansluta personer med andra med liknande intressen. Azure Cosmos-DB kan användas för att hantera sociala nätverk och spåra kundinställningar och data.

* Rekommendationsmotorer

 Det här scenariot används ofta inom detaljhandeln. Genom att kombinera information om produkter, användare och användarinteraktioner som köp, bläddring eller klassificeringen av ett objekt, kan du skapa anpassade rekommendationer. De låga svarstiderna, elastiska skalbarheten och det interna diagramstödet för Azure Cosmos DB är idealiskt för modellering av de här interaktionerna.

* Geospatial

 Många program inom telekommunikation, logistik och reseplanering måste hitta en plats av intresse inom ett område eller hitta den kortaste/optimala vägen mellan två platser. Azure Cosmos DB är en naturlig lösning på de här problemen.

* Internet of Things

 Med nätverket och anslutningarna mellan IoT-enheter som modellerats som ett diagram, kan du skapa en bättre förståelse för dina enheters och tillgångars tillstånd. Du kan också undersöka hur ändringar i en del av nätverket kan påverka en annan del.

## <a name="next-steps"></a>Nästa steg
Mer information om diagramstöd i Azure Cosmos DB finns i:

* Kom igång med [Självstudien Azure Cosmos DB-diagram](create-graph-dotnet.md).
* Läs mer om hur du [frågar diagram i Azure Cosmos DB med hjälp av Gremlin](gremlin-support.md).
