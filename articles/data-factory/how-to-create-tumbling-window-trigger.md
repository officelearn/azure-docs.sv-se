---
title: Skapa utlösare för rullande fönster i Azure Data Factory | Microsoft Docs
description: Lär dig hur du skapar en utlösare i Azure Data Factory som kör en pipeline på ett rullande fönster.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: shlo
ms.openlocfilehash: 6fbdee71ab1123c258a5191a78e38f51eb41cbab
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433237"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Skapa en utlösare som kör en pipeline på ett rullande fönster
Den här artikeln innehåller steg för att skapa, starta och övervaka en utlösare för rullande fönster. Allmän information om utlösare och typerna som stöds finns i [Pipelinekörning och utlösare](concepts-pipeline-execution-triggers.md).

Utlösare för rullande fönster är en typ av utlösare som går igång med jämna tidsintervall från en angiven starttid och behåller sitt tillstånd. Rullande fönster är en serie sammanhängande tidsintervall med fast storlek som inte överlappar. En utlösare för rullande fönster har ett förhållande med en pipeline och kan bara referera till en enda pipeline.

## <a name="data-factory-ui"></a>Data Factory-användargränssnitt

Om du vill skapa en utlösare för rullande fönster i Azure portal, Välj **utlösare > rullande fönster > nästa**, och sedan konfigurera de egenskaper som definierar rullande fönster.

![Skapa en utlösare för rullande fönster i Azure portal](media/how-to-create-tumbling-window-trigger/create-tumbling-window-trigger.png)

## <a name="tumbling-window-trigger-type-properties"></a>Rullande fönster Typegenskaper för utlösare
Ett rullande fönster har följande egenskaper för typ av utlösare:

```
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": "<<Started/Stopped/Disabled - readonly>>",
        "typeProperties": {
            "frequency": "<<Minute/Hour>>",
            "interval": <<int>>,
            "startTime": "<<datetime>>",
            "endTime: "<<datetime – optional>>"",
            "delay": "<<timespan – optional>>",
            “maxConcurrency”: <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                “intervalInSeconds”: <<int>>,
            }
        },
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipelineName"
            },
            "parameters": {
                "parameter1": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "parameter2": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "parameter3": "https://mydemo.azurewebsites.net/api/demoapi"
            }
        }
    }
}
```

Följande tabell innehåller en översikt över de viktigaste JSON-element som är relaterade till upprepning och schemaläggning i en utlösare för rullande fönster:

| JSON-element | Beskrivning | Type | Tillåtna värden | Krävs |
|:--- |:--- |:--- |:--- |:--- |
| **typ** | Typ av utlösaren. Typen är det fasta värdet ”TumblingWindowTrigger”. | String | "TumblingWindowTrigger" | Ja |
| **runtimeState** | Körningstiden för det aktuella tillståndet för utlösaren.<br/>**Obs!** Det här elementet har \<readOnly >. | String | ”Started”, Stoppad ”” ”inaktiverad” | Ja |
| **frequency** | En sträng som representerar frekvens (minuter eller timmar) att utlösaren ska återkomma. Om den **startTime** datumvärden är större än den **frekvens** värde, den **startTime** datum anses när fönstret gränserna beräknas. Till exempel om den **frekvens** värdet är per timme och **startTime** värdet är 2017-09-01T10:10:10Z, det första fönstret är (2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z). | String | ”minut”, ”hour”  | Ja |
| **interval** | Ett positivt heltal som anger intervallet för värdet för **frequency** och som avgör hur ofta utlösaren körs. Till exempel om den **intervall** är 3 och **frekvens** är ”hour” utlösaren återkommer var tredje timme. | Integer | Ett positivt heltal. | Ja |
| **startTime**| Den första förekomsten, vilket kan vara i förflutna. Den första utlösaren är (**startTime**, **startTime** + **intervall**). | DateTime | Ett DateTime-värde. | Ja |
| **endTime**| Den sista förekomsten, vilket kan vara i förflutna. | DateTime | Ett DateTime-värde. | Ja |
| **fördröjning** | Hur lång tid att fördröja starten av databearbetningen för fönstret. Pipelinekörningen startas efter den förväntade tiden för körningen plus det **fördröjning**. Den **fördröjning** definierar hur länge utlösaren väntar tidigare förfallotiden innan du utlöser ett nytt kör. Den **fördröjning** påverkar inte fönstret **startTime**. Till exempel en **fördröjning** värdet 00:10:00 innebär en fördröjning på 10 minuter. | Tidsintervall<br/>(: mm: ss)  | Ett timespan-värde där standardvärdet är 00:00:00. | Nej |
| **maxConcurrency** | Antal samtidiga utlösarkörningar som aktiverats för windows som är klara. Om du vill Bakgrundsfyllning körs till exempel varje timme för igår resultat i 24 windows. Om **maxConcurrency** = 10, utlösare händelser skickas endast för de första 10 windows (00:00-01:00 - 09:00-10:00). När de första 10 utlösta pipelinekörningarna har slutförts kan utlösta utlösarkörningar för nästa 10 windows (10:00-11:00 – 19:00-20:00). Du fortsätter med det här exemplet på **maxConcurrency** = 10, om det finns 10 windows som är redo, det finns 10 totala pipelinekörningar. Om det finns bara 1 fönstret redo, finns bara 1 pipeline-körning. | Integer | Ett heltal mellan 1 och 50. | Ja |
| **retryPolicy: Antal** | Antalet försök innan pipeline-körning har markerats som ”misslyckades”.  | Integer | Ett heltal, där standardvärdet är 0 (inga nya försök). | Nej |
| **retryPolicy: intervalInSeconds** | Fördröjningen mellan återförsök som anges i sekunder. | Integer | Antal sekunder, där standardvärdet är 30. | Nej |

### <a name="windowstart-and-windowend-system-variables"></a>WindowStart och WindowEnd systemvariabler

Du kan använda den **WindowStart** och **WindowEnd** systemvariabler av utlösare för rullande fönster i din **pipeline** definition (det vill säga för en del av en fråga). Skicka systemvariablerna som parametrar till din pipeline i den **utlösaren** definition. I följande exempel visar hur du skickar dessa variabler som parametrar:

```
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
            ...
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipelineName"
            },
            "parameters": {
                "MyWindowStart": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "MyWindowEnd": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                }
            }
        }
    }
}
```

Du använder den **WindowStart** och **WindowEnd** system variabelvärdena i pipeline-definition använder parametrarna ”MyWindowStart” och ”MyWindowEnd”, i enlighet med detta.

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>Körningsordning för Windows i ett scenario med återfyller
När det finns flera fönster för körning (särskilt i ett scenario med återfyller), är ordningen för körningen för windows deterministisk från äldsta till nyaste intervall. Det här beteendet kan för närvarande inte ändras.

### <a name="existing-triggerresource-elements"></a>Befintliga TriggerResource element
Följande punkter gäller för befintliga **TriggerResource** element:

* Om värdet för den **frekvens** element (eller fönsterstorleken) av utlösaren ändringarna, tillståndet för windows som redan har behandlats är *inte* återställa. Utlösaren fortsätter att för windows från fönstret senaste som den körs med hjälp av det nya fönstrets storleken.
* Om värdet för den **endTime** element i utlösaren ändringarna (läggs till eller uppdateras), tillståndet för windows som redan har behandlats är *inte* återställa. Utlösaren godkänner den nya **endTime** värde. Om den nya **endTime** värdet är innan windows som redan körs, stoppas för utlösaren. I annat fall utlösaren stannar när den nya **endTime** värde har påträffats.

## <a name="sample-for-azure-powershell"></a>Exempel för Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Det här avsnittet visar hur du använder Azure PowerShell för att skapa, starta och övervaka en utlösare.

1. Skapa en JSON-fil med namnet **MyTrigger.json** i mappen C:\ADFv2QuickStartPSH\ med följande innehåll:

    > [!IMPORTANT]
    > Innan du sparar JSON-fil, ange värdet för den **startTime** elementet så att den aktuella UTC-tiden. Ange värdet för den **endTime** element till en timme efter den aktuella UTC-tiden.

    ```json
    {
      "name": "PerfTWTrigger",
      "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
          "frequency": "Minute",
          "interval": "15",
          "startTime": "2017-09-08T05:30:00Z",
          "delay": "00:00:01",
          "retryPolicy": {
            "count": 2,
            "intervalInSeconds": 30
          },
          "maxConcurrency": 50
        },
        "pipeline": {
          "pipelineReference": {
            "type": "PipelineReference",
            "referenceName": "DynamicsToBlobPerfPipeline"
          },
          "parameters": {
            "windowStart": "@trigger().outputs.windowStartTime",
            "windowEnd": "@trigger().outputs.windowEndTime"
          }
        },
        "runtimeState": "Started"
      }
    }
    ```

2. Skapa en utlösare med hjälp av den **Set-AzDataFactoryV2Trigger** cmdlet:

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
    
3. Kontrollera att statusen för utlösaren är **stoppad** med hjälp av den **Get-AzDataFactoryV2Trigger** cmdlet:

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Starta utlösaren med hjälp av den **Start AzDataFactoryV2Trigger** cmdlet:

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Kontrollera att statusen för utlösaren är **startad** med hjälp av den **Get-AzDataFactoryV2Trigger** cmdlet:

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6. Hämta som utlösaren körs i Azure PowerShell med hjälp av den **Get-AzDataFactoryV2TriggerRun** cmdlet. Kör följande kommando med jämna mellanrum för att få information om utlösaren körs. Uppdatera den **TriggerRunStartedAfter** och **TriggerRunStartedBefore** värdena för att matcha värdena i utlösarens definition:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
Övervaka utlösarkörningar och pipeline som körs i Azure portal, se [övervaka pipelinekörningar](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="next-steps"></a>Nästa steg
Detaljerad information om utlösare finns i [Pipelinekörning och utlösare](concepts-pipeline-execution-triggers.md#triggers).
