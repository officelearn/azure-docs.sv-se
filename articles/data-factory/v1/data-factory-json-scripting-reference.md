---
title: Referens för Azure Data Factory – JSON-skriptreferens
description: Tillhandahåller JSON-scheman för datafabrikentiteter.
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
ms.openlocfilehash: 3492f917be8116d0eed0c7ec03ed8aa9ff506520
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346587"
---
# <a name="azure-data-factory---json-scripting-reference"></a>Referens för Azure Data Factory – JSON-skriptreferens
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory.


Den här artikeln innehåller JSON-scheman och exempel för att definiera Azure Data Factory-entiteter (pipeline, aktivitet, datauppsättning och länkad tjänst).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="pipeline"></a>Pipeline
Högnivåstrukturen för en rörledningsdefinition är följande:

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

I följande tabell beskrivs egenskaperna i JSON-pipeline-definitionen:

| Egenskap | Beskrivning | Krävs
-------- | ----------- | --------
| namn | Namnet på pipeline. Ange ett namn som representerar den åtgärd som aktiviteten eller pipelinen är konfigurerad att utföra<br/><ul><li>Max. antal tecken: 260</li><li>Måste börja med ett bokstavsnummer\_eller ett understreck ( )</li><li>Följande tecken är inte tillåtna: ".", "+", "?", "/", "<",">","*","%","&",":""\\"</li></ul> |Ja |
| description |Text som beskriver vad aktiviteten eller pipelinen används för | Inga |
| activities | Innehåller en lista över aktiviteter. | Ja |
| start |Startdatum för pipelinen. Måste vara i [ISO-format](https://en.wikipedia.org/wiki/ISO_8601). Till exempel: 2014-10-14T16:32:41. <br/><br/>Det är möjligt att ange en lokal tid, till exempel en EST-tid. Här är ett `2016-02-27T06:00:00**-05:00`exempel: , som är 06:00 EST.<br/><br/>Start- och slutegenskaperna anger tillsammans aktiv period för pipelinen. Utdatasegment produceras endast med under den här aktiva perioden. |Inga<br/><br/>Om du anger ett värde för slutegenskapen måste du ange värdet för startegenskapen.<br/><br/>Start- och sluttiderna kan båda vara tomma för att skapa en pipeline. Du måste ange båda värdena för att ange en aktiv period för att pipelinen ska köras. Om du inte anger start- och sluttider när du skapar en pipeline kan du ställa in dem med cmdlet Set-AzDataFactoryPipelineActivePeriod senare. |
| slut |Slutdatumtid för pipelinen. Om det anges måste vara i ISO-format. Till exempel: 2014-10-14T17:32:41 <br/><br/>Det är möjligt att ange en lokal tid, till exempel en EST-tid. Här är ett `2016-02-27T06:00:00**-05:00`exempel: , som är 06:00 EST.<br/><br/>Om du vill köra pipelinen på obestämd tid, anger du 9999-09-09 som värde för slutegenskapen. |Inga <br/><br/>Om du anger ett värde för startegenskapen måste du ange värdet för slutegenskapen.<br/><br/>Se anteckningar **start** för startegenskapen. |
| isPaused |Om den är inställd på true körs inte pipelinen. Standardvärde = falskt. Du kan använda den här egenskapen för att aktivera eller inaktivera. |Inga |
| pipelineMode |Metoden för schemaläggning körs för pipelinen. Tillåtna värden är: schemalagda (standard), en gång.<br/><br/>"Schemalagd" anger att rörledningen körs med ett angivet tidsintervall beroende på dess aktiva period (start- och sluttid). "Onetime" anger att pipelinen bara körs en gång. Engångspipelderna när de har skapats kan inte ändras/uppdateras för närvarande. Se [Onetime pipeline](data-factory-create-pipelines.md#onetime-pipeline) för information om engångsinställning. |Inga |
| förfallodatumTime |Varaktigheten av tiden efter det att rörledningen har skapats och bör förbli etablerad. Om den inte har några aktiva, misslyckade eller väntande körningar tas pipelinen bort automatiskt när den når förfallotiden. |Inga |


## <a name="activity"></a>Aktivitet
Högnivåstrukturen för en aktivitet inom en rörledningsdefinition (aktivitetselement) är följande:

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

I följande tabell beskrivs egenskaperna i aktiviteten JSON-definitionen:

| Tagga | Beskrivning | Krävs |
| --- | --- | --- |
| namn |Namnet på aktiviteten. Ange ett namn som representerar den åtgärd som aktiviteten är konfigurerad att utföra<br/><ul><li>Max. antal tecken: 260</li><li>Måste börja med ett bokstavsnummer\_eller ett understreck ( )</li><li>Följande tecken är inte tillåtna: ".", "+", "?", "/", "<",">","*","%","&",":""\\"</li></ul> |Ja |
| description |Text som beskriver vad aktiviteten används för. |Inga |
| typ |Anger typen av aktivitet. Se avsnitten [DATALAGER](#data-stores) OCH [DATA TRANSFORMATION ACTIVITIES](#data-transformation-activities) för olika typer av aktiviteter. |Ja |
| Ingångar |Indatatabeller som används av aktiviteten<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Nej för HDInsightStreaming- och SqlServerStoredProcedure-aktiviteter <br/> <br/> Ja för alla andra |
| Utgångar |Utdatatabeller som används av aktiviteten.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": “outputtable1” } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” }  ],` |Ja |
| linkedServiceName |Namnet på den länkade tjänst som används av aktiviteten. <br/><br/>En aktivitet kan kräva att du anger den länkade tjänst som länkar till den nödvändiga beräkningsmiljön. |Ja för HDInsight-aktiviteter, Azure Machine Learning-aktiviteter och lagrad proceduraktivitet. <br/><br/>Nej för alla andra |
| typeProperties |Egenskaperna i avsnittet typeProperties beror på aktivitetens typ. |Inga |
| policy |Principer som påverkar körningsbeteende för aktiviteten. Om det inte anges används standardprinciper. |Inga |
| scheduler |Egenskapen "scheduler" används för att definiera önskad schemaläggning för aktiviteten. Dess underegenskaper är desamma som de i [egenskapen availability i en datauppsättning](data-factory-create-datasets.md#dataset-availability). |Inga |

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

### <a name="typeproperties-section"></a>avsnittet typeProperties
Avsnittet typeProperties är olika för varje aktivitet. Omvandlingsaktiviteter har bara typegenskaperna. Se avsnittet [DATA TRANSFORMATION ACTIVITIES](#data-transformation-activities) i den här artikeln för JSON-exempel som definierar omvandlingsaktiviteter i en pipeline.

**Kopieringsaktivitet** har två underavsnitt i avsnittet typeProperties: **källa** och **diskho**. Se avsnittet [DATALAGER](#data-stores) I den här artikeln för JSON-exempel som visar hur du använder ett datalager som källa och/eller diskho.

### <a name="sample-copy-pipeline"></a>Exempel på kopieringspipeline
I följande exempel på pipeline finns det en aktivitet av typen **Copy** (Kopiera) i avsnittet **activities**. I det här exemplet [kopierar aktiviteten Kopiera](data-factory-data-movement-activities.md) data från en Azure Blob-lagring till en Azure SQL-databas.

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

Se avsnittet [DATALAGER](#data-stores) I den här artikeln för JSON-exempel som visar hur du använder ett datalager som källa och/eller diskho.

En fullständig genomgång av hur du skapar den här pipelinen finns i [Självstudiekurs: Kopiera data från Blob Storage till SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

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
* Avsnittet definierar används för att ange de **körningsinställningar** som skickas till registreringsdatafilen `${hiveconf:inputtable}`som `${hiveconf:partitionedtable}`Hive-konfigurationsvärden (t.ex. .

Se avsnittet [DATA TRANSFORMATION ACTIVITIES](#data-transformation-activities) i den här artikeln för JSON-exempel som definierar omvandlingsaktiviteter i en pipeline.

En fullständig genomgång av hur du skapar den här pipelinen finns i [Självstudiekurs: Skapa din första pipeline för att bearbeta data med Hadoop-klustret](data-factory-build-your-first-pipeline.md).

## <a name="linked-service"></a>Länkad tjänst
Högnivåstrukturen för en länkad tjänstedefinition är följande:

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

I följande tabell beskrivs egenskaperna i aktiviteten JSON-definitionen:

| Egenskap | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| namn | Namnet på den länkade tjänsten. | Ja |
| egenskaper - typ | Typ av den länkade tjänsten. Till exempel: Azure Storage, Azure SQL Database. |
| typeProperties | Avsnittet typeProperties har element som är olika för varje datalager eller beräkningsmiljö. Se avsnittet datalager för alla datalagerlänkade tjänster och [beräkningsmiljöer](#compute-environments) för alla beräkningslänkade tjänster |

## <a name="dataset"></a>Datauppsättning
En datauppsättning i Azure Data Factory definieras på följande sätt:

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

I följande tabell beskrivs egenskaper i ovanstående JSON:

| Egenskap | Beskrivning | Krävs | Default |
| --- | --- | --- | --- |
| namn | Datauppsättningens namn. Se [Azure Data Factory - Namngivningsregler](data-factory-naming-rules.md) för namngivningsregler. |Ja |Ej tillämpligt |
| typ | Typ av datauppsättning. Ange en av de typer som stöds av Azure Data Factory (till exempel AzureBlob, AzureSqlTable). Se avsnittet [DATALAGER](#data-stores) för alla datalager och datauppsättningstyper som stöds av Data Factory. |
| Struktur | Schema för datauppsättningen. Den innehåller kolumner, deras typer, etc. | Inga |Ej tillämpligt |
| typeProperties | Egenskaper som motsvarar den valda typen. Se avsnittet [DATALAGER](#data-stores) för typer som stöds och deras egenskaper. |Ja |Ej tillämpligt |
| extern | Boolesk flagga för att ange om en datauppsättning uttryckligen produceras av en datafabrikspipeline eller inte. |Inga |false |
| availability | Definierar bearbetningsfönstret eller skivningsmodellen för datauppsättningsproduktionen. Mer information om datauppsättningsuppsättningsmodellen finns i artikeln [Schemaläggning och körning.](data-factory-scheduling-and-execution.md) |Ja |Ej tillämpligt |
| policy |Definierar villkoret eller villkoret som datauppsättningssegmenten måste uppfylla. <br/><br/>Mer information finns i avsnittet Datauppsättningsprincip. |Inga |Ej tillämpligt |

Varje kolumn i **strukturavsnittet** innehåller följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| namn |Kolumnens namn. |Ja |
| typ |Datatyp för kolumnen.  |Inga |
| Kultur |.NET-baserad odling som ska användas när typen `Datetime` `Datetimeoffset`anges och är .NET-typ eller . Standardvärdet är `en-us`. |Inga |
| format |Formatera sträng som ska användas när typen anges `Datetime` `Datetimeoffset`och är .NET-typ eller . |Inga |

I följande exempel har datauppsättningen `slicetimestamp`tre `projectname`kolumner `pageviews` , och och de är av typen: Sträng, Sträng respektive Decimal.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

I följande tabell beskrivs egenskaper **availability** som du kan använda i tillgänglighetsavsnittet:

| Egenskap | Beskrivning | Krävs | Default |
| --- | --- | --- | --- |
| frequency |Anger tidsenheten för datauppsättningssegmentproduktion.<br/><br/><b>Frekvens som stöds</b>: Minut, Timme, Dag, Vecka, Månad |Ja |Ej tillämpligt |
| interval |Anger en multiplikator för frekvens<br/><br/>"Frekvens x intervall" avgör hur ofta segmentet produceras.<br/><br/>Om du behöver datauppsättningen som ska segmenteras per timme anger du <b>Frekvens</b> till <b>Timme</b>och <b>intervall</b> till <b>1</b>.<br/><br/><b>Obs:</b>Om du anger Frekvens som minut rekommenderar vi att du ställer in intervallet till inte mindre än 15 |Ja |Ej tillämpligt |
| stil |Anger om segmentet ska produceras i början/slutet av intervallet.<ul><li>StartofInterval</li><li>Slutavinföring</li></ul><br/><br/>Om Frekvens är inställt på Månad och format är inställt på EndOfInterval, produceras segmentet den sista dagen i månaden. Om formatet är inställt på StartOfInterval produceras segmentet den första dagen i månaden.<br/><br/>Om Frekvens är inställt på Dag och formatet är inställt på EndOfInterval, produceras segmentet under den sista timmen på dagen.<br/><br/>Om Frekvens är inställt på Timme och formatet är inställt på EndOfInterval, produceras segmentet i slutet av timmen. För ett segment för 13:00 till 14:00 period, är segmentet produceras vid 2 PM. |Inga |Slutavinföring |
| anchorDateTime |Definierar den absoluta positionen i tid som används av schemaläggaren för att beräkna datauppsättningssegmentgränser. <br/><br/><b>Obs:</b>Om AnchorDateTime har datumdelar som är mer detaljerade än frekvensen ignoreras de mer detaljerade delarna. <br/><br/>Om <b>intervallet</b> till exempel är <b>varje timme</b> (frekvens: timme och intervall: 1) och <b>AnchorDateTime</b> innehåller <b>minuter och sekunder</b> ignoreras minuter <b>och sekunder</b> i AnchorDateTime. |Inga |01/01/0001 |
| offset |Tidsintervall med vilket början och slutet av alla datauppsättningssegment flyttas. <br/><br/><b>Obs:</b>Om både anchorDateTime och offset anges, blir resultatet det kombinerade skiftet. |Inga |Ej tillämpligt |

I följande tillgänglighetsavsnitt anges att utdatauppsättningen antingen produceras per timme (eller) indatauppsättning är tillgänglig varje timme:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

**Principavsnittet** i datauppsättningsdefinitionen definierar villkoren eller villkoret som datauppsättningssegmenten måste uppfylla.

| Principnamn | Beskrivning | Tillämpas på | Krävs | Default |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Verifierar att data i en **Azure-blob** uppfyller minimikraven för storlek (i megabyte). |Azure-blobb |Inga |Ej tillämpligt |
| minimumRows |Verifierar att data i en **Azure SQL-databas** eller en **Azure-tabell** innehåller det minsta antalet rader. |<ul><li>Azure SQL Database</li><li>Azure-tabell</li></ul> |Inga |Ej tillämpligt |

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

Om inte en datauppsättning produceras av Azure Data Factory bör den markeras som **extern**. Den här inställningen gäller vanligtvis för indata från första aktiviteten i en pipeline om inte aktivitet eller pipeline-kedja används.

| Namn | Beskrivning | Krävs | Standardvärde |
| --- | --- | --- | --- |
| dataDelay |Dags att fördröja kontrollen av tillgängligheten för externa data för det angivna segmentet. Om data till exempel är tillgängliga varje timme är kontrollen för att se att externa data är tillgängliga och motsvarande segment är Klar kan fördröjas med hjälp av dataDelay.<br/><br/>Gäller endast för närvarande.  Om det till exempel är 13:00 just nu och det här värdet är 10 minuter börjar valideringen 13:10.<br/><br/>Den här inställningen påverkar inte segment tidigare (segment med segmentsluttid + dataDelay < Now) bearbetas utan dröjsmål.<br/><br/>Tid som är större än 23:59 timmar måste anges med formatet. `day.hours:minutes:seconds` Om du till exempel vill ange 24 timmar ska du inte använda 24:00:00. Använd i stället 1.00:00:00. Om du använder 24:00:00 behandlas den som 24 dagar (24.00:00:00). För 1 dag och 4 timmar anger du 1:04:00:00. |Inga |0 |
| retryInterval |Väntetiden mellan ett fel och nästa försök till nytt försök. Om ett försök misslyckas är nästa försök efter återförsökInterval. <br/><br/>Om det är 13:00 just nu, börjar vi första försöket. Om varaktigheten för att slutföra den första valideringskontrollen är 1 minut och åtgärden misslyckades, är nästa återförsök 1:00 + 1 min (varaktighet) + 1 min (återförsöksintervall) = 1:02 PM. <br/><br/>För segment i det förflutna finns det ingen fördröjning. Återförsöket sker omedelbart. |Inga |00:01:00 (1 minut) |
| försöktimeout igen |Tidsgränsen för varje försök till nytt försök.<br/><br/>Om den här egenskapen är inställd på 10 minuter måste valideringen slutföras inom 10 minuter. Om det tar längre tid än 10 minuter att utföra valideringen, time out.If it takes longer than 10 minutes to perform the validation, the retry time out.<br/><br/>Om alla försök för valideringen timeout markeras segmentet som TimedOut. |Inga |00:10:00 (10 minuter) |
| maximumRetry |Antal gånger för att kontrollera om de externa data finns tillgängliga. Det tillåtna maxvärdet är 10. |Inga |3 |


## <a name="data-stores"></a>DATALAGER
Avsnittet [Länkad tjänst](#linked-service) gav beskrivningar för JSON-element som är gemensamma för alla typer av länkade tjänster. Det här avsnittet innehåller information om JSON-element som är specifika för varje datalager.

Avsnittet [Datauppsättning](#dataset) gav beskrivningar för JSON-element som är gemensamma för alla typer av datauppsättningar. Det här avsnittet innehåller information om JSON-element som är specifika för varje datalager.

Avsnittet [Aktivitet](#activity) innehåller beskrivningar för JSON-element som är gemensamma för alla typer av aktiviteter. Det här avsnittet innehåller information om JSON-element som är specifika för varje datalager när de används som källa/mottagare i en kopieringsaktivitet.

Klicka på länken för den butik som du är intresserad av för att se JSON-schemana för länkad tjänst, datauppsättning och käll-/diskho för kopieringsaktiviteten.

| Kategori | Datalager
|:--- |:--- |
| **Azure** |[Azure Blob-lagring](#azure-blob-storage) |
| &nbsp; |Azure Data Lake Store |
| &nbsp; |[Azure Cosmos DB](#azure-cosmos-db) |
| &nbsp; |[Azure SQL Database](#azure-sql-database) |
| &nbsp; |[Azure SQL-datalager](#azure-sql-data-warehouse) |
| &nbsp; |[Azure Cognitive Search](#azure-cognitive-search) |
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
| &nbsp; |[Mongodb](#mongodb) |
| **Fil** |[Amazon S3](#amazon-s3) |
| &nbsp; |[Filsystem](#file-system) |
| &nbsp; |[FTP](#ftp) |
| &nbsp; |[HDFS](#hdfs) |
| &nbsp; |[SFTP](#sftp) |
| **Andra** |[HTTP](#http) |
| &nbsp; |[OData](#odata) |
| &nbsp; |[ODBC](#odbc) |
| &nbsp; |[Salesforce](#salesforce) |
| &nbsp; |Webbtabell |

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="linked-service"></a>Länkad tjänst
Det finns två typer av länkade tjänster: Azure Storage-länkad tjänst och Azure Storage SAS-länkad tjänst.

#### <a name="azure-storage-linked-service"></a>Länkad Azure Storage-tjänst
Om du vill länka ditt Azure-lagringskonto till en datafabrik med hjälp av **kontonyckeln**skapar du en Azure Storage-länkad tjänst. Om du vill definiera en Azure Storage-länkad tjänst anger du **typen** av länkad tjänst till **AzureStorage**. Sedan kan du ange följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| Connectionstring |Ange information som behövs för att ansluta till Azure-lagring för egenskapen connectionString. |Ja |

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

#### <a name="azure-storage-sas-linked-service"></a>Azure Storage SAS-länkad tjänst
Med den länkade Azure Storage SAS-tjänsten kan du länka ett Azure Storage-konto till en Azure-datafabrik med hjälp av en SAS (Shared Access Signature). Det ger datafabriken begränsad/tidsbunden åtkomst till alla/specifika resurser (blob/container) i lagringen. Om du vill länka ditt Azure-lagringskonto till en datafabrik med hjälp av Signature för delad åtkomst skapar du en Azure Storage SAS-länkad tjänst. Om du vill definiera en Azure Storage SAS-länkad tjänst anger du **typen** av länkad tjänst till **AzureStorageSas**. Sedan kan du ange följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| sasuri (samma sak) |Ange URI för delad åtkomstsignatur till Azure Storage-resurser som blob, behållare eller tabell. |Ja |

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

Mer information om dessa länkade tjänster finns i artikel [i Azure Blob Storage connector.](data-factory-azure-blob-connector.md#linked-service-properties)

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en Azure Blob-datauppsättning anger du **typen** av datauppsättningen till **AzureBlob**. Ange sedan följande Azure Blob-specifika egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Sökväg till behållaren och mappen i blob-lagringen. Exempel: myblobcontainer\myblobfolder\ |Ja |
| fileName |Namnet på blobben. fileName är valfritt och skiftlägeskänsligt.<br/><br/>Om du anger ett filnamn fungerar aktiviteten (inklusive Kopiera) på den specifika Bloben.<br/><br/>När filnamn inte har angetts innehåller Copy alla Blobbar i folderPath för indatauppsättning.<br/><br/>När fileName inte anges för en utdatauppsättning, skulle namnet på den genererade `Data.<Guid>.txt` filen vara i följande format: (till exempel: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Inga |
| partitioneradAv |partitionedBy är en valfri egenskap. Du kan använda den för att ange en dynamisk folderPath och filnamn för tidsseriedata. FolderPath kan till exempel parameteriseras för varje timme data. |Inga |
| format | Följande formattyper stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange **type** typegenskapen under format till ett av dessa värden. Mer information finns i avsnitten [Textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)och [Parquet Format.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Om du vill **kopiera filer som de är** mellan filbaserade butiker (binär kopia) hoppar du över formatavsnittet i definitionerna för både in- och utdatadata. |Inga |
| komprimering | Ange typ och komprimeringsnivå för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**och **ZipDeflate**. Nivåer som stöds är: **Optimal** och **snabbaste**. Mer information finns [i Fil- och komprimeringsformat i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Inga |

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


Mer information finns i artikel [i Azure Blob Connector.](data-factory-azure-blob-connector.md#dataset-properties)

### <a name="blobsource-in-copy-activity"></a>BlobSource i kopieringsaktivitet
Om du kopierar data från en Azure Blob Storage anger du **källtypen** för kopieringsaktiviteten till **BlobSource**och anger följande egenskaper i **källavsnittet:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| Rekursiv |Anger om data läss rekursivt från undermapparna eller bara från den angivna mappen. |Sant (standardvärde), Falskt |Inga |

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
### <a name="blobsink-in-copy-activity"></a>BlobSink i kopieringsaktivitet
Om du kopierar data till en Azure Blob Storage anger du **sink-typen** för kopieringsaktiviteten till **BlobSink**och anger följande egenskaper i **handfatavsnittet:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| kopieraBehavior |Definierar kopieringsbeteendet när källan är BlobSource eller FileSystem. |<b>PreserveHierarchy</b>: bevarar filhierarkin i målmappen. Den relativa sökvägen för källfilen till källmappen är identisk med målfilens relativa sökväg till målmappen.<br/><br/><b>FlattenHierarchy</b>: alla filer från källmappen är i den första nivån av målmappen. Målfilerna har automatiskt genererat namn. <br/><br/><b>MergeFiles (standard):</b> sammanfogar alla filer från källmappen till en fil. Om fil-/blobnamnet anges är det kopplade filnamnet det angivna namnet. annars skulle vara automatiskt genererat filnamn. |Inga |

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

Mer information finns i artikel [i Azure Blob Connector.](data-factory-azure-blob-connector.md#copy-activity-properties)

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="linked-service"></a>Länkad tjänst
Om du vill definiera en Azure Data Lake Store-länkad tjänst anger du typen av länkad tjänst till **AzureDataLakeStore**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typegenskapen måste ställas in på: **AzureDataLakeStore** | Ja |
| dataLakeStoreUri | Ange information om Azure Data Lake Store-kontot. Det är i följande `https://[accountname].azuredatalakestore.net/webhdfs/v1` `adl://[accountname].azuredatalakestore.net/`format: eller . | Ja |
| subscriptionId | Azure-prenumerations-ID som Data Lake Store tillhör. | Krävs för diskbänk |
| resourceGroupName | Azure-resursgruppsnamn som Data Lake Store tillhör. | Krävs för diskbänk |
| servicePrincipalId | Ange programmets klient-ID. | Ja (för autentisering av tjänstens huvudnamn) |
| servicePrincipalKey | Ange programmets nyckel. | Ja (för autentisering av tjänstens huvudnamn) |
| tenant | Ange klientinformation (domännamn eller klient-ID) som programmet finns under. Du kan hämta den genom att hålla musen i det övre högra hörnet av Azure-portalen. | Ja (för autentisering av tjänstens huvudnamn) |
| auktorisering | Klicka på **Knappen Auktorisera** i **Data Factory Editor** och ange autentiseringsuppgifterna som tilldelar den automatiskt genererade auktoriseringsadressen till den här egenskapen. | Ja (för autentiseringsuppgifter för användare)|
| Sessionid | OAuth-sessions-ID från OAuth-auktoriseringssessionen. Varje sessions-ID är unikt och får endast användas en gång. Den här inställningen genereras automatiskt när du använder Data Factory Editor. | Ja (för autentiseringsuppgifter för användare) |

#### <a name="example-using-service-principal-authentication"></a>Exempel: använda autentisering av tjänstens huvudnamn
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

#### <a name="example-using-user-credential-authentication"></a>Exempel: använda autentiseringsuppgifter för användare
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

Mer information finns i artikel [i Azure Data Lake Store-anslutningsartikeln.](data-factory-azure-datalake-connector.md#linked-service-properties)

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en Azure Data Lake Store-datauppsättning anger du **typen** av datauppsättningen till **AzureDataLakeStore**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| folderPath |Sökväg till behållaren och mappen i Azure Data Lake Store. |Ja |
| fileName |Namn på filen i Azure Data Lake Store. fileName är valfritt och skiftlägeskänsligt. <br/><br/>Om du anger ett filnamn fungerar aktiviteten (inklusive Kopiera) på den specifika filen.<br/><br/>När filnamn inte har angetts innehåller Copy alla filer i folderPath för indatauppsättning.<br/><br/>När fileName inte anges för en utdatauppsättning, skulle namnet på den genererade `Data.<Guid>.txt` filen vara i följande format: (till exempel: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Inga |
| partitioneradAv |partitionedBy är en valfri egenskap. Du kan använda den för att ange en dynamisk folderPath och filnamn för tidsseriedata. FolderPath kan till exempel parameteriseras för varje timme data. |Inga |
| format | Följande formattyper stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange **type** typegenskapen under format till ett av dessa värden. Mer information finns i avsnitten [Textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)och [Parquet Format.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Om du vill **kopiera filer som de är** mellan filbaserade butiker (binär kopia) hoppar du över formatavsnittet i definitionerna för både in- och utdatadata. |Inga |
| komprimering | Ange typ och komprimeringsnivå för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**och **ZipDeflate**. Nivåer som stöds är: **Optimal** och **snabbaste**. Mer information finns [i Fil- och komprimeringsformat i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Inga |

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

Mer information finns i artikel [i Azure Data Lake Store-anslutningsartikeln.](data-factory-azure-datalake-connector.md#dataset-properties)

### <a name="azure-data-lake-store-source-in-copy-activity"></a>Azure Data Lake Store-källa i kopieringsaktivitet
Om du kopierar data från ett Azure Data Lake Store anger du **källtypen** för kopieringsaktiviteten till **AzureDataLakeStoreSource**och anger följande egenskaper i **källavsnittet:**

**AzureDataLakeStoreSource** stöder följande **egenskapstypEgenskaper:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| Rekursiv |Anger om data läss rekursivt från undermapparna eller bara från den angivna mappen. |Sant (standardvärde), Falskt |Inga |

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

Mer information finns i artikel [i Azure Data Lake Store-anslutningsartikeln.](data-factory-azure-datalake-connector.md#copy-activity-properties)

### <a name="azure-data-lake-store-sink-in-copy-activity"></a>Azure Data Lake Store Sink i kopieringsaktivitet
Om du kopierar data till ett Azure Data Lake Store anger du **sink-typen** för kopieringsaktiviteten till **AzureDataLakeStoreSink**och anger följande egenskaper i **handfinkavsnittet:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| kopieraBehavior |Anger kopieringsbeteendet. |<b>PreserveHierarchy</b>: bevarar filhierarkin i målmappen. Den relativa sökvägen för källfilen till källmappen är identisk med målfilens relativa sökväg till målmappen.<br/><br/><b>FlattenHierarchy</b>: alla filer från källmappen skapas i den första nivån av målmappen. Målfilerna skapas med automatiskt genererat namn.<br/><br/><b>MergeFiles</b>: sammanfogar alla filer från källmappen till en fil. Om fil-/blobnamnet anges är det kopplade filnamnet det angivna namnet. annars skulle vara automatiskt genererat filnamn. |Inga |

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

Mer information finns i artikel [i Azure Data Lake Store-anslutningsartikeln.](data-factory-azure-datalake-connector.md#copy-activity-properties)

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="linked-service"></a>Länkad tjänst
Om du vill definiera en Azure Cosmos DB-länkad tjänst anger du **typen** av länkad tjänst till **DocumentDb**och anger följande egenskaper i avsnittet **typeProperties:**

| **Egenskap** | **Beskrivning** | **Obligatoriskt** |
| --- | --- | --- |
| Connectionstring |Ange information som behövs för att ansluta till Azure Cosmos DB-databas. |Ja |

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
Mer information finns i azure [cosmos DB](data-factory-azure-documentdb-connector.md#linked-service-properties) connector-artikel.

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en Azure Cosmos DB-datauppsättning anger du **typen** av datauppsättningen till **DocumentDbCollection**och anger följande egenskaper i avsnittet **typeProperties:**

| **Egenskap** | **Beskrivning** | **Obligatoriskt** |
| --- | --- | --- |
| collectionName (samlingsnamn) |Namn på Azure Cosmos DB-samlingen. |Ja |

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
Mer information finns i azure [cosmos DB](data-factory-azure-documentdb-connector.md#dataset-properties) connector-artikel.

### <a name="azure-cosmos-db-collection-source-in-copy-activity"></a>Azure Cosmos DB-samlingskälla i kopieringsaktivitet
Om du kopierar data från en Azure Cosmos DB anger du **källtypen** för kopieringsaktiviteten till **DocumentDbCollectionSource**och anger följande egenskaper i **källavsnittet:**


| **Egenskap** | **Beskrivning** | **Tillåtna värden** | **Obligatoriskt** |
| --- | --- | --- | --- |
| DocumentDB |Ange frågan för att läsa data. |Frågesträng som stöds av Azure Cosmos DB. <br/><br/>Exempel: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Inga <br/><br/>Om inget anges körs SQL-uttrycket som körs:`select <columns defined in structure> from mycollection` |
| nestingSeparator |Specialtecken som anger att dokumentet är kapslat |Vilket tecken som helst. <br/><br/>Azure Cosmos DB är ett NoSQL-arkiv för JSON-dokument, där kapslade strukturer tillåts. Azure Data Factory gör det möjligt för användare att beteckna hierarki via nestingSeparator, som är "." i ovanstående exempel. Med avgränsaren genererar kopieringsaktiviteten "Namn"-objektet med tre underordnade element Först, Mitten och Sist, enligt "Name.First", "Name.Middle" och "Name.Last" i tabelldefinitionen. |Inga |

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

### <a name="azure-cosmos-db-collection-sink-in-copy-activity"></a>Azure Cosmos DB Collection Sink i kopieringsaktivitet
Om du kopierar data till Azure Cosmos DB anger du **sink-typen** för kopieringsaktiviteten till **DocumentDbCollectionSink**och anger följande egenskaper i **handfatavsnittet:**

| **Egenskap** | **Beskrivning** | **Tillåtna värden** | **Obligatoriskt** |
| --- | --- | --- | --- |
| nestingSeparator |Ett specialtecken i källkolumnens namn för att ange att kapslat dokument behövs. <br/><br/>Till exempel `Name.First` ovan: i utdatatabellen skapas följande JSON-struktur i Cosmos DB-dokumentet:<br/><br/>"Namn": {<br/>    "Först": "John"<br/>}, |Tecken som används för att avgränsa kapslingsnivåer.<br/><br/>Standardvärdet `.` är (punkt). |Tecken som används för att avgränsa kapslingsnivåer. <br/><br/>Standardvärdet `.` är (punkt). |
| skriverBatchSize |Antal parallella begäranden till Azure Cosmos DB-tjänst för att skapa dokument.<br/><br/>Du kan finjustera prestanda när du kopierar data till/från Azure Cosmos DB med hjälp av den här egenskapen. Du kan förvänta dig bättre prestanda när du ökar writeBatchSize eftersom fler parallella begäranden till Azure Cosmos DB skickas. Du måste dock undvika begränsning som kan kasta felmeddelandet: "Begäranden är stor".<br/><br/>Begränsning bestäms av ett antal faktorer, inklusive storlek på dokument, antal termer i dokument, indexering politik målinsamling, etc. För kopieringsåtgärder kan du använda en bättre samling (till exempel S3) för att ha mest tillgängliga dataflöde (2 500 begärandeenheter/sekund). |Integer |Nej (standard: 5) |
| skriverBatchTimeout |Vänta på att åtgärden ska slutföras innan den har time out. |Gått<br/><br/> Exempel: "00:30:00" (30 minuter). |Inga |

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

Mer information finns i azure [cosmos DB](data-factory-azure-documentdb-connector.md#copy-activity-properties) connector-artikel.

## <a name="azure-sql-database"></a>Azure SQL Database

### <a name="linked-service"></a>Länkad tjänst
Om du vill definiera en azure SQL Database-länkad tjänst anger du **typen** av länkad tjänst till **AzureSqlDatabase**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| Connectionstring |Ange information som behövs för att ansluta till Azure SQL Database-instansen för egenskapen connectionString. |Ja |

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

Mer information finns i [Azure SQL Connector-artikeln.](data-factory-azure-sql-connector.md#linked-service-properties)

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en Azure SQL Database-datauppsättning anger du **typen** av datauppsättningen till **AzureSqlTable**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen eller vyn i Azure SQL Database-instansen som länkade tjänsten refererar till. |Ja |

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
Mer information finns i [Azure SQL Connector-artikeln.](data-factory-azure-sql-connector.md#dataset-properties)

### <a name="sql-source-in-copy-activity"></a>SQL-källa i kopieringsaktivitet
Om du kopierar data från en Azure SQL-databas anger du **källtypen** för kopieringsaktiviteten till **SqlSource**och anger följande egenskaper i **källavsnittet:**


| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| sqlReaderQuery |Använd den anpassade frågan för att läsa data. |SQL-frågesträng. Exempel: `select * from MyTable`. |Inga |
| sqlReaderStoredProcedureName |Namnet på den lagrade proceduren som läser data från källtabellen. |Namnet på den lagrade proceduren. |Inga |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värdepar. Namn och hölje av parametrar måste stämma överens med namn och hölje för de lagrade procedureparametrarna. |Inga |

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
Mer information finns i [Azure SQL Connector-artikeln.](data-factory-azure-sql-connector.md#copy-activity-properties)

### <a name="sql-sink-in-copy-activity"></a>SQL-mottagare i kopieringsaktivitet
Om du kopierar data till Azure SQL Database anger du **sink-typen** för kopieringsaktiviteten till **SqlSink**och anger följande egenskaper i **handfatavsnittet:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| skriverBatchTimeout |Vänta på att batchinsatsen ska slutföras innan den har klarats upp. |Gått<br/><br/> Exempel: "00:30:00" (30 minuter). |Inga |
| skriverBatchSize |Infogar data i SQL-tabellen när buffertstorleken når writeBatchSize. |Heltal (antal rader) |Nej (standard: 10000) |
| sqlWriterCleanupScript |Ange en fråga för Kopieringsaktivitet som ska köras så att data för ett visst segment rensas. |En frågesats. |Inga |
| sliceIdentifierColumnName |Ange ett kolumnnamn för Kopiera aktivitet som ska fyllas med automatisk genererad segmentidentifierare, som används för att rensa data från ett visst segment när du kör igen. |Kolumnnamnet på en kolumn med datatyp av binär(32). |Inga |
| sqlWriterStoredProcedureName |Namn på den lagrade procedur som lägger till (uppdateringar/infogar) data i måltabellen. |Namnet på den lagrade proceduren. |Inga |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värdepar. Namn och hölje av parametrar måste stämma överens med namn och hölje för de lagrade procedureparametrarna. |Inga |
| sqlWriterTableType |Ange ett tabelltypnamn som ska användas i den lagrade proceduren. Kopieringsaktivitet gör de data som flyttas tillgängliga i en temp-tabell med den här tabelltypen. Lagrad procedurkod kan sedan sammanfoga data som kopieras med befintliga data. |Ett tabelltypnamn. |Inga |

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

Mer information finns i [Azure SQL Connector-artikeln.](data-factory-azure-sql-connector.md#copy-activity-properties)

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

### <a name="linked-service"></a>Länkad tjänst
Om du vill definiera en Azure SQL Data Warehouse-länkad tjänst anger du **typen** av länkad tjänst till **AzureSqlDW**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| Connectionstring |Ange information som behövs för att ansluta till Azure SQL Data Warehouse-instansen för egenskapen connectionString. |Ja |



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

Mer information finns i artikel [i Azure SQL Data Warehouse Connector.](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties)

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en Azure SQL Data Warehouse-datauppsättning anger du **typen** av datauppsättningen till **AzureSqlDWTable**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namn på tabellen eller vyn i Azure SQL Data Warehouse-databasen som den länkade tjänsten refererar till. |Ja |

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

Mer information finns i artikel [i Azure SQL Data Warehouse Connector.](data-factory-azure-sql-data-warehouse-connector.md#dataset-properties)

### <a name="sql-dw-source-in-copy-activity"></a>SQL DW-källa i kopieringsaktivitet
Om du kopierar data från Azure SQL Data Warehouse anger du **källtypen** för kopieringsaktiviteten till **SqlDWSource**och anger följande egenskaper i **källavsnittet:**


| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| sqlReaderQuery |Använd den anpassade frågan för att läsa data. |SQL-frågesträng. Till exempel: `select * from MyTable`. |Inga |
| sqlReaderStoredProcedureName |Namnet på den lagrade proceduren som läser data från källtabellen. |Namnet på den lagrade proceduren. |Inga |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värdepar. Namn och hölje av parametrar måste stämma överens med namn och hölje för de lagrade procedureparametrarna. |Inga |

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

Mer information finns i artikel [i Azure SQL Data Warehouse Connector.](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

### <a name="sql-dw-sink-in-copy-activity"></a>SQL DW-mottagare i kopieringsaktivitet
Om du kopierar data till Azure SQL Data Warehouse anger du **sink-typen** för kopieringsaktiviteten till **SqlDWSink**och anger följande egenskaper i **handfatavsnittet:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Ange en fråga för Kopieringsaktivitet som ska köras så att data för ett visst segment rensas. |En frågesats. |Inga |
| allowPolyBase |Anger om PolyBase (i förekommande fall) ska användas i stället för BULKINSERT-mekanism. <br/><br/> **Att använda PolyBase är det rekommenderade sättet att läsa in data i SQL Data Warehouse.** |True <br/>False (standard) |Inga |
| polyBaseSettings polyBaseSettings polyBaseSettings polyBase |En grupp egenskaper som kan anges när egenskapen **allowPolybase** är inställd på **true**. |&nbsp; |Inga |
| avvisaVärde |Anger antalet eller procentandelen rader som kan avvisas innan frågan misslyckas. <br/><br/>Läs mer om PolyBases avvisningsalternativ i avsnittet **Argument** i avsnittet [SKAPA EXTERN TABELL (Transact-SQL).](https://msdn.microsoft.com/library/dn935021.aspx) |0 (standard), 1, 2, ... |Inga |
| rejectType (avskände) |Anger om alternativet rejectValue anges som ett litteralt värde eller en procentsats. |Värde (standard), Procent |Inga |
| avvisaPlevalue |Bestämmer antalet rader som ska hämtas innan PolyBase beräknar om procentandelen avvisade rader. |1, 2, ... |Ja, om **rejectType** är **procent** |
| useTypeDefault |Anger hur värden som saknas i avgränsade textfiler ska hanteras när PolyBase hämtar data från textfilen.<br/><br/>Läs mer om den här egenskapen från avsnittet Argument i [SKAPA EXTERNT FILFORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |Sant, Falskt (standard) |Inga |
| skriverBatchSize |Infogar data i SQL-tabellen när buffertstorleken når writeBatchSize |Heltal (antal rader) |Nej (standard: 10000) |
| skriverBatchTimeout |Vänta på att batchinsatsen ska slutföras innan den har klarats upp. |Gått<br/><br/> Exempel: "00:30:00" (30 minuter). |Inga |

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

Mer information finns i artikel [i Azure SQL Data Warehouse Connector.](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

## <a name="azure-cognitive-search"></a>Azure Cognitive Search

### <a name="linked-service"></a>Länkad tjänst
Om du vill definiera en Azure Cognitive Search-länkad tjänst anger du **typen** av länkad tjänst till **AzureSearch**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| url | URL för söktjänsten. | Ja |
| key | Administratörsnyckel för söktjänsten. | Ja |

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

Mer information finns i artikel [i Azure Cognitive Search connector.](data-factory-azure-search-connector.md#linked-service-properties)

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en Azure Cognitive Search-datauppsättning anger du **typen** av datauppsättningen till **AzureSearchIndex**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| typ | Typegenskapen måste anges till **AzureSearchIndex**.| Ja |
| indexNamn | Namn på sökindexet. Data Factory skapar inte indexet. Indexet måste finnas i Azure Cognitive Search. | Ja |

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

Mer information finns i artikel [i Azure Cognitive Search connector.](data-factory-azure-search-connector.md#dataset-properties)

### <a name="azure-cognitive-search-index-sink-in-copy-activity"></a>Azure Cognitive Search Index Sink i kopieringsaktivitet
Om du kopierar data till ett sökindex anger du **sink-typen** för kopieringsaktiviteten till **AzureSearchIndexSink**och anger följande egenskaper i **handfatavsnittet:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| -------- | ----------- | -------------- | -------- |
| SkrivBeteende | Anger om det ska slås samman eller ersätta när det redan finns ett dokument i indexet. | Sammanfoga (standard)<br/>Ladda upp| Inga |
| SkrivBatchSize | Överför data till sökindexet när buffertstorleken når writeBatchSize. | 1 till 1000. Standardvärdet är 1000. | Inga |

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

Mer information finns i artikel [i Azure Cognitive Search connector.](data-factory-azure-search-connector.md#copy-activity-properties)

## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="linked-service"></a>Länkad tjänst
Det finns två typer av länkade tjänster: Azure Storage-länkad tjänst och Azure Storage SAS-länkad tjänst.

#### <a name="azure-storage-linked-service"></a>Länkad Azure Storage-tjänst
Om du vill länka ditt Azure-lagringskonto till en datafabrik med hjälp av **kontonyckeln**skapar du en Azure Storage-länkad tjänst. Om du vill definiera en Azure Storage-länkad tjänst anger du **typen** av länkad tjänst till **AzureStorage**. Sedan kan du ange följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Typegenskapen måste anges till: **AzureStorage** |Ja |
| Connectionstring |Ange information som behövs för att ansluta till Azure-lagring för egenskapen connectionString. |Ja |

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

#### <a name="azure-storage-sas-linked-service"></a>Azure Storage SAS-länkad tjänst
Med den länkade Azure Storage SAS-tjänsten kan du länka ett Azure Storage-konto till en Azure-datafabrik med hjälp av en SAS (Shared Access Signature). Det ger datafabriken begränsad/tidsbunden åtkomst till alla/specifika resurser (blob/container) i lagringen. Om du vill länka ditt Azure-lagringskonto till en datafabrik med hjälp av Signature för delad åtkomst skapar du en Azure Storage SAS-länkad tjänst. Om du vill definiera en Azure Storage SAS-länkad tjänst anger du **typen** av länkad tjänst till **AzureStorageSas**. Sedan kan du ange följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Typegenskapen måste anges till: **AzureStorageSas** |Ja |
| sasuri (samma sak) |Ange URI för delad åtkomstsignatur till Azure Storage-resurser som blob, behållare eller tabell. |Ja |

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

Mer information om dessa länkade tjänster finns i artikel [i Azure Table Storage connector.](data-factory-azure-table-connector.md#linked-service-properties)

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en Azure Table-datauppsättning anger du **typen** av datauppsättningen till **AzureTable**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i azure table database-instansen som länkade tjänsten refererar till. |Ja. När ett tabellnamn anges utan en azureTableSourceQuery kopieras alla poster från tabellen till målet. Om en azureTableSourceQuery också anges kopieras poster från tabellen som uppfyller frågan till målet. |

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

Mer information om dessa länkade tjänster finns i artikel [i Azure Table Storage connector.](data-factory-azure-table-connector.md#dataset-properties)

### <a name="azure-table-source-in-copy-activity"></a>Azure-tabellkälla i kopieringsaktivitet
Om du kopierar data från Azure Table Storage anger du **källtypen** för kopieringsaktiviteten till **AzureTableSource**och anger följande egenskaper i **källavsnittet:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| azureTableSourceQuery |Använd den anpassade frågan för att läsa data. |Azure-tabellfrågesträng. Se exempel i nästa avsnitt. |Nej. När ett tabellnamn anges utan en azureTableSourceQuery kopieras alla poster från tabellen till målet. Om en azureTableSourceQuery också anges kopieras poster från tabellen som uppfyller frågan till målet. |
| azureTableSourceIgnoreTableNotFound |Ange om det inte finns något undantag för tabellen. |TRUE<br/>FALSE |Inga |

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

Mer information om dessa länkade tjänster finns i artikel [i Azure Table Storage connector.](data-factory-azure-table-connector.md#copy-activity-properties)

### <a name="azure-table-sink-in-copy-activity"></a>Azure tabellmottagare i kopieringsaktivitet
Om du kopierar data till Azure Table Storage anger du **sink-typen** för kopieringsaktiviteten till **AzureTableSink**och anger följande egenskaper i **handfatavsnittet:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Standardpartitionsnyckelvärde som kan användas av diskhon. |Ett strängvärde. |Inga |
| azureTablePartitionKeyName |Ange namnet på den kolumn vars värden används som partitionsnycklar. Om inget anges används AzureTableDefaultPartitionKeyValue som partitionsnyckel. |Ett kolumnnamn. |Inga |
| azureTableRowKeyName |Ange namnet på den kolumn vars kolumnvärden används som radnyckel. Om inget anges använder du ett GUID för varje rad. |Ett kolumnnamn. |Inga |
| azureTableInsertType |Läget för att infoga data i Azure-tabellen.<br/><br/>Den här egenskapen styr om befintliga rader i utdatatabellen med matchande partitions- och radnycklar har sina värden ersatta eller sammanfogade. <br/><br/>Mer information om hur dessa inställningar (sammanfoga och ersätta) fungerar finns i [Infoga eller sammanfoga entitetsavsnitt](https://msdn.microsoft.com/library/azure/hh452241.aspx) och [Infoga eller ersätta entitetsavsnitt.](https://msdn.microsoft.com/library/azure/hh452242.aspx) <br/><br> Den här inställningen gäller på radnivå, inte tabellnivån, och inget av alternativen tar bort rader i utdatatabellen som inte finns i indata. |sammanfoga (standard)<br/>Ersätta |Inga |
| skriverBatchSize |Infogar data i Azure-tabellen när writeBatchSize eller writeBatchTimeout träffas. |Heltal (antal rader) |Nej (standard: 10000) |
| skriverBatchTimeout |Infogar data i Azure-tabellen när writeBatchSize eller writeBatchTimeout träffas |Gått<br/><br/>Exempel: "00:20:00" (20 minuter) |Nej (Standard för standardtidsgränsen för lagringsklient 90 sek) |

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
Mer information om dessa länkade tjänster finns i artikel [i Azure Table Storage connector.](data-factory-azure-table-connector.md#copy-activity-properties)

## <a name="amazon-redshift"></a>Amazon RedShift

### <a name="linked-service"></a>Länkad tjänst
Om du vill definiera en Länkad Amazon Redshift-tjänst anger du **typen** av länkad tjänst till **AmazonRedshift**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| server |IP-adress eller värdnamn för Amazon Redshift-servern. |Ja |
| port |Numret på TCP-porten som Amazon Redshift-servern använder för att lyssna efter klientanslutningar. |Nej, standardvärde: 5439 |
| databas |Namnet på Amazon Redshift-databasen. |Ja |
| användarnamn |Namn på den användare som har åtkomst till databasen. |Ja |
| password |Lösenord för användarkontot. |Ja |

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

Mer information finns i artikeln Amazon Redshift connector.

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en Amazon Redshift-datauppsättning anger du **typen** av datauppsättningen på **RelationalTable**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i Amazon Redshift-databasen som länkade tjänsten refererar till. |Nej (om **frågan om** **RelationalSource** har angetts) |


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
Mer information finns i artikeln Amazon Redshift connector.

### <a name="relational-source-in-copy-activity"></a>Relationskälla i kopieringsaktivitet
Om du kopierar data från Amazon Redshift anger du **källtypen** för kopieringsaktiviteten på **RelationalSource**och anger följande egenskaper i **källavsnittet:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd den anpassade frågan för att läsa data. |SQL-frågesträng. Till exempel: `select * from MyTable`. |Nej (om **tabellNamn för** datauppsättning har **angetts)** |

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
Mer information finns i artikeln Amazon Redshift connector.

## <a name="ibm-db2"></a>IBM DB2

### <a name="linked-service"></a>Länkad tjänst
Om du vill definiera en IBM DB2-länkad tjänst anger du **typen** av länkad tjänst till **OnPremisesDB2**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| server |Namn på DB2-servern. |Ja |
| databas |Namn på DB2-databasen. |Ja |
| Schemat |Namnet på schemat i databasen. Schemanamnet är skiftlägeskänsligt. |Inga |
| authenticationType |Typ av autentisering som används för att ansluta till DB2-databasen. Möjliga värden är: Anonym, Basic och Windows. |Ja |
| användarnamn |Ange användarnamn om du använder Grundläggande autentisering eller Windows-autentisering. |Inga |
| password |Ange lösenord för det användarkonto som du angav för användarnamnet. |Inga |
| gatewayName (gatewayName) |Namn på den gateway som datafabrikstjänsten ska använda för att ansluta till den lokala DB2-databasen. |Ja |

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
Mer information finns i IBM DB2-anslutningsartikel.

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en DB2-datauppsättning anger du **typen** av datauppsättningen på **RelationalTable**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i DB2 Database-instansen som länkade tjänsten refererar till. TableName är skiftlägeskänsligt. |Nej (om **frågan om** **RelationalSource** har angetts)

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

Mer information finns i IBM DB2-anslutningsartikel.

### <a name="relational-source-in-copy-activity"></a>Relationskälla i kopieringsaktivitet
Om du kopierar data från IBM DB2 anger du **källtypen** för kopieringsaktiviteten till **RelationalSource**och anger följande egenskaper i **källavsnittet:**


| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd den anpassade frågan för att läsa data. |SQL-frågesträng. Till exempel: `"query": "select * from "MySchema"."MyTable""`. |Nej (om **tabellNamn för** datauppsättning har **angetts)** |

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
Mer information finns i IBM DB2-anslutningsartikel.

## <a name="mysql"></a>MySQL

### <a name="linked-service"></a>Länkad tjänst
Om du vill definiera en MySQL-länkad tjänst anger du **typen** av länkad tjänst till **OnPremisesMySql**och anger följande egenskaper i avsnittet **typEgenskaper:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| server |Namnet på MySQL-servern. |Ja |
| databas |Namn på MySQL-databasen. |Ja |
| Schemat |Namnet på schemat i databasen. |Inga |
| authenticationType |Typ av autentisering som används för att ansluta till MySQL-databasen. Möjliga värden `Basic`är: . |Ja |
| userName |Ange användarnamn som ska anslutas till MySQL-databasen. |Ja |
| password |Ange lösenord för det angivna användarkontot. |Ja |
| gatewayName (gatewayName) |Namnet på den gateway som datafabrikstjänsten ska använda för att ansluta till den lokala MySQL-databasen. |Ja |

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

Mer information finns i [MySQL-anslutningsartikel.](data-factory-onprem-mysql-connector.md#linked-service-properties)

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en MySQL-datauppsättning anger du **typen** av datauppsättningen på **RelationalTable**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i mysql-databasinstansen som länkade tjänsten refererar till. |Nej (om **frågan om** **RelationalSource** har angetts) |

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
Mer information finns i [MySQL-anslutningsartikel.](data-factory-onprem-mysql-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Relationskälla i kopieringsaktivitet
Om du kopierar data från en MySQL-databas anger du **källtypen** för kopieringsaktiviteten till **RelationalSource**och anger följande egenskaper i **källavsnittet:**


| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd den anpassade frågan för att läsa data. |SQL-frågesträng. Till exempel: `select * from MyTable`. |Nej (om **tabellNamn för** datauppsättning har **angetts)** |


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

Mer information finns i [MySQL-anslutningsartikel.](data-factory-onprem-mysql-connector.md#copy-activity-properties)

## <a name="oracle"></a>Oracle

### <a name="linked-service"></a>Länkad tjänst
Om du vill definiera en Oracle-länkad tjänst anger du **typen** av länkad tjänst till **OnPremisesOracle**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| driverType | Ange vilken drivrutin som ska användas för att kopiera data från/till Oracle-databasen. Tillåtna värden är **Microsoft** eller **ODP** (standard). Se Version och installation som stöds om drivrutinsinformation. | Inga |
| Connectionstring | Ange information som behövs för att ansluta till Oracle-databasinstansen för egenskapen connectionString. | Ja |
| gatewayName (gatewayName) | Namnet på gatewayen som används för att ansluta till den lokala Oracle-servern |Ja |

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

Mer information finns i [oracle-anslutningsartikel.](data-factory-onprem-oracle-connector.md#linked-service-properties)

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en Oracle-datauppsättning anger du **typen** av datauppsättning till **OracleTable**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i Oracle-databasen som den länkade tjänsten refererar till. |Nej (om **oracleReaderQuery** of **OracleSource** har angetts) |

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
Mer information finns i [oracle-anslutningsartikel.](data-factory-onprem-oracle-connector.md#dataset-properties)

### <a name="oracle-source-in-copy-activity"></a>Oracle-källa i kopieringsaktivitet
Om du kopierar data från en Oracle-databas anger du **källtypen** för kopieringsaktiviteten till **OracleSource**och anger följande egenskaper i **källavsnittet:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| orakelLäsareQuery |Använd den anpassade frågan för att läsa data. |SQL-frågesträng. Exempel: `select * from MyTable` <br/><br/>Om inget anges körs SQL-uttrycket som körs:`select * from MyTable` |Nej (om **tabellNamn för** datauppsättning har **angetts)** |

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

Mer information finns i [oracle-anslutningsartikel.](data-factory-onprem-oracle-connector.md#copy-activity-properties)

### <a name="oracle-sink-in-copy-activity"></a>Oracle Sink i kopiera aktivitet
Om du kopierar data till Oracle-databasen anger du **diskhontypen** för kopieringsaktiviteten till **OracleSink**och anger följande egenskaper i **handfatavsnittet:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| skriverBatchTimeout |Vänta på att batchinsatsen ska slutföras innan den har klarats upp. |Gått<br/><br/> Exempel: 00:30:00 (30 minuter). |Inga |
| skriverBatchSize |Infogar data i SQL-tabellen när buffertstorleken når writeBatchSize. |Heltal (antal rader) |Nej (standard: 100) |
| sqlWriterCleanupScript |Ange en fråga för Kopieringsaktivitet som ska köras så att data för ett visst segment rensas. |En frågesats. |Inga |
| sliceIdentifierColumnName |Ange kolumnnamn för Kopiera aktivitet som ska fyllas med automatisk genererad segmentidentifierare, som används för att rensa data från ett visst segment när den körs igen. |Kolumnnamnet på en kolumn med datatyp av binär(32). |Inga |

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
Mer information finns i [oracle-anslutningsartikel.](data-factory-onprem-oracle-connector.md#copy-activity-properties)

## <a name="postgresql"></a>PostgreSQL

### <a name="linked-service"></a>Länkad tjänst
Om du vill definiera en PostgreSQL-länkad tjänst anger du **typen** av länkad tjänst till **OnPremisesPostgreSql**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| server |Namnet på PostgreSQL-servern. |Ja |
| databas |Namn på PostgreSQL-databasen. |Ja |
| Schemat |Namnet på schemat i databasen. Schemanamnet är skiftlägeskänsligt. |Inga |
| authenticationType |Typ av autentisering som används för att ansluta till PostgreSQL-databasen. Möjliga värden är: Anonym, Basic och Windows. |Ja |
| användarnamn |Ange användarnamn om du använder Grundläggande autentisering eller Windows-autentisering. |Inga |
| password |Ange lösenord för det användarkonto som du angav för användarnamnet. |Inga |
| gatewayName (gatewayName) |Namn på den gateway som datafabrikstjänsten ska använda för att ansluta till den lokala PostgreSQL-databasen. |Ja |

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
Mer information finns i [PostgreSQL-kopplingsartikel.](data-factory-onprem-postgresql-connector.md#linked-service-properties)

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en PostgreSQL-datauppsättning anger du **typen** av datauppsättningen till **RelationalTable**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i postgreSQL-databasinstansen som länkad tjänst refererar till. TableName är skiftlägeskänsligt. |Nej (om **frågan om** **RelationalSource** har angetts) |

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
Mer information finns i [PostgreSQL-kopplingsartikel.](data-factory-onprem-postgresql-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Relationskälla i kopieringsaktivitet
Om du kopierar data från en PostgreSQL-databas anger du **källtypen** för kopieringsaktiviteten till **RelationalSource**och anger följande egenskaper i **källavsnittet:**


| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd den anpassade frågan för att läsa data. |SQL-frågesträng. Till exempel: "fråga": "välj * från \"MySchema\". \"MyTable\"". |Nej (om **tabellNamn för** datauppsättning har **angetts)** |

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

Mer information finns i [PostgreSQL-kopplingsartikel.](data-factory-onprem-postgresql-connector.md#copy-activity-properties)

## <a name="sap-business-warehouse"></a>SAP Business Warehouse


### <a name="linked-service"></a>Länkad tjänst
Om du vill definiera en SAP Business Warehouse (BW) länkad tjänst anger du **typen** av den länkade tjänsten till **SapBw**och anger följande egenskaper i avsnittet **typeProperties:**

Egenskap | Beskrivning | Tillåtna värden | Krävs
-------- | ----------- | -------------- | --------
server | Namn på den server där SAP BW-instansen finns. | sträng | Ja
systemNummer | Systemnummer för SAP BW-systemet. | Tvåsiffrigt decimaltal representeras som en sträng. | Ja
ClientID | Klient-ID för klienten i SAP W-systemet. | Tresiffrigt decimaltal representeras som en sträng. | Ja
användarnamn | Namn på den användare som har åtkomst till SAP-servern | sträng | Ja
password | Lösenordet för användaren. | sträng | Ja
gatewayName (gatewayName) | Namnet på den gateway som datafabrikstjänsten ska använda för att ansluta till den lokala SAP BW-instansen. | sträng | Ja
krypteradKnuren | Den krypterade referenssträngen. | sträng | Inga

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

Mer information finns i artikel [om SAP Business Warehouse-anslutning.](data-factory-sap-business-warehouse-connector.md#linked-service-properties)

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en SAP BW-datauppsättning anger du **typen** av datauppsättningen till **RelationalTable**. Det finns inga typspecifika egenskaper som stöds för SAP BW-datauppsättningen för typen **RelationalTable**.

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
Mer information finns i artikel [om SAP Business Warehouse-anslutning.](data-factory-sap-business-warehouse-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Relationskälla i kopieringsaktivitet
Om du kopierar data från SAP Business Warehouse anger du **källtypen** för kopieringsaktiviteten till **RelationalSource**och anger följande egenskaper i **källavsnittet:**


| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB | Anger mdx-frågan för att läsa data från SAP BW-instansen. | MDX-fråga. | Ja |

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

Mer information finns i artikel [om SAP Business Warehouse-anslutning.](data-factory-sap-business-warehouse-connector.md#copy-activity-properties)

## <a name="sap-hana"></a>SAP HANA

### <a name="linked-service"></a>Länkad tjänst
Om du vill definiera en SAP HANA-länkad tjänst anger du **typen** av länkad tjänst till **SapHana**och anger följande egenskaper i avsnittet **typeProperties:**

Egenskap | Beskrivning | Tillåtna värden | Krävs
-------- | ----------- | -------------- | --------
server | Namn på den server där SAP HANA-instansen finns. Om servern använder en anpassad `server:port`port anger du . | sträng | Ja
authenticationType | Typ av autentisering. | Sträng. "Basic" eller "Windows" | Ja
användarnamn | Namn på den användare som har åtkomst till SAP-servern | sträng | Ja
password | Lösenordet för användaren. | sträng | Ja
gatewayName (gatewayName) | Namnet på den gateway som datafabrikstjänsten ska använda för att ansluta till den lokala SAP HANA-instansen. | sträng | Ja
krypteradKnuren | Den krypterade referenssträngen. | sträng | Inga

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
Mer information finns i [SAP HANA-anslutningsartikel.](data-factory-sap-hana-connector.md#linked-service-properties)

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en SAP HANA-datauppsättning anger du **typen** av datauppsättningen till **RelationalTable**. Det finns inga typspecifika egenskaper som stöds för SAP HANA-datauppsättningen för typen **RelationalTable**.

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
Mer information finns i [SAP HANA-anslutningsartikel.](data-factory-sap-hana-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Relationskälla i kopieringsaktivitet
Om du kopierar data från ett SAP HANA-datalager anger du **källtypen** för kopieringsaktiviteten till **RelationalSource**och anger följande egenskaper i **källavsnittet:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB | Anger den SQL-fråga som ska läsas data från SAP HANA-instansen. | SQL-fråga. | Ja |


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

Mer information finns i [SAP HANA-anslutningsartikel.](data-factory-sap-hana-connector.md#copy-activity-properties)


## <a name="sql-server"></a>SQL Server

### <a name="linked-service"></a>Länkad tjänst
Du skapar en länkad tjänst av typen **OnPremisesSqlServer** för att länka en lokal SQL Server-databas till en datafabrik. I följande tabell beskrivs beskrivning av JSON-element som är specifika för den lokala SQL Server-länkade tjänsten.

I följande tabell beskrivs beskrivning av JSON-element som är specifika för SQL Server-länkade tjänst.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen Type ska anges till: **OnPremisesSqlServer**. |Ja |
| Connectionstring |Ange anslutningStringsinformation som behövs för att ansluta till den lokala SQL Server-databasen med antingen SQL-autentisering eller Windows-autentisering. |Ja |
| gatewayName (gatewayName) |Namnet på den gateway som tjänsten Data Factory ska använda för att ansluta till den lokala SQL Server-databasen. |Ja |
| användarnamn |Ange användarnamn om du använder Windows-autentisering. Exempel: **användarnamn\\på domännamn**. |Inga |
| password |Ange lösenord för det användarkonto som du angav för användarnamnet. |Inga |

Du kan kryptera autentiseringsuppgifter med cmdleten **New-AzDataFactoryEncryptValue** och använda dem i anslutningssträngen enligt följande exempel (**Egenskapen EncryptedCredential):**

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

Om användarnamn och lösenord anges använder gatewayen dem för att personifiera det angivna användarkontot för att ansluta till den lokala SQL Server-databasen. Annars ansluter gatewayen till SQL Server direkt med säkerhetskontexten för Gateway (dess startkonto).

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

Mer information finns i [sql server-anslutningsartikel.](data-factory-sqlserver-connector.md#linked-service-properties)

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en SQL Server-datauppsättning anger du **typen** av datauppsättningen till **SqlServerTable**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen eller vyn i SQL Server Database-instansen som länkade tjänsten refererar till. |Ja |

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

Mer information finns i [sql server-anslutningsartikel.](data-factory-sqlserver-connector.md#dataset-properties)

### <a name="sql-source-in-copy-activity"></a>Sql-källa i kopieringsaktivitet
Om du kopierar data från en SQL Server-databas anger du **källtypen** för kopieringsaktiviteten till **SqlSource**och anger följande egenskaper i **källavsnittet:**


| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| sqlReaderQuery |Använd den anpassade frågan för att läsa data. |SQL-frågesträng. Till exempel: `select * from MyTable`. Kan referera till flera tabeller från databasen som refereras av indatauppsättningen. Om inget anges väljer SQL-uttrycket som körs: välj från MyTable. |Inga |
| sqlReaderStoredProcedureName |Namnet på den lagrade proceduren som läser data från källtabellen. |Namnet på den lagrade proceduren. |Inga |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värdepar. Namn och hölje av parametrar måste stämma överens med namn och hölje för de lagrade procedureparametrarna. |Inga |

Om **sqlReaderQuery** har angetts för SqlSource körs den här frågan mot SQL Server-databaskällan för att hämta data.

Du kan också ange en lagrad procedur genom att ange **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren tar parametrar).

Om du inte anger sqlReaderQuery eller sqlReaderStoredProcedureName används kolumnerna som definieras i strukturavsnittet för att skapa en urvalsfråga som ska köras mot SQL Server-databasen. Om datauppsättningsdefinitionen inte har strukturen markeras alla kolumner i tabellen.

> [!NOTE]
> När du använder **sqlReaderStoredProcedureName**måste du fortfarande ange ett värde för **egenskapen tableName** i datauppsättningen JSON. Det finns inga valideringar som utförs mot den här tabellen dock.


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

I det här exemplet anges **sqlReaderQuery** för SqlSource. Kopieringsaktiviteten kör den här frågan mot SQL Server-databaskällan för att hämta data. Du kan också ange en lagrad procedur genom att ange **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren tar parametrar). SqlReaderQuery kan referera till flera tabeller i databasen som refereras av indatauppsättningen. Den är inte begränsad till endast den tabell som har angetts som datauppsättningens tableName typeProperty.

Om du inte anger sqlReaderQuery eller sqlReaderStoredProcedureName används kolumnerna som definieras i strukturavsnittet för att skapa en urvalsfråga som ska köras mot SQL Server-databasen. Om datauppsättningsdefinitionen inte har strukturen markeras alla kolumner i tabellen.

Mer information finns i [sql server-anslutningsartikel.](data-factory-sqlserver-connector.md#copy-activity-properties)

### <a name="sql-sink-in-copy-activity"></a>Sql Sink i kopieringsaktivitet
Om du kopierar data till en SQL Server-databas anger du **sink-typen** för kopieringsaktiviteten till **SqlSink**och anger följande egenskaper i **handfatavsnittet:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| skriverBatchTimeout |Vänta på att batchinsatsen ska slutföras innan den har klarats upp. |Gått<br/><br/> Exempel: "00:30:00" (30 minuter). |Inga |
| skriverBatchSize |Infogar data i SQL-tabellen när buffertstorleken når writeBatchSize. |Heltal (antal rader) |Nej (standard: 10000) |
| sqlWriterCleanupScript |Ange fråga för Kopieringsaktivitet som ska köras så att data för ett visst segment rensas. Mer information finns i avsnittet repeterbarhet. |En frågesats. |Inga |
| sliceIdentifierColumnName |Ange kolumnnamn för Kopiera aktivitet som ska fyllas med automatisk genererad segmentidentifierare, som används för att rensa data från ett visst segment när den körs igen. Mer information finns i avsnittet repeterbarhet. |Kolumnnamnet på en kolumn med datatyp av binär(32). |Inga |
| sqlWriterStoredProcedureName |Namn på den lagrade procedur som lägger till (uppdateringar/infogar) data i måltabellen. |Namnet på den lagrade proceduren. |Inga |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värdepar. Namn och hölje av parametrar måste stämma överens med namn och hölje för de lagrade procedureparametrarna. |Inga |
| sqlWriterTableType |Ange tabelltypsnamn som ska användas i den lagrade proceduren. Kopieringsaktivitet gör de data som flyttas tillgängliga i en temp-tabell med den här tabelltypen. Lagrad procedurkod kan sedan sammanfoga data som kopieras med befintliga data. |Ett tabelltypnamn. |Inga |

#### <a name="example"></a>Exempel
Pipelinen innehåller en kopieringsaktivitet som är konfigurerad för att använda dessa in- och utdatauppsättningar och som är schemalagd att köras varje timme. I pipeline-JSON-definitionen anges **källtypen** till **BlobSource** och **sink-typen** är inställd på **SqlSink**.

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

Mer information finns i [sql server-anslutningsartikel.](data-factory-sqlserver-connector.md#copy-activity-properties)

## <a name="sybase"></a>Sybase

### <a name="linked-service"></a>Länkad tjänst
Om du vill definiera en Sybase-länkad tjänst anger du **typen** av länkad tjänst till **OnPremisesSybase**och anger följande egenskaper i avsnittet **typEgenskaper:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| server |Namn på Sybase-servern. |Ja |
| databas |Namn på Sybase-databasen. |Ja |
| Schemat |Namnet på schemat i databasen. |Inga |
| authenticationType |Typ av autentisering som används för att ansluta till Sybase-databasen. Möjliga värden är: Anonym, Basic och Windows. |Ja |
| användarnamn |Ange användarnamn om du använder Grundläggande autentisering eller Windows-autentisering. |Inga |
| password |Ange lösenord för det användarkonto som du angav för användarnamnet. |Inga |
| gatewayName (gatewayName) |Namnet på den gateway som datafabrikstjänsten ska använda för att ansluta till den lokala Sybase-databasen. |Ja |

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

Mer information finns i [Sybase](data-factory-onprem-sybase-connector.md#linked-service-properties) connector-artikel.

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en Sybase-datauppsättning anger du **typen** av datauppsättningen på **RelationalTable**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i Sybase Database-instansen som länkade tjänsten refererar till. |Nej (om **frågan om** **RelationalSource** har angetts) |

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

Mer information finns i [Sybase](data-factory-onprem-sybase-connector.md#dataset-properties) connector-artikel.

### <a name="relational-source-in-copy-activity"></a>Relationskälla i kopieringsaktivitet
Om du kopierar data från en Sybase-databas anger du **källtypen** för kopieringsaktiviteten till **RelationalSource**och anger följande egenskaper i **källavsnittet:**


| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd den anpassade frågan för att läsa data. |SQL-frågesträng. Till exempel: `select * from MyTable`. |Nej (om **tabellNamn för** datauppsättning har **angetts)** |

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

Mer information finns i [Sybase](data-factory-onprem-sybase-connector.md#copy-activity-properties) connector-artikel.

## <a name="teradata"></a>Teradata

### <a name="linked-service"></a>Länkad tjänst
Om du vill definiera en Teradata-länkad tjänst anger du **typen** av länkad tjänst till **OnPremisesTeradata**och anger följande egenskaper i avsnittet **typEgenskaper:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| server |Namnet på Teradata-servern. |Ja |
| authenticationType |Typ av autentisering som används för att ansluta till Teradata-databasen. Möjliga värden är: Anonym, Basic och Windows. |Ja |
| användarnamn |Ange användarnamn om du använder Grundläggande autentisering eller Windows-autentisering. |Inga |
| password |Ange lösenord för det användarkonto som du angav för användarnamnet. |Inga |
| gatewayName (gatewayName) |Namnet på den gateway som datafabrikstjänsten ska använda för att ansluta till den lokala Teradata-databasen. |Ja |

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

Mer information finns i [Teradata](data-factory-onprem-teradata-connector.md#linked-service-properties) connector-artikeln.

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en Teradata Blob-datauppsättning anger du **typen** av datauppsättningen till **RelationalTable**. För närvarande finns det inga typegenskaper som stöds för Teradata-datauppsättningen.

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

Mer information finns i [Teradata](data-factory-onprem-teradata-connector.md#dataset-properties) connector-artikeln.

### <a name="relational-source-in-copy-activity"></a>Relationskälla i kopieringsaktivitet
Om du kopierar data från en Teradata-databas anger du **källtypen** för kopieringsaktiviteten till **RelationalSource**och anger följande egenskaper i **källavsnittet:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd den anpassade frågan för att läsa data. |SQL-frågesträng. Till exempel: `select * from MyTable`. |Ja |

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

Mer information finns i [Teradata](data-factory-onprem-teradata-connector.md#copy-activity-properties) connector-artikeln.

## <a name="cassandra"></a>Cassandra


### <a name="linked-service"></a>Länkad tjänst
Om du vill definiera en Cassandra-länkad tjänst anger du **typen** av länkad tjänst till **OnPremisesCassandra**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| värd |En eller flera IP-adresser eller värdnamn för Cassandra-servrar.<br/><br/>Ange en kommaavgränsad lista med IP-adresser eller värdnamn som ska anslutas till alla servrar samtidigt. |Ja |
| port |TCP-porten som Cassandra-servern använder för att lyssna efter klientanslutningar. |Nej, standardvärde: 9042 |
| authenticationType |Grundläggande eller anonym |Ja |
| användarnamn |Ange användarnamn för användarkontot. |Ja, om authenticationType är inställt på Basic. |
| password |Ange lösenord för användarkontot. |Ja, om authenticationType är inställt på Basic. |
| gatewayName (gatewayName) |Namnet på gatewayen som används för att ansluta till den lokala Cassandra-databasen. |Ja |
| krypteradKnuren |Autentiseringsuppgifter krypteras av gatewayen. |Inga |

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

Mer information finns i [artikeln med Cassandra-anslutning.](data-factory-onprem-cassandra-connector.md#linked-service-properties)

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en Cassandra-datauppsättning anger du **typen** av datauppsättningen till **CassandraTable**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| nyckelutrymme |Namn på nyckelutrymmet eller schemat i Cassandra-databasen. |Ja (Om **frågan** för **CassandraSource** inte har definierats). |
| tableName |Namnet på tabellen i Cassandra-databasen. |Ja (Om **frågan** för **CassandraSource** inte har definierats). |

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

Mer information finns i [artikeln med Cassandra-anslutning.](data-factory-onprem-cassandra-connector.md#dataset-properties)

### <a name="cassandra-source-in-copy-activity"></a>Cassandra källa i kopieringsaktivitet
Om du kopierar data från Cassandra anger du **källtypen** för kopieringsaktiviteten till **CassandraSource**och anger följande egenskaper i **källavsnittet:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd den anpassade frågan för att läsa data. |SQL-92-fråga eller CQL-fråga. Se [CQL-referens](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>När du använder SQL-frågan anger du **namnet keyspace name.table** som ska representera den tabell som du vill fråga. |Nej (om tableName och keyspace på datauppsättning definieras). |
| konsekvensNivå |Konsekvensnivån anger hur många repliker som måste svara på en läsbegäran innan data returneras till klientprogrammet. Cassandra kontrollerar det angivna antalet repliker för data för att uppfylla läsbegäran. |ETT, TVÅ, TRE, KVORUM, ALLA, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. Mer information [finns i Konfigurera datakonsekvens.](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) |Nej. Standardvärdet är ETT. |

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

Mer information finns i [artikeln med Cassandra-anslutning.](data-factory-onprem-cassandra-connector.md#copy-activity-properties)

## <a name="mongodb"></a>MongoDB

### <a name="linked-service"></a>Länkad tjänst
Om du vill definiera en MongoDB-länkad tjänst anger du **typen** av länkad tjänst till **OnPremisesMongoDB**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| server |IP-adress eller värdnamn för MongoDB-servern. |Ja |
| port |TCP-port som MongoDB-servern använder för att lyssna efter klientanslutningar. |Valfritt standardvärde: 27017 |
| authenticationType |Grundläggande eller anonym. |Ja |
| användarnamn |Användarkonto för att komma åt MongoDB. |Ja (om grundläggande autentisering används). |
| password |Lösenordet för användaren. |Ja (om grundläggande autentisering används). |
| authSource |Namnet på den MongoDB-databas som du vill använda för att kontrollera dina autentiseringsuppgifter. |Valfritt (om grundläggande autentisering används). standard: använder administratörskontot och databasen som anges med hjälp av egenskapen databaseName. |
| Databasename |Namn på den MongoDB-databas som du vill komma åt. |Ja |
| gatewayName (gatewayName) |Namn på den gateway som kommer åt datalagret. |Ja |
| krypteradKnuren |Autentiseringsuppgifter krypteras av gateway. |Valfri |

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

Mer information finns i [mongoDb-anslutningsartikel](data-factory-on-premises-mongodb-connector.md#linked-service-properties)

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en MongoDB-datauppsättning anger du **typen** av datauppsättningen till **MongoDbCollection**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| collectionName (samlingsnamn) |Namn på samlingen i MongoDB-databasen. |Ja |

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

Mer information finns i [mongoDb-anslutningsartikel](data-factory-on-premises-mongodb-connector.md#dataset-properties)

#### <a name="mongodb-source-in-copy-activity"></a>MongoDB Källa i kopiera aktivitet
Om du kopierar data från MongoDB anger du **källtypen** för kopieringsaktiviteten till **MongoDbSource**och anger följande egenskaper i **källavsnittet:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd den anpassade frågan för att läsa data. |SQL-92-frågesträngen. Till exempel: `select * from MyTable`. |Nej (om **insamlingNamn** för datauppsättning har **angetts)** |

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

Mer information finns i [mongoDb-anslutningsartikel](data-factory-on-premises-mongodb-connector.md#copy-activity-properties)

## <a name="amazon-s3"></a>Amazon S3


### <a name="linked-service"></a>Länkad tjänst
Om du vill definiera en Amazon S3-länkad tjänst anger du **typen** av länkad tjänst till **AwsAccessKey**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| accessKeyID |ID för den hemliga åtkomstnyckeln. |sträng |Ja |
| secretAccessKey |Den hemliga åtkomstnyckeln själv. |Krypterad hemlig sträng |Ja |

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

Mer information finns i [Amazon S3-anslutningsartikel](data-factory-amazon-simple-storage-service-connector.md#linked-service-properties).

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en Amazon S3-datauppsättning anger du **typen** av datauppsättningen till **AmazonS3**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| bucketName (bucketName) |S3-hinknamnet. |String |Ja |
| key |S3-objektnyckeln. |String |Inga |
| Prefix |Prefix för S3-objektnyckeln. Objekt vars nycklar börjar med det här prefixet är markerade. Gäller endast när nyckeln är tom. |String |Inga |
| version |Versionen av S3-objektet om S3-versionshantering är aktiverad. |String |Inga |
| format | Följande formattyper stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange **type** typegenskapen under format till ett av dessa värden. Mer information finns i avsnitten [Textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)och [Parquet Format.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Om du vill **kopiera filer som de är** mellan filbaserade butiker (binär kopia) hoppar du över formatavsnittet i definitionerna för både in- och utdatadata. |Inga | |
| komprimering | Ange typ och komprimeringsnivå för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**och **ZipDeflate**. Nivåerna som stöds är: **Optimal** och **Snabbast**. Mer information finns [i Fil- och komprimeringsformat i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Inga | |


> [!NOTE]
> bucketName + nyckel anger platsen för S3-objektet där bucket är rotbehållaren för S3-objekt och nyckeln är den fullständiga sökvägen till S3-objektet.

#### <a name="example-sample-dataset-with-prefix"></a>Exempel: Exempeldatauppsättning med prefix

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

#### <a name="example-dynamic-paths-for-s3"></a>Exempel: Dynamiska banor för S3
I exemplet använder vi fasta värden för nyckel- och bucketName-egenskaper i Amazon S3-datauppsättningen.

```json
"key": "testFolder/test.orc",
"bucketName": "<S3 bucket name>",
```

Du kan låta Data Factory beräkna nyckeln och bucketName dynamiskt vid körning med hjälp av systemvariabler som SliceStart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Du kan göra samma sak för prefixet egenskapen för en Amazon S3-datauppsättning. Se [Data Factory-funktioner och systemvariabler](data-factory-functions-variables.md) för en lista över funktioner och variabler som stöds.

Mer information finns i [Amazon S3-anslutningsartikel](data-factory-amazon-simple-storage-service-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Filsystemkälla i kopieringsaktivitet
Om du kopierar data från Amazon S3 anger du **källtypen** för kopieringsaktiviteten till **FileSystemSource**och anger följande egenskaper i **källavsnittet:**


| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| Rekursiv |Anger om S3-objekt ska anges rekursivt under katalogen. |sant/falskt |Inga |


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

Mer information finns i [Amazon S3-anslutningsartikel](data-factory-amazon-simple-storage-service-connector.md#copy-activity-properties).

## <a name="file-system"></a>Filsystem


### <a name="linked-service"></a>Länkad tjänst
Du kan länka ett lokalt filsystem till en Azure-datafabrik med den **lokala filserverlänkade** tjänsten. I följande tabell finns beskrivningar av JSON-element som är specifika för den lokala filserverlänkade tjänsten.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Kontrollera att typegenskapen är inställd **på OnPremisesFileServer**. |Ja |
| värd |Anger rotsökvägen för den mapp som du vill kopiera. Använd escape-tecknet ' \ ' för specialtecken i strängen. Se Exempel på länkade tjänst- och datauppsättningsdefinitioner för exempel. |Ja |
| userid |Ange ID för den användare som har åtkomst till servern. |Nej (om du väljer encryptedCredential) |
| password |Ange lösenordet för användaren (userid). |Nej (om du väljer encryptedCredential |
| krypteradKnuren |Ange de krypterade autentiseringsuppgifter som du kan få genom att köra cmdleten New-AzDataFactoryEncryptValue. |Nej (om du väljer att ange userid och lösenord i oformaterad text) |
| gatewayName (gatewayName) |Anger namnet på den gateway som Data Factory ska använda för att ansluta till den lokala filservern. |Ja |

#### <a name="sample-folder-path-definitions"></a>Exempel på mappsökvägsdefinitioner

| Scenario | Värd i länkad tjänstdefinition | folderPath i definition av datauppsättning |
| --- | --- | --- |
| Lokal mapp på datahanteringsgateway-dator: <br/><br/>Exempel: D:\\ \* eller D:\folder\subfolder\\* |D:\\ \\ (för Data Management Gateway 2.0 och senare versioner) <br/><br/> localhost (för tidigare versioner än Data Management Gateway 2.0) |. eller\\\\mappundermapp (för Data Management Gateway 2.0 och senare versioner) \\ \\ <br/><br/>D:\\ \\ eller\\\\D: mappundermapp\\\\(för gateway version nedan 2.0) |
| Delad mapp för fjärr: <br/><br/>Exempel: \\ \\undermappen \\ \\dela\\\\\\\\ \* minserver\\eller minservermapp\\* |\\\\\\\\myserver\\\\dela |. eller mappundermapp\\\\ \\ \\ |


#### <a name="example-using-username-and-password-in-plain-text"></a>Exempel: Använda användarnamn och lösenord i oformaterad text

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

#### <a name="example-using-encryptedcredential"></a>Exempel: Använda krypteradkredential

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

Mer information finns i [artikeln Filsystemkoppling](data-factory-onprem-file-system-connector.md#linked-service-properties).

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en filsystemdatauppsättning anger du **typen** av datauppsättningen till **FileShare**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Anger undersökvägen till mappen. Använd escape-tecknet '\' för specialtecken i strängen. Se Exempel på länkade tjänst- och datauppsättningsdefinitioner för exempel.<br/><br/>Du kan kombinera den här egenskapen med **partitionBy** om du vill ha mappsökvägar baserat på start-/slutdatumtider för segment. |Ja |
| fileName |Ange namnet på filen i **folderPath** om du vill att tabellen ska referera till en viss fil i mappen. Om du inte anger något värde för den här egenskapen pekar tabellen på alla filer i mappen.<br/><br/>När filnamn inte har angetts för en utdatauppsättning är namnet på den genererade filen i följande format: <br/><br/>`Data.<Guid>.txt`(Exempel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Inga |
| filFilter |Ange ett filter som ska användas för att välja en delmängd av filer i folderPath i stället för alla filer. <br/><br/>Tillåtna värden `*` är: (flera tecken) och `?` (enstaka tecken).<br/><br/>Exempel 1: "fileFilter": "*.log"<br/>Exempel 2: "fileFilter": 2016-1-?. txt"<br/><br/>Observera att fileFilter är tillämpligt för en indatauppsättning fileshare.Note that fileFilter is applicable for an input FileShare dataset. |Inga |
| partitioneradAv |Du kan använda partitionedBy för att ange en dynamisk folderPath/fileName för tidsseriedata. Ett exempel är folderPath-parameteriserad för varje timme data. |Inga |
| format | Följande formattyper stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange **type** typegenskapen under format till ett av dessa värden. Mer information finns i avsnitten [Textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)och [Parquet Format.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Om du vill **kopiera filer som de är** mellan filbaserade butiker (binär kopia) hoppar du över formatavsnittet i definitionerna för både in- och utdatadata. |Inga |
| komprimering | Ange typ och komprimeringsnivå för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**och **ZipDeflate**; och nivåer som stöds är: **Optimal** och **snabbaste**. se [Fil- och komprimeringsformat i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Inga |

> [!NOTE]
> Du kan inte använda fileName och fileFilter samtidigt.

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

Mer information finns i [artikeln Filsystemkoppling](data-factory-onprem-file-system-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Filsystemkälla i kopieringsaktivitet
Om du kopierar data från Filsystemet anger du **källtypen** för kopieringsaktiviteten till **FileSystemSource**och anger följande egenskaper i **källavsnittet:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| Rekursiv |Anger om data läss rekursivt från undermapparna eller bara från den angivna mappen. |Sant, Falskt (standard) |Inga |

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
Mer information finns i [artikeln Filsystemkoppling](data-factory-onprem-file-system-connector.md#copy-activity-properties).

### <a name="file-system-sink-in-copy-activity"></a>Filsystemmottagare i kopieringsaktivitet
Om du kopierar data till Filsystem anger du **handfinktypen** för kopieringsaktiviteten till **FileSystemSink**och anger följande egenskaper i **handfatavsnittet:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| kopieraBehavior |Definierar kopieringsbeteendet när källan är BlobSource eller FileSystem. |**BevaraHierarchy:** Bevarar filhierarkin i målmappen. Det vill än, källfilens relativa sökväg till källmappen är samma som målfilens relativa sökväg till målmappen.<br/><br/>**FlattenHierarchy:** Alla filer från källmappen skapas i den första nivån i målmappen. Målfilerna skapas med ett autogenererat namn.<br/><br/>**MergeFiles:** Sammanfogar alla filer från källmappen till en fil. Om filnamnet/blobnamnet anges är det kopplade filnamnet det angivna namnet. Annars är det ett automatiskt genererat filnamn. |Inga |

auto-

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

Mer information finns i [artikeln Filsystemkoppling](data-factory-onprem-file-system-connector.md#copy-activity-properties).

## <a name="ftp"></a>FTP

### <a name="linked-service"></a>Länkad tjänst
Om du vill definiera en FTP-länkad tjänst anger du **typen** av länkad tjänst till **FtpServer**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs | Default |
| --- | --- | --- | --- |
| värd |FTP-serverns namn eller IP-adress |Ja |&nbsp; |
| authenticationType |Ange autentiseringstyp |Ja |Grundläggande, Anonym |
| användarnamn |Användare som har åtkomst till FTP-servern |Inga |&nbsp; |
| password |Lösenord för användaren (användarnamn) |Inga |&nbsp; |
| krypteradKnuren |Krypterad autentiseringsuppgifter för åtkomst till FTP-servern |Inga |&nbsp; |
| gatewayName (gatewayName) |Namnet på datahanteringsgatewayen för att ansluta till en lokal FTP-server |Inga |&nbsp; |
| port |Port som FTP-servern lyssnar på |Inga |21 |
| enableSsl enableSsl enableSsl enableS |Ange om FTP ska användas via SSL/TLS-kanal |Inga |true |
| aktiveraServerCertificateValidation |Ange om server-TLS/SSL-certifikatvalidering ska aktiveras när FTP används via SSL/TLS-kanal |Inga |true |

#### <a name="example-using-anonymous-authentication"></a>Exempel: Använda anonym autentisering

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

#### <a name="example-using-username-and-password-in-plain-text-for-basic-authentication"></a>Exempel: Använda användarnamn och lösenord i oformaterad text för grundläggande autentisering

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

#### <a name="example-using-port-enablessl-enableservercertificatevalidation"></a>Exempel: Använda port, enableSsl, enableServerCertificateValidation

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

#### <a name="example-using-encryptedcredential-for-authentication-and-gateway"></a>Exempel: Använda krypteradked för autentisering och gateway

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

Mer information finns i [FTP-anslutningsartikel.](data-factory-ftp-connector.md#linked-service-properties)

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en FTP-datauppsättning anger du **typen** av datauppsättningen till **FileShare**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Undersökväg till mappen. Använd escape-tecknet ' \ ' för specialtecken i strängen. Se Exempel på länkade tjänst- och datauppsättningsdefinitioner för exempel.<br/><br/>Du kan kombinera den här egenskapen med **partitionBy** om du vill ha mappsökvägar baserat på start-/slutdatumtider för segment. |Ja
| fileName |Ange namnet på filen i **folderPath** om du vill att tabellen ska referera till en viss fil i mappen. Om du inte anger något värde för den här egenskapen pekar tabellen på alla filer i mappen.<br/><br/>När filnamn inte har angetts för en utdatauppsättning, skulle namnet på den genererade filen vara i följande format: <br/><br/>`Data.<Guid>.txt`(Exempel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Inga |
| filFilter |Ange ett filter som ska användas för att välja en delmängd av filer i folderPath i stället för alla filer.<br/><br/>Tillåtna värden `*` är: (flera tecken) och `?` (enstaka tecken).<br/><br/>Exempel 1:`"fileFilter": "*.log"`<br/>Exempel 2:`"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter gäller för en indatafildeldatauppsättning. Den här egenskapen stöds inte med HDFS. |Inga |
| partitioneradAv |partitionedBy kan användas för att ange en dynamisk folderPath, filnamn för tidsseriedata. FolderPath har till exempel parameteriserats för varje timme data. |Inga |
| format | Följande formattyper stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange **type** typegenskapen under format till ett av dessa värden. Mer information finns i avsnitten [Textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)och [Parquet Format.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Om du vill **kopiera filer som de är** mellan filbaserade butiker (binär kopia) hoppar du över formatavsnittet i definitionerna för både in- och utdatadata. |Inga |
| komprimering | Ange typ och komprimeringsnivå för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**och **ZipDeflate**; och nivåer som stöds är: **Optimal** och **snabbaste**. Mer information finns [i Fil- och komprimeringsformat i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Inga |
| användaBinaryTransfer |Ange om binärt överföringsläge ska användas. Sant för binärt läge och falskt ASCII. Standardvärde: Sant. Den här egenskapen kan bara användas när associerad länkad tjänsttyp är av typen: FtpServer. |Inga |

> [!NOTE]
> filnamn och filFilter kan inte användas samtidigt.

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

Mer information finns i [FTP-anslutningsartikel.](data-factory-ftp-connector.md#dataset-properties)

### <a name="file-system-source-in-copy-activity"></a>Filsystemkälla i kopieringsaktivitet
Om du kopierar data från en FTP-server anger du **källtypen** för kopieringsaktiviteten till **FileSystemSource**och anger följande egenskaper i **källavsnittet:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| Rekursiv |Anger om data läss rekursivt från undermapparna eller bara från den angivna mappen. |Sant, Falskt (standard) |Inga |

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

Mer information finns i [FTP-anslutningsartikel.](data-factory-ftp-connector.md#copy-activity-properties)


## <a name="hdfs"></a>HDFS

### <a name="linked-service"></a>Länkad tjänst
Om du vill definiera en HDFS-länkad tjänst anger du **typen** av länkad tjänst till **Hdfs**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen Type måste ställas in på: **HDfs** |Ja |
| URL |URL till HDFS |Ja |
| authenticationType |Anonym, eller Windows. <br><br> Om du vill använda **Kerberos-autentisering** för HDFS-anslutning läser du det här avsnittet för att konfigurera den lokala miljön i enlighet med detta. |Ja |
| userName |Användarnamn för Windows-autentisering. |Ja (för Windows-autentisering) |
| password |Lösenord för Windows-autentisering. |Ja (för Windows-autentisering) |
| gatewayName (gatewayName) |Namnet på den gateway som datafabrikstjänsten ska använda för att ansluta till HDFS. |Ja |
| krypteradKnuren |[Ny-AzDataFactoryEncryptVärdevärdeutdata](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) för åtkomstautentiseringsuppgifterna. |Inga |

#### <a name="example-using-anonymous-authentication"></a>Exempel: Använda anonym autentisering

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

#### <a name="example-using-windows-authentication"></a>Exempel: Använda Windows-autentisering

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

Mer information finns i HDFS-anslutningsartikel.

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en HDFS-datauppsättning anger du **typen** av datauppsättningen till **FileShare**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Sökväg till mappen. Exempel: `myfolder`<br/><br/>Använd escape-tecknet ' \ ' för specialtecken i strängen. Till exempel: för mapp\undermapp\\\\anger du mappundermapp och för d:\samplefolder anger du d:\\\\samplefolder.<br/><br/>Du kan kombinera den här egenskapen med **partitionBy** om du vill ha mappsökvägar baserat på start-/slutdatumtider för segment. |Ja |
| fileName |Ange namnet på filen i **folderPath** om du vill att tabellen ska referera till en viss fil i mappen. Om du inte anger något värde för den här egenskapen pekar tabellen på alla filer i mappen.<br/><br/>När filnamn inte har angetts för en utdatauppsättning, skulle namnet på den genererade filen vara i följande format: <br/><br/>`Data.<Guid>.txt`(till exempel: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Inga |
| partitioneradAv |partitionedBy kan användas för att ange en dynamisk folderPath, filnamn för tidsseriedata. Exempel: folderPath parameteriserad för varje timme data. |Inga |
| format | Följande formattyper stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange **type** typegenskapen under format till ett av dessa värden. Mer information finns i avsnitten [Textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)och [Parquet Format.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Om du vill **kopiera filer som de är** mellan filbaserade butiker (binär kopia) hoppar du över formatavsnittet i definitionerna för både in- och utdatadata. |Inga |
| komprimering | Ange typ och komprimeringsnivå för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**och **ZipDeflate**. Nivåer som stöds är: **Optimal** och **snabbaste**. Mer information finns [i Fil- och komprimeringsformat i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Inga |

> [!NOTE]
> filnamn och filFilter kan inte användas samtidigt.

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

Mer information finns i HDFS-anslutningsartikel.

### <a name="file-system-source-in-copy-activity"></a>Filsystemkälla i kopieringsaktivitet
Om du kopierar data från HDFS anger du **källtypen** för kopieringsaktiviteten till **FileSystemSource**och anger följande egenskaper i **källavsnittet:**

**FileSystemSource** stöder följande egenskaper:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| Rekursiv |Anger om data läss rekursivt från undermapparna eller bara från den angivna mappen. |Sant, Falskt (standard) |Inga |

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

Mer information finns i HDFS-anslutningsartikel.

## <a name="sftp"></a>SFTP


### <a name="linked-service"></a>Länkad tjänst
Om du vill definiera en SFTP-länkad tjänst anger du **typen** av länkad tjänst till **Sftp**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| värd | Namn eller IP-adress på SFTP-servern. |Ja |
| port |Port som SFTP-servern lyssnar på. Standardvärdet är: 21 |Inga |
| authenticationType |Ange autentiseringstyp. Tillåtna värden: **Basic**, **SshPublicKey**. <br><br> Se Använda grundläggande autentisering och använda autentiseringsavsnitt för [SSH-offentliga nyckel](#using-ssh-public-key-authentication) på fler egenskaper respektive JSON-exempel. |Ja |
| hoppa överHostKeyValidation | Ange om värdnyckelverifiering ska ska skipas. | Nej. Standardvärdet: falskt |
| hostKeyFingerprint | Ange värdnyckelns fingeravtryck. | Ja om `skipHostKeyValidation` den är inställd på false.  |
| gatewayName (gatewayName) |Namnet på datahanteringsgatewayen för att ansluta till en lokal SFTP-server. | Ja om du kopierar data från en lokal SFTP-server. |
| krypteradKnuren | Krypterad autentiseringsuppgifter för åtkomst till SFTP-servern. Genereras automatiskt när du anger grundläggande autentisering (användarnamn + lösenord) eller SshPublicKey-autentisering (användarnamn + sökväg eller innehåll för privata nycklar) i kopieringsguiden eller popup-dialogrutan ClickOnce. | Nej. Använd endast när du kopierar data från en lokal SFTP-server. |

#### <a name="example-using-basic-authentication"></a>Exempel: Använda grundläggande autentisering

Om du vill `authenticationType` använda `Basic`grundläggande autentisering anger du som och anger följande egenskaper förutom de generiska SFTP-anslutningsapparna som introducerades i det sista avsnittet:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| användarnamn | Användare som har åtkomst till SFTP-servern. |Ja |
| password | Lösenord för användaren (användarnamn). | Ja |

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

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Exempel: **Grundläggande autentisering med krypterad autentiseringsuppgifter**

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

#### <a name="using-ssh-public-key-authentication"></a>**Använda autentisering av SSH-offentliga nyckel:**

Om du vill `authenticationType` använda `SshPublicKey`grundläggande autentisering anger du som och anger följande egenskaper förutom de generiska SFTP-anslutningsapparna som introducerades i det sista avsnittet:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| användarnamn |Användare som har åtkomst till SFTP-servern |Ja |
| privateKeyPath (privatKeyPath) | Ange absolut sökväg till den privata nyckelfilen som gatewayen kan komma åt. | Ange antingen `privateKeyPath` `privateKeyContent`eller . <br><br> Använd endast när du kopierar data från en lokal SFTP-server. |
| privatKeyContent | En serialiserad sträng med det privata nyckelinnehållet. Kopieringsguiden kan läsa den privata nyckelfilen och extrahera innehållet i den privata nyckeln automatiskt. Om du använder något annat verktyg/SDK använder du egenskapen privateKeyPath i stället. | Ange antingen `privateKeyPath` `privateKeyContent`eller . |
| Lösenfras | Ange lösenfrasen/lösenordet för att dekryptera den privata nyckeln om nyckelfilen skyddas av en lösenfras. | Ja om den privata nyckelfilen skyddas av en lösenfras. |

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

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Exempel: **SshPublicKey-autentisering med privat nyckelinnehåll**

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

Mer information finns i [SFTP-anslutningsartikel.](data-factory-sftp-connector.md#linked-service-properties)

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en SFTP-datauppsättning anger du **typen** av datauppsättningen till **FileShare**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Undersökväg till mappen. Använd escape-tecknet ' \ ' för specialtecken i strängen. Se Exempel på länkade tjänst- och datauppsättningsdefinitioner för exempel.<br/><br/>Du kan kombinera den här egenskapen med **partitionBy** om du vill ha mappsökvägar baserat på start-/slutdatumtider för segment. |Ja |
| fileName |Ange namnet på filen i **folderPath** om du vill att tabellen ska referera till en viss fil i mappen. Om du inte anger något värde för den här egenskapen pekar tabellen på alla filer i mappen.<br/><br/>När filnamn inte har angetts för en utdatauppsättning, skulle namnet på den genererade filen vara i följande format: <br/><br/>`Data.<Guid>.txt`(Exempel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Inga |
| filFilter |Ange ett filter som ska användas för att välja en delmängd av filer i folderPath i stället för alla filer.<br/><br/>Tillåtna värden `*` är: (flera tecken) och `?` (enstaka tecken).<br/><br/>Exempel 1:`"fileFilter": "*.log"`<br/>Exempel 2:`"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter gäller för en indatafildeldatauppsättning. Den här egenskapen stöds inte med HDFS. |Inga |
| partitioneradAv |partitionedBy kan användas för att ange en dynamisk folderPath, filnamn för tidsseriedata. FolderPath har till exempel parameteriserats för varje timme data. |Inga |
| format | Följande formattyper stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange **type** typegenskapen under format till ett av dessa värden. Mer information finns i avsnitten [Textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)och [Parquet Format.](data-factory-supported-file-and-compression-formats.md#parquet-format) <br><br> Om du vill **kopiera filer som de är** mellan filbaserade butiker (binär kopia) hoppar du över formatavsnittet i definitionerna för både in- och utdatadata. |Inga |
| komprimering | Ange typ och komprimeringsnivå för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**och **ZipDeflate**. Nivåer som stöds är: **Optimal** och **snabbaste**. Mer information finns [i Fil- och komprimeringsformat i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Inga |
| användaBinaryTransfer |Ange om binärt överföringsläge ska användas. Sant för binärt läge och falskt ASCII. Standardvärde: Sant. Den här egenskapen kan bara användas när associerad länkad tjänsttyp är av typen: FtpServer. |Inga |

> [!NOTE]
> filnamn och filFilter kan inte användas samtidigt.

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

Mer information finns i [SFTP-anslutningsartikel.](data-factory-sftp-connector.md#dataset-properties)

### <a name="file-system-source-in-copy-activity"></a>Filsystemkälla i kopieringsaktivitet
Om du kopierar data från en SFTP-källa anger du **källtypen** för kopieringsaktiviteten till **FileSystemSource**och anger följande egenskaper i **källavsnittet:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| Rekursiv |Anger om data läss rekursivt från undermapparna eller bara från den angivna mappen. |Sant, Falskt (standard) |Inga |



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

Mer information finns i [SFTP-anslutningsartikel.](data-factory-sftp-connector.md#copy-activity-properties)


## <a name="http"></a>HTTP

### <a name="linked-service"></a>Länkad tjänst
Om du vill definiera en HTTP-länkad tjänst anger du **typen** av länkad tjänst till **Http**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| url | Grundläggande URL till webbservern | Ja |
| authenticationType | Anger autentiseringstypen. Tillåtna värden är: **Anonym**, **Basic**, **Digest**, **Windows**, **ClientCertificate**. <br><br> Se avsnitt nedan för den här tabellen om fler egenskaper och JSON-exempel för dessa autentiseringstyper. | Ja |
| aktiveraServerCertificateValidation | Ange om server-TLS/SSL-certifikatvalidering ska aktiveras om källan är HTTPS Web Server | Nej, standard är sant |
| gatewayName (gatewayName) | Namnet på datahanteringsgatewayen för att ansluta till en lokal HTTP-källa. | Ja om du kopierar data från en lokal HTTP-källa. |
| krypteradKnuren | Krypterad autentiseringsuppgifter för åtkomst till HTTP-slutpunkten. Genereras automatiskt när du konfigurerar autentiseringsinformationen i kopieringsguiden eller popup-dialogrutan ClickOnce. | Nej. Använd endast när du kopierar data från en lokal HTTP-server. |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Exempel: Använda autentisering av grundläggande, sammanfattning eller Windows
Ange `authenticationType` `Basic`som `Digest`, `Windows`eller , och ange följande egenskaper förutom de generiska HTTP-anslutningsapparna som introduceras ovan:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| användarnamn | Användarnamn för att komma åt HTTP-slutpunkten. | Ja |
| password | Lösenord för användaren (användarnamn). | Ja |

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

#### <a name="example-using-clientcertificate-authentication"></a>Exempel: Använda clientcertificate-autentisering

Om du vill `authenticationType` använda `ClientCertificate`grundläggande autentisering anger du som och anger följande egenskaper förutom de allmänna HTTP-anslutningsapparna som introduceras ovan:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| inbäddadeCertData | Base64-kodade innehållet i binära data i PFX-filen (Personal Information Exchange). | Ange antingen `embeddedCertData` `certThumbprint`eller . |
| certThumbprint | Tumavtrycket för certifikatet som installerades på gateway-datorns cert-arkiv. Använd endast när du kopierar data från en lokal HTTP-källa. | Ange antingen `embeddedCertData` `certThumbprint`eller . |
| password | Lösenord som är associerat med certifikatet. | Inga |

Om du `certThumbprint` använder för autentisering och certifikatet är installerat i den lokala datorns personliga arkiv måste du bevilja läsbehörighet till gateway-tjänsten:

1. Starta Microsoft Management Console (MMC). Lägg till snapin-modulen **Certifikat** som är inriktad på den **lokala datorn**.
2. Expandera **certifikat**, **Personligt**och klicka på **Certifikat**.
3. Högerklicka på certifikatet från det personliga arkivet och välj **Alla uppgifter**->**hantera privata nycklar...**
3. Lägg till användarkontot som värdtjänsten för Data Management Gateway körs under på fliken **Säkerhet** med läsbehörighet till certifikatet.

**Exempel: använda klientcertifikat:** Den här länkade tjänsten länkar datafabriken till en lokal HTTP-webbserver. Den använder ett klientcertifikat som är installerat på datorn med Data Management Gateway installerat.

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

#### <a name="example-using-client-certificate-in-a-file"></a>Exempel: använda klientcertifikat i en fil
Den här länkade tjänsten länkar datafabriken till en lokal HTTP-webbserver. Den använder en klientcertifikatfil på datorn med Data Management Gateway installerat.

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

Mer information finns i [HTTP-anslutningsartikel.](data-factory-http-connector.md#linked-service-properties)

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en HTTP-datauppsättning anger du **typen** av datauppsättningen till **Http**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| släktingUrl | En relativ URL till resursen som innehåller data. När sökvägen inte anges används endast den URL som anges i den länkade tjänstdefinitionen. <br><br> Om du vill skapa dynamisk URL kan du använda `"relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)"` [datafabriksfunktioner och systemvariabler](data-factory-functions-variables.md), Exempel: . | Inga |
| begäranMethod | Http-metod. Tillåtna värden är **GET** eller **POST**. | Nej. Standardvärdet är `GET`. |
| additionalHeaders | Ytterligare HTTP-begäranden. | Inga |
| begäranKropp | Brödtext för HTTP-begäran. | Inga |
| format | Om du bara vill **hämta data från HTTP-slutpunkten som de är** utan att tolka dem hoppar du över de här formatinställningarna. <br><br> Om du vill tolka HTTP-svarsinnehållet under kopiering stöds följande formattyper: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Mer information finns i avsnitten [Textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format)och [Parquet Format.](data-factory-supported-file-and-compression-formats.md#parquet-format) |Inga |
| komprimering | Ange typ och komprimeringsnivå för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**och **ZipDeflate**. Nivåer som stöds är: **Optimal** och **snabbaste**. Mer information finns [i Fil- och komprimeringsformat i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Inga |

#### <a name="example-using-the-get-default-method"></a>Exempel: använda metoden GET (standard)

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

#### <a name="example-using-the-post-method"></a>Exempel: använda POST-metoden

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
Mer information finns i [HTTP-anslutningsartikel.](data-factory-http-connector.md#dataset-properties)

### <a name="http-source-in-copy-activity"></a>HTTP-källa i kopieringsaktivitet
Om du kopierar data från en HTTP-källa anger du **källtypen** för kopieringsaktiviteten till **HttpSource**och anger följande egenskaper i **källavsnittet:**

| Egenskap | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| httpRequestTimeout (På) | Timeout (TimeSpan) för HTTP-begäran för att få ett svar. Det är timeouten för att få ett svar, inte timeout för att läsa svarsdata. | Nej. Standardvärde: 00:01:40 |


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

Mer information finns i [HTTP-anslutningsartikel.](data-factory-http-connector.md#copy-activity-properties)

## <a name="odata"></a>OData

### <a name="linked-service"></a>Länkad tjänst
Om du vill definiera en OData-länkad tjänst anger du **typen** av länkad tjänst till **OData**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| url |Url till OData-tjänsten. |Ja |
| authenticationType |Typ av autentisering som används för att ansluta till OData-källan. <br/><br/> För moln-OData är möjliga värden Anonym, Basic och OAuth (obs Azure Data Factory stöder för närvarande endast Azure Active Directory-baserad OAuth). <br/><br/> För lokala OData är möjliga värden Anonyma, Grundläggande och Windows. |Ja |
| användarnamn |Ange användarnamn om du använder grundläggande autentisering. |Ja (endast om du använder grundläggande autentisering) |
| password |Ange lösenord för det användarkonto som du angav för användarnamnet. |Ja (endast om du använder grundläggande autentisering) |
| auktoriseradRedential |Om du använder OAuth klickar du på **Knappen Auktorisera** i guiden Kopia av Data fabrik eller Redigerare och anger autentiseringsuppgifterna, så genereras värdet för den här egenskapen automatiskt. |Ja (endast om du använder OAuth-autentisering) |
| gatewayName (gatewayName) |Namnet på den gateway som datafabrikstjänsten ska använda för att ansluta till den lokala OData-tjänsten. Ange bara om du kopierar data från den lokala OData-källan. |Inga |

#### <a name="example---using-basic-authentication"></a>Exempel - Använda grundläggande autentisering
```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

#### <a name="example---using-anonymous-authentication"></a>Exempel - Använda anonym autentisering

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

#### <a name="example---using-windows-authentication-accessing-on-premises-odata-source"></a>Exempel - Använda Windows-autentisering som använder lokal OData-källa

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

#### <a name="example---using-oauth-authentication-accessing-cloud-odata-source"></a>Exempel - Använda OAuth-autentisering som använder moln-OData-källa
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

Mer information finns i [OData-anslutningsartikel.](data-factory-odata-connector.md#linked-service-properties)

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en OData-datauppsättning anger du **typen** av datauppsättningen till **ODataResource**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| path |Sökväg till OData-resursen |Inga |

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

Mer information finns i [OData-anslutningsartikel.](data-factory-odata-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Relationskälla i kopieringsaktivitet
Om du kopierar data från en OData-källa anger du **källtypen** för kopieringsaktiviteten till **RelationalSource**och anger följande egenskaper i **källavsnittet:**

| Egenskap | Beskrivning | Exempel | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd den anpassade frågan för att läsa data. |"?$select=Namn, Beskrivning&$top=5" |Inga |

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

Mer information finns i [OData-anslutningsartikel.](data-factory-odata-connector.md#copy-activity-properties)


## <a name="odbc"></a>ODBC


### <a name="linked-service"></a>Länkad tjänst
Om du vill definiera en ODBC-länkad tjänst anger du **typen** av länkad tjänst till **OnPremisesOdbc**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| Connectionstring |Den icke-åtkomst-referensdelen av anslutningssträngen och en valfri krypterad autentiseringsåtkomst. Se exempel i följande avsnitt. |Ja |
| credential |Åtkomstautentiseringsdelen av anslutningssträngen som anges i drivrutinsspecifikt egenskapsvärdeformat. Exempel: `“Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;”.` |Inga |
| authenticationType |Typ av autentisering som används för att ansluta till ODBC-datalagret. Möjliga värden är: Anonym och Basic. |Ja |
| användarnamn |Ange användarnamn om du använder grundläggande autentisering. |Inga |
| password |Ange lösenord för det användarkonto som du angav för användarnamnet. |Inga |
| gatewayName (gatewayName) |Namn på den gateway som datafabrikstjänsten ska använda för att ansluta till ODBC-datalagret. |Ja |

#### <a name="example---using-basic-authentication"></a>Exempel - Använda grundläggande autentisering

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
#### <a name="example---using-basic-authentication-with-encrypted-credentials"></a>Exempel - Använda grundläggande autentisering med krypterade autentiseringsuppgifter
Du kan kryptera autentiseringsuppgifterna med cmdleten [New-AzDataFactoryEncryptValue.](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue)

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

#### <a name="example-using-anonymous-authentication"></a>Exempel: Använda anonym autentisering

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

Mer information finns i [ODBC-kopplingsartikel.](data-factory-odbc-connector.md#linked-service-properties)

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en ODBC-datauppsättning anger du **typen** av datauppsättningen på **RelationalTable**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i ODBC-datalagret. |Ja |


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

Mer information finns i [ODBC-kopplingsartikel.](data-factory-odbc-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Relationskälla i kopieringsaktivitet
Om du kopierar data från ett ODBC-datalager anger du **källtypen** för kopieringsaktiviteten på **RelationalSource**och anger följande egenskaper i **källavsnittet:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd den anpassade frågan för att läsa data. |SQL-frågesträng. Till exempel: `select * from MyTable`. |Ja |

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

Mer information finns i [ODBC-kopplingsartikel.](data-factory-odbc-connector.md#copy-activity-properties)

## <a name="salesforce"></a>Salesforce


### <a name="linked-service"></a>Länkad tjänst
Om du vill definiera en Salesforce-länkad tjänst anger du **typen** av länkad tjänst till **Salesforce**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| miljöUrl | Ange URL:en för Salesforce-instansen. <br><br> - Standard är\/"https: /login.salesforce.com". <br> - Om du vill kopierahttps://test.salesforce.comdata från sandlådan anger du " ". <br> - Om du vill kopiera data från en anpassad domän anger du till exempel "https://[domain].my.salesforce.com". |Inga |
| användarnamn |Ange ett användarnamn för användarkontot. |Ja |
| password |Ange ett lösenord för användarkontot. |Ja |
| säkerhetToken |Ange en säkerhetstoken för användarkontot. Se [Hämta säkerhetstoken](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) för instruktioner om hur du återställer/hämtar en säkerhetstoken. Mer information om säkerhetstoken i allmänhet finns i [Säkerhet och API: et](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Ja |

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

Mer information finns i [artikel om Salesforce-anslutning.](data-factory-salesforce-connector.md#linked-service-properties)

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en Salesforce-datauppsättning anger du **typen** av datauppsättningen till **RelationalTable**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i Salesforce. |Nej (om en **fråga om** **RelationalSource** har angetts) |

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

Mer information finns i [artikel om Salesforce-anslutning.](data-factory-salesforce-connector.md#dataset-properties)

### <a name="relational-source-in-copy-activity"></a>Relationskälla i kopieringsaktivitet
Om du kopierar data från Salesforce anger du **källtypen** för kopieringsaktiviteten till **RelationalSource**och anger följande egenskaper i **källavsnittet:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd den anpassade frågan för att läsa data. |En SQL-92-fråga eller [SOQL-fråga (Salesforce Object Query Language).](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) Till exempel: `select * from MyTable__c`. |Nej (om **datauppsättningens** **tabellNamn** har angetts) |

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
> Den "__c" delen av API-namnet behövs för alla anpassade objekt.

Mer information finns i [artikel om Salesforce-anslutning.](data-factory-salesforce-connector.md#copy-activity-properties)

## <a name="web-data"></a>Webbdata

### <a name="linked-service"></a>Länkad tjänst
Om du vill definiera en webblänkd tjänst anger du **typen** av länkad tjänst till **webben**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| URL |URL till webbkällan |Ja |
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

Mer information finns i artikeln [Webbtabellkoppling.](data-factory-web-table-connector.md#linked-service-properties)

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en webbdatauppsättning anger du **typen** av datauppsättningen till **WebTable**och anger följande egenskaper i avsnittet **typeProperties:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |typ av datauppsättning. måste vara inställt på **WebTable** |Ja |
| path |En relativ URL till resursen som innehåller tabellen. |Nej. När sökvägen inte anges används endast den URL som anges i den länkade tjänstdefinitionen. |
| Index |Indexet för tabellen i resursen. Se Hämta index över en tabell i ett HTML-sidavsnitt för steg för att hämta index över en tabell på en HTML-sida. |Ja |

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

Mer information finns i artikeln [Webbtabellkoppling.](data-factory-web-table-connector.md#dataset-properties)

### <a name="web-source-in-copy-activity"></a>Webbkälla i kopieringsaktivitet
Om du kopierar data från en webbtabell anger du **källtypen** för kopieringsaktiviteten till **WebSource**. När källan i kopieringsaktiviteten är av typen **WebSource**stöds inga ytterligare egenskaper.

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

Mer information finns i artikeln [Webbtabellkoppling.](data-factory-web-table-connector.md#copy-activity-properties)

## <a name="compute-environments"></a>BERÄKNINGSMILJÖER
I följande tabell visas beräkningsmiljöer som stöds av Data Factory och de omvandlingsaktiviteter som kan köras på dem. Klicka på länken för den beräkning du är intresserad av för att se JSON-schemana för länkad tjänst för att länka den till en datafabrik.

| Compute-miljö | Aktiviteter |
| --- | --- |
| [HDInsight-kluster](#on-demand-azure-hdinsight-cluster) på begäran eller [ditt eget HDInsight-kluster](#existing-azure-hdinsight-cluster) |[.NET anpassad aktivitet](#net-custom-activity), [Hive-aktivitet,](#hdinsight-hive-activity) [Grisaktivitet,](#hdinsight-pig-activity) [MapReduce-aktivitet,](#hdinsight-mapreduce-activity)Hadoop-strömningsaktivitet, [Spark-aktivitet](#hdinsight-spark-activity) |
| [Azure Batch](#azure-batch) |[.NET-anpassad aktivitet](#net-custom-activity) |
| [Azure Machine Learning](#azure-machine-learning) | [Körningsaktivitet för maskininlärningsbatchen](#machine-learning-batch-execution-activity), [Resursaktivitet för maskininlärningsuppdatering](#machine-learning-update-resource-activity) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics) |[Data Lake Analytics U-SQL](#data-lake-analytics-u-sql-activity) |
| [Azure SQL Database](#azure-sql-database), [Azure SQL Data Warehouse](#azure-sql-data-warehouse), SQL [Server](#sql-server-stored-procedure) |[Lagrad procedur](#stored-procedure-activity) |

## <a name="on-demand-azure-hdinsight-cluster"></a>Azure HDInsight-kluster på begäran
Azure Data Factory-tjänsten kan automatiskt skapa ett Windows/Linux-baserat HDInsight-kluster på begäran för att bearbeta data. Klustret skapas i samma region som lagringskontot (egenskapen linkedServiceName i JSON) som är associerat med klustret. Du kan köra följande omvandlingsaktiviteter för den här länkade tjänsten: [.NET-anpassad aktivitet](#net-custom-activity), [Hive-aktivitet](#hdinsight-hive-activity), [Grisaktivitet](#hdinsight-pig-activity), [MapReduce-aktivitet](#hdinsight-mapreduce-activity), Hadoop-direktuppspelningsaktivitet, [Spark-aktivitet](#hdinsight-spark-activity).

### <a name="linked-service"></a>Länkad tjänst
Följande tabell innehåller beskrivningar av de egenskaper som används i Azure JSON-definitionen av en HDInsight-länkad tjänst på begäran.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen Type ska anges till **HDInsightOnDemand**. |Ja |
| clusterSize |Antal arbets-/datanoder i klustret. HDInsight-klustret skapas med två huvudnoder tillsammans med antalet arbetsnoder som du anger för den här egenskapen. Noderna är av storlek Standard_D3 som har 4 kärnor, så ett 4-arbetsnodkluster tar 24 kärnor (4\*\*4 = 16 kärnor för arbetsnoder plus 2 4 = 8 kärnor för huvudnoder). Mer information om den Standard_D3 nivån finns [i Skapa Linux-baserade Hadoop-kluster i HDInsight.](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) |Ja |
| tidtolering |Den tillåtna inaktiva tiden för HDInsight-klustret på begäran. Anger hur länge HDInsight-klustret på begäran förblir vid liv efter slutförandet av en aktivitetskörning om det inte finns några andra aktiva jobb i klustret.<br/><br/>Om en aktivitetskörning till exempel tar 6 minuter och tidstolk är inställd på 5 minuter, förblir klustret vid liv i 5 minuter efter 6 minuters bearbetning av aktivitetskörningen. Om en annan aktivitetskörning körs med 6-minutersfönstret bearbetas den av samma kluster.<br/><br/>Att skapa ett HDInsight-kluster på begäran är en dyr åtgärd (kan ta ett tag), så använd den här inställningen efter behov för att förbättra prestanda för en datafabrik genom att återanvända ett HDInsight-kluster på begäran.<br/><br/>Om du anger tidstolkivvärde till 0 tas klustret bort så fort aktiviteten körs i bearbetad. Å andra sidan, om du anger ett högt värde, kan klustret stanna inaktiv i onödan vilket resulterar i höga kostnader. Därför är det viktigt att du anger lämpligt värde baserat på dina behov.<br/><br/>Flera pipelines kan dela samma instans av HDInsight-klustret på begäran om egenskapsvärdet för tidstolk har angetts på lämpligt sätt |Ja |
| version |Version av HDInsight-klustret. Mer information finns [i HDInsight-versioner som stöds i Azure Data Factory](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory). |Inga |
| linkedServiceName |Azure Storage-länkad tjänst som ska användas av klustret på begäran för lagring och bearbetning av data. <p>För närvarande kan du inte skapa ett HDInsight-kluster på begäran som använder ett Azure Data Lake Store som lagring. Om du vill lagra resultatdata från HDInsight-bearbetning i ett Azure Data Lake Store använder du en kopieringsaktivitet för att kopiera data från Azure Blob Storage till Azure Data Lake Store.</p>  | Ja |
| ytterligareLänkade tjänstnamn |Anger ytterligare lagringskonton för den HDInsight-länkade tjänsten så att datafabrikstjänsten kan registrera dem för din räkning. |Inga |
| osType (olika) |Typ av operativsystem. Tillåtna värden är: Windows (standard) och Linux |Inga |
| hcatalogLinkedServiceName |Namnet på Azure SQL-länkade tjänst som pekar på HCatalog-databasen. HDInsight-klustret på begäran skapas med hjälp av Azure SQL-databasen som metabutik. |Inga |

### <a name="json-example"></a>JSON exempel
Följande JSON definierar en Linux-baserad HDInsight-länkad tjänst på begäran. Tjänsten Data Factory skapar automatiskt ett **Linux-baserat HDInsight-kluster** när du bearbetar ett datasegment.

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

Mer information finns i artikel [om beräkningslänkade tjänster.](data-factory-compute-linked-services.md)

## <a name="existing-azure-hdinsight-cluster"></a>Befintligt Azure HDInsight-kluster
Du kan skapa en Azure HDInsight-länkad tjänst för att registrera ditt eget HDInsight-kluster med Data Factory. Du kan köra följande dataomvandlingsaktiviteter för den här länkade tjänsten: [.NET-anpassad aktivitet](#net-custom-activity), [Hive-aktivitet](#hdinsight-hive-activity), [Grisaktivitet](#hdinsight-pig-activity), [MapReduce-aktivitet](#hdinsight-mapreduce-activity), Hadoop-direktuppspelningsaktivitet, [Spark-aktivitet](#hdinsight-spark-activity).

### <a name="linked-service"></a>Länkad tjänst
Följande tabell innehåller beskrivningar av de egenskaper som används i Azure JSON-definitionen av en Azure HDInsight-länkad tjänst.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen Type ska ställas in på **HDInsight**. |Ja |
| clusterUri (klusterUri) |Uri i HDInsight-klustret. |Ja |
| användarnamn |Ange namnet på den användare som ska användas för att ansluta till ett befintligt HDInsight-kluster. |Ja |
| password |Ange lösenord för användarkontot. |Ja |
| linkedServiceName | Namnet på den Azure Storage-länkade tjänst som refererar till Azure-bloblagringslagringen som används av HDInsight-klustret. <p>För närvarande kan du inte ange en Azure Data Lake Store-länkad tjänst för den här egenskapen. Du kan komma åt data i Azure Data Lake Store från Hive/Pig-skript om HDInsight-klustret har åtkomst till DataSjöarkivet. </p>  |Ja |

Versioner av HDInsight-kluster som stöds finns i [HDInsight-versioner som stöds](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory).

#### <a name="json-example"></a>JSON exempel

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
Du kan skapa en Azure Batch-länkad tjänst för att registrera en batchpool med virtuella datorer (VMs) med en datafabrik. Du kan köra .NET-anpassade aktiviteter med antingen Azure Batch eller Azure HDInsight. Du kan köra en [.NET-anpassad aktivitet](#net-custom-activity) på den här länkade tjänsten.

### <a name="linked-service"></a>Länkad tjänst
Följande tabell innehåller beskrivningar för de egenskaper som används i Azure JSON-definitionen av en Azure Batch-länkad tjänst.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Typegenskapen ska anges till **AzureBatch**. |Ja |
| accountName |Namn på Azure Batch-kontot. |Ja |
| Accesskey |Åtkomstnyckel för Azure Batch-kontot. |Ja |
| poolNamn |Namn på poolen med virtuella datorer. |Ja |
| linkedServiceName |Namnet på den Azure Storage-länkade tjänst som är associerad med den här Azure Batch-länkade tjänsten. Den här länkade tjänsten används för mellanlagringsfiler som krävs för att köra aktiviteten och lagra loggarna för aktivitetskörning. |Ja |


#### <a name="json-example"></a>JSON exempel

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
Du skapar en Azure Machine Learning-länkad tjänst för att registrera en slutpunkt för machine learning-batchbedömning med en datafabrik. Två dataomvandlingsaktiviteter som kan köras på den här länkade tjänsten: [Machine Learning Batch Execution Activity](#machine-learning-batch-execution-activity), Machine Learning Update Resource [Activity](#machine-learning-update-resource-activity).

### <a name="linked-service"></a>Länkad tjänst
Följande tabell innehåller beskrivningar av de egenskaper som används i Azure JSON-definitionen av en Azure Machine Learning-länkad tjänst.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| Typ |Typegenskapen ska anges till: **AzureML**. |Ja |
| mlEndpoint (mlEndpoint) |Url:en för batchbedömning. |Ja |
| apiKey (apiKey) |Den publicerade arbetsytemodellens API. |Ja |

#### <a name="json-example"></a>JSON exempel

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
Du skapar en **Azure Data Lake Analytics-länkad** tjänst för att länka en Azure Data Lake Analytics-beräkningstjänst till en Azure-datafabrik innan du använder Data Lake Analytics [U-SQL-aktiviteten](data-factory-usql-activity.md) i en pipeline.

### <a name="linked-service"></a>Länkad tjänst

I följande tabell finns beskrivningar av de egenskaper som används i JSON-definitionen av en Azure Data Lake Analytics-länkad tjänst.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| Typ |Typegenskapen ska anges till: **AzureDataLakeAnalytics**. |Ja |
| accountName |Azure Data Lake Analytics-kontonamn. |Ja |
| dataLakeAnalyticsUri |Azure Data Lake Analytics URI. |Inga |
| auktorisering |Auktoriseringskoden hämtas automatiskt efter att ha klickat på **knappen Auktorisera** i Data Factory Editor och slutfört OAuth-inloggningen. |Ja |
| subscriptionId |Azure-prenumerations-ID |Nej (Om inget anges används prenumerationen på datafabriken). |
| resourceGroupName |Azure-resursgruppsnamn |Nej (Om det inte anges används datafabrikens resursgrupp). |
| Sessionid |sessions-ID från OAuth-auktoriseringssessionen. Varje sessions-ID är unikt och får endast användas en gång. När du använder Data Factory Editor genereras det här ID:et automatiskt. |Ja |


#### <a name="json-example"></a>JSON exempel
I följande exempel innehåller JSON-definitionen för en Azure Data Lake Analytics-länkad tjänst.

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

## <a name="sql-server-stored-procedure"></a>Lagrad SQL-server-procedur

Du skapar en SQL Server-länkad tjänst och använder den med [den lagrade proceduraktiviteten](data-factory-stored-proc-activity.md) för att anropa en lagrad procedur från en Data Factory-pipeline.

### <a name="linked-service"></a>Länkad tjänst
Du skapar en länkad tjänst av typen **OnPremisesSqlServer** för att länka en lokal SQL Server-databas till en datafabrik. I följande tabell beskrivs beskrivning av JSON-element som är specifika för den lokala SQL Server-länkade tjänsten.

I följande tabell beskrivs beskrivning av JSON-element som är specifika för SQL Server-länkade tjänst.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen Type ska anges till: **OnPremisesSqlServer**. |Ja |
| Connectionstring |Ange anslutningStringsinformation som behövs för att ansluta till den lokala SQL Server-databasen med antingen SQL-autentisering eller Windows-autentisering. |Ja |
| gatewayName (gatewayName) |Namnet på den gateway som tjänsten Data Factory ska använda för att ansluta till den lokala SQL Server-databasen. |Ja |
| användarnamn |Ange användarnamn om du använder Windows-autentisering. Exempel: **användarnamn\\på domännamn**. |Inga |
| password |Ange lösenord för det användarkonto som du angav för användarnamnet. |Inga |

Du kan kryptera autentiseringsuppgifter med cmdleten **New-AzDataFactoryEncryptValue** och använda dem i anslutningssträngen enligt följande exempel (**Egenskapen EncryptedCredential):**

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

Om användarnamn och lösenord anges använder gatewayen dem för att personifiera det angivna användarkontot för att ansluta till den lokala SQL Server-databasen. Annars ansluter gatewayen till SQL Server direkt med säkerhetskontexten för Gateway (dess startkonto).

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

Mer information finns i [sql server-anslutningsartikel.](data-factory-sqlserver-connector.md#linked-service-properties)

## <a name="data-transformation-activities"></a>AKTIVITETER FÖR DATAOMVANDLING

Aktivitet | Beskrivning
-------- | -----------
[HDInsight Hive-aktivitet](#hdinsight-hive-activity) | HDInsight Hive-aktiviteten i en Data Factory-pipeline kör Hive-frågor på ditt eget eller on-demand Windows/Linux-baserade HDInsight-kluster.
[HDInsight Pig aktivitet](#hdinsight-pig-activity) | HDInsight Pig-aktiviteten i en Pipeline för datafabrik kör Grisfrågor på egen hand eller på begäran Windows/Linux-baserade HDInsight-kluster.
[HDInsight MapReduce-aktivitet](#hdinsight-mapreduce-activity) | HDInsight MapReduce-aktiviteten i en Pipeline för Data Factory kör MapReduce-program på ditt eget eller on-demand Windows/Linux-baserade HDInsight-kluster.
[HDInsight streamingaktivitet](#hdinsight-streaming-activity) | HDInsight Streaming Activity i en Pipeline för datafabrik kör Hadoop Streaming-program på ditt eget eller on-demand Windows/Linux-baserade HDInsight-kluster.
[HDInsight Spark-aktivitet](#hdinsight-spark-activity) | HDInsight Spark-aktiviteten i en Pipeline för Data Factory kör Spark-program på ditt eget HDInsight-kluster.
[Machine Learning Batch-körningsaktivitet](#machine-learning-batch-execution-activity) | Med Azure Data Factory kan du enkelt skapa pipelines som använder en publicerad Azure Machine Learning-webbtjänst för prediktiv analys. Med hjälp av batchkörningsaktiviteten i en Azure Data Factory-pipeline kan du anropa en Machine Learning-webbtjänst för att göra förutsägelser om data i batch.
[Machine Learning-uppdateringsresursaktivitet](#machine-learning-update-resource-activity) | Med tiden måste de prediktiva modellerna i machine learning-bedömningsexperimenten omskolas med hjälp av nya indatauppsättningar. När du är klar med omskolningen vill du uppdatera bedömningswebbtjänsten med den omskolade Machine Learning-modellen. Du kan använda uppdatera resursaktivitet för att uppdatera webbtjänsten med den nyligen utbildade modellen.
[Lagrad proceduraktivitet](#stored-procedure-activity) | Du kan använda aktiviteten Lagrad procedur i en Data Factory-pipeline för att anropa en lagrad procedur i något av följande datalager: Azure SQL Database, Azure SQL Data Warehouse, SQL Server Database i ditt företag eller en Azure VM.
[U-SQL-aktivitet för DataSjöanalys](#data-lake-analytics-u-sql-activity) | U-SQL-aktivitet för DataSjöanalys kör ett U-SQL-skript i ett Azure Data Lake Analytics-kluster.
[.NET-anpassad aktivitet](#net-custom-activity) | Om du behöver omvandla data på ett sätt som inte stöds av Data Factory kan du skapa en anpassad aktivitet med din egen databehandlingslogik och använda aktiviteten i pipelinen. Du kan konfigurera den anpassade .NET-aktiviteten så att den körs med antingen en Azure Batch-tjänst eller ett Azure HDInsight-kluster.


## <a name="hdinsight-hive-activity"></a>HDInsight Hive-aktivitet
Du kan ange följande egenskaper i en Hive-aktivitets-JSON-definition. Egenskapen Type för aktiviteten måste vara: **HDInsightHive**. Du måste först skapa en HDInsight-länkad tjänst och ange namnet på den som ett värde för egenskapen **linkedServiceName.** Följande egenskaper stöds i avsnittet **typeProperties** när du anger typen av aktivitet till HDInsightHive:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| -skriptet |Ange inline-skriptet för Hive-skript |Inga |
| skriptsökväg |Lagra Hive-skriptet i en Azure blob-lagring och ange sökvägen till filen. Använd egenskapen script eller 'scriptPath'. Båda kan inte användas tillsammans. Filnamnet är skiftlägeskänsligt. |Inga |
| Definierar |Ange parametrar som nyckel-/värdepar för refererande i Hive-skriptet med hjälp av hiveconf |Inga |

Dessa typegenskaper är specifika för Hive-aktiviteten. Andra egenskaper (utanför avsnittet typEgenskaper) stöds för alla aktiviteter.

### <a name="json-example"></a>JSON exempel
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

Mer information finns i artikeln [Hive-aktivitet.](data-factory-hive-activity.md)

## <a name="hdinsight-pig-activity"></a>HDInsight-piggningsåtgärd
Du kan ange följande egenskaper i en JSON-definition för grisaktivitet. Egenskapen Type för aktiviteten måste vara: **HDInsightPig**. Du måste först skapa en HDInsight-länkad tjänst och ange namnet på den som ett värde för egenskapen **linkedServiceName.** Följande egenskaper stöds i avsnittet **typeProperties** när du anger typen av aktivitet till HDInsightPig:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| -skriptet |Ange inline för Pig-skript |Inga |
| skriptsökväg |Lagra Pig-skriptet i en Azure blob-lagring och ange sökvägen till filen. Använd egenskapen script eller 'scriptPath'. Båda kan inte användas tillsammans. Filnamnet är skiftlägeskänsligt. |Inga |
| Definierar |Ange parametrar som nyckel-/värdepar för refererande i pigskriptet |Inga |

Dessa typegenskaper är specifika för grisaktiviteten. Andra egenskaper (utanför avsnittet typEgenskaper) stöds för alla aktiviteter.

### <a name="json-example"></a>JSON exempel

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

Mer information finns i artikeln Grisaktivitet.

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce-aktivitet
Du kan ange följande egenskaper i en MapReduce Activity JSON-definition. Egenskapen Type för aktiviteten måste vara: **HDInsightMapReduce**. Du måste först skapa en HDInsight-länkad tjänst och ange namnet på den som ett värde för egenskapen **linkedServiceName.** Följande egenskaper stöds i avsnittet **typeProperties** när du anger typen av aktivitet till HDInsightMapReduce:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| jarLinkedService | Namnet på den länkade tjänsten för Azure Storage som innehåller JAR-filen. | Ja |
| jarFilePath | Sökväg till JAR-filen i Azure Storage. | Ja |
| Classname | Namn på huvudklassen i JAR-filen. | Ja |
| Argument | En lista med kommaavgränsade argument för MapReduce-programmet. Vid körning visas några extra argument (till exempel mapreduce.job.tags) från MapReduce-ramverket. Om du vill skilja dina argument med argumenten MapReduce kan du använda både alternativ och värde som argument som visas i följande exempel (-s, --input, --output etc., är alternativ som omedelbart följs av deras värden) | Inga |

### <a name="json-example"></a>JSON exempel

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

Mer information finns i artikel [MapReduce Activity.](data-factory-map-reduce.md)

## <a name="hdinsight-streaming-activity"></a>HDInsight-strömningsaktivitet
Du kan ange följande egenskaper i en Hadoop Streaming Activity JSON-definition. Egenskapen Type för aktiviteten måste vara: **HDInsightStreaming**. Du måste först skapa en HDInsight-länkad tjänst och ange namnet på den som ett värde för egenskapen **linkedServiceName.** Följande egenskaper stöds i avsnittet **typeProperties** när du anger typen av aktivitet till HDInsightStreaming:

| Egenskap | Beskrivning |
| --- | --- |
| Mapper | Namn på den körbara mapparen. I exemplet är cat.exe den körbara mapparen.|
| Reducering | Namnet på den körbara reduceren. I exemplet är wc.exe den körbara reduceren. |
| indata | Indatafil (inklusive plats) för mappern. I exemplet: `"wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt"`: adfsample är blob-behållaren, exempel/data/Gutenberg är mappen och davinci.txt är bloben. |
| utdata | Utdatafil (inklusive plats) för reduceraren. Utdata för jobbet Hadoop Streaming skrivs till den plats som angetts för den här egenskapen. |
| filePaths | Sökvägar för mapper och reducer körbara filer. I exemplet "adfsample/example/apps/wc.exe" är adfsample blob-behållaren, exempel/appar mappen och wc.exe är den körbara filen. |
| fileLinkedService | Azure Storage-länkad tjänst som representerar Azure-lagring som innehåller de filer som anges i avsnittet filePaths. |
| Argument | En lista med kommaavgränsade argument för MapReduce-programmet. Vid körning visas några extra argument (till exempel mapreduce.job.tags) från MapReduce-ramverket. Om du vill skilja dina argument med argumenten MapReduce kan du använda både alternativ och värde som argument som visas i följande exempel (-s, --input, --output etc., är alternativ som omedelbart följs av deras värden) |
| getDebugInfo | Ett valfritt element. När den är inställd på Fel hämtas loggarna endast vid fel. När den är inställd på Alla hämtas loggar alltid oavsett körningsstatus. |

> [!NOTE]
> Du måste ange en utdatauppsättning för hadoop-direktuppspelningsaktiviteten för egenskapen **outputs.** Denna datauppsättning kan vara bara en dummy datauppsättning som krävs för att köra rörledningen schema (timme, dagligen, etc.). Om aktiviteten inte tar en indata kan du hoppa över att ange en indatauppsättning för aktiviteten för egenskapen **indata.**

## <a name="json-example"></a>JSON exempel

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

Mer information finns i artikeln [För Hadoop-strömningsaktivitet.](data-factory-hadoop-streaming-activity.md)

## <a name="hdinsight-spark-activity"></a>HDInsight Apache Spark-aktivitet
Du kan ange följande egenskaper i en Spark Activity JSON-definition. Egenskapen Type för aktiviteten måste vara: **HDInsightSpark**. Du måste först skapa en HDInsight-länkad tjänst och ange namnet på den som ett värde för egenskapen **linkedServiceName.** Följande egenskaper stöds i avsnittet **typeProperties** när du anger typen av aktivitet till HDInsightSpark:

| Egenskap | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| rootPath (rotPath) | Azure Blob-behållaren och mappen som innehåller Spark-filen. Filnamnet är skiftlägeskänsligt. | Ja |
| entryFilePath | Relativ sökväg till rotmappen för Spark-koden/-paketet. | Ja |
| Classname | Programmets huvudklass Java/Spark | Inga |
| Argument | En lista med kommandoradsargument till Spark-programmet. | Inga |
| proxyAnvändare | Användarkontot som personifieras för att köra Spark-programmet | Inga |
| sparkConfig (på andra sätt) | Egenskaper för sparkkonfiguration. | Inga |
| getDebugInfo | Anger när Spark-loggfilerna kopieras till Azure-lagring som används av HDInsight-kluster (eller) som anges av sparkJobLinkedService. Tillåtna värden: Inga, Alltid eller Fel. Standardvärde: Ingen. | Inga |
| sparkJobLinkedService | Azure Storage-länkade tjänsten som innehåller Spark-jobbfilen, beroenden och loggar.  Om du inte anger något värde för den här egenskapen används lagringen som är associerad med HDInsight-klustret. | Inga |

### <a name="json-example"></a>JSON exempel

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

- Egenskapen **Type** är inställd på **HDInsightSpark**.
- **RootPath** är inställd på **adfspark\\pyFiles** där adfspark är Azure Blob-behållaren och pyFiles är fin mapp i den behållaren. I det här exemplet är Azure Blob Storage det som är associerat med Spark-klustret. Du kan ladda upp filen till en annan Azure Storage. Om du gör det skapar du en Azure Storage-länkad tjänst för att länka lagringskontot till datafabriken. Ange sedan namnet på den länkade tjänsten som ett värde för egenskapen **sparkJobLinkedService.** Se Egenskaper för Spark-aktivitet för information om den här egenskapen och andra egenskaper som stöds av Spark-aktiviteten.
- **PostenFilePath** är inställd på **test.py**, som är python-filen.
- Egenskapen **getDebugInfo** är inställd **på Alltid**, vilket innebär att loggfilerna alltid genereras (framgång eller misslyckande).

    > [!IMPORTANT]
    > Vi rekommenderar att du inte ställer in den här egenskapen till Alltid i en produktionsmiljö om du inte felsöker ett problem.
- **Utdataavsnittet** har en utdatauppsättning. Du måste ange en utdatauppsättning även om spark-programmet inte ger någon utdata. Utdatauppsättningen driver schemat för pipelinen (timme, dag, etc.).

Mer information om aktiviteten finns i artikel [Spark Activity.](data-factory-spark.md)

## <a name="machine-learning-batch-execution-activity"></a>Machine Learning Batch-körningsaktivitet
Du kan ange följande egenskaper i en Azure Machine Learning studio Batch Execution Activity JSON definition. Egenskapen type för aktiviteten måste vara: **AzureMLBatchExecution**. Du måste först skapa en Azure Machine Learning-länkad tjänst och ange namnet på den som ett värde för egenskapen **linkedServiceName.** Följande egenskaper stöds i avsnittet **typeProperties** när du anger typen av aktivitet till AzureMLBatchExecution:

Egenskap | Beskrivning | Krävs
-------- | ----------- | --------
webServiceInput | Den datauppsättning som ska skickas som en indata för webbtjänsten Azure Machine Learning studio. Denna datauppsättning måste också inkluderas i indata för aktiviteten. |Använd antingen webServiceInput eller webServiceInputs. |
webServiceInputs | Ange datauppsättningar som ska skickas som indata för webbtjänsten Azure Machine Learning studio. Om webbtjänsten tar flera indata använder du egenskapen webServiceInputs i stället för att använda egenskapen webServiceInput. Datauppsättningar som refereras av **webServiceInputs** måste också **inkluderas**i indata för aktivitet . | Använd antingen webServiceInput eller webServiceInputs. |
webServiceOutputs | De datauppsättningar som tilldelas som utdata för webbtjänsten Azure Machine Learning studio. Webbtjänsten returnerar utdata i den här datauppsättningen. | Ja |
globalaParametrar | Ange värden för webbtjänstparametrarna i det här avsnittet. | Inga |

### <a name="json-example"></a>JSON exempel
I det här exemplet har aktiviteten datauppsättningen **MLSqlInput** som indata och **MLSqlOutput** som utdata. **MLSqlInput** skickas som en indata till webbtjänsten med hjälp av egenskapen **webServiceInput** JSON. **MLSqlOutput** skickas som en utdata till webbtjänsten med hjälp av egenskapen **webServiceOutputs** JSON.

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

I JSON-exemplet använder den distribuerade Azure Machine Learning-webbtjänsten en läsare och en skrivmodul för att läsa/skriva data från/till en Azure SQL-databas. Den här webbtjänsten visar följande fyra parametrar: Databasservernamn, Databasnamn, Server-användarkontonamn och Server-användarkontolösenord.

> [!NOTE]
> Endast indata och utdata för AzureMLBatchExecution-aktiviteten kan skickas som parametrar till webbtjänsten. I ovanstående JSON-kodavsnitt är MLSqlInput till exempel en indata till AzureMLBatchExecution-aktiviteten, som skickas som en indata till webbtjänsten via parametern webServiceInput.

## <a name="machine-learning-update-resource-activity"></a>Machine Learning-uppdateringsresursaktivitet
Du kan ange följande egenskaper i en Azure Machine Learning studio Update Resource Activity JSON definition. Egenskapen type för aktiviteten måste vara: **AzureMLUpdateResource**. Du måste först skapa en Azure Machine Learning-länkad tjänst och ange namnet på den som ett värde för egenskapen **linkedServiceName.** Följande egenskaper stöds i avsnittet **typeProperties** när du anger typen av aktivitet till AzureMLUpdateResource:

Egenskap | Beskrivning | Krävs
-------- | ----------- | --------
tränatModelName | Namnet på den omskolade modellen. | Ja |
tränatModelDatasetName | Datauppsättning som pekar på iLearner-filen som returneras av omskolningsåtgärden. | Ja |

### <a name="json-example"></a>JSON exempel
Pipelinen har två aktiviteter: **AzureMLBatchExecution** och **AzureMLUpdateResource**. Azure Machine Learning studio Batch Execution-aktiviteten tar utbildningsdata som indata och producerar en iLearner-fil som en utdata. Aktiviteten anropar utbildningswebbtjänsten (utbildningsexperiment exponeras som en webbtjänst) med indatautbildningsdata och tar emot ilearnerfilen från webbtjänsten. PlatshållarenBlob är bara en dummy utdatauppsättning som krävs av Azure Data Factory-tjänsten för att köra pipelinen.


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
Du kan ange följande egenskaper i en U-SQL Activity JSON-definition. Egenskapen type för aktiviteten måste vara: **DataLakeAnalyticsU-SQL**. Du måste skapa en Azure Data Lake Analytics-länkad tjänst och ange namnet på den som ett värde för egenskapen **linkedServiceName.** Följande egenskaper stöds i avsnittet **typeProperties** när du anger typen av aktivitet till DataLakeAnalyticsU-SQL:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| scriptPath |Sökväg till mapp som innehåller U-SQL-skriptet. Filens namn är skiftlägeskänsligt. |Nej (om du använder skript) |
| scriptLinkedService |Länkad tjänst som länkar lagringen som innehåller skriptet till datafabriken |Nej (om du använder skript) |
| -skriptet |Ange infogade skript i stället för att ange scriptPath och scriptLinkedService. Till exempel: "script": "CREATE DATABASE test". |Nej (om du använder scriptPath och scriptLinkedService) |
| gradAvParallelism |Det maximala antalet noder som används samtidigt för att köra jobbet. |Inga |
| prioritet |Avgör vilka jobb av alla som står i kö för att köras först. Ju lägre siffra, desto högre prioritet. |Inga |
| parameters |Parametrar för U-SQL-skriptet |Inga |

### <a name="json-example"></a>JSON exempel

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

Mer information finns i [Data Lake Analytics U-SQL Activity](data-factory-usql-activity.md).

## <a name="stored-procedure-activity"></a>Lagrad proceduraktivitet
Du kan ange följande egenskaper i en JSON-definition för lagrad proceduraktivitet. Egenskapen Type för aktiviteten måste vara: **SqlServerStoredProcedure**. Du måste skapa en av följande länkade tjänster och ange namnet på den länkade tjänsten som ett värde för egenskapen **linkedServiceName:**

- SQL Server
- Azure SQL Database
- Azure SQL Data Warehouse

Följande egenskaper stöds i avsnittet **typeProperties** när du anger typen av aktivitet till SqlServerStoredProcedure:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| storedProcedureName |Ange namnet på den lagrade proceduren i Azure SQL-databasen eller Azure SQL Data Warehouse som representeras av den länkade tjänst som utdatatabellen använder. |Ja |
| storedProcedureParameters |Ange värden för parametrar för lagrade procedurer. Om du behöver skicka null för en parameter använder du syntaxen: "param1": null (alla gemener). Läs följande exempel om du vill veta mer om hur du använder den här egenskapen. |Inga |

Om du anger en indatauppsättning måste den vara tillgänglig (i "Klar"-status) för att den lagrade proceduraktiviteten ska kunna köras. Indatauppsättningen kan inte förbrukas i den lagrade proceduren som en parameter. Det används bara för att kontrollera beroendet innan du startar den lagrade proceduraktiviteten. Du måste ange en utdatauppsättning för en lagrad proceduraktivitet.

Utdatauppsättningen anger **schemat** för den lagrade proceduraktiviteten (timme, vecka, månad osv.). Utdatauppsättningen måste använda en **länkad tjänst** som refererar till en Azure SQL-databas eller en Azure SQL Data Warehouse eller en SQL Server-databas där du vill att den lagrade proceduren ska köras. Utdatauppsättningen kan fungera som ett sätt att överföra resultatet av det lagrade förfarandet för efterföljande bearbetning av en annan aktivitet[(kedjeaktiviteter)](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)på gång. Data Factory skriver dock inte automatiskt utdata från en lagrad procedur till den här datauppsättningen. Det är den lagrade proceduren som skrivs till en SQL-tabell som utdatauppsättningen pekar på. I vissa fall kan utdatauppsättningen vara en **dummy-datauppsättning**, som endast används för att ange schemat för att köra den lagrade proceduraktiviteten.

### <a name="json-example"></a>JSON exempel

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample",
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

Mer information finns i artikeln [Lagrad proceduraktivitet.](data-factory-stored-proc-activity.md)

## <a name="net-custom-activity"></a>.NET-anpassad aktivitet
Du kan ange följande egenskaper i en .NET-anpassad aktivitet JSON-definition. Egenskapen type för aktiviteten måste vara: **DotNetActivity**. Du måste skapa en Azure HDInsight-länkad tjänst eller en Azure Batch-länkad tjänst och ange namnet på den länkade tjänsten som ett värde för egenskapen **linkedServiceName.** Följande egenskaper stöds i avsnittet **typeProperties** när du anger typen av aktivitet till DotNetActivity:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| AssemblyName (Monteringsnamn) | Namn på sammansättningen. I exemplet är det: **MyDotnetActivity.dll**. | Ja |
| EntryPoint |Namn på den klass som implementerar gränssnittet IDotNetActivity. I exemplet är det: **MyDotNetActivityNS.MyDotNetActivity** där MyDotNetActivityNS är namnområdet och MyDotNetActivity är klassen.  | Ja |
| PackageLinkedService | Namnet på den Azure Storage-länkade tjänst som pekar på blob-lagringen som innehåller den anpassade zip-filen för aktivitet. I exemplet är det: **AzureStorageLinkedService**.| Ja |
| PackageFile | Namnet på zip-filen. I exemplet är det: **customactivitycontainer/MyDotNetActivity.zip**. | Ja |
| extendedProperties | Utökade egenskaper som du kan definiera och vidarebefordra till .NET-koden. I det här exemplet är **segmentstartvariabeln** inställd på ett värde baserat på systemvariabeln SegmentStart. | Inga |

### <a name="json-example"></a>JSON exempel

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

Detaljerad information finns [i Använda anpassade aktiviteter i datafabrikens](data-factory-use-custom-activities.md) artikel.

## <a name="next-steps"></a>Efterföljande moment
Se följande självstudiekurser:

- [Självstudiekurs: skapa en pipeline med en kopieringsaktivitet](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Självstudiekurs: skapa en pipeline med en hive-aktivitet](data-factory-build-your-first-pipeline.md)
