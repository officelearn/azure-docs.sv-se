---
title: 'Snabbstart: Skapa ett bildklassificeringsprojekt med Custom Vision SDK för Go'
titlesuffix: Azure Cognitive Services
description: Skapa ett projekt, lägg till taggar, ladda upp bilder, träna ditt projekt och gör en förutsägelse med hjälp av Go SDK.
services: cognitive-services
author: areddish
manager: daauld
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: areddish
ms.openlocfilehash: f740974d17ad5f95bca6530a61619ee0283f819a
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58479996"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-go-sdk"></a>Snabbstart: Skapa ett bildklassificeringsprojekt med Custom Vision SDK för Go

Den här artikeln innehåller information och exempelkod som hjälper dig att komma igång med att använda Custom Vision SDK med Go för att skapa en bildklassificeringsmodell. När den har skapats kan du lägga till taggar, ladda upp bilder, träna projektet, hämta projektets publicerade förutsägelse slutpunkts-URL och använder slutpunkten programmatiskt en bild. Använd det här exemplet som mall för att skapa dit eget Go-program. Om du vill gå igenom processen med att skapa och använda en bildklassificeringsmodell _utan_ kod kan du i stället läsa den [webbläsarbaserade vägledningen](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Förutsättningar

- [Go 1.8+](https://golang.org/doc/install)

## <a name="install-the-custom-vision-sdk"></a>Installera Custom Vision-SDK

Kör följande kommando i PowerShell för att installera Custom Vision Service SDK för Go:

```
go get -u github.com/Azure/azure-sdk-for-go/...
```

eller i din lagringsplatskörning om du använder dep:
```
dep ensure -add github.com/Azure/azure-sdk-for-go
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [python-get-images](includes/python-get-images.md)]

## <a name="add-the-code"></a>Lägga till koden

Skapa en ny fil med namnet *sample.go* i den projektkatalog som du vill använda.

### <a name="create-the-custom-vision-service-project"></a>Skapa Custom Vision Service-projektet

Lägg till följande kod i skriptet för att skapa ett nytt Custom Vision Service-projekt. Infoga dina prenumerationsnycklar i lämpliga definitioner.

```go
import(
    "context"
    "bytes"
    "fmt"
    "io/ioutil"
    "path"
    "log"
    "time"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v3.0/customvision/training"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v3.0/customvision/prediction"
)

var (
    training_key string = "<your training key>"
    prediction_key string = "<your prediction key>"
    prediction_resource_id = "<your prediction resource id>"
    endpoint string = "https://southcentralus.api.cognitive.microsoft.com"
    project_name string = "Go Sample Project"
    iteration_publish_name = "classifyModel"
    sampleDataDirectory = "<path to sample images>"
)

func main() {
    fmt.Println("Creating project...")

    ctx = context.Background()

    trainer := training.New(training_key, endpoint)

    project, err := trainer.CreateProject(ctx, project_name, "sample project", nil, string(training.Multilabel))
    if (err != nil) {
        log.Fatal(err)
    }
```

### <a name="create-tags-in-the-project"></a>Skapa taggar i projektet

Om du vill skapa klassificeringstaggar i projektet lägger du till följande kod i slutet av *sample.go*:

```go
    // Make two tags in the new project
    hemlockTag, _ := trainer.CreateTag(ctx, *project.ID, "Hemlock", "Hemlock tree tag", string(training.Regular))
    cherryTag, _ := trainer.CreateTag(ctx, *project.ID, "Japanese Cherry", "Japanese cherry tree tag", string(training.Regular))
```

### <a name="upload-and-tag-images"></a>Ladda upp och tagga bilder

Infoga följande kod efter att taggen har skapats för att lägga till exempelbilder i projektet. Den här koden laddar upp varje bild med dess motsvarande tagg. Du behöver ange basbildens URL-sökväg, baserat på var du laddade ned Cognitive Services Python Go Samples-projektet.

> [!NOTE]
> Du behöver ändra sökvägen till bilderna utifrån var du laddade ned Cognitive Services Python Go Samples-projektet tidigare.

```go
    fmt.Println("Adding images...")
    japaneseCherryImages, err := ioutil.ReadDir(path.Join(sampleDataDirectory, "Japanese Cherry"))
    if err != nil {
        fmt.Println("Error finding Sample images")
    }

    hemLockImages, err := ioutil.ReadDir(path.Join(sampleDataDirectory, "Hemlock"))
    if err != nil {
        fmt.Println("Error finding Sample images")
    }

    for _, file := range hemLockImages {
        imageFile, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Hemlock", file.Name()))
        imageData := ioutil.NopCloser(bytes.NewReader(imageFile))

        trainer.CreateImagesFromData(ctx, *project.ID, imageData, []string{ hemlockTag.ID.String() })
    }

    for _, file := range japaneseCherryImages {
        imageFile, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Japanese Cherry", file.Name()))
        imageData := ioutil.NopCloser(bytes.NewReader(imageFile))
        trainer.CreateImagesFromData(ctx, *project.ID, imageData, []string{ cherryTag.ID.String() })
    }
```

### <a name="train-the-classifier-and-publish"></a>Träna klassificeraren och publicera

Den här koden skapar den första upprepningen i projektet och sedan publicerar den iterationen till slutpunkten för förutsägelse. Namnet på den publicerade iterationen kan användas för att skicka förfrågningar för förutsägelse. En iteration är inte tillgänglig i förutsägelse slutpunkten tills den har publicerats.

```go
    fmt.Println("Training...")
    iteration, _ := trainer.TrainProject(ctx, *project.ID)
    for {
        if *iteration.Status != "Training" {
            break
        }
        fmt.Println("Training status: " + *iteration.Status)
        time.Sleep(1 * time.Second)
        iteration, _ = trainer.GetIteration(ctx, *project.ID, *iteration.ID)
    }
    fmt.Println("Training status: " + *iteration.Status)

    trainer.PublishIteration(ctx, *project.ID, *iteration.ID, iteration_publish_name, prediction_resource_id))
```

### <a name="get-and-use-the-published-iteration-on-the-prediction-endpoint"></a>Hämta och använda den publicerade iterationen på slutpunkten för förutsägelse

Om du vill skicka en bild till slutpunkten för förutsägelse och hämta förutsägelsen lägger du till följande kod i slutet av filen:

```go
    fmt.Println("Predicting...")
    predictor := prediction.New(prediction_key, endpoint)

    testImageData, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Test", "test_image.jpg"))
    results, _ := predictor.ClassifyImage(ctx, *project.ID, iteration_publish_name, ioutil.NopCloser(bytes.NewReader(testImageData)), "")

    for _, prediction := range *results.Predictions {
        fmt.Printf("\t%s: %.2f%%", *prediction.TagName, *prediction.Probability * 100)
        fmt.Println("")
    }
}
```

## <a name="run-the-application"></a>Köra programmet

Kör *sample.go*.

```powershell
go run sample.go
```

Programmets utdata bör ser ut ungefär som nedanstående text:

```
Creating project...
Adding images...
Training...
Training status: Training
Training status: Training
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

Du kan sedan kontrollera att testbilden (finns i **<base_image_url>/Images/Test/**) har taggats på rätt sätt. Du kan även gå tillbaka till [Custom Vision-webbplatsen](https://customvision.ai) och se det aktuella tillståndet för det nyskapade projektet.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Nästa steg

Nu har du sett hur varje steg i bildklassificeringsprocessen kan utföras med kod. Det här exemplet kör en enstaka träningsiteration, men ofta måste du träna och testa modellen flera gånger för att kunna göra den mer exakt.

> [!div class="nextstepaction"]
> [Testa och träna om en modell](test-your-model.md)