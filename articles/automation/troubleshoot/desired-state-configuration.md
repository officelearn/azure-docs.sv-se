---
title: Felsöka Konfiguration av Azure Automation State (DSC)
description: Den här artikeln innehåller information om felsökning av DSC (Azure Automation State Configuration).
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4c045e110e21ed201278dcd84f38cb4a376ae8db
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679311"
---
# <a name="troubleshoot-issues-with-azure-automation-state-configuration-dsc"></a>Felsöka problem med DSC (Azure Automation State Configuration)

Den här artikeln innehåller information om felsökningsproblem som uppstår vid kompilering eller distribution av konfigurationer i DSC (Azure Automation State Configuration).

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](../automation-update-azure-modules.md).

## <a name="diagnosing-an-issue"></a>Diagnostisera ett problem

När du får ett kompilerings- eller distributionsfel för konfigurationen följer här några steg som hjälper dig att diagnostisera problemet.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Se till att konfigurationen har kompilerats på den lokala datorn

Azure Automation State Configuration (DSC) bygger på PowerShell Desired State Configuration (DSC). Du hittar dokumentationen för DSC-språket och syntaxen i [PowerShell DSC Docs](https://docs.microsoft.com/powershell/scripting/overview).

Genom att kompilera en DSC-konfiguration på den lokala datorn kan du identifiera och lösa vanliga fel, till exempel:

   - Moduler som saknas
   - Syntaxfel
   - Logiska fel

### <a name="2-view-dsc-logs-on-your-node"></a>2. Visa DSC-loggar på noden

Om konfigurationen kompileras, men misslyckas när den tillämpas på en nod, kan du hitta detaljerad information i DSC-loggarna. Information om var du hittar dessa loggar finns i [Var finns DSC-händelseloggarna](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

[XDscDiagnostics-modulen](https://github.com/PowerShell/xDscDiagnostics) kan hjälpa dig att tolka detaljerad information från DSC-loggarna. Om du kontaktar supporten kräver de att dessa loggar diagnostiserar problemet.

Du kan `xDscDiagnostics` installera modulen på din lokala dator med hjälp av instruktionerna i [Installera den stabila versionsmodulen](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module).

Om du `xDscDiagnostics` vill installera modulen på din [Azure-dator använder du Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0). Du kan också använda **kommandoalternativet Kör** i Azure-portalen genom att följa stegen i [Kör PowerShell-skript i din Windows VM med kommandot Kör](../../virtual-machines/windows/run-command.md).

Information om hur du använder **xDscDiagnostics**finns i [Använda xDscDiagnostics för att analysera DSC-loggar](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs). Se även [xDscDiagnostics Cmdlets](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Se till att noder och automationsarbetsytan har nödvändiga moduler

DSC beror på moduler som är installerade på noden. När du använder Azure Automation State Configuration importerar du alla nödvändiga moduler till ditt Automation-konto med hjälp av stegen i [Importmoduler](../shared-resources/modules.md#importing-modules). Konfigurationer kan också ha ett beroende av specifika versioner av moduler. Mer information finns i [Felsöka moduler](shared-resources.md#modules).

## <a name="scenario-a-configuration-with-special-characters-cannot-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Scenario: En konfiguration med specialtecken kan inte tas bort från portalen

### <a name="issue"></a>Problem

NÃ¤s nÃ¤ringar tillklädnings-borttagning av en DSC-konfiguration frÃ¥n portalen visas följande fel:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>Orsak

Det här felet är ett tillfälligt problem som är planerat att lösas.

### <a name="resolution"></a>Lösning

Använd cmdleten [Remove-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration?view=azps-3.7.0 för att ta bort konfigurationen.

### <a name="scenario-failed-to-register-dsc-agent"></a><a name="failed-to-register-agent"></a>Scenario: Det gick inte att registrera Dsc Agent

### <a name="issue"></a>Problem

När [Set-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) eller en annan DSC-cmdlet visas felet:

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

### <a name="cause"></a>Orsak

Det här felet orsakas normalt av en brandvägg, datorn som ligger bakom en proxyserver eller andra nätverksfel.

### <a name="resolution"></a>Lösning

Kontrollera att datorn har åtkomst till rätt slutpunkter för DSC och försök igen. En lista över portar och adresser som behövs finns i [nätverksplanering](../automation-dsc-overview.md#network-planning)

## <a name="a-nameunauthorizedscenario-status-reports-return-response-code-unauthorized"></a><a name="unauthorized"><a/>Scenario: Statusrapporter retursvarskod Obehörig

### <a name="issue"></a>Problem

När du registrerar en nod med Azure Automation State Configuration visas något av följande felmeddelanden:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Orsak

Det här problemet orsakas av ett felaktigt eller utgånget certifikat. Se [Certifikatets förfallodatum och omregistrering](../automation-dsc-onboarding.md#re-registering-a-node).

Det här problemet kan också orsakas av en proxykonfiguration som inte tillåter åtkomst till ***.azure-automation.net**. Mer information finns i [Konfiguration av privata nätverk](../automation-dsc-overview.md#network-planning). 

### <a name="resolution"></a>Lösning

Följ stegen nedan för att registrera den felaktiga DSC-noden.

Steg 1 - Avregistrera noden.

1. I Azure-portalen navigerar du till **Home** -> **Automation Accounts** -> (ditt Automation-konto) -> State Configuration **(DSC)**.
2. Välj **Noder**och klicka på den nod som har problem.
3. Klicka på **Avregistrera** om du vill avregistrera noden.

Steg 2 - Avinstallera DSC-tillägget från noden.

1. I Azure-portalen navigerar du till **Home** -> **Virtual Machine** -> (misslyckas nod) -> **tillägg**.
2. Välj **Microsoft.Powershell.DSC**, PowerShell DSC-tillägget.
3. Klicka på **Avinstallera** om du vill avinstallera tillägget.

Steg 3 - Ta bort alla felaktiga eller utgångna certifikat från noden.

Kör dessa kommandon på den felaktiga noden från en upphöjd PowerShell-prompt:

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

Steg 4 - Registrera den felaktiga noden igen.

1. I Azure-portalen navigerar du till **Home** -> **Automation Accounts** -> (ditt Automation-konto) -> **tillståndskonfiguration (DSC)**
2. Välj **Noder**.
3. Klicka på **Lägg till**.
4. Markera den felaktiga noden.
5. Klicka på **Anslut** och välj önskade alternativ.

## <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Scenario: Noden har status som inte hittats med felet "Hittades inte"

### <a name="issue"></a>Problem

Noden har en rapport med statusen Misslyckades och som innehåller felet:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

### <a name="cause"></a>Orsak

Det här felet uppstår vanligtvis när noden tilldelas ett konfigurationsnamn, till exempel **ABC**, i stället för ett MOF-filnamn (Nod configuration), till exempel **ABC. Webbserver**.

### <a name="resolution"></a>Lösning

* Kontrollera att du tilldelar noden med nodkonfigurationsnamnet och inte konfigurationsnamnet.
* Du kan tilldela en nodkonfiguration till en nod med Azure-portalen eller med en PowerShell-cmdlet.

  * I Azure-portalen navigerar du till **Home** -> **Automation Accounts** -> (ditt Automation-konto) -> **tillståndskonfiguration (DSC)** och väljer sedan en nod och klickar på **Tilldela nodkonfiguration**.
  * Använd [cmdlet set-azautomationDscNode.](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationDscNode?view=azps-3.7.0)

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>Scenario: Inga nodkonfigurationer (MOF-filer) producerades när en konfiguration kompilerades

### <a name="issue"></a>Problem

Ditt DSC-kompileringsjobb avbryts med felet:

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>Orsak

När uttrycket `Node` efter nyckelordet i DSC-konfigurationen utvärderas till `$null`skapas ingen nodkonfigurationer.

### <a name="resolution"></a>Lösning

Använd någon av följande lösningar för att åtgärda problemet:

* Kontrollera att uttrycket bredvid nyckelordet `Node` i konfigurationsdefinitionen inte utvärderas till Null.
* Om du skickar [ConfigurationData](../automation-dsc-compile.md) när du kompilerar konfigurationen kontrollerar du att du skickar de värden som konfigurationen förväntar sig av konfigurationsdata.

### <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>Scenario: DSC-nodrapporten fastnar i tillståndet Pågår

### <a name="issue"></a>Problem

DSC-agentutgångarna:

```error
No instance found with given property values
```

### <a name="cause"></a>Orsak

Du har uppgraderat WMF-versionen (Windows Management Framework) och har skadat WMI (Windows Management Instrumentation).

### <a name="resolution"></a>Lösning

Följ instruktionerna i [DSC kända problem och begränsningar](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc).

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Scenario: Det går inte att använda en autentiseringsdokument i en DSC-konfiguration

### <a name="issue"></a>Problem

Ditt DSC-kompileringsjobb avbröts med felet:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>Orsak

Du har använt en autentiseringsdokument i en konfiguration `ConfigurationData` men `PSDscAllowPlainTextPassword` inte angett korrekt för att ange true för varje nodkonfiguration.

### <a name="resolution"></a>Lösning

Se till att passera `ConfigurationData` i `PSDscAllowPlainTextPassword` rätt för att ställa in till true för varje nodkonfiguration som nämns i konfigurationen. Se [Kompilera DSC-konfigurationer i Azure Automation State Configuration](../automation-dsc-compile.md).

## <a name="scenario-failure-processing-extension-error-when-onboarding-from-dsc-extension"></a><a name="failure-processing-extension"></a>Scenario: felet "Fel bearbetningstillägg" vid introduktion från DSC-tillägg

### <a name="issue"></a>Problem

När du börjar använda ett DSC-tillägg uppstår ett fel som innehåller felet:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>Orsak

Det här felet uppstår vanligtvis när noden tilldelas ett nodkonfigurationsnamn som inte finns i tjänsten.

### <a name="resolution"></a>Lösning

* Kontrollera att du tilldelar noden med ett namn som exakt matchar namnet i tjänsten.
* Du kan välja att inte inkludera nodkonfigurationsnamnet, vilket resulterar i att noden ska finnas ombord men inte tilldela en nodkonfiguration.

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-using-powershell"></a><a name="cross-subscription"></a>Scenario: Felet "Ett eller flera fel uppstod" vid registrering av en nod med PowerShell

### <a name="issue"></a>Problem

När du registrerar en nod med [Register-AzAutomationDSCNode](https://docs.microsoft.com/powershell/module/az.automation/register-azautomationdscnode?view=azps-3.7.0) eller [Register-AzureRMAutomationDSCNode](https://docs.microsoft.com/powershell/module/azurerm.automation/register-azurermautomationdscnode?view=azurermps-6.13.0)visas följande fel:

```error
One or more errors occurred.
```

### <a name="cause"></a>Orsak

Det här felet uppstår när du försöker registrera en nod i en separat prenumeration från den som används av Automation-kontot.

### <a name="resolution"></a>Lösning

Behandla noden för korsabonnemang som om den har definierats för ett separat moln eller lokalt. Registrera noden med något av följande introduktionsalternativ:

* Windows - [Fysiska/virtuella Windows-datorer lokalt eller i ett annat moln än Azure/AWS](../automation-dsc-onboarding.md#onboarding-physicalvirtual-windows-machines).
* Linux - [Fysiska/virtuella Linux-datorer lokalt eller i ett annat moln än Azure](../automation-dsc-onboarding.md#onboarding-physicalvirtual-linux-machines).

## <a name="scenario-error-message---provisioning-failed"></a><a name="agent-has-a-problem"></a>Scenario: Felmeddelande - "Etablering misslyckades"

### <a name="issue"></a>Problem

När du registrerar en nod visas felet:

```error
Provisioning has failed
```

### <a name="cause"></a>Orsak

Det här meddelandet uppstår när det finns ett problem med anslutningen mellan noden och Azure.

### <a name="resolution"></a>Lösning

Ta reda på om noden finns i ett virtuellt privat nätverk (VPN) eller har andra problem med att ansluta till Azure. Se [Felsöka fel vid introduktionslösningar](onboarding.md).

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>Scenario: Fel med ett allmänt fel vid tillämpning av en konfiguration i Linux

### <a name="issue"></a>Problem

När en konfiguration tillämpas i Linux uppstår ett fel som innehåller felet:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>Orsak

Om **/tmp-platsen** är `noexec`inställd på kan den aktuella versionen av DSC inte tillämpa konfigurationer.

### <a name="resolution"></a>Lösning

Ta `noexec` bort alternativet från **/tmp-platsen.**

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-bad-release"></a><a name="compilation-node-name-overlap"></a>Scenario: Nodkonfigurationsnamn som överlappar kan resultera i felaktig utgivning

### <a name="issue"></a>Problem

När du använder ett enda konfigurationsskript för att generera flera nodkonfigurationer och vissa nodkonfigurationsnamn är delmängder av andra namn, kan kompileringstjänsten till tilldela fel konfiguration. Det här problemet uppstår bara när du använder ett enda skript för att generera konfigurationer med konfigurationsdata per nod, och endast när namnöverlappningen inträffar i början av strängen. Ett exempel är ett enda konfigurationsskript som används för att generera konfigurationer baserat på noddata som skickas som hashtable med cmdlets, och noddata innehåller servrar som heter **server** och **1server**.

### <a name="cause"></a>Orsak

Detta är ett känt problem med kompileringstjänsten.

### <a name="resolution"></a>Lösning

Den bästa lösningen är att kompilera lokalt eller i en CI / CD pipeline och ladda upp nod konfiguration MOF filer direkt till tjänsten. Om kompilering i tjänsten är ett krav är den näst bästa lösningen att dela upp kompileringsjobben så att det inte finns någon överlappning i namn.

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Scenario: Timeout-fel för gateway vid DSC-konfigurationsöverföring

#### <a name="issue"></a>Problem

Ett `GatewayTimeout` felmeddelande visas när du laddar upp en DSC-konfiguration. 

### <a name="cause"></a>Orsak

DSC-konfigurationer som tar lång tid att kompilera kan orsaka det här felet.

### <a name="resolution"></a>Lösning

Du kan göra din DSC-konfigurationer analysera `ModuleName` snabbare genom att uttryckligen inkludera parametern för alla [Import-DSCResource-anrop.](https://docs.microsoft.com/powershell/scripting/dsc/configurations/import-dscresource?view=powershell-5.1)

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet ovan eller inte kan lösa problemet kan du prova någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Azure Forum](https://azure.microsoft.com/support/forums/).
* Anslut [@AzureSupport](https://twitter.com/azuresupport)med , det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Arkivera en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj **Hämta support**.