---
title: Gränssnittsdefinition för anpassade kunskaper
titleSuffix: Azure Cognitive Search
description: Anpassat gränssnitt för dataextrahering för anpassad webb-api-färdighet i en AI-anrikningspipeline i Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 78f5f6eda28bed164668445b5671dad92f8dedd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77500257"
---
# <a name="how-to-add-a-custom-skill-to-an-azure-cognitive-search-enrichment-pipeline"></a>Så här lägger du till en anpassad färdighet i en Azure Cognitive Search-anrikningspipeline

En [anrikningspipeline](cognitive-search-concept-intro.md) i Azure Cognitive Search kan monteras från [inbyggda kognitiva färdigheter](cognitive-search-predefined-skills.md) samt [anpassade kunskaper](cognitive-search-custom-skill-web-api.md) som du personligen skapar och lägger till i pipelinen. I den här artikeln får du lära dig hur du skapar en anpassad färdighet som exponerar ett gränssnitt så att det kan inkluderas i en AI-anrikningspipeline. 

Genom att skapa en anpassad färdighet kan du infoga omvandlingar som är unika för ditt innehåll. En anpassad färdighet körs oberoende av dem och tillämpar det anrikningssteg du behöver. Du kan till exempel definiera fältspecifika anpassade entiteter, skapa anpassade klassificeringsmodeller för att differentiera affärs- och ekonomikontrakt och dokument eller lägga till en taligenkänningsfärdighet för att nå djupare in i ljudfiler för relevant innehåll. Ett steg-för-steg-exempel finns i [Exempel: Skapa en anpassad färdighet för AI-anrikning](cognitive-search-create-custom-skill-example.md).

 Oavsett anpassad kapacitet du behöver, det finns ett enkelt och tydligt gränssnitt för att ansluta en anpassad färdighet till resten av anrikning pipeline. Det enda kravet för att ingå i en [kompetens](cognitive-search-defining-skillset.md) är förmågan att acceptera indata och avge resultat på ett sätt som är förbrukningsbart inom kompetensen som helhet. Fokus för den här artikeln är på indata- och utdataformat som anrikningspipelinen kräver.

## <a name="web-api-custom-skill-interface"></a>Anpassat färdighetsgränssnitt för webb-API

Anpassade WebAPI-färdighetsslutpunkter som standard timeout om de inte returnerar ett svar i ett 30-sekundersfönster. Indexeringspipelinen är synkron och indexeringen ger ett timeout-fel om ett svar inte tas emot i det fönstret.  Det är möjligt att konfigurera tidsgränsen till upp till 230 sekunder genom att ställa in timeout-parametern:

```json
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill has a 230 second timeout",
        "uri": "https://[your custom skill uri goes here]",
        "timeout": "PT230S",
```

Kontrollera att URI:n är säker (HTTPS).

För närvarande är den enda mekanismen för att interagera med en anpassad färdighet via ett webb-API-gränssnitt. Webb-API-behoven måste uppfylla kraven som beskrivs i det här avsnittet.

### <a name="1--web-api-input-format"></a>1. Inmatningsformat för webb-API

Webb-API:et måste acceptera en matris med poster som ska bearbetas. Varje post måste innehålla en "egenskapspåse" som är indata som anges i webb-API:et. 

Anta att du vill skapa en enkel berikare som identifierar det första datumet som nämns i texten i ett kontrakt. I det här exemplet accepterar färdigheten ett enda *indatakontraktText* som kontraktstext. Färdigheten har också en enda utdata, vilket är datumet för kontraktet. För att göra berikaren mer intressant, returnera detta *kontraktDate* i form av en komplex typ i flera delar.

Webb-API:et ska vara redo att ta emot en bunt indataposter. Varje medlem *values* i värdematrisen representerar indata för en viss post. Varje post måste ha följande element:

+ En *recordId-medlem* som är den unika identifieraren för en viss post. När din berikare returnerar resultaten måste den tillhandahålla den här *recordId* för att den som ringer ska kunna matcha postresultaten med sina indata.

+ En *datamedlem,* som i huvudsak är en påse med indatafält för varje post.

Om du vill vara mer konkret, enligt exemplet ovan, bör ditt webb-API förvänta sig begäranden som ser ut så här:

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
I verkligheten kan din tjänst bli kallad med hundratals eller tusentals poster istället för bara de tre som visas här.

### <a name="2-web-api-output-format"></a>2. Utdataformat för webb-API

Formatet på utdata är en uppsättning poster som innehåller en *recordId*och en egenskapspåse 

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

Det här exemplet har bara en utdata, men du kan mata ut mer än en egenskap. 

### <a name="errors-and-warning"></a>Fel och varning

Som visas i föregående exempel kan du returnera felmeddelanden och varningsmeddelanden för varje post.

## <a name="consuming-custom-skills-from-skillset"></a>Konsumerar anpassade färdigheter från kompetens

När du skapar en webb-API-enricher kan du beskriva HTTP-rubriker och parametrar som en del av begäran. Kodavsnittet nedan visar hur parametrar för begäran och *valfria* HTTP-huvuden kan beskrivas som en del av kompetensdefinitionen. HTTP-huvuden är inte ett krav, men de gör att du kan lägga till ytterligare konfigurationsfunktioner till din färdighet och ange dem från kompetensdefinitionen.

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

Den här artikeln omfattade gränssnittskrav som krävs för att integrera en anpassad färdighet i en kompetens. Klicka på följande länkar om du vill veta mer om anpassade färdigheter och kompetenssammansättning.

+ [Power Skills: en databas med anpassade färdigheter](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Exempel: Skapa en anpassad färdighet för AI-anrikning](cognitive-search-create-custom-skill-example.md)
+ [Hur man definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Skapa skillset (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Så här mappar du berikade fält](cognitive-search-output-field-mapping.md)
