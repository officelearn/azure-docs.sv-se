---
title: Kopiera data till och från Azure SQL Data Warehouse med hjälp av Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från stöds källa lagrar till Azure SQL Data Warehouse eller SQL Data Warehouse sink stöds butiker med hjälp av Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/28/2018
ms.author: jingwang
ms.openlocfilehash: 5a7ee7862e102093efa2c203eac2497b025af4e5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "36337823"
---
#  <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Kopiera data till och från Azure SQL Data Warehouse med hjälp av Azure Data Factory 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you're using:"]
> * [Version 1: GA](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Version 2: Preview](connector-azure-sql-data-warehouse.md)

Den här artikeln förklarar hur du använder Kopieringsaktiviteten i Azure Data Factory för att kopiera data till och från Azure SQL Data Warehouse. Den bygger på den [Kopieringsaktiviteten översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över Kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory för närvarande under förhandsgranskning. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Azure SQL Data Warehouse-kopplingen i V1](v1/data-factory-azure-sql-data-warehouse-connector.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Azure SQL Data Warehouse till alla stöds sink-datalagret. Och du kan kopiera data från alla datalager stöds källa till Azure SQL Data Warehouse. En lista över datalager som stöds som datakällor eller sänkor av Kopieringsaktiviteten, finns det [datalager och format stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här anslutningen i Azure SQL Data Warehouse dessa funktioner:

- Kopiera data genom att använda SQL-autentisering och autentisering för Azure Active Directory (AD Azure) token med ett huvudnamn för tjänsten eller hanterade tjänsten identitet (MSI).
- Hämta data med hjälp av en SQL-fråga eller en lagrad procedur som källa.
- Läs in data med PolyBase eller en grupp Infoga som en mottagare. PolyBase rekommenderar vi av prestandaskäl kopia.

> [!IMPORTANT]
> Observera att PolyBase stöder endast SQL-autentisering men inte Azure AD-autentisering.

> [!IMPORTANT]
> Om du kopierar data med hjälp av Azure Data Factory Integration Runtime, konfigurera en [Azure SQL server-brandvägg](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) så att Azure-tjänster har åtkomst till servern.
> Om du kopierar data med hjälp av en automatisk värdbaserade integration runtime, konfigurera Azure SQL server-brandväggen så att rätt IP-adressintervall. Det här intervallet inkluderar den datorns IP-adress som används för att ansluta till Azure SQL Database.

## <a name="get-started"></a>Kom igång

> [!TIP]
> Använd PolyBase för att uppnå bästa prestanda, att läsa in data till Azure SQL Data Warehouse. Den [Använd PolyBase för att läsa in data till Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) avsnitt innehåller information. En genomgång med ett användningsfall finns [läsa in 1 TB i Azure SQL Data Warehouse under 15 minuter med Azure Data Factory](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som definierar Data Factory entiteter till en Azure SQL Data Warehouse-koppling.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för en länkad Azure SQL Data Warehouse-tjänst:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till **AzureSqlDW**. | Ja |
| connectionString | Ange den information som behövs för att ansluta till Azure SQL Data Warehouse-instans för den **connectionString** egenskapen. Markera det här fältet som en **SecureString** att lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| servicePrincipalId | Ange programmets klient-ID. | Ja, när du använder Azure AD-autentisering med ett huvudnamn för tjänsten. |
| servicePrincipalKey | Ange programmets nyckeln. Markera det här fältet som en SecureString lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja, när du använder Azure AD-autentisering med ett huvudnamn för tjänsten. |
| klient | Ange information om klient (domain name eller klient ID) under där programmet finns. Du kan hämta den håller musen i övre högra hörnet i Azure-portalen. | Ja, när du använder Azure AD-autentisering med ett huvudnamn för tjänsten. |
| connectVia | Den [integrering runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller en egen värdbaserade integration runtime (om datalager finns i ett privat nätverk). Om inget anges används standard-Azure Integration Runtime. | Nej |

För olika typer av autentisering, se följande avsnitt om krav och JSON-exempel respektive:

- [SQL-autentisering](#sql-authentication)
- Azure AD application tokenautentisering: [tjänstens huvudnamn](#service-principal-authentication)
- Azure AD application tokenautentisering: [hanterade tjänstidentiteten](#managed-service-identity-authentication)

### <a name="sql-authentication"></a>SQL-autentisering

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Länkad tjänst-exempel som använder SQL-autentisering

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Autentisering av tjänstens huvudnamn

Följ dessa steg om du vill använda autentisering för service principal-baserad Azure AD-token:

1. **[Skapa ett program i Azure Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application)**  från Azure-portalen. Anteckna namnet på programmet och följande värden som definierar den länkade tjänsten:

    - Program-ID:t
    - Nyckeln för programmet
    - Klient-ID:t

2. **[Etablera en Azure Active Directory-administratör](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  för din Azure SQL server på Azure portal om du inte redan gjort det. Azure AD-administratör kan vara en Azure AD-användare eller Azure AD-grupp. Om du ger gruppen MSI en administratörsroll kan du hoppa över steg 3 och 4. Administratören har fullständig åtkomst till databasen.

3. **[Skapa oberoende databasanvändare](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  för tjänstens huvudnamn. Ansluta till datalagret från eller som du vill kopiera data med hjälp av verktyg som SSMS, med en Azure AD-identitet som har minst ALTER för alla användare. Kör följande T-SQL:
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Bevilja behörigheter som krävs för tjänstens huvudnamn** som vanligt för SQL-användare eller andra. Kör följande kod:

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. **Konfigurera en länkad Azure SQL Data Warehouse-tjänst** i Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Länkad tjänst-exempel som använder service principal autentisering

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            },
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-service-identity-authentication"></a>Den hanterade tjänstidentiteten autentisering

En datafabrik kan associeras med en [hanterade tjänstidentiteten](data-factory-service-identity.md) som representerar specifika fabriken. Du kan använda den här tjänstidentiteten för Azure SQL Data Warehouse-autentisering. Avsedda fabriken har åtkomst till och kopieringsdata från eller till dina data warehouse med hjälp av den här identiteten.

> [!IMPORTANT]
> Observera att PolyBase inte stöds för närvarande för MSI-autentisering.

Följ dessa steg om du vill använda autentisering för MSI-baserad Azure AD-token:

1. **Skapa en grupp i Azure AD.** Se fabriken MSI medlem i gruppen.

    a. Hitta data factory tjänstidentiteten från Azure-portalen. Gå till din data factory **egenskaper**. Kopiera ID. tjänsten identitet

    b. Installera den [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) modul. Logga in med hjälp av den `Connect-AzureAD` kommando. Kör följande kommandon för att skapa en grupp och Lägg till data factory MSI som en medlem.
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory service identity ID>"
    ```

2. **[Etablera en Azure Active Directory-administratör](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  för din Azure SQL server på Azure portal om du inte redan gjort det.

3. **[Skapa oberoende databasanvändare](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  för Azure AD-grupp. Ansluta till datalagret från eller som du vill kopiera data med hjälp av verktyg som SSMS, med en Azure AD-identitet som har minst ALTER för alla användare. Kör följande T-SQL. 
    
    ```sql
    CREATE USER [your Azure AD group name] FROM EXTERNAL PROVIDER;
    ```

4. **Ge Azure AD-grupp nödvändiga behörigheter** som vanligt för SQL-användare och andra. Till exempel köra följande kod.

    ```sql
    EXEC sp_addrolemember [role name], [your Azure AD group name];
    ```

5. **Konfigurera en länkad Azure SQL Data Warehouse-tjänst** i Azure Data Factory.

#### <a name="linked-service-example-that-uses-msi-authentication"></a>Länkad tjänst-exempel som använder MSI-autentisering

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns på [datauppsättningar](https://docs.microsoft.com/en-us/azure/data-factory/concepts-datasets-linked-services) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure SQL Data Warehouse-datauppsättningen.

Om du vill kopiera data från eller till Azure SQL Data Warehouse, ange den **typen** -egenskapen för datamängden som **AzureSqlDWTable**. Följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Den **typen** -egenskapen för dataset måste anges till **AzureSqlDWTable**. | Ja |
| tableName | Namnet på den tabell eller vy i Azure SQL Data Warehouse-instans som den länkade tjänsten refererar till. | Ja |

#### <a name="dataset-properties-example"></a>DataSet egenskaper exempel

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Data Warehouse linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Azure SQL Data Warehouse källa och mottagare.

### <a name="azure-sql-data-warehouse-as-the-source"></a>Azure SQL Data Warehouse som källa

Om du vill kopiera data från Azure SQL Data Warehouse, ange den **typen** egenskap i Kopieringsaktiviteten källan till **SqlDWSource**. Följande egenskaper stöds i aktiviteten kopiera **källa** avsnitt:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Den **typen** -egenskapen för aktiviteten kopiera källan måste anges till **SqlDWSource**. | Ja |
| sqlReaderQuery | Använda anpassade SQL-frågan för att läsa data. Exempel: `select * from MyTable`. | Nej |
| sqlReaderStoredProcedureName | Namnet på den lagrade proceduren som läser data från källtabellen. Den senaste SQL-instruktionen måste vara en SELECT-instruktion i den lagrade proceduren. | Nej |
| storedProcedureParameters | Parametrar för den lagrade proceduren.<br/>Tillåtna värden är namn eller värde-par. Namn och skiftläge parametrar måste matcha namn och versaler och gemener i parametrarna för lagrade procedurer. | Nej |

### <a name="points-to-note"></a>Information

- Om den **sqlReaderQuery** har angetts för den **SqlSource**, kopiera-aktivitet körs den här frågan mot Azure SQL Data Warehouse-källa för att hämta data. Eller så kan du ange en lagrad procedur. Ange den **sqlReaderStoredProcedureName** och **storedProcedureParameters** tar den lagrade proceduren parametrar.
- Om du inte anger någon **sqlReaderQuery** eller **sqlReaderStoredProcedureName**, kolumnerna som definieras i den **struktur** avsnitt i datauppsättningen JSON används för att Skapa en fråga. `select column1, column2 from mytable` körs mot Azure SQL Data Warehouse. Om datauppsättningsdefinitionen inte har den **struktur**, markeras alla kolumner från tabellen.
- När du använder **sqlReaderStoredProcedureName**, du måste ange en **tableName** egenskap i datauppsättningen JSON.

#### <a name="sql-query-example"></a>Exempel för SQL-fråga

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlDWSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="stored-procedure-example"></a>Exempel för lagrad procedur

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlDWSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="stored-procedure-definition"></a>Definition av lagrad procedur

```sql
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

### <a name="azure-sql-data-warehouse-as-sink"></a> Azure SQL Data Warehouse som mottagare

Om du vill kopiera data till Azure SQL Data Warehouse, anger du sink i Kopieringsaktiviteten till **SqlDWSink**. Följande egenskaper stöds i aktiviteten kopiera **sink** avsnitt:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Den **typen** -egenskapen för Kopieringsaktiviteten sink måste anges till **SqlDWSink**. | Ja |
| allowPolyBase | Anger om du vill använda PolyBase, i förekommande fall, i stället för mekanismen BULKINSERT. <br/><br/> Vi rekommenderar att du läser in data till SQL Data Warehouse med PolyBase. Finns det [Använd PolyBase för att läsa in data till Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) avsnittet begränsningar och information.<br/><br/>Tillåtna värden är **SANT** och **FALSKT** (standard).  | Nej |
| polyBaseSettings | En grupp egenskaper som kan anges när den **allowPolybase** egenskap är inställd på **SANT**. | Nej |
| rejectValue | Anger det antal eller procentandelen rader som kan avvisas innan frågan misslyckas.<br/><br/>Mer information om Polybases avvisa alternativ i avsnittet argument i [Skapa extern tabell (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>Tillåtna värden är 0 (standard), 1, 2, osv. |Nej |
| rejectType | Anger om den **rejectValue** alternativ är ett litteralvärde eller ett procentvärde.<br/><br/>Tillåtna värden är **värdet** (standard) och **procentandel**. | Nej |
| rejectSampleValue | Anger antalet rader som ska hämtas innan PolyBase beräknar andelen Avvisade rader.<br/><br/>Tillåtna värden är 1, 2, osv. | Ja, om den **rejectType** är **procentandel**. |
| useTypeDefault | Anger hur du hanterar saknade värden i avgränsade textfiler när PolyBase hämtar data från filen.<br/><br/>Mer information om denna egenskap från avsnittet argument i [skapa externt FILFORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Tillåtna värden är **SANT** och **FALSKT** (standard). | Nej |
| writeBatchSize | Infogar data i SQL-tabellen när buffertstorleken når **writeBatchSize**. Gäller endast när PolyBase inte används.<br/><br/>Det tillåtna värdet är **heltal** (antalet rader). | Nej. Standardvärdet är 10 000. |
| writeBatchTimeout | Vänta tills batch insert-åtgärden slutförs innan tidsgränsen uppnås. Gäller endast när PolyBase inte används.<br/><br/>Det tillåtna värdet är **timespan**. Exempel ”: 00: 30:00” (30 minuter). | Nej |
| preCopyScript | Ange en SQL-fråga för aktiviteten kopiera köras innan skrivning av data till Azure SQL Data Warehouse i varje körning. Använd den här egenskapen Rensa förinstallerade data. | Nej | (#repeatability-under-kopia). | En frågesats. | Nej |

#### <a name="sql-data-warehouse-sink-example"></a>SQL Data Warehouse sink-exempel

```json
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

Mer information om hur du använder PolyBase för att effektivt ladda SQL Data Warehouse i nästa avsnitt.

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Använd PolyBase för att läsa in data till Azure SQL Data Warehouse

Med hjälp av [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) är ett effektivt sätt att läsa in stora mängder data i Azure SQL Data Warehouse med hög genomströmning. Du ser en stor vinst i genomflödet med PolyBase i stället för BULKINSERT standardmekanism. Se [Prestandareferens](copy-activity-performance.md#performance-reference) en detaljerad jämförelse. En genomgång med ett användningsfall finns [ladda 1 TB i Azure SQL Data Warehouse](https://docs.microsoft.com/en-us/azure/data-factory/v1/data-factory-load-sql-data-warehouse).

* Om datakällan finns i Azure Blob storage eller Azure Data Lake Store och formatet är kompatibelt med PolyBase, kopiera direkt till Azure SQL Data Warehouse med PolyBase. Mer information finns i  **[dirigera kopia med PolyBase](#direct-copy-by-using-polybase)**.
* Om din datalagret för källa och formatera ursprungligen inte stöds av PolyBase, använder du den **[mellanlagrad kopia genom att använda PolyBase](#staged-copy-by-using-polybase)** funktion i stället. Stegvis kopieringsfunktionen ger dig bättre genomflöde. Data konverteras automatiskt till PolyBase-kompatibelt format. Och den lagrar data i Azure Blob storage. Data hämtas sedan till SQL Data Warehouse.

> [!IMPORTANT]
> Observera att PolyBase inte stöds för närvarande för MSI-baserat program i Azure AD-tokenautentisering.

### <a name="direct-copy-by-using-polybase"></a>Direkt kopiera genom att använda PolyBase

SQL Data Warehouse PolyBase stöder direkt Azure Blob och Azure Data Lake Store. Den använder tjänstens huvudnamn som källa och har viss fil formatkraven. Om dina källdata uppfyller de kriterier som beskrivs i det här avsnittet, kan du använda PolyBase för att kopiera direkt från datalagret källan till Azure SQL Data Warehouse. Annars använder [mellanlagras kopia av med PolyBase](#staged-copy-by-using-polybase).

> [!TIP]
> Om du vill kopiera data effektivt från Data Lake Store till SQL Data Warehouse, Läs mer från [Azure Data Factory gör det även enklare och praktiskt att få insikter från data när du använder Data Lake Store med SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Om kraven inte uppfylls, kontrollerar du inställningarna för Azure Data Factory och faller automatiskt tillbaka till BULKINSERT mekanism för flytt av data.

1. Den **källa länkade tjänsten** typ är **AzureStorage** eller **AzureDataLakeStore** med huvudsakliga autentiseringen av tjänsten.
2. Den **inkommande dataset** typ är **AzureBlob** eller **AzureDataLakeStoreFile**. Formattypen under `type` egenskaper är **OrcFormat**, **ParquetFormat**, eller **TextFormat**, med följande konfigurationer:

   1. `rowDelimiter` måste vara **\n**.
   2. `nullValue` anges till **tom sträng** (””), eller `treatEmptyAsNull` är inställd på **SANT**.
   3. `encodingName` anges till **utf-8**, vilket är standardvärdet.
   4. `escapeChar`, `quoteChar`, `firstRowAsHeader`, och `skipLineCount` inte anges.
   5. `compression` kan vara **ingen komprimering**, **GZip**, eller **Deflate**.

    ```json
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

3. Det finns inga `skipHeaderLineCount` inställningen **BlobSource** eller **AzureDataLakeStore** för Kopieringsaktivitet i pipelinen.
4. Det finns inga `sliceIdentifierColumnName` inställningen **SqlDWSink** för Kopieringsaktivitet i pipelinen. PolyBase garanterar att alla data har uppdaterats eller ingenting uppdateras i en enda körning. Att uppnå **repeterbarhet**, Använd `sqlWriterCleanupScript`.

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "BlobDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-by-using-polybase"></a>Stegvis kopiera genom att använda PolyBase

Om datakällan inte uppfyller villkoren i föregående avsnitt, aktivera data kopierar via en mellanliggande fristående Azure Blob storage-instans. Det går inte att vara Azure Premium-lagring. Azure Data Factory körs i det här fallet automatiskt omformningar på data som ska uppfylla kraven för data-format för PolyBase. Sedan använder PolyBase för att läsa in data till SQL Data Warehouse. Slutligen rensar den tillfälliga data från blob storage. Se [mellanlagrad kopiera](copy-activity-performance.md#staged-copy) mer information om hur du kopierar data via en fristående Azure Blob storage-instans.

Om du vill använda den här funktionen kan du skapa en [Azure länkade lagringstjänsten](connector-azure-blob-storage.md#linked-service-properties) som refererar till Azure storage-konto med tillfälliga blob storage. Ange den `enableStaging` och `stagingSettings` egenskaper för aktiviteten kopiera som visas i följande kod:

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

## <a name="best-practices-for-using-polybase"></a>Metodtips för att använda PolyBase

I följande avsnitt beskrivs bästa praxis utöver de som nämns i [Metodtips för Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Behörighet krävs

Användaren som läser in data i SQL Data Warehouse måste ha för att använda PolyBase [”behörighet”](https://msdn.microsoft.com/library/ms191291.aspx) på måldatabasen. Ett sätt att uppnå som är att lägga till användaren som en medlem i den **db_owner** roll. Lär dig hur du gör i den [översikt över SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limits"></a>Ange gränser Radstorleken och data

PolyBase belastningar är begränsade till rader som är mindre än 1 MB. De kan inte läsa in VARCHR(MAX), NVARCHAR(MAX) eller VARBINARY(MAX). Mer information finns i [kapacitetsbegränsningar för SQL Data Warehouse tjänsten](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

När datakällan har rader som är större än 1 MB, kanske du vill dela lodrätt källtabellerna i flera små. Kontrollera att den största storleken på varje rad inte överskrider gränsen. Mindre tabeller kan sedan laddas av med PolyBase och sammanfogas tillsammans i Azure SQL Data Warehouse.

### <a name="sql-data-warehouse-resource-class"></a>SQL Data Warehouse resursklassen

Tilldela en större resursklassen för användaren som läser in data i SQL Data Warehouse via PolyBase för att uppnå bästa möjliga genomströmningen.

### <a name="tablename-in-azure-sql-data-warehouse"></a>**tableName** i Azure SQL Data Warehouse

Följande tabell innehåller exempel på hur du anger den **tableName** egenskap i JSON-datauppsättningen. Den visar flera kombinationer av schema och tabellnamn.

| DB-Schema | Tabellnamn | **tableName** JSON-egenskap |
| --- | --- | --- |
| dbo | Mytable prefix | Mytable prefix eller dbo. Mytable prefix eller [dbo]. [MyTable] |
| dbo1 | Mytable prefix | dbo1. Mytable prefix eller [dbo1]. [MyTable] |
| dbo | My.Table | [My.Table] eller [dbo]. [My.Table] |
| dbo1 | My.Table | [dbo1]. [My.Table] |

Om du ser följande fel kan problemet kanske du det angivna värdet för den **tableName** egenskapen. Se tabellen ovan på rätt sätt att ange värden för den **tableName** JSON-egenskapen.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Kolumner med standardvärden

PolyBase-funktionen i Data Factory accepterar för närvarande endast samma antal kolumner som i måltabellen. Ett exempel är en tabell med fyra kolumner där en av dem har definierats med ett standardvärde. Indata måste fortfarande ha fyra kolumner. En inkommande dataset tre kolumner ger ett fel som liknar följande meddelande:

```
All columns of the table must be specified in the INSERT BULK statement.
```

NULL-värdet är en speciell typ av standardvärdet. Om kolumnen är nullbar vara indata i blob för kolumnen tom. Men den kan inte vara saknas i indata DataSet. PolyBase infogas NULL för saknade värden i Azure SQL Data Warehouse.

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Datatypsmappningen för Azure SQL Data Warehouse

När du kopierar data från eller till Azure SQL Data Warehouse, används följande mappningar från Azure SQL Data Warehouse-datatyper till Azure Data Factory tillfälliga datatyper. Se [schema- och Skriv mappningar](copy-activity-schema-and-type-mapping.md) att lära dig hur Kopieringsaktiviteten mappar källtypen schema och data till sink.

| Azure SQL Data Warehouse-datatyp | Data Factory tillfälliga datatyp |
|:--- |:--- |
| bigint | Int64 |
| Binär | Byte] |
| bitar | Boolesk |
| Char | Sträng, Char] |
| datum | DateTime |
| DateTime | DateTime |
| datetime2 | DateTime |
| DateTimeOffset | DateTimeOffset |
| Decimal | Decimal |
| FILESTREAM-attributet (varbinary(max)) | Byte] |
| Flyttal | Dubbel |
| image | Byte] |
| int | Int32 |
| Money | Decimal |
| nchar | Sträng, Char] |
| ntext | Sträng, Char] |
| numeriskt | Decimal |
| nvarchar | Sträng, Char] |
| Verklig | Enkel |
| ROWVERSION | Byte] |
| smalldatetime | DateTime |
| smallint | Int16 |
| smallmoney | Decimal |
| sql_variant | Objektet * |
| text | Sträng, Char] |
| time | TimeSpan |
| tidsstämpel | Byte] |
| tinyint | Byte |
| Unik identifierare | GUID |
| varbinary | Byte] |
| varchar | Sträng, Char] |
| xml | Xml |

## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av Kopieringsaktiviteten i Azure Data Factory finns [datalager och format stöds](copy-activity-overview.md##supported-data-stores-and-formats).
