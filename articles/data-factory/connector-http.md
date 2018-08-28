---
title: Kopiera data från HTTP-källan med Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från en källa för molnet eller lokalt HTTP till mottagarens datalager genom att använda en Kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/24/2018
ms.author: jingwang
ms.openlocfilehash: 5afb2fccd5c7b8ca306079941837d854c0b21349
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43091725"
---
# <a name="copy-data-from-http-endpoint-using-azure-data-factory"></a>Kopiera data från HTTP-slutpunkt med hjälp av Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-http-connector.md)
> * [Aktuell version](connector-http.md)

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från en HTTP-slutpunkt. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från HTTP-källan till alla datalager för mottagare som stöds. En lista över datalager som stöds som källor/mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här HTTP-anslutningen:

- Hämta data från HTTP/s-slutpunkten med hjälp av HTTP **hämta** eller **POST** metod.
- Hämtning av data med hjälp av följande autentiseringar: **anonym**, **grundläggande**, **sammanfattad**, **Windows**, och  **ClientCertificate**.
- Kopiera HTTP-svar som – är eller parsning den med den [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md).

Skillnaden mellan den här anslutningen och [tabell Webbanslutning](connector-web-table.md) är att det senare används för att extrahera innehåll från webb-HTML-sidan.

>[!TIP]
>Om du vill testa HTTP-begäran för data som hämtas innan du konfigurerar HTTP-anslutningsappen i ADF du lär dig från API-specifikationen på rubriken och brödtexten krav och använda verktyg som Postman eller en webbläsare för att verifiera.

## <a name="getting-started"></a>Komma igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för HTTP-anslutningsappen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för HTTP-länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till: **HTTP-servern**. | Ja |
| url | Grundläggande URL: en till webbservern | Ja |
| enableServerCertificateValidation | Ange om du vill aktivera serververifiering SSL-certifikat när du ansluter till HTTP-slutpunkt. När HTTPS-server använder självsignerade certifikat kan du ange det här till false. | Nej, standard är SANT |
| authenticationType | Anger autentiseringstypen. Tillåtna värden är: **anonym**, **grundläggande**, **sammanfattad**, **Windows**, **ClientCertificate**. <br><br> Avse respektive avsnitt under den här tabellen på fler egenskaper och JSON-exempel för dessa typer av autentisering. | Ja |
| connectVia | Den [Integration Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller lokal Integration Runtime (om ditt datalager finns i privat nätverk). Om den inte anges används standard Azure Integration Runtime. |Nej |

### <a name="using-basic-digest-or-windows-authentication"></a>Med hjälp av grundläggande, sammanfattad eller Windows-autentisering

Ange egenskapen ”authenticationType” som **grundläggande**, **sammanfattad**, eller **Windows**, och ange följande egenskaper tillsammans med allmänna egenskaper som beskrivs i föregående avsnitt:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| Användarnamn | Användarnamn för att få åtkomst till HTTP-slutpunkt. | Ja |
| lösenord | Lösenordet för användaren (användarnamn). Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |

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

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| embeddedCertData | Base64-kodat certifikatdata. | Ange antingen den `embeddedCertData` eller `certThumbprint`. |
| certThumbprint | Tumavtrycket för certifikatet som är installerad på lokal Integration Runtime-datorns certifikatarkiv. Gäller bara när lokal Integration Runtime har angetts i connectVia. | Ange antingen den `embeddedCertData` eller `certThumbprint`. |
| lösenord | Lösenordet som är associerat med certifikatet. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Nej |

Om du använder ”certThumbprint” för autentisering och certifikatet är installerat i det personliga arkivet i den lokala datorn, måste du bevilja läsbehörigheten till den lokala Integration Runtime:

1. Starta Microsoft Management Console (MMC). Lägg till den **certifikat** snapin-modul som riktar sig mot den **lokala**.
2. Expandera **certifikat**, **personliga**, och klicka på **certifikat**.
3. Högerklicka på certifikatet från det personliga arkivet och välj **alla uppgifter** -> **hantera privata nycklar...**
3. På den **Security** fliken, lägga till användarkontot som värdtjänsten för Integration Runtime (DIAHostService) körs under med läsbehörighet till certifikatet.

**Exempel 1: använda certThumbprint**

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

Om du vill kopiera data från HTTP, ange typegenskapen på datauppsättningen till **HttpFile**. Följande egenskaper stöds:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för datauppsättningen måste anges till: **HttpFile** | Ja |
| relativeUrl | En relativ URL till den resurs som innehåller data. När den här egenskapen inte anges används bara den URL som anges i länkade tjänstedefinition. | Nej |
| requestMethod | HTTP-metoden.<br/>Tillåtna värden är **hämta** (standard) eller **Post**. | Nej |
| additionalHeaders | Ytterligare rubriker för HTTP-begäran. | Nej |
| RequestBody | Brödtexten för HTTP-begäran. | Nej |
| Format | Om du vill **hämta data från HTTP-slutpunkt som – är** utan parsning av den och kopiera till en filbaserad butik, hoppa över avsnittet format i både inkommande och utgående datamängd definitioner.<br/><br/>Om du vill parsa HTTP-svarsinnehåll vid kopiering format för följande filtyper stöds: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Ange den **typ** egenskapen under format till ett av dessa värden. Mer information finns i [Json-Format](supported-file-formats-and-compression-codecs.md#json-format), [textformat](supported-file-formats-and-compression-codecs.md#text-format), [Avro-formatet](supported-file-formats-and-compression-codecs.md#avro-format), [Orc-Format](supported-file-formats-and-compression-codecs.md#orc-format), och [Parquet-Format](supported-file-formats-and-compression-codecs.md#parquet-format) avsnitt. |Nej |
| Komprimering | Ange typ och komprimeringsnivå för data. Mer information finns i [stöds filformat och komprimering codec](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Typer som stöds är: **GZip**, **Deflate**, **BZip2**, och **ZipDeflate**.<br/>Nivåer som stöds är: **Optimal** och **snabbast**. |Nej |

>[!NOTE]
>Maxstorleken för HTTP-begäran nyttolasten är cirka 500KB. Om den nyttolast som du vill skicka till din webbslutpunkt är större än så kan du till batch i mindre segment.

**Exempel 1: med hjälp av Get-metoden (standard)**

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av HTTP-källa.

### <a name="http-as-source"></a>HTTP som källa

För att kopiera data från HTTP, ange typ av datakälla i kopieringsaktiviteten till **HttpSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för aktiviteten kopieringskälla måste anges till: **HttpSource** | Ja |
| httpRequestTimeout | Tidsgräns (TimeSpan) för HTTP-begäran att få svar. Tidsgränsen är det för att få svar timeout inte att läsa svarsdata.<br/> Standardvärdet är: 00:01:40  | Nej |

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
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
