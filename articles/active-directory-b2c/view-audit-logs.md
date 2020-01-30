---
title: Åtkomst och granska gransknings loggar
titleSuffix: Azure AD B2C
description: Få åtkomst till Azure AD B2C gransknings loggar program mässigt och i Azure Portal.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/16/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 5695968973c7446220d8d77b84dfebb4a23ae8c7
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847763"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Åtkomst till Azure AD B2C gransknings loggar

Azure Active Directory B2C (Azure AD B2C) genererar gransknings loggar som innehåller aktivitets information om B2C-resurser, token som utfärdats och administratörs åtkomst. Den här artikeln innehåller en kort översikt över den information som är tillgänglig i gransknings loggar och instruktioner om hur du kommer åt dessa data för din Azure AD B2C-klient.

Gransknings logg händelser behålls endast i **sju dagar**. Planera att ladda ned och lagra dina loggar med någon av de metoder som visas nedan om du behöver en längre kvarhållningsperiod.

> [!NOTE]
> Du kan inte se användar inloggningar för enskilda Azure AD B2C program under avsnittet **användare** i **Azure Active Directory** eller **Azure AD B2C** sidor i Azure Portal. Inloggnings händelserna visar användar aktivitet, men kan inte korreleras tillbaka till det B2C-program som användaren har loggat in på. Du måste använda gransknings loggarna för den, enligt beskrivningen i den här artikeln.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Översikt över tillgängliga aktiviteter i B2C-kategorin för gransknings loggar

Kategorin **B2C** i gransknings loggar innehåller följande typer av aktiviteter:

|Aktivitetstyp |Beskrivning  |
|---------|---------|
|Autentisering |Aktiviteter som rör auktorisering av en användare för att få åtkomst till B2C-resurser (till exempel en administratör som har åtkomst till en lista över B2C-principer).         |
|Katalog |Aktiviteter relaterade till katalogattribut som hämtats när en administratör loggar in med hjälp av Azure Portal. |
|Program | Skapa, läsa, uppdatera och ta bort (CRUD) åtgärder på B2C-program. |
|Nyckel |CRUD åtgärder för nycklar som lagras i en B2C Key-behållare. |
|Resurs |CRUD-åtgärder på B2C-resurser. Till exempel principer och identitets leverantörer.
|Autentisering |Validering av användarautentiseringsuppgifter och utfärdande av token.|

För CRUD-aktiviteter för användar objekt, se kategorin **kärn katalog** .

## <a name="example-activity"></a>Exempel aktivitet

I den här exempel bilden från Azure Portal visas de data som registrerats när en användare loggar in med en extern identitetsprovider, i det här fallet Facebook:

![Exempel på informations sidan om gransknings loggs aktiviteter i Azure Portal](./media/view-audit-logs/audit-logs-example.png)

Panelen aktivitets information innehåller följande relevanta information:

|Section|Field|Beskrivning|
|-------|-----|-----------|
| Aktivitet | Namn | Vilken aktivitet som ägde rum. Du kan till exempel *utfärda en id_token till programmet*, vilket avslutar den faktiska användar inloggningen. |
| Initierad av (aktör) | ObjectId | **Objekt-ID** för det B2C-program som användaren loggar in på. Den här identifieraren visas inte i Azure Portal, men är tillgänglig via Microsoft Graph API. |
| Initierad av (aktör) | Namn | **Program-ID** för det B2C-program som användaren loggar in på. |
| Mål (ar) | ObjectId | **Objekt-ID** för den användare som loggar in. |
| Ytterligare information | TenantId | **Klient-ID** för Azure AD B2C klient organisationen. |
| Ytterligare information | `PolicyId` | **Princip-ID: t** för det användar flöde (princip) som används för att signera användaren i. |
| Ytterligare information | ApplicationId | **Program-ID** för det B2C-program som användaren loggar in på. |

## <a name="view-audit-logs-in-the-azure-portal"></a>Visa gransknings loggar i Azure Portal

Azure Portal ger till gång till Gransknings logg händelser i Azure AD B2C klienten.

1. Logga in på [Azure-portalen](https://portal.azure.com)
1. Växla till den katalog som innehåller Azure AD B2C klienten och bläddra sedan till **Azure AD B2C**.
1. Under **aktiviteter** i den vänstra menyn väljer du **gransknings loggar**.

En lista över aktivitets händelser som loggats under de senaste sju dagarna visas.

![Exempel filter med två aktivitets händelser i Azure Portal](./media/view-audit-logs/audit-logs-example-filter.png)

Det finns flera filtrerings alternativ, inklusive:

* **Resurs typ för aktivitet** – filtrera efter de aktivitets typer som visas i tabellen i avsnittet [Översikt över tillgängliga aktiviteter](#overview-of-activities-available-in-the-b2c-category-of-audit-logs) .
* **Datum** – filtrera datum intervallet för de aktiviteter som visas.

Om du väljer en rad i listan visas aktivitets informationen för händelsen.

Om du vill hämta listan över aktivitets händelser i en fil med kommaavgränsade värden (CSV) väljer du **Hämta**.

## <a name="get-audit-logs-with-the-azure-ad-reporting-api"></a>Hämta gransknings loggar med Azure AD repor ting API

Gransknings loggar publiceras i samma pipeline som andra aktiviteter för Azure Active Directory, så att de kan nås via [rapporterings-API: et för Azure Active Directory](https://docs.microsoft.com/graph/api/directoryaudit-list). Mer information finns i [Kom igång med API för Azure Active Directory rapportering](../active-directory/reports-monitoring/concept-reporting-api.md).

### <a name="enable-reporting-api-access"></a>Aktivera rapporterings-API-åtkomst

Om du vill tillåta skript-eller programbaserad åtkomst till Azure AD repor ting-API: n måste du ha ett Azure Active Directory-program registrerat i Azure AD B2C-klienten med följande API-behörigheter:

* Microsoft Graph > program behörigheter > AuditLog. Read. all

Du kan aktivera de här behörigheterna för en befintlig Azure Active Directory program registrering i B2C-klienten eller skapa en ny som är specifik för användning med gransknings logg automatisering.

Följ de här stegen registrera ett program, tilldela det nödvändiga Microsoft Graph API-behörigheter och skapa sedan en klient hemlighet.

### <a name="register-application-in-azure-active-directory"></a>Registrera program i Azure Active Directory

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="assign-api-access-permissions"></a>Tilldela API-åtkomst behörigheter

#### <a name="applicationstabapplications"></a>[Program](#tab/applications/)

1. På sidan **registrerad app** -översikt väljer du **Inställningar**.
1. Under **API-åtkomst**väljer du **nödvändiga behörigheter**.
1. Välj **Lägg till**och **Välj sedan ett API**.
1. Välj **Microsoft Graph**och **Välj**sedan.
1. Under **program behörigheter**väljer du **Läs alla Gransknings logg data**.
1. Välj knappen **Välj** och välj sedan **färdig**.
1. Välj **bevilja**, och välj sedan **Ja**.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Appregistreringar (för hands version)](#tab/app-reg-preview/)

1. Under **Hantera**, Välj **API-behörigheter**.
1. Under **konfigurerade behörigheter**väljer du **Lägg till en behörighet**.
1. Välj fliken **Microsoft API: er** .
1. Välj **Microsoft Graph**.
1. Välj **Programbehörigheter**.
1. Expandera **AuditLog** och markera kryss rutan **AuditLog. Read. all** .
1. Välj **Lägg till behörigheter**. Vänta några minuter innan du fortsätter till nästa steg.
1. Välj **bevilja administrativt godkännande för (ditt klient namn)** .
1. Välj ditt inloggade konto om det har tilldelats rollen som *Global administratör* eller logga in med ett konto i Azure AD B2C-klienten som har tilldelats rollen som *Global administratör* .
1. Välj **Acceptera**.
1. Välj **Uppdatera**och verifiera sedan att "beviljat..." visas under **status** för *AuditLog. Read. all* behörighet. Det kan ta några minuter innan behörigheterna har spridits.

* * *

### <a name="create-client-secret"></a>Skapa klient hemlighet

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Nu har du ett program med nödvändig API-åtkomst, ett program-ID och en nyckel som du kan använda i dina Automation-skript. I avsnittet om PowerShell-skript längre fram i den här artikeln finns ett exempel på hur du kan hämta aktivitets händelser med ett skript.

### <a name="access-the-api"></a>Åtkomst till API: et

Om du vill hämta Azure AD B2C Gransknings logg händelser via API: et, filtrerar du loggarna i `B2C` kategorin. Om du vill filtrera efter kategori använder du parametern `filter` frågesträng när du anropar slut punkten för Azure AD repor ting API.

```HTTP
https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$filter=loggedByService eq 'B2C' and activityDateTime gt 2019-09-10T02:28:17Z
```

### <a name="powershell-script"></a>PowerShell-skript

Följande PowerShell-skript visar ett exempel på hur du frågar Azure AD repor ting-API: et. Efter att ha frågat API: et skrivs de loggade händelserna ut till standardutdata och skriver sedan JSON-utdata till en fil.

Du kan testa det här skriptet i [Azure Cloud Shell](overview.md). Se till att du uppdaterar den med ditt program-ID, klient hemlighet och namnet på din Azure AD B2C klient.

```powershell
# This script requires the registration of a Web Application in Azure Active Directory:
# https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-reporting-api

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's client ID, a GUID (registered by Global Admin)
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's client secret
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # Insert your Azure AD B2C tenant; for example, contoso.onmicrosoft.com
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

Här är en JSON-representation av exempel aktivitets händelsen som visades tidigare i artikeln:

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

Du kan automatisera andra administrations uppgifter, till exempel [Hantera användare med .net](manage-user-accounts-graph-api.md).
