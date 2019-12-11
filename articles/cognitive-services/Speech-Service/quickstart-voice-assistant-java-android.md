---
title: 'Snabb start: anpassad röst assistent, Java (Android) – tal tjänst'
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar ett röst assistents program i Java på Android med hjälp av talet SDK
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: travisw
ms.openlocfilehash: 70031513d6f120e539e80d6befad4bf9320fb9c4
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975966"
---
# <a name="quickstart-create-a-voice-assistant-in-java-on-android-by-using-the-speech-sdk"></a>Snabb start: skapa en röst assistent i Java på Android med hjälp av talet SDK

Det finns även en snabb start för [tal-till-text](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android) och [text till tal](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-java&tabs=android).

I den här artikeln skapar du en röst assistent med Java för Android med hjälp av [talet SDK](speech-sdk.md). Det här programmet ansluter till en robot som du redan har skapat och konfigurerat med den [direkta rad igenkännings kanalen](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech). Sedan skickas en röst förfrågan till roboten och visar en röst aktive rad svars aktivitet.

Det här programmet har skapats med tal SDK maven-paketet och Android Studio 3,3. Speech SDK är kompatibelt med Android-enheter med 32/64-bitars ARM-processorer och Intel x86/x64-kompatibla processorer.

> [!NOTE]
> Information om Speech Devices SDK och Roobo-enheten finns i [Speech Devices SDK](speech-devices-sdk.md).

## <a name="prerequisites"></a>Krav

- En Azure-prenumerationsnyckel för tjänsten Speech. [Hämta ett kostnads fritt](get-started.md) eller skapa det på [Azure Portal](https://portal.azure.com).
- En tidigare skapad robot som kon figurer ATS med den [direkta linjens tal kanal](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Android Studio](https://developer.android.com/studio/) v 3.3 eller senare

    > [!NOTE]
    > Se [listan över regioner som stöds för röst assistenter](regions.md#voice-assistants) och se till att dina resurser distribueras i någon av dessa regioner.

## <a name="create-and-configure-a-project"></a>Skapa och konfigurera ett projekt

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>Skapa användargränssnitt

I det här avsnittet ska vi skapa ett grundläggande användar gränssnitt (UI) för programmet. Vi börjar med att öppna huvud aktiviteten: `activity_main.xml`. Den grundläggande mallen innehåller en namn List med programmets namn och en `TextView` med meddelandet "Hello World!".

Ersätt sedan innehållet i `activity_main.xml` med följande kod:

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

I den här XML-koden definieras ett enkelt gränssnitt för att interagera med din robot.

- `button`-elementet initierar en interaktion och anropar `onBotButtonClicked`-metoden när du klickar på den.
- `recoText`-elementet visar tal-till-text-resultatet när du pratar med din robot.
- `activityText`-elementet visar JSON-nyttolasten för den senaste bot Framework-aktiviteten från din robot.

Texten och den grafiska representationen av ditt användargränssnitt bör nu se ut så här:

![](media/sdk/qs-java-android-assistant-designer-ui.png)

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

   * Metoden `onCreate` innehåller kod som begär mikrofon-och Internet-behörigheter.

   * Metoden `onBotButtonClicked` är, som tidigare nämnts, knappklickshanteraren. En knapp tryckning utlöser en enda interaktion ("Vänd") med din robot.

   * Metoden `registerEventListeners` visar de händelser som används av `DialogServiceConnector` och grundläggande hantering av inkommande aktiviteter.

1. I samma fil ersätter du konfigurations strängarna så att de matchar dina resurser:

    * Ersätt `YourChannelSecret` med den direkta linjens tal kanal hemlighet för din robot.

    * Ersätt `YourSpeechSubscriptionKey` med din prenumerationsnyckel.

    * Ersätt `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration endast en delmängd av tal service regionerna stöds för närvarande med direkt linje tal. Mer information finns i [regioner](regions.md#voice-assistants).

## <a name="build-and-run-the-app"></a>Skapa och kör appen

1. Anslut din Android-enhet till utvecklingsdatorn. Kontrollera att du har aktiverat [utvecklingsläge och USB-felsökning](https://developer.android.com/studio/debug/dev-options) på enheten.

1. Tryck på Ctrl + F9 för att skapa programmet, eller välj **Skapa** > **Skapa projekt** på menyraden.

1. Tryck på Skift + F10 för att starta programmet, eller välj **Kör** > **Kör ”app”** .

1. Välj din Android-enhet i målfönstret för distribution som visas.

   ![Skärmbild av fönstret för att välja distributionsmål](media/sdk/qs-java-android-12-deploy.png)

När programmet och dess aktivitet har startats klickar du på knappen för att börja prata med din robot. Texten som skrivs in visas när du talar och den senaste aktiviteten har du fått från din robot visas när den tas emot. Om din robot har kon figurer ATS för att ge talade svar spelas tal-till-text upp automatiskt.

![Skärmbild av Android-programmet](media/sdk/qs-java-android-assistant-completed-turn.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa och distribuera en grundläggande robot](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>Se också
- [Om röst assistenter](voice-assistants.md)
- [Hämta en prenumerations nyckel för Speech service kostnads fritt](get-started.md)
- [Anpassade nyckelord](speech-devices-sdk-create-kws.md)
- [Anslut direkt linje tal till din robot](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Utforska Java-exempel på GitHub](https://aka.ms/csspeech/samples)
