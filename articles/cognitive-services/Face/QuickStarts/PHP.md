---
title: 'Snabbstart: Identifiera ansikten i en bild med hjälp av REST API och PHP'
titleSuffix: Azure Cognitive Services
description: I den här snabb starten identifierar du ansikten från en bild med hjälp av ansikts REST API med PHP.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 11/23/2020
ms.author: pafarley
ms.openlocfilehash: 649c456309d2524fec8d44f13c0f3081725c4361
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95994100"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-php"></a>Snabbstart: Identifiera ansikten i en bild med hjälp av REST API och PHP

I den här snabb starten ska du använda Azures ansikts REST API med PHP för att identifiera mänskliga ansikten i en bild.

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" skapar du en ansikts resurs "  target="_blank"> skapa en ansikts resurs <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Ansikts-API. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.
* En kod redigerare som [Visual Studio Code](https://code.visualstudio.com/download).
* PHP [HTTP_Request2](https://pear.php.net/package/HTTP_Request2) -paketet.
* En PHP-aktiverad webbläsare. Om du inte har konfigurerat detta kan du göra det genom att installera och konfigurera [XAMPP](https://www.apachefriends.org/) på datorn.

## <a name="initialize-the-html-file"></a>Initiera HTML-filen

Skapa en ny HTML-fil, *detectFaces.html*, och lägg till följande kod.

```html
<html>
    <head>
        <title>Face Detect Sample</title>
    </head>
    <body></body>
</html>
```

## <a name="write-the-php-script"></a>Skriva PHP-skriptet

Lägg till följande kod i elementet `body` i dokumentet. Den här koden konfigurerar ett grundläggande användar gränssnitt med ett URL-fält, en **analys ansikte** -knapp, ett svars fönster och ett fönster för bild visning.

:::code language="php" source="~/cognitive-services-quickstart-code/php/face/rest/detect.php":::

Du måste uppdatera `subscriptionKey` fältet med värdet för din prenumerations nyckel och du måste ändra `uriBase` strängen så att den innehåller rätt slut punkts sträng. Fältet `returnFaceAttributes` anger vilka ansiktsattribut som ska hämtas. Du kan behöva ändra den här strängen beroende på hur din användning kommer att bli.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="run-the-script"></a>Kör skriptet

Öppna filen i en PHP-aktiverad webbläsare. Du bör få en JSON-sträng med ansiktsdata på följande sätt.

```json
[
    {
        "faceId": "e93e0db1-036e-4819-b5b6-4f39e0f73509",
        "faceRectangle": {
            "top": 621,
            "left": 616,
            "width": 195,
            "height": 195
        }
    }
]
```

## <a name="extract-face-attributes"></a>Extrahera ansikts attribut
 
Om du vill extrahera ansikts attribut använder du identifierings modell 1 och lägger till `returnFaceAttributes` Frågeparametern.

```php
$parameters = array(
    // Request parameters
    'detectionModel' => 'detection_01',
    'returnFaceAttributes' => 'age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise',
   'returnFaceId' => 'true');
```

Svaret innehåller nu ansikts attribut. Exempel:

```json
[
    {
        "faceId": "e93e0db1-036e-4819-b5b6-4f39e0f73509",
        "faceRectangle": {
            "top": 621,
            "left": 616,
            "width": 195,
            "height": 195
        },
        "faceAttributes": {
            "smile": 0,
            "headPose": {
                "pitch": 0,
                "roll": 6.8,
                "yaw": 3.7
            },
            "gender": "male",
            "age": 37,
            "facialHair": {
                "moustache": 0.4,
                "beard": 0.4,
                "sideburns": 0.1
            },
            "glasses": "NoGlasses",
            "emotion": {
                "anger": 0,
                "contempt": 0,
                "disgust": 0,
                "fear": 0,
                "happiness": 0,
                "neutral": 0.999,
                "sadness": 0.001,
                "surprise": 0
            },
            "blur": {
                "blurLevel": "high",
                "value": 0.89
            },
            "exposure": {
                "exposureLevel": "goodExposure",
                "value": 0.51
            },
            "noise": {
                "noiseLevel": "medium",
                "value": 0.59
            },
            "makeup": {
                "eyeMakeup": true,
                "lipMakeup": false
            },
            "accessories": [],
            "occlusion": {
                "foreheadOccluded": false,
                "eyeOccluded": false,
                "mouthOccluded": false
            },
            "hair": {
                "bald": 0.04,
                "invisible": false,
                "hairColor": [
                    {
                        "color": "black",
                        "confidence": 0.98
                    },
                    {
                        "color": "brown",
                        "confidence": 0.87
                    },
                    {
                        "color": "gray",
                        "confidence": 0.85
                    },
                    {
                        "color": "other",
                        "confidence": 0.25
                    },
                    {
                        "color": "blond",
                        "confidence": 0.07
                    },
                    {
                        "color": "red",
                        "confidence": 0.02
                    }
                ]
            }
        }
    },
    {
        "faceId": "37c7c4bc-fda3-4d8d-94e8-b85b8deaf878",
        "faceRectangle": {
            "top": 693,
            "left": 1503,
            "width": 180,
            "height": 180
        },
        "faceAttributes": {
            "smile": 0.003,
            "headPose": {
                "pitch": 0,
                "roll": 2,
                "yaw": -2.2
            },
            "gender": "female",
            "age": 56,
            "facialHair": {
                "moustache": 0,
                "beard": 0,
                "sideburns": 0
            },
            "glasses": "NoGlasses",
            "emotion": {
                "anger": 0,
                "contempt": 0.001,
                "disgust": 0,
                "fear": 0,
                "happiness": 0.003,
                "neutral": 0.984,
                "sadness": 0.011,
                "surprise": 0
            },
            "blur": {
                "blurLevel": "high",
                "value": 0.83
            },
            "exposure": {
                "exposureLevel": "goodExposure",
                "value": 0.41
            },
            "noise": {
                "noiseLevel": "high",
                "value": 0.76
            },
            "makeup": {
                "eyeMakeup": false,
                "lipMakeup": false
            },
            "accessories": [],
            "occlusion": {
                "foreheadOccluded": false,
                "eyeOccluded": false,
                "mouthOccluded": false
            },
            "hair": {
                "bald": 0.06,
                "invisible": false,
                "hairColor": [
                    {
                        "color": "black",
                        "confidence": 0.99
                    },
                    {
                        "color": "gray",
                        "confidence": 0.89
                    },
                    {
                        "color": "other",
                        "confidence": 0.64
                    },
                    {
                        "color": "brown",
                        "confidence": 0.34
                    },
                    {
                        "color": "blond",
                        "confidence": 0.07
                    },
                    {
                        "color": "red",
                        "confidence": 0.03
                    }
                ]
            }
        }
    }
]
```

## <a name="next-steps"></a>Nästa steg

Utforska Ansikts-API som används för att identifiera mänskliga ansikten i en bild, avgränsa ansikten med rektanglar och returnera attribut som ålder och kön.

> [!div class="nextstepaction"]
> [Ansikts-API:er](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
 