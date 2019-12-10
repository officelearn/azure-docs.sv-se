---
title: API-exempel och definitioner för användnings rapportering
titleSuffix: Azure AD B2C
description: Guide och exempel på hur du får rapporter om Azure AD B2C klient användare, autentiseringar och Multi-Factor Authentication.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f81acf28b502965f896cd8b38767e7c2e925156c
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949346"
---
# <a name="accessing-usage-reports-in-azure-ad-b2c-via-the-reporting-api"></a>Få åtkomst till användnings rapporter i Azure AD B2C via rapporterings-API: et

Azure Active Directory B2C (Azure AD B2C) tillhandahåller autentisering baserat på användar inloggning och Azure-Multi-Factor Authentication. Autentisering anges för slutanvändare av din program familj över identitets leverantörer. När du vet hur många användare som registrerats i klienten, vilka leverantörer de använde för att registrera sig och antalet autentiseringar efter typ, kan du svara på frågor som:
* Hur många användare från varje typ av identitetsprovider (till exempel ett Microsoft-eller LinkedIn-konto) har registrerats under de senaste 10 dagarna?
* Hur många autentiseringar som använder Multi-Factor Authentication har slutförts under den senaste månaden?
* Hur många inloggnings-baserade autentiseringar slutfördes den här månaden? Per dag? Per program?
* Hur kan jag beräkna den förväntade månads kostnaden för min Azure AD B2C klient aktivitet?

Den här artikeln fokuserar på rapporter som är kopplade till fakturerings aktiviteter, som baseras på antalet användare, fakturerbara inloggnings-baserade autentiseringar och Multi-Factor Authentication.


## <a name="prerequisites"></a>Krav
Innan du börjar måste du slutföra stegen i [kraven för att få åtkomst till Azure AD repor ting-API: erna](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/). Skapa ett program, skaffa en hemlighet för det och ge åtkomst behörighet till din Azure AD B2C klients rapporter. *Bash-skript* och *Python-skript* exempel finns också här.

## <a name="powershell-script"></a>PowerShell-skript
Det här skriptet visar hur du skapar fyra användnings rapporter med hjälp av `TimeStamp`-parametern och `ApplicationId`s filtret.

```powershell
# This script will require the Web Application and permissions setup in Azure Active Directory

# Constants
$ClientID      = "your-client-application-id-here"
$ClientSecret  = "your-client-application-secret-here"
$loginURL      = "https://login.microsoftonline.com"
$tenantdomain  = "your-b2c-tenant-domain.onmicrosoft.com"
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body          = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth         = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
if ($oauth.access_token -ne $null) {
    $headerParams  = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    Write-host Data from the tenantUserCount report
    Write-host ====================================================
     # Returns a JSON document for the report
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the tenantUserCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?%24filter=TimeStamp+gt+2016-10-15&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCountSummary report
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=TimeStamp+gt+2016-09-20+and+TimeStamp+lt+2016-10-03&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with ApplicationId filter
    Write-host ====================================================
    # Returns a JSON document for the " " report
        $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCountSummary
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCount?%24filter=TimeStamp+gt+2016-09-10+and+TimeStamp+lt+2016-10-04&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with ApplicationId filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
     Write-host $myReport.Content

} else {
    Write-Host "ERROR: No Access Token"
    }
```


## <a name="usage-report-definitions"></a>Användnings rapport definitioner
* **tenantUserCount**: antalet användare i klient organisationen efter typ av identitetsprovider, per dag under de senaste 30 dagarna. (Om du vill kan ett `TimeStamp`-filter ge användar antal från ett angivet datum till dagens datum). Rapporten innehåller:
  * **TotalUserCount**: antalet alla användar objekt.
  * **OtherUserCount**: antalet Azure Active Directory användare (inte Azure AD B2C användare).
  * **LocalUserCount**: antalet Azure AD B2C-användarkonton som skapats med lokala autentiseringsuppgifter till Azure AD B2C klienten.

* **AlternateIdUserCount**: antalet Azure AD B2C användare som registrerats med externa identitets leverantörer (till exempel Facebook, en Microsoft-konto eller en annan Azure Active Directory klient organisation, även kallat en `OrgId`).

* **b2cAuthenticationCountSummary**: översikt över det dagliga antalet fakturerbara autentiseringar under de senaste 30 dagarna, per dag och typ av autentiseringspaket.

* **b2cAuthenticationCount**: antalet autentiseringar inom en tids period. Standardvärdet är de senaste 30 dagarna.  (Valfritt: de inledande och avslutande `TimeStamp` parametrarna definierar en viss tids period.) Utdata innehåller `StartTimeStamp` (tidigaste datum för aktiviteten för den här klienten) och `EndTimeStamp` (senaste uppdatering).

* **b2cMfaRequestCountSummary**: översikt över det dagliga antalet Multi-Factor Authentication, per dag och typ (SMS eller Voice).


## <a name="limitations"></a>Begränsningar
Data för antal användare uppdateras var 24 till 48 timmar. Autentiseringar uppdateras flera gånger per dag. När du använder `ApplicationId`-filtret kan ett tomt rapport svar bero på ett av följande villkor:
  * Program-ID: t finns inte i klient organisationen. Kontrol lera att det är korrekt.
  * Program-ID: t finns, men det gick inte att hitta några data i rapporterings perioden. Granska dina datum-/tids parametrar.


## <a name="next-steps"></a>Nästa steg
### <a name="monthly-bill-estimates-for-azure-ad"></a>Månads fakturerings uppskattningar för Azure AD
När de kombineras med [den mest aktuella Azure AD B2C prissättningen](https://azure.microsoft.com/pricing/details/active-directory-b2c/)kan du uppskatta varje dag, veckovis och månatlig Azure-förbrukning.  En uppskattning är särskilt användbar när du planerar ändringar i klientens beteende som kan påverka den totala kostnaden. Du kan granska de faktiska kostnaderna i den [länkade Azure-prenumerationen](active-directory-b2c-how-to-enable-billing.md).

### <a name="options-for-other-output-formats"></a>Alternativ för andra utdataformat
Följande kod visar exempel på hur du skickar utdata till JSON, en namn värde lista och XML:
```powershell
# to output to JSON use following line in the PowerShell sample
$myReport.Content | Out-File -FilePath name-your-file.json -Force

# to output the content to a name value list
($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath name-your-file.txt -Force

# to output the content in XML use the following line
(($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath name-your-file.xml -Force
```
