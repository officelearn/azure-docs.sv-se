---
title: 'Snabbstart: Känna igen tal från en mikrofon, Swift - Taltjänst'
titleSuffix: Azure Cognitive Services
description: Lär dig hur du känner igen tal i Swift på iOS med hjälp av Tal-SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/23/2019
ms.author: chlandsi
ms.openlocfilehash: 31f5339c70d52019400ca5f1fe873de4790a3bd6
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75380532"
---
# <a name="quickstart-recognize-speech-in-swift-on-ios-by-using-the-speech-sdk"></a>Snabbstart: Känna igen tal i Swift på iOS med hjälp av Tal-SDK

Snabbstarter finns också tillgängliga för [talsyntes](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/swift-ios.md).

I den här artikeln får du lära dig hur du skapar en iOS-app i Swift med hjälp av Azure Cognitive Services Speech SDK för att transkribera tal som spelats in från en mikrofon till text.

## <a name="prerequisites"></a>Krav

Innan du börjar behöver du:

* En [prenumerationsnyckel](~/articles/cognitive-services/Speech-Service/get-started.md) för taltjänsten.
* En macOS-maskin med [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) eller senare och [CocoaPods](https://cocoapods.org/) installerade.

## <a name="get-the-speech-sdk-for-ios"></a>Skaffa Speech SDK för iOS

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Den här självstudien fungerar inte med en version av SDK tidigare än 1.6.0.

Cognitive Services Speech SDK för iOS distribueras som ett rampaket. Det kan användas i Xcode projekt som en https://aka.ms/csspeech/iosbinary [CocoaPod](https://cocoapods.org/) eller laddas ner från och länkas manuellt. Denna artikel använder en CocoaPod.

## <a name="create-an-xcode-project"></a>Skapa ett Xcode-projekt

Starta Xcode och starta ett nytt projekt genom att välja **Arkiv** > **nytt** > **projekt**.
Välj **iOS-appmallen i** mallval.

Gör följande val i dialogrutorna som följer.

1. I dialogrutan **Projektalternativ:**
    1. Ange ett namn för snabbstartsappen, till exempel *helloworld*.
    1. Ange ett lämpligt organisationsnamn och en organisationsidentifierare om du redan har ett Apple-utvecklarkonto. Använd ett namn som *testorg*för testning. För att signera appen behöver du en korrekt etableringsprofil. Mer information finns på [Apples utvecklarwebbplats](https://developer.apple.com/).
    1. Se till att **Swift** väljs som språk för projektet.
    1. Inaktivera kryssrutorna om du vill använda storyboards och skapa ett dokumentbaserat program. Det enkla användargränssnittet för exempelappen skapas programmässigt.
    1. Avmarkera alla kryssrutor för tester och kärndata.
1. Välj en projektkatalog:
    1. Välj en katalog som projektet ska användas. Det här steget skapar en helloworld-katalog i den valda katalogen som innehåller alla filer för Xcode-projektet.
    1. Inaktivera skapandet av en Git-lagringsplats för det här exempelprojektet.
1. Appen måste också deklarera användningen av `Info.plist` mikrofonen i filen. Markera filen i översikten och lägg till nyckeln **Sekretess - Mikrofonanvändningsbeskrivning** med ett värde som *Mikrofon behövs för taligenkänning*.

    ![Inställningar i Info.plist](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-info-plist.png)

1. Stäng Xcode-projektet. Du använder en annan instans av den senare när du har ställt in CocoaPods.

## <a name="add-the-sample-code"></a>Lägga till exempelkoden

1. Placera en ny rubrikfil `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` med namnet i helloworld-katalogen i helloworld-projektet. Klistra in följande kod i den:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]

1. Lägg till `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` den relativa sökvägen till brygghuvudet i Swift-projektinställningarna för helloworld-målet i fältet **Mål-C-brygghuvud.**

   ![Egenskaper för huvud](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-bridging-header.png)

1. Ersätt innehållet i den autogenererade `AppDelegate.swift` filen med följande kod:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/AppDelegate.swift#code)]
1. Ersätt innehållet i den autogenererade `ViewController.swift` filen med följande kod:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/ViewController.swift#code)]
1. Ersätt `ViewController.swift`strängen `YourSubscriptionKey` med prenumerationsnyckeln i .
1. Ersätt strängen `YourServiceRegion` med den [region som](~/articles/cognitive-services/Speech-Service/regions.md) är associerad med din prenumeration. Använd till `westus` exempel för den kostnadsfria utvärderingsprenumerationen.

## <a name="install-the-sdk-as-a-cocoapod"></a>Installera SDK som en CocoaPod

1. Installera CocoaPod beroende manager som beskrivs i dess [installationsanvisningar](https://guides.cocoapods.org/using/getting-started.html).
1. Gå till katalogen för din exempelapp, som är helloworld. Placera en textfil med namnet *Podfile* och följande innehåll i den katalogen:

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/Podfile)]
1. Gå till helloworld-katalogen i en `pod install`terminal och kör kommandot . Det här kommandot `helloworld.xcworkspace` genererar en Xcode-arbetsyta som innehåller både exempelappen och Tal-SDK som ett beroende. Den här arbetsytan används i följande steg.

## <a name="build-and-run-the-sample"></a>Skapa och köra exempelappen

1. Öppna arbetsytan `helloworld.xcworkspace` i Xcode.
1. Gör felsökningsutdata synliga genom att välja **Visa** > **felsökningsområde** > **Aktivera konsolen**.
1. Välj antingen iOS-simulatorn eller en iOS-enhet som är ansluten till utvecklingsdatorn som mål för appen i listan på menyn > **Produktmål.** **Product**
1. Skapa och kör exempelkoden i iOS-simulatorn genom att välja > **Produktkörning** på menyn. **Product** Du kan också välja knappen **Spela upp.**
1. När du har valt knappen **Identifiera** i appen och säga några ord bör du se texten du har talat upp på den nedre delen av skärmen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska våra exempel på GitHub](https://aka.ms/csspeech/samples)
