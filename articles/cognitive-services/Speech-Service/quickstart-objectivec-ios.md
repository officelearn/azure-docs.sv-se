---
title: 'Snabbstart: Känna igen tal i Objective-C på iOS med hjälp av Cognitive Services tal SDK'
titleSuffix: Microsoft Cognitive Services
description: Lär dig att känna igen tal i Objective-C på iOS med hjälp av Cognitive Services tal SDK
services: cognitive-services
author: chlandsi
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 09/24/2018
ms.author: chlandsi
ms.openlocfilehash: e343c24a5ef223e1fd6dc618f41d4acf89fc2f5d
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47226032"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-using-the-cognitive-services-speech-sdk"></a>Snabbstart: Känna igen tal i Objective-C på iOS med hjälp av Cognitive Services tal SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

I den här artikeln lär du dig att skapa en iOS-app i Objective-C med hjälp av Cognitive Services tal SDK för att transkribera en ljudfil med inspelade tal till text.

## <a name="prerequisites"></a>Förutsättningar

* En prenumerationsnyckel för Speech-tjänsten. Se [prova speech-tjänsten utan kostnad](get-started.md).
* En Mac med Xcode 9.4.1 installeras som iOS-utvecklingsmiljö. Den här kursen riktar sig mot iOS-versioner 11.4. Om du inte har Xcode ännu, du kan installera det från den [App Store](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12).

## <a name="get-the-speech-sdk-for-ios"></a>Hämta tal SDK för iOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Den aktuella versionen av Cognitive Services tal SDK är `1.0.0`.

Cognitive Services tal SDK för Mac och iOS är för närvarande distribueras som ett Cocoa ramverk.
Den kan hämtas från https://aka.ms/csspeech/iosbinary. Ladda ned filen till arbetskatalogen.


## <a name="create-an-xcode-project"></a>Skapa en Xcode-projekt 

Starta Xcode och starta ett nytt projekt genom att klicka på **filen** > **New** > **projekt**.
I dialogrutan för val av mallen väljer du den ”iOS Single View App” mall.

I dialogrutorna som följer, gör följande val:

1. Alternativ-dialogen för projektet
    1. Ange ett namn för Snabbstart-appen, till exempel `helloworld`.
    1. Ange en lämplig organisationsnamn och organisations-ID om du redan har ett Apple developer-konto. I testsyfte kan du bara välja vilket namn som `testorg`. Om du vill registrera appen, måste du också en korrekt etableringsprofil för. Finns det [Apple-utvecklarwebbplatsen](https://developer.apple.com/) mer information.
    1. Kontrollera att Objective-C har valts som språk för projektet.
    1. Inaktivera alla kryssrutor för tester och grundläggande information.
    ![Projektinställningar](media/sdk/qs-objectivec-project-settings.png)
1. Välj projektkatalogen
    1. Välj din hemkatalog att placera projektet i. Detta skapar en `helloworld` katalogen i din arbetskatalog som innehåller alla filer för Xcode-projekt.
    1. Inaktivera skapandet av en Git-lagringsplats för det här exemplet-projektet.
    1. Justera sökvägar till SDK i den *Projektinställningar*.
        1. I den **Allmänt** fliken den **inbäddade binära** rubrik, lägga till SDK-biblioteket som ett ramverk: **Lägg till inbäddade binära** > **lägga till andra ...**  > Gå till din hemkatalog och välj filen `MicrosoftCognitiveServicesSpeech.framework`. Detta lägger automatiskt till SDK-biblioteket i rubriken **länkade ramverk och bibliotek**.
        ![Har lagts till Framework](media/sdk/qs-objectivec-framework.png)
        1. Gå till den **Versionsinställningar** fliken och aktivera **alla** inställningar.
        1. Lägg till katalogen `$(SRCROOT)/..` till den *Framework sökvägar* under den **sökvägar** rubrik.
        ![Framework-inställningen för sökvägen](media/sdk/qs-objectivec-framework-search-paths.png)


## <a name="set-up-the-ui"></a>Konfigurera Användargränssnittet

Exempelappen har ett mycket enkelt användargränssnitt: en för att starta bearbetning av filen och en textetikett att visa resultatet.
Gränssnittet har ställts in i den `Main.storyboard` ingår i projektet.
Öppna XML-visning av storyboard genom att högerklicka på den `Main.storyboard` inmatning av projektet trädet och välja **öppna som...**   >  **Källkod**.
Ersätt den automatiskt genererade XML med detta:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>Lägg till exempelkoden

1. Ladda ned den [wav exempelfilen](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav) genom att högerklicka på länken och välja **Spara mål som...** . Lägg till wav-filen i projektet som en resurs genom att dra den från ett Finder-fönster på rotnivå i projektvyn.
Klicka på **Slutför** i följande dialogruta utan att ändra inställningarna.
1. Ersätt innehållet i den automatiskt genererade `ViewController.m` genom att:

   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/ViewController.m#code)]
1. Lägg till begäran om åtkomst till mikrofonen. Högerklicka på posten info.plist i projektträdet och välj **öppna som...**   >  **Källkod**. Lägg till följande rader i den `<dict>` avsnittet och sedan spara filen.
    ```xml
    <key>NSMicrophoneUsageDescription</key>

    <string>Need microphone access for speech recognition from microphone.</string>
    ```
1. Ersätt strängen `YourSubscriptionKey` med din prenumerationsnyckel.
1. Ersätt strängen `YourServiceRegion` med den [region](regions.md) som är associerade med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).


## <a name="building-and-running-the-sample"></a>Bygga och köra exemplet

1. Se utdata för felsökning (**visa** > **felsöka området** > **aktivera konsolen**).
1. Skapa och kör exempelkoden i iOS-simulatorn genom att välja **produkten** -> **kör** från menyn eller klicka på den **spela upp** knappen. Anslut enheten till din utvecklingsdator för att köra på en iOS-enhet, och välj enheten som körs mål. Tal-SDK stöder för närvarande endast 64-bitars iOS-plattformen.
1. När du klickar på ”identifiera”! knappen i appen, bör du se innehållet i ljudet filen ”vad är vädret som”? på den nedre delen av skärmen.

 ![Simulerade iOS-App](media/sdk/qs-objectivec-simulated-app.png)

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Leta efter det här exemplet i den `quickstart/objectivec-ios` mapp.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hämta våra exempel](speech-sdk.md#get-the-samples)
