---
title: Så här förbereder du data för tjänsten Custom Voice - Speech
titleSuffix: Azure Cognitive Services
description: Skapa en anpassad röst för ditt varumärke med taltjänsten. Du tillhandahåller studioinspelningar och tillhörande skript, tjänsten genererar en unik röstmodell inställd på den inspelade rösten. Använd den här rösten för att syntetisera tal i dina produkter, verktyg och program.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: 5427e9f996fb77d455aa8064fc7cb1c65e1fcf7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74805985"
---
# <a name="prepare-data-to-create-a-custom-voice"></a>Förbereda data för att skapa en anpassad röst

När du är redo att skapa en anpassad text-till-tal-röst för ditt program är det första steget att samla in ljudinspelningar och tillhörande skript för att börja träna röstmodellen. Taltjänsten använder dessa data för att skapa en unik röst inställd för att matcha rösten i inspelningarna. När du har tränat rösten kan du börja syntetisera tal i dina program.

Du kan börja med en liten mängd data för att skapa ett konceptbevis. Men ju mer data du tillhandahåller, desto mer naturligt kommer din anpassade röst att låta. Innan du kan träna din egen text-till-tal-röstmodell behöver du ljudinspelningar och tillhörande textavskrifter. På den här sidan granskar vi datatyper, hur de används och hur du hanterar var och en.

## <a name="data-types"></a>Datatyper

En röstutbildningsdatauppsättning innehåller ljudinspelningar och en textfil med tillhörande transkriptioner. Varje ljudfil ska innehålla ett enda uttryck (en enda mening eller en enda sväng för ett dialogsystem) och vara mindre än 15 sekunder lång.

I vissa fall kanske du inte har rätt datauppsättning redo och kommer att vilja testa den anpassade röstutbildningen med tillgängliga ljudfiler, korta eller långa, med eller utan utskrifter. Vi tillhandahåller verktyg (beta) som hjälper dig att segmentera ditt ljud i yttranden och förbereda avskrifter med hjälp av [Batch Transkription API](batch-transcription.md).

I den här tabellen visas datatyper och hur var och en används för att skapa en anpassad röstmodell för text till tal.

| Datatyp | Beskrivning | När du ska använda detta | Ytterligare service krävs | Kvantitet för utbildning av en modell | Språk(er) |
| --------- | ----------- | ----------- | --------------------------- | ----------------------------- | --------- |
| **Individuella yttranden + matchande avskrift** | En samling (.zip) med ljudfiler (.wav) som enskilda yttranden. Varje ljudfil bör vara 15 sekunder eller mindre lång, parat med en formaterad avskrift (.txt). | Professionella inspelningar med matchande avskrifter | Redo för träning. | Inga hårda krav för en-USA och zh-CN. Mer än 2 000+ olika yttranden för andra språk. | [Alla anpassade röstinst.på](language-support.md#customization) |
| **Långt ljud + avskrift (beta)** | En samling (.zip) med långa, osegmenterade ljudfiler (längre än 20 sekunder), parat med en avskrift (.txt) som innehåller alla talade ord. | Du har ljudfiler och matchande avskrifter, men de är inte segmenterade i yttranden. | Segmentering (med hjälp av batchranskription).<br>Ljudformat omvandling där det behövs. | Inga hårda krav  | [Alla anpassade röstinst.på](language-support.md#customization) |
| **Endast ljud (beta)** | En samling (.zip) av ljudfiler utan en utskrift. | Du har bara ljudfiler tillgängliga, utan utskrifter. | Segmentering + transkriptionsgenerering (med hjälp av batchranskription).<br>Ljudformat omvandling där det behövs.| Inga hårda krav | [Alla anpassade röstinst.på](language-support.md#customization) |

Filer ska grupperas efter typ i en datauppsättning och laddas upp som en zip-fil. Varje datauppsättning kan bara innehålla en enda datatyp.

> [!NOTE]
> Det maximala antalet datauppsättningar som får importeras per prenumeration är 10 .zip-filer för kostnadsfria prenumerationsanvändare (F0) och 500 för S0-användare (Standard subscription).

## <a name="individual-utterances--matching-transcript"></a>Individuella yttranden + matchande avskrift

Du kan förbereda inspelningar av enskilda yttranden och den matchande avskriften på två sätt. Antingen skriva ett skript och få den läst av en röst talang eller använda offentligt tillgängliga ljud och transkribera den till text. Om du gör det senare, redigera disfluenser från ljudfiler, till exempel "um" och andra fyllmedel ljud, hakar, mumlade ord eller feluttal.

Om du vill skapa ett bra röstteckensnitt skapar du inspelningarna i ett tyst rum med en mikrofon av hög kvalitet. Konsekvent volym, talfrekvens, talhöjd och uttrycksfulla manér av tal är viktiga.

> [!TIP]
> För att skapa en röst för produktionsbruk rekommenderar vi att du använder en professionell inspelningsstudio och rösttalang. Mer information finns i [Så här spelar du in röstexempel för en anpassad röst](record-custom-voice-samples.md).

### <a name="audio-files"></a>Ljudfiler

Varje ljudfil ska innehålla ett enda uttryck (en enda mening eller en enda sväng i ett dialogsystem), mindre än 15 sekunder långt. Alla filer måste vara på samma talade språk. Flerspråkiga anpassade text-till-tal-röster stöds inte, med undantag för den kinesisk-engelska tvåspråkiga. Varje ljudfil måste ha ett unikt numeriskt filnamn med filnamnstillägget .wav.

Följ dessa riktlinjer när du förbereder ljud.

| Egenskap | Värde |
| -------- | ----- |
| Filformat | RIFF (.wav), grupperat i en ZIP-fil |
| Samplingsfrekvens | Minst 16 000 Hz |
| Exempelformat | PCM, 16-bitars |
| Filnamn | Numerisk, med .wav-tillägget. Inga dubblettfilnamn tillåts. |
| Ljudlängd | Kortare än 15 sekunder |
| Arkivformat | .zip |
| Maximal arkivstorlek | 2048 MB |

> [!NOTE]
> WAV-filer med en samplingsfrekvens som är lägre än 16 000 Hz kommer att avvisas. Om en ZIP-fil innehåller WAV-filer med olika samplingsfrekvens importeras endast de som är lika med eller högre än 16 000 Hz. Portalen importerar för närvarande .zip-arkiv upp till 200 MB. Flera arkiv kan dock laddas upp.

### <a name="transcripts"></a>Utskrifter

Transkriptionsfilen är en oformaterad textfil. Använd dessa riktlinjer för att förbereda dina transkriptioner.

| Egenskap | Värde |
| -------- | ----- |
| Filformat | Oformaterad text (.txt) |
| Kodningsformat | ANSI/ASCII, UTF-8, UTF-8-BOM, UTF-16-LE eller UTF-16-BE. För zh-CN stöds inte ANSI/ASCII- och UTF-8-kodningar. |
| antal yttrande per rad | **En** - Varje rad i transkriptionsfilen ska innehålla namnet på en av ljudfilerna, följt av motsvarande transkription. Filnamnet och transkriptionen ska separeras med ett tabbtecken (\t). |
| Maximal filstorlek | 2048 MB |

Nedan följer ett exempel på hur transkriptionerna är ordnade yttrande genom yttrande i en .txt-fil:

```
0000000001[tab] This is the waistline, and it's falling.
0000000002[tab] We have trouble scoring.
0000000003[tab] It was Janet Maslin.
```
Det är viktigt att transkriptionerna är 100% korrekta transkriptioner av motsvarande ljud. Fel i utskrifterna kommer att införa kvalitetsförlust under utbildningen.

> [!TIP]
> När du skapar text-till-tal-röster för produktion väljer du yttranden (eller skrivskript) som tar hänsyn till både fonetisk täckning och effektivitet. Har du problem med att få de resultat du vill ha? [Kontakta Custom Voice-teamet](mailto:speechsupport@microsoft.com) för att ta reda på mer om hur vi kan konsultera.

## <a name="long-audio--transcript-beta"></a>Långt ljud + avskrift (beta)

I vissa fall kanske du inte har segmenterat ljud tillgängligt. Vi tillhandahåller en tjänst (beta) via den anpassade röstportalen som hjälper dig att segmentera långa ljudfiler och skapa transkriptioner. Tänk på att den här tjänsten debiteras mot din tal-till-text-prenumerationsanvändning.

> [!NOTE]
> Tjänsten för segmentering av långa ljud utnyttjar batch-transkriptionsfunktionen i tal-till-text, som endast stöder S0-användare (Standard subscription). Under bearbetningen av segmenteringen skickas även dina ljudfiler och transkriptioner till tjänsten Custom Speech för att förfina igenkänningsmodellen så att noggrannheten kan förbättras för dina data. Inga data kommer att lagras under den här processen. När segmenteringen är klar lagras endast yttranden som segmenterats och deras mappningsavskrifter för nedladdning och utbildning.

### <a name="audio-files"></a>Ljudfiler

Följ dessa riktlinjer när du förbereder ljud för segmentering.

| Egenskap | Värde |
| -------- | ----- |
| Filformat | RIFF (.wav) med en samplingsfrekvens på minst 16 khz-16-bitars i PCM eller .mp3 med en bithastighet på minst 256 KBps, grupperad i en ZIP-fil |
| Filnamn | ASCII- och Unicode-tecken stöds. Inga dubblettnamn tillåts. |
| Ljudlängd | Längre än 20 sekunder |
| Arkivformat | .zip |
| Maximal arkivstorlek | 2048 MB |

Alla ljudfiler ska grupperas i en zip-fil. Det är OK att sätta .wav-filer och .mp3-filer i en ljud zip. Du kan till exempel ladda upp en zip-fil som innehåller en ljudfil med namnet "kingstory.wav", 45 sekunder lång och ett annat ljud med namnet "queenstory.mp3", 200 sekunder lång. Alla .mp3-filer omvandlas till WAV-formatet efter bearbetningen.

### <a name="transcripts"></a>Utskrifter

Avskrifter måste vara beredda att de specifikationer som anges i denna tabell. Varje ljudfil måste matchas med en utskrift.

| Egenskap | Värde |
| -------- | ----- |
| Filformat | Oformaterad text (.txt), grupperad i en .zip |
| Filnamn | Använda samma namn som den matchande ljudfilen |
| Kodningsformat | ENDAST UTF-8-STRUKTUR |
| antal yttrande per rad | Obegränsad |
| Maximal filstorlek | 2048 MB |

Alla transkriptionsfiler i den här datatypen ska grupperas i en zip-fil. Till exempel har du laddat upp en zip-fil som innehåller en ljudfil med namnet "kingstory.wav", 45 sekunder lång, och en annan som heter "queenstory.mp3", 200 sekunder lång. Du måste ladda upp en annan zip-fil som innehåller två avskrifter, en som heter "kingstory.txt", den andra en "queenstory.txt". I varje oformaterad textfil kommer du att tillhandahålla den fullständiga korrekta transkriptionen för det matchande ljudet.

När din datauppsättning har laddats upp hjälper vi dig att segmentera ljudfilen i yttranden baserat på den utskrift som tillhandahålls. Du kan kontrollera de segmenterade yttrandena och de matchande transkriptionerna genom att hämta datauppsättningen. Unika ID:n tilldelas automatiskt till segmenterade yttranden. Det är viktigt att du ser till att de utskrifter du anger är 100% korrekta. Fel i transkriptionerna kan minska noggrannheten under ljudsegmenteringen och ytterligare införa kvalitetsförlust i träningsfasen som kommer senare.

## <a name="audio-only-beta"></a>Endast ljud (beta)

Om du inte har transkriptioner för dina ljudinspelningar använder du alternativet **Endast ljud** för att ladda upp dina data. Vårt system kan hjälpa dig att segmentera och transkribera dina ljudfiler. Tänk på att den här tjänsten räknas in i prenumerationsanvändningen för tal till text.

Följ dessa riktlinjer när du förbereder ljud.

> [!NOTE]
> Tjänsten för segmentering av långa ljud utnyttjar batch-transkriptionsfunktionen i tal-till-text, som endast stöder S0-användare (Standard subscription).

| Egenskap | Värde |
| -------- | ----- |
| Filformat | RIFF (.wav) med en samplingsfrekvens på minst 16 khz-16-bitars i PCM eller .mp3 med en bithastighet på minst 256 KBps, grupperad i en ZIP-fil |
| Filnamn | ASCII- och Unicode-tecken stöds. Inget dubblettnamn tillåts. |
| Ljudlängd | Längre än 20 sekunder |
| Arkivformat | .zip |
| Maximal arkivstorlek | 2048 MB |

Alla ljudfiler ska grupperas i en zip-fil. När din datauppsättning har laddats upp hjälper vi dig att segmentera ljudfilen i yttranden baserat på vår utskriftstjänst för talbatchen. Unika ID:n tilldelas automatiskt till segmenterade yttranden. Matchande avskrifter genereras genom taligenkänning. Alla .mp3-filer omvandlas till WAV-formatet efter bearbetningen. Du kan kontrollera de segmenterade yttrandena och de matchande transkriptionerna genom att hämta datauppsättningen.

## <a name="next-steps"></a>Nästa steg

- [Skapa en anpassad röst](how-to-custom-voice-create-voice.md)
- [Guide: Spela in dina röstexempel](record-custom-voice-samples.md)
