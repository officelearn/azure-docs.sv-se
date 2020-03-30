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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "75500005"
---
# <a name="introduction-to-azure-cosmos-db-gremlin-api"></a>Introduktion till Azure Cosmos DB: Gremlin-API

[Azure Cosmos DB](introduction.md) är den globalt distribuerade databastjänsten med flera modeller från Microsoft för verksamhetskritiska program. Det är en databas med flera modeller och stöder datamodeller för dokument, nyckelvärde, diagram och kolumnfamilj. Azure Cosmos DB Gremlin API används för att lagra och arbeta med grafdata på en fullständigt hanterad databastjänst som utformats för alla skalningar.  

![Azure Cosmos DB-diagramarkitektur](./media/graph-introduction/cosmosdb-graph-architecture.png)

Den här artikeln ger en översikt över Azure Cosmos DB Gremlin-API och förklarar hur du kan använda den för att lagra massiva diagram med miljarder brytpunkter och kanter. Du kan fråga diagrammen med millisekunds svarstid och enkelt utveckla grafstrukturen. Azure Cosmos DB:s Gremlin API baseras på [Apache TinkerPop-grafdatabasstandarden](https://tinkerpop.apache.org) och använder Gremlin-frågespråket. 

Azure Cosmos DB:s Gremlin API kombinerar kraften i grafdatabasalgoritmer med mycket skalbar, hanterad infrastruktur för att tillhandahålla en unik, flexibel lösning på de vanligaste dataproblemen i samband med bristande flexibilitet och relationsmetoder. 

## <a name="features-of-azure-cosmos-db-graph-database"></a>Funktioner i diagramdatabasen Azure Cosmos DB
 
Azure Cosmos DB är en fullständigt hanterad diagramdatabas som erbjuder global distribution, elastisk skalbarhet av lagring och dataflöde, automatisk indexering och frågor, justerbara konsekvensnivåer och stöd för TinkerPop-standarden. 

Följande är de differentierade funktioner som Azure Cosmos DB Gremlin API erbjuder:

* **Elastiskt skalbara dataflöden och lagring**

  Diagram i verkligheten behöver skala utöver kapaciteten för en enskild server. Azure Cosmos DB stöder horisontellt skalbara grafdatabaser som kan ha en praktiskt taget obegränsad storlek när det gäller lagring och etablerat dataflöde. När skalan av grafdatabasen växer distribueras data automatiskt med hjälp av [grafpartitionering](https://docs.microsoft.com/azure/cosmos-db/graph-partitioning).

* **Replikering för flera regioner**

  Azure Cosmos DB kan automatiskt replikera dina grafdata till alla Azure-regioner över hela världen. Global replikering förenklar utvecklingen av program som kräver global åtkomst till data. Förutom att minimera läs- och skrivfördröjning var som helst i världen tillhandahåller Azure Cosmos DB automatisk regional redundansmekanism som kan säkerställa kontinuiteten i ditt program i sällsynta fall av ett avbrott i en region. 

* **Snabba frågor och traversals med den mest antagna graffrågestandarden**

  Lagra heterogena hörn och kanter och fråga dem genom en välbekant Gremlin-syntax. Gremlin är ett absolut nödvändigt, funktionellt frågespråk som ger ett omfattande gränssnitt för att implementera vanliga grafalgoritmer. 
  
  Azure Cosmos DB möjliggör omfattande realtidsfrågor och traversals utan att behöva ange schematips, sekundära index eller vyer. Läs mer i [Fråga diagram med hjälp av Gremlin](gremlin-support.md).

* **Fullständigt hanterad grafdatabas**

  Azure Cosmos DB eliminerar behovet av att hantera databasen och datorresurserna. De flesta befintliga grafdatabasplattformar är bundna till begränsningarna i sin infrastruktur och kräver ofta en hög grad av underhåll för att säkerställa dess funktion. 
  
  Som en fullständigt hanterad tjänst tar Cosmos DB bort behovet av att hantera virtuella datorer, uppdatera körningsprogram, hantera fragmentering eller replikering eller hantera komplexa uppgraderingar på datanivå. Varje databas säkerhetskopieras och skyddas automatiskt mot regionala fel. Dessa garantier gör det möjligt för utvecklare att fokusera på att leverera programvärde i stället för att driva och hantera sina grafdatabaser. 

* **Automatisk indexering**

  Som standard indexerar Azure Cosmos DB automatiskt alla egenskaperna inom noderna och kanterna i diagrammet och varken förväntar sig eller kräver något schema eller att sekundära index skapas. Läs mer om [indexering i Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/index-overview). 

* **Kompatibilitet med Apache TinkerPop**

  Azure Cosmos DB stöder [Apache TinkerPop-standarden med öppen källkod](https://tinkerpop.apache.org/). Tinkerpop-standarden har ett stort ekosystem av program och bibliotek som enkelt kan integreras med Azure Cosmos DB:s Gremlin API. 

* **Justerbara konsekvensnivåer**

  Azure Cosmos DB ger fem väldefinierade konsekvensnivåer för att uppnå rätt avvägning mellan konsekvens och prestanda för ditt program. Azure Cosmos DB erbjuder fem olika konsekvensnivåer för frågor och läsåtgärder: stark, bunden utgång, session, enhetligt prefix och slutlig. Med de här detaljerade, väldefinierade konsekvensnivåerna kan du själv avgöra balansen mellan konsekvens, tillgänglighet och svarstid. Läs mer i [Justerbara datakonsekvensnivåer i Azure Cosmos DB](consistency-levels.md).

## <a name="scenarios-that-can-use-gremlin-api"></a>Scenarier där Gremlin-API:t kan användas
Här är några scenarier där diagramstöd för Azure Cosmos DB kan vara användbart:

* **Sociala nätverk/Kund 365**

  Genom att kombinera data om dina kunder och deras interaktioner med andra personer kan du utveckla anpassade upplevelser, förutsäga kundbeteende eller ansluta personer med andra med liknande intressen. Azure Cosmos-DB kan användas för att hantera sociala nätverk och spåra kundinställningar och data.

* **Rekommendationsmotorer**

  Det här scenariot används ofta inom detaljhandeln. Genom att kombinera information om produkter, användare och användarinteraktioner som köp, bläddring eller klassificeringen av ett objekt, kan du skapa anpassade rekommendationer. Stöd för azure Cosmos DB är perfekt för de här scenarierna med låg latens, elastisk skala och inbyggt diagram.

* **Geospatial**

  Många program inom telekommunikation, logistik och reseplanering måste hitta en plats av intresse inom ett område eller hitta den kortaste/optimala vägen mellan två platser. Azure Cosmos DB är en naturlig lösning på de här problemen.

* **Internet of Things**

  Med nätverket och anslutningarna mellan IoT-enheter som modellerats som ett diagram, kan du skapa en bättre förståelse för dina enheters och tillgångars tillstånd. Du kan också undersöka hur ändringar i en del av nätverket kan påverka en annan del.

## <a name="introduction-to-graph-databases"></a>Introduktion till grafdatabaser
Data som de visas i verkligheten är naturligt sammanlänkade. Traditionell datamodellering fokuserar på att definiera enheter separat och beräkna deras relationer vid körning. Även om den här modellen har sina fördelar kan mycket anslutna data vara utmanande att hantera under dess begränsningar.  

En diagramdatabasmetod bygger på beständiga relationer i lagringslagret i stället, vilket leder till mycket effektiva grafhämtningsåtgärder. Azure Cosmos DB:s Gremlin API stöder [egenskapsdiagrammodellen](https://tinkerpop.apache.org/docs/current/reference/#intro).

### <a name="property-graph-objects"></a>Egenskapsdiagramobjekt

Ett [egenskapsdiagram](http://mathworld.wolfram.com/Graph.html) är en struktur som består av [hörn](http://mathworld.wolfram.com/GraphVertex.html) och [kanter](http://mathworld.wolfram.com/GraphEdge.html). Båda objekten kan ha ett godtyckligt antal nyckel-värde-par som egenskaper. 

* **Hörn** - Hörn betecknar diskreta entiteter, till exempel en person, en plats eller en händelse.

* **Kanter** – Kanter betecknar relationer mellan brytpunkter. En person kan till exempel känna till en annan person, vara inblandad i en händelse och nyligen ha varit på en plats. 

* **Egenskaper** – Egenskaper uttrycker information om brytpunkterna och kanterna. Det kan finnas valfritt antal egenskaper i antingen hörn eller kanter, och de kan användas för att beskriva och filtrera objekten i en fråga. Exempelegenskaper är ett hörn som har namn och ålder, eller en kant, som kan ha en tidsstämpel och/eller en vikt. 

Diagramdatabaser ingår ofta i nosql-kategorin eller icke-relationsdatabaskategorin, eftersom det inte finns något beroende av ett schema eller begränsad datamodell. Denna brist på schema möjliggör modellering och lagring av anslutna strukturer naturligt och effektivt. 

### <a name="gremlin-by-example"></a>Gremlin efter exempel
Nu ska vi använda ett exempeldiagram för att förstå hur frågor kan uttryckas i Gremlin. Följande bild visar ett affärsprogram som hanterar data om användare, intressen och enheter i form av ett diagram.  

![Exempeldatabas som visar personer, enheter och intressen](./media/gremlin-support/sample-graph.png) 

Det här diagrammet har följande *hörntyper* (kallas "etikett" i Gremlin):

- **Människor**: Diagrammet har tre personer, Robin, Thomas och Ben
- **Intressen:** Deras intressen, i detta exempel, spelet fotboll
- **Enheter**: De enheter som människor använder
- **Operativsystem:** De operativsystem som enheterna körs på

Vi representerar relationerna mellan dessa *edge* entiteter via följande kanttyper/-etiketter:

- **Vet:** Till exempel "Thomas känner Robin"
- **Intresserad:** Att företräda folkets intressen i vårt diagram, till exempel"Ben är intresserad av fotboll"
- **RunsOS**: Laptop kör Windows OS
- **Använder**: För att representera vilken enhet en person använder. Till exempel Robin använder en Motorola-telefon med serienummer 77

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

Där diagram verkligen kommer till sin rätt är när det gäller att svara på frågor som Vilka operativsystem använder Thomas vänner? Du kan köra denna Gremlin traversal för att få den informationen från diagrammet:

```java
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```

## <a name="next-steps"></a>Nästa steg
Mer information om diagramstöd i Azure Cosmos DB finns i:

* Kom igång med [Självstudien Azure Cosmos DB-diagram](create-graph-dotnet.md).
* Läs mer om hur du [frågar diagram i Azure Cosmos DB med hjälp av Gremlin](gremlin-support.md).
