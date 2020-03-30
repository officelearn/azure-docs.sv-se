---
title: Åtkomst- och granskningsloggar
titleSuffix: Azure AD B2C
description: Så här kommer du åt Azure AD B2C-granskningsloggar programmässigt och i Azure-portalen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 83086fa2cb96eba423b9111134a0406d7256821f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264224"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Komma åt Granskningsloggar för Azure AD B2C

Azure Active Directory B2C (Azure AD B2C) avger granskningsloggar som innehåller aktivitetsinformation om B2C-resurser, utfärdade token och administratörsåtkomst. Den här artikeln innehåller en kort översikt över den information som finns i granskningsloggar och instruktioner om hur du kommer åt dessa data för din Azure AD B2C-klient.

Granskningslogghändelser behålls bara i **sju dagar**. Planera att ladda ner och lagra dina loggar med någon av de metoder som visas nedan om du behöver en längre kvarhållningsperiod.

> [!NOTE]
> Du kan inte se användarloggningar för enskilda Azure AD B2C-program under avsnittet **Användare** på **Azure Active Directory-** eller Azure AD **B2C-sidorna** i Azure-portalen. Inloggningshändelserna där visar användaraktivitet, men kan inte korreleras tillbaka till det B2C-program som användaren loggade in på. Du måste använda granskningsloggarna för det, vilket förklaras ytterligare i den här artikeln.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Översikt över aktiviteter som är tillgängliga i B2C-kategorin av revisionsloggar

**B2C-kategorin** i revisionsloggar innehåller följande typer av aktiviteter:

|Aktivitetstyp |Beskrivning  |
|---------|---------|
|Auktorisering |Aktiviteter som rör auktorisering av en användare att komma åt B2C-resurser (till exempel en administratör som har åtkomst till en lista över B2C-principer).         |
|Katalog |Aktiviteter relaterade till katalogattribut som hämtas när en administratör loggar in med Azure-portalen. |
|Program | Skapa, läsa, uppdatera och ta bort (CRUD) åtgärder på B2C-program. |
|Nyckel |CRUD-åtgärder på nycklar som lagras i en B2C-nyckelbehållare. |
|Resurs |CRUD-operationer på B2C-resurser. Till exempel principer och identitetsleverantörer.
|Autentisering |Validering av användarautentiseringsuppgifter och tokenutfärdande.|

För CRUD-aktiviteter för användarobjekt finns i kategorin **Kärnkatalog.**

## <a name="example-activity"></a>Exempel på aktivitet

Den här exempelavbildningen från Azure-portalen visar de data som fångas när en användare loggar in med en extern identitetsleverantör, i det här fallet Facebook:

![Exempel på sidan Aktivitetsinformation för granskningslogg i Azure-portalen](./media/view-audit-logs/audit-logs-example.png)

Panelen För aktivitetsinformation innehåller följande relevanta uppgifter:

|Section|Field|Beskrivning|
|-------|-----|-----------|
| Aktivitet | Namn | Vilken aktivitet ägde rum. Utfärda till exempel *en id_token till programmet*, som avslutar den faktiska användarloggningen. |
| Initierad av (skådespelare) | ObjectId | **Objekt-ID** för B2C-programmet som användaren loggar in på. Den här identifieraren visas inte i Azure-portalen, men är tillgänglig via Microsoft Graph API. |
| Initierad av (skådespelare) | Spn | **Program-ID** för B2C-programmet som användaren loggar in på. |
| Mål | ObjectId | **Objekt-ID** för den användare som loggar in. |
| Ytterligare information | TenantId | **Klient-ID** för Azure AD B2C-klienten. |
| Ytterligare information | PolicyId | **Princip-ID:t** för det användarflöde (principen) som används för att logga in användaren. |
| Ytterligare information | ApplicationId | **Program-ID** för B2C-programmet som användaren loggar in på. |

## <a name="view-audit-logs-in-the-azure-portal"></a>Visa granskningsloggar i Azure-portalen

Azure-portalen ger åtkomst till granskningslogghändelserna i din Azure AD B2C-klientorganisation.

1. Logga in på [Azure-portalen](https://portal.azure.com)
1. Växla till katalogen som innehåller din Azure AD B2C-klient och bläddra sedan till **Azure AD B2C**.
1. Under **Aktiviteter** i den vänstra menyn väljer du **Granskningsloggar**.

En lista över aktivitetshändelser som loggats under de senaste sju dagarna visas.

![Exempelfilter med två aktivitetshändelser i Azure Portal](./media/view-audit-logs/audit-logs-example-filter.png)

Det finns flera filtreringsalternativ, bland annat:

* **Aktivitetsresurstyp** - Filtrera efter de aktivitetstyper som visas i tabellen i avsnittet [Översikt över tillgängliga aktiviteter.](#overview-of-activities-available-in-the-b2c-category-of-audit-logs)
* **Datum** - Filtrera datumintervallet för de aktiviteter som visas.

Om du väljer en rad i listan visas aktivitetsinformationen för händelsen.

Om du vill hämta listan över aktivitetshändelser i en CSV-fil (kommaavgränsade värden) väljer du **Hämta**.

## <a name="get-audit-logs-with-the-azure-ad-reporting-api"></a>Hämta granskningsloggar med Azure AD-rapporterings-API:et

Granskningsloggar publiceras i samma pipeline som andra aktiviteter för Azure Active Directory, så att de kan nås via [Azure Active Directory-rapporterings-API:et](https://docs.microsoft.com/graph/api/directoryaudit-list). Mer information finns i [Komma igång med Azure Active Directory reporting API](../active-directory/reports-monitoring/concept-reporting-api.md).

### <a name="enable-reporting-api-access"></a>Aktivera åtkomst till rapporterings-API

Om du vill tillåta skript- eller programbaserad åtkomst till Azure AD-rapporterings-API:et behöver du ett program som är registrerat i din Azure AD B2C-klient med följande API-behörigheter. Du kan aktivera dessa behörigheter för en befintlig programregistrering i din B2C-klientorganisation eller skapa en ny som är särskilt avsedd för granskningsloggautomatisering.

* Behörigheter för Microsoft Graph >-program > AuditLog > AuditLog.Read.All

Följ stegen i följande artikel för att registrera ett program med de behörigheter som krävs:

[Hantera Azure AD B2C med Microsoft Graph](microsoft-graph-get-started.md)

När du har registrerat ett program med rätt behörighet läser du skriptavsnittet för PowerShell senare i den här artikeln i ett exempel på hur du kan hämta aktivitetshändelser med ett skript.

### <a name="access-the-api"></a>Få tillgång till API:et

Om du vill hämta Azure AD B2C-granskningslogghändelser `B2C` via API:et filtrerar du loggarna i kategorin. Om du vill filtrera `filter` efter kategori använder du frågesträngparametern när du anropar slutpunkten för Azure AD-rapporterings-API.

```HTTP
https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$filter=loggedByService eq 'B2C' and activityDateTime gt 2019-09-10T02:28:17Z
```

### <a name="powershell-script"></a>PowerShell-skript

Följande PowerShell-skript visar ett exempel på hur du frågar Azure AD-rapporterings-API:et. När API:et har frågats skrivs de loggade händelserna ut till standardutdata och skrivs sedan JSON-utdata till en fil.

Du kan prova det här skriptet i [Azure Cloud Shell](overview.md). Var noga med att uppdatera det med ditt program-ID, klienthemlighet och namnet på din Azure AD B2C-klient.

```powershell
# This script requires an application registration that's granted Microsoft Graph API permission
# https://docs.microsoft.com/azure/active-directory-b2c/microsoft-graph-get-started

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's client ID, a GUID
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's client secret
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # Insert your Azure AD B2C tenant domain name

$loginURL       = "https://login.microsoftonline.com"
$resource       = "https://graph.microsoft.com"           # Microsoft Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 through n for number of nextLink pages
if ($oauth.access_token -ne $null) {
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = "https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?`$filter=loggedByService eq 'B2C' and activityDateTime gt  " + $7daysago

    # loop through each query page (1 through n)
    Do {
        # display each event on the console window
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output ($event | ConvertTo-Json)
        }

        # save the query page to an output file
        Write-Output "Save the output to a file audit$i.json"
        $myReport.Content | Out-File -FilePath audit$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)
} else {
    Write-Host "ERROR: No Access Token"
}
```

Här är JSON-representationen av exempelaktivitetshändelsen som visas tidigare i artikeln:

```JSON
{
    "id": "B2C_DQO3J_4984536",
    "category": "Authentication",
    "correlationId": "00000000-0000-0000-0000-000000000000",
    "result": "success",
    "resultReason": "N/A",
    "activityDisplayName": "Issue an id_token to the application",
    "activityDateTime": "2019-09-14T18:13:17.0618117Z",
    "loggedByService": "B2C",
    "operationType": "",
    "initiatedBy": {
        "user": null,
        "app": {
            "appId": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "servicePrincipalId": null,
            "servicePrincipalName": "00000000-0000-0000-0000-000000000000"
        }
    },
    "targetResources": [
        {
            "id": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "type": "User",
            "userPrincipalName": null,
            "groupType": null,
            "modifiedProperties": []
        }
    ],
    "additionalDetails": [
        {
            "key": "TenantId",
            "value": "test.onmicrosoft.com"
        },
        {
            "key": "PolicyId",
            "value": "B2C_1A_signup_signin"
        },
        {
            "key": "ApplicationId",
            "value": "00000000-0000-0000-0000-000000000000"
        },
        {
            "key": "Client",
            "value": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.132 Safari/537.36"
        },
        {
            "key": "IdentityProviderName",
            "value": "facebook"
        },
        {
            "key": "IdentityProviderApplicationId",
            "value": "0000000000000000"
        },
        {
            "key": "ClientIpAddress",
            "value": "127.0.0.1"
        }
    ]
}
```

## <a name="next-steps"></a>Nästa steg

Du kan automatisera andra administrativa uppgifter, till exempel [hantera Azure AD B2C-användarkonton med Microsoft Graph](manage-user-accounts-graph-api.md).
