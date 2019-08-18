---
title: Kopiera data från en webb tabell med Azure Data Factory | Microsoft Docs
description: Lär dig mer om webb tabell koppling till Azure Data Factory som gör att du kan kopiera data från en webb tabell till data lager som stöds av Data Factory som mottagare.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 09de2919b22ebb088b23c1ab59f60d182657a2f1
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68720414"
---
# <a name="copy-data-from-web-table-by-using-azure-data-factory"></a>Kopiera data från en webb tabell med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-web-table-connector.md)
> * [Aktuell version](connector-web-table.md)

Den här artikeln beskriver hur du använder kopierings aktiviteten i Azure Data Factory för att kopiera data från en webb tabell databas. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

Skillnaden mellan den här webb tabell kopplingen, [rest-anslutningen](connector-rest.md) och [http-anslutningen](connector-http.md) är:

- **Webb tabells koppling** extraherar tabell innehåll från en HTML-webbsida.
- **Rest Connector** har stöd för att kopiera data från RESTful-API: er.
- **Http-anslutningen** är generisk för att hämta data från alla http-slutpunkter, t. ex. för att hämta filen. 

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från webb tabell databasen till alla mottagar data lager som stöds. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här webb tabell anslutningen **extrahering av tabell innehåll från en HTML-sida**.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda den här webb tabell anslutningen måste du konfigurera en egen värd Integration Runtime. Se [lokal Integration Runtime](create-self-hosted-integration-runtime.md) nedan för information.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för webb tabell koppling.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för länkad webb tabell tjänst:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Egenskapen Type måste anges till: **Webb** |Ja |
| url | URL till webb adressen |Ja |
| authenticationType | Tillåtet värde är: **Anonymous**. |Ja |
| connectVia | Den [Integration Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Det krävs en egen värd Integration Runtime som anges i [krav](#prerequisites). |Ja |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av webb tabell data uppsättning.

Om du vill kopiera data från en webb tabell anger du egenskapen type för datauppsättningen till webtable. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Data uppsättningens typ-egenskap måste anges till: **WebTable** | Ja |
| path |En relativ URL till den resurs som innehåller tabellen. |Nej. Om ingen sökväg anges används endast den URL som angavs i den länkade tjänst definitionen. |
| index |Indexet för tabellen i resursen. Se avsnittet [Hämta index för en tabell i en HTML-sida](#get-index-of-a-table-in-an-html-page) för steg för att hämta index för en tabell på en HTML-sida. |Ja |

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av webb tabellens källa.

### <a name="web-table-as-source"></a>Webb tabell som källa

Om du vill kopiera data från en webb tabell anger du käll typen i kopieringsaktiviteten till Websource. inga ytterligare egenskaper stöds.

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

Om du vill hämta indexet för en tabell som du behöver konfigurera i [data uppsättnings egenskaper](#dataset-properties)kan du använda t. ex. Excel 2016 som verktyg:

1. Starta **Excel 2016** och växla till fliken **data** .
2. Klicka på **ny fråga** i verktygsfältet, peka på **från andra källor** och klicka på **från webben**.

    ![Power Query-menyn](./media/copy-data-from-web-table/PowerQuery-Menu.png)
3. I dialog rutan **från webben** anger du den **URL** som du skulle använda i länkad tjänst-JSON (till https://en.wikipedia.org/wiki/) exempel: tillsammans med sökvägen som du anger för data uppsättningen (till exempel: AFI%27s_100_Years... 100_Movies) och klicka på **OK**.

    ![Från webben-dialog](./media/copy-data-from-web-table/FromWeb-DialogBox.png)

    URL som används i det här exemplet: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Om dialog rutan **Öppna webb innehåll** visas väljer du rätt **URL**, **autentisering**och klickar på **Anslut**.

   ![Dialog rutan för att komma åt webb innehåll](./media/copy-data-from-web-table/AccessWebContentDialog.png)
5. Klicka på ett **tabell** objekt i trädvyn om du vill visa innehåll från tabellen och klicka sedan på knappen **Redigera** längst ned.  

   ![Navigerings dialog ruta](./media/copy-data-from-web-table/Navigator-DialogBox.png)
6. I fönstret **Frågeredigeraren** klickar du på **avancerad redigerare** knapp i verktygsfältet.

    ![Avancerad redigerare knapp](./media/copy-data-from-web-table/QueryEditor-AdvancedEditorButton.png)
7. I dialog rutan Avancerad redigerare är talet intill "källa" indexet.

    ![Avancerad redigerare-index](./media/copy-data-from-web-table/AdvancedEditor-Index.png)

Om du använder Excel 2013 använder du [Microsoft Power Query för Excel](https://www.microsoft.com/download/details.aspx?id=39379) för att hämta indexet. Mer information finns i artikeln om [att ansluta till en webb sida](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) . Stegen är liknande om du använder [Microsoft Power BI för skriv bord](https://powerbi.microsoft.com/desktop/).


## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
