---
title: Azure Active Directory reporting granska API samples | Microsoft Docs
description: Hur du kommer igång med Azure Active Directory Reporting API
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: de8b8ec3-49b3-4aa8-93fb-e38f52c99743
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/30/2018
ms.author: dhanyahk;rolyon
ms.reviewer: dhanyahk
ms.openlocfilehash: 77ecb1f0c4b3614c9692715edae21a09f261b277
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34588172"
---
# <a name="azure-active-directory-reporting-audit-api-samples"></a>Azure Active Directory reporting audit API-exempel
Den här artikeln är en del av en samling artiklar om Azure Active Directory reporting API.  
Azure AD-rapportering ger dig en API som gör att du kan komma åt granskningsdata via kod eller relaterade verktyg.
Omfånget för den här artikeln är att ge dig exempelkod för den **granska API**.

Se:

* [Granskningsloggar](active-directory-reporting-azure-portal.md#activity-reports) mer information
* [Komma igång med Azure Active Directory Reporting API](active-directory-reporting-api-getting-started.md) för mer information om reporting API.

För frågor, frågor eller kommentarer, kontakta [AAD Reporting hjälper](mailto:aadreportinghelp@microsoft.com).


## <a name="prerequisites"></a>Förutsättningar
Innan du kan använda exemplen i den här artikeln, måste du slutföra de [krav för att få åtkomst till Azure AD reporting API](active-directory-reporting-api-prerequisites.md).  

## <a name="known-issue"></a>Kända problem
Appen Auth fungerar inte om din klient är i EU-region. Använd autentisering av användare för att komma åt granska API som en tillfällig lösning förrän vi lösa problemet. 

## <a name="powershell-script"></a>PowerShell-skript


```powershell

# This script will require the Web Application and permissions setup in Azure Active Directory
$clientID       = "<appid>"             # ApplicationId
$clientSecret   = "<key>"         # Should be a ~44 character string insert your info here
$loginURL       = "https://login.windows.net/"
$tenantdomain   = "<domain>"            # For example, contoso.onmicrosoft.com
$msgraphEndpoint = "https://graph.microsoft.com"
$countOfAuditDocsToBeSavedInAFile = 2000
    
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$msgraphEndpoint;client_id=$clientID;client_secret=$clientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
    
if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    
    $url = "$msgraphEndpoint/beta/auditLogs/directoryAudits"
    Write-Output "Fetching data using Uri: $url"
    $i=0
    $docCount=0
    Do{
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        $jsonReport = ($myReport.Content | ConvertFrom-Json).value
        $fetchedRecordCount = $jsonReport.Count
        $docCount = $docCount + $fetchedRecordCount
        $totalFetchedRecordCount = $totalFetchedRecordCount + $fetchedRecordCount
        Write-Output "Fetched $fetchedRecordCount records and saved into Audits$i.json"
        if($docCount -le $countOfAuditDocsToBeSavedInAFile)
        {
            $myReport.Content | Out-File -FilePath Audits$i.json -append  -Force        
        }
        else
        {           
            $docCount=0
            $i = $i+1
        }
            
        #Get url from next link
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'         
    }while($url -ne $null)
    Write-Output "Total Fetched record count is : $totalFetchedRecordCount"
                
} else {
    Write-Host "ERROR: No Access Token"
}

```

### <a name="executing-the-powershell-script"></a>Kör PowerShell-skript
När du har redigerat skriptet körs och kontrollera att de förväntade data från revision loggar rapporten returneras.

Skriptet returnerar utdata från rapporten i JSON-format. Det skapar också en `Audits.json` fil med samma utdata. Du kan experimentera genom att ändra skriptet för att returnera data från andra rapporter och kommentera ut utdataformat som du inte behöver.





## <a name="next-steps"></a>Nästa steg
* Vill du anpassa exemplen i den här artikeln? Kolla in den [Azure Active Directory audit API-referens för](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit). 
* Om du vill se en fullständig översikt över med Azure Active Directory reporting API, se [komma igång med Azure Active Directory reporting API](active-directory-reporting-api-getting-started.md).
* Om du vill veta mer om Azure Active Directory reporting finns i [Azure Active Directory Reporting Guide](active-directory-reporting-guide.md).  

