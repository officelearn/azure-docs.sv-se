---
title: Anropa API för visuellt | Microsoft Docs
description: Lär dig hur du anropar den API för visuellt innehåll med hjälp av REST i Cognitive Services.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 01/20/2017
ms.author: kefre
ms.openlocfilehash: 34705681e665b57a89c43f31ca695e0acb45ae3f
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35760278"
---
# <a name="how-to-call-the-computer-vision-api"></a>Hur du anropar den API för visuellt innehåll

Den här guiden visar hur du anropar API för visuellt innehåll med hjälp av REST. Exemplen är skrivna i C# med hjälp av klientbiblioteket för API för visuellt innehåll, såväl som HTTP POST/GET-anrop. Vi fokuserar på:

-   Hur du hämtar ”taggar”, ”beskrivning” och ”kategorier”.
-   Så här att hämta information om ”domänspecifika” (kändisar).

### <a name="Prerequisites">Förutsättningar</a> 
Bild-URL eller sökvägen till lokalt lagrad bild.
  * Stöds inmatningsmetoder: Raw binära i form av ett program/oktett stream eller bild-URL-bild
  * Bildformat som stöds: JPEG, PNG, GIF, BMP
  * Bild filstorlek: mindre än 4MB
  * Bild dimension: är större än 50 x 50 bildpunkter
  
I exemplen nedan visas följande funktioner:

1. Analysera en bild och få en matris med taggar och en beskrivning som returneras.
2. Analysera en bild med en domänspecifika-modell (mer specifikt ”kändisar”-modellen) och hämta motsvarande resultera i JSON-retune.

Funktioner är uppdelade på:

  * **Alternativ ett:** Databasomfattande analys - analysera bara en viss modell
  * **Alternativ två:** utökad analys - analysera för att tillhandahålla ytterligare detaljer med [86 kategorier taxonomi](../Category-Taxonomy.md)
  
### <a name="Step1">Steg 1: Auktorisera API-anrop</a> 
Varje anrop till API för visuellt innehåll krävs en prenumerationsnyckel. Nyckeln måste antingen skickas via en frågesträngparameter eller anges i förfrågans sidhuvud. 

För att skaffa en prenumerationsnyckel kan se [så hämta Prenumerationsnycklar](../Vision-API-How-to-Topics/HowToSubscribe.md
).

**1.** Skicka prenumerationsnyckeln via en frågesträng nedan följer ett exempel med API för visuellt innehåll:

```https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>```

**2.** Skicka prenumerationsnyckeln kan också anges i HTTP-frågehuvudet:

```ocp-apim-subscription-key: <Your subscription key>```

**3.** När du använder klientbiblioteket skickas prenumerationsnyckeln i konstruktorn för VisionServiceClient:

```var visionClient = new VisionServiceClient(“Your subscriptionKey”);```

### <a name="Step2">Steg 2: Ladda upp en bild till tjänsten API för visuellt innehåll och få tillbaka taggar, beskrivningar och kändisar</a>
Det grundläggande sättet att utföra API för visuellt innehåll anropet är genom att ladda upp en bild direkt. Detta görs genom att skicka en ”POST”-begäran med application/octet-ström innehållstyp tillsammans med de data som läses från avbildningen. ”Taggar” och ”beskrivning” är den här metoden upload för samma för alla API för visuellt innehåll-anrop. Den enda skillnaden är frågeparametrar som användaren anger. 

Här är hur du hämtar ”taggar” och ”beskrivning” för en viss avbildning:

**Alternativ ett:** Hämta lista över ”taggar” och en ”beskrivning”
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
**Alternativ två** hämta listan över ”taggar” endast, eller endast lista över ”beskrivning”:

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
### <a name="here-is-how-to-get-domain-specific-analysis-in-our-case-for-celebrities"></a>Här är hur du hämtar domänspecifika analysis (i vårt fall för kändisar).

**Alternativ ett:** Databasomfattande analys - analysera bara en viss modell
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```
Alla andra frågeparametrar {visualFeatures, information om} är inte giltiga för det här alternativet. Om du vill se alla modeller Använd: 
```
GET https://westus.api.cognitive.microsoft.com/vision/v2.0/models 
var models = await visionClient.ListModelsAsync();
```
**Alternativ två:** utökad analys - analysera för att tillhandahålla ytterligare detaljer med [86 kategorier taxonomi](../Category-Taxonomy.md)

För program där du vill få allmän bildanalys utöver information från en eller flera domänspecifika modeller, utöka vi v1-API med Frågeparametern modeller.
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.0/analyze?details=celebrities
```
När den här metoden har anropats kommer vi kallar 86-kategori-klassificerare först. Om någon av kategorierna matchar som en känd/matchar modell, sker en igenom av klassificerare anrop. Till exempel om ”information = all”, eller ”information” inkludera ”kändisar', vi kan kalla kändisar modellen när 86-kategori-klassificerare kallas och resultatet inkluderar personen som kategori. Detta ökar svarstiden för användare som är intresserade av kändisar, jämfört med en alternativ.

Alla v1-frågeparametrar kommer fungerar på samma sätt i det här fallet.  Om visualFeatures = kategorier inte anges den kommer att aktiveras implicit.

### <a name="Step3">Steg 3: Hämta och förstå JSON-utdata för att analysera och visualFeatures = taggar, beskrivning</a>

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
[] med taggar. Namn | sträng    | Nyckelord från taggar klassificerare
[] med taggar. Poäng    | nummer    | Förtroendepoäng, mellan 0 och 1.
beskrivning  | objekt   | Översta objekt för en beskrivning.
Description.Tags] |    sträng  | Lista med taggar.  Om det finns inte tillräckligt med förtroende för möjligheten att skapa en etikett, taggarna kan vara den enda informationen tillgänglig för anroparen.
Description.captions[].text | sträng    | En mening som beskriver bilden.
Description.captions[].Confidence   | nummer    | Förtroende för frasen.

### <a name="Step4">Steg 4: Hämta och förstå JSON-utdata för domänspecifika modeller</a>

**Alternativ ett:** Databasomfattande analys - analysera bara en viss modell

Följande utdata returneras en matris med taggar blir ett exempel som i följande exempel:
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

**Alternativ två:** utökad analys - analysera för att ge ytterligare information 86 kategorier taxonomi

För domänspecifika modeller med hjälp av alternativet två (förbättrad analys), returnerar kategorier typen utökas. Ett exempel visas nedan:
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

Kategorifältet är en lista över en eller flera av de [86 kategorier](../Category-Taxonomy.md) i den ursprungliga taxonomin. Observera också att kategorier som slutar med ett understreck matchar den kategorin och dess underordnade (till exempel people_ samt people_group för kändisar modell).

Fält   | Typ  | Innehåll
------|------|------|
kategorier | objekt | Översta objektet
kategorier [] .name    | sträng   | Namnge från 86 kategoritaxonomi
kategorier [] .score  | nummer    | Förtroenderesultat mellan 0 och 1
kategorier [] .detail  | objektet?      | Valfritt begärandemål

Observera att om matchar flera kategorier (till exempel 86-kategori klassificerare returnerar ett värde för både people_ och people_young när modeller = kändisar), information är kopplade till de allmänna nivå matchning (people_ i det här exemplet).

### <a name="Errors">Fel svar</a>
Det här är identiska med vision.analyze, med ytterligare fel felets NotSupportedModel (HTTP 400), som kan returneras i både alternativet en och alternativet två scenarier. För två alternativ (förbättrad analys), om någon av modeller som angetts i informationen om inte känns igen, returnerar API: et NotSupportedModel, även om en eller flera av dem är giltiga.  Användare kan anropa listModels att ta reda på vilka modeller som stöds.

### <a name="Summary">Sammanfattning</a>

Dessa är de grundläggande funktionerna i API för visuellt innehåll: hur du kan ladda upp bilder och hämta värdefulla metadata i utbyte.

Om du vill använda REST-API, gå till [API-referens för datorn Vision](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44).
 
