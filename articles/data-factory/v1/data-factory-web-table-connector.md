---
title: Flytta data från Webbtabell med Azure Data Factory | Microsoft Docs
description: Läs mer om hur du flyttar data från en tabell i en webbsida med Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: f54a26a4-baa4-4255-9791-5a8f935898e2
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 1ba8db3ebe2caf4c37d147f744326b6e631cb556
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54022061"
---
# <a name="move-data-from-a-web-table-source-using-azure-data-factory"></a>Flytta data från en webbadress för tabellen med Azure Data Factory
> [!div class="op_single_selector" title1="Välj vilken version av Data Factory-tjänsten du använder:"]
> * [Version 1](data-factory-web-table-connector.md)
> * [Version 2 (aktuell version)](../connector-web-table.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [tabell Webbanslutning i V2](../connector-web-table.md).

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att flytta data från en tabell i en webbsida till ett datalager för mottagare som stöds. Den här artikeln bygger vidare på den [dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikel som ger en allmän översikt över dataförflyttning Kopieringsaktivitet och en lista över datalager som stöds som källor/mottagare.

Data factory stöder för närvarande endast flyttar data från en webbtabell till datalager, men inte flyttar data från andra data lagrar till ett mål för Web-tabellen.

> [!IMPORTANT]
> Den här Webbanslutning stöder för närvarande endast extrahering tabellinnehåll från en HTML-sida. Använd för att hämta data från en HTTP/s-slutpunkt, [HTTP-anslutningsappen](data-factory-http-connector.md) i stället.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda den här tabellen Webbanslutning måste du konfigurera en lokal Integration Runtime (även kallat Data Management Gateway) och konfigurera den `gatewayName` -egenskapen i sink länkad tjänst. Till exempel för att kopiera från Webbtabell till Azure Blob storage, konfigurera Azure Storage-länkade tjänst som följande:

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>",
      "gatewayName": "<gateway name>"
    }
  }
}
```

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en Kopieringsaktivitet som flyttar data från ett datalager för lokal Cassandra med hjälp av olika verktyg/API: er. 

- Det enklaste sättet att skapa en pipeline är att använda den **Kopieringsguiden**. Se [självstudien: Skapa en pipeline med Copy Wizard](data-factory-copy-data-wizard-tutorial.md) en snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera data. 
- Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall**, **.NET API**, och  **REST-API**. Se [kopiera aktivitet självstudien](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet. 

Om du använder verktyg eller API: er kan utföra du följande steg för att skapa en pipeline som flyttar data från källans datalager till mottagarens datalager:

1. Skapa **länkade tjänster** länka inkommande och utgående data du lagrar till din datafabrik.
2. Skapa **datauppsättningar** som representerar inkommande och utgående data för kopieringen. 
3. Skapa en **pipeline** med en Kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata. 

När du använder guiden skapas JSON-definitioner för dessa Data Factory-entiteter (länkade tjänster, datauppsättningar och pipeline) automatiskt åt dig. När du använder Verktyg/API: er (med undantag för .NET-API) kan definiera du dessa Data Factory-entiteter med hjälp av JSON-format.  Ett exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data från en webbtabell hittar [JSON-exempel: Kopiera data från Webbtabell till Azure Blob](#json-example-copy-data-from-web-table-to-azure-blob) i den här artikeln. 

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory-entiteter som är specifika för en webbtabell:

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
Följande tabell innehåller en beskrivning för JSON-element som är specifika för länkade webbtjänsten.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Type-egenskapen måste anges till: **Webb** |Ja |
| URL |URL: en till webbadressen |Ja |
| authenticationType |Anonym. |Ja |

### <a name="using-anonymous-authentication"></a>Använder anonym autentisering

```json
{
    "name": "web",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [skapar datauppsättningar](data-factory-create-datasets.md) artikeln. Avsnitt som struktur, tillgänglighet och princip av en datauppsättnings-JSON är liknande för alla datauppsättningstyper av (Azure SQL, Azure-blob, Azure-tabell osv.).

Den **typeProperties** avsnittet är olika för varje typ av datauppsättning och tillhandahåller information om platsen för data i datalagret. TypeProperties avsnittet för datauppsättningen av typen **WebTable** har följande egenskaper

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Typ av datauppsättningen. måste vara inställt på **WebTable** |Ja |
| sökväg |En relativ URL till den resurs som innehåller tabellen. |Nej. Om sökvägen inte anges används bara den URL som anges i länkade tjänstedefinition. |
| index |Index för tabellen i resursen. Se [Get index för en tabell i en HTML-sida](#get-index-of-a-table-in-an-html-page) avsnittet anvisningar för hur du hämtar index för en tabell i en HTML-sida. |Ja |

**Exempel:**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [skapa Pipelines](data-factory-create-pipelines.md) artikeln. Egenskaper, till exempel namn, beskrivning, indata och utdata tabeller och principen är tillgängliga för alla typer av aktiviteter.

Medan egenskaper som är tillgängliga i avsnittet typeProperties aktivitetens varierar med varje aktivitetstyp av. För kopieringsaktiviteten variera de beroende på vilka typer av källor och mottagare.

För närvarande när källan i kopieringsaktiviteten är av typen **WebSource**, inga ytterligare egenskaper som stöds.


## <a name="json-example-copy-data-from-web-table-to-azure-blob"></a>JSON-exempel: Kopiera data från Webbtabell till Azure Blob
I följande exempel visas:

1. En länkad tjänst av typen [Web](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Indata [datauppsättning](data-factory-create-datasets.md) av typen [WebTable](#dataset-properties).
4. Utdata [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med en Kopieringsaktivitet som använder [WebSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från en webbtabell till en Azure-blob varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

I följande exempel visas hur du kopierar data från en webbtabell till en Azure-blob. Dock datan kan kopieras direkt till någon av de mottagare som anges i den [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikeln med hjälp av Kopieringsaktiviteten i Azure Data Factory.

**Web-länkade tjänst** det här exemplet använder tjänsten Webblänkade med anonym autentisering. Se [Web länkad tjänst](#linked-service-properties) för olika typer av autentisering som du kan använda.

```json
{
    "name": "WebLinkedService",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

**Länkad Azure Storage-tjänst**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>",
      "gatewayName": "<gateway name>"
    }
  }
}
```

**WebTable indatauppsättningen** inställningen **externa** till **SANT** informerar Data Factory-tjänsten att datauppsättningen är extern till datafabriken och inte kommer från en aktivitet i data factory.

> [!NOTE]
> Se [Get index för en tabell i en HTML-sida](#get-index-of-a-table-in-an-html-page) avsnittet anvisningar för hur du hämtar index för en tabell i en HTML-sida.  
>
>

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```


**Utdatauppsättning för Azure-blob**

Data skrivs till en ny blob varje timme (frequency: timme, intervall: 1).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/Movies"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```



**Pipeline med en Kopieringsaktivitet**

Pipelinen innehåller en Kopieringsaktivitet som har konfigurerats för användning av in- och utdatauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen i **källa** är **WebSource** och **mottagare** är **BlobSink**.

Se [WebSource typegenskaperna](#copy-activity-type-properties) lista över egenskaper som stöds av WebSource.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "WebTableToAzureBlob",
        "description": "Copy from a Web table to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "WebTableInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "WebSource"
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

## <a name="get-index-of-a-table-in-an-html-page"></a>Hämta index för en tabell i en HTML-sida
1. Starta **Excel 2016** och växla till den **Data** fliken.  
2. Klicka på **ny fråga** i verktygsfältet, pekar på **från andra källor** och klicka på **från webben**.

    ![Power Query-menyn](./media/data-factory-web-table-connector/PowerQuery-Menu.png)
3. I den **från webben** dialogrutan anger **URL** som du vill använda i länkad tjänst JSON (till exempel: https://en.wikipedia.org/wiki/) tillsammans med sökvägen som du anger för datauppsättningen (till exempel: Afrika % 27s_100_Years... 100_Movies), och klicka på **OK**.

    ![Web-dialogrutan](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

    URL som används i det här exemplet: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Om du ser **åtkomst webbinnehåll** dialogrutan väljer du rätt **URL**, **autentisering**, och klicka på **Connect**.

   ![Dialogrutan för åtkomst till webb-innehåll](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5. Klicka på en **tabell** objekt i trädvyn se innehåll från tabellen och klicka sedan på **redigera** längst ned.  

   ![Storleksändras](./media/data-factory-web-table-connector/Navigator-DialogBox.png)
6. I den **frågeredigeraren** fönstret klickar du på **Avancerad redigerare** i verktygsfältet.

    ![Knappen Avancerat redigeringsprogram](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)
7. I dialogrutan Avancerad redigerare är talet bredvid ”källa” indexet.

    ![Avancerad redigerare - Index](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)

Om du använder Excel 2013 kan du använda [Microsoft Power Query för Excel](https://www.microsoft.com/download/details.aspx?id=39379) att hämta index. Se [Anslut till en webbsida](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) nedan för information. Stegen är liknande om du använder [Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/).

> [!NOTE]
> Om du vill mappa kolumner från datauppsättningen för källan till kolumner från en datauppsättning för mottagare, se [mappning av kolumner för datauppsättningar i Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestanda- och justering
Se [kopiera aktivitet prestanda- och Justeringsguide](data-factory-copy-activity-performance.md) att lära dig om viktiga faktorer att påverka prestandan för dataförflyttning (Kopieringsaktiviteten) i Azure Data Factory och olika sätt att optimera den.
