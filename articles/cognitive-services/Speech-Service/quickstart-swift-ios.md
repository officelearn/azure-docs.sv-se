---
title: 'Snabbstart: Identifiera tal, Swift-tal-tjänst'
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
ms.openlocfilehash: c001b10f14167b4c0947c2ef0ae3f27cc4f55657
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68553536"
---
# <a name="quickstart-recognize-speech-in-swift-on-ios-using-the-speech-sdk"></a>Snabbstart: Identifiera tal i SWIFT på iOS med hjälp av tal-SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

I den här artikeln får du lära dig hur du skapar en iOS-app i Swift med hjälp av Cognitive Services Speech SDK för att skriva tal som registrerats från en mikrofon till text.

## <a name="prerequisites"></a>Förutsättningar

Gå igenom den här listan med förhandskrav innan du sätter igång:

* En [prenumerations nyckel](get-started.md) för tal tjänsten.
* En macOS-dator med [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) eller senare och [CocoaPods](https://cocoapods.org/) installerad.

## <a name="get-the-speech-sdk-for-ios"></a>Skaffa Speech SDK för iOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Den aktuella versionen av Cognitive Services Speech SDK är `1.6.0`. Observera att den här självstudien inte fungerar utan ändringar för någon tidigare version av SDK.

Cognitive Services Speech SDK för iOS distribueras som ett Ramverks paket.
Den kan användas i Xcode-projekt som en [CocoaPod](https://cocoapods.org/)eller hämtas från https://aka.ms/csspeech/macosbinary och länkas manuellt. Den här guiden använder en CocoaPod.

## <a name="create-an-xcode-project"></a>Skapa ett Xcode-projekt

Starta Xcode och starta ett nytt projekt genom att klicka på **File** > **New** > **Project** (Arkiv > Nytt > Projekt).
I dialogrutan för mallval väljer du mallen ”iOS Single View App”.

I dialogrutorna som följer gör du följande val:

1. Dialogrutan Project Options (Projektalternativ)
    1. Ange namnet på snabbstartsappen, till exempel `helloworld`.
    1. Ange ett lämpligt organisations namn och en organisations identifierare om du redan har ett Apple Developer-konto. I testsyfte kan du välja vilket namn som helst, till exempel `testorg`. För att signera appen behöver du en korrekt etableringsprofil. Information finns på [Apple Developer-webbplatsen](https://developer.apple.com/).
    1. Se till att Swift är valt som språk för projektet.
    1. Inaktivera kryss rutorna om du vill använda storyboards och skapa ett dokument baserat program. Det enkla användar gränssnittet för exempel programmet kommer att skapas program mässigt.
    1. Inaktivera alla kryssrutor för test och kärndata.
1. Välja projektkatalog
    1. Välj en katalog där projektet ska läggas till. Detta skapar en `helloworld` katalog i den valda katalogen som innehåller alla filer för Xcode-projektet.
    1. Inaktivera skapandet av en Git-lagringsplats för det här exempelprojektet.
1. Appen måste också deklarera användningen av mikrofonen i `Info.plist` filen. Klicka på filen i översikten och Lägg till nyckeln "sekretess-mikrofon användning Beskrivning" med ett värde som "mikrofon krävs för tal igenkänning".
    ![Inställningar i info. plist](media/sdk/qs-swift-ios-info-plist.png)
1. Stäng Xcode-projektet. Du kommer att använda en annan instans av det senare när du har konfigurerat CocoaPods.

## <a name="add-the-sample-code"></a>Lägga till exempelkoden

1. Placera en ny rubrik fil med namnet `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` `helloworld` i katalogen i projektet HelloWorld och klistra in följande kod i den:[!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. Lägg till den relativa `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` sökvägen till interimskontot för överbryggning i Swift-projektfilen för målet HelloWorld i rubrik ![egenskaper för *mål-C-bryggning*](media/sdk/qs-swift-ios-bridging-header.png)
1. Ersätt innehållet i den automatiskt genererade `AppDelegate.swift`-filen genom att göra följande:  
   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/AppDelegate.swift#code)]
1. Ersätt innehållet i den automatiskt genererade `ViewController.swift`-filen genom att göra följande:  
   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/ViewController.swift#code)]
1. I `ViewController.swift`ersätter du strängen `YourSubscriptionKey` med din prenumerations nyckel.
1. Ersätt strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

## <a name="install-the-sdk-as-a-cocoapod"></a>Installera SDK som en CocoaPod

1. Installera CocoaPod-beroende hanteraren enligt beskrivningen i [installations anvisningarna](https://guides.cocoapods.org/using/getting-started.html).
1. Navigera till katalogen för din exempel App (`helloworld`). Placera en textfil med namnet `Podfile` och följande innehåll i katalogen:  
   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/Podfile)]
1. Navigera till `helloworld` katalogen i en Terminal och kör kommandot `pod install`. Detta skapar en `helloworld.xcworkspace` Xcode-arbetsyta som innehåller både exempel appen och tal-SDK som ett beroende. Den här arbets ytan kommer att användas i följande.

## <a name="build-and-run-the-sample"></a>Skapa och köra exempelappen

1. `helloworld.xcworkspace` Öppna arbets ytan i Xcode.
1. Gör felsökningsresultatet synligt (**View** > **Debug Area** > **Activate Console**) (Visa > Felsökningsområde > Aktivera konsol).
1. Välj antingen iOS-simulatorn eller en iOS-enhet som är ansluten till utvecklings datorn som mål för appen från listan på menyn **produkt** > **mål** .
1. Skapa och kör exempelkoden i iOS-simulatorn genom att välja **Product** > **Run** (Produkt > Kör) på menyn eller genom att klicka på **uppspelningsknappen**.
1. När du klickar på knappen "identifiera" i appen och säger några ord bör du se texten som du har talat om i den nedre delen av skärmen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska våra exempel på GitHub](https://aka.ms/csspeech/samples)
