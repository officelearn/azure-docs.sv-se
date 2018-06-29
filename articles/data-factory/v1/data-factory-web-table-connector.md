---
title: Flytta data från webben tabellen med hjälp av Azure Data Factory | Microsoft Docs
description: Läs mer om hur du flyttar data från en tabell i en webbsida med Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: f54a26a4-baa4-4255-9791-5a8f935898e2
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 05833599059c2724529f9fd23edcd86934793835
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048864"
---
# <a name="move-data-from-a-web-table-source-using-azure-data-factory"></a>Flytta data från en webbadress för tabellen med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-web-table-connector.md)
> * [Version 2 (aktuell version)](../connector-web-table.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns [Web tabell connector i V2](../connector-web-table.md).

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att flytta data från en tabell i en webbsida i ett datalager stöds sink. Den här artikeln bygger på den [data movement aktiviteter](data-factory-data-movement-activities.md) artikel som ger en allmän översikt över dataflyttning kopieringsaktiviteten och listan över datakällor som stöds som källor/sänkor.

Data factory stöder för närvarande endast flytta data från en webbserver tabell till andra databaser, men inte flytta data från andra data lagrar till ett mål för Web-tabellen.

> [!IMPORTANT]
> Den här Web connector stöder för närvarande endast extrahera innehållet från en HTML-sida. Använd för att hämta data från en HTTP/s-slutpunkt [HTTP-anslutningen](data-factory-http-connector.md) i stället.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda den här tabellen Web-anslutningen måste du ställa in en Self-hosted integrering Runtime (aka Data Management Gateway) och konfigurera den `gatewayName` egenskap i sink länkade tjänsten. Till exempel för att kopiera från webben tabellen till Azure Blob storage, konfigurera länkad Azure Storage-tjänsten som följande:

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
Du kan skapa en pipeline med en kopia-aktivitet som flyttar data från en lokal Cassandra data store med hjälp av olika verktyg/API: er. 

- Det enklaste sättet att skapa en pipeline är att använda den **guiden Kopiera**. Finns [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera data. 
- Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall** , **.NET API**, och **REST API**. Se [kopiera aktivitet kursen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet. 

Om du använder verktyg eller API: er, kan du utföra följande steg för att skapa en pipeline som flyttar data från ett dataarkiv som källa till ett dataarkiv som mottagare:

1. Skapa **länkade tjänster** att länka inkommande och utgående data lagras till din data factory.
2. Skapa **datauppsättningar** att representera inkommande och utgående data för kopieringen. 
3. Skapa en **pipeline** med en kopia-aktivitet som tar en datamängd som indata och en dataset som utdata. 

När du använder guiden skapas JSON definitioner för dessa Data Factory-enheter (länkade tjänster, datauppsättningar och pipelinen) automatiskt för dig. När du använder Verktyg/API: er (utom .NET API), kan du definiera dessa Data Factory-enheter med hjälp av JSON-format.  Ett exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data från en webbtabell finns [JSON-exempel: kopiera data från Webbtabell till Azure Blob](#json-example-copy-data-from-web-table-to-azure-blob) i den här artikeln. 

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory entiteter i en webbtabell:

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper
Följande tabell innehåller en beskrivning för JSON-element som är specifika för länkade webbtjänsten.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen type måste anges till: **Web** |Ja |
| URL |URL till webbadressen |Ja |
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
En fullständig lista över egenskaper som är tillgängliga för att definiera datauppsättningarna & avsnitt finns i [skapa datauppsättningar](data-factory-create-datasets.md) artikel. Avsnitt som struktur, tillgänglighet och princip på en datamängd JSON är liknande för alla typer av dataset (Azure SQL Azure blob, Azure-tabellen, osv.).

Den **typeProperties** avsnitt är olika för varje typ av dataset och innehåller information om placeringen av data i datalagret. TypeProperties avsnittet för dataset av typen **WebTable** har följande egenskaper

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |Typ av datauppsättningen. måste anges till **WebTable** |Ja |
| sökväg |En relativ URL till den resurs som innehåller tabellen. |Nej. Om sökvägen inte anges används den URL som angavs i definitionen länkade tjänsten. |
| index |Index för tabellen i resursen. Se [Get-index för en tabell i en HTML-sida](#get-index-of-a-table-in-an-html-page) avsnittet steg för att få index för en tabell i en HTML-sida. |Ja |

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
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i [skapar Pipelines](data-factory-create-pipelines.md) artikel. Egenskaper som namn, beskrivning, ingående och utgående tabeller och principen är tillgängliga för alla typer av aktiviteter.

De egenskaper som är tillgängliga i avsnittet typeProperties i aktiviteten varierar beroende på varje aktivitetstyp. För Kopieringsaktivitet kan variera de beroende på vilka typer av datakällor och sänkor.

För närvarande när datakällan i en Kopieringsaktivitet är av typen **WebSource**, inga ytterligare egenskaper som stöds.


## <a name="json-example-copy-data-from-web-table-to-azure-blob"></a>JSON-exempel: kopiera data från Webbtabell till Azure-Blob
I följande exempel visas:

1. En länkad tjänst av typen [Web](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Indata [dataset](data-factory-create-datasets.md) av typen [WebTable](#dataset-properties).
4. Utdata [dataset](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med Kopieringsaktiviteten som använder [WebSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från en webbserver-tabell till en Azure blob varje timme. JSON-egenskaper som används i exemplen beskrivs i exemplen i följande avsnitt.

I följande exempel visas hur du kopierar data från en webbserver-tabell till en Azure blob. Dock datan kan kopieras direkt till någon av sänkor som anges i den [Data Movement aktiviteter](data-factory-data-movement-activities.md) artikel med hjälp av aktiviteten kopiera i Azure Data Factory.

**Web länkade tjänsten** det här exemplet använder webbtjänsten länkade med anonym autentisering. Se [Web länkade tjänsten](#linked-service-properties) avsnittet för olika typer av autentisering som du kan använda.

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

**WebTable inkommande dataset** inställningen **externa** till **SANT** informerar Data Factory-tjänsten att datamängden är extern till data factory och inte tillverkas av en aktivitet i datafabriken.

> [!NOTE]
> Se [Get-index för en tabell i en HTML-sida](#get-index-of-a-table-in-an-html-page) avsnittet steg för att få index för en tabell i en HTML-sida.  
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

Data skrivs till en ny blob varje timme (frekvens: timme, intervall: 1).

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



**Pipeline med kopieringsaktiviteten**

Pipelinen innehåller en kopia-aktivitet som är konfigurerad för att använda indata och utdata-datauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen av **källa** är inställd på **WebSource** och **sink** är inställd på **BlobSink**.

Se [WebSource Typegenskaper](#copy-activity-type-properties) lista över egenskaper som stöds av WebSource.

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
2. Klicka på **ny fråga** i verktygsfältet, pekar på **från andra källor** och på **från webben**.

    ![Power Query-menyn](./media/data-factory-web-table-connector/PowerQuery-Menu.png)
3. I den **från webben** dialogrutan Ange **URL** som du vill använda i länkad tjänst-JSON (till exempel: https://en.wikipedia.org/wiki/) tillsammans med sökvägen som du anger för datauppsättningen (till exempel: Afrika % 27s_100_Years... 100_Movies) och klicka på **OK**.

    ![Från webben dialogrutan](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

    URL som används i det här exemplet: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Om du ser **åtkomst till webbinnehåll** dialogrutan Välj höger **URL**, **autentisering**, och klicka på **Anslut**.

   ![Åtkomst till innehåll dialogrutan för webbplats](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5. Klicka på en **tabell** objekt i trädvyn finns innehåll från tabellen och klicka sedan på **redigera** längst ned.  

   ![Navigator dialogrutan](./media/data-factory-web-table-connector/Navigator-DialogBox.png)
6. I den **frågeredigeraren** -fönstret klickar du på **avancerade Editor** i verktygsfältet.

    ![Knappen Avancerat redigeraren](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)
7. I dialogrutan Avancerad redigerare är talet bredvid ”källa” indexet.

    ![Avancerad redigerare - Index](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)

Om du använder Excel 2013 använder [Microsoft Power Query för Excel](https://www.microsoft.com/download/details.aspx?id=39379) att hämta index. Se [Anslut till en webbsida](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) artikeln för information. Stegen är ungefär som om du använder [Microsoft Power BI för skrivbordet](https://powerbi.microsoft.com/desktop/).

> [!NOTE]
> Om du vill mappa kolumner från källan dataset till kolumner från sink dataset finns [mappa dataset kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestanda och finjustering
Se [kopiera aktivitet prestanda och justera guiden](data-factory-copy-activity-performance.md) vill veta mer om viktiga faktorer som påverkan prestanda för flytt av data (Kopieringsaktiviteten) i Azure Data Factory och olika sätt att optimera den.
