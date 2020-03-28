---
title: 'Snabbstart: Client Library för Computer Vision för Python'
description: Kom igång med client library för Computer Vision för Python med den här snabbstarten.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 5f552dfc320459d23e538e65e5f34c085f5b5ad9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80136009"
---
<a name="HOLTop"></a>

[Referensdokumentation](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision) | [Ursprungskod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-computervision) | [Paket (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-computervision/) | [Prover](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [Skapa en gratis](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Inrätta

### <a name="create-a-computer-vision-azure-resource"></a>Skapa en Azure-resurs för datorseende

Azure Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för Datorseende med Hjälp av [Azure-portalen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på din lokala dator. Du kan också:

* Få en [testnyckel](https://azure.microsoft.com/try/cognitive-services/#decision) giltig i sju dagar gratis. När du har registrerat dig är den tillgänglig på [Azure-webbplatsen](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Visa din resurs på [Azure-portalen](https://portal.azure.com/)

När du har fått en nyckel från utvärderingsprenumerationen eller resursen `COMPUTER_VISION_ENDPOINT`skapar du [miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckel- och slutpunkts-URL:en, med namn `COMPUTER_VISION_SUBSCRIPTION_KEY` respektive , respektive.
 
### <a name="create-a-new-python-application"></a>Skapa ett nytt Python-program

Skapa ett nytt&mdash;Python-skript*quickstart-file.py*, till exempel. Öppna den sedan i önskad redigerare eller IDE och importera följande bibliotek.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_imports)]

Skapa sedan variabler för resursens Azure-slutpunkt och nyckel.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_vars)]

> [!NOTE]
> Om du har skapat miljövariabeln efter att du har startat programmet måste du stänga och öppna redigeraren, IDE:t eller skalet som kör den för att komma åt variabeln.

### <a name="install-the-client-library"></a>Installera klientbiblioteket

Du kan installera klientbiblioteket med:

```console
pip install --upgrade azure-cognitiveservices-vision-computervision
```

## <a name="object-model"></a>Objektmodell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Computer Vision Python SDK.

|Namn|Beskrivning|
|---|---|
|[ComputerVisionClientOperationsMixin](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin?view=azure-python)| Den här klassen hanterar direkt alla bildåtgärder, till exempel bildanalys, textidentifiering och miniatyrgenerering.|
| [ComputerVisionClient (Dator)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python) | Den här klassen behövs för alla datorseendefunktioner. Du instansierar den med din prenumerationsinformation och använder den för att producera instanser av andra klasser. Den implementerar **ComputerVisionClientOperationsMixin**.|
|[VisualFeatureTypes](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-python)| Den här uppräkningen definierar de olika typer av bildanalys som kan göras i en standardanalysåtgärd. Du anger en uppsättning **VisualFeatureTypes-värden** beroende på dina behov. |

## <a name="code-examples"></a>Kodexempel

Dessa kodavsnitt visar hur du utför följande uppgifter med klientbiblioteket Computer Vision för Python:

* [Autentisera klienten](#authenticate-the-client)
* [Analysera en bild](#analyze-an-image)
* [Läsa tryckt och handskriven text](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autentisera klienten

> [!NOTE]
> Den här snabbstarten förutsätter att du har skapat `COMPUTER_VISION_SUBSCRIPTION_KEY`en [miljövariabel](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) för nyckeln Datorseende med namnet .

Instansiera en klient med din slutpunkt och nyckel. Skapa ett [CognitiveServicesCredentials-objekt](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) med nyckeln och använd det med slutpunkten för att skapa ett [ComputerVisionClient-objekt.](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python)

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_client)]

## <a name="analyze-an-image"></a>Analysera en bild

Spara en referens till url:en för en bild som du vill analysera.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_remoteimage)]

### <a name="get-image-description"></a>Få bildbeskrivning

Följande kod hämtar listan över genererade bildtexter för bilden. Mer information finns i [Beskriv bilder.](../../concept-describing-images.md)

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_describe)]

### <a name="get-image-category"></a>Hämta bildkategori

Följande kod får den identifierade kategorin av bilden. Mer information [finns i Kategorisera bilder.](../../concept-categorizing-images.md)

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_categorize)]

### <a name="get-image-tags"></a>Hämta bildtaggar

Följande kod hämtar uppsättningen identifierade taggar i bilden. Mer information [finns i Innehållstaggar.](../../concept-tagging-images.md)

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_tags)]

### <a name="detect-objects"></a>Upptäcka objekt

Följande kod identifierar vanliga objekt i bilden och skriver ut dem på konsolen. Mer information [finns i Objektidentifiering.](../../concept-object-detection.md)

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_objects)]

### <a name="detect-brands"></a>Identifiera varumärken

Följande kod identifierar företagets varumärken och logotyper i bilden och skriver ut dem på konsolen. Mer information [finns i Varumärkesidentifiering.](../../concept-brand-detection.md)

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_brands)]

### <a name="detect-faces"></a>Identifiera ansikten

Följande kod returnerar de identifierade ytorna i bilden med sina rektangelkoordinater och väljer ansiktsattribut. Mer information finns i [Ansiktsigenkänning.](../../concept-detecting-faces.md)

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Identifiera innehåll för vuxna, racy eller blodiga

Följande kod skriver ut den upptäckta förekomsten av sexuellt innehåll i bilden. Se [Vuxen, racy, blodiga innehåll](../../concept-detecting-adult-content.md) för mer information.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Hämta bildfärgschema

Följande kod skriver ut de identifierade färgattributen i bilden, till exempel de dominerande färgerna och accentfärgen. Se [Färgscheman](../../concept-detecting-color-schemes.md) för mer information.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Hämta domänspecifikt innehåll

Computer Vision kan använda specialiserad modell för att göra ytterligare analys på bilder. Mer information [finns i Domänspecifikt innehåll.](../../concept-detecting-domain-content.md) 

Följande kod tolkar data om identifierade kändisar i bilden.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_celebs)]

Följande kod tolkar data om identifierade landmärken i bilden.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Hämta bildtypen

Följande kod skriver ut information om&mdash;vilken typ av bild det är ClipArt eller linjeritning.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Läsa tryckt och handskriven text

Computer Vision kan läsa synlig text i en bild och konvertera den till en teckenström. Du gör detta i två delar.

### <a name="call-the-read-api"></a>Anropa läs-API:et

Använd först följande kod för att anropa **batch_read_file-metoden** för den givna bilden. Detta returnerar ett åtgärds-ID och startar en asynkron process för att läsa bildens innehåll.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_call)]

### <a name="get-read-results"></a>Få läsresultat

Hämta sedan åtgärds-ID:et som returneras från batch_read_file-anropet och använda det för att fråga tjänsten efter åtgärdsresultat. **batch_read_file** Följande kod kontrollerar åtgärden med en sekunds mellanrum tills resultaten returneras. Sedan skrivs de extraherade textdata ut till konsolen.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_response)]

## <a name="run-the-application"></a>Köra appen

Kör programmet med `python` kommandot på snabbstartsfilen.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services-prenumeration kan du ta bort resursen eller resursgruppen. Om du tar bort resursgruppen tas även alla andra resurser som är associerade bort.

* [Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)


## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du använder datorseendebiblioteket för Python för att göra basuppgifter. Därefter kan du utforska referensdokumentationen om du vill veta mer om biblioteket.


> [!div class="nextstepaction"]
>[API-referens för datorseende (Python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)

* [Vad är visuellt innehåll?](../../Home.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py).
