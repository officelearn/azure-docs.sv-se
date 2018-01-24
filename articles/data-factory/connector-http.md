---
title: "Kopiera data från http-datakälla med hjälp av Azure Data Factory | Microsoft Docs"
description: "Lär dig hur du kopierar data från en källa för molnet eller lokalt HTTP till stöds sink datalager med hjälp av en kopia aktivitet i ett Azure Data Factory-pipelinen."
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
ms.date: 01/10/2018
ms.author: jingwang
ms.openlocfilehash: 232b9bed1ea719dfb76d639bc8d5274551cdab6f
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2018
---
# <a name="copy-data-from-http-endpoint-using-azure-data-factory"></a>Kopiera data från http-slutpunkten med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-http-connector.md)
> * [Version 2 – förhandsversion](connector-http.md)

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data från en HTTP-slutpunkt. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [HTTP-anslutningen i V1](v1/data-factory-http-connector.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från http-källa till alla stöds sink-datalagret. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här HTTP-anslutningen:

- Hämtar data från HTTP/s-slutpunkten med hjälp av HTTP **hämta** eller **POST** metod.
- Hämtning av data med hjälp av följande autentiseringar: **anonym**, **grundläggande**, **sammanfattad**, **Windows**, och  **ClientCertificate**.
- Kopiera HTTP-svar som-är eller parsning av den med den [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md).

Skillnaden mellan den här kopplingen och [Web tabell connector](connector-web-table.md) är att denna används för att extrahera innehållet från webbsidan HTML.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till HTTP-anslutningen.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för HTTP-länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **HTTP-servern**. | Ja |
| url | Bas-URL till webbservern | Ja |
| enableServerCertificateValidation | Ange om du vill aktivera server SSL-certifikatsverifiering vid anslutning till HTTP-slutpunkten. | Nej, standard är SANT |
| AuthenticationType | Anger vilken autentiseringstyp. Tillåtna värden är: **anonym**, **grundläggande**, **sammanfattad**, **Windows**, **ClientCertificate**. <br><br> Avse respektive avsnitt under den här tabellen på fler egenskaper och JSON-exempel för dessa typer av autentisering. | Ja |
| connectVia | Den [integrering Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller Self-hosted integrering Runtime (om datalager finns i privat nätverk). Om inget anges används standard-Azure Integration Runtime. |Nej |

### <a name="using-basic-digest-or-windows-authentication"></a>Med hjälp av grundläggande, sammanfattad eller Windows-autentisering

Egenskapen ”authenticationType” **grundläggande**, **sammanfattad**, eller **Windows**, och ange följande egenskaper tillsammans med allmänna egenskaper som beskrivs i föregående avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| userName | Användarnamnet för åtkomst av HTTP-slutpunkten. | Ja |
| lösenord | Lösenord för användare (användarnamn). Markera det här fältet som SecureString. | Ja |

**Exempel**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<HTTP endpoint>",
            "userName": "<username>",
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

Om du vill använda ClientCertificate-autentisering för egenskapen ”authenticationType” **ClientCertificate**, och ange följande egenskaper tillsammans med de allmänna egenskaper som beskrivs i föregående avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| embeddedCertData | Base64-kodat certifikatdata. | Ange antingen det `embeddedCertData` eller `certThumbprint`. |
| certThumbprint | Tumavtrycket för certifikatet som är installerad på datorn Self-hosted integrering Runtime certifikatarkivet. Gäller endast när automatisk värdbaserade Integration Runtime har angetts i connectVia. | Ange antingen det `embeddedCertData` eller `certThumbprint`. |
| lösenord | Lösenordet för certifikatet. Markera det här fältet som SecureString. | Nej |

Om du använder ”certThumbprint” för autentisering och certifikatet är installerat i det personliga arkivet i den lokala datorn, måste du bevilja läsbehörigheten till Self-hosted integrering körning:

1. Starta Microsoft Management Console (MMC). Lägg till den **certifikat** snapin-modulen som riktar sig den **lokal dator**.
2. Expandera **certifikat**, **personliga**, och klicka på **certifikat**.
3. Högerklicka på certifikatet från det personliga arkivet och välj **alla aktiviteter** -> **hantera privata nycklar...**
3. På den **säkerhet** Lägg till användarkontot som Integration Runtime Host-tjänsten (DIAHostService) körs under med läsbehörighet till certifikatet.

**Exempel 1: använder certThumbprint**

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

**Exempel 2: använda embeddedCertData**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "embeddedCertData": "<base64 encoded cert data>",
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln datauppsättningar. Det här avsnittet innehåller en lista över egenskaper som stöds av HTTP-datauppsättningen.

Ange egenskapen type för datauppsättningen till för att kopiera data från HTTP **HttpFile**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för dataset måste anges till: **HttpFile** | Ja |
| relativeUrl | En relativ URL till den resurs som innehåller data. När den här egenskapen inte anges används den URL som angavs i definitionen länkade tjänsten. | Nej |
| requestMethod | HTTP-metod.<br/>Tillåtna värden är **hämta** (standard) eller **efter**. | Nej |
| additionalHeaders | Ytterligare HTTP-begärans sidhuvud. | Nej |
| requestBody | Brödtext för HTTP-begäran. | Nej |
| format | Om du vill **hämta data från HTTP-slutpunkt som-är** utan parsning den och kopiera till en filbaserad lagring, hoppa över avsnittet format i både inkommande och utgående dataset-definitioner.<br/><br/>Om du vill att parsa innehållet i HTTP-svar vid kopiering format för följande filtyper stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange den **typen** egenskap under format till ett av dessa värden. Mer information finns i [Json-Format](supported-file-formats-and-compression-codecs.md#json-format), [textformat](supported-file-formats-and-compression-codecs.md#text-format), [Avro-formatet](supported-file-formats-and-compression-codecs.md#avro-format), [Orc Format](supported-file-formats-and-compression-codecs.md#orc-format), och [parkettgolv Format](supported-file-formats-and-compression-codecs.md#parquet-format) avsnitt. |Nej |
| Komprimering | Ange typ och kompression för data. Mer information finns i [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**.<br/>Nivåer som stöds är: **Optimal** och **snabbast**. |Nej |

**Exempel 1: använder Get-metoden (standard)**

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

**Exempel 2: använda Post-metoden**

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av HTTP-källa.

### <a name="http-as-source"></a>HTTP som källa

Om du vill kopiera data från HTTP, anger du källa i kopieringsaktiviteten till **HttpSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **HttpSource** | Ja |
| httpRequestTimeout | Tidsgräns (TimeSpan) för HTTP-begäran att få svar. Tidsgränsen är det inget svar timeout inte att läsa svarsdata.<br/> Standardvärdet är: 00:01:40  | Nej |

**Exempel:**

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
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats).