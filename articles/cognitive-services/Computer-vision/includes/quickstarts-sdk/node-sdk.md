---
title: 'Snabbstart: Client Library för Computer Vision för Node.js'
description: Kom igång med klientbiblioteket Computer Vision för Node.js med den här snabbstarten
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 01/22/2020
ms.author: pafarley
ms.openlocfilehash: 234e2402287fd76bdfd96cb6884c3fd20e805d06
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80136023"
---
<a name="HOLTop"></a>

[Referensdokumentation](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest) | [Bibliotekskällkodpaket](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-computervision) | [(npm)](https://www.npmjs.com/package/@azure/cognitiveservices-computervision) | [Exempel](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [Skapa en gratis](https://azure.microsoft.com/free/)
* Den aktuella versionen av [Node.js](https://nodejs.org/)

## <a name="setting-up"></a>Inrätta

### <a name="create-a-computer-vision-azure-resource"></a>Skapa en Azure-resurs för datorseende

Azure Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för Datorseende med Hjälp av [Azure-portalen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på din lokala dator. Du kan också:

* Få en [testnyckel](https://azure.microsoft.com/try/cognitive-services/#decision) giltig i sju dagar gratis. När du har registrerat dig är den tillgänglig på [Azure-webbplatsen](https://azure.microsoft.com/try/cognitive-services/my-apis/).
* Visa din resurs på [Azure-portalen](https://portal.azure.com/).

När du har fått en nyckel från din utvärderingsprenumeration eller -resurs [skapar du miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckel- och slutpunkts-URL:en, namngiven `COMPUTER_VISION_SUBSCRIPTION_KEY` respektive. `COMPUTER_VISION_ENDPOINT`

### <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js-program

Skapa en ny katalog för appen i ett konsolfönster (till exempel cmd, PowerShell eller Bash) och navigera till den.

```console
mkdir myapp && cd myapp
```

Kör `npm init` kommandot för att skapa ett `package.json` nodprogram med en fil.

```console
npm init
```

### <a name="install-the-client-library"></a>Installera klientbiblioteket

Installera `ms-rest-azure` NPM-paketen och `@azure/cognitiveservices-computervision` NPM:arna:

```console
npm install @azure/cognitiveservices-computervision
```

Appens `package.json` fil uppdateras med beroenden.

### <a name="prepare-the-nodejs-script"></a>Förbereda nod.js-skriptet

Skapa en ny fil, *index.js*och öppna den i en textredigerare. Lägg till följande importsatser.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imports)]

Definiera sedan en `computerVision` funktion och deklarera en asynkron serie med primär funktion och motringningsfunktion. Du lägger till snabbstartskoden i `computerVision` den primära funktionen och anropar längst ned i skriptet.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_begin)]

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_end)]

## <a name="object-model"></a>Objektmodell

Följande klasser och gränssnitt hanterar några av huvudfunktionerna i Computer Vision Node.js SDK.

|Namn|Beskrivning|
|---|---|
| [ComputerVisionClient (Dator)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest) | Den här klassen behövs för alla datorseendefunktioner. Du instansierar den med din prenumerationsinformation och använder den för att göra de flesta avbildningsåtgärder.|
|[VisualFeatureTypes](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/visualfeaturetypes?view=azure-node-latest)| Den här uppräkningen definierar de olika typer av bildanalys som kan göras i en standardanalysåtgärd. Du anger en uppsättning **VisualFeatureTypes-värden** beroende på dina behov. |

## <a name="code-examples"></a>Kodexempel

Dessa kodavsnitt visar hur du utför följande uppgifter med klientbiblioteket Computer Vision för Node.js:

* [Autentisera klienten](#authenticate-the-client)
* [Analysera en bild](#analyze-an-image)
* [Läsa tryckt och handskriven text](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autentisera klienten

Skapa variabler för resursens Azure-slutpunkt och -nyckel. Om du har skapat miljövariabeln efter att du har startat programmet måste du stänga och öppna redigeraren, IDE:t eller skalet som kör den för att komma åt variabeln.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_vars)]

Instansiera en klient med din slutpunkt och nyckel. Skapa ett [ApiKeyCredentials-objekt](https://docs.microsoft.com/python/api/msrest/msrest.authentication.apikeycredentials?view=azure-python) med nyckeln och slutpunkten och använd det för att skapa ett [ComputerVisionClient-objekt.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest)

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_client)]

## <a name="analyze-an-image"></a>Analysera en bild

Koden i det här avsnittet analyserar fjärrbilder för att extrahera olika visuella funktioner. Du kan utföra dessa åtgärder som en del av **metoden analyzeImage** för klientobjektet, eller så kan du anropa dem med hjälp av enskilda metoder. Mer information finns i [referensdokumentationen.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest)

> [!NOTE]
> Du kan också analysera en lokal bild. Se exempelkoden på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) för scenarier med lokala avbildningar.

### <a name="get-image-description"></a>Få bildbeskrivning

Följande kod hämtar listan över genererade bildtexter för bilden. Mer information finns i [Beskriv bilder.](../../concept-describing-images.md)

Definiera först url:en för en bild som ska analyseras:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe_image)]

Lägg sedan till följande kod för att få bildbeskrivningen och skriva ut den på konsolen.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe)]

### <a name="get-image-category"></a>Hämta bildkategori

Följande kod får den identifierade kategorin av bilden. Mer information [finns i Kategorisera bilder.](../../concept-categorizing-images.md)

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories)]

Definiera hjälpfunktionen: `formatCategories`

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories_format)]

### <a name="get-image-tags"></a>Hämta bildtaggar

Följande kod hämtar uppsättningen identifierade taggar i bilden. Mer information [finns i Innehållstaggar.](../../concept-tagging-images.md)

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tags)]

Definiera hjälpfunktionen: `formatTags`

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tagsformat)]

### <a name="detect-objects"></a>Upptäcka objekt

Följande kod identifierar vanliga objekt i bilden och skriver ut dem på konsolen. Mer information [finns i Objektidentifiering.](../../concept-object-detection.md)

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objects)]

Definiera hjälpfunktionen: `formatRectObjects`

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objectformat)]

### <a name="detect-brands"></a>Identifiera varumärken

Följande kod identifierar företagets varumärken och logotyper i bilden och skriver ut dem på konsolen. Mer information [finns i Varumärkesidentifiering.](../../concept-brand-detection.md)

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_brands)]

### <a name="detect-faces"></a>Identifiera ansikten

Följande kod returnerar de identifierade ytorna i bilden med sina rektangelkoordinater och väljer ansiktsattribut. Mer information finns i [Ansiktsigenkänning.](../../concept-detecting-faces.md)

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_faces)]

Definiera hjälpfunktionen: `formatRectFaces`

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_formatfaces)]

### <a name="detect-adult-racy-or-gory-content"></a>Identifiera innehåll för vuxna, racy eller blodiga

Följande kod skriver ut den upptäckta förekomsten av sexuellt innehåll i bilden. Se [Vuxen, racy, blodiga innehåll](../../concept-detecting-adult-content.md) för mer information.

Definiera URL:en för den bild som ska användas:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult_image)]

Lägg sedan till följande kod för att identifiera barnförbjudet innehåll och skriva ut resultaten på konsolen.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Hämta bildfärgschema

Följande kod skriver ut de identifierade färgattributen i bilden, till exempel de dominerande färgerna och accentfärgen. Se [Färgscheman](../../concept-detecting-color-schemes.md) för mer information.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors)]

Definiera hjälpfunktionen `printColorScheme` för att skriva ut information om färgschemat till konsolen.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors_print)]

### <a name="get-domain-specific-content"></a>Hämta domänspecifikt innehåll

Computer Vision kan använda specialiserad modell för att göra ytterligare analys på bilder. Mer information [finns i Domänspecifikt innehåll.](../../concept-detecting-domain-content.md)

Definiera först url:en för en bild som ska analyseras:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_domain_image)]

Följande kod tolkar data om identifierade landmärken i bilden.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks)]

Definiera hjälpfunktionen `formatRectDomain` för att tolka platsdata om identifierade landmärken.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks_rect)]

### <a name="get-the-image-type"></a>Hämta bildtypen

Följande kod skriver ut information om&mdash;vilken typ av bild det är ClipArt eller linjeritning.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype)]

Definiera hjälpfunktionen: `describeType`

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype_describe)]

## <a name="read-printed-and-handwritten-text"></a>Läsa tryckt och handskriven text

Computer Vision kan läsa synlig text i en bild och konvertera den till en teckenström.

> [!NOTE]
> Du kan också läsa text från en lokal bild. Se exempelkoden på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) för scenarier med lokala avbildningar.

### <a name="set-up-test-images"></a>Konfigurera testbilder

Spara en referens till url:en för de bilder som du vill extrahera text från.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_images)]

### <a name="call-the-recognize-api"></a>Anropa API:et för identifiera

Lägg till koden nedan, `recognizeText` som anropar funktionen för de givna bilderna.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_call)]

Definiera `recognizeText` funktionen. Detta **anropar metoden recognizeText** på klientobjektet, som returnerar ett åtgärds-ID och startar en asynkron process för att läsa bildens innehåll. Sedan används åtgärds-ID för att kontrollera åtgärden med en sekunds mellanrum tills resultaten returneras. Den returnerar sedan de extraherade resultaten.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_helper)]

Definiera sedan hjälpfunktionen `printRecText`, som skriver ut resultatet av en Recognize-åtgärd till konsolen.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_print)]

## <a name="run-the-application"></a>Köra appen

Kör programmet med `node` kommandot på snabbstartsfilen.

```console
node index.js
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services-prenumeration kan du ta bort resursen eller resursgruppen. Om du tar bort resursgruppen tas även alla andra resurser som är associerade bort.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
>[API-referens för datorseende (nod.js)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest)

* [Vad är visuellt innehåll?](../../Home.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js).
