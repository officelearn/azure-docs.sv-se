---
title: "Kopiera data från webben tabellen med hjälp av Azure Data Factory | Microsoft Docs"
description: "Läs mer om Web tabell koppling av Azure Data Factory som låter dig kopieringsdata från en webbtabell datalager som stöds av Data Factory som sänkor."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: affe0d7da4b6e40da3b7fdb10d53b9e793ec19bd
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
---
# <a name="copy-data-from-web-table-by-using-azure-data-factory"></a>Kopiera data från Webbtabell med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-web-table-connector.md)
> * [Version 2 – förhandsversion](connector-web-table.md)

Den här artikeln beskriver hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data från en tabell Web-databas. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.


> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Webbtabell connector i V1](v1/data-factory-web-table-connector.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från webben tabell databasen till alla stöds sink-datalagret. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt har stöd för den här tabellen Web-anslutningen **extraherar innehållet från en HTML-sida**. Använd för att hämta data från en HTTP/s-slutpunkt [HTTP-anslutningen](connector-http.md) i stället.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med kopieringsaktiviteten använder .NET SDK, Python SDK, Azure PowerShell, REST-API eller Azure Resource Manager-mall. Se [kopiera aktivitet kursen](create-self-hosted-integration-runtime.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till Web tabell connector.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för Web tabell länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **Web** |Ja |
| URL: en | URL till webbadressen |Ja |
| AuthenticationType | Tillåtna värde är: **anonym**. |Ja |
| connectVia | Den [integrering Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller Self-hosted integrering Runtime (om datalager finns i privat nätverk). Om inget anges används standard-Azure Integration Runtime. |Nej |

**Exempel:**

```json
{
    "name": "WebLinkedService",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "url" : "https://en.wikipedia.org/wiki/",
            "authenticationType": "Anonymous"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av Web tabell dataset.

Om du vill kopiera data från Webbtabell, ange typegenskapen för datauppsättningen till **RelationalTable**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för dataset måste anges till: **WebTable** | Ja |
| Sökväg |En relativ URL till den resurs som innehåller tabellen. |Nej. Om sökvägen inte anges används den URL som angavs i definitionen länkade tjänsten. |
| Index |Index för tabellen i resursen. Se [Get-index för en tabell i en HTML-sida](#get-index-of-a-table-in-an-html-page) avsnittet steg för att få index för en tabell i en HTML-sida. |Ja |

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
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av tabellen webbadress.

### <a name="web-table-as-source"></a>Web tabellen som källa

För att kopiera data från webben tabellen anger du datakällan i kopieringsaktiviteten till **WebSource**, inga ytterligare egenskaper som stöds.

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromWebTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Web table input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "WebSource"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="get-index-of-a-table-in-an-html-page"></a>Hämta index för en tabell i en HTML-sida

1. Starta **Excel 2016** och växla till den **Data** fliken.
2. Klicka på **ny fråga** i verktygsfältet, pekar på **från andra källor** och på **från webben**.

    ![Power Query-menyn](./media/copy-data-from-web-table/PowerQuery-Menu.png)
3. I den **från webben** dialogrutan Ange **URL** som du vill använda i länkad tjänst-JSON (till exempel: https://en.wikipedia.org/wiki/) tillsammans med sökvägen som du anger för datauppsättningen (till exempel: Afrika % 27s_ 100_Years... 100_Movies) och klicka på **OK**.

    ![Från webben dialogrutan](./media/copy-data-from-web-table/FromWeb-DialogBox.png)

    URL som används i det här exemplet: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Om du ser **åtkomst till webbinnehåll** dialogrutan Välj höger **URL**, **autentisering**, och klicka på **Anslut**.

   ![Åtkomst till innehåll dialogrutan för webbplats](./media/copy-data-from-web-table/AccessWebContentDialog.png)
5. Klicka på en **tabell** objekt i trädvyn finns innehåll från tabellen och klicka sedan på **redigera** längst ned.  

   ![Navigator dialogrutan](./media/copy-data-from-web-table/Navigator-DialogBox.png)
6. I den **frågeredigeraren** -fönstret klickar du på **avancerade Editor** i verktygsfältet.

    ![Knappen Avancerat redigeraren](./media/copy-data-from-web-table/QueryEditor-AdvancedEditorButton.png)
7. I dialogrutan Avancerad redigerare är talet bredvid ”källa” indexet.

    ![Avancerad redigerare - Index](./media/copy-data-from-web-table/AdvancedEditor-Index.png)

Om du använder Excel 2013 använder [Microsoft Power Query för Excel](https://www.microsoft.com/download/details.aspx?id=39379) att hämta index. Se [Anslut till en webbsida](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) artikeln för information. Stegen är ungefär som om du använder [Microsoft Power BI för skrivbordet](https://powerbi.microsoft.com/desktop/).


## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [stöds datalager](copy-activity-overview.md##supported-data-stores-and-formats).