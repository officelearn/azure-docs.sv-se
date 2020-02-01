---
title: Tids krävande ljud-API (för hands version) – tal tjänst
titleSuffix: Azure Cognitive Services
description: 'Lär dig hur den långa ljud-API: n är utformad för asynkron syntes av lång Forms text till tal.'
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: dapine
ms.openlocfilehash: 033103e10971be2f6c220ccdb2c3586c7dc2ef05
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900243"
---
# <a name="long-audio-api-preview"></a>Långt ljud-API (för hands version)

Den långa ljud-API: n är utformad för asynkron syntes av lång Forms text till tal (till exempel: ljud böcker). Det här API: t returnerar inte syntetiskt ljud i real tid, i stället är det förväntat att du ska söka efter svar och använda de utdata som de görs tillgängliga från tjänsten. Till skillnad från text till tal-API som används av talet SDK, kan det långa ljud-API: et skapa syntetiskt ljud som är längre än 10 minuter, vilket gör det perfekt för utgivare och ljud innehålls plattformar.

Ytterligare fördelar med den långa ljud-API: et:

* Syntetiskt tal som returneras av tjänsten använder neurala-röster, som säkerställer ljud uppspelning med hög åter givning.
* Eftersom real tids svar inte stöds behöver du inte distribuera en röst slut punkt.

> [!NOTE]
> API för långa ljud stöder nu bara [anpassad neurala röst](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices).

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
> För kinesiska (fast), kinesiska (Hongkong), kinesiska (Taiwan), japanska och koreanska, räknas ett ord som två tecken. 

## <a name="submit-synthesis-requests"></a>Skicka syntes begär Anden

När du har bearbetat indata-innehållet följer du snabb starten för det [långa form ljud syntesen](https://aka.ms/long-audio-python) för att skicka begäran. Om du har fler än en indatafil måste du skicka flera begär Anden. Det finns vissa begränsningar som du bör känna till: 
* Klienten får skicka upp till 5 förfrågningar till server per sekund för varje Azure-prenumerations konto. Om den överskrider begränsningen får klienten en 429-felkod (för många begär Anden). Minska antalet begär Anden per sekund
* Servern kan köra och köa upp till 120 förfrågningar för varje Azure-prenumerations konto. Om den överskrider begränsningen returnerar servern en 429-felkod (för många begär Anden). Vänta och Undvik att skicka ny begäran förrän vissa begär Anden har slutförts
* Servern kommer att behålla upp till 20 000 förfrågningar för varje Azure-prenumerations konto. Om det överskrider begränsningen tar du bort några begär Anden innan du skickar nya

## <a name="audio-output-formats"></a>Format för ljud uppspelning

Vi har stöd för flexibla format för ljud uppspelning. Du kan generera ljud utmatningar per stycke eller slå ihop ljuden till en utmatning genom att ange parametern "concatenateResult". Följande format för ljudutdata stöds av den långa ljud-API: et:

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
* [Exempel kod:C#](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Exempel kod: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
