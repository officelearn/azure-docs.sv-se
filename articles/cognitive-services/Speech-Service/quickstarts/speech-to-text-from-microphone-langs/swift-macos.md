---
title: 'Snabbstart: Känna igen tal från en mikrofon, Swift - Taltjänst'
titleSuffix: Azure Cognitive Services
description: Lär dig hur du känner igen tal i Swift på macOS med tal-SDK
services: cognitive-services
author: cbasoglu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/23/2019
ms.author: cbasoglu
ms.openlocfilehash: 7c5611a142087cff06eefb0277b12ff786074e1a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446836"
---
# <a name="quickstart-recognize-speech-in-swift-on-macos-using-the-speech-sdk"></a>Snabbstart: Känna igen tal i Swift på macOS med tal-SDK

Snabbstarter finns också tillgängliga för [talsyntes](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/swift-macos.md).

I den här artikeln får du lära dig hur du skapar en macOS-app i Swift med cognitive services speech SDK för att transkribera tal som spelats in från en mikrofon till text.

## <a name="prerequisites"></a>Krav

Gå igenom den här listan med förhandskrav innan du sätter igång:

* En [prenumerationsnyckel](~/articles/cognitive-services/Speech-Service/get-started.md) för taltjänsten.
* En macOS-maskin med [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) eller senare och [CocoaPods](https://cocoapods.org/) installerade.

## <a name="get-the-speech-sdk-for-macos"></a>Hämta Tal SDK för macOS

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Observera att den här självstudien inte fungerar med versionen av SDK tidigare än 1.6.0.

Cognitive Services Speech SDK för macOS distribueras som ett rampaket.
Det kan användas i Xcode projekt som en [CocoaPod](https://cocoapods.org/), eller laddas ner från https://aka.ms/csspeech/macosbinary och länkas manuellt. Denna guide använder en CocoaPod.

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
1. Ange berättiganden för nätverks- och mikrofonåtkomst. Klicka på appnamnet på den första raden i översikten till vänster för att komma till appkonfigurationen och välj sedan fliken "Funktioner".
    1. Aktivera inställningen "Appsandlåda" för appen.
    1. Aktivera kryssrutorna för åtkomst till "Utgående anslutningar" och "Mikrofon".
    ![Inställningar för begränsat läge](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-macos-sandbox.png)
1. Appen måste också deklarera användningen av `Info.plist` mikrofonen i filen. Klicka på filen i översikten och lägg till nyckeln "Sekretess - Mikrofonanvändningsbeskrivning", med ett värde som "Mikrofon behövs för taligenkänning".
    ![Inställningar i Info.plist](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-macos-info-plist.png)
1. Stäng Xcode-projektet. Du kommer att använda en annan instans av det senare efter att ha ställt in CocoaPods.

## <a name="add-the-sample-code"></a>Lägga till exempelkoden

1. Placera en ny rubrikfil `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` med `helloworld` namnet i katalogen i helloworld-projektet och klistra in följande kod i den:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/from-microphone/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. Lägg till `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` den relativa sökvägen till brygghuvudet i Swift-projektinställningarna för helloworld-målet i egenskaperna *Mål-C-brygghuvudhuvudhuvudhuvud* ![](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-macos-bridging-header.png)
1. Ersätt innehållet i den automatiskt genererade `AppDelegate.swift`-filen genom att göra följande:

   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/from-microphone/helloworld/helloworld/AppDelegate.swift#code)]
1. Ersätt `AppDelegate.swift`strängen `YourSubscriptionKey` med prenumerationsnyckeln i .
1. Ersätt strängen `YourServiceRegion` med den region som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

## <a name="install-the-sdk-as-a-cocoapod"></a>Installera SDK som en CocoaPod

1. Installera CocoaPod beroende manager som beskrivs i dess [installationsanvisningar](https://guides.cocoapods.org/using/getting-started.html).
1. Navigera till katalogen för`helloworld`exempelappen ( ). Placera en textfil `Podfile` med namnet och följande innehåll i den katalogen:

   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/from-microphone/helloworld/Podfile)]
1. Navigera till `helloworld` katalogen i en `pod install`terminal och kör kommandot . Detta genererar `helloworld.xcworkspace` en Xcode-arbetsyta som innehåller både exempelappen och Tal-SDK som ett beroende. Den här arbetsytan kommer att användas i följande.

## <a name="build-and-run-the-sample"></a>Skapa och köra exempelappen

1. Öppna `helloworld.xcworkspace` arbetsytan i Xcode.
1. Gör felsökningsutdata synliga (**Visa** > **felsökningsområde** > **Aktivera konsol**).
1. Skapa och kör exempelkoden genom att välja > **Produktkörning** på menyn eller klicka på knappen **Spela** upp. **Product**
1. När du har klickat på knappen "Känn igen" i appen och säga några ord bör du se texten du har talat i den nedre delen av appfönstret.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska våra exempel på GitHub](https://aka.ms/csspeech/samples)

