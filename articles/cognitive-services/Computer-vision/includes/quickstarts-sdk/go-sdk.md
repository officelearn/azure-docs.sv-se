---
title: 'Snabbstart: Client Library för Datorseende för Go'
titleSuffix: Azure Cognitive Services
description: Kom igång med klientbiblioteket Computer Vision for Go med den här snabbstarten.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: d8f40ab57ee2569b2cb5bf62f391919476b8ab17
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80136016"
---
<a name="HOLTop"></a>

[Källkodspaket för](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision) | [referensdokumentation](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision) | [bibliotek](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [Skapa en gratis](https://azure.microsoft.com/free/)
* Den senaste versionen av [Go](https://golang.org/dl/)

## <a name="setting-up"></a>Inrätta

### <a name="create-a-computer-vision-azure-resource"></a>Skapa en Azure-resurs för datorseende

Azure Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för Datorseende med Hjälp av [Azure-portalen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på din lokala dator. Du kan också:

* Få en [testnyckel](https://azure.microsoft.com/try/cognitive-services/#decision) giltig i sju dagar gratis. När du har registrerat dig är den tillgänglig på [Azure-webbplatsen](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Visa din resurs på [Azure-portalen](https://portal.azure.com/).

När du har fått en nyckel från utvärderingsprenumerationen eller resursen `COMPUTER_VISION_ENDPOINT`skapar du [miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckel- och slutpunkts-URL:en, med namn `COMPUTER_VISION_SUBSCRIPTION_KEY` respektive , respektive.

### <a name="create-a-go-project-directory"></a>Skapa en Go-projektkatalog

Skapa en ny arbetsyta för Go-projektet med namnet `my-app`Go-projektet i ett konsolfönster (cmd, PowerShell, Terminal, Bash) och navigera till den.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

Arbetsytan innehåller tre mappar:

* **src** - Den här katalogen innehåller källkod och paket. Alla paket som `go get` installeras med kommandot kommer att gå i den här katalogen.
* **pkg** - Den här katalogen innehåller de kompilerade Go-paketobjekten. Dessa filer har `.a` alla ett tillägg.
* **bin** - Den här katalogen innehåller de binära `go install`körbara filer som skapas när du kör .

> [!TIP]
> Mer information om strukturen på en Go-arbetsyta finns i [dokumentdokumentationen Gå.](https://golang.org/doc/code.html#Workspaces) Den här guiden `$GOPATH` innehåller `$GOROOT`information för inställning och .

### <a name="install-the-client-library-for-go"></a>Installera klientbiblioteket för Go

Installera sedan klientbiblioteket för Go:

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

eller i din lagringsplatskörning om du använder dep:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

### <a name="create-a-go-application"></a>Skapa ett Go-program

Skapa sedan en fil i **src-katalogen** med namnet `sample-app.go`:

```bash
cd src
touch sample-app.go
```

Öppna `sample-app.go` i önskad IDE- eller textredigerare. Lägg sedan till paketnamnet och importera följande bibliotek:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports)]

Deklarera också en kontext i skriptets rot. Du behöver det här objektet för att köra de flesta datorseende funktionsanrop:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_context)]

Därefter ska du börja lägga till kod för att utföra olika datorseendeåtgärder.

## <a name="object-model"></a>Objektmodell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Computer Vision Go SDK.

|Namn|Beskrivning|
|---|---|
| [BaseClient (avbaskl)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | Den här klassen behövs för alla datorseendefunktioner, till exempel bildanalys och textläsning. Du instansierar den med din prenumerationsinformation och använder den för att göra de flesta avbildningsåtgärder.|
|[BildAnalys](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ImageAnalysis)| Den här typen innehåller resultatet av ett AnalyzeImage-funktionsanrop. **AnalyzeImage** Det finns liknande typer för var och en av de kategorispecifika funktionerna.|
|[ReadOperationResult](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ReadOperationResult)| Den här typen innehåller resultatet av en batchläsning. |
|[VisualFeatureTypes](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#VisualFeatureTypes)| Den här typen definierar de olika typer av bildanalys som kan göras i en standardanalysåtgärd. Du anger en uppsättning VisualFeatureTypes-värden beroende på dina behov. |

## <a name="code-examples"></a>Kodexempel

De här kodavsnitten visar hur du utför följande uppgifter med klientbiblioteket Computer Vision for Go:

* [Autentisera klienten](#authenticate-the-client)
* [Analysera en bild](#analyze-an-image)
* [Läsa tryckt och handskriven text](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autentisera klienten

> [!NOTE]
> Det här steget förutsätter att du har [skapat miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckeln och slutpunkten för datorseende, namngivna `COMPUTER_VISION_SUBSCRIPTION_KEY` respektive. `COMPUTER_VISION_ENDPOINT`

Skapa `main` en funktion och lägg till följande kod i den för att instansiera en klient med din slutpunkt och nyckel.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

## <a name="analyze-an-image"></a>Analysera en bild

Följande kod använder klientobjektet för att analysera en fjärravbildning och skriva ut resultaten till konsolen. Du kan få en textbeskrivning, kategorisering, lista med taggar, identifierade objekt, identifierade varumärken, identifierade ansikten, flaggor för barnförbjudet innehåll, huvudfärger och bildtyp.

### <a name="set-up-test-image"></a>Konfigurera testavbildning

Spara först en referens till url:en för den bild som du vill analysera. Sätt detta `main` i din funktion.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_url)]

> [!NOTE]
> Du kan också analysera en lokal bild. Se exempelkoden på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) för scenarier med lokala avbildningar.

### <a name="specify-visual-features"></a>Ange visuella funktioner

Följande funktionsanrop extraherar olika visuella funktioner från exempelbilden. Du definierar dessa funktioner i följande avsnitt.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze)]

### <a name="get-image-description"></a>Få bildbeskrivning

Följande funktion hämtar listan över genererade bildtexter för bilden. Mer information om bildbeskrivning finns i [Beskriv bilder](../../concept-describing-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_describe)]

### <a name="get-image-category"></a>Hämta bildkategori

Följande funktion får den identifierade kategorin av bilden. Mer information finns i [Kategorisera bilder](../../concept-categorizing-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_categorize)]

### <a name="get-image-tags"></a>Hämta bildtaggar

Följande funktion hämtar uppsättningen identifierade taggar i bilden. Mer information finns i [Innehållstaggar](../../concept-tagging-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_tags)]

### <a name="detect-objects"></a>Upptäcka objekt

Följande funktion identifierar vanliga objekt i bilden och skriver ut dem till konsolen. Mer information finns i [Objektidentifiering](../../concept-object-detection.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_objects)]

### <a name="detect-brands"></a>Identifiera varumärken

Följande kod identifierar företagets varumärken och logotyper i bilden och skriver ut dem på konsolen. För mer information, [Brand detection](../../concept-brand-detection.md).

Deklarera först en referens till en `main` ny bild i funktionen.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brand_url)]

Följande kod definierar varumärkesidentifieringsfunktionen.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brands)]

### <a name="detect-faces"></a>Identifiera ansikten

Följande funktion returnerar de identifierade ytorna i bilden med sina rektangelkoordinater och vissa ansiktsattribut. Mer information finns i [Ansiktsigenkänning](../../concept-detecting-faces.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Identifiera innehåll för vuxna, racy eller blodiga

Följande funktion skriver ut den upptäckta förekomsten av sexuellt innehåll i bilden. Mer information finns i [Vuxen, racy, blodiga innehåll](../../concept-detecting-adult-content.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Hämta bildfärgschema

I följande funktion skrivs de identifierade färgattributen i bilden ut, till exempel de dominerande färgerna och accentfärgen. Mer information finns i [Färgscheman](../../concept-detecting-color-schemes.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Hämta domänspecifikt innehåll

Computer Vision kan använda specialiserade modeller för att göra ytterligare analys på bilder. Mer information finns i [Domänspecifikt innehåll](../../concept-detecting-domain-content.md). 

Följande kod tolkar data om identifierade kändisar i bilden.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_celebs)]

Följande kod tolkar data om identifierade landmärken i bilden.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Hämta bildtypen

I följande funktion skrivs information&mdash;om vilken typ av bild det är ClipArt eller en linjeritning.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Läsa tryckt och handskriven text

Computer Vision kan läsa synlig text i en bild och konvertera den till en teckenström. Koden i det här avsnittet `RecognizeTextReadAPIRemoteImage`definierar en funktion, som använder klientobjektet för att identifiera och extrahera utskriven eller handskriven text i bilden.

Lägg till exempelbildsreferensen `main` och funktionsanropet i din funktion.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_readinmain)]

> [!NOTE]
> Du kan också extrahera text från en lokal bild. Se exempelkoden på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) för scenarier med lokala avbildningar.

### <a name="call-the-read-api"></a>Anropa läs-API:et

Definiera den nya funktionen `RecognizeTextReadAPIRemoteImage`för att läsa text, . Lägg till koden nedan, som **anropar Metoden BatchReadFile** för den givna bilden. Den här metoden returnerar ett åtgärds-ID och startar en asynkron process för att läsa bildens innehåll.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_call)]

### <a name="get-read-results"></a>Få läsresultat

Hämta sedan åtgärds-ID:et som returneras från **BatchReadFile-anropet** och använder det med metoden **GetReadOperationResult** för att fråga tjänsten efter åtgärdsresultat. Följande kod kontrollerar åtgärden med en sekunds mellanrum tills resultaten returneras. Sedan skrivs de extraherade textdata ut till konsolen.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_response)]

### <a name="display-read-results"></a>Visa läsresultat

Lägg till följande kod för att tolka och visa hämtade textdata och avsluta funktionsdefinitionen.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_display)]

## <a name="run-the-application"></a>Köra appen

Kör programmet från programkatalogen `go run` med kommandot.

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services-prenumeration kan du ta bort resursen eller resursgruppen. Om du tar bort resursgruppen tas även alla andra resurser som är associerade bort.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [API-referens för datorseende (Go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision)

* [Vad är visuellt innehåll?](../../Home.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go).
