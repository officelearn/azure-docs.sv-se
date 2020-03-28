---
title: 'Snabbstart: Syntetisera tal, Swift - Taltjänst'
titleSuffix: Azure Cognitive Services
description: Lär dig hur du syntetiserar tal i Swift på macOS med tal-SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: ea0f61ddd2c60d2806af0f6dcf97c7d2388335d0
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975881"
---
# <a name="quickstart-synthesize-speech-in-swift-on-macos-using-the-speech-sdk"></a>Snabbstart: Syntetisera tal i Swift på macOS med tal-SDK

I den här artikeln får du lära dig hur du skapar en macOS-app i Swift med cognitive services speech SDK för att syntetisera tal från text och spela upp det med standardljudutdata.

## <a name="prerequisites"></a>Krav

Gå igenom den här listan med förhandskrav innan du sätter igång:

* En [prenumerationsnyckel](~/articles/cognitive-services/Speech-Service/get-started.md) för taltjänsten.
* En macOS-maskin med [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) eller senare och [CocoaPods](https://cocoapods.org/) installerade.

## <a name="get-the-speech-sdk-for-macos"></a>Hämta Tal SDK för macOS

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Cognitive Services Speech SDK för macOS distribueras som ett rampaket.
Det kan användas i Xcode projekt som en [CocoaPod](https://cocoapods.org/), eller laddas ner från https://aka.ms/csspeech/macosbinary och länkas manuellt. Denna guide använder en CocoaPod.

> [!NOTE] 
> Den här självstudien fungerar inte med versioner av SDK tidigare än 1.7.0.

## <a name="create-an-xcode-project"></a>Skapa ett Xcode-projekt

Starta Xcode och starta ett nytt projekt genom att klicka på **Arkiv** > **nytt** > **projekt**.
Välj mallen "Cocoa App" i dialogrutan för mallval.

I dialogrutorna som följer gör du följande val:

1. Dialogrutan Project Options (Projektalternativ)
    1. Ange namnet på snabbstartsappen, till exempel `helloworld`.
    1. Ange ett lämpligt organisationsnamn och en organisationsidentifierare om du redan har ett Apple-utvecklarkonto. I testsyfte kan du välja vilket namn som helst, till exempel `testorg`. För att signera appen behöver du en korrekt etableringsprofil. Information finns på [Apple Developer-webbplatsen](https://developer.apple.com/).
    1. Se till att Swift väljs som språk för projektet.
    1. Inaktivera kryssrutorna om du vill använda storyboards och skapa ett dokumentbaserat program. Det enkla användargränssnittet för exempelappen skapas programmässigt.
    1. Inaktivera alla kryssrutor för test och kärndata.
1. Välja projektkatalog
    1. Välj en katalog som projektet ska användas. Detta skapar `helloworld` en katalog i den valda katalogen som innehåller alla filer för Xcode-projektet.
    1. Inaktivera skapandet av en Git-lagringsplats för det här exempelprojektet.
1. Stäng Xcode-projektet. Du kommer att använda en annan instans av det senare efter att ha ställt in CocoaPods.

## <a name="add-the-sample-code"></a>Lägga till exempelkoden

1. Placera en ny rubrikfil `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` med `helloworld` namnet i katalogen i helloworld-projektet och klistra in följande kod i den:

   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/text-to-speech/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. Lägg till `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` den relativa sökvägen till brygghuvudet i Swift-projektinställningarna för helloworld-målet i egenskaperna *Mål-C-brygghuvudhuvudhuvudhuvud* ![](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-macos-bridging-header.png)
1. Ersätt innehållet i den automatiskt genererade `AppDelegate.swift`-filen genom att göra följande:

   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/text-to-speech/helloworld/helloworld/AppDelegate.swift#code)]
1. Ersätt `AppDelegate.swift`strängen `YourSubscriptionKey` med prenumerationsnyckeln i .
1. Ersätt strängen `YourServiceRegion` med den region som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

## <a name="install-the-sdk-as-a-cocoapod"></a>Installera SDK som en CocoaPod

1. Installera CocoaPod beroende manager som beskrivs i dess [installationsanvisningar](https://guides.cocoapods.org/using/getting-started.html).
1. Navigera till katalogen för`helloworld`exempelappen ( ). Placera en textfil `Podfile` med namnet och följande innehåll i den katalogen:

   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/text-to-speech/helloworld/Podfile)]
1. Navigera till `helloworld` katalogen i en `pod install`terminal och kör kommandot . Detta genererar `helloworld.xcworkspace` en Xcode-arbetsyta som innehåller både exempelappen och Tal-SDK som ett beroende. Den här arbetsytan kommer att användas i följande.

## <a name="build-and-run-the-sample"></a>Skapa och köra exempelappen

1. Öppna `helloworld.xcworkspace` arbetsytan i Xcode.
1. Gör felsökningsutdata synliga (**Visa** > **felsökningsområde** > **Aktivera konsol**).
1. Skapa och kör exempelkoden genom att välja > **Produktkörning** på menyn eller klicka på knappen **Spela** upp. **Product**
1. När du har matat in text och klickat på knappen i appen bör du höra det syntesljud som spelas upp.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska våra exempel på GitHub](https://aka.ms/csspeech/samples)

