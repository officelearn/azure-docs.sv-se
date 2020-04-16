---
title: Köra runbooks på Azure Automation Hybrid Runbook Worker
description: Den här artikeln innehåller information om hur du kör runbooks på datorer i ditt lokala datacenter eller molnleverantör med hybridkörningsarbetsrollen.
services: automation
ms.subservice: process-automation
ms.date: 01/29/2019
ms.topic: conceptual
ms.openlocfilehash: b65c72e0c65cf9aa84cb614478fbdf78258f3054
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405828"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Köra runbooks på en Hybrid Runbook Worker

Runbooks som riktar sig till en Hybrid Runbook Worker hanterar vanligtvis resurser på den lokala datorn eller mot resurser i den lokala miljön där arbetaren distribueras. Runbooks i Azure Automation hanterar vanligtvis resurser i Azure-molnet. Även om de används på olika sätt är runbooks som körs i Azure Automation och runbooks som körs på en Hybrid Runbook Worker identiska i struktur.

När du skapar en runbook som ska köras på en Hybrid Runbook Worker bör du redigera och testa runbooken på datorn som är värd för arbetaren. Värddatorn har alla PowerShell-moduler och nätverksåtkomst som krävs för att hantera och komma åt de lokala resurserna. När du har testat runbooken på Hybrid Runbook Worker-datorn kan du sedan överföra den till Azure Automation-miljön, där den kan köras på arbetaren. 

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Runbook-behörigheter för en Hybrid Runbook Worker

Eftersom de använder icke-Azure-resurser kan runbooks som körs på en Hybrid Runbook Worker inte använda den autentiseringsmekanism som vanligtvis används av runbooks autentisera till Azure-resurser. En runbook tillhandahåller antingen sin egen autentisering till lokala resurser eller konfigurerar autentisering med [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). Du kan också ange ett Run As-konto för att ange en användarkontext för alla runbooks.

### <a name="runbook-authentication"></a>Autentisering av runbook

Som standard körs runbooks på den lokala datorn. För Windows körs de i kontexten för det lokala **systemkontot.** För Linux körs de i samband med det särskilda användarkontot **nxautomation**. I båda fallen måste runbooks tillhandahålla sin egen autentisering till resurser som de kommer åt.

Du kan använda [Autentiseringsuppgifter](automation-credentials.md) och [certifikatresurser](automation-certificates.md) i runbooken med cmdlets som gör att du kan ange autentiseringsuppgifter så att runbooken kan autentisera till olika resurser. I följande exempel visas en del av en runbook som startar om en dator. Autentiseringsuppgifter hämtas från en autentiseringstillgång och namnet på datorn från en `Restart-Computer` variabel tillgång och använder sedan dessa värden med cmdleten.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Du kan också använda en [InlineScript-aktivitet.](automation-powershell-workflow.md#inlinescript) `InlineScript`kan du köra kodblock på en annan dator med autentiseringsuppgifter som anges av [pscredential gemensamma parametern](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="run-as-account"></a>Kör som-konto

I stället för att runbooken ska tillhandahålla sin egen autentisering till lokala resurser kan du ange ett Run As-konto för en Hybrid Runbook Worker-grupp. För att kunna göra detta måste du definiera en [autentiseringstillgång](automation-credentials.md) som har åtkomst till lokala resurser. Dessa resurser omfattar certifikatarkiv och alla runbooks som körs under dessa autentiseringsuppgifter på en Hybrid Runbook Worker i gruppen.

Användarnamnet för autentiseringsuppgifterna måste vara i något av följande format:

* Domän\användarnamn
* username@domain
* användarnamn (för konton som är lokala till den lokala datorn)

Använd följande procedur för att ange ett Run As-konto för en hybridkörningsarbetsgrupp.

1. Skapa en [autentiseringstillgång](automation-credentials.md) med åtkomst till lokala resurser.
2. Öppna Automation-kontot i Azure-portalen.
3. Markera panelen **Hybridarbetaregrupper** och välj sedan gruppen.
4. Välj **Alla inställningar**, följt av Inställningar för **hybridarbetsgrupp**.
5. Ändra värdet **för Kör från** **standard** till **anpassad**.
6. Markera autentiseringsuppgifterna och klicka på **Spara**.

### <a name="managed-identities-for-azure-resources"></a><a name="managed-identities-for-azure-resources"></a>Hanterade identiteter för Azure-resurser

Hybrid Runbook Workers på virtuella Azure-datorer kan använda hanterade identiteter för Azure-resurser för att autentisera till Azure-resurser. Att använda hanterade identiteter för Azure-resurser i stället för Run As-konton ger fördelar eftersom du inte behöver:

* Exportera Run As-certifikatet och importera det sedan till Hybrid Runbook Worker.
* Förnya certifikatet som används av kontot Kör som.
* Hantera anslutningsobjektet Run As i runbookkoden.

Följ nästa steg för att använda en hanterad identitet för Azure-resurser på en Hybrid Runbook Worker.

1. Skapa en virtuell Azure-dator.
2. Konfigurera hanterade identiteter för Azure-resurser på den virtuella datorn. Se [Konfigurera hanterade identiteter för Azure-resurser på en virtuell dator med Azure-portalen](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).
3. Ge den virtuella datorn åtkomst till en resursgrupp i Resource Manager. Se [Använda en systemtilldelad windows VM-systemidentitet för att komma åt Resource Manager](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
4. Installera Hybrid Runbook-arbetaren på den virtuella datorn. Se [Distribuera en Windows Hybrid Runbook Worker](automation-windows-hrw-install.md).
5. Uppdatera runbooken för att använda cmdleten `Identity` [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) med parametern för att autentisera till Azure-resurser. Den här konfigurationen minskar behovet av att använda ett Run As-konto och utföra den associerade kontohanteringen.

```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
```

> [!NOTE]
> `Connect-AzAccount -Identity`fungerar för en Hybrid Runbook Worker med hjälp av en systemtilldelad identitet och en enda användartilldelad identitet. Om du använder flera användartilldelade identiteter på Hybrid Runbook `AccountId` Worker `Connect-AzAccount` måste runbooken ange parametern för att välja en viss användartilldelad identitet.

### <a name="automation-run-as-account"></a><a name="runas-script"></a>Automation kör som konto

Som en del av din automatiska byggprocess för distribution av resurser i Azure kan du behöva åtkomst till lokala system för att stödja en uppgift eller uppsättning steg i distributionssekvensen. Om du vill tillhandahålla autentisering mot Azure med kontot Kör som måste du installera kontocertifikatet Kör som.

Följande PowerShell-runbook, kallad **Export-RunAsCertificateToHybridWorker**, exporterar Run As-certifikatet från ditt Azure Automation-konto. Runbook hämtar och importerar certifikatet till certifikatarkivet för den lokala datorn på en Hybrid Runbook Worker som är ansluten till samma konto. När det är klart det steget verifierar runbooken att arbetaren kan autentisera till Azure med kontot Kör som.

```azurepowershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account. Run this runbook on the hybrid worker where you want the certificate installed. This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running on the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
LASTEDIT: 2016.10.13
#>

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

# Test to see if authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzContext -Subscription $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm that Azure Resource Manager calls are working
Get-AzAutomationAccount | Select-Object AutomationAccountName
```

>[!NOTE]
>För `Add-AzAccount` PowerShell-runbooks `Add-AzureRMAccount` och är `Connect-AzAccount`alias för . När du söker i biblioteksobjekten kan `Add-AzAccount`du använda om du inte ser `Connect-AzAccount`det eller uppdatera modulerna i ditt Automation-konto.

Så här slutför du förberedelsen av kontot Kör som:

1. Spara **runascertificatetoHybridWorker-runbooken** på datorn med ett **PS1-tillägg.**
2. Importera den till ditt Automation-konto.
3. Redigera runbooken och ändra `Password` variabelns värde till ditt eget lösenord. 
4. Publicera runbooken.
5. Kör runbooken med inriktning på hybridkörningsarbetsgruppen som körs och autentiserar runbooks med kontot Kör som. 
6. Undersök jobbströmmen för att se att den rapporterar försöket att importera certifikatet till det lokala datorarkivet och följer med flera rader. Det här problemet beror på hur många Automation-konton du definierar i din prenumeration och hur framgångsrik autentiseringen är.

## <a name="job-behavior-on-hybrid-runbook-workers"></a>Jobbbeteende på Hybrid Runbook Workers

Azure Automation hanterar jobb på Hybrid Runbook Workers något annorlunda än jobb som körs i Azure sandlådor. En viktig skillnad är att det inte finns någon gräns för jobbvaraktighet för runbook-arbetare. Runbooks som körs i Azure-sandlådor är begränsade till tre timmar på grund av [rättvis delning](automation-runbook-execution.md#fair-share).

För en tidskrävande runbook vill du vara säker på att den är motståndskraftig mot en eventuell omstart, till exempel om datorn som är värd för arbetaren startas om. Om värddatorn för Hybrid Runbook Worker startas om startas alla runbook-jobb som körs om från början eller från den senaste kontrollpunkten för PowerShell-arbetsflödeskörningsböcker. När ett runbook-jobb har startats om mer än tre gånger pausas det.

Kom ihåg att jobb för Hybrid Runbook Workers körs under det lokala systemkontot på Windows eller **nxautomation-kontot** på Linux. För Linux måste du se till att **nxautomation-kontot** har åtkomst till den plats där runbookmodulerna lagras. När du använder cmdleten [Install-Module](/powershell/module/powershellget/install-module) måste du ange `Scope` AllUsers för parametern för att säkerställa att **nxautomationskontot** har åtkomst. Mer information om PowerShell på Linux finns i [Kända problem för PowerShell på plattformar som inte kommer från Windows](https://docs.microsoft.com/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms).

## <a name="starting-a-runbook-on-a-hybrid-runbook-worker"></a>Starta en runbook på en Hybrid Runbook Worker

[Starta en runbook i Azure Automation](automation-starting-a-runbook.md) beskriver olika metoder för att starta en runbook. Start för en runbook på en Hybrid Runbook Worker använder ett **Run on-alternativ** som gör att du kan ange namnet på en Hybrid Runbook Worker-grupp. När en grupp har angetts hämtar och kör en av arbetarna i den gruppen runbooken. Om runbooken inte anger det här alternativet kör Azure Automation runbooken som vanligt.

När du startar en runbook i Azure-portalen visas alternativet **Kör på** som du kan välja **Azure** eller **Hybrid Worker**för . Om du väljer **Hybridarbetare**kan du välja gruppen Hybrid Runbook Worker i en listruta.

Använd `RunOn` parametern med [Cmdleten Start-AzAutomationRunbook.](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0) I följande exempel används Windows PowerShell för att starta en runbook med namnet **Test-Runbook** i en Hybrid Runbook Worker-grupp med namnet MyHybridGroup.

```azurepowershell-interactive
Start-AzAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> Du bör [hämta den senaste PowerShell-versionen](https://azure.microsoft.com/downloads/) om du har en tidigare installerad. Installera endast den här versionen på den arbetsstation där du startar runbooken från PowerShell. Du behöver inte installera den på Hybrid Runbook Worker-datorn om du inte tänker starta runbooks från den här datorn.

## <a name="working-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Arbeta med signerade runbooks på en Windows Hybrid Runbook Worker

Du kan konfigurera en Windows Hybrid Runbook Worker så att endast signerade runbooks körs.

> [!IMPORTANT]
> När du har konfigurerat en Hybrid Runbook Worker för att köra endast signerade runbooks, kommer runbooks som inte har signerats att misslyckas med att köra på arbetaren.

### <a name="create-signing-certificate"></a>Skapa signeringscertifikat

I följande exempel skapas ett självsignerat certifikat som kan användas för signering av runbooks. Den här koden skapar certifikatet och exporterar det så att Hybrid Runbook Worker kan importera det senare. Tumavtrycket returneras också för senare användning i referenscertifikatet.

```powershell
# Create a self-signed certificate that can be used for code signing
$SigningCert = New-SelfSignedCertificate -CertStoreLocation cert:\LocalMachine\my `
                                        -Subject "CN=contoso.com" `
                                        -KeyAlgorithm RSA `
                                        -KeyLength 2048 `
                                        -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
                                        -KeyExportPolicy Exportable `
                                        -KeyUsage DigitalSignature `
                                        -Type CodeSigningCert


# Export the certificate so that it can be imported to the hybrid workers
Export-Certificate -Cert $SigningCert -FilePath .\hybridworkersigningcertificate.cer

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Retrieve the thumbprint for later use
$SigningCert.Thumbprint
```

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>Importera certifikat och konfigurera arbetare för signaturverifiering

Kopiera certifikatet som du har skapat till varje Hybrid Runbook Worker i en grupp. Kör följande skript för att importera certifikatet och konfigurera arbetarna så att de använder signaturverifiering på runbooks.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>Signera dina runbooks med certifikatet

När Hybrid Runbook Workers har konfigurerats för att endast använda signerade runbooks måste du signera runbooks som ska användas på Hybrid Runbook Worker. Använd följande exempel på PowerShell-kod för att signera dessa runbooks.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

När en runbook har signerats måste du importera den till ditt Automation-konto och publicera den med signaturblocket. Mer information om hur du importerar runbooks finns i [Importera en runbook från en fil till Azure Automation](manage-runbooks.md#importing-a-runbook).

## <a name="working-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Arbeta med signerade runbooks på en Linux Hybrid Runbook Worker

För att kunna arbeta med signerade runbooks måste en Linux Hybrid Runbook Worker ha [GPG](https://gnupg.org/index.html) körbar på den lokala datorn.

> [!IMPORTANT]
> När du har konfigurerat en Hybrid Runbook Worker för att köra endast signerade runbooks, kommer runbooks som inte har signerats att misslyckas med att köra på arbetaren.

### <a name="create-a-gpg-keyring-and-keypair"></a>Skapa en GPG-nyckelring och nyckelpar

Om du vill skapa GPG-nyckelringen och nyckelparet använder du hybridkörningsarbetskontot. **nxautomation**

1. Använd sudo-programmet för att logga in som **nxautomation-konto.**

    ```bash
    sudo su – nxautomation
    ```

2. När du använder **nxautomation,** generera GPG keypair. GPG guidar dig genom stegen. Du måste ange namn, e-postadress, förfallotid och lösenfras. Sedan väntar du tills det finns tillräckligt med entropin på maskinen för att nyckeln ska genereras.

    ```bash
    sudo gpg --generate-key
    ```

3. Eftersom GPG-katalogen har genererats med sudo måste du ändra dess ägare till **nxautomation** med hjälp av följande kommando.

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>Göra nyckelringen tillgänglig för Hybrid Runbook Worker

När nyckelringen har skapats gör du den tillgänglig för Hybrid Runbook Worker. Ändra inställningsfilen **/var/opt/microsoft/omsagent/state/automationworker/diy/worker.conf** för att inkludera följande `[worker-optional]`exempelkod under filavsnittet .

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>Kontrollera att signaturverifieringen är aktiverad

Om signaturverifiering har inaktiverats på datorn måste du aktivera den genom att köra följande sudo-kommando. Ersätt `<LogAnalyticsworkspaceId>` med arbetsyte-ID: et.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Signera en runbook

När du har konfigurerat signaturvalidering använder du följande GPG-kommando för att signera en runbook.

```bash
gpg –-clear-sign <runbook name>
```

Den signerade runbooken kallas ** <runbook name>.asc**.

Du kan nu ladda upp den signerade runbooken till Azure Automation och köra den som en vanlig runbook.

## <a name="next-steps"></a>Nästa steg

* Mer information om metoderna för att starta en runbook finns [i Starta en runbook i Azure Automation](automation-starting-a-runbook.md).
* Information om hur du använder textredigeraren för att arbeta med PowerShell-runbooks i Azure Automation finns [i Redigera en runbook i Azure Automation](automation-edit-textual-runbook.md).
* Om runbooks inte slutförs läser du felsökningsguiden för [körningsfel](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails)i runbook .
* Mer information om PowerShell, inklusive språkreferens- och utbildningsmoduler, finns i [PowerShell Docs](https://docs.microsoft.com/powershell/scripting/overview).
* En PowerShell-cmdlet-referens finns i [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
