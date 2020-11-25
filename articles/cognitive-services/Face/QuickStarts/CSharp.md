---
title: 'Snabbstart: Identifiera ansikten i en bild med Azure REST API och C#'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten ska du använda Azure ansikts-REST API med C# för att identifiera ansikten i en bild.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 11/23/2020
ms.author: pafarley
ms.openlocfilehash: f15fbc340aafa325f772a1fd50a2129fa5044c47
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96020882"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-c"></a>Snabbstart: Identifiera ansikten i en bild med hjälp av ansikts-REST API och C#

I den här snabb starten ska du använda Azures ansikts REST API med C# för att identifiera mänskliga ansikten i en bild.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" skapar du en ansikts resurs "  target="_blank"> skapa en ansikts resurs <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Ansikts-API. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.
- Vilken version av [Visual Studio](https://www.visualstudio.com/downloads/)som helst.

## <a name="create-the-visual-studio-project"></a>Skapa Visual Studio-projektet

1. Skapa ett nytt projekt för **Konsolprogram (.NET Framework)** i Visual Studio och ge det namnet **FaceDetection**.
1. Om det finns andra projekt i din lösning väljer du den här kopian som det enda startprojektet.

## <a name="add-face-detection-code"></a>Lägga till kod för ansiktsigenkänning

Öppna det nya projektets *Program.cs*-fil. Här lägger du till den kod som behövs för att läsa in bilder och identifiera ansikten.

### <a name="include-namespaces"></a>Inkludera namnområden

Lägg till följande `using`-instruktioner överst i *Program.cs*-filen.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="dependencies":::

### <a name="add-essential-fields"></a>Lägga till grundläggande fält

Lägg till **program** klassen som innehåller följande fält. Dessa data anger hur du ansluter till ansiktsigenkänningstjänsten och var du hämtar indata. Du måste uppdatera `subscriptionKey` fältet med värdet för din prenumerations nyckel och du kan behöva ändra `uriBase` strängen så att den innehåller din resurs slut punkts sträng.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="environment":::

### <a name="receive-image-input"></a>Ta emot indata

Lägg till följande kod i metoden **Main** i klassen **Program**. Den här koden skriver en prompt till-konsolen som ber användaren att ange sin lokala avbildnings fil Sök väg. Sedan anropas en annan metod, **MakeAnalysisRequest**, för att bearbeta bilden på den platsen.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="main":::

### <a name="call-the-face-detection-rest-api"></a>Anropa REST API för ansiktsigenkänning

Lägg till följande metod i klassen **Program**. Den skapar ett REST-anrop till Ansikts-API för att identifiera ansiktsinformation i fjärrbilden (`requestParameters`-strängen anger vilka ansiktsattribut som ska hämtas). Sedan skriver den utdata till en JSON-sträng.

Du definierar hjälpmetoden i följande steg.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="request":::

### <a name="process-the-input-image-data"></a>Bearbeta inmatade bilddata

Lägg till följande metod i klassen **Program**. Den här metoden konverterar avbildningen på den angivna fil Sök vägen till en byte mat ris.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="getimage":::

### <a name="parse-the-json-response"></a>Tolka JSON-svaret

Lägg till följande metod i klassen **Program**. Den här metoden formaterar JSON-ingången så att den blir lättare att läsa. Appen skriver dessa strängdata till konsolen. Sedan kan du stänga klassen och namn området.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="print":::

## <a name="run-the-app"></a>Kör appen

Ett lyckat svar visar ansiktsinformation i lättläst JSON-format. Exempel:

```json
[
   {
      "faceId": "f7eda569-4603-44b4-8add-cd73c6dec644",
      "faceRectangle": {
         "top": 131,
         "left": 177,
         "width": 162,
         "height": 162
      }
   }
]
```

## <a name="extract-face-attributes"></a>Extrahera ansikts attribut
 
Om du vill extrahera ansikts attribut använder du identifierings modell 1 och lägger till `returnFaceAttributes` Frågeparametern.

```csharp
string requestParameters = "detectionModel=detection_01&returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";
```

Svaret innehåller nu ansikts attribut. Exempel:

```json
[
   {
      "faceId": "f7eda569-4603-44b4-8add-cd73c6dec644",
      "faceRectangle": {
         "top": 131,
         "left": 177,
         "width": 162,
         "height": 162
      },
      "faceAttributes": {
         "smile": 0.0,
         "headPose": {
            "pitch": 0.0,
            "roll": 0.1,
            "yaw": -32.9
         },
         "gender": "female",
         "age": 22.9,
         "facialHair": {
            "moustache": 0.0,
            "beard": 0.0,
            "sideburns": 0.0
         },
         "glasses": "NoGlasses",
         "emotion": {
            "anger": 0.0,
            "contempt": 0.0,
            "disgust": 0.0,
            "fear": 0.0,
            "happiness": 0.0,
            "neutral": 0.986,
            "sadness": 0.009,
            "surprise": 0.005
         },
         "blur": {
            "blurLevel": "low",
            "value": 0.06
         },
         "exposure": {
            "exposureLevel": "goodExposure",
            "value": 0.67
         },
         "noise": {
            "noiseLevel": "low",
            "value": 0.0
         },
         "makeup": {
            "eyeMakeup": true,
            "lipMakeup": true
         },
         "accessories": [

         ],
         "occlusion": {
            "foreheadOccluded": false,
            "eyeOccluded": false,
            "mouthOccluded": false
         },
         "hair": {
            "bald": 0.0,
            "invisible": false,
            "hairColor": [
               {
                  "color": "brown",
                  "confidence": 1.0
               },
               {
                  "color": "black",
                  "confidence": 0.87
               },
               {
                  "color": "other",
                  "confidence": 0.51
               },
               {
                  "color": "blond",
                  "confidence": 0.08
               },
               {
                  "color": "red",
                  "confidence": 0.08
               },
               {
                  "color": "gray",
                  "confidence": 0.02
               }
            ]
         }
      }
   }
]
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du ett enkelt .NET-konsol program som använder REST-anrop med Azure Face service för att identifiera ansikten i en bild och returnera deras attribut. Utforska sedan referensdokumentationen för Ansikts-API för att lära dig mer om de scenarier som stöds.

> [!div class="nextstepaction"]
> [Ansikts-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
