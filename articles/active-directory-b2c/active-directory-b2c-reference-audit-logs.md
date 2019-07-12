---
title: Granskningsloggar exempel och definitioner i Azure Active Directory B2C | Microsoft Docs
description: Guiden och exempel om hur du använder Azure AD B2C-granskningsloggar.
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
ms.openlocfilehash: 216f5413ce3dae1f2d040643a30a4d7db4a879b8
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835402"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Åtkomst till Azure AD B2C-granskningsloggar

Azure Active Directory B2C (Azure AD B2C) genererar granskningsloggar som innehåller information om B2C-resurser, utfärdade token och administratörsåtkomst systemaktivitet. Den här artikeln innehåller en kort översikt över informationen som är tillgänglig via granskningsloggar och instruktioner om hur du kommer åt dessa data för din Azure AD B2C-klient.

> [!IMPORTANT]
> Granskningsloggar behålls endast i sju dagar. Planera att hämta och lagra dina loggar med någon av metoderna nedan om du behöver en längre period.

> [!NOTE]
> Du kan inte se användarinloggningar för enskilda Azure AD B2C-program under den **användare** delen av den **Azure Active Directory** eller **Azure AD B2C** blad. Inloggningar det visar användaraktivitet, men det går inte att korreleras tillbaka till B2C-programmet som användaren har loggat in till. Du måste använda granskningsloggarna för det som beskrivs ytterligare i den här artikeln.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Översikt över aktiviteter som är tillgängliga i kategorin B2C på granskningsloggar
Den **B2C** kategori i granskningsloggarna innehåller följande typer av aktiviteter:

|Aktivitetstyp |Beskrivning  |
|---------|---------|
|Authorization |Aktiviteter om tillståndet för en användare att komma åt B2C resurser (exempelvis kan en administratör åtkomst till en lista med B2C-principer)         |
|Katalog |Aktiviteter relaterade till katalogattribut hämtas när en administratör loggar in med Azure portal |
|Program | CRUD-åtgärder på B2C-program |
|Nyckel |CRUD-åtgärder på nycklar som lagras i B2C-nyckelbehållare |
|Resource |CRUD-åtgärder på B2C-resurser (till exempel principer och Identitetsproviders)
|Authentication |Verifiering av autentiseringsuppgifter för användare och utfärdande|

> [!NOTE]
> Objektet CRUD användaraktiviteter, finns det **Kärnkatalog** kategori.

## <a name="example-activity"></a>Exemplet på aktivitet
Exemplet nedan visar data som hämtats när en användare loggar in med en extern identitetsprovider: ![Exempel på sidan Granska loggen aktivitetsinformation på Azure portal](./media/active-directory-b2c-reference-audit-logs/audit-logs-example.png)

Informationspanel aktivitet innehåller följande relevant information:

|Section|Fält|Beskrivning|
|-------|-----|-----------|
| Aktivitet | Namn | Vilka aktiviteten ägde rum. Till exempel ”utfärda ett id_token för programmet” (vilket avslutar den faktiska användare logga in). |
| Initierad av (aktör) | ObjectId | Den **objekt-ID** till B2C-programmet som användaren loggar in till (den här identifieraren visas inte i Azure-portalen men den är tillgänglig via Graph API till exempel). |
| Initierad av (aktör) | SPN | Den **program-ID** till B2C-programmet som användaren loggar in till. |
| Mål | ObjectId | Den **objekt-ID** för den användare som loggar in. |
| Ytterligare information | TenantId | Den **klient-ID** för Azure AD B2C-klient. |
| Ytterligare information | `PolicyId` | Den **princip-ID** av användarflöde (principen) som används för att logga in användaren. |
| Ytterligare information | ApplicationId | Den **program-ID** till B2C-programmet som användaren loggar in till. |

## <a name="accessing-audit-logs-through-the-azure-portal"></a>Få åtkomst till granskningsloggar via Azure portal
1. Gå till [Azure-portalen](https://portal.azure.com). Kontrollera att du är i din B2C-katalog.
2. Klicka på **Azure Active Directory** i fältet Favoriter till vänster

    ![Azure Active Directory-knappen som är markerad i vänster meny på portalen](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-aad.png)

1. Under **aktivitet**, klicka på **granskningsloggar**

    ![Granska loggar knappen är markerad i avsnittet aktivitet i menyn](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-section.png)

2. I den **kategori** dropbox, väljer **B2C**
3. Klicka på **gäller**

    ![Kategori och Verkställ markerat i granskningsloggen filter](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-category.png)

Du kommer se en lista över aktiviteter loggas under de senaste sju dagarna.
- Använd den **Aktivitetsresurstyp** listrutan för att filtrera efter aktivitetstyper som beskrivs ovan
- Använd den **datumintervall** listrutan för att filtrera datumintervallet för de aktiviteter som visas
- Om du klickar på en specifik rad i listan över visas en sammanhangsberoende rutan till höger ytterligare attribut som är associerat med aktiviteten
- Klicka på **hämta** att hämta aktiviteterna som en csv-fil

> [!NOTE]
> Du kan också se granskningsloggarna genom att gå till **Azure AD B2C** snarare än **Azure Active Directory** i fältet Favoriter till vänster. Under **aktiviteter**, klicka på **granskningsloggar**, där du hittar samma loggar med liknande filtreringsfunktioner.

## <a name="accessing-audit-logs-through-the-azure-ad-reporting-api"></a>Få åtkomst till granskningsloggar via Azure AD reporting-API
Granskningsloggar publiceras till samma pipelinen som andra aktiviteter för Azure Active Directory, så att de kan nås via den [Azure Active Directory reporting API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference).

### <a name="prerequisites"></a>Förutsättningar
För att autentisera till Azure AD reporting API: et måste du först registrera ett program. Se till att följa stegen i [krav för att få åtkomst till Azure AD reporting API: er](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/).

### <a name="accessing-the-api"></a>Åtkomst till API: et
Om du vill ladda ned Azure AD B2C-granskningsloggar via API: et, bör du filterloggar till den **B2C** kategori. Om du vill filtrera efter kategori, använder du parametern för frågesträngen när du anropar Azure AD reporting API-slutpunkter, enligt nedan:

`https://graph.windows.net/your-b2c-tentant.onmicrosoft.com/activities/audit?api-version=beta&$filter=category eq 'B2C'`

### <a name="powershell-script"></a>PowerShell-skript
Följande skript innehåller ett exempel på hur du använder PowerShell för att fråga efter Azure AD reporting API: et och lagrar resultatet som en JSON-fil:

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
