---
title: Azure Monitor-arbetsböcker med anpassade parametrar
description: Förenkla komplex rapportering med färdiga och anpassade parameteriserade arbetsböcker
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 4d9f6e48722f01970a90a3a1d8d8b58b5d939774
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658285"
---
# <a name="interactive-workbooks"></a>Interaktiva arbetsböcker

Arbetsböcker gör det möjligt för författare att skapa interaktiva rapporter och upplevelser för sina konsumenter. Interaktivitet stöds på flera olika sätt.

## <a name="parameter-changes"></a>Ändringar av parametern
När en arbetsboksanvändare uppdaterar en parameter uppdateras och ritas en kontroll som använder parametern automatiskt upp och ritas om för att återspegla det nya tillståndet. Så här stöder de flesta Azure-portalrapporter interaktivitet. Arbetsböcker ger detta på ett mycket rakt fram sätt med minimal användaransträngning.

Läs mer om [Parametrar i arbetsböcker](workbooks-parameters.md)

## <a name="grid-row-clicks"></a>Klick på rutnätsrad
Arbetsböcker gör det möjligt för författare att skapa scenarier där om du klickar på en rad i ett rutnät uppdateras efterföljande diagram baserat på innehållet på raden. 

En användare kan till exempel ha ett rutnät som visar en lista över begäranden och viss statistik som felantal. De kan ställa in det så att klicka på en rad som motsvarar en begäran, kommer att resultera i detaljerade diagram nedan uppdatera för att filtrera ner till just denna begäran.

### <a name="setting-up-interactivity-on-grid-row-click"></a>Ställa in interaktivitet på rutnätsradens klick
1. Växla arbetsboken till redigeringsläge genom att klicka på alternativet _Redigera_ verktygsfält.
2. Använd länken _Lägg till fråga_ om du vill lägga till en loggfrågekontroll i arbetsboken. 
3. Välj frågetypen som _Log_, resurstyp (till exempel Application Insights) och de resurser som ska riktas.
4. Använd Frågeredigeraren för att ange KQL för din analys
    ```kusto
    requests
    | summarize AllRequests = count(), FailedRequests = countif(success == false) by Request = name
    | order by AllRequests desc    
    ```
5. `Run query`för att se resultaten
6. Klicka på ikonen _Avancerade inställningar_ på frågefoten (ikonen ser ut som en växel). Då öppnas fönstret avancerade inställningar 
7. Kontrollera inställningen:`When an item is selected, export a parameter`
    1. Fält att exportera:`Request`
    2. Parameternamn:`SelectedRequest`
    3. Standardvärdet:`All requests`
    
    ![Bild som visar den avancerade redigeraren med inställningar för export av fält som parametrar](./media/workbooks-interactive/advanced-settings.png)

8. Klicka på `Done Editing`.
9. Lägg till ytterligare en frågekontroll med steg 2 och 3.
10. Använd Frågeredigeraren för att ange KQL för din analys
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
11. `Run query`för att se resultaten.
12. Ändra _visualisering_ till`Area chart`
12. Klicka på en rad i det första rutnätet. Observera hur ytdiagrammet nedan filtrerar till den valda begäran.

Den resulterande rapporten ser ut så här i redigeringsläge:

![Bild som visar skapandet av en interaktiv upplevelse med hjälp av rutnätsradklick](./media/workbooks-interactive//grid-click-create.png)

Bilden nedan visar en mer detaljerad interaktiv rapport i läsläge baserat på samma principer. Rapporten använder rutnätsklick för att exportera parametrar – som i sin tur används i två diagram och ett textblock.

![Bild som visar skapandet av en interaktiv upplevelse med hjälp av rutnätsradklick](./media/workbooks-interactive/grid-click-read-mode.png)

### <a name="exporting-the-contents-of-an-entire-row"></a>Exportera innehållet i en hel rad
Ibland är det önskvärt att exportera hela innehållet på den markerade raden i stället för bara en viss kolumn. I sådana fall, `Field to export` lämna fastigheten okopplad i steg 7.1 ovan. Arbetsböcker exporterar hela radinnehållet som ett skämt till parametern. 

På den refererande KQL-kontrollen `todynamic` använder du funktionen för att tolka json och komma åt de enskilda kolumnerna.

 ## <a name="grid-cell-clicks"></a>Klick på rutnätscell
Arbetsböcker gör det möjligt för författare att lägga till interaktivitet via en särskild typ av rutnätskolumnrenader som kallas `link renderer`. En länkåtergivning konverterar en rutnätscell till en hyperlänk baserat på cellens innehåll. Arbetsböcker stöder många typer av länkrendare – inklusive sådana som gör det möjligt att öppna resursöversiktsblad, fastighetsskasse-tittare, appstatistiksökning, användning, transaktionsspårning osv.

### <a name="setting-up-interactivity-using-grid-cell-clicks"></a>Ställa in interaktivitet med rutnätscellklick
1. Växla arbetsboken till redigeringsläge genom att klicka på alternativet _Redigera_ verktygsfält.
2. Använd länken _Lägg till fråga_ om du vill lägga till en loggfrågekontroll i arbetsboken. 
3. Välj frågetypen som _Log_, resurstyp (till exempel Application Insights) och de resurser som ska riktas.
4. Använd Frågeredigeraren för att ange KQL för din analys
    ```kusto
    requests
    | summarize Count = count(), Sample = any(pack_all()) by Request = name
    | order by Count desc
    ```
5. `Run query`för att se resultaten
6. Öppna inställningsfönstret genom att klicka _på Kolumninställningar._
7. I avsnittet _Kolumner_ anger du:
    1. _Exempel_ - Kolumnåtergivning: `Link`, Visa `Cell Details`för att öppna: , Länketikett:`Sample`
    2. _Antal_ - Kolumnåtergivning: `Bar`, Färgpalett: `Blue`, Lägsta värde:`0`
    3. _Begäran_ - Kolumnåtergivning:`Automatic`
    4. Klicka på _Spara och stäng_ om du vill tillämpa ändringar
8. Klicka på en `Sample` av länkarna i rutnätet. Då öppnas en egenskapsruta med information om en exempelbegäran.

    ![Bild som visar skapandet av en interaktiv upplevelse med rutnätscellklick](./media/workbooks-interactive/grid-cell-click-create.png)

### <a name="link-renderer-actions"></a>Åtgärder för länkåtergivning
| Länka åtgärd | Åtgärd vid klickning |
|:------------- |:-------------|
| `Generic Details` | Visar radvärdena i ett kontextblad för egenskapsrutnät |
| `Cell Details` | Visar cellvärdet i ett kontextblad för egenskapsstöd. Användbart när cellen innehåller en dynamisk typ med information (till exempel json med begäranegenskaper som plats, rollinstans osv.). |
| `Cell Details` | Visar cellvärdet i ett kontextblad för egenskapsstöd. Användbart när cellen innehåller en dynamisk typ med information (till exempel json med begäranegenskaper som plats, rollinstans osv.). |
| `Custom Event Details` | Öppnar sökinformationen för Application Insights med det anpassade händelse-ID:t (itemId) i cellen |
| `* Details` | Liknar anpassad händelseinformation, med undantag för beroenden, undantag, sidvisningar, begäranden och spårningar. |
| `Custom Event User Flows` | Öppnar användarflöden för programinsikter som är pivoterade på det anpassade händelsenamnet i cellen |
| `* User Flows` | Liknar anpassade händelseanvändarflöden förutom undantag, sidvisningar och begäranden |
| `User Timeline` | Öppnar användarens tidslinje med användar-ID (user_Id) i cellen |
| `Session Timeline` | Öppnar sökupplevelsen för Application Insights efter värdet i cellen (sök till exempel efter texten 'abc' där abc är värdet i cellen) |
| `Resource overview` | Öppna resursens översikt i portalen baserat på resurs-ID-värdet i cellen |

## <a name="conditional-visibility"></a>Villkorlig synlighet
Arbetsboken gör det möjligt för användare att se till att vissa kontroller visas eller försvinner baserat på värden för parametrarna. Detta gör att författare kan ha rapporter ser annorlunda ut baserat på användarindata eller telemetri tillstånd. Ett exempel är att visa konsumenterna bara en sammanfattning när saker och ting är bra men visa fullständig information när något är fel.

### <a name="setting-up-interactivity-using-conditional-visibility"></a>Ställa in interaktivitet med villkorad synlighet
1. Följ stegen i `Setting up interactivity on grid row click` avsnittet för att konfigurera två interaktiva kontroller.
2. Lägg till en ny parameter högst upp:
    1. Namn:`ShowDetails`
    2. Parametertyp:`Drop down`
    3. Krävs:`checked`
    4. Hämta data från:`JSON`
    5. JSON Ingång:`["Yes", "No"]`
    6. Spara för att genomföra ändringar.
3. Ange parametervärde till`Yes`
4. Klicka på ikonen _Avancerade inställningar (kugghjulsikonen)_ i frågekontrollen med områdesdiagrammet.
5. Kontrollera inställningen`Make this item conditionally visible`
    1. Det här objektet `ShowDetails` är `equals` synligt om parametervärdet`Yes`
6. Klicka på _Klar redigering_ om du vill genomföra ändringar.
7. Klicka på _Klar redigering_ i verktygsfältet för arbetsbok för att gå in i läsläge.
8. Växla parametervärdet `ShowDetails` till `No`. Observera att diagrammet nedan försvinner.

Bilden nedan visar det `ShowDetails` synliga fallet där`Yes`

![Bild som visar den villkorliga synligheten där diagrammet är synligt](./media/workbooks-interactive/conditional-visibility.png)

Bilden nedan visar det `ShowDetails` dolda fallet där det finns`No`

![Bild som visar den villkorliga synligheten där diagrammet är dolt](./media/workbooks-interactive/conditional-invisible.png)

## <a name="next-steps"></a>Nästa steg


* [Kom igång](workbooks-visualizations.md) med att lära dig mer om arbetsböcker många avancerade visualiseringar alternativ.
* [Kontrollera](workbooks-access-control.md) och dela åtkomst till arbetsboksresurserna.
