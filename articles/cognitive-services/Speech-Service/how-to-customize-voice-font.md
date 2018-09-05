---
title: Vad är anpassade röst? – Azure Cognitive Services
description: Den här artikeln översikter Microsoft Text till tal röst anpassning, där du kan skapa en identifierbara, en av-unika varumärkesröst.
services: cognitive-services
author: noellelacharite
ms.service: cognitive-services
ms.topic: article
ms.date: 05/07/2018
ms.author: nolach
ms.openlocfilehash: 1f9facf94b8068b98aa49c49ece7070a83db0686
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2018
ms.locfileid: "43665093"
---
# <a name="creating-custom-voice-fonts"></a>Skapa anpassade rösttyper

Microsoft Text till-tal röst anpassning kan du skapa en identifierbara, en av en typ ton för ditt varumärke: en *rösttyp.* 

För att skapa din rösttyp du gör en studio-registrering och ladda upp de associerade skript som träningsdata. Tjänsten skapar sedan en unik röst modell koll på inspelningen. Du kan sedan använda den här rösttyp för att syntetisera tal. 

Du kan komma igång med en liten mängd data för ett konceptbevis. Men ju mer data du tillhandahålla, desto mer naturliga och professional låter din röst.


## <a name="prerequisites"></a>Förutsättningar

Text till tal röst anpassning av funktionen är för tillfället i privat förhandsvisning. [Fyll i formuläret programmet](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0N8Vcdi8MZBllkZb70o6KdURjRaUzhBVkhUNklCUEMxU0tQMEFPMjVHVi4u) övervägas för åtkomst.

Du behöver också en Azure-konto och en prenumeration på Speech-tjänsten. [Skapa en](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started) om du inte redan har gjort. Anslut din prenumeration till anpassad Voice-portalen på följande sätt.

1. Logga in på den [anpassad röst portal](https://customvoice.ai) med samma Microsoft-konto som du använde för att tillämpa för åtkomst.

2. Gå till ”Subscriptions” under kontonamnet på ditt längst upp till höger.

    ![Prenumerationer](media/custom-voice/subscriptions.png)

3. På sidan ”Subscriptions” väljer du Anslut befintlig prenumeration.

     ![Ansluta befintliga prenumeration](media/custom-voice/connect-existing-sub.png)

4. Klistra in din prenumerationsnyckel i tabellen, enligt nedan. Varje prenumeration har två nycklar och du kan använda någon av dessa.

     ![Lägg till prenumeration](media/custom-voice/add-subscription.png)

Du är redo att sätta igång!

> [!IMPORTANT]
> Under fasen privat förhandsgranskning måste prenumerationer vara vitlistad för att använda funktionen för anpassad röst. Följ anvisningarna på sidan för att få din prenumeration i listan över godkända.

## <a name="prepare-recordings-and-transcripts"></a>Förbereda inspelningar och betyg

En datauppsättning för träning röst består av en uppsättning ljudfiler, tillsammans med en textfil som innehåller avskrifter av alla dessa ljudfiler.

Du kan förbereda dessa filer i båda riktningarna: antingen skriva ett skript och läses av röst talanger, eller använda offentligt tillgängliga ljud och transkribera till text. Redigera disfluencies från ljudfiler, till exempel ”um” och andra information ljud, hackar, mumbled ord eller mispronunciations i det senare fallet.

För att skapa en bra rösttyp, är det viktigt att inspelningen är klar i en tyst rum med en mikrofon av hög kvalitet. Konsekvent volym talar pris, samtalsstil motivation och lättfattliga maner, ovanor tal är nödvändiga för att skapa en fantastisk digital röst. Om du vill skapa en röst för användning i produktion, rekommenderar vi att du använder en professionell inspelning studio och röst personal. Mer information finns i [så spela in röst-exempel för en anpassad röst](record-custom-voice-samples.md).

### <a name="audio-files"></a>Ljudfiler

Varje ljudfil ska innehålla en enda uttryck (till exempel en mening eller en enda aktivera för ett system för dialogrutan). Alla filer måste finnas i samma språk (flerspråkig anpassade röster inte stöds). Ljudfiler måste också ha ett unikt numeriska filnamn med filnamnstillägget `.wav`.

Ljudfiler bör vara beredd på följande sätt. Andra format stöds inte och kommer att avvisas.

| **Egenskap** | **Värde** |
| ------------ | --------- |
| Filformat  | RIFF (WAV)|
| Samplingsfrekvens| minst 16 000 Hz |
| Samplingsformat| PCM, 16-bitars |
| Filnamn    | Numeriska med `.wav` tillägg |
| Arkivformat| Zip      |
| Maximal arkivstorlek|200 MB|

> [!NOTE]
> Wave-filer med en lägre än 16 000 Hz samplingsfrekvensen avvisas. I de fall där en zip-fil innehåller vågor med med olika samplingshastigheter, importeras endast dessa lika med eller högre än 16 000 Hz.
> Portalen för närvarande importerar ZIP arkiverar upp till 200 MB. Dock kan du överföra flera Arkiv. Det maximala antalet datauppsättningar som tillåts är 10 ZIP-filer utan kostnad användare och 50 för standard användare.

### <a name="transcripts"></a>Avskrifter

Transkription-filen är en vanlig textfil (ANSI/UTF-8/UTF-8-BOM/UTF-16-LE/UTF-16-BE). Varje rad i filen avskrift måste ha namnet på en ljudfil, följt av ett tabbtecken (kodpunkt 9) och slutligen dess avskrift. Några tomma rader tillåts.

Exempel:

```
0000000001  This is the waistline, and it's falling.
0000000002  We have trouble scoring.
0000000003  It was Janet Maslin.
```

Anpassade röst systemet normaliserar avskrifter genom att omvandla text till gemener och ta bort överflödig skiljetecken. Det är viktigt att avskrifter är 100% korrekt till motsvarande ljudinspelningar.

> [!TIP]
> Om att skapa text till tal röster, väljer yttranden (eller skriva skript) som överväger både fonetiska täckning och effektivitet. Problem med att resultaten vill? [Anpassad Voice-teamet](mailto:tts@microsoft.com) att ta reda på mer om med oss finns.

## <a name="upload-your-datasets"></a>Ladda upp dina datauppsättningar

När du har förberett din ljudfil arkivering och avskrifter, överföra dem via den [anpassad röst leverantörer](https://customvoice.ai).

> [!NOTE]
> Datauppsättningar kan inte redigeras när de har överförts. Om du glömmer att inkludera avskrifter av några av ljudfilerna, till exempel eller råkar välja fel kön måste du ladda upp hela datauppsättningen igen. Kontrollera dina inställningar och datauppsättningen noggrant innan du börjar överföringen.

1. Logga in på portalen.

2. Välj **Data** under Anpassad röst på huvudsidan. 

    Tabellen Mina Voice-Data visas. Den är tom om du inte har ännu har överfört röst datauppsättningar.

3. Klicka på **dataimport** att öppna sidan för att ladda upp en ny datauppsättning.

    ![Importera Voice-Data](media/custom-voice/import-voice-data.png)

4. Ange ett namn och beskrivning i fälten. 

5. Välj språk för din rösttyper. Kontrollera att språkinformationen matchar språket i spela in data och skripten. 

6. Välj kön talaren vars röst som du använder.

7. Välj skript och ljudfiler för att ladda upp. 

8. Klicka på **Import** att ladda upp data. För större datamängder kan det ta flera minuter att importera.

> [!NOTE]
> Kostnadsfria användare kan ladda upp två datauppsättningar i taget. Standard-prenumerationsanvändare kan samtidigt överföra fem datauppsättningar. Om du når gränsen, vänta tills minst en av dina datauppsättningar har importerats, försök sedan igen.

När överföringen är klar, visas tabellen Mina Voice-Data igen. Du bör se en post som motsvarar till din datauppsättning precis överförs. 

Datauppsättningar verifieras automatiskt efter överföringen. Dataverifiering innehåller en rad kontroller på ljudfiler att verifiera sina filformat, storlek och samplingsfrekvensen. Kontrollerar om filerna som taltranskription verifiera filformatet och utföra vissa text normalisering. Talade transkriberas använder taligenkänning och den resulterande texten jämförs med avskrift som du angav.

![Min Voice-Data](media/custom-voice/my-voice-data.png)

I följande tabell visas bearbetning tillstånden för importerade datauppsättningar. 

| Status | Betydelse
| ----- | -------
| `NotStarted` | Din datauppsättning har tagits emot och är i kö för bearbetning
| `Running` | Din datauppsättning verifieras
| `Succeeded` | Din datauppsättning har verifierats och kan nu användas för att skapa en rösttyp

När verifieringen är klar visas det totala antalet matchande yttranden för var och en av dina datauppsättningar i kolumnen uttryck.

Du kan hämta en rapport för att kontrollera uttal poängen och bruset nivån för var och en av dina inspelningar. Uttal av poängen anges från 0 till 100. en poäng under 70 betyder vanligen att ett tal fel eller ett skript-matchningsfel. En tung accent kan minska dina uttal poäng och påverka genererade digitala röst.

Högre signal-brus förhållande (SNR) anger lägre bruset i dina ljud. Normalt kan du nå en 50 + SNR genom inspelningen via professionella studios. Ljud med en SNR nedan 20 kan leda till uppenbara bruset i din röst när du genererade.

Överväg att inspelningen yttranden med låg uttal poäng eller dålig signal brus förhållanden. Om det inte går att spela in igen, kan du undanta dessa yttranden från din datauppsättning.

## <a name="build-your-voice-font"></a>Skapa din rösttyp

När din datauppsättning har verifierats, kan du använda den för att skapa din anpassad rösttyp. 

1. Välj **modeller** i listrutan ”anpassade röst”. 
 
    Mina Rösttyper tabellen visas med alla anpassade rösttyper som du redan har skapat.

1. Klicka på **skapa röster** under rubriken tabell. 

    Sidan för att skapa en rösttyp visas. De aktuella nationella inställningarna visas i den första raden i tabellen. Ändra språk för att skapa en röst i ett annat språk. De nationella inställningarna måste vara samma som de datauppsättningar som används för att skapa röst.

1. Ange ett namn och beskrivning för att identifiera den här modellen som du gjorde när du har laddat upp din datauppsättning. 

    Namnet du anger här blir namnet som du använder för att ange röst i din begäran för talsyntes som en del av SSML indata, så du väljer noggrant. Endast bokstäver, siffror och skiljetecken tecken som '-', '_' '(',')' tillåts.

    Ett vanligt användningsområde för beskrivningsfältet är att anteckna namnen på de datauppsättningar som har använts för att skapa modellen.

1. Välj din rösttyp kön. Det måste matcha kön för datauppsättningen.

1. Välj de datauppsättningar som du vill använda för att träna rösttyp. Alla datauppsättningar som används måste komma från samma API.

1. Klicka på **skapa** att börja skapa din rösttyp.

    ![Skapa modell](media/custom-voice/create-model.png)

Den nya modellen visas i tabellen Mina Rösttyper. 

![Min Rösttyper](media/custom-voice/my-voice-fonts.png)

Det visade statusvärdet visar att konvertera datauppsättningen till en rösttyp som visas här.

| Status | Betydelse
| ----- | -------
| `NotStarted` | Din begäran för att skapa en röst teckensnittet är i kö för bearbetning
| `Running` | Din rösttyp håller på att skapas
| `Succeeded` | Din rösttyp har skapats och kan distribueras

Utbildning tiden varierar beroende på mängden bearbetade ljud data. Vanliga gånger mellan om 30 minuter för hundratals yttranden och 40 timmar för 20 000 yttranden.

> [!NOTE]
> Kostnadsfria användare kan träna en rösttyp i taget. Standard-prenumerationsanvändare kan träna tre röster samtidigt. Om du når gränsen, vänta tills minst en av dina rösttyper är klar utbildning och försök igen.

## <a name="test-your-voice-font"></a>Testa din rösttyp

När din rösttyp har har skapats, kan du testa den innan du distribuerar den för användning. Klicka på **Test** i kolumnen åtgärder. Testsidan visas för den valda rösttyp. Tabellen är tom om du ännu inte har skickat alla test-begäranden för röst.

![Min Rösttyper, del 2](media/custom-voice/my-voice-fonts2.png)

Klicka på **Test med text** knappen under tabelltitel så visas en popup-meny för att skicka begäranden om text. Du kan skicka din begäran för testning i oformaterad text eller SSML. Den maximala storleken för indata är 1 024 tecken, inklusive alla taggar för SSML begäran. Språk i texten måste vara samma som din rösttyp språk.

![Röst teckensnitt för testning](media/custom-voice/voice-font-testing.png)

När du fyller i textrutan och bekräftar läget som indata, klickar du på **Ja** att skicka din begäran för testning och återgå till testsidan. Tabellen innehåller nu en post som motsvarar din nya förfrågan och välbekanta statuskolumnen. Det kan ta några minuter att syntetisera tal. När statuskolumnen läser lyckades, du kan ladda ned textinmatningen (en `.txt` fil) och ljuduppspelning (en `.wav` fil) och lyssna det senare för kvalitet.

![Röst teckensnitt testning, del 2](media/custom-voice/voice-font-testing2.png)

## <a name="create-and-use-a-custom-endpoint"></a>Skapa och använda en anpassad slutpunkt

När du har skapat och testat din röst-modell, distribuerar du den i en anpassad Text till tal-slutpunkt. Du kan sedan använda den här slutpunkten i stället för slutpunkten som vanligt när Text till tal-förfrågningar via REST API. Din anpassade slutpunkt kan anropas bara av den prenumeration som du använde för att distribuera teckensnittet.

Om du vill skapa en ny anpassad slutpunkt, Välj **slutpunkter** anpassade röst-menyn längst upp på sidan. Sidan Mina distribueras röster visas med sin tabell av den aktuella anpassade röst slutpunkter, om sådana. De aktuella nationella inställningarna visas i den första raden i tabellen. Ändra det visade språket om du vill skapa en distribution för ett annat språk. (Det måste matcha röst du distribuerar.)

Klicka på den **distribuera röster** för att skapa en ny slutpunkt. Ange namn och beskrivning av din anpassade slutpunkt.

Välj den prenumeration som du vill använda på menyn prenumeration. Kostnadsfria användare kan ha endast en modell som distribuerats i taget. Standard användare kan skapa upp till 20 slutpunkter, var och en med sin egen anpassade röst.

![Skapa slutpunkt](media/custom-voice/create-endpoint.png)

När du har valt modellen distribueras, klickar du på **skapa**. Sidan Mina distribueras röster igen nu innehåller en post för din nya slutpunkt. Det kan ta några minuter att skapa en instans av en ny slutpunkt. När statusen för distributionen är slutfört är slutpunkten redo att användas.

![Min distribuerade röster](media/custom-voice/my-deployed-voices.png)

När distributionsstatusen är slutfört visas slutpunkten för din distribuerade rösttyp i tabellen Mina distribueras röster. Du kan använda den här URI: N direkt i en HTTP-begäran.

Online testning av slutpunkten är också tillgängligt via portalen anpassade röst. Om du vill testa din slutpunkt, Välj **slutpunkter testning** anpassad röst nedrullningsbara menyn. Slutpunkten testning sida visas. Välj en distribuerade anpassade röst och ange vilken text som ska läsas (i oformaterad text eller SSML format) i textrutan.

> [!NOTE] 
> När du använder SSML, den `<voice>` taggen måste ange det namn du gav din anpassade röst när du skapade den. Om du skickar in oformaterad text, används alltid anpassade röst.

Klicka på **spela upp** att höra texten som sägs i din anpassad rösttyp.

![Slutpunkt för testning](media/custom-voice/endpoint-testing.png)

Anpassad slutpunkt är funktionellt identisk standard slutpunkten som används för text till tal-begäranden. Se [REST API](rest-apis.md) för mer information.

## <a name="language-support"></a>Stöd för språk

Röst anpassning är tillgängligt för amerikansk engelska (en-US), fastlandet kinesiska (zh-CN) och italienska (it-IT).

> [!NOTE]
> Italienska ton börjar med en datauppsättning av över 2 000 yttranden. Kinesiska – engelska tvåspråkig modeller stöds också med en datauppsättning av över 2 000 yttranden.

## <a name="next-steps"></a>Nästa steg

- [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Känna igen tal i C#](quickstart-csharp-dotnet-windows.md)
