---
title: Felsöka DSC (Azure Automation Desired State Configuration)
description: Den här artikeln innehåller information om felsökning av dsc -konfiguration (Desired State Configuration)
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9f33dc9528d5f7043dda2c6fad207a9a51347a2b
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631479"
---
# <a name="troubleshoot-issues-with-azure-automation-desired-state-configuration-dsc"></a>Felsöka problem med DSC (Azure Automation Desired State Configuration)

Den här artikeln innehåller information om felsökningsproblem med DSC (Desired State Configuration).

## <a name="diagnosing-an-issue"></a>Diagnostisera ett problem

När du har fel när du kompilerar eller distribuerar konfigurationer i Azure State Configuration följer här några steg som hjälper dig att diagnostisera problemet.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Se till att konfigurationen har kompilerats på den lokala datorn

Azure State Configuration bygger på PowerShell DSC. Du hittar dokumentationen för DSC-språket och syntaxen i [PowerShell DSC Docs](https://docs.microsoft.com/powershell/scripting/overview).

Genom att kompilera DSC-konfigurationen på den lokala datorn kan du identifiera och lösa vanliga fel, till exempel:

   - Moduler som saknas
   - Syntaxfel
   - Logiska fel

### <a name="2-view-dsc-logs-on-your-node"></a>2. Visa DSC-loggar på noden

Om konfigurationen kompileras, men misslyckas när den tillämpas på en nod, kan du hitta detaljerad information i DSC-loggarna. Information om var du hittar dessa loggar finns i [Var finns DSC-händelseloggarna](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

[XDscDiagnostics-modulen](https://github.com/PowerShell/xDscDiagnostics) kan hjälpa dig att tolka detaljerad information från DSC-loggarna. Om du kontaktar supporten kräver de att dessa loggar diagnostiserar problemet.

Du kan installera xDscDiagnostics-modulen på din lokala dator med hjälp av instruktionerna i [Installera den stabila versionsmodulen](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module).

Om du vill installera xDscDiagnostics-modulen på din Azure-dator använder du [Invoke-AzVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand). Du kan också använda **kommandoalternativet Kör** från portalen genom att följa stegen i [Kör PowerShell-skript i windows VM med kommandot Kör](../../virtual-machines/windows/run-command.md).

Information om hur du använder xDscDiagnostics finns i [Använda xDscDiagnostics för att analysera DSC-loggar](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs). Se även [xDscDiagnostics Cmdlets](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Se till att noder och automationsarbetsytan har nödvändiga moduler

DSC beror på moduler som är installerade på noden. När du använder Azure Automation State Configuration importerar du alla nödvändiga moduler till ditt Automation-konto med hjälp av stegen i [Importmoduler](../shared-resources/modules.md#importing-modules). Konfigurationer kan också ha ett beroende av specifika versioner av moduler. Mer information finns i [Felsöka moduler](shared-resources.md#modules).

## <a name="common-errors-when-working-with-dsc"></a>Vanliga fel vid arbete med DSC

### <a name="scenario-a-configuration-with-special-characters-cannot-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Scenario: En konfiguration med specialtecken kan inte tas bort från portalen

#### <a name="issue"></a>Problem

NÃ¤s nÃ¤ringar tillklädnings-borttagning av en DSC-konfiguration frÃ¥n portalen visas följande fel:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>Orsak

Det här felet är ett tillfälligt problem som är planerat att lösas.

#### <a name="resolution"></a>Lösning

* Använd Az Cmdlet "Remove-AzAutomationDscConfiguration" för att ta bort konfigurationen.
* Dokumentationen för den här cmdleten har inte uppdaterats ännu.  Tills dess, se dokumentationen för AzureRM-modulen.
  * [Ta bort AzureRmAutomationDSCConfiguration](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="scenario-failed-to-register-dsc-agent"></a><a name="failed-to-register-agent"></a>Scenario: Det gick inte att registrera Dsc Agent

#### <a name="issue"></a>Problem

När du `Set-DscLocalConfigurationManager` försöker köra eller en annan DSC-cmdlet visas felet:

```error
Registration of the Dsc Agent with the server
https://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000 failed. The
underlying error is: Failed to register Dsc Agent with AgentId 00000000-0000-0000-0000-000000000000 with the server htt
ps://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000/Nodes(AgentId='00000000-0000-0000-0000-000000000000'). .
    + CategoryInfo          : InvalidResult: (root/Microsoft/...gurationManager:String) [], CimException
    + FullyQualifiedErrorId : RegisterDscAgentCommandFailed,Microsoft.PowerShell.DesiredStateConfiguration.Commands.Re
   gisterDscAgentCommand
    + PSComputerName        : <computerName>
```

#### <a name="cause"></a>Orsak

Det här felet orsakas normalt av en brandvägg, datorn som ligger bakom en proxyserver eller andra nätverksfel.

#### <a name="resolution"></a>Lösning

Kontrollera att datorn har åtkomst till rätt slutpunkter för Azure Automation DSC och försök igen. En lista över portar och adresser som behövs finns i [nätverksplanering](../automation-dsc-overview.md#network-planning)

### <a name="a-nameunauthorizedscenario-status-reports-return-response-code-unauthorized"></a><a name="unauthorized"><a/>Scenario: Statusrapporter retursvarskod "Obehörig"

#### <a name="issue"></a>Problem

När du registrerar en nod med tillståndskonfiguration (DSC) visas något av följande felmeddelanden:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Orsak

Det här problemet orsakas av ett felaktigt eller utgånget certifikat.  Mer information finns i [Certifikatets förfallodatum och omregistrering](../automation-dsc-onboarding.md#re-registering-a-node).

### <a name="resolution"></a>Lösning

Följ stegen nedan för att registrera den felaktiga DSC-noden.

Avmarkera först noden med hjälp av följande steg.

1. Från Azure-portalen under **Home** -> **Automation-konton**-> {Ditt Automation-konto} -> **Tillståndskonfiguration (DSC)**
2. Klicka på "Noder" och klicka på noden med problem.
3. Klicka på "Avregistrera" om du vill avregistrera noden.

För det andra avinstallerar du DSC-tillägget från noden.

1. Från Azure-portalen under**Virtuell dator** för **hemmet** -> -> {Misslyckad nod} -> **tillägg**
2. Klicka på "Microsoft.Powershell.DSC".
3. Klicka på "Avinstallera", om du vill avinstallera PowerShell DSC-tillägget.

För det tredje tar du bort alla felaktiga eller utgångna certifikat från noden.

På den felaktiga noden från en upphöjd Powershell-prompt kör du följande:

```powershell
$certs = @()
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC"}
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC-OaaS Client Authentication"}
$certs += dir cert:\localmachine\CA | ?{$_.subject -like "CN=AzureDSCExtension*"}
"";"== DSC Certificates found: " + $certs.Count
$certs | FL ThumbPrint,FriendlyName,Subject
If (($certs.Count) -gt 0)
{ 
    ForEach ($Cert in $certs) 
    {
        RD -LiteralPath ($Cert.Pspath) 
    }
}
```

Slutligen, registrera den felaktiga noden med hjälp av följande steg.

1. Från Azure-portalen under **Home** -> **Automation-konton** -> {Ditt Automation-konto} -> **Tillståndskonfiguration (DSC)**
2. Klicka på "Noder".
3. Klicka på knappen "Lägg till".
4. Markera den felaktiga noden.
5. Klicka på "Anslut" och välj önskade alternativ.

### <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Scenario: Noden har status som inte hittats med felet "Hittades inte"

#### <a name="issue"></a>Problem

Noden har en rapport med **statusen Misslyckades** och som innehåller felet:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Orsak

Det här felet uppstår vanligtvis när noden tilldelas ett konfigurationsnamn (till exempel ABC) i stället för ett nodkonfigurationsnamn (till exempel ABC. webbserver).

#### <a name="resolution"></a>Lösning

* Kontrollera att du tilldelar noden med "nodkonfigurationsnamn" och inte "konfigurationsnamnet".
* Du kan tilldela en nodkonfiguration till en nod med Azure-portalen eller med en PowerShell-cmdlet.

  * Om du vill tilldela en nodkonfiguration till en nod med Azure-portalen öppnar du sidan **DSC-noder** och väljer sedan en nod och klickar på Knappen **Tilldela nodkonfiguration.**
  * Om du vill tilldela en nodkonfiguration till en nod med PowerShell-cmdlet använder du **Cmdlet Set-AzureRmAutomationDscNode**

### <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-is-compiled"></a><a name="no-mof-files"></a>Scenario: Inga nodkonfigurationer (MOF-filer) producerades när en konfiguration kompileras

#### <a name="issue"></a>Problem

Ditt DSC-kompileringsjobb avbryts med felet:

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Orsak

När uttrycket efter nyckelordet **Node** i DSC-konfigurationen utvärderas till `$null`skapas inga nodkonfigurationer.

#### <a name="resolution"></a>Lösning

I någon av följande lösningar löser problemet:

* Kontrollera att uttrycket bredvid nyckelordet **Node** i konfigurationsdefinitionen inte utvärderas för att $null.
* Om du skickar ConfigurationData när du kompilerar konfigurationen kontrollerar du att du skickar de förväntade värden som konfigurationen kräver från [ConfigurationData](../automation-dsc-compile.md).

### <a name="scenario-the-dsc-node-report-becomes-stuck-in-progress-state"></a><a name="dsc-in-progress"></a>Scenario: DSC-nodrapporten fastnar i "pågår"-tillstånd

#### <a name="issue"></a>Problem

DSC-agentutgångarna:

```error
No instance found with given property values
```

#### <a name="cause"></a>Orsak

Du har uppgraderat din WMF-version och har skadat WMI.

#### <a name="resolution"></a>Lösning

För att åtgärda problemet, följ instruktionerna i [DSC kända problem och begränsningar](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc) artikeln.

### <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Scenario: Det går inte att använda en autentiseringsdokument i en DSC-konfiguration

#### <a name="issue"></a>Problem

Ditt DSC-kompileringsjobb avbröts med felet:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Orsak

Du har använt en autentiseringsdokument i en konfiguration men inte gett korrekt **ConfigurationData** för att ställa **IN PSDscAllowPlainTextPassword** till true för varje nodkonfiguration.

#### <a name="resolution"></a>Lösning

* Se till att passera i rätt **ConfigurationData** för att ställa **IN PSDscAllowPlainTextPassword** till true för varje nodkonfiguration som nämns i konfigurationen. Mer information finns [i Kompilera DSC-konfigurationer i Azure Automation State Configuration](../automation-dsc-compile.md).

### <a name="scenario-onboarding-from-dsc-extension-failure-processing-extension-error"></a><a name="failure-processing-extension"></a>Scenario: Onboarding från dsc-tillägg, fel för tillägget "Fel bearbetning"

#### <a name="issue"></a>Problem

Vid introduktion med DSC-tillägg uppstår ett fel som innehåller felet:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>Orsak

Det här felet uppstår vanligtvis när noden tilldelas ett nodkonfigurationsnamn som inte finns i tjänsten.

#### <a name="resolution"></a>Lösning

* Kontrollera att du tilldelar noden med ett nodkonfigurationsnamn som exakt matchar namnet i tjänsten.
* Du kan välja att inte inkludera nodkonfigurationsnamnet, vilket resulterar i att noden som finns ombord men inte tilldelar en nodkonfiguration

### <a name="scenario-registering-a-node-with-powershell-returns-the-error-one-or-more-errors-occurred"></a><a name="cross-subscription"></a>Scenario: Om du registrerar en nod med PowerShell returneras felet "Ett eller flera fel uppstod"

#### <a name="issue"></a>Problem

När du registrerar en `Register-AzAutomationDSCNode` `Register-AzureRMAutomationDSCNode`nod med eller visas följande fel.

```error
One or more errors occurred.
```

#### <a name="cause"></a>Orsak

Det här felet uppstår när du försöker registrera en nod som finns i en separat prenumeration än Automation-kontot.

#### <a name="resolution"></a>Lösning

Behandla noden för korsabonnemang som om den finns i ett separat moln eller lokalt.

Följ stegen nedan för att registrera noden.

* Windows - [Fysiska/virtuella Windows-datorer lokalt eller i ett annat moln än Azure/AWS](../automation-dsc-onboarding.md#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances).
* Linux - [Fysiska/virtuella Linux-datorer lokalt eller i ett annat moln än Azure](../automation-dsc-onboarding.md#onboarding-physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure).

### <a name="scenario-error-message---provisioning-failed"></a><a name="agent-has-a-problem"></a>Scenario: Felmeddelande - "Etablering misslyckades"

#### <a name="issue"></a>Problem

När du registrerar en nod visas felet:

```error
Provisioning has failed
```

#### <a name="cause"></a>Orsak

Det här meddelandet uppstår när det finns ett anslutningsproblem mellan noden och Azure.

#### <a name="resolution"></a>Lösning

Ta reda på om noden finns i ett privat virtuellt nätverk eller har andra problem med att ansluta till Azure.

Mer information finns i [Felsöka fel vid introduktionslösningar](onboarding.md).

### <a name="scenario-applying-a-configuration-in-linux-a-failure-occurs-with-a-general-error"></a><a name="failure-linux-temp-noexec"></a>Scenario: Om du använder en konfiguration i Linux uppstår ett fel med ett allmänt fel

#### <a name="issue"></a>Problem

När en konfiguration tillämpas i Linux uppstår ett fel som innehåller felet:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

#### <a name="cause"></a>Orsak

Kunder har identifierat `/tmp` att om `noexec`platsen är inställd på kommer den aktuella versionen av DSC inte att tillämpa konfigurationer.

#### <a name="resolution"></a>Lösning

* Ta `noexec` bort alternativet `/tmp` från platsen.

### <a name="scenario-node-configuration-names-that-overlap-could-result-in-bad-release"></a><a name="compilation-node-name-overlap"></a>Scenario: Nodkonfigurationsnamn som överlappar kan resultera i felaktig utgivning

#### <a name="issue"></a>Problem

Om ett enda konfigurationsskript används för att generera flera nodkonfigurationer och vissa nodkonfigurationer har ett namn som är en delmängd av andra, kan ett problem i kompileringstjänsten resultera i att fel konfiguration tilldelas.  Detta inträffar endast när du använder ett enda skript för att generera konfigurationer med konfigurationsdata per nod, och endast när namnöverlappningen inträffar i början av strängen.

Exempel, om ett enda konfigurationsskript används för att generera konfigurationer baserat på noddata som skickas som hashtable med cmdlets, och noddata innehåller en server med namnet "server" och "1server".

#### <a name="cause"></a>Orsak

Känt problem med kompileringstjänsten.

#### <a name="resolution"></a>Lösning

Den bästa lösningen skulle vara att kompilera lokalt eller i en CI / CD pipeline och ladda upp MOF filer direkt till tjänsten.  Om kompilering i tjänsten är ett krav, skulle den näst bästa lösningen vara att dela upp kompileringsjobben så att det inte finns någon överlappning i namn.

### <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Scenario: Timeout-fel för gateway vid DSC-konfigurationsöverföring

#### <a name="issue"></a>Problem

Ett `GatewayTimeout` felmeddelande visas när du laddar upp en DSC-konfiguration. 

#### <a name="cause"></a>Orsak

DSC-konfigurationer som tar lång tid att kompilera kan orsaka det här felet.

#### <a name="resolution"></a>Lösning

Du kan göra din DSC-konfigurationer analysera `ModuleName` snabbare genom `Import-DscResource` att uttryckligen inkludera parametern för alla anrop. Mer information finns i [Använda Import-DSCResource](https://docs.microsoft.com/powershell/scripting/dsc/configurations/import-dscresource?view=powershell-5.1).

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Forum](https://azure.microsoft.com/support/forums/).
* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Om du behöver mer hjälp kan du lämna in en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj **Hämta support**.
