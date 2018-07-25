---
title: Omvandla data med Databricks Notebook - Azure | Microsoft Docs
description: Lär dig hur du bearbeta eller omvandla data genom att köra en Databricks notebook.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/15/2018
ms.author: douglasl
ms.openlocfilehash: 5f21f33678b8cf09d9dbd8966d42b1a5ebac9ffb
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224660"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Transformera data genom att köra en Databricks notebook

Azure Databricks Notebook-aktiviteten i en [Data Factory-pipeline](concepts-pipelines-activities.md) kör en Databricks notebook i Azure Databricks-arbetsytan. Den här artikeln bygger vidare på den [datatransformeringsaktiviteter](transform-data.md) artikel som anger en allmän översikt över Dataomvandling och stöds transformeringsaktiviteter. Azure Databricks är en hanterad plattform för att köra Apache Spark.

## <a name="databricks-notebook-activity-definition"></a>Definitionen för Databricks Notebook-aktivitet

Här är exempel JSON-definition för en Databricks Notebook-aktiviteten:

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

I följande tabell beskrivs de JSON-egenskaper som används i JSON-definition:

|Egenskap |Beskrivning|Krävs|
|---|---|---|
|namn|Namnet på aktiviteten i pipelinen.|Ja|
|beskrivning|Text som beskriver hur aktiviteten ska hantera.|Nej|
|typ|För Databricks Notebook-aktiviteten är aktivitetstypen DatabricksNotebook.|Ja|
|linkedServiceName|Namnet på den länkade tjänsten för Databricks som Databricks notebook körs. Mer information om den här länkade tjänsten, se [länkade tjänster för Compute](compute-linked-services.md) artikeln.|Ja|
|notebookPath|Den absoluta sökvägen till anteckningsboken för att köras i Databricks-arbetsytan. Den här sökvägen måste börja med ett snedstreck.|Ja|
|baseParameters|En matris med nyckel / värde-par. Grundläggande parametrar kan användas för varje aktivitet kör. Om anteckningsboken tar en parameter som inte anges, används standardvärdet från anteckningsboken. Hitta mer information om parametrar i [Databricks-anteckningsböcker](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|Nej|
|Bibliotek|En lista med bibliotek som ska installeras på det kluster som ska utföra jobbet. Det kan vara en matris med \<sträng, objekt->.|Nej|


## <a name="supported-libraries-for-databricks-activities"></a>Stöds-bibliotek för Databricks-aktiviteter

I ovanstående Databricks aktivitetsdefinitionen, anger du dessa typer av dokumentbibliotek: *jar*, *ägg*, *maven*, *pypi*,  *cran*.

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
                "repo": "http://cran.us.r-project.org"
            }
        }
    ]
}

```

Mer information finns i den [dokumentation för Databricks](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) för bibliotekstyper.

## <a name="how-to-upload-a-library-in-databricks"></a>Hur du överför ett bibliotek i Databricks

#### <a name="using-databricks-workspace-uihttpsdocsazuredatabricksnetuser-guidelibrarieshtmlcreate-a-library"></a>[Med hjälp av Användargränssnittet för Databricks-arbetsyta](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Om du vill ha antingen sökvägen till biblioteket har lagts till med hjälp av Användargränssnittet kan du använda den [Databricks CLI (installation)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Normalt Jar-bibliotek lagras under dbfs: / FileStore/JAR: er när du använder Användargränssnittet. Du kan lista alla via CLI: *databricks fs ls dbfs: / FileStore/JAR-filer*.



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Kopiera bibliotek med Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Exempel: *databricks fs cp SparkPi-sammansättningen-0.1.jar dbfs: / FileStore/JAR-filer*
