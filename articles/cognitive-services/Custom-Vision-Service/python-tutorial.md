---
title: 'Snabbstart: Skapa ett bildklassificeringsprojekt med Custom Vision-SDK för Python'
titlesuffix: Azure Cognitive Services
description: Skapa ett projekt, lägg till taggar, ladda upp bilder, träna ditt projekt och gör en förutsägelse med hjälp av Python-SDK.
services: cognitive-services
author: areddish
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: quickstart
ms.date: 11/2/2018
ms.author: areddish
ms.openlocfilehash: 4de1a33006f580bda9d356027e93b4bf2309dd90
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51281395"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-python-sdk"></a>Snabbstart: Skapa ett bildklassificeringsprojekt med Custom Vision-SDK med Python

Den här artikeln innehåller information och exempelkod som hjälper dig att komma igång med att använda Custom Vision-SDK med Python för att skapa en bildklassificeringsmodell. När den har skapats kan du lägga till taggar, ladda upp bilder, träna projektet, hämta slutpunkts-URL:en för projektets standardförutsägelse och använda slutpunkten för att testa en avbildning programmatiskt. Använd det här exemplet som en mall för att skapa en egen Python-app. Om du vill gå igenom processen med att skapa och använda en bildklassificeringsmodell _utan_ kod kan du istället läsa den [webbläsarbaserade vägledningen](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

- [Python 2.7+ eller 3.5+](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/)-verktyget

## <a name="install-the-custom-vision-sdk"></a>Installera Custom Vision-SDK

Kör följande kommando i PowerShell för att installera Custom Vision Service SDK för Python:

```PowerShell
pip install azure-cognitiveservices-vision-customvision
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [python-get-images](includes/python-get-images.md)]


## <a name="add-the-code"></a>Lägga till koden

Skapa en ny fil med namnet *sample.py* i den projektkatalog du vill använda.

### <a name="create-the-custom-vision-service-project"></a>Skapa Custom Vision Service-projektet

Lägg till följande kod i skriptet för att skapa ett nytt Custom Vision Service-projekt. Infoga dina prenumerationsnycklar i lämpliga definitioner.

```Python
from azure.cognitiveservices.vision.customvision.training import training_api
from azure.cognitiveservices.vision.customvision.training.models import ImageUrlCreateEntry

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"

trainer = training_api.TrainingApi(training_key)

# Create a new project
print ("Creating project...")
project = trainer.create_project("My New Project")
```

### <a name="create-tags-in-the-project"></a>Skapa taggar i projektet

Om du vill skapa klassificeringstaggar i projektet lägger du till följande kod i slutet av *sample.py*:

```Python
# Make two tags in the new project
hemlock_tag = trainer.create_tag(project.id, "Hemlock")
cherry_tag = trainer.create_tag(project.id, "Japanese Cherry")
```

### <a name="upload-and-tag-images"></a>Ladda upp och tagga bilder

Infoga följande kod efter att taggen har skapats för att lägga till exempelbilder i projektet. Den här koden laddar upp varje bild med dess motsvarande tagg. Du måste ange basbildens URL-sökväg, baserat på var du har laddat ned Cognitive Services Python SDK Samples-projektet.

> [!NOTE]
> Du måste ändra sökvägen till bilderna utifrån var du har laddat ned Cognitive Services Python SDK Samples-projektet tidigare.

```Python
base_image_url = "<path to project>"

print ("Adding images...")
for image_num in range(1,10):
    image_url = base_image_url + "Images/Hemlock/hemlock_{}.jpg".format(image_num)
    trainer.create_images_from_urls(project.id, [ ImageUrlCreateEntry(url=image_url, tag_ids=[ hemlock_tag.id ] ) ])

for image_num in range(1,10):
    image_url = base_image_url + "Images/Japanese Cherry/japanese_cherry_{}.jpg".format(image_num)
    trainer.create_images_from_urls(project.id, [ ImageUrlCreateEntry(url=image_url, tag_ids=[ cherry_tag.id ] ) ])
```

### <a name="train-the-classifier"></a>Träna klassificeraren

Den här koden skapar den första upprepningen i projektet och markerar den som standardupprepningen. Standardupprepningen speglar den version av modellen som svarar på förutsägelsebegäranden. Du bör uppdatera detta varje gång du tränar modellen.

```Python
import time

print ("Training...")
iteration = trainer.train_project(project.id)
while (iteration.status != "Completed"):
    iteration = trainer.get_iteration(project.id, iteration.id)
    print ("Training status: " + iteration.status)
    time.sleep(1)

# The iteration is now trained. Make it the default project endpoint
trainer.update_iteration(project.id, iteration.id, is_default=True)
print ("Done!")
```

### <a name="get-and-use-the-default-prediction-endpoint"></a>Hämta och använda standardslutpunkten för förutsägelse

Om du vill skicka en bild till slutpunkten för förutsägelse och hämta förutsägelsen, lägger du till följande kod i slutet av filen:

```python
from azure.cognitiveservices.vision.customvision.prediction import prediction_endpoint
from azure.cognitiveservices.vision.customvision.prediction.prediction_endpoint import models

# Now there is a trained endpoint that can be used to make a prediction

predictor = prediction_endpoint.PredictionEndpoint(prediction_key)

test_img_url = base_image_url + "Images/Test/test_image.jpg"
results = predictor.predict_image_url(project.id, iteration.id, url=test_img_url)

# Display the results.
for prediction in results.predictions:
    print ("\t" + prediction.tag_name + ": {0:.2f}%".format(prediction.probability * 100))
```

## <a name="run-the-application"></a>Köra programmet

Kör *sample.py*.

```PowerShell
python sample.py
```

Programmets utdata bör ser ut ungefär som nedanstående text:

```
Creating project...
Adding images...
Training...
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

Du kan sedan kontrollera att testbilden (finns i **<base_image_url>/Images/Test/**) har taggats på rätt sätt. Du kan också gå tillbaka till [Custom Vision-webbplatsen](https://customvision.ai) och se det aktuella tillståndet för det nyskapade projektet.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Nästa steg

Nu har du sett hur varje steg i bildklassificeringsprocessen kan göras med kod. Det här exemplet kör en enstaka träningsupprepning men ofta måste du träna och testa modellen flera gånger för att kunna göra den mer exakt.

> [!div class="nextstepaction"]
> [Testa och träna om modellen](test-your-model.md)