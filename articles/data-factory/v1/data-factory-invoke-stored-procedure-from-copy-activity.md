---
title: Anropa lagrad procedur från Azure Data Factory Copy Activity
description: Lär dig hur du anropar en lagrad procedur i Azure SQL Database eller SQL Server från en Azure Data Factory-kopieringsaktivitet.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d05c2b03a0c498144f37c9b6205053120a596b09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74924087"
---
# <a name="invoke-stored-procedure-from-copy-activity-in-azure-data-factory"></a>Anropa lagrad procedur från kopieringsaktivitet i Azure Data Factory
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [transformera data med hjälp av lagrad proceduraktivitet i Data Factory](../transform-data-using-stored-procedure.md).


När du kopierar data till [SQL Server](data-factory-sqlserver-connector.md) eller [Azure SQL Database](data-factory-azure-sql-connector.md)kan du konfigurera **SqlSink** i kopieringsaktivitet för att anropa en lagrad procedur. Du kanske vill använda den lagrade proceduren för att utföra ytterligare bearbetning (sammanslagningskolumner, svar på värden, införande i flera tabeller osv.) krävs innan data infogas i måltabellen. Den här funktionen drar nytta av [tabellvärderade parametrar](https://msdn.microsoft.com/library/bb675163.aspx). 

Följande exempel visar hur du anropar en lagrad procedur i en SQL Server-databas från en Data Factory-pipeline (kopieringsaktivitet):  

## <a name="output-dataset-json"></a>Utdatauppsättning JSON
I utdatadatauppsättningen JSON anger du **typen** till: **SqlServerTable**. Ställ in den på **AzureSqlTable** att använda med en Azure SQL-databas. Värdet för **egenskapen tableName** måste matcha namnet på den första parametern för den lagrade proceduren.  

```json
{
  "name": "SqlOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlLinkedService",
    "typeProperties": {
      "tableName": "Marketing"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

## <a name="sqlsink-section-in-copy-activity-json"></a>SqlSink avsnitt i kopieringsaktivitet JSON
Definiera **avsnittet SqlSink** i kopieringsaktiviteten JSON enligt följande. Om du vill anropa en lagrad procedur när du infogar data i diskhon/måldatabasen anger du värden för egenskaperna **sqlwriterStoredProcedureName** och **SqlWriterTableType.** Beskrivningar av dessa egenskaper finns [i avsnittet SqlSink i sql server-anslutningsartikeln](data-factory-sqlserver-connector.md#sqlsink).

```json
"sink":
{
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
    "storedProcedureParameters":
            {
                "stringData": 
                {
                    "value": "str1"     
                }
            }
}
```

## <a name="stored-procedure-definition"></a>Definition av lagrad procedur 
I databasen definierar du den lagrade proceduren med samma namn som **SqlWriterStoredProcedureName**. Den lagrade proceduren hanterar indata från källdatalagret och infogar data i en tabell i måldatabasen. Namnet på den första parametern för lagrad procedur måste matcha tabellenNamn som definierats i datauppsättningen JSON (Marknadsföring).

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
AS
BEGIN
    DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
    INSERT [dbo].[Marketing](ProfileID, State)
    SELECT * FROM @Marketing
END
```

## <a name="table-type-definition"></a>Definition av tabelltyp
I databasen definierar du tabelltypen med samma namn som **SqlWriterTableType**. Tabelltypens schema måste matcha schemat för indatauppsättningen.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL
)
```

## <a name="next-steps"></a>Nästa steg
Läs följande kopplingsartiklar som för fullständiga JSON-exempel: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
