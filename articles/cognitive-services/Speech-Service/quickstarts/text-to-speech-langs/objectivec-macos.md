---
title: 'Snabbstart: Syntetisera tal, Objective-C - Taltjänst'
titleSuffix: Azure Cognitive Services
description: Lär dig hur du syntetiserar tal i Objective-C på macOS med tal-SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: 37eed03ed839411f1acf5d963d4118a3c6d2c379
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975932"
---
# <a name="quickstart-synthesize-speech-in-objective-c-on-macos-using-the-speech-sdk"></a>Snabbstart: Syntetisera tal i Objective-C på macOS med tal-SDK

I den här artikeln får du lära dig hur du skapar en macOS-app i Objective-C med hjälp av Cognitive Services Speech SDK för att syntetisera tal från text och spela upp det med standardljudutdata.

## <a name="prerequisites"></a>Krav

Gå igenom den här listan med förhandskrav innan du sätter igång:

* En [prenumerationsnyckel](~/articles/cognitive-services/Speech-Service/get-started.md) för taltjänsten
* En macOS-maskin med [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) eller senare och macOS 10.13 eller senare

## <a name="get-the-speech-sdk-for-macos"></a>Hämta Tal SDK för macOS

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Observera att den här självstudien inte fungerar med versionen av SDK tidigare än 1.7.0.

Cognitive Services Speech SDK för Mac distribueras som ett rampaket.
Det kan användas i Xcode projekt som en [CocoaPod](https://cocoapods.org/), eller laddas ner från https://aka.ms/csspeech/macosbinary och länkas manuellt. Denna guide använder en CocoaPod.

## <a name="create-an-xcode-project"></a>Skapa ett Xcode-projekt

Starta Xcode och starta ett nytt projekt genom att klicka på **Arkiv** > **nytt** > **projekt**.
Välj mallen "Cocoa App" i dialogrutan för mallval.

I dialogrutorna som följer gör du följande val:

1. Dialogrutan Project Options (Projektalternativ)
    1. Ange namnet på snabbstartsappen, till exempel `helloworld`.
    1. Ange ett lämpligt organisationsnamn och en organisationsidentifierare om du redan har ett Apple-utvecklarkonto. I testsyfte kan du välja vilket namn som helst, till exempel `testorg`. För att signera appen behöver du en korrekt etableringsprofil. Information finns på [Apple Developer-webbplatsen](https://developer.apple.com/).
    1. Kontrollera att Objective-C har valts som språk för projektet.
    1. Inaktivera kryssrutorna om du vill använda storyboards och skapa ett dokumentbaserat program. Det enkla användargränssnittet för exempelappen skapas programmässigt.
    1. Inaktivera alla kryssrutor för test och kärndata.
    ![Projektinställningar](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-project-settings.png)
1. Välja projektkatalog
    1. Välj en katalog som projektet ska användas. Då skapas en `helloworld`-katalog i arbetskatalogen som innehåller alla filer för Xcode-projektet.
    1. Inaktivera skapandet av en Git-lagringsplats för det här exempelprojektet.
1. Ange berättiganden för nätverksåtkomst. Klicka på appnamnet på den första raden i översikten till vänster för att komma till appkonfigurationen och välj sedan fliken "Funktioner".
    1. Aktivera inställningen "Appsandlåda" för appen.
    1. Aktivera kryssrutorna för åtkomst till utgående anslutningar.
    ![Inställningar för begränsat läge](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-sandbox-tts.png)
1. Stäng Xcode-projektet. Du kommer att använda en annan instans av det senare efter att ha ställt in CocoaPods.

## <a name="install-the-sdk-as-a-cocoapod"></a>Installera SDK som en CocoaPod

1. Installera CocoaPod beroende manager som beskrivs i dess [installationsanvisningar](https://guides.cocoapods.org/using/getting-started.html).
1. Navigera till katalogen för`helloworld`exempelappen ( ). Placera en textfil `Podfile` med namnet och följande innehåll i den katalogen:  
   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/text-to-speech/helloworld/Podfile)]
1. Navigera till `helloworld` katalogen i en `pod install`terminal och kör kommandot . Detta genererar `helloworld.xcworkspace` en Xcode-arbetsyta som innehåller både exempelappen och Tal-SDK som ett beroende. Den här arbetsytan kommer att användas i följande.

## <a name="add-the-sample-code"></a>Lägga till exempelkoden

1. Öppna `helloworld.xcworkspace` arbetsytan i Xcode.
1. Ersätt innehållet i den automatiskt genererade `AppDelegate.m`-filen genom att göra följande:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/text-to-speech/helloworld/helloworld/AppDelegate.m#code)]
1. Ersätt strängen `YourSubscriptionKey` med din prenumerationsnyckel.
1. Ersätt strängen `YourServiceRegion` med den [region](~/articles/cognitive-services/Speech-Service/regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

## <a name="build-and-run-the-sample"></a>Skapa och köra exempelappen

1. Gör felsökningsutdata synliga (**Visa** > **felsökningsområde** > **Aktivera konsol**).
1. Skapa och kör exempelkoden genom att välja -> **Produktkörning** på menyn eller klicka på knappen **Spela** upp. **Product**
1. När du har matat in text och klickat på knappen i appen bör du höra det syntesljud som spelas upp.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska Objektive-C-exempel på GitHub](https://aka.ms/csspeech/samples)

