---
title: Anpassade fält i Azure Monitor (förhandsversion) | Microsoft-dokument
description: Med funktionen Anpassade fält i Azure Monitor kan du skapa egna sökbara fält från poster på en Log Analytics-arbetsyta som lägger till egenskaperna för en insamlad post.  I den här artikeln beskrivs processen för att skapa ett anpassat fält och en detaljerad genomgång med en exempelhändelse.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/23/2019
ms.openlocfilehash: bfb0a73631564c96a4af745fe9d7540a3a84f9c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655369"
---
# <a name="create-custom-fields-in-a-log-analytics-workspace-in-azure-monitor-preview"></a>Skapa anpassade fält på en Log Analytics-arbetsyta i Azure Monitor (förhandsversion)

> [!NOTE]
> I den här artikeln beskrivs hur du tolkar textdata på en Log Analytics-arbetsyta när de samlas in. Vi rekommenderar att du tolkar textdata i ett frågefilter när de har samlats in enligt vägledningen som beskrivs i [Textdata från Parse i Azure Monitor](../log-query/parse-text.md). Det ger flera fördelar jämfört med att använda anpassade fält.

Med funktionen **Anpassade fält** i Azure Monitor kan du utöka befintliga poster på arbetsytan Log Analytics genom att lägga till egna sökbara fält.  Anpassade fält fylls i automatiskt från data som extraheras från andra egenskaper i samma post.

![Översikt](media/custom-fields/overview.png)

Exempel på provposten nedan har till exempel användbara data begravda i händelsebeskrivningen. Genom att extrahera dessa data till en separat egenskap blir de tillgängliga för sådana åtgärder som sortering och filtrering.

![Provextrakt](media/custom-fields/sample-extract.png)

> [!NOTE]
> I förhandsgranskningen är du begränsad till 100 anpassade fält på arbetsytan.  Den här gränsen utökas när den här funktionen når allmän tillgänglighet.

## <a name="creating-a-custom-field"></a>Skapa ett anpassat fält
När du skapar ett anpassat fält måste Log Analytics förstå vilka data som ska användas för att fylla i dess värde.  Den använder en teknik från Microsoft Research som kallas FlashExtract för att snabbt identifiera dessa data.  I stället för att kräva att du ger uttryckliga instruktioner lär sig Azure Monitor om de data du vill extrahera från exempel som du tillhandahåller.

I följande avsnitt beskrivs proceduren för att skapa ett anpassat fält.  Längst ner i den här artikeln finns en genomgång av en exempelextrahering.

> [!NOTE]
> Det anpassade fältet fylls i när poster som matchar de angivna villkoren läggs till på log analytics-arbetsytan, så det visas bara på poster som samlats in när det anpassade fältet har skapats.  Det anpassade fältet läggs inte till i poster som redan finns i datalagret när det skapas.
> 

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>Steg 1 – Identifiera poster som har det anpassade fältet
Det första steget är att identifiera de poster som hämtar det anpassade fältet.  Du börjar med en [standardloggfråga](../log-query/log-query-overview.md) och väljer sedan en post som ska fungera som den modell som Azure Monitor ska lära sig av.  När du anger att du ska extrahera data till ett anpassat fält öppnas **guiden Fältextrahering** där du validerar och förfinar villkoren.

1. Gå till **Loggar** och använd en [fråga för att hämta de poster](../log-query/log-query-overview.md) som har det anpassade fältet.
2. Välj en post som Log Analytics ska använda för att fungera som en modell för att extrahera data för att fylla i det anpassade fältet.  Du kommer att identifiera de data som du vill extrahera från den här posten och Log Analytics använder den här informationen för att bestämma logiken för att fylla i det anpassade fältet för alla liknande poster.
3. Expandera postegenskaperna, klicka på ellipsen till vänster om postens övre egenskap och välj **Utdragfält från**.
4. **Guiden Fältextrahering** öppnas och den post du valde visas i kolumnen **Huvudexempel.**  Det anpassade fältet definieras för de poster som har samma värden i de egenskaper som är markerade.  
5. Om markeringen inte är exakt vad du vill använda markerar du ytterligare fält för att begränsa villkoren.  Om du vill ändra fältvärdena för villkoren måste du avbryta och välja en annan post som matchar de villkor du vill använda.

### <a name="step-2---perform-initial-extract"></a>Steg 2 - Utför det första extraktet.
När du har identifierat de poster som ska ha det anpassade fältet identifierar du de data som du vill extrahera.  Log Analytics använder den här informationen för att identifiera liknande mönster i liknande poster.  I steget efter detta kommer du att kunna validera resultaten och ge ytterligare information för Log Analytics att använda i sin analys.

1. Markera texten i exempelposten som du vill fylla i det anpassade fältet.  Du kommer då att presenteras med en dialogruta för att ange ett namn och en datatyp för fältet och för att utföra det första utdraget.  Tecknen ** \_CF** läggs automatiskt till.
2. Klicka på **Extrahera** om du vill utföra en analys av insamlade poster.  
3. I avsnitten **Sammanfattning** **och Sökresultat** visas resultatet av extraktet så att du kan kontrollera dess riktighet.  **Sammanfattning visar** de kriterier som används för att identifiera poster och ett antal för vart och ett av de identifierade datavärdena.  **Sökresultaten** innehåller en detaljerad lista över poster som matchar villkoren.

### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>Steg 3 – Kontrollera att extraktet är korrekt och skapa anpassat fält
När du har utfört det första extraktet visar Log Analytics sina resultat baserat på data som redan har samlats in.  Om resultaten ser korrekta ut kan du skapa det anpassade fältet utan ytterligare arbete.  Om inte, kan du förfina resultaten så att Log Analytics kan förbättra dess logik.

1. Om några värden i det första extraktet inte är korrekta klickar du på ikonen **Redigera** bredvid en felaktig post och väljer **Ändra den här markeringen** för att ändra markeringen.
2. Posten kopieras till avsnittet **Ytterligare exempel** under **huvudexemplet**.  Du kan justera markeringen här för att hjälpa Log Analytics att förstå det val som den borde ha gjort.
3. Klicka på **Extrahera** om du vill använda den här nya informationen för att utvärdera alla befintliga poster.  Resultaten kan ändras för andra poster än den du just ändrat baserat på den här nya informationen.
4. Fortsätt att lägga till korrigeringar tills alla poster i utdraget identifierar de data som ska fyllas i det nya anpassade fältet.
5. Klicka på **Spara utdrag** när du är nöjd med resultatet.  Det anpassade fältet har nu definierats, men det läggs inte till i några poster ännu.
6. Vänta tills nya poster som matchar de angivna villkoren ska samlas in och kör sedan loggsökningen igen. Nya poster bör ha det anpassade fältet.
7. Använd det anpassade fältet som vilken annan postegenskap som helst.  Du kan använda den för att sammanställa och gruppera data och till och med använda den för att skapa nya insikter.

## <a name="viewing-custom-fields"></a>Visa anpassade fält
Du kan visa en lista över alla anpassade fält i **hanteringsgruppen** på menyn Avancerade inställningar på din Log Analytics-arbetsyta i Azure-portalen.  Välj **Data** och sedan **Anpassade fält** för en lista över alla anpassade fält på arbetsytan.  

![Anpassade fält](media/custom-fields/list.png)

## <a name="removing-a-custom-field"></a>Ta bort ett anpassat fält
Det finns två sätt att ta bort ett anpassat fält.  Den första är alternativet **Ta bort** för varje fält när du visar hela listan enligt beskrivningen ovan.  Den andra metoden är att hämta en post och klicka på knappen till vänster om fältet.  Menyn har ett alternativ för att ta bort det anpassade fältet.

## <a name="sample-walkthrough"></a>Exempelgenomgång
I följande avsnitt går du igenom ett fullständigt exempel på hur du skapar ett anpassat fält.  I det här exemplet extraheras tjänstnamnet i Windows-händelser som anger ett tillstånd för att ändra tjänsten.  Detta är beroende av händelser som skapats av Service Control Manager under systemstart på Windows-datorer.  Om du vill följa det här exemplet måste du [samla in informationshändelser för systemloggen](data-sources-windows-events.md).

Vi anger följande fråga för att returnera alla händelser från Service Control Manager som har ett händelse-ID på 7036 som är den händelse som anger en tjänst som startar eller stoppar.

![Söka i data](media/custom-fields/query.png)

Vi väljer sedan och expanderar alla poster med händelse-ID 7036.

![Källpost](media/custom-fields/source-record.png)

Vi definierar anpassade fält genom att klicka på ellipsen bredvid den översta egenskapen.

![Extrahera fält](media/custom-fields/extract-fields.png)

**Guiden Fältextrahering** öppnas och fälten **EventLog** och **EventID** markeras i kolumnen **Huvudexempel.**  Detta indikerar att det anpassade fältet kommer att definieras för händelser från systemloggen med ett händelse-ID på 7036.  Detta är tillräckligt så att vi inte behöver välja några andra fält.

![Huvudexempel](media/custom-fields/main-example.png)

Vi markerar namnet på tjänsten i egenskapen **RenderedDescription** och använder **Tjänst** för att identifiera tjänstnamnet.  Det anpassade fältet kallas **Service_CF**. Fälttypen i det här fallet är en sträng, så vi kan lämna den oförändrad.

![Fältrubrik](media/custom-fields/field-title.png)

Vi ser att tjänstnamnet identifieras korrekt för vissa poster men inte för andra.   **Sökresultaten** visar att en del av namnet på **WMI-prestandakortet** inte har valts.  **Sammanfattningen** visar att en post identifierade **Modules Installer** i stället för **Windows Modules Installer**.  

![Sökresultat](media/custom-fields/search-results-01.png)

Vi börjar med **WMI Performance** Adapter-posten.  Vi klickar på dess redigeringsikon och **sedan ändra denna höjdpunkt**.  

![Ändra markering](media/custom-fields/modify-highlight.png)

Vi ökar höjdpunkten för att inkludera ordet **WMI** och sedan köra extraktet igen.  

![Ytterligare exempel](media/custom-fields/additional-example-01.png)

Vi kan se att posterna för **WMI-prestandaadapter** har korrigerats och Log Analytics använde också den informationen för att korrigera posterna för **Windows Module Installer**.

![Sökresultat](media/custom-fields/search-results-02.png)

Vi kan nu köra en fråga som verifierar **Service_CF** skapas men ännu inte har lagts till i några poster. Det beror på att det anpassade fältet inte fungerar mot befintliga poster så vi måste vänta på att nya poster ska samlas in.

![Inledande antal](media/custom-fields/initial-count.png)

Efter en tid har gått så att nya händelser samlas in kan vi se att **fältet Service_CF** nu läggs till i poster som matchar våra kriterier.

![Slutliga resultat](media/custom-fields/final-results.png)

Vi kan nu använda det anpassade fältet som alla andra postegenskaper.  För att illustrera detta skapar vi en fråga som grupperar efter det nya **fältet Service_CF** för att granska vilka tjänster som är mest aktiva.

![Grupp för fråga](media/custom-fields/query-group.png)

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [loggfrågor](../log-query/log-query-overview.md) för att skapa frågor med hjälp av anpassade fält för villkor.
* Övervaka [anpassade loggfiler](data-sources-custom-logs.md) som du tolkar med hjälp av anpassade fält.

