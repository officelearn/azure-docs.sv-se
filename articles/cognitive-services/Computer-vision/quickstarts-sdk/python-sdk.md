---
title: 'Snabbstart: Python SDK'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten får du lära dig hur du använder Python SDK för vanliga uppgifter, till exempel analysera bild, beskrivning av Erhåll, identifiera text och generera miniatyrbilden.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: ce7e8788ec807c8ceccb49a7d435041b34e75348
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917083"
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

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Python 3.6+][python]
* Kostnadsfria [visuellt nyckeln] [ computervision_resource] och associerade slutpunkten. Du behöver dessa värden när du skapar en instans av den [ComputerVisionClient] [ ref_computervisionclient] klientobjektet. Använd en av följande metoder för att hämta dessa värden.

### <a name="if-you-dont-have-an-azure-subscription"></a>Om du inte har en Azure-prenumeration

Skapa en kostnadsfri nyckel giltigt i 7 dagar med den **[prova] [ computervision_resource]** för tjänsten för visuellt innehåll. När nyckeln skapas, kopierar du nyckel och slutpunkt-namnet. Du behöver detta för att [skapa klienten](#create-client).

Spara följande när nyckeln har skapats:

* Nyckelvärdet: en sträng med 32 tecken med formatet `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`
* Viktiga slutpunkt: basslutpunktens Webbadress, https\://westcentralus.api.cognitive.microsoft.com

### <a name="if-you-have-an-azure-subscription"></a>Om du har en Azure-prenumeration

Det enklaste sättet att skapa en resurs i din prenumeration är att använda följande [Azure CLI] [ azure_cli] kommando. Detta skapar en Cognitive Service-nyckel som kan användas i många kognitiva tjänster. Du måste välja den _befintliga_ resursgruppens namn, till exempel ”min cogserv-grupp” och den nya dator visionen resursen namnet, till exempel ”min dator-vision-resurs”.

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

Installera Azure Cognitive Services-SDK för visuellt innehåll för Python-[paket][pypi_computervision] med [pip][pip]:

```Bash
pip install azure-cognitiveservices-vision-computervision
```

## <a name="authentication"></a>Autentisering

När du skapar resursen för visuellt innehåll, måste dess **endpoint**, och en av dess **kontonycklar** att skapa en instans av klientobjektet.

Genom att använda dessa värden när du skapar en instans av den [ComputerVisionClient] [ ref_computervisionclient] klientobjektet.

Exempelvis kan du använda Bash-terminalen för att ställa in miljövariablerna som:

```Bash
ACCOUNT_ENDPOINT=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>
```

### <a name="for-azure-subscription-users-get-credentials-for-key-and-endpoint"></a>Hämta autentiseringsuppgifter för nyckel och slutpunkt för Azure-prenumerationsanvändare

Om du inte kommer ihåg din slutpunkt och nyckel, kan du använda följande metod för att hitta dem. Om du vill skapa en nyckel och slutpunkt kan du använda metoden för [innehavare av Azure-prenumeration](#if-you-have-an-azure-subscription) eller för [användare utan en Azure-prenumeration](#if-you-dont-have-an-azure-subscription).

Använd den [Azure CLI] [ cloud_shell] kodavsnittet nedan för att fylla i två miljövariabler med kontot visuellt **endpoint** och en av dess **nycklar**(du hittar även dessa värden i den [Azure-portalen][azure_portal]). Kodfragmentet är formaterat för Bash-gränssnittet.

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

Hämta slutpunkten och nyckeln från miljövariabler och skapa sedan den [ComputerVisionClient] [ ref_computervisionclient] klientobjektet.

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

Du behöver en [ComputerVisionClient] [ ref_computervisionclient] klientobjektet innan du använder någon av följande uppgifter.

### <a name="analyze-an-image"></a>Analysera en bild

Du kan analysera en bild för vissa funktioner med [`analyze_image`][ref_computervisionclient_analyze_image]. Använd [`visual_features`][ref_computervision_model_visualfeatures]-egenskapen för att ange vilka typer av analyser som ska utföras på bilden. Vanliga värden är `VisualFeatureTypes.tags` och `VisualFeatureTypes.description`.

```Python
url = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/Broadway_and_Times_Square_by_night.jpg/450px-Broadway_and_Times_Square_by_night.jpg"

image_analysis = client.analyze_image(url,visual_features=[VisualFeatureTypes.tags])

for tag in image_analysis.tags:
    print(tag)
```

### <a name="get-subject-domain-list"></a>Hämta en ämnesdomänlista

Granska de ämnesdomäner som används för att analysera bilden med [`list_models`][ref_computervisionclient_list_models]. Dessa domännamn används när [en bild analyseras av domänen](#analyze-an-image-by-domain). Ett exempel på en domän är `landmarks`.

```Python
models = client.list_models()

for x in models.models_property:
    print(x)
```

### <a name="analyze-an-image-by-domain"></a>Analysera en bild efter domän

Du kan analysera en bild av ämnesdomänen med [`analyze_image_by_domain`][ref_computervisionclient_analyze_image_by_domain]. Hämta [listan med ämnesdomäner som stöds](#get-subject-domain-list) för att kunna använda rätt domännamn.

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

Du kan få en språkbaserad textbeskrivning av en bild med [`describe_image`][ref_computervisionclient_describe_image]. Begär flera beskrivningar med `max_description`-egenskapen om du genomför textanalys för nyckelord som associeras med bilden. Exempel på en textbeskrivning för följande bild är `a train crossing a bridge over a body of water`, `a large bridge over a body of water` och `a train crossing a bridge over a large body of water`.

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

Du kan hämta handskriven eller tryckt text från en bild. Detta kräver två anrop till SDK: [ `batch_read_file` ](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python#batch-read-file-url--mode--custom-headers-none--raw-false----operation-config-) och [ `get_read_operation_result` ](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python#get-read-operation-result-operation-id--custom-headers-none--raw-false----operation-config-). Anropet till `batch_read_file` är asynkron. I resultatet av den `get_read_operation_result` anrop, måste du kontrollera om det första anropet slutfördes med [ `TextOperationStatusCodes` ] [ ref_computervision_model_textoperationstatuscodes] innan du extrahera textdata. Resultatet är texten samt omgivande koordinater för textens avgränsningsfält.

```Python
# import models
from azure.cognitiveservices.vision.computervision.models import TextRecognitionMode
from azure.cognitiveservices.vision.computervision.models import TextOperationStatusCodes
import time

url = "https://azurecomcdn.azureedge.net/cvt-1979217d3d0d31c5c87cbd991bccfee2d184b55eeb4081200012bdaf6a65601a/images/shared/cognitive-services-demos/read-text/read-1-thumbnail.png"
mode = TextRecognitionMode.handwritten
raw = True
custom_headers = None
numberOfCharsInOperationId = 36

# Async SDK call
rawHttpResponse = client.batch_read_file(url, mode, custom_headers,  raw)

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

Du kan skapa en miniatyrbild (JPG) av en bild med [`generate_thumbnail`][ref_computervisionclient_generate_thumbnail]. Miniatyren behöver inte ha samma proportioner som den ursprungliga bilden.

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

När du interagerar med den [ComputerVisionClient] [ ref_computervisionclient] klientobjektet med hjälp av Python-SDK i [ `ComputerVisionErrorException` ] [ ref_computervision_computervisionerrorexception] klassen används att returnera fel. Fel som returneras av tjänsten motsvarar samma HTTP-statuskoder som returneras för REST API-begäranden.

Om du exempelvis försöker analysera en bild med en ogiltig nyckel, returneras felet `401`. I följande kodfragment hanteras [felet][ref_httpfailure] korrekt genom att fånga upp undantaget och visa ytterligare information om felet.

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

När du arbetar med den [ComputerVisionClient] [ ref_computervisionclient] klienten, som kan uppstå tillfälliga fel som orsakas av [hastighetsbegränsningar] [ computervision_request_units] tvingande av tjänsten eller andra tillfälliga problem angående avbrott i nätverket. Information om hur du hanterar dessa typer av fel finns i [Återförsöksmönster][azure_pattern_retry] i guiden för molndesignmönster och relaterade [Kretsbrytarmönster][azure_pattern_circuit_breaker].

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
