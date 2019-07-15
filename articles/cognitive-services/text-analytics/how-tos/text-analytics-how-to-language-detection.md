---
title: Identifiera språk med den REST API för textanalys | Microsoft Docs
description: Identifiera språk med hjälp av REST API för textanalys från Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 02/26/2019
ms.author: aahi
ms.openlocfilehash: e1adeb34cf999f471bb183e4d7de9c65427252bb
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "67986515"
---
# <a name="example-detect-language-with-text-analytics"></a>Exempel: Identifiera språk med textanalys

Den [språkidentifiering](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) funktion i Azure Text Analytics REST API utvärderar textinmatning för varje dokument och returnerar språk-ID: n med ett värde som anger styrkan hos analysen.

Den här funktionen är användbar för innehållslager samlar in godtycklig text, där språket är okänt. Du kan parsa resultatet av den här analysen för att avgöra vilket språk som används i dokumentet. Svaret returnerar också en poäng som återspeglar förtroende för modellen. Poäng-värdet är mellan 0 och 1.

Funktionen språkidentifiering kan identifiera en mängd olika språk, varianter, dialekter och vissa regionala eller kulturella språk. Den exakta listan över språk för den här funktionen publiceras inte.

Om du har innehåll som uttrycks i ett språk som används mindre ofta kan prova du funktionen språkidentifiering för att se om det returnerar en kod. Svaret för språk som inte kan identifieras är `unknown`.

> [!TIP]
> Textanalys ger även en Linux-baserad Docker-containeravbildning för språkidentifiering, så att du kan [installera och köra Textanalys-containern](text-analytics-how-to-install-containers.md) nära dina data.

## <a name="preparation"></a>Förberedelse

Du måste ha JSON-dokument i det här formatet: ID och text.

Dokumentets storlek måste vara under 5,120 tecken per dokument. Du kan ha upp till 1 000 objekt (ID) per samling. Samlingen skickas i begäranstexten. I följande exempel är ett exempel på innehåll som du kan skicka in för språkidentifiering:

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

Läs mer på begäran definition [anropa API för textanalys](text-analytics-how-to-call-api.md). Följande punkter har anges på nytt för enkelhetens skull:

+ Skapa en POST-begäran. API-dokumentationen för den här begäran finns i den [API: T för språk](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7).

+ Ange HTTP-slutpunkt för språkidentifiering. Använd antingen en Text Analytics-resurs på Azure eller en initierad [textanalys behållare](text-analytics-how-to-install-containers.md). Det måste innehålla den `/languages` resurs: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`.

+ Ange en begäransrubrik som inkluderar åtkomstnyckeln för textanalysåtgärder. Mer information finns i [hitta slutpunkter och åtkomstnycklar](text-analytics-how-to-access-key.md).

+ Ange den JSON-dokumentsamling som du har förberett för den här analysen i begärandetexten.

> [!Tip]
> Använd [Postman](text-analytics-how-to-call-api.md) eller öppna **API-testkonsolen** i [dokumentationen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) för att strukturera en begäran och skicka den till tjänsten.

## <a name="step-2-post-the-request"></a>Steg 2: Skicka begäran

Analysen utförs när begäran har tagits emot. Information om storlek och antal begäranden kan du skicka per minut och sekund, visa den [databegränsningar](../overview.md#data-limits) avsnittet i översikten.

Kom ihåg att tjänsten är tillståndslös. Inga data lagras i ditt konto. Resultaten returneras omedelbart i svaret.


## <a name="step-3-view-the-results"></a>Steg 3: Visa resultatet

Alla POST-förfrågningar returnerar en JSON-formaterade svar med ID: N och upptäcks egenskaper.

Utdata returneras direkt. Du kan strömma resultaten till ett program som stöder JSON eller spara utdata till en fil på det lokala systemet. Importera sedan utdata till ett program som du kan använda för att sortera, söka och manipulera data.

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

Om analysatorn det går inte att parsa indata, returnerar `(Unknown)`. Ett exempel är om du skickar in ett textblock som består enbart av arabiska siffror.

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
### <a name="mixed-language-content"></a>Blandat innehåll

Blandat innehåll inom samma dokument returnerar språk med största representation i innehållet, men med en lägre positivt klassificering. Omdömet återspeglar marginell styrkan hos utvärderingen. I följande exempel är indata en blandning av engelska, spanska och franska. Det dominerande språket fastställs genom att analysatorn räknar tecken i varje segment.

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

Resultatet består av dominerande språk med ett resultat på mindre än 1,0, vilket betyder en svagare andelen förtroende.

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

I den här artikeln har du lärt dig begrepp och arbetsflöde för språkidentifiering genom att använda textanalys i Azure Cognitive Services. Följande punkter har beskrivs och visas:

+ [Språkidentifiering](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) är tillgänglig för en mängd olika språk, varianter, dialekter och vissa regionala eller kulturella språk.
+ JSON-dokument i begärandetexten innehåller ID: T och text.
+ POST-begäran är att en `/languages` slutpunkt med hjälp av en personligt anpassad [åtkomst till nyckeln och en slutpunkt](text-analytics-how-to-access-key.md) som är giltig för prenumerationen.
+ Svarsutdata består av språk-ID: n för varje dokument-ID. Utdata kan strömmas till någon app som tar emot JSON. Exempelappar innehåller Excel och Power BI för att nämna några.

## <a name="see-also"></a>Se också 

 [Översikt över Textanalys](../overview.md)  
 [Vanliga frågor och svar (FAQ)](../text-analytics-resource-faq.md)</br>
 [Produktsida för textanalys](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Analysera känsla](text-analytics-how-to-sentiment-analysis.md)
