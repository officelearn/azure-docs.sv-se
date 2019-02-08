---
title: Interpret method - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Använd metoden tolkningar för att returnera formaterade tolkningar av frågesträngar för användaren baserat på Academic Graph-data och akademiska grammatik i Microsoft Cognitive Services.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: 6db9a5b65fc2723af2eae006ad81716e23e52133
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55860532"
---
# <a name="interpret-method"></a>tolka metod

Den **tolka** REST API tar slutanvändaren frågesträng (t.ex, en fråga som angetts av en användare av ditt program) och returnerar formaterad tolkningar av användaravsikt baserat på Academic Graph-data och akademiska grammatik.

Om du vill skapa en interaktiv upplevelse, kan du anropa den här metoden upprepade gånger efter varje tecken som anges av användaren. I så fall bör du ange den **fullständig** parameter 1 för att aktivera automatisk komplettering förslag. Om ditt program inte behöver automatisk komplettering, bör du ange den **fullständig** parametern till 0.

**REST-slutpunkt:**

    https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?

## <a name="request-parameters"></a>Parametrar för begäran

Namn     | Värde | Krävs?  | Beskrivning
---------|---------|---------|---------
**Fråga**    | Textsträngen | Ja | Frågan som angetts av användaren.  Om fullständig har angetts till 1, tolkas fråga som ett prefix för generering frågeförslag för automatisk komplettering.        
**model**    | Textsträngen | Nej  | Namnet på den modell som du vill fråga.  För närvarande standardvärdet *senaste*.        
**Slutför** | 0 eller 1 | Nej<br>standard: 0  | 1 innebär att automatisk komplettering förslag genereras baserat på de grammatik och graph.         
**antal**    | Tal | Nej<br>standard: 10 | Maximalt antal tolkningar för att returnera.         
**offset**   | Tal | Nej<br>standard: 0  | Index för den första tolkningen ska returneras. Till exempel *count = 2 & örskjutning = 0* returnerar tolkningar 0 och 1. *Antal = 2 & örskjutning = 2* returnerar tolkningar 2 och 3.       
**timeout**  | Tal | Nej<br>standard: 1000 | Tidsgräns i millisekunder. Endast tolkningar hittades före tidsgränsen har gått ut returneras.
<br>
  
## <a name="response-json"></a>Svar (JSON)
Namn     | Beskrivning
---------|---------
**Fråga** |Den *fråga* parametern från begäran.
**tolkningar** |En matris med 0 eller fler olika sätt med matchande indata från användaren mot grammatik.
**tolkningar [x] .logprob**  |Den relativa naturliga logg sannolikheten för tolkning. Större värden är mer troligt.
**tolkningar [x] .parse**  |En XML-sträng som visar hur varje del av frågan har tolkas.
**interpretations[x].rules**  |En matris med 1 eller flera regler som definierats i grammatik och som har utförts under tolkning. För Academic Knowledge API, kommer det alltid finnas 1 regel.
**tolkningar [.rules [y] .name x]**  |namnet på regeln.
**interpretations[x].rules[y].output**  |Utdata från regeln.
**tolkningar [.rules [y].output.type x]** |Datatypen för utdatan från regeln.  Academic Knowledge API är det här alltid ”query”.
**interpretations[x].rules[y].output.value**  |Utdata från regeln. Det här är en frågesträng för uttryck som kan skickas till metoderna utvärdera och calchistogram för Academic Knowledge API.
**avbröts** | SANT om uppnåddes för begäran.

<br>
#### <a name="example"></a>Exempel:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime&complete=1&count=2
 ```
<br>Svaret nedan innehåller de två (på grund av parametern *count = 2*) mest sannolika tolkningar som Slutför partiella användarindata *handlingar jaime*: *handlingar jaime teevan*  och *papers jaime grönt*.  Frågeifyllning service som genereras i stället för funderar på att endast exakta matchningar för författaren *jaime* eftersom begäran angetts *fullständig = 1*. Observera att värdet för canonical *j l grön* matchas via synonymen *Johan grönt*, som anges i parsa.


```JSON
{
  "query": "papers by jaime",
  "interpretations": [
    {
      "logprob": -12.728,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#AA.AuN\">jaime teevan</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(AA.AuN=='jaime teevan')"
          }
        }
      ]
    },
    {
      "logprob": -12.774,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#AA.AuN\" canonical=\"j l green\">jaime green</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(AA.AuN=='j l green')"
          }
        }
      ]
    }
  ]
}
```  
<br>Använd för att hämta entiteten resultat för en tolkning *output.value* från den **tolka** API, och skicka det till den **utvärdera** API via den *uttryck*  parametern. I det här exemplet är frågan för första tolkning: 
```
evaluate?expr=Composite(AA.AuN=='jaime teevan')
```
 
