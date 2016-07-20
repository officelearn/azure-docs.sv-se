<properties
    pageTitle="Komma igång med Azure Mobile Engagement"
    description="Lär dig hur du använder Azure Mobile Engagement med analyser och push-meddelanden för Android-appar."
    services="mobile-engagement"
    documentationCenter="android"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="hero-article"
    ms.date="05/12/2016"
    ms.author="piyushjo;ricksal" />

# Komma igång med Azure Mobile Engagement för Android-appar

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

I det här avsnittet beskrivs hur du använder Azure Mobile Engagement för att förstå appanvändningen, och hur du skickar push-meddelanden till segmenterade användare i en Android-app.
I den här självstudiekursen visas ett enkelt scenario för sändning med Mobile Engagement. I självstudiekursen skapar du en tom Android-app som samlar in grundläggande data och tar emot push-meddelanden med Google Cloud Messaging (GCM).

## Nödvändiga komponenter

För den här kursen krävs [Android Developer Tools](https://developer.android.com/sdk/index.html) (utvecklingsverktyg för Android) som omfattar Android Studio Integrated Development Environment och den senaste Android-plattformen.

Du behöver även [Mobile Engagement Android SDK](https://aka.ms/vq9mfn).

> [AZURE.IMPORTANT] Du behöver ett Azure-konto för att slutföra den här självstudiekursen. Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [här](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-android-get-started).

## Konfigurera Mobile Engagement för din Android-app

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal.md)]

## Anslut appen till Mobile Engagement-serverdelen

I den här självstudien anges en ”grundläggande integration”, vilket är den minsta uppsättningen som krävs för att samla in data och skicka ett push-meddelande. Den fullständiga integrationsdokumentationen finns i [Mobile Engagement Android SDK-integration](mobile-engagement-android-sdk-overview.md)

Vi skapar en grundläggande app i Android Studio för att demonstrera integrationen.

### Skapa ett nytt Android-projekt

1. Starta **Android Studio** och välj **Start a new Android Studio project** (Starta ett nytt Android Studio-projekt) i popup-fönstret.

    ![][1]

2. Ange ett namn för appen och en företagsdomän. Anteckna vad du fyller i eftersom du kommer att använda samma information senare. Klicka på **Nästa**.

    ![][2]

3. Välj formfaktor för mål samt API-nivå och klicka på **Nästa**.

    >[AZURE.NOTE] Mobile Engagement kräver en API-nivå på minst 10 (Android 2.3.3).

    ![][3]

4. Välj **Blank Activity** (Tom aktivitet) och klicka på **Nästa**. Det här är den enda skärm som visas i appen.

    ![][4]

5. Låt standardvärdena vara och klicka på **Slutför**.

    ![][5]

Android Studio skapar nu demoappen där Mobile Engagement ska integreras.

### Inkludera SDK-biblioteket i ditt projekt

1. Ladda ned [Mobile Engagement Android SDK].
2. Extrahera arkivfilen till en mapp på datorn.
3. Hitta .jar-biblioteket för den aktuella SDK-versionen och kopiera det till Urklipp.

      ![][6]

4. Navigera till avsnittet **Projekt** (1) och klistra in .jar i biblioteksmappen (2).

      ![][7]

5. Synkronisera projektet för att läsa in biblioteket.

      ![][8]

### Anslut appen till Mobile Engagement-serverdelen med anslutningssträngen

1. Kopiera följande rader med kod till den aktivitet som skapas (får endast göras på ett ställe i appen, vanligtvis i huvudaktiviteten). I den här exempelappen öppnar du MainActivity i mappen src -> main -> java och lägger till följande:

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
        EngagementAgent.getInstance(this).init(engagementConfiguration);

2. Matcha referenserna genom att trycka på Alt + Retur eller lägga till följande importuttryck:

        import com.microsoft.azure.engagement.EngagementAgent;
        import com.microsoft.azure.engagement.EngagementConfiguration;

3. Gå tillbaka till den klassiska Azure-portalen via appsidan **Anslutningsinformation** och kopiera **anslutningssträngen**.

      ![][9]

4. Klistra in den i parametern `setConnectionString` för att ersätta det exempel som visas nedan:

        engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

### Lägg till behörigheter och en tjänstedeklaration

1. Lägg till följande behörigheter i Manifest.xml i ditt projekt omedelbart före eller efter taggen `<application>`:

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

2. Lägg till följande mellan taggarna `<application>` och `</application>` för att deklarera agenttjänsten:

        <service
            android:name="com.microsoft.azure.engagement.service.EngagementService"
            android:exported="false"
            android:label="<Your application name>"
            android:process=":Engagement"/>

3. Ersätt `"<Your application name>"` i etiketten i den kod som du precis klistrade in. Detta visas på **inställningsmenyn** där användarna kan se vilka tjänster som körs på enheten. Du kan till exempel lägga till ordet ”Service” eller ”Tjänst” i etiketten.

### Skicka en skärm till Mobile Engagement

Du måste skicka minst en skärm (aktivitet) till Mobile Engagement-serverdelen för att kunna börja skicka data och se till att användarna aktiveras.

Gå till **MainActivity.java** och lägg till följande om du vill byta ut basklassen **MainActivity** mot **EngagementActivity**:

    public class MainActivity extends EngagementActivity {

> [AZURE.NOTE] Om din basklass inte är *aktivitet* läser du [Avancerad Android-rapportering](mobile-engagement-android-advanced-reporting.md#modifying-your-codeactivitycode-classes) för att få veta hur du gör för att ärva från olika klasser.


Du bör kommentera ut (exkludera) följande rad i detta enkla exempelscenario:

    // setSupportActionBar(toolbar);

Om du vill behålla detta bör du först läsa [Avancerad Android-rapportering](mobile-engagement-android-advanced-reporting.md#modifying-your-codeactivitycode-classes).

## Anslut appen med realtidsövervakning

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## Aktivera push-meddelanden och meddelanden i appen

Med Mobile Engagement kan du samverka med användarna, nå ut till dem och köra kampanjer med push-meddelanden och meddelanden i appen. Modulen som används till detta heter REACH i Mobile Engagement-portalen.
I följande avsnitt konfigurerar du appen för att ta emot dem.

### Kopiera SDK-resurser i ditt projekt

1. Gå tillbaka till den nedladdade SDK-filen och kopiera mappen **res**.

    ![][10]

2. Gå tillbaka till Android Studio, markera **huvudkatalogen (main)** för dina projektfiler och klistra in den för att lägga till resurser i projektet.

    ![][11]

[AZURE.INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[AZURE.INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[AZURE.INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

## Nästa steg

Gå till [Android SDK](mobile-engagement-android-sdk-overview.md) för detaljerad information om SDK-integration.

<!-- Images. -->
[1]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[2]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[3]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[4]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[5]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[6]: ./media/mobile-engagement-android-get-started/sdk-content.png
[7]: ./media/mobile-engagement-android-get-started/paste-jar.png
[8]: ./media/mobile-engagement-android-get-started/sync-project.png
[9]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[10]: ./media/mobile-engagement-android-get-started/copy-resources.png
[11]: ./media/mobile-engagement-android-get-started/paste-resources.png



<!--HONumber=Jun16_HO2-->


