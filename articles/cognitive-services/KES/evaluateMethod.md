---
title: Utvärdera metod i Knowledge utforskning Service API | Microsoft Docs
description: Lär dig hur du använder metoden utvärdera i den kunskap utforskning Service (KES) API i kognitiva Services.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: fc3d73b326b565cfe40d1b82cc419357b28a801a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351663"
---
# <a name="evaluate-method"></a>Utvärdera metod
Den *utvärdera* metoden utvärderar och returnerar resultatet av ett strukturerade frågeuttryck baserat på data i indexet.

Normalt hämtas uttryck från ett svar till metoden interpret.  Men du kan också skapa frågeuttryck själv (se [strukturerade frågeuttryck](Expressions.md)).  

## <a name="request"></a>Förfrågan 
`http://<host>/evaluate?expr=<expr>&attributes=<attrs>[&<options>]`   

Namn|Värde|Beskrivning
----|----|----
uttryck       | Textsträng | Strukturerade frågeuttryck som väljer en delmängd av indexet entiteter.
attribut | Textsträng | Kommaavgränsad lista med attribut som ska ingå i svaret.
count      | Antal (standard = 10) | Maximalt antal resultat ska returneras.
förskjutning     | Antal (standard = 0) | Indexet för det första resultatet ska returneras.
OrderBy |   Textsträng | Namnet på attributet som används för att sortera resultaten, följt av valfri sorteringsordning (standard = asc) ”:*%{attrname/*[: (asc&#124;desc)]”.  Om inget anges returneras resultatet genom att minska sannolikheten för den naturliga logaritmen.
timeout  | Antal (standard = 1000) | Tidsgräns i millisekunder. Endast resultat beräknade innan timeout förflutit.

Med hjälp av den *antal* och *offset* parametrar, ett stort antal resultat kan erhållas inkrementellt över flera förfrågningar.
  
## <a name="response-json"></a>Svar (JSON)
JSONPath|Beskrivning
----|----
$.expr | *uttryck* parametern från begäran.
$.entities | Matris för 0 eller fler objekt entiteter matchar structured query-uttryck. 
$.aborted | TRUE om tidsgränsen för begäran.

Varje entitet innehåller en *logprob* värde och värdena för de begärda attributen.

## <a name="example"></a>Exempel
I exemplet academic publikationer följande begäran skickar ett strukturerade frågeuttryck (potentiellt från utdata från ett *tolka* begäran) och hämtar några attribut för övre 2 matchar enheter:

`http://<host>/evaluate?expr=Composite(Author.Name=='jaime teevan')&attributes=Title,Y,Author.Name,Author.Id&count=2`

Svaret innehåller upp 2 (”count = 2”) troligen matchar entiteter.  För varje entitet returneras rubrik, år, författare och författare ID-attribut.  Observera hur strukturen för sammansatt attributvärden matchar på sätt som de har angetts i datafilen. 

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
