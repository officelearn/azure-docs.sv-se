---
title: Kopiera data till och från Azure SQL Data Warehouse | Microsoft Docs
description: Lär dig hur du kopierar data till och från Azure SQL Data Warehouse med Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: d90fa9bd-4b79-458a-8d40-e896835cfd4a
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 4c431b149edb0677585da3c84e37d64873478ccf
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57432744"
---
# <a name="copy-data-to-and-from-azure-sql-data-warehouse-using-azure-data-factory"></a>Kopieringsdata till och från Azure SQL Data Warehouse med Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-azure-sql-data-warehouse-connector.md)
> * [Version 2 (aktuell version)](../connector-azure-sql-data-warehouse.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [Azure SQL Data Warehouse-anslutning i V2](../connector-azure-sql-data-warehouse.md).

Den här artikeln förklarar hur du använder Kopieringsaktivitet i Azure Data Factory för att flytta data till och från Azure SQL Data Warehouse. Den bygger på den [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikel som anger en allmän översikt över dataförflyttning med kopieringsaktiviteten.

> [!TIP]
> För att uppnå bästa prestanda kan du använda PolyBase för att läsa in data i Azure SQL Data Warehouse. Den [använda PolyBase för att läsa in data i Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) -avsnittet innehåller information om. En genomgång med ett användningsfall finns i [läsa in 1 TB i Azure SQL Data Warehouse under 15 minuter med Azure Data Factory](data-factory-load-sql-data-warehouse.md).

## <a name="supported-scenarios"></a>Scenarier som stöds
Du kan kopiera data **från Azure SQL Data Warehouse** till följande data lagras:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Du kan kopiera data från följande datalager **till Azure SQL Data Warehouse**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!TIP]
> När du kopierar data från SQL Server eller Azure SQL Database till Azure SQL Data Warehouse, om tabellen inte finns i målarkiv, kan Data Factory automatiskt skapa tabellen i SQL Data Warehouse med hjälp av schemat för tabellen i källdatalagret. Se [automatiskt när tabellen skulle skapas](#auto-table-creation) mer information.

## <a name="supported-authentication-type"></a>Autentiseringstyp som stöds
Azure SQL Data Warehouse connector stöder grundläggande autentisering.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en Kopieringsaktivitet som flyttar data till/från en Azure SQL Data Warehouse med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline som kopierar data till och från Azure SQL Data Warehouse är att använda guiden Kopiera data. Se [självstudien: Läs in data till SQL Data Warehouse med Data Factory](../../sql-data-warehouse/sql-data-warehouse-load-with-data-factory.md) en snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall**, **.NET API**, och  **REST-API**. Se [kopiera aktivitet självstudien](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Om du använder verktyg eller API: er kan utföra du följande steg för att skapa en pipeline som flyttar data från källans datalager till mottagarens datalager:

1. Skapa en **datafabrik**. En datafabrik kan innehålla en eller flera pipelines. 
2. Skapa **länkade tjänster** länka inkommande och utgående data du lagrar till din datafabrik. Till exempel om du kopierar data från Azure blob storage till en Azure SQL data warehouse, skapa du två länkade tjänster för att länka ditt Azure storage-konto och Azure SQL data warehouse till din datafabrik. Länkade tjänstegenskaper som är specifika för Azure SQL Data Warehouse, se [länkade tjänstegenskaper](#linked-service-properties) avsnittet. 
3. Skapa **datauppsättningar** som representerar inkommande och utgående data för kopieringen. I det tidigare exemplet i det sista steget, skapar du en datauppsättning för att ange blobbehållare och mapp som innehåller indata. Och skapar du en annan datauppsättning för att ange tabellen i Azure SQL data warehouse som innehåller de data som kopieras från blob storage. Egenskaper för datamängd som är specifika för Azure SQL Data Warehouse, se [egenskaper för datamängd](#dataset-properties) avsnittet.
4. Skapa en **pipeline** med en Kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata. I exemplet som tidigare nämnts, använder du BlobSource som en källa och SqlDWSink som mottagare för kopieringsaktiviteten. På samma sätt, om du kopierar från Azure SQL Data Warehouse till Azure Blob Storage, använder du SqlDWSource och BlobSink i kopieringsaktiviteten. Kopiera Aktivitetsegenskaper som är specifika för Azure SQL Data Warehouse, se [kopiera Aktivitetsegenskaper](#copy-activity-properties) avsnittet. Mer information om hur du använder ett datalager som en källa eller mottagare klickar du på länken i föregående avsnitt för ditt datalager.

När du använder guiden skapas JSON-definitioner för dessa Data Factory-entiteter (länkade tjänster, datauppsättningar och pipeline) automatiskt åt dig. När du använder Verktyg/API: er (med undantag för .NET-API) kan definiera du dessa Data Factory-entiteter med hjälp av JSON-format. Exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data till/från en Azure SQL Data Warehouse finns [JSON-exempel](#json-examples-for-copying-data-to-and-from-sql-data-warehouse) i den här artikeln.

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory-entiteter som är specifika för Azure SQL Data Warehouse:

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
Följande tabell innehåller en beskrivning för JSON-element som är specifika för Azure SQL Data Warehouse länkad tjänst.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Type-egenskapen måste anges till: **AzureSqlDW** |Ja |
| connectionString |Ange information som behövs för att ansluta till Azure SQL Data Warehouse-instans för connectionString-egenskapen. Endast grundläggande autentisering stöds. |Ja |

> [!IMPORTANT]
> Konfigurera [Azure SQL Database-brandvägg](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) och databasservern till [ge Azure-tjänster åtkomst till servern](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Om du kopierar data till Azure SQL Data Warehouse från utanför Azure, inklusive från lokala datakällor med data factory gateway måste du dessutom konfigurera lämplig IP-adressintervall för den dator som skickar data till Azure SQL Data Warehouse.

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [skapar datauppsättningar](data-factory-create-datasets.md) artikeln. Avsnitt som struktur, tillgänglighet och princip av en datauppsättnings-JSON är liknande för alla datauppsättningstyper av (Azure SQL, Azure-blob, Azure-tabell osv.).

Avsnittet typeProperties är olika för varje typ av datauppsättning och tillhandahåller information om platsen för data i datalagret. Den **typeProperties** avsnittet för datauppsättningen av typen **AzureSqlDWTable** har följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på tabellen eller vyn i Azure SQL Data Warehouse-databas som den länkade tjänsten refererar till. |Ja |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [skapa Pipelines](data-factory-create-pipelines.md) artikeln. Egenskaper, till exempel namn, beskrivning, indata och utdata tabeller och principen är tillgängliga för alla typer av aktiviteter.

> [!NOTE]
> Kopieringsaktivitet tar bara en inmatning och ger endast en utdata.

Medan egenskaper som är tillgängliga i avsnittet typeProperties aktivitetens varierar med varje aktivitetstyp av. För kopieringsaktiviteten variera de beroende på vilka typer av källor och mottagare.

### <a name="sqldwsource"></a>SqlDWSource
När källan är av typen **SqlDWSource**, följande egenskaper är tillgängliga i **typeProperties** avsnittet:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| sqlReaderQuery |Använd anpassad fråga för att läsa data. |SQL-sträng. Till exempel: Välj * från MyTable. |Nej |
| sqlReaderStoredProcedureName |Namnet på den lagrade proceduren som läser data från källtabellen. |Namnet på den lagrade proceduren. Den senaste SQL-instruktionen måste vara en SELECT-instruktion i den lagrade proceduren. |Nej |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värde-par. Namn och versaler och gemener i parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. |Nej |

Om den **sqlReaderQuery** har angetts för SqlDWSource, Kopieringsaktivitet körs den här frågan mot Azure SQL Data Warehouse-källa för att hämta data.

Du kan också ange en lagrad procedur genom att ange den **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren tar parametrar).

Om du inte anger sqlReaderQuery eller sqlReaderStoredProcedureName, används de kolumner som definierats i avsnittet strukturen i datauppsättnings-JSON för att skapa en fråga ska köras på Azure SQL Data Warehouse. Exempel: `select column1, column2 from mytable`. Om definitionen för datauppsättningen inte har strukturen, markeras alla kolumner från tabellen.

#### <a name="sqldwsource-example"></a>SqlDWSource exempel

```JSON
"source": {
    "type": "SqlDWSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```
**Definitionen av lagrade proceduren:**

```SQL
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sqldwsink"></a>SqlDWSink
**SqlDWSink** har stöd för följande egenskaper:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Ange en fråga för Kopieringsaktiviteten till att köra så att data för en viss sektor rensas. Mer information finns i [repeterbarhet avsnittet](#repeatability-during-copy). |Ett frågeuttryck. |Nej |
| allowPolyBase |Anger om du vill använda PolyBase (om tillämpligt) i stället för BULKINSERT mekanism. <br/><br/> **Med PolyBase är det rekommenderade sättet att läsa in data i SQL Data Warehouse.** Se [använda PolyBase för att läsa in data i Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) avsnittet begränsningar och information. |True <br/>FALSKT (standard) |Nej |
| polyBaseSettings |En grupp egenskaper som kan anges när den **allowPolybase** är inställd på **SANT**. |&nbsp; |Nej |
| rejectValue |Anger det tal eller procentandelen rader som kan avvisas innan frågan inte kunde köras. <br/><br/>Mer information om den PolyBase avvisningsalternativen i den **argument** delen av [Skapa extern tabell (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) avsnittet. |0 (standardvärde), 1, 2... |Nej |
| rejectType |Anger om alternativet rejectValue anges som ett exakt värde eller en procentandel. |Värde (standard), procent |Nej |
| rejectSampleValue |Anger antalet rader som ska hämtas innan PolyBase beräknar om procentandelen avvisade raden. |1, 2, … |Ja, om **rejectType** är **procent** |
| useTypeDefault |Anger hur du hanterar värden som saknas i avgränsade textfiler när PolyBase hämtar data från textfilen.<br/><br/>Mer information om den här egenskapen från avsnittet argument i [skapa externt FILFORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |SANT, FALSKT (standard) |Nej |
| WriteBatchSize |Infogar data i SQL-tabell när buffertstorleken når writeBatchSize |Heltal (antal rader) |Nej (standard: 10000) |
| writeBatchTimeout |Väntetid för batch insert-åtgärden ska slutföras innan tidsgränsen uppnås. |Tidsintervall<br/><br/> Exempel: ”00: 30:00” (30 minuter). |Nej |

#### <a name="sqldwsink-example"></a>SqlDWSink example

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Använda PolyBase för att läsa in data i Azure SQL Data Warehouse
Med hjälp av **[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)** är ett effektivt sätt för att läsa in stora mängder data till Azure SQL Data Warehouse med högt dataflöde. Du kan se en stor vinst i dataflödet med PolyBase i stället för BULKINSERT standardmekanismen. Se [kopiera prestanda referensnummer](data-factory-copy-activity-performance.md#performance-reference) med detaljerad jämförelse. En genomgång med ett användningsfall finns i [läsa in 1 TB i Azure SQL Data Warehouse under 15 minuter med Azure Data Factory](data-factory-load-sql-data-warehouse.md).

* Om dina källdata finns i **Azure Blob eller Azure Data Lake Store**, och detta format är kompatibelt med PolyBase kan du kopiera direkt till Azure SQL Data Warehouse med PolyBase. Se **[direkt kopia med PolyBase](#direct-copy-using-polybase)** med information.
* Om dina källdatalagret och format inte stöds ursprungligen av PolyBase, kan du använda den **[mellanlagrad kopiering med PolyBase](#staged-copy-using-polybase)** funktionen i stället. Det ger dig också bättre genomströmning genom att automatiskt konvertera data till PolyBase-kompatibelt format och lagra data i Azure Blob storage. Den sedan läser in data till SQL Data Warehouse.

Ange den `allowPolyBase` egenskap **SANT** som visas i följande exempel för Azure Data Factory för att använda PolyBase för att kopiera data till Azure SQL Data Warehouse. När du anger allowPolyBase till true anger du PolyBase specifika egenskaper med hjälp av den `polyBaseSettings` Egenskapsgruppen. se den [SqlDWSink](#SqlDWSink) finns mer information om egenskaper som du kan använda med polyBaseSettings.

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

### <a name="direct-copy-using-polybase"></a>Direct kopia med PolyBase
SQL Data Warehouse PolyBase stöd direkt för Azure Blob- och Azure Data Lake Store (med tjänstens huvudnamn) som källa och kraven för specifika fil-format. Om dina källdata uppfyller kriterierna som beskrivs i det här avsnittet, kan du direkt kopiera från källans datalager till Azure SQL Data Warehouse med PolyBase. Annars kan du använda [mellanlagrad kopiering med PolyBase](#staged-copy-using-polybase).

> [!TIP]
> Om du vill kopiera data från Data Lake Store till SQL Data Warehouse effektivt, lär du dig mer från [Azure Data Factory gör det ännu enklare och praktiskt att få fram insikter från data när du använder Data Lake Store med SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Om kraven inte uppfylls, Azure Data Factory kontrollerar du inställningarna och faller automatiskt tillbaka till BULKINSERT mekanism för dataförflyttning.

1. **Käll-länkade tjänst** är av typen: **AzureStorage** eller **AzureDataLakeStore med autentisering av tjänstens huvudnamn**.
2. Den **indatauppsättningen** är av typen: **AzureBlob** eller **AzureDataLakeStore**, och formatet skriver `type` egenskaper är **OrcFormat**, **ParquetFormat**, eller **TextFormat** med följande konfigurationer:

    1. `rowDelimiter` måste vara **\n**.
    2. `nullValue` anges till **tom sträng** (””), eller `treatEmptyAsNull` är inställd på **SANT**.
    3. `encodingName` anges till **utf-8**, vilket är **standard** värde.
    4. `escapeChar`, `quoteChar`, `firstRowAsHeader`, och `skipLineCount` har inte angetts.
    5. `compression` kan vara **ingen komprimering**, **GZip**, eller **Deflate**.

    ```JSON
    "typeProperties": {
        "folderPath": "<blobpath>",
        "format": {
            "type": "TextFormat",
            "columnDelimiter": "<any delimiter>",
            "rowDelimiter": "\n",
            "nullValue": "",
            "encodingName": "utf-8"
        },
        "compression": {
            "type": "GZip",
            "level": "Optimal"
        }
    },
    ```

3. Det finns inga `skipHeaderLineCount` inställningen **BlobSource** eller **AzureDataLakeStore** för kopieringsaktiviteten i pipelinen.
4. Det finns inga `sliceIdentifierColumnName` inställningen **SqlDWSink** för kopieringsaktiviteten i pipelinen. (PolyBase garanterar att alla data har uppdaterats eller ingenting har uppdaterats under en enda körning. Få **repeterbarhet**, kan du använda `sqlWriterCleanupScript`).
5. Det finns inga `columnMapping` som används i den associerade i kopieringen aktivitet.

### <a name="staged-copy-using-polybase"></a>Mellanlagrad kopiering med PolyBase
När dina källdata inte uppfyller de kriterier som presenteras i föregående avsnitt, kan du Aktivera kopiering av data via tillfälliga mellanlagringsplatsen Azure Blob Storage (inte får vara Premium Storage). I det här fallet utför Azure Data Factory automatiskt transformationer på data för att uppfylla krav om formatet för data med PolyBase och sedan använda PolyBase för att läsa in data till SQL Data Warehouse, och på senaste Rensa dina temp data från Blob storage. Se [mellanlagrad kopiering](data-factory-copy-activity-performance.md#staged-copy) för information om hur kopiering av data via en mellanlagrings Azure-Blob fungerar i allmänhet.

> [!NOTE]
> När kopierar data från en lokala datalager till Azure SQL Data Warehouse med PolyBase och mellanlagring, om din Data Management Gateway-version är lägre än 2.4 krävs JRE (Java Runtime Environment) på din gateway-dator som används för att omvandla din källa data i rätt format. Rekommenderar att du uppgraderar din gateway till den senaste versionen för att undvika sådana beroende.
>

Om du vill använda denna funktion kan du skapa en [länkad Azure Storage-tjänst](data-factory-azure-blob-connector.md#azure-storage-linked-service) som refererar till Azure Storage-konto som har tillfälliga blob-lagringen, ange sedan den `enableStaging` och `stagingSettings` egenskaperna för aktiviteten kopiera enligt följande kod:

```json
"activities":[
{
    "name": "Sample copy activity from SQL Server to SQL Data Warehouse via PolyBase",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDWOutput" }],
    "typeProperties": {
        "source": {
            "type": "SqlSource",
        },
        "sink": {
            "type": "SqlDwSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyStagingBlob"
        }
    }
}
]
```

## <a name="best-practices-when-using-polybase"></a>Bästa praxis när du använder PolyBase
Följande avsnitt innehåller ytterligare bästa praxis för att de som nämns i [Metodtips för Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Behörighet krävs
För att använda PolyBase, kräver att användaren som används för att läsa in data i SQL Data Warehouse har den [”behörighet”](https://msdn.microsoft.com/library/ms191291.aspx) i måldatabasen. Ett sätt att uppnå som är att lägga till den användaren som en medlem i rollen ”db_owner”. Lär dig hur du gör det genom att följa [i det här avsnittet](../../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limitation"></a>Ange begränsning Radstorleken och data
Polybase-inläsningar är begränsade till inläsning av rader både mindre än **1 MB** och det går inte att läsa in VARCHR(MAX), NVARCHAR(MAX) eller VARBINARY(MAX). Referera till [här](../../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Om du har källdata med rader är större än 1 MB kan du dela upp källtabellerna lodrätt i flera små där den största Radstorleken på var och en av dem inte överskrider gränsen. Mindre tabeller kan sedan att läsa in med PolyBase och sammanfogas tillsammans i Azure SQL Data Warehouse.

### <a name="sql-data-warehouse-resource-class"></a>SQL Data Warehouse resursklass
Överväg att tilldela större resursklass till användaren som används för att läsa in data i SQL Data Warehouse via PolyBase för att uppnå bästa möjliga genomflöde. Lär dig hur du gör det genom att följa [ändra en klass för användaren resursexempel](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).

### <a name="tablename-in-azure-sql-data-warehouse"></a>Tabellnamn i Azure SQL Data Warehouse
I följande tabell innehåller exempel på hur du anger den **tableName** egenskapen i datamängden JSON för olika kombinationer av schema och tabellnamn.

| DB-Schema | Tabellnamn | tableName JSON-egenskap |
| --- | --- | --- |
| dbo |MyTable |MyTable eller dbo. MyTable eller [dbo]. [Tabell] |
| dbo1 |MyTable |dbo1. MyTable eller [dbo1]. [Tabell] |
| dbo |My.Table |[My.Table] eller [dbo]. [My.Table] |
| dbo1 |My.Table |[dbo1]. [My.Table] |

Om du ser följande fel kan bero det på ett problem med det värde du angav för egenskapen tableName. Se tabellen för det korrekta sättet att ange värden för egenskapen tableName JSON.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Kolumner med standardvärden
PolyBase-funktionen i Data Factory accepterar för närvarande endast samma antal kolumner som i måltabellen. Anta att att du har en tabell med fyra kolumner och en av dem har definierats med ett standardvärde. Indata ska fortfarande innehålla fyra kolumner. Att tillhandahålla en indatauppsättning 3 kolumner skulle returneras ett fel som liknar följande meddelande:

```
All columns of the table must be specified in the INSERT BULK statement.
```
NULL-värde är en särskild form av standardvärdet. Om kolumnen är nullbar indata (i blob) för kolumnen vara tom (kan inte vara saknas från den inkommande datauppsättningen). PolyBase infogar NULL för dem i Azure SQL Data Warehouse.

## <a name="auto-table-creation"></a>Skapa en automatisk tabell
Om du använder guiden Kopiera för att kopiera data från SQL Server eller Azure SQL Database till Azure SQL Data Warehouse och tabellen som motsvarar källtabellen inte finns i målarkiv, kan Data Factory automatiskt skapa tabellen i informationslagret genom u registrerar källan tabellschemat.

Data Factory skapar tabellen i målarkiv med samma tabellnamnet på källdatalagret. Datatyperna för kolumnerna är valt baserat på följande typmappningen. Om det behövs utför typkonverteringar för att åtgärda alla inkompatibiliteter mellan källa och mål. Dessutom används resursallokering tabelldistribution.

| Kolumntyp för käll-SQL-databas | Måltypen SQL DW kolumnen (storleksbegränsning) |
| --- | --- |
| Int | Int |
| BigInt | BigInt |
| SmallInt | SmallInt |
| TinyInt | TinyInt |
| Bit | Bit |
| Decimal | Decimal |
| Numerisk | Decimal |
| Flyttal | Flyttal |
| pengar | pengar |
| Real | Real |
| SmallMoney | SmallMoney |
| Binär | Binär |
| varbinary | Varbinary (upp till 8000) |
| Date | Date |
| DateTime | DateTime |
| DateTime2 | DateTime2 |
| Tid | Tid |
| DateTimeOffset | DateTimeOffset |
| SmallDateTime | SmallDateTime |
| Text | Varchar (upp till 8000) |
| NText | NVarChar (upp till 4 000) |
| Bild | VarBinary (upp till 8000) |
| UniqueIdentifier | UniqueIdentifier |
| Char | Char |
| NChar | NChar |
| VarChar | VarChar (upp till 8000) |
| NVarChar | NVarChar (upp till 4 000) |
| Xml | Varchar (upp till 8000) |

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../../includes/data-factory-type-repeatability-for-sql-sources.md)]

## <a name="type-mapping-for-azure-sql-data-warehouse"></a>Mappning för Azure SQL Data Warehouse
Som vi nämnde i den [dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikeln kopieringsaktiviteten utför automatisk konverteringar från typer av datakällor till mottagare typer med följande metod i steg 2:

1. Konvertera från interna källtyper till .NET-typ
2. Konvertera från .NET-typ till interna mottagare

När du flyttar data till och från Azure SQL Data Warehouse, används följande mappningar från SQL-typ till .NET-typ och vice versa.

Mappningen är samma som den [Datatypsmappningen i SQL Server för ADO.NET](https://msdn.microsoft.com/library/cc716729.aspx).

| SQL Server Database Engine-typ | .NET framework-typ |
| --- | --- |
| bigint |Int64 |
| binär |Byte] |
| bitars |Boolesk |
| Char |Sträng, Char] |
| datum |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| DateTimeOffset |DateTimeOffset |
| decimaltal |decimaltal |
| FILESTREAM-attributet (varbinary(max)) |Byte] |
| Flyttal |Double-värde |
| image |Byte] |
| int |Int32 |
| pengar |decimaltal |
| nchar |Sträng, Char] |
| ntext |Sträng, Char] |
| numeriskt |decimaltal |
| nvarchar |Sträng, Char] |
| verkliga |Enkel |
| ROWVERSION |Byte] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |decimaltal |
| sql_variant |Objektet * |
| text |Sträng, Char] |
| time |Tidsintervall |
| tidsstämpel |Byte] |
| tinyint |Byte |
| uniqueidentifier |GUID |
| varbinary |Byte] |
| varchar |Sträng, Char] |
| xml |Xml |

Du kan också mappa kolumner från datauppsättningen för källan till kolumner från en datauppsättning för mottagare i aktivitetsdefinitionen kopia. Mer information finns i [mappning av kolumner för datauppsättningar i Azure Data Factory](data-factory-map-columns.md).

## <a name="json-examples-for-copying-data-to-and-from-sql-data-warehouse"></a>JSON-exempel för att kopiera data till och från SQL Data Warehouse
I följande exempel får exempel JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data till och från Azure SQL Data Warehouse och Azure Blob Storage. Men du kan kopiera data **direkt** från källor till någon av de mottagare som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) använda Kopieringsaktivitet i Azure Data Factory.

### <a name="example-copy-data-from-azure-sql-data-warehouse-to-azure-blob"></a>Exempel: Kopiera data från Azure SQL Data Warehouse till Azure Blob
Exemplet definierar följande Data Factory-entiteter:

1. En länkad tjänst av typen [AzureSqlDW](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Indata [datauppsättning](data-factory-create-datasets.md) av typen [AzureSqlDWTable](#dataset-properties).
4. Utdata [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med en Kopieringsaktivitet som använder [SqlDWSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar time series-data (per timme, dagligen, o.s.v.) från en tabell i Azure SQL Data Warehouse-databas till en blob varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

**Azure SQL Data Warehouse länkad tjänst:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Azure Blob storage-länkade tjänst:**

```JSON
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
**Indatauppsättning för Azure SQL Data Warehouse:**

Exemplet förutsätter att du har skapat en tabell ”MyTable” i Azure SQL Data Warehouse och innehåller en kolumn med namnet ”timestampcolumn” för time series-data.

Ange ”external”: ”true” informerar Data Factory-tjänsten att datauppsättningen är extern till datafabriken och inte kommer från en aktivitet i data factory.

```JSON
{
  "name": "AzureSqlDWInput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
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
**Utdatauppsättning för Azure Blob:**

Data skrivs till en ny blob varje timme (frequency: timme, intervall: 1). Sökvägen till mappen för bloben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Sökvägen till mappen använder år, månad, dag och timmar delar av starttiden.

```JSON
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

**Kopieringsaktivitet i en pipeline med SqlDWSource och BlobSink:**

Pipelinen innehåller en Kopieringsaktivitet som har konfigurerats för användning av in- och utdatauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen i **källa** är **SqlDWSource** och **mottagare** är **BlobSink**. SQL-frågan som angetts för den **SqlReaderQuery** egenskapen väljer vilka data under den senaste timmen att kopiera.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLDWtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSqlDWInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlDWSource",
            "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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
> [!NOTE]
> I det här exemplet **sqlReaderQuery** har angetts för SqlDWSource. Kopieringsaktivitet körs den här frågan mot Azure SQL Data Warehouse-källa för att hämta data.
>
> Du kan också ange en lagrad procedur genom att ange den **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren tar parametrar).
>
> Om du inte anger sqlReaderQuery eller sqlReaderStoredProcedureName, används de kolumner som definierats i avsnittet strukturen i datauppsättnings-JSON för att skapa en fråga (Välj kolumn1, kolumn2 från mytable) för att köra mot Azure SQL Data Warehouse. Om definitionen för datauppsättningen inte har strukturen, markeras alla kolumner från tabellen.
>
>

### <a name="example-copy-data-from-azure-blob-to-azure-sql-data-warehouse"></a>Exempel: Kopiera data från Azure Blob till Azure SQL Data Warehouse
Exemplet definierar följande Data Factory-entiteter:

1. En länkad tjänst av typen [AzureSqlDW](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Indata [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Utdata [datauppsättning](data-factory-create-datasets.md) av typen [AzureSqlDWTable](#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med en Kopieringsaktivitet som använder [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) och [SqlDWSink](#copy-activity-properties).

Exempel-kopior time series-data (varje timme, varje dag, osv.) från Azure-blobb till en tabell i Azure SQL Data Warehouse-databas varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

**Azure SQL Data Warehouse länkad tjänst:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Azure Blob storage-länkade tjänst:**

```JSON
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
**Indatauppsättning för Azure-Blobb:**

Data hämtas från en ny blob varje timme (frequency: timme, intervall: 1). Mappnamn för sökvägen och filnamnet för bloben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Sökvägen till mappen använder år, månad och dag för starttiden och filnamnet använder timme en del av starttiden. ”external”: ”true” inställningen informerar Data Factory-tjänsten att den här tabellen är extern till datafabriken och inte kommer från en aktivitet i data factory.

```JSON
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
**Utdatauppsättning för Azure SQL Data Warehouse:**

Exemplet kopierar data till en tabell med namnet ”MyTable” i Azure SQL Data Warehouse. Skapa tabell i Azure SQL Data Warehouse med samma antal kolumner som du förväntar dig att Blob CSV-filen ska innehålla. Nya rader har lagts till i tabell varje timme.

```JSON
{
  "name": "AzureSqlDWOutput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
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
**Kopieringsaktivitet i en pipeline med BlobSource och SqlDWSink:**

Pipelinen innehåller en Kopieringsaktivitet som har konfigurerats för användning av in- och utdatauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen i **källa** är **BlobSource** och **mottagare** är **SqlDWSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQLDW",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlDWOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
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
En genomgång finns i [läsa in 1 TB i Azure SQL Data Warehouse under 15 minuter med Azure Data Factory](data-factory-load-sql-data-warehouse.md) och [Läs in data med Azure Data Factory](../../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) artikeln i Azure SQL Data Warehouse-dokumentationen.

## <a name="performance-and-tuning"></a>Prestanda- och justering
Se [kopiera aktivitet prestanda- och Justeringsguide](data-factory-copy-activity-performance.md) att lära dig om viktiga faktorer att påverka prestandan för dataförflyttning (Kopieringsaktiviteten) i Azure Data Factory och olika sätt att optimera den.
