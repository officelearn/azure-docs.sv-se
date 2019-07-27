---
title: 'Snabbstart: Identifiera tal-och mål-C-tal-tjänsten'
titleSuffix: Azure Cognitive Services
description: Lär dig att känna igen tal i mål-C på macOS med hjälp av tal-SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 45cd1210ee6af3c456171a427729f6e16caf2d58
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559363"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-macos-using-the-speech-sdk"></a>Snabbstart: Identifiera tal i mål-C på macOS med hjälp av tal-SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

I den här artikeln får du lära dig hur du skapar en macOS-app i mål-C med Cognitive Services Speech SDK för att skriva om tal som registrerats från en mikrofon till text.

## <a name="prerequisites"></a>Förutsättningar

Gå igenom den här listan med förhandskrav innan du sätter igång:

* En [prenumerationsnyckel](get-started.md) för Speech Service
* En macOS-dator med [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) eller senare och MacOS 10,13 eller senare

## <a name="get-the-speech-sdk-for-macos"></a>Hämta tal-SDK för macOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Den aktuella versionen av Cognitive Services Speech SDK är `1.6.0`.

Cognitive Services Speech SDK för Mac distribueras som ett Ramverks paket.
Den kan användas i Xcode-projekt som en [CocoaPod](https://cocoapods.org/)eller hämtas från https://aka.ms/csspeech/macosbinary och länkas manuellt. Den här guiden använder en CocoaPod.

## <a name="create-an-xcode-project"></a>Skapa ett Xcode-projekt

Starta Xcode och starta ett nytt projekt genom att klicka på **File** > **New** > **Project** (Arkiv > Nytt > Projekt).
I dialog rutan Mallval väljer du mallen "kakao app".

I dialogrutorna som följer gör du följande val:

1. Dialogrutan Project Options (Projektalternativ)
    1. Ange namnet på snabbstartsappen, till exempel `helloworld`.
    1. Ange ett lämpligt organisations namn och en organisations identifierare om du redan har ett Apple Developer-konto. I testsyfte kan du välja vilket namn som helst, till exempel `testorg`. För att signera appen behöver du en korrekt etableringsprofil. Information finns på [Apple Developer-webbplatsen](https://developer.apple.com/).
    1. Kontrollera att Objective-C har valts som språk för projektet.
    1. Inaktivera kryss rutorna om du vill använda storyboards och skapa ett dokument baserat program. Det enkla användar gränssnittet för exempel programmet kommer att skapas program mässigt.
    1. Inaktivera alla kryssrutor för test och kärndata.
    ![Projektinställningar](media/sdk/qs-objectivec-macos-project-settings.png)
1. Välja projektkatalog
    1. Välj en katalog där projektet ska läggas till. Då skapas en `helloworld`-katalog i arbetskatalogen som innehåller alla filer för Xcode-projektet.
    1. Inaktivera skapandet av en Git-lagringsplats för det här exempelprojektet.
1. Ange rättigheter för nätverks-och mikrofon åtkomst. Klicka på namnet på appen i den första raden i översikten till vänster för att komma till appens konfiguration och välj sedan fliken funktioner.
    1. Aktivera inställningen "app Sandbox" för appen.
    1. Aktivera kryss rutorna för åtkomst till "utgående anslutningar" och "mikrofon".
    ![Sandbox-inställningar](media/sdk/qs-objectivec-macos-sandbox.png)
1. Appen måste också deklarera användningen av mikrofonen i `Info.plist` filen. Klicka på filen i översikten och Lägg till nyckeln "sekretess-mikrofon användning Beskrivning" med ett värde som "mikrofon krävs för tal igenkänning".
    ![Inställningar i info. plist](media/sdk/qs-objectivec-macos-info-plist.png)
1. Stäng Xcode-projektet. Du kommer att använda en annan instans av det senare när du har konfigurerat CocoaPods.

## <a name="install-the-sdk-as-a-cocoapod"></a>Installera SDK som en CocoaPod

1. Installera CocoaPod-beroende hanteraren enligt beskrivningen i [installations anvisningarna](https://guides.cocoapods.org/using/getting-started.html).
1. Navigera till katalogen för din exempel App (`helloworld`). Placera en textfil med namnet `Podfile` och följande innehåll i katalogen:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-macos/helloworld/Podfile)]
1. Navigera till `helloworld` katalogen i en Terminal och kör kommandot `pod install`. Detta skapar en `helloworld.xcworkspace` Xcode-arbetsyta som innehåller både exempel appen och tal-SDK som ett beroende. Den här arbets ytan kommer att användas i följande.

## <a name="add-the-sample-code"></a>Lägga till exempelkoden

1. `helloworld.xcworkspace` Öppna arbets ytan i Xcode.
1. Ersätt innehållet i den automatiskt genererade `AppDelegate.m`-filen genom att göra följande:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-macos/helloworld/helloworld/AppDelegate.m#code)]
1. Ersätt strängen `YourSubscriptionKey` med din prenumerationsnyckel.
1. Ersätt strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

## <a name="build-and-run-the-sample"></a>Skapa och köra exempelappen

1. Gör felsökningsresultatet synligt (**View** > **Debug Area** > **Activate Console**) (Visa > Felsökningsområde > Aktivera konsol).
1. Skapa och kör exempel koden genom att välja **produkt** > **körning** på menyn eller genom att klicka  på uppspelnings knappen.
1. När du klickar på knappen och säger några ord bör du se texten du har talat om i den nedre delen av skärmen. När du kör appen för första gången ska du uppmanas att ge appen åtkomst till datorns mikrofon.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska Objektive-C-exempel på GitHub](https://aka.ms/csspeech/samples)
