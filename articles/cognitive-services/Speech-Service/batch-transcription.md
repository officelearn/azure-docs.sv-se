---
title: Azure Batch avskrift API
titlesuffix: Azure Cognitive Services
description: Exempel för att skriva av stora mängder ljudinnehåll.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: panosper
ms.openlocfilehash: e7523bf97d6252422ebb853b818453c935640f50
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2018
ms.locfileid: "49648810"
---
# <a name="batch-transcription"></a>Batch-transkription

Batch avskrift är perfekt om du har stora mängder ljud i lagring. Med vår Rest-API kan du pekar på ljudfiler av SAS-URI och asynkront får avskrifter.

## <a name="batch-transcription-api"></a>Batch avskrift API

Batch-avskrift API erbjuder asynkron tal till text avskrift, tillsammans med ytterligare funktioner. Det är ett REST-API som exponerar metoder för att:

1. Skapar batch-bearbetning av begäranden

2. Frågestatus 

3. Ladda ned trnascriptions

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

Som med alla funktioner i Speech-tjänsten skapar du en prenumerationsnyckel från den [Azure-portalen](https://portal.azure.com) följande vår [Get-Started guide](get-started.md). Om du planerar att hämta avskrifter från våra basmodeller sedan det här är allt du behöver göra. 

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

När du har fått en token måste du ange SAS-URI som pekar på filen kräver avskrift. Resten av koden upprepas status och visar resultat. En skulle först ställa in nyckeln, region, modeller för att använda och SA. som du ser i kodfragmentet nedan. Detta åtföljs av instansiering av klienten och POST-begäran. 

```cs
            private const string SubscriptionKey = "<your Speech subscription key>";
            private const string HostName = "westus.cris.ai";
            private const int Port = 443;
    
            // SAS URI 
            private const string RecordingsBlobUri = "some SAS URI";

            // adapted model Ids
            private static Guid AdaptedAcousticId = new Guid("some guid");
            private static Guid AdaptedLanguageId = new Guid("some guid");

            // Creating a Batch transcription API Client
            var client = CrisClient.CreateApiV2Client(SubscriptionKey, HostName, Port);
            
            var transcriptionLocation = await client.PostTranscriptionAsync(Name, Description, Locale, new Uri(RecordingsBlobUri), new[] { AdaptedAcousticId, AdaptedLanguageId }).ConfigureAwait(false);
```

Nu när begäran har gjorts användaren kan fråga efter och hämta avskrift resultat som kodfragment i kod.

```cs
  
            // get all transcriptions for the user
            transcriptions = await client.GetTranscriptionAsync().ConfigureAwait(false);

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
            }
        }
```

Vår [Swagger-dokument](https://westus.cris.ai/swagger/ui/index) innehåller fullständig information om ovanstående anrop. Det fullständiga exemplet som visas här finns på [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

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
