---
title: Använda anpassade Vision tjänsten från en C#-program - kognitiva Azure-tjänster | Microsoft Docs
description: 'Utforska en grundläggande C#-app som använder API: et för anpassade Vision i kognitiva Microsoft-tjänster. Skapa ett projekt, lägga till taggar, ladda upp bilder, träna projektet och göra en förutsägelse med hjälp av standardslutpunkten.'
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 80cb022808748ed2c60dff7c363d6020cb4043a8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352869"
---
# <a name="use-the-custom-vision-service-from-a-c35-application"></a>Använda anpassade Vision tjänsten från en C&#35; program

Lär dig hur du använder anpassade Vision tjänsten från ett C#-program. När den har skapats kan du lägga till taggar, ladda upp bilder, träna projektet, hämta projektets standard förutsägelse slutpunkts-URL och använder slutpunkten programmatiskt en bild. Använd det här exemplet med öppen källkod som en mall för att skapa din egen app för Windows med hjälp av anpassade Vision Service API.

## <a name="prerequisites"></a>Förutsättningar

* En utgåva av Visual Studio 2015 eller 2017 för Windows.

* Den [anpassade Vision Service SDK](http://github.com/Microsoft/Cognitive-CustomVision-Windows/). Detta inkluderar exempel och bilder som används i det här dokumentet.

## <a name="get-the-training-and-prediction-keys"></a>Hämta nycklar utbildning och förutsägelse

För att få de nycklar som används i det här exemplet finns i [anpassad Vision webbsida](https://customvision.ai) och välj den __kugghjulet ikonen__ i det övre högra hörnet. I den __konton__ och kopiera värdena från den __utbildning nyckeln__ och __förutsägelse nyckeln__ fält.

![Bild av nycklar UI](./media/csharp-tutorial/training-prediction-keys.png)

## <a name="understand-the-code"></a>Förstå koden

Öppna i Visual Studio finns i projektet i `Samples/CustomVision.Sample/` för SDK-projektet.

Det här programmet använder utbildning-nyckel som du hämtade tidigare om du vill skapa ett nytt projekt med namnet __Mina nytt projekt__. Den sedan överför avbildningar för att träna och testa en klassificerare. Klassificeraren anger om ett träd är en __Viol__ eller en __japanska Cherry__.

Följande kodavsnitt implementera primära funktionerna i det här exemplet:

* __Skapa ett nytt anpassat Vision Service-projekt__:

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

* __Ange en standard iteration för förutsägelse slutpunkten__:

    ```csharp
    // The iteration is now trained. Make it the default project endpoint
    iteration.IsDefault = true;
    trainingApi.UpdateIteration(project.Id, iteration.Id, iteration);
    Console.WriteLine("Done!\n");
    ```

* __Skapa en slutpunkt för förutsägelse__:
 
    ```csharp
    // Create a prediction endpoint, passing in obtained prediction key
    PredictionEndpoint endpoint = new PredictionEndpoint() { ApiKey = predictionKey };
    ```
 
* __Skicka en bild till slutpunkten för förutsägelse__:

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

1. Gör följande ändringar för att lägga till utbildning och förutsägelse nycklar i programmet:

    * Lägg till din __utbildning nyckeln__ till följande rad:

        ```csharp
        string trainingKey = "<your key here>";
        ```

    * Lägg till din __förutsägelse nyckeln__ till följande rad:

        ```csharp
        string predictionKey = "<your key here>";
        ```

2. Kör appen. När programmet körs skrivs i följande utdata till konsolen:

    ```
    Creating new project:
            Uploading images
            Training
    Done!

    Making a prediction:
            Hemlock: 95.0%
            Japanese Cherry: 0.0%
    ```

3. Tryck på valfri tangent för att avsluta programmet.
