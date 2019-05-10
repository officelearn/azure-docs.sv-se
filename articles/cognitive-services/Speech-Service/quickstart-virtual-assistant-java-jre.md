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
ms.openlocfilehash: 83149a8422db25106a97b1711c0ae9ce3c6603eb
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65465668"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-with-the-speech-sdk-java"></a>Snabbstart: Skapa en röst-första virtuella assistenter med Speech-SDK för Java

Snabbstarter kan också användas för [tal till text](quickstart-java-jre.md) och [talöversättning](quickstart-translate-speech-java-jre.md).

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

    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;
    import com.microsoft.cognitiveservices.speech.dialog.BotConnectorConfig;
    import com.microsoft.cognitiveservices.speech.dialog.SpeechBotConnector;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;

    import javax.sound.sampled.AudioFormat;
    import javax.sound.sampled.AudioSystem;
    import javax.sound.sampled.DataLine;
    import javax.sound.sampled.SourceDataLine;
    import java.io.InputStream;

    public class Main {
        final Logger log = LoggerFactory.getLogger(Main.class);

        public static void main(String[] args) {
            // New code will go here
        }

        private void playAudioStream(PullAudioOutputStream audio) {
            ActivityAudioStream stream = new ActivityAudioStream(audio);
            final ActivityAudioStream.ActivityAudioFormat audioFormat = stream.getActivityAudioFormat();
            final AudioFormat format = new AudioFormat(
                    AudioFormat.Encoding.PCM_SIGNED,
                    audioFormat.getSamplesPerSecond(),
                    audioFormat.getBitsPerSample(),
                    audioFormat.getChannels(),
                    audioFormat.getFrameSize(),
                    audioFormat.getSamplesPerSecond(),
                    false);
            try {
                int bufferSize = format.getFrameSize();
                final byte[] data = new byte[bufferSize];

                SourceDataLine.Info info = new DataLine.Info(SourceDataLine.class, format);
                SourceDataLine line = (SourceDataLine) AudioSystem.getLine(info);
                line.open(format);

                if (line != null) {
                    line.start();
                    int nBytesRead = 0;
                    while (nBytesRead != -1) {
                        nBytesRead = stream.read(data);
                        if (nBytesRead != -1) {
                            line.write(data, 0, nBytesRead);
                        }
                    }
                    line.drain();
                    line.stop();
                    line.close();
                }
                stream.close();

            } catch (Exception e) {
                e.printStackTrace();
            }
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

    // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities.
    botConnector.activityReceived.addEventListener((o, activityEventArgs) -> {
        final String act = activityEventArgs.getActivity().serialize();
            log.info("Received activity {} audio", activityEventArgs.hasAudio() ? "with" : "without");
            if (activityEventArgs.hasAudio()) {
                playAudioStream(activityEventArgs.getAudio());
            }
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

1. För att stödja svaret uppspelning du lägger till en extra klass som transformerar PullAudioOutputStream-objektet som returnerades från getAudio() API till en java InputStream för att underlätta hanteringen. Den här ActivityAudioStream är en särskild klass som hanterar ljud svar från ”tal för Direct Line-kanal”. Den tillhandahåller behöriga personer för att hämta ljudformatet information som krävs för att hantera uppspelning: För att välja **filen** > **New** > **klass**.

1. I den **ny Java-klass** fönstret, ange **speechsdk.quickstart** till den **paketet** fält, och **ActivityAudioStream** till  **Namn på** fält.

1. Öppna den nyligen skapade **ActivityAudioStream** klassen och Ersätt med koden nedan.

    ```java
    package com.speechsdk.quickstart;

    import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;

    import java.io.IOException;
    import java.io.InputStream;


    public final class ActivityAudioStream extends InputStream {
        /**
         * The number of samples played per second. (16 kHz)
         */
        public static final long SAMPLE_RATE = 16000;
        /**
         * The number of bits in each sample of a sound that has this format. (16 bits)
         */
        public static final int BITS_PER_SECOND = 16;
        /**
         * The number of audio channels in this format (1 for mono).
         */
        public static final int CHANNELS = 1;
        /**
         * The number of bytes in each frame of a sound that has this format (2).
         */
        public static final int FRAME_SIZE = 2;

        /**
         * Reads up to a specified maximum number of bytes of data from the audio
         * stream, putting them into the given byte array.
         *
         * @param b   the buffer into which the data is read
         * @param off the offset, from the beginning of array <code>b</code>, at which
         *            the data will be written
         * @param len the maximum number of bytes to read
         * @return the total number of bytes read into the buffer, or -1 if there
         * is no more data because the end of the stream has been reached
         */
        @Override
        public int read(byte[] b, int off, int len) {
            byte[] tempBuffer = new byte[len];
            int n = (int) this.pullStreamImpl.read(tempBuffer);
            for (int i = 0; i < n; i++) {
                if (off + i > b.length) {
                    throw new ArrayIndexOutOfBoundsException(b.length);
                }
                b[off + i] = tempBuffer[i];
            }
            if (n == 0) {
                return -1;
            }
            return n;
        }

        /**
         * Reads the next byte of data from the activity audio stream if available.
         *
         * @return the next byte of data, or -1 if the end of the stream is reached
         * @see #read(byte[], int, int)
         * @see #read(byte[])
         * @see #available
         * <p>
         */
        @Override
        public int read() {
            byte[] data = new byte[1];
            int temp = read(data);
            if (temp <= 0) {
                // we have a weird situation if read(byte[]) returns 0!
                return -1;
            }
            return data[0] & 0xFF;
        }

        /**
         * Reads up to a specified maximum number of bytes of data from the activity audio stream
         * putting them into the given byte array.
         *
         * @param b the buffer into which the data is read
         * @return the total number of bytes read into the buffer, or -1 if there
         * is no more data because the end of the stream has been reached
         */
        @Override
        public int read(byte[] b) {
            int n = (int) pullStreamImpl.read(b);
            if (n == 0) {
                return -1;
            }
            return n;
        }

        /**
         * Skips over and discards a specified number of bytes from this
         * audio input stream.
         *
         * @param n the requested number of bytes to be skipped
         * @return the actual number of bytes skipped
         * @throws IOException if an input or output error occurs
         * @see #read
         * @see #available
         */
        @Override
        public long skip(long n) {
            if (n <= 0) {
                return 0;
            }
            if (n <= Integer.MAX_VALUE) {
                byte[] tempBuffer = new byte[(int) n];
                return read(tempBuffer);
            }
            long count = 0;
            for (long i = n; i > 0; i -= Integer.MAX_VALUE) {
                int size = (int) Math.min(Integer.MAX_VALUE, i);
                byte[] tempBuffer = new byte[size];
                count += read(tempBuffer);
            }
            return count;
        }

        /**
         * Closes this audio input stream and releases any system resources associated
         * with the stream.
         */
        @Override
        public void close() {
            this.pullStreamImpl.close();
        }

        /**
         * Fetch the audio format for the ActivityAudioStream. The ActivityAudioFormat defines the sample rate, bits per sample and the # channels
         *
         * @return instance of the ActivityAudioFormat associated with the stream
         */
        public ActivityAudioStream.ActivityAudioFormat getActivityAudioFormat() {
            return activityAudioFormat;
        }

        /**
         * Returns the maximum number of bytes that can be read (or skipped over) from this
         * audio input stream without blocking.
         *
         * @return the number of bytes that can be read from this audio input stream without blocking.
         * As this implementation does not buffer this will be defaulted to 0
         */
        @Override
        public int available() {
            return 0;
        }

        public ActivityAudioStream(final PullAudioOutputStream stream) {
            pullStreamImpl = stream;
            this.activityAudioFormat = new ActivityAudioStream.ActivityAudioFormat(SAMPLE_RATE, BITS_PER_SECOND, CHANNELS, FRAME_SIZE, AudioEncoding.PCM_SIGNED);
        }

        private PullAudioOutputStream pullStreamImpl;

        private ActivityAudioFormat activityAudioFormat;

        /**
         * ActivityAudioFormat is an internal format which contains metadata regarding the type of arrangement of
         * audio bits in this activity audio stream.
         */
        static class ActivityAudioFormat {

            private long samplesPerSecond;
            private int bitsPerSample;
            private int channels;
            private int frameSize;
            private AudioEncoding encoding;

            public ActivityAudioFormat(long samplesPerSecond, int bitsPerSample, int channels, int frameSize, AudioEncoding encoding) {
                this.samplesPerSecond = samplesPerSecond;
                this.bitsPerSample = bitsPerSample;
                this.channels = channels;
                this.encoding = encoding;
                this.frameSize = frameSize;
            }

            /**
             * Fetch the number of samples played per second for the associated audio stream format.
             *
             * @return the number of samples played per second
             */
            public long getSamplesPerSecond() {
                return samplesPerSecond;
            }

            /**
             * Fetch the number of bits in each sample of a sound that has this audio stream format.
             *
             * @return the number of bits per sample
             */
            public int getBitsPerSample() {
                return bitsPerSample;
            }

            /**
             * Fetch the number of audio channels used by this audio stream format.
             *
             * @return the number of channels
             */
            public int getChannels() {
                return channels;
            }

            /**
             * Fetch the default number of bytes in a frame required by this audio stream format.
             *
             * @return the number of bytes
             */
            public int getFrameSize() {
                return frameSize;
            }

            /**
             * Fetch the audio encoding type associated with this audio stream format.
             *
             * @return the encoding associated
             */
            public AudioEncoding getEncoding() {
                return encoding;
            }
        }

        /**
         * Enum defining the types of audio encoding supported by this stream
         */
        public enum AudioEncoding {
            PCM_SIGNED("PCM_SIGNED");

            String value;

            AudioEncoding(String value) {
                this.value = value;
            }
        }
    }

    ```

1. Vill du spara ändringarna i `ActivityAudioStream` fil.

## <a name="build-and-run-the-app"></a>Skapa och kör appen

Tryck på F11 eller välj **Kör** > **Felsök**.
Konsolen visar ett meddelande ”säger något” nu, kan du talar ett engelska fras eller en mening som dina robotar kan förstå. Ditt tal kommer att överföras till din robot via tal för Direct Line-kanalen där det tolkas, bearbetas av dina robotar och svaret returneras som en aktivitet. Om din robot returnerar tal som svar, ljudet ska spelas tillbaka med hjälp av den `AudioPlayer` klass.

![Skärmbild av konsolutdata efter lyckad taligenkänning](media/sdk/qs-java-jre-08-console-output.png)

## <a name="next-steps"></a>Nästa steg

Ytterligare exempel, till exempel att läsa tal från en ljudfil, finns på GitHub.

> [!div class="nextstepaction"]
> [Utforska Java-exempel på GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Se också

- [Snabbstart: Översätta tal, Java (Windows, Linux)](quickstart-translate-speech-java-jre.md)
- [Anpassa akustiska modeller](how-to-customize-acoustic-models.md)
- [Anpassa språkmodeller](how-to-customize-language-model.md)
