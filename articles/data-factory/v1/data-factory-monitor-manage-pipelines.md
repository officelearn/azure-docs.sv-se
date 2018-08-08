---
title: Övervaka och hantera pipelines med hjälp av Azure-portalen och PowerShell | Microsoft Docs
description: Lär dig hur du använder Azure-portalen och Azure PowerShell för att övervaka och hantera Azure-datafabriker och pipelines som du har skapat.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 9b0fdc59-5bbe-44d1-9ebc-8be14d44def9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 843b92c20b2ec930ce67659802a4287328a08650
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39618877"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>Övervaka och hantera Azure Data Factory-pipelines med hjälp av Azure-portalen och PowerShell
> [!div class="op_single_selector"]
> * [Med hjälp av Azure portal/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Med hjälp av övervaknings- och hanteringsappen](data-factory-monitor-manage-app.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [övervaka och hantera Data Factory-pipelines i](../monitor-visually.md).

Den här artikeln beskriver hur du övervakar, hanterar och felsöker pipelines med hjälp av Azure-portalen och PowerShell.

> [!IMPORTANT]
> Övervakning och hantering av programmet ger en bättre stöd för övervakning och hantera dina datapipelines och felsöka eventuella problem. Mer information om hur du använder programmet finns i [övervaka och hantera Data Factory-pipelines med hjälp av övervakning och hantering av appen](data-factory-monitor-manage-app.md). 

> [!IMPORTANT]
> Azure Data Factory version 1 nu använder den nya [Azure Monitor-aviseringar infrastruktur](../../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). Den gamla aviseringsdata infrastrukturen är inaktuell. Därför kan konfigurerats dina befintliga aviseringar för version 1 data fabriker inte längre att fungera. Din befintliga aviseringar för v1-datafabriker migreras inte automatiskt. Du måste återskapa dessa aviseringar på den nya aviseringsdata infrastrukturen. Logga in på Azure portal och väljer **övervakaren** att skapa nya aviseringar för mått (till exempel misslyckade körningar eller lyckade körningar) för din version 1 datafabriker.

## <a name="understand-pipelines-and-activity-states"></a>Pipelines och aktivitetsstatus
Med hjälp av Azure-portalen, kan du:

* Visa din data factory som ett diagram.
* Visa aktiviteter i en pipeline.
* Visa in- och utdatauppsättningar.

Det här avsnittet beskriver också hur en datamängdssektor övergår från ett tillstånd till ett annat tillstånd.   

### <a name="navigate-to-your-data-factory"></a>Gå till din data factory
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **datafabriker** på menyn till vänster. Om du inte ser det klickar du på **fler tjänster >**, och klicka sedan på **datafabriker** under den **information + analys** kategori.

   ![Bläddra igenom alla > datafabriker](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
3. På den **datafabriker** bladet välj datafabrik som du är intresserad av.

    ![Välja datafabrik](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)

   Du bör se startsidan för datafabriken.

   ![Bladet Datafabrik](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Diagramvy i din data factory
Den **Diagram** vy av en data factory tillhandahåller en enda glasruta att övervaka och hantera data factory och dess tillgångar. Se den **Diagram** visa i din data factory, klickar du på **Diagram** på startsidan för datafabriken.

![Diagramvy](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

Du kan zooma in, Zooma ut, Zooma om du vill anpassa, Zooma till 100%, låsa layouten för diagrammet och automatiskt placera pipelines och datauppsättningar. Du kan också se data härkomstinformation (det vill säga visa överordnade och underordnade objekt för de valda objekten).

### <a name="activities-inside-a-pipeline"></a>Aktiviteter i en pipeline
1. Högerklicka på pipelinen och klicka sedan på **öppna pipeline** att se alla aktiviteter i pipelinen tillsammans med indata- och utdatauppsättningar för aktiviteter. Den här funktionen är användbart när din pipeline innehåller fler än en aktivitet och du vill förstå operativa härkomst av en enda pipeline.

    ![Menyn Öppna pipeline](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. I följande exempel ser du en Kopieringsaktivitet i pipelinen med indata och utdata. 

    ![Aktiviteter i en pipeline](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png)
3. Du kan gå tillbaka till startsidan för datafabriken genom att klicka på den **datafabrik** länken i länken i det övre vänstra hörnet.

    ![Gå tillbaka till data factory](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-the-state-of-each-activity-inside-a-pipeline"></a>Visa status för varje aktivitet i en pipeline
Du kan visa det aktuella tillståndet för en aktivitet genom att visa status för alla de datamängder som produceras av aktiviteten.

Genom att dubbelklicka på den **OutputBlobTable** i den **Diagram**, du kan se alla sektorer som genereras av olika aktivitetskörningar i en pipeline. Du kan se att Kopieringsaktivitet kördes har för de senaste åtta timmarna och produceras sektorer i den **redo** tillstånd.  

![Status för pipelinen](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

Datauppsättningen segment i datafabriken kan ha något av följande statusar:

<table>
<tr>
    <th align="left">Status</th><th align="left">Undertillstånd</th><th align="left">Beskrivning</th>
</tr>
<tr>
    <td rowspan="8">Väntar</td><td>ScheduleTime</td><td>Tiden har inte inne för att köra sektorn.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>Uppströmsberoendena är inte redo.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Beräkningsresurserna är inte tillgängliga.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Alla aktivitetsinstanserna är upptagna med att köra andra sektorer.</td>
</tr>
<tr>
<td>ActivityResume</td><td>Aktiviteten har pausats och kan inte köra sektorerna förrän aktiviteten återupptas.</td>
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
<td>Sektorn behandlas.</td>
</tr>
<tr>
<td rowspan="4">Misslyckad</td><td>Nådde sin tidsgräns</td><td>Körningsmiljön för aktiviteten tog längre tid än vad som är tillåtet av aktiviteten.</td>
</tr>
<tr>
<td>Avbrutna</td><td>Sektorn avbröts av användaren.</td>
</tr>
<tr>
<td>Validering</td><td>Verifieringen misslyckades.</td>
</tr>
<tr>
<td>-</td><td>Det gick inte att vara genereras och/eller verifiera sektorn.</td>
</tr>
<td>Klar</td><td>-</td><td>Sektorn är klar att förbrukas.</td>
</tr>
<tr>
<td>Hoppades över</td><td>Ingen</td><td>Sektorn bearbetas inte.</td>
</tr>
<tr>
<td>Ingen</td><td>-</td><td>En sektor som brukade finnas med en annan status, men den har återställts.</td>
</tr>
</table>



Du kan visa information om en sektor genom att klicka på en sektor-post på den **nyligen uppdaterade sektorer** bladet.

![Information om datasektorn](./media/data-factory-monitor-manage-pipelines/slice-details.png)

Om sektorn har körts flera gånger, ser du flera rader i den **aktivitetskörningar** lista. Du kan visa information om en aktivitet som körs genom att klicka på posten kör i den **aktivitetskörningar** lista. I listan visas alla loggfiler, tillsammans med ett felmeddelande om det finns en. Den här funktionen är användbar för att visa loggar och felsökningsloggar utan att behöva lämna din data factory.

![Aktivitetskörningsinformation](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Om sektorn inte finns i den **redo** tillstånd, kan du se sektorer uppströms som inte är redo och som blockerar aktuell sektor från att köras i den **sektorer uppströms som inte är redo** lista. Den här funktionen är användbar när din sektorn har **väntar på** tillstånd och du vill förstå uppströmsberoendena sektorn väntar på.

![Sektorer uppströms som inte är klara](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>Datauppsättningen tillståndsdiagram
När du distribuerar en data factory pipelines har en ogiltig aktiv period och skär datauppsättningen övergång från ett tillstånd till ett annat. För närvarande följande statusen för sektorn diagrammet nedan tillstånd:

![Tillståndsdiagram](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

Datauppsättningen tillstånd övergången flödet i data factory är följande: väntar -> förloppet/i-pågår (verifierar) -> redo/misslyckades.

Sektorn startar i en **väntar på** tillstånd, väntar på villkor vara uppfyllda innan den kan köras. Sedan aktiviteten startar körning och sektorn hamnar i ett **pågår** tillstånd. Körningsmiljön för aktiviteten kan lyckas eller misslyckas. Sektorn har markerats som **redo** eller **misslyckades**, baserat på resultatet av körningen.

Du kan återställa sektorn gå tillbaka från den **redo** eller **misslyckades** tillstånd till den **väntar på** tillstånd. Du kan också markera sektorn läget till **hoppa över**, vilket förhindrar aktivitet från körning och inte bearbeta sektorn.

## <a name="pause-and-resume-pipelines"></a>Pausa och återuppta pipelines
Du kan hantera dina pipelines med hjälp av Azure PowerShell. Du kan till exempel pausa och återuppta pipelines genom att köra Azure PowerShell-cmdlets. 

> [!NOTE] 
> I diagramvyn stöder inte pausar och återupptar pipelines. Använd övervaknings- och hantera program om du vill använda ett användargränssnitt. Mer information om hur du använder programmet finns i [övervaka och hantera Data Factory-pipelines med hjälp av övervakning och hantering av appen](data-factory-monitor-manage-app.md) artikeln. 

Du kan Pausa/Pausa pipelines med hjälp av den **Suspend-AzureRmDataFactoryPipeline** PowerShell-cmdlet. Denna cmdlet är användbar när du inte vill att köra dina pipelines förrän problemet har lösts. 

```powershell
Suspend-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Exempel:

```powershell
Suspend-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

När problemet har åtgärdats med pipeline, kan du återuppta pausade pipelinen genom att köra följande PowerShell-kommando:

```powershell
Resume-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Exempel:

```powershell
Resume-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

## <a name="debug-pipelines"></a>Felsök pipelines
Azure Data Factory innehåller omfattande funktioner för dig att felsöka pipelines med hjälp av Azure-portalen och Azure PowerShell.

> [!NOTE] 
> Det är mycket enklare att Felsökte fel med övervaknings- och Hanteringsappen. Mer information om hur du använder programmet finns i [övervaka och hantera Data Factory-pipelines med hjälp av övervakning och hantering av appen](data-factory-monitor-manage-app.md) artikeln. 

### <a name="find-errors-in-a-pipeline"></a>Hitta fel i en pipeline
Om det inte går att aktiviteten kör i en pipeline kan är datauppsättningen som produceras av pipelinen i ett feltillstånd på grund av felet. Du kan felsöka och Felsök i Azure Data Factory med hjälp av följande metoder.

#### <a name="use-the-azure-portal-to-debug-an-error"></a>Använda Azure-portalen för att felsöka ett fel
1. På den **tabell** bladet klickar du på sektorn problem som har den **Status** inställd **misslyckades**.

   ![Tabellblad med problem](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. På den **datasektor** bladet, klickar du på aktiviteten kör som misslyckades.

   ![Datasektorn med ett fel](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. På den **aktivitetskörningsinformation** bladet som du kan hämta de filer som är kopplade till HDInsight-bearbetning. Klicka på **hämta** för Status/stderr att ladda ned loggfilen som innehåller information om felet.

   ![Aktivitetskörning informationsbladet med fel](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-powershell-to-debug-an-error"></a>Använd PowerShell för att felsöka ett fel
1. Starta **PowerShell**.
2. Kör den **Get-AzureRmDataFactorySlice** kommando för att visa segment och deras status. Du bör se en sektor med statusen **misslyckades**.        

    ```powershell   
    Get-AzureRmDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   Exempel:

    ```powershell   
    Get-AzureRmDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```

   Ersätt **StartDateTime** med starttiden för din pipeline. 
3. Kör nu den **Get-AzureRmDataFactoryRun** cmdlet för att få information om aktiviteten kör för sektorn.

    ```powershell   
    Get-AzureRmDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime]
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```

    Exempel:

    ```powershell   
    Get-AzureRmDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```

    Värdet för StartDateTime är starttiden för den sektor i fel/problem som du antecknade i föregående steg. Datum / tid ska omges av dubbla citattecken.
4. Du bör se utdata med information om fel som liknar följande:

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
5. Du kan köra den **spara AzureRmDataFactoryLog** cmdlet med Id-värde som du ser utdata och ladda ned filerna med hjälp av den **- DownloadLogsoption** för cmdleten.

    ```powershell
    Save-AzureRmDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>Kör fel i en pipeline

> [!IMPORTANT]
> Det är enklare att felsöka och köra misslyckade sektorer med hjälp av övervaknings- och Hanteringsappen. Mer information om hur du använder programmet finns i [övervaka och hantera Data Factory-pipelines med hjälp av övervakning och hantering av appen](data-factory-monitor-manage-app.md). 

### <a name="use-the-azure-portal"></a>Använda Azure-portalen
När du felsöker och felsöka fel i en pipeline kan du köra fel genom att gå till fel sektorn och klicka på **kör** knappen i kommandofältet.

![Kör en misslyckad sektor](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

I fallet sektorn valideringen misslyckades på grund av en princip-fel (till exempel om data är inte tillgängligt), kan du åtgärda felet och validera igen genom att klicka på den **verifiera** knappen i kommandofältet.

![Åtgärda fel och verifiera](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="use-azure-powershell"></a>Använda Azure PowerShell
Du kan köra fel med hjälp av den **Set-AzureRmDataFactorySliceStatus** cmdlet. Se den [Set-AzureRmDataFactorySliceStatus](https://docs.microsoft.com/powershell/module/azurerm.datafactories/set-azurermdatafactoryslicestatus) avsnittet syntax och annan information om cmdlet: en.

**Exempel:**

I följande exempel anger status för alla segment för tabellen ”DAWikiAggregatedData” till att vänta i Azure data factory 'WikiADF'.

Uppdateringstyp är inställd på 'UpstreamInPipeline ”, vilket innebär att status för varje segment för tabellen och alla beroende (överordnad) tabeller är inställda på” väntar ”. Det andra möjliga värdet för den här parametern är ”person”.

```powershell
Set-AzureRmDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```
## <a name="create-alerts-in-the-azure-portal"></a>Skapa aviseringar i Azure portal

1.  Logga in på Azure portal och väljer **övervakaren -> aviseringar** att öppna sidan aviseringar.

    ![Öppna sidan aviseringar.](media/data-factory-monitor-manage-pipelines/v1alerts-image1.png)

2.  Välj **+ ny aviseringsregel** att skapa en ny avisering.

    ![Skapa en ny avisering](media/data-factory-monitor-manage-pipelines/v1alerts-image2.png)

3.  Definiera den **Avisera villkor**. (Se till att välja **datafabriker** i den **filtrera efter resurstyp** fält.) Du kan även ange värden för **dimensioner**.

    ![Definiera aviseringsvillkoren - Välj mål](media/data-factory-monitor-manage-pipelines/v1alerts-image3.png)

    ![Definiera villkoret för aviseringen – lägga till aviseringsvillkoren](media/data-factory-monitor-manage-pipelines/v1alerts-image4.png)

    ![Definiera villkoret för aviseringen – lägga till aviseringslogik](media/data-factory-monitor-manage-pipelines/v1alerts-image5.png)

4.  Definiera den **aviseringsinformation**.

    ![Definiera Aviseringsinformationen](media/data-factory-monitor-manage-pipelines/v1alerts-image6.png)

5.  Definiera den **åtgärdsgrupp**.

    ![Definiera åtgärdsgruppen – skapa en ny åtgärdsgrupp](media/data-factory-monitor-manage-pipelines/v1alerts-image7.png)

    ![Definiera åtgärdsgruppen – ange egenskaper](media/data-factory-monitor-manage-pipelines/v1alerts-image8.png)

    ![Definiera åtgärdsgruppen – ny åtgärdsgrupp har skapats](media/data-factory-monitor-manage-pipelines/v1alerts-image9.png)

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>Flytta en data factory till en annan resursgrupp eller prenumeration
Du kan flytta en data factory till en annan resursgrupp eller en annan prenumeration med hjälp av den **flytta** kommandot liggande knappen på startsidan på din datafabrik.

![Flytta data factory](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

Du kan också flytta alla relaterade resurser (till exempel aviseringar som är associerade med data factory), tillsammans med data factory.

![Dialogrutan för flytta resurser](./media/data-factory-monitor-manage-pipelines/MoveResources.png)
