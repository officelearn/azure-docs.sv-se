---
title: 'Snabbstart: Visuellt innehåll klient bibliotek för Node. js | Microsoft Docs'
description: Kom igång med Visuellt innehåll klient biblioteket för Node. js...
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: pafarley
ms.openlocfilehash: a51a8e02ec08b9a4ee353c2c45345c4d01246894
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981833"
---
# <a name="quickstart-computer-vision-client-library-for-nodejs"></a>Snabbstart: Visuellt innehåll klient bibliotek för Node. js

Kom igång med Visuellt innehåll klient biblioteket för Node. js. Följ de här stegen för att installera paketet och prova exempel koden för grundläggande uppgifter. 

Använd Visuellt innehåll klient bibliotek för Node. js för att:

* [Analysera en bild](#analyze-an-image)
* [Skriv ut och handskriven text](#read-printed-and-handwritten-text)

[Referens dokumentation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest) | [biblioteks käll kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-computervision) | -[paket (NPM)](https://www.npmjs.com/package/azure-cognitiveservices-computervision) | [exempel](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* Den aktuella versionen av [Node. js](https://nodejs.org/)

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-computer-vision-azure-resource"></a>Skapa en Visuellt innehåll Azure-resurs

Azure-Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för Visuellt innehåll med hjälp av [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på den lokala datorn. Du kan också:

* Få en [utvärderings nyckel](https://azure.microsoft.com/try/cognitive-services/#decision) som är giltig i sju dagar utan kostnad. När du har registrerat dig kommer den att vara tillgänglig på [Azure-webbplatsen](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Visa din resurs på [Azure Portal](https://portal.azure.com/).

När du har fått en nyckel från din utvärderings prenumeration eller resurs [skapar du miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckel-och slut punkts-URL: en med namnet `COMPUTER_VISION_SUBSCRIPTION_KEY` respektive `COMPUTER_VISION_ENDPOINT`.
 
### <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js-program

I ett konsol fönster (till exempel cmd, PowerShell eller bash) skapar du en ny katalog för din app och navigerar till den. 

```console
mkdir myapp && cd myapp
```

Kör kommandot `npm init` för att skapa ett Node-program med en `package.json`-fil. 

```console
npm init
```

### <a name="install-the-client-library"></a>Installera klient biblioteket

Installera `ms-rest-azure`-och `azure-cognitiveservices-computervision`-NPM-paket:

```console
npm install azure-cognitiveservices-computervision ms-rest-azure
```

Appens `package.json`-fil kommer att uppdateras med beroenden.

### <a name="prepare-the-nodejs-script"></a>Förbereda Node. js-skriptet

Skapa en ny fil, *index. js*och öppna den i en text redigerare. Lägg till följande import uttryck.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imports)]

Definiera sedan en funktion `computerVision` och deklarera en asynkron serie med primär funktion och callback-funktion. Du kommer att lägga till snabb starts koden i den primära funktionen och anropa `computerVision` längst ned i skriptet.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_begin)]

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_end)]

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Visuellt innehåll Node. js SDK.

|Name|Beskrivning|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest) | Den här klassen krävs för alla Visuellt innehåll-funktioner. Du instansierar det med din prenumerations information och använder den för att utföra de flesta avbildnings åtgärder.|
|[VisualFeatureTypes](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/visualfeaturetypes?view=azure-node-latest)| Den här uppräkningen definierar de olika typerna av bild analys som kan göras i en standard analys åtgärd. Du anger en uppsättning **VisualFeatureTypes** -värden beroende på dina behov. |

## <a name="code-examples"></a>Kod exempel

De här kodfragmenten visar hur du gör följande uppgifter med Visuellt innehåll-klient biblioteket för Node. js:

* [Autentisera klienten](#authenticate-the-client)
* [Analysera en bild](#analyze-an-image)
* [Skriv ut och handskriven text](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autentisera klienten

Skapa variabler för resursens Azure-slutpunkt och nyckel. Om du har skapat miljövariabeln efter att du har startat programmet måste du stänga och öppna redigerings programmet, IDE eller gränssnittet som kör det för att få åtkomst till variabeln.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_vars)]

Instansiera en klient med din slut punkt och nyckel. Skapa ett [ApiKeyCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.apikeycredentials?view=azure-python) -objekt med din nyckel och slut punkt och Använd det för att skapa ett [ComputerVisionClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest) -objekt.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_client)]

## <a name="analyze-an-image"></a>Analysera en bild

Koden i det här avsnittet analyserar fjärravbildningar för att extrahera olika visuella funktioner. Du kan utföra dessa åtgärder som en del av **analyzeImage** -metoden för klient objekt, eller så kan du anropa dem med hjälp av enskilda metoder. Mer information finns i [referens dokumentationen](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest) .

> [!NOTE]
> Du kan också analysera en lokal avbildning. Se exempel koden på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) för scenarier som involverar lokala avbildningar.

### <a name="get-image-description"></a>Beskrivning av Hämta avbildning

Följande kod hämtar listan över genererade under texter för avbildningen. Se [Beskriv avbildningar](../concept-describing-images.md) för mer information.

Definiera först URL: en för en bild som ska analyseras:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe_image)]

Lägg sedan till följande kod för att hämta avbildnings beskrivningen och skriva ut den till-konsolen.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe)]

### <a name="get-image-category"></a>Hämta bild kategori

Följande kod hämtar den identifierade kategorin för avbildningen. Se [kategorisera bilder](../concept-categorizing-images.md) för mer information.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories)]

Definiera hjälp funktionen `formatCategories`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories_format)]

### <a name="get-image-tags"></a>Hämta bild etiketter

Följande kod hämtar en uppsättning identifierade Taggar i avbildningen. Se [innehålls etiketter](../concept-tagging-images.md) för mer information.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tags)]

Definiera hjälp funktionen `formatTags`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tagsformat)]

### <a name="detect-objects"></a>Identifiera objekt

Följande kod identifierar vanliga objekt i avbildningen och skriver ut dem till-konsolen. Mer information finns i [objekt identifiering](../concept-object-detection.md) .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objects)]

Definiera hjälp funktionen `formatRectObjects`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objectformat)]

### <a name="detect-brands"></a>Identifiera varumärken

Följande kod identifierar företags märken och logo typer i bilden och skriver ut dem till-konsolen. Mer information finns i [varumärkes identifiering](../concept-brand-detection.md) .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_brands)]

### <a name="detect-faces"></a>Identifiera ansikten

Följande kod returnerar identifierade ansikten i bilden med deras Rectangle-koordinater och välj ansikts attribut. Mer information finns i [ansikts igenkänning](../concept-detecting-faces.md) .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_faces)]

Definiera hjälp funktionen `formatRectFaces`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_formatfaces)]

### <a name="detect-adult-racy-or-gory-content"></a>Identifiera vuxna, vågat eller fullständig innehåll

Följande kod skriver ut den identifierade förekomsten av olämpligt innehåll i bilden. Mer information finns i [vuxen, vågat, fullständig-innehåll](../concept-detecting-adult-content.md) .

Definiera URL: en för den bild som ska användas:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult_image)] 

Lägg sedan till följande kod för att identifiera innehåll som är vuxna och skriv ut resultatet till-konsolen.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult)] 

### <a name="get-image-color-scheme"></a>Hämta färg schema för bild

Följande kod skriver ut de identifierade färgattributen i bilden, till exempel dominerande färger och dekor färg. Se [färg scheman](../concept-detecting-color-schemes.md) för mer information.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors)]

Definiera hjälp funktionen `printColorScheme` om du vill skriva ut information om färg schema till-konsolen.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors_print)]

### <a name="get-domain-specific-content"></a>Hämta domänbaserat innehåll

Visuellt innehåll kan använda specialiserad modell för att utföra ytterligare analyser av avbildningar. Se [domänbaserat innehåll](../concept-detecting-domain-content.md) för mer information. 

Definiera först URL: en för en bild som ska analyseras:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_domain_image)]

Följande kod tolkar data om identifierade landmärken i bilden.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks)]

Definiera hjälp funktionen `formatRectDomain` om du vill parsa plats data om identifierade landmärken.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks_rect)]

### <a name="get-the-image-type"></a>Hämta avbildnings typen

Följande kod skriver ut information om den typ av bild @ no__t-0whether som är ClipArt eller linje ritning.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype)]

Definiera hjälp funktionen `describeType`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype_describe)]

## <a name="read-printed-and-handwritten-text"></a>Skriv ut och handskriven text

Visuellt innehåll kan läsa synlig text i en bild och konvertera den till en tecken ström.

> [!NOTE]
> Du kan också läsa text från en lokal avbildning. Se exempel koden på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) för scenarier som involverar lokala avbildningar.

### <a name="set-up-test-images"></a>Konfigurera test avbildningar

Spara en referens till URL: en för de avbildningar som du vill extrahera text från.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_images)]

### <a name="call-the-recognize-api"></a>Anropa API: et för igenkänning

Lägg till koden nedan, som anropar funktionen `recognizeText` för de aktuella avbildningarna.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_call)]

Definiera `recognizeText`-funktionen. Detta anropar metoden **recognizeText** på klient objekt, som returnerar ett ÅTGÄRDS-ID och startar en asynkron process för att läsa innehållet i avbildningen. Sedan använder den åtgärds-ID: t för att kontrol lera åtgärden vid en sekunds intervall tills resultatet returneras. Den returnerar sedan de extraherade resultaten.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_helper)]

Definiera sedan hjälp funktionen `printRecText`, som skriver ut resultatet av en åtgärd för att identifiera till-konsolen.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_print)]

## <a name="run-the-application"></a>Köra programmet

Kör programmet med kommandot `node` på snabb starts filen.

```console
node index.js
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
>[API för visuellt innehåll referens (Node. js)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest)

* [Vad är API för visuellt innehåll?](../Home.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js).
