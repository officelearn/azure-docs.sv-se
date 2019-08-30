---
title: 'Snabbstart: Visuellt innehåll klient bibliotek för Java'
titleSuffix: Azure Cognitive Services
description: Kom igång med Visuellt innehåll klient bibliotek för Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 07/25/2019
ms.author: pafarley
ms.openlocfilehash: daea1415c42960970d097753bc657392d4e1a1f4
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70137263"
---
# <a name="quickstart-computer-vision-client-library-for-java"></a>Snabbstart: Visuellt innehåll klient bibliotek för Java

Kom igång med Visuellt innehåll klient bibliotek för Java. Följ de här stegen för att installera paketet och prova exempel koden för grundläggande uppgifter. Visuellt innehåll ger dig till gång till avancerade algoritmer för bearbetning av bilder och att returnera information.

Använd Visuellt innehåll klient bibliotek för Java för att:

* Analysera en bild för taggar, text beskrivning, ansikten, vuxen innehåll med mera.

[](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable) | [Exempel](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0) på referens dokumentations[artefakt (maven)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | 

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* Den aktuella versionen av [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle build-verktyget](https://gradle.org/install/)eller någon annan beroende hanterare.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-computer-vision-azure-resource"></a>Skapa en Visuellt innehåll Azure-resurs

Azure-Cognitive Services representeras av Azure-resurser som du prenumererar på. Skapa en resurs för Visuellt innehåll med hjälp av [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på den lokala datorn. Du kan också:

* Få en [utvärderings nyckel](https://azure.microsoft.com/try/cognitive-services/#decision) som är giltig i sju dagar utan kostnad. När du har registrerat dig kommer den att vara tillgänglig på [Azure-webbplatsen](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Visa din resurs på [Azure Portal](https://portal.azure.com/).

Skapa sedan [miljövariabler](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för nyckel-och tjänst slut punkts strängen, `COMPUTER_VISION_SUBSCRIPTION_KEY` med `COMPUTER_VISION_ENDPOINT`namnet respektive.

### <a name="create-a-new-gradle-project"></a>Skapa ett nytt Gradle-projekt

I ett konsol fönster (till exempel cmd, PowerShell eller bash) skapar du en ny katalog för din app och navigerar till den. 

```console
mkdir myapp && cd myapp
```

`gradle init` Kör kommandot från din arbets katalog. Med det här kommandot skapas viktiga build-filer för Gradle, inklusive *build. Gradle. KTS*, som används vid körning för att skapa och konfigurera ditt program.

```console
gradle init --type basic
```

Välj en **DSL** när du uppmanas till det och välj **Kotlin**.

Leta upp *build. gradle. KTS* och öppna den med önskad IDE-eller text redigerare. Kopiera sedan i följande build-konfiguration. Den här konfigurationen definierar projektet som ett Java-program vars start punkt är klassen **ComputerVisionQuickstarts**. Visuellt innehålls biblioteket importeras.

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "ComputerVisionQuickstarts"
}
repositories {
    mavenCentral()
}
```

Kör följande kommando från din arbets katalog för att skapa en mapp för projekt Källa:

```console
mkdir -p src/main/java
```

Navigera till den nya mappen och skapa en fil med namnet *ComputerVisionQuickstarts. java*. Öppna det i önskat redigerings program eller IDE och Lägg till följande `import` -uttryck:

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_imports)]

Lägg sedan till en klass definition för **ComputerVisionQuickstarts**.

### <a name="install-the-client-library"></a>Installera klient biblioteket

I den här snabb starten används Gradle-beroende hanteraren. Du hittar klient biblioteket och information för andra beroende hanterare på den [centrala maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/)-lagringsplatsen.

I projektets *build. gradle. KTS* -fil inkluderar du visuellt innehåll klient biblioteket som ett beroende.

```kotlin
dependencies {
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-computervision", version = "1.0.2-beta")
}
```

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Visuellt innehåll Java SDK.

|Name|Beskrivning|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) | Den här klassen krävs för alla Visuellt innehåll-funktioner. Du instansierar det med din prenumerations information och använder den för att skapa instanser av andra klasser.|
|[ComputerVision](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision?view=azure-java-stable)| Den här klassen kommer från klient objekt och hanterar direkt alla avbildnings åtgärder, till exempel bild analys, text identifiering och generering av miniatyrer.
|[VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable)| Den här uppräkningen definierar de olika typerna av bild analys som kan göras i en standard analys åtgärd. Du anger en uppsättning VisualFeatureTypes-värden beroende på dina behov. |

## <a name="code-examples"></a>Kod exempel

De här kodfragmenten visar hur du gör följande uppgifter med Visuellt innehåll klient bibliotek för java:

* [Autentisera klienten](#authenticate-the-client)
* [Analysera en bild](#analyze-an-image)

## <a name="authenticate-the-client"></a>Autentisera klienten

> [!NOTE]
> Den här snabb starten förutsätter att du har [skapat en miljö variabel](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) för din `COMPUTER_VISION_SUBSCRIPTION_KEY`visuellt innehåll nyckel, med namnet.

Följande kod lägger till en `main` metod i klassen och skapar variabler för resursens Azure-slutpunkt och nyckel. Du måste ange din egen slut punkts sträng, som du hittar i översikts avsnittet av Azure Portal. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_mainvars)]

Lägg sedan till följande kod för att skapa ett [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) -objekt och skickar det till andra metoder, som du definierar senare.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_client)]

> [!NOTE]
> Om du har skapat miljövariabeln efter att du har startat programmet måste du stänga och öppna redigerings programmet, IDE eller gränssnittet som kör det för att få åtkomst till variabeln.

## <a name="analyze-an-image"></a>Analysera en bild

Följande kod definierar en metod, `AnalyzeLocalImage`som använder klient objekt för att analysera en lokal avbildning och skriva ut resultaten. Metoden returnerar en text beskrivning, kategorisering, lista med taggar, identifierade ansikten, olämpliga innehålls flaggor, huvud färger och bildtyp.

### <a name="set-up-test-image"></a>Konfigurera test avbildning

Börja med att skapa en **resurs/** mapp i ditt projekts **src/main/-** mapp och Lägg till en avbildning som du vill analysera. Lägg sedan till följande metod definition i **ComputerVisionQuickstarts** -klassen. Om det `pathToLocalImage` behövs ändrar du värdet för för att matcha bild filen. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_refs)]

### <a name="specify-visual-features"></a>Ange visuella funktioner

Ange sedan vilka visuella funktioner du vill extrahera i analysen. Se [VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable) -uppräkningen för en fullständig lista.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_features)]

### <a name="analyze"></a>Analysera
Den här metoden skriver ut detaljerade resultat till-konsolen för varje omfång av bild analys. Vi rekommenderar att du omger det här metod anropet i ett try/catch-block

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_analyze)]

### <a name="display-results"></a>Visa resultat

Metod anropet ovan returnerar ett ImageAnalysis-objekt som innehåller all extraherad information. Du kan använda ett kodblock som följande för att skriva ut information om en specifik visuell funktion.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_display)]

En fullständig uppsättning visnings alternativ finns i exempel koden på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/ComputerVisionQuickstart.java) .

## <a name="run-the-application"></a>Köra programmet

Du kan bygga appen med:

```console
gradle build
```

Kör programmet med `gradle run` kommandot:

```console
gradle run
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du använder Visuellt innehåll Java-biblioteket för att utföra grundläggande uppgifter. Sedan kan du utforska referens dokumentationen och läsa mer om biblioteket.

> [!div class="nextstepaction"]
>[Visuellt innehåll referens (Java)](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable)

* [Vad är Visuellt innehåll?](../Home.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/ComputerVisionQuickstart.java).