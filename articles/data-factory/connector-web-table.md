---
title: Kopiera data från Webbtabell med Azure Data Factory | Microsoft Docs
description: Läs mer om Web tabell Connector för Azure Data Factory som låter dig kopieringsdata från en webbtabell till datalager som stöds av Data Factory som mottagare.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: e578b3a6b3905569567b568b0130c1ed1b90d915
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60557774"
---
# <a name="copy-data-from-web-table-by-using-azure-data-factory"></a>Kopiera data från Webbtabell med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-web-table-connector.md)
> * [Aktuell version](connector-web-table.md)

Den här artikeln beskriver hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från en databas för Web-tabellen. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

Skillnaden mellan den här tabellen Webbanslutning, den [REST-anslutningsapp](connector-rest.md) och [HTTP-anslutningsappen](connector-http.md) är:

- **Tabellen Webbanslutning** extraherar tabellen innehåll från en HTML-webbsidan.
- **REST-anslutningsapp** mer specifikt stöd som kopierar data från RESTful API: er.
- **HTTP-anslutningsappen** är generisk att hämta data från alla HTTP-slutpunkt, t.ex. att hämta filen. 

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Web Tabelldatabasen till alla datalager för mottagare som stöds. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här tabellen Webbanslutning **extraherar innehåll från en HTML-sida**.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill använda den här tabellen Webbanslutning, måste du konfigurera en lokal Integration Runtime. Se [lokal Integration Runtime](create-self-hosted-integration-runtime.md) nedan för information.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Webbanslutning för tabellen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för Web table länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen måste anges till: **Webb** |Ja |
| url | URL: en till webbadressen |Ja |
| authenticationType | Tillåtna värdet är: **Anonym**. |Ja |
| connectVia | Den [Integration Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. En lokal Integration Runtime krävs enligt [krav](#prerequisites). |Ja |

**Exempel:**

```json
{
    "name": "WebLinkedService",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "url" : "https://en.wikipedia.org/wiki/",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av Web-tabelldatauppsättning.

Kopiera data från Webbtabell genom att ange typegenskapen på datauppsättningen till **WebTable**. Följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Type-egenskapen för datauppsättningen måste anges till: **WebTable** | Ja |
| path |En relativ URL till den resurs som innehåller tabellen. |Nej. Om sökvägen inte anges används bara den URL som anges i länkade tjänstedefinition. |
| index |Index för tabellen i resursen. Se [Get index för en tabell i en HTML-sida](#get-index-of-a-table-in-an-html-page) avsnittet anvisningar för hur du hämtar index för en tabell i en HTML-sida. |Ja |

**Exempel:**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": {
            "referenceName": "<Web linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av tabellen webbadress.

### <a name="web-table-as-source"></a>Webbtabell som källa

För att kopiera data från Webbtabell, ange typ av datakälla i kopieringsaktiviteten till **WebSource**, inga ytterligare egenskaper som stöds.

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

Att hämta index för en tabell som du måste konfigurera i [egenskaper för datamängd](#dataset-properties), du kan använda t.ex. Excel 2016 som verktyget på följande sätt:

1. Starta **Excel 2016** och växla till den **Data** fliken.
2. Klicka på **ny fråga** i verktygsfältet, pekar på **från andra källor** och klicka på **från webben**.

    ![Power Query-menyn](./media/copy-data-from-web-table/PowerQuery-Menu.png)
3. I den **från webben** dialogrutan anger **URL** som du vill använda i länkad tjänst JSON (till exempel: https://en.wikipedia.org/wiki/) tillsammans med sökvägen som du anger för datauppsättningen (till exempel: Afrika % 27s_100_Years... 100_Movies), och klicka på **OK**.

    ![Web-dialogrutan](./media/copy-data-from-web-table/FromWeb-DialogBox.png)

    URL som används i det här exemplet: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Om du ser **åtkomst webbinnehåll** dialogrutan väljer du rätt **URL**, **autentisering**, och klicka på **Connect**.

   ![Dialogrutan för åtkomst till webb-innehåll](./media/copy-data-from-web-table/AccessWebContentDialog.png)
5. Klicka på en **tabell** objekt i trädvyn se innehåll från tabellen och klicka sedan på **redigera** längst ned.  

   ![Storleksändras](./media/copy-data-from-web-table/Navigator-DialogBox.png)
6. I den **frågeredigeraren** fönstret klickar du på **Avancerad redigerare** i verktygsfältet.

    ![Knappen Avancerat redigeringsprogram](./media/copy-data-from-web-table/QueryEditor-AdvancedEditorButton.png)
7. I dialogrutan Avancerad redigerare är talet bredvid ”källa” indexet.

    ![Avancerad redigerare - Index](./media/copy-data-from-web-table/AdvancedEditor-Index.png)

Om du använder Excel 2013 kan du använda [Microsoft Power Query för Excel](https://www.microsoft.com/download/details.aspx?id=39379) att hämta index. Se [Anslut till en webbsida](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) nedan för information. Stegen är liknande om du använder [Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/).


## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
