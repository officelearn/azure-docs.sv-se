---
title: Skapa utlösande fönsterutlösare i Azure Data Factory
description: Lär dig hur du skapar en utlösare i Azure Data Factory som kör en pipeline i ett tumlande fönster.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 2a634c81273c26722d53610a13e362e5e453f7e9
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380117"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Skapa en utlösare som kör en pipeline på ett rullande fönster
Den här artikeln innehåller steg för att skapa, starta och övervaka en utlösare av ett tumlande fönster. Allmän information om utlösare och typer som stöds finns i [Pipeline-körning och utlösare](concepts-pipeline-execution-triggers.md).

Utlösare för rullande fönster är en typ av utlösare som går igång med jämna tidsintervall från en angiven starttid och behåller sitt tillstånd. Rullande fönster är en serie sammanhängande tidsintervall med fast storlek som inte överlappar. En utlösare för tumlande fönster har en 1:1-relation med en pipeline och kan bara referera till en enstaka pipeline.

## <a name="data-factory-ui"></a>Data Factory-användargränssnitt

1. Om du vill skapa en utlösare för ett tumlande fönster i datafabriksgränssnittet markerar du fliken **Utlösare** och väljer sedan **Ny**. 
1. När konfigurationsfönstret för utlösare har öppnats väljer du **Tumlande fönster**och definierar sedan utlösaregenskaperna för tumlande fönster. 
1. När du är klar väljer du **Spara**.

![Skapa en utlösare för ett tumlande fönster i Azure-portalen](media/how-to-create-tumbling-window-trigger/create-tumbling-window-trigger.png)

## <a name="tumbling-window-trigger-type-properties"></a>Egenskaper för utlösande text för tumlande fönster

Ett tumlande fönster har följande egenskaper för utlösartyp:

```
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": "<<Started/Stopped/Disabled - readonly>>",
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": "<<datetime>>",
            "endTime: <<datetime – optional>>,
            "delay": <<timespan – optional>>,
            "maxConcurrency": <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                "intervalInSeconds": <<int>>,
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan – optional>>,
                    "referenceTrigger": {
                        "referenceName": "MyTumblingWindowDependency1",
                        "type": "TriggerReference"
                    }
                },
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan>>
                }
            ]
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

Följande tabell innehåller en översikt på hög nivå över de större JSON-element som är relaterade till upprepning och schemaläggning av en utlösare av ett tumlande fönster:

| JSON-element | Beskrivning | Typ | Tillåtna värden | Krävs |
|:--- |:--- |:--- |:--- |:--- |
| **Typ** | Typ av utlösare. Typen är det fasta värdet "TumblingWindowTrigger". | Sträng | "TumlandeWindowTrigger" | Ja |
| **runtimeState (runtimeState)** | Det aktuella tillståndet för utlösarkörningstiden.<br/>**Obs:** Det \<här elementet läss i>. | Sträng | "Startad", "Stoppad", "Inaktiverad" | Ja |
| **Frekvens** | En sträng som representerar frekvensenheten (minuter eller timmar) vid vilken utlösaren återkommer. Om **startTime-datumvärdena** är mer detaljerade än **frekvensvärdet** beaktas **startTime-datumen** när fönstergränserna beräknas. Om **frekvensvärdet** till exempel är per timme och **startTime-värdet** är 2017-09-01T10:10:10Z, är det första fönstret (2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z). | Sträng | "minut", "timme"  | Ja |
| **Intervall** | Ett positivt heltal som anger intervallet för värdet för **frequency** och som avgör hur ofta utlösaren körs. Om **intervallet** till exempel är 3 och **frekvensen** är "timme" återkommer utlösaren var tredje timme. <br/>**Minsta**fönsterintervall är 5 minuter. | Integer | Ett positivt heltal. | Ja |
| **startTime**| Den första förekomsten, som kan vara i det förflutna. Det första utlösarintervallet är **(startTime**, **startTime-intervall** + **interval**). | DateTime | Ett DateTime-värde. | Ja |
| **endTime**| Den sista förekomsten, som kan vara i det förflutna. | DateTime | Ett DateTime-värde. | Ja |
| **Försening** | Hur lång tid det ska ta att fördröja starten av databearbetningen för fönstret. Pipelinekörningen startas efter den förväntade körningstiden plus **fördröjningen.** **Fördröjningen** definierar hur länge utlösaren väntar förbi förfallna tiden innan du utlöser en ny körning. **Fördröjningen** ändrar inte fönstret **startTime**. Ett **fördröjningsvärde** på 00:10:00 innebär till exempel en fördröjning på 10 minuter. | Tidsintervall<br/>(hh:mm:ss)  | Ett tidsintervallvärde där standardvärdet är 00:00:00. | Inga |
| **maxKonkurrens** | Antalet samtidiga utlösarkörningar som utlöses för fönster som är klara. Till exempel, för att backa fylla varje timme körs för igår resulterar i 24 fönster. Om **maxConcurrency** = 10 utlöses utlösarhändelser endast för de första 10 fönstren (00:00-01:00 - 09:00-10:00). När de första 10 utlösta pipeline-körningarna har slutförts utlöses utlösarkörningar för nästa 10 fönster (10:00-11:00 - 19:00-20:00). Fortsätter med detta exempel **på maxConcurrency** = 10, om det finns 10 fönster redo, det finns 10 totala pipeline körningar. Om det bara finns ett fönster klart, finns det bara en pipeline körning. | Integer | Ett heltal mellan 1 och 50. | Ja |
| **retryPolicy: Antal** | Antalet återförsök innan pipelinekörningen markeras som "Misslyckades".  | Integer | Ett heltal, där standardvärdet är 0 (inga försök). | Inga |
| **retryPolicy: intervalInSeconds** | Fördröjningen mellan försök till återförsök som anges i sekunder. | Integer | Antalet sekunder, där standardvärdet är 30. | Inga |
| **dependsOn: typ** | Typ av TumblingWindowTriggerReference. Krävs om ett samband har angetts. | Sträng |  "TumblingWindowTriggerBeroendeReference", "SelfDependencyTumblingWindowTriggerReference" | Inga |
| **dependsOn: storlek** | Storleken på beroendet tumlande fönster. | Tidsintervall<br/>(hh:mm:ss)  | Ett positivt tidsintervallvärde där standardvärdet är fönsterstorleken för den underordnade utlösaren  | Inga |
| **dependsOn: offset** | Förskjutning av beroendeutlösaren. | Tidsintervall<br/>(hh:mm:ss) |  Ett tidsintervallvärde som måste vara negativt i ett självberoende. Om inget värde anges är fönstret samma som själva utlösaren. | Självberoende: Ja<br/>Övrigt: Nej  |

### <a name="windowstart-and-windowend-system-variables"></a>Systemvariabler för WindowStart och WindowEnd

Du kan använda systemvariablerna **WindowStart** och **WindowEnd** för utlösaren för tumlande fönster i **pipelinedefinitionen** (det vill säga för en del av en fråga). Skicka systemvariablerna som parametrar till pipelinen i **utlösardefinitionen.** I följande exempel visas hur du skickar dessa variabler som parametrar:

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

Om du vill använda systemvariabelvärdena **WindowStart** och **WindowEnd** i pipelinedefinitionen använder du parametrarna "MyWindowStart" och "MyWindowEnd" i enlighet med detta.

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>Körningsordning för fönster i ett återfyllningsscenario
När det finns flera fönster upp för körning (särskilt i ett backfill-scenario) är körningsordningen för Windows deterministisk, från äldsta till nyaste intervall. Det här beteendet kan för närvarande inte ändras.

### <a name="existing-triggerresource-elements"></a>Befintliga TriggerResource-element
Följande punkter gäller för befintliga **TriggerResource-element:**

* Om värdet för **frekvenselementet** (eller fönsterstorleken) för utlösaren ändras återställs *inte* tillståndet för de fönster som redan har bearbetats. Utlösaren fortsätter att avfyras för fönstren från det senaste fönstret som den körde med hjälp av den nya fönsterstorleken.
* Om värdet för **endTime-elementet** för utlösaren ändras (läggs till eller uppdateras) återställs *inte* tillståndet för de fönster som redan har bearbetats. Utlösaren hedrar det nya **endTime-värdet.** Om det nya **endTime-värdet** är före de fönster som redan har körts, stoppas utlösaren. Annars stoppas utlösaren när det nya **endTime-värdet** påträffas.

### <a name="tumbling-window-trigger-dependency"></a>Utlösa beroende av utlösande fönster i Tumbling window

Om du vill vara säker på att en utlösare för tumlande fönster körs först efter att en annan utlösare för ett tumlande fönster har genomförts i datafabriken [skapar du ett utlösande beroende](tumbling-window-trigger-dependency.md)av utlösande fönster . 

## <a name="sample-for-azure-powershell"></a>Exempel för Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

I det här avsnittet visas hur du använder Azure PowerShell för att skapa, starta och övervaka en utlösare.

1. Skapa en JSON-fil med namnet **MyTrigger.json** i mappen C:\ADFv2QuickStartPSH\ med följande innehåll:

    > [!IMPORTANT]
    > Innan du sparar JSON-filen anger du värdet **för startTime-elementet** på den aktuella UTC-tiden. Ange värdet för **endTime-elementet** till en timme efter den aktuella UTC-tiden.

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

2. Skapa en utlösare med hjälp av cmdleten **Set-AzDataFactoryV2Trigger:**

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
    
3. Bekräfta att utlösarens status **stoppas** med hjälp av cmdleten **Get-AzDataFactoryV2Trigger:**

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Starta utlösaren med hjälp av cmdleten **Start-AzDataFactoryV2Trigger:**

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Bekräfta att utlösarens status är **Startad** med hjälp av cmdleten **Get-AzDataFactoryV2Trigger:**

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6. Hämta utlösaren körs i Azure PowerShell med hjälp av cmdleten **Get-AzDataFactoryV2TriggerRun.** Om du vill hämta information om utlösarkörningarna kör du följande kommando med jämna mellanrum. Uppdatera **triggerrunstartedafter** och **TriggerRunStartedBefore** värdena för att matcha värdena i utlösardefinitionen:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
Information om hur du övervakar utlösarkörningar och pipeline-körningar i Azure-portalen finns i [Övervaka pipelinekörningar](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="next-steps"></a>Nästa steg

* Detaljerad information om utlösare finns i [Pipeline-körning och utlösare](concepts-pipeline-execution-triggers.md#triggers).
* [Skapa ett beroende för utlösare för rullande fönster](tumbling-window-trigger-dependency.md)
