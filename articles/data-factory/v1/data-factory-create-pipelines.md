---
title: Skapa/Schemalägg Pipelines, länka aktiviteter i Data Factory | Microsoft Docs
description: Lär dig att skapa en datapipeline i Azure Data Factory för att flytta och omvandla data. Skapa en datadriven arbetsflöde att producera klar för att använda information.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 13b137c7-1033-406f-aea7-b66f25b313c0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 09fd2f38c3746cf92d576325058dc36221ae50cd
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38668035"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Pipelines och aktiviteter i Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-create-pipelines.md)
> * [Version 2 (aktuell version)](../concepts-pipelines-activities.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [Pipelines i V2](../concepts-pipelines-activities.md).

I den här artikeln beskriver vi pipelines och aktiviteter i Azure Data Factory och hur du kan använda dem för att konstruera datadrivna arbetsflöden från slutpunkt till slutpunkt för dina dataförflyttnings- och databearbetningsscenarier.  

> [!NOTE]
> Den här artikeln förutsätter att du har gått igenom [introduktion till Azure Data Factory](data-factory-introduction.md). Om du inte har praktiska-på-upplevelse med att skapa datafabriker, gå igenom [självstudien för omvandling av data](data-factory-build-your-first-pipeline.md) och/eller [data movement självstudien](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) skulle hjälpa dig att bättre förstå den här artikeln.  

## <a name="overview"></a>Översikt
En datafabrik kan ha en eller flera pipelines. En pipeline är en logisk gruppering aktiviteter som tillsammans utför en uppgift. Aktiviteterna i en pipeline definierar åtgärder som ska utföras för dina data. Du kan till exempel använda en kopieringsaktivitet för att kopiera data från en lokal SQL Server till Azure Blob Storage. Använd sedan en Hive-aktivitet som kör ett Hive-skript på ett Azure HDInsight-kluster för att bearbeta/transformera data från Blob Storage för att producera utdata. Till sist använder du en andra kopieringsaktivitet för att kopiera utdata till ett Azure SQL Data Warehouse som Business Intelligence-rapporteringslösningar har skapats på. 

En aktivitet kan ha noll eller flera [indatauppsättningar](data-factory-create-datasets.md) och kan producera en eller flera [utdatauppsättningar](data-factory-create-datasets.md). I följande diagram visas förhållandet mellan pipeline, aktivitet och datauppsättning i Data Factory: 

![Förhållandet mellan pipeline, aktivitet och datauppsättning](media/data-factory-create-pipelines/relationship-pipeline-activity-dataset.png)

En pipeline kan du hantera aktiviteter som en grupp i stället för vart och ett enskilt. Du kan till exempel distribuera, schemalägga, pausa och återuppta en pipeline i stället för att ta itu med aktiviteter i pipelinen oberoende av varandra.

Data Factory stöder två typer av aktiviteter: aktiviteter för dataförflyttning och datatransformering. Varje aktivitet kan ha noll eller flera [datauppsättningar](data-factory-create-datasets.md) och framställer en eller flera datauppsättningar som utdata.

En indatauppsättning representerar indata för en aktivitet i pipeline och en utdatauppsättning representerar utdata för aktiviteten. Datauppsättningar identifierar data inom olika datalager, till exempel tabeller, filer, mappar och dokument. När du har skapat en datauppsättning kan du använda den med aktiviteter i en pipeline. Till exempel kan en datauppsättning vara en in-/utdatauppsättning för en kopieringsaktivitet eller en HDInsightHive-aktivitet. Mer information om datauppsättning finns i artikeln [Datauppsättningar i Azure Data Factory](data-factory-create-datasets.md).

### <a name="data-movement-activities"></a>Dataförflyttningsaktiviteter
Kopieringsaktiviteten i Data Factory kopierar data från källans datalager till mottagarens datalager. Data Factory har stöd för följande datalager. Data kan skrivas från valfri källa till valfri mottagare. Klicka på ett datalager om du vill veta hur du kopierar data till och från det datalagret.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Datalager med * kan finnas lokalt eller på Azure IaaS, och kräver att du installerar [Data Management Gateway](data-factory-data-management-gateway.md) på en lokal/Azure IaaS-dator.

Mer information finns i artikeln [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md).

### <a name="data-transformation-activities"></a>Datatransformeringsaktiviteter
[!INCLUDE [data-factory-transformation-activities](../../../includes/data-factory-transformation-activities.md)]

Mer information finns i artikeln [Datatransformeringsaktiviteter](data-factory-data-transformation-activities.md).

### <a name="custom-net-activities"></a>Anpassa .NET-aktiviteter 
Om du behöver flytta data till/från ett datalager som Kopieringsaktivitet inte stöder, eller Transformera data med egen logik kan du skapa en **anpassad .NET-aktivitet**. Mer information om att skapa och använda en anpassad aktivitet finns i [Use custom activities in an Azure Data Factory pipeline (Använda anpassade aktiviteter i en Azure Data Factory-pipeline)](data-factory-use-custom-activities.md).

## <a name="schedule-pipelines"></a>Schema-pipelines
En pipeline är aktiv endast mellan dess **starta** tid och **slutet** tid. Då utförs inte före starttiden eller efter sluttiden. Om pipelinen pausas körs den inte oavsett dess start- och tid. För en pipeline kan köras, bör det inte pausas. Se [schemaläggning och körning](data-factory-scheduling-and-execution.md) förstår hur schemaläggning och körning fungerar i Azure Data Factory.

## <a name="pipeline-json"></a>Pipeline JSON
Nu tar vi en närmare titt på hur en pipeline definieras i JSON-format. Den allmänna strukturen för en pipeline ser ut så här:

```json
{
    "name": "PipelineName",
    "properties": 
    {
        "description" : "pipeline description",
        "activities":
        [

        ],
        "start": "<start date-time>",
        "end": "<end date-time>",
        "isPaused": true/false,
        "pipelineMode": "scheduled/onetime",
        "expirationTime": "15.00:00:00",
        "datasets": 
        [
        ]
    }
}
```

| Tagga | Beskrivning | Krävs |
| --- | --- | --- |
| namn |Namnet på pipeline. Ange ett namn som representerar åtgärden som pipeline utför. <br/><ul><li>Max. antal tecken: 260</li><li>Måste börja med en bokstav, en siffra eller ett understreck (_)</li><li>Följande tecken är inte tillåtna ”:”., ”+” ”,”?, ”/”, ”<” ”, >” ”, *”, ”%”, ”&” ”,:” ”,\\”</li></ul> |Ja |
| beskrivning | Ange texten som beskriver vad pipeline används till. |Ja |
| activities | Avsnittet **activities** kan ha en eller flera definierade aktiviteter. Se nästa avsnitt för information om aktiviteter JSON-element. | Ja |  
| start | Starta datum / tid för pipelinen. Måste vara i [ISO-format](http://en.wikipedia.org/wiki/ISO_8601). Till exempel: `2016-10-14T16:32:41Z`. <br/><br/>Det är möjligt att ange en lokal tid, till exempel en EST tid. Här är ett exempel: `2016-02-27T06:00:00-05:00`”, vilket är 6 AM uppskattad<br/><br/>Egenskaper för start- och ange tillsammans aktiva perioden för pipelinen. Utdatasegment produceras bara med i den här aktiva period. |Nej<br/><br/>Om du anger ett värde för end-egenskapen, måste du ange värdet för egenskapen start.<br/><br/>Start- och sluttider kan vara tom för att skapa en pipeline. Du måste ange båda värdena för att ställa in en aktiva perioden för pipelinen att köra. Om du inte anger start- och sluttider när du skapar en pipeline kan du ange dem med hjälp av cmdleten Set-AzureRmDataFactoryPipelineActivePeriod senare. |
| slut | Slutdatum /-tid för pipelinen. Om anges måste vara i ISO-format. Exempel: `2016-10-14T17:32:41Z` <br/><br/>Det är möjligt att ange en lokal tid, till exempel en EST tid. Här är ett exempel: `2016-02-27T06:00:00-05:00`, vilket är 6 AM EST.<br/><br/>Om du vill köra pipelinen på obestämd tid, ange 9999-09-09 som värde för end-egenskapen. <br/><br/> En pipeline är aktiv endast mellan dess start- och sluttid. Då utförs inte före starttiden eller efter sluttiden. Om pipelinen pausas körs den inte oavsett dess start- och tid. För en pipeline kan köras, bör det inte pausas. Se [schemaläggning och körning](data-factory-scheduling-and-execution.md) förstår hur schemaläggning och körning fungerar i Azure Data Factory. |Nej <br/><br/>Om du anger ett värde för egenskapen start, måste du ange värdet för egenskapen slutet.<br/><br/>Se information om den **starta** egenskapen. |
| isPaused | Om värdet är true, pipelinen inte körs. Det är i pausläge. Standardvärde = false. Du kan använda den här egenskapen för att aktivera eller inaktivera en pipeline. |Nej |
| pipelineMode | Metod för att schemalägga körningar för pipelinen. Tillåtna värden är: schemalagd (standard), genomfört.<br/><br/>”Schemalagd” anger att pipelinen körs vid ett angivet tidsintervall enligt den aktiva perioden (start- och -tid). ”Onetime” anger att pipelinen körs bara en gång. Onetime pipelines som är skapade får inte vara ändrade/uppdateras för tillfället. Se [Onetime pipeline](#onetime-pipeline) mer information om hur du genomfört. |Nej |
| expirationTime | Tid när du har skapat som den [enstaka pipeline](#onetime-pipeline) är giltig och bör vara etablerade. Om den inte har någon aktiv, misslyckades, eller väntande körningar, pipelinen är automatiskt når bort när den förfallotid. Standardvärde: `"expirationTime": "3.00:00:00"`|Nej |
| datauppsättningar |Lista över datauppsättningar som ska användas av aktiviteterna som definierats i pipelinen. Den här egenskapen kan användas för att definiera datauppsättningar som är specifika för denna pipeline och inte har definierats i data factory. Datauppsättningar som definierats i denna pipeline kan bara användas av denna pipeline och kan inte delas. Se [begränsade datauppsättningar](data-factory-create-datasets.md#scoped-datasets) mer information. |Nej |

## <a name="activity-json"></a>Aktivitets-JSON
Avsnittet **activities** kan ha en eller flera definierade aktiviteter. Varje aktivitet har följande toppnivåstruktur:

```json
{
    "name": "ActivityName",
    "description": "description", 
    "type": "<ActivityType>",
    "inputs":  "[]",
    "outputs":  "[]",
    "linkedServiceName": "MyLinkedService",
    "typeProperties":
    {

    },
    "policy":
    {
    },
    "scheduler":
    {
    }
}
```

I följande tabell beskrivs egenskaperna i definitionen för aktivitets-JSON:

| Tagga | Beskrivning | Krävs |
| --- | --- | --- |
| namn | Namnet på aktiviteten. Ange ett namn som representerar åtgärden som aktiviteten utför. <br/><ul><li>Max. antal tecken: 260</li><li>Måste börja med en bokstav, en siffra eller ett understreck (_)</li><li>Följande tecken är inte tillåtna ”:”., ”+” ”,”?, ”/”, ”<” ”, >” ”, *”, ”%”, ”&” ”,:” ”,\\”</li></ul> |Ja |
| beskrivning | Text som beskriver vad aktiviteten används till |Ja |
| typ | Typ av aktivitet. Se den [Dataförflyttningsaktiviteter](#data-movement-activities) och [Datatransformeringsaktiviteter](#data-transformation-activities) avsnitten om olika typer av aktiviteter. |Ja |
| inmatningar |Inkommande tabeller som används av aktiviteten<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Ja |
| utdata |Utdata tabeller som används av aktiviteten.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": "outputtable1" } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": "outputtable1" }, { "name": "outputtable2" }  ],` |Ja |
| linkedServiceName |Namnet på den länkade tjänst som används av aktiviteten. <br/><br/>En aktivitet kan kräva att du anger den länkade tjänst som länkar till den nödvändiga beräkningsmiljön. |Ja för HDInsight-aktivitet och Azure Machine Learning-Batchbedömningsaktivitet <br/><br/>Nej för alla andra |
| typeProperties |Egenskaper i den **typeProperties** avsnittet beror på typ av aktivitet. Om du vill visa typegenskaper för en aktivitet klickar du på länkarna till aktiviteten i föregående avsnitt. | Nej |
| policy |Principer som påverkar körningsbeteende för aktiviteten. Om det inte anges används standardprinciper. |Nej |
| Scheduler | ”schemaegenskapen” används för att definiera önskade schemaläggning för aktiviteten. Dess subegenskaper är samma som de i den [tillgänglighet-egenskapen i en datauppsättning](data-factory-create-datasets.md#dataset-availability). |Nej |


### <a name="policies"></a>Principer
Principer påverkar körningsbeteende för en aktivitet, särskilt när sektorn i en tabell har bearbetats. I följande tabell innehåller information.

| Egenskap  | Tillåtna värden | Standardvärde | Beskrivning |
| --- | --- | --- | --- |
| samtidighet |Integer <br/><br/>Maxvärde: 10 |1 |Antal samtidiga körningar av aktiviteten.<br/><br/>Den avgör antalet körningar för parallell aktivitet som kan inträffa på olika segment. Till exempel om en aktivitet behöver genomgå påskyndar en stor mängd tillgängliga data, med ett större värde för samtidighet databearbetningen. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Anger sorteringen av datasektorer som bearbetas.<br/><br/>Till exempel om du har 2 skär (en händer klockan 4 och en ny 17: 00) och båda är väntar på att köras. Om du ställer in executionPriorityOrder vara NewestFirst bearbetas sektor 17: 00 först. På samma sätt om du ställer in executionPriorityORder vara OldestFIrst bearbetas sedan sektorn klockan 4. |
| retry |Integer<br/><br/>Max-värdet kan vara 10 |0 |Antal försök innan databearbetningen för sektorn har markerats som ett fel. Körningsmiljön för aktiviteten för en datasektor göras upp till det angivna antalet återförsök. Återförsök sker så snart som möjligt efter felet. |
| timeout |Tidsintervall |00:00:00 |Tidsgränsen för aktiviteten. Exempel: 00:10:00 (inbegriper timeout 10 minuter)<br/><br/>Om ett värde inte har angetts eller är 0, är tidsgränsen oändliga.<br/><br/>Om bearbetningstiden som data på en sektor överskrider timeout-värdet, den avbryts och försöker systemet att försök bearbetningen. Antal återförsök beror på egenskapen försök igen. När timeout uppstår är status nådde sin tidsgräns. |
| fördröjning |Tidsintervall |00:00:00 |Ange fördröjning före databearbetningen av sektorn startar.<br/><br/>Körningen av aktiviteten för en datasektor startas när fördröjningen har passerat den förväntade tiden för körningen.<br/><br/>Exempel: 00:10:00 (inbegriper fördröjning på 10 minuter) |
| longRetry |Integer<br/><br/>Maxvärde: 10 |1 |Antal långt återförsök innan sektorn körningen misslyckades.<br/><br/>longRetry försök är fördelade av longRetryInterval. Så om du vill ange en tid mellan nya försök använda longRetry. Om både återförsök och longRetry anges varje longRetry försök återförsök det maximala antalet försök används och försök igen * longRetry.<br/><br/>Till exempel, om vi har följande inställningar i aktivitetsprincipen:<br/>Försök: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Anta att det finns endast en sektor att köra (status väntar) och körningsmiljön för aktiviteten misslyckas varje gång. Det skulle ursprungligen vara 3 körning av på varandra följande försök. Efter varje försök är statusen för sektorn försök igen. När det är första 3 försök över kan är statusen för sektorn LongRetry.<br/><br/>Efter en timme (det vill säga Longretryinteval's värde), skulle det finnas en annan uppsättning 3 körning av på varandra följande försök. Efter det skulle vara misslyckades sektorstatus och inga fler försök skulle göras. Därför har övergripande 6 försök gjorts.<br/><br/>Om alla körningen lyckades visas statusen för sektorn är klar och inga fler försök kommer att göras.<br/><br/>longRetry kan användas i situationer där beroende data kommer till icke-deterministisk gånger eller den övergripande miljön är flaky under vilken databearbetningen sker. I sådana fall kan göra återförsök efter varandra inte kan hjälpa och gör det när du har ett intervall på tid du leder till önskade utdata.<br/><br/>Liten varning: anger inte hög värden för longRetry eller longRetryInterval. Vanligtvis en högre värden andra systemfel problem. |
| longRetryInterval |Tidsintervall |00:00:00 |Fördröjningen mellan långt återförsök |

## <a name="sample-copy-pipeline"></a>Exempel på kopieringspipeline
I följande exempel på pipeline finns det en aktivitet av typen **Copy** (Kopiera) i avsnittet **activities**. I det här exempel kopierar [kopieringsaktiviteten](data-factory-data-movement-activities.md) data från Azure Blob Storage till en Azure SQL-databas. 

```json
{
  "name": "CopyPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputDataset"
          }
        ],
        "outputs": [
          {
            "name": "OutputDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
} 
```

Observera följande punkter:

* I avsnittet Aktiviteter finns det bara en aktivitet vars **typ** anges till **Kopia**.
* Indata för aktiviteten är inställd på **InputDataset** och utdata för aktiviteten är inställd på **OutputDataset**. I artikeln [Datauppsättningar](data-factory-create-datasets.md) finns information om hur du definierar datauppsättningar i JSON. 
* I avsnittet för **typeProperties** har **BlobSource** angetts som källtyp och **SqlSink** har angetts som mottagartyp. I den [dataförflyttningsaktiviteter](#data-movement-activities) klickar du på de datalager som du vill använda som källa eller mottagare att lära dig mer om att flytta data till/från det datalagringen. 

En fullständig genomgång för att skapa denna pipeline finns i [självstudie: kopiera data från Blob Storage till SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

## <a name="sample-transformation-pipeline"></a>Exempel på transfomeringspipeline
I följande exempel på pipeline finns det en aktivitet av typen **HDInsightHive** i avsnittet **activities**. I det här exemplet transformerar [HDInsight Hive-aktiviteten](data-factory-hive-activity.md) data från Azure Blob Storage genom att köra en Hive-skriptfil på ett Azure HDInsight Hadoop-kluster. 

```json
{
    "name": "TransformPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
        "start": "2016-04-01T00:00:00Z",
        "end": "2016-04-02T00:00:00Z",
        "isPaused": false
    }
}
```

Observera följande punkter: 

* I activities-avsnittet finns det bara en aktivitet vars **typ** anges till **HDInsightHive**.
* Hive-skriptfilen **partitionweblogs.hql** lagras i Azure-lagringskontot (anges med scriptLinkedService, kallas **AzureStorageLinkedService**), och i mappen **skript** i behållaren **adfgetstarted**.
* Den `defines` används för att ange körningsinställningar som skickas till hive-skriptet som Hive-konfigurationsvärden (t.ex `${hiveconf:inputtable}`, `${hiveconf:partitionedtable}`).

Avsnittet **typeProperties** är olika för varje transformeringsaktivitet. Mer information om egenskaper som stöds för en transformeringsaktivitet, klickar du på transformeringsaktivitet i den [datatransformeringsaktiviteter](#data-transformation-activities) tabell. 

En fullständig genomgång för att skapa denna pipeline finns i [självstudie: skapa din första pipeline för att bearbeta data med Hadoop-kluster](data-factory-build-your-first-pipeline.md). 

## <a name="multiple-activities-in-a-pipeline"></a>Flera aktiviteter i en pipeline
De två föregående exemplen innehåller bara en aktivitet. Du kan fler än en aktivitet i en pipeline.  

Om du har flera aktiviteter i en pipeline och utdata för en aktivitet är inte en indata för en annan aktivitet, kan aktiviteterna köras parallellt om indata segmenten för aktiviteterna är redo. 

Du kan länka två aktiviteter genom att låta datauppsättningen för utdata för en aktivitet som den inkommande datauppsättningen för den andra aktiviteten. Den andra aktiviteten körs bara när den första som är helt klar.

![Länkning av aktiviteter i samma pipelinen](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

I det här exemplet pipelinen har två aktiviteter: Activity1 och Activity2. Activity1 tar Dataset1 som indata och producerar utdata Dataset2. Aktiviteten tar Dataset2 som indata och producerar utdata Dataset3. Sedan utdata från Activity1 (Dataset2) är inmatning av Activity2, Activity2 körs endast när aktiviteten har slutförts och producerade sektorn Dataset2. Om Activity1 av någon anledning misslyckas och genererar inte Dataset2 sektorn, aktivitet 2 fungerar inte för den sektorn (till exempel: 9: 00 till 10 AM). 

Du kan också länka aktiviteter som finns i olika pipelines.

![Kedjesammansättning av aktiviteter i två pipelines](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

I det här exemplet har Pipeline1 bara en aktivitet som tar Dataset1 som indata och producerar Dataset2 som utdata. Pipeline2 har också bara en aktivitet som tar Dataset2 som indata och Dataset3 som utdata. 

Mer information finns i [schemaläggning och körning](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline). 

## <a name="create-and-monitor-pipelines"></a>Skapa och övervaka pipelines
Du kan skapa pipelines med någon av dessa verktyg och SDK: er. 

- Guiden Kopiera. 
- Azure Portal
- Visual Studio
- Azure PowerShell
- Azure Resource Manager-mall
- REST-API
- .NET-API

Se följande självstudiekurser för stegvisa instruktioner för att skapa pipelines med någon av dessa verktyg och SDK: er.
 
- [Skapa en pipeline med en datatransformeringsaktivitet](data-factory-build-your-first-pipeline.md)
- [Skapa en pipeline med en aktivitet för flytt av data](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

När en pipeline är skapat/distribueras kan du hantera och övervaka dina pipelines med hjälp av bladen på Azure portal eller övervaka och Hanteringsappen. Se följande avsnitt hittar du stegvisa instruktioner. 

- [Övervaka och hantera pipelines med hjälp av Azure-portalblad](data-factory-monitor-manage-pipelines.md).
- [Övervaka och hantera pipelines med hjälp av appen övervaka och hantera](data-factory-monitor-manage-app.md)


## <a name="onetime-pipeline"></a>Onetime pipeline
Du kan skapa och schemalägga en pipeline kan köras med jämna mellanrum (till exempel: per timme eller per dag) i start- och sluttider som du anger i pipeline-definition. Se [schemalägga aktiviteter](#scheduling-and-execution) mer information. Du kan också skapa en pipeline som körs bara en gång. Om du vill göra det anger du den **pipelineMode** -egenskapen i pipeline-definition till **genomfört** enligt följande JSON-exempel. Standardvärdet för den här egenskapen är **schemalagda**.

```json
{
    "name": "CopyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ]
                "name": "CopyActivity-0"
            }
        ]
        "pipelineMode": "OneTime"
    }
}
```

Observera följande:

* **Starta** och **slutet** sluttider för pipelinen har inte angetts.
* **Tillgänglighet** för indata och utdata datauppsättningar har angetts (**frekvens** och **intervall**), även om Data Factory inte använder värdena.  
* Diagramvyn visas inte enstaka pipelines. Det här beteendet är avsiktligt.
* Enstaka pipelines kan inte uppdateras. Du kan klona en enstaka pipeline, Byt namn på den, uppdatera egenskaper och distribuerar den för att skapa en ny.


## <a name="next-steps"></a>Nästa steg
- Läs mer om datauppsättningar, [skapa datauppsättningar](data-factory-create-datasets.md) artikeln. 
- Läs mer om hur pipelines schemaläggs och körs, [schemaläggning och körning i Azure Data Factory](data-factory-scheduling-and-execution.md) artikeln. 
  

