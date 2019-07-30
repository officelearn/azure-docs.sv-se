---
title: Förbered test data för Custom Speech Speech service
titleSuffix: Azure Cognitive Services
description: Oavsett om du testar för att se hur korrekt Microsoft tal igenkänning är eller tränar dina egna modeller, behöver du data (i form av ljud och/eller text). På den här sidan tar vi upp typer av data, hur de används och hur de hanteras.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: fad0fada4d9dd888b0b2a37b59e4eac1e016aec4
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663587"
---
# <a name="prepare-data-for-custom-speech"></a>Förbereda data för Custom Speech

Oavsett om du testar för att se hur korrekt Microsoft tal igenkänning är eller tränar dina egna modeller, behöver du data i form av ljud och text. På den här sidan tar vi upp typer av data, hur de används och hur de hanteras.

## <a name="data-types"></a>Datatyper

I den här tabellen listas godkända data typer, när varje datatyp ska användas och den rekommenderade kvantiteten. Det krävs inte alla data typer för att skapa en modell. Data kraven varierar beroende på om du skapar ett test eller tränar en modell.

| Datatyp | Används för testning | Antal | Används för utbildning | Antal |
|-----------|-----------------|----------|-------------------|----------|
| [Ljud](#audio-data-for-testing) | Ja<br>Används för visuell granskning | 5 + ljudfiler | Nej | Ej tillämpligt |
| [Ljud + medmärkta avskrifter](#audio--human-labeled-transcript-data-for-testingtraining) | Ja<br>Används för att utvärdera noggrannhet | 0,5 – 5 timmars ljud | Ja | 1 – 1 000 timmars ljud |
| [Relaterad text](#related-text-data-for-training) | Nej | Ej tillämpligt | Ja | 1-200 MB relaterad text |

Filerna ska grupperas efter typ i en data uppsättning och laddas upp som en zip-fil. Varje data uppsättning får bara innehålla en enda datatyp.

## <a name="upload-data"></a>Ladda upp data

När du är redo att ladda upp dina data klickar du på **överför data** för att starta guiden och skapa din första data uppsättning. Du uppmanas att välja en tal data typ för din data uppsättning innan du tillåter att du överför dina data.

![Välj ljud från tal portalen](./media/custom-speech/custom-speech-select-audio.png)

Varje data uppsättning som du överför måste uppfylla kraven för den datatyp som du väljer. Det är viktigt att du formaterar dina data korrekt innan de överförs. Detta säkerställer att data bearbetas korrekt av tjänsten Custom Speech. Kraven visas i följande avsnitt.

När din data uppsättning har laddats upp har du några alternativ:

* Du kan navigera till fliken **test** och visuellt inspektera enbart ljud eller ljud + mänskligt avskrifts data.
* Du kan navigera till fliken **utbildning** och använda ljud-och färg SKRIFTS data eller relaterade text data för att träna en anpassad modell.

## <a name="audio-data-for-testing"></a>Ljud data för testning

Ljuddata är optimala för att testa noggrannheten hos Microsofts bas linje tal-till-text-modell eller en anpassad modell. Kom ihåg att ljuddata används för att kontrol lera precisionen för tal med avseende på en speciell modells prestanda. Om du vill kvantifiera precisionen för en modell använder du [ljud + mänskligt avskrifts data](#audio--human-labeled-transcript-data-for-testingtraining).

Använd den här tabellen för att se till att ljudfilerna är korrekt formaterade för användning med Custom Speech:

| Egenskap | Värde |
|----------|-------|
| Fil format | RIFF (WAV) |
| Samplingshastighet | 8 000 Hz eller 16 000 Hz |
| Kanaler | 1 (mono) |
| Maximal längd per ljud | 2 timmar |
| Exempel format | PCM, 16-bitars |
| Arkiv format | .zip |
| Maximal Arkiv storlek | 2 GB |

Om ljudet inte uppfyller dessa egenskaper eller om du vill kontrol lera om det gör det rekommenderar vi att du hämtar [SOX](http://sox.sourceforge.net) för att kontrol lera eller konvertera ljudet. Nedan visas några exempel på hur var och en av dessa aktiviteter kan göras via kommando raden:

| Aktivitet | Beskrivning | SOX-kommando |
|----------|-------------|-------------|
| Kontrol lera ljud formatet | Använd det här kommandot för att kontrol lera ljud fil formatet. | `sox --i <filename>` |
| Konvertera ljud format | Använd det här kommandot för att konvertera ljud filen till en kanal, 16-bitars 16 KHz. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Ljud + mänskligt avskrifts data för testning/utbildning

För att mäta noggrannheten hos Microsofts tal-till-text-precision vid bearbetning av ljudfiler, måste du tillhandahålla medmärkta avskrifter (ord för ord) för jämförelse. Även om det ofta finns tids krävande avskrifter, är det nödvändigt att utvärdera precisionen och träna modellen för dina användnings fall. Kom ihåg att förbättringarna i igenkänningen bara är lika lämpliga som de data som tillhandahålls. Därför är det viktigt att endast avskrifter av hög kvalitet överförs.  

| Egenskap | Value |
|----------|-------|
| Fil format | RIFF (WAV) |
| Samplingshastighet | 8 000 Hz eller 16 000 Hz |
| Kanaler | 1 (mono) |
| Maximal längd per ljud | 60 s |
| Exempel format | PCM, 16-bitars |
| Arkiv format | .zip |
| Maximal zip-storlek | 2 GB |

För att lösa problem som Word-borttagning eller ersättning krävs en stor mängd data för att förbättra igenkänningen. I allmänhet rekommenderar vi att du ger ord för ord-avskrifter i ungefär 10 till 1 000 timmar av ljud. Transkriptioner för alla WAV-filer bör ingå i en enda fil med oformaterad text. Varje rad i transkriptionsfilen ska innehålla namnet på en av ljudfilerna följt av motsvarande transkription. Filnamnet och transkriptionen ska separeras med ett tabbtecken (\t).

  Exempel:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```
> [!NOTE]
> Transkriptionen ska kodas som UTF-8-byteordningsmärke (BOM).

Transkriptionerna textnormaliseras så att de kan bearbetas av systemet. Det finns dock vissa viktiga normaliseringar som måste utföras av användaren _innan_ data laddas upp till Custom Speech Service. För det språk som ska användas när du förbereder dina avskrifter, se [så här skapar du en](how-to-custom-speech-human-labeled-transcriptions.md) medhjälpad avskrift

När du har samlat in dina ljudfiler och motsvarande avskrifter bör de paketeras som en enda zip-fil innan de överförs till Custom Speech Portal. Detta är ett exempel på en data uppsättning med tre ljudfiler och en fil med mänsklig märkning:

![Välj ljud från tal portalen](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>Relaterade text data för utbildning

Om du har produkt namn eller funktioner som är unika och vill se till att de identifieras korrekt, är det viktigt att inkludera relaterade text data för utbildning. Två typer av relaterade text data kan tillhandahållas för att förbättra igenkänningen:

| Datatyp | Hur dessa data förbättrar igenkänningen |
|-----------|------------------------------------|
| Yttranden och/eller meningar | Dessa kan förbättra precisionen vid igenkänning av produkt namn eller branschspecifika ord listor inom ramen för en mening. |
| Uttal | Dessa kan förbättra uttal av ovanliga termer, akronymer eller andra ord med odefinierade uttal. |

Yttranden kan anges som en enda eller flera textfiler. Mer exakta text data är att talas, desto större sannolikhet att noggrannheten förbättras. Uttal ska anges som en enskild textfil. Allt kan paketeras som en enda zip-fil och överföras till Custom Speech Portal.

### <a name="guidelines-to-create-an-utterances-file"></a>Rikt linjer för att skapa en yttranden-fil

Om du vill skapa en anpassad modell med relaterad text måste du ange en lista över yttranden. Dessa yttranden behöver inte vara fullständiga meningar eller grammatiskt korrekt, men de måste avspegla den talade ingången som du förväntar dig i produktionen. Om du vill att vissa villkor har ökat vikt kan du lägga till flera meningar till din relaterade datafil som innehåller dessa specifika villkor.

Använd den här tabellen för att se till att din relaterade datafil för yttranden är korrekt formaterad:

| Egenskap | Value |
|----------|-------|
| Textkodning | UTF-8 BOM |
| antal yttrande per rad | 1 |
| Maximal filstorlek | 200 MB |

Dessutom bör du ta hänsyn till följande begränsningar:

* Undvik upprepade tecken mer än fyra gånger. Till exempel: "AAAA" eller "uuuu".
* Använd inte specialtecken eller UTF-8-tecken över U + 00A1.
* URI: er kommer att avvisas.

### <a name="guidelines-to-create-a-pronunciation-file"></a>Rikt linjer för att skapa en uttal-fil

Om det finns ovanliga termer utan standard uttal som användarna kommer att stöta på eller använder, kan du ange en anpassad uttal-fil för att förbättra igenkänningen.

> [!IMPORTANT]
> Vi rekommenderar inte att du använder den här funktionen för att ändra uttal av vanliga ord.

Detta inkluderar exempel på en talade uttryck och ett anpassat uttal för var och en:

| Identifierat/visat formulär | Talat formulär |
|--------------|--------------------------|
| 3CPO | tre c p o |  
| CNTK | c n t k |
| STANDARDEN | i tredubbel e |

Det talade formuläret är den fonetiska sekvensen som har stavats ut. Det kan bestå av bokstäver, ord, stavelser eller en kombination av alla tre.

Anpassat uttal är tillgängligt på engelska (en-US) och tyska (de-DE). I den här tabellen visas tecken som stöds efter språk:

| Språk | Nationell inställning | Karaktärer |
|----------|--------|------------|
| Svenska | en-US | a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |
| Tyska | de-DE | ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, b, x, y, z |

Använd den här tabellen för att kontrol lera att din relaterade datafil för uttal är korrekt formaterad. Uttal-filerna är små och bör inte överstiga några få KB.

| Egenskap | Value |
|----------|-------|
| Textkodning | UTF-8-struktur (ANSI stöds också för engelska) |
| antal uttal per rad | 1 |
| Maximal filstorlek | 1 MB (1 KB ledig nivå) |

## <a name="next-steps"></a>Nästa steg

* [Inspektera dina data](how-to-custom-speech-inspect-data.md)
* [Utvärdera dina data](how-to-custom-speech-evaluate-data.md)
* [Träna din modell](how-to-custom-speech-train-model.md)
* [Distribuera din modell](how-to-custom-speech-deploy-model.md)
