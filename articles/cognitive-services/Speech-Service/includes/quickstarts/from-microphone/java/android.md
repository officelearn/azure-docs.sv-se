---
title: 'Snabbstart: Känna igen tal från en mikrofon, Java (Android) - Taltjänst'
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
ms.openlocfilehash: 2641464e52b80c59b6c6b5a123ad1605c1d41495
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81275531"
---
## <a name="prerequisites"></a>Krav

Innan du börjar:

> [!div class="checklist"]
> * [Skapa en Azure-talresurs](../../../../get-started.md)
> * [Konfigurera din utvecklingsmiljö](../../../../quickstarts/setup-platform.md?tabs=android&pivots=programming-language-java)
> * Se till att du har tillgång till en mikrofon för ljudinspelning

## <a name="create-a-user-interface"></a>Skapa ett användargränssnitt

Nu ska vi skapa ett grundläggande användargränssnitt för programmet. Redigera layouten för din huvudsakliga aktivitet, `activity_main.xml`. Inledningsvis innehåller layouten en namnlist med programmets namn och en TextView som innehåller texten "Hello World!".

* Markera Elementet TextView. Ändra dess ID-attribut i det övre högra hörnet till `hello`.

* Dra en knapp till det `activity_main.xml` tomma utrymmet ovanför texten från paletten längst upp till vänster i fönstret.

* I knappens attribut till höger, i värdet för attributet `onClick` anger du `onSpeechButtonClicked`. Vi skriver en metod med det här namnet att hantera knapphändelsen. Ändra dess ID-attribut i det övre högra hörnet till `button`.

* Använd trollstavsikonen överst i designern för att härleda layoutbegränsningar.

  ![Skärmbild trollstavsikonen](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-10-infer-layout-constraints.png)

Texten och den grafiska representationen av ditt användargränssnitt bör nu se ut så här:

![Användargränssnitt](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-11-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/from-microphone/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Lägg till exempelkod

1. Öppna till källfilen `MainActivity.java`. Ersätt all kod i den här filen med följande:

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/from-microphone/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Metoden `onCreate` inkluderar den kod som begär mikrofon- och internet-behörigheter och initierar inbyggd plattformsbindning. Du behöver bara konfigurera inbyggda plattformsbindningar en gång. Det bör göras tidigt under initieringen av programmet.

   * Metoden `onSpeechButtonClicked` är, som tidigare nämnts, knappklickshanteraren. En knapptryckning utlöser transkription mellan tal och text.

1. Ersätt strängen `YourSubscriptionKey` i samma fil med din prenumerationsnyckel.

1. Ersätt också `YourServiceRegion` strängen med **regionidentifieraren** från [den region](https://aka.ms/speech/sdkregion) som är associerad med din prenumeration. Använd till `westus` exempel för den kostnadsfria utvärderingsprenumerationen.

## <a name="build-and-run-the-app"></a>Skapa och kör appen

1. Anslut din Android-enhet till utvecklingsdatorn. Se till att du har aktiverat [utvecklingsläge och USB-felsökning](https://developer.android.com/studio/debug/dev-options) på enheten.

1. Om du vill skapa programmet väljer du Ctrl+F9 eller väljer **Skapa** > **projekt** på menyraden.

1. Om du vill starta programmet väljer du Skift+F10 eller väljer **Kör** > **kör "app".**

1. Välj din Android-enhet i fönstret för distributionsmål som visas.

   ![Skärmbild av fönstret för att välja distributionsmål](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-12-deploy.png)

Välj knappen i programmet för att starta ett taligenkänningsavsnitt. De nästa 15 sekunderna med engelskt tal skickas till Speech-tjänsten och transkriberas. Resultatet visas i Android-programmet och i fönstret logcat i Android Studio.

![Skärmbild av Android-programmet](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-13-gui-on-device.png)

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
