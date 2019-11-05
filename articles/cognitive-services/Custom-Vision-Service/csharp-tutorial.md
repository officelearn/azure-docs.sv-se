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
ms.openlocfilehash: ca21bbd77b269e3034fd69cc4685311e91295f36
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "73519105"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-net-sdk"></a>Snabbstart: Skapa ett bildklassificeringsprojekt med Custom Vision .NET SDK

Den här artikeln innehåller information och exempelkod som hjälper dig att komma igång med att använda Custom Vision-SDK med C# för att skapa en bildklassificeringsmodell. När den har skapats kan du lägga till taggar, ladda upp bilder, träna projektet, hämta slutpunkts-URL:en för projektets standardförutsägelse och använda slutpunkten för att testa en avbildning programmatiskt. Använd det här exemplet som en mall för att skapa dit eget .NET-program. Om du vill gå igenom processen med att skapa och använda en bildklassificeringsmodell _utan_ kod kan du i stället läsa den [webbläsarbaserade vägledningen](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Förutsättningar

- Valfri version av [Visual Studio 2015 eller 2017](https://www.visualstudio.com/downloads/)
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Hämta Custom Vision-SDK:n och exempelkoden

Om du vill skriva ett .NET-program som använder Custom Vision behöver du Custom Vision NuGet-paket. De här paketen ingår i det exempel projekt som du kommer att hämta, men du kan komma åt dem separat här.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Klona eller ladda ned [Cognitive Services .NET-exempelprojektet](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples). Navigera till mappen **CustomVision/ImageClassification** och öppna _ImageClassification.csproj_ i Visual Studio.

Det här Visual Studio-projektet skapar ett nytt Custom Vision-projekt med namnet __My New Project__, som kan nås via [Custom Vision-webbplatsen](https://customvision.ai/). Det laddar sedan upp bilder för att träna och testa en klassificerare. I det här projektet är klassificeraren avsedd att avgöra huruvida ett träd är en __hemlockgran__ eller ett __japanskt körsbärsträd__.

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>Förstå koden

Öppna filen _Program.cs_ och granska koden. [Skapa miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för dina utbildnings-och förutsägelse nycklar med namnet `CUSTOM_VISION_TRAINING_KEY` respektive `CUSTOM_VISION_PREDICTION_KEY`. Skriptet kommer att leta efter dessa.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_keys)]

Hämta även slut punkts-URL: en från sidan Inställningar på webbplatsen för Custom Vision. Spara den till en miljö variabel som kallas `CUSTOM_VISION_ENDPOINT`. Skriptet sparar en referens till den i roten av klassen.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_endpoint)]

Följande rader i koden kör de primära funktionerna i projektet.

### <a name="create-a-new-custom-vision-service-project"></a>Skapa ett nytt projekt för Custom Vision Service

Det skapade projektet visas på den [Custom Vision-webbplats](https://customvision.ai/) som du besökte tidigare. Se [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__) -metoden för att ange andra alternativ när du skapar ditt projekt (förklaras i guiden [skapa en klassificerings](getting-started-build-a-classifier.md) webb Portal).   

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_create)]

### <a name="create-tags-in-the-project"></a>Skapa taggar i projektet

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>Ladda upp och tagga bilder

Avbildningarna för det här projektet ingår. De refereras till i metoden **LoadImagesFromDisk** i _Program.cs_. Du kan ladda upp upp till 64 avbildningar i en enda batch.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_upload)]

### <a name="train-the-classifier-and-publish"></a>Träna klassificeraren och publicera

Den här koden skapar den första iterationen i projektet och publicerar sedan en upprepning till förutsägelse slut punkten. Det namn som ges till den publicerade iterationen kan användas för att skicka förutsägelse begär Anden. En iteration är inte tillgänglig i förutsägelse slut punkten förrän den har publicerats.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_train)]

### <a name="set-the-prediction-endpoint"></a>Ange förutsägelseslutpunkten

Förutsägelseslutpunkten är den referens som du kan använda för att skicka en avbildning till den aktuella modellen och få en klassificeringsförutsägelse.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction_endpoint)]

### <a name="submit-an-image-to-the-default-prediction-endpoint"></a>Skicka en avbildning till standardslutpunkten för förutsägelse

I det här skriptet läses testavbildningen in i metoden **LoadImagesFromDisk**, och modellens förutsägelseutdata ska visas i konsolen. Värdet för variabeln publishedModelName ska motsvara värdet "publicerat as" på Custom Vision Portals flik för **prestanda** . 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction)]

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
