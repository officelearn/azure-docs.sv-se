---
title: Kopiera data från och till Dynamics CRM eller Dynamics 365 (Common Data Service) med hjälp av Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från Microsoft Dynamics CRM eller Microsoft Dynamics 365 (Common Data Service) till stöd för datalager för mottagare, eller från datalager som stöds källa till Dynamics CRM eller Dynamics 365 med en Kopieringsaktivitet i en data factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: jingwang
ms.openlocfilehash: 772b9b191a2e6464ff481ff6661308e00ef6033a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60535328"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Kopiera data från och till Dynamics 365 (Common Data Service) eller Dynamics CRM med hjälp av Azure Data Factory

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från och till Microsoft Dynamics 365 eller Microsoft Dynamics CRM. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Dynamics 365 (Common Data Service) eller Dynamics CRM till alla datalager för mottagare som stöds. Du kan också kopiera data från alla dataarkiv till Dynamics 365 (Common Data Service) eller Dynamics CRM. En lista över datalager som stöds som källor och mottagare av Kopieringsaktivitet finns i den [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) tabell.

Den här Dynamics-anslutningsappen stöder följande versioner av Dynamics och typer av autentisering. (IFD är kort för distribution av mot internet.)

| Dynamics-versioner | Autentiseringstyper | Länkad tjänst-exempel |
|:--- |:--- |:--- |
| Dynamics 365 online <br> Dynamics CRM Online | Office365 | [Dynamics online + Office365-autentisering](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 lokalt med IFD <br> Dynamics CRM 2016 lokalt med IFD <br> Dynamics CRM 2015 lokalt med IFD | IFD | [Dynamics lokalt med IFD + IFD auth](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

För Dynamics 365 mer specifikt kan stöds följande programtyper:

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 för marknadsföring

Andra programtyper t.ex. finans och åtgärder, personal, etc. stöds inte av den här anslutningen.

>[!TIP]
>Kopiera data från **Dynamics 365 Finance and Operations**, du kan använda den [Dynamics AX-anslutning](connector-dynamics-ax.md).

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Dynamics.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för den länkade tjänsten Dynamics.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 och Dynamics CRM Online

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till **Dynamics**. | Ja |
| deploymentType | Typen av distribution av Dynamics-instans. Det måste vara **”Online”** för Dynamics online. | Ja |
| serviceUri | Tjänst-URL: en för din Dynamics instans, t.ex. `https://adfdynamics.crm.dynamics.com`. | Ja |
| authenticationType | Autentiseringstyp för anslutning till en Dynamics-server. Ange **”Office365”** för Dynamics online. | Ja |
| användarnamn | Ange användarnamn för anslutning till Dynamics. | Ja |
| password | Ange lösenordet för det användarkonto som du angav för användarnamn. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. Om den inte anges används standard Azure Integration Runtime. | Inte för källa har Ja för kanalmottagare om källan länkade tjänsten inte en integration runtime |

>[!IMPORTANT]
>När du kopierar data till Dynamics kan standard Azure Integration Runtime inte användas till att köra kopia. Med andra ord om källan länkade tjänsten har inte en angiven integreringskörning uttryckligen [skapa en Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) med en plats nära din Dynamics-instans. Hitta var din Dynamics-instans finns genom att referera till den [regionlista för Dynamics 365](https://docs.microsoft.com/dynamics365/customer-engagement/admin/datacenter/new-datacenter-regions). Koppla den i den länkade tjänsten Dynamics som i följande exempel.

>[!NOTE]
>Dynamics-koppling som används för att använda valfritt ”företagsnamn”-egenskapen för att identifiera din Dynamics CRM/365 Online-instans. Medan det håller fungerar behöver föreslås du för att ange egenskapen ”serviceUri” i stället för att få bättre prestanda, till exempel identifiering.

**Exempel: Dynamics online med Office 365-autentisering**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics online linked service using Office365 authentication",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://adfdynamics.crm.dynamics.com",
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

*Ytterligare egenskaper som jämför till Dynamics online är ”värdnamnet” och ”port”.*

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till **Dynamics**. | Ja |
| deploymentType | Typen av distribution av Dynamics-instans. Det måste vara **”OnPremisesWithIfd”** för Dynamics lokalt med IFD.| Ja |
| hostName | Värdnamnet för den lokala Dynamics-servern. | Ja |
| port | Porten för den lokala Dynamics-servern. | Nej, standard är 443 |
| Organisationsnamn | Organisationens namn på Dynamics-instans. | Ja |
| authenticationType | Autentiseringstyp för anslutning till Dynamics-servern. Ange **”Ifd”** för Dynamics lokalt med IFD. | Ja |
| användarnamn | Ange användarnamn för anslutning till Dynamics. | Ja |
| password | Ange lösenordet för det användarkonto som du angav för användarnamn. Du kan välja att markera det här fältet som en SecureString att lagra den på ett säkert sätt i ADF eller lagra lösenord i Azure Key Vault och låta kopieringsaktiviteten hämta därifrån när du utför kopiering av data – Lär dig mer från [Store autentiseringsuppgifter i Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. Om den inte anges används standard Azure Integration Runtime. | Nej för källa, Ja för mottagare |

>[!IMPORTANT]
>Kopiera data till Dynamics, uttryckligen [skapa en Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) med plats nära din Dynamics-instans. Koppla den i den länkade tjänsten som i följande exempel.

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Dynamics-datauppsättningen.

För att kopiera data från och till Dynamics, ange typegenskapen på datauppsättningen till **DynamicsEntity**. Följande egenskaper stöds.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för datauppsättningen måste anges till **DynamicsEntity**. |Ja |
| entityName | Det logiska namnet för att hämta entiteten. | Nej för källa (om ”query” i källan för aktiviteten har angetts), Ja för mottagare |

> [!IMPORTANT]
>- När du kopierar data från Dynamics är ”struktur”-avsnittet valfritt men rekommenderat i Dynamics datauppsättningen så att en deterministisk Kopiera resultatet. Den definierar och datatyp för Dynamics-data som du vill kopiera över. Mer information finns i [datauppsättningsstrukturen](concepts-datasets-linked-services.md#dataset-structure) och [datatypsmappningen för Dynamics](#data-type-mapping-for-dynamics).
>- När du importerar schemat i redigering Användargränssnittet härleda ADF schemat genom att sampling de översta raderna från Dynamics frågeresultat att initiera struktur tillverkning, i vilket fall kolumner utan värden utelämnas. Du kan granska och lägga till fler kolumner i Dynamics datauppsättning/datastrukturen vid behov, vilket kommer att användas under kopiera körning.
>- När du kopierar data till Dynamics är ”struktur”-avsnittet valfritt i Dynamics-datauppsättningen. Vilka kolumner som ska kopieras till bestäms av dataschemat källa. Om källan är en CSV-fil utan rubrik som är i datauppsättningen för indata anger du ”strukturen” med namn och datatyp för kolumnen. De mappar till fält i CSV-fil i taget i ordning.

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
        "typeProperties": {
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Dynamics käll- och mottagar-typer.

### <a name="dynamics-as-a-source-type"></a>Dynamics som en typ av datakälla

För att kopiera data från Dynamics, ange typ av datakälla i kopieringsaktiviteten till **DynamicsSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för aktiviteten kopieringskälla måste anges till **DynamicsSource**. | Ja |
| DocumentDB | FetchXML är en upphovsrättsskyddad frågespråk som används i Dynamics (online och on-premises). Se följande exempel. Mer information finns i [skapa frågor med FeachXML](https://msdn.microsoft.com/library/gg328332.aspx). | Nej (om ”entityName” i datauppsättningen har angetts) |

>[!NOTE]
>Kolumnen PK kopieras alltid även om kolumnen projektionen som du konfigurerar i FetchXML-fråga inte innehåller den.

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

### <a name="sample-fetchxml-query"></a>Exemplet FetchXML-fråga

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

### <a name="dynamics-as-a-sink-type"></a>Dynamics som en Mottagartyp

För att kopiera data till Dynamics, ange Mottagartyp i kopieringsaktiviteten till **DynamicsSink**. Följande egenskaper stöds i kopieringsaktiviteten **mottagare** avsnittet.

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen type kopiera aktivitet komprimeringstyp måste anges till **DynamicsSink**. | Ja |
| writeBehavior | Åtgärden Skriv beteende.<br/>Tillåtna värde är **”Upsert”**. | Ja |
| writeBatchSize | Antal rader för data som skrivs till Dynamics i varje batch. | Nej (standardvärdet är 10) |
| ignoreNullValues | Anger om du vill ignorera null-värden från indata (utom nyckelfält) vid skrivning.<br/>Tillåtna värden är **SANT** och **FALSKT**.<br>- **True**: Lämna data i målobjektet oförändrade när du gör en upsert/uppdateringsåtgärd. Infoga ett definierat standardvärde när du gör en insert-åtgärd.<br/>- **False**: Uppdatera data i målobjektet till NULL när du gör en upsert/uppdateringsåtgärd. Infoga värdet NULL när du gör en insert-åtgärd. | Nej (standard är FALSKT) |

>[!NOTE]
>Standardvärdet för mottagaren ”**writeBatchSize**” och Kopieringsaktivitet ”**[parallelCopies](copy-activity-performance.md#parallel-copy)**” för Dynamics-mottagaren är båda 10. Därför skickas 100 poster till Dynamics samtidigt.

För Dynamics 365 online, det finns en gräns på [2 samtidiga batch-anrop per organisation](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations). Om den gränsen överskrids, genereras ett ”servern är upptagen” fel innan den första begäran någonsin har körts. Att hålla ”writeBatchSize” mindre än eller lika med 10 skulle undvika sådan begränsning för samtidiga anrop.

Optimal kombination av ”**writeBatchSize**” och ”**parallelCopies**” beror på schemat för entiteten t.ex. antalet kolumner, Radstorleken, antal plugin-program/arbetsflöden/arbetsflödesaktiviteter ansluten Dessa anrop, osv. Standardinställningen för 10 writeBatchSize * 10 parallelCopies är rekommendationen enligt Dynamics-tjänsten, som skulle fungera för de flesta Dynamics-entiteter inte kan dock vara bästa prestanda. Du kan finjustera prestanda genom att justera kombinationen i din inställningarna för kopieringsaktiviteten.

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

När du kopierar data från Dynamics, används följande mappningar från Dynamics-datatyper till Data Factory tillfälliga-datatyper. Läs hur kopieringsaktiviteten mappar källtypen schema och data till mottagaren i [Schema och data skriver mappningar](copy-activity-schema-and-type-mapping.md).

Konfigurera den motsvarande datatypen för Data Factory i en dataset-struktur baserat på källan Dynamics-datatyp med hjälp av mappningstabellen nedan.

| Dynamics-datatypen | Data Factory tillfälliga datatyp | Stöds som källa | Stöds som mottagare |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | Lång | ✓ | ✓ |
| AttributeTypeCode.Boolean | Boolean | ✓ | ✓ |
| AttributeType.Customer | Guid | ✓ | | 
| AttributeType.DateTime | DateTime | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | String | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | Guid | ✓ | ✓ (med enda mål som är associerade) |
| AttributeType.ManagedProperty | Boolean | ✓ | |
| AttributeType.Memo | String | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ | ✓ |
| AttributeType.Owner | Guid | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | Guid | ✓ | ✓ |
| AttributeType.String | String | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |


> [!NOTE]
> Dynamics-datatyper AttributeType.CalendarRules och AttributeType.PartyList stöds inte.

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
