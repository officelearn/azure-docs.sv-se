---
title: Köra runbooks i Azure Automation Hybrid Runbook Worker
description: Den här artikeln innehåller information om runbooks som körs på virtuella datorer i ditt lokala datacenter eller i hyperskala med Hybrid Runbook Worker-rollen.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 07/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 118f9d7865728177f323078c036aee1884a61431
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39390304"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Runbooks som körs på en Hybrid Runbook Worker

Det finns ingen skillnad i runbooks som körs i Azure Automation och de som körs på en Hybrid Runbook Worker-strukturen. Runbooks som du använder med var och en troligen skiljer sig avsevärt dock eftersom runbooks som körs mot en Hybrid Runbook Worker vanligtvis hantera resurser på den lokala datorn sig själv eller mot resurser i den lokala miljön där det har distribuerats, samtidigt som runbooks i Azure Automation hanterar vanligtvis resurser i Azure-molnet.

När du skapar runbooks för att köra på en Hybrid Runbook Worker ska du redigera och testa runbooks på datorn som är värd för Hybrid worker. Värddatorn har alla PowerShell-moduler och nätverksåtkomst som du behöver hantera och komma åt lokala resurser. När en runbook har redigerats och testas på den Hybrid worker-datorn kan överföra du den till Azure Automation-miljön där den är tillgänglig för körning i Hybrid worker. Det är viktigt att veta att jobbet körs under det lokala systemkontot för windows eller ett särskilt användarkonto **nxautomation** på Linux, vilket kan medföra små skillnader vid redigering av runbooks för en Hybrid Runbook Worker som det ska vara beaktas.

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>Starta en runbook på Hybrid Runbook Worker

[Starta en Runbook i Azure Automation](automation-starting-a-runbook.md) beskriver olika metoder för att starta en runbook. Hybrid Runbook Worker lägger till en **RunOn** alternativ där du kan ange namnet på en Hybrid Runbook Worker-grupp. Om en grupp anges, hämtas runbook och kör något av arbetare i gruppen. Om det här alternativet inte anges, sedan körs den i Azure Automation som vanligt.

När du startar en runbook i Azure-portalen, visas en **körs på** där du kan välja alternativet **Azure** eller **Hybrid Worker**. Om du väljer **Hybrid Worker**, kan du markera gruppen från en listruta.

Använd den **RunOn** parametern. Du kan använda följande kommando för att starta en runbook med namnet Test-Runbook på en Hybrid Runbook Worker-grupp med namnet MyHybridGroup med hjälp av Windows PowerShell.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> Den **RunOn** parameter har lagts till i den **Start AzureAutomationRunbook** cmdlet i version 0.9.1 till och med Microsoft Azure PowerShell. Du bör [hämta den senaste versionen](https://azure.microsoft.com/downloads/) om du har en tidigare installerad. Du behöver bara installera den här versionen på en arbetsstation där du startar runbooken från PowerShell. Du behöver inte installera den på worker-datorn om du inte tänker starta runbooks från datorn ”

## <a name="runbook-permissions"></a>Runbook-behörigheter

Runbooks som körs på en Hybrid Runbook Worker kan inte använda samma metod som används vanligtvis för runbooks-autentisering mot Azure-resurser, eftersom de har åtkomst till resurser utanför Azure. Runbook kan antingen ge sin egen autentisering till lokala resurser och du kan ange ett RunAs-konto för att tillhandahålla en användarkontext för alla runbooks.

### <a name="runbook-authentication"></a>Runbook-autentisering

Som standard runbooks körs i kontexten för det lokala systemkontot för Windows och ett särskilt användarkonto **nxautomation** för Linux på den lokala datorn, så de måste autentisera sina egna till resurser som de har åtkomst till .

Du kan använda [Credential](automation-credentials.md) och [certifikat](automation-certificates.md) tillgångar i din runbook med cmdlets så att du kan ange autentiseringsuppgifter så att du kan autentisera till olika resurser. I följande exempel visas en del av en runbook som startar om en dator. Den hämtar autentiseringsuppgifter från en autentiseringsuppgifttillgång och namnet på datorn från en variabel tillgång och använder dessa värden med cmdleten Restart-Computer.

```azurepowershell-interactive
$Cred = Get-AzureRmAutomationCredential -ResourceGroupName "ResourceGroup01" -Name "MyCredential"
$Computer = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" -Name  "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Du kan också använda [InlineScript](automation-powershell-workflow.md#inlinescript), där du kan köra kodblock på en annan dator med autentiseringsuppgifter som anges av den [PSCredential gemensamma parametern](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="runas-account"></a>Kör som-konto

Som standard använder Hybrid Runbook Worker lokala System för Windows och ett särskilt användarkonto **nxautomation** för Linux för att köra runbooks. I stället för att runbooks som sin egen autentisering till lokala resurser, kan du ange en **RunAs** för en Hybrid worker-grupp. Du anger en [autentiseringstillgång](automation-credentials.md) som har åtkomst till lokala resurser och alla runbooks som körs under autentiseringsuppgifterna vid körning på en Hybrid Runbook Worker i gruppen.

Användarnamn för autentiseringsuppgifter måste vara i något av följande format:

* domän\användarnamn
* username@domain
* användarnamn (för konton som är lokalt på en lokal dator)

Använd följande procedur för att ange ett RunAs-konto för en Hybrid worker-grupp:

1. Skapa en [autentiseringstillgång](automation-credentials.md) med åtkomst till lokala resurser.
2. Öppna Automation-kontot i Azure-portalen.
3. Välj den **Hybrid Worker-grupper** panelen och välj sedan gruppen.
4. Välj **alla inställningar** och sedan **Hybrid worker-gruppinställningar**.
5. Ändra **kör som-** från **standard** till **anpassad**.
6. Välj autentiseringsuppgiften och klicka på **spara**.

### <a name="automation-run-as-account"></a>Automation kör som-konto

Som en del av automatisk build-process för att distribuera resurser i Azure, kan du behöva åtkomst till lokala system för att stödja en eller flera av stegen i sekvensen distribution. Du måste installera kör som-kontocertifikatet för att stödja autentisering mot Azure med hjälp av kör som-kontot.

Följande PowerShell-runbook *Export RunAsCertificateToHybridWorker*, exporterar det kör som-certifikatet från ditt Azure Automation-konto och hämtar och importerar den till lokala datorns certifikatarkiv på en Hybrid Worker som är anslutna till samma konto. När steget har slutförts verifierar arbetaren kan autentisera till Azure med hjälp av kör som-kontot.

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
> **Add-AzureRmAccount** är nu ett alias för **Connect-AzureRMAccount**. När sökningen biblioteket objekt, om du inte ser **Connect-AzureRMAccount**, du kan använda **Add-AzureRmAccount**, eller så kan du uppdatera dina moduler i ditt Automation-konto.

Spara den *Export RunAsCertificateToHybridWorker* runbook på datorn med en `.ps1` tillägget. Importera den till ditt Automation-konto och redigera runbooken, ändra värdet för variabeln `$Password` med ditt eget lösenord. Publicera och sedan kör en runbook som riktar in sig på Hybrid Worker-grupp som körs och autentisera runbooks med hjälp av kör som-kontot. Jobbströmmen rapporterar försöket att importera certifikatet till lokala datorns Arkiv och följer med flera rader beroende på hur många Automation-konton har definierats i din prenumeration och om autentiseringen har lyckats.

## <a name="job-behavior"></a>Jobbet beteende

Jobb hanteras något annorlunda på Hybrid Runbook Worker än de är när de körs på Azure sandbox-miljöer. En viktig skillnad är att det finns ingen gräns på varaktighet för jobb på Hybrid Runbook Worker. Runbooks som kördes i Azure sandbox-miljöer är begränsade till tre timmar på grund av [rättmätiga del](automation-runbook-execution.md#fair-share). Om du har en tidskrävande runbook som du vill se till att det kan stå emot möjliga omstart, till exempel om den dator som är värd för Hybrid worker startar om. Om värddatorn för Hybrid worker startar om datorn startar alla runbook-jobb som körs från början eller från den senaste kontrollpunkten för PowerShell Workflow-runbooks. Om en runbook-jobbet har startats om mer än 3 gånger sedan pausas den.

## <a name="run-only-signed-runbooks"></a>Kör endast signerade Runbooks

Hybrid Runbook Worker kan konfigureras för att köra endast signerade runbooks med viss konfiguration. I följande avsnitt beskrivs hur du ställer in din Hybrid Runbook Worker för att köra signerade runbooks och hur du registrerar dina runbooks.

> [!NOTE]
> När du har konfigurerat en Hybrid Runbook Worker för att köra endast signerade runbooks, runbook-flöden som har **inte** har registrerat kommer gick inte att köra på worker.

### <a name="create-signing-certificate"></a>Skapa signeringscertifikat

I följande exempel skapas ett självsignerat certifikat som kan användas för signering av runbooks. Exemplet skapar certifikatet och exporterar den. Certifikatet har importerats till Hybrid Runbook Worker senare. Tumavtrycket returneras, detta används senare för att referera till certifikatet.

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

### <a name="configure-the-hybrid-runbook-workers"></a>Konfigurera Hybrid Runbook Worker

Kopiera certifikatet som skapades för varje Hybrid Runbook Worker i en grupp. Kör följande skript för att importera certifikatet och konfigurera Hybrid Worker för att använda verifiera signaturen på runbooks.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>Registrera dina Runbooks med hjälp av certifikatet

Med Hybrid Runbook Worker-arbeten som konfigurerats för att använda endast signerade runbooks, måste du logga runbooks som ska användas på den Hybrid Runbook Worker. Använd följande exempel PowerShell för att registrera dina runbooks.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

När runbooken har signerats, måste de importeras till ditt Automation-konto och publiceras med signaturblocket. Läs hur man importerar runbooks i [importera en runbook från en fil till Azure Automation](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-azure-automation).

## <a name="troubleshoot"></a>Felsöka

Om dina runbooks inte slutförs, granska felsökningsguiden på [fel vid körning av runbook](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).

## <a name="next-steps"></a>Nästa steg

* Läs mer om olika metoder som kan användas för att starta en runbook i [starta en Runbook i Azure Automation](automation-starting-a-runbook.md).
* Information om olika procedurer för att arbeta med PowerShell och PowerShell Workflow-runbooks i Azure Automation med textredigeraren finns i [redigera en Runbook i Azure Automation](automation-edit-textual-runbook.md)
