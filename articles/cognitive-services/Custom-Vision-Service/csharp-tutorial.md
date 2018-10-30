---
title: 'Självstudie: Skapa ett bildklassificeringsprojekt med Custom Vision-SDK för C#'
titlesuffix: Azure Cognitive Services
description: Skapa ett projekt, lägg till taggar, ladda upp bilder, träna ditt projekt och gör en förutsägelse genom att använda standardslutpunkten.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: e046fe452a13384ae7929be805c6252d6ad2fbf9
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49953051"
---
# <a name="tutorial-create-an-image-classification-project-with-the-custom-vision-sdk-for-c"></a>Självstudie: Skapa ett bildklassificeringsprojekt med Custom Vision-SDK för C#

Lär dig att använda Custom Vision Service SDK i ett C#-program. När den har skapats kan du lägga till taggar, ladda upp bilder, träna projektet, hämta slutpunkts-URL:en för projektets standardförutsägelse och använda slutpunkten för att testa en avbildning programmatiskt. Använd det här exemplet med öppen källkod som en mall för att skapa din egen app för Windows med hjälp av Custom Vision Service-API:et.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Valfri version av Visual Studio 2017 för Windows.

## <a name="get-the-custom-vision-sdk-and-samples"></a>Hämta Custom Vision-SDK:n och exempel
Om du vill skapa det här exemplet behöver du Custom Vision SDK NuGet-paketen:

* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
* [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

Du kan ladda ned bilder tillsammans med [C#-exemplet](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/CustomVision).

## <a name="get-the-training-and-prediction-keys"></a>Hämta utbildningen och förutsägelsenycklarna

För att hämta nycklarna som används i det här exemplet går du till [Custom Vision-webbsidan](https://customvision.ai) och väljer __kugghjulsikonen__ i det övre högra hörnet. Kopiera värdena från fälten __Utbildningsnyckel__ och __Förutsägelsenyckel__ i avsnittet __Konton__.

![Bild på gränssnittet för nycklarna](./media/csharp-tutorial/training-prediction-keys.png)

## <a name="understand-the-code"></a>Förstå koden

Öppna projektet i SDK-projektets `Samples/CustomVision.Sample/`-katalog i Visual Studio.

Det här programmet använder den utbildningsnyckel som du hämtade tidigare för att skapa ett nytt projekt med namnet __Mitt nya projekt__. Det laddar sedan upp bilder för att träna och testa en klassificerare. Klassificeraren anger om ett träd är en __hemlockgran__ eller ett __japanskt körsbärsträd__.

I följande kodavsnitt implementeras de primära funktionerna i det här exemplet:

* __Skapa ett nytt projekt för Custom Vision Service__:

    ```csharp
     // Create a new project
    Console.WriteLine("Creating new project:");
    var project = trainingApi.CreateProject("My New Project");
    ```

* __Skapa taggar i ett projekt__:

    ```csharp
    // Make two tags in the new project
    var hemlockTag = trainingApi.CreateTag(project.Id, "Hemlock");
    var japaneseCherryTag = trainingApi.CreateTag(project.Id, "Japanese Cherry");
    ```

* __Ladda upp och tagga bilder__:

    ```csharp
    // Add some images to the tags
    Console.WriteLine("\tUploading images");
    LoadImagesFromDisk();

    // Images can be uploaded one at a time
    foreach (var image in hemlockImages)
    {
        using (var stream = new MemoryStream(File.ReadAllBytes(image)))
        {
            trainingApi.CreateImagesFromData(project.Id, stream, new List<string>() { hemlockTag.Id.ToString() });
        }
    }

    // Or uploaded in a single batch 
    var imageFiles = japaneseCherryImages.Select(img => new ImageFileCreateEntry(Path.GetFileName(img), File.ReadAllBytes(img))).ToList();
    trainingApi.CreateImagesFromFiles(project.Id, new ImageFileCreateBatch(imageFiles, new List<Guid>() { japaneseCherryTag.Id }));
    ```

* __Träna klassificeraren__:

    ```csharp
    // Now there are images with tags start training the project
    Console.WriteLine("\tTraining");
    var iteration = trainingApi.TrainProject(project.Id);

    // The returned iteration will be in progress, and can be queried periodically to see when it has completed
    while (iteration.Status == "Completed")
    {
        Thread.Sleep(1000);

        // Re-query the iteration to get it's updated status
        iteration = trainingApi.GetIteration(project.Id, iteration.Id);
    }
    ```

* __Ange en standarditeration för förutsägelseslutpunkten__:

    ```csharp
    // The iteration is now trained. Make it the default project endpoint
    iteration.IsDefault = true;
    trainingApi.UpdateIteration(project.Id, iteration.Id, iteration);
    Console.WriteLine("Done!\n");
    ```

* __Skapa en förutsägelseslutpunkt__:
 
    ```csharp
    // Create a prediction endpoint, passing in obtained prediction key
    PredictionEndpoint endpoint = new PredictionEndpoint() { ApiKey = predictionKey };
    ```
 
* __Skicka en avbildning till förutsägelseslutpunkten__:

    ```csharp
    // Make a prediction against the new project
    Console.WriteLine("Making a prediction:");
    var result = endpoint.PredictImage(project.Id, testImage);

    // Loop over each prediction and write out the results
    foreach (var c in result.Predictions)
    {
        Console.WriteLine($"\t{c.TagName}: {c.Probability:P1}");
    }
    ```

## <a name="run-the-application"></a>Köra programmet

1. Lägg till utbildnings- och förutsägelsenycklarna till programmet genom att göra följande ändringar:

    * Lägg till din __utbildningsnyckel__ på följande rad:

        ```csharp
        string trainingKey = "<your key here>";
        ```

    * Lägg till din __förutsägelsenyckel__ på följande rad:

        ```csharp
        string predictionKey = "<your key here>";
        ```

2. Kör appen. När programmet körs skrivs följande utdata till konsolen:

    ```
    Creating new project:
            Uploading images
            Training
    Done!

    Making a prediction:
            Hemlock: 95.0%
            Japanese Cherry: 0.0%
    ```

3. Avsluta programmet genom att trycka på valfri tangent.
