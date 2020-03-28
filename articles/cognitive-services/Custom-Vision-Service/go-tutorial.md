---
title: 'Snabbstart: Skapa ett bildklassificeringsprojekt med Custom Vision SDK for Go'
titleSuffix: Azure Cognitive Services
description: Skapa ett projekt, lägg till taggar, ladda upp bilder, träna ditt projekt och gör en förutsägelse med hjälp av Go SDK.
services: cognitive-services
author: areddish
manager: daauld
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: areddish
ms.openlocfilehash: f8391818ebf13afb3b07eead55133aadde6158f0
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170108"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-go-sdk"></a>Snabbstart: Skapa ett bildklassificeringsprojekt med Custom Vision Go SDK

Den här artikeln innehåller information och exempelkod som hjälper dig att komma igång med att använda Custom Vision SDK med Go för att skapa en bildklassificeringsmodell. När den har skapats kan du lägga till taggar, ladda upp bilder, träna projektet, hämta projektets publicerade slutpunkts-URL för förutsägelse och använda slutpunkten för att programmässigt testa en bild. Använd det här exemplet som mall för att skapa dit eget Go-program. Om du vill gå igenom processen med att skapa och använda en bildklassificeringsmodell _utan_ kod kan du istället läsa den [webbläsarbaserade vägledningen](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Krav

- [Go 1.8+](https://golang.org/doc/install)
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="install-the-custom-vision-sdk"></a>Installera Custom Vision-SDK

Kör följande kommando i PowerShell för att installera Custom Vision Service SDK för Go:

```shell
go get -u github.com/Azure/azure-sdk-for-go/...
```

eller om `dep`du använder , inom din repo run:
```shell
dep ensure -add github.com/Azure/azure-sdk-for-go
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [python-get-images](includes/python-get-images.md)]

## <a name="add-the-code"></a>Lägga till koden

Skapa en ny fil med namnet *sample.go* i den projektkatalog som du vill använda.

### <a name="create-the-custom-vision-service-project"></a>Skapa Custom Vision Service-projektet

Lägg till följande kod i skriptet för att skapa ett nytt Custom Vision Service-projekt. Infoga dina prenumerationsnycklar i lämpliga definitioner. Hämta även slut punkts-URL: en från sidan Inställningar på webbplatsen för Custom Vision.

Se metoden [CreateProject](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) för att ange andra alternativ när du skapar projektet (förklaras i webbportalguiden [Skapa en klassificerare).](getting-started-build-a-classifier.md)

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
    endpoint string = "<your endpoint URL>"
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

Infoga följande kod efter att taggen har skapats för att lägga till exempelbilder i projektet. Den här koden laddar upp varje bild med dess motsvarande tagg. Du kan ladda upp upp till 64 bilder i en enda batch.

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

Den här koden skapar den första iterationen av förutsägelsemodellen och publicerar sedan iterationen till förutsägelseslutpunkten. Det namn som ges till den publicerade iterationen kan användas för att skicka förutsägelse begär Anden. En iteration är inte tillgänglig i förutsägelseslutpunkten förrän den publiceras.

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

### <a name="get-and-use-the-published-iteration-on-the-prediction-endpoint"></a>Hämta och använda den publicerade iterationen på förutsägelseslutpunkten

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

## <a name="run-the-application"></a>Köra appen

Kör *sample.go*.

```shell
go run sample.go
```

Programmets utdata bör ser ut ungefär som nedanstående text:

```console
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

Du kan sedan kontrollera att testbilden (finns i **<base_image_url>/Images/Test/**) har taggats på rätt sätt. Du kan också gå tillbaka till [Custom Vision-webbplatsen](https://customvision.ai) och se det aktuella tillståndet för det nyskapade projektet.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Nästa steg

Nu har du sett hur varje steg i bildklassificeringsprocessen kan göras med kod. Det här exemplet kör en enstaka träningsiteration, men ofta måste du träna och testa modellen flera gånger för att kunna göra den mer exakt.

> [!div class="nextstepaction"]
> [Testa och träna om en modell](test-your-model.md)