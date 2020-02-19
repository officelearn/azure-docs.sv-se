---
title: Felsöka Azure Automation önskad tillstånds konfiguration (DSC)
description: Den här artikeln innehåller information om hur du felsöker önskad tillstånds konfiguration (DSC)
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: dcd0371d275c3a46fe9bf07c96516a2d0820abb7
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2020
ms.locfileid: "77430541"
---
# <a name="troubleshoot-issues-with-azure-automation-desired-state-configuration-dsc"></a>Felsöka problem med Azure Automation önskad tillstånds konfiguration (DSC)

Den här artikeln innehåller information om fel sökning av problem med önskad tillstånds konfiguration (DSC).

## <a name="diagnosing-an-issue"></a>Diagnostisera ett problem

När du har problem med att kompilera eller distribuera konfigurationer i Azure-tillstånds konfiguration, finns här några steg som hjälper dig att diagnostisera problemet.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. kontrol lera att konfigurationen kompileras på den lokala datorn

Azure State Configuration bygger på PowerShell DSC. Du hittar dokumentationen för DSC-språket och syntaxen i [POWERSHELL DSC-dokumenten](https://docs.microsoft.com/powershell/scripting/overview).

Genom att kompilera DSC-konfigurationen på den lokala datorn kan du identifiera och lösa vanliga fel, till exempel:

   - Moduler som saknas
   - Syntaxfel
   - Logic-fel

### <a name="2-view-dsc-logs-on-your-node"></a>2. Visa DSC-loggar på noden

Om konfigurationen kompileras korrekt, men inte fungerar när den används på en nod, kan du hitta detaljerad information i DSC-loggarna. Information om var du hittar dessa loggar finns i [var DSC-händelseloggen](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

[XDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) -modulen kan hjälpa dig att parsa detaljerad information från DSC-loggarna. Om du kontaktar supporten kräver de dessa loggar för att diagnostisera problemet.

Du kan installera xDscDiagnostics-modulen på den lokala datorn med hjälp av anvisningarna i [installera modulen stabil version](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module).

Om du vill installera xDscDiagnostics-modulen på din Azure-dator använder du [Invoke-AzVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand). Du kan också använda **kommando alternativet Kör** från portalen genom att följa stegen i [köra PowerShell-skript i din virtuella Windows-dator med kommandot kör](../../virtual-machines/windows/run-command.md).

Information om hur du använder xDscDiagnostics finns i [använda xDscDiagnostics för att analysera DSC-loggar](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs). Se även [xDscDiagnostics-cmdletar](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. kontrol lera att noderna och automation-arbetsytan har nödvändiga moduler

DSC är beroende av moduler som är installerade på noden. När du använder Azure Automation tillstånds konfiguration importerar du alla obligatoriska moduler till ditt Automation-konto med hjälp av stegen i [importera moduler](../shared-resources/modules.md#import-modules). Konfigurationer kan också vara beroende av vissa versioner av moduler. Mer information finns i [Felsöka moduler](shared-resources.md#modules).

## <a name="common-errors-when-working-with-dsc"></a>Vanliga fel när du arbetar med DSC

### <a name="unsupported-characters"></a>Scenario: en konfiguration med specialtecken kan inte tas bort från portalen

#### <a name="issue"></a>Problem

När du försöker ta bort en DSC-konfiguration från portalen visas följande fel:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>Orsak

Det här felet är ett tillfälligt problem som planeras att lösas.

#### <a name="resolution"></a>Lösning

* Använd cmdleten AZ "Remove-AzAutomationDscConfiguration" för att ta bort konfigurationen.
* Dokumentationen för den här cmdleten har ännu inte uppdaterats.  Tills dess kan du läsa dokumentationen för AzureRM-modulen.
  * [Remove-AzureRmAutomationDSCConfiguration](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="failed-to-register-agent"></a>Scenario: det gick inte att registrera DSC-agenten

#### <a name="issue"></a>Problem

När du försöker köra `Set-DscLocalConfigurationManager` eller en annan DSC-cmdlet får du ett fel meddelande:

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

Det här felet orsakas normalt av en brand vägg, datorn är bakom en proxyserver eller andra nätverks fel.

#### <a name="resolution"></a>Lösning

Kontrol lera att datorn har åtkomst till rätt slut punkter för Azure Automation DSC och försök igen. En lista över portar och adresser som behövs finns i [nätverks planering](../automation-dsc-overview.md#network-planning)

### <a name="a-nameunauthorizedascenario-status-reports-return-response-code-unauthorized"></a><a name="unauthorized"><a/>scenario: status rapporter returnerar svars koden "overifierad"

#### <a name="issue"></a>Problem

När du registrerar en nod med tillstånds konfiguration (DSC) får du ett av följande fel meddelanden:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Orsak

Det här problemet orsakas av ett felaktigt eller utgånget certifikat.  Mer information finns i [certifikatets förfallo datum och omregistrering](../automation-dsc-onboarding.md#certificate-expiration-and-re-registration).

### <a name="resolution"></a>Lösning

Följ stegen nedan för att registrera den felande DSC-noden igen.

Först avregistrerar du noden med hjälp av följande steg.

1. Från Azure Portal, under **start** -> **Automation-konton**– > {ditt Automation-konto}-> **tillstånds konfiguration (DSC)**
2. Klicka på noder och klicka på noden med problem.
3. Klicka på avregistrera om du vill avregistrera noden.

Sedan avinstallerar du DSC-tillägget från noden.

1. Från Azure Portal, under **start** -> **virtuell dator** -> {misslyckad Node}-> **tillägg**
2. Klicka på Microsoft. PowerShell. DSC.
3. Klicka på Avinstallera om du vill avinstallera PowerShell DSC-tillägget.

Ta sedan bort alla dåliga eller utgångna certifikat från noden.

Kör följande på noden som misslyckats från en upphöjd PowerShell-prompt:

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

Sedan kan du registrera den felaktiga noden på nytt med hjälp av följande steg.

1. Från Azure Portal, under **start** -> **Automation-konton** – > {ditt Automation-konto}-> **tillstånds konfiguration (DSC)**
2. Klicka på noder.
3. Klicka på knappen Lägg till.
4. Välj noden som misslyckats.
5. Klicka på Anslut och välj önskade alternativ.

### <a name="failed-not-found"></a>Scenario: noden har statusen misslyckades med fel meddelandet "hittades inte"

#### <a name="issue"></a>Problem

Noden har en rapport med **felaktig** status och innehåller felet:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Orsak

Det här felet uppstår vanligt vis när noden tilldelas ett konfigurations namn (till exempel ABC) i stället för ett konfigurations namn för noden (till exempel ABC. Webbserver).

#### <a name="resolution"></a>Lösning

* Se till att du tilldelar noden "konfigurations namn för nod" och inte "konfigurations namn".
* Du kan tilldela en nods konfiguration till en nod med Azure Portal eller med en PowerShell-cmdlet.

  * Om du vill tilldela en nods konfiguration till en nod med hjälp av Azure Portal öppnar du sidan **DSC-noder** och väljer sedan en nod och klickar på **tilldela konfigurations** knapp för nod.
  * Om du vill tilldela en nods konfiguration till en nod med PowerShell-cmdlet använder du **set-AzureRmAutomationDscNode-** cmdleten

### <a name="no-mof-files"></a>Scenario: inga nodkonfigurationer (MOF-filer) skapas när en konfiguration kompileras

#### <a name="issue"></a>Problem

DSC-kompileringen pausas med felet:

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Orsak

När uttrycket efter **nodens** nyckelord i DSC-konfigurationen utvärderas till `$null`skapas inga nodkonfigurationer.

#### <a name="resolution"></a>Lösning

Någon av följande lösningar löser problemet:

* Se till att uttrycket bredvid nyckelordet **Node** i konfigurations definitionen inte utvärderas till $null.
* Om du skickar ConfigurationData när du kompilerar konfigurationen kontrollerar du att du skickar de förväntade värdena som krävs av konfigurationen från [ConfigurationData](../automation-dsc-compile.md).

### <a name="dsc-in-progress"></a>Scenario: DSC-nodens rapport blir fast i status

#### <a name="issue"></a>Problem

DSC-agentens utdata:

```error
No instance found with given property values
```

#### <a name="cause"></a>Orsak

Du har uppgraderat din WMF-version och har skadat WMI.

#### <a name="resolution"></a>Lösning

Åtgärda problemet genom att följa anvisningarna i artikeln om [kända problem och begränsningar i DSC](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc) .

### <a name="issue-using-credential"></a>Scenario: det går inte att använda autentiseringsuppgifter i en DSC-konfiguration

#### <a name="issue"></a>Problem

Ditt DSC-kompilator jobb pausades med felet:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Orsak

Du har använt en autentiseringsuppgift i en konfiguration men angav inte rätt **ConfigurationData** för att ange **PSDscAllowPlainTextPassword** till true för varje nods konfiguration.

#### <a name="resolution"></a>Lösning

* Se till att skicka in rätt **ConfigurationData** för att ange **PSDscAllowPlainTextPassword** till true för varje nods konfiguration som nämns i konfigurationen. Mer information finns i [kompilera DSC-konfigurationer i Azure Automation tillstånds konfiguration](../automation-dsc-compile.md).

### <a name="failure-processing-extension"></a>Scenario: onboarding från DSC-tillägg, fel bearbetnings tillägg

#### <a name="issue"></a>Problem

När du använder DSC-tillägg uppstår ett fel som innehåller felet:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>Orsak

Det här felet uppstår vanligt vis när noden tilldelas ett konfigurations namn för noden som inte finns i tjänsten.

#### <a name="resolution"></a>Lösning

* Kontrol lera att du har tilldelats noden med ett konfigurations namn för noden som exakt matchar namnet i tjänsten.
* Du kan välja att inte inkludera konfigurations namnet för noden, vilket leder till att du registrerar noden men inte tilldelar en nods konfiguration

### <a name="cross-subscription"></a>Scenario: om du registrerar en nod med PowerShell returneras felet "ett eller flera fel inträffade"

#### <a name="issue"></a>Problem

När du registrerar en nod med `Register-AzAutomationDSCNode` eller `Register-AzureRMAutomationDSCNode`får du följande fel meddelande.

```error
One or more errors occurred.
```

#### <a name="cause"></a>Orsak

Det här felet uppstår när du försöker registrera en nod som finns i en separat prenumeration än Automation-kontot.

#### <a name="resolution"></a>Lösning

Behandlar noden över prenumerationer som om den finns i ett separat moln eller lokalt.

Följ stegen nedan för att registrera noden.

* Windows- [fysiska/virtuella Windows-datorer lokalt eller i ett annat moln än Azure/AWS](../automation-dsc-onboarding.md#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances).
* Linux- [fysiska/virtuella Linux-datorer lokalt eller i ett annat moln än Azure](../automation-dsc-onboarding.md#physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure).

### <a name="agent-has-a-problem"></a>Scenario: fel meddelande-"Det gick inte att utföra etableringen"

#### <a name="issue"></a>Problem

När du registrerar en nod visas felet:

```error
Provisioning has failed
```

#### <a name="cause"></a>Orsak

Det här meddelandet inträffar när det finns ett anslutnings problem mellan noden och Azure.

#### <a name="resolution"></a>Lösning

Ta reda på om din nod finns i ett privat virtuellt nätverk eller om det finns andra problem att ansluta till Azure.

Mer information finns i [Felsöka fel vid integrering av lösningar](onboarding.md).

### <a name="failure-linux-temp-noexec"></a>Scenario: att tillämpa en konfiguration i Linux uppstår ett fel med ett allmänt fel

#### <a name="issue"></a>Problem

När du använder en konfiguration i Linux uppstår ett fel som innehåller felet:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

#### <a name="cause"></a>Orsak

Kunder har identifierat att om `/tmp`s platsen är inställd på `noexec`kommer den aktuella versionen av DSC inte att tillämpa konfigurationer.

#### <a name="resolution"></a>Lösning

* Ta bort alternativet `noexec` från `/tmp`s platsen.

### <a name="compilation-node-name-overlap"></a>Scenario: konfigurations namn för noder som överlappar kan resultera i dålig version

#### <a name="issue"></a>Problem

Om ett enda konfigurations skript används för att generera flera nodkonfigurationer och några av nodkonfigurationer har ett namn som är en delmängd av andra, kan ett problem i Compilation-tjänsten leda till att du tilldelar fel konfiguration.  Detta inträffar bara när du använder ett enda skript för att generera konfigurationer med konfigurations data per nod, och endast när namnet överlappar inträffar i början av strängen.

Exempel: om ett enda konfigurations skript används för att generera konfigurationer som baseras på Node-data som skickas som en hash-fil med hjälp av cmdletar, och nodens data innehåller en server med namnet "Server" och "1server".

#### <a name="cause"></a>Orsak

Känt problem med Compilation service.

#### <a name="resolution"></a>Lösning

Den bästa lösningen är att kompilera lokalt eller i en CI/CD-pipeline och ladda upp MOF-filerna direkt till tjänsten.  Om kompileringen i tjänsten är ett krav, är nästa bästa lösning att dela upp Compilation-jobben så att det inte finns några överlappande i namn.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure-forum](https://azure.microsoft.com/support/forums/).
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure-kontot för att förbättra kundtjänstupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Om du behöver mer hjälp kan du skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.
