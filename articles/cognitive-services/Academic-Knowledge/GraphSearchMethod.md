---
title: Diagram över Search-metoden i Academic Knowledge API | Microsoft Docs
description: Använd diagram Search-metoden i Academic Knowledge API för att returnera en uppsättning academic enheter baserat på specifika diagram mönster i kognitiva Microsoft-tjänster.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: d811db117c934c0d41fbfea1220d241cc022e4a8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351390"
---
# <a name="graph-search-method"></a>Diagrammet Search-metoden

Den **diagram Sök** REST-API används för att returnera en uppsättning av academic enheter baserat på diagrammet mönster.  Svaret är en uppsättning diagrammet sökvägar som uppfyller begränsningarna som angetts av användaren. Ett diagram sökvägen är en överlagrad sekvens med diagramnoder och kanter i form av _v0, e0, v1,..., e1 vn_, där _v0_ är den första noden i sökvägen.
<br>

**REST-slutpunkt:**  
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?
```   
<br>

## <a name="request-parameters"></a>Begäranparametrar  
Namn     | Värde | Krävs?  | Beskrivning
-----------|-----------|---------|--------
**läge**       | Textsträng | Ja | Namnet på det läge som du vill använda. Värdet är antingen *json* eller *lambda*.

Diagrammet search-metoden måste anropas via en HTTP POST-begäran. Post-begäran ska innehålla content-type-huvud: **application/json**.

##### <a name="json-search"></a>JSON-sökning 

För den *json* Sök efter brödtext är en JSON-objekt. JSON-objekt som beskriver en sökvägar med användardefinierade begränsningar (finns i [specificering av JSON-objekt](JSONSearchSyntax.md) för *json* Sök).


##### <a name="lambda-search"></a>Lambda-sökning

För den *lambda* Sök efter brödtext är en sträng som oformaterad text. POST-meddelandetexten är en LIKQ lambda frågesträng, vilket är en enskild C#-instruktion (finns i [specificering av frågesträngen](LambdaSearchSyntax.md) för *lambda* Sök). 

<br>
## <a name="response-json"></a>Svar (JSON)
Namn | Beskrivning
-------|-----   
**resultat** | En matris med 0 eller fler entiteter som matchar frågeuttrycket. Varje entitet innehåller värdena i begärda attribut. Det här fältet är tillgänglig om begäran har bearbetats.
**Fel** | Statuskoder för HTTP. Det här fältet är tillgänglig om begäran misslyckas.
**meddelande** | Felmeddelande. Det här fältet är tillgänglig om begäran misslyckas.

Om en fråga inte kan bearbetas i _800 ms_, _timeout_ fel returneras. 

<br>
#### <a name="example"></a>Exempel:

##### <a name="json-search"></a>JSON-sökning
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?mode=json
```
<br>
För den *json* söka, om vi vill hämta rapporter med titlar som innehåller ”kurva motorn” och skrivits av ”bin shao” vi kan ange frågan på följande sätt.

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

Utdata från en fråga är en matris med diagrammet sökvägar. En graph-sökväg är en matris med nod-objekt som motsvarar de noder som anges i sökvägen till fråga. Dessa noder har minst en egenskap *CellID*, som representerar entitets-ID. Andra egenskaper som kan hämtas genom att ange egenskapsnamn via operatorn väljer av en [ *Traversal åtgärdsobjektet*](JSONSearchSyntax.md).

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
För den *lambda* sökning, om vi vill hämta författaren ID på en viss papper vi kan skriva en fråga som det nedanstående.

```
MAG.StartFrom(@"{
    type  : ""Paper"",
    match : {
        NormalizedTitle : ""trinity: a distributed graph engine on a memory cloud""
    }
}").FollowEdge("AuthorIDs").VisitNode(Action.Return)
```

Utdata från en *lambda* sökfråga också är en matris med diagrammet sökvägar:

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
 
## <a name="graph-schema"></a>Schemat för diagram

Diagrammet schemat är användbart för att skriva diagrammet sökfrågor. Den visas i följande bild.

![Academic Microsoft Graph Schema](./Images/AcademicGraphSchema.png)
