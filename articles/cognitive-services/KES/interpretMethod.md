---
title: Tolka metod - Knowledge API för tjänst för Kunskapsutveckling
titlesuffix: Azure Cognitive Services
description: Lär dig hur du använder-metoden tolkningar i den kunskap utforskning Service (KES) API.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 88776e2f4167c950d60c0405dcf950b5173fb989
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60814145"
---
# <a name="interpret-method"></a>tolka metod

Den *tolka* metoden tar en frågesträng för naturligt språk och returnerar formaterad tolkningar av användaravsikt baserat på de grammatik- eller indexhantering.  För att ge en interaktiv sökning, den här metoden kan anropas när varje tecken anges av användaren med den *fullständig* parameteruppsättning till 1 för att aktivera automatisk komplettering förslag.

## <a name="request"></a>Förfrågan

`http://<host>/interpret?query=<query>[&<options>]`

Namn|Värde| Beskrivning
----|----|----
DocumentDB    | Textsträngen | Frågan som angetts av användaren.  Om fullständig har angetts till 1, tolkas fråga som ett prefix för generering frågeförslag för automatisk komplettering.        
Slutför | 0 (standardvärde) eller 1 | 1 innebär att automatisk komplettering förslag genereras baserat på de grammatik- eller indexhantering.         
count    | Antal (standard = 10) | Maximalt antal tolkningar för att returnera.         
offset   | Antal (standard = 0) | Index för den första tolkningen ska returneras.  Till exempel *count = 2 & örskjutning = 0* returnerar tolkningar 0 och 1. *Antal = 2 & örskjutning = 2* returnerar tolkningar 2 och 3.       
timeout  | Antal (standard = 1000) | Tidsgräns i millisekunder. Endast tolkningar hittades före tidsgränsen har gått ut returneras.

Med hjälp av den *antal* och *offset* parametrar, ett stort antal resultat kan erhållas stegvis över flera förfrågningar.

## <a name="response-json"></a>Svar (JSON)

JSONPath     | Beskrivning
---------|---------
$.query |*fråga* parametern från begäran.
$.interpretations   |Matris med 0 eller fler sätt att matcha indatafrågan mot grammatik.
$.interpretations[\*].logprob   |Relativa log sannolikheten för tolkningen (< = 0).  Högre värden är mer sannolikt.
$.interpretations[\*].parse |XML-sträng som visar hur varje del av frågan har tolkas.
$.interpretations[\*].rules |Matris med-1 eller flera regler som definierats i grammatik anropas under tolkning.
$.interpretations[\*].rules[\*].name    |namnet på regeln.
$.interpretations[\*].rules[\*].output  |Semantisk utdata från regeln.
$.interpretations[\*].rules[\*].output.type |Datatypen för semantisk utdata.
$.interpretations[\*].rules[\*].output.value|Värdet för semantisk utdata.  
$.aborted | SANT om uppnåddes för begäran.

### <a name="parse-xml"></a>Tolka XML

Parsa XML annoterar (slutförd) frågan med information om hur den matchar mot reglerna i grammatik och attribut i indexet.  Nedan visas ett exempel från akademiska publikationer domänen:

```xml
<rule name="#GetPapers">
  papers by 
  <attr name="academic#Author.Name" canonical="heungyeung shum">harry shum</attr>
  <rule name="#GetPaperYear">
    written in
    <attr name="academic#Year">2000</attr>
  </rule>
</rule>
```

Den `<rule>` elementet avgränsar intervallet i den fråga som matchar regeln som anges av dess `name` attribut.  Det kan kapslas när parsa omfattar hänvisningar till i grammatik.

Den `<attr>` elementet avgränsar intervallet i den fråga som matchar attributet index som anges av dess `name` attribut.  När matchningen omfattar en synonym i indatafrågan, den `canonical` attributet kommer att innehålla den kanoniska värde som matchar synonymen från indexet.

## <a name="example"></a>Exempel

I exemplet akademiska publikationer returnerar följande begäran upp till 2 automatisk komplettering förslag för prefix frågan ”handlingar jaime”:

`http://<host>/interpret?query=papers by jaime&complete=1&count=2`

Svaret innehåller de två (”antal = 2”) sannolikt tolkningar som Slutför partiella frågan ”handlingar jaime”: ”papers av jaime teevan” och ”papers jaime grönt”.  Frågeifyllning service som genereras i stället för funderar på att endast exakta matchningar för författare ”jaime” eftersom begäran har angetts ”klar = 1”. Observera att canonical värdet ”j l grön” matchas via synonymen ”jamie grönt”, som anges i parsa.


```json
{
  "query": "papers by jaime",
  "interpretations": [
    {
      "logprob": -5.615,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#Author.Name\">jaime teevan</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(Author.Name=='jaime teevan')"
          }
        }
      ]
    },
    {
      "logprob": -5.849,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#Author.Name\" canonical=\"j l green\">jaime green</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(Author.Name=='j l green')"
          }
        }
      ]
    }
  ]
}
```  

När typ av semantiska utdata är ”fråga”, som i följande exempel matchande objekt kan hämtas genom att skicka *output.value* till den [ *utvärdera* ](evaluateMethod.md) API via *uttryck* parametern.

`http://<host>/evaluate?expr=Composite(AA.AuN=='jaime teevan')`
  
