---
title: 'Snabbstart: Skapa ett objektidentifieringsprojekt med Custom Vision SDK för Go'
titlesuffix: Azure Cognitive Services
description: Skapa ett projekt, lägg till taggar, ladda upp bilder, träna projektet och identifiera objekt med hjälp av Go SDK.
services: cognitive-services
author: areddish
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: quickstart
ms.date: 2/25/2018
ms.author: daauld
ms.openlocfilehash: 93a6d923aff49811a4b5b0bc2236af8d0bd4c067
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885254"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-go-sdk"></a>Snabbstart: Skapa ett objektidentifieringsprojekt med Custom Vision. Go SDK

Den här artikeln innehåller information och exempelkod som hjälper dig att komma igång med att använda Custom Vision-SDK med Go för att skapa en objektidentifierarmodell. När den har skapats kan du lägga till taggade regioner, ladda upp bilder, träna projektet, hämta slutpunkts-URL:en för projektets standardförutsägelse och använda slutpunkten för att testa en bild programmatiskt. Använd det här exemplet som mall för att skapa dit eget Go-program.

## <a name="prerequisites"></a>Nödvändiga komponenter

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

Lägg till följande kod i skriptet för att skapa ett nytt Custom Vision Service-projekt. Infoga dina prenumerationsnycklar i lämpliga definitioner. Observera att skillnaden mellan att skapa ett projekt för objektidentifiering och för bildklassificering är den domän som anges i anropet av **create_project**.

```go
import(
    "context"
    "bytes"
    "fmt"
    "io/ioutil"
    "path"
    "log"
    "time"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.2/customvision/training"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.1/customvision/prediction"
)

var (
    training_key string = "<your training key>"
    prediction_key string = "<your prediction key>"
    endpoint string = "https://southcentralus.api.cognitive.microsoft.com"
    project_name string = "Go Sample OD Project"
    sampleDataDirectory = "<path to sample images>"
)

func main() {
    fmt.Println("Creating project...")

    ctx = context.Background()

    trainer := training.New(training_key, endpoint)

    var objectDetectDomain training.Domain
    domains, _ := trainer.GetDomains(ctx)

    for _, domain := range *domains.Value {
        fmt.Println(domain, domain.Type)
        if domain.Type == "ObjectDetection" && *domain.Name == "General" {
            objectDetectDomain = domain
            break
        }
    }
    fmt.Println("Creating project...")
    project, _ := trainer.CreateProject(ctx, project_name, "", objectDetectDomain.ID, "")
```

### <a name="create-tags-in-the-project"></a>Skapa taggar i projektet

Om du vill skapa klassificeringstaggar i projektet lägger du till följande kod i slutet av *sample.go*:

```Go
    # Make two tags in the new project
    forkTag, _ := trainer.CreateTag(ctx, *project.ID, "fork", "A fork", string(training.Regular))
    scissorsTag, _ := trainer.CreateTag(ctx, *project.ID, "scissors", "Pair of scissors", string(training.Regular))
```

### <a name="upload-and-tag-images"></a>Ladda upp och tagga bilder

När du taggar bilder i objektidentifieringsprojekt måste du bestämma region för varje taggat objekt med hjälp av normaliserade koordinater.

För att lägga till bilder, taggar och regioner i projektet lägger du till följande kod efter att taggarna har skapats. Observera att regionerna för den här självstudien är hårdkodade i linje med koden. Regionerna specificerar avgränsningsfältet i normaliserade koordinater, och koordinaterna anges i följande ordning: vänster, överst, bredd, höjd.

```Go
    forkImageRegions := map[string][4]float64{
        "fork_1.jpg": [4]float64{ 0.145833328, 0.3509314, 0.5894608, 0.238562092 },
        "fork_2.jpg": [4]float64{ 0.294117659, 0.216944471, 0.534313738, 0.5980392 },
        "fork_3.jpg": [4]float64{ 0.09191177, 0.0682516545, 0.757352948, 0.6143791 },
        "fork_4.jpg": [4]float64{ 0.254901975, 0.185898721, 0.5232843, 0.594771266 },
        "fork_5.jpg": [4]float64{ 0.2365196, 0.128709182, 0.5845588, 0.71405226 },
        "fork_6.jpg": [4]float64{ 0.115196079, 0.133611143, 0.676470637, 0.6993464 },
        "fork_7.jpg": [4]float64{ 0.164215669, 0.31008172, 0.767156839, 0.410130739 },
        "fork_8.jpg": [4]float64{ 0.118872553, 0.318251669, 0.817401946, 0.225490168 },
        "fork_9.jpg": [4]float64{ 0.18259804, 0.2136765, 0.6335784, 0.643790841 },
        "fork_10.jpg": [4]float64{ 0.05269608, 0.282303959, 0.8088235, 0.452614367 },
        "fork_11.jpg": [4]float64{ 0.05759804, 0.0894935, 0.9007353, 0.3251634 },
        "fork_12.jpg": [4]float64{ 0.3345588, 0.07315363, 0.375, 0.9150327 },
        "fork_13.jpg": [4]float64{ 0.269607842, 0.194068655, 0.4093137, 0.6732026 },
        "fork_14.jpg": [4]float64{ 0.143382356, 0.218578458, 0.7977941, 0.295751631 },
        "fork_15.jpg": [4]float64{ 0.19240196, 0.0633497, 0.5710784, 0.8398692 },
        "fork_16.jpg": [4]float64{ 0.140931368, 0.480016381, 0.6838235, 0.240196079 },
        "fork_17.jpg": [4]float64{ 0.305147052, 0.2512582, 0.4791667, 0.5408496 },
        "fork_18.jpg": [4]float64{ 0.234068632, 0.445702642, 0.6127451, 0.344771236 },
        "fork_19.jpg": [4]float64{ 0.219362751, 0.141781077, 0.5919118, 0.6683006 },
        "fork_20.jpg": [4]float64{ 0.180147052, 0.239820287, 0.6887255, 0.235294119 },
    }
    
    scissorsImageRegions := map[string][4]float64{
        "scissors_1.jpg": [4]float64{ 0.4007353, 0.194068655, 0.259803921, 0.6617647 },
        "scissors_2.jpg": [4]float64{ 0.426470578, 0.185898721, 0.172794119, 0.5539216 },
        "scissors_3.jpg": [4]float64{ 0.289215684, 0.259428144, 0.403186262, 0.421568632 },
        "scissors_4.jpg": [4]float64{ 0.343137264, 0.105833367, 0.332107842, 0.8055556 },
        "scissors_5.jpg": [4]float64{ 0.3125, 0.09766343, 0.435049027, 0.71405226 },
        "scissors_6.jpg": [4]float64{ 0.379901975, 0.24308826, 0.32107842, 0.5718954 },
        "scissors_7.jpg": [4]float64{ 0.341911763, 0.20714055, 0.3137255, 0.6356209 },
        "scissors_8.jpg": [4]float64{ 0.231617644, 0.08459154, 0.504901946, 0.8480392 },
        "scissors_9.jpg": [4]float64{ 0.170343131, 0.332957536, 0.767156839, 0.403594762 },
        "scissors_10.jpg": [4]float64{ 0.204656869, 0.120539248, 0.5245098, 0.743464053 },
        "scissors_11.jpg": [4]float64{ 0.05514706, 0.159754932, 0.799019635, 0.730392158 },
        "scissors_12.jpg": [4]float64{ 0.265931368, 0.169558853, 0.5061275, 0.606209159 },
        "scissors_13.jpg": [4]float64{ 0.241421565, 0.184264734, 0.448529422, 0.6830065 },
        "scissors_14.jpg": [4]float64{ 0.05759804, 0.05027781, 0.75, 0.882352948 },
        "scissors_15.jpg": [4]float64{ 0.191176474, 0.169558853, 0.6936275, 0.6748366 },
        "scissors_16.jpg": [4]float64{ 0.1004902, 0.279036, 0.6911765, 0.477124184 },
        "scissors_17.jpg": [4]float64{ 0.2720588, 0.131977156, 0.4987745, 0.6911765 },
        "scissors_18.jpg": [4]float64{ 0.180147052, 0.112369314, 0.6262255, 0.6666667 },
        "scissors_19.jpg": [4]float64{ 0.333333343, 0.0274019931, 0.443627447, 0.852941155 },
        "scissors_20.jpg": [4]float64{ 0.158088237, 0.04047389, 0.6691176, 0.843137264 },
    }
```
Använd sedan den här mappningen av associationer för att ladda upp varje exempelbild med dess regionkoordinater. Lägg till följande kod.

> [!NOTE]
> Du behöver ändra sökvägen till bilderna utifrån var du laddade ned Cognitive Services Python Go Samples-projektet tidigare.

```Go
    // Go through the data table above and create the images
    fmt.Println("Adding images...")
    var fork_images []training.ImageFileCreateEntry
    for file, region := range forkImageRegions {
        imageFile, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "fork", file))

        imageRegion := training.Region { 
            TagID:forkTag.ID,
            Left:&region[0],
            Top:&region[1],
            Width:&region[2],
            Height:&region[3],
        }

        fork_images = append(fork_images, training.ImageFileCreateEntry {
            Name: &file,
            Contents: &imageFile,
            Regions: &[]training.Region{ imageRegion },
        })
    }
        
    fork_batch, _ := trainer.CreateImagesFromFiles(ctx, *project.ID, training.ImageFileCreateBatch{ 
        Images: &fork_images,
    })

    if (!*fork_batch.IsBatchSuccessful) {
        fmt.Println("Batch upload failed.")
    }

    var scissor_images []training.ImageFileCreateEntry
    for file, region := range scissorsImageRegions {
        imageFile, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "scissors", file))

        imageRegion := training.Region { 
            TagID:scissorsTag.ID,
            Left:&region[0],
            Top:&region[1],
            Width:&region[2],
            Height:&region[3],
        }

        scissor_images = append(scissor_images, training.ImageFileCreateEntry {
            Name: &file,
            Contents: &imageFile,
            Regions: &[]training.Region{ imageRegion },
        })
    }
        
    scissor_batch, _ := trainer.CreateImagesFromFiles(ctx, *project.ID, training.ImageFileCreateBatch{ 
        Images: &scissor_images,
     })
     
    if (!*scissor_batch.IsBatchSuccessful) {
        fmt.Println("Batch upload failed.")
    }    
```

### <a name="train-the-project"></a>Utbilda projektet

Den här koden skapar den första iterationen i projektet och markerar den som standardinteration. Standarditerationen speglar versionen av den modell som svarar på förutsägelsebegäranden. Du bör uppdatera detta varje gång du tränar om modellen.

```go
    iteration, _ := trainer.TrainProject(ctx, *project.ID)
    fmt.Println("Training status:", *iteration.Status)
    for {
        if *iteration.Status != "Training" {
            break
        }
        time.Sleep(5 * time.Second)
        iteration, _ = trainer.GetIteration(ctx, *project.ID, *iteration.ID)
        fmt.Println("Training status:", *iteration.Status)
    }

    // Mark iteration as default
    *iteration.IsDefault = true
    trainer.UpdateIteration(ctx, *project.ID, *iteration.ID, iteration)
```

### <a name="get-and-use-the-default-prediction-endpoint"></a>Hämta och använda standardslutpunkten för förutsägelse

Om du vill skicka en bild till slutpunkten för förutsägelse och hämta förutsägelsen lägger du till följande kod i slutet av filen:

```go
    fmt.Println("Predicting...")
    predictor := prediction.New(prediction_key, endpoint)

    testImageData, _ := ioutil.ReadFile(path.Join(sampleDataDirectory, "Test", "test_od_image.jpg"))
    results, _ := predictor.PredictImage(ctx, *project.ID, ioutil.NopCloser(bytes.NewReader(testImageData)), iteration.ID, "")

    for _, prediction := range *results.Predictions {
        boundingBox := *prediction.BoundingBox

        fmt.Printf("\t%s: %.2f%% (%.2f, %.2f, %.2f, %.2f)", 
            *prediction.TagName,
            *prediction.Probability * 100,
            *boundingBox.Left,
            *boundingBox.Top,
            *boundingBox.Width,
            *boundingBox.Height)
        fmt.Println("")
    }
}
```

## <a name="run-the-application"></a>Köra programmet

Kör *sample.go*.

```PowerShell
go run sample.go
```

Programmets utdata bör visas i konsolen. Du kan sedan kontrollera att testbilden (som finns i **samples/vision/images/Test**) har taggats på rätt sätt och att regionidentifieringen är korrekt.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>Nästa steg

Nu har du sett hur varje steg i processen för objektidentifiering kan utföras med kod. Det här exemplet kör en enstaka träningsiteration, men ofta måste du träna och testa modellen flera gånger för att kunna göra den mer exakt. Följande guide behandlar bildklassificering, men principerna liknar dem som gäller för objektidentifiering.

> [!div class="nextstepaction"]
> [Testa och träna om en modell](test-your-model.md)