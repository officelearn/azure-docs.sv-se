---
title: Förbereda data för tjänsten Custom Speech - Tal
titleSuffix: Azure Cognitive Services
description: När du testar riktigheten i Microsofts taligenkänning eller utbildning av dina anpassade modeller behöver du ljud- och textdata. På den här sidan täcker vi vilka typer av data som används, hur du använder och hanterar dem.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 78857709447f99895c36f23d8760f44f8468ba7c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402131"
---
# <a name="prepare-data-for-custom-speech"></a>Förbereda data för Custom Speech

När du testar riktigheten i Microsofts taligenkänning eller utbildning av dina anpassade modeller behöver du ljud- och textdata. På den här sidan täcker vi vilka typer av data som används, hur du använder och hanterar dem.

## <a name="data-types"></a>Datatyper

I den här tabellen visas godkända datatyper när varje datatyp ska användas och den rekommenderade kvantiteten. Alla datatyper krävs inte för att skapa en modell. Datakraven varierar beroende på om du skapar ett test eller tränar en modell.

| Datatyp | Används för testning | Rekommenderad kvantitet | Används för utbildning | Rekommenderad kvantitet |
|-----------|-----------------|----------|-------------------|----------|
| [Ljud](#audio-data-for-testing) | Ja<br>Används för okulärbesiktning | 5+ ljudfiler | Inga | Ej mycket |
| [Ljud + Mänskligt märkta avskrifter](#audio--human-labeled-transcript-data-for-testingtraining) | Ja<br>Används för att utvärdera noggrannhet | 0,5-5 timmars ljud | Ja | 1-1000 timmars ljud |
| [Relaterad text](#related-text-data-for-training) | Inga | Ej mycket | Ja | 1-200 MB relaterad text |

Filer ska grupperas efter typ i en datauppsättning och överföras som en ZIP-fil. Varje datauppsättning kan bara innehålla en enda datatyp.

> [!TIP]
> Om du snabbt vill komma igång bör du överväga att använda exempeldata. Se den här GitHub-databasen för <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/sampledata/customspeech" target="_target">exempel på anpassade taldata <span class="docon docon-navigate-external x-hidden-focus"></span> </a>

## <a name="upload-data"></a>Ladda upp data

Om du vill ladda upp dina data navigerar du till <a href="https://speech.microsoft.com/customspeech" target="_blank">portalen <span class="docon docon-navigate-external x-hidden-focus"> </span>Anpassat tal </a>. Klicka på **Ladda upp data** från portalen för att starta guiden och skapa din första datauppsättning. Du blir ombedd att välja en taldatatyp för datauppsättningen innan du kan ladda upp dina data.

![Välj ljud från Talportalen](./media/custom-speech/custom-speech-select-audio.png)

Varje datauppsättning som du laddar upp måste uppfylla kraven för den datatyp som du väljer. Dina data måste vara korrekt formaterade innan de laddas upp. Korrekt formaterade data säkerställer att de bearbetas korrekt av tjänsten Custom Speech. Kraven anges i följande avsnitt.

När datauppsättningen har laddats upp har du några alternativ:

* Du kan navigera till fliken **Testning** och inspektera endast ljud eller ljud + data med mänskligt märkt transkription.
* Du kan navigera till fliken **Utbildning** och använda ljud + data för mänsklig transkription eller relaterade textdata för att träna en anpassad modell.

## <a name="audio-data-for-testing"></a>Ljuddata för testning

Ljuddata är optimala för att testa noggrannheten i Microsofts tal-till-text-modell vid baslinjen eller en anpassad modell. Tänk på att ljuddata används för att kontrollera hur exakt tal är när det gäller en viss modells prestanda. Om du vill kvantifiera riktigheten av en modell använder du [ljud + mänskligt märkt transkriptionsdata](#audio--human-labeled-transcript-data-for-testingtraining).

Använd den här tabellen för att se till att dina ljudfiler är korrekt formaterade för användning med anpassat tal:

| Egenskap                 | Värde                 |
|--------------------------|-----------------------|
| Filformat              | RIFF (WAV)            |
| Samplingsfrekvens              | 8 000 Hz eller 16 000 Hz |
| Kanaler                 | 1 (mono)              |
| Maximal längd per ljud | 2 timmar               |
| Exempelformat            | PCM, 16-bitars           |
| Arkivformat           | .zip                  |
| Maximal arkivstorlek     | 2 GB                  |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!TIP]
> När du överför tränings- och testdata får zip-filstorleken inte överstiga 2 GB. Om du behöver mer data för utbildning delar du upp den i flera ZIP-filer och laddar upp dem separat. Senare kan du välja att träna från *flera* datauppsättningar. Du kan dock bara testa från en *enda* datauppsättning.

Använd <a href="http://sox.sourceforge.net" target="_blank" rel="noopener">SoX <span class="docon docon-navigate-external x-hidden-focus"></span> </a> för att verifiera ljudegenskaper eller konvertera befintligt ljud till lämpliga format. Nedan följer några exempel på hur var och en av dessa aktiviteter kan göras via SoX-kommandoraden:

| Aktivitet | Beskrivning | SoX, kommando |
|----------|-------------|-------------|
| Kontrollera ljudformat | Använd det här kommandot för att kontrollera<br>ljudfilsformatet. | `sox --i <filename>` |
| Konvertera ljudformat | Använd det här kommandot för att konvertera<br>ljudfilen till en kanal, 16-bitars, 16 KHz. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Ljud + mänskligt märkta transkriptionsdata för testning/utbildning

Om du vill mäta riktigheten i Microsofts tal-till-text-noggrannhet när du bearbetar ljudfilerna måste du ange utskrifter med mänskligt namn (ord för ord) för jämförelse. Medan mänskligt märkt transkription är ofta tidskrävande, är det nödvändigt att utvärdera noggrannheten och att träna modellen för dina användningsfall. Tänk på att förbättringarna i erkännandet bara kommer att vara lika bra som de data som tillhandahålls. Därför är det viktigt att endast högkvalitativa utskrifter laddas upp.

| Egenskap                 | Värde                               |
|--------------------------|-------------------------------------|
| Filformat              | RIFF (WAV)                          |
| Samplingsfrekvens              | 8 000 Hz eller 16 000 Hz               |
| Kanaler                 | 1 (mono)                            |
| Maximal längd per ljud | 2 timmar (testning) / 60 s (utbildning) |
| Exempelformat            | PCM, 16-bitars                         |
| Arkivformat           | .zip                                |
| Maximal zip-storlek         | 2 GB                                |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!NOTE]
> När du överför tränings- och testdata får zip-filstorleken inte överstiga 2 GB. Du kan bara testa från en *enda* datauppsättning, se till att hålla den inom lämplig filstorlek. Dessutom kan varje träningsfil inte överstiga 60 sekunder annars kommer det att fel ut.

För att ta itu med problem som borttagning av ord eller ersättning krävs en betydande mängd data för att förbättra igenkänningen. I allmänhet rekommenderas att tillhandahålla transkriptioner ord för ord för ungefär 10 till 1 000 timmars ljud. Transkriptioner för alla WAV-filer bör ingå i en enda fil med oformaterad text. Varje rad i transkriptionsfilen ska innehålla namnet på en av ljudfilerna följt av motsvarande transkription. Filnamnet och transkriptionen ska separeras med ett tabbtecken (\t).

  Ett exempel:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```

> [!IMPORTANT]
> Transkriptionen ska kodas som UTF-8-byteordningsmärke (BOM).

Transkriptionerna textnormaliseras så att de kan bearbetas av systemet. Det finns dock några viktiga normaliseringar som måste göras innan du överför data till Talstudion. För rätt språk att använda när du förbereder dina transkriptioner, se [Hur du skapar en mänsklig märkt transkription](how-to-custom-speech-human-labeled-transcriptions.md)

När du har samlat in ljudfiler och motsvarande transkriptioner paketerar du dem som en enda ZIP-fil innan du laddar upp till <a href="https://speech.microsoft.com/customspeech" target="_blank">portalen <span class="docon docon-navigate-external x-hidden-focus"> </span>Anpassat tal </a>. Nedan följer ett exempel på datauppsättning med tre ljudfiler och en mänskligt märkt transkriptionsfil:

> [!div class="mx-imgBorder"]
> ![Välj ljud från Talportalen](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>Relaterade textdata för utbildning

Produktnamn eller funktioner som är unika bör innehålla relaterade textdata för utbildning. Relaterad text bidrar till att säkerställa korrekt igenkänning. Två typer av relaterade textdata kan tillhandahållas för att förbättra igenkänningen:

| Datatyp | Hur dessa data förbättrar igenkänningen |
|-----------|------------------------------------|
| Meningar (yttranden) | Förbättra noggrannheten när du känner igen produktnamn, eller branschspecifikt ordförråd inom ramen för en mening. |
| Uttal | Förbättra uttalet av ovanliga termer, förkortningar, eller andra ord med odefinierade uttal. |

Meningar kan tillhandahållas som en enda textfil eller flera textfiler. Om du vill förbättra noggrannheten använder du textdata som ligger närmare de förväntade talade yttrandena. Uttal bör tillhandahållas som en enda textfil. Allt kan paketeras som en enda zip-fil och laddas upp till <a href="https://speech.microsoft.com/customspeech" target="_blank">Custom Speech portal <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

### <a name="guidelines-to-create-a-sentences-file"></a>Riktlinjer för att skapa en meningsfil

Om du vill skapa en anpassad modell med meningar måste du ange en lista över exempelyttranden. Yttranden behöver _inte_ vara fullständiga eller grammatiskt korrekta, men de måste korrekt återspegla den talade indata du förväntar dig i produktionen. Om du vill att vissa termer ska ha ökad vikt lägger du till flera meningar som innehåller dessa specifika termer.

Som allmän vägledning är modellanpassning mest effektiv när utbildningstexten ligger så nära den verkliga text som förväntas i produktionen. Domänspecifika jargonger och fraser som du riktar dig till för att förbättra bör ingå i utbildningstexten. Försök om möjligt att få en mening eller ett nyckelord styrt på en separat rad. För sökord och fraser som är viktiga för dig (till exempel produktnamn) kan du kopiera dem några gånger. Men kom ihåg, kopiera inte för mycket - det kan påverka den totala igenkänningsfrekvensen.

Använd den här tabellen för att se till att din relaterade datafil för yttranden är korrekt formaterad:

| Egenskap | Värde |
|----------|-------|
| Textkodning | UTF-8 BOM |
| antal yttrande per rad | 1 |
| Maximal filstorlek | 200 MB |

Dessutom vill du ta hänsyn till följande begränsningar:

* Undvik att upprepa tecken mer än fyra gånger. Till exempel: "aaaa" eller "uuuu".
* Använd inte specialtecken eller UTF-8 `U+00A1`tecken ovan .
* Uri:er kommer att avvisas.

### <a name="guidelines-to-create-a-pronunciation-file"></a>Riktlinjer för att skapa en uttalsfil

Om det finns ovanliga termer utan standarduttal som användarna kommer att stöta på eller använda, kan du ange en anpassad uttalsfil för att förbättra igenkänningen.

> [!IMPORTANT]
> Det rekommenderas inte att använda anpassade uttalsfiler för att ändra uttalet av vanliga ord.

Detta inkluderar exempel på ett talat uttryck och ett anpassat uttal för varje:

| Formulär som känns igen/visas | Talad form |
|--------------|--------------------------|
| 3CPO (30) | tre c p o |
| CNTK | c n t k |
| Ieee | jag trefaldigt e |

Den talade formen är den fonetiska sekvensen som stavas. Den kan bestå av bokstav, ord, stavelser eller en kombination av alla tre.

Anpassat uttal finns på engelska`en-US`( )`de-DE`och tyska ( ). I den här tabellen visas tecken som stöds efter språk:

| Språk | Nationell inställning | Tecken |
|----------|--------|------------|
| Svenska | `en-US` | `a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |
| Tyska | `de-DE` | `ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |

Använd följande tabell för att se till att din relaterade datafil för uttal är korrekt formaterad. Uttalsfiler är små, och bör bara vara några kilobyte i storlek.

| Egenskap | Värde |
|----------|-------|
| Textkodning | UTF-8 BOM (ANSI stöds också för engelska) |
| Antal uttal per rad | 1 |
| Maximal filstorlek | 1 MB (1 KB för gratis nivå) |

## <a name="next-steps"></a>Nästa steg

* [Granska dina data](how-to-custom-speech-inspect-data.md)
* [Utvärdera dina data](how-to-custom-speech-evaluate-data.md)
* [Träna din modell](how-to-custom-speech-train-model.md)
* [Distribuera din modell](how-to-custom-speech-deploy-model.md)
