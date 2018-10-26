---
title: Felsöka fel med hantering av uppdateringar
description: Lär dig att felsöka problem med hantering av uppdateringar
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: f52767058ef69d29465f1274109b6d3ffe58296c
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092635"
---
# <a name="troubleshooting-issues-with-update-management"></a>Felsökning av problem med hantering av uppdateringar

Den här artikeln beskriver lösningar för att lösa problem som du kan köra över när du använder hantering av uppdateringar.

Det finns en agent-felsökare för Hybrid Worker-agenten att fastställa det underliggande problemet. Läs mer om felsökaren i [Felsök agenten uppdateringsproblem](update-agent-issues.md). Andra problem finns i den detaljerade informationen nedan om eventuella problem.

## <a name="general"></a>Allmänt

### <a name="components-enabled-not-working"></a>Scenario: Komponenterna för ' uppdateringshanteringslösningen ' har aktiverats och nu den här virtuella datorn konfigureras

#### <a name="issue"></a>Problem

Fortsätter att visas följande meddelande på en virtuell dator 15 minuter efter integreringen:

```
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

#### <a name="cause"></a>Orsak

Det här felet kan orsakas av följande orsaker:

1. Kommunikation till Automation-kontot blockeras.
2. Den virtuella datorn som det gäller kanske har kommit från en klonad dator som inte är Sysprep med Microsoft Monitoring Agent installerad.

#### <a name="resolution"></a>Lösning

1. Besök, [nätverksplanering](../automation-hybrid-runbook-worker.md#network-planning) att lära dig om vilka adresser och portar måste vara tillgängliga för hantering av uppdateringar ska fungera.
2. Om med sysprep-avbildningen en klonade avbildningen först och installerar MMA-agenten i efterhand.

## <a name="windows"></a>Windows

Om det uppstår problem vid försök att publicera lösningen på en virtuell dator, kontrollera den **Operations Manager** Loggboken under **program- och tjänstloggar** på den lokala datorn för händelser med händelse-ID **4502** och händelsemeddelanden som innehåller **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**.

Följande avsnitt illustrerar specifika felmeddelanden och en möjlig lösning för var och en. Andra onboarding problem finns i, [felsöka publiceringen av lösningen](onboarding.md).

### <a name="machine-already-registered"></a>Scenario: Datorn är redan registrerad till ett annat konto

#### <a name="issue"></a>Problem

Du får följande felmeddelande visas:

```
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

#### <a name="cause"></a>Orsak

Datorn har redan publicerats till en annan arbetsyta för uppdateringshantering.

#### <a name="resolution"></a>Lösning

Rensa gamla artefakter på datorn genom att [tar bort hybridrunbookgruppen](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group) och försök igen.

### <a name="machine-unable-to-communicate"></a>Scenario: Datorn kan inte kommunicera med tjänsten

#### <a name="issue"></a>Problem

Du får något av följande felmeddelanden:

```
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

#### <a name="cause"></a>Orsak

Det kan finnas en gateway, en proxy eller en brandvägg som blockerar nätverkskommunikation.

#### <a name="resolution"></a>Lösning

Granska dina nätverk och se till att lämpliga portar och adresser tillåts. Se [krav på](../automation-hybrid-runbook-worker.md#network-planning), en lista över portar och adresser som krävs av hantering av uppdateringar och Hybrid Runbook Worker.

### <a name="unable-to-create-selfsigned-cert"></a>Scenario: Det går inte att skapa självsignerat certifikat

#### <a name="issue"></a>Problem

Du får något av följande felmeddelanden:

```
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

#### <a name="cause"></a>Orsak

Hybrid Runbook Worker gick inte att generera ett självsignerat certifikat

#### <a name="resolution"></a>Lösning

Kontrollera system-kontot har läsbehörighet till mappen **C:\ProgramData\Microsoft\Crypto\RSA** och försök igen.

### <a name="nologs"></a>Scenario: Uppdateringshantering data visas inte i Log Analytics för en dator

#### <a name="issue"></a>Problem

Du har datorer som visas som **ej utvärderat** under **efterlevnad**, men du kan se pulsslag data i Log Analytics för Hybrid Runbook Worker men inte hantering av uppdateringar.

#### <a name="cause"></a>Orsak

Hybrid Runbook Worker kan behöva registreras igen och ominstalleras.

#### <a name="resolution"></a>Lösning

Följ stegen i [distribuera en Windows Hybrid Runbook Worker](../automation-windows-hrw-install.md) att installera om Hybrid Worker.

### <a name="hresult"></a>Scenario: Datorn visas som ej utvärderat och visar ett HResult-undantag

#### <a name="issue"></a>Problem

Du har datorer som visas som **ej utvärderat** under **efterlevnad**, och du ser ett Undantagsmeddelande under den.

#### <a name="cause"></a>Orsak

Windows update är inte korrekt konfigurerad på datorn.

#### <a name="resolution"></a>Lösning

Dubbelklicka på undantaget som visas i rött att se hela Undantagsmeddelandet. Kontrollera i följande tabell för möjliga lösningar eller åtgärder som ska vidtas:

|Undantag  |Lösning eller åtgärd  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Sök efter den relevanta felkoden i [Windows uppdatera kod fellistan](https://support.microsoft.com/help/938205/windows-update-error-code-list) att ha ytterligare information om orsaken till undantaget.        |
|`0x8024402C` eller `0x8024401C`     | Felen är problem med nätverksanslutningen. Se till att datorn har rätt nätverksanslutningen till hantering av uppdateringar. Se avsnittet om [nätverksplanering](../automation-update-management.md#ports) en lista över portar och adresser som krävs.        |
|`0x8024402C`     | Om du använder en WSUS-server kontrollerar du registervärdena `WUServer` och `WUStatusServer` under registernyckeln `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` har korrekt WSUS-servern.        |
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Kontrollera att Windows Update-tjänsten (wuauserv) körs och har inte inaktiverats.        |
|Allmänt undantag     | Gör en sökning efter möjliga lösningar på internet och arbeta med den lokala IT-supporten.         |

## <a name="linux"></a>Linux

### <a name="scenario-update-run-fails-to-start"></a>Scenario: Uppdateringskörningen inte går att starta

#### <a name="issue"></a>Problem

En uppdatering körs kunde inte starta på en Linux-dator.

#### <a name="cause"></a>Orsak

Linux Hybrid Worker är felfri.

#### <a name="resolution"></a>Lösning

Skapa en kopia av följande loggfiler och bevara i felsökningssyfte:

```
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

### <a name="scenario-update-run-starts-but-encounters-errors"></a>Scenario: Uppdateringskörningen börjar, men det uppstår problem

#### <a name="issue"></a>Problem

En uppdateringskörning börjar, men det uppstår problem under körningen.

#### <a name="cause"></a>Orsak

Möjliga orsaker kan vara följande:

* Pakethanterare är i feltillstånd
* Specifika paket kan störa molnbaserad korrigeringar
* Andra orsaker

#### <a name="resolution"></a>Lösning

Om fel uppstår under en uppdatering som körs när den har startats på Linux loggen det utdata från den berörda datorn i körningen. Du kanske felmeddelanden från din dator Pakethanteraren som du kan undersöka och vidta åtgärder för. Hantering av uppdateringar kräver Pakethanteraren felfria för lyckade distributioner.

I vissa fall kan paketuppdateringar störa uppdateringshantering förhindrar en uppdateringsdistribution från att slutföras. Om du ser som du måste antingen undanta dessa paket från framtida uppdateringskörningar eller installera dem manuellt själv.

Om du inte kan lösa ett korrigeringsfel, göra en kopia av följande loggfiler och bevara den **innan** nästa uppdateringsdistribution startar i felsökningssyfte:

```
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="next-steps"></a>Nästa steg

Om du inte ser ditt problem eller inte kan lösa problemet besöker du någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Azure-forumen](https://azure.microsoft.com/support/forums/)
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure-kontot för att förbättra kundtjänstupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Om du behöver mer hjälp kan öppna du en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **hämta stöder**.