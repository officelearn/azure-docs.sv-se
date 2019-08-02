---
title: Utvärdera metod-Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Använd metoden utvärdera för att returnera en uppsättning akademiska entiteter baserat på ett frågeuttryck.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 69e701d6727e5410b71e6cf8fbe20a1cd038ddb0
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705010"
---
# <a name="evaluate-method"></a>Utvärdera metod

**Utvärdera** REST API används för att returnera en uppsättning akademiska entiteter baserat på ett frågeuttryck.
<br>

**REST-slutpunkt:**  
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate? 
```   
<br>

## <a name="request-parameters"></a>Begäranparametrar  

Namn     | Value | Obligatorisk?  | Beskrivning
-----------|-----------|---------|--------
**uttrycks**       | Text sträng | Ja | Ett frågeuttryck som anger vilka entiteter som ska returneras.
**förlag**      | Text sträng | Nej  | Namnet på den modell som du vill fråga.  För närvarande är standardvärdet *senaste*.        
**dokumentattribut** | Text sträng | Nej<br>objekt ID | En kommaavgränsad lista som anger de attributvärden som ingår i svaret. Attributnamn är Skift läges känsliga.
**antal**        | Number | Nej<br>Standard: 10 | Antal resultat som ska returneras.
**redovisningsmotkonto**     | Number |   Nej<br>Standard: 0    | Index för det första resultat som ska returneras.
**OrderBy** |   Text sträng | Nej<br>Standard: genom att minska sannolikheten | Namn på ett attribut som används för att sortera entiteterna. Du kan också ange stigande/fallande ordning. Formatet är: *Namn: ASC* eller *Namn: DESC*.
  
 <br>

## <a name="response-json"></a>Svar (JSON)

Namn | Beskrivning
-------|-----   
**uttrycks** |  *Expr* -parametern från begäran.
**poster** |  En matris med 0 eller fler entiteter som matchade frågeuttrycket. Varje entitet innehåller ett sannolikhetsvärde för naturlig logg och värdena för andra begärda attribut.
**avbröts** | Sant om tids gränsen för begäran uppnåddes.

<br>

#### <a name="example"></a>Exempel:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/evaluate?expr=
Composite(AA.AuN=='jaime teevan')&count=2&attributes=Ti,Y,CC,AA.AuN,AA.AuId
```
<br>Normalt kommer ett uttryck att hämtas från ett svar till tolknings metoden.  Men du kan också skapa frågeuttryck själv (se [syntax för frågeuttryck](QueryExpressionSyntax.md)).  
  
Med hjälp av *Count* -och *offset* -parametrarna kan ett stort antal resultat erhållas utan att skicka en enskild begäran som resulterar i ett enorma (och eventuellt långsamt) svar.  I det här exemplet använde begäran uttrycket för den första tolkningen från **tolka** API-svaret som *uttr* -värde. Parametern *Count = 2* anger att 2 enhets resultat begärs. Och *attributen = ti, Y, CC, AA. AuN, AA. Parametern AuId* anger att rubrik, år, antal hänvisningar, författar namn och författar-ID begärs för varje resultat.  Se [entitets-attribut](EntityAttributes.md) för en lista med attribut.
  
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
 
