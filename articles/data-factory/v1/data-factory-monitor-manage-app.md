---
title: Övervaka och hantera datapipelpipelar - Azure
description: Lär dig hur du använder appen Övervakning och hantering för att övervaka och hantera Azure-datafabriker och pipelines.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: f3f07bc4-6dc3-4d4d-ac22-0be62189d578
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: de56ba1281d0f20c8be838fa1bc9ebc24905b26c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260376"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-monitoring-and-management-app"></a>Övervaka och hantera Azure Data Factory-pipelines med hjälp av appen Övervakning och hantering
> [!div class="op_single_selector"]
> * [Använda Azure portal/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Använda appen Övervakning och hantering](data-factory-monitor-manage-app.md)
>
>

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [övervaka och hantera datafabrikspipelor i](../monitor-visually.md).

I den här artikeln beskrivs hur du använder appen Övervakning och hantering för att övervaka, hantera och felsöka datafabrikens pipelines. Du kan komma igång med att använda programmet genom att titta på följande video:

> [!NOTE]
> Användargränssnittet som visas i videon kanske inte exakt matchar det du ser i portalen. Det är något äldre, men begreppen förblir desamma. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Data-Factory-Monitoring-and-Managing-Big-Data-Piplines/player]
>

## <a name="launch-the-monitoring-and-management-app"></a>Starta appen Övervakning och hantering
Om du vill starta appen Bildskärm och hantering klickar du på panelen **Övervaka & Hantera** på **datafabriken** för datafabriken.

![Övervakningspanel på startsidan för Data Factory](./media/data-factory-monitor-manage-app/MonitoringAppTile.png)

Du bör se appen Övervakning och hantering öppen i ett separat fönster.  

![Övervaknings- och hanteringsapp](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [!NOTE]
> Om du ser att webbläsaren har fastnat på "Auktorisera..." avmarkerar du kryssrutan **Blockera cookies från tredje part och webbplatsdata** – eller håller den markerad, skapar ett undantag för **login.microsoftonline.com**och försöker sedan öppna appen igen.


I listan Aktivitetsfönster i mittenfönstret visas ett aktivitetsfönster för varje körning av en aktivitet. Om du till exempel har schemalagd att köras varje timme i fem timmar visas fem aktivitetsfönster som är associerade med fem datasegment. Om du inte ser aktivitetsfönster i listan längst ned gör du följande:
 
- Uppdatera **start-** och **sluttidsfiltren** högst upp för att matcha start- och sluttiderna för pipelinen och klicka sedan på knappen **Använd.**  
- Listan Aktivitetsfönster uppdateras inte automatiskt. Klicka på knappen **Uppdatera** i verktygsfältet i listan **Aktivitetsfönster.**  

Om du inte har ett Data Factory-program att testa dessa steg med gör du självstudien: [kopiera data från Blob Storage till SQL Database med Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="understand-the-monitoring-and-management-app"></a>Förstå appen Övervakning och hantering
Det finns tre flikar till vänster: **Resource Explorer,** **Övervakningsvyer**och **aviseringar**. Den första fliken (**Resource Explorer**) är markerad som standard.

### <a name="resource-explorer"></a>Utforskare för resurs
Du ser följande:

* Trädvyn **Resursutforskaren** i den vänstra rutan.
* **Diagramvyn** högst upp i mittenrutan.
* **Listan Aktivitetsfönster** längst ned i mittenrutan.
* Flikarna **Egenskaper,** **Aktivitetsfönsterutforskaren**och **Skript** i den högra rutan.

I Resource Explorer visas alla resurser (pipelines, datauppsättningar, länkade tjänster) i datafabriken i en trädvy. När du markerar ett objekt i Resource Explorer:

* Den associerade datafabrikentiteten markeras i diagramvyn.
* [Associerade aktivitetsfönster](data-factory-scheduling-and-execution.md) markeras i listan Aktivitetsfönster längst ned.  
* Egenskaperna för det markerade objektet visas i fönstret Egenskaper i den högra rutan.
* JSON-definitionen för det markerade objektet visas, om tillämpligt. Till exempel: en länkad tjänst, en datauppsättning eller en pipeline.

![Utforskare för resurs](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

Mer information om aktivitetsfönster finns i artikeln [Schemaläggning och körning.](data-factory-scheduling-and-execution.md)

### <a name="diagram-view"></a>Diagramvy
Diagramvyn för en datafabrik tillhandahåller en enda glasruta för att övervaka och hantera en datafabrik och dess tillgångar. När du väljer en datafabriksenhet (datauppsättning/pipeline) i diagramvyn:

* Datafabrikentiteten väljs i trädvyn.
* De associerade aktivitetsfönstren markeras i listan Aktivitetsfönster.
* Egenskaperna för det markerade objektet visas i fönstret Egenskaper.

När pipelinen är aktiverad (inte i pausat läge) visas den med en grön linje:

![Pipeline körs](./media/data-factory-monitor-manage-app/PipelineRunning.png)

Du kan pausa, återuppta eller avsluta en pipeline genom att markera den i diagramvyn och använda knapparna i kommandofältet.

![Pausa/återuppta i kommandofältet](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
 
Det finns tre kommandofältsknappar för pipelinen i diagramvyn. Du kan använda den andra knappen för att pausa pipelinen. Pausa avslutar inte de aktiviteter som körs och låter dem fortsätta att slutföras. Den tredje knappen pausar pipelinen och avslutar dess befintliga köraktiviteter. Den första knappen återupptar pipelinen. När pipelinen pausas ändras färgen på pipelinen. En pausad pipeline ser till exempel ut i följande bild: 

![Pipelinen har pausats](./media/data-factory-monitor-manage-app/PipelinePaused.png)

Du kan markera två eller flera pipelines flera gånger med hjälp av Ctrl-tangenten. Du kan använda kommandofältsknapparna för att pausa/återuppta flera pipelines åt gången.

Du kan också högerklicka på en pipeline och välja alternativ för att pausa, återuppta eller avsluta en pipeline. 

![Snabbmeny för pipeline](./media/data-factory-monitor-manage-app/right-click-menu-for-pipeline.png)

Klicka på alternativet **Öppna pipeline** om du vill visa alla aktiviteter i pipelinen. 

![Menyn Öppna pipeline](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

I den öppnade pipeline-vyn visas alla aktiviteter på pipelinen. I det här exemplet finns det bara en aktivitet: Kopiera aktivitet. 

![Öppnad pipeline](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

Om du vill gå tillbaka till föregående vy klickar du på datafabriksnamnet i sökvägen högst upp.

När du väljer en utdatauppsättning eller när du flyttar musen över utdatauppsättningen i pipelinevyn visas popup-fönstret Aktivitet Windows för den datauppsättningen.

![Popup-fönster i Aktivitetsfönster för Windows](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

Du kan klicka på ett aktivitetsfönster om du vill se information om det i fönstret **Egenskaper** i den högra rutan.

![Egenskaper för aktivitetsfönster](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

I den högra rutan växlar du till fliken **Utforskaren i aktivitetsfönstret** för att se mer information.

![Utforskaren för aktivitetsfönstret](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png)

Du ser också **lösta variabler** för varje **körningsförsök** för en aktivitet i avsnittet Försök.

![Lösta variabler](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

Växla till fliken **Skript** för att se JSON-skriptdefinitionen för det markerade objektet.   

![Fliken Skript](./media/data-factory-monitor-manage-app/ScriptTab.png)

Du kan se aktivitetsfönster på tre ställen:

* Popup-fönstret Aktivitetsfönster i diagramvyn (mittenfönstret).
* Utforskaren för aktivitetsfönstret i den högra rutan.
* Listan Aktivitetsfönster i det nedre fönstret.

I popup-fönstret Aktivitetsfönster och Utforskaren för aktivitetsfönstret kan du bläddra till föregående vecka och nästa vecka med hjälp av vänster- och högerpilarna.

![Vänster-/högerpilar för Aktivitetsfönster utforskare](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

Längst ned i diagramvyn visas följande knappar: Zooma in, Zooma ut, Zooma för att passa, Zooma 100%, Lås layout. Knappen **Lås layout** förhindrar att du av misstag flyttar tabeller och pipelines i diagramvyn. Det är på som standard. Du kan stänga av den och flytta entiteter runt i diagrammet. När du stänger av den kan du använda den sista knappen för att automatiskt placera tabeller och rörledningar. Du kan också zooma in eller ut med hjälp av mushjulet.

![Zoomkommandon för diagramvy](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)

### <a name="activity-windows-list"></a>Lista med aktivitetsfönster
I listan Aktivitetsfönster längst ned i mittenfönstret visas alla aktivitetsfönster för den datauppsättning som du valde i Resursutforskaren eller diagramvyn. Som standard är listan i fallande ordning, vilket innebär att du ser det senaste aktivitetsfönstret högst upp.

![Lista med aktivitetsfönster](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

Den här listan uppdateras inte automatiskt, så använd uppdateringsknappen i verktygsfältet för att uppdatera den manuellt.  

Aktivitetsfönster kan vara i någon av följande status:

<table>
<tr>
    <th align="left">Status</th><th align="left">Understatus</th><th align="left">Beskrivning</th>
</tr>
<tr>
    <td rowspan="8">Väntar</td><td>ScheduleTime (ScheduleTime)</td><td>Tiden har inte kommit för aktivitetsfönstret att köras.</td>
</tr>
<tr>
<td>DatasetBeroende</td><td>Uppströmsberoendena är inte klara.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Beräkningsresurserna är inte tillgängliga.</td>
</tr>
<tr>
<td>SamtidighetLimit</td> <td>Alla aktivitetsinstanser är upptagna med att köra andra aktivitetsfönster.</td>
</tr>
<tr>
<td>ActivityResume</td><td>Aktiviteten pausas och kan inte köra aktivitetsfönstren förrän den har återupptagits.</td>
</tr>
<tr>
<td>Försök igen</td><td>Aktivitetskörningen görs på nytt.</td>
</tr>
<tr>
<td>Validering</td><td>Valideringen har inte startat ännu.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Valideringen väntar på att göras om.</td>
</tr>
<tr>
<tr>
<td rowspan="2">InProgress (InProgress)</td><td>Validera</td><td>Validering pågår.</td>
</tr>
<td>-</td>
<td>Aktivitetsfönstret bearbetas.</td>
</tr>
<tr>
<td rowspan="4">Misslyckades</td><td>Timeout</td><td>Aktivitetskörningen tog längre tid än vad som tillåts av aktiviteten.</td>
</tr>
<tr>
<td>Avbrutna</td><td>Aktivitetsfönstret avbröts av användaråtgärden.</td>
</tr>
<tr>
<td>Validering</td><td>Valideringen misslyckades.</td>
</tr>
<tr>
<td>-</td><td>Aktivitetsfönstret kunde inte genereras eller valideras.</td>
</tr>
<td>Redo</td><td>-</td><td>Aktivitetsfönstret är klart för förbrukning.</td>
</tr>
<tr>
<td>Överhoppad</td><td>-</td><td>Aktivitetsfönstret bearbetades inte.</td>
</tr>
<tr>
<td>Inget</td><td>-</td><td>Ett aktivitetsfönster som används för att finnas med en annan status, men har återställts.</td>
</tr>
</table>


När du klickar på ett aktivitetsfönster i listan visas information om det i fönstret Aktivitet i **Utforskaren** eller **fönstret Egenskaper** till höger.

![Utforskaren för aktivitetsfönstret](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### <a name="refresh-activity-windows"></a>Uppdatera aktivitetsfönster
Informationen uppdateras inte automatiskt, så använd uppdateringsknappen (den andra knappen) i kommandofältet för att manuellt uppdatera listan över aktivitetsfönster.  

### <a name="properties-window"></a>Fönstret Egenskaper
Fönstret Egenskaper finns i fönstret mest höger i appen Övervakning och hantering.

![Fönstret Egenskaper](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

Den visar egenskaper för det objekt som du valde i listan Resursutforskaren (trädvyn), diagramvyn eller Aktivitetsfönster.

### <a name="activity-window-explorer"></a>Utforskaren för aktivitetsfönstret
**Fönstret Aktivitetsfönster explorer** finns i fönstret mest höger i appen Övervakning och hantering. Den visar information om det aktivitetsfönster som du valde i popup-fönstret Aktivitet Windows eller i listan Aktivitetsfönster.

![Utforskaren för aktivitetsfönstret](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

Du kan växla till ett annat aktivitetsfönster genom att klicka på det i kalendervyn högst upp. Du kan också använda vänsterpil/högerpil längst upp för att se aktivitetsfönster från föregående vecka eller nästa vecka.

Du kan använda verktygsfältsknapparna i det nedre fönstret för att köra aktivitetsfönstret igen eller uppdatera informationen i fönstret.

### <a name="script"></a>Skript
Du kan använda fliken **Skript** för att visa JSON-definitionen för den valda datafabrikentiteten (länkad tjänst, datauppsättning eller pipeline).

![Fliken Skript](./media/data-factory-monitor-manage-app/ScriptTab.png)

## <a name="use-system-views"></a>Använda systemvyer
Appen Övervakning och hantering innehåller färdiga systemvyer (**Senaste aktivitetsfönster**, **Misslyckade aktivitetsfönster**, **Aktivitetsfönster för pågående lägen**) som gör att du kan visa aktivitetsfönster för senaste/misslyckade/pågående aktiviteter för datafabriken.

Växla till fliken **Övervakningsvyer** till vänster genom att klicka på den.

![Fliken Övervakningsvyer](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

För närvarande finns det tre systemvyer som stöds. Välj ett alternativ för att se de senaste aktivitetsfönstren, misslyckade aktivitetsfönster eller pågående aktivitetsfönster i aktivitetsfönstret i listan Aktivitetsfönster (längst ned i mittenfönstret).

När du väljer alternativet **Senaste aktivitetsfönster** visas alla senaste aktivitetsfönster i fallande ordning efter **senaste försökstillfället**.

Du kan använda vyn **Misslyckade aktivitetsfönster** om du vill visa alla misslyckade aktivitetsfönster i listan. Välj ett misslyckat aktivitetsfönster i listan om du vill se information om det i **fönstret Egenskaper** eller **Utforskaren för aktivitetsfönstret**. Du kan också hämta alla loggar för ett misslyckat aktivitetsfönster.

## <a name="sort-and-filter-activity-windows"></a>Sortera och filtrera aktivitetsfönster
Ändra **inställningarna** för starttid och **sluttid** i kommandofältet för att filtrera aktivitetsfönster. När du har ändrat starttid och sluttid klickar du på knappen bredvid sluttiden för att uppdatera listan Aktivitetsfönster.

![Start- och sluttider](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [!NOTE]
> För närvarande är alla tider i UTC-format i appen Övervakning och hantering.
>
>

Klicka på namnet på en kolumn i **listan Aktivitetsfönster**(till exempel Status).

![Kolumnmeny för aktivitetslista i Windows](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

Du kan göra följande:

* Sortera i stigande ordning.
* Sortera i fallande ordning.
* Filtrera efter ett eller flera värden (Klar, Väntar och så vidare).

När du anger ett filter i en kolumn visas filterknappen aktiverad för den kolumnen, vilket anger att värdena i kolumnen är filtrerade värden.

![Filtrera i en kolumn i listan Aktivitet i Windows](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

Du kan använda samma popup-fönster för att rensa filter. Om du vill ta bort alla filter för listan Aktivitetsfönster klickar du på knappen Rensa filter i kommandofältet.

![Rensa alla filter för listan Aktivitetsfönster](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)

## <a name="perform-batch-actions"></a>Utföra batchåtgärder
### <a name="rerun-selected-activity-windows"></a>Köra markerade aktivitetsfönster igen
Markera ett aktivitetsfönster, klicka på nedpilen för den första kommandolistknappen och välj **Kör** / **kör igen med uppströms i pipeline**. När du väljer alternativet **Kör igen med uppströms i pipeline** körs även alla aktivitetsfönster uppströms.
    ![Köra ett aktivitetsfönster igen](./media/data-factory-monitor-manage-app/ReRunSlice.png)

Du kan också markera flera aktivitetsfönster i listan och köra dem samtidigt igen. Du kanske vill filtrera aktivitetsfönster baserat på statusen (till **exempel: Misslyckades**) – och sedan köra de misslyckade aktivitetsfönstren igen när du har korrigerat problemet som gör att aktivitetsfönstren misslyckas. Mer information om hur du filtrerar aktivitetsfönster finns i listan i följande avsnitt.  

### <a name="pauseresume-multiple-pipelines"></a>Pausa/återuppta flera pipelines
Du kan fleravälja två eller flera pipelines med hjälp av Ctrl-tangenten. Du kan använda kommandofältsknapparna (som markeras i den röda rektangeln i följande bild) för att pausa/återuppta dem.

![Pausa/återuppta i kommandofältet](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
