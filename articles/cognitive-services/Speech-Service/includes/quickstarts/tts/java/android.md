---
title: 'Snabbstart: Syntetisera tal, Java (Android) - Taltjänst'
titleSuffix: Azure Cognitive Services
description: Lär dig hur du syntetiserar tal i Java på Android med hjälp av Tal SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/19/2019
ms.author: yulili
ms.openlocfilehash: 230b4abb9a740e830392a92369477c7dc44f60ee
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925701"
---
## <a name="prerequisites"></a>Krav

Innan du börjar måste du:

> [!div class="checklist"]
> * [Skapa en Azure-talresurs](../../../../get-started.md)
> * [Konfigurera utvecklingsmiljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md?tabs=android)

## <a name="create-user-interface"></a>Skapa användargränssnitt

Vi skapar ett grundläggande användargränssnitt för programmet. Redigera layouten för din huvudsakliga aktivitet, `activity_main.xml`. Inledningsvis innehåller layouten en namnlist med programmets namn och en TextView med texten ”Hello World!”.

1. Klicka på elementet TextView. Ändra dess ID-attribut i det `outputMessage`övre högra hörnet till och dra det till den nedre skärmen. Ta bort texten.

1. I paletten i det övre vänstra hörnet i fönstret `activity_main.xml` drar du en knapp till det tomma utrymmet ovanför texten.

1. I knappens attribut till höger, i värdet för attributet `onClick` anger du `onSpeechButtonClicked`. Vi skriver en metod med det här namnet att hantera knapphändelsen.  Ändra dess ID-attribut i det övre högra hörnet till `button`.

1. Dra en oformaterad text till utrymmet ovanför knappen. ändra dess ID-attribut till `speakText`och `Hi there!`ändra textattributet till .

1. Använd trollstavsikonen överst i designern för att härleda layoutbegränsningar.


    ![Skärmbild trollstavsikonen](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-10-infer-layout-constraints.png)

Texten och den grafiska representationen av ditt användargränssnitt bör nu se ut så här:

![](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-11-2-tts-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/text-to-speech/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Lägg till exempelkod

1. Öppna till källfilen `MainActivity.java`. Ersätt all kod i den här filen med följande.

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/text-to-speech/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Metoden `onSpeechButtonClicked` är, som tidigare nämnts, knappklickshanteraren. En knapptryckning utlöser talsyntes.

1. Ersätt strängen `YourSubscriptionKey` i samma fil med din prenumerationsnyckel.

1. Ersätt även strängen `YourServiceRegion` med den [region](~/articles/cognitive-services/Speech-Service/regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri utvärderingsversion).

## <a name="build-and-run-the-app"></a>Skapa och kör appen

1. Anslut din Android-enhet till utvecklingsdatorn. Kontrollera att du har aktiverat [utvecklingsläge och USB-felsökning](https://developer.android.com/studio/debug/dev-options) på enheten. Alternativt kan du skapa en [Android-emulator](https://developer.android.com/studio/run/emulator).

1. Om du vill skapa programmet trycker du på Ctrl+F9 eller väljer **Skapa** > **projekt** på menyraden.

1. Om du vill starta programmet trycker du på Skift+F10 eller väljer **Kör** > **kör "app".**

1. Välj din Android-enhet eller emulator i fönstret för distributionsmål som visas.

   ![Skärmbild av fönstret för att välja distributionsmål](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-12-deploy.png)

Skriv en text och tryck på knappen i programmet för att påbörja ett avsnitt med talsyntes. Du kommer att höra det syntetiserade ljudet `speech synthesis succeeded` från standardhögtalaren och se informationen på skärmen.

![Skärmbild av Android-programmet](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-13-2-gui-on-device-tts.png)

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Se även

- [Skapa en anpassad röst](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Spela in anpassade röstexempel](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
