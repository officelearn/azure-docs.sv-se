---
title: Azure Active Directory inloggningsaktivitet rapporten API-exempel | Microsoft Docs
description: "Hur du kommer igång med Azure Active Directory Reporting API"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: c41c1489-726b-4d3f-81d6-83beb932df9c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 158f121880dbc1fc1495c8599367502ffd93ce20
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2018
---
# <a name="azure-active-directory-sign-in-activity-report-api-samples"></a>Azure Active Directory inloggningsaktivitet rapporten API-exempel
Det här avsnittet är en del av en samling ämnen om Azure Active Directory reporting API.  
Azure AD-rapportering ger dig en API som gör att du kan komma åt inloggningsaktivitet data med hjälp av koden eller relaterade verktyg.  
Omfattningen av det här avsnittet är att ge dig exempelkod för den **aktivitet API inloggning**.

Se:

* [Granskningsloggar](active-directory-reporting-azure-portal.md#activity-reports) mer information
* [Komma igång med Azure Active Directory Reporting API](active-directory-reporting-api-getting-started.md) för mer information om reporting API.


## <a name="prerequisites"></a>Förutsättningar
Innan du kan använda exemplen i det här avsnittet, måste du slutföra de [krav för att få åtkomst till Azure AD reporting API](active-directory-reporting-api-prerequisites.md).  

## <a name="powershell-script"></a>PowerShell-skript
    # This script will require the Web Application and permissions setup in Azure Active Directory
    $ClientID       = "<clientId>"             # Should be a ~35 character string insert your info here
    $ClientSecret   = "<clientSecret>"         # Should be a ~44 character string insert your info here
    $loginURL       = "https://login.microsoftonline.com/"
    $tenantdomain   = "<tenantDomain>"
    $ daterange            # For example, contoso.onmicrosoft.com

    $7daysago = "{0:s}" -f (get-date).AddDays(-7) + "Z"
    # or, AddMinutes(-5)

    Write-Output $7daysago

    # Get an Oauth 2 access token based on client id, secret and tenant domain
    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}

    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    $url = "https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&`$filter=signinDateTime ge $7daysago"

    $i=0

    Do{
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        Write-Output "Save the output to a file SigninActivities$i.json"
        Write-Output "---------------------------------------------"
        $myReport.Content | Out-File -FilePath SigninActivities$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)

    } else {

        Write-Host "ERROR: No Access Token"
    }




## <a name="executing-the-script"></a>Körning av skriptet
När du har redigerat skriptet körs och kontrollera att de förväntade data från revision loggar rapporten returneras.

Skriptet returnerar utdata från rapporten i JSON-format. Det skapar också en `SigninActivities.json` fil med samma utdata. Du kan experimentera genom att ändra skriptet för att returnera data från andra rapporter och kommentera ut utdataformat som du inte behöver.

## <a name="next-steps"></a>Nästa steg
* Vill du anpassa exemplen i det här avsnittet? Kolla in den [Azure Active Directory inloggningsaktivitet API-referens för](active-directory-reporting-api-sign-in-activity-reference.md). 
* Om du vill se en fullständig översikt över med Azure Active Directory reporting API, se [komma igång med Azure Active Directory reporting API](active-directory-reporting-api-getting-started.md).
* Om du vill veta mer om Azure Active Directory reporting finns i [Azure Active Directory Reporting Guide](active-directory-reporting-guide.md).  

