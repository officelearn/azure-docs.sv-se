---
title: Omvandla data med Databricks Jar
description: Lär dig hur du bearbetar eller omvandlar data genom att köra en Databricks Jar.
services: data-factory
documentationcenter: ''
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
manager: shwang
ms.date: 03/15/2018
ms.openlocfilehash: 20858069b745beeaf64951c4ef23c2eb85251985
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74929121"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Omvandla data genom att köra en Jar-aktivitet i Azure Databricks

Azure Databricks Jar-aktivitet i en [Data Factory-pipeline](concepts-pipelines-activities.md) kör en Spark Jar i ditt Azure Databricks-kluster. Den här artikeln bygger på artikeln [om dataomvandlingsaktiviteter,](transform-data.md) som ger en allmän översikt över dataomvandling och de omvandlingsaktiviteter som stöds.Azure Databricks är en hanterad plattform för att köra Apache Spark.

Om du vill se en introduktion och demonstration av den här funktionen rekommenderar vi följande videoklipp (11 minuter):

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-jar-activity-definition"></a>Definition av Databricks Jar-aktivitet

Här är provet JSON definition av en Databricks Jar Aktivitet:

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

## <a name="databricks-jar-activity-properties"></a>Aktivitetsegenskaper för Databricks Jar

I följande tabell beskrivs de JSON-egenskaper som används i JSON-definitionen:

|Egenskap|Beskrivning|Krävs|
|:--|---|:-:|
|namn|Namnet på aktiviteten på pipelinen.|Ja|
|description|Text som beskriver vad aktiviteten gör.|Inga|
|typ|För Databricks Jar Activity är aktivitetstypen DatabricksSparkJar.|Ja|
|linkedServiceName|Namn på den Länkade databricks-tjänsten som jar-aktiviteten körs på. Mer information om den länkade tjänsten finns i artikel [om beräkningslänkade tjänster.](compute-linked-services.md) |Ja|
|mainClassName (huvudklassnamn)|Det fullständiga namnet på den klass som innehåller huvudmetoden som ska utföras. Den här klassen måste finnas i en JAR som tillhandahålls som ett bibliotek.|Ja|
|parameters|Parametrar som skickas till huvudmetoden.  Det här är en rad strängar.|Inga|
|bibliotek|En lista över bibliotek som ska installeras i klustret som ska köra jobbet. Det kan vara en matris med <sträng, objekt>|Ja (minst en som innehåller metoden mainClassName)|

> [!NOTE]
> **Känt problem** - När du använder samma [interaktiva kluster](compute-linked-services.md#example---using-existing-interactive-cluster-in-databricks) för att köra samtidiga Databricks Jar-aktiviteter (utan omstart av klustret) finns det ett känt problem i Databricks där i parametrar för den första aktiviteten kommer att användas av följande aktiviteter också. Därför leder till att felaktiga parametrar skickas till efterföljande jobb. För att minska detta använder du ett [jobbkluster](compute-linked-services.md#example---using-new-job-cluster-in-databricks) i stället. 

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

Vanligtvis lagras Jar-biblioteken under dbfs:/FileStore/jars när du använder användargränssnittet. Du kan lista hela CLI: *databricks fs ls dbfs:/FileStore/job-jars* 



#### <a name="copy-library-using-databricks-cli"></a>[Kopiera bibliotek med Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)
Använd Databricks CLI [(installationssteg)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Exempel - kopiera JAR till dbfs: *dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar*
