---
title: 'Snabbstart: Känna igen tal från en mikrofon, Objective-C - Taltjänst'
titleSuffix: Azure Cognitive Services
description: Lär dig hur du känner igen tal i Objective-C på iOS med hjälp av Tal-SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/23/2019
ms.author: chlandsi
ms.openlocfilehash: c1246b19670a18f8dadc0c5e1c64dd5af4c1b210
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75380787"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-by-using-the-speech-sdk"></a>Snabbstart: Känna igen tal i Objective-C på iOS med hjälp av Tal-SDK

Snabbstarter finns också tillgängliga för [talsyntes](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/objectivec-ios.md).

I den här artikeln får du lära dig hur du skapar en iOS-app i Objective-C med hjälp av Azure Cognitive Services Speech SDK för att transkribera tal till text från en mikrofon eller från en fil med inspelat ljud.

## <a name="prerequisites"></a>Krav

Innan du börjar behöver du:

* En [prenumerationsnyckel](~/articles/cognitive-services/Speech-Service/get-started.md) för taltjänsten.
* En macOS-maskin med [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) eller senare.
* Målet är inställt på iOS version 9.3 eller senare.

## <a name="get-the-speech-sdk-for-ios"></a>Skaffa Speech SDK för iOS

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Cognitive Services Speech SDK för iOS distribueras för närvarande som ett kakaoramverk.
Det kan laddas ner från [denna webbplats](https://aka.ms/csspeech/iosbinary). Ladda ned filen till arbetskatalogen.

## <a name="create-an-xcode-project"></a>Skapa ett Xcode-projekt

Starta Xcode och starta ett nytt projekt genom att välja **Arkiv** > **nytt** > **projekt**.
Välj **iOS-appmallen i** mallval.

Gör följande val i dialogrutorna som följer.

1. I dialogrutan **Projektalternativ:**
    1. Ange ett namn för snabbstartsappen, till exempel *helloworld*.
    1. Ange ett lämpligt organisationsnamn och organisationsidentifierare om du redan har ett Apple-utvecklarkonto. Använd ett namn som *testorg*för testning. För att signera appen behöver du en korrekt etableringsprofil. Mer information finns på [Apples utvecklarwebbplats](https://developer.apple.com/).
    1. Kontrollera att **Objective-C** är valt som språk för projektet.
    1. Avmarkera alla kryssrutor för tester och kärndata.

    ![Projektinställningar](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-project-settings.png)

1. Välj en projektkatalog:
   1. Välj arbetskatalogen och spara projektet där. Det här steget skapar en helloworld-katalog i din hemkatalog som innehåller alla filer för Xcode-projektet.
   1. Inaktivera skapandet av en Git-lagringsplats för det här exempelprojektet.
   1. Justera banorna till SDK på skärmen för projektinställningar.
      1. Lägg till SDK-biblioteket som ett ramverk på fliken **Allmänt** **Add embedded binaries** > **Add other**under rubriken **Inbäddade binärfiler.** Gå till din hemkatalog `MicrosoftCognitiveServicesSpeech.framework`och välj filen . Den här åtgärden lägger till SDK-biblioteket i rubriken **Länkad ramverk och bibliotek** automatiskt.
         ![Tillagd ram](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-framework.png)
      1. Gå till fliken **Bygginställningar** och välj inställningen **Alla.**
      1. Lägg till katalogen $(SRCROOT)/.. **till Ramsökvägar** under rubriken **Sökvägar.**

      ![Inställning av sökvägar för ramverk](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-framework-search-paths.png)

## <a name="set-up-the-ui"></a>Konfigurera användargränssnittet

Exempelappen har ett mycket enkelt användargränssnitt. Den har två knappar för att starta taligenkänning antingen från fil eller från mikrofoninmatning och en textetikett för att visa resultatet. Användargränssnittet konfigureras i `Main.storyboard`-delen av projektet. Öppna XML-vyn för storyboarden genom `Main.storyboard` att högerklicka på posten i projektträdet och välja **Öppen källkod** > **.**

Ersätt den automatiskt genererade XML:en med följande kod:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/from-microphone/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>Lägga till exempelkoden

1. Hämta [exempelvågsfilen](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav) genom att högerklicka på länken och välja **Spara mål som**.
   Lägg till wav-filen i projektet som en resurs genom att dra det från ett Finder-fönster till projektvyns rotnivå.
   Välj **Slutför** i följande dialogruta utan att ändra inställningarna.
1. Ersätt innehållet i den autogenererade `ViewController.m` filen med följande kod:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/from-microphone/helloworld/helloworld/ViewController.m#code)]
1. Ersätt strängen `YourSubscriptionKey` med din prenumerationsnyckel.
1. Ersätt strängen `YourServiceRegion` med den [region som](~/articles/cognitive-services/Speech-Service/regions.md) är associerad med din prenumeration. Använd till `westus` exempel för den kostnadsfria utvärderingsprenumerationen.
1. Lägg till begäran om åtkomst till mikrofonen. Högerklicka på `Info.plist` posten för projektträdet och välj **Öppna som** > **källkod**. Lägg till följande `<dict>` rader i avsnittet och spara sedan filen.

    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="build-and-run-the-sample"></a>Skapa och köra exempelappen

1. Gör felsökningsutdata synliga genom att välja **Visa** > **felsökningsområde** > **Aktivera konsolen**.
1. Välj antingen iOS-simulatorn eller en iOS-enhet som är ansluten till utvecklingsdatorn som mål för appen i listan på menyn > **Produktmål.** **Product**
1. Skapa och kör exempelkoden i iOS-simulatorn genom att välja > **Produktkörning** på menyn. **Product** Du kan också välja knappen **Spela upp.**
1. När du har valt knappen **Identifiera (arkiv)** i appen bör du se innehållet i ljudfilen "Hur är vädret?" på den nedre delen av skärmen.

   ![Simulerad iOS-app](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-simulated-app.png)

1. När du har valt knappen **Identifiera (mikrofon)** i appen och säga några ord bör du se texten du har talat upp på den nedre delen av skärmen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska Objektive-C-exempel på GitHub](https://aka.ms/csspeech/samples)
