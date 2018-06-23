---
title: Azure Data Factory - JSON-skript referens | Microsoft Docs
description: Ger JSON-scheman för Data Factory entiteter.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 0f46ed8ce6c059fadb0b9e581863ef75e3f887fb
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36319502"
---
# <a name="azure-data-factory---json-scripting-reference"></a>Azure Data Factory - JSON-skript referens
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA).


Den här artikeln innehåller exempel och JSON-scheman för att definiera Azure Data Factory-enheter (pipeline, aktivitet, datamängd och länkade tjänsten).  

## <a name="pipeline"></a>Pipeline 
Den övergripande strukturen för en pipeline-definition är följande: 

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

Följande tabell beskriver egenskaper i pipeline-JSON-definitionen:

| Egenskap  | Beskrivning | Krävs
-------- | ----------- | --------
| namn | Namnet på pipeline. Ange ett namn som representerar åtgärden som aktiviteten eller pipeline har konfigurerats för att göra<br/><ul><li>Max. antal tecken: 260</li><li>Måste börja med en bokstav, en siffra eller ett understreck (_)</li><li>Följande tecken är inte tillåtna ”:”., ”+” ”,”?, ”/”, ”<” ”, >” ”, *”, ”%”, ”&” ”,:” ”,\\”</li></ul> |Ja |
| description |Text som beskriver vilka aktivitet eller pipeline används för | Nej |
| activities | Innehåller en lista med aktiviteter. | Ja |
| start |Datum-starttiden för pipelinen. Måste vara i [ISO-format](http://en.wikipedia.org/wiki/ISO_8601). Till exempel: 2014-10-14T16:32:41. <br/><br/>Det är möjligt att ange en lokal tid, till exempel en EST tid. Här är ett exempel: `2016-02-27T06:00:00**-05:00`, vilket är 6 AM EST.<br/><br/>Egenskaper för start- och ange tillsammans aktiva perioden för pipeline. Utdata segment som endast produceras med i den här aktiva period. |Nej<br/><br/>Om du anger ett värde för end-egenskapen måste du ange värdet för egenskapen start.<br/><br/>Start- och sluttider kan båda vara tomma om du vill skapa en pipeline. Du måste ange både värden om du vill ange en aktiv period för pipelinen ska köras. Om du inte anger start- och sluttider när du skapar en pipeline kan du ange dem med hjälp av cmdlet Set-AzureRmDataFactoryPipelineActivePeriod senare. |
| slut |Datum sluttiden för pipelinen. Om du måste vara i ISO-format. Till exempel: 2014-10-14T17:32:41 <br/><br/>Det är möjligt att ange en lokal tid, till exempel en EST tid. Här är ett exempel: `2016-02-27T06:00:00**-05:00`, vilket är 6 AM EST.<br/><br/>Ange 09-09-9999 som värde för end-egenskapen för att köra pipelinen på obestämd tid. |Nej <br/><br/>Om du anger ett värde för egenskapen start, måste du ange värde för end-egenskapen.<br/><br/>Du hittar information för den **starta** egenskapen. |
| isPaused |Om värdet är true pipelinen inte körs. Standardvärde = false. Du kan använda den här egenskapen för att aktivera eller inaktivera. |Nej |
| pipelineMode |Metoden för schemaläggning körs för pipeline. Tillåtna värden är: schemalagda (standard), görs.<br/><br/>”Schemalagd” anger att pipelinen körs vid ett angivet tidsintervall enligt den aktiva perioden (start- och -tid). 'Görs ”anger att pipelinen körs bara en gång. Görs pipelines när skapat går inte att ändra/uppdatera för närvarande. Se [Onetime pipeline](data-factory-create-pipelines.md#onetime-pipeline) mer information om hur du görs. |Nej |
| expirationTime |Tidsperiod efter som pipelinen är giltig och vara etablerade har skapats. Om den inte har någon aktiv, misslyckades, eller väntande körs pipeline tas bort automatiskt när når den förfallotid. |Nej |


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
    }
    "scheduler":
    {
    }
}
```

Följande tabell Beskriv egenskaperna i JSON-definitionen för aktiviteten:

| Tagga | Beskrivning | Krävs |
| --- | --- | --- |
| namn |Namnet på aktiviteten. Ange ett namn som representerar åtgärden som aktiviteten har konfigurerats för att göra<br/><ul><li>Max. antal tecken: 260</li><li>Måste börja med en bokstav, en siffra eller ett understreck (_)</li><li>Följande tecken är inte tillåtna ”:”., ”+” ”,”?, ”/”, ”<” ”, >” ”, *”, ”%”, ”&” ”,:” ”,\\”</li></ul> |Ja |
| description |Text som beskriver aktiviteten är det som används för. |Nej |
| typ |Anger typen för aktiviteten. Finns det [DATALAGER](#data-stores) och [DATA TRANSFORMATION aktiviteter](#data-transformation-activities) avsnitt för olika typer av aktiviteter. |Ja |
| inmatningar |Indatatabeller som används av aktiviteten<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Inga aktiviteter för HDInsightStreaming och SqlServerStoredProcedure <br/> <br/> Ja för alla andra |
| utdata |Utdata tabeller som används av aktiviteten.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": “outputtable1” } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” }  ],` |Ja |
| linkedServiceName |Namnet på den länkade tjänst som används av aktiviteten. <br/><br/>En aktivitet kan kräva att du anger den länkade tjänst som länkar till den nödvändiga beräkningsmiljön. |Ja för HDInsight aktiviteter, Azure Machine Learning aktiviteter och lagrade Proceduraktiviteten. <br/><br/>Nej för alla andra |
| typeProperties |Egenskaper i avsnittet typeProperties beror på typen för aktiviteten. |Nej |
| policy |Principer som påverkar körningsbeteende för aktiviteten. Om det inte anges används standardprinciper. |Nej |
| Schemaläggaren |Egenskapen ”scheduler” används för att definiera önskade schemaläggning för aktiviteten. Dess subegenskaper är samma som de som finns i den [tillgänglighet egenskap i en dataset](data-factory-create-datasets.md#dataset-availability). |Nej |

### <a name="policies"></a>Principer
Principer påverkar beteendet körning av en aktivitet, särskilt när segment i en tabell som har bearbetats. Följande tabell innehåller information.

| Egenskap  | Tillåtna värden | Standardvärde | Beskrivning |
| --- | --- | --- | --- |
| concurrency |Integer <br/><br/>Värdet för maximalt antal: 10 |1 |Antal samtidiga körningar av aktiviteten.<br/><br/>Den avgör antalet ParallellAktivitet körningar som kan inträffa på olika segment. Till exempel om en aktivitet behöver gå igenom snabbare en stor mängd tillgänglig data, med ett större värde för samtidighet behandling. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Anger den sorteringen av datasektorer som bearbetas.<br/><br/>Till exempel om du har 2 sektorer (en inträffar klockan 4, och en annan kl) och båda finns väntande körning. Om du ställer in executionPriorityOrder ska NewestFirst bearbetas sektorn kl först. På liknande sätt om du ställer in executionPriorityORder ska OldestFIrst bearbetas sedan sektorn klockan 4. |
| retry |Integer<br/><br/>Värdet för maximalt antal kan vara 10 |0 |Antal försök innan databearbetning för sektorn som har markerats som ett fel. Aktivitetskörningen för en datasektorn försöks upp till antalet angivna försök igen. Det nya försöket görs så snart som möjligt efter fel. |
| timeout |TimeSpan |00:00:00 |Tidsgräns för aktiviteten. Exempel: 00:10:00 (inbegriper timeout 10 minuter)<br/><br/>Om ett värde har angetts eller är 0, är tidsgränsen obegränsad.<br/><br/>Om databearbetningstiden på ett segment överskrider timeout-värdet, det avbryts och försöker systemet att försök bearbetning. Antalet försök beror på egenskapen försök igen. När timeout uppstår är status för lång tid. |
| fördröjning |TimeSpan |00:00:00 |Ange fördröjning innan databearbetningen av sektorn startar.<br/><br/>Körningen av aktiviteten för en datasektorn startas efter fördröjningen har passerat den förväntade tiden för körningen.<br/><br/>Exempel: 00:10:00 (inbegriper fördröjning på 10 minuter) |
| longRetry |Integer<br/><br/>Värdet för maximalt antal: 10 |1 |Antal lång nya försök innan körningen sektorn misslyckades.<br/><br/>longRetry försök fördelade av longRetryInterval. Så om du behöver ange en tid mellan nya försök använda longRetry. Om både försök och longRetry anges varje longRetry försök omförsök max antal försök används och försök igen * longRetry.<br/><br/>Till exempel om vi har följande inställningar i principen för aktiviteten:<br/>Försök: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Anta att det finns endast en sektor att köra (status väntar) och aktivitetskörningen misslyckas varje gång. Det skulle inledningsvis 3 körning på varandra följande försök. Efter varje försök är sektorn status försök igen. Efter första 3 försök över är statusen för sektorn LongRetry.<br/><br/>Efter en timme (det vill säga Longretryinteval's värde), skulle det finnas en annan uppsättning 3 körning på varandra följande försök. Efter detta segment status skulle misslyckades och inga fler försök kan inte genomföras. Därför har övergripande 6 försök gjorts.<br/><br/>Om alla körning lyckas, statusen för sektorn är klar och inga fler försök görs.<br/><br/>longRetry får användas i situationer där beroende data kommer till icke-deterministiska gånger eller hela miljön är flaky under vilken databearbetningen sker. I sådana fall kan detta återförsök efter varandra inte kan hjälpa och gör att när ett intervall på tid som resulterar i önskad utdata.<br/><br/>Liten varning: Ange inte höga värden för longRetry eller longRetryInterval. Normalt en högre värden andra systemfel problem. |
| longRetryInterval |TimeSpan |00:00:00 |Fördröjning mellan försök har lång |

### <a name="typeproperties-section"></a>typeProperties avsnitt
Avsnittet typeProperties är olika för varje aktivitet. Omvandling aktiviteter har precis Typegenskaper. Se [DATA TRANSFORMATION aktiviteter](#data-transformation-activities) i den här artikeln för JSON-exempel som definierar omvandling av aktiviteter i en pipeline. 

**Kopieringsaktiviteten** har två avsnitt i avsnittet typeProperties: **källa** och **sink**. Se [DATALAGER](#data-stores) avsnitt i den här artikeln för JSON-exempel som visar hur du använder en data-butiken som en källa och/eller mottagare. 

### <a name="sample-copy-pipeline"></a>Exempel på kopieringspipeline
I följande exempel på pipeline finns det en aktivitet av typen **Copy** (Kopiera) i avsnittet **activities**. I det här exemplet i [Kopieringsaktiviteten](data-factory-data-movement-activities.md) kopierar data från ett Azure Blob storage till en Azure SQL database. 

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

Se [DATALAGER](#data-stores) avsnitt i den här artikeln för JSON-exempel som visar hur du använder en data-butiken som en källa och/eller mottagare.    

En fullständig genomgång av hur du skapar den här pipelinen finns [Självstudier: kopiera data från Blob Storage till SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

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
* Hive-skriptfilen **partitionweblogs.hql** lagras i Azure-lagringskontot (anges med scriptLinkedService, kallas **AzureStorageLinkedService**), och i mappen **skript** i behållaren **adfgetstarted**.
* Den **definierar** avsnitt används för att ange körningsinställningar för som skickas till hive-skript som Hive konfigurationsvärden (t.ex `${hiveconf:inputtable}`, `${hiveconf:partitionedtable}`).

Se [DATA TRANSFORMATION aktiviteter](#data-transformation-activities) i den här artikeln för JSON-exempel som definierar omvandling av aktiviteter i en pipeline.

En fullständig genomgång av hur du skapar den här pipelinen finns [Självstudier: skapa din första pipeline för bearbetning av data med Hadoop-kluster](data-factory-build-your-first-pipeline.md). 

## <a name="linked-service"></a>Länkad tjänst
Den övergripande strukturen för en länkad tjänst-definition är följande:

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

Följande tabell Beskriv egenskaperna i JSON-definitionen för aktiviteten:

| Egenskap  | Beskrivning | Krävs |
| -------- | ----------- | -------- | 
| namn | Namnet på den länkade tjänsten. | Ja | 
| Egenskaper - typ | Typ av den länkade tjänsten. Till exempel: Azure Storage, Azure SQL Database. |
| typeProperties | Avsnittet typeProperties har element som är olika för varje dataarkiv eller compute-miljö. Se [datalager](#datastores) avsnittet för alla data butiken länkade tjänster och [compute miljöer](#compute-environments) länkade tjänsterna för alla beräkningen |   

## <a name="dataset"></a>Datauppsättning 
En datamängd i Azure Data Factory definieras enligt följande:

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
| namn | Namnet på datamängden. Se [Azure Data Factory - namngivningsregler](data-factory-naming-rules.md) för namngivningsregler. |Ja |Ej tillämpligt |
| typ | Typ av datauppsättningen. Ange en av de typer som stöds av Azure Data Factory (till exempel: AzureBlob, AzureSqlTable). Se [DATALAGER](#data-stores) avsnittet för alla datalager och dataset-typer som stöds av Data Factory. | 
| struktur | Schemat för datauppsättningen. Den innehåller kolumner, deras typer och så vidare. | Nej |Ej tillämpligt |
| typeProperties | Egenskaper som motsvarar den valda typen. Se [DATALAGER](#data-stores) för typer som stöds och deras egenskaper. |Ja |Ej tillämpligt |
| extern | Boolesk flagga som anger om en datamängd uttryckligen produceras av en data factory-pipelinen eller inte. |Nej |false |
| availability | Definierar fönstret bearbetning eller slicing modellen för produktion dataset. Mer information om datauppsättningen segmentering modellen finns [schemaläggning och körning](data-factory-scheduling-and-execution.md) artikel. |Ja |Ej tillämpligt |
| policy |Definierar villkoren eller det villkor som dataset-segment måste vara uppfyllda. <br/><br/>Mer information finns i [Dataset princip](#Policy) avsnitt. |Nej |Ej tillämpligt |

Varje kolumn i den **struktur** avsnittet innehåller följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| namn |Namnet på kolumnen. |Ja |
| typ |Datatypen för kolumnen.  |Nej |
| kultur |.NET baserat kulturen som ska användas när typ har angetts och .NET-typ `Datetime` eller `Datetimeoffset`. Standardvärdet är `en-us`. |Nej |
| Format |Formatsträng som ska användas när typ har angetts och .NET-typ `Datetime` eller `Datetimeoffset`. |Nej |

I följande exempel datamängden har tre kolumner `slicetimestamp`, `projectname`, och `pageviews` och de är av typen: String, String och Decimal respektive.

```json
structure:  
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

Följande tabell beskriver egenskaper som kan användas i den **tillgänglighet** avsnitt:

| Egenskap  | Beskrivning | Krävs | Standard |
| --- | --- | --- | --- |
| frequency |Anger tidsenheten för dataset sektorn produktion.<br/><br/><b>Stöd för frekvens</b>: minut, timma, dag, vecka, månad |Ja |Ej tillämpligt |
| interval |Anger en multiplikator för frekvens<br/><br/>”X frekvensintervall” avgör hur ofta sektorn skapas.<br/><br/>Om du behöver datamängden som segmenterat timme kan du ange <b>frekvens</b> till <b>timme</b>, och <b>intervall</b> till <b>1</b>.<br/><br/><b>Obs</b>: Om du anger frekvens som minut, rekommenderar vi att du anger intervallet till mindre än 15 |Ja |Ej tillämpligt |
| format |Anger om sektorn ska produceras start/slutet av intervallet.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Om frekvensen är inställd på månad och stil anges till EndOfInterval producerade sektorn på den sista dagen i månaden. Om formatet som har angetts till StartOfInterval producerade sektorn på den första dagen i månaden.<br/><br/>Om frekvensen är inställd på dagen och stil anges till EndOfInterval producerade sektorn under den senaste timmen på dagen.<br/><br/>Om frekvensen är inställd på timme och stil anges till EndOfInterval producerade sektorn i slutet av en timme. För ett segment för PM 1 – 2 PM period, till exempel produceras sektorn klockan 2. |Nej |EndOfInterval |
| anchorDateTime |Definierar absolut placering i tid som används av Schemaläggaren för att beräkna dataset sektorn gränser. <br/><br/><b>Obs</b>: om AnchorDateTime har datumdelar som är mer detaljerad än frekvensen sedan mer detaljerade delar ignoreras. <br/><br/>Till exempel om den <b>intervall</b> är <b>varje timme</b> (frekvens: timme och intervall: 1) och <b>AnchorDateTime</b> innehåller <b>minuter och sekunder</b>sedan <b>minuter och sekunder</b> delar av AnchorDateTime ignoreras. |Nej |01/01/0001 |
| förskjutning |TimeSpan som start- och slutdatum för alla dataset segment flyttat. <br/><br/><b>Obs</b>: om både anchorDateTime och offset anges, resultatet är kombinerade SKIFT. |Nej |Ej tillämpligt |

I följande avsnitt för tillgänglighet anger att datamängd för utdata är producerade varje timme (eller) indata dataset finns varje timme:

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 1    
}
```

Den **princip** avsnitt i datauppsättningsdefinitionen definierar villkoren eller det villkor som dataset-segment måste vara uppfyllda.

| Principnamn | Beskrivning | Tillämpas på | Krävs | Standard |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Validerar att data i en **Azure blob** uppfyller minsta storlek (i megabyte). |Azure-blobb |Nej |Ej tillämpligt |
| minimumRows |Validerar att data i en **Azure SQL database** eller en **Azure-tabellen** innehåller det minsta antalet rader. |<ul><li>Azure SQL Database</li><li>Azure-tabell</li></ul> |Nej |Ej tillämpligt |

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

Om ett DataSet-objekt produceras av Azure Data Factory, bör det markeras som **externa**. Den här inställningen gäller vanligtvis indata som första aktivitet i en pipeline om aktiviteten eller pipeline länkning används.

| Namn | Beskrivning | Krävs | Standardvärde |
| --- | --- | --- | --- |
| dataDelay |Tid för att kontrollera tillgängligheten för externa data för den angivna sektorn fördröjning. Om data finns varje timme, kan kontrollen externa data är tillgängliga och motsvarande sektorn är klar försenas med hjälp av dataDelay.<br/><br/>Gäller endast den aktuella tiden.  Om det är 1:00 PM just nu och det här värdet är 10 minuter, till exempel startas valideringen klockan 13:10.<br/><br/>Den här inställningen påverkar inte segment tidigare (segment med segment sluttid + dataDelay < nu) bearbetas utan fördröjning.<br/><br/>Tid som är större än 23:59 timmar måste anges med den `day.hours:minutes:seconds` format. Till exempel vill ange 24 timmar, Använd inte 24:00:00; Använd i stället 1.00:00:00. Om du använder 24:00:00, behandlas den som 24 dagar (24.00:00:00). Ange 1:04:00:00 för 1 dag och 4 timmar. |Nej |0 |
| retryInterval |Väntetiden mellan ett fel och nästa försök försök. Om ett försök misslyckas, är nästa försök efter retryInterval. <br/><br/>Om den är 1:00 PM just nu kan börja vi första försöket. Om tid att slutföra den första verifieringen är 1 minut och åtgärden misslyckades, nästa försök görs 1:00 + 1 min. (varaktighet) + 1 min. (Återförsöksintervall) = 1:02 PM. <br/><br/>Segment tidigare finns det ingen fördröjning. Den här gången sker omedelbart. |Nej |00:01:00 (1 minut) |
| retryTimeout |Tidsgräns för varje nytt försök.<br/><br/>Om den här egenskapen har angetts till 10 minuter måste valideringen slutföras inom 10 minuter. Om det tar längre tid än 10 minuter att utföra valideringen timeout för och försök igen.<br/><br/>Om alla försök för validering timeout har sektorn markerats som för lång tid. |Nej |00:10:00 (10 minuter) |
| maximumRetry |Antal gånger för att kontrollera tillgänglighet för externa data. Det högsta tillåtna värdet är 10. |Nej |3 |


## <a name="data-stores"></a>DATALAGER
Den [länkade tjänsten](#linked-service) avsnitt som finns beskrivningar för JSON-element som är gemensamma för alla typer av länkade tjänster. Det här avsnittet innehåller information om JSON-element som är specifika för varje dataarkiv.

Den [Dataset](#dataset) avsnitt som finns beskrivningar för JSON-element som är gemensamma för alla typer av datauppsättningar. Det här avsnittet innehåller information om JSON-element som är specifika för varje dataarkiv.

Den [aktiviteten](#activity) avsnitt som finns beskrivningar för JSON-element som är gemensamma för alla typer av aktiviteter. Det här avsnittet innehåller information om JSON-element som är specifika för varje dataarkiv när den används som en källor/mottagare i en Kopieringsaktivitet.  

Klicka på länken för det arkivet som du är intresserad av att se JSON-scheman för länkad tjänst och dataset källor/mottagare för kopieringsaktiviteten.

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
Det finns två typer av länkade tjänster: Azure länkade lagringstjänsten och Azure Storage SAS länkade tjänsten.

#### <a name="azure-storage-linked-service"></a>Länkad Azure Storage-tjänst
Länka ditt Azure storage-konto till en datafabrik med hjälp av den **kontonyckel**, skapa en länkad Azure Storage-tjänst. Definiera ett Azure Storage länkade tjänsten genom att ange den **typen** på den länkade tjänsten till **AzureStorage**. Du kan sedan ange följande egenskaper i den **typeProperties** avsnitt:  

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| connectionString |Ange information som krävs för att ansluta till Azure-lagring för egenskapen connectionString. |Ja |

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

#### <a name="azure-storage-sas-linked-service"></a>Azure Storage SAS länkade tjänsten
Azure Storage SAS länkade tjänsten kan du länka ett Azure Storage-konto till ett Azure data factory med hjälp av en delad signatur åtkomst (SAS). Det ger datafabriken begränsad/Tidsbundna åtkomst till alla utvalda resurser (blobbehållare) i lagringen. Länkade tjänsten för att länka ditt Azure storage-konto till en datafabrik med signatur för delad åtkomst, skapa ett Azure Storage-SAS. Definiera ett Azure Storage SAS länkade tjänsten genom att ange den **typen** på den länkade tjänsten till **AzureStorageSas**. Du kan sedan ange följande egenskaper i den **typeProperties** avsnitt:   

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| sasUri |Ange URI för delad åtkomst-signaturen i Azure Storage-resurser, till exempel blob, behållare eller tabellen. |Ja |

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

Mer information om dessa länkade tjänster finns [Azure Blob Storage connector](data-factory-azure-blob-connector.md#linked-service-properties) artikel. 

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en Azure-blobbdatauppsättning, ange den **typen** på datamängden som ska **AzureBlob**. Sedan anger du följande Azure Blob specifika egenskaper i den **typeProperties** avsnitt: 

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Sökvägen till behållaren och mappen i blob storage. Exempel: myblobcontainer\myblobfolder\ |Ja |
| fileName |Namnet på blob. Filnamnet är valfria och skiftlägeskänsligt.<br/><br/>Om du anger ett filnamn fungerar aktiviteten (inklusive kopia) i specifika Blob.<br/><br/>Om filnamnet har angetts, innehåller kopia alla BLOB i mappsökvägen för inkommande dataset.<br/><br/>Om filnamnet inte anges för en datamängd för utdata är namnet på den genererade filen i följande följande format: Data. <Guid>.txt (till exempel:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nej |
| partitionedBy |partitionedBy är en valfri egenskap. Du kan använda den för att ange en dynamisk folderPath och ett filnamn för tid series-data. Exempelvis kan folderPath parameteriseras för varje timme av data. |Nej |
| Format | Följande format stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Ange den **typen** egenskap under format till ett av dessa värden. Mer information finns i [textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-formatet](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), och [parkettgolv Format](data-factory-supported-file-and-compression-formats.md#parquet-format) avsnitt. <br><br> Om du vill **kopiera filer som-är** mellan filbaserade butiker (binär kopia), hoppa över avsnittet format i både inkommande och utgående dataset-definitioner. |Nej |
| komprimering | Ange typ och kompression för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**. Nivåer som stöds är: **Optimal** och **snabbast**. Mer information finns i [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nej |

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


Mer information finns i [Azure Blob-koppling](data-factory-azure-blob-connector.md#dataset-properties) artikel.

### <a name="blobsource-in-copy-activity"></a>BlobSource i en Kopieringsaktivitet
Om du vill kopiera data från ett Azure Blob Storage, ange den **typ av datakälla** för kopieringsaktiviteten till **BlobSource**, och ange följande egenskaper i den **källa** avsnitt:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| rekursiva |Anger om data läses rekursivt från undermappar eller endast från den angivna mappen. |SANT (standardvärdet), FALSKT |Nej |

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
### <a name="blobsink-in-copy-activity"></a>BlobSink i en Kopieringsaktivitet
Om du kopierar data till ett Azure Blob Storage, ange den **sink typen** för kopieringsaktiviteten till **BlobSink**, och ange följande egenskaper i den **sink** avsnitt:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| copyBehavior |Definierar beteendet kopia när källan är BlobSource eller filsystem. |<b>PreserveHierarchy</b>: bevarar fil hierarkin i målmappen. Den relativa sökvägen för källfilen till källmappen är identisk med den relativa sökvägen för filen till målmappen.<br/><br/><b>FlattenHierarchy</b>: alla filer från källmappen finns i den första nivån i målmappen. Mål-filer har genereras automatiskt namn. <br/><br/><b>MergeFiles (standard):</b> sammanfogar alla filer från källmappen till en fil. Om namnet på filen/Blob har angetts, är kopplade filnamnet det angivna namnet; annars skulle vara automatiskt genererade filnamn. |Nej |

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

Mer information finns i [Azure Blob-koppling](data-factory-azure-blob-connector.md#copy-activity-properties) artikel. 

## <a name="azure-data-lake-store"></a>Azure Data Lake Store

### <a name="linked-service"></a>Länkad tjänst
Definiera ett Azure Data Lake Store länkade tjänsten genom att ange typ av den länkade tjänsten till **AzureDataLakeStore**, och ange följande egenskaper i den **typeProperties** avsnitt:  

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **AzureDataLakeStore** | Ja |
| dataLakeStoreUri | Ange information om Azure Data Lake Store-konto. Det är i följande format: `https://[accountname].azuredatalakestore.net/webhdfs/v1` eller `adl://[accountname].azuredatalakestore.net/`. | Ja |
| subscriptionId | Id för Azure-prenumeration som Data Lake Store tillhör. | Krävs för sink |
| resourceGroupName | Azure resursgruppens namn som Datasjölager tillhör. | Krävs för sink |
| servicePrincipalId | Ange programmets klient-ID. | Ja (för tjänstens huvudnamn autentisering) |
| servicePrincipalKey | Ange programmets nyckeln. | Ja (för tjänstens huvudnamn autentisering) |
| klient | Ange information om klient (domain name eller klient ID) under där programmet finns. Du kan hämta den håller musen i övre högra hörnet i Azure-portalen. | Ja (för tjänstens huvudnamn autentisering) |
| Auktorisering | Klicka på **auktorisera** knappen i den **Data Factory-redigeraren** och ange dina autentiseringsuppgifter som tilldelar automatiskt genererade auktorisering URL: en till den här egenskapen. | Ja (för autentisering av autentiseringsuppgifter för användare)|
| sessions-ID | OAuth sessions-id från OAuth-auktorisering sessionen. Varje sessions-id är unikt och får endast användas en gång. Den här inställningen genereras automatiskt när du använder Data Factory-redigeraren. | Ja (för autentisering av autentiseringsuppgifter för användare) |

#### <a name="example-using-service-principal-authentication"></a>Exempel: med service principal autentisering
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

Mer information finns i [Azure Data Lake Store connector](data-factory-azure-datalake-connector.md#linked-service-properties) artikel. 

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en datauppsättning för Azure Data Lake Store, ange den **typen** på datamängden som ska **AzureDataLakeStore**, och ange följande egenskaper i den **typeProperties** avsnitt: 

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| folderPath |Sökvägen till behållaren och mappen i Azure Data Lake lagra. |Ja |
| fileName |Namnet på filen i Azure Data Lake store. Filnamnet är valfria och skiftlägeskänsligt. <br/><br/>Om du anger ett filnamn, fungerar aktiviteten (inklusive kopia) för specifik fil.<br/><br/>Om filnamnet har angetts innehåller kopiera alla filer i folderPath för inkommande dataset.<br/><br/>Om filnamnet inte anges för en datamängd för utdata är namnet på den genererade filen i följande följande format: Data. <Guid>.txt (till exempel:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nej |
| partitionedBy |partitionedBy är en valfri egenskap. Du kan använda den för att ange en dynamisk folderPath och ett filnamn för tid series-data. Exempelvis kan folderPath parameteriseras för varje timme av data. |Nej |
| Format | Följande format stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Ange den **typen** egenskap under format till ett av dessa värden. Mer information finns i [textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-formatet](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), och [parkettgolv Format](data-factory-supported-file-and-compression-formats.md#parquet-format) avsnitt. <br><br> Om du vill **kopiera filer som-är** mellan filbaserade butiker (binär kopia), hoppa över avsnittet format i både inkommande och utgående dataset-definitioner. |Nej |
| komprimering | Ange typ och kompression för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**. Nivåer som stöds är: **Optimal** och **snabbast**. Mer information finns i [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nej |

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

Mer information finns i [Azure Data Lake Store connector](data-factory-azure-datalake-connector.md#dataset-properties) artikel. 

### <a name="azure-data-lake-store-source-in-copy-activity"></a>Azure Data Lake Store-källan i en Kopieringsaktivitet
Om du vill kopiera data från ett Azure Data Lake Store, ange den **typ av datakälla** för kopieringsaktiviteten till **AzureDataLakeStoreSource**, och ange följande egenskaper i den **källa**avsnitt:

**AzureDataLakeStoreSource** stöder följande egenskaper **typeProperties** avsnitt:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| rekursiva |Anger om data läses rekursivt från undermappar eller endast från den angivna mappen. |SANT (standardvärdet), FALSKT |Nej |

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

Mer information finns i [Azure Data Lake Store connector](data-factory-azure-datalake-connector.md#copy-activity-properties) artikel.

### <a name="azure-data-lake-store-sink-in-copy-activity"></a>Azure Data Lake Store mottagare i en Kopieringsaktivitet
Om du kopierar data till ett Azure Data Lake Store, ange den **sink typen** för kopieringsaktiviteten till **AzureDataLakeStoreSink**, och ange följande egenskaper i den **sink** avsnitt:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| copyBehavior |Anger beteendet kopia. |<b>PreserveHierarchy</b>: bevarar fil hierarkin i målmappen. Den relativa sökvägen för källfilen till källmappen är identisk med den relativa sökvägen för filen till målmappen.<br/><br/><b>FlattenHierarchy</b>: alla filer från källmappen skapas i den första nivån i målmappen. Mål-filer som skapas med namnet genereras automatiskt.<br/><br/><b>MergeFiles</b>: sammanfogar alla filer från källmappen till en fil. Om namnet på filen/Blob har angetts, är kopplade filnamnet det angivna namnet; annars skulle vara automatiskt genererade filnamn. |Nej |

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

Mer information finns i [Azure Data Lake Store connector](data-factory-azure-datalake-connector.md#copy-activity-properties) artikel. 

## <a name="azure-cosmos-db"></a>Azure Cosmos DB  

### <a name="linked-service"></a>Länkad tjänst
Att definiera en Azure-Cosmos-DB länkade tjänsten genom att ange den **typen** på den länkade tjänsten till **DocumentDb**, och ange följande egenskaper i den **typeProperties** avsnitt:  

| **Egenskap** | **Beskrivning** | **Krävs** |
| --- | --- | --- |
| connectionString |Ange information som behövs för att ansluta till Azure DB som Cosmos-databasen. |Ja |

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
Mer information finns i [Azure Cosmos DB connector](data-factory-azure-documentdb-connector.md#linked-service-properties) artikel.

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en datauppsättning för Azure Cosmos DB, ange den **typen** på datamängden som ska **DocumentDbCollection**, och ange följande egenskaper i den **typeProperties** avsnitt: 

| **Egenskap** | **Beskrivning** | **Krävs** |
| --- | --- | --- |
| Samlingsnamn |Namnet på Azure DB som Cosmos-samling. |Ja |

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
Mer information finns i [Azure Cosmos DB connector](data-factory-azure-documentdb-connector.md#dataset-properties) artikel.

### <a name="azure-cosmos-db-collection-source-in-copy-activity"></a>Azure Cosmos DB samling källa i en Kopieringsaktivitet
Om du vill kopiera data från en Azure-Cosmos-databas, ange den **typ av datakälla** för kopieringsaktiviteten till **DocumentDbCollectionSource**, och ange följande egenskaper i den **källa**avsnitt:


| **Egenskap** | **Beskrivning** | **Tillåtna värden** | **Krävs** |
| --- | --- | --- | --- |
| DocumentDB |Ange frågan som läser data. |Frågesträng stöds av Azure Cosmos DB. <br/><br/>Exempel: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nej <br/><br/>Om inget annat anges, SQL-instruktionen som körs: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Specialtecken som visar att dokumentet är kapslad |Valfritt tecken. <br/><br/>Azure Cosmos-DB är en NoSQL store för JSON-dokument, där kapslade strukturer är tillåtna. Azure Data Factory gör det möjligt för användaren att ange hierarkin via nestingSeparator, vilket är ””. i ovanstående exempel. Med avgränsare, kopieringsaktiviteten genererar ”Name”-objektet med tre underordnade element först mellan- och efternamn enligt ”Name.First”, ”Name.Middle” och ”Name.Last” i tabelldefinitionen. |Nej |

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

### <a name="azure-cosmos-db-collection-sink-in-copy-activity"></a>Azure Cosmos DB samling mottagare i en Kopieringsaktivitet
Om du kopierar data till Azure Cosmos DB, ange den **sink typen** för kopieringsaktiviteten till **DocumentDbCollectionSink**, och ange följande egenskaper i den **sink** avsnitt :

| **Egenskap** | **Beskrivning** | **Tillåtna värden** | **Krävs** |
| --- | --- | --- | --- |
| nestingSeparator |Ett specialtecken i källkolumnsnamnet att ange kapslade dokumentet krävs. <br/><br/>Till exempel ovan: `Name.First` i utdata tabell ger följande JSON-strukturen i Cosmos-DB-dokument:<br/><br/>”Name”: {<br/>    ”Första”: ”John”<br/>}, |Tecken som används för att avgränsa kapslingsnivåer.<br/><br/>Standardvärdet är `.` (punkt). |Tecken som används för att avgränsa kapslingsnivåer. <br/><br/>Standardvärdet är `.` (punkt). |
| writeBatchSize |Antalet parallella begäranden till Azure DB som Cosmos-tjänsten att skapa dokument.<br/><br/>Du kan finjustera prestanda vid kopiering av data till och från Azure Cosmos DB genom att använda den här egenskapen. Du kan förvänta dig bättre prestanda om du ökar writeBatchSize eftersom fler parallella begäranden till Azure Cosmos DB skickas. Men du behöver undvika begränsning som kan utlösa ett felmeddelande: ”begär frekvensen är stor”.<br/><br/>Begränsning bestäms av ett antal faktorer, bland annat storlek dokument, antalet villkoren i dokument, indexering princip målsamling osv. För kopieringsåtgärd, du kan använda en bättre samling (till exempel S3) har mest genomströmning tillgänglig (2 500 begärande enheter per sekund). |Integer |Nej (standard: 5) |
| writeBatchTimeout |Vänta tills åtgärden har slutförts innan tidsgränsen uppnås. |TimeSpan<br/><br/> Exempel ”: 00: 30:00” (30 minuter). |Nej |

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

Mer information finns i [Azure Cosmos DB connector](data-factory-azure-documentdb-connector.md#copy-activity-properties) artikel.

## <a name="azure-sql-database"></a>Azure SQL Database

### <a name="linked-service"></a>Länkad tjänst
Definiera en Azure SQL Database länkade tjänsten genom att ange den **typen** på den länkade tjänsten till **AzureSqlDatabase**, och ange följande egenskaper i den **typeProperties** avsnitt:  

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| connectionString |Ange information som behövs för att ansluta till Azure SQL Database-instans för egenskapen connectionString. |Ja |

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

Mer information finns i [Azure SQL-anslutningen](data-factory-azure-sql-connector.md#linked-service-properties) artikel. 

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en datauppsättning för Azure SQL Database, ange den **typen** på datamängden som ska **AzureSqlTable**, och ange följande egenskaper i den **typeProperties** avsnitt: 

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på den tabell eller vy i Azure SQL Database-instans som den länkade tjänsten refererar till. |Ja |

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
Mer information finns i [Azure SQL-anslutningen](data-factory-azure-sql-connector.md#dataset-properties) artikel. 

### <a name="sql-source-in-copy-activity"></a>SQL-datakälla i en Kopieringsaktivitet
Om du vill kopiera data från en Azure SQL Database, ange den **typ av datakälla** för kopieringsaktiviteten till **SqlSource**, och ange följande egenskaper i den **källa** avsnitt:


| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| sqlReaderQuery |Använd anpassad fråga för att läsa data. |SQL-sträng. Exempel: `select * from MyTable`. |Nej |
| sqlReaderStoredProcedureName |Namnet på den lagrade proceduren som läser data från källtabellen. |Namnet på den lagrade proceduren. |Nej |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värde-par. Namn och skiftläge parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. |Nej |

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
Mer information finns i [Azure SQL-anslutningen](data-factory-azure-sql-connector.md#copy-activity-properties) artikel. 

### <a name="sql-sink-in-copy-activity"></a>SQL-mottagare i en Kopieringsaktivitet
Om du kopierar data till Azure SQL Database, ange den **sink typen** för kopieringsaktiviteten till **SqlSink**, och ange följande egenskaper i den **sink** avsnitt:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| writeBatchTimeout |Vänta tills batch insert-åtgärden ska slutföras innan tidsgränsen uppnås. |TimeSpan<br/><br/> Exempel ”: 00: 30:00” (30 minuter). |Nej |
| writeBatchSize |Infogar data i SQL-tabellen när buffertstorleken når writeBatchSize. |Heltal (antalet rader) |Nej (standard: 10000) |
| sqlWriterCleanupScript |Ange en fråga för Kopieringsaktiviteten att köra så att data för ett visst segment har rensats bort. |En frågesats. |Nej |
| sliceIdentifierColumnName |Ange ett kolumnnamn för Kopieringsaktiviteten med genereras automatiskt segment-ID, som används för att rensa data för ett visst segment när köras på nytt. |Kolumnnamnet för en kolumn med datatypen för binary(32). |Nej |
| sqlWriterStoredProcedureName |Namnet på den lagrade proceduren upserts (uppdateringar/infogar) data i måltabellen. |Namnet på den lagrade proceduren. |Nej |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värde-par. Namn och skiftläge parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. |Nej |
| sqlWriterTableType |Ange ett tabell-typnamn som ska användas i den lagrade proceduren. Kopieringsaktiviteten tillhandahåller data flyttas i en temporär tabell med den här tabellen. Lagrade procedurer kan sedan koppla data kopieras med befintliga data. |Ett namn för tabellen. |Nej |

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

Mer information finns i [Azure SQL-anslutningen](data-factory-azure-sql-connector.md#copy-activity-properties) artikel. 

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

### <a name="linked-service"></a>Länkad tjänst
Definiera en Azure SQL Data Warehouse länkade tjänsten genom att ange den **typen** på den länkade tjänsten till **AzureSqlDW**, och ange följande egenskaper i den **typeProperties** avsnitt:  

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| connectionString |Ange information som behövs för att ansluta till Azure SQL Data Warehouse-instans för egenskapen connectionString. |Ja |



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

Mer information finns i [Azure SQL Data Warehouse connector](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties) artikel. 

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en datauppsättning för Azure SQL Data Warehouse, ange den **typen** på datamängden som ska **AzureSqlDWTable**, och ange följande egenskaper i den **typeProperties** avsnitt: 

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på den tabell eller vy i Azure SQL Data Warehouse-databas som den länkade tjänsten refererar till. |Ja |

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

Mer information finns i [Azure SQL Data Warehouse connector](data-factory-azure-sql-data-warehouse-connector.md#dataset-properties) artikel. 

### <a name="sql-dw-source-in-copy-activity"></a>SQL DW-datakälla i en Kopieringsaktivitet
Om du vill kopiera data från Azure SQL Data Warehouse, anger du den **typ av datakälla** för kopieringsaktiviteten till **SqlDWSource**, och ange följande egenskaper i den **källa** avsnitt:


| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| sqlReaderQuery |Använd anpassad fråga för att läsa data. |SQL-sträng. Till exempel: `select * from MyTable`. |Nej |
| sqlReaderStoredProcedureName |Namnet på den lagrade proceduren som läser data från källtabellen. |Namnet på den lagrade proceduren. |Nej |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värde-par. Namn och skiftläge parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. |Nej |

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

Mer information finns i [Azure SQL Data Warehouse connector](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) artikel. 

### <a name="sql-dw-sink-in-copy-activity"></a>SQL DW mottagare i en Kopieringsaktivitet
Om du kopierar data till Azure SQL Data Warehouse, anger du den **sink typen** för kopieringsaktiviteten till **SqlDWSink**, och ange följande egenskaper i den **sink** avsnitt:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Ange en fråga för Kopieringsaktiviteten att köra så att data för ett visst segment har rensats bort. |En frågesats. |Nej |
| allowPolyBase |Anger om du vill använda PolyBase (i förekommande fall) i stället för BULKINSERT mekanism. <br/><br/> **Med PolyBase är det rekommenderade sättet att läsa in data till SQL Data Warehouse.** |True <br/>FALSKT (standard) |Nej |
| polyBaseSettings |En grupp egenskaper som kan anges när den **allowPolybase** egenskap är inställd på **SANT**. |&nbsp; |Nej |
| rejectValue |Anger det antal eller procentandelen rader som kan avvisas innan frågan misslyckas. <br/><br/>Mer information om den PolyBase avvisa alternativ i den **argument** avsnitt i [Skapa extern tabell (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) avsnittet. |0 (standard), 1, 2... |Nej |
| rejectType |Anger om alternativet rejectValue har angetts som ett litteralvärde eller ett procentvärde. |Värdet (standard), procent |Nej |
| rejectSampleValue |Anger antalet rader som ska hämtas innan PolyBase beräknar andelen Avvisade rader. |1, 2, … |Ja, om **rejectType** är **procent** |
| useTypeDefault |Anger hur du hanterar saknade värden i avgränsade textfiler när PolyBase hämtar data från filen.<br/><br/>Mer information om denna egenskap från avsnittet argument i [skapa externt FILFORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |SANT, FALSKT (standard) |Nej |
| writeBatchSize |Infogar data i SQL-tabellen när buffertstorleken når writeBatchSize |Heltal (antalet rader) |Nej (standard: 10000) |
| writeBatchTimeout |Vänta tills batch insert-åtgärden ska slutföras innan tidsgränsen uppnås. |TimeSpan<br/><br/> Exempel ”: 00: 30:00” (30 minuter). |Nej |

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

Mer information finns i [Azure SQL Data Warehouse connector](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties) artikel. 

## <a name="azure-search"></a>Azure Search

### <a name="linked-service"></a>Länkad tjänst
Definiera ett Azure Search länkade tjänsten genom att ange den **typ** på den länkade tjänsten till **AzureSearch**, och ange följande egenskaper i den **typeProperties** avsnitt:  

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

Mer information finns i [Azure Search connector](data-factory-azure-search-connector.md#linked-service-properties) artikel.

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en datauppsättning för Azure Search kan du ange den **typen** på datamängden som ska **AzureSearchIndex**, och ange följande egenskaper i den **typeProperties** avsnitt: 

| Egenskap  | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| typ | Egenskapen type måste anges till **AzureSearchIndex**.| Ja |
| Indexnamn | Namnet på det Azure Search-indexet. Data Factory kan inte skapa indexet. Index måste finnas i Azure Search. | Ja |

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

Mer information finns i [Azure Search connector](data-factory-azure-search-connector.md#dataset-properties) artikel.

### <a name="azure-search-index-sink-in-copy-activity"></a>Azure Search Index mottagare i en Kopieringsaktivitet
Om du kopierar data till ett Azure Search-index, ange den **sink typen** för kopieringsaktiviteten till **AzureSearchIndexSink**, och ange följande egenskaper i den **sink** avsnitt:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Anger om du vill slå samman eller ersätta ett dokument som redan finns i indexet. | Sammanfoga (standard)<br/>Ladda upp| Nej |
| WriteBatchSize | Överför data till Azure Search index när buffertstorleken når writeBatchSize. | 1 till 1 000. Standardvärdet är 1000. | Nej |

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

Mer information finns i [Azure Search connector](data-factory-azure-search-connector.md#copy-activity-properties) artikel.

## <a name="azure-table-storage"></a>Azure Table Storage

### <a name="linked-service"></a>Länkad tjänst
Det finns två typer av länkade tjänster: Azure länkade lagringstjänsten och Azure Storage SAS länkade tjänsten.

#### <a name="azure-storage-linked-service"></a>Länkad Azure Storage-tjänst
Länka ditt Azure storage-konto till en datafabrik med hjälp av den **kontonyckel**, skapa en länkad Azure Storage-tjänst. Definiera ett Azure Storage länkade tjänsten genom att ange den **typen** på den länkade tjänsten till **AzureStorage**. Du kan sedan ange följande egenskaper i den **typeProperties** avsnitt:  

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Egenskapen type måste anges till: **AzureStorage** |Ja |
| connectionString |Ange information som krävs för att ansluta till Azure-lagring för egenskapen connectionString. |Ja |

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

#### <a name="azure-storage-sas-linked-service"></a>Azure Storage SAS länkade tjänsten
Azure Storage SAS länkade tjänsten kan du länka ett Azure Storage-konto till ett Azure data factory med hjälp av en delad signatur åtkomst (SAS). Det ger datafabriken begränsad/Tidsbundna åtkomst till alla utvalda resurser (blobbehållare) i lagringen. Länkade tjänsten för att länka ditt Azure storage-konto till en datafabrik med signatur för delad åtkomst, skapa ett Azure Storage-SAS. Definiera ett Azure Storage SAS länkade tjänsten genom att ange den **typen** på den länkade tjänsten till **AzureStorageSas**. Du kan sedan ange följande egenskaper i den **typeProperties** avsnitt:   

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Egenskapen type måste anges till: **AzureStorageSas** |Ja |
| sasUri |Ange URI för delad åtkomst-signaturen i Azure Storage-resurser, till exempel blob, behållare eller tabellen. |Ja |

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

Mer information om dessa länkade tjänster finns [Azure Table Storage connector](data-factory-azure-table-connector.md#linked-service-properties) artikel. 

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en datauppsättning för Azure Table, ange den **typen** på datamängden som ska **AzureTable**, och ange följande egenskaper i den **typeProperties** avsnitt: 

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i Azure Table databasinstansen som den länkade tjänsten refererar till. |Ja. När ett tabellnamn har angetts utan ett azureTableSourceQuery, kopieras alla poster från tabellen till målet. Om en azureTableSourceQuery också anges kopieras poster från tabellen som uppfyller frågan till målet. |

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

Mer information om dessa länkade tjänster finns [Azure Table Storage connector](data-factory-azure-table-connector.md#dataset-properties) artikel. 

### <a name="azure-table-source-in-copy-activity"></a>Azure Tabellkälla i en Kopieringsaktivitet
Om du vill kopiera data från Azure Table Storage, ange den **typ av datakälla** för kopieringsaktiviteten till **AzureTableSource**, och ange följande egenskaper i den **källa** avsnitt:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| azureTableSourceQuery |Använd anpassad fråga för att läsa data. |Azure-tabellen frågesträngen. Se exemplen i nästa avsnitt. |Nej. När ett tabellnamn har angetts utan ett azureTableSourceQuery, kopieras alla poster från tabellen till målet. Om en azureTableSourceQuery också anges kopieras poster från tabellen som uppfyller frågan till målet. |
| azureTableSourceIgnoreTableNotFound |Ange om swallow undantag av tabellen inte finns. |SANT<br/>FALSKT |Nej |

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

Mer information om dessa länkade tjänster finns [Azure Table Storage connector](data-factory-azure-table-connector.md#copy-activity-properties) artikel. 

### <a name="azure-table-sink-in-copy-activity"></a>Azure-tabellen mottagare i en Kopieringsaktivitet
Om du kopierar data till Azure Table Storage, ange den **sink typen** för kopieringsaktiviteten till **AzureTableSink**, och ange följande egenskaper i den **sink** avsnitt:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Standard partitionsnyckelvärde som kan användas av sink. |Ett strängvärde. |Nej |
| azureTablePartitionKeyName |Ange namnet på den kolumn som används som partitionsnycklar. Om inget anges används AzureTableDefaultPartitionKeyValue som partitionsnyckel. |Ett kolumnnamn. |Nej |
| azureTableRowKeyName |Ange namnet på den kolumn vars kolumnvärdena används som radnyckel. Om inget annat anges, kan du använda ett GUID för varje rad. |Ett kolumnnamn. |Nej |
| azureTableInsertType |Läget att infoga data i Azure-tabellen.<br/><br/>Den här egenskapen anger om befintliga rader i utdatatabellen med matchande partition och radnycklar få sina värden bytas ut eller samman. <br/><br/>Läs om hur dessa inställningar (dokument och Ersätt) fungerar i [Insert- eller Merge-entiteten](https://msdn.microsoft.com/library/azure/hh452241.aspx) och [infoga eller ersätta entiteten](https://msdn.microsoft.com/library/azure/hh452242.aspx) avsnitt. <br/><br> Den här inställningen gäller på radnivå inte tabellnivån, varken alternativet tar bort rader i utdatatabellen som inte finns i indata. |sammanfoga (standard)<br/>Ersätt |Nej |
| writeBatchSize |Infogar data i Azure-tabellen när writeBatchSize eller writeBatchTimeout namn. |Heltal (antalet rader) |Nej (standard: 10000) |
| writeBatchTimeout |Infogar data i Azure-tabellen när writeBatchSize eller writeBatchTimeout namn |TimeSpan<br/><br/>Exempel ”: 00: 20:00” (20 minuter) |Nej (för lagring klienten standardtimeout standardvärdet 90 sek) |

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
Mer information om dessa länkade tjänster finns [Azure Table Storage connector](data-factory-azure-table-connector.md#copy-activity-properties) artikel. 

## <a name="amazon-redshift"></a>Amazon RedShift

### <a name="linked-service"></a>Länkad tjänst
Definiera en Amazon Redshift länkade tjänsten genom att ange den **typen** på den länkade tjänsten till **AmazonRedshift**, och ange följande egenskaper i den **typeProperties** avsnitt :  

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| server |IP-adressen eller värdnamnet namnet på Amazon Redshift-servern. |Ja |
| port |Antalet TCP-porten som Amazon Redshift-servern använder för att lyssna efter anslutningar. |Nej, standardvärde: 5439 |
| databas |Namnet på Amazon Redshift-databasen. |Ja |
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

Mer information finns i [Amazon Redshift connector](#data-factory-amazon-redshift-connector.md#linked-service-properties) artikel. 

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en datauppsättning för Amazon Redshift, ange den **typen** på datamängden som ska **RelationalTable**, och ange följande egenskaper i den **typeProperties** avsnitt: 

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i databasen Amazon Redshift som den länkade tjänsten refererar till. |Nej (om **frågan** av **RelationalSource** har angetts) |


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
Mer information finns i [Amazon Redshift connector](#data-factory-amazon-redshift-connector.md#dataset-properties) artikel.

### <a name="relational-source-in-copy-activity"></a>Relationella källa i en Kopieringsaktivitet 
Om du vill kopiera data från Amazon Redshift, ange den **typ av datakälla** för kopieringsaktiviteten till **RelationalSource**, och ange följande egenskaper i den **källa** avsnitt:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd anpassad fråga för att läsa data. |SQL-sträng. Till exempel: `select * from MyTable`. |Nej (om **tableName** av **dataset** har angetts) |

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
Mer information finns i [Amazon Redshift connector](#data-factory-amazon-redshift-connector.md#copy-activity-properties) artikel.

## <a name="ibm-db2"></a>IBM DB2

### <a name="linked-service"></a>Länkad tjänst
Definiera en IBM DB2 länkade tjänsten genom att ange den **typen** på den länkade tjänsten till **OnPremisesDB2**, och ange följande egenskaper i den **typeProperties** avsnitt:  

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| server |Namnet på DB2-server. |Ja |
| databas |Namnet på DB2-databasen. |Ja |
| schemat |Namnet på schemat i databasen. Schemanamnet är skiftlägeskänslig. |Nej |
| authenticationType |Typ av autentisering som används för att ansluta till DB2-databasen. Möjliga värden är: anonym, grundläggande och Windows. |Ja |
| användarnamn |Ange användarnamnet om du använder grundläggande eller Windows-autentisering. |Nej |
| lösenord |Ange lösenordet för det användarkonto som du angav för användarnamnet. |Nej |
| gatewayName |Namnet på den gateway som Data Factory-tjänsten ska använda för att ansluta till den lokala DB2-databasen. |Ja |

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
Mer information finns i [IBM DB2-koppling](#data-factory-onprem-db2-connector.md#linked-service-properties) artikel.

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en DB2-dataset, ange den **typen** på datamängden som ska **RelationalTable**, och ange följande egenskaper i den **typeProperties** avsnitt:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i DB2-databasinstansen som den länkade tjänsten refererar till. TableName är skiftlägeskänslig. |Nej (om **frågan** av **RelationalSource** har angetts) 

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

Mer information finns i [IBM DB2-koppling](#data-factory-onprem-db2-connector.md#dataset-properties) artikel.

### <a name="relational-source-in-copy-activity"></a>Relationella källa i en Kopieringsaktivitet
Om du vill kopiera data från IBM DB2, ange den **typ av datakälla** för kopieringsaktiviteten till **RelationalSource**, och ange följande egenskaper i den **källa** avsnitt:


| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd anpassad fråga för att läsa data. |SQL-sträng. Till exempel: `"query": "select * from "MySchema"."MyTable""`. |Nej (om **tableName** av **dataset** har angetts) |

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
Mer information finns i [IBM DB2-koppling](#data-factory-onprem-db2-connector.md#copy-activity-properties) artikel.

## <a name="mysql"></a>MySQL

### <a name="linked-service"></a>Länkad tjänst
Definiera en MySQL länkade tjänsten genom att ange den **typen** på den länkade tjänsten till **OnPremisesMySql**, och ange följande egenskaper i den **typeProperties** avsnitt:  

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| server |Namnet på MySQL-servern. |Ja |
| databas |Namnet på MySQL-databas. |Ja |
| schemat |Namnet på schemat i databasen. |Nej |
| authenticationType |Typ av autentisering som används för att ansluta till MySQL-databas. Möjliga värden är: `Basic`. |Ja |
| användarnamn |Ange användarnamn för att ansluta till MySQL-databas. |Ja |
| lösenord |Ange lösenordet för det användarkonto som du angett. |Ja |
| gatewayName |Namnet på den gateway som Data Factory-tjänsten ska använda för att ansluta till den lokala MySQL-databasen. |Ja |

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

Mer information finns i [MySQL connector](data-factory-onprem-mysql-connector.md#linked-service-properties) artikel. 

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en MySQL-dataset, ange den **typen** på datamängden som ska **RelationalTable**, och ange följande egenskaper i den **typeProperties** avsnitt: 

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i MySQL-databasinstans som den länkade tjänsten refererar till. |Nej (om **frågan** av **RelationalSource** har angetts) |

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
Mer information finns i [MySQL connector](data-factory-onprem-mysql-connector.md#dataset-properties) artikel. 

### <a name="relational-source-in-copy-activity"></a>Relationella källa i en Kopieringsaktivitet
Om du vill kopiera data från en MySQL-databas, ange den **typ av datakälla** för kopieringsaktiviteten till **RelationalSource**, och ange följande egenskaper i den **källa** avsnitt:


| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd anpassad fråga för att läsa data. |SQL-sträng. Till exempel: `select * from MyTable`. |Nej (om **tableName** av **dataset** har angetts) |


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

Mer information finns i [MySQL connector](data-factory-onprem-mysql-connector.md#copy-activity-properties) artikel. 

## <a name="oracle"></a>Oracle 

### <a name="linked-service"></a>Länkad tjänst
Definiera en Oracle länkade tjänsten genom att ange den **typen** på den länkade tjänsten till **OnPremisesOracle**, och ange följande egenskaper i den **typeProperties** avsnitt:  

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| driverType | Ange vilka drivrutiner som ska använda för att kopiera data från/till Oracle-databas. Tillåtna värden är **Microsoft** eller **ODP** (standard). Se [stöds version och vilka installationsalternativ](#supported-versions-and-installation) avsnitt för mer information. | Nej |
| connectionString | Ange information som behövs för att ansluta till Oracle-databasinstans för egenskapen connectionString. | Ja |
| gatewayName | Namnet på gatewayen som används för att ansluta till lokal Oracle-server |Ja |

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

Mer information finns i [Oracle connector](data-factory-onprem-oracle-connector.md#linked-service-properties) artikel.

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en datauppsättning för Oracle, ange den **typen** på datamängden som ska **OracleTable**, och ange följande egenskaper i den **typeProperties** avsnitt: 

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
Mer information finns i [Oracle connector](data-factory-onprem-oracle-connector.md#dataset-properties) artikel.

### <a name="oracle-source-in-copy-activity"></a>Oracle-källan i en Kopieringsaktivitet
Om du vill kopiera data från en Oracle-databas, ange den **typ av datakälla** för kopieringsaktiviteten till **OracleSource**, och ange följande egenskaper i den **källa** avsnitt:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| oracleReaderQuery |Använd anpassad fråga för att läsa data. |SQL-sträng. Exempel: `select * from MyTable` <br/><br/>Om inget annat anges, SQL-instruktionen som körs: `select * from MyTable` |Nej (om **tableName** av **dataset** har angetts) |

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

Mer information finns i [Oracle connector](data-factory-onprem-oracle-connector.md#copy-activity-properties) artikel.

### <a name="oracle-sink-in-copy-activity"></a>Oracle mottagare i en Kopieringsaktivitet
Om du kopierar data till am Oracle-databas, ange den **sink typen** för kopieringsaktiviteten till **OracleSink**, och ange följande egenskaper i den **sink** avsnitt:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| writeBatchTimeout |Vänta tills batch insert-åtgärden ska slutföras innan tidsgränsen uppnås. |TimeSpan<br/><br/> Exempel: 00:30:00 (30 minuter). |Nej |
| writeBatchSize |Infogar data i SQL-tabellen när buffertstorleken når writeBatchSize. |Heltal (antalet rader) |Nej (standard: 100) |
| sqlWriterCleanupScript |Ange en fråga för Kopieringsaktiviteten att köra så att data för ett visst segment har rensats bort. |En frågesats. |Nej |
| sliceIdentifierColumnName |Ange kolumnnamnet för Kopieringsaktiviteten med genereras automatiskt segment-ID, som används för att rensa data för ett visst segment när köras på nytt. |Kolumnnamnet för en kolumn med datatypen för binary(32). |Nej |

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
Mer information finns i [Oracle connector](data-factory-onprem-oracle-connector.md#copy-activity-properties) artikel.

## <a name="postgresql"></a>PostgreSQL

### <a name="linked-service"></a>Länkad tjänst
Definiera en PostgreSQL länkade tjänsten genom att ange den **typen** på den länkade tjänsten till **OnPremisesPostgreSql**, och ange följande egenskaper i den **typeProperties** avsnitt:  

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| server |Namnet på PostgreSQL-server. |Ja |
| databas |Namnet på PostgreSQL-databas. |Ja |
| schemat |Namnet på schemat i databasen. Schemanamnet är skiftlägeskänslig. |Nej |
| authenticationType |Typ av autentisering som används för att ansluta till PostgreSQL-databasen. Möjliga värden är: anonym, grundläggande och Windows. |Ja |
| användarnamn |Ange användarnamnet om du använder grundläggande eller Windows-autentisering. |Nej |
| lösenord |Ange lösenordet för det användarkonto som du angav för användarnamnet. |Nej |
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
Mer information finns i [PostgreSQL connector](data-factory-onprem-postgresql-connector.md#linked-service-properties) artikel.

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en PostgreSQL-dataset, ange den **typen** på datamängden som ska **RelationalTable**, och ange följande egenskaper i den **typeProperties** avsnitt: 

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i PostgreSQL-databasinstansen som den länkade tjänsten refererar till. TableName är skiftlägeskänslig. |Nej (om **frågan** av **RelationalSource** har angetts) |

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
Mer information finns i [PostgreSQL connector](data-factory-onprem-postgresql-connector.md#dataset-properties) artikel.

### <a name="relational-source-in-copy-activity"></a>Relationella källa i en Kopieringsaktivitet
Om du vill kopiera data från en PostgreSQL-databas, ange den **typ av datakälla** för kopieringsaktiviteten till **RelationalSource**, och ange följande egenskaper i den **källa** avsnitt:


| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd anpassad fråga för att läsa data. |SQL-sträng. Till exempel: ”frågan” ”: Välj * från \"MySchema\".\" Mytable prefix\"”. |Nej (om **tableName** av **dataset** har angetts) |

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

Mer information finns i [PostgreSQL connector](data-factory-onprem-postgresql-connector.md#copy-activity-properties) artikel.

## <a name="sap-business-warehouse"></a>SAP Business Warehouse


### <a name="linked-service"></a>Länkad tjänst
Att definiera en SAP Business Warehouse (BW) länkade tjänsten genom att ange den **typen** på den länkade tjänsten till **SapBw**, och ange följande egenskaper i den **typeProperties** avsnitt :  

Egenskap  | Beskrivning | Tillåtna värden | Krävs
-------- | ----------- | -------------- | --------
server | Namnet på den server som SAP BW-instansen finns. | sträng | Ja
systemNumber | Systemnummer för SAP BW-system. | Två siffror decimaltal representeras som en sträng. | Ja
clientId | Klient-ID för klienten i systemets SAP-W. | Tre siffror decimaltal representeras som en sträng. | Ja
användarnamn | Namnet på den användare som har åtkomst till SAP-server | sträng | Ja
lösenord | Lösenord för användaren. | sträng | Ja
gatewayName | Namnet på den gateway som Data Factory-tjänsten ska använda för att ansluta till lokal SAP BW-instans. | sträng | Ja
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

Mer information finns i [SAP Business Warehouse-anslutningsapp](data-factory-sap-business-warehouse-connector.md#linked-service-properties) artikel. 

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en SAP BW dataset, ange den **typen** på datamängden som ska **RelationalTable**. Det finns inga typspecifika egenskaper som stöds för SAP BW datauppsättningen av typen **RelationalTable**.  

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
Mer information finns i [SAP Business Warehouse-anslutningsapp](data-factory-sap-business-warehouse-connector.md#dataset-properties) artikel. 

### <a name="relational-source-in-copy-activity"></a>Relationella källa i en Kopieringsaktivitet
Om du vill kopiera data från SAP Business Warehouse, anger du den **typ av datakälla** för kopieringsaktiviteten till **RelationalSource**, och ange följande egenskaper i den **källa** avsnitt:


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

Mer information finns i [SAP Business Warehouse-anslutningsapp](data-factory-sap-business-warehouse-connector.md#copy-activity-properties) artikel. 

## <a name="sap-hana"></a>SAP HANA

### <a name="linked-service"></a>Länkad tjänst
Definiera en SAP HANA länkade tjänsten genom att ange den **typen** på den länkade tjänsten till **SapHana**, och ange följande egenskaper i den **typeProperties** avsnitt:  

Egenskap  | Beskrivning | Tillåtna värden | Krävs
-------- | ----------- | -------------- | --------
server | Namnet på den server som SAP HANA-instansen finns. Om servern använder en anpassad port, ange `server:port`. | sträng | Ja
authenticationType | Typ av autentisering. | sträng. ”Basic” eller ”Windows” | Ja 
användarnamn | Namnet på den användare som har åtkomst till SAP-server | sträng | Ja
lösenord | Lösenord för användaren. | sträng | Ja
gatewayName | Namnet på den gateway som Data Factory-tjänsten ska använda för att ansluta till lokal SAP HANA-instans. | sträng | Ja
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
Mer information finns i [SAP HANA connector](data-factory-sap-hana-connector.md#linked-service-properties) artikel.
 
### <a name="dataset"></a>Datauppsättning
Om du vill definiera en SAP HANA-dataset, ange den **typen** på datamängden som ska **RelationalTable**. Det finns inga typspecifika egenskaper som stöds för SAP HANA-dataset av typen **RelationalTable**. 

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
Mer information finns i [SAP HANA connector](data-factory-sap-hana-connector.md#dataset-properties) artikel. 

### <a name="relational-source-in-copy-activity"></a>Relationella källa i en Kopieringsaktivitet
Om du vill kopiera data från en SAP HANA-databas, ange den **typ av datakälla** för kopieringsaktiviteten till **RelationalSource**, och ange följande egenskaper i den **källa** avsnitt:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB | Anger SQL-frågan som läser data från SAP HANA-instans. | SQL-frågan. | Ja |


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

Mer information finns i [SAP HANA connector](data-factory-sap-hana-connector.md#copy-activity-properties) artikel.


## <a name="sql-server"></a>SQL Server

### <a name="linked-service"></a>Länkad tjänst
Du skapar en länkad tjänst av typen **OnPremisesSqlServer** att länka en lokal SQL Server-databas till en datafabrik. Följande tabell innehåller en beskrivning för JSON-element som är specifika för lokala SQL Server länkad tjänst.

Följande tabell innehåller en beskrivning för JSON-element som är specifika för SQL Server som är länkad tjänst.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Typegenskapen bör anges till: **OnPremisesSqlServer**. |Ja |
| connectionString |Ange connectionString information som behövs för att ansluta till lokal SQL Server-databasen med hjälp av SQL-autentisering eller Windows-autentisering. |Ja |
| gatewayName |Namnet på den gateway som Data Factory-tjänsten ska använda för att ansluta till lokal SQL Server-databasen. |Ja |
| användarnamn |Ange användarnamnet om du använder Windows-autentisering. Exempel: **domainname\\användarnamn**. |Nej |
| lösenord |Ange lösenordet för det användarkonto som du angav för användarnamnet. |Nej |

Du kan kryptera autentiseringsuppgifterna med hjälp av den **ny AzureRmDataFactoryEncryptValue** cmdlet och använda dem i anslutningssträngen som visas i följande exempel (**EncryptedCredential** egenskapen):  

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

Om användarnamn och lösenord har angetts använder gateway dem för att personifiera det angivna användarkontot kan ansluta till lokal SQL Server-databasen. Annars ansluter gateway till SQL Server direkt med säkerhetskontexten för Gateway (dess start-konto).

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

Mer information finns i [SQL Server-anslutningen](data-factory-sqlserver-connector.md#linked-service-properties) artikel. 

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en datauppsättning för SQL Server, ange den **typen** på datamängden som ska **SqlServerTable**, och ange följande egenskaper i den **typeProperties** avsnitt: 

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på den tabell eller vy i SQL Server-databasinstansen som den länkade tjänsten refererar till. |Ja |

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

Mer information finns i [SQL Server-anslutningen](data-factory-sqlserver-connector.md#dataset-properties) artikel. 

### <a name="sql-source-in-copy-activity"></a>SQL-datakälla i en Kopieringsaktivitet
Om du vill kopiera data från en SQL Server-databas, ange den **typ av datakälla** för kopieringsaktiviteten till **SqlSource**, och ange följande egenskaper i den **källa** avsnitt:


| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| sqlReaderQuery |Använd anpassad fråga för att läsa data. |SQL-sträng. Till exempel: `select * from MyTable`. Kan referera till flera tabeller från databasen som refereras av inkommande dataset. Om inget annat anges, SQL-instruktionen som körs: Välj från mytable prefix. |Nej |
| sqlReaderStoredProcedureName |Namnet på den lagrade proceduren som läser data från källtabellen. |Namnet på den lagrade proceduren. |Nej |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värde-par. Namn och skiftläge parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. |Nej |

Om den **sqlReaderQuery** har angetts för SqlSource, kopiera-aktivitet körs den här frågan mot SQL Server-databas källan för att hämta data.

Du kan också ange en lagrad procedur genom att ange den **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren har parametrar).

Om du inte anger sqlReaderQuery eller sqlReaderStoredProcedureName, används de kolumner som anges i strukturen för att skapa en select-frågan ska köras mot SQL Server-databasen. Om datauppsättningsdefinitionen saknar strukturen, markeras alla kolumner från tabellen.

> [!NOTE]
> När du använder **sqlReaderStoredProcedureName**, du måste ange ett värde för den **tableName** egenskap i datauppsättningen JSON. Det finns inga verifieringar utföras om mot den här tabellen.


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

I det här exemplet **sqlReaderQuery** har angetts för SqlSource. Kopieringsaktiviteten körs den här frågan mot SQL Server-databas källan för att hämta data. Du kan också ange en lagrad procedur genom att ange den **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren har parametrar). SqlReaderQuery kan referera till flera tabeller i databasen som refereras av inkommande dataset. Det är inte begränsad till endast tabellen som den dataset tableName typeProperty.

Om du inte anger sqlReaderQuery eller sqlReaderStoredProcedureName, används de kolumner som anges i strukturen för att skapa en select-frågan ska köras mot SQL Server-databasen. Om datauppsättningsdefinitionen saknar strukturen, markeras alla kolumner från tabellen.

Mer information finns i [SQL Server-anslutningen](data-factory-sqlserver-connector.md#copy-activity-properties) artikel. 

### <a name="sql-sink-in-copy-activity"></a>SQL-mottagare i en Kopieringsaktivitet
Om du kopierar data till en SQL Server-databas, ange den **sink typen** för kopieringsaktiviteten till **SqlSink**, och ange följande egenskaper i den **sink** avsnitt:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| writeBatchTimeout |Vänta tills batch insert-åtgärden ska slutföras innan tidsgränsen uppnås. |TimeSpan<br/><br/> Exempel ”: 00: 30:00” (30 minuter). |Nej |
| writeBatchSize |Infogar data i SQL-tabellen när buffertstorleken når writeBatchSize. |Heltal (antalet rader) |Nej (standard: 10000) |
| sqlWriterCleanupScript |Ange frågan för Kopieringsaktiviteten att köra så att data för ett visst segment har rensats bort. Mer information finns i [repeterbarhet](#repeatability-during-copy) avsnitt. |En frågesats. |Nej |
| sliceIdentifierColumnName |Ange kolumnnamnet för Kopieringsaktiviteten med genereras automatiskt segment-ID, som används för att rensa data för ett visst segment när köras på nytt. Mer information finns i [repeterbarhet](#repeatability-during-copy) avsnitt. |Kolumnnamnet för en kolumn med datatypen för binary(32). |Nej |
| sqlWriterStoredProcedureName |Namnet på den lagrade proceduren upserts (uppdateringar/infogar) data i måltabellen. |Namnet på den lagrade proceduren. |Nej |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värde-par. Namn och skiftläge parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. |Nej |
| sqlWriterTableType |Ange tabellen typnamn som ska användas i den lagrade proceduren. Kopieringsaktiviteten tillhandahåller data flyttas i en temporär tabell med den här tabellen. Lagrade procedurer kan sedan koppla data kopieras med befintliga data. |Ett namn för tabellen. |Nej |

#### <a name="example"></a>Exempel
Pipelinen innehåller en kopia-aktivitet som är konfigurerad för att använda dessa indata och utdata-datauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen av **källa** är inställd på **BlobSource** och **sink** är inställd på **SqlSink**.

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

Mer information finns i [SQL Server-anslutningen](data-factory-sqlserver-connector.md#copy-activity-properties) artikel. 

## <a name="sybase"></a>Sybase

### <a name="linked-service"></a>Länkad tjänst
Definiera en Sybase länkade tjänsten genom att ange den **typen** på den länkade tjänsten till **OnPremisesSybase**, och ange följande egenskaper i den **typeProperties** avsnitt:  

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| server |Namnet på Sybase-servern. |Ja |
| databas |Namnet på Sybase-databasen. |Ja |
| schemat |Namnet på schemat i databasen. |Nej |
| authenticationType |Typ av autentisering som används för att ansluta till Sybase-databasen. Möjliga värden är: anonym, grundläggande och Windows. |Ja |
| användarnamn |Ange användarnamnet om du använder grundläggande eller Windows-autentisering. |Nej |
| lösenord |Ange lösenordet för det användarkonto som du angav för användarnamnet. |Nej |
| gatewayName |Namnet på den gateway som Data Factory-tjänsten ska använda för att ansluta till den lokala Sybase-databasen. |Ja |

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

Mer information finns i [Sybase connector](data-factory-onprem-sybase-connector.md#linked-service-properties) artikel. 

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en Sybase-dataset, ange den **typen** på datamängden som ska **RelationalTable**, och ange följande egenskaper i den **typeProperties** avsnitt: 

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i Sybase-databasinstansen som den länkade tjänsten refererar till. |Nej (om **frågan** av **RelationalSource** har angetts) |

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

Mer information finns i [Sybase connector](data-factory-onprem-sybase-connector.md#dataset-properties) artikel. 

### <a name="relational-source-in-copy-activity"></a>Relationella källa i en Kopieringsaktivitet
Om du vill kopiera data från en Sybase-databas, ange den **typ av datakälla** för kopieringsaktiviteten till **RelationalSource**, och ange följande egenskaper i den **källa** avsnitt :


| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd anpassad fråga för att läsa data. |SQL-sträng. Till exempel: `select * from MyTable`. |Nej (om **tableName** av **dataset** har angetts) |

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

Mer information finns i [Sybase connector](data-factory-onprem-sybase-connector.md#copy-activity-properties) artikel.

## <a name="teradata"></a>Teradata

### <a name="linked-service"></a>Länkad tjänst
Definiera en Teradata länkade tjänsten genom att ange den **typ** på den länkade tjänsten till **OnPremisesTeradata**, och ange följande egenskaper i den **typeProperties** avsnitt:  

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| server |Namnet på Teradata-server. |Ja |
| authenticationType |Typ av autentisering som används för att ansluta till Teradata-databasen. Möjliga värden är: anonym, grundläggande och Windows. |Ja |
| användarnamn |Ange användarnamnet om du använder grundläggande eller Windows-autentisering. |Nej |
| lösenord |Ange lösenordet för det användarkonto som du angav för användarnamnet. |Nej |
| gatewayName |Namnet på den gateway som Data Factory-tjänsten ska använda för att ansluta till den lokala Teradata-databasen. |Ja |

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

Mer information finns i [Teradata connector](data-factory-onprem-teradata-connector.md#linked-service-properties) artikel.

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en Teradata-blobbdatauppsättning, ange den **typen** på datamängden som ska **RelationalTable**. Det finns för närvarande inga egenskaper som stöds för Teradata-datauppsättningen. 

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

Mer information finns i [Teradata connector](data-factory-onprem-teradata-connector.md#dataset-properties) artikel.

### <a name="relational-source-in-copy-activity"></a>Relationella källa i en Kopieringsaktivitet
Om du vill kopiera data från en Teradata-databas, ange den **typ av datakälla** för kopieringsaktiviteten till **RelationalSource**, och ange följande egenskaper i den **källa** avsnitt:

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

Mer information finns i [Teradata connector](data-factory-onprem-teradata-connector.md#copy-activity-properties) artikel.

## <a name="cassandra"></a>Cassandra


### <a name="linked-service"></a>Länkad tjänst
Om du vill definiera en Cassandra länkad tjänst, ange den **typen** på den länkade tjänsten till **OnPremisesCassandra**, och ange följande egenskaper i den **typeProperties** avsnitt:  

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| värd |En eller flera IP-adresser eller värdnamn Cassandra servrar.<br/><br/>Ange en kommaavgränsad lista med IP-adresser eller värdnamn för att ansluta till alla servrar samtidigt. |Ja |
| port |TCP-porten som används av Cassandra-server för att lyssna efter anslutningar. |Nej, standardvärde: 9042 |
| authenticationType |Grundläggande eller anonym |Ja |
| användarnamn |Ange användarnamnet för användarkontot. |Ja, om authenticationType anges till Basic. |
| lösenord |Ange lösenordet för användarkontot. |Ja, om authenticationType anges till Basic. |
| gatewayName |Namnet på den gateway som används för att ansluta till lokalt Cassandra-databasen. |Ja |
| encryptedCredential |Autentiseringsuppgifter har krypterats med gatewayen. |Nej |

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

Mer information finns i [Cassandra connector](data-factory-onprem-cassandra-connector.md#linked-service-properties) artikel. 

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en Cassandra dataset, ange den **typen** på datamängden som ska **CassandraTable**, och ange följande egenskaper i den **typeProperties** avsnitt: 

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| keyspace |Namnet på keyspace eller schema i Cassandra databasen. |Ja (om **frågan** för **CassandraSource** har inte definierats). |
| tableName |Namnet på tabellen i Cassandra databas. |Ja (om **frågan** för **CassandraSource** har inte definierats). |

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

Mer information finns i [Cassandra connector](data-factory-onprem-cassandra-connector.md#dataset-properties) artikel. 

### <a name="cassandra-source-in-copy-activity"></a>Cassandra källa i en Kopieringsaktivitet
Om du vill kopiera data från Cassandra, ange den **typ av datakälla** för kopieringsaktiviteten till **CassandraSource**, och ange följande egenskaper i den **källa** avsnitt:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd anpassad fråga för att läsa data. |SQL-92 frågan eller CQL frågan. Se [CQL referens](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>När du använder SQL-frågan anger **keyspace name.table namn** som representerar den tabell som du vill fråga. |Nej (om tabellnamn och keyspace för datauppsättningen har definierats). |
| consistencyLevel |Nivå för konsekvenskontroll anger hur många repliker måste svara på en läsbegäran innan den returnerar data till klientprogrammet. Cassandra kontrollerar det angivna antalet repliker för data att uppfylla read-förfrågan. |EN, TVÅ, TRE, KVORUM, ALL, LOCAL_QUORUM EACH_QUORUM, LOCAL_ONE. Se [konfigurera datakonsekvens](http://docs.datastax.com/en//cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) mer information. |Nej. Standardvärdet är en. |

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

Mer information finns i [Cassandra connector](data-factory-onprem-cassandra-connector.md#copy-activity-properties) artikel.

## <a name="mongodb"></a>MongoDB

### <a name="linked-service"></a>Länkad tjänst
Definiera en MongoDB länkade tjänsten genom att ange den **typ** på den länkade tjänsten till **OnPremisesMongoDB**, och ange följande egenskaper i den **typeProperties** avsnitt:  

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| server |IP-adressen eller värdnamnet namnet på MongoDB-servern. |Ja |
| port |TCP-port som MongoDB-servern använder för att lyssna efter anslutningar. |Valfritt, standardvärdet: 27017 |
| authenticationType |Grundläggande eller anonym. |Ja |
| användarnamn |Användarkonto för att få åtkomst till MongoDB. |Ja (om grundläggande autentisering används). |
| lösenord |Lösenord för användaren. |Ja (om grundläggande autentisering används). |
| authSource |Namnet på MongoDB-databas som du vill använda för att kontrollera autentiseringsuppgifterna för autentisering. |Valfritt (om grundläggande autentisering används). standard: använder administratörskonto och databasen som anges med egenskapen databaseName. |
| databaseName |Namnet på den MongoDB-databas som du vill komma åt. |Ja |
| gatewayName |Namnet på den gateway som har åtkomst till datalagret. |Ja |
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

Mer information finns i [MongoDB connector artikel](data-factory-on-premises-mongodb-connector.md#linked-service-properties)

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en datauppsättning för MongoDB, ange den **typen** på datamängden som ska **MongoDbCollection**, och ange följande egenskaper i den **typeProperties** avsnitt: 

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

Mer information finns i [MongoDB connector artikel](data-factory-on-premises-mongodb-connector.md#dataset-properties)

#### <a name="mongodb-source-in-copy-activity"></a>MongoDB-källan i en Kopieringsaktivitet
Om du vill kopiera data från MongoDB, ange den **typ av datakälla** för kopieringsaktiviteten till **MongoDbSource**, och ange följande egenskaper i den **källa** avsnitt:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd anpassad fråga för att läsa data. |SQL-92 frågesträngen. Till exempel: `select * from MyTable`. |Nej (om **samlingsnamn** av **dataset** har angetts) |

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

Mer information finns i [MongoDB connector artikel](data-factory-on-premises-mongodb-connector.md#copy-activity-properties)

## <a name="amazon-s3"></a>Amazon S3


### <a name="linked-service"></a>Länkad tjänst
Definiera en Amazon S3 länkade tjänsten genom att ange den **typen** på den länkade tjänsten till **AwsAccessKey**, och ange följande egenskaper i den **typeProperties** avsnitt:  

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| accessKeyID |ID för den hemliga åtkomstnyckeln. |sträng |Ja |
| secretAccessKey |Hemlig själva åtkomstnyckeln. |Krypterad hemliga sträng |Ja |

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

Mer information finns i [Amazon S3 connector artikel](data-factory-amazon-simple-storage-service-connector.md#linked-service-properties).

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en datauppsättning för Amazon S3, ange den **typen** på datamängden som ska **AmazonS3**, och ange följande egenskaper i den **typeProperties** avsnitt: 

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| bucketName |S3-Bucketnamn. |Sträng |Ja |
| key |S3 objekt nyckeln. |Sträng |Nej |
| prefix |Prefix för nyckeln S3 objekt. Objekt vars nycklar som börjar med prefixet är markerade. Gäller endast när nyckeln är tom. |Sträng |Nej |
| version |Versionen av S3 objekt om S3 versionshantering är aktiverat. |Sträng |Nej |
| Format | Följande format stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Ange den **typen** egenskap under format till ett av dessa värden. Mer information finns i [textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-formatet](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), och [parkettgolv Format](data-factory-supported-file-and-compression-formats.md#parquet-format) avsnitt. <br><br> Om du vill **kopiera filer som-är** mellan filbaserade butiker (binär kopia), hoppa över avsnittet format i både inkommande och utgående dataset-definitioner. |Nej | |
| komprimering | Ange typ och kompression för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**. Nivåerna som stöds är: **Optimal** och **snabbast**. Mer information finns i [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nej | |


> [!NOTE]
> Anger platsen för S3 objekt där bucket är Rotbehållare för S3 objekt och nyckeln är den fullständiga sökvägen till S3 objekt bucketName + nyckel.

#### <a name="example-sample-dataset-with-prefix"></a>Exempel: Exempeldatamängd med prefix

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
#### <a name="example-sample-data-set-with-version"></a>Exempel: Exempel datauppsättning (med version)

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

#### <a name="example-dynamic-paths-for-s3"></a>Exempel: Dynamiska sökvägar för S3
I det här exemplet använder vi fasta värden för nyckeln och bucketName egenskaper i Amazon S3 datamängden.

```json
"key": "testFolder/test.orc",
"bucketName": "<S3 bucket name>",
```

Du kan ha Data Factory beräkna nyckel och bucketName dynamiskt vid körning med systemvariabler som SliceStart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

Du kan göra samma för egenskapen prefix för en Amazon S3 datauppsättning. Se [Data Factory-funktioner och systemvariabler](data-factory-functions-variables.md) en lista över funktioner som stöds och variabler.

Mer information finns i [Amazon S3 connector artikel](data-factory-amazon-simple-storage-service-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Källa för System i en Kopieringsaktivitet
Om du vill kopiera data från Amazon S3, ange den **typ av datakälla** för kopieringsaktiviteten till **FileSystemSource**, och ange följande egenskaper i den **källa** avsnitt:


| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| rekursiva |Anger om att rekursivt listan S3 objekt i katalogen. |SANT/FALSKT |Nej |


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

Mer information finns i [Amazon S3 connector artikel](data-factory-amazon-simple-storage-service-connector.md#copy-activity-properties).

## <a name="file-system"></a>Filsystem


### <a name="linked-service"></a>Länkad tjänst
Du kan länka ett lokalt filsystem till ett Azure data factory med den **lokala filserver** länkade tjänsten. Följande tabell innehåller beskrivningar för JSON-element som är specifika för lokala filserver länkade tjänsten.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Se till att egenskapen type har angetts **OnPremisesFileServer**. |Ja |
| värd |Anger rotsökvägen för den mapp som du vill kopiera. Använda escape-tecknet ' \ ' för specialtecken i strängen. Se [exempel länkad tjänst-och dataset](#sample-linked-service-and-dataset-definitions) exempel. |Ja |
| användar-ID |Ange ID för den användare som har åtkomst till servern. |Nej (om du väljer encryptedCredential) |
| lösenord |Ange lösenord för användaren (användar-ID). |Nej (om du väljer encryptedCredential |
| encryptedCredential |Ange de kryptera autentiseringsuppgifter som du kan få genom att köra cmdlet New-AzureRmDataFactoryEncryptValue. |Nej (om du väljer att ange användarnamn och lösenord i klartext) |
| gatewayName |Anger namnet på den gateway som Data Factory ska använda för att ansluta till den lokala filservern. |Ja |

#### <a name="sample-folder-path-definitions"></a>Exempel mappen sökväg definitioner 
| Scenario | Värden i länkade tjänstdefinitionen | folderPath i datauppsättningsdefinitionen |
| --- | --- | --- |
| Lokal mapp på Data Management Gateway-datorn: <br/><br/>Exempel: D:\\ \* eller D:\folder\subfolder\\* |D:\\ \\ (för Data Management Gateway 2.0 och senare) <br/><br/> localhost (för tidigare versioner än Data Management Gateway 2.0) |. \\ \\ eller mappen\\\\undermapp (för Data Management Gateway 2.0 och senare) <br/><br/>D:\\ \\ eller D:\\\\mappen\\\\undermapp (för gateway-versionen nedan 2.0) |
| Delad fjärrmapp: <br/><br/>Exempel: \\ \\minserver\\dela\\ \* eller \\ \\minserver\\dela\\mappen\\undermapp\\* |\\\\\\\\minserver\\\\dela |. \\ \\ eller mappen\\\\undermapp |


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

#### <a name="example-using-encryptedcredential"></a>Exempel: Använda encryptedcredential

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

Mer information finns i [filsystemet connector artikel](data-factory-onprem-file-system-connector.md#linked-service-properties).

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en datauppsättning för filsystem, ange den **typen** på datamängden som ska **filresursen**, och ange följande egenskaper i den **typeProperties** avsnitt: 

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Anger undersökvägen till mappen. Använda escape-tecknet ' \' för specialtecken i strängen. Se [exempel länkad tjänst-och dataset](#sample-linked-service-and-dataset-definitions) exempel.<br/><br/>Du kan kombinera den här egenskapen med **partitionBy** ha mappen sökvägar baserat på sektorn börja/sluta datum gånger. |Ja |
| fileName |Ange namnet på filen i den **folderPath** om du vill att referera till en viss fil i mappen. Om du inte anger något värde för den här egenskapen tabellen pekar på alla filer i mappen.<br/><br/>Om filnamnet inte anges för en datamängd för utdata är namnet på filen skapas i följande format: <br/><br/>`Data.<Guid>.txt` (Exempel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Nej |
| fileFilter |Ange ett filter som används för att välja en delmängd av filer i mappsökvägen i stället för alla filer. <br/><br/>Tillåtna värden är: `*` (flera tecken) och `?` (valfritt tecken).<br/><br/>Exempel 1: ”fileFilter” ”: * .log”<br/>Exempel 2: ”fileFilter”: 2016 - 1-?. txt ”<br/><br/>Observera att fileFilter gäller för en inkommande filresursen datauppsättning. |Nej |
| partitionedBy |Du kan använda partitionedBy för att ange en dynamisk folderPath/filnamn för time series-data. Ett exempel är folderPath parametriserade varje timme av data. |Nej |
| Format | Följande format stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Ange den **typen** egenskap under format till ett av dessa värden. Mer information finns i [textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-formatet](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), och [parkettgolv Format](data-factory-supported-file-and-compression-formats.md#parquet-format) avsnitt. <br><br> Om du vill **kopiera filer som-är** mellan filbaserade butiker (binär kopia), hoppa över avsnittet format i både inkommande och utgående dataset-definitioner. |Nej |
| komprimering | Ange typ och kompression för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**; och nivåer som stöds är: **Optimal** och **snabbaste**. Se [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nej |

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

Mer information finns i [filsystemet connector artikel](data-factory-onprem-file-system-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Källa för System i en Kopieringsaktivitet
Om du kopierar data från filsystemet, ange den **typ av datakälla** för kopieringsaktiviteten till **FileSystemSource**, och ange följande egenskaper i den **källa** avsnitt:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| rekursiva |Anger om data läses rekursivt från undermapparna eller endast från den angivna mappen. |SANT, FALSKT (standard) |Nej |

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
Mer information finns i [filsystemet connector artikel](data-factory-onprem-file-system-connector.md#copy-activity-properties).

### <a name="file-system-sink-in-copy-activity"></a>Filsystem mottagare i en Kopieringsaktivitet
Om du kopierar data till filsystemet, ange den **sink typen** för kopieringsaktiviteten till **FileSystemSink**, och ange följande egenskaper i den **sink** avsnitt:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| copyBehavior |Definierar beteendet kopia när källan är BlobSource eller filsystem. |**PreserveHierarchy:** bevarar fil hierarkin i målmappen. Den relativa sökvägen för källfilen för målmappen är samma som den relativa sökvägen för filen till målmappen.<br/><br/>**FlattenHierarchy:** alla filer från källmappen skapas i den första nivån i målmappen. Mål-filer som skapas med ett namn som genererats automatiskt.<br/><br/>**MergeFiles:** sammanfogar alla filer från källmappen till en fil. Om name-blob filnamn har angetts är kopplade filnamnet det angivna namnet. Annars är ett automatiskt genererat namn. |Nej |
Auto-

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

Mer information finns i [filsystemet connector artikel](data-factory-onprem-file-system-connector.md#copy-activity-properties).

## <a name="ftp"></a>FTP

### <a name="linked-service"></a>Länkad tjänst
Definiera en FTP länkade tjänsten genom att ange den **typen** på den länkade tjänsten till **FtpServer**, och ange följande egenskaper i den **typeProperties** avsnitt:  

| Egenskap  | Beskrivning | Krävs | Standard |
| --- | --- | --- | --- |
| värd |Namn eller IP-adressen till FTP-servern |Ja |&nbsp; |
| authenticationType |Ange autentiseringstyp |Ja |Grundläggande, anonyma |
| användarnamn |Användare som har åtkomst till FTP-servern |Nej |&nbsp; |
| lösenord |Lösenord för användare (användarnamn) |Nej |&nbsp; |
| encryptedCredential |Krypterade autentiseringsuppgifter för åtkomst till FTP-servern |Nej |&nbsp; |
| gatewayName |Namnet på Data Management Gateway för att ansluta till en lokal FTP-server |Nej |&nbsp; |
| port |Port som FTP-servern lyssnar på. |Nej |21 |
| enableSsl |Ange om du använder FTP över SSL/TLS-kanalen |Nej |true |
| enableServerCertificateValidation |Ange om du vill aktivera server SSL-certifikatsverifiering när du använder FTP över SSL/TLS-kanalen |Nej |true |

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

Mer information finns i [FTP-anslutningen](data-factory-ftp-connector.md#linked-service-properties) artikel.

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en datauppsättning med FTP, ange den **typen** på datamängden som ska **filresursen**, och ange följande egenskaper i den **typeProperties** avsnitt: 

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Sub sökvägen till mappen. Använda escape-tecknet ' \ ' för specialtecken i strängen. Se [exempel länkad tjänst-och dataset](#sample-linked-service-and-dataset-definitions) exempel.<br/><br/>Du kan kombinera den här egenskapen med **partitionBy** ha mappen sökvägar baserat på sektorn börja/sluta datum gånger. |Ja 
| fileName |Ange namnet på filen i den **folderPath** om du vill att referera till en viss fil i mappen. Om du inte anger något värde för den här egenskapen tabellen pekar på alla filer i mappen.<br/><br/>Om filnamnet inte anges för en datamängd för utdata är namnet på den genererade filen i följande det här formatet: <br/><br/>Data. <Guid>.txt (exempel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nej |
| fileFilter |Ange ett filter som används för att välja en delmängd av filer i mappsökvägen i stället för alla filer.<br/><br/>Tillåtna värden är: `*` (flera tecken) och `?` (valfritt tecken).<br/><br/>Exempel 1: `"fileFilter": "*.log"`<br/>Exempel 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter gäller för en inkommande filresursen datauppsättning. Den här egenskapen stöds inte med HDFS. |Nej |
| partitionedBy |partitionedBy kan användas för att ange en dynamisk folderPath filnamn för tid series-data. Till exempel folderPath som innehåller parametrar för varje timme av data. |Nej |
| Format | Följande format stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Ange den **typen** egenskap under format till ett av dessa värden. Mer information finns i [textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-formatet](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), och [parkettgolv Format](data-factory-supported-file-and-compression-formats.md#parquet-format) avsnitt. <br><br> Om du vill **kopiera filer som-är** mellan filbaserade butiker (binär kopia), hoppa över avsnittet format i både inkommande och utgående dataset-definitioner. |Nej |
| komprimering | Ange typ och kompression för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**; och nivåer som stöds är: **Optimal** och **snabbaste**. Mer information finns i [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nej |
| useBinaryTransfer |Ange om använder binära överföringsläge. True för en binär och FALSKT ASCII. Standardvärde: True. Den här egenskapen kan endast användas när associerade linked service-typen är av typen: FtpServer. |Nej |

> [!NOTE]
> filnamnet och fileFilter kan inte användas samtidigt.

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

Mer information finns i [FTP-anslutningen](data-factory-ftp-connector.md#dataset-properties) artikel.

### <a name="file-system-source-in-copy-activity"></a>Källa för System i en Kopieringsaktivitet
Om du vill kopiera data från en FTP-server, ange den **typ av datakälla** för kopieringsaktiviteten till **FileSystemSource**, och ange följande egenskaper i den **källa** avsnitt:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| rekursiva |Anger om data läses rekursivt från undermappar eller endast från den angivna mappen. |SANT, FALSKT (standard) |Nej |

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

Mer information finns i [FTP-anslutningen](data-factory-ftp-connector.md#copy-activity-properties) artikel.


## <a name="hdfs"></a>HDFS

### <a name="linked-service"></a>Länkad tjänst
Definiera en HDFS länkade tjänsten genom att ange den **typ** på den länkade tjänsten till **Hdfs**, och ange följande egenskaper i den **typeProperties** avsnitt:  

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen type måste anges till: **Hdfs** |Ja |
| URL |URL till HDFS |Ja |
| authenticationType |Anonym, eller Windows. <br><br> Att använda **Kerberos-autentisering** HDFS-anslutningen finns i [i det här avsnittet](#use-kerberos-authentication-for-hdfs-connector) därefter konfigurera din lokala miljö. |Ja |
| Användarnamn |Användarnamn för Windows-autentisering. |Ja (för Windows-autentisering) |
| lösenord |Lösenordet för Windows-autentisering. |Ja (för Windows-autentisering) |
| gatewayName |Namnet på den gateway som Data Factory-tjänsten ska använda för att ansluta till HDFS. |Ja |
| encryptedCredential |[Nya AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) utdata för autentiseringsuppgifterna för åtkomst. |Nej |

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

#### <a name="example-using-windows-authentication"></a>Exempel: Med Windows-autentisering

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

Mer information finns i [HDFS connector](#data-factory-hdfs-connector.md#linked-service-properties) artikel. 

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en datauppsättning för HDFS, ange den **typen** på datamängden som ska **filresursen**, och ange följande egenskaper i den **typeProperties** avsnitt: 

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Sökvägen till mappen. Exempel: `myfolder`<br/><br/>Använda escape-tecknet ' \ ' för specialtecken i strängen. Till exempel: Ange mapp för folder\subfolder,\\\\undermapp och ange d: för d:\samplefolder,\\\\Exempelmapp.<br/><br/>Du kan kombinera den här egenskapen med **partitionBy** ha mappen sökvägar baserat på sektorn börja/sluta datum gånger. |Ja |
| fileName |Ange namnet på filen i den **folderPath** om du vill att referera till en viss fil i mappen. Om du inte anger något värde för den här egenskapen tabellen pekar på alla filer i mappen.<br/><br/>Om filnamnet inte anges för en datamängd för utdata är namnet på den genererade filen i följande det här formatet: <br/><br/>Data. <Guid>.txt (till exempel:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nej |
| partitionedBy |partitionedBy kan användas för att ange en dynamisk folderPath filnamn för tid series-data. Exempel: folderPath parametriserade varje timme av data. |Nej |
| Format | Följande format stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Ange den **typen** egenskap under format till ett av dessa värden. Mer information finns i [textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-formatet](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), och [parkettgolv Format](data-factory-supported-file-and-compression-formats.md#parquet-format) avsnitt. <br><br> Om du vill **kopiera filer som-är** mellan filbaserade butiker (binär kopia), hoppa över avsnittet format i både inkommande och utgående dataset-definitioner. |Nej |
| komprimering | Ange typ och kompression för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**. Nivåer som stöds är: **Optimal** och **snabbast**. Mer information finns i [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nej |

> [!NOTE]
> filnamnet och fileFilter kan inte användas samtidigt.

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

Mer information finns i [HDFS connector](#data-factory-hdfs-connector.md#dataset-properties) artikel. 

### <a name="file-system-source-in-copy-activity"></a>Källa för System i en Kopieringsaktivitet
Om du vill kopiera data från HDFS, ange den **typ av datakälla** för kopieringsaktiviteten till **FileSystemSource**, och ange följande egenskaper i den **källa** avsnitt:

**FileSystemSource** stöder följande egenskaper:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| rekursiva |Anger om data läses rekursivt från undermappar eller endast från den angivna mappen. |SANT, FALSKT (standard) |Nej |

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

Mer information finns i [HDFS connector](#data-factory-hdfs-connector.md#copy-activity-properties) artikel.

## <a name="sftp"></a>SFTP


### <a name="linked-service"></a>Länkad tjänst
Definiera en SFTP länkade tjänsten genom att ange den **typen** på den länkade tjänsten till **Sftp**, och ange följande egenskaper i den **typeProperties** avsnitt:  

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- | --- |
| värd | Namn eller IP-adress till SFTP-server. |Ja |
| port |Port som avlyssnas SFTP-servern. Standardvärdet är: 21 |Nej |
| authenticationType |Ange autentiseringstypen. Tillåtna värden: **grundläggande**, **SshPublicKey**. <br><br> Referera till [använder grundläggande autentisering](#using-basic-authentication) och [med hjälp av SSH autentisering med offentlig nyckel](#using-ssh-public-key-authentication) respektive avsnitt på fler egenskaper och JSON-exempel. |Ja |
| skipHostKeyValidation | Ange om du vill hoppa över värden viktiga validering. | Nej. Standardvärde: false |
| hostKeyFingerprint | Ange fingeravtryck för värdnyckeln. | Ja om den `skipHostKeyValidation` är inställd på false.  |
| gatewayName |Namnet på Data Management Gateway för att ansluta till en lokal SFTP-server. | Ja om du kopierar data från en lokal SFTP-server. |
| encryptedCredential | Krypterade autentiseringsuppgifter för åtkomst till SFTP-servern. Genereras automatiskt när du anger grundläggande autentisering (användarnamn och lösenord) eller SshPublicKey autentisering (användarnamn + privat sökväg eller innehåll) i guiden Kopiera eller ClickOnce popup-dialogruta. | Nej. Gäller bara när du kopierar data från en lokal SFTP-server. |

#### <a name="example-using-basic-authentication"></a>Exempel: Använder grundläggande autentisering

Om du vill använda grundläggande autentisering, `authenticationType` som `Basic`, och ange följande egenskaper förutom SFTP kopplingen generiska som introducerades i det sista avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- | --- |
| användarnamn | Användare som har åtkomst till SFTP-servern. |Ja |
| lösenord | Lösenord för användare (användarnamn). | Ja |

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

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Exempel: **grundläggande autentisering med krypterade autentiseringsuppgifter**

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

#### <a name="using-ssh-public-key-authentication"></a>**Använda autentisering med SSH offentlig nyckel:**

Om du vill använda grundläggande autentisering, `authenticationType` som `SshPublicKey`, och ange följande egenskaper förutom SFTP kopplingen generiska som introducerades i det sista avsnittet:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- | --- |
| användarnamn |Användare som har åtkomst till SFTP-server |Ja |
| privateKeyPath | Ange absolut sökväg till filen för privat nyckel som gateway kan komma åt. | Ange antingen det `privateKeyPath` eller `privateKeyContent`. <br><br> Gäller bara när du kopierar data från en lokal SFTP-server. |
| privateKeyContent | En serialiserad sträng av privat nyckel innehållet. Guiden Kopiera kan läsa filen för privat nyckel och extrahera privata nyckel innehållet automatiskt. Om du använder någon annan verktyget/SDK, använder du egenskapen privateKeyPath. | Ange antingen det `privateKeyPath` eller `privateKeyContent`. |
| Lösenfrasen | Ange pass frasen/lösenord för att dekryptera den privata nyckeln om nyckelfilen skyddas av ett lösenord. | Ja om filen för privata nyckeln skyddas av ett lösenord. |

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

Mer information finns i [SFTP connector](data-factory-sftp-connector.md#linked-service-properties) artikel. 

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en SFTP datauppsättning, ange den **typen** på datamängden som ska **filresursen**, och ange följande egenskaper i den **typeProperties** avsnitt: 

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| folderPath |Sub sökvägen till mappen. Använda escape-tecknet ' \ ' för specialtecken i strängen. Se [exempel länkad tjänst-och dataset](#sample-linked-service-and-dataset-definitions) exempel.<br/><br/>Du kan kombinera den här egenskapen med **partitionBy** ha mappen sökvägar baserat på sektorn börja/sluta datum gånger. |Ja |
| fileName |Ange namnet på filen i den **folderPath** om du vill att referera till en viss fil i mappen. Om du inte anger något värde för den här egenskapen tabellen pekar på alla filer i mappen.<br/><br/>Om filnamnet inte anges för en datamängd för utdata är namnet på den genererade filen i följande det här formatet: <br/><br/>Data. <Guid>.txt (exempel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Nej |
| fileFilter |Ange ett filter som används för att välja en delmängd av filer i mappsökvägen i stället för alla filer.<br/><br/>Tillåtna värden är: `*` (flera tecken) och `?` (valfritt tecken).<br/><br/>Exempel 1: `"fileFilter": "*.log"`<br/>Exempel 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter gäller för en inkommande filresursen datauppsättning. Den här egenskapen stöds inte med HDFS. |Nej |
| partitionedBy |partitionedBy kan användas för att ange en dynamisk folderPath filnamn för tid series-data. Till exempel folderPath som innehåller parametrar för varje timme av data. |Nej |
| Format | Följande format stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**,  **ParquetFormat**. Ange den **typen** egenskap under format till ett av dessa värden. Mer information finns i [textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-formatet](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), och [parkettgolv Format](data-factory-supported-file-and-compression-formats.md#parquet-format) avsnitt. <br><br> Om du vill **kopiera filer som-är** mellan filbaserade butiker (binär kopia), hoppa över avsnittet format i både inkommande och utgående dataset-definitioner. |Nej |
| komprimering | Ange typ och kompression för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**. Nivåer som stöds är: **Optimal** och **snabbast**. Mer information finns i [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nej |
| useBinaryTransfer |Ange om använder binära överföringsläge. True för en binär och FALSKT ASCII. Standardvärde: True. Den här egenskapen kan endast användas när associerade linked service-typen är av typen: FtpServer. |Nej |

> [!NOTE]
> filnamnet och fileFilter kan inte användas samtidigt.

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

Mer information finns i [SFTP connector](data-factory-sftp-connector.md#dataset-properties) artikel. 

### <a name="file-system-source-in-copy-activity"></a>Källa för System i en Kopieringsaktivitet
Om du kopierar data från en källa för SFTP, ange den **typ av datakälla** för kopieringsaktiviteten till **FileSystemSource**, och ange följande egenskaper i den **källa** avsnitt:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| rekursiva |Anger om data läses rekursivt från undermappar eller endast från den angivna mappen. |SANT, FALSKT (standard) |Nej |



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

Mer information finns i [SFTP connector](data-factory-sftp-connector.md#copy-activity-properties) artikel.


## <a name="http"></a>HTTP

### <a name="linked-service"></a>Länkad tjänst
Definiera en HTTP länkade tjänsten genom att ange den **typen** på den länkade tjänsten till **Http**, och ange följande egenskaper i den **typeProperties** avsnitt:  

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| url | Bas-URL till webbservern | Ja |
| authenticationType | Anger vilken autentiseringstyp. Tillåtna värden är: **anonym**, **grundläggande**, **sammanfattad**, **Windows**, **ClientCertificate**. <br><br> Avse respektive avsnitt under den här tabellen på fler egenskaper och JSON-exempel för dessa typer av autentisering. | Ja |
| enableServerCertificateValidation | Ange om du vill aktivera server SSL-certifikatsverifiering om datakällan är HTTPS-webbserver | Nej, standard är SANT |
| gatewayName | Namnet på Data Management Gateway för att ansluta till en lokal http-källa. | Ja om du kopierar data från en lokal http-källa. |
| encryptedCredential | Krypterade autentiseringsuppgifter till HTTP-slutpunkten. Genereras automatiskt när du konfigurerar autentiseringsinformation i guiden Kopiera eller ClickOnce popup-dialogruta. | Nej. Gäller bara när du kopierar data från en lokal HTTP-server. |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Exempel: Med grundläggande, sammanfattad eller Windows-autentisering
Ange `authenticationType` som `Basic`, `Digest`, eller `Windows`, och ange följande egenskaper utöver HTTP-anslutningen generiska de introduceras ovan:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| användarnamn | Användarnamn för att få åtkomst till HTTP-slutpunkten. | Ja |
| lösenord | Lösenord för användare (användarnamn). | Ja |

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

#### <a name="example-using-clientcertificate-authentication"></a>Exempel: Med ClientCertificate autentisering

Om du vill använda grundläggande autentisering, `authenticationType` som `ClientCertificate`, och ange följande egenskaper utöver HTTP-anslutningen generiska de introduceras ovan:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| embeddedCertData | Base64-kodade innehåll binära data i filen Personal Information Exchange (PFX). | Ange antingen det `embeddedCertData` eller `certThumbprint`. |
| certThumbprint | Tumavtryck för certifikat som har installerats på gateway-datorns certifikatarkiv. Gäller bara när du kopierar data från en lokal http-källa. | Ange antingen det `embeddedCertData` eller `certThumbprint`. |
| lösenord | Lösenordet för certifikatet. | Nej |

Om du använder `certThumbprint` för autentisering och certifikatet har installerats i det personliga arkivet i den lokala datorn, måste du bevilja läsbehörighet till gatewaytjänsten:

1. Starta Microsoft Management Console (MMC). Lägg till den **certifikat** snapin-modulen som riktar sig den **lokal dator**.
2. Expandera **certifikat**, **personliga**, och klicka på **certifikat**.
3. Högerklicka på certifikatet från det personliga arkivet och välj **alla aktiviteter**->**hantera privata nycklar...**
3. På den **säkerhet** Lägg till användarkontot under vilken Data Management Gateway-värdtjänsten körs med läsbehörighet till certifikatet.  

**Exempel: använder klientcertifikat:** detta länkade tjänsten länkar din data factory till en lokal webbserver för HTTP. Den använder ett klientcertifikat som är installerade på datorn med Data Management Gateway är installerad.

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
Det här länkade tjänsten länkar din data factory på en lokal webbserver för HTTP. Det använder en klient-certifikatfil på datorn med Data Management Gateway är installerad.

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

Mer information finns i [HTTP-anslutningen](data-factory-http-connector.md#linked-service-properties) artikel.

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en HTTP-dataset, ange den **typen** på datamängden som ska **Http**, och ange följande egenskaper i den **typeProperties** avsnitt: 

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| relativeUrl | En relativ URL till den resurs som innehåller data. Om sökvägen inte anges används den URL som angavs i definitionen länkade tjänsten. <br><br> Du kan använda för att skapa dynamiska URL [Data Factory-funktioner och systemvariabler](data-factory-functions-variables.md), exempel: `"relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)"`. | Nej |
| requestMethod | HTTP-metod. Tillåtna värden är **hämta** eller **efter**. | Nej. Standardvärdet är `GET`. |
| additionalHeaders | Ytterligare HTTP-begärans sidhuvud. | Nej |
| requestBody | Brödtext för HTTP-begäran. | Nej |
| Format | Om du vill bara **hämta data från HTTP-slutpunkt som-är** hoppa över den här formatinställningar utan parsning den. <br><br> Om du vill att parsa innehållet i HTTP-svar vid kopiering, stöds följande format: **TextFormat**, **JsonFormat**, **AvroFormat**,  **OrcFormat**, **ParquetFormat**. Mer information finns i [textformat](data-factory-supported-file-and-compression-formats.md#text-format), [Json-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-formatet](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc Format](data-factory-supported-file-and-compression-formats.md#orc-format), och [parkettgolv Format](data-factory-supported-file-and-compression-formats.md#parquet-format) avsnitt. |Nej |
| komprimering | Ange typ och kompression för data. Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**. Nivåer som stöds är: **Optimal** och **snabbast**. Mer information finns i [format och komprimering i Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nej |

#### <a name="example-using-the-get-default-method"></a>Exempel: med metoden GET (standard)

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
Mer information finns i [HTTP-anslutningen](data-factory-http-connector.md#dataset-properties) artikel.

### <a name="http-source-in-copy-activity"></a>HTTP-källan i en Kopieringsaktivitet
Om du kopierar data från en HTTP-källa, ange den **typ av datakälla** för kopieringsaktiviteten till **HttpSource**, och ange följande egenskaper i den **källa** avsnitt:

| Egenskap  | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| httpRequestTimeout | Tidsgräns (TimeSpan) för HTTP-begäran att få svar. Tidsgränsen är det inget svar timeout inte att läsa svarsdata. | Nej. Standardvärde: 00:01:40 |


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

Mer information finns i [HTTP-anslutningen](data-factory-http-connector.md#copy-activity-properties) artikel.

## <a name="odata"></a>OData

### <a name="linked-service"></a>Länkad tjänst
Definiera en OData länkade tjänsten genom att ange den **typen** på den länkade tjänsten till **OData**, och ange följande egenskaper i den **typeProperties** avsnitt:  

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| url |URL för OData-tjänsten. |Ja |
| authenticationType |Typ av autentisering som används för att ansluta till OData-källan. <br/><br/> För molnet OData är möjliga värden anonym, grundläggande och OAuth (Observera Azure Data Factory för närvarande endast stöder Azure Active Directory-baserad OAuth). <br/><br/> För lokala OData är möjliga värden anonym, grundläggande och Windows. |Ja |
| användarnamn |Ange användarnamnet om du använder grundläggande autentisering. |Ja (endast om du använder grundläggande autentisering) |
| lösenord |Ange lösenordet för det användarkonto som du angav för användarnamnet. |Ja (endast om du använder grundläggande autentisering) |
| authorizedCredential |Om du använder OAuth, klickar du på **auktorisera** i guiden för Data Factory kopiera eller redigerare och ange dina autentiseringsuppgifter och sedan värdet för den här egenskapen kommer att genereras automatiskt. |Ja (endast om du använder OAuth-autentisering) |
| gatewayName |Namnet på den gateway som Data Factory-tjänsten ska använda för att ansluta till den lokala OData-tjänsten. Ange endast om du vill kopiera data från lokala OData-källan. |Nej |

#### <a name="example---using-basic-authentication"></a>Exempel: använder grundläggande autentisering
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

#### <a name="example---using-anonymous-authentication"></a>Exempel: använder anonym autentisering

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

#### <a name="example---using-windows-authentication-accessing-on-premises-odata-source"></a>Exempel – med hjälp av Windows-autentisering åtkomst till lokala OData-källan

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

#### <a name="example---using-oauth-authentication-accessing-cloud-odata-source"></a>Exempel – med åtkomst till molnet OData-källan OAuth-autentisering
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

Mer information finns i [OData connector](data-factory-odata-connector.md#linked-service-properties) artikel.

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en OData-datauppsättning, ange den **typen** på datamängden som ska **ODataResource**, och ange följande egenskaper i den **typeProperties** avsnitt: 

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

Mer information finns i [OData connector](data-factory-odata-connector.md#dataset-properties) artikel.

### <a name="relational-source-in-copy-activity"></a>Relationella källa i en Kopieringsaktivitet
Om du kopierar data från en OData-källa, ange den **typ av datakälla** för kopieringsaktiviteten till **RelationalSource**, och ange följande egenskaper i den **källa** avsnitt:

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

Mer information finns i [OData connector](data-factory-odata-connector.md#copy-activity-properties) artikel.


## <a name="odbc"></a>ODBC


### <a name="linked-service"></a>Länkad tjänst
Definiera ODBC länkade tjänsten genom att ange den **typen** på den länkade tjänsten till **OnPremisesOdbc**, och ange följande egenskaper i den **typeProperties** avsnitt:  

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| connectionString |Den icke-autentiseringsuppgifter delen av anslutningssträngen och en valfri krypterade autentiseringsuppgifter. Se exemplen i följande avsnitt. |Ja |
| credential |Åtkomst autentiseringsuppgifter del av den angivna anslutningssträngen i drivrutinsspecifika egenskapsvärdet format. Exempel ”: Uid =<user ID>; Pwd =<password>; RefreshToken =<secret refresh token>”;. |Nej |
| authenticationType |Typ av autentisering som används för att ansluta till ODBC-datalagret. Möjliga värden är: anonyma och grundläggande. |Ja |
| användarnamn |Ange användarnamnet om du använder grundläggande autentisering. |Nej |
| lösenord |Ange lösenordet för det användarkonto som du angav för användarnamnet. |Nej |
| gatewayName |Namnet på den gateway som Data Factory-tjänsten ska använda för att ansluta till ODBC-datalagret. |Ja |

#### <a name="example---using-basic-authentication"></a>Exempel: använder grundläggande autentisering

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
Du kan kryptera autentiseringsuppgifterna med den [ny AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) (version 1.0 av Azure PowerShell) cmdlet eller [ny AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/dn834940.aspx) (0,9 eller tidigare version av Azure PowerShell).  

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

Mer information finns i [ODBC connector](data-factory-odbc-connector.md#linked-service-properties) artikel. 

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en ODBC-datauppsättning, ange den **typen** på datamängden som ska **RelationalTable**, och ange följande egenskaper i den **typeProperties** avsnitt: 

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i ODBC-datakällan. |Ja |


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

Mer information finns i [ODBC connector](data-factory-odbc-connector.md#dataset-properties) artikel. 

### <a name="relational-source-in-copy-activity"></a>Relationella källa i en Kopieringsaktivitet
Om du vill kopiera data från en ODBC-datalagret, ange den **typ av datakälla** för kopieringsaktiviteten till **RelationalSource**, och ange följande egenskaper i den **källa** avsnitt :

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

Mer information finns i [ODBC connector](data-factory-odbc-connector.md#copy-activity-properties) artikel.

## <a name="salesforce"></a>Salesforce


### <a name="linked-service"></a>Länkad tjänst
Definiera en Salesforce länkade tjänsten genom att ange den **typen** på den länkade tjänsten till **Salesforce**, och ange följande egenskaper i den **typeProperties** avsnitt:  

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| environmentUrl | Ange URL: en för Salesforce-instans. <br><br> – Standardvärdet är ”https://login.salesforce.com”. <br> -Om du vill kopiera data från sandbox, ange ”https://test.salesforce.com”. <br> -Om du vill kopiera data från domänen, ange, till exempel ”https://[domain].my.salesforce.com”. |Nej |
| användarnamn |Ange ett användarnamn för användarkontot. |Ja |
| lösenord |Ange ett lösenord för användarkontot. |Ja |
| securityToken |Ange en säkerhetstoken för användarkontot. Se [hämta säkerhetstoken](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) för instruktioner om hur du återställa/hämta en säkerhetstoken. Läs om säkerhetstoken i allmänhet i [säkerhets- och API: et](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Ja |

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

Mer information finns i [Salesforce-anslutningsprogrammet](data-factory-salesforce-connector.md#linked-service-properties) artikel. 

### <a name="dataset"></a>Datauppsättning
Om du vill definiera en Salesforce-dataset, ange den **typen** på datamängden som ska **RelationalTable**, och ange följande egenskaper i den **typeProperties** avsnitt: 

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i Salesforce. |Nej (om en **frågan** av **RelationalSource** har angetts) |

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

Mer information finns i [Salesforce-anslutningsprogrammet](data-factory-salesforce-connector.md#dataset-properties) artikel. 

### <a name="relational-source-in-copy-activity"></a>Relationella källa i en Kopieringsaktivitet
Om du vill kopiera data från Salesforce, ange den **typ av datakälla** för kopieringsaktiviteten till **RelationalSource**, och ange följande egenskaper i den **källa** avsnitt:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd anpassad fråga för att läsa data. |En SQL-92-fråga eller [Salesforce objektet Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) frågan. Till exempel: `select * from MyTable__c`. |Nej (om den **tableName** av den **dataset** har angetts) |

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
> ”__C”-delen av API-namnet krävs för alla anpassade objekt.

Mer information finns i [Salesforce-anslutningsprogrammet](data-factory-salesforce-connector.md#copy-activity-properties) artikel. 

## <a name="web-data"></a>Web-Data 

### <a name="linked-service"></a>Länkad tjänst
Att definiera en webbplats länkade tjänsten genom att ange den **typen** på den länkade tjänsten till **Web**, och ange följande egenskaper i den **typeProperties** avsnitt:  

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| URL |URL till webbadressen |Ja |
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

Mer information finns i [Webbtabell connector](data-factory-web-table-connector.md#linked-service-properties) artikel. 

### <a name="dataset"></a>Datauppsättning
För att definiera en Web dataset, ange den **typen** på datamängden som ska **WebTable**, och ange följande egenskaper i den **typeProperties** avsnitt: 

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Typ av datauppsättningen. måste anges till **WebTable** |Ja |
| sökväg |En relativ URL till den resurs som innehåller tabellen. |Nej. Om sökvägen inte anges används den URL som angavs i definitionen länkade tjänsten. |
| index |Index för tabellen i resursen. Se [Get-index för en tabell i en HTML-sida](#get-index-of-a-table-in-an-html-page) avsnittet steg för att få index för en tabell i en HTML-sida. |Ja |

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

Mer information finns i [Webbtabell connector](data-factory-web-table-connector.md#dataset-properties) artikel. 

### <a name="web-source-in-copy-activity"></a>Webbadress i en Kopieringsaktivitet
Om du kopierar data från en webbserver-tabell, ange den **typ av datakälla** för kopieringsaktiviteten till **WebSource**. För närvarande när datakällan i en Kopieringsaktivitet är av typen **WebSource**, inga ytterligare egenskaper som stöds.

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

Mer information finns i [Webbtabell connector](data-factory-web-table-connector.md#copy-activity-properties) artikel. 

## <a name="compute-environments"></a>COMPUTE-MILJÖER
I följande tabell visas beräknings-miljöer som stöds av Data Factory och omvandling av aktiviteter som kan köras på dem. Klicka på länken för den beräknade som du är intresserad av att se JSON-scheman för den länkade tjänsten att länka den till en datafabrik. 

| Compute-miljö | Aktiviteter |
| --- | --- |
| [HDInsight-kluster på begäran](#on-demand-azure-hdinsight-cluster) eller [egna HDInsight-kluster](#existing-azure-hdinsight-cluster) |[.NET anpassad aktivitet](#net-custom-activity), [Hive aktiviteten](#hdinsight-hive-activity), [svin aktivitet] (#hdinsight-pig-aktivitet, [MapReduce activity](#hdinsight-mapreduce-activity), [Hadoop-strömning aktiviteten](#hdinsight-streaming-activityd), [ Spark-aktivitet](#hdinsight-spark-activity) |
| [Azure Batch](#azure-batch) |[.NET-anpassad aktivitet](#net-custom-activity) |
| [Azure Machine Learning](#azure-machine-learning) | [Maskininlärning Batchkörningsaktivitet](#machine-learning-batch-execution-activity), [Maskininlärning Uppdateringsresursaktivitet](#machine-learning-update-resource-activity) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics) |[Data Lake Analytics U-SQL](#data-lake-analytics-u-sql-activity) |
| [Azure SQL Database](#azure-sql-database-1), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-1), [SQLServer](#sql-server-1) |[Lagrad procedur](#stored-procedure-activity) |

## <a name="on-demand-azure-hdinsight-cluster"></a>Azure HDInsight-kluster på begäran
Azure Data Factory-tjänsten kan automatiskt skapa ett Windows/Linux-baserade på begäran HDInsight-kluster för bearbetning av data. Klustret skapas i samma region som lagringskontot (linkedServiceName-egenskapen i JSON) som är associerade med klustret. Du kan köra följande omvandling aktiviteter på den här länkade tjänsten: [.NET anpassad aktivitet](#net-custom-activity), [Hive aktiviteten](#hdinsight-hive-activity), [svin aktivitet] (#hdinsight-pig-aktivitet, [MapReduce activity ](#hdinsight-mapreduce-activity), [Hadoop-strömning aktiviteten](#hdinsight-streaming-activityd), [Väck aktiviteten](#hdinsight-spark-activity). 

### <a name="linked-service"></a>Länkad tjänst 
Följande tabell innehåller beskrivningar för egenskaper som används i Azure JSON-definitionen för en länkad HDInsight på begäran-tjänst.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Typegenskapen bör anges till **HDInsightOnDemand**. |Ja |
| clusterSize |Antal worker/data noder i klustret. HDInsight-kluster skapas med 2 huvudnoderna tillsammans med antalet arbetarnoder som du anger för den här egenskapen. Noder har storlek Standard_D3 med 4 kärnor, så ett kluster med noder 4 worker tar 24 kärnor (4\*4 = 16 kärnor för arbetarnoder plus 2\*4 = 8 kärnor för huvudnoderna). Se [skapa Linux-baserade Hadoop-kluster i HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) för ytterligare information om Standard_D3-nivå. |Ja |
| TimeToLive |Tillåten inaktivitetstid för HDInsight-kluster på begäran. Anger hur länge HDInsight-kluster på begäran förblir aktiva efter slutförande av en aktivitet som kör om det finns inga aktiva jobb i klustret.<br/><br/>Till exempel om en aktivitet kör tar 6 minuter och timetolive är inställd på 5 minuter, förblir klustret aktiv på 5 minuter efter 6 minuter för aktiviteten kör. Om en annan aktivitet kör körs med fönstret 6 minuter, bearbetas men det av samma kluster.<br/><br/>Skapar ett HDInsight-kluster på begäran är en kostsam åtgärd (kan ta en stund), så Använd den här inställningen som krävs för att förbättra prestanda för en datafabrik genom att återanvända ett HDInsight-kluster på begäran.<br/><br/>Om du anger timetolive-värdet till 0 tas klustret bort när aktiviteten körs i bearbetade. Å andra sidan, om du anger ett högt värde för kan klustret förblir inaktiva i onödan ledde höga kostnader. Det är därför viktigt att du har angett rätt värde baserat på dina behov.<br/><br/>Flera pipelines kan dela samma instans av HDInsight-kluster på begäran om egenskapen timetolive-värdet är korrekt inställd |Ja |
| version |Version av HDInsight-klustret. Mer information finns i [HDInsight-versioner som stöds i Azure Data Factory](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory). |Nej |
| linkedServiceName |Azure Storage länkade tjänsten som ska användas av klustret på begäran för lagring och bearbetning av data. <p>För närvarande kan du skapa ett HDInsight-kluster med på begäran som använder ett Azure Data Lake Store som lagring. Om du vill lagra Resultatdata från HDInsight som bearbetas i en Azure Data Lake Store kan du använda en Kopieringsaktiviteten för att kopiera data från Azure Blobblagring till Azure Data Lake Store.</p>  | Ja |
| additionalLinkedServiceNames |Anger ytterligare lagringskonton för till HDInsight länkade tjänsten så att Data Factory-tjänsten kan registrera dem å dina vägnar. |Nej |
| osType |Typ av operativsystem. Tillåtna värden är: (standard) för Windows och Linux |Nej |
| hcatalogLinkedServiceName |Namnet på Azure SQL länkade tjänst som pekar på HCatalog-databasen. HDInsight-kluster på begäran har skapats med Azure SQL-databas som metastore. |Nej |

### <a name="json-example"></a>JSON-exempel
Följande JSON definierar en Linux-baserade på begäran HDInsight länkad tjänst. Data Factory-tjänsten skapar automatiskt en **Linux-baserade** vid bearbetning av en datasektorn HDInsight-kluster. 

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

Mer information finns i [Compute länkade tjänster](data-factory-compute-linked-services.md) artikel. 

## <a name="existing-azure-hdinsight-cluster"></a>Befintligt Azure HDInsight-kluster
Du kan skapa en Azure HDInsight länkad tjänst för att registrera ditt eget kluster i HDInsight med Data Factory. Du kan köra följande data transformation aktiviteter på den här länkade tjänsten: [.NET anpassad aktivitet](#net-custom-activity), [Hive aktiviteten](#hdinsight-hive-activity), [svin aktivitet] (#hdinsight-pig-aktivitet, [MapReduce aktiviteten](#hdinsight-mapreduce-activity), [Hadoop-strömning aktiviteten](#hdinsight-streaming-activityd), [Väck aktiviteten](#hdinsight-spark-activity). 

### <a name="linked-service"></a>Länkad tjänst
Följande tabell innehåller beskrivningar för egenskaper som används i Azure JSON-definitionen för en Azure HDInsight länkad tjänst.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Typegenskapen bör anges till **HDInsight**. |Ja |
| clusterUri |URI för HDInsight-klustret. |Ja |
| användarnamn |Ange namnet på användaren som ska användas för att ansluta till ett befintligt HDInsight-kluster. |Ja |
| lösenord |Ange lösenordet för användarkontot. |Ja |
| linkedServiceName | Namn på länkad Azure Storage-tjänst som refererar till Azure blob storage som används av HDInsight-klustret. <p>För närvarande kan du ange ett Azure Data Lake Store länkade tjänsten för den här egenskapen. Du kan komma åt data i Azure Data Lake Store från Hive/Pig-skript om HDInsight-klustret har åtkomst till Data Lake Store. </p>  |Ja |

Versioner av HDInsight-kluster som stöds finns [HDInsight-versioner som stöds](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory). 

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
Du kan skapa en Azure Batch länkad tjänst för att registrera en Batch-pool med virtuella datorer (VM) med en data factory. Du kan köra .NET anpassade aktiviteter med hjälp av Azure Batch eller Azure HDInsight. Du kan köra en [.NET anpassad aktivitet](#net-custom-activity) på den här länkade tjänsten. 

### <a name="linked-service"></a>Länkad tjänst
Följande tabell innehåller beskrivningar för egenskaper som används i Azure JSON-definitionen för en Azure Batch länkad tjänst.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Typegenskapen bör anges till **AzureBatch**. |Ja |
| Kontonamn |Namnet på Azure Batch-kontot. |Ja |
| accessKey |Åtkomstnyckeln för Azure Batch-kontot. |Ja |
| Poolnamn |Namnet på poolen med virtuella datorer. |Ja |
| linkedServiceName |Namnet på Azure Storage länkade tjänst som är associerad med den här Azure Batch länkade tjänsten. Den här länkade tjänsten används för tillfälliga filer som krävs för att köra aktiviteten och lagrar körningsloggar aktivitet. |Ja |


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
Du kan skapa en Azure Machine Learning länkad tjänst för att registrera en Machine Learning-batch bedömningsslutpunkten med en data factory. Två data transformation aktiviteter som kan köras på den här länkade tjänsten: [Machine Learning-Batchkörningsaktivitet](#machine-learning-batch-execution-activity), [Machine Learning-Uppdateringsresursaktivitet](#machine-learning-update-resource-activity). 

### <a name="linked-service"></a>Länkad tjänst
Följande tabell innehåller beskrivningar för egenskaper som används i Azure JSON-definitionen för en Azure Machine Learning länkad tjänst.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| Typ |Typegenskapen bör anges till: **AzureML**. |Ja |
| mlEndpoint |URL för batchbedömningsjobbet. |Ja |
| apiKey |Den publicerade arbetsytemodellens API. |Ja |

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
Du skapar en **Azure Data Lake Analytics** länkad tjänst för att länka ett Azure Data Lake Analytics compute tjänst till ett Azure data factory innan du använder den [Data Lake Analytics U-SQL-aktivitet](data-factory-usql-activity.md) i en pipeline.

### <a name="linked-service"></a>Länkad tjänst

Följande tabell innehåller beskrivningar för egenskaper som används i JSON-definitionen för en länkad Azure Data Lake Analytics-tjänst. 

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| Typ |Typegenskapen bör anges till: **AzureDataLakeAnalytics**. |Ja |
| Kontonamn |Azure Data Lake Analytics-kontonamn. |Ja |
| dataLakeAnalyticsUri |Azure Data Lake Analytics-URI. |Nej |
| Auktorisering |Auktoriseringskoden hämtas automatiskt när du klickar på **auktorisera** knappen i den Data Factory-redigeraren och slutfört OAuth-inloggningen. |Ja |
| subscriptionId |Azure prenumerations-id |Nej (om den inte anges data factory-prenumeration används). |
| resourceGroupName |Azure-resursgruppsnamn |Nej (om inget annat anges, resursgruppen av datafabriken används). |
| sessions-ID |sessions-id från OAuth-auktorisering sessionen. Varje sessions-id är unikt och får endast användas en gång. När du använder Data Factory-redigeraren genereras detta ID automatiskt. |Ja |


#### <a name="json-example"></a>JSON-exempel
I följande exempel innehåller JSON-definitionen för en länkad Azure Data Lake Analytics-tjänst.

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
Du skapar en Azure SQL-länkade tjänst och använda den med den [lagrade Proceduraktiviteten](#stored-procedure-activity) att anropa en lagrad procedur från Data Factory-pipelinen. 

### <a name="linked-service"></a>Länkad tjänst
Definiera en Azure SQL Database länkade tjänsten genom att ange den **typen** på den länkade tjänsten till **AzureSqlDatabase**, och ange följande egenskaper i den **typeProperties** avsnitt:  

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| connectionString |Ange information som behövs för att ansluta till Azure SQL Database-instans för egenskapen connectionString. |Ja |

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

Se [Azure SQL Connector](data-factory-azure-sql-connector.md#linked-service-properties) artikeln för information om den här länkade tjänsten.

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Du skapar en länkad Azure SQL Data Warehouse-tjänst och använda den med den [lagrade Proceduraktiviteten](data-factory-stored-proc-activity.md) att anropa en lagrad procedur från Data Factory-pipelinen. 

### <a name="linked-service"></a>Länkad tjänst
Definiera en Azure SQL Data Warehouse länkade tjänsten genom att ange den **typen** på den länkade tjänsten till **AzureSqlDW**, och ange följande egenskaper i den **typeProperties** avsnitt:  

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| connectionString |Ange information som behövs för att ansluta till Azure SQL Data Warehouse-instans för egenskapen connectionString. |Ja |

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

Mer information finns i [Azure SQL Data Warehouse connector](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties) artikel. 

## <a name="sql-server"></a>SQL Server 
Du skapar en SQL Server som är länkad tjänst och använda den med den [lagrade Proceduraktiviteten](data-factory-stored-proc-activity.md) att anropa en lagrad procedur från Data Factory-pipelinen. 

### <a name="linked-service"></a>Länkad tjänst
Du skapar en länkad tjänst av typen **OnPremisesSqlServer** att länka en lokal SQL Server-databas till en datafabrik. Följande tabell innehåller en beskrivning för JSON-element som är specifika för lokala SQL Server länkad tjänst.

Följande tabell innehåller en beskrivning för JSON-element som är specifika för SQL Server som är länkad tjänst.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Typegenskapen bör anges till: **OnPremisesSqlServer**. |Ja |
| connectionString |Ange connectionString information som behövs för att ansluta till lokal SQL Server-databasen med hjälp av SQL-autentisering eller Windows-autentisering. |Ja |
| gatewayName |Namnet på den gateway som Data Factory-tjänsten ska använda för att ansluta till lokal SQL Server-databasen. |Ja |
| användarnamn |Ange användarnamnet om du använder Windows-autentisering. Exempel: **domainname\\användarnamn**. |Nej |
| lösenord |Ange lösenordet för det användarkonto som du angav för användarnamnet. |Nej |

Du kan kryptera autentiseringsuppgifterna med hjälp av den **ny AzureRmDataFactoryEncryptValue** cmdlet och använda dem i anslutningssträngen som visas i följande exempel (**EncryptedCredential** egenskapen):  

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

Om användarnamn och lösenord har angetts använder gateway dem för att personifiera det angivna användarkontot kan ansluta till lokal SQL Server-databasen. Annars ansluter gateway till SQL Server direkt med säkerhetskontexten för Gateway (dess start-konto).

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

Mer information finns i [SQL Server-anslutningen](data-factory-sqlserver-connector.md#linked-service-properties) artikel.

## <a name="data-transformation-activities"></a>DATA TRANSFORMATION AKTIVITETER

Aktivitet | Beskrivning
-------- | -----------
[HDInsight Hive-aktivitet](#hdinsight-hive-activity) | HDInsight Hive-aktivitet i en Data Factory-pipelinen kör Hive-frågor på egen hand eller på begäran Windows/Linux-baserade HDInsight-kluster. 
[HDInsight Pig-aktivitet](#hdinsight-pig-activity) | HDInsight Pig-aktiviteten i en Data Factory-pipelinen kör Pig frågor på egen hand eller på begäran Windows/Linux-baserade HDInsight-kluster.
[HDInsight MapReduce-aktivitet](#hdinsight-mapreduce-activity) | HDInsight MapReduce-aktiviteten i en Data Factory-pipelinen kör MapReduce program på egen hand eller på begäran Windows/Linux-baserade HDInsight-kluster.
[HDInsight-strömningsaktivitet](#hdinsight-streaming-activity) | HDInsight Streaming Activity i Data Factory-pipelinen kör Hadoop Streaming program på egen hand eller på begäran Windows/Linux-baserade HDInsight-kluster.
[HDInsight Spark-aktivitet](#hdinsight-spark-activity) | HDInsight Spark-aktivitet i en Data Factory-pipelinen körs Spark-program på din egen HDInsight-kluster. 
[Machine Learning Batch-körningsaktivitet](#machine-learning-batch-execution-activity) | Azure Data Factory kan du enkelt skapa pipelines som använder en publicerad Azure Machine Learning-webbtjänst för förutsägelseanalys. Du kan med hjälp av den Batchkörningsaktivitet i ett Azure Data Factory-pipelinen för att anropa en Machine Learning-webbtjänst för att göra förutsägelser på data i batch. 
[Machine Learning-uppdateringsresursaktivitet](#machine-learning-update-resource-activity) | Över tiden måste förutsägelsemodeller i Maskininlärning bedömningen experiment vara retrained med nya indatauppsättningar. När du är klar med omtränings som du vill uppdatera bedömningsprofil webbtjänsten med retrained Machine Learning-modellen. Du kan använda aktiviteten för att uppdatera resursen för att uppdatera webbtjänsten med den nyligen tränade modellen.
[Lagrad proceduraktivitet](#stored-procedure-activity) | Du kan använda aktiviteten lagrad procedur i en Data Factory-pipelinen för att anropa en lagrad procedur i någon av följande datalager: Azure SQL Database, Azure SQL Data Warehouse, SQL Server-databas i ditt företag eller en Azure VM. 
[Data Lake Analytics U-SQL-aktivitet](#data-lake-analytics-u-sql-activity) | Data Lake Analytics U-SQL-aktivitet körs ett U-SQL-skript i ett Azure Data Lake Analytics-kluster.  
[.NET-anpassad aktivitet](#net-custom-activity) | Om du behöver transformera data på ett sätt som inte stöds av Data Factory kan du skapa en anpassad aktivitet med din egen databearbetning logik och använder aktiviteten i pipelinen. Du kan konfigurera den anpassade .NET-aktiviteten ska köras med Azure Batch-tjänsten eller ett Azure HDInsight-kluster. 

     
## <a name="hdinsight-hive-activity"></a>HDInsight Hive-aktivitet
Du kan ange följande egenskaper i en definition av Hive aktivitets-JSON. Type-egenskapen för aktiviteten måste vara: **HDInsightHive**. Du måste först skapa en länkad HDInsight-tjänst och ange namnet på det som ett värde för den **linkedServiceName** egenskapen. Följande egenskaper stöds i den **typeProperties** avsnittet när du anger vilken typ av aktivitet som HDInsightHive:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| skriptet |Ange infogat för Hive-skript |Nej |
| sökvägen för skriptet |Lagra Hive-skript i ett Azure blob storage och ange sökvägen till filen. Använd egenskapen 'script' eller 'scriptPath'. Båda kan inte användas tillsammans. Filnamnet är skiftlägeskänslig. |Nej |
| definierar |Ange parametrar som nyckel/värde-par för refererar till i Hive-skript med hjälp av 'hiveconf' |Nej |

Dessa egenskaper är specifika för Hive-aktivitet. Andra egenskaper (utanför avsnittet typeProperties) stöds för alla aktiviteter.   

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

Mer information finns i [Hive aktiviteten](data-factory-hive-activity.md) artikel. 

## <a name="hdinsight-pig-activity"></a>HDInsight-piggningsåtgärd
Du kan ange följande egenskaper i en Pig aktivitet JSON-definition. Type-egenskapen för aktiviteten måste vara: **HDInsightPig**. Du måste först skapa en länkad HDInsight-tjänst och ange namnet på det som ett värde för den **linkedServiceName** egenskapen. Följande egenskaper stöds i den **typeProperties** avsnittet när du anger vilken typ av aktivitet som HDInsightPig: 

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| skriptet |Ange infogat för Pig-skript |Nej |
| sökvägen för skriptet |Lagra Pig-skriptet i en Azure blob storage och ange sökvägen till filen. Använd egenskapen 'script' eller 'scriptPath'. Båda kan inte användas tillsammans. Filnamnet är skiftlägeskänslig. |Nej |
| definierar |Ange parametrar som nyckel/värde-par för refererar till i Pig-skriptet |Nej |

Dessa egenskaper är specifika för Pig-aktiviteten. Andra egenskaper (utanför avsnittet typeProperties) stöds för alla aktiviteter.   

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

Mer information finns i [Pig aktiviteten](#data-factory-pig-activity.md) artikel. 

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce-aktivitet
Du kan ange följande egenskaper i en MapReduce aktivitet JSON-definition. Type-egenskapen för aktiviteten måste vara: **HDInsightMapReduce**. Du måste först skapa en länkad HDInsight-tjänst och ange namnet på det som ett värde för den **linkedServiceName** egenskapen. Följande egenskaper stöds i den **typeProperties** avsnittet när du anger vilken typ av aktivitet som HDInsightMapReduce: 

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| jarLinkedService | Namnet på den länkade tjänsten för Azure Storage som innehåller JAR-filen. | Ja |
| jarFilePath | Sökvägen till JAR-filen i Azure Storage. | Ja | 
| Klassnamn | Namnet på klassen huvudsakliga i JAR-filen. | Ja | 
| argument | En lista över kommaavgränsade argument för MapReduce-programmet. Vid körning kan du se några extra argument (till exempel: mapreduce.job.tags) från MapReduce-ramverket. Överväg att använda både alternativet och värde som argument som visas i följande exempel för att skilja dina argument med MapReduce-argument (- s,--indata--utdata osv., är en alternativ direkt följt av deras värden) | Nej | 

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

Mer information finns i [MapReduce Activity](data-factory-map-reduce.md) artikel. 

## <a name="hdinsight-streaming-activity"></a>HDInsight-strömningsaktivitet
Du kan ange följande egenskaper i en definition av Hadoop Streaming aktivitets-JSON. Type-egenskapen för aktiviteten måste vara: **HDInsightStreaming**. Du måste först skapa en länkad HDInsight-tjänst och ange namnet på det som ett värde för den **linkedServiceName** egenskapen. Följande egenskaper stöds i den **typeProperties** avsnittet när du anger vilken typ av aktivitet som HDInsightStreaming: 

| Egenskap  | Beskrivning | 
| --- | --- |
| Mapper | Namnet på den körbara mapparen. I det här exemplet är cat.exe mapper körbara.| 
| Reducer | Namnet på den körbara reducer. I det här exemplet är wc.exe reducer körbara. | 
| indata | Indatafilen (inklusive platsen) för mapparen. Exempel ”: wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt”: adfsample är blobbehållare, exempel/data/Gutenberg är mappen och davinci.txt är blob. |
| utdata | Utdatafilen (inklusive platsen) för reducer. Utdata för direktuppspelning av Hadoop-jobb skrivs till den plats som anges för den här egenskapen. |
| filePaths | Sökvägar för mappning och reducer körbara filer. Exempel: ”adfsample/example/apps/wc.exe” adfsample är blobbehållare, exempel/appar är mappen och wc.exe är den körbara filen. | 
| fileLinkedService | Azure Storage länkade tjänst som representerar den Azure-lagring som innehåller de filer som anges i avsnittet filePaths. | 
| argument | En lista över kommaavgränsade argument för MapReduce-programmet. Vid körning kan du se några extra argument (till exempel: mapreduce.job.tags) från MapReduce-ramverket. Överväg att använda både alternativet och värde som argument som visas i följande exempel för att skilja dina argument med MapReduce-argument (- s,--indata--utdata osv., är en alternativ direkt följt av deras värden) | 
| getDebugInfo | Ett valfritt element. När den är inställd på fel laddas i loggarna ned endast vid fel. När den är inställd på alla laddas alltid loggar oavsett körningsstatusen. | 

> [!NOTE]
> Du måste ange en datamängd för utdata för Hadoop Streaming Activity för den **matar ut** egenskapen. Den här datauppsättningen kan bara en dummy datamängd som krävs för att driva pipeline-schema (varje timme, varje dag, osv.). Om aktiviteten inte tar indata, du kan hoppa över att ange en inkommande datauppsättning för aktiviteten för den **indata** egenskapen.  

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

Mer information finns i [Hadoop Streaming Activity](data-factory-hadoop-streaming-activity.md) artikel. 

## <a name="hdinsight-spark-activity"></a>HDInsight Apache Spark-aktivitet
Du kan ange följande egenskaper i en definition av Spark aktivitet JSON. Type-egenskapen för aktiviteten måste vara: **HDInsightSpark**. Du måste först skapa en länkad HDInsight-tjänst och ange namnet på det som ett värde för den **linkedServiceName** egenskapen. Följande egenskaper stöds i den **typeProperties** avsnittet när du anger vilken typ av aktivitet som HDInsightSpark: 

| Egenskap  | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| rootPath | Azure Blob-behållaren och mappen som innehåller filen Spark. Filnamnet är skiftlägeskänslig. | Ja |
| entryFilePath | Relativa sökvägen till rotmappen på Spark/kodpaketet. | Ja |
| Klassnamn | Programmets Java/Spark huvudsakliga klass | Nej | 
| argument | En lista med kommandoradsargument till Spark-program. | Nej | 
| proxyUser | Användarkontot som ska personifiera för att köra Spark-program | Nej | 
| sparkConfig | Spark konfigurationsegenskaper. | Nej | 
| getDebugInfo | Anger när Spark loggfilerna kopieras till Azure storage som används av HDInsight-kluster (eller) anges av sparkJobLinkedService. Tillåtna värden: None, alltid eller fel. Standardvärde: Ingen. | Nej | 
| sparkJobLinkedService | Azure Storage länkade tjänst som äger Spark fil, beroenden och loggar.  Om du inte anger ett värde för den här egenskapen används lagring som är associerade med HDInsight-kluster. | Nej |

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

- Den **typen** egenskap är inställd på **HDInsightSpark**.
- Den **rootPath** är inställd på **adfspark\\pyFiles** där adfspark är Azure Blob-behållaren och pyFiles är bra mapp i behållaren. I det här exemplet är det som är associerad med Spark-kluster i Azure Blob Storage. Du kan överföra filen till en annan Azure Storage. Om du gör det, kan du skapa en länkad Azure Storage-tjänst om du vill länka detta lagringskonto till datafabriken. Ange namnet på den länkade tjänsten som ett värde för den **sparkJobLinkedService** egenskapen. Se [Spark Aktivitetsegenskaper](#spark-activity-properties) för ytterligare information om den här egenskapen och andra egenskaper som stöds av aktiviteten Spark.
- Den **entryFilePath** anges till den **test.py**, vilket är python-fil. 
- Den **getDebugInfo** egenskap är inställd på **alltid**, vilket betyder att filerna är alltid genereras (lyckade eller misslyckade).  

    > [!IMPORTANT]
    > Vi rekommenderar att du inte anger den här egenskapen till alltid i en produktionsmiljö om du felsöker ett problem. 
- Den **matar ut** avsnittet innehåller en datamängd för utdata. Du måste ange en datamängd för utdata, även om spark-program inte producerar några utdata. Datamängd för utdata enheter schemat för pipeline (varje timme, varje dag, osv.).

Mer information om aktiviteten finns [Spark aktiviteten](data-factory-spark.md) artikel.  

## <a name="machine-learning-batch-execution-activity"></a>Machine Learning Batch-körningsaktivitet
Du kan ange följande egenskaper i en Azure ML Batch Execution aktivitet JSON-definition. Type-egenskapen för aktiviteten måste vara: **AzureMLBatchExecution**. Du måste skapa en Azure Machine Learning länkade tjänsten först och ange namnet på det som ett värde för den **linkedServiceName** egenskapen. Följande egenskaper stöds i den **typeProperties** avsnittet när du anger vilken typ av aktivitet som AzureMLBatchExecution:

Egenskap  | Beskrivning | Krävs 
-------- | ----------- | --------
webServiceInput | Dataset som ska skickas som indata för Azure ML web service. Den här datauppsättningen måste också tas med i indata för aktiviteten. |Använda webServiceInput eller webServiceInputs. | 
webServiceInputs | Ange datauppsättningar som ska skickas som indata för Azure ML web service. Om webbtjänsten tar flera inmatningar, använder du egenskapen webServiceInputs istället för att använda egenskapen webServiceInput. Datauppsättningar som refererar till den **webServiceInputs** måste också tas med i aktiviteten **indata**. | Använda webServiceInput eller webServiceInputs. | 
webServiceOutputs | Datauppsättningar som är tilldelad som utdata för Azure ML web service. Webbtjänsten returnerar utdata i denna dataset. | Ja | 
globalParameters | Ange värden för parametrarna web service i det här avsnittet. | Nej | 

### <a name="json-example"></a>JSON-exempel
I det här exemplet aktiviteten har datauppsättningen **MLSqlInput** som indata och **MLSqlOutput** som utdata. Den **MLSqlInput** skickas som indata till webbtjänsten genom att använda den **webServiceInput** JSON-egenskapen. Den **MLSqlOutput** skickas som utdata till webbtjänsten genom att använda den **webServiceOutputs** JSON-egenskapen. 

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

I JSON-exempel använder distribuerade Azure Machine Learning webbtjänsten en läsare och skrivare-modulen för att läsa/skriva data från/till en Azure SQL Database. Den här webbtjänsten visar följande fyra parametrar: databasen servernamnet, databasnamnet, Server-användarkonto och serverlösenord.

> [!NOTE]
> Endast indata och utdata för aktiviteten AzureMLBatchExecution kan skickas som parametrar till webbtjänsten. Ovanstående JSON-kodutdrag är till exempel MLSqlInput indata till aktiviteten AzureMLBatchExecution som skickas som indata till webbtjänsten via webServiceInput-parametern.

## <a name="machine-learning-update-resource-activity"></a>Machine Learning-uppdateringsresursaktivitet
Du kan ange följande egenskaper i en Azure ML Update resurs aktiviteten JSON-definition. Type-egenskapen för aktiviteten måste vara: **AzureMLUpdateResource**. Du måste skapa en Azure Machine Learning länkade tjänsten först och ange namnet på det som ett värde för den **linkedServiceName** egenskapen. Följande egenskaper stöds i den **typeProperties** avsnittet när du anger vilken typ av aktivitet som AzureMLUpdateResource:

Egenskap  | Beskrivning | Krävs 
-------- | ----------- | --------
trainedModelName | Namnet på retrained modell. | Ja |  
trainedModelDatasetName | DataSet pekar på iLearner-fil som returneras av omtränings igen. | Ja | 

### <a name="json-example"></a>JSON-exempel
Pipelinen har två aktiviteter: **AzureMLBatchExecution** och **AzureMLUpdateResource**. Azure ML-batchkörning aktiviteten hämtar utbildning data som indata och genererar en iLearner-fil som utdata. Aktiviteten anropar webbtjänsten utbildning (träningsexperiment visas som en webbtjänst) med data som indata utbildning och tar emot den ilearner-fil från den här webbtjänsten. PlaceholderBlob är bara en dummy datamängd för utdata som krävs av Azure Data Factory-tjänsten för att köra pipelinen.


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
Du kan ange följande egenskaper i en definition av U-SQL-aktivitet JSON. Type-egenskapen för aktiviteten måste vara: **DataLakeAnalyticsU SQL**. Du måste skapa en länkad Azure Data Lake Analytics-tjänst och ange namnet på det som ett värde för den **linkedServiceName** egenskapen. Följande egenskaper stöds i den **typeProperties** avsnittet när du anger vilken typ av aktivitet som DataLakeAnalyticsU SQL: 

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| scriptPath |Sökvägen till mappen som innehåller U-SQL-skript. Namnet på filen är skiftlägeskänslig. |Nej (om du använder skriptet) |
| scriptLinkedService |Länkade tjänst som länkar den lagring som innehåller skriptet till data factory |Nej (om du använder skriptet) |
| skriptet |Ange infogat skript i stället för att ange scriptPath och scriptLinkedService. Till exempel: ”skript”: ”skapa databastest”. |Nej (om du använder scriptPath och scriptLinkedService) |
| degreeOfParallelism |Maximalt antal noder samtidigt används för att köra jobbet. |Nej |
| prioritet |Anger vilka jobb av alla köas ska väljas att köras först. Ju lägre nummer, desto högre prioritet. |Nej |
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
Du kan ange följande egenskaper i en lagrad procedur aktivitet JSON-definition. Type-egenskapen för aktiviteten måste vara: **SqlServerStoredProcedure**. Du måste skapa en av följande länkade tjänster och ange namnet på den länkade tjänsten som värde för den **linkedServiceName** egenskapen:

- SQL Server 
- Azure SQL Database
- Azure SQL Data Warehouse

Följande egenskaper stöds i den **typeProperties** avsnittet när du anger vilken typ av aktivitet som SqlServerStoredProcedure:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| storedProcedureName |Ange namnet på den lagrade proceduren i Azure SQL database eller Azure SQL Data Warehouse som representeras av den länkade tjänst som använder utdatatabellen. |Ja |
| storedProcedureParameters |Ange värden för parametrarna för lagrade procedurer. Om du måste överföra null för en parameter, använder du syntax: ”param1”: null (alla gemen). Se följande exempel mer information om hur du använder den här egenskapen. |Nej |

Om du anger en inkommande datauppsättning, måste det vara tillgänglig (statusen ”klar”) för aktiviteten lagrad procedur att köra. Inkommande dataset kan inte användas i den lagrade proceduren som en parameter. Den används endast för att kontrollera beroendet innan du startar aktiviteten lagrad procedur. Du måste ange en datamängd för utdata för en lagrad procedur-aktivitet. 

Utdatauppsättningen anger den **schema** för aktiviteten lagrad procedur (varje timme, varje vecka, månad, etc.). Datamängd för utdata måste använda en **länkade tjänsten** som refererar till en Azure SQL Database eller ett Azure SQL Data Warehouse eller en SQL Server-databas som du vill använda den lagrade proceduren för att köra. Datamängd för utdata kan fungera som ett sätt att skicka resultatet av den lagrade proceduren för senare bearbetning av en annan aktivitet ([länkning aktiviteter](data-factory-scheduling-and-execution.md##multiple-activities-in-a-pipeline)) i pipelinen. Dock skriver Data Factory inte automatiskt utdata från en lagrad procedur denna DataSet. Det är den lagrade proceduren som skriver till en SQLtabell som datamängd för utdata som pekar på. I vissa fall datamängd för utdata kan vara en **dummy dataset**, som används bara för att ange schemat för att köra aktiviteten lagrad procedur.  

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

Mer information finns i [lagrade Proceduraktiviteten](data-factory-stored-proc-activity.md) artikel. 

## <a name="net-custom-activity"></a>.NET-anpassad aktivitet
Du kan ange följande egenskaper i en anpassad aktivitet för .NET JSON-definitionen. Type-egenskapen för aktiviteten måste vara: **DotNetActivity**. Du måste skapa en Azure HDInsight länkad tjänst eller ett Azure Batch länkade tjänsten och ange namnet på den länkade tjänsten som värde för den **linkedServiceName** egenskapen. Följande egenskaper stöds i den **typeProperties** avsnittet när du anger vilken typ av aktivitet som DotNetActivity:
 
| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| AssemblyName | Namnet på sammansättningen. I det här exemplet är: **MyDotnetActivity.dll**. | Ja |
| EntryPoint |Namnet på den klass som implementerar gränssnittet IDotNetActivity. I det här exemplet är: **MyDotNetActivityNS.MyDotNetActivity** där MyDotNetActivityNS är namnområdet och MyDotNetActivity är klassen.  | Ja | 
| PackageLinkedService | Namn på länkad Azure Storage-tjänst som pekar till blob-lagring som innehåller anpassad aktivitet zip-filen. I det här exemplet är: **AzureStorageLinkedService**.| Ja |
| PackageFile | Namnet på zip-filen. I det här exemplet är: **customactivitycontainer/MyDotNetActivity.zip**. | Ja |
| extendedProperties | Utökade egenskaper som du kan definiera och vidarebefordra till .NET-kod. I det här exemplet i **SliceStart** variabeln anges till ett värde baserat på variabeln SliceStart system. | Nej | 

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

Detaljerad information finns i [använda anpassade aktiviteter i Data Factory](data-factory-use-custom-activities.md) artikel. 

## <a name="next-steps"></a>Nästa steg
Se följande självstudiekurser: 

- [Självstudier: skapa en pipeline med en kopia-aktivitet](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Självstudier: skapa en pipeline med en hive-aktivitet](data-factory-build-your-first-pipeline-using-editor.md)
