---
title: Länkade tjänster i Azure Data Factory | Microsoft Docs
description: Läs mer om länkade tjänster i Datafabriken. Länkade tjänster länkar beräkning/datalager till datafabriken.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: shlo
ms.openlocfilehash: ba2041495e1e3c63ee322a0b748753ad6cb68914
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870139"
---
# <a name="linked-services-in-azure-data-factory"></a>Länkade tjänster i Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-create-datasets.md)
> * [Aktuell version](concepts-datasets-linked-services.md)

Den här artikeln beskriver vilka länkade tjänster som är, hur de definieras i JSON-format och hur de används i Azure Data Factory-pipelines.

Om du är nybörjare till Data Factory finns i [introduktion till Azure Data Factory](introduction.md) en översikt.

## <a name="overview"></a>Översikt
En datafabrik kan ha en eller flera pipelines. En **pipeline** är en logisk gruppering av **aktiviteter** som tillsammans utför en uppgift. Aktiviteterna i en pipeline definierar åtgärder som ska utföras för dina data. Du kan till exempel använda en Kopieringsaktivitet som kopierar data från en lokal SQL Server till Azure Blob storage. Du kan sedan använda en Hive-aktivitet som kör ett Hive-skript på ett Azure HDInsight-kluster att bearbeta data från Blob storage för att producera utdata. Slutligen kan du använda en andra Kopieringsaktivitet för att kopiera utdata till Azure SQL Data Warehouse ovanpå som business intelligence (BI) reporting-lösningarna. Läs mer om pipelines och aktiviteter, [Pipelines och aktiviteter](concepts-pipelines-activities.md) i Azure Data Factory.

Nu kan en **datauppsättning** är en namngiven vy över data som helt enkelt pekar eller refererar till de data som du vill använda i din **aktiviteter** som indata eller utdata.

Innan du skapar en datauppsättning, måste du skapa en **länkad tjänst** att länka ditt datalager till datafabriken. Länkade tjänster liknar anslutningssträngar som definierar den anslutningsinformation som behövs för att Data Factory ska kunna ansluta till externa resurser. Se det här sättet. datamängden representerar strukturen för data i länkade datalager och den länkade tjänsten definierar anslutningen till datakällan. Till exempel länkad en Azure Storage-tjänsten länkar ett storage-konto till datafabriken. En Azure Blob-datauppsättning representerar blobbehållaren och mappen i den Azure storage-konto som innehåller indatablobbar som ska bearbetas.

Här är ett exempelscenario. Om du vill kopiera data från Blob storage till en SQL-databas, skapar du två länkade tjänster: Azure Storage och Azure SQL Database. Skapa sedan två datauppsättningar: Azure Blob-datauppsättning (som refererar till den länkade Azure Storage-tjänsten) och Azure SQL-tabelldatauppsättning (som refererar till länkad Azure SQL Database-tjänsten). Innehåller anslutningssträngar som Datafabriken använder vid körning för att ansluta till ditt Azure Storage och Azure SQL Database, respektive Azure Storage och länkad Azure SQL Database-tjänster. Azure Blob-datauppsättning anger blobbehållaren och blobbmapp som innehåller indatablobbar i Blob storage. Azure SQL-tabelldatauppsättning ange den SQL-tabellen i SQL-databasen som data ska kopieras.

Följande diagram visar relationerna mellan pipeline, aktivitet, datauppsättning och den länkade tjänsten i Data Factory:

![Förhållandet mellan pipeline, aktivitet, datauppsättning, länkade tjänster](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>Länkad tjänst-JSON
En länkad tjänst i Data Factory har definierats i JSON-format på följande sätt:

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

I följande tabell beskrivs egenskaperna i ovanstående JSON:

Egenskap  | Beskrivning | Krävs |
-------- | ----------- | -------- |
namn | Namnet på den länkade tjänsten. Se [Azure Data Factory – namnregler](naming-rules.md). |  Ja |
typ | Typ av den länkade tjänsten. Exempel: AzureStorage (datalager) eller AzureBatch (beräkning). Se beskrivningen av typeProperties. | Ja |
typeProperties | Typegenskaperna är olika för varje datalager eller databeräkningar. <br/><br/> Lagra typer och deras egenskaper för data som stöds, se den [datauppsättningstypen](concepts-datasets-linked-services.md#dataset-type) tabellen i den här artikeln. Gå till data store connector artikeln för att lära dig om typegenskaperna som är specifika för ett datalager. <br/><br/> Stöds beräkningstyper och deras egenskaper finns i [länkade tjänster för Compute](compute-linked-services.md). | Ja |
connectVia | Den [Integration Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller lokal Integration Runtime (om ditt datalager finns i ett privat nätverk). Om den inte anges används standard Azure Integration Runtime. | Nej

## <a name="linked-service-example"></a>Länkad tjänst-exempel
Följande länkade tjänst är en länkad Azure Storage-tjänst. Observera att typen har angetts till AzureStorage. Typegenskaperna för länkad Azure Storage-tjänsten innehåller en anslutningssträng. Data Factory-tjänsten använder den här anslutningssträngen för att ansluta till datalagret i körningsfasen.

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="create-linked-services"></a>Skapa länkade tjänster
Du kan skapa länkade tjänster med något av dessa verktyg och SDK: er: [.NET API](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [REST API](quickstart-create-data-factory-rest-api.md), Azure Resource Manager-mall och Azure portal

## <a name="data-store-linked-services"></a>Länkade tjänster för datalager
Ansluta till datalager som finns i vår [datalager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats). Hänvisa till listan för specifika anslutningsegenskaperna som behövs för olika butiker.

## <a name="compute-linked-services"></a>Länkade tjänster för Compute
Referens [compute-miljöer stöds](compute-linked-services.md) för att få information om olika miljöer du kan ansluta till från din data factory, samt olika konfigurationer.

## <a name="next-steps"></a>Nästa steg
Se följande självstudie för stegvisa instruktioner för att skapa pipelines och datauppsättningar med någon av dessa verktyg och SDK: er.

- [Snabbstart: skapa en datafabrik med hjälp av .NET](quickstart-create-data-factory-dot-net.md)
- [Snabbstart: skapa en datafabrik med hjälp av PowerShell](quickstart-create-data-factory-powershell.md)
- [Snabbstart: skapa en datafabrik med hjälp av REST API](quickstart-create-data-factory-rest-api.md)
- [Snabbstart: skapa en datafabrik med hjälp av Azure portal](quickstart-create-data-factory-portal.md)
