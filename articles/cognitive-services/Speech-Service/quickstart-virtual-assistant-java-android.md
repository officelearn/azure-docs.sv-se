---
title: 'Snabbstart: Anpassade röst första virtuella assistenter (förhandsversion), Java (Android) – Speech Services'
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar en röst-första virtuella assistenter program i Java på Azure med hjälp av tal-SDK
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 5/24/2019
ms.author: travisw
ms.openlocfilehash: 7e82b2ef9500defe0d08351da1e3487e4671155f
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67467032"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-in-java-on-android-by-using-the-speech-sdk"></a>Snabbstart: Skapa en röst-första virtuella assistenter i Java på Azure med hjälp av tal-SDK

Det finns även en Snabbstart för [tal till text](quickstart-java-android.md).

I den här artikeln ska du skapa en röst-första virtuella assistenter med Java för Android med hjälp av den [tal SDK](speech-sdk.md). Det här programmet ska ansluta till en robot som du redan har skapats och konfigurerats med den [Direct Line tal kanal](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech). Den sedan skicka en röst-begäran till roboten och presentera en röst-aktiverade svar-aktivitet.

Det här programmet har byggts med tal SDK Maven-paketet och Android Studio 3.3. Speech SDK är kompatibelt med Android-enheter med 32/64-bitars ARM-processorer och Intel x86/x64-kompatibla processorer.

> [!NOTE]
> Information om Speech Devices SDK och Roobo-enheten finns i [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration-nyckel för Speech Services. [Skaffa ett kostnadsfritt](get-started.md) eller skapa den på den [Azure-portalen](https://portal.azure.com).
* En tidigare skapad robot som konfigurerats med den [tal för Direct Line-kanal](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
* [Android Studio](https://developer.android.com/studio/) v3.3 eller senare

    > [!NOTE]
    > Direct Line-tal (förhandsversion) är för närvarande tillgängligt i en delmängd av Speech Services-regioner. Se [listan över regioner som stöds för röst-första virtuella assistenter](regions.md#Voice-first virtual assistants) och se till att resurserna distribueras i någon av dessa regioner.

## <a name="create-and-configure-a-project"></a>Skapa och konfigurera ett projekt

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>Skapa användargränssnitt

I det här avsnittet ska skapa vi ett grundläggande användargränssnitt (UI) för programmet. Låt oss börja med att öppna i huvudaktiviteten: `activity_main.xml`. Grundläggande mallen innehåller en namnlist med programmets namn och en `TextView` med meddelandet ”Hello world”!.

Ersätt innehållet i den `activity_main.xml` med följande kod:

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:onClick="onBotButtonClicked"
        android:text="Talk to your bot" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Recognition Data"
        android:textSize="18dp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/recoText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="  \n(Recognition goes here)\n" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Activity Data"
        android:textSize="18dp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/activityText"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:scrollbars="vertical"
        android:text="  \n(Activities go here)\n" />

   </LinearLayout>
   ```

Den här XML definierar ett enkelt gränssnitt för att interagera med din robot.

* Den `button` element initierar en interaktion och anropar den `onBotButtonClicked` metoden när du klickar på.
* Den `recoText` element visas resultatet tal till text som du prata med din robot.
* Den `activityText` element visas JSON-nyttolast för den senaste Bot Framework-aktiviteten från din robot.

Texten och den grafiska representationen av ditt användargränssnitt bör nu se ut så här:

![](media/sdk/qs-java-android-assistant-designer-ui.png)

## <a name="add-sample-code"></a>Lägga till exempelkod

1. Öppna `MainActivity.java`, och Ersätt innehållet med följande kod:

   ```java
    package samples.speech.cognitiveservices.microsoft.com;

    import android.media.AudioFormat;
    import android.media.AudioManager;
    import android.media.AudioTrack;
    import android.support.v4.app.ActivityCompat;
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import android.text.method.ScrollingMovementMethod;
    import android.view.View;
    import android.widget.TextView;

    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConfig;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConnector;

    import org.json.JSONException;
    import org.json.JSONObject;

    import static android.Manifest.permission.*;

    public class MainActivity extends AppCompatActivity {
        // Replace below with your bot's own Direct Line Speech channel secret
        private static String channelSecret = "YourChannelSecret";
        // Replace below with your own speech subscription key
        private static String speechSubscriptionKey = "YourSpeechSubscriptionKey";
        // Replace below with your own speech service region (note: only a subset of regions are currently supported)
        private static String serviceRegion = "YourSpeechServiceRegion";

        private DialogServiceConnector connector;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            TextView recoText = (TextView) this.findViewById(R.id.recoText);
            TextView activityText = (TextView) this.findViewById(R.id.activityText);
            recoText.setMovementMethod(new ScrollingMovementMethod());
            activityText.setMovementMethod(new ScrollingMovementMethod());

            // Note: we need to request permissions for audio input and network access
            int requestCode = 5; // unique code for the permission request
            ActivityCompat.requestPermissions(MainActivity.this, new String[]{RECORD_AUDIO, INTERNET}, requestCode);
        }

        public void onBotButtonClicked(View v) {
            // Recreate the DialogServiceConnector on each button press, ensuring that the existing one is closed
            if (connector != null) {
                connector.close();
                connector = null;
            }

            // Create the DialogServiceConnector from the channel and speech subscription information
            DialogServiceConfig config = DialogServiceConfig.fromBotSecret(channelSecret, speechSubscriptionKey, serviceRegion);
            connector = new DialogServiceConnector(config, AudioConfig.fromDefaultMicrophoneInput());

            // Optional step: preemptively connect to reduce first interaction latency
            connector.connectAsync();

            // Register the DialogServiceConnector's event listeners
            registerEventListeners();

            // Begin sending audio to your bot
            connector.listenOnceAsync();
        }

        private void registerEventListeners() {
            TextView recoText = (TextView) this.findViewById(R.id.recoText); // 'recoText' is the ID of your text view
            TextView activityText = (TextView) this.findViewById(R.id.activityText); // 'activityText' is the ID of your text view

            // Recognizing will provide the intermediate recognized text while an audio stream is being processed
            connector.recognizing.addEventListener((o, recoArgs) -> {
                recoText.setText("  Recognizing: " + recoArgs.getResult().getText());
            });

            // Recognized will provide the final recognized text once audio capture is completed
            connector.recognized.addEventListener((o, recoArgs) -> {
                recoText.setText("  Recognized: " + recoArgs.getResult().getText());
            });

            // SessionStarted will notify when audio begins flowing to the service for a turn
            connector.sessionStarted.addEventListener((o, sessionArgs) -> {
                recoText.setText("Listening...");
            });

            // SessionStopped will notify when a turn is complete and it's safe to begin listening again
            connector.sessionStopped.addEventListener((o, sessionArgs) -> {
            });

            // Canceled will be signaled when a turn is aborted or experiences an error condition
            connector.canceled.addEventListener((o, canceledArgs) -> {
                recoText.setText("Canceled (" + canceledArgs.getReason().toString() + ") error details: {}" + canceledArgs.getErrorDetails());
                connector.disconnectAsync();
            });

            // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities.
            connector.activityReceived.addEventListener((o, activityArgs) -> {
                try {
                    // Here we use JSONObject only to "pretty print" the condensed Activity JSON
                    String rawActivity = activityArgs.getActivity().serialize();
                    String formattedActivity = new JSONObject(rawActivity).toString(2);
                    activityText.setText(formattedActivity);
                } catch (JSONException e) {
                    activityText.setText("Couldn't format activity text: " + e.getMessage());
                }

                if (activityArgs.hasAudio()) {
                    // Text-to-speech audio associated with the activity is 16 kHz 16-bit mono PCM data
                    final int sampleRate = 16000;
                    int bufferSize = AudioTrack.getMinBufferSize(sampleRate, AudioFormat.CHANNEL_OUT_MONO, AudioFormat.ENCODING_PCM_16BIT);

                    AudioTrack track = new AudioTrack(
                            AudioManager.STREAM_MUSIC,
                            sampleRate,
                            AudioFormat.CHANNEL_OUT_MONO,
                            AudioFormat.ENCODING_PCM_16BIT,
                            bufferSize,
                            AudioTrack.MODE_STREAM);

                    track.play();

                    PullAudioOutputStream stream = activityArgs.getAudio();

                    // Audio is streamed as it becomes available. Play it as it arrives.
                    byte[] buffer = new byte[bufferSize];
                    long bytesRead = 0;

                    do {
                        bytesRead = stream.read(buffer);
                        track.write(buffer, 0, (int) bytesRead);
                    } while (bytesRead == bufferSize);

                    track.release();
                }
            });
        }
    }
   ```

   * Den `onCreate` metoden innehåller kod som begär behörigheter mikrofon och internet.

   * Metoden `onBotButtonClicked` är, som tidigare nämnts, knappklickshanteraren. Tryck på en knapp utlöser en enda interaktion (”aktivera”) med din robot.

   * Den `registerEventListeners` metoden visar händelser som används av den `DialogServiceConnector` och grundläggande hantering av inkommande aktiviteter.

1. Ersätt konfigurationssträngarna för att matcha dina resurser i samma fil:

    * Ersätt `YourChannelSecret` med Direct Line tal kanal hemligheten för din robot.

    * Ersätt `YourSpeechSubscriptionKey` med din prenumerationsnyckel.

    * Ersätt `YourServiceRegion` med den [region](regions.md) som är associerade med prenumerationen endast en delmängd av Speech Services regioner stöds för närvarande med Direct Line-tal. Mer information finns i [regioner](regions.md#voice-first-virtual-assistants).

## <a name="build-and-run-the-app"></a>Skapa och kör appen

1. Anslut din Android-enhet till utvecklingsdatorn. Kontrollera att du har aktiverat [utvecklingsläge och USB-felsökning](https://developer.android.com/studio/debug/dev-options) på enheten.

1. Tryck på Ctrl + F9 för att skapa programmet, eller välj **Skapa** > **Skapa projekt** på menyraden.

1. Tryck på Skift + F10 för att starta programmet, eller välj **Kör** > **Kör ”app”** .

1. Välj din Android-enhet i målfönstret för distribution som visas.

   ![Skärmbild av fönstret för att välja distributionsmål](media/sdk/qs-java-android-12-deploy.png)

När programmet och dess aktiviteter har startats klickar du på knappen för att börja prata din robot. Transkriberade texten visas som du tala och den senaste aktiviteten har du fått från din bot kommer visas när den tas emot. Om din robot är konfigurerad för att tillhandahålla talat svar, spelas automatiskt tal till text.

![Skärmbild av Android-programmet](media/sdk/qs-java-android-assistant-completed-turn.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa och distribuera en grundläggande bot](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>Se också
- [Om röst första virtuella assistenter](voice-first-virtual-assistants.md)
- [Skaffa en prenumerationsnyckel för Speech Services utan kostnad](get-started.md)
- [Anpassad aktivering ord](speech-devices-sdk-create-kws.md)
- [Anslut direkt rad tal till din robot](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Utforska Java-exempel på GitHub](https://aka.ms/csspeech/samples)
