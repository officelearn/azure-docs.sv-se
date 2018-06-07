---
title: Anropa lagrade procedur från Azure Data Factory-Kopieringsaktiviteten | Microsoft Docs
description: Lär dig mer om att anropa en lagrad procedur i Azure SQL Database eller SQL Server från en aktivitet för Azure Data Factory kopia.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c06e8c1a64f6f784f106c9b925f698b33c196320
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621844"
---
# <a name="invoke-stored-procedure-from-copy-activity-in-azure-data-factory"></a>Anropa lagrade procedur från kopieringsaktiviteten i Azure Data Factory
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [Transformera data med hjälp av aktiviteten lagrad procedur i Data Factory version 2](../transform-data-using-stored-procedure.md).


När du kopierar data till [SQL Server](data-factory-sqlserver-connector.md) eller [Azure SQL Database](data-factory-azure-sql-connector.md), kan du konfigurera den **SqlSink** i en Kopieringsaktivitet att anropa en lagrad procedur. Du kanske vill använda den lagrade proceduren för att utföra några andra processer (sammanslagning kolumner, söka efter värden, infogning i flera tabeller, etc.) krävs innan du infogar data i måltabellen. Den här funktionen utnyttjar [Table-Valued parametrar](https://msdn.microsoft.com/library/bb675163.aspx). 

I följande exempel visas hur du anropa en lagrad procedur i en SQL Server-databas från Data Factory-pipelinen (kopieringsaktiviteten):  

## <a name="output-dataset-json"></a>Utdatauppsättningen JSON
Ange i utdatauppsättningen JSON den **typen** till: **SqlServerTable**. Ange det till **AzureSqlTable** ska användas med en Azure SQL database. Värdet för **tableName** -egenskap måste matcha namnet på den första parametern för den lagrade proceduren.  

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

## <a name="sqlsink-section-in-copy-activity-json"></a>SqlSink avsnitt i JSON-kopieringsaktiviteten
Definiera den **SqlSink** under kopieringsaktiviteten JSON på följande sätt. Ange värden för både för att anropa en lagrad procedur vid infoga data i sink/måldatabasen **SqlWriterStoredProcedureName** och **SqlWriterTableType** egenskaper. Beskrivningar av de här egenskaperna finns [SqlSink avsnitt i SQL Server-anslutningen artikeln](data-factory-sqlserver-connector.md#sqlsink).

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
I databasen, definierar du den lagrade proceduren med samma namn som **SqlWriterStoredProcedureName**. Den lagrade proceduren hanterar inkommande data från datalagret källa och infogar data i en tabell i måldatabasen. Namnet på den första parametern för lagrad procedur måste matcha det tabellnamn som definieras i dataset JSON (marknadsföring).

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
AS
BEGIN
    DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
    INSERT [dbo].[Marketing](ProfileID, State)
    SELECT * FROM @Marketing
END
```

## <a name="table-type-definition"></a>Typdefinitionen för tabellen
I databasen, definierar den tabell med samma namn som **SqlWriterTableType**. Schemat för tabelltypen måste matcha schemat för inkommande dataset.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL
)
```

## <a name="next-steps"></a>Nästa steg
Granska följande kopplingen artiklar som slutförts JSON-exempel: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
