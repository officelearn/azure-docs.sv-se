---
title: Transkribera flera deltagare konversationer med tal SDK - Speech Services
titleSuffix: Azure Cognitive Services
description: Lär dig mer om att använda konversationen avskrift med tal SDK. Tillgänglig för C++, C#, och Java.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: jhakulin
ms.openlocfilehash: f0838d345abfcfdf69ca6ea44d3206c23010b457
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073093"
---
# <a name="transcribe-multi-participant-conversations-with-the-speech-sdk"></a>Transkribera flera deltagare konversationer med Speech-SDK

Tal-SDK **ConversationTranscriber** API kan du transkribera möten/konversationer med möjlighet att lägga till, ta bort och identifiera deltagarna genom strömning av ljud till Speech Services med `PullStream` eller `PushStream`.

## <a name="limitations"></a>Begränsningar

* Konversationen transkribering stöds för C++, C#, och Java på Windows, Linux och Android.
* ROOBO DevKit är miljön maskinvara som stöds för att skapa konversationen avskrifter som ger cirkulär flera mikrofon matris som kan användas effektivt för talaridentifiering. [Mer information finns i tal Devices SDK](speech-devices-sdk.md).
* Tal SDK stöd för konversationen avskrift är begränsad till användning av ljud pull och push-läge strömmar med åtta kanaler med 16-bitars 16 kHz PCM ljud.
* Konversationen avskrift är för närvarande tillgängligt i ”en-US” och ”zh-CN” språk i följande regioner: centralus och asienöstra.

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Lär dig hur du använder tal till text med tal SDK.](quickstart-csharp-dotnet-windows.md)
* [Få en kostnadsfri prenumeration tal.](https://azure.microsoft.com/try/cognitive-services/)
* Tal SDK-version 1.5.1 eller senare krävs.

## <a name="create-voice-signatures-for-participants"></a>Skapa röst signaturer för deltagare

Det första steget är att skapa röst signaturer för konversationen deltagare. Skapa röst signaturer krävs för effektiv talaridentifiering.

### <a name="requirements-for-input-wave-file"></a>Krav för inkommande ljudfil

* Inkommande ljud wave-filen för att skapa röst signaturer skall vara i 16-bitars exempel, 16 kHz samplingshastighet och ett enda kanal (Mono)-format.
* Det rekommenderade antalet för varje ljud exempel är mellan 30 sekunder och två minuter.

I följande exempel visas två olika sätt att skapa röst signatur genom [med hjälp av REST-API](https://aka.ms/cts/signaturegenservice) från C#:

```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        var region = "YourServiceRegion";
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        var region = "YourServiceRegion";
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
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

## <a name="transcribing-conversations"></a>Transkribera konversationer

För att transkribera konversationer med flera deltagare, skapa den `ConversationTranscriber` objekt som är associerad med den `AudioConfig` objekt som skapats för konversationen sessionen och ljud med hjälp av `PullAudioInputStream` eller `PushAudioInputStream`.

Anta att du har en ConversationTranscriber klass med namnet `MyConversationTranscriber`. Din kod kan se ut så här:

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Conversation;

public class MyConversationTranscriber
{
    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region.
        // Replace with your own subscription key and region.
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
        var stopTranscription = new TaskCompletionSource<int>();

        // Create an audio stream from a wav file.
        // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
        using (var audioInput = Helper.OpenWavFile(@"8channelsOfRecordedPCMAudio.wav"))
        {
            // Creates a conversation transcriber using audio stream input.
            using (var transcriber = new ConversationTranscriber(config, audioInput))
            {
                // Subscribes to events.
                transcriber.Recognizing += (s, e) =>
                {
                    Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                };

                transcriber.Recognized += (s, e) =>
                {
                    if (e.Result.Reason == ResultReason.RecognizedSpeech)
                    {
                        Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}, UserID={e.Result.UserId}");
                    }
                    else if (e.Result.Reason == ResultReason.NoMatch)
                    {
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    }
                };

                transcriber.Canceled += (s, e) =>
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

                transcriber.SessionStarted += (s, e) =>
                {
                    Console.WriteLine("\nSession started event.");
                };

                transcriber.SessionStopped += (s, e) =>
                {
                    Console.WriteLine("\nSession stopped event.");
                    Console.WriteLine("\nStop recognition.");
                    stopTranscription.TrySetResult(0);
                };

                // Sets a conversation Id.
                transcriber.ConversationId = "AConversationFromTeams";

                // Add participants to the conversation.
                // Create voice signatures using REST API described in the earlier section in this document. 
                // Voice signature needs to be in the following format:
                // { "Version": <Numeric value>, "Tag": "string", "Data": "string" }

                var speakerA = Participant.From("Speaker_A", "en-us", signatureA);
                var speakerB = Participant.From("Speaker_B", "en-us", signatureB);
                var speakerC = Participant.From("SPeaker_C", "en-us", signatureC);
                transcriber.AddParticipant(speakerA);
                transcriber.AddParticipant(speakerB);
                transcriber.AddParticipant(speakerC);

                // Starts transcribing of the conversation. Uses StopTranscribingAsync() to stop transcribing when all participants leave.
                await transcriber.StartTranscribingAsync().ConfigureAwait(false);

                // Waits for completion.
                // Use Task.WaitAny to keep the task rooted.
                Task.WaitAny(new[] { stopTranscription.Task });

                // Stop transcribing the conversation.
                await transcriber.StopTranscribingAsync().ConfigureAwait(false);

                // Ends the conversation.
                await transcriber.EndConversationAsync().ConfigureAwait(false);
            }
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska våra exempel på GitHub](https://aka.ms/csspeech/samples)
