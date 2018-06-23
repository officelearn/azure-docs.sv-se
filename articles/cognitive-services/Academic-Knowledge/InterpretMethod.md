---
title: Tolka metod i Academic Knowledge API | Microsoft Docs
description: Använd metoden Interpret om du vill returnera formaterad tolkningar av användaren frågesträngar baserat på Academic diagramdata och Academic grammatiken i kognitiva Microsoft-tjänster.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: a46c792f14fabf6562666d1067ef880bd505741f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351450"
---
# <a name="interpret-method"></a>Tolka metod

Den **tolka** REST API tar en slutanvändare frågesträng (d.v.s. en fråga som angetts av en användare av ditt program) och returnerar formaterad tolkningar av användaravsikt baserat på Academic diagramdata och Academic grammatik.

För att ge en interaktiv upplevelse, kan du anropa metoden upprepade gånger efter varje tecken som anges av användaren. I så fall bör du ange den **fullständig** parameter 1 för att aktivera automatisk komplettering förslag. Om ditt program inte behöver automatisk komplettering, bör du ange den **fullständig** parametern till 0.

**REST-slutpunkt:**

    https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?

## <a name="request-parameters"></a>Begäranparametrar

Namn     | Värde | Krävs?  | Beskrivning
---------|---------|---------|---------
**frågan**    | Textsträng | Ja | Frågan som angetts av användaren.  Om fullständig har angetts till 1, tolkas fråga som ett prefix för att fråga automatisk komplettering förslag.        
**modellen**    | Textsträng | Nej  | Namnet på den modell som du vill fråga.  För närvarande standardvärdet *senaste*.        
**Slutför** | 0 eller 1 | Nej<br>standard: 0  | 1 innebär att automatisk komplettering förslag genereras baserat på data i grammatik och diagram.         
**Antal**    | Tal | Nej<br>standard: 10 | Maximalt antal tolkningar att returnera.         
**förskjutning**   | Tal | Nej<br>standard: 0  | Indexet för den första tolkningen att returnera. Till exempel *count = 2 & förskjutning = 0* returnerar tolkningar 0 och 1. *Antal = 2 & förskjutning = 2* returnerar tolkningar 2 och 3.       
**timeout**  | Tal | Nej<br>standard: 1000 | Tidsgräns i millisekunder. Endast tolkningar hittades före tidsgränsen har gått ut returneras.
<br>
  
## <a name="response-json"></a>Svar (JSON)
Namn     | Beskrivning
---------|---------
**frågan** |Den *frågan* parametern från begäran.
**tolkningar** |En matris med 0 eller fler olika sätt att matcha användarindata mot grammatik.
**tolkningar [x] .logprob**  |Den relativa fysiska logg sannolikheten för tolkning. Större värden är mer troligt.
**tolkningar [x] .parse**  |En XML-sträng som visar hur varje del av frågan har tolkas.
**tolkningar [x] .rules**  |En matris med 1 eller flera regler som definierats i grammatiken och som har utförts under tolkning. För Academic Knowledge API, kommer det alltid vara 1 regeln.
**tolkningar [.name .rules [-y] x]**  |Namnet på regeln.
**tolkningar [.output .rules [-y] x]**  |Utdata från regeln.
**tolkningar [.output.type .rules [-y] x]** |Datatypen för utdata från regeln.  Academic Knowledge API: et är det här alltid ”frågan”.
**tolkningar [.output.value .rules [-y] x]**  |Utdata från regeln. Detta är en frågesträng för uttryck som kan skickas till metoderna utvärdera och calchistogram för i Academic Knowledge API: et.
**avbröts** | TRUE om tidsgränsen för begäran.

<br>
#### <a name="example"></a>Exempel:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime&complete=1&count=2
 ```
<br>Svaret nedan innehåller två översta (på grund av parametern *count = 2*) troligen tolkningar som Slutför den partiella användarindata *handlingar jaime*: *handlingar jaime teevan*  och *papper jaime grönt*.  Tjänsten genereras frågan slutföranden i stället för att ta hänsyn till endast exakt matchar för författaren *jaime* eftersom begäran har angetts *fullständig = 1*. Observera att kanoniskt värde *j l grön* matchas via synonymen *Johan grönt*, som anges i parsa.


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
<br>Använd för att hämta entiteten resultat för en tolkning *output.value* från den **tolka** API, och skickar som i den **utvärdera** API via den *uttryck*  parameter. I det här exemplet är frågan för första tolkning: 
```
evaluate?expr=Composite(AA.AuN=='jaime teevan')
```
 