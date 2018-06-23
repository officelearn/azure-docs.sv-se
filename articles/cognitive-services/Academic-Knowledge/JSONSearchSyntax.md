---
title: JSON-söksyntax i Academic Knowledge API | Microsoft Docs
description: 'Läs mer om JSON-söksyntax som du kan använda i Academic Knowledge API: et i kognitiva Microsoft-tjänster.'
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: a4b9cf535dae60258d71c43bba6f9eec1444bd41
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351366"
---
# <a name="json-search-syntax"></a>JSON-Söksyntax

```javascript
/* Query Object:
   Suppose we have a query path /v0/e0/v1/e1/...
   A query object contains constraints to be applied to graph nodes in a path.
   Constraints are given in <"node identifier", {constraint object}> key-value pairs: 
*/
{
    /* query path can also be set in the query object */
    path: "/v0/e0/v1/e1/.../vn/",
    v0: { /* A Constraint Object */ },
    v1: { /* A Constraint Object */ },
}
```

Nod-namn i en fråga sökväg (_v0 v1..._ ) fungerar som noden identifierare som kan refereras i frågeobjektet. edge-namn (_e0 e1..._ ) representerar typerna av motsvarande kanter i sökvägen. Vi kan använda en asterisk _*_ som en nod eller kant namn (utom den första noden, som måste anges) för att deklarera som det finns inga begränsningar på sådana element. Till exempel en sökväg frågan `/v0/*/v1/e1/*/` hämtar sökvägar från diagrammet utan att begränsa typ av kanten _(v0 v1)_. Under tiden kan har frågan inte begränsningar för målet (den sista noden) sökvägen antingen.

När en sökväg innehåller bara en nod, säg _v0_, bara returneras alla enheter som uppfyller villkoren. En begränsning-objektet som tillämpas på den första noden kallas en *startar frågeobjekt*, vars har inte angetts på följande sätt.

```javascript
/* Starting Query Object:
   Specifies constraints on the starting node
*/
{
    /* "match" operator searches for entities with the specified properties. 
       All properties defined in the graph schema be queried such as "Name" and "NormalizedTitle".
     */
    "match": { 
        "Name" : "some name",
        ...
    },
    /* "id" operator directly specifies the IDs of the starting nodes. 
       When it is present, the "match" operator is ignored. 
     */
    "id": [ id1, id2, id3... ],
    /* "type" operator limits results to a certain type of entities,
       for example, "Author" or "Paper".
     */
    "type": "Author",
    /* "select" operator pulls properties from the database and 
       returns them to the client.
     */
    "select": ["PaperRank", ...]
}
```

När en sökväg innehåller mer än bara startnoden, utför frågeprocessorn ett diagram traversal enligt den angivna sökvägen. När den når en nod, användardefinierade traversal åtgärder ska utlösas, det vill säga om du vill stoppa på den aktuella noden och returnera eller fortsätta att utforska diagrammet. Om du har angett något traversal ska standardåtgärder vidtas. En mellanliggande nod är standardåtgärden att fortsätta att utforska diagrammet. För den sista noden på en sökväg är standardåtgärden att stoppa och returnera. En begränsning-objekt som anger traversal åtgärder kallas en *Traversal åtgärdsobjektet*. Specifikation ges på följande sätt:

```javascript
/* Traversal Action Object:
   Specifies graph traversal actions on a node (except for the starting node).
 */
{
    /* Set the continue condition here. */
    continue: { 
        /* simple property exact match */
        "property_key" : "property_value", 
        /* Advanced query operators */
        /* -- Numerical comparisons */
        "property_key_2" : { "gt" /* or simply ">" */ : 123 },
        /* -- Substring query */
        "property_key_3" : { "substring" : "456" },
        /* -- CellID query */
        "id": [ 111, 222, 333... ],
        /* -- Entity type query */
        "type": "cell_type",
        /* -- Property existance query */
        "has" : "property_key_4",
        /* -- Logical operators */
        /* ---- Note that, by default the operators are combined with AND semantics */
        
        /* -- OR operator */
        "or": {
          /* Query operators... */
        },
        /* -- NOT operator */
        "not": {
            /* Query operators... */
        }
    },
    /* Set the return condition here. */
    return: {
        /* Same operators as the continue object */
    },
    /* The selected properties to return. */
    select: ["property_key_1", ...]
}
```

EFTER innehållet i en *json* sökfråga ska innehålla minst en *sökväg* mönster. Bläddra åtgärd objekt är valfria. Här är två exempel.

```JSON
{
  "path": "/series/ConferenceInstanceIDs/conference/FieldOfStudyIDs/field",
  "series": {
    "type": "ConferenceSeries",
    "FullName": "graph",
    "select": [ "FullName", "ShortName" ]
  },
  "conference": {
    "type": "ConferenceInstance",
    "select": [ "FullName" ]
  },
  "field": {
    "type": "FieldOfStudy",
    "select": [ "Name" ],
    "return": { "Name": { "substring" : "World Wide Web" } }
  }
}
```

```JSON
{
  "path": "/author/PaperIDs/paper",
  "author": {
    "type": "Author",
    "select": [ "DisplayAuthorName" ],
    "match": { "Name": "leslie lamport" }
  },
  "paper": {
    "type": "Paper",
    "select": [ "OriginalTitle", "CitationCount" ],
    "return": { "CitationCount": { "gt": 100 } }
  }
}
```

