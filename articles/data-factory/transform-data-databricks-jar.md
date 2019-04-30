---
title: Omvandla data med Databricks Jar - Azure | Microsoft Docs
description: Lär dig hur du bearbeta eller omvandla data genom att köra en Databricks-Jar.
services: data-factory
documentationcenter: ''
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/15/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: d299a785d50657ef40c0c49cb2dce33b8939fd02
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60860997"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Transformera data genom att köra en Jar-aktivitet i Azure Databricks

Azure Databricks Jar-aktiviteten i en [Data Factory-pipeline](concepts-pipelines-activities.md) kör ett Spark-Jar i Azure Databricks-klustret. Den här artikeln bygger vidare på den [datatransformeringsaktiviteter](transform-data.md) artikel som anger en allmän översikt över Dataomvandling och stöds transformeringsaktiviteter. Azure Databricks är en hanterad plattform för att köra Apache Spark.

Om du vill se en introduktion och demonstration av den här funktionen rekommenderar vi följande videoklipp (11 minuter):

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-jar-activity-definition"></a>Databricks Jar aktivitetsdefinition

Här är exempel JSON-definition för en Databricks Jar aktivitet:

```json
{
    "name": "SparkJarActivity",
    "type": "DatabricksSparkJar",
    "linkedServiceName": {
        "referenceName": "AzureDatabricks",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mainClassName": "org.apache.spark.examples.SparkPi",
        "parameters": [ "10" ],
        "libraries": [
            {
                "jar": "dbfs:/docs/sparkpi.jar"
            }
        ]
    }
}

```

## <a name="databricks-jar-activity-properties"></a>Egenskaper för Databricks Jar-aktivitet

I följande tabell beskrivs de JSON-egenskaper som används i JSON-definition:

|Egenskap |Beskrivning|Krävs|
|:--|---|:-:|
|namn|Namnet på aktiviteten i pipelinen.|Ja|
|description|Text som beskriver hur aktiviteten ska hantera.|Nej|
|typ|För Databricks Jar-aktiviteten är aktivitetstypen DatabricksSparkJar.|Ja|
|linkedServiceName|Namnet på den länkade tjänsten för Databricks som Jar-aktiviteten körs. Mer information om den här länkade tjänsten, se [länkade tjänster för Compute](compute-linked-services.md) artikeln.|Ja|
|mainClassName|Det fullständiga namnet på den klass som innehåller main-metoden som ska köras. Den här klassen måste finnas i en JAR som tillhandahålls som ett bibliotek.|Ja|
|parameters|Parametrar som skickas till main-metoden.  Det här är en matris med strängar.|Nej|
|Bibliotek|En lista med bibliotek som ska installeras på det kluster som ska utföra jobbet. Det kan vara en matris med < sträng, objekt->|Ja (minst en som innehåller metoden mainClassName)|

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

Vanligtvis Jar-bibliotek lagras under dbfs: / FileStore/JAR: er när du använder Användargränssnittet. Du kan lista alla via CLI: *databricks fs ls dbfs: / FileStore/jobb-JAR-filer* 



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Kopiera bibliotek med Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)
Använda Databricks CLI [(installationsstegen)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Exempel – kopiera JAR-filen till dbfs: *dbfs cp SparkPi-sammansättningen-0.1.jar dbfs:/docs/sparkpi.jar*
