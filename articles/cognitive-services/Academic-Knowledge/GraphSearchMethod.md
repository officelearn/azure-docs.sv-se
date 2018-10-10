---
title: Graph sökmetoden – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Använd metoden Grafsökning i Academic Knowledge API för att returnera en uppsättning akademiska enheter baserat på specifika grafmönster.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 83f29106d72f564f894c968102b703ab6bb5d8c2
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902391"
---
# <a name="graph-search-method"></a>Sökmetod

Den **grafsökning** REST-API används för att returnera en uppsättning akademiska enheter baserat på de angivna graph-mönster.  Svaret är en uppsättning graph-sökvägar som uppfyller de användardefinierade begränsningarna. En sökväg för graph är en överlagrad sekvens av graph-noder och kanter i form av _v0, e0, v1, e1, …, vn_, där _v0_ är den första noden i sökvägen.
<br>

**REST-slutpunkt:**  
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?
```   
<br>

## <a name="request-parameters"></a>Parametrar för begäran  
Namn     | Värde | Krävs?  | Beskrivning
-----------|-----------|---------|--------
**läge**       | Textsträngen | Ja | Namnet på det läge som du vill använda. Värdet är antingen *json* eller *lambda*.

Sökmetoden måste anropas via en HTTP POST-begäran. Post-begäran bör innehålla rubriken innehållstyp: **application/json**.

##### <a name="json-search"></a>JSON-sökning 

För den *json* search, brödtexten INLÄGGET är ett JSON-objekt. JSON-objektet beskriver ett sökvägsmönster med användardefinierade begränsningar (se den [specifikation av JSON-objekt](JSONSearchSyntax.md) för *json* Sök).


##### <a name="lambda-search"></a>Lambda-sökning

För den *lambda* search, POST brödtexten är en vanlig textsträng. Brödtexten för POST är en LIKQ lambda frågesträng, vilket är en enda C#-instruktion (se den [specifikation av frågesträngen](LambdaSearchSyntax.md) för *lambda* Sök). 

<br>
## <a name="response-json"></a>Svar (JSON)
Namn | Beskrivning
-------|-----   
**Resultat** | En matris med 0 eller fler entiteter som matchar frågeuttrycket. Varje entitet innehåller värdena för attribut som begärs. Det här fältet är tillgänglig om begäran har behandlats.
**Fel** | HTTP-statuskoder. Det här fältet är tillgänglig om begäran misslyckas.
**meddelande** | Felmeddelande. Det här fältet är tillgänglig om begäran misslyckas.

Om en fråga inte kan bearbetas inom _800 ms_, ett _timeout_ fel returneras. 

<br>
#### <a name="example"></a>Exempel:

##### <a name="json-search"></a>JSON-sökning
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?mode=json
```
<br>
För den *json* söka, om vi vill hämta rapporter med titlar som innehåller ”graph motorn” och skrivits av ”bin shao”, vi kan ange frågan på följande sätt.

```JSON
{
  "path": "/paper/AuthorIDs/author",
  "paper": {
    "type": "Paper",
    "NormalizedTitle": "graph engine",
    "select": [
      "OriginalTitle"
    ]
  },
  "author": {
    "return": {
      "type": "Author",
      "Name": "bin shao"
    }
  }
}
```

Utdata från en fråga är en matris med sökvägar för diagrammet. En sökväg för graph är en matris med nod-objekt som motsvarar de noder som anges i frågan sökväg. Dessa noder har minst en egenskap *CellID*, som representerar entitets-ID. Andra egenskaper som kan hämtas genom att ange egenskapsnamn via operatorn väljer av en [ *Edge Traversal åtgärdsobjektet*](JSONSearchSyntax.md).

```JSON
{
  "Results": [
    [
      {
        "CellID": 2160459668,
        "OriginalTitle": "Trinity: a distributed graph engine on a memory cloud"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 2171539317,
        "OriginalTitle": "A distributed graph engine for web scale RDF data"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 2411554868,
        "OriginalTitle": "A distributed graph engine for web scale RDF data"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 73304046,
        "OriginalTitle": "The Trinity graph engine"
      },
      {
        "CellID": 2093502026
      }
    ]
  ]
}
 ```

##### <a name="lambda-search"></a>Lambda-sökning 

```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?mode=lambda
```
<br>
För den *lambda* sökning, om vi vill hämta författaren ID på en viss dokumentet, vi kan skriva en fråga som det nedanstående.

```
MAG.StartFrom(@"{
    type  : ""Paper"",
    match : {
        NormalizedTitle : ""trinity: a distributed graph engine on a memory cloud""
    }
}").FollowEdge("AuthorIDs").VisitNode(Action.Return)
```

Utdata från en *lambda* sökfråga är också en matris med graph sökvägar:

```JSON
{
  "Results": [
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2142490828
      }
    ],
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2116756368
      }
    ],
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2093502026
      }
    ]
  ]
}
```
 
## <a name="graph-schema"></a>Diagramscheman

Diagramscheman är användbart för att skriva graph sökfrågor. Den visas i följande bild.

![Microsoft Academic Graph Schema](./Images/AcademicGraphSchema.png)
