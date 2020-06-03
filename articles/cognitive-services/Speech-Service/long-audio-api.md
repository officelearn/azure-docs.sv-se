---
title: Tids krävande ljud-API (för hands version) – tal tjänst
titleSuffix: Azure Cognitive Services
description: 'Lär dig hur den långa ljud-API: n är utformad för asynkron syntes av lång Forms text till tal.'
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: trbye
ms.openlocfilehash: 550579b40470d7a1ad02031b8140e7d0a7164f46
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310605"
---
# <a name="long-audio-api-preview"></a>Långt ljud-API (för hands version)

Den långa ljud-API: n är utformad för asynkron syntes av lång Forms text till tal (till exempel: ljud böcker, nyhets artiklar och dokument). Det här API: t returnerar inte syntetiskt ljud i real tid, i stället är det förväntat att du ska söka efter svar och använda de utdata som de görs tillgängliga från tjänsten. Till skillnad från text till tal-API som används av talet SDK, kan det långa ljud-API: et skapa syntetiskt ljud som är längre än 10 minuter, vilket gör det perfekt för utgivare och ljud innehålls plattformar.

Ytterligare fördelar med den långa ljud-API: et:

* Syntetiskt tal som returneras av tjänsten använder de bästa neurala-rösterna.
* Du behöver inte distribuera en röst slut punkt när den syntetiserar röster i ingen real tids batch-läge.

> [!NOTE]
> API för långa ljud stöder nu både [offentliga neurala-röster](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices) och [anpassade neurala-röster](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices).

## <a name="workflow"></a>Arbetsflöde

När du använder det långa ljud-API: et skickar du normalt en textfil eller filer som ska syntetiseras, avsöker efter status. om statusen är klar kan du ladda ned ljud uppspelningen.

Det här diagrammet innehåller en översikt över arbets flödet.

![Arbets flödes diagram för långt ljud-API](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Förbered innehåll för syntes

När du förbereder text filen måste du se till att den:

* Är antingen oformaterad text (. txt) eller SSML text (. txt)
* Kodas som [UTF-8 med byte ordnings tecken (BOM)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* Är en enskild fil, inte ett zip
* Innehåller fler än 400 tecken för oformaterad text eller 400 [fakturerbara tecken](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech#pricing-note) för SSML text och färre än 10 000 stycken
  * För oformaterad text separeras varje stycke genom att trycka på **RETUR/retur** -Visa [text ingångs exempel](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)
  * För SSML text betraktas varje SSML-enhet som ett stycke. SSML bitar separeras av olika stycken – Visa [SSML text ingångs exempel](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)
> [!NOTE]
> För kinesiska (fast), kinesiska (Hongkong SAR), kinesiska (Taiwan), japanska och koreanska, räknas ett ord som två tecken. 

## <a name="submit-synthesis-requests"></a>Skicka syntes begär Anden

När du har bearbetat indata-innehållet följer du snabb starten för det [långa form ljud syntesen](https://aka.ms/long-audio-python) för att skicka begäran. Om du har fler än en indatafil måste du skicka flera begär Anden. 

**Http-status koderna** anger vanliga fel.

| API | HTTP-statuskod | Description | Förslag |
|-----|------------------|-------------|----------|
| Skapa | 400 | Röst syntesen är inte aktive rad i den här regionen. | Ändra tal prenumerations nyckeln med en region som stöds. |
|        | 400 | Endast **standard** tal prenumerationen för den här regionen är giltig. | Ändra tal prenumerations nyckeln till pris nivån "standard". |
|        | 400 | Överskrid gränsen för 20 000-begäran för Azure-kontot. Ta bort några förfrågningar innan du skickar nya. | Servern kommer att ha upp till 20 000 förfrågningar för varje Azure-konto. Ta bort några begär Anden innan du skickar nya. |
|        | 400 | Det går inte att använda den här modellen i röst syntesen: {modelID}. | Kontrol lera att status för {modelID} är korrekt. |
|        | 400 | Regionen för begäran matchar inte regionen för modellen {modelID}. | Kontrol lera att {modelID} s region stämmer överens med begärans region. |
|        | 400 | Röst syntesen stöder bara text filen i UTF-8-kodningen med byte-ordnings markören. | Kontrol lera att indatafilerna är i UTF-8-kodning med markör för byte-ordning. |
|        | 400 | Endast giltiga SSML-indata är tillåtna i röst syntes förfrågan. | Kontrol lera att SSML-uttrycken är korrekta. |
|        | 400 | Det gick inte att hitta röst namnet {voiceName} i indatafilen. | Röst namnet för SSML är inte justerat med modell-ID: t. |
|        | 400 | Styckets mängd i indatafilen ska vara mindre än 10 000. | Kontrol lera att stycket i filen är mindre än 10 000. |
|        | 400 | Indatafilen får innehålla mer än 400 tecken. | Kontrol lera att indatafilen överskrider 400 tecken. |
|        | 404 | Det går inte att hitta modellen som har deklarerats i röst syntes definitionen: {modelID}. | Kontrol lera att {modelID} är korrekt. |
|        | 429 | Överskrida den aktiva röst syntes gränsen. Vänta tills några begär Anden har slutförts. | Servern kan köra och köa upp till 120 förfrågningar för varje Azure-konto. Vänta och Undvik att skicka nya begär Anden förrän vissa begär Anden har slutförts. |
| Alla       | 429 | Det finns för många begär Anden. | Klienten får skicka upp till 5 förfrågningar till server per sekund för varje Azure-konto. Minska antalet begär Anden per sekund. |
| Ta bort    | 400 | Röst syntes aktiviteten används fortfarande. | Du kan bara ta bort begär Anden som har **slutförts** eller **misslyckats**. |
| GetByID   | 404 | Det går inte att hitta den angivna entiteten. | Kontrol lera att syntes-ID: t är korrekt. |

## <a name="regions-and-endpoints"></a>Regioner och slut punkter

Den långa ljud-API: n är tillgänglig i flera regioner med unika slut punkter.

| Region | Slutpunkt |
|--------|----------|
| Australien, östra | `https://australiaeast.customvoice.api.speech.microsoft.com` |
| Kanada, centrala | `https://canadacentral.customvoice.api.speech.microsoft.com` |
| USA, östra | `https://eastus.customvoice.api.speech.microsoft.com` |
| Indien, centrala | `https://centralindia.customvoice.api.speech.microsoft.com` |
| USA, södra centrala | `https://southcentralus.customvoice.api.speech.microsoft.com` |
| Sydostasien | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| Storbritannien, södra | `https://uksouth.customvoice.api.speech.microsoft.com` |
| Europa, västra | `https://westeurope.customvoice.api.speech.microsoft.com` |
| USA, västra 2 | `https://westus2.customvoice.api.speech.microsoft.com` |

## <a name="audio-output-formats"></a>Format för ljud uppspelning

Vi har stöd för flexibla format för ljud uppspelning. Du kan generera ljud utmatningar per stycke eller sammanfoga ljud utmatningarna till en enda utmatning genom att ange parametern "concatenateResult". Följande format för ljudutdata stöds av den långa ljud-API: et:

> [!NOTE]
> Standard ljud formatet är riff-16khz-bitarsläge-mono-PCM.

* riff-8khz-bitarsläge-mono-PCM
* riff-16khz-bitarsläge-mono-PCM
* riff-24khz-bitarsläge-mono-PCM
* riff-48kHz-bitarsläge-mono-PCM
* Audio-16khz-32kbitrate-mono-MP3
* Audio-16khz-64kbitrate-mono-MP3
* Audio-16khz-128kbitrate-mono-MP3
* Audio-24khz-48kbitrate-mono-MP3
* Audio-24khz-96kbitrate-mono-MP3
* Audio-24khz-160kbitrate-mono-MP3

## <a name="quickstarts"></a>Snabbstarter

Vi erbjuder snabb starter som har utformats för att hjälpa dig att köra den långa ljud-API: n. Den här tabellen innehåller en lista med snabb starter för långa ljud-API ordnade efter språk.

* [Snabb start: python](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>Exempelkod
Exempel kod för långsiktigt ljud-API finns på GitHub.

* [Exempel kod: python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Exempel kod: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Exempel kod: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
