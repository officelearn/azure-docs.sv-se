---
title: Utvärdera metod - Knowledge API för tjänst för Kunskapsutveckling
titlesuffix: Azure Cognitive Services
description: Lär dig hur du använder i den kunskap utforskning Service (KES) API-Evaluate-metoden.
services: cognitive-services
author: bojunehsu
manager: cgronlun
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: d6ddb76954108c037dc4135393df8622c9dbd101
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55220781"
---
# <a name="evaluate-method"></a>utvärdera metod

Den *utvärdera* metoden utvärderar och returnerar resultatet av ett strukturerade frågeuttryck baserat på index-data.

Vanligtvis hämtas ett uttryck som från ett svar till metoden tolkningar.  Men du kan också skapa frågeuttryck själv (se [strukturerade frågeuttryck](Expressions.md)).  

## <a name="request"></a>Begäran 

`http://<host>/evaluate?expr=<expr>&attributes=<attrs>[&<options>]`   

Name|Value|Beskrivning
----|----|----
uttryck för Markörstorlek       | Textsträngen | Strukturerade frågeuttryck som väljer en delmängd av index entiteter.
Attribut | Textsträngen | Kommaavgränsad lista med attribut som ska ingå i svaret.
 antal      | Antal (standard = 10) | Maximalt antal resultat som ska returneras.
offset     | Antal (standard = 0) | Index för det första resultatet ska returneras.
orderby |   Textsträngen | Namnet på attributet som används för att sortera resultaten, följt av valfritt sorteringsordningen (standard = asc) ”:*%{attrname/*[: (asc&#124;desc)]”.  Om inte anges returneras resultatet genom att minska sannolikheten för naturliga loggen.
timeout  | Antal (standard = 1000) | Tidsgräns i millisekunder. Endast returneras en beräknad innan tidsgränsen har gått ut.

Med hjälp av den *antal* och *offset* parametrar, ett stort antal resultat kan erhållas stegvis över flera förfrågningar.
  
## <a name="response-json"></a>Svar (JSON)
JSONPath|Beskrivning
----|----
$.expr | *uttryck* parametern från begäran.
$.entities | Matris med 0 eller fler objekt entiteter som matchar det strukturerade frågeuttrycket. 
$.aborted | SANT om uppnåddes för begäran.

Varje entitet innehåller en *logprob* värde och värdena för de begärda attributen.

## <a name="example"></a>Exempel
I exemplet akademiska publikationer följande begäran skickar ett strukturerade frågeuttryck (potentiellt från utdata från en *tolka* begäran) och hämtar några attribut för övre 2 matchande entiteter:

`http://<host>/evaluate?expr=Composite(Author.Name=='jaime teevan')&attributes=Title,Y,Author.Name,Author.Id&count=2`

Svaret innehåller viktigaste 2 (”antal = 2”) är förmodligen matchande entiteter.  Rubrik, år, författarens namn och författare ID-attribut som returneras för varje entitet.  Observera hur strukturen för sammansatta attributvärden matchar på sätt som de anges i datafilen. 

```json
{
  "expr": "Composite(Author.Name=='jaime teevan')",
  "entities": 
  [
    {
      "logprob": -6.645,
      "Ti": "personalizing search via automated analysis of interests and activities",
      "Y": 2005,
      "Author": [
        {
          "Name": "jaime teevan",
          "Id": 1968481722
        },
        {
          "Name": "susan t dumais",
          "Id": 676500258
        },
        {
          "Name": "eric horvitz",
          "Id": 1470530979
        }
      ]
    },
    {
      "logprob": -6.764,
      "Ti": "the perfect search engine is not enough a study of orienteering behavior in directed search",
      "Y": 2004,
      "Author": [
        {
          "Name": "jaime teevan",
          "Id": 1982462162
        },
        {
          "Name": "christine alvarado",
          "Id": 2163512453
        },
        {
          "Name": "mark s ackerman",
          "Id": 2055132526
        },
        {
          "Name": "david r karger",
          "Id": 2012534293
        }
      ]
    }
  ]
}
```
