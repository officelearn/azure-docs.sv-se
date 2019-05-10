---
title: Kopiera data från Office 365 med Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från Office 365 till datalager för mottagare som stöds med hjälp av Kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: jingwang
ms.openlocfilehash: 9ca3cbb1ef46c7fe53b6b16bda40ebef245613f3
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415665"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory"></a>Kopiera data från Office 365 till Azure med hjälp av Azure Data Factory

Azure Data Factory kan du hämta den omfattande organisationens data i din Office 365-klient till Azure på ett skalbart sätt och skapa analysprogram och extrahera insikter utifrån dessa viktiga datatillgångar. Integrering med Privileged Access Management ger säker åtkomstkontroll för värdefulla utvalda data i Office 365.  Mer information om Microsoft Graph-data ansluta, referera till [den här länken](https://docs.microsoft.com/graph/data-connect-concept-overview).

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från Office 365. Den bygger på den [översikt över Kopieringsaktivitet](copy-activity-overview.md) artikel som ger en allmän översikt över Kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds

För närvarande i en enda Kopieringsaktivitet du kan bara **kopiera data från Office 365 till [Azure Blob Storage](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), och [Azure Data Lake Storage Gen2 ](connector-azure-data-lake-storage.md) i JSON-format** (typen setOfObjects). Om du vill att läsa in Office 365 till andra typer av datalager eller i andra format, du kan länka första kopieringsaktiviteten med en efterföljande Kopieringsaktivitet ytterligare läsa in data i någon av de [stöds ADF målarkiv](copy-activity-overview.md#supported-data-stores-and-formats) (finns i ” stöds som en mottagare ”kolumn i tabellen” stöds datalager och format ”).

>[!IMPORTANT]
>- Azure-prenumerationen som innehåller data factory och det mottagande datalagren måste vara under samma Azure Active Directory (Azure AD)-klient som Office 365-klient.
>- Se till att Azure Integration Runtime-regionen som används för kopieringsaktiviteten samt målet är i samma region där Office 365-klient användarnas postlådan finns. Se [här](concepts-integration-runtime.md#integration-runtime-location) att förstå hur Azure IR-platsen bestäms. Referera till [tabellen här](https://docs.microsoft.com/graph/data-connect-datasets#regions) lista över regioner som stöds Office och motsvarande Azure-regioner.
>- Tjänstens huvudnamn autentisering är endast autentiseringsmekanism som stöds för Azure Blob Storage, Azure Data Lake Storage Gen1 och Gen2 för Azure Data Lake Storage som målarkiv.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill kopiera data från Office 365 till Azure, måste du utföra följande nödvändiga steg:

- Din Office 365-klientadministratör slutföra registreringen av åtgärder enligt [här](https://docs.microsoft.com/graph/data-connect-get-started).
- Skapa och konfigurera ett Azure AD-webbprogram i Azure Active Directory.  Anvisningar finns i [skapa ett Azure AD-program](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application).
- Anteckna följande värden som du använder för att definiera den länkade tjänsten för Office 365:
    - Klient-ID. Anvisningar finns i [hämta klient-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-id).
    - Nyckel för program-ID och program.  Anvisningar finns i [Get ID och autentiseringsnyckel programnyckel](../active-directory/develop/howto-create-service-principal-portal.md#get-application-id-and-authentication-key).
- Lägg till användar-ID som ska upprätta data förfrågan som ägare till Azure AD-webbappen (från Azure AD-webbprogram > Inställningar > ägare > Lägg till ägare). 
    - Användar-ID måste vara i Office 365-organisation du får data från och får inte vara en gästanvändare.

## <a name="approving-new-data-access-requests"></a>Godkänna åtkomstbegäranden för nya data

Om det här är första gången du begär data för den här kontexten (en kombination av vilka data som tabell håller på att åtkomst, vilken destination kontot är data som läses in i och vilka användaridentitet är att göra data åtkomstbegäran), ser du kopieringsaktiviteten status som ”pågår” och bara när du klickar på [”information” länka under åtgärder](copy-activity-overview.md#monitoring) ska du se statusen som ”RequestingConsent”.  En medlem i gruppen data access godkännaren måste godkänna begäran i Privileged Access Management innan extrahering av data kan fortsätta.

Se [här](https://docs.microsoft.com/graph/data-connect-tips#approve-pam-requests-via-office-365-admin-portal) på hur godkännaren kan godkänna data åt begäran och se [här](https://docs.microsoft.com/graph/data-connect-pam) förklaringar på övergripande integrering med Privileged Access Management, inklusive hur du ställer in data godkännare för nyckelringar.

## <a name="policy-validation"></a>Verifieringen av användarprinciper

Om ADF skapas som en del av en hanterad app och Azure-principer tilldelningar görs på resurser i resursgruppen management, sedan kontrollerar för varje kopieringsaktivitetskörning, ADF om du vill kontrollera principtilldelningar används. Se [här](https://docs.microsoft.com/graph/data-connect-policies#policies) en lista över principer som stöds.

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
| type | Type-egenskapen måste anges till: **Office365** | Ja |
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
| type | Type-egenskapen för datauppsättningen måste anges till: **Office365Table** | Ja |
| tableName | Namn på datauppsättning för att extrahera från Office 365. Se [här](https://docs.microsoft.com/graph/data-connect-datasets#datasets) lista över tillgängliga för extrahering av Office 365-datauppsättningar. | Ja |
| allowedGroups | Predikatet för val av grupp.  Använd den här egenskapen för att välja upp till 10 användargrupper för vilka data hämtas.  Om det finns inga grupper har angetts, returneras data för hela organisationen. | Nej |
| userScopeFilterUri | När `allowedGroups` egenskapen inte har angetts kan du använda en predikatuttryck som tillämpas på hela klientorganisationen att filtrera specifika rader att extrahera från Office 365. Predikatet formatet måste överensstämma frågeformat Microsoft Graph API: er, t.ex. `https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'`. | Nej |
| dateFilterColumn | Namnet på kolumnen datum/tid-filter. Använd den här egenskapen om du vill begränsa tidsintervallet för vilka Office 365-data hämtas. | Ja om datauppsättningen har en eller flera kolumner för datum/tid. Se [här](https://docs.microsoft.com/graph/data-connect-filtering#filtering) lista över datauppsättningar som kräver det här datum/tid-filtret. |
| startTime | Starta DateTime-värde att filtrera på. | Ja om `dateFilterColumn` har angetts |
| endTime | Avsluta DateTime-värde att filtrera på. | Ja om `dateFilterColumn` har angetts |

**Exempel**

```json
{
    "name": "DS_May2019_O365_Message",
    "properties": {
        "type": "Office365Table",
        "linkedServiceName": {
            "referenceName": "<Office 365 linked service name>",
            "type": "LinkedServiceReference"
        },
        "structure": [
            {
                "name": "Id",
                "type": "String",
                "description": "The unique identifier of the event."
            },
            {
                "name": "CreatedDateTime",
                "type": "DateTime",
                "description": "The date and time that the event was created."
            },
            {
                "name": "LastModifiedDateTime",
                "type": "DateTime",
                "description": "The date and time that the event was last modified."
            },
            {
                "name": "ChangeKey",
                "type": "String",
                "description": "Identifies the version of the event object. Every time the event is changed, ChangeKey changes as well. This allows Exchange to apply changes to the correct version of the object."
            },
            {
                "name": "Categories",
                "type": "String",
                "description": "The categories associated with the event. Format: ARRAY<STRING>"
            },
            {
                "name": "OriginalStartTimeZone",
                "type": "String",
                "description": "The start time zone that was set when the event was created. See DateTimeTimeZone for a list of valid time zones."
            },
            {
                "name": "OriginalEndTimeZone",
                "type": "String",
                "description": "The end time zone that was set when the event was created. See DateTimeTimeZone for a list of valid time zones."
            },
            {
                "name": "ResponseStatus",
                "type": "String",
                "description": "Indicates the type of response sent in response to an event message. Format: STRUCT<Response: STRING, Time: STRING>"
            },
            {
                "name": "iCalUId",
                "type": "String",
                "description": "A unique identifier that is shared by all instances of an event across different calendars."
            },
            {
                "name": "ReminderMinutesBeforeStart",
                "type": "Int32",
                "description": "The number of minutes before the event start time that the reminder alert occurs."
            },
            {
                "name": "IsReminderOn",
                "type": "Boolean",
                "description": "Set to true if an alert is set to remind the user of the event."
            },
            {
                "name": "HasAttachments",
                "type": "Boolean",
                "description": "Set to true if the event has attachments."
            },
            {
                "name": "Subject",
                "type": "String",
                "description": "The text of the event's subject line."
            },
            {
                "name": "Body",
                "type": "String",
                "description": "The body of the message associated with the event.Format: STRUCT<ContentType: STRING, Content: STRING>"
            },
            {
                "name": "Importance",
                "type": "String",
                "description": "The importance of the event: Low, Normal, High."
            },
            {
                "name": "Sensitivity",
                "type": "String",
                "description": "Indicates the level of privacy for the event: Normal, Personal, Private, Confidential."
            },
            {
                "name": "Start",
                "type": "String",
                "description": "The start time of the event. Format: STRUCT<DateTime: STRING, TimeZone: STRING>"
            },
            {
                "name": "End",
                "type": "String",
                "description": "The date and time that the event ends. Format: STRUCT<DateTime: STRING, TimeZone: STRING>"
            },
            {
                "name": "Location",
                "type": "String",
                "description": "Location information of the event. Format: STRUCT<DisplayName: STRING, Address: STRUCT<Street: STRING, City: STRING, State: STRING, CountryOrRegion: STRING, PostalCode: STRING>, Coordinates: STRUCT<Altitude: DOUBLE, Latitude: DOUBLE, Longitude: DOUBLE, Accuracy: DOUBLE, AltitudeAccuracy: DOUBLE>>"
            },
            {
                "name": "IsAllDay",
                "type": "Boolean",
                "description": "Set to true if the event lasts all day. Adjusting this property requires adjusting the Start and End properties of the event as well."
            },
            {
                "name": "IsCancelled",
                "type": "Boolean",
                "description": "Set to true if the event has been canceled."
            },
            {
                "name": "IsOrganizer",
                "type": "Boolean",
                "description": "Set to true if the message sender is also the organizer."
            },
            {
                "name": "Recurrence",
                "type": "String",
                "description": "The recurrence pattern for the event. Format: STRUCT<Pattern: STRUCT<Type: STRING, `Interval`: INT, Month: INT, DayOfMonth: INT, DaysOfWeek: ARRAY<STRING>, FirstDayOfWeek: STRING, Index: STRING>, `Range`: STRUCT<Type: STRING, StartDate: STRING, EndDate: STRING, RecurrenceTimeZone: STRING, NumberOfOccurrences: INT>>"
            },
            {
                "name": "ResponseRequested",
                "type": "Boolean",
                "description": "Set to true if the sender would like a response when the event is accepted or declined."
            },
            {
                "name": "ShowAs",
                "type": "String",
                "description": "The status to show: Free, Tentative, Busy, Oof, WorkingElsewhere, Unknown."
            },
            {
                "name": "Type",
                "type": "String",
                "description": "The event type: SingleInstance, Occurrence, Exception, SeriesMaster."
            },
            {
                "name": "Attendees",
                "type": "String",
                "description": "The collection of attendees for the event. Format: ARRAY<STRUCT<EmailAddress: STRUCT<Name: STRING, Address: STRING>, Status: STRUCT<Response: STRING, Time: STRING>, Type: STRING>>"
            },
            {
                "name": "Organizer",
                "type": "String",
                "description": "The organizer of the event. Format: STRUCT<EmailAddress: STRUCT<Name: STRING, Address: STRING>>"
            },
            {
                "name": "WebLink",
                "type": "String",
                "description": "The URL to open the event in Outlook Web App."
            },
            {
                "name": "Attachments",
                "type": "String",
                "description": "The FileAttachment and ItemAttachment attachments for the message. Navigation property. Format: ARRAY<STRUCT<LastModifiedDateTime: STRING, Name: STRING, ContentType: STRING, Size: INT, IsInline: BOOLEAN, Id: STRING>>"
            },
            {
                "name": "BodyPreview",
                "type": "String",
                "description": "The preview of the message associated with the event. It is in text format."
            },
            {
                "name": "Locations",
                "type": "String",
                "description": "The locations where the event is held or attended from. The location and locations properties always correspond with each other. Format:  ARRAY<STRUCT<DisplayName: STRING, Address: STRUCT<Street: STRING, City: STRING, State: STRING, CountryOrRegion: STRING, PostalCode: STRING>, Coordinates: STRUCT<Altitude: DOUBLE, Latitude: DOUBLE, Longitude: DOUBLE, Accuracy: DOUBLE, AltitudeAccuracy: DOUBLE>, LocationEmailAddress: STRING, LocationUri: STRING, LocationType: STRING, UniqueId: STRING, UniqueIdType: STRING>>"
            },
            {
                "name": "OnlineMeetingUrl",
                "type": "String",
                "description": "A URL for an online meeting. The property is set only when an organizer specifies an event as an online meeting such as a Skype meeting"
            },
            {
                "name": "OriginalStart",
                "type": "DateTime",
                "description": "The start time that was set when the event was created in UTC time."
            },
            {
                "name": "SeriesMasterId",
                "type": "String",
                "description": "The ID for the recurring series master item, if this event is part of a recurring series."
            }
        ],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Event_v1",
            "dateFilterColumn": "CreatedDateTime",
            "startTime": "2019-04-28T16:00:00.000Z",
            "endTime": "2019-05-05T16:00:00.000Z",
            "userScopeFilterUri": "https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'"
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
