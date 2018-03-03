---
title: "Övervaka och hantera pipelines med hjälp av Azure portal och PowerShell | Microsoft Docs"
description: "Lär dig hur du använder Azure-portalen och Azure PowerShell för att övervaka och hantera Azure datafabriker och pipelines som du har skapat."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 9b0fdc59-5bbe-44d1-9ebc-8be14d44def9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: b361ca67ebece9ba1ced1010835eb90586dd7359
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>Övervaka och hantera Azure Data Factory pipelines med hjälp av Azure portal och PowerShell
> [!div class="op_single_selector"]
> * [Med hjälp av Azure portal/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Med hjälp av övervakning och Management-appen](data-factory-monitor-manage-app.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [övervaka och hantera Data Factory pipelines i version 2](../monitor-visually.md).

> [!IMPORTANT]
> Övervakning och hantering av programmet ger ett bättre stöd för att övervaka och hantera dina data pipelines och felsöka eventuella problem. Mer information om hur du använder programmet finns [övervaka och hantera Data Factory pipelines med hjälp av övervakning och hantering av appen](data-factory-monitor-manage-app.md). 


Den här artikeln beskriver hur du övervaka, hantera och felsöka din pipelines med hjälp av Azure portal och PowerShell. Artikeln innehåller även information om hur du skapar aviseringar och håll dig informerad om misslyckade.

## <a name="understand-pipelines-and-activity-states"></a>Förstå pipelines och aktivitet tillstånd
Med hjälp av Azure portal, kan du:

* Visa din data factory som ett diagram.
* Visa aktiviteter i en pipeline.
* Visa inkommande och utgående datauppsättningar.

Det här avsnittet beskriver också hur en datamängdssektor övergångar från ett tillstånd till ett annat tillstånd.   

### <a name="navigate-to-your-data-factory"></a>Navigera till din data factory
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **datafabriker** på menyn till vänster. Om du inte ser det klickar du på **fler tjänster >**, och klicka sedan på **datafabriker** under den **INTELLIGENCE + analys** kategori.

   ![Bläddra igenom alla > datafabriker](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
3. På den **datafabriker** bladet välj datafabriken som du är intresserad av.

    ![Välja datafabrik](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)

   Du bör se startsidan för datafabriken.

   ![Bladet Datafabrik](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Diagramvy i din data factory
Den **Diagram** vy av en datafabrik som tillhandahåller en och samma plats att övervaka och hantera data factory och dess tillgångar. Se den **Diagram** visa i din data factory, klickar du på **Diagram** på startsidan för data factory.

![Diagramvy](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

Du kan zooma in, Zooma ut, Zooma in för att passa, Zooma till 100%, låsa layouten för diagrammet och placera pipelines och datauppsättningar automatiskt. Du kan också se informationen om härkomst (det vill säga visa överordnade och underordnade objekt av valda objekt).

### <a name="activities-inside-a-pipeline"></a>Aktiviteter i en pipeline
1. Högerklicka på pipeline och klicka sedan på **öppna pipeline** att se alla aktiviteter i pipelinen, tillsammans med indata- och datauppsättningar för aktiviteter. Den här funktionen är användbart när din pipeline innehåller mer än en aktivitet och du vill förstå operativa härkomst av en enkel rörledning.

    ![Menyn Öppna pipeline](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. I följande exempel visas en kopia aktivitet i pipelinen med indata och utdata. 

    ![Aktiviteter i en pipeline](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png)
3. Du kan gå tillbaka till startsidan för datafabriken genom att klicka på **datafabriken** länk i sökvägen i det övre vänstra hörnet.

    ![Gå tillbaka till data factory](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-the-state-of-each-activity-inside-a-pipeline"></a>Visa status för varje aktivitet i en pipeline
Du kan visa det aktuella tillståndet för en aktivitet genom att visa status för alla datauppsättningar som produceras av aktiviteten.

Genom att dubbelklicka på den **OutputBlobTable** i den **Diagram**, du kan se alla segment som genereras av en annan aktivitet körs i en pipeline. Du kan se att kopieringsaktiviteten kördes har för de senaste åtta timmarna och producerade sektorer i den **klar** tillstånd.  

![Tillståndet för pipeline](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

Dataset-segment i datafabriken kan ha en av följande status:

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
<td>Sektorn behandlas.</td>
</tr>
<tr>
<td rowspan="4">Misslyckad</td><td>För lång tid</td><td>Aktivitetskörningen tog längre tid än vad som tillåts av aktiviteten.</td>
</tr>
<tr>
<td>Avbrutna</td><td>Sektorn avbröts av en användare.</td>
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
<td>Hoppades över</td><td>Ingen</td><td>Sektorn är inte bearbetas.</td>
</tr>
<tr>
<td>Ingen</td><td>-</td><td>En sektor som brukade finnas med en annan status, men den har återställts.</td>
</tr>
</table>



Du kan visa information om en sektor genom att klicka på en post i sektorn på den **nyligen uppdaterat segment** bladet.

![Sektorn information](./media/data-factory-monitor-manage-pipelines/slice-details.png)

Om sektorn har körts flera gånger, ser du flera rader i den **aktiviteten körs** lista. Du kan visa information om en aktivitet som kör genom att klicka på Kör post i den **aktiviteten körs** lista. I listan visas alla loggfiler, tillsammans med ett felmeddelande om det finns en. Den här funktionen är användbar för att visa loggar och felsökningsloggar utan att behöva lämna din data factory.

![Aktivitetskörningsinformation](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Om sektorn inte i den **klar** tillstånd, kan du se sektorer uppströms som inte är klar och blockerar aktuellt segment från att köras i den **sektorer uppströms som inte är redo** lista. Den här funktionen är användbart när din sektorn är i **väntar på** tillstånd och du vill förstå uppströmsberoendena sektorn väntar på.

![Sektorer uppströms som inte är klara](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>DataSet tillståndsdiagram
När du distribuerar en datafabrik och pipelines har en ogiltig aktiv period, sektorer datauppsättningen övergång från ett tillstånd till en annan. För närvarande följande sektorn status i följande tillståndsdiagram:

![Tillståndsdiagram](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

Dataset tillstånd övergången flödet i data factory är följande: väntar -> förlopp/i-pågående (verifierar) -> klar/misslyckades.

Sektorn startar i en **väntar på** tillstånd, väntar på villkor som måste uppfyllas innan den körs. Sedan aktiviteten startar körning och sektorn försätts i ett **pågående** tillstånd. Aktivitetskörningen kan lyckas eller misslyckas. Sektorn är markerad som **klar** eller **misslyckades**, baserat på resultatet av körningen.

Du kan återställa sektorn gå tillbaka från den **klar** eller **misslyckades** till den **väntar på** tillstånd. Du kan också markera segment läget till **hoppa över**, vilket förhindrar att aktiviteten körs och icke-bearbetning av sektorn.

## <a name="pause-and-resume-pipelines"></a>Pausa och återuppta pipelines
Du kan hantera dina pipelines med hjälp av Azure PowerShell. Du kan till exempel pausa och återuppta pipelines genom att köra Azure PowerShell-cmdlets. 

> [!NOTE] 
> Diagramvyn stöder inte pausa och återuppta pipelines. Använd övervaknings- och hantera program om du vill använda ett användargränssnitt. Mer information om hur du använder programmet finns [övervaka och hantera Data Factory pipelines med hjälp av övervakning och hantering av appen](data-factory-monitor-manage-app.md) artikel. 

Du kan Pausa/Pausa pipelines med hjälp av den **pausa AzureRmDataFactoryPipeline** PowerShell-cmdlet. Denna cmdlet är användbar när du inte vill köra din pipelines förrän problemet har åtgärdats. 

```powershell
Suspend-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Exempel:

```powershell
Suspend-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

När problemet har åtgärdats med pipeline, kan du återuppta pausade pipeline genom att köra följande PowerShell-kommando:

```powershell
Resume-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Exempel:

```powershell
Resume-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

## <a name="debug-pipelines"></a>Felsöka pipelines
Azure Data Factory ger omfattande funktioner för dig att felsöka pipelines med hjälp av Azure-portalen och Azure PowerShell.

> [! Observera} är det mycket enklare Felsökte fel med övervakning & Management-appen. Mer information om hur du använder programmet finns [övervaka och hantera Data Factory pipelines med hjälp av övervakning och hantering av appen](data-factory-monitor-manage-app.md) artikel. 

### <a name="find-errors-in-a-pipeline"></a>Sök efter fel i en pipeline
Om aktiviteten körs inte i en pipeline, är dataset som produceras av pipelinen i ett feltillstånd på grund av felet. Du kan felsöka och felsöka i Azure Data Factory med hjälp av följande metoder.

#### <a name="use-the-azure-portal-to-debug-an-error"></a>Använda Azure portal för att felsöka ett fel
1. På den **tabell** bladet på problemet sektor som har det **Status** inställd på **misslyckades**.

   ![Tabell bladet med problem](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. På den **datasektorn** bladet, klickar du på aktiviteten körs som misslyckades.

   ![Datasektorn med ett fel](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. På den **aktivitet köras information** bladet som du kan hämta de filer som är associerade med HDInsight-bearbetning. Klicka på **hämta** för Status/stderr att ladda ned felloggen som innehåller information om felet.

   ![Aktiviteten kör informationsbladet med fel](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-powershell-to-debug-an-error"></a>Använd PowerShell för att felsöka ett fel
1. Starta **PowerShell**.
2. Kör den **Get-AzureRmDataFactorySlice** kommandot för att se sektorerna och deras status. Du bör se ett segment med statusen **misslyckades**.        

    ```powershell   
    Get-AzureRmDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   Exempel:

    ```powershell   
    Get-AzureRmDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```

   Ersätt **StartDateTime** med starttiden för din pipeline. 
3. Kör nu den **Get-AzureRmDataFactoryRun** för att hämta information om aktiviteten kör för sektorn.

    ```powershell   
    Get-AzureRmDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime]
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```

    Exempel:

    ```powershell   
    Get-AzureRmDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```

    Värdet för StartDateTime är starttiden för sektorn fel/problem som du antecknade från föregående steg. Datum och tid ska omges av dubbla citattecken.
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
5. Du kan köra den **spara AzureRmDataFactoryLog** med ID-värde som du ser från utdata och hämta filerna med hjälp av den **- DownloadLogsoption** för cmdleten.

    ```powershell
    Save-AzureRmDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>Kör fel i en pipeline

> [!IMPORTANT]
> Det är enklare att felsöka och kör misslyckade segment med hjälp av övervakning & Management-appen. Mer information om hur du använder programmet finns [övervaka och hantera Data Factory pipelines med hjälp av övervakning och hantering av appen](data-factory-monitor-manage-app.md). 

### <a name="use-the-azure-portal"></a>Använda Azure-portalen
När du felsöker och felsöka fel i en pipeline, kan du köra fel genom att gå till fel segment och klicka på den **kör** knappen i kommandofältet.

![Kör en misslyckad sektor](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

I fallet sektorn valideringen misslyckades på grund av en princip-fel (till exempel om data inte är tillgängligt), kan du åtgärda felet och validera igen genom att klicka på den **verifiera** knappen i kommandofältet.

![Åtgärda felen och validera](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="use-azure-powershell"></a>Använda Azure PowerShell
Du kan köra fel med hjälp av den **Set AzureRmDataFactorySliceStatus** cmdlet. Finns det [Set AzureRmDataFactorySliceStatus](https://msdn.microsoft.com/library/mt603522.aspx) avsnittet annan information om cmdlet och syntax.

**Exempel:**

I följande exempel anger status för alla segment för tabellen 'DAWikiAggregatedData' till väntar i Azure data factory 'WikiADF'.

Uppdateringstyp är inställd på 'UpstreamInPipeline', vilket innebär att statusen för varje segment för tabellen och alla beroende (överordnad) tabeller är inställt på ”väntande”. Andra möjliga värde för den här parametern är 'Person'.

```powershell
Set-AzureRmDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```

## <a name="create-alerts"></a>Skapa aviseringar
Användaren Azure loggar händelser när en Azure-resurs (till exempel data factory) skapas, uppdateras eller tas bort. Du kan skapa aviseringar på dessa händelser. Du kan använda Data Factory för att samla in olika mått och skapa aviseringar på mått. Vi rekommenderar att du använder händelser för övervakning i realtid och använda mått i historiksyfte.

### <a name="alerts-on-events"></a>Aviseringar om händelser
Azure händelser ger användbar insikter om vad som händer i Azure-resurser. När du använder Azure Data Factory händelser genereras när:

* En datafabrik har skapats, uppdateras eller tas bort.
* Databearbetning (som ”kör”) har startats eller slutförts.
* Ett HDInsight-kluster på begäran skapas eller tas bort.

Du kan skapa varningar på dessa användarhändelser och konfigurera dem för att skicka e-postaviseringar till administratören och coadministrators för prenumerationen. Dessutom kan du ange ytterligare e-postadresserna för användare som behöver ta emot e-postaviseringar när villkoren är uppfyllda. Den här funktionen är användbart när du vill få meddelanden på fel och inte vill att ständigt övervaka din data factory.

> [!NOTE]
> För närvarande visar portalen inte aviseringar om händelser. Använd den [övervakning och hantering av](data-factory-monitor-manage-app.md) att se alla varningar.


#### <a name="specify-an-alert-definition"></a>Ange en definition av varning
Om du vill ange en definition av aviseringen, kan du skapa en JSON-fil som beskriver de åtgärder som du vill bli aviserad om. I följande exempel skickar aviseringen ett e-postmeddelande för RunFinished igen. Om du vill att specifika skickas ett e-postmeddelande när en körning i datafabriken har slutförts och kör misslyckades (Status = FailedExecution).

```JSON
{
    "contentVersion": "1.0.0.0",
     "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "parameters": {},
    "resources":
    [
        {
            "name": "ADFAlertsSlice",
            "type": "microsoft.insights/alertrules",
            "apiVersion": "2014-04-01",
            "location": "East US",
            "properties":
            {
                "name": "ADFAlertsSlice",
                "description": "One or more of the data slices for the Azure Data Factory has failed processing.",
                "isEnabled": true,
                "condition":
                {
                    "odata.type": "Microsoft.Azure.Management.Insights.Models.ManagementEventRuleCondition",
                    "dataSource":
                    {
                        "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleManagementEventDataSource",
                        "operationName": "RunFinished",
                        "status": "Failed",
                        "subStatus": "FailedExecution"   
                    }
                },
                "action":
                {
                    "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
                    "customEmails": [ "<your alias>@contoso.com" ]
                }
            }
        }
    ]
}
```

Du kan ta bort **subStatus** från JSON-definitionen om du inte vill bli aviserad om ett specifikt fel.

Det här exemplet konfigurerar varningen för alla datafabriker i din prenumeration. Om du vill att aviseringen ska ställas in för en viss datafabrik, kan du ange data factory **resourceUri** i den **dataSource**:

```JSON
"resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>"
```

Följande tabell innehåller en lista över tillgängliga åtgärder och status (och underordnad status).

| Åtgärdsnamn | Status | Substatus |
| --- | --- | --- |
| RunStarted |Startad |Startar |
| RunFinished |Misslyckades / lyckades |FailedResourceAllocation<br/><br/>Lyckades<br/><br/>FailedExecution<br/><br/>För lång tid<br/><br/>< avbröts<br/><br/>FailedValidation<br/><br/>Avbrutna |
| OnDemandClusterCreateStarted |Startad | |
| OnDemandClusterCreateSuccessful |Lyckades | |
| OnDemandClusterDeleted |Lyckades | |

Se [skapa Varningsregeln](https://msdn.microsoft.com/library/azure/dn510366.aspx) för ytterligare information om JSON-element som används i exemplet.

#### <a name="deploy-the-alert"></a>Distribuera aviseringen
Använda Azure PowerShell-cmdlet för att distribuera aviseringen **ny AzureRmResourceGroupDeployment**som visas i följande exempel:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\ADFAlertFailedSlice.json  
```

När resursen gruppdistributionen är klar visas följande meddelanden:

```
VERBOSE: 7:00:48 PM - Template is valid.
WARNING: 7:00:48 PM - The StorageAccountName parameter is no longer used and will be removed in a future release.
Please update scripts to remove this parameter.
VERBOSE: 7:00:49 PM - Create template deployment 'ADFAlertFailedSlice'.
VERBOSE: 7:00:57 PM - Resource microsoft.insights/alertrules 'ADFAlertsSlice' provisioning status is succeeded

DeploymentName    : ADFAlertFailedSlice
ResourceGroupName : adf
ProvisioningState : Succeeded
Timestamp         : 10/11/2014 2:01:00 AM
Mode              : Incremental
TemplateLink      :
Parameters        :
Outputs           :
```

> [!NOTE]
> Du kan använda den [Skapa regel för varning](https://msdn.microsoft.com/library/azure/dn510366.aspx) REST API för att skapa en aviseringsregel. JSON-nyttolast liknar JSON-exemplet.  


#### <a name="retrieve-the-list-of-azure-resource-group-deployments"></a>Hämta listan över distributionen av Azure resursgrupper
Om du vill hämta listan över distributionen av distribuerade Azure resursgrupper, använder du cmdlet **Get-AzureRmResourceGroupDeployment**som visas i följande exempel:

```powershell
Get-AzureRmResourceGroupDeployment -ResourceGroupName adf
```

```
DeploymentName    : ADFAlertFailedSlice
ResourceGroupName : adf
ProvisioningState : Succeeded
Timestamp         : 10/11/2014 2:01:00 AM
Mode              : Incremental
TemplateLink      :
Parameters        :
Outputs           :
```

#### <a name="troubleshoot-user-events"></a>Felsöka användarhändelser
1. Du kan se alla händelser som genereras när du klickar på den **mått och åtgärder** panelen.

    ![Panelen mått och åtgärder](./media/data-factory-monitor-manage-pipelines/metrics-and-operations-tile.png)
2. Klicka på den **händelser** rutan för att se händelserna.

    ![Panelen händelser](./media/data-factory-monitor-manage-pipelines/events-tile.png)
3. På den **händelser** bladet hittar du information om händelser, filtreras händelser och så vidare.

    ![Händelser bladet](./media/data-factory-monitor-manage-pipelines/events-blade.png)
4. Klicka på en **åtgärden** i listan över funktioner som orsakar ett fel.

    ![Välj en åtgärd](./media/data-factory-monitor-manage-pipelines/select-operation.png)
5. Klicka på en **fel** händelsen för att se information om felet.

    ![Händelsefel](./media/data-factory-monitor-manage-pipelines/operation-error-event.png)

Se [Azure Insight cmdlets](https://msdn.microsoft.com/library/mt282452.aspx) hämta för PowerShell-cmdlets som du kan använda för att lägga till eller ta bort aviseringar. Här följer några exempel på användning av den **Get-AlertRule** cmdlet:

```powershell
get-alertrule -res $resourceGroup -n ADFAlertsSlice -det
```

```
Properties :
Action      : Microsoft.Azure.Management.Insights.Models.RuleEmailAction
Condition   :
DataSource :
EventName             :
Category              :
Level                 :
OperationName         : RunFinished
ResourceGroupName     :
ResourceProviderName  :
ResourceId            :
Status                : Failed
SubStatus             : FailedExecution
Claims                : Microsoft.Azure.Management.Insights.Models.RuleManagementEventClaimsDataSource
Condition      :
Description : One or more of the data slices for the Azure Data Factory has failed processing.
Status      : Enabled
Name:       : ADFAlertsSlice
Tags       :
$type          : Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/ADFAlertsSlice
Location   : West US
Name       : ADFAlertsSlice
```

```powershell
Get-AlertRule -res $resourceGroup
```
```
Properties : Microsoft.Azure.Management.Insights.Models.Rule
Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
Location   : West US
Name       : FailedExecutionRunsWest0

Properties : Microsoft.Azure.Management.Insights.Models.Rule
Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest3
Location   : West US
Name       : FailedExecutionRunsWest3
```

```powershell
Get-AlertRule -res $resourceGroup -Name FailedExecutionRunsWest0
```

```
Properties : Microsoft.Azure.Management.Insights.Models.Rule
Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
Location   : West US
Name       : FailedExecutionRunsWest0
```

Kör följande kommandon för get-help att se information och exempel för att cmdleten Get-AlertRule.

```powershell
get-help Get-AlertRule -detailed
```

```powershell
get-help Get-AlertRule -examples
```


Om du ser varningsgenereringen händelser på portalbladet men du inte får e-postmeddelanden, kontrollera om e-postadressen som anges har angetts för att ta emot e-post från externa avsändare. Meddelandena kanske har blockerats av din e-postinställningar.

### <a name="alerts-on-metrics"></a>Aviseringar om mått
I Data Factory du samla in olika mått och skapa aviseringar på mått. Du kan övervaka och skapa aviseringar på följande mätvärden för sektorerna i din data factory:

* **Misslyckade körs**
* **Lyckad körs**

De här måtten är användbara och hjälper dig att få en översikt över övergripande misslyckade och lyckade körs i datafabriken. Mått är orsakat varje gång det är ett segment kör. I början av en timme, är de här måtten samman och pushas till ditt lagringskonto. Om du vill aktivera mätvärden, ställa in ett lagringskonto.

#### <a name="enable-metrics"></a>Aktivera mätvärden
Om du vill aktivera mätvärden, klickar du på följande från den **Data Factory** bladet:

**Övervaka** > **mått** > **diagnostikinställningar** > **diagnostik**

![Diagnostik-länk](./media/data-factory-monitor-manage-pipelines/diagnostics-link.png)

På den **diagnostik** bladet, klickar du på **på**, Välj lagringskonto och på **spara**.

![Diagnostik-bladet](./media/data-factory-monitor-manage-pipelines/diagnostics-blade.png)

Det kan ta upp till en timme för de mätvärden som ska visas på den **övervakning** bladet eftersom mått aggregering sker varje timme.

### <a name="set-up-an-alert-on-metrics"></a>Skapa en avisering om mått
Klicka på den **Data Factory mått** panelen:

![Data factory mått sida vid sida](./media/data-factory-monitor-manage-pipelines/data-factory-metrics-tile.png)

På den **mått** bladet, klickar du på **+ Lägg till avisering** i verktygsfältet.
![Data factory mått bladet > Lägg till avisering](./media/data-factory-monitor-manage-pipelines/add-alert.png)

På den **lägga till en varningsregel** , gör du följande och klicka på **OK**.

* Ange ett namn för aviseringen (exempel: ”misslyckades aviseringen”).
* Ange en beskrivning för aviseringen (exempel: ”skicka ett e-postmeddelande när ett fel uppstår”).
* Markera ett mått (vs ”kunde inte körs”. ”Lyckades körs”).
* Ange ett villkor och ett tröskelvärde.   
* Ange tidsperioden.
* Ange om ett e-postmeddelande ska skickas till ägare, deltagare och läsare.

![Data factory mått bladet > Lägg till regel för varning](./media/data-factory-monitor-manage-pipelines/add-an-alert-rule.png)

När regeln har lagts till, bladet stängs och du ser den nya aviseringen på den **mått** bladet.

![Data factory mått bladet > Ny avisering som lagts till](./media/data-factory-monitor-manage-pipelines/failed-alert-in-metric-blade.png)

Du bör också se antalet aviseringar i den **Varna regler** panelen. Klicka på den **Varna regler** panelen.

![Data factory mått-bladet – Varningsregler](./media/data-factory-monitor-manage-pipelines/alert-rules-tile-rules.png)

På den **aviseringar regler** bladet visas alla befintliga varningar. Om du vill lägga till en avisering klickar du på **Lägg till avisering** i verktygsfältet.

![Varningsregler bladet](./media/data-factory-monitor-manage-pipelines/alert-rules-blade.png)

### <a name="alert-notifications"></a>Varningsmeddelanden
När regeln matchar villkoret, bör du få ett e-postmeddelande som säger att aviseringen har aktiverats. När problemet är löst och aviseringstillståndet matchar inte längre kan få du ett e-postmeddelande att aviseringen har lösts.

Det här skiljer sig händelser där skickas ett meddelande på alla fel som en aviseringsregel är berättigad till.

### <a name="deploy-alerts-by-using-powershell"></a>Distribuera aviseringar med hjälp av PowerShell
Du kan distribuera aviseringar för mått på samma sätt som du gör för händelser.

**Varningsdefinitionen**

```JSON
{
    "contentVersion" : "1.0.0.0",
    "$schema" : "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "parameters" : {},
    "resources" : [
    {
            "name" : "FailedRunsGreaterThan5",
            "type" : "microsoft.insights/alertrules",
            "apiVersion" : "2014-04-01",
            "location" : "East US",
            "properties" : {
                "name" : "FailedRunsGreaterThan5",
                "description" : "Failed Runs greater than 5",
                "isEnabled" : true,
                "condition" : {
                    "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
                    "odata.type" : "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
                    "dataSource" : {
                        "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
                        "odata.type" : "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
                        "resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName
>/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>",
                        "metricName" : "FailedRuns"
                    },
                    "threshold" : 5.0,
                    "windowSize" : "PT3H",
                    "timeAggregation" : "Total"
                },
                "action" : {
                    "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
                    "odata.type" : "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
                    "customEmails" : ["abhinav.gpt@live.com"]
                }
            }
        }
    ]
}
```

Ersätt *subscriptionId*, *resourceGroupName*, och *dataFactoryName* i exemplet med lämpliga värden.

*metricName* stöder för närvarande två värden:

* FailedRuns
* SuccessfulRuns

Distribuera aviseringen

Använda Azure PowerShell-cmdlet för att distribuera aviseringen **ny AzureRmResourceGroupDeployment**som visas i följande exempel:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\FailedRunsGreaterThan5.json
```

Du bör se följande meddelande efter en lyckad distribution:

```
VERBOSE: 12:52:47 PM - Template is valid.
VERBOSE: 12:52:48 PM - Create template deployment 'FailedRunsGreaterThan5'.
VERBOSE: 12:52:55 PM - Resource microsoft.insights/alertrules 'FailedRunsGreaterThan5' provisioning status is succeeded


DeploymentName    : FailedRunsGreaterThan5
ResourceGroupName : adf
ProvisioningState : Succeeded
Timestamp         : 7/27/2015 7:52:56 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
Outputs           
```

Du kan också använda den **Lägg till AlertRule** att distribuera en aviseringsregel. Finns det [Lägg till AlertRule](https://msdn.microsoft.com/library/mt282468.aspx) avsnittet information och exempel.  

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>Flytta en datafabrik till en annan resursgrupp eller prenumeration
Du kan flytta en datafabrik till en annan resursgrupp eller en annan prenumeration med hjälp av den **flytta** kommandot liggande knappen på startsidan i din data factory.

![Flytta data factory](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

Du kan också flytta alla relaterade resurser (till exempel aviseringar som är associerade med data factory), tillsammans med datafabriken.

![Flytta dialogrutan resurser](./media/data-factory-monitor-manage-pipelines/MoveResources.png)
