---
title: "Transformera data med hjälp av U-SQL - skript i Azure | Microsoft Docs"
description: "Lär dig hur du bearbetar eller Transformera data genom att köra U-SQL-skript på Azure Data Lake Analytics-tjänsten för beräkning."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: e17c1255-62c2-4e2e-bb60-d25274903e80
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: ff91a3da978fd027605b3674eae14d1d74b309cd
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Transformera data genom att köra U-SQL-skript på Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](data-factory-usql-activity.md)
> * [Version 2 – förhandsversion](../transform-data-using-data-lake-analytics.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [U-SQL-aktivitet i V2](../transform-data-using-data-lake-analytics.md).

En pipeline i ett Azure data factory bearbetar data i länkade storage-tjänster med hjälp av länkade beräknings-tjänster. Den innehåller en serie aktiviteter där varje aktivitet utför en specifik bearbetning. Den här artikeln beskriver den **Data Lake Analytics U-SQL-aktivitet** som kör en **U-SQL** skript på en **Azure Data Lake Analytics** compute länkade tjänsten. 

Skapa ett Azure Data Lake Analytics-konto innan du skapar en pipeline med en Data Lake Analytics U-SQL-aktivitet. Mer information om Azure Data Lake Analytics, se [Kom igång med Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

Granska de [skapa din första pipeline-självstudierna](data-factory-build-your-first-pipeline.md) detaljerade steg för att skapa en datafabrik länkade tjänster, datauppsättningar och en pipeline. Använda JSON kodavsnitt med Data Factory-redigeraren eller Visual Studio eller Azure PowerShell för att skapa Data Factory entiteter.

## <a name="supported-authentication-types"></a>Typer av autentiseringsmetoder som stöds
U-SQL-aktiviteten stöder nedan autentiseringstyper mot Data Lake Analytics:
* Autentisering av tjänstens huvudnamn
* Användarautentisering autentiseringsuppgifter (OAuth) 

Vi rekommenderar att du använder service principal autentisering, särskilt för en schemalagd U-SQL-körningen. Giltighetstid för token kan inträffa med autentiseringsuppgifter för användarautentisering. Konfigurationsinformation finns i [länkade tjänstegenskaper](#azure-data-lake-analytics-linked-service) avsnitt.

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics länkade tjänsten
Du skapar en **Azure Data Lake Analytics** länkad tjänst för att länka ett Azure Data Lake Analytics compute-tjänst till ett Azure data factory. Data Lake Analytics U-SQL-aktivitet i pipelinen refererar till den här länkade tjänsten. 

Följande tabell innehåller beskrivningar för allmänna egenskaper som används i JSON-definitionen. Du kan ytterligare välja mellan tjänstens huvudnamn och autentiseringsuppgifter för användarautentisering.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| **typ** |Typegenskapen bör anges till: **AzureDataLakeAnalytics**. |Ja |
| **Kontonamn** |Azure Data Lake Analytics-kontonamn. |Ja |
| **dataLakeAnalyticsUri** |Azure Data Lake Analytics-URI. |Nej |
| **prenumerations-ID** |Azure prenumerations-id |Nej (om den inte anges data factory-prenumeration används). |
| **resourceGroupName** |Azure resursgruppens namn |Nej (om inget annat anges, resursgruppen av datafabriken används). |

### <a name="service-principal-authentication-recommended"></a>Tjänstens huvudnamn autentisering (rekommenderas)
Om du vill använda huvudnamn autentiseringen av tjänsten registrera en entitet för program i Azure Active Directory (AD Azure) och bevilja åtkomst till Data Lake Store. Detaljerade anvisningar finns i [tjänst-till-tjänst autentisering](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Anteckna följande värden som du använder för att definiera den länkade tjänsten:
* Program-ID:t
* Nyckeln för programmet 
* Klient-ID:t

Använd service principal autentisering genom att ange följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| **servicePrincipalId** | Ange programmets klient-ID. | Ja |
| **servicePrincipalKey** | Ange programmets nyckeln. | Ja |
| **klient** | Ange information om klient (domain name eller klient ID) under där programmet finns. Du kan hämta den hovrar muspekaren i det övre högra hörnet i Azure-portalen. | Ja |

**Exempel: Service principal autentisering**
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
Alternativt kan du använda användarautentisering för autentiseringsuppgifter för Data Lake Analytics genom att ange följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| **auktorisering** | Klicka på den **auktorisera** i den Data Factory-redigeraren och ange dina autentiseringsuppgifter som tilldelar automatiskt genererade auktorisering URL till den här egenskapen. | Ja |
| **sessions-ID** | OAuth sessions-ID från OAuth-auktorisering sessionen. Varje sessions-ID är unikt och kan bara användas en gång. Den här inställningen genereras automatiskt när du använder Data Factory-redigeraren. | Ja |

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

#### <a name="token-expiration"></a>Token upphör att gälla
Auktoriseringskoden du genererade med hjälp av den **auktorisera** knappen upphör att gälla efter en stund. Förfallodatum tidpunkter för olika typer av användarkonton finns i följande tabell. Du kan se följande fel visas vid autentiseringen **token upphör att gälla**: autentiseringsuppgifter fel: invalid_grant - AADSTS70002: fel vid verifiering av autentiseringsuppgifter. AADSTS70008: Bevilja beviljade åtkomsten har upphört att gälla eller återkallats. Trace-ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 Korrelations-ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 tidsstämpel: 2015-12-15 21:09:31Z

| Användartyp | Upphör att gälla efter |
|:--- |:--- |
| Användarkonton som inte hanteras av Azure Active Directory (@hotmail.com, @live.comosv.) |12 timmar |
| Användarkonton som hanteras av Azure Active Directory (AAD) |14 dagar efter det sista segmentet kör. <br/><br/>90 dagar, om ett segment baserat på OAuth-baserad länkade tjänst körs på minst en gång var fjortonde dag. |

Om du vill undvika/åtgärda felet, omauktorisera med hjälp av den **auktorisera** knappen när den **token upphör att gälla** och distribuera den länkade tjänsten. Du kan också generera värdena för **sessionId** och **auktorisering** egenskaper programmässigt med hjälp av koden på följande sätt:

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

Se [AzureDataLakeStoreLinkedService klassen](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService klassen](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx), och [AuthorizationSessionGetResponse klassen](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) avsnitt hittar du information om Data Factory-klasser som används i koden. Lägg till en referens till: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll för WindowsFormsWebAuthenticationDialog-klassen. 

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-aktivitet
Följande JSON-fragment definierar en pipeline med en Data Lake Analytics U-SQL-aktivitet. Aktivitetsdefinitionen har en referens till länkade Azure Data Lake Analytics-tjänsten som du skapade tidigare.   

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
| typ                | Egenskapen type måste anges till **DataLakeAnalyticsU SQL**. | Ja                                      |
| linkedServiceName   | Referens till Azure Data Lake Analytics är registrerat som en länkad tjänst i Data Factory | Ja                                      |
| ScriptPath          | Sökvägen till mappen som innehåller U-SQL-skript. Namnet på filen är skiftlägeskänslig. | Nej (om du använder skriptet)                   |
| scriptLinkedService | Länkade tjänst som länkar den lagring som innehåller skriptet till data factory | Nej (om du använder skriptet)                   |
| Skriptet              | Ange infogat skript i stället för att ange scriptPath och scriptLinkedService. Till exempel: `"script": "CREATE DATABASE test"`. | Nej (om du använder scriptPath och scriptLinkedService) |
| degreeOfParallelism | Maximalt antal noder samtidigt används för att köra jobbet. | Nej                                       |
| Prioritet            | Anger vilka jobb av alla köas ska väljas att köras först. Ju lägre nummer, desto högre prioritet. | Nej                                       |
| parameters          | Parametrar för U-SQL-skript          | Nej                                       |
| runtimeVersion      | Körtidsversion U-SQL-motorn att använda | Nej                                       |
| compilationMode     | <p>Kompileringsläge för U-SQL. Måste vara ett av följande värden:</p> <ul><li>**Semantiska:** endast utföra semantiska kontroller och nödvändiga hälsokontroller.</li><li>**Fullständig:** utföra fullständig kompilering, inklusive syntaxkontrollen, optimering, kodgenerering osv.</li><li>**SingleBox:** utföra fullständig sammanställning med TargetType inställningen till SingleBox.</li></ul><p>Om du inte anger ett värde för den här egenskapen anger servern optimala kompilering. </p> | Nej                                       |

Se [SearchLogProcessing.txt skript Definition](#sample-u-sql-script) skript-definition. 

## <a name="sample-input-and-output-datasets"></a>Exempel på indata och utdata datauppsättningar
### <a name="input-dataset"></a>Indatauppsättning
I det här exemplet finns indata i ett Azure Data Lake Store (SearchLog.tsv-fil i mappen datalake-indata). 

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

### <a name="output-dataset"></a>Datamängd för utdata
I det här exemplet lagras utdata som genereras av U-SQL-skript i ett Azure Data Lake Store (datalake-/ utdata mapp). 

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

### <a name="sample-data-lake-store-linked-service"></a>Exempel Data Lake Store länkade tjänsten
Det här är definitionen av exemplet Azure Data Lake Store länkade tjänst som används av in-/ utdata-datauppsättningar. 

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

Se [flytta data till och från Azure Data Lake Store](data-factory-azure-datalake-connector.md) artikel beskrivningar av JSON-egenskaper. 

## <a name="sample-u-sql-script"></a>Exempel U-SQL-skript

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

Värdena för  **@in**  och  **@out**  parametrar i U-SQL-skriptet skickas dynamiskt av ADF med hjälp av avsnittet-Parametrar'. Se avsnittet-Parametrar' i pipeline-definitionen.

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


