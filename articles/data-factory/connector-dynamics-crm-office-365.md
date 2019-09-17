---
title: Kopiera data från och till Dynamics CRM eller Dynamics 365 (Common Data Service) med hjälp av Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från Microsoft Dynamics CRM eller Microsoft Dynamics 365 (Common Data Service) till mottagar data lager som stöds eller från käll data lager som stöds till Dynamics CRM eller Dynamics 365 genom att använda en kopierings aktivitet i en Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: jingwang
ms.openlocfilehash: 18fdb14430eee97ff2780d963abf3e5ceafe1126
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/16/2019
ms.locfileid: "71009399"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Kopiera data från och till Dynamics 365 (Common Data Service) eller Dynamics CRM genom att använda Azure Data Factory

Den här artikeln beskriver hur du använder kopierings aktivitet i Azure Data Factory för att kopiera data från och till Microsoft Dynamics 365 eller Microsoft Dynamics CRM. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här anslutningen stöds för följande aktiviteter:

- [Kopierings aktivitet](copy-activity-overview.md) med [matrisen source/Sink som stöds](copy-activity-overview.md)
- [Sökningsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Dynamics 365 (Common Data Service) eller Dynamics CRM till alla mottagar data lager som stöds. Du kan också kopiera data från alla käll data lager som stöds till Dynamics 365 (Common Data Service) eller Dynamics CRM. En lista över data lager som stöds som källor eller handfat av kopierings aktiviteten finns i tabellen över [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) .

Den här Dynamics Connector stöder Dynamics version 7. x till 9. x för både online eller lokalt. Mer specifikt,

- Version 7. x mappar till Dynamics CRM 2015
- Version 8. x mappar till Dynamics CRM 2016 och den tidiga versionen av Dynamics 365
- Version 9. x mappar till den senare versionen av Dynamics 365

Se följande tabell på de autentiseringstyper och konfigurationer som stöds för respektive Dynamics-version/-produkter. (IFD är kort för distribution på Internet.)

| Dynamics-versioner | Autentiseringstyper | Länkade tjänst exempel |
|:--- |:--- |:--- |
| Dynamics 365 online <br> Dynamics CRM Online | Office365 | [Dynamics online + Office365-autentisering](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 lokalt med IFD <br> Dynamics CRM 2016 lokalt med IFD <br> Dynamics CRM 2015 lokalt med IFD | IFD | [Dynamics lokalt med IFD + IFD-autentisering](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

För Dynamics 365 är det specifikt att följande program typer stöds:

- Dynamics 365 för försäljning
- Dynamics 365 för kund tjänst
- Dynamics 365 for Field Service
- Dynamics 365 för Project service Automation
- Dynamics 365 för marknadsföring

Andra program typer, t. ex. ekonomi och drift, personal osv. stöds inte av den här anslutningen.

Den här Dynamics Connector bygger på [Dynamics XRM-verktyg](https://docs.microsoft.com/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools).

>[!TIP]
>Om du vill kopiera data från **ekonomi och åtgärder i dynamics 365**kan du använda [Dynamics AX-anslutningen](connector-dynamics-ax.md).

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory entiteter som är speciella för Dynamics.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för den länkade Dynamics-tjänsten.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 och Dynamics CRM Online

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Egenskapen Type måste anges till **Dynamics**. | Ja |
| deploymentType | Dynamics-instansens distributions typ. Det måste vara **"online"** för Dynamics Online. | Ja |
| serviceUri | Tjänst-URL för din Dynamics-instans, t `https://adfdynamics.crm.dynamics.com`. ex. | Ja |
| authenticationType | Autentiseringstypen för att ansluta till en Dynamics-Server. Ange **"Office365"** för Dynamics Online. | Ja |
| username | Ange användar namnet för att ansluta till Dynamics. | Ja |
| password | Ange lösen ordet för det användar konto som du har angett för användar namn. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory, eller [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. Om den inte anges används standard Azure Integration Runtime. | Nej för källa, Ja för mottagare om den länkade käll tjänsten inte har en integrerings körning |

>[!NOTE]
>Dynamics-anslutningen som används för att använda valfri "företags namn"-egenskap för att identifiera din Dynamics CRM/365 Online-instans. Medan det fungerar, rekommenderar vi att du anger den nya egenskapen "serviceUri" i stället för att få bättre prestanda för instans identifiering.

**Exempel: Dynamics Online med Office365-autentisering**

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

*Ytterligare egenskaper som jämförs med Dynamics Online är "värdnamn" och "Port".*

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Egenskapen Type måste anges till **Dynamics**. | Ja |
| deploymentType | Dynamics-instansens distributions typ. Det måste vara **"OnPremisesWithIfd"** för Dynamics-lokal med IFD.| Ja |
| Värdnamn | Värd namnet för den lokala Dynamics-servern. | Ja |
| port | Porten för den lokala Dynamics-servern. | Nej, standard är 443 |
| organizationName | Organisations namnet för Dynamics-instansen. | Ja |
| authenticationType | Autentiseringstypen för att ansluta till Dynamics-servern. Ange **"IFD"** för Dynamics lokalt med IFD. | Ja |
| username | Ange användar namnet för att ansluta till Dynamics. | Ja |
| password | Ange lösen ordet för det användar konto som du har angett för användar namn. Du kan välja att markera det här fältet som en SecureString för att lagra det på ett säkert sätt i ADF eller lagra lösen ord i Azure Key Vault och låta kopierings aktiviteten hämta från där när data kopieringen ska utföras – Läs mer från [lagra autentiseringsuppgifter i Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | Den [integreringskörningen](concepts-integration-runtime.md) som används för att ansluta till datalagret. Om den inte anges används standard Azure Integration Runtime. | Nej för källa, Ja för mottagare |

**Exempel: Dynamics lokalt med IFD med IFD-autentisering**

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Dynamics DataSet.

Om du vill kopiera data från och till Dynamics anger du egenskapen type för data uppsättningen till **DynamicsEntity**. Följande egenskaper stöds.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Data uppsättningens typ-egenskap måste anges till **DynamicsEntity**. |Ja |
| entityName | Det logiska namnet på den entitet som ska hämtas. | Nej för källa (om "fråga" i aktivitets källan har angetts), Ja för mottagare |

> [!IMPORTANT]
>- När du kopierar data från Dynamics är avsnittet "struktur" valfritt, men det är mycket omkommandoat i Dynamics-datauppsättningen för att säkerställa ett deterministiskt kopierings resultat. Den definierar kolumn namnet och data typen för de Dynamics-data som du vill kopiera. Mer information finns i [data uppsättnings struktur](concepts-datasets-linked-services.md#dataset-structure-or-schema) och [data typs mappning för Dynamics](#data-type-mapping-for-dynamics).
>- När du importerar schemat i redigerings gränssnittet, härleder ADF schemat genom att sampla de översta raderna från exemplet på Dynamics-frågeresultatet för att initiera struktur konstruktionen. i så fall kommer kolumner utan värden att utelämnas. Samma sak gäller för kopierings körningar om det inte finns någon explicit struktur definition. Du kan granska och lägga till fler kolumner i schemat/strukturen för Dynamics-datauppsättningen efter behov, vilket kommer att behållas under kopierings körningen.
>- När du kopierar data till Dynamics är avsnittet "struktur" valfritt i Dynamics-datauppsättningen. Vilka kolumner som ska kopieras till bestäms av käll data schemat. Om källan är en CSV-fil utan sidhuvud, anger du "struktur" med kolumn namn och datatyp i indata-datauppsättningen. De mappar till fält i CSV-filen en i ordning.

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av typerna Dynamics-källa och mottagare.

### <a name="dynamics-as-a-source-type"></a>Dynamics som typ av källa

Om du vill kopiera data från Dynamics anger du käll typen i kopierings aktiviteten till **DynamicsSource**. Följande egenskaper stöds i kopieringsaktiviteten **källa** avsnittet.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Typ egenskapen för kopierings aktivitets källan måste anges till **DynamicsSource**. | Ja |
| query | FetchXML är ett patentskyddat frågespråk som används i Dynamics (online och lokalt). Se följande exempel. Mer information finns i [Bygg frågor med FetchXML](https://msdn.microsoft.com/library/gg328332.aspx). | Nej (om "entityName" i data uppsättningen har angetts) |

>[!NOTE]
>Kolumnen PK kommer alltid att kopieras, även om den kolumn projektion som du konfigurerar i FetchXML-frågan inte innehåller den.

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

### <a name="sample-fetchxml-query"></a>Exempel på FetchXML-fråga

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

### <a name="dynamics-as-a-sink-type"></a>Dynamics som mottagar typ

Om du vill kopiera data till Dynamics ställer du in mottagar typen i kopierings aktiviteten till **DynamicsSink**. Följande egenskaper stöds i avsnittet Kopiera aktivitets **mottagare** .

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Egenskapen Type för kopierings aktivitetens Sink måste anges till **DynamicsSink**. | Ja |
| writeBehavior | Åtgärdens Skriv funktion.<br/>Tillåtet värde är **"upsert"** . | Ja |
| writeBatchSize | Rad antalet data som skrivs till Dynamics i varje batch. | Nej (standard är 10) |
| ignoreNullValues | Anger om null-värden ska ignoreras från indata (förutom nyckel fält) under en Skriv åtgärd.<br/>Tillåtna värden är **true** och **false**.<br>- **Sant**: Lämna data i målobjektet oförändrade när du gör en upsert/uppdatering-åtgärd. Infoga ett definierat standardvärde när du infogar en åtgärd.<br/>- **Falskt**: Uppdatera data i målobjektet till NULL när du gör en upsert/uppdatering-åtgärd. Infoga ett NULL-värde när du gör en infognings åtgärd. | Nej (standard är falskt) |

>[!NOTE]
>Standardvärdet för sinken "**writeBatchSize**" och kopierings aktiviteten " **[ParallelCopies](copy-activity-performance.md#parallel-copy)** " för Dynamics-Sink är båda 10. Därför skickas 100 poster till Dynamics samtidigt.

För Dynamics 365 online finns det en gräns på [2 samtidiga batch-anrop per organisation](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations). Om den här gränsen överskrids genereras ett "Server upptagen"-fel innan den första begäran någonsin körs. Att hålla "writeBatchSize" mindre eller lika med 10 skulle förhindra att samtidiga samtal begränsas.

Den optimala kombinationen av "**writeBatchSize**" och "**parallelCopies**" är beroende av schemat för entiteten, t. ex. antal kolumner, rad storlek, antal plugin-program/arbets flöden/arbets flödes aktiviteter som kopplats samman till dessa anrop osv. Standardinställningen 10 writeBatchSize * 10 parallelCopies är rekommendationen enligt Dynamics-tjänsten, som skulle fungera för de flesta Dynamics-entiteter, men det kanske inte är bästa prestanda. Du kan justera prestanda genom att justera kombinationen i inställningarna för kopierings aktiviteten.

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

## <a name="data-type-mapping-for-dynamics"></a>Data typs mappning för Dynamics

När du kopierar data från Dynamics används följande mappningar från data typer i Dynamics för att Data Factory interimistiska data typer. Information om hur kopierings aktiviteten mappar käll schema och data typ till mottagaren finns i schema- [och data typs mappningar](copy-activity-schema-and-type-mapping.md).

Konfigurera motsvarande Data Factory data typ i en data uppsättnings struktur baserat på data typen för din käll-Dynamics med hjälp av följande mappnings tabell.

| Data typen Dynamics | Data Factory tillfälliga datatyp | Stöds som källa | Stöds som mottagare |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | Lång | ✓ | ✓ |
| AttributeTypeCode. Boolean | Boolesk | ✓ | ✓ |
| AttributeType. kund | Guid | ✓ | |
| AttributeType. DateTime | DateTime | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType. Double | Double | ✓ | ✓ |
| AttributeType. EntityName | Sträng | ✓ | ✓ |
| AttributeType. Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | Guid | ✓ | ✓ (med associerat enskilt mål) |
| AttributeType.ManagedProperty | Boolesk | ✓ | |
| AttributeType.Memo | Sträng | ✓ | ✓ |
| AttributeType. Money | Decimal | ✓ | ✓ |
| AttributeType. owner | Guid | ✓ | |
| AttributeType. listruta | Int32 | ✓ | ✓ |
| AttributeType. uniqueidentifier | Guid | ✓ | ✓ |
| AttributeType. String | Sträng | ✓ | ✓ |
| AttributeType. State | Int32 | ✓ | ✓ |
| AttributeType. status | Int32 | ✓ | ✓ |

> [!NOTE]
> Det finns inte stöd för Dynamics data typerna AttributeType. CalendarRules, AttributeType. MultiSelectPicklist och AttributeType. PartyList.

## <a name="lookup-activity-properties"></a>Egenskaper för Sök aktivitet

Om du vill veta mer om egenskaperna kontrollerar du [söknings aktiviteten](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
