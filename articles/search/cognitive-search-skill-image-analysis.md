---
title: Bild analys kognitiva Sök kunskaper (Azure Search) | Microsoft Docs
description: Extrahera semantiska text i avbildningen analys med ImageAnalysis kognitiva kunskaper i en Azure Search berikande pipeline.
services: search
manager: pablocas
author: luiscabrer
documentationcenter: ''
ms.assetid: ''
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: f4b0758ed6f1a0d11aeb1061cac34f1e9084ef53
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
#   <a name="image-analysis-cognitive-skill"></a>Bild analys kognitiva kunskaper

Den **avbildningen Analysis** kunskaper extraherar en omfattande uppsättning visuella funktioner baserat på innehållet i operativsystemsavbildningen. Till exempel generera en rubrik från en avbildning, generera taggar eller identifiera Kändisars och landmärken.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Vision.ImageAnalysisSkill 

## <a name="skill-parameters"></a>Kunskaper parametrar

Parametrar är skiftlägeskänsliga.

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| defaultLanguageCode   |  En sträng som anger språket som ska returneras. Tjänsten returnerar resultat i ett angivet språk. Om den här parametern inte anges är standardvärdet ”SV”. <br/><br/>Språk som stöds är: <br/>*en* -engelska (standard) <br/> *zh* -förenklad kinesiska|
|visualFeatures |   En matris med strängar som anger vilka visuell funktion som ska returneras. Giltigt visual funktionen typer är:  <ul><li> *kategorier* -kategoriserar innehållet i operativsystemsavbildningen enligt en taxonomi som definierats i tjänsterna kognitiva [dokumentationen](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy).</li><li> *taggar* -taggar avbildningen med en detaljerad lista över ord som rör innehållet i operativsystemsavbildningen.</li><li>*Beskrivning* -beskriver innehåll med en fullständig engelska mening bilden.</li><li>*Ytor* -identifierar om ytor finns. Om den finns, genererar koordinater, kön och ålder.</li><li> *ImageType* -identifierar om bilden är clipart eller en rad ritning.</li><li>   *Färg* -avgör accent färg, företag färg, och om en avbildning är svart & vit.</li><li>*Vuxna* -identifierar om bilden pornografi karaktär (visar nakenhet eller en kön act). Webbplatser något innehåll identifieras också.</li></ul> Namnen på visuella funktioner är skiftlägeskänsliga.|
| detaljer   | En matris med strängar som anger vilka domänspecifik information för att returnera. Giltigt visual funktionen typer är: <ul><li>*Kändisars* -identifierar Kändisars om upptäcktes i avbildningen.</li><li>*Landmärken* -identifierar landmärken om upptäcktes i avbildningen.</li></ul>
 |

## <a name="skill-inputs"></a>Kunskaper indata

| Indata     | Beskrivning |
|--------------------|-------------|
| url | Unik positionerare för bilden. Det kan vara en URL eller platsen för blob-lagring.|



##  <a name="sample-definition"></a>Exempel definition

```json
{
    "@odata.type": "#Microsoft.Skills.Vision.ImageAnalysisSkill",
    "visualFeatures": [
        "Tags",
        "Faces",
        "Categories",
        "Adult",
        "Description",
        "ImageType",
        "Color"
    ],
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "url",
            "source": "/document/metadata_storage_path"
        }
    ],
    "outputs": [
        {
            "name": "categories",
            "targetName": "myCategories"
        },
        {
            "name": "tags",
            "targetName": "myTags"
        },
        {
            "name": "description",
            "targetName": "myDescription"
        },
        {
            "name": "faces",
            "targetName": "myFaces"
        },
        {
            "name": "imageType",
            "targetName": "myImageType"
        },
        {
            "name": "color",
            "targetName": "myColor"
        },
        {
            "name": "adult",
            "targetName": "myAdultCategory"
        }
    ]
}
```

##  <a name="sample-input"></a>Exempelindata

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "url": "https://storagesample.blob.core.windows.net/sample-container/image.jpg"
            }
        }
    ]
}
```


##  <a name="sample-output"></a>Exempel på utdata

```json
{
    "values": [
      {
        "recordId": "1",
            "data": {
                "categories": [
           {
                        "name": "abstract_",
                        "score": 0.00390625
                    },
                    {
                "name": "people_",
                        "score": 0.83984375,
                "detail": {
                            "celebrities": [
                                {
                                    "name": "Satya Nadella",
                                    "faceRectangle": {
                                        "left": 597,
                                        "top": 162,
                                        "width": 248,
                                        "height": 248
                                    },
                                    "confidence": 0.999028444
                                }
                            ],
                            "landmarks": [
                                {
                                    "name": "Forbidden City",
                                    "confidence": 0.9978346
                                }
                            ]
                        }
                    }
                ],
                "adult": {
                    "isAdultContent": false,
                    "isRacyContent": false,
                    "adultScore": 0.0934349000453949,
                    "racyScore": 0.068613491952419281
                },
                "tags": [
                    {
                        "name": "person",
                        "confidence": 0.98979085683822632
                    },
                    {
                        "name": "man",
                        "confidence": 0.94493889808654785
                    },
                    {
                        "name": "outdoor",
                        "confidence": 0.938492476940155
                    },
                    {
                        "name": "window",
                        "confidence": 0.89513939619064331
                    }
                ],
                "description": {
                    "tags": [
                        "person",
                        "man",
                        "outdoor",
                        "window",
                        "glasses"
                    ],
                    "captions": [
                        {
                            "text": "Satya Nadella sitting on a bench",
                            "confidence": 0.48293603002174407
                        }
                    ]
                },
                "requestId": "0dbec5ad-a3d3-4f7e-96b4-dfd57efe967d",
                "metadata": {
                    "width": 1500,
                    "height": 1000,
                    "format": "Jpeg"
                },
                "faces": [
                    {
                        "age": 44,
                        "gender": "Male",
                    "faceRectangle": {
                            "left": 593,
                            "top": 160,
                            "width": 250,
                            "height": 250
                        }
                    }
                ],
                "color": {
                    "dominantColorForeground": "Brown",
                    "dominantColorBackground": "Brown",
                    "dominantColors": [
                        "Brown",
                        "Black"
                    ],
                    "accentColor": "873B59",
                    "isBWImg": false
                    },
                "imageType": {
                    "clipArtType": 0,
                    "lineDrawingType": 0
                }
           }
      }
    ]
}
```


## <a name="error-cases"></a>Fel
I följande fall fel extraheras inga element.

| Felkod | Beskrivning |
|------------|-------------|
| NotSupportedLanguage | Det angivna språket stöds inte. |
| InvalidImageUrl | Bild-URL är felaktigt formaterat eller är inte tillgänglig.|
| InvalidImageFormat | Indata är inte en giltig bild. |
| InvalidImageSize | Inkommande bilden är för stor. |
| NotSupportedVisualFeature  | Angiven funktion är inte giltig. |
| NotSupportedImage | Bilden stöds inte, till exempel barnpornografi. |
| InvalidDetails | Domänspecifika-modell som inte stöds. |

## <a name="see-also"></a>Se också

+ [Fördefinierade kunskaper](cognitive-search-predefined-skills.md)
+ [Hur du definierar en kunskaper](cognitive-search-defining-skillset.md)