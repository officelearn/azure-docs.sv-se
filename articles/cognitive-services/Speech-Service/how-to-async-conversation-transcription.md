---
title: Avskrift av asynkron konversation (för hands version) – tal tjänst
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder asynkron konversations avskrift med hjälp av tal tjänsten. Endast tillgängligt för Java.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.openlocfilehash: 9a2a00a8c76c7b5c02cde623bf93f536b27cf074
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73621959"
---
# <a name="asynchronous-conversation-transcription-preview"></a>Avskrift av asynkron konversation (förhands granskning)

I den här artikeln visas den asynkrona konversations avskriften med hjälp av **RemoteConversationTranscriptionClient** -API: et. Om du har konfigurerat konversations avskrift för asynkron avskriftning och har en `conversationId`, kan du hämta avskriften som är associerad med `conversationId` med **RemoteConversationTranscriptionClient** -API: et.

## <a name="asynchronous-vs-real-time--asynchronous"></a>Asynkron vs. real tids + asynkron

Med asynkron avskrift strömmar du konversations ljudet, men behöver ingen avskrift i real tid. När ljudet har skickats använder du i stället `conversationId` av `Conversation` för att fråga efter statusen för den asynkrona avskriften. När den asynkrona avskriften är klar får du en `RemoteConversationTranscriptionResult`.

Med real tids plus asynkront får du avskrifter i real tid, men du kan också få avskriften genom att fråga med `conversationId` (liknar asynkront scenario).

Två steg krävs för att utföra asynkron avskriftering. Det första steget är att ladda upp ljudet, välja antingen asynkront eller i real tid och asynkront. Det andra steget är att hämta resultatet av avskriften.

## <a name="upload-the-audio"></a>Ladda upp ljudet

Innan asynkron avskrift kan utföras måste du skicka ljudet till konversations avskrifts tjänsten med hjälp av Microsofts kognitiva tal klient-SDK (version 1.8.0 eller senare).

I den här exempel koden visas hur du skapar en konversations beskrivare för endast asynkront läge. För att kunna strömma ljud till-skrivarna måste du lägga till kod för direkt uppspelning av ljud som härletts från att skriva om [samtal i real tid med talet SDK](how-to-use-conversation-transcription-service.md). I avsnittet **begränsningar** i det här avsnittet visas de plattformar och språk-API: er som stöds.

```java
// Create the speech config object
// Substitute real information for "YourSubscriptionKey" and "Region"
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");
speechConfig.setProperty("ConversationTranscriptionInRoomAndOnline", "true");

// Set the property for asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);

// pick a conversation Id that is a GUID.
String conversationId = UUID.randomUUID().toString();

// Create a Conversation
Conversation conversation = new Conversation(speechConfig, conversationId);

// Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
// Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
PullAudioInputStreamCallback wavfilePullStreamCallback = Helper.OpenWavFile("16Khz16Bits8channelsOfRecordedPCMAudio.wav");
// Create an audio stream format assuming the file used above is 16Khz, 16 bits and 8 channel pcm wav file
AudioStreamFormat audioStreamFormat = AudioStreamFormat.getWaveFormatPCM((long)16000, (short)16,(short)8);
// Create an input stream
AudioInputStream audioStream = AudioInputStream.createPullStream(wavfilePullStreamCallback, audioStreamFormat);

// Create a conversation transcriber
ConversationTranscriber transcriber = new ConversationTranscriber(AudioConfig.fromStreamInput(audioStream));

// join a conversation
transcriber.joinConversationAsync(conversation);

// Add the event listener for the realtime events
transcriber.transcribed.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber Recognized:" + e.toString());
});

transcriber.canceled.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber canceled:" + e.toString());
    try {
        transcriber.stopTranscribingAsync().get();
    } catch (InterruptedException ex) {
        ex.printStackTrace();
    } catch (ExecutionException ex) {
        ex.printStackTrace();
    }
});

transcriber.sessionStopped.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber stopped:" + e.toString());

    try {
        transcriber.stopTranscribingAsync().get();
    } catch (InterruptedException ex) {
        ex.printStackTrace();
    } catch (ExecutionException ex) {
        ex.printStackTrace();
    }
});

// start the transcription.
Future<?> future = transcriber.startTranscribingAsync();
...
```

Om du vill ha ett real tids _plus_ asynkront, kommentera och ta bort kommentarer till rätt rader med kod enligt följande:

```java
// Set the property for asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);
```

## <a name="get-transcription-results"></a>Hämta avskrifts resultat

Det här steget hämtar resultatet av den asynkrona avskriften men förutsätter att all bearbetning i real tid som du kan ha krävt görs någon annan stans. Mer information finns i skriva [upp konversationer i real tid med talet SDK](how-to-use-conversation-transcription-service.md).

För den kod som visas här behöver du **1.8.0 för fjärrkonversationer**, som endast stöds för Java (1.8.0 eller senare) i Windows, Linux och Android (endast API-nivå 26 eller senare).

### <a name="obtaining-the-client-sdk"></a>Hämta klient-SDK

Du kan hämta en **fjärran** sluten konversation genom att redigera Pom. XML-filen på följande sätt.

1. I slutet av filen, innan den avslutande taggen `</project>`, skapar du ett `repositories`-element med en referens till maven-lagringsplatsen för tal-SDK:

   ```xml
   <repositories>
     <repository>
       <id>maven-cognitiveservices-speech</id>
       <name>Microsoft Cognitive Services Speech Maven Repository</name>
       <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
     </repository>
   </repositories>
   ```

2. Lägg också till ett `dependencies`-element med remoteconversation-client-SDK-1.8.0 som ett beroende:

   ```xml
   <dependencies>
     <dependency>
       <groupId>com.microsoft.cognitiveservices.speech.remoteconversation</groupId>
       <artifactId>remote-conversation</artifactId>
       <version>1.8.0</version>
     </dependency>
   </dependencies>
   ```

3. Spara ändringarna

### <a name="sample-transcription-code"></a>Exempel kod för avskrift

När du har `conversationId`skapar du en **RemoteConversationTranscriptionClient** för avskrifts avskrift i klient programmet för att fråga statusen för den asynkrona avskriften. Använd **getTranscriptionOperation** -metoden i **RemoteConversationTranscriptionClient** för att hämta ett [PollerFlux](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/PollerFlux.java) -objekt. PollerFlux-objektet kommer att ha information om fjärråtgärdens status **RemoteConversationTranscriptionOperation** och det slutliga resultatet **RemoteConversationTranscriptionResult**. När åtgärden har slutförts hämtar du **RemoteConversationTranscriptionResult** genom att anropa **GetFinalResult** på en [SyncPoller](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/SyncPoller.java). I den här koden skriver vi helt enkelt ut resultat innehållet till systemets utdata.

```java
// Create the speech config object
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");

// Create a remote Conversation Transcription client
RemoteConversationTranscriptionClient client = new RemoteConversationTranscriptionClient(speechConfig);

// Get the PollerFlux for the remote operation
PollerFlux<RemoteConversationTranscriptionOperation, RemoteConversationTranscriptionResult> remoteTranscriptionOperation = client.getTranscriptionOperation(conversationId);

// Subscribe to PollerFlux to get the remote operation status
remoteTranscriptionOperation.subscribe(
        pollResponse -> {
            System.out.println("Poll response status : " + pollResponse.getStatus());
            System.out.println("Poll response status : " + pollResponse.getValue().getServiceStatus());
        }
);

// Obtain the blocking operation using getSyncPoller
SyncPoller<RemoteConversationTranscriptionOperation, RemoteConversationTranscriptionResult> blockingOperation =  remoteTranscriptionOperation.getSyncPoller();

// Wait for the operation to finish
blockingOperation.waitForCompletion();

// Get the final result response
RemoteConversationTranscriptionResult resultResponse = blockingOperation.getFinalResult();

// Print the result
if(resultResponse != null) {
    if(resultResponse.getConversationTranscriptionResults() != null) {
        for (int i = 0; i < resultResponse.getConversationTranscriptionResults().size(); i++) {
            ConversationTranscriptionResult result = resultResponse.getConversationTranscriptionResults().get(i);
            System.out.println(result.getProperties().getProperty(PropertyId.SpeechServiceResponse_JsonResult.name()));
            System.out.println(result.getProperties().getProperty(PropertyId.SpeechServiceResponse_JsonResult));
            System.out.println(result.getOffset());
            System.out.println(result.getDuration());
            System.out.println(result.getUserId());
            System.out.println(result.getReason());
            System.out.println(result.getResultId());
            System.out.println(result.getText());
            System.out.println(result.toString());
        }
    }
}

System.out.println("Operation finished");
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska våra exempel på GitHub](https://aka.ms/csspeech/samples)
