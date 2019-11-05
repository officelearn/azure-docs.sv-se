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
ms.openlocfilehash: 433ea2778d99de39991565b2529e2f8eab4e13df
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506982"
---
# <a name="asynchronous-conversation-transcription-preview"></a>Avskrift av asynkron konversation (förhands granskning)

I den här artikeln visas den asynkrona konversations avskriften med hjälp av **RemoteConversationTranscriptionClient** -API: et. Om du har konfigurerat konversations avskrift för asynkron avskriftning och har en `conversationId`, kan du hämta avskriften som är associerad med `conversationId` med **RemoteConversationTranscriptionClient** -API: et.

## <a name="asynchronous-vs-real-time--asynchronous"></a>Asynkron vs. real tids + asynkron

Med asynkron avskrift strömmar du konversations ljudet, men behöver ingen avskrift i real tid. När ljudet har skickats använder du i stället `conversationId` av `ConversationTranscriber` för att fråga efter statusen för den asynkrona avskriften. När den asynkrona avskriften är klar får du en `RemoteConversationTranscriptionResult`.

Med real tids plus asynkront får du avskrifter i real tid, men du kan också få avskriften genom att fråga med `conversationId` (liknar asynkront scenario).

Två steg krävs för att utföra asynkron avskriftering. Det första steget är att ladda upp ljudet, välja antingen asynkront eller i real tid och asynkront. Det andra steget är att hämta resultatet av avskriften.

## <a name="upload-the-audio"></a>Ladda upp ljudet

Innan asynkron avskrift kan utföras måste du skicka ljudet till konversations avskriften med hjälp av Microsofts kognitiva tal klient-SDK (version 1.8.0 eller senare).

I den här exempel koden visas hur du skapar en konversations beskrivare för endast asynkront läge. För att kunna strömma ljud till-skrivarna måste du lägga till kod för direkt uppspelning av ljud som härletts från att skriva om [samtal i real tid med talet SDK](how-to-use-conversation-transcription-service.md). I avsnittet **begränsningar** i det här avsnittet visas de plattformar och språk-API: er som stöds.

```java
// Create the speech config object
// Substitute real information for "YourSubscriptionKey" and "Region"
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");
speech_config.setProperty("ConversationTranscriptionInRoomAndOnline", "true");

// Set the property for asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);

// Do rest of the things as explained in how to use Conversation Transcription

// pick a conversation Id that is a GUID.
Conversation conversation = new Conversation(speechConfig, conversationId);

// Create a conversation transcriber
ConversationTranscriber transcriber = new ConversationTranscriber(AudioConfig.fromDefaultMicrophoneInput());

// join a conversation
transcriber.joinConversationAsync(conversation).get();

// stream audio as shown in “Transcribe conversations in real time”
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

För den kod som visas här behöver du **remoteconversation-client-SDK version 1.0.0**, som endast stöds för Java (1,8 eller senare) i Windows, Linux och Android (API-nivå 26 eller senare).

### <a name="obtaining-the-client-sdk"></a>Hämta klient-SDK

Du kan hämta **remoteconversation-client-SDK** genom att redigera din Pom. XML-fil på följande sätt.

- I slutet av filen, innan den avslutande taggen `</project>`, skapar du ett `repositories`-element med en referens till maven-lagringsplatsen för tal-SDK:

  ```xml
  <repositories>
    <repository>
      <id>maven-cognitiveservices-speech</id>
      <name>Microsoft Cognitive Services Speech Maven Repository</name>
      <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
    </repository>
  </repositories>
  ```

- Lägg också till ett `dependencies`-element med remoteconversation-client-SDK-1.0.0 som ett beroende:

  ```xml
  <dependencies>
    <dependency>
      <groupId>com.microsoft.cognitiveservices.speech.remoteconversation</groupId>
      <artifactId>remoteconversation-client-sdk</artifactId>
      <version>1.0.0</version>
    </dependency>
  </dependencies>
  ```

- Spara ändringarna

### <a name="sample-transcription-code"></a>Exempel kod för avskrift

När du har `conversationId`skapar du en fjärran sluten åtgärds objekt **RemoteConversationTranscriptionOperation** på klienten för att fråga efter status för den asynkrona avskriften. **RemoteConversationTranscriptionOperation** har utökats från [avsökningen](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/Poller.java). När avsökningen har avslut ATS får du **RemoteConversationTranscriptionResult** genom att prenumerera på avsökningen och skicka en fråga till objektet. I den här koden skriver vi helt enkelt ut resultat innehållet till systemets utdata.

```java
// Create the speech config object
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");

// Create a remote Conversation Transcription client
RemoteConversationTranscriptionClient client = new RemoteConversationTranscriptionClient(speechConfig);

// Create a remote Conversation Transcription operation
RemoteConversationTranscriptionOperation operation = new RemoteConversationTranscriptionOperation(conversationId, client);

// Operation identifier now be the value of `conversationId`
System.out.println("Operation Identifier:" + operation.getId());

// Get the observer (which is a Flux) and subscribe to it for the response
operation.getObserver()
                .subscribe(
                        pollResponse -> {
                            System.out.println("Poll response status : " + pollResponse.getStatus());
                            if(pollResponse.getValue().getConversationTranscriptionResults() != null) {
                                for (int i = 0; i < pollResponse.getValue().getConversationTranscriptionResults().size(); i++) {
                                    ConversationTranscriptionResult result = pollResponse.getValue().getConversationTranscriptionResults().get(i);
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
                );
// Block on the operation till it is finished
operation.block();

System.out.println("Operation finished");
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska våra exempel på GitHub](https://aka.ms/csspeech/samples)
