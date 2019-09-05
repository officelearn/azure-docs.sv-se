---
title: 'Snabbstart: Python SDK'
titleSuffix: Azure Cognitive Services
description: I den här snabb starten får du lära dig hur du använder python SDK för vanliga uppgifter, till exempel analysera bild, Hämta Beskrivning, identifiera text och generera miniatyr.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 09/03/2019
ms.author: pafarley
ms.openlocfilehash: bc42edc3e97aa68c5fe9d2b3162913e8925df4ee
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375644"
---
# <a name="azure-cognitive-services-computer-vision-sdk-for-python"></a>Azure Cognitive Services-SDK för visuellt innehåll och för Python

Via tjänsten Visuellt innehåll har utvecklare tillgång till avancerade algoritmer för bearbetning av bilder och returnering av information. Algoritmer för visuellt innehåll kan analysera innehållet i en bild på olika sätt beroende på vilka visuella egenskaper som du är intresserad av.

* [Analysera en bild](#analyze-an-image)
* [Hämta ämnesdomänlista](#get-subject-domain-list)
* [Analysera en bild efter domän](#analyze-an-image-by-domain)
* [Hämta textbeskrivning av en bild](#get-text-description-of-an-image)
* [Hämta handskriven text från en bild](#get-text-from-image)
* [Skapa en miniatyrbild](#generate-thumbnail)

Mer information om den här tjänsten finns i [Vad är visuellt innehåll?][computervision_docs].

Letar du efter mer dokumentation?

* [Referensdokumentation för SDK](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)
* [Dokumentation för Cognitive Services – Visuellt innehåll](https://docs.microsoft.com/azure/cognitive-services/computer-vision/)

## <a name="prerequisites"></a>Förutsättningar

* [Python 3.6+][python]
* Gratis [visuellt innehåll nyckel][computervision_resource] och associerad slut punkt. Du behöver dessa värden när du skapar instansen för [ComputerVisionClient][ref_computervisionclient] -klient-objektet. Använd en av följande metoder för att hämta dessa värden.

### <a name="if-you-dont-have-an-azure-subscription"></a>Om du inte har en Azure-prenumeration

Skapa en kostnads fri nyckel som är giltig i 7 dagar **[med den visuellt innehåll][computervision_resource]** tjänsten. När nyckeln har skapats kopierar du nyckeln och slut punkts namnet. Du behöver detta för att [skapa klienten](#create-client).

Spara följande när nyckeln har skapats:

* Nyckelvärdet: en sträng med 32 tecken med formatet `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`
* Nyckel slut punkt: bas slut punktens URL\:, https//westcentralus.API.Cognitive.Microsoft.com

### <a name="if-you-have-an-azure-subscription"></a>Om du har en Azure-prenumeration

Den enklaste metoden för att skapa en resurs i din prenumeration är att använda följande [Azure CLI][azure_cli] -kommando. Detta skapar en kognitiv tjänst nyckel som kan användas för många kognitiva tjänster. Du måste välja namnet på den _befintliga_ resurs gruppen, till exempel "My-cogserv-Group" och det nya resurs namnet för dator vision, till exempel "min-dator-vision-Resource".

```Bash
RES_REGION=westeurope
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

az cognitiveservices account create \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --location $RES_REGION \
    --kind CognitiveServices \
    --sku S0 \
    --yes
```

<!--
## Installation

Install the Azure Cognitive Services Computer Vision SDK with [pip][pip], optionally within a [virtual environment][venv].

### Configure a virtual environment (optional)

Although not required, you can keep your base system and Azure SDK environments isolated from one another if you use a [virtual environment][virtualenv]. Execute the following commands to configure and then enter a virtual environment with [venv][venv], such as `cogsrv-vision-env`:

```Bash
python3 -m venv cogsrv-vision-env
source cogsrv-vision-env/bin/activate
```
-->

### <a name="install-the-sdk"></a>Installera SDK:n

Installera Azure Cognitive Services Visuellt innehåll SDK för python- [paketet][pypi_computervision] med [pip][pip]:

```Bash
pip install azure-cognitiveservices-vision-computervision
```

## <a name="authentication"></a>Authentication

När du har skapat din Visuellt innehåll-resurs behöver du dess **slut punkt**och en av dess **konto nycklar** för att instansiera objektet klient.

Använd de här värdena när du skapar instansen för [ComputerVisionClient][ref_computervisionclient] -klient-objektet.

Använd till exempel bash-terminalen för att ange miljövariablerna:

```Bash
ACCOUNT_ENDPOINT=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>
```

### <a name="for-azure-subscription-users-get-credentials-for-key-and-endpoint"></a>För Azure-prenumerations användare, Hämta autentiseringsuppgifter för nyckel och slut punkt

Om du inte kommer ihåg din slut punkt och nyckel kan du använda följande metod för att hitta dem. Om du behöver skapa en nyckel och slut punkt kan du använda metoden för Azure- [prenumerations innehavare](#if-you-have-an-azure-subscription) eller för [användare utan en Azure-prenumeration](#if-you-dont-have-an-azure-subscription).

Använd [Azure CLI][cloud_shell] -kodfragmentet nedan för att fylla i två miljövariabler med visuellt innehåll-kontots **slut punkt** och en av dess **nycklar** (du kan också hitta värdena i [Azure Portal][azure_portal]). Kodfragmentet är formaterat för Bash-gränssnittet.

```Bash
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

export ACCOUNT_ENDPOINT=$(az cognitiveservices account show \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query endpoint \
    --output tsv)

export ACCOUNT_KEY=$(az cognitiveservices account keys list \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query key1 \
    --output tsv)
```


### <a name="create-client"></a>Skapa en klient

Hämta slut punkten och nyckeln från miljövariablerna och skapa sedan [ComputerVisionClient][ref_computervisionclient] -klient-objektet.

```Python
from azure.cognitiveservices.vision.computervision import ComputerVisionClient
from azure.cognitiveservices.vision.computervision.models import VisualFeatureTypes
from msrest.authentication import CognitiveServicesCredentials

# Get endpoint and key from environment variables
import os
endpoint = os.environ['ACCOUNT_ENDPOINT']
key = os.environ['ACCOUNT_KEY']

# Set credentials
credentials = CognitiveServicesCredentials(key)

# Create client
client = ComputerVisionClient(endpoint, credentials)
```

## <a name="examples"></a>Exempel

Du behöver ett [ComputerVisionClient][ref_computervisionclient] -klient objekt innan du använder någon av följande uppgifter.

### <a name="analyze-an-image"></a>Analysera en bild

Du kan analysera en avbildning för vissa funktioner i [`analyze_image`][ref_computervisionclient_analyze_image]. [`visual_features`][ref_computervision_model_visualfeatures] Använd egenskapen för att ange vilka typer av analyser som ska utföras på avbildningen. Vanliga värden är `VisualFeatureTypes.tags` och `VisualFeatureTypes.description`.

```Python
url = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/Broadway_and_Times_Square_by_night.jpg/450px-Broadway_and_Times_Square_by_night.jpg"

image_analysis = client.analyze_image(url,visual_features=[VisualFeatureTypes.tags])

for tag in image_analysis.tags:
    print(tag)
```

### <a name="get-subject-domain-list"></a>Hämta en ämnesdomänlista

Granska ämnes domänerna som används för att analysera din [`list_models`][ref_computervisionclient_list_models]avbildning med. Dessa domännamn används när [en bild analyseras av domänen](#analyze-an-image-by-domain). Ett exempel på en domän är `landmarks`.

```Python
models = client.list_models()

for x in models.models_property:
    print(x)
```

### <a name="analyze-an-image-by-domain"></a>Analysera en bild efter domän

Du kan analysera en avbildning efter ämnes domän med [`analyze_image_by_domain`][ref_computervisionclient_analyze_image_by_domain]. Hämta [listan med ämnesdomäner som stöds](#get-subject-domain-list) för att kunna använda rätt domännamn.

```Python
# type of prediction
domain = "landmarks"

# Public domain image of Eiffel tower
url = "https://images.pexels.com/photos/338515/pexels-photo-338515.jpeg"

# English language response
language = "en"

analysis = client.analyze_image_by_domain(domain, url, language)

for landmark in analysis.result["landmarks"]:
    print(landmark["name"])
    print(landmark["confidence"])
```

### <a name="get-text-description-of-an-image"></a>Hämta en textbeskrivning av en bild

Du kan hämta en språk-baserad text Beskrivning av en bild med [`describe_image`][ref_computervisionclient_describe_image]. Begär flera beskrivningar med `max_description`-egenskapen om du genomför textanalys för nyckelord som associeras med bilden. Exempel på en textbeskrivning för följande bild är `a train crossing a bridge over a body of water`, `a large bridge over a body of water` och `a train crossing a bridge over a large body of water`.

```Python
domain = "landmarks"
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"
language = "en"
max_descriptions = 3

analysis = client.describe_image(url, max_descriptions, language)

for caption in analysis.captions:
    print(caption.text)
    print(caption.confidence)
```

### <a name="get-text-from-image"></a>Hämta text från bilden

Du kan hämta handskriven eller tryckt text från en bild. Detta kräver två anrop till SDK: n [`batch_read_file`](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python) : [`get_read_operation_result`](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python)och. Anropet till `batch_read_file` är asynkront. I resultatet av `get_read_operation_result` anropet måste du kontrol lera om det första anropet slutfördes med [`TextOperationStatusCodes`][ref_computervision_model_textoperationstatuscodes] innan du extraherar text data. Resultatet är texten samt omgivande koordinater för textens avgränsningsfält.

```Python
# import models
from azure.cognitiveservices.vision.computervision.models import TextOperationStatusCodes
import time

url = "https://azurecomcdn.azureedge.net/cvt-1979217d3d0d31c5c87cbd991bccfee2d184b55eeb4081200012bdaf6a65601a/images/shared/cognitive-services-demos/read-text/read-1-thumbnail.png"
raw = True
custom_headers = None
numberOfCharsInOperationId = 36

# Async SDK call
rawHttpResponse = client.batch_read_file(url, custom_headers,  raw)

# Get ID from returned headers
operationLocation = rawHttpResponse.headers["Operation-Location"]
idLocation = len(operationLocation) - numberOfCharsInOperationId
operationId = operationLocation[idLocation:]

# SDK call
while True:
    result = client.get_read_operation_result(operationId)
    if result.status not in ['NotStarted', 'Running']:
        break
    time.sleep(1)

# Get data
if result.status == TextOperationStatusCodes.succeeded:
    for textResult in result.recognition_results:
        for line in textResult.lines:
            print(line.text)
            print(line.bounding_box)
```

### <a name="generate-thumbnail"></a>Skapa en miniatyrbild

Du kan generera en miniatyr bild (JPG) för en bild [`generate_thumbnail`][ref_computervisionclient_generate_thumbnail]med. Miniatyren behöver inte ha samma proportioner som den ursprungliga bilden.

Installera **Pillow** för att använda det här exemplet:

```bash
pip install Pillow
```

När Pillow har installerats använder du paketet i följande kodexempel för att generera miniatyrbilden.

```Python
# Pillow package
from PIL import Image

# IO package to create local image
import io

width = 50
height = 50
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"

thumbnail = client.generate_thumbnail(width, height, url)

for x in thumbnail:
    image = Image.open(io.BytesIO(x))

image.save('thumbnail.jpg')
```

## <a name="troubleshooting"></a>Felsökning

### <a name="general"></a>Allmänt

När du interagerar med [ComputerVisionClient][ref_computervisionclient] -klientprogrammet med python SDK, [`ComputerVisionErrorException`][ref_computervision_computervisionerrorexception] används klassen för att returnera fel. Fel som returneras av tjänsten motsvarar samma HTTP-statuskoder som returneras för REST API-begäranden.

Om du exempelvis försöker analysera en bild med en ogiltig nyckel, returneras felet `401`. I följande kodfragment hanteras [felet][ref_httpfailure] smidigt genom att undantags felet och ytterligare information om felet visas.

```Python

domain = "landmarks"
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"
language = "en"
max_descriptions = 3

try:
    analysis = client.describe_image(url, max_descriptions, language)

    for caption in analysis.captions:
        print(caption.text)
        print(caption.confidence)
except HTTPFailure as e:
    if e.status_code == 401:
        print("Error unauthorized. Make sure your key and endpoint are correct.")
    else:
        raise
```

### <a name="handle-transient-errors-with-retries"></a>Hantera tillfälliga fel med återförsök

När du arbetar med [ComputerVisionClient][ref_computervisionclient] -klienten kan du stöta på tillfälliga fel som orsakas av de [hastighets begränsningar][computervision_request_units] som tillämpas av tjänsten eller andra övergående problem som nätverks avbrott. Information om hur du hanterar dessa typer av fel finns i [mönster för återförsök][azure_pattern_retry] i design mönster guiden för molnet och det relaterade [krets brytar mönstret][azure_pattern_circuit_breaker].

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Applicera innehållstaggar på bilder](../concept-tagging-images.md)

<!-- LINKS -->
[pip]: https://pypi.org/project/pip/
[python]: https://www.python.org/downloads/

[azure_cli]: https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create
[azure_pattern_circuit_breaker]: https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker
[azure_pattern_retry]: https://docs.microsoft.com/azure/architecture/patterns/retry
[azure_portal]: https://portal.azure.com
[azure_sub]: https://azure.microsoft.com/free/

[cloud_shell]: https://docs.microsoft.com/azure/cloud-shell/overview

[venv]: https://docs.python.org/3/library/venv.html
[virtualenv]: https://virtualenv.pypa.io

[source_code]: https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-vision-computervision

[pypi_computervision]:https://pypi.org/project/azure-cognitiveservices-vision-computervision/
[pypi_pillow]:https://pypi.org/project/Pillow/

[ref_computervision_sdk]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision?view=azure-python
[ref_computervision_computervisionerrorexception]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.computervisionerrorexception?view=azure-python
[ref_httpfailure]: https://docs.microsoft.com/python/api/msrest/msrest.exceptions.httpoperationerror?view=azure-python


[computervision_resource]: https://azure.microsoft.com/try/cognitive-services/?

[computervision_docs]: https://docs.microsoft.com/azure/cognitive-services/computer-vision/home

[ref_computervisionclient]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_analyze_image]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_list_models]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_analyze_image_by_domain]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_describe_image]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_get_text_operation_result]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_generate_thumbnail]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python


[ref_computervision_model_visualfeatures]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-python

[ref_computervision_model_textoperationstatuscodes]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.textoperationstatuscodes?view=azure-python

[computervision_request_units]:https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/
