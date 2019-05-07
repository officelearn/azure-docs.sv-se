---
title: Hur du förbereder data för anpassad röst – Speech Services
titlesuffix: Azure Cognitive Services
description: Skapa en anpassad ton för ditt varumärke med Azure Speech Services. Du anger studio inspelningar och associerade skript, tjänsten genererar en unik röst modell koll på inspelade röst. Använd rösten för att syntetisera tal i dina produkter, verktyg och program.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.openlocfilehash: 18e1bb486c47baf7648a74e31451e2db73f72250
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65156865"
---
# <a name="prepare-data-to-create-a-custom-voice"></a>Förbereda data för att skapa en anpassad röst

När du är redo att skapa en anpassad rösten för ditt program kan är det första steget att samla in ljudinspelningar och associerade skript för att starta träna voice-modell. Tjänsten använder dessa data för att skapa en unik röst verksamhetsbehoven röst i inspelningen. När du har tränat röst, kan du börja synthesizing tal i dina program.

Du kan börja med en liten mängd data för att skapa ett konceptbevis. Men ju mer data som du anger, mer naturliga låter din anpassade röst. Innan du kan träna dina egna rösten modell, behöver du ljudinspelningar och associerade text avskrifter. På den här sidan kommer vi att granska datatyper, hur de används och hur du hanterar varje.

## <a name="data-types"></a>Datatyper

En datauppsättning för träning röst innehåller ljudinspelningar och en textfil med associerade avskrifter. Varje ljudfil bör innehålla en enda uttryck (en mening eller en enda aktivera för ett dialogrutan system) och vara mindre än 15 sekunder långa.

I vissa fall kan du kanske inte har rätt datauppsättningen klar och vill testa anpassade voice-utbildning med tillgängliga ljudfiler kort eller lång, med eller utan avskrifter. Vi ger verktyg (beta) för att hjälpa dig att segmentera dina ljud i uttryck och förbereda avskrifter med hjälp av den [Batch avskrift API](batch-transcription.md).

Den här tabellen anger datatyper och hur var och en för att skapa en anpassad text till tal röst-modell.

| Datatyp | Beskrivning | När du ska använda detta | Ytterligare tjänst som krävs | Kvantitet för att träna en modell | Alla |
| --------- | ----------- | ----------- | --------------------------- | ----------------------------- | --------- |
| **Enskilda yttranden + matchande avskrift** | En samling (.zip) ljudfiler (.wav) som enskilda yttranden. Varje ljudfil ska vara 15 sekunder eller färre, tillsammans med en formaterad avskrift (.txt). | Professionella inspelningar med matchande avskrifter | Redo för utbildning. | Inga hårda krav för en-US och zh-CN. Mer än 2 000 + distinkta yttranden för andra språk. | Alla anpassade Voice-språk |
| **Lång ljud + avskrifter (beta)** | En samling (.zip) för långa, osegmenterat ljudfiler (mer än 20 sekunder), tillsammans med en avskrift (.txt) som innehåller alla tal. | Du har ljudfiler och matchande avskrifter, men de delats upp inte i yttranden. | Segmentering (med batch avskrift).<br>Ljudformatet omvandling om det behövs. | Inga hårda krav för en-US och zh-CN. | `en-US` och `zh-CN` |
| **Endast ljud (beta)** | En samling (.zip) ljudfiler utan en avskrift. | Du kan bara ha ljudfiler som är tillgängliga utan avskrifter. | Segmentering + avskrifter generation (med batch avskrift).<br>Ljudformatet omvandling om det behövs.| Inga hårda krav på `en-US` och `zh-CN`. | `en-US` och `zh-CN` |

Filer bör grupperade efter typ till en datauppsättning och laddas upp som en zip-fil. Varje datamängd kan bara innehålla en enda datatyp.

> [!NOTE]
> Det maximala antalet datauppsättningar som kan importeras per prenumeration är 10 ZIP-filer utan kostnad prenumerationsanvändare (F0) och 500 för standard prenumerationsanvändare (S0).

## <a name="individual-utterances--matching-transcript"></a>Enskilda yttranden + matchande avskrift

Du kan förbereda inspelningar av enskilda yttranden och matchande avskriften på två sätt. Antingen skriva ett skript och läsas av en röst-personal eller använda offentligt tillgängliga ljud och transkribera till text. Om du gör det senare måste du redigera disfluencies från ljudfiler, till exempel ”um” och andra information ljud, hackar, mumbled ord eller mispronunciations.

Skapa inspelningarna för att skapa en bra rösttyp i en tyst rum med en mikrofon av hög kvalitet. Konsekvent volym talar pris, samtalsstil motivation och lättfattliga maner, ovanor tal är nödvändiga.

> [!TIP]
> Om du vill skapa en röst för användning i produktion, rekommenderar vi att du använder en professionell inspelning studio och röst personal. Mer information finns i [så spela in röst-exempel för en anpassad röst](https://review.docs.microsoft.com/azure/cognitive-services/speech-service/record-custom-voice-samples).

### <a name="audio-files"></a>Ljudfiler

Varje ljudfil ska innehålla en enda uttryck (en mening eller en enda aktivera för ett dialogrutan system), mindre än 15 sekunder långa. Alla filer måste finnas i samma talat språk. Anpassad text till tal röster för flera språk stöds inte, med undantag för den kinesiska engelska bi språkgränserna. Varje ljudfil måste ha ett unikt numeriska filnamn med filnamnstillägget .wav filnamn.

Följ dessa riktlinjer när du förbereder ljud.

| Egenskap  | Värde |
| -------- | ----- |
| Filformat | RIFF (.wav) som är grupperade i en .zip-fil |
| Samplingsfrekvens | minst 16 000 Hz |
| Exempelformat | PCM, 16-bitars |
| Filnamn | Numeriska, med filtillägget .wav. Inga dubbla filnamn som tillåts. |
| Ljud längd | Kortare än 15 sekunder |
| Arkivformat | .zip |
| Maximal arkivstorlek | 200 MB |

> [!NOTE]
> WAV-filer med en lägre än 16 000 Hz samplingsfrekvensen avvisas. Om en .zip-fil innehåller WAV-filer med olika samplingsfrekvensen, importeras endast dessa lika med eller högre än 16 000 Hz. I portalen för närvarande importerar .zip arkiverar upp till 200 MB. Dock kan du överföra flera Arkiv.

### <a name="transcripts"></a>Avskrifter

Transkription-filen är en oformaterad textfil. Använd dessa riktlinjer för att förbereda din avskrifter.

| Egenskap  | Värde |
| -------- | ----- |
| Filformat | Oformaterad text (.txt) |
| Kodningsformatet | ANSI/ASCII, UTF-8, UTF-8-BOM, UTF-16-LE eller UTF-16-BE. ANSI/ASCII och UTF-8-kodning stöds inte för zh-CN. |
| antal yttrande per rad | **En** -varje rad i filen avskrift bör innehålla namnet på en ljudfiler, följt av motsvarande avskrift. Filnamnet och transkriptionen ska separeras med ett tabbtecken (\t). |
| Maximal filstorlek | 50 MB |

Nedan visas ett exempel på hur avskrifter organiseras uttryck av uttryck i en txt-fil:

```
0000000001[tab] This is the waistline, and it's falling.
0000000002[tab] We have trouble scoring.
0000000003[tab] It was Janet Maslin.
```
Det är viktigt att avskrifter är 100% korrekt avskrifter motsvarande ljudet. Fel i betyg kommer att införa kvalitetsförlust under utbildningen.

> [!TIP]
> När du skapar produktion text till tal röster, väljer yttranden (eller skriva skript) som kontot både fonetiska täckning och effektivitet. Problem med att resultaten vill? [Kontakta anpassad röst](mailto:speechsupport@microsoft.com) -teamet för att ta reda på mer om med oss finns.

## <a name="long-audio--transcript-beta"></a>Lång ljud + avskrifter (beta)

I vissa fall kanske du inte har segmenterade ljud som är tillgängliga. Vi tillhandahåller en tjänst (beta) via anpassade voice-portalen kan du segmentera länge ljudfiler och skapa avskrifter. Att tänka på, den här tjänsten kommer att debiteras mot din för tal till text-prenumerationsanvändning.

> [!NOTE]
> Tjänsten långa ljud segmentering att utnyttja funktionen batch avskrift i tal till text, som stöder endast standard prenumerationsanvändare (S0). Under bearbetning av segmenteringen skickas din ljudfiler och avskrifter också till Custom Speech-tjänsten att förfina igenkänningsfunktion för så att det arbete du utfört kan förbättras för dina data. Inga data kommer att hållas kvar under den här processen. När segmenteringen görs lagras endast talade segmenterade och deras mappning avskrifter för att hämta och utbildning.

### <a name="audio-files"></a>Ljudfiler

Följ dessa riktlinjer när du förbereder ljud för segmentering.

| Egenskap  | Värde |
| -------- | ----- |
| Filformat | RIFF (.wav) med en samplingsfrekvensen av minst 16 khz-16-bitars i PCM eller .mp3 med en bithastighet på minst 256 kbit/s, grupperade i en .zip-fil |
| Filnamn | ASCII-tecken. Unicode-tecken i namnet misslyckas (till exempel kinesiska tecken, eller symboler som ””,). Inga Dubblettnamn tillåts. |
| Ljud längd | Längre än 20 sekunder |
| Arkivformat | .zip |
| Maximal arkivstorlek | 200 MB |

Alla ljudfiler ska grupperas i en zip-fil. Det är OK om du vill placera WAV-filer och MP3-filer i en ljud zip, men ingen undermapp tillåts i zip-filen. Du kan till exempel överföra en zip-fil som innehåller en ljudfil 'kingstory.wav', 45 sekund långa och en annan en namngiven 'queenstory.mp3', 200 sekund-långa, utan undermappar. Alla filer som .mp3 transformeras i formatet .wav efter bearbetning.

### <a name="transcripts"></a>Avskrifter

Avskrifter måste vara beredd att specifikationer i den här tabellen. Varje ljudfil måste matchas med en avskrift.

| Egenskap  | Värde |
| -------- | ----- |
| Filformat | Oformaterad text (.txt), som är grupperade i en .zip |
| Filnamn | Använd samma namn som den matchande ljudfilen |
| Kodningsformatet | UTF-8-BOM endast |
| antal yttrande per rad | Obegränsad |
| Maximal filstorlek | 50M |

Alla avskrifter filer i den här datatypen ska grupperas i en zip-fil. Ingen undermapp tillåts i zip-filen. Exempel: du har överfört en zip-fil som innehåller en ljudfil med namnet kingstory.wav, 45 sekunder långa och en annan en namngiven 'queenstory.mp3', 200 sekunder länge. Behöver du överföra en annan zip-fil som innehåller två avskrifter, en namngiven 'kingstory.txt', den andra en 'queenstory.txt'. Inom varje oformaterad textfil får du fullständig rätt avskrift för matchande ljudet.

När din datauppsättning har överförts kan du segmentera ljudfilen i yttranden baserat på avskrifter tillhandahålls. Du kan kontrollera segmenterade yttranden och matchande avskrifter genom att hämta datauppsättningen. Unika ID: N kommer automatiskt att tilldelas till segmenterade yttranden. Det är viktigt att du ser till avskrifter som du anger är 100% korrekt. Fel i avskrifter kan minska det arbete du utfört under ljud segmentering och ytterligare orsakar kvaliteten i fasen utbildning som kommer senare.

## <a name="audio-only-beta"></a>Endast ljud (beta)

Om du inte har avskrifter för din ljudinspelningar kan använda den **endast ljud** möjlighet att ladda upp dina data. Vårt system kan hjälpa dig att segmentera och effektiv din ljudfiler. Tänk på, den här tjänsten tillgodoräkna dig prenumerationsanvändning ditt tal till text.

Följ dessa riktlinjer när du förbereder ljud.

> [!NOTE]
> Tjänsten långa ljud segmentering att utnyttja funktionen batch avskrift i tal till text, som stöder endast standard prenumerationsanvändare (S0).

| Egenskap  | Värde |
| -------- | ----- |
| Filformat | RIFF (.wav) med en samplingsfrekvensen av minst 16 khz-16-bitars i PCM eller .mp3 med en bithastighet på minst 256 kbit/s, grupperade i en .zip-fil |
| Filnamn | ASCII-tecken. Unicode-tecken i namnet misslyckas (till exempel kinesiska tecken, eller symboler som ””,). Inga Dubblettnamn tillåts. |
| Ljud längd | Längre än 20 sekunder |
| Arkivformat | .zip |
| Maximal arkivstorlek | 200 MB |

Alla ljudfiler ska grupperas i en zip-fil. Ingen undermapp tillåts i zip-filen. När din datauppsättning har har överförts, vi hjälper dig att segmentera ljudfilen i yttranden baserat på vår avskrift batchtjänsten tal. Unika ID: N kommer automatiskt att tilldelas till segmenterade yttranden. Matchar avskrifter genereras via taligenkänning. Alla filer som .mp3 transformeras i formatet .wav efter bearbetning. Du kan kontrollera segmenterade yttranden och matchande avskrifter genom att hämta datauppsättningen.

## <a name="next-steps"></a>Nästa steg

- [Skapa en anpassad röst](how-to-custom-voice-create-voice.md)
- [Guide: Registrera din röst-exempel](record-custom-voice-samples.md)
