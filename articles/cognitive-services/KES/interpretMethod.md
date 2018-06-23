---
title: Tolka metod i Knowledge utforskning Service API | Microsoft Docs
description: Lär dig hur du använder metoden Interpret i den kunskap utforskning Service (KES) API i kognitiva Services.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: ef68d98dacf393abf8d030b9312217ea380947d2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351714"
---
# <a name="interpret-method"></a>Tolka metod
Den *tolka* metoden tar en frågesträng för naturligt språk och returnerar formaterad tolkningar av användaravsikt baserat på data i grammatik och index.  För att ge en interaktiv sökinställningar, den här metoden kan anropas eftersom varje tecken har angetts av användaren med den *fullständig* parametern värdet 1 aktiverar automatisk komplettering förslag.

## <a name="request"></a>Förfrågan
`http://<host>/interpret?query=<query>[&<options>]`

Namn|Värde| Beskrivning
----|----|----
DocumentDB    | Textsträng | Frågan som angetts av användaren.  Om fullständig har angetts till 1, tolkas fråga som ett prefix för att fråga automatisk komplettering förslag.        
Slutför | 0 (standard) eller 1 | 1 innebär att automatisk komplettering förslag genereras baserat på data i grammatik och index.         
count    | Antal (standard = 10) | Maximalt antal tolkningar att returnera.         
förskjutning   | Antal (standard = 0) | Indexet för den första tolkningen att returnera.  Till exempel *count = 2 & förskjutning = 0* returnerar tolkningar 0 och 1. *Antal = 2 & förskjutning = 2* returnerar tolkningar 2 och 3.       
timeout  | Antal (standard = 1000) | Tidsgräns i millisekunder. Endast tolkningar hittades före tidsgränsen har gått ut returneras.

Med hjälp av den *antal* och *offset* parametrar, ett stort antal resultat kan erhållas inkrementellt över flera förfrågningar.

## <a name="response-json"></a>Svar (JSON)
JSONPath     | Beskrivning
---------|---------
$.query |*frågan* parametern från begäran.
$.interpretations   |Matris för 0 eller fler sätt att matcha inkommande frågan mot grammatik.
$.interpretations [\*] .logprob   |Relativa loggen sannolikheten för tolkningen (< = 0).  Högre värden är mer sannolikt.
$.interpretations [\*] .parse |XML-sträng som visar hur varje del av frågan har tolkas.
$.interpretations [\*] .rules |Matris med-1 eller flera regler som definierats i grammatiken anropas under tolkning.
$.interpretations [\*] .rules [\*] .name    |Namnet på regeln.
$.interpretations [\*] .rules [\*] .output  |Semantiska utdata från regeln.
$.interpretations [\*] .rules [\*]. output.type |Datatypen för semantisk utdata.
$.interpretations [\*] .rules [\*]. output.value|Värdet för semantisk utdata.  
$.aborted | TRUE om tidsgränsen för begäran.

### <a name="parse-xml"></a>Tolka XML
Parsa XML annoterar (slutförda) frågan med information om hur den matchas mot reglerna i grammatiken och attribut i indexet.  Nedan visas ett exempel från domänen academic publikationer:

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

Den `<rule>` element avgränsar intervallet i frågan som matchar regeln som anges av dess `name` attribut.  Den kan kapslas när parse omfattar regeln referenser i grammatik.

Den `<attr>` element avgränsar intervallet i fråga matchar attributet index som anges av dess `name` attribut.  När matchningen omfattar en synonym i inkommande frågan i `canonical` attributet kommer att innehålla kanoniskt värde matchar synonymen från indexet.

## <a name="example"></a>Exempel
I exemplet academic publikationer returnerar följande begäran upp till 2 automatisk komplettering förslag för prefix frågan ”handlingar jaime”:

`http://<host>/interpret?query=papers by jaime&complete=1&count=2`

Svaret innehåller två översta (”count = 2”) sannolikt tolkningar som Slutför partiella frågan ”handlingar jaime”: ”papers av jaime teevan” och ”papers jaime grönt”.  Tjänsten genereras frågan slutföranden i stället för att ta hänsyn till endast exakt matchar för författare ”jaime” eftersom begäran har angetts ”klar = 1”. Observera att matchas kanoniskt värde ”j l grön” via synonymen ”Johan grönt”, som anges i parsa.


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

När du anger semantiska är ”fråga”, som i följande exempel matchande objekt kan hämtas genom att skicka *output.value* till den [ *utvärdera* ](evaluateMethod.md) API via *uttryck* parameter.

`http://<host>/evaluate?expr=Composite(AA.AuN=='jaime teevan')`
  
