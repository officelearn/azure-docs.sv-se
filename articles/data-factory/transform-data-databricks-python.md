---
title: Omvandla data med Databricks Python
description: Lär dig hur du bearbetar eller omvandlar data genom att köra en Databricks Python.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/15/2018
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: anandsub
ms.openlocfilehash: e102b14d8471a19564f66edc27cc328c2a789c98
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414605"
---
# <a name="transform-data-by-running-a-python-activity-in-azure-databricks"></a>Omvandla data genom att köra en Python-aktivitet i Azure Databricks
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


Azure Databricks Python-aktivitet i en [Data Factory-pipeline](concepts-pipelines-activities.md) kör en Python-fil i ditt Azure Databricks-kluster. Den här artikeln bygger på artikeln [om dataomvandlingsaktiviteter,](transform-data.md) som ger en allmän översikt över dataomvandling och de omvandlingsaktiviteter som stöds.Azure Databricks är en hanterad plattform för att köra Apache Spark.

Om du vill se en introduktion och demonstration av den här funktionen rekommenderar vi följande videoklipp (11 minuter):

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-python-activity-definition"></a>Databricks Python-aktivitetsdefinition

Här är exempel JSON-definitionen av en Databricks Python-aktivitet:

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

## <a name="databricks-python-activity-properties"></a>Aktivitetsegenskaper för Databricks Python

I följande tabell beskrivs de JSON-egenskaper som används i JSON-definitionen:

|Egenskap|Beskrivning|Krävs|
|---|---|---|
|namn|Namnet på aktiviteten på pipelinen.|Ja|
|description|Text som beskriver vad aktiviteten gör.|Inga|
|typ|För Databricks Python Activity är aktivitetstypen DatabricksSparkPython.|Ja|
|linkedServiceName|Namnet på den länkade databricks-tjänsten som Python-aktiviteten körs på. Mer information om den länkade tjänsten finns i artikel [om beräkningslänkade tjänster.](compute-linked-services.md) |Ja|
|pythonFile|URI-filen för Python-filen som ska köras. Endast DBFS-sökvägar stöds.|Ja|
|parameters|Kommandoradsparametrar som skickas till Python-filen. Det här är en rad strängar.|Inga|
|bibliotek|En lista över bibliotek som ska installeras i klustret som ska köra jobbet. Det kan vara en matris med <sträng, objekt>|Inga|

## <a name="supported-libraries-for-databricks-activities"></a>Bibliotek som stöds för databricks-aktiviteter

I ovanstående Databricks aktivitetsdefinition anger du dessa bibliotekstyper: *burk*, *ägg*, *maven*, *pypi*, *cran*.

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

Mer information finns i Dokumentationen för [Databricks](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) för bibliotekstyper.

## <a name="how-to-upload-a-library-in-databricks"></a>Så här laddar du upp ett bibliotek i Databricks

#### <a name="using-databricks-workspace-ui"></a>[Använda databricks arbetsytans användargränssnitt](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Om du vill hämta sökvägen till dbfs för biblioteket som lagts till med användargränssnittet kan du använda [Databricks CLI (installation)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Vanligtvis lagras Jar-biblioteken under dbfs:/FileStore/jars när du använder användargränssnittet. Du kan lista hela CLI: *databricks fs ls dbfs:/FileStore/jars* 



#### <a name="copy-library-using-databricks-cli"></a>[Kopiera bibliotek med Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Exempel: *databricks fs cp SparkPi-assembly-0.1.jar dbfs:/FileStore/jars*