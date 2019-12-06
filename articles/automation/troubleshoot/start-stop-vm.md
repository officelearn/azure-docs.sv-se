---
title: Felsöka start och stopp av virtuella datorer med Azure Automation
description: Den här artikeln innehåller information om hur du felsöker start och stopp av virtuella datorer i Azure Automation
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1817d8e060f944b1bcc31c8ea9eb4fbcff58a165
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850116"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Felsök lösningen starta/stoppa virtuella datorer under låg tid

## <a name="deployment-failure"></a>Scenario: Starta/stoppa VM-lösningen kan inte distribueras korrekt

### <a name="issue"></a>Problem

När du distribuerar en lösning för att [Starta/stoppa virtuella datorer under](../automation-solution-vm-management.md)inläsningen av timmar får du ett av följande fel:

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

Distributioner kan Miss lyckas på grund av en av följande orsaker:

1. Det finns redan ett Automation-konto med samma namn i den valda regionen.
2. En princip är på plats som inte tillåter distribution av lösningen starta/stoppa virtuella datorer.
3. Resurs typerna `Microsoft.OperationsManagement`, `Microsoft.Insights`eller `Microsoft.Automation` har inte registrerats.
4. Din Log Analytics-arbetsyta har ett lås på den.
5. Du har en inaktuell version av AzureRM-moduler eller starta/stoppa-lösningen.

### <a name="resolution"></a>Upplösning

Läs följande lista för eventuella lösningar på problemet eller platser att titta på:

1. Automation-konton måste vara unika inom en Azure-region, även om de finns i olika resurs grupper. Kontrol lera dina befintliga Automation-konton i mål regionen.
2. En befintlig princip förhindrar att en resurs som krävs för att starta/stoppa VM-lösningen ska distribueras. Gå till princip tilldelningarna i Azure Portal och kontrol lera om du har en princip tilldelning som inte tillåter distributionen av den här resursen. Mer information om detta finns i [RequestDisallowedByPolicy](../../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md).
3. För att kunna distribuera lösningen starta/stoppa virtuell dator måste din prenumeration registreras till följande Azure-resurs namnrum:
    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Se, [Lös fel för registrering av resurs leverantör](../../azure-resource-manager/resource-manager-register-provider-errors.md) för att lära dig mer om fel vid registrering av leverantörer.
4. Om du har ett lås på Log Analytics arbets ytan går du till din arbets yta i Azure Portal och tar bort eventuella lås på resursen.
5. Om lösningarna ovan inte löser problemet följer du anvisningarna under [Uppdatera lösningen](../automation-solution-vm-management.md#update-the-solution) för att omdistribuera start-/stopp lösningen.

## <a name="all-vms-fail-to-startstop"></a>Scenario: alla virtuella datorer kan inte starta/stoppa

### <a name="issue"></a>Problem

Du har konfigurerat lösningen starta/stoppa virtuell dator, men den startar eller stoppar inte alla konfigurerade virtuella datorer.

### <a name="cause"></a>Orsak

Felet kan bero på någon av följande orsaker:

1. Ett schema har inte kon figurer ATS korrekt
2. RunAs-kontot kanske inte är korrekt konfigurerat
3. En Runbook kan ha stött på fel
4. De virtuella datorerna kan ha uteslutits

### <a name="resolution"></a>Upplösning

Läs följande lista för eventuella lösningar på problemet eller platser att titta på:

* Kontrol lera att du har konfigurerat ett schema korrekt för lösningen starta/stoppa VM. Information om hur du konfigurerar ett schema finns i artikeln [om schemaläggning](../automation-schedules.md) .

* Kontrol lera [jobb strömmarna](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) för att leta efter eventuella fel. I portalen går du till ditt Automation-konto och väljer **jobb** under **process automatisering**. På sidan **jobb** letar du efter jobb från någon av följande Runbooks:

  * AutoStop_CreateAlert_Child
  * AutoStop_CreateAlert_Parent
  * AutoStop_Disable
  * AutoStop_VM_Child
  * ScheduledStartStop_Base_Classic
  * ScheduledStartStop_Child_Classic
  * ScheduledStartStop_Child
  * ScheduledStartStop_Parent
  * SequencedStartStop_Parent

* Kontrol lera att ditt kör som- [konto](../manage-runas-account.md) har rätt behörigheter till de virtuella datorer som du försöker starta eller stoppa. Information om hur du kontrollerar behörigheter för en resurs finns i [snabb start: Visa roller tilldelade till en användare med hjälp av Azure Portal](../../role-based-access-control/check-access.md). Du måste ange program-ID för tjänstens huvud namn som används av kör som-kontot. Du kan hämta det här värdet genom att gå till ditt Automation-konto i Azure Portal, välja **Kör som-konton** under **konto inställningar** och klicka på lämpligt kör som-konto.

* Det går inte att starta eller stoppa virtuella datorer om de uttryckligen utesluts. Exkluderade virtuella datorer i uppsättningen i **External_ExcludeVMNames** -variabeln i Automation-kontot som lösningen distribueras till. I följande exempel visas hur du kan fråga det värdet med PowerShell.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="some-vms-fail-to-startstop"></a>Scenario: vissa av mina virtuella datorer kan inte startas eller stoppas

### <a name="issue"></a>Problem

Du har konfigurerat lösningen starta/stoppa virtuell dator, men den startar eller stoppar inte några av de virtuella datorerna som kon figurer ATS.

### <a name="cause"></a>Orsak

Felet kan bero på någon av följande orsaker:

1. Om du använder Sequence-scenariot kan en tagg saknas eller vara felaktig
2. Den virtuella datorn kan undantas
3. RunAs-kontot har kanske inte tillräckligt med behörigheter på den virtuella datorn
4. Den virtuella datorn kan ha något som avbrutit den från att starta eller stoppa

### <a name="resolution"></a>Upplösning

Läs följande lista för eventuella lösningar på problemet eller platser att titta på:

* När du använder [sekvens scenariot](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags) för lösningen starta/stoppa virtuell dator under inaktive ring av timmar, måste du se till att varje virtuell dator som du vill starta eller stoppa har rätt tagg. Se till att de virtuella datorer som du vill starta har taggen `sequencestart` och de virtuella datorer som du vill stoppa har `sequencestop`-taggen. Båda taggarna kräver ett positivt heltals värde. Du kan använda en fråga som liknar följande exempel för att söka efter alla virtuella datorer med taggarna och deras värden.

  ```powershell-interactive
  Get-AzureRmResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Det går inte att starta eller stoppa virtuella datorer om de uttryckligen utesluts. Exkluderade virtuella datorer i uppsättningen i **External_ExcludeVMNames** -variabeln i Automation-kontot som lösningen distribueras till. I följande exempel visas hur du kan fråga det värdet med PowerShell.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* För att starta och stoppa virtuella datorer måste kör som-kontot för Automation-kontot ha nödvändig behörighet för den virtuella datorn. Information om hur du kontrollerar behörigheter för en resurs finns i [snabb start: Visa roller tilldelade till en användare med hjälp av Azure Portal](../../role-based-access-control/check-access.md). Du måste ange program-ID för tjänstens huvud namn som används av kör som-kontot. Du kan hämta det här värdet genom att gå till ditt Automation-konto i Azure Portal, välja **Kör som-konton** under **konto inställningar** och klicka på lämpligt kör som-konto.

* Om den virtuella datorn har problem med att starta eller att allokera kan det här beteendet orsakas av ett problem på den virtuella datorn. Några exempel eller potentiella problem är att en uppdatering tillämpas när du försöker stänga av, en tjänst låser sig och mer). Navigera till den virtuella dator resursen och kontrol lera **aktivitets loggarna** för att se om det finns några fel i loggarna. Du kan också försöka logga in på den virtuella datorn för att se om det finns några fel i händelse loggarna. Mer information om hur du felsöker din virtuella dator finns i [Felsöka Azure Virtual Machines](../../virtual-machines/troubleshooting/index.md)

* Kontrol lera [jobb strömmarna](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) för att leta efter eventuella fel. I portalen går du till ditt Automation-konto och väljer **jobb** under **process automatisering**.

## <a name="custom-runbook"></a>Scenario: min anpassade Runbook kan inte starta eller stoppa mina virtuella datorer

### <a name="issue"></a>Problem

Du har skapat en anpassad Runbook eller hämtat en från PowerShell-galleriet och fungerar inte som den ska.

### <a name="cause"></a>Orsak

Orsaken till felet kan vara något av många saker. Gå till ditt Automation-konto i Azure Portal och välj **jobb** under **process automatisering**. På sidan **jobb** letar du efter jobb från din Runbook för att visa eventuella jobb haverier.

### <a name="resolution"></a>Upplösning

Vi rekommenderar att du använder [lösningen starta/stoppa virtuella datorer under låg belastning](../automation-solution-vm-management.md) för att starta och stoppa virtuella datorer i Azure Automation. Den här lösningen har skapats av Microsoft. Anpassade Runbooks stöds inte av Microsoft. Du kan hitta en lösning för din anpassade Runbook genom att gå till [fel söknings](runbooks.md) artikeln för Runbook. Den här artikeln innehåller allmän vägledning och fel sökning för Runbooks av alla typer. Kontrol lera [jobb strömmarna](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) för att leta efter eventuella fel. I portalen går du till ditt Automation-konto och väljer **jobb** under **process automatisering**.

## <a name="dont-start-stop-in-sequence"></a>Scenario: virtuella datorer startar eller stoppas inte i rätt ordning

### <a name="issue"></a>Problem

De virtuella datorer som du har konfigurerat i lösningen startar eller stoppas inte i rätt ordning.

### <a name="cause"></a>Orsak

Detta beror på felaktig taggning på de virtuella datorerna.

### <a name="resolution"></a>Upplösning

Utför följande steg för att säkerställa att lösningen är korrekt konfigurerad.

1. Se till att alla virtuella datorer som startas eller stoppas har en `sequencestart`-eller `sequencestop`-tagg, beroende på din situation. Taggarna måste ha ett positivt heltal som värde. De virtuella datorerna bearbetas i stigande ordning baserat på det här värdet.
2. Kontrol lera att resurs grupperna för de virtuella datorerna som ska startas eller stoppas finns i `External_Start_ResourceGroupNames` eller `External_Stop_ResourceGroupNames` variabler, beroende på din situation.
3. Testa ändringarna genom att köra `SequencedStartStop_Parent` Runbook med parametern WHATIF inställd på True för att förhandsgranska ändringarna.

Mer detaljerad information och ytterligare instruktioner om hur du använder lösningen för att starta och stoppa virtuella datorer i sekvenser finns i [Starta/stoppa virtuella datorer i följd](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags).

## <a name="403"></a>Scenario: det går inte att starta/stoppa VM-jobbet med status 403 förbjuden

### <a name="issue"></a>Problem

Du hittar jobb som misslyckades med ett `403 forbidden` fel för Runbooks för att starta/stoppa virtuella datorer under inaktive ring av timmar.

### <a name="cause"></a>Orsak

Det här problemet kan orsakas av ett felaktigt konfigurerat eller utgånget kör som-konto. Det kan också bero på otillräcklig behörighet till VM-resurserna av kör som-kontot för Automation-konton.

### <a name="resolution"></a>Upplösning

Om du vill kontrol lera att kör som-kontot är korrekt konfigurerat går du till ditt Automation-konto i Azure Portal och väljer **Kör som-konton** under **konto inställningar**. Här visas statusen för dina kör som-konton, om ett Kör som-konto är felaktigt konfigurerat eller upphör att gälla visas detta.

Om kör som-kontot är [felkonfigurerat](../manage-runas-account.md#misconfiguration)bör du ta bort och återskapa kör som-kontot.

Om certifikatet har upphört att gälla för ditt kör som-konto följer du stegen i listan med [självsignerat certifikat för förnyelse](../manage-runas-account.md#cert-renewal) av certifikatet.

Problemet kan bero på att behörigheter saknas. Information om hur du kontrollerar behörigheter för en resurs finns i [snabb start: Visa roller tilldelade till en användare med hjälp av Azure Portal](../../role-based-access-control/check-access.md). Du måste ange program-ID för tjänstens huvud namn som används av kör som-kontot. Du kan hämta det här värdet genom att gå till ditt Automation-konto i Azure Portal, välja **Kör som-konton** under **konto inställningar** och klicka på lämpligt kör som-konto.

## <a name="other"></a>Scenario: mitt problem visas inte ovan

### <a name="issue"></a>Problem

Du får ett problem eller oväntat resultat när du använder Starta/stoppa virtuella datorer när de inte används-lösningen som inte visas på den här sidan.

### <a name="cause"></a>Orsak

Många gånger kan fel uppstå genom att använda en gammal och inaktuell version av lösningen.

> [!NOTE]
> Den Starta/stoppa virtuella datorer när de inte används lösningen har testats med Azure-modulerna som importeras till ditt Automation-konto när du distribuerar lösningen. Lösningen fungerar för närvarande inte med nyare versioner av Azure-modulen. Detta påverkar bara det Automation-konto som du använder för att köra Starta/stoppa virtuella datorer när de inte används-lösningen. Du kan fortfarande använda nyare versioner av Azure-modulen i andra Automation-konton, enligt beskrivningen i [så här uppdaterar du Azure PowerShell moduler i Azure Automation](../automation-update-azure-modules.md)

### <a name="resolution"></a>Upplösning

För att lösa många fel rekommenderar vi att du tar bort och uppdaterar lösningen. Information om hur du uppdaterar lösningen finns i [Uppdatera tjänsten starta/stoppa virtuella datorer vid inaktive ring av timmar](../automation-solution-vm-management.md#update-the-solution). Dessutom kan du kontrol lera [jobb strömmarna](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) för att söka efter eventuella fel. I portalen går du till ditt Automation-konto och väljer **jobb** under **process automatisering**.

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure-forumen](https://azure.microsoft.com/support/forums/)
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure-kontot för att förbättra kundtjänstupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Om du behöver mer hjälp kan du skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.
