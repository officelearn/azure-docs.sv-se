---
title: 'Snabbstart: Skapa ett bildklassificeringsprojekt med Custom Vision-SDK för Python'
titlesuffix: Azure Cognitive Services
description: Skapa ett projekt, lägg till taggar, ladda upp bilder, träna ditt projekt och gör en förutsägelse med hjälp av Python-SDK.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: areddish
ms.openlocfilehash: 47e2f2a03c08ae1e44dcba35b440880ce06f6f95
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484469"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-python-sdk"></a>Snabbstart: Skapa ett bildklassificeringsprojekt med Custom Vision för Python SDK

Den här artikeln innehåller information och exempelkod som hjälper dig att komma igång med att använda Custom Vision-SDK med Python för att skapa en bildklassificeringsmodell. När den har skapats kan du lägga till taggar, ladda upp bilder, träna projektet, hämta projektets publicerade förutsägelse slutpunkts-URL och använder slutpunkten programmatiskt en bild. Använd det här exemplet som en mall för att skapa ditt eget Python-program. Om du vill gå igenom processen med att skapa och använda en bildklassificeringsmodell _utan_ kod kan du i stället läsa den [webbläsarbaserade vägledningen](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Förutsättningar

- [Python 2.7+ eller 3.5+](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/)-verktyget

## <a name="install-the-custom-vision-sdk"></a>Installera Custom Vision-SDK

Kör följande kommando i PowerShell för att installera Custom Vision Service SDK för Python:

```powershell
pip install azure-cognitiveservices-vision-customvision
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [python-get-images](includes/python-get-images.md)]


## <a name="add-the-code"></a>Lägga till koden

Skapa en ny fil med namnet *sample.py* i den projektkatalog du vill använda.

### <a name="create-the-custom-vision-service-project"></a>Skapa Custom Vision Service-projektet

Lägg till följande kod i skriptet för att skapa ett nytt Custom Vision Service-projekt. Infoga dina prenumerationsnycklar i lämpliga definitioner.

```Python
from azure.cognitiveservices.vision.customvision.training import CustomVisionTrainingClient
from azure.cognitiveservices.vision.customvision.training.models import ImageFileCreateEntry

ENDPOINT = "https://southcentralus.api.cognitive.microsoft.com"

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"
prediction_resource_id = "<your prediction resource id>"

publish_iteration_name = "classifyModel"

trainer = CustomVisionTrainingClient(training_key, endpoint=ENDPOINT)

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

print("Adding images...")

image_list = []

for image_num in range(1, 11):
    file_name = "hemlock_{}.jpg".format(image_num)
    with open(base_image_url + "images/Hemlock/" + file_name, "rb") as image_contents:
        image_list.append(ImageFileCreateEntry(name=file_name, contents=image_contents.read(), tag_ids=[hemlock_tag.id]))

for image_num in range(1, 11):
    file_name = "japanese_cherry_{}.jpg".format(image_num)
    with open(base_image_url + "images/Japanese Cherry/" + file_name, "rb") as image_contents:
        image_list.append(ImageFileCreateEntry(name=file_name, contents=image_contents.read(), tag_ids=[cherry_tag.id]))

upload_result = trainer.create_images_from_files(project.id, images=image_list)
if not upload_result.is_batch_successful:
    print("Image batch upload failed.")
    for image in upload_result.images:
        print("Image status: ", image.status)
    exit(-1)
```

### <a name="train-the-classifier-and-publish"></a>Träna klassificeraren och publicera

Den här koden skapar den första upprepningen i projektet och sedan publicerar den iterationen till slutpunkten för förutsägelse. Namnet på den publicerade iterationen kan användas för att skicka förfrågningar för förutsägelse. En iteration är inte tillgänglig i förutsägelse slutpunkten tills den har publicerats.

```Python
import time

print ("Training...")
iteration = trainer.train_project(project.id)
while (iteration.status != "Completed"):
    iteration = trainer.get_iteration(project.id, iteration.id)
    print ("Training status: " + iteration.status)
    time.sleep(1)

# The iteration is now trained. Publish it to the project endpoint
trainer.publish_iteration(project.id, iteration.id, publish_iteration_name, prediction_resource_id)
print ("Done!")
```

### <a name="get-and-use-the-published-iteration-on-the-prediction-endpoint"></a>Hämta och använda den publicerade iterationen på slutpunkten för förutsägelse

Om du vill skicka en bild till slutpunkten för förutsägelse och hämta förutsägelsen lägger du till följande kod i slutet av filen:

```python
from azure.cognitiveservices.vision.customvision.prediction import CustomVisionPredictionClient

# Now there is a trained endpoint that can be used to make a prediction
predictor = CustomVisionPredictionClient(prediction_key, endpoint=ENDPOINT)

with open(base_image_url + "images/Test/test_image.jpg", "rb") as image_contents:
    results = predictor.classify_image(project.id, publish_iteration_name, image_contents.read())

    # Display the results.
    for prediction in results.predictions:
        print ("\t" + prediction.tag_name + ": {0:.2f}%".format(prediction.probability * 100))
```

## <a name="run-the-application"></a>Köra programmet

Kör *sample.py*.

```powershell
python sample.py
```

Programmets utdata bör ser ut ungefär som nedanstående text:

```console
Creating project...
Adding images...
Training...
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
