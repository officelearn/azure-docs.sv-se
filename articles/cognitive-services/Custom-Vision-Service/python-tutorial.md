---
title: Skapa en Custom Vision Service Python-självstudie – Azure Cognitive Services | Microsoft Docs
description: Utforska en grundläggande Python-app som använder anpassat API för visuellt innehåll i Microsoft Cognitive Services. Skapa ett projekt, lägga till taggar, ladda upp bilder, träna ditt projekt och göra en förutsägelse med hjälp av standardslutpunkt.
services: cognitive-services
author: areddish
manager: chbuehle
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 08/28/2018
ms.author: areddish
ms.openlocfilehash: df0bdc0bbd2768566336323851f366c9ae280a88
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44301607"
---
# <a name="custom-vision-api-python-tutorial"></a>Anpassade Vision API Python-självstudier

Lär dig hur du skapar ett projekt för klassificering av avbildning med Custom Vision Service och ett grundläggande Python-skript. När den har skapats kan du lägga till taggar, ladda upp bilder, träna projektet, hämta projektets standard förutsägelse slutpunkts-URL och använder programmatiskt en bild. Använd det här exemplet med öppen källkod som en mall för att skapa din egen app med hjälp av anpassat API för visuellt innehåll.



## <a name="prerequisites"></a>Förutsättningar

- Python 2.7 + eller Python 3.5 +.
- Pip-verktyget.

## <a name="get-the-training-and-prediction-keys"></a>Hämta nycklar Inlärnings- och förutsägelsetransaktioner

För att få nycklarna som används i det här exemplet kan du gå till den [Custom Vision-webbsida](https://customvision.ai) och välj den __kugghjulsikonen__ i det övre högra hörnet. I den __konton__ och kopiera värdena från den __utbildning nyckeln__ och __förutsägelse nyckeln__ fält.

![Bild av nycklar UI](./media/python-tutorial/training-prediction-keys.png)

## <a name="install-the-custom-vision-service-sdk"></a>Installera Custom Vision Service SDK

Om du vill installera Custom Vision Service SDK, använder du följande kommando:

```
pip install azure-cognitiveservices-vision-customvision
```

## <a name="get-example-images"></a>Hämta exempel bilder

Det här exemplet använder avbildningar från den `Samples/Images` katalogen i den [ https://github.com/Microsoft/Cognitive-CustomVision-Windows ](https://github.com/Microsoft/Cognitive-CustomVision-Windows/tree/master/Samples/Images) projekt. Klona eller ladda ned och extrahera projektet till utvecklingsmiljön.

## <a name="create-a-custom-vision-service-project"></a>Skapa ett projekt för Custom Vision Service

Om du vill skapa ett nytt projekt för Custom Vision Service, skapa ny fil med namnet `sample.py`. Använd följande kod som filinnehållet:

> [!IMPORTANT]
> Ange den `training_key` till utbildning nyckelvärdet som hämtades tidigare.
>
> Ange den `prediction_key` till förutsägelse nyckelvärdet som hämtades tidigare.

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

## <a name="add-tags-to-your-project"></a>Lägg till taggar i projektet

Lägg till taggar i projektet genom att lägga till följande kod i slutet av den `sample.py` fil:

```python
# Make two tags in the new project
hemlock_tag = trainer.create_tag(project.id, "Hemlock")
cherry_tag = trainer.create_tag(project.id, "Japanese Cherry")
```

## <a name="upload-images-to-the-project"></a>Ladda upp bilder till projektet

Infoga följande kod efter det taggen har skapandet för att lägga till exempelbilderna i projektet. Den här koden överför avbildningen med motsvarande taggen:

> [!IMPORTANT]
>
> Ändra sökvägen till bilderna utifrån där du hämtade Cognitive-CustomVision-Windows-projektet tidigare.

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

För att träna klassificeraren, lägger du till följande kod i slutet av den `sample.py` fil:

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

Om du vill skicka en avbildning till slutpunkten för förutsägelse och hämta förutsägelsen, lägger du till följande kod i slutet av den `sample.py` fil:

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

## <a name="run-the-example"></a>Kör exempel

Kör lösningen. De förutsagda resultaten som visas på konsolen.

```
python sample.py
```

Utdata från programmet liknar följande text:

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