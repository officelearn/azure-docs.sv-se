---
title: Övervaka och hantera datapipelines – Azure | Microsoft Docs
description: Lär dig hur du använder appen för övervakning och hantering för att övervaka och hantera Azure-datafabriker och pipelines.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: f3f07bc4-6dc3-4d4d-ac22-0be62189d578
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 5b70edd4f65538b52c70881258bc500a34b04d80
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60826755"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-monitoring-and-management-app"></a>Övervaka och hantera Azure Data Factory-pipelines med hjälp av övervakning och hantering av appen
> [!div class="op_single_selector"]
> * [Med hjälp av Azure portal/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Med hjälp av övervaknings- och hanteringsappen](data-factory-monitor-manage-app.md)
>
>

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [övervaka och hantera Data Factory-pipelines i](../monitor-visually.md).

Den här artikeln beskriver hur du använder appen för övervakning och hantering för att övervaka, hantera och Felsök Data Factory-pipeliner. Du kan komma igång med att använda programmet genom att titta på nedanstående video:

> [!NOTE]
> Användargränssnittet visas i videon kanske inte exakt matchar det som visas i portalen. Det är något äldre, men begrepp är desamma. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Data-Factory-Monitoring-and-Managing-Big-Data-Piplines/player]
>

## <a name="launch-the-monitoring-and-management-app"></a>Starta övervaknings- och hanteringsappen
Om du vill starta appen övervaka och hantering, klickar du på den **övervaka och hantera** panel på den **Data Factory** bladet för din datafabrik.

![Övervakning av panelen på startsidan Datafabrik](./media/data-factory-monitor-manage-app/MonitoringAppTile.png)

Du bör se övervakning och hantering av appen öppnas i ett separat fönster.  

![Övervaknings- och hanteringsapp](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [!NOTE]
> Om du ser att webbläsaren har fastnat på ”auktoriserar...”, avmarkera de **blockera cookies från tredje part och platsdata** kryssrutan – eller behåll det valt skapar ett undantag för **login.microsoftonline.com**, och Försök att öppna appen igen.


I listan aktivitet Windows i den mellersta rutan kan du se ett aktivitetsfönster för varje körning av en aktivitet. Till exempel om du har den aktivitet som schemalagts att köras varje timme i fem timmar kan se du fem aktivitetsfönster som är associerade med fem datasektorer. Om du inte ser aktivitetsfönster i listan längst ned på sidan, gör du följande:
 
- Uppdatera den **starttid** och **sluttid** filter längst upp för att matcha start- och sluttider i pipelinen och klicka sedan på den **tillämpa** knappen.  
- Aktivitet Windows listan uppdateras inte automatiskt. Klicka på den **uppdatera** i verktygsfältet i den **aktivitet Windows** lista.  

Om du inte har ett Data Factory-program för att testa de här stegen med igenom självstudien: [kopiera data från Blob Storage till SQL Database med Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="understand-the-monitoring-and-management-app"></a>Förstå övervakning och hantering av app
Det finns tre flikar till vänster: **Resursläsaren**, **övervakningsvyer**, och **aviseringar**. Den första fliken (**Resursläsaren**) väljs som standard.

### <a name="resource-explorer"></a>Resursläsaren
Du ser följande:

* Resursläsaren **trädvy** i den vänstra rutan.
* Den **diagramvyn** högst upp i den mellersta rutan.
* Den **aktivitet Windows** längst ned i den mellersta rutan.
* Den **egenskaper**, **aktivitet fönstret Explorer**, och **skriptet** flikarna i den högra rutan.

I resursutforskaren ser du alla resurser (pipelines, datauppsättningar, länkade tjänster) i data factory i en trädvy. När du väljer ett objekt i Resursläsaren:

* Den associerade Data Factory-posten är markerad i diagramvyn.
* [Associerade aktivitetsfönster](data-factory-scheduling-and-execution.md) markeras i listan aktivitet Windows längst ned på sidan.  
* Egenskaper för det markerade objektet visas i fönstret Egenskaper i den högra rutan.
* JSON-definition för det markerade objektet visas, om tillämpligt. Till exempel: en länkad tjänst, en datauppsättning eller en pipeline.

![Resursläsaren](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

Se den [schemaläggning och körning](data-factory-scheduling-and-execution.md) artikeln för detaljerad information om aktivitetsfönster.

### <a name="diagram-view"></a>Diagramvy
I diagramvyn på en data factory tillhandahåller en enda glasruta att övervaka och hantera en data factory och dess tillgångar. När du väljer en Data Factory-entitet (datauppsättning/pipeline) i diagramvyn:

* Data factory-entitet är valt i trädvyn.
* De associerade aktivitetsfönster markeras i listan över aktivitet Windows.
* Egenskaper för det markerade objektet visas i fönstret Egenskaper.

När pipelinen har aktiverats (inte i pausläge), visas den med en grön linje:

![Pipeline som körs](./media/data-factory-monitor-manage-app/PipelineRunning.png)

Du kan pausa, återuppta eller avsluta en pipeline med att markera den i diagramvyn och med hjälp av knapparna i kommandofältet.

![Pausa/återuppta i kommandofältet](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
 
Det finns tre knapparna i fältet för pipeline i diagramvyn. Du kan använda den andra knappen för att pausa pipelinen. Pausar Avsluta inte pågående aktiviteter och kan de fortsätta att slutföras. Knappen tredje pausar pipelinen och avslutar det befintliga köra aktiviteter. Den första knappen återupptas pipelinen. När din pipeline är pausad, ändras färgen för pipelinen. Till exempel en pausad pipeline ser ut som i följande bild: 

![Pipelinen har pausats](./media/data-factory-monitor-manage-app/PipelinePaused.png)

Du kan välja flera två eller flera pipelines med hjälp av Ctrl-tangenten. Du kan använda knapparna i kommandofältet för att pausa/återuppta flera pipelines i taget.

Du kan också högerklicka på en pipeline och välja alternativ för att pausa, återuppta eller avsluta en pipeline. 

![Snabbmenyn för pipeline](./media/data-factory-monitor-manage-app/right-click-menu-for-pipeline.png)

Klicka på den **öppna pipeline** alternativet om du vill se alla aktiviteter i pipelinen. 

![Menyn Öppna pipeline](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

I vyn öppna pipeline ser du alla aktiviteter i pipelinen. I det här exemplet finns bara en aktivitet: kopieringsaktiviteten. 

![Öppnad pipeline](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

Om du vill gå tillbaka till den föregående vyn, klickar du på datafabrikens namn i adressfältmenyn längst upp.

I vyn pipeline när du väljer en utdatauppsättning eller när du flyttar musen över datauppsättningen för utdata ser du aktiviteten Windows popup-fönstret för datauppsättningen.

![Aktivitet Windows popup-fönstret](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

Du kan klicka på ett aktivitetsfönster om du vill se information för den i den **egenskaper** fönstret i den högra rutan.

![Fönstret Aktivitetsegenskaper](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

I den högra rutan, växlar du till den **aktivitet fönstret Explorer** fliken om du vill ha mer information.

![Aktivitet fönstret Explorer](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png)

Du kan också se **löst variabler** för varje försök för en aktivitet i den **försök** avsnittet.

![Löst variabler](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

Växla till den **skriptet** fliken för att se JSON-skript-definitionen för det valda objektet.   

![Fliken skript](./media/data-factory-monitor-manage-app/ScriptTab.png)

Du kan se aktivitetsfönster på tre platser:

* Aktivitet Windows popup-fönstret i diagramvyn (rutan i mitten).
* I fönstret-Utforskaren för aktiviteten i den högra rutan.
* Aktivitet Windows listan längst ned i fönstret.

I popup-fönster och aktivitet Windows och aktivitet Windows Explorer, kan du bläddra till föregående vecka och nästa vecka med hjälp av vänster och höger pilarna.

![Aktivitet fönstret Explorer åt vänster och höger pilar](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

Du kan se dessa knappar längst ned i diagramvyn: Zooma In, Zooma ut, Zooma anpassa, Zooma 100 procent, Lås layout. Den **Lås layout** knappen förhindrar du oavsiktligt flyttar tabeller och pipelines i diagramvyn. Den är aktiverad som standard. Du kan stänga av den och runt entiteter i diagrammet. När du inaktiverar det, kan du använda knappen sista att automatiskt placera tabeller och pipelines. Du kan zooma in eller ut med mushjulet.

![Visa zoomning kommandon ett diagram](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)

### <a name="activity-windows-list"></a>Lista med aktivitetsfönster
Aktivitet Windows listan längst ned i den mellersta rutan visar alla aktivitetsfönster för datauppsättningar som du valde i Resursläsaren eller diagramvyn. Som standard är listan i fallande ordning, vilket innebär att du ser den senaste aktivitetsfönstret längst upp.

![Lista med aktivitetsfönster](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

Den här listan inte uppdateras automatiskt, så Använd på uppdateringsknappen i verktygsfältet för att manuellt uppdatera det.  

Aktivitetsfönster kan vara i något av följande statusar:

<table>
<tr>
    <th align="left">Status</th><th align="left">Understatus</th><th align="left">Beskrivning</th>
</tr>
<tr>
    <td rowspan="8">Väntar</td><td>ScheduleTime</td><td>Tiden har inte inne för aktivitetsfönstret att köra.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>Uppströmsberoendena är inte redo.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Beräkningsresurserna är inte tillgängliga.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Alla aktivitetsinstanserna är upptagna med att köra andra aktivitetsfönster.</td>
</tr>
<tr>
<td>ActivityResume</td><td>Aktiviteten har pausats och kan inte köra aktivitetsfönster förrän den har återupptagits.</td>
</tr>
<tr>
<td>Försök igen</td><td>Körningsmiljön för aktiviteten görs.</td>
</tr>
<tr>
<td>Validering</td><td>Verifieringen har inte startat ännu.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Verifiering väntar på att skickas igen.</td>
</tr>
<tr>
<tr>
<td rowspan="2">Pågår</td><td>Verifierar</td><td>Verifiering pågår.</td>
</tr>
<td>-</td>
<td>Aktivitetsfönstret bearbetas.</td>
</tr>
<tr>
<td rowspan="4">Misslyckad</td><td>TimedOut</td><td>Körningsmiljön för aktiviteten tog längre tid än vad som är tillåtet av aktiviteten.</td>
</tr>
<tr>
<td>Avbrutna</td><td>Aktivitetsfönstret avbröts av användaren.</td>
</tr>
<tr>
<td>Validering</td><td>Verifieringen misslyckades.</td>
</tr>
<tr>
<td>-</td><td>Det gick inte att genereras eller Validera Aktivitetsfönstret.</td>
</tr>
<td>Redo</td><td>-</td><td>Aktivitetsfönstret är redo för användning.</td>
</tr>
<tr>
<td>Hoppades över</td><td>-</td><td>Aktivitetsfönstret bearbetas inte.</td>
</tr>
<tr>
<td>Ingen</td><td>-</td><td>Ett aktivitetsfönster brukade finnas med en annan status, men har återställts.</td>
</tr>
</table>


När du klickar på ett aktivitetsfönster i listan visas information om den i den **aktivitet Windows Explorer** eller **egenskaper** fönstret till höger.

![Aktivitet fönstret Explorer](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### <a name="refresh-activity-windows"></a>Uppdatera aktivitetsfönster
Informationen uppdateras inte automatiskt, så Använd uppdateringsknappen (knapp) i kommandofältet för att manuellt uppdatera listan med windows.  

### <a name="properties-window"></a>Egenskapsfönster
Fönstret Egenskaper är i fönstret höger appens övervakning och hantering.

![Egenskapsfönster](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

Egenskaper för objekt som du har valt i Resource Explorer (trädvyn), diagramvyn eller aktivitet Windows listan visas.

### <a name="activity-window-explorer"></a>Aktivitet fönstret Explorer
Den **aktivitet fönstret Explorer** fönstret är i fönstret höger appens övervakning och hantering. Den visar information om aktivitetsfönstret som du har valt i popup-fönstret aktivitet Windows eller aktivitet Windows-lista.

![Aktivitet fönstret Explorer](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

Du kan växla till ett annat aktivitetsfönster i genom att klicka på kalendervyn högst upp. Du kan också använda pilknapparna vänstra HÖGERPIL längst upp för att visa aktivitetsfönster från föregående vecka eller nästa vecka.

Du kan använda knapparna i verktygsfältet längst ned i fönstret för att köra ett aktivitetsfönster eller uppdatera informationen i fönstret.

### <a name="script"></a>Skript
Du kan använda den **skriptet** fliken för att visa JSON-definitionen för den valda Data Factory-entiteten (länkad tjänst, datauppsättning eller pipeline).

![Fliken skript](./media/data-factory-monitor-manage-app/ScriptTab.png)

## <a name="use-system-views"></a>Använda systemvyer
Övervakning och hantering av appen innehåller färdiga systemvyer (**senaste aktivitetsfönster**, **misslyckades aktivitetsfönster**, **pågår aktivitetsfönster**) som gör det Du kan visa de senaste/misslyckades/pågående aktivitetsfönster för din datafabrik.

Växla till den **övervakning vyer** fliken till vänster genom att klicka på den.

![Övervakning av vyer fliken](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

Det finns för närvarande tre systemvyer som stöds. Välj ett alternativ för att se de senaste aktivitetsfönster, misslyckade aktivitetsfönster eller pågående aktivitetsfönster i listan aktivitet Windows (längst ned i den mellersta rutan).

När du väljer den **senaste aktivitetsfönster** alternativet kan du se alla de senaste aktivitetsfönster i fallande ordning efter den **senast försöka tid**.

Du kan använda den **misslyckades aktivitetsfönster** vy för att se alla misslyckade aktivitetsfönster i listan. Välj en misslyckad aktivitetsfönster i listan om du vill se information om den i den **egenskaper** fönster eller **aktivitet fönstret Explorer**. Du kan också hämta loggar för en misslyckad aktivitetsfönster.

## <a name="sort-and-filter-activity-windows"></a>Sortera och filtrera aktivitetsfönster
Ändra den **starttid** och **sluttid** inställningar i kommandofältet för att filtrera aktivitetsfönster. Klicka på knappen bredvid sluttid för att uppdatera listan med aktiviteten Windows när du har ändrat starttid och sluttid.

![Start- och sluttider](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [!NOTE]
> Det finns för närvarande hela tiden i UTC-format i appen övervakning och hantering.
>
>

I den **aktivitet Windows lista**, klicka på namnet på en kolumn (till exempel: Status).

![Kolumnen standardmenyn Windows lista](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

Du kan göra följande:

* Sortera i stigande ordning.
* Sortera i fallande ordning.
* Filtrera efter ett eller flera värden (klart, väntar och så vidare).

När du anger ett filter på en kolumn kan se du filtreringsknappen aktiverat för denna kolumn, vilket betyder att värdena i kolumnen är filtrerade värdena.

![Filtrera efter en kolumn av listan över aktivitet Windows](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

Du kan använda samma popup-fönstret för att ta bort filter. Om du vill ta bort alla filter för aktiviteten Windows-listan, klickar du på knappen Rensa filtret i kommandofältet.

![Rensa alla filter för aktiviteten Windows-lista](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)

## <a name="perform-batch-actions"></a>Utföra åtgärder för batch
### <a name="rerun-selected-activity-windows"></a>Kör valda aktivitetsfönster
Välj ett aktivitetsfönster, klickar du på nedpilen för första fältet kommandoknappen och välj **kör** / **kör med uppströms i pipeline**. När du väljer den **kör med uppströms i pipeline** alternativet den produktmiljö alla uppströmsaktivitet windows samt.
    ![Kör ett aktivitetsfönster](./media/data-factory-monitor-manage-app/ReRunSlice.png)

Du kan också markera flera aktivitetsfönster i listan och köra dem på samma gång. Du kanske vill filtrera aktivitetsfönster baserat på status (till exempel: **Det gick inte**) – och kör sedan den underkända aktivitet windows när du har korrigerat problemet som orsakar aktivitetsfönster misslyckas. Se följande avsnitt innehåller information om hur du filtrerar aktivitetsfönster i listan.  

### <a name="pauseresume-multiple-pipelines"></a>Pausa/återuppta flera pipelines
Du kan markera flera två eller flera pipelines med hjälp av Ctrl-tangenten. Du kan använda knapparna i kommandofältet (som är markerade på den röda rektangeln i följande bild) för att pausa/återuppta dem.

![Pausa/återuppta i kommandofältet](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
