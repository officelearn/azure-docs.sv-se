---
title: Transformera data med hjälp av U-SQL-skript – Azure | Microsoft Docs
description: Lär dig hur du bearbeta eller omvandla data genom att köra U-SQL-skript på Azure Data Lake Analytics-databearbetningstjänst.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: abnarain
ms.openlocfilehash: d5b074fcf182bcc9bf4dc17ba21215d27e13cbdd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60888443"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Transformera data genom att köra U-SQL-skript på Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-usql-activity.md)
> * [Aktuell version](transform-data-using-data-lake-analytics.md)

En pipeline i Azure data factory bearbetar data i länkade storage-tjänster med hjälp av länkade Beräkningstjänster. Den innehåller en serie aktiviteter där varje aktivitet utför en viss bearbetning av åtgärd. Den här artikeln beskriver den **Data Lake Analytics U-SQL-aktivitet** som kör en **U-SQL** skript på en **Azure Data Lake Analytics** compute länkad tjänst. 

Skapa ett Azure Data Lake Analytics-konto innan du skapar en pipeline med en Data Lake Analytics U-SQL-aktivitet. Läs om Azure Data Lake Analytics i [Kom igång med Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md).


## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics länkad tjänst
Du skapar en **Azure Data Lake Analytics** compute service till en Azure data factory-länkade tjänst som länkar ett Azure Data Lake Analytics. Data Lake Analytics U-SQL-aktivitet i pipelinen refererar till den här länkade tjänsten. 

Följande tabell innehåller beskrivningar av de allmänna egenskaper som används i JSON-definitionen. 

| Egenskap                  | Beskrivning                              | Krävs                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **typ**                 | Type-egenskapen ska anges till: **AzureDataLakeAnalytics**. | Ja                                      |
| **accountName**          | Azure Data Lake Analytics-kontonamn.  | Ja                                      |
| **dataLakeAnalyticsUri** | Azure Data Lake Analytics-URI.           | Nej                                       |
| **Prenumerations-ID**       | Azure prenumerations-ID                    | Nej                                       |
| **resourceGroupName**    | Azure-resursgruppsnamn                | Nej                                       |

### <a name="service-principal-authentication"></a>Autentisering av tjänstens huvudnamn
Länkad Azure Data Lake Analytics-tjänsten kräver en autentisering av tjänstens huvudnamn att ansluta till Azure Data Lake Analytics-tjänsten. Registrera en entitet för program i Azure Active Directory (Azure AD) för att använda autentisering av tjänstens huvudnamn, och ge det åtkomst till både Data Lake Analytics och Data Lake Store som används. Detaljerade anvisningar finns i [tjänst-till-tjänst-autentisering](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Anteckna följande värden som du använder för att definiera den länkade tjänsten:

* Program-ID:t
* Programnyckel 
* Klient-ID:t

Ge tjänstens huvudnamn behörighet till Azure Data Lake Anatlyics med den [guiden Lägg till användare](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#add-a-new-user).

Använd autentisering av tjänstens huvudnamn genom att ange följande egenskaper:

| Egenskap                 | Beskrivning                              | Krävs |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Ange programmets klient-ID.     | Ja      |
| **servicePrincipalKey** | Ange programmets nyckel.           | Ja      |
| **klient**              | Ange klientinformation (domain name eller klient-ID) under där programmet finns. Du kan hämta den håller musen i det övre högra hörnet i Azure Portal. | Ja      |

**Exempel: Autentisering av tjänstens huvudnamn**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "<azure data lake analytics URI>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }       
    }
}
```

Läs mer om den länkade tjänsten i [länkade tjänster för Compute](compute-linked-services.md).

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-aktivitet
Följande JSON-kodfragmentet definierar en pipeline med en Data Lake Analytics U-SQL-aktivitet. Aktivitetsdefinitionen innehåller en referens till den länkade Azure Data Lake Analytics-tjänsten som du skapade tidigare. Om du vill köra ett Data Lake Analytics U-SQL-skript, Data Factory skickar skriptet som du har angett till Data Lake Analytics och nödvändiga indata och utdata har definierats i skriptet för Data Lake Analytics att hämta och utdata. 

```json
{
    "name": "ADLA U-SQL Activity",
    "description": "description",
    "type": "DataLakeAnalyticsU-SQL",
    "linkedServiceName": {
        "referenceName": "<linked service name of Azure Data Lake Analytics>",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "<linked service name of Azure Data Lake Store or Azure Storage which contains the U-SQL script>",
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

I följande tabell beskrivs namn och beskrivningar för egenskaper som är specifika för den här aktiviteten. 

| Egenskap             | Beskrivning                              | Krävs |
| :------------------ | :--------------------------------------- | :------- |
| namn                | Namnet på aktiviteten i pipelinen     | Ja      |
| description         | Text som beskriver hur aktiviteten ska hantera.  | Nej       |
| typ                | För Data Lake Analytics U-SQL-aktivitet, är aktivitetstypen **DataLakeAnalyticsU SQL**. | Ja      |
| linkedServiceName   | Länkad tjänst till Azure Data Lake Analytics. Mer information om den här länkade tjänsten, se [länkade tjänster för Compute](compute-linked-services.md) artikeln.  |Ja       |
| scriptPath          | Sökvägen till mappen som innehåller U-SQL-skriptet. Filens namn är skiftlägeskänsligt. | Ja      |
| scriptLinkedService | Länkad tjänst som länkar den **Azure Data Lake Store** eller **Azure Storage** som innehåller skriptet som data factory | Ja      |
| degreeOfParallelism | Det maximala antalet noder samtidigt används för att köra jobbet. | Nej       |
| prioritet            | Anger vilka jobb av alla som är köade ska väljas att köras först. Ju lägre nummer, desto högre prioritet. | Nej       |
| parameters          | Parametrar som ska skickas till U-SQL-skriptet.    | Nej       |
| runtimeVersion      | Runtime-versionen av U-SQL-motor som ska användas. | Nej       |
| compilationMode     | <p>Kompileringsläge för U-SQL. Måste vara ett av följande värden: **Semantisk:** Endast utföra semantiska kontroller och nödvändiga hälsokontroller **fullständig:** Utföra fullständig kompileringen, inklusive syntaxkontrollen, optimering, kodgenerering, osv., **SingleBox:** Utföra fullständig kompileringen med TargetType inställningen om du vill SingleBox. Om du inte anger ett värde för den här egenskapen anger servern optimala kompileringsläge. | Nej |

Se [SearchLogProcessing.txt](#sample-u-sql-script) för skript-definition. 

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

I ovanstående exempel på skript, indata och utdata till skriptet har definierats i  **\@i** och  **\@ut** parametrar. Värdena för  **\@i** och  **\@ut** parametrar i U-SQL-skriptet som skickas dynamiskt med Data Factory med hjälp av avsnittet ”parameters”. 

Du kan också ange andra egenskaper som degreeOfParallelism och prioritet i din pipelinedefinition för jobb som körs på Azure Data Lake Analytics-tjänsten.

## <a name="dynamic-parameters"></a>Dynamiska parametrar
I exemplet pipeline-definition tilldelas och in parametrar med hårdkodade värden. 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

Det är möjligt att använda dynamiska parametrar i stället. Exempel: 

```json
"parameters": {
    "in": "/datalake/input/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/data.tsv",
    "out": "/datalake/output/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/result.tsv"
}
```

I det här fallet indatafilerna plockas fortfarande upp från mappen /datalake/input och utdatafiler som skapas i mappen /datalake/output. Filnamnen är dynamiskt baserat på den starttid som skickas när pipeline aktiveras.  

## <a name="next-steps"></a>Nästa steg
Se följande artiklar som beskriver hur du omvandlar data på andra sätt: 

* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [Piggningsåtgärd](transform-data-using-hadoop-pig.md)
* [MapReduce-aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming activity](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning-batchkörningsaktivitet](transform-data-using-machine-learning.md)
* [Lagrad proceduraktivitet](transform-data-using-stored-procedure.md)
