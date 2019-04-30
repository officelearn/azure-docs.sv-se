---
title: Omvandla data med Databricks Python – Azure | Microsoft Docs
description: Lär dig hur du bearbeta eller omvandla data genom att köra en Databricks-Python.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/15/2018
ms.author: douglasl
ms.openlocfilehash: 60aafd983d1c21777276683a8685376a247d11f5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60589213"
---
# <a name="transform-data-by-running-a-python-activity-in-azure-databricks"></a>Transformera data genom att köra en Python-aktivitet i Azure Databricks

Azure Databricks Python-aktiviteten i en [Data Factory-pipeline](concepts-pipelines-activities.md) körs en Python-fil i ditt Azure Databricks-kluster. Den här artikeln bygger vidare på den [datatransformeringsaktiviteter](transform-data.md) artikel som anger en allmän översikt över Dataomvandling och stöds transformeringsaktiviteter. Azure Databricks är en hanterad plattform för att köra Apache Spark.

Om du vill se en introduktion och demonstration av den här funktionen rekommenderar vi följande videoklipp (11 minuter):

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-python-activity-definition"></a>Databricks Python aktivitetsdefinition

Här är exempel JSON-definition för en Databricks Python aktivitet:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksSparkPython",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "pythonFile": "dbfs:/docs/pi.py",
            "parameters": [
                "10"
            ],
            "libraries": [
                {
                    "pypi": {
                        "package": "tensorflow"
                    }
                }
            ]
        }
    }
}
```

## <a name="databricks-python-activity-properties"></a>Databricks Python Aktivitetsegenskaper

I följande tabell beskrivs de JSON-egenskaper som används i JSON-definition:

|Egenskap |Beskrivning|Krävs|
|---|---|---|
|namn|Namnet på aktiviteten i pipelinen.|Ja|
|description|Text som beskriver hur aktiviteten ska hantera.|Nej|
|typ|För Databricks Python-aktivitet är aktivitetstypen DatabricksSparkPython.|Ja|
|linkedServiceName|Namnet på den länkade tjänsten för Databricks där Python-aktiviteten körs. Mer information om den här länkade tjänsten, se [länkade tjänster för Compute](compute-linked-services.md) artikeln.|Ja|
|pythonFile|URI för Python-fil som ska köras. Endast DBFS sökvägar stöds.|Ja|
|parameters|Kommandoradsparametrar som ska skickas till Python-filen. Det här är en matris med strängar.|Nej|
|Bibliotek|En lista med bibliotek som ska installeras på det kluster som ska utföra jobbet. Det kan vara en matris med < sträng, objekt->|Nej|

## <a name="supported-libraries-for-databricks-activities"></a>Stöds-bibliotek för databricks-aktiviteter

I ovanstående Databricks aktivitetsdefinitionen anger du dessa typer av dokumentbibliotek: *jar*, *ägg*, *maven*, *pypi*,  *cran*.

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

Mer information finns [dokumentation för Databricks](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) för bibliotekstyper.

## <a name="how-to-upload-a-library-in-databricks"></a>Hur du överför ett bibliotek i Databricks

#### <a name="using-databricks-workspace-uihttpsdocsazuredatabricksnetuser-guidelibrarieshtmlcreate-a-library"></a>[Med hjälp av Användargränssnittet för Databricks-arbetsyta](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Du kan använda för att få antingen sökvägen till biblioteket har lagts till med hjälp av Användargränssnittet kan [Databricks CLI (installation)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Vanligtvis Jar-bibliotek lagras under dbfs: / FileStore/JAR: er när du använder Användargränssnittet. Du kan lista alla via CLI: *databricks fs ls dbfs: / FileStore/JAR-filer* 



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Kopiera bibliotek med Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Exempel: *databricks fs cp SparkPi-sammansättningen-0.1.jar dbfs: / FileStore/JAR-filer*