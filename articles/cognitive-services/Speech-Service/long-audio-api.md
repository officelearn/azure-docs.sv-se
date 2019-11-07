---
title: Tids krävande ljud-API (för hands version) – tal tjänst
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/26/2019
ms.author: erhopf
ms.openlocfilehash: 391cddbbd1b69fb7cb5422adbaea2f3378e273bf
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580165"
---
# <a name="long-audio-api-preview"></a>Långt ljud-API (för hands version)

Den långa ljud-API: n är utformad för asynkron syntes av lång Forms text till tal (till exempel: ljud böcker). Det här API: t returnerar inte syntetiskt ljud i real tid, i stället är det förväntat att du ska söka efter svar och använda de utdata som de görs tillgängliga från tjänsten. Till skillnad från text till tal-API som används av talet SDK, kan det långa ljud-API: et skapa syntetiskt ljud som är längre än 10 minuter, vilket gör det perfekt för utgivare och ljud innehålls plattformar.

Ytterligare fördelar med den långa ljud-API: et:

* Syntetiskt tal som returneras av tjänsten använder neurala-röster, som säkerställer ljud uppspelning med hög åter givning.
* Eftersom real tids svar inte stöds behöver du inte distribuera en röst slut punkt.

## <a name="workflow"></a>Arbetsflöde

När du använder det långa ljud-API: et skickar du normalt en textfil eller filer som ska syntetiseras, avsöker efter status. om statusen är klar kan du ladda ned ljud uppspelningen.

Det här diagrammet innehåller en översikt över arbets flödet.

![Arbets flödes diagram för långt ljud-API](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Förbered innehåll för syntes

När du förbereder text filen måste du se till att den:

* Är antingen oformaterad text (. txt) eller SSML text (. txt)
  * För oformaterad text separeras varje stycke genom att trycka på **RETUR/retur** -Visa [text ingångs exempel](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)
  * För SSML text betraktas varje SSML-enhet som ett stycke. SSML bitar separeras av olika stycken – Visa [SSML text ingångs exempel](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt). För språk kod, se [tal syntes märknings språk (SSML)](speech-synthesis-markup.md)
* Kodas som [UTF-8 med byte ordnings tecken (BOM)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* Innehåller fler än 10 000 tecken eller mer än 50 stycken
* Är en enskild fil, inte ett zip

## <a name="audio-output-formats"></a>Format för ljud uppspelning

Följande format för ljudutdata stöds av den långa ljud-API: et:

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
