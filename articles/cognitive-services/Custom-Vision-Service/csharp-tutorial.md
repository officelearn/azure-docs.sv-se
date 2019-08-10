---
title: 'Snabbstart: Skapa ett bildklassificeringsprojekt med Custom Vision-SDK för C#'
titleSuffix: Azure Cognitive Services
description: Skapa ett projekt, lägg till taggar, ladda upp bilder, träna ditt projekt och gör en förutsägelse med hjälp av .NET SDK med C#.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 08/08/2019
ms.author: anroth
ms.openlocfilehash: ef5e1d7bb2c5b4404ce1a43bc719b7b5a242b1c7
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946250"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-net-sdk"></a>Snabbstart: Skapa ett bildklassificeringsprojekt med Custom Vision .NET SDK

Den här artikeln innehåller information och exempelkod som hjälper dig att komma igång med att använda Custom Vision-SDK med C# för att skapa en bildklassificeringsmodell. När den har skapats kan du lägga till taggar, ladda upp bilder, träna projektet, hämta slutpunkts-URL:en för projektets standardförutsägelse och använda slutpunkten för att testa en avbildning programmatiskt. Använd det här exemplet som en mall för att skapa dit eget .NET-program. Om du vill gå igenom processen med att skapa och använda en bildklassificeringsmodell _utan_ kod kan du i stället läsa den [webbläsarbaserade vägledningen](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Förutsättningar

- Valfri version av [Visual Studio 2015 eller 2017](https://www.visualstudio.com/downloads/)

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Hämta Custom Vision-SDK:n och exempelkoden

Om du vill skriva ett .NET-program som använder Custom Vision behöver du Custom Vision NuGet-paket. De här paketen ingår i det exempel projekt som du kommer att hämta, men du kan komma åt dem separat här.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Klona eller ladda ned [Cognitive Services .NET-exempelprojektet](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Navigera till mappen **CustomVision/ImageClassification** och öppna _ImageClassification.csproj_ i Visual Studio.

Det här Visual Studio-projektet skapar ett nytt Custom Vision-projekt med namnet __My New Project__ , som kan nås via [Custom Vision-webbplatsen](https://customvision.ai/). Det laddar sedan upp bilder för att träna och testa en klassificerare. I det här projektet är klassificeraren avsedd att avgöra huruvida ett träd är en __hemlockgran__ eller ett __japanskt körsbärsträd__ .

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>Förstå koden

Öppna filen _Program.cs_ och granska koden. Infoga dina prenumerationsnycklar i lämpliga definitioner i metoden **Main**.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=21-30)]

Slutpunktsparametern bör peka på den region där den Azure-resursgrupp som innehåller Custom Vision-resurserna skapades. I det här exemplet antar vi regionen Södra centrala USA och använder:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=14-14)]

Följande rader i koden kör de primära funktionerna i projektet.

### <a name="create-a-new-custom-vision-service-project"></a>Skapa ett nytt projekt för Custom Vision Service

Det skapade projektet visas på den [Custom Vision-webbplats](https://customvision.ai/) som du besökte tidigare. Se [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__) -metoden för att ange andra alternativ när du skapar ditt projekt (förklaras i guiden [skapa en klassificerings](getting-started-build-a-classifier.md) webb Portal).   

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=32-34)]

### <a name="create-tags-in-the-project"></a>Skapa taggar i projektet

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=36-38)]

### <a name="upload-and-tag-images"></a>Ladda upp och tagga bilder

Avbildningarna för det här projektet ingår. De refereras till i metoden **LoadImagesFromDisk** i _Program.cs_ . Du kan ladda upp upp till 64 avbildningar i en enda batch.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=40-55)]

### <a name="train-the-classifier-and-publish"></a>Träna klassificeraren och publicera

Den här koden skapar den första iterationen i projektet och publicerar sedan en upprepning till förutsägelse slut punkten. Det namn som ges till den publicerade iterationen kan användas för att skicka förutsägelse begär Anden. En iteration är inte tillgänglig i förutsägelse slut punkten förrän den har publicerats.

```csharp
var iteration = trainingApi.TrainProject(project.Id);
// The returned iteration will be in progress, and can be queried periodically to see when it has completed
while (iteration.Status == "Training")
{
        Thread.Sleep(1000);

        // Re-query the iteration to get it's updated status
        iteration = trainingApi.GetIteration(project.Id, iteration.Id);
}

// The iteration is now trained. Publish it to the prediction end point.
var publishedModelName = "treeClassModel";
var predictionResourceId = "<target prediction resource ID>";
trainingApi.PublishIteration(project.Id, iteration.Id, publishedModelName, predictionResourceId);
Console.WriteLine("Done!\n");
```

### <a name="set-the-prediction-endpoint"></a>Ange förutsägelseslutpunkten

Förutsägelseslutpunkten är den referens som du kan använda för att skicka en avbildning till den aktuella modellen och få en klassificeringsförutsägelse.

```csharp
// Create a prediction endpoint, passing in obtained prediction key
CustomVisionPredictionClient endpoint = new CustomVisionPredictionClient()
{
        ApiKey = predictionKey,
        Endpoint = SouthCentralUsEndpoint
};
```

### <a name="submit-an-image-to-the-default-prediction-endpoint"></a>Skicka en avbildning till standardslutpunkten för förutsägelse

I det här skriptet läses testavbildningen in i metoden **LoadImagesFromDisk**, och modellens förutsägelseutdata ska visas i konsolen.

```csharp
// Make a prediction against the new project
Console.WriteLine("Making a prediction:");
var result = endpoint.ClassifyImage(project.Id, publishedModelName, testImage);

// Loop over each prediction and write out the results
foreach (var c in result.Predictions)
{
        Console.WriteLine($"\t{c.TagName}: {c.Probability:P1}");
}
```

## <a name="run-the-application"></a>Köra programmet

När programmet körs ska det öppna ett konsolfönster och skriva följande utdata:

```console
Creating new project:
        Uploading images
        Training
Done!

Making a prediction:
        Hemlock: 95.0%
        Japanese Cherry: 0.0%
```

Du kan sedan kontrollera att testbilden (som finns i **Images/Test/** ) har taggats på rätt sätt. Avsluta programmet genom att trycka på valfri tangent. Du kan även gå tillbaka till [Custom Vision-webbplatsen](https://customvision.ai) och se det aktuella tillståndet för det nyskapade projektet.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Nästa steg

Nu har du sett hur varje steg i bildklassificeringsprocessen kan utföras med kod. Det här exemplet kör en enstaka träningsiteration, men ofta måste du träna och testa modellen flera gånger för att kunna göra den mer exakt.

> [!div class="nextstepaction"]
> [Testa och träna om en modell](test-your-model.md)
