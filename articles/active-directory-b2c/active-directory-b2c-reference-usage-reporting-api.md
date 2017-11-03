---
title: "Azure Active Directory B2C: Användning reporting API samples och definitioner | Microsoft Docs"
description: "Guiden och exempel på att få rapporter om Azure AD B2C-klient användare, autentiseringar och Multi-Factor Authentication"
services: active-directory-b2c
documentationcenter: dev-center-name
author: rojasja
manager: mbaldwin
ms.service: active-directory-b2c
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/04/2017
ms.author: joroja
ms.openlocfilehash: 52180b760046d273c5a75720df0a73532d0343ad
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="accessing-usage-reports-in-azure-ad-b2c-via-the-reporting-api"></a>Åtkomst till användningsrapporter i Azure AD B2C via reporting API

Azure Active Directory B2C (Azure AD B2C) ger autentisering baserat på användarens inloggning och Azure Multi-Factor Authentication. Autentisering har angetts för slutanvändare i din familj program över identitetsleverantörer. När du vet hur många användare som har registrerats i innehavaren, providers som används för att registrera och antalet autentiseringar med typen kan du svara på frågor som:
* Hur många användare från varje typ av identitetsleverantör (till exempel en Microsoft eller LinkedIn-konto) har registrerats under de senaste 10 dagarna?
* Hur många autentiseringar med Multi-Factor Authentication har slutförts i den senaste månaden?
* Hur många tecken i baserade autentiseringar har slutfört den här månaden? Per dag? Per program?
* Hur kan jag för att beräkna den förväntade månatliga kostnaden för aktiviteten min Azure AD B2C-klient?

Den här artikeln fokuserar på rapporter som är knutna till fakturering aktivitet som baseras på antalet användare, fakturerbar logga-baserat autentiseringar och Multi-Factor Authentication.


## <a name="prerequisites"></a>Krav
Innan du börjar måste du slutföra stegen i [krav för att få åtkomst till Azure AD reporting API: er](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/). Skapa ett program, skaffa en hemlighet för den och ge det åtkomst rättigheter till din Azure AD B2C-klient-rapporter. *Bash skriptet* och *Python-skriptet* exempel tillhandahålls också här. 

## <a name="powershell-script"></a>PowerShell-skript
Det här skriptet visar skapandet av fyra användningsrapporter med hjälp av den `TimeStamp` parameter och `ApplicationId` filter.

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


## <a name="usage-report-definitions"></a>Användning rapportdefinitioner
* **tenantUserCount**: antalet användare i innehavaren av typ av identitetsleverantör per dag under de senaste 30 dagarna. (Du kan också en `TimeStamp` filtret innehåller användaren antal från det angivna datumet till dagens datum). Den här rapporten innehåller:
  * **TotalUserCount**: antalet alla användarobjekt.
  * **OtherUserCount**: antalet Azure Active Directory-användare (inte Azure AD B2C-användare).
  * **LocalUserCount**: antal användarkonton för Azure AD B2C lokala till Azure AD B2C-klient skapas med autentiseringsuppgifter.

* **AlternateIdUserCount**: antalet Azure AD B2C-användare som har registrerats med externa identitetsleverantörer (till exempel Facebook, ett Microsoft-konto eller ett annat Azure Active Directory-klient, kallas även en `OrgId`).

* **b2cAuthenticationCountSummary**: Sammanfattning av dagliga antalet fakturerbar autentiseringar under de senaste 30 dagarna, per dag och typ av autentiseringsflödet.

* **b2cAuthenticationCount**: Antal autentiseringar inom en angiven tidsperiod. Standardvärdet är de senaste 30 dagarna.  (Valfritt: början och slutet `TimeStamp` parametrar som definierar en viss tidsperiod.) Utdata innehåller `StartTimeStamp` (tidigaste datumet för aktiviteten för den här klienten) och `EndTimeStamp` (senaste uppdatering).

* **b2cMfaRequestCountSummary**: Sammanfattning av dagliga antalet Multi-Factor Authentication per dag och typ (SMS eller röst).


## <a name="limitations"></a>Begränsningar
Antal användardata uppdateras var 24 till 48 timmar. Autentiseringar uppdateras flera gånger om dagen. När du använder den `ApplicationId` filter, ett tomt rapporten svar kan bero på något av följande villkor:
  * Program-ID finns inte i klientorganisationen. Kontrollera att den är korrekt.
  * Program-ID finns, men inga data hittades under rapporteringsperioden. Granska parametrarna datum/tid.


## <a name="next-steps"></a>Nästa steg
### <a name="monthly-bill-estimates-for-azure-ad"></a>Månadsfaktura beräknar för Azure AD
När den kombineras med [senaste Azure AD B2C prissättning tillgängliga](https://azure.microsoft.com/pricing/details/active-directory-b2c/), du kan beräkna dagliga, veckovisa och månatliga Azure-förbrukningen.  En uppskattning är särskilt användbart när du planerar för ändringar i klient-funktioner som kan påverka övergripande kostnader. Du kan granska faktiska kostnader i din [länkade Azure-prenumeration](active-directory-b2c-how-to-enable-billing.md).

### <a name="options-for-other-output-formats"></a>Alternativ för andra utdataformat
Följande kod visar exempel för att skicka utdata till JSON, en lista med namn på värden och XML:
```powershell
# to output to JSON use following line in the PowerShell sample
$myReport.Content | Out-File -FilePath b2cUserJourneySummaryEvents.json -Force

# to output the content to a name value list
($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath name-your-file.txt -Force

# to output the content in XML use the following line
(($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath name-your-file.xml -Force
```
