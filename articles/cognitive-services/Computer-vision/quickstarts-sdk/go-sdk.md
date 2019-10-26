---
title: 'Snabb start: Visuellt innehåll klient bibliotek för go | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Kom igång med Visuellt innehåll klient biblioteket för go.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 10/22/2019
ms.author: pafarley
ms.openlocfilehash: f05ef80d34d1c0635bdcdca244aacba6185565fb
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72958415"
---
# <a name="quickstart-computer-vision-client-library-for-go"></a>Snabb start: Visuellt innehåll klient bibliotek för go

Kom igång med Visuellt innehåll klient biblioteket för go. Följ de här stegen för att installera paketet och prova exempel koden för grundläggande uppgifter. Visuellt innehåll ger dig till gång till avancerade algoritmer för bearbetning av bilder och att returnera information.

Använd Visuellt innehåll klient bibliotek för att gå till:

* Analysera en bild för taggar, text beskrivning, ansikten, vuxen innehåll med mera.
* Identifiera skriven text skriven och handskriven text med API: t batch Read.

[Referens dokumentation](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision) | [bibliotekets käll kod](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision) | [paketet](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* Den senaste versionen av [Go](https://golang.org/dl/)

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-computer-vision-azure-resource"></a>Skapa en Visuellt innehåll Azure-resurs

Azure-Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för Visuellt innehåll med hjälp av [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på den lokala datorn. Du kan också:

* Få en [utvärderings nyckel](https://azure.microsoft.com/try/cognitive-services/#decision) som är giltig i sju dagar utan kostnad. När du har registrerat dig kommer den att vara tillgänglig på [Azure-webbplatsen](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Visa din resurs på [Azure Portal](https://portal.azure.com/).

När du har fått en nyckel från din utvärderings prenumeration eller resurs [skapar du miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckel-och slut punkts-URL: en med namnet `COMPUTER_VISION_SUBSCRIPTION_KEY` respektive `COMPUTER_VISION_ENDPOINT`.

### <a name="create-a-go-project-directory"></a>Skapa en go-projektfil

I ett konsol fönster (cmd, PowerShell, Terminal, bash) skapar du en ny arbets yta för ditt go-projekt, med namnet `my-app`och navigerar till den.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

Arbets ytan kommer att innehålla tre mappar:

* **src** – den här katalogen kommer att innehålla källkod och paket. Alla paket som installeras med `go get` kommandot hamnar i den här katalogen.
* **pkg** – den här katalogen kommer att innehålla de kompilerade go-paket-objekten. De här filerna har ett `.a`-tillägg.
* **bin** – den här katalogen innehåller de binära körbara filer som skapas när du kör `go install`.

> [!TIP]
> Läs mer om strukturen för en Go-arbetsyta i [språk dokumentationen för go](https://golang.org/doc/code.html#Workspaces). Den här guiden innehåller information om hur du ställer in `$GOPATH` och `$GOROOT`.

### <a name="install-the-client-library-for-go"></a>Installera klient biblioteket för go

Installera sedan klient biblioteket för Go:

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

eller i din lagringsplatskörning om du använder dep:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

### <a name="create-a-go-application"></a>Skapa ett Go-program

Skapa sedan en fil i **src** -katalogen med namnet `sample-app.go`:

```bash
cd src
touch sample-app.go
```

Öppna `sample-app.go` i önskad IDE-eller text redigerare. Lägg sedan till paket namnet och importera följande bibliotek:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports)]

Deklarera också en kontext i roten i skriptet. Du behöver det här objektet för att köra de flesta Visuellt innehåll funktions anrop:

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_context)]

Härnäst ska du börja lägga till kod för att utföra olika Visuellt innehåll åtgärder.

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Visuellt innehåll go SDK.

|Namn|Beskrivning|
|---|---|
| [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | Den här klassen krävs för alla Visuellt innehåll funktioner, till exempel bild analys och text läsning. Du instansierar det med din prenumerations information och använder den för att utföra de flesta avbildnings åtgärder.|
|[ImageAnalysis](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ImageAnalysis)| Den här typen innehåller resultatet av ett **AnalyzeImage** -funktions anrop. Det finns liknande typer för var och en av de företagsspecifika funktionerna.|
|[ReadOperationResult](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ReadOperationResult)| Den här typen innehåller resultatet av en batch-Läs åtgärd. |
|[VisualFeatureTypes](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#VisualFeatureTypes)| Den här typen definierar de olika typerna av bild analys som kan göras i en standard analys åtgärd. Du anger en uppsättning VisualFeatureTypes-värden beroende på dina behov. |

## <a name="code-examples"></a>Kod exempel

De här kodfragmenten visar hur du gör följande uppgifter med Visuellt innehåll klient biblioteket för Go:

* [Autentisera klienten](#authenticate-the-client)
* [Analysera en bild](#analyze-an-image)
* [Skriv ut och handskriven text](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autentisera klienten

> [!NOTE]
> Det här steget förutsätter att du har [skapat miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för din visuellt innehåll nyckel och slut punkt med namnet `COMPUTER_VISION_SUBSCRIPTION_KEY` respektive `COMPUTER_VISION_ENDPOINT`.

Skapa en `main`-funktion och Lägg till följande kod i den för att instansiera en klient med din slut punkt och nyckel.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

## <a name="analyze-an-image"></a>Analysera en bild

I följande kod används klient objekt för att analysera en fjärravbildning och skriva ut resultatet till-konsolen. Du kan få en text beskrivning, kategorisering, lista med taggar, identifierade objekt, identifierade märken, identifierade ansikten, vuxen innehålls flaggor, huvud färger och bildtyp.

### <a name="set-up-test-image"></a>Konfigurera test avbildning

Spara först en referens till URL: en för den avbildning som du vill analysera. Lägg detta i din `main`-funktion.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_url)]

> [!NOTE]
> Du kan också analysera en lokal avbildning. Se exempel koden på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) för scenarier som involverar lokala avbildningar.

### <a name="specify-visual-features"></a>Ange visuella funktioner

Följande funktions anrop extraherar olika visuella funktioner från exempel bilden. Du definierar dessa funktioner i följande avsnitt.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze)]

### <a name="get-image-description"></a>Beskrivning av Hämta avbildning

Följande funktion hämtar listan över genererade under texter för avbildningen. Mer information om avbildnings beskrivning finns i [Beskriv avbildningar](../concept-describing-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_describe)]

### <a name="get-image-category"></a>Hämta bild kategori

Följande funktion hämtar den identifierade kategorin för avbildningen. Mer information finns i [kategorisera bilder](../concept-categorizing-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_categorize)]

### <a name="get-image-tags"></a>Hämta bild etiketter

Följande funktion hämtar en uppsättning identifierade Taggar i bilden. Mer information finns i [innehålls etiketter](../concept-tagging-images.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_tags)]

### <a name="detect-objects"></a>Identifiera objekt

Följande funktion identifierar vanliga objekt i avbildningen och skriver ut dem till-konsolen. Mer information finns i [objekt identifiering](../concept-object-detection.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_objects)]

### <a name="detect-brands"></a>Identifiera varumärken

Följande kod identifierar företags märken och logo typer i bilden och skriver ut dem till-konsolen. För mer information, [varumärkes identifiering](../concept-brand-detection.md).

Deklarera först en referens till en ny avbildning i din `main` funktion.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brand_url)]

Följande kod definierar varumärkes identifierings funktionen.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brands)]

### <a name="detect-faces"></a>Identifiera ansikten

Följande funktion returnerar identifierade ansikten i bilden med deras Rectangle-koordinater och vissa ansikts attribut. Mer information finns i [ansikts igenkänning](../concept-detecting-faces.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Identifiera vuxna, vågat eller fullständig innehåll

Följande funktion skriver ut den identifierade förekomsten av olämpligt innehåll i bilden. Mer information finns i [vuxen, vågat, fullständig-innehåll](../concept-detecting-adult-content.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Hämta färg schema för bild

Följande funktion skriver ut de identifierade färgattributen i bilden, till exempel dominerande färger och dekor färg. Mer information finns i [färg scheman](../concept-detecting-color-schemes.md).

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Hämta domänbaserat innehåll

Visuellt innehåll kan använda specialiserade modeller för att utföra ytterligare analyser av avbildningar. Mer information finns i [domänbaserat innehåll](../concept-detecting-domain-content.md). 

Följande kod tolkar data om identifierade kändisar i avbildningen.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_celebs)]

Följande kod tolkar data om identifierade landmärken i bilden.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Hämta avbildnings typen

Följande funktion skriver ut information om typen av bild&mdash;om den är ClipArt eller en linje ritning.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Skriv ut och handskriven text

Visuellt innehåll kan läsa synlig text i en bild och konvertera den till en tecken ström. Koden i det här avsnittet definierar en funktion, `RecognizeTextReadAPIRemoteImage`, som använder-klient objekt för att identifiera och extrahera utskrift eller handskriven text i bilden.

Lägg till exempel bild referensen och funktions anropet i din `main` funktion.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_readinmain)]

> [!NOTE]
> Du kan också Extrahera text från en lokal avbildning. Se exempel koden på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) för scenarier som involverar lokala avbildningar.

### <a name="call-the-read-api"></a>Anropa Read API

Definiera den nya funktionen för att läsa text, `RecognizeTextReadAPIRemoteImage`. Lägg till koden nedan, som anropar **BatchReadFile** -metoden för den aktuella avbildningen. Den här metoden returnerar ett åtgärds-ID och startar en asynkron process för att läsa innehållet i avbildningen.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_call)]

### <a name="get-read-results"></a>Hämta Läs resultat

Hämta sedan det åtgärds-ID som returnerades från **BatchReadFile** -anropet och Använd det med **GetReadOperationResult** -metoden för att fråga tjänsten efter åtgärds resultat. Följande kod kontrollerar åtgärden vid en sekunds intervall tills resultatet returneras. Den skriver sedan ut de extraherade text data till-konsolen.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_response)]

### <a name="display-read-results"></a>Visa Läs resultat

Lägg till följande kod för att parsa och Visa hämtade text data och slutför funktions definitionen.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_display)]

## <a name="run-the-application"></a>Köra programmet

Kör programmet från program katalogen med kommandot `go run`.

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portalen](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [API för visuellt innehåll referens (go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision)

* [Vad är API för visuellt innehåll?](../Home.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go).
