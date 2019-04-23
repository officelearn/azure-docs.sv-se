---
title: 'Exempel: Anropa API för att analysera bild – visuellt innehåll'
titlesuffix: Azure Cognitive Services
description: Lär dig att anropa API för viuellt innehåll genom att använda REST i Azure Cognitive Services.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 03/21/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 0e2767660edf2a9dbcb8617b07a6b9f71fedb743
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60011266"
---
# <a name="example-how-to-call-the-computer-vision-api"></a>Exempel: Så här anropar du API för visuellt innehåll

Den här guiden visar hur du anropar ett API för visuellt innehåll med hjälp av REST. Exemplen är skrivna i C# med hjälp av klientbiblioteket för API för visuellt innehåll, samt HTTP POST-/GET-anrop. Vi fokuserar på:

- Hur du hämtar ”taggar”, ”beskrivning” och ”kategorier”.
- Hur du hämtar ”domänspecifik” information (kändisar).

## <a name="prerequisites"></a>Nödvändiga komponenter

- Bild-URL eller sökvägen till en lokalt lagrad bild.
- Indatametoder som stöds: Binära rawbildfiler i form av ett program-/oktettflöde eller bild-URL
- Bildformat som stöds: JPEG, PNG, GIF, BMP
- Storlek på bildfil: Mindre än 4 MB
- Bilddimensioner: Större än 50 x 50 bildpunkter
  
Följande funktioner demonstreras i nedanstående exempel:

1. Analysera en bild och returnera en uppsättning taggar och en beskrivning.
2. Analysera en bild med en domänspecifik modell (i detta fall modellen ”kändisar”) och returnera motsvarande resultat i JSON-retune.

Funktionerna kan fördelas mellan:

- **Alternativ ett:** Databasomfattande analys – analysera endast en viss modell
- **Alternativ två:** Utökad analys – analysera för att tillhandahålla ytterligare detaljer med [86 kategoriers taxonomi](../Category-Taxonomy.md)
  
## <a name="authorize-the-api-call"></a>Auktorisera API-anropet

Varje anrop till ett API för visuellt innehåll kräver en prenumerationsnyckel. Nyckeln måste antingen skickas via en frågesträngparameter eller anges i begärans sidhuvud.

För att få en kostnadsfri utvärderingsversion nyckel kan se [prova Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Eller följ instruktionerna i [skapa ett Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) att prenumerera på visuellt innehåll och få din nyckel.

1. Skicka prenumerationsnyckeln via en frågesträng. Nedan följer ett exempel med API för visuellt innehåll:

```https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>```

1. Du kan även ange att prenumerationsnyckeln ska skickas i HTTP-begärans sidhuvud:

```ocp-apim-subscription-key: <Your subscription key>```

1. När du använder klientbiblioteket skickas prenumerationsnyckeln i konstruktorn för VisionServiceClient:

```var visionClient = new VisionServiceClient("Your subscriptionKey");```

## <a name="upload-an-image-to-the-computer-vision-api-service-and-get-back-tags-descriptions-and-celebrities"></a>Ladda upp en bild till tjänsten API för visuellt innehåll och få tillbaka taggar, beskrivningar och kändisar

Det grundläggande sättet att anropa et API för visuellt innehåll är genom att ladda upp en bild direkt. Detta sker genom att skicka en ”POST”-begäran med innehållstypen program-/oktettflöde tillsammans med de data som läses från avbildningen. För ”Taggar” och ”Beskrivning” kommer den här överföringsmetoden att vara samma för samtliga anrop till API för visuellt innehåll. Den enda skillnaden är vilka frågeparametrar som användaren anger. 

Så här hämtar du ”Taggar” och ”Beskrivning” för en viss avbildning:

**Alternativ ett:** Hämta lista över ”Taggar” och en ”Beskrivning”

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```

```csharp
using Microsoft.ProjectOxford.Vision;
using Microsoft.ProjectOxford.Vision.Contract;
using System.IO;

AnalysisResult analysisResult;
var features = new VisualFeature[] { VisualFeature.Tags, VisualFeature.Description };

using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  analysisResult = await visionClient.AnalyzeImageAsync(fs, features);
}
```

**Alternativ två:** Hämta endast listan över ”Taggar” eller endast listan med ”Beskrivning”:

###### <a name="tags-only"></a>Taggar:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/tag&subscription-key=<Your subscription key>
var analysisResult = await visionClient.GetTagsAsync("http://contoso.com/example.jpg");
```

###### <a name="description-only"></a>Beskrivning:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/describe&subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  analysisResult = await visionClient.DescribeAsync(fs);
}
```

### <a name="get-domain-specific-analysis-celebrities"></a>Hämta domänspecifika analysis (kändisar)

**Alternativ ett:** Databasomfattande analys – analysera endast en viss modell
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```

För det här alternativet är alla andra frågeparametrar (visualFeatures, information) ogiltiga. Om du vill se alla modeller använder du:

```
GET https://westus.api.cognitive.microsoft.com/vision/v2.0/models 
var models = await visionClient.ListModelsAsync();
```

**Alternativ två:** Utökad analys – analysera för att tillhandahålla ytterligare detaljer med [86 kategoriers taxonomi](../Category-Taxonomy.md)

För program där du vill få allmän bildanalys utöver information från en eller flera domänspecifika modeller utökar vi v1-API:et med modellens frågeparameter.

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?details=celebrities
```

När den här metoden anropas kommer vi först att anropa 86-kategoriklassificeraren. Om någon av kategorierna motsvarar en känd eller motsvarande modell kommer ett andra klassificeraranrop att ske. For example, om "detaljer=alla", or "detaljer" inkluderar ”kändisar” kommer vi att kalla modellen kändisar efter namnet på 86-kategoriklassificeraren och resultatet inkluderar kategoripersonen. Detta ökar svarstiden för användare som är intresserade av kändisar, jämfört med alternativ ett.

Alla v1-frågeparametrar kommer att fungera på samma sätt i det här fallet.  Om visualFeatures = kategorier inte anges kommer det att aktiveras införstått.

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

Fält | Type | Innehåll
------|------|------|
Taggar  | `object` | Huvudobjekt för taggmatris
tags[].Name | `string`  | Nyckelord från taggklassificerare
tags[].Score    | `number`  | Förtroendepoäng, mellan 0 och 1.
description  | `object` | Huvudobjekt för beskrivning
description.tags[] |    `string`    | Lista med taggar.  Om det finns inte tillräckligt med förtroende för möjligheten att skapa en etikett, kan taggarna vara den enda informationen tillgänglig för anroparen.
description.captions[].text | `string`  | En mening som beskriver bilden.
description.captions[].confidence   | `number`  | Förtroende för frasen.

## <a name="retrieve-and-understand-the-json-output-of-domain-specific-models"></a>Hämta och förstå JSON-utdata för domänspecifika modeller

**Alternativ ett:** Databasomfattande analys – analysera endast en viss modell

Utdatan r en matris med taggar som i följande exempel:

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

**Alternativ två:** Utökad analys – analysera för att tillhandahålla ytterligare detaljer med 86 kategoriers taxonomi

För domänspecifika modeller med alternativ två (utökad analys) utökas den returnerade kategoritypen. Ett exempel visas nedan:

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

Kategorifältet är en lista över en eller flera av de [86 kategorierna](../Category-Taxonomy.md) i den ursprungliga taxonomin. Observera också att kategorier som slutar med ett understreck matchar den kategorin och dess underordnade (till exempel personer_ samt personer_grupp för kändismodellen).

Fält   | Type  | Innehåll
------|------|------|
kategorier | `object`   | Toppnivåobjekt
categories[].name    | `string` | Namn från 86-kategoritaxonomi
categories[].score  | `number`  | Förtroendepoäng, mellan 0 och 1
categories[].detail  | `object?`      | Valfritt detaljobjekt

Observera att om flera kategorier matchar (till exmpel 86-kategoriklassificeraren returnerar ett värde för såväl personer_ som personer_unga när modell=kändisar) är informationen kopplad till den mest allmänna nivåmatchningen (personer_ i detta exempel).

## <a name="errors-responses"></a>Fel svar

Dessa är identiska till vision.analyze med ett ytterligare felet NotSupportedModel (HTTP 400) som kan returneras i såväl alternativ ett- som alternativ två-scenarier. För alternativ två (utökad analys), om någon av de specificerade modellerna inte känns igen kommer API:et returnera NotSupportedModel, även om en eller flera är giltiga.  Användare kan anropa listModels för att ta reda på vilka modeller som stöds.

## <a name="next-steps"></a>Nästa steg

Om du vill använda REST-API, gå till [API-referens för datorvision](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44).
