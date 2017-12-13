---
title: "Kopiera data från Presto med hjälp av Azure Data Factory (Beta) | Microsoft Docs"
description: "Lär dig hur du kopierar data från Presto till stöds sink datalager med hjälp av en kopia aktivitet i ett Azure Data Factory-pipelinen."
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
ms.date: 11/30/2017
ms.author: jingwang
ms.openlocfilehash: 80a0b0d1ef23dad55e2c2bb54825a8db3dfbbf1f
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2017
---
# <a name="copy-data-from-presto-using-azure-data-factory-beta"></a>Kopiera data från Presto med hjälp av Azure Data Factory (Beta)

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data från Presto. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Kopieringsaktiviteten i V1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Den här anslutningen är för närvarande i betaversion. Du kan prova och ge oss feedback. Använd den inte i produktionsmiljöer.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Presto till alla stöds sink-datalagret. En lista över datalager som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Azure Data Factory innehåller en inbyggd drivrutin att tillåta anslutningar, måste du därför inte att manuellt installera en drivrutin med den här anslutningen.

## <a name="getting-started"></a>Komma igång

Du kan skapa en pipeline med kopieringsaktiviteten använder .NET SDK, Python SDK, Azure PowerShell, REST-API eller Azure Resource Manager-mall. Se [kopiera aktivitet kursen](quickstart-create-data-factory-dot-net.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till Presto connector.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för Presto länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **Presto** | Ja |
| värden | IP-adressen eller värdnamnet namnet på servern Presto. (d.v.s. 192.168.222.160)  | Ja |
| serverVersion | Versionen av Presto server. (d.v.s. 0.148-t)  | Ja |
| katalog | Kontexten katalog för alla begäranden mot servern.  | Ja |
| port | TCP-porten som Presto servern använder för att lyssna efter anslutningar. Standardvärdet är 8080.  | Nej |
| AuthenticationType | Den autentiseringsmetod som används för att ansluta till servern Presto. <br/>Tillåtna värden är: **anonym**, **LDAP** | Ja |
| användarnamn | Användarnamnet som används för att ansluta till Presto-servern.  | Nej |
| lösenord | Lösenordet för användarnamnet. Du kan välja att markera det här fältet som en SecureString att lagra den säkert i ADF eller lagra lösenord i Azure Key Vault och låta kopieringsaktiviteten hämtar därifrån vid kopiering av data - mer information från [lagra autentiseringsuppgifter i Nyckelvalvet](store-credentials-in-key-vault.md). | Nej |
| enableSsl | Anger om anslutningar till servern krypteras med SSL. Standardvärdet är false.  | Nej |
| trustedCertPath | Den fullständiga sökvägen till PEM-filen som innehåller certifikat för betrodda Certifikatutfärdare för att verifiera servern när du ansluter via SSL. Den här egenskapen kan bara anges när du använder SSL på själva värdbaserade IR. Standardvärdet är filen cacerts.pem installeras med IR.  | Nej |
| useSystemTrustStore | Anger om du vill använda ett certifikat från arkivet med betrodda system eller från en angiven PEM-fil. Standardvärdet är false.  | Nej |
| allowHostNameCNMismatch | Anger om en Certifikatutfärdare som utfärdade SSL certifikatets namn att matcha värdnamn för servern när du ansluter via SSL. Standardvärdet är false.  | Nej |
| allowSelfSignedServerCert | Anger om självsignerade certifikat från servern. Standardvärdet är false.  | Nej |
| timeZoneID | Den lokala tidszon som används av anslutningen. Giltiga värden för det här alternativet har angetts i databasen med IANA tidszoner. Standardvärdet är datorns tidszon.  | Nej |

**Exempel:**

```json
{
    "name": "PrestoLinkedService",
    "properties": {
        "type": "Presto",
        "typeProperties": {
            "host" : "<host>",
            "serverVersion" : "0.148-t",
            "catalog" : "<catalog>",
            "port" : "<port>",
            "authenticationType" : "Anonymous",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            },
            "timeZoneID" : "Europe/Berlin"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns på [datauppsättningar](concepts-datasets-linked-services.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Presto dataset.

Ange typegenskapen för dataset för att kopiera data från Presto, **PrestoObject**. Det finns ingen ytterligare typspecifika egenskap i den här typen av datauppsättningen.

**Exempel**

```json
{
    "name": "PrestoDataset",
    "properties": {
        "type": "PrestoObject",
        "linkedServiceName": {
            "referenceName": "<Presto linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Presto källa.

### <a name="prestosource-as-source"></a>PrestoSource som källa

Om du vill kopiera data från Presto anger källa i kopieringsaktiviteten till **PrestoSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **PrestoSource** | Ja |
| DocumentDB | Använda anpassade SQL-frågan för att läsa data. Till exempel: `"SELECT * FROM MyTable"`. | Ja |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromPresto",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Presto input dataset name>",
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
                "type": "PrestoSource",
                "query": "SELECT * FROM MyTable"
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
