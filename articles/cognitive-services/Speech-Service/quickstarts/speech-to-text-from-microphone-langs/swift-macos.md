---
title: 'Snabb start: identifiera tal, tjänsten Swift-tal (macOS)'
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar en app för att identifiera tal i Swift för en macOS-dator med hjälp av Cognitive Services Speech SDK.
services: cognitive-services
author: cbasoglu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/25/2020
ms.author: cbasoglu
ms.openlocfilehash: 68947446d18ce1632b63ac3ba27311f648f3deea
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86524122"
---
# <a name="quickstart-recognize-speech-in-swift-on-macos-using-the-speech-sdk"></a>Snabb start: identifiera tal i SWIFT på macOS med hjälp av tal-SDK

Snabb Starter är också tillgängliga för [tal syntes](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/swift-macos.md).

I den här artikeln får du lära dig hur du skapar en macOS-app i Swift med Cognitive Services Speech SDK för att skriva om tal som registrerats från en mikrofon till text.

## <a name="prerequisites"></a>Förutsättningar

Gå igenom den här listan med förhandskrav innan du sätter igång:

* En [prenumerations nyckel](~/articles/cognitive-services/Speech-Service/get-started.md) för tal tjänsten.
* En macOS-dator med [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) eller senare och [CocoaPods](https://cocoapods.org/) installerad.

## <a name="get-the-speech-sdk-for-macos"></a>Hämta tal-SDK för macOS

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Observera att den här självstudien inte fungerar med tidigare version av SDK än 1.6.0.

Cognitive Services Speech SDK för macOS distribueras som ett Ramverks paket.
Den kan användas i Xcode-projekt som en [CocoaPod](https://cocoapods.org/)eller hämtas från https://aka.ms/csspeech/macosbinary och länkas manuellt. Den här guiden använder en CocoaPod.

## <a name="create-an-xcode-project"></a>Skapa ett Xcode-projekt

Starta Xcode och starta ett nytt projekt genom att klicka på **Arkiv**  >  **nytt**  >  **projekt**.
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
1. Ange rättigheter för nätverks-och mikrofon åtkomst. Klicka på namnet på appen i den första raden i översikten till vänster för att komma till appens konfiguration och välj sedan fliken funktioner.
    1. Aktivera inställningen "app Sandbox" för appen.
    1. Aktivera kryss rutorna för åtkomst till "utgående anslutningar" och "mikrofon".
    ![Sandbox-inställningar](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-macos-sandbox.png)
1. Appen måste också deklarera användningen av mikrofonen i `Info.plist` filen. Klicka på filen i översikten och Lägg till nyckeln "sekretess-mikrofon användning Beskrivning" med ett värde som "mikrofon krävs för tal igenkänning".
    ![Inställningar i info. plist](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-macos-info-plist.png)
1. Stäng Xcode-projektet. Du kommer att använda en annan instans av det senare när du har konfigurerat CocoaPods.

## <a name="add-the-sample-code"></a>Lägga till exempelkoden

1. Placera en ny rubrik fil med namnet `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` i katalogen i `helloworld` projektet HelloWorld och klistra in följande kod i den:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/from-microphone/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. Lägg till den relativa sökvägen `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` till interimskontot för överbryggning i Swift-projektfilen för målet HelloWorld i *rubrik egenskaper för mål-C-bryggning* ![](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-macos-bridging-header.png)
1. Ersätt innehållet i den automatiskt genererade `AppDelegate.swift`-filen genom att göra följande:

   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/from-microphone/helloworld/helloworld/AppDelegate.swift#code)]
1. I `AppDelegate.swift` ersätter du strängen `YourSubscriptionKey` med din prenumerations nyckel.
1. Ersätt strängen `YourServiceRegion` med den region som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

## <a name="install-the-sdk-as-a-cocoapod"></a>Installera SDK som en CocoaPod

1. Installera CocoaPod-beroende hanteraren enligt beskrivningen i [installations anvisningarna](https://guides.cocoapods.org/using/getting-started.html).
1. Navigera till katalogen för din exempel App ( `helloworld` ). Placera en textfil med namnet `Podfile` och följande innehåll i katalogen:

   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/from-microphone/helloworld/Podfile)]
1. Navigera till `helloworld` katalogen i en Terminal och kör kommandot `pod install` . Detta skapar en `helloworld.xcworkspace` Xcode-arbetsyta som innehåller både exempel appen och tal-SDK som ett beroende. Den här arbets ytan kommer att användas i följande.

## <a name="build-and-run-the-sample"></a>Skapa och köra exempelappen

1. Öppna `helloworld.xcworkspace` arbets ytan i Xcode.
1. Gör fel söknings resultatet synligt (**Visa**  >  **fel söknings ytan**  >  **Aktivera konsol**).
1. Skapa och kör exempel koden genom att välja **produkt**  >  **körning** på menyn eller genom att klicka på **uppspelnings** knappen.
1. När du klickar på knappen "identifiera" i appen och säger några ord bör du se texten du har talat i den nedre delen av app-fönstret.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska våra exempel på GitHub](https://aka.ms/csspeech/samples)

