---
title: 'Självstudie: Skapa ett bildklassificeringsprojekt med Custom Vision-SDK för Java'
titlesuffix: Azure Cognitive Services
description: Skapa ett projekt, lägg till taggar, ladda upp bilder, träna ditt projekt och gör en förutsägelse med hjälp av standardslutpunkten.
services: cognitive-services
author: areddish
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 08/28/2018
ms.author: areddish
ms.openlocfilehash: e302fc580d9c83d269f0deedd051a3ea23bd274e
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957229"
---
# <a name="tutorial-create-an-image-classification-project-with-the-custom-vision-sdk-for-java"></a>Självstudie: Skapa ett bildklassificeringsprojekt med Custom Vision-SDK för Java

Lär dig hur du skapar ett projekt för bildklassificering med hjälp av Custom Vision Service och Java. När projektet har skapats kan du lägga till taggar, ladda upp bilder, träna projektet, hämta slutpunkts-URL:en för projektets standardförutsägelse och använda projektet för att testa en bild programmatiskt. Använd det här exemplet med öppen källkod som en mall för att skapa din egen app med hjälp av API för Custom Vision.

## <a name="prerequisites"></a>Nödvändiga komponenter

- JDK 7 eller 8 installerat.
- Maven installerat.

## <a name="get-the-training-and-prediction-keys"></a>Hämta utbildnings- och förutsägelsenycklarna

För att hämta nycklarna som används i det här exemplet går du till [Custom Vision-webbsidan](https://customvision.ai) och väljer __kugghjulsikonen__ i det övre högra hörnet. Kopiera värdena från fälten __Utbildningsnyckel__ och __Förutsägelsenyckel__ i avsnittet __Konton__.

![Bild på gränssnittet för nycklarna](./media/python-tutorial/training-prediction-keys.png)

## <a name="install-the-custom-vision-service-sdk"></a>Installera Custom Vision Service SDK

Du kan installera SDK för Custom Vision från maven-centrallager:
* [Training SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
* [Prediction SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

## <a name="understand-the-code"></a>Förstå koden

Den fullständiga projekt, inklusive bilder, är tillgänglig från [Custom Vision Azure-exempel för Java-lagringsplatsen](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master). 

Använd din favorit Java-IDE för att öppna projektet `Vision/CustomVision`. 

Det här programmet använder den utbildningsnyckel som du hämtade tidigare för att skapa ett nytt projekt med namnet __Exampelprojekt med Java__. Det laddar sedan upp bilder för att träna och testa en klassificerare. Klassificeraren anger om ett träd är en __hemlockgran__ eller ett __japanskt körsbärsträd__.

I följande kodavsnitt implementeras de primära funktionerna i det här exemplet:

## <a name="create-a-custom-vision-service-project"></a>Skapa ett projekt för Custom Vision Service

> [!IMPORTANT]
> Ställ in `trainingApiKey` till det utbildningsnyckelvärde som du hämtade tidigare.

```java
final String trainingApiKey = "insert your training key here";
TrainingApi trainClient = CustomVisionTrainingManager.authenticate(trainingApiKey);

Trainings trainer = trainClient.trainings();

System.out.println("Creating project...");
Project project = trainer.createProject()
            .withName("Sample Java Project")
            .execute();
```

## <a name="add-tags-to-your-project"></a>Lägga till taggar till projektet

```java
// create hemlock tag
Tag hemlockTag = trainer.createTag()
    .withProjectId(project.id())
    .withName("Hemlock")
    .execute();

// create cherry tag
Tag cherryTag = trainer.createTag()
    .withProjectId(project.id())
    .withName("Japanese Cherry")
    .execute();
```

## <a name="upload-images-to-the-project"></a>Ladda upp bilder till projektet

Exemplet är konfigurerat för att visa bilderna i slutpaketet. Bilderna lösas från resursdelen för jar och överförs till tjänsten.

```java
System.out.println("Adding images...");
for (int i = 1; i <= 10; i++) {
    String fileName = "hemlock_" + i + ".jpg";
    byte[] contents = GetImage("/Hemlock", fileName);
    AddImageToProject(trainer, project, fileName, contents, hemlockTag.id(), null);
}

for (int i = 1; i <= 10; i++) {
    String fileName = "japanese_cherry_" + i + ".jpg";
    byte[] contents = GetImage("/Japanese Cherry", fileName);
    AddImageToProject(trainer, project, fileName, contents, cherryTag.id(), null);
}
```

Föregående kodfragment använder två hjälpfunktioner som hämtar bilderna som resursdataströmmar och överför dem till tjänsten.

```java
private static void AddImageToProject(Trainings trainer, Project project, String fileName, byte[] contents, UUID tag)
{
    System.out.println("Adding image: " + fileName);

    ImageFileCreateEntry file = new ImageFileCreateEntry()
        .withName(fileName)
        .withContents(contents);

    ImageFileCreateBatch batch = new ImageFileCreateBatch()
        .withImages(Collections.singletonList(file));

    batch = batch.withTagIds(Collections.singletonList(tag));

    trainer.createImagesFromFiles(project.id(), batch);
}

private static byte[] GetImage(String folder, String fileName)
{
    try {
        return ByteStreams.toByteArray(CustomVisionSamples.class.getResourceAsStream(folder + "/" + fileName));
    } catch (Exception e) {
        System.out.println(e.getMessage());
        e.printStackTrace();
    }

    return null;
}
```

## <a name="train-the-project"></a>Utbilda projektet

Detta skapar den första upprepningen i projektet och markerar den här upprepningen som standardupprepningen. 

```java
System.out.println("Training...");
Iteration iteration = trainer.trainProject(project.id());

while (iteration.status().equals("Training"))
{
    System.out.println("Training Status: "+ iteration.status());
    Thread.sleep(1000);
    iteration = trainer.getIteration(project.id(), iteration.id());
}

System.out.println("Training Status: "+ iteration.status());
trainer.updateIteration(project.id(), iteration.id(), iteration.withIsDefault(true));
```

## <a name="get-and-use-the-default-prediction-endpoint"></a>Hämta och använda standardslutpunkten för förutsägelse

> [!IMPORTANT]
> Ställ in `predictionApiKey` till det förutsägelsenyckelvärde som du hämtade tidigare.

```java
final String predictionApiKey = "insert your prediction key here";
PredictionEndpoint predictClient = CustomVisionPredictionManager.authenticate(predictionApiKey);

// Use below for predictions from a url
// String url = "some url";
// ImagePrediction results = predictor.predictions().predictImage()
//                         .withProjectId(project.id())
//                         .withUrl(url)
//                         .execute();

// load test image
byte[] testImage = GetImage("/Test", "test_image.jpg");

// predict
ImagePrediction results = predictor.predictions().predictImage()
    .withProjectId(project.id())
    .withImageData(testImage)
    .execute();

for (Prediction prediction: results.predictions())
{
    System.out.println(String.format("\t%s: %.2f%%", prediction.tagName(), prediction.probability() * 100.0f));
}
```

## <a name="run-the-example"></a>Köra exemplet

Kompilera och kör lösningen med maven:

```
mvn compile exec:java
```

Programmets utdata ser ut ungefär som nedanstående text:

```
Creating project...
Adding images...
Adding image: hemlock_1.jpg
Adding image: hemlock_2.jpg
Adding image: hemlock_3.jpg
Adding image: hemlock_4.jpg
Adding image: hemlock_5.jpg
Adding image: hemlock_6.jpg
Adding image: hemlock_7.jpg
Adding image: hemlock_8.jpg
Adding image: hemlock_9.jpg
Adding image: hemlock_10.jpg
Adding image: japanese_cherry_1.jpg
Adding image: japanese_cherry_2.jpg
Adding image: japanese_cherry_3.jpg
Adding image: japanese_cherry_4.jpg
Adding image: japanese_cherry_5.jpg
Adding image: japanese_cherry_6.jpg
Adding image: japanese_cherry_7.jpg
Adding image: japanese_cherry_8.jpg
Adding image: japanese_cherry_9.jpg
Adding image: japanese_cherry_10.jpg
Training...
Training status: Training
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```