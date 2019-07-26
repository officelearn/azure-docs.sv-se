---
title: Transformera data med Databricks Notebook – Azure | Microsoft Docs
description: Lär dig hur du bearbetar eller transformerar data genom att köra en Databricks Notebook.
services: data-factory
documentationcenter: ''
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/15/2018
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: 2bc8b84d4b98036acc93788dee88444786df139e
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335851"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Transformera data genom att köra en Databricks Notebook

Azure Databricks Notebook-aktiviteten i en [Data Factory-pipeline](concepts-pipelines-activities.md) kör en Databricks-anteckningsbok i din Azure Databricks-arbetsyta. Den här artikeln bygger på artikeln [data omvandlings aktiviteter](transform-data.md) , som visar en allmän översikt över Datatransformeringen och de omvandlings aktiviteter som stöds. Azure Databricks är en hanterad plattform för att köra Apache Spark.

## <a name="databricks-notebook-activity-definition"></a>Aktivitets definition för Databricks Notebook

Här är exempel-JSON-definitionen för en Databricks Notebook-aktivitet:

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
            },
            "libraries": [
                {
                "jar": "dbfs:/docs/library.jar"
                }
            ]
        }
    }
}
```

## <a name="databricks-notebook-activity-properties"></a>Egenskaper för Databricks Notebook-aktivitet

I följande tabell beskrivs de JSON-egenskaper som används i JSON-definitionen:

|Egenskap|Beskrivning|Obligatorisk|
|---|---|---|
|name|Namnet på aktiviteten i pipelinen.|Ja|
|description|Text som beskriver vad aktiviteten gör.|Nej|
|type|Aktivitets typen är DatabricksNotebook för Databricks Notebook-aktivitet.|Ja|
|linkedServiceName|Namnet på den länkade Databricks-tjänsten som Databricks-anteckningsboken körs på. Mer information om den här länkade tjänsten finns i artikeln [Compute-länkade tjänster](compute-linked-services.md) .|Ja|
|notebookPath|Den absoluta sökvägen till antecknings boken som ska köras i Databricks-arbetsytan. Sökvägen måste börja med ett snedstreck.|Ja|
|baseParameters|En matris med nyckel/värde-par. Du kan använda bas parametrar för varje aktivitets körning. Om antecknings boken använder en parameter som inte har angetts används standardvärdet från antecknings boken. Hitta mer information om parametrar i [Databricks Notebooks](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|Nej|
|biblioteksfliken|En lista med bibliotek som ska installeras i klustret som ska köra jobbet. Det kan vara en matris med \<sträng, objekt >.|Nej|


## <a name="supported-libraries-for-databricks-activities"></a>Bibliotek som stöds för Databricks-aktiviteter

I Databricks-aktivitets definitionen anger du dessa biblioteks typer: *jar*, *ägg*, *WHL*, *maven*, *pypi*, *cran*.

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
    {
            "whl": "dbfs:/mnt/libraries/mlflow-0.0.1.dev0-py2-none-any.whl"
        },
        {
            "whl": "dbfs:/mnt/libraries/wheel-libraries.wheelhouse.zip"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

Mer information finns i Databricks- [dokumentationen](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) för biblioteks typer.

## <a name="how-to-upload-a-library-in-databricks"></a>Ladda upp ett bibliotek i Databricks

#### <a name="using-databricks-workspace-uihttpsdocsazuredatabricksnetuser-guidelibrarieshtmlcreate-a-library"></a>[Använda användar gränssnittet för Databricks-arbetsyta](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Du kan hämta dBFS-sökvägen till biblioteket som lagts till med hjälp av användar gränssnittet genom att använda [DATABRICKS CLI (installation)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Jar-bibliotek lagras vanligt vis under dBFS:/FileStore/jar v7 när användar gränssnittet används. Du kan visa alla via CLI: *databricks FS LS dBFS:/FileStore/jar v7*.



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Kopiera bibliotek med Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Exempel: *databricks FS CP sparkpi-Assembly-0,1. jar dBFS:/FileStore/jar v7*
