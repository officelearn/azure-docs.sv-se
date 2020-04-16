---
title: Kopiera data från en HTTP-källa med hjälp av Azure Data Factory
description: Lär dig hur du kopierar data från en moln- eller lokal HTTP-källa till sink-datalager som stöds med hjälp av en kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 730efb552ef218cc5a5ce6a984d20b4e23b364ac
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416944"
---
# <a name="copy-data-from-an-http-endpoint-by-using-azure-data-factory"></a>Kopiera data från en HTTP-slutpunkt med hjälp av Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-http-connector.md)
> * [Aktuell version](connector-http.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs hur du använder Kopiera aktivitet i Azure Data Factory för att kopiera data från en HTTP-slutpunkt. Artikeln bygger på [Kopiera aktivitet i Azure Data Factory](copy-activity-overview.md), som presenterar en allmän översikt över kopiera aktivitet.

Skillnaden mellan den här HTTP-kopplingen, [REST-kopplingen](connector-rest.md) och [webbtabellkopplingen](connector-web-table.md) är:

- **REST-anslutningsappen** har särskilt stöd för kopiering av data från RESTful API:er. 
- **HTTP-anslutningsappen** är allmän för att hämta data från valfri HTTP-slutpunkt, t.ex. Innan REST-anslutning blir tillgänglig kan det hända att du använder HTTP-anslutningen för att kopiera data från RESTful API, som stöds men mindre funktionellt jämfört med REST-anslutningsappen.
- **Webbbordskoppling** extraherar tabellinnehåll från en HTML-webbsida.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här HTTP-kopplingen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från en HTTP-källa till alla sink-datalager som stöds. En lista över datalager som Kopierar aktivitet stöder som källor och sänkor finns i [Datalager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

Du kan använda den här HTTP-kopplingen för att:

- Hämta data från en HTTP/S-slutpunkt med hjälp av HTTP **GET-** eller **POST-metoderna.**
- Hämta data med någon av följande autentiseringar: **Anonym**, **Basic**, **Digest**, **Windows**eller **ClientCertificate**.
- Kopiera HTTP-svaret som det är eller tolka det med hjälp av [filformat och komprimeringskodicer som stöds](supported-file-formats-and-compression-codecs.md).

> [!TIP]
> Om du vill testa en HTTP-begäran om datahämtning innan du konfigurerar HTTP-anslutningen i Data Factory kan du läsa om API-specifikationen för huvud- och brödtextkrav. Du kan använda verktyg som Brevbärare eller en webbläsare för att validera.

## <a name="prerequisites"></a>Krav

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som du kan använda för att definiera datafabrikentiteter som är specifika för HTTP-kopplingen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för den HTTP-länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Egenskapen Type** måste anges till **HttpServer**. | Ja |
| url | Bas-URL:en till webbservern. | Ja |
| aktiveraServerCertificateValidation | Ange om server-TLS/SSL-certifikatvalidering ska aktiveras när du ansluter till en HTTP-slutpunkt. Om HTTPS-servern använder ett självsignerat certifikat anger du den här egenskapen till **false**. | Inga<br /> (standardvärdet är **sant)** |
| authenticationType | Anger autentiseringstypen. Tillåtna värden är **Anonym**, **Basic,** **Digest**, **Windows**och **ClientCertificate**. <br><br> Se avsnitten som följer den här tabellen för fler egenskaper och JSON-exempel för dessa autentiseringstyper. | Ja |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Läs mer från avsnittet [Förutsättningar.](#prerequisites) Om inget anges används standardkörningen för Azure Integration Runtime. |Inga |

### <a name="using-basic-digest-or-windows-authentication"></a>Använda autentisering av grundläggande, sammanfattning eller Windows

Ange egenskapen **authenticationType** till **Basic,** **Digest**eller **Windows**. Förutom de generiska egenskaper som beskrivs i föregående avsnitt anger du följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| userName | Det användarnamn som ska användas för att komma åt HTTP-slutpunkten. | Ja |
| password | Lösenordet för användaren **(värdet användarnamn).** Markera det här fältet som en **SecureString-typ** för att lagra det säkert i Data Factory. Du kan också [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |

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

### <a name="using-clientcertificate-authentication"></a>Använda clientcertificate-autentisering

Om du vill använda ClientCertificate-autentisering anger du egenskapen **authenticationType** till **ClientCertificate**. Förutom de generiska egenskaper som beskrivs i föregående avsnitt anger du följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| inbäddadeCertData | Base64-kodade certifikatdata. | Ange antingen **embeddedCertData** eller **certThumbprint**. |
| certThumbprint | Tumavtrycket för certifikatet som är installerat på din självvärderade Integration Runtime-dators cert-arkiv. Gäller endast när den självvärderade typen av Integration Runtime anges i egenskapen **connectVia.** | Ange antingen **embeddedCertData** eller **certThumbprint**. |
| password | Lösenordet som är associerat med certifikatet. Markera det här fältet som en **SecureString-typ** för att lagra det säkert i Data Factory. Du kan också [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Inga |

Om du använder **certThumbprint** för autentisering och certifikatet är installerat i den lokala datorns personliga arkiv beviljar du läsbehörighet till den självvärderade integrationskörningen:

1. Öppna Microsoft Management Console (MMC). Lägg till snapin-modulen **Certifikat** som är inriktad på **lokal dator**.
2. Expandera **Certifikat** > **Personal**och välj sedan **Certifikat**.
3. Högerklicka på certifikatet från det personliga arkivet och välj sedan **Alla uppgifter** > **hantera privata nycklar**.
3. Lägg till användarkontot som tjänsten Integration Runtime Host Service (DIAHostService) körs under på fliken **Säkerhet,** med läsbehörighet till certifikatet.

**Exempel 1: Använda certThumbprint**

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

**Exempel 2: Använda embeddedCertData**

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Datauppsättningar.](concepts-datasets-linked-services.md) 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för `location` HTTP under inställningar i formatbaserad datauppsättning:

| Egenskap    | Beskrivning                                                  | Krävs |
| ----------- | ------------------------------------------------------------ | -------- |
| typ        | Egenskapen type `location` under in dataset måste anges till **HttpServerLocation**. | Ja      |
| släktingUrl | En relativ URL till resursen som innehåller data. HTTP-kopplingen kopierar data från `[URL specified in linked service][relative URL specified in dataset]`den kombinerade URL:en: .   | Inga       |

> [!NOTE]
> Nyttolaststorleken för HTTP-begäran som stöds är cirka 500 kB. Om nyttolastens storlek som du vill skicka till webbslutpunkten är större än 500 kB kan du överväga att gruppera nyttolasten i mindre segment.

**Exempel:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HttpServerLocation",
                "relativeUrl": "<relative url>"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

Det här avsnittet innehåller en lista över egenskaper som HTTP-källan stöder.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md). 

### <a name="http-as-source"></a>HTTP som källa

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Följande egenskaper stöds för `storeSettings` HTTP under inställningar i formatbaserad kopieringskälla:

| Egenskap                 | Beskrivning                                                  | Krävs |
| ------------------------ | ------------------------------------------------------------ | -------- |
| typ                     | Egenskapen type `storeSettings` under måste anges till **HttpReadSettings**. | Ja      |
| begäranMethod            | HTTP-metoden. <br>Tillåtna värden är **Get** (standard) och **Bokför**. | Inga       |
| addtionalHeaders         | Ytterligare HTTP-begäranden.                             | Inga       |
| begäranKropp              | Brödtexten för HTTP-begäran.                               | Inga       |
| httpRequestTimeout (På)           | Timeout **(TimeSpan-värdet)** för HTTP-begäran för att få ett svar. Det här värdet är tidsgränsen för att få ett svar, inte timeout för att läsa svarsdata. Standardvärdet är **00:01:40**. | Inga       |
| maxConcurrentAnslutningar | Antalet anslutningar som ska anslutas till lagringsarkivet samtidigt. Ange bara när du vill begränsa den samtidiga anslutningen till datalagret. | Inga       |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "HttpReadSettings",
                    "requestMethod": "Post",
                    "additionalHeaders": "<header key: header value>\n<header key: header value>\n",
                    "requestBody": "<body for POST HTTP request>"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).

## <a name="legacy-models"></a>Äldre modeller

>[!NOTE]
>Följande modeller stöds fortfarande i sig för bakåtkompatibilitet. Du föreslås använda den nya modellen som nämns i ovanstående avsnitt framöver, och ADF-redigeringsgränssnittet har bytt till att generera den nya modellen.

### <a name="legacy-dataset-model"></a>Äldre datauppsättningsmodell

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen **Type** property för datauppsättningen måste anges till **HttpFile**. | Ja |
| släktingUrl | En relativ URL till resursen som innehåller data. När den här egenskapen inte anges används bara webbadressen som anges i den länkade tjänstdefinitionen. | Inga |
| begäranMethod | HTTP-metoden. Tillåtna värden är **Get** (standard) och **Bokför**. | Inga |
| additionalHeaders | Ytterligare HTTP-begäranden. | Inga |
| begäranKropp | Brödtexten för HTTP-begäran. | Inga |
| format | Om du vill hämta data från HTTP-slutpunkten som de är utan att tolka dem och sedan kopiera data till ett filbaserat arkiv hoppar du över **formatavsnittet** i både indata- och utdatauppsättningsdefinitionerna.<br/><br/>Om du vill tolka HTTP-svarsinnehållet under kopiering stöds följande filformattyper: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**och **ParquetFormat**. Under **format**anger du typegenskapen till ett av dessa värden. **type** Mer information finns i [JSON-format](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Textformat](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Avro-format](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc-format](supported-file-formats-and-compression-codecs-legacy.md#orc-format)och [Parkettformat](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). |Inga |
| komprimering | Ange typ och komprimeringsnivå för data. Mer information finns i [Filformat och komprimeringskodicer som stöds](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/><br/>Typer som stöds: **GZip**, **Deflate**, **BZip2**och **ZipDeflate**.<br/>Nivåer som stöds: **Optimal och** **snabbast**. |Inga |

> [!NOTE]
> Nyttolaststorleken för HTTP-begäran som stöds är cirka 500 kB. Om nyttolastens storlek som du vill skicka till webbslutpunkten är större än 500 kB kan du överväga att gruppera nyttolasten i mindre segment.

**Exempel 1: Använda metoden Hämta (standard)**

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

**Exempel 2: Använda metoden Post**

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

### <a name="legacy-copy-activity-source-model"></a>Källmodell för äldre kopieringsaktivitet

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | **Egenskapen Type** property för kopians aktivitetskälla måste anges till **HttpSource**. | Ja |
| httpRequestTimeout (På) | Timeout **(TimeSpan-värdet)** för HTTP-begäran för att få ett svar. Det här värdet är tidsgränsen för att få ett svar, inte timeout för att läsa svarsdata. Standardvärdet är **00:01:40**.  | Inga |

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

En lista över datalager som kopierar aktivitet stöder som källor och sänkor i Azure Data Factory finns [i Datalager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
