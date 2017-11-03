---
title: "Kopiera data till/från Azure SQL Data Warehouse | Microsoft Docs"
description: "Lär dig att kopiera data till och från Azure SQL Data Warehouse med Azure Data Factory"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: d90fa9bd-4b79-458a-8d40-e896835cfd4a
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: a56afa7c5200b53b398f8a99e8a36df3685b2f66
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2017
---
# <a name="copy-data-to-and-from-azure-sql-data-warehouse-using-azure-data-factory"></a>Kopiera data till och från Azure SQL Data Warehouse med Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](data-factory-azure-sql-data-warehouse-connector.md)
> * [Version 2 – förhandsversion](../connector-azure-sql-data-warehouse.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [Azure SQL Data Warehouse-kopplingen i V2](../connector-azure-sql-data-warehouse.md).

Den här artikeln förklarar hur du använder aktiviteten kopiera i Azure Data Factory för att flytta data till och från Azure SQL Data Warehouse. Den bygger på den [Data Movement aktiviteter](data-factory-data-movement-activities.md) artikel som presenterar en allmän översikt över dataflyttning med copy-aktivitet.  

> [!TIP]
> Använd PolyBase för att uppnå bästa prestanda, att läsa in data till Azure SQL Data Warehouse. Den [Använd PolyBase för att läsa in data till Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) avsnitt innehåller information. En genomgång med ett användningsfall finns [läsa in 1 TB i Azure SQL Data Warehouse under 15 minuter med Azure Data Factory](data-factory-load-sql-data-warehouse.md).

## <a name="supported-scenarios"></a>Scenarier som stöds
Du kan kopiera data **från Azure SQL Data Warehouse** till följande data lagras:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Du kan kopiera data från följande datalager **till Azure SQL Data Warehouse**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!TIP]
> När du kopierar data från SQL Server eller Azure SQL Database till Azure SQL Data Warehouse, om tabellen inte finns i målarkivet för, kan Data Factory automatiskt skapa tabellen i SQL Data Warehouse med hjälp av schemat på tabellen i datalagret källa. Se [automatiskt skapande av tabell](#auto-table-creation) mer information.

## <a name="supported-authentication-type"></a>Autentiseringstypen som stöds
Azure SQL Data Warehouse connector stöder grundläggande autentisering.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopia-aktivitet som flyttar data till och från ett Azure SQL Data Warehouse med hjälp av olika verktyg/API: er.

Det enklaste sättet att skapa en pipeline som kopierar data till och från Azure SQL Data Warehouse är att använda guiden Kopiera data. Se [Självstudier: Läs in data till SQL Data Warehouse med Data Factory](../../sql-data-warehouse/sql-data-warehouse-load-with-data-factory.md) för en snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera data.

Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall**, **.NET API**, och **REST API**. Se [kopiera aktivitet kursen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Om du använder verktyg eller API: er, kan du utföra följande steg för att skapa en pipeline som flyttar data från ett dataarkiv som källa till ett dataarkiv som mottagare:

1. Skapa en **datafabriken**. En datafabrik kan innehålla en eller flera pipelines. 
2. Skapa **länkade tjänster** att länka inkommande och utgående data lagras till din data factory. Om du kopierar data från ett Azure blob storage till en Azure SQL data warehouse, skapa till exempel två länkade tjänster för att länka ditt Azure storage-konto och Azure SQL data warehouse till din data factory. Länkad tjänstegenskaper som är specifika för Azure SQL Data Warehouse, se [länkade tjänstegenskaper](#linked-service-properties) avsnitt. 
3. Skapa **datauppsättningar** att representera inkommande och utgående data för kopieringen. I det tidigare exemplet i det sista steget, skapar du en datauppsättning för att ange blob-behållaren och mappen som innehåller indata. Och du skapar en annan dataset för att ange tabellen i Azure SQL data warehouse som innehåller de data som kopieras från blob storage. Egenskaper för datamängd som är specifika för Azure SQL Data Warehouse, se [egenskaper för datamängd](#dataset-properties) avsnitt.
4. Skapa en **pipeline** med en kopia-aktivitet som tar en datamängd som indata och en dataset som utdata. I exemplet ovan kan använda du BlobSource som en källa och SqlDWSink som en mottagare för kopieringsaktiviteten. På samma sätt om du vill kopiera från Azure SQL Data Warehouse till Azure Blob Storage, använder du SqlDWSource och BlobSink i aktiviteten kopia. Kopiera Aktivitetsegenskaper som är specifika för Azure SQL Data Warehouse, se [kopiera Aktivitetsegenskaper](#copy-activity-properties) avsnitt. Mer information om hur du använder ett dataarkiv som en källa eller en mottagare klickar du på länken i föregående avsnitt för datalager.

När du använder guiden skapas JSON definitioner för dessa Data Factory-enheter (länkade tjänster, datauppsättningar och pipelinen) automatiskt för dig. När du använder Verktyg/API: er (utom .NET API), kan du definiera dessa Data Factory-enheter med hjälp av JSON-format.  Exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data till/från en Azure SQL Data Warehouse finns [JSON-exempel](#json-examples-for-copying-data-to-and-from-sql-data-warehouse) i den här artikeln.

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory entiteter till Azure SQL Data Warehouse:

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper
Följande tabell innehåller en beskrivning för JSON-element som är specifika för Azure SQL Data Warehouse länkad tjänst.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen type måste anges till: **AzureSqlDW** |Ja |
| connectionString |Ange information som behövs för att ansluta till Azure SQL Data Warehouse-instans för egenskapen connectionString. Endast grundläggande autentisering stöds. |Ja |

> [!IMPORTANT]
> Konfigurera [Azure SQL Database-brandvägg](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) och databasservern till [ge Azure-tjänster åtkomst till servern](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Om du kopierar data till Azure SQL Data Warehouse från utanför Azure inklusive från lokala datakällor med data factory-gateway måste du dessutom konfigurera rätt IP-adressintervall för den dator som skickar data till Azure SQL Data Warehouse.

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över egenskaper som är tillgängliga för att definiera datauppsättningarna & avsnitt finns i [skapa datauppsättningar](data-factory-create-datasets.md) artikel. Avsnitt som struktur, tillgänglighet och princip på en datamängd JSON är liknande för alla typer av dataset (Azure SQL Azure blob, Azure-tabellen, osv.).

Avsnittet typeProperties är olika för varje typ av dataset och ger information om placeringen av data i datalagret. Den **typeProperties** avsnittet för datauppsättningen av typen **AzureSqlDWTable** har följande egenskaper:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| tableName |Namnet på den tabell eller vy i Azure SQL Data Warehouse-databas som den länkade tjänsten refererar till. |Ja |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i [skapar Pipelines](data-factory-create-pipelines.md) artikel. Egenskaper som namn, beskrivning, ingående och utgående tabeller och principen är tillgängliga för alla typer av aktiviteter.

> [!NOTE]
> Kopieringsaktiviteten tar endast en inmatning och ger en enda utdata.

De egenskaper som är tillgängliga i avsnittet typeProperties i aktiviteten varierar beroende på varje aktivitetstyp. För Kopieringsaktivitet kan variera de beroende på vilka typer av datakällor och sänkor.

### <a name="sqldwsource"></a>SqlDWSource
När datakällan är av typen **SqlDWSource**, följande egenskaper är tillgängliga i **typeProperties** avsnitt:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| sqlReaderQuery |Använd anpassad fråga för att läsa data. |SQL-sträng. Till exempel: Välj * från mytable prefix. |Nej |
| sqlReaderStoredProcedureName |Namnet på den lagrade proceduren som läser data från källtabellen. |Namnet på den lagrade proceduren. Den senaste SQL-instruktionen måste vara en SELECT-instruktion i den lagrade proceduren. |Nej |
| storedProcedureParameters |Parametrar för den lagrade proceduren. |Namn/värde-par. Namn och skiftläge parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. |Nej |

Om den **sqlReaderQuery** har angetts för SqlDWSource, kopiera-aktivitet körs den här frågan mot Azure SQL Data Warehouse-källa för att hämta data.

Du kan också ange en lagrad procedur genom att ange den **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren har parametrar).

Om du inte anger sqlReaderQuery eller sqlReaderStoredProcedureName används kolumner som har definierats i avsnittet strukturen i datauppsättningen JSON för att skapa en fråga som körs mot Azure SQL Data Warehouse. Exempel: `select column1, column2 from mytable`. Om datauppsättningsdefinitionen saknar strukturen, markeras alla kolumner från tabellen.

#### <a name="sqldwsource-example"></a>SqlDWSource-exempel

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
**Den lagrade proceduren definitionen:**

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
**SqlDWSink** stöder följande egenskaper:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Ange en fråga för Kopieringsaktiviteten att köra så att data för ett visst segment har rensats bort. Mer information finns i [repeterbarhet avsnittet](#repeatability-during-copy). |En frågesats. |Nej |
| allowPolyBase |Anger om du vill använda PolyBase (i förekommande fall) i stället för BULKINSERT mekanism. <br/><br/> **Med PolyBase är det rekommenderade sättet att läsa in data till SQL Data Warehouse.** Se [Använd PolyBase för att läsa in data till Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) avsnittet begränsningar och information. |True <br/>FALSKT (standard) |Nej |
| polyBaseSettings |En grupp egenskaper som kan anges när den **allowPolybase** egenskap är inställd på **SANT**. |&nbsp; |Nej |
| rejectValue |Anger det antal eller procentandelen rader som kan avvisas innan frågan misslyckas. <br/><br/>Mer information om den PolyBase avvisa alternativ i den **argument** avsnitt i [Skapa extern tabell (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) avsnittet. |0 (standard), 1, 2... |Nej |
| rejectType |Anger om alternativet rejectValue har angetts som ett litteralvärde eller ett procentvärde. |Värdet (standard), procent |Nej |
| rejectSampleValue |Anger antalet rader som ska hämtas innan PolyBase beräknar andelen Avvisade rader. |1, 2, … |Ja, om **rejectType** är **procent** |
| useTypeDefault |Anger hur du hanterar saknade värden i avgränsade textfiler när PolyBase hämtar data från filen.<br/><br/>Mer information om denna egenskap från avsnittet argument i [skapa externt FILFORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |SANT, FALSKT (standard) |Nej |
| writeBatchSize |Infogar data i SQL-tabellen när buffertstorleken når writeBatchSize |Heltal (antalet rader) |Nej (standard: 10000) |
| writeBatchTimeout |Vänta tills batch insert-åtgärden ska slutföras innan tidsgränsen uppnås. |TimeSpan<br/><br/> Exempel ”: 00: 30:00” (30 minuter). |Nej |

#### <a name="sqldwsink-example"></a>SqlDWSink-exempel

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Använd PolyBase för att läsa in data till Azure SQL Data Warehouse
Med hjälp av  **[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)**  är ett effektivt sätt för att läsa in stora mängder data till Azure SQL Data Warehouse med hög genomströmning. Du kan se en stor vinst i genomflödet med PolyBase i stället för BULKINSERT standardmekanism. Se [kopiera prestanda referensnummer](data-factory-copy-activity-performance.md#performance-reference) med detaljerad jämförelse. En genomgång med ett användningsfall finns [läsa in 1 TB i Azure SQL Data Warehouse under 15 minuter med Azure Data Factory](data-factory-load-sql-data-warehouse.md).

* Om datakällan finns i **Azure Blob- eller Azure Data Lake Store**, och formatet som är kompatibelt med PolyBase, du kan kopiera direkt till Azure SQL Data Warehouse med PolyBase. Se  **[direkt kopia med PolyBase](#direct-copy-using-polybase)**  med information.
* Om din käll-datalagret och format inte stöds ursprungligen av PolyBase, kan du använda den  **[mellanlagrad kopia med PolyBase](#staged-copy-using-polybase)**  funktion i stället. Det ger dig bättre genomströmning genom att automatiskt konvertera data till PolyBase-kompatibelt format och lagra data i Azure Blob storage. Data hämtas sedan till SQL Data Warehouse.

Ange den `allowPolyBase` egenskapen **SANT** som visas i följande exempel för Azure Data Factory för att använda PolyBase för att kopiera data till Azure SQL Data Warehouse. När du anger allowPolyBase till true anger du PolyBase specifika egenskaper med hjälp av den `polyBaseSettings` egenskapsgrupp. finns det [SqlDWSink](#SqlDWSink) finns mer information om egenskaper som du kan använda med polyBaseSettings.

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

### <a name="direct-copy-using-polybase"></a>Direkt kopia med PolyBase
SQL Data Warehouse PolyBase stöd direkt för Azure Blob och Azure Data Lake Store (med tjänstens huvudnamn) som källa och krav för fil-format. Om dina källdata uppfyller de kriterier som beskrivs i det här avsnittet, kopiera du direkt från datalagret för källan till Azure SQL Data Warehouse med PolyBase. Annars kan du använda [mellanlagrad kopia med PolyBase](#staged-copy-using-polybase).

> [!TIP]
> Om du vill kopiera data från Data Lake Store till SQL Data Warehouse effektivt, mer information från [Azure Data Factory gör det även enklare och praktiskt att få insikter från data när du använder Data Lake Store med SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Om kraven inte uppfylls, kontrollerar du inställningarna för Azure Data Factory och faller automatiskt tillbaka till BULKINSERT mekanism för flytt av data.

1. **Källan länkade tjänsten** är av typen: **AzureStorage** eller **AzureDataLakeStore med huvudsakliga autentiseringen av tjänsten**.  
2. Den **inkommande dataset** är av typen: **AzureBlob** eller **AzureDataLakeStore**, och skriv `type` egenskaper är **OrcFormat**, eller **TextFormat** med följande konfigurationer:

   1. `rowDelimiter`måste vara  **\n** .
   2. `nullValue`anges till **tom sträng** (””), eller `treatEmptyAsNull` är inställd på **SANT**.
   3. `encodingName`anges till **utf-8**, vilket är **standard** värde.
   4. `escapeChar`, `quoteChar`, `firstRowAsHeader`, och `skipLineCount` har inte angetts.
   5. `compression`kan vara **ingen komprimering**, **GZip**, eller **Deflate**.

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

3. Det finns inga `skipHeaderLineCount` inställningen **BlobSource** eller **AzureDataLakeStore** för aktiviteten kopiera i pipelinen.
4. Det finns inga `sliceIdentifierColumnName` inställningen **SqlDWSink** för aktiviteten kopiera i pipelinen. (PolyBase garanterar att alla data har uppdaterats eller ingenting uppdateras i en enda körning. Att uppnå **repeterbarhet**, du kan använda `sqlWriterCleanupScript`).
5. Det finns ingen `columnMapping` som används i den associerade i kopian aktivitet.

### <a name="staged-copy-using-polybase"></a>Stegvis kopia med PolyBase
När källdata inte uppfyller de kriterier som introducerades i föregående avsnitt, kan du Aktivera kopiering av data via en mellanliggande fristående Azure Blob Storage (inte får vara Premium-lagring). I det här fallet utför Azure Data Factory automatiskt omvandlingar på data som ska uppfylla kraven för data-format i PolyBase och sedan använda PolyBase för att läsa in data till SQL Data Warehouse, och på sista Rensa temp data från Blob storage. Se [mellanlagrad kopiera](data-factory-copy-activity-performance.md#staged-copy) för information om hur kopiering av data via en fristående Azure-Blob fungerar i allmänhet.

> [!NOTE]
> När kopiering av data från en lokal lagring till Azure SQL Data Warehouse med PolyBase och mellanlagring, om Data Management Gateway-version som är lägre än 2.4 JRE (Java Runtime Environment) krävs på gateway-datorn som används för att omvandla dina källdata i rätt format. Rekommenderar att du uppgraderar din gateway till den senaste versionen om du vill undvika detta beroende.
>

Om du vill använda den här funktionen kan du skapa en [Azure länkade lagringstjänsten](data-factory-azure-blob-connector.md#azure-storage-linked-service) som refererar till Azure Storage-konto som har tillfälligt blob-lagring, ange den `enableStaging` och `stagingSettings` egenskaper för aktiviteten kopiera enligt följande kod:

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

## <a name="best-practices-when-using-polybase"></a>Rekommenderade metoder när du använder PolyBase
Följande avsnitt innehåller ytterligare bästa praxis för de som nämns i [Metodtips för Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Behörighet krävs
Om du vill använda PolyBase kräver att användaren som används för att läsa in data till SQL Data Warehouse har den [”behörighet”](https://msdn.microsoft.com/library/ms191291.aspx) på måldatabasen. Ett sätt att uppnå som är att lägga till användaren som en medlem i ”db_owner”. Lär dig hur du gör det genom att följa [i det här avsnittet](../../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limitation"></a>Ange begränsning Radstorleken och data
Polybase belastningar är begränsade till inläsning rader både mindre än **1 MB** och det går inte att läsa in VARCHR(MAX), NVARCHAR(MAX) eller VARBINARY(MAX). Referera till [här](../../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Du kanske vill dela källtabellerna lodrätt i flera små där den största Radstorleken på var och en av dem inte överskrider gränsen i källdata med rader av större än 1 MB. Mindre tabeller kan sedan laddas med PolyBase och sammanfogas tillsammans i Azure SQL Data Warehouse.

### <a name="sql-data-warehouse-resource-class"></a>SQL Data Warehouse resursklassen
Överväg att för att tilldela användaren som används för att läsa in data till SQL Data Warehouse via PolyBase större resursklassen för att uppnå bästa möjliga genomflöde. Lär dig hur du gör det genom att följa [ändra ett exempel på användaren resurs klassen](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).

### <a name="tablename-in-azure-sql-data-warehouse"></a>Tabellnamn i Azure SQL Data Warehouse
Följande tabell innehåller exempel på hur du anger den **tableName** egenskap i dataset JSON för olika kombinationer av schema och tabellnamn.

| DB-Schema | Tabellnamnet | tableName JSON-egenskapen |
| --- | --- | --- |
| dbo |Mytable prefix |Mytable prefix eller dbo. Mytable prefix eller [dbo]. [MyTable] |
| dbo1 |Mytable prefix |dbo1. Mytable prefix eller [dbo1]. [MyTable] |
| dbo |My.Table |[My.Table] eller [dbo]. [My.Table] |
| dbo1 |My.Table |[dbo1]. [My.Table] |

Om du ser följande fel kan bero det på ett problem med värdet som angetts för egenskapen tableName. Se tabellen på rätt sätt att ange värden för egenskapen tableName JSON.  

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Kolumner med standardvärden
PolyBase-funktionen i Data Factory accepterar för närvarande endast samma antal kolumner som i måltabellen. Att du har en tabell med fyra kolumner och en av dem har definierats med ett standardvärde. Indata måste fortfarande innehålla fyra kolumner. Att tillhandahålla en inkommande dataset 3 kolumner ger ett fel som liknar följande meddelande:

```
All columns of the table must be specified in the INSERT BULK statement.
```
NULL-värde är en speciell typ av standardvärdet. Om kolumnen är nullbar indata (i blob) för kolumnen vara tom (kan inte vara saknas i indata DataSet). PolyBase infogas NULL för dem i Azure SQL Data Warehouse.  

## <a name="auto-table-creation"></a>Automatiskt skapande av tabell
Om du använder guiden Kopiera för att kopiera data från SQL Server eller Azure SQL-databas till Azure SQL Data Warehouse och tabellen som motsvarar källtabellen inte finns i målarkivet kan Data Factory automatiskt skapa tabellen i datalagret genom u här registrerar tabellschemat källa.

Data Factory skapas tabellen i målarkivet med samma namn i datalagret källa. Datatyperna för kolumnerna är valt baserat på följande mappning för typen. Om det behövs, utför konverteringar för att åtgärda alla inkompatibiliteter mellan käll- och Arkiv. Dessutom används resursallokering tabell distribution.

| Typ av kolumn SQL-databas | Måltypen SQL DW kolumn (storleksbegränsning) |
| --- | --- |
| int | int |
| BigInt | BigInt |
| SmallInt | SmallInt |
| TinyInt | TinyInt |
| bitar | bitar |
| Decimal | Decimal |
| numeriskt | Decimal |
| flyttal | flyttal |
| Money | Money |
| Real | Real |
| SmallMoney | SmallMoney |
| Binär | Binär |
| varbinary | Varbinary (upp till 8000) |
| Date | Date |
| Datum och tid | Datum och tid |
| DateTime2 | DateTime2 |
| Tid | Tid |
| DateTimeOffset | DateTimeOffset |
| SmallDateTime | SmallDateTime |
| Text | Varchar (upp till 8000) |
| NText | NVarChar (upp till 4 000) |
| Bild | VarBinary (upp till 8000) |
| Unik identifierare | Unik identifierare |
| Char | Char |
| NChar | NChar |
| VarChar | VarChar (upp till 8000) |
| NVarChar | NVarChar (upp till 4 000) |
| XML | Varchar (upp till 8000) |

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../../includes/data-factory-type-repeatability-for-sql-sources.md)]

## <a name="type-mapping-for-azure-sql-data-warehouse"></a>Mappning för Azure SQL Data Warehouse
Som anges i den [data movement aktiviteter](data-factory-data-movement-activities.md) artikeln kopieringsaktiviteten utför automatisk konverteringar från källtyper att registrera typer med följande metod i steg 2:

1. Konvertera från interna källtyper till .NET-typ
2. Konvertera från .NET-typ till interna mottagare typ.

När data flyttas till och från Azure SQL Data Warehouse, används följande mappning från SQL-typen till .NET-typ och vice versa.

Mappningen är samma som den [Datatypsmappningen i SQL Server för ADO.NET](https://msdn.microsoft.com/library/cc716729.aspx).

| SQL Server Database Engine-typ | .NET framework-typ |
| --- | --- |
| bigint |Int64 |
| Binär |byte] |
| bitar |Booleskt värde |
| Char |Sträng, Char] |
| Datum |Datum och tid |
| Datum och tid |Datum och tid |
| datetime2 |Datum och tid |
| DateTimeOffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM-attributet (varbinary(max)) |byte] |
| flyttal |dubbla |
| Bild |byte] |
| int |Int32 |
| Money |Decimal |
| nchar |Sträng, Char] |
| ntext |Sträng, Char] |
| numeriskt |Decimal |
| nvarchar |Sträng, Char] |
| Verklig |Enskild |
| ROWVERSION |byte] |
| smalldatetime |Datum och tid |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Objektet * |
| Text |Sträng, Char] |
| time |TimeSpan |
| tidsstämpel |byte] |
| tinyint |Mottagna byte |
| Unik identifierare |GUID |
| varbinary |byte] |
| varchar |Sträng, Char] |
| xml |XML |

Du kan också mappa kolumner från källan dataset till kolumner från sink datamängd i aktivitetsdefinitionen kopia. Mer information finns i [mappa dataset kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="json-examples-for-copying-data-to-and-from-sql-data-warehouse"></a>JSON-exempel för att kopiera data till och från SQL Data Warehouse
Följande exempel ger exempel JSON definitioner som du kan använda för att skapa en pipeline med hjälp av [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De visar hur du kopierar data till och från Azure SQL Data Warehouse och Azure Blob Storage. Dock datan kan kopieras **direkt** från alla källor till någon av sänkor anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av aktiviteten kopiera i Azure Data Factory.

### <a name="example-copy-data-from-azure-sql-data-warehouse-to-azure-blob"></a>Exempel: Kopiera data från Azure SQL Data Warehouse till Azure-Blob
Exemplet definierar följande Data Factory-enheter:

1. En länkad tjänst av typen [AzureSqlDW](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Indata [dataset](data-factory-create-datasets.md) av typen [AzureSqlDWTable](#dataset-properties).
4. Utdata [dataset](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med Kopieringsaktiviteten som använder [SqlDWSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar tidsserier (varje timme, dag, etc.) data från en tabell i Azure SQL Data Warehouse-databas till en blobb varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

**Azure SQL Data Warehouse länkade tjänsten:**

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
**Azure Blob storage länkade tjänsten:**

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
**Azure SQL Data Warehouse-indatauppsättningen:**

Exemplet förutsätter att du har skapat en tabell ”mytable” som prefix i Azure SQL Data Warehouse och innehåller en kolumn med namnet ”timestampcolumn” för tid series-data.

Inställningen ”externa”: ”true” informerar Data Factory-tjänsten att datamängden är extern till data factory och inte tillverkas av en aktivitet i datafabriken.

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
**Azure Blob utdatauppsättningen:**

Data skrivs till en ny blob varje timme (frekvens: timme, intervall: 1). Sökvägen till mappen för blobben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Mappsökvägen använder år, månad, dag och timmar delar av starttiden.

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

Pipelinen innehåller en kopia-aktivitet som är konfigurerad för att använda indata och utdata-datauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen av **källa** är inställd på **SqlDWSource** och **sink** är inställd på **BlobSink**. SQL-frågan som angetts för den **SqlReaderQuery** egenskapen väljer vilka data under den senaste timmen att kopiera.

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
> I det här exemplet **sqlReaderQuery** har angetts för SqlDWSource. Kopieringsaktiviteten körs den här frågan mot Azure SQL Data Warehouse-källa för att hämta data.
>
> Du kan också ange en lagrad procedur genom att ange den **sqlReaderStoredProcedureName** och **storedProcedureParameters** (om den lagrade proceduren har parametrar).
>
> Om du inte anger sqlReaderQuery eller sqlReaderStoredProcedureName används kolumner som har definierats i avsnittet strukturen i datauppsättningen JSON för att skapa en fråga (Välj kolumn1, kolumn2 från mytable prefix) om du vill köra mot Azure SQL Data Warehouse. Om datauppsättningsdefinitionen saknar strukturen, markeras alla kolumner från tabellen.
>
>

### <a name="example-copy-data-from-azure-blob-to-azure-sql-data-warehouse"></a>Exempel: Kopiera data från Azure Blob till Azure SQL Data Warehouse
Exemplet definierar följande Data Factory-enheter:

1. En länkad tjänst av typen [AzureSqlDW](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Indata [dataset](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Utdata [dataset](data-factory-create-datasets.md) av typen [AzureSqlDWTable](#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med kopieringsaktiviteten som använder [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) och [SqlDWSink](#copy-activity-properties).

Exempel kopiorna tidsserier data (varje timme, dagligen, etc.) från Azure blob till en tabell i Azure SQL Data Warehouse-databas i timmen. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

**Azure SQL Data Warehouse länkade tjänsten:**

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
**Azure Blob storage länkade tjänsten:**

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
**Azure Blob inkommande datamängd:**

Data hämtas från en ny blob varje timme (frekvens: timme, intervall: 1). Mappen sökvägen och filnamnet för blobben utvärderas dynamiskt baserat på starttiden för den sektor som bearbetas. Mappsökvägen använder år, månad och som en dagdel av starttiden filnamn används i timmen som en del av starttiden. ”externa”: ”true” inställningen informerar Data Factory-tjänsten att den här tabellen är extern till data factory och inte tillverkas av en aktivitet i datafabriken.

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
**Azure SQL Data Warehouse utdatauppsättningen:**

Exemplet kopierar data till en tabell med namnet ”mytable” som prefix i Azure SQL Data Warehouse. Skapa tabellen i Azure SQL Data Warehouse med samma antal kolumner som du förväntar dig att Blob CSV-filen ska innehålla. Nya rader läggs till i tabell varje timme.

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

Pipelinen innehåller en kopia-aktivitet som är konfigurerad för att använda indata och utdata-datauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen av **källa** är inställd på **BlobSource** och **sink** är inställd på **SqlDWSink**.

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
En genomgång finns [läsa in 1 TB i Azure SQL Data Warehouse under 15 minuter med Azure Data Factory](data-factory-load-sql-data-warehouse.md) och [Läs in data med Azure Data Factory](../../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) artikel i Azure SQL Data Warehouse-dokumentationen.

## <a name="performance-and-tuning"></a>Prestanda och finjustering
Se [kopiera aktivitet prestanda och justera guiden](data-factory-copy-activity-performance.md) vill veta mer om viktiga faktorer som påverkan prestanda för flytt av data (Kopieringsaktiviteten) i Azure Data Factory och olika sätt att optimera den.
