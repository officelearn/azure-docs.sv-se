---
title: Start Skapa ett objekt identifierings projekt med SDK för C# -Custom vision
titleSuffix: Azure Cognitive Services
description: Skapa ett projekt, Lägg till taggar, ladda upp bilder, träna ditt projekt och identifiera objekt med hjälp av .NET C#SDK med.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: d56953932a92eb4d9e39c8fe5f0d709a83ba3eaa
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404186"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-net-sdk"></a>Start Skapa ett objekt identifierings projekt med Custom Vision .NET SDK

Den här artikeln visar hur du kommer igång med Custom Vision SDK med C# för att skapa en modell för objekt identifiering. När den har skapats kan du lägga till taggade regioner, ladda upp bilder, träna projektet, Hämta projektets standard-URL för förutsägelse slut punkt och använda slut punkten för att testa en avbildning. Använd det här exemplet som mall för att skapa ditt eget .NET-program. 

## <a name="prerequisites"></a>Förutsättningar

- Alla utgåvor av [Visual Studio 2015 eller 2017](https://www.visualstudio.com/downloads/)
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Hämta Custom Vision SDK och exempel kod

Om du vill skriva en .NET-app som använder Custom Vision behöver du Custom Vision NuGet-paketen. De här paketen ingår i det exempel projekt som du hämtar, men du kan komma åt dem separat här.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Klona eller ladda ned [Cognitive Services .NET-exempelprojektet](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Navigera till **customvision/objectdetection-mappen** och öppna _ObjectDetection.csproj_ i Visual Studio.

Det här projektet i Visual Studio skapar ett nytt Custom Vision-projekt med namnet __Mitt nya projekt__, som kan nås via [Custom Vision-webbplatsen](https://customvision.ai/). Det laddar sedan upp bilder för att träna och testa en objektidentifierarmodell. I det här projektet tränas modellen i att identifiera gafflar och saxar i bilder.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>Förstå koden

Öppna filen _Program.cs_ och granska koden. [Skapa miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för utbildnings- `CUSTOM_VISION_TRAINING_KEY` `CUSTOM_VISION_PREDICTION_KEY`och förutsägelsenycklarna med namnet respektive . Skriptet kommer att leta efter dessa variabler.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_keys)]

Hämta även slut punkts-URL: en från sidan Inställningar på webbplatsen för Custom Vision. Spara den till en miljö variabel som kallas `CUSTOM_VISION_ENDPOINT`. Skriptet sparar en referens till den i roten av klassen.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_endpoint)]

### <a name="create-a-new-custom-vision-service-project"></a>Skapa ett nytt Custom Vision Service-projekt

Nästa bit kod skapar ett projekt för identifiering av objekt. Det skapade projektet visas på den [Custom Vision-webbplats](https://customvision.ai/) som du besökte tidigare. Se metoden [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__) för att ange andra alternativ när du skapar projektet (förklaras i webbportalguiden [För bygg en detektor).](get-started-build-detector.md)  

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_create)]


### <a name="add-tags-to-the-project"></a>Lägg till taggar i projektet

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>Ladda upp och tagga bilder

När du taggar bilder i objekt identifierings projekt måste du ange regionen för varje taggat objekt med normaliserade koordinater. Följande kod associerar var och en av exempel bilderna med dess taggade region.

> [!NOTE]
> Om du inte har ett klick-och-dra-verktyg för att markera koordinaterna för regioner kan du använda webbgränssnittet på [Customvision.ai](https://www.customvision.ai/). I det här exemplet finns koordinaterna redan.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload_regions)]

Den här mappningen av associationer används sedan för att ladda upp varje exempelbild med dess regionkoordinater. Du kan ladda upp upp till 64 bilder i en enda batch.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload)]

Nu har du laddat upp alla exempel bilder och taggat var och en (**förgrening** eller **sax**) med en associerad pixel-rektangel.

### <a name="train-the-project"></a>Träna projektet

Den här koden skapar den första inlärnings iterationen i projektet.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_train)]

### <a name="publish-the-current-iteration"></a>Publicera den aktuella iterationen

Det namn som ges till den publicerade iterationen kan användas för att skicka förutsägelse begär Anden. En iteration är inte tillgänglig i förutsägelse slut punkten förrän den har publicerats.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_publish)]

### <a name="create-a-prediction-endpoint"></a>Skapa en förutsägelse slut punkt

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction_endpoint)]

### <a name="use-the-prediction-endpoint"></a>Använd förutsägelse slut punkten

Den här delen av skriptet läser in test avbildningen, frågar modell slut punkten och matar ut förutsägelse data till-konsolen.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>Köra appen

När programmet körs ska det öppna ett konsol fönster och skriva följande utdata:

```console
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```

Sedan kan du kontrol lera att test bilden (finns i **bilder/test/** ) är korrekt Taggad och att identifierings området är korrekt. I det här läget kan du trycka på valfri tangent för att avsluta programmet.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>Nästa steg

Nu har du sett hur du gör varje steg i objektidentifieringsprocessen i kod. Det här exemplet kör en enda träningsiteration, men ofta måste du träna och testa din modell flera gånger för att göra den mer exakt. Följande guide behandlar bildklassificering, men principerna liknar dem som gäller för objektidentifiering.

> [!div class="nextstepaction"]
> [Testa och träna om en modell](test-your-model.md)
