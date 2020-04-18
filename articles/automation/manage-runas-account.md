---
title: Hantera Azure Automation Run As-konton
description: I den här artikeln beskrivs hur du hanterar dina Kör som-konton med PowerShell eller från portalen.
services: automation
ms.subservice: shared-capabilities
ms.date: 05/24/2019
ms.topic: conceptual
ms.openlocfilehash: 341db4ffa5b2e0641572f2c9dc011e91fac6a1e9
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617315"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Hantera Azure Automation Run As-konton

Kör som konton i Azure Automation tillhandahåller autentisering för hantering av resurser i Azure med hjälp av Azure-cmdlets. När du skapar ett Run As-konto skapas en ny tjänsthuvudanvändare i Azure Active Directory (AD) och tilldelas rollen Deltagare till den här användaren på prenumerationsnivå. För runbooks som använder Hybrid Runbook Workers på virtuella Azure-datorer kan du använda [hanterade identiteter för Azure-resurser](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) i stället för Run As-konton för att autentisera till dina Azure-resurser.

Tjänsthuvudhuvudet för ett Run as-konto har inte behörighet att läsa Azure AD som standard. Om du vill lägga till behörigheter för att läsa eller hantera Azure AD måste du bevilja behörigheterna för tjänstens huvudnamn under **API-behörigheter**. Mer information finns i [Lägga till behörigheter för åtkomst till webb-API:er](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="types-of-run-as-accounts"></a>Typer av Kör som konton

Azure Automation använder två typer av Run As-konton:

* Kör som-konto i Azure
* Azure Classic kör som konto

>[!NOTE]
>CSP-prenumerationer (Azure Cloud Solution Provider) stöder endast Azure Resource Manager-modellen. Icke-Azure Resource Manager-tjänster är inte tillgängliga i programmet. När du använder en CSP-prenumeration skapas inte Azure Classic Run As-kontot, men Azure Run As-kontot skapas. Mer information om CSP-prenumerationer finns [i Tillgängliga tjänster i CSP-prenumerationer](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).

### <a name="run-as-account"></a>Kör som-konto

Kontot Kör som hanterar [Resurser för Resurshanterarens distributionsmodell.](../azure-resource-manager/management/deployment-models.md) Det gör följande uppgifter.

* Skapar ett Azure AD-program med ett självsignerat certifikat och ett tjänstobjektskonto för programmet i Azure AD och rollen Deltagare tilldelas för kontot i din aktuella prenumeration. Du kan ändra certifikatinställningen till Ägare eller någon annan roll. Mer information finns i [Rollbaserad åtkomstkontroll i Azure Automation](automation-role-based-access-control.md).
  
* Skapar en automationscertifikattillgång som namnges `AzureRunAsCertificate` i det angivna Automation-kontot. Certifikattillgången innehåller den privata certifikatnyckeln som Azure AD-programmet använder.
  
* Skapar en automationsanslutningstillgång som namnges `AzureRunAsConnection` i det angivna Automation-kontot. Anslutningstillgången innehåller program-ID, klient-ID, prenumerations-ID och certifikattumavtryck.

### <a name="azure-classic-run-as-account"></a>Klassiskt Kör som-konto i Azure

Azure Classic Run As-kontot hanterar [klassiska distributionsmodellresurser.](../azure-resource-manager/management/deployment-models.md) Du måste vara medadministratör på prenumerationen för att kunna skapa eller förnya den här typen av konto.

Azure Classic Run As-kontot utför följande uppgifter.

  * Skapar ett hanteringscertifikat i prenumerationen.

  * Skapar en automationscertifikattillgång som namnges `AzureClassicRunAsCertificate` i det angivna Automation-kontot. Certifikattillgången innehåller den privata nyckelns certifikat som används av hanteringscertifikatet.

  * Skapar en automationsanslutningstillgång som namnges `AzureClassicRunAsConnection` i det angivna Automation-kontot. Anslutningstillgången innehåller prenumerationsnamnet, prenumerations-ID: n och certifikattillgångsnamnet.

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Kör som kontobehörigheter

Det här avsnittet definierar behörigheter för både vanliga Kör som-konton och Klassiska körning som konton.

### <a name="permissions-to-configure-run-as-accounts"></a>Behörigheter för att konfigurera Kör som konton

Om du vill skapa eller uppdatera ett Run As-konto måste du ha specifika behörigheter och behörigheter. En programadministratör i Azure Active Directory och en ägare i en prenumeration kan slutföra alla uppgifter. I en situation där du har åtskillnad av arbetsuppgifter, visar följande tabell en lista över de uppgifter, motsvarande cmdlet, och behörigheter som behövs:

|Aktivitet|Cmdlet  |Minsta behörigheter  |Var du anger behörigheterna|
|---|---------|---------|---|
|Skapa Azure AD-program|[Ny-AzADApplication](https://docs.microsoft.com/powershell/module/az.resources/new-azadapplication?view=azps-3.5.0)     | Roll för programutvecklare<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Registreringar > Azure AD > App |
|Lägg till en autentiseringsdag i programmet.|[Ny-AzADAppCredential](https://docs.microsoft.com/powershell/module/az.resources/new-azadappcredential?view=azps-3.5.0)     | Programadministratör eller Global administratör<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Registreringar > Azure AD > App|
|Skapa och hämta ett huvudnamn för Azure AD-tjänsten|[New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-3.5.0)</br>[Hämta-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal?view=azps-3.5.0)     | Programadministratör eller Global administratör<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Registreringar > Azure AD > App|
|Tilldela eller hämta RBAC-rollen för det angivna huvudbeloppet|[New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment?view=azps-3.5.0)</br>[Get-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/Get-AzRoleAssignment?view=azps-3.5.0)      | Administratör eller ägare för användaråtkomst eller har följande behörigheter:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Prenumeration](../role-based-access-control/role-assignments-portal.md)</br>Prenumerationer på > \<\> > prenumerationer - Åtkomstkontroll (IAM)|
|Skapa eller ta bort ett Automation-certifikat|[Ny-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.5.0)</br>[Ta bort-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationcertificate?view=azps-3.5.0)     | Deltagare i resursgruppen         |Resursgrupp för automationskonto|
|Skapa eller ta bort en Automation-anslutning|[Ny-AzAutomationAnslutning](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.5.0)</br>[Ta bort-AzAutomationAnslutning](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationconnection?view=azps-3.5.0)|Deltagare i resursgruppen |Resursgrupp för automationskonto|

<sup>1</sup> Användare som inte är administratörer i din Azure AD-klient kan [registrera AD-program](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) om Azure AD-klientens **användare kan registrera programalternativet** på sidan Användarinställningar är inställt på **Ja**. Om programregistreringsinställningen är **Nej**måste användaren som utför den här åtgärden vara den som definieras i den här tabellen.

Om du inte är medlem i prenumerationens Active Directory-instans innan du läggs till i rollen Global administratör för prenumerationen läggs du till som gäst. I det här fallet `You do not have permissions to create…` får du en varning på sidan Lägg till automationskonto. 

Om du är medlem i prenumerationens Active Directory-instans när rollen Global administratör `You do not have permissions to create…` har tilldelats kan du också få en varning på sidan Lägg till automationskonto. I det här fallet kan du begära borttagning från prenumerationens Active Directory-instans och sedan begära att läggas till igen, så att du blir full användare i Active Directory.

Så här kontrollerar du att situationen som ger upphov till felmeddelandet har åtgärdats:

1. Välj **Användare och grupper**i Fönstret Azure Active Directory i Azure-portalen . 
2. Välj **Alla användare**.
3. Välj ditt namn och välj sedan **Profil**. 
4. Kontrollera att värdet för attributet **Användartyp** under användarens profil inte är inställt på **Gäst**.

### <a name="permissions-to-configure-classic-run-as-accounts"></a><a name="permissions-classic"></a>Behörigheter för att konfigurera klassiska körning som konton

Om du vill konfigurera eller förnya Klassiska Körning som konton måste du ha rollen medadministratör på prenumerationsnivå. Mer information om klassiska prenumerationsbehörigheter finns i [Azure classic subscription administrators](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="creating-a-run-as-account-in-azure-portal"></a>Skapa ett Run As-konto i Azure-portalen

Utför följande steg för att uppdatera ditt Azure Automation-konto i Azure-portalen. Skapa kontona Kör som och Klassisk körning som individuellt. Om du inte behöver hantera klassiska resurser kan du bara skapa Azure Kör som-kontot.

1. Logga in på Azure-portalen med ett konto som är medlem i rollen Prenumerationsadministratörer och som är medadministratör för prenumerationen.
2. Sök efter och välj **Automation-konton**.
3. På sidan Automation-konton väljer du ditt Automation-konto i listan.
4. Välj Kör som **konton** i avsnittet Kontoinställningar i den vänstra rutan.
5. Beroende på vilket konto du behöver väljer du antingen **Azures Kör som-konto** eller **Azures klassiska Kör som-konto**. 
6. Beroende på kontot av intresse använder du fönstret **Lägg till Azure Run As** eller Lägg till Azure Classic Run As **Account.** När du har granskat översiktsinformationen klickar du på **Skapa**.
6. Medan Azure skapar Kör-som-kontot kan du följa förloppet under **Meddelanden** på menyn. En banderoll visas också som anger att kontot skapas. Processen kan ta några minuter att slutföra.

## <a name="creating-a-run-as-account-using-powershell"></a>Skapa ett Run As-konto med PowerShell

Följande lista innehåller kraven för att skapa ett Run As-konto i PowerShell. Dessa krav gäller för båda typerna av Kör som konton.

* Windows 10 eller Windows Server 2016 med Azure Resource Manager-moduler 3.4.1 och senare. PowerShell-skriptet stöder inte tidigare versioner av Windows.
* Azure PowerShell 1.0 och senare. Information om PowerShell 1.0-versionen finns i [Installera och konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Ett Automation-konto, som refereras som `AutomationAccountName` `ApplicationDisplayName` värdet för och parametrar.
* Behörigheter som motsvarar de behörigheter som anges i [Obligatoriska behörigheter för att konfigurera Kör som konton](#permissions).

Om du vill `SubscriptionId` `ResourceGroupName`hämta värdena för , , och , som krävs parametrar för PowerShell-skriptet, slutför du nästa steg.

1. I Azure-portalen väljer du **Automation-konton**.
1. På sidan Automation-konton väljer du ditt Automation-konto.
1. Välj **Egenskaper**i avsnittet Kontoinställningar .
1. Observera värdena för **NAME,** **PRENUMERATIONS-ID**och **RESURSGRUPP** på sidan Egenskaper. Dessa värden motsvarar värdena `AutomationAccountName` `SubscriptionId`för `ResourceGroupName` skriptparametrarna , och PowerShell.

   ![Sidan Egenskaper för automatiseringskonto](media/manage-runas-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>PowerShell-skript för att skapa ett Run As-konto

Det här avsnittet innehåller ett PowerShell-skript för att skapa ett Run As-konto. Skriptet innehåller stöd för flera konfigurationer.

* Skapa ett Kör som-konto med hjälp av ett självsignerat certifikat.
* Skapa ett Kör som-konto och ett klassiska Kör som-konto med hjälp av ett självsignerat certifikat.
* Skapa ett Kör som-konto och ett klassiskt Kör som-konto genom att använda ett certifikat utfärdat av en företagscertifikatutfärdare (CA).
* Skapa ett Kör som-konto och ett klassiskt Kör som-konto med hjälp av ett självsignerat certifikat i Azure Government-molnet.

Skriptet använder flera Azure Resource Manager-cmdlets för att skapa resurser. För cmdlets och de behörigheter de behöver finns [i Behörigheter för att konfigurera Kör som konton](#permissions-to-configure-run-as-accounts).

Spara skriptet på datorn med filnamnet **New-RunAsAccount.ps1**.

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
>`Add-AzAccount`och `Add-AzureRMAccount` är alias för [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Du kan använda dessa cmdlets eller så kan du [uppdatera dina moduler](automation-update-azure-modules.md) i ditt Automation-konto till de senaste versionerna. Du kan behöva uppdatera dina moduler även om du just har skapat ett nytt Automation-konto.

### <a name="execute-the-powershell-script"></a>Köra PowerShell-skriptet

1. Starta **Windows PowerShell** från **startskärmen** med utökade användarrättigheter.
1. Gå till mappen som innehåller skriptet från det upphöjda kommandoradsskalet.
1. Kör skriptet med hjälp av parametervärdena för den konfiguration som du behöver.
1. Om du skapar ett Classic Run As-konto, när skriptet har körts, laddar du upp det offentliga certifikatet ( .cer-filnamnstillägget) till hanteringsarkivet för prenumerationen där Automation-kontot har skapats.**.cer**

När skriptet har körts uppmanas du att autentisera med Azure. Logga in med ett konto som är medlem i rollen prenumerationsadministratörer och medadministratör för prenumerationen.

#### <a name="create-a-run-as-account-by-using-a-self-signed-certificate"></a>Skapa ett Kör som-konto med hjälp av ett självsignerat certifikat

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate"></a>Skapa ett Kör som-konto och ett klassiska Kör som-konto med hjälp av ett självsignerat certifikat

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-an-enterprise-certificate"></a>Skapa ett Kör som-konto och ett klassiskt Kör som-konto med hjälp av ett företagscertifikat

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
```

Om du har skapat ett Klassiskt kör som-konto med ett offentligt företagscertifikat (**.cer-fil)** använder du det här certifikatet. Se [Ladda upp ett API-certifikat för hantering till Azure-portalen](../azure-api-management-certs.md).

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate-in-the-azure-government-cloud"></a>Skapa ett Run As-konto och ett Classic Run As-konto med hjälp av ett självsignerat certifikat i Azure Government-molnet

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
```

Om du har skapat ett Klassiskt kör som-konto med ett självsignerat offentligt certifikat (**.cer-fil)** skapar och sparar skriptet i mappen med temporära filer på datorn. Den finns i användarprofilen `%USERPROFILE%\AppData\Local\Temp`, som du använde för att köra PowerShell-sessionen.

## <a name="deleting-a-run-as-or-classic-run-as-account"></a>Ta bort ett Run As- eller Classic Run As-konto

I det här avsnittet beskrivs hur du tar bort ett Kör som- eller klassiskt kör som-konto. När du utför den här åtgärden behålls Automation-kontot. När du har tagit bort kontot kan du återskapa det i Azure-portalen.

1. Öppna ditt Automation-konto på Azure Portal.

2. Välj Kör som **konton** i avsnittet Kontoinställningar i den vänstra rutan.

3. På egenskapssidan för Kör som-konton väljer du antingen Kör som-kontot eller det klassiska Kör som-kontot som du vill ta bort. 

4. Klicka på **Ta bort**i fönstret Egenskaper för det markerade kontot .

   ![Ta bort Kör som-konto](media/manage-runas-account/automation-account-delete-runas.png)

5. Medan kontot tas bort kan du följa förloppet under **Meddelanden** på menyn.

6. När kontot har tagits bort måste du återskapa det på egenskapssidan för Kör som-konton genom att välja alternativet Skapa för **Kör som-konto i Azure**.

   ![Återskapa Kör som-kontot för Automation](media/manage-runas-account/automation-account-create-runas.png)

## <a name="renewing-a-self-signed-certificate"></a><a name="cert-renewal"></a>Förnya ett självsignerat certifikat

Det självsignerade certifikat som du har skapat för kontot Kör som upphör att gälla ett år från det datum då det skapades. Någon gång innan run as-kontot går ut måste du förnya certifikatet. Du kan förnya den när som helst innan den går ut. 

När du förnyar det självsignerade certifikatet behålls det aktuella giltiga certifikatet för att säkerställa att alla runbooks som körs i kö och som autentiseras med kontot Kör som påverkas inte negativt. Certifikatet förblir giltigt fram till dess förfallodatum.

>[!NOTE]
>Om du tror att kör som-kontot har komprometterats kan du ta bort och återskapa det självsignerade certifikatet.

>[!NOTE]
>Om du har konfigurerat ditt Run As-konto för att använda ett certifikat som utfärdats av företagets certifikatutfärdare och du använder alternativet för att förnya ett självsignerat certifikatalternativ ersätts företagscertifikatet med ett självsignerat certifikat.

Följ följande steg för att förnya det självsignerade certifikatet.

1. Öppna ditt Automation-konto på Azure Portal.

1. Välj **Kör som konton** i avsnittet kontoinställningar.

    ![Egenskapsrutan för Automation-konto](media/manage-runas-account/automation-account-properties-pane.png)

1. På sidan Kör som konton väljer du antingen kontot Kör som eller det klassiska kör som-kontot för att förnya certifikatet för.

1. Klicka på **Förnya certifikat**i egenskapsfönstret för det markerade kontot .

    ![Förnya certifikat för Kör som-konto](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Medan certifikatet förnyas kan du följa förloppet under **Meddelanden** på menyn.

## <a name="setting-up-automatic-certificate-renewal-with-an-automation-runbook"></a><a name="auto-cert-renewal"></a>Ställa in automatisk förnyelse av certifikat med en automatiseringskörningsbok

Om du vill förnya certifikat automatiskt kan du använda en automatiseringskörningsbok. Det här skriptet på [GitHub](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1) aktiverar den här funktionen i ditt Automation-konto.

>[!NOTE]
>Du måste vara global administratör eller företagsadministratör i Azure AD för att kunna köra skriptet.

Det här skriptet skapar ett veckoschema för att förnya Run As-kontocertifikat. Den lägger till en **Update-AutomationRunAsCredential** runbook till ditt Automation-konto. Du kan visa runbook-koden på GitHub i skriptet [Update-AutomationRunAsCredential.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1). Du kan använda PowerShell-koden i filen för att förnya certifikat manuellt efter behov.

Gör så här för att testa förnyelseprocessen omedelbart.

1. Redigera **update-automationRunAsCredential** runbook och placera ett kommentartecken (#) på rad 122, framför kommandot **Avsluta(1).**

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

5. Efter testet redigerar du runbooken och tar bort kommentarstecknet som du lade till i steg 1.
6. Publicera runbooken.

## <a name="limiting-run-as-account-permissions"></a><a name="limiting-run-as-account-permissions"></a>Begränsa behörigheter för körning som konto

Om du vill styra inriktningen av Automatisering mot resurser i Azure kan du köra skriptet [Update-AutomationRunAsAccountRoleAssignments.ps1.](https://aka.ms/AA5hug8) Det här skriptet ändrar ditt befintliga huvudnamn för körningstjänsten för att skapa och använda en anpassad rolldefinition. Rollen har behörigheter för alla resurser utom [Key Vault](https://docs.microsoft.com/azure/key-vault/).

>[!IMPORTANT]
>När du har kört skriptet **Update-AutomationRunAsAccountRoleAssignments.ps1** fungerar runbooks som använder Key Vault med hjälp av Kör som konton inte längre. Innan du kör skriptet bör du granska runbooks i ditt konto för samtal till Azure Key Vault. Om du vill aktivera åtkomst till Key Vault från Azure Automation-runbooks måste du [lägga till kontot Kör som i Key Vaults behörigheter](#add-permissions-to-key-vault).

Om du behöver begränsa, ytterligare vad tjänsten Kör som-tjänstens huvudnamn `NotActions` kan göra, kan du lägga till andra resurstyper i elementet i den anpassade rolldefinitionen. I följande exempel begränsas `Microsoft.Compute/*`åtkomsten till . Om du lägger `NotActions` till den här resurstypen för rolldefinitionen kan rollen inte komma åt någon beräkningsresurs. Mer information om rolldefinitioner finns i [Förstå rolldefinitioner för Azure-resurser](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Du kan avgöra om tjänstens huvudnamn som används av ditt Run As-konto finns i rolldefinitionen deltagare eller ett anpassat. 

1. Gå till ditt Automation-konto och välj **Kör som konton** i avsnittet kontoinställningar.
2. Välj **Azure-körning som konto**. 
3. Välj **Roll** för att hitta den rolldefinition som används.

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

Du kan också bestämma vilken rolldefinition som används av kontona Kör som för flera prenumerationer eller Automation-konton. Gör detta med hjälp av [check-automationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) skriptet i PowerShell-galleriet.

### <a name="add-permissions-to-key-vault"></a>Lägga till behörigheter i Key Vault

Du kan tillåta att Azure Automation verifierar om Key Vault och huvudmannen för körningstjänsten använder en anpassad rolldefinition. Du måste:

* Bevilja behörigheter till Key Vault.
* Ange åtkomstprincipen.

Du kan använda skriptet [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) i PowerShell-galleriet för att ge dina behörigheter för Run As-kontot till Key Vault. Mer information om hur du anger behörigheter för Key Vault finns [i Bevilja programåtkomst till ett nyckelvalv.](../key-vault/general/group-permissions-for-apps.md)

## <a name="resolving-misconfiguration-issues-for-run-as-accounts"></a>Lösa problem med felkonfiguration för Kör som konton

Vissa konfigurationsobjekt som krävs för ett Run As- eller Classic Run As-konto kan ha tagits bort eller skapats felaktigt under den första installationen. Möjliga fall av felkonfiguration är:

* Certifikattillgång
* Anslutningstillgång
* Kör som konto som tagits bort från rollen Deltagare
* Huvudnamn för tjänsten eller program i Azure AD

För sådana felkonfigurationsinstanser identifierar Automation-kontot ändringarna och `Incomplete` visar statusen för i egenskapsfönstret Kör som konton för kontot.

![Konfigurationsstatusen Ofullständig för Kör som-konto](media/manage-runas-account/automation-account-runas-incomplete-config.png)

När du väljer kontot Kör som visas följande felmeddelande i fönstret Kontoegenskaper:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Du kan snabbt lösa dessa problem med Kör som-kontot genom att ta bort och återskapa kontot.

## <a name="next-steps"></a>Nästa steg

* Mer information om tjänstens huvudnamn finns i [Programobjekt och Huvudobjekt för tjänsten](../active-directory/develop/app-objects-and-service-principals.md).
* Mer information om certifikat och Azure-tjänster finns i [översikt över certifikat för Azure Cloud Services](../cloud-services/cloud-services-certs-create.md).
