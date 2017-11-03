---
title: "Transformera data med hjälp av U-SQL - skript i Azure | Microsoft Docs"
description: "Lär dig hur du bearbetar eller Transformera data genom att köra U-SQL-skript på Azure Data Lake Analytics-tjänsten för beräkning."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: shengc
ms.openlocfilehash: 5e54464ceabfe1fea2af80d63e538bea6a0a50a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Transformera data genom att köra U-SQL-skript på Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-usql-activity.md)
> * [Version 2 – förhandsversion](transform-data-using-data-lake-analytics.md)

En pipeline i ett Azure data factory bearbetar data i länkade storage-tjänster med hjälp av länkade beräknings-tjänster. Den innehåller en serie aktiviteter där varje aktivitet utför en specifik bearbetning. Den här artikeln beskriver den **Data Lake Analytics U-SQL-aktivitet** som kör en **U-SQL** skript på en **Azure Data Lake Analytics** compute länkade tjänsten. 

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [USQL aktivitet i V1](v1/data-factory-usql-activity.md).

Skapa ett Azure Data Lake Analytics-konto innan du skapar en pipeline med en Data Lake Analytics U-SQL-aktivitet. Mer information om Azure Data Lake Analytics, se [Kom igång med Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md).


## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics länkad tjänst
Du skapar en **Azure Data Lake Analytics** länkad tjänst för att länka ett Azure Data Lake Analytics compute-tjänst till ett Azure data factory. Data Lake Analytics U-SQL-aktivitet i pipelinen refererar till den här länkade tjänsten. 

Följande tabell innehåller beskrivningar för allmänna egenskaper som används i JSON-definitionen. 

| Egenskap                 | Beskrivning                              | Krävs                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **typ**                 | Typegenskapen bör anges till: **AzureDataLakeAnalytics**. | Ja                                      |
| **Kontonamn**          | Azure Data Lake Analytics-kontonamn.  | Ja                                      |
| **dataLakeAnalyticsUri** | Azure Data Lake Analytics-URI.           | Nej                                       |
| **prenumerations-ID**       | Azure prenumerations-ID                    | Nej (om den inte anges data factory-prenumeration används). |
| **resourceGroupName**    | Azure resursgruppens namn                | Nej (om inget annat anges, resursgruppen av datafabriken används). |

### <a name="service-principal-authentication"></a>Autentisering av tjänstens huvudnamn
Länkad Azure Data Lake Analytics-tjänsten kräver ett huvudnamn autentiseringen av tjänsten att ansluta till Azure Data Lake Analytics-tjänsten. Om du vill använda huvudnamn autentiseringen av tjänsten registrera en Programenhet i Azure Active Directory (Azure AD) och bevilja åtkomst till både Data Lake Analytics och Data Lake Store används. Detaljerade anvisningar finns i [tjänst-till-tjänst autentisering](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Anteckna följande värden som du använder för att definiera den länkade tjänsten:
* Program-ID:t
* Nyckeln för programmet 
* Klient-ID:t

Använd service principal autentisering genom att ange följande egenskaper:

| Egenskap                | Beskrivning                              | Krävs |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Ange programmets klient-ID.     | Ja      |
| **servicePrincipalKey** | Ange programmets nyckeln.           | Ja      |
| **klient**              | Ange information om klient (domain name eller klient ID) under där programmet finns. Du kan hämta den hovrar muspekaren i det övre högra hörnet i Azure-portalen. | Ja      |

**Exempel: Service principal autentisering**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics URI",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }       
    }
}
```

Mer information om den länkade tjänsten finns [Compute länkade tjänster](compute-linked-services.md).

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-aktivitet
Följande JSON-fragment definierar en pipeline med en Data Lake Analytics U-SQL-aktivitet. Aktivitetsdefinitionen har en referens till länkade Azure Data Lake Analytics-tjänsten som du skapade tidigare. Om du vill köra ett Data Lake Analytics U-SQL-skript, skickar Data Factory skriptet som du har angett till Data Lake Analytics och kräver indata och utdata har definierats i skriptet för Data Lake Analytics att hämta och utdata. 

```json
{
    "name": "ADLA U-SQL Activity",
    "description": "description",
    "type": "DataLakeAnalyticsU-SQL",
    "linkedServiceName": {
        "referenceName": "AzureDataLakeAnalyticsLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "LinkedServiceofAzureBlobStorageforscriptPath",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
        "degreeOfParallelism": 3,
        "priority": 100,
        "parameters": {
            "in": "/datalake/input/SearchLog.tsv",
            "out": "/datalake/output/Result.tsv"
        }
    }   
}
```

I följande tabell beskrivs namn och beskrivningar av egenskaper som är specifika för den här aktiviteten. 

| Egenskap            | Beskrivning                              | Krävs |
| :------------------ | :--------------------------------------- | :------- |
| namn                | Namnet på aktivitet i pipelinen     | Ja      |
| description         | Text som beskriver hur aktiviteten ska hantera.  | Nej       |
| typ                | För Data Lake Analytics U-SQL-aktivitet, aktivitetstypen är **DataLakeAnalyticsU SQL**. | Ja      |
| linkedServiceName   | Länkade tjänsten Azure Data Lake Analytics. Mer information om den här länkade tjänsten, se [Compute länkade tjänster](compute-linked-services.md) artikel.  |Ja       |
| ScriptPath          | Sökvägen till mappen som innehåller U-SQL-skript. Namnet på filen är skiftlägeskänslig. | Ja      |
| scriptLinkedService | Länkade tjänst som länkar den lagring som innehåller skriptet till data factory | Ja      |
| degreeOfParallelism | Maximalt antal noder samtidigt används för att köra jobbet. | Nej       |
| Prioritet            | Anger vilka jobb av alla köas ska väljas att köras först. Ju lägre nummer, desto högre prioritet. | Nej       |
| parameters          | Parametrar för U-SQL-skript          | Nej       |
| runtimeVersion      | Körtidsversion U-SQL-motorn att använda | Nej       |
| compilationMode     | <p>Kompileringsläge för U-SQL. Måste vara ett av följande värden: **semantiska:** endast utföra semantiska kontroller och nödvändiga hälsokontroller **fullständig:** utföra fullständig kompilering, inklusive syntaxkontrollen, optimering, kodgenerering osv., **SingleBox:** utföra fullständig sammanställning med TargetType inställningen till SingleBox. Om du inte anger ett värde för den här egenskapen anger servern optimala kompilering. | Nej |

Data Factory skickar Se [SearchLogProcessing.txt skript Definition](#sample-u-sql-script) skript-definition. 

## <a name="sample-u-sql-script"></a>Exempelskript för U-SQL

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int,
            Urls            string,
            ClickedUrls     string
    FROM @in
    USING Extractors.Tsv(nullEscape:"#NULL#");

@rs1 =
    SELECT Start, Region, Duration
    FROM @searchlog
WHERE Region == "en-gb";

@rs1 =
    SELECT Start, Region, Duration
    FROM @rs1
    WHERE Start <= DateTime.Parse("2012/02/19");

OUTPUT @rs1   
    TO @out
      USING Outputters.Tsv(quoting:false, dateTimeFormat:null);
```

I ovanstående exempel på skript, indata och utdata till skriptet har definierats i  **@in**  och  **@out**  parametrar. Värdena för  **@in**  och  **@out**  parametrar i U-SQL-skriptet skickas dynamiskt av Data Factory med hjälp av avsnittet-Parametrar'. 

Du kan ange andra egenskaper, till exempel degreeOfParallelism och prioritet samt i pipeline-definitionen för de jobb som körs på Azure Data Lake Analytics-tjänsten.

## <a name="dynamic-parameters"></a>Dynamiska parametrar
I exemplet pipeline-definition tilldelas in och ut parametrar med hårdkodade värden. 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

Det är möjligt att använda dynamiska parametrar i stället. Exempel: 

```json
"parameters": {
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

I det här fallet fortfarande tas upp inkommande filer från mappen /datalake/input och utdatafiler skapas i mappen /datalake/output. Filnamnen är dynamiska baserat på starttid sektorn.  

## <a name="next-steps"></a>Nästa steg
Se följande artiklar som förklarar hur du Transformera data på andra sätt: 

* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [Pig-aktivitet](transform-data-using-hadoop-pig.md)
* [MapReduce-aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming activity](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Batch Execution aktivitet](transform-data-using-machine-learning.md)
* [Aktiviteten lagrad procedur](transform-data-using-stored-procedure.md)
