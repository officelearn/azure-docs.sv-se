---
title: Azure Data Factory - JSON-skript referens | Microsoft Docs
description: Innehåller JSON-scheman för Data Factory-entiteter.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 7b38bc8a2cdb740363dbf2c797738fc5277ff2bc
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54036449"
---
# <a name="azure-data-factory---json-scripting-reference"></a>Azure Data Factory - JSON-skript referens
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory.


Den här artikeln innehåller JSON-scheman och exempel för att definiera Azure Data Factory-enheter (pipeline, aktivitet, datauppsättningen och länkad tjänst).

## <a name="pipeline"></a>Pipeline
Den övergripande strukturen för en pipelinedefinition är följande:

```json
{
  "name": "SamplePipeline",
  "properties": {
    "description": "Describe what pipeline does",
    "activities": [
    ],
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
}
```

Följande tabell beskrivs egenskaperna i pipeline-JSON-definition:

| Egenskap  | Beskrivning | Krävs
-------- | ----------- | --------
| namn | Namnet på pipeline. Ange ett namn som representerar åtgärden aktivitet eller pipeline har konfigurerats för att göra<br/><ul><li>Maximalt antal tecken: 260</li><li>Måste börja med en bokstav siffra eller ett understreck (\_)</li><li>Följande tecken är inte tillåtna ”:”., ”+” ”,”?, ”/”, ”<” ”, >” ”, *”, ”%”, ”&” ”,:” ”,\\”</li></ul> |Ja |
| beskrivning |Text som beskriver vad aktiviteten eller pipeline används till | Nej |
| activities | Innehåller en lista över aktiviteter. | Ja |
| start |Starta datum / tid för pipelinen. Måste vara i [ISO-format](http://en.wikipedia.org/wiki/ISO_8601). Exempel: 2014-10-14T16:32:41. <br/><br/>Det är möjligt att ange en lokal tid, till exempel en EST tid. Här är ett exempel: `2016-02-27T06:00:00**-05:00`, vilket är 6 AM EST.<br/><br/>Egenskaper för start- och ange tillsammans aktiva perioden för pipelinen. Utdatasegment produceras bara med i den här aktiva period. |Nej<br/><br/>Om du anger ett värde för end-egenskapen, måste du ange värdet för egenskapen start.<br/><br/>Start- och sluttider kan vara tom för att skapa en pipeline. Du måste ange båda värdena för att ställa in en aktiva perioden för pipelinen att köra. Om du inte anger start- och sluttider när du skapar en pipeline kan du ange dem med hjälp av cmdleten Set-AzureRmDataFactoryPipelineActivePeriod senare. |
| slut |Slutdatum /-tid för pipelinen. Om anges måste vara i ISO-format. Exempel: 2014-10-14T17:32:41 <br/><br/>Det är möjligt att ange en lokal tid, till exempel en EST tid. Här är ett exempel: `2016-02-27T06:00:00**-05:00`, vilket är 6 AM EST.<br/><br/>Om du vill köra pipelinen på obestämd tid, ange 9999-09-09 som värde för end-egenskapen. |Nej <br/><br/>Om du anger ett värde för egenskapen start, måste du ange värdet för egenskapen slutet.<br/><br/>Se information om den **starta** egenskapen. |
| isPaused |Om värdet är true pipelinen inte körs. Standardvärde = false. Du kan använda den här egenskapen för att aktivera eller inaktivera. |Nej |
| pipelineMode |Metod för att schemalägga körningar för pipelinen. Tillåtna värden är: schemalagd (standard), genomfört.<br/><br/>”Schemalagd” anger att pipelinen körs vid ett angivet tidsintervall enligt den aktiva perioden (start- och -tid). ”Onetime” anger att pipelinen körs bara en gång. Onetime pipelines som är skapade får inte vara ändrade/uppdateras för tillfället. Se [Onetime pipeline](data-factory-create-pipelines.md#onetime-pipeline) mer information om hur du genomfört. |Nej |
| expirationTime |Tidsperiod när du har skapat som pipelinen är giltig och bör vara etablerade. Om den inte har någon aktiv, misslyckades, eller väntande körningar, pipelinen raderas automatiskt när den når den upphör att gälla. |Nej |


## <a name="activity"></a>Aktivitet
Den övergripande strukturen för en aktivitet i en pipeline-definition (aktiviteter element) är följande:

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

Följande tabell beskrivs egenskaperna i aktiviteten JSON-definition:

| Tagga | Beskrivning | Krävs |
| --- | --- | --- |
| namn |Namnet på aktiviteten. Ange ett namn som representerar åtgärden som aktiviteten har konfigurerats för att göra<br/><ul><li>Maximalt antal tecken: 260</li><li>Måste börja med en bokstav siffra eller ett understreck (\_)</li><li>Följande tecken är inte tillåtna ”:”., ”+” ”,”?, ”/”, ”<” ”, >” ”, *”, ”%”, ”&” ”,:” ”,\\”</li></ul> |Ja |
| beskrivning |Text som beskriver vad aktiviteten används till. |Nej |
| typ |Anger vilken typ av aktiviteten. Se den [DATALAGER](#data-stores) och [DATATRANSFORMERINGSAKTIVITETER](#data-transformation-activities) avsnitten om olika typer av aktiviteter. |Ja |
| inmatningar |Inkommande tabeller som används av aktiviteten<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Nej för HDInsightStreaming och SqlServerStoredProcedure aktiviteter <br/> <br/> Ja för alla andra |
| utdata |Utdata tabeller som används av aktiviteten.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": “outputtable1” } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” }  ],` |Ja |
| linkedServiceName |Namnet på den länkade tjänst som används av aktiviteten. <br/><br/>En aktivitet kan kräva att du anger den länkade tjänst som länkar till den nödvändiga beräkningsmiljön. |Ja för HDInsight aktiviteter, Azure Machine Learning-aktiviteter och lagrade Proceduraktiviteten. <br/><br/>Nej för alla andra |
| typeProperties |Egenskaperna i avsnittet typeProperties beror på typ av aktivitet. |Nej |
| policy |Principer som påverkar körningsbeteende för aktiviteten. Om det inte anges används standardprinciper. |Nej |
| Scheduler |”schemaegenskapen” används för att definiera önskade schemaläggning för aktiviteten. Dess subegenskaper är samma som de i den [tillgänglighet-egenskapen i en datauppsättning](data-factory-create-datasets.md#dataset-availability). |Nej |

### <a name="policies"></a>Principer
Principer påverkar körningsbeteende för en aktivitet, särskilt när sektorn i en tabell har bearbetats. I följande tabell innehåller information.

| Egenskap  | Tillåtna värden | Standardvärde | Beskrivning |
| --- | --- | --- | --- |
| samtidighet |Integer <br/><br/>Maxvärde: 10 |1 |Antal samtidiga körningar av aktiviteten.<br/><br/>Den avgör antalet körningar för parallell aktivitet som kan inträffa på olika segment. Till exempel om en aktivitet behöver genomgå påskyndar en stor mängd tillgängliga data, med ett större värde för samtidighet databearbetningen. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Anger sorteringen av datasektorer som bearbetas.<br/><br/>Till exempel om du har 2 skär (en händer klockan 4 och en ny 17: 00) och båda är väntar på att köras. Om du ställer in executionPriorityOrder vara NewestFirst bearbetas sektor 17: 00 först. På samma sätt om du ställer in executionPriorityORder vara OldestFIrst bearbetas sedan sektorn klockan 4. |
| retry |Integer<br/><br/>Max-värdet kan vara 10 |0 |Antal försök innan databearbetningen för sektorn har markerats som ett fel. Körningsmiljön för aktiviteten för en datasektor göras upp till det angivna antalet återförsök. Återförsök sker så snart som möjligt efter felet. |
| timeout |Tidsintervall |00:00:00 |Tidsgränsen för aktiviteten. Exempel: 00:10:00 (inbegriper timeout 10 minuter)<br/><br/>Om ett värde inte har angetts eller är 0, är tidsgränsen oändliga.<br/><br/>Om bearbetningstiden som data på en sektor överskrider timeout-värdet, den avbryts och försöker systemet att försök bearbetningen. Antal återförsök beror på egenskapen försök igen. När timeout uppstår är status nådde sin tidsgräns. |
| fördröjning |Tidsintervall |00:00:00 |Ange fördröjning före databearbetningen av sektorn startar.<br/><br/>Körningen av aktiviteten för en datasektor startas när fördröjningen har passerat den förväntade tiden för körningen.<br/><br/>Exempel: 00:10:00 (inbegriper fördröjning på 10 minuter) |
| longRetry |Integer<br/><br/>Maxvärde: 10 |1 |Antal långt återförsök innan sektorn körningen misslyckades.<br/><br/>longRetry försök är fördelade av longRetryInterval. Så om du vill ange en tid mellan nya försök använda longRetry. Om både återförsök och longRetry anges varje longRetry försök återförsök det maximala antalet försök används och försök igen * longRetry.<br/><br/>Till exempel, om vi har följande inställningar i aktivitetsprincipen:<br/>Gör om: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Anta att det finns endast en sektor att köra (status väntar) och körningsmiljön för aktiviteten misslyckas varje gång. Det skulle ursprungligen vara 3 körning av på varandra följande försök. Efter varje försök är statusen för sektorn försök igen. När det är första 3 försök över kan är statusen för sektorn LongRetry.<br/><br/>Efter en timme (det vill säga Longretryinteval's värde), skulle det finnas en annan uppsättning 3 körning av på varandra följande försök. Efter det skulle vara misslyckades sektorstatus och inga fler försök skulle göras. Därför har övergripande 6 försök gjorts.<br/><br/>Om alla körningen lyckades visas statusen för sektorn är klar och inga fler försök kommer att göras.<br/><br/>longRetry kan användas i situationer där beroende data kommer till icke-deterministisk gånger eller den övergripande miljön är flaky under vilken databearbetningen sker. I sådana fall kan göra återförsök efter varandra inte kan hjälpa och gör det när du har ett intervall på tid du leder till önskade utdata.<br/><br/>Liten varning: anger inte hög värden för longRetry eller longRetryInterval. Vanligtvis en högre värden andra systemfel problem. |
| longRetryInterval |Tidsintervall |00:00:00 |Fördröjningen mellan långt återförsök |

### <a name="typeproperties-section"></a>avsnittet typeProperties
Avsnittet typeProperties är olika för varje aktivitet. Transformeringsaktiviteter har bara egenskaperna för anslutningstypen. Se [DATATRANSFORMERINGSAKTIVITETER](#data-transformation-activities) i den här artikeln för JSON-exempel som definierar transformeringsaktiviteter i en pipeline.

**Kopiera aktivitet** har två underavsnitt i avsnittet typeProperties: **källa** och **mottagare**. Se [DATALAGER](#data-stores) avsnittet i den här artikeln för JSON-exempel som visar hur du använder en data-butiken som en källa och/eller mottagare.

### <a name="sample-copy-pipeline"></a>Exempel på kopieringspipeline
I följande exempel på pipeline finns det en aktivitet av typen **Copy** (Kopiera) i avsnittet **activities**. I det här exemplet på [Kopieringsaktivitet](data-factory-data-movement-activities.md) kopierar data från Azure Blob storage till en Azure SQL database.

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
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
}
```

Observera följande punkter:

* I avsnittet Aktiviteter finns det bara en aktivitet vars **typ** anges till **Kopia**.
* Indata för aktiviteten är inställd på **InputDataset** och utdata för aktiviteten är inställd på **OutputDataset**.
* I avsnittet för **typeProperties** har **BlobSource** angetts som källtyp och **SqlSink** har angetts som mottagartyp.

Se [DATALAGER](#data-stores) avsnittet i den här artikeln för JSON-exempel som visar hur du använder en data-butiken som en källa och/eller mottagare.

En fullständig genomgång för att skapa denna pipeline finns i [självstudien: Kopiera data från Blob Storage till SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

### <a name="sample-transformation-pipeline"></a>Exempel på transfomeringspipeline
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
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00",
        "isPaused": false
    }
}
```

Observera följande punkter:

* I activities-avsnittet finns det bara en aktivitet vars **typ** anges till **HDInsightHive**.
* Hive-skriptfilen **partitionweblogs.hql** lagras i Azure-lagringskontot (anges med scriptLinkedService, kallas **AzureStorageLinkedService**), och i mappen **skript** i containern **adfgetstarted**.
* Den **definierar** används för att ange körningsinställningar som skickas till hive-skriptet som Hive-konfigurationsvärden (t.ex `${hiveconf:inputtable}`, `${hiveconf:partitionedtable}`).

Se [DATATRANSFORMERINGSAKTIVITETER](#data-transformation-activities) i den här artikeln för JSON-exempel som definierar transformeringsaktiviteter i en pipeline.

En fullständig genomgång för att skapa denna pipeline finns i [självstudien: Skapa din första pipeline för att bearbeta data med Hadoop-kluster](data-factory-build-your-first-pipeline.md).

## <a name="linked-service"></a>Länkad tjänst
Den övergripande strukturen för en definition av länkad tjänst är följande:

```json
{
    "name": "<name of the linked service>",
    "properties": {
        "type": "<type of the linked service>",
        "typeProperties": {
        }
    }
}
```

Följande tabell beskrivs egenskaperna i aktiviteten JSON-definition:

| Egenskap  | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| namn | Namnet på den länkade tjänsten. | Ja |
| Egenskaper - typ | Typ av den länkade tjänsten. Exempel: Azure Storage, Azure SQL Database. |
| typeProperties | Avsnittet typeProperties har element som är olika för varje datalager eller beräkningsmiljön. Se [datalager](#datastores) avsnittet för alla data store-länkade tjänster och [compute miljöer](#compute-environments) länkade tjänster för alla instanser |

## <a name="dataset"></a>Datauppsättning
En datauppsättning i Azure Data Factory definieras enligt följande:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": <boolean flag to indicate external data. only for input datasets>,
        "linkedServiceName": "<Name of the linked service that refers to a data store.>",
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        },
        "availability": {
            "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
            "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
        },
        "policy":
        {
        }
    }
}
```

I följande tabell beskrivs egenskaperna i ovanstående JSON:

| Egenskap  | Beskrivning | Krävs | Standard |
| --- | --- | --- | --- |
| namn | Namnet på datauppsättningen. Se [Azure Data Factory – namnregler](data-factory-naming-rules.md) för regler för namngivning. |Ja |Ej tillämpligt |
| typ | Typ av datauppsättningen. Ange en av de typer som stöds av Azure Data Factory (till exempel: AzureBlob, AzureSqlTable). Se [DATALAGER](#data-stores) för alla datalager och datauppsättningstyper som stöds av Data Factory. |
| struktur | Schemat för datauppsättningen. Den innehåller kolumner, deras typer och så vidare. | Nej |Ej tillämpligt |
| typeProperties | Egenskaper för den valda typen. Se [DATALAGER](#data-stores) för typer som stöds och deras egenskaper. |Ja |Ej tillämpligt |
| extern | Boolesk flagga för att ange om en datauppsättning uttryckligen produceras av data factory-pipeline eller inte. |Nej |false |
| availability | Definierar fönstret bearbetning eller slicing modellen för produktion för datauppsättningen. Mer information om datauppsättningen uppdelning modellen finns [schemaläggning och körning](data-factory-scheduling-and-execution.md) artikeln. |Ja |Ej tillämpligt |
| policy |Definierar kriterierna eller villkor som datauppsättning segment måste vara uppfyllda. <br/><br/>Mer information finns i [datauppsättning princip](#Policy) avsnittet. |Nej |Ej tillämpligt |

Varje kolumn i den **struktur** avsnittet innehåller följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| namn |Namnet på kolumnen. |Ja |
| typ |Datatypen för kolumnen.  |Nej |
| kultur |.NET-baserade språkmiljö som ska användas när typ har angetts och .NET-typ `Datetime` eller `Datetimeoffset`. Standardvärdet är `en-us`. |Nej |
| Format |Formatera strängen som ska användas när typ har angetts och .NET-typ `Datetime` eller `Datetimeoffset`. |Nej |

I följande exempel har tre kolumner i datauppsättningen `slicetimestamp`, `projectname`, och `pageviews` och de är av typen: String, String och Decimal respektive.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

I följande tabell beskrivs egenskaperna som du kan använda i den **tillgänglighet** avsnittet:

| Egenskap  | Beskrivning | Krävs | Standard |
| --- | --- | --- | --- |
| frequency |Anger tidsenheten för datauppsättningen sektorn produktion.<br/><br/><b>Stöds frekvens</b>: Minut, timme, dag, vecka, månad |Ja |Ej tillämpligt |
| interval |Anger en multiplikator för frekvensen<br/><br/>”X frekvensintervall” avgör hur ofta sektorn skapas.<br/><br/>Om du behöver datauppsättningen att delas timme kan du ställa in <b>frekvens</b> till <b>timme</b>, och <b>intervall</b> till <b>1</b>.<br/><br/><b>Obs!</b> Om du anger frekvens som minut, rekommenderar vi att du ställer in intervall på mindre än 15 |Ja |Ej tillämpligt |
| stil |Anger om sektorn ska produceras vid start/slut intervallet.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Om frekvensen är inställd på månad och format har angetts till EndOfInterval, producerade sektorn på den sista dagen i månaden. Om formatet har angetts till StartOfInterval producerade sektorn på den första dagen i månaden.<br/><br/>Om frekvensen är inställd på dagen och format har angetts till EndOfInterval, producerade sektorn under den senaste timmen på dagen.<br/><br/>Om frekvensen är inställd på timme och format har angetts till EndOfInterval, producerade sektorn i slutet av timmen. För en sektor under PM 1 – 2 PM, till exempel produceras sektorn klockan 2. |Nej |EndOfInterval |
| anchorDateTime |Definierar absolut position i tid som används av scheduler för att beräkna datauppsättning sektorn gränser. <br/><br/><b>Obs!</b> Om AnchorDateTime har datumdelar som är större än frekvensen ignoreras de mer detaljerade delarna. <br/><br/>Till exempel om den <b>intervall</b> är <b>per timme</b> (frequency: hour och interval: (1) och <b>AnchorDateTime</b> innehåller <b>minuter och sekunder</b> kommer <b>minuter och sekunder</b> delar av AnchorDateTime ignoreras. |Nej |01/01/0001 |
| förskjutning |TimeSpan som början och slutet av alla datauppsättningen sektorer beräkningsarbete. <br/><br/><b>Obs!</b> Om både anchorDateTime och förskjutning anges, skapas det kombinerade skiftet. |Nej |Ej tillämpligt |

Tillgänglighet nedan anger att utdatauppsättningen är antingen producerade varje timme (eller) indata datauppsättningen är tillgängliga per timme:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

Den **princip** avsnittet i definitionen av datauppsättningen definierar kriterierna eller villkor som datauppsättning segment måste vara uppfyllda.

| Principnamn | Beskrivning | Tillämpas på | Krävs | Standard |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Kontrollerar att data i en **Azure blob** uppfyller minsta storlek (i megabyte). |Azure-blobb |Nej |Ej tillämpligt |
| minimumRows |Kontrollerar att data i en **Azure SQL-databas** eller en **Azure-tabell** innehåller det minsta antalet rader. |<ul><li>Azure SQL Database</li><li>Azure-tabell</li></ul> |Nej |Ej tillämpligt |

**Exempel:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

Om inte en datauppsättningen produceras av Azure Data Factory, bör det markeras som **externa**. Den här inställningen gäller vanligtvis indata för den första aktiviteten i en pipeline, såvida inte aktivitet eller länkning av pipelinen som används.

| Namn | Beskrivning | Krävs | Standardvärde |
| --- | --- | --- | --- |
| dataDelay |Tid att fördröja kontrollera tillgängligheten för externa data för givna sektorn. Om data är tillgängliga per timme, kan kontrollen externa data är tillgängliga och motsvarande sektorn är klar fördröjas med hjälp av dataDelay.<br/><br/>Gäller endast för den aktuella tiden.  Om det är 1:00 PM just nu och det här värdet är 10 minuter, till exempel startar verifieringen klockan 13:10.<br/><br/>Den här inställningen påverkar inte sektorer tidigare (segment med sluttid för sektor + dataDelay < nu) bearbetas utan fördröjning.<br/><br/>Tid som är större än 23:59 timmar måste anges med hjälp av den `day.hours:minutes:seconds` format. Till exempel vill ange 24 timmar, inte använda 24:00:00; Använd i stället 1.00:00:00. Om du använder 24:00:00, behandlas den som 24 dagar (24.00:00:00). För 1 dag och fyra timmar, anger du 1:04:00:00. |Nej |0 |
| retryInterval |Väntetiden mellan ett fel och nästa återförsök. Om ett försök misslyckas, är nästa försök efter retryInterval. <br/><br/>Om den är 1:00 PM just nu kan börja vi första försöket. Om tid att slutföra första valideringskontrollen är 1 minut och åtgärden misslyckades, nästa återförsök var 1:00 + 1 minut (varaktighet) + 1 min (återförsöksintervallet) = 1:02 PM. <br/><br/>Det finns ingen fördröjning för segment i förflutna. Återförsök sker omedelbart. |Nej |00:01:00 (1 minut) |
| retryTimeout |Tidsgränsen för varje nytt försök.<br/><br/>Om den här egenskapen anges till 10 minuter, måste valideringen slutföras inom 10 minuter. Om det tar längre tid än 10 minuter att utföra valideringen tidsgränsen återförsök.<br/><br/>Om alla försök för verifieringen tidsgränsen har sektorn markerats som nådde sin tidsgräns. |Nej |00:10:00 (10 minuter) |
| maximumRetry |Antal gånger för att kontrollera tillgänglighet för externa data. Det högsta tillåtna värdet är 10. |Nej |3 |


## <a name="data-stores"></a>DATALAGER
Den [länkad tjänst](#linked-service) avsnitt som finns beskrivningar av JSON-element som är gemensamma för alla typer av länkade tjänster. Det här avsnittet innehåller information om JSON-element som är specifika för varje datalager.

Den [datauppsättning](#dataset) avsnitt som finns beskrivningar av JSON-element som är gemensamma för alla typer av datauppsättningar. Det här avsnittet innehåller information om JSON-element som är specifika för varje datalager.

Den [aktivitet](#activity) avsnitt som finns beskrivningar av JSON-element som är gemensamma för alla typer av aktiviteter. Det här avsnittet innehåller information om JSON-element som är specifika för varje datalager när det används som källa/mottagare i en Kopieringsaktivitet.

Klicka på länken för store som du är intresserad av att se JSON-scheman för länkad tjänst, datauppsättningen och källa/mottagare för kopieringsaktiviteten.

| Kategori | Datalager
|:--- |:--- |
| **Azure** |[Azure Blob Storage](#azure-blob-storage) |
| &nbsp; |[Azure Data Lake Store](#azure-datalake-store) |
| &nbsp; |[Azure Cosmos DB](#azure-cosmos-db) |
| &nbsp; |[Azure SQL Database](#azure-sql-database) |
| &nbsp; |[Azure SQL Data Warehouse](#azure-sql-data-warehouse) |
| &nbsp; |[Azure Search](#azure-search) |
| &nbsp; |[Azure Table Storage](#azure-table-storage) |
| **Databaser** |[Amazon Redshift](#amazon-redshift) |
| &nbsp; |[IBM DB2](#ibm-db2) |
| &nbsp; |[MySQL](#mysql) |
| &nbsp; |[Oracle](#oracle) |
| &nbsp; |[PostgreSQL](#postgresql) |
| &nbsp; |[SAP Business Warehouse](#sap-business-warehouse) |
| &nbsp; |[SAP HANA](#sap-hana) |
| &nbsp; |[SQL Server](#sql-server) |
| &nbsp; |[Sybase](#sybase) |
| &nbsp; |[Teradata](#teradata) |
| **NoSQL** |[Cassandra](#cassandra) |
| &nbsp; |[MongoDB](#mongodb) |
| **Fil** |[Amazon S3](#amazon-s3) |
| &nbsp; |[Filsystem](#file-system) |
| &nbsp; |[FTP](#ftp) |
| &nbsp; |[HDFS](#hdfs) |
| &nbsp; |[SFTP](#sftp) |
| **Andra** |[HTTP](#http) |
| &nbsp; |[OData](#odata) |
| &nbsp; |[ODBC](#odbc) |
| &nbsp; |[Salesforce](#salesforce) |
| &nbsp; |[Webbtabell](#web-table) |

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="linked-service"></a>Länkad tjänst
Det finns två typer av länkade tjänster: Länkad Azure Storage-tjänst och Azure Storage SAS länkad tjänst.

#### <a name="azure-storage-linked-service"></a>Länkad Azure Storage-tjänst
Länka ditt Azure storage-konto till en datafabrik med hjälp av den **kontonyckel**, skapa en länkad Azure Storage-tjänst. Definiera ett Azure Storage länkade tjänsten genom att ange den **typ** på den länkade tjänsten till **AzureStorage**. Sedan kan du ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| connectionString |Ange information som behövs för att ansluta till Azure storage för connectionString-egenskapen. |Ja |

##### <a name="example"></a>Exempel

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

#### <a name="azure-storage-sas-linked-service"></a>Länkad Azure Storage SAS-tjänst
Med Azure Storage SAS länkad service kan du länka ett Azure Storage-konto till en Azure-datafabrik med hjälp av en signatur för delad åtkomst (SAS). Data factory skapar med begränsade/Tidsbundna åtkomst till alla utvalda resurser (blobbehållare) i lagringen. Länkad tjänst för att länka ditt Azure storage-konto till en datafabrik med hjälp av signatur för delad åtkomst, skapa ett Azure Storage-SAS. För att definiera en Azure Storage-SAS länkad tjänst genom att ange den **typ** på den länkade tjänsten till **AzureStorageSas**. Sedan kan du ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| sasUri |Ange URI för delad åtkomst-signatur i Azure Storage-resurser, till exempel blob, behållaren eller tabellen. |Ja |

##### <a name="example"></a>Exempel

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<storageUri>?<sasToken>"
        }
    }
}
```

Läs mer om dessa länkade tjänster, [Azure Blob Storage connector](data-factory-azure-blob-connector.md#linked-service-properties) artikeln.

### <a name="dataset"></a>Datauppsättning
För att definiera en Azure Blob-datauppsättning, ange den **typ** på datauppsättningen till **AzureBlob**. Sedan anger du följande specifika egenskaper med Azure Blob i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Sökvägen till behållaren och mappen i blob storage. Exempel: myblobcontainer\myblobfolder\ |Ja |
| fileName |Namnet på bloben. Filnamnet är valfria och skiftlägeskänsliga.<br/><br/>Om du anger ett filename fungerar aktiviteten (inklusive kopia) på den specifika blobben.<br/><br/>Om filnamnet inte anges innehåller kopiera alla Blobbar i folderPath för datauppsättningen för indata.<br/><br/>När filnamn har angetts för en utdatauppsättning, namnet på den genererade filen vara i följande det här formatet: Data. <Guid>.txt (exempel:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nej |
| partitionedBy |partitionedBy är en valfri egenskap. Du kan använda den för att ange en dynamisk folderPath och ett filnamn för time series-data. Till exempel kan folderPath parameteriseras för varje timme som data. |Nej |
| Format | Följande formattyper av stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange den **typ** egenskapen under format till ett av dessa värden. Mer information finns i [textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-formatet](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc-Format](data-factory-supported-file-and-compression-formats.md#orc-format), och [Parquet-Format](data-factory-supported-file-and-compression-formats.md#parquet-format) avsnitt. <br><br> Om du vill **kopiera filer som – är** hoppa över avsnittet format i både inkommande och utgående datamängd definitioner mellan filbaserade (binär kopia). |Nej |
| Komprimering | Ange typ och komprimeringsnivå för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**. Nivåer som stöds är: **Optimal** och **snabbaste**. Mer information finns i [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nej |

#### <a name="example"></a>Exempel

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
 ```


Mer information finns i [Azure Blob-anslutningsapp](data-factory-azure-blob-connector.md#dataset-properties) artikeln.

### <a name="blobsource-in-copy-activity"></a>BlobSource i Kopieringsaktiviteten
Om du kopierar data från Azure Blob Storage, ange den **källtyp** av kopieringsaktiviteten till **BlobSource**, och ange följande egenskaper i den **källa** avsnittet:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| rekursiv |Anger om data läses rekursivt från undermappar eller endast från den angivna mappen. |SANT (standard), FALSKT |Nej |

#### <a name="example-blobsource"></a>Exempel: **BlobSource**
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
### <a name="blobsink-in-copy-activity"></a>BlobSink i Kopieringsaktiviteten
Om du kopierar data till Azure Blob Storage, ange den **mottagare typ** av kopieringsaktiviteten till **BlobSink**, och ange följande egenskaper i den **mottagare** avsnittet:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| copyBehavior |Definierar kopieringsbeteendet när källan är BlobSource eller filsystem. |<b>PreserveHierarchy</b>: bevarar filen hierarkin i målmappen. Den relativa sökvägen för källfilen för källmappen är identisk med den relativa sökvägen för målfilen till målmappen.<br/><br/><b>FlattenHierarchy</b>: alla filer från källmappen finns i den första nivån i målmappen. Målfiler har automatiskt genererad namn. <br/><br/><b>MergeFiles (standard):</b> slår samman alla filer från källmappen till en fil. Om namnet på filen/blobben har angetts, blir namnet på sammanfogade filen det angivna namnet; annars skulle vara automatiskt genererade filnamn. |Nej |

#### <a name="example-blobsink"></a>Exempel: BlobSink

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Mer information finns i [Azure Blob-anslutningsapp](data-factory-azure-blob-connector.md#copy-activity-properties) artikeln.

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="linked-service"></a>Länkad tjänst
För att definiera en Azure Data Lake Store länkad tjänst, ange typ av den länkade tjänsten till **AzureDataLakeStore**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till: **AzureDataLakeStore** | Ja |
| dataLakeStoreUri | Ange information om Azure Data Lake Store-konto. Det är i följande format: `https://[accountname].azuredatalakestore.net/webhdfs/v1` eller `adl://[accountname].azuredatalakestore.net/`. | Ja |
| subscriptionId | Azure-prenumerations-Id som Data Lake Store tillhör. | Krävs för mottagare |
| resourceGroupName | Azure resursgruppens namn som Data Lake Store tillhör. | Krävs för mottagare |
| servicePrincipalId | Ange programmets klient-ID. | Ja (för autentisering av tjänstens huvudnamn) |
| servicePrincipalKey | Ange programmets nyckel. | Ja (för autentisering av tjänstens huvudnamn) |
| klient | Ange klientinformation (domain name eller klient-ID) under där programmet finns. Du kan hämta den håller musen i det övre högra hörnet i Azure Portal. | Ja (för autentisering av tjänstens huvudnamn) |
| Auktorisering | Klicka på **auktorisera** knappen i den **Data Factory-redigeraren** och ange dina autentiseringsuppgifter som tilldelar automatiskt genererade URL: Auktoriseringswebbadressen till den här egenskapen. | Ja (för autentisering med autentiseringsuppgifter)|
| sessions-ID | OAuth sessions-id från OAuth-auktorisering sessionen. Varje sessions-id är unikt och får endast användas en gång. Den här inställningen genereras automatiskt när du använder Data Factory-redigeraren. | Ja (för autentisering med autentiseringsuppgifter) |

#### <a name="example-using-service-principal-authentication"></a>Exempel: med hjälp av autentisering av tjänstens huvudnamn
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info. Example: microsoft.onmicrosoft.com>"
        }
    }
}
```

#### <a name="example-using-user-credential-authentication"></a>Exempel: med hjälp av användarautentisering för autentiseringsuppgifter
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

Mer information finns i [Azure Data Lake Store connector](data-factory-azure-datalake-connector.md#linked-service-properties) artikeln.

### <a name="dataset"></a>Datauppsättning
För att definiera en Azure Data Lake Store-datauppsättning, ange den **typ** på datauppsättningen till **AzureDataLakeStore**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| folderPath |Sökvägen till behållaren och mappen i Azure Data Lake store. |Ja |
| fileName |Namnet på filen i Azure Data Lake store. Filnamnet är valfria och skiftlägeskänsliga. <br/><br/>Om du anger ett filename fungerar aktiviteten (inklusive kopia) på den specifika filen.<br/><br/>Om filnamnet inte anges innehåller kopiera alla filer i folderPath för datauppsättningen för indata.<br/><br/>När filnamn har angetts för en utdatauppsättning, namnet på den genererade filen vara i följande det här formatet: Data. <Guid>.txt (exempel:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nej |
| partitionedBy |partitionedBy är en valfri egenskap. Du kan använda den för att ange en dynamisk folderPath och ett filnamn för time series-data. Till exempel kan folderPath parameteriseras för varje timme som data. |Nej |
| Format | Följande formattyper av stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange den **typ** egenskapen under format till ett av dessa värden. Mer information finns i [textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-formatet](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc-Format](data-factory-supported-file-and-compression-formats.md#orc-format), och [Parquet-Format](data-factory-supported-file-and-compression-formats.md#parquet-format) avsnitt. <br><br> Om du vill **kopiera filer som – är** hoppa över avsnittet format i både inkommande och utgående datamängd definitioner mellan filbaserade (binär kopia). |Nej |
| Komprimering | Ange typ och komprimeringsnivå för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**. Nivåer som stöds är: **Optimal** och **snabbaste**. Mer information finns i [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nej |

#### <a name="example"></a>Exempel
```json
{
    "name": "AzureDataLakeStoreInput",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Mer information finns i [Azure Data Lake Store connector](data-factory-azure-datalake-connector.md#dataset-properties) artikeln.

### <a name="azure-data-lake-store-source-in-copy-activity"></a>Azure Data Lake Store-källan i Kopieringsaktiviteten
Om du kopierar data från en Azure Data Lake Store, ange den **källtyp** av kopieringsaktiviteten till **AzureDataLakeStoreSource**, och ange följande egenskaper i den **källa**avsnitt:

**AzureDataLakeStoreSource** har stöd för följande egenskaper **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| rekursiv |Anger om data läses rekursivt från undermappar eller endast från den angivna mappen. |SANT (standard), FALSKT |Nej |

#### <a name="example-azuredatalakestoresource"></a>Exempel: AzureDataLakeStoreSource

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureDakeLaketoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureDataLakeStoreInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureDataLakeStoreSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Mer information finns i [Azure Data Lake Store connector](data-factory-azure-datalake-connector.md#copy-activity-properties) artikeln.

### <a name="azure-data-lake-store-sink-in-copy-activity"></a>Azure Data Lake Store-mottagare i Kopieringsaktiviteten
Om du kopierar data till en Azure Data Lake Store, anger du den **mottagare typ** av kopieringsaktiviteten till **AzureDataLakeStoreSink**, och ange följande egenskaper i den **mottagare** avsnitt:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| copyBehavior |Anger kopieringsbeteendet. |<b>PreserveHierarchy</b>: bevarar filen hierarkin i målmappen. Den relativa sökvägen för källfilen för källmappen är identisk med den relativa sökvägen för målfilen till målmappen.<br/><br/><b>FlattenHierarchy</b>: alla filer från källmappen skapas i den första nivån i målmappen. Målfiler som skapas med automatiskt genererad namn.<br/><br/><b>MergeFiles</b>: slår samman alla filer från källmappen till en fil. Om namnet på filen/blobben har angetts, blir namnet på sammanfogade filen det angivna namnet; annars skulle vara automatiskt genererade filnamn. |Nej |

#### <a name="example-azuredatalakestoresink"></a>Exempel: AzureDataLakeStoreSink
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoDataLake",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureDataLakeStoreOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureDataLakeStoreSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Mer information finns i [Azure Data Lake Store connector](data-factory-azure-datalake-connector.md#copy-activity-properties) artikeln.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="linked-service"></a>Länkad tjänst
För att definiera en Azure Cosmos DB länkad tjänst genom att ange den **typ** på den länkade tjänsten till **DocumentDb**, och ange följande egenskaper i den **typeProperties** avsnittet:

| **Egenskap** | **Beskrivning** | **Krävs** |
| --- | --- | --- |
| connectionString |Ange information som behövs för att ansluta till Azure Cosmos DB-databas. |Ja |

#### <a name="example"></a>Exempel

```json
{
    "name": "CosmosDBLinkedService",
    "properties": {
        "type": "DocumentDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
    }
}
```
Mer information finns i [Azure Cosmos DB-anslutningsapp](data-factory-azure-documentdb-connector.md#linked-service-properties) artikeln.

### <a name="dataset"></a>Datauppsättning
För att definiera en Azure Cosmos DB-datauppsättning, ange den **typ** på datauppsättningen till **DocumentDbCollection**, och ange följande egenskaper i den **typeProperties** avsnittet:

| **Egenskap** | **Beskrivning** | **Krävs** |
| --- | --- | --- |
| Samlingsnamn |Namnet på Azure Cosmos DB-samling. |Ja |

#### <a name="example"></a>Exempel

```json
{
    "name": "PersonCosmosDBTable",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "CosmosDBLinkedService",
        "typeProperties": {
            "collectionName": "Person"
        },
        "external": true,
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```
Mer information finns i [Azure Cosmos DB-anslutningsapp](data-factory-azure-documentdb-connector.md#dataset-properties) artikeln.

### <a name="azure-cosmos-db-collection-source-in-copy-activity"></a>Källa för Azure Cosmos DB-samling i Kopieringsaktiviteten
Om du kopierar data från ett Azure Cosmos DB, ange den **källtyp** av kopieringsaktiviteten till **DocumentDbCollectionSource**, och ange följande egenskaper i den **källa**avsnitt:


| **Egenskap** | **Beskrivning** | **Tillåtna värden** | **Krävs** |
| --- | --- | --- | --- |
| DocumentDB |Ange fråga för att läsa data. |Frågesträng som stöds av Azure Cosmos DB. <br/><br/>Exempel: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nej <br/><br/>Om inte anges, SQL-instruktionen som körs: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Specialtecken som visar att dokumentet är kapslade |Valfritt tecken. <br/><br/>Azure Cosmos DB är en NoSQL-lagring av JSON-dokument, där kapslade strukturer är tillåtna. Azure Data Factory kan användaren att ange hierarki via nestingSeparator, vilket är ””. i ovanstående exempel. Med avgränsaren, Kopieringsaktivitet genererar ”Name”-objekt med tre underordnade element först mellan- och efternamn enligt ”Name.First”, ”Name.Middle” och ”Name.Last” i tabelldefinitionen. |Nej |

#### <a name="example"></a>Exempel

```json
{
    "name": "DocDbToBlobPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "DocumentDbCollectionSource",
                    "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
                    "nestingSeparator": "."
                },
                "sink": {
                    "type": "BlobSink",
                    "blobWriterAddHeader": true,
                    "writeBatchSize": 1000,
                    "writeBatchTimeout": "00:00:59"
                }
            },
            "inputs": [{
                "name": "PersonCosmosDBTable"
            }],
            "outputs": [{
                "name": "PersonBlobTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromCosmosDbToBlob"
        }],
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00"
    }
}
```

### <a name="azure-cosmos-db-collection-sink-in-copy-activity"></a>Azure Cosmos DB-samling mottagare i Kopieringsaktiviteten
Om du kopierar data till Azure Cosmos DB, ange den **mottagare typ** av kopieringsaktiviteten till **DocumentDbCollectionSink**, och ange följande egenskaper i den **mottagare** avsnittet :

| **Egenskap** | **Beskrivning** | **Tillåtna värden** | **Krävs** |
| --- | --- | --- | --- |
| nestingSeparator |Specialtecken i källkolumnsnamnet som visar det kapslade dokumentet krävs. <br/><br/>Till exempel ovan: `Name.First` i utdata tabell ger följande JSON-strukturen i Cosmos DB-dokument:<br/><br/>”Name”: {<br/>    ”Första”: ”John”<br/>}, |Tecken som används för att avgränsa kapslingsnivåer.<br/><br/>Standardvärdet är `.` (punkt). |Tecken som används för att avgränsa kapslingsnivåer. <br/><br/>Standardvärdet är `.` (punkt). |
| WriteBatchSize |Antalet parallella begäranden till Azure Cosmos DB-tjänsten för att skapa dokument.<br/><br/>Du kan finjustera prestanda när du kopierar data till och från Azure Cosmos DB med hjälp av den här egenskapen. Du kan förvänta dig en bättre prestanda om du ökar writeBatchSize eftersom flera parallella förfrågningar till Azure Cosmos DB skickas. Men genererar du måste undvika begränsning som ett felmeddelande: ”Förfrågan är stor”.<br/><br/>Begränsning avgörs av ett antal faktorer, bland annat dokument, antalet villkoren i dokument storlek, indexeringspolicy målsamlingen osv. För kopieringsåtgärder, du kan använda en bättre samling (t.ex, S3) ha de dataflöden som är tillgängliga (2 500 begäran begärandeenheter/sekund). |Integer |Nej (standard: 5) |
| writeBatchTimeout |Vänta tills åtgärden har slutförts innan tidsgränsen uppnås. |Tidsintervall<br/><br/> Exempel: ”00: 30:00” (30 minuter). |Nej |

#### <a name="example"></a>Exempel

```json
{
    "name": "BlobToDocDbPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "DocumentDbCollectionSink",
                    "nestingSeparator": ".",
                    "writeBatchSize": 2,
                    "writeBatchTimeout": "00:00:00"
                },
                "translator": {
                    "type": "TabularTranslator",
                    "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix"
                }
            },
            "inputs": [{
                "name": "PersonBlobTableIn"
            }],
            "outputs": [{
                "name": "PersonCosmosDbTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromBlobToCosmosDb"
        }],
        "start": "2016-04-14T00:00:00",
        "end": "2016-04-15T00:00:00"
    }
}
```

Mer information finns i [Azure Cosmos DB-anslutningsapp](data-factory-azure-documentdb-connector.md#copy-activity-properties) artikeln.

## <a name="azure-sql-database"></a>Azure SQL Database

### <a name="linked-service"></a>Länkad tjänst
För att definiera en Azure SQL Database länkad tjänst genom att ange den **typ** på den länkade tjänsten till **AzureSqlDatabase**, och ange följande egenskaper i den **typeProperties** avsnitt:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| connectionString |Ange information som behövs för att ansluta till Azure SQL Database-instans för connectionString-egenskapen. |Ja |

#### <a name="example"></a>Exempel
```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Mer information finns i [Azure SQL-anslutningen](data-factory-azure-sql-connector.md#linked-service-properties) artikeln.

### <a name="dataset"></a>Datauppsättning
För att definiera en Azure SQL Database-datauppsättning, ange den **typ** på datauppsättningen till **AzureSqlTable**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen eller vyn i Azure SQL Database-instans som den länkade tjänsten refererar till. |Ja |

#### <a name="example"></a>Exempel

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Mer information finns i [Azure SQL-anslutningen](data-factory-azure-sql-connector.md#dataset-properties) artikeln.

### <a name="sql-source-in-copy-activity"></a>SQL-källans i Kopieringsaktiviteten
Om du kopierar data från en Azure SQL Database, ange den **källtyp** av kopieringsaktiviteten till **SqlSource**, och ange följande egenskaper i den **källa** avsnittet:


| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| sqlReaderQuery |Använd anpassad fråga för att läsa data. |SQL-sträng. Exempel: `select * from MyTable`. |Nej |
| sqlReaderStoredProcedureName |Namnet på den lagrade proceduren som läser data från källtabellen. |Namnet på den lagrade proceduren. |Nej |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värde-par. Namn och versaler och gemener i parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. |Nej |

#### <a name="example"></a>Exempel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Mer information finns i [Azure SQL-anslutningen](data-factory-azure-sql-connector.md#copy-activity-properties) artikeln.

### <a name="sql-sink-in-copy-activity"></a>SQL-mottagare i Kopieringsaktiviteten
Om du kopierar data till Azure SQL Database, ange den **mottagare typ** av kopieringsaktiviteten till **SqlSink**, och ange följande egenskaper i den **mottagare** avsnittet:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| writeBatchTimeout |Väntetid för batch insert-åtgärden ska slutföras innan tidsgränsen uppnås. |Tidsintervall<br/><br/> Exempel: ”00: 30:00” (30 minuter). |Nej |
| WriteBatchSize |Infogar data i SQL-tabell när buffertstorleken når writeBatchSize. |Heltal (antal rader) |Nej (standard: 10000) |
| sqlWriterCleanupScript |Ange en fråga för Kopieringsaktiviteten till att köra så att data för en viss sektor rensas. |Ett frågeuttryck. |Nej |
| sliceIdentifierColumnName |Ange ett kolumnnamn för Kopieringsaktiviteten vill fylla med automatiskt genererad sektorn identifierare som används för att rensa data för en viss sektor när köras på nytt. |Kolumnnamnet på en kolumn med datatypen för binary(32). |Nej |
| sqlWriterStoredProcedureName |Namnet på den lagrade proceduren som upsertar (uppdateringar/infogningar) data i måltabellen. |Namnet på den lagrade proceduren. |Nej |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värde-par. Namn och versaler och gemener i parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. |Nej |
| sqlWriterTableType |Ange ett tabellnamn typ som ska användas i den lagrade proceduren. Kopieringsaktivitet tillhandahåller data som flyttas i en temporär tabell med den här tabellen. Lagrad procedur kod kan sedan sammanfoga data kopieras med befintliga data. |Ett namn för tabellen. |Nej |

#### <a name="example"></a>Exempel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Mer information finns i [Azure SQL-anslutningen](data-factory-azure-sql-connector.md#copy-activity-properties) artikeln.

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

### <a name="linked-service"></a>Länkad tjänst
För att definiera en Azure SQL Data Warehouse länkad tjänst genom att ange den **typ** på den länkade tjänsten till **AzureSqlDW**, och ange följande egenskaper i den **typeProperties** avsnitt:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| connectionString |Ange information som behövs för att ansluta till Azure SQL Data Warehouse-instans för connectionString-egenskapen. |Ja |



#### <a name="example"></a>Exempel

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Mer information finns i [Azure SQL Data Warehouse-anslutningsappen](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties) artikeln.

### <a name="dataset"></a>Datauppsättning
För att definiera en Azure SQL Data Warehouse-datauppsättning, ange den **typ** på datauppsättningen till **AzureSqlDWTable**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen eller vyn i Azure SQL Data Warehouse-databas som den länkade tjänsten refererar till. |Ja |

#### <a name="example"></a>Exempel

```json
{
    "name": "AzureSqlDWInput",
    "properties": {
    "type": "AzureSqlDWTable",
        "linkedServiceName": "AzureSqlDWLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Mer information finns i [Azure SQL Data Warehouse-anslutningsappen](data-factory-azure-sql-data-warehouse-connector.md#dataset-properties) artikeln.

### <a name="sql-dw-source-in-copy-activity"></a>SQL DW-källan i Kopieringsaktiviteten
Om du kopierar data från Azure SQL Data Warehouse, anger du den **källtyp** av kopieringsaktiviteten till **SqlDWSource**, och ange följande egenskaper i den **källa** avsnitt:


| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| sqlReaderQuery |Använd anpassad fråga för att läsa data. |SQL-sträng. Till exempel: `select * from MyTable`. |Nej |
| sqlReaderStoredProcedureName |Namnet på den lagrade proceduren som läser data från källtabellen. |Namnet på den lagrade proceduren. |Nej |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värde-par. Namn och versaler och gemener i parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. |Nej |

#### <a name="example"></a>Exempel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLDWtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSqlDWInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlDWSource",
                    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Mer information finns i [Azure SQL Data Warehouse-anslutningsappen](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) artikeln.

### <a name="sql-dw-sink-in-copy-activity"></a>SQL DW mottagare i Kopieringsaktiviteten
Om du kopierar data till Azure SQL Data Warehouse, anger du den **mottagare typ** av kopieringsaktiviteten till **SqlDWSink**, och ange följande egenskaper i den **mottagare** avsnittet:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Ange en fråga för Kopieringsaktiviteten till att köra så att data för en viss sektor rensas. |Ett frågeuttryck. |Nej |
| allowPolyBase |Anger om du vill använda PolyBase (om tillämpligt) i stället för BULKINSERT mekanism. <br/><br/> **Med PolyBase är det rekommenderade sättet att läsa in data i SQL Data Warehouse.** |True <br/>FALSKT (standard) |Nej |
| polyBaseSettings |En grupp egenskaper som kan anges när den **allowPolybase** är inställd på **SANT**. |&nbsp; |Nej |
| rejectValue |Anger det tal eller procentandelen rader som kan avvisas innan frågan inte kunde köras. <br/><br/>Mer information om den PolyBase avvisningsalternativen i den **argument** delen av [Skapa extern tabell (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) avsnittet. |0 (standardvärde), 1, 2... |Nej |
| rejectType |Anger om alternativet rejectValue anges som ett exakt värde eller en procentandel. |Värde (standard), procent |Nej |
| rejectSampleValue |Anger antalet rader som ska hämtas innan PolyBase beräknar om procentandelen avvisade raden. |1, 2, … |Ja, om **rejectType** är **procent** |
| useTypeDefault |Anger hur du hanterar värden som saknas i avgränsade textfiler när PolyBase hämtar data från textfilen.<br/><br/>Mer information om den här egenskapen från avsnittet argument i [skapa externt FILFORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |SANT, FALSKT (standard) |Nej |
| WriteBatchSize |Infogar data i SQL-tabell när buffertstorleken når writeBatchSize |Heltal (antal rader) |Nej (standard: 10000) |
| writeBatchTimeout |Väntetid för batch insert-åtgärden ska slutföras innan tidsgränsen uppnås. |Tidsintervall<br/><br/> Exempel: ”00: 30:00” (30 minuter). |Nej |

#### <a name="example"></a>Exempel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQLDW",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlDWOutput"
            }],
            "typeProperties": {
                "source": {
                "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlDWSink",
                    "allowPolyBase": true
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Mer information finns i [Azure SQL Data Warehouse-anslutningsappen](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) artikeln.

## <a name="azure-search"></a>Azure Search

### <a name="linked-service"></a>Länkad tjänst
För att definiera ett Azure Search länkad tjänst genom att ange den **typ** på den länkade tjänsten till **AzureSearch**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| url | URL för Azure Search-tjänsten. | Ja |
| key | Admin-nyckel för Azure Search-tjänsten. | Ja |

#### <a name="example"></a>Exempel

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

Mer information finns i [Azure Search connector](data-factory-azure-search-connector.md#linked-service-properties) artikeln.

### <a name="dataset"></a>Datauppsättning
För att definiera en Azure Search-datauppsättning, ange den **typ** på datauppsättningen till **AzureSearchIndex**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| typ | Type-egenskapen måste anges till **AzureSearchIndex**.| Ja |
| Indexnamn | Namnet på Azure Search-index. Data Factory skapar inte indexet. Indexet måste finnas i Azure Search. | Ja |

#### <a name="example"></a>Exempel

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties": {
            "indexName": "products"
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
    }
}
```

Mer information finns i [Azure Search connector](data-factory-azure-search-connector.md#dataset-properties) artikeln.

### <a name="azure-search-index-sink-in-copy-activity"></a>Azure Search-Index mottagare i Kopieringsaktiviteten
Om du kopierar data till ett Azure Search-index, ange den **mottagare typ** av kopieringsaktiviteten till **AzureSearchIndexSink**, och ange följande egenskaper i den **mottagare** avsnitt:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Anger om du vill slå samman eller Ersätt när det finns redan ett dokument i indexet. | Sammanfoga (standard)<br/>Ladda upp| Nej |
| WriteBatchSize | Överför data till Azure Search-indexet när buffertstorleken når writeBatchSize. | 1 och 1 000. Standardvärdet är 1 000. | Nej |

#### <a name="example"></a>Exempel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoAzureSearchIndex",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureSearchIndexDataset"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "AzureSearchIndexSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Mer information finns i [Azure Search connector](data-factory-azure-search-connector.md#copy-activity-properties) artikeln.

## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="linked-service"></a>Länkad tjänst
Det finns två typer av länkade tjänster: Länkad Azure Storage-tjänst och Azure Storage SAS länkad tjänst.

#### <a name="azure-storage-linked-service"></a>Länkad Azure Storage-tjänst
Länka ditt Azure storage-konto till en datafabrik med hjälp av den **kontonyckel**, skapa en länkad Azure Storage-tjänst. Definiera ett Azure Storage länkade tjänsten genom att ange den **typ** på den länkade tjänsten till **AzureStorage**. Sedan kan du ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Type-egenskapen måste anges till: **AzureStorage** |Ja |
| connectionString |Ange information som behövs för att ansluta till Azure storage för connectionString-egenskapen. |Ja |

**Exempel:**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

#### <a name="azure-storage-sas-linked-service"></a>Länkad Azure Storage SAS-tjänst
Med Azure Storage SAS länkad service kan du länka ett Azure Storage-konto till en Azure-datafabrik med hjälp av en signatur för delad åtkomst (SAS). Data factory skapar med begränsade/Tidsbundna åtkomst till alla utvalda resurser (blobbehållare) i lagringen. Länkad tjänst för att länka ditt Azure storage-konto till en datafabrik med hjälp av signatur för delad åtkomst, skapa ett Azure Storage-SAS. För att definiera en Azure Storage-SAS länkad tjänst genom att ange den **typ** på den länkade tjänsten till **AzureStorageSas**. Sedan kan du ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Type-egenskapen måste anges till: **AzureStorageSas** |Ja |
| sasUri |Ange URI för delad åtkomst-signatur i Azure Storage-resurser, till exempel blob, behållaren eller tabellen. |Ja |

**Exempel:**

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<storageUri>?<sasToken>"
        }
    }
}
```

Läs mer om dessa länkade tjänster, [Azure Table Storage connector](data-factory-azure-table-connector.md#linked-service-properties) artikeln.

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en Azure-tabelldatauppsättning ange den **typ** på datauppsättningen till **AzureTable**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i Azure Table-databasinstansen som den länkade tjänsten refererar till. |Ja. När du anger ett tabellnamn utan en azureTableSourceQuery kopieras alla poster från tabellen till målet. Om en azureTableSourceQuery anges också kopieras poster från den tabell som uppfyller villkoren i frågan till målet. |

#### <a name="example"></a>Exempel

```json
{
    "name": "AzureTableInput",
    "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Läs mer om dessa länkade tjänster, [Azure Table Storage connector](data-factory-azure-table-connector.md#dataset-properties) artikeln.

### <a name="azure-table-source-in-copy-activity"></a>Azure Tabellkälla i Kopieringsaktiviteten
Om du kopierar data från Azure Table Storage, ange den **källtyp** av kopieringsaktiviteten till **AzureTableSource**, och ange följande egenskaper i den **källa** avsnitt:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| azureTableSourceQuery |Använd anpassad fråga för att läsa data. |Frågesträng för Azure-tabell. Se exemplen i nästa avsnitt. |Nej. När du anger ett tabellnamn utan en azureTableSourceQuery kopieras alla poster från tabellen till målet. Om en azureTableSourceQuery anges också kopieras poster från den tabell som uppfyller villkoren i frågan till målet. |
| azureTableSourceIgnoreTableNotFound |Indikera om det inte finns swallow undantag av tabellen. |SANT<br/>FALSKT |Nej |

#### <a name="example"></a>Exempel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureTabletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureTableSource",
                    "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Läs mer om dessa länkade tjänster, [Azure Table Storage connector](data-factory-azure-table-connector.md#copy-activity-properties) artikeln.

### <a name="azure-table-sink-in-copy-activity"></a>Azure-tabell mottagare i Kopieringsaktiviteten
Om du kopierar data till Azure Table Storage, ange den **mottagare typ** av kopieringsaktiviteten till **AzureTableSink**, och ange följande egenskaper i den **mottagare** avsnittet:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Standard partitionsnyckelvärde som kan användas av mottagaren. |Ett strängvärde. |Nej |
| azureTablePartitionKeyName |Ange namnet på kolumnen vars värden används som partitionsnycklar. Om den inte anges används AzureTableDefaultPartitionKeyValue som partitionsnyckel. |Ett kolumnnamn. |Nej |
| azureTableRowKeyName |Ange namnet på den kolumn vars kolumnvärdena används som radnyckel. Om inte anges kan du använda ett GUID för varje rad. |Ett kolumnnamn. |Nej |
| azureTableInsertType |Läget för att infoga data i Azure-tabell.<br/><br/>Den här egenskapen styr om befintliga rader i utdatatabellen med matchande partition och radnycklar har sina värden ersättas eller samman. <br/><br/>Läs om hur dessa inställningar (merge och Ersätt) fungerar i [Insert- eller Merge-entitet](https://msdn.microsoft.com/library/azure/hh452241.aspx) och [infoga eller ersätta entitet](https://msdn.microsoft.com/library/azure/hh452242.aspx) ämnen. <br/><br> Den här inställningen gäller på radnivå, inte på tabellnivå, och varken alternativet tar bort rader i utdatatabellen som inte finns i aktuella indata. |Sammanfoga (standard)<br/>Ersätt |Nej |
| WriteBatchSize |Infogar data i Azure-tabellen när writeBatchSize eller writeBatchTimeout uppnås. |Heltal (antal rader) |Nej (standard: 10000) |
| writeBatchTimeout |Infogar data i Azure-tabellen när writeBatchSize eller writeBatchTimeout namn |Tidsintervall<br/><br/>Exempel: ”00: 20:00” (20 minuter) |Nej (standard storage klienten standardvärdet för timeout-värdet 90 sek) |

#### <a name="example"></a>Exempel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoTable",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureTableOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureTableSink",
                    "writeBatchSize": 100,
                    "writeBatchTimeout": "01:00:00"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Läs mer om dessa länkade tjänster, [Azure Table Storage connector](data-factory-azure-table-connector.md#copy-activity-properties) artikeln.

## <a name="amazon-redshift"></a>Amazon RedShift

### <a name="linked-service"></a>Länkad tjänst
För att definiera en Amazon Redshift länkad tjänst genom att ange den **typ** på den länkade tjänsten till **AmazonRedshift**, och ange följande egenskaper i den **typeProperties** avsnittet :

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| server |IP-adressen eller värdnamnet namnet på Amazon Redshift-servern. |Ja |
| port |Antalet TCP-porten som Amazon Redshift-servern använder för att lyssna efter klientanslutningar. |Nej, standardvärde: 5439 |
| databas |Namnet på Amazon Redshift-databas. |Ja |
| användarnamn |Namnet på användaren som har åtkomst till databasen. |Ja |
| lösenord |Lösenordet för användarkontot. |Ja |

#### <a name="example"></a>Exempel

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties": {
        "type": "AmazonRedshift",
        "typeProperties": {
            "server": "<Amazon Redshift host name or IP address>",
            "port": 5439,
            "database": "<database name>",
            "username": "user",
            "password": "password"
        }
    }
}
```

Mer information finns i [Amazon Redshift-anslutningsappen](#data-factory-amazon-redshift-connector.md#linked-service-properties) artikeln.

### <a name="dataset"></a>Datauppsättning
För att definiera en Amazon Redshift-datamängd, ange den **typ** på datauppsättningen till **RelationalTable**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i Amazon Redshift-databas som den länkade tjänsten refererar till. |Nej (om **fråga** av **RelationalSource** har angetts) |


#### <a name="example"></a>Exempel

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Mer information finns i [Amazon Redshift-anslutningsappen](#data-factory-amazon-redshift-connector.md#dataset-properties) artikeln.

### <a name="relational-source-in-copy-activity"></a>Relationskälla i Kopieringsaktiviteten
Om du kopierar data från Amazon Redshift, ange den **källtyp** av kopieringsaktiviteten till **RelationalSource**, och ange följande egenskaper i den **källa** avsnittet:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd anpassad fråga för att läsa data. |SQL-sträng. Till exempel: `select * from MyTable`. |Nej (om **tableName** av **datauppsättning** har angetts) |

#### <a name="example"></a>Exempel

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonRedshiftInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonRedshiftToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
Mer information finns i [Amazon Redshift-anslutningsappen](#data-factory-amazon-redshift-connector.md#copy-activity-properties) artikeln.

## <a name="ibm-db2"></a>IBM DB2

### <a name="linked-service"></a>Länkad tjänst
För att definiera en IBM DB2 länkad tjänst genom att ange den **typ** på den länkade tjänsten till **OnPremisesDB2**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| server |Namn på DB2-servern. |Ja |
| databas |Namn på DB2-databas. |Ja |
| schemat |Namnet på schemat i databasen. Schemanamnet är skiftlägeskänsligt. |Nej |
| authenticationType |Typ av autentisering som används för att ansluta till DB2-databasen. Möjliga värden: Anonym, Basic och Windows. |Ja |
| användarnamn |Ange användarnamnet om du använder grundläggande eller Windows-autentisering. |Nej |
| lösenord |Ange lösenord för det användarkonto som du angav för användarnamnet. |Nej |
| gatewayName |Namnet på den gateway som Data Factory-tjänsten ska använda för att ansluta till den lokala DB2-databas. |Ja |

#### <a name="example"></a>Exempel
```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
Mer information finns i [IBM DB2-anslutningsapp](#data-factory-onprem-db2-connector.md#linked-service-properties) artikeln.

### <a name="dataset"></a>Datauppsättning
För att definiera en DB2-datauppsättningen, ange den **typ** på datauppsättningen till **RelationalTable**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i DB2-databasinstansen som den länkade tjänsten refererar till. TableName är skiftlägeskänsligt. |Nej (om **fråga** av **RelationalSource** har angetts)

#### <a name="example"></a>Exempel
```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Mer information finns i [IBM DB2-anslutningsapp](#data-factory-onprem-db2-connector.md#dataset-properties) artikeln.

### <a name="relational-source-in-copy-activity"></a>Relationskälla i Kopieringsaktiviteten
Om du kopierar data från IBM DB2, ange den **källtyp** av kopieringsaktiviteten till **RelationalSource**, och ange följande egenskaper i den **källa** avsnittet:


| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd anpassad fråga för att läsa data. |SQL-sträng. Till exempel: `"query": "select * from "MySchema"."MyTable""`. |Nej (om **tableName** av **datauppsättning** har angetts) |

#### <a name="example"></a>Exempel
```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from \"Orders\""
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "Db2DataSet"
            }],
            "outputs": [{
                "name": "AzureBlobDb2DataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "Db2ToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
Mer information finns i [IBM DB2-anslutningsapp](#data-factory-onprem-db2-connector.md#copy-activity-properties) artikeln.

## <a name="mysql"></a>MySQL

### <a name="linked-service"></a>Länkad tjänst
För att definiera en MySQL länkad tjänst genom att ange den **typ** på den länkade tjänsten till **OnPremisesMySql**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| server |Namnet på den MySQL-servern. |Ja |
| databas |Namnet på MySQL-databasen. |Ja |
| schemat |Namnet på schemat i databasen. |Nej |
| authenticationType |Typ av autentisering som används för att ansluta till MySQL-databasen. Möjliga värden är: `Basic`. |Ja |
| användarnamn |Ange användarnamn för anslutning till MySQL-databasen. |Ja |
| lösenord |Ange lösenord för det användarkonto som du har angett. |Ja |
| gatewayName |Namnet på den gateway som Data Factory-tjänsten ska använda för att ansluta till den lokala MySQL-databas. |Ja |

#### <a name="example"></a>Exempel

```json
{
    "name": "OnPremMySqlLinkedService",
    "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
            "server": "<server name>",
            "database": "<database name>",
            "schema": "<schema name>",
            "authenticationType": "<authentication type>",
            "userName": "<user name>",
            "password": "<password>",
            "gatewayName": "<gateway>"
        }
    }
}
```

Mer information finns i [MySQL connector](data-factory-onprem-mysql-connector.md#linked-service-properties) artikeln.

### <a name="dataset"></a>Datauppsättning
För att definiera en MySQL-datauppsättning, ange den **typ** på datauppsättningen till **RelationalTable**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i MySQL-databasinstansen som den länkade tjänsten refererar till. |Nej (om **fråga** av **RelationalSource** har angetts) |

#### <a name="example"></a>Exempel

```json
{
    "name": "MySqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremMySqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Mer information finns i [MySQL connector](data-factory-onprem-mysql-connector.md#dataset-properties) artikeln.

### <a name="relational-source-in-copy-activity"></a>Relationskälla i Kopieringsaktiviteten
Om du vill kopiera data från en MySQL-databas, anger du den **källtyp** av kopieringsaktiviteten till **RelationalSource**, och ange följande egenskaper i den **källa** avsnittet:


| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd anpassad fråga för att läsa data. |SQL-sträng. Till exempel: `select * from MyTable`. |Nej (om **tableName** av **datauppsättning** har angetts) |


#### <a name="example"></a>Exempel
```json
{
    "name": "CopyMySqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MySqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobMySqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MySqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Mer information finns i [MySQL connector](data-factory-onprem-mysql-connector.md#copy-activity-properties) artikeln.

## <a name="oracle"></a>Oracle

### <a name="linked-service"></a>Länkad tjänst
För att definiera en Oracle länkad tjänst genom att ange den **typ** på den länkade tjänsten till **OnPremisesOracle**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| driverType | Ange vilka drivrutiner som ska använda för att kopiera data från/till Oracle-databas. Tillåtna värden är **Microsoft** eller **ODP** (standard). Se [stöds version och vilka](#supported-versions-and-installation) avsnittet med mer information. | Nej |
| connectionString | Ange information som behövs för att ansluta till Oracle Database-instans för connectionString-egenskapen. | Ja |
| gatewayName | Namnet på den gateway som används för att ansluta till en lokal Oracle-server |Ja |

#### <a name="example"></a>Exempel
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Mer information finns i [Oracle-anslutningsapp](data-factory-onprem-oracle-connector.md#linked-service-properties) artikeln.

### <a name="dataset"></a>Datauppsättning
För att definiera en Oracle-datauppsättning, ange den **typ** på datauppsättningen till **OracleTable**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i Oracle-databas som den länkade tjänsten refererar till. |Nej (om **oracleReaderQuery** av **OracleSource** har angetts) |

#### <a name="example"></a>Exempel

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2016-02-27T12:00:00",
            "frequency": "Hour"
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Mer information finns i [Oracle-anslutningsapp](data-factory-onprem-oracle-connector.md#dataset-properties) artikeln.

### <a name="oracle-source-in-copy-activity"></a>Oracle-källan i Kopieringsaktiviteten
Om du vill kopiera data från en Oracle-databas, anger du den **källtyp** av kopieringsaktiviteten till **OracleSource**, och ange följande egenskaper i den **källa** avsnittet:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| oracleReaderQuery |Använd anpassad fråga för att läsa data. |SQL-sträng. Exempel: `select * from MyTable` <br/><br/>Om inte anges, SQL-instruktionen som körs: `select * from MyTable` |Nej (om **tableName** av **datauppsättning** har angetts) |

#### <a name="example"></a>Exempel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "OracletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " OracleInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "OracleSource",
                    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
            "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Mer information finns i [Oracle-anslutningsapp](data-factory-onprem-oracle-connector.md#copy-activity-properties) artikeln.

### <a name="oracle-sink-in-copy-activity"></a>Oracle-mottagare i Kopieringsaktiviteten
Om du kopierar data till am Oracle-databas, anger du den **mottagare typ** av kopieringsaktiviteten till **OracleSink**, och ange följande egenskaper i den **mottagare** avsnittet:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| writeBatchTimeout |Väntetid för batch insert-åtgärden ska slutföras innan tidsgränsen uppnås. |Tidsintervall<br/><br/> Exempel: 00:30:00 (30 minuter). |Nej |
| WriteBatchSize |Infogar data i SQL-tabell när buffertstorleken når writeBatchSize. |Heltal (antal rader) |Nej (standard: 100) |
| sqlWriterCleanupScript |Ange en fråga för Kopieringsaktiviteten till att köra så att data för en viss sektor rensas. |Ett frågeuttryck. |Nej |
| sliceIdentifierColumnName |Ange kolumnnamnet för Kopieringsaktivitet för att fylla med automatiskt genererad sektorn identifierare som används för att rensa data för en viss sektor när köras på nytt. |Kolumnnamnet på en kolumn med datatypen för binary(32). |Nej |

#### <a name="example"></a>Exempel
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-05T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoOracle",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "OracleOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "OracleSink"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Mer information finns i [Oracle-anslutningsapp](data-factory-onprem-oracle-connector.md#copy-activity-properties) artikeln.

## <a name="postgresql"></a>PostgreSQL

### <a name="linked-service"></a>Länkad tjänst
För att definiera en PostgreSQL länkad tjänst genom att ange den **typ** på den länkade tjänsten till **OnPremisesPostgreSql**, och ange följande egenskaper i den **typeProperties** avsnitt:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| server |Namnet på PostgreSQL-servern. |Ja |
| databas |Namnet på PostgreSQL-databasen. |Ja |
| schemat |Namnet på schemat i databasen. Schemanamnet är skiftlägeskänsligt. |Nej |
| authenticationType |Typ av autentisering som används för att ansluta till PostgreSQL-databasen. Möjliga värden: Anonym, Basic och Windows. |Ja |
| användarnamn |Ange användarnamnet om du använder grundläggande eller Windows-autentisering. |Nej |
| lösenord |Ange lösenord för det användarkonto som du angav för användarnamnet. |Nej |
| gatewayName |Namnet på den gateway som Data Factory-tjänsten ska använda för att ansluta till den lokala PostgreSQL-databasen. |Ja |

#### <a name="example"></a>Exempel

```json
{
    "name": "OnPremPostgreSqlLinkedService",
    "properties": {
        "type": "OnPremisesPostgreSql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
Mer information finns i [PostgreSQL connector](data-factory-onprem-postgresql-connector.md#linked-service-properties) artikeln.

### <a name="dataset"></a>Datauppsättning
För att definiera en PostgreSQL-datauppsättning, ange den **typ** på datauppsättningen till **RelationalTable**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i PostgreSQL-databasinstansen som den länkade tjänsten refererar till. TableName är skiftlägeskänsligt. |Nej (om **fråga** av **RelationalSource** har angetts) |

#### <a name="example"></a>Exempel
```json
{
    "name": "PostgreSqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremPostgreSqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Mer information finns i [PostgreSQL connector](data-factory-onprem-postgresql-connector.md#dataset-properties) artikeln.

### <a name="relational-source-in-copy-activity"></a>Relationskälla i Kopieringsaktiviteten
Om du vill kopiera data från en PostgreSQL-databas, anger du den **källtyp** av kopieringsaktiviteten till **RelationalSource**, och ange följande egenskaper i den **källa** avsnitt:


| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd anpassad fråga för att läsa data. |SQL-sträng. Till exempel: ”query” ”: Välj * från \"MySchema\".\" MyTable\"”. |Nej (om **tableName** av **datauppsättning** har angetts) |

#### <a name="example"></a>Exempel

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from \"public\".\"usstates\""
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "PostgreSqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobPostgreSqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "PostgreSqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Mer information finns i [PostgreSQL connector](data-factory-onprem-postgresql-connector.md#copy-activity-properties) artikeln.

## <a name="sap-business-warehouse"></a>SAP Business Warehouse


### <a name="linked-service"></a>Länkad tjänst
För att definiera en SAP Business Warehouse (BW) länkad tjänst genom att ange den **typ** på den länkade tjänsten till **SapBw**, och ange följande egenskaper i den **typeProperties** avsnittet :

Egenskap  | Beskrivning | Tillåtna värden | Krävs
-------- | ----------- | -------------- | --------
server | Namnet på den server som SAP BW-instansen finns. | sträng | Ja
systemNumber | Systemnummer för SAP BW-system. | Tvåsiffrig decimaltal representeras som en sträng. | Ja
ClientId | Klient-ID för klienten i SAP W systemet. | Tresiffrig decimaltal representeras som en sträng. | Ja
användarnamn | Namnet på den användare som har åtkomst till SAP-server | sträng | Ja
lösenord | Lösenordet för användaren. | sträng | Ja
gatewayName | Namnet på den gateway som Data Factory-tjänsten ska använda för att ansluta till en lokal SAP BW-instansen. | sträng | Ja
encryptedCredential | Strängen som krypterade autentiseringsuppgifter. | sträng | Nej

#### <a name="example"></a>Exempel

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Mer information finns i [SAP Business Warehouse-anslutningsappen](data-factory-sap-business-warehouse-connector.md#linked-service-properties) artikeln.

### <a name="dataset"></a>Datauppsättning
För att definiera en SAP BW-datauppsättning, ange den **typ** på datauppsättningen till **RelationalTable**. Det finns inga typspecifika egenskaper som stöds för SAP BW-datauppsättningen av typen **RelationalTable**.

#### <a name="example"></a>Exempel

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Mer information finns i [SAP Business Warehouse-anslutningsappen](data-factory-sap-business-warehouse-connector.md#dataset-properties) artikeln.

### <a name="relational-source-in-copy-activity"></a>Relationskälla i Kopieringsaktiviteten
Om du kopierar data från SAP Business Warehouse, anger du den **källtyp** av kopieringsaktiviteten till **RelationalSource**, och ange följande egenskaper i den **källa** avsnitt:


| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB | Anger MDX-fråga för att läsa data från SAP BW-instans. | MDX-fråga. | Ja |

#### <a name="example"></a>Exempel

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<MDX query for SAP BW>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapBwDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapBwToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

Mer information finns i [SAP Business Warehouse-anslutningsappen](data-factory-sap-business-warehouse-connector.md#copy-activity-properties) artikeln.

## <a name="sap-hana"></a>SAP HANA

### <a name="linked-service"></a>Länkad tjänst
För att definiera en SAP HANA länkad tjänst genom att ange den **typ** på den länkade tjänsten till **SapHana**, och ange följande egenskaper i den **typeProperties** avsnittet:

Egenskap  | Beskrivning | Tillåtna värden | Krävs
-------- | ----------- | -------------- | --------
server | Namnet på den server som SAP HANA-instans finns. Om servern använder en anpassad port, ange `server:port`. | sträng | Ja
authenticationType | Typ av autentisering. | sträng. ”Grundläggande” eller ”Windows” | Ja
användarnamn | Namnet på den användare som har åtkomst till SAP-server | sträng | Ja
lösenord | Lösenordet för användaren. | sträng | Ja
gatewayName | Namnet på den gateway som Data Factory-tjänsten ska använda för att ansluta till en lokal SAP HANA-instans. | sträng | Ja
encryptedCredential | Strängen som krypterade autentiseringsuppgifter. | sträng | Nej

#### <a name="example"></a>Exempel

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}

```
Mer information finns i [SAP HANA-anslutningsappen](data-factory-sap-hana-connector.md#linked-service-properties) artikeln.

### <a name="dataset"></a>Datauppsättning
För att definiera en SAP HANA-datauppsättning, ange den **typ** på datauppsättningen till **RelationalTable**. Det finns inga typspecifika egenskaper som stöds för SAP HANA-datauppsättningen av typen **RelationalTable**.

#### <a name="example"></a>Exempel

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Mer information finns i [SAP HANA-anslutningsappen](data-factory-sap-hana-connector.md#dataset-properties) artikeln.

### <a name="relational-source-in-copy-activity"></a>Relationskälla i Kopieringsaktiviteten
Om du kopierar data från ett datalager för SAP HANA, ange den **källtyp** av kopieringsaktiviteten till **RelationalSource**, och ange följande egenskaper i den **källa** avsnitt:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB | Anger SQL-frågan som läser data från SAP HANA-instans. | SQL-fråga. | Ja |


#### <a name="example"></a>Exempel


```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<SQL Query for HANA>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapHanaDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapHanaToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

Mer information finns i [SAP HANA-anslutningsappen](data-factory-sap-hana-connector.md#copy-activity-properties) artikeln.


## <a name="sql-server"></a>SQL Server

### <a name="linked-service"></a>Länkad tjänst
Du skapar en länkad tjänst av typen **OnPremisesSqlServer** att länka en lokal SQL Server-databas till en data factory. Följande tabell innehåller en beskrivning för JSON-element som är specifika för den lokala SQL Server-länkade tjänst.

Följande tabell innehåller en beskrivning för JSON-element som är specifika för SQL Server-länkade tjänst.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Type-egenskapen ska anges till: **OnPremisesSqlServer**. |Ja |
| connectionString |Ange connectionString information som behövs för att ansluta till en lokal SQL Server-databasen med hjälp av SQL-autentisering eller Windows-autentisering. |Ja |
| gatewayName |Namnet på den gateway som Data Factory-tjänsten ska använda för att ansluta till en lokal SQL Server-databasen. |Ja |
| användarnamn |Ange användarnamnet om du använder Windows-autentisering. Exempel: **domainname\\användarnamn**. |Nej |
| lösenord |Ange lösenord för det användarkonto som du angav för användarnamnet. |Nej |

Du kan kryptera autentiseringsuppgifter med hjälp av den **New-AzureRmDataFactoryEncryptValue** cmdlet och Använd dem i anslutningssträngen som du ser i följande exempel (**EncryptedCredential** egenskapen):

```json
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Exempel: JSON för att använda SQL-autentisering

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>Exempel: JSON för att använda Windows-autentisering

Om användarnamn och lösenord anges använder gateway dem för att personifiera det angivna användarkontot för att ansluta till en lokal SQL Server-databasen. I annat fall ansluter gateway till SQL-servern direkt med säkerhetskontexten för Gateway (dess start-konto).

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Mer information finns i [SQL Server-anslutningen](data-factory-sqlserver-connector.md#linked-service-properties) artikeln.

### <a name="dataset"></a>Datauppsättning
För att definiera en datauppsättning för SQL Server, ange den **typ** på datauppsättningen till **SqlServerTable**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen eller vyn i SQL Server-databasinstansen som den länkade tjänsten refererar till. |Ja |

#### <a name="example"></a>Exempel
```json
{
    "name": "SqlServerInput",
    "properties": {
        "type": "SqlServerTable",
        "linkedServiceName": "SqlServerLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Mer information finns i [SQL Server-anslutningen](data-factory-sqlserver-connector.md#dataset-properties) artikeln.

### <a name="sql-source-in-copy-activity"></a>SQL-källans i Kopieringsaktiviteten
Om du kopierar data från en SQL Server-databas, ange den **källtyp** av kopieringsaktiviteten till **SqlSource**, och ange följande egenskaper i den **källa** avsnittet:


| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| sqlReaderQuery |Använd anpassad fråga för att läsa data. |SQL-sträng. Till exempel: `select * from MyTable`. Kan referera till flera tabeller från databasen som refereras av datauppsättningen för indata. Om inte anges, SQL-instruktionen som körs: Välj ett värde från MyTable. |Nej |
| sqlReaderStoredProcedureName |Namnet på den lagrade proceduren som läser data från källtabellen. |Namnet på den lagrade proceduren. |Nej |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värde-par. Namn och versaler och gemener i parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. |Nej |

Om den **sqlReaderQuery** har angetts för SqlSource, Kopieringsaktivitet körs den här frågan mot SQL Server Database-källan för att hämta data.

Du kan också ange en lagrad procedur genom att ange den **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren tar parametrar).

Om du inte anger sqlReaderQuery eller sqlReaderStoredProcedureName, används de kolumner som definierats i avsnittet struktur för att skapa en select-frågan ska köras mot SQL Server-databasen. Om definitionen för datauppsättningen inte har strukturen, markeras alla kolumner från tabellen.

> [!NOTE]
> När du använder **sqlReaderStoredProcedureName**, behöver du fortfarande ange ett värde för den **tableName** egenskap i JSON-datauppsättningen. Det finns inga verifieringar utförs men mot den här tabellen.


#### <a name="example"></a>Exempel
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

I det här exemplet **sqlReaderQuery** har angetts för SqlSource. Kopieringsaktivitet körs den här frågan mot SQL Server Database-källan för att hämta data. Du kan också ange en lagrad procedur genom att ange den **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren tar parametrar). SqlReaderQuery kan referera till flera tabeller i databasen refererar till datauppsättningen för indata. Det är inte begränsad till endast tabellen som datauppsättningens tableName typeProperty.

Om du inte anger sqlReaderQuery eller sqlReaderStoredProcedureName, används de kolumner som definierats i avsnittet struktur för att skapa en select-frågan ska köras mot SQL Server-databasen. Om definitionen för datauppsättningen inte har strukturen, markeras alla kolumner från tabellen.

Mer information finns i [SQL Server-anslutningen](data-factory-sqlserver-connector.md#copy-activity-properties) artikeln.

### <a name="sql-sink-in-copy-activity"></a>SQL-mottagare i Kopieringsaktiviteten
Om du kopierar data till en SQL Server-databas, anger du den **mottagare typ** av kopieringsaktiviteten till **SqlSink**, och ange följande egenskaper i den **mottagare** avsnittet:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| writeBatchTimeout |Väntetid för batch insert-åtgärden ska slutföras innan tidsgränsen uppnås. |Tidsintervall<br/><br/> Exempel: ”00: 30:00” (30 minuter). |Nej |
| WriteBatchSize |Infogar data i SQL-tabell när buffertstorleken når writeBatchSize. |Heltal (antal rader) |Nej (standard: 10000) |
| sqlWriterCleanupScript |Ange fråga för Kopieringsaktiviteten till att köra så att data för en viss sektor rensas. Mer information finns i [repeterbarhet](#repeatability-during-copy) avsnittet. |Ett frågeuttryck. |Nej |
| sliceIdentifierColumnName |Ange kolumnnamnet för Kopieringsaktivitet för att fylla med automatiskt genererad sektorn identifierare som används för att rensa data för en viss sektor när köras på nytt. Mer information finns i [repeterbarhet](#repeatability-during-copy) avsnittet. |Kolumnnamnet på en kolumn med datatypen för binary(32). |Nej |
| sqlWriterStoredProcedureName |Namnet på den lagrade proceduren som upsertar (uppdateringar/infogningar) data i måltabellen. |Namnet på den lagrade proceduren. |Nej |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värde-par. Namn och versaler och gemener i parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. |Nej |
| sqlWriterTableType |Ange tabellen typnamn som ska användas i den lagrade proceduren. Kopieringsaktivitet tillhandahåller data som flyttas i en temporär tabell med den här tabellen. Lagrad procedur kod kan sedan sammanfoga data kopieras med befintliga data. |Ett namn för tabellen. |Nej |

#### <a name="example"></a>Exempel
Pipelinen innehåller en Kopieringsaktivitet som är konfigurerad för att använda dessa in- och utdatauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen i **källa** är **BlobSource** och **mottagare** är **SqlSink**.

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": " SqlServerOutput "
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Mer information finns i [SQL Server-anslutningen](data-factory-sqlserver-connector.md#copy-activity-properties) artikeln.

## <a name="sybase"></a>Sybase

### <a name="linked-service"></a>Länkad tjänst
För att definiera en Sybase länkad tjänst genom att ange den **typ** på den länkade tjänsten till **OnPremisesSybase**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| server |Namnet på Sybase-servern. |Ja |
| databas |Namnet på Sybase-databas. |Ja |
| schemat |Namnet på schemat i databasen. |Nej |
| authenticationType |Typ av autentisering som används för att ansluta till Sybase-databasen. Möjliga värden: Anonym, Basic och Windows. |Ja |
| användarnamn |Ange användarnamnet om du använder grundläggande eller Windows-autentisering. |Nej |
| lösenord |Ange lösenord för det användarkonto som du angav för användarnamnet. |Nej |
| gatewayName |Namnet på den gateway som Data Factory-tjänsten ska använda för att ansluta till den lokala Sybase-databas. |Ja |

#### <a name="example"></a>Exempel
```json
{
    "name": "OnPremSybaseLinkedService",
    "properties": {
        "type": "OnPremisesSybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

Mer information finns i [Sybase connector](data-factory-onprem-sybase-connector.md#linked-service-properties) artikeln.

### <a name="dataset"></a>Datauppsättning
För att definiera en Sybase-datauppsättning, ange den **typ** på datauppsättningen till **RelationalTable**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i Sybase-databasinstansen som den länkade tjänsten refererar till. |Nej (om **fråga** av **RelationalSource** har angetts) |

#### <a name="example"></a>Exempel

```json
{
    "name": "SybaseDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremSybaseLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Mer information finns i [Sybase connector](data-factory-onprem-sybase-connector.md#dataset-properties) artikeln.

### <a name="relational-source-in-copy-activity"></a>Relationskälla i Kopieringsaktiviteten
Om du vill kopiera data från en Sybase-databas, anger du den **källtyp** av kopieringsaktiviteten till **RelationalSource**, och ange följande egenskaper i den **källa** avsnittet :


| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd anpassad fråga för att läsa data. |SQL-sträng. Till exempel: `select * from MyTable`. |Nej (om **tableName** av **datauppsättning** har angetts) |

#### <a name="example"></a>Exempel

```json
{
    "name": "CopySybaseToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from DBA.Orders"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "SybaseDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobSybaseDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SybaseToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Mer information finns i [Sybase connector](data-factory-onprem-sybase-connector.md#copy-activity-properties) artikeln.

## <a name="teradata"></a>Teradata

### <a name="linked-service"></a>Länkad tjänst
För att definiera en Teradata länkad tjänst genom att ange den **typ** på den länkade tjänsten till **OnPremisesTeradata**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| server |Namnet på Teradata-servern. |Ja |
| authenticationType |Typ av autentisering som används för att ansluta till Teradata-databasen. Möjliga värden: Anonym, Basic och Windows. |Ja |
| användarnamn |Ange användarnamnet om du använder grundläggande eller Windows-autentisering. |Nej |
| lösenord |Ange lösenord för det användarkonto som du angav för användarnamnet. |Nej |
| gatewayName |Namnet på den gateway som Data Factory-tjänsten ska använda för att ansluta till den lokala Teradata-databas. |Ja |

#### <a name="example"></a>Exempel
```json
{
    "name": "OnPremTeradataLinkedService",
    "properties": {
        "type": "OnPremisesTeradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

Mer information finns i [Teradata connector](data-factory-onprem-teradata-connector.md#linked-service-properties) artikeln.

### <a name="dataset"></a>Datauppsättning
För att definiera en Teradata-Blob-datauppsättning, ange den **typ** på datauppsättningen till **RelationalTable**. Det finns för närvarande inga egenskaper som stöds för Teradata-datauppsättningen.

#### <a name="example"></a>Exempel
```json
{
    "name": "TeradataDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremTeradataLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Mer information finns i [Teradata connector](data-factory-onprem-teradata-connector.md#dataset-properties) artikeln.

### <a name="relational-source-in-copy-activity"></a>Relationskälla i Kopieringsaktiviteten
Om du vill kopiera data från en Teradata-databas, anger du den **källtyp** av kopieringsaktiviteten till **RelationalSource**, och ange följande egenskaper i den **källa** avsnitt:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd anpassad fråga för att läsa data. |SQL-sträng. Till exempel: `select * from MyTable`. |Ja |

#### <a name="example"></a>Exempel

```json
{
    "name": "CopyTeradataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "TeradataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobTeradataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "TeradataToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "isPaused": false
    }
}
```

Mer information finns i [Teradata connector](data-factory-onprem-teradata-connector.md#copy-activity-properties) artikeln.

## <a name="cassandra"></a>Cassandra


### <a name="linked-service"></a>Länkad tjänst
För att definiera en Cassandra-länkad tjänst, ange den **typ** på den länkade tjänsten till **OnPremisesCassandra**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| värd |En eller flera IP-adresser eller värdnamn för Cassandra-servrar.<br/><br/>Ange en kommaavgränsad lista med IP-adresser eller värdnamn för att ansluta till alla servrar samtidigt. |Ja |
| port |TCP-port som Cassandra-servern använder för att lyssna efter klientanslutningar. |Nej, standardvärde: 9042 |
| authenticationType |Grundläggande eller anonym |Ja |
| användarnamn |Ange användarnamn för användarkontot. |Ja, om authenticationType anges till Basic. |
| lösenord |Ange lösenordet för användarkontot. |Ja, om authenticationType anges till Basic. |
| gatewayName |Namnet på den gateway som används för att ansluta till den lokala Cassandra-databasen. |Ja |
| encryptedCredential |Autentiseringsuppgifter har krypterats av gatewayen. |Nej |

#### <a name="example"></a>Exempel

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "OnPremisesCassandra",
        "typeProperties": {
            "authenticationType": "Basic",
            "host": "<cassandra server name or IP address>",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Mer information finns i [Cassandra connector](data-factory-onprem-cassandra-connector.md#linked-service-properties) artikeln.

### <a name="dataset"></a>Datauppsättning
För att definiera en Cassandra-datauppsättning, ange den **typ** på datauppsättningen till **CassandraTable**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| keyspace |Namnet på keyspace eller schema i Cassandra-databasen. |Ja (om **fråga** för **CassandraSource** har inte definierats). |
| tableName |Namnet på tabellen i Cassandra-databas. |Ja (om **fråga** för **CassandraSource** har inte definierats). |

#### <a name="example"></a>Exempel

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "<key space>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Mer information finns i [Cassandra connector](data-factory-onprem-cassandra-connector.md#dataset-properties) artikeln.

### <a name="cassandra-source-in-copy-activity"></a>Cassandra-källan i Kopieringsaktiviteten
Om du kopierar data från Cassandra, ange den **källtyp** av kopieringsaktiviteten till **CassandraSource**, och ange följande egenskaper i den **källa** avsnittet:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd anpassad fråga för att läsa data. |SQL-92 fråga eller CQL-fråga. Se [CQL referens](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>När du använder SQL-fråga, ange **keyspace name.table namn** som representerar den tabell som du vill fråga. |Nej (om tabellnamn och keyspace för datauppsättningen har definierats). |
| consistencyLevel |Konsekvensnivån som anger hur många kopior måste svara på en läsbegäran innan det returneras data till klientprogrammet. Cassandra kontrollerar det angivna antalet repliker för data för att tillgodose läsförfrågan. |EN, TVÅ, TRE, KVORUM, ALL, LOCAL_QUORUM EACH_QUORUM, LOCAL_ONE. Se [konfigurera datakonsekvens](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) mer information. |Nej. Standardvärdet är en. |

#### <a name="example"></a>Exempel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "CassandraInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Mer information finns i [Cassandra connector](data-factory-onprem-cassandra-connector.md#copy-activity-properties) artikeln.

## <a name="mongodb"></a>MongoDB

### <a name="linked-service"></a>Länkad tjänst
För att definiera en MongoDB länkad tjänst genom att ange den **typ** på den länkade tjänsten till **OnPremisesMongoDB**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| server |IP-adressen eller värdnamnet namnet på MongoDB-servern. |Ja |
| port |TCP-port som MongoDB-servern använder för att lyssna efter klientanslutningar. |Valfritt, standardvärde: 27017 |
| authenticationType |Grundläggande eller anonym. |Ja |
| användarnamn |Användarkonto för att få åtkomst till MongoDB. |Ja (om du använder grundläggande autentisering). |
| lösenord |Lösenordet för användaren. |Ja (om du använder grundläggande autentisering). |
| authSource |Namnet på MongoDB-databasen som du vill använda för att kontrollera dina autentiseringsuppgifter för autentisering. |Valfritt (om du använder grundläggande autentisering). standard: använder administratörskontot och databasen som anges med egenskapen databaseName. |
| databaseName |Namnet på MongoDB-databasen som du vill komma åt. |Ja |
| gatewayName |Namnet på den gateway som ansluter till datalagret. |Ja |
| encryptedCredential |Autentiseringsuppgifter har krypterats av gateway. |Valfri |

#### <a name="example"></a>Exempel

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties": {
        "type": "OnPremisesMongoDb",
        "typeProperties": {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
            "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Mer information finns i [MongoDB connector artikeln](data-factory-on-premises-mongodb-connector.md#linked-service-properties)

### <a name="dataset"></a>Datauppsättning
För att definiera en MongoDB-datauppsättning, ange den **typ** på datauppsättningen till **MongoDbCollection**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| Samlingsnamn |Namnet på samlingen i MongoDB-databas. |Ja |

#### <a name="example"></a>Exempel

```json
{
    "name": "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

Mer information finns i [MongoDB connector artikeln](data-factory-on-premises-mongodb-connector.md#dataset-properties)

#### <a name="mongodb-source-in-copy-activity"></a>MongoDB-källa i Kopieringsaktiviteten
Om du kopierar data från MongoDB, ange den **källtyp** av kopieringsaktiviteten till **MongoDbSource**, och ange följande egenskaper i den **källa** avsnittet:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd anpassad fråga för att läsa data. |SQL-92 frågesträngen. Till exempel: `select * from MyTable`. |Nej (om **collectionName** av **datauppsättning** har angetts) |

#### <a name="example"></a>Exempel

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "MongoDbSource",
                    "query": "select * from MyTable"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MongoDbInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MongoDBToAzureBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Mer information finns i [MongoDB connector artikeln](data-factory-on-premises-mongodb-connector.md#copy-activity-properties)

## <a name="amazon-s3"></a>Amazon S3


### <a name="linked-service"></a>Länkad tjänst
För att definiera en Amazon S3 länkad tjänst genom att ange den **typ** på den länkade tjänsten till **AwsAccessKey**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| accessKeyID |ID för den hemliga åtkomstnyckeln. |sträng |Ja |
| secretAccessKey |Den hemliga åtkomstnyckeln själva. |Krypterad hemlighet sträng |Ja |

#### <a name="example"></a>Exempel
```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

Mer information finns i [Amazon S3 connector artikeln](data-factory-amazon-simple-storage-service-connector.md#linked-service-properties).

### <a name="dataset"></a>Datauppsättning
För att definiera en Amazon S3-datamängd, ange den **typ** på datauppsättningen till **AmazonS3**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| bucketName |S3-Bucketnamn. |Sträng |Ja |
| key |Objektnyckel S3. |Sträng |Nej |
| prefix |Prefix för Objektnyckel S3. Objekt vars nycklar som börjar med prefixet är markerade. Gäller endast när nyckeln är tom. |Sträng |Nej |
| version |Versionen av S3-objekt om S3 versionshantering är aktiverad. |Sträng |Nej |
| Format | Följande formattyper av stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange den **typ** egenskapen under format till ett av dessa värden. Mer information finns i [textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-formatet](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc-Format](data-factory-supported-file-and-compression-formats.md#orc-format), och [Parquet-Format](data-factory-supported-file-and-compression-formats.md#parquet-format) avsnitt. <br><br> Om du vill **kopiera filer som – är** hoppa över avsnittet format i både inkommande och utgående datamängd definitioner mellan filbaserade (binär kopia). |Nej | |
| Komprimering | Ange typ och komprimeringsnivå för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**. Nivåerna som stöds är: **Optimal** och **snabbaste**. Mer information finns i [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nej | |


> [!NOTE]
> bucketName + nyckel anger du platsen för S3-objektet där bucket är Rotbehållare för S3-objekt och nyckeln är den fullständiga sökvägen till S3-objektet.

#### <a name="example-sample-dataset-with-prefix"></a>Exempel: Exempeldatauppsättningen med prefix

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "<S3 bucket name>",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
#### <a name="example-sample-data-set-with-version"></a>Exempel: Exempeldatauppsättning (med version)

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "<S3 bucket name>",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

#### <a name="example-dynamic-paths-for-s3"></a>Exempel: Dynamisk sökvägar för S3
I det här exemplet använder vi fasta värden för nyckeln och bucketName egenskaper i Amazon S3-datamängd.

```json
"key": "testFolder/test.orc",
"bucketName": "<S3 bucket name>",
```

Du kan ha Data Factory beräknar nyckel och bucketName dynamiskt vid körning med hjälp av systemvariabler, till exempel SliceStart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Du kan göra detsamma för egenskapen prefix för Amazon S3-datamängd. Se [Data Factory-funktioner och systemvariabler](data-factory-functions-variables.md) en lista över funktioner som stöds och variabler.

Mer information finns i [Amazon S3 connector artikeln](data-factory-amazon-simple-storage-service-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Filkälla System i Kopieringsaktiviteten
Om du kopierar data från Amazon S3, ange den **källtyp** av kopieringsaktiviteten till **FileSystemSource**, och ange följande egenskaper i den **källa** avsnittet:


| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| rekursiv |Anger om rekursivt lista S3 objekt i katalogen. |SANT/FALSKT |Nej |


#### <a name="example"></a>Exempel


```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource",
                    "recursive": true
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonS3InputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonS3ToBlob"
        }],
        "start": "2016-08-08T18:00:00",
        "end": "2016-08-08T19:00:00"
    }
}
```

Mer information finns i [Amazon S3 connector artikeln](data-factory-amazon-simple-storage-service-connector.md#copy-activity-properties).

## <a name="file-system"></a>Filsystem


### <a name="linked-service"></a>Länkad tjänst
Du kan länka ett lokalt filsystem till en Azure-datafabrik med den **lokala filservern** länkad tjänst. I följande tabell innehåller beskrivningar av JSON-element som är specifika för den lokala filen länkad Server-tjänsten.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Kontrollera att type-egenskapen är inställd på **OnPremisesFileServer**. |Ja |
| värd |Anger rotsökvägen i den mapp som du vill kopiera. Använd escape-tecknet ”\” för specialtecken i strängen. Se [exempel länkad tjänst-och datauppsättningen](#sample-linked-service-and-dataset-definitions) exempel. |Ja |
| användar-ID |Ange ID för den användare som har åtkomst till servern. |Nej (om du väljer encryptedCredential) |
| lösenord |Ange lösenordet för användaren (användar-ID). |Nej (om du väljer encryptedCredential |
| encryptedCredential |Ange de krypterade autentiseringsuppgifterna som du kan få genom att köra cmdlet New-AzureRmDataFactoryEncryptValue. |Nej (om du vill ange användar-ID och lösenord i klartext) |
| gatewayName |Anger namnet på den gateway som Data Factory ska använda för att ansluta till den lokala servern. |Ja |

#### <a name="sample-folder-path-definitions"></a>Exemplet mappen sökväg definitioner
| Scenario | Vara värd för i definition av länkad tjänst | folderPath i definitionen av datauppsättningen |
| --- | --- | --- |
| Lokal mapp på Data Management Gateway-datorn: <br/><br/>Exempel: D:\\ \* eller D:\folder\subfolder\\* |D:\\ \\ (för Data Management Gateway 2.0 och senare versioner) <br/><br/> localhost (för tidigare versioner än Data Management Gateway 2.0) |. \\ \\ eller mapp\\\\undermapp (för Data Management Gateway 2.0 och senare versioner) <br/><br/>D:\\ \\ eller D:\\\\mappen\\\\undermapp (för gateway som är äldre än 2.0) |
| Delad fjärrmapp: <br/><br/>Exempel: \\ \\minserver\\dela\\ \* eller \\ \\minserver\\dela\\mappen\\undermapp\\* |\\\\\\\\minserver\\\\dela |. \\ \\ eller mapp\\\\undermapp |


#### <a name="example-using-username-and-password-in-plain-text"></a>Exempel: Med användarnamn och lösenord i klartext

```json
{
    "Name": "OnPremisesFileServerLinkedService",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "\\\\Contosogame-Asia",
            "userid": "Admin",
            "password": "123456",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-encryptedcredential"></a>Exempel: Med hjälp av encryptedcredential

```json
{
    "Name": " OnPremisesFileServerLinkedService ",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "D:\\",
            "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Mer information finns i [filsystem connector artikeln](data-factory-onprem-file-system-connector.md#linked-service-properties).

### <a name="dataset"></a>Datauppsättning
För att definiera en datauppsättning för filsystem, ange den **typ** på datauppsättningen till **FileShare**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Anger underordnad sökväg innehavaradministratörens till mappen. Använd escape-tecknet ”\” för specialtecken i strängen. Se [exempel länkad tjänst-och datauppsättningen](#sample-linked-service-and-dataset-definitions) exempel.<br/><br/>Du kan kombinera den här egenskapen med **partitionBy** ha mappen sökvägarna baserat på sektorn start/slut datum / tid. |Ja |
| fileName |Ange namnet på filen i den **folderPath** om du vill att tabellen för att referera till en viss fil i mappen. Om du inte anger något värde för den här egenskapen, tabellen pekar på alla filer i mappen.<br/><br/>När filnamn har angetts för en utdatauppsättning, är namnet på den genererade filen i följande format: <br/><br/>`Data.<Guid>.txt` (Exempel: Data.0a405f8a-93ff-4C6F-B3BE-f69616f1df7a.txt) |Nej |
| fileFilter |Ange ett filter som används för att välja en delmängd av filer i folderPath i stället för alla filer. <br/><br/>Tillåtna värden är: `*` (flera tecken) och `?` (tecken).<br/><br/>Exempel 1: ”fileFilter” ”: * .log”<br/>Exempel 2: ”fileFilter”: 2016 - 1-?. txt ”<br/><br/>Observera att fileFilter gäller för en indatauppsättning filresursen. |Nej |
| partitionedBy |Du kan använda partitionedBy för att ange en dynamisk folderPath/fileName för time series-data. Ett exempel är folderPath som innehåller parametrar för varje timme som data. |Nej |
| Format | Följande formattyper av stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange den **typ** egenskapen under format till ett av dessa värden. Mer information finns i [textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-formatet](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc-Format](data-factory-supported-file-and-compression-formats.md#orc-format), och [Parquet-Format](data-factory-supported-file-and-compression-formats.md#parquet-format) avsnitt. <br><br> Om du vill **kopiera filer som – är** hoppa över avsnittet format i både inkommande och utgående datamängd definitioner mellan filbaserade (binär kopia). |Nej |
| Komprimering | Ange typ och komprimeringsnivå för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**; och nivåer som stöds är: **Optimal** och **snabbaste**. Se [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nej |

> [!NOTE]
> Du kan inte använda filnamn och fileFilter samtidigt.

#### <a name="example"></a>Exempel

```json
{
    "name": "OnpremisesFileSystemInput",
    "properties": {
        "type": " FileShare",
        "linkedServiceName": " OnPremisesFileServerLinkedService ",
        "typeProperties": {
            "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
            "fileName": "{Hour}.csv",
            "partitionedBy": [{
                "name": "Year",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                        "format": "yyyy"
                }
            }, {
                "name": "Month",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "MM"
                }
            }, {
                "name": "Day",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "dd"
                }
            }, {
                "name": "Hour",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "HH"
                }
            }]
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Mer information finns i [filsystem connector artikeln](data-factory-onprem-file-system-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Filkälla System i Kopieringsaktiviteten
Om du kopierar data från filsystemet, ange den **källtyp** av kopieringsaktiviteten till **FileSystemSource**, och ange följande egenskaper i den **källa** avsnittet:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| rekursiv |Anger om data läses rekursivt från undermapparna eller endast från den angivna mappen. |SANT, FALSKT (standard) |Nej |

#### <a name="example"></a>Exempel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T19:00:00",
        "description": "Pipeline for copy activity",
        "activities": [{
            "name": "OnpremisesFileSystemtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "OnpremisesFileSystemInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
            "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Mer information finns i [filsystem connector artikeln](data-factory-onprem-file-system-connector.md#copy-activity-properties).

### <a name="file-system-sink-in-copy-activity"></a>Filsystem mottagare i Kopieringsaktiviteten
Om du kopierar data till filsystemet, ange den **mottagare typ** av kopieringsaktiviteten till **FileSystemSink**, och ange följande egenskaper i den **mottagare** avsnittet:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| copyBehavior |Definierar kopieringsbeteendet när källan är BlobSource eller filsystem. |**PreserveHierarchy:** Bevarar filen hierarkin i målmappen. Den relativa sökvägen på källfilen för målmappen är samma som den relativa sökvägen till målfilen till målmappen.<br/><br/>**FlattenHierarchy:** Alla filer från källmappen skapas i den första nivån i målmappen. Målfiler som skapas med en automatiskt genererade namnet.<br/><br/>**MergeFiles:** Slår samman alla filer från källmappen till en fil. Om namnet på namn-/ blob anges är sammanfogade filnamnet det angivna namnet. I annat fall är det en automatiskt genererad filnamn. |Nej |
automaticky

#### <a name="example"></a>Exempel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T20:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoOnPremisesFile",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "OnpremisesFileSystemOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "FileSystemSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 3,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Mer information finns i [filsystem connector artikeln](data-factory-onprem-file-system-connector.md#copy-activity-properties).

## <a name="ftp"></a>FTP

### <a name="linked-service"></a>Länkad tjänst
För att definiera en FTP länkad tjänst genom att ange den **typ** på den länkade tjänsten till **FtpServer**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs | Standard |
| --- | --- | --- | --- |
| värd |Namn eller IP-adressen för FTP-servern |Ja |&nbsp; |
| authenticationType |Ange autentiseringstyp |Ja |Basic-, anonym |
| användarnamn |Användare som har åtkomst till FTP-servern |Nej |&nbsp; |
| lösenord |Lösenord för användaren (användarnamn) |Nej |&nbsp; |
| encryptedCredential |Krypterade autentiseringsuppgifter för åtkomst till FTP-servern |Nej |&nbsp; |
| gatewayName |Namnet på Data Management Gateway att ansluta till en lokal FTP-server |Nej |&nbsp; |
| port |Porten som FTP-servern lyssnar på. |Nej |21 |
| enableSsl |Ange om du använder FTP över SSL/TLS-kanal |Nej |true |
| enableServerCertificateValidation |Ange om du vill aktivera serververifiering SSL-certifikat när du använder FTP över SSL/TLS-kanal |Nej |true |

#### <a name="example-using-anonymous-authentication"></a>Exempel: Använder anonym autentisering

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
            "typeProperties": {
            "authenticationType": "Anonymous",
            "host": "myftpserver.com"
        }
    }
}
```

#### <a name="example-using-username-and-password-in-plain-text-for-basic-authentication"></a>Exempel: Med användarnamn och lösenord i klartext för grundläggande autentisering

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```

#### <a name="example-using-port-enablessl-enableservercertificatevalidation"></a>Exempel: Med port, enableSsl enableServerCertificateValidation

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

#### <a name="example-using-encryptedcredential-for-authentication-and-gateway"></a>Exempel: Använder encryptedCredential för autentisering och gateway

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
      }
}
```

Mer information finns i [FTP-anslutningsappen](data-factory-ftp-connector.md#linked-service-properties) artikeln.

### <a name="dataset"></a>Datauppsättning
För att definiera en FTP-datauppsättning, ange den **typ** på datauppsättningen till **FileShare**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Sub sökvägen till mappen. Använd escape-tecknet ”\” för specialtecken i strängen. Se [exempel länkad tjänst-och datauppsättningen](#sample-linked-service-and-dataset-definitions) exempel.<br/><br/>Du kan kombinera den här egenskapen med **partitionBy** ha mappen sökvägarna baserat på sektorn start/slut datum / tid. |Ja
| fileName |Ange namnet på filen i den **folderPath** om du vill att tabellen för att referera till en viss fil i mappen. Om du inte anger något värde för den här egenskapen, tabellen pekar på alla filer i mappen.<br/><br/>När filnamn har angetts för en utdatauppsättning, namnet på den genererade filen vara i följande det här formatet: <br/><br/>`Data.<Guid>.txt` (Exempel: Data.0a405f8a-93ff-4C6F-B3BE-f69616f1df7a.txt) |Nej |
| fileFilter |Ange ett filter som används för att välja en delmängd av filer i folderPath i stället för alla filer.<br/><br/>Tillåtna värden är: `*` (flera tecken) och `?` (tecken).<br/><br/>Exempel 1: `"fileFilter": "*.log"`<br/>Exempel 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter gäller för en indatauppsättning filresursen. Den här egenskapen stöds inte med HDFS. |Nej |
| partitionedBy |partitionedBy kan användas för att ange en dynamisk folderPath filnamn för time series-data. Till exempel folderPath som innehåller parametrar för varje timme som data. |Nej |
| Format | Följande formattyper av stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange den **typ** egenskapen under format till ett av dessa värden. Mer information finns i [textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-formatet](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc-Format](data-factory-supported-file-and-compression-formats.md#orc-format), och [Parquet-Format](data-factory-supported-file-and-compression-formats.md#parquet-format) avsnitt. <br><br> Om du vill **kopiera filer som – är** hoppa över avsnittet format i både inkommande och utgående datamängd definitioner mellan filbaserade (binär kopia). |Nej |
| Komprimering | Ange typ och komprimeringsnivå för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**; och nivåer som stöds är: **Optimal** och **snabbaste**. Mer information finns i [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nej |
| useBinaryTransfer |Ange om använda binär överföringsläge. Gäller för binärt läge och FALSKT ASCII. Standardvärde: SANT. Den här egenskapen kan bara användas när tillhörande länkade tjänsttypen är av typen: FtpServer. |Nej |

> [!NOTE]
> filnamn och fileFilter kan inte användas samtidigt.

#### <a name="example"></a>Exempel

```json
{
    "name": "FTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "FTPLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv",
            "useBinaryTransfer": true
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Mer information finns i [FTP-anslutningsappen](data-factory-ftp-connector.md#dataset-properties) artikeln.

### <a name="file-system-source-in-copy-activity"></a>Filkälla System i Kopieringsaktiviteten
Om du kopierar data från en FTP-server, ange den **källtyp** av kopieringsaktiviteten till **FileSystemSource**, och ange följande egenskaper i den **källa** avsnittet:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| rekursiv |Anger om data läses rekursivt från undermappar eller endast från den angivna mappen. |SANT, FALSKT (standard) |Nej |

#### <a name="example"></a>Exempel

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00",
        "end": "2016-08-24T19:00:00"
    }
}
```

Mer information finns i [FTP-anslutningsappen](data-factory-ftp-connector.md#copy-activity-properties) artikeln.


## <a name="hdfs"></a>HDFS

### <a name="linked-service"></a>Länkad tjänst
För att definiera en HDFS länkad tjänst genom att ange den **typ** på den länkade tjänsten till **Hdfs**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Type-egenskapen måste anges till: **Hdfs** |Ja |
| URL |URL: en med HDFS |Ja |
| authenticationType |Anonym, eller Windows. <br><br> Att använda **Kerberos-autentisering** HDFS-anslutningstjänsten finns i [i det här avsnittet](#use-kerberos-authentication-for-hdfs-connector) att ställa in din lokala miljö på lämpligt sätt. |Ja |
| Användarnamn |Användarnamn för Windows-autentisering. |Ja (för Windows-autentisering) |
| lösenord |Lösenordet för Windows-autentisering. |Ja (för Windows-autentisering) |
| gatewayName |Namnet på den gateway som Data Factory-tjänsten ska använda för att ansluta till med HDFS. |Ja |
| encryptedCredential |[Ny AzureRMDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/azurerm.datafactories/new-azurermdatafactoryencryptvalue) utdata för åtkomst-autentiseringsuppgift. |Nej |

#### <a name="example-using-anonymous-authentication"></a>Exempel: Använder anonym autentisering

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-windows-authentication"></a>Exempel: Med hjälp av Windows-autentisering

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Mer information finns i [HDFS connector](#data-factory-hdfs-connector.md#linked-service-properties) artikeln.

### <a name="dataset"></a>Datauppsättning
För att definiera en HDFS-datauppsättning, ange den **typ** på datauppsättningen till **FileShare**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Sökvägen till mappen. Exempel: `myfolder`<br/><br/>Använd escape-tecknet ”\” för specialtecken i strängen. Till exempel: Ange mapp för folder\subfolder,\\\\undermapp och d:\samplefolder, ange d:\\\\Exempelmapp.<br/><br/>Du kan kombinera den här egenskapen med **partitionBy** ha mappen sökvägarna baserat på sektorn start/slut datum / tid. |Ja |
| fileName |Ange namnet på filen i den **folderPath** om du vill att tabellen för att referera till en viss fil i mappen. Om du inte anger något värde för den här egenskapen, tabellen pekar på alla filer i mappen.<br/><br/>När filnamn har angetts för en utdatauppsättning, namnet på den genererade filen vara i följande det här formatet: <br/><br/>Data. <Guid>.txt (exempel:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nej |
| partitionedBy |partitionedBy kan användas för att ange en dynamisk folderPath filnamn för time series-data. Exempel: folderPath innehåller parametrar för varje timme som data. |Nej |
| Format | Följande formattyper av stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange den **typ** egenskapen under format till ett av dessa värden. Mer information finns i [textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-formatet](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc-Format](data-factory-supported-file-and-compression-formats.md#orc-format), och [Parquet-Format](data-factory-supported-file-and-compression-formats.md#parquet-format) avsnitt. <br><br> Om du vill **kopiera filer som – är** hoppa över avsnittet format i både inkommande och utgående datamängd definitioner mellan filbaserade (binär kopia). |Nej |
| Komprimering | Ange typ och komprimeringsnivå för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**. Nivåer som stöds är: **Optimal** och **snabbaste**. Mer information finns i [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nej |

> [!NOTE]
> filnamn och fileFilter kan inte användas samtidigt.

#### <a name="example"></a>Exempel

```json
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Mer information finns i [HDFS connector](#data-factory-hdfs-connector.md#dataset-properties) artikeln.

### <a name="file-system-source-in-copy-activity"></a>Filkälla System i Kopieringsaktiviteten
Om du kopierar data från HDFS, ange den **källtyp** av kopieringsaktiviteten till **FileSystemSource**, och ange följande egenskaper i den **källa** avsnittet:

**FileSystemSource** har stöd för följande egenskaper:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| rekursiv |Anger om data läses rekursivt från undermappar eller endast från den angivna mappen. |SANT, FALSKT (standard) |Nej |

#### <a name="example"></a>Exempel

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "HdfsToBlobCopy",
            "inputs": [{
                "name": "InputDataset"
            }],
            "outputs": [{
                "name": "OutputDataset"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Mer information finns i [HDFS connector](#data-factory-hdfs-connector.md#copy-activity-properties) artikeln.

## <a name="sftp"></a>SFTP


### <a name="linked-service"></a>Länkad tjänst
För att definiera en SFTP länkad tjänst genom att ange den **typ** på den länkade tjänsten till **Sftp**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- | --- |
| värd | Namn eller IP-adressen för SFTP-servern. |Ja |
| port |Porten som SFTP-servern lyssnar. Standardvärdet är: 21 |Nej |
| authenticationType |Ange autentiseringstyp. Tillåtna värden: **Grundläggande**, **SshPublicKey**. <br><br> Referera till [använder grundläggande autentisering](#using-basic-authentication) och [med hjälp av SSH autentisering med offentlig nyckel](#using-ssh-public-key-authentication) respektive avsnitt på fler egenskaper och JSON-exempel. |Ja |
| skipHostKeyValidation | Ange om du vill hoppa över nyckelvalidering för värden. | Nej. Standard: FALSKT |
| ska hostKeyFingerprint | Ange fingeravtryck av serverns värdnyckel. | Ja om den `skipHostKeyValidation` är inställd på false.  |
| gatewayName |Namnet på Data Management Gateway att ansluta till en lokal SFTP-server. | Ja om du kopierar data från en lokal SFTP-server. |
| encryptedCredential | Krypterade autentiseringsuppgifter för åtkomst till SFTP-servern. Genereras automatiskt när du anger grundläggande autentisering (användarnamn och lösenord) eller SshPublicKey autentisering (användarnamn + privata Nyckelsökväg eller innehåll) i Kopieringsguiden eller ClickOnce popup-dialogruta. | Nej. Gäller endast när du kopierar data från en lokal SFTP-server. |

#### <a name="example-using-basic-authentication"></a>Exempel: Använder grundläggande autentisering

Om du vill använda grundläggande autentisering, ange `authenticationType` som `Basic`, och ange följande egenskaper förutom SFTP-anslutningsappen Allmänt som introducerades i det sista avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- | --- |
| användarnamn | Användare som har åtkomst till SFTP-servern. |Ja |
| lösenord | Lösenordet för användaren (användarnamn). | Ja |

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<SFTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Exempel: **Grundläggande autentisering med krypterade autentiseringsuppgifter**

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="using-ssh-public-key-authentication"></a>**Med hjälp av SSH-autentisering för offentlig nyckel:**

Om du vill använda grundläggande autentisering, ange `authenticationType` som `SshPublicKey`, och ange följande egenskaper förutom SFTP-anslutningsappen Allmänt som introducerades i det sista avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- | --- |
| användarnamn |Användare som har åtkomst till SFTP-server |Ja |
| privateKeyPath | Ange absolut sökväg till filen för privat nyckel som gatewayen kan komma åt. | Ange antingen den `privateKeyPath` eller `privateKeyContent`. <br><br> Gäller endast när du kopierar data från en lokal SFTP-server. |
| privateKeyContent | En serialiserade sträng med privat nyckel innehållet. Kopieringsguiden kan läsa filen för privat nyckel och extrahera det privata nyckeln innehållet automatiskt. Om du använder några andra verktyg/SDK kan du använda egenskapen privateKeyPath i stället. | Ange antingen den `privateKeyPath` eller `privateKeyContent`. |
| Lösenfras | Ange pass frasen/lösenord för att dekryptera den privata nyckeln om nyckelfilen skyddas av en godkänd fras. | Ja om filen för privata nyckeln skyddas av en godkänd fras. |

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Exempel: **SshPublicKey autentisering med hjälp av privat nyckel innehåll**

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

Mer information finns i [SFTP-anslutningsappen](data-factory-sftp-connector.md#linked-service-properties) artikeln.

### <a name="dataset"></a>Datauppsättning
För att definiera en SFTP-datauppsättning, ange den **typ** på datauppsättningen till **FileShare**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Sub sökvägen till mappen. Använd escape-tecknet ”\” för specialtecken i strängen. Se [exempel länkad tjänst-och datauppsättningen](#sample-linked-service-and-dataset-definitions) exempel.<br/><br/>Du kan kombinera den här egenskapen med **partitionBy** ha mappen sökvägarna baserat på sektorn start/slut datum / tid. |Ja |
| fileName |Ange namnet på filen i den **folderPath** om du vill att tabellen för att referera till en viss fil i mappen. Om du inte anger något värde för den här egenskapen, tabellen pekar på alla filer i mappen.<br/><br/>När filnamn har angetts för en utdatauppsättning, namnet på den genererade filen vara i följande det här formatet: <br/><br/>`Data.<Guid>.txt` (Exempel: Data.0a405f8a-93ff-4C6F-B3BE-f69616f1df7a.txt) |Nej |
| fileFilter |Ange ett filter som används för att välja en delmängd av filer i folderPath i stället för alla filer.<br/><br/>Tillåtna värden är: `*` (flera tecken) och `?` (tecken).<br/><br/>Exempel 1: `"fileFilter": "*.log"`<br/>Exempel 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter gäller för en indatauppsättning filresursen. Den här egenskapen stöds inte med HDFS. |Nej |
| partitionedBy |partitionedBy kan användas för att ange en dynamisk folderPath filnamn för time series-data. Till exempel folderPath som innehåller parametrar för varje timme som data. |Nej |
| Format | Följande formattyper av stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange den **typ** egenskapen under format till ett av dessa värden. Mer information finns i [textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-formatet](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc-Format](data-factory-supported-file-and-compression-formats.md#orc-format), och [Parquet-Format](data-factory-supported-file-and-compression-formats.md#parquet-format) avsnitt. <br><br> Om du vill **kopiera filer som – är** hoppa över avsnittet format i både inkommande och utgående datamängd definitioner mellan filbaserade (binär kopia). |Nej |
| Komprimering | Ange typ och komprimeringsnivå för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**. Nivåer som stöds är: **Optimal** och **snabbaste**. Mer information finns i [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nej |
| useBinaryTransfer |Ange om använda binär överföringsläge. Gäller för binärt läge och FALSKT ASCII. Standardvärde: SANT. Den här egenskapen kan bara användas när tillhörande länkade tjänsttypen är av typen: FtpServer. |Nej |

> [!NOTE]
> filnamn och fileFilter kan inte användas samtidigt.

#### <a name="example"></a>Exempel

```json
{
    "name": "SFTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "SftpLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Mer information finns i [SFTP-anslutningsappen](data-factory-sftp-connector.md#dataset-properties) artikeln.

### <a name="file-system-source-in-copy-activity"></a>Filkälla System i Kopieringsaktiviteten
Om du kopierar data från en SFTP-källa, ange den **källtyp** av kopieringsaktiviteten till **FileSystemSource**, och ange följande egenskaper i den **källa** avsnittet:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| rekursiv |Anger om data läses rekursivt från undermappar eller endast från den angivna mappen. |SANT, FALSKT (standard) |Nej |



#### <a name="example"></a>Exempel

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2017-02-20T18:00:00",
        "end": "2017-02-20T19:00:00"
    }
}
```

Mer information finns i [SFTP-anslutningsappen](data-factory-sftp-connector.md#copy-activity-properties) artikeln.


## <a name="http"></a>HTTP

### <a name="linked-service"></a>Länkad tjänst
För att definiera en HTTP länkad tjänst genom att ange den **typ** på den länkade tjänsten till **Http**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| url | Grundläggande URL: en till webbservern | Ja |
| authenticationType | Anger autentiseringstypen. Tillåtna värden är: **Anonym**, **grundläggande**, **sammanfattad**, **Windows**, **ClientCertificate**. <br><br> Avse respektive avsnitt under den här tabellen på fler egenskaper och JSON-exempel för dessa typer av autentisering. | Ja |
| enableServerCertificateValidation | Ange om du vill aktivera server SSL-certifikatsverifiering om källan är HTTPS-webbserver | Nej, standard är SANT |
| gatewayName | Namnet på Data Management Gateway att ansluta till en lokal HTTP-källa. | Ja om du kopierar data från en lokal HTTP-källa. |
| encryptedCredential | Krypterade autentiseringsuppgifter till HTTP-slutpunkt. Genereras automatiskt när du konfigurerar autentiseringsinformation i Kopieringsguiden eller ClickOnce popup-dialogruta. | Nej. Gäller endast när du kopierar data från en lokal HTTP-server. |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Exempel: Med hjälp av grundläggande, sammanfattad eller Windows-autentisering
Ange `authenticationType` som `Basic`, `Digest`, eller `Windows`, och ange följande egenskaper utöver HTTP-anslutningsappen Allmänt som introduceras ovan:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| användarnamn | Användarnamnet för att få åtkomst till HTTP-slutpunkt. | Ja |
| lösenord | Lösenordet för användaren (användarnamn). | Ja |

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "basic",
            "url": "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

#### <a name="example-using-clientcertificate-authentication"></a>Exempel: Med hjälp av ClientCertificate autentisering

Om du vill använda grundläggande autentisering, ange `authenticationType` som `ClientCertificate`, och ange följande egenskaper utöver HTTP-anslutningsappen Allmänt som introduceras ovan:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| embeddedCertData | Base64-kodade innehåll binära data i filen Personal Information Exchange (PFX). | Ange antingen den `embeddedCertData` eller `certThumbprint`. |
| certThumbprint | Tumavtryck för certifikatet som installerades på gateway-datorns certifikatarkiv. Gäller endast när du kopierar data från en lokal HTTP-källa. | Ange antingen den `embeddedCertData` eller `certThumbprint`. |
| lösenord | Lösenordet som är associerat med certifikatet. | Nej |

Om du använder `certThumbprint` för autentisering och certifikatet är installerat i det personliga arkivet i den lokala datorn kan du behöva ge läsbehörighet till gateway-tjänsten:

1. Starta Microsoft Management Console (MMC). Lägg till den **certifikat** snapin-modul som riktar sig mot den **lokala**.
2. Expandera **certifikat**, **personliga**, och klicka på **certifikat**.
3. Högerklicka på certifikatet från det personliga arkivet och välj **alla uppgifter**->**hantera privata nycklar...**
3. På den **Security** fliken, lägga till användarkontot där Data Management Gateway-värdtjänsten körs med läsbehörighet till certifikatet.

**Exempel: använder klientcertifikat:** Den här länkade tjänsten länkar din data factory på en lokal webbserver för HTTP. Den använder ett klientcertifikat som är installerad på datorn med Data Management Gateway installerad.

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"
        }
    }
}
```

#### <a name="example-using-client-certificate-in-a-file"></a>Exempel: använder klientcertifikat i en fil
Den här länkade tjänsten länkar din data factory på en lokal webbserver för HTTP. Det använder en klientcertifikatfilen på datorn med Data Management Gateway installerad.

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "base64 encoded cert data",
            "password": "password of cert"
        }
    }
}
```

Mer information finns i [HTTP-anslutningsappen](data-factory-http-connector.md#linked-service-properties) artikeln.

### <a name="dataset"></a>Datauppsättning
För att definiera en HTTP-datauppsättning, ange den **typ** på datauppsättningen till **Http**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| relativeUrl | En relativ URL till den resurs som innehåller data. Om sökvägen inte anges används bara den URL som anges i länkade tjänstedefinition. <br><br> Du kan använda för att skapa dynamiska URL [Data Factory-funktioner och systemvariabler](data-factory-functions-variables.md), exempel: `"relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)"`. | Nej |
| requestMethod | HTTP-metoden. Tillåtna värden är **hämta** eller **POST**. | Nej. Standardvärdet är `GET`. |
| additionalHeaders | Ytterligare rubriker för HTTP-begäran. | Nej |
| RequestBody | Brödtexten för HTTP-begäran. | Nej |
| Format | Om du vill bara **hämta data från HTTP-slutpunkt som – är** utan att parsa den hoppar du över det här formatinställningar. <br><br> Om du vill parsa HTTP-svarsinnehåll vid kopiering stöds följande formattyper av: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Mer information finns i [textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-formatet](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc-Format](data-factory-supported-file-and-compression-formats.md#orc-format), och [Parquet-Format](data-factory-supported-file-and-compression-formats.md#parquet-format) avsnitt. |Nej |
| Komprimering | Ange typ och komprimeringsnivå för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**. Nivåer som stöds är: **Optimal** och **snabbaste**. Mer information finns i [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nej |

#### <a name="example-using-the-get-default-method"></a>Exempel: med hjälp av metoden GET (standard)

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

#### <a name="example-using-the-post-method"></a>Exempel: med POST-metoden

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
            "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```
Mer information finns i [HTTP-anslutningsappen](data-factory-http-connector.md#dataset-properties) artikeln.

### <a name="http-source-in-copy-activity"></a>HTTP-källan i Kopieringsaktiviteten
Om du kopierar data från en HTTP-källa, ange den **källtyp** av kopieringsaktiviteten till **HttpSource**, och ange följande egenskaper i den **källa** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| httpRequestTimeout | Tidsgräns (TimeSpan) för HTTP-begäran att få svar. Tidsgränsen är det för att få svar timeout inte att läsa svarsdata. | Nej. Standardvärde: 00:01:40 |


#### <a name="example"></a>Exempel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "HttpSourceToAzureBlob",
            "description": "Copy from an HTTP source to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "HttpSourceDataInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "HttpSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Mer information finns i [HTTP-anslutningsappen](data-factory-http-connector.md#copy-activity-properties) artikeln.

## <a name="odata"></a>OData

### <a name="linked-service"></a>Länkad tjänst
För att definiera en OData länkad tjänst genom att ange den **typ** på den länkade tjänsten till **OData**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| url |URL för OData-tjänsten. |Ja |
| authenticationType |Typ av autentisering som används för att ansluta till OData-källan. <br/><br/> För molnet OData är möjliga värden anonym, grundläggande och OAuth (Observera att Azure Data Factory för närvarande endast stöder Azure Active Directory-baserad OAuth). <br/><br/> För den lokala OData är möjliga värden anonym, grundläggande och Windows. |Ja |
| användarnamn |Ange användarnamnet om du använder grundläggande autentisering. |Ja (endast om du använder grundläggande autentisering) |
| lösenord |Ange lösenord för det användarkonto som du angav för användarnamnet. |Ja (endast om du använder grundläggande autentisering) |
| authorizedCredential |Om du använder OAuth, klickar du på **auktorisera** i Data Factory-Kopieringsguide eller redigerare och ange dina autentiseringsuppgifter och sedan värdet för den här egenskapen kommer att genereras automatiskt. |Ja (endast om du använder OAuth-autentisering) |
| gatewayName |Namnet på den gateway som Data Factory-tjänsten ska använda för att ansluta till OData-tjänst på plats. Ange endast om du kopierar data från en lokal OData-källan. |Nej |

#### <a name="example---using-basic-authentication"></a>Exempel – använder grundläggande autentisering
```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "http://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

#### <a name="example---using-anonymous-authentication"></a>Exempel – använder anonym autentisering

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "http://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

#### <a name="example---using-windows-authentication-accessing-on-premises-odata-source"></a>Exempel - åtkomst med hjälp av Windows-autentisering på lokala OData-källan

```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of on-premises OData source, for example, Dynamics CRM>",
            "authenticationType": "Windows",
            "username": "domain\\user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example---using-oauth-authentication-accessing-cloud-odata-source"></a>Exempel – med hjälp av OAuth-autentisering som får åtkomst till molnresurser OData-källan
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "<endpoint of cloud OData source, for example, https://<tenant>.crm.dynamics.com/XRMServices/2011/OrganizationData.svc>",
            "authenticationType": "OAuth",
            "authorizedCredential": "<auto generated by clicking the Authorize button on UI>"
        }
    }
}
```

Mer information finns i [OData-anslutningsapp](data-factory-odata-connector.md#linked-service-properties) artikeln.

### <a name="dataset"></a>Datauppsättning
För att definiera en OData-datauppsättning, ange den **typ** på datauppsättningen till **ODataResource**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| sökväg |Sökvägen till OData-resurs |Nej |

#### <a name="example"></a>Exempel

```json
{
    "name": "ODataDataset",
    "properties": {
        "type": "ODataResource",
        "typeProperties": {
            "path": "Products"
        },
        "linkedServiceName": "ODataLinkedService",
        "structure": [],
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
        }
    }
}
```

Mer information finns i [OData-anslutningsapp](data-factory-odata-connector.md#dataset-properties) artikeln.

### <a name="relational-source-in-copy-activity"></a>Relationskälla i Kopieringsaktiviteten
Om du kopierar data från en OData-källan, ange den **källtyp** av kopieringsaktiviteten till **RelationalSource**, och ange följande egenskaper i den **källa** avsnittet:

| Egenskap  | Beskrivning | Exempel | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd anpassad fråga för att läsa data. |”? $select = namn, beskrivning och $top = 5” |Nej |

#### <a name="example"></a>Exempel

```json
{
    "name": "CopyODataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "?$select=Name, Description&$top=5"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "ODataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobODataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "ODataToBlob"
        }],
        "start": "2017-02-01T18:00:00",
        "end": "2017-02-03T19:00:00"
    }
}
```

Mer information finns i [OData-anslutningsapp](data-factory-odata-connector.md#copy-activity-properties) artikeln.


## <a name="odbc"></a>ODBC


### <a name="linked-service"></a>Länkad tjänst
För att definiera en ODBC länkad tjänst genom att ange den **typ** på den länkade tjänsten till **OnPremisesOdbc**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| connectionString |Den icke-autentiseringsuppgifter delen av anslutningssträngen och en valfri krypterade autentiseringsuppgifter. Se exemplen i följande avsnitt. |Ja |
| credential |Åtkomst till autentiseringsuppgifter delen av anslutningssträngen som angetts i drivrutinsspecifika egenskapsvärdet format. Exempel: `“Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;”.` |Nej |
| authenticationType |Typ av autentisering som används för att ansluta till ODBC-datalager. Möjliga värden: Anonym och grundläggande. |Ja |
| användarnamn |Ange användarnamnet om du använder grundläggande autentisering. |Nej |
| lösenord |Ange lösenord för det användarkonto som du angav för användarnamnet. |Nej |
| gatewayName |Namnet på den gateway som Data Factory-tjänsten ska använda för att ansluta till ODBC-datalager. |Ja |

#### <a name="example---using-basic-authentication"></a>Exempel – använder grundläggande autentisering

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```
#### <a name="example---using-basic-authentication-with-encrypted-credentials"></a>Exempel – med grundläggande autentisering och krypterade autentiseringsuppgifter
Du kan kryptera autentiseringsuppgifterna med den [New-AzureRMDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/azurerm.datafactories/new-azurermdatafactoryencryptvalue) (version 1.0 av Azure PowerShell) cmdlet eller [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/dn834940.aspx) (0,9 eller tidigare version av Azure PowerShell).

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-anonymous-authentication"></a>Exempel: Använder anonym autentisering

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
            "credential": "UID={uid};PWD={pwd}",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Mer information finns i [ODBC-anslutningsprogram](data-factory-odbc-connector.md#linked-service-properties) artikeln.

### <a name="dataset"></a>Datauppsättning
För att definiera en ODBC-datauppsättning, ange den **typ** på datauppsättningen till **RelationalTable**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i ODBC-datalager. |Ja |


#### <a name="example"></a>Exempel

```json
{
    "name": "ODBCDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "ODBCLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Mer information finns i [ODBC-anslutningsprogram](data-factory-odbc-connector.md#dataset-properties) artikeln.

### <a name="relational-source-in-copy-activity"></a>Relationskälla i Kopieringsaktiviteten
Om du kopierar data från ett ODBC-datalager, ange den **källtyp** av kopieringsaktiviteten till **RelationalSource**, och ange följande egenskaper i den **källa** avsnittet :

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd anpassad fråga för att läsa data. |SQL-sträng. Till exempel: `select * from MyTable`. |Ja |

#### <a name="example"></a>Exempel

```json
{
    "name": "CopyODBCToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "OdbcDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobOdbcDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "OdbcToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Mer information finns i [ODBC-anslutningsprogram](data-factory-odbc-connector.md#copy-activity-properties) artikeln.

## <a name="salesforce"></a>Salesforce


### <a name="linked-service"></a>Länkad tjänst
För att definiera ett Salesforce länkad tjänst genom att ange den **typ** på den länkade tjänsten till **Salesforce**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| environmentUrl | Ange URL: en för Salesforce-instans. <br><br> – Standardvärdet är ”https://login.salesforce.com”. <br> – Om du vill kopiera data från sandbox, ange ”https://test.salesforce.com”. <br> – Om du vill kopiera data från anpassad domän, ange, till exempel ”https://[domain].my.salesforce.com”. |Nej |
| användarnamn |Ange ett användarnamn för användarkontot. |Ja |
| lösenord |Ange ett lösenord för användarkontot. |Ja |
| securityToken |Ange en säkerhetstoken för användarkontot. Se [hämta säkerhetstoken för](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) för instruktioner om hur du återställning/hämta en säkerhetstoken. Läs mer om säkerhetstoken i allmänhet i [säkerhets- och API: et](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Ja |

#### <a name="example"></a>Exempel

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<user name>",
            "password": "<password>",
            "securityToken": "<security token>"
        }
    }
}
```

Mer information finns i [Salesforce-anslutningsprogrammet](data-factory-salesforce-connector.md#linked-service-properties) artikeln.

### <a name="dataset"></a>Datauppsättning
För att definiera en Salesforce-datauppsättning, ange den **typ** på datauppsättningen till **RelationalTable**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i Salesforce. |Nej (om en **fråga** av **RelationalSource** har angetts) |

#### <a name="example"></a>Exempel

```json
{
    "name": "SalesforceInput",
    "properties": {
        "linkedServiceName": "SalesforceLinkedService",
        "type": "RelationalTable",
        "typeProperties": {
            "tableName": "AllDataType__c"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Mer information finns i [Salesforce-anslutningsprogrammet](data-factory-salesforce-connector.md#dataset-properties) artikeln.

### <a name="relational-source-in-copy-activity"></a>Relationskälla i Kopieringsaktiviteten
Om du kopierar data från Salesforce, ange den **källtyp** av kopieringsaktiviteten till **RelationalSource**, och ange följande egenskaper i den **källa** avsnittet:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd anpassad fråga för att läsa data. |En SQL-92-fråga eller [Salesforce objektet Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) fråga. Till exempel: `select * from MyTable__c`. |Nej (om den **tableName** av den **datauppsättning** har angetts) |

#### <a name="example"></a>Exempel



```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "SalesforceToAzureBlob",
            "description": "Copy from Salesforce to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "SalesforceInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

> [!IMPORTANT]
> Den ”__c”-delen av API-namn krävs för alla anpassade objekt.

Mer information finns i [Salesforce-anslutningsprogrammet](data-factory-salesforce-connector.md#copy-activity-properties) artikeln.

## <a name="web-data"></a>Webbdata

### <a name="linked-service"></a>Länkad tjänst
För att definiera en länkad tjänst genom att ange den **typ** på den länkade tjänsten till **Web**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| URL |URL: en till webbadressen |Ja |
| authenticationType |Anonym. |Ja |


#### <a name="example"></a>Exempel


```json
{
    "name": "web",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "url": "https://en.wikipedia.org/wiki/"
        }
    }
}
```

Mer information finns i [Webbtabell connector](data-factory-web-table-connector.md#linked-service-properties) artikeln.

### <a name="dataset"></a>Datauppsättning
För att definiera en Web-datauppsättning, ange den **typ** på datauppsättningen till **WebTable**, och ange följande egenskaper i den **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Typ av datauppsättningen. måste vara inställt på **WebTable** |Ja |
| sökväg |En relativ URL till den resurs som innehåller tabellen. |Nej. Om sökvägen inte anges används bara den URL som anges i länkade tjänstedefinition. |
| index |Index för tabellen i resursen. Se [Get index för en tabell i en HTML-sida](#get-index-of-a-table-in-an-html-page) avsnittet anvisningar för hur du hämtar index för en tabell i en HTML-sida. |Ja |

#### <a name="example"></a>Exempel

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Mer information finns i [Webbtabell connector](data-factory-web-table-connector.md#dataset-properties) artikeln.

### <a name="web-source-in-copy-activity"></a>Webbadress i Kopieringsaktiviteten
Om du kopierar data från en webbtabell, ange den **källtyp** av kopieringsaktiviteten till **WebSource**. För närvarande när källan i kopieringsaktiviteten är av typen **WebSource**, inga ytterligare egenskaper som stöds.

#### <a name="example"></a>Exempel

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "WebTableToAzureBlob",
            "description": "Copy from a Web table to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "WebTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "WebSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Mer information finns i [Webbtabell connector](data-factory-web-table-connector.md#copy-activity-properties) artikeln.

## <a name="compute-environments"></a>COMPUTE-MILJÖER
I följande tabell visas de beräkningsmiljöer som stöds av Data Factory och transformeringsaktiviteter som kan köras på dem. Klicka på länken för den beräkning som du är intresserad av att se JSON-scheman för den länkade tjänsten att länka den till en data factory.

| Compute-miljö | Aktiviteter |
| --- | --- |
| [HDInsight-kluster på begäran](#on-demand-azure-hdinsight-cluster) eller [ett eget HDInsight-kluster](#existing-azure-hdinsight-cluster) |[.NET-anpassad aktivitet](#net-custom-activity), [Hive-aktivitet](#hdinsight-hive-activity), [Apache Pig-aktivitet](#hdinsight-pig-activity), [MapReduce-aktivitet](#hdinsight-mapreduce-activity), [Hadoop-strömmande aktivitet](#hdinsight-streaming-activityd), [Spark-aktivitet](#hdinsight-spark-activity) |
| [Azure Batch](#azure-batch) |[.NET-anpassad aktivitet](#net-custom-activity) |
| [Azure Machine Learning](#azure-machine-learning) | [Machine Learning-Batchkörningsaktivitet](#machine-learning-batch-execution-activity), [Machine Learning-Uppdateringsresursaktivitet](#machine-learning-update-resource-activity) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics) |[Data Lake Analytics U-SQL](#data-lake-analytics-u-sql-activity) |
| [Azure SQL Database](#azure-sql-database-1), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-1), [SQLServer](#sql-server-1) |[Lagrad procedur](#stored-procedure-activity) |

## <a name="on-demand-azure-hdinsight-cluster"></a>Azure HDInsight-kluster på begäran
Azure Data Factory-tjänsten kan automatiskt skapa en Windows/Linux-baserat HDInsight-kluster på begäran att bearbeta data. Klustret skapas i samma region som lagringskontot (linkedServiceName-egenskapen i JSON) som är associerade med klustret. Du kan köra följande transformeringsaktiviteter på den här länkade tjänsten: [.NET-anpassad aktivitet](#net-custom-activity), [Hive-aktivitet](#hdinsight-hive-activity), [Apache Pig-aktivitet](#hdinsight-pig-activity), [MapReduce aktiviteten](#hdinsight-mapreduce-activity), [Hadoop-strömmande aktivitet](#hdinsight-streaming-activityd), [Spark-aktivitet](#hdinsight-spark-activity).

### <a name="linked-service"></a>Länkad tjänst
Följande tabell innehåller beskrivningar av de egenskaper som används i Azure JSON-definition för en på begäran länkad HDInsight-tjänst.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Type-egenskapen ska anges till **HDInsightOnDemand**. |Ja |
| clusterSize |Antal worker/data noder i klustret. HDInsight-klustret har skapats med 2 huvudnoder tillsammans med antalet arbetsnoder som du anger för den här egenskapen. Noderna är storlek Standard_D3 med 4 kärnor, så att ett kluster med noder 4 worker tar 24 kärnor (4\*4 = 16 kärnor för arbetsnoder plus 2\*4 = 8 kärnor för huvudnoder). Se [skapa Linux-baserade Hadoop-kluster i HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) mer information om Standard_D3-nivå. |Ja |
| TimeToLive |Tillåtna inaktivitetstiden för HDInsight-kluster på begäran. Anger hur länge HDInsight-kluster på begäran förblir aktiv efter det att en aktivitet som körs om det finns inga aktiva jobb i klustret.<br/><br/>Till exempel om en aktivitet kör tar 6 minuter och timetolive är inställd på 5 minuter, kvar klustret alive under 5 minuter efter 6 minuter bearbeta aktiviteten körs. Om en annan aktivitet som kör körs med fönstret 6 minuter, bearbetas den av samma kluster.<br/><br/>Det är en kostsam åtgärd (kan ta en stund), så Använd den här inställningen som krävs för att förbättra prestanda för en data factory genom att återanvända ett HDInsight-kluster på begäran att skapa ett HDInsight-kluster på begäran.<br/><br/>Om du ställer in timetolive-värdet till 0 tas klustret bort när aktiviteten körs bearbetade. Å andra sidan, om du anger ett högt värde för kan klustret Håll inaktiv onödigt lett till höga kostnader. Det är därför viktigt att du ställer in lämpligt värde utifrån dina behov.<br/><br/>Flera pipelines kan dela samma instans av HDInsight-kluster på begäran om timetolive egenskapens värde har angetts på rätt sätt |Ja |
| version |Versionen av HDInsight-klustret. Mer information finns i [HDInsight-versioner som stöds i Azure Data Factory](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory). |Nej |
| linkedServiceName |Länkad Azure Storage-tjänst som ska användas av klustret på begäran för att lagra och bearbeta data. <p>För närvarande kan skapa du inte en på begäran HDInsight-kluster som använder en Azure Data Lake Store som lagring. Om du vill lagra Resultatdata från HDInsight i ett Azure Data Lake Store, kan du använda en Kopieringsaktivitet för att kopiera data från Azure Blob Storage till Azure Data Lake Store.</p>  | Ja |
| additionalLinkedServiceNames |Anger ytterligare lagringskonton för HDInsight-länkad tjänst så att Data Factory-tjänsten kan registrera dem för din räkning. |Nej |
| osType |Typ av operativsystem. Tillåtna värden är: Windows (standard) och Linux |Nej |
| hcatalogLinkedServiceName |Namnet på Azure SQL länkade tjänst som pekar på HCatalog-databasen. HDInsight-kluster på begäran skapas med hjälp av Azure SQL-databas som metaarkiv. |Nej |

### <a name="json-example"></a>JSON-exempel
Följande JSON definierar en Linux-baserade på begäran HDInsight-länkad tjänst. Tjänsten Data Factory skapar automatiskt en **Linux-baserade** vid bearbetning av en datasektor HDInsight-kluster.

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

Mer information finns i [länkade tjänster för Compute](data-factory-compute-linked-services.md) artikeln.

## <a name="existing-azure-hdinsight-cluster"></a>Befintligt Azure HDInsight-kluster
Du kan skapa en Azure HDInsight-länkad tjänst för att registrera ett eget HDInsight-kluster med Data Factory. Du kan köra följande datatransformeringsaktiviteter på den här länkade tjänsten: [.NET-anpassad aktivitet](#net-custom-activity), [Hive-aktivitet](#hdinsight-hive-activity), [Apache Pig-aktivitet](#hdinsight-pig-activity), [ MapReduce-aktivitet](#hdinsight-mapreduce-activity), [Hadoop-strömmande aktivitet](#hdinsight-streaming-activityd), [Spark-aktivitet](#hdinsight-spark-activity).

### <a name="linked-service"></a>Länkad tjänst
Följande tabell innehåller beskrivningar av de egenskaper som används i Azure JSON-definition för en Azure HDInsight-länkad tjänst.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Type-egenskapen ska anges till **HDInsight**. |Ja |
| clusterUri |URI för HDInsight-klustret. |Ja |
| användarnamn |Ange namnet på användaren som ska användas för att ansluta till ett befintligt HDInsight-kluster. |Ja |
| lösenord |Ange lösenordet för användarkontot. |Ja |
| linkedServiceName | Namnet på den länkade Azure Storage-tjänst som refererar till Azure blob-lagring som används av HDInsight-klustret. <p>För närvarande kan ange du inte en Azure Data Lake Store-länkad tjänst för den här egenskapen. Du kan komma åt data i Azure Data Lake Store från Hive/Pig-skript om HDInsight-klustret har åtkomst till Data Lake Store. </p>  |Ja |

Versioner av HDInsight-kluster som stöds finns i [HDInsight-versioner som stöds](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory).

#### <a name="json-example"></a>JSON-exempel

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
            "userName": "admin",
            "password": "<password>",
            "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

## <a name="azure-batch"></a>Azure Batch
Du kan skapa en Azure Batch-länkad tjänst för att registrera en Batch-pool med virtuella datorer (VM) med en data factory. Du kan köra .NET anpassade aktiviteter med hjälp av Azure Batch eller Azure HDInsight. Du kan köra en [.NET-anpassad aktivitet](#net-custom-activity) på den här länkade tjänsten.

### <a name="linked-service"></a>Länkad tjänst
Följande tabell innehåller beskrivningar av de egenskaper som används i Azure JSON-definition för en Azure Batch-länkad tjänst.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Type-egenskapen ska anges till **AzureBatch**. |Ja |
| Kontonamn |Namnet på Azure Batch-kontot. |Ja |
| accessKey |Åtkomstnyckel för Azure Batch-kontot. |Ja |
| Poolnamn |Namnet på poolen med virtuella datorer. |Ja |
| linkedServiceName |Namnet på Azure Storage länkade tjänsten som är associerad med den här Azure Batch-länkade tjänsten. Den här länkade tjänsten används för tillfälliga filer som krävs för att köra aktiviteten och lagra aktivitetsloggar för körning. |Ja |


#### <a name="json-example"></a>JSON-exempel

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "<Azure Batch account name>",
            "accessKey": "<Azure Batch account key>",
            "poolName": "<Azure Batch pool name>",
            "linkedServiceName": "<Specify associated storage linked service reference here>"
        }
    }
}
```

## <a name="azure-machine-learning"></a>Azure Machine Learning
Skapar du en Azure Machine Learning-länkad tjänst för att registrera en Machine Learning batch bedömningsslutpunkten med en data factory. Två datatransformeringsaktiviteter som kan köras på den här länkade tjänsten: [Machine Learning-Batchkörningsaktivitet](#machine-learning-batch-execution-activity), [Machine Learning-Uppdateringsresursaktivitet](#machine-learning-update-resource-activity).

### <a name="linked-service"></a>Länkad tjänst
Följande tabell innehåller beskrivningar av de egenskaper som används i Azure JSON-definition för en Azure Machine Learning-länkad tjänst.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| Typ |Type-egenskapen ska anges till: **AzureML**. |Ja |
| mlEndpoint |Batchbedömnings-URL: en. |Ja |
| ApiKey |Den publicerade arbetsytemodellens API. |Ja |

#### <a name="json-example"></a>JSON-exempel

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://[batch scoring endpoint]/jobs",
            "apiKey": "<apikey>"
        }
    }
}
```

## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
Du skapar en **Azure Data Lake Analytics** länkade tjänst som länkar ett Azure Data Lake Analytics beräkningstjänst till en Azure data factory innan du använder den [Data Lake Analytics U-SQL-aktivitet](data-factory-usql-activity.md) i en pipeline.

### <a name="linked-service"></a>Länkad tjänst

Följande tabell innehåller beskrivningar av de egenskaper som används i JSON-definition för en länkad Azure Data Lake Analytics-tjänst.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| Typ |Type-egenskapen ska anges till: **AzureDataLakeAnalytics**. |Ja |
| Kontonamn |Azure Data Lake Analytics-kontonamn. |Ja |
| dataLakeAnalyticsUri |Azure Data Lake Analytics-URI. |Nej |
| Auktorisering |Auktoriseringskoden hämtas automatiskt när du klickar på **auktorisera** knappen i Data Factory-redigeraren och slutfört OAuth-inloggningen. |Ja |
| subscriptionId |Azure-prenumerations-id |Nej (om inte anges i data factory-prenumeration används). |
| resourceGroupName |Azure-resursgruppsnamn |Nej (om inte anges resursgruppen för data factory används). |
| sessions-ID |sessions-id från OAuth-auktorisering sessionen. Varje sessions-id är unikt och får endast användas en gång. När du använder Data Factory-redigeraren är detta ID automatiskt genererad. |Ja |


#### <a name="json-example"></a>JSON-exempel
I följande exempel innehåller JSON-definition för en länkad Azure Data Lake Analytics-tjänst.

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>",
            "subscriptionId": "<subscription id>",
            "resourceGroupName": "<resource group name>"
        }
    }
}
```

## <a name="azure-sql-database"></a>Azure SQL Database
Du skapar en länkad Azure SQL-tjänst och använda det med den [lagrade Proceduraktiviteten](#stored-procedure-activity) att anropa en lagrad procedur från Data Factory-pipeline.

### <a name="linked-service"></a>Länkad tjänst
För att definiera en Azure SQL Database länkad tjänst genom att ange den **typ** på den länkade tjänsten till **AzureSqlDatabase**, och ange följande egenskaper i den **typeProperties** avsnitt:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| connectionString |Ange information som behövs för att ansluta till Azure SQL Database-instans för connectionString-egenskapen. |Ja |

#### <a name="json-example"></a>JSON-exempel

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Se [Azure SQL-anslutningen](data-factory-azure-sql-connector.md#linked-service-properties) nedan för information om den här länkade tjänsten.

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Du skapar en länkad Azure SQL Data Warehouse-tjänst och använda det med den [lagrade Proceduraktiviteten](data-factory-stored-proc-activity.md) att anropa en lagrad procedur från Data Factory-pipeline.

### <a name="linked-service"></a>Länkad tjänst
För att definiera en Azure SQL Data Warehouse länkad tjänst genom att ange den **typ** på den länkade tjänsten till **AzureSqlDW**, och ange följande egenskaper i den **typeProperties** avsnitt:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| connectionString |Ange information som behövs för att ansluta till Azure SQL Data Warehouse-instans för connectionString-egenskapen. |Ja |

#### <a name="json-example"></a>JSON-exempel

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Mer information finns i [Azure SQL Data Warehouse-anslutningsappen](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties) artikeln.

## <a name="sql-server"></a>SQL Server
Du skapar en länkad SQL Server-tjänst och använda det med den [lagrade Proceduraktiviteten](data-factory-stored-proc-activity.md) att anropa en lagrad procedur från Data Factory-pipeline.

### <a name="linked-service"></a>Länkad tjänst
Du skapar en länkad tjänst av typen **OnPremisesSqlServer** att länka en lokal SQL Server-databas till en data factory. Följande tabell innehåller en beskrivning för JSON-element som är specifika för den lokala SQL Server-länkade tjänst.

Följande tabell innehåller en beskrivning för JSON-element som är specifika för SQL Server-länkade tjänst.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Type-egenskapen ska anges till: **OnPremisesSqlServer**. |Ja |
| connectionString |Ange connectionString information som behövs för att ansluta till en lokal SQL Server-databasen med hjälp av SQL-autentisering eller Windows-autentisering. |Ja |
| gatewayName |Namnet på den gateway som Data Factory-tjänsten ska använda för att ansluta till en lokal SQL Server-databasen. |Ja |
| användarnamn |Ange användarnamnet om du använder Windows-autentisering. Exempel: **domainname\\användarnamn**. |Nej |
| lösenord |Ange lösenord för det användarkonto som du angav för användarnamnet. |Nej |

Du kan kryptera autentiseringsuppgifter med hjälp av den **New-AzureRmDataFactoryEncryptValue** cmdlet och Använd dem i anslutningssträngen som du ser i följande exempel (**EncryptedCredential** egenskapen):

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Exempel: JSON för att använda SQL-autentisering

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>Exempel: JSON för att använda Windows-autentisering

Om användarnamn och lösenord anges använder gateway dem för att personifiera det angivna användarkontot för att ansluta till en lokal SQL Server-databasen. I annat fall ansluter gateway till SQL-servern direkt med säkerhetskontexten för Gateway (dess start-konto).

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Mer information finns i [SQL Server-anslutningen](data-factory-sqlserver-connector.md#linked-service-properties) artikeln.

## <a name="data-transformation-activities"></a>DATATRANSFORMERINGSAKTIVITETER

Aktivitet | Beskrivning
-------- | -----------
[HDInsight Hive-aktivitet](#hdinsight-hive-activity) | HDInsight Hive-aktiviteten i Data Factory-pipeline kör Hive-frågor på egen hand eller Windows/Linux-baserat HDInsight-kluster på begäran.
[HDInsight-piggningsåtgärd](#hdinsight-pig-activity) | HDInsight-piggningsåtgärd i Data Factory-pipeline utför Pig frågor på egen hand eller Windows/Linux-baserat HDInsight-kluster på begäran.
[HDInsight MapReduce-aktivitet](#hdinsight-mapreduce-activity) | HDInsight MapReduce-aktivitet i en Data Factory-pipeline kör MapReduce-program på egen hand eller Windows/Linux-baserat HDInsight-kluster på begäran.
[HDInsight-strömningsaktivitet](#hdinsight-streaming-activity) | HDInsight-Strömningsaktivitet i Data Factory-pipeline utför Hadoop Streaming program på egen hand eller Windows/Linux-baserat HDInsight-kluster på begäran.
[HDInsight Spark-aktivitet](#hdinsight-spark-activity) | HDInsight Spark-aktivitet i en Data Factory-pipeline kör Spark-program på ett eget HDInsight-kluster.
[Machine Learning Batch-körningsaktivitet](#machine-learning-batch-execution-activity) | Azure Data Factory kan du enkelt kan skapa pipelines som använder en publicerade Azure Machine Learning-webbtjänst för förutsägande analys. Du kan anropa en Machine Learning-webbtjänst för att göra förutsägelser på data i batch med hjälp av Batch-Körningsaktivitet i en Azure Data Factory-pipeline.
[Machine Learning-uppdateringsresursaktivitet](#machine-learning-update-resource-activity) | Framöver kommer måste förutsägande modeller i Maskininlärning bedömning experiment vara modellkomponenten med hjälp av nya indatauppsättningar. När du är klar med att träna, som du vill uppdatera bedömning av webbtjänsten med retrained Machine Learning-modellen. Du kan använda aktiviteten för att uppdatera resursen för att uppdatera webbtjänsten med den nyligen tränade modellen.
[Lagrad proceduraktivitet](#stored-procedure-activity) | Du kan använda aktiviteten lagringsprocedur i en Data Factory-pipeline anropa en lagrad procedur i någon av följande datalager: Azure SQL Database, Azure SQL Data Warehouse, SQL Server-databas i ditt företag eller en Azure-dator.
[Data Lake Analytics U-SQL-aktivitet](#data-lake-analytics-u-sql-activity) | Data Lake Analytics U-SQL-aktivitet kör en U-SQL-skript på ett Azure Data Lake Analytics-kluster.
[.NET-anpassad aktivitet](#net-custom-activity) | Om du vill omvandla data på ett sätt som inte stöds av Data Factory kan du skapa en anpassad aktivitet med egen logik för databearbetning och använda aktiviteten i pipelinen. Du kan konfigurera anpassad .NET-aktivitet ska köras med en Azure Batch-tjänst eller ett Azure HDInsight-kluster.


## <a name="hdinsight-hive-activity"></a>HDInsight Hive-aktivitet
Du kan ange följande egenskaper i en Hive-aktivitet JSON-definition. Egenskapen type för aktiviteten måste vara: **HDInsightHive**. Du måste först skapa en länkad HDInsight-tjänst och ange namnet på det som värde för den **linkedServiceName** egenskapen. Följande egenskaper stöds i den **typeProperties** avsnittet när du anger vilken typ av aktivitet till HDInsightHive:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| skriptet |Ange infogat för Hive-skript |Nej |
| skriptets sökväg |Store Hive-skriptet i Azure blob storage och ange sökvägen till filen. Använd ”skript” eller ”scriptPath-egenskapen. Båda kan inte användas tillsammans. Filnamnet är skiftlägeskänsligt. |Nej |
| definierar |Ange parametrar som nyckel/värde-par för refererar till Hive-skript med hjälp av ”hiveconf” |Nej |

Dessa egenskaper är specifika för Hive-aktivitet. Andra egenskaper (utanför avsnittet typeProperties) har stöd för alla aktiviteter.

### <a name="json-example"></a>JSON-exempel
Följande JSON definierar en HDInsight Hive-aktivitet i en pipeline.

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "inputs": [
      {
        "name": "input tables"
      }
    ],
    "outputs": [
      {
        "name": "output tables"
      }
    ],
    "linkedServiceName": "MyHDInsightLinkedService",
    "typeProperties": {
      "script": "Hive script",
      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
      "defines": {
        "param1": "param1Value"
      }
    },
   "scheduler": {
      "frequency": "Day",
      "interval": 1
    }
}
```

Mer information finns i [Hive-aktivitet](data-factory-hive-activity.md) artikeln.

## <a name="hdinsight-pig-activity"></a>HDInsight-piggningsåtgärd
Du kan ange följande egenskaper i en Pig aktivitets-JSON-definition. Egenskapen type för aktiviteten måste vara: **HDInsightPig**. Du måste först skapa en länkad HDInsight-tjänst och ange namnet på det som värde för den **linkedServiceName** egenskapen. Följande egenskaper stöds i den **typeProperties** avsnittet när du anger vilken typ av aktivitet till HDInsightPig:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| skriptet |Ange infogat för Pig-skript |Nej |
| skriptets sökväg |Store Pig-skriptet i Azure blob storage och ange sökvägen till filen. Använd ”skript” eller ”scriptPath-egenskapen. Båda kan inte användas tillsammans. Filnamnet är skiftlägeskänsligt. |Nej |
| definierar |Ange parametrar som nyckel/värde-par för refererar till Pig-skript |Nej |

Dessa egenskaper är specifika för Pig-aktivitet. Andra egenskaper (utanför avsnittet typeProperties) har stöd för alla aktiviteter.

### <a name="json-example"></a>JSON-exempel

```json
{
    "name": "HiveActivitySamplePipeline",
      "properties": {
    "activities": [
        {
            "name": "Pig Activity",
            "description": "description",
            "type": "HDInsightPig",
            "inputs": [
                  {
                    "name": "input tables"
                  }
            ],
            "outputs": [
                  {
                    "name": "output tables"
                  }
            ],
            "linkedServiceName": "MyHDInsightLinkedService",
            "typeProperties": {
                  "script": "Pig script",
                  "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
                  "defines": {
                    "param1": "param1Value"
                  }
            },
               "scheduler": {
                  "frequency": "Day",
                  "interval": 1
            }
          }
    ]
  }
}
```

Mer information finns i [Piggningsåtgärd](#data-factory-pig-activity.md) artikeln.

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce-aktivitet
Du kan ange följande egenskaper i en MapReduce aktivitets-JSON-definition. Egenskapen type för aktiviteten måste vara: **HDInsightMapReduce**. Du måste först skapa en länkad HDInsight-tjänst och ange namnet på det som värde för den **linkedServiceName** egenskapen. Följande egenskaper stöds i den **typeProperties** avsnittet när du anger vilken typ av aktivitet till HDInsightMapReduce:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| jarLinkedService | Namnet på den länkade tjänsten för Azure Storage som innehåller JAR-filen. | Ja |
| jarFilePath | Sökväg till JAR-filen i Azure Storage. | Ja |
| Klassnamn | Namnet på klassen huvudsakliga i JAR-filen. | Ja |
| argument | En lista över kommaavgränsade argument för MapReduce-program. Vid körning, som du ser några extra argument (till exempel: mapreduce.job.tags) från MapReduce-ramverket. Överväg att använda både alternativet och värdet som argument som visas i följande exempel för att skilja dina argument med MapReduce-argument (- s – indata,--utdata osv., är alternativen följt av deras värden) | Nej |

### <a name="json-example"></a>JSON-exempel

```json
{
    "name": "MahoutMapReduceSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calculates an Item Similarity Matrix to determine the similarity between two items",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                    "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": ["-s", "SIMILARITY_LOGLIKELIHOOD", "--input", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input", "--output", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/", "--maxSimilaritiesPerItem", "500", "--tempDir", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"]
                },
                "inputs": [
                    {
                        "name": "MahoutInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "MahoutOutput"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "MahoutActivity",
                "description": "Custom Map Reduce to generate Mahout result",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-01-03T00:00:00",
        "end": "2017-01-04T00:00:00"
    }
}
```

Mer information finns i [MapReduce-aktivitet](data-factory-map-reduce.md) artikeln.

## <a name="hdinsight-streaming-activity"></a>HDInsight-strömningsaktivitet
Du kan ange följande egenskaper i Hadoop Streaming aktivitets-JSON-definitionen. Egenskapen type för aktiviteten måste vara: **HDInsightStreaming**. Du måste först skapa en länkad HDInsight-tjänst och ange namnet på det som värde för den **linkedServiceName** egenskapen. Följande egenskaper stöds i den **typeProperties** avsnittet när du anger vilken typ av aktivitet till HDInsightStreaming:

| Egenskap  | Beskrivning |
| --- | --- |
| händelsemappning | Namnet på den körbara mapparen. I det här exemplet är cat.exe mapper körbara.|
| Reducer | Namnet på den körbara reducer. I det här exemplet är wc.exe reducer körbara. |
| indata | Indatafilen (inklusive plats) för mappningen. I det här exemplet: `"wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt"`: adfsample är blob-behållaren, exempel/data/Gutenberg är mappen och davinci.txt är blob. |
| utdata | Utdatafil (inklusive plats) för reducer. Utdata från Hadoop Streaming-jobbet skrivs till den angivna platsen för den här egenskapen. |
| filePaths | Sökvägar för mapper och reducer körbara filer. I det här exemplet: ”adfsample/example/apps/wc.exe” adfsample är blobbehållaren exempelappar/är mappen och wc.exe är den körbara filen. |
| fileLinkedService | Länkad Azure Storage-tjänst som representerar Azure-lagring som innehåller de filer som anges i avsnittet filePaths. |
| argument | En lista över kommaavgränsade argument för MapReduce-program. Vid körning, som du ser några extra argument (till exempel: mapreduce.job.tags) från MapReduce-ramverket. Överväg att använda både alternativet och värdet som argument som visas i följande exempel för att skilja dina argument med MapReduce-argument (- s – indata,--utdata osv., är alternativen följt av deras värden) |
| getDebugInfo | Ett valfritt element. När den är inställd på fel laddas i loggarna ned endast vid fel. När den är inställd på alla hämtas alltid loggar oavsett körningsstatusen. |

> [!NOTE]
> Du måste ange en utdatauppsättning för Hadoop Streaming Activity för den **matar ut** egenskapen. Den här datauppsättningen kan vara bara en dummy datauppsättning som krävs för att driva pipeline-schema (varje timme, varje dag, osv.). Om aktiviteten inte får en inmatning, du kan hoppa över att ange en indatauppsättning för aktiviteten för de **indata** egenskapen.

## <a name="json-example"></a>JSON-exempel

```json
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": ["<nameofthecluster>/example/apps/wc.exe","<nameofthecluster>/example/apps/cat.exe"],
                    "fileLinkedService": "StorageLinkedService",
                    "getDebugInfo": "Failure"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-04T00:00:00",
        "end": "2014-01-05T00:00:00"
    }
}
```

Mer information finns i [Hadoop Streaming Activity](data-factory-hadoop-streaming-activity.md) artikeln.

## <a name="hdinsight-spark-activity"></a>HDInsight Apache Spark-aktivitet
Du kan ange följande egenskaper i en Spark aktivitets-JSON-definition. Egenskapen type för aktiviteten måste vara: **HDInsightSpark**. Du måste först skapa en länkad HDInsight-tjänst och ange namnet på det som värde för den **linkedServiceName** egenskapen. Följande egenskaper stöds i den **typeProperties** avsnittet när du anger vilken typ av aktivitet till HDInsightSpark:

| Egenskap  | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| rootPath | Azure Blob-behållaren och mappen som innehåller filen Spark. Filnamnet är skiftlägeskänsligt. | Ja |
| entryfilepath = | Relativa sökvägen till rotmappen för koden/paketet Spark. | Ja |
| Klassnamn | Programmets Java/Spark-huvudklass | Nej |
| argument | En lista med kommandoradsargument till Spark-programmet. | Nej |
| proxyUser | Användarkonto för att personifiera för att köra Spark-programmet | Nej |
| sparkConfig | Spark-konfigurationsegenskaper. | Nej |
| getDebugInfo | Anger när Spark filerna kopieras till Azure storage används av HDInsight-kluster (eller) anges av sparkJobLinkedService. Tillåtna värden: Ingen alltid kan eller inte. Standardvärde: Ingen. | Nej |
| sparkJobLinkedService | Azure Storage-länkade tjänst som innehåller Spark jobbfilen, beroenden och loggar.  Om du inte anger ett värde för den här egenskapen används den lagring som är associerad med HDInsight-kluster. | Nej |

### <a name="json-example"></a>JSON-exempel

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-05T00:00:00",
        "end": "2017-02-06T00:00:00"
    }
}
```
Observera följande punkter:

- Den **typ** är inställd på **HDInsightSpark**.
- Den **rootPath** är inställd på **adfspark\\pyFiles** där adfspark är Azure Blob-behållare och pyFiles är bra mapp i den behållaren. I det här exemplet är det som är associerad med Spark-kluster i Azure Blob Storage. Du kan överföra filen till en annan Azure-lagring. Om du gör det måste du skapa en länkad Azure Storage-tjänst för att länka det storage-kontot till datafabriken. Ange namnet på den länkade tjänsten som värde för den **sparkJobLinkedService** egenskapen. Se [Spark Aktivitetsegenskaper](#spark-activity-properties) mer information om den här egenskapen och andra egenskaper som stöds av Spark-aktivitet.
- Den **entryFilePath** är inställd på den **test.py**, vilket är python-filen.
- Den **getDebugInfo** är inställd på **alltid**, vilket innebär att loggfilerna är alltid genereras (lyckade eller misslyckade).  

    > [!IMPORTANT]
    > Vi rekommenderar att du inte anger den här egenskapen att alltid i en produktionsmiljö såvida inte du felsöker ett problem.
- Den **matar ut** -avsnittet innehåller en datamängd för utdata. Du måste ange en utdatauppsättning även om spark-programmet inte producerar några utdata. Utdatauppsättningen styr schemat för pipelinen (varje timme, varje dag, osv.).

Mer information om aktiviteten finns i [Spark-aktivitet](data-factory-spark.md) artikeln.

## <a name="machine-learning-batch-execution-activity"></a>Machine Learning Batch-körningsaktivitet
Du kan ange följande egenskaper i en Azure ML Batch körning aktivitets-JSON-definition. Egenskapen type för aktiviteten måste vara: **AzureMLBatchExecution**. Du måste skapa en Azure Machine Learning-länkade tjänsten först och ange namnet på det som värde för den **linkedServiceName** egenskapen. Följande egenskaper stöds i den **typeProperties** avsnittet när du anger vilken typ av aktivitet till AzureMLBatchExecution:

Egenskap  | Beskrivning | Krävs
-------- | ----------- | --------
webServiceInput | Datauppsättningen som ska skickas som indata för Azure Machine Learning-webbtjänst. Den här datauppsättningen måste också inkluderas i indata för aktiviteten. |Använda webServiceInput eller webServiceInputs. |
webServiceInputs | Ange datauppsättningar som ska skickas som indata för Azure Machine Learning-webbtjänst. Om webbtjänsten tar flera inmatningar kan du använda egenskapen webServiceInputs istället för att använda egenskapen webServiceInput. Datauppsättningar som refererar till den **webServiceInputs** måste också inkluderas i aktiviteten **indata**. | Använda webServiceInput eller webServiceInputs. |
webServiceOutputs | De datauppsättningar som är tilldelad som utdata för Azure Machine Learning-webbtjänst. Webbtjänsten returnerar utdata i den här datauppsättningen. | Ja |
globalParameters | Ange värden för webbtjänstparametrar i det här avsnittet. | Nej |

### <a name="json-example"></a>JSON-exempel
I det här exemplet aktiviteten har datauppsättningen **MLSqlInput** som indata och **MLSqlOutput** som utdata. Den **MLSqlInput** skickas som indata till webbtjänsten genom att använda den **webServiceInput** JSON-egenskap. Den **MLSqlOutput** skickas som utdata till webbtjänsten genom att använda den **webServiceOutputs** JSON-egenskap.

```json
{
   "name": "MLWithSqlReaderSqlWriter",
   "properties": {
      "description": "Azure ML model with sql azure reader/writer",
      "activities": [{
         "name": "MLSqlReaderSqlWriterActivity",
         "type": "AzureMLBatchExecution",
         "description": "test",
         "inputs": [ { "name": "MLSqlInput" }],
         "outputs": [ { "name": "MLSqlOutput" } ],
         "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
         "typeProperties":
         {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
               "output1": "MLSqlOutput"
            },
            "globalParameters": {
               "Database server name": "<myserver>.database.windows.net",
               "Database name": "<database>",
               "Server user account name": "<user name>",
               "Server user account password": "<password>"
            }
         },
         "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
         }
      }],
      "start": "2016-02-13T00:00:00",
       "end": "2016-02-14T00:00:00"
   }
}
```

I JSON-exemplet använder distribuerade Azure Machine Learning Web-tjänsten en läsare och en skrivarmodul för att läsa/skriva data från/till en Azure SQL Database. Den här webbtjänsten exponerar följande fyra parametrar:  -Databasens servernamn, databasnamn, Server användarens kontonamn och Server lösenord.

> [!NOTE]
> Endast indata och utdata för aktiviteten AzureMLBatchExecution kan skickas som parametrar till webbtjänsten. I ovanstående JSON-kodfragmentet är till exempel MLSqlInput indata för aktiviteten AzureMLBatchExecution som skickas som indata till webbtjänsten via webServiceInput-parametern.

## <a name="machine-learning-update-resource-activity"></a>Machine Learning-uppdateringsresursaktivitet
Du kan ange följande egenskaper i en Azure ML Update Resource aktivitets-JSON-definition. Egenskapen type för aktiviteten måste vara: **AzureMLUpdateResource**. Du måste skapa en Azure Machine Learning-länkade tjänsten först och ange namnet på det som värde för den **linkedServiceName** egenskapen. Följande egenskaper stöds i den **typeProperties** avsnittet när du anger vilken typ av aktivitet till AzureMLUpdateResource:

Egenskap  | Beskrivning | Krävs
-------- | ----------- | --------
trainedModelName | Namnet på retrained modellen. | Ja |
trainedModelDatasetName | Datauppsättning som pekar på den iLearner-fil som returneras av omtränings igen. | Ja |

### <a name="json-example"></a>JSON-exempel
Pipelinen har två aktiviteter: **AzureMLBatchExecution** och **AzureMLUpdateResource**. Azure ML-batchkörningsaktivitet tar träningsdata som indata och genererar en iLearner-fil som utdata. Aktiviteten anropar webbtjänsten utbildning (träningsexperiment visas som en webbtjänst) med utbildningsdata som indata och tar emot den ilearner-fil från webbtjänsten. PlaceholderBlob är bara en dummy utdata-datauppsättning som krävs av Azure Data Factory-tjänsten att köra en pipeline.


```json
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "trained model",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [{ "name": "trainedModelBlob" }],
                "outputs": [{ "name": "placeholderBlob" }],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00",
        "end": "2016-02-14T00:00:00"
    }
}
```

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-aktivitet
Du kan ange följande egenskaper i en U-SQL aktivitets-JSON-definition. Egenskapen type för aktiviteten måste vara: **DataLakeAnalyticsU SQL**. Du måste skapa en länkad Azure Data Lake Analytics-tjänst och ange namnet på det som värde för den **linkedServiceName** egenskapen. Följande egenskaper stöds i den **typeProperties** avsnittet när du anger vilken typ av aktivitet till DataLakeAnalyticsU SQL:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| scriptPath |Sökvägen till mappen som innehåller U-SQL-skriptet. Filens namn är skiftlägeskänsligt. |Nej (om du använder skriptet) |
| scriptLinkedService |Länkade tjänst som länkar den lagring som innehåller skriptet som data factory |Nej (om du använder skriptet) |
| skriptet |Ange infogade skript istället för att ange scriptPath och scriptLinkedService. Till exempel: ”skript”: ”CREATE DATABASE-test”. |Nej (om du använder scriptPath och scriptLinkedService) |
| degreeOfParallelism |Det maximala antalet noder samtidigt används för att köra jobbet. |Nej |
| prioritet |Anger vilka jobb av alla som är köade ska väljas att köras först. Ju lägre nummer, desto högre prioritet. |Nej |
| parameters |Parametrar för U-SQL-skript |Nej |

### <a name="json-example"></a>JSON-exempel

```json
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This pipeline computes events for en-gb locale and date less than Feb 19, 2012.",
        "activities":
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs":
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00",
        "end": "2015-08-08T01:00:00",
        "isPaused": false
    }
}
```

Mer information finns i [Data Lake Analytics U-SQL-aktivitet](data-factory-usql-activity.md).

## <a name="stored-procedure-activity"></a>Lagrad proceduraktivitet
Du kan ange följande egenskaper i en lagrad procedur aktivitets-JSON-definition. Egenskapen type för aktiviteten måste vara: **SqlServerStoredProcedure**. Du måste skapa en av följande länkade tjänster och ange namnet på den länkade tjänsten som värde för den **linkedServiceName** egenskapen:

- SQL Server
- Azure SQL Database
- Azure SQL Data Warehouse

Följande egenskaper stöds i den **typeProperties** avsnittet när du anger vilken typ av aktivitet till SqlServerStoredProcedure:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| storedProcedureName |Ange namnet på den lagrade proceduren i Azure SQL database eller Azure SQL Data Warehouse som representeras av den länkade tjänst som använder utdatatabellen. |Ja |
| storedProcedureParameters |Ange värden för parametrarna för lagrad procedur. Om du behöver skicka null för en parameter, använder du syntax: ”param1”: null (gemener). Se följande exempel för att lära dig om hur du använder den här egenskapen. |Nej |

Om du anger en indatauppsättning, måste den vara tillgänglig (statusen ”klar”) för aktiviteten lagrad procedur att köra. Datauppsättningen för indata kan inte användas i den lagrade proceduren som en parameter. Det är bara används för att kontrollera beroendet innan du startar aktivitet för lagrad procedur. Du måste ange en utdatauppsättning för en lagrad procedur-aktivitet.

Datauppsättningen för utdata anger den **schema** för aktiviteten lagrad procedur (varje timme, varje vecka, månadsvis, osv.). Datauppsättningen för utdata måste använda en **länkad tjänst** som refererar till en Azure SQL Database eller en Azure SQL Data Warehouse eller en SQL Server-databas som du vill att den lagrade proceduren för att köra. Datauppsättningen för utdata kan fungera som ett sätt att skicka resultatet av den lagrade proceduren för efterföljande bearbetning av en annan aktivitet ([länkning av aktiviteter](data-factory-scheduling-and-execution.md##multiple-activities-in-a-pipeline)) i pipelinen. Data Factory skriva inte dock automatiskt utdata från en lagrad procedur till den här datauppsättningen. Det är den lagrade proceduren som skriver till en SQLtabell som utdata-datauppsättningen pekar på. I vissa fall kan datauppsättningen för utdata kan vara en **dummy datauppsättning**, vilket används endast för att ange schemat för att köra aktiviteten lagrad procedur.

### <a name="json-example"></a>JSON-exempel

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_sample",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                    }
                },
                "outputs": [{ "name": "sprocsampleout" }],
                "name": "SprocActivitySample"
            }
        ],
         "start": "2016-08-02T00:00:00",
         "end": "2016-08-02T05:00:00",
        "isPaused": false
    }
}
```

Mer information finns i [lagrade Proceduraktiviteten](data-factory-stored-proc-activity.md) artikeln.

## <a name="net-custom-activity"></a>.NET-anpassad aktivitet
Du kan ange följande egenskaper i en anpassad .NET-aktivitet JSON-definition. Egenskapen type för aktiviteten måste vara: **DotNetActivity**. Du måste skapa en Azure HDInsight-länkad tjänst eller en länkad Azure-Batch-tjänsten och ange namnet på den länkade tjänsten som värde för den **linkedServiceName** egenskapen. Följande egenskaper stöds i den **typeProperties** avsnittet när du anger vilken typ av aktivitet till DotNetActivity:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| AssemblyName | Namnet på sammansättningen. I det här exemplet är det: **MyDotnetActivity.dll**. | Ja |
| EntryPoint |Namnet på den klass som implementerar IDotNetActivity-gränssnittet. I det här exemplet är det: **MyDotNetActivityNS.MyDotNetActivity** där MyDotNetActivityNS är namnområdet och MyDotNetActivity är klassen.  | Ja |
| PackageLinkedService | Namnet på den länkade Azure Storage-tjänst som pekar mot blob-lagringen som innehåller anpassad aktivitet zip-filen. I det här exemplet är det: **AzureStorageLinkedService**.| Ja |
| PackageFile | Namnet på zip-filen. I det här exemplet är det: **customactivitycontainer/MyDotNetActivity.zip**. | Ja |
| ExtendedProperties | Utökade egenskaper som du kan definiera och vidarebefordra till .NET-kod. I det här exemplet på **SliceStart** variabeln anges till ett värde baserat på systemvariabeln SliceStart. | Nej |

### <a name="json-example"></a>JSON-exempel

```json
{
  "name": "ADFTutorialPipelineCustom",
  "properties": {
    "description": "Use custom activity",
    "activities": [
      {
        "Name": "MyDotNetActivity",
        "Type": "DotNetActivity",
        "Inputs": [
          {
            "Name": "InputDataset"
          }
        ],
        "Outputs": [
          {
            "Name": "OutputDataset"
          }
        ],
        "LinkedServiceName": "AzureBatchLinkedService",
        "typeProperties": {
          "AssemblyName": "MyDotNetActivity.dll",
          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
          "PackageLinkedService": "AzureStorageLinkedService",
          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
          "extendedProperties": {
            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
          }
        },
        "Policy": {
          "Concurrency": 2,
          "ExecutionPriorityOrder": "OldestFirst",
          "Retry": 3,
          "Timeout": "00:30:00",
          "Delay": "00:00:00"
        }
      }
    ],
    "start": "2016-11-16T00:00:00",
    "end": "2016-11-16T05:00:00",
    "isPaused": false
  }
}
```

Detaljerad information finns i [använda anpassade aktiviteter i Data Factory](data-factory-use-custom-activities.md) artikeln.

## <a name="next-steps"></a>Nästa steg
Se följande självstudiekurser:

- [Självstudier: skapa en pipeline med en Kopieringsaktivitet](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Självstudier: skapa en pipeline med en hive-aktivitet](data-factory-build-your-first-pipeline-using-editor.md)
