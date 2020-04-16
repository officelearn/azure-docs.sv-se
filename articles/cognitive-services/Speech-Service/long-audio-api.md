---
title: Long Audio API (Preview) - Taltjänst
titleSuffix: Azure Cognitive Services
description: Lär dig hur Long Audio API är utformat för asynkron syntes av långformad text till tal.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: trbye
ms.openlocfilehash: b7cca314ec59e46cf17751b1aec28b5c3ea029ed
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81401059"
---
# <a name="long-audio-api-preview"></a>Långt ljud-API (förhandsgranskning)

Long Audio API är utformat för asynkron syntes av långformad text till tal (till exempel: ljudböcker). Detta API returnerar inte syntetiserat ljud i realtid, istället är förhoppningen att du kommer att söka efter svaren (er) och förbruka utdata (er) som de görs tillgängliga från tjänsten. Till skillnad från texten till tal-API:et som används av Speech SDK kan API:et för långt ljud skapa syntetiserat ljud längre än 10 minuter, vilket gör det idealiskt för utgivare och ljudinnehållsplattformar.

Ytterligare fördelar med Long Audio API:

* Syntetiserat tal som returneras av tjänsten använder neurala röster, vilket säkerställer hifi-ljudutgångar.
* Eftersom svar i realtid inte stöds behöver du inte distribuera en röstslutpunkt.

> [!NOTE]
> Long Audio API stöder nu endast [Custom Neural Voice](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices).

## <a name="workflow"></a>Arbetsflöde

När du använder Long Audio API skickar du vanligtvis en textfil eller filer som ska syntetiseras, avsöka status och sedan om statusen lyckas kan du hämta ljudutdata.

Det här diagrammet ger en översikt på hög nivå över arbetsflödet.

![Arbetsflödesdiagram för långt ljud-API](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Förbered innehåll för syntes

När du förbereder textfilen kontrollerar du att den:

* Är antingen oformaterad text (.txt) eller SSML-text (.txt)
* Kodas som [UTF-8 med byteordermark (BOM)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* Är en enda fil, inte en zip
* Innehåller mer än 400 tecken för oformaterad text eller 400 [fakturerbara tecken](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech#pricing-note) för SSML-text och mindre än 10 000 stycken
  * För oformaterad text avgränsas varje stycke genom att trycka **på Indataexempel för retur** - visa [oformaterad text](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)
  * För SSML-text betraktas varje SSML-del som ett stycke. SSML-delar ska avgränsas med olika stycken - Visa [exempel på SSML-textinmatning](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)
> [!NOTE]
> För kinesiska (fastlandet), kinesiska (Hongkong), kinesiska (Taiwan), japanska och koreanska räknas ett ord som två tecken. 

## <a name="submit-synthesis-requests"></a>Skicka syntesbegäranden

När du har förberett indatainnehållet följer du snabbstarten för [den långa ljudsyntesen](https://aka.ms/long-audio-python) för att skicka begäran. Om du har mer än en indatafil måste du skicka flera begäranden. Det finns vissa begränsningar att vara medveten om: 
* Klienten tillåts skicka upp till 5 begäranden till servern per sekund för varje Azure-prenumerationskonto. Om det överskrider begränsningen får klienten en felkod på 429 (för många begäranden). Minska begäran om belopp per sekund
* Servern tillåts köra och köa upp till 120 begäranden för varje Azure-prenumerationskonto. Om det överskrider begränsningen returnerar servern en felkod på 429 (för många begäranden). Vänta och undvik att skicka in en ny begäran tills vissa begäranden har slutförts
* Servern behåller upp till 20 000 begäranden för varje Azure-prenumerationskonto. Om det överskrider begränsningen, ta bort några förfrågningar innan du skickar in nya

## <a name="audio-output-formats"></a>Format för ljudutdata

Vi stöder flexibla ljudutmatningsformat. Du kan generera ljudutgångar per stycke eller sammanfoga ljuden till en utgång genom att ställa in parametern "concatenateResult". Följande ljudutdataformat stöds av Long Audio API:

> [!NOTE]
> Standardljudformatet är riff-16khz-16bit-mono-pcm.

* riff-8khz-16bit-mono-pcm
* riff-16khz-16bit-mono-pcm
* riff-24khz-16bit-mono-pcm
* riff-48khz-16bit-mono-pcm
* audio-16khz-32kbitrate-mono-mp3
* audio-16khz-64kbitrate-mono-mp3
* audio-16khz-128kbitrate-mono-mp3
* audio-24khz-48kbitrate-mono-mp3
* audio-24khz-96kbitrate-mono-mp3
* audio-24khz-160kbitrate-mono-mp3

## <a name="quickstarts"></a>Snabbstarter

Vi erbjuder snabbstarter som utformats för att hjälpa dig att köra Long Audio API framgångsrikt. Den här tabellen innehåller en lista över snabbstarter för Long Audio API ordnade efter språk.

* [Snabbstart: Python](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>Exempelkod
Exempelkod för Long Audio API är tillgänglig på GitHub.

* [Exempelkod: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Exempelkod: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Exempelkod: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
