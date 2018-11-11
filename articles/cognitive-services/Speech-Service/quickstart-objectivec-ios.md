---
title: 'Snabbstart: Identifiera tal i Objective-C på iOS med Speech Service-SDK'
titleSuffix: Azure Cognitive Services
description: Lär dig hur du identifierar tal i Objective-C på iOS med Speech Service-SDK
services: cognitive-services
author: chlandsi
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: chlandsi
ms.openlocfilehash: 7d1e05e13e55b8b7bc07eda71d63f96f12c81ff9
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219196"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-using-the-speech-service-sdk"></a>Snabbstart: Identifiera tal i Objective-C på iOS med Speech Service-SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

I den här artikeln lär du dig hur du skapar en iOS-app i Objective-C med hjälp av Cognitive Services Speech-SDK för att transkribera en ljudfil med inspelat tal till text.

## <a name="prerequisites"></a>Nödvändiga komponenter

* En prenumerationsnyckel för taltjänsten. Se [Prova Speech Service kostnadsfritt](get-started.md).
* En Mac med Xcode 9.4.1 installerad som iOS-utvecklingsmiljö. I den här självstudiekursen används iOS 11.4-versioner. Om du inte har Xcode än kan du installera det från [App Store](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12).

## <a name="get-the-speech-sdk-for-ios"></a>Skaffa Speech SDK för iOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Den aktuella versionen av Cognitive Services Speech SDK är `1.1.0`.

Cognitive Services Speech SDK för Mac och iOS distribueras för närvarande som ett Cocoa-ramverk.
Du kan ladda ned det på https://aka.ms/csspeech/iosbinary. Ladda ned filen till arbetskatalogen.

## <a name="create-an-xcode-project"></a>Skapa ett Xcode-projekt 

Starta Xcode och starta ett nytt projekt genom att klicka på **File** > **New** > **Project** (Arkiv > Nytt > Projekt).
I dialogrutan för mallval väljer du mallen ”iOS Single View App”.

I dialogrutorna som följer gör du följande val:

1. Dialogrutan Project Options (Projektalternativ)
    1. Ange namnet på snabbstartsappen, till exempel `helloworld`.
    1. Ange ett lämpligt organisationsnamn och organisations-ID, om du redan har ett Apple Developer-konto. I testsyfte kan du välja vilket namn som helst, till exempel `testorg`. För att kunna signera appen behöver du även en korrekt etableringsprofil. Information finns på [Apple Developer-webbplatsen](https://developer.apple.com/).
    1. Kontrollera att Objective-C har valts som språk för projektet.
    1. Inaktivera alla kryssrutor för test och kärndata.
    ![Projektinställningar](media/sdk/qs-objectivec-project-settings.png)
1. Välja projektkatalog
    1. Välj arbetskatalogen och spara projektet där. Då skapas en `helloworld`-katalog i arbetskatalogen som innehåller alla filer för Xcode-projektet.
    1. Inaktivera skapandet av en Git-lagringsplats för det här exempelprojektet.
    1. Justera sökvägarna till SDK:et i *Project Settings* (Projektinställningar).
        1. På fliken **General** (Allmänt) under rubriken **Embedded Binaries** (Inbäddade binära), lägger du till SDK-biblioteket som ramverk: **Add embedded binaries** > **Add other...** (Lägg till inbäddade binära > Lägg till annan) > navigera till arbetskatalogen och välj filen `MicrosoftCognitiveServicesSpeech.framework`. Det här lägger även automatiskt till SDK-biblioteket i rubriken **Linked Framework and Libraries** (Länkat ramverk och bibliotek).
        ![Tillagt ramverk](media/sdk/qs-objectivec-framework.png)
        1. Gå till fliken **Build Settings** (Versionsinställningar) och aktivera **All** (Alla).
        1. Lägg till katalogen `$(SRCROOT)/..` i *Framework Search Paths* (Sökvägar för ramverket) under rubriken **Search Paths** (Sökvägar).
        Inställning för ![Framework Search Path](media/sdk/qs-objectivec-framework-search-paths.png) (Sökväg för ramverket)

## <a name="set-up-the-ui"></a>Konfigurera användargränssnittet

Exempelappen har ett mycket enkelt användargränssnitt: Två knappar för att starta taligenkänningen från fil eller från mikrofonen, och en textetikett som visar resultatet.
Användargränssnittet konfigureras i `Main.storyboard`-delen av projektet.
Öppna XML-vyn av storyboarden genom att klicka på posten `Main.storyboard` i projektträdet och välja **Open As...** > **Source Code** (Öppna som... > Källkod).
Ersätt den automatiskt genererade XML:en med följande:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>Lägga till exempelkoden

1. Ladda ned [wav-exempelfilen](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav) genom att högerklicka på länken och välja **Save target as...** (Spara mål som...). Lägg till wav-filen i projektet som en resurs genom att dra det från ett Finder-fönster till projektvyns rotnivå.
Klicka på **Finish** (Slutför) i följande dialogruta utan att ändra inställningarna.
1. Ersätt innehållet i den automatiskt genererade `ViewController.m`-filen genom att göra följande:

   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/ViewController.m#code)]
1. Ersätt strängen `YourSubscriptionKey` med din prenumerationsnyckel.
1. Ersätt strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).
1. Lägg till begäran om åtkomst till mikrofonen. Högerklicka på posten `Info.plist` i projektträdet och välj **Open As...** > **Source Code** (Öppna som... > Källkod). Lägg till följande rader i avsnittet `<dict>` och spara sedan filen.
    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="building-and-running-the-sample"></a>Skapa och köra exemplet

1. Gör felsökningsresultatet synligt (**View** > **Debug Area** > **Activate Console**) (Visa > Felsökningsområde > Aktivera konsol).
1. Välj iOS-simulatorn eller en iOS-enhet som är ansluten till utvecklingsdatorn som mål för appen från listan på menyn **Product** -> **Destination** (Produkt > Mål).
1. Skapa och kör exempelkoden i iOS-simulatorn genom att välja **Product** -> **Run** (Produkt > Kör) på menyn eller genom att klicka på **uppspelningsknappen**.
För närvarande stöder Speech SDK bara 64-bitars iOS-plattformar.
1. När du har klickat på knappen ”Recognize (File)” (Identifiera (fil)) i appen bör du se innehållet i ljudfilen ”What's the weather like?” (Vad är det för väder?) på den nedre delen av skärmen.

 ![Simulerad iOS-app](media/sdk/qs-objectivec-simulated-app.png)

1. När du har klickat på knappen ”Recognize (Microphone)” (Identifiera (mikrofon)) i appen och säger några ord bör du se texten för det du har sagt på den nedre delen av skärmen.

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Leta efter det här exemplet i mappen `quickstart/objectivec-ios`.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hämta våra exempel](speech-sdk.md#get-the-samples)

