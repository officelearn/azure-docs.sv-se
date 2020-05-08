---
title: Skapa rullande Window triggers i Azure Data Factory
description: Lär dig hur du skapar en utlösare i Azure Data Factory som kör en pipeline i ett rullande-fönster.
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
ms.openlocfilehash: ed7b01fb83ebd0c494f3f0f06a28dbf4e98c0b2d
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2020
ms.locfileid: "82592095"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Skapa en utlösare som kör en pipeline på ett rullande fönster
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln innehåller steg för att skapa, starta och övervaka en utlösare för rullande fönster. Allmän information om utlösare och de typer som stöds finns i [pipeline-körning och utlösare](concepts-pipeline-execution-triggers.md).

Utlösare för rullande fönster är en typ av utlösare som går igång med jämna tidsintervall från en angiven starttid och behåller sitt tillstånd. Rullande fönster är en serie sammanhängande tidsintervall med fast storlek som inte överlappar. En utlösare för rullande fönster har en en-till-en-relation med en pipeline och kan bara referera till en singular pipeline.

## <a name="data-factory-ui"></a>Data Factory-användargränssnitt

1. Om du vill skapa en utlösare för rullande fönster i Data Factory användar gränssnitt väljer du fliken **utlösare** och väljer sedan **ny**. 
1. När fönstret utlösarens konfiguration öppnas väljer du **fönstret rullande**och definierar sedan utlösarens egenskaper för rullande window. 
1. När du är klar väljer du **Spara**.

![Skapa en utlösare för rullande fönster i Azure Portal](media/how-to-create-tumbling-window-trigger/create-tumbling-window-trigger.png)

## <a name="tumbling-window-trigger-type-properties"></a>Rullande fönster utlösarens typ egenskaper

Ett rullande-fönster har följande egenskaper för utlösnings typ:

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

Följande tabell innehåller en översikt över de viktigaste JSON-elementen som är relaterade till upprepning och schemaläggning av en utlösare för rullande fönster:

| JSON-element | Beskrivning | Typ | Tillåtna värden | Krävs |
|:--- |:--- |:--- |:--- |:--- |
| **bastyp** | Typ av utlösare. Typen är det fasta värdet "TumblingWindowTrigger". | Sträng | "TumblingWindowTrigger" | Ja |
| **runtimeState** | Det aktuella läget för utlösarens körnings tid.<br/>**Obs**: det här elementet \<är skrivskyddat>. | Sträng | "Startat", "stoppad", "inaktive rad" | Ja |
| **frekvens** | En sträng som representerar frekvens enheten (minuter eller timmar) då utlösaren upprepas. Om värdena för **StartTime** -datum är mer detaljerade än **frekvens** svärdet beaktas **StartTime** -datum när fönster gränserna beräknas. Om värdet för **frekvens** till exempel är per timme och **StartTime** -värdet är 2017-09-01T10:10:10Z, är det första fönstret (2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z). | Sträng | "minut", "timme"  | Ja |
| **intervall** | Ett positivt heltal som anger intervallet för värdet för **frequency** och som avgör hur ofta utlösaren körs. Om **intervallet** till exempel är 3 och **frekvensen** är "timme" upprepas utlösaren var 3: e timme. <br/>**Obs!** det minsta fönster intervallet är 5 minuter. | Integer | Ett positivt heltal. | Ja |
| **startTime**| Den första förekomsten, som kan vara i det förflutna. Det första **Utlösar intervallet är** + (**StartTime**,**Interval**). | DateTime | Ett DateTime-värde. | Ja |
| **endTime**| Den sista förekomsten, som kan vara i det förflutna. | DateTime | Ett DateTime-värde. | Ja |
| **förskjutning** | Hur lång tid det tar att fördröja starten av data bearbetningen för fönstret. Pipeline-körningen startas efter den förväntade körnings tiden plus **fördröjnings**mängden. **Fördröjningen** definierar hur länge utlösaren ska vänta efter förfallo tiden innan en ny körning utlöses. **Fördröjningen** ändrar inte fönstret **StartTime**. Till exempel innebär ett **fördröjnings** värde på 00:10:00 en fördröjning på 10 minuter. | Tidsintervall<br/>(hh: mm: SS)  | Ett TimeSpan-värde där standardvärdet är 00:00:00. | Inga |
| **maxConcurrency** | Antal körningar av samtidiga utlösare som utlöses för Windows som är klara. Om du till exempel vill köra en hel timmes körning i igår resulterar det i 24 fönster. Om **maxConcurrency** = 10, utlöses Utlös ande händelser endast för de första 10 windows (00:00-01:00-09:00-10:00). När de första 10 utlösta pipeline-körningarna har slutförts utlöses utlösare för nästa 10 Windows (10:00-11:00-19:00-20:00). Om du fortsätter med det här exemplet på **maxConcurrency** = 10, om det finns 10 Windows Ready, så finns det 10 totala pipelinen körs. Om det bara är ett fönster som är klart finns det bara en pipeline-körning. | Integer | Ett heltal mellan 1 och 50. | Ja |
| **retryPolicy: antal** | Antalet återförsök innan pipeline-körningen har marker ATS som "misslyckades".  | Integer | Ett heltal där standardvärdet är 0 (inga återförsök). | Inga |
| **retryPolicy: intervalInSeconds** | Fördröjningen mellan återförsök som anges i sekunder. | Integer | Antalet sekunder, där standardvärdet är 30. | Inga |
| **dependsOn: typ** | Typ av TumblingWindowTriggerReference. Krävs om ett beroende har angetts. | Sträng |  "TumblingWindowTriggerDependencyReference", "SelfDependencyTumblingWindowTriggerReference" | Inga |
| **dependsOn: storlek** | Storleken på fönstret beroende rullande. | Tidsintervall<br/>(hh: mm: SS)  | Ett positivt TimeSpan-värde där standardvärdet är fönster storleken för den underordnade utlösaren  | Inga |
| **dependsOn: förskjutning** | Offset för beroende utlösare. | Tidsintervall<br/>(hh: mm: SS) |  Ett TimeSpan-värde som måste vara negativt i ett själv-beroende. Om inget värde anges är fönstret detsamma som själva utlösaren. | Själv-beroende: Ja<br/>Övrigt: Nej  |

> [!NOTE]
> När en utlösare för rullande fönster har publicerats kan **intervall** och **frekvens** inte redige ras.

### <a name="windowstart-and-windowend-system-variables"></a>WindowStart-och WindowEnd-systemvariabler

Du kan använda systemvariablerna **WindowStart** och **WindowEnd** i utlösaren rullande Window i din **pipeline** -definition (det vill säga för en del av en fråga). Skicka systemvariablerna som parametrar till din pipeline i **utlösnings** definitionen. I följande exempel visas hur du skickar dessa variabler som parametrar:

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

Använd parametrarna "MyWindowStart" och "MyWindowEnd" för att använda system variabel värden **WindowStart** och **WindowEnd** i pipeline-definitionen.

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>Körnings ordning för Windows i ett bakfyllnings scenario
När det finns flera fönster för körning (särskilt i ett bakfyllnings scenario) är ordningen för körningen av Windows deterministisk, från äldsta till nyaste intervall. Det här beteendet kan för närvarande inte ändras.

### <a name="existing-triggerresource-elements"></a>Befintliga TriggerResource-element
Följande punkter gäller för befintliga **TriggerResource** -element:

* Om värdet för **frekvens** elementet (eller fönster storleken) för utlösaren ändras, återställs *inte* statusen för de fönster som redan bearbetas. Utlösaren fortsätter att utlösa för Windows från det sista fönstret som den kördes med den nya fönster storleken.
* Om värdet för slut punkts elementet i utlösaren ändras (tillagt eller uppdaterat) återställs *inte* statusen **för de fönster** som redan har bearbetats. Utlösaren följer **det nya slut** tid svärdet. Om **det nya slut** tid svärdet är före det fönster som redan har körts stoppas utlösaren. Annars stoppas utlösaren när det **nya slut** tid svärdet påträffas.

### <a name="tumbling-window-trigger-dependency"></a>Rullande Window utlöser beroende

Om du vill vara säker på att en utlösare för rullande fönster bara körs när en annan utlösare för rullande fönster har körts i data fabriken [skapar du ett rullande Window-utlösare-beroende](tumbling-window-trigger-dependency.md). 

## <a name="sample-for-azure-powershell"></a>Exempel för Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Det här avsnittet visar hur du använder Azure PowerShell för att skapa, starta och övervaka en utlösare.

1. Skapa en JSON-fil med namnet **untrigger. JSON** i mappen C:\ADFv2QuickStartPSH\ med följande innehåll:

    > [!IMPORTANT]
    > Innan du sparar JSON-filen ställer du in värdet för **StartTime** -elementet på den aktuella UTC-tiden. Ange värdet för slut tids **elementet till** en timme efter den aktuella UTC-tiden.

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

2. Skapa en utlösare med hjälp av cmdleten **set-AzDataFactoryV2Trigger** :

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
    
3. Bekräfta att statusen för utlösaren har **stoppats** med cmdleten **Get-AzDataFactoryV2Trigger** :

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Starta utlösaren med cmdleten **Start-AzDataFactoryV2Trigger** :

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Bekräfta att statusen för utlösaren har **startats** med hjälp av cmdleten **Get-AzDataFactoryV2Trigger** :

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6. Hämta utlösaren körs i Azure PowerShell med hjälp av cmdleten **Get-AzDataFactoryV2TriggerRun** . Om du vill hämta information om utlösaren kör du följande kommando med jämna mellanrum. Uppdatera värdena för **TriggerRunStartedAfter** och **TriggerRunStartedBefore** så att de matchar värdena i din utlösnings definition:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
Om du vill övervaka utlösarens körningar och pipelines körs i Azure Portal, se [övervaka pipelines körs](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="next-steps"></a>Nästa steg

* Detaljerad information om utlösare finns i [pipeline-körning och utlösare](concepts-pipeline-execution-triggers.md#trigger-execution).
* [Skapa ett beroende för utlösare för rullande fönster](tumbling-window-trigger-dependency.md)
