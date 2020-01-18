---
title: 'Snabb start: skapa ett projekt för objekt identifiering med SDK C# för-Custom vision'
titleSuffix: Azure Cognitive Services
description: Skapa ett projekt, lägg till taggar, ladda upp bilder, träna ditt projekt och identifiera objekt med .NET SDK med C#.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: areddish
ms.openlocfilehash: c6aaf69ba3ed682a00a203079b024a47121334e3
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2020
ms.locfileid: "76170071"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-net-sdk"></a>Snabbstart: Skapa ett objektidentifieringsprojekt med Custom Vision. NET SDK

Den här artikeln visar hur du kommer igång med Custom Vision SDK med C# för att skapa en modell för objekt identifiering. När den har skapats kan du lägga till taggade regioner, ladda upp bilder, träna projektet, hämta slutpunkts-URL:en för projektets standardförutsägelse och använda slutpunkten för att testa en bild programmatiskt. Använd det här exemplet som en mall för att skapa en egen .NET-app. 

## <a name="prerequisites"></a>Krav

- Valfri version av [Visual Studio 2015 eller 2017](https://www.visualstudio.com/downloads/)
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Hämta Custom Vision-SDK:n och exempelkoden

Om du vill skriva ett .NET-program som använder Custom Vision behöver du Custom Vision NuGet-paket. De här paketen ingår i det exempel projekt som du hämtar, men du kan komma åt dem separat här.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Klona eller ladda ned [Cognitive Services .NET-exempelprojektet](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Gå till mappen **CustomVision/ObjectDetection** och öppna _ObjectDetection.csproj_ i Visual Studio.

Det här projektet i Visual Studio skapar ett nytt Custom Vision-projekt med namnet __Mitt nya projekt__, som kan nås via [Custom Vision-webbplatsen](https://customvision.ai/). Det laddar sedan upp bilder för att träna och testa en objektidentifierarmodell. I det här projektet tränas modellen i att identifiera gafflar och saxar i bilder.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>Förstå koden

Öppna filen _Program.cs_ och granska koden. [Skapa miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för dina utbildnings-och förutsägelse nycklar med namnet `CUSTOM_VISION_TRAINING_KEY` respektive `CUSTOM_VISION_PREDICTION_KEY`. Skriptet kommer att söka efter dessa variabler.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_keys)]

Hämta även slut punkts-URL: en från sidan Inställningar på webbplatsen för Custom Vision. Spara den till en miljö variabel som kallas `CUSTOM_VISION_ENDPOINT`. Skriptet sparar en referens till den i roten av klassen.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_endpoint)]

### <a name="create-a-new-custom-vision-service-project"></a>Skapa ett nytt projekt för Custom Vision Service

Nästa bit kod skapar ett projekt för identifiering av objekt. Det skapade projektet visas på den [Custom Vision-webbplats](https://customvision.ai/) som du besökte tidigare. Se [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__) -metoden för att ange andra alternativ när du skapar ditt projekt (förklaras i guiden [skapa en Detektors](get-started-build-detector.md) webb Portal).  

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_create)]


### <a name="add-tags-to-the-project"></a>Lägga till taggar till projektet

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>Ladda upp och tagga bilder

När du taggar bilder i objektidentifieringsprojekt måste du bestämma region för varje taggat objekt med hjälp av normaliserade koordinater. Följande kod associerar var och en av exempelbilderna med dess taggade region.

> [!NOTE]
> Om du inte har ett verktyg för att klicka och dra för att markera koordinaterna för regionerna kan du använda webb gränssnittet på [Customvision.AI](https://www.customvision.ai/). I det här exemplet har koordinaterna redan angetts.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload_regions)]

Den här mappningen av associationer används sedan för att ladda upp varje exempelbild med dess regionkoordinater. Du kan ladda upp upp till 64 avbildningar i en enda batch.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload)]

Nu har du laddat upp alla exempel bilder och taggat var och en (**förgrening** eller **sax**) med en associerad pixel-rektangel.

### <a name="train-the-project"></a>Utbilda projektet

Koden skapar den första träningsiterationen i projektet.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_train)]

### <a name="publish-the-current-iteration"></a>Publicera den aktuella iterationen

Det namn som ges till den publicerade iterationen kan användas för att skicka förutsägelse begär Anden. En iteration är inte tillgänglig i förutsägelse slut punkten förrän den har publicerats.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_publish)]

### <a name="create-a-prediction-endpoint"></a>Skapa en förutsägelseslutpunkt

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction_endpoint)]

### <a name="use-the-prediction-endpoint"></a>Använda förutsägelseslutpunkten

Den här delen av skriptet läser in testbilden, frågar modellslutpunkten och genererar förutsägelsedata till konsolen.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>Köra programmet

När programmet körs ska det öppna ett konsolfönster och skriva följande utdata:

```console
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```

Du kan sedan kontrollera att testbilden (finns i **Images/Test/** ) har taggats på rätt sätt och att regionidentifieringen är rätt. Nu kan du avsluta programmet genom att trycka på valfri tangent.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>Nästa steg

Nu har du sett hur du gör varje steg i processen för objekt identifiering i kod. Det här exemplet kör en enda inlärnings upprepning, men ofta behöver du träna och testa din modell flera gånger för att göra den mer exakt. Följande guide behandlar bildklassificering, men principerna liknar dem som gäller för objektidentifiering.

> [!div class="nextstepaction"]
> [Testa och träna om en modell](test-your-model.md)
