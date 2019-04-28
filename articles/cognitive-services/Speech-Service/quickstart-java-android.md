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
ms.openlocfilehash: 690656449fdb86c200a8978f0e17db562e4abbca
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62118228"
---
# <a name="quickstart-recognize-speech-in-java-on-android-by-using-the-speech-sdk"></a>Snabbstart: Känna igen tal i Java på Android med hjälp av Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

I den här artikeln lär du dig att utveckla ett Java-program för Android med hjälp av Speech SDK för Cognitive Services för att transkribera tal till text.
Programmet baseras på tal SDK Maven-paketet, version 1.4.0 och Android Studio 3.3.
Speech SDK är kompatibelt med Android-enheter med 32/64-bitars ARM-processorer och Intel x86/x64-kompatibla processorer.

> [!NOTE]
> Information om Speech Devices SDK och Roobo-enheten finns i [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver en Speech Services-prenumeration för att slutföra den här snabbstarten. Du kan skaffa en utan kostnad. Se [testa Speech Services kostnadsfritt](get-started.md) mer information.

## <a name="create-and-configure-a-project"></a>Skapa och konfigurera ett projekt

1. Starta Android Studio och välj alternativet för att **starta ett nytt Android Studio-projekt** i välkomstfönstret.

    ![Skärmbild av Android Studio-välkomstfönstret](media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. Den **väljer du projektet** guiden visas, väljer **Telefoner och surfplattor** och **tom aktivitet** i aktivitetsrutan. Välj **Nästa**.

   ![Skärmbild av Välj ditt projekt](media/sdk/qs-java-android-02-target-android-devices.png)

1. I den **konfigurera ditt projekt** anger **snabbstarten** som **namn**, **samples.speech.cognitiveservices.microsoft.com** som **Paketnamn**, och välj en projektkatalogen. För **minsta API-nivå** Välj **API-23: Android 6.0 (Marshmallow)**, lämnar du alla andra kryssrutorna avmarkerad och välj **Slutför**.

   ![Skärmbild av konfigurera ditt projekt](media/sdk/qs-java-android-03-create-android-project.png)

Det tar en stund för Android Studio att förbereda det nya projektet för Android. Konfigurera projektet att veta om Speech SDK och använda Java 8.

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Den aktuella versionen av Cognitive Services Speech SDK är `1.4.0`.

Speech SDK för Android kommer som ett [AAR (Android-biblioteket)](https://developer.android.com/studio/projects/android-library), som innehåller nödvändiga bibliotek samt Android behörigheter som krävs.
Den finns i ett Maven-centrallager på https:\//csspeechstorage.blob.core.windows.net/maven/.

Konfigurera ditt projekt för att använda Speech SDK. Öppna projektstrukturfönstret genom att välja **File** (Fil)  > **Project Structure** (Projektstruktur) på menyraden i Android Studio. I projektstrukturfönstret gör du följande ändringar:

1. I listan till vänster i fönstret väljer du **Projekt**. Redigera inställningarna för **standardbiblioteksdatabasen** genom att lägga till ett kommatecken och vår URL till Maven-lagringsplatsen inom enkla citattecken. ”https:\//csspeechstorage.blob.core.windows.net/maven/”

   ![Skärmbild av projektstrukturfönstret](media/sdk/qs-java-android-06-add-maven-repository.png)

1. På samma skärm till vänster väljer du **app**. Välj sedan fliken **Beroenden** högst upp i fönstret. Markera det gröna plustecknet (+) och välj **Library dependency** (Biblioteksberoenden) på den nedrullningsbara menyn.

   ![Skärmbild av projektstrukturfönstret](media/sdk/qs-java-android-07-add-module-dependency.png)

1. I fönstret som visas anger du namnet och versionen av vårt Speech SDK för Android, `com.microsoft.cognitiveservices.speech:client-sdk:1.4.0`. Välj sedan **OK**.
   Speech SDK:n ska läggas till i listan över beroenden nu enligt nedan:

   ![Skärmbild av projektstrukturfönstret](media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Välj fliken **Egenskaper**. För både **Källkompatibilitet** och **Målkompatibilitet** väljer du **1.8**.

   ![](media/sdk/qs-java-android-09-dependency-added.png)

1. Välj **OK** för att stänga fönstret Project Structure (Projektstruktur) och tillämpa ändringarna i projektet.

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

1. Tryck på Skift + F10 för att starta programmet, eller välj **Kör** > **Kör ”app”**.

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
