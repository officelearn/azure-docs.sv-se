---
title: Kopiera data från Office 365 med Azure Data Factory (förhandsversion) | Microsoft Docs
description: Lär dig hur du kopierar data från Office 365 till datalager för mottagare som stöds med hjälp av Kopieringsaktivitet i en Azure Data Factory-pipeline.
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
ms.date: 10/15/2018
ms.author: jingwang
ms.openlocfilehash: c9252380581e77049e9464316ca77cc135f784b6
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49377609"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory-preview"></a>Kopiera data från Office 365 till Azure med hjälp av Azure Data Factory (förhandsversion) 

Azure Data Factory kan du hämta den omfattande organisationens data i din Office 365-klient till Azure på ett skalbart sätt och skapa analysprogram och extrahera insikter utifrån dessa viktiga datatillgångar. Integrering med Privileged Access Management ger säker åtkomstkontroll för värdefulla utvalda data i Office 365.  Mer information om Microsoft Graph-data ansluta, referera till [den här länken](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki).

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från Office 365. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

Titta på följande videoklipp för en nio minuters introduktion och demonstration om hur du ansluter Data Factory till Office 365-data:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-cloud-scale-analytics-of-Office-365-data-with-Azure-Data-Factory/player]

## <a name="supported-capabilities"></a>Funktioner som stöds

För närvarande i en enda Kopieringsaktivitet du kan bara **kopiera data från Office 365 till [Azure Blob Storage](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), och [Azure Data Lake Storage Gen2 ( Förhandsversion)](connector-azure-data-lake-storage.md) i JSON-format** (typen setOfObjects). Om du vill att läsa in Office 365 till andra typer av datalager eller i andra format, du kan länka första kopieringsaktiviteten med en efterföljande Kopieringsaktivitet ytterligare läsa in data i någon av de [stöds ADF målarkiv](copy-activity-overview.md#supported-data-stores-and-formats) (finns i ” stöds som en mottagare ”kolumn i tabellen” stöds datalager och format ”).

>[!IMPORTANT]
>- Azure-prenumerationen som innehåller data factory och det mottagande datalagren måste vara under samma Azure Active Directory (Azure AD)-klient som Office 365-klient.
>- Se till att Azure Integration Runtime-regionen som används för kopieringsaktiviteten samt målet är i samma region där Office 365-klient användarnas postlådan finns. Se [här](concepts-integration-runtime.md#integration-runtime-location) att förstå hur Azure IR-platsen bestäms. Referera till [tabellen här](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/Capabilities#data-regions) lista över regioner som stöds Office och motsvarande Azure-regioner.
>-  Om du läser in Office 365-data i **Azure Blob Storage** som mål, se till att du använder **[tjänstobjektautentisering](connector-azure-blob-storage.md#service-principal-authentication)** när du definierar länkat Tjänsten till Azure Blob Storage och inte använder [kontonyckel](connector-azure-blob-storage.md#account-key-authentication), [signatur för delad åtkomst](connector-azure-blob-storage.md#shared-access-signature-authentication) eller [hanterade identiteter för Azure-resurser](connector-azure-blob-storage.md#managed-identity) autentiseringar.
>-  Om du läser in Office 365-data i **Azure Data Lake Storage Gen1** som mål, se till att du använder [ **tjänstobjektautentisering** ](connector-azure-data-lake-store.md#using-service-principal-authentication) när du definierar den Länkad tjänst till Azure Data Lake Storage Gen1 och inte använder [hanterade identiteter för Azure-resurser autentisering](connector-azure-data-lake-store.md#managed-identity).

## <a name="prerequisites"></a>Förutsättningar

Om du vill kopiera data från Office 365 till Azure, måste du utföra följande nödvändiga steg:

- Din Office 365-klientadministratör slutföra registreringen av åtgärder enligt [här](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/On-boarding).
- Skapa och konfigurera ett Azure AD-webbprogram i Azure Active Directory.  Anvisningar finns i [skapa ett Azure AD-program](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application).
- Anteckna följande värden som du använder för att definiera den länkade tjänsten för Office 365:
    - Klient-ID.  Anvisningar finns i [hämta klient-ID](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).
    - Nyckel för program-ID och program.  Anvisningar finns i [Get ID och autentiseringsnyckel programnyckel](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).
- Lägg till användar-ID som ska upprätta data förfrågan som ägare till Azure AD-webbappen (från Azure AD-webbprogram > Inställningar > ägare > Lägg till ägare).

## <a name="approving-new-data-access-requests"></a>Godkänna åtkomstbegäranden för nya data

Om det här är första gången du begär data för den här kontexten (en kombination av vilka data som tabell håller på att åtkomst, vilken destination kontot är data som läses in i och vilka användaridentitet är att göra data åtkomstbegäran), ser du kopieringsaktiviteten status som ”pågår” och bara när du klickar på [”information” länka under åtgärder](copy-activity-overview.md#monitoring) ska du se statusen som ”RequestingConsent”.  En medlem i gruppen data access godkännaren måste godkänna begäran i Privileged Access Management innan extrahering av data kan fortsätta.

Se [här](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Approving-data-access-requests) på hur godkännaren kan godkänna data åt begäran och se [här](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Capabilities#integration-with-privileged-access-management) förklaringar på övergripande integrering med Privileged Access Management, inklusive hur du ställer in data godkännare för nyckelringar.

## <a name="policy-validation"></a>Verifieringen av användarprinciper

Om ADF skapas som en del av en hanterad app och Azure-principer tilldelningar görs på resurser i resursgruppen management, sedan kontrollerar för varje kopieringsaktivitetskörning, ADF om du vill kontrollera principtilldelningar används. Se [här](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/Capabilities#policies) en lista över principer som stöds.

## <a name="getting-started"></a>Komma igång

>[!TIP]
>En genomgång på hur du använder Office 365-anslutning finns i [läsa in data från Office 365](load-office-365-data.md) artikeln.

Du kan skapa en pipeline med Kopieringsaktivitet med någon av följande verktyg och SDK: er. Välj en länk som leder till en självstudie med stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet. 

- [Azure Portal](quickstart-create-data-factory-portal.md)
- [.NET SDK](quickstart-create-data-factory-dot-net.md)
- [Python SDK](quickstart-create-data-factory-python.md)
- [Azure PowerShell](quickstart-create-data-factory-powershell.md)
- [REST-API](quickstart-create-data-factory-rest-api.md)
- [Azure Resource Manager-mall](quickstart-create-data-factory-resource-manager-template.md). 

Följande avsnitt innehåller information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Office 365-anslutning.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för Office 365-länkade tjänsten:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen måste anges till: **Office 365** | Ja |
| office365TenantId | Azure klient-ID som Office 365-kontot tillhör. | Ja |
| servicePrincipalTenantId | Ange klient som din Azure AD-webbappen finns. | Ja |
| servicePrincipalId | Ange programmets klient-ID. | Ja |
| servicePrincipalKey | Ange programmets nyckel. Markera det här fältet som en SecureString ska lagras på ett säkert sätt i Data Factory. | Ja |
| connectVia | Integration Runtime som används för att ansluta till datalagret.  Om den inte anges används standard Azure Integration Runtime. | Nej |

>[!NOTE]
> Skillnaden mellan **office365TenantId** och **servicePrincipalTenantId** och motsvarande värde att tillhandahålla:
>- Om du är en enterprise-utvecklare utvecklar ett program mot Office 365-data för användning i din organisation, och du bör ange samma klient-ID för båda egenskaperna som är AAD-klient för din organisations-ID.
>- Om du är en ISV utvecklare utvecklar ett program för dina kunder så office365TenantId kommer att vara din kunds (applikationens installationsprogram) blir klient-ID för AAD och servicePrincipalTenantId ditt företags AAD-klientorganisations-ID.

**Exempel:**

```json
{
    "name": "Office365LinkedService",
    "properties": {
        "type": "Office365",
        "typeProperties": {
            "office365TenantId": "<Office 365 tenant id>",
            "servicePrincipalTenantId": "<AAD app service principal tenant id>",
            "servicePrincipalId": "<AAD app service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<AAD app service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [datauppsättningar](concepts-datasets-linked-services.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Office 365-datauppsättningen.

För att kopiera data från Office 365, stöds följande egenskaper:

| Egenskap  | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Type-egenskapen för datauppsättningen måste anges till: **Office365Table** | Ja |
| tableName | Namn på datauppsättning för att extrahera från Office 365. Se [här](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Capabilities#datasets) lista över tillgängliga för extrahering av Office 365-datauppsättningar. | Ja |
| Predikatet | Ett predikat uttryck som kan användas för att filtrera specifika rader att extrahera från Office 365.  Se [här](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Capabilities#filters) att ta reda på vilka kolumner som kan användas för filtrering med predikat för varje tabell och uttrycksformatet filter. | Nej<br>(Om ingen predikatet anges är standardvärdet att extrahera data för de senaste 30 dagarna) |

**Exempel**

```json
{
    "name": "Office365Table1",
    "properties": {
        "type": "Office365Table",
        "linkedServiceName": {
            "referenceName": "<Office 365 linked service name>",
            "type": "LinkedServiceReference"
        },
        "structure": [
            {
                "name": "ReceivedDateTime",
                "type": "datetime"
            },
            {
                "name": "SentDateTime",
                "type": "datetime"
            },
            {
                "name": "HasAttachments",
                "type": "boolean"
            },
            {
                "name": "InternetMessageId",
                "type": "string"
            },
            {
                "name": "Subject",
                "type": "string"
            },
            {
                "name": "Importance",
                "type": "string"
            },
            {
                "name": "ParentFolderId",
                "type": "string"
            },
            {
                "name": "Sender",
                "type": "string"
            },
            {
                "name": "From",
                "type": "string"
            },
            {
                "name": "ToRecipients",
                "type": "string"
            },
            {
                "name": "CcRecipients",
                "type": "string"
            },
            {
                "name": "BccRecipients",
                "type": "string"
            },
            {
                "name": "ReplyTo",
                "type": "string"
            },
            {
                "name": "ConversationId",
                "type": "string"
            },
            {
                "name": "UniqueBody",
                "type": "string"
            },
            {
                "name": "IsDeliveryReceiptRequested",
                "type": "boolean"
            },
            {
                "name": "IsReadReceiptRequested",
                "type": "boolean"
            },
            {
                "name": "IsRead",
                "type": "boolean"
            },
            {
                "name": "IsDraft",
                "type": "boolean"
            },
            {
                "name": "WebLink",
                "type": "string"
            },
            {
                "name": "CreatedDateTime",
                "type": "datetime"
            },
            {
                "name": "LastModifiedDateTime",
                "type": "datetime"
            },
            {
                "name": "ChangeKey",
                "type": "string"
            },
            {
                "name": "Categories",
                "type": "string"
            },
            {
                "name": "Id",
                "type": "string"
            },
            {
                "name": "Attachments",
                "type": "string"
            }
        ],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Contact_v0",
            "predicate": "CreatedDateTime >= 2018-07-11T16:00:00.000Z AND CreatedDateTime <= 2018-07-18T16:00:00.000Z"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [Pipelines](concepts-pipelines-activities.md) artikeln. Det här avsnittet innehåller en lista över egenskaper som stöds av Office 365-källa.

### <a name="office-365-as-source"></a>Office 365 som källa

För att kopiera data från Office 365, ange typ av datakälla i kopieringsaktiviteten till **Office365Source**. Inga ytterligare egenskaper som stöds i kopieringsaktiviteten **källa** avsnittet.

**Exempel:**

```json
"activities": [
    {
        "name": "CopyFromO365ToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Office 365 input dataset name>",
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
                "type": "Office365Source"
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och mottagare av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
