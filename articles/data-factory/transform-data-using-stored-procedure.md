---
title: Omvandla data med hjälp av aktiviteten lagringsprocedur i Azure Data Factory | Microsoft Docs
description: Beskriver hur du använder lagrade Proceduraktiviteten i SQL Server för att anropa en lagrad procedur i en Azure SQL Database/Data Warehouse från Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: douglasl
ms.openlocfilehash: a56e9c2a7cceed8012f35c9d02e9c3bc5703b31f
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54353217"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Omvandla data med hjälp av SQL Server-lagrad procedur i Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-stored-proc-activity.md)
> * [Aktuell version](transform-data-using-stored-procedure.md)

Du använder datatransformeringsaktiviteter i en Data Factory [pipeline](concepts-pipelines-activities.md) att transformera och bearbeta rådata till förutsägelser och insikter. Den lagrade Proceduraktiviteten är en av transformeringsaktiviteter som har stöd för Data Factory. Den här artikeln bygger vidare på den [Transformera data](transform-data.md) artikel som anger en allmän översikt över Dataomvandling och stöds transformeringsaktiviteter i Data Factory.

> [!NOTE]
> Om du är nybörjare på Azure Data Factory, Läs igenom [introduktion till Azure Data Factory](introduction.md) och igenom självstudien: [Självstudie: Transformera data](tutorial-transform-data-spark-powershell.md) innan du läser den här artikeln. 

Du kan använda den lagrade Proceduraktiviteten för att anropa en lagrad procedur i någon av följande datalager i ditt företag eller på en Azure-dator (VM): 

- Azure SQL Database
- Azure SQL Data Warehouse
- SQL Server-databas.  Om du använder SQL Server kan du installera lokal integration runtime på samma dator som är värd för databasen eller på en separat dator som har åtkomst till databasen. Lokal integration runtime är en komponent som ansluter data datakällor på plats/på virtuella Azure-datorer med molntjänster i ett säkert och hanterat sätt. Se [integration runtime med egen värd](create-self-hosted-integration-runtime.md) nedan för information.

> [!IMPORTANT]
> När du kopierar data till Azure SQL Database eller SQL Server, kan du konfigurera den **SqlSink** i kopieringsaktiviteten att anropa en lagrad procedur med hjälp av den **sqlWriterStoredProcedureName** egenskapen. Information om egenskapen hittar du i följande artiklar för anslutningen: [Azure SQL Database](connector-azure-sql-database.md), [SQLServer](connector-sql-server.md). Anropa en lagrad procedur när du kopierar data till en Azure SQL Data Warehouse med hjälp av en Kopieringsaktivitet stöds inte. Men du kan använda aktiviteten lagrad procedur för att anropa en lagrad procedur i ett SQL Data Warehouse. 
>
> När du kopierar data från Azure SQL Database eller SQL Server eller Azure SQL Data Warehouse, kan du konfigurera **SqlSource** i kopieringsaktiviteten att anropa en lagrad procedur för att läsa data från källdatabasen med hjälp av den  **sqlReaderStoredProcedureName** egenskapen. Mer information finns i följande artiklar för anslutningen: [Azure SQL Database](connector-azure-sql-database.md), [SQLServer](connector-sql-server.md), [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md)          

 

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
        "storedProcedureName": "usp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

I följande tabell beskrivs de här JSON-egenskaper:

| Egenskap                   | Beskrivning                              | Krävs |
| ------------------------- | ---------------------------------------- | -------- |
| namn                      | Namn på aktiviteten                     | Ja      |
| beskrivning               | Text som beskriver vad aktiviteten används till | Nej       |
| typ                      | För lagrade Proceduraktiviteten aktivitetstyp är **SqlServerStoredProcedure** | Ja      |
| linkedServiceName         | Referensen till den **Azure SQL Database** eller **Azure SQL Data Warehouse** eller **SQL Server** registrerad som en länkad tjänst i Datafabriken. Mer information om den här länkade tjänsten, se [länkade tjänster för Compute](compute-linked-services.md) artikeln. | Ja      |
| storedProcedureName       | Ange namnet på den lagrade proceduren att anropa. | Ja      |
| storedProcedureParameters | Ange värden för parametrarna för lagrad procedur. Använd `"param1": { "value": "param1Value","type":"param1Type" }` skickar parametervärden och deras typ som stöds av datakällan. Om du vill skicka null för en parameter kan du använda `"param1": { "value": null }` (gemener). | Nej       |

## <a name="error-info"></a>Felinformation

När en lagrad procedur misslyckas och returnerar information om fel, kan inte du hämta felinformation direkt i aktivitetsutdata. Data Factory pumps alla dess aktivitetskörning händelser till Azure Monitor. Bland händelser att Data Factory-pumpar till Azure Monitor, skickar den information om det inte. Du kan till exempel konfigurera e-postaviseringar från dessa händelser. Mer information finns i [Avisera och övervaka datafabriker med hjälp av Azure Monitor](monitor-using-azure-monitor.md).

## <a name="next-steps"></a>Nästa steg
Se följande artiklar som beskriver hur du omvandlar data på andra sätt: 

* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [Piggningsåtgärd](transform-data-using-hadoop-pig.md)
* [MapReduce-aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming Activity](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Bach Körningsaktivitet](transform-data-using-machine-learning.md)
* [Lagrad proceduraktivitet](transform-data-using-stored-procedure.md)
