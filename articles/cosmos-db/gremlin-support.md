---
title: Stöd för Gremlin i Azure Cosmos DB
description: Läs mer om Gremlin-språket från Apache TinkerPop. Läs vilka funktioner och steg som finns tillgängliga i Azure Cosmos DB
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 06/24/2019
ms.author: lbosq
ms.openlocfilehash: 564e69e3cd852c6a0f8c20278d4742b77f064298
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "75499988"
---
# <a name="azure-cosmos-db-gremlin-graph-support"></a>Stöd för Azure Cosmos DB Gremlin-diagram
Azure Cosmos DB stöder [Apache Tinkerpops](https://tinkerpop.apache.org) grafkorsspråk, kallat [Gremlin](https://tinkerpop.apache.org/docs/3.3.2/reference/#graph-traversal-steps). Du kan använda Gremlin-språket för att skapa diagramentiteter (brytpunkter och kanter), ändra egenskaper inom de entiteterna, utföra frågor och bläddringar samt ta bort entiteter. 

I den här artikeln ger vi en snabb genomgång av Gremlin och räknar upp Gremlin-funktionerna som stöds av Gremlin-API:et.

## <a name="compatible-client-libraries"></a>Kompatibla klientbibliotek

Följande tabell visar populära Gremlin-drivrutiner som du kan använda mot Azure Cosmos DB:

| Ladda ned | Källa | Komma igång | Version av anslutningsappen som stöds |
| --- | --- | --- | --- |
| [.NET](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-DotNet) | [Gremlin.NET på GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-dotnet) | [Skapa diagram med .NET](create-graph-dotnet.md) | 3.4.0-RC2 |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) | [Gremlin JavaDoc](https://tinkerpop.apache.org/javadocs/current/full/) | [Skapa diagram med Java](create-graph-java.md) | 3.2.0+ |
| [Node.js](https://www.npmjs.com/package/gremlin) | [Gremlin-JavaScript på GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-javascript) | [Skapa diagram med Node.js](create-graph-nodejs.md) | 3.3.4+ |
| [Python](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-python) | [Gremlin-Python på GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-python) | [Skapa diagram med Python](create-graph-python.md) | 3.2.7 |
| [Php](https://packagist.org/packages/brightzone/gremlin-php) | [Gremlin-PHP på GitHub](https://github.com/PommeVerte/gremlin-php) | [Skapa diagram med PHP](create-graph-php.md) | 3.1.0 |
| [Gremlin-konsol](https://tinkerpop.apache.org/downloads.html) | [TinkerPop-dokument](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |  [Skapa diagram med Gremlin-konsolen](create-graph-gremlin-console.md) | 3.2.0 + |

## <a name="supported-graph-objects"></a>Grafobjekt som stöds
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

Azure Cosmos DB använder [GraphSON-formatet](https://tinkerpop.apache.org/docs/current/reference/#graphson) när du returnerar resultat från Gremlin-åtgärder. Azure Cosmos DB stöder för närvarande "GraphSONv2"-version. GraphSON är Gremlin-standardformatet för att representera brytpunkter, kanter och egenskaper (egenskaper med enkla och flera värden) med JSON.

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

De egenskaper som används av GraphSON för hörn beskrivs nedan:

| Egenskap | Beskrivning | 
| --- | --- | --- |
| `id` | ID för brytpunkten. Måste vara unik (i kombination `_partition` med värdet av om tillämpligt). Om inget värde anges kommer det automatiskt att levereras med ett GUID | 
| `label` | Etiketten för brytpunkten. Den här egenskapen används för att beskriva entitetstypen. |
| `type` | Används för att särskilja brytpunkter från icke-diagramdokument |
| `properties` | En uppsättning användardefinierade egenskaper associerade med brytpunkten. Varje egenskap kan ha flera värden. |
| `_partition` | Partitionsnyckeln för brytpunkten. Används för [grafpartitionering](graph-partitioning.md). |
| `outE` | Den här egenskapen innehåller en lista över utkanter från ett hörn. Lagring av angränsande information med brytpunkter för snabbare körning av bläddring. Kanter grupperas baserat på deras etiketter. |

Och kanten innehåller följande information för att underlätta navigeringen till andra delar av diagrammet.

| Egenskap | Beskrivning |
| --- | --- |
| `id` | ID för kanten. Måste vara unikt (i kombination `_partition` med värdet av om tillämpligt) |
| `label` | Etiketten för kanten. Den här egenskapen är valfri och används för att beskriva relationstypen. |
| `inV` | Den här egenskapen innehåller en lista över hörn för en kant. Lagring av angränsningsinformation med kanter tillåter snabb körning av bläddringar. Brytpunkter grupperas baserat på deras etiketter. |
| `properties` | En uppsättning användardefinierade egenskaper associerade med kanten. Varje egenskap kan ha flera värden. |

Varje egenskap kan lagra flera värden inom en matris. 

| Egenskap | Beskrivning |
| --- | --- |
| `value` | Värdet på egenskapen

## <a name="gremlin-steps"></a>Gremlin-steg
Nu ska vi titta på de Gremlin-steg som stöds av Azure Cosmos DB. En fullständig referens om Gremlin finns i [TinkerPop-referens](https://tinkerpop.apache.org/docs/3.3.2/reference).

| steg | Beskrivning | TinkerPop 3.2-dokumentation |
| --- | --- | --- |
| `addE` | Lägger till en kant mellan två brytpunkter | [addE step](https://tinkerpop.apache.org/docs/3.3.2/reference/#addedge-step) |
| `addV` | Lägger till en brytpunkt i diagrammet | [addV step](https://tinkerpop.apache.org/docs/3.3.2/reference/#addvertex-step) |
| `and` | Ser till att alla bläddringar returnerar ett värde | [and step](https://tinkerpop.apache.org/docs/3.3.2/reference/#and-step) |
| `as` | Ett stegmodulator för att tilldela en variabel till utdata från ett steg | [as step](https://tinkerpop.apache.org/docs/3.3.2/reference/#as-step) |
| `by` | En stegmodulator som används med `group` och `order` | [by step](https://tinkerpop.apache.org/docs/3.3.2/reference/#by-step) |
| `coalesce` | Returnerar den första bläddringen som returnerar ett resultat | [coalesce step](https://tinkerpop.apache.org/docs/3.3.2/reference/#coalesce-step) |
| `constant` | Returnerar ett konstant värde. Används med `coalesce`| [constant step](https://tinkerpop.apache.org/docs/3.3.2/reference/#constant-step) |
| `count` | Returnerar antalet från bläddringen | [count step](https://tinkerpop.apache.org/docs/3.3.2/reference/#count-step) |
| `dedup` | Returnerar värden med borttagna dubbletter | [dedup step](https://tinkerpop.apache.org/docs/3.3.2/reference/#dedup-step) |
| `drop` | Släpper värdena (brytpunkt/kant) | [drop step](https://tinkerpop.apache.org/docs/3.3.2/reference/#drop-step) |
| `executionProfile` | Skapar en beskrivning av alla åtgärder som genereras av det utförda Gremlin-steget | [steget executionProfile](graph-execution-profile.md) |
| `fold` | Fungerar som en barriär som beräknar sammanställningen av resultat| [fold step](https://tinkerpop.apache.org/docs/3.3.2/reference/#fold-step) |
| `group` | Grupperar värdena baserat på de angivna etiketterna| [group step](https://tinkerpop.apache.org/docs/3.3.2/reference/#group-step) |
| `has` | Används för att filtrera egenskaper, brytpunkter och kanter. Stöder varianterna `hasLabel`, `hasId`, `hasNot` och `has`. | [has step](https://tinkerpop.apache.org/docs/3.3.2/reference/#has-step) |
| `inject` | Matar in värden i en dataström| [inject step](https://tinkerpop.apache.org/docs/3.3.2/reference/#inject-step) |
| `is` | Används för att utföra ett filter med ett booleskt uttryck | [is step](https://tinkerpop.apache.org/docs/3.3.2/reference/#is-step) |
| `limit` | Används för att begränsa antalet objekt i bläddringen| [limit step](https://tinkerpop.apache.org/docs/3.3.2/reference/#limit-step) |
| `local` | Bäddar in ett avsnitt av en bläddring lokalt, liknar en underfråga | [local step](https://tinkerpop.apache.org/docs/3.3.2/reference/#local-step) |
| `not` | Används för att skapa negationer av ett filter | [not step](https://tinkerpop.apache.org/docs/3.3.2/reference/#not-step) |
| `optional` | Returnerar resultatet av den angivna bläddringen om den ger upphov till ett resultat, annars returneras det anropande elementet | [valfritt steg](https://tinkerpop.apache.org/docs/3.3.2/reference/#optional-step) |
| `or` | Garanterar att minst en av bläddringarna returnerar ett värde | [or step](https://tinkerpop.apache.org/docs/3.3.2/reference/#or-step) |
| `order` | Returnerar resultat i den angivna sorteringsordningen | [order step](https://tinkerpop.apache.org/docs/3.3.2/reference/#order-step) |
| `path` | Returnerar den fullständiga sökvägen för bläddringen | [path step](https://tinkerpop.apache.org/docs/3.3.2/reference/#path-step) |
| `project` | Projicerar egenskaperna som en karta | [project step](https://tinkerpop.apache.org/docs/3.3.2/reference/#project-step) |
| `properties` | Returnerar egenskaperna för de angivna etiketterna | [properties step](https://tinkerpop.apache.org/docs/3.3.2/reference/#_properties_step) |
| `range` | Filtrerar till det angivna intervallet med värden| [range step](https://tinkerpop.apache.org/docs/3.3.2/reference/#range-step) |
| `repeat` | Upprepar steget för det angivna antalet gånger. Används för upprepning | [repeat step](https://tinkerpop.apache.org/docs/3.3.2/reference/#repeat-step) |
| `sample` | Används för exempelresultat för bläddringen | [sample step](https://tinkerpop.apache.org/docs/3.3.2/reference/#sample-step) |
| `select` | Används för att projicera resultat från bläddringen |  [select step](https://tinkerpop.apache.org/docs/3.3.2/reference/#select-step) |
| `store` | Används för icke-blockerande sammanställningar från bläddringen | [store step](https://tinkerpop.apache.org/docs/3.3.2/reference/#store-step) |
| `TextP.startingWith(string)` | Strängfiltreringsfunktion. Den här funktionen används som predikat för `has()` steget för att matcha en egenskap med början av en viss sträng | [TextP predikater](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.endingWith(string)` |  Strängfiltreringsfunktion. Den här funktionen används som predikat för `has()` steget för att matcha en egenskap med slutet av en viss sträng | [TextP predikater](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.containing(string)` | Strängfiltreringsfunktion. Den här funktionen används som predikat för `has()` steget för att matcha en egenskap med innehållet i en viss sträng | [TextP predikater](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notStartingWith(string)` | Strängfiltreringsfunktion. Den här funktionen används som predikat för `has()` steget för att matcha en egenskap som inte börjar med en viss sträng | [TextP predikater](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notEndingWith(string)` | Strängfiltreringsfunktion. Den här funktionen används som predikat för `has()` steget för att matcha en egenskap som inte slutar med en viss sträng | [TextP predikater](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notContaining(string)` | Strängfiltreringsfunktion. Den här funktionen används som predikat för `has()` steget för att matcha en egenskap som inte innehåller en viss sträng | [TextP predikater](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `tree` | Sammanställ sökvägar från en brytpunkt i ett träd | [tree step](https://tinkerpop.apache.org/docs/3.3.2/reference/#tree-step) |
| `unfold` | Rulla upp en iterator som ett steg| [unfold step](https://tinkerpop.apache.org/docs/3.3.2/reference/#unfold-step) |
| `union` | Sammanfoga resultat från flera bläddringar| [union step](https://tinkerpop.apache.org/docs/3.3.2/reference/#union-step) |
| `V` | Inkluderar de steg som krävs för bläddringar mellan brytpunkter och kanter `V`, `E`, `out`, `in`, `both`, `outE`, `inE`, `bothE`, `outV`, `inV`, `bothV` och `otherV` för | [vertex steps](https://tinkerpop.apache.org/docs/3.3.2/reference/#vertex-steps) |
| `where` | Används för att filtrera resultat från bläddringen. Stöder operatorerna `eq`, `neq`, `lt`, `lte`, `gt`, `gte` och `between`  | [where step](https://tinkerpop.apache.org/docs/3.3.2/reference/#where-step) |

Den skrivoptimerade motorn som tillhandahålls av Azure Cosmos DB stöder automatisk indexering av alla egenskaperna i brytpunkter och kanter som standard. Därför bearbetas frågor med filter, intervallfrågor, sortering eller sammanställning av alla egenskaper från index och hanteras effektivt. Mer information om hur indexering fungerar i Azure Cosmos DV finns i vårt papper om [schemaoberoende indexering](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf).

## <a name="next-steps"></a>Nästa steg
* Kom igång med att skapa ett diagramprogram [med våra SDK:er](create-graph-dotnet.md) 
* Läs mer om [diagramstöd](graph-introduction.md) i Azure Cosmos DB
