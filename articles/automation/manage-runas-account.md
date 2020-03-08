---
title: Hantera Azure Automation kör som-konton
description: Den här artikeln beskriver hur du hanterar Kör som-konton med PowerShell eller från portalen.
services: automation
ms.subservice: shared-capabilities
ms.date: 05/24/2019
ms.topic: conceptual
ms.openlocfilehash: f0ba7a9f196379aa16d9b652e8b1f33df6118c6e
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78892749"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Hantera Azure Automation kör som-konton

Kör som-konton i Azure Automation tillhandahålla autentisering för att hantera resurser i Azure med hjälp av Azure-cmdletar. När du skapar ett Kör som-konto skapar det en ny tjänst huvud användare i Azure Active Directory (AD) och tilldelar rollen deltagare till den här användaren på prenumerations nivå. För Runbooks som använder hybrid Runbook Worker på Azure Virtual Machines kan du använda [hanterade identiteter för Azure-resurser](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) i stället för kör som-konton för att autentisera till dina Azure-resurser.

Tjänstens huvud namn för ett Kör som-konto har inte behörighet att läsa Azure AD som standard. Om du vill lägga till behörigheter för att läsa eller hantera Azure AD måste du bevilja behörighet för tjänstens huvud namn under **API-behörigheter**. Läs mer i [lägga till behörigheter för åtkomst till webb-API: er](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installations anvisningar för AZ-modulen på Hybrid Runbook Worker finns i [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med hjälp av [hur du uppdaterar Azure PowerShell moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="types-of-run-as-accounts"></a>Typer av kör som-konton

Azure Automation använder två typer av kör som-konton:

* Kör som-konto i Azure
* Det klassiska kör som-kontot i Azure

>[!NOTE]
>Azure Cloud Solution Provider (CSP)-prenumerationer stöder endast Azure Resource Managers modellen. Icke-Azure Resource Manager tjänster är inte tillgängliga i programmet. När du använder en CSP-prenumeration skapas inte det klassiska kör som-kontot i Azure, men kör som-kontot i Azure skapas. Mer information om CSP-prenumerationer finns i [tillgängliga tjänster i CSP-prenumerationer](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).

### <a name="run-as-account"></a>Kör som-konto

Kör som-kontot hanterar resurser för [distributions modeller i Resource Manager](../azure-resource-manager/management/deployment-models.md) . Den utför följande uppgifter.

* Skapar ett Azure AD-program med ett självsignerat certifikat och ett tjänstobjektskonto för programmet i Azure AD och rollen Deltagare tilldelas för kontot i din aktuella prenumeration. Du kan ändra certifikat inställningen till ägare eller en annan roll. Mer information finns i [Rollbaserad åtkomstkontroll i Azure Automation](automation-role-based-access-control.md).
  
* Skapar en Automation-certifikattillgång med namnet **AzureRunAsCertificate** i det angivna Automation-kontot. Certifikat till gången innehåller certifikatets privata nyckel som Azure AD-programmet använder.
  
* Skapar en Automation-anslutningstillgång med namnet **AzureRunAsConnection** i det angivna Automation-kontot. Anslutnings till gången innehåller program-ID, klient-ID, prenumerations-ID och tumavtryck för certifikatet.

### <a name="azure-classic-run-as-account"></a>Klassiskt Kör som-konto i Azure

Det klassiska kör som-kontot i Azure hanterar klassiska resurser för [distributions modeller](../azure-resource-manager/management/deployment-models.md) . Du måste vara en medadministratör i prenumerationen för att kunna skapa eller förnya den här typen av konto.

Det klassiska kör som-kontot i Azure utför följande uppgifter.

  * Skapar ett hanterings certifikat i prenumerationen.

  * Skapar en Automation-certifikattillgång med namnet **AzureClassicRunAsCertificate** i det angivna Automation-kontot. Certifikattillgången innehåller den privata nyckelns certifikat som används av hanteringscertifikatet.

  * Skapar en Automation-anslutningstillgång med namnet **AzureClassicRunAsConnection** i det angivna Automation-kontot. Anslutnings till gången innehåller prenumerations namnet, prenumerations-ID och certifikatets till gångs namn.

## <a name="permissions"></a>Behörigheter för kör som-konto

I det här avsnittet definieras behörigheter för både vanliga kör som-konton och klassiska kör som-konton.

### <a name="permissions-to-configure-run-as-accounts"></a>Behörigheter för att konfigurera kör som-konton

Om du vill skapa eller uppdatera ett Kör som-konto måste du ha vissa behörigheter och behörigheter. En program administratör i Azure Active Directory och en ägare i en prenumeration kan slutföra alla uppgifter. I en situation där du har separering av uppgifter visar följande tabell en lista över aktiviteterna, motsvarande cmdlet och behörigheter som krävs:

|Aktivitet|Cmdlet  |Lägsta behörighet  |Där du anger behörigheter|
|---|---------|---------|---|
|Skapa Azure AD-program|[New-AzADApplication](/https://docs.microsoft.com/powershell/module/az.resources/new-azadapplication?view=azps-3.5.0)     | Programutvecklare, roll<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Start > Azure AD > App-registreringar |
|Lägg till en autentiseringsuppgift i programmet.|[New-AzADAppCredential](https://docs.microsoft.com/powershell/module/az.resources/new-azadappcredential?view=azps-3.5.0)     | Program administratör eller global administratör<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Start > Azure AD > App-registreringar|
|Skapa och hämta en Azure AD-tjänstens huvud namn|[New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-3.5.0)</br>[Get-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal?view=azps-3.5.0)     | Program administratör eller global administratör<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Start > Azure AD > App-registreringar|
|Tilldela eller hämta RBAC-rollen för det angivna huvudobjektet|[New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment?view=azps-3.5.0)</br>[Get-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/Get-AzRoleAssignment?view=azps-3.5.0)      | Administratör för användar åtkomst eller ägare eller ha följande behörigheter:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Prenumeration](../role-based-access-control/role-assignments-portal.md)</br>Start > prenumerationer > \<prenumerations namn\>-Access Control (IAM)|
|Skapa eller ta bort ett Automation-certifikat|[New-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.5.0)</br>[Remove-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationcertificate?view=azps-3.5.0)     | Deltagare i resurs gruppen         |Resurs grupp för Automation-konto|
|Skapa eller ta bort en Automation-anslutning|[New-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.5.0)</br>[Remove-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationconnection?view=azps-3.5.0)|Deltagare i resurs gruppen |Resurs grupp för Automation-konto|

<sup>1</sup> användare som inte är administratörer i din Azure AD-klient kan [registrera AD-program](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) om alternativet för Azure AD-klientens **användare kan registrera program** på sidan användar inställningar är inställt på **Ja**. Om program registrerings inställningen är **Nej**måste användaren som utför den här åtgärden vara som definieras i den här tabellen.

Om du inte är medlem i prenumerationens Active Directory instans innan du lägger till den globala administratörs rollen för prenumerationen läggs du till som gäst. I den här situationen får du ett meddelande **om att du inte har behörighet att skapa...** Varning på sidan Lägg till Automation-konto. 

Om du är medlem i prenumerationens Active Directory instans när rollen global administratör tilldelas, kan du också ta emot **att du inte har behörighet att skapa...** Varning på sidan Lägg till Automation-konto. I så fall kan du begära borttagning från prenumerationens Active Directory instans och sedan begära att du lägger till den igen, så att du blir fullständig användare i Active Directory. 

För att kontrol lera att den situation som genererar fel meddelandet har åtgärd ATS:

1. I fönstret Azure Active Directory i Azure Portal väljer du **användare och grupper**. 
2. Välj **alla användare**.
3. Välj ditt namn och välj sedan **profil**. 
4. Se till att värdet för attributet **användar typ** under användarens profil inte är inställt på **gäst**.

### <a name="permissions-classic"></a>Behörigheter för att konfigurera klassiska kör som-konton

Om du vill konfigurera eller förnya klassiska kör som-konton måste du ha rollen medadministratör på prenumerations nivå. Mer information om klassiska prenumerations behörigheter finns i [Azures klassiska prenumerations administratörer](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="creating-a-run-as-account-in-azure-portal"></a>Skapa ett Kör som-konto i Azure Portal

Utför följande steg för att uppdatera ditt Azure Automation-konto i Azure Portal. Du måste skapa kör som-och klassiska kör som-konton individuellt. Om du inte behöver hantera klassiska resurser kan du bara skapa Azure Kör som-kontot.

1. Logga in på Azure-portalen med ett konto som är medlem i rollen Prenumerationsadministratörer och som är medadministratör för prenumerationen.
2. Sök efter och välj **Automation-konton**.
3. På sidan Automation-konton väljer du ditt Automation-konto i listan över Automation-konton.
4. I det vänstra fönstret väljer du **Kör som-konton** i avsnittet konto inställningar.
5. Beroende på vilket konto du behöver väljer du antingen **Azures Kör som-konto** eller **Azures klassiska Kör som-konto**. 
6. Beroende på kontots intresse använder du fönstret Lägg till Azure kör som eller Lägg till Azures klassiska kör som-konto. När du har granskat översikts informationen klickar du på **skapa** för att fortsätta med att skapa kör som-kontot.
6. Medan Azure skapar Kör-som-kontot kan du följa förloppet under **Meddelanden** på menyn. En banderoll visas också som anger att kontot skapas. Processen kan ta några minuter att slutföra.

## <a name="creating-a-run-as-account-using-powershell"></a>Skapa ett Kör som-konto med hjälp av PowerShell

I följande lista finns kraven för att skapa ett Kör som-konto i PowerShell. Dessa krav gäller för båda typerna av kör som-konton.

* Windows 10 eller Windows Server 2016 med Azure Resource Manager modulerna 3.4.1 och senare. PowerShell-skriptet stöds inte i tidigare versioner av Windows.
* Azure PowerShell 1.0 och senare. Information om PowerShell 1.0-versionen finns i [Installera och konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Ett Automation-konto, som refereras till som värde för parametrarna *AutomationAccountName* och *ApplicationDisplayName* .
* Behörigheter som motsvarar vad som anges i de [behörigheter som krävs för att konfigurera kör som-konton](#permissions).

För att hämta värdena för *SubscriptionId*, *ResourceGroupName*och *AutomationAccountName*, som är obligatoriska parametrar för PowerShell-skriptet, slutför du nästa steg.

1. I Azure Portal väljer du **Automation-konton**.
1. På sidan Automation-konton väljer du ditt Automation-konto.
1. I avsnittet konto inställningar väljer du **Egenskaper**.
1. Anteckna värdena för **namn**, **prenumerations-ID**och **resurs grupp** på sidan Egenskaper. Dessa värden motsvarar värdena för PowerShell-skript parametrarna *AutomationAccountName*, *SubscriptionId*respektive *ResourceGroupName* .

   ![Egenskaps sida för Automation-konto](media/manage-runas-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>PowerShell-skript för att skapa ett Kör som-konto

Det här avsnittet innehåller ett PowerShell-skript för att skapa ett Kör som-konto. Skriptet innehåller stöd för flera konfigurationer.

* Skapa ett Kör som-konto med hjälp av ett självsignerat certifikat.
* Skapa ett Kör som-konto och ett klassiska Kör som-konto med hjälp av ett självsignerat certifikat.
* Skapa ett Kör som-konto och ett klassiskt Kör som-konto genom att använda ett certifikat utfärdat av en företagscertifikatutfärdare (CA).
* Skapa ett Kör som-konto och ett klassiskt Kör som-konto med hjälp av ett självsignerat certifikat i Azure Government-molnet.

Skriptet använder flera Azure Resource Manager-cmdletar för att skapa resurser. För-cmdletar och de behörigheter som de kräver, se [behörigheter för att konfigurera kör som-konton](#permissions-to-configure-run-as-accounts).

Spara skriptet på datorn med fil namnet **New-RunAsAccount. ps1**.

```powershell
    #Requires -RunAsAdministrator
    Param (
        [Parameter(Mandatory = $true)]
        [String] $ResourceGroup,

        [Parameter(Mandatory = $true)]
        [String] $AutomationAccountName,

        [Parameter(Mandatory = $true)]
        [String] $ApplicationDisplayName,

        [Parameter(Mandatory = $true)]
        [String] $SubscriptionId,

        [Parameter(Mandatory = $true)]
        [Boolean] $CreateClassicRunAsAccount,

        [Parameter(Mandatory = $true)]
        [String] $SelfSignedCertPlainPassword,

        [Parameter(Mandatory = $false)]
        [string] $EnterpriseCertPathForRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPlainPasswordForRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPathForClassicRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

        [Parameter(Mandatory = $false)]
        [ValidateSet("AzureCloud", "AzureUSGovernment")]
        [string]$EnvironmentName = "AzureCloud",

        [Parameter(Mandatory = $false)]
        [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
    )

    function CreateSelfSignedCertificate([string] $certificateName, [string] $selfSignedCertPlainPassword,
        [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
        $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
            -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
            -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired) -HashAlgorithm SHA256

        $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
    }

    function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {
        $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
        $keyId = (New-Guid).Guid

        # Create an Azure AD application, AD App Credential, AD ServicePrincipal

        # Requires Application Developer Role, but works with Application administrator or GLOBAL ADMIN
        $Application = New-AzADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId)
        # Requires Application administrator or GLOBAL ADMIN
        $ApplicationCredential = New-AzADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $PfxCert.NotBefore -EndDate $PfxCert.NotAfter
        # Requires Application administrator or GLOBAL ADMIN
        $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $Application.ApplicationId
        $GetServicePrincipal = Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id

        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        # Requires User Access Administrator or Owner.
        $NewRole = New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6) {
            Sleep -s 10
            New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
            $NewRole = Get-AzRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
            $Retries++;
        }
        return $Application.ApplicationId.ToString();
    }

    function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName, [string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force
        Remove-AzAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
    }

    function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
    }

    Import-Module AzureRm.Profile
    Import-Module AzureRm.Resources

    $AureRmProfileVersion = (Get-Module AzureRm.Profile).Version
    if (!(($AzureRmProfileVersion.Major -ge 3 -and $AzureRmProfileVersion.Minor -ge 4) -or ($AzureRmProfileVersion.Major -gt 3))) {
        Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
        return
    }

    # To use the new Az modules to create your Run As accounts, please uncomment the following lines and ensure you comment out the previous 8 lines that import the AzureRM modules to avoid any issues. To learn about about using Az modules in your Automation account see https://docs.microsoft.com/azure/automation/az-modules.

    # Import-Module Az.Automation
    # Enable-AzureRmAlias


    Connect-AzAccount -Environment $EnvironmentName
    $Subscription = Get-AzSubscription -SubscriptionId $SubscriptionId

    # Create a Run As account by using a service principal
    $CertifcateAssetName = "AzureRunAsCertificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    $ConnectionTypeName = "AzureServicePrincipal"

    if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
        $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
        $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
    }
    else {
        $CertificateName = $AutomationAccountName + $CertifcateAssetName
        $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
        $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
        $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
        CreateSelfSignedCertificate $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
    }

    # Create a service principal
    $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
    $ApplicationId = CreateServicePrincipal $PfxCert $ApplicationDisplayName

    # Create the Automation certificate asset
    CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

    # Populate the ConnectionFieldValues
    $SubscriptionInfo = Get-AzSubscription -SubscriptionId $SubscriptionId
    $TenantID = $SubscriptionInfo | Select TenantId -First 1
    $Thumbprint = $PfxCert.Thumbprint
    $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}

    # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

    if ($CreateClassicRunAsAccount) {
        # Create a Run As account by using a service principal
        $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
        $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
        $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
        $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
        "Log in to the Microsoft Azure portal (https://portal.azure.com) and select Subscriptions -> Management Certificates." + [Environment]::NewLine +
        "Then click Upload and upload the .cer format of #CERT#"

        if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
            $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
            $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
            $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
        }
        else {
            $ClassicRunAsAccountCertificateName = $AutomationAccountName + $ClassicRunAsAccountCertifcateAssetName
            $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
            $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
            $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
            $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
            CreateSelfSignedCertificate $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

        # Populate the ConnectionFieldValues
        $SubscriptionName = $subscription.Subscription.Name
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName   $ClassicRunAsAccountConnectionFieldValues

        Write-Host -ForegroundColor red       $UploadMessage
    }
```

>[!NOTE]
>**Add-AzAccount** och **Add-AzureRMAccount** är alias för [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Du kan använda dessa cmdletar, eller så kan du [Uppdatera dina moduler](automation-update-azure-modules.md) i ditt Automation-konto till de senaste versionerna. Du kan behöva uppdatera dina moduler även om du precis har skapat ett nytt Automation-konto.

### <a name="execute-the-powershell-script"></a>Kör PowerShell-skriptet

1. Starta **Windows PowerShell** från **startskärmen** med utökade användarrättigheter.
1. Från det upphöjda kommandoradsgränssnittet går du till mappen som innehåller det skript som du skapade i steg 1.
1. Kör skriptet genom att använda de parametervärden för konfigurationen som du behöver.
1. Om du skapar ett klassiskt kör som-konto när skriptet har körts, laddar du upp det offentliga certifikatet (fil namns tillägget. cer) till hanterings arkivet för den prenumeration som Automation-kontot har skapats i.

När skriptet har körts uppmanas du att autentisera med Azure. Logga in med ett konto som är medlem i rollen för prenumerationsadministratörer och som är medadministratör för prenumerationen.

#### <a name="create-a-run-as-account-by-using-a-self-signed-certificate"></a>Skapa ett Kör som-konto med hjälp av ett självsignerat certifikat

```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate"></a>Skapa ett Kör som-konto och ett klassiskt kör som-konto med hjälp av ett självsignerat certifikat

```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-an-enterprise-certificate"></a>Skapa ett Kör som-konto och ett klassiskt kör som-konto med hjälp av ett företags certifikat

```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
```

Om du har skapat ett klassiskt kör som-konto med ett offentligt företags certifikat (CER-fil) använder du det här certifikatet. Följ anvisningarna för [att ladda upp ett hanterings-API-certifikat till Azure Portal](../azure-api-management-certs.md).

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate-in-the-azure-government-cloud"></a>Skapa ett Kör som-konto och ett klassiskt kör som-konto med hjälp av ett självsignerat certifikat i Azure Government molnet

```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
```

Om du har skapat ett klassiskt kör som-konto med ett självsignerat offentligt certifikat (CER-fil) skapar skriptet och sparar det i mappen för temporära filer på datorn. Den finns i användar profilen **%USERPROFILE%\AppData\Local\Temp**, som du använde för att köra PowerShell-sessionen.

## <a name="deleting-a-run-as-or-classic-run-as-account"></a>Ta bort ett Kör som-konto eller ett klassiskt kör som-konto

I det här avsnittet beskrivs hur du tar bort ett Kör som-konto eller ett klassiskt kör som-konto. När du utför den här åtgärden behålls Automation-kontot. När du har tagit bort kontot kan du återskapa det i Azure Portal.

1. Öppna ditt Automation-konto på Azure Portal.

2. I det vänstra fönstret väljer du **Kör som-konton** i avsnittet konto inställningar.

3. På sidan Egenskaper för kör som-konton väljer du antingen kör som-kontot eller det klassiska kör som-kontot som du vill ta bort. 

4. I rutan Egenskaper för det valda kontot klickar du på **ta bort**.

   ![Ta bort Kör som-konto](media/manage-runas-account/automation-account-delete-runas.png)

5. Medan kontot tas bort kan du följa förloppet under **Meddelanden** på menyn.

6. När kontot har tagits bort kan du återskapa det på egenskaps sidan för kör som-konton genom att välja alternativet för att skapa **Azure kör som-konto**.

   ![Återskapa Kör som-kontot för Automation](media/manage-runas-account/automation-account-create-runas.png)

## <a name="cert-renewal"></a>Förnya ett självsignerat certifikat

Det självsignerade certifikatet som du har skapat för kör som-kontot går ut ett år från datumet då det skapades. Innan ditt kör som-konto går ut måste du förnya certifikatet. Du kan förnya det när som helst innan det upphör att gälla. 

När du förnyar det självsignerade certifikatet behålls det aktuella giltiga certifikatet för att säkerställa att alla Runbooks som köas eller aktivt körs, och som autentiserar med kör som-kontot, inte påverkas negativt. Certifikatet förblir giltigt fram till dess förfallodatum.

>[!NOTE]
>Om du tror att kör som-kontot har komprometterats kan du ta bort och återskapa det självsignerade certifikatet.

>[!NOTE]
>Om du har konfigurerat ditt kör som-konto för att använda ett certifikat som utfärdats av din företags certifikat utfärdare och du använder alternativet för att förnya ett självsignerat certifikat, ersätts företags certifikatet av ett självsignerat certifikat.

Använd följande steg för att förnya det självsignerade certifikatet.

1. Öppna ditt Automation-konto på Azure Portal.

1. Välj **Kör som-konton** i avsnittet konto inställningar.

    ![Egenskapsrutan för Automation-konto](media/manage-runas-account/automation-account-properties-pane.png)

1. På sidan Egenskaper för kör som-konton väljer du antingen kör som-kontot eller det klassiska kör som-kontot som du vill förnya certifikatet för.

1. I rutan Egenskaper för det valda kontot klickar du på **Förnya certifikat**.

    ![Förnya certifikat för Kör som-konto](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Medan certifikatet förnyas kan du följa förloppet under **Meddelanden** på menyn.

## <a name="auto-cert-renewal"></a>Konfigurera automatisk certifikat förnyelse med en Automation-Runbook

Om du vill förnya certifikat automatiskt kan du använda en Automation-Runbook. Det här skriptet på [GitHub](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1) aktiverar den här funktionen i ditt Automation-konto.

>[!NOTE]
>Du måste vara global administratör eller företags administratör i Azure AD för att kunna köra skriptet.

Det här skriptet skapar ett vecko schema för att förnya kör som-konto certifikat. Den lägger till en **Update-AutomationRunAsCredential** Runbook till ditt Automation-konto. Du kan visa Runbook-koden på GitHub i skriptet [Update-AutomationRunAsCredential. ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1). Du kan använda PowerShell-koden i filen om du vill förnya certifikat manuellt om det behövs.

Använd följande steg för att testa förnyelse processen direkt.

1. Redigera **AutomationRunAsCredential-** Runbook och Lägg till ett kommentars text (#) på rad 122, framför kommandot **exit (1)** .

   ```powershell
   #Exit(1)
   ```

2. Publicera runbooken.
3. Starta runbooken.
4. Verifiera lyckad förnyelse med följande kod:

   ```powershell
   (Get-AzAutomationCertificate -AutomationAccountName TestAA
                                -Name AzureRunAsCertificate
                                -ResourceGroupName TestAutomation).ExpiryTime.DateTime
   ```
    Resultat:

   ```Output
   Thursday, November 7, 2019 7:00:00 PM
   ```

5. Efter testet redigerar du runbooken och tar bort kommentars tecknen som du lade till i steg 1.
6. Publicera runbooken.

## <a name="limiting-run-as-account-permissions"></a>Begränsa behörigheter för kör som-konto

Du kan styra automatiseringen av automatisering mot resurser i Azure genom att köra skriptet [Update-AutomationRunAsAccountRoleAssignments. ps1](https://aka.ms/AA5hug8) . Det här skriptet ändrar ditt befintliga huvud namn för kör som-kontot för att skapa och använda en anpassad roll definition. Rollen har behörigheter för alla resurser utom [Key Vault](https://docs.microsoft.com/azure/key-vault/).

>[!IMPORTANT]
>När du har kört skriptet **Update-AutomationRunAsAccountRoleAssignments. ps1** fungerar Runbooks som har åtkomst Key Vault genom användningen av kör som-konton inte längre. Innan du kör skriptet bör du granska Runbooks i ditt konto för anrop till Azure Key Vault. Om du vill ge åtkomst till Key Vault från Azure Automation runbooks måste du [lägga till kör som-kontot i Key Vault behörigheter](#add-permissions-to-key-vault).

Om du behöver begränsa ytterligare vad huvud namnet för kör som-tjänsten kan göra kan du lägga till andra resurs typer i **NotActions** -elementet i den anpassade roll definitionen. I följande exempel begränsas åtkomsten till `Microsoft.Compute/*`. Om du lägger till den här resurs typen i **NotActions** för roll definitionen kommer rollen inte att kunna komma åt någon beräknings resurs. Mer information om roll definitioner finns i [förstå roll definitioner för Azure-resurser](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Du kan kontrol lera om tjänstens huvud namn som används av ditt kör som-konto finns i roll definitionen medarbetare eller en anpassad. Gör så här:

1. Gå till ditt Automation-konto och välj **Kör som-konton** i avsnittet konto inställningar.
2. Välj **Kör som-konto i Azure**. 
3. Välj **roll** för att hitta den roll definition som används.

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

Du kan också bestämma vilken roll definition som används av kör som-kontona för flera prenumerationer eller Automation-konton. Det gör du med hjälp av skriptet [Check-AutomationRunAsAccountRoleAssignments. ps1](https://aka.ms/AA5hug5) i PowerShell-galleriet.

### <a name="add-permissions-to-key-vault"></a>Lägg till behörigheter i Key Vault

Du kan låta Azure Automation verifiera om Key Vault och ditt kör som-konto-tjänstens huvud namn använder en anpassad roll definition. För att göra detta måste du:

* Bevilja behörighet till Key Vault.
* Ange åtkomst principen.

Du kan använda skriptet [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault. ps1](https://aka.ms/AA5hugb) i PowerShell-galleriet för att ge ditt kör som-konto behörighet att Key Vault. Mer information om hur du anger behörigheter för Key Vault finns i [bevilja program åtkomst till ett nyckel valv](../key-vault/key-vault-group-permissions-for-apps.md) .

## <a name="resolving-misconfiguration-issues-for-run-as-accounts"></a>Lösa fel konfigurations problem för kör som-konton

Vissa konfigurations objekt som krävs för ett Kör som-eller klassiskt kör som-konto kan ha tagits bort eller skapats felaktigt under den första installationen. Några förekomster av felaktig konfiguration är:

* Certifikattillgång
* Anslutningstillgång
* Kör som-konto har tagits bort från deltagar rollen
* Huvudnamn för tjänsten eller program i Azure AD

För sådana felkonfigurations instanser identifierar Automation-kontot ändringarna och visar statusen **ofullständig** i fönstret Egenskaper för kör som-konton för kontot.

![Konfigurationsstatusen Ofullständig för Kör som-konto](media/manage-runas-account/automation-account-runas-incomplete-config.png)

När du väljer Kör som-kontot visas följande fel meddelande i fönstret konto egenskaper:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Du kan snabbt lösa dessa problem med Kör som-kontot genom att ta bort och återskapa kontot.

## <a name="next-steps"></a>Nästa steg

* Mer information om tjänstens huvud namn finns i [program objekt och tjänst huvud objekt](../active-directory/develop/app-objects-and-service-principals.md).
* Mer information om certifikat och Azure-tjänster finns i [Översikt över certifikat för Azure Cloud Services](../cloud-services/cloud-services-certs-create.md).
