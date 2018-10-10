---
title: Utvärdera metoden – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Använd metoden utvärdera om du vill returnera en uppsättning akademiska enheter baserat på ett frågeuttryck.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: 262beeefbbafefc95da51e9f4afcbc1bc143f952
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902339"
---
# <a name="evaluate-method"></a>utvärdera metod

Den **utvärdera** REST-API används för att returnera en uppsättning akademiska enheter baserat på ett frågeuttryck.
<br>

**REST-slutpunkt:**  
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate? 
```   
<br>
## <a name="request-parameters"></a>Parametrar för begäran  
Namn     | Värde | Krävs?  | Beskrivning
-----------|-----------|---------|--------
**uttryck för Markörstorlek**       | Textsträngen | Ja | Ett frågeuttryck som anger vilka enheter som ska returneras.
**Modellen**      | Textsträngen | Nej  | Namnet på den modell som du vill fråga.  För närvarande standardvärdet *senaste*.        
**Attribut** | Textsträngen | Nej<br>standard: Id | En kommaavgränsad lista som anger de attributvärden som ingår i svaret. Attributnamn är skiftlägeskänsliga.
**Antal**        | Tal | Nej<br>Standard: 10 | Antalet resultat som ska returneras.
**förskjutning**     | Tal |   Nej<br>Standard: 0    | Index för det första resultatet ska returneras.
**OrderBy** |   Textsträngen | Nej<br>Standard: genom att minska sannolikhet | Namnet på ett attribut som används för att sortera entiteterna. Du kan också kan stigande/fallande anges. Formatet är: *namn: asc* eller *namn: desc*.
  
 <br>
## <a name="response-json"></a>Svar (JSON)
Namn | Beskrivning
-------|-----   
**uttryck för Markörstorlek** |  Den *uttryck* parametern från begäran.
**Entiteter** |  En matris med 0 eller fler entiteter som matchade frågeuttrycket. Varje entitet innehåller ett naturliga loggen sannolikhetsvärde och värdena för andra attribut som begärs.
**avbröts** | SANT om uppnåddes för begäran.

<br>
#### <a name="example"></a>Exempel:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate?expr=
Composite(AA.AuN=='jaime teevan')&count=2&attributes=Ti,Y,CC,AA.AuN,AA.AuId
```
<br>Vanligtvis ett uttryck som ska hämtas från ett svar till den **tolka** metod.  Men du kan också skapa frågeuttryck själv (se [fråga uttryckssyntax](QueryExpressionSyntax.md)).  
  
Med hjälp av den *antal* och *offset* parametrar, ett stort antal resultat kan erhållas utan att skicka en begäran att resultat i en enorm (och potentiellt långsamt)-svar.  I det här exemplet används begäran uttrycket för den första tolkningen från den **tolka** API-svar som de *uttryck* värde. Den *count = 2* parametern anger att 2 enhetsresultat tas emot. Och *attribut = Ti, Y, CC, AA. AuN AA. AuId* parametern anger att rubriken, år, antalet källhänvisningar, författarens namn och ID: T för författaren begärs för varje resultat.  Se [entitetsattribut](EntityAttributes.md) en lista över attribut.
  
```JSON
{
  "expr": "Composite(AA.AuN=='jaime teevan')",
  "entities": 
  [
    {
      "logprob": -15.08,
      "Ti": "personalizing search via automated analysis of interests and activities",
      "Y": 2005,
      "CC": 372,
      "AA": [
        {
          "AuN": "jaime teevan",
          "AuId": 1968481722
        },
        {
          "AuN": "susan t dumais",
          "AuId": 676500258
        },
        {
          "AuN": "eric horvitz",
          "AuId": 1470530979
        }
      ]
    },
    {
      "logprob": -15.389,
      "Ti": "the perfect search engine is not enough a study of orienteering behavior in directed search",
      "Y": 2004,
      "CC": 237,
      "AA": [
        {
          "AuN": "jaime teevan",
          "AuId": 1982462162
        },
        {
          "AuN": "christine alvarado",
          "AuId": 2163512453
        },
        {
          "AuN": "mark s ackerman",
          "AuId": 2055132526
        },
        {
          "AuN": "david r karger",
          "AuId": 2012534293
        }
      ]
    }
  ]
}
 ```
 
