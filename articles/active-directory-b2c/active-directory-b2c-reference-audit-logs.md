---
title: Exempel och definitioner för gransknings loggar i Azure Active Directory B2C | Microsoft Docs
description: Guide och exempel på hur du kommer åt Azure AD B2C gransknings loggar.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: d8cc67b8e243fb2b97cd1522a850adc63c84428e
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69969632"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Åtkomst till Azure AD B2C gransknings loggar

Azure Active Directory B2C (Azure AD B2C) genererar gransknings loggar som innehåller aktivitets information om B2C-resurser, utfärdade tokens och administratörs åtkomst. Den här artikeln innehåller en kort översikt över den information som är tillgänglig via gransknings loggar och instruktioner om hur du kommer åt dessa data för din Azure AD B2C-klient.

> [!IMPORTANT]
> Gransknings loggar behålls bara i sju dagar. Planera att ladda ned och lagra dina loggar med någon av de metoder som visas nedan om du behöver en längre kvarhållningsperiod.

> [!NOTE]
> Det går inte att se användar inloggningar för enskilda Azure AD B2C program under avsnittet **användare** på bladet **Azure Active Directory** eller **Azure AD B2C** . I inloggningarna visas användar aktivitet, men det går inte att korrelera tillbaka till det B2C-program som användaren har loggat in på. Du måste använda gransknings loggarna för den, enligt beskrivningen i den här artikeln.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Översikt över tillgängliga aktiviteter i B2C-kategorin för gransknings loggar
Kategorin **B2C** i gransknings loggar innehåller följande typer av aktiviteter:

|Aktivitetstyp |Beskrivning  |
|---------|---------|
|Authorization |Aktiviteter som rör auktorisering av en användare för att få åtkomst till B2C-resurser (till exempel en administratör som har åtkomst till en lista över B2C-principer)         |
|Katalog |Aktiviteter relaterade till katalogattribut som hämtats när en administratör loggar in med hjälp av Azure Portal |
|Program | CRUD-åtgärder på B2C-program |
|Nyckel |CRUD åtgärder för nycklar som lagras i B2C Key container |
|Resource |CRUD-åtgärder på B2C-resurser (till exempel principer och identitets leverantörer)
|Authentication |Validering av användarautentiseringsuppgifter och utfärdande av token|

> [!NOTE]
> För CRUD-aktiviteter för användar objekt, se kategorin **kärn katalog** .

## <a name="example-activity"></a>Exempel aktivitet
Exemplet nedan visar de data som fångas när en användare loggar in med en extern identitetsprovider:  
    ![Exempel på informations sidan om gransknings loggs aktiviteter i Azure Portal](./media/active-directory-b2c-reference-audit-logs/audit-logs-example.png)

Panelen aktivitets information innehåller följande relevanta information:

|Section|Fält|Beskrivning|
|-------|-----|-----------|
| Aktivitet | Name | Vilken aktivitet som ägde rum. Till exempel "utfärda en id_token till programmet" (som avslutar den faktiska användar inloggningen). |
| Initierad av (aktör) | ObjectId | **Objekt-ID:** t för det B2C-program som användaren loggar in på (denna identifierare visas inte i Azure Portal, men den är tillgänglig via Graph API till exempel). |
| Initierad av (aktör) | SPN | **Program-ID** för det B2C-program som användaren loggar in på. |
| Mål (ar) | ObjectId | **Objekt-ID** för den användare som loggar in. |
| Ytterligare information | TenantId | **Klient-ID** för Azure AD B2C klient organisationen. |
| Ytterligare information | `PolicyId` | **Princip-ID: t** för det användar flöde (princip) som används för att signera användaren i. |
| Ytterligare information | ApplicationId | **Program-ID** för det B2C-program som användaren loggar in på. |

## <a name="accessing-audit-logs-through-the-azure-portal"></a>Få åtkomst till gransknings loggar via Azure Portal
1. Gå till [Azure-portalen](https://portal.azure.com). Se till att du är i B2C-katalogen.
2. Klicka på **Azure Active Directory** i fältet Favoriter till vänster

    ![Azure Active Directory knapp markerad i menyn till vänster Portal](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-aad.png)

1. Under **aktivitet**klickar du på **gransknings loggar**

    ![Knappen gransknings loggar markerad i avsnittet aktivitet i menyn](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-section.png)

2. I **kategorin** Dropbox väljer du **B2C**
3. Klicka på **Verkställ**

    ![Kategori och tillämpa knapp markerad i gransknings logg filter](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-category.png)

Du kommer att se en lista över aktiviteter som loggats under de senaste sju dagarna.
- Använd List rutan **aktivitets resurs typ** för att filtrera efter de aktivitets typer som beskrivs ovan
- Använd List rutan **datum intervall** för att filtrera datum intervallet för de aktiviteter som visas
- Om du klickar på en enskild rad i listan visas en sammanhangsbaserad ruta till höger som visar ytterligare attribut som är associerade med aktiviteten
- Klicka på **Hämta** för att ladda ned aktiviteterna som en CSV-fil

> [!NOTE]
> Du kan också se gransknings loggarna genom att navigera till **Azure AD B2C** i stället för att **Azure Active Directory** i fältet Favoriter till vänster. Under **aktiviteter**klickar du på **gransknings loggar**där du kommer att hitta samma loggar med liknande filtrerings funktioner.

## <a name="accessing-audit-logs-through-the-azure-ad-reporting-api"></a>Åtkomst till gransknings loggar via Azure AD repor ting-API: et
Gransknings loggar publiceras i samma pipeline som andra aktiviteter för Azure Active Directory, så att de kan nås via rapporterings- [API: et för Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference).

### <a name="prerequisites"></a>Förutsättningar
För att autentisera till Azure AD repor ting-API: et måste du först registrera ett program. Se till att följa stegen i [krav för att komma åt Azure AD repor ting-API: erna](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/).

### <a name="accessing-the-api"></a>Åtkomst till API: et
Om du vill hämta Azure AD B2C gransknings loggar via API: et, vill du filtrera loggarna till kategorin **B2C** . Om du vill filtrera efter kategori använder du frågesträngparametern när du anropar slut punkten för Azure AD repor ting API: et enligt nedan:

`https://graph.windows.net/your-b2c-tentant.onmicrosoft.com/activities/audit?api-version=beta&$filter=category eq 'B2C'`

### <a name="powershell-script"></a>PowerShell-skript
Följande skript innehåller ett exempel på hur du använder PowerShell för att fråga Azure AD repor ting API och lagra resultatet som en JSON-fil:

```powershell
# This script will require registration of a Web Application in Azure Active Directory (see https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's Client ID, a Globally Unique ID (registered by Global Admin)
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's Client Key/Secret string
$loginURL       = "https://login.microsoftonline.com"
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # AAD B2C Tenant; for example, contoso.onmicrosoft.com
$resource       = "https://graph.windows.net"             # Azure AD Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 thru n for number of nextLink pages
if ($oauth.access_token -ne $null) {
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = 'https://graph.windows.net/' + $tenantdomain + '/activities/audit?api-version=beta&$filter=category eq ''B2C''and activityDate gt ' + $7daysago

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
