---
title: 'Snabb start: identifiera tal-, Java-(Android) – tal-service'
titleSuffix: Azure Cognitive Services
description: Lär dig att känna igen tal i Java på Android med hjälp av Speech SDK
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 2fb7bc0851868f6551ed57d742c4005550caec99
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675582"
---
# <a name="quickstart-recognize-speech-in-java-on-android-by-using-the-speech-sdk"></a>Snabbstart: Känna igen tal i Java på Android med hjälp av Speech SDK

Snabb Starter är också tillgängliga för [tal syntes](quickstart-text-to-speech-java-android.md) och [röst för första virtuella assistent](quickstart-virtual-assistant-java-android.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

I den här artikeln får du lära dig hur du utvecklar ett Java-program för Android med hjälp av Azure Cognitive Services Speech SDK för att skriva tal till text.

Programmet baseras på tal SDK maven-paketet och Android Studio 3,3. Talet SDK är för närvarande kompatibelt med Android-enheter som har 32-bitars-eller 64-bitars ARM-och Intel x86/x64-kompatibla processorer.

> [!NOTE]
> Information om Speech Devices SDK och Roobo-enheten finns i [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Krav

Du behöver en prenumerations nyckel för tal tjänster för att slutföra den här snabb starten. Du kan skaffa en utan kostnad. Mer information finns i [testa tal tjänster kostnads fritt](get-started.md).

## <a name="create-and-configure-a-project"></a>Skapa och konfigurera ett projekt

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-a-user-interface"></a>Skapa ett användar gränssnitt

Nu ska vi skapa ett grundläggande användar gränssnitt för programmet. Redigera layouten för din huvudsakliga aktivitet, `activity_main.xml`. Inlednings vis innehåller layouten en namn List med programmets namn och en TextView som innehåller texten "Hello World!".

* Välj TextView-elementet. Ändra dess ID-attribut i det övre högra hörnet till `hello`.

* Från paletten längst upp till vänster i `activity_main.xml` fönstret, drar du en knapp till det tomma utrymmet ovanför texten.

* I knappens attribut till höger, i värdet för attributet `onClick` anger du `onSpeechButtonClicked`. Vi skriver en metod med det här namnet att hantera knapphändelsen. Ändra dess ID-attribut i det övre högra hörnet till `button`.

* Använd trollstavsikonen överst i designern för att härleda layoutbegränsningar.

  ![Skärmbild trollstavsikonen](media/sdk/qs-java-android-10-infer-layout-constraints.png)

Texten och den grafiska representationen av ditt användargränssnitt bör nu se ut så här:

![Användargränssnitt](media/sdk/qs-java-android-11-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Lägg till exempelkod

1. Öppna till källfilen `MainActivity.java`. Ersätt all kod i den här filen med följande:

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Metoden `onCreate` inkluderar den kod som begär mikrofon- och internet-behörigheter och initierar inbyggd plattformsbindning. Du behöver bara konfigurera inbyggda plattformsbindningar en gång. Det bör göras tidigt under initieringen av programmet.

   * Metoden `onSpeechButtonClicked` är, som tidigare nämnts, knappklickshanteraren. En knapp tryckning utlöser tal-till-text-avskrift.

1. Ersätt strängen `YourSubscriptionKey` i samma fil med din prenumerationsnyckel.

1. Ersätt också strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration. Använd till exempel `westus` för den kostnads fria prov prenumerationen.

## <a name="build-and-run-the-app"></a>Skapa och kör appen

1. Anslut din Android-enhet till utvecklingsdatorn. Se till att du har aktiverat [utvecklings läge och USB-felsökning](https://developer.android.com/studio/debug/dev-options) på enheten.

1. Om du vill bygga programmet väljer du Ctrl + F9 eller väljer **build**  > **skapa projekt** från meny raden.

1. För att starta programmet väljer du Shift + F10 eller väljer **kör**  > **Kör "app"** .

1. I fönstret distributions mål som visas väljer du din Android-enhet.

   ![Skärmbild av fönstret för att välja distributionsmål](media/sdk/qs-java-android-12-deploy.png)

Välj knappen i programmet för att starta ett tal igenkännings avsnitt. De närmaste 15 sekunderna engelska talet kommer att skickas till tal tjänster och har tilldelats. Resultatet visas i Android-programmet och i fönstret logcat i Android Studio.

![Skärmbild av Android-programmet](media/sdk/qs-java-android-13-gui-on-device.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska Java-exempel på GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Se också

- [Anpassa akustiska modeller](how-to-customize-acoustic-models.md)
- [Anpassa språkmodeller](how-to-customize-language-model.md)
