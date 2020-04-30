---
title: Introduktion till Azure Cosmos DB Gremlin API
description: Läs hur du kan använda Azure Cosmos DB för att lagra, fråga och bläddra igenom massiva diagram med låga svarstider med hjälp av Gremlin-diagramfrågespråket för Apache TinkerPop.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 07/18/2019
ms.author: lbosq
ms.openlocfilehash: 9151b54d7fa0b64a465aa8384cb4bfdb8e72c482
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "75500005"
---
# <a name="introduction-to-azure-cosmos-db-gremlin-api"></a>Introduktion till Azure Cosmos DB: Gremlin-API

[Azure Cosmos DB](introduction.md) är en globalt distribuerad databas tjänst för flera data modeller från Microsoft för verksamhets kritiska program. Det är en databas med flera modeller och stöd för dokument, nyckel värdes-, graf-och kolumn serie data modeller. Azure Cosmos DB Gremlin-API: et används för att lagra och använda diagram data på en fullständigt hanterad databas tjänst som har utformats för alla skalor.  

![Azure Cosmos DB-diagramarkitektur](./media/graph-introduction/cosmosdb-graph-architecture.png)

Den här artikeln ger en översikt över Azure Cosmos DB Gremlin-API och förklarar hur du kan använda den för att lagra massiva diagram med miljarder brytpunkter och kanter. Du kan fråga graferna med millisekunder och snabbt utveckla diagram strukturen. Azure Cosmos DBens Gremlin-API baseras på [Apache TinkerPop](https://tinkerpop.apache.org) graphs databas standard och använder Gremlin-frågespråket. 

Azure Cosmos DBens Gremlin-API kombinerar kraften i graf Database-algoritmer med en mycket skalbar, hanterad infrastruktur för att tillhandahålla en unik, flexibel lösning för de vanligaste data problemen som är kopplade till bristande flexibilitet och Relations metoder. 

## <a name="features-of-azure-cosmos-db-graph-database"></a>Funktioner i diagramdatabasen Azure Cosmos DB
 
Azure Cosmos DB är en fullständigt hanterad diagramdatabas som erbjuder global distribution, elastisk skalbarhet av lagring och dataflöde, automatisk indexering och frågor, justerbara konsekvensnivåer och stöd för TinkerPop-standarden. 

Följande är de differentierade funktionerna som Azure Cosmos DB Gremlin-API: er:

* **Elastiskt skalbara dataflöden och lagring**

  Diagram i verkligheten behöver skala utöver kapaciteten för en enskild server. Azure Cosmos DB stöder vågrätt skalbara diagram databaser som kan ha en i princip obegränsad storlek i termer av lagring och allokerat data flöde. I takt med att diagram databasen skalas, distribueras data automatiskt med hjälp av [diagram partitionering](https://docs.microsoft.com/azure/cosmos-db/graph-partitioning).

* **Replikering för flera regioner**

  Azure Cosmos DB kan automatiskt replikera dina diagram data till alla Azure-regioner i hela världen. Global replikering fören klar utvecklingen av program som kräver global åtkomst till data. Förutom att minimera Läs-och skriv fördröjning var som helst över hela världen, tillhandahåller Azure Cosmos DB automatisk regional redundans som kan garantera programmets kontinuitet i sällsynta fall av ett tjänst avbrott i en region. 

* **Snabba frågor och bläddringskontroll med den mest antagna standarden för kurv frågor**

  Lagra heterogena hörn och kanter och fråga dem genom en välbekant Gremlin-syntax. Gremlin är ett tvingande, funktionellt frågespråk som ger ett omfattande gränssnitt för att implementera vanliga graf-algoritmer. 
  
  Azure Cosmos DB möjliggör omfattande frågor och bläddringskontroll i real tid utan att behöva ange schema tips, sekundära index eller vyer. Läs mer i [Fråga diagram med hjälp av Gremlin](gremlin-support.md).

* **Fullständigt hanterad diagram databas**

  Azure Cosmos DB eliminerar behovet av att hantera databasen och datorresurserna. De flesta befintliga Graph Database-plattformarna är kopplade till begränsningarna i infrastrukturen och kräver ofta en hög grad av underhåll för att säkerställa driften. 
  
  Som en helt hanterad tjänst tar Cosmos DB bort behovet av att hantera virtuella datorer, uppdatera runtime-programvaran, hantera horisontell partitionering eller replikering, eller hantera komplexa uppgraderingar av data nivåer. Varje databas säkerhetskopieras och skyddas automatiskt mot regionala fel. Dessa garantier gör att utvecklare kan fokusera på att leverera program värde i stället för att hantera sina graf-databaser. 

* **Automatisk indexering**

  Som standard indexerar Azure Cosmos DB automatiskt alla egenskaperna inom noderna och kanterna i diagrammet och varken förväntar sig eller kräver något schema eller att sekundära index skapas. Läs mer om [indexering i Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/index-overview). 

* **Kompatibilitet med Apache TinkerPop**

  Azure Cosmos DB stöder [Apache TinkerPop-standarden med öppen källkod](https://tinkerpop.apache.org/). Tinkerpop-standarden har ett stort eko system med program och bibliotek som enkelt kan integreras med Azure Cosmos DBens Gremlin-API. 

* **Justerbara konsekvensnivåer**

  Azure Cosmos DB tillhandahåller fem väldefinierade konsekvens nivåer för att uppnå rätt kompromisser mellan konsekvens och prestanda för ditt program. Azure Cosmos DB erbjuder fem olika konsekvensnivåer för frågor och läsåtgärder: stark, bunden utgång, session, enhetligt prefix och slutlig. Med de här detaljerade, väldefinierade konsekvensnivåerna kan du själv avgöra balansen mellan konsekvens, tillgänglighet och svarstid. Läs mer i [Justerbara datakonsekvensnivåer i Azure Cosmos DB](consistency-levels.md).

## <a name="scenarios-that-can-use-gremlin-api"></a>Scenarier där Gremlin-API:t kan användas
Här följer några scenarier där diagram stöd för Azure Cosmos DB kan vara användbart:

* **Sociala nätverk/kund 365**

  Genom att kombinera data om dina kunder och deras interaktioner med andra personer kan du utveckla anpassade upplevelser, förutsäga kundbeteende eller ansluta personer med andra med liknande intressen. Azure Cosmos-DB kan användas för att hantera sociala nätverk och spåra kundinställningar och data.

* **Rekommendationsmotorer**

  Det här scenariot används ofta inom detaljhandeln. Genom att kombinera information om produkter, användare och användarinteraktioner som köp, bläddring eller klassificeringen av ett objekt, kan du skapa anpassade rekommendationer. Stöd för låg latens, elastisk skalning och inbyggd graf för Azure Cosmos DB är perfekt för dessa scenarier.

* **Geospatial**

  Många program inom telekommunikation, logistik och reseplanering måste hitta en plats av intresse inom ett område eller hitta den kortaste/optimala vägen mellan två platser. Azure Cosmos DB är en naturlig lösning på de här problemen.

* **Internet of Things**

  Med nätverket och anslutningarna mellan IoT-enheter som modellerats som ett diagram, kan du skapa en bättre förståelse för dina enheters och tillgångars tillstånd. Du kan också undersöka hur ändringar i en del av nätverket kan påverka en annan del.

## <a name="introduction-to-graph-databases"></a>Introduktion till diagram databaser
Data som de visas i verkligheten är naturligt sammanlänkade. Traditionell data modellering fokuserar på att definiera entiteter separat och att beräkna deras relationer vid körning. Även om den här modellen har sina fördelar kan data som är mycket anslutna vara svåra att hantera under sina begränsningar.  

En diagram databas metod förlitar sig på beständiga relationer i lagrings lagret i stället, vilket leder till mycket effektiva diagram hämtnings åtgärder. Azure Cosmos DBens Gremlin-API stöder [egenskaps diagrammets modell](https://tinkerpop.apache.org/docs/current/reference/#intro).

### <a name="property-graph-objects"></a>Egenskaps diagram objekt

Ett egenskaps [diagram](http://mathworld.wolfram.com/Graph.html) är en struktur som består av [hörn](http://mathworld.wolfram.com/GraphVertex.html) och [kanter](http://mathworld.wolfram.com/GraphEdge.html). Båda objekten kan ha ett godtyckligt antal nyckel/värde-par som egenskaper. 

* **Formhörn** – hörnen kännetecknar diskreta entiteter, till exempel en person, en plats eller en händelse.

* **Kanter** – Kanter betecknar relationer mellan brytpunkter. En person kan till exempel känna till en annan person, vara inblandad i en händelse och nyligen ha varit på en plats. 

* **Egenskaper** – Egenskaper uttrycker information om brytpunkterna och kanterna. Det kan finnas ett valfritt antal egenskaper i antingen formhörn eller kanter, och de kan användas för att beskriva och filtrera objekten i en fråga. Exempel egenskaperna innehåller ett hörn som har namn och ålder, eller en kant, som kan ha en tidstämpel och/eller en vikt. 

Graf-databaser ingår ofta i NoSQL eller icke-relationell databas kategori eftersom det inte finns något beroende av ett schema eller en begränsad data modell. Detta brist på schema möjliggör modellering och lagring av anslutna strukturer naturligt och effektivt. 

### <a name="gremlin-by-example"></a>Gremlin efter exempel
Nu ska vi använda ett exempeldiagram för att förstå hur frågor kan uttryckas i Gremlin. Följande bild visar ett affärsprogram som hanterar data om användare, intressen och enheter i form av ett diagram.  

![Exempeldatabas som visar personer, enheter och intressen](./media/gremlin-support/sample-graph.png) 

Det här diagrammet har följande *hörn* typer (kallas "etikett" i Gremlin):

- **Personer**: grafen har tre personer, Robin, Thomas och ben
- **Intressen**: deras intressen, i det här exemplet spel på fotboll
- **Enheter**: enheterna som människor använder
- **Operativ system**: de operativ system som enheterna kör på

Vi representerar relationerna mellan dessa entiteter via följande *kant* typer/etiketter:

- **Känner**: till exempel "Thomas vet Robin"
- **Intresse rad**: att representera deltagarnas intressen i vårt diagram, till exempel "ben är intresserade av fotboll"
- **Körs**: den bärbara datorn kör Windows OS
- **Använder**: för att representera vilken enhet en person använder. Till exempel Robin använder en Motorola-telefon med serienummer 77

Vi kör några åtgärder mot det här diagrammet med [Gremlin-konsolen](https://tinkerpop.apache.org/docs/3.3.2/reference/#gremlin-console). Du kan också utföra dessa åtgärder med hjälp av Gremlin-drivrutinerna i den plattform du vill (Java, Node.js, Python eller .NET).  Innan vi tittar på vad som stöds i Azure Cosmos DB ska vi titta på några exempel för att bekanta oss med syntaxen.

Först ska vi titta på CRUD. Följande Gremlin-uttryck infogar Thomas-brytpunkten i diagrammet:

```java
:> g.addV('person').property('id', 'thomas.1').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

Därefter infogar följande Gremlin-uttryck en känner-kant mellan Thomas och Robin.

```java
:> g.V('thomas.1').addE('knows').to(g.V('robin.1'))
```

Följande fråga returnerar person-brytpunkten i fallande ordning efter deras förnamn:
```java
:> g.V().hasLabel('person').order().by('firstName', decr)
```

Där diagram verkligen kommer till sin rätt är när det gäller att svara på frågor som Vilka operativsystem använder Thomas vänner? Du kan köra den här Gremlin-genom gången för att hämta informationen från grafen:

```java
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```

## <a name="next-steps"></a>Nästa steg
Mer information om diagramstöd i Azure Cosmos DB finns i:

* Kom igång med [Självstudien Azure Cosmos DB-diagram](create-graph-dotnet.md).
* Läs mer om hur du [frågar diagram i Azure Cosmos DB med hjälp av Gremlin](gremlin-support.md).
