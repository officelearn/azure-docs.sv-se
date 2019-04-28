---
title: Transformera data med hjälp av U-SQL-skript – Azure | Microsoft Docs
description: Lär dig hur du bearbeta eller omvandla data genom att köra U-SQL-skript på Azure Data Lake Analytics-databearbetningstjänst.
services: data-factory
documentationcenter: ''
ms.assetid: e17c1255-62c2-4e2e-bb60-d25274903e80
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/01/2017
author: nabhishek
ms.author: abnarain
manager: craigg
robots: noindex
ms.openlocfilehash: 5835c37363c7e9d2dd3253c08ab97f17852725f5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61248155"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Transformera data genom att köra U-SQL-skript på Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-usql-activity.md)
> * [Version 2 (aktuell version)](../transform-data-using-data-lake-analytics.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [U-SQL-aktivitet i V2](../transform-data-using-data-lake-analytics.md).

En pipeline i Azure data factory bearbetar data i länkade storage-tjänster med hjälp av länkade Beräkningstjänster. Den innehåller en serie aktiviteter där varje aktivitet utför en viss bearbetning av åtgärd. Den här artikeln beskriver den **Data Lake Analytics U-SQL-aktivitet** som kör en **U-SQL** skript på en **Azure Data Lake Analytics** compute länkad tjänst. 

Skapa ett Azure Data Lake Analytics-konto innan du skapar en pipeline med en Data Lake Analytics U-SQL-aktivitet. Läs om Azure Data Lake Analytics i [Kom igång med Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

Granska den [skapa din första pipeline-självstudie](data-factory-build-your-first-pipeline.md) för detaljerade steg för att skapa en datafabrik, länkade tjänster, datauppsättningar och en pipeline. Använd JSON-kodfragment med Data Factory-redigeraren eller Visual Studio eller Azure PowerShell för att skapa Data Factory-entiteter.

## <a name="supported-authentication-types"></a>Typer av autentisering stöds
U-SQL-aktivitet stöder nedan autentiseringstyper mot Data Lake Analytics:
* Autentisering av tjänstens huvudnamn
* Användarautentisering för autentiseringsuppgifter (OAuth) 

Vi rekommenderar att du använder autentisering av tjänstens huvudnamn, särskilt för körning av en schemalagd U-SQL. Giltighetstid för token kan inträffa med användarautentisering för autentiseringsuppgifter. Konfigurationsinformation finns i den [länkade tjänstegenskaper](#azure-data-lake-analytics-linked-service) avsnittet.

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics-länkad tjänst
Du skapar en **Azure Data Lake Analytics** compute service till en Azure data factory-länkade tjänst som länkar ett Azure Data Lake Analytics. Data Lake Analytics U-SQL-aktivitet i pipelinen refererar till den här länkade tjänsten. 

Följande tabell innehåller beskrivningar av de allmänna egenskaper som används i JSON-definitionen. Du kan ytterligare välja mellan tjänstens huvudnamn och användarautentisering för autentiseringsuppgifter.

| Egenskap  | Beskrivning | Obligatoriskt |
| --- | --- | --- |
| **typ** |Type-egenskapen ska anges till: **AzureDataLakeAnalytics**. |Ja |
| **accountName** |Azure Data Lake Analytics-kontonamn. |Ja |
| **dataLakeAnalyticsUri** |Azure Data Lake Analytics-URI. |Nej |
| **Prenumerations-ID** |Azure-prenumerations-id |Nej (om inte anges i data factory-prenumeration används). |
| **resourceGroupName** |Azure-resursgruppsnamn |Nej (om inte anges resursgruppen för data factory används). |

### <a name="service-principal-authentication-recommended"></a>Autentisering av tjänstens huvudnamn (rekommenderas)
Registrera en entitet för program i Azure Active Directory (Azure AD) för att använda autentisering av tjänstens huvudnamn, och ge det åtkomst till Data Lake Store. Detaljerade anvisningar finns i [tjänst-till-tjänst-autentisering](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Anteckna följande värden som du använder för att definiera den länkade tjänsten:
* Program-ID:t
* Programnyckel 
* Klient-ID:t

Använd autentisering av tjänstens huvudnamn genom att ange följande egenskaper:

| Egenskap  | Beskrivning | Obligatoriskt |
|:--- |:--- |:--- |
| **servicePrincipalId** | Ange programmets klient-ID. | Ja |
| **servicePrincipalKey** | Ange programmets nyckel. | Ja |
| **klient** | Ange klientinformation (domain name eller klient-ID) under där programmet finns. Du kan hämta den håller musen i det övre högra hörnet i Azure Portal. | Ja |

**Exempel: Autentisering av tjänstens huvudnamn**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>Användarautentisering för autentiseringsuppgifter
Du kan också använda användarautentisering för autentiseringsuppgifter för Data Lake Analytics genom att ange följande egenskaper:

| Egenskap  | Beskrivning | Obligatoriskt |
|:--- |:--- |:--- |
| **Auktorisering** | Klicka på den **auktorisera** i Data Factory-redigeraren och ange dina autentiseringsuppgifter som tilldelar automatiskt genererade auktorisering URL: en till den här egenskapen. | Ja |
| **sessionId** | OAuth sessions-ID från OAuth-auktorisering sessionen. Varje sessions-ID är unik och kan bara användas en gång. Den här inställningen genereras automatiskt när du använder Data Factory-redigeraren. | Ja |

**Exempel: Användarautentisering för autentiseringsuppgifter**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>Giltighetstid för token
Auktoriseringskod som du genererade med hjälp av den **auktorisera** knappen upphör att gälla efter en stund. Se tabellen nedan för förfallodatum tiderna för olika typer av användarkonton. Du kan se följande fel visas vid autentiseringen **token upphör att gälla**: Åtgärdsfel för autentiseringsuppgift: invalid_grant - AADSTS70002: Fel vid verifiering av autentiseringsuppgifter. AADSTS70008: Det tillhandahållna åtkomst beviljandet har upphört att gälla eller återkallats. Trace-ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 Korrelations-ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 tidsstämpel: 2015-12-15 21:09:31Z

| Användartyp | Upphör att gälla efter |
|:--- |:--- |
| Användarkonton som inte hanteras av Azure Active Directory (@hotmail.com, @live.comosv.) |12 timmar |
| Användarkonton som hanteras av Azure Active Directory (AAD) |Kör 14 dagar efter den sista sektorn. <br/><br/>90 dagar, om ett segment baserat på OAuth-baserad länkad tjänst körs minst en gång var fjortonde dag. |

Om du vill undvika/Lös det här felet, omauktorisera med hjälp av den **auktorisera** knappen när den **token upphör att gälla** och distribuera om den länkade tjänsten. Du kan också skapa värden för **sessionId** och **auktorisering** egenskaper via programmering med hjälp av kod på följande sätt:

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

Se [AzureDataLakeStoreLinkedService klass](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService klass](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx), och [AuthorizationSessionGetResponse klass](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) avsnitt hittar du information Om Data Factory-klasser som används i koden. Lägg till en referens till: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll för klassen WindowsFormsWebAuthenticationDialog. 

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-aktivitet
Följande JSON-kodfragmentet definierar en pipeline med en Data Lake Analytics U-SQL-aktivitet. Aktivitetsdefinitionen innehåller en referens till den länkade Azure Data Lake Analytics-tjänsten som du skapade tidigare.   

```json
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
        "activities": 
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs": 
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00Z",
        "end": "2015-08-08T01:00:00Z",
        "isPaused": false
    }
}
```

I följande tabell beskrivs namn och beskrivningar för egenskaper som är specifika för den här aktiviteten. 

| Egenskap             | Beskrivning                              | Krävs                                 |
| :------------------ | :--------------------------------------- | :--------------------------------------- |
| typ                | Type-egenskapen måste anges till **DataLakeAnalyticsU SQL**. | Ja                                      |
| linkedServiceName   | Referens till Azure Data Lake Analytics registrerad som en länkad tjänst i Data Factory | Ja                                      |
| scriptPath          | Sökvägen till mappen som innehåller U-SQL-skriptet. Filens namn är skiftlägeskänsligt. | Nej (om du använder skriptet)                   |
| scriptLinkedService | Länkade tjänst som länkar den lagring som innehåller skriptet som data factory | Nej (om du använder skriptet)                   |
| skript              | Ange infogade skript istället för att ange scriptPath och scriptLinkedService. Till exempel: `"script": "CREATE DATABASE test"`. | Nej (om du använder scriptPath och scriptLinkedService) |
| degreeOfParallelism | Det maximala antalet noder samtidigt används för att köra jobbet. | Nej                                       |
| prioritet            | Anger vilka jobb av alla som är köade ska väljas att köras först. Ju lägre nummer, desto högre prioritet. | Nej                                       |
| parameters          | Parametrar för U-SQL-skript          | Nej                                       |
| runtimeVersion      | Runtime-versionen av U-SQL-motor som ska användas | Nej                                       |
| compilationMode     | <p>Kompileringsläge för U-SQL. Måste vara ett av följande värden:</p> <ul><li>**Semantisk:** Bara utföra semantiska kontroller och nödvändiga hälsokontroller.</li><li>**Fullständig:** Utföra fullständig kompileringen, inklusive syntaxkontrollen, optimering, kodgenerering osv.</li><li>**SingleBox:** Utföra fullständig kompileringen med TargetType inställningen om du vill SingleBox.</li></ul><p>Om du inte anger ett värde för den här egenskapen anger servern optimala kompileringsläge. </p> | Nej                                       |

Se [SearchLogProcessing.txt skriptet Definition](#sample-u-sql-script) för skript-definition. 

## <a name="sample-input-and-output-datasets"></a>Exempel på indata och utdata datauppsättningar
### <a name="input-dataset"></a>Datauppsättningen för indata
I det här exemplet finns indatan i en Azure Data Lake Store (SearchLog.tsv-fil i mappen datalake/input). 

```json
{
    "name": "DataLakeTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}    
```

### <a name="output-dataset"></a>Datauppsättningen för utdata
I det här exemplet lagras de utdata som genereras av U-SQL-skriptet i en Azure Data Lake Store (datalake-/ utdata-mapp). 

```json
{
    "name": "EventsByRegionTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="sample-data-lake-store-linked-service"></a>Exemplet Data Lake Store-länkade tjänst
Här är definitionen av exemplet Azure Data Lake Store-länkade tjänst som används av datauppsättningarna som indata/utdata. 

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
        }
    }
}
```

Se [flytta data till och från Azure Data Lake Store](data-factory-azure-datalake-connector.md) artikeln beskrivningar av JSON-egenskaper. 

## <a name="sample-u-sql-script"></a>Exempelskript för U-SQL

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
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

Värdena för  **\@i** och  **\@ut** parametrar i U-SQL-skriptet som skickas dynamiskt med ADF med hjälp av avsnittet ”parameters”. Se avsnittet ”parameters” i pipeline-definition.

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
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

I det här fallet indatafilerna plockas fortfarande upp från mappen /datalake/input och utdatafiler som skapas i mappen /datalake/output. Filnamnen är dynamiskt baserat på starttid för sektor.  


