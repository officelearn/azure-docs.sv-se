---
title: Omvandla data med databricks bärbar dator
description: Lär dig hur du bearbetar eller omvandlar data genom att köra en Databricks-anteckningsbok.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: nabhishek
ms.author: abnarain
manager: shwang
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: c7a2aec35511ef066033c3d6462143ac31660e76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74923063"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Omvandla data genom att köra en databricks-anteckningsbok

Azure Databricks Notebook Activity i en [Data Factory-pipeline](concepts-pipelines-activities.md) kör en Databricks-anteckningsbok på din Azure Databricks-arbetsyta. Den här artikeln bygger på artikeln [om dataomvandlingsaktiviteter,](transform-data.md) som ger en allmän översikt över dataomvandling och de omvandlingsaktiviteter som stöds.Azure Databricks är en hanterad plattform för att köra Apache Spark.

## <a name="databricks-notebook-activity-definition"></a>Databricks Anteckningsbok aktivitetsdefinition

Här är exemplet JSON definition av en Databricks Notebook Activity:

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

## <a name="databricks-notebook-activity-properties"></a>Aktivitetsegenskaper för Databricks Bärbar dator

I följande tabell beskrivs de JSON-egenskaper som används i JSON-definitionen:

|Egenskap|Beskrivning|Krävs|
|---|---|---|
|namn|Namnet på aktiviteten på pipelinen.|Ja|
|description|Text som beskriver vad aktiviteten gör.|Inga|
|typ|För Databricks Notebook Activity är aktivitetstypen DatabricksNotebook.|Ja|
|linkedServiceName|Namnet på den Databricks Linked Service som den bärbara databricks-anteckningsboken körs på. Mer information om den länkade tjänsten finns i artikel [om beräkningslänkade tjänster.](compute-linked-services.md) |Ja|
|notebookPath (notebookPath)|Den absoluta sökvägen till den anteckningsbok som ska köras i Databricks-arbetsytan. Den här sökvägen måste börja med ett snedstreck.|Ja|
|basParametrar|En matris med nyckelvärdespar. Basparametrar kan användas för varje aktivitetskörning. Om anteckningsboken tar en parameter som inte har angetts används standardvärdet från anteckningsboken. Hitta mer om parametrar i [Databricks Bärbara datorer](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|Inga|
|bibliotek|En lista över bibliotek som ska installeras i klustret som ska köra jobbet. Det kan vara \<en matris med sträng, objekt>.|Inga|


## <a name="supported-libraries-for-databricks-activities"></a>Bibliotek som stöds för Databricks-aktiviteter

I ovanstående Databricks aktivitetsdefinition anger du dessa bibliotekstyper: *burk*, *ägg*, *whl*, *maven*, *pypi*, *cran*.

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

Mer information finns i Dokumentationen för [Databricks](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) för bibliotekstyper.

## <a name="passing-parameters-between-notebooks-and-data-factory"></a>Skicka parametrar mellan anteckningsböcker och Data Factory

Du kan skicka datafabriksparametrar till anteckningsböcker med hjälp av *egenskapen baseParameters* i databricks-aktivitet. 

I vissa fall kan du behöva skicka tillbaka vissa värden från anteckningsboken tillbaka till datafabriken, som kan användas för kontrollflöde (villkorliga kontroller) i datafabriken eller förbrukas av aktiviteter i efterföljande led (storleksgränsen är 2 MB). 

1. I anteckningsboken kan du ringa [dbutils.notebook.exit("returnValue")](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-workflows.html#notebook-workflows-exit) och motsvarande "returnValue" kommer att returneras till datafabriken.

2. Du kan använda utdata i datafabriken med hjälp av uttryck som `'@activity('databricks notebook activity name').output.runOutput'`. 

   > [!IMPORTANT]
   > Om du skickar JSON-objekt kan du hämta värden genom att lägga till egenskapsnamn. Exempel: `'@activity('databricks notebook activity name').output.runOutput.PropertyName'`

## <a name="how-to-upload-a-library-in-databricks"></a>Så här laddar du upp ett bibliotek i Databricks

#### <a name="using-databricks-workspace-ui"></a>[Använda databricks arbetsytans användargränssnitt](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Om du vill hämta sökvägen till dbfs för biblioteket som lagts till med användargränssnittet kan du använda [Databricks CLI (installation)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Vanligtvis lagras Jar-biblioteken under dbfs:/FileStore/jars när användargränssnittet används. Du kan lista hela CLI: *databricks fs ls dbfs:/FileStore/jars*.



#### <a name="copy-library-using-databricks-cli"></a>[Kopiera bibliotek med Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Exempel: *databricks fs cp SparkPi-assembly-0.1.jar dbfs:/FileStore/jars*
