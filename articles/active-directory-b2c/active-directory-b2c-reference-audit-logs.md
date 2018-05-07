---
title: 'Azure Active Directory B2C: Granskningsloggar exemplen och definitioner'
description: Guide och exempel på åtkomst till Azure AD B2C-granskningsloggar
services: active-directory-b2c
author: sromeroz
manager: sasubram
ms.author: sezambra
ms.service: active-directory-b2c
ms.topic: article
ms.workload: identity
ms.date: 08/04/2017
ms.openlocfilehash: 8cc48853b0677230c4e19df4f0ecd93ce88b119d
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Åtkomst till Azure AD B2C granskningsloggar

Azure Active Directory B2C (Azure AD B2C) genererar granskningsloggar som innehåller aktivitetsinformation om B2C resurser, utfärdade token och administratörsåtkomst. Den här artikeln innehåller en kort översikt över information som är tillgängliga via granskningsloggar och instruktioner om hur du kommer åt dessa data för din Azure AD B2C-klient.

> [!IMPORTANT]
> Granskningsloggar behålls endast under sju dagar. Planera att ladda ned och lagra loggarna genom att använda någon av metoderna nedan om du behöver en längre period. 

##<a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Översikt över aktiviteter som är tillgängliga i kategorin B2C på granskningsloggar
Den **B2C** kategori i granskningsloggarna innehåller följande typer av aktiviteter:
|Aktivitetstyp |Beskrivning  |
|---------|---------|
|Auktorisering |Aktiviteter om tillståndet för en användare att komma åt B2C resurser (t.ex, en administratör att komma åt en lista över B2C principer)         |
|Katalog |Aktiviteter som rör katalogattribut hämtas när en administratör loggar in med hjälp av Azure-portalen |
|Program | CRUD-åtgärder på B2C-program |
|Nyckel |CRUD-åtgärder på nycklar som lagras i B2C-nyckelbehållare |
|Resurs |CRUD-åtgärder på B2C-resurser (till exempel principer och identitetsleverantörer)
|Autentisering |Verifiering av autentiseringsuppgifter för användare och utfärdande|

> [!NOTE]
> Objektet CRUD användaraktiviteter, finns det **Core Directory** kategori.

##<a name="example-activity"></a>Exemplet aktivitet
Exemplet nedan visar data som hämtats när en användare loggar in med en extern identitetsleverantör: ![granskningsloggar - exempel](./media/active-directory-b2c-reference-audit-logs/audit-logs-example.png)

##<a name="accessing-audit-logs-through-the-azure-portal"></a>Komma åt granskningsloggar via Azure Portal
1. Gå till [Azure-portalen](https://portal.azure.com). Kontrollera att du befinner dig i din B2C-katalog.
2. Klicka på **Azure Active Directory** i fältet Favoriter till vänster 
    
    ![Granskningsloggar - AAD-knappen](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-aad.png)

1. Under **aktiviteten**, klicka på **granskningsloggar**

    ![Granskningsloggar - loggar avsnitt](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-section.png)

2. I den **kategori** dropbox, Välj **B2C**
3. Klicka på **tillämpas**

    ![Granskningsloggar - kategori](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-category.png)

Du kommer se en lista över aktiviteter loggas under de senaste sju dagarna. 
- Använd den **aktivitet resurstypen** listrutan för att filtrera efter aktivitetstyper som beskrivs ovan
- Använd den **datumintervall** listrutan för att filtrera datumintervallet för de aktiviteter som visas
- Om du klickar på en specifik rad i listan över visas en kontextuella rutan till höger ytterligare attribut som är associerade med aktiviteten
- Klicka på **hämta** att hämta aktiviteterna som en csv-fil

##<a name="accessing-audit-logs-through-the-azure-ad-reporting-api"></a>Komma åt granskningsloggar via Azure AD reporting API
Granskningsloggar publiceras till samma rörledning som andra aktiviteter för Azure Active Directory, så att de kan nås via de [Azure Active Directory reporting API](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-api-audit-reference). 

###<a name="prerequisites"></a>Förutsättningar
Om du vill autentisera till Azure AD reporting API måste du först registrera ett program. Se till att följa stegen i [krav för att få åtkomst till Azure AD reporting API: er](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/).

###<a name="accesing-the-api"></a>Åtkomst till API
Ladda ned Azure AD B2C granskningsloggarna via API: et, ska du loggfilerna för att filtrera de **B2C** kategori. Om du vill filtrera efter kategori, använder du frågesträngparametern vid anrop av Azure AD reporting API-slutpunkt som visas nedan:

`https://graph.windows.net/your-b2c-tentant.onmicrosoft.com/activities/audit?api-version=beta&$filter=category eq 'B2C'`

###<a name="powershell-script"></a>PowerShell-skript
Följande skript innehåller ett exempel på att använda PowerShell för att fråga Azure AD reporting API och spara resultatet som en JSON-fil:

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
    Do{
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

