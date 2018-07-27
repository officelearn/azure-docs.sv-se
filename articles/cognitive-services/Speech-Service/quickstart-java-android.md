---
title: 'Snabbstart: Känna igen tal i Java på Android med hjälp av Cognitive Services tal SDK'
titleSuffix: Microsoft Cognitive Services
description: Lär dig att känna igen tal i Java på Android med hjälp av Cognitive Services tal SDK
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: fmegen
ms.openlocfilehash: 585728d95db800f32d98d71cf0c64e689845794f
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285518"
---
# <a name="quickstart-recognize-speech-in-java-on-android-using-the-speech-sdk"></a>Snabbstart: Känna igen tal i Java på Android med hjälp av tal-SDK

I den här artikeln lär du dig att skapa ett Java-program för Android med hjälp av Cognitive Services tal SDK för att transkribera tal till text.
Programmet är baserad på Microsoft Cognitive Services tal SDK Maven-paketet, version 0.5.0 och Android Studio 3.1.

> [!NOTE]
> Tal Devices SDK Roobo-enhet, finns det [tal Devices SDK](speech-devices-sdk.md) sidan.

## <a name="prerequisites"></a>Förutsättningar

* En prenumerationsnyckel för Speech-tjänsten. Se [prova speech-tjänsten utan kostnad](get-started.md).
* En dator (Windows, Linux, Mac) kapacitet att köra Android Studio.
* Version 3.1 av [Android Studio](https://developer.android.com/studio/).
* En ARM-baserade Android-enhet (API-23: Android 6.0 Marshmallow eller senare) [aktiverat för utveckling](https://developer.android.com/studio/debug/dev-options) med en fungerande mikrofon.

## <a name="create-an-android-studio-project"></a>Skapa ett Android Studio-projekt

Starta Android Studio och välj **starta ett nytt Android Studio-projekt**.

![](media/sdk/qs-java-android-01-start-new-android-studio-project.png)

I den **Skapa nytt projekt** guiden som visas gör du följande val:

1. I den **skapa Androidprojektet** anger **snabbstarten** som **programnamn**, **samples.speech.cognitiveservices.microsoft.com** som **företagsdomän**, och väljer en projektplats. Lämnar du kryssrutorna avmarkerad och klickar på **nästa**.

   ![](media/sdk/qs-java-android-02-create-android-project.png)

1. I den **Android målenheter** kan du kontrollera **Telefoner och surfplattor** som det enda alternativet Välj **API-23: Android 6.0 (Marshmallow)** under den och klicka på listrutan **Nästa**.

   ![](media/sdk/qs-java-android-03-target-android-devices.png)

1. I den **lägga till en aktivitet till Mobile** väljer **tom aktivitet** och klicka på **nästa**.

   ![](media/sdk/qs-java-android-04-add-an-activity-to-mobile.png)

1. I den **konfigurera aktiviteten** hjälp **MainActivity** som aktivitetsnamnet och **aktivitet\_huvudsakliga** som namn på den Layout. Båda kryssrutorna och klicka på **Slutför**.

   ![](media/sdk/qs-java-android-05-configure-activity.png)

Efter att ha kört ett tag, bör det nyskapade projektet i Android Studio kommer.

## <a name="configure-your-project-for-the-speech-sdk"></a>Konfigurera ditt projekt för tal-SDK

[!include[License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Den aktuella versionen av Cognitive Services tal SDK är `0.5.0`.

Tal-SDK för Android kommer som ett [AAR (Android-biblioteket)](https://developer.android.com/studio/projects/android-library), som innehåller nödvändiga bibliotek samt Android behörigheter som krävs för att använda den.
Den finns i ett Maven-centrallager på https://csspeechstorage.blob.core.windows.net/maven/.

Beskrivs nedan hur du ställer in ditt projekt att använda SDK: N för tal.

Öppna fönstret projekt struktur under **filen** \> **projektstruktur**.
I fönstret som visas gör följande ändringar (klicka på **OK** bara när du har slutfört alla steg):

1. Välj **projekt**, och redigera den **standard biblioteket databasen** inställningar genom att lägga till ett kommatecken och våra URL till Maven-lagringsplats inom enkla citattecken `'https://csspeechstorage.blob.core.windows.net/maven/'`:

  ![](media/sdk/qs-java-android-06-add-maven-repository.png)

1. Fortfarande på samma skärm till vänster, Välj den **App** modulen, och längst upp i **beroenden** fliken. Sedan klickar du på grönt plustecken i det övre högra hörnet och välj **biblioteket beroende**.

  ![](media/sdk/qs-java-android-07-add-module-dependency.png)

1. I fönstret som visas anger du namnet och versionen av vår tal-SDK för Android, `com.microsoft.cognitiveservices.speech:client-sdk:0.5.0`, klicka sedan på **OK**.
   Tal-SDK ska läggas till i listan över beroenden nu enligt nedan:

  ![](media/sdk/qs-java-android-08-dependency-added.png)

1. I övre, Välj den **egenskaper** fliken. Välj **1.8** både för **Source Compability** och **rikta kompatibilitet**.

  ![](media/sdk/qs-java-android-09-dependency-added.png)

1. Klicka slutligen på **OK** att Stäng den **projektstruktur** windows och installera alla uppdateringar.

## <a name="create-a-minimal-ui"></a>Skapa ett minimalt gränssnitt

Redigera layouten för dina huvudsakliga aktiviteter `activity_main.xml`.
Som standard bör den få fram en namnlist med programmets namn och en TextView med texten ”Hello World”!.

* Klicka på TextView. Ändra dess ID-attribut i det övre högra hörnet att `hello`.

* I paletten i det övre vänstra hörnet av din `activity_main.xml` och dra en knapp i det tomma utrymmet ovanför texten.

* I knappens attribut till höger, i värdet för den `onClick` attributet, ange `onSpeechButtonClicked`, som kommer att vara namnet på vår knappen-hanteraren.
  Ändra dess ID-attribut i det övre högra hörnet att `button`.

* Använd ikonen magic Trollstav överst i designern om du vill att härleda layout begränsningar för dig.

  ![](media/sdk/qs-java-android-10-infer-layout-constraints.png)

Text och grafiska version i Gränssnittet bör nu se ut ungefär så här:

<table>
<tr>
<td valign="top">
![](media/sdk/qs-java-android-11-gui.png)
</td>
<td valign="top">
[! code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/res/layout/activity_main.xml)]
</td>
</tr>
</table>

## <a name="add-the-sample-code"></a>Lägg till exempelkoden

1. Redigera den `MainActivity.java` källfil och Ersätt Koden med följande (under kontoutdraget paketet):

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * Den `onCreate` metoden inkluderar den kod som begär mikrofon och Internet-behörigheter som initierar inbyggd plattform bindningen. Konfigurera inbyggd plattform-bindningar är endast nödvändiga en gång, det vill säga den bör göras tidigt under initieringen av programmet.
   
   * Metoden `onSpeechButtonClicked` har tidigare utformad så klickar du på knappen på hanteraren. Tryck på en knapp utlöser den faktiska taligenkänningen.

1. Ersätt strängen `YourSubscriptionKey` med din prenumerationsnyckel.

1. Ersätt strängen `YourServiceRegion` med den [region](regions.md) som är associerade med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

## <a name="build-and-run-the-sample"></a>Skapa och köra exempelappen

* Att bygga, tryck på Ctrl + F9 eller välj **skapa** \> **se projekt**.

* Anslut din Android-enhet till utvecklingen av din dator. Kontrollera att du har [utvecklingsläge och USB-felsökning aktiverat](https://developer.android.com/studio/debug/dev-options).

* Tryck på SKIFT + F10 för att starta appen, eller välj **kör** \> **kör ”app”**.

* I windows för distribution-mål som visas, väljer du en Android-enhet.

  ![Starta appen into-felsökning](media/sdk/qs-java-android-12-deploy.png)

* Appen ska starta på din enhet.
  När du trycker på knappen Nästa 15 sekunder ska identifieras och visas i Användargränssnittet (du bör även kunna se svar i fönstret logcat i Android Studio):

  ![Användargränssnittet efter lyckad taligenkänning](media/sdk/qs-java-android-13-gui-on-device.png)

Den här skärmbilden avslutar Android Snabbstart. Exempelkoden-projekt kan hämtas från exempeldatabasen.

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Leta efter det här exemplet i den `quickstart/java-android` mapp.

## <a name="next-steps"></a>Nästa steg

* Gå till den [sidan med kodexempel](samples.md) ytterligare exempel.
