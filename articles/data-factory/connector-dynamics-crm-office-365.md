---
title: "Kopiera data från och till Dynamics CRM eller Dynamics 365 med hjälp av Azure Data Factory | Microsoft Docs"
description: "Lär dig att kopiera data från Microsoft Dynamics CRM eller Microsoft Dynamics 365 till stöd för sink datalager eller från stöd för datalager för källan till Dynamics CRM eller Dynamics 365 genom att använda en kopia aktivitet i en data factory-pipelinen."
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
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: b00c594f87a3126bd3f1548cd904adffcb214031
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="copy-data-from-and-to-dynamics-365-or-dynamics-crm-by-using-azure-data-factory"></a>Kopiera data från och till Dynamics 365 eller Dynamics CRM med hjälp av Azure Data Factory

Den här artikeln beskrivs hur du använder Kopieringsaktiviteten i Azure Data Factory för att kopiera data från och till Microsoft Dynamics 365 eller Microsoft Dynamics CRM. Den bygger på den [Kopieringsaktiviteten översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över Kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory som är allmänt tillgänglig, se [Kopieringsaktiviteten i version 1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Dynamics 365 eller Dynamics CRM till alla stöds sink-datalagret. Du kan också kopiera data från alla datalager stöds källa till Dynamics 365 eller Dynamics CRM. En lista över datakällor som stöds som datakällor eller sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Den här anslutningen Dynamics stöder följande versioner av Dynamics och typer av autentisering. (IFD är för kort för distribution av mot internet.)

| Dynamics versioner | Autentiseringstyper | Länkad tjänst-exempel |
|:--- |:--- |:--- |
| Dynamics 365 online <br> Dynamics CRM Online | Office365 | [Dynamics online + Office365 auth](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 lokalt med IFD <br> Dynamics CRM 2016 lokalt med IFD <br> Dynamics CRM 2015 lokalt med IFD | IFD | [Dynamics lokalt med IFD + IFD auth](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

För Dynamics 365 mer specifikt kan stöds följande programtyper:

- Dynamics 365 för försäljning
- Dynamics 365 för kundservice
- Dynamics 365 för fältet Service
- Dynamics 365 för projektet Service Automation
- Dynamics 365 marknadsföring

Andra program typer t.ex. Operations och ekonomi, användare, etc. stöds inte.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter till Dynamics.

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper

Följande egenskaper har stöd för den länkade tjänsten Dynamics.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 och Dynamics CRM Online

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till **Dynamics**. | Ja |
| deploymentType | Typen av distribution av Dynamics-instans. Det måste vara **”Online”** för Dynamics online. | Ja |
| Organisationsnamn | Organisationsnamn Dynamics-instans. | Nej, ska anges om det finns fler än en Dynamics instanser som är associerade med användaren |
| AuthenticationType | Autentiseringstypen som ansluter till en Dynamics-server. Ange **”Office365”** för Dynamics online. | Ja |
| användarnamn | Ange användarnamnet för att ansluta till Dynamics. | Ja |
| lösenord | Ange lösenordet för det användarkonto som du angett för användarnamn. Markera det här fältet som en SecureString lagra den på ett säkert sätt i Data Factory eller [referera en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | Den [integrering runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Om inget anges används standard-Azure Integration Runtime. | Inte för källa och har Ja för sink om källan länkade tjänsten inte en integration körning |

>[!IMPORTANT]
>Standard Azure Integration Runtime kan inte användas för att köra kopia när du kopierar data till Dynamics. Med andra ord om käll-länkad-tjänsten inte har en angiven integration runtime explicit [skapa en Azure Integration körning](create-azure-integration-runtime.md#create-azure-ir) med en plats nära din Dynamics-instans. Koppla den i den länkade tjänsten Dynamics som i följande exempel.

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

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 och Dynamics CRM lokalt med IFD

*Ytterligare egenskaper som jämför med Dynamics online är ”värdnamnet” och ””.*

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type måste anges till **Dynamics**. | Ja |
| deploymentType | Typen av distribution av Dynamics-instans. Det måste vara **”OnPremisesWithIfd”** för Dynamics lokalt med IFD.| Ja |
| hostName | Värdnamnet på den lokala Dynamics servern. | Ja |
| port | Porten för lokal Dynamics server. | Nej, standard är 443 |
| Organisationsnamn | Organisationsnamn Dynamics-instans. | Ja |
| AuthenticationType | Autentiseringstypen att ansluta till Dynamics-servern. Ange **”Ifd”** för Dynamics lokalt med IFD. | Ja |
| användarnamn | Ange användarnamnet för att ansluta till Dynamics. | Ja |
| lösenord | Ange lösenordet för det användarkonto som du angett för användarnamn. Du kan välja att markera det här fältet som en SecureString att lagra den säkert i ADF eller lagra lösenord i Azure Key Vault och låta kopieringsaktiviteten hämtar därifrån vid kopiering av data - mer information från [lagra autentiseringsuppgifter i Nyckelvalvet](store-credentials-in-key-vault.md). | Ja |
| connectVia | Den [integrering runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Om inget anges används standard-Azure Integration Runtime. | Ingen datakälla Ja för sink |

>[!IMPORTANT]
>Kopiera data till Dynamics, uttryckligen [skapa en Azure Integration körning](create-azure-integration-runtime.md#create-azure-ir) med platsen nära din Dynamics-instans. Koppla den i den länkade tjänsten som i följande exempel.

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

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns på [datauppsättningar](concepts-datasets-linked-services.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Dynamics dataset.

Ange typegenskapen för dataset för att kopiera data från och till Dynamics, **DynamicsEntity**. Följande egenskaper stöds.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för dataset måste anges till **DynamicsEntity**. |Ja |
| EntityName | Det logiska namnet för att hämta entiteten. | Nej för källa (om ”fråga” i aktivitetskälla har angetts), Ja till mottagare |

> [!IMPORTANT]
>- När du kopierar data från Dynamics krävs avsnittet ”struktur” i Dynamics dataset. Den definierar namn och datatyp för uppgifter som du vill kopiera över. Läs mer i [datauppsättningsstrukturen](concepts-datasets-linked-services.md#dataset-structure) och [datatypsmappningen för Dynamics](#data-type-mapping-for-dynamics).
>- När du kopierar data till Dynamics är avsnittet ”struktur” valfritt i Dynamics dataset. Vilka kolumner som ska kopieras till bestäms källa dataschemat. Om datakällan är en CSV-fil utan rubrik som är i den inkommande datamängden ange ”strukturen” med kolumnen namn och datatyp. De mappas till fält i CSV-fil i taget i ordning.

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Dynamics källa och mottagare typer.

### <a name="dynamics-as-a-source-type"></a>Dynamics som typ av datakälla

Om du vill kopiera data från Dynamics anger källa i kopieringsaktiviteten till **DynamicsSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till **DynamicsSource**. | Ja |
| DocumentDB | FetchXML är en upphovsrättsskyddad frågespråk som används i Dynamics (online och on-premises). Se följande exempel. Läs mer i [bygga frågor med FeachXML](https://msdn.microsoft.com/en-us/library/gg328332.aspx). | Nej (om ”entityName” i datamängden har angetts) |

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

### <a name="dynamics-as-a-sink-type"></a>Dynamics som en Mottagartypen

Om du vill kopiera data till Dynamics anger sink i kopieringsaktiviteten till **DynamicsSink**. Följande egenskaper stöds i kopieringsaktiviteten **sink** avsnitt.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för kopiera aktivitet sink måste anges till **DynamicsSink**. | Ja |
| WriteBehavior | Åtgärden Skriv beteende.<br/>Tillåtna värde är **”Upsert”**. | Ja |
| writeBatchSize | Antalet rader med data som skrivs till Dynamics i varje batch. | Nej (standardvärdet är 10) |
| ignoreNullValues | Anger om du vill ignorera null-värden från indata (utom nyckelfält) vid skrivning.<br/>Tillåtna värden är **SANT** och **FALSKT**.<br>- **SANT**: ändra data i målobjektet inte när du gör en upsert/update-åtgärd. Infoga ett definierat standardvärde när du gör en infogning.<br/>- **FALSKT**: uppdatera data i målobjektet till NULL när du gör en upsert/update-åtgärd. Infoga värdet NULL när du gör en infogning. | Nej (standard är FALSKT) |

>[!NOTE]
>Standardvärdet för sink-writeBatchSize och kopieringsaktiviteten [parallelCopies](copy-activity-performance.md#parallel-copy) för Dynamics sink är båda 10. Därför skickas 100 poster till Dynamics samtidigt.

**Exempel:**

```json
"activities":[
    {
        "name": "CopyToDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Dynamics output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DynamicsSink",
                "writeBehavior": "Upsert",
                "writeBatchSize": 10,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="data-type-mapping-for-dynamics"></a>Datatypen mappning för Dynamics

När du kopierar data från Dynamics används följande mappningar från Dynamics datatyper för Data Factory tillfälliga datatyper. Information om hur kopieringsaktiviteten mappar källtypen schema och data till sink finns [Schema- och Skriv mappningar](copy-activity-schema-and-type-mapping.md).

Konfigurera motsvarande Data Factory-datatypen i en dataset-struktur baserat på käll-Dynamics-datatyp med hjälp av följande mappningstabellen.

| Dynamics-datatyp | Data Factory tillfälliga datatyp | Stöds som källa | Stöds som mottagare |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | Lång | ✓ | ✓ |
| AttributeTypeCode.Boolean | Boolesk | ✓ | ✓ |
| AttributeType.DateTime | DateTime | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | Dubbel | ✓ | ✓ |
| AttributeType.EntityName | Sträng | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | GUID | ✓ | |
| AttributeType.ManagedProperty | Boolesk | ✓ | |
| AttributeType.Memo | Sträng | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ | ✓ |
| AttributeType.Owner | GUID | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | GUID | ✓ | ✓ |
| AttributeType.String | Sträng | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |


> [!NOTE]
> Datatyperna Dynamics AttributeType.CalendarRules och AttributeType.PartyList stöds inte.

## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Data Factory finns [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats).
