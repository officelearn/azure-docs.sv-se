---
title: 'Självstudie: Skapa ett bildklassificeringsprojekt med Custom Vision-SDK för Python'
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
ms.openlocfilehash: 96125ba1c54f742bb9ddf32a1588173217be0766
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49953120"
---
# <a name="tutorial-create-an-image-classification-project-with-the-custom-vision-sdk-for-python"></a>Självstudie: Skapa ett bildklassificeringsprojekt med Custom Vision-SDK för Python

Lär dig hur du skapar ett projekt för bildklassificering med hjälp av Custom Vision Service och ett grundläggande Python-skript. När projektet har skapats kan du lägga till taggar, ladda upp bilder, träna projektet, hämta slutpunkts-URL:en för projektets standardförutsägelse och använda projektet för att testa en bild programmatiskt. Använd det här exemplet med öppen källkod som en mall för att skapa din egen app med hjälp av API för Custom Vision.



## <a name="prerequisites"></a>Nödvändiga komponenter

- Python 2.7+ eller Python 3.5+.
- Pip-verktyget.

## <a name="get-the-training-and-prediction-keys"></a>Hämta utbildnings- och förutsägelsenycklarna

För att hämta nycklarna som används i det här exemplet går du till [Custom Vision-webbsidan](https://customvision.ai) och väljer __kugghjulsikonen__ i det övre högra hörnet. Kopiera värdena från fälten __Utbildningsnyckel__ och __Förutsägelsenyckel__ i avsnittet __Konton__.

![Bild på gränssnittet för nycklarna](./media/python-tutorial/training-prediction-keys.png)

## <a name="install-the-custom-vision-service-sdk"></a>Installera Custom Vision Service SDK

Om du vill installera Custom Vision Service SDK, använder du följande kommando:

```
pip install azure-cognitiveservices-vision-customvision
```

## <a name="get-example-images"></a>Hämta exempel på bilder

Det här exemplet använder bilderna från katalogen `Samples/Images` i projektet [https://github.com/Microsoft/Cognitive-CustomVision-Windows](https://github.com/Microsoft/Cognitive-CustomVision-Windows/tree/master/Samples/Images). Klona eller ladda ned och extrahera projektet till utvecklingsmiljön.

## <a name="create-a-custom-vision-service-project"></a>Skapa ett Custom Vision Service-projekt

För att skapa ett nytt Custom Vision Service-projekt börjar du med att skapa en ny fil med namnet `sample.py`. Använd följande kod som filinnehåll:

> [!IMPORTANT]
> Ställ in `training_key` till det utbildningsnyckelvärde som du hämtade tidigare.
>
> Ställ in `prediction_key` till det förutsägelsenyckelvärde som du hämtade tidigare.

```python
from azure.cognitiveservices.vision.customvision.training import training_api
from azure.cognitiveservices.vision.customvision.training.models import ImageUrlCreateEntry

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"

trainer = training_api.TrainingApi(training_key)

# Create a new project
print ("Creating project...")
project = trainer.create_project("My Project")
```

## <a name="add-tags-to-your-project"></a>Lägga till taggar till projektet

Lägg till nedanstående kod i slutet av filen `sample.py` för att lägga till taggar till ditt projekt:

```python
# Make two tags in the new project
hemlock_tag = trainer.create_tag(project.id, "Hemlock")
cherry_tag = trainer.create_tag(project.id, "Japanese Cherry")
```

## <a name="upload-images-to-the-project"></a>Ladda upp bilder till projektet

Infoga följande kod efter att taggen har skapats för att lägga till exempelbilder i projektet. Den här koden laddar upp bilden med motsvarande tagg:

> [!IMPORTANT]
>
> Ändra sökvägen till bilderna baserat på var du laddade ned Cognitive-CustomVision-Windows-projektet tidigare.

```python
base_image_url = "https://raw.githubusercontent.com/Microsoft/Cognitive-CustomVision-Windows/master/Samples/"

print ("Adding images...")
for image_num in range(1,10):
    image_url = base_image_url + "Images/Hemlock/hemlock_{}.jpg".format(image_num)
    trainer.create_images_from_urls(project.id, [ ImageUrlCreateEntry(url=image_url, tag_ids=[ hemlock_tag.id ] ) ])

for image_num in range(1,10):
    image_url = base_image_url + "Images/Japanese Cherry/japanese_cherry_{}.jpg".format(image_num)
    trainer.create_images_from_urls(project.id, [ ImageUrlCreateEntry(url=image_url, tag_ids=[ cherry_tag.id ] ) ])


# Alternatively, if the images were on disk in a folder called Images alongside the sample.py, then
# they can be added by using the following:
#
#import os
#hemlock_dir = "Images\\Hemlock"
#for image in os.listdir(os.fsencode("Images\\Hemlock")):
#    with open(hemlock_dir + "\\" + os.fsdecode(image), mode="rb") as img_data: 
#        trainer.create_images_from_data(project.id, img_data, [ hemlock_tag.id ])
#
#cherry_dir = "Images\\Japanese Cherry"
#for image in os.listdir(os.fsencode("Images\\Japanese Cherry")):
#    with open(cherry_dir + "\\" + os.fsdecode(image), mode="rb") as img_data: 
#        trainer.create_images_from_data(project.id, img_data, [ cherry_tag.id ])
```

## <a name="train-the-project"></a>Träna projektet

Lägg till nedanstående kod i slutet av filen `sample.py` för att träna klassificeraren:

```python
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

## <a name="get-and-use-the-default-prediction-endpoint"></a>Hämta och använda standardslutpunkten för förutsägelse

Om du vill skicka en bild till slutpunkten för förutsägelse och hämta förutsägelsen, lägger du till följande kod i slutet av filen `sample.py`:

```python
from azure.cognitiveservices.vision.customvision.prediction import prediction_endpoint
from azure.cognitiveservices.vision.customvision.prediction.prediction_endpoint import models

# Now there is a trained endpoint that can be used to make a prediction

predictor = prediction_endpoint.PredictionEndpoint(prediction_key)

test_img_url = base_image_url + "Images/Test/test_image.jpg"
results = predictor.predict_image_url(project.id, iteration.id, url=test_img_url)

# Alternatively, if the images were on disk in a folder called Images alongside the sample.py, then
# they can be added by using the following.
#
# Open the sample image and get back the prediction results.
# with open("Images\\test\\test_image.jpg", mode="rb") as test_data:
#     results = predictor.predict_image(project.id, test_data, iteration.id)

# Display the results.
for prediction in results.predictions:
    print ("\t" + prediction.tag_name + ": {0:.2f}%".format(prediction.probability * 100))
```

## <a name="run-the-example"></a>Kör exemplet

Kör lösningen. Förutsägelseresultatet visas i konsolen.

```
python sample.py
```

Programmets utdata ser ut ungefär som nedanstående text:

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