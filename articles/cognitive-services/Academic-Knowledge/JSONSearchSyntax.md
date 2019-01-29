---
title: Söksyntax för JSON - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Läs mer om söksyntax för JSON som du kan använda i Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: b3f0c5c22775a6eb0ab7a34592d816ed174539c0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55196573"
---
# <a name="json-search-syntax"></a>Söksyntax för JSON

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

Nod-namn i en fråga sökväg (_v0, v1..._ ) fungerar som noden identifierare som kan refereras i frågeobjektet. edge-namn (_e0, e1 och..._ ) representerar typerna av motsvarande kanter i sökvägen. Vi kan använda en asterisk _*_ som en nod eller kanttabell namn (förutom från noden som måste anges) för att deklarera att det finns inga begränsningar på sådana element. Exempelvis kan en fråga sökväg `/v0/*/v1/e1/*/` hämtar sökvägar från diagrammet utan att begränsa typ av gränsen _(v0 v1)_. Under tiden kan har frågan inte begränsningar på målet (den sista noden) sökvägens antingen.

När en sökväg innehåller bara en nod, säger _v0_, helt enkelt returnerar frågan alla enheter som uppfyller begränsningarna. En begränsning-objektet som tillämpas på noden från kallas en *startar frågeobjektet*, vars har inte angetts på följande sätt.

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

När en sökväg innehåller mer än bara en från nod, utför frågeprocessorn en genomgång av diagram enligt den angivna sökvägen. När den når en nod, användardefinierade edge traversal åtgärder utlöses, det vill säga om du vill avsluta när den aktuella noden och returnera eller fortsätta att utforska i diagrammet. När du anger ingen edge traversal-åtgärd vidtas standardåtgärder. För ett mellanliggande nod är standardåtgärden att fortsätta att utforska i diagrammet. För den sista noden på en sökväg är standardåtgärden att stoppa och returnera. En begränsning-objekt som anger edge traversal åtgärder kallas en *Edge Traversal åtgärdsobjektet*. Specifikation ges på följande sätt:

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

POST brödtexten i en *json* sökfrågan måste innehålla minst en *sökväg* mönster. Bläddra åtgärd objekt är valfria. Här följer två exempel.

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

