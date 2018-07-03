---
title: Azure Batch avskrift API | Azure Microsoft Docs
description: Exempel
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.technology: Speech to Text
ms.topic: article
ms.date: 04/26/2018
ms.author: panosper
ms.openlocfilehash: 9dd7479ae95f74123d9b762e42ec95e8dbf25818
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2018
ms.locfileid: "37346457"
---
# <a name="batch-transcription"></a>Batch-transkription

Batch avskrift är perfekt för användningsfall med stora mängder ljud. Det gör det möjligt för utvecklare att peka på ljudfiler och få tillbaka avskrifter i asynkron läge.

## <a name="batch-transcription-api"></a>Batch avskrift API

Batch-avskrift API gör det möjligt för scenariot ovan. Den erbjuder asynkron tal till text avskrift tillsammans med ytterligare funktioner.

> [!NOTE]
> Batch-avskrift API är perfekt för Call Center som vanligtvis ackumuleras tusentals timmars ljud. Fire & Glöm filosofin API gör det enkelt att transkribera stort antal ljudinspelningar.

### <a name="supported-formats"></a>Format som stöds

Batch-avskrift API syftar till att bli Tyskland-facto för alla offline anrop center-relaterade scenarier och erbjuder support för alla relaterade format. Format som för närvarande stöds:

Namn| Kanal  |
----|----------|
MP3-filen |   Mono   |   
MP3-filen |  Stereo  | 
WAV |   Mono   |
WAV |  Stereo  |

För stereo ljudströmmar delar Batch avskrift kanalen vänster och höger under utskrift. De två JSON-filerna med resultatet skapas var och en från en enda kanal. Tidsstämplar per uttryck gör att utvecklare kan skapa en ordnad slutlig avskrift. I följande JSON-exempel visas resultatet av en kanal.

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
> Batch-avskrift API använder en REST-tjänst för att begära avskrifter, deras status och associerade resultat. API: et kan användas från alla språk. I nästa avsnitt beskrivs hur de används.

## <a name="authorization-token"></a>Autentiseringstoken

Som med alla funktioner i tjänsten enhetliga Speech användaren behöver för att skapa en prenumerationsnyckel från den [Azure-portalen](https://portal.azure.com). Dessutom kan måste en API-nyckel förvärvas från portalen tal. Stegen för att generera en API-nyckel:

1. Logga in på https://customspeech.ai.

2. Klicka på prenumerationer.

3. Klicka på alternativet `Generate API Key`.

    ![Ladda upp vyn](media/stt/Subscriptions.jpg)

4. Kopiera och klistra in nyckeln i klientkoden i exemplet nedan.

> [!NOTE]
> Om du planerar att använda en anpassad modell måste ID för den modellen för. Observera att detta inte är distributionen eller slutpunkts-ID som du hittar på slutpunkten detaljer, men modell-ID som du kan hämta när du klickar på information om den modellen

## <a name="sample-code"></a>Exempelkod

Genom att utnyttja API: et är ganska enkelt. Exempelkoden nedan måste uppgraderas till en prenumerationsnyckel och en API-nyckel, vilken i sin tur kan utvecklare hämta någon ägartoken, som i följande kodavsnitt visar:

```cs
    public static async Task<CrisClient> CreateApiV1ClientAsync(string username, string key, string hostName, int port)
        {
            var client = new HttpClient();
            client.Timeout = TimeSpan.FromMinutes(25);
            client.BaseAddress = new UriBuilder(Uri.UriSchemeHttps, hostName, port).Uri;

            var tokenProviderPath = "/oauth/ctoken";
            var clientToken = await CreateClientTokenAsync(client, hostName, port, tokenProviderPath, username, key).ConfigureAwait(false);
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("bearer", clientToken.AccessToken);

            return new CrisClient(client);
        }
```

När token hämtas måste utvecklaren ange SAS-Uri som pekar på filen kräver avskrift. Resten av koden helt enkelt upprepas status och visar resultat.

```cs
   static async Task TranscribeAsync()
        { 
            // Creating a Batch transcription API Client
            var client = 
                await CrisClient.CreateApiV1ClientAsync(
                    "<your msa>", // MSA email
                    "<your api key>", // API key
                    "stt.speech.microsoft.com",
                    443).ConfigureAwait(false);
            
            var newLocation = 
                await client.PostTranscriptionAsync(
                    "<selected locale i.e. en-us>", // Locale 
                    "<your subscription key>", // Subscription Key
                    new Uri("<SAS URI to your file>")).ConfigureAwait(false);

            var transcription = await client.GetTranscriptionAsync(newLocation).ConfigureAwait(false);

            while (true)
            {
                transcription = await client.GetTranscriptionAsync(transcription.Id).ConfigureAwait(false);

                if (transcription.Status == "Failed" || transcription.Status == "Succeeded")
                {
                    Console.WriteLine("Transcription complete!");

                    if (transcription.Status == "Succeeded")
                    {
                        var resultsUri = transcription.ResultsUrls["channel_0"];

                        WebClient webClient = new WebClient();

                        var filename = Path.GetTempFileName();
                        webClient.DownloadFile(resultsUri, filename);

                        var results = File.ReadAllText(filename);
                        Console.WriteLine(results);
                    }

                    await client.DeleteTranscriptionAsync(transcription.Id).ConfigureAwait(false);

                    break;
                }
                else
                {
                    Console.WriteLine("Transcription status: " + transcription.Status);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }

            Console.ReadLine();
        }
```

> [!NOTE]
> Prenumerationsnyckel som nämns i kodfragmentet ovan är nyckeln från Speech(Preview) resursen som du skapar på Azure-portalen. Nycklar som hämtats från resursen Custom Speech Service fungerar inte.


Lägg märke till asynkron konfigurationen för att skicka ljud och ta emot avskrift status. Klienten som skapade är en .NET-Http-klient. Det finns en `PostTranscriptions` metod för att skicka ljud filinformation, och en `GetTranscriptions` metoden för att hämta resultaten. `PostTranscriptions` Returnerar en referens och `GetTranscriptions` metoden att använda den här hanteraren för att skapa en referens för att hämta statusen avskrift.

Aktuella exempelkoden anger inte några anpassade modeller. Tjänsten använder baslinjemodeller för att skriva av filen. Om du vill ange modeller, överföra en på samma metod modelIDs för akustiska och språkmodellen. 

Om något inte vill använda baslinje, måste en klara modell-ID: N för språk- och språkdata-modeller.

> [!NOTE]
> För baslinjen saknar avskrift användaren deklarera slutpunkterna för baslinjemodeller. Om du vill använda anpassade modeller han skulle behöva ange deras slutpunkter-ID som den [exempel](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI). Om användare vill använda en akustisk baslinje med en baslinje språkmodell sedan behöver han bara deklarera anpassade modellen endpoint-ID. Internt ska vårt system ta reda på partner baslinje-modellen (vara den akustiska eller språk) och använder dem för att uppfylla begäran avskrift.

### <a name="supported-storage"></a>Lagring som stöds

Den enda lagring som stöds är för närvarande Azure-blob.

## <a name="downloading-the-sample"></a>Ladda ned exemplet

I exemplet som visas här finns på [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> En ljudutskrift kräver vanligtvis ett tidsintervall som är lika med varaktigheten för ljudfilen plus en 2 – 3 minuter kostnader.

## <a name="next-steps"></a>Nästa steg

* [Få en kostnadsfri prenumeration tal](https://azure.microsoft.com/try/cognitive-services/)
