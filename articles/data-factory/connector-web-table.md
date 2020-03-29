---
title: Kopiera data från webbtabell med Azure Data Factory
description: Lär dig mer om Web Table Connector för Azure Data Factory som gör att du kan kopiera data från en webbtabell till datalager som stöds av Data Factory som sänkor.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 76f0dbb48ca5e250a383e8427ce2dd0c9dd618c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930937"
---
# <a name="copy-data-from-web-table-by-using-azure-data-factory"></a>Kopiera data från webbtabell med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-web-table-connector.md)
> * [Aktuell version](connector-web-table.md)

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att kopiera data från en webbtabelldatabas. Den bygger på [kopian aktivitet översikt](copy-activity-overview.md) artikeln som presenterar en allmän översikt över kopieringsaktivitet.

Skillnaden mellan den här webbtabellkopplingen, [REST-kopplingen](connector-rest.md) och [HTTP-kopplingen](connector-http.md) är:

- **Webbbordskoppling** extraherar tabellinnehåll från en HTML-webbsida.
- **REST-anslutningsappen** har särskilt stöd för att kopiera data från RESTful API:er.
- **HTTP-anslutningsappen** är allmän för att hämta data från valfri HTTP-slutpunkt, t.ex. 

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här webbtabellkopplingen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från webbtabelldatabasen till alla sink-datalager som stöds. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)

Den här webbtabellkopplingen stöder **extrahering av tabellinnehåll från en HTML-sida**.

## <a name="prerequisites"></a>Krav

Om du vill använda den här webbtabellkopplingen måste du konfigurera en självvärderad integrationskörning. Mer information finns i artikeln Integration Runtime för [självvärd.](create-self-hosted-integration-runtime.md)

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera datafabrikentiteter som är specifika för webbtabellkopplingen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för webbtabelllänkade tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **Web** |Ja |
| url | URL till webbkällan |Ja |
| authenticationType | Tillåtet värde är: **Anonym**. |Ja |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. En självvärdad integrationskörning krävs som nämns i [Förutsättningar](#prerequisites). |Ja |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningsartikeln.](concepts-datasets-linked-services.md) Det här avsnittet innehåller en lista över egenskaper som stöds av webbtabelldatauppsättning.

Om du vill kopiera data från webbtabellen anger du egenskapen typ för datauppsättningen till **WebTable**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Datauppsättningens typegenskap måste anges till: **WebTable** | Ja |
| path |En relativ URL till resursen som innehåller tabellen. |Nej. När sökvägen inte anges används endast den URL som anges i den länkade tjänstdefinitionen. |
| Index |Indexet för tabellen i resursen. Se [Hämta index över en tabell i ett HTML-sidavsnitt](#get-index-of-a-table-in-an-html-page) för steg för att hämta index över en tabell på en HTML-sida. |Ja |

**Exempel:**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Web linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av webbtabellkällan.

### <a name="web-table-as-source"></a>Webbtabell som källa

Om du vill kopiera data från webbtabellen anger du källtypen i kopieringsaktiviteten till **WebSource**, inga ytterligare egenskaper stöds.

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

## <a name="get-index-of-a-table-in-an-html-page"></a>Hämta index över en tabell på en HTML-sida

Om du vill hämta indexet för en tabell som du behöver konfigurera i [datauppsättningsegenskaper](#dataset-properties)kan du använda t.ex.

1. Starta **Excel 2016** och växla till fliken **Data.**
2. Klicka på **Ny fråga** i verktygsfältet, peka på Från **andra källor** och klicka på Från **webben**.

    ![Menyn Power Query](./media/copy-data-from-web-table/PowerQuery-Menu.png)
3. I dialogrutan **Från webben** anger du **URL** som du skulle använda https://en.wikipedia.org/wiki/) i den länkade tjänsten JSON (till exempel: tillsammans med sökväg som du anger för datauppsättningen (till exempel AFI%27s_100_Years... 100_Movies) och klicka på **OK**.

    ![Dialogrutan Från webben](./media/copy-data-from-web-table/FromWeb-DialogBox.png)

    URL som används i det här exemplet:https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Om dialogrutan **Access-webbinnehåll** visas markerar du rätt **URL,** **autentisering**och klickar på **Anslut**.

   ![Dialogrutan Access-webbinnehåll](./media/copy-data-from-web-table/AccessWebContentDialog.png)
5. Klicka på ett **tabellobjekt** i trädvyn om du vill visa innehåll från tabellen och klicka sedan på **knappen Redigera** längst ned.  

   ![Navigeringsdialogruta](./media/copy-data-from-web-table/Navigator-DialogBox.png)
6. Klicka på knappen **Avancerad redigerare** i verktygsfältet i fönstret **Frågeredigeraren.**

    ![Knappen Avancerad redigerare](./media/copy-data-from-web-table/QueryEditor-AdvancedEditorButton.png)
7. I dialogrutan Avancerad redigerare är numret bredvid "Källa" indexet.

    ![Avancerad redigerare - Index](./media/copy-data-from-web-table/AdvancedEditor-Index.png)

Om du använder Excel 2013 använder du [Microsoft Power Query för Excel för](https://www.microsoft.com/download/details.aspx?id=39379) att hämta indexet. Mer information finns i Artikeln Anslut [till en webbsida.](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) Stegen är liknande om du använder [Microsoft Power BI för skrivbordet](https://powerbi.microsoft.com/desktop/).


## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
