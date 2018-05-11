---
title: Köra runbooks på Azure Automation Hybrid Runbook Worker
description: Den här artikeln innehåller information om runbooks som körs på datorer i ditt lokala datacenter eller molnprovider med Hybrid Runbook Worker-rollen.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 75fc5336458670f4904584b3349f4fac7dc4ca68
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Runbooks som körs på en Hybrid Runbook Worker

Det finns ingen skillnad i runbooks som körs i Azure Automation och de som körs på en Hybrid Runbook Worker-strukturen. Runbooks som du använder med varje troligen påtagligt skiljer sig dock eftersom runbooks inriktning på en Hybrid Runbook Worker vanligtvis hantera resurser på den lokala datorn sig själv eller mot resurser i den lokala miljön där den distribueras, medan runbooks i Azure Automation kan vanligtvis hantera resurser i Azure-molnet.

När du skapar runbooks körs på en Hybrid Runbook Worker, bör du redigera och testa runbooks på den datorn som är värd för worker-hybriden. Värddatorn har alla PowerShell-moduler och nätverksåtkomst som du behöver hantera och komma åt lokala resurser. När en runbook har redigeras och testat på Hybrid worker-dator kan överföra du den till Azure Automation-miljön där den är tillgänglig för körning i worker-hybriden. Det är viktigt att veta att jobb ska köras under det lokala systemkontot för windows eller ett speciellt användarkonto **nxautomation** på Linux, som kan introducera skillnader vid redigering av runbooks för en Hybrid Runbook Worker ska den vara beaktas.

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>Starta en runbook på Hybrid Runbook Worker

[Starta en Runbook i Azure Automation](automation-starting-a-runbook.md) beskrivs olika metoder för att starta en runbook. Runbook Worker-hybrid lägger till en **RunOn** alternativet där du kan ange namnet på en Hybrid Runbook Worker-gruppen. Om en grupp anges hämtas runbook och kör någon av de anställda i gruppen. Om det här alternativet inte anges, sedan körs i Azure Automation som vanligt.

När du startar en runbook i Azure portal visas med en **körs på** där du kan välja alternativet **Azure** eller **Hybrid Worker**. Om du väljer **Hybrid Worker**, kan du markera gruppen en listrutan.

Använd den **RunOn** parameter. Du kan använda följande kommando för att starta en runbook med namnet Test-Runbook på en Hybrid Runbook Worker-grupp med namnet MyHybridGroup med Windows PowerShell.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> Den **RunOn** parametern har lagts till i den **Start AzureAutomationRunbook** cmdlet i version 0.9.1 av Microsoft Azure PowerShell. Du bör [hämta den senaste versionen](https://azure.microsoft.com/downloads/) om du har en tidigare installerad. Du behöver bara installera den här versionen på en arbetsstation där du startar runbook från PowerShell. Du behöver inte installera den på worker-datorn om du inte tänker starta runbooks från datorn ”

## <a name="runbook-permissions"></a>Runbook-behörigheter

Runbooks som körs på en Hybrid Runbook Worker kan inte använda samma metod som används vanligtvis för runbooks som autentiserar till Azure-resurser, eftersom de har åtkomst till resurser utanför Azure. Runbook kan antingen ange sin egen autentisering till lokala resurser eller ange ett RunAs-konto för att ge en användarkontext för alla runbooks.

### <a name="runbook-authentication"></a>Runbook-autentisering

Som standard runbooks körs i kontexten för det lokala systemkontot för Windows och ett speciellt användarkonto **nxautomation** för Linux på den lokala datorn, så de måste autentisera sina egna till resurser som de har åtkomst till .

Du kan använda [autentiseringsuppgifter](automation-credentials.md) och [certifikat](automation-certificates.md) tillgångar i din runbook med cmdlets som låter dig ange autentiseringsuppgifter så att du kan autentisera till olika resurser. I följande exempel visas en del av en runbook som startar om en dator. Den hämtar autentiseringsuppgifter från en autentiseringstillgång och namnet på datorn från en variabel tillgång och använder dessa värden med cmdleten Restart-Computer.

```azurepowershell-interactive
$Cred = Get-AzureRmAutomationCredential -ResourceGroupName "ResourceGroup01" -Name "MyCredential"
$Computer = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" -Name  "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Du kan också använda [InlineScript](automation-powershell-workflow.md#inlinescript), där du kan köra kodblock på en annan dator med autentiseringsuppgifter som anges av den [PSCredential gemensamma parametern](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="runas-account"></a>RunAs-konto

Som standard använder Hybrid Runbook Worker lokala System för Windows och ett speciellt användarkonto **nxautomation** för Linux att köra runbooks. I stället för med runbooks sin egen autentisering till lokala resurser, kan du ange en **RunAs** för Hybrid worker-gruppen. Du anger en [autentiseringsuppgiftstillgång](automation-credentials.md) som har åtkomst till lokala resurser och alla runbooks som körs under autentiseringsuppgifterna vid körning på en Hybrid Runbook Worker i gruppen.

Användarnamn för autentiseringsuppgifter måste vara i något av följande format:

* domän\användarnamn
* username@domain
* användarnamn (för konton som är lokala för lokal dator)

Använd följande procedur för att ange ett RunAs-konto för en Hybrid worker-gruppen:

1. Skapa en [autentiseringsuppgiftstillgång](automation-credentials.md) åtkomst till lokala resurser.
2. Öppna Automation-kontot i Azure-portalen.
3. Välj den **Hybrid Worker grupper** panelen och välj sedan gruppen.
4. Välj **alla inställningar** och sedan **Hybrid worker gruppinställningar**.
5. Ändra **kör som-** från **standard** till **anpassad**.
6. Välj autentiseringsuppgifter och klicka på **spara**.

### <a name="automation-run-as-account"></a>Automation-kör som-konto

Du kan behöva åtkomst till lokala system för att stödja en aktivitet eller en uppsättning steg i sekvensen distribution som del av din automatiserade build-processen för att distribuera resurser i Azure. Du måste installera kör som-konto-certifikat för att stödja autentisering mot Azure med hjälp av kör som-kontot.

Följande PowerShell-runbook *Export RunAsCertificateToHybridWorker*, exporterar kör som-certifikatet från Azure Automation-konto och laddar ned och importerar den till lokala datorns certifikatarkiv på en Hybrid Worker anslutna till samma konto. När steget har slutförts verifierar arbetaren kan autentisera till Azure med Kör som-kontot.

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

Spara den *Export RunAsCertificateToHybridWorker* runbook på datorn med en `.ps1` tillägg. Importera den till ditt Automation-konto och redigera runbook, ändra värdet för variabeln `$Password` med ditt eget lösenord. Publicera och sedan köra runbook riktad Hybrid Worker-grupp som körs och autentisera runbooks med Kör som-kontot. Jobbet dataströmmen rapporterar försök att importera certifikatet till lokala datorns Arkiv och följer med flera rader beroende på hur många Automation-konton har definierats i din prenumeration och om autentiseringen har lyckats.

## <a name="job-behavior"></a>Jobbet beteende

Jobb hanteras något annorlunda på Hybrid Runbook Workers än de är när de körs på Azure sandboxar. En viktigaste skillnaden är att det finns ingen gräns på varaktighet för jobbet på Hybrid Runbook Workers. Om du har en tidskrävande runbook vill du kontrollera att det är motståndskraftiga mot möjliga omstart, till exempel om datorn som är värd för worker-hybriden startas om. Om värddatorn Hybrid worker startas om, startar alla runbook-jobb som körs från början, eller från den senaste kontrollpunkten för PowerShell-arbetsflöde runbooks. Om en runbook-jobbet har startats om mer än 3 gånger är sedan den avbruten.

## <a name="troubleshooting-runbooks-on-hybrid-runbook-worker"></a>Felsökning av runbooks på Hybrid Runbook Worker

Loggfilerna lagras lokalt på varje worker-hybrid på C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Hybrid Worker-arbeten också registrera fel och händelser i händelseloggen i Windows under **program och tjänster Logs\Microsoft-SMA\Operational**. Runbooks som körs på worker-relaterade händelser skrivs till **program och tjänster Logs\Microsoft-Automation\Operational**. Den **Microsoft SMA** loggen innehåller många fler händelser relaterade till runbook-jobbet pushas till arbetaren och bearbetning av runbook. När den **Microsoft Automation** händelseloggen inte har många händelser med information som hjälper med felsökning av runbook-körningen, det innehåller resultatet av runbook-jobbet.

[Runbook-utdata och meddelanden om](automation-runbook-output-and-messages.md) skickas till Azure Automation från hybrid Worker-arbeten precis som runbook-jobb körs i molnet. Du kan också aktivera utförlig och förlopp strömmar på samma sätt som för andra runbooks.

Om dina runbooks inte har slutförts korrekt och jobbet sammanfattning visar statusen **pausad**, granska felsökningsartikel [Runbook Worker-Hybrid: runbook-jobbet avslutas med statusen Pausad](automation-troubleshooting-hybrid-runbook-worker.md#a-runbook-job-terminates-with-a-status-of-suspended).

## <a name="next-steps"></a>Nästa steg

* Läs mer om olika metoder som kan användas för att starta en runbook i [starta en Runbook i Azure Automation](automation-starting-a-runbook.md).
* Olika procedurer för att arbeta med PowerShell och PowerShell-arbetsflöde runbooks i Azure Automation med hjälp av textrepresentation editor finns [redigera en Runbook i Azure Automation](automation-edit-textual-runbook.md)
