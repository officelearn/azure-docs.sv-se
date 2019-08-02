---
title: Tolka metod-Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Använd tolka-metoden för att returnera formaterade tolkningar av användar frågesträngar baserat på akademiska diagram data och den akademiska grammatiken i Microsoft Cognitive Services.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: d960aff109e0eca70cb87463770620093e563f63
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706679"
---
# <a name="interpret-method"></a>Tolka metod

**Tolka** REST API tar en frågesträng för slutanvändare (dvs. en fråga som angetts av en användare av ditt program) och returnerar formaterade tolkningar av användar avsikten baserat på akademiska diagram data och den akademiska grammatiken.

Om du vill tillhandahålla en interaktiv upplevelse kan du anropa den här metoden flera gånger efter varje tangent som anges av användaren. I så fall bör du ange den **fullständiga** parametern till 1 för att aktivera förslag för automatisk komplettering. Om programmet inte behöver komplettera automatiskt ska du ange den **fullständiga** parametern till 0.

**REST-slutpunkt:**

    https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?

## <a name="request-parameters"></a>Begäranparametrar

Namn     | Value | Obligatorisk?  | Beskrivning
---------|---------|---------|---------
**query**    | Text sträng | Ja | Fråga som anges av användaren.  Om Complete är inställt på 1, tolkas frågan som ett prefix för att generera förslag för automatisk komplettering av frågor.        
**förlag**    | Text sträng | Nej  | Namnet på den modell som du vill fråga.  För närvarande är standardvärdet *senaste*.        
**full** | 0 eller 1 | Nej<br>standard: 0  | 1 innebär att förslag för automatisk komplettering genereras baserat på grammatik-och diagram data.         
**antal**    | Number | Nej<br>standard: 10 | Högsta antal tolkningar som ska returneras.         
**redovisningsmotkonto**   | Number | Nej<br>standard: 0  | Index för den första tolkningen som ska returneras. Exempelvis *Count = 2 & offset = 0* returnerar tolkningar 0 och 1. *Count = 2 & offset = 2* returnerar tolkningar 2 och 3.       
**standardvärde**  | Number | Nej<br>standard: 1 000 | Timeout i millisekunder. Endast tolkningar som hittas innan tids gränsen har förflutit returneras.

<br>
  
## <a name="response-json"></a>Svar (JSON)

Namn     | Beskrivning
---------|---------
**query** |*Frågeparametern* från begäran.
**tolkningar** |En matris med 0 eller flera olika sätt att matcha indata från användaren mot grammatiken.
**tolkningar [x]. logprob**  |Den relativa naturliga logg sannolikheten för tolkningen. Större värden är mer sannolika.
**tolkningar [x]. parsa**  |En XML-sträng som visar hur varje del av frågan tolkades.
**tolkningar [x]. regler**  |En matris med 1 eller flera regler som har definierats i grammatiken som anropades under tolkningen. För Academic Knowledge API finns det alltid 1 regel.
**tolkningar [x]. regler [y]. namn**  |Regelns namn.
**tolkningar [x]. regler [y]. utdata**  |Utmatning av regeln.
**tolkningar [x]. regler [y]. output. Type** |Data typen för regelns utdata.  Detta är alltid "fråga" för Academic Knowledge API.
**tolkningar [x]. regler [y]. output. Value**  |Utdatan för regeln. För Academic Knowledge API är detta en frågeuttryck sträng som kan skickas till metoderna evaluate och calchistogram.
**avbröts** | Sant om tids gränsen för begäran uppnåddes.

<br>

#### <a name="example"></a>Exempel:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime&complete=1&count=2
 ```
<br>Svaret nedan innehåller de två översta (på grund av parameter *Count = 2*) som är mest sannolika och som fyller i de delvis inmatade *dokumenten av kolla*: dokumenten av *kolla teevan* och *dokument av kolla grönt*.  Tjänsten har genererat frågor i stället för att bara beakta exakta matchningar för författaren *kolla* eftersom begäran som angetts *är Complete = 1*. Observera att det kanoniska värdet *j l grönt* matchas med synonymen *Janne grönt*, enligt vad som anges i avsnittet parsa.


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
<br>Om du vill hämta enhets resultat för en tolkning använder du *output. Value* från **tolknings** -API: et och överför det till **evaluate** -API: et via parametern *expr* . I det här exemplet är frågan för den första tolkningen: 
```
evaluate?expr=Composite(AA.AuN=='jaime teevan')
```
 
