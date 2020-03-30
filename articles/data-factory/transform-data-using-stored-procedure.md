---
title: Omvandla data med hjälp av aktiviteten Lagrad procedur
description: I artikeln beskrivs hur du anser SQL Server Stored Procedure Activity för att anropa en lagrad procedur i en Azure SQL Database/Data Warehouse från en Data Factory-pipeline.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 11/27/2018
ms.openlocfilehash: 4a0709b4eaa8742069eecb4c39712e384645304b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74926666"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Omvandla data med hjälp av aktiviteten LAGRAD SQL Server-procedur i Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-stored-proc-activity.md)
> * [Aktuell version](transform-data-using-stored-procedure.md)

Du använder dataomvandlingsaktiviteter i en Data [Factory-pipeline](concepts-pipelines-activities.md) för att omvandla och bearbeta rådata till förutsägelser och insikter. Den lagrade proceduraktiviteten är en av de omvandlingsaktiviteter som Data Factory stöder. Den här artikeln bygger på [transformeringsdataartikeln,](transform-data.md) som ger en allmän översikt över dataomvandling och de omvandlingsaktiviteter som stöds i Data Factory.

> [!NOTE]
> Om du inte har gjort det tidigare i Azure Data Factory läser du [in introduktion till Azure Data Factory](introduction.md) och gör självstudien: [Självstudiekurs: omvandla data](tutorial-transform-data-spark-powershell.md) innan du läser den här artikeln. 

Du kan använda aktiviteten Lagrad procedur för att anropa en lagrad procedur i något av följande datalager i företaget eller på en virtuell Azure-dator (VM): 

- Azure SQL Database
- Azure SQL Data Warehouse
- SQL Server-databas.  Om du använder SQL Server installerar du Självvärdbaserad integreringskörning på samma dator som är värd för databasen eller på en separat dator som har åtkomst till databasen. Självvärdbaserad integrationskörning är en komponent som ansluter datakällor lokalt/på Azure VM med molntjänster på ett säkert och hanterat sätt. Mer information finns i artikeln För [självvärdförd integrering för](create-self-hosted-integration-runtime.md) mer information.

> [!IMPORTANT]
> När du kopierar data till Azure SQL Database eller SQL Server kan du konfigurera **SqlSink** i kopieringsaktivitet för att anropa en lagrad procedur med hjälp av egenskapen **sqlWriterStoredProcedureName.** Mer information om egenskapen finns i följande anslutningsartiklar: [Azure SQL Database](connector-azure-sql-database.md), SQL [Server](connector-sql-server.md). Det går inte att anropa en lagrad procedur när du kopierar data till ett Azure SQL Data Warehouse med hjälp av en kopieringsaktivitet. Men du kan använda den lagrade proceduraktiviteten för att anropa en lagrad procedur i ett SQL Data Warehouse. 
>
> När du kopierar data från Azure SQL Database eller SQL Server eller Azure SQL Data Warehouse kan du konfigurera **SqlSource** i kopieringsaktivitet för att anropa en lagrad procedur för att läsa data från källdatabasen med hjälp av egenskapen **sqlReaderStoredProcedureName.** Mer information finns i följande anslutningsartiklar: [Azure SQL Database](connector-azure-sql-database.md), SQL [Server](connector-sql-server.md), Azure SQL [Data Warehouse](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>Syntaxinformation
Här är JSON-formatet för att definiera en lagrad proceduraktivitet:

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
        "storedProcedureName": "usp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

I följande tabell beskrivs dessa JSON-egenskaper:

| Egenskap                  | Beskrivning                              | Krävs |
| ------------------------- | ---------------------------------------- | -------- |
| namn                      | Aktivitetens namn                     | Ja      |
| description               | Text som beskriver vad aktiviteten används för | Inga       |
| typ                      | För aktivitet med lagrad procedur är **aktivitetstypen SqlServerStoredProcedure** | Ja      |
| linkedServiceName         | Referens till **Azure SQL Database** eller Azure SQL Data **Warehouse** eller SQL **Server** som registrerats som en länkad tjänst i Data Factory. Mer information om den länkade tjänsten finns i artikel [om beräkningslänkade tjänster.](compute-linked-services.md) | Ja      |
| storedProcedureName       | Ange namnet på den lagrade proceduren som ska anropas. | Ja      |
| storedProcedureParameters | Ange värden för lagrade procedurparametrar. Används `"param1": { "value": "param1Value","type":"param1Type" }` för att skicka parametervärden och deras typ som stöds av datakällan. Om du behöver skicka null för `"param1": { "value": null }` en parameter använder du (alla gemener). | Inga       |

## <a name="parameter-data-type-mapping"></a>Mappning av parameterdatatyp
Den datatyp som du anger för parametern är typen Azure Data Factory som mappar till datatypen i datakällan som du använder. Du hittar datatypsmappningarna för datakällan i anslutningsområdet. Några exempel är

| Datakälla          | Mappning av datatyp |
| ---------------------|-------------------|
| Azure SQL Data Warehouse | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#data-type-mapping-for-azure-sql-data-warehouse |
| Azure SQL Database   | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#data-type-mapping-for-azure-sql-database | 
| Oracle               | https://docs.microsoft.com/azure/data-factory/connector-oracle#data-type-mapping-for-oracle |
| SQL Server           | https://docs.microsoft.com/azure/data-factory/connector-sql-server#data-type-mapping-for-sql-server |


## <a name="error-info"></a>Felinformation

När en lagrad procedur misslyckas och felinformation returneras kan du inte samla in felinformationen direkt i aktivitetsutdatan. Data Factory pumpar dock alla sina aktivitetskörningshändelser till Azure Monitor. Bland de händelser som Data Factory pumpar till Azure Monitor, det driver felinformation där. Du kan till exempel ställa in e-postaviseringar från dessa händelser. Mer information finns i [Alert- och Monitor-datafabriker med Azure Monitor](monitor-using-azure-monitor.md).

## <a name="next-steps"></a>Nästa steg
Se följande artiklar som förklarar hur du omvandlar data på andra sätt: 

* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [Grisaktivitet](transform-data-using-hadoop-pig.md)
* [MapReduce-aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming aktivitet](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Bach utförande aktivitet](transform-data-using-machine-learning.md)
* [Lagrad proceduraktivitet](transform-data-using-stored-procedure.md)
