---
title: 'Snabbstart: Syntetisera tal, Java (Android) – tal service'
titleSuffix: Azure Cognitive Services
description: Lär dig mer om att syntetisera tal i Java på Android med hjälp av tal-SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/19/2019
ms.author: yulili
ms.openlocfilehash: 17fb0c1a4ba5495506ceb658eb61d0ac2959caf8
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71804043"
---
# <a name="quickstart-synthesize-speech-in-java-on-android-by-using-the-speech-sdk"></a>Snabbstart: Syntetisera tal i Java på Android med hjälp av tal-SDK

Snabb Starter är också tillgängliga för [tal igenkänning](quickstart-java-android.md) och [röst för första virtuella assistent](quickstart-virtual-assistant-java-android.md).

I den här artikeln får du lära dig hur du utvecklar ett Java-program för Android med hjälp av Cognitive Services Speech SDK för att syntetisera tal från text.
Programmet baseras på tal SDK maven-paketet, version 1.7.0 och Android Studio 3,3.
Speech SDK är kompatibelt med Android-enheter med 32/64-bitars ARM-processorer och Intel x86/x64-kompatibla processorer.

> [!NOTE]
> Information om Speech Devices SDK och Roobo-enheten finns i [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Förutsättningar

Du behöver en prenumerations nyckel för tal tjänster för att slutföra den här snabb starten. Du kan skaffa en utan kostnad. Mer information finns i [testa tal tjänsterna kostnads fritt](get-started.md) .

## <a name="create-and-configure-a-project"></a>Skapa och konfigurera ett projekt

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>Skapa användargränssnitt

Vi skapar ett grundläggande användargränssnitt för programmet. Redigera layouten för din huvudsakliga aktivitet, `activity_main.xml`. Inledningsvis innehåller layouten en namnlist med programmets namn och en TextView med texten ”Hello World!”.

1. Klicka på elementet TextView. Ändra dess ID-attribut i det övre högra hörnet till `outputMessage` och dra det till den nedre skärmen. Ta bort dess text.

1. I paletten i det övre vänstra hörnet i fönstret `activity_main.xml` drar du en knapp till det tomma utrymmet ovanför texten.

1. I knappens attribut till höger, i värdet för attributet `onClick` anger du `onSpeechButtonClicked`. Vi skriver en metod med det här namnet att hantera knapphändelsen.  Ändra dess ID-attribut i det övre högra hörnet till `button`.

1. Dra en oformaterad text till utrymmet ovanför knappen. ändra dess ID-attribut till `speakText` och ändra attributet text till `Hi there!`.

1. Använd trollstavsikonen överst i designern för att härleda layoutbegränsningar.


    ![Skärmbild trollstavsikonen](media/sdk/qs-java-android-10-infer-layout-constraints.png)

Texten och den grafiska representationen av ditt användargränssnitt bör nu se ut så här:

![](media/sdk/qs-java-android-11-2-tts-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/java-android/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Lägg till exempelkod

1. Öppna till källfilen `MainActivity.java`. Ersätt all kod i den här filen med följande.

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Metoden `onSpeechButtonClicked` är, som tidigare nämnts, knappklickshanteraren. En knapp tryckning utlöser tal syntes.

1. Ersätt strängen `YourSubscriptionKey` i samma fil med din prenumerationsnyckel.

1. Ersätt även strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri utvärderingsversion).

## <a name="build-and-run-the-app"></a>Skapa och kör appen

1. Anslut din Android-enhet till utvecklingsdatorn. Kontrollera att du har aktiverat [utvecklingsläge och USB-felsökning](https://developer.android.com/studio/debug/dev-options) på enheten. Du kan också skapa en [Android-emulator](https://developer.android.com/studio/run/emulator).

1. Tryck på Ctrl + F9 för att skapa programmet, eller välj **Skapa** > **Skapa projekt** på menyraden.

1. Tryck på Skift + F10 för att starta programmet, eller välj **Kör** > **Kör ”app”** .

1. I fönstret distributions mål som visas väljer du din Android-enhet eller emulator.

   ![Skärmbild av fönstret för att välja distributionsmål](media/sdk/qs-java-android-12-deploy.png)

Ange en text och tryck på knappen i programmet för att starta ett tal syntes avsnitt. Du får höra det syntetiserade ljudet från standard högtalaren och se `speech synthesis succeeded`-informationen på skärmen.

![Skärmbild av Android-programmet](media/sdk/qs-java-android-13-2-gui-on-device-tts.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska Java-exempel på GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Se också

- [Anpassa röst teckensnitt](how-to-customize-voice-font.md)
- [Spela in röst exempel](record-custom-voice-samples.md)
