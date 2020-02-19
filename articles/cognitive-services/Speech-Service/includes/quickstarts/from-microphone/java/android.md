---
title: 'Snabb start: identifiera tal från en mikrofon, Java (Android)-tal-service'
titleSuffix: Azure Cognitive Services
description: Lär dig att känna igen tal i Java på Android med hjälp av Speech SDK
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/05/2019
ms.author: wolfma
ms.openlocfilehash: 6348d09351cf627624340083e2c419def38dfc01
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2020
ms.locfileid: "77446152"
---
## <a name="prerequisites"></a>Förutsättningar

Innan du börjar:

> [!div class="checklist"]
> * [Skapa en Azure tal-resurs](../../../../get-started.md)
> * [Konfigurera utvecklings miljön](../../../../quickstarts/setup-platform.md?tabs=android)
> * Kontrol lera att du har åtkomst till en mikrofon för ljud inspelning

## <a name="create-a-user-interface"></a>Skapa ett användar gränssnitt

Nu ska vi skapa ett grundläggande användar gränssnitt för programmet. Redigera layouten för din huvudsakliga aktivitet, `activity_main.xml`. Inlednings vis innehåller layouten en namn List med programmets namn och en TextView som innehåller texten "Hello World!".

* Välj TextView-elementet. Ändra dess ID-attribut i det övre högra hörnet till `hello`.

* Från paletten längst upp till vänster i `activity_main.xml` fönstret, drar du en knapp till det tomma utrymmet ovanför texten.

* I knappens attribut till höger, i värdet för attributet `onClick` anger du `onSpeechButtonClicked`. Vi skriver en metod med det här namnet att hantera knapphändelsen. Ändra dess ID-attribut i det övre högra hörnet till `button`.

* Använd trollstavsikonen överst i designern för att härleda layoutbegränsningar.

  ![Skärmbild trollstavsikonen](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-10-infer-layout-constraints.png)

Texten och den grafiska representationen av ditt användargränssnitt bör nu se ut så här:

![Användargränssnitt](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-11-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/from-microphone/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Lägga till exempelkod

1. Öppna till källfilen `MainActivity.java`. Ersätt all kod i den här filen med följande:

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/from-microphone/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Metoden `onCreate` inkluderar den kod som begär mikrofon- och internet-behörigheter och initierar inbyggd plattformsbindning. Du behöver bara konfigurera inbyggda plattformsbindningar en gång. Det bör göras tidigt under initieringen av programmet.

   * Metoden `onSpeechButtonClicked` är, som tidigare nämnts, knappklickshanteraren. En knapp tryckning utlöser tal-till-text-avskrift.

1. Ersätt strängen `YourSubscriptionKey` i samma fil med din prenumerationsnyckel.

1. Ersätt också strängen `YourServiceRegion` med **regions-ID: n** från den [region](https://aka.ms/speech/sdkregion) som är associerad med din prenumeration. Använd till exempel `westus` för den kostnads fria prov prenumerationen.

## <a name="build-and-run-the-app"></a>Skapa och kör appen

1. Anslut din Android-enhet till utvecklingsdatorn. Se till att du har aktiverat [utvecklings läge och USB-felsökning](https://developer.android.com/studio/debug/dev-options) på enheten.

1. Om du vill bygga programmet väljer du Ctrl + F9 eller väljer **build** > **skapa projekt** från meny raden.

1. För att starta programmet väljer du Shift + F10 eller väljer **kör** > **Kör "app"** .

1. I fönstret distributions mål som visas väljer du din Android-enhet.

   ![Skärmbild av fönstret för att välja distributionsmål](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-12-deploy.png)

Välj knappen i programmet för att starta ett tal igenkännings avsnitt. De nästa 15 sekunderna med engelskt tal skickas till Speech-tjänsten och transkriberas. Resultatet visas i Android-programmet och i fönstret logcat i Android Studio.

![Skärmbild av Android-programmet](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-13-gui-on-device.png)

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]

