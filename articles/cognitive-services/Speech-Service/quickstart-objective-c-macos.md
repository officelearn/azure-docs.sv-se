---
title: 'Snabbstart: Identifiera tal, Objective-C – Speech Service'
titleSuffix: Azure Cognitive Services
description: Lär dig att känna igen tal i Objective-C på macOS med hjälp av tal-SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/03/2019
ms.author: chlandsi
ms.openlocfilehash: 55fc671d926880375b0420e0eafb6dc63f170ba6
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2019
ms.locfileid: "59012201"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-macos-using-the-speech-sdk"></a>Snabbstart: Känna igen tal i Objective-C på macOS med hjälp av tal-SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

I den här artikeln lär du dig att skapa en macOS-app i Objective-C med hjälp av Cognitive Services tal SDK för att transkribera tal som registrerats från en mikrofon till text.

## <a name="prerequisites"></a>Förutsättningar

Gå igenom den här listan med förhandskrav innan du sätter igång:

* En [prenumerationsnyckel](get-started.md) för Speech Service
* En macOS-dator med [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) eller senare och macOS 10.13 eller senare

## <a name="get-the-speech-sdk-for-macos"></a>Hämta SDK tal för macOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Den aktuella versionen av Cognitive Services Speech SDK är `1.4.0`.

Cognitive Services tal SDK för Mac distribueras som ett framework-paket.
Det kan användas i Xcode-projekt som en [CocoaPod](https://cocoapods.org/), eller hämtats från https://aka.ms/csspeech/macosbinary och länkad manuellt. Den här guiden använder en CocoaPod.

## <a name="create-an-xcode-project"></a>Skapa en Xcode-projekt

Starta Xcode och starta ett nytt projekt genom att klicka på **File** > **New** > **Project** (Arkiv > Nytt > Projekt).
I dialogrutan för val av mallen väljer du mallen ”Cocoa App”.

I dialogrutorna som följer gör du följande val:

1. Dialogrutan Project Options (Projektalternativ)
    1. Ange namnet på snabbstartsappen, till exempel `helloworld`.
    1. Ange en lämplig organisationens namn och en organisations-ID om du redan har ett Apple developer-konto. I testsyfte kan du välja vilket namn som helst, till exempel `testorg`. För att signera appen behöver du en korrekt etableringsprofil. Information finns på [Apple Developer-webbplatsen](https://developer.apple.com/).
    1. Kontrollera att Objective-C har valts som språk för projektet.
    1. Inaktivera kryssrutorna att använda återgivningar som genereras och skapa ett dokument-baserade program. Enkel Gränssnittet för exempelappen skapas programmässigt.
    1. Inaktivera alla kryssrutor för test och kärndata.
    ![Projektinställningar](media/sdk/qs-objectivec-macos-project-settings.png)
1. Välja projektkatalog
    1. Välj en katalog för att placera projektet i. Då skapas en `helloworld`-katalog i arbetskatalogen som innehåller alla filer för Xcode-projektet.
    1. Inaktivera skapandet av en Git-lagringsplats för det här exempelprojektet.
1. Ställa in rättigheter för Nätverks- och mikrofon åtkomst. Klicka på appnamnet på den första raden i Översikt till vänster för att komma till appkonfigurationen och välj sedan fliken ”funktioner”.
    1. Aktivera inställningen ”sandlåda App” för appen.
    1. Aktivera kryssrutorna för ”utgående anslutningar” och ”mikrofon” åtkomst.
    ![Sandbox-inställningar](media/sdk/qs-objectivec-macos-sandbox.png)
1. Appen måste också att deklarera användning av mikrofonen i den `Info.plist` filen. Klicka på filen i översikten och Lägg till nyckel för ”sekretess – mikrofon användning beskrivning”, med ett värde som ”mikrofon krävs för taligenkänning”.
    ![Inställningarna i filen Info.plist](media/sdk/qs-objectivec-macos-info-plist.png)
1. Stäng Xcode-projekt. Du använder en annan instans av det senare när du har installerat CocoaPods.

## <a name="install-the-sdk-as-a-cocoapod"></a>Installera SDK: N som en CocoaPod

1. Installera Beroendehanteraren CocoaPod enligt beskrivningen i dess [Installationsinstruktioner](https://guides.cocoapods.org/using/getting-started.html).
1. Gå till katalogen för din exempelapp (`helloworld`). Placera en textfil med namnet `Podfile` och följande innehåll i katalogen:
    ```
    target 'helloworld' do
        platform :osx, '10.13'
        pod 'MicrosoftCognitiveServicesSpeech-macOS', '~> 1.4.0'
    end
    ```
1. Navigera till den `helloworld` katalogen i en terminal och kör kommandot `pod install`. Detta genererar en `helloworld.xcworkspace` Xcode-arbetsyta som innehåller både exempelappen och SDK tal som ett beroende. Den här arbetsytan kommer att användas i följande.

## <a name="add-the-sample-code"></a>Lägga till exempelkoden

1. Öppna den `helloworld.xcworkspace` arbetsyta i Xcode.
1. Ersätt innehållet i den automatiskt genererade `AppDelegate.m` genom att: [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-macos/helloworld/helloworld/AppDelegate.m#code)]
1. Ersätt strängen `YourSubscriptionKey` med din prenumerationsnyckel.
1. Ersätt strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

## <a name="build-and-run-the-sample"></a>Skapa och köra exempelappen

1. Gör felsökningsresultatet synligt (**View** > **Debug Area** > **Activate Console**) (Visa > Felsökningsområde > Aktivera konsol).
1. Skapa och kör exempelkoden genom att välja **produkten** -> **kör** från menyn eller klicka på den **spela upp** knappen.
1. När du klickar du på knappen och säg några ord, bör du se den text som du har talat på den nedre delen av skärmen. När du kör appen för första gången ska du uppmanas att ge appen åtkomst till datorns mikrofon.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska Objective-C-exempel på GitHub](https://aka.ms/csspeech/samples)

