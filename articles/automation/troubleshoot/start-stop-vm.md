---
title: Felsöka start-/stopp-virtuella datorer under starttimmarslösning
description: Den här artikeln innehåller information om felsökning av Start/Stop VM-lösningen.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 003c2c5a2c09957e7a3a4ac0a26b87a9ac43dace
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679150"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Felsöka start-/stopp-virtuella datorer under starttimmarslösning

Den här artikeln innehåller information om felsökningsproblem som uppstår när du arbetar med start-/stopp-virtuella datorer under lösningen av lediga timmar.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](../automation-update-azure-modules.md).

## <a name="scenario-the-startstop-vms-during-off-hours-solution-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Scenario: Start/Stop virtuella datorer under lediga timmar-lösningen kan inte distribueras korrekt

### <a name="issue"></a>Problem

När du distribuerar [start-/stopp-virtuella datorer under starttimmar](../automation-solution-vm-management.md)får du något av följande fel:

```error
Account already exists in another resourcegroup in a subscription. ResourceGroupName: [MyResourceGroup].
```

```error
Resource 'StartStop_VM_Notification' was disallowed by policy. Policy identifiers: '[{\\\"policyAssignment\\\":{\\\"name\\\":\\\"[MyPolicyName]".
```

```error
The subscription is not registered to use namespace 'Microsoft.OperationsManagement'.
```

```error
The subscription is not registered to use namespace 'Microsoft.Insights'.
```

```error
The scope '/subscriptions/000000000000-0000-0000-0000-00000000/resourcegroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView' cannot perform write operation because following scope(s) are locked: '/subscriptions/000000000000-0000-0000-0000-00000000/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView'. Please remove the lock and try again
```

```error
A parameter cannot be found that matches parameter name 'TagName'
```

```error
Start-AzureRmVm : Run Login-AzureRmAccount to login
```

### <a name="cause"></a>Orsak

Distributioner kan misslyckas på grund av något av följande skäl:

1. Det finns redan ett Automation-konto med samma namn i den valda regionen.
2. En princip tillåter inte distribution av start/stopp-virtuella datorer under starttimmarslösning.
3. Resurstypen `Microsoft.OperationsManagement`, `Microsoft.Insights`eller `Microsoft.Automation` är inte registrerad.
4. Din Log Analytics-arbetsyta är låst.
5. Du har en inaktuell version av AzureRM-modulerna eller start/stop-virtuella datorer under starttimmarslösning.

### <a name="resolution"></a>Lösning

Läs följande korrigeringar för möjliga lösningar på problemet:

* Automation-konton måste vara unika inom en Azure-region, även om de finns i olika resursgrupper. Kontrollera dina befintliga Automation-konton i målregionen.
* En befintlig princip förhindrar att en resurs som krävs för att start-/stopp-virtuella datorer under avdämningstimmar ska distribueras. Gå till dina principtilldelningar i Azure-portalen och kontrollera om du har en principtilldelning som inte tillåter distribution av den här resursen. Mer information om detta finns i [RequestDisallowedByPolicy](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md).
* Om du vill distribuera start-/stopp-virtuella datorer måste din prenumeration registreras i följande Azure-resursnamnområden:

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Se [Lösa fel för registrering av resursleverantörer](../../azure-resource-manager/templates/error-register-resource-provider.md) om du vill veta mer om fel när du registrerar leverantörer.
* Om du har ett lås på din Log Analytics-arbetsyta går du till arbetsytan i Azure-portalen och tar bort eventuella lås på resursen.
* Om lösningarna ovan inte löser problemet följer du instruktionerna under [Uppdatera lösningen](../automation-solution-vm-management.md#update-the-solution) för att distribuera Start/Stop-lösningen igen.

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>Scenario: Alla virtuella datorer kan inte starta eller stoppa

### <a name="issue"></a>Problem

Du har konfigurerat start/stopp-start-/stopp-start-datorerna under starttimmarslösningen, men den startar eller stoppar inte alla virtuella datorer.

### <a name="cause"></a>Orsak

Det här felet kan orsakas av något av följande orsaker:

1. Ett schema är inte korrekt konfigurerat.
2. Kör som-kontot kanske inte är korrekt konfigurerat.
3. En runbook kan ha stött på fel.
4. De virtuella datorerna kan ha uteslutits.

### <a name="resolution"></a>Lösning

Läs följande lista för möjliga lösningar på problemet:

* Kontrollera att du har konfigurerat ett schema för start-/stopp-virtuella datorer under starttimmarslösningen. Mer information om hur du konfigurerar ett schema finns i artikeln [Scheman.](../automation-schedules.md)

* Kontrollera [jobbströmmarna](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) för att leta efter eventuella fel. Leta efter jobb från någon av följande runbooks:

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* Kontrollera att ditt [Run As-konto](../manage-runas-account.md) har rätt behörighet till de virtuella datorer som du försöker starta eller stoppa. Mer information om hur du kontrollerar behörigheterna för en resurs finns i [Snabbstart: Visa roller som tilldelats en användare med Azure-portalen](../../role-based-access-control/check-access.md). Du måste ange program-ID:et för tjänstens huvudnamn som används av kontot Kör som. Du kan hämta det här värdet genom att gå till ditt Automation-konto i Azure-portalen, välja **Kör som konton** under **Kontoinställningar**och klicka på lämpligt Kör som-konto.

* Virtuella datorer kanske inte startas eller stoppas om de uttryckligen utesluts. Exkluderade virtuella datorer `External_ExcludeVMNames` anges i variabeln i automationskontot som lösningen distribueras till. I följande exempel visas hur du kan fråga det värdet med PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Scenario: Vissa av mina virtuella datorer kan inte starta eller stoppa

### <a name="issue"></a>Problem

Du har konfigurerat start/stopp-start-/stopp-start-/start-datorerna under starttimmarslösningen, men den startar eller stoppar inte några av de konfigurerade virtuella datorerna.

### <a name="cause"></a>Orsak

Det här felet kan orsakas av något av följande orsaker:

1. I sekvensscenariot kan en tagg saknas eller vara felaktig.
2. Den virtuella datorn kan uteslutas.
3. Kör som-kontot kanske inte har tillräckligt med behörigheter för den virtuella datorn.
4. Den virtuella datorn kan ha ett problem som hindrade den från att starta eller stoppa.

### <a name="resolution"></a>Lösning

Läs följande lista för potentiella lösningar på ditt problem eller platser att titta på:

* När du använder [sekvensscenariot för](../automation-solution-vm-management.md) start/stopp-virtuella datorer under avtidslösning måste du se till att varje virtuell dator som du vill starta eller stoppa har rätt tagg. Kontrollera att de virtuella datorer som `sequencestart` du vill starta har taggen `sequencestop` och de virtuella datorer som du vill stoppa har taggen. Båda taggarna kräver ett positivt heltalsvärde. Du kan använda en fråga som liknar följande exempel för att leta efter alla virtuella datorer med taggarna och deras värden.

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Virtuella datorer kanske inte startas eller stoppas om de uttryckligen utesluts. Exkluderade virtuella datorer `External_ExcludeVMNames` anges i variabeln i automationskontot som lösningen distribueras till. I följande exempel visas hur du kan fråga det värdet med PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* För att starta och stoppa virtuella datorer måste kontot Kör som för Automation-kontot ha rätt behörighet till den virtuella datorn. Mer information om hur du kontrollerar behörigheterna för en resurs finns i [Snabbstart: Visa roller som tilldelats en användare med Azure-portalen](../../role-based-access-control/check-access.md). Du måste ange program-ID:et för tjänstens huvudnamn som används av kontot Kör som. Du kan hämta det här värdet genom att gå till ditt Automation-konto i Azure-portalen, välja **Kör som konton** under **Kontoinställningar** och klicka på lämpligt Kör som-konto.

* Om den virtuella datorn har problem med att starta eller frigöra kan det finnas ett problem på själva den virtuella datorn. Till exempel tillämpas en uppdatering när den virtuella datorn försöker stänga av, en tjänst låser sig med mera. Navigera till vm-resursen och kontrollera **aktivitetsloggarna** för att se om det finns några fel i loggarna. Du kan också försöka logga in på den virtuella datorn för att se om det finns några fel i händelseloggarna. Mer information om felsökning av den virtuella datorn finns i [Felsöka virtuella Azure-datorer](../../virtual-machines/troubleshooting/index.yml)

* Kontrollera [jobbströmmarna](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) för att leta efter eventuella fel. Gå till ditt Automation-konto i portalen och välj **Jobb** under **Processautomation**.

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Scenario: Min anpassade runbook kan inte starta eller stoppa mina virtuella datorer

### <a name="issue"></a>Problem

Du har skapat en anpassad runbook eller hämtat en från PowerShell-galleriet och den fungerar inte som den ska.

### <a name="cause"></a>Orsak

Det kan finnas många orsaker till felet. Gå till ditt Automation-konto i Azure-portalen och välj **Jobb** under **Process Automation**. Leta efter jobb från runbooken på sidan Jobb för att visa eventuella jobbfel.

### <a name="resolution"></a>Lösning

Det rekommenderas att:

* Använd [start-/stopp-virtuella datorer under starttimmar](../automation-solution-vm-management.md) för att starta och stoppa virtuella datorer i Azure Automation. Den här lösningen är skapad av Microsoft. 

* Tänk på att Microsoft inte stöder anpassade runbooks. Du kan hitta en lösning för din anpassade runbook från [runbook felsökning](runbooks.md). Kontrollera [jobbströmmarna](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) för att leta efter eventuella fel. 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Scenario: Virtuella datorer startar eller stoppar inte i rätt ordning

### <a name="issue"></a>Problem

De virtuella datorer som du har konfigurerat i lösningen startar eller stoppas inte i rätt ordning.

### <a name="cause"></a>Orsak

Det här problemet orsakas av felaktig taggning på de virtuella datorerna.

### <a name="resolution"></a>Lösning

Vidta följande åtgärder för att se till att lösningen är korrekt konfigurerad.

1. Se till att alla virtuella datorer `sequencestart` `sequencestop` som ska startas eller stoppas har en tagg, beroende på din situation. Dessa taggar behöver ett positivt heltal som värde. Virtuella datorer bearbetas i stigande ordning baserat på det här värdet.
2. Kontrollera att resursgrupperna för de virtuella datorerna `External_Start_ResourceGroupNames` som `External_Stop_ResourceGroupNames` ska startas eller stoppas finns i variablerna eller, beroende på din situation.
3. Testa ändringarna genom att `SequencedStartStop_Parent` köra runbooken `WHATIF` med parametern inställd på Sant för att förhandsgranska ändringarna.
4. Mer information om hur du använder lösningen för att starta och stoppa virtuella datorer i följd finns [i Start/Stop virtuella datorer i följd](../automation-solution-vm-management.md).

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>Scenario: Start/Stop virtuella datorer under lediga timmar misslyckas jobbet med 403 förbjudna fel

### <a name="issue"></a>Problem

Du hittar jobb som `403 forbidden` misslyckades med ett fel för start/stopp virtuella datorer under lediga timmar lösningskörningsböcker.

### <a name="cause"></a>Orsak

Det här problemet kan orsakas av ett felaktigt konfigurerat eller utgånget Kör som-konto. Det kan också bero på otillräckliga behörigheter till vm-resurser av kontot Kör som.

### <a name="resolution"></a>Lösning

Om du vill kontrollera att ditt Run As-konto är korrekt konfigurerat går du till ditt Automation-konto i Azure-portalen och väljer **Kör som konton** under **Kontoinställningar**. Om ett Run As-konto är felaktigt konfigurerat eller utgånget visar statusen villkoret.

Om ditt Run As-konto är felkonfigurerat bör du ta bort och återskapa ditt Run As-konto. Se [Hantera Azure Automation Run As-konton](../manage-runas-account.md).

Om certifikatet har upphört att gälla för ditt Run As-konto läser du stegen i [Självsignerad certifikatförnyelse](../manage-runas-account.md#cert-renewal) för att förnya certifikatet.

Om det saknas behörigheter läser du [Snabbstart: Visa roller som tilldelats en användare med Azure-portalen](../../role-based-access-control/check-access.md). Du måste ange program-ID:et för tjänsthuvudhuvudet som används av kontot Kör som. Du kan hämta det här värdet genom att gå till ditt Automation-konto i Azure-portalen, välja **Kör som konton** under **Kontoinställningar**och klicka på lämpligt Kör som-konto.

## <a name="scenario-my-problem-isnt-listed-above"></a><a name="other"></a>Scenario: Mitt problem visas inte ovan

### <a name="issue"></a>Problem

Du uppstår ett problem eller ett oväntat resultat när du använder start-/stopp-virtuella datorer under starttimmar som inte finns med på den här sidan.

### <a name="cause"></a>Orsak

Många gånger fel kan orsakas av att använda en gammal och föråldrad version av lösningen.

> [!NOTE]
> Start/Stop-virtuella datorer under lediga timmar har testats med Azure-moduler som importeras till ditt Automation-konto när du distribuerar lösningen. Lösningen fungerar för närvarande inte med nyare versioner av Azure-modulen. Detta påverkar bara det Automation-konto som du använder för att köra start-/stopp-virtuella datorer under ledig tid. Du kan fortfarande använda nyare versioner av Azure-modulen i dina andra Automation-konton, enligt beskrivningen i [Så här uppdaterar du Azure PowerShell-moduler i Azure Automation](../automation-update-azure-modules.md)

### <a name="resolution"></a>Lösning

För att lösa många fel rekommenderar vi att du tar bort och [uppdaterar start-/stopp-virtuella datorer under starttimmarslösning](../automation-solution-vm-management.md#update-the-solution). Dessutom kan du kontrollera [jobbströmmarna](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) för att leta efter eventuella fel. 

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet ovan eller inte kan lösa problemet kan du prova någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Azure Forum](https://azure.microsoft.com/support/forums/).
* Anslut [@AzureSupport](https://twitter.com/azuresupport)med , det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Arkivera en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj **Hämta support**.