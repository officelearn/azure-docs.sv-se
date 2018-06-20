---
title: Azure Cosmos DB Gremlin-stöd | Microsoft Docs
description: Läs mer om Gremlin-språket från Apache TinkerPop. Läs vilka funktioner och steg som finns tillgängliga i Azure Cosmos DB
services: cosmos-db
author: LuisBosquez
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.devlang: na
ms.topic: overview
ms.date: 01/02/2018
ms.author: lbosq
ms.openlocfilehash: c675f37e50f5b8a259048d9a92fcdbe5b947068c
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34797625"
---
# <a name="azure-cosmos-db-gremlin-graph-support"></a>Stöd för Azure Cosmos DB Gremlin-diagram
Azure Cosmos DB stöder [Apache Tinkerpops](http://tinkerpop.apache.org) diagrambläddringsspråk [Gremlin](http://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps), vilket är en Graph API för att skapa diagramentiteter och utföra diagramfrågeåtgärder. Du kan använda Gremlin-språket för att skapa diagramentiteter (brytpunkter och kanter), ändra egenskaper inom de entiteterna, utföra frågor och bläddringar samt ta bort entiteter. 

Azure Cosmos DB ger dig företagsklara funktioner diagramdatabaser. Det inkluderar global distribution, oberoende skalning av lagring och dataflöde, förutsägbara svarstider i ensiffriga millisekunder, automatisk indexering, serviceavtal, lästillgänglighet för databaskonton som sträcker sig över två eller flera Azure-regioner. Eftersom Azure Cosmos DB stöder TinkerPop/Gremlin, kan du enkelt migrera program som skrivits med en annan daigramdatabas utan att behöva göra kodändringar. Dessutom tack vare stöd för Gremlin, integreras Azure Cosmos DB smidigt med TinkerPop-aktiverade ramverk för analys som [Apache Spark GraphX](http://spark.apache.org/graphx/). 

I den här artikeln ger vi en snabb genomgång av Gremlin och räknar upp de Gremlin-funktioner och steg som stöds av Graph API:t.

## <a name="gremlin-by-example"></a>Gremlin efter exempel
Nu ska vi använda ett exempeldiagram för att förstå hur frågor kan uttryckas i Gremlin. Följande bild visar ett affärsprogram som hanterar data om användare, intressen och enheter i form av ett diagram.  

![Exempeldatabas som visar personer, enheter och intressen](./media/gremlin-support/sample-graph.png) 

Det här diagrammet innehåller följande brytpunktstyper (kallas etikett i Gremlin):

- Personer: diagrammet innehåller tre personer, Robin, Thomas och Ben
- Intressen: Deras intressen, i det här exemplet, Fotboll
- Enheter: De enheter som personerna använder
- Operativsystem: De operativsystem som enheterna körs på

Vi representerar relationerna mellan dessa entiteter via följande kanttyper/etiketter:

- Känner: till exempel Thomas känner Robin
- Intressen: representerar intressena för personerna i vårt diagram, till exempel Ben är intresserad av fotboll
- KörOS: den bärbara datorn kör Windows-operativsystemet
- Användning: Representerar vilken enhet som en person använder. Till exempel Robin använder en Motorola-telefon med serienummer 77

Vi kör några åtgärder mot det här diagrammet med [Gremlin-konsolen](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console). Du kan också utföra dessa åtgärder med hjälp av Gremlin-drivrutinerna i den plattform du vill (Java, Node.js, Python eller .NET).  Innan vi tittar på vad som stöds i Azure Cosmos DB ska vi titta på några exempel för att bekanta oss med syntaxen.

Först ska vi titta på CRUD. Följande Gremlin-uttryck infogar Thomas-brytpunkten i diagrammet:

```
:> g.addV('person').property('id', 'thomas.1').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

Därefter infogar följande Gremlin-uttryck en känner-kant mellan Thomas och Robin.

```
:> g.V('thomas.1').addE('knows').to(g.V('robin.1'))
```

Följande fråga returnerar person-brytpunkten i fallande ordning efter deras förnamn:
```
:> g.V().hasLabel('person').order().by('firstName', decr)
```

Där diagram verkligen kommer till sin rätt är när det gäller att svara på frågor som Vilka operativsystem använder Thomas vänner? Du kan köra den här enkla Gremlin-bläddringen för att få ut den informationen från diagrammet:

```
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```
Nu ska vi titta på vad Azure Cosmos DB tillhandahåller för Gremlin-utvecklare.

## <a name="gremlin-features"></a>Gremlin-funktioner
TinkerPop är en standard som omfattar en mängd olika diagramtekniker. Därför har den standardterminologi som beskriver vilka funktioner som tillhandahålls av en diagramprovider. Azure Cosmos DB tillhandahåller en beständig, skrivbar diagramdatabas med hög samtidighet som kan partitioneras över flera servrar eller kluster. 

Följande tabell visar den TinkerPop-funktioner som implementeras av Azure Cosmos DB: 

| Kategori | Azure Cosmos DB-implementering |  Anteckningar | 
| --- | --- | --- |
| Diagramfunktioner | Ger beständighet och ConcurrentAccess. Designad att stödja transaktioner | Datormetoder kan implementeras via Spark-anslutningsappen. |
| Variabla funktioner | Stöder boolesk, heltal, byte, dubbel, flyttal, heltal, lång, sträng | Har stöd för primitiva typer, är kompatibel med komplexa typer via datamodellen |
| Brytpunktsfunktioner | Stöder RemoveVertices, MetaProperties, AddVertices, MultiProperties, StringIds, UserSuppliedIds, AddProperty, RemoveProperty  | Stöder att skapa, ändra och ta bort brytpunkter |
| Funktioner för brytpunktsegenskapen | StringIds, UserSuppliedIds, AddProperty, RemoveProperty, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Stöder att skapa, ändra och ta bort brytpunktsegenskaper |
| Kantfunktioner | AddEdges, RemoveEdges, StringIds, UserSuppliedIds, AddProperty, RemoveProperty | Stöder att skapa, ändra och ta bort kanter |
| Kantegenskapsfunktioner | Properties, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Stöder att skapa, ändra och ta bort kantegenskaper |

## <a name="gremlin-wire-format-graphson"></a>Gremlin-trådformat: GraphSON

Azure Cosmos DB använder [GraphSON-formatet](https://github.com/thinkaurelius/faunus/wiki/GraphSON-Format) när du returnerar resultat från Gremlin-åtgärder. GraphSON är Gremlin-standardformatet för att representera brytpunkter, kanter och egenskaper (egenskaper med enkla och flera värden) med JSON. 

Följande kodavsnitt visar exempelvis en GraphSON-representation av en brytpunkt *returneras till klienten* från Azure Cosmos DB. 

```json
  {
    "id": "a7111ba7-0ea1-43c9-b6b2-efc5e3aea4c0",
    "label": "person",
    "type": "vertex",
    "outE": {
      "knows": [
        {
          "id": "3ee53a60-c561-4c5e-9a9f-9c7924bc9aef",
          "inV": "04779300-1c8e-489d-9493-50fd1325a658"
        },
        {
          "id": "21984248-ee9e-43a8-a7f6-30642bc14609",
          "inV": "a8e3e741-2ef7-4c01-b7c8-199f8e43e3bc"
        }
      ]
    },
    "properties": {
      "firstName": [
        {
          "value": "Thomas"
        }
      ],
      "lastName": [
        {
          "value": "Andersen"
        }
      ],
      "age": [
        {
          "value": 45
        }
      ]
    }
  }
```

De egenskaper som används av GraphSON för brytpunkterna är följande:

| Egenskap | Beskrivning |
| --- | --- |
| id | ID för brytpunkten. Måste vara unika (i kombination med värdet av _partition om tillämpligt) |
| etikett | Etiketten för brytpunkten. Det här är valfritt och används för att beskriva entitetstypen. |
| typ | Används för att särskilja brytpunkter från icke-diagramdokument |
| properties | En uppsättning användardefinierade egenskaper associerade med brytpunkten. Varje egenskap kan ha flera värden. |
| _partition (konfigurerbar) | Partitionsnyckeln för brytpunkten. Kan användas för att skala ut diagram till flera servrar |
| outE | Innehåller en lista över kanterna från en brytpunkt. Lagring av angränsande information med brytpunkter för snabbare körning av bläddring. Kanter grupperas baserat på deras etiketter. |

Och kanten innehåller följande information för att underlätta navigeringen till andra delar av diagrammet.

| Egenskap | Beskrivning |
| --- | --- |
| id | ID för kanten. Måste vara unika (i kombination med värdet av _partition om tillämpligt) |
| etikett | Etiketten för kanten. Den här egenskapen är valfri och används för att beskriva relationstypen. |
| inV | Innehåller en lista över in-brytpunkter för en kant. Lagring av angränsningsinformation med kanter tillåter snabb körning av bläddringar. Brytpunkter grupperas baserat på deras etiketter. |
| properties | En uppsättning användardefinierade egenskaper associerade med kanten. Varje egenskap kan ha flera värden. |

Varje egenskap kan lagra flera värden inom en matris. 

| Egenskap | Beskrivning |
| --- | --- |
| värde | Värdet på egenskapen

## <a name="gremlin-steps"></a>Gremlin-steg
Nu ska vi titta på de Gremlin-steg som stöds av Azure Cosmos DB. En fullständig referens om Gremlin finns i [TinkerPop-referens](http://tinkerpop.apache.org/docs/current/reference).

| steg | Beskrivning | TinkerPop 3.2-dokumentation |
| --- | --- | --- |
| `addE` | Lägger till en kant mellan två brytpunkter | [addE step](http://tinkerpop.apache.org/docs/current/reference/#addedge-step) |
| `addV` | Lägger till en brytpunkt i diagrammet | [addV step](http://tinkerpop.apache.org/docs/current/reference/#addvertex-step) |
| `and` | Ser till att alla bläddringar returnerar ett värde | [and step](http://tinkerpop.apache.org/docs/current/reference/#and-step) |
| `as` | Ett stegmodulator för att tilldela en variabel till utdata från ett steg | [as step](http://tinkerpop.apache.org/docs/current/reference/#as-step) |
| `by` | En stegmodulator som används med `group` och `order` | [by step](http://tinkerpop.apache.org/docs/current/reference/#by-step) |
| `coalesce` | Returnerar den första bläddringen som returnerar ett resultat | [coalesce step](http://tinkerpop.apache.org/docs/current/reference/#coalesce-step) |
| `constant` | Returnerar ett konstant värde. Används med `coalesce`| [constant step](http://tinkerpop.apache.org/docs/current/reference/#constant-step) |
| `count` | Returnerar antalet från bläddringen | [count step](http://tinkerpop.apache.org/docs/current/reference/#count-step) |
| `dedup` | Returnerar värden med borttagna dubbletter | [dedup step](http://tinkerpop.apache.org/docs/current/reference/#dedup-step) |
| `drop` | Släpper värdena (brytpunkt/kant) | [drop step](http://tinkerpop.apache.org/docs/current/reference/#drop-step) |
| `fold` | Fungerar som en barriär som beräknar sammanställningen av resultat| [fold step](http://tinkerpop.apache.org/docs/current/reference/#fold-step) |
| `group` | Grupperar värdena baserat på de angivna etiketterna| [group step](http://tinkerpop.apache.org/docs/current/reference/#group-step) |
| `has` | Används för att filtrera egenskaper, brytpunkter och kanter. Stöder varianterna `hasLabel`, `hasId`, `hasNot` och `has`. | [has step](http://tinkerpop.apache.org/docs/current/reference/#has-step) |
| `inject` | Matar in värden i en dataström| [inject step](http://tinkerpop.apache.org/docs/current/reference/#inject-step) |
| `is` | Används för att utföra ett filter med ett booleskt uttryck | [is step](http://tinkerpop.apache.org/docs/current/reference/#is-step) |
| `limit` | Används för att begränsa antalet objekt i bläddringen| [limit step](http://tinkerpop.apache.org/docs/current/reference/#limit-step) |
| `local` | Bäddar in ett avsnitt av en bläddring lokalt, liknar en underfråga | [local step](http://tinkerpop.apache.org/docs/current/reference/#local-step) |
| `not` | Används för att skapa negationer av ett filter | [not step](http://tinkerpop.apache.org/docs/current/reference/#not-step) |
| `optional` | Returnerar resultatet av den angivna bläddringen om den ger upphov till ett resultat, annars returneras det anropande elementet | [optional step](http://tinkerpop.apache.org/docs/current/reference/#optional-step) |
| `or` | Garanterar att minst en av bläddringarna returnerar ett värde | [or step](http://tinkerpop.apache.org/docs/current/reference/#or-step) |
| `order` | Returnerar resultat i den angivna sorteringsordningen | [order step](http://tinkerpop.apache.org/docs/current/reference/#order-step) |
| `path` | Returnerar den fullständiga sökvägen för bläddringen | [path step](http://tinkerpop.apache.org/docs/current/reference/#path-step) |
| `project` | Projicerar egenskaperna som en karta | [project step](http://tinkerpop.apache.org/docs/current/reference/#project-step) |
| `properties` | Returnerar egenskaperna för de angivna etiketterna | [properties step](http://tinkerpop.apache.org/docs/current/reference/#properties-step) |
| `range` | Filtrerar till det angivna intervallet med värden| [range step](http://tinkerpop.apache.org/docs/current/reference/#range-step) |
| `repeat` | Upprepar steget för det angivna antalet gånger. Används för upprepning | [repeat step](http://tinkerpop.apache.org/docs/current/reference/#repeat-step) |
| `sample` | Används för exempelresultat för bläddringen | [sample step](http://tinkerpop.apache.org/docs/current/reference/#sample-step) |
| `select` | Används för att projicera resultat från bläddringen |  [select step](http://tinkerpop.apache.org/docs/current/reference/#select-step) | |
| `store` | Används för icke-blockerande sammanställningar från bläddringen | [store step](http://tinkerpop.apache.org/docs/current/reference/#store-step) |
| `tree` | Sammanställ sökvägar från en brytpunkt i ett träd | [tree step](http://tinkerpop.apache.org/docs/current/reference/#tree-step) |
| `unfold` | Rulla upp en iterator som ett steg| [unfold step](http://tinkerpop.apache.org/docs/current/reference/#unfold-step) |
| `union` | Sammanfoga resultat från flera bläddringar| [union step](http://tinkerpop.apache.org/docs/current/reference/#union-step) |
| `V` | Inkluderar de steg som krävs för bläddringar mellan brytpunkter och kanter `V`, `E`, `out`, `in`, `both`, `outE`, `inE`, `bothE`, `outV`, `inV`, `bothV` och `otherV` för | [vertex steps](http://tinkerpop.apache.org/docs/current/reference/#vertex-steps) |
| `where` | Används för att filtrera resultat från bläddringen. Stöder operatorerna `eq`, `neq`, `lt`, `lte`, `gt`, `gte` och `between`  | [where step](http://tinkerpop.apache.org/docs/current/reference/#where-step) |

Den skrivoptimerade motorn som tillhandahålls av Azure Cosmos DB stöder automatisk indexering av alla egenskaperna i brytpunkter och kanter som standard. Därför bearbetas frågor med filter, intervallfrågor, sortering eller sammanställning av alla egenskaper från index och hanteras effektivt. Mer information om hur indexering fungerar i Azure Cosmos DV finns i vårt papper om [schemaoberoende indexering](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf).

## <a name="next-steps"></a>Nästa steg
* Kom igång med att skapa ett diagramprogram [med våra SDK:er](create-graph-dotnet.md) 
* Läs mer om [diagramstöd](graph-introduction.md) i Azure Cosmos DB
