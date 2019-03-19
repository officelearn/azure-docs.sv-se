---
title: Lösning för Office 365 i Azure | Microsoft Docs
description: Den här artikeln innehåller information om konfiguration och användning av Office 365-lösning i Azure.  Den innehåller en detaljerad beskrivning av Office 365-poster som skapats i Azure Monitor.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/24/2019
ms.author: bwren
ms.openlocfilehash: 6a13988af7a46ff6fafe352e850ee238cda79c08
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57996709"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Lösning för Office 365 i Azure (förhandsversion)

![Logotyp för Office 365](media/solution-office-365/icon.png)

Hanteringslösning för Office 365 kan du övervaka din Office 365-miljö i Azure Monitor.

- Övervaka användaraktiviteter på Office 365-konton för att analysera användningsmönster samt identifiera beteendeanalys trender. Du kan exempelvis extrahera specifika Användningsscenarier, till exempel filer som delas utanför din organisation eller mest populära SharePoint-webbplatser.
- Övervaka administratöraktiviteter för att spåra konfigurationsändringar eller Privilegierade åtgärder.
- Upptäck och undersök oönskad användarnas beteende och som kan anpassas efter organisationens behov.
- Visa gransknings- och kompatibilitetskontroller. Du kan till exempel övervaka åtkomst filåtgärder på konfidentiella filer, som kan hjälpa dig med processen gransknings- och kompatibilitetskontroller.
- Utföra operativa felsökning med hjälp av [logga frågor](../log-query/log-query-overview.md) ovanpå Office 365 aktivitetsdata för din organisation.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar
Följande krävs innan den här lösningen som den installeras och konfigureras.

- Organisationens prenumeration på Office 365.
- Autentiseringsuppgifter för ett användarkonto som är en Global administratör.
- För att få granskningsdata, måste du [konfigurera granskning](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin) i din Office 365-prenumeration.  Observera att [granskning av postlådan](https://technet.microsoft.com/library/dn879651.aspx) konfigureras separat.  Du kan fortfarande installera lösningen för och samla in andra data om granskning inte har konfigurerats.
 

## <a name="management-packs"></a>Hanteringspaket
Den här lösningen installerar inte alla hanteringspaket i [anslutna hanteringsgrupper](../platform/om-agents.md).
  
## <a name="install-and-configure"></a>Installera och konfigurera
Starta genom att lägga till den [Office 365-lösningen till din prenumeration](solutions.md#install-a-monitoring-solution). När den har lagts till, måste du utföra konfigurationsstegen i det här avsnittet för att ge åtkomst till din Office 365-prenumeration.

### <a name="required-information"></a>Nödvändig information
Samla in följande information innan du påbörjar den här proceduren.

Från din Log Analytics-arbetsyta:

- Arbetsytans namn: Arbetsytan där Office 365-data kommer att samlas in.
- Resursgruppsnamn: Den resursgrupp som innehåller arbetsytan.
- Azure-prenumerations-ID: Den prenumeration som innehåller arbetsytan.

Från din Office 365-prenumeration:

- Användarnamn: E-postadress ett administrativt konto.
- Klientorganisations-ID: Unikt ID för Office 365-prenumeration.
- Klient-ID: 16 tecken lång sträng som representerar Office 365-klienten.
- Klienthemlighet: Krypterad sträng som krävs för autentisering.

### <a name="create-an-office-365-application-in-azure-active-directory"></a>Skapa ett Office 365-program i Azure Active Directory
Det första steget är att skapa ett program i Azure Active Directory som hanteringslösningen använder för att få åtkomst till din Office 365-lösning.

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com/).
1. Välj **Azure Active Directory** och sedan **appregistreringar**.
1. Klicka på **Ny programregistrering**.

    ![Lägg till appregistrering](media/solution-office-365/add-app-registration.png)
1. Ange ett program **namn** och **inloggnings-URL**.  Namnet bör vara beskrivande.  Använd `http://localhost` för URL: en och håll _webbapp / API_ för den **programtyp**
    
    ![Skapa program](media/solution-office-365/create-application.png)
1. Klicka på **skapa** och validera programinformationen.

    ![Registrerad app](media/solution-office-365/registered-app.png)

### <a name="configure-application-for-office-365"></a>Konfigurera program för Office 365

1. Klicka på **inställningar** att öppna den **inställningar** menyn.
1. Välj **egenskaper**. Ändra **med flera innehavare** till _Ja_.

    ![Inställningar för multitenant](media/solution-office-365/settings-multitenant.png)

1. Välj **nödvändiga behörigheter** i den **inställningar** menyn och klicka sedan på **Lägg till**.
1. Klicka på **Välj en API** och sedan **API: er för Office 365-Management**. Klicka på **API: er för Office 365-Management**. Klicka på **Välj**.

    ![Välj API](media/solution-office-365/select-api.png)

1. Under **Välj behörigheter** väljer du följande alternativ för både **programbehörigheter** och **delegerade behörigheter**:
   - Läs tjänsthälsoinformation för din organisation
   - Läs aktivitetsdata för din organisation
   - Läs aktivitetsrapporter för din organisation

     ![Välj API](media/solution-office-365/select-permissions.png)

1. Klicka på **Välj** och sedan **klar**.
1. Klicka på **bevilja** och klicka sedan på **Ja** när du tillfrågas för verifiering.

    ![Bevilja behörigheter](media/solution-office-365/grant-permissions.png)

### <a name="add-a-key-for-the-application"></a>Lägg till en nyckel för programmet

1. Välj **nycklar** i den **inställningar** menyn.
1. Ange en **beskrivning** och **varaktighet** för den nya nyckeln.
1. Klicka på **spara** och kopierar sedan de **värdet** som genereras.

    ![Nycklar](media/solution-office-365/keys.png)

### <a name="add-admin-consent"></a>Lägg till administratörens godkännande
Om du vill aktivera administratörskonto för första gången, måste du ange administratörs godkännande för programmet. Du kan göra detta med ett PowerShell-skript. 

1. Spara följande skript som *office365_consent.ps1*.

    ```
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,     
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId
    )
    
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $WorkspaceLocation= $Workspace.Location
    $WorkspaceLocation
    
    Function AdminConsent{
    
    $domain='login.microsoftonline.com'
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
                             $domain='login.microsoftonline.us'; break}
           default {$OfficeAppClientId="55b65fb5-b825-43b5-8972-c8b6875867c1";
                    $domain='login.windows-ppe.net'; break} #Int
        }
    
        $domain
        Start-Process -FilePath  "https://$($domain)/common/adminconsent?client_id=$($OfficeAppClientId)&state=12345"
    }
    
    AdminConsent -ErrorAction Stop
    ```

2. Kör skriptet med följande kommando. Du uppmanas att två gånger för autentiseringsuppgifter. Ange autentiseringsuppgifter för Log Analytics-arbetsytan först och sedan autentiseringsuppgifterna som global administratör för Office 365-klient.
    ```
    .\office365_consent.ps1 -WorkspaceName <Workspace name> -ResourceGroupName <Resource group name> -SubscriptionId <Subscription ID>
    ```
    Exempel:

    ```
    .\office365_consent.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631- yyyyyyyyyyyy'
    ```

1. Du kommer att visas ett fönster som liknar den nedan. Klicka på **acceptera**.
    
    ![Administratörsmedgivande](media/solution-office-365/admin-consent.png)

### <a name="subscribe-to-log-analytics-workspace"></a>Prenumerera på Log Analytics-arbetsyta
Det sista steget är att prenumerera på programmet till Log Analytics-arbetsytan. Du också göra detta med ett PowerShell-skript.

1. Spara följande skript som *office365_subscription.ps1*.

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
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
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
                                    'contentTypes':'Audit.Exchange,Audit.AzureActiveDirectory,Audit.SharePoint'
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

### <a name="troubleshooting"></a>Felsökning

Du kan se följande fel om programmet redan prenumererar på den här arbetsytan eller om den här klientorganisationen en prenumeration på en annan arbetsyta.

```
Invoke-WebRequest : {"Message":"An error has occurred."}
At C:\Users\v-tanmah\Desktop\ps scripts\office365_subscription.ps1:161 char:19
+ $officeresponse = Invoke-WebRequest @Officeparams
+                   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (System.Net.HttpWebRequest:HttpWebRequest) [Invoke-WebRequest], WebException
    + FullyQualifiedErrorId : WebCmdletWebResponseException,Microsoft.PowerShell.Commands.InvokeWebRequestCommand 
```

Du kan se följande fel om ogiltiga parametervärden har angetts.

```
Select-AzSubscription : Please provide a valid tenant or a valid subscription.
At line:12 char:18
+ ... cription = (Select-AzSubscription -SubscriptionId $($Subscriptio ...
+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzContext], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.SetAzContextCommand

```

## <a name="uninstall"></a>Avinstallera
Du kan ta bort Office 365-hanteringslösning som använder processen i [ta bort en hanteringslösning för](solutions.md#remove-a-monitoring-solution). Detta förhindrar inte data som samlas in från Office 365 i Azure Monitor dock. Följ anvisningarna nedan för att avbryta prenumerationen från Office 365 och stoppa insamling av data.

1. Spara följande skript som *office365_unsubscribe.ps1*.

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
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
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
Office 365-lösningen inte hämta data från någon av de [Log Analytics-agenter](../platform/agent-data-sources.md).  Den hämtar data direkt från Office 365.

### <a name="collection-frequency"></a>Insamlingsfrekvens
Det kan ta några timmar innan data inledningsvis samlas in. När den börjar samla in, Office 365 skickar en [webhook-meddelande](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) med detaljerade data till Azure Monitor varje gång en post skapas. Den här posten är tillgängliga i Azure Monitor inom ett par minuter efter mottagandet.

## <a name="using-the-solution"></a>Använda lösningen

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

När du lägger till Office 365-lösningen i Log Analytics-arbetsytan i **Office 365** läggs panelen på instrumentpanelen. Den här panelen visar antal och en grafisk representation av antalet datorer i din miljö och deras uppdateringskompatibilitet.<br><br>
![Sammanfattningspanel för Office 365](media/solution-office-365/tile.png)  

Klicka på den **Office 365** panelen för att öppna den **Office 365** instrumentpanelen.

![Instrumentpanelen för Office 365](media/solution-office-365/dashboard.png)  

Instrumentpanelen innehåller kolumnerna i följande tabell. Varje kolumn visar de översta tio aviseringarna efter antal som matchar denna kolumns kriterier för specificerat omfång och tidsintervall. Du kan köra en loggsökning som innehåller hela listan genom att klicka på se alla längst ned i kolumnen eller genom att klicka på kolumnrubriken.

| Kolumn | Beskrivning |
|:--|:--|
| Åtgärder | Innehåller information om de aktiva användarna från alla övervakade Office 365-prenumerationer. Du kommer även att kunna se antalet aktiviteter som sker över tid.
| Exchange | Visar fördelningen av Exchange Server-aktiviteter, till exempel Lägg till postlåda behörighet eller Set-postlåda. |
| SharePoint | Visar de översta aktiviteterna att användarna kan utföra för SharePoint-dokument. När du går nedåt från den här panelen visar sidan Sök efter information om dessa aktiviteter, till exempel måldokumentet och platsen för den här aktiviteten. Till exempel för en fil åt händelse, du kommer att kunna se dokumentet som används, associerade kontonamn och IP-adress. |
| Azure Active Directory | Innehåller översta användaraktiviteter, till exempel återställa användarlösenord och inloggningsförsök. När du detaljnivån, kommer du att kunna se information om dessa aktiviteter som resultat. Det här är mest användbart om du vill övervaka misstänkta aktiviteter på Azure Active Directory. |




## <a name="azure-monitor-log-records"></a>Azure Monitor-loggposter

Alla poster som skapats i Log Analytics-arbetsyta i Azure Monitor med hjälp av Office 365-lösningen har en **typ** av **OfficeActivity**.  Den **OfficeWorkload** egenskapen avgör vilka Office 365-tjänst som posten refererar till - Exchange, AzureActiveDirectory, SharePoint eller OneDrive.  Den **RecordType** egenskap anger vilken typ av åtgärd.  Egenskaperna varierar för varje åtgärdstyp av och visas i tabellerna nedan.

### <a name="common-properties"></a>Gemensamma egenskaper
Följande egenskaper är gemensamma för alla Office 365-poster.

| Egenskap  | Beskrivning |
|:--- |:--- |
| Type | *OfficeActivity* |
| ClientIP | IP-adressen för den enhet som användes när aktiviteten loggades. IP-adressen visas i en IPv4- eller IPv6-adressformat. |
| OfficeWorkload | Office 365-tjänst som posten refererar till.<br><br>AzureActiveDirectory<br>Exchange<br>SharePoint|
| Åtgärd | Namnet på användarens eller administratörens aktivitet.  |
| OrganizationId | GUID för organisationens Office 365-klient. Det här värdet kommer alltid att samma för din organisation, oavsett Office 365-tjänst där det inträffar. |
| RecordType | Typ av åtgärder som utförs. |
| ResultStatus | Anger om åtgärden (anges i egenskapen Operation) lyckades eller inte. Möjliga värden är Succeeded eller PartiallySucceeded misslyckades. Värdet är för administratörsaktivitet för Exchange, antingen SANT eller FALSKT. |
| UserId | UPN (User Principal Name) för den användare som utförde den åtgärd som resulterade i posten loggades, till exempel my_name@my_domain_name. Observera att poster för aktiviteter som utförs av Systemkonton (som SHAREPOINT\system eller NTAUTHORITY\SYSTEM) ingår också. | 
| UserKey | Ett alternativt ID för den användare som identifieras i användar-ID-egenskapen.  Den här egenskapen har fyllts i med unikt passport-ID (PUID) för händelser som utförs av användare i SharePoint, OneDrive för företag och Exchange. Den här egenskapen kan även ange samma värde som egenskapen användar-ID för händelser som inträffar i andra tjänster och händelser som utförs av systemkonton|
| UserType | Typ av användare som utförde åtgärden.<br><br>Administratör<br>Program<br>DcAdmin<br>Normal<br>Reserverad<br>ServicePrincipal<br>System |


### <a name="azure-active-directory-base"></a>Azure Active Directory-bas
Följande egenskaper är gemensamma för alla poster i Azure Active Directory.

| Egenskap  | Beskrivning |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Typ av Azure AD-händelse. |
| ExtendedProperties | De utökade egenskaperna för Azure AD-händelse. |


### <a name="azure-active-directory-account-logon"></a>Inloggning för Azure Active Directory-konto
Dessa poster skapas när en Active Directory-användare försöker logga in.

| Egenskap  | Beskrivning |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectoryAccountLogon |
| Program | Programmet som utlöser händelsen konto inloggning, till exempel Office 15. |
| Client | Information om klienten enhet, enhetens operativsystem och enhetens webbläsare som användes för den händelsens konto logga in. |
| LoginStatus | Den här egenskapen är direkt från OrgIdLogon.LoginStatus. Mappningen av olika intressanta inloggningsfel kan göras av avisering algoritmer. |
| UserDomain | Klient ID-Information (TII). | 


### <a name="azure-active-directory"></a>Azure Active Directory
Dessa poster skapas för att ändra eller tillägg har gjorts i Azure Active Directory-objekt.

| Egenskap  | Beskrivning |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | Den användare som åtgärden (som identifieras med egenskapen Operation) utfördes på. |
| Aktör | Användaren eller tjänstens huvudnamn som utförde åtgärden. |
| ActorContextId | GUID för den organisation som aktören tillhör. |
| ActorIpAddress | Skådespelare, IP-adress i IPV4 eller IPV6-adressformat. |
| InterSystemsId | GUID som spårar åtgärder för komponenter i Office 365-tjänsten. |
| IntraSystemId |   GUID som genereras av Azure Active Directory för att spåra åtgärden. |
| SupportTicketId | Biljett-ID kundsupporten för åtgärden i ”act-on-behalf-of” situationer. |
| TargetContextId | GUID för den organisation som den angivna användaren tillhör. |


### <a name="data-center-security"></a>Datacenter-säkerhet
Dessa poster skapas från Data Center Security granskningsdata.  

| Egenskap  | Beskrivning |
|:--- |:--- |
| EffectiveOrganization | Namnet på den klient som varit mål för höjning/cmdleten på. |
| ElevationApprovedTime | Tidsstämpel för när höjningen har godkänts. |
| ElevationApprover | Namnet på en Microsoft-chef. |
| ElevationDuration | Den tid som höjningen var aktiv. |
| ElevationRequestId |  En unik identifierare för höjning-begäran. |
| ElevationRole | Rollen höjningen har begärts för. |
| ElevationTime | Starttid för höjningen. |
| Starttid | Starttiden för cmdlet-körningen. |


### <a name="exchange-admin"></a>Exchange-administratör
Dessa poster skapas när ändringar görs i Exchange-konfiguration.

| Egenskap  | Beskrivning |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeAdmin |
| ExternalAccess |  Anger om cmdleten kördes av en användare i din organisation, av Microsoft datacenter-personal eller ett tjänstkonto för datacenter eller genom en delegerad administratör. Värdet falskt anger att cmdleten kördes av någon i din organisation. Värdet True anger att cmdleten kördes av datacenter personal, ett tjänstkonto för datacenter eller en delegerad administratör. |
| ModifiedObjectResolvedName |  Det här är användarvänligt namn på det objekt som har ändrats av cmdlet: en. Detta loggas endast om cmdleten ändrar objektet. |
| OrganizationName | Namnet på klienten. |
| OriginatingServer | Namnet på den server där cmdleten kördes. |
| Parametrar | Namn och värde för alla parametrar som användes med cmdlet: en som identifieras i Operations-egenskapen. |


### <a name="exchange-mailbox"></a>Exchange Mailbox
Dessa poster skapas när ändringar eller tillägg görs till Exchange-postlådor.

| Egenskap  | Beskrivning |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| ClientInfoString | Information om e-postklienten som användes för att utföra åtgärden, till exempel en webbläsarversion, Outlook-version och information om mobila enheter. |
| Client_IPAddress | IP-adressen för den enhet som användes när åtgärden har loggats. IP-adressen visas i en IPv4- eller IPv6-adressformat. |
| ClientMachineName | Namnet på den dator som är värd för Outlook-klienten. |
| ClientProcessName | Den e-postklienten som användes för att få åtkomst till postlådan. |
| ClientVersion | Versionen av den e-postklienten. |
| InternalLogonType | Reserverat för internt bruk. |
| Logon_Type | Anger vilken typ av användare som postlådan och som utförde åtgärden som loggades. |
| LogonUserDisplayName |    Ett användarvänligt namn för den användare som utförde åtgärden. |
| LogonUserSid | SID för användaren som utförde åtgärden. |
| MailboxGuid | Exchange-GUID för postlådan som användes. |
| MailboxOwnerMasterAccountSid | Postlåda ägare kontots huvudkonto SID. |
| MailboxOwnerSid | SID för postlådans ägare. |
| MailboxOwnerUPN | E-postadressen för den person som äger postlådan som användes. |


### <a name="exchange-mailbox-audit"></a>Exchange-postlåda granskning
Dessa poster skapas när en postlåda granskningspost skapas.

| Egenskap  | Beskrivning |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| Objekt | Representerar det objektet som åtgärden utfördes | 
| SendAsUserMailboxGuid | Exchange-GUID för postlådan som användes för att skicka e-post. |
| SendAsUserSmtp | SMTP-adress för den användare som har personifierats. |
| SendonBehalfOfUserMailboxGuid | GUID för postlådan som användes för att skicka e-post för Exchange. |
| SendOnBehalfOfUserSmtp | SMTP-adress för den användare som för vars räkning e-postmeddelandet skickas. |


### <a name="exchange-mailbox-audit-group"></a>Exchange-postlåda Audit-gruppen
Dessa poster skapas när ändringar eller tillägg som görs till Exchange-grupper.

| Egenskap  | Beskrivning |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | Information om varje objekt i gruppen. |
| CrossMailboxOperations | Anger om åtgärden involverad mer än en postlåda. |
| DestMailboxId | Ange endast om parametern CrossMailboxOperations är sant. Anger mål postlådan GUID. |
| DestMailboxOwnerMasterAccountSid | Ange endast om parametern CrossMailboxOperations är sant. Anger SID för huvudkontot SID för target postlådans ägare. |
| DestMailboxOwnerSid | Ange endast om parametern CrossMailboxOperations är sant. Anger SID för mål-postlåda. |
| DestMailboxOwnerUPN | Ange endast om parametern CrossMailboxOperations är sant. Anger UPN-namnet för ägaren till mål-postlåda. |
| DestFolder | Målmapp för åtgärder som till exempel flytta. |
| Mapp | Den mapp där det finns en grupp med objekt. |
| Mappar |     Information om mapparna källa som ingår i en funktion. Om exempelvis mappar är valt och tas sedan bort. |


### <a name="sharepoint-base"></a>SharePoint-bas
Dessa egenskaper är gemensamma för alla poster i SharePoint.

| Egenskap  | Beskrivning |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| Händelsekälla | Identifierar att en händelse har inträffat i SharePoint. Möjliga värden är SharePoint eller ObjectModel. |
| ItemType | Typ av objekt som har använts eller ändrats. Se tabellen ItemType mer information om vilka typer av objekt. |
| MachineDomainInfo | Information om synkroniseringsåtgärder för enheten. Den här informationen rapporteras endast om den finns i begäran. |
| MachineId |   Information om synkroniseringsåtgärder för enheten. Den här informationen rapporteras endast om den finns i begäran. |
| Site_ | GUID för den plats där filen eller mappen som används av användaren finns. |
| Source_Name | Entiteten som utlöste åtgärden granskad. Möjliga värden är SharePoint eller ObjectModel. |
| UserAgent | Information om användarens klient eller webbläsare. Den här informationen tillhandahålls av klienten eller webbläsare. |


### <a name="sharepoint-schema"></a>SharePoint Schema
Dessa poster skapas när ändringar görs i SharePoint.

| Egenskap  | Beskrivning |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | Valfri sträng för anpassade händelser. |
| Event_Data |  Valfritt nyttolast för anpassade händelser. |
| ModifiedProperties | Egenskapen ingår för administratörshändelser, t.ex att lägga till en användare som en medlem i en plats eller en webbplatsgrupp samling administratör. Egenskapen innehåller namnet på den egenskap som ändrades (t.ex, platsen administratörsgruppen), det nya värdet för egenskapen ändrade (sådana den användare som har lagts till som en plats-administratör) och det tidigare värdet för det ändrade objektet. |


### <a name="sharepoint-file-operations"></a>SharePoint-filåtgärder
Dessa poster skapas som svar på filåtgärder i SharePoint.

| Egenskap  | Beskrivning |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | Filnamnstillägget för en fil som kopieras eller flyttas. Den här egenskapen visas bara för FileCopied och FileMoved händelser. |
| DestinationFileName | Namnet på den fil som kopieras eller flyttas. Den här egenskapen visas bara för FileCopied och FileMoved händelser. |
| DestinationRelativeUrl | URL till målmappen där en fil kopieras eller flyttas. Kombinationen av värden för parametrarna SiteURL, DestinationRelativeURL och DestinationFileName är detsamma som värdet för egenskapen ObjectID, vilket är den fullständiga sökvägen för filen som du kopierade. Den här egenskapen visas bara för FileCopied och FileMoved händelser. |
| SharingType | Typ av behörighet som har tilldelats till den användare som resursen har delats med. Den här användaren identifieras med parametern UserSharedWith. |
| Site_Url | URL till platsen där filen eller mappen som används av användaren äger rum. |
| SourceFileExtension | Filnamnstillägget för filen som användes av användaren. Den här egenskapen är tom om det objekt som användes är en mapp. |
| SourceFileName |  Namnet på filen eller mappen som används av användaren. |
| SourceRelativeUrl | URL till den mapp som innehåller filen kan nås av användaren. Kombinationen av värden för parametrarna SiteURL, SourceRelativeURL och SourceFileName är detsamma som värdet för egenskapen ObjectID, vilket är den fullständiga sökvägen för filen som används av användaren. |
| UserSharedWith |  Den användare som en resurs har delats med. |




## <a name="sample-log-searches"></a>Exempel på loggsökningar
Följande tabell innehåller exempel på sökningar i loggen för uppdateringsposter som har samlats in av den här lösningen.

| Söka i data | Beskrivning |
| --- | --- |
|Uppräkning av alla åtgärder på Office 365-prenumerationen |OfficeActivity &#124; sammanfatta antal() efter åtgärd |
|Användningen av SharePoint-webbplatser|OfficeActivity &#124; där OfficeWorkload = ~ ”sharepoint” &#124; sammanfatta antal() efter SiteUrl \| sortera efter antal asc|
|Filåtgärder för åtkomst efter användartyp|Sök i (OfficeActivity) OfficeWorkload = ~ ”azureactivedirectory” och ”MyTest”|
|Sök med ett specifikt nyckelord|Type=OfficeActivity OfficeWorkload=azureactivedirectory "MyTest"|
|Övervaka externa åtgärder på Exchange|OfficeActivity &#124; där OfficeWorkload = ~ ”exchange” och ExternalAccess == true|



## <a name="next-steps"></a>Nästa steg
* Använd [logga frågor i Azure Monitor](../log-query/log-query-overview.md) att visa detaljerad uppdateringsinformation.
* [Skapa dina egna instrumentpaneler](../learn/tutorial-logs-dashboards.md) att visa dina favorit Office 365-sökfrågor.
* [Skapa aviseringar](../platform/alerts-overview.md) för att proaktivt aviseras om viktiga Office 365-aktiviteter.  
