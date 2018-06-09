---
title: Office 365 hanteringslösning i Azure | Microsoft Docs
description: Den här artikeln innehåller information om konfiguration och användning av Office 365-lösningen i Azure.  Den innehåller en detaljerad beskrivning av Office 365-poster som skapats i logganalys.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: bwren
ms.openlocfilehash: e93860328ed6a31b7a06cc3420fb50ab4af9a00c
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236111"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Office 365 hanteringslösning i Azure (förhandsversion)

![Office 365-logotyp](media/oms-solution-office-365/icon.png)

Hanteringslösning för Office 365 kan du övervaka din Office 365-miljö i logganalys.

- Övervaka användaraktiviteter på Office 365-konton för att analysera användningsmönster samt identifiera beteendebaserade trender. Du kan exempelvis extrahera av specifika Användningsscenarier, till exempel filer som delas utanför din organisation eller populäraste SharePoint-webbplatser.
- Övervaka aktiviteter för administratören att spåra konfigurationsändringar eller Privilegierade åtgärder.
- Identifiera och undersöka oönskade användarbeteende som kan anpassas efter organisationens behov.
- Visa granskning och kompatibilitet. Exempelvis kan du övervaka åtkomståtgärder på konfidentiella filer som kan hjälpa dig med processen gransknings- och efterlevnad.
- Använd felsökning med hjälp av [logga sökningar](../log-analytics/log-analytics-log-search.md) ovanpå Office 365 aktivitetsdata i din organisation.

## <a name="prerequisites"></a>Förutsättningar
Följande krävs innan den här lösningen som ska installeras och konfigureras.

- Organisationens prenumeration på Office 365.
- Autentiseringsuppgifterna för ett konto som är en Global administratör.
- För att ta emot granskningsdata, måste du [konfigurera granskning](https://support.office.com/en-us/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin) i Office 365-prenumeration.  Observera att [postlåda granskning](https://technet.microsoft.com/library/dn879651.aspx) konfigureras separat.  Du kan fortfarande installera lösningen och samla in andra data om granskning inte har konfigurerats.
 

## <a name="management-packs"></a>Hanteringspaket
Den här lösningen installerar inte några hanteringspaket i [anslutna hanteringsgrupper](../log-analytics/log-analytics-om-agents.md).
  
## <a name="install-and-configure"></a>Installera och konfigurera
Starta genom att lägga till den [Office 365-lösningen till din prenumeration](/monitoring/monitoring-solutions.md#install-a-management-solution). När den har lagts till, måste du konfigurationsstegen i det här avsnittet för att ge åtkomst till din Office 365-prenumeration.

### <a name="required-information"></a>Nödvändig information
Samla in följande information innan du börjar den här proceduren.

Från logganalys-arbetsytan:

- Arbetsytans namn: arbetsytan där Office 365-data samlas in.
- Resursgruppens namn: resursgruppen som innehåller arbetsytan.
- Azure prenumerations-ID: den prenumeration som innehåller arbetsytan.

Från din prenumeration på Office 365:

- Användarnamn: E-postadress med ett administrativt konto.
- Klient-ID: Unikt ID för Office 365-prenumeration.
- Klient-ID: 16 tecken lång sträng som representerar Office 365-klienten.
- Klienthemligheten: Krypterad sträng krävs för autentisering.

### <a name="create-an-office-365-application-in-azure-active-directory"></a>Skapa ett Office 365-program i Azure Active Directory
Det första steget är att skapa ett program i Azure Active Directory som hanteringslösningen för att få åtkomst till Office 365-lösningen.

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com/).
1. Välj **Azure Active Directory** och sedan **App registreringar**.
1. Klicka på **Ny programregistrering**.

    ![Lägg till appregistrering](media/oms-solution-office-365/add-app-registration.png)
1. Ange ett program **namn** och **inloggnings-URL**.  Namnet ska vara beskrivande.  Använd _http://localhost_ för URL: en och Behåll _webbapp / API_ för den **programtyp**
    
    ![Skapa program](media/oms-solution-office-365/create-application.png)
1. Klicka på **skapa** och verifiera informationen i programmet.

    ![Registrerad app](media/oms-solution-office-365/registered-app.png)

### <a name="configure-application-for-office-365"></a>Konfigurera program för Office 365

1. Klicka på **inställningar** att öppna den **inställningar** menyn.
1. Välj **egenskaper**. Ändra **flera innehavare** till _Ja_.

    ![Inställningar för multitenant](media/oms-solution-office-365/settings-multitenant.png)

1. Välj **nödvändiga behörigheter** i den **inställningar** -menyn och klicka sedan på **Lägg till**.
1. Klicka på **väljer en API** och sedan **Office 365 Management API: er**. Klicka på **Office 365 Management API: er**. Klicka på **Välj**.

    ![Välj API](media/oms-solution-office-365/select-api.png)

1. Under **Välj behörigheter** välja följande alternativ för både **programbehörigheter** och **delegerad behörighet**:
    - Läs tjänsthälsoinformation för din organisation
    - Läs aktivitetsdata för din organisation
    - Läs aktivitetsrapporter för din organisation

    ![Välj API](media/oms-solution-office-365/select-permissions.png)

1. Klicka på **Välj** och sedan **klar**.
1. Klicka på **bevilja behörigheter** och klicka sedan på **Ja** när en förfrågan om verifiering.

    ![Bevilja behörigheter](media/oms-solution-office-365/grant-permissions.png)

### <a name="add-a-key-for-the-application"></a>Lägg till en nyckel för programmet

1. Välj **nycklar** i den **inställningar** menyn.
1. Ange en **beskrivning** och **varaktighet** för den nya nyckeln.
1. Klicka på **spara** och sedan kopiera den **värdet** som genereras.

    ![Nycklar](media/oms-solution-office-365/keys.png)

### <a name="add-admin-consent"></a>Lägg till administratör medgivande
Du måste ange administrativa medgivande för programmet om du vill aktivera det administrativa kontot för första gången. Du kan göra detta med ett PowerShell-skript. 

1. Sparar följande skript som *office365_consent.ps1*.

    ```
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,     
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId
    )
    
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
        
    IF ($Subscription -eq $null)
        {Login-AzureRmAccount -ErrorAction Stop}
    $Subscription = (Select-AzureRmSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $Workspace = (Set-AzureRMOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $WorkspaceLocation= $Workspace.Location
    $WorkspaceLocationShort= $Workspace.PortalUrl.Split("/",$option)[1].Split(".",$option)[0]
    $WorkspaceLocation
    
    Function AdminConsent{
    
    $domain='login.microsoftonline.com'
    $mmsDomain = 'login.mms.microsoft.com'
    $WorkspaceLocationShort= $Workspace.PortalUrl.Split("/",$option)[1].Split(".",$option)[0]
    $WorkspaceLocationShort
    $WorkspaceLocation
    switch ($WorkspaceLocation.Replace(" ","").ToLower()) {
           "eastus"   {$OfficeAppClientId="d7eb65b0-8167-4b5d-b371-719a2e5e30cc"; break}
           "westeurope"   {$OfficeAppClientId="c9005da2-023d-40f1-a17a-2b7d91af4ede"; break}
           "southeastasia"   {$OfficeAppClientId="09c5b521-648d-4e29-81ff-7f3a71b27270"; break}
           "australiasoutheast"  {$OfficeAppClientId="f553e464-612b-480f-adb9-14fd8b6cbff8"; break}   
           "westcentralus"  {$OfficeAppClientId="98a2a546-84b4-49c0-88b8-11b011dc8c4e"; break}
           "japaneast"   {$OfficeAppClientId="b07d97d3-731b-4247-93d1-755b5dae91cb"; break}
           "uksouth"   {$OfficeAppClientId="f232cf9b-e7a9-4ebb-a143-be00850cd22a"; break}
           "centralindia"   {$OfficeAppClientId="ffbd6cf4-cba8-4bea-8b08-4fb5ee2a60bd"; break}
           "canadacentral"  {$OfficeAppClientId="c2d686db-f759-43c9-ade5-9d7aeec19455"; break}
           "eastus2"  {$OfficeAppClientId="7eb65b0-8167-4b5d-b371-719a2e5e30cc"; break}
           "westus2"  {$OfficeAppClientId="98a2a546-84b4-49c0-88b8-11b011dc8c4e"; break} #Need to check
           "usgovvirginia" {$OfficeAppClientId="c8b41a87-f8c5-4d10-98a4-f8c11c3933fe"; 
                             $domain='login.microsoftonline.us';
                             $mmsDomain = 'usbn1.login.oms.microsoft.us'; break} # US Gov Virginia
           default {$OfficeAppClientId="55b65fb5-b825-43b5-8972-c8b6875867c1";
                    $domain='login.windows-ppe.net'; break} #Int
        }
    
        $OfficeAppRedirectUrl="https://$($WorkspaceLocationShort).$($mmsDomain)/Office365/Authorize"
        $OfficeAppRedirectUrl
        $domain
        Start-Process -FilePath  "https://$($domain)/common/adminconsent?client_id=$($OfficeAppClientId)&state=12345&redirect_uri=$($OfficeAppRedirectUrl)"
    }
    
    AdminConsent -ErrorAction Stop
    ```

2. Kör skriptet med följande kommando.
    ```
    .\office365_consent.ps1 -WorkspaceName <Workspace name> -ResourceGroupName <Resource group name> -SubscriptionId <Subscription ID>
    ```
    Exempel:

    ```
    .\office365_consent.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631- yyyyyyyyyyyy'
    ```

1. Du kommer att visas ett fönster som liknar den nedan. Klicka på **acceptera**.
    
    ![Administratörsmedgivande](media/oms-solution-office-365/admin-consent.png)

### <a name="subscribe-to-log-analytics-workspace"></a>Prenumerera på logganalys-arbetsytan
Det sista steget är att prenumerera programmet till logganalys-arbetsytan. Du också göra detta med ett PowerShell-skript.

1. Sparar följande skript som *office365_subscription.ps1*.

    ```
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeUsername,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId,
        [Parameter(Mandatory=$True)][string]$OfficeClientId,
        [Parameter(Mandatory=$True)][string]$OfficeClientSecret
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    $line
    IF ($Subscription -eq $null)
        {Login-AzureRmAccount -ErrorAction Stop}
    $Subscription = (Select-AzureRmSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzureRMOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    $OfficeClientSecret =[uri]::EscapeDataString($OfficeClientSecret)
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant = $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    # Load ADAL Azure AD Authentication Library Assemblies
    $adal = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll"
    $null = [System.Reflection.Assembly]::LoadFrom($adal)
    $null = [System.Reflection.Assembly]::LoadFrom($adalforms)
     
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $global:authResultARM = $authContext.AcquireToken($resourceAppIdURIARM, $clientId, $redirectUri, "Auto")
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Failure {
    $line
    $formatstring = "{0} : {1}`n{2}`n" +
                    "    + CategoryInfo          : {3}`n" +
                    "    + FullyQualifiedErrorId : {4}`n"
    $fields = $_.InvocationInfo.MyCommand.Name,
              $_.ErrorDetails.Message,
              $_.InvocationInfo.PositionMessage,
              $_.CategoryInfo.ToString(),
              $_.FullyQualifiedErrorId
    
    $formatstring -f $fields
    $_.Exception.Response
    
    $line
    break
    }
    
    Function Connection-API
    {
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   =  $Subscription[0].Subscription.Id
    
    $line
    $connectionAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/connections/office365connection_' + $SubscriptionId + $OfficeTennantId + '?api-version=2017-04-26-preview'
    $connectionAPIUrl
    $line
    
    $xms_client_tenant_Id ='1da8f770-27f4-4351-8cb3-43ee54f14759'
    
    $BodyString = "{
                    'properties': {
                                    'AuthProvider':'Office365',
                                    'clientId': '" + $OfficeClientId + "',
                                    'clientSecret': '" + $OfficeClientSecret + "',
                                    'Username': '" + $OfficeUsername   + "',
                                    'Url': 'https://$($domain)/" + $OfficeTennantId + "/oauth2/token',
                                  },
                    'etag': '*',
                    'kind': 'Connection',
                    'solution': 'Connection',
                   }"
    
    $params = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id #Prod-'1da8f770-27f4-4351-8cb3-43ee54f14759'
        'Content-Type' = 'application/json'
        }
        Body = $BodyString
        Method = 'Put'
        URI = $connectionAPIUrl
    }
    $response = Invoke-WebRequest @params 
    $response
    $line
    
    }
    
    Function Office-Subscribe-Call{
    try{
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $SubscriptionId   =  $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_' + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $OfficeBodyString = "{
                    'properties': {
                                    'AuthProvider':'Office365',
                                    'office365TenantID': '" + $OfficeTennantId + "',
                                    'connectionID': 'office365connection_" + $SubscriptionId + $OfficeTennantId + "',
                                    'office365AdminUsername': '" + $OfficeUsername + "',
                                    'contentTypes':'Audit.Exchange,Audit.AzureActiveDirectory'
                                  },
                    'etag': '*',
                    'kind': 'Office365',
                    'solution': 'Office365',
                   }"
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Body = $OfficeBodyString
        Method = 'Put'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    }
    catch{ Failure }
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Connection-API -ErrorAction Stop
    Office-Subscribe-Call -ErrorAction Stop
    ```

2. Kör skriptet med följande kommando:
    ```
    .\office365_subscription.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeUsername <OfficeUsername> -OfficeTennantID <Tenant ID> -OfficeClientId <Client ID> -OfficeClientSecret <Client secret>
    ```
    Exempel:

    ```
    .\office365_subscription.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeUsername 'admin@contoso.com' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx' -OfficeClientId 'f8f14c50-5438-4c51-8956-zzzzzzzzzzzz' -OfficeClientSecret 'y5Lrwthu6n5QgLOWlqhvKqtVUZXX0exrA2KRHmtHgQb='
    ```

### <a name="troublshooting"></a>Troublshooting

Du kan se följande fel om du försöker skapa en prenumeration när prenumerationen finns redan.

```
Invoke-WebRequest : {"Message":"An error has occurred."}
At C:\Users\v-tanmah\Desktop\ps scripts\office365_subscription.ps1:161 char:19
+ $officeresponse = Invoke-WebRequest @Officeparams
+                   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (System.Net.HttpWebRequest:HttpWebRequest) [Invoke-WebRequest], WebException
    + FullyQualifiedErrorId : WebCmdletWebResponseException,Microsoft.PowerShell.Commands.InvokeWebRequestCommand 
```

Du kan se felmeddelande om ogiltiga parametervärden tillhandahålls.

```
Select-AzureRmSubscription : Please provide a valid tenant or a valid subscription.
At line:12 char:18
+ ... cription = (Select-AzureRmSubscription -SubscriptionId $($Subscriptio ...
+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzureRmContext], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.SetAzureRMContextCommand

```

## <a name="uninstall"></a>Avinstallera
Du kan ta bort Office 365-hanteringslösning som med hjälp av processen i [ta bort en lösning för](../monitoring/monitoring-solutions.md#remove-a-management-solution). Detta förhindrar inte data som samlas in från Office 365 till logganalys om. Följ anvisningarna nedan om du vill avbryta prenumerationen på Office 365 och sluta samla in data.

1. Sparar följande skript som *office365_unsubscribe.ps1*.

    ```
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    
    $line
    IF ($Subscription -eq $null)
        {Login-AzureRmAccount -ErrorAction Stop}
    $Subscription = (Select-AzureRmSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzureRMOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant =  $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    # Load ADAL Azure AD Authentication Library Assemblies
    $adal = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll"
    $null = [System.Reflection.Assembly]::LoadFrom($adal)
    $null = [System.Reflection.Assembly]::LoadFrom($adalforms)
     
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $global:authResultARM = $authContext.AcquireToken($resourceAppIdURIARM, $clientId, $redirectUri, "Auto")
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Office-UnSubscribe-Call{
    
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   = $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_'  + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Method = 'Delete'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Office-UnSubscribe-Call -ErrorAction Stop
    ```

2. Kör skriptet med följande kommando:

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeTennantID <Tenant ID> 
    ```

    Exempel:

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

## <a name="data-collection"></a>Datainsamling
### <a name="supported-agents"></a>Agenter som stöds
Office 365-lösningen inte hämta data från någon av de [OMS agenter](../log-analytics/log-analytics-data-sources.md).  Den hämtar data direkt från Office 365.

### <a name="collection-frequency"></a>Insamlingsfrekvens
Det kan ta några timmar innan data inledningsvis samlas in. När den börjar samla in Office 365 skickar en [webhook meddelande](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) med detaljerad information till logganalys varje gång en post har skapats. Den här posten finns i logganalys inom några minuter efter tas emot.

## <a name="using-the-solution"></a>Använda lösningen
När du lägger till Office 365-lösningen logganalys-arbetsytan i **Office 365** panelen kommer att läggas till på instrumentpanelen. Den här panelen visar antal och en grafisk representation av antalet datorer i din miljö och deras uppdateringskompatibilitet.<br><br>
![Panelen för Office 365-sammanfattning](media/oms-solution-office-365/tile.png)  

Klicka på den **Office 365** öppna den **Office 365** instrumentpanelen.

![Office 365-instrumentpanelen](media/oms-solution-office-365/dashboard.png)  

Instrumentpanelen innehåller kolumnerna i följande tabell. Varje kolumnen visar de tio översta aviseringarna efter antal som matchar den kolumnen villkoren för angivet omfång och tidsintervall. Du kan köra en sökning i logg som innehåller hela listan genom att klicka på se alla längst ned i kolumnen eller genom att klicka på kolumnrubriken.

| Kolumn | Beskrivning |
|:--|:--|
| Åtgärder | Innehåller information om de aktiva användarna från alla övervakade Office 365-prenumerationer. Du kan också se antalet aktiviteter som sker över tid.
| Exchange | Visar fördelningen av Exchange Server-aktiviteter, till exempel Lägg till postlåda behörighet eller Set-postlåda. |
| SharePoint | Visar de översta aktiviteterna att användare utför på SharePoint-dokument. När du går nedåt från den här panelen visar sidan Sök efter information om dessa aktiviteter, till exempel måldokumentet och platsen för den här aktiviteten. Till exempel en komma åt filen händelse du kommer att kunna se dokumentet som används, associerade kontonamn och IP-adress. |
| Azure Active Directory | Innehåller översta användaraktiviteter, t.ex återställa användarlösenord och inloggningsförsök. När du går nedåt kommer du att kunna se detaljer för dessa aktiviteter som resultat. Detta är mest användbart om du vill övervaka misstänkta aktiviteter på Azure Active Directory. |




## <a name="log-analytics-records"></a>Log Analytics-poster

Alla poster som skapats i logganalys-arbetsytan med Office 365-lösningen har en **typen** av **OfficeActivity**.  Den **OfficeWorkload** egenskapen avgör vilken Office 365-tjänst som posten refererar till - Exchange, AzureActiveDirectory, SharePoint eller OneDrive.  Den **RecordType** egenskapen anger typ av åtgärd.  Egenskaperna varierar för varje åtgärdstyp av och visas i tabellerna nedan.

### <a name="common-properties"></a>Gemensamma egenskaper
Följande egenskaper är gemensamma för alla Office 365-poster.

| Egenskap  | Beskrivning |
|:--- |:--- |
| Typ | *OfficeActivity* |
| ClientIP | IP-adressen på den enhet som användes när aktiviteten har loggats. IP-adressen visas i en IPv4- eller IPv6-adress-format. |
| OfficeWorkload | Office 365-tjänst som posten refererar till.<br><br>AzureActiveDirectory<br>Exchange<br>SharePoint|
| Åtgärd | Namnet på användaren eller administratören aktiviteten.  |
| Organisations-ID | GUID för Office 365-klient för din organisation. Det här värdet kommer alltid att samma för din organisation, oavsett tjänsten Office 365 den uppstår. |
| RecordType | Typ av av åtgärd utförs. |
| ResultStatus | Anger om åtgärden (anges i egenskapen åtgärden) lyckades eller inte. Möjliga värden är slutfört, PartiallySucceded eller misslyckades. Värdet är för Exchange-administratörsaktivitet, antingen SANT eller FALSKT. |
| UserId | UPN (User Principal Name) för användaren som utförde åtgärden resulterade i posten loggas; till exempel my_name@my_domain_name. Observera att posterna för aktivitet som utförs av Systemkonton (till exempel SHAREPOINT\system eller NTAUTHORITY\SYSTEM) ingår också. | 
| UserKey | Ett alternativt ID för den användare som identifieras i användar-ID-egenskapen.  Den här egenskapen är fylls i med passport unikt ID (PUID) för händelser som utförs av användare i SharePoint, OneDrive för företag och Exchange. Den här egenskapen kan också ange samma värde som egenskapen användar-ID för händelser i andra tjänster och händelser som utförs av systemkonton|
| UserType | Typ av användaren som utförde åtgärden.<br><br>Administratör<br>Program<br>DcAdmin<br>Vanliga<br>Reserverad<br>ServicePrincipal<br>System |


### <a name="azure-active-directory-base"></a>Azure Active Directory-bas
Följande egenskaper är gemensamma för alla poster i Azure Active Directory.

| Egenskap  | Beskrivning |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Typ av Azure AD-händelse. |
| extendedProperties | De utökade egenskaperna för Azure AD-händelse. |


### <a name="azure-active-directory-account-logon"></a>Inloggning med Azure Active Directory-konto
Dessa poster skapas när Active Directory-användare försöker logga in.

| Egenskap  | Beskrivning |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectoryAccountLogon |
| Program | Programmet som utlöser händelsen konto inloggning, till exempel Office 15. |
| Client | Information om klienten enhet, enhetens operativsystem och enhetens webbläsare som användes för den i händelsen konto inloggningen. |
| loginStatus | Den här egenskapen är direkt från OrgIdLogon.LoginStatus. Mappningen av olika intressanta inloggningsfel kan göras av aviseringar algoritmer. |
| UserDomain | Identitetsinformation klient (TII). | 


### <a name="azure-active-directory"></a>Azure Active Directory
Dessa poster skapas när ändringen eller tillägg görs i Azure Active Directory-objekt.

| Egenskap  | Beskrivning |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | Användaren som åtgärden (som identifieras av egenskapen åtgärden) utfördes på. |
| Aktör | Användaren eller tjänstens huvudnamn som utförde åtgärden. |
| ActorContextId | GUID för den organisation som aktören tillhör. |
| ActorIpAddress | Skådespelare, IP-adress i IPV4- eller IPV6-adress-format. |
| InterSystemsId | GUID som spårar åtgärder mellan komponenter i tjänsten Office 365. |
| IntraSystemId |   GUID som genereras av Azure Active Directory för att spåra åtgärden. |
| SupportTicketId | Biljett-ID kundsupport för åtgärden i ”act-on-behalf-of” situationer. |
| TargetContextId | GUID för den organisation som den aktuella användaren tillhör. |


### <a name="data-center-security"></a>Datacenter-säkerhet
Dessa poster har skapats från granskningsdata Center datasäkerhet.  

| Egenskap  | Beskrivning |
|:--- |:--- |
| EffectiveOrganization | Namnet på den klient som höjning/cmdlet är riktad mot. |
| ElevationApprovedTime | Tidsstämpel för när höjningen har godkänts. |
| ElevationApprover | Namnet på en Microsoft-hanterare. |
| ElevationDuration | Varaktigheten höjningen var aktiv. |
| ElevationRequestId |  En unik identifierare för höjning begäran. |
| ElevationRole | Rollen höjningen mottog en begäran för. |
| ElevationTime | Starttid för höjningen. |
| Starttid | Starttiden för den cmdlet som körs. |


### <a name="exchange-admin"></a>Exchange-administratören
Dessa poster skapas när ändringar görs i Exchange-konfiguration.

| Egenskap  | Beskrivning |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeAdmin |
| ExternalAccess |  Anger om cmdleten kördes av en användare i din organisation, av Microsoft datacenter-personal eller ett tjänstkonto för datacenter eller av en delegerad administratör. Värdet falskt anger att cmdleten kördes av någon i din organisation. Värdet True anger att cmdleten kördes av datacenter personal, ett tjänstkonto för datacenter eller en delegerad administratör. |
| ModifiedObjectResolvedName |  Detta är egna namnet på det objekt som har ändrats av cmdlet. Detta loggas endast om cmdlet ändrar objektet. |
| Organisationsnamn | Namn för innehavaren. |
| OriginatingServer | Namnet på den server där cmdleten kördes. |
| Parametrar | Namn och värde för alla parametrar som användes med den cmdlet som identifieras i Operations-egenskapen. |


### <a name="exchange-mailbox"></a>Exchange-postlåda
Dessa poster skapas när ändringar eller tillägg som görs till Exchange-postlådor.

| Egenskap  | Beskrivning |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| ClientInfoString | Information om e-postklienten som användes för att utföra åtgärden, till exempel en webbläsarversion, Outlook-versionen och information för mobila enheter. |
| Client_IPAddress | IP-adressen på den enhet som användes när åtgärden har loggats. IP-adressen visas i en IPv4- eller IPv6-adress-format. |
| ClientMachineName | Namnet på den dator som är värd för Outlook-klienten. |
| ClientProcessName | E-postklienten som användes för att komma åt postlådan. |
| ClientVersion | Versionen av e-postklienten. |
| InternalLogonType | Reserverat för intern användning. |
| Logon_Type | Anger vilken typ av användare som nås postlådan och utföra åtgärden som loggades. |
| LogonUserDisplayName |    Användarvänligt namn för användaren som utförde åtgärden. |
| LogonUserSid | SID för användaren som utförde åtgärden. |
| MailboxGuid | Exchange-GUID för postlådan som öppnade. |
| MailboxOwnerMasterAccountSid | Postlåda ägare konto master kontots SID. |
| MailboxOwnerSid | SID för postlådans ägare. |
| MailboxOwnerUPN | Den person som äger den postlåda som har åtkomst till e-postadress. |


### <a name="exchange-mailbox-audit"></a>Exchange-postlåda granskning
Dessa poster skapas när en postlåda granskningspost skapas.

| Egenskap  | Beskrivning |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| Objekt | Representerar det objektet som åtgärden utfördes | 
| SendAsUserMailboxGuid | GUID för postlådan som användes för att skicka e-postmeddelande som Exchange. |
| SendAsUserSmtp | SMTP-adress för den användare som har personifierats. |
| SendonBehalfOfUserMailboxGuid | GUID för den postlåda som användes för att skicka e-post för Exchange. |
| SendOnBehalfOfUserSmtp | SMTP-adress för den användare som e-postmeddelandet skickas. |


### <a name="exchange-mailbox-audit-group"></a>Exchange-postlåda Audit grupp
Dessa poster skapas när ändringar eller tillägg som görs till Exchange-grupper.

| Egenskap  | Beskrivning |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | Information om varje objekt i gruppen. |
| CrossMailboxOperations | Anger om åtgärden involverad mer än en postlåda. |
| DestMailboxId | Ange endast om parametern CrossMailboxOperations är True. Anger målet postlådan GUID. |
| DestMailboxOwnerMasterAccountSid | Ange endast om parametern CrossMailboxOperations är True. Anger SID för master kontots SID för målet postlådans ägare. |
| DestMailboxOwnerSid | Ange endast om parametern CrossMailboxOperations är True. Anger SID för mål-postlådan. |
| DestMailboxOwnerUPN | Ange endast om parametern CrossMailboxOperations är True. Anger UPN för ägaren av mål-postlåda. |
| DestFolder | Målmapp för åtgärder som till exempel flytta. |
| Mapp | Den mapp där det finns en grupp med objekt. |
| Mappar |     Information om mapparna källa ingår i en funktion. Om exempelvis mappar valts och sedan ta bort. |


### <a name="sharepoint-base"></a>SharePoint-bas
Dessa egenskaper är gemensamma för alla SharePoint-poster.

| Egenskap  | Beskrivning |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| eventSource | Identifierar att en händelse har inträffat i SharePoint. Möjliga värden är SharePoint eller ObjectModel. |
| itemType | Typ av objekt som har åtkomst till eller ändrats. Se tabellen ItemType mer information om vilka typer av objekt. |
| MachineDomainInfo | Information om enheten synkroniseringsåtgärder. Denna information rapporteras endast om den finns i begäran. |
| MachineId |   Information om enheten synkroniseringsåtgärder. Denna information rapporteras endast om den finns i begäran. |
| Site_ | GUID för den plats där filen eller mappen som används av användaren finns. |
| Source_Name | Den enhet som utlöste åtgärden granskad. Möjliga värden är SharePoint eller ObjectModel. |
| UserAgent | Information om användarens klienten eller webbläsare. Denna information tillhandahålls av klienten eller webbläsare. |


### <a name="sharepoint-schema"></a>SharePoint-Schema
Dessa poster skapas när konfigurationsändringar görs i SharePoint.

| Egenskap  | Beskrivning |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | Valfri sträng för anpassade händelser. |
| Event_Data |  Valfria nyttolasten för anpassade händelser. |
| ModifiedProperties | Egenskapen ingår för admin-händelser, till exempel lägga till en användare som en medlem av en webbplats eller en samling administratörsgrupp. Egenskapen innehåller namnet på egenskapen som har ändrats (t.ex, platsen administratörsgruppen), det nya värdet för egenskapen ändrade (sådana den användare som har lagts till som en plats-administratör) och det tidigare värdet för det ändrade objektet. |


### <a name="sharepoint-file-operations"></a>SharePoint-filåtgärder
Dessa poster skapas som svar på filåtgärder i SharePoint.

| Egenskap  | Beskrivning |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | Filnamnstillägget för en fil som kopieras eller flyttas. Den här egenskapen visas endast för FileCopied och FileMoved händelser. |
| DestinationFileName | Namnet på den fil som kopieras eller flyttas. Den här egenskapen visas endast för FileCopied och FileMoved händelser. |
| DestinationRelativeUrl | URL till målmappen där en fil kopieras eller flyttas. Kombinationen av värden för parametrarna SiteURL, DestinationRelativeURL och DestinationFileName är detsamma som värdet för egenskapen ObjectID som är den fullständiga sökvägen för filen som har kopierats. Den här egenskapen visas endast för FileCopied och FileMoved händelser. |
| SharingType | Typ av behörighet som har tilldelats som resursen har delat med användaren. Den här användaren identifieras med parametern UserSharedWith. |
| Site_Url | URL till den plats där filen eller mappen som används av användaren finns. |
| SourceFileExtension | Filnamnstillägget för den fil som användes av användaren. Den här egenskapen är tom om det objekt som användes är en mapp. |
| SourceFileName |  Namnet på filen eller mappen som används av användaren. |
| SourceRelativeUrl | URL till den mapp som innehåller filen som används av användaren. Kombinationen av värden för parametrarna SiteURL, SourceRelativeURL och SourceFileName är detsamma som värdet för egenskapen ObjectID som är den fullständiga sökvägen för filen som används av användaren. |
| UserSharedWith |  Användaren som en resurs har delat med. |




## <a name="sample-log-searches"></a>Exempel på loggsökningar
Följande tabell innehåller exempel på sökningar i loggen för uppdateringsposter som har samlats in av den här lösningen.

| Fråga | Beskrivning |
| --- | --- |
|Antalet för alla åtgärder på Office 365-prenumeration |OfficeActivity &#124; sammanfatta count() för åtgärden |
|Användning av SharePoint-webbplatser|OfficeActivity &#124; där OfficeWorkload = ~ ”sharepoint” &#124; sammanfatta count() av SiteUrl | Sortera efter antal asc|
|Filåtgärder för åtkomst av typ|Sök i (OfficeActivity) OfficeWorkload = ~ ”azureactivedirectory” och ”MinTest”|
|Söka med ett specifikt nyckelord|Typ = OfficeActivity OfficeWorkload = azureactivedirectory ”MinTest”|
|Övervaka externa åtgärder i Exchange|OfficeActivity &#124; där OfficeWorkload = ~ ”exchange” och ExternalAccess == true|



## <a name="next-steps"></a>Nästa steg
* Använd loggsökningar i [Log Analytics](../log-analytics/log-analytics-log-searches.md) för att visa detaljerad uppdateringsinformation.
* [Skapa dina egna instrumentpaneler](../log-analytics/log-analytics-dashboards.md) att visa dina Favoriter Office 365-sökningar.
* [Skapa aviseringar](../log-analytics/log-analytics-alerts.md) ska meddelas proaktivt om viktiga Office 365-aktiviteter.  
