---
title: Skriva över konversationer med flera deltagare med tal SDK-tal tjänsten
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder en konversations avskrift med talet SDK. Tillgängligt för C++, C#och Java.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: jhakulin
ms.openlocfilehash: 8c4ecc017d058900297f2220173e064700e7051b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559459"
---
# <a name="transcribe-multi-participant-conversations-with-the-speech-sdk"></a>Skriva över konversationer med flera deltagare med talet SDK

Talet SDK: s **ConversationTranscriber** -API gör att du kan lägga upp möten/samtal med möjlighet att lägga till, ta bort och identifiera deltagare genom att strömma ljud till tal tjänsterna `PullStream` med `PushStream`hjälp av eller.

## <a name="limitations"></a>Begränsningar

* Konversations-inskrivare stöds C++för C#, och Java på Windows, Linux och Android.
* ROOBO-DevKit är den maskin varu miljö som stöds för att skapa konversations avskrifter som ger en cirkulär matris med flera mikrofoner som kan användas effektivt för högtalar identifiering. [Mer information finns i avsnittet om tal enheter SDK](speech-devices-sdk.md).
* Stöd för tal-SDK för konversations avskrift är begränsat till användning av strömnings-och push-läge i ljud med åtta kanaler på 16-bitars 16 kHz PCM-ljud.
* Konversations avskrift är för närvarande tillgängligt i språken "en-US" och "zh-CN" i följande regioner: Central-och asienöstra.

## <a name="prerequisites"></a>Förutsättningar

* [Lär dig hur du använder tal-till-text med talet SDK.](quickstart-csharp-dotnet-windows.md)
* [Hämta din utvärderings prenumeration för tal.](https://azure.microsoft.com/try/cognitive-services/)
* Tal SDK-version 1.5.1 eller senare krävs.

## <a name="create-voice-signatures-for-participants"></a>Skapa röst under skrifter för deltagare

Det första steget är att skapa röst signaturer för konversations deltagarna. Du måste skapa röst signaturer för att kunna identifiera en effektiv talare.

### <a name="requirements-for-input-wave-file"></a>Krav för ingångs Wave-fil

* Indata-ljudwave-filen för att skapa röst under skrifter måste vara i 16-bitars exempel, 16 kHz samplings frekvens och ett enda kanal format (mono).
* Den rekommenderade längden för varje ljud sampling är mellan 30 sekunder och två minuter.

I följande exempel visas två olika sätt att skapa röst signatur med [hjälp av REST API](https://aka.ms/cts/signaturegenservice) från C#:

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

## <a name="transcribing-conversations"></a>Skriva över konversationer

Om du vill skriva över konversationer med flera deltagare skapar `ConversationTranscriber` du objektet som är associerat `AudioConfig` med objektet som har skapats för konversationen och strömma `PullAudioInputStream` ljud `PushAudioInputStream`med eller.

Vi antar att du har en ConversationTranscriber-klass som `MyConversationTranscriber`heter. Din kod kan se ut så här:

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
