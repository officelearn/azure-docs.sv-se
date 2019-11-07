---
title: Transformera data med Databricks python – Azure
description: Lär dig hur du bearbetar eller transformerar data genom att köra en Databricks python.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/15/2018
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: craigg
ms.openlocfilehash: 47654e8183dab120376f94df63d7664ead5dd580
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683945"
---
# <a name="transform-data-by-running-a-python-activity-in-azure-databricks"></a>Transformera data genom att köra en python-aktivitet i Azure Databricks

Azure Databricks python-aktivitet i en [Data Factory pipeline](concepts-pipelines-activities.md) kör en python-fil i Azure Databricks klustret. Den här artikeln bygger på [data Transformations aktiviteter](transform-data.md) artikeln, som visar en allmän översikt över Datatransformeringen och de omvandlings aktiviteter som stöds. Azure Databricks är en hanterad plattform för att köra Apache Spark.

Om du vill se en introduktion och demonstration av den här funktionen rekommenderar vi följande videoklipp (11 minuter):

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-python-activity-definition"></a>Databricks python-aktivitets definition

Här är exempel-JSON-definitionen för en Databricks python-aktivitet:

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

## <a name="databricks-python-activity-properties"></a>Egenskaper för Databricks python-aktivitet

I följande tabell beskrivs de JSON-egenskaper som används i JSON-definitionen:

|Egenskap|Beskrivning|Krävs|
|---|---|---|
|namn|Namnet på aktiviteten i pipelinen.|Ja|
|description|Text som beskriver vad aktiviteten gör.|Nej|
|typ|Aktivitets typen är DatabricksSparkPython för Databricks python-aktivitet.|Ja|
|linkedServiceName|Namnet på den länkade Databricks-tjänst som python-aktiviteten körs på. Mer information om den här länkade tjänsten finns i artikeln [Compute Linked services](compute-linked-services.md) .|Ja|
|pythonFile|URI för python-filen som ska köras. Endast DBFS-sökvägar stöds.|Ja|
|parameters|Kommando rads parametrar som ska skickas till python-filen. Detta är en sträng mat ris.|Nej|
|biblioteksfliken|En lista med bibliotek som ska installeras i klustret som ska köra jobbet. Det kan vara en matris med < sträng, objekt >|Nej|

## <a name="supported-libraries-for-databricks-activities"></a>Bibliotek som stöds för databricks-aktiviteter

I ovanstående Databricks-aktivitets definition anger du dessa biblioteks typer: *jar*, *ägg*, *maven*, *pypi*, *cran*.

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

Mer information finns i [Databricks-dokumentationen](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) för biblioteks typer.

## <a name="how-to-upload-a-library-in-databricks"></a>Ladda upp ett bibliotek i Databricks

#### <a name="using-databricks-workspace-uihttpsdocsazuredatabricksnetuser-guidelibrarieshtmlcreate-a-library"></a>[Använda användar gränssnittet för Databricks-arbetsyta](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Du kan hämta dBFS-sökvägen till biblioteket som lagts till med hjälp av användar gränssnittet genom att använda [DATABRICKS CLI (installation)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Vanligt vis lagras jar-biblioteken under dBFS:/FileStore/jar v7 när du använder användar gränssnittet. Du kan visa alla via CLI: *databricks FS LS dBFS:/FileStore/jar v7* 



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Kopiera bibliotek med Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Exempel: *databricks FS CP sparkpi-Assembly-0,1. jar dBFS:/FileStore/jar v7*