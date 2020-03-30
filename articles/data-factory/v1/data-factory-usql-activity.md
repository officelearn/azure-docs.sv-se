---
title: Omvandla data med U-SQL-skript - Azure
description: Lär dig hur du bearbetar eller transformerar data genom att köra U-SQL-skript på Azure Data Lake Analytics-beräkningstjänsten.
services: data-factory
documentationcenter: ''
ms.assetid: e17c1255-62c2-4e2e-bb60-d25274903e80
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/01/2017
author: nabhishek
ms.author: abnarain
manager: anandsub
robots: noindex
ms.openlocfilehash: c6d3510dfdd02bf2eb07d656c706c44d895c582d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74927903"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Transformera data genom att köra U-SQL-skript på Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-usql-activity.md)
> * [Version 2 (aktuell version)](../transform-data-using-data-lake-analytics.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [U-SQL-aktivitet i V2](../transform-data-using-data-lake-analytics.md).

En pipeline i en Azure-datafabrik bearbetar data i länkade lagringstjänster med hjälp av länkade beräkningstjänster. Den innehåller en sekvens av aktiviteter där varje aktivitet utför en viss bearbetning. I den här artikeln beskrivs **datasjöanalys U-SQL-aktivitet** som kör ett **U-SQL-skript** på en Azure Data Lake Analytics-beräkningslänkad tjänst. **Azure Data Lake Analytics** 

Skapa ett Azure Data Lake Analytics-konto innan du skapar en pipeline med en Data Lake Analytics U-SQL-aktivitet. Mer information om Azure Data Lake Analytics finns [i Komma igång med Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

Läs den [första pipeline-självstudien](data-factory-build-your-first-pipeline.md) för att skapa en datafabrik, länkade tjänster, datauppsättningar och en pipeline. Använd JSON-kodavsnitt med Data Factory Editor eller Visual Studio eller Azure PowerShell för att skapa datafabrikentiteter.

## <a name="supported-authentication-types"></a>Autentiseringstyper som stöds
U-SQL-aktivitet stöder under autentiseringstyper mot DataSjöanalys:
* Autentisering av tjänstens huvudnamn
* OAuth-autentisering (OAuth) autentisering (användarautentiseringsuppgifter) 

Vi rekommenderar att du använder autentisering av tjänstens huvudnamn, särskilt för en schemalagd U-SQL-körning. Tokenförfallobeteende kan uppstå med autentiseringsuppgifter för användare. Konfigurationsinformation finns i avsnittet [Egenskaper för länkade tjänster.](#azure-data-lake-analytics-linked-service)

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics länkad tjänst
Du skapar en **Azure Data Lake Analytics-länkad** tjänst för att länka en Azure Data Lake Analytics-beräkningstjänst till en Azure-datafabrik. Data Lake Analytics U-SQL-aktivitet på pipelinen refererar till den länkade tjänsten. 

I följande tabell finns beskrivningar av de allmänna egenskaper som används i JSON-definitionen. Du kan ytterligare välja mellan tjänstens huvudnamn och autentiseringsuppgifter för användare.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| **Typ** |Typegenskapen ska anges till: **AzureDataLakeAnalytics**. |Ja |
| **kontoNamn** |Azure Data Lake Analytics-kontonamn. |Ja |
| **dataLakeAnalyticsUri** |Azure Data Lake Analytics URI. |Inga |
| **subscriptionId** |Azure-prenumerations-ID |Nej (Om inget anges används prenumerationen på datafabriken). |
| **resursGroupName** |Azure-resursgruppsnamn |Nej (Om det inte anges används datafabrikens resursgrupp). |

### <a name="service-principal-authentication-recommended"></a>Autentisering av tjänstens huvudnamn (rekommenderas)
Om du vill använda tjänsthuvudbehörighetsautentisering registrerar du en programentitet i Azure Active Directory (Azure AD) och ger den åtkomst till DataSjö store. Detaljerade steg finns i [Autentisering](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md)från tjänst till tjänst . Anteckna följande värden, som du använder för att definiera den länkade tjänsten:
* Program-ID:t
* Programnyckel 
* Klient-ID:t

Använd autentisering av tjänstens huvudnamn genom att ange följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| **servicePrincipalId** | Ange programmets klient-ID. | Ja |
| **servicePrincipalKey** | Ange programmets nyckel. | Ja |
| **Hyresgästen** | Ange klientinformation (domännamn eller klient-ID) som programmet finns under. Du kan hämta den genom att hålla musen i det övre högra hörnet av Azure-portalen. | Ja |

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

### <a name="user-credential-authentication"></a>Autentisering av användarautentiseringsuppgifter
Du kan också använda autentiseringsuppgifter för DataSjöanalys genom att ange följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| **Tillstånd** | Klicka på knappen **Auktorisera** i Data Factory Editor och ange autentiseringsuppgifterna som tilldelar den automatiskt skapade auktoriseringsadressen till den här egenskapen. | Ja |
| **Sessionid** | OAuth-sessions-ID från OAuth-auktoriseringssessionen. Varje sessions-ID är unikt och kan endast användas en gång. Den här inställningen genereras automatiskt när du använder Data Factory Editor. | Ja |

**Exempel: Autentisering av användarautentiseringsuppgifter**
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

#### <a name="token-expiration"></a>Token förfallodatum
Auktoriseringskoden som du har genererat med knappen **Auktorisera** upphör att gälla efter någon gång. Se följande tabell för förfallotider för olika typer av användarkonton. Följande felmeddelande visas när **autentiseringstoken upphör att gälla:** Åtgärdsfel för autentiseringsuppgifter: invalid_grant - AADSTS70002: Felvalda autentiseringsuppgifter. AADSTS70008: Förutsatt åtkomstbidrag har upphört att gälla eller återkallas. Spår-ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 Korrelations-ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timepstam: 2015-12-15 21:09:31Z

| Användartyp | Upphör att gälla efter |
|:--- |:--- |
| Användarkonton som inte hanteras@hotmail.com @live.comav Azure Active Directory ( , , etc.) |12 timmar |
| Användarkonton som hanteras av Azure Active Directory (AAD) |14 dagar efter den sista segmentkörningen. <br/><br/>90 dagar, om ett segment baserat på OAuth-baserad länkad tjänst körs minst en gång var 14:e dag. |

Undvik/lösa det här felet genom att återauktorisera med knappen **Auktorisera** när **token upphör att gälla** och distribuera om den länkade tjänsten. Du kan också generera värden för **sessionId-** och **auktoriseringsegenskaper** programmässigt med hjälp av kod enligt följande:

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

Mer information om de datafabriksklasser som används i koden finns i [avsnitten](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) [AzureDataLakeStoreLinkedService Class,](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx) [AzureDataLakeStoreLinkedService Class.](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) Lägg till en referens till: Klassen Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll för klassen WindowsFormsWebAuthenticationDialog. 

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-aktivitet
Följande JSON-kodavsnitt definierar en pipeline med en Data Lake Analytics U-SQL-aktivitet. Aktivitetsdefinitionen har en referens till den Azure Data Lake Analytics-länkade tjänst som du skapade tidigare.   

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

I följande tabell beskrivs namn och beskrivningar av egenskaper som är specifika för den här aktiviteten. 

| Egenskap            | Beskrivning                              | Krävs                                 |
| :------------------ | :--------------------------------------- | :--------------------------------------- |
| typ                | Typegenskapen måste anges till **DataLakeAnalyticsU-SQL**. | Ja                                      |
| linkedServiceName   | Referens till Azure Data Lake Analytics registrerad som en länkad tjänst i Data Factory | Ja                                      |
| scriptPath          | Sökväg till mapp som innehåller U-SQL-skriptet. Filens namn är skiftlägeskänsligt. | Nej (om du använder skript)                   |
| scriptLinkedService | Länkad tjänst som länkar lagringen som innehåller skriptet till datafabriken | Nej (om du använder skript)                   |
| -skriptet              | Ange infogade skript i stället för att ange scriptPath och scriptLinkedService. Till exempel: `"script": "CREATE DATABASE test"`. | Nej (om du använder scriptPath och scriptLinkedService) |
| gradAvParallelism | Det maximala antalet noder som används samtidigt för att köra jobbet. | Inga                                       |
| prioritet            | Avgör vilka jobb av alla som står i kö för att köras först. Ju lägre siffra, desto högre prioritet. | Inga                                       |
| parameters          | Parametrar för U-SQL-skriptet          | Inga                                       |
| runtimeVersion      | Körningsversion av U-SQL-motorn som ska användas | Inga                                       |
| sammanställningMode     | <p>Kompileringsläge för U-SQL. Måste vara ett av följande värden:</p> <ul><li>**Semantisk:** Utför endast semantiska kontroller och nödvändiga sanity kontroller.</li><li>**Full:** Utför hela kompileringen, inklusive syntaxkontroll, optimering, kodgenerering, etc.</li><li>**SingleBox:** Utför hela kompileringen med TargetType-inställningen till SingleBox.</li></ul><p>Om du inte anger något värde för den här egenskapen bestämmer servern det optimala kompileringsläget. </p> | Inga                                       |

Se [SearchLogProcessing.txt Script Definition](#sample-u-sql-script) för skriptdefinitionen. 

## <a name="sample-input-and-output-datasets"></a>Exempel på indata- och utdatauppsättningar
### <a name="input-dataset"></a>Indatauppsättning
I det här exemplet finns indata i en Azure Data Lake Store (SearchLog.tsv-fil i datalake/indatamappen). 

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

### <a name="output-dataset"></a>Utdatauppsättning
I det här exemplet lagras utdata som produceras av U-SQL-skriptet i en Azure Data Lake Store (datalake/utdatamapp). 

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

### <a name="sample-data-lake-store-linked-service"></a>Exempel på länkad tjänst för datasjölager
Här är definitionen av exemplet Azure Data Lake Store länkad tjänst som används av indata-/utdatauppsättningar. 

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

Se [Flytta data till och från](data-factory-azure-datalake-connector.md) Azure Data Lake Store-artikel för beskrivningar av JSON-egenskaper. 

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

Värdena för ** \@in-** och ** \@utparametrar** i U-SQL-skriptet skickas dynamiskt av ADF med hjälp av avsnittet parametrar. Se avsnittet parametrar i pipelinedefinitionen.

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
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

I det här fallet hämtas indatafiler fortfarande från mappen /datalake/input och utdatafiler genereras i mappen /datalake/output. Filnamnen är dynamiska baserat på segmentstarttiden.  


