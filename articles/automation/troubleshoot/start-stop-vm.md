---
title: Felsöka lösningen starta/stoppa virtuella datorer under låg tid
description: Den här artikeln innehåller information om fel sökning av lösningen starta/stoppa virtuell dator.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 003c2c5a2c09957e7a3a4ac0a26b87a9ac43dace
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679150"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Felsök lösningen starta/stoppa virtuella datorer under låg tid

Den här artikeln innehåller information om fel sökning av problem som uppstår när du arbetar med en lösning för att starta/stoppa virtuella datorer under låg tid.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installations anvisningar för AZ-modulen på Hybrid Runbook Worker finns i [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med hjälp av [hur du uppdaterar Azure PowerShell moduler i Azure Automation](../automation-update-azure-modules.md).

## <a name="scenario-the-startstop-vms-during-off-hours-solution-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Scenario: lösningen starta/stoppa virtuella datorer under låg drifts tid kan inte distribueras korrekt

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

Distributioner kan inte utföras på grund av en av följande orsaker:

1. Det finns redan ett Automation-konto med samma namn i den valda regionen.
2. En princip tillåter inte distribution av lösningen starta/stoppa virtuella datorer under låg tid.
3. Resurs `Microsoft.OperationsManagement`typen `Microsoft.Insights`, eller `Microsoft.Automation` är inte registrerad.
4. Din Log Analytics-arbetsyta är låst.
5. Du har en inaktuell version av AzureRM-modulerna eller tjänsten starta/stoppa virtuella datorer vid inaktive ring av timmar.

### <a name="resolution"></a>Lösning

Läs följande korrigeringar för möjliga lösningar på problemet:

* Automation-konton måste vara unika inom en Azure-region, även om de finns i olika resurs grupper. Kontrol lera dina befintliga Automation-konton i mål regionen.
* En befintlig princip förhindrar en resurs som krävs för att lösningen starta/stoppa virtuella datorer under drift timmar ska kunna distribueras. Gå till princip tilldelningarna i Azure Portal och kontrol lera om du har en princip tilldelning som inte tillåter distributionen av den här resursen. Mer information om detta finns i [RequestDisallowedByPolicy](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md).
* Om du vill distribuera lösningen för att starta/stoppa virtuella datorer måste din prenumeration registreras till följande Azure-resurs namnrum:

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Mer information om fel vid registrering av leverantörer finns i [lösa fel för registrering av resurs leverantör](../../azure-resource-manager/templates/error-register-resource-provider.md) .
* Om du har ett lås på Log Analytics arbets ytan går du till din arbets yta i Azure Portal och tar bort eventuella lås på resursen.
* Om lösningarna ovan inte löser problemet följer du anvisningarna under [Uppdatera lösningen](../automation-solution-vm-management.md#update-the-solution) för att omdistribuera start-/stopp lösningen.

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>Scenario: alla virtuella datorer kan inte startas eller stoppas

### <a name="issue"></a>Problem

Du har konfigurerat lösningen starta/stoppa virtuella datorer vid inaktive ring av timmar, men den startar eller stoppar inte alla virtuella datorer.

### <a name="cause"></a>Orsak

Felet kan bero på någon av följande orsaker:

1. Ett schema har inte kon figurer ATS korrekt.
2. Kör som-kontot kanske inte har kon figurer ATS korrekt.
3. En Runbook kan ha stött på fel.
4. De virtuella datorerna kan ha uteslutits.

### <a name="resolution"></a>Lösning

Läs följande lista för möjliga lösningar på problemet:

* Kontrol lera att du har konfigurerat ett schema korrekt för lösningen starta/stoppa virtuella datorer under inaktive ring av timmar. Information om hur du konfigurerar ett schema finns i artikeln [om schemaläggning](../automation-schedules.md) .

* Kontrol lera [jobb strömmarna](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) för att leta efter eventuella fel. Sök efter jobb från någon av följande Runbooks:

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* Kontrol lera att ditt [Kör som-konto](../manage-runas-account.md) har rätt behörigheter till de virtuella datorer som du försöker starta eller stoppa. Information om hur du kontrollerar behörigheter för en resurs finns i [snabb start: Visa roller tilldelade till en användare med hjälp av Azure Portal](../../role-based-access-control/check-access.md). Du måste ange program-ID för tjänstens huvud namn som används av kör som-kontot. Du kan hämta det här värdet genom att gå till ditt Automation-konto i Azure Portal, välja **Kör som-konton** under **konto inställningar**och klicka på lämpligt kör som-konto.

* Det går inte att starta eller stoppa virtuella datorer om de uttryckligen utesluts. Undantagna virtuella datorer anges i `External_ExcludeVMNames` variabeln i Automation-kontot som lösningen har distribuerats till. I följande exempel visas hur du kan fråga det värdet med PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Scenario: vissa av mina virtuella datorer kan inte startas eller stoppas

### <a name="issue"></a>Problem

Du har konfigurerat lösningen starta/stoppa virtuella datorer under låg tid, men den startar eller stoppar inte några av de virtuella datorerna som kon figurer ATS.

### <a name="cause"></a>Orsak

Felet kan bero på någon av följande orsaker:

1. I sekvens-scenariot kan en tagg saknas eller vara felaktig.
2. Den virtuella datorn kan vara Exkluderad.
3. Kör som-kontot kanske inte har tillräcklig behörighet på den virtuella datorn.
4. Den virtuella datorn kan ha ett problem som hindrade den från att starta eller stoppa.

### <a name="resolution"></a>Lösning

Läs följande lista för eventuella lösningar på problemet eller platser att titta på:

* När du använder [sekvens-scenariot](../automation-solution-vm-management.md) för lösningen starta/stoppa virtuella datorer vid inaktive ring av timmar, måste du se till att varje virtuell dator som du vill starta eller stoppa har rätt tagg. Se till att de virtuella datorer som du vill starta har `sequencestart` taggen och att de virtuella datorer som du vill stoppa `sequencestop` har taggen. Båda taggarna kräver ett positivt heltals värde. Du kan använda en fråga som liknar följande exempel för att söka efter alla virtuella datorer med taggarna och deras värden.

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Det är inte säkert att de virtuella datorerna startas eller stoppas om de uttryckligen utesluts. Undantagna virtuella datorer anges i `External_ExcludeVMNames` variabeln i Automation-kontot som lösningen har distribuerats till. I följande exempel visas hur du kan fråga det värdet med PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* För att starta och stoppa virtuella datorer måste kör som-kontot för Automation-kontot ha nödvändig behörighet för den virtuella datorn. Information om hur du kontrollerar behörigheter för en resurs finns i [snabb start: Visa roller tilldelade till en användare med hjälp av Azure Portal](../../role-based-access-control/check-access.md). Du måste ange program-ID för tjänstens huvud namn som används av kör som-kontot. Du kan hämta det här värdet genom att gå till ditt Automation-konto i Azure Portal, välja **Kör som-konton** under **konto inställningar** och klicka på lämpligt kör som-konto.

* Om den virtuella datorn har problem med att starta eller att allokera kan det finnas ett problem på den virtuella datorn. En uppdatering tillämpas till exempel när den virtuella datorn försöker stängas av, en tjänst låser sig med mera. Navigera till den virtuella dator resursen och kontrol lera **aktivitets loggarna** för att se om det finns några fel i loggarna. Du kan också försöka logga in på den virtuella datorn för att se om det finns några fel i händelse loggarna. Mer information om hur du felsöker din virtuella dator finns i [Felsöka Azure Virtual Machines](../../virtual-machines/troubleshooting/index.yml)

* Kontrol lera [jobb strömmarna](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) för att leta efter eventuella fel. I portalen går du till ditt Automation-konto och väljer **jobb** under **process automatisering**.

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Scenario: min anpassade Runbook kan inte starta eller stoppa mina virtuella datorer

### <a name="issue"></a>Problem

Du har skapat en anpassad Runbook eller hämtat en från PowerShell-galleriet och fungerar inte som den ska.

### <a name="cause"></a>Orsak

Det kan finnas många orsaker till det här problemet. Gå till ditt Automation-konto i Azure Portal och välj **jobb** under **process automatisering**. På sidan jobb letar du efter jobb från din Runbook för att visa eventuella jobb haverier.

### <a name="resolution"></a>Lösning

Vi rekommenderar att du:

* Använd [lösningen starta/stoppa virtuella datorer under låg tid](../automation-solution-vm-management.md) för att starta och stoppa virtuella datorer i Azure Automation. Den här lösningen har skapats av Microsoft. 

* Tänk på att Microsoft inte stöder anpassade Runbooks. Du kan hitta en lösning för din anpassade Runbook från [fel sökning av Runbook](runbooks.md). Kontrol lera [jobb strömmarna](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) för att leta efter eventuella fel. 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Scenario: virtuella datorer startar eller stoppas inte i rätt ordning

### <a name="issue"></a>Problem

De virtuella datorer som du har konfigurerat i lösningen startar eller stoppas inte i rätt ordning.

### <a name="cause"></a>Orsak

Det här problemet orsakas av felaktig taggning på de virtuella datorerna.

### <a name="resolution"></a>Lösning

Utför följande steg för att säkerställa att lösningen är korrekt konfigurerad.

1. Se till att alla virtuella datorer som startas eller stoppas har en `sequencestart` or `sequencestop` -tagg, beroende på din situation. Taggarna måste ha ett positivt heltal som värde. De virtuella datorerna bearbetas i stigande ordning baserat på det här värdet.
2. Kontrol lera att resurs grupperna för de virtuella datorerna som ska startas eller stoppas `External_Start_ResourceGroupNames` finns `External_Stop_ResourceGroupNames` i variablerna eller, beroende på din situation.
3. Testa ändringarna genom att köra `SequencedStartStop_Parent` runbooken med `WHATIF` parametern inställt på sant för att förhandsgranska ändringarna.
4. Mer information om hur du använder lösningen för att starta och stoppa virtuella datorer i sekvenser finns i [Starta/stoppa virtuella datorer i följd](../automation-solution-vm-management.md).

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>Scenario: jobbet starta/stoppa virtuella datorer vid inaktivitet Miss lyckas med 403-otillåtet fel

### <a name="issue"></a>Problem

Du hittar jobb som misslyckades med ett `403 forbidden` fel för att starta/stoppa virtuella datorer under inläsningar av timmar-Runbooks.

### <a name="cause"></a>Orsak

Det här problemet kan orsakas av ett felaktigt konfigurerat eller utgånget kör som-konto. Det kan också bero på otillräcklig behörighet till VM-resurserna av kör som-kontot.

### <a name="resolution"></a>Lösning

Kontrol lera att kör som-kontot är korrekt konfigurerat genom att gå till ditt Automation-konto i Azure Portal och välja **Kör som-konton** under **konto inställningar**. Om ett Kör som-konto har kon figurer ATS felaktigt eller har upphört att gälla, visar status villkoret.

Om kör som-kontot är felkonfigurerat bör du ta bort och återskapa kör som-kontot. Se [hantera Azure Automation kör som-konton](../manage-runas-account.md).

Om certifikatet har upphört att gälla för ditt kör som-konto, se steg i [självsignerat certifikat förnyelse](../manage-runas-account.md#cert-renewal) för att förnya certifikatet.

Om behörigheter saknas, se [snabb start: Visa roller tilldelade till en användare med hjälp av Azure Portal](../../role-based-access-control/check-access.md). Du måste ange program-ID för tjänstens huvud namn som används av kör som-kontot. Du kan hämta det här värdet genom att gå till ditt Automation-konto i Azure Portal, välja **Kör som-konton** under **konto inställningar**och klicka på lämpligt kör som-konto.

## <a name="scenario-my-problem-isnt-listed-above"></a><a name="other"></a>Scenario: mitt problem visas inte ovan

### <a name="issue"></a>Problem

Du får ett problem eller ett oväntat resultat när du använder lösningen starta/stoppa virtuella datorer under ledighet som inte visas på den här sidan.

### <a name="cause"></a>Orsak

Många gånger kan fel uppstå genom att använda en gammal och inaktuell version av lösningen.

> [!NOTE]
> Lösningen starta/stoppa virtuella datorer under låg tid har testats med Azure-modulerna som importeras till ditt Automation-konto när du distribuerar lösningen. Lösningen fungerar för närvarande inte med nyare versioner av Azure-modulen. Detta påverkar bara det Automation-konto som du använder för att köra lösningen starta/stoppa virtuella datorer under låg tid. Du kan fortfarande använda nyare versioner av Azure-modulen i andra Automation-konton, enligt beskrivningen i [så här uppdaterar du Azure PowerShell moduler i Azure Automation](../automation-update-azure-modules.md)

### <a name="resolution"></a>Lösning

För att lösa många fel rekommenderar vi att du tar bort och [uppdaterar lösningen starta/stoppa virtuella datorer under låg belastning](../automation-solution-vm-management.md#update-the-solution). Dessutom kan du kontrol lera [jobb strömmarna](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) för att söka efter eventuella fel. 

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet ovan eller inte kan lösa problemet kan du prova någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Azure-forum](https://azure.microsoft.com/support/forums/).
* Anslut till [@AzureSupport](https://twitter.com/azuresupport), det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Filen en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.