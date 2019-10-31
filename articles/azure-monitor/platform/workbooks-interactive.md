---
title: Skapa interaktiva rapporter med anpassade parametrar för Azure Monitor arbets böcker | Microsoft-dokument
description: Förenkla komplex rapportering med förbyggda och anpassade parameterstyrda arbets böcker
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: eeb6eb5d8b3ad6498ff90a9afe1fa4f2c18d30e5
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73165958"
---
# <a name="interactive-workbooks"></a>Interaktiva arbets böcker

Med arbets böcker kan författare skapa interaktiva rapporter och upplevelser för sina konsumenter. Interaktivitet stöds på flera sätt.

## <a name="parameter-changes"></a>Parameter ändringar
När en arbets boks användare uppdaterar en parameter uppdateras alla kontroller som använder parametern automatiskt och ritas om för att återspegla det nya läget. Så här fungerar de flesta av Azure Portal-rapporterna stöd för interaktivitet. Arbets böcker ger detta på ett mycket rakt sätt med minimal användar ansträngning.

Lär dig mer om [parametrar i arbets böcker](workbooks-parameters.md)

## <a name="grid-row-clicks"></a>Rutnäts rad klickningar
Med arbets böcker kan författare skapa scenarier där man klickar på en rad i ett rutnät uppdaterar efterföljande diagram baserat på radens innehåll. 

En användare kan till exempel ha ett rutnät som visar en lista med begär Anden och vissa statistik som antal misslyckade. De kan konfigureras så att de klickar på en rad som motsvarar en begäran, vilket leder till att detaljerade diagram nedan uppdaterar för att filtrera ned till just den begäran.

### <a name="setting-up-interactivity-on-grid-row-click"></a>Konfigurera interaktivitet i rutnäts rad klickar du på
1. Ändra arbets boken till redigerings läge genom att klicka på objektet _Redigera_ verktygsfält.
2. Använd länken _Lägg till fråga_ för att lägga till en logg frågas kontroll i arbets boken. 
3. Välj typ av fråga som _logg_, resurs typ (till exempel Application Insights) och de resurser som ska riktas mot målet.
4. Använd Frågeredigeraren för att ange KQL för din analys
    ```kusto
    requests
    | summarize AllRequests = count(), FailedRequests = countif(success == false) by Request = name
    | order by AllRequests desc    
    ```
5. `Run query` för att se resultatet
6. Klicka på ikonen _Avancerade inställningar_ i frågans sidfot (ikonen ser ut som ett kugg hjul). Då öppnas fönstret avancerade inställningar 
7. Kontrol lera inställningen: `When an item is selected, export a parameter`
    1. Fält som ska exporteras: `Request`
    2. Parameter namn: `SelectedRequest`
    3. Standardvärde: `All requests`
    
    ![Bild som visar avancerad redigerare med inställningar för att exportera fält som parametrar](./media/workbooks-interactive/advanced-settings.png)

8. Klicka på `Done Editing`.
9. Lägg till en annan fråges kontroll med steg 2 och 3.
10. Använd Frågeredigeraren för att ange KQL för din analys
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
11. `Run query` för att se resultatet.
12. Ändra _visualisering_ till `Area chart`
12. Klicka på en rad i det första rutnätet. Observera hur ytdiagram nedan filtrerar till den valda begäran.

Den resulterande rapporten ser ut så här i redigerings läge:

![Bild som visar hur du skapar en interaktiv upplevelse med rutnäts rad klickningar](./media/workbooks-interactive//grid-click-create.png)

Bilden nedan visar en mer avancerad interaktiv rapport i Läs läge baserat på samma principer. I rapporten används rutnäts klickningar för att exportera parametrar, vilket i sin tur används i två diagram och ett textblock.

![Bild som visar hur du skapar en interaktiv upplevelse med rutnäts rad klickningar](./media/workbooks-interactive/grid-click-read-mode.png)

### <a name="exporting-the-contents-of-an-entire-row"></a>Exportera innehållet i en hel rad
Ibland är det önskvärt att exportera hela innehållet i den markerade raden i stället för bara en viss kolumn. I sådana fall lämnar du `Field to export` egenskap unset i steg 7,1 ovan. I arbets böckerna exporteras hela rad innehållet som en JSON-parameter till-parametern. 

Använd funktionen `todynamic` för att parsa JSON och få åtkomst till de enskilda kolumnerna på den refererande KQL-kontrollen.

 ## <a name="grid-cell-clicks"></a>Rutnäts cell klickningar
Med arbets böcker kan författare lägga till interaktivitet via en speciell typ av rutnäts kolumn åter givning som kallas en `link renderer`. En länk åter givning konverterar en rutnäts cell till en hyperlänk baserat på innehållet i cellen. Arbets böcker har stöd för många typer av länk åter givning, inklusive de som tillåter att öppna resurs översikts blad, egenskaps uppsättnings visnings program, App Insights-sökning, användning, transaktions spårning osv.

### <a name="setting-up-interactivity-using-grid-cell-clicks"></a>Konfigurera interaktivitet med cell klickningar i rutnät
1. Ändra arbets boken till redigerings läge genom att klicka på objektet _Redigera_ verktygsfält.
2. Använd länken _Lägg till fråga_ för att lägga till en logg frågas kontroll i arbets boken. 
3. Välj typ av fråga som _logg_, resurs typ (till exempel Application Insights) och de resurser som ska riktas mot målet.
4. Använd Frågeredigeraren för att ange KQL för din analys
    ```kusto
    requests
    | summarize Count = count(), Sample = any(pack_all()) by Request = name
    | order by Count desc
    ```
5. `Run query` för att se resultatet
6. Klicka på _kolumn inställningar_ för att öppna fönstret inställningar.
7. I avsnittet _kolumner_ ställer du in:
    1. _Exempel_ på kolumn åter givning: `Link`, Visa för att öppna: `Cell Details`, länk etikett: `Sample`
    2. _Antal_ -kolumn åter givning: `Bar`, färgpalett: `Blue`, minimalt värde: `0`
    3. _Begäran_ -kolumn åter givning: `Automatic`
    4. Klicka på _Spara och Stäng_ för att tillämpa ändringarna
8. Klicka på någon av `Sample` länkar i rutnätet. Då öppnas ett egenskaps fönster med information om en exempel förfrågan.

    ![Bild som visar hur du skapar en interaktiv upplevelse med rutnäts cell klickningar](./media/workbooks-interactive/grid-cell-click-create.png)

### <a name="link-renderer-actions"></a>Länka åter givnings åtgärder
| Länk åtgärd | Åtgärd vid klickning |
|:------------- |:-------------|
| `Generic Details` | Visar rad värden i ett Sammanhangs blad för egenskaps rutnät |
| `Cell Details` | Visar cellvärdet i ett Sammanhangs blad för egenskaps rutnät. Användbart när cellen innehåller en dynamisk typ med information (t. ex. JSON med begär ande egenskaper som plats, roll instans osv.). |
| `Cell Details` | Visar cellvärdet i ett Sammanhangs blad för egenskaps rutnät. Användbart när cellen innehåller en dynamisk typ med information (t. ex. JSON med begär ande egenskaper som plats, roll instans osv.). |
| `Custom Event Details` | Öppnar Application Insights Sök information med det anpassade händelse-ID: t (itemId) i cellen |
| `* Details` | Liknar anpassad händelse information, förutom för beroenden, undantag, sid visningar, begär Anden och spår. |
| `Custom Event User Flows` | Öppnar Application Insights Användarflödens upplevelsen som är pivoterad på det anpassade händelse namnet i cellen |
| `* User Flows` | Liknar anpassade händelse Användarflöden utom undantag, sid visningar och begär Anden |
| `User Timeline` | Öppnar användarens tids linje med användar-ID (user_Id) i cellen |
| `Session Timeline` | Öppnar den Application Insights Sök upplevelsen för värdet i cellen (du kan till exempel söka efter texten "ABC" där ABC är värdet i cellen) |
| `Resource overview` | Öppna resursens översikt i portalen baserat på resurs-ID-värdet i cellen |

## <a name="conditional-visibility"></a>Villkorlig synlighet
Med hjälp av arbets boken kan användare se till att vissa kontroller visas eller försvinner baserat på parametrarnas värden. På så sätt kan skribenter se olika ut baserat på användarens indata-eller telemetri-tillstånd. Ett exempel visar att konsumenterna bara är en sammanfattning när saker är bra men Visa fullständig information när något är fel.

### <a name="setting-up-interactivity-using-conditional-visibility"></a>Konfigurera interaktivitet med villkorsstyrd synlighet
1. Följ stegen i avsnittet `Setting up interactivity on grid row click` för att konfigurera två interaktiva kontroller.
2. Lägg till en ny parameter högst upp:
    1. Namn: `ShowDetails`
    2. Parameter typ: `Drop down`
    3. Krävs: `checked`
    4. Hämta data från: `JSON`
    5. JSON-inmatare: `["Yes", "No"]`
    6. Spara för att genomföra ändringar.
3. Ange parameter värde till `Yes`
4. I kontrollen fråga med ytdiagram klickar du på ikonen _Avancerade inställningar_ (kugg hjuls ikonen)
5. Kontrol lera inställningen `Make this item conditionally visible`
    1. Det här objektet visas om `ShowDetails` parameter värde `equals` `Yes`
6. Klicka på _klar redigering_ för att genomföra ändringarna.
7. Klicka på _klar redigering_ i arbets bokens verktygsfält för att ange Läs läge.
8. Växla värdet för parametern `ShowDetails` till `No`. Observera att diagrammet nedan försvinner.

Bilden nedan visar det synliga fallet där `ShowDetails` är `Yes`

![Bild som visar den villkorliga synlighet där diagrammet är synligt](./media/workbooks-interactive/conditional-visibility.png)

Bilden nedan visar det dolda fallet där `ShowDetails` är `No`

![Bild som visar den villkorliga synlighet där diagrammet är dolt](./media/workbooks-interactive/conditional-invisible.png)

## <a name="next-steps"></a>Nästa steg


* [Kom igång](workbooks-visualizations.md) lär dig mer om arbets böcker många avancerade visualiserings alternativ.
* [Kontrol lera](workbooks-access-control.md) och dela åtkomst till dina arbets boks resurser.
