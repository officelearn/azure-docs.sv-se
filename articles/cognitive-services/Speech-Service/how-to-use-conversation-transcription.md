---
title: Konversationsavskrift i realtid (förhandsgranskning) - Taltjänst
titleSuffix: Azure Cognitive Services
description: Läs om hur du använder konversationsavskription i realtid med Tal-SDK. Tillgänglig för C++, C#och Java.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: weixu
ms.openlocfilehash: 64a9e11cec7164fb4421dd018238de9f0670382b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76263735"
---
# <a name="real-time-conversation-transcription-preview"></a>Konversationsavskrift i realtid (förhandsgranskning)

Med Speech SDK:s **ConversationTranscriber-API** kan du transkribera möten och andra konversationer med möjlighet att `PullStream` lägga `PushStream`till, ta bort och identifiera flera deltagare genom att strömma ljud till taltjänsten med eller . Det här avsnittet kräver att du vet hur du använder Tal-till-text med Tal-SDK (version 1.8.0 eller senare). Mer information finns i [Vad är taltjänster](overview.md).

## <a name="limitations"></a>Begränsningar

- ConversationTranscriber-API:et stöds för C++, C# och Java på Windows, Linux och Android.
- För närvarande finns i "en-US" och "zh-CN" språk i följande regioner: _centralus_ och _eastasia_.
- Kräver en matris med 7 mikrofoner med uppspelningsreferens. Mikrofonen array bör uppfylla [våra specifikationer](https://aka.ms/sdsdk-microphone).
- [Speech Devices SDK](speech-devices-sdk.md) tillhandahåller lämpliga enheter och en exempelapp som visar konversationsavskription.

## <a name="optional-sample-code-resources"></a>Valfria exempelkodresurser

Speech Device SDK innehåller exempelkod i Java för ljudinspelning i realtid med 8 kanaler.

- [EXEMPELKOD FÖR ROOBO-enhet](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/Conversation.java)
- [Exempelkod för Azure Kinect Dev Kit](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)

## <a name="prerequisites"></a>Krav

En prenumeration på taltjänsten. Du kan [få en taltestprenumeration](https://azure.microsoft.com/try/cognitive-services/) om du inte har någon.

## <a name="create-voice-signatures"></a>Skapa röstsignaturer

Det första steget är att skapa röstsignaturer för konversationsdeltagarna för effektiv högtalaridentifiering.

### <a name="audio-input-requirements"></a>Krav på ljudinmatning

- Indataljudvågsfilen för att skapa röstsignaturer ska finnas i 16-bitarsprover, 16 kHz-samplingsfrekvens och ett monoformat med en kanal (.input audio wave file for creating voice signatures should be in 16-bit samples, 16 kHz sample rate, and a single channel (mono) format.
- Den rekommenderade längden för varje ljudprov är mellan trettio sekunder och två minuter.

### <a name="sample-code"></a>Exempelkod

I följande exempel visas två olika sätt att skapa röstsignatur med hjälp av [REST API](https://aka.ms/cts/signaturegenservice) i C#. Observera att du måste ersätta verklig information med "YourSubscriptionKey", ditt vågfilnamn för "speakerVoice.wav" och din region för `{region}` och "YourServiceRegion"_(centralus_ eller _eastasia)._

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
        // Voice signature format example: { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }
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
        // Voice signature format example: { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static void Main(string[] args)
    {
        CreateVoiceSignatureByUsingFormData().Wait();
        CreateVoiceSignatureByUsingBody().Wait();
    }
}
```

## <a name="transcribe-conversations"></a>Transkribera konversationer

Följande exempelkod visar hur du transkriberar konversationer i realtid för tre högtalare. Det förutsätter att du redan har skapat röstsignaturer för varje högtalare som visas ovan. Ersätt verklig information med "YourSubscriptionKey" och "YourServiceRegion" när du skapar SpeechConfig-objektet.

Exempel på kodhöjdpunkter är:

- Skapa `Conversation` ett objekt `SpeechConfig` från objektet med hjälp av en mötesidentifierare som genereras med hjälp av`Guid.NewGuid()`
- Skapa `ConversationTranscriber` ett objekt och `JoinConversationAsync()` delta i konversationen med för att starta transkriptionen
- Registrera evenemang av intresse
- Lägga till eller ta bort deltagare i konversationen med konversationsobjektet
- Strömma ljudet
- I Tal SDK version 1.9.0 `int` `string` och framåt stöds båda och värdetyper i versionsfältet för röstsignatur.

Transkriptionen och högtalaridentifieraren kommer tillbaka i de registrerade händelserna.

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
            using (var conversation = await Conversation.CreateConversationAsync(config, meetingId).ConfigureAwait(false))
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
                    // { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }

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
> [Asynkron samtalstranskription](how-to-async-conversation-transcription.md)
