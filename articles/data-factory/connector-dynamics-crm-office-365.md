---
title: Kopiera data i Dynamics (Common Data Service)
description: Lär dig hur du kopierar data från Microsoft Dynamics CRM eller Microsoft Dynamics 365 (Common Data Service) till sink-datalager som stöds eller från källdatalager som stöds till Dynamics CRM eller Dynamics 365 med hjälp av en kopieringsaktivitet i en datafabrikspipeline.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 11/20/2019
ms.openlocfilehash: c891cb4eca2c286b3ac636e5995714accd591772
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417345"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Kopiera data från och till Dynamics 365 (Common Data Service) eller Dynamics CRM med hjälp av Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs hur du använder Kopiera aktivitet i Azure Data Factory för att kopiera data från och till Microsoft Dynamics 365 eller Microsoft Dynamics CRM. Den bygger på översiktsartikeln [Kopiera aktivitet](copy-activity-overview.md) som ger en allmän översikt över kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

Den här kopplingen stöds för följande aktiviteter:

- [Kopiera aktivitet](copy-activity-overview.md) med [käll-/sink-matris som stöds](copy-activity-overview.md)
- [Uppslagsaktivitet](control-flow-lookup-activity.md)

Du kan kopiera data från Dynamics 365 (Common Data Service) eller Dynamics CRM till alla sink-datalager som stöds. Du kan också kopiera data från alla källdatalager som stöds till Dynamics 365 (Common Data Service) eller Dynamics CRM. En lista över datalager som stöds som källor eller sänkor av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](copy-activity-overview.md#supported-data-stores-and-formats)

Den här Dynamics-kopplingen stöder Dynamics version 7.x till 9.x för både online eller lokalt. Mer specifikt är att

- Version 7.x kartor till Dynamics CRM 2015
- Version 8.x mappar till Dynamics CRM 2016 och den tidiga versionen av Dynamics 365
- Version 9.x mappar till den senare versionen av Dynamics 365

Se följande tabell över autentiseringstyper och konfigurationer som stöds för respektive Dynamics-versioner/-produkter. (IFD är en förkortning för internetbaserad distribution.)

| Dynamics-versioner | Autentiseringstyper | Exempel på länkade tjänster |
|:--- |:--- |:--- |
| Common Data Service <br> Dynamics 365 online <br> Dynamics CRM Online | AAD-tjänstens huvudnamn <br> Office365 | [Dynamics online + AAD-tjänsthuvudnamn eller Office365-auth](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 lokalt med IFD <br> Dynamics CRM 2016 lokalt med IFD <br> Dynamics CRM 2015 lokalt med IFD | IFD | [Dynamik lokalt med IFD + IFD auth](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

För Dynamics 365 specifikt stöds följande programtyper:

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

Andra applikationstyper t.ex.

Den här Dynamics-kopplingen är byggd ovanpå [Dynamics XRM-verktyg](https://docs.microsoft.com/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools).

>[!TIP]
>Om du vill kopiera data från **Dynamics 365 Finance and Operations**kan du använda Dynamics [AX-kopplingen](connector-dynamics-ax.md).

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

I följande avsnitt finns information om egenskaper som används för att definiera datafabrikentiteter som är specifika för Dynamics.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för den Dynamics-länkade tjänsten.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 och Dynamics CRM Online

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typegenskapen måste anges till **Dynamics,** **DynamicsCrm**eller **CommonDataServiceForApps**. | Ja |
| deploymentType (distributionstyp) | Distributionstypen för Dynamics-instansen. Det måste vara **"Online"** för Dynamics online. | Ja |
| serviceUri | Tjänst-URL:en för din Dynamics-instans, t.ex. `https://adfdynamics.crm.dynamics.com` | Ja |
| authenticationType | Autentiseringstypen för anslutning till en Dynamics-server. Tillåtna värden är: **AADServicePrincipal** eller **"Office365".** | Ja |
| servicePrincipalId | Ange Azure Active Directory-programmets klient-ID. | Ja när `AADServicePrincipal` du använder autentisering |
| servicePrincipalCredentialType | Ange vilken autentiseringstyp som ska användas för huvudautentisering av tjänsten. Tillåtna värden är: **ServicePrincipalKey** eller **ServicePrincipalCert**. | Ja när `AADServicePrincipal` du använder autentisering |
| servicePrincipalCredential | Ange tjänstens huvudautentiseringsuppgifter. <br>När `ServicePrincipalKey` du använder som autentiseringsuppgifter typ, `servicePrincipalCredential` kan vara en sträng (ADF kommer att kryptera den vid länkad tjänst distribution) eller en hänvisning till en hemlighet i AKV. <br>När `ServicePrincipalCert` du använder som `servicePrincipalCredential` autentiseringsuppgifter bör vara en referens till ett certifikat i AKV. | Ja när `AADServicePrincipal` du använder autentisering | 
| användarnamn | Ange vilket användarnamn som ska anslutas till Dynamics. | Ja när `Office365` du använder autentisering |
| password | Ange lösenordet för det användarkonto som du angav för användarnamnet. Markera det här fältet som en SecureString för att lagra det säkert i Data Factory, eller [referera till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). | Ja när `Office365` du använder autentisering |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Om det inte anges används standardkörningen för Azure Integration. | Nej för källa, Ja för mottagare om den källlänkade tjänsten inte har en integrationskörningstid |

>[!NOTE]
>Dynamics-kopplingen som används för att använda valfri egenskap "organizationName" för att identifiera din Dynamics CRM/365 Online-instans. Medan det fortsätter att fungera, föreslås du att ange den nya "serviceUri" egenskapen istället för att få bättre prestanda för till exempel identifiering.

**Exempel: Dynamics online med AAD-tjänstens huvudnamn + nyckelautentisering**

```json
{  
    "name": "DynamicsLinkedService",  
    "properties": {  
        "type": "Dynamics",  
        "typeProperties": {  
            "deploymentType": "Online",  
            "serviceUri": "https://adfdynamics.crm.dynamics.com",  
            "authenticationType": "AADServicePrincipal",  
            "servicePrincipalId": "<service principal id>",  
            "servicePrincipalCredentialType": "ServicePrincipalKey",  
            "servicePrincipalCredential": "<service principal key>"
        },  
        "connectVia": {  
            "referenceName": "<name of Integration Runtime>",  
            "type": "IntegrationRuntimeReference"  
        }  
    }  
}  
```
**Exempel: Dynamics online med AAD-tjänstens huvudnamn + certifikatautentisering**

```json
{ 
    "name": "DynamicsLinkedService", 
    "properties": { 
        "type": "Dynamics", 
        "typeProperties": { 
            "deploymentType": "Online", 
            "serviceUri": "https://adfdynamics.crm.dynamics.com", 
            "authenticationType": "AADServicePrincipal", 
            "servicePrincipalId": "<service principal id>", 
            "servicePrincipalCredentialType": "ServicePrincipalCert", 
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
            } 
        }, 
        "connectVia": { 
            "referenceName": "<name of Integration Runtime>", 
            "type": "IntegrationRuntimeReference" 
        } 
    } 
} 
```

**Exempel: Dynamics online med Office365-autentisering**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
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

*Ytterligare egenskaper som står sig i jämförelse med Dynamics online är "hostName" och "port".*

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Typegenskapen måste anges till **Dynamics,** **DynamicsCrm**eller **CommonDataServiceForApps**. | Ja |
| deploymentType (distributionstyp) | Distributionstypen för Dynamics-instansen. Det måste vara **"OnPremisesWithIfd"** för Dynamics lokalt med IFD.| Ja |
| Värdnamn | Värdnamnet på den lokala Dynamics-servern. | Ja |
| port | Porten på den lokala Dynamics-servern. | Nej, standard är 443 |
| Organisationsnamn | Organisationsnamnet för Dynamics-förekomsten. | Ja |
| authenticationType | Autentiseringstypen som ska anslutas till Dynamics-servern. Ange **"Ifd"** för Dynamics lokalt med IFD. | Ja |
| användarnamn | Ange vilket användarnamn som ska anslutas till Dynamics. | Ja |
| password | Ange lösenordet för det användarkonto som du angav för användarnamnet. Du kan välja att markera det här fältet som en SecureString för att lagra det säkert i ADF, eller lagra lösenord i Azure Key Vault och låta kopieringsaktiviteten hämta därifrån när du utför datakopiering - läs mer från [Store-autentiseringsuppgifter i Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia (på) | [Den integrationskörning som](concepts-integration-runtime.md) ska användas för att ansluta till datalagret. Om det inte anges används standardkörningen för Azure Integration. | Nej för källa, Ja för diskho |

**Exempel: Dynamik lokalt med IFD med IFD-autentisering**

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Datauppsättningar.](concepts-datasets-linked-services.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Dynamics-datauppsättning.

Om du vill kopiera data från och till Dynamics stöds följande egenskaper.

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för datauppsättningen måste anges till **DynamicsEntity**, **DynamicsCrmEntity**eller **CommonDataServiceForAppsEntity**. |Ja |
| Entityname | Det logiska namnet på entiteten som ska hämtas. | Nej för källa (om "fråga" i aktivitetskällan anges), Ja för diskho |

**Exempel:**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "schema": [],
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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Dynamics-käll- och diskbänkstyper.

### <a name="dynamics-as-a-source-type"></a>Dynamics som källtyp

Om du vill kopiera data från Dynamics stöds följande egenskaper i avsnittet kopiera **aktivitetskälla.**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för kopiaaktivitetskällan måste anges till **DynamicsSource**, **DynamicsCrmSource**eller **CommonDataServiceForAppsSource**. | Ja |
| DocumentDB | FetchXML är ett eget frågespråk som används i Dynamics (online och lokalt). Se följande exempel. Mer information finns i [Skapa frågor med FetchXML](https://msdn.microsoft.com/library/gg328332.aspx). | Nej (om "entityName" i datauppsättningen har angetts) |

>[!NOTE]
>PK-kolumnen kopieras alltid ut även om kolumnprojektionen som du konfigurerar i FetchXML-frågan inte innehåller den.

> [!IMPORTANT]
>- När du kopierar data från Dynamics är explicit kolumnmappning från Dynamics till diskho valfri men mycket återbefalld för att säkerställa ett deterministiskt kopieringsresultat.
>- När du importerar schema i redigeringsgränssnittet härleder ADF schemat genom att sampling av de översta raderna från Dynamics-frågeresultatet för att initiera källkolumnlistan, i vilket fall kolumner utan värden på de översta raderna utelämnas. Samma beteende gäller för kopieringskörningar om det inte finns någon explicit mappning. Du kan granska och lägga till fler kolumner i mappningen, som kommer att hedras under kopieringskörning.

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

### <a name="dynamics-as-a-sink-type"></a>Dynamics som en sink-typ

Om du vill kopiera data till Dynamics stöds följande egenskaper i avsnittet kopiera **aktivitetsmottagare.**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för copy activity sink måste anges till **DynamicsSink**, **DynamicsCrmSink**eller **CommonDataServiceForAppsSink**. | Ja |
| skriverBeteende | Skrivbeteendet för åtgärden.<br/>Tillåtet värde är **"Upsert".** | Ja |
| alternateKeyName | Ange det alternativa nyckelnamn som definierats på din entitet för att utföra "Upsert". | Inga |
| skriverBatchSize | Antalet rader av data som skrivits till Dynamics i varje batch. | Nej (standard är 10) |
| ignoreraNullValues | Anger om null-värden ska ignoreras från indata (utom nyckelfält) under en skrivåtgärd.<br/>Tillåtna värden är **sanna** och **falska**.<br>- **Sant**: Lämna data i målobjektet oförändrade när du gör en upsert/update-åtgärd. Infoga ett definierat standardvärde när du gör en infogad åtgärd.<br/>- **Falskt**: Uppdatera data i målobjektet till NULL när du utför en upsert/update-åtgärd. Infoga ett NULL-värde när du gör en infogad åtgärd. | Nej (standard är falskt) |

>[!NOTE]
>Standardvärdet för diskbänken "**writeBatchSize**" och kopieringsaktiviteten "**[parallelCopies](copy-activity-performance-features.md#parallel-copy)**" för Dynamics-diskbänken är båda 10. Därför skickas 100 poster till Dynamics samtidigt.

För Dynamics 365 online finns det en gräns [på 2 samtidiga batchanrop per organisation](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations). Om den gränsen överskrids genereras ett fel för server upptagen innan den första begäran någonsin körs. Att hålla "writeBatchSize" mindre eller lika med 10 skulle undvika sådan begränsning av samtidiga samtal.

Den optimala kombinationen av "**writeBatchSize**" och "**parallelCopies**" beror på schemat för din enhet, t.ex. Standardinställningen för 10 writeBatchSize * 10 parallelCopies är rekommendationen enligt Dynamics-tjänsten, som skulle fungera för de flesta Dynamics-entiteter men kanske inte är bästa prestanda. Du kan ställa in prestanda genom att justera kombinationen i inställningarna för kopieringsaktivitet.

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

## <a name="data-type-mapping-for-dynamics"></a>Mappning av datatyp för Dynamics

När du kopierar data från Dynamics används följande mappningar från Dynamics-datatyper till interimsdatatyper för Data Factory. Mer information om hur kopieringsaktiviteten mappar källschemat och datatypen till diskhon finns i [Schema- och datatypsmappningar](copy-activity-schema-and-type-mapping.md).

Konfigurera motsvarande datafabriksdatatyp i en datauppsättningsstruktur baserat på din källdynamikdatatyp med hjälp av följande mappningstabell.

| Datatyp för Dynamics | Data Factory interimdatatyp | Stöds som källa | Stöds som diskbänk |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | Lång | ✓ | ✓ |
| AttributeTypeCode.Boolean | Boolesk | ✓ | ✓ |
| AttributeType.Kund | GUID | ✓ | |
| AttributType.DateTime | Datumtid | ✓ | ✓ |
| Attributtyp.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributType.entitetsnamn | Sträng | ✓ | ✓ |
| Integer för AttributeType. | Int32 | ✓ | ✓ |
| AttributeType.Letaupp | GUID | ✓ | ✓ (med ett enda mål förknippat) |
| AttributeType.ManagedProperty | Boolesk | ✓ | |
| AttributType.MEMO | Sträng | ✓ | ✓ |
| AttributType.Money | Decimal | ✓ | ✓ |
| AttributeType.Owner | GUID | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | GUID | ✓ | ✓ |
| AttributType.String | Sträng | ✓ | ✓ |
| AttributType.Tillstånd | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |

> [!NOTE]
> Dynamics-datatyperna AttributeType.CalendarRules, AttributeType.MultiSelectPicklist och AttributeType.PartyList stöds inte.

## <a name="lookup-activity-properties"></a>Egenskaper för uppslagsaktivitet

Om du vill veta mer om egenskaperna kontrollerar du [uppslagsaktivitet](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Data Factory finns i [Datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
