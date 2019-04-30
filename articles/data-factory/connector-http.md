---
title: Kopiera data från en HTTP-källa med hjälp av Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från en källa för molnet eller lokalt HTTP till mottagarens datalager genom att använda en Kopieringsaktivitet i en Azure Data Factory-pipeline.
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
ms.openlocfilehash: 87505081f16008dff7da1f567c1265c695f3f0ab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60653780"
---
# <a name="copy-data-from-an-http-endpoint-by-using-azure-data-factory"></a>Kopiera data från en HTTP-slutpunkt med hjälp av Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-http-connector.md)
> * [Aktuell version](connector-http.md)

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från en HTTP-slutpunkt. Artikeln bygger vidare på [Kopieringsaktivitet i Azure Data Factory](copy-activity-overview.md), som anger en allmän översikt över Kopieringsaktivitet.

Skillnaden mellan den här HTTP-anslutningen i [REST-anslutningsapp](connector-rest.md) och [tabell Webbanslutning](connector-web-table.md) är:

- **REST-anslutningsapp** mer specifikt stöd som kopierar data från RESTful API: er; 
- **HTTP-anslutningsappen** är generisk att hämta data från alla HTTP-slutpunkt, t.ex. att hämta filen. Innan REST-anslutningsapp blir tillgänglig, kan du råkar använda HTTP-anslutningen för att kopiera data från RESTful-API, vilket är stöds men mindre funktionella jämföra för REST-anslutningsapp.
- **Tabellen Webbanslutning** extraherar tabellen innehåll från en HTML-webbsidan.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från en HTTP-källa till alla datalager för mottagare som stöds. En lista över data lagrar att det stöder Kopieringsaktiviteten som källor och mottagare, finns i [datalager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

Du kan använda den här HTTP-anslutningen till:

- Hämta data från en HTTP/S-slutpunkt med hjälp av HTTP **hämta** eller **POST** metoder.
- Hämta data med någon av följande autentiseringar: **Anonym**, **grundläggande**, **sammanfattad**, **Windows**, eller **ClientCertificate**.
- Kopiera HTTP-svar som – är eller parsa den med hjälp av [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md).

> [!TIP]
> Läs mer om API-specifikationen för rubriken och brödtexten krav för att testa en HTTP-begäran för datahämtning innan du konfigurerar HTTP-anslutningen i Data Factory. Du kan använda verktyg som Postman eller en webbläsare för att verifiera.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som du kan använda för att definiera Data Factory-entiteter som är specifika för HTTP-anslutningsappen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för HTTP-länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Den **typ** egenskapen måste anges till **HTTP-servern**. | Ja |
| url | Den grundläggande Webbadressen till webbservern. | Ja |
| enableServerCertificateValidation | Ange om du vill aktivera serververifiering SSL-certifikat när du ansluter till en HTTP-slutpunkt. Om din HTTPS-servern använder ett självsignerat certifikat, ange egenskapen till **FALSKT**. | Nej<br /> (standardvärdet är **SANT**) |
| authenticationType | Anger autentiseringstypen. Tillåtna värden är **anonym**, **grundläggande**, **sammanfattad**, **Windows**, och **ClientCertificate**. <br><br> Se avsnitten som följer den här tabellen för mer egenskaper och JSON-exempel för dessa typer av autentisering. | Ja |
| connectVia | Den [Integreringskörningen](concepts-integration-runtime.md) för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller en lokal Integration Runtime (om ditt datalager finns i ett privat nätverk). Om den inte anges används den här egenskapen standard Azure Integration Runtime. |Nej |

### <a name="using-basic-digest-or-windows-authentication"></a>Med hjälp av grundläggande, sammanfattad eller Windows-autentisering

Ange den **authenticationType** egenskap **grundläggande**, **sammanfattad**, eller **Windows**. Förutom de allmänna egenskaper som beskrivs i föregående avsnitt, anger du följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| userName | Användarnamnet du använder för att få åtkomst till HTTP-slutpunkt. | Ja |
| password | Lösenordet för användaren (den **användarnamn** värde). Markera det här fältet som en **SecureString** Skriv för att lagra den på ett säkert sätt i Data Factory. Du kan också [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |

**Exempel**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<HTTP endpoint>",
            "userName": "<user name>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-clientcertificate-authentication"></a>Med hjälp av ClientCertificate autentisering

Om du vill använda ClientCertificate autentisering, ange den **authenticationType** egenskap **ClientCertificate**. Förutom de allmänna egenskaper som beskrivs i föregående avsnitt, anger du följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| embeddedCertData | Base64-kodat certifikatdata. | Ange antingen **embeddedCertData** eller **certThumbprint**. |
| certThumbprint | Tumavtrycket för certifikatet som är installerad på din egen värd Integration Runtime-datorns certifikatarkiv. Gäller endast när typen lokal installation av Integration Runtime har angetts i den **connectVia** egenskapen. | Ange antingen **embeddedCertData** eller **certThumbprint**. |
| password | Lösenordet som är associerat med certifikatet. Markera det här fältet som en **SecureString** Skriv för att lagra den på ett säkert sätt i Data Factory. Du kan också [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Nej |

Om du använder **certThumbprint** för autentisering och certifikatet är installerat i det personliga arkivet i den lokala datorn kan bevilja läsbehörighet till den lokal Integration Runtime:

1. Öppna Microsoft Management Console (MMC). Lägg till den **certifikat** snapin-modul som riktar sig mot **lokala**.
2. Expandera **certifikat** > **personliga**, och välj sedan **certifikat**.
3. Högerklicka på certifikatet från det personliga arkivet och välj sedan **alla uppgifter** > **hantera privata nycklar**.
3. På den **Security** fliken, lägga till användarkontot som värdtjänsten för Integration Runtime (DIAHostService) körs under, med läsbehörighet till certifikatet.

**Exempel 1: Med hjälp av certThumbprint**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "certThumbprint": "<thumbprint of certificate>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Exempel 2: Med hjälp av embeddedCertData**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "embeddedCertData": "<Base64-encoded cert data>",
            "password": {
                "type": "SecureString",
                "value": "password of cert"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

Det här avsnittet innehåller en lista över egenskaper som har stöd för HTTP-datauppsättningen. 

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningar och länkade tjänster](concepts-datasets-linked-services.md). 

Om du vill kopiera data från HTTP, ange den **typ** egenskapen på datauppsättningen till **HttpFile**. Följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Den **typ** egenskap måste anges till **HttpFile**. | Ja |
| relativeUrl | En relativ URL till den resurs som innehåller data. När den här egenskapen har inte angetts används bara den URL som anges i länkade tjänstedefinition. | Nej |
| requestMethod | HTTP-metoden. Tillåtna värden är **hämta** (standard) och **Post**. | Nej |
| additionalHeaders | Ytterligare rubriker för HTTP-begäran. | Nej |
| requestBody | Brödtexten för HTTP-begäran. | Nej |
| Format | Om du vill hämta data från HTTP-slutpunkt som – är utan parsning och kopiera sedan data till ett filbaserade lager, hoppa över den **format** avsnittet i både inkommande och utgående datamängd definitionerna.<br/><br/>Om du vill parsa HTTP-svarsinnehåll vid kopiering stöds format för följande filtyper: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, och **ParquetFormat**. Under **format**, ange den **typ** egenskap enligt en av dessa värden. Mer information finns i [JSON-format](supported-file-formats-and-compression-codecs.md#json-format), [textformat](supported-file-formats-and-compression-codecs.md#text-format), [Avro-formatet](supported-file-formats-and-compression-codecs.md#avro-format), [Orc-format](supported-file-formats-and-compression-codecs.md#orc-format), och [Parquet-format](supported-file-formats-and-compression-codecs.md#parquet-format). |Nej |
| Komprimering | Ange typ och komprimeringsnivå för data. Mer information finns i [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md#compression-support).<br/><br/>Typer som stöds: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**.<br/>Stöds nivåer:  **Optimal** och **snabbaste**. |Nej |

> [!NOTE]
> Maxstorleken för HTTP-begäran nyttolasten är cirka 500 KB. Om den nyttolast som du vill skicka till din webbslutpunkt är större än 500 KB, Överväg batchbearbetning nyttolast i mindre segment.

**Exempel 1: Med Get-metoden (standard)**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        }
    }
}
```

**Exempel 2: Med hjälp av metoden Post**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "requestMethod": "Post",
            "requestBody": "<body for POST HTTP request>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

Det här avsnittet innehåller en lista över egenskaper som har stöd för HTTP-källa.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md). 

### <a name="http-as-source"></a>HTTP som källa

Om du vill kopiera data från HTTP, ange **källtyp** i kopieringsaktiviteten till **HttpSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Den **typ** egenskapen för aktiviteten kopieringskälla måste anges till **HttpSource**. | Ja |
| httpRequestTimeout | Timeout (i **TimeSpan** värde) för HTTP-begäran att få svar. Det här värdet är tidsgränsen för att få svar timeout inte att läsa svarsdata. Standardvärdet är **00:01:40**.  | Nej |

**Exempel**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<HTTP input dataset name>",
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
                "type": "HttpSource",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```


## <a name="next-steps"></a>Nästa steg

En lista över datalager som Kopieringsaktiviteten som källor och egenskaperna i Azure Data Factory finns i [datalager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
