---
title: Användning reporting API-exempel och definitioner i Azure Active Directory B2C | Microsoft Docs
description: Guiden och exempel på att få rapporter för Azure AD B2C-klientorganisation, användare, autentiseringar och multifaktorautentiseringar.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 08/04/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: eb8c41f657d8901535ba284c147f4ca3aa86198d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64703628"
---
# <a name="accessing-usage-reports-in-azure-ad-b2c-via-the-reporting-api"></a>Åtkomst till användningsrapporter i Azure AD B2C via rapporterings-API

Azure Active Directory B2C (Azure AD B2C) tillhandahåller autentisering baserat på användare logga in och Azure Multi-Factor Authentication. Autentisering har angetts för slutanvändare i din familj för program över identitetsleverantörer. När du vet hur många användare som har registrerats i klientorganisationen, providers som används för att registrera och antalet autentiseringar efter typ, kan du besvara frågor som:
* Hur många användare från varje typ av identitetsprovider (till exempel en Microsoft och LinkedIn-konto) har registrerats under de senaste 10 dagarna?
* Hur många autentiseringar med Multi-Factor Authentication har slutförts under den senaste månaden?
* Hur många logga-i-baserade autentiseringar har slutfört den här månaden? Per dag? Per program?
* Hur beräknar jag förväntade månadskostnaden för min Azure AD B2C-klientaktivitet?

Den här artikeln fokuserar på rapporter som är kopplad till faktureringen som baseras på antalet användare, fakturerbara logga-i-baserade autentiseringar och multifaktorautentiseringar.


## <a name="prerequisites"></a>Nödvändiga komponenter
Innan du börjar måste du slutföra stegen i [krav för att få åtkomst till Azure AD reporting API: er](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/). Skapa ett program, hämta en hemlighet för den och ge det åtkomst rättigheter till din Azure AD B2C-klient-rapporter. *Bash-skript* och *Python-skriptet* exempel tillhandahålls även här. 

## <a name="powershell-script"></a>PowerShell-skript
Det här skriptet visar skapandet av fyra användningsrapporter med hjälp av den `TimeStamp` parametern och `ApplicationId` filter.

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


## <a name="usage-report-definitions"></a>Användning-rapportdefinitioner
* **tenantUserCount**: Antalet användare i klienten efter typ av identitetsprovider per dag under de senaste 30 dagarna. (Du kan också en `TimeStamp` -filtret innehåller antalet användare från ett visst datum till aktuellt datum). Rapporten innehåller:
  * **TotalUserCount**: Antal alla användarobjekt.
  * **OtherUserCount**: Antalet Azure Active Directory-användare (inte Azure AD B2C-användare).
  * **LocalUserCount**: Antalet Azure AD B2C-användarkonton som skapats med autentiseringsuppgifter för lokal till Azure AD B2C-klient.

* **AlternateIdUserCount**: Antalet Azure AD B2C-användare som har registrerats med externa indentitetsprovidrar (till exempel Facebook, ett Microsoft-konto eller en annan Azure Active Directory-klient, kallas även en `OrgId`).

* **b2cAuthenticationCountSummary**: Sammanfattning av det dagliga antalet fakturerbara autentiseringar under de senaste 30 dagarna, per dag och typ av autentiseringsflödet.

* **b2cAuthenticationCount**: Antalet autentiseringar inom en tidsperiod. Standardvärdet är de senaste 30 dagarna.  (Valfritt: Början och slutet `TimeStamp` parametrar definiera en viss tidsperiod.) Utdata innehåller `StartTimeStamp` (tidigaste datum för aktivitet för den här klienten) och `EndTimeStamp` (senaste uppdatering).

* **b2cMfaRequestCountSummary**: Sammanfattning av det dagliga antalet multifaktorautentiseringar per dag och typ (SMS eller röst).


## <a name="limitations"></a>Begränsningar
Antal användardata uppdateras var 24 till 48 timmar. Autentiseringar uppdateras flera gånger per dag. När du använder den `ApplicationId` filter, en tom rapport response kan bero på något av följande villkor:
  * Program-ID finns inte i klienten. Kontrollera att den är korrekt.
  * Program-ID finns, men inga data hittades i rapporteringsperioden. Granska parametrarna datum/tid.


## <a name="next-steps"></a>Nästa steg
### <a name="monthly-bill-estimates-for-azure-ad"></a>Månadsfaktura beräknar för Azure AD
När de kombineras med [de senaste Azure AD B2C priserna tillgängliga](https://azure.microsoft.com/pricing/details/active-directory-b2c/), du kan beräkna dagliga, veckovisa och månatliga Azure-förbrukning.  En uppskattning är särskilt användbart när du planerar för ändringar i klient-beteende som kan påverka sammanlagda kostnaden. Du kan granska faktiska kostnader i din [länkad Azure-prenumeration](active-directory-b2c-how-to-enable-billing.md).

### <a name="options-for-other-output-formats"></a>Alternativ för andra utdataformat
Följande kod visar exempel på skicka utdata till JSON, en lista med namn på värden och XML:
```powershell
# to output to JSON use following line in the PowerShell sample
$myReport.Content | Out-File -FilePath name-your-file.json -Force

# to output the content to a name value list
($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath name-your-file.txt -Force

# to output the content in XML use the following line
(($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath name-your-file.xml -Force
```
