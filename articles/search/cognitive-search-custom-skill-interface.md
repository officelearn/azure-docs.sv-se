---
title: Gränssnittsdefinitionen för anpassade kognitiv sökning – Azure Search-kunskaper
description: Anpassade data extrahering gränssnitt för webb-api anpassade färdighet i kognitiv sökning pipeline i Azure Search.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: e4fe511228f6e80a17af8325ee74ae0927a760bd
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55754735"
---
# <a name="how-to-add-a-custom-skill-to-a-cognitive-search-pipeline"></a>Lägga till en anpassad kunskap till en pipeline för kognitiv sökning

En [kognitiv sökning indexering av pipeline](cognitive-search-concept-intro.md) i Azure Search kan monteras från [fördefinierade kunskaper](cognitive-search-predefined-skills.md) samt [anpassade funktioner](cognitive-search-custom-skill-web-api.md) som du skapar personligt och lägga till den pipeline. I den här artikeln lär du dig hur du skapar en anpassad kunskap som exponerar ett gränssnitt som gör det möjligt att ingå i en pipeline för kognitiv sökning. 

Att skapa en anpassad färdighet ger dig ett sätt att infoga transformationer som är unika för ditt innehåll. En anpassad färdighet körs oberoende av varandra, oavsett berikande steg som du behöver. Du kan till exempel definiera fält-specifik anpassade entiteter, skapa modeller för anpassad klassificering för att skilja företag och finansiella kontrakt och dokument eller Lägg till ett tal för kompetens att nå djupare i ljudfiler för relevant innehåll. Steg för steg-exempel finns i [exempel: skapa en anpassad färdighet](cognitive-search-create-custom-skill-example.md).

 Den anpassade funktionen som du behöver, finns det en enkel och tydlig användargränssnitt för att ansluta en anpassad kunskap till resten av berikande pipelinen. Det enda kravet ska ingå i en [kompetens](cognitive-search-defining-skillset.md) är möjligheten att acceptera indata och genererar utdata på ett sätt som är färdiga att konsumeras inom kompetens som helhet. Fokus i den här artikeln finns på de format som in- och utdata som kräver att berikande pipelinen.

## <a name="web-api-custom-skill-interface"></a>Anpassade färdighet webbgränssnitt API

Anpassade slutpunkter på grund av WebAPI-kunskaper med standardvärdet för timeout om de inte returnerar något svar inom en 30-sekundersfönster. För indexering av pipeline synkront, indexering skapas ett timeout-fel om inte svar i fönstret.  Det är möjligt att konfigurera timeout-värde för att vara upp till 90 sekunder, genom att ange timeout-parameter:

```json
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill has a 90 second timeout",
        "uri": "https://[your custom skill uri goes here]",
        "timeout": "PT90S",
```

För närvarande är den enda mekanismen för att interagera med en anpassad kunskap genom ett webb-API-gränssnitt. Webb-API måste behöver uppfylla kraven som beskrivs i det här avsnittet.

### <a name="1--web-api-input-format"></a>1.  Format för webb-API-indata

Webb-API måste godkänna en matris med poster som ska bearbetas. Varje post måste innehålla en ”egenskapsuppsättning” som indata till ditt webb-API. 

Anta att du vill skapa en enkel enricher som identifierar det första datumet som nämns i texten i ett kontrakt. I det här exemplet kompetensen godkänner en enda indata *contractText* som kontrakt-text. Kompetensen har också ett enda utflöde, vilket är datumet då avtalet. Om du vill göra enricher mer intressant återgå det *contractDate* i form av en flerdelad komplex typ.

Ditt webb-API ska vara redo att ta emot en batch med indata-poster. Varje medlem i den *värden* matrisen representerar indata för en viss post. Varje post måste ha följande element:

+ En *recordId* medlem som är den unika identifieraren för en viss post. När din enricher returnerar resultat, den måste ange detta *recordId* avsätts anroparen vill matcha posten resultatet till sin syn.

+ En *data* medlem, vilket är i grunden en egenskapsuppsättning av inmatningsfält för varje post.

För att vara mer konkret per i exemplet ovan, ditt webb-API kan förvänta dig begäranden som ser ut så här:

```json
{
    "values": [
      {
        "recordId": "a1",
        "data":
           {
             "contractText": 
                "This is a contract that was issues on November 3, 2017 and that involves... "
           }
      },
      {
        "recordId": "b5",
        "data":
           {
             "contractText": 
                "In the City of Seattle, WA on February 5, 2018 there was a decision made..."
           }
      },
      {
        "recordId": "c3",
        "data":
           {
             "contractText": null
           }
      }
    ]
}
```
I verkligheten kan hämta din tjänst volat med hundratals eller tusentals poster i stället för de tre som visas här.

### <a name="2-web-api-output-format"></a>2. Utdataformat för webb-API

Formatet för utdata är en uppsättning poster som innehåller en *recordId*, och en egenskapsuppsättning 

```json
{
  "values": 
  [
      {
        "recordId": "b5",
        "data" : 
        {
            "contractDate":  { "day" : 5, "month": 2, "year" : 2018 }
        }
      },
      {
        "recordId": "a1",
        "data" : {
            "contractDate": { "day" : 3, "month": 11, "year" : 2017 }                    
        }
      },
      {
        "recordId": "c3",
        "data" : 
        {
        },
        "errors": [ { "message": "contractText field required "}   ],  
        "warnings": [ {"message": "Date not found" }  ]
      }
    ]
}
```

Det här exemplet har endast en utdata, men du kunde resulterande utdata bli mer än en egenskap. 

### <a name="errors-and-warning"></a>Fel och varningar

I exemplet ovan visas kan du returnera fel- och varningsmeddelanden för varje post.

## <a name="consuming-custom-skills-from-skillset"></a>Använda anpassade funktioner från kompetens

När du skapar en webb-API-enricher kan beskriva du HTTP-huvuden och parametrar som en del av begäran. Fragmentet nedan visar hur parametrarna och HTTP-huvuden kan beskrivas som en del av definitionen kompetens.

```json
{
    "skills": [
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill calls an Azure function, which in turn calls TA sentiment",
        "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/DateExtractor?language=en",
        "context": "/document",
        "httpHeaders": {
            "DateExtractor-Api-Key": "foo"
        },
        "inputs": [
          {
            "name": "contractText",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "contractDate",
            "targetName": "date"
          }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

+ [Exempel: Skapa en anpassad färdighet för översätta Text-API](cognitive-search-create-custom-skill-example.md)
+ [Hur du definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Skapa kompetens (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Avancerad och mappning](cognitive-search-output-field-mapping.md)
