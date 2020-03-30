---
title: Kör Azure Machine Learning-pipelines
description: Lär dig hur du kör dina Azure Machine Learning-pipelines i dina Azure Data Factory-pipelines.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76155171"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>Köra Azure Machine Learning-pipelines i Azure Data Factory

Kör dina Azure Machine Learning-pipelines som ett steg i dina Azure Data Factory-pipelines. Machine Learning Execute Pipeline-aktiviteten möjliggör batchförutsägelsescenarier som att identifiera möjliga standardvärden för lån, bestämma sentiment och analysera kundbeteendemönster.

Nedanstående video har en sex minuters introduktion och demonstration av denna funktion.

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

## <a name="type-properties"></a>Egenskaper för typ

Egenskap | Beskrivning | Tillåtna värden | Krävs
-------- | ----------- | -------------- | --------
namn | Namnet på den verksamhet som är på gång | String | Ja
typ | Typ av aktivitet är "AzureMLExecutePipeline" | String | Ja
linkedServiceName | Länkad tjänst till Azure Machine Learning | Länkad tjänstreferens | Ja
mlPipelineId | ID för den publicerade Azure Machine Learning-pipelinen | Sträng (eller uttryck med resultType av sträng) | Ja
experimentNamn | Kör historikexperimentnamnet för pipelinekörningen Machine Learning | Sträng (eller uttryck med resultType av sträng) | Inga
mlPipelineParameters | Nyckel, värdepar som ska skickas till den publicerade Azure Machine Learning-pipelineslutpunkten. Nycklar måste matcha namnen på pipelineparametrar som definierats i den publicerade Machine Learning-pipelinen | Objekt med nyckelvärdepar (eller Uttryck med resultType-objekt) | Inga
mlParentRunId | Det överordnade Azure Machine Learning-pipelinekörnings-ID:et | Sträng (eller uttryck med resultType av sträng) | Inga
fortsättOnStepFailure | Om andra steg ska fortsättas i pipelinekörningen för maskininlärning om ett steg misslyckas | boolean | Inga

## <a name="next-steps"></a>Nästa steg
Se följande artiklar som förklarar hur du omvandlar data på andra sätt:

* [Kör dataflödesaktivitet](control-flow-execute-data-flow-activity.md)
* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [Grisaktivitet](transform-data-using-hadoop-pig.md)
* [MapReduce aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming aktivitet](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Lagrad proceduraktivitet](transform-data-using-stored-procedure.md)
