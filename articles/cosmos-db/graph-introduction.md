---
title: Introduktion till Azure Cosmos DB Gremlin API
description: Läs hur du kan använda Azure Cosmos DB för att lagra, fråga och bläddra igenom massiva diagram med låga svarstider med hjälp av Gremlin-diagramfrågespråket för Apache TinkerPop.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 06/25/2019
ms.author: lbosq
ms.openlocfilehash: 126c825106b7844a5fc8a5a3cdbcc7aa6c273b5b
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502807"
---
# <a name="introduction-to-azure-cosmos-db-gremlin-api"></a>Introduktion till Azure Cosmos DB: Gremlin-API

[Azure Cosmos DB](introduction.md) är tjänsten globalt distribuerad databas för flera datamodeller från Microsoft för verksamhetskritiska program. Det är en databas för flera modeller och har stöd för dokument, nyckel / värde-, graf och kolumndatamodeller. Azure Cosmos DB Gremlin-API används för att lagra och arbeta med graph-data i en fullständigt hanterad databas-miljö i valfri skala.  

![Azure Cosmos DB-diagramarkitektur](./media/graph-introduction/cosmosdb-graph-architecture.png)

Den här artikeln ger en översikt över Azure Cosmos DB Gremlin-API och förklarar hur du kan använda den för att lagra massiva diagram med miljarder brytpunkter och kanter. Du kan fråga i grafer med millisekunds fördröjning och enkelt kan utveckla graph-struktur. Azure Cosmos DB Gremlin-API är baserad på den [Apache TinkerPop](https://tinkerpop.apache.org) graph-databasen som är standard och använder Gremlin-frågespråket. 

Azure Cosmos DB Gremlin-API kombinerar kraften i graph databas algoritmer med mycket skalbar och hanterad infrastruktur för att ge en unik, flexibel lösning för att de vanligaste problem som är associerade med brist på flexibilitet och relationella metoder. 

## <a name="features-of-azure-cosmos-db-graph-database"></a>Funktioner i diagramdatabasen Azure Cosmos DB
 
Azure Cosmos DB är en fullständigt hanterad diagramdatabas som erbjuder global distribution, elastisk skalbarhet av lagring och dataflöde, automatisk indexering och frågor, justerbara konsekvensnivåer och stöd för TinkerPop-standarden. 

Följande är de differentierad funktionerna som erbjuder Azure Cosmos DB Gremlin-API:

* **Elastiska och skalbara dataflöden och lagring**

  Diagram i verkligheten behöver skala utöver kapaciteten för en enskild server. Azure Cosmos DB stöder horisontellt skalbar grafdatabaser som kan ha en nästan obegränsad storlek i och etablerade dataflöden. När graph databas skalan växer kan data distribueras automatiskt med hjälp av [grafpartitionering](https://docs.microsoft.com/azure/cosmos-db/graph-partitioning).

* **Replikering av flera regioner**

  Azure Cosmos DB kan automatiskt replikera din diagramdata till alla Azure-regioner. Replikering förenklar utvecklingen av program som kräver global åtkomst till data. Förutom minimerar lässvarstid tillhandahåller Azure Cosmos DB en mekanism för regional redundans som kan kontinuiteten i ditt program i de sällsynta fall om ett tjänstavbrott i en region. 

* **Snabba frågor och bläddringar med standarden mest välbekanta graph-fråga**

  Lagra heterogena brytpunkter och kanter och fråga de här dokumenten via ett bekant Gremlin-syntax. Gremlin är ett viktigt, funktions-frågespråk som tillhandahåller en omfattande gränssnitt för att implementera vanliga graph-algoritmer. 
  
  Azure Cosmos DB möjliggör omfattande förfrågningar i realtid och bläddringar utan att behöva ange schematips, sekundärindex eller vyer. Läs mer i [Fråga diagram med hjälp av Gremlin](gremlin-support.md).

* **Fullständigt hanterad grafdatabas**

  Azure Cosmos DB eliminerar behovet av att hantera databasen och datorresurserna. De flesta plattformar av graph-databasen är bundna till begränsningarna i sin infrastruktur och kräver ofta en hög grad av underhåll för att se till att dess drift. 
  
  Som en fullständigt hanterad Microsoft Azure-tjänst finns behöver du inte hantera virtuella datorer, uppdatera runtime-programvara, hantera horisontell partitionering eller replikering eller hantera komplexa datanivå uppgraderingar. Varje databas säkerhetskopieras och skyddas automatiskt mot regionala fel. Dessa garantier att utvecklare kan fokusera på att leverera program värde i stället för att använda och hantera sina databaser. 

* **Automatisk indexering**

  Som standard indexerar Azure Cosmos DB automatiskt alla egenskaperna inom noderna och kanterna i diagrammet och varken förväntar sig eller kräver något schema eller att sekundära index skapas. Läs mer om [indexering i Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/index-overview). 

* **Kompatibilitet med Apache TinkerPop**

  Azure Cosmos DB stöder den [öppen källkod Apache TinkerPop standard](http://tinkerpop.apache.org/). Tinkerpop standard har ett stort ekosystem av program och bibliotek som enkelt kan integreras med Azure Cosmos DB Gremlin-API. 

* **Justerbara konsekvensnivåer**

  Välj mellan fem väldefinierade konsekvensnivåer för bästa möjliga balans mellan konsekvens och prestanda. Azure Cosmos DB erbjuder fem olika konsekvensnivåer för frågor och läsåtgärder: stark, bunden utgång, session, enhetligt prefix och slutlig. Med de här detaljerade, väldefinierade konsekvensnivåerna kan du själv avgöra balansen mellan konsekvens, tillgänglighet och svarstid. Läs mer i [Justerbara datakonsekvensnivåer i Azure Cosmos DB](consistency-levels.md).

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

## <a name="introduction-to-graph-databases"></a>Introduktion till graph-databaser
Data som de visas i verkligheten är naturligt sammanlänkade. Traditionella datamodellering fokuserar på Definiera entiteter separat och databehandling deras relationer vid körning. Även om den här modellen har sina fördelar, mycket anslutna data kan vara en utmaning för att hantera under dess begränsningar.  

En metod för graph-databasen förlitar sig på bestående relationer i storage-skiktet i stället, vilket leder till högeffektiv graph datahämtningsrelaterade uppgifter. Azure Cosmos DB Gremlin API: et stöder den [egenskapen graph modellen](https://tinkerpop.apache.org/docs/current/reference/#intro).

### <a name="property-graph-objects"></a>Egenskapen graph-objekt

En egenskap [graph](http://mathworld.wolfram.com/Graph.html) är en struktur som består av [hörn](http://mathworld.wolfram.com/GraphVertex.html) och [kanter](http://mathworld.wolfram.com/GraphEdge.html). Båda-objekten kan ha ett valfritt antal nyckel / värde-par som egenskaper. 

* **Hörn** -hörn anger diskreta enheter, till exempel en person, plats eller en händelse.

* **Kanter** – Kanter betecknar relationer mellan brytpunkter. En person kan till exempel känna till en annan person, vara inblandad i en händelse och nyligen ha varit på en plats. 

* **Egenskaper** – Egenskaper uttrycker information om brytpunkterna och kanterna. Det kan vara valfritt antal egenskaper i hörn eller kanter och de kan användas för att beskriva och filtrera objekten i en fråga. Egenskaper för exempel omfattar ett hörn som har namnet och ålder eller en kant som kan ha en tidsstämpel och/eller en vikt. 

Grafdatabaser är ofta ingår i NoSQL eller icke-relationell databas kategori eftersom det finns inga beroende på ett schema eller begränsad datamodellen. Bristen på schemat kan modellering och lagrar anslutna strukturer naturligt och effektivt. 

### <a name="gremlin-by-example"></a>Gremlin efter exempel
Nu ska vi använda ett exempeldiagram för att förstå hur frågor kan uttryckas i Gremlin. Följande bild visar ett affärsprogram som hanterar data om användare, intressen och enheter i form av ett diagram.  

![Exempeldatabas som visar personer, enheter och intressen](./media/gremlin-support/sample-graph.png) 

Det här diagrammet innehåller följande brytpunktstyper (kallas etikett i Gremlin):

- Personer: Diagrammet innehåller tre personer, Robin, Thomas och Ben
- Intressen: Deras intressen, i det här exemplet, Fotboll
- Enheter: De enheter som personerna använder
- Operativsystem: De operativsystem som enheterna körs på

Vi representerar relationerna mellan dessa entiteter via följande kanttyper/etiketter:

- Känner: till exempel, Thomas känner Robin
- Intressen: Representerar intressena för personerna i vårt diagram, till exempel Ben är intresserad av fotboll
- KörOS: den bärbara datorn kör Windows-operativsystemet
- Använder: Representerar vilken enhet som en person använder. Till exempel Robin använder en Motorola-telefon med serienummer 77

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

Där diagram verkligen kommer till sin rätt är när det gäller att svara på frågor som Vilka operativsystem använder Thomas vänner? Du kan köra den här Gremlin-genomgång för att hämta informationen från diagrammet:

```java
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```
Nu ska vi titta på vad Azure Cosmos DB tillhandahåller för Gremlin-utvecklare.

## <a name="next-steps"></a>Nästa steg
Mer information om diagramstöd i Azure Cosmos DB finns i:

* Kom igång med [Självstudien Azure Cosmos DB-diagram](create-graph-dotnet.md).
* Läs mer om hur du [frågar diagram i Azure Cosmos DB med hjälp av Gremlin](gremlin-support.md).
