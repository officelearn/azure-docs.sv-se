---
title: Identifiera anvisningar språk i Text Analytics REST-API (Microsoft kognitiva tjänster i Azure) | Microsoft Docs
description: Så här identifiera språk med Text Analytics REST API i kognitiva Microsoft-tjänster i Azure i den här genomgången självstudiekursen.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 3/07/2018
ms.author: heidist
ms.openlocfilehash: f8e2d9a36533c298addcf42d3cb2061e9c2d1ac7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352230"
---
# <a name="how-to-detect-language-in-text-analytics"></a>Hur du identifierar språket i texten Analytics

Den [språk identifiering API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) utvärderar text indata- och för varje dokument och returnerar språkidentifierare med en poäng som anger styrkan hos analys. Textanalys identifierar upp till 120 språk.

Den här funktionen är användbart för innehåll lagras att samla in godtycklig texten där språk är okänd. Du kan analysera resultaten av den här analysen för att avgöra vilket språk som används i dokumentet. Svaret returnerar även en poäng som speglar förtroende för modellen (ett värde mellan 0 och 1).

## <a name="preparation"></a>Förberedelse

Du måste ha JSON-dokument i det här formatet: id, text

Storlek måste vara under 5 000 tecken per dokument och du kan ha upp till 1 000 objekt (ID) per samling. Samlingen skickas i meddelandetexten i begäran. Följande är ett exempel på innehåll som du kan skicka för språkidentifiering av.

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
                "text": "Этот документ находится на английском языке."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Steg 1: Struktur begäran

Information om begäran definition finns i [hur du anropar API: et för Text Analytics](text-analytics-how-to-call-api.md). Följande punkter räknas av praktiska skäl:

+ Skapa en **POST** begäran. Granska API-dokumentationen för denna begäran: [språk identifiering API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)

+ Ange HTTP-slutpunkten för språkidentifiering av. Det måste innehålla den `/languages` resursen: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages`

+ Ange ett huvud med snabbtangenten för Text Analytics-åtgärder. Mer information finns i [hur du hittar slutpunkter och åtkomstnycklar](text-analytics-how-to-access-key.md).

+ Ange samlingen JSON-dokument du förberedd för den här analysen i frågans brödtext

> [!Tip]
> Använd [Postman](text-analytics-how-to-call-api.md) eller öppna den **API-testet konsolen** i den [dokumentationen](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) struktur en begäran och skicka den till tjänsten.

## <a name="step-2-post-the-request"></a>Steg 2: Publicera begäran

Analys utförs vid mottagning av begäran. Tjänsten accepterar upp till 100 begäranden per minut. Varje begäran kan vara upp till 1 MB.

Kom ihåg att tjänsten är tillståndslösa. Inga data lagras i ditt konto. Resultaten returneras omedelbart i svaret.


## <a name="step-3-view-results"></a>Steg 3: Visa resultat

Alla POST-förfrågningar returnera en JSON-formaterad svar med ID: N och egenskaper identifierades.

Utdata returneras direkt. Du kan strömma resultaten till ett program som accepterar JSON eller spara utdata till en fil på den lokala datorn och sedan importera den till ett program som gör att du kan sortera, söka och manipulera data.

Resultat för exempelbegäran bör se ut som följande JSON. Observera att det är ett dokument med flera objekt. Utdata är på engelska. Språk-ID: n är ett eget namn och en språkkod i [ISO 639-1](https://www.iso.org/standard/22109.html) format.

Ett positivt resultat på 1.0 representerar högsta möjliga konfidensnivån för analys.



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

Om analyzer går inte att parsa indata (till exempel förutsätter att du har skickat ett textblock som endast består av arabiska siffror), returneras `(Unknown)`.

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
### <a name="mixed-language-content"></a>Blandat innehåll språk

Blandat innehåll i samma dokument returnerar språk med största representationen i innehållet, men med en lägre positivt klassificering, reflektion marginell styrkan hos utvärderingen. I följande exempel är indata en blandning av engelska och spanska franska. Analyzer räknar tecken i varje segment att bestämma övervägande språk.

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

Resultatet består av övervägande språk, med ett resultat på mindre än 1,0 anger en lägre nivå av säkerhet.

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

I den här artikeln får du har lärt dig begrepp och arbetsflödet för att identifiera språk med hjälp av Text Analytics i kognitiva Services. Följande är en snabb påminnelse om de viktigaste aspekterna beskrivs tidigare och visas:

+ [Identifiera språk API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) är tillgänglig på 120 språk.
+ JSON-dokument i begärandetexten innehåller id och text.
+ POST-begäran är att en `/languages` slutpunkten, med hjälp av en anpassad [åtkomst till nyckeln och en slutpunkt](text-analytics-how-to-access-key.md) som är giltig för prenumerationen.
+ Svarsutdata som består av språkidentifierare för varje dokument-ID, kan strömmas till någon app som accepterar JSON, inklusive Excel och Power BI att nämna några.

## <a name="see-also"></a>Se också 

 [Översikt över text Analytics](../overview.md)  
 [Vanliga frågor och svar (FAQ)](../text-analytics-resource-faq.md)</br>
 [Text Analytics produktsidan](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Analysera sentiment](text-analytics-how-to-sentiment-analysis.md)
