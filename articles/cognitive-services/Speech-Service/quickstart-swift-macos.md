---
title: 'Snabbstart: Recognize speech, Swift - Speech Services'
titleSuffix: Azure Cognitive Services
description: Lär dig att känna igen tal i Swift på macOS med hjälp av tal-SDK
services: cognitive-services
author: cbasoglu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/28/2019
ms.author: cbasoglu
ms.openlocfilehash: d81384c4a3b4691f179dc2b11330b25fe7fb42bb
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561519"
---
# <a name="quickstart-recognize-speech-in-swift-on-macos-using-the-speech-sdk"></a>Snabbstart: Känna igen tal i Swift på macOS med hjälp av tal-SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

I den här artikeln lär du dig att skapa en macOS-app i Swift som använde Cognitive Services tal SDK för att transkribera tal som registrerats från en mikrofon till text.

## <a name="prerequisites"></a>Förutsättningar

Gå igenom den här listan med förhandskrav innan du sätter igång:

* En [prenumerationsnyckel](get-started.md) för Speech-tjänsten.
* En macOS-dator med [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) eller senare och [CocoaPods](https://cocoapods.org/) installerad.

## <a name="get-the-speech-sdk-for-ios"></a>Skaffa Speech SDK för iOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Den aktuella versionen av Cognitive Services Speech SDK är `1.6.0`. Observera att den här självstudien inte fungerar utan ändringar för en tidigare version av SDK: N.

Cognitive Services tal SDK för macOS distribueras som ett framework-paket.
Det kan användas i Xcode-projekt som en [CocoaPod](https://cocoapods.org/), eller hämtats från https://aka.ms/csspeech/macosbinary och länkad manuellt. Den här guiden använder en CocoaPod.

## <a name="create-an-xcode-project"></a>Skapa en Xcode-projekt

Starta Xcode och starta ett nytt projekt genom att klicka på **File** > **New** > **Project** (Arkiv > Nytt > Projekt).
I dialogrutan för mallval väljer du mallen ”iOS Single View App”.

I dialogrutorna som följer gör du följande val:

1. Dialogrutan Project Options (Projektalternativ)
    1. Ange namnet på snabbstartsappen, till exempel `helloworld`.
    1. Ange en lämplig organisationens namn och en organisations-ID om du redan har ett Apple developer-konto. I testsyfte kan du välja vilket namn som helst, till exempel `testorg`. För att signera appen behöver du en korrekt etableringsprofil. Information finns på [Apple Developer-webbplatsen](https://developer.apple.com/).
    1. Kontrollera att Swift väljs som språk för projektet.
    1. Inaktivera kryssrutorna att använda återgivningar som genereras och skapa ett dokument-baserade program. Enkel Gränssnittet för exempelappen skapas programmässigt.
    1. Inaktivera alla kryssrutor för test och kärndata.
1. Välja projektkatalog
    1. Välj en katalog för att placera projektet i. Detta skapar en `helloworld` katalogen i den valda katalogen som innehåller alla filer för Xcode-projekt.
    1. Inaktivera skapandet av en Git-lagringsplats för det här exempelprojektet.
1. Appen måste också att deklarera användning av mikrofonen i den `Info.plist` filen. Klicka på filen i översikten och Lägg till nyckel för ”sekretess – mikrofon användning beskrivning”, med ett värde som ”mikrofon krävs för taligenkänning”.
    ![Inställningarna i filen Info.plist](media/sdk/qs-swift-ios-info-plist.png)
1. Stäng Xcode-projekt. Du använder en annan instans av det senare när du har installerat CocoaPods.

## <a name="add-the-sample-code"></a>Lägga till exempelkoden

1. Placera en ny rubrikfil med namnet `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` till den `helloworld` katalogen i projektet helloworld och klistra in följande kod till den: [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. Lägg till den relativa sökvägen `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` till datacenterbryggning rubrik till i Swift projektinställningar för helloworld-mål i den *Interimshuvudfilen med Objective-C* fältet ![egenskaper för sidhuvud](media/sdk/qs-swift-ios-bridging-header.png)
1. Ersätt innehållet i den automatiskt genererade `AppDelegate.swift` genom att: [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/AppDelegate.swift#code)]
1. Ersätt innehållet i den automatiskt genererade `ViewController.swift` genom att: [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/ViewController.swift#code)]
1. I `ViewController.swift`, Ersätt strängen `YourSubscriptionKey` med din prenumerationsnyckel.
1. Ersätt strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

## <a name="install-the-sdk-as-a-cocoapod"></a>Installera SDK: N som en CocoaPod

1. Installera Beroendehanteraren CocoaPod enligt beskrivningen i dess [Installationsinstruktioner](https://guides.cocoapods.org/using/getting-started.html).
1. Gå till katalogen för din exempelapp (`helloworld`). Placera en textfil med namnet `Podfile` och följande innehåll i katalogen: [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/Podfile)]
1. Navigera till den `helloworld` katalogen i en terminal och kör kommandot `pod install`. Detta genererar en `helloworld.xcworkspace` Xcode-arbetsyta som innehåller både exempelappen och SDK tal som ett beroende. Den här arbetsytan kommer att användas i följande.

## <a name="build-and-run-the-sample"></a>Skapa och köra exempelappen

1. Öppna den `helloworld.xcworkspace` arbetsyta i Xcode.
1. Gör felsökningsresultatet synligt (**View** > **Debug Area** > **Activate Console**) (Visa > Felsökningsområde > Aktivera konsol).
1. Välj den iOS-simulatorn eller en iOS-enhet är ansluten till din utvecklingsdator som mål för appen från listan i den **produkten** > **mål** menyn.
1. Skapa och kör exempelkoden i iOS-simulatorn genom att välja **Product** > **Run** (Produkt > Kör) på menyn eller genom att klicka på **uppspelningsknappen**.
1. När du klickar på ”identifiera” i appen och säg några ord, bör du se den text som du har talat på den nedre delen av skärmen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska våra exempel på GitHub](https://aka.ms/csspeech/samples)

