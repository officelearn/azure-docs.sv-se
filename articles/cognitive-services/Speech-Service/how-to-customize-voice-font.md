---
title: Vad är anpassad röst? -Kognitiva azure-tjänster | Microsoft Docs
description: Den här artikeln översikter Microsoft Text till tal röst anpassning, där du kan skapa en går att känna igen, en för en slag varumärken röst.
services: cognitive-services
author: noellelacharite
manager: nolach
ms.service: cognitive-services
ms.topic: article
ms.date: 05/07/2018
ms.author: nolach
ms.openlocfilehash: ad5af799fd46dc51b85432999f986de8cdb056ec
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355095"
---
# <a name="creating-custom-voice-fonts"></a>Skapa anpassade röst teckensnitt

Microsoft Text till tal (TTS) röst anpassning kan du skapa en röst för att känna igen, en av en typ för ditt varumärke: en *röst teckensnitt.* 

För att skapa din röst teckensnitt, gör en studio registrering och ladda upp de associerade skript som utbildning data. Tjänsten skapar sedan en unik röst modell inställda för din registrering. Du kan sedan använda det här teckensnittet röst för att syntetisera tal. 

Du kan komma igång med en liten mängd data för ett konceptbevis. Men ju mer data du tillhandahålla, desto naturliga och professional låter din röst.

Röst anpassning är tillgängliga för amerikansk engelska (en-US) och fastlandet kinesiska (zh-CN).

## <a name="prerequisites"></a>Förutsättningar

Text till tal röst anpassning av funktionen är för tillfället i privat förhandsvisning. [Fyll i formuläret programmet](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0N8Vcdi8MZBllkZb70o6KdURjRaUzhBVkhUNklCUEMxU0tQMEFPMjVHVi4u) beaktas för åtkomst.

Du behöver också:

* Ett Azure-konto ([registrera dig för gratis](https://azure.microsoft.com/free/ai/) om du ännu inte har en).

* En prenumeration på tjänsten tal. [Skapa en](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices) om du inte redan har gjort.

    ![Skapa panelen](media/custom-voice/create-panel.png)

När du har skapat prenumerationen hittar du två prenumeration nycklar på panelen för Snabbstart eller översikt av den nya prenumerationen. Du kan använda antingen nyckel.

Slutligen ansluta din prenumeration till anpassad röst-portal på följande sätt.

1. Logga in på den [anpassad röst portal](https://customvoice.ai) med samma Microsoft-konto som du använde för att använda för åtkomst.

2. Gå till prenumerationer om du under namnet på ditt konto på upp till höger.

    ![Prenumerationer](media/custom-voice/subscriptions.png)

3. Välj Anslut befintliga prenumeration på '' prenumerationssidan.

     ![Ansluta befintliga prenumeration](media/custom-voice/connect-existing-sub.png)

4. Klistra in din prenumeration nyckel i tabellen, enligt nedan.

     ![Lägg till prenumeration](media/custom-voice/add-subscription.png)

Du är redo att sätta igång!

## <a name="prepare-recordings-and-transcripts"></a>Förbereda inspelningar och betyg

En röst utbildning dataset består av en uppsättning ljudfiler, tillsammans med en textfil som innehåller betyg i alla dessa ljudfiler.

Du kan förbereda filerna i vardera riktning: antingen skriva ett skript och läses av röst användare eller använda offentligt tillgängliga ljud och transkribera dem till text. Redigera disfluencies från ljudfiler, till exempel ”um” och andra information ljud, hackar, mumbled ord eller mispronunciations i det senare fallet.

För att skapa ett bra röst teckensnitt, är det viktigt att inspelningen är klar i ett tyst rum med en mikrofon av hög kvalitet. Konsekvent volym tala hastighet, tala breddsteg och lättfattliga maner, ovanor av tal är nödvändiga för att skapa en bra digitala röst. Om du vill skapa en röst för produktion, rekommenderar vi att du använder en professional spela in studio och röst användare.

### <a name="audio-files"></a>Ljudfiler

Varje ljudfil ska innehålla en enda utterance (till exempel en mening eller en enda Stäng dialogrutan System). Alla filer måste vara på samma språk (flerspråkig anpassade röster inte stöds). Ljudfiler måste också ha ett unikt numeriska filnamn med filnamnstillägget `.wav`.

Ljudfiler bör vara beredd på följande sätt. Andra format stöds inte och ignorerades.

| **Egenskap** | **Värde** |
| ------------ | --------- |
| Filformat  | RIFF (WAV)|
| Samplingsfrekvens| 16 000 Hz |
| Kanaler     | 1 (monophonic)  |
| Exempel-Format| PCM, 16-bitars |
| Filnamn    | Numeriska med `.wav` tillägg |
| Arkivformat| ZIP-      |
| Maximal arkivstorlek|200 MB|

Placera uppsättningen ljudfiler i en enda mapp utan underkataloger och hela uppsättningen som ett enda filarkiv ZIP-paketet.

> [!NOTE]
> Portalen för närvarande importerar ZIP arkiveras upp till 200 MB. Flera Arkiv kan laddas upp. Det maximala antalet tillåtna datauppsättningar är 10 ZIP kostnadsfritt filer användare och 50 för standard användare.

### <a name="transcripts"></a>Betyg

Skrivfel-filen är en vanlig Unicode-textfil (lite UTF-16-endian). Varje rad i filen skrivfel måste ha namnet på en ljudfil, följt av ett tabbtecken (kodpunkt 9) och slutligen dess betyg. Inga tomma rader är tillåtna.

Exempel:

```
0000000001  This is the waistline, and it's falling.
0000000002  We have trouble scoring.
0000000003  It was Janet Maslin.
```

Anpassade röst systemet normaliserar betyg genom konvertera texten till gemener och ta bort överflödig skiljetecken. Det är viktigt att betyg är 100% korrekt med motsvarande ljudinspelningar.

> [!TIP]
> När skapa produktion text till tal röster, Välj utterances (eller skriva skript) med tanke på både fonetisk täckning och effektivitet.

## <a name="upload-your-datasets"></a>Ladda upp dina datauppsättningar

När du har förberett din ljudfil arkivet och betyg överför dem via den [anpassad röst Tjänstportalen](https://customvoice.ai).

> [!NOTE]
> Datauppsättningar kan inte ändras när de har överförts. Om du har glömt att inkludera betyg på några av ljudfilerna, till exempel eller oavsiktligt väljer fel kön, måste du överföra hela datamängden igen. Kontrollera dina dataset och inställningar noggrant innan du börjar överföringen.

1. Logga in på portalen.

2. Välj **Data** under Anpassad röst på huvudsidan. 

    ![Mina projekt](media/custom-voice/my-projects.png)

    Min röst tabellen visas. Den är tom om du inte ännu har överfört en röst datauppsättningar.

3. Klicka på **dataimport** och gå till sidan för att hämta en ny datamängd.

    ![Importera Voice-Data](media/custom-voice/import-voice-data.png)

4. Ange ett namn och en beskrivning i fälten. 

5. Välj språk för röst-teckensnitt. Kontrollera att språkinformationen matchar språket för spela in data och skript. 

6. Välj kön på högtalaren vars röst som du använder.

7. Välj skript och ljudfiler att överföra. 

8. Klicka på **importera** att överföra data. För större datamängder kan det ta flera minuter att importera.

> [!NOTE]
> Kostnadsfri prenumeration som användarna kan ladda upp två datamängder i taget. Vanliga användare kan samtidigt överföra fem datauppsättningar. Om du når gränsen, vänta tills minst en av dina datauppsättningar har importerats och försök igen.

När överföringen är klar visas Mina röst tabellen igen. Du bör se en post som motsvarar din bara upp DataSet. 

Datauppsättningar verifieras automatiskt efter överföringen. Dataverifiering innehåller en rad kontroller på ljudfiler att verifiera sina filformat, storlek och samplingsfrekvensen. Kontroller av skrivfel filerna Kontrollera filformatet och utföra vissa text normalisering. Utterances är sätt med hjälp av taligenkänning och den resulterande texten jämförs med betyg som du angav.

![Mina Voice-Data](media/custom-voice/my-voice-data.png)

Följande tabell innehåller de bearbetning för importerade datauppsättningar. 

| Status | Betydelse
| ----- | -------
| `NotStarted` | Datamängden har tagits emot och är i kö för bearbetning
| `Running` | Datauppsättningens verifieras
| `Succeeded` | Datamängden har verifierats och kan nu användas för att bygga en röst teckensnitt

När verifieringen är klar kan se du det totala antalet matchade utterances för var och en av dina datauppsättningar i kolumnen Utterance.

Du kan hämta en rapport för att kontrollera uttal poängen och nivån bruset för var och en av dina inspelningar. Uttal poäng intervall från 0 till 100. en poäng nedan 70 betyder vanligen att ett tal fel eller ett skript-matchningsfel. En tung accent kan minska uttal poängen och påverkar den genererade digitala rösten.

Ett högre signal-brus-förhållande (SNR) anger lägre bruset i ljudenheten. Vanligtvis kan du nå 50 + SNR genom registrering via professional studios. Ljud med en SNR nedan 20 kan leda till uppenbara bruset i rösten genererade.

Överväg att inspelningen eventuella utterances med låg uttal resultat eller dålig signal brus förhållanden. Om det inte går att inspelningen, kan du undanta dessa utterances från datamängden.

## <a name="build-your-voice-font"></a>Skapa din röst teckensnitt

När din datauppsättning har verifierats, kan du använda den för att skapa anpassade röst-teckensnitt. 

1. Välj **modeller** i listrutan ”anpassad röst”. 
 
    Tabellen min röst teckensnitt visas med eventuella anpassade röst teckensnitt som du redan har skapat.

1. Klicka på **skapa röster** under rubriken för tabellen. 

    Sidan för att skapa en röst teckensnitt visas. Språket visas i den första raden i tabellen. Ändra språk för att skapa en röst på ett annat språk. Språk måste vara samma som de datauppsättningar som används för att skapa rösten.

1. Ange ett namn och en beskrivning som hjälper dig att identifiera den här modellen som du gjorde när du har överfört din datauppsättning. 

    Namnet som du anger här kommer att namnet du använder för att ange röst i din begäran om tal sammanfattande som en del av SSML-indata, så Välj noggrant. Endast bokstäver, siffror och skiljetecken tecken som '-', '_' '(',')' är tillåtna.

    Ett vanligt användningsområde för beskrivningsfältet är att anteckna namnen på de datamängder som användes för att skapa modellen.

1. Välj kön för röst-teckensnitt. Den måste matcha kön för datauppsättningen.

1. Välj datauppsättning/ar som du vill använda för att träna teckensnittet röst. Alla datauppsättningar som används måste vara från samma högtalaren.

1. Klicka på **skapa** att börja skapa röst-teckensnitt.

    ![Skapa modell](media/custom-voice/create-model.png)

Din nya modell visas i tabellen min röst teckensnitt. 

![Röst teckensnitt](media/custom-voice/my-voice-fonts.png)

Status visas visar att konvertera din datauppsättning till en röst teckensnitt som visas här.

| Status | Betydelse
| ----- | -------
| `NotStarted` | Din begäran för att skapa en röst teckensnittet är i kö för bearbetning
| `Running` | Teckensnittet röst skapas
| `Succeeded` | Teckensnittet röst har skapats och kan distribueras

Utbildning tid varierar beroende på mängden bearbetade ljud data. Vanliga gånger intervallet om 30 minuter för hundratals utterances till 40 timmar för 20 000 utterances.

> [!NOTE]
> Fria användare kan träna två röst teckensnitt i taget. Vanliga användare kan träna tre röster samtidigt. Om du når gränsen, vänta tills minst en röst teckensnitt är färdigt utbildning och försök igen.

## <a name="test-your-voice-font"></a>Testa din röst teckensnitt

När röst teckensnittet är har skapat kan testa du den innan du distribuerar den för användning. Klicka på **Test** i kolumnen åtgärder. Testsidan visas för det valda rösten teckensnittet. Tabellen är tom om du ännu inte har skickats alla test-förfrågningar för röst.

![Min röst teckensnitt, del 2](media/custom-voice/my-voice-fonts2.png)

Klicka på **Test med text** knappen under rubriken tabellen ska visas ett popup-menyn för att skicka begäranden som text. Du kan skicka testbegäran i oformaterad text eller SSML. Maximal inkommande storlek är 1 024 tecken, inklusive alla taggar för SSML-begäran. Språket i texten måste vara samma som språket för röst-teckensnitt.

![Röst teckensnitt testning](media/custom-voice/voice-font-testing.png)

När du fyller i textrutan och bekräfta det inkommande läget, klickar du på **Ja** att skicka testbegäran och återgå till testsidan. Tabellen innehåller nu en post som motsvarar din begäran om ny och välbekanta status-kolumnen. Det kan ta några minuter att syntetisera tal. När i statuskolumnen läser lyckades, kan du hämta text-indata (en `.txt` filen) och ljuduppspelning (en `.wav` filen) och lyssna denna för kvalitet.

![Röst teckensnitt testning, del 2](media/custom-voice/voice-font-testing2.png)

## <a name="create-and-use-a-custom-endpoint"></a>Skapa och använda en anpassad slutpunkt

När du har skapat och testat din röst-modell, distribuerar du den i en anpassad Text till tal-slutpunkt. Du kan sedan använda den här slutpunkten i stället för vanliga slutpunkten vid Text till tal-begäranden via REST API. Din anpassade slutpunkt kan bara anropas av den prenumeration som du använde för att distribuera teckensnitt.

Om du vill skapa en ny anpassad slutpunkt, Välj **slutpunkter** på menyn anpassade röst överst på sidan. Sidan distributionen visas med sin tabell aktuella anpassade röst slutpunkter, eventuella.

Klicka på den **distribuera röster** för att skapa en ny slutpunkt. I skapa-slutpunkten ”sidan språket avspeglas i den första raden i tabellen. Ändra visas om du vill skapa en distribution för ett annat språk. (Den måste matcha den rösten som du distribuerar.) Ange namn och beskrivning av din anpassade slutpunkt.

Välj den prenumeration som du vill använda på menyn prenumeration. Kostnadsfri prenumeration som användare kan ha endast en modell som har distribuerats i taget. Vanliga användare kan skapa upp till 20 slutpunkter, var och en med sin egen anpassade röst.

![Skapa slutpunkten](media/custom-voice/create-endpoint.png)

När du har valt modellen som ska distribueras, klickar du på **skapa**. Sidan distribution igen nu med en post för din nya slutpunkt. Det kan ta några minuter att skapa en instans av en ny slutpunkt. Slutpunkten är redo för användning när statusen för distributionen är klar.

![Min distribuerade röster](media/custom-voice/my-deployed-voices.png)

När distributionsstatusen är klar slutpunkten för distribuerade röst teckensnittet visas i den distribuerade röster tabellen. Du kan använda den här URI direkt i en HTTP-begäran.

Online testning av slutpunkten är också tillgängliga via den anpassa röst-portalen. Om du vill testa din slutpunkt, Välj **slutpunkter testning** från listrutan anpassad röst. Slutpunkten testa sidan visas. Välj en röst som du har distribuerat och ange texten som ska läsas (i oformaterad text eller SSML-format) i textrutan.

> [!NOTE] 
> När du använder SSML, den `<voice>` taggen måste ange det namn du gav din anpassade röst när du skapade den.

Klicka på **spela upp** höra texten talas i din anpassade röst teckensnitt.

![Testning av slutpunkten](media/custom-voice/endpoint-testing.png)

Anpassade slutpunkten är funktionellt identiskt med standardslutpunkten används för text till tal-begäranden. Se [REST API](rest-apis.md) för mer information.

## <a name="next-steps"></a>Nästa steg

- [Hämta din utvärderingsprenumeration tal](https://azure.microsoft.com/try/cognitive-services/)
- [Identifiera tal i C#](quickstart-csharp-windows.md)
