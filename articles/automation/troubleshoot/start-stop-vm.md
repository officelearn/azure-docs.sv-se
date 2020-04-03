---
title: Felsöka start- och stoppa virtuella datorer – Azure Automation
description: Den här artikeln innehåller information om felsökning av start- och stoppa virtuella datorer i Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 73a9680cc570179c47b527a4844488da69193cb3
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586102"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Felsöka start-/stopp-virtuella datorer under starttimmarslösning

## <a name="scenario-the-startstop-vm-solution-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Scenario: Lösningen för start/stop-virtuella datorer kan inte distribueras korrekt

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
2. En princip finns på plats som inte tillåter distribution av start/stop-virtuella datorer-lösningen.
3. `Microsoft.OperationsManagement`Resurstyperna `Microsoft.Insights`, `Microsoft.Automation` eller resursen registreras inte.
4. Din Log Analytics-arbetsyta har ett lås på den.
5. Du har en inaktuell version av AzureRM-moduler eller Start/Stop-lösningen.

### <a name="resolution"></a>Lösning

Läs följande lista för potentiella lösningar på ditt problem eller platser att titta på:

1. Automation-konton måste vara unika inom en Azure-region, även om de finns i olika resursgrupper. Kontrollera dina befintliga Automation-konton i målregionen.
2. En befintlig princip förhindrar att en resurs som krävs för att start/stop-VM-lösningen ska kunna distribueras. Gå till dina principtilldelningar i Azure-portalen och kontrollera om du har en principtilldelning som inte tillåter distribution av den här resursen. Mer information om detta finns i [RequestDisallowedByPolicy](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md).
3. För att distribuera start/stop-vm-lösningen måste din prenumeration registreras i följande Azure-resursnamnområden:
    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Se [Lös fel för registrering av resursleverantörer](../../azure-resource-manager/templates/error-register-resource-provider.md) om du vill veta mer om fel när du registrerar leverantörer.
4. Om du har ett lås på din Log Analytics-arbetsyta går du till arbetsytan i Azure-portalen och tar bort eventuella lås på resursen.
5. Om lösningarna ovan inte löser problemet följer du instruktionerna under [Uppdatera lösningen](../automation-solution-vm-management.md#update-the-solution) för att distribuera Start/Stop-lösningen igen.

## <a name="scenario-all-vms-fail-to-startstop"></a><a name="all-vms-fail-to-startstop"></a>Scenario: Alla virtuella datorer kan inte starta/stoppa

### <a name="issue"></a>Problem

Du har konfigurerat Start/Stop VM-lösningen, men den startar eller stoppar inte alla konfigurerade virtuella datorer.

### <a name="cause"></a>Orsak

Det här felet kan orsakas av något av följande orsaker:

1. Ett schema är inte korrekt konfigurerat
2. RunAs-kontot kanske inte är korrekt konfigurerat
3. En runbook kan ha stött på fel
4. De virtuella datorerna kan ha uteslutits

### <a name="resolution"></a>Lösning

Läs följande lista för potentiella lösningar på ditt problem eller platser att titta på:

* Kontrollera att du har konfigurerat ett schema för start/stop-vm-lösningen. Mer information om hur du konfigurerar ett schema finns i artikeln [Scheman.](../automation-schedules.md)

* Kontrollera [jobbströmmarna](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) för att leta efter eventuella fel. Gå till ditt Automation-konto i portalen och välj **Jobb** under **Processautomation**. Leta efter jobb från någon av följande runbooks på sidan **Jobb:**

  * AutoStop_CreateAlert_Child
  * AutoStop_CreateAlert_Parent
  * AutoStop_Disable
  * AutoStop_VM_Child
  * ScheduledStartStop_Base_Classic
  * ScheduledStartStop_Child_Classic
  * ScheduledStartStop_Child
  * ScheduledStartStop_Parent
  * SequencedStartStop_Parent

* Verifiera att ditt [RunAs-konto](../manage-runas-account.md) har rätt behörighet till de virtuella datorer som du försöker starta eller stoppa. Mer information om hur du kontrollerar behörigheterna för en resurs finns i [Snabbstart: Visa roller som tilldelats en användare med Azure-portalen](../../role-based-access-control/check-access.md). Du måste ange program-ID:et för tjänstens huvudnamn som används av kontot Kör som. Du kan hämta det här värdet genom att gå till ditt Automation-konto i Azure-portalen, välja **Kör som konton** under **Kontoinställningar** och klicka på lämpligt Kör som konto.

* Virtuella datorer kanske inte startas eller stoppas om de uttryckligen utesluts. Exkluderade virtuella datorer som anges i **variabeln External_ExcludeVMNames** i Automation-kontot som lösningen distribueras till. I följande exempel visas hur du kan fråga det värdet med PowerShell.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Scenario: Vissa av mina virtuella datorer kan inte starta eller stoppa

### <a name="issue"></a>Problem

Du har konfigurerat Start/Stop VM-lösningen, men den startar eller stoppar inte några av de konfigurerade virtuella datorerna.

### <a name="cause"></a>Orsak

Det här felet kan orsakas av något av följande orsaker:

1. Om sekvensscenariot med användning kan en tagg saknas eller vara felaktig
2. Den virtuella datorn kan uteslutas
3. RunAs-kontot kanske inte har tillräckligt med behörigheter för den virtuella datorn
4. Den virtuella datorn kan ha något som hindrade den från att starta eller stoppa

### <a name="resolution"></a>Lösning

Läs följande lista för potentiella lösningar på ditt problem eller platser att titta på:

* När du använder [sekvensscenariot för](../automation-solution-vm-management.md) start/stop-datorn under avdylningstimmarslösning måste du se till att varje virtuell dator som du vill starta eller stoppa har rätt tagg. Kontrollera att de virtuella datorer som `sequencestart` du vill starta har taggen `sequencestop` och de virtuella datorer som du vill stoppa har taggen. Båda taggarna kräver ett positivt heltalsvärde. Du kan använda en fråga som liknar följande exempel för att leta efter alla virtuella datorer med taggarna och deras värden.

  ```powershell-interactive
  Get-AzureRmResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Virtuella datorer kanske inte startas eller stoppas om de uttryckligen utesluts. Exkluderade virtuella datorer som anges i **variabeln External_ExcludeVMNames** i Automation-kontot som lösningen distribueras till. I följande exempel visas hur du kan fråga det värdet med PowerShell.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* För att starta och stoppa virtuella datorer måste Kontot RunAs för Automation-kontot ha lämpliga behörigheter till den virtuella datorn. Mer information om hur du kontrollerar behörigheterna för en resurs finns i [Snabbstart: Visa roller som tilldelats en användare med Azure-portalen](../../role-based-access-control/check-access.md). Du måste ange program-ID:et för tjänstens huvudnamn som används av kontot Kör som. Du kan hämta det här värdet genom att gå till ditt Automation-konto i Azure-portalen, välja **Kör som konton** under **Kontoinställningar** och klicka på lämpligt Kör som konto.

* Om den virtuella datorn har problem med att starta eller frigöra kan det här beteendet orsakas av ett problem på själva den virtuella datorn. Några exempel eller potentiella problem är, en uppdatering tillämpas när du försöker stänga, en tjänst hänger sig och mycket mer). Navigera till vm-resursen och kontrollera **aktivitetsloggarna** för att se om det finns några fel i loggarna. Du kan också försöka logga in på den virtuella datorn för att se om det finns några fel i händelseloggarna. Mer information om felsökning av den virtuella datorn finns i [Felsöka virtuella Azure-datorer](../../virtual-machines/troubleshooting/index.yml)

* Kontrollera [jobbströmmarna](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) för att leta efter eventuella fel. Gå till ditt Automation-konto i portalen och välj **Jobb** under **Processautomation**.

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Scenario: Min anpassade runbook kan inte starta eller stoppa mina virtuella datorer

### <a name="issue"></a>Problem

Du har skapat en anpassad runbook eller hämtat en från PowerShell-galleriet och den fungerar inte som den ska.

### <a name="cause"></a>Orsak

Orsaken till misslyckandet kan vara en av många saker. Gå till ditt Automation-konto i Azure-portalen och välj **Jobb** under **Process Automation**. Leta efter jobb från runbooken på sidan **Jobb** för att visa eventuella jobbfel.

### <a name="resolution"></a>Lösning

Vi rekommenderar att du använder [start/stopp-virtuella datorer under starttimmar](../automation-solution-vm-management.md) för att starta och stoppa virtuella datorer i Azure Automation. Den här lösningen är skapad av Microsoft. Anpassade runbooks stöds inte av Microsoft. Du kan hitta en lösning för din anpassade runbook genom att besöka felsökningsartikeln för [runbook.](runbooks.md) Den här artikeln innehåller allmän vägledning och felsökning för runbooks av alla typer. Kontrollera [jobbströmmarna](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) för att leta efter eventuella fel. Gå till ditt Automation-konto i portalen och välj **Jobb** under **Processautomation**.

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Scenario: Virtuella datorer startar eller stoppar inte i rätt ordning

### <a name="issue"></a>Problem

De virtuella datorer som du har konfigurerat i lösningen startar eller stoppas inte i rätt ordning.

### <a name="cause"></a>Orsak

Detta orsakas av felaktig taggning på de virtuella datorerna.

### <a name="resolution"></a>Lösning

Vidta följande åtgärder för att se till att lösningen är korrekt konfigurerad.

1. Se till att alla virtuella datorer `sequencestart` `sequencestop` som ska startas eller stoppas har en tagg, beroende på din situation. Dessa taggar behöver ett positivt heltal som värde. Virtuella datorer bearbetas i stigande ordning baserat på det här värdet.
2. Kontrollera att resursgrupperna för de virtuella datorerna `External_Start_ResourceGroupNames` som `External_Stop_ResourceGroupNames` ska startas eller stoppas finns i variablerna eller, beroende på din situation.
3. Testa ändringarna genom att `SequencedStartStop_Parent` köra runbooken med parametern WHATIF inställd på Sant för att förhandsgranska ändringarna.

Mer detaljerade och ytterligare instruktioner om hur du använder lösningen för att starta och stoppa virtuella datorer i följd finns [i Start/Stop-virtuella datorer i följd](../automation-solution-vm-management.md).

## <a name="scenario-startstop-vm-job-fails-with-403-forbidden-status"></a><a name="403"></a>Scenario: Start/Stop VM-jobbet misslyckas med 403 förbjuden status

### <a name="issue"></a>Problem

Du hittar jobb som `403 forbidden` misslyckades med ett fel för start/stopp-virtuella datorer under starttimmars lösningskörningsböcker.

### <a name="cause"></a>Orsak

Det här problemet kan orsakas av ett felaktigt konfigurerat eller utgånget kör som konto. Det kan också bero på otillräckliga behörigheter till vm-resurser av Automation-konton kör som konto.

### <a name="resolution"></a>Lösning

Om du vill kontrollera att ditt Run As-konto är korrekt konfigurerat går du till ditt Automation-konto i Azure-portalen och väljer **Kör som konton** under **Kontoinställningar**. Här ser du statusen för din körning som konton, om ett Kör som-konto är felaktigt konfigurerat eller har upphört att gälla kommer statusen att visa detta.

Om ditt Run As-konto är felkonfigurerat bör du ta bort och återskapa ditt Run As-konto. Se [Hantera Azure Automation Run As-konton](../manage-runas-account.md).

Om certifikatet har upphört att gälla för ditt Kör som-konto följer du stegen i [självasignerade certifikatförnyelse](../manage-runas-account.md#cert-renewal) för att förnya certifikatet.

Problemet kan bero på saknade behörigheter. Mer information om hur du kontrollerar behörigheterna för en resurs finns i [Snabbstart: Visa roller som tilldelats en användare med Azure-portalen](../../role-based-access-control/check-access.md). Du måste ange program-ID:et för tjänstens huvudnamn som används av kontot Kör som. Du kan hämta det här värdet genom att gå till ditt Automation-konto i Azure-portalen, välja **Kör som konton** under **Kontoinställningar** och klicka på lämpligt Kör som konto.

## <a name="scenario-my-problem-isnt-listed-above"></a><a name="other"></a>Scenario: Mitt problem visas inte ovan

### <a name="issue"></a>Problem

Du uppstår ett problem eller ett oväntat resultat när du använder start-/stopp-virtuella datorer under ledig tid-lösning som inte finns med på den här sidan.

### <a name="cause"></a>Orsak

Många gånger fel kan orsakas av att använda en gammal och föråldrad version av lösningen.

> [!NOTE]
> Start/Stop-virtuella datorer under ledig tid-lösningen har testats med Azure-moduler som importeras till ditt Automation-konto när du distribuerar lösningen. Lösningen fungerar för närvarande inte med nyare versioner av Azure-modulen. Detta påverkar bara det Automation-konto som du använder för att köra start-/stopp-virtuella datorer under ledig tid-lösning. Du kan fortfarande använda nyare versioner av Azure-modulen i dina andra Automation-konton, enligt beskrivningen i [Så här uppdaterar du Azure PowerShell-moduler i Azure Automation](../automation-update-azure-modules.md)

### <a name="resolution"></a>Lösning

För att lösa många fel rekommenderar vi att du tar bort och uppdaterar lösningen. Mer information om hur du uppdaterar lösningen finns i [Uppdatera start/stopp-virtuella datorer under ledig tid.](../automation-solution-vm-management.md#update-the-solution) Dessutom kan du kontrollera [jobbströmmarna](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) för att leta efter eventuella fel. Gå till ditt Automation-konto i portalen och välj **Jobb** under **Processautomation**.

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure-forumen](https://azure.microsoft.com/support/forums/)
* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Om du behöver mer hjälp kan du lämna in en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj **Hämta support**.
