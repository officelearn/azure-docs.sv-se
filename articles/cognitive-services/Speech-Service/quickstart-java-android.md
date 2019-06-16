---
title: 'Snabbstart: Identifiera tal, Java (Android) – Speech Services'
titleSuffix: Azure Cognitive Services
description: Lär dig att känna igen tal i Java på Android med hjälp av Speech SDK
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 2/20/2019
ms.author: wolfma
ms.openlocfilehash: 369373d19945ea5226e4b9d005cffd3b35064be9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067641"
---
# <a name="quickstart-recognize-speech-in-java-on-android-by-using-the-speech-sdk"></a>Snabbstart: Känna igen tal i Java på Android med hjälp av Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

I den här artikeln lär du dig att utveckla ett Java-program för Android med hjälp av Speech SDK för Cognitive Services för att transkribera tal till text.
Programmet baseras på tal SDK Maven-paketet, version 1.5.1 och Android Studio 3.3.
Speech SDK är kompatibelt med Android-enheter med 32/64-bitars ARM-processorer och Intel x86/x64-kompatibla processorer.

> [!NOTE]
> Information om Speech Devices SDK och Roobo-enheten finns i [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver en Speech Services-prenumeration för att slutföra den här snabbstarten. Du kan skaffa en utan kostnad. Se [testa Speech Services kostnadsfritt](get-started.md) mer information.

## <a name="create-and-configure-a-project"></a>Skapa och konfigurera ett projekt

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>Skapa användargränssnitt

Vi skapar ett grundläggande användargränssnitt för programmet. Redigera layouten för din huvudsakliga aktivitet, `activity_main.xml`. Inledningsvis innehåller layouten en namnlist med programmets namn och en TextView med texten ”Hello World!”.

* Klicka på elementet TextView. Ändra dess ID-attribut i det övre högra hörnet till `hello`.

* I paletten i det övre vänstra hörnet i fönstret `activity_main.xml` drar du en knapp till det tomma utrymmet ovanför texten.

* I knappens attribut till höger, i värdet för attributet `onClick` anger du `onSpeechButtonClicked`. Vi skriver en metod med det här namnet att hantera knapphändelsen.  Ändra dess ID-attribut i det övre högra hörnet till `button`.

* Använd trollstavsikonen överst i designern för att härleda layoutbegränsningar.

  ![Skärmbild trollstavsikonen](media/sdk/qs-java-android-10-infer-layout-constraints.png)

Texten och den grafiska representationen av ditt användargränssnitt bör nu se ut så här:

![](media/sdk/qs-java-android-11-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Lägga till exempelkod

1. Öppna till källfilen `MainActivity.java`. Ersätt all kod i den här filen med följande.

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Metoden `onCreate` inkluderar den kod som begär mikrofon- och internet-behörigheter och initierar inbyggd plattformsbindning. Du behöver bara konfigurera inbyggda plattformsbindningar en gång. Det bör göras tidigt under initieringen av programmet.

   * Metoden `onSpeechButtonClicked` är, som tidigare nämnts, knappklickshanteraren. En knapptryckning utlöser transkription från tal till text.

1. Ersätt strängen `YourSubscriptionKey` i samma fil med din prenumerationsnyckel.

1. Ersätt även strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri utvärderingsversion).

## <a name="build-and-run-the-app"></a>Skapa och kör appen

1. Anslut din Android-enhet till utvecklingsdatorn. Kontrollera att du har aktiverat [utvecklingsläge och USB-felsökning](https://developer.android.com/studio/debug/dev-options) på enheten.

1. Tryck på Ctrl + F9 för att skapa programmet, eller välj **Skapa** > **Skapa projekt** på menyraden.

1. Tryck på Skift + F10 för att starta programmet, eller välj **Kör** > **Kör ”app”** .

1. Välj din Android-enhet i målfönstret för distribution som visas.

   ![Skärmbild av fönstret för att välja distributionsmål](media/sdk/qs-java-android-12-deploy.png)

Tryck på knappen i programmet för att starta en taligenkänningssession. Nästa 15 sekunder engelska tal ska skickas till tal-tjänsterna och transkriberas. Resultatet visas i Android-programmet och i fönstret logcat i Android Studio.

![Skärmbild av Android-programmet](media/sdk/qs-java-android-13-gui-on-device.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska Java-exempel på GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Se också

- [Anpassa akustiska modeller](how-to-customize-acoustic-models.md)
- [Anpassa språkmodeller](how-to-customize-language-model.md)
