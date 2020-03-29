---
title: Skapa en anpassad röst - taltjänst
titleSuffix: Azure Cognitive Services
description: När du är redo att ladda upp dina data går du till portalen För anpassad röst. Skapa eller välj ett anpassat röstprojekt. Projektet måste dela rätt språk/språk och könsegenskaperna som de data du tänker använda för din röstutbildning.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: bbe1d651a7d2d2cac1b1aa78b815b2797ad185c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76717324"
---
# <a name="create-a-custom-voice"></a>Skapa en anpassad röst

I [Förbereda data för anpassad röst](how-to-custom-voice-prepare-data.md)beskrev vi de olika datatyper som du kan använda för att träna en anpassad röst och de olika formatkraven. När du har förberett dina data kan du börja ladda upp dem till [Custom Voice-portalen](https://aka.ms/custom-voice-portal)eller via CUSTOM Voice-utbildnings-API:et. Här beskriver vi stegen för att träna en anpassad röst via portalen.

> [!NOTE]
> Den här sidan förutsätter att du har läst [Komma igång med anpassade röst-](how-to-custom-voice.md) och förbereda data för anpassad [röst](how-to-custom-voice-prepare-data.md)och har skapat ett anpassat röstprojekt.

Kontrollera de språk som stöds för anpassad röst: [språk för anpassning](language-support.md#customization).

## <a name="upload-your-datasets"></a>Ladda upp dina datauppsättningar

När du är redo att ladda upp dina data går du till [portalen Anpassad röst](https://aka.ms/custom-voice-portal). Skapa eller välj ett anpassat röstprojekt. Projektet måste dela rätt språk/språk och könsegenskaperna som de data du tänker använda för din röstutbildning. Välj `en-GB` till exempel om de ljudinspelningar du har görs på engelska med brittisk accent.

Gå till fliken **Data** och klicka på **Ladda upp data**. Välj rätt datatyp i guiden som matchar det du har förberett.

Varje datauppsättning som du laddar upp måste uppfylla kraven för den datatyp som du väljer. Det är viktigt att du formaterar dina data korrekt innan de laddas upp. Detta säkerställer att data kommer att behandlas korrekt av custom voice-tjänsten. Gå till [Förbered data för anpassad röst](how-to-custom-voice-prepare-data.md) och se till att dina data har formaterats med rätta.

> [!NOTE]
> Gratis prenumeration (F0) användare kan ladda upp två datauppsättningar samtidigt. Standardprenumerationer (S0) kan ladda upp fem datauppsättningar samtidigt. Om du når gränsen väntar du tills minst en av dina datauppsättningar har importerats. Försök sedan igen.

> [!NOTE]
> Det maximala antalet datauppsättningar som får importeras per prenumeration är 10 .zip-filer för kostnadsfria prenumerationsanvändare (F0) och 500 för S0-användare (Standard subscription).

Datauppsättningar valideras automatiskt när du trycker på uppladdningsknappen. Dataverifiering innehåller en serie kontroller av ljudfilerna för att verifiera filformat, storlek och samplingsfrekvens. Åtgärda eventuella fel och skicka igen. När begäran om dataimporter har initierats bör du se en post i datatabellen som motsvarar den datauppsättning som du just har laddat upp.

I följande tabell visas bearbetningstillstånden för importerade datauppsättningar:

| Status | Betydelse |
| ----- | ------- |
| Bearbetar | Din datauppsättning har tagits emot och bearbetas. |
| Lyckades | Datauppsättningen har validerats och kan nu användas för att skapa en röstmodell. |
| Misslyckades | Datauppsättningen har misslyckats under bearbetningen på grund av många orsaker, till exempel filfel, dataproblem eller nätverksproblem. |

När valideringen är klar kan du se det totala antalet matchade yttranden för var och en av dina datauppsättningar i kolumnen **Yttranden.** Om den datatyp du har valt kräver lång ljudsegmentering återspeglar den här kolumnen bara de yttranden som vi har segmenterat åt dig, antingen baserat på dina avskrifter eller via tjänsten för talavskrifter. Du kan hämta datauppsättningen som validerats ytterligare för att visa detaljerade resultat av de yttranden som har importerats och deras mappningsavskrifter. Tips: segmentering med lång ljud kan ta mer än en timme att slutföra databehandlingen.

För en-US och zh-CN dataset, kan du ladda ner ytterligare en rapport för att kontrollera uttalet poäng och ljudnivån för var och en av dina inspelningar. Uttalspoängen varierar från 0 till 100. En poäng under 70 indikerar normalt ett talfel eller skriptmatchning. En tung accent kan minska ditt uttal poäng och påverka den genererade digitala rösten.

Ett högre signal-brusförhållande (SNR) indikerar lägre brus i ljudet. Du kan vanligtvis nå en 50 + SNR genom att spela in på professionella studior. Ljud med en SNR under 20 kan resultera i uppenbara ljud i din genererade röst.

Överväg att åter registrera eventuella yttranden med låga uttalspoäng eller dåliga signal-brus-förhållanden. Om du inte kan spela in igen kan du utesluta dessa yttranden från datauppsättningen.

## <a name="build-your-custom-voice-model"></a>Bygg din anpassade röstmodell

När datauppsättningen har validerats kan du använda den för att skapa din anpassade röstmodell.

1.  Navigera till **text-till-tal-> anpassad >-utbildning**.

2.  Klicka på **Tågmodell**.

3.  Ange sedan ett **namn** och **en beskrivning som** hjälper dig att identifiera den här modellen.

    Välj ett namn noggrant. Namnet du anger här blir det namn du använder för att ange rösten i din begäran om talsyntes som en del av SSML-indata. Endast bokstäver, siffror och några skiljetecken som \_-, och (', ') är tillåtna. Använd olika namn för olika röstmodeller.

    En vanlig användning av fältet **Beskrivning** är att registrera namnen på de datauppsättningar som användes för att skapa modellen.

4.  På sidan **Välj träningsdata** väljer du en eller flera datauppsättningar som du vill använda för utbildning. Kontrollera antalet yttranden innan du skickar in dem. Du kan börja med valfritt antal yttranden för en-USA och zh-CN röstmodeller. För andra språk måste du välja mer än 2 000 yttranden för att kunna träna en röst.

    > [!NOTE]
    > Dubbla ljudnamn tas bort från träningen. Kontrollera att de datauppsättningar du väljer inte innehåller samma ljudnamn över flera ZIP-filer.

    > [!TIP]
    > Det krävs användning av datauppsättningar från samma högtalare för kvalitetsresultat. När de datauppsättningar du har skickat in för utbildning innehåller ett totalt antal mindre än 6 000 olika yttranden, kommer du att träna din röstmodell genom den statistiska parametriska syntestekniken. Om dina träningsdata överstiger totalt 6 000 olika yttranden kommer du att starta en träningsprocess med concatenationsyntestekniken. Normalt kan sammanfogningstekniken resultera i mer naturliga och högre trohetsröstresultat. [Kontakta Custom Voice-teamet](https://go.microsoft.com/fwlink/?linkid=2108737) om du vill träna en modell med den senaste Neural TTS-tekniken som kan producera en digital röst som motsvarar de offentligt tillgängliga [neurala rösterna.](language-support.md#neural-voices)

5.  Klicka på **Träna** för att börja skapa din röstmodell.

Tabellen Utbildning visar en ny post som motsvarar den här nyskapade modellen. Tabellen visar också status: Bearbetning, lyckades, Misslyckades.

Statusen som visas återspeglar processen att konvertera datauppsättningen till en röstmodell, som visas här.

| Status | Betydelse |
| ----- | ------- |
| Bearbetar | Din röstmodell skapas. |
| Lyckades | Din röstmodell har skapats och kan distribueras. |
| Misslyckades | Din röstmodell har misslyckats i utbildning på grund av många orsaker, till exempel osynliga dataproblem eller nätverksproblem. |

Träningstiden varierar beroende på mängden ljuddata som bearbetas. Typiska tider sträcker sig från cirka 30 minuter för hundratals yttranden till 40 timmar för 20 000 yttranden. När din modell utbildning har slutförts, kan du börja testa den.

> [!NOTE]
> Gratis prenumeration (F0) användare kan träna en röst teckensnitt samtidigt. Standardabonnemang (S0) användare kan träna tre röster samtidigt. Om du når gränsen väntar du tills minst ett av röstteckensnitten är klart och försöker sedan igen.

> [!NOTE]
> Det maximala antalet röstmodeller som får utbildas per prenumeration är 10 modeller för gratis prenumeration (F0) användare och 100 för standardprenumeration (S0) användare.

Om du använder neural röst träning kapacitet, kan du välja att träna en modell optimerad för realtid streaming scenarier, eller en HD neural modell optimerad för asynkron [lång-ljud syntes](long-audio-api.md).  

## <a name="test-your-voice-model"></a>Testa din röstmodell

När röstteckensnittet har skapats kan du testa det innan du distribuerar det för användning.

1.  Navigera till **text-till-tal-> anpassad >-testning**.

2.  Klicka på **Lägg till test**.

3.  Välj en eller flera modeller som du vill testa.

4.  Ange den text som du vill att rösten/röstarna ska tala. Om du har valt att testa flera modeller samtidigt används samma text för testning för olika modeller.

    > [!NOTE]
    > Språket i texten måste vara samma språk som språket i röstteckensnittet. Endast framgångsrikt utbildade modeller kan testas. Endast oformaterad text stöds i det här steget.

5.  Klicka på **Skapa**.

När du har skickat in din testbegäran kommer du tillbaka till testsidan. Tabellen innehåller nu en post som motsvarar din nya begäran och statuskolumnen. Det kan ta några minuter att syntetisera tal. När statuskolumnen säger **Lyckades**kan du spela upp ljudet eller hämta textinmatningen (en TXT-fil) och ljudutdata (en WAV-fil) och ytterligare provspela den senare för kvalitet.

Du kan också hitta testresultaten på detaljsidan för varje modell som du har valt för testning. Gå till fliken **Utbildning** och klicka på modellnamnet för att ange modellinformationssidan.

## <a name="create-and-use-a-custom-voice-endpoint"></a>Skapa och använda en anpassad röstslutpunkt

När du har skapat och testat din röstmodell distribuerar du den i en anpassad slutpunkt för text-till-tal. Du använder sedan den här slutpunkten i stället för den vanliga slutpunkten när du gör text-till-tal-begäranden via REST API.You then use this endpoint in place of the usual endpoint when making Text-to-Speech requests through the REST API. Din anpassade slutpunkt kan bara anropas av den prenumeration som du har använt för att distribuera teckensnittet.

Om du vill skapa en ny anpassad röstslutpunkt går du **till Text-till-tal-> Anpassad röst >-distribution**. Välj **Lägg till slutpunkt** och ange ett **namn** och **en beskrivning** för den anpassade slutpunkten. Välj sedan den anpassade röstmodell som du vill associera med den här slutpunkten.

När du har klickat på knappen **Lägg till** visas en post för den nya slutpunkten i slutpunktstabellen. Det kan ta några minuter att instansiera en ny slutpunkt. När statusen för distributionen **har slutförts**är slutpunkten klar för användning.

> [!NOTE]
> Kostnadsfria prenumerationsanvändare (F0) kan bara ha en modell distribuerad. Standardprenumerationer (S0) kan skapa upp till 50 slutpunkter, var och en med sin egen anpassade röst.

> [!NOTE]
> Om du vill använda din anpassade röst måste du ange röstmodellnamnet, använda den anpassade URI:n direkt i en HTTP-begäran och använda samma prenumeration för att gå igenom autentiseringen av TTS-tjänsten.

När slutpunkten har distribuerats visas slutpunktsnamnet som en länk. Klicka på länken om du vill visa information som är specifik för slutpunkten, till exempel slutpunktsnyckeln, slutpunkts-URL:en och exempelkoden.

Onlinetestning av slutpunkten är också tillgänglig via den anpassade röstportalen. Om du vill testa slutpunkten väljer du **Kontrollera slutpunkt** på **informationssidan för slutpunkt.** Sidan för slutpunktstestning visas. Ange den text som ska läsas upp (i oformaterad text eller [SSML-format](speech-synthesis-markup.md) i textrutan. Om du vill höra texten som läses upp i det anpassade röstteckensnittet väljer du **Spela upp**. Den här testfunktionen debiteras för din anpassade talsyntesanvändning.

Den anpassade slutpunkten är funktionellt identisk med standardslutpunkten som används för text-till-tal-begäranden. Mer information finns i [REST API.](rest-text-to-speech.md)

## <a name="next-steps"></a>Nästa steg

* [Guide: Spela in dina röstexempel](record-custom-voice-samples.md)
* [API-referens för text-till-tal](rest-text-to-speech.md)
* [Api för långt ljud](long-audio-api.md)
