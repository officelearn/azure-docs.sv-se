---
title: Azure Batch avskrift API
description: Exempel
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.technology: Speech to Text
ms.topic: article
ms.date: 04/26/2018
ms.author: panosper
ms.openlocfilehash: 860b58a18fbc14532a8591fc753453d60492d3c0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46981380"
---
# <a name="batch-transcription"></a>Batch-transkription

Batch avskrift är perfekt om du har stora mängder ljud. Du kan peka på ljudfiler och få tillbaka avskrifter i asynkron läge.

## <a name="batch-transcription-api"></a>Batch avskrift API

Batch-avskrift API erbjuder asynkron tal till text avskrift, tillsammans med ytterligare funktioner.

> [!NOTE]
> API: et för Batch avskrift är perfekt för call Center, som vanligtvis ackumuleras tusentals timmars ljud. API: et leds av en ”utlöses och Glöm” filosofin, vilket gör det enkelt att transkribera stort antal ljudinspelningar.

### <a name="supported-formats"></a>Format som stöds

Batch-avskrift API stöder följande format:

Namn| Kanal  |
----|----------|
MP3-filen |   Mono   |   
MP3-filen |  Stereo  | 
WAV |   Mono   |
WAV |  Stereo  |

Delar upp kanalen vänster och höger under utskrift för stereo ljudströmmar Batch avskrift. De två JSON-filerna med resultatet skapas var och en från en enda kanal. Tidsstämplar per uttryck gör att utvecklare kan skapa en ordnad slutlig avskrift. I följande JSON-exempel visas resultatet av en kanal.

```json
       {
        "recordingsUrl": "https://mystorage.blob.core.windows.net/cris-e2e-datasets/TranscriptionsDataset/small_sentence.wav?st=2018-04-19T15:56:00Z&se=2040-04-21T15:56:00Z&sp=rl&sv=2017-04-17&sr=b&sig=DtvXbMYquDWQ2OkhAenGuyZI%2BYgaa3cyvdQoHKIBGdQ%3D",
        "resultsUrls": {
            "channel_0": "https://mystorage.blob.core.windows.net/bestor-87a0286f-304c-4636-b6bd-b3a96166df28/TranscriptionData/24265e4c-e459-4384-b572-5e3e7795221f?sv=2017-04-17&sr=b&sig=IY2qd%2Fkgtz2PwRe2C88BphH4Hv%2F1VCb1UVJ33xsw%2BEY%3D&se=2018-04-23T14:48:24Z&sp=r"
        },
        "statusMessage": "None.",
        "id": "0bb95356-ff06-469d-acc7-81f9144a269a",
        "createdDateTime": "2018-04-20T14:11:57.167",
        "lastActionDateTime": "2018-04-20T14:12:54.643",
        "status": "Succeeded",
        "locale": "en-US"
    },
```

> [!NOTE]
> Batch-avskrift API använder en REST-tjänst för att begära avskrifter, deras status och associerade resultat. Du kan använda API: T från alla språk. I nästa avsnitt beskrivs hur de används.

## <a name="authorization-token"></a>Autentiseringstoken

Som med alla funktioner i enhetliga Speech-tjänsten skapar du en prenumerationsnyckel från den [Azure-portalen](https://portal.azure.com) följande vår [Get-Started guide](get-started.md). Om du planerar att hämta avskrifter från våra basmodeller sedan det här är allt du behöver göra. 

Om du planerar att anpassa och använda en anpassad modell måste du lägga till den här nyckeln subscritpion i portal för anpassat tal på följande sätt:

1. Logga in på [Custom Speech](https://customspeech.ai).

2. Välj **Prenumerationer**.

3. Välj **ansluta befintliga prenumeration**.

4. Lägg till prenumerationsnyckeln och ett alias i vyn som visas

    ![Skärmbild för anpassat tal prenumerationssidan](media/stt/Subscriptions.jpg)

5. Kopiera och klistra in nyckeln i klientkoden i följande exempel.

> [!NOTE]
> Om du planerar att använda en anpassad modell behöver du ID för den modellen för. Observera att detta inte är slutpunkts-ID som du hittar i vyn information om slutpunkten. Det är det modell-ID som du kan hämta när du väljer information om den modellen.

## <a name="sample-code"></a>Exempelkod

Anpassa följande exempelkod med en prenumerationsnyckel och en API-nyckel. På så sätt kan du hämta en ägartoken.

```cs
     public static CrisClient CreateApiV2Client(string key, string hostName, int port)

        {
            var client = new HttpClient();
            client.Timeout = TimeSpan.FromMinutes(25);
            client.BaseAddress = new UriBuilder(Uri.UriSchemeHttps, hostName, port).Uri;
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);
         
            return new CrisClient(client);
        }
```

När du har fått en token måste du ange SAS-URI som pekar på filen kräver avskrift. Resten av koden upprepas status och visar resultat.

```cs
   static async Task TranscribeAsync()
        { 
            private const string SubscriptionKey = "<your Speech subscription key>";
            private const string HostName = "westus.cris.ai";
            private const int Port = 443;
    
            // Creating a Batch transcription API Client
            var client = CrisClient.CreateApiV2Client(SubscriptionKey, HostName, Port);
            
            var transcriptions = await client.GetTranscriptionAsync().ConfigureAwait(false);

            var transcriptionLocation = await client.PostTranscriptionAsync(Name, Description, Locale, new Uri(RecordingsBlobUri), new[] { AdaptedAcousticId, AdaptedLanguageId }).ConfigureAwait(false);

            // get the transcription Id from the location URI
            var createdTranscriptions = new List<Guid>();
            createdTranscriptions.Add(new Guid(transcriptionLocation.ToString().Split('/').LastOrDefault()))

            while (true)
            {
                // get all transcriptions for the user
                transcriptions = await client.GetTranscriptionAsync().ConfigureAwait(false);
                completed = 0; running = 0; notStarted = 0;

                // for each transcription in the list we check the status
                foreach (var transcription in transcriptions)
                {
                    switch(transcription.Status)
                    {
                        case "Failed":
                        case "Succeeded":

                            // we check to see if it was one of the transcriptions we created from this client.
                            if (!createdTranscriptions.Contains(transcription.Id))
                            {
                                // not creted form here, continue
                                continue;
                            }
                            
                            completed++;
                            
                            // if the transcription was successfull, check the results
                            if (transcription.Status == "Succeeded")
                            {
                                var resultsUri = transcription.ResultsUrls["channel_0"];
                                WebClient webClient = new WebClient();
                                var filename = Path.GetTempFileName();
                                webClient.DownloadFile(resultsUri, filename);
                                var results = File.ReadAllText(filename);
                                Console.WriteLine("Transcription succedded. Results: ");
                                Console.WriteLine(results);
                            }
                            break;
                        case "Running":
                            running++;
                            break;
                        case "NotStarted":
                            notStarted++;
                            break;
                    }
                }

                Console.WriteLine(string.Format("Transcriptions status: {0} completed, {1} running, {2} not started yet", completed, running, notStarted));

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }

            Console.WriteLine("Press any key...");
        }
```

> [!NOTE]
> I den föregående koden är prenumerationsnyckeln från tal-resurs som du skapar på Azure-portalen. Nycklar som hämtats från resursen Custom Speech Service fungerar inte.

Lägg märke till asynkron konfigurationen för att skicka ljud och ta emot avskrift status. Klienten som skapade är en .NET-Http-klient. Det finns en `PostTranscriptions` metod för att skicka ljud filinformation, och en `GetTranscriptions` metoden för att hämta resultaten. `PostTranscriptions` Returnerar en referens och `GetTranscriptions` använder den här referensen för att skapa en referens för att hämta statusen avskrift.

Aktuella exempelkoden anger inte några anpassade modeller. Tjänsten använder baslinjemodeller för att skriva av den filen eller filerna. Om du vill ange modellerna, kan du skicka på samma metod modell-ID: N för akustiska och språkmodellen. 

Om du inte vill använda baslinjen, måste du ange modell-ID: N för språk- och språkdata-modeller.

> [!NOTE]
> För baslinjen avskrift har du inte deklarera slutpunkterna för baslinjemodeller. Om du vill använda anpassade modeller kan du ange deras slutpunkter-ID som den [exempel](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI). Om du vill använda en akustisk baslinje med en baslinje språkmodell behöver du bara deklarera anpassade modellen endpoint-ID. Microsoft identifierar baslinje partner modell (vara den akustiska eller språk), och använder som för att uppfylla begäran avskrift.

### <a name="supported-storage"></a>Lagring som stöds

Den enda lagring som stöds är för närvarande Azure Blob storage.

## <a name="downloading-the-sample"></a>Ladda ned exemplet

Det här exemplet finns på [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> En ljudutskrift kräver vanligtvis ett tidsintervall som är lika med varaktigheten för filen, plus en 2 – 3 minuter kostnader.

## <a name="next-steps"></a>Nästa steg

* [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
