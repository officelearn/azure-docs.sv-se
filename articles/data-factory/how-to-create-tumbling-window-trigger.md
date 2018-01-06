---
title: "Så här skapar du rullande fönster utlösare i Azure Data Factory | Microsoft Docs"
description: "Lär dig hur du skapar en utlösare i Azure Data Factory som kör en pipeline på en rullande fönster."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: shlo
ms.openlocfilehash: a3b056ae4bb4eda26fec58ca3b6bed7f0744e36e
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2018
---
# <a name="how-to-create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Hur du skapar en utlösare som kör en pipeline på en rullande fönster
Den här artikeln innehåller steg för att skapa, starta och övervaka en rullande fönster utlösare. Allmän information om utlösare och de typer som stöder vi finns [Pipeline körning och utlösare](concepts-pipeline-execution-triggers.md).

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Läs [get started with Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (kom igång med Data Factory version 1) om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig.

Rullande fönster utlösare är en typ av utlösare som startar med ett regelbundna tidsintervall från en angiven starttid, samtidigt som företaget behåller tillstånd. Rullande fönster är en serie med fast storlek, inte överlappar och sammanhängande intervall. En rullande fönster utlösare har en 1:1-relation med en rörledning och kan bara referera till en enkel rörledning.

## <a name="tumbling-window-trigger-type-properties"></a>Rullande fönster utlösaren egenskaper
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

Följande tabell innehåller en översikt över viktiga element relaterade till upprepning och schemaläggning i en rullande fönster utlösare.

| **JSON-namn** | **Beskrivning** | **Tillåtna värden** | **Krävs** |
|:--- |:--- |:--- |:--- |
| **typ** | Typ av utlösaren. Detta är fast ”TumblingWindowTrigger”. | Sträng | Ja |
| **runtimeState** | <readOnly>Möjliga värden: Startas, stoppas, inaktiverad | Sträng | Ja, readOnly |
| **frekvens** |Den *frekvens* sträng som representerar frekvens enheten att utlösaren ska återkomma. Värden som stöds är ”minut” och ”timme”. Om starttiden har datumdelar som är mer detaljerad än frekvensen, ska de beaktas att beräkna fönstret gränser. För exempel: om frekvensen är varje timme och starttiden är 2016-04-01T10:10:10Z, det första fönstret är (2017-09-01T10:10:10Z 2017-09-01T11:10:10Z.)  | Sträng. Typer som stöds ”minuter”, ”timmar” | Ja |
| **intervall** |Den *intervall* är ett positivt heltal och anger intervallet för den *frekvens* som avgör hur ofta utlösaren ska köras. Till exempel om *intervall* är 3 och *frekvens* är ”timme” utlösaren återkommer var 3: e timme. | Integer | Ja |
| **startTime**|*startTime* är datum och tid. *startTime* är den första förekomsten och kan vara i förflutna. Det första utlösaren intervallet att (startTime, startTime + intervall). | DateTime | Ja |
| **Sluttid**|*endTime* är datum och tid. *endTime* är den sista förekomsten och kan vara i förflutna. | DateTime | Ja |
| **fördröjning** | Ange fördröjning innan data bearbetning av fönstret startas. Pipelinen kör startas efter att den förväntade tiden för körningen + fördröjning. Fördröjning definierar hur länge utlösaren väntar förfallna tid innan nytt kör. Starttid för begränsningsfönstret påverkar inte. | TimeSpan (exempel: 00:10:00, innebär en fördröjning på 10 minuter) |  Nej. Standardvärdet är ”00: 00:00” |
| **högsta antal samtidiga** | Antal samtidiga utlösaren körs som aktiverats för windows som är klara. Exempel: om vi försöker fyller varje timme för i går, som är 24 windows. Om samtidighet = 10, utlösare händelser skickas endast för de första 10 windows (01 00:00:00 - 09:00-10:00). När de första 10 utlösta pipelinen körs är klart, utlöses utlösaren körs för nästa 10 (10:00-11:00 – 19:00-20:00). Fortsätter med exempel på samtidighet = 10, om det finns 10 windows redo finns 10 pipelinen körs. Om det finns endast 1 fönstret redo, finnas det endast 1 pipeline kör. | Integer | Ja. Möjliga värden 1-50 |
| **retryPolicy: antal** | Antalet försök innan du kör pipeline har markerats som ”misslyckades”  | Integer |  Nej. Standardvärdet är 0 återförsök |
| **retryPolicy: intervalInSeconds** | Fördröjningen mellan omförsök i sekunder | Integer |  Nej. Standardvärdet är 30 sekunder |

### <a name="using-system-variables-windowstart-and-windowend"></a>Med hjälp av systemvariabler: WindowStart och WindowEnd

Om du vill använda WindowStart och WindowEnd rullande fönster utlösare i din **pipeline** definition (d.v.s. för en del av en fråga), måste du skicka variablerna som parametrar till din pipeline i den **utlösaren**definition, t.ex:
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

Sedan, i pipeline-definitionen om du vill använda värdena WindowStart och WindowEnd använda parametrarna därför ”MyWindowStart” och ”MyWindowEnd”

### <a name="notes-on-backfill"></a>Information om fyller
När det finns flera fönster för körning (esp. i fyller scenariot), är deterministisk ordningen för körningen av windows och blir från äldsta till nyaste intervall. Det går inte att ändra det här beteendet från och med nu.

### <a name="updating-an-existing-triggerresource"></a>Uppdatera en befintlig TriggerResource
* Om frekvensen (eller fönsterstorleken) för utlösaren ändras tillståndet för windows redan har bearbetats kommer *inte* återställas. Utlösaren fortsätter att för windows från den sista som den körs med den nya fönsterstorleken.
* Sluttid för utlösaren ändringarna (läggs till eller uppdateras), tillståndet för windows redan bearbetas kommer *inte* återställas. Utlösaren följer bara nya sluttid. Om sluttid är före windows redan körs, stoppas utlösaren. Annars slutar när den nya sluttiden påträffas.

## <a name="sample-using-azure-powershell"></a>Exempel med hjälp av Azure PowerShell
Det här avsnittet visar hur du använder Azure PowerShell för att skapa, starta och övervaka en utlösare.

1. Skapa en JSON-fil med namnet MyTrigger.json i mappen C:\ADFv2QuickStartPSH\ med följande innehåll:

   > [!IMPORTANT]
   > Ange **startTime** till den aktuella UTC-tid och **endTime** tid innan du sparar JSON-filen till en timme efter den aktuella UTC.

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
2. Skapa en utlösare med hjälp av den **Set AzureRmDataFactoryV2Trigger** cmdlet.

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    
3. Confirm that the status of the trigger is **Stopped** by using the **Get-AzureRmDataFactoryV2Trigger** cmdlet.

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
4. Starta utlösaren med hjälp av den **Start AzureRmDataFactoryV2Trigger** cmdlet:

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
5. Bekräfta att status för utlösaren är **igång** med hjälp av den **Get-AzureRmDataFactoryV2Trigger** cmdlet.

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
6.  Hämta utlösaren körs med PowerShell med hjälp av den **Get-AzureRmDataFactoryV2TriggerRun** cmdlet. För att få information om utlösaren körs kan du köra följande kommando med jämna mellanrum: uppdatera **TriggerRunStartedAfter** och **TriggerRunStartedBefore** värden som stämmer med värdena i utlösardefinition .

    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
Om du vill övervaka utlösaren körs/pipeline körs i Azure portal finns [övervakaren pipeline körs](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="next-steps"></a>Nästa steg
Detaljerad information om utlösare finns [Pipeline körning och utlösare](concepts-pipeline-execution-triggers.md#triggers).
