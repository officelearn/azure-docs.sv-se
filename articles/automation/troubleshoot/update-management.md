---
title: Felsöka med uppdateringshantering
description: Lär dig hur du felsöker problem med hantering av uppdateringar
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: b77d1210ff48a4bd30834fcbad64173bf77b1290
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064102"
---
# <a name="troubleshooting-issues-with-update-management"></a>Felsökning av problem med hantering av uppdateringar

Den här artikeln beskrivs lösningar för att lösa problem som kan uppstå när du använder hantering av uppdateringar

## <a name="windows"></a>Windows

Kontrollera om det uppstår problem vid försök att publicera lösningen på en virtuell dator kan den **Operations Manager** händelseloggen under **program- och tjänstloggar** på den lokala datorn för händelser med händelse-ID **4502** och händelse som innehåller **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**.

Följande avsnitt visar felmeddelanden och en möjlig lösning för varje. Problem finns andra onboarding [felsöka lösning onboarding](onboarding.md).

### <a name="machine-already-registered"></a>Scenario: Datorn har redan registrerats till ett annat konto

#### <a name="issue"></a>Problem

Följande felmeddelande visas:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

#### <a name="cause"></a>Orsak

Datorn är redan publicerats så att en annan arbetsyta för hantering av uppdateringar.

#### <a name="resolution"></a>Lösning

Rensa gamla artefakter på datorn med [bort hybrid runbook gruppen](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group) och försök igen.

### <a name="machine-unable-to-communicate"></a>Scenario: Datorn kan inte kommunicera med tjänsten

#### <a name="issue"></a>Problem

Visas något av följande felmeddelanden:

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

Det kan finnas en proxy, -gateway eller brandvägg som blockerar nätverkskommunikation.

#### <a name="resolution"></a>Lösning

Granska ditt nätverk och se till att rätt portar och adresser tillåts. Se [krav på](../automation-hybrid-runbook-worker.md#network-planning), en lista över portar och adresser som krävs för hantering av uppdateringar och Hybrid Runbook Worker.

### <a name="unable-to-create-selfsigned-cert"></a>Scenario: Det gick inte att skapa självsignerat certifikat

#### <a name="issue"></a>Problem

Visas något av följande felmeddelanden:

```
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

#### <a name="cause"></a>Orsak

Runbook Worker-hybriden kunde inte generera ett självsignerat certifikat

#### <a name="resolution"></a>Lösning

Kontrollera system-kontot har läsbehörighet till mappen **C:\ProgramData\Microsoft\Crypto\RSA** och försök igen.

## <a name="linux"></a>Linux

### <a name="scenario-update-run-fails-to-start"></a>Scenario: Uppdateringskörningen kan inte startas

#### <a name="issue"></a>Problem

En uppdatering körs kunde inte starta på en Linux-dator.

#### <a name="cause"></a>Orsak

Worker-hybriden Linux är felfri.

#### <a name="resolution"></a>Lösning

Skapa en kopia av följande loggfil och bevara för felsökning:

```
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

### <a name="scenario-update-run-starts-but-encounters-errors"></a>Scenario: Uppdateringskörningen börjar, men påträffar ett fel

#### <a name="issue"></a>Problem

En uppdateringskörning startar, men påträffar ett fel under körningen.

#### <a name="cause"></a>Orsak

Möjliga orsaker kan vara:

* Pakethanteraren är felfri
* Vissa paket kan störa molnbaserad korrigering
* Andra orsaker

#### <a name="resolution"></a>Lösning

Om fel uppstår under en uppdatering som körs när den startar på Linux, kontrollera utdata från den berörda datorn kör jobbet. Du kan hitta felmeddelanden från din dator Pakethanteraren som du kan undersöka och vidta åtgärder för. Uppdateringshantering kräver package manager ska felfri för lyckade uppdateringsdistributioner.

Paketet uppdateringar kan i vissa fall kan störa uppdatera Management hindrar en distribution från att slutföras. Om du ser som du måste antingen undanta dessa paket från framtida uppdateringskörningar eller installera dem manuellt själv.

Om du inte kan lösa ett problem med uppdatering, göra en kopia av följande loggfil och bevara den **innan** nästa uppdateringsdistributionen startar i felsökningssyfte:

```
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="next-steps"></a>Nästa steg

Om du inte fick ditt problem eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure-forumen](https://azure.microsoft.com/support/forums/)
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure-kontot för att förbättra kundtjänstupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Du kan arkivera en incident i Azure-supporten om du behöver mer hjälp. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **hämta stöder**.