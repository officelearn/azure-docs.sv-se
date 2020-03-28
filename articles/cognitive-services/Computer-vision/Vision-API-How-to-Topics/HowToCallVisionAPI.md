---
title: Anropa API:et för visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Lär dig hur du anropar API:et för datorseende med hjälp av REST API i Azure Cognitive Services.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 298228eedb73298f00654f4f72c201d9ed671090
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72177049"
---
# <a name="call-the-computer-vision-api"></a>Anropa API:et för visuellt innehåll

Den här artikeln visar hur du anropar API:et för datorseende med hjälp av REST API.This article demonstrates how to call the Computer Vision API by using the REST API. Exemplen skrivs både i C# med hjälp av computer vision API-klientbiblioteket och som HTTP POST- eller GET-anrop. Artikeln fokuserar på:

- Hämta taggar, en beskrivning och kategorier
- Hämta domänspecifik information, eller "kändisar"

## <a name="prerequisites"></a>Krav

- En bild-URL eller en sökväg till en lokalt lagrad bild
- Inmatningsmetoder som stöds: en binär rå bild i form av en application/octet-stream eller en bild-URL
- Bildfilformat som stöds: JPEG, PNG, GIF och BMP
- Bildfilstorlek: 4 MB eller mindre
- Bilddimensioner: 50 &times; 50 pixlar eller mer
  
Exemplen i den här artikeln visar följande funktioner:

* Analysera en bild för att returnera en matris med taggar och en beskrivning
* Analysera en bild med en domänspecifik modell (särskilt "kändisar"-modellen) för att returnera motsvarande resultat i JSON

Funktionerna erbjuder följande alternativ:

- **Alternativ 1:** Scoped Analysis - Analysera endast en angiven modell
- **Alternativ 2:** Förbättrad analys - Analysera för att ge ytterligare information med hjälp av [86 kategorier taxonomi](../Category-Taxonomy.md)
  
## <a name="authorize-the-api-call"></a>Auktorisera API-anropet

Varje anrop till ett API för visuellt innehåll kräver en prenumerationsnyckel. Den här nyckeln måste antingen skickas genom en frågesträngparameter eller anges i begäranden.

Gör något av följande om du vill ha en kostnadsfri utvärderingsversion:
* Gå till sidan [Prova kognitiva tjänster.](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision) 
* Gå till sidan [Skapa ett cognitive services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) för att prenumerera på Datorseende.

Du kan skicka prenumerationsnyckeln genom att göra något av följande:

* Skicka den genom en frågesträng, som i det här API-exemplet för datorseende:

  ```
  https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
  ```

* Ange det i HTTP-begäranden:

  ```
  ocp-apim-subscription-key: <Your subscription key>
  ```

* När du använder klientbiblioteket skickar du nyckeln genom konstruktorn för ComputerVisionClient och anger regionen i en egenskap för klienten:

    ```
    var visionClient = new ComputerVisionClient(new ApiKeyServiceClientCredentials("Your subscriptionKey"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com"
    }
    ```

## <a name="upload-an-image-to-the-computer-vision-api-service"></a>Ladda upp en bild till API-tjänsten Computer Vision

Det grundläggande sättet att utföra API-anropet för visuellt innehåll är att ladda upp en bild direkt för att returnera taggar, en beskrivning och kändisar. Du gör detta genom att skicka en "POST"-begäran med den binära bilden i HTTP-brödtexten tillsammans med data som läs av bilden. Uppladdningsmetoden är densamma för alla API-anrop för datorseende. Den enda skillnaden är de frågeparametrar som du anger. 

För en angiven bild får du taggar och en beskrivning med något av följande alternativ:

### <a name="option-1-get-a-list-of-tags-and-a-description"></a>Alternativ 1: Få en lista med taggar och en beskrivning

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```

```csharp
using System.IO;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

ImageAnalysis imageAnalysis;
var features = new VisualFeatureTypes[] { VisualFeatureTypes.Tags, VisualFeatureTypes.Description };

using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageAnalysis = await visionClient.AnalyzeImageInStreamAsync(fs, features);
}
```

### <a name="option-2-get-a-list-of-tags-only-or-a-description-only"></a>Alternativ 2: Få en lista med taggar eller endast en beskrivning

Endast för taggar kör du:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/tag?subscription-key=<Your subscription key>
var tagResults = await visionClient.TagImageAsync("http://contoso.com/example.jpg");
```

Kör endast en beskrivning:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/describe?subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageDescription = await visionClient.DescribeImageInStreamAsync(fs);
}
```

## <a name="get-domain-specific-analysis-celebrities"></a>Få domänspecifik analys (kändisar)

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>Alternativ 1: Begränsad analys - Analysera endast en angiven modell
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```

För det här alternativet är alla andra frågeparametrar (visualFeatures, information) ogiltiga. Om du vill se alla modeller använder du:

```
GET https://westus.api.cognitive.microsoft.com/vision/v2.1/models 
var models = await visionClient.ListModelsAsync();
```

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-86-categories-taxonomy"></a>Alternativ 2: Förbättrad analys - Analysera för att ge ytterligare information med hjälp av 86 kategorier taxonomi

För program där du vill få en allmän bildanalys utöver information från en eller flera domänspecifika modeller utökar du v1 API:et med hjälp av parametern models query.

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?details=celebrities
```

När du anropar den här metoden anropar du först klassificeraren i [86-kategori.](../Category-Taxonomy.md) Om någon av kategorierna matchar en känd eller matchande modell sker ett andra pass av klassificeraranrop. Om till exempel "details=all" eller "details" innehåller "kändisar" ringer du kändisarmodellen när du har ringt klassificeraren i 86 kategorier. I resultatet ingår kategoripersonen. I motsats till alternativ 1 ökar den här metoden latensen för användare som är intresserade av kändisar.

I det här fallet fungerar alla v1-frågeparametrar på samma sätt. Om du inte anger visualFeatures=categories är det implicit aktiverat.

## <a name="retrieve-and-understand-the-json-output-for-analysis"></a>Hämta och förstå JSON-utdata för analys

Här är ett exempel:

```json
{  
  "tags":[  
    {  
      "name":"outdoor",
      "score":0.976
    },
    {  
      "name":"bird",
      "score":0.95
    }
  ],
  "description":{  
    "tags":[  
      "outdoor",
      "bird"
    ],
    "captions":[  
      {  
        "text":"partridge in a pear tree",
        "confidence":0.96
      }
    ]
  }
}
```

Field | Typ | Innehåll
------|------|------|
Taggar  | `object` | Objektet på den översta nivån för en matris med taggar.
tags[].Name | `string`  | Nyckelordet från taggarklassificeraren.
tags[].Score    | `number`  | Självförtroendet, 0–1.
description  | `object` | Objektet på den översta nivån för en beskrivning.
description.tags[] |    `string`    | Listan över taggar.  Om det inte finns tillräckligt förtroende för möjligheten att skapa en bildtext kan taggarna vara den enda information som är tillgänglig för den som ringer.
description.captions[].text | `string`  | En mening som beskriver bilden.
description.captions[].confidence   | `number`  | Konfidenspoängen för frasen.

## <a name="retrieve-and-understand-the-json-output-of-domain-specific-models"></a>Hämta och förstå JSON-utdata för domänspecifika modeller

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>Alternativ 1: Begränsad analys - Analysera endast en angiven modell

Utdata är en matris med taggar, som visas i följande exempel:

```json
{  
  "result":[  
    {  
      "name":"golden retriever",
      "score":0.98
    },
    {  
      "name":"Labrador retriever",
      "score":0.78
    }
  ]
}
```

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-the-86-categories-taxonomy"></a>Alternativ 2: Förbättrad analys - Analysera för att ge ytterligare information med hjälp av taxonomi i "86 kategorier"

För domänspecifika modeller med alternativ 2 (förbättrad analys) utökas kategoriernas returtyp, vilket visas i följande exempel:

```json
{  
  "requestId":"87e44580-925a-49c8-b661-d1c54d1b83b5",
  "metadata":{  
    "width":640,
    "height":430,
    "format":"Jpeg"
  },
  "result":{  
    "celebrities":[  
      {  
        "name":"Richard Nixon",
        "faceRectangle":{  
          "left":107,
          "top":98,
          "width":165,
          "height":165
        },
        "confidence":0.9999827
      }
    ]
  }
}
```

Kategorifältet är en lista över en eller flera av de [86 kategorierna](../Category-Taxonomy.md) i den ursprungliga taxonomin. Kategorier som slutar i ett understreck matchar den kategorin och dess underordnade (till exempel "people_" eller "people_group" för kändisarmodellen).

Field   | Typ  | Innehåll
------|------|------|
kategorier | `object`   | Objektet på den översta nivån.
categories[].name    | `string` | Namnet från taxonomilistan med 86 kategorier.
categories[].score  | `number`  | Självförtroendet, 0–1.
categories[].detail  | `object?`      | (Valfritt) Detaljobjektet.

Om flera kategorier matchar (till exempel 86-kategoriklassificeraren returnerar en poäng för både "people_" och "people_young", när modell=kändisar) kopplas informationen till den mest allmänna nivåmatchningen ("people_", i det exemplet).

## <a name="error-responses"></a>Felsvar

Dessa fel är identiska med dem i vision.analyze, med ytterligare inte stödsmodel fel (HTTP 400), som kan returneras i både alternativ 1 och alternativ 2 scenarier. För alternativ 2 (förbättrad analys), om någon av de modeller som anges i informationen inte känns igen, returnerar API:et en NotSupportedModel, även om en eller flera av dem är giltiga. Om du vill ta reda på vilka modeller som stöds kan du anropa listModels.

## <a name="next-steps"></a>Nästa steg

Om du vill använda REST-API, gå till [API-referens för datorvision](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44).
