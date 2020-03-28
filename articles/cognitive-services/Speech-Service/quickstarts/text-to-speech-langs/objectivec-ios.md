---
title: 'Snabbstart: Syntetisera tal, Objective-C - Taltjänst'
titleSuffix: Azure Cognitive Services
description: Lär dig hur du syntetiserar tal i Objective-C på iOS med tal-SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: 1f5a569f9c3bfa91b78c836e37a22642238674be
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975949"
---
# <a name="quickstart-synthesize-speech-in-objective-c-on-ios-using-the-speech-sdk"></a>Snabbstart: Syntetisera tal i Objective-C på iOS med tal-SDK

I den här artikeln får du lära dig hur du skapar en iOS-app i Objective-C med hjälp av Cognitive Services Speech SDK för att syntetisera tal från text.

## <a name="prerequisites"></a>Krav

Gå igenom den här listan med förhandskrav innan du sätter igång:

* En [prenumerationsnyckel](~/articles/cognitive-services/Speech-Service/get-started.md) för taltjänsten
* En macOS-dator med [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) eller senare
* Målet är inställt på iOS version 9.3 eller senare

## <a name="get-the-speech-sdk-for-ios"></a>Skaffa Speech SDK för iOS

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Observera att den här självstudien inte fungerar med versionen av SDK tidigare än 1.7.0.

Cognitive Services Speech SDK för iOS distribueras för närvarande som ett kakaoramverk.
Det kan användas i Xcode projekt som en [CocoaPod](https://cocoapods.org/), eller laddas ner från https://aka.ms/csspeech/iosbinary och länkas manuellt. Denna guide använder en CocoaPod.

## <a name="create-an-xcode-project"></a>Skapa ett Xcode-projekt

Starta Xcode och starta ett nytt projekt genom att klicka på **Arkiv** > **nytt** > **projekt**.
I dialogrutan för mallval väljer du mallen ”iOS Single View App”.

I dialogrutorna som följer gör du följande val:

1. Dialogrutan Project Options (Projektalternativ)
    1. Ange namnet på snabbstartsappen, till exempel `helloworld`.
    1. Ange ett lämpligt organisationsnamn och organisations-ID, om du redan har ett Apple Developer-konto. I testsyfte kan du välja vilket namn som helst, till exempel `testorg`. För att signera appen behöver du en korrekt etableringsprofil. Information finns på [Apple Developer-webbplatsen](https://developer.apple.com/).
    1. Kontrollera att Objective-C har valts som språk för projektet.
    1. Inaktivera alla kryssrutor för test och kärndata.
    ![Projektinställningar](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-project-settings.png)
1. Välja projektkatalog
    1. Välj arbetskatalogen och spara projektet där. Då skapas en `helloworld`-katalog i arbetskatalogen som innehåller alla filer för Xcode-projektet.
    1. Inaktivera skapandet av en Git-lagringsplats för det här exempelprojektet.

## <a name="install-the-sdk-as-a-cocoapod"></a>Installera SDK som en CocoaPod

1. Installera CocoaPod beroende manager som beskrivs i dess [installationsanvisningar](https://guides.cocoapods.org/using/getting-started.html).
1. Navigera till katalogen för`helloworld`exempelappen ( ). Placera en textfil `Podfile` med namnet och följande innehåll i den katalogen:  
   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/Podfile)]
1. Navigera till `helloworld` katalogen i en `pod install`terminal och kör kommandot . Detta genererar `helloworld.xcworkspace` en Xcode-arbetsyta som innehåller både exempelappen och Tal-SDK som ett beroende. Den här arbetsytan kommer att användas i följande.

## <a name="add-the-sample-code"></a>Lägga till exempelkoden

1. Öppna `helloworld.xcworkspace` arbetsytan i Xcode.
1. Ersätt innehållet i den automatiskt genererade `AppDelegate.m`-filen genom att göra följande:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/helloworld/AppDelegate.m#code)]
1. Ersätt innehållet i den automatiskt genererade `ViewController.m`-filen genom att göra följande:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/text-to-speech/helloworld/helloworld/ViewController.m#code)]
1. Ersätt strängen `YourSubscriptionKey` med din prenumerationsnyckel.
1. Ersätt strängen `YourServiceRegion` med den [region](~/articles/cognitive-services/Speech-Service/regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

## <a name="build-and-run-the-sample"></a>Skapa och köra exempelappen

1. Gör felsökningsutdata synliga (**Visa** > **felsökningsområde** > **Aktivera konsol**).
1. Välj antingen iOS-simulatorn eller en iOS-enhet som är ansluten till utvecklingsdatorn som mål för appen i listan på menyn > **Produktmål.** **Product**
1. Skapa och kör exempelkoden i iOS-simulatorn genom att välja **Play** **Produktkörning** > **Run** på menyn eller klicka på knappen Spela upp.

   ![Simulerad iOS-app](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-simulated-app-tts.png)

1. När du har matat in text och klickat på knappen i appen bör du höra det syntesljud som spelas upp.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska Objektive-C-exempel på GitHub](https://aka.ms/csspeech/samples)

