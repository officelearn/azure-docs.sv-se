---
title: Så här skapar du en anpassad rösttyp
titlesuffix: Azure Cognitive Services
description: Den här artikeln är en översikt över Text till tal röst anpassning, där du kan skapa en identifierbara, en av-unika varumärkesröst.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: panosper
ms.openlocfilehash: 24b98ce8cd2c587f0d39390954eb8a64747ca2ab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60654081"
---
# <a name="creating-custom-voice-fonts"></a>Skapa anpassade rösttyper

Tal röst anpassning kan du skapa en identifierbara, en av en typ ton för ditt varumärke: en *rösttyp.*

För att skapa din rösttyp du gör en studio-registrering och ladda upp de associerade skript som träningsdata. Tjänsten skapar sedan en unik röst modell koll på inspelningen. Du kan använda den här rösttyp för att syntetisera tal.

Du kan komma igång med en liten mängd data för ett konceptbevis. Men ju mer data du tillhandahålla, desto mer naturliga och professional låter din röst.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver ett Azure-konto och en prenumeration på Speech-tjänsten. [Skapa en](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started) om du inte redan har gjort. Ansluta din prenumeration till den anpassade Voice-portalen som visas här.

1. Logga in på den [anpassad röst portal](https://customvoice.ai) med hjälp av samma Microsoft-konto som du använde för att tillämpa för åtkomst.

2. Under kontonamnet på ditt längst upp till höger, gå till **prenumerationer**.

    ![Subscriptions](media/custom-voice/subscriptions.png)

3. På sidan prenumerationer väljer **ansluta befintliga prenumeration**. Observera att Speech Services har stöd för olika regioner. Kontrollera den region där din prenumerationsnyckel skapades, och kontrollera att du ansluter din nyckel till rätt underordnade portalen.  

4. Klistra in din prenumerationsnyckel i tabellen, som visas i följande exempel. Varje prenumeration har två nycklar och du kan använda någon av dessa.

     ![Lägg till prenumeration](media/custom-voice/add-subscription.png)

Du är redo att sätta igång!

## <a name="prepare-recordings-and-transcripts"></a>Förbereda inspelningar och betyg

En datauppsättning för träning röst består av en uppsättning ljudfiler, tillsammans med en textfil som innehåller avskrifter av ljud filerna.

Du kan förbereda de här filerna på två sätt. Antingen skriva ett skript och läses av röst personal eller använda offentligt tillgängliga ljud och transkribera till text. Om du gör det senare måste du redigera disfluencies från ljudfiler, till exempel ”um” och andra information ljud, hackar, mumbled ord eller mispronunciations.

För att skapa en bra rösttyp, gör du inspelningarna i en tyst rum med en mikrofon av hög kvalitet. Konsekvent volym talar pris, samtalsstil motivation och lättfattliga maner, ovanor tal är nödvändiga för att skapa en fantastisk digital röst.

Om du vill skapa en röst för användning i produktion, rekommenderar vi att du använder en professionell inspelning studio och röst personal. Mer information finns i [så spela in röst-exempel för en anpassad röst](record-custom-voice-samples.md).

### <a name="audio-files"></a>Ljudfiler

Varje ljudfil ska innehålla en enda uttryck (till exempel en mening eller en enda aktivera för ett system för dialogrutan). Alla filer måste finnas i samma språk. (Röster för flera språk anpassade stöds inte.) Ljudfiler måste också ha ett unikt numeriska filnamn med filnamnstillägget `.wav`.

Ljudfiler bör vara beredd på följande sätt. Andra format stöds inte och kommer att avvisas.

| **Egenskap** | **Värde** |
| ------------ | --------- |
| Filformat  | RIFF (.wav)|
| Samplingsfrekvens| minst 16 000 Hz |
| Exempelformat| PCM, 16-bitars |
| Filnamn    | Numeriska med `.wav` tillägg |
| Arkivformat| .zip      |
| Maximal arkivstorlek|200 MB|

> [!NOTE]
> WAV-filer med en lägre än 16 000 Hz samplingsfrekvensen avvisas. Om en .zip-fil innehåller vågor med olika sampling kan importeras endast dessa lika med eller högre än 16 000 Hz.
> I portalen för närvarande importerar .zip arkiverar upp till 200 MB. Dock kan du överföra flera Arkiv. Det maximala antalet datauppsättningar som tillåts är 10 .zip kostnadsfritt filer användare och 50 för standard användare.

### <a name="transcripts"></a>Avskrifter

Transkription-filen är oformaterad text (ANSI, UTF-8, UTF-8-BOM, UTF-16-LE eller UTF-16-BE). Varje rad i filen avskrift måste ha namnet på en ljudfil, följt av ett tabbtecken (kodpunkt 9) och slutligen dess avskrift. Några tomma rader tillåts.

Exempel:

```
0000000001  This is the waistline, and it's falling.
0000000002  We have trouble scoring.
0000000003  It was Janet Maslin.
```

Anpassade röst systemet normaliserar avskrifter genom att konvertera texten till gemener och ta bort överflödig skiljetecken. Det är viktigt att avskrifter är 100% korrekt avskrifter av motsvarande ljudinspelningar.

> [!TIP]
> När kontot bygga produktion text till tal röster, väljer yttranden (eller skriva skript) som gör i både fonetiska täckning och effektivitet. Problem med att resultaten vill? [Anpassad Voice-teamet](mailto:speechsupport@microsoft.com) att ta reda på mer om med oss finns.

## <a name="upload-your-datasets"></a>Ladda upp dina datauppsättningar

När du har förberett din ljudfil arkivering och betyg överföra dem via den [anpassad röst tjänstportalen](https://customvoice.ai).

> [!NOTE]
> Datauppsättningar kan inte redigeras när de har överförts. Om du glömmer att inkludera avskrifter av några av ljudfilerna, till exempel eller råkar välja fel kön måste du ladda upp hela datauppsättningen igen. Kontrollera dina inställningar och datauppsättningen noggrant innan du startar överföringen.

1. Logga in på portalen.

2. På huvudsidan under **anpassad röst**väljer **Data**.   

    Den **min röst** tabellen visas. Den är tom om du inte har laddat upp röst datauppsättningar ännu.

3. Om du vill öppna sidan för att ladda upp en ny datauppsättning, Välj **dataimport**.

    ![Importera Voice-Data](media/custom-voice/import-voice-data.png)

4. Ange ett namn och beskrivning i fälten som tillhandahålls.

5. Välj språk för din rösttyper. Kontrollera att språkinformationen matchar språket i spela in data och skripten.

6. Välj kön talaren vars röst som du använder.

7. Välj skript och ljudfiler för att ladda upp.

8. Välj **Import** att ladda upp data. För större datamängder kan det ta flera minuter att importera.

> [!NOTE]
> Kostnadsfria användare kan ladda upp två datauppsättningar i taget. Standard-prenumerationsanvändare kan samtidigt överföra fem datauppsättningar. Om du når gränsen kan du vänta tills minst en av dina datauppsättningar har importerats. Försök sedan igen.

När överföringen är klar, den **Mina röstdata** tabellen visas igen. Du bör se en post som motsvarar den datauppsättning som du just har överfört.

Datauppsättningar verifieras automatiskt efter överföringen. Dataverifiering innehåller en rad kontroller på ljudfiler att verifiera sina filformat, storlek och samplingsfrekvensen. Kontrollerar om filerna som taltranskription Kontrollera filformatet och göra vissa text normalisering. Talade transkriberas använder taligenkänning. Sedan jämförs resultatsträngen med avskrift som du angav.

![My Voice Data](media/custom-voice/my-voice-data.png)

I följande tabell visas bearbetning tillstånden för importerade datauppsättningar:

| Status | Betydelse
| ----- | -------
| `NotStarted` | Din datauppsättning har tagits emot och är i kö för bearbetning.
| `Running` | Din datauppsättning verifieras.
| `Succeeded` | Din datauppsättning har verifierats och kan nu användas för att skapa en rösttyp.

När verifieringen är klar kan du se det totala antalet matchande yttranden för var och en av dina datauppsättningar i den **uttryck** kolumn.

Du kan hämta en rapport för att kontrollera uttal poängen och bruset nivån för var och en av dina inspelningar. Uttal av poängen är 0 och 100. En poäng under 70 betyder vanligen att ett tal fel eller ett skript-matchningsfel. En tung accent kan minska dina uttal poäng och påverka genererade digitala röst.

Högre signal-brus förhållande (SNR) anger lägre bruset i dina ljud. Du kan vanligtvis nå 50 + SNR genom att spela in med professionella studios. Ljud med en SNR nedan 20 kan leda till uppenbara bruset i din röst när du genererade.

Överväg att inspelningen yttranden med låg uttal poäng eller dålig signal brus förhållanden. Om du inte kan registrera igen, kan du undanta dessa yttranden från din datauppsättning.

## <a name="build-your-voice-font"></a>Skapa din rösttyp

När din datauppsättning har verifierats, kan du använda den för att skapa din anpassad rösttyp.

1.  I den **anpassad röst** nedrullningsbara menyn, Välj **modeller**.

    Den **Mina Rösttyper** tabellen visas, visa en lista över alla anpassade rösttyper du redan har skapat.

1. Under rubriken tabellen, väljer **skapa röster**.

    Sidan för att skapa en rösttyp visas. De aktuella nationella inställningarna visas i den första raden i tabellen. Ändra språk för att skapa en röst i ett annat språk. De nationella inställningarna måste vara samma som för de datauppsättningar som används för att skapa röst.

1. Ange ett namn och beskrivning för att identifiera den här modellen som du gjorde när du har laddat upp din datauppsättning.

    Välj ett namn noggrant. Namnet du anger här blir det namn som du använder för att ange röst i din begäran om talsyntes som en del av SSML indata. Endast bokstäver, siffror och några skiljetecken som `-`, `_`, och `(', ')` tillåts.

    Ett vanligt användningsområde för den **beskrivning** fält är att anteckna namnen på de datauppsättningar som har använts för att skapa modellen.

1. Välj din rösttyp kön. Det måste matcha kön för datauppsättningen.

1. Välj de datauppsättningar som du vill använda för att träna rösttyp. Alla datauppsättningar som du använder måste komma från samma API.

1. Klicka på **skapa** att börja skapa din rösttyp.

    ![Skapa modell](media/custom-voice/create-model.png)

Den nya modellen visas i den **Mina Rösttyper** tabell.

![Min Rösttyper](media/custom-voice/my-voice-fonts.png)

Den status som visas återspeglar att konvertera datauppsättningen till en rösttyp som visas här.

| Status | Betydelse
| ----- | -------
| `NotStarted` | Din begäran för att skapa en röst teckensnittet är i kö för bearbetning.
| `Running` | Din rösttyp håller på att skapas.
| `Succeeded` | Din rösttyp har skapats och kan distribueras.

Utbildning tiden varierar beroende på mängden bearbetade ljud data. Vanliga gånger mellan om 30 minuter för hundratals yttranden och 40 timmar för 20 000 yttranden.

> [!NOTE]
> Kostnadsfria användare kan träna en rösttyp i taget. Standard-prenumerationsanvändare kan träna tre röster samtidigt. Om du når gränsen, vänta tills minst en av dina rösttyper är klar utbildning och försök sedan igen.

## <a name="test-your-voice-font"></a>Testa din rösttyp

När din rösttyp har har skapats, kan du testa den innan du distribuerar den för användning. I den **Operations** kolumnen, markerar **Test**. Testsidan visas för den valda rösttyp. Tabellen är tom om du ännu inte har skickat alla test-begäranden för röst.

Om du vill visa en popup-meny för att skicka begäranden för text, Välj den **Test med text** knappen under rubriken tabell. Du kan skicka din begäran för testning i oformaterad text eller SSML. Den maximala storleken för indata är 1 024 tecken, inklusive alla taggar för SSML-begäran. Språk i texten måste vara samma som din rösttyp språk.

När du fyller i textrutan och bekräftar läget som indata, **Ja** att skicka din begäran för testning och återgå till testsidan. Tabellen innehåller nu en post som motsvarar din nya begäran och i statuskolumnen. Det kan ta några minuter att syntetisera tal. När statuskolumnen säger **lyckades**, du kan ladda ned textinmatningen (en `.txt` fil) och ljuduppspelning (en `.wav` fil), och lyssna det senare för kvalitet.

## <a name="create-and-use-a-custom-endpoint"></a>Skapa och använda en anpassad slutpunkt

När du har skapat och testat din röst-modell kan distribuera du den i en anpassad text till tal-slutpunkt. Du kan sedan använda den här slutpunkten i stället för slutpunkten som vanligt när text till tal-förfrågningar via REST API. Din anpassade slutpunkt kan anropas bara av den prenumeration som du använde för att distribuera teckensnittet.

Om du vill skapa en ny anpassad slutpunkt, Välj **slutpunkter** från den **anpassade röst** menyn längst upp på sidan. Den **Mina distribueras röster** visas, med dess tabell med den aktuella anpassade röst slutpunkter, om sådana. De aktuella nationella inställningarna visas i den första raden i tabellen. Ändra det visade språket om du vill skapa en distribution för ett annat språk. (Det måste matcha röst du distribuerar.)

Om du vill skapa en ny slutpunkt, Välj den **distribuera röster** knappen. Ange namn och beskrivning av din anpassade slutpunkt.

Från den **prenumeration** menyn, Välj den prenumeration som du vill använda. Kostnadsfria användare kan ha endast en modell som distribuerats i taget. Standard användare kan skapa upp till 20 slutpunkter, var och en med sin egen anpassade röst.

![Skapa slutpunkt](media/custom-voice/create-endpoint.png)

När du har valt modellen distribueras, Välj **skapa**. Den **Mina distribueras röster** sidan dyker upp nu innehåller en post för din nya slutpunkt. Det kan ta några minuter att skapa en instans av en ny slutpunkt. När statusen för distributionen är **lyckades**, slutpunkten är redo att användas.

![Min distribuerade röster](media/custom-voice/my-deployed-voices.png)

När distributionen har **lyckades**, slutpunkten för din distribuerade rösttyp visas i den **Mina distribueras röster** tabell. Du kan använda den här URI: N direkt i en HTTP-begäran.

Online testning av slutpunkten är också tillgängligt via portalen anpassade röst. Om du vill testa din slutpunkt, Välj **slutpunkter testning** från den **anpassad röst** nedrullningsbara menyn. Slutpunkten testning sida visas. Välj en distribuerade anpassade röst och ange vilken text som ska läsas (i oformaterad text eller SSML format) i textrutan.

> [!NOTE]
> När du använder SSML, den `<voice>` taggen måste ange namnet som du gav din anpassade röst när du skapade den. Om du skickar in oformaterad text, används alltid anpassade röst.

Om du vill ta del av texten som sägs i din anpassad rösttyp, Välj **spela upp**.

![Slutpunkt för testning](media/custom-voice/endpoint-testing.png)

Anpassad slutpunkt är funktionellt identiskt med standard-slutpunkt som används för text till tal-begäranden. Se [REST API](rest-apis.md) för mer information.

## <a name="language-support"></a>Stöd för språk

Röst anpassning är tillgänglig på följande språk:

| Språk | Nationell inställning |
|----------|--------|
| Kinesiska (fastlandet) | zh-CN |
| English (US) | en-US |
| Franska | fr-FR |
| Tyska | de-DE |
| Italienska | IT-IT |

> [!NOTE]
> Franska, tyska och italienska röst utbildning börjar med en datauppsättning av över 2 000 yttranden. Kinesiska – engelska tvåspråkig modeller stöds också med en datauppsättning av över 2 000 yttranden.

## <a name="next-steps"></a>Nästa steg

- [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Registrera din röst-exempel](record-custom-voice-samples.md)
