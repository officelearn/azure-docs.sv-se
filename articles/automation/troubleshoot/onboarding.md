---
title: Felsöka onboarding av Azure Automation hanterings lösningar
description: Lär dig hur du felsöker fel vid lösnings registrering.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: da5152b459f54cbaae5ec168f103f23a237edebd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679238"
---
# <a name="troubleshoot-solution-onboarding"></a>Felsöka lösnings registrering

Du kan få fel meddelanden när du registrerar Uppdateringshantering-lösningen eller Ändringsspårning-och inventerings lösningen. I den här artikeln beskrivs de olika fel som kan uppstå och hur du kan lösa dem.

## <a name="known-issues"></a>Kända problem

### <a name="scenario-renaming-a-registered-node-requires-unregister-or-register-again"></a><a name="node-rename"></a>Scenario: om du byter namn på en registrerad nod måste du avregistrera eller registrera igen

#### <a name="issue"></a>Problem

En nod är registrerad på Azure Automation och sedan ändras operativ systemets dator namn. Rapporter från noden visas fortfarande med det ursprungliga namnet.

#### <a name="cause"></a>Orsak

Att byta namn på registrerade noder uppdaterar inte nodnamnet i Azure Automation.

#### <a name="resolution"></a>Lösning

Avregistrera noden från Azure Automation tillstånds konfiguration och registrera den sedan igen. Rapporter som publicerats till tjänsten innan den tiden kommer inte längre att vara tillgängliga.

### <a name="scenario-re-signing-certificates-via-https-proxy-is-not-supported"></a><a name="resigning-cert"></a>Scenario: det finns inte stöd för att signera om certifikat via https-proxy

#### <a name="issue"></a>Problem

När du ansluter via en proxyserver som avslutar HTTPS-trafik och sedan krypterar om trafiken med hjälp av ett nytt certifikat, tillåter inte-tjänsten anslutningen.

#### <a name="cause"></a>Orsak

Azure Automation stöder inte omsignering av certifikat som används för att kryptera trafik.

#### <a name="resolution"></a>Lösning

Det finns för närvarande ingen lösning på problemet.

## <a name="general-errors"></a>Allmänna fel

### <a name="scenario-onboarding-fails-with-the-message---the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>Scenario: onboarding Miss lyckas med meddelandet-lösningen kan inte aktive ras

#### <a name="issue"></a>Problem

Du får ett av följande meddelanden när du försöker publicera en virtuell dator till en lösning:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Orsak

Felet beror på felaktig eller saknas behörighet på den virtuella datorn eller arbets ytan eller för användaren.

#### <a name="resolution"></a>Lösning

Se till att du har rätt [behörigheter som krävs för att publicera datorer](../automation-role-based-access-control.md#onboarding-permissions) och försök sedan att publicera lösningen igen. Om du får ett fel `The solution cannot be enabled on this VM because the permission to read the workspace is missing`meddelande måste du kontrol lera att `Microsoft.OperationalInsights/workspaces/read` du har behörighet att kunna hitta om den virtuella datorn har publicerats i en arbets yta.

### <a name="scenario-onboarding-fails-with-the-message-failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>Scenario: onboarding Miss lyckas med meddelandet: det gick inte att konfigurera Automation-konto för diagnostisk loggning

#### <a name="issue"></a>Problem

Följande meddelande visas när du försöker publicera en virtuell dator till en lösning:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Orsak

Felet kan bero på att pris nivån inte matchar prenumerationens fakturerings modell. Se [övervakning av användning och uppskattade kostnader i Azure Monitor](https://aka.ms/PricingTierWarning).

#### <a name="resolution"></a>Lösning

Skapa din Log Analytics arbets yta manuellt och upprepa onboarding-processen för att välja den arbets yta som skapats.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>Scenario: ComputerGroupQueryFormatError

#### <a name="issue"></a>Problem

Den här felkoden innebär att frågan för den sparade Sök dator gruppen som används för att rikta lösningen inte är korrekt formaterad. 

#### <a name="cause"></a>Orsak

Du kan ha ändrat frågan, eller så kan systemet ha ändrat det.

#### <a name="resolution"></a>Lösning

Du kan ta bort frågan för lösningen och sedan publicera lösningen igen, som återskapar frågan. Du hittar frågan i din arbets yta under **sparade sökningar**. Namnet på frågan är **MicrosoftDefaultComputerGroup**och frågans kategori är namnet på den associerade lösningen. Om flera lösningar är aktiverade visar **MicrosoftDefaultComputerGroup** -frågan flera gånger under **sparade sökningar**.

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>Scenario: PolicyViolation

#### <a name="issue"></a>Problem

Den här felkoden anger att distributionen misslyckades på grund av överträdelse av en eller flera principer.

#### <a name="cause"></a>Orsak 

En princip blockerar åtgärden från att slutföras.

#### <a name="resolution"></a>Lösning

För att kunna distribuera lösningen måste du överväga att ändra den angivna principen. Eftersom det finns många olika typer av principer som kan definieras beror ändringarna som krävs på den princip som har överskridits. Om en princip exempelvis definieras i en resurs grupp som nekar behörighet att ändra innehållet i vissa resurser, kan du välja någon av följande korrigeringar:

* Ta bort principen helt.
* Försök att publicera lösningen till en annan resurs grupp.
* Rikta in principen till en speciell resurs, till exempel ett Automation-konto.
* Ändra den uppsättning resurser som principen är konfigurerad att neka.

Kontrol lera meddelandena i det övre högra hörnet av Azure Portal eller navigera till den resurs grupp som innehåller ditt Automation-konto och välj **distributioner** under **Inställningar** för att visa den misslyckade distributionen. Mer information om Azure Policy finns i [Översikt över Azure policy](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>Scenario: fel vid försök att ta bort länken till en arbets yta

#### <a name="issue"></a>Problem

Du får följande fel meddelande när du försöker ta bort länken till en arbets yta:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Orsak

Det här felet uppstår när du fortfarande har lösningar aktiva på din Log Analytics arbets yta som är beroende av ditt Automation-konto och Log Analytics arbets yta som är länkad.

### <a name="resolution"></a>Lösning

Ta bort följande lösningar från arbets ytan om du använder dem:

* Uppdateringshantering
* Ändringsspårning och inventering
* Starta/stoppa virtuella datorer utanför arbetstid

När du har tagit bort lösningarna kan du ta bort länken till arbets ytan. Det är viktigt att du rensar befintliga artefakter från dessa lösningar från din arbets yta och ditt Automation-konto 

* För Uppdateringshantering tar du bort uppdaterings distributioner (scheman) från ditt Automation-konto.
* För starta/stoppa virtuella datorer när de inte används tar du bort eventuella lås på lösnings komponenter i Automation-kontot under **Inställningar** > **Lås**. Se [ta bort starta/stoppa virtuella datorer när de inte används-lösningen](../automation-solution-vm-management.md#remove-the-solution).

## <a name="log-analytics-for-windows-extension-failures"></a><a name="mma-extension-failures"></a>Log Analytics för Windows-tilläggsbegäranden

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

En installation av Log Analytics agent för Windows-tillägget kan Miss Miss av olika orsaker. I följande avsnitt beskrivs onboarding-problem som kan orsaka fel under distributionen av Log Analytics agent för Windows-tillägg.

>[!NOTE]
>Log Analytics agent för Windows är namnet som används för närvarande i Azure Automation för Microsoft Monitoring Agent (MMA).

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>Scenario: ett undantag inträffade under en WebClient-begäran

Log Analytics för Windows-tillägget på den virtuella datorn kan inte kommunicera med externa resurser och distributionen Miss lyckas.

#### <a name="issue"></a>Problem

Följande är exempel på fel meddelanden som returneras:

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>Orsak

Några möjliga orsaker till det här felet är:

* En proxy som kon figurer ATS på den virtuella datorn tillåter endast vissa portar.
* En brand Väggs inställning har blockerat åtkomst till de portar och adresser som krävs.

#### <a name="resolution"></a>Lösning

Kontrol lera att du har rätt portar och adresser öppna för kommunikation. En lista över portar och adresser finns i [Planera nätverket](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="scenario-install-failed-because-of-a-transient-environment-issues"></a><a name="transient-environment-issue"></a>Scenario: installationen misslyckades på grund av ett tillfälligt miljö problem

Installationen av Log Analytics för Windows-tillägget misslyckades under distributionen på grund av en annan installation eller åtgärd som blockerar installationen

#### <a name="issue"></a>Problem

Följande är exempel på fel meddelanden som kan returneras:

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

Några möjliga orsaker till det här felet är:

* En annan installation pågår.
* Systemet utlöses för omstart vid mall-distributionen.

#### <a name="resolution"></a>Lösning

Det här felet är av tillfällig natur. Gör om distributionen för att installera tillägget.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>Scenario: tids gräns för installation

Installationen av Log Analytics för Windows-tillägget slutfördes inte på grund av en tids gräns.

#### <a name="issue"></a>Problem

Följande är ett exempel på ett fel meddelande som kan returneras:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Orsak

Den här typen av fel inträffar eftersom den virtuella datorn är under en kraftig belastning under installationen.

### <a name="resolution"></a>Lösning

Försök att installera Log Analytics agent för Windows-tillägg när den virtuella datorn har en lägre belastning.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet ovan eller inte kan lösa problemet kan du prova någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Azure-forum](https://azure.microsoft.com/support/forums/).
* Anslut till [@AzureSupport](https://twitter.com/azuresupport), det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Filen en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.
