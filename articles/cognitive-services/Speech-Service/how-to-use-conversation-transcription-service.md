---
title: Konversations avskrift i real tid (för hands version) – tal tjänst
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder konversations avskrift i real tid med talet SDK. Tillgängligt för C++, C#och Java.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: weixu
ms.openlocfilehash: 4ee597f96d28b43b9c69d1515634aadde2f0ab7c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464447"
---
# <a name="real-time-conversation-transcription-preview"></a>Konversations avskrift i real tid (för hands version)

Talet SDK: s **ConversationTranscriber** -API gör att du kan skriva över möten och andra konversationer med möjlighet att lägga till, ta bort och identifiera flera deltagare genom att strömma ljud till tal tjänster med hjälp av `PullStream` eller `PushStream`. I det här avsnittet måste du veta hur du använder tal-till-text med talet SDK (version 1.8.0 eller senare). Mer information finns i [Vad är tal tjänster](overview.md).

## <a name="limitations"></a>Begränsningar

- ConversationTranscriber-API: et stöds C++för C#, och Java på Windows, Linux och Android.
- För närvarande tillgängligt i språken "en-US" och "zh-CN" i följande regioner: _Central_ -och _asienöstra_.
- Kräver en 7-MIC cirkulär multi-Microphone-matris med en uppspelnings referens ström. Mikrofonens matris ska uppfylla [vår specifikation](https://aka.ms/sdsdk-microphone).
- [Tal enheternas SDK](speech-devices-sdk.md) tillhandahåller lämpliga enheter och en exempel-app som demonstrerar konversations avskrift.

## <a name="optional-sample-code-resources"></a>Valfria exempel kod resurser

Tal enhets-SDK: n innehåller exempel kod i Java för ljud fångst i real tid med 8 kanaler.

- [Exempel kod för ROOBO-enhet](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/Conversation.java)
- [Exempel kod för Azure Kinect dev kit](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)

## <a name="prerequisites"></a>Förutsättningar

En prenumeration på tal tjänster. Du kan [få en utvärderings prenumeration för tal](https://azure.microsoft.com/try/cognitive-services/) om du inte har någon.

## <a name="create-voice-signatures"></a>Skapa röst under skrifter

Det första steget är att skapa röst signaturer för konversations deltagarna för effektiv högtalar identifiering.

### <a name="audio-input-requirements"></a>Krav för ljud inspelning

- Indata-ljudwave-filen för att skapa röst under skrifter ska vara i 16-bitars exempel, 16 kHz samplings frekvens och ett enda kanal format (mono).
- Den rekommenderade längden för varje ljud sampling är mellan trettio sekunder och två minuter.

### <a name="sample-code"></a>Exempelkod

I följande exempel visas två olika sätt att skapa röst signatur med [hjälp av REST API](https://aka.ms/cts/signaturegenservice) i C#. Observera att du måste ersätta verklig information för "YourSubscriptionKey", Wave-filnamnet för "speakerVoice. wav" och din region för `{region}` och "YourServiceRegion" (_centralt_ eller _asienöstra_).

```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static void Main(string[] args)
    {
        CreateVoiceSignatureByUsingFormData().Wait();
        CreateVoiceSignatureByUsingBody().Wait();
    }
}
```

## <a name="transcribe-conversations"></a>Skriva över konversationer

Följande exempel kod visar hur du kan skriva om konversationer i real tid för tre högtalare. Det förutsätter att du redan har skapat röst signaturer för varje talare som visas ovan. Ersätt verklig information för "YourSubscriptionKey" och "YourServiceRegion" när du skapar SpeechConfig-objektet.

Exempel kod markeringar är:

- Skapa ett `Conversation`-objekt från `SpeechConfig`-objektet med hjälp av ett mötes-ID som genereras med hjälp av `Guid.NewGuid()`
- Skapa ett `ConversationTranscriber`-objekt och delta i konversationen med `JoinConversationAsync()` för att starta avskriften
- Registrera händelser av intresse
- Lägga till eller ta bort deltagare i konversationen med hjälp av objektet konversation
- Strömma ljudet

Avskrifts-och högtalar identifieraren kommer tillbaka i de registrerade händelserna.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Transcription;

public class MyConversationTranscriber
{
    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region
        // Replace with your own subscription key and region
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
        config.SetProperty("ConversationTranscriptionInRoomAndOnline", "true");
        var stopTranscription = new TaskCompletionSource<int>();

        // Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
        // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
        using (var audioInput = Helper.OpenWavFile(@"8channelsOfRecordedPCMAudio.wav"))
        {
            var meetingId = Guid.NewGuid().ToString();
            using (var conversation = new Conversation(config, meetingId))
            {
                // Create a conversation transcriber using audio stream input
                using (var conversationTranscriber = new ConversationTranscriber    (audioInput))
                {
                    await conversationTranscriber.JoinConversationAsync(conversation);

                    // Subscribe to events
                    conversationTranscriber.Transcribing += (s, e) =>
                    {
                            Console.WriteLine($"TRANSCRIBING: Text={e.Result.Text}");
                    };

                    conversationTranscriber.Transcribed += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"TRANSCRIBED: Text={e.Result.Text}, UserID={e.Result.UserId}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    conversationTranscriber.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");

                        if (e.Reason == CancellationReason.Error)
                        {
                            Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
                            Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
                            Console.WriteLine($"CANCELED: Did you update the subscription info?");
                            stopTranscription.TrySetResult(0);
                        }
                    };

                    conversationTranscriber.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\nSession started event.");
                    };

                    conversationTranscriber.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\nSession stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopTranscription.TrySetResult(0);
                    };

                    // Add participants to the conversation.
                    // Create voice signatures using REST API described in the earlier section in this document.
                    // Voice signature needs to be in the following format:
                    // { "Version": <Numeric value>, "Tag": "string", "Data": "string" }

                    var speakerA = Participant.From("Speaker_A", "en-us", signatureA);
                    var speakerB = Participant.From("Speaker_B", "en-us", signatureB);
                    var speakerC = Participant.From("SPeaker_C", "en-us", signatureC);
                    await conversation.AddParticipantAsync(speakerA);
                    await conversation.AddParticipantAsync(speakerB);
                    await conversation.AddParticipantAsync(speakerC);

                    // Starts transcribing of the conversation. Uses StopTranscribingAsync() to stop transcribing when all participants leave.
                    await conversationTranscriber.StartTranscribingAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopTranscription.Task });

                    // Stop transcribing the conversation.
                    await conversationTranscriber.StopTranscribingAsync().ConfigureAwait(false);
                 }
            }
       }
    }
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Avskrift av asynkron konversation](how-to-async-conversation-transcription.md)
