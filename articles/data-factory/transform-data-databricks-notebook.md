---
title: Transformera data med Databricks bärbara - Azure | Microsoft Docs
description: Lär dig hur du bearbetar eller Transformera data genom att köra en Databricks bärbar dator.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: douglasl
ms.openlocfilehash: d4a57e45d5ddf55906fcf575df39135a227418ec
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Transformera data genom att köra en Databricks anteckningsbok

Azure Databricks anteckningsboken aktiviteten i en [Data Factory-pipelinen](concepts-pipelines-activities.md) körs en Databricks bärbar dator i Azure Databricks arbetsytan. Den här artikeln bygger på den [data transformation aktiviteter](transform-data.md) artikel som presenterar en allmän översikt över data transformation och stöds omvandling aktiviteter. Azure Databricks är en hanterad plattform för att köra Apache Spark.

## <a name="databricks-notebook-activity-definition"></a>Databricks anteckningsboken aktivitetsdefinition

Här är exempel JSON-definitionen för en Databricks anteckningsboken aktivitet:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksNotebook",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "notebookPath": "/Users/user@example.com/ScalaExampleNotebook",
            "baseParameters": {
                "inputpath": "input/folder1/",
                "outputpath": "output/"
            }
        }
    }
}
```

## <a name="databricks-notebook-activity-properties"></a>Databricks anteckningsboken Aktivitetsegenskaper

I följande tabell beskrivs JSON-egenskaper som används i JSON-definitionen:

|Egenskap|Beskrivning|Krävs|
|---|---|---|
|namn|Namnet på aktivitet i pipelinen.|Ja|
|description|Text som beskriver hur aktiviteten ska hantera.|Nej|
|typ|Aktivitetstypen är DatabricksNotebook för Databricks anteckningsboken aktiviteten.|Ja|
|linkedServiceName|Namnet på den länkade Databricks-tjänsten som körs på den bärbara datorn Databricks. Mer information om den här länkade tjänsten, se [Compute länkade tjänster](compute-linked-services.md) artikel.|Ja|
|notebookPath|Den absoluta sökvägen till den bärbara datorn körs i arbetsytan Databricks. Den här sökvägen måste börja med ett snedstreck.|Ja|
|baseParameters|En matris med nyckel-värdepar. Grundläggande parametrar kan användas för varje aktivitet körs. Om den bärbara datorn tar en parameter som inte anges, används standardvärdet från den bärbara datorn. Hitta mer om parametrar i [Databricks anteckningsböcker](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|Nej|
