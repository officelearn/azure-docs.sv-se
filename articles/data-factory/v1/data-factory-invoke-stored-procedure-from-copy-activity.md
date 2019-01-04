---
title: Anropa lagrade procedur från Azure Data Factory-Kopieringsaktiviteten | Microsoft Docs
description: Lär dig hur du anropar en lagrad procedur i Azure SQL Database eller SQL Server från en aktivitet för Azure Data Factory-kopia.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 35e9347039a7b9939ab4d2719f9738429dec168c
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54016078"
---
# <a name="invoke-stored-procedure-from-copy-activity-in-azure-data-factory"></a>Anropa lagrade procedur från Kopieringsaktivitet i Azure Data Factory
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [Transformera data med aktivitet för lagrad procedur i Data Factory](../transform-data-using-stored-procedure.md).


När du kopierar data till [SQL Server](data-factory-sqlserver-connector.md) eller [Azure SQL Database](data-factory-azure-sql-connector.md), kan du konfigurera den **SqlSink** i kopieringsaktiviteten att anropa en lagrad procedur. Du kanske vill använda den lagrade proceduren för att utföra några andra processer (sammanslagning kolumner, leta upp värden, infogning i flera tabeller, etc.) krävs innan du infogar data i tabellen. Den här funktionen utnyttjar [Table-Valued parametrar](https://msdn.microsoft.com/library/bb675163.aspx). 

I följande exempel visas hur du anropar en lagrad procedur i en SQL Server-databas från en Data Factory-pipelinen (kopieringsaktiviteten):  

## <a name="output-dataset-json"></a>Utdatauppsättningen JSON
I datauppsättningen för utdata JSON, ställer du in den **typ** till: **SqlServerTable**. Ange den till **AzureSqlTable** ska användas med en Azure SQL database. Värdet för **tableName** -egenskap måste matcha namnet på den första parametern för den lagrade proceduren.  

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

## <a name="sqlsink-section-in-copy-activity-json"></a>SqlSink-avsnittet i kopieringsaktiviteten JSON
Definiera den **SqlSink** avsnittet i kopieringsaktiviteten JSON på följande sätt. Ange värden för både för att anropa en lagrad procedur vid infoga data i mottagare/måldatabasen **SqlWriterStoredProcedureName** och **SqlWriterTableType** egenskaper. Beskrivningar av de här egenskaperna finns i [SqlSink-avsnittet i artikeln för SQL Server-anslutningen](data-factory-sqlserver-connector.md#sqlsink).

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

## <a name="stored-procedure-definition"></a>Lagrad Procedurdefinition 
I databasen, definierar du den lagrade proceduren med samma namn som **SqlWriterStoredProcedureName**. Den lagrade proceduren hanterar inkommande data från källdatalagret och infogar data i en tabell i måldatabasen. Namnet på den första parametern för lagrad procedur måste matcha det tabellnamn som definierats i datauppsättningen JSON (marknadsföring).

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
AS
BEGIN
    DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
    INSERT [dbo].[Marketing](ProfileID, State)
    SELECT * FROM @Marketing
END
```

## <a name="table-type-definition"></a>Definition av tabell
I databasen, definierar du tabelltyp med samma namn som **SqlWriterTableType**. Schemat för tabelltypen måste matcha schemat för datauppsättningen för indata.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL
)
```

## <a name="next-steps"></a>Nästa steg
Granska följande kopplingen artiklar som för fullständiga JSON-exempel: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
