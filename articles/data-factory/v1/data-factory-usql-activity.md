---
title: Transformera data med U-SQL-skript – Azure
description: Lär dig hur du bearbetar eller transformerar data genom att köra U-SQL-skript på Azure Data Lake Analytics Compute service.
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
ms.openlocfilehash: 7608719c4e0c2b9e23f1982efda9789d25f50224
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73665948"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Transformera data genom att köra U-SQL-skript på Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-usql-activity.md)
> * [Version 2 (aktuell version)](../transform-data-using-data-lake-analytics.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [U-SQL-aktivitet i v2](../transform-data-using-data-lake-analytics.md).

En pipeline i en Azure Data Factory bearbetar data i länkade lagrings tjänster med hjälp av länkade Compute-tjänster. Den innehåller en sekvens med aktiviteter där varje aktivitet utför en speciell bearbetnings åtgärd. I den här artikeln beskrivs **data Lake Analytics u-SQL-aktivitet** som kör ett **U-SQL** -skript i en **Azure Data Lake Analytics** Compute-länkad tjänst. 

Skapa ett Azure Data Lake Analytics konto innan du skapar en pipeline med en Data Lake Analytics U-SQL-aktivitet. Mer information om Azure Data Lake Analytics finns i [Kom igång med Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

Gå igenom [självstudien skapa din första pipeline](data-factory-build-your-first-pipeline.md) för detaljerade steg för att skapa en data fabrik, länkade tjänster, data uppsättningar och en pipeline. Använd JSON-kodfragment med Data Factory redigerare eller Visual Studio eller Azure PowerShell för att skapa Data Factory entiteter.

## <a name="supported-authentication-types"></a>Autentiseringstyper som stöds
U-SQL-aktiviteten stöder under autentiseringstyper med Data Lake Analytics:
* Autentisering av tjänstens huvudnamn
* OAuth-autentisering (User Credential) 

Vi rekommenderar att du använder tjänstens huvud namns autentisering, särskilt för schemalagd U-SQL-körning. Förfallo beteende för token kan uppstå med autentisering av användarautentisering. Konfigurations information finns i avsnittet [Egenskaper för länkad tjänst](#azure-data-lake-analytics-linked-service) .

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics länkad tjänst
Du skapar en **Azure Data Lake Analytics** länkad tjänst för att länka en Azure Data Lake Analytics Compute-tjänst till en Azure Data Factory. Data Lake Analytics U-SQL-aktiviteten i pipelinen refererar till den här länkade tjänsten. 

Följande tabell innehåller beskrivningar av de allmänna egenskaper som används i JSON-definitionen. Du kan välja mellan tjänstens huvud namn och autentisering med användar behörighet.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| **typ** |Egenskapen Type ska anges till: **AzureDataLakeAnalytics**. |Ja |
| **Konto** |Azure Data Lake Analytics konto namn. |Ja |
| **dataLakeAnalyticsUri** |Azure Data Lake Analytics-URI. |Nej |
| **subscriptionId** |ID för Azure-prenumeration |Nej (om inget anges används prenumerationen på data fabriken). |
| **resourceGroupName** |Azure-resursgruppsnamn |Nej (om inget värde anges används resurs gruppen för data fabriken). |

### <a name="service-principal-authentication-recommended"></a>Autentisering av tjänstens huvud namn (rekommenderas)
Om du vill använda tjänstens huvud namns autentisering registrerar du en program enhet i Azure Active Directory (Azure AD) och ger den åtkomst till Data Lake Store. Detaljerade anvisningar finns i [tjänst-till-tjänst-autentisering](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Anteckna följande värden som du använder för att definiera den länkade tjänsten:
* Program-ID:t
* Program nyckel 
* Klient-ID:t

Använd tjänstens huvud namns autentisering genom att ange följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| **servicePrincipalId** | Ange programmets klient-ID. | Ja |
| **servicePrincipalKey** | Ange programmets nyckel. | Ja |
| **innehav** | Ange den klient information (domän namn eller klient-ID) som programmet finns under. Du kan hämta det genom att hovra musen i det övre högra hörnet av Azure Portal. | Ja |

**Exempel: autentisering av tjänstens huvud namn**
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

### <a name="user-credential-authentication"></a>Autentisering av autentiseringsuppgifter för användare
Du kan också använda autentisering med användarens autentiseringsuppgifter för Data Lake Analytics genom att ange följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| **auktoriseringsregeln** | Klicka på knappen **auktorisera** i Data Factory redigeraren och ange autentiseringsuppgifterna som tilldelar URL: en automatiskt genererad auktorisering till den här egenskapen. | Ja |
| **sessionId** | OAuth-sessions-ID från OAuth-auktoriseringsbegäran. Varje sessions-ID är unikt och kan bara användas en gång. Den här inställningen genereras automatiskt när du använder Data Factory redigeraren. | Ja |

**Exempel: autentisering med användar behörighet**
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

#### <a name="token-expiration"></a>Förfallo datum för token
Den auktoriseringskod som du genererade med knappen **auktorisera** upphör att gälla efter en stund. Se följande tabell för förfallo tiderna för olika typer av användar konton. Du kan se följande fel meddelande när autentiseringstoken **upphör att gälla**: autentiseringsuppgifter för autentiseringsuppgift: INVALID_GRANT-AADSTS70002: det gick inte att verifiera autentiseringsuppgifterna. AADSTS70008: den angivna åtkomst tilldelningen har förfallit eller återkallats. Spårnings-ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 korrelations-ID: fac30a0c-6BE6-4e02-8d69-a776d2ffefd7 tidsstämpel: 2015-12-15 21:09:31Z

| Användar typ | Upphör att gälla efter |
|:--- |:--- |
| Användar konton som inte hanteras av Azure Active Directory (@hotmail.com, @live.comosv.) |12 timmar |
| Användar konton som hanteras av Azure Active Directory (AAD) |14 dagar efter den sista sektor körningen. <br/><br/>90 dagar, om en sektor som baseras på OAuth-baserad länkad tjänst körs minst en gång var 14: e dag. |

Du kan undvika/lösa det här felet genom att auktorisera med knappen **auktorisera** när **token upphör att gälla** och distribuera om den länkade tjänsten. Du kan också generera värden för **SessionID** och egenskaper för **auktorisering** program mässigt med hjälp av kod på följande sätt:

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

Mer information om de Data Factory klasser som används i koden finns i avsnittet [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)-klass, [AzureDataLakeAnalyticsLinkedService-klass](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)och AuthorizationSessionGetResponse- [klass](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) . Lägg till en referens till: Microsoft. IdentityModel. clients. ActiveDirectory. WindowsForms. dll för klassen WindowsFormsWebAuthenticationDialog. 

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-aktivitet
Följande JSON-kodfragment definierar en pipeline med en Data Lake Analytics U-SQL-aktivitet. Aktivitets definitionen har en referens till den Azure Data Lake Analytics länkade tjänsten som du skapade tidigare.   

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

I följande tabell beskrivs namn och beskrivningar av egenskaper som är unika för den här aktiviteten. 

| Egenskap            | Beskrivning                              | Krävs                                 |
| :------------------ | :--------------------------------------- | :--------------------------------------- |
| typ                | Egenskapen Type måste anges till **DataLakeAnalyticsU-SQL**. | Ja                                      |
| linkedServiceName   | Referens till Azure Data Lake Analytics som registrerats som en länkad tjänst i Data Factory | Ja                                      |
| scriptPath          | Sökväg till mapp som innehåller U-SQL-skriptet. Filens namn är Skift läges känsligt. | Nej (om du använder skript)                   |
| scriptLinkedService | Länkad tjänst som länkar det lagrings utrymme som innehåller skriptet till data fabriken | Nej (om du använder skript)                   |
| -skriptet              | Ange infogat skript i stället för att ange scriptPath och scriptLinkedService. Till exempel: `"script": "CREATE DATABASE test"`. | Nej (om du använder scriptPath och scriptLinkedService) |
| degreeOfParallelism | Det maximala antalet noder som används samtidigt för att köra jobbet. | Nej                                       |
| prioritet            | Bestämmer vilka jobb som inte är i kö som ska väljas för att köras först. Ju lägre siffra, desto högre prioritet. | Nej                                       |
| parameters          | Parametrar för U-SQL-skriptet          | Nej                                       |
| runtimeVersion      | Körnings version av U-SQL-motorn som ska användas | Nej                                       |
| compilationMode     | <p>Compiler-läge för U-SQL. Måste vara något av följande värden:</p> <ul><li>**Semantisk:** Utför endast semantiska kontroller och nödvändiga Sanity-kontroller.</li><li>**Fullständig:** Genomför fullständig kompilering, inklusive syntaxkontroll, optimering, kodgenerering, osv.</li><li>**Enkel:** Utför fullständig kompilering med TargetType-inställningen till en enda.</li></ul><p>Om du inte anger något värde för den här egenskapen, fastställer servern det optimala kompilerings läget. </p> | Nej                                       |

Se skript definitionen [SearchLogProcessing. txt](#sample-u-sql-script) för skript definitionen. 

## <a name="sample-input-and-output-datasets"></a>Exempel på indata och utdata för data uppsättningar
### <a name="input-dataset"></a>Indata-datauppsättning
I det här exemplet finns indata i en Azure Data Lake Store (SearchLog. TSV-fil i mappen datalake/indata). 

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

### <a name="output-dataset"></a>Data uppsättning för utdata
I det här exemplet lagras de utdata som genereras av U-SQL-skriptet i en Azure Data Lake Store (datalake/output-mapp). 

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

### <a name="sample-data-lake-store-linked-service"></a>Exempel på länkad tjänst för Data Lake Store
Här är definitionen av det exempel Azure Data Lake Store länkade tjänsten som används av data uppsättningarna för indata/utdata. 

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

Se [Flytta data till och från Azure Data Lake Store](data-factory-azure-datalake-connector.md) artikel för beskrivningar av JSON-egenskaper. 

## <a name="sample-u-sql-script"></a>Exempel på U-SQL-skript

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

Värdena för **\@i** och **\@ut** -PARAMETRARNA i U-SQL-skriptet skickas dynamiskt med ADF med hjälp av avsnittet Parameters. Se avsnittet "parameters" i pipeline-definitionen.

Du kan ange andra egenskaper som degreeOfParallelism och prioritet även i din pipeline-definition för de jobb som körs på Azure Data Lake Analyticss tjänsten.

## <a name="dynamic-parameters"></a>Dynamiska parametrar
I exempel definitionen för pipeline kan in-och out-parametrarna tilldelas med hårdkodade värden. 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

Du kan använda dynamiska parametrar i stället. Till exempel: 

```json
"parameters": {
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

I det här fallet hämtas indatafilerna fortfarande från mappen/datalake/input och utdatafilerna skapas i mappen/datalake/output. Fil namnen är dynamiska utifrån sektorens start tid.  


