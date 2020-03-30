---
title: Aktivitet för dataflöde
description: Så här kör du dataflöden inifrån en pipeline för datafabriker.
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: makromer
ms.date: 03/16/2020
ms.openlocfilehash: 115cb3e499117457629e130b6432a1cbc2224edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79463058"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Dataflödesaktivitet i Azure Data Factory

Använd aktiviteten Dataflöde för att omvandla och flytta data via mappningsdataflöden. Om du inte har tidigare information om dataflöden läser du [Översikt över Mappning av dataflöde](concepts-data-flow-overview.md)

## <a name="syntax"></a>Syntax

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "MyDataFlow",
         "type": "DataFlowReference"
      },
      "compute": {
         "coreCount": 8,
         "computeType": "General"
      },
      "staging": {
          "linkedService": {
              "referenceName": "MyStagingLinkedService",
              "type": "LinkedServiceReference"
          },
          "folderPath": "my-container/my-folder"
      },
      "integrationRuntime": {
          "referenceName": "MyDataFlowIntegrationRuntime",
          "type": "IntegrationRuntimeReference"
      }
}

```

## <a name="type-properties"></a>Egenskaper för typ

Egenskap | Beskrivning | Tillåtna värden | Krävs
-------- | ----------- | -------------- | --------
dataflöde | Hänvisningen till det dataflöde som körs | DataFlowReference | Ja
integrationRuntime | Beräkningsmiljön som dataflödet körs på. Om inget anges används körningen för automatisk integrering av Azure-integrering | IntegrationRuntimeReference | Inga
compute.coreCount | Antalet kärnor som används i sparkklustret. Kan bara anges om den automatiska lösningen av Azure Integration-körningen används | 8, 16, 32, 48, 80, 144, 272 | Inga
compute.computeType | Den typ av beräkning som används i sparkklustret. Kan bara anges om den automatiska lösningen av Azure Integration-körningen används | "Allmänt", "ComputeOptimized", "MemoryOptimized" | Inga
staging.linkedService | Om du använder en SQL DW-källa eller diskho visas det lagringskonto som används för PolyBase-mellanlagring | LinkedServiceReference | Endast om dataflödet läser eller skriver till en SQL DW
staging.folderPath | Om du använder en SQL DW-källa eller diskho visas mappsökvägen i blob-lagringskontot som används för PolyBase-mellanlagring | String | Endast om dataflödet läser eller skriver till en SQL DW

![Kör dataflöde](media/data-flow/activity-data-flow.png "Kör dataflöde")

### <a name="dynamically-size-data-flow-compute-at-runtime"></a>Dynamisk storlek dataflödesberäkning vid körning

Egenskaperna Kärnantal och beräkningstyp kan ställas in dynamiskt för att justera till storleken på inkommande källdata vid körning. Använd pipeline-aktiviteter som Uppslag eller Hämta metadata för att hitta storleken på källdatauppsättningsdata. Använd sedan Lägg till dynamiskt innehåll i aktivitetsegenskaperna Dataflöde.

![Dynamiskt dataflöde](media/data-flow/dyna1.png "Dynamiskt dataflöde")

[Här är en kort video tutorial som förklarar denna teknik](https://www.youtube.com/watch?v=jWSkJdtiJNM)

### <a name="data-flow-integration-runtime"></a>Körning för dataflödesintegrering

Välj vilken integrationskörning som ska användas för körningen av dataflödesaktivitet. Som standard använder Data Factory den automatiskt lösa Azure Integration-körningen med fyra arbetskärnor och ingen tid att leva (TTL). Den här IR:et har en beräkningstyp för allmänt syfte och körs i samma region som din fabrik. Du kan skapa egna Azure Integration Runtimes som definierar specifika regioner, beräkningstyp, kärnantal och TTL för körning av dataflödesaktivitet.

För pipelinekörningar är klustret ett jobbkluster, vilket tar flera minuter att starta innan körningen startar. Om ingen TTL anges krävs den här starttiden på varje pipeline-körning. Om du anger en TTL förblir en varm klusterpool aktiv under den tid som angavs efter den senaste körningen, vilket resulterar i kortare starttider. Om du till exempel har en TTL på 60 minuter och kör ett dataflöde på den en gång i timmen förblir klusterpoolen aktiv. Mer information finns i [Azure integration runtime](concepts-integration-runtime.md).

![Körning för Azure-integrering](media/data-flow/ir-new.png "Körning för Azure-integrering")

> [!NOTE]
> Valet av integrationskörning i aktiviteten Dataflöde gäller endast *utlösta körningar* av pipelinen. Felsökning av pipelinen med dataflöden körs i klustret som anges i felsökningssessionen.

### <a name="polybase"></a>PolyBase

Om du använder ett Azure SQL Data Warehouse som en diskho eller källa måste du välja en mellanlagringsplats för din PolyBase-batchbelastning. PolyBase möjliggör batchinläsning i bulk i stället för att läsa in data rad för rad. PolyBase minskar inläsningstiden drastiskt till SQL DW.

## <a name="parameterizing-data-flows"></a>Parameterisera dataflöden

### <a name="parameterized-datasets"></a>Parameteriserade datauppsättningar

Om dataflödet använder parameteriserade datauppsättningar anger du parametervärdena på fliken **Inställningar.**

![Köra dataflödesparametrar](media/data-flow/params.png "Parametrar")

### <a name="parameterized-data-flows"></a>Parameteriserade dataflöden

Om dataflödet är parameteriserat anger du dynamiska värden för dataflödesparametrarna på fliken **Parametrar.** Du kan använda antingen ADF-pipeline-uttrycksspråket (endast för strängtyper) eller dataflödesuttrycksspråket för att tilldela dynamiska eller litterala parametervärden. Mer information finns i [Dataflödesparametrar](parameters-data-flow.md).

![Kör dataflödesparameter exempel](media/data-flow/parameter-example.png "Exempel på parameter")

### <a name="parameterized-compute-properties"></a>Parameteriserade beräkningsegenskaper.

Du kan parameterisera kärnantalet eller beräkningstypen om du använder körningen för automatisk integrering av Azure Integration och anger värden för compute.coreCount och compute.computeType.

![Kör dataflödesparameter exempel](media/data-flow/parameterize-compute.png "Exempel på parameter")

## <a name="pipeline-debug-of-data-flow-activity"></a>Pipeline-felsökning av dataflödesaktivitet

Om du vill köra en felsökningspipeline som körs med en dataflödesaktivitet måste du aktivera felsökningsläge för dataflödet via skjutreglaget **Dataflödesfelsökning** i det övre fältet. Med felsökningsläget kan du köra dataflödet mot ett aktivt Spark-kluster. Mer information finns i [Felsökningsläge](concepts-data-flow-debug-mode.md).

![Knappen Felsökning](media/data-flow/debugbutton.png "Knappen Felsökning")

Felsökningspipelinen körs mot det aktiva felsökningsklustret, inte den integrationskörningsmiljö som anges i aktivitetsinställningarna för dataflöde. Du kan välja felsökningsberäkningsmiljön när du startar felsökningsläge.

## <a name="monitoring-the-data-flow-activity"></a>Övervaka aktiviteten dataflöde

Aktiviteten Dataflöde har en särskild övervakningsupplevelse där du kan visa partitionering, scentid och datalinjeinformation. Öppna övervakningsfönstret via glasögonikonen under **Åtgärder**. Mer information finns i [Övervaka dataflöden](concepts-data-flow-monitoring.md).

### <a name="use-data-flow-activity-results-in-a-subsequent-activity"></a>Använda dataflödesaktivitet resulterar i en efterföljande aktivitet

Dataflödesaktiviteten utdatamått för antalet rader som skrivits till varje diskho och rader som läss från varje källa. Dessa resultat returneras `output` i avsnittet i resultatet för aktivitetskörning. De returnerade måtten är i formatet nedanstående json.

``` json
{
    "runStatus": {
        "metrics": {
            "<your sink name1>": {
                "rowsWritten": <number of rows written>,
                "sinkProcessingTime": <sink processing time in ms>,
                "sources": {
                    "<your source name1>": {
                        "rowsRead": <number of rows read>
                    },
                    "<your source name2>": {
                        "rowsRead": <number of rows read>
                    },
                    ...
                }
            },
            "<your sink name2>": {
                ...
            },
            ...
        }
    }
}
```

Om du till exempel vill komma till antalet rader som skrivits till en diskho `@activity('dataflowActivity').output.runStatus.metrics.sink1.rowsWritten`med namnet "sink1" i en aktivitet med namnet "dataflowActivity" använder du .

Om du vill att antalet rader ska läsas från en källa med `@activity('dataflowActivity').output.runStatus.metrics.sink1.sources.source1.rowsRead`namnet "source1" som användes i diskhon använder du .

> [!NOTE]
> Om en diskho har noll rader skrivna visas den inte i mått. Existens kan verifieras `contains` med hjälp av funktionen. Till exempel `contains(activity('dataflowActivity').output.runStatus.metrics, 'sink1')` kommer att kontrollera om några rader skrevs till sink1.

## <a name="next-steps"></a>Nästa steg

Se kontrollflödesaktiviteter som stöds av Data Factory: 

- [If-villkorsaktivitet](control-flow-if-condition-activity.md)
- [Execute Pipeline-aktivitet](control-flow-execute-pipeline-activity.md)
- [För varje aktivitet](control-flow-for-each-activity.md)
- [Hämta metadataaktivitet](control-flow-get-metadata-activity.md)
- [Lookup-aktivitet](control-flow-lookup-activity.md)
- [Webbaktivitet](control-flow-web-activity.md)
- [Until-aktivitet](control-flow-until-activity.md)
