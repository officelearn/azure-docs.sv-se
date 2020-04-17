---
title: Felsöka lösningar för Azure Automation-hantering
description: Lär dig hur du felsöker inbyggda fel med lösningarna Uppdateringshantering, ändringsspårning och lager
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: ae359e5210a9a11c33dd3ff9b474e28aa2548c57
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536972"
---
# <a name="troubleshoot-errors-when-onboarding-update-management-change-tracking-and-inventory"></a>Felsöka fel vid introduktion av uppdateringshantering, ändringsspårning och lager

Du kan stöta på fel när du ar inbyggt lösningar som uppdateringshantering eller ändringsspårning och lager. I den här artikeln beskrivs de olika fel som kan uppstå och hur du löser dem.

## <a name="known-issues"></a>Kända problem

### <a name="scenario-renaming-a-registered-node-requires-unregister--register-again"></a><a name="node-rename"></a>Scenario: Byta namn på en registrerad nod kräver avregistrera / registrera igen

#### <a name="issue"></a>Problem

En nod registreras i Azure Automation och sedan ändras operativsystemets datornamn.  Rapporter från noden fortsätter att visas med det ursprungliga namnet.

#### <a name="cause"></a>Orsak

Att byta namn på registrerade noder uppdaterar inte nodnamnet i Azure Automation.

#### <a name="resolution"></a>Lösning

Avregistrera noden från Azure Automation State Configuration och registrera den sedan igen.  Rapporter som publicerats till tjänsten före den tiden kommer inte längre att vara tillgängliga.


### <a name="scenario-re-signing-certificates-via-https-proxy-is-not-supported"></a><a name="resigning-cert"></a>Scenario: Omsignering av certifikat via https-proxy stöds inte

#### <a name="issue"></a>Problem

Kunder har rapporterat att när de ansluter via en proxylösning som avslutar https-trafik och sedan krypterar om trafiken med ett nytt certifikat, tillåter tjänsten inte anslutningen.

#### <a name="cause"></a>Orsak

Azure Automation stöder inte omsignering av certifikat som används för att kryptera trafik.

#### <a name="resolution"></a>Lösning

Det finns ingen lösning på problemet.

## <a name="general-errors"></a>Allmänna fel

### <a name="scenario-onboarding-fails-with-the-message---the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>Scenario: Introduktionsstart misslyckas med meddelandet - Lösningen kan inte aktiveras

#### <a name="issue"></a>Problem

Du får något av följande meddelanden när du försöker gå in på en virtuell dator till en lösning:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Orsak

Det här felet orsakas av felaktiga eller saknade behörigheter på den virtuella datorn, arbetsytan eller för användaren.

#### <a name="resolution"></a>Lösning

Kontrollera att du har rätt behörighet att gå in på den virtuella datorn. Granska de [behörigheter som behövs för att komma in på datorer](../automation-role-based-access-control.md#onboarding-permissions) och försök att gå in på lösningen igen. Om felet visas `The solution cannot be enabled on this VM because the permission to read the workspace is missing`kontrollerar du `Microsoft.OperationalInsights/workspaces/read` att du har behörighet att kunna hitta om den virtuella datorn är inbyggt på en arbetsyta.

### <a name="scenario-onboarding-fails-with-the-message---failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>Scenario: Introduktionen misslyckas med meddelandet - Det gick inte att konfigurera Automation-konto för diagnostikloggning

#### <a name="issue"></a>Problem

Du får följande meddelande när du försöker gå in på en virtuell dator till en lösning:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Orsak

Det här felet kan uppstå om prisnivån inte matchar prenumerationens faktureringsmodell. Mer information finns i [Övervaka användning och uppskattade kostnader i Azure Monitor](https://aka.ms/PricingTierWarning).

#### <a name="resolution"></a>Lösning

Skapa din Log Analytics-arbetsyta manuellt och upprepa introduktionsprocessen för att välja den arbetsyta som skapats.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>Scenario: ComputerGroupQueryFormatError

#### <a name="issue"></a>Problem

Den här felkoden innebär att den sparade sökdatorgruppsfrågan som användes för att rikta lösningen inte var korrekt formaterad. 

#### <a name="cause"></a>Orsak

Du kan ha ändrat frågan eller så kan den ha ändrats av systemet.

#### <a name="resolution"></a>Lösning

Du kan ta bort frågan för den här lösningen och skriva in lösningen igen, vilket återskapar frågan. Frågan finns på arbetsytan under **Sparade sökningar**. Namnet på frågan är **MicrosoftDefaultComputerGroup**och frågans kategori är namnet på den lösning som är associerad med den här frågan. Om flera lösningar är aktiverade visar **MicrosoftDefaultComputerGroup** flera gånger under **Sparade sökningar**.

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>Scenario: PolicyViolation

#### <a name="issue"></a>Problem

Den här felkoden innebär att distributionen misslyckades på grund av brott mot en eller flera principer.

#### <a name="cause"></a>Orsak 

En princip finns på plats som blockerar åtgärden från att slutföras.

#### <a name="resolution"></a>Lösning

För att kunna distribuera lösningen måste du överväga att ändra den angivna principen. Eftersom det finns många olika typer av principer som kan definieras beror de specifika ändringar som krävs på vilken princip som överträds. Om till exempel en princip har definierats för en resursgrupp som nekades behörighet att ändra innehållet i vissa typer av resurser inom resursgruppen, kan du till exempel göra något av följande:

* Ta bort principen helt och hållet.
* Försök att gå ombord till en annan resursgrupp.
* Revidera principen genom att till exempel:
  * Rikta om principen till en viss resurs (till exempel till ett visst Automation-konto).
  * Revidera uppsättningen resurser som principen har konfigurerats för att neka.

Kontrollera aviseringarna i det övre högra hörnet på Azure-portalen eller navigera till resursgruppen som innehåller ditt automatiseringskonto och välj **Distributioner** under **Inställningar** för att visa den misslyckade distributionen. Mer information om Azure Policy-besök: [Översikt över Azure Policy](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>Scenario: Fel som försöker ta bort länken till en arbetsyta

#### <a name="issue"></a>Problem

Följande felmeddelande visas när du försöker ta bort länken till en arbetsyta:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Orsak

Det här felet uppstår när du fortfarande har lösningar som är aktiva på din Log Analytics-arbetsyta som är beroende av att din Automation-konto och Logganalysarbetsyta länkas.

### <a name="resolution"></a>Lösning

För att lösa detta måste du ta bort följande lösningar från arbetsytan om du använder dem:

* Uppdateringshantering
* Spårning av ändringar
* Starta/stoppa virtuella datorer utanför arbetstid

När du har tagit bort lösningarna kan du ta bort länken till arbetsytan. Det är viktigt att rensa upp alla befintliga artefakter från dessa lösningar från din arbetsyta och Automation-konto också.  

* Uppdateringshantering
  * Ta bort uppdateringsdistributioner (scheman) från ditt Automation-konto
* Starta/stoppa virtuella datorer utanför arbetstid
  * Ta bort eventuella lås på lösningskomponenter i ditt Automation-konto under **Inställningar** > **Lås**.
  * Ytterligare steg för att ta bort start-/stopp-virtuella datorer under starttimmarslösning finns i [Ta bort start/stopp-datorn under start-/stopp-lösningen](../automation-solution-vm-management.md#remove-the-solution).

## <a name="mma-extension-failures"></a><a name="mma-extension-failures"></a>FEL I MMA-tillägget

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

När du distribuerar en lösning distribueras en mängd relaterade resurser. En av dessa resurser är Microsoft Monitoring Agent Extension eller Log Analytics agent för Linux. Dessa är tillägg för virtuella datorer som installeras av den virtuella datorns gästagent som ansvarar för att kommunicera med den konfigurerade Log Analytics-arbetsytan, i syfte att senare samordna hämtningen av binärfiler och andra filer som lösningen du är ombord på beror på när den börjar körningen.
Du får vanligtvis först kännedom om MMA- eller Log Analytics-agent för Linux-installationsfel från ett meddelande som visas i notificationshubben. Om du klickar på meddelandet får du ytterligare information om det specifika felet. Navigering till resursen Resursgrupper och sedan till distributionselementet i den innehåller också information om distributionsfel som inträffade.
Installationen av MMA- eller Log Analytics-agenten för Linux kan misslyckas av olika anledningar, och stegen för att åtgärda dessa fel varierar beroende på problemet. Specifika felsökningssteg följer.

I följande avsnitt beskrivs olika problem som du kan stöta på när du är ombord som orsakar ett fel i distributionen av MMA-tillägget.

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>Scenario: Ett undantag inträffade under en WebClient-begäran

MMA-tillägget på den virtuella datorn kan inte kommunicera med externa resurser och distributionen misslyckas.

#### <a name="issue"></a>Problem

Följande är exempel på felmeddelanden som returneras:

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>Orsak

Några potentiella orsaker till det här felet är:

* Det finns en proxy som konfigurerats i den virtuella datorn, som bara tillåter specifika portar.

* En brandväggsinställning har blockerat åtkomsten till de portar och adresser som krävs.

#### <a name="resolution"></a>Lösning

Se till att du har rätt portar och adresser öppna för kommunikation. En lista över portar och adresser finns i [planera nätverket](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="scenario-install-failed-because-of-a-transient-environment-issues"></a><a name="transient-environment-issue"></a>Scenario: Installationen misslyckades på grund av tillfälliga miljöproblem

Installationen av tillägget Microsoft Monitoring Agent misslyckades under distributionen på grund av en annan installation eller åtgärd som blockerade installationen

#### <a name="issue"></a>Problem

Följande är exempel på felmeddelanden kan returneras:

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

Några potentiella orsaker till det här felet är:

* En annan installation pågår
* Systemet utlöses för att starta om under malldistributionen

#### <a name="resolution"></a>Lösning

Det här felet är ett tillfälligt fel av naturen. Försök att installera tillägget igen.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>Scenario: Timeout för installation

Installationen av MMA-tillägget slutfördes inte på grund av en timeout.

#### <a name="issue"></a>Problem

Följande exempel är ett felmeddelande som kan returneras:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Orsak

Det här felet beror på att den virtuella datorn är under en tung belastning under installationen.

### <a name="resolution"></a>Lösning

Försök att installera MMA-tillägget när den virtuella datorn är under en lägre belastning.

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure-forumen](https://azure.microsoft.com/support/forums/)
* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Om du behöver mer hjälp kan du lämna in en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj **Hämta support**.
