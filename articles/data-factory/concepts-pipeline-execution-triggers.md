---
title: Pipeline-körning och utlösare i Azure Data Factory | Microsoft Docs
description: Den här artikeln innehåller information om hur du kör en pipeline i Azure Data Factory på begäran eller genom att skapa en utlösare.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/03/2018
ms.author: shlo
ms.openlocfilehash: 08fcc2eec1914d9f7535ea66d33045240452e2a6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="pipeline-execution-and-triggers-in-azure-data-factory"></a>Pipeline-körning och utlösare i Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of the Data Factory service that you're using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-scheduling-and-execution.md)
> * [Version 2 – förhandsversion](concepts-pipeline-execution-triggers.md)

En _pipelinekörning_ i Azure Data Factory Version 2 definierar en instans av en pipeline-åtgärd. Anta till exempel att du har en pipeline som körs kl. 8.00, 9.00 och 10.00. Det skulle i så fall vara tre separata körningar av pipelinen, eller pipelinekörningar. Varje pipelinekörning har ett unikt pipelinekörnings-ID. Ett körnings-ID är ett GUID som unikt definierar den specifika pipelinekörningen. 

Pipelinekörningar instansieras normalt genom att skicka argument till parametrar som du definierar i pipelinen. Du kan köra en pipeline antingen manuellt eller via en _utlösare_. Den här artikeln innehåller information om båda körningssätten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Azure Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Azure Data Factory, som är allmänt tillgänglig, kan du läsa om [schemaläggning och körning i version 1 av Azure Data Factory](v1/data-factory-scheduling-and-execution.md).

## <a name="manual-execution-on-demand"></a>Manuell körning (på begäran)
En manuell körning av en pipeline kallas även för en körning _på begäran_.

Anta till exempel att du har en grundläggande pipeline som heter **copyPipeline**, som du vill köra. Pipelinen har en enda aktivitet som kopierar från en källmapp i Azure Blob Storage till en målmapp i samma lagringsutrymme. Följande JSON-definition visar det här pipelineexemplet:

```json
{
  "name": "copyPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "name": "CopyBlobtoBlob",
        "inputs": [
          {
            "referenceName": "sourceBlobDataset",
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sinkBlobDataset",
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      }
    }
  }
}
```

Pipelinen har två parametrar i JSON-definitionen: **sourceBlobContainer** och **sinkBlobContainer**. Du skickar värden till parametrarna vid körning.

Du kan köra din pipeline manuellt med hjälp av en av följande metoder:
- .NET SDK
- Azure PowerShell-modul
- REST-API
- Python SDK

### <a name="rest-api"></a>REST-API
Kommandot i följande exempel visar hur du kör din pipeline manuellt med hjälp av REST-API:t:  

```
POST
https://management.azure.com/subscriptions/mySubId/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory/pipelines/copyPipeline/createRun?api-version=2017-03-01-preview
```

Ett mer komplett exempel finns i [Snabbstart: skapa en datafabrik med hjälp av REST API](quickstart-create-data-factory-rest-api.md).

### <a name="azure-powershell"></a>Azure PowerShell
Kommandot i följande exempel visar hur du kör din pipeline manuellt med hjälp av Azure PowerShell:

```powershell
Invoke-AzureRmDataFactoryV2Pipeline -DataFactory $df -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
```

Du skickar parametrar i texten i nyttolasten för begäran. I .NET SDK, Azure PowerShell och Python SDK kan du skicka värden i en ordlista som skickas som ett argument i anropet:

```json
{
  “sourceBlobContainer”: “MySourceFolder”,
  “sinkBlobCountainer”: “MySinkFolder”
}
```

Svarsnyttolasten är ett unikt ID för pipeline-körningen:

```json
{
  "runId": "0448d45a-a0bd-23f3-90a5-bfeea9264aed"
}
```

Ett mer komplett exempel finns i [Snabbstart: skapa en datafabrik med hjälp av Azure PowerShell](quickstart-create-data-factory-powershell.md).

### <a name="net-sdk"></a>.NET SDK
Anropet i följande exempel visar hur du kör din pipeline manuellt med hjälp av .NET SDK:

```csharp
client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters)
```

Ett mer komplett exempel finns i [Snabbstart: skapa en datafabrik med hjälp av .NET SDK](quickstart-create-data-factory-dot-net.md).

> [!NOTE]
> Du kan använda .NET SDK till att anropa Data Factory-pipeliner från Azure Functions, från dina egna webbtjänster och så vidare.

<h2 id="triggers">Körning via utlösare</h2>
Du kan även köra en pipelinekörning med utlösare. Utlösare representerar en bearbetningsenhet som avgör när en pipeline-körning måste startas. Data Factory har för närvarande stöd för två typer av utlösare:

- Schemautlösare: en utlösare som anropar en pipeline enligt ett tidsschema.
- Utlösare för rullande fönster: en utlösare som körs med ett regelbundet intervall och samtidigt bibehåller ett tillstånd. Azure Data Factory har för närvarande inte stöd för händelsebaserade utlösare. Exempelvis stöds inte en utlösare för en pipeline som körs när en fil ankommer.

Pipeliner och utlösare har en många-till-många-relation. Flera utlösare kan starta en enda pipeline, och en enda utlösare kan starta flera pipeliner. I följande utlösardefinition refererar **pipelines**-egenskapen till en lista med pipeliner som utlöses av den aktuella utlösaren. Egenskapsdefinition innehåller värden för pipelineparametrarna.

### <a name="basic-trigger-definition"></a>Grundläggande utlösardefinition

```json
    "properties": {
        "name": "MyTrigger",
        "type": "<type of trigger>",
        "typeProperties": {
            …
        },
        "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                          "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
            }
        ]
    }
```

## <a name="schedule-trigger"></a>Schemautlösare
En schemautlösare kör pipeliner enligt ett tidsschema. Den här utlösaren har stöd för periodiska och avancerade kalenderalternativ. Utlösaren har till exempel stöd för intervall som ”varje vecka” eller ”måndagar kl. 17.00 och torsdagar kl. 21:00”. Schemautlösaren är flexibel eftersom datamängdsmönstret är oberoende och utlösaren inte gör skillnad mellan tidsseriedata och andra typer av data.

Mer information om schemautlösare och exempel hittar du i [Skapa en schemautlösare](how-to-create-schedule-trigger.md).

## <a name="tumbling-window-trigger"></a>Utlösare för rullande fönster
Utlösare för rullande fönster är en typ av utlösare som går igång med jämna tidsintervall från en angiven starttid och behåller sitt tillstånd. Rullande fönster är en serie sammanhängande tidsintervall med fast storlek som inte överlappar. Mer information om utlösare för rullande fönster och exempel finns i [Skapa en utlösare för rullande fönster](how-to-create-tumbling-window-trigger.md).

## <a name="schedule-trigger-definition"></a>Definition av schemautlösare
När du skapar en schemautlösare anger du schemaläggning och upprepning med en JSON-definition. 

Om du vill att schemautlösaren ska starta en pipelinekörning tar du med en pipelinereferens till den aktuella pipelinen i utlösardefinitionen. Pipeliner och utlösare har en många-till-många-relation. Flera utlösare kan starta en pipeline. En enskild utlösare kan starta flera pipeliner.

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Year>>,
        "interval": <<int>>,             // How often to fire
        "startTime": <<datetime>>,
        "endTime": <<datetime>>,
        "timeZone": "UTC"
        "schedule": {                    // Optional (advanced scheduling specifics)
          "hours": [<<0-24>>],
          "weekDays": ": [<<Monday-Sunday>>],
          "minutes": [<<0-60>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurences": [
               {
                    "day": <<Monday-Sunday>>,
                    "occurrence": <<1-5>>
               }
           ] 
        }
      }
    },
   "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                        "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
           }
      ]
  }
}
```

> [!IMPORTANT]
> Egenskapen **parameters** är en obligatorisk egenskap i elementet **pipelines**. Om din pipeline inte tar emot några parametrar måste du ta med en JSON-definition för egenskapen **parameters**.

### <a name="schema-overview"></a>Schemaöversikt
I följande tabell ges en översikt över de viktigaste schemaelementen relaterade till upprepning och schemaläggning i en utlösare:

| JSON-egenskap | Beskrivning |
|:--- |:--- |
| **startTime** | Ett datum/tid-värde. För grundläggande scheman gäller värdet för egenskapen **startTime** den första förekomsten. För komplexa scheman startar utlösaren tidigast vid det angivna värdet för **startTime**. |
| **endTime** | Slutdatum och tidpunkt för utlösaren. Utlösaren körs inte efter angivet slutdatum och sluttid. Värdet för egenskapen kan inte ha passerat. <!-- This property is optional. --> |
| **timeZone** | Tidszonen. För närvarande stöds bara tidszonen UTC. |
| **recurrence** | Ett upprepningsobjekt som anger upprepningsregler för utlösaren. Upprepningsobjektet har stöd för elementen **frequency** (frekvens), **interval** (intervall), **endTime** (sluttid), **count** (antal) och **schedule** (schema). När du definierar ett upprepningsobjekt är elementet **frequency** obligatoriskt. De andra elementen är valfria. |
| **frequency** | Frekvensen som utlösaren ska upprepas med. Du kan använda värden som ”minute”, ”hour”, ”day”, ”week” och ”month”. |
| **interval** | Ett positivt heltal som anger intervallet för värdet för **frequency**. Värdet **frequency** bestämmer hur ofta utlösaren ska köras. Om **interval** till exempel är 3 och **frequency** är ”week” (vecka) upprepas utlösaren var tredje vecka. |
| **schedule** | Upprepningsschemat för utlösaren. En utlösare med ett angivet värde för **frequency** ändrar sin upprepning baserat på ett upprepningsschema. Egenskapen **schedule** innehåller ändringar för upprepningen som baseras på minuter, timmar, veckodagar, dagar i månaden och veckonummer.

### <a name="schedule-trigger-example"></a>Exempel på schemaläggning av utlösare

```json
{
    "properties": {
        "name": "MyTrigger",
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Hour",
                "interval": 1,
                "startTime": "2017-11-01T09:00:00-08:00",
                "endTime": "2017-11-02T22:00:00-08:00"
            }
        },
        "pipelines": [{
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "SQLServerToBlobPipeline"
                },
                "parameters": {}
            },
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "SQLServerToAzureSQLPipeline"
                },
                "parameters": {}
            }
        ]
    }
}
```

### <a name="schema-defaults-limits-and-examples"></a>Standardvärden för scheman, begränsningar och exempel

| JSON-egenskap | Typ | Krävs | Standardvärde | Giltiga värden | Exempel |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** | sträng | Ja | Ingen | ISO 8601 datum/tid | `"startTime" : "2013-01-09T09:30:00-08:00"` |
| **recurrence** | objekt | Ja | Inget | Ett upprepningsobjekt | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **interval** | nummer | Nej | 1 | 1 till 1000 | `"interval":10` |
| **endTime** | sträng | Ja | Inget | Ett datum/tid-värde som representerar en tidpunkt i framtiden | `"endTime" : "2013-02-09T09:30:00-08:00"` |
| **schedule** | objekt | Nej | Ingen | Ett schemaobjekt | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="starttime-property"></a>Egenskapen startTime
I följande tabell visas hur egenskapen **startTime** styr körningen av en utlösare:

| startTime-värde | Upprepning utan schema | Upprepning med schema |
|:--- |:--- |:--- |
| **Starttiden är i det förflutna** | Beräknar första framtida körningstid efter starttiden och körs vid den tidpunkten.<br /><br />Utför efterföljande körningar baserat på beräkningar från senaste körningstid.<br /><br />Se exemplet som följer den här tabellen. | Utlösaren startar _tidigast_ den angivna starttiden. Den första förekomsten baseras på schemat som beräknas från starttiden.<br /><br />Kör efterföljande körningar baserat på upprepningsschemat. |
| **Starttiden är i framtiden eller aktuell tid** | Körs en gång på angiven starttid.<br /><br />Utför efterföljande körningar baserat på beräkningar från senaste körningstid. | Utlösaren startar _tidigast_ den angivna starttiden. Den första förekomsten baseras på schemat som beräknas från starttiden.<br /><br />Kör efterföljande körningar baserat på upprepningsschemat. |

Låt oss se vad som händer när starttiden har passerat, med upprepning men utan schema. Anta att den aktuella tiden är 2017-04-08 13:00, starttiden är 2017-04-07 14:00 och upprepningen är varannan dag. (**Upprepningen** definieras genom att du sätter egenskapen **frequency** till ”day” och egenskapen **interval** till 2.) Observera att värdet för **startTime** har passerat och inträffat före aktuell tid.

Med dessa villkor sker den första körningen 2017-04-09 kl. 14:00. Scheduler-motor beräknar körningsförekomster från starttiden. Alla tidigare instanser ignoreras. Motorn använder nästa förekomst som förekommer i framtiden. I det här scenariot är starttiden 2017-04-07 kl. 02:00. Nästa instans är två dagar från den tiden, vilket är 2017-04-09 kl. 02:00.

Den första körningstiden är samma även om **startTime** är 2017-04-05 14:00 eller 2017-04-01 14:00. Efter den första körningen beräknas efterföljande körningar med hjälp av schemat. Därför utförs efterföljande körningar 2017-04-11 kl. 14:00, och sedan 2017-04-13 kl. 14:00, sedan 2017-04-15 kl. 14:00 och så vidare.

När inga timmar eller minuter anges i schemat för en utlösare används som standard samma timmar och minuter som i den första körningen.

### <a name="schedule-property"></a>Egenskapen schedule
Du kan använda ett **schema** för att *begränsa* antalet utlösarkörningar. Om en utlösare med månatlig frekvens till exempel har ett schema som bara körs dag 31 så körs utlösaren bara de månader som har en 31:a dag.

Du kan även använda ett **schema** för att *utöka* antalet utlösarkörningar. En utlösare med månatlig frekvens som har schemalagts för att köras dag 1 och 2 körs den första och andra dagen i månaden snarare än en gång i månaden.

Om flera **schemaelement** anges är utvärderingsordningen från den största till minsta schemainställningen – veckonummer, dag i månaden, veckodag, timme och minut.

I följande tabell beskrivs **schedule**-elementen i detalj:

| JSON-element | Beskrivning | Giltiga värden |
|:--- |:--- |:--- |
| **minutes** | Minuter för den timme då utlösaren körs. |– Heltal<br />– Heltalsmatris|
| **hours** | Timmar på dagen då utlösaren körs. |– Heltal<br />– Heltalsmatris|
| **weekDays** | Veckodagar då utlösaren körs. Värdet kan bara anges med en veckofrekvens.|<br />– Måndag<br />– Tisdag<br />– Onsdag<br />– Torsdag<br />– Fredag<br />– Lördag<br />– Söndag<br />– Matris med dagvärden (maximal matrisstorlek är 7)<br /><br />Dagvärdena är inte skiftlägeskänsliga|
| **monthlyOccurrences** | Dagar i månaden som utlösaren körs på. Värdet kan bara anges med en månadsfrekvens. |– Matris med **monthlyOccurence-objekt**: `{ "day": day,  "occurrence": occurence }`<br />– Attributet **day** är veckodagen som utlösaren körs på. Om egenskapen **monthlyOccurrences** till exempel har **day**-värdet `{Sunday}` innebär det varje söndag i månaden. Attributet **day** är obligatoriskt.<br />– Attributet **occurrence** är förekomsten av **day**-värdet i månaden. Om egenskapen **monthlyOccurrences** till exempel har **day**- och **occurrence**-värdena `{Sunday, -1}` innebär det den sista söndagen i månaden. Attributet **occurrence** är valfritt.|
| **monthDays** | Dagar i månaden som utlösaren körs på. Värdet kan bara anges med en månadsfrekvens. |– Ett värde <= -1 och >= -31<br />– Ett värde > = 1 och < = 31<br />– Matris med värden|

## <a name="examples-of-trigger-recurrence-schedules"></a>Exempel på scheman för upprepning av utlösare
Det här avsnittet innehåller exempel på återkommande scheman. Det fokuserar på de objektet **schema** och dess element.

I exemplen antas att värdet för **interval** är 1 och att värdet för **frequency** är giltigt enligt schemadefinitionen. Du kan till exempel inte använda **frequency**-värdet ”day” och samtidigt ha en **monthDays**-modifiering i **schedule**-objektet. Dessa typer av begränsningar beskrivs i tabellen i föregående avsnitt.

| Exempel | Beskrivning |
|:--- |:--- |
| `{"hours":[5]}` | Kör kl. 05.00 varje dag. |
| `{"minutes":[15], "hours":[5]}` | Kör kl. 05.15 varje dag. |
| `{"minutes":[15], "hours":[5,17]}` | Kör kl. 05.15 och 17.15 varje dag |
| `{"minutes":[15,45], "hours":[5,17]}` | Kör kl. 05.15, 5.45, 17.15 och 17.45 varje dag. |
| `{"minutes":[0,15,30,45]}` | Kör var 15:e minut. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Kör varje timme.<br /><br />Den här utlösaren körs varje timme. Minuterna styrs av **startTime**-värdet när du anger ett sådant. Om du inte anger något värde styrs minuterna av tiden för skapandet. Om starttiden eller skapandetiden (beroende på vilken som gäller) till exempel är 00.25 körs utlösaren 00.25, 01.25, 02.25, …, 23:25.<br /><br />Schemat motsvarar att ha en utlösare med **frequency**-värdet ”hour” (timme), **interval**-värdet 1 och inget **schema**. Det här schemat kan användas med andra värden för **frequency** och **interval** om du vill skapa andra utlösare. När värdet för **frequency** till exempel är ”month” körs schemat bara en gång i månaden, snarare än varje dag när värdet för **frequency** är ”day”. |
| `{"minutes":[0]}` | Körs varje hel timme.<br /><br />Den här utlösaren körs varje timma med början vid 00.00, 01.00, 02.00 och så vidare.<br /><br />Det här schemat motsvarar en utlösare med **frequency**-värdet ”hour” och **startTime**-värdet noll minuter, och inget **schema** men **frequency**-värdet ”day”. Om **frequency**-värdet är ”week” eller ”month” körs schemat en dag i veckan eller en dag i månaden. |
| `{"minutes":[15]}` | Körs 15 minuter efter varje hel timme.<br /><br />Den här utlösaren körs 15 minuter efter varje timme med början vid 00.15, 01.15, 02.15 och så vidare. |
| `{"hours":[17], "weekDays":["saturday"]}` | Körs 17.00 varje lördag. |
| `{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Körs 17.00 varje måndag, onsdag och fredag. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Körs 17.15 och 17.45 varje måndag, onsdag och fredag. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Körs var 15:e minut på vardagar. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Körs var 15:e minut på vardagar mellan 09.00 och 16.45. |
| `{"weekDays":["tuesday", "thursday"]}` | Körs varje tisdag och torsdag den angivna starttiden. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` | Körs 06.00 den 28:e dagen varje månad (förutsatt att **frequency**-värdet är ”month”). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Körs 06.00 den sista dagen i månaden.<br /><br />Om du vill köra en utlösare den sista dagen i månaden ska du använda -1 istället för dag 28, 29, 30 eller 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Körs 06.00 den första och sista dagen varje månad. |
| `{monthDays":[1,14]}` | Körs på den första och fjortonde dagen i varje månad på den angivna starttiden. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Körs den första fredagen i varje månad 05.00. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Körs den första fredagen i varje månad vid den angivna starttiden. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | Körs den tredje fredagen från slutet av månad, varje månad, vid den angivna starttiden. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Körs den första och sista fredagen i varje månad 05.15. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Körs den första och sista fredagen i varje månad vid den angivna starttiden. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Körs den femte fredagen i varje månad vid den angivna starttiden.<br /><br />När det inte finns någon femte fredag i månaden körs inte pipelinen. Om du vill köra utlösaren på den sista fredagen i månaden kan du använda -1 istället för 5 som värde för **occurrence**. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Körs var 15:e minut den sista fredagen i månaden. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Körs 05.15, 05.45, 17.15 och 17.45 den tredje onsdagen varje månad. |

## <a name="trigger-type-comparison"></a>Jämförelse av olika utlösare
Både utlösare för rullande fönster och schemautlösare körs vid specifika tidpunkter. Hur skiljer de sig åt?

I följande tabell ges en jämförelse av utlösare för rullande fönster och schemautlösare:

|  | Utlösare för rullande fönster | Schemautlösare |
|:--- |:--- |:--- |
| **Återfyllnadsscenarier** | Stöds. Pipelinekörningar kan schemaläggas för fönster i det förflutna. | Stöds ej. Pipelinekörningar kan bara köras i tidsperioder från nu och framöver. |
| **Tillförlitlighet** | 100 % tillförlitlighet. Pipelinekörningar kan schemaläggas i alla fönster från ett angivet startdatum utan luckor. | Mindre tillförlitligt. |
| **Återförsökskapacitet** | Stöds. Misslyckade pipelinekörningar har återförsöksprincipen 0 som standard, eller en policy som anges av användaren i utlösarens definition. Försöker köra pipelinen igen automatiskt när körningen misslyckas på grund av samtidighet/server/begränsningar (det vill säga statuskoderna 400: användarfel, 429: för många begäranden och 500: internt serverfel). | Stöds ej. |
| **Samtidighet** | Stöds. Användare kan uttryckligen ange samtidighetsgränser för utlösaren. Tillåter mellan 1 och 50 utlösta pipelinekörningar samtidigt. | Stöds ej. |
| **Systemvariabler** | Systemvariablerna **WindowStart** och **WindowEnd** kan användas. Användare kan komma åt `triggerOutputs().windowStartTime` och `triggerOutputs().windowEndTime` som systemvariabler för utlösaren i definitionen av utlösaren. Värdena används som start- respektive sluttid för fönstret. För en utlösare för rullande fönster som körs timme i fönstret 01.00 till 02.00 är till exempel definitionen `triggerOutputs().WindowStartTime = 2017-09-01T01:00:00Z` och `triggerOutputs().WindowEndTime = 2017-09-01T02:00:00Z`. | Stöds ej. |
| **Relation pipeline-till-utlösare** | Har stöd för en 1:1-relation. Endast en pipeline kan utlösas. | Har stöd för många:många-relationer. Flera utlösare kan starta en pipeline. En enskild utlösare kan starta flera pipeliner. | 

## <a name="next-steps"></a>Nästa steg
Se följande självstudiekurser:

- [Snabbstart: skapa en datafabrik med hjälp av .NET SDK](quickstart-create-data-factory-dot-net.md)
- [Skapa en schemautlösare](how-to-create-schedule-trigger.md)
- [Skapa en utlösare för rullande fönster](how-to-create-tumbling-window-trigger.md)
