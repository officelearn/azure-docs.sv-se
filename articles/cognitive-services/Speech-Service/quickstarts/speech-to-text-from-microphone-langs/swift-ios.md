---
title: 'Snabb start: identifiera tal från en mikrofon, Swift-tal-tjänst'
titleSuffix: Azure Cognitive Services
description: Lär dig att känna igen tal i SWIFT på iOS med hjälp av tal-SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 661dd94caa2a1998b26ec1270688c0729011c15a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503628"
---
# <a name="quickstart-recognize-speech-in-swift-on-ios-by-using-the-speech-sdk"></a>Snabb start: identifiera tal i SWIFT på iOS med hjälp av tal-SDK

Snabb Starter är också tillgängliga för [tal syntes](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/swift-ios.md).

I den här artikeln får du lära dig hur du skapar en iOS-app i Swift genom att använda Azure Cognitive Services Speech SDK för att skriva tal som registrerats från en mikrofon till text.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar måste du ha:

* En [prenumerations nyckel](~/articles/cognitive-services/Speech-Service/get-started.md) för tal tjänsten.
* En macOS-dator med [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) eller senare och [CocoaPods](https://cocoapods.org/) installerad.

## <a name="get-the-speech-sdk-for-ios"></a>Skaffa Speech SDK för iOS

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Den här självstudien fungerar inte med en tidigare version av SDK än 1.6.0.

Cognitive Services Speech SDK för iOS distribueras som ett Ramverks paket. Den kan användas i Xcode-projekt som en [CocoaPod](https://cocoapods.org/) eller hämtas från https://aka.ms/csspeech/macosbinary och länkas manuellt. I den här artikeln används en CocoaPod.

## <a name="create-an-xcode-project"></a>Skapa ett Xcode-projekt

Starta Xcode och starta ett nytt projekt genom att välja **fil** > **nytt** > **projekt**.
I dialog rutan Mallval väljer du app-mallen för **iOS-enkel vy** .

Gör följande val i dialog rutorna som följer.

1. I dialog rutan **projekt alternativ** :
    1. Ange ett namn för snabb starts appen, till exempel *HelloWorld*.
    1. Ange ett lämpligt organisations namn och ett organisations-ID om du redan har ett Apple Developer-konto. I test syfte kan du använda ett namn som *testorg*. För att signera appen behöver du en korrekt etableringsprofil. Mer information finns på webbplatsen för [Apple Developer](https://developer.apple.com/).
    1. Se till att **Swift** är valt som språk för projektet.
    1. Inaktivera kryss rutorna om du vill använda storyboards och skapa ett dokument baserat program. Det enkla användar gränssnittet för exempel programmet skapas program mässigt.
    1. Avmarkera alla kryss rutor för test-och kärn data.
1. Välj en projekt katalog:
    1. Välj en katalog där projektet ska läggas till. Det här steget skapar en HelloWorld-katalog i den valda katalogen som innehåller alla filer för Xcode-projektet.
    1. Inaktivera skapandet av en Git-lagringsplats för det här exempelprojektet.
1. Appen måste också deklarera användningen av mikrofonen i `Info.plist`-filen. Välj filen i översikten och Lägg till nyckeln för **användnings Beskrivning av sekretess-mikrofon** med ett värde som *mikrofon krävs för tal igenkänning*.

    ![Inställningar i info. plist](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-info-plist.png)

1. Stäng Xcode-projektet. Du använder en annan instans av det senare när du har konfigurerat CocoaPods.

## <a name="add-the-sample-code"></a>Lägga till exempelkoden

1. Placera en ny rubrik fil med namnet `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` i katalogen HelloWorld i projektet HelloWorld. Klistra in följande kod i den:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]

1. Lägg till den relativa sökvägen `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` till interimskontot för överbryggning till SWIFT-projektfilen för målet HelloWorld i **huvud fältet mål-C-bryggning** .

   ![Rubrik egenskaper](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-bridging-header.png)

1. Ersätt innehållet i den automatiskt genererade `AppDelegate.swift`-filen med följande kod:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/AppDelegate.swift#code)]
1. Ersätt innehållet i den automatiskt genererade `ViewController.swift`-filen med följande kod:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/ViewController.swift#code)]
1. I `ViewController.swift`ersätter du strängen `YourSubscriptionKey` med din prenumerations nyckel.
1. Ersätt strängen `YourServiceRegion` med den [region](~/articles/cognitive-services/Speech-Service/regions.md) som är associerad med din prenumeration. Använd till exempel `westus` för den kostnads fria prov prenumerationen.

## <a name="install-the-sdk-as-a-cocoapod"></a>Installera SDK som en CocoaPod

1. Installera CocoaPod-beroende hanteraren enligt beskrivningen i [installations anvisningarna](https://guides.cocoapods.org/using/getting-started.html).
1. Gå till katalogen för din exempel app, som är HelloWorld. Placera en textfil med namnet *Podfile* och följande innehåll i den katalogen:

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/Podfile)]
1. Gå till katalogen HelloWorld i en Terminal och kör kommandot `pod install`. Det här kommandot genererar en `helloworld.xcworkspace` Xcode-arbetsyta som innehåller både exempel programmet och tal-SDK som ett beroende. Den här arbets ytan används i följande steg.

## <a name="build-and-run-the-sample"></a>Skapa och köra exempelappen

1. Öppna arbets ytans `helloworld.xcworkspace` i Xcode.
1. Se till att fel söknings utdata visas genom att välja **visa** > **fel söknings områden** > **Aktivera konsol**.
1. Välj antingen iOS-simulatorn eller en iOS-enhet som är ansluten till utvecklings datorn som mål för appen från listan på menyn **produkt** > **mål** .
1. Skapa och kör exempel koden i iOS-simulatorn genom att välja **produkt** > **köra** från menyn. Du kan också välja knappen **spela upp** .
1. När du har valt knappen **identifiera** i appen och sagt några ord bör du se texten du har talat om i den nedre delen av skärmen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska våra exempel på GitHub](https://aka.ms/csspeech/samples)
