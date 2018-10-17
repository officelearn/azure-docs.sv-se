---
title: 'Exempel: Anropa API:et för visuellt innehåll'
titlesuffix: Azure Cognitive Services
description: Lär dig att anropa API för viuellt innehåll genom att använda REST i Azure Cognitive Services.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: sample
ms.date: 01/20/2017
ms.author: kefre
ms.openlocfilehash: e8297fbe59ebe2dea9caf112ebea4517447cf9e0
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "45981753"
---
# <a name="example-how-to-call-the-computer-vision-api"></a>Exempel: Så här anropar du API:et för visuellt innehåll

Den här guiden visar hur du anropar ett API för visuellt innehåll med hjälp av REST. Exemplen är skrivna i C# med hjälp av klientbiblioteket för API för visuellt innehåll, samt HTTP POST-/GET-anrop. Vi fokuserar på:

-   Hur du hämtar ”taggar”, ”beskrivning” och ”kategorier”.
-   Hur du hämtar ”domänspecifik” information (kändisar).

### <a name="Prerequisites">Förutsättningar</a> 
Bild-URL eller sökvägen till en lokalt lagrad bild.
  * Indatametoder som stöds: Binära rawbildfiler i form av ett program-/oktettflöde eller bild-URL
  * Bildformat som stöds: JPEG, PNG, GIF, BMP
  * Bildfilstorlek: mindre än 4 MB
  * Bilddimension: större än 50 x 50 bildpunkter
  
Följande funktioner demonstreras i nedanstående exempel:

1. Analysera en bild och returnera en uppsättning taggar och en beskrivning.
2. Analysera en bild med en domänspecifik modell (i detta fall modellen ”kändisar”) och returnera motsvarande resultat i JSON-retune.

Funktionerna kan fördelas mellan:

  * **Alternativ ett:** Databasomfattande analys – analysera endast en viss modell
  * **Alternativ två:** utökad analys – analysera för att tillhandahålla ytterligare detaljer med [86 kategoriers taxonomi](../Category-Taxonomy.md)
  
### <a name="Step1">Steg 1: Auktorisera API-anrop</a> 
Varje anrop till ett API för visuellt innehåll kräver en prenumerationsnyckel. Nyckeln måste antingen skickas via en frågesträngparameter eller anges i begärans sidhuvud. 

Du kan skaffa en prenumerationsnyckel genom att följa anvisningarna i [Skaffa prenumerationsnycklar](../Vision-API-How-to-Topics/HowToSubscribe.md
).

**1.** Skicka prenumerationsnyckeln via en frågesträng. Nedan följer ett exempel med API för visuellt innehåll:

```https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>```

**2.** Du kan även ange att prenumerationsnyckeln ska skickas i HTTP-begärans sidhuvud:

```ocp-apim-subscription-key: <Your subscription key>```

**3.** När du använder klientbiblioteket skickas prenumerationsnyckeln i konstruktorn för VisionServiceClient:

```var visionClient = new VisionServiceClient(“Your subscriptionKey”);```

### <a name="Step2">Steg 2: Ladda upp en bild till tjänsten API för visuellt innehåll och få tillbaka taggar, beskrivningar och kändisar</a>
Det grundläggande sättet att anropa et API för visuellt innehåll är genom att ladda upp en bild direkt. Detta sker genom att skicka en ”POST”-begäran med innehållstypen program-/oktettflöde tillsammans med de data som läses från avbildningen. För ”Taggar” och ”Beskrivning” kommer den här överföringsmetoden att vara samma för samtliga anrop till API för visuellt innehåll. Den enda skillnaden är vilka frågeparametrar som användaren anger. 

Så här hämtar du ”Taggar” och ”Beskrivning” för en viss avbildning:

**Alternativ ett:** Hämta lista över ”Taggar” och en ”Beskrivning”
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```
```
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

###### <a name="tags-only"></a>Endast taggar:
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/tag&subscription-key=<Your subscription key>
var analysisResult = await visionClient.GetTagsAsync("http://contoso.com/example.jpg");
```

###### <a name="description-only"></a>Endast beskrivning:
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/describe&subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  analysisResult = await visionClient.DescribeAsync(fs);
}
```
### <a name="here-is-how-to-get-domain-specific-analysis-in-our-case-for-celebrities"></a>Så här hämtar du domänspecifik analysis (i vårt fall för kändisar).

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
**Alternativ två:** utökad analys – analysera för att tillhandahålla ytterligare detaljer med [86 kategoriers taxonomi](../Category-Taxonomy.md)

För program där du vill få allmän bildanalys utöver information från en eller flera domänspecifika modeller utökar vi v1-API:et med modellens frågeparameter.
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?details=celebrities
```
När den här metoden anropas kommer vi först att anropa 86-kategoriklassificeraren. Om någon av kategorierna motsvarar en känd eller motsvarande modell kommer ett andra klassificeraranrop att ske. For example, om "detaljer=alla", or "detaljer" inkluderar ”kändisar” kommer vi att kalla modellen kändisar efter namnet på 86-kategoriklassificeraren och resultatet inkluderar kategoripersonen. Detta ökar svarstiden för användare som är intresserade av kändisar, jämfört med alternativ ett.

Alla v1-frågeparametrar kommer att fungera på samma sätt i det här fallet.  Om visualFeatures = kategorier inte anges kommer det att aktiveras införstått.

### <a name="Step3">Steg 3: Hämta och förstå JSON-utdata för att analyze&visualFeatures = taggar, beskrivning</a>

Här är ett exempel:
```
  {
    “tags”: [
    {
    "name": "outdoor",
        "score": 0.976
    },
    {
    "name": "bird",
        "score": 0.95
    }
            ],
    “description”: 
    {
    "tags": [
    "outdoor",
    "bird"
            ],
    "captions": [
    {
    "text”: “partridge in a pear tree”,
            “confidence”: 0.96
    }
                ]
    }
  }
```
Fält   | Typ  | Innehåll
------|------|------|
Taggar    | objekt    | Huvudobjekt för taggmatris
tags[].Name | sträng    | Nyckelord från taggklassificerare
tags[].Score    | nummer    | Förtroendepoäng, mellan 0 och 1.
beskrivning  | objekt   | Huvudobjekt för beskrivning
description.tags[] |    sträng  | Lista med taggar.  Om det finns inte tillräckligt med förtroende för möjligheten att skapa en etikett, kan taggarna vara den enda informationen tillgänglig för anroparen.
description.captions[].text | sträng    | En mening som beskriver bilden.
description.captions[].confidence   | nummer    | Förtroende för frasen.

### <a name="Step4">Steg 4: Hämta och förstå JSON-utdata för domänspecifika modeller</a>

**Alternativ ett:** Databasomfattande analys – analysera endast en viss modell

Utdatan r en matris med taggar som i följande exempel:
```
  { 
    "result": [ 
      { 
    "name": "golden retriever", 
    "score": 0.98
    },
    { 
    "name": "Labrador retriever", 
    "score": 0.78
    }
              ]
  }
```

**Alternativ två:** utökad analys – analysera för att tillhandahålla ytterligare detaljer med 86 kategoriers taxonomi

För domänspecifika modeller med alternativ två (utökad analys) utökas den returnerade kategoritypen. Ett exempel visas nedan:
```
  {
    "requestId": "87e44580-925a-49c8-b661-d1c54d1b83b5",
    "metadata":     {
      "width": 640,
      "height": 430,
      "format": "Jpeg"
                    },
    "result": {
      "celebrities": 
      [
        {
        "name": "Richard Nixon",
        "faceRectangle": {
          "left": 107,
          "top": 98,
          "width": 165,
          "height": 165
                         },
        "confidence": 0.9999827
        }
      ]
  }
```

Kategorifältet är en lista över en eller flera av de [86 kategorierna](../Category-Taxonomy.md) i den ursprungliga taxonomin. Observera också att kategorier som slutar med ett understreck matchar den kategorin och dess underordnade (till exempel personer_ samt personer_grupp för kändismodellen).

Fält   | Typ  | Innehåll
------|------|------|
kategorier | objekt | Toppnivåobjekt
categories[].name    | sträng   | Namn från 86-kategoritaxonomi
categories[].score  | nummer    | Förtroendepoäng, mellan 0 och 1
categories[].detail  | Objekt?      | Valfritt detaljobjekt

Observera att om flera kategorier matchar (till exmpel 86-kategoriklassificeraren returnerar ett värde för såväl personer_ som personer_unga när modell=kändisar) är informationen kopplad till den mest allmänna nivåmatchningen (personer_ i detta exempel).

### <a name="Errors">Felsvar</a>
Dessa är identiska till vision.analyze med ett ytterligare felet NotSupportedModel (HTTP 400) som kan returneras i såväl alternativ ett- som alternativ två-scenarier. För alternativ två (utökad analys), om någon av de specificerade modellerna inte känns igen kommer API:et returnera NotSupportedModel, även om en eller flera är giltiga.  Användare kan anropa listModels för att ta reda på vilka modeller som stöds.

### <a name="Summary">Sammanfattning</a>

Dessa är de grundläggande funktionerna i API för visuellt innehåll: hur du kan ladda upp bilder och hämta värdefulla metadata i utbyte.

Om du vill använda REST-API, gå till [API-referens för datorvision](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44).
 
