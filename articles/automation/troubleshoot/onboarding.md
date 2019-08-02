---
title: Felsöka fel vid registrering Uppdateringshantering, Ändringsspårning och inventering
description: Lär dig hur du felsöker onboarding-fel med Uppdateringshantering, Ändringsspårning och inventerings lösningar
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 8b4ee999bb23abdcea3411720bde244b2da4e89f
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516405"
---
# <a name="troubleshoot-errors-when-onboarding-solutions"></a>Felsök fel vid integrering av lösningar

Du kan stöta på fel när du registrerar lösningar som Uppdateringshantering eller Ändringsspårning och inventering. I den här artikeln beskrivs de olika fel som kan uppstå och hur du kan lösa dem.

## <a name="known-issues"></a>Kända problem

### <a name="node-rename"></a>Situationen Att byta namn på en registrerad nod kräver avregistrering/registrering igen

#### <a name="issue"></a>Problem

En nod är registrerad på Azure Automation och sedan ändras operativ systemets dator namn.  Rapporter från noden visas fortfarande med det ursprungliga namnet.

#### <a name="cause"></a>Orsak

Att byta namn på registrerade noder uppdaterar inte nodnamnet i Azure Automation.

#### <a name="resolution"></a>Lösning

Avregistrera noden från Azure Automation tillstånds konfiguration och registrera den sedan igen.  Rapporter som publicerats till tjänsten innan den tiden kommer inte längre att vara tillgängliga.


### <a name="resigning-cert"></a>Situationen Det finns inte stöd för att signera om certifikat via https-proxy

#### <a name="issue"></a>Problem

Kunder har rapporterat att vid anslutning via en proxyserver som avslutar HTTPS-trafik och sedan krypterar om trafik med hjälp av ett nytt certifikat, tillåter inte anslutningen av tjänsten.

#### <a name="cause"></a>Orsak

Azure Automation stöder inte omsignering av certifikat som används för att kryptera trafik.

#### <a name="resolution"></a>Lösning

Det finns ingen lösning för det här problemet.

## <a name="general-errors"></a>Allmänna fel

### <a name="missing-write-permissions"></a>Situationen Onboarding Miss lyckas med meddelandet-lösningen kan inte aktive ras

#### <a name="issue"></a>Problem

Du får ett av följande meddelanden när du försöker publicera en virtuell dator till en lösning:

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Orsak

Felet beror på felaktig eller saknas behörighet på den virtuella datorn, arbets ytan eller för användaren.

#### <a name="resolution"></a>Lösning

Se till att du har rätt behörigheter för att publicera den virtuella datorn. Granska de [behörigheter som krävs för att publicera datorer](../automation-role-based-access-control.md#onboarding) och försök att publicera lösningen igen. Om du får felet `The solution cannot be enabled on this VM because the permission to read the workspace is missing`kontrollerar du att du `Microsoft.OperationalInsights/workspaces/read` har behörighet att kunna hitta om den virtuella datorn har publicerats i en arbets yta.

### <a name="diagnostic-logging"></a>Situationen Onboarding Miss lyckas med meddelandet – Det gick inte att konfigurera Automation-konto för diagnostisk loggning

#### <a name="issue"></a>Problem

Följande meddelande visas när du försöker publicera en virtuell dator till en lösning:

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Orsak

Felet kan bero på att pris nivån inte matchar prenumerationens fakturerings modell. Mer information finns i [övervaka användning och uppskattade kostnader i Azure Monitor](https://aka.ms/PricingTierWarning).

#### <a name="resolution"></a>Lösning

Skapa din Log Analytics arbets yta manuellt och upprepa onboarding-processen för att välja den arbets yta som skapats.

### <a name="computer-group-query-format-error"></a>Situationen ComputerGroupQueryFormatError

#### <a name="issue"></a>Problem

Den här felkoden innebär att frågan för den sparade Sök dator gruppen som används för att rikta lösningen inte var korrekt formaterad. 

#### <a name="cause"></a>Orsak

Du kan ha ändrat frågan, eller så kan den ha ändrats av systemet.

#### <a name="resolution"></a>Lösning

Du kan ta bort frågan för den här lösningen och återskapa lösningen som återskapar frågan. Du hittar frågan i din arbets yta under **sparade sökningar**. Namnet på frågan är **MicrosoftDefaultComputerGroup**och frågans kategori är namnet på lösningen som är associerad med den här frågan. Om flera lösningar är aktiverade visas **MicrosoftDefaultComputerGroup** flera gånger under **sparade sökningar**.

### <a name="policy-violation"></a>Situationen PolicyViolation

#### <a name="issue"></a>Problem

Den här felkoden innebär att distributionen misslyckades på grund av överträdelse av en eller flera principer.

#### <a name="cause"></a>Orsak 

En princip är på plats som hindrar åtgärden från att slutföras.

#### <a name="resolution"></a>Lösning

För att kunna distribuera lösningen måste du överväga att ändra den angivna principen. Eftersom det finns många olika typer av principer som kan definieras beror de aktuella ändringarna på den princip som överträds. Om en princip exempelvis har definierats i en resurs grupp som nekade behörighet att ändra innehållet i vissa typer av resurser inom den resurs gruppen, kan du till exempel göra något av följande:

* Ta bort principen helt.
* Försök att publicera till en annan resurs grupp.
* Ändra principen, till exempel:
  * Ommålerar principen till en speciell resurs (till exempel ett speciellt Automation-konto).
  * Ändrar den uppsättning av resurser som principen har kon figurer ATS för att neka.

Kontrol lera meddelandena i det övre högra hörnet av Azure Portal eller navigera till den resurs grupp som innehåller ditt Automation-konto och välj **distributioner** under **Inställningar** för att visa den misslyckade distributionen. Läs mer om Azure Policy på: [Översikt över Azure policy](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

### <a name="unlink"></a>Situationen Fel vid försök att ta bort länk till en arbets yta

#### <a name="issue"></a>Problem

Du får följande fel meddelande när du försöker ta bort länken till en arbets yta:

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Orsak

Felet uppstår när du fortfarande har lösningar aktiva på din Log Analytics arbets yta som är beroende av ditt Automation-konto och logg analys-arbetsytan som länkas.

### <a name="resolution"></a>Lösning

För att lösa detta måste du ta bort följande lösningar från arbets ytan om du använder dem:

* Uppdateringshantering
* Spårning av ändringar
* Starta/stoppa virtuella datorer utanför arbetstid

När du har tagit bort lösningarna kan du ta bort länken till arbets ytan. Det är viktigt att du rensar alla befintliga artefakter från dessa lösningar från arbets ytan och automation-kontot också.  

* Uppdateringshantering
  * Ta bort uppdaterings distributioner (scheman) från ditt Automation-konto
* Starta/stoppa virtuella datorer utanför arbetstid
  * Ta bort eventuella lås på lösnings komponenter i Automation-kontot under **Inställningar** > **Lås**.
  * Mer information om hur du tar bort lösningen för att starta/stoppa virtuella datorer vid låg belastnings tider finns i [ta bort den virtuella datorn för att starta/stoppa en virtuell dator vid låg belastning](../automation-solution-vm-management.md##remove-the-solution).

## <a name="mma-extension-failures"></a>MMA-tilläggsbegäranden

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

När du distribuerar en lösning distribueras en rad relaterade resurser. En av dessa resurser är Microsoft Monitoring Agent-tillägget eller Log Analytics-agenten för Linux. Dessa är tillägg för virtuella datorer som installeras av den virtuella datorns gästa Gent som ansvarar för kommunikation med den konfigurerade Log Analytics arbets ytan, i syfte att senare samordna nedladdningen av binärfiler och andra filer som lösningen som du registrerar är beroende av när den börjar köras.
Du får normalt sett vara medveten om MMA eller Log Analytics agent för Linux-installations problem från ett meddelande som visas i hubben meddelanden. Om du klickar på meddelandet får du ytterligare information om det aktuella problemet. Navigering till resurs grupps resursen och sedan till distributions elementet i den innehåller även information om de distributions fel som har inträffat.
Installation av MMA-eller Log Analytics-agenten för Linux kan Miss lyckas av olika orsaker och stegen för att ta itu med de här felen varierar beroende på problemet. Detaljerade fel söknings steg följer.

I följande avsnitt beskrivs olika problem som du kan stöta på när du registrerar dig som orsakar ett fel i distributionen av MMA-tillägget.

### <a name="webclient-exception"></a>Situationen Ett undantag uppstod under en WebClient-begäran

MMA-tillägget på den virtuella datorn kan inte kommunicera med externa resurser och distributionen Miss lyckas.

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

* En proxy har kon figurer ATS på den virtuella datorn som bara tillåter vissa portar.

* En brand Väggs inställning har blockerat åtkomst till de portar och adresser som krävs.

#### <a name="resolution"></a>Lösning

Kontrol lera att du har rätt portar och adresser öppna för kommunikation. En lista över portar och adresser finns i [Planera nätverket](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="transient-environment-issue"></a>Situationen Installationen misslyckades på grund av ett tillfälligt miljö problem

Installationen av Microsoft Monitoring Agent-tillägget misslyckades under distributionen på grund av en annan installation eller åtgärd som blockerar installationen

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

* En annan installation pågår
* Systemet utlöses för omstart vid mall-distributionen

#### <a name="resolution"></a>Lösning

Det här felet är ett tillfälligt fel. Gör om distributionen för att installera tillägget.

### <a name="installation-timeout"></a>Situationen Tids gräns för installation

Installationen av MMA-tillägget slutfördes inte på grund av en tids gräns.

#### <a name="issue"></a>Problem

I följande exempel visas ett fel meddelande som kan returneras:

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Orsak

Felet beror på att den virtuella datorn är under en kraftig belastning under installationen.

### <a name="resolution"></a>Lösning

Försök att installera MMA-tillägget när den virtuella datorn har en lägre belastning.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure-forumen](https://azure.microsoft.com/support/forums/)
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure-kontot för att förbättra kundtjänstupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Om du behöver mer hjälp kan du skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.
