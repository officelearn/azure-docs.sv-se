---
title: Identifiera språk med REST-API:et för textanalys
titleSuffix: Azure Cognitive Services
description: Identifiera språk med hjälp av REST-API:et för textanalys från Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: d34f3a03e1bcd35c270d13c4dda57d0394a36e4b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70387789"
---
# <a name="example-detect-language-with-text-analytics"></a>Exempel: Identifiera språk med textanalys

Funktionen [Språkidentifiering](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) i AZURE Text Analytics REST API utvärderar textindata för varje dokument och returnerar språkidentifierare med en poäng som anger analysens styrka.

Den här funktionen är användbar för innehållslager samlar in godtycklig text, där språket är okänt. Du kan parsa resultatet av den här analysen för att avgöra vilket språk som används i dokumentet. Svaret returnerar också en poäng som återspeglar modellens förtroende. Poängvärdet är mellan 0 och 1.

Funktionen Språkidentifiering kan identifiera ett brett spektrum av språk, varianter, dialekter och vissa regionala eller kulturella språk. Den exakta listan över språk för den här funktionen publiceras inte.

Om du har innehåll som uttrycks på ett språk som används mindre ofta kan du prova funktionen Språkidentifiering för att se om den returnerar en kod. Svaret för språk som inte kan `unknown`identifieras är .

> [!TIP]
> Textanalys ger även en Linux-baserad Docker-containeravbildning för språkidentifiering, så att du kan [installera och köra Textanalys-containern](text-analytics-how-to-install-containers.md) nära dina data.

## <a name="preparation"></a>Förberedelse

Du måste ha JSON-dokument i det här formatet: ID och text.

Dokumentstorleken måste vara mindre än 5 120 tecken per dokument. Du kan ha upp till 1 000 objekt (ID) per samling. Samlingen skickas i begäranstexten. Följande exempel är ett exempel på innehåll som du kan skicka in för språkidentifiering:

```json
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

Mer information om definition av begäran finns i [Anropa API:et för textanalys](text-analytics-how-to-call-api.md). Följande punkter har anges på nytt för enkelhetens skull:

+ Skicka en POST-begäran. Information om hur du granskar API-dokumentationen för den här begäran finns i [API:et för språkidentifiering](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7).

+ Ange HTTP-slutpunkt för språkidentifiering. Använd antingen en Text Analytics-resurs på Azure eller en instansierad [Text Analytics-behållare](text-analytics-how-to-install-containers.md). Du måste `/text/analytics/v2.1/languages` inkludera i webbadressen. Till exempel: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/languages`.

+ Ange ett begäranhuvud så att [åtkomstnyckeln](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) för Text Analytics-åtgärder ska inkluderas.

+ Ange den JSON-dokumentsamling som du har förberett för den här analysen i begärandetexten.

> [!Tip]
> Använd [Postman](text-analytics-how-to-call-api.md) eller öppna **API-testkonsolen** i [dokumentationen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) för att strukturera en begäran och skicka den till tjänsten.

## <a name="step-2-post-the-request"></a>Steg 2: POST begäran

Analysen utförs när begäran har tagits emot. Information om storleken och antalet begäranden som du kan skicka per minut och sekund finns i avsnittet [datagränser](../overview.md#data-limits) i översikten.

Kom ihåg att tjänsten är tillståndslös. Inga data lagras i ditt konto. Resultaten returneras omedelbart i svaret.


## <a name="step-3-view-the-results"></a>Steg 3: Visa resultaten

Alla POST-begäranden returnerar ett JSON-formaterat svar med ID:na och identifierade egenskaper.

Utdata returneras direkt. Du kan strömma resultaten till ett program som accepterar JSON eller spara utdata till en fil i det lokala systemet. Importera sedan utdata till ett program som du kan använda för att sortera, söka efter och ändra data.

Resultat för exempelbegäran bör se ut som följande JSON. Observera att det är ett dokument med flera objekt. Utdata är på engelska. Språkidentifierarna innehåller ett eget namn och en språkkod i [ISO 639-1](https://www.iso.org/standard/22109.html)-format.

Ett positivt resultat på 1.0 uttrycker högsta möjliga förtroendenivå för analysen.

```json
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
        "errors": []
    }
```

### <a name="ambiguous-content"></a>Tvetydig innehåll

I vissa fall kan det vara svårt att disambiguate språk baserat på indata. Du kan `countryHint` använda parametern för att ange en landskod med två bokstäver. Som standard api använder "USA" som standardlandInta, för att ta bort detta beteende kan `countryHint = ""` du återställa den här parametern genom att ställa in det här värdet till tom sträng .

Till exempel är "Omöjligt" gemensamt för både engelska och franska och om det ges med begränsat sammanhang svaret kommer att baseras på "USA" land ledtråd. Om det är fastställt att textens ursprung är franskt, så kan detta ges som ett tips.

**Input**

```json
    {
        "documents": [
            {
                "id": "1",
                "text": "impossible"
            },
            {
                "id": "2",
                "text": "impossible",
                "countryHint": "fr"
            }
        ]
    }
```

Tjänsten har nu ytterligare sammanhang för att göra ett bättre omdöme: 

**Produktionen**

```json
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
                        "name": "French",
                        "iso6391Name": "fr",
                        "score": 1
                    }
                ]
            }
        ],
        "errors": []
    }
```

Om analysatorn inte kan tolka indata `(Unknown)`returneras den . Ett exempel är om du skickar in ett textblock som endast består av arabiska siffror.

```json
    {
        "id": "5",
        "detectedLanguages": [
            {
                "name": "(Unknown)",
                "iso6391Name": "(Unknown)",
                "score": "NaN"
            }
        ]
    }
```

### <a name="mixed-language-content"></a>Innehåll på blandade språk

Innehåll med blandade språk i samma dokument returnerar det språk som har den största representationen i innehållet, men med lägre positiv betyg. Kreditbetyget återspeglar bedömningens marginella styrka. I följande exempel är indata en blandning av engelska, spanska och franska. Det dominerande språket fastställs genom att analysatorn räknar tecken i varje segment.

**Input**

```json
    {
      "documents": [
        {
          "id": "1",
          "text": "Hello, I would like to take a class at your University. ¿Se ofrecen clases en español? Es mi primera lengua y más fácil para escribir. Que diriez-vous des cours en français?"
        }
      ]
    }
```

**Produktionen**

Den resulterande produktionen består av det dominerande språket, med en poäng på mindre än 1,0, vilket indikerar en svagare förtroendenivå.

```json
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

I den här artikeln lärde du dig begrepp och arbetsflöde för språkidentifiering med hjälp av Text Analytics i Azure Cognitive Services. Följande punkter förklarades och demonstrerades:

+ [Språkidentifiering](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) är tillgängligt för ett brett spektrum av språk, varianter, dialekter och vissa regionala eller kulturella språk.
+ JSON-dokument i begäran innehåller ett ID och text.
+ POST-begäran är `/languages` till en slutpunkt med hjälp av en anpassad [åtkomstnyckel och en slutpunkt](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) som gäller för din prenumeration.
+ Svarsutdata består av språkidentifierare för varje dokument-ID. Utdata kan strömmas till alla appar som accepterar JSON. Exempel på appar är Excel och Power BI, för att nämna några.

## <a name="see-also"></a>Se även

 [Översikt över Textanalys](../overview.md) [Vanliga frågor och svar](../text-analytics-resource-faq.md)</br>
 [Produktsida för textanalys](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Analysera känsla](text-analytics-how-to-sentiment-analysis.md)
