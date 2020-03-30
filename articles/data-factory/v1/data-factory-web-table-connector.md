---
title: Flytta data från webbtabell med Azure Data Factory
description: Lär dig mer om hur du flyttar data från en tabell på en webbsida med Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: f54a26a4-baa4-4255-9791-5a8f935898e2
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d2ea038c7d7212529185d77a6ba9e64deacb1c9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265719"
---
# <a name="move-data-from-a-web-table-source-using-azure-data-factory"></a>Flytta data från en webbtabellkälla med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-web-table-connector.md)
> * [Version 2 (aktuell version)](../connector-web-table.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [Webbtabellkopplingen i V2](../connector-web-table.md).

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att flytta data från en tabell på en webbsida till ett diskbänksdatalager som stöds. Den här artikeln bygger på artikeln [om dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) som ger en allmän översikt över dataförflyttningar med kopieringsaktivitet och listan över datalager som stöds som källor/sänkor.

Data factory stöder för närvarande endast att flytta data från en webbtabell till andra datalager, men inte att flytta data från andra datalager till ett webbtabellmål.

> [!IMPORTANT]
> Den här webbkopplingen stöder för närvarande endast att extrahera tabellinnehåll från en HTML-sida. Om du vill hämta data från en HTTP/s-slutpunkt använder du [HTTP-anslutningsappen](data-factory-http-connector.md) i stället.

## <a name="prerequisites"></a>Krav

Om du vill använda den här webbtabellkopplingen måste du konfigurera en självvärderad integrationskörning (aka Data Management Gateway) och konfigurera egenskapen `gatewayName` i den sink-länkade tjänsten. Om du till exempel vill kopiera från webbtabell till Azure Blob-lagring konfigurerar du den Azure Storage-länkade tjänsten som följande:

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
Du kan skapa en pipeline med en kopieringsaktivitet som flyttar data från ett lokalt Cassandra-datalager med hjälp av olika verktyg/API:er. 

- Det enklaste sättet att skapa en pipeline är att använda **kopieringsguiden**. Se [självstudiekurs: Skapa en pipeline med hjälp av kopieringsguiden](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång när du skapar en pipeline med hjälp av guiden Kopiera data. 
- Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-mall,** **.NET API**och REST **API**. Se [Kopiera aktivitetshandledning](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för steg-för-steg-instruktioner för att skapa en pipeline med en kopieringsaktivitet. 

Oavsett om du använder verktygen eller API:erna utför du följande steg för att skapa en pipeline som flyttar data från ett källdatalager till ett sink-datalager:

1. Skapa **länkade tjänster** för att länka in- och utdatalager till datafabriken.
2. Skapa **datauppsättningar** för att representera in- och utdata för kopieringen. 
3. Skapa en **pipeline** med en kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata. 

När du använder guiden skapas JSON-definitioner för dessa datafabrikentiteter (länkade tjänster, datauppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API:er (förutom .NET API) definierar du dessa datafabrikentiteter med hjälp av JSON-formatet.  Ett exempel på ett exempel med JSON-definitioner för datafabrikentiteter som används för att kopiera data från en webbtabell finns i [JSON-exempel: Kopiera data från webbtabell till Azure Blob-avsnitt](#json-example-copy-data-from-web-table-to-azure-blob) i den här artikeln. 

I följande avsnitt finns information om JSON-egenskaper som används för att definiera datafabrikentiteter som är specifika för en webbtabell:

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
I följande tabell beskrivs beskrivning av JSON-element som är specifika för webblänkade tjänster.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen Type måste anges till: **Web** |Ja |
| URL |URL till webbkällan |Ja |
| authenticationType |Anonym. |Ja |

### <a name="using-anonymous-authentication"></a>Använda anonym autentisering

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
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Skapa datauppsättningar.](data-factory-create-datasets.md) Avsnitt som struktur, tillgänglighet och princip för en datauppsättning JSON är liknande för alla datauppsättningstyper (Azure SQL, Azure blob, Azure-tabell osv.).

Avsnittet **typeProperties** är olika för varje typ av datauppsättning och ger information om platsen för data i datalagret. Avsnittet typeProperties för webbtabell av typens **datauppsättning** har följande egenskaper

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ |typ av datauppsättning. måste vara inställt på **WebTable** |Ja |
| path |En relativ URL till resursen som innehåller tabellen. |Nej. När sökvägen inte anges används endast den URL som anges i den länkade tjänstdefinitionen. |
| Index |Indexet för tabellen i resursen. Se [Hämta index över en tabell i ett HTML-sidavsnitt](#get-index-of-a-table-in-an-html-page) för steg för att hämta index över en tabell på en HTML-sida. |Ja |

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
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Skapa pipelines.](data-factory-create-pipelines.md) Egenskaper som namn, beskrivning, indata- och utdatatabeller och princip är tillgängliga för alla typer av aktiviteter.

Medan egenskaper som är tillgängliga i avsnittet typeProperties i aktiviteten varierar med varje aktivitetstyp. För kopieringsaktivitet varierar de beroende på vilka typer av källor och sänkor som finns.

När källan i kopieringsaktiviteten är av typen **WebSource**stöds inga ytterligare egenskaper.


## <a name="json-example-copy-data-from-web-table-to-azure-blob"></a>JSON exempel: Kopiera data från webbtabell till Azure Blob
Följande exempel visar:

1. En länkad tjänst av typen [Web](#linked-service-properties).
2. En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. En [indatauppsättning](data-factory-create-datasets.md) av typen [WebTable](#dataset-properties).
4. En [utdatauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. En [pipeline](data-factory-create-pipelines.md) med kopieringsaktivitet som använder [WebSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Exemplet kopierar data från en webbtabell till en Azure-blob varje timme. De JSON-egenskaper som används i dessa prover beskrivs i avsnitt som följer proverna.

Följande exempel visar hur du kopierar data från en webbtabell till en Azure-blob. Data kan dock kopieras direkt till någon av de diskhoar som anges i artikeln [Data movement Activities](data-factory-data-movement-activities.md) med hjälp av kopieringsaktiviteten i Azure Data Factory.

**Webblänkd tjänst** I det här exemplet används den webblänkade tjänsten med anonym autentisering. Se [avsnittet Webblänkade tjänster](#linked-service-properties) för olika typer av autentisering som du kan använda.

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

**Azure Storage-länkad tjänst**

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

**Datauppsättning för webbtabletdata** Om du ställer **in externt** för **true** informerar datafabrikstjänsten om att datauppsättningen är extern till datafabriken och inte produceras av en aktivitet i datafabriken.

> [!NOTE]
> Se [Hämta index över en tabell i ett HTML-sidavsnitt](#get-index-of-a-table-in-an-html-page) för steg för att hämta index över en tabell på en HTML-sida.  
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



**Pipeline med kopieringsaktivitet**

Pipelinen innehåller en kopieringsaktivitet som är konfigurerad för att använda in- och utdatauppsättningar och som är schemalagd att köras varje timme. I JSON-definitionen för pipelinen är **källtypen** inställd på **WebSource** och **sink-typen** är inställd på **BlobSink**.

Se Egenskaper för WebSource-typ för listan över egenskaper som stöds av WebSource.

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

## <a name="get-index-of-a-table-in-an-html-page"></a>Hämta index över en tabell på en HTML-sida
1. Starta **Excel 2016** och växla till fliken **Data.**  
2. Klicka på **Ny fråga** i verktygsfältet, peka på Från **andra källor** och klicka på Från **webben**.

    ![Menyn Power Query](./media/data-factory-web-table-connector/PowerQuery-Menu.png)
3. I dialogrutan **Från webben** anger du **URL** som du skulle använda https://en.wikipedia.org/wiki/) i den länkade tjänsten JSON (till exempel: tillsammans med sökväg som du anger för datauppsättningen (till exempel AFI%27s_100_Years... 100_Movies) och klicka på **OK**.

    ![Dialogrutan Från webben](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

    URL som används i det här exemplet:https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Om dialogrutan **Access-webbinnehåll** visas markerar du rätt **URL,** **autentisering**och klickar på **Anslut**.

   ![Dialogrutan Access-webbinnehåll](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5. Klicka på ett **tabellobjekt** i trädvyn om du vill visa innehåll från tabellen och klicka sedan på **knappen Redigera** längst ned.  

   ![Navigeringsdialogruta](./media/data-factory-web-table-connector/Navigator-DialogBox.png)
6. Klicka på knappen **Avancerad redigerare** i verktygsfältet i fönstret **Frågeredigeraren.**

    ![Knappen Avancerad redigerare](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)
7. I dialogrutan Avancerad redigerare är numret bredvid "Källa" indexet.

    ![Avancerad redigerare - Index](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)

Om du använder Excel 2013 använder du [Microsoft Power Query för Excel för](https://www.microsoft.com/download/details.aspx?id=39379) att hämta indexet. Mer information finns i Artikeln Anslut [till en webbsida.](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) Stegen är liknande om du använder [Microsoft Power BI för skrivbordet](https://powerbi.microsoft.com/desktop/).

> [!NOTE]
> Information om hur du mappar kolumner från källdatauppsättning till kolumner från sink-datauppsättning finns [i Mappa datauppsättningskolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestanda och justering
Se [Kopiera aktivitetsprestanda & justeringsguide](data-factory-copy-activity-performance.md) om du vill veta mer om viktiga faktorer som påverkar prestanda för datarörelser (kopieringsaktivitet) i Azure Data Factory och olika sätt att optimera den.
