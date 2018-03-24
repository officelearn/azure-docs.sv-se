---
title: Skapa rullande fönster utlösare i Azure Data Factory | Microsoft Docs
description: Lär dig hur du skapar en utlösare i Azure Data Factory som kör en pipeline på en rullande fönster.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: shlo
ms.openlocfilehash: 312072a5de21ff1c6b602fed93b77c564b15a9f1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Skapa en utlösare som kör en pipeline på en rullande fönster
Den här artikeln innehåller steg för att skapa, starta och övervaka en rullande fönster utlösare. Allmän information om utlösare och typerna som stöds finns [Pipeline körning och utlösare](concepts-pipeline-execution-triggers.md).

> [!NOTE]
> Den här artikeln gäller för version 2 av Azure Data Factory, som för närvarande är en förhandsversion. Om du använder Azure Data Factory version 1, som är allmänt tillgänglig (GA), se [Kom igång med Azure Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Utlösare för rullande fönster är en typ av utlösare som går igång med jämna tidsintervall från en angiven starttid och behåller sitt tillstånd. Rullande fönster är en serie sammanhängande tidsintervall med fast storlek som inte överlappar. En rullande fönster utlösare har en 1: 1-relation med en rörledning och kan bara referera till en enkel rörledning.

## <a name="tumbling-window-trigger-type-properties"></a>Rullande fönster utlösaren egenskaper
En rullande fönster har följande egenskaper för typen av utlösare:

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
                "count":  <<int - optional, default: 0>>,
                “intervalInSeconds”: <<int>>,
            }
        },
        "pipeline":
            {
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

Följande tabell innehåller en översikt över viktiga JSON-element som är relaterade till upprepning och schemaläggning av en rullande fönster utlösare:

| JSON-element | Beskrivning | Typ | Tillåtna värden | Krävs |
|:--- |:--- |:--- |:--- |:--- |
| **Typ** | Typ av utlösaren. Typen är det fasta värdet ”TumblingWindowTrigger”. | Sträng | "TumblingWindowTrigger" | Ja |
| **runtimeState** | Det aktuella tillståndet för utlösaren körtid.<br/>**Obs**: det här elementet har \<readOnly >. | Sträng | ”Starta”, stoppades ””, ”inaktiverad” | Ja |
| **frequency** | En sträng som representerar frekvens enhet (minuter eller timmar) att utlösaren ska återkomma. Om den **startTime** datumvärden är mer detaljerad än den **frekvens** värde, den **startTime** datum anses när fönstret gränser beräknas. Till exempel om den **frekvens** värdet är varje timme och **startTime** värdet är 2016-04-01T10:10:10Z, det första fönstret är (2017-09-01T10:10:10Z 2017-09-01T11:10:10Z). | Sträng | ”minuter”, ”timmar”  | Ja |
| **interval** | Ett positivt heltal som anger intervallet för värdet för **frequency** och som avgör hur ofta utlösaren körs. Till exempel om den **intervall** är 3 och **frekvens** är ”timme” utlösaren återkommer var 3: e timme. | Integer | Ett positivt heltal. | Ja |
| **startTime**| Den första förekomsten, vilket kan vara i förflutna. Den första utlösaren är (**startTime**, **startTime** + **intervall**). | DateTime | Ett DateTime-värde. | Ja |
| **endTime**| Den sista förekomsten, vilket kan vara i förflutna. | DateTime | Ett DateTime-värde. | Ja |
| **delay** | Hur lång tid att fördröja starten av databearbetningen för fönster. Pipelinen körs startas efter att den förväntade tiden för körningen plus mängden **fördröjning**. Den **fördröjning** definierar hur länge utlösaren ska vänta efter förfallotiden innan ett nytt kör. Den **fördröjning** påverkar inte fönstret **startTime**. Till exempel en **fördröjning** värdet 00:10:00 innebär en fördröjning på 10 minuter. | Tidsintervall  | Ett tidsvärde där standardvärdet är 00:00:00. | Nej |
| **maxConcurrency** | Antalet samtidiga utlösaren körs som aktiverats för windows som är klara. Om du vill säkerhetskopiera fill körs exempelvis varje timme i 24 windows igår resultat. Om **maxConcurrency** = 10, utlösare händelser skickas endast för de första 10 windows (01 00:00:00 - 09:00-10:00). När de första 10 utlösta pipelinen körs är klart, utlöses utlösaren körs för nästa 10 windows (10:00-11:00 – 19:00-20:00). Fortsätter med det här exemplet på **maxConcurrency** = 10, om det finns 10 windows redo, det finns 10 totala pipelinen körs. Om det finns endast 1 fönstret redo, är det bara 1 pipeline kör. | Integer | Ett heltal mellan 1 och 50. | Ja |
| **retryPolicy: antal** | Antalet försök innan du kör pipeline har markerats som ”misslyckades”.  | Integer | Ett heltal, där standardvärdet är 0 (inga nya försök). | Nej |
| **retryPolicy: intervalInSeconds** | Fördröjning mellan försök i sekunder. | Integer | Antal sekunder, där standardvärdet är 30. | Nej |

### <a name="windowstart-and-windowend-system-variables"></a>WindowStart och WindowEnd systemvariabler

Du kan använda den **WindowStart** och **WindowEnd** systemvariabler rullande fönster utlösare i din **pipeline** definition (det vill säga för en del av en fråga). Skicka systemvariabler som parametrar till din pipeline i den **utlösaren** definition. I följande exempel visas hur du skickar dessa variabler som parametrar:

```  
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
            ...
        "pipeline":
            {
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

Att använda den **WindowStart** och **WindowEnd** system variabelvärden i pipeline-definitionen använda parametrarna ”MyWindowStart” och ”MyWindowEnd”, i enlighet med detta.

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>Körningsordning av windows i ett scenario med fyller
När det finns flera fönster för körning (särskilt i ett scenario med fyller), är ordningen för körningen för windows deterministisk från äldsta till nyaste intervall. Det här beteendet kan för närvarande inte ändras.

### <a name="existing-triggerresource-elements"></a>Befintliga TriggerResource element
Följande punkter gäller för befintliga **TriggerResource** element:

* Om värdet för den **frekvens** element (eller fönsterstorleken) utlösaren ändringar, tillståndet för windows som redan har behandlats är *inte* återställa. Utlösaren fortsätter att för windows från det sista fönstret som den kördes med hjälp av den nya fönsterstorleken.
* Om värdet för den **endTime** elementet utlösaren ändringar (läggs till eller uppdateras), tillståndet för windows som redan har behandlats är *inte* återställa. Utlösaren godkänner den nya **endTime** värde. Om den nya **endTime** värdet är innan windows som redan körs stoppar för utlösare. Annars utlösaren slutar när den nya **endTime** värde har påträffats.

## <a name="sample-for-azure-powershell"></a>Exempel för Azure PowerShell
Det här avsnittet visar hur du använder Azure PowerShell för att skapa, starta och övervaka en utlösare.

1. Skapa en JSON-fil med namnet **MyTrigger.json** i mappen C:\ADFv2QuickStartPSH\ med följande innehåll:

   > [!IMPORTANT]
   > Innan du sparar JSON-fil, ange värdet för den **startTime** elementet så att den aktuella UTC-tid. Ange värdet för den **endTime** element till en timme efter den aktuella UTC-tid.

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

2. Skapa en utlösare med hjälp av den **Set AzureRmDataFactoryV2Trigger** cmdlet:

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
    
3. Bekräfta att status för utlösaren är **stoppad** med hjälp av den **Get-AzureRmDataFactoryV2Trigger** cmdlet:

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Starta utlösaren med hjälp av den **Start AzureRmDataFactoryV2Trigger** cmdlet:

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Bekräfta att status för utlösaren är **igång** med hjälp av den **Get-AzureRmDataFactoryV2Trigger** cmdlet:

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6. Get-utlösaren körs i Azure PowerShell med hjälp av den **Get-AzureRmDataFactoryV2TriggerRun** cmdlet. Kör följande kommando med jämna mellanrum för att få information om utlösaren körs. Uppdatering av **TriggerRunStartedAfter** och **TriggerRunStartedBefore** värden som stämmer med värdena i utlösardefinition:

    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
Övervaka utlösaren körs och pipeline körs i Azure-portalen, se [övervakaren pipeline körs](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="next-steps"></a>Nästa steg
Detaljerad information om utlösare finns [Pipeline körning och utlösare](concepts-pipeline-execution-triggers.md#triggers).
