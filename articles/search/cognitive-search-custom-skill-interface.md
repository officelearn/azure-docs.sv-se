---
title: Gränssnitt definition för anpassade kunskaper i en kognitiva Sök pipeline (Azure Search) | Microsoft Docs
description: Anpassade data extrahering gränssnitt för webb-api anpassade kunskaper i kognitiva Sök pipeline i Azure Search.
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: f415eb6080a02d25fc47c40b2719544d2ea99c5b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-add-a-custom-skill-to-a-cognitive-search-pipeline"></a>Hur du lägger till en anpassad kunskap för en pipeline kognitiva sökning

Lär dig hur du lägger till en anpassad kunskap för en pipeline kognitiva Sök i den här artikeln. En [kognitiva Sök pipeline för fulltextindexering](cognitive-search-concept-intro.md) i Azure Search kan monteras från [fördefinierade kunskaper](cognitive-search-predefined-skills.md) och anpassade kunskaper som du personligt skapa och lägga till pipelinen.

Skapa en anpassad kunskap ger dig ett sätt att infoga transformationer unika till ditt innehåll. En anpassad kunskap kör oberoende av varandra, oavsett berikande steg som du behöver använda. Du kan till exempel definiera anpassade entiteter för specifika fält, bygga modeller för anpassad klassificering för att skilja företag och finansiella kontrakt och dokument eller Lägg till en speech recognition kunskap till når djupare in ljudfiler för relevant innehåll. Steg för steg-exempel finns [exempel: skapa en anpassad kunskap](cognitive-search-create-custom-skill-example.md).

 Den anpassade kapaciteten som du behöver det finns en klar och tydlig gränssnitt för att ansluta en anpassad kunskap till resten av berikande pipeline. Det enda kravet för att inkluderas i en [kunskaper](cognitive-search-defining-skillset.md) har möjlighet att acceptera indata och generera utdata på sätt som kan användas inom kunskaper som helhet. I den här artikeln fokuserar på de inkommande och utgående format som kräver berikande pipeline.

## <a name="web-api-custom-skill-interface"></a>Anpassade kunskaper webbgränssnitt API

Den enda mekanismen för att interagera med en anpassad kunskap är för närvarande via ett Web API-gränssnitt. Webb-API måste behov uppfylla kraven som beskrivs i det här avsnittet.

### <a name="1--web-api-input-format"></a>1.  Format för Web API-indata

Webb-API måste godkänna en matris med poster som ska bearbetas. Varje post måste innehålla en ”egenskapsuppsättning” som indata som angetts för webb-API. 

Anta att du vill skapa en enkel enricher som identifierar det första datumet som nämns i ett avtal. I det här exemplet kompetensen accepterar en enda inmatning *contractText* som text för kontraktet. Kompetensen har också ett enda utflöde är datumet för kontraktet. Om du vill göra enricher mer intressant returnera detta *contractDate* i form av en komplex typ med flera delar.

Webb-API bör vara redo att ta emot en batch med indata-poster. Varje medlem i den *värden* matrisen representerar indata för en viss post. Varje post måste ha följande element:

+ En *recordId* medlem som är den unika identifieraren för en viss post. När din enricher resultat, det måste finnas *recordId* för att tillåta anroparen vill matcha posten resultatet till sina indata.

+ En *data* medlem som är i grunden en påse inmatningsfält för varje post.

För att vara mer konkret per exemplet ovan, webb-API kan förvänta begäranden som ser ut så här:

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
I verkligheten kan tjänsten anropades med hundratals eller tusentals poster i stället för de tre som visas här.

### <a name="2-web-api-output-format"></a>2. Format för Web API-utdata

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

Det här exemplet har endast en utdata, men du kan skapa mer än en egenskap. 

### <a name="errors-and-warning"></a>Fel och varning

Som det visas i exemplet ovan, kan du returnera fel och varningar för varje post.

## <a name="consuming-custom-skills-from-skillset"></a>Använda anpassade kunskaper kunskaper

När du skapar ett Web API-enricher, kan du beskriva HTTP-huvuden och parametrar som en del av begäran. Kodfragmentet nedan visar hur de begäranparametrarna och HTTP-huvuden kan beskrivas som en del av definitionen kunskaper.

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

+ [Exempel: Skapa en anpassad kunskap översätta Text-API: t](cognitive-search-create-custom-skill-example.md)
+ [Hur du definierar en kunskaper](cognitive-search-defining-skillset.md)
+ [Skapa kunskaper (REST)](ref-create-skillset.md)
+ [Utökade mappning](cognitive-search-output-field-mapping.md)
