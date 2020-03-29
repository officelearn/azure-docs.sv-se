---
title: Asynkron konversationsavskription (förhandsgranskning) - Taltjänst
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder asynkron konversationsavskrift med hjälp av taltjänsten. Endast tillgängligt för Java.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.openlocfilehash: 57543f4a3779145ce66259eec1abac195b63c7ba
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384304"
---
# <a name="asynchronous-conversation-transcription-preview"></a>Asynkron konversationsavskrift (förhandsgranskning)

I den här artikeln visas asynkron konversationsavskription med hjälp av **RemoteConversationTranscriptionClient** API. Om du har konfigurerat konversationsavskrifter för att göra `conversationId`asynkron transkription `conversationId` och har en kan du hämta transkriptionen som är associerad med den med hjälp av **RemoteConversationTranscriptionClient** API.

## <a name="asynchronous-vs-real-time--asynchronous"></a>Asynkron kontra realtid + asynkron

Med asynkron transkription streamar du konversationsljudet, men behöver inte en transkription som returneras i realtid. När ljudet har skickats använder `conversationId` `Conversation` du i stället frågan om statusen för den asynkrona transkriptionen. När den asynkrona transkriptionen är klar `RemoteConversationTranscriptionResult`får du en .

Med realtid plus asynkron får du transkriptionen i realtid, men får också `conversationId` transkriptionen genom att fråga med (liknande asynkrona scenario).

Två steg krävs för att utföra asynkron transkription. Det första steget är att ladda upp ljudet, välja antingen asynkrona bara eller realtid plus asynkrona. Det andra steget är att få transkriptionsresultat.

## <a name="upload-the-audio"></a>Ladda upp ljudet

Innan asynkron transkription kan utföras måste du skicka ljudet till konversationsavskriftstjänsten med Microsoft Cognitive Speech-klienten SDK (version 1.8.0 eller senare).

Den här exempelkoden visar hur du skapar konversations transkriberare för asynkront läge. För att strömma ljud till transkriberaren måste du lägga till ljudströmningskod som härletts från [Transkribera konversationer i realtid med Tal SDK](how-to-use-conversation-transcription-service.md). Se avsnittet **Begränsningar** i det avsnittet om att se de plattformar och språk som stöds.

```java
// Create the speech config object
// Substitute real information for "YourSubscriptionKey" and "Region"
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");
speechConfig.setProperty("ConversationTranscriptionInRoomAndOnline", "true");

// Set the property for asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);

// pick a conversation Id that is a GUID.
String conversationId = UUID.randomUUID().toString();

// Create a Conversation
Future<Conversation> conversationFuture = Conversation.createConversationAsync(speechConfig, conversationId);
Conversation conversation = conversationFuture.get();

// Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
// Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
PullAudioInputStreamCallback wavfilePullStreamCallback = Helper.OpenWavFile("16kHz16Bits8channelsOfRecordedPCMAudio.wav");
// Create an audio stream format assuming the file used above is 16kHz, 16 bits and 8 channel pcm wav file
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

Om du vill _ha_ realtid plus asynkron, kommentera och avkommentera lämpliga kodrader enligt följande:

```java
// Set the property for asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);
```

## <a name="get-transcription-results"></a>Få transkriptionsresultat

Det här steget får asynkrona transkriptionsresultat men förutsätter att all bearbetning i realtid som du kan ha krävt görs någon annanstans. Mer information finns [i Transkribera konversationer i realtid med Tal SDK](how-to-use-conversation-transcription-service.md).

För koden som visas här behöver du **fjärrkonversation version 1.8.0**, stöds endast för Java (1.8.0 eller högre) på Windows, Linux och Android (ENDAST API-nivå 26 eller högre).

### <a name="obtaining-the-client-sdk"></a>Skaffa klienten SDK

Du kan hämta **fjärrkonversation** genom att redigera filen pom.xml på följande sätt.

1. I slutet av filen, före `</project>`den avslutande `repositories` taggen, skapar du ett element med en referens till Maven-databasen för Tal-SDK:

   ```xml
   <repositories>
     <repository>
       <id>maven-cognitiveservices-speech</id>
       <name>Microsoft Cognitive Services Speech Maven Repository</name>
       <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
     </repository>
   </repositories>
   ```

2. Lägg också `dependencies` till ett element med remoteconversation-client-sdk 1.8.0 som ett beroende:

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

### <a name="sample-transcription-code"></a>Exempel på transkriptionskod

När du `conversationId`har , skapa en fjärrkonversation transkription klient **RemoteConversationTranscriptionClient** på klientprogrammet för att fråga status för asynkron transkription. Använd **metoden getTranscriptionOperation** i **RemoteConversationTranscriptionClient** för att hämta ett [PollerFlux-objekt.](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/PollerFlux.java) PollerFlux-objektet kommer att ha information om fjärråtgärdsstatusen **RemoteConversationTranscriptionOperation** och slutresultatet **RemoteConversationTranscriptionResult**. När åtgärden är klar får du **RemoteConversationTranscriptionResult** genom att anropa **getFinalResult** på en [SyncPoller](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/SyncPoller.java). I denna kod skriver vi helt enkelt ut resultatinnehållet till systemutdata.

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
