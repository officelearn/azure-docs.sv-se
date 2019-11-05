---
title: 'Snabb start: identifiera tal från en mikrofon, mål-C-tal-tjänsten'
titleSuffix: Azure Cognitive Services
description: Lär dig att känna igen tal i mål-C på macOS med hjälp av talet SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 0025e6e84a8a76ad3a332056f51f802ca95da23f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503593"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-macos-by-using-the-speech-sdk"></a>Snabb start: identifiera tal i mål-C på macOS med hjälp av tal-SDK

Snabb Starter är också tillgängliga för [tal syntes](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/objectivec-macos.md).

I den här artikeln får du lära dig hur du skapar en macOS-app i mål-C genom att använda Azure Cognitive Services Speech SDK för att skriva tal som registrerats från en mikrofon till text.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar måste du ha:

* En [prenumerations nyckel](~/articles/cognitive-services/Speech-Service/get-started.md) för tal tjänsten.
* En macOS-dator med [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) eller senare och MacOS 10,13 eller senare.

## <a name="get-the-speech-sdk-for-macos"></a>Hämta tal-SDK för macOS

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Cognitive Services Speech SDK för Mac distribueras som ett Ramverks paket. Den kan användas i Xcode-projekt som en [CocoaPod](https://cocoapods.org/) eller hämtas från https://aka.ms/csspeech/macosbinary och länkas manuellt. I den här artikeln används en CocoaPod.

## <a name="create-an-xcode-project"></a>Skapa ett Xcode-projekt

Starta Xcode och starta ett nytt projekt genom att välja **fil** > **nytt** > **projekt**. I dialog rutan Val av mall väljer du mallen för **kakao-app** .

Gör följande val i dialog rutorna som följer.

1. I dialog rutan **projekt alternativ** :
    1. Ange ett namn för snabb starts appen, till exempel *HelloWorld*.
    1. Ange ett lämpligt organisations namn och ett organisations-ID om du redan har ett Apple Developer-konto. I test syfte kan du använda ett namn som *testorg*. För att signera appen behöver du en korrekt etableringsprofil. Mer information finns på webbplatsen för [Apple Developer](https://developer.apple.com/).
    1. Kontrol lera att **mål-C** har valts som språk för projektet.
    1. Avmarkera kryss rutorna om du vill använda storyboards och skapa ett dokument baserat program. Det enkla användar gränssnittet för exempel programmet skapas program mässigt.
    1. Avmarkera alla kryss rutor för test-och kärn data.

    ![Projekt inställningar](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-project-settings.png)

1. Välj en projekt katalog:
    1. Välj en katalog där projektet ska läggas till. Det här steget skapar en HelloWorld-katalog i din arbets katalog som innehåller alla filer för Xcode-projektet.
    1. Inaktivera skapandet av en Git-lagringsplats för det här exempelprojektet.
1. Ange rättigheter för nätverks-och mikrofon åtkomst. Välj namnet på appen i den första raden i översikten till vänster för att komma till appens konfiguration. Välj sedan fliken **funktioner** .
    1. Aktivera **appens sandbox** -inställning för appen.
    1. Markera kryss rutorna för **utgående anslutningar** och **mikrofon** åtkomst.

    ![Sandbox-inställningar](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-sandbox.png)

1. Appen måste också deklarera användningen av mikrofonen i `Info.plist`-filen. Välj filen i översikten och Lägg till nyckeln för **användnings Beskrivning av sekretess-mikrofon** med ett värde som *mikrofon krävs för tal igenkänning*.

    ![Inställningar i info. plist](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-info-plist.png)

1. Stäng Xcode-projektet. Du använder en annan instans av det senare när du har konfigurerat CocoaPods.

## <a name="install-the-sdk-as-a-cocoapod"></a>Installera SDK som en CocoaPod

1. Installera CocoaPod-beroende hanteraren enligt beskrivningen i [installations anvisningarna](https://guides.cocoapods.org/using/getting-started.html).
1. Gå till katalogen för din exempel app, som är HelloWorld. Placera en textfil med namnet *Podfile* och följande innehåll i den katalogen:

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/from-microphone/helloworld/Podfile)]
1. Gå till katalogen HelloWorld i en Terminal och kör kommandot `pod install`. Det här kommandot genererar en `helloworld.xcworkspace` Xcode-arbetsyta som innehåller både exempel programmet och tal-SDK som ett beroende. Den här arbets ytan används i följande steg.

## <a name="add-the-sample-code"></a>Lägga till exempelkoden

1. Öppna arbets ytans `helloworld.xcworkspace` i Xcode.
1. Ersätt innehållet i den automatiskt genererade `AppDelegate.m`-filen med följande kod:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/from-microphone/helloworld/helloworld/AppDelegate.m#code)]
1. Ersätt strängen `YourSubscriptionKey` med din prenumerationsnyckel.
1. Ersätt strängen `YourServiceRegion` med den [region](~/articles/cognitive-services/Speech-Service/regions.md) som är associerad med din prenumeration. Använd till exempel `westus` för den kostnads fria prov prenumerationen.

## <a name="build-and-run-the-sample"></a>Skapa och köra exempelappen

1. Se till att fel söknings utdata visas genom att välja **visa** > **fel söknings områden** > **Aktivera konsol**.
1. Skapa och kör exempel koden genom att välja **produkt** > **köra** från menyn. Du kan också välja **Play**.
1. När du har valt knappen och sagt några ord bör du se texten du har talat om i den nedre delen av skärmen. När du kör appen för första gången ska du uppmanas att ge appen åtkomst till datorns mikrofon.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska Objektive-C-exempel på GitHub](https://aka.ms/csspeech/samples)
