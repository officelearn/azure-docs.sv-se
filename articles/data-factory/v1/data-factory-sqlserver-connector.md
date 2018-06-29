---
title: Flytta data till och från SQL Server | Microsoft Docs
description: Lär dig mer om hur du flyttar data till/från SQL Server-databas som är lokalt eller i en virtuell Azure-dator med hjälp av Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 864ece28-93b5-4309-9873-b095bbe6fedd
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c4038ea5a450f32a46f24a306d1ee30bd61308a5
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054593"
---
# <a name="move-data-to-and-from-sql-server-on-premises-or-on-iaas-azure-vm-using-azure-data-factory"></a>Flytta data till och från SQL Server lokalt eller på IaaS (Azure VM) med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-sqlserver-connector.md)
> * [Version 2 (aktuell version)](../connector-sql-server.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns [SQL Server-anslutningen i V2](../connector-sql-server.md).

Den här artikeln förklarar hur du använder aktiviteten kopiera i Azure Data Factory för att flytta data till/från en lokal SQL Server-databas. Den bygger på den [Data Movement aktiviteter](data-factory-data-movement-activities.md) artikel som presenterar en allmän översikt över dataflyttning med copy-aktivitet. 

## <a name="supported-scenarios"></a>Scenarier som stöds
Du kan kopiera data **från en SQL Server-databas** till följande data lagras:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Du kan kopiera data från följande datalager **till en SQL Server-databas**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-sql-server-versions"></a>SQL Server-versioner som stöds
Den här connector-stöd för SQL Server som kopierar data från/till följande versioner av instansen finns på lokalt eller i Azure IaaS med både SQL-autentisering och Windows-autentisering: SQL Server 2016, SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008, SQLServer 2005

## <a name="enabling-connectivity"></a>Aktivera anslutning
Koncept och steg som krävs för att ansluta med SQL Server finns lokalt eller i Azure IaaS (Infrastructure-as-a-Service) virtuella datorer är samma. I båda fallen måste behöver du använda Data Management Gateway-anslutningen.

Se [flytta data mellan lokala platser och moln](data-factory-move-data-between-onprem-and-cloud.md) artikeln innehåller information om Data Management Gateway och stegvisa instruktioner om hur du konfigurerar en gateway. Ställa in en gateway-instans är ett krav för att ansluta med SQL Server.

Medan du kan installera gatewayen på samma lokala dator eller molnet VM-instans som SQL-servern för bättre prestanda, rekommenderar vi att du installerar dem på separata datorer. Med gatewayen och SQL Server på separata datorer minskar resurskonflikter.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopia-aktivitet som flyttar data till/från en lokal SQL Server-databas med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline är att använda den **guiden Kopiera**. Finns [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall** , **.NET API**, och **REST API**. Se [kopiera aktivitet kursen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet. 

Om du använder verktyg eller API: er, kan du utföra följande steg för att skapa en pipeline som flyttar data från ett dataarkiv som källa till ett dataarkiv som mottagare: 

1. Skapa en **datafabriken**. En datafabrik kan innehålla en eller flera pipelines. 
2. Skapa **länkade tjänster** att länka inkommande och utgående data lagras till din data factory. Till exempel om du kopierar data från en SQL Server-databas till en Azure blob storage måste skapa du två länkade tjänster om du vill länka till SQL Server-databasen och Azure storage-konto till din data factory. Länkad tjänstegenskaper som är specifika för SQL Server-databasen, se [länkade tjänstegenskaper](#linked-service-properties) avsnitt. 
3. Skapa **datauppsättningar** att representera inkommande och utgående data för kopieringen. I det tidigare exemplet i det sista steget, skapar du en datauppsättning för att ange SQL-tabell i SQL Server-databasen som innehåller indata. Och du skapar en annan dataset för att ange blob-behållaren och mappen som innehåller de data som kopieras från SQL Server-databasen. Egenskaper för datamängd som är specifika för SQL Server-databasen, se [egenskaper för datamängd](#dataset-properties) avsnitt.
4. Skapa en **pipeline** med en kopia-aktivitet som tar en datamängd som indata och en dataset som utdata. I exemplet ovan kan använda du SqlSource som en källa och BlobSink som en mottagare för kopieringsaktiviteten. På samma sätt om du kopierar från Azure Blob Storage till SQL Server-databas, använder du BlobSource och SqlSink i kopieringsaktiviteten. Kopiera Aktivitetsegenskaper som är specifika för SQL Server-databas, se [kopiera Aktivitetsegenskaper](#copy-activity-properties) avsnitt. Mer information om hur du använder ett dataarkiv som en källa eller en mottagare klickar du på länken i föregående avsnitt för datalager. 

När du använder guiden skapas JSON definitioner för dessa Data Factory-enheter (länkade tjänster, datauppsättningar och pipelinen) automatiskt för dig. När du använder Verktyg/API: er (utom .NET API), kan du definiera dessa Data Factory-enheter med hjälp av JSON-format.  Exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data till/från en lokal SQL Server-databas finns [JSON-exempel](#json-examples-for-copying-data-from-and-to-sql-server) i den här artikeln. 

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory entiteter till SQL Server: 

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper
Du skapar en länkad tjänst av typen **OnPremisesSqlServer** att länka en lokal SQL Server-databas till en datafabrik. Följande tabell innehåller en beskrivning för JSON-element som är specifika för lokala SQL Server länkad tjänst.

Följande tabell innehåller en beskrivning för JSON-element som är specifika för SQL Server som är länkad tjänst.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Typegenskapen bör anges till: **OnPremisesSqlServer**. |Ja |
| connectionString |Ange connectionString information som behövs för att ansluta till lokal SQL Server-databasen med hjälp av SQL-autentisering eller Windows-autentisering. |Ja |
| gatewayName |Namnet på den gateway som Data Factory-tjänsten ska använda för att ansluta till lokal SQL Server-databasen. |Ja |
| användarnamn |Ange användarnamnet om du använder Windows-autentisering. Exempel: **domainname\\användarnamn**. |Nej |
| lösenord |Ange lösenordet för det användarkonto som du angav för användarnamnet. |Nej |

Du kan kryptera autentiseringsuppgifterna med hjälp av den **ny AzureRmDataFactoryEncryptValue** cmdlet och använda dem i anslutningssträngen som visas i följande exempel (**EncryptedCredential** egenskapen):  

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

### <a name="samples"></a>Exempel
**JSON för att använda SQL-autentisering**

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties":
    {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
**JSON för att använda Windows-autentisering**

Data Management Gateway kommer att personifiera det angivna användarkontot kan ansluta till lokal SQL Server-databasen. 

```json
{
     "Name": " MyOnPremisesSQLDB",
     "Properties":
     {
         "type": "OnPremisesSqlServer",
         "typeProperties": {
             "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
             "username": "<domain\\username>",
             "password": "<password>",
             "gatewayName": "<gateway name>"
        }
     }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd
I prover, du har använt ett DataSet-objekt av typen **SqlServerTable** som representerar en tabell i SQL Server-databasen.  

En fullständig lista över egenskaper som är tillgängliga för att definiera datauppsättningarna & avsnitt finns i [skapa datauppsättningar](data-factory-create-datasets.md) artikel. Avsnitt som struktur, tillgänglighet och princip på en datamängd JSON är liknande för alla typer av datauppsättningen (SQL Server, Azure blob, Azure-tabellen, osv).

Avsnittet typeProperties är olika för varje typ av dataset och ger information om placeringen av data i datalagret. Den **typeProperties** avsnittet för datauppsättningen av typen **SqlServerTable** har följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på den tabell eller vy i SQL Server-databasinstansen som den länkade tjänsten refererar till. |Ja |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
Om du flyttar data från en SQL Server-databas måste du ange källtypen i kopieringsaktiviteten till **SqlSource**. På samma sätt om du flyttar data till en SQL Server-databas måste du anger sink i kopieringsaktiviteten till **SqlSink**. Det här avsnittet innehåller en lista över egenskaper som stöds av SqlSource och SqlSink.

En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i [skapar Pipelines](data-factory-create-pipelines.md) artikel. Egenskaper som namn, beskrivning, ingående och utgående tabeller och principer är tillgängliga för alla typer av aktiviteter.

> [!NOTE]
> Kopieringsaktiviteten tar endast en inmatning och ger en enda utdata.

De egenskaper som är tillgängliga i avsnittet typeProperties i aktiviteten varierar beroende på varje aktivitetstyp. För Kopieringsaktivitet kan variera de beroende på vilka typer av datakällor och sänkor.

### <a name="sqlsource"></a>SqlSource
När datakällan i en Kopieringsaktivitet är av typen **SqlSource**, följande egenskaper är tillgängliga i **typeProperties** avsnitt:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| sqlReaderQuery |Använd anpassad fråga för att läsa data. |SQL-sträng. Till exempel: Välj * från mytable prefix. Kan referera till flera tabeller från databasen som refereras av inkommande dataset. Om inget annat anges, SQL-instruktionen som körs: Välj från mytable prefix. |Nej |
| sqlReaderStoredProcedureName |Namnet på den lagrade proceduren som läser data från källtabellen. |Namnet på den lagrade proceduren. Den senaste SQL-instruktionen måste vara en SELECT-instruktion i den lagrade proceduren. |Nej |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värde-par. Namn och skiftläge parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. |Nej |

Om den **sqlReaderQuery** har angetts för SqlSource, kopiera-aktivitet körs den här frågan mot SQL Server-databas källan för att hämta data.

Du kan också ange en lagrad procedur genom att ange den **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren har parametrar).

Om du inte anger sqlReaderQuery eller sqlReaderStoredProcedureName, används de kolumner som anges i strukturen för att skapa en select-frågan ska köras mot SQL Server-databasen. Om datauppsättningsdefinitionen saknar strukturen, markeras alla kolumner från tabellen.

> [!NOTE]
> När du använder **sqlReaderStoredProcedureName**, du måste ange ett värde för den **tableName** egenskap i datauppsättningen JSON. Det finns inga verifieringar utföras om mot den här tabellen.

### <a name="sqlsink"></a>SqlSink
**SqlSink** stöder följande egenskaper:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| writeBatchTimeout |Vänta tills batch insert-åtgärden ska slutföras innan tidsgränsen uppnås. |TimeSpan<br/><br/> Exempel ”: 00: 30:00” (30 minuter). |Nej |
| writeBatchSize |Infogar data i SQL-tabellen när buffertstorleken når writeBatchSize. |Heltal (antalet rader) |Nej (standard: 10000) |
| sqlWriterCleanupScript |Ange frågan för Kopieringsaktiviteten att köra så att data för ett visst segment har rensats bort. Mer information finns i [repeterbara kopiera](#repeatable-copy) avsnitt. |En frågesats. |Nej |
| sliceIdentifierColumnName |Ange kolumnnamnet för Kopieringsaktiviteten med genereras automatiskt segment-ID, som används för att rensa data för ett visst segment när köras på nytt. Mer information finns i [repeterbara kopiera](#repeatable-copy) avsnitt. |Kolumnnamnet för en kolumn med datatypen för binary(32). |Nej |
| sqlWriterStoredProcedureName |Namnet på den lagrade proceduren som definierar hur ska gälla källdata i måltabellen, t.ex. vill upserts eller transformera med hjälp av egna affärslogik. <br/><br/>Observera den här lagrade proceduren kommer att **anropas per batch**. Om du vill göra åtgärden som endast körs en gång och har ingenting att göra med källdata som t.ex. Ta bort/trunkera använder `sqlWriterCleanupScript` egenskapen. |Namnet på den lagrade proceduren. |Nej |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värde-par. Namn och skiftläge parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. |Nej |
| sqlWriterTableType |Ange tabellen typnamn som ska användas i den lagrade proceduren. Kopieringsaktiviteten tillhandahåller data flyttas i en temporär tabell med den här tabellen. Lagrade procedurer kan sedan koppla data kopieras med befintliga data. |Ett namn för tabellen. |Nej |


## <a name="json-examples-for-copying-data-from-and-to-sql-server"></a>JSON-exempel för att kopiera data från och till SQL Server
Följande exempel ger exempel JSON definitioner som du kan använda för att skapa en pipeline med hjälp av [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Följande exempel visar hur du kopierar data till och från SQL Server och Azure Blob Storage. Dock datan kan kopieras **direkt** från alla källor till någon av sänkor anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av aktiviteten kopiera i Azure Data Factory.     

## <a name="example-copy-data-from-sql-server-to-azure-blob"></a>Exempel: Kopiera data från SQL Server till Azure-Blob
I följande exempel visas:

1. En länkad tjänst av typen [OnPremisesSqlServer](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Indata [dataset](data-factory-create-datasets.md) av typen [SqlServerTable](#dataset-properties).
4. Utdata [dataset](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Den [pipeline](data-factory-create-pipelines.md) med kopieringsaktiviteten som använder [SqlSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar time series-data från en tabell i SQL Server till en Azure blob varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

Som ett första steg bör du konfigurera data management gateway. Anvisningarna är i den [flytta data mellan lokala platser och moln](data-factory-move-data-between-onprem-and-cloud.md) artikel.

**SQL Server som är länkad tjänst**
```json
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```
**Azure Blob länkade lagringstjänsten**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**SQL Server inkommande dataset**

Exemplet förutsätter att du har skapat en tabell ”mytable” som prefix i SQL Server och den innehåller en kolumn med namnet ”timestampcolumn” för tid series-data. Du kan fråga över flera tabeller i samma databas med hjälp av en enda dataset, men en enskild tabell måste användas för att datauppsättningen tableName typeProperty.

Inställningen ”externa”: ”true” informerar Data Factory-tjänsten att datamängden är extern till data factory och inte tillverkas av en aktivitet i datafabriken.

```json
{
  "name": "SqlServerInput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
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

Data skrivs till en ny blob varje timme (frekvens: timme, intervall: 1). Sökvägen till mappen för blobben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Mappsökvägen använder år, månad, dag och timmar delar av starttiden.

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Pipeline med kopieringsaktiviteten**

Pipelinen innehåller en kopia-aktivitet som är konfigurerad för att använda dessa indata och utdata-datauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen av **källa** är inställd på **SqlSource** och **sink** är inställd på **BlobSink**. SQL-frågan som angetts för den **SqlReaderQuery** egenskapen väljer vilka data under den senaste timmen att kopiera.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2016-06-01T18:00:00",
    "end":"2016-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[  
      {
        "name": "SqlServertoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
     ]
   }
}
```
I det här exemplet **sqlReaderQuery** har angetts för SqlSource. Kopieringsaktiviteten körs den här frågan mot SQL Server-databas källan för att hämta data. Du kan också ange en lagrad procedur genom att ange den **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren har parametrar). SqlReaderQuery kan referera till flera tabeller i databasen som refereras av inkommande dataset. Det är inte begränsad till endast tabellen som den dataset tableName typeProperty.

Om du inte anger sqlReaderQuery eller sqlReaderStoredProcedureName, används de kolumner som anges i strukturen för att skapa en select-frågan ska köras mot SQL Server-databasen. Om datauppsättningsdefinitionen saknar strukturen, markeras alla kolumner från tabellen.

Finns det [Sql-källans](#sqlsource) avsnitt och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) lista över egenskaper som stöds av SqlSource och BlobSink.

## <a name="example-copy-data-from-azure-blob-to-sql-server"></a>Exempel: Kopiera data från Azure Blob till SQL Server
I följande exempel visas:

1. Den länkade tjänsten av typen [OnPremisesSqlServer](#linked-service-properties).
2. Den länkade tjänsten av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Indata [dataset](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Utdata [dataset](data-factory-create-datasets.md) av typen [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties).
5. Den [pipeline](data-factory-create-pipelines.md) med kopieringsaktiviteten som använder [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) och [SqlSink](#sql-server-copy-activity-type-properties).

Exempel kopiorna tidsserier data från ett Azure blob till en SQL Server tabellen varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

**SQL Server som är länkad tjänst**

```json
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```
**Azure Blob länkade lagringstjänsten**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Azure Blob-inkommande datamängd**

Data hämtas från en ny blob varje timme (frekvens: timme, intervall: 1). Mappen sökvägen och filnamnet för blobben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Mappsökvägen använder år, månad och som en dagdel av starttiden filnamn används i timmen som en del av starttiden. ”externa”: ”true” inställningen informerar Data Factory-tjänsten att datamängden är extern till data factory och inte tillverkas av en aktivitet i datafabriken.

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
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
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
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
**SQL Server-utdatauppsättningen**

Exemplet kopierar data till en tabell med namnet ”mytable” som prefix i SQL Server. Skapa tabellen i SQL Server med samma antal kolumner som du förväntar dig Blob CSV-fil som innehåller. Nya rader läggs till i tabell varje timme.

```json
{
  "name": "SqlServerOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Pipeline med kopieringsaktiviteten**

Pipelinen innehåller en kopia-aktivitet som är konfigurerad för att använda dessa indata och utdata-datauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen av **källa** är inställd på **BlobSource** och **sink** är inställd på **SqlSink**.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": " SqlServerOutput "
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```

## <a name="troubleshooting-connection-issues"></a>Felsökning av anslutningsproblem med
1. Konfigurera SQL Server för att acceptera fjärranslutningar. Starta **SQL Server Management Studio**, högerklicka på **server**, och klicka på **egenskaper**. Välj **anslutningar** i listan och kontrollera **tillåta fjärranslutningar till servern**.

    ![Aktivera fjärranslutningar](./media/data-factory-sqlserver-connector/AllowRemoteConnections.png)

    Se [konfigurerar fjärråtkomst Server Configuration Option](https://msdn.microsoft.com/library/ms191464.aspx) detaljerade anvisningar.
2. Starta **SQL Server Configuration Manager**. Expandera **SQL Server-nätverkskonfigurationen** för instansen och väljer **protokoll för MSSQLSERVER**. Du bör se protokoll i den högra rutan. Aktivera TCP/IP genom att högerklicka på **TCP/IP** och klicka på **aktivera**.

    ![Aktivera TCP/IP](./media/data-factory-sqlserver-connector/EnableTCPProptocol.png)

    Se [aktivera eller inaktivera nätverksprotokoll Server](https://msdn.microsoft.com/library/ms191294.aspx) information och alternativa metoder för att aktivera TCP/IP-protokollet.
3. Dubbelklicka i samma fönster **TCP/IP** att starta **TCP/IP-egenskaper** fönster.
4. Växla till den **IP-adresser** fliken. Rulla Se **IPAll** avsnitt. Notera den ** TCP-Port ** (standard är **1433**).
5. Skapa en **regeln för Windows-brandväggen** på datorn för att tillåta inkommande trafik via den här porten.  
6. **Verifiera anslutning**: använda SQL Server Management Studio för att ansluta till SQL Server med hjälp av fullständigt kvalificerat namn, från en annan dator. Till exempel: ”<machine>.<domain>.corp.<company>.com,1433.”

   > [!IMPORTANT]

   > Se [flytta data mellan lokala källor och molnet med Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) detaljerad information.
   >
   > Se [felsökning av problem med gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) tips om hur du felsöker anslutning /-gateway relaterade problem.
   >
   >


## <a name="identity-columns-in-the-target-database"></a>Identitetskolumner i måldatabasen
Det här avsnittet innehåller ett exempel som kopierar data från en källtabell med någon identity-kolumn till en måltabell med en identitetskolumn.

**Källtabellen:**

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```
**Måltabellen:**

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

Observera att måltabellen har en identitetskolumn.

**Källa JSON-definitionen för datauppsättning**

```json
{
    "name": "SampleSource",
    "properties": {
        "published": false,
        "type": " SqlServerTable",
        "linkedServiceName": "TestIdentitySQL",
        "typeProperties": {
            "tableName": "SourceTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```
**Mål JSON-definitionen för datauppsättning**

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "TestIdentitySQLSource",
        "typeProperties": {
            "tableName": "TargetTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

Observera att som käll- och tabellen har olika schema (målet har en ytterligare kolumn med identity). I det här scenariot måste du ange **struktur** egenskap i datauppsättningsdefinitionen mål som inte innehåller en identity-kolumn.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Anropa lagrade procedur från SQL-mottagare
Se [anropa lagrad procedur för SQL-mottagare i en Kopieringsaktivitet](data-factory-invoke-stored-procedure-from-copy-activity.md) artikel ett exempel på att anropa en lagrad procedur från SQL-mottagare i en Kopieringsaktivitet för en pipeline.

## <a name="type-mapping-for-sql-server"></a>Mappning för SQLServer
Som anges i den [data movement aktiviteter](data-factory-data-movement-activities.md) artikel, kopieringsaktiviteten utför automatisk konverteringar från källtyper att registrera typer med följande metod i steg 2:

1. Konvertera från interna källtyper till .NET-typ
2. Konvertera från .NET-typ till interna mottagare typ.

När data flyttas till och från SQLServer, används följande mappning från SQL-typen till .NET-typ och vice versa.

Mappningen är densamma som SQL-Server-Datatypsmappningen för ADO.NET.

| SQL Server Database Engine-typ | .NET framework-typ |
| --- | --- |
| bigint |Int64 |
| Binär |Byte] |
| bitar |Boolesk |
| Char |Sträng, Char] |
| datum |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| DateTimeOffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM-attributet (varbinary(max)) |Byte] |
| Flyttal |Dubbel |
| image |Byte] |
| int |Int32 |
| Money |Decimal |
| nchar |Sträng, Char] |
| ntext |Sträng, Char] |
| numeriskt |Decimal |
| nvarchar |Sträng, Char] |
| Verklig |Enkel |
| ROWVERSION |Byte] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Objektet * |
| text |Sträng, Char] |
| time |TimeSpan |
| tidsstämpel |Byte] |
| tinyint |Byte |
| Unik identifierare |GUID |
| varbinary |Byte] |
| varchar |Sträng, Char] |
| xml |Xml |

## <a name="mapping-source-to-sink-columns"></a>Mappning källan till mottagare för kolumner
Om du vill mappa kolumner från källan dataset till kolumner från sink dataset finns [mappa dataset kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Repeterbara kopia
När du kopierar data till SQL Server-databas, lägger kopieringsaktiviteten data till tabellen mottagare som standard. För att genomföra en UPSERT i stället Se [Repeatable skriva till SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) artikel. 

Tänk på att undvika oväntade resultat repeterbarhet när kopiering av data från relationella data lagras. I Azure Data Factory, kan du köra en sektor manuellt. Du kan också konfigurera i principen för en dataset så att ett segment som körs när ett fel uppstår. När ett segment körs på något sätt, måste du kontrollera att samma data läses oavsett hur många gånger ett segment körs. Se [Repeatable läsa från relationella källor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda och finjustering
Se [kopiera aktivitet prestanda och justera guiden](data-factory-copy-activity-performance.md) vill veta mer om viktiga faktorer som påverkan prestanda för flytt av data (Kopieringsaktiviteten) i Azure Data Factory och olika sätt att optimera den.
