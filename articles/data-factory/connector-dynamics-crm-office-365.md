---
title: "Kopiera data från Dynamics CRM och 365 med Azure Data Factory | Microsoft Docs"
description: "Lär dig hur du kopierar data från Dynamics CRM och 365 till stöds sink datalager med hjälp av en kopia aktivitet i ett Azure Data Factory-pipelinen."
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
ms.date: 11/02/2017
ms.author: jingwang
ms.openlocfilehash: 1af330596052a92237469aba4729474e7fe417aa
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
---
# <a name="copy-data-from-dynamics-365dynamics-crm-using-azure-data-factory"></a>Kopiera data från Dynamics 365 / Dynamics CRM med hjälp av Azure Data Factory

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data från Dynamics 365 / Dynamics CRM. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Kopieringsaktiviteten i V1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Dynamics 365 / Dynamics CRM till alla stöds sink-datalagret. En lista över datakällor som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Mer specifikt stöder den här anslutningen Dynamics nedan Dynamics versioner och typer av autentisering:

| Dynamics versioner | Typer av autentisering | Länkad tjänst-exempel |
|:--- |:--- |:--- |
| Dynamics 365 online <br> Dynamics CRM online | Office 365 | [Dynamics Online + Office365 auth](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 lokalt med IFD <br> Dynamics CRM 2016 lokalt med IFD <br> Dynamics CRM 2015 lokalt med IFD | IFD | [Dynamics lokalt med IFD + IFD auth](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

*IFD är kort för Internet Facing Deployment.*

> [!NOTE]
> Om du vill använda Dynamics connector lagra lösenord i Azure Key Vault och låt ADF kopiera aktivitet pull därifrån vid kopiering av data. Se hur du konfigurerar i [länkade tjänstegenskaper](#linked-service-properties) avsnitt.

## <a name="getting-started"></a>Komma igång

Du kan skapa en pipeline med kopieringsaktiviteten använder .NET SDK, Python SDK, Azure PowerShell, REST-API eller Azure Resource Manager-mall. Se [kopiera aktivitet kursen](quickstart-create-data-factory-dot-net.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet.

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till Dynamics.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper stöds för Dynamics länkade tjänsten:

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 och Dynamics CRM Online

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **Dynamics**. | Ja |
| deploymentType | Typen av distribution av Dynamics-instans. Måste vara **”Online”** för Dynamics Online. | Ja |
| Organisationsnamn | Organisationsnamn Dynamics-instans. | Nej, ska anges om det finns fler än en Dynamics instanser som är associerade med användaren. |
| AuthenticationType | Autentiseringstypen att ansluta till Dynamics-servern. Ange **”Office365”** för Dynamics Online. | Ja |
| användarnamn | Ange användarnamn för att ansluta till dynamiska. | Ja |
| lösenord | Ange lösenordet för det användarkonto som du angav för användarnamnet. Du måste placera lösenordet i Azure Key Vault och konfigurera lösenord som ”AzureKeyVaultSecret”. Mer information finns i [lagra autentiseringsuppgifter i Nyckelvalvet](store-credentials-in-key-vault.md) artikel. | Ja |

**Exempel: Dynamics online med Office 365-autentisering**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics online linked service using Office365 authentication",
        "typeProperties": {
            "deploymentType": "Online",
            "organizationName": "orga02d9c75",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "mySecret",
                "store":{
                    "linkedServiceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 och Dynamics CRM lokalt med IFD

*Ytterligare egenskaper att jämföra Dyanmics online är ”värdnamnet” och ””.*

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **Dynamics**. | Ja |
| deploymentType | Typen av distribution av Dynamics-instans. Måste vara **”OnPremisesWithIfd”** för Dynamics lokalt med IFD.| Ja |
| **Värdnamn** | Värdnamnet för lokal Dynamics server. | Ja |
| **port** | Porten för lokal Dynamics server. | Nej, standard är 443 |
| Organisationsnamn | Organisationsnamn Dynamics-instans. | Ja |
| AuthenticationType | Autentiseringstypen att ansluta till Dynamics-servern. Ange **”Ifd”** för Dynamics lokalt med IFD. | Ja |
| användarnamn | Ange användarnamn för att ansluta till dynamiska. | Ja |
| lösenord | Ange lösenordet för det användarkonto som du angav för användarnamnet. Observera att du måste placera lösenordet i Azure Key Vault och konfigurera lösenord som ”AzureKeyVaultSecret”. Mer information finns i [lagra autentiseringsuppgifter i Nyckelvalvet](store-credentials-in-key-vault.md) artikel. | Ja |

**Exempel: Dynamics lokalt med IFD med IFD autentisering**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "mySecret",
                "store":{
                    "linkedServiceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns på [datauppsättningar](concepts-datasets-linked-services.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Dynamics dataset.

Ange egenskapen type för datauppsättningen till för att kopiera data från Dynamics **DynamicsEntity**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för dataset måste anges till: **DynamicsEntity** |Ja |
| EntityName | Det logiska namnet för att hämta entiteten. | Nej (om ”fråga” i aktivitetskälla har angetts) |

> [!IMPORTANT]
> **Avsnittet ”struktur” i dataset krävs för Dynamics**, som definierar kolumnen namn och datatyp för uppgifter som du vill kopiera över. Mer information från [datauppsättningsstrukturen](concepts-datasets-linked-services.md#dataset-structure) och [datatypsmappningen för Dynamics](#data-type-mapping-for-dynamics).

**Exempel:**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "structure": [
            {
                "name": "accountid",
                "type": "Guid"
            },
            {
                "name": "name",
                "type": "String"
            },
            {
                "name": "marketingonly",
                "type": "Boolean"
            },
            {
                "name": "modifiedon",
                "type": "Datetime"
            }
        ],
        "typePoperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Dynamics källa.

### <a name="dynamics-as-source"></a>Dynamics som källa

Om du vill kopiera data från Dynamics anger källa i kopieringsaktiviteten till **DynamicsSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till: **DynamicsSource**  | Ja |
| DocumentDB  | FetchXML är en upphovsrättsskyddad frågespråk som används i Microsoft Dynamics (online & lokalt). Se exemplet nedan och Läs mer från [bygga frågor med FeachXML](https://msdn.microsoft.com/en-us/library/gg328332.aspx). | Nej (om ”entityName” i datamängden har angetts)  |

**Exempel:**

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>FetchXML exempelfråga

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

## <a name="data-type-mapping-for-dynamics"></a>Datatypen mappning för Dynamics

När du kopierar data från Dynamics, används följande mappningar från Dynamics datatyper till Azure Data Factory tillfälliga datatyper. Se [Schema- och Skriv mappningar](copy-activity-schema-and-type-mapping.md) att lära dig hur kopieringsaktiviteten mappar källtypen schema och data till sink.

Konfigurera motsvarande ADF-datatypen i datauppsättningsstrukturen baserat på käll-Dynamics data skriva med mappningstabellen nedan:

| Dynamics-datatyp | Data factory tillfälliga datatyp |
|:--- |:--- |
| AttributeTypeCode.BigInt | Lång |
| AttributeTypeCode.Boolean | Booleskt värde |
| AttributeType.Customer | GUID |
| AttributeType.DateTime | Datum och tid |
| AttributeType.Decimal | Decimal |
| AttributeType.Double | dubbla |
| AttributeType.EntityName | Sträng |
| AttributeType.Integer | Int32 |
| AttributeType.Lookup | GUID |
| AttributeType.ManagedProperty | Booleskt värde |
| AttributeType.Memo | Sträng |
| AttributeType.Money | Decimal |
| AttributeType.Owner | GUID |
| AttributeType.Picklist | Int32 |
| AttributeType.Uniqueidentifier | GUID |
| AttributeType.String | Sträng |
| AttributeType.State | Int32 |
| AttributeType.Status | Int32 |

> [!NOTE]
> Datatypen för Dynamics AttributeType.CalendarRules och AttributeType.PartyList stöds inte.


## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [stöds datalager](copy-activity-overview.md##supported-data-stores-and-formats).