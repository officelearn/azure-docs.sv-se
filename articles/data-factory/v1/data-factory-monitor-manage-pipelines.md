---
title: Övervaka och hantera pipelines med hjälp av Azure-portalen och PowerShell
description: Lär dig hur du använder Azure-portalen och Azure PowerShell för att övervaka och hantera Azure-datafabriker och pipelines som du har skapat.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 44aadecfa80524345932c03abb51e8ebd040a902
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73666967"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>Övervaka och hantera Azure Data Factory-pipelines med hjälp av Azure-portalen och PowerShell
> [!div class="op_single_selector"]
> * [Använda Azure portal/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Använda appen Övervakning och hantering](data-factory-monitor-manage-app.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [övervaka och hantera datafabrikspipelor i](../monitor-visually.md).

I den här artikeln beskrivs hur du övervakar, hanterar och felsöker dina pipelines med hjälp av Azure Portal och PowerShell.

> [!IMPORTANT]
> Övervakningsprogrammet för & ger ett bättre stöd för övervakning och hantering av datapipelor och felsökning av eventuella problem. Mer information om hur du använder programmet finns i [övervaka och hantera datafabrikspipelor med hjälp av appen Övervakning och hantering](data-factory-monitor-manage-app.md). 

> [!IMPORTANT]
> Azure Data Factory version 1 använder nu den nya [Azure Monitor-aviseringsinfrastrukturen](../../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). Den gamla varningsinfrastrukturen är föråldrad. Därför fungerar inte längre dina befintliga aviseringar som konfigurerats för datafabriker i version 1. Dina befintliga aviseringar för v1-datafabriker migreras inte automatiskt. Du måste återskapa dessa aviseringar på den nya aviseringsinfrastrukturen. Logga in på Azure-portalen och välj **Övervaka** för att skapa nya aviseringar om mått (till exempel misslyckade körningar eller lyckade körningar) för dina version 1-datafabriker.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="understand-pipelines-and-activity-states"></a>Förstå pipelines och aktivitetstillstånd
Genom att använda Azure-portalen kan du:

* Visa din datafabrik som ett diagram.
* Visa aktiviteter i en pipeline.
* Visa indata- och utdatauppsättningar.

I det här avsnittet beskrivs också hur ett datamängdssegment övergår från ett tillstånd till ett annat tillstånd.   

### <a name="navigate-to-your-data-factory"></a>Navigera till din datafabrik
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **Datafabriker** till vänster på menyn. Om du inte ser det klickar du på **Fler tjänster >** och sedan på **Datafabriker** under kategorin **INTELLIGENS + ANALYS.**

   ![Bläddra bland alla > datafabriker](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
3. På bladet **Datafabriker** väljer du den datafabrik som du är intresserad av.

    ![Välja datafabrik](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)

   Du bör se hemsidan för datafabriken.

   ![Bladet Datafabrik](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Diagramvy över din datafabrik
**Diagramvyn** för en datafabrik tillhandahåller en enda glasruta för att övervaka och hantera datafabriken och dess tillgångar. Om du vill se **diagramvyn** för din datafabrik klickar du på **Diagram** på startsidan för datafabriken.

![Diagramvy](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

Du kan zooma in, zooma ut, zooma för att passa, zooma till 100 %, låsa diagrammets layout och automatiskt placera rörledningar och datauppsättningar. Du kan också se dataraderingsinformationen (det vill säga visa uppströms- och nedströmsobjekt för markerade objekt).

### <a name="activities-inside-a-pipeline"></a>Aktiviteter inuti en pipeline
1. Högerklicka på pipelinen och klicka sedan på **Öppna pipeline** för att se alla aktiviteter i pipelinen, tillsammans med indata- och utdatauppsättningar för aktiviteterna. Den här funktionen är användbar när pipelinen innehåller mer än en aktivitet och du vill förstå den operativa härstamningen för en enda pipeline.

    ![Menyn Öppna pipeline](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. I följande exempel visas en kopieringsaktivitet i pipelinen med en indata och en utdata. 

    ![Aktiviteter inuti en pipeline](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png)
3. Du kan navigera tillbaka till datafabrikens startsida genom att klicka på länken **Datafabrik** i sökvägen längst upp till vänster.

    ![Navigera tillbaka till datafabriken](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-the-state-of-each-activity-inside-a-pipeline"></a>Visa tillståndet för varje aktivitet i en pipeline
Du kan visa det aktuella tillståndet för en aktivitet genom att visa status för någon av de datauppsättningar som produceras av aktiviteten.

Genom att dubbelklicka på **OutputBlobTable** i **diagrammet**kan du se alla segment som produceras av olika aktivitetskörningar inuti en pipeline. Du kan se att kopieringsaktiviteten har körts under de senaste åtta timmarna och producerat segmenten i läget **Klar.**  

![Rörledningens tillstånd](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

Datauppsättningssegmenten i datafabriken kan ha någon av följande statusar:

<table>
<tr>
    <th align="left">Status</th><th align="left">Delstat</th><th align="left">Beskrivning</th>
</tr>
<tr>
    <td rowspan="8">Väntar</td><td>ScheduleTime (ScheduleTime)</td><td>Tiden har inte kommit för att skivan ska gå.</td>
</tr>
<tr>
<td>DatasetBeroende</td><td>Uppströmsberoendena är inte klara.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Beräkningsresurserna är inte tillgängliga.</td>
</tr>
<tr>
<td>SamtidighetLimit</td> <td>Alla aktivitetsinstanser är upptagna med att köra andra segment.</td>
</tr>
<tr>
<td>ActivityResume</td><td>Aktiviteten pausas och kan inte köra segmenten förrän aktiviteten har återupptagits.</td>
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
<td>Segmentet bearbetas.</td>
</tr>
<tr>
<td rowspan="4">Misslyckades</td><td>Timeout</td><td>Aktivitetskörningen tog längre tid än vad som tillåts av aktiviteten.</td>
</tr>
<tr>
<td>Avbrutna</td><td>Segmentet avbröts av användaråtgärden.</td>
</tr>
<tr>
<td>Validering</td><td>Valideringen misslyckades.</td>
</tr>
<tr>
<td>-</td><td>Segmentet kunde inte genereras och/eller valideras.</td>
</tr>
<td>Redo</td><td>-</td><td>Segmentet är klart för konsumtion.</td>
</tr>
<tr>
<td>Överhoppad</td><td>Inget</td><td>Segmentet bearbetas inte.</td>
</tr>
<tr>
<td>Inget</td><td>-</td><td>Ett segment som används för att finnas med en annan status, men det har återställts.</td>
</tr>
</table>



Du kan visa information om ett segment genom att klicka på en segmentpost på bladet **Nyligen uppdaterade segment.**

![Information om segment](./media/data-factory-monitor-manage-pipelines/slice-details.png)

Om segmentet har körts flera gånger visas flera rader i listan **Aktivitetskörning.** Du kan visa information om en aktivitet som körs genom att klicka på körningsposten i listan **Aktivitetskörning.** Listan visar alla loggfiler, tillsammans med ett felmeddelande om det finns en. Den här funktionen är användbar för att visa och felsöka loggar utan att behöva lämna din datafabrik.

![Aktivitetskörningsinformation](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Om segmentet inte är i läget **Klar** kan du se uppströmssegmenten som inte är klara och blockerar det aktuella segmentet från att köras i **uppströmssegmenten som inte är klara.** Den här funktionen är användbar när segmentet är i **vänteläge** och du vill förstå de uppströmsberoenden som segmentet väntar på.

![Uppströms segment som inte är klara](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>Tillståndsdiagram för datauppsättning
När du har distribuerat en datafabrik och pipelines har en giltig aktiv period övergår datauppsättningssegmenten från ett tillstånd till ett annat. För närvarande följer segmentstatus följande tillståndsdiagram:

![Tillståndsdiagram](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

Övergångsflödet för datauppsättningstillstånd i datafabriken är följande: Väntar -> Pågår/Pågår (validera) -> klar/misslyckades.

Segmentet börjar i **vänteläge** och väntar på att förutsättningar ska uppfyllas innan det körs. Sedan börjar aktiviteten köras och segmentet hamnar i ett **in-progress-tillstånd.** Aktivitetskörningen kan lyckas eller misslyckas. Segmentet markeras som **Klar** eller **Misslyckades**, baserat på resultatet av körningen.

Du kan återställa segmentet för att gå tillbaka från läget **Klar** eller **Misslyckad** till **vänteläget.** Du kan också markera segmenttillståndet som **Hoppa över**, vilket förhindrar att aktiviteten körs och inte bearbetar segmentet.

## <a name="pause-and-resume-pipelines"></a>Pausa och återuppta rörledningar
Du kan hantera dina pipelines med hjälp av Azure PowerShell. Du kan till exempel pausa och återuppta pipelines genom att köra Azure PowerShell-cmdlets. 

> [!NOTE] 
> Diagramvyn stöder inte pausning och återupptar pipelines. Om du vill använda ett användargränssnitt använder du övervaknings- och hanteringsprogrammet. Mer information om hur du använder programmet finns i [övervaka och hantera datafabrikspipelor med hjälp av](data-factory-monitor-manage-app.md) artikel om övervakning och hantering. 

Du kan pausa/pausa pipelines med hjälp av PowerShell-cmdleten **Suspend-AzDataFactoryPipeline.** Den här cmdleten är användbar när du inte vill köra pipelines tills ett problem har åtgärdats. 

```powershell
Suspend-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Ett exempel:

```powershell
Suspend-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

När problemet har åtgärdats med pipelinen kan du återuppta den avstängda pipelinen genom att köra följande PowerShell-kommando:

```powershell
Resume-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Ett exempel:

```powershell
Resume-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

## <a name="debug-pipelines"></a>Felsöka pipelines
Azure Data Factory innehåller omfattande funktioner för att felsöka och felsöka pipelines med hjälp av Azure-portalen och Azure PowerShell.

> [!NOTE] 
> Det är mycket enklare att felspränga med hjälp av övervaknings-& management-appen. Mer information om hur du använder programmet finns i [övervaka och hantera datafabrikspipelor med hjälp av](data-factory-monitor-manage-app.md) artikel om övervakning och hantering. 

### <a name="find-errors-in-a-pipeline"></a>Hitta fel i en pipeline
Om aktivitetskörningen misslyckas i en pipeline är datauppsättningen som produceras av pipelinen i ett feltillstånd på grund av felet. Du kan felsöka och felsöka fel i Azure Data Factory med hjälp av följande metoder.

#### <a name="use-the-azure-portal-to-debug-an-error"></a>Använda Azure-portalen för att felsöka ett fel
1. Klicka på det problemsegment som har **statusuppsättningen** **Misslyckades**på **tabellbladet.**

   ![Tabellblad med problemsegment](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. Klicka på aktivitetskörningen som misslyckades på **bladet Data segment.**

   ![Datasegment med ett fel](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. På bladet **Aktivitetskörningsinformation** kan du hämta de filer som är associerade med HDInsight-bearbetningen. Klicka på **Hämta** för status/stderr om du vill hämta felloggfilen som innehåller information om felet.

   ![Informationsblad för aktivitetskörning med fel](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-powershell-to-debug-an-error"></a>Använda PowerShell för att felsöka ett fel
1. Starta **PowerShell**.
2. Kör kommandot **Get-AzDataFactorySlice** för att se segmenten och deras status. Du bör se ett segment med statusen **Misslyckades**.        

    ```powershell   
    Get-AzDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   Ett exempel:

    ```powershell   
    Get-AzDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```

   Ersätt **StartDateTime** med starttiden för pipelinen. 
3. Kör nu **cmdleten Get-AzDataFactoryRun** för att få information om aktiviteten som körs för segmentet.

    ```powershell   
    Get-AzDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime]
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```

    Ett exempel:

    ```powershell   
    Get-AzDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```

    Värdet för StartDateTime är starttiden för fel-/problemsegmentet som du noterade från föregående steg. Datumtiden ska omges av dubbla citattecken.
4. Du bör se utdata med information om felet som liknar följande:

    ```   
    Id                      : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
    ResourceGroupName       : ADF
    DataFactoryName         : LogProcessingFactory3
    DatasetName               : EnrichedGameEventsTable
    ProcessingStartTime     : 10/10/2014 3:04:52 AM
    ProcessingEndTime       : 10/10/2014 3:06:49 AM
    PercentComplete         : 0
    DataSliceStart          : 5/5/2014 12:00:00 AM
    DataSliceEnd            : 5/6/2014 12:00:00 AM
    Status                  : FailedExecution
    Timestamp               : 10/10/2014 3:04:52 AM
    RetryAttempt            : 0
    Properties              : {}
    ErrorMessage            : Pig script failed with exit code '5'. See wasb://        adfjobs@spestore.blob.core.windows.net/PigQuery
                                    Jobs/841b77c9-d56c-48d1-99a3-
                8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for
                more details.
    ActivityName            : PigEnrichLogs
    PipelineName            : EnrichGameLogsPipeline
    Type                    :
    ```
5. Du kan köra cmdleten **Save-AzDataFactoryLog** med det ID-värde som du ser från utdata och hämta loggfilerna med hjälp av **-DownloadLogsoption** för cmdleten.

    ```powershell
    Save-AzDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>Köra fel igen i en pipeline

> [!IMPORTANT]
> Det är enklare att felsöka fel och köra om misslyckade segment med hjälp av appen Övervakning & Management. Mer information om hur du använder programmet finns i [övervaka och hantera datafabrikspipelor med hjälp av appen Övervakning och hantering](data-factory-monitor-manage-app.md). 

### <a name="use-the-azure-portal"></a>Använda Azure-portalen
När du har felsökt och felsökt fel i en pipeline kan du köra fel igen genom att navigera till felsegmentet och klicka på knappen **Kör** i kommandofältet.

![Köra ett misslyckat segment igen](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

Om segmentet har misslyckats validering på grund av ett principfel (till exempel om data inte är tillgängliga), kan du åtgärda felet och validera igen genom att klicka på knappen **Validera** i kommandofältet.

![Åtgärda fel och verifiera](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="use-azure-powershell"></a>Använda Azure PowerShell
Du kan köra fel igen med hjälp av cmdleten **Set-AzDataFactorySliceStatus.** Se avsnittet [Set-AzDataFactorySliceStatus](https://docs.microsoft.com/powershell/module/az.datafactory/set-azdatafactoryslicestatus) för syntax och annan information om cmdleten.

**Exempel:**

I följande exempel anges status för alla segment för tabellen DAWikiAggregatedData till "Waiting" i Azure-datafabriken "WikiADF".

"UpdateType" är inställd på "UpstreamInPipeline", vilket innebär att status för varje segment för tabellen och alla beroende (uppströms) tabeller är inställda på "Väntar". Det andra möjliga värdet för den här parametern är "Individuell".

```powershell
Set-AzDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```
## <a name="create-alerts-in-the-azure-portal"></a>Skapa aviseringar i Azure-portalen

1.  Logga in på Azure-portalen och välj **Övervaka -> aviseringar** för att öppna sidan Aviseringar.

    ![Öppna sidan Aviseringar.](media/data-factory-monitor-manage-pipelines/v1alerts-image1.png)

2.  Välj **+ Ny aviseringsregel** om du vill skapa en ny avisering.

    ![Skapa en ny avisering](media/data-factory-monitor-manage-pipelines/v1alerts-image2.png)

3.  Definiera **varningsvillkoret**. (Se till att välja **Datafabriker** i fältet **Filter efter resurstyp.)** Du kan också ange värden för **Dimensioner**.

    ![Definiera varningsvillkoret - Välj mål](media/data-factory-monitor-manage-pipelines/v1alerts-image3.png)

    ![Definiera varningsvillkor - Lägg till varningsvillkor](media/data-factory-monitor-manage-pipelines/v1alerts-image4.png)

    ![Definiera varningsvillkoret - Lägg till varningslogik](media/data-factory-monitor-manage-pipelines/v1alerts-image5.png)

4.  Definiera **aviseringsinformationen**.

    ![Definiera varningsinformation](media/data-factory-monitor-manage-pipelines/v1alerts-image6.png)

5.  Definiera **åtgärdsgruppen**.

    ![Definiera åtgärdsgruppen – skapa en ny åtgärdsgrupp](media/data-factory-monitor-manage-pipelines/v1alerts-image7.png)

    ![Definiera åtgärdsgruppen – ange egenskaper](media/data-factory-monitor-manage-pipelines/v1alerts-image8.png)

    ![Definiera åtgärdsgruppen - den nya åtgärdsgruppen skapad](media/data-factory-monitor-manage-pipelines/v1alerts-image9.png)

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>Flytta en datafabrik till en annan resursgrupp eller prenumeration
Du kan flytta en datafabrik till en annan resursgrupp eller en annan prenumeration med hjälp av knappen **Flytta** kommandofält på startsidan för datafabriken.

![Flytta datafabrik](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

Du kan också flytta alla relaterade resurser (till exempel aviseringar som är associerade med datafabriken) tillsammans med datafabriken.

![Dialogrutan Flytta resurser](./media/data-factory-monitor-manage-pipelines/MoveResources.png)
