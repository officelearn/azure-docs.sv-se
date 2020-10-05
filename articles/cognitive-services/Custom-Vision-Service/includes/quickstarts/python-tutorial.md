---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 09/15/2020
ms.openlocfilehash: a091222b01669c6b83c599787c61dcd6b62b05d0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "90605010"
---
Den här guiden innehåller instruktioner och exempel kod som hjälper dig att komma igång med Custom Vision klient biblioteket för python för att skapa en bild klassificerings modell. Du skapar ett projekt, lägger till taggar, tränar projektet och använder projektets förutsäga slut punkts-URL för att program mässigt testa det. Använd det här exemplet som mall för att skapa en egen bild igenkännings app.

> [!NOTE]
> Om du vill skapa och träna en klassificerings modell _utan att_ skriva kod, se den [webbläsarbaserade vägledningen](../../getting-started-build-a-classifier.md) i stället.

## <a name="prerequisites"></a>Förutsättningar

- [Python 2.7+ eller 3.5+](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/)-verktyget
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="install-the-custom-vision-client-library"></a>Installera klient biblioteket för Custom Vision

Om du vill skriva en app Analysis-app med Custom Vision för python behöver du Custom Vision klient biblioteket. Kör följande kommando i PowerShell:

```powershell
pip install azure-cognitiveservices-vision-customvision
```

[!INCLUDE [get-keys](../../includes/get-keys.md)]

[!INCLUDE [python-get-images](../../includes/python-get-images.md)]

## <a name="add-the-code"></a>Lägga till koden

Skapa en ny fil med namnet *sample.py* i den projektkatalog du vill använda.

## <a name="create-the-custom-vision-project"></a>Skapa Custom Vision-projektet

Lägg till följande kod i skriptet för att skapa ett nytt Custom Vision Service-projekt. Infoga dina prenumerationsnycklar i lämpliga definitioner. Hämta även slut punkts-URL: en från sidan Inställningar på webbplatsen för Custom Vision.

Se [create_project](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin?view=azure-python#create-project-name--description-none--domain-id-none--classification-type-none--target-export-platforms-none--custom-headers-none--raw-false----operation-config-) -metoden för att ange andra alternativ när du skapar ditt projekt (förklaras i guiden [skapa en klassificerings](../../getting-started-build-a-classifier.md) webb Portal).  

```Python
from azure.cognitiveservices.vision.customvision.training import CustomVisionTrainingClient
from azure.cognitiveservices.vision.customvision.training.models import ImageFileCreateBatch, ImageFileCreateEntry
from msrest.authentication import ApiKeyCredentials

ENDPOINT = "<your API endpoint>"

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"
prediction_resource_id = "<your prediction resource id>"

publish_iteration_name = "classifyModel"

credentials = ApiKeyCredentials(in_headers={"Training-key": training_key})
trainer = CustomVisionTrainingClient(ENDPOINT, credentials)

# Create a new project
print ("Creating project...")
project = trainer.create_project("My New Project")
```

## <a name="create-tags-in-the-project"></a>Skapa taggar i projektet

Om du vill skapa klassificeringstaggar i projektet lägger du till följande kod i slutet av *sample.py*:

```Python
# Make two tags in the new project
hemlock_tag = trainer.create_tag(project.id, "Hemlock")
cherry_tag = trainer.create_tag(project.id, "Japanese Cherry")
```

## <a name="upload-and-tag-images"></a>Ladda upp och tagga bilder

Infoga följande kod efter att taggen har skapats för att lägga till exempelbilder i projektet. Den här koden laddar upp varje bild med dess motsvarande tagg. Du kan ladda upp upp till 64 avbildningar i en enda batch.

> [!NOTE]
> Du måste ändra sökvägen till avbildningarna baserat på var du laddade ned Cognitive Services python SDK-exempel lagrings platsen tidigare.

```Python
base_image_url = "<path to repo directory>/cognitive-services-python-sdk-samples/samples/vision/"

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

upload_result = trainer.create_images_from_files(project.id, ImageFileCreateBatch(images=image_list))
if not upload_result.is_batch_successful:
    print("Image batch upload failed.")
    for image in upload_result.images:
        print("Image status: ", image.status)
    exit(-1)
```

## <a name="train-and-publish-the-project"></a>Träna och publicera projektet

Den här koden skapar den första iterationen av förutsägelse modellen och publicerar sedan en upprepning till förutsägelse slut punkten. Det namn som ges till den publicerade iterationen kan användas för att skicka förutsägelse begär Anden. En iteration är inte tillgänglig i förutsägelse slut punkten förrän den har publicerats.

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

## <a name="use-the-prediction-endpoint"></a>Använd förutsägelse slut punkten

Om du vill skicka en bild till slutpunkten för förutsägelse och hämta förutsägelsen lägger du till följande kod i slutet av filen:

```python
from azure.cognitiveservices.vision.customvision.prediction import CustomVisionPredictionClient
from msrest.authentication import ApiKeyCredentials

# Now there is a trained endpoint that can be used to make a prediction
prediction_credentials = ApiKeyCredentials(in_headers={"Prediction-key": prediction_key})
predictor = CustomVisionPredictionClient(ENDPOINT, prediction_credentials)

with open(base_image_url + "images/Test/test_image.jpg", "rb") as image_contents:
    results = predictor.classify_image(
        project.id, publish_iteration_name, image_contents.read())

    # Display the results.
    for prediction in results.predictions:
        print("\t" + prediction.tag_name +
              ": {0:.2f}%".format(prediction.probability * 100))
```

## <a name="run-the-application"></a>Kör programmet

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

Sedan kan du kontrol lera att test avbildningen (som finns i **<base_image_url>bilder/test/**) är korrekt Taggad. Du kan också gå tillbaka till [Custom Vision-webbplatsen](https://customvision.ai) och se det aktuella tillståndet för det nyskapade projektet.

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Nästa steg

Nu har du sett hur varje steg i objekt identifierings processen kan göras i kod. Det här exemplet kör en enda inlärnings upprepning, men ofta behöver du träna och testa din modell flera gånger för att göra den mer exakt.

> [!div class="nextstepaction"]
> [Testa och träna om en modell](../../test-your-model.md)

* [Vad är Custom Vision?](../../overview.md)
* [Referensdokumentation för SDK](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/customvision?view=azure-python)