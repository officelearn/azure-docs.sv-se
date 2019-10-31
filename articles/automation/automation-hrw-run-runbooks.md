---
title: Köra Runbooks på Azure Automation Hybrid Runbook Worker
description: Den här artikeln innehåller information om att köra Runbooks på datorer i ditt lokala data Center eller en moln leverantör med Hybrid Runbook Worker-rollen.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 01/29/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 83c185a6ba8f1c5e6edf095db5baf575f750fa3b
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176467"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Köra Runbooks på en Hybrid Runbook Worker

Det finns ingen skillnad i strukturen för Runbooks som körs i Azure Automation och Runbooks som körs på en Hybrid Runbook Worker. Runbooks som du använder med de mest sannolika kan variera avsevärt. Den här skillnaden beror på att Runbooks som riktar sig till en Hybrid Runbook Worker vanligt vis hanterar resurser på den lokala datorn eller mot resurser i den lokala miljön där den distribueras. Runbooks i Azure Automation hanterar vanligt vis resurser i Azure-molnet.

När du skapar runbooks som ska köras på en Hybrid Runbook Worker bör du redigera och testa Runbooks i datorn som är värd för Hybrid Worker. Värddatorn har alla PowerShell-moduler och nätverks åtkomst som du behöver för att hantera och komma åt lokala resurser. När en Runbook har testats på Hybrid Worker-datorn kan du sedan ladda upp den till Azure Automation-miljön där den är tillgänglig för körning i hybrid Worker. Det är viktigt att veta att jobb som körs under det lokala system kontot för Windows eller ett särskilt användar konto `nxautomation` på Linux. I Linux innebär det att du måste se till att kontot `nxautomation` har åtkomst till den plats där du lagrar dina moduler. När du använder cmdleten [install-module](/powershell/module/powershellget/install-module) anger du **AllUsers** till parametern `-Scope` för att bekräfta att `nxautomation` kontot har åtkomst.

Mer information om PowerShell på Linux finns i [kända problem för PowerShell på andra plattformar än Windows-plattformar](https://docs.microsoft.com/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms).

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>Starta en Runbook på Hybrid Runbook Worker

Om du [startar en Runbook i Azure Automation](automation-starting-a-runbook.md) beskrivs olika metoder för att starta en Runbook. Hybrid Runbook Worker lägger till ett **RunOn** -alternativ där du kan ange namnet på en hybrid Runbook Workers grupp. När en grupp anges hämtas och körs runbooken av en av arbetarna i gruppen. Om det här alternativet inte anges körs det i Azure Automation som normalt.

När du startar en Runbook i Azure Portal visas ett alternativ **för att köra på** där du kan välja **Azure** eller **hybrid Worker**. Om du väljer **hybrid Worker**kan du välja gruppen från en listruta.

Använd parametern **RunOn** . Du kan använda följande kommando för att starta en Runbook med namnet test-Runbook på en Hybrid Runbook Worker grupp med namnet MyHybridGroup med hjälp av Windows PowerShell.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> Parametern **RunOn** har lagts till i cmdleten **Start-AzureAutomationRunbook** i version 0.9.1 till och av Microsoft Azure PowerShell. Du bör [Ladda ned den senaste versionen](https://azure.microsoft.com/downloads/) om du har en tidigare installerad. Du behöver bara installera den här versionen på en arbets station där du startar runbooken från PowerShell. Du behöver inte installera den på arbets datorn om du inte tänker starta Runbooks från den datorn "

## <a name="runbook-permissions"></a>Runbook-behörigheter

Runbooks som körs på en Hybrid Runbook Worker kan inte använda samma metod som vanligt vis används för Runbooks som autentiserar sig för Azure-resurser, eftersom de har åtkomst till resurser som inte finns i Azure. Runbooken kan antingen tillhandahålla egen autentisering till lokala resurser eller konfigurera autentisering med [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). Du kan också ange ett RunAs-konto för att tillhandahålla en användar kontext för alla Runbooks.

### <a name="runbook-authentication"></a>Runbook-autentisering

Som standard körs Runbooks i kontexten för det lokala system kontot för Windows och ett särskilt användar konto `nxautomation` för Linux på den lokala datorn, så de måste ge sin egen autentisering till resurser som de har åtkomst till.

Du kan använda [autentiseringsuppgifter](automation-credentials.md) och [certifikat](automation-certificates.md) till gångar i din Runbook med cmdletar som gör att du kan ange autentiseringsuppgifter så att du kan autentisera till olika resurser. I följande exempel visas en del av en Runbook som startar om en dator. Den hämtar autentiseringsuppgifter från en inloggnings till gång och namnet på datorn från en variabel till gång och använder sedan dessa värden med cmdleten Restart-Computer.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Du kan också använda [InlineScript](automation-powershell-workflow.md#inlinescript), vilket gör att du kan köra kodblock på en annan dator med autentiseringsuppgifter som anges av [parametern PSCredential common](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="runas-account"></a>Kör som-konto

Som standard använder Hybrid Runbook Worker lokalt system för Windows och ett särskilt användar konto `nxautomation` för Linux för att köra Runbooks. I stället för att ha Runbooks som ger sin egen autentisering till lokala resurser kan du ange ett **Kör** som-konto för en hybrid Worker-grupp. Du anger en [inloggnings till gång](automation-credentials.md) som har åtkomst till lokala resurser, inklusive certifikat Arkiv och alla Runbooks som körs under dessa autentiseringsuppgifter när de körs på en hybrid Runbook Worker i gruppen.

Användar namnet för autentiseringsuppgiften måste vara i något av följande format:

* namn
* username@domain
* användar namn (för lokala konton på den lokala datorn)

Använd följande procedur för att ange ett Kör som-konto för en hybrid Worker-grupp:

1. Skapa en [inloggnings till gång](automation-credentials.md) med till gång till lokala resurser.
2. Öppna Automation-kontot i Azure Portal.
3. Välj panelen **hybrid Worker grupper** och välj sedan gruppen.
4. Välj **alla inställningar** och sedan **Inställningar för Hybrid Worker-grupper**.
5. Ändra **Kör som** från **standard** till **anpassad**.
6. Välj autentiseringsuppgiften och klicka på **Spara**.

### <a name="managed-identities-for-azure-resources"></a>Hanterade identiteter för Azure-resurser

Hybrid Runbook Worker som körs på virtuella Azure-datorer kan använda hanterade identiteter för Azure-resurser för att autentisera till Azure-resurser. Det finns många fördelar med att använda hanterade identiteter för Azure-resurser via kör som-konton.

* Du behöver inte exportera kör som-certifikatet och sedan importera det till Hybrid Runbook Worker
* Du behöver inte förnya certifikatet som används av kör som-kontot
* Du behöver inte hantera kör som-anslutningsobjektet i Runbook-koden

Om du vill använda en hanterad identitet för Azure-resurser på en hybrid Runbook Worker måste du utföra följande steg:

1. Skapa en virtuell Azure-dator
2. [Konfigurera hanterade identiteter för Azure-resurser på den virtuella datorn](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)
3. [Ge din VM åtkomst till en resurs grupp i Resource Manager](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager) -självstudie – Windows-VM-Access-arm. MD # get-a-Access-token-using-the-VM-system-Assigned-Identity-and-use-it-to-Call-Azure-Resource-Manager)
4. [Installera Windows-hybrid Runbook Worker](automation-windows-hrw-install.md#installing-the-windows-hybrid-runbook-worker) på den virtuella datorn.

När föregående steg har slutförts kan du använda `Connect-AzureRmAccount -Identity` i runbooken för att autentisera till Azure-resurser. Den här konfigurationen minskar behovet av att använda ett Kör som-konto och hantera certifikatet för kör som-kontot.

```powershell
# Connect to Azure using the Managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
Connect-AzureRmAccount -Identity

# Get all VM names from the subscription
Get-AzureRmVm | Select Name
```

> [!NOTE]
> `Connect-AzureRMAccount -Identity` fungerar för en Hybrid Runbook Worker som använder en tilldelad identitet och en enskild användare som tilldelats identiteten. Om du behöver använda flera användare som tilldelats identiteter på HRW måste du ange parametern `-AccountId` för att välja den tilldelade användaren.

### <a name="runas-script"></a>Kör som-konto för Automation

Som en del av den automatiserade Bygg processen för att distribuera resurser i Azure kan du behöva åtkomst till lokala system för att stödja en uppgift eller en uppsättning steg i din distributions ordning. Om du vill ha stöd för autentisering mot Azure med hjälp av kör som-kontot måste du installera certifikatet för kör som-kontot.

Följande PowerShell-Runbook, **export-RunAsCertificateToHybridWorker**, exporterar kör som-certifikatet från ditt Azure Automation-konto och laddar ned och importerar den till den lokala datorns certifikat Arkiv på en hybrid Worker, som är ansluten till samma konto. När steget har slutförts verifierar den att arbetaren kan autentiseras till Azure med hjälp av kör som-kontot.

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
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.
Run this runbook in the hybrid worker where you want the certificate installed.
This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running in the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
AUTHOR: Azure Automation Team
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

# Test that authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzureRmContext -SubscriptionId $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm Azure Resource Manager calls are working
Get-AzureRmAutomationAccount | Select-Object AutomationAccountName
```

> [!IMPORTANT]
> **Add-AzureRmAccount** är nu ett alias för **Connect-AzureRmAccount**. Om du inte ser **Connect-AzureRMAccount**när du söker i biblioteks objekt kan du använda **Add-AzureRMAccount**, eller så kan du uppdatera dina moduler i ditt Automation-konto.

Spara *export-RunAsCertificateToHybridWorker* -runbooken till datorn med ett tillägg i `.ps1`. Importera den till ditt Automation-konto och redigera runbooken och ändra värdet för variabeln `$Password` med ditt eget lösen ord. Publicera och kör sedan runbooken. Rikta den Hybrid Worker grupp som ska köra och autentisera Runbooks med hjälp av kör som-kontot. Jobb strömmen rapporterar försöket att importera certifikatet till den lokala datorns Arkiv och följer med flera rader. Detta beteende beror på hur många Automation-konton du definierar i din prenumeration och om autentiseringen lyckas.

## <a name="job-behavior"></a>Jobb beteende

Jobb hanteras något annorlunda i hybrid Runbook Worker än de är när de körs på Azure-sandbox. En viktig skillnad är att det inte finns någon gräns för jobb varaktigheten för Hybrid Runbook Worker. Runbooks som kördes i Azure-Sandbox är begränsade till tre timmar på grund av en [rättvis delning](automation-runbook-execution.md#fair-share). För en långvarig Runbook vill du se till att den är elastisk för eventuell omstart. Till exempel om den dator som är värd för Hybrid Worker-omstarter. Om hybrid Worker-datorn startas om startar alla pågående Runbook-jobb från början eller från den senaste kontroll punkten för PowerShell Workflow-Runbooks. När ett Runbook-jobb har startats om mer än tre gånger är det pausat.

## <a name="run-only-signed-runbooks"></a>Kör bara signerade Runbooks

Hybrid Runbook Worker kan konfigureras att bara köra signerade Runbooks med en del konfiguration. I följande avsnitt beskrivs hur du konfigurerar dina hybrid Runbook Worker-användare att köra signerade [Windows hybrid Runbook Worker](#windows-hybrid-runbook-worker) -och [Linux-hybrid Runbook Worker](#linux-hybrid-runbook-worker)

> [!NOTE]
> När du har konfigurerat en Hybrid Runbook Worker att bara köra signerade Runbooks, kommer Runbooks som **inte** har signerats att kunna köras i arbetaren.

### <a name="windows-hybrid-runbook-worker"></a>Windows Hybrid Runbook Worker

#### <a name="create-signing-certificate"></a>Skapa signerings certifikat

I följande exempel skapas ett självsignerat certifikat som kan användas för signering av Runbooks. Exemplet skapar certifikatet och exporterar det. Certifikatet importeras till hybrid Runbook Worker senare. Tumavtrycket returneras även det här värdet används senare för att referera till certifikatet.

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

#### <a name="configure-the-hybrid-runbook-workers"></a>Konfigurera hybrid Runbook Worker

Kopiera certifikatet som skapats till varje Hybrid Runbook Worker i en grupp. Kör följande skript för att importera certifikatet och konfigurera Hybrid Worker att använda verifiering av signaturen på Runbooks.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

#### <a name="sign-your-runbooks-using-the-certificate"></a>Signera dina Runbooks med hjälp av certifikatet

Med hybrid Runbook Worker som kon figurer ATS att bara använda signerade Runbooks måste du signera Runbooks som ska användas på Hybrid Runbook Worker. Använd följande PowerShell-exempel för att signera dina runbooks.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

När runbooken har signerats måste den importeras till ditt Automation-konto och publiceras med signatur-blocket. Information om hur du importerar Runbooks finns i [Importera en Runbook från en fil till Azure Automation](manage-runbooks.md#import-a-runbook).

### <a name="linux-hybrid-runbook-worker"></a>Linux-Hybrid Runbook Worker

För att signera Runbooks på en Linux-Hybrid Runbook Worker måste Hybrid Runbook Worker ha den körbara filen [GPG](https://gnupg.org/index.html) på datorn.

#### <a name="create-a-gpg-keyring-and-keypair"></a>Skapa en GPG-nyckelring och nyckel par

Om du vill skapa nyckel ringen och nyckel paret måste du använda Hybrid Runbook Worker konto `nxautomation`.

Använd `sudo` för att logga in som `nxautomation`-konto.

```bash
sudo su – nxautomation
```

När du använder `nxautomation`-kontot genererar du GPG-nyckelpar.

```bash
sudo gpg --generate-key
```

GPG vägleder dig genom stegen för att skapa ett nyckel par. Du måste ange ett namn, en e-postadress, förfallo tid, lösen fras och vänta tills det finns tillräckligt med entropi på datorn för att nyckeln ska genereras.

Eftersom GPG-katalogen genererades med sudo, måste du ändra dess ägare till `nxautomation`.

Kör följande kommando för att ändra ägare.

```bash
sudo chown -R nxautomation ~/.gnupg
```

#### <a name="make-the-keyring-available-the-hybrid-runbook-worker"></a>Se till att nyckel ringen är tillgänglig Hybrid Runbook Worker

När nyckel ringen har skapats måste du göra den tillgänglig för Hybrid Runbook Worker. Ändra inställnings filen `/var/opt/microsoft/omsagent/state/automationworker/diy/worker.conf` att inkludera följande exempel under avsnittet `[worker-optional]`

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

#### <a name="verify-signature-validation-is-on"></a>Verifiera att verifieringen av signaturen är på

Om verifiering av signatur har inaktiverats på datorn måste du aktivera den. Kör följande kommando för att aktivera verifiering av signatur. Ersätter `<LogAnalyticsworkspaceId>` med ditt arbetsyte-ID.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

#### <a name="sign-a-runbook"></a>Signera en Runbook

När signaturverifiering har kon figurer ATS kan du använda följande kommando för att signera en Runbook:

```bash
gpg –-clear-sign <runbook name>
```

Den signerade Runbook-flödet får namnet `<runbook name>.asc`.

Den signerade runbooken kan nu överföras till Azure Automation och kan köras som en vanlig Runbook.

## <a name="next-steps"></a>Nästa steg

* Mer information om de olika metoder som kan användas för att starta en Runbook finns [i starta en Runbook i Azure Automation](automation-starting-a-runbook.md).
* För att förstå de olika sätten att arbeta med PowerShell-Runbooks i Azure Automation använda text redigeraren, se [Redigera en Runbook i Azure Automation](automation-edit-textual-runbook.md)
* Om Runbooks inte slutförs kan du läsa fel söknings guiden om [fel i Runbook-körningen](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).
* Mer information om PowerShell, inklusive språk referens-och inlärnings moduler finns i [PowerShell-dokumenten](https://docs.microsoft.com/en-us/powershell/scripting/overview).
