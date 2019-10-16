---
title: 'Snabb start: identifiera tal, mål-C-tal-tjänsten'
titleSuffix: Azure Cognitive Services
description: Lär dig att känna igen tal i mål-C på iOS med hjälp av tal-SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: f943f47cdef901f80aa455d3d1e02a753e0f06e4
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72327758"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-by-using-the-speech-sdk"></a>Snabb start: identifiera tal i mål-C på iOS med hjälp av tal-SDK

Snabb Starter är också tillgängliga för [tal syntes](quickstart-text-to-speech-objectivec-ios.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

I den här artikeln får du lära dig hur du skapar en iOS-app i mål-C genom att använda Azure Cognitive Services Speech SDK för att skriva tal till text från en mikrofon eller från en fil med inspelat ljud.

## <a name="prerequisites"></a>Krav

Innan du börjar måste du ha:

* En [prenumerations nyckel](get-started.md) för tal tjänsten.
* En macOS-dator med [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) eller senare.
* Mål uppsättningen till iOS version 9,3 eller senare.

## <a name="get-the-speech-sdk-for-ios"></a>Skaffa Speech SDK för iOS

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Cognitive Services Speech SDK för iOS är för närvarande distribuerad som ett kakao-ramverk.
Den kan hämtas från [den här webbplatsen](https://aka.ms/csspeech/iosbinary). Ladda ned filen till arbetskatalogen.

## <a name="create-an-xcode-project"></a>Skapa ett Xcode-projekt

Starta Xcode och starta ett nytt projekt genom att välja **fil** > **nytt** > -**projekt**.
I dialog rutan Mallval väljer du app-mallen för **iOS-enkel vy** .

Gör följande val i dialog rutorna som följer.

1. I dialog rutan **projekt alternativ** :
    1. Ange ett namn för snabb starts appen, till exempel *HelloWorld*.
    1. Ange ett lämpligt organisations namn och organisations-ID om du redan har ett Apple Developer-konto. I test syfte kan du använda ett namn som *testorg*. För att signera appen behöver du en korrekt etableringsprofil. Mer information finns på webbplatsen för [Apple Developer](https://developer.apple.com/).
    1. Kontrol lera att **mål-C** har valts som språk för projektet.
    1. Avmarkera alla kryss rutor för test-och kärn data.

    ![Projekt inställningar](media/sdk/qs-objectivec-project-settings.png)

1. Välj en projekt katalog:
   1. Välj arbetskatalogen och spara projektet där. Det här steget skapar en HelloWorld-katalog i din arbets katalog som innehåller alla filer för Xcode-projektet.
   1. Inaktivera skapandet av en Git-lagringsplats för det här exempelprojektet.
   1. Justera Sök vägarna till SDK: n på skärmen projekt inställningar.
      1. På fliken **Allmänt** under rubriken **inbäddade binärfiler** lägger du till SDK-biblioteket som ett ramverk genom att välja **lägg till inbäddade binärfiler** > **Lägg till annan**. Gå till din Hem Katalog och välj filen `MicrosoftCognitiveServicesSpeech.framework`. Den här åtgärden lägger till SDK-biblioteket i sidhuvudet **och biblioteken som länkas** automatiskt.
         ![Added Framework @ no__t-1
      1. Gå till fliken **versions inställningar** och välj inställningen **alla** .
      1. Lägg till katalogen $ (SRCROOT)/.. till **ramverk Sök vägar** under rubriken **Sök sökvägar** .

      ![Inställning av Ramverks Sök vägar](media/sdk/qs-objectivec-framework-search-paths.png)

## <a name="set-up-the-ui"></a>Konfigurera användargränssnittet

Exempel appen har ett väldigt enkelt användar gränssnitt. Det finns två knappar för att starta tal igenkänning från en fil eller från mikrofon indata och en text etikett för att visa resultatet. Användargränssnittet konfigureras i `Main.storyboard`-delen av projektet. Öppna XML-vyn för storyboard genom att högerklicka på posten `Main.storyboard` i projekt trädet och välja **öppna som** > -**källkod**.

Ersätt den automatiskt genererade XML:en med följande kod:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>Lägga till exempelkoden

1. Hämta [exempel filen WAV](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav) genom att högerklicka på länken och välja **Spara mål som**.
   Lägg till wav-filen i projektet som en resurs genom att dra det från ett Finder-fönster till projektvyns rotnivå.
   Välj **Slutför** i följande dialog ruta utan att ändra inställningarna.
1. Ersätt innehållet i den automatiskt genererade `ViewController.m`-filen med följande kod:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/ViewController.m#code)]
1. Ersätt strängen `YourSubscriptionKey` med din prenumerationsnyckel.
1. Ersätt strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration. Använd till exempel `westus` för den kostnads fria prov prenumerationen.
1. Lägg till begäran om åtkomst till mikrofonen. Högerklicka på posten `Info.plist` i projekt trädet och välj **öppna som** > **käll kod**. Lägg till följande rader i avsnittet `<dict>` och spara sedan filen.

    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="build-and-run-the-sample"></a>Skapa och köra exempelappen

1. Gör fel söknings resultatet synligt genom att välja **visa** > **fel söknings sektion** > **Aktivera konsol**.
1. Välj antingen iOS-simulatorn eller en iOS-enhet som är ansluten till utvecklings datorn som mål för appen från listan på**mål** menyn för **produkt** > .
1. Skapa och kör exempel koden i iOS-simulatorn genom att välja **produkt** > **Kör** på menyn. Du kan också välja knappen **spela upp** .
1. När du har valt knappen **identifiera (fil)** i appen bör du se innehållet i ljud filen "Vad är väder som?" på den nedre delen av skärmen.

   ![Simulerad iOS-app](media/sdk/qs-objectivec-simulated-app.png)

1. När du har valt knappen **identifiera (mikrofon)** i appen och sagt några ord bör du se texten du har talat om i den nedre delen av skärmen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska Objektive-C-exempel på GitHub](https://aka.ms/csspeech/samples)
