---
title: Azure Active Directory-inloggningsaktivitet, rapport-API-exempel | Microsoft Docs
description: Kom igång med Azure Active Directory Reporting API
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: c41c1489-726b-4d3f-81d6-83beb932df9c
ms.service: active-directory
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 05/31/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: b02c0ac482c13868606674a097a84976780b7ef6
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2018
ms.locfileid: "41919788"
---
# <a name="azure-active-directory-sign-in-activity-report-api-samples"></a>Azure Active Directory-inloggningsaktivitet, rapport-API-exempel
Den här artikeln är en del av en samling artiklar om Azure Active Directory rapporterings-API.  
Azure AD-rapportering ger dig en API som gör att du kan komma åt aktivitetsdata för inloggning med hjälp av kod eller relaterade verktyg.  
Syftet med den här artikeln är att ge dig exempelkod för **API för inloggningsaktivitet**.

Se:

* [Granskningsloggar](overview-reports.md#activity-reports) för mer information
* [Komma igång med Azure Active Directory Reporting API](concept-reporting-api.md) för mer information om rapporterings-API.


## <a name="prerequisites"></a>Nödvändiga komponenter
Innan du kan använda exemplen i den här artikeln, måste du slutföra [krav för att få åtkomst till Azure AD Reporting API](howto-configure-prerequisites-for-reporting-api.md).  

## <a name="powershell-script"></a>PowerShell-skript

```powershell

# This script will require the Web Application and permissions setup in Azure Active Directory
$clientID       = "<appid>"             # ApplicationId
$clientSecret   = "<key>"         # Should be a ~44 character string insert your info here
$loginURL       = "https://login.windows.net/"
$tenantdomain   = "<domain>"            # For example, contoso.onmicrosoft.com
$msgraphEndpoint = "https://graph.microsoft.com"
$countOfSignInDocsToBeSavedInAFile = 2000
    
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$msgraphEndpoint;client_id=$clientID;client_secret=$clientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
    
if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    
    $url = "$msgraphEndpoint/beta/auditLogs/signIns"
    Write-Output "Fetching data using Uri: $url"
    $i=0
    $docCount=0
    Do{
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        $jsonReport = ($myReport.Content | ConvertFrom-Json).value
        $fetchedRecordCount = $jsonReport.Count
        $docCount = $docCount + $fetchedRecordCount
        $totalFetchedRecordCount = $totalFetchedRecordCount + $fetchedRecordCount
        Write-Output "Fetched $fetchedRecordCount records and saved into SignIns$i.json"
        if($docCount -le $countOfSignInDocsToBeSavedInAFile)
        {
            $myReport.Content | Out-File -FilePath SignIns$i.json -append  -Force       
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




## <a name="executing-the-script"></a>Körning av skriptet
När du har redigerat skriptet, kör det och kontrollera att förväntade data från inloggningens loggrapport returneras.

Skriptet returnerar utdata från inloggningsrapporten i JSON-format. Det skapar också en `SignIns.json`-fil med samma utdata. Du kan experimentera genom att ändra skriptet för att returnera data från andra rapporter och kommentera bort utdataformat som du inte behöver.

## <a name="next-steps"></a>Nästa steg
* Vill du anpassa exemplen i den här artikeln? Kolla in [API-referens för Azure Active Directory-inloggningsaktivitet](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin). 
* Om du vill se en fullständig översikt över användning av Azure Active Directory rapporterings-API, se [Komma igång med Azure Active Directory Reporting API](concept-reporting-api.md).
* Mer information om Azure Active Directory-rapportering finns i [Azure Active Directory Reporting Guide](overview-reports.md).  

