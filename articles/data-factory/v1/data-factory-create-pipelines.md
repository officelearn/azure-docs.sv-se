---
title: Skapa/schemalägga pipelines, kedjeaktiviteter i datafabriken
description: Lär dig att skapa en datapipeline i Azure Data Factory för att flytta och omvandla data. Skapa ett datadrivet arbetsflöde för att producera redo att använda information.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: f93bea240ee3f139c9be84199d116f9f3f231261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281527"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Pipelines och aktiviteter i Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-create-pipelines.md)
> * [Version 2 (aktuell version)](../concepts-pipelines-activities.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [Pipelines i V2](../concepts-pipelines-activities.md).

I den här artikeln beskriver vi pipelines och aktiviteter i Azure Data Factory och hur du kan använda dem för att konstruera datadrivna arbetsflöden från slutpunkt till slutpunkt för dina dataförflyttnings- och databearbetningsscenarier.

> [!NOTE]
> Den här artikeln förutsätter att du har gått igenom [Introduktion till Azure Data Factory](data-factory-introduction.md). Om du inte har praktisk erfarenhet av att skapa datafabriker, gå igenom [dataomvandling handledning](data-factory-build-your-first-pipeline.md) och / eller [data rörelse handledning](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) skulle hjälpa dig att förstå den här artikeln bättre.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Översikt
En datafabrik kan ha en eller flera pipelines. En pipeline är en logisk gruppering aktiviteter som tillsammans utför en uppgift. Aktiviteterna i en pipeline definierar åtgärder som ska utföras för dina data. Du kan till exempel använda en kopieringsaktivitet för att kopiera data från en lokal SQL Server till Azure Blob Storage. Använd sedan en Hive-aktivitet som kör ett Hive-skript på ett Azure HDInsight-kluster för att bearbeta/transformera data från Blob Storage för att producera utdata. Till sist använder du en andra kopieringsaktivitet för att kopiera utdata till ett Azure SQL Data Warehouse som Business Intelligence-rapporteringslösningar har skapats på.

En aktivitet kan ha noll eller flera [indatauppsättningar](data-factory-create-datasets.md) och kan producera en eller flera [utdatauppsättningar](data-factory-create-datasets.md). I följande diagram visas förhållandet mellan pipeline, aktivitet och datauppsättning i Data Factory:

![Relation mellan pipeline, aktivitet och datauppsättning](media/data-factory-create-pipelines/relationship-pipeline-activity-dataset.png)

Med en pipeline kan du hantera aktiviteter som en uppsättning i stället för var och en individuellt. Du kan till exempel distribuera, schemalägga, pausa och återuppta en pipeline i stället för att hantera aktiviteter i pipelinen oberoende av dem.

Data Factory stöder två typer av aktiviteter: aktiviteter för dataförflyttning och datatransformering. Varje aktivitet kan ha noll eller fler [indatauppsättningar](data-factory-create-datasets.md) och producera en eller flera utdatauppsättningar.

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
Om du behöver flytta data till/från ett datalager som kopieringsaktiviteten inte stöder eller omvandla data med din egen logik skapar du en **anpassad .NET-aktivitet**. Mer information om att skapa och använda en anpassad aktivitet finns i [Use custom activities in an Azure Data Factory pipeline (Använda anpassade aktiviteter i en Azure Data Factory-pipeline)](data-factory-use-custom-activities.md).

## <a name="schedule-pipelines"></a>Schemalägga pipelines
En pipeline är bara aktiv mellan **starttiden** och **sluttiden.** Den körs inte före starttiden eller efter sluttiden. Om pipelinen pausas körs den inte oavsett start- och sluttid. För att en pipeline ska köras bör den inte pausas. Se [Schemaläggning och körning](data-factory-scheduling-and-execution.md) för att förstå hur schemaläggning och körning fungerar i Azure Data Factory.

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
| namn |Namnet på pipeline. Ange ett namn som representerar åtgärden som pipeline utför. <br/><ul><li>Max. antal tecken: 260</li><li>Måste börja med ett bokstavsnummer\_eller ett understreck ( )</li><li>Följande tecken är inte tillåtna: ".", "+", "?", "/", "<",">","\*""%","&",":""\\"</li></ul> |Ja |
| description | Ange texten som beskriver vad pipeline används till. |Ja |
| activities | Avsnittet **activities** kan ha en eller flera definierade aktiviteter. Se nästa avsnitt för mer information om aktiviteterna JSON element. | Ja |
| start | Startdatum för pipelinen. Måste vara i [ISO-format](https://en.wikipedia.org/wiki/ISO_8601). Till exempel: `2016-10-14T16:32:41Z`. <br/><br/>Det är möjligt att ange en lokal tid, till exempel en EST-tid. Här är ett `2016-02-27T06:00:00-05:00`exempel: ", som är 06:00 EST.<br/><br/>Start- och slutegenskaperna anger tillsammans aktiv period för pipelinen. Utdatasegment produceras endast med under den här aktiva perioden. |Inga<br/><br/>Om du anger ett värde för slutegenskapen måste du ange värdet för startegenskapen.<br/><br/>Start- och sluttiderna kan båda vara tomma för att skapa en pipeline. Du måste ange båda värdena för att ange en aktiv period för att pipelinen ska köras. Om du inte anger start- och sluttider när du skapar en pipeline kan du ställa in dem med cmdlet Set-AzDataFactoryPipelineActivePeriod senare. |
| slut | Slutdatumtid för pipelinen. Om det anges måste vara i ISO-format. Exempel: `2016-10-14T17:32:41Z` <br/><br/>Det är möjligt att ange en lokal tid, till exempel en EST-tid. Här är ett `2016-02-27T06:00:00-05:00`exempel: , som är 06:00 EST.<br/><br/>Om du vill köra pipelinen på obestämd tid, anger du 9999-09-09 som värde för slutegenskapen. <br/><br/> En pipeline är bara aktiv mellan starttiden och sluttiden. Den körs inte före starttiden eller efter sluttiden. Om pipelinen pausas körs den inte oavsett start- och sluttid. För att en pipeline ska köras bör den inte pausas. Se [Schemaläggning och körning](data-factory-scheduling-and-execution.md) för att förstå hur schemaläggning och körning fungerar i Azure Data Factory. |Inga <br/><br/>Om du anger ett värde för startegenskapen måste du ange värdet för slutegenskapen.<br/><br/>Se anteckningar **start** för startegenskapen. |
| isPaused | Om den är inställd på true körs inte pipelinen. Det är i pausat tillstånd. Standardvärde = falskt. Du kan använda den här egenskapen för att aktivera eller inaktivera en pipeline. |Inga |
| pipelineMode | Metoden för schemaläggning körs för pipelinen. Tillåtna värden är: schemalagda (standard), en gång.<br/><br/>"Schemalagd" anger att rörledningen körs med ett angivet tidsintervall beroende på dess aktiva period (start- och sluttid). "Onetime" anger att pipelinen bara körs en gång. Engångspipelderna när de har skapats kan inte ändras/uppdateras för närvarande. Se [Onetime pipeline](#onetime-pipeline) för information om engångsinställning. |Inga |
| förfallodatumTime | Varaktigheten av tiden efter skapandet för vilken [engångspipelinen](#onetime-pipeline) är giltig och bör förbli etablerad. Om den inte har några aktiva, misslyckade eller väntande körningar tas pipelinen automatiskt bort när den når förfallotiden. Standardvärdet:`"expirationTime": "3.00:00:00"`|Inga |
| datauppsättningar |Förteckning över de datamängder som ska användas av aktiviteter som definierats på gång. Den här egenskapen kan användas för att definiera datauppsättningar som är specifika för den här pipelinen och som inte har definierats i datafabriken. Datauppsättningar som definierats i den här pipelinen kan endast användas av den här pipelinen och kan inte delas. Mer information finns i [Scoped-datauppsättningar.](data-factory-create-datasets.md#scoped-datasets) |Inga |

## <a name="activity-json"></a>Aktivitets-JSON
Avsnittet **activities** kan ha en eller flera definierade aktiviteter. Varje aktivitet har följande struktur på den högsta nivån:

```json
{
    "name": "ActivityName",
    "description": "description",
    "type": "<ActivityType>",
    "inputs": "[]",
    "outputs": "[]",
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
| namn | Namnet på aktiviteten. Ange ett namn som representerar åtgärden som aktiviteten utför. <br/><ul><li>Max. antal tecken: 260</li><li>Måste börja med ett bokstavsnummer\_eller ett understreck ( )</li><li>Följande tecken är inte tillåtna: ".", "+", "?", "/", "<",">","*","%","&",":""\\"</li></ul> |Ja |
| description | Text som beskriver vad aktiviteten används till |Ja |
| typ | Typ av aktivitet. Se avsnitten [Data movement Activities](#data-movement-activities) och Data Transformation [Activities](#data-transformation-activities) för olika typer av aktiviteter. |Ja |
| Ingångar |Indatatabeller som används av aktiviteten<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Ja |
| Utgångar |Utdatatabeller som används av aktiviteten.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": "outputtable1" } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": "outputtable1" }, { "name": "outputtable2" }  ],` |Ja |
| linkedServiceName |Namnet på den länkade tjänst som används av aktiviteten. <br/><br/>En aktivitet kan kräva att du anger den länkade tjänst som länkar till den nödvändiga beräkningsmiljön. |Ja för HDInsight-aktivitet och Azure Machine Learning Batch Scoring Activity <br/><br/>Nej för alla andra |
| typeProperties |Egenskaperna i avsnittet **typeProperties** beror på aktivitetens typ. Om du vill visa typegenskaper för en aktivitet klickar du på länkarna till aktiviteten i föregående avsnitt. | Inga |
| policy |Principer som påverkar körningsbeteende för aktiviteten. Om det inte anges används standardprinciper. |Inga |
| scheduler | Egenskapen "scheduler" används för att definiera önskad schemaläggning för aktiviteten. Dess underegenskaper är desamma som de i [egenskapen availability i en datauppsättning](data-factory-create-datasets.md#dataset-availability). |Inga |

### <a name="policies"></a>Principer
Principer påverkar körningsbeteendet för en aktivitet, särskilt när segmentet i en tabell bearbetas. Följande tabell innehåller information.

| Egenskap | Tillåtna värden | Standardvärde | Beskrivning |
| --- | --- | --- | --- |
| samtidighet |Integer <br/><br/>Maxvärde: 10 |1 |Antal samtidiga körningar av aktiviteten.<br/><br/>Det avgör antalet parallella aktivitetskörningar som kan hända på olika segment. Om en aktivitet till exempel behöver gå igenom en stor uppsättning tillgängliga data, snabbar databearbetningen upp databearbetningen om du har ett större samtidighetsvärde. |
| körningPriorityOrder |NyasteFörsta<br/><br/>ÄldstaFörsta |ÄldstaFörsta |Bestämmer ordningen på datasegment som bearbetas.<br/><br/>Om du till exempel har 2 segment (en som händer klockan 16.00 och en annan klockan 17.00) och båda väntar på körning. Om du ställer in körningenPriorityOrder till nyast först bearbetas segmentet vid 17:00 först. På samma sätt om du anger att körningenPriorityORder ska vara OldestFIrst bearbetas segmentet vid 16:00. |
| retry |Integer<br/><br/>Maxvärde kan vara 10 |0 |Antal återförsök före databearbetningen för segmentet markeras som Fel. Aktivitetskörning för ett datasegment görs om upp till det angivna antalet försök. Återförsöket görs så snart som möjligt efter felet. |
| timeout |TimeSpan |00:00:00 |Timeout för aktiviteten. Exempel: 00:10:00 (innebär timeout 10 minuter)<br/><br/>Om ett värde inte anges eller är 0 är timeout oändlig.<br/><br/>Om databearbetningstiden för ett segment överskrider timeout-värdet avbryts det och systemet försöker försöka bearbeta igen. Antalet återförsök beror på egenskapen för återförsök. När timeout inträffar anges statusen till TimedOut. |
| Försening |TimeSpan |00:00:00 |Ange fördröjningen innan databearbetningen av segmentet startar.<br/><br/>Körningen av aktivitet för ett datasegment startas när fördröjningen har passerat den förväntade körningstiden.<br/><br/>Exempel: 00:10:00 (innebär fördröjning på 10 minuter) |
| longRetry |Integer<br/><br/>Maxvärde: 10 |1 |Antalet långa försök att försöka igen innan segmentkörningen misslyckas.<br/><br/>longRetry-försök är fördelade efter longRetryInterval. Så om du behöver ange en tid mellan försök att försöka igen använder du longRetry. If both Retry and longRetry are specified, each longRetry attempt includes Retry attempts and the max number of attempts is Retry * longRetry.<br/><br/>Om vi till exempel har följande inställningar i aktivitetspolicyn:<br/>Försök igen: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Anta att det bara finns ett segment att köra (status väntar) och aktivitetskörningen misslyckas varje gång. Inledningsvis skulle det finnas 3 på varandra följande avrättningsförsök. Efter varje försök skulle segmentstatusen vara Försök igen. Efter första 3 försök är över, skulle segmentet status LongRetry.<br/><br/>Efter en timme (det vill vara longRetryIntevals värde) skulle det finnas ytterligare en uppsättning med 3 på varandra följande körningsförsök. Därefter skulle segmentstatusen misslyckas och inga fler försök skulle göras. Därför gjordes totalt 6 försök.<br/><br/>Om någon körning lyckas, skulle segmentstatus vara Klar och inga fler försök görs.<br/><br/>longRetry kan användas i situationer där beroende data anländer till icke-deterministiska tider eller den övergripande miljön är flagnande under vilken databehandling sker. I sådana fall kan det hända att du inte hjälper till att göra försök igen efter varandra och att göra det efter ett tidsintervall resulterar i önskad utgång.<br/><br/>Varningens ord: ange inte höga värden för longRetry eller longRetryInterval. Vanligtvis innebär högre värden andra systemproblem. |
| longRetryInterval |TimeSpan |00:00:00 |Fördröjningen mellan långa försök att försöka igen |

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
* I avsnittet för **typeProperties** har **BlobSource** angetts som källtyp och **SqlSink** har angetts som mottagartyp. I avsnittet [Dataflyttningsaktiviteter](#data-movement-activities) klickar du på det datalager som du vill använda som källa eller diskho för att lära dig mer om hur du flyttar data till/från det datalagret.

En fullständig genomgång av hur du skapar den här pipelinen finns i [Självstudiekurs: Kopiera data från Blob Storage till SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

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
* Hive-skriptfilen **partitionweblogs.hql** lagras i Azure-lagringskontot (anges med scriptLinkedService, kallas **AzureStorageLinkedService**), och i mappen **skript** i containern **adfgetstarted**.
* Avsnittet `defines` används för att ange de körningsinställningar som skickas till registreringsdatafilen som `${hiveconf:inputtable}` `${hiveconf:partitionedtable}`Hive-konfigurationsvärden (t.ex. .

Avsnittet **typeProperties** är olika för varje transformeringsaktivitet. Om du vill veta mer om typegenskaper som stöds för en omvandlingsaktivitet klickar du på omvandlingsaktiviteten i tabellen [Dataomvandlingsaktiviteter.](#data-transformation-activities)

En fullständig genomgång av hur du skapar den här pipelinen finns i [Självstudiekurs: Skapa din första pipeline för att bearbeta data med Hadoop-klustret](data-factory-build-your-first-pipeline.md).

## <a name="multiple-activities-in-a-pipeline"></a>Flera aktiviteter i en pipeline
De två föregående exemplen innehåller bara en aktivitet. Du kan fler än en aktivitet i en pipeline.

Om du har flera aktiviteter i en pipeline och utdata från en aktivitet inte är en indata för en annan aktivitet, kan aktiviteterna köras parallellt om indatasegmenten för aktiviteterna är klara.

Du kan kedja två aktiviteter genom att ha utdatauppsättningen för en aktivitet som indatauppsättning för den andra aktiviteten. Den andra aktiviteten körs bara när den första slutförs.

![Kedja verksamhet i samma rörledning](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

I det här exemplet har pipelinen två aktiviteter: Activity1 och Activity2. Activity1 tar Dataset1 som indata och producerar en utdatadatauppsättning2. Aktiviteten tar Dataset2 som indata och producerar en utdatadatauppsättning3. Eftersom utdata för Activity1 (Dataset2) är indata för Activity2 körs Activity2 först när aktiviteten har slutförts och datauppsättning2-segmentet har skapats. Om aktiviteten1 misslyckas av någon anledning och inte producerar segmentet Dataset2 körs inte segmentet Aktivitet 2 för det segmentet (till exempel: 09:00 till 10:00).

Du kan också kedja aktiviteter som finns i olika pipelines.

![Kedja verksamhet i två rörledningar](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

I det här exemplet har Pipeline1 bara en aktivitet som tar Dataset1 som indata och producerar Dataset2 som utdata. Pipeline2 har också bara en aktivitet som tar Dataset2 som indata och Dataset3 som utdata.

Mer information finns i [schemaläggning och körning](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).
## <a name="create-and-monitor-pipelines"></a>Skapa och övervaka pipelines
Du kan skapa pipelines med hjälp av något av dessa verktyg eller SDK:er.

- Guiden Kopiera
- Visual Studio
- Azure PowerShell
- Azure Resource Manager-mall
- REST API
- .NET-API

Se följande självstudier för steg-för-steg-instruktioner för att skapa pipelines med hjälp av något av dessa verktyg eller SDK:er.

- [Skapa en pipeline med en datatransformeringsaktivitet](data-factory-build-your-first-pipeline.md)
- [Skapa en pipeline med en dataförflyttningsaktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

När en pipeline har skapats/distribuerats kan du hantera och övervaka dina pipelines med hjälp av Azure-portalbladen eller Övervaka och hantera app. Se följande avsnitt för steg-för-steg-instruktioner.

- [Övervaka och hantera pipelines med hjälp av Azure portalblad](data-factory-monitor-manage-pipelines.md).
- [Övervaka och hantera pipelines med hjälp av Övervaka och hantera app](data-factory-monitor-manage-app.md)

## <a name="onetime-pipeline"></a>Engångspipeline
Du kan skapa och schemalägga en pipeline så att den körs med jämna mellanrum (till exempel varje timme eller dagligen) inom de start- och sluttider som du anger i pipelinedefinitionen. Mer information finns i Schemaläggningsaktiviteter. Du kan också skapa en pipeline som bara körs en gång. För att göra det anger du egenskapen **pipelineMode** i pipelinedefinitionen till **engångsdata** som visas i följande JSON-exempel. Standardvärdet för den här egenskapen är **schemalagt**.

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
                ],
                "name": "CopyActivity-0"
            }
        ],
        "pipelineMode": "OneTime"
    }
}
```

Observera följande:

* **Start-** och **sluttider** för pipelinen har inte angetts.
* **Tillgänglighet för** in- och utdatauppsättningar anges **(frekvens** och **intervall),** även om Data Factory inte använder värdena.
* Diagramvyn visar inte engångspipeld. Det här beteendet är avsiktligt.
* Engångspipelderna kan inte uppdateras. Du kan klona en engångspipeline, byta namn på den, uppdatera egenskaper och distribuera den för att skapa en annan.

## <a name="next-steps"></a>Nästa steg
- Mer information om datauppsättningar finns i [Artikeln Skapa datauppsättningar.](data-factory-create-datasets.md)
- Mer information om hur pipelines schemaläggs och körs finns [i artikeln Schemaläggning och körning i Azure Data Factory.](data-factory-scheduling-and-execution.md)
