---
title: Använda Azure Batch-avskrift API
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
ms.openlocfilehash: cd57e9a90b07447392fbff48017bb29f002ad29e
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2018
ms.locfileid: "51035959"
---
# <a name="use-batch-transcription"></a>Använda batch avskrift

Batch avskrift är perfekt om du har stora mängder ljud i lagring. Med hjälp av REST-API, kan du pekar på ljudfiler genom signatur för delad åtkomst (SAS) URI och ta emot avskrifter asynkront.

## <a name="the-batch-transcription-api"></a>Batch-avskrift API

API: et för Batch avskrift erbjuder asynkron tal till text-avskrift, tillsammans med ytterligare funktioner. Det är en REST-API som exponerar metoder för att:

1. Skapar batch-bearbetning av begäranden
1. Frågestatus 
1. Ladda ned avskrifter

> [!NOTE]
> API: et för Batch avskrift är perfekt för call Center, som vanligtvis ackumuleras tusentals timmars ljud. API: et leds av en ”utlöses och Glöm” filosofin, vilket gör det enkelt att transkribera stora mängder ljudinspelningar.

### <a name="supported-formats"></a>Format som stöds

API: et för Batch avskrift stöder följande format:

Namn| Kanal  |
----|----------|
MP3-filen |   Mono   |   
MP3-filen |  Stereo  | 
WAV |   Mono   |
WAV |  Stereo  |
Opus|   Mono   |
Opus|  Stereo  |

Delar upp kanalen vänster och höger under utskrift för stereo ljudströmmar batch avskrift. De två JSON-filerna med resultatet skapas var och en från en enda kanal. Tidsstämplar per uttryck gör att utvecklare kan skapa en ordnad slutlig avskrift. Utdata från en kanal, inklusive egenskaper för att konfigurera filter mot olämpligt språk och skiljetecken-modellen illustreras i följande JSON-exempel:

```json
{
  "recordingsUrl": "https://contoso.com/mystoragelocation",
  "models": [],
  "locale": "en-US",
  "name": "Transcription using locale en-US",
  "description": "An optional description of the transcription.",
  "properties": {
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic"
  },
```

> [!NOTE]
> API för Batch-avskrift använder en REST-tjänst för att begära avskrifter, deras status och associerade resultat. Du kan använda API: T från alla språk. I nästa avsnitt beskrivs hur API: et används.

## <a name="authorization-token"></a>Autentiseringstoken

Som med alla funktioner i Speech-tjänsten skapar du en prenumerationsnyckel från den [Azure-portalen](https://portal.azure.com) genom att följa våra [startguide](get-started.md). Om du planerar att hämta avskrifter från våra basmodeller, är skapar en nyckel allt du behöver göra. 

Om du planerar att anpassa och använda en anpassad modell, lägger du till prenumerationsnyckeln till portal för anpassat tal genom att göra följande:

1. Logga in på [Custom Speech](https://customspeech.ai).

2. Längst upp till höger, Välj **prenumerationer**.

3. Välj **ansluta befintliga prenumeration**.

4. Lägg till prenumerationsnyckeln och ett alias i popup-fönstret.

    ![Fönstret Lägg till prenumeration](media/stt/Subscriptions.jpg)

5. Kopiera och klistra in nyckeln i klientkoden i följande exempel.

> [!NOTE]
> Om du planerar att använda en anpassad modell behöver du ID för den modellen för. Detta ID är inte slutpunkts-ID som du hittar i vyn information om slutpunkten. Det är det modell-ID som du kan hämta när du väljer information om den modellen.

## <a name="sample-code"></a>Exempelkod

Anpassa följande exempelkod med en prenumerationsnyckel och en API-nyckel. Den här åtgärden kan du få en ägartoken.

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

När du har fått en token, anger du SAS-URI som pekar på filen som kräver avskrift. Resten av koden upprepas status och visar resultatet. Först ska ställa du in nyckel, region, modeller ska användas och SA, enligt följande kodavsnitt. Nu ska du skapa en instans av klienten och POST-begäran. 

```cs
            private const string SubscriptionKey = "<your Speech subscription key>";
            private const string HostName = "westus.cris.ai";
            private const int Port = 443;
    
            // SAS URI 
            private const string RecordingsBlobUri = "SAS URI pointing to the file in Azure Blob Storage";

            // adapted model Ids
            private static Guid AdaptedAcousticId = new Guid("guid of the acoustic adaptation model");
            private static Guid AdaptedLanguageId = new Guid("guid of the language model");

            // Creating a Batch Transcription API Client
            var client = CrisClient.CreateApiV2Client(SubscriptionKey, HostName, Port);
            
            var transcriptionLocation = await client.PostTranscriptionAsync(Name, Description, Locale, new Uri(RecordingsBlobUri), new[] { AdaptedAcousticId, AdaptedLanguageId }).ConfigureAwait(false);
```

Nu när du har gjort begäran, kan du fråga och ladda ned avskrift-resultat som visas i följande kodavsnitt:

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
                            // not created from here, continue
                            continue;
                        }
                            
                        completed++;
                            
                        // if the transcription was successful, check the results
                        if (transcription.Status == "Succeeded")
                        {
                            var resultsUri = transcription.ResultsUrls["channel_0"];
                            WebClient webClient = new WebClient();
                            var filename = Path.GetTempFileName();
                            webClient.DownloadFile(resultsUri, filename);
                            var results = File.ReadAllText(filename);
                            Console.WriteLine("Transcription succeeded. Results: ");
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

Fullständig information om föregående anrop finns i vår [swagger-dokument](https://westus.cris.ai/swagger/ui/index). För det fullständiga exemplet som visas här, gå till [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> I den föregående koden är prenumerationsnyckeln från tal-resurs som du skapar i Azure-portalen. Nycklar som du får från resursen Custom Speech Service fungerar inte.

Anteckna asynkron konfigurationen för att skicka ljud och ta emot avskrift status. Klienten som du skapar är en .NET-HTTP-klient. Det finns en `PostTranscriptions` metod för att skicka ljud Filinformation och en `GetTranscriptions` metod för att ta emot resultaten. `PostTranscriptions` Returnerar en referens och `GetTranscriptions` används för att skapa en referens för att hämta status för avskrift.

Aktuella exempelkoden Ange inte en anpassad modell. Tjänsten använder baslinjemodeller för att skriva av den filen eller filerna. Om du vill ange modeller, kan du skicka på samma metod som modell-ID för akustiska och språkmodellen. 

Om du inte vill använda baslinjen, skicka modell-ID: N för språk- och språkdata-modeller.

> [!NOTE]
> För baslinjen avskrifter har du inte deklarera slutpunkterna för baslinjemodeller. Om du vill använda anpassade modeller kan du ange deras slutpunkter-ID som den [exempel](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI). Om du vill använda en akustisk baslinje med en baslinje språkmodell måste du deklarera endast anpassade modellen endpoint-ID. Microsoft identifierar partner baslinje modellen&mdash;om språkdata eller språk&mdash;och används för att uppfylla begäran avskrift.

### <a name="supported-storage"></a>Lagring som stöds

För närvarande är endast lagring som stöds Azure Blob storage.

## <a name="download-the-sample"></a>Hämta exemplet

Du hittar exemplet i den här artikeln på [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> En ljudutskrift kräver normalt ett tidsintervall som är lika med varaktigheten för filen, plus en två till tre minuters kostnader.

## <a name="next-steps"></a>Nästa steg

* [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
