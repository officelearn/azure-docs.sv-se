---
title: Omvandla data med U-SQL-skript
description: Lär dig hur du bearbetar eller transformerar data genom att köra U-SQL-skript på Azure Data Lake Analytics-beräkningstjänsten.
services: data-factory
documentationcenter: ''
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2018
ms.openlocfilehash: 257c71f7994b889540ec8cc5d0f384f3f8894f4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74913279"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Transformera data genom att köra U-SQL-skript på Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-usql-activity.md)
> * [Aktuell version](transform-data-using-data-lake-analytics.md)

En pipeline i en Azure-datafabrik bearbetar data i länkade lagringstjänster med hjälp av länkade beräkningstjänster. Den innehåller en sekvens av aktiviteter där varje aktivitet utför en viss bearbetning. I den här artikeln beskrivs **datasjöanalys U-SQL-aktivitet** som kör ett **U-SQL-skript** på en Azure Data Lake Analytics-beräkningslänkad tjänst. **Azure Data Lake Analytics** 

Skapa ett Azure Data Lake Analytics-konto innan du skapar en pipeline med en Data Lake Analytics U-SQL-aktivitet. Mer information om Azure Data Lake Analytics finns [i Komma igång med Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md).


## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics länkad tjänst
Du skapar en **Azure Data Lake Analytics-länkad** tjänst för att länka en Azure Data Lake Analytics-beräkningstjänst till en Azure-datafabrik. Data Lake Analytics U-SQL-aktivitet på pipelinen refererar till den länkade tjänsten. 

I följande tabell finns beskrivningar av de allmänna egenskaper som används i JSON-definitionen. 

| Egenskap                 | Beskrivning                              | Krävs                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **Typ**                 | Typegenskapen ska anges till: **AzureDataLakeAnalytics**. | Ja                                      |
| **kontoNamn**          | Azure Data Lake Analytics-kontonamn.  | Ja                                      |
| **dataLakeAnalyticsUri** | Azure Data Lake Analytics URI.           | Inga                                       |
| **subscriptionId**       | Azure-prenumerations-ID                    | Inga                                       |
| **resursGroupName**    | Azure-resursgruppsnamn                | Inga                                       |

### <a name="service-principal-authentication"></a>Autentisering av tjänstens huvudnamn
Den länkade Azure Data Lake Analytics-tjänsten kräver en autentisering av tjänstens huvudnamn för att ansluta till Azure Data Lake Analytics-tjänsten. Om du vill använda autentisering av tjänstens huvudnamn registrerar du en programentitet i Azure Active Directory (Azure AD) och ger den åtkomst till både DataSjöanalys och DataSjölagring som används. Detaljerade steg finns i [Autentisering](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)från tjänst till tjänst . Anteckna följande värden, som du använder för att definiera den länkade tjänsten:

* Program-ID:t
* Programnyckel 
* Klient-ID:t

Ge tjänstens huvudbehörighet till Din Anatlyik för Azure Data Lake med [hjälp av guiden Lägg till användare](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#add-a-new-user).

Använd autentisering av tjänstens huvudnamn genom att ange följande egenskaper:

| Egenskap                | Beskrivning                              | Krävs |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Ange programmets klient-ID.     | Ja      |
| **servicePrincipalKey** | Ange programmets nyckel.           | Ja      |
| **Hyresgästen**              | Ange klientinformation (domännamn eller klient-ID) som programmet finns under. Du kan hämta den genom att hålla musen i det övre högra hörnet av Azure-portalen. | Ja      |

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

Mer information om den länkade tjänsten finns i [Beräkna länkade tjänster](compute-linked-services.md).

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-aktivitet
Följande JSON-kodavsnitt definierar en pipeline med en Data Lake Analytics U-SQL-aktivitet. Aktivitetsdefinitionen har en referens till den Azure Data Lake Analytics-länkade tjänst som du skapade tidigare. Om du vill köra ett U-SQL-dataskript för DataSjöanalys skickar Data Factory skriptet som du har angett till DataSjöanalysen, och de indata och utdata som krävs definieras i skriptet för DataSjöanalys för att hämta och mata ut. 

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

I följande tabell beskrivs namn och beskrivningar av egenskaper som är specifika för den här aktiviteten. 

| Egenskap            | Beskrivning                              | Krävs |
| :------------------ | :--------------------------------------- | :------- |
| namn                | Namnet på den verksamhet som är på gång     | Ja      |
| description         | Text som beskriver vad aktiviteten gör.  | Inga       |
| typ                | För Data Lake Analytics U-SQL-aktivitet är **aktivitetstypen DataLakeAnalyticsU-SQL**. | Ja      |
| linkedServiceName   | Länkad tjänst till Azure Data Lake Analytics. Mer information om den länkade tjänsten finns i artikel [om beräkningslänkade tjänster.](compute-linked-services.md)  |Ja       |
| scriptPath          | Sökväg till mapp som innehåller U-SQL-skriptet. Filens namn är skiftlägeskänsligt. | Ja      |
| scriptLinkedService | Länkad tjänst som länkar **Azure Data Lake Store** eller Azure **Storage** som innehåller skriptet till datafabriken | Ja      |
| gradAvParallelism | Det maximala antalet noder som används samtidigt för att köra jobbet. | Inga       |
| prioritet            | Avgör vilka jobb av alla som står i kö för att köras först. Ju lägre siffra, desto högre prioritet. | Inga       |
| parameters          | Parametrar för att gå in i U-SQL-skriptet.    | Inga       |
| runtimeVersion      | Körningsversion av den U-SQL-motor som ska användas. | Inga       |
| sammanställningMode     | <p>Kompileringsläge för U-SQL. Måste vara ett av dessa värden: **Semantisk:** Utför endast semantiska kontroller och nödvändiga sanitetskontroller, **Fullständig:** Utför den fullständiga kompileringen, inklusive syntaxkontroll, optimering, kodgenerering, etc., **SingleBox:** Utför den fullständiga kompileringen, med TargetType-inställningen till SingleBox. Om du inte anger något värde för den här egenskapen bestämmer servern det optimala kompileringsläget. | Inga |

Se [SearchLogProcessing.txt](#sample-u-sql-script) för skriptdefinitionen. 

## <a name="sample-u-sql-script"></a>Exempel på U-SQL-skript

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

I ovanstående skriptexempel definieras indata och utdata till skriptet i ** \@in-** och ** \@utparametrar.** Värdena för ** \@in-** och ** \@utparametrar** i U-SQL-skriptet skickas dynamiskt av Data Factory med hjälp av avsnittet parametrar. 

Du kan ange andra egenskaper som degreeOfParallelism och prioritet samt i pipelinedefinitionen för de jobb som körs på Azure Data Lake Analytics-tjänsten.

## <a name="dynamic-parameters"></a>Dynamiska parametrar
I exempelpipellinedefinitionen tilldelas in- och utparametrar med hårdkodade värden. 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

Det är möjligt att använda dynamiska parametrar i stället. Ett exempel: 

```json
"parameters": {
    "in": "/datalake/input/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/data.tsv",
    "out": "/datalake/output/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/result.tsv"
}
```

I det här fallet hämtas indatafiler fortfarande från mappen /datalake/input och utdatafiler genereras i mappen /datalake/output. Filnamnen är dynamiska baserat på fönstrets starttid som skickas in när pipelinen utlöses.  

## <a name="next-steps"></a>Nästa steg
Se följande artiklar som förklarar hur du omvandlar data på andra sätt: 

* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [Grisaktivitet](transform-data-using-hadoop-pig.md)
* [MapReduce aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming aktivitet](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Körning av maskininlärningsbatch](transform-data-using-machine-learning.md)
* [Lagrad proceduraktivitet](transform-data-using-stored-procedure.md)
