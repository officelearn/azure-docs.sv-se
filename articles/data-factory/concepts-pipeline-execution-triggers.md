---
title: "Pipeline-körning och utlösare i Azure Data Factory | Microsoft Docs"
description: "Den här artikeln innehåller information om hur du kör en pipeline i Azure Data Factory på begäran eller genom att skapa en utlösare."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/03/2018
ms.author: shlo
ms.openlocfilehash: 88ae5dfbf6246ecf92d6528ad3d9a8e5fb57e4b0
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2018
---
# <a name="pipeline-execution-and-triggers-in-azure-data-factory"></a>Pipeline-körning och utlösare i Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-scheduling-and-execution.md)
> * [Version 2 – förhandsversion](concepts-pipeline-execution-triggers.md)

En **pipelinekörning** är en term i Azure Data Factory Version 2, som definierar en instans av en pipeline-åtgärd. Anta till exempel att du har en pipeline som körs kl. 8, 9 och 10. Det skulle i det här fallet vara tre s.k. pipeline-körningar. Varje pipeline-körning har en unik pipeline-körnings-ID, vilket är ett GUID som unikt definierar den specifika pipeline-körningen. Pipeline-körningar instansieras normalt genom att skicka argument till parametrar som definieras i pipelines. Det finns två sätt att köra en pipeline: **manuellt** eller via en **utlösare**. Den här artikeln innehåller information om båda körningssätten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig, läser du om [schemaläggning och körning i Data Factory V1](v1/data-factory-scheduling-and-execution.md).

## <a name="run-pipeline-on-demand"></a>Kör pipeline på begäran
I den här metoden kan köra din pipeline manuellt. Det ses även som på-begäran-körning av en pipeline.

Anta till exempel att du har en pipeline som heter **copyPipeline**, som du vill köra. Pipelinen är en enkel pipeline med en enda aktivitet som kopierar från en källmapp i Azure Blob Storage till en målmapp i samma lagringsutrymme. Här är ett exempel på pipeline-definition:

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
Pipelinen har två parametrar: sourceBlobContainer och sinkBlobContainer som visas i JSON-definitionen. Du skickar värden till parametrarna vid körning.

Om du vill köra pipelinen manuellt kan du använda något av följande sätt: .NET, PowerShell, REST och Python.

### <a name="rest-api"></a>REST-API
Här är ett exempel på REST-kommando:  

```
POST
https://management.azure.com/subscriptions/mySubId/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory/pipelines/copyPipeline/createRun?api-version=2017-03-01-preview
```
Se [Snabbstart: skapa en datafabrik med hjälp av REST API](quickstart-create-data-factory-rest-api.md) för ett fullständigt exempel.

### <a name="powershell"></a>PowerShell
Här är ett exempel på PowerShell-kommando:

```powershell
Invoke-AzureRmDataFactoryV2Pipeline -DataFactory $df -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
```

Du skickar parametrar i texten i nyttolasten för begäran. I .NET, Powershell och Python kan du skicka värden i en ordlista som skickas som ett argument till anropet.

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


Se [Snabbstart: skapa en datafabrik med hjälp av PowerShell](quickstart-create-data-factory-powershell.md) för ett fullständigt exempel.

### <a name="net"></a>.NET
Här är ett exempel på .NET-anrop:

```csharp
client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters)
```

Se [Snabbstart: skapa en datafabrik med hjälp av .NET](quickstart-create-data-factory-dot-net.md) för ett fullständigt exempel.

> [!NOTE]
> Du kan använda .NET-API för att anropa Data Factory-pipelines från Azure Functions egna webbtjänster osv.

## <a name="triggers"></a>Utlösare
Utlösare är det andra sättet att utföra en pipeline-körning. Utlösare representerar en bearbetningsenhet som avgör när en pipeline-körning måste startas. För närvarande stöder Data Factory två typer av utlösare: 1)**Scheduler-utlösaren**, en utlösare som anropar en pipeline på ett klocktidsschema (wall-clock) och 2) en **utlösare för rullande fönster**: för utlösare som körs med jämna intervall och behåller sitt tillstånd. För närvarande stöder inte Data Factory händelsebaserade utlösare, som en utlösare för en pipeline-körning av händelsen för en filankomst.

Pipeliner och utlösare har en många-till-många-relation. Flera utlösare kan starta en enskild pipeline och en enskild utlösare kan starta flera pipeliner. I följande JSON-definition för en utlösare refererar **pipelines**-egenskapen till en lista med pipelines som utlöses av en vissa utlösare, och värden för pipeline-parametrar.

### <a name="basic-trigger-definition"></a>Grundläggande utlösardefinition:
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
Schemautlösaren kör pipelines enligt ett klocktidsschema (wall-clock). Den här utlösaren stödjer periodiska och avancerade kalenderalternativ (varje vecka, måndag kl. 17 och torsdag kl. 9). Den är flexibel genom att den är datamönsteragnostisk, utan att skilja på tidsserie- och icke-tidsseriedata.

Mer detaljerad information om schemautlösare och exempel hittar du i [Så här skapar du en schemautlösare](how-to-create-schedule-trigger.md)

## <a name="tumbling-window-trigger"></a>Utlösare för rullande fönster
Utlösare för rullande fönster är en typ av utlösare som går igång med jämna tidsintervall från en angiven starttid och behåller sitt tillstånd. Rullande fönster är en serie med sammanhängande tidsintervaller med fast storlek som inte överlappar.
Mer detaljerad information om utlösare för rullande fönster och exempel finns i [Så här skapar du en utlösare för rullande fönster](how-to-create-tumbling-window-trigger.md)

### <a name="scheduler-trigger-json-definition"></a>JSON-definition för Scheduler-utlösaren
När du skapar en Scheduler-utlösare kan du ange schemaläggning och upprepning med JSON enligt exemplet i det här avsnitt. 

Om du vill att Scheduler-utlösaren startar en pipelinekörning tar du med en pipelinereferens i utlösardefinitionen. Pipeliner och utlösare har en många-till-många-relation. Flera utlösare kan starta en pipeline. En enskild utlösare kan starta flera pipeliner.

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Year>>,
        "interval": <<int>>,             // how often to fire
        "startTime": <<datetime>>,
        "endTime": <<datetime>>,
        "timeZone": "UTC"
        "schedule": {                    // optional (advanced scheduling specifics)
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
>  Egenskapen **parametrar** är en obligatorisk egenskap i **pipeliner**. Inkludera en tom json för parametrar eftersom egenskapen måste finnas, även om din pipeline inte tar några parametrar.


### <a name="overview-scheduler-trigger-schema"></a>Översikt: Scheduler-utlösarschema
Följande tabell innehåller en översikt över huvudelementen relaterade till upprepning och schemaläggning i en utlösare:

JSON-egenskap |     Beskrivning
------------- | -------------
startTime | startTime är datum och tid. StartTime är den första förekomsten för enkla scheman. För komplexa scheman startar utlösaren tidigast startTime.
endTime | Anger slutvärdet för datum/tid för utlösaren. Utlösaren körs inte efter den tidpunkten. Det går inte att ha en endTime (sluttid) i dåtid.
Tidszon | För närvarande stöds endast UTC. 
recurrence | Objektet recurrence anger upprepningsregler för utlösaren. Upprepningsobjektet stöder följande element: frequency (frekvens), interval (intervall), endTime (sluttid), count (antal) och schedule (schema). Om recurrence (upprepning) definieras krävs frequency (frekvens). De andra recurrence-elementen är valfria.
frequency | Representerar frekvensen med vilken utlösaren upprepas. Värden som stöds är: `minute`, `hour`, `day`, `week` eller `month`.
interval | Elementet interval är ett positivt heltal. Den anger intervallet för den frekvens som avgör hur ofta utlösaren körs. Om intervallet till exempel är 3 och frekvensen är week (vecka) upprepas utlösaren var tredje vecka.
schedule | En utlösare med en angiven frekvens ändrar sin upprepning baserat på ett recurrence schedule (upprepningsschema). Ett schema innehåller ändringar som baseras på minuter, timmar, veckodagar, dagar i månaden och veckonummer.


## <a name="tumbling-window-trigger-vs-schedule-trigger"></a>Utlösare för rullande fönster jämfört med Schemautlösare
Vad skiljer utlösare för rullande fönster och schemautlösare åt, med tanke på att båda två körs via tidspulsslag?
För utlösare för rullande fönster:
* **Återfyllnadsscenarier**: Utlösare för rullande fönster stöder återfyllnadsscenarier, och kan schemalägga körningar för tidigare fönster. Schemautlösare kan enbart köras för tidsperioder från nuläget och framåt.
* **Tillförlitlighet:** Utlösare för rullande fönster schemalägger pipelinekörningar för alla fönster från ett startdatum utan luckor med 100 % tillförlitlighet.
* **Försök igen**: Utlösare för rullande fönster har kapacitet för att försöka igen. Misslyckade pipelinekörningar har en standardprincip för återförsök på 0 eller ett antal som angivits av användaren som en del av utlösarens definition. Den försöker också igen automatiskt på instanser när körningar misslyckas på grund av begränsningar för samtidighet/server/nätverk till exempel. Det gäller statuskod 400 (fel från användarens sida), 429 (för många förfrågningar), 500 (internt serverfel).
* **Samtidighet**: Utlösare för rullande fönster låter användarna att explicit ange samtidighetsgränser för utlösaren (högst 150 samtidiga utlösta pipelinekörningar)
* **Variabler för start- och sluttid för fönster**: För utlösare för rullande fönster kan användarna få åtkomst till triggerOutputs().windowStartTime och triggerOutputs().windowEndTime som variabler för utlösarsystem i definitionen för utlösare som är fönstrets start- respektive sluttid. Om du till exempel har en utlösare för rullande fönster som körs varje timme för perioden 01–02 blir triggerOutputs().WindowStartTime = 2017-09-01T01:00:00Z och triggerOutputs().WindowEndTime = 2017-09-01T02:00:00Z.
* **Relationen mellan pipeline och utlösare**: Schemautlösare har en n:m-relation med pipelines. En schemautlösare kan utlösa flera pipelines. Utlösare för rullande fönster har ett 1:1-förhållande med pipelines. En utlösare för rullande fönster kan endast utlösa en pipeline.

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

### <a name="overview-scheduler-trigger-schema-defaults-limits-and-examples"></a>Översikt: Scheduler-utlösaren – schemastandardvärden, begränsningar och exempel

JSON-namn | Värdetyp | Krävs? | Standardvärde | Giltiga värden | Exempel
--------- | ---------- | --------- | ------------- | ------------ | -------
startTime | Sträng | Ja | Ingen | ISO 8601-datum/tid | ```"startTime" : "2013-01-09T09:30:00-08:00"```
recurrence | Objekt | Ja | Ingen | Upprepningsobjekt | ```"recurrence" : { "frequency" : "monthly", "interval" : 1 }```
interval | Tal | Ja | Ingen | 1 till 1000. | ```"interval":10```
endTime | Sträng | Ja | Ingen | Datum/tid-värde som representerar en tidpunkt i framtiden | `"endTime" : "2013-02-09T09:30:00-08:00"`
schedule | Objekt | Nej | Ingen | Schemaobjekt | `"schedule" : { "minute" : [30], "hour" : [8,17] }`

### <a name="deep-dive-starttime"></a>Djupdykning: startTime (starttid)
Följande tabell visar hur startTime styr hur en utlösare körs:

startTime-värde | Upprepning utan schema | Upprepning med schema
--------------- | --------------------------- | ------------------------
Starttid i förfluten tid | Beräknar första framtida körningstid efter starttid och kör den tiden.<p>Utför efterföljande körningar baserat på beräkning från senaste körningstid.</p><p>Se exemplet som följer den här tabellen.</p> | Utlösaren startar _tidigast_ på den angivna starttiden. Den första förekomsten baseras på schemat som beräknas från starttiden. <p>Kör efterföljande körningar baserat på upprepningsschemat</p>
Starttid i framtiden eller aktuell tid | Körs en gång på angiven starttid. <p>Utför efterföljande körningar baserat på beräkning från senaste körningstid.</p> | Utlösaren startar _tidigast_ på den angivna starttiden. Den första förekomsten baseras på schemat som beräknas från starttiden.<p>Kör efterföljande körningar baserat på upprepningsschemat.</p>

Nu tittar vi på ett exempel på när startTime är i förfluten tid, med upprepning men inget schema. Anta att den aktuella tiden är `2017-04-08 13:00`, startTime är `2017-04-07 14:00` och upprepningen är varannan dag (definieras med frequency (frekvens): day (dag) och interval (intervall): 2) Observera att startTime är i förfluten tid och inträffar före aktuell tid.

Med dessa villkor sker den första körningen `2017-04-09 at 14:00`. Scheduler-motor beräknar körningsförekomster från starttiden. Alla tidigare instanser ignoreras. Motorn använder nästa förekomst som förekommer i framtiden. I det här fallet är startTime `2017-04-07 at 2:00pm`, så nästa instans är två dagar från den tiden, vilket är `2017-04-09 at 2:00pm`.

Den första körningstiden är samma även om startTime `2017-04-05 14:00` eller `2017-04-01 14:00`. Efter den första körningen beräknas efterföljande körningar med hjälp av schemat. De är därför `2017-04-11 at 2:00pm`, sedan `2017-04-13 at 2:00pm`, sedan `2017-04-15 at 2:00pm` osv.

Till sist: när en utlösare har ett schema, om timmar och/eller minuter inte anges i schemat, används standardvärdet för timmar och/eller minuter för den första körningen.

### <a name="deep-dive-schedule"></a>Djupdykning: schema
Å ena sidan kan ett schema begränsa antalet utlösarkörningar. Om en utlösare med frekvensen month (månad) har ett schema som bara körs på dag 31 körs utlösaren bara på de månader som har en 31:a dag.

Ett schema kan å andra sidan även utöka antalet utlösarkörningar. Om en utlösare med frekvensen month (månad) har ett schema som körs på dag 1 och 2 i månaden körs utlösaren på den första och andra dagen i månaden istället för en gång i månaden.

Om flera schemaelement anges är utvärderingsordningen från största till minsta – veckonummer, dag i månaden, veckodag, timme och minut.

I följande tabell beskrivs schemaelement i detalj:


JSON-namn | Beskrivning | Giltiga värden
--------- | ----------- | ------------
minutes | Minuter för den timme då utlösaren körs. | <ul><li>Heltalsmatris</li></ul>
hours | Timmar på dagen då utlösaren körs. | <ul><li>Heltalsmatris</li></ul>
weekDays | Veckodagar då utlösaren körs. Kan bara anges med veckofrekvens. | <ul><li>Matris med något av värdena nedan (max. matrisstorlek 7)<ul><li>måndag</li><li>tisdag</li><li>onsdag</li><li>torsdag</li><li>fredag</li><li>lördag</li><li>söndag</li></ul></li></p>Inte skiftlägeskänslig</p>
monthlyOccurrences | Anger vilka dagar i månaden som utlösaren körs. Kan bara anges med månadsfrekvens. | Matris med monthlyOccurence-objekt: `{ "day": day,  "occurrence": occurence }`. <p> Dagen är den veckodag då utlösaren körs, till exempel är `{Sunday}` varje söndag i månaden. Krävs.<p>Förekomst är förekomsten av dag under månaden, till exempel är `{Sunday, -1}` den sista söndagen i månaden. Valfri.
monthDays | Dagen i månaden då utlösaren körs. Kan bara anges med månadsfrekvens. | <ul><li>En matris med nedanstående värden</li><ul><li>Ett värde < = -1 och > =-31</li><li>Ett värde > = 1 och < = 31</li></ul></ul> |


## <a name="examples-recurrence-schedules"></a>Exempel: upprepningsscheman
I det här avsnittet ges exempel på upprepningsscheman – med fokus på schemaobjektet och dess underelement.

I dessa exempelscheman antas att 1 anges som intervallvärde. Anta dessutom rätt frekvens enligt vad som finns i schemat – till exempel kan du inte använda frekvensen day (dag) och ha en monthDays-modifiering (dagar i månaden) i schemat. Dessa begränsningar nämns i tabellen i föregående avsnitt. 

Exempel | Beskrivning
------- | -----------
`{"hours":[5]}` | Körs 05:00 varje dag
`{"minutes":[15], "hours":[5]}` | Körs 05:15 varje dag
`{"minutes":[15], "hours":[5,17]}` | Körs 05:15 och 17:15 varje dag
`{"minutes":[15,45], "hours":[5,17]}` | Körs 05:15, 05:45, 17:15 och 17:45 varje dag
`{"minutes":[0,15,30,45]}` | Körs var 15:e minut
`{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Körs varje timme. Den här utlösaren körs varje timme. Minuten störs av startTime, om den har angetts, eller skapandetiden, om den inte har angetts. Exempel: om starttiden eller skapandetiden (beroende på vilken som gäller) är 12:25 körs utlösaren 00:25, 01:25, 02:25, …, 23:25. Schemat motsvarar att ha en utlösare med frekvensen hour (timme), intervallet 1 och inget schema. Skillnaden är att det här schemat kan användas med olika frekvens och intervall för att också skapa andra utlösare. Om frekvensen är month (månad) skulle schemat bara köras en gång i månaden istället för varje dag, om frekvensen är day (dag).
`{"minutes":[0]}` | Körs varje timme. Den här utlösaren körs även varje timme, men prick på timmen (t.ex. 12:00, 13:00, 14:00 osv.). Den här inställningen motsvarar en utlösare med frekvensen hour (timme) startTime med noll minuter och inget scheman om frekvensen är day (dag) men om frekvensen är week (vecka) eller month (månad) körs schemat bara en gång i veckan respektive en gång i månaden.
`{"minutes":[15]}` | Körs 15 minuter över varje hel timme. Körs varje timme, med start 00:15, 01:15, 02:15 osv och slutar 22:15 och 23:15.
`{"hours":[17], "weekDays":["saturday"]}` | Körs 17:00 på lördagar varje vecka
`{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Körs 17: 00 på måndag, onsdag och fredag varje vecka
`{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Körs 17:15 och 17:45 på måndag, onsdag och fredag varje vecka
`{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Körs var 15: e minut på vardagar
`{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Körs var 15: e minut på vardagar mellan 09:00 och 16:45
`{"weekDays":["tuesday", "thursday"]}` | Körs varje tisdag och torsdag på den angivna starttiden.
`{"minutes":[0], "hours":[6], "monthDays":[28]}` | Körs 06: 00 på den 28:e dagen varje månad (förutsatt frekvensen månad)
`{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Körs 06:00 på den sista dagen i månaden. Om du vill köra en utlösare på sista dagen i månaden använder du -1 istället för dag 28, 29, 30 eller 31.
`{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Körs 06:00 på första och sista dagen varje månad
`{monthDays":[1,14]}` | Körs på den första och fjortonde dagen i varje månad på den angivna starttiden.
`{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Körs på den första fredagen i varje månad 05:00
`{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Körs på den första fredagen i varje månad på den angivna starttiden.
`{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | Körs på den tredje fredagen från slutet i månaden, varje månad, på starttiden
`{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Körs på första och sista fredagen i varje månad 05:15
`{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Körs på första och sista fredagen i varje månad på den angivna starttiden
`{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Körs på den femte fredagen i varje månad på starttiden. Om det inte finns någon femte fredag i en månad körs inte pipelinen, eftersom den är schemalagd att bara köras på den femte fredagen.  Om du vill köra utlösaren på den sista fredagen i månaden kan du använda -1 istället för 5 som förekomst.
`{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Körs var 15:e minut på sista fredagen i månaden.
`{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Körs 05:15, 05:45, 17:15 och 17:45 på den tredje onsdagen i varje månad.




## <a name="next-steps"></a>Nästa steg
Se följande självstudiekurser:

- [Snabbstart: skapa en datafabrik med hjälp av .NET](quickstart-create-data-factory-dot-net.md)
- [Så här skapar du en schemautlösare](how-to-create-schedule-trigger.md)
- [Så här skapar du en utlösare för rullande fönster](how-to-create-tumbling-window-trigger.md)
