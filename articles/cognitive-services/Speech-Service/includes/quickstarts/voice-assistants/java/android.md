---
title: 'Snabbstart: Skapa en anpassad röstassistent, Java (Android) - Taltjänst'
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar en anpassad röstassistent i Java på Android med hjälp av Speech SDK.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: travisw
ms.openlocfilehash: 8a1dd07fd567f41c2b406aabccd0421b5a6983af
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671816"
---
## <a name="prerequisites"></a>Krav

Innan du börjar måste du:

> [!div class="checklist"]
> * [Skapa en Azure Speech-resurs](~/articles/cognitive-services/speech-service/get-started.md)
> * [Ställ in utvecklingsmiljön och skapa ett tomt projekt](~/articles/cognitive-services/speech-service/quickstarts/setup-platform.md?tabs=android&pivots=programming-language-java)
> * Skapa en robot som är ansluten till [direktlinjetalskanalen](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
> * Se till att du har tillgång till en mikrofon för ljudinspelning

  > [!NOTE]
  > Se [listan över regioner som stöds för röstassistenter](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) och se till att dina resurser distribueras i en av dessa regioner.

## <a name="create-and-configure-a-project"></a>Skapa och konfigurera ett projekt

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>Skapa användargränssnitt

I det här avsnittet skapar vi ett grundläggande användargränssnitt för programmet. Låt oss börja med att `activity_main.xml`öppna huvudaktiviteten: . Den grundläggande mallen innehåller en namnlist med `TextView` programmets namn och a med meddelandet "Hello world!".

Ersätt sedan innehållet i `activity_main.xml` innehållet i koden med följande kod:

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

Den här XML-koden definierar ett enkelt användargränssnitt för att interagera med din robot.

- Elementet `button` initierar en interaktion `onBotButtonClicked` och anropar metoden när du klickar på den.
- Elementet `recoText` visar tal-till-text-resultaten när du pratar med din robot.
- Elementet `activityText` visar JSON-nyttolasten för den senaste Bot Framework-aktiviteten från din bot.

Texten och den grafiska representationen av ditt användargränssnitt bör nu se ut så här:

![](~/articles/cognitive-services/speech-service/media/sdk/qs-java-android-assistant-designer-ui.png)

## <a name="add-sample-code"></a>Lägg till exempelkod

1. Öppna `MainActivity.java`och ersätt innehållet med följande kod:

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
        // Replace below with your own speech subscription key
        private static String speechSubscriptionKey = "YourSpeechSubscriptionKey";
        // Replace below with your own speech service region
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

            // Create the DialogServiceConnector from speech subscription information
            BotFrameworkConfig config = BotFrameworkConfig.fromSubscription(speechSubscriptionKey, serviceRegion);
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

   * Metoden `onCreate` innehåller kod som begär mikrofon- och internetbehörigheter.

   * Metoden `onBotButtonClicked` är, som tidigare nämnts, knappklickshanteraren. En knapptryckning utlöser en enda interaktion ("turn") med din bot.

   * Metoden `registerEventListeners` visar de händelser som `DialogServiceConnector` används av och grundläggande hantering av inkommande aktiviteter.

1. I samma fil ersätter du konfigurationssträngarna så att de matchar dina resurser:

    * Ersätt `YourSpeechSubscriptionKey` med din prenumerationsnyckel.

    * Ersätt `YourServiceRegion` med den [region som](~/articles/cognitive-services/speech-service/regions.md) är associerad med din prenumeration Endast en delmängd av taltjänstregioner stöds för närvarande med direkt radtal. Mer information finns i [regioner](~/articles/cognitive-services/speech-service/regions.md#voice-assistants).

## <a name="build-and-run-the-app"></a>Skapa och kör appen

1. Anslut din Android-enhet till utvecklingsdatorn. Kontrollera att du har aktiverat [utvecklingsläge och USB-felsökning](https://developer.android.com/studio/debug/dev-options) på enheten.

1. Om du vill skapa programmet trycker du på Ctrl+F9 eller väljer **Skapa** > **projekt** på menyraden.

1. Om du vill starta programmet trycker du på Skift+F10 eller väljer **Kör** > **kör "app".**

1. Välj din Android-enhet i målfönstret för distribution som visas.

   ![Skärmbild av fönstret för att välja distributionsmål](~/articles/cognitive-services/speech-service/media/sdk/qs-java-android-12-deploy.png)

När programmet och dess aktivitet har startat, klicka på knappen för att börja prata med din bot. Transkriberad text visas när du talar och den senaste aktiviteten har du fått från din bot visas när den tas emot. Om din bot är konfigurerad för att ge talade svar spelas tal till text automatiskt upp.

![Skärmbild av Android-programmet](~/articles/cognitive-services/speech-service/media/sdk/qs-java-android-assistant-completed-turn.png)

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]

