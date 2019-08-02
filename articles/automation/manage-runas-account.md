---
title: Hantera Azure Automation kör som-konton
description: Den här artikeln beskriver hur du hanterar Kör som-konton med PowerShell eller från portalen.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: bobbytreed
ms.author: robreed
ms.date: 05/24/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 318a9c2df7902ae89a731ca45b24b8bb6241faa1
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498384"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Hantera Azure Automation kör som-konton

Kör som-konton i Azure Automation används för att tillhandahålla autentisering för att hantera resurser i Azure med Azure-cmdletar.

När du skapar ett Kör som-konto skapas en ny tjänst huvud användare i Azure Active Directory och deltagar rollen tilldelas den här användaren på prenumerations nivå. För Runbooks som använder hybrid Runbook Worker på Azure Virtual Machines kan du använda [hanterade identiteter för Azure-resurser](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) i stället för kör som-konton för att autentisera till dina Azure-resurser.

Det finns två typer av kör som-konton:

* **Kör som-konto i Azure** – det här kontot används för att hantera resurser för [distributions modeller i Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md) .
  * Skapar ett Azure AD-program med ett självsignerat certifikat och ett tjänstobjektskonto för programmet i Azure AD och rollen Deltagare tilldelas för kontot i din aktuella prenumeration. Du kan ändra den här inställningen till Ägare eller en annan roll. Mer information finns i [Rollbaserad åtkomstkontroll i Azure Automation](automation-role-based-access-control.md).
  * Skapar en Automation-certifikattillgång med namnet *AzureRunAsCertificate* i det angivna Automation-kontot. Certifikattillgången innehåller certifikatets privata nyckel som används av Azure AD-programmet.
  * Skapar en Automation-anslutningstillgång med namnet *AzureRunAsConnection* i det angivna Automation-kontot. Anslutningstillgången innehåller applicationId, tenantId, subscriptionId och certifikatets tumavtryck.

* Det **klassiska kör som-kontot i Azure** – det här kontot används för att hantera klassiska resurser för distributions [modeller](../azure-resource-manager/resource-manager-deployment-model.md) .
  * Skapar ett hanterings certifikat i prenumerationen
  * Skapar en Automation-certifikattillgång med namnet *AzureClassicRunAsCertificate* i det angivna Automation-kontot. Certifikattillgången innehåller den privata nyckelns certifikat som används av hanteringscertifikatet.
  * Skapar en Automation-anslutningstillgång med namnet *AzureClassicRunAsConnection* i det angivna Automation-kontot. Anslutningstillgången innehåller prenumerationsnamnet, subscriptionId och certifikattillgångens namn.
  * Måste vara en medadministratör för prenumerationen för att kunna skapa eller förnya

  > [!NOTE]
  > Azure-prenumerationer för moln lösningar (Azure CSP) stöder endast Azure Resource Manager-modellen, icke-Azure Resource Manager-tjänster är inte tillgängliga i programmet. När du använder en CSP-prenumeration skapas inte det klassiska kör som-kontot i Azure. Kör som-kontot i Azure skapas fortfarande. Mer information om CSP-prenumerationer finns i [tillgängliga tjänster i CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services#comments)-prenumerationer.

  > [!NOTE]
  > Tjänstens huvud namn för ett Kör som-konto har inte behörighet att läsa Azure Active Directory som standard. Om du vill lägga till behörigheter för att läsa eller hantera Azure Active Directory måste du bevilja den behörigheten för tjänstens huvud namn under **API-behörigheter**. Läs mer i [lägga till behörigheter för åtkomst till webb-API: er](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="permissions"></a>Behörigheter för att konfigurera kör som-konton

Om du vill skapa eller uppdatera ett Kör som-konto måste du ha vissa behörigheter och behörigheter. En global administratör i Azure Active Directory och en ägare i en prenumeration kan slutföra alla uppgifter. I en situation där du har separering av uppgifter visar följande tabell en lista över aktiviteterna, motsvarande cmdlet och behörigheter som krävs:

|Uppgift|Cmdlet:  |Lägsta behörighet  |Där du anger behörigheter|
|---|---------|---------|---|
|Skapa Azure AD-program|[New-AzureRmADApplication](/powershell/module/azurerm.resources/new-azurermadapplication)     | Programutvecklare, roll<sup>1</sup>        |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Start > Azure Active Directory registrering av >-appar |
|Lägg till en autentiseringsuppgift i programmet.|[New-AzureRmADAppCredential](/powershell/module/AzureRM.Resources/New-AzureRmADAppCredential)     | Program administratör eller GLOBAL administratör<sup>1</sup>         |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Start > Azure Active Directory registrering av >-appar|
|Skapa och hämta en Azure AD-tjänstens huvud namn|[New-AzureRMADServicePrincipal](/powershell/module/AzureRM.Resources/New-AzureRmADServicePrincipal)</br>[Get-AzureRmADServicePrincipal](/powershell/module/AzureRM.Resources/Get-AzureRmADServicePrincipal)     | Program administratör eller GLOBAL administratör<sup>1</sup>        |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Start > Azure Active Directory registrering av >-appar|
|Tilldela eller hämta RBAC-rollen för det angivna huvudobjektet|[New-AzureRMRoleAssignment](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment)</br>[Get-AzureRMRoleAssignment](/powershell/module/AzureRM.Resources/Get-AzureRmRoleAssignment)      | Du måste ha följande behörigheter:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br>Eller var:</br></br>Administratör för användar åtkomst eller ägare        | [Prenumeration](../role-based-access-control/role-assignments-portal.md)</br>Start > prenumerationer \<> prenumerations namn\> -Access Control (IAM)|
|Skapa eller ta bort ett Automation-certifikat|[New-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/New-AzureRmAutomationCertificate)</br>[Remove-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationCertificate)     | Deltagare i resurs gruppen         |Resurs grupp för Automation-konto|
|Skapa eller ta bort en Automation-anslutning|[New-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/New-AzureRmAutomationConnection)</br>[Remove-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationConnection)|Deltagare i resurs gruppen |Resurs grupp för Automation-konto|

<sup>1</sup> användare som inte är administratörer i din Azure AD-klient kan [registrera AD-program](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) om alternativet för Azure AD-klientens **användare kan registrera program** på sidan **användar inställningar** är inställt på **Ja**. Om inställningen för appens registrering är inställd på **Nej**, måste användaren som utför den här åtgärden vara vad som definieras i tabellen ovan.

Om du inte är medlem i prenumerationens Active Directory instans innan du lägger till den **globala administratörs** rollen för prenumerationen läggs du till som gäst. I så fall får du en `You do not have permissions to create…` varning på sidan **Lägg till Automation-konto** . Användare som har lagts till i rollen som **Global administratör** kan först tas bort från prenumerationens Active Directory instans och sedan läggas till igen så att de blir fullständiga användare i Active Directory. Du kan kontrollera detta i rutan **Azure Active Directory** på Azure Portal genom att välja **Användare och grupper**, välja **Alla användare**, välja den specifika användaren och sedan välja **Profil**. Värdet för attributet **Användartyp** under användarens profil bör inte vara lika med **Gäst**.

## <a name="permissions-classic"></a>Behörigheter för att konfigurera klassiska kör som-konton

Om du vill konfigurera eller förnya klassiska kör som **-** konton måste du ha rollen medadministratör på prenumerations nivå. Mer information om klassiska behörigheter finns i [Azures klassiska prenumerations administratörer](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="create-a-run-as-account-in-the-portal"></a>Skapa ett Kör som-konto i portalen

I det här avsnittet utför du följande steg för att uppdatera ditt Azure Automation-konto i Azure Portal. Du skapar kör som-konton och klassiska kör som-konton var för sig. Om du inte behöver hantera klassiska resurser kan du bara skapa Azure Kör som-kontot.

1. Logga in på Azure Portal med ett konto som är medlem i rollen Prenumerationsadministratörer och som är medadministratör för prenumerationen.
2. Klicka på **Alla tjänster** på Azure Portal. I listan över resurser skriver du **Automation**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Automation-konton**.
3. På sidan **Automation-konton** väljer du ditt Automation-konto i listan med Automation-konton.
4. I vänster fönster väljer du **Kör som-konton** under avsnittet **Kontoinställningar**.
5. Beroende på vilket konto du behöver väljer du antingen **Azures Kör som-konto** eller **Azures klassiska Kör som-konto**. När du har gjort ditt val visas antingen fönstret **Lägg till Azures Kör som-konto** eller **Lägg till Azures klassiska Kör som-konto**. Efter att du läst översiktsinformationen klickar du på **Skapa** för att fortsätta att skapa Kör som-kontot.
6. Medan Azure skapar Kör-som-kontot kan du följa förloppet under **Meddelanden** på menyn. En banderoll som meddelar att kontot skapats visas också. Den här processen kan ta ett par minuter att slutföra.

## <a name="create-run-as-account-using-powershell"></a>Skapa ett Kör som-konto med hjälp av PowerShell

## <a name="prerequisites"></a>Förutsättningar

Följande lista innehåller krav för att skapa ett Kör som-konto i PowerShell:

* Windows 10 eller Windows Server 2016 med Azure Resource Manager modulerna 3.4.1 och senare. PowerShell-skriptet stöds inte i tidigare versioner av Windows.
* Azure PowerShell 1.0 och senare. Information om PowerShell 1.0-versionen finns i [Installera och konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Ett Automation-konto, som refereras till som värdet för *-AutomationAccountName*- och *-ApplicationDisplayName*-parametrarna.
* Behörigheter som motsvarar vad som anges i de [behörigheter som krävs för att konfigurera kör som-konton](#permissions)

Utför följande steg för att hämta värdena för *SubscriptionID*, *ResourceGroup*och *AutomationAccountName*, som är obligatoriska parametrar för skriptet:

1. Klicka på **Alla tjänster** på Azure Portal. I listan över resurser skriver du **Automation**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Automation-konton**.
1. På sidan Automation-konto väljer du ditt Automation-konto och sedan under **Kontoinställningar** väljer du **Egenskaper**.
1. Anteckna värdena för **prenumerations-ID**, **namn**och **resurs grupp** på sidan **Egenskaper** .

   ![Sidan Egenskaper för Automation-konto](media/manage-runas-account/automation-account-properties.png)

Det här PowerShell-skriptet har stöd för följande konfigurationer:

* Skapa ett Kör som-konto med hjälp av ett självsignerat certifikat.
* Skapa ett Kör som-konto och ett klassiska Kör som-konto med hjälp av ett självsignerat certifikat.
* Skapa ett Kör som-konto och ett klassiskt Kör som-konto genom att använda ett certifikat utfärdat av en företagscertifikatutfärdare (CA).
* Skapa ett Kör som-konto och ett klassiskt Kör som-konto med hjälp av ett självsignerat certifikat i Azure Government-molnet.

>[!NOTE]
> Om du väljer något av alternativen för att skapa ett klassiskt Kör som-konto laddar du upp det offentliga certifikatet (filnamnstillägget .cer) när skriptet har körts till hanteringsarkivet för den prenumeration som Automation-kontot skapades i.

1. Spara följande skript på datorn. I det här exemplet sparar du det med filnamnet *New-RunAsAccount.ps1*.

   Skriptet använder flera Azure Resource Manager-cmdletar för att skapa resurser. I den [](#permissions) föregående behörighets tabellen visas de cmdlets och deras behörigheter som krävs.

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
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId)
        # Requires Application administrator or GLOBAL ADMIN
        $ApplicationCredential = New-AzureRmADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $PfxCert.NotBefore -EndDate $PfxCert.NotAfter
        # Requires Application administrator or GLOBAL ADMIN
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id

        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        # Requires User Access Administrator or Owner.
        $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6) {
            Sleep -s 10
            New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
            $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
            $Retries++;
        }
        return $Application.ApplicationId.ToString();
    }

    function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName, [string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force
        Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
    }

    function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
    }

    Import-Module AzureRM.Profile
    Import-Module AzureRM.Resources

    $AzureRMProfileVersion = (Get-Module AzureRM.Profile).Version
    if (!(($AzureRMProfileVersion.Major -ge 3 -and $AzureRMProfileVersion.Minor -ge 4) -or ($AzureRMProfileVersion.Major -gt 3))) {
        Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
        return
    }

    # To use the new Az modules to create your Run As accounts please uncomment the following lines and ensure you comment out the previous 8 lines that import the AzureRM modules to avoid any issues. To learn about about using Az modules in your Automation Account see https://docs.microsoft.com/azure/automation/az-modules

    # Import-Module Az.Automation
    # Enable-AzureRmAlias


    Connect-AzureRmAccount -Environment $EnvironmentName
    $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId

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
    $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
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

    > [!IMPORTANT]
    > **Add-AzureRmAccount** är nu ett alias för **Connect-AzureRmAccount**. Om du inte ser **Connect-AzureRMAccount**när du söker i biblioteks objekt kan du använda **Add-AzureRMAccount**, eller så kan du [Uppdatera dina moduler](automation-update-azure-modules.md) i ditt Automation-konto.

1. Starta **Windows PowerShell** från **startskärmen** med utökade användarrättigheter.
1. Från det upphöjda kommandoradsgränssnittet går du till mappen som innehåller det skript som du skapade i steg 1.
1. Kör skriptet genom att använda de parametervärden för konfigurationen som du behöver.

    **Skapa ett Kör som-konto med hjälp av ett självsignerat certifikat**

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
    ```

    **Skapa ett Kör som-konto och ett klassiska Kör som-konto med hjälp av ett självsignerat certifikat**

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
    ```

    **Skapa ett Kör som-konto och ett klassiskt Kör som-konto med hjälp av ett företagscertifikat**

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
    ```

    **Skapa ett Kör som-konto och ett klassiskt Kör som-konto med hjälp av ett självsignerat certifikat i Azure Government-molnet**

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
    ```

    > [!NOTE]
    > När skriptet har körts uppmanas du att autentisera med Azure. Logga in med ett konto som är medlem i rollen för prenumerationsadministratörer och som är medadministratör för prenumerationen.

Notera följande när skriptet har körts:

* Om du skapade ett klassiskt Kör som-konto med ett självsignerat offentligt certifikat (CER-fil) skapas och sparas det av skriptet i mappen för tillfälliga filer på datorn under användarprofilen *%USERPROFILE%\AppData\Local\Temp*, som du använde för att köra PowerShell-sessionen.

* Om du skapade ett klassiskt Kör som-konto med ett offentligt företagscertifikat (CER-fil) använder du det här certifikatet. Följ anvisningarna för [att ladda upp ett hanterings-API-certifikat till Azure Portal](../azure-api-management-certs.md).

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Ta bort ett Kör som-konto eller ett klassiskt Kör som-konto

Det här avsnittet beskriver hur du tar bort och sedan återskapar ett Kör som-konto eller ett klassiskt Kör som-konto. När du utför den här åtgärden behålls Automation-kontot. När du har tagit bort ett Kör som-konto eller ett klassiskt Kör som-konto kan du återskapa det på Azure Portal.

1. Öppna ditt Automation-konto på Azure Portal.

2. På sidan **Automation-konto** väljer du **Kör som-konton**.

3. På egenskapssidan för **Kör som-konton** väljer du antingen Kör som-kontot eller det klassiska Kör som-kontot som du vill ta bort. Klicka på **Ta bort** i rutan **Egenskaper** för det valda kontot.

   ![Ta bort Kör som-konto](media/manage-runas-account/automation-account-delete-runas.png)

1. Medan kontot tas bort kan du följa förloppet under **Meddelanden** på menyn.

1. När kontot har tagits bort måste du återskapa det på egenskapssidan för **Kör som-konton** genom att välja alternativet Skapa för **Kör som-konto i Azure**.

   ![Återskapa Kör som-kontot för Automation](media/manage-runas-account/automation-account-create-runas.png)

## <a name="cert-renewal"></a>Förnyat självsignerat certifikat

Innan ditt kör som-konto går ut måste du förnya certifikatet. Om du tror att ditt Kör som-konto har komprometterats kan du ta bort och återskapa det. Det här avsnittet beskriver hur du utför dessa åtgärder.

Det självsignerade certifikatet som du skapade för Kör som-kontot går ut ett år efter det datum då det skapades. Du kan förnya det när som helst innan det upphör att gälla. När du förnyar det behålls det aktuella giltiga certifikatet för att säkerställa att alla Runbooks som är köade eller aktivt körs, och som autentiserar med kör som-kontot, inte påverkas negativt. Certifikatet förblir giltigt fram till dess förfallodatum.

> [!NOTE]
> Om du har konfigurerat ditt Kör som-konto för Automation att använda ett certifikat som utfärdats av din företagscertifikatutfärdare och du använder det här alternativet, så ersätts företagscertifikatet av ett självsignerat certifikat.

Du förnyar certifikatet genom att göra följande:

1. Öppna ditt Automation-konto på Azure Portal.

1. Välj **Kör som-konton** under **konto inställningar**.

    ![Egenskapsrutan för Automation-konto](media/manage-runas-account/automation-account-properties-pane.png)

1. På egenskapssidan för **Kör som-konton** väljer du antingen Kör som-kontot eller det klassiska Kör som-kontot som du vill förnya certifikatet för.

1. I rutan **Egenskaper** för det valda kontot klickar du på **Förnya certifikat**.

    ![Förnya certifikat för Kör som-konto](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Medan certifikatet förnyas kan du följa förloppet under **Meddelanden** på menyn.

## <a name="auto-cert-renewal"></a>Konfigurera automatisk certifikat förnyelse med en Automation-Runbook

Om du vill förnya certifikat automatiskt kan du använda en Automation-Runbook. Följande skript på [GitHub](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1) aktiverar den här funktionen i ditt Automation-konto.

- `GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1` Skriptet skapar ett vecko schema för att förnya kör som-konto certifikat.
- Skriptet lägger till en **Update-AutomationRunAsCredential** Runbook till ditt Automation-konto.
  - Du kan också Visa Runbook-koden på GitHub, i skriptet: [Update-AutomationRunAsCredential. ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1).
  - Du kan också använda PowerShell-koden i filen om du vill förnya certifikat manuellt vid behov.

Om du vill testa förnyelse processen direkt använder du följande steg:

1. Redigera **AutomationRunAsCredential-** Runbook och placera ett kommentars text (`#`) på rad 122, framför `Exit(1)` kommandot, som du ser nedan.

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

   ```Output
   Thursday, November 7, 2019 7:00:00 PM
   ```

5. Efter testet redigerar du runbooken och tar bort kommentars tecknen som du lade till i **steg 1**.
6. **Publicera** runbooken.

> [!NOTE]
> Du måste vara **Global administratör** eller **företags administratör** i Azure Active Directory för att köra skriptet.

## <a name="limiting-run-as-account-permissions"></a>Begränsa behörigheter för kör som-konto

Om du vill kontrol lera anpassningen av automatisering mot resurser i Azure kan du köra skriptet [Update-AutomationRunAsAccountRoleAssignments. ps1](https://aka.ms/AA5hug8) i PowerShell-galleriet för att ändra ditt befintliga kör som-konto-tjänstens huvud namn för att skapa och använda en anpassad roll definition. Den här rollen har behörighet till alla resurser utom [Key Vault](https://docs.microsoft.com/azure/key-vault/).

> [!IMPORTANT]
> När `Update-AutomationRunAsAccountRoleAssignments.ps1` skriptet har körts kommer Runbooks som har åtkomst till nyckel valvet genom användning av runas-konton inte längre att fungera. Du bör granska Runbooks i ditt konto för anrop till Azure-nyckel valv.
>
> Om du vill ge åtkomst till ett nyckel valv från Azure Automation-runbooks måste du [lägga till runas-kontot i nyckel valvets behörigheter](#add-permissions-to-key-vault).

Om du behöver begränsa vad runas-tjänstens huvud namn kan göra ytterligare kan du lägga till andra resurs typer till `NotActions` den anpassade roll definitionen. I följande exempel begränsas åtkomsten till `Microsoft.Compute`. Om du lägger till detta i **NotActions** i roll definitionen kommer den här rollen inte att kunna komma åt någon beräknings resurs. Mer information om roll definitioner finns i [förstå roll definitioner för Azure-resurser](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzureRmRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzureRMRoleDefinition
```

För att avgöra om tjänstens huvud namn som används av ditt kör som-  konto finns i deltagaren eller en anpassad roll definition går du till ditt Automation-konto och under **konto inställningar**väljer du **Kör som-konton** > kör som-**konto i Azure** . Under **roll** hittar du den roll definition som används.

[![](media/manage-runas-account/verify-role.png "Verifiera kör som-konto rollen")](media/manage-runas-account/verify-role-expanded.png#lightbox)

Om du vill fastställa vilken roll definition som används av Automation kör som-konton för flera prenumerationer eller Automation-konton kan du använda skriptet [Check-AutomationRunAsAccountRoleAssignments. ps1](https://aka.ms/AA5hug5) i PowerShell-galleriet.

### <a name="add-permissions-to-key-vault"></a>Lägg till behörigheter i Key Vault

Om du vill tillåta Azure Automation hantera Key Vault och ditt kör som-konto-tjänstens huvud namn använder en anpassad roll definition måste du vidta ytterligare åtgärder för att tillåta detta:

* Bevilja behörighet till Key Vault
* Ange åtkomst principen

Du kan använda skriptet [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault. ps1](https://aka.ms/AA5hugb) i PowerShell-galleriet för att ge ditt kör som-konto behörigheter till nyckel valv, eller så kan du gå till [bevilja program åtkomst till ett nyckel valv](../key-vault/key-vault-group-permissions-for-apps.md) för mer information om inställningar behörigheter för nyckel valv.

## <a name="misconfiguration"></a>Felaktig konfiguration

Vissa konfigurationsobjekt som krävs för att Kör som-kontot eller det klassiska Kör som-kontot ska fungera kanske har tagits bort eller kanske inte skapades korrekt under den ursprungliga konfigurationen. Exempel på dessa objekt är:

* Certifikattillgång
* Anslutningstillgång
* Kör som-konto har tagits bort från deltagarrollen
* Huvudnamn för tjänsten eller program i Azure AD

I den föregående instansen och andra instanser av felaktiga konfigurationer identifierar Automation-kontot ändringarna och visar statusen *Ofullständig* på egenskapssidan för **Kör som-konton** för kontot.

![Konfigurationsstatusen Ofullständig för Kör som-konto](media/manage-runas-account/automation-account-runas-incomplete-config.png)

När du väljer Kör som-kontot visas följande felmeddelande i rutan **Egenskaper** för kontot:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Du kan snabbt lösa dessa problem med Kör som-kontot genom att ta bort och återskapa kontot.

## <a name="next-steps"></a>Nästa steg

* Mer information om tjänstens huvud namn finns i [program objekt och tjänst huvud objekt](../active-directory/develop/app-objects-and-service-principals.md).
* Mer information om certifikat och Azure-tjänster finns i [Översikt över certifikat för Azure Cloud Services](../cloud-services/cloud-services-certs-create.md).
