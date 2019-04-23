---
title: Felsöka fel onboarding uppdateringshantering, ändringsspårning och inventering
description: Lär dig att felsöka onboarding med uppdateringshantering, ändringsspårning och inventering lösningar
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/20/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: eaafee304f606ae4d511a6cea1824c26db838635
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59793154"
---
# <a name="troubleshoot-errors-when-onboarding-solutions"></a>Felsöka fel när onboarding-lösningar

Fel kan uppstå när onboarding-lösningar som uppdateringshantering eller ändringsspårning och inventering. Den här artikeln beskriver de olika fel som kan uppstå och hur du löser dem.

## <a name="general-errors"></a>Allmänt fel

### <a name="missing-write-permissions"></a>Scenario: Onboarding misslyckas med meddelandet – Det går inte att aktivera lösningen

#### <a name="issue"></a>Problem

Du får något av följande meddelanden när du försöker att publicera en virtuell dator till en lösning:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Orsak

Det här felet orsakas av felaktig eller saknas behörigheter på den virtuella datorn, arbetsytan eller för användaren.

#### <a name="resolution"></a>Lösning

Se till att du har tillräckliga behörigheter för att publicera den virtuella datorn. Granska den [behörigheter som krävs för att publicera datorer](../automation-role-based-access-control.md#onboarding) och försök att publicera lösningen igen. Om du får felet `The solution cannot be enabled on this VM because the permission to read the workspace is missing`, kontrollera att du har den `Microsoft.OperationalInsights/workspaces/read` behörighet för att kunna hitta om den virtuella datorn har publicerats i en arbetsyta.

### <a name="computer-group-query-format-error"></a>Scenario: ComputerGroupQueryFormatError

#### <a name="issue"></a>Problem

Den här felkoden innebär att sparad sökning dator grupp frågan används för att rikta lösningen inte har formaterats korrekt. 

#### <a name="cause"></a>Orsak

Frågan kan ha ändrats eller den kan ha ändrats av systemet.

#### <a name="resolution"></a>Lösning

Du kan ta bort frågan för den här lösningen och reonboard lösning som återskapar frågan. Frågan finns i din arbetsyta under **sparade sökningar**. Namnet på frågan är **MicrosoftDefaultComputerGroup**, och kategorin för frågan är namnet på den lösning som är associerad med den här frågan. Om flera lösningar är aktiverade i **MicrosoftDefaultComputerGroup** visas flera gånger under **sparade sökningar**.

### <a name="policy-violation"></a>Scenario: PolicyViolation

#### <a name="issue"></a>Problem

Den här felkoden betyder att distributionen misslyckades på grund av överträdelse av en eller flera principer.

#### <a name="cause"></a>Orsak 

En princip är på plats som blockerar åtgärden att slutföras.

#### <a name="resolution"></a>Lösning

För att kunna distribuera lösningen, måste du överväga att ändra den angivna principen. Eftersom det finns många olika typer av principer som kan definieras, beror specifika ändringar som krävs på den princip som har överskridits. Till exempel om en princip har definierats i en resursgrupp som nekas behörighet att ändra innehållet i vissa typer av resurser i resursgruppen, kan du till exempel göra något av följande:

* Ta bort principen helt och hållet.
* Försök att publicera till en annan resursgrupp.
* Ändra principen, av, till exempel:
  * Nytt inriktning principen till en specifik resurs (till exempel ett specifikt Automation-konto).
  * Ändra uppsättningen av resurser principen har konfigurerats för att neka.

Kontrollera meddelanden i det övre högra hörnet i Azure-portalen eller navigera till resursgruppen som innehåller ditt automation-konto och välj **distributioner** under **inställningar** att visa den distribution. Om du vill veta finns mer om Azure Policy: [Översikt över Azure Policy](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

## <a name="mma-extension-failures"></a>MMA-datortillägg

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

När du distribuerar en lösning distribueras en mängd relaterade resurser. En av dessa resurser är tillägget Microsoft Monitoring Agent eller Log Analytics-agenten för Linux. Dessa är tillägg till virtuella datorer installeras med den virtuella datorn Gästagent som ansvarar för att kommunicera med den konfigurerade Log Analytics-arbetsytan i syfte att senare samordning av hämtning av binärfiler och andra filer som den lösningen är du onboarding beror på när den körs.
Du vanligtvis får först kännedom om MMA eller Log Analytics-agenten för Linux-installationsfel från ett meddelande som visas i Meddelandehubben. När du klickar på som meddelanden ger ytterligare information om det specifika felet. Navigering till resursen i resursgrupper och sedan till elementet distributioner i den innehåller också information om distributionsfel som uppstått.
Installation av MMA eller Log Analytics-agenten för Linux kan misslyckas av olika orsaker och stegen för att åtgärda de här felen varierar beroende på problemet. Specifika felsökningsanvisningar följer.

I följande avsnitt beskrivs olika problem som du kan stöta på när onboarding som orsakar ett fel vid distribution av MMA-tillägget.

### <a name="webclient-exception"></a>Scenario: Ett undantag uppstod under en WebClient-begäran

MMA-tillägget på den virtuella datorn kan inte kommunicera med externa resurser och distributionen misslyckas.

#### <a name="issue"></a>Problem

Här följer några exempel på felmeddelanden som returneras:

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>Orsak

Några möjliga orsaker till felet är:

* Det finns en proxyserver i den virtuella datorn, som endast tillåter specifika portar.

* En brandväggsinställning har blockerat åtkomsten till de nödvändiga portarna och adresser.

#### <a name="resolution"></a>Lösning

Kontrollera att du har rätt portar och adresser som är öppna för kommunikation. En lista över portar och adresser finns i [planerar nätverket](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="transient-environment-issue"></a>Scenario: Installationen misslyckades på grund av ett tillfälligt miljö-problem

Installationen av tillägget Microsoft Monitoring Agent misslyckades under distributionen på grund av en annan installation eller åtgärd som blockerar installationen

#### <a name="issue"></a>Problem

Följande är exempel på felmeddelanden som kan returneras:

```error
The Microsoft Monitoring Agent failed to install on this machine. Please try to uninstall and reinstall the extension. If the issue persists, please contact support.
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1618'
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.2) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.2\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1601'
```

#### <a name="cause"></a>Orsak

Några möjliga orsaker till felet är:

* En annan installation pågår
* Systemet utlöses om du vill starta om när mallen distribueras

#### <a name="resolution"></a>Lösning

Det här felet är ett tillfälligt fel sin natur. Gör om distributionen för att installera tillägget.

### <a name="installation-timeout"></a>Scenario: Tidsgräns för installation

Installationen av MMA-tillägget kunde inte slutföra på grund av en tidsgräns.

#### <a name="issue"></a>Problem

I följande exempel har ett felmeddelande som kan returneras:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Orsak

Det här felet beror på att den virtuella datorn är hårt belastat under installationen.

### <a name="resolution"></a>Lösning

Försök att installera tillägget MMA när den virtuella datorn har en lägre belastning.

## <a name="next-steps"></a>Nästa steg

Om du inte ser ditt problem eller inte kan lösa problemet besöker du någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Azure-forumen](https://azure.microsoft.com/support/forums/)
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure-kontot för att förbättra kundtjänstupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Om du behöver mer hjälp kan öppna du en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **hämta stöder**.
