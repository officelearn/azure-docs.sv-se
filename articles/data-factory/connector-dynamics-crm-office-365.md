---
title: "Kopiera data från/till Dynamics CRM och 365 med Azure Data Factory | Microsoft Docs"
description: "Lär dig hur du kopierar data från Dynamics CRM och 365 till stöds sink datalager (eller) från datalager stöds källan till Dynamics CRM och 365 genom att använda en kopia aktivitet i ett Azure Data Factory-pipelinen."
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
ms.date: 01/05/2018
ms.author: jingwang
ms.openlocfilehash: b9b7091a8cb1de3eefcce77cbf82eedfcb33c787
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2018
---
# <a name="copy-data-fromto-dynamics-365dynamics-crm-using-azure-data-factory"></a>Kopiera data från/till Dynamics 365 / Dynamics CRM med hjälp av Azure Data Factory

Den här artikeln beskrivs hur du använder aktiviteten kopiera i Azure Data Factory för att kopiera data från och till Dynamics 365 / Dynamics CRM. Den bygger på den [kopiera aktivitet översikt](copy-activity-overview.md) artikel som presenterar en allmän översikt över kopieringsaktiviteten.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Kopieringsaktiviteten i V1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Dynamics 365 / Dynamics CRM till alla stöds sink-datalagret eller kopiera data från alla datalager stöds källa till Dynamics 365 / Dynamics CRM. En lista över datakällor som stöds som källor/sänkor av kopieringsaktiviteten, finns det [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Den här anslutningen Dynamics stöder nedan Dynamics versioner och typer av autentisering (*IFD är kort för Internet Facing Deployment*):

| Dynamics versioner | Typer av autentisering | Länkad tjänst-exempel |
|:--- |:--- |:--- |
| Dynamics 365 online <br> Dynamics CRM online | Office365 | [Dynamics Online + Office365 auth](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 lokalt med IFD <br> Dynamics CRM 2016 lokalt med IFD <br> Dynamics CRM 2015 lokalt med IFD | IFD | [Dynamics lokalt med IFD + IFD auth](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

För Dynamics 365 mer specifikt kan stöds följande programtyper:

- Dynamics 365 för försäljning
- Dynamics 365 för kundservice
- Dynamics 365 för fältet Service
- Dynamics 365 för projektet Service Automation
- Dynamics 365 marknadsföring

> [!NOTE]
> Om du vill använda Dynamics connector lagra lösenord i Azure Key Vault och kan kopiera aktivitet pull därifrån vid kopiering av data. Se hur du konfigurerar i [länkade tjänstegenskaper](#linked-service-properties) avsnitt.

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
| lösenord | Ange lösenordet för det användarkonto som du angav för användarnamnet. Du måste placera lösenordet i Azure Key Vault och konfigurera lösenord som ”AzureKeyVaultSecret”. Mer information från [lagra autentiseringsuppgifter i Nyckelvalvet](store-credentials-in-key-vault.md). | Ja |
| connectVia | Den [integrering Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Om inget anges används standard-Azure Integration Runtime. | Inte för källa och har Ja för sink om källa länkade tjänsten inte IR |

>[!IMPORTANT]
>När du kopierar data **till** Dynamics Azure Integration körning inte kan användas för att köra kopiera standard. I andra word om käll-länkad-tjänsten inte har en angiven IR explicit [skapa ett Azure-IR](create-azure-integration-runtime.md#create-azure-ir) med en plats nära din Dynamics och koppla i den länkade tjänsten Dynamics som i följande exempel.

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
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
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
| lösenord | Ange lösenordet för det användarkonto som du angav för användarnamnet. Observera att du måste placera lösenordet i Azure Key Vault och konfigurera lösenord som ”AzureKeyVaultSecret”. Mer information från [lagra autentiseringsuppgifter i Nyckelvalvet](store-credentials-in-key-vault.md). | Ja |
| connectVia | Den [integrering Runtime](concepts-integration-runtime.md) som används för att ansluta till datalagret. Om inget anges används standard-Azure Integration Runtime. | Ingen datakälla Ja för sink |

>[!IMPORTANT]
>Kopiera data till Dynamics, uttryckligen [skapa ett Azure-IR](create-azure-integration-runtime.md#create-azure-ir) med plats nära din Dynamics och koppla i den länkade tjänsten som i följande exempel.

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
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
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

Ange typegenskapen för dataset för att kopiera data från/till Dynamics, **DynamicsEntity**. Följande egenskaper stöds:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för dataset måste anges till: **DynamicsEntity** |Ja |
| EntityName | Det logiska namnet för att hämta entiteten. | Nej för källa (om ”fråga” i aktivitetskälla har angetts), Ja till mottagare |

> [!IMPORTANT]
>- **När du kopierar data från Dynamics avsnittet ”struktur” krävs** i Dynamics dataset som definierar kolumnen namn och datatyp för uppgifter som du vill kopiera över. Mer information från [datauppsättningsstrukturen](concepts-datasets-linked-services.md#dataset-structure) och [datatypsmappningen för Dynamics](#data-type-mapping-for-dynamics).
>- **När du kopierar data till Dynamics ”struktur”-avsnittet är valfritt** i Dynamics dataset. Vilka kolumner som ska kopieras till bestäms av dataschemat källa. Om datakällan är CSV-filen utan huvud, i datamängden inkommande ange ”strukturen” med kolumnen namn och datatyp som mappar till fält i CSV-fil i taget i ordning.

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md) artikel. Det här avsnittet innehåller en lista över egenskaper som stöds av Dynamics källa och mottagare.

### <a name="dynamics-as-source"></a>Dynamics som källa

Om du vill kopiera data från Dynamics anger källa i kopieringsaktiviteten till **DynamicsSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för aktiviteten kopieringskälla måste anges till: **DynamicsSource**  | Ja |
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

### <a name="dynamics-as-sink"></a>Dynamics som mottagare

Om du vill kopiera data till Dynamics anger sink i kopieringsaktiviteten till **DynamicsSink**. Följande egenskaper stöds i kopieringsaktiviteten **sink** avsnitt:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type för kopiera aktivitet sink måste anges till: **DynamicsSink**  | Ja |
| WriteBehavior | Åtgärden Skriv beteende.<br/>Tillåtna värde är: **”Upsert”**. | Ja |
| writeBatchSize | Antalet rader med data som skrivs till Dynamics i varje batch. | Nej (standardvärdet är 10) |
| ignoreNullValues | Anger om du vill ignorera null-värden från indata (utom nyckelfält) under skrivning.<br/>Tillåtna värden är: **SANT**, och **FALSKT**.<br>-värdet är sant: lämna data i målet objekt ändras när du gör upsert/uppdateringsåtgärden och infoga definierat standardvärde när gör insert-åtgärden.<br/>-FALSKT: uppdatera data i målobjektet till NULL när du gör upsert/uppdateringsåtgärden och infoga NULL-värde när du gör insert-åtgärden.  | Nej (standard är FALSKT) |

>[!NOTE]
>Standardvärdet för sink writeBatchSize och kopiera aktiviteten [parallelCopies](copy-activity-performance.md#parallel-copy) Dynamics sink är båda 10, vilket betyder 100 poster som skickas till Dynamics samtidigt.

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

När du kopierar data från Dynamics, används följande mappningar från Dynamics datatyper till Azure Data Factory tillfälliga datatyper. Se [Schema- och Skriv mappningar](copy-activity-schema-and-type-mapping.md) att lära dig hur kopieringsaktiviteten mappar källtypen schema och data till sink.

Konfigurera motsvarande Data Factory-datatypen i datauppsättningsstrukturen baserat på käll-Dynamics data skriva med mappningstabellen nedan:

| Dynamics-datatyp | Data factory tillfälliga datatyp | Stöds som källa | Stöds som mottagare |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | Lång | ✓ | ✓ |
| AttributeTypeCode.Boolean | Boolesk | ✓ | ✓ |
| AttributeType.Customer | GUID | ✓ |  |
| AttributeType.DateTime | DateTime | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | dubbla | ✓ | ✓ |
| AttributeType.EntityName | Sträng | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | GUID | ✓ |  |
| AttributeType.ManagedProperty | Boolesk | ✓ |  |
| AttributeType.Memo | Sträng | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ | ✓ |
| AttributeType.Owner | GUID | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | GUID | ✓ | ✓ |
| AttributeType.String | Sträng | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |


> [!NOTE]
> Datatypen för Dynamics AttributeType.CalendarRules och AttributeType.PartyList stöds inte.

## <a name="next-steps"></a>Nästa steg
En lista över datakällor som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats).