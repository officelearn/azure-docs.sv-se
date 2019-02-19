---
title: Identifiera språk med REST API för textanalys | Microsoft Docs
description: Så här identifierar du språk med hjälp av REST API:et för textanalys från Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 549ef0d073a740edf700c54d02a0d9ef247db0b7
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56245097"
---
# <a name="example-how-to-detect-language-with-text-analytics"></a>Exempel: Hur man identifierar språk i Textanalys

[Språkidentifiering-API:et](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) utvärderar textindata och för varje dokument och returnerar språkidentifierare med ett värde som anger styrkan hos analysen. Textanalysen kan identifiera upp till 120 språk.

Den här funktionen är användbar för innehållslager samlar in godtycklig text, där språket är okänt. Du kan parsa resultatet av den här analysen för att avgöra vilket språk som används i dokumentet. Svaret returnerar också ett resultat som återspeglar modellens förtroende (ett värde mellan 0 och 1).

> [!TIP]
> Textanalys ger även en Linux-baserad Docker-containeravbildning för språkidentifiering, så att du kan [installera och köra Textanalys-containern](text-analytics-how-to-install-containers.md) nära dina data.

## <a name="preparation"></a>Förberedelse

Du måste ha JSON-dokument i följande format: id, text

Dokumentstorleken måste vara under 5 000 tecken per dokument och du kan ha upp till 1 000 objekt (ID:n) per samling. Samlingen skickas i begäranstexten. Följande är ett exempel på innehåll som du kan skicka in för språkidentifiering.

   ```
    {
        "documents": [
            {
                "id": "1",
                "text": "This document is in English."
            },
            {
                "id": "2",
                "text": "Este documento está en inglés."
            },
            {
                "id": "3",
                "text": "Ce document est en anglais."
            },
            {
                "id": "4",
                "text": "本文件为英文"
            },                
            {
                "id": "5",
                "text": "Этот документ на английском языке."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Steg 1: Strukturera begäran

Information om begäransdefinitionen finns i [Hur anropar man textanalys API:et](text-analytics-how-to-call-api.md). Följande punkter har anges på nytt för enkelhetens skull:

+ Skicka en **POST**-begäran. Läs API-dokumentationen för denna begäran: [Språkidentifierings-API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)

+ Ange HTTP-slutpunkten för språkidentifiering, med hjälp av en Textanalys-resurs på Azure eller en instansierad [Textanalys-container](text-analytics-how-to-install-containers.md). Den måste innehålla `/languages`-resursen: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages`

+ Ange en begäransrubrik som inkluderar åtkomstnyckeln för textanalysåtgärder. Mer information finns i [Hitta slutpunkter och åtkomstnycklar](text-analytics-how-to-access-key.md).

+ Ange den JSON-dokumentsamling som du har förberett för den här analysen i begärandetexten

> [!Tip]
> Använd [Postman](text-analytics-how-to-call-api.md) eller öppna **API-testkonsolen** i [dokumentationen](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) för att strukturera en begäran och skicka den till tjänsten.

## <a name="step-2-post-the-request"></a>Steg 2: Publicera begäran

Analysen utförs när begäran har tagits emot. Tjänsten accepterar upp till 100 begäranden per minut. Varje begäran kan vara högst 1 MB.

Kom ihåg att tjänsten är tillståndslös. Inga data lagras i ditt konto. Resultaten returneras omedelbart i svaret.


## <a name="step-3-view-results"></a>Steg 3: Visa resultat

Alla POST-begäranden returnerar ett JSON-formaterat svar med ID:n och identifierade egenskaper.

Utdata returneras direkt. Du kan strömma resultaten till ett program som stöder JSON eller spara utdata till en fil på den lokala datorn och sedan importera den till ett program som gör så att du kan sortera, söka och hantera data.

Resultat för exempelbegäran bör se ut som följande JSON. Observera att det är ett dokument med flera objekt. Utdata är på engelska. Språkidentifierarna innehåller ett eget namn och en språkkod i [ISO 639-1](https://www.iso.org/standard/22109.html)-format.

Ett positivt resultat på 1.0 uttrycker högsta möjliga förtroendenivå för analysen.



```
{
    "documents": [
        {
            "id": "1",
            "detectedLanguages": [
                {
                    "name": "English",
                    "iso6391Name": "en",
                    "score": 1
                }
            ]
        },
        {
            "id": "2",
            "detectedLanguages": [
                {
                    "name": "Spanish",
                    "iso6391Name": "es",
                    "score": 1
                }
            ]
        },
        {
            "id": "3",
            "detectedLanguages": [
                {
                    "name": "French",
                    "iso6391Name": "fr",
                    "score": 1
                }
            ]
        },
        {
            "id": "4",
            "detectedLanguages": [
                {
                    "name": "Chinese_Simplified",
                    "iso6391Name": "zh_chs",
                    "score": 1
                }
            ]
        },
        {
            "id": "5",
            "detectedLanguages": [
                {
                    "name": "Russian",
                    "iso6391Name": "ru",
                    "score": 1
                }
            ]
        }
    ],
```

### <a name="ambiguous-content"></a>Tvetydig innehåll

Om analysatorn inte kan parsa indata (låt oss t.ex. anta att du skickat ett textblock som enbart består av arabiska siffror) så returnerar den `(Unknown)`.

```
    {
      "id": "5",
      "detectedLanguages": [
        {
          "name": "(Unknown)",
          "iso6391Name": "(Unknown)",
          "score": "NaN"
        }
      ]
```
### <a name="mixed-language-content"></a>Blandat språkinnehåll

Blandat språkinnehåll i samma dokument returnerar språket med den största representationen i innehållet, men med lägre positiv klassificering, vilket speglar utvärderingens marginella styrka. I följande exempel är indata en blandning av engelska, spanska och franska. Det dominerande språket fastställs genom att analysatorn räknar tecken i varje segment.

**Indata**

```
{
  "documents": [
    {
      "id": "1",
      "text": "Hello, I would like to take a class at your University. ¿Se ofrecen clases en español? Es mi primera lengua y más fácil para escribir. Que diriez-vous des cours en français?"
    }
  ]
}
```

**Resultat**

Resulterande utdata består av dominerande språket, med ett resultat på mindre än 1,0, vilket indikerar en svagare förtroendenivå.

```
{
  "documents": [
    {
      "id": "1",
      "detectedLanguages": [
        {
          "name": "Spanish",
          "iso6391Name": "es",
          "score": 0.9375
        }
      ]
    }
  ],
  "errors": []
}
```

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt känna begrepp och arbetsflöden för språkidentifiering med hjälp av textanalys i Cognitive Services. Det följande är en snabb påminnelse om de viktiga aspekter som förklarats och beskrivits tidigare:

+ [Språkidentifierings-API:et](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) är tillgänglig för 120 språk.
+ JSON-dokument i begärandetexten innehåller ID och text.
+ POST-begäran riktas till en `/languages`-slutpunkt, med hjälp av en personligt anpassad [åtkomstnyckel och en slutpunkt](text-analytics-how-to-access-key.md) som är giltig för prenumerationen.
+ Svarsutdata, vilka består av språkidentifierare för varje dokument-ID, kan strömmas till vilken app som helst som tar emot JSON, inklusive Excel och Power BI bara för att nämna några.

## <a name="see-also"></a>Se även 

 [Översikt över Textanalys](../overview.md)  
 [Vanliga frågor och svar (FAQ)](../text-analytics-resource-faq.md)</br>
 [Produktsida för textanalys](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Analysera känsla](text-analytics-how-to-sentiment-analysis.md)
