---
title: Det finns inte några länkade tjänster i Azure Data Factory
description: Läs mer om länkade tjänster i Data Factory. Länkade tjänster länkar beräknings-/datalager till datafabriken.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: 90e51e8b56bd3fb63d56c630d47770e97f439796
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75563554"
---
# <a name="linked-services-in-azure-data-factory"></a>Det finns inte några länkade tjänster i Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-create-datasets.md)
> * [Aktuell version](concepts-linked-services.md)

I den här artikeln beskrivs vilka länkade tjänster som är, hur de definieras i JSON-format och hur de används i Azure Data Factory-pipelines.

Om du inte har något nytt för Data Factory läser [du Introduktion till Azure Data Factory](introduction.md) för en översikt.

## <a name="overview"></a>Översikt
En datafabrik kan ha en eller flera pipelines. En **pipeline** är en logisk gruppering av **aktiviteter** som tillsammans utför en uppgift. Aktiviteterna i en pipeline definierar åtgärder som ska utföras för dina data. Du kan till exempel använda en kopieringsaktivitet för att kopiera data från en lokal SQL Server till Azure Blob-lagring. Sedan kan du använda en Hive-aktivitet som kör ett Hive-skript på ett Azure HDInsight-kluster för att bearbeta data från Blob-lagring för att producera utdata. Slutligen kan du använda en andra kopieringsaktivitet för att kopiera utdata till Azure SQL Data Warehouse, ovanpå vilka business intelligence -rapporteringslösningar (BI) skapas. Mer information om pipelines och aktiviteter finns i [Pipelines och aktiviteter](concepts-pipelines-activities.md) i Azure Data Factory.

Nu är en **datauppsättning** en namngiven vy av data som helt enkelt pekar eller refererar till de data som du vill använda i dina **aktiviteter** som indata och utdata.

Innan du skapar en datauppsättning måste du skapa en **länkad tjänst** för att länka datalagret till datafabriken. Länkade tjänster liknar anslutningssträngar som definierar den anslutningsinformation som behövs för att Data Factory ska kunna ansluta till externa resurser. Tänk på det så här; Datauppsättningen representerar datastrukturen i de länkade datalagret och den länkade tjänsten definierar anslutningen till datakällan. En Azure Storage-länkad tjänst länkar till exempel ett lagringskonto till datafabriken. En Azure Blob-datauppsättning representerar blob-behållaren och mappen i det Azure-lagringskontot som innehåller de indatablobar som ska bearbetas.

Här är ett exempelscenario. Om du vill kopiera data från Blob-lagring till en SQL-databas skapar du två länkade tjänster: Azure Storage och Azure SQL Database. Skapa sedan två datauppsättningar: Azure Blob-datauppsättning (som refererar till azure storage-länkade tjänsten) och Azure SQL Table-datauppsättningen (som refererar till azure SQL Database-länkade tjänsten). Azure Storage- och Azure SQL Database-länkade tjänster innehåller anslutningssträngar som Data Factory använder vid körning för att ansluta till din Azure Storage respektive Azure SQL Database. Azure Blob-datauppsättningen anger blob-behållaren och blob-mappen som innehåller indatablobar i blob-lagringen. Datauppsättningen för Azure SQL Table anger SQL-tabellen i SQL-databasen som data ska kopieras till.

I följande diagram visas relationerna mellan pipeline, aktivitet, datauppsättning och länkad tjänst i Data Factory:

![Förhållandet mellan pipeline, aktivitet, datauppsättning, länkade tjänster](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>Länkad tjänst JSON
En länkad tjänst i Data Factory definieras i JSON-format på följande sätt:

```json
{
    "name": "<Name of the linked service>",
    "properties": {
        "type": "<Type of the linked service>",
        "typeProperties": {
              "<data store or compute-specific type properties>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

I följande tabell beskrivs egenskaper i ovanstående JSON:

Egenskap | Beskrivning | Krävs |
-------- | ----------- | -------- |
namn | Namnet på den länkade tjänsten. Se [Azure Data Factory - Namngivningsregler](naming-rules.md). |  Ja |
typ | Typ av den länkade tjänsten. Till exempel: AzureStorage (datalager) eller AzureBatch (beräkning). Se beskrivningen för typeProperties. | Ja |
typeProperties | Typegenskaperna är olika för varje datalager eller beräkning. <br/><br/> Information om vilka typer som stöds och deras typegenskaper finns i [datauppsättningstyptabellen](concepts-datasets-linked-services.md#dataset-type) i den här artikeln. Navigera till artikeln för anslutningsappen för datalager och lär dig mer om typegenskaper som är specifika för ett datalager. <br/><br/> För beräkningstyperna som stöds och deras typegenskaper finns i [Beräkna länkade tjänster](compute-linked-services.md). | Ja |
connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller Self-hosted Integration Runtime (om ditt datalager finns i ett privat nätverk). Om det inte anges används standardkörningen för Azure Integration. | Inga

## <a name="linked-service-example"></a>Exempel på länkad tjänst
Följande länkade tjänst är en Azure Storage-länkad tjänst. Observera att typen är inställd på AzureStorage. Typegenskaperna för azure storage-länkade tjänsten inkluderar en anslutningssträng. Tjänsten Data Factory använder den här anslutningssträngen för att ansluta till datalagret vid körning.

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="create-linked-services"></a>Skapa länkade tjänster

Du kan skapa länkade tjänster med hjälp av något av dessa verktyg eller SDK:er: [.NET API](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [REST API](quickstart-create-data-factory-rest-api.md), Azure Resource Manager Template och Azure portal

## <a name="data-store-linked-services"></a>Datalagrets länkade tjänster
Du hittar listan över datalager som stöds av Data Factory från [kopplingsöversiktsartikeln.](copy-activity-overview.md#supported-data-stores-and-formats) Klicka på ett datalager om du vill lära dig de anslutningsegenskaper som stöds.

## <a name="compute-linked-services"></a>Länkade tjänster för Compute
[Referensberäkningsmiljöer som stöds](compute-linked-services.md) för information om olika beräkningsmiljöer som du kan ansluta till från datafabriken samt de olika konfigurationerna.

## <a name="next-steps"></a>Nästa steg
Se följande självstudiekurs för steg-för-steg-instruktioner för att skapa pipelines och datauppsättningar med hjälp av något av dessa verktyg eller SDK:er.

- [Snabbstart: skapa en datafabrik med hjälp av .NET](quickstart-create-data-factory-dot-net.md)
- [Snabbstart: skapa en datafabrik med PowerShell](quickstart-create-data-factory-powershell.md)
- [Snabbstart: skapa en datafabrik med REST API](quickstart-create-data-factory-rest-api.md)
- [Snabbstart: skapa en datafabrik med Azure-portalen](quickstart-create-data-factory-portal.md)
