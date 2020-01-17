---
title: Köra Azure Machine Learning pipelines
description: Lär dig hur du kör Azure Machine Learning pipelines i Azure Data Factory pipeliner.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.date: 10/10/2019
ms.openlocfilehash: b54504cf8ca7b32bf14bd4b7e0c561ffd56d4098
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2020
ms.locfileid: "76155171"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>Köra Azure Machine Learning pipelines i Azure Data Factory

Kör Azure Machine Learning pipelines som ett steg i Azure Data Factory pipeliner. Med aktiviteten kör pipeline för Machine Learning kan du definiera scenarier för batch-förutsägelse, till exempel identifiera möjliga låne standarder, bestämma sentiment och analysera kund beteende mönster.

I de här video funktionerna nedan finns en halvtimmes introduktion och demonstration av den här funktionen.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/How-to-execute-Azure-Machine-Learning-service-pipelines-in-Azure-Data-Factory/player]

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
namn | Namn på aktiviteten i pipelinen | String | Ja
typ | Typ av aktivitet är ' AzureMLExecutePipeline ' | String | Ja
linkedServiceName | Länkad tjänst till Azure Machine Learning | Länkad tjänst referens | Ja
mlPipelineId | ID för den publicerade Azure Machine Learning pipelinen | Sträng (eller uttryck med resultType för sträng) | Ja
ExperimentName | Körnings experimentets experiment namn för Machine Learning pipeline-körningen | Sträng (eller uttryck med resultType för sträng) | Inga
mlPipelineParameters | Nyckel, värdepar som ska skickas till den publicerade Azure Machine Learning pipeline-slutpunkten. Nycklar måste matcha namnen på de pipeline-parametrar som definierats i den publicerade Machine Learning pipelinen | Objekt med nyckel värdes par (eller uttryck med resultType-objekt) | Inga
mlParentRunId | ID för pipeline-körning för överordnad Azure Machine Learning | Sträng (eller uttryck med resultType för sträng) | Inga
continueOnStepFailure | Om körningen av andra steg i Machine Learning pipelinen ska fortsätta köras om ett steg Miss lyckas | boolean | Inga

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
