---
title: Fel sökning av problem med Azure Automation önskad tillstånds konfiguration (DSC)
description: Den här artikeln innehåller information om hur du felsöker önskad tillstånds konfiguration (DSC)
services: automation
ms.service: automation
ms.subservice: ''
author: bobbytreed
ms.author: robreed
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b9d2dda589cc59be24b73ce16dcdcbbe79b31aef
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259164"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>Felsök önskad tillstånds konfiguration (DSC)

Den här artikeln innehåller information om fel sökning av problem med önskad tillstånds konfiguration (DSC).

## <a name="steps-to-troubleshoot-desired-state-configuration-dsc"></a>Steg för att felsöka önskad tillstånds konfiguration (DSC)

När du har problem med att kompilera eller distribuera konfigurationer i Azure-tillstånds konfiguration så är det några steg som hjälper dig att diagnostisera problemet.

1. **Se till att konfigurationen kompileras korrekt på den lokala datorn:**  Azure State Configuration bygger på PowerShell DSC. Du hittar dokumentationen för DSC-språket och syntaxen i [POWERSHELL DSC-dokumenten](https://docs.microsoft.com/en-us/powershell/scripting/overview).

   Genom att kompilera DSC-konfigurationen på den lokala datorn kan du identifiera och lösa vanliga fel, till exempel:

   - **Moduler som saknas**
   - **Syntaxfel**
   - **Logic-fel**

2. **Visa DSC-loggar på din nod:** Om konfigurationen kompileras korrekt, men Miss lyckas när den tillämpas på en nod, kan du hitta detaljerad information i loggarna. Information om var du hittar DSC-loggar finns i [var är DSC-händelseloggen](/powershell/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

   Dessutom kan [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) hjälpa dig att parsa detaljerad information från DSC-loggarna. Om du kontaktar supporten kräver de dessa loggar för att diagnostisera problemet.

   Du kan installera **xDscDiagnostics** på din lokala dator med hjälp av anvisningarna som finns under [installera den säkra versionen av modulen](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module).

   Om du vill installera **xDscDiagnostics** på din Azure-dator kan du använda [AZ VM Run-Command](/cli/azure/vm/run-command) eller [Invoke-AzVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand). Du kan också använda **kommando alternativet Kör** från portalen genom att följa stegen i [köra PowerShell-skript i din virtuella Windows-dator med kommandot kör](../../virtual-machines/windows/run-command.md).

   Information om hur du använder **xDscDiagnostics**finns i [använda xDscDiagnostics för att analysera DSC-loggar](/powershell/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs), samt xDscDiagnostics- [cmdletar](https://github.com/PowerShell/xDscDiagnostics#cmdlets).
3. **Se till att noderna och automation-arbetsytan har de moduler som krävs:** Önskad tillstånds konfiguration är beroende av moduler som är installerade på noden.  När du använder Azure Automation tillstånds konfiguration importerar du alla obligatoriska moduler till ditt Automation-konto med hjälp av stegen i [importera moduler](../shared-resources/modules.md#import-modules). Konfigurationer kan också vara beroende av vissa versioner av moduler.  Mer information finns i, [Felsöka moduler](shared-resources.md#modules).

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>Vanliga fel när du arbetar med önskad tillstånds konfiguration (DSC)

### <a name="unsupported-characters"></a>Situationen Det går inte att ta bort en konfiguration med specialtecken från portalen

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

### <a name="failed-to-register-agent"></a>Situationen Det gick inte att registrera DSC-agenten

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

### <a name="failed-not-found"></a>Situationen Noden har statusen misslyckades med felet "hittades inte"

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

### <a name="no-mof-files"></a>Situationen Inga nodkonfigurationer (MOF-filer) skapades när en konfiguration kompilerades

#### <a name="issue"></a>Problem

DSC-kompileringen pausas med felet:

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Orsak

När uttrycket efter **nodens** nyckelord i DSC-konfigurationen utvärderas till `$null`, skapas inga nodkonfigurationer.

#### <a name="resolution"></a>Lösning

Någon av följande lösningar löser problemet:

* Se till att uttrycket bredvid nyckelordet **Node** i konfigurations definitionen inte utvärderas till $null.
* Om du skickar ConfigurationData när du kompilerar konfigurationen kontrollerar du att du skickar de förväntade värdena som krävs av konfigurationen från [ConfigurationData](../automation-dsc-compile.md).

### <a name="dsc-in-progress"></a>Situationen DSC-nodens rapport har fastnat i status

#### <a name="issue"></a>Problem

DSC-agentens utdata:

```error
No instance found with given property values
```

#### <a name="cause"></a>Orsak

Du har uppgraderat din WMF-version och har skadat WMI.

#### <a name="resolution"></a>Lösning

Åtgärda problemet genom att följa anvisningarna i artikeln om [kända problem och begränsningar i DSC](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc) .

### <a name="issue-using-credential"></a>Situationen Det går inte att använda autentiseringsuppgifter i en DSC-konfiguration

#### <a name="issue"></a>Problem

Ditt DSC-kompilator jobb pausades med felet:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Orsak

Du har använt en autentiseringsuppgift i en konfiguration men angav inte rätt **ConfigurationData** för att ange **PSDscAllowPlainTextPassword** till true för varje nods konfiguration.

#### <a name="resolution"></a>Lösning

* Se till att skicka in rätt **ConfigurationData** för att ange **PSDscAllowPlainTextPassword** till true för varje nods konfiguration som nämns i konfigurationen. Mer information finns i [till gångar i Azure Automation DSC](../automation-dsc-compile.md#working-with-assets-in-azure-automation-during-compilation).

### <a name="failure-processing-extension"></a>Situationen Onboarding från DSC-tillägg, fel vid bearbetning av fel bearbetning

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

### <a name="failure-linux-temp-noexec"></a>Situationen Att tillämpa en konfiguration i Linux uppstår ett fel med ett allmänt fel

#### <a name="issue"></a>Problem

När du använder en konfiguration i Linux uppstår ett fel som innehåller felet:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

#### <a name="cause"></a>Orsak

Kunder har identifierat att om `/tmp` platsen är inställd på `noexec`kommer den aktuella versionen av DSC inte att tillämpa konfigurationer.

#### <a name="resolution"></a>Lösning

* Ta bort `/tmp` alternativet från platsen. `noexec`

### <a name="compilation-node-name-overlap"></a>Situationen Konfigurations namn för noder som överlappar kan resultera i dåliga versioner

#### <a name="issue"></a>Problem

Om ett enda konfigurations skript används för att generera flera nodkonfigurationer och några av nodkonfigurationer har ett namn som är en delmängd av andra, kan ett problem i Compilation-tjänsten leda till att du tilldelar fel konfiguration.  Detta inträffar bara när du använder ett enda skript för att generera konfigurationer med konfigurations data per nod, och endast när namnet överlappar inträffar i början av strängen.

Exempel: om ett enda konfigurations skript används för att generera konfigurationer som baseras på Node-data som skickas som en hash-fil med hjälp av cmdletar, och nodens data innehåller en server med namnet "Server" och "1server".

#### <a name="cause"></a>Orsak

Känt problem med Compilation service.

#### <a name="resolution"></a>Lösning

Den bästa lösningen är att kompilera lokalt eller i en CI/CD-pipeline och ladda upp MOF-filerna direkt till tjänsten.  Om kompileringen i tjänsten är ett krav, är nästa bästa lösning att dela upp Compilation-jobben så att det inte finns några överlappande i namn.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure-forumen](https://azure.microsoft.com/support/forums/)
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure-kontot för att förbättra kundtjänstupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Om du behöver mer hjälp kan du skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.
