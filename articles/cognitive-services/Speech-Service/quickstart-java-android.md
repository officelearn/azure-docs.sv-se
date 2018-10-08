---
title: 'Snabbstart: Känna igen tal i Java på Android med hjälp av Speech SDK för Cognitive Services'
titleSuffix: Microsoft Cognitive Services
description: Lär dig att känna igen tal i Java på Android med hjälp av Speech SDK för Cognitive Services
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.technology: Speech
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: fmegen
ms.openlocfilehash: 0a52889ef879aeb8a5a1ed59b74619dc3337e1e9
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47432816"
---
# <a name="quickstart-recognize-speech-in-java-on-android-by-using-the-speech-sdk"></a>Snabbstart: Känna igen tal i Java på Android med hjälp av Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

I den här artikeln lär du dig att skapa ett Java-program för Android med hjälp av Speech SDK för Cognitive Services för att transkribera tal till text.
Programmet är baserat på Microsoft Cognitive Services Speech SDK Maven-paketet, version 1.0.0 och Android Studio 3.1.
Speech SDK är kompatibelt med Android-enheter med 32-bitars eller 64-bitars ARM-processorer.

> [!NOTE]
> Information om Speech Devices SDK och Roobo-enheten finns i [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver en Speech-tjänstprenumerationsnyckel för att slutföra den här snabbstarten. Du kan skaffa en utan kostnad. Mer information finns i [Prova Speech Service kostnadsfritt](get-started.md).

## <a name="create-and-configure-a-project"></a>Skapa och konfigurera ett projekt

1. Starta Android Studio och välj alternativet för att **starta ett nytt Android Studio-projekt** i välkomstfönstret.

    ![Skärmbild av Android Studio-välkomstfönstret](media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. Guiden **Skapa ett nytt projekt** visas. På skärmen för att **skapa Android-projekt** anger du **snabbstart** som **programnamn**, **samples.speech.cognitiveservices.microsoft.com** som **företagsdomän** och väljer en projektkatalog. Lämna kryssrutorna C++ och Kotlin avmarkerade och välj **Nästa**.

   ![Skärmdump som visar guiden Skapa ett nytt projekt](media/sdk/qs-java-android-02-create-android-project.png)

1. På skärmen **Target Android Devices** (Android-målenheter) väljer du endast **Phone and Tablet** (Telefon och surfplatta). I listrutan under väljer du **API 23: Android 6.0 (Marshmallow)** och väljer **Nästa**.

   ![Skärmdump som visar guiden Skapa ett nytt projekt](media/sdk/qs-java-android-03-target-android-devices.png)

1. På skärmen för att **lägga till en aktivitet till en mobil** väljer du **Tom aktivitet** och klickar på **Nästa**.

   ![Skärmdump som visar guiden Skapa ett nytt projekt](media/sdk/qs-java-android-04-add-an-activity-to-mobile.png)

1. På skärmen för att **konfigurera aktivitet** använder du **MainActivity** som aktivitetsnamn och **activity\_main** som layoutnamn. Markera båda kryssrutorna och välj **Slutför**.

   ![Skärmdump som visar guiden Skapa ett nytt projekt](media/sdk/qs-java-android-05-configure-activity.png)

Det tar en stund för Android Studio att förbereda det nya projektet för Android. Konfigurera projektet att veta om Speech SDK och använda Java 8.

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Den aktuella versionen av Cognitive Services Speech SDK är `1.0.0`.

Speech SDK för Android kommer som ett [AAR (Android-biblioteket)](https://developer.android.com/studio/projects/android-library), som innehåller nödvändiga bibliotek samt Android behörigheter som krävs för att använda den.
Den finns i ett Maven-centrallager på https://csspeechstorage.blob.core.windows.net/maven/.

Konfigurera ditt projekt för att använda Speech SDK. Öppna projektstrukturfönstret genom att välja **File** (Fil)  > **Project Structure** (Projektstruktur) på menyraden i Android Studio. I projektstrukturfönstret gör du följande ändringar: 

1. I listan till vänster i fönstret väljer du **Projekt**. Redigera inställningarna för **standardbiblioteksdatabasen** genom att lägga till ett kommatecken och vår URL till Maven-lagringsplatsen inom enkla citattecken. 'https://csspeechstorage.blob.core.windows.net/maven/'

   ![Skärmbild av projektstrukturfönstret](media/sdk/qs-java-android-06-add-maven-repository.png)

1. På samma skärm till vänster väljer du **app**. Välj sedan fliken **Beroenden** högst upp i fönstret. Markera det gröna plustecknet (+) och välj **Library dependency** (Biblioteksberoenden) på den nedrullningsbara menyn.

   ![Skärmbild av projektstrukturfönstret](media/sdk/qs-java-android-07-add-module-dependency.png)

1. I fönstret som visas anger du namnet och versionen av vårt Speech SDK för Android, `com.microsoft.cognitiveservices.speech:client-sdk:1.0.0`. Välj sedan **OK**.
   Speech SDK:n ska läggas till i listan över beroenden nu enligt nedan:

   ![Skärmbild av projektstrukturfönstret](media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Välj fliken **Egenskaper**. För både **Källkompatibilitet** och **Målkompatibilitet** väljer du **1.8**.

   ![](media/sdk/qs-java-android-09-dependency-added.png)

1. Välj **OK** för att stänga fönstret Project Structure (Projektstruktur) och tillämpa ändringarna i projektet.

## <a name="create-user-interface"></a>Skapa användargränssnitt

Vi skapar ett grundläggande användargränssnitt för programmet. Redigera layouten för din huvudsakliga aktivitet, `activity_main.xml`. Inledningsvis innehåller layouten en namnlist med programmets namn och en TextView med texten ”Hello World”!

* Klicka på elementet TextView. Ändra dess ID-attribut i det övre högra hörnet till `hello`.

* I paletten i det övre vänstra hörnet i fönstret `activity_main.xml` drar du en knapp till det tomma utrymmet ovanför texten.

* I knappens attribut till höger, i värdet för attributet `onClick` anger du `onSpeechButtonClicked`. Vi skriver en metod med det här namnet att hantera knapphändelsen.  Ändra dess ID-attribut i det övre högra hörnet till `button`.

* Använd trollstavsikonen överst i designern för att härleda layoutbegränsningar.

  ![Skärmbild trollstavsikonen](media/sdk/qs-java-android-10-infer-layout-constraints.png)

Texten och den grafiska representationen av ditt användargränssnitt bör nu se ut så här.

<table>
<tr>
<td valign="top">
![](media/sdk/qs-java-android-11-gui.png)
</td>
<td valign="top">
[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/res/layout/activity_main.xml)]
</td>
</tr>
</table>

## <a name="add-sample-code"></a>Lägg till exempelkod

1. Öppna till källfilen `MainActivity.java`. Ersätt koden efter instruktionen `package` med följande.

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Metoden `onCreate` inkluderar den kod som begär mikrofon- och internet-behörigheter och initierar inbyggd plattformsbindning. Du behöver bara konfigurera inbyggda plattformsbindningar en gång. Det bör göras tidigt under initieringen av programmet.
   
   * Metoden `onSpeechButtonClicked` är, som tidigare nämnts, knappklickshanteraren. En knapptryckning utlöser transkription från tal till text.

1. Ersätt strängen `YourSubscriptionKey` i samma fil med din prenumerationsnyckel.

1. Ersätt även strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

## <a name="build-and-run-the-app"></a>Skapa och kör appen

1. Anslut din Android-enhet till utvecklingsdatorn. Kontrollera att du har aktiverat [utvecklingsläge och USB-felsökning](https://developer.android.com/studio/debug/dev-options) på enheten.

1. Tryck på Ctrl + F9 för att skapa programmet, eller välj **Skapa** > **Skapa projekt** på menyraden.

1. Tryck på Skift + F10 för att starta programmet, eller välj **Kör** > **Kör ”app”**.

1. Välj din Android-enhet i målfönstret för distribution som visas.

   ![Skärmbild av fönstret för att välja distributionsmål](media/sdk/qs-java-android-12-deploy.png)

Tryck på knappen i programmet för att starta en taligenkänningssession. De nästa 15 sekunderna med engelskt tal skickas till Speech-tjänsten och transkriberas. Resultatet visas i Android-programmet och i fönstret logcat i Android Studio.

![Skärmbild av Android-programmet](media/sdk/qs-java-android-13-gui-on-device.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Leta efter det här exemplet i mappen `quickstart/java-android`.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Identifiera avsikter från tal med hjälp av Speech SDK för Java](how-to-recognize-intents-from-speech-java.md)

## <a name="see-also"></a>Se även

- [Översätta tal](how-to-translate-speech-csharp.md)
- [Anpassa akustiska modeller](how-to-customize-acoustic-models.md)
- [Anpassa språkmodeller](how-to-customize-language-model.md)
