---
title: Förbereda testdata för anpassat tal – Speech Services
titlesuffix: Azure Cognitive Services
description: Om du testar om du vill se hur exakt Microsoft taligenkänning är eller utbildning egna modeller och du behöver data (i form av ljud och/eller text). Vi beskriver vilka typer av data, hur de används och hur du hanterar dem på den här sidan.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 1e689d7ce65fda43e5657383ed44890c90c095cd
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025889"
---
# <a name="prepare-data-for-custom-speech"></a>Förbereda data för anpassat tal

Om du testar om du vill se hur exakt Microsoft taligenkänning är eller utbildning egna modeller och du behöver data i form av ljud och text. Vi beskriver vilka typer av data, hur de används och hur du hanterar dem på den här sidan.

## <a name="data-types"></a>Datatyper

Den här tabellen visar en lista över godkända datatyper, när varje datatyp som ska användas och den rekommendera mängden. Inte alla datatypen krävs för att skapa en modell. Data-kraven varierar beroende på om du skapar ett test eller träna en modell.

| Datatyp | Används för testning | Kvantitet | Används för utbildning | Kvantitet |
|-----------|-----------------|----------|-------------------|----------|
| [Ljud](#audio-data-for-testing) | Ja<br>Används för granskning | 5 + ljudfiler | Nej | Ej tillämpligt |
| [Ljud + mänskliga etikett avskrifter](#audio--human-labeled-transcript-data-for-testingtraining) | Ja<br>Används för att utvärdera noggrannhet | 0,5 – 5 timmar med ljud | Ja | 1 – 1 000 timmar med ljud |
| [Relaterade text](##related-text-data-for-training) | Nej | Ej tillämpligt | Ja | 1 200 MB relaterade text |

Filer bör grupperade efter typ till en datauppsättning och laddas upp som en zip-fil. Varje datamängd kan bara innehålla en enda datatyp.

## <a name="upload-data"></a>Ladda upp data

När du är redo att ladda upp data, klickar du på **ladda upp data** att starta guiden och skapa din första datauppsättning. Du kommer att uppmanas att välja en tal-datatyp för din datauppsättning innan så att du kan ladda upp data.

![Välj ljud från portalen tal](./media/custom-speech/custom-speech-select-audio.png)

Varje datauppsättning som du överför måste uppfylla kraven för den datatyp som du väljer. Det är viktigt att korrekt formatera dina data innan det överförs. Detta garanterar att data bearbetas korrekt av Custom Speech-tjänsten. Kraven finns i följande avsnitt.

När din datauppsättning har överförts, har du några alternativ:

* Du kan navigera till den **testning** fliken och visuellt inspektera endast ljud eller ljud + mänskliga etikett avskrift data.
* Du kan navigera till den **utbildning** fliken och oss ljud + mänskliga avskrift data eller relaterade textdata för att träna en anpassad modell.

## <a name="audio-data-for-testing"></a>Ljuddata för testning

Ljuddata är optimalt för att testa det arbete du utfört Microsofts baslinje tal till text modell eller en anpassad modell. Tänk på, ljuddata används för att kontrollera korrektheten i tal för en viss modell prestanda. Om du vill kvantifiera korrektheten i en modell, använda [ljud + mänskliga etikett avskrift data](#audio--human-labeled-transcript-data-for-testingtraining).

Använd den här tabellen för att säkerställa att dina ljudfiler formateras på rätt sätt för användning med anpassat tal:

| Egenskap  | Värde |
|----------|-------|
| Filformat | RIFF (WAV) |
| Samplingshastighet | 8 000 eller 16 000 Hz |
| Kanaler | 1 (mono) |
| Maximal längd per ljud | 2 timmar |
| Exempelformat | PCM, 16-bitars |
| Arkivformat | .zip |
| Maximal arkivstorlek | 2 GB |

Om din ljud inte uppfyller de här egenskaperna eller om du vill kontrollera om den gör, vi rekommenderar att ladda ned [sox](http://sox.sourceforge.net) att kontrollera eller konvertera ljudet. Nedan visas några exempel på hur var och en av dessa aktiviteter kan göras via kommandoraden:

| Aktivitet | Beskrivning | SOx kommando |
|----------|-------------|-------------|
| Kontrollera ljudformatet | Du kan använda det här kommandot för att kontrollera formatet ljud. | `soxi <filename>.wav` |
| Konvertera ljudformatet | Använd kommandot för att konvertera filen till en kanal, 16-bitars 48 KHz. | `sox <filename>.wav -b 16 -3 signed-integer -c l -r 48k -t wav <filename>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Ljud + mänskliga etikett avskrift data för testning/utbildning

För att mäta korrektheten i Microsofts tal till text precision vid bearbetning av din ljudfiler, måste du ange mänskliga etikett avskrifter (ord för ord) för jämförelse. Human etikett avskrift är ofta tidskrävande, är det nödvändigt att utvärdera Precision och för att träna modellen för ditt användningsfall. Tänk på, förbättringarna i Taligenkänning kommer bara att lika bra som tillhandahålls. Därför är det viktigt att endast högkvalitativa avskrifter överförs.  

| Egenskap  | Värde |
|----------|-------|
| Filformat | RIFF (WAV) |
| Samplingshastighet | 8 000 eller 16 000 Hz |
| Kanaler | 1 (mono) |
| Maximal längd per ljud | 60 s |
| Exempelformat | PCM, 16-bitars |
| Arkivformat | .zip |
| Maximal zip-storlek | 2 GB |

Att åtgärda problem med som word borttagning eller ersättningen, en betydande mängd data krävs för att förbättra taligenkänning. I allmänhet rekommenderar vi för att tillhandahålla ord för ord avskrifter för ungefär 10 och 1 000 timmar med ljud. Transkriptioner för alla WAV-filer bör ingå i en enda fil med oformaterad text. Varje rad i transkriptionsfilen ska innehålla namnet på en av ljudfilerna följt av motsvarande transkription. Filnamnet och transkriptionen ska separeras med ett tabbtecken (\t).

  Exempel:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```
> [!NOTE]
> Transkriptionen ska kodas som UTF-8-byteordningsmärke (BOM).

Transkriptionerna textnormaliseras så att de kan bearbetas av systemet. Det finns dock vissa viktiga normaliseringar som måste utföras av användaren _innan_ data laddas upp till Custom Speech Service. Lämplig språket som ska användas när du förbereder din avskrifter, se [så här skapar du en etikett mänskliga avskrift](how-to-custom-speech-human-labeled-transcriptions.md)

När du har samlat in dina ljudfiler och motsvarande avskrifter, bör de paketerad som en enda .zip-fil innan du laddar upp till portalen för anpassat tal. Det här är en exempel-datauppsättning med tre ljudfiler och en etikett mänskliga avskrift-fil:

![Välj ljud från portalen tal](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>Relaterade textdata för träning

Om du har produktnamn eller funktioner som är unika och du vill kontrollera att de identifieras korrekt, är det viktigt att inkludera relaterade textdata för utbildning. Två typer av relaterade textdata kan anges för att förbättra taligenkänning:

| Datatyp | Hur dessa data förbättrar taligenkänning |
|-----------|------------------------------------|
| Yttranden och/eller meningar | Dessa kan förbättra noggrannheten vid tolkning av produktnamn eller branschspecifika ordförråd inom ramen för en mening. |
| Uttal | Dessa kan förbättra uttal för ovanliga termer eller förkortningar andra ord med odefinierat uttal. |

Yttranden kan anges som en eller flera textfiler. Ju närmare text data är att det ska vara sägs, desto större sannolikhet att bättre noggrannhet. Uttal bör anges som en enda textfil. Allt kan vara paketerad som en enda zip-fil och laddas upp till portalen för anpassat tal.

### <a name="guidelines-to-create-an-utterances-file"></a>Riktlinjer för att skapa en yttranden-fil

Om du vill skapa en anpassad modell med relaterade text, måste du ange en lista över exemplet yttranden. Dessa yttranden behöver inte fullständiga meningar eller korrigera grammatiskt, men de måste korrekt speglar talat indata som du förväntar dig i produktion. Om du vill att vissa villkor ha ökat vikt, kan du lägga till flera meningar relaterade datafilen som innehåller dessa specifika villkor.

Använd den här tabellen för att se till att datafilen relaterade för yttranden har formaterats korrekt:

| Egenskap  | Värde |
|----------|-------|
| Textkodning | UTF-8 BOM |
| antal yttrande per rad | 1 |
| Maximal filstorlek | 200 MB |

Dessutom bör du hänsyn till följande begränsningar:

* Undvika upprepade tecken mer än fyra gånger. Till exempel: ”aaaa” eller ”uuuu”.
* Använd inte specialtecken eller UTF-8 tecken utöver U + 00A1.
* URI: er kommer att avvisas.

### <a name="guidelines-to-create-a-pronunciation-file"></a>Riktlinjer för att skapa en uttal av fil

Om det är ovanligt villkor utan standard uttal som användarna stöter på eller använda kan ange du en anpassad uttal-fil för att förbättra taligenkänning.

> [!IMPORTANT]
> Du bör inte använda den här funktionen för att ändra uttal för vanliga ord.

Det innehåller exempel på en talat uttryck och en anpassad uttal för var och en:

| Talat formulär | Identifieras/visas formuläret |
|--------------|--------------------------|
| tre c-p-o | 3CPO |  
| s n t k | CNTK |
| i tredubbla e | IEE |

Formuläret talat är fonetiska sekvensen med bokstäver. Det kan bestå av bokstäver, ord, stavelser eller en kombination av alla tre.

Anpassade uttal är tillgänglig på engelska (en-US) och tyska (de-DE). Den här tabellen visar tecken som stöds av språk:

| Språk | Nationell inställning | Karaktärer |
|----------|--------|------------|
| Svenska | en-US | a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, frågor och, r, s, t, u, v, w, x, y, z |
| Tyska | de-DE | ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |

Använd den här tabellen för att säkerställa att din relaterade datafil för uttal har formaterats korrekt. Uttal av filer är små och får inte överstiga några KB-artiklar.

| Egenskap  | Värde |
|----------|-------|
| Textkodning | UTF-8 BOM (ANSI stöds också för engelska) |
| Antal uttal per rad | 1 |
| Maximal filstorlek | 1 MB (1 KB för den kostnadsfria nivån) |

## <a name="next-steps"></a>Nästa steg

* [Granska dina data](how-to-custom-speech-inspect-data.md)
* [Utvärdera dina data](how-to-custom-speech-evaluate-data.md)
* [Träna din modell](how-to-custom-speech-train-model.md)
* [Distribuera din modell](how-to-custom-speech-deploy-model.md)
