---
title: 'Snabb start: syntetisera tal, Java (Android) – tal service'
titleSuffix: Azure Cognitive Services
description: Lär dig mer om att syntetisera tal i Java på Android med hjälp av tal-SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/19/2019
ms.author: yulili
ms.openlocfilehash: 230b4abb9a740e830392a92369477c7dc44f60ee
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925701"
---
## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du se till att:

> [!div class="checklist"]
> * [Skapa en Azure tal-resurs](../../../../get-started.md)
> * [Konfigurera utvecklings miljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md?tabs=android)

## <a name="create-user-interface"></a>Skapa användargränssnitt

Vi skapar ett grundläggande användargränssnitt för programmet. Redigera layouten för din huvudsakliga aktivitet, `activity_main.xml`. Inledningsvis innehåller layouten en namnlist med programmets namn och en TextView med texten ”Hello World!”.

1. Klicka på elementet TextView. Ändra dess ID-attribut i det övre högra hörnet till `outputMessage`och dra det till den nedre skärmen. Ta bort dess text.

1. I paletten i det övre vänstra hörnet i fönstret `activity_main.xml` drar du en knapp till det tomma utrymmet ovanför texten.

1. I knappens attribut till höger, i värdet för attributet `onClick` anger du `onSpeechButtonClicked`. Vi skriver en metod med det här namnet att hantera knapphändelsen.  Ändra dess ID-attribut i det övre högra hörnet till `button`.

1. Dra en oformaterad text till utrymmet ovanför knappen. ändra dess ID-attribut till `speakText`och ändra attributet text till `Hi there!`.

1. Använd trollstavsikonen överst i designern för att härleda layoutbegränsningar.


    ![Skärmbild trollstavsikonen](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-10-infer-layout-constraints.png)

Texten och den grafiska representationen av ditt användargränssnitt bör nu se ut så här:

![](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-11-2-tts-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/text-to-speech/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Lägga till exempelkod

1. Öppna till källfilen `MainActivity.java`. Ersätt all kod i den här filen med följande.

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/text-to-speech/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Metoden `onSpeechButtonClicked` är, som tidigare nämnts, knappklickshanteraren. En knapp tryckning utlöser tal syntes.

1. Ersätt strängen `YourSubscriptionKey` i samma fil med din prenumerationsnyckel.

1. Ersätt även strängen `YourServiceRegion` med den [region](~/articles/cognitive-services/Speech-Service/regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

## <a name="build-and-run-the-app"></a>Skapa och kör appen

1. Anslut din Android-enhet till utvecklingsdatorn. Kontrollera att du har aktiverat [utvecklingsläge och USB-felsökning](https://developer.android.com/studio/debug/dev-options) på enheten. Du kan också skapa en [Android-emulator](https://developer.android.com/studio/run/emulator).

1. Tryck på Ctrl + F9 för att skapa programmet, eller välj **Skapa** > **Skapa projekt** på menyraden.

1. Tryck på Skift + F10 för att starta programmet, eller välj **Kör** > **Kör ”app”** .

1. I fönstret distributions mål som visas väljer du din Android-enhet eller emulator.

   ![Skärmbild av fönstret för att välja distributionsmål](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-12-deploy.png)

Ange en text och tryck på knappen i programmet för att starta ett tal syntes avsnitt. Du får höra det syntetiserade ljudet från standard högtalaren och se `speech synthesis succeeded` information på skärmen.

![Skärmbild av Android-programmet](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-13-2-gui-on-device-tts.png)

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Se även

- [Skapa en anpassad röst](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Spela in anpassade röst exempel](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
