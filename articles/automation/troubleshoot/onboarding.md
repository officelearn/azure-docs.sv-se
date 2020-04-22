---
title: Felsöka introduktion av Azure Automation-hanteringslösningar
description: Läs om hur du felsöker lösningsfel.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: da5152b459f54cbaae5ec168f103f23a237edebd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679238"
---
# <a name="troubleshoot-solution-onboarding"></a>Felsöka inbyggd lösning

Du kan få felmeddelanden när du ar ombord på lösning för uppdateringshantering eller lösningen för ändringsspårning och lager. I den här artikeln beskrivs de olika fel som kan uppstå och hur du löser dem.

## <a name="known-issues"></a>Kända problem

### <a name="scenario-renaming-a-registered-node-requires-unregister-or-register-again"></a><a name="node-rename"></a>Scenario: Byta namn på en registrerad nod kräver avregistrera eller registrera igen

#### <a name="issue"></a>Problem

En nod registreras i Azure Automation och sedan ändras namnet på operativsystemets dator. Rapporter från noden fortsätter att visas med det ursprungliga namnet.

#### <a name="cause"></a>Orsak

Att byta namn på registrerade noder uppdaterar inte nodnamnet i Azure Automation.

#### <a name="resolution"></a>Lösning

Avregistrera noden från Azure Automation State Configuration och registrera den sedan igen. Rapporter som publicerats till tjänsten före den tiden kommer inte längre att vara tillgängliga.

### <a name="scenario-re-signing-certificates-via-https-proxy-is-not-supported"></a><a name="resigning-cert"></a>Scenario: Omsignering av certifikat via https-proxy stöds inte

#### <a name="issue"></a>Problem

När du ansluter via en proxylösning som avslutar HTTPS-trafik och sedan krypterar om trafiken med ett nytt certifikat, tillåter tjänsten inte anslutningen.

#### <a name="cause"></a>Orsak

Azure Automation stöder inte omsignering av certifikat som används för att kryptera trafik.

#### <a name="resolution"></a>Lösning

Det finns för närvarande ingen lösning på problemet.

## <a name="general-errors"></a>Allmänna fel

### <a name="scenario-onboarding-fails-with-the-message---the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>Scenario: Introduktionsstart misslyckas med meddelandet - Lösningen kan inte aktiveras

#### <a name="issue"></a>Problem

Du får ett av följande meddelanden när du försöker gå in på en virtuell dator till en lösning:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Orsak

Det här felet orsakas av felaktiga eller saknade behörigheter på den virtuella datorn eller arbetsytan eller för användaren.

#### <a name="resolution"></a>Lösning

Kontrollera att du har rätt [behörighet som behövs för att använda datorer ombord](../automation-role-based-access-control.md#onboarding-permissions) och försök sedan att gå in på lösningen igen. Om felet visas `The solution cannot be enabled on this VM because the permission to read the workspace is missing`kontrollerar du `Microsoft.OperationalInsights/workspaces/read` att du har behörighet att kunna hitta om den virtuella datorn är inbyggt på en arbetsyta.

### <a name="scenario-onboarding-fails-with-the-message-failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>Scenario: Introduktionen misslyckas med meddelandet: Det gick inte att konfigurera automatiseringskonto för diagnostikloggning

#### <a name="issue"></a>Problem

Du får följande meddelande när du försöker gå in på en virtuell dator till en lösning:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Orsak

Det här felet kan uppstå om prisnivån inte matchar prenumerationens faktureringsmodell. Se [Övervaka användning och uppskattade kostnader i Azure Monitor](https://aka.ms/PricingTierWarning).

#### <a name="resolution"></a>Lösning

Skapa din Log Analytics-arbetsyta manuellt och upprepa introduktionsprocessen för att välja den arbetsyta som skapats.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>Scenario: ComputerGroupQueryFormatError

#### <a name="issue"></a>Problem

Den här felkoden innebär att den sparade sökdatorgruppsfrågan som används för att rikta lösningen inte är korrekt formaterad. 

#### <a name="cause"></a>Orsak

Du kan ha ändrat frågan eller så kan systemet ha ändrat den.

#### <a name="resolution"></a>Lösning

Du kan ta bort frågan för lösningen och sedan gå in på lösningen igen, vilket återskapar frågan. Frågan finns på arbetsytan under **Sparade sökningar**. Namnet på frågan är **MicrosoftDefaultComputerGroup**och frågans kategori är namnet på den associerade lösningen. Om flera lösningar är aktiverade visas flera gånger under **Sparade sökningar**i **MicrosoftDefaultComputerGroup-frågan** .

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>Scenario: PolicyViolation

#### <a name="issue"></a>Problem

Den här felkoden anger att distributionen misslyckades på grund av brott mot en eller flera principer.

#### <a name="cause"></a>Orsak 

En princip blockerar åtgärden från att slutföras.

#### <a name="resolution"></a>Lösning

För att kunna distribuera lösningen måste du överväga att ändra den angivna principen. Eftersom det finns många olika typer av principer som kan definieras beror de ändringar som krävs på vilken princip som överträds. Om till exempel en princip har definierats för en resursgrupp som nekar behörighet att ändra innehållet i vissa inneslutna resurser kan du välja en av dessa korrigeringar:

* Ta bort principen helt och hållet.
* Försök att gå in på lösningen till en annan resursgrupp.
* Rikta om principen till en viss resurs, till exempel ett Automation-konto.
* Ändra den uppsättning resurser som principen är konfigurerad för att neka.

Kontrollera aviseringarna i det övre högra hörnet av Azure-portalen eller navigera till resursgruppen som innehåller ditt Automation-konto och välj **Distributioner** under **Inställningar** för att visa den misslyckade distributionen. Mer information om Azure-princip finns i [Översikt över Azure Policy](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>Scenario: Fel som försöker ta bort länken till en arbetsyta

#### <a name="issue"></a>Problem

Följande felmeddelande visas när du försöker ta bort länken till en arbetsyta:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Orsak

Det här felet uppstår när du fortfarande har lösningar som är aktiva på din Log Analytics-arbetsyta som är beroende av att ditt Automation-konto och Log Analytics-arbetsyta länkas.

### <a name="resolution"></a>Lösning

Ta bort följande lösningar från arbetsytan om du använder dem:

* Uppdateringshantering
* Ändringsspårning och inventering
* Starta/stoppa virtuella datorer utanför arbetstid

När du har tagit bort lösningarna kan du ta bort länken till arbetsytan. Det är viktigt att rensa upp alla befintliga artefakter från dessa lösningar från din arbetsyta och ditt Automation-konto 

* För uppdateringshantering tar du bort uppdateringsdistributioner (scheman) från ditt Automation-konto.
* För start-/stopp-virtuella datorer under ledig tid tar du bort eventuella lås på lösningskomponenter i ditt Automation-konto under **Inställningar** > **lås**. Se [Ta bort start-/stopp-virtuella datorer under ledig tid.](../automation-solution-vm-management.md#remove-the-solution)

## <a name="log-analytics-for-windows-extension-failures"></a><a name="mma-extension-failures"></a>Logga analytics för Windows-tilläggsfel

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

En installation av Log Analytics-agenten för Windows-tillägget kan misslyckas av olika anledningar. I följande avsnitt beskrivs introduktionsproblem som kan orsaka fel under distributionen av Log Analytics-agenten för Windows-tillägget.

>[!NOTE]
>Log Analytics-agent för Windows är det namn som för närvarande används i Azure Automation för Microsoft Monitoring Agent (MMA).

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>Scenario: Ett undantag inträffade under en WebClient-begäran

Log Analytics för Windows-tillägget på den virtuella datorn kan inte kommunicera med externa resurser och distributionen misslyckas.

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

* En proxy som konfigurerats i den virtuella datorn tillåter endast specifika portar.
* En brandväggsinställning har blockerat åtkomsten till de portar och adresser som krävs.

#### <a name="resolution"></a>Lösning

Se till att du har rätt portar och adresser öppna för kommunikation. En lista över portar och adresser finns i [planera nätverket](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="scenario-install-failed-because-of-a-transient-environment-issues"></a><a name="transient-environment-issue"></a>Scenario: Installationen misslyckades på grund av tillfälliga miljöproblem

Installationen av tillägget Log Analytics för Windows misslyckades under distributionen på grund av en annan installation eller åtgärd som blockerade installationen

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

* En annan installation pågår.
* Systemet utlöses för att starta om under malldistributionen.

#### <a name="resolution"></a>Lösning

Det här felet är övergående till sin natur. Försök att installera tillägget igen.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>Scenario: Timeout för installation

Installationen av log analytics för Windows-tillägget slutfördes inte på grund av en timeout.

#### <a name="issue"></a>Problem

Följande är ett exempel på ett felmeddelande som kan returneras:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Orsak

Den här typen av fel uppstår eftersom den virtuella datorn är under en tung belastning under installationen.

### <a name="resolution"></a>Lösning

Försök att installera Log Analytics-agenten för Windows-tillägget när den virtuella datorn är lägre.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet ovan eller inte kan lösa problemet kan du prova någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Azure Forum](https://azure.microsoft.com/support/forums/).
* Anslut [@AzureSupport](https://twitter.com/azuresupport)med , det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Arkivera en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj **Hämta support**.
