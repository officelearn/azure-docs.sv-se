---
title: Felsöka fel med hantering av uppdateringar
description: Lär dig att felsöka problem med hantering av uppdateringar
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 08/08/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 2e47320d5ad88edfa8ea6122f3a0abd104230974
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2018
ms.locfileid: "42055395"
---
# <a name="troubleshooting-issues-with-update-management"></a>Felsökning av problem med hantering av uppdateringar

Den här artikeln beskriver lösningar för att lösa problem som kan uppstå när du använder hantering av uppdateringar.

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
2. Virtuell dator som har integrerats kan ha kom från en klonade datorn som inte var Sysprep med Microsoft Monitoring Agent installerad.

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

Det kan finnas en proxy, gateway eller brandvägg som blockerar nätverkskommunikation.

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