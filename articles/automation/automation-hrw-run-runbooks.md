---
title: "Köra runbooks på Azure Automation Hybrid Runbook Worker | Microsoft Docs"
description: "Den här artikeln innehåller information om runbooks som körs på datorer i ditt lokala datacenter eller molnprovider med Hybrid Runbook Worker-rollen."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: 06227cda-f3d1-47fe-b3f8-436d2b9d81ee
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: magoedte
ms.openlocfilehash: d73bb33b4b330df803e140145ed63319af4a6733
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2018
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Runbooks som körs på en Hybrid Runbook Worker 
Det finns ingen skillnad i runbooks som körs i Azure Automation och de som körs på en Hybrid Runbook Worker-strukturen. Runbooks som du använder med varje troligen påtagligt skiljer sig dock eftersom runbooks inriktning på en Hybrid Runbook Worker vanligtvis hantera resurser på den lokala datorn sig själv eller mot resurser i den lokala miljön där den distribueras, medan runbooks i Azure Automation kan vanligtvis hantera resurser i Azure-molnet.

Du kan redigera en runbook för Hybrid Runbook Worker i Azure Automation, men du kan det vara svårt att testa runbook i redigeraren.  PowerShell-moduler som har åtkomst till lokala resurser kan inte installeras i Azure Automation-miljö i vilket fall testet misslyckas.  Om du installerar modulerna som krävs, körs runbook, men det kommer inte att få åtkomst till lokala resurser för en testning.

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>Starta en runbook på Hybrid Runbook Worker
[Starta en Runbook i Azure Automation](automation-starting-a-runbook.md) beskrivs olika metoder för att starta en runbook.  Runbook Worker-hybrid lägger till en **RunOn** alternativet där du kan ange namnet på en Hybrid Runbook Worker-gruppen.  Om en grupp anges hämtas runbook och kör av av de anställda i gruppen.  Om det här alternativet inte anges, sedan körs i Azure Automation som vanligt.

När du startar en runbook i Azure portal visas med en **körs på** där du kan välja alternativet **Azure** eller **Hybrid Worker**.  Om du väljer **Hybrid Worker**, kan du markera gruppen en listrutan.

Använd den **RunOn** parameter.  Du kan använda följande kommando för att starta en runbook med namnet Test-Runbook på en Hybrid Runbook Worker-grupp med namnet MyHybridGroup med Windows PowerShell.

    Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"

> [!NOTE]
> Den **RunOn** parametern har lagts till i den **Start AzureAutomationRunbook** cmdlet i version 0.9.1 av Microsoft Azure PowerShell.  Du bör [hämta den senaste versionen](https://azure.microsoft.com/downloads/) om du har en tidigare installerad.  Du behöver bara installera den här versionen på en arbetsstation där du startar runbook från Windows PowerShell.  Du behöver inte installera den på worker-datorn om du inte tänker starta runbooks från datorn.  Du kan för närvarande startar en runbook på en Hybrid Runbook Worker från en annan runbook eftersom detta kräver den senaste versionen av Azure Powershell installeras i ditt Automation-konto.  Den senaste versionen uppdateras automatiskt i Azure Automation och automatiskt flyttas fram till arbetare snart.
>
>

## <a name="runbook-permissions"></a>Runbook-behörigheter
Runbooks som körs på en Hybrid Runbook Worker kan inte använda samma metod som används vanligtvis för runbooks som autentiserar till Azure-resurser, eftersom de har åtkomst till resurser utanför Azure.  Runbook kan antingen ange sin egen autentisering till lokala resurser eller ange ett RunAs-konto för att ge en användarkontext för alla runbooks.

### <a name="runbook-authentication"></a>Runbook-autentisering
Som standard körs runbooks i kontexten för det lokala systemkontot på den lokala datorn, så de måste ange sina egna autentisering till resurser som de kommer åt.  

Du kan använda [autentiseringsuppgifter](http://msdn.microsoft.com/library/dn940015.aspx) och [certifikat](http://msdn.microsoft.com/library/dn940013.aspx) tillgångar i din runbook med cmdlets som låter dig ange autentiseringsuppgifter så att du kan autentisera till olika resurser.  I följande exempel visas en del av en runbook som startar om en dator.  Den hämtar autentiseringsuppgifter från en autentiseringstillgång och namnet på datorn från en variabel tillgång och använder dessa värden med cmdleten Restart-Computer.

    $Cred = Get-AzureRmAutomationCredential -ResourceGroupName "ResourceGroup01" -Name "MyCredential"
    $Computer = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" -Name  "ComputerName"

    Restart-Computer -ComputerName $Computer -Credential $Cred

Du kan också använda [InlineScript](automation-powershell-workflow.md#inlinescript), där du kan köra kodblock på en annan dator med autentiseringsuppgifter som anges av den [PSCredential gemensamma parametern](http://technet.microsoft.com/library/jj129719.aspx).

### <a name="runas-account"></a>RunAs-konto
I stället för med runbooks sin egen autentisering till lokala resurser, kan du ange en **RunAs** för Hybrid worker-gruppen.  Du anger en [autentiseringsuppgiftstillgång](automation-credentials.md) som har åtkomst till lokala resurser och alla runbooks som körs under autentiseringsuppgifterna vid körning på en Hybrid Runbook Worker i gruppen.  

Användarnamn för autentiseringsuppgifter måste vara i något av följande format:

* domain\username
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
Du kan behöva åtkomst till lokala system för att stödja en aktivitet eller en uppsättning steg i sekvensen distribution som del av din automatiserade build-processen för att distribuera resurser i Azure.  Du måste installera kör som-konto-certifikat för att stödja autentisering mot Azure med hjälp av kör som-kontot.  

Följande PowerShell-runbook *Export RunAsCertificateToHybridWorker*, exporterar kör som-certifikatet från Azure Automation-konto och laddar ned och importerar den till lokala datorns certifikatarkiv på en Hybrid Worker anslutna till samma konto.  När steget har slutförts verifierar arbetaren kan autentisera till Azure med Kör som-kontot.

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

    [OutputType([string])] 

    # Set the password used for this certificate
    $Password = "YourStrongPasswordForTheCert"

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
    
    Add-AzureRmAccount `
      -ServicePrincipal `
      -TenantId $RunAsConnection.TenantId `
      -ApplicationId $RunAsConnection.ApplicationId `
      -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

    Set-AzureRmContext -SubscriptionId $RunAsConnection.SubscriptionID | Write-Verbose

    # List automation accounts to confirm Azure Resource Manager calls are working
    Get-AzureRmAutomationAccount | Select-Object AutomationAccountName

Spara den *Export RunAsCertificateToHybridWorker* runbook på datorn med en `.ps1` tillägg.  Importera den till ditt Automation-konto och redigera runbook, ändra värdet för variabeln `$Password` med ditt eget lösenord.  Publicera och sedan köra runbook riktad Hybrid Worker-grupp som körs och autentisera runbooks med Kör som-kontot.  Jobbet dataströmmen rapporterar försök att importera certifikatet till lokala datorns Arkiv och följer med flera rader beroende på hur många Automation-konton har definierats i din prenumeration och om autentiseringen har lyckats.  

## <a name="troubleshooting-runbooks-on-hybrid-runbook-worker"></a>Felsökning av runbooks på Hybrid Runbook Worker
Loggfilerna lagras lokalt på varje worker-hybrid på C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes.  Worker-hybrid också registrerar fel och händelser i händelseloggen i Windows under **program och tjänster Logs\Microsoft-SMA\Operational**.  Runbooks som körs på worker-relaterade händelser skrivs till **program och tjänster Logs\Microsoft-Automation\Operational**.  Den **Microsoft SMA** loggen innehåller många fler händelser relaterade till runbook-jobbet pushas till arbetaren och bearbetning av runbook.  När den **Microsoft Automation** händelseloggen inte har många händelser med information som hjälper med felsökning av runbook-körningen, hittar du minst resultaten av runbook-jobbet.  

[Runbook-utdata och meddelanden om](automation-runbook-output-and-messages.md) skickas till Azure Automation från hybrid Worker-arbeten precis som runbook-jobb körs i molnet.  Du kan också aktivera utförlig och förlopp strömmar på samma sätt som för andra runbooks.  

Om dina runbooks inte har slutförts korrekt och jobbet sammanfattning visar statusen **pausad**, granska felsökningsartikel [Hybrid Runbook Worker: runbook-jobbet avslutas med statusen Pausad ](automation-troubleshooting-hybrid-runbook-worker.md#a-runbook-job-terminates-with-a-status-of-suspended).   

## <a name="next-steps"></a>Nästa steg
* Läs mer om olika metoder som kan användas för att starta en runbook i [starta en Runbook i Azure Automation](automation-starting-a-runbook.md).  
* Olika procedurer för att arbeta med PowerShell och PowerShell-arbetsflöde runbooks i Azure Automation med hjälp av textrepresentation editor finns [redigera en Runbook i Azure Automation](automation-edit-textual-runbook.md)
