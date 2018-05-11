---
title: Skapa en Azure Automation kör som-konton
description: I den här artikeln beskrivs hur du uppdaterar ditt Automation-konto och skapar Kör som-konton med PowerShell eller från Portal.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 03a5cf6fd049e63616155a87bf112636b74be0a1
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="update-your-automation-account-authentication-with-run-as-accounts"></a>Uppdatera autentiseringen av ditt Automation-konto med Kör som-konton 
Du kan uppdatera ditt befintliga Automation-konto från Azure Portal eller med PowerShell om:

* Du skapar ett Automation-konto men väljer att inte skapa Kör som-kontot.
* Du redan har ett Automation-konto för att hantera Resource Manager-resurser och du vill uppdatera det med ett Kör som-konto för runbook-autentisering.
* Du redan hanterar klassiska resurser med hjälp av ett Automation-konto och du vill uppdatera det och använda det klassiska Kör som-kontot i stället för att skapa ett nytt konto och migrera dina runbookflöden och tillgångar till det.   

Följande objekt skapas i ditt Automation-konto.

**För Kör som-konton:**

* Skapar ett Azure AD-program med ett självsignerat certifikat och ett tjänstobjektskonto för programmet i Azure AD och rollen Deltagare tilldelas för kontot i din aktuella prenumeration. Du kan ändra den här inställningen till Ägare eller en annan roll. Mer information finns i [Rollbaserad åtkomstkontroll i Azure Automation](automation-role-based-access-control.md).
* Skapar en Automation-certifikattillgång med namnet *AzureRunAsCertificate* i det angivna Automation-kontot. Certifikattillgången innehåller certifikatets privata nyckel som används av Azure AD-programmet.
* Skapar en Automation-anslutningstillgång med namnet *AzureRunAsConnection* i det angivna Automation-kontot. Anslutningstillgången innehåller applicationId, tenantId, subscriptionId och certifikatets tumavtryck.

**För klassiska Kör som-konton:**

* Skapar en Automation-certifikattillgång med namnet *AzureClassicRunAsCertificate* i det angivna Automation-kontot. Certifikattillgången innehåller den privata nyckelns certifikat som används av hanteringscertifikatet.
* Skapar en Automation-anslutningstillgång med namnet *AzureClassicRunAsConnection* i det angivna Automation-kontot. Anslutningstillgången innehåller prenumerationsnamnet, subscriptionId och certifikattillgångens namn.

## <a name="prerequisites"></a>Förutsättningar
Om du väljer att [use PowerShell to create the Run As accounts](#create-run-as-account-using-powershell) (använda PowerShell för att skapa Kör-som-konton). Den här processen kräver:

* Windows 10 och Windows Server 2016 med Azure Resource Manager-moduler med version 3.4.1 och senare. PowerShell-skriptet stöds inte i tidigare versioner av Windows.
* Azure PowerShell 1.0 och senare. Information om PowerShell 1.0-versionen finns i [Installera och konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Ett Automation-konto, som refereras till som värdet för *-AutomationAccountName*- och *-ApplicationDisplayName*-parametrarna.

Du hämtar värdena för *SubscriptionID*, *ResourceGroup* och *AutomationAccountName*, som är obligatoriska parametrar för skriptet, genom att göra följande:

1. Klicka på **Alla tjänster** på Azure Portal. I listan över resurser skriver du **Automation**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Automation-konton**.
2. På sidan Automation-konto väljer du ditt Automation-konto och sedan under **Kontoinställningar** väljer du **Egenskaper**.  
3. Notera värdena på sidan **Egenskaper**.<br><br> ![Bladet Egenskaper för Automation-kontot](media/automation-create-runas-account/automation-account-properties.png)  

### <a name="required-permissions-to-update-your-automation-account"></a>Behörigheter som krävs för att uppdatera ditt Automation-konto
För att kunna uppdatera Automation-kontot och slutföra det här avsnittet måste du ha vissa behörigheter.   
 
* AD-användarkontot måste läggas till en roll med behörigheter som motsvarar deltagarrollen för Microsoft.Automation resurser som beskrivs i artikel [rollbaserad åtkomstkontroll i Azure Automation](automation-role-based-access-control.md#contributor).  
* Användare som inte är administratörer i din Azure AD-klient kan [registrera AD-program](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions) om alternativet **Användare kan registrera program** för Azure AD-klienten har angetts till **Ja** på sidan **Användarinställningar**. Om **Nej** har angetts för inställningen Appregistreringar måste användaren som utför den här åtgärden vara global administratör i Azure AD.

Om du inte är medlem i prenumerationens Active Directory-instans innan du läggs till i rollen som global administratör/medadministratör för prenumerationen läggs du till i Active Directory som gäst. I så fall visas varningen ”Du har inte behörighet att skapa ...” på bladet **Lägg till Automation-konto**. Användare som har tilldelats rollen som global administratör/medadministratör kan tas bort från prenumerationens Active Directory-instans och sedan läggas till igen så att de blir fullständiga användare i Active Directory. Du kan kontrollera detta i rutan **Azure Active Directory** på Azure Portal genom att välja **Användare och grupper**, välja **Alla användare**, välja den specifika användaren och sedan välja **Profil**. Värdet för attributet **Användartyp** under användarens profil bör inte vara lika med **Gäst**.

## <a name="create-run-as-account-from-the-portal"></a>Skapa ett Kör som-konto från Portal
I det här avsnittet utför du följande steg för att uppdatera ditt Azure Automation-konto i Azure Portal. Du skapar kör som-konton och klassiska kör som-konton var för sig. Om du inte behöver hantera klassiska resurser kan du bara skapa Azure Kör som-kontot.  

1. Logga in på Azure Portal med ett konto som är medlem i rollen Prenumerationsadministratörer och som är medadministratör för prenumerationen.
2. Klicka på **Alla tjänster** på Azure Portal. I listan över resurser skriver du **Automation**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Automation-konton**.
3. På sidan **Automation-konton** väljer du ditt Automation-konto i listan med Automation-konton.
4. I vänster fönster väljer du **Kör som-konton** under avsnittet **Kontoinställningar**.  
5. Beroende på vilket konto du behöver väljer du antingen **Azures Kör som-konto** eller **Azures klassiska Kör som-konto**. När du har gjort ditt val visas antingen fönstret **Lägg till Azures Kör som-konto** eller **Lägg till Azures klassiska Kör som-konto**. Efter att du läst översiktsinformationen klickar du på **Skapa** för att fortsätta att skapa Kör som-kontot.  
6. Medan Azure skapar Kör-som-kontot kan du följa förloppet under **Meddelanden** på menyn. En banderoll som meddelar att kontot skapats visas också. Den här processen kan ta ett par minuter att slutföra.  

## <a name="create-run-as-account-using-powershell-script"></a>Skapa ett Kör som-konto med ett PowerShell-skript
Det här PowerShell-skriptet har stöd för följande konfigurationer:

* Skapa ett Kör som-konto med hjälp av ett självsignerat certifikat.
* Skapa ett Kör som-konto och ett klassiska Kör som-konto med hjälp av ett självsignerat certifikat.
* Skapa ett Kör som-konto och ett klassiskt Kör som-konto genom att använda ett certifikat utfärdat av en företagscertifikatutfärdare (CA).
* Skapa ett Kör som-konto och ett klassiskt Kör som-konto med hjälp av ett självsignerat certifikat i Azure Government-molnet.

>[!NOTE]
> Om du väljer något av alternativen för att skapa ett klassiskt Kör som-konto laddar du upp det offentliga certifikatet (filnamnstillägget .cer) när skriptet har körts till hanteringsarkivet för den prenumeration som Automation-kontot skapades i.
> 

1. Spara följande skript på datorn. I det här exemplet sparar du det med filnamnet *New-RunAsAccount.ps1*.

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
        [String] $EnterpriseCertPathForRunAsAccount,
        
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
        
        $startDate = Get-Date
        $endDate = (Get-Date $PfxCert.GetExpirationDateString()).AddDays(-1)
        
        #Create an Azure AD application, AD App Credential, AD ServicePrincipal
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId) 
        $ApplicationCredential = New-AzureRmADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $startDate -EndDate $endDate 
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId 
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id
        
        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
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

2. Starta **Windows PowerShell** från **startskärmen** med utökade användarrättigheter.
3. Från det upphöjda kommandoradsgränssnittet går du till mappen som innehåller det skript som du skapade i steg 1.  
4. Kör skriptet genom att använda de parametervärden för konfigurationen som du behöver.

    **Skapa ett Kör som-konto med hjälp av ett självsignerat certifikat**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false`

    **Skapa ett Kör som-konto och ett klassiska Kör som-konto med hjälp av ett självsignerat certifikat**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true`

    **Skapa ett Kör som-konto och ett klassiskt Kör som-konto med hjälp av ett företagscertifikat**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>`

    **Skapa ett Kör som-konto och ett klassiskt Kör som-konto med hjälp av ett självsignerat certifikat i Azure Government-molnet**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment`

    > [!NOTE]
    > När skriptet har körts uppmanas du att autentisera med Azure. Logga in med ett konto som är medlem i rollen för prenumerationsadministratörer och som är medadministratör för prenumerationen.
    >
    >

Notera följande när skriptet har körts:
* Om du skapade ett klassiskt Kör som-konto med ett självsignerat offentligt certifikat (CER-fil) skapas och sparas det av skriptet i mappen för tillfälliga filer på datorn under användarprofilen *%USERPROFILE%\AppData\Local\Temp*, som du använde för att köra PowerShell-sessionen.
* Om du skapade ett klassiskt Kör som-konto med ett offentligt företagscertifikat (CER-fil) använder du det här certifikatet. Följ instruktionerna för [ladda upp ett hanteringscertifikat API till Azure portal](../azure-api-management-certs.md), och sedan Validera konfigurationen för serveradministratörsautentisering med klassisk distributionsresurser med hjälp av den [exempelkod för autentisering med Azure klassiska distributionsresurser](automation-verify-runas-authentication.md#classic-run-as-authentication). 
* Om du *inte* skapade ett klassiskt Kör som-konto autentiserar du med Resource Manager-resurser och verifierar konfigurationen av autentiseringsuppgifterna med hjälp av [exempelkoden för autentisering med Service Management-resurser](automation-verify-runas-authentication.md#automation-run-as-authentication).

## <a name="limiting-run-as-account-permissions"></a>Begränsar behörigheterna kör som-konto

Om du vill styra meddelanderegler för automation mot resurser i Azure Automation, beviljas kör som-kontot som standard rättigheter som deltagare i prenumeration. Om du vill begränsa vad RunAs tjänstens huvudnamn kan göra du ta bort kontot från deltagarrollen till prenumerationen och Lägg till den som en deltagare till resursgrupper som du vill ange.

Välj i Azure-portalen **prenumerationer** och välj prenumerationen för Automation-konto. Välj **åtkomstkontroll (IAM)** och söka efter tjänstens huvudnamn för Automation-kontot (det ser ut som \<AutomationAccountName\>_unique identifierare). Välj kontot och klicka på **ta bort** ta bort den från prenumerationen.

![Deltagare i prenumeration](media/automation-create-runas-account/automation-account-remove-subscription.png)

Om du vill lägga till tjänstens huvudnamn i en resursgrupp, välja en resursgrupp i Azure portal och välj **åtkomstkontroll (IAM)**. Välj **Lägg till**, öppnas den **lägga till behörigheter** sidan. För **rollen**väljer **deltagare**. I den **Välj** text Skriv namnet på tjänstens huvudnamn för Kör som-konto och välj i listan. Klicka på **Spara** för att spara ändringarna. Gör detta för resursgrupper som du vill ge din Azure Automation kör som service principal åtkomst till.

## <a name="next-steps"></a>Nästa steg
* Läs mer om tjänstens huvudnamn [program och tjänstens huvudnamn objekt](../active-directory/active-directory-application-objects.md).
* Mer information om certifikat och Azure-tjänster finns [certifikat översikt för Azure Cloud Services](../cloud-services/cloud-services-certs-create.md).
