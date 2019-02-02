---
title: Felsöka starta och stoppa virtuella datorer med Azure Automation
description: Den här artikeln innehåller information om hur du felsöker startar och stoppar virtuella datorer i Azure Automation
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 01/30/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ef2a782a19dd319de346f14d6189759d0a26686c
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55666760"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Felsöka den Starta/stoppa virtuella datorer utanför timmar lösning

## <a name="all-vms-fail-to-startstop"></a>Scenario: Det gick inte att starta/stoppa alla virtuella datorer

### <a name="issue"></a>Problem

Du har konfigurerat lösningen Starta/Stoppa virtuell dator, men det inte starta eller stoppa alla virtuella datorer konfigureras.

### <a name="cause"></a>Orsak

Det här felet kan bero på något av följande orsaker:

1. Ett schema har inte konfigurerats korrekt
2. RunAs-kontot kanske inte är korrekt konfigurerad
3. En runbook kan ha stöter på fel
4. De virtuella datorerna har undantagits

### <a name="resolution"></a>Lösning

Granska följande lista innehåller tänkbara lösningar på ditt problem eller platser att söka:

* Kontrollera att ett schema för lösningen Starta/Stoppa virtuell dator har konfigurerats korrekt. Läs hur du konfigurerar ett schema i den [scheman](../automation-schedules.md) artikeln.

* Kontrollera jobbströmmar för runbooks du söker efter eventuella fel. Gå till ditt Automation-konto i portalen och välj **jobb** under **Processautomatisering**. Från den **jobb** sidan Sök efter jobb från någon av de följande runbooks:

  * AutoStop_CreateAlert_Child
  * AutoStop_CreateAlert_Parent
  * AutoStop_Disable
  * AutoStop_VM_Child
  * ScheduledStartStop_Base_Classic
  * ScheduledStartStop_Child_Classic
  * ScheduledStartStop_Child
  * ScheduledStartStop_Parent
  * SequencedStartStop_Parent

* Kontrollera din [RunAs-kontot](../manage-runas-account.md) har rätt behörighet till de virtuella datorerna som du försöker starta eller stoppa. Läs hur du kontrollerar behörigheterna för en resurs i [snabbstarten: Visa roller som tilldelats till en användare med Azure portal](../../role-based-access-control/check-access.md). Du måste ange det program-Id för tjänstens huvudnamn som används av kör som-kontot. Du kan hämta det här värdet genom att gå till ditt Automation-konto i Azure portal, välja **kör som-konton** under **kontoinställningar** och klicka på lämplig kör som-kontot.

* Virtuella datorer kan inte startas eller stoppas om de är att uttryckligen är undantagen. Exkluderade virtuella datorer vid uppsättningen i den **External_ExcludeVMNames** variabel i Automation-kontot lösningen har distribuerats till. I följande exempel visas hur du kan fråga efter värdet med PowerShell.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="some-vms-fail-to-startstop"></a>Scenario: Några av Mina virtuella datorer kunde inte starta eller stoppa

### <a name="issue"></a>Problem

Du har konfigurerat lösningen Starta/Stoppa virtuell dator, men det inte starta eller stoppa några av de virtuella datorerna konfigureras.

### <a name="cause"></a>Orsak

Det här felet kan bero på något av följande orsaker:

1. Om du använder sekvens-scenariot, kan en tagg vara saknas eller är felaktig
2. Den virtuella datorn kan inte uteslutas
3. RunAs-kontot kanske inte har tillräckligt med behörighet på den virtuella datorn
4. Den virtuella datorn kan ha något som hindrade den från att starta eller stoppa

### <a name="resolution"></a>Lösning

Granska följande lista innehåller tänkbara lösningar på ditt problem eller platser att söka:

* När du använder den [sekvens scenariot](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags) av Starta/Stoppa VM under av timmar lösning, måste du se till att varje virtuell dator som du vill starta eller stoppa har rätt taggen. Se till att de virtuella datorerna som du vill starta har den `sequencestart` tagg och de virtuella datorerna som du vill stoppa har den `sequencestop` tagg. Båda taggarna kräver ett positivt heltalsvärde. Du kan använda en fråga som liknar följande exempel för att söka efter alla virtuella datorer med taggar och deras värden.

  ```powershell-interactive
  Get-AzureRmResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Virtuella datorer kan inte startas eller stoppas om de är att uttryckligen är undantagen. Exkluderade virtuella datorer vid uppsättningen i den **External_ExcludeVMNames** variabel i Automation-kontot lösningen har distribuerats till. I följande exempel visas hur du kan fråga efter värdet med PowerShell.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* För att starta och stoppa virtuella datorer, måste RunAs-kontot för Automation-kontot ha behörighet till den virtuella datorn. Läs hur du kontrollerar behörigheterna för en resurs i [snabbstarten: Visa roller som tilldelats till en användare med Azure portal](../../role-based-access-control/check-access.md). Du måste ange det program-Id för tjänstens huvudnamn som används av kör som-kontot. Du kan hämta det här värdet genom att gå till ditt Automation-konto i Azure portal, välja **kör som-konton** under **kontoinställningar** och klicka på lämplig kör som-kontot.

* Om den virtuella datorn har ett problem startar eller frigörs, kan problemet orsakas av ett problem på Virtuellt datorn. Vissa exempel eller potentiella problem är kan en uppdatering som används vid försök att stänga av, en tjänst låser sig med mera). Navigera till din VM-resurs och markera den **aktivitetsloggar** att se om det finns några fel i loggarna. Du kan även försöka att logga in på den virtuella datorn för att se om det finns några fel i händelseloggarna.

## <a name="custom-runbook"></a>Scenario: Min anpassade runbook misslyckas att starta eller stoppa Mina virtuella datorer

### <a name="issue"></a>Problem

Du har skapat en anpassad runbook eller hämtat en från PowerShell-galleriet och det fungerar inte korrekt.

### <a name="cause"></a>Orsak

Orsaken till felet kan vara en av många saker. Gå till ditt Automation-konto i Azure-portalen och välj **jobb** under **Processautomatisering**. Från den **jobb** sidan, leta efter jobb från din runbook för att visa jobbfel.

### <a name="resolution"></a>Lösning

Vi rekommenderar att du använder den [Starta/stoppa virtuella datorer utanför timmar lösning](../automation-solution-vm-management.md) att starta och stoppa virtuella datorer i Azure Automation. Den här lösningen är skapad av Microsoft. Anpassade runbooks stöds inte av Microsoft. Du kan hitta en lösning för din anpassade runbook genom att besöka den [runbook felsökning](runbooks.md) artikeln. Den här artikeln innehåller allmänna riktlinjer och felsökning av runbook-flöden av alla typer.

## <a name="dont-start-stop-in-sequence"></a>Scenario: Virtuella datorer inte starta eller stoppa i rätt ordning

### <a name="issue"></a>Problem

De virtuella datorerna som du har konfigurerat i lösningen inte starta eller stoppa i rätt ordning.

### <a name="cause"></a>Orsak

Detta orsakas av felaktig taggning på virtuella datorer.

### <a name="resolution"></a>Lösning

Vidta följande steg för att kontrollera att lösningen är korrekt konfigurerade.

1. Se till att alla virtuella datorer till startas eller stoppas har en `sequencestart` eller `sequencestop` tagg, beroende på din situation. Dessa taggar måste ett positivt heltal som värde. Virtuella datorer som hanteras i stigande ordning baserat på det här värdet.
2. Kontrollera att resursgrupper för de virtuella datorerna startas eller stoppas finns i den `External_Start_ResourceGroupNames` eller `External_Stop_ResourceGroupNames` variabler, beroende på din situation.
3. Testa ändringarna genom att köra den `SequencedStartStop_Parent` runbook med parametern WHATIF angetts till True för att förhandsgranska dina ändringar.

Mer detaljerad och ytterligare instruktioner om hur du använder lösningen att starta och stoppa virtuella datorer i följd finns i [Starta/stoppa virtuella datorer i följd](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags).

## <a name="403"></a>Scenario: Starta/Stoppa VM jobbet misslyckas med 403 otillåtna status 

### <a name="issue"></a>Problem

Du hittar jobb som misslyckades med ett `403 forbidden` fel för den Starta/stoppa virtuella datorer utanför timmar lösning runbooks.

### <a name="cause"></a>Orsak

Det här problemet kan orsakas av ett felaktigt konfigurerad eller har upphört att gälla kör som-konto. Det kan också vara på grund av otillräckliga behörigheter till VM-resurser med Automation-konton kör som-kontot.

### <a name="resolution"></a>Lösning

Kontrollera ditt kör som-konto har konfigurerats korrekt genom gå till ditt Automation-konto i Azure-portalen och välj **kör som-konton** under **kontoinställningar**. Här visas status för ditt kör som-konton, om en Kör som-kontot är felaktigt konfigurerad eller upphört att gälla status visas här.

Om ditt kör som-konto är [felkonfigurerad](../manage-runas-account.md#misconfiguration), bör du ta bort och återskapa ditt kör som-konto.

Om certifikatet har upphört att gälla för ditt kör som-konto, följer du stegen som visas i [självsignerade certifikatförnyelse](../manage-runas-account.md#cert-renewal) att förnya certifikatet.

Problemet kan bero på behörigheter som saknas. Läs hur du kontrollerar behörigheterna för en resurs i [snabbstarten: Visa roller som tilldelats till en användare med Azure portal](../../role-based-access-control/check-access.md). Du måste ange det program-Id för tjänstens huvudnamn som används av kör som-kontot. Du kan hämta det här värdet genom att gå till ditt Automation-konto i Azure portal, välja **kör som-konton** under **kontoinställningar** och klicka på lämplig kör som-kontot.

## <a name="other"></a>Scenario: Mitt problem visas inte i listan ovan

### <a name="issue"></a>Problem

Du får en fråga eller ett oväntat resultat när du använder Starta/stoppa virtuella datorer vid låg belastning på nätverket lösning som inte visas på den här sidan.

### <a name="cause"></a>Orsak

Många gånger fel kan orsakas av använder en gammal och inaktuell version av lösningen.

### <a name="resolution"></a>Lösning

För att lösa många fel, rekommenderar vi att ta bort och uppdatera lösningen. Läs hur du uppdaterar lösningen i [uppdatera Starta/stoppa virtuella datorer under av timmar lösning](../automation-solution-vm-management.md#update-the-solution).

## <a name="next-steps"></a>Nästa steg

Om du inte ser ditt problem eller inte kan lösa problemet besöker du någon av följande kanaler för ytterligare support:

* Få svar från Azure-experter via [Azure-forumen](https://azure.microsoft.com/support/forums/)
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure-kontot för att förbättra kundtjänstupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Om du behöver mer hjälp kan öppna du en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **hämta stöder**.
