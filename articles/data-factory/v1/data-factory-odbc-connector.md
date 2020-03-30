---
title: Flytta data från ODBC-datalager
description: Läs mer om hur du flyttar data från ODBC-datalager med Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: ad70a598-c031-4339-a883-c6125403cb76
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/19/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e1735c2d2ed107f7ec65d68a6826267ee83a93f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281397"
---
# <a name="move-data-from-odbc-data-stores-using-azure-data-factory"></a>Flytta data från ODBC-datalager med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-odbc-connector.md)
> * [Version 2 (aktuell version)](../connector-odbc.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [ODBC-anslutning i V2](../connector-odbc.md).


I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att flytta data från ett lokalt ODBC-datalager. Den bygger på [datarörelseaktiviteter](data-factory-data-movement-activities.md) artikeln, som presenterar en allmän översikt över data förflyttning med kopian aktivitet.

Du kan kopiera data från ett ODBC-datalager till alla sink-datalager som stöds. En lista över datalager som stöds som mottagare av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Data factory stöder för närvarande endast flytta data från ett ODBC-datalager till andra datalager, men inte för att flytta data från andra datalager till ett ODBC-datalager.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enabling-connectivity"></a>Aktivera anslutning
Data Factory-tjänsten stöder anslutning till lokala ODBC-källor med hjälp av Data Management Gateway. Se [flytta data mellan lokala platser och molnartikel](data-factory-move-data-between-onprem-and-cloud.md) om du vill veta mer om Data Management Gateway och steg-för-steg-instruktioner om hur du konfigurerar gatewayen. Använd gatewayen för att ansluta till ett ODBC-datalager även om den finns i en Azure IaaS VM.

Du kan installera gatewayen på samma lokala dator eller Azure VM som ODBC-datalagret. Vi rekommenderar dock att du installerar gatewayen på en separat dator/Azure IaaS VM för att undvika resurskonkurrens och för bättre prestanda. När du installerar gatewayen på en separat dator bör maskinen kunna komma åt datorn med ODBC-datalagret.

Förutom Data Management Gateway måste du också installera ODBC-drivrutinen för datalagret på gateway-datorn.

> [!NOTE]
> Se [Felsöka gatewayproblem](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) för tips om felsökning av anslutnings-/gatewayrelaterade problem.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopieringsaktivitet som flyttar data från ett ODBC-datalager med hjälp av olika verktyg/API:er.

Det enklaste sättet att skapa en pipeline är att använda **kopieringsguiden**. Se [självstudiekurs: Skapa en pipeline med hjälp av kopieringsguiden](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång när du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-mall,** **.NET API**och REST **API**. Se [Kopiera aktivitetshandledning](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för steg-för-steg-instruktioner för att skapa en pipeline med en kopieringsaktivitet.

Oavsett om du använder verktygen eller API:erna utför du följande steg för att skapa en pipeline som flyttar data från ett källdatalager till ett sink-datalager:

1. Skapa **länkade tjänster** för att länka in- och utdatalager till datafabriken.
2. Skapa **datauppsättningar** för att representera in- och utdata för kopieringen.
3. Skapa en **pipeline** med en kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata.

När du använder guiden skapas JSON-definitioner för dessa datafabrikentiteter (länkade tjänster, datauppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API:er (förutom .NET API) definierar du dessa datafabrikentiteter med hjälp av JSON-formatet. Ett exempel på ett exempel med JSON-definitioner för datafabrikentiteter som används för att kopiera data från ett ODBC-datalager finns i [JSON-exempel: Kopiera data från ODBC-datalagring till Azure Blob-avsnittet](#json-example-copy-data-from-odbc-data-store-to-azure-blob) i den här artikeln.

I följande avsnitt finns information om JSON-egenskaper som används för att definiera datafabrikentiteter som är specifika för ODBC-datalager:

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
I följande tabell beskrivs JSON-element som är specifika för ODBC-länkad tjänst.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen Type måste anges till: **OnPremisesOdbc** |Ja |
| Connectionstring |Den icke-åtkomst-referensdelen av anslutningssträngen och en valfri krypterad autentiseringsåtkomst. Se exempel i följande avsnitt. <br/><br/>Du kan ange anslutningssträngen med mönster som `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`, eller använda systemet DSN (Data Source Name) som du ställer in på gateway-datorn med `"DSN=<name of the DSN>;"` (du måste fortfarande ange autentiseringsuppgifterna i den länkade tjänsten därefter). |Ja |
| credential |Åtkomstautentiseringsdelen av anslutningssträngen som anges i drivrutinsspecifikt egenskapsvärdeformat. Exempel: `"Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;"`. |Inga |
| authenticationType |Typ av autentisering som används för att ansluta till ODBC-datalagret. Möjliga värden är: Anonym och Basic. |Ja |
| userName |Ange användarnamnet om du använder grundläggande autentisering. |Inga |
| password |Ange lösenordet för det användarkonto som du angav för användarnamnet. |Inga |
| gatewayName (gatewayName) |Namn på den gateway som datafabrikstjänsten ska använda för att ansluta till ODBC-datalagret. |Ja |

### <a name="using-basic-authentication"></a>Använda grundläggande autentisering

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
### <a name="using-basic-authentication-with-encrypted-credentials"></a>Använda grundläggande autentisering med krypterade autentiseringsuppgifter
Du kan kryptera autentiseringsuppgifterna med cmdleten [New-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) (1.0 version av Azure PowerShell) eller [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/dn834940.aspx) (0.9 eller tidigare version av Azure PowerShell).

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

### <a name="using-anonymous-authentication"></a>Använda anonym autentisering

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
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Skapa datauppsättningar.](data-factory-create-datasets.md) Avsnitt som struktur, tillgänglighet och princip för en datauppsättning JSON är liknande för alla datauppsättningstyper (Azure SQL, Azure blob, Azure-tabell osv.).

Avsnittet **typeProperties** är olika för varje typ av datauppsättning och ger information om platsen för data i datalagret. Avsnittet typeProperties för datauppsättning av typen **RelationalTable** (som inkluderar ODBC-datauppsättning) har följande egenskaper

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen i ODBC-datalagret. |Ja |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Skapa pipelines.](data-factory-create-pipelines.md) Egenskaper som namn, beskrivning, indata- och utdatatabeller och principer är tillgängliga för alla typer av aktiviteter.

Egenskaper som är tillgängliga i avsnittet **typEgenskaper** i aktiviteten å andra sidan varierar beroende på varje aktivitetstyp. För kopieringsaktivitet varierar de beroende på vilka typer av källor och sänkor som finns.

I kopieringsaktivitet, när källan är av typen **RelationalSource** (som inkluderar ODBC), är följande egenskaper tillgängliga i avsnittet typeProperties:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd den anpassade frågan för att läsa data. |SQL-frågesträng. Till exempel: välj * från MyTable. |Ja |


## <a name="json-example-copy-data-from-odbc-data-store-to-azure-blob"></a>JSON-exempel: Kopiera data från ODBC-datalager till Azure Blob
Det här exemplet innehåller JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Den visar hur du kopierar data från en ODBC-källa till en Azure Blob Storage. Data kan dock kopieras till någon av de diskhoar som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av kopieringsaktiviteten i Azure Data Factory.

Exemplet har följande datafabriksenheter:

1. En länkad tjänst av typen [OnPremisesOdbc](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. En [indatauppsättning](data-factory-create-datasets.md) av typen [RelationalTable](#dataset-properties).
4. En [utdatauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med kopieringsaktivitet som använder [RelationalSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från en fråga resulterar i ett ODBC-datalager till en blob varje timme. De JSON-egenskaper som används i dessa prover beskrivs i avsnitt som följer proverna.

Som ett första steg ställer du in datahanteringsgatewayen. Instruktionerna finns i [flyttdata mellan lokala platser och molnartikel.](data-factory-move-data-between-onprem-and-cloud.md)

**ODBC-länkad tjänst** I det här exemplet används grundläggande autentisering. Se [avsnittet ODBC-länkad tjänst](#linked-service-properties) för olika typer av autentisering som du kan använda.

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

**Azure Storage-länkad tjänst**

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

**ODBC-indatauppsättning**

Exemplet förutsätter att du har skapat en tabell "MyTable" i en ODBC-databas och den innehåller en kolumn som kallas "tidsstämpelmonlumn" för tidsseriedata.

Inställningen "extern": "true" informerar datafabrikstjänsten om att datauppsättningen är extern till datafabriken och inte produceras av en aktivitet i datafabriken.

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

Data skrivs till en ny blob varje timme (frekvens: timme, intervall: 1). Mappsökvägen för blobben utvärderas dynamiskt baserat på starttiden för det segment som bearbetas. Mappsökvägen använder delar av starttiden för år, månad, dag och timmar.

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

**Kopiera aktivitet i en pipeline med ODBC-källa (RelationalSource) och Blob sink (BlobSink)**

Pipelinen innehåller en kopieringsaktivitet som är konfigurerad för att använda dessa in- och utdatauppsättningar och som är schemalagd att köras varje timme. I JSON-definitionen för pipelinen anges **källtypen** till **RelationalSource** och **sink-typen** är inställd på **BlobSink**. Den SQL-fråga som **query** angetts för frågeegenskapen väljer de data som ska kopieras under den senaste timmen.

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
### <a name="type-mapping-for-odbc"></a>Typmappning för ODBC
Som nämns i artikeln [om dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) utför Copy-aktivitet automatiska typkonverteringar från källtyper till sinktyper med följande tvåstegsmetod:

1. Konvertera från ursprungliga källtyper till .NET-typ
2. Konvertera från .NET-typ till inbyggt handfattyp

När du flyttar data från ODBC-datalager mappas ODBC-datatyper till .NET-typer som nämns i avsnittet [ODBC-datatypmappningar.](https://msdn.microsoft.com/library/cc668763.aspx)

## <a name="map-source-to-sink-columns"></a>Kartkälla för att sänka kolumner
Mer information om hur du mappar kolumner i källdatauppsättning till kolumner i sink-datauppsättning finns [i Mappa datauppsättningskolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Repeterbar läsning från relationskällor
När du kopierar data från relationsdatalager bör du tänka på repeterbarhet för att undvika oavsiktliga resultat. I Azure Data Factory kan du köra ett segment manuellt igen. Du kan också konfigurera återförsöksprincipen för en datauppsättning så att ett segment körs igen när ett fel inträffar. När ett segment körs på något sätt måste du se till att samma data läss oavsett hur många gånger ett segment körs. Se [Repeterbar läsning från relationskällor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="troubleshoot-connectivity-issues"></a>Felsök anslutningsproblem
Om du vill felsöka anslutningsproblem använder du fliken **Diagnostik** i Configuration Manager för **Data Management Gateway**.

1. Starta **Konfigurationshanteraren för datahanteringsgateway**. Du kan antingen köra "C:\Program Files\Microsoft Data Management Gateway\1.0\Shared\ConfigManager.exe" direkt (eller) söka efter **Gateway** för att hitta en länk till **Microsoft Data Management Gateway-programmet** enligt följande bild.

    ![Sök gateway](./media/data-factory-odbc-connector/search-gateway.png)
2. Växla till fliken **Diagnostik.**

    ![Gatewaydiagnostik](./media/data-factory-odbc-connector/data-factory-gateway-diagnostics.png)
3. Välj **typ** av datalager (länkad tjänst).
4. Ange **autentisering** och ange **autentiseringsuppgifter** (eller) ange **anslutningssträng** som används för att ansluta till datalagret.
5. Klicka på **Testa anslutning** om du vill testa anslutningen till datalagret.

## <a name="performance-and-tuning"></a>Prestanda och justering
Se [Kopiera aktivitetsprestanda & justeringsguide](data-factory-copy-activity-performance.md) om du vill veta mer om viktiga faktorer som påverkar prestanda för datarörelser (kopieringsaktivitet) i Azure Data Factory och olika sätt att optimera den.
