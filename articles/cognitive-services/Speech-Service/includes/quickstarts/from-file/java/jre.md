---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 12/17/2019
ms.author: erhopf
ms.openlocfilehash: 0f8d0c0b1f65cdbb9ae456d134696f6564078080
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75469106"
---
## <a name="prerequisites"></a>Krav

> [!div class="checklist"]
> * [Skapa en Azure tal-resurs](../../../../get-started.md)
> * [Konfigurera utvecklings miljön](../../../../quickstarts/setup-platform.md?tabs=jre)
> * [Skapa ett tomt exempel projekt](../../../../quickstarts/create-project.md?tabs=jre)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="add-sample-code"></a>Lägg till exempelkod

1. Du lägger till en ny tom klass i Java-projektet genom att välja **Arkiv** > **Nytt** > **Klass**.

1. I fönstret **New Java Class** (Ny Java-klass) anger du **speechsdk.quickstart** i fältet **Paket** och anger **Main** i fältet **Namn**.

   ![Skärmbild av fönstret New Java Class (Ny Java-klass)](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-06-create-main-java.png)

1. Ersätt all kod i `Main.java` med följande kodavsnitt:

   ```Java

   package speechsdk.quickstart;

   import java.util.concurrent.Future;
   import com.microsoft.cognitiveservices.speech.*;

   /**
    * Quickstart: recognize speech using the Speech SDK for Java.
    */
   public class Main {

       /**
        * @param args Arguments are ignored in this sample.
        */
       public static void main(String[] args) {
           try {
               // Replace below with your own subscription key
               String speechSubscriptionKey = "YourSubscriptionKey";
               // Replace below with your own service region (e.g., "westus").
               String serviceRegion = "YourServiceRegion";
               // Replace below with your own filename.
               String audioFileName = "whatstheweatherlike.wav";

               int exitCode = 1;
               SpeechConfig config = SpeechConfig.fromSubscription(speechSubscriptionKey, serviceRegion);
               assert(config != null);

               AudioConfig audioInput = AudioConfig.fromWavFileInput(audioFileName);
               assert(audioInput != null);

               SpeechRecognizer reco = new SpeechRecognizer(config, audioInput);
               assert(reco != null);

               System.out.println("Recognizing first result...");

               Future<SpeechRecognitionResult> task = reco.recognizeOnceAsync();
               assert(task != null);

               SpeechRecognitionResult result = task.get();
               assert(result != null);

               if (result.getReason() == ResultReason.RecognizedSpeech) {
                   System.out.println("We recognized: " + result.getText());
                   exitCode = 0;
               }
               else if (result.getReason() == ResultReason.NoMatch) {
                   System.out.println("NOMATCH: Speech could not be recognized.");
               }
               else if (result.getReason() == ResultReason.Canceled) {
                   CancellationDetails cancellation = CancellationDetails.fromResult(result);
                   System.out.println("CANCELED: Reason=" + cancellation.getReason());

                   if (cancellation.getReason() == CancellationReason.Error) {
                       System.out.println("CANCELED: ErrorCode=" + cancellation.getErrorCode());
                       System.out.println("CANCELED: ErrorDetails=" + cancellation.getErrorDetails());
                       System.out.println("CANCELED: Did you update the subscription info?");
                   }
               }

               reco.close();

               System.exit(exitCode);
           } catch (Exception ex) {
               System.out.println("Unexpected exception: " + ex.getMessage());

               assert(false);
               System.exit(1);
           }
       }
   }
   ```

1. Ersätt strängen `YourSubscriptionKey` med din prenumerationsnyckel.

1. Ersätt strängen `YourServiceRegion` med den [region](~/articles/cognitive-services/Speech-Service/regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

1. Ersätt strängen `whatstheweatherlike.wav` med ditt eget fil namn.

1. Spara ändringarna i projektet.

> [!NOTE]
> Tal-SDK: n kommer att känna igen med en-US för språket, se [Ange käll språk för tal till text](../../../../how-to-specify-source-language.md) om du vill ha information om hur du väljer käll språk.

## <a name="build-and-run-the-app"></a>Skapa och kör appen

Tryck på F11 eller välj **Kör** > **Felsök**.
De första 15 sekundernas tal indata från ljud filen kommer att identifieras och loggas i konsol fönstret.

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]