---
title: Skapa en Custom Vision Service Java-självstudie – Azure Cognitive Services | Microsoft Docs
description: Utforska en grundläggande Java-app som använder anpassat API för visuellt innehåll i Microsoft Cognitive Services. Skapa ett projekt, lägga till taggar, ladda upp bilder, träna ditt projekt och göra en förutsägelse med hjälp av standardslutpunkt.
services: cognitive-services
author: areddish
manager: chbuehle
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 08/28/2018
ms.author: areddish
ms.openlocfilehash: a83a2f5cac9281a4cd79c1a0cead0f2af82d73df
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44305711"
---
# <a name="use-custom-vision-api-to-build-an-image-classification-project-with-java"></a>Använd anpassad API för visuellt innehåll för att skapa ett projekt för klassificering av avbildning med Java

Lär dig hur du skapar ett projekt för klassificering av avbildning med Custom Vision Service med hjälp av Java. När den har skapats kan du lägga till taggar, ladda upp bilder, träna projektet, hämta projektets standard förutsägelse slutpunkts-URL och använder programmatiskt en bild. Använd det här exemplet med öppen källkod som en mall för att skapa din egen app med hjälp av anpassat API för visuellt innehåll.

## <a name="prerequisites"></a>Förutsättningar

- JDK 7 eller 8 installerat.
- Maven har installerats.

## <a name="get-the-training-and-prediction-keys"></a>Hämta nycklar Inlärnings- och förutsägelsetransaktioner

För att få nycklarna som används i det här exemplet kan du gå till den [Custom Vision-webbsida](https://customvision.ai) och välj den __kugghjulsikonen__ i det övre högra hörnet. I den __konton__ och kopiera värdena från den __utbildning nyckeln__ och __förutsägelse nyckeln__ fält.

![Bild av nycklar UI](./media/python-tutorial/training-prediction-keys.png)

## <a name="install-the-custom-vision-service-sdk"></a>Installera Custom Vision Service SDK

Du kan installera SDK: N för Custom Vision från maven-centrallager:
* [Utbildning SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
* [Förutsägelse SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

## <a name="understand-the-code"></a>Förstå koden

Den fullständiga projekt, inklusive bilder, är tillgänglig från den [Custom Vision Azure-exempel för Java-lagringsplatsen](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master). 

Använd din favorit Java-IDE för att öppna den `Vision/CustomVision` projekt. 

Det här programmet använder nyckeln utbildning som hämtades tidigare för att skapa ett nytt projekt med namnet __Java exempelprojektet__. Det laddar sedan upp bilder för att träna och testa en klassificerare. Klassificeraren anger om ett träd är en __Viol__ eller en __japanska Välj ut__.

I följande kodavsnitt implementera de primära funktionerna i det här exemplet:

## <a name="create-a-custom-vision-service-project"></a>Skapa ett projekt för Custom Vision Service

> [!IMPORTANT]
> Ange den `trainingApiKey` till utbildning nyckelvärdet som hämtades tidigare.

```java
final String trainingApiKey = "insert your training key here";
TrainingApi trainClient = CustomVisionTrainingManager.authenticate(trainingApiKey);

Trainings trainer = trainClient.trainings();

System.out.println("Creating project...");
Project project = trainer.createProject()
            .withName("Sample Java Project")
            .execute();
```

## <a name="add-tags-to-your-project"></a>Lägg till taggar i projektet

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

I exemplet är konfigurerad att ta med bilder i slutpaketet. Bilder att läsa från jar resursdel och överförts till tjänsten.

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

Föregående kodfragment koden använder två hjälpfunktioner som hämta bilder som resursen dataströmmar och överföra dem till tjänsten.

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

## <a name="train-the-project"></a>Träna projektet

Detta skapar den första upprepningen i projektet och markerar den här iterationen som standard iteration. 

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
> Ange den `predictionApiKey` till förutsägelse nyckelvärdet som hämtades tidigare.

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

## <a name="run-the-example"></a>Kör exempel

Kompilera och kör lösningen med maven:

```
mvn compile exec:java
```

Utdata från programmet liknar följande text:

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