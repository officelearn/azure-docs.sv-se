---
title: Felsöka fel onboarding uppdateringshantering, ändringsspårning och lager
description: Lär dig att felsöka onboarding med uppdateringshantering, ändringsspårning och lösningar för lager
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 044cb56b8991a1eb2dd6a1d35be621f2ffab3250
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064040"
---
# <a name="troubleshoot-errors-when-onboarding-solutions"></a>Felsöka när onboarding-lösningar

Fel kan inträffa när onboarding-lösningar som uppdateringshantering eller ändringsspårning och lager. Den här artikeln beskriver de olika fel som kan uppstå och hur du löser dem.

## <a name="general-errors"></a>Allmänt fel

### <a name="computer-grou-query-format-error"></a>Scenario: ComputerGroupQueryFormatError

#### <a name="issue"></a>Problem

Felet innebär att sparad sökning datorn grupp frågan som används för att rikta lösningen inte var korrekt formaterad. 

#### <a name="cause"></a>Orsak

Frågan kan ha ändrats eller det kan ha ändrats av systemet.

#### <a name="resolution"></a>Lösning

Du kan ta bort frågan för den här lösningen och reonboard lösning som återskapar frågan. Frågan kan hittas i din arbetsyta under **sparade sökningar**. Namnet på frågan är **MicrosoftDefaultComputerGroup**, och kategorin i frågan är namnet på den lösning som är associerade med den här frågan. Om flera lösningar är aktiverade på **MicrosoftDefaultComputerGroup** visas flera gånger under **sparade sökningar**.

### <a name="policy-violation"></a>Scenario: PolicyViolation

#### <a name="issue"></a>Problem

Felet innebär att distributionen misslyckades på grund av överträdelse av en eller flera principer.

#### <a name="cause"></a>Orsak 

En princip är på plats som blockerar åtgärden att slutföras.

#### <a name="resolution"></a>Lösning

För att distribuera lösningen, måste du överväga att ändra den angivna principen. Eftersom många olika typer av principer som kan definieras beror de ändringar som krävs på den princip som har överskridits. Till exempel om en princip har definierats i en resursgrupp som nekas behörighet att ändra innehållet i vissa typer av resurser i resursgruppen, kan du till exempel göra något av följande:

* Ta bort principen helt och hållet.
* Försök att publicera till en annan resursgrupp.
* Ändra principen av, till exempel:
  * Nytt inriktning principen till en viss resurs (till exempel ett visst Automation-konto).
  * Ändra uppsättningen av resurser som principen har konfigurerats för att neka.

Kontrollera meddelanden i det övre högra hörnet i Azure-portalen eller navigera till den resursgrupp som innehåller ditt automation-konto och välj **distributioner** under **inställningar** att visa den misslyckade distribution. Läs mer om Azure princip besök: [översikt över Azure princip](../../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fautomation%2ftoc.json).

## <a name="mma-extension-failures"></a>MMA tillägget fel

När du distribuerar en lösning för en mängd relaterade resurser har distribuerats. En av dessa resurser är tillägg för Microsoft Monitoring Agent eller OMS-Agent för Linux. Dessa är virtuella tillägg installeras av den virtuella datorn Gästagent som ansvarar för att kommunicera med konfigurerade Operations Management Suite (OMS) arbetsytan för senare samordning av hämtning av binärfiler och andra filer som du är onboarding lösningen är beroende av när den körs.
Du vanligtvis blir först medveten om MMA eller OMS-Agent för Linux-installationsfel från ett meddelande som visas i Meddelandehubben. Klicka på den meddelanden som ger mer information om det specifika felet. Navigering till resursen resursgrupper och sedan till elementet distributioner i det innehåller även information om distributionsfel som inträffat.
Installation av MMA eller OMS-Agent för Linux kan misslyckas av flera olika orsaker och steg för att åtgärda dessa fel varierar beroende på problemet. Specifika felsökningsanvisningar följer.

I följande avsnitt beskrivs olika problem som kan uppstå när onboarding som orsakar ett fel vid distribution av MMA tillägget.

### <a name="webclient-exception"></a>Scenario: Ett undantag uppstod under en WebClient-begäran

MMA tillägget på den virtuella datorn kan inte kommunicera med externa resurser och distributionen misslyckas.

#### <a name="issue"></a>Problem

Följande är exempel på felmeddelanden som returneras:

```
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>Orsak

Några möjliga orsaker till felet är:

* Det finns en proxyserver i den virtuella datorn, som endast tillåter specifika portar.

* Inställningen för en brandvägg har blockerat åtkomsten till de nödvändiga portarna och adresser.

#### <a name="resolution"></a>Lösning

Kontrollera att du har rätt portar och adresser som är öppna för kommunikation. En lista över portar och adresser, se [planerar nätverket](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="transient-environment-issue"></a>Scenario: Installera misslyckades på grund av problem tillfälligt miljön

Det gick inte att installera Microsoft Monitoring Agent-tillägget under distributionen på grund av en annan installation eller åtgärd som blockerar installationen

#### <a name="issue"></a>Problem

Följande är exempel på felmeddelanden som kan returneras:

```
The Microsoft Monitoring Agent failed to install on this machine. Please try to uninstall and reinstall the extension. If the issue persists, please contact support.
```

```
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1618'
```

```
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.2) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.2\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1601'
```

#### <a name="cause"></a>Orsak

Några möjliga orsaker till felet är:

* En annan installation pågår
* Systemet är utlöstes om du vill starta om när mallen distribueras

#### <a name="resolution"></a>Lösning

Det här felet är ett tillfälligt fel till sin natur. Gör om distributionen för att installera tillägget.

### <a name="installation-timeout"></a>Scenario: Tidsgräns för Installation

Installationen av tillägget MMA slutfördes inte på grund av en tidsgräns.

#### <a name="issue"></a>Problem

Följande är ett exempel på ett felmeddelande som kan returneras:

```
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Orsak

Det här felet beror på den virtuella datorn är hårt belastat under installationen.

### <a name="resolution"></a>Lösning

Försök att installera tillägget MMA när den virtuella datorn är en lägre belastning.

## <a name="next-steps"></a>Nästa steg

Om du inte fick ditt problem eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure-forumen](https://azure.microsoft.com/support/forums/)
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure-kontot för att förbättra kundtjänstupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Du kan arkivera en incident i Azure-supporten om du behöver mer hjälp. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **hämta stöder**.
