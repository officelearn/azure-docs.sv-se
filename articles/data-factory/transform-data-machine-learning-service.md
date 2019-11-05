---
title: Köra Azure Machine Learning pipelines i Azure Data Factory pipelines | Microsoft Docs
description: Lär dig hur du kör Azure Machine Learning pipelines i Azure Data Factory pipeliner.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/10/2019
author: djpmsft
ms.author: daperlov
ms.openlocfilehash: c05a0fde7019d9162a051a04696c2251301d70dc
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490591"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>Köra Azure Machine Learning pipelines i Azure Data Factory

Kör Azure Machine Learning pipelines som ett steg i Azure Data Factory pipeliner. Med aktiviteten kör pipeline för Machine Learning kan du definiera scenarier för batch-förutsägelse, till exempel identifiera möjliga låne standarder, bestämma sentiment och analysera kund beteende mönster.

## <a name="syntax"></a>Syntax

```json
{
    "name": "Machine Learning Execute Pipeline",
    "type": "AzureMLExecutePipeline",
    "linkedServiceName": {
        "referenceName": "AzureMLService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mlPipelineId": "machine learning pipeline ID",
        "experimentName": "experimentName",
        "mlPipelineParameters": {
            "mlParameterName": "mlParameterValue"
        }
    }
}

```

## <a name="type-properties"></a>Typ egenskaper

Egenskap | Beskrivning | Tillåtna värden | Krävs
-------- | ----------- | -------------- | --------
namn | Namn på aktiviteten i pipelinen | Sträng | Ja
typ | Typ av aktivitet är ' AzureMLExecutePipeline ' | Sträng | Ja
linkedServiceName | Länkad tjänst till Azure Machine Learning | Länkad tjänst referens | Ja
mlPipelineId | ID för den publicerade Azure Machine Learning pipelinen | Sträng (eller uttryck med resultType för sträng) | Ja
experimentName | Körnings experimentets experiment namn för Machine Learning pipeline-körningen | Sträng (eller uttryck med resultType för sträng) | Nej
mlPipelineParameters | Nyckel, värdepar som ska skickas till den publicerade Azure Machine Learning pipeline-slutpunkten. Nycklar måste matcha namnen på de pipeline-parametrar som definierats i den publicerade Machine Learning pipelinen | Objekt med nyckel värdes par (eller uttryck med resultType-objekt) | Nej
mlParentRunId | ID för pipeline-körning för överordnad Azure Machine Learning | Sträng (eller uttryck med resultType för sträng) | Nej
continueOnStepFailure | Om körningen av andra steg i Machine Learning pipelinen ska fortsätta köras om ett steg Miss lyckas | boolesk | Nej

## <a name="next-steps"></a>Nästa steg
Se följande artiklar som förklarar hur du omformar data på andra sätt:

* [Kör data flödes aktivitet](control-flow-execute-data-flow-activity.md)
* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [Aktivitet i gris](transform-data-using-hadoop-pig.md)
* [MapReduce-aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming-aktivitet](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Lagrad procedur aktivitet](transform-data-using-stored-procedure.md)
