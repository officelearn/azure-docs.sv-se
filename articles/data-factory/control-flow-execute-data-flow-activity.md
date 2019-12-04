---
title: Data flödes aktivitet i Azure Data Factory
description: Så här kör du data flöden inifrån en Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.author: makromer
ms.date: 10/07/2019
ms.openlocfilehash: 3f05b9ae490ea2b9d8e7b89ce02c7c1eb818bb0a
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769583"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Data flödes aktivitet i Azure Data Factory

Använd data flödes aktiviteten för att transformera och flytta data via data flöden för mappning. Om du är nybörjare på data flöden, se [Översikt över kart data flöde](concepts-data-flow-overview.md)

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

## <a name="type-properties"></a>Typ egenskaper

Egenskap | Beskrivning | Tillåtna värden | Krävs
-------- | ----------- | -------------- | --------
data flöde | Referens till det data flöde som körs | DataFlowReference | Ja
integrationRuntime | Beräknings miljön som data flödet körs på | IntegrationRuntimeReference | Ja
mellanlagring. linkedService | Om du använder en SQL DW-källa eller-mottagare är det lagrings konto som används för PolyBase-mellanlagring | LinkedServiceReference | Endast om data flödet läser eller skriver till en SQL DW
mellanlagring. folderPath | Om du använder en SQL DW-källa eller mottagare, är mappsökvägen i Blob Storage-kontot som används för PolyBase-mellanlagring | Sträng | Endast om data flödet läser eller skriver till en SQL DW

![Kör data flöde](media/data-flow/activity-data-flow.png "Kör data flöde")

### <a name="data-flow-integration-runtime"></a>Integration runtime för data flöde

Välj vilken Integration Runtime som ska användas för körningen av data flödes aktiviteten. Som standard använder Data Factory automatiskt lösa Azure integration runtime med fyra arbets kärnor och ingen TTL-värde (Time to Live). Denna IR har en generell beräknings typ och körs i samma region som din fabrik. Du kan skapa dina egna Azure-integrerings körningar som definierar specifika regioner, beräknings typ, antal kärnor och TTL för din data flödes aktivitets körning.

För pipeline-körningar är klustret ett jobb kluster, vilket tar flera minuter att starta innan körningen börjar. Om du inte anger något TTL-värde krävs den här start tiden för varje pipeline-körning. Om du anger ett TTL-värde förblir en varm klustrad pool aktiv under den tid som anges efter den senaste körningen, vilket leder till kortare start tider. Om du till exempel har en TTL på 60 minuter och kör ett data flöde på den en gång i timmen förblir anslutningspoolen aktiv. Mer information finns i [Azure integration runtime](concepts-integration-runtime.md).

![Azure Integration Runtime](media/data-flow/ir-new.png "Azure Integration Runtime")

> [!NOTE]
> Integration Runtime valet i data flödes aktiviteten gäller endast *utlösta körningar* av din pipeline. Fel sökning av din pipeline med data flöden körs på det kluster som anges i felsökningssessionen.

### <a name="polybase"></a>PolyBase

Om du använder en Azure SQL Data Warehouse som mottagare eller källa måste du välja en mellanlagringsplats för din PolyBase-inläsning. PolyBase tillåter satsvis inläsning i bulk i stället för att läsa in data rad för rad. PolyBase minskar drastiskt inläsnings tiden i SQL DW.

## <a name="parameterizing-data-flows"></a>Parameters-data flöden

### <a name="parameterized-datasets"></a>Parameterstyrda data uppsättningar

Om data flödet använder parametriserade data uppsättningar anger du parameter värden på fliken **Inställningar** .

![Kör data flödes parametrar](media/data-flow/params.png "Parametrar")

### <a name="parameterized-data-flows"></a>Parameter data flöden

Om ditt data flöde är parameterstyrda anger du de dynamiska värdena för data flödes parametrarna på fliken **parametrar** . Du kan använda antingen uttrycks språket ADF-pipeline (endast för sträng typer) eller språket för data flödes uttrycket för att tilldela dynamiska eller exakta parameter värden. Mer information finns i [data flödes parametrar](parameters-data-flow.md).

![Exempel på körning av data flödes parameter](media/data-flow/parameter-example.png "Parameter exempel")

## <a name="pipeline-debug-of-data-flow-activity"></a>Fel sökning av pipeline för data flödes aktivitet

Om du vill köra en pipeline för fel sökning med en data flödes aktivitet måste du växla till fel söknings läge för data flöde via skjutreglaget för **fel sökning av data flöde** i det översta fältet. Med fel söknings läge kan du köra data flödet mot ett aktivt Spark-kluster. Mer information finns i [fel söknings läge](concepts-data-flow-debug-mode.md).

![Knappen Felsök](media/data-flow/debugbutton.png "Knappen Felsök")

Fel söknings pipelinen körs mot det aktiva fel söknings klustret, inte integrerings körnings miljön som anges i inställningarna för data flödes aktiviteten. Du kan välja beräknings miljö för fel sökning när du startar fel söknings läge.

## <a name="monitoring-the-data-flow-activity"></a>Övervaka data flödes aktiviteten

Data flödes aktiviteten har en särskild övervaknings upplevelse där du kan visa information om partitionering, fas tid och data härkomst. Öppna fönstret övervakning via glasögon-ikonen under **åtgärder**. Mer information finns i [övervaka data flöden](concepts-data-flow-monitoring.md).

### <a name="use-data-flow-activity-results-in-a-subsequent-activity"></a>Använd data flödes aktivitets resultat i en efterföljande aktivitet

Data flödes aktiviteten matar ut mått för antalet rader som skrivs till varje mottagare och rader läses från varje källa. De här resultaten returneras i avsnittet `output` i resultatet av aktivitets körningen. De värden som returneras är i formatet under JSON.

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

Om du till exempel vill komma till antalet rader som skrivs till en mottagare med namnet "sink1" i en aktivitet med namnet "dataflowActivity" använder du `@activity('dataflowActivity').output.runStatus.metrics.sink1.rowsWritten`.

Använd `@activity('dataflowActivity').output.runStatus.metrics.sink1.sources.source1.rowsRead`om du vill hämta antalet rader från en källa med namnet ' source1 ' som användes i denna mottagare.

> [!NOTE]
> Om en Sink har noll rader skrivna visas den inte i mått. Förekomst kan verifieras med hjälp av funktionen `contains`. `contains(activity('dataflowActivity').output.runStatus.metrics, 'sink1')` kan till exempel kontrol lera om några rader skrevs till sink1.

## <a name="next-steps"></a>Nästa steg

Se kontroll flödes aktiviteter som stöds av Data Factory: 

- [If-villkorsaktivitet](control-flow-if-condition-activity.md)
- [Execute Pipeline-aktivitet](control-flow-execute-pipeline-activity.md)
- [För varje aktivitet](control-flow-for-each-activity.md)
- [GetMetadata-aktivitet](control-flow-get-metadata-activity.md)
- [Lookup-aktivitet](control-flow-lookup-activity.md)
- [Webb aktivitet](control-flow-web-activity.md)
- [Until-aktivitet](control-flow-until-activity.md)
