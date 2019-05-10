---
title: Skapa en anpassad röst – Speech Services
titlesuffix: Azure Cognitive Services
description: När du är redo att överföra dina data kan gå till anpassad röst-portalen. Skapa eller välj ett anpassat Voice-projekt. Projektet måste dela rätt språk och nationella och egenskaper för kön som du avser använda för röst utbildning.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.openlocfilehash: 6189ea2866d1c16f994179df0179e29353e6c47d
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65410727"
---
# <a name="create-a-custom-voice"></a>Skapa en anpassad röst

I [förbereda data för anpassade röst](how-to-custom-voice-prepare-data.md), vi beskrivs de olika datatyper som du kan använda för att träna en anpassad röst och olika formatkraven. När du har förberett din data, kan du börja överföra dem till den [anpassad röst portal](https://aka.ms/custom-voice-portal), eller via API: et för anpassad röst utbildning. Här beskrivs hur du tränar en egen röst via portalen.

> [!NOTE]
> Den här sidan förutsätter att du har läst [Kom igång med anpassade röst](how-to-custom-voice.md) och [förbereda data för anpassad röst](how-to-custom-voice-prepare-data.md), och har skapat en anpassad Voice-projekt.

Kontrollera de språk som stöds för anpassade Röst: [språk för anpassning](language-support.md#customization).

## <a name="upload-your-datasets"></a>Ladda upp dina datauppsättningar

När du är redo att överföra dina data kan gå till den [anpassad röst portal](https://aka.ms/custom-voice-portal). Skapa eller välj ett anpassat Voice-projekt. Projektet måste dela rätt språk och nationella och egenskaper för kön som du avser använda för röst utbildning. Välj exempelvis `en-GB` om ljudinspelningar som du har gjort på engelska med en accent för Storbritannien.

Gå till den **Data** fliken och klicka på **ladda upp data**. I guiden väljer du rätt datatyp som matchar vad du har förberett.

Varje datauppsättning som du överför måste uppfylla kraven för den datatyp som du väljer. Det är viktigt att korrekt formatera dina data innan det överförs. Detta garanterar att data bearbetas korrekt av anpassade Voice-tjänsten. Gå till [förbereda data för anpassad röst](how-to-custom-voice-prepare-data.md) och se till att dina data har formaterats ställa in.

> [!NOTE]
> Kostnadsfri prenumeration (F0) användare kan ladda upp två datauppsättningar samtidigt. Standard (S0) användare kan samtidigt överföra fem datauppsättningar. Om du når gränsen kan du vänta tills minst en av dina datauppsättningar har importerats. Försök sedan igen.

> [!NOTE]
> Det maximala antalet datauppsättningar som kan importeras per prenumeration är 10 ZIP-filer utan kostnad prenumerationsanvändare (F0) och 500 för standard prenumerationsanvändare (S0).

Datauppsättningar verifieras automatiskt när du trycker på knappen ladda upp. Dataverifiering innehåller en rad kontroller på ljudfiler att verifiera sina filformat, storlek och samplingsfrekvensen. Åtgärda felen om sådana och skicka igen. När begäran för import av data initieras har, bör du se en post i en tabell som motsvarar den datauppsättning som du har laddat upp.

I följande tabell visas bearbetning tillstånden för importerade datauppsättningar:

| Status | Betydelse |
| ----- | ------- |
| Bearbetning | Din datauppsättning har tagits emot och bearbetas. |
| Lyckades | Din datauppsättning har verifierats och kan nu användas för att skapa en röst-modell. |
| Fungerar inte | Din datauppsättning misslyckades under bearbetning på grund av flera orsaker, till exempel filfel, data problem eller nätverksproblem. |

När verifieringen är klar kan du se det totala antalet matchande yttranden för var och en av dina datauppsättningar i den **yttranden** kolumn. Om den datatyp som du har valt kräver långa ljud segmentering, visar den här kolumnen endast talade vi har segmenterade antingen baserat på dina betyg eller via tjänsten tal avskrift. Ytterligare kan du hämta datauppsättningen verifieras för att visa information om resultatet av yttranden som har importerats och deras mappning avskrifter. Tips: långa ljud segmentering kan ta mer än en timme att slutföra databearbetning.

För en-US och zh-CN datauppsättningar, kan du hämta en rapport för att kontrollera uttal poängen och bruset nivån för var och en av dina inspelningar ytterligare. Uttal av poängen är 0 och 100. En poäng under 70 betyder vanligen att ett tal fel eller ett skript-matchningsfel. En tung accent kan minska dina uttal poäng och påverka genererade digitala röst.

Högre signal-brus förhållande (SNR) anger lägre bruset i dina ljud. Du kan vanligtvis nå 50 + SNR genom att spela in med professionella studios. Ljud med en SNR nedan 20 kan leda till uppenbara bruset i din röst när du genererade.

Överväg att inspelningen yttranden med låg uttal poäng eller dålig signal brus förhållanden. Om du inte kan registrera igen, kan du undanta dessa yttranden från din datauppsättning.

## <a name="build-your-custom-voice-model"></a>Skapa anpassade voice-modell

När din datauppsättning har verifierats, kan du använda den för att skapa anpassade voice-modell.

1.  Gå till **text till tal > anpassade röst > utbildning**.

2.  Klicka på **träningsmodell**.

3.  Ange sedan en **namn** och **beskrivning** för att identifiera den här modellen.

    Välj ett namn noggrant. Namnet du anger här blir det namn som du använder för att ange röst i din begäran om talsyntes som en del av SSML indata. Endast bokstäver, siffror och några skiljetecken t.ex-, \_, och (',') är tillåtna. Använda olika namn för annan röst modeller.

    Ett vanligt användningsområde för den **beskrivning** fält är att anteckna namnen på de datauppsättningar som har använts för att skapa modellen.

4.  Från den **Välj träningsdata** väljer du en eller flera datauppsättningar som du vill använda för träning. Kontrollera antalet yttranden innan du skickar dem. Du kan börja med valfritt antal yttranden för en-US och zh-CN voice-modeller. Du måste välja fler än 2 000 yttranden för att kunna skapa en röst för andra språk.

    > [!NOTE]
    > Ljud dubblettnamn tas bort från utbildningen. Kontrollera att de datauppsättningar som du väljer inte innehåller samma ljud namn i flera ZIP-filer.

    > [!TIP]
    > Med datauppsättningar från samma talaren krävs för kvalitetsresultat. När de datauppsättningar som du har skickat för träning innehåller ett antal mindre än 6 000 distinkta yttranden, kommer du träna din röst modell genom statistisk parametrisk syntes-teknik. I fall där dina utbildningsdata överskrider det totala antalet 6 000 distinkta yttranden startar du en träningsprocess med sammanfogning syntes-metoden. Normalt kan sammanfogning-teknik resultera i mer naturliga och större exakthet röst resultat. [Anpassad Voice-teamet](mailto:speechsupport@microsoft.com) om du tränar en modell med den senaste Neural text till tal-teknik som kan producera en digital röst som motsvarar de offentligt tillgängliga [neural röster](language-support.md#neural-voices).

5.  Klicka på **träna** att börja skapa din röst-modell.

Tabellen utbildning visas en ny post som motsvarar till den nya modellen. Tabellen visar även status: Bearbetningsfunktioner, lyckades, misslyckades.

Den status som visas återspeglar att konvertera datauppsättningen till en röst-modell, som visas här.

| Status | Betydelse |
| ----- | ------- |
| Bearbetning | Din röst modell skapas. |
| Lyckades | Din röst-modellen har skapats och kan distribueras. |
| Fungerar inte | Din röst-modell misslyckades i utbildning på grund av flera orsaker, till exempel överblivna data problem eller nätverksproblem. |

Utbildning tiden varierar beroende på mängden bearbetade ljud data. Vanliga gånger mellan om 30 minuter för hundratals yttranden och 40 timmar för 20 000 yttranden. När din modellträning är slutfört kan du börja testa den.

> [!NOTE]
> Kostnadsfri prenumeration (F0) användare kan träna en rösttyp samtidigt. Standard (S0) användare kan träna tre röster samtidigt. Om du når gränsen, vänta tills minst en av dina rösttyper är klar utbildning och försök sedan igen.

> [!NOTE]
> Det maximala antalet voice-modeller får utbildas per prenumeration är 10 modeller för kostnadsfri prenumeration (F0)-användare och 100 för standard prenumerationsanvändare (S0).

## <a name="test-your-voice-model"></a>Testa din röst-modell

När din rösttyp har har skapats, kan du testa den innan du distribuerar den för användning.

1.  Gå till **text till tal > anpassade röst > Testa**.

2.  Klicka på **Lägg till test**.

3.  Välj en eller flera modeller som du vill testa.

4.  Ange den text som du vill voice(s) att tala. Om du har valt för att testa flera modeller i taget, används samma text för att testa för olika modeller.

    > [!NOTE]
    > Språk i texten måste vara samma som din rösttyp språk. Endast har tränade modeller kan testas. Endast oformaterad text stöds i det här steget.

5.  Klicka på **Skapa**.

När du har skickat din begäran för testning, återgår du till testsidan. Tabellen innehåller nu en post som motsvarar din nya begäran och i statuskolumnen. Det kan ta några minuter att syntetisera tal. När statuskolumnen säger **lyckades**, du kan spela upp ljudet eller hämta textinmatning (en txt-fil) och ljud utdata (en .wav-fil) och ytterligare lyssna det senare för kvalitet.

Du kan också hitta testresultaten i den här informationssidan av varje modeller som du har valt för testning. Gå till den **utbildning** och på modellnamnet ange sidan med modellen.

## <a name="create-and-use-a-custom-voice-endpoint"></a>Skapa och använda en anpassad voice-slutpunkt

När du har skapat och testat din röst-modell kan distribuera du den i en anpassad text till tal-slutpunkt. Du kan sedan använda den här slutpunkten i stället för slutpunkten som vanligt när text till tal-förfrågningar via REST API. Din anpassade slutpunkt kan anropas bara av den prenumeration som du använde för att distribuera teckensnittet.

Om du vill skapa en ny anpassad voice-slutpunkt, gå till **text till tal > anpassade röst > distribution**. Välj **Lägg till slutpunkt** och ange en **namn** och **beskrivning** för din anpassade slutpunkt. Välj sedan den anpassa voice-modellen som du vill associera med den här slutpunkten.

När du har klickat på den **Lägg till** knapp, i tabellen med slutpunkter, visas en post för din nya slutpunkt. Det kan ta några minuter att skapa en instans av en ny slutpunkt. När statusen för distributionen är **lyckades**, slutpunkten är redo att användas.

> [!NOTE]
> Kostnadsfri prenumeration (F0) användare kan ha endast en modell som har distribuerats. Standard-prenumerationen (S0) användare kan skapa upp till 50 slutpunkter, var och en med sin egen anpassade röst.

> [!NOTE]
> Om du vill använda din egen röst, måste du ange modellnamn röst, Använd anpassade URI: N direkt i en HTTP-begäran och Använd samma prenumeration för att passera autentisering av text till tal-tjänst.

När slutpunkten har distribuerats visas namnet på slutpunkten som en länk. Klicka på länken om du vill visa information som är specifik för din slutpunkt, som slutpunktsnyckeln, slutpunkts-URL och exempelkod.

Online testning av slutpunkten är också tillgängligt via portalen anpassade röst. Om du vill testa din slutpunkt, Välj **Kontrollera endpoint** från den **Endpoint detalj** sidan. Slutpunkten testning sida visas. Ange vilken text som ska läsas (i oformaterad text eller [SSML format](speech-synthesis-markup.md) i textrutan. Om du vill ta del av texten som sägs i din anpassad rösttyp, Välj **spela upp**. Testa funktionen kommer att debiteras mot din anpassade tal syntes användning.

Anpassad slutpunkt är funktionellt identiskt med standard-slutpunkt som används för text till tal-begäranden. Se [REST API](rest-text-to-speech.md) för mer information.

## <a name="next-steps"></a>Nästa steg

* [Guide: Registrera din röst-exempel](record-custom-voice-samples.md)
* [Text till tal-API-referens](rest-text-to-speech.md)
