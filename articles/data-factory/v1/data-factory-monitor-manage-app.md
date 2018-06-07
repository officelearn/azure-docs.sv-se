---
title: Övervaka och hantera data pipelines - Azure | Microsoft Docs
description: Lär dig hur du använder appen för hantering och övervakning för att övervaka och hantera Azure datafabriker och rörledningar.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: f3f07bc4-6dc3-4d4d-ac22-0be62189d578
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: b71ad5701cfd1f8a274318c69d3464cf4fa34708
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621096"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-monitoring-and-management-app"></a>Övervaka och hantera Azure Data Factory pipelines med hjälp av övervakning och hantering av appen
> [!div class="op_single_selector"]
> * [Med hjälp av Azure portal/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Med hjälp av övervakning och Management-appen](data-factory-monitor-manage-app.md)
>
>

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [övervaka och hantera Data Factory pipelines i version 2](../monitor-visually.md).

Den här artikeln beskriver hur du använder appen för hantering och övervakning för att övervaka, hantera och felsöka din Data Factory pipelines. Du kan komma igång med hjälp av programmet genom att titta på nedanstående video:

> [!NOTE]
> Användargränssnittet som visas i videon kanske inte stämmer exakt vad som visas i portalen. Det är något äldre men begrepp förblir detsamma. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Data-Factory-Monitoring-and-Managing-Big-Data-Piplines/player]
>

## <a name="launch-the-monitoring-and-management-app"></a>Starta övervaknings- och Management-appen
Om du vill starta appen Övervakare och hantering, klickar du på den **övervaka och hantera** panelen på den **Data Factory** bladet för din data factory.

![Övervakning av panelen på startsidan Data Factory](./media/data-factory-monitor-manage-app/MonitoringAppTile.png)

Du bör se övervakning och hantering av appen öppnas i ett separat fönster.  

![Övervaknings- och hanteringsapp](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [!NOTE]
> Om du ser att webbläsaren har ”auktorisera...”, avmarkera den **blockerar cookies från tredje part och platsdata** kryssrutan-- eller behålla den har markerats kan du skapa ett undantag för **login.microsoftonline.com**, och Försök att öppna appen igen.


I listan aktivitet Windows i den mellersta rutan finns en aktivitetsfönstret för varje körning av en aktivitet. Om du har aktiviteten ska köras varje timme för fem timmar finns till exempel fem aktivitetsfönster som är associerade med fem datasektorer. Om du inte ser aktivitet windows i listan längst ned, gör du följande:
 
- Uppdatering av **starttid** och **sluttiden** filter längst upp för att matcha start- och sluttider för din pipeline och klicka sedan på den **tillämpa** knappen.  
- Listan över Windows aktivitet uppdateras inte automatiskt. Klicka på den **uppdatera** i verktygsfältet i den **aktivitet Windows** lista.  

Om du inte har ett Data Factory-program för att testa dessa steg med, gör kursen: [kopiera data från Blob Storage till SQL-databas med hjälp av Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="understand-the-monitoring-and-management-app"></a>Förstå övervakning och Management-appen
Det finns tre flikar till vänster: **Resursläsaren**, **övervakning vyer**, och **aviseringar**. Den första fliken (**Resursläsaren**) väljs som standard.

### <a name="resource-explorer"></a>Resursläsaren
Du ser följande:

* Resursläsaren **trädvy** i den vänstra rutan.
* Den **diagramvyn** längst upp i den mellersta rutan.
* Den **aktivitet Windows** listan längst ned i den mellersta rutan.
* Den **egenskaper**, **aktivitet fönstret Explorer**, och **skriptet** flikar i den högra rutan.

I resursutforskaren visas alla resurser (pipelines, datauppsättningar, länkade tjänster) i datafabriken i en trädvy. När du väljer ett objekt i Resursläsaren:

* Den associerade Data Factory-posten är markerad i diagramvyn.
* [Associerade aktiviteten windows](data-factory-scheduling-and-execution.md) är markerade i listan över aktiviteten Windows längst ned.  
* Egenskaper för det markerade objektet visas i fönstret Egenskaper i den högra rutan.
* JSON-definitionen för det markerade objektet visas, om tillämpligt. Exempel: en länkad tjänst, ett dataset eller en pipeline.

![Resursläsaren](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

Finns det [schemaläggning och körning](data-factory-scheduling-and-execution.md) artikel detaljerad konceptuell information om aktiviteten windows.

### <a name="diagram-view"></a>Diagramvy
Diagramvy för en datafabrik ger en och samma plats att övervaka och hantera en datafabrik och dess tillgångar. När du väljer en Data Factory-entitet (dataset/pipeline) i diagramvyn:

* Data factory-entiteten är valt i trädvyn.
* Den associerade aktivitet windows markeras i listan över aktiviteten Windows.
* Egenskaper för det markerade objektet visas i fönstret Egenskaper.

När pipeline aktiveras (inte i ett pausat tillstånd), visas det med en grön linje:

![Pipelinen körs](./media/data-factory-monitor-manage-app/PipelineRunning.png)

Du kan pausa, återuppta eller avsluta en pipeline genom att markera den i diagramvyn och med hjälp av knapparna i kommandofältet.

![Pausa i kommandofältet](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
 
Det finns tre knapparna i fältet för pipeline i diagramvyn. Du kan använda den andra knappen för att pausa pipeline. Pausa Avsluta inte pågående aktiviteter och kan fortsätta att slutföras. Knappen tredje pausar pipeline och avslutar sin befintliga aktiviteter körs. Knappen första återupptar pipeline. När din pipeline pausas ändras färgen för pipeline. Till exempel en pausad pipeline ser ut som i följande bild: 

![Pipeline pausats](./media/data-factory-monitor-manage-app/PipelinePaused.png)

Du kan välja flera två eller flera pipelines med Ctrl-tangenten. Du kan använda knapparna i kommandofältet för att pausa/Fortsätt flera pipelines i taget.

Du kan också högerklicka på en pipeline och välja alternativ för att pausa, fortsätta eller avsluta en pipeline. 

![Snabbmenyn för pipeline](./media/data-factory-monitor-manage-app/right-click-menu-for-pipeline.png)

Klicka på den **öppna pipeline** alternativet för att visa alla aktiviteter i pipelinen. 

![Menyn Öppna pipeline](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

I vyn öppnade pipeline kan du se alla aktiviteter i pipelinen. I det här exemplet är bara en aktivitet: Kopieringsaktiviteten. 

![Öppna pipeline](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

Om du vill gå tillbaka till den föregående vyn, klickar du på datafabriksnamnet i den dynamiska menyn längst upp.

I pipeline-vyn när du väljer en utdatamängd eller när du flyttar musen över datamängd för utdata visas aktiviteten Windows popup-fönstret för denna dataset.

![Aktiviteten Windows popup-fönster](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

Du kan klicka på ett fönster i aktiviteten för att se detaljer för den i den **egenskaper** fönster i den högra rutan.

![Fönstret Egenskaper för aktivitet](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

Växla till i den högra rutan i **aktivitet fönstret Explorer** fliken för att se mer information.

![Aktiviteten fönstret Explorer](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png)

Du också se **matcha variabler** för varje försök har misslyckats för en aktivitet i den **försök** avsnitt.

![Matcha variabler](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

Växla till den **skriptet** fliken för att se JSON-skript definitionen för det valda objektet.   

![Fliken skript](./media/data-factory-monitor-manage-app/ScriptTab.png)

Du kan se aktiviteten windows på tre platser:

* Aktiviteten Windows popup-fönstret i diagramvyn (mellersta rutan).
* Aktiviteten fönstret Utforskaren i den högra rutan.
* Listan över aktiviteten Windows längst ned i fönstret.

Aktiviteten Windows popup-fönster och aktivitet Windows Explorer, kan du bläddra till i föregående vecka och nästa vecka med hjälp av vänster och höger pilarna.

![Aktiviteten fönstret Explorer åt vänster och höger pilarna](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

Längst ned i diagramvyn visas dessa knappar: Zooma In, Zooma ut Zooma till innehåll, Zooma 100% Lås layout. Den **Lås layout** knappen förhindrar du av misstag flyttar tabeller och rörledningar i diagramvyn. Den är aktiverad som standard. Du kan stänga av den och flytta entiteter i diagrammet. När du inaktiverar den kan använda du knappen sista automatisk placering tabeller och rörledningar. Du kan zooma in eller ut genom att använda mushjulet.

![Diagram visa zoomning kommandon](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)

### <a name="activity-windows-list"></a>Lista med aktivitetsfönster
Listan över aktiviteten Windows längst ned i den mellersta rutan visar alla aktivitetsfönster för datamängden som du valde i Resursläsaren eller diagramvyn. Som standard är listan i fallande ordning, vilket innebär att du ser den senaste aktivitetsfönstret längst upp.

![Lista med aktivitetsfönster](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

Den här listan inte automatiskt, så Använd uppdateringsknappen i verktygsfältet manuellt uppdatera det.  

Aktiviteten windows kan vara i något av följande status:

<table>
<tr>
    <th align="left">Status</th><th align="left">Substatus</th><th align="left">Beskrivning</th>
</tr>
<tr>
    <td rowspan="8">Väntar</td><td>ScheduleTime</td><td>Tiden har inte inne för aktivitetsfönstret ska köras.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>Uppströmsberoendena är inte redo.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Beräkningsresurserna är inte tillgängliga.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Alla aktivitetsinstanserna är upptagna med andra windows aktivitet.</td>
</tr>
<tr>
<td>ActivityResume</td><td>Aktiviteten har pausats och kan inte köra aktiviteten windows förrän den har återupptagits.</td>
</tr>
<tr>
<td>Försök igen</td><td>Aktivitetskörningen försöks.</td>
</tr>
<tr>
<td>Validering</td><td>Verifieringen har inte startat ännu.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Verifieringen väntar på att göras.</td>
</tr>
<tr>
<tr>
<td rowspan="2">Pågår</td><td>Verifierar</td><td>Verifiering pågår.</td>
</tr>
<td>-</td>
<td>Aktivitetsfönstret bearbetas.</td>
</tr>
<tr>
<td rowspan="4">Misslyckad</td><td>För lång tid</td><td>Aktivitetskörningen tog längre tid än vad som tillåts av aktiviteten.</td>
</tr>
<tr>
<td>Avbrutna</td><td>Aktivitetsfönstret avbröts av en användare.</td>
</tr>
<tr>
<td>Validering</td><td>Verifieringen misslyckades.</td>
</tr>
<tr>
<td>-</td><td>Det gick inte att genereras eller verifiera Aktivitetsfönstret.</td>
</tr>
<td>Klar</td><td>-</td><td>Aktivitetsfönstret är redo för användning.</td>
</tr>
<tr>
<td>Hoppades över</td><td>-</td><td>Aktivitetsfönstret bearbetas inte.</td>
</tr>
<tr>
<td>Ingen</td><td>-</td><td>En aktivitetsfönstret brukade finnas med en annan status, men har återställts.</td>
</tr>
</table>


När du klickar på en aktivitetsfönstret i listan visas information om den i den **aktivitet Utforskaren** eller **egenskaper** fönstret till höger.

![Aktiviteten fönstret Explorer](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### <a name="refresh-activity-windows"></a>Uppdatera aktiviteten windows
Informationen uppdateras inte automatiskt, så Använd uppdateringsknappen (andra knappen) i kommandofältet för att manuellt uppdatera listan med windows.  

### <a name="properties-window"></a>Egenskapsfönstret
Fönstret Egenskaper är i rutan längst till höger i appen övervakning och hantering.

![Egenskapsfönstret](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

Egenskaper för objekt som du har valt i Resursläsaren (trädvyn), diagramvyn eller aktivitet Windows listan visas.

### <a name="activity-window-explorer"></a>Aktiviteten fönstret Explorer
Den **aktivitet fönstret Explorer** fönstret är i rutan längst till höger i appen övervakning och hantering. Den visar information om aktivitetsfönstret som du valde i popup-fönstret aktivitet Windows eller aktivitet Windows-lista.

![Aktiviteten fönstret Explorer](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

Du kan växla till en annan aktivitetsfönstret genom att klicka på kalendervyn överst. Du kan också använda vänstra pilen och höger pilknappar för längst upp för att se aktiviteten windows från föregående vecka eller nästa vecka.

Du kan använda knapparna längst ned i fönstret för att köra aktivitetsfönstret eller uppdatera information i fönstret.

### <a name="script"></a>Skript
Du kan använda den **skriptet** att visa JSON-definitionen av den valda Data Factory-entiteten (länkad tjänst, datauppsättningen eller pipeline).

![Fliken skript](./media/data-factory-monitor-manage-app/ScriptTab.png)

## <a name="use-system-views"></a>Använd systemvyer
Övervakning och hantering av appen innehåller fördefinierade systemvyer (**senaste aktivitet windows**, **misslyckades aktivitet windows**, **pågående aktivitet windows**) som tillåter Du kan visa senaste/misslyckades/pågående aktivitet windows för din data factory.

Växla till den **övervakning vyer** fliken till vänster genom att klicka på den.

![Övervaka vyer fliken](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

Det finns tre systemvyer som stöds. Välj ett alternativ för att visa den senaste aktiviteten windows, underkända aktiviteten windows eller pågående aktivitet windows i listan aktivitet Windows (längst ned i den mellersta rutan).

När du väljer den **senaste aktivitet windows** alternativet visas alla aktivitetsfönster för senaste i fallande ordning efter den **tid för senaste försök**.

Du kan använda den **misslyckades aktivitet windows** vill se alla underkända aktiviteten windows i listan. Välj en misslyckad aktivitetsfönstret i listan för att se information om den i den **egenskaper** fönster eller **aktivitet fönstret Explorer**. Du kan också hämta loggar för misslyckade Aktivitetsfönstret.

## <a name="sort-and-filter-activity-windows"></a>Sortera och filtrera aktiviteten windows
Ändra den **starttid** och **sluttiden** inställningar i kommandofältet filter aktivitet windows. Klicka på knappen bredvid sluttid för att uppdatera listan med aktiviteten Windows när du har ändrat starttid och sluttid.

![Start- och sluttider](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [!NOTE]
> Alla tider för närvarande i UTC-format i appen övervakning och hantering.
>
>

I den **aktivitet Windows lista**, klicka på namnet på en kolumn (till exempel: Status).

![Kolumnen standardmenyn Windows lista](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

Du kan göra följande:

* Sortering i stigande ordning.
* Sortering i fallande ordning.
* Filtrera efter ett eller flera värden (klar, väntande och så vidare).

När du anger ett filter för en kolumn, kan du se filterknappen har aktiverats för den kolumn som visar att värdena i kolumnen filtrerade värdena.

![Filtrera efter en kolumn i listan över Windows-aktivitet](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

Du kan använda samma popup-fönstret för att ta bort filter. Klicka på knappen Ta bort filter i kommandofältet om du vill ta bort alla filter för aktiviteten Windows-listan.

![Ta bort alla filter för aktiviteten Windows-lista](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)

## <a name="perform-batch-actions"></a>Utföra åtgärder för batch
### <a name="rerun-selected-activity-windows"></a>Kör vald aktivitet windows
Välj ett fönster med en aktivitet, klickar du på nedpilen för knappen första fältet och väljer **kör** / **kör med uppströms i pipeline**. När du väljer den **kör med uppströms i pipeline** alternativet den kör alla uppströmsaktivitet windows samt.
    ![Kör en aktivitetsfönstret](./media/data-factory-monitor-manage-app/ReRunSlice.png)

Du kan också markera flera aktivitetsfönster i listan och köra dem på samma gång. Du kanske vill filtrera aktiviteten windows baserat på status (till exempel: **misslyckades**)-- och kör sedan den underkända aktivitet windows när du har korrigerat problemet som orsakar aktivitet windows misslyckas. Se följande avsnitt för mer information om filtrering aktivitet windows i listan.  

### <a name="pauseresume-multiple-pipelines"></a>Pausa/Fortsätt flera pipelines
Du kan multiselect två eller flera pipelines med Ctrl-tangenten. Du kan använda knapparna i kommandofältet (som är markerade i den röda rektangeln i följande bild) för att pausa/Fortsätt dem.

![Pausa i kommandofältet](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
