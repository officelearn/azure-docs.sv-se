---
title: Azure Batch skrivfel API | Azure Microsoft Docs
description: Exempel
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.technology: Speech to Text
ms.topic: article
ms.date: 04/26/2018
ms.author: panosper
ms.openlocfilehash: 01bbf4ca19b0fb702aa76d5149fb0e38389fe455
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054831"
---
# <a name="batch-transcription"></a>Batch skrivfel

Batch skrivfel är idealisk för användningsområden med stora mängder ljud. Det gör det möjligt för utvecklare att peka på ljudfiler och hämta transcriptions i asynkron läge.

## <a name="batch-transcription-api"></a>Batch skrivfel API

Batch-skrivfel API gör det möjligt att scenariot ovan. Den erbjuder asynkron tal till text skrivfel tillsammans med ytterligare funktioner.

> [!NOTE]
> Batch skrivfel API är idealisk för samtal som vanligtvis ackumuleras tusentals timmars ljud. Brand & Glöm principerna om API: N gör det enkelt att transkribera stora mängder ljudinspelningar.

### <a name="supported-formats"></a>Format som stöds

Batch-skrivfel API syftar till att bli Tyskland-facto för alla offline call center-relaterade scenarier och erbjuda stöd för alla relaterade format. För närvarande stöds formaten:

Namn| Kanal  |
----|----------|
MP3 |   Mono   |   
MP3 |  Stereo  | 
WAV |   Mono   |
WAV |  Stereo  |

För stereo ljudströmmar delas Batch skrivfel den vänstra och högra kanalen under utskrift. Två JSON-filer med resultatet skapas varje från en enda kanal. Tidsstämplar per utterance kan utvecklare att skapa en ordnad slutliga betyg. Följande JSON-exemplet visar resultatet för en kanal.

    ```
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
> Batch-skrivfel API använder en REST-tjänst för att begära transcriptions, status och associerade resultat. Den är baserad på .NET och har inte några externa beroenden. I nästa avsnitt beskrivs hur de används.

## <a name="authorization-token"></a>Autentiseringstoken

Som med alla funktioner i tjänsten Unified tal måste användaren att skapa en prenumeration nyckel från den [Azure-portalen](https://portal.azure.com). Dessutom måste en API-nyckel förvärvas från portalen tal. Stegen för att generera en API-nyckel:

1. Logga in på https://customspeech.ai.

2. Klicka på prenumerationer.

3. Klicka på alternativet `Generate API Key`.

    ![Överför vyn](media/stt/Subscriptions.jpg)

4. Kopiera och klistra in nyckeln i klientkod i exemplet nedan.

> [!NOTE]
> Om du planerar att använda en anpassad modellen måste ID för den modellen för. Observera att detta inte är distribution eller slutpunkts-ID som du hittar på slutpunkten Detaljvyn men modell-ID som du kan hämta när du klickar på information om att modellen

## <a name="sample-code"></a>Exempelkod

Genom att utnyttja API: et är ganska enkelt. Exempelkoden nedan måste anpassas med en prenumeration och en API-nyckel.

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
                    "<your subscripition key>", // Subscription Key
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
> Nyckeln prenumeration enligt ovan kodfragmentet är nyckeln från Speech(Preview) resursen som du skapar på Azure-portalen. Nycklar från anpassade tal tjänstresurs fungerar inte.


Observera asynkron inställningarna för bokföring ljud och ta emot skrivfel status. Klienten skapas är en NET Http-klient. Det finns en `PostTranscriptions` metod för att skicka information ljud- och en `GetTranscriptions` metoden för att hämta resultaten. `PostTranscriptions` Returnerar en referens och `GetTranscriptions` metoden använder den här referensen för att skapa en referens för att hämta status för skrivfel.

Aktuella exempelkoden anger inte några anpassade modeller. Tjänsten använder baslinjen modeller för att skriva filen. Om användare vill ange modeller, överföra en på samma metod modelIDs för acoustic och språkmodell. 

Om något inte vill använda baslinje, måste en klara modell-ID: N för både acoustic och språk-modeller.

> [!NOTE]
> För baslinjen saknar skrivfel användaren deklarera slutpunkter av baslinje-modeller. Om användaren vill använda anpassade modeller han skulle behöva ange sina slutpunkter-ID: N som den [exempel](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI). Om användaren vill använda ett ljud baslinjen med en baslinje språkmodell sedan behöver han bara deklarera anpassade modellen endpoint-ID. Internt kommer vårt system ta reda på partner baslinjen modellen (vara den ljud eller språk) och använda den för att fullfill skrivfel begäran.

### <a name="supported-storage"></a>Stöds

För närvarande är stöds endast lagring Azure blob.

## <a name="downloading-the-sample"></a>Hämta exemplet

Exempel som visas här finns på [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> Ett ljud skrivfel kräver normalt en tidsrymd som är lika med varaktigheten för ljudfilen plus en 2-3 minuter kostnader.

## <a name="next-steps"></a>Nästa steg

* [Hämta din utvärderingsprenumeration tal](https://azure.microsoft.com/try/cognitive-services/)
