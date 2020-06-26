---
title: 'Snabb start: syntetiskt tal, mål-C-tal-tjänsten'
titleSuffix: Azure Cognitive Services
description: Lär dig mer om att syntetisera tal i mål-C på macOS med hjälp av talet SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/25/2020
ms.author: yulili
ms.openlocfilehash: 2c859965c951ad271b1b9e272ce60de64aa3d3d5
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85391374"
---
# <a name="quickstart-synthesize-speech-in-objective-c-on-macos-using-the-speech-sdk"></a>Snabb start: syntetisera tal i mål-C på macOS med hjälp av talet SDK

I den här artikeln får du lära dig hur du skapar en macOS-app i mål-C med hjälp av Cognitive Services tal-SDK för att syntetisera tal från text och spela upp det med standard ljud utmatningen.

## <a name="prerequisites"></a>Krav

Gå igenom den här listan med förhandskrav innan du sätter igång:

* En [prenumerations nyckel](~/articles/cognitive-services/Speech-Service/get-started.md) för tal tjänsten
* En macOS-dator med [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) eller senare och MacOS 10,13 eller senare

## <a name="get-the-speech-sdk-for-macos"></a>Hämta tal-SDK för macOS

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Observera att den här självstudien inte fungerar med tidigare version av SDK än 1.7.0.

Cognitive Services Speech SDK för Mac distribueras som ett Ramverks paket.
Den kan användas i Xcode-projekt som en [CocoaPod](https://cocoapods.org/)eller hämtas från https://aka.ms/csspeech/macosbinary och länkas manuellt. Den här guiden använder en CocoaPod.

## <a name="create-an-xcode-project"></a>Skapa ett Xcode-projekt

Starta Xcode och starta ett nytt projekt genom att klicka på **Arkiv**  >  **nytt**  >  **projekt**.
I dialog rutan Mallval väljer du mallen "kakao app".

I dialogrutorna som följer gör du följande val:

1. Dialogrutan Project Options (Projektalternativ)
    1. Ange namnet på snabbstartsappen, till exempel `helloworld`.
    1. Ange ett lämpligt organisations namn och en organisations identifierare om du redan har ett Apple Developer-konto. I testsyfte kan du välja vilket namn som helst, till exempel `testorg`. För att signera appen behöver du en korrekt etableringsprofil. Information finns på [Apple Developer-webbplatsen](https://developer.apple.com/).
    1. Kontrollera att Objective-C har valts som språk för projektet.
    1. Inaktivera kryss rutorna om du vill använda storyboards och skapa ett dokument baserat program. Det enkla användar gränssnittet för exempel programmet kommer att skapas program mässigt.
    1. Inaktivera alla kryssrutor för test och kärndata.
    ![Projektinställningar](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-project-settings.png)
1. Välja projektkatalog
    1. Välj en katalog där projektet ska läggas till. Då skapas en `helloworld`-katalog i arbetskatalogen som innehåller alla filer för Xcode-projektet.
    1. Inaktivera skapandet av en Git-lagringsplats för det här exempelprojektet.
1. Ange rättigheter för nätverks åtkomst. Klicka på namnet på appen i den första raden i översikten till vänster för att komma till appens konfiguration och välj sedan fliken funktioner.
    1. Aktivera inställningen "app Sandbox" för appen.
    1. Aktivera kryss rutorna för åtkomst till "utgående anslutningar".
    ![Sandbox-inställningar](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-sandbox-tts.png)
1. Stäng Xcode-projektet. Du kommer att använda en annan instans av det senare när du har konfigurerat CocoaPods.

## <a name="install-the-sdk-as-a-cocoapod"></a>Installera SDK som en CocoaPod

1. Installera CocoaPod-beroende hanteraren enligt beskrivningen i [installations anvisningarna](https://guides.cocoapods.org/using/getting-started.html).
1. Navigera till katalogen för din exempel App ( `helloworld` ). Placera en textfil med namnet `Podfile` och följande innehåll i katalogen:  
   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/text-to-speech/helloworld/Podfile)]
1. Navigera till `helloworld` katalogen i en Terminal och kör kommandot `pod install` . Detta skapar en `helloworld.xcworkspace` Xcode-arbetsyta som innehåller både exempel appen och tal-SDK som ett beroende. Den här arbets ytan kommer att användas i följande.

## <a name="add-the-sample-code"></a>Lägga till exempelkoden

1. Öppna `helloworld.xcworkspace` arbets ytan i Xcode.
1. Ersätt innehållet i den automatiskt genererade `AppDelegate.m`-filen genom att göra följande:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/text-to-speech/helloworld/helloworld/AppDelegate.m#code)]
1. Ersätt strängen `YourSubscriptionKey` med din prenumerationsnyckel.
1. Ersätt strängen `YourServiceRegion` med den [region](~/articles/cognitive-services/Speech-Service/regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

## <a name="build-and-run-the-sample"></a>Skapa och köra exempelappen

1. Gör fel söknings resultatet synligt (**Visa**  >  **fel söknings ytan**  >  **Aktivera konsol**).
1. Skapa och kör exempel koden genom att välja **produkt**  ->  **körning** på menyn eller genom att klicka på **uppspelnings** knappen.
1. När du har matat in text och klickat på knappen i appen bör du höra det syntetiskt spelade ljudet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska Objektive-C-exempel på GitHub](https://aka.ms/csspeech/samples)

