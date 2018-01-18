---
title: "Transformera data med hjälp av aktiviteten lagrad procedur i Azure Data Factory | Microsoft Docs"
description: "Beskriver hur du använder lagrade Proceduraktiviteten i SQL Server för att anropa en lagrad procedur i en Azure SQL Database/Data Warehouse från Data Factory-pipelinen."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: shengc
ms.openlocfilehash: 6b4523747b57ee7a3d48211c9bb7fba1123fe4ce
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2018
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Transformera data med hjälp av aktiviteten lagrad procedur för SQL Server i Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-stored-proc-activity.md)
> * [Version 2 – förhandsversion](transform-data-using-stored-procedure.md)


Du kan använda data transformation aktiviteter i en Datafabrik [pipeline](concepts-pipelines-activities.md) att transformera och bearbeta rådata till förutsägelser och insikter. Den lagrade Proceduraktiviteten är en av omvandling av aktiviteter som har stöd för Data Factory. Den här artikeln bygger på den [Transformera data](transform-data.md) artikel som presenterar en allmän översikt över data transformation och stöds omvandling aktiviteter i Data Factory.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [lagrade Proceduraktiviteten i V1](v1/data-factory-stored-proc-activity.md).
> 
> Om du har använt Azure Data Factory, Läs igenom [introduktion till Azure Data Factory](introduction.md) och gör kursen: [Självstudier: Transformera data](tutorial-transform-data-spark-powershell.md) innan du läser den här artikeln. 

Du kan använda den lagrade Proceduraktiviteten för att anropa en lagrad procedur i någon av följande datalager i ditt företag eller på ett Azure-dator (VM): 

- Azure SQL Database
- Azure SQL Data Warehouse
- SQL Server-databas.  Om du använder SQL Server, installera själva värdbaserade integration runtime på samma dator som värd för databasen eller på en separat dator som har åtkomst till databasen. Automatisk värdbaserade integration runtime är en komponent som ansluter datakällor på lokala/på virtuella Azure-datorn med molntjänster i en säker och hanterad sätt. Se [egenvärdbaserat integrering runtime](create-self-hosted-integration-runtime.md) artikeln för information.

> [!IMPORTANT]
> När du kopierar data till Azure SQL Database eller SQL Server, kan du konfigurera den **SqlSink** i en Kopieringsaktivitet att anropa en lagrad procedur med hjälp av den **sqlWriterStoredProcedureName** egenskapen. Mer information om egenskapen Se följande artiklar för koppling: [Azure SQL Database](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md). Anropa en lagrad procedur vid kopiering av data till en Azure SQL Data Warehouse med hjälp av en kopieringsaktiviteten stöds inte. Men du kan använda aktiviteten lagrad procedur för att anropa en lagrad procedur i en SQL Data Warehouse. 
>
> När du kopierar data från Azure SQL Database eller SQL Server eller Azure SQL Data Warehouse, kan du konfigurera **SqlSource** i en Kopieringsaktivitet att anropa en lagrad procedur för att läsa data från källdatabasen med hjälp av den  **sqlReaderStoredProcedureName** egenskapen. Mer information finns i följande artiklar för koppling: [Azure SQL Database](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md), [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>Information om syntax
Här är JSON-format för att definiera en lagrade Proceduraktiviteten:

```json
{
    "name": "Stored Procedure Activity",
    "description":"Description",
    "type": "SqlServerStoredProcedure",
    "linkedServiceName": {
        "referenceName": "AzureSqlLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "storedProcedureName": "sp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

I följande tabell beskrivs egenskaperna JSON:

| Egenskap                  | Beskrivning                              | Krävs |
| ------------------------- | ---------------------------------------- | -------- |
| namn                      | Namnet på aktiviteten                     | Ja      |
| description               | Text som beskriver aktiviteten är det som används för | Nej       |
| typ                      | För lagrade Proceduraktiviteten aktivitetstypen är **SqlServerStoredProcedure** | Ja      |
| linkedServiceName         | Referensen till den **Azure SQL Database** eller **Azure SQL Data Warehouse** eller **SQL Server** registreras som en länkad tjänst i Data Factory. Mer information om den här länkade tjänsten, se [Compute länkade tjänster](compute-linked-services.md) artikel. | Ja      |
| storedProcedureName       | Ange namnet på den lagrade proceduren att anropa. | Ja      |
| storedProcedureParameters | Ange värden för parametrarna för lagrade procedurer. Använd `"param1": { "value": "param1Value","type":"param1Type" }` skickar parametervärden och deras typ som stöds av datakällan. Om du måste överföra null för en parameter kan använda `"param1": { "value": null }` (alla gemen). | Nej       |

## <a name="next-steps"></a>Nästa steg
Se följande artiklar som förklarar hur du Transformera data på andra sätt: 

* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [Pig-aktivitet](transform-data-using-hadoop-pig.md)
* [MapReduce Activity](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming Activity](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Bach körning av aktiviteten](transform-data-using-machine-learning.md)
* [Aktiviteten lagrad procedur](transform-data-using-stored-procedure.md)
