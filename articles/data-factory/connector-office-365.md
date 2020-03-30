---
title: Kopiera data från Office 365 med Azure Data Factory
description: Lär dig hur du kopierar data från Office 365 till sink-datalager som stöds med hjälp av kopieringsaktivitet i en Azure Data Factory-pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/20/2019
ms.author: jingwang
ms.openlocfilehash: d97b3caccc92f0fdfeb229d94e30ee6499c26181
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74912413"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory"></a>Kopiera data från Office 365 till Azure med Azure Data Factory

Azure Data Factory integreras med [Microsoft Graph-dataanslutning,](https://docs.microsoft.com/graph/data-connect-concept-overview)så att du kan föra in de omfattande organisationsdata i din Office 365-klientorganisation i Azure på ett skalbart sätt och skapa analysprogram och extrahera insikter baserat på dessa värdefulla datatillgångar. Integrering med privilegierad åtkomsthantering ger säker åtkomstkontroll för värdefulla kurerade data i Office 365.  Mer information finns i [den här länken](https://docs.microsoft.com/graph/data-connect-concept-overview) om Microsoft Graph-dataanslutning och mer information om den här [länken.](https://docs.microsoft.com/graph/data-connect-policies#licensing)

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att kopiera data från Office 365. Den bygger på [kopian aktivitet översikt](copy-activity-overview.md) artikeln som presenterar en allmän översikt över kopieringsaktivitet.

## <a name="supported-capabilities"></a>Funktioner som stöds
ADF Office 365-anslutningsappen och Microsoft Graph-dataanslutning möjliggör skalning av olika typer av datauppsättningar från Exchange-e-postlådor, inklusive adressbokskontakter, kalenderhändelser, e-postmeddelanden, användarinformation, postlådeinställningar och så vidare.  Se [här](https://docs.microsoft.com/graph/data-connect-datasets) för att se hela listan över tillgängliga datauppsättningar.

För tillfället kan du inom en enda kopieringsaktivitet bara **kopiera data från Office 365 till Azure [Blob Storage,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)och [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) i JSON-format** (typ setOfObjects). Om du vill läsa in Office 365 i andra typer av datalager eller i andra format kan du kedja den första kopieringsaktiviteten med en efterföljande kopieringsaktivitet för att ytterligare läsa in data i någon av de [ADF-målarkiv](copy-activity-overview.md#supported-data-stores-and-formats) som stöds (se kolumnen "stöds som en diskbänk" i tabellen "Datalager och format som stöds" som stöds).

>[!IMPORTANT]
>- Azure-prenumerationen som innehåller datafabriken och sink-datalagringen måste finnas under samma Azure Active Directory -klientorganisation (Azure AD) som Office 365-klient.
>- Kontrollera att Azure Integration Runtime-regionen som används för kopieringsaktivitet samt målet ligger i samma region där Office 365-klientanvändarnas postlåda finns. Se [här](concepts-integration-runtime.md#integration-runtime-location) för att förstå hur Azure IR-platsen bestäms. Se [tabellen här](https://docs.microsoft.com/graph/data-connect-datasets#regions) för listan över Office-regioner som stöds och motsvarande Azure-regioner.
>- Tjänsthuvudnamnsautentisering är den enda autentiseringsmekanism som stöds för Azure Blob Storage, Azure Data Lake Storage Gen1 och Azure Data Lake Storage Gen2 som mållager.

## <a name="prerequisites"></a>Krav

Om du vill kopiera data från Office 365 till Azure måste du följa följande steg:

- Din Office 365-klientadministratör måste utföra åtgärder för ombordstigning enligt beskrivningen [här](https://docs.microsoft.com/graph/data-connect-get-started).
- Skapa och konfigurera ett Azure AD-webbprogram i Azure Active Directory.  Instruktioner finns i [Skapa ett Azure AD-program](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application).
- Anteckna följande värden, som du använder för att definiera den länkade tjänsten för Office 365:
    - Klient-ID. Instruktioner finns i [Hämta klient-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
    - Program-ID och programnyckel.  Instruktioner finns i [Hämta program-ID och autentiseringsnyckel](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
- Lägg till användaridentiteten som kommer att göra begäran om dataåtkomst som ägare till Azure AD-webbprogrammet (från Azure AD-webbprogrammet > Inställningar > Ägare > Lägg till ägare). 
    - Användaridentiteten måste finnas i den Office 365-organisation som du hämtar data från och får inte vara gästanvändare.

## <a name="approving-new-data-access-requests"></a>Godkänna nya begäranden om dataåtkomst

Om det är första gången du begär data för den här kontexten (en kombination av vilken datatabell som används, vilket målkonto som är de data som läses in i och vilken användaridentitet som gör begäran om dataåtkomst), ser du kopieringsaktivitetsstatusen som "Pågår", och först när du klickar på [länken "Detaljer" under Åtgärder](copy-activity-overview.md#monitoring) ser du statusen som "RequestingConsent".  En medlem i gruppen för godkännande av dataåtkomst måste godkänna begäran i hantering av privilegierad åtkomst innan datautvinningen kan fortsätta.

Här [here](https://docs.microsoft.com/graph/data-connect-tips#approve-pam-requests-via-office-365-admin-portal) hänvisas till hur godkännaren kan godkänna begäran om dataåtkomst och [här](https://docs.microsoft.com/graph/data-connect-pam) hänvisa en förklaring om den övergripande integreringen med Privilegierad åtkomsthantering, inklusive hur du ställer in gruppen för dataåtkomstgodkännare.

## <a name="policy-validation"></a>Principverifiering

Om ADF skapas som en del av en hanterad app och Azure-principtilldelningar görs på resurser inom hanteringsresursgruppen, kontrollerar ADF för varje kopieringsaktivitet som körs, att se till att principtilldelningarna tillämpas. Se [här](https://docs.microsoft.com/graph/data-connect-policies#policies) för en lista över principer som stöds.

## <a name="getting-started"></a>Komma igång

>[!TIP]
>En genomgång av hur du använder Office 365-anslutningsappen finns i [Läsa in data från Office 365-artikeln.](load-office-365-data.md)

Du kan skapa en pipeline med kopieringsaktiviteten med hjälp av något av följande verktyg eller SDK:er. Välj en länk för att gå till en självstudiekurs med steg-för-steg-instruktioner för att skapa en pipeline med en kopieringsaktivitet. 

- [Azure-portal](quickstart-create-data-factory-portal.md)
- [.NET SDK](quickstart-create-data-factory-dot-net.md)
- [Python SDK](quickstart-create-data-factory-python.md)
- [Azure PowerShell](quickstart-create-data-factory-powershell.md)
- [REST API](quickstart-create-data-factory-rest-api.md)
- [Azure Resource Manager-mall](quickstart-create-data-factory-resource-manager-template.md). 

I följande avsnitt finns information om egenskaper som används för att definiera Data Factory-entiteter som är specifika för Office 365-anslutningsappen.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper stöds för den länkade tjänsten För Office 365:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type måste anges till: **Office365** | Ja |
| office365TenantId | Azure-klient-ID som Office 365-kontot tillhör. | Ja |
| servicePrincipalTenantId | Ange den klientinformation som ditt Azure AD-webbprogram finns under. | Ja |
| servicePrincipalId | Ange programmets klient-ID. | Ja |
| servicePrincipalKey | Ange programmets nyckel. Markera det här fältet som en SecureString för att lagra det säkert i Data Factory. | Ja |
| connectVia (på) | Den integrationskörning som ska användas för att ansluta till datalagret.  Om det inte anges används standardkörningen för Azure Integration. | Inga |

>[!NOTE]
> Skillnaden mellan **office365TenantId** och **servicePrincipalTenantId** och motsvarande värde för att tillhandahålla:
>- Om du är en företagsutvecklare som utvecklar ett program mot Office 365-data för din egen organisations användning bör du ange samma klient-ID för båda egenskaperna, som är organisationens AAD-klient-ID.
>- Om du är en ISV-utvecklare som utvecklar ett program för dina kunder, kommer office365TenantId att vara kundens (programinstallationsprogrammet) AAD-klient-ID och servicePrincipalTenantId kommer att vara företagets AAD-klient-ID.

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

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningsartikeln.](concepts-datasets-linked-services.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Office 365-datauppsättningen.

Så här kopierar du data från Office 365 stöds följande egenskaper:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Datauppsättningens typegenskap måste anges till: **Office365Table** | Ja |
| tableName | Namnet på den datauppsättning som ska extraheras från Office 365. Se [här](https://docs.microsoft.com/graph/data-connect-datasets#datasets) för listan över Office 365-datauppsättningar som är tillgängliga för extrahering. | Ja |

Om du `dateFilterColumn`ställer `startTime` `endTime`in `userScopeFilterUri` , , och i datauppsättningen stöds det fortfarande som det är, medan du föreslås använda den nya modellen i aktivitetskällan framöver.

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
        "schema": [],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Event_v1"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Pipelines.](concepts-pipelines-activities.md) Det här avsnittet innehåller en lista över egenskaper som stöds av Office 365-källa.

### <a name="office-365-as-source"></a>Office 365 som källa

Om du vill kopiera data från Office 365 stöds följande egenskaper i källavsnittet för **kopieringsaktivitet:**

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| typ | Egenskapen Type property för kopians aktivitetskälla måste anges till: **Office365Source** | Ja |
| tillåtna Grupper | Gruppval predikat.  Använd den här egenskapen om du vill välja upp till 10 användargrupper för vilka data ska hämtas.  Om inga grupper anges returneras data för hela organisationen. | Inga |
| användareScopeFilterUri | När `allowedGroups` egenskapen inte anges kan du använda ett predikatuttryck som används på hela klienten för att filtrera de specifika raderna att extrahera från Office 365. Predikatformatet ska matcha frågeformatet för Microsoft Graph API:er, t.ex. `https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'` | Inga |
| datumFilterKolumn | Namn på filterkolumnen DateTime. Använd den här egenskapen om du vill begränsa det tidsintervall för vilket Office 365-data extraheras. | Ja om datauppsättningen har en eller flera DateTime-kolumner. Se [här](https://docs.microsoft.com/graph/data-connect-filtering#filtering) för en lista över datauppsättningar som kräver detta DateTime-filter. |
| startTime | Start DateTime-värde att filtrera på. | Ja `dateFilterColumn` om det anges |
| endTime | Avsluta DateTime-värde som ska filtreras på. | Ja `dateFilterColumn` om det anges |
| outputKolunner | Matris med kolumnerna som ska kopieras till handfat. | Inga |

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
                "type": "Office365Source",
                "dateFilterColumn": "CreatedDateTime",
                "startTime": "2019-04-28T16:00:00.000Z",
                "endTime": "2019-05-05T16:00:00.000Z",
                "userScopeFilterUri": "https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'",
                "outputColumns": [
                    {
                        "name": "Id"
                    },
                    {
                        "name": "CreatedDateTime"
                    },
                    {
                        "name": "LastModifiedDateTime"
                    },
                    {
                        "name": "ChangeKey"
                    },
                    {
                        "name": "Categories"
                    },
                    {
                        "name": "OriginalStartTimeZone"
                    },
                    {
                        "name": "OriginalEndTimeZone"
                    },
                    {
                        "name": "ResponseStatus"
                    },
                    {
                        "name": "iCalUId"
                    },
                    {
                        "name": "ReminderMinutesBeforeStart"
                    },
                    {
                        "name": "IsReminderOn"
                    },
                    {
                        "name": "HasAttachments"
                    },
                    {
                        "name": "Subject"
                    },
                    {
                        "name": "Body"
                    },
                    {
                        "name": "Importance"
                    },
                    {
                        "name": "Sensitivity"
                    },
                    {
                        "name": "Start"
                    },
                    {
                        "name": "End"
                    },
                    {
                        "name": "Location"
                    },
                    {
                        "name": "IsAllDay"
                    },
                    {
                        "name": "IsCancelled"
                    },
                    {
                        "name": "IsOrganizer"
                    },
                    {
                        "name": "Recurrence"
                    },
                    {
                        "name": "ResponseRequested"
                    },
                    {
                        "name": "ShowAs"
                    },
                    {
                        "name": "Type"
                    },
                    {
                        "name": "Attendees"
                    },
                    {
                        "name": "Organizer"
                    },
                    {
                        "name": "WebLink"
                    },
                    {
                        "name": "Attachments"
                    },
                    {
                        "name": "BodyPreview"
                    },
                    {
                        "name": "Locations"
                    },
                    {
                        "name": "OnlineMeetingUrl"
                    },
                    {
                        "name": "OriginalStart"
                    },
                    {
                        "name": "SeriesMasterId"
                    }
                ]
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="next-steps"></a>Nästa steg
En lista över datalager som stöds som källor och sänkor av kopieringsaktiviteten i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
