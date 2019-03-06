---
title: Flytta data från ODBC-datalager | Microsoft Docs
description: Läs mer om hur du flyttar data från ODBC-datalager med Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: ad70a598-c031-4339-a883-c6125403cb76
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/19/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 83de0046a56788e40b9224823af0411a18dea5eb
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57449945"
---
# <a name="move-data-from-odbc-data-stores-using-azure-data-factory"></a>Flytta data från ODBC-datalager med Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-odbc-connector.md)
> * [Version 2 (aktuell version)](../connector-odbc.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [ODBC-anslutningsprogram i V2](../connector-odbc.md).


Den här artikeln förklarar hur du använder Kopieringsaktiviteten i Azure Data Factory för att flytta data från ett lokalt ODBC-datalager. Den bygger på den [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikel som anger en allmän översikt över dataförflyttning med kopieringsaktiviteten.

Du kan kopiera data från ett ODBC-datalager till alla datalager för mottagare som stöds. En lista över datalager som stöds som mottagare av Kopieringsaktivitet finns i den [datalager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabell. Data factory stöder för närvarande endast flyttar data från ett ODBC-datalager till datalager, men inte för att flytta data från andra datalager till ett ODBC-datalager.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enabling-connectivity"></a>Aktivera anslutning
Data Factory-tjänsten stöder anslutning till den lokala ODBC-källor med hjälp av Data Management Gateway. Se [flytta data mellan lokala platser och molnet](data-factory-move-data-between-onprem-and-cloud.md) du lär dig om Data Management Gateway och stegvisa instruktioner om hur du konfigurerar gatewayen. Använda gateway för att ansluta till en ODBC-datalager, även om den finns i en Azure IaaS-VM.

Du kan installera gatewayen på samma lokala dator eller virtuell Azure-dator som ODBC-datalager. Vi rekommenderar dock att du installerar gatewayen på en separat dator/Azure IaaS VM för att undvika resurskonflikter och få bättre prestanda. När du installerar gatewayen på en separat dator kan ska datorn kunna få åtkomst till datorn med ODBC-datalager.

Förutom de Data Management Gateway måste du också installera ODBC-drivrutinen för datalagret på gateway-datorn.

> [!NOTE]
> Se [felsöka problem med gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) tips om hur du felsöker anslutning/gateway-relaterade problem.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en Kopieringsaktivitet som flyttar data från ett ODBC-datalager med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline är att använda den **Kopieringsguiden**. Se [självstudien: Skapa en pipeline med Copy Wizard](data-factory-copy-data-wizard-tutorial.md) en snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall**, **.NET API**, och  **REST-API**. Se [kopiera aktivitet självstudien](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Om du använder verktyg eller API: er kan utföra du följande steg för att skapa en pipeline som flyttar data från källans datalager till mottagarens datalager:

1. Skapa **länkade tjänster** länka inkommande och utgående data du lagrar till din datafabrik.
2. Skapa **datauppsättningar** som representerar inkommande och utgående data för kopieringen.
3. Skapa en **pipeline** med en Kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata.

När du använder guiden skapas JSON-definitioner för dessa Data Factory-entiteter (länkade tjänster, datauppsättningar och pipeline) automatiskt åt dig. När du använder Verktyg/API: er (med undantag för .NET-API) kan definiera du dessa Data Factory-entiteter med hjälp av JSON-format. Ett exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data från ett ODBC-datalager, se [JSON-exempel: Kopiera data från ODBC-datalager till Azure Blob](#json-example-copy-data-from-odbc-data-store-to-azure-blob) i den här artikeln.

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory-entiteter som är specifika för ODBC-datalager:

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
Följande tabell innehåller beskrivning för JSON-element som är specifika för ODBC-länkad tjänst.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Type-egenskapen måste anges till: **OnPremisesOdbc** |Ja |
| connectionString |Den icke-autentiseringsuppgifter delen av anslutningssträngen och en valfri krypterade autentiseringsuppgifter. Se exemplen i följande avsnitt. <br/><br/>Du kan ange anslutningssträngen med mönster som `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`, eller använda systemet-DSN (Data Source Name) som du har konfigurerat på gateway-datorn med `"DSN=<name of the DSN>;"` (du måste fortfarande ange credential-delen i den länkade tjänsten i enlighet med detta). |Ja |
| credential |Åtkomst till autentiseringsuppgifter delen av anslutningssträngen som angetts i drivrutinsspecifika egenskapsvärdet format. Exempel: `"Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;"`. |Nej |
| authenticationType |Typ av autentisering som används för att ansluta till ODBC-datalager. Möjliga värden: Anonym och grundläggande. |Ja |
| användarnamn |Ange användarnamnet om du använder grundläggande autentisering. |Nej |
| lösenord |Ange lösenord för det användarkonto som du angav för användarnamnet. |Nej |
| gatewayName |Namnet på den gateway som Data Factory-tjänsten ska använda för att ansluta till ODBC-datalager. |Ja |

### <a name="using-basic-authentication"></a>Använder grundläggande autentisering

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```
### <a name="using-basic-authentication-with-encrypted-credentials"></a>Med grundläggande autentisering och krypterade autentiseringsuppgifter
Du kan kryptera autentiseringsuppgifterna med den [New-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) (version 1.0 av Azure PowerShell) cmdlet eller [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/dn834940.aspx) (0,9 eller tidigare version av Azure PowerShell).

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-anonymous-authentication"></a>Använder anonym autentisering

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
            "credential": "UID={uid};PWD={pwd}",
            "gatewayName": "mygateway"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [skapar datauppsättningar](data-factory-create-datasets.md) artikeln. Avsnitt som struktur, tillgänglighet och princip av en datauppsättnings-JSON är liknande för alla datauppsättningstyper av (Azure SQL, Azure-blob, Azure-tabell osv.).

Den **typeProperties** avsnittet är olika för varje typ av datauppsättning och tillhandahåller information om platsen för data i datalagret. TypeProperties avsnittet för datauppsättningen av typen **RelationalTable** (som inkluderar ODBC datauppsättning) har följande egenskaper

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i ODBC-datalager. |Ja |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [skapa Pipelines](data-factory-create-pipelines.md) artikeln. Egenskaper, till exempel namn, beskrivning, indata och utdata tabeller och principer är tillgängliga för alla typer av aktiviteter.

Egenskaper som är tillgängliga i den **typeProperties** avsnittet aktivitetens å andra sidan varierar med varje aktivitetstyp av. För kopieringsaktiviteten variera de beroende på vilka typer av källor och mottagare.

I kopieringsaktiviteten när källan är av typen **RelationalSource** (som inkluderar ODBC), följande egenskaper är tillgängliga i avsnittet typeProperties:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd anpassad fråga för att läsa data. |SQL-sträng. Till exempel: Välj * från MyTable. |Ja |


## <a name="json-example-copy-data-from-odbc-data-store-to-azure-blob"></a>JSON-exempel: Kopiera data från ODBC-datalager till Azure Blob
Det här exemplet innehåller JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Den visar hur du kopierar data från en ODBC-källa till ett Azure Blob Storage. Dock datan kan kopieras till någon av de mottagare som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) använda Kopieringsaktivitet i Azure Data Factory.

Exemplet har följande data factory-entiteter:

1. En länkad tjänst av typen [OnPremisesOdbc](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Indata [datauppsättning](data-factory-create-datasets.md) av typen [RelationalTable](#dataset-properties).
4. Utdata [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med en Kopieringsaktivitet som använder [RelationalSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från ett frågeresultat i ett ODBC-datalager till en blob varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

Som ett första steg att konfigurera data management gateway. Anvisningarna finns i den [flytta data mellan lokala platser och molnet](data-factory-move-data-between-onprem-and-cloud.md) artikeln.

**ODBC-länkad tjänst** det här exemplet använder grundläggande autentisering. Se [ODBC-länkad tjänst](#linked-service-properties) för olika typer av autentisering som du kan använda.

```json
{
    "name": "OnPremOdbcLinkedService",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

**Länkad Azure Storage-tjänst**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

**ODBC-datauppsättningen för indata**

Exemplet förutsätter att du har skapat en tabell ”MyTable” i en ODBC-databas och innehåller en kolumn med namnet ”timestampcolumn” för time series-data.

Ange ”external”: ”true” informerar Data Factory-tjänsten att datauppsättningen är extern till datafabriken och inte kommer från en aktivitet i data factory.

```json
{
    "name": "ODBCDataSet",
    "properties": {
        "published": false,
        "type": "RelationalTable",
        "linkedServiceName": "OnPremOdbcLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**Utdatauppsättning för Azure-blob**

Data skrivs till en ny blob varje timme (frequency: timme, intervall: 1). Sökvägen till mappen för bloben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Sökvägen till mappen använder år, månad, dag och timmar delar av starttiden.

```json
{
    "name": "AzureBlobOdbcDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/odbc/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Kopiera aktivitet i en pipeline med ODBC-datakälla (RelationalSource) och Blob-mottagare (BlobSink)**

Pipelinen innehåller en Kopieringsaktivitet som är konfigurerad för att använda dessa in- och utdatauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen i **källa** är **RelationalSource** och **mottagare** är **BlobSink**. SQL-frågan som angetts för den **fråga** egenskapen väljer vilka data under den senaste timmen att kopiera.

```json
{
    "name": "CopyODBCToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "OdbcDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOdbcDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "OdbcToBlob"
            }
        ],
        "start": "2016-06-01T18:00:00Z",
        "end": "2016-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-odbc"></a>Mappning för ODBC
Som vi nämnde i den [dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikeln kopieringsaktiviteten utför automatisk konverteringar från typer av datakällor till mottagare typer med följande metod i två steg:

1. Konvertera från interna källtyper till .NET-typ
2. Konvertera från .NET-typ till interna mottagare

När du flyttar data från ODBC-datalager, ODBC-datatyper är mappade till .NET-typerna som anges i den [ODBC mappningar av datatyper](https://msdn.microsoft.com/library/cc668763.aspx) avsnittet.

## <a name="map-source-to-sink-columns"></a>Kartkälla till kolumner för mottagare
Mer information om mappning av kolumner i datauppsättningen för källan till kolumner i datauppsättning för mottagare, se [mappning av kolumner för datauppsättningar i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Upprepbar läsning från relationella källor
Kom ihåg att undvika oväntade resultat repeterbarhet när kopiera data från relationsdata lagras. I Azure Data Factory kan du köra en sektor manuellt. Du kan också konfigurera återförsöksprincipen för en datauppsättning så att en sektor som körs när ett fel uppstår. När ett segment ska köras på nytt på något sätt, måste du se till att samma data läses oavsett hur många gånger som en sektor körs. Se [Repeatable läsa från relationella källor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="troubleshoot-connectivity-issues"></a>Felsöka anslutningsproblem
Felsök problem med anslutningen genom att använda den **diagnostik** fliken **Data Management Gateway Configuration Manager**.

1. Starta **Data Management Gateway Configuration Manager**. Du kan antingen köra ”C:\Program Files\Microsoft Data Management Gateway\1.0\Shared\ConfigManager.exe” direkt (eller) sökning för **Gateway** att hitta en länk till **Microsoft Data Management Gateway** program som du ser i följande bild.

    ![Sök gateway](./media/data-factory-odbc-connector/search-gateway.png)
2. Växla till den **diagnostik** fliken.

    ![Gateway-diagnostik](./media/data-factory-odbc-connector/data-factory-gateway-diagnostics.png)
3. Välj den **typ** lagra data (länkad tjänst).
4. Ange **autentisering** och ange **autentiseringsuppgifter** (eller) ange **anslutningssträngen** som används för att ansluta till datalagret.
5. Klicka på **Testanslutning** att testa anslutningen till datalagret.

## <a name="performance-and-tuning"></a>Prestanda- och justering
Se [kopiera aktivitet prestanda- och Justeringsguide](data-factory-copy-activity-performance.md) att lära dig om viktiga faktorer att påverka prestandan för dataförflyttning (Kopieringsaktiviteten) i Azure Data Factory och olika sätt att optimera den.
