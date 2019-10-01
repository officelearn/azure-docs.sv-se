---
title: 'Snabbstart: Visuellt innehåll klient bibliotek för python | Microsoft Docs'
description: Kom igång med Visuellt innehåll klient biblioteket för python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 09/10/2019
ms.author: pafarley
ms.openlocfilehash: c0a11c90b59c1c475f80b82073c6b151d57535c3
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709792"
---
# <a name="quickstart-computer-vision-client-library-for-python"></a>Snabbstart: Visuellt innehåll klient bibliotek för python

Via tjänsten Visuellt innehåll har utvecklare tillgång till avancerade algoritmer för bearbetning av bilder och returnering av information. Algoritmer för visuellt innehåll kan analysera innehållet i en bild på olika sätt beroende på vilka visuella egenskaper som du är intresserad av.

Använd Visuellt innehåll klient bibliotek för python för att:

* Analysera en bild för taggar, text beskrivning, ansikten, vuxen innehåll med mera.
* Identifiera skriven text skriven och handskriven text med API: t batch Read.

> [!NOTE]
> I scenarierna i den här snabb starten används URL: er för fjärravbildningen. Exempel kod som utför samma åtgärder på lokala avbildningar finns i koden på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py).

[Referens dokumentation](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision) | [bibliotek käll kods](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-computervision) | [paket (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-computervision/) | [exempel](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-computer-vision-azure-resource"></a>Skapa en Visuellt innehåll Azure-resurs

Azure-Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för Visuellt innehåll med hjälp av [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på den lokala datorn. Du kan också:

* Få en [utvärderings nyckel](https://azure.microsoft.com/try/cognitive-services/#decision) som är giltig i sju dagar utan kostnad. När du har registrerat dig kommer den att vara tillgänglig på [Azure-webbplatsen](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Visa din resurs på [Azure Portal](https://portal.azure.com/)

När du har fått en nyckel från din utvärderings prenumeration eller resurs [skapar du miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckel-och slut punkts `COMPUTER_VISION_ENDPOINT`-URL: en, med namnet `COMPUTER_VISION_SUBSCRIPTION_KEY` respektive.
 
### <a name="create-a-new-python-application"></a>Skapa ett nytt Python-program

Skapa ett nytt python-&mdash;skript*QuickStart-File.py*, till exempel. Öppna den sedan i önskat redigerings program eller IDE och importera följande bibliotek.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_imports)]

Skapa sedan variabler för resursens Azure-slutpunkt och nyckel.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_vars)]

> [!NOTE]
> Om du har skapat miljövariabeln efter att du har startat programmet måste du stänga och öppna redigerings programmet, IDE eller gränssnittet som kör det för att få åtkomst till variabeln.

### <a name="install-the-client-library"></a>Installera klient biblioteket

Du kan installera klient biblioteket med:

```console
pip install --upgrade azure-cognitiveservices-vision-computervision
```

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Visuellt innehåll python SDK.

|Name|Beskrivning|
|---|---|
|[ComputerVisionClientOperationsMixin](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin?view=azure-python)| Den här klassen hanterar alla avbildnings åtgärder direkt, till exempel bild analys, text identifiering och generering av miniatyrer.|
| [ComputerVisionClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python) | Den här klassen krävs för alla Visuellt innehåll-funktioner. Du instansierar det med din prenumerations information och använder den för att skapa instanser av andra klasser. Den implementerar **ComputerVisionClientOperationsMixin**.|
|[VisualFeatureTypes](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-python)| Den här uppräkningen definierar de olika typerna av bild analys som kan göras i en standard analys åtgärd. Du anger en uppsättning **VisualFeatureTypes** -värden beroende på dina behov. |

## <a name="code-examples"></a>Kod exempel

De här kodfragmenten visar hur du utför följande uppgifter med Visuellt innehåll klient biblioteket för python:

* [Autentisera klienten](#authenticate-the-client)
* [Analysera en bild](#analyze-an-image)
* [Skriv ut och handskriven text](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autentisera klienten

> [!NOTE]
> Den här snabb starten förutsätter att du har [skapat en miljö variabel](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) för din `COMPUTER_VISION_SUBSCRIPTION_KEY`visuellt innehåll nyckel, med namnet.

Instansiera en klient med din slut punkt och nyckel. Skapa ett [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) -objekt med din nyckel och Använd den med slut punkten för att skapa ett [ComputerVisionClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python) -objekt.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_client)]

## <a name="analyze-an-image"></a>Analysera en bild

Spara en referens till URL: en för en bild som du vill analysera.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_remoteimage)]

### <a name="get-image-description"></a>Beskrivning av Hämta avbildning

Följande kod hämtar listan över genererade under texter för avbildningen. Se [Beskriv avbildningar](../concept-describing-images.md) för mer information.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_describe)]

### <a name="get-image-category"></a>Hämta bild kategori

Följande kod hämtar den identifierade kategorin för avbildningen. Se [kategorisera bilder](../concept-categorizing-images.md) för mer information.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_categorize)]

### <a name="get-image-tags"></a>Hämta bild etiketter

Följande kod hämtar en uppsättning identifierade Taggar i avbildningen. Se [innehålls etiketter](../concept-tagging-images.md) för mer information.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_tags)]

### <a name="detect-objects"></a>Identifiera objekt

Följande kod identifierar vanliga objekt i avbildningen och skriver ut dem till-konsolen. Mer information finns i [objekt identifiering](../concept-object-detection.md) .

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_objects)]

### <a name="detect-brands"></a>Identifiera varumärken

Följande kod identifierar företags märken och logo typer i bilden och skriver ut dem till-konsolen. Mer information finns i [varumärkes identifiering](../concept-brand-detection.md) .

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_brands)]

### <a name="detect-faces"></a>Identifiera ansikten

Följande kod returnerar identifierade ansikten i bilden med deras Rectangle-koordinater och välj ansikts attribut. Mer information finns i [ansikts igenkänning](../concept-detecting-faces.md) .

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_faces)]

### <a name="detect-adult-or-racy-content"></a>Identifiera vuxen eller vågat innehåll

Följande kod skriver ut den identifierade förekomsten av vuxen eller vågat innehåll i avbildningen. Se [vuxen och vågatt innehåll](../concept-detecting-adult-content.md) för mer information.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Hämta färg schema för bild

Följande kod skriver ut de identifierade färgattributen i bilden, till exempel dominerande färger och dekor färg. Se [färg scheman](../concept-detecting-color-schemes.md) för mer information.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Hämta domänbaserat innehåll

Visuellt innehåll kan använda specialiserad modell för att utföra ytterligare analyser av avbildningar. Se [domänbaserat innehåll](../concept-detecting-domain-content.md) för mer information. 

Följande kod tolkar data om identifierade kändisar i avbildningen.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_celebs)]

Följande kod tolkar data om identifierade landmärken i bilden.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Hämta avbildnings typen

Följande kod skriver ut information om typen av bild&mdash;oavsett om den är en ClipArt-eller linje ritning.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Skriv ut och handskriven text

Visuellt innehåll kan läsa synlig text i en bild och konvertera den till en tecken ström. Du gör detta i två delar.

### <a name="call-the-read-api"></a>Anropa Read API

Använd först följande kod för att anropa **batch_read_file** -metoden för den aktuella avbildningen. Detta returnerar ett åtgärds-ID och startar en asynkron process för att läsa innehållet i avbildningen.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_call)]

### <a name="get-read-results"></a>Hämta Läs resultat

Hämta sedan det åtgärds-ID som returnerades från **batch_read_file** -anropet och Använd det för att fråga tjänsten efter åtgärds resultat. Följande kod kontrollerar åtgärden vid en sekunds intervall tills resultatet returneras. Den skriver sedan ut de extraherade text data till-konsolen.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_response)]

## <a name="run-the-application"></a>Köra programmet

Kör programmet med `python` kommandot på snabb starts filen.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)


## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du använder Visuellt innehåll-biblioteket för python för att utföra grundläggande uppgifter. Sedan kan du utforska referens dokumentationen och läsa mer om biblioteket.


> [!div class="nextstepaction"]
>[API för visuellt innehåll referens (python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)

* [Vad är API för visuellt innehåll?](../Home.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py).
