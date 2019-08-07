---
title: Hanterings lösning för Office 365 i Azure | Microsoft Docs
description: Den här artikeln innehåller information om konfiguration och användning av Office 365-lösningen i Azure.  Den innehåller en detaljerad beskrivning av de Office 365-poster som skapats i Azure Monitor.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/01/2019
ms.author: bwren
ms.openlocfilehash: d50b3ab68b406db47a4cc8fec081b2fc076071d1
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2019
ms.locfileid: "68741663"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Office 365-hanterings lösning i Azure (för hands version)

![Logotyp för Office 365](media/solution-office-365/icon.png)


> [!NOTE]
> Den rekommenderade metoden för att installera och konfigurera Office 365-lösningen är att aktivera [office 365-anslutningen](../../sentinel/connect-office-365.md) i [Azure Sentinel](../../sentinel/overview.md) i stället för att följa stegen i den här artikeln. Det här är en uppdaterad version av Office 365-lösningen med en förbättrad konfigurations upplevelse. Om du vill ansluta Azure AD-loggar kan du antingen använda [Azure Sentinel Azure AD Connector](../../sentinel/connect-azure-active-directory.md) eller [Konfigurera Azure AD-diagnostikinställningar](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md), som ger bättre logg data än hanterings loggarna för Office 365. 
>
> När du har registrerat [Azure Sentinel](../../sentinel/quickstart-onboard.md)anger du Log Analytics arbets ytan som du vill att lösningen Office 365 ska installeras i. När du aktiverar anslutningen är lösningen tillgänglig i arbets ytan och används exakt på samma sätt som andra övervaknings lösningar som du har installerat.
>
> Användare av Azures myndighets moln måste installera Office 365 med hjälp av stegen i den här artikeln eftersom Azure Sentinel inte ännu är tillgängligt i det offentliga molnet.

Med hanterings lösningen för Office 365 kan du övervaka din Office 365-miljö i Azure Monitor.

- Övervaka användar aktiviteter på dina Office 365-konton för att analysera användnings mönster och identifiera beteende trender. Du kan till exempel extrahera vissa användnings scenarier, till exempel filer som delas utanför organisationen eller de mest populära SharePoint-webbplatserna.
- Övervaka administratörs aktiviteter för att spåra konfigurations ändringar eller åtgärder med hög behörighet.
- Identifiera och undersök oönskade användar beteenden, som kan anpassas efter organisationens behov.
- Demonstrera granskning och efterlevnad. Du kan till exempel övervaka fil åtkomst åtgärder på konfidentiella filer, vilket kan hjälpa dig med gransknings-och efterlevnads processen.
- Utför drift fel sökning genom att använda [logg frågor](../log-query/log-query-overview.md) ovanpå Office 365-aktivitets data i din organisation.


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

Följande krävs innan den här lösningen installeras och konfigureras.

- Organisations Office 365-prenumeration.
- Autentiseringsuppgifter för ett användar konto som är en global administratör.
- Om du vill ta emot gransknings data måste du [Konfigurera granskning](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin) i din Office 365-prenumeration.  Observera att [post lådans granskning](https://technet.microsoft.com/library/dn879651.aspx) konfigureras separat.  Du kan fortfarande installera lösningen och samla in andra data om granskning inte har kon figurer ATS.
 

## <a name="management-packs"></a>Hanteringspaket

Den här lösningen installerar inte några hanterings paket i [anslutna hanterings grupper](../platform/om-agents.md).
  

## <a name="install-and-configure"></a>Installera och konfigurera

Börja med att lägga till [Office 365-lösningen i din prenumeration](solutions.md#install-a-monitoring-solution). När den har lagts till måste du utföra konfigurations stegen i det här avsnittet för att ge den åtkomst till din Office 365-prenumeration.

### <a name="required-information"></a>Nödvändig information

Samla in följande information innan du påbörjar den här proceduren.

Från din Log Analytics arbets yta:

- Arbetsytans namn: Arbets ytan där Office 365-data kommer att samlas in.
- Resursgruppsnamn: Den resurs grupp som innehåller arbets ytan.
- ID för Azure-prenumeration: Prenumerationen som innehåller arbets ytan.

Från din Office 365-prenumeration:

- Användarnamn: E-postadress för ett administratörs konto.
- Klientorganisations-ID: Unikt ID för Office 365-prenumeration.
- Klient-ID: 16 tecken sträng som representerar Office 365-klienten.
- Klient hemlighet: Krypterad sträng som krävs för autentisering.

### <a name="create-an-office-365-application-in-azure-active-directory"></a>Skapa ett Office 365-program i Azure Active Directory

Det första steget är att skapa ett program i Azure Active Directory att hanterings lösningen ska använda för att komma åt din Office 365-lösning.

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com/).
1. Välj **Azure Active Directory** och **Appregistreringar**.
1. Klicka på **Ny programregistrering**.

    ![Lägg till registrerings program](media/solution-office-365/add-app-registration.png)
1. Ange ett program **namn** och **en inloggnings-URL**.  Namnet ska vara en beskrivande.  Använd `http://localhost` för URL: en och behåll _webbapp/API_ för **program typen**
    
    ![Skapa program](media/solution-office-365/create-application.png)
1. Klicka på **skapa** och verifiera programinformationen.

    ![Registrerad app](media/solution-office-365/registered-app.png)

### <a name="configure-application-for-office-365"></a>Konfigurera program för Office 365

1. Klicka på **Inställningar** för att öppna menyn **Inställningar** .
1. Välj **egenskaper**. Ändra **flera klienter** till _Ja_.

    ![Inställningar för flera klient organisationer](media/solution-office-365/settings-multitenant.png)

1. Välj **nödvändiga behörigheter** på menyn **Inställningar** och klicka sedan på **Lägg till**.
1. Klicka på **Välj ett API** och sedan hanterings- **API: er för Office 365**. Klicka på **Office 365 Management-API: er**. Klicka på **Välj**.

    ![Välj API](media/solution-office-365/select-api.png)

1. Under **Välj behörigheter** väljer du följande alternativ för både **program behörigheter** och **delegerade behörigheter**:
   - Läs tjänsthälsoinformation för din organisation
   - Läs aktivitetsdata för din organisation
   - Läs aktivitetsrapporter för din organisation

     ![Välj API](media/solution-office-365/select-permissions.png)

1. Klicka på **Välj** och sedan på **Slutför**.
1. Klicka på **bevilja behörigheter** och klicka sedan på **Ja** när du uppmanas att verifiera.

    ![Bevilja behörigheter](media/solution-office-365/grant-permissions.png)

### <a name="add-a-key-for-the-application"></a>Lägg till en nyckel för programmet

1. Välj **nycklar** på menyn **Inställningar** .
1. Ange en **Beskrivning** och **varaktighet** för den nya nyckeln.
1. Klicka på **Spara** och kopiera sedan **värdet** som genereras.

    ![Nycklar](media/solution-office-365/keys.png)

### <a name="add-admin-consent"></a>Lägg till administratörs medgivande

Om du vill aktivera det administrativa kontot för första gången måste du ge det administrativa godkännandet för programmet. Du kan göra detta med ett PowerShell-skript. 

1. Spara följande skript som *office365_consent. ps1*.

    ```powershell
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

2. Kör skriptet med följande kommando. Du kommer att uppmanas att ange autentiseringsuppgifter två gånger. Ange autentiseringsuppgifterna för din Log Analytics-arbetsyta först och sedan de globala administratörs uppgifterna för din Office 365-klient.

    ```
    .\office365_consent.ps1 -WorkspaceName <Workspace name> -ResourceGroupName <Resource group name> -SubscriptionId <Subscription ID>
    ```

    Exempel:

    ```
    .\office365_consent.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631- yyyyyyyyyyyy'
    ```

1. Du kommer att visas ett fönster liknande det som visas nedan. Klicka på **acceptera**.
    
    ![Administratörsmedgivande](media/solution-office-365/admin-consent.png)

### <a name="subscribe-to-log-analytics-workspace"></a>Prenumerera på Log Analytics arbets yta

Det sista steget är att prenumerera på programmet till din Log Analytics-arbetsyta. Du kan också göra detta med ett PowerShell-skript.

1. Spara följande skript som *office365_subscription. ps1*.

    ```powershell
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

    ```powershell
    .\office365_subscription.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeUsername 'admin@contoso.com' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx' -OfficeClientId 'f8f14c50-5438-4c51-8956-zzzzzzzzzzzz' -OfficeClientSecret 'y5Lrwthu6n5QgLOWlqhvKqtVUZXX0exrA2KRHmtHgQb='
    ```

### <a name="troubleshooting"></a>Felsökning

Följande fel kan visas om ditt program redan prenumererar på den här arbets ytan eller om klienten prenumererar på en annan arbets yta.

```Output
Invoke-WebRequest : {"Message":"An error has occurred."}
At C:\Users\v-tanmah\Desktop\ps scripts\office365_subscription.ps1:161 char:19
+ $officeresponse = Invoke-WebRequest @Officeparams
+                   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (System.Net.HttpWebRequest:HttpWebRequest) [Invoke-WebRequest], WebException
    + FullyQualifiedErrorId : WebCmdletWebResponseException,Microsoft.PowerShell.Commands.InvokeWebRequestCommand 
```

Följande fel kan visas om ogiltiga parameter värden anges.

```Output
Select-AzSubscription : Please provide a valid tenant or a valid subscription.
At line:12 char:18
+ ... cription = (Select-AzSubscription -SubscriptionId $($Subscriptio ...
+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzContext], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.SetAzContextCommand

```

## <a name="uninstall"></a>Avinstallera

Du kan ta bort hanterings lösningen för Office 365 med processen i [ta bort en hanterings lösning](solutions.md#remove-a-monitoring-solution). Detta kommer inte att stoppa data som samlas in från Office 365 till Azure Monitor. Följ proceduren nedan om du vill avbryta prenumerationen på Office 365 och sluta samla in data.

1. Spara följande skript som *office365_unsubscribe. ps1*.

    ```powershell
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

    ```powershell
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

## <a name="data-collection"></a>Datainsamling

### <a name="supported-agents"></a>Agenter som stöds

Office 365-lösningen hämtar inte data från någon av de [Log Analytics agenterna](../platform/agent-data-sources.md).  Data hämtas direkt från Office 365.

### <a name="collection-frequency"></a>Insamlingsfrekvens

Det kan ta några timmar innan data samlas in från början. När den börjar samla in skickar Office 365 en [webhook-avisering](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) med detaljerade data till Azure Monitor varje gång en post skapas. Den här posten är tillgänglig i Azure Monitor inom några minuter efter att de mottagits.

## <a name="using-the-solution"></a>Använda lösningen

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

När du lägger till Office 365-lösningen i din Log Analytics arbets yta läggs **office 365** -panelen till på instrument panelen. Den här panelen visar antal och en grafisk representation av antalet datorer i din miljö och deras uppdateringskompatibilitet.<br><br>
![Sammanfattnings panel för Office 365](media/solution-office-365/tile.png)  

Klicka på panelen **office 365** för att öppna **Office 365** -instrumentpanelen.

![Office 365-instrumentpanel](media/solution-office-365/dashboard.png)  

Instrumentpanelen innehåller kolumnerna i följande tabell. Varje kolumn listar de tio främsta aviseringarna genom att räkna matchningen av kolumnens kriterier för det angivna omfånget och tidsintervallet. Du kan köra en loggs ökning som ger hela listan genom att klicka på Visa alla längst ned i kolumnen eller genom att klicka på kolumn rubriken.

| Kolumn | Beskrivning |
|:--|:--|
| Åtgärder | Innehåller information om aktiva användare från alla övervakade Office 365-prenumerationer. Du kommer också att kunna se antalet aktiviteter som sker över tid.
| Exchange | Visar en uppdelning av Exchange Server-aktiviteter som Add-Mailbox-behörighet eller ange-Mailbox. |
| SharePoint | Visar de viktigaste aktiviteterna som användare utför i SharePoint-dokument. När du ökar detalj nivån från den här panelen visar Sök sidan information om dessa aktiviteter, till exempel mål dokumentet och platsen för aktiviteten. För en fil som används, kan du till exempel se det dokument som har åtkomst till, dess associerade konto namn och IP-adress. |
| Azure Active Directory | Omfattar de vanligaste användar aktiviteterna, till exempel återställning av användar lösen ord och inloggnings försök. När du ökar detalj nivån kommer du att kunna se information om dessa aktiviteter, till exempel resultat status. Detta är mest användbart om du vill övervaka misstänkta aktiviteter på din Azure Active Directory. |




## <a name="azure-monitor-log-records"></a>Azure Monitor logg poster

Alla poster som skapats i arbets ytan Log Analytics i Azure Monitor av Office 365-lösningen har en **typ** av **OfficeActivity**.  Egenskapen **OfficeWorkload** bestämmer vilken Office 365-tjänst som posten refererar till-Exchange, AzureActiveDirectory, SharePoint eller OneDrive.  Egenskapen **RecordType** anger typ av åtgärd.  Egenskaperna varierar för varje åtgärds typ och visas i tabellerna nedan.

### <a name="common-properties"></a>Gemensamma egenskaper

Följande egenskaper är gemensamma för alla Office 365-poster.

| Egenskap | Description |
|:--- |:--- |
| type | *OfficeActivity* |
| ClientIP | IP-adressen för den enhet som användes när aktiviteten loggades. IP-adressen visas antingen i ett IPv4-eller IPv6-adress format. |
| OfficeWorkload | Office 365-tjänst som posten refererar till.<br><br>AzureActiveDirectory<br>Exchange<br>SharePoint|
| Åtgärd | Namnet på användaren eller administratörs aktiviteten.  |
| OrganizationId | GUID för organisationens Office 365-klient. Det här värdet är alltid detsamma för din organisation, oavsett Office 365-tjänsten där det sker. |
| RecordType | Typ av åtgärd som utförs. |
| ResultStatus | Anger om åtgärden (anges i egenskapen operation) lyckades eller inte. Möjliga värden är lyckades, PartiallySucceeded eller misslyckades. För Exchange admin-aktivitet är värdet antingen sant eller falskt. |
| UserId | UPN (User Principal Name) för den användare som utförde åtgärden som resulterade i att posten loggades. till exempel my_name@my_domain_name. Observera att poster för aktivitet som utförs av system konton (t. ex. SHAREPOINT\system eller NTAUTHORITY\SYSTEM) också ingår. | 
| UserKey | Ett alternativt ID för den användare som identifierats i UserId-egenskapen.  Till exempel fylls den här egenskapen i med Passport-unikt ID (PUID) för händelser som utförs av användare i SharePoint, OneDrive för företag och Exchange. Den här egenskapen kan också ange samma värde som UserID-egenskapen för händelser som inträffar i andra tjänster och händelser som utförs av system konton|
| UserType | Typ av användare som utförde åtgärden.<br><br>Admin<br>Program<br>DcAdmin<br>Normal<br>Reserverad<br>ServicePrincipal<br>System |


### <a name="azure-active-directory-base"></a>Azure Active Directory bas

Följande egenskaper är gemensamma för alla Azure Active Directory poster.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Typ av Azure AD-händelse. |
| ExtendedProperties | Utökade egenskaper för Azure AD-händelsen. |


### <a name="azure-active-directory-account-logon"></a>Azure Active Directory konto inloggning

Dessa poster skapas när en Active Directory användare försöker logga in.

| Egenskap | Beskrivning |
|:--- |:--- |
| `OfficeWorkload` | AzureActiveDirectory |
| `RecordType`     | AzureActiveDirectoryAccountLogon |
| `Application` | Det program som utlöser konto inloggnings händelsen, till exempel Office 15. |
| `Client` | Information om klient enheten, enhetens operativ system och enhetens webbläsare som användes för konto inloggnings händelsen. |
| `LoginStatus` | Den här egenskapen är från OrgIdLogon. LoginStatus direkt. Mappningen av olika intressanta inloggnings problem kan göras genom att algoritmer för aviseringar. |
| `UserDomain` | Klientens identitets information (TII). | 


### <a name="azure-active-directory"></a>Azure Active Directory

Dessa poster skapas när ändringar eller tillägg görs till Azure Active Directory objekt.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | Användaren som åtgärden (identifieras av egenskapen operation) utfördes på. |
| Aktör | Användaren eller tjänstens huvud namn som utförde åtgärden. |
| ActorContextId | GUID för den organisation som aktören tillhör. |
| ActorIpAddress | Aktörens IP-adress i IPV4-eller IPV6-adress format. |
| InterSystemsId | GUID som spårar åtgärder mellan komponenter i Office 365-tjänsten. |
| IntraSystemId |   Det GUID som genereras av Azure Active Directory för att spåra åtgärden. |
| SupportTicketId | Kund Supportens biljett-ID för åtgärden i "Act-on-of"-situationer. |
| TargetContextId | GUID för den organisation som mål användaren tillhör. |


### <a name="data-center-security"></a>Säkerhet för data Center

Dessa poster skapas från data Center säkerhets gransknings data.  

| Egenskap | Beskrivning |
|:--- |:--- |
| EffectiveOrganization | Namnet på den klient som höjningen/cmdleten riktades mot. |
| ElevationApprovedTime | Tidsstämpeln för när höjningen godkändes. |
| ElevationApprover | Namnet på en Microsoft-chef. |
| ElevationDuration | Varaktigheten för vilken höjningen var aktiv. |
| ElevationRequestId |  En unik identifierare för begäran om höjning. |
| ElevationRole | Rollen som höjningen begärdes för. |
| ElevationTime | Start tiden för utökningen. |
| Start_Time | Start tiden för cmdlet-körningen. |


### <a name="exchange-admin"></a>Exchange-administratör

Dessa poster skapas när ändringar görs i Exchange-konfigurationen.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeAdmin |
| ExternalAccess |  Anger om cmdleten kördes av en användare i din organisation, av Microsoft Data Center-personal eller ett Data Center tjänst konto eller av en delegerad administratör. Värdet FALSE anger att cmdleten kördes av någon i din organisation. Värdet true anger att cmdleten kördes av data Center personal, ett Data Center tjänst konto eller en delegerad administratör. |
| ModifiedObjectResolvedName |  Detta är användarvänligt namn på objektet som ändrades av cmdleten. Detta loggas endast om cmdleten ändrar objektet. |
| Organisationsnamn | Namnet på klienten. |
| OriginatingServer | Namnet på den server från vilken cmdleten kördes. |
| Parametrar | Namnet och värdet för alla parametrar som användes med cmdleten som identifierades i egenskapen Operations. |


### <a name="exchange-mailbox"></a>Exchange-postlåda

Dessa poster skapas när ändringar eller tillägg görs i Exchange-postlådor.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| ClientInfoString | Information om e-postklienten som användes för att utföra åtgärden, till exempel en webb läsar version, Outlook-version och information om mobila enheter. |
| Client_IPAddress | IP-adressen för den enhet som användes när åtgärden loggades. IP-adressen visas antingen i ett IPv4-eller IPv6-adress format. |
| ClientMachineName | Namnet på datorn som är värd för Outlook-klienten. |
| ClientProcessName | E-postklienten som användes för att få åtkomst till post lådan. |
| ClientVersion | Versionen av e-postklienten. |
| InternalLogonType | Reserverat för internt bruk. |
| Logon_Type | Anger vilken typ av användare som har åtkomst till post lådan och utförde åtgärden som loggades. |
| LogonUserDisplayName |    Det användarvänliga namnet på den användare som utförde åtgärden. |
| LogonUserSid | SID för den användare som utförde åtgärden. |
| MailboxGuid | Exchange-GUID för den post låda som har öppnats. |
| MailboxOwnerMasterAccountSid | Post lådans ägar kontos huvud konto-SID. |
| MailboxOwnerSid | SID för post lådans ägare. |
| MailboxOwnerUPN | E-postadressen till den person som äger post lådan som har öppnats. |


### <a name="exchange-mailbox-audit"></a>Granskning av Exchange-postlåda

Dessa poster skapas när en post lådas gransknings post skapas.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| Objekt | Representerar objektet som åtgärden utfördes på | 
| SendAsUserMailboxGuid | Exchange-GUID för den post låda som användes för att skicka e-post som. |
| SendAsUserSmtp | SMTP-adress för den användare som personifieras. |
| SendonBehalfOfUserMailboxGuid | Exchange-GUID för den post låda som användes för att skicka e-post åt. |
| SendOnBehalfOfUserSmtp | SMTP-adress till den användare på vars vägnar e-postmeddelandet skickas. |


### <a name="exchange-mailbox-audit-group"></a>Gransknings grupp för Exchange-postlåda

Dessa poster skapas när ändringar eller tillägg görs i Exchange-grupper.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | Information om varje objekt i gruppen. |
| CrossMailboxOperations | Anger om åtgärden involverar fler än en post låda. |
| DestMailboxId | Ange endast om parametern CrossMailboxOperations är true. Anger mål post lådans GUID. |
| DestMailboxOwnerMasterAccountSid | Ange endast om parametern CrossMailboxOperations är true. Anger SID för huvud kontots SID för ägaren till mål brev lådan. |
| DestMailboxOwnerSid | Ange endast om parametern CrossMailboxOperations är true. Anger SID-mål post lådans SID. |
| DestMailboxOwnerUPN | Ange endast om parametern CrossMailboxOperations är true. Anger UPN för mål post lådans ägare. |
| DestFolder | Målmappen för åtgärder som flytta. |
| Mapp | Mappen där en grupp objekt finns. |
| Mappar |     Information om källmappen som ingår i en åtgärd. till exempel om mappar är markerade och tas bort. |


### <a name="sharepoint-base"></a>SharePoint-bas

De här egenskaperna är gemensamma för alla SharePoint-poster.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | Anger att en händelse har inträffat i SharePoint. Möjliga värden är SharePoint eller ObjectModel. |
| ItemType | Typ av objekt som har öppnats eller ändrats. Se tabellen ItemType för information om objekt typer. |
| MachineDomainInfo | Information om synkronisering av enhets åtgärder. Den här informationen rapporteras endast om den finns i begäran. |
| MachineId |   Information om synkronisering av enhets åtgärder. Den här informationen rapporteras endast om den finns i begäran. |
| Site_ | GUID för den plats där filen eller mappen som används av användaren finns. |
| Source_Name | Den entitet som utlöste den granskade åtgärden. Möjliga värden är SharePoint eller ObjectModel. |
| UserAgent | Information om användarens klient eller webbläsare. Den här informationen tillhandahålls av klienten eller webbläsaren. |


### <a name="sharepoint-schema"></a>SharePoint Schema

Dessa poster skapas när konfigurations ändringar görs i SharePoint.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | Valfri sträng för anpassade händelser. |
| Event_Data |  Valfri nytto last för anpassade händelser. |
| ModifiedProperties | Egenskapen ingår för administratörs händelser, till exempel att lägga till en användare som medlem i en webbplats eller en administratörs grupp för webbplats samlingar. Egenskapen innehåller namnet på egenskapen som ändrades (t. ex. plats administratörs gruppen), det nya värdet för egenskapen modified (till exempel den användare som lades till som en plats administratör) och det tidigare värdet för det ändrade objektet. |


### <a name="sharepoint-file-operations"></a>SharePoint-filåtgärder

Dessa poster skapas som svar på fil åtgärder i SharePoint.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | Fil namns tillägget för en fil som kopieras eller flyttas. Den här egenskapen visas bara för FileCopied-och FileMoved-händelser. |
| DestinationFileName | Namnet på filen som kopieras eller flyttas. Den här egenskapen visas bara för FileCopied-och FileMoved-händelser. |
| DestinationRelativeUrl | URL-adressen till målmappen dit en fil kopieras eller flyttas. Kombinationen av värdena för parametrarna SiteURL, DestinationRelativeURL och DestinationFileName är samma som värdet för egenskapen ObjectID, som är det fullständiga Sök vägs namnet för den fil som kopierades. Den här egenskapen visas bara för FileCopied-och FileMoved-händelser. |
| SharingType | Den typ av delnings behörigheter som har tilldelats användaren som resursen delades med. Den här användaren identifieras av UserSharedWith-parametern. |
| Site_Url | URL-adressen till den plats där filen eller mappen som används av användaren finns. |
| SourceFileExtension | Fil namns tillägget för den fil som har öppnats av användaren. Den här egenskapen är tom om objektet som har öppnats är en mapp. |
| SourceFileName |  Namnet på filen eller mappen som används av användaren. |
| SourceRelativeUrl | URL till den mapp som innehåller den fil som används av användaren. Kombinationen av värdena för parametrarna SiteURL, SourceRelativeURL och SourceFileName är samma som värdet för egenskapen ObjectID, som är det fullständiga Sök vägs namnet för den fil som används av användaren. |
| UserSharedWith |  Användaren som en resurs delades med. |




## <a name="sample-log-searches"></a>Exempel på loggsökningar

Följande tabell innehåller exempel på sökningar i loggen för uppdateringsposter som har samlats in av den här lösningen.

| Söka i data | Beskrivning |
| --- | --- |
|Antal åtgärder på din Office 365-prenumeration |OfficeActivity &#124; Sammanfattning av antal () efter åtgärd |
|Användning av SharePoint-webbplatser|OfficeActivity &#124; där OfficeWorkload = ~ "SharePoint" &#124; sammanfatta antal () efter SiteUrl \| sortera efter antal ASC|
|Fil åtkomst åtgärder efter användar typ|Sök i (OfficeActivity) OfficeWorkload = ~ "azureactivedirectory" och "testtest"|
|Sök med ett speciellt nyckelord|Skriv = OfficeActivity OfficeWorkload = azureactivedirectory "test"|
|Övervaka externa åtgärder på Exchange|OfficeActivity &#124; där OfficeWorkload = ~ "Exchange" och ExternalAccess = = True|



## <a name="next-steps"></a>Nästa steg

* Använd [logg frågor i Azure Monitor](../log-query/log-query-overview.md) om du vill visa detaljerade uppdaterings data.
* [Skapa dina egna instrument paneler](../learn/tutorial-logs-dashboards.md) för att visa dina favorit Sök frågor för Office 365.
* [Skapa aviseringar](../platform/alerts-overview.md) för att proaktivt meddela viktiga Office 365-aktiviteter.  
