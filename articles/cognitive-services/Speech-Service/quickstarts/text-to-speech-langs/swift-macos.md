---
title: 'Snabb start: syntetisera tal, Swift-tal-tjänst'
titleSuffix: Azure Cognitive Services
description: Lär dig att syntetisera tal i SWIFT på macOS med hjälp av tal-SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 616d212c9968fe740811805eb6ecdc200c749ed7
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74817726"
---
# <a name="quickstart-synthesize-speech-in-swift-on-macos-using-the-speech-sdk"></a>Snabb start: syntetisera tal i SWIFT på macOS med hjälp av tal-SDK

I den här artikeln får du lära dig hur du skapar en macOS-app i Swift med hjälp av Cognitive Services Speech SDK för att syntetisera tal från text och spela upp det med standard ljud uppspelning.

## <a name="prerequisites"></a>Krav

Gå igenom den här listan med förhandskrav innan du sätter igång:

* En [prenumerations nyckel](~/articles/cognitive-services/Speech-Service/get-started.md) för tal tjänsten.
* En macOS-dator med [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) eller senare och [CocoaPods](https://cocoapods.org/) installerad.

## <a name="get-the-speech-sdk-for-macos"></a>Hämta tal-SDK för macOS

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Cognitive Services Speech SDK för macOS distribueras som ett Ramverks paket.
Den kan användas i Xcode-projekt som en [CocoaPod](https://cocoapods.org/)eller hämtas från https://aka.ms/csspeech/macosbinary och länkas manuellt. Den här guiden använder en CocoaPod.

> [!NOTE] 
> Den här självstudien kommer inte att fungera med tidigare versioner av SDK än 1.7.0.

## <a name="create-an-xcode-project"></a>Skapa ett Xcode-projekt

Starta Xcode och starta ett nytt projekt genom att klicka på **File** > **New** > **Project** (Arkiv > Nytt > Projekt).
I dialog rutan Mallval väljer du mallen "kakao app".

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
1. Stäng Xcode-projektet. Du kommer att använda en annan instans av det senare när du har konfigurerat CocoaPods.

## <a name="add-the-sample-code"></a>Lägga till exempelkoden

1. Placera en ny rubrik fil med namnet `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` i `helloworld` Directory i projektet HelloWorld och klistra in följande kod i den:

   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/text-to-speech/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. Lägg till den relativa sökvägen `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` till interimskontot för att skapa Swift-projekt för målet HelloWorld i huvud fältet *mål-C-bryggning* ![rubrik egenskaper](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-macos-bridging-header.png)
1. Ersätt innehållet i den automatiskt genererade `AppDelegate.swift`-filen genom att göra följande:

   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/text-to-speech/helloworld/helloworld/AppDelegate.swift#code)]
1. I `AppDelegate.swift`ersätter du strängen `YourSubscriptionKey` med din prenumerations nyckel.
1. Ersätt strängen `YourServiceRegion` med den region som är associerad med din prenumeration (till exempel `westus` för den kostnads fria utvärderings prenumerationen).

## <a name="install-the-sdk-as-a-cocoapod"></a>Installera SDK som en CocoaPod

1. Installera CocoaPod-beroende hanteraren enligt beskrivningen i [installations anvisningarna](https://guides.cocoapods.org/using/getting-started.html).
1. Navigera till katalogen för din exempel App (`helloworld`). Placera en textfil med namnet `Podfile` och följande innehåll i den katalogen:

   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/text-to-speech/helloworld/Podfile)]
1. Navigera till `helloworld` Directory i en Terminal och kör kommandot `pod install`. Detta genererar en `helloworld.xcworkspace` Xcode-arbetsyta som innehåller både exempel-appen och tal-SDK som ett beroende. Den här arbets ytan kommer att användas i följande.

## <a name="build-and-run-the-sample"></a>Skapa och köra exempelappen

1. Öppna arbets ytan `helloworld.xcworkspace` i Xcode.
1. Gör felsökningsresultatet synligt (**View** > **Debug Area** > **Activate Console**) (Visa > Felsökningsområde > Aktivera konsol).
1. Skapa och kör exempel koden genom att välja **produkt** > att **köra** på menyn eller genom att klicka på **uppspelnings** knappen.
1. När du har matat in text och klickat på knappen i appen bör du höra det syntetiskt spelade ljudet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska våra exempel på GitHub](https://aka.ms/csspeech/samples)

