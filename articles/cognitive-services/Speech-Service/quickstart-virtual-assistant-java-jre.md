---
title: 'Snabbstart: Anpassade röst första virtuella assistenter (förhandsversion), Java (Windows, Linux) – Speech Services'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten får du lära dig hur du använder den Cognitive Services tal Software Development Kit (SDK) i ett Java-konsolprogram. Du får lära dig hur du kan ansluta ditt klientprogram till en tidigare skapad Bot Framework-robot som konfigurerats att använda Direct Line tal kanalen och aktivera en röst-första virtuella assistenter upplevelse.
services: cognitive-services
author: bidishac
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: bidishac
ms.openlocfilehash: 4e9010bed54d0b2a7cb1a95b9e01e5ba02ea9fd5
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027021"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-with-the-speech-sdk-java"></a>Snabbstart: Skapa en röst-första virtuella assistenter med Speech-SDK för Java

I den här artikeln skapar du ett Java-konsolprogram med hjälp av den [Cognitive Services tal SDK](speech-sdk.md). Programmet ansluter till en tidigare skapade robot som konfigurerats för att använda tal för Direct Line-kanal, skicka en förfrågan om röst och returnera en röst svar aktivitet (om konfigurerad). Programmet har byggts med tal SDK Maven-paketet och Java Eclipse IDE på Windows, Ubuntu Linux eller macOS. Det körs i en 64-bitars Java 8-körningsmiljö (JRE).

## <a name="prerequisites"></a>Nödvändiga komponenter

För den här snabbstarten krävs:

* Operativsystem: Windows (64-bitars), Ubuntu Linux 16.04/18.04 (64-bitars) och macOS 10.13 eller senare
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) eller [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* En Azure-prenumerationsnyckel för taltjänsten. [Skaffa en kostnadsfritt](get-started.md).
* En förkonfigurerad robot som skapats med Bot Framework version 4.2 eller senare. Roboten måste prenumerera på den nya ”Direct Line tal”-kanalen för att ta emot röst indata.

    > [!NOTE]
    > I förhandsversion, tal för Direct Line-kanal stöder för närvarande endast den **westus2** region.

    > [!NOTE]
    > En 30-dagars utvärderingsversion för standardprisnivån som beskrivs i [testa Speech Services kostnadsfritt](get-started.md) är begränsad till **westus** (inte **westus2**) och är därför inte kompatibel med direktanslutning Rad tal. Nivåerna kostnadsfri och standard **westus2** prenumerationer är kompatibla.

Om du kör Ubuntu 16.04/18.04, kontrollera att dessa beroenden är installerade innan du startar Eclipse:

```console
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2 wget
```

Om du kör Windows (64-bitars), kan du kontrollera att du har installerat Microsoft Visual C++ Redistributable för din plattform:
* [Ladda ned Microsoft Visual C++ Redistributable for Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="optional-get-started-fast"></a>Valfritt: Kom igång snabbt

Den här snabbstarten visar steg för steg hur du gör ett enkelt klientprogram att ansluta till din talbaserade robot. Om du vill sätta igång direkt i, fullständiga och redo att kompilera källkoden används i den här snabbstarten finns i den [tal SDK-prov](https://aka.ms/csspeech/samples) under den `quickstart` mapp.

## <a name="create-and-configure-project"></a>Skapa och konfigurera projektet

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

Dessutom för att aktivera loggning, uppdatera den **pom.xml** filen för att inkludera följande beroende.

   ```xml
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-simple</artifactId>
        <version>1.7.5</version>
    </dependency>
   ```

## <a name="add-sample-code"></a>Lägga till exempelkod

1. Du lägger till en ny tom klass i Java-projektet genom att välja **Arkiv** > **Nytt** > **Klass**.

1. I fönstret **New Java Class** (Ny Java-klass) anger du **speechsdk.quickstart** i fältet **Paket** och anger **Main** i fältet **Namn**.

   ![Skärmbild av fönstret New Java Class (Ny Java-klass)](media/sdk/qs-java-jre-06-create-main-java.png)

1. Öppna den nyligen skapade **Main** klassen och Ersätt innehållet i den `Main.java` filen med följande från kod.

    ```java
    package speechsdk.quickstart;

    import java.io.IOException;
    import java.io.PipedOutputStream;
    import java.util.HashMap;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;

    import com.microsoft.cognitiveservices.speech.ResultReason;
    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.dialog.BotConnectorConfig;
    import com.microsoft.cognitiveservices.speech.dialog.SpeechBotConnector;

    public class Main {
        final Logger log = LoggerFactory.getLogger(Main.class);

        public static void main(String[] args) {
            // New code will go here
        }
    }
    ```

1. I den **huvudsakliga** metod, ska du först konfigurera din `BotConnectorConfig` och använda den för att skapa en `SpeechBotConnector` instans. Detta kommer att ansluta till den direktlinje tal kanalen för att interagera med din robot. En `AudioConfig` instans används också för att ange källan för ljudinspelning. I det här exemplet används standard-mikrofon med `AudioConfig.fromDefaultMicrophoneInput()`.

    * Ersätt strängen `YourSubscriptionKey` med din prenumerationsnyckel som du kan hämta från [här](get-started.md).
    * Ersätt strängen `YourServiceRegion` med den [region](regions.md) som är associerade med prenumerationen.
    * Ersätt strängen `YourChannelSecret` med din direktlinje tal kanal hemlighet.

    > [!NOTE]
    > I förhandsversion, tal för Direct Line-kanal stöder för närvarande endast den **westus2** region.

    > [!NOTE]
    > En 30-dagars utvärderingsversion för standardprisnivån som beskrivs i [testa Speech Services kostnadsfritt](get-started.md) är begränsad till **westus** (inte **westus2**) och är därför inte kompatibel med direktanslutning Rad tal. Nivåerna kostnadsfri och standard **westus2** prenumerationer är kompatibla.

    ```java
    final String channelSecret = "YourChannelSecret"; // Your channel secret
    final String subscriptionKey = "YourSubscriptionKey"; // your subscription key
    final String region = "YourServiceRegion"; // Your service region. Currently assumed to be westus2
    final BotConnectorConfig botConnectorConfig = BotConnectorConfig.fromSecretKey(channelSecret, subscriptionKey, region);

    // Configure audio input from microphone.
    final AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();

    // Create a SpeechjBotConnector instance
    final SpeechBotConnector botConnector = new SpeechBotConnector(botConnectorConfig, audioConfig);
    ```

1. `SpeechBotConnector` förlitar sig på flera händelser att kommunicera aktiviteterna bot, tal resultat och annan information. Lägga till dessa event lyssnare härnäst.

    ```java
    // Recognizing will provide the intermediate recognized text while an audio stream is being processed
    botConnector.recognizing.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognizing speech event text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // Recognized will provide the final recognized text once audio capture is completed
    botConnector.recognized.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognized speech event reason text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // SessionStarted will notify when audio begins flowing to the service for a turn
    botConnector.sessionStarted.addEventListener((o, sessionEventArgs) -> {
        log.info("Session Started event id: {} ", sessionEventArgs.getSessionId());
    });

    // SessionStopped will notify when a turn is complete and it's safe to begin listening again
    botConnector.sessionStopped.addEventListener((o, sessionEventArgs) -> {
        log.info("Session stopped event id: {}", sessionEventArgs.getSessionId());
    });

    // Canceled will be signaled when a turn is aborted or experiences an error condition
    botConnector.canceled.addEventListener((o, canceledEventArgs) -> {
        log.info("Canceled event details: {}", canceledEventArgs.getErrorDetails());
        botConnector.disconnectAsync();
    });

    // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities
    botConnector.activityReceived.addEventListener((o, activityEventArgs) -> {
        String act = activityEventArgs.getActivity().serialize();
        log.info("Received activity: {}", act);
    });
    ```

1. Anslut den `SpeechBotConnector` till Direct Line tal genom att aktivera den `connectAsync()` metoden. Om du vill testa din robot, kan du anropa den `listenOnceAsync` metod för att skicka in ljud från mikrofonen. Dessutom kan du kan också använda den `sendActivityAsync` metod för att skicka en anpassad aktivitet som en serialiserad sträng. Dessa anpassade aktiviteter kan ge ytterligare data som din robot ska använda i konversationen.

    ```java
    botConnector.connectAsync();
    // Start listening.
    System.out.println("Say something ...");
    botConnector.listenOnceAsync();

    // botConnector.sendActivityAsync(...)
    ```

1. Vill du spara ändringarna i `Main` fil.

1. För att stödja svaret uppspelning du lägger till en extra klass som innehåller metoder för att stödja ljud. Om du vill aktivera ljud, Lägg till ytterligare en ny tom klass i projektet Java: Välj **filen** > **New** > **klass**.

1. I den **ny Java-klass** fönstret, ange **speechsdk.quickstart** till den **paketet** fält, och **AudioPlayer** till **Namn** fält.

   ![Skärmbild av fönstret New Java Class (Ny Java-klass)](media/sdk/qs-java-jre-06-create-main-java.png)

1. Öppna den nyligen skapade **AudioPlayer** klassen och Ersätt med koden nedan.

    ```java
    import static javax.sound.sampled.AudioFormat.Encoding.PCM_SIGNED;

    import java.io.InputStream;
    import java.io.PipedInputStream;
    import java.io.PipedOutputStream;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    import java.util.concurrent.atomic.AtomicBoolean;

    import javax.sound.sampled.AudioFormat;
    import javax.sound.sampled.AudioSystem;
    import javax.sound.sampled.DataLine;
    import javax.sound.sampled.SourceDataLine;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;


    public class AudioPlayer {

        public static final int SAMPLE_RATE = 16000; // 16Hz sampling rate
        public static final int SAMPLE_SIZE_IN_BITS = 16; // 16 bit PCM
        public static final int CHANNELS = 1; // Use Mono / Single channel

        public static final int FRAME_RATE = 16000;
        public static final int FRAME_SIZE = 2;

        private static final Logger log = LoggerFactory.getLogger(AudioPlayer.class);
        private AtomicBoolean isPlaying = new AtomicBoolean(false);
        private ExecutorService executorService = Executors.newSingleThreadExecutor();

        public boolean isPlaying() {
            return isPlaying.get();
        }

        public void stopPlaying() {
            isPlaying.set(false);
        }

        public void play(final PipedOutputStream pipedOutputStream) {
            // The current audio supported by the Microsoft Bot framework ~ 16-bit PCM encoding, 16KHz sampling rate.
            final AudioFormat defaultFormat = new AudioFormat(PCM_SIGNED, SAMPLE_RATE, SAMPLE_SIZE_IN_BITS, CHANNELS, FRAME_SIZE, FRAME_RATE, false);
            try {
                final PipedInputStream inputStream = new PipedInputStream(pipedOutputStream);

                executorService.submit(() -> {
                    try {
                        isPlaying.set(true);
                        play(inputStream, defaultFormat);
                        inputStream.close();
                    } catch (Exception e) {
                        log.error("Exception thrown during playback. Message: {}", e.getMessage(), e);
                    }
                });
            } catch (Exception e) {
                log.error("Exception thrown during playback. Message: {}", e.getMessage(), e);
            }
        }

        private void play(final InputStream inputStream, final AudioFormat targetFormat) throws Exception {
            final byte[] buffer = new byte[1024];
            final DataLine.Info info = new DataLine.Info(SourceDataLine.class, targetFormat);
            final SourceDataLine line = (SourceDataLine) AudioSystem.getLine(info);
            line.open();
            if (line != null) {
                line.start();
                int bytesRead = 0;
                while (bytesRead != -1) {
                    bytesRead = inputStream.read(buffer, 0, buffer.length);
                    if (bytesRead != -1) {
                        line.write(buffer, 0, bytesRead);
                    }
                }
                line.drain();
                line.stop();
                line.close();
            }
        }
    }
    ```

1. Vill du spara ändringarna i `AudioPlayer` fil.

## <a name="build-and-run-the-app"></a>Skapa och kör appen

Tryck på F11 eller välj **Kör** > **Felsök**.
Konsolen visar ett meddelande ”säger något” nu, kan du talar ett engelska fras eller en mening som dina robotar kan förstå. Ditt tal kommer att överföras till din robot via tal för Direct Line-kanalen där det tolkas, bearbetas av dina robotar och svaret returneras som en aktivitet. Om din robot returnerar tal som svar, ljudet ska spelas tillbaka med hjälp av den `AudioPlayer` klass.

![Skärmbild av konsolutdata efter lyckad taligenkänning](media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>Nästa steg

Ytterligare exempel, till exempel att läsa tal från en ljudfil, finns på GitHub.

> [!div class="nextstepaction"]
> [Utforska Java-exempel på GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Se också

- [Snabbstart: Översätta tal, Java (Windows, Linux)](quickstart-translate-speech-java-jre.md)
- [Anpassa akustiska modeller](how-to-customize-acoustic-models.md)
- [Anpassa språkmodeller](how-to-customize-language-model.md)
