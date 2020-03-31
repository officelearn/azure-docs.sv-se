---
title: Kopiera data till/från Azure SQL Data Warehouse
description: Lär dig hur du kopierar data till/från Azure SQL Data Warehouse med Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: d90fa9bd-4b79-458a-8d40-e896835cfd4a
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 2df49e65603573e4a3adcdda0635982252e70b18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80130824"
---
# <a name="copy-data-to-and-from-azure-sql-data-warehouse-using-azure-data-factory"></a>Kopiera data till och från Azure SQL Data Warehouse med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-azure-sql-data-warehouse-connector.md)
> * [Version 2 (aktuell version)](../connector-azure-sql-data-warehouse.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [Azure SQL Data Warehouse-anslutningsappen i V2](../connector-azure-sql-data-warehouse.md).

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att flytta data till/från Azure SQL Data Warehouse. Den bygger på [datarörelseaktiviteter](data-factory-data-movement-activities.md) artikeln, som presenterar en allmän översikt över data förflyttning med kopian aktivitet.

> [!TIP]
> För att uppnå bästa prestanda, använd PolyBase för att läsa in data i Azure SQL Data Warehouse. Avsnittet [Använd PolyBase för att läsa in data i Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) innehåller information. En genomgång med ett användningsfall finns [i Ladda 1 TB till Azure SQL Data Warehouse under 15 minuter med Azure Data Factory](data-factory-load-sql-data-warehouse.md).

## <a name="supported-scenarios"></a>Scenarier som stöds
Du kan kopiera data **från Azure SQL Data Warehouse** till följande datalager:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Du kan kopiera data från följande datalager **till Azure SQL Data Warehouse:**

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!TIP]
> När data kopieras från SQL Server eller Azure SQL Database till Azure SQL Data Warehouse, om tabellen inte finns i målarkivet, kan Data Factory automatiskt skapa tabellen i SQL Data Warehouse med hjälp av schemat för tabellen i källdataarkivet. Mer information [finns i Skapa tabell](#auto-table-creation) automatiskt.

## <a name="supported-authentication-type"></a>Autentiseringstyp som stöds
Azure SQL Data Warehouse-anslutningsapp stöder grundläggande autentisering.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopieringsaktivitet som flyttar data till/från ett Azure SQL Data Warehouse med hjälp av olika verktyg/API:er.

Det enklaste sättet att skapa en pipeline som kopierar data till/från Azure SQL Data Warehouse är att använda guiden Kopiera data. Se [självstudiekurs: Läs in data i SQL Data Warehouse med Data Factory](../../sql-data-warehouse/sql-data-warehouse-load-with-data-factory.md) för en snabb genomgång när du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-mall,** **.NET API**och REST **API**. Se [Kopiera aktivitetshandledning](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för steg-för-steg-instruktioner för att skapa en pipeline med en kopieringsaktivitet.

Oavsett om du använder verktygen eller API:erna utför du följande steg för att skapa en pipeline som flyttar data från ett källdatalager till ett sink-datalager:

1. Skapa en **datafabrik**. En datafabrik kan innehålla en eller flera pipelines. 
2. Skapa **länkade tjänster** för att länka in- och utdatalager till datafabriken. Om du till exempel kopierar data från en Azure-bloblagring till ett Azure SQL-datalager skapar du två länkade tjänster för att länka ditt Azure-lagringskonto och Azure SQL-datalager till din datafabrik. För länkade tjänstegenskaper som är specifika för Azure SQL Data Warehouse finns i avsnittet [länkade tjänstegenskaper.](#linked-service-properties) 
3. Skapa **datauppsättningar** för att representera in- och utdata för kopieringen. I exemplet som nämns i det sista steget skapar du en datauppsättning för att ange blob-behållaren och mappen som innehåller indata. Och du skapar en annan datauppsättning för att ange tabellen i Azure SQL-informationslagret som innehåller data som kopierats från blob-lagringen. För datauppsättningsegenskaper som är specifika för Azure SQL Data Warehouse finns i avsnittet [egenskaper för datauppsättning.](#dataset-properties)
4. Skapa en **pipeline** med en kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata. I det tidigare exemplet använder du BlobSource som källa och SqlDWSink som en mottagare för kopieringsaktiviteten. Om du kopierar från Azure SQL Data Warehouse till Azure Blob Storage använder du sqldwsource och blobSink i kopieringsaktiviteten. För kopieringsaktivitetsegenskaper som är specifika för Azure SQL Data Warehouse finns i [avsnittet kopiera aktivitetsegenskaper.](#copy-activity-properties) Om du vill ha information om hur du använder ett datalager som källa eller en diskbänk klickar du på länken i föregående avsnitt för datalagret.

När du använder guiden skapas JSON-definitioner för dessa datafabrikentiteter (länkade tjänster, datauppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API:er (förutom .NET API) definierar du dessa datafabrikentiteter med hjälp av JSON-formatet. För exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data till/från ett Azure SQL Data Warehouse finns i avsnittet [JSON-exempel](#json-examples-for-copying-data-to-and-from-sql-data-warehouse) i den här artikeln.

I följande avsnitt finns information om JSON-egenskaper som används för att definiera Data Factory-entiteter som är specifika för Azure SQL Data Warehouse:

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
Följande tabell innehåller en beskrivning av JSON-element som är specifika för Azure SQL Data Warehouse-länkade tjänst.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Typegenskapen måste anges till: **AzureSqlDW** |Ja |
| Connectionstring |Ange information som behövs för att ansluta till Azure SQL Data Warehouse-instansen för egenskapen connectionString. Endast grundläggande autentisering stöds. |Ja |

> [!IMPORTANT]
> Konfigurera [Azure SQL Database Firewall](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) och databasservern så att Azure Services kan komma åt [servern](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Om du kopierar data till Azure SQL Data Warehouse utanför Azure, inklusive från lokala datakällor med datafabriksgateway, konfigurerar du lämpligt IP-adressintervall för den dator som skickar data till Azure SQL Data Warehouse.

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Skapa datauppsättningar.](data-factory-create-datasets.md) Avsnitt som struktur, tillgänglighet och princip för en datauppsättning JSON är liknande för alla datauppsättningstyper (Azure SQL, Azure blob, Azure-tabell osv.).

Avsnittet typeProperties är olika för varje typ av datauppsättning och ger information om platsen för data i datalagret. Avsnittet **typeProperties** för datauppsättningen av typen **AzureSqlDWTable** har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namn på tabellen eller vyn i Azure SQL Data Warehouse-databasen som den länkade tjänsten refererar till. |Ja |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Skapa pipelines.](data-factory-create-pipelines.md) Egenskaper som namn, beskrivning, indata- och utdatatabeller och princip är tillgängliga för alla typer av aktiviteter.

> [!NOTE]
> Kopieringsaktiviteten tar bara en indata och producerar bara en utdata.

Medan egenskaper som är tillgängliga i avsnittet typeProperties i aktiviteten varierar med varje aktivitetstyp. För kopieringsaktivitet varierar de beroende på vilka typer av källor och sänkor som finns.

### <a name="sqldwsource"></a>SqlDWSource
När källan är av typen **SqlDWSource**är följande egenskaper tillgängliga i **avsnittet typeProperties:**

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| sqlReaderQuery |Använd den anpassade frågan för att läsa data. |SQL-frågesträng. Till exempel: välj * från MyTable. |Inga |
| sqlReaderStoredProcedureName |Namnet på den lagrade proceduren som läser data från källtabellen. |Namnet på den lagrade proceduren. Det senaste SQL-uttrycket måste vara en SELECT-sats i den lagrade proceduren. |Inga |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värdepar. Namn och hölje av parametrar måste stämma överens med namn och hölje för de lagrade procedureparametrarna. |Inga |

Om **sqlReaderQuery** har angetts för SqlDWSource körs den här frågan mot Azure SQL Data Warehouse-källan för att hämta data.

Du kan också ange en lagrad procedur genom att ange **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren tar parametrar).

Om du inte anger antingen sqlReaderQuery eller sqlReaderStoredProcedureName används kolumnerna som definieras i strukturavsnittet i datauppsättningen JSON för att skapa en fråga som ska köras mot Azure SQL Data Warehouse. Exempel: `select column1, column2 from mytable`. Om datauppsättningsdefinitionen inte har strukturen markeras alla kolumner i tabellen.

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
**Den lagrade procedurens definition:**

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

### <a name="sqldwsink"></a>SqlDWSink (SqlDWSink)
**SqlDWSink** stöder följande egenskaper:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Ange en fråga för Kopieringsaktivitet som ska köras så att data för ett visst segment rensas. Mer information finns i [avsnittet repeterbarhet](#repeatability-during-copy). |En frågesats. |Inga |
| allowPolyBase |Anger om PolyBase (i förekommande fall) ska användas i stället för BULKINSERT-mekanism. <br/><br/> **Att använda PolyBase är det rekommenderade sättet att läsa in data i SQL Data Warehouse.** Se [Använda PolyBase för att läsa in data i avsnittet Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) för begränsningar och information. |True <br/>False (standard) |Inga |
| polyBaseSettings polyBaseSettings polyBaseSettings polyBase |En grupp egenskaper som kan anges när egenskapen **allowPolybase** är inställd på **true**. |&nbsp; |Inga |
| avvisaVärde |Anger antalet eller procentandelen rader som kan avvisas innan frågan misslyckas. <br/><br/>Läs mer om PolyBases avvisningsalternativ i avsnittet **Argument** i avsnittet [SKAPA EXTERN TABELL (Transact-SQL).](https://msdn.microsoft.com/library/dn935021.aspx) |0 (standard), 1, 2, ... |Inga |
| rejectType (avskände) |Anger om alternativet rejectValue anges som ett litteralt värde eller en procentsats. |Värde (standard), Procent |Inga |
| avvisaPlevalue |Bestämmer antalet rader som ska hämtas innan PolyBase beräknar om procentandelen avvisade rader. |1, 2, ... |Ja, om **rejectType** är **procent** |
| useTypeDefault |Anger hur värden som saknas i avgränsade textfiler ska hanteras när PolyBase hämtar data från textfilen.<br/><br/>Läs mer om den här egenskapen från avsnittet Argument i [SKAPA EXTERNT FILFORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |Sant, Falskt (standard) |Inga |
| skriverBatchSize |Infogar data i SQL-tabellen när buffertstorleken når writeBatchSize |Heltal (antal rader) |Nej (standard: 10000) |
| skriverBatchTimeout |Vänta på att batchinsatsen ska slutföras innan den har klarats upp. |Gått<br/><br/> Exempel: "00:30:00" (30 minuter). |Inga |

#### <a name="sqldwsink-example"></a>SqlDWSink exempel

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Använd PolyBase för att läsa in data i Azure SQL Data Warehouse
Att använda **[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)** är ett effektivt sätt att läsa in stora mängder data i Azure SQL Data Warehouse med högt dataflöde. Du kan se en stor vinst i dataflödet med hjälp av PolyBase i stället för standard BULKINSERT-mekanismen. Se [referensnummer för prestanda](data-factory-copy-activity-performance.md#performance-reference) med detaljerad jämförelse. En genomgång med ett användningsfall finns [i Ladda 1 TB till Azure SQL Data Warehouse under 15 minuter med Azure Data Factory](data-factory-load-sql-data-warehouse.md).

* Om källdata finns i **Azure Blob eller Azure Data Lake Store**och formatet är kompatibelt med PolyBase kan du kopiera direkt till Azure SQL Data Warehouse med PolyBase. Se **[Direktkopiering med PolyBase](#direct-copy-using-polybase)** med information.
* Om källdatalagringen och formatet inte stöds ursprungligen av PolyBase kan du använda funktionen **[Mellanlagra kopia med PolyBase](#staged-copy-using-polybase)** i stället. Det ger dig också bättre dataflöde genom att automatiskt konvertera data till PolyBase-kompatibelt format och lagra data i Azure Blob-lagring. Den läser sedan in data i SQL Data Warehouse.

Ange `allowPolyBase` egenskapen till **true** som visas i följande exempel för Azure Data Factory för att använda PolyBase för att kopiera data till Azure SQL Data Warehouse. När du anger att allowPolyBase är true kan `polyBaseSettings` du ange PolyBase-specifika egenskaper med hjälp av egenskapsgruppen. Se [avsnittet SqlDWSink](#sqldwsink) för mer information om egenskaper som du kan använda med polyBaseSettings.

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

### <a name="direct-copy-using-polybase"></a>Direktkopiering med PolyBase
SQL Data Warehouse PolyBase stöder direkt Azure Blob och Azure Data Lake Store (med tjänstens huvudnamn) som källa och med specifika filformatskrav. Om källdata uppfyller de kriterier som beskrivs i det här avsnittet kan du kopiera direkt från källdatalagring till Azure SQL Data Warehouse med PolyBase. Annars kan du använda [Stegvis kopia med PolyBase](#staged-copy-using-polybase).

> [!TIP]
> Om du vill kopiera data från Data Lake Store till SQL Data Warehouse effektivt gör du mer från [Azure Data Factory ännu enklare och bekvämt att upptäcka insikter från data när du använder Data Lake Store med SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Om kraven inte uppfylls kontrollerar Azure Data Factory inställningarna och faller automatiskt tillbaka till BULKINSERT-mekanismen för dataflyttning.

1. **Källlänkade tjänsten** är av typen: **AzureStorage** eller **AzureDataLakeStore med autentisering av tjänstens huvudnamn**.
2. **Indatauppsättningen** är av typen: **AzureBlob** eller **AzureDataLakeStore** `type` och formattypen under egenskaper är **OrcFormat,** **ParquetFormat**eller **TextFormat** med följande konfigurationer:

   1. `rowDelimiter`måste vara **\n**.
   2. `nullValue`är inställd på **tom** sträng `treatEmptyAsNull` (""), eller är inställd på **true**.
   3. `encodingName`är inställd på **utf-8**, vilket är **standardvärde.**
   4. `escapeChar`, `quoteChar` `firstRowAsHeader`och `skipLineCount` har inte angetts.
   5. `compression`kan inte vara **någon komprimering,** **GZip**eller **Deflate**.

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

3. Det finns `skipHeaderLineCount` ingen inställning under **BlobSource** eller **AzureDataLakeStore** för kopieringsaktiviteten i pipelinen.
4. Det finns `sliceIdentifierColumnName` ingen inställning under **SqlDWSink** för kopieringsaktiviteten på pipelinen. (PolyBase garanterar att alla data uppdateras eller att ingenting uppdateras i en enda körning. För att uppnå **repeterbarhet**kan du använda). `sqlWriterCleanupScript`
5. Det går `columnMapping` inte att använda i den associerade i kopieringsaktiviteten.

### <a name="staged-copy-using-polybase"></a>Iscensatt kopia med PolyBase
När källdata inte uppfyller de kriterier som introducerades i föregående avsnitt kan du aktivera kopiering av data via en tillfällig mellanlagring av Azure Blob Storage (kan inte vara Premium Storage). I det här fallet utför Azure Data Factory automatiskt omvandlingar på data för att uppfylla dataformatkraven för PolyBase, sedan använda PolyBase för att läsa in data i SQL Data Warehouse och slutligen rensa dina temp-data från Blob-lagringen. Se [Stegvis kopia](data-factory-copy-activity-performance.md#staged-copy) för mer information om hur kopiering av data via en mellanlagring Av Azure Blob fungerar i allmänhet.

> [!NOTE]
> När du kopierar data från ett lokalt datalager till Azure SQL Data Warehouse med PolyBase och mellanlagring krävs om din Data Management Gateway-version är under 2.4, JRE (Java Runtime Environment) på din gateway-dator som används för att omvandla källan data till rätt format. Föreslår att du uppgraderar din gateway till det senaste för att undvika sådant beroende.
>

Om du vill använda den här funktionen skapar du en [Azure Storage-länkad tjänst](data-factory-azure-blob-connector.md#azure-storage-linked-service) som `enableStaging` `stagingSettings` refererar till Azure Storage-kontot som har interimsblobblagringen och anger sedan egenskaperna för kopieringsaktiviteten enligt följande kod:

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

## <a name="best-practices-when-using-polybase"></a>Metodtips när du använder PolyBase
Följande avsnitt innehåller ytterligare metodtips för de som nämns i [metodtips för Azure SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Obligatorisk databasbehörighet
För att använda PolyBase kräver det att användaren som används för att läsa in data i SQL Data Warehouse har [behörigheten "CONTROL"](https://msdn.microsoft.com/library/ms191291.aspx) på måldatabasen. Ett sätt att uppnå detta är att lägga till den användaren som medlem i "db_owner"-rollen. Läs om hur du gör det genom att följa [det här avsnittet](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limitation"></a>Begränsning av radstorlek och datatyp
Polybasbelastningar är begränsade till att ladda rader som är både mindre än **1 MB** och kan inte läsas in till VARCHR(MAX), NVARCHAR(MAX) eller VARBINARY(MAX). Se [här](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Om du har källdata med rader med en storlek som är större än 1 MB kanske du vill dela upp källtabellerna lodrätt i flera små där den största radstorleken för var och en av dem inte överskrider gränsen. De mindre tabellerna kan sedan läsas in med PolyBase och slås samman i Azure SQL Data Warehouse.

### <a name="sql-data-warehouse-resource-class"></a>Resursklass för SQL Data Warehouse
För att uppnå bästa möjliga dataflöde, överväga att tilldela större resursklass till den användare som används för att läsa in data i SQL Data Warehouse via PolyBase. Lär dig hur du gör det genom att följa [Ändra ett exempel på användarresursklass](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).

### <a name="tablename-in-azure-sql-data-warehouse"></a>tableName i Azure SQL Data Warehouse
I följande tabell finns exempel på hur du anger egenskapen **tableName** i datauppsättningen JSON för olika kombinationer av schema och tabellnamn.

| DB-schema | Tabellnamn | tableName JSON, egenskapen tableName JSON |
| --- | --- | --- |
| Dbo |MyTable (MyTable) |MyTable eller dbo. MyTable eller [dbo]. [MyTable] |
| dbo1 (på andra) |MyTable (MyTable) |dbo1. MyTable eller [dbo1]. [MyTable] |
| Dbo |My.Table (bord) |[My.Table] eller [dbo]. [Mitt.Bord] |
| dbo1 (på andra) |My.Table (bord) |[dbo1]. [Mitt.Bord] |

Om följande fel visas kan det vara ett problem med det värde som du har angett för egenskapen tableName. Se tabellen för rätt sätt att ange värden för egenskapen tableName JSON.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Kolumner med standardvärden
För närvarande accepterar PolyBase-funktionen i Data Factory bara samma antal kolumner som i måltabellen. Du har en tabell med fyra kolumner och en av dem definieras med ett standardvärde. Indata bör fortfarande innehålla fyra kolumner. Om du tillhandahåller en indatauppsättning med indata med tre kolumner skulle det ge ett fel som liknar följande meddelande:

```
All columns of the table must be specified in the INSERT BULK statement.
```
NULL-värde är en särskild form av standardvärde. Om kolumnen är nullable kan indata (i blob) för den kolumnen vara tomma (kan inte saknas från indatauppsättningen). PolyBase infogar NULL för dem i Azure SQL Data Warehouse.

## <a name="auto-table-creation"></a>Skapa automatisk tabell
Om du använder kopieringsguiden för att kopiera data från SQL Server eller Azure SQL Database till Azure SQL Data Warehouse och tabellen som motsvarar källtabellen inte finns i målarkivet, kan Data Factory automatiskt skapa tabellen i informationslagret genom att med hjälp av källtabellschemat.

Data Factory skapar tabellen i målarkivet med samma tabellnamn i källdatalagret. Datatyperna för kolumner väljs baserat på följande typmappning. Om det behövs utförs typkonverteringar för att åtgärda eventuella inkompatibiliteter mellan käll- och målbutiker. Den använder också Round Robin tabell distribution.

| Kolumntyp för KÄLLA SQL Database | Mål-KOLUMNTYP FÖR SQL DW (storleksbegränsning) |
| --- | --- |
| Int | Int |
| Bigint | Bigint |
| Smallint | Smallint |
| Tinyint | Tinyint |
| Bitars | Bitars |
| Decimal | Decimal |
| Numerisk | Decimal |
| Float (Flyttal) | Float (Flyttal) |
| Money (Pengar) | Money (Pengar) |
| Verkliga | Verkliga |
| SmallMoney (småpengar) | SmallMoney (småpengar) |
| Binär | Binär |
| Varbinary | Varbinary (upp till 8000) |
| Datum | Datum |
| DateTime | DateTime |
| DateTime2 | DateTime2 |
| Tid | Tid |
| DateTimeOffset | DateTimeOffset |
| SmallDateTime | SmallDateTime |
| Text | Varchar (upp till 8000) |
| Ntext | NVarChar (upp till 4000) |
| Bild | VarBinary (upp till 8000) |
| Unikidentifierare | Unikidentifierare |
| Char | Char |
| Nchar | Nchar |
| Varchar | VarChar (upp till 8000) |
| Nvarchar | NVarChar (upp till 4000) |
| Xml | Varchar (upp till 8000) |

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../../includes/data-factory-type-repeatability-for-sql-sources.md)]

## <a name="type-mapping-for-azure-sql-data-warehouse"></a>Typmappning för Azure SQL Data Warehouse
Som nämns i artikeln [om dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) utför Copy-aktivitet automatiska typkonverteringar från källtyper till sinktyper med följande tvåstegsmetod:

1. Konvertera från ursprungliga källtyper till .NET-typ
2. Konvertera från .NET-typ till inbyggt handfattyp

När du flyttar data till & från Azure SQL Data Warehouse används följande mappningar från SQL-typ till .NET-typ och vice versa.

Mappningen är samma som [SQL Server Data Type Mapping för ADO.NET](https://msdn.microsoft.com/library/cc716729.aspx).

| Sql Server-databasmotortyp | .NET-ramtyp |
| --- | --- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |Sträng, Röding[] |
| date |DateTime |
| Datumtid |DateTime |
| datetime2 |DateTime |
| Datumtidsdatum |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM-attribut (varbinary(max)) |Byte[] |
| Float (Flyttal) |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |Sträng, Röding[] |
| ntext |Sträng, Röding[] |
| numeric |Decimal |
| nvarchar |Sträng, Röding[] |
| real |Enkel |
| Rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 (int16) |
| smallmoney |Decimal |
| Sql_variant |Objekt * |
| text |Sträng, Röding[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |GUID |
| varbinary |Byte[] |
| varchar |Sträng, Röding[] |
| xml |Xml |

Du kan också mappa kolumner från källdatauppsättning till kolumner från sink-datauppsättning i definitionen av kopieringsaktivitet. Mer information finns [i Mappa datauppsättningskolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="json-examples-for-copying-data-to-and-from-sql-data-warehouse"></a>JSON-exempel för kopiering av data till och från SQL Data Warehouse
Följande exempel innehåller exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data till och från Azure SQL Data Warehouse och Azure Blob Storage. Data kan dock kopieras **direkt** från någon av källorna till någon av de diskhoar som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av kopieringsaktiviteten i Azure Data Factory.

### <a name="example-copy-data-from-azure-sql-data-warehouse-to-azure-blob"></a>Exempel: Kopiera data från Azure SQL Data Warehouse till Azure Blob
Exemplet definierar följande datafabrikentiteter:

1. En länkad tjänst av typen [AzureSqlDW](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. En [indatauppsättning](data-factory-create-datasets.md) av typen [AzureSqlDWTable](#dataset-properties).
4. En [utdatauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med kopieringsaktivitet som använder [SqlDWSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar tidsseriedata (per timme, dag osv.) från en tabell i Azure SQL Data Warehouse-databasen till en blob varje timme. De JSON-egenskaper som används i dessa prover beskrivs i avsnitt som följer proverna.

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
**Azure Blob storage-länkad tjänst:**

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
**Azure SQL Data Warehouse indatauppsättning:**

Exemplet förutsätter att du har skapat en tabell "MyTable" i Azure SQL Data Warehouse och den innehåller en kolumn som kallas "tidsstämpelmonlumn" för tidsseriedata.

Inställningen "extern": "true" informerar datafabrikstjänsten om att datauppsättningen är extern till datafabriken och inte produceras av en aktivitet i datafabriken.

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
**Azure Blob-utdatauppsättning:**

Data skrivs till en ny blob varje timme (frekvens: timme, intervall: 1). Mappsökvägen för blobben utvärderas dynamiskt baserat på starttiden för det segment som bearbetas. Mappsökvägen använder delar av starttiden för år, månad, dag och timmar.

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

**Kopiera aktivitet i en pipeline med SqlDWSource och BlobSink:**

Pipelinen innehåller en kopieringsaktivitet som är konfigurerad för att använda in- och utdatauppsättningar och som är schemalagd att köras varje timme. I pipeline-JSON-definitionen anges **källtypen** till **SqlDWSource** och **sink-typen** är inställd på **BlobSink**. Sql-frågan som angetts för egenskapen **SqlReaderQuery** väljer de data som ska kopieras under den senaste timmen.

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
> I exemplet anges **sqlReaderQuery** för SqlDWSource. Kopieringsaktiviteten kör den här frågan mot Azure SQL Data Warehouse-källan för att hämta data.
>
> Du kan också ange en lagrad procedur genom att ange **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren tar parametrar).
>
> Om du inte anger antingen sqlReaderQuery eller sqlReaderStoredProcedureName används kolumnerna som definieras i strukturavsnittet i datauppsättningen JSON för att skapa en fråga (välj kolumn1, kolumn2 från mytable) för att köras mot Azure SQL Data Warehouse. Om datauppsättningsdefinitionen inte har strukturen markeras alla kolumner i tabellen.
>
>

### <a name="example-copy-data-from-azure-blob-to-azure-sql-data-warehouse"></a>Exempel: Kopiera data från Azure Blob till Azure SQL Data Warehouse
Exemplet definierar följande datafabrikentiteter:

1. En länkad tjänst av typen [AzureSqlDW](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. En [indatauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. En [utdatauppsättning](data-factory-create-datasets.md) av typen [AzureSqlDWTable](#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med kopieringsaktivitet som använder [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) och [SqlDWSink](#copy-activity-properties).

Exemplet kopierar tidsseriedata (varje timme, dagligen osv.) från Azure-blob till en tabell i Azure SQL Data Warehouse-databasen varje timme. De JSON-egenskaper som används i dessa prover beskrivs i avsnitt som följer proverna.

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
**Azure Blob storage-länkad tjänst:**

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
**Azure Blob-indatauppsättning:**

Data hämtas från en ny blob varje timme (frekvens: timme, intervall: 1). Mappsökvägen och filnamnet för blobben utvärderas dynamiskt baserat på starttiden för det segment som bearbetas. Mappsökvägen använder år, månad och dag del av starttiden och filnamnet använder timdelen av starttiden. "extern": "true"-inställningen informerar datafabrikstjänsten om att den här tabellen är extern till datafabriken och inte produceras av en aktivitet i datafabriken.

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
**Azure SQL Data Warehouse-utdatauppsättning:**

Exemplet kopierar data till en tabell med namnet "MyTable" i Azure SQL Data Warehouse. Skapa tabellen i Azure SQL Data Warehouse med samma antal kolumner som du förväntar dig att Blob CSV-filen ska innehålla. Nya rader läggs till i tabellen varje timme.

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
**Kopiera aktivitet i en pipeline med BlobSource och SqlDWSink:**

Pipelinen innehåller en kopieringsaktivitet som är konfigurerad för att använda in- och utdatauppsättningar och som är schemalagd att köras varje timme. I pipeline-JSON-definitionen är **källtypen** inställd på **BlobSource** och **sink-typen** är inställd på **SqlDWSink**.

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
En genomgång finns [i informations- och läsa tb läs in 1 TB i Azure SQL Data Warehouse under 15 minuter med Azure Data Factory](data-factory-load-sql-data-warehouse.md) och Läs in data med Azure Data [Factory-artikeln](../../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) i Azure SQL Data Warehouse-dokumentationen.

## <a name="performance-and-tuning"></a>Prestanda och justering
Se [Kopiera aktivitetsprestanda & justeringsguide](data-factory-copy-activity-performance.md) om du vill veta mer om viktiga faktorer som påverkar prestanda för datarörelser (kopieringsaktivitet) i Azure Data Factory och olika sätt att optimera den.
