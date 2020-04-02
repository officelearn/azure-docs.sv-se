---
title: Konfigurera start-/stopp-start-/stopp-start-/start-vm-lösning
description: I den här artikeln beskrivs hur du konfigurerar start-/stopp-virtuella datorer under ledig tid för att stödja olika användningsfall eller scenarier.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: d3ca8d17d6637f0ab2b5a5d3d7a99ac0beaafd2e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550388"
---
# <a name="how-to-configure-startstop-vms-during-off-hours-solution"></a>Konfigurera start-/stopp-virtuella datorer under starttimmarslösning

Med start-/stopp-virtuella datorer under starttimmarslösning kan du:

- [Schemalägg virtuella datorer så att de startar och stoppar](#schedule).
- Schemalägg virtuella datorer så att de startar och slutar i stigande ordning med hjälp av [Azure-taggar](#tags) (stöds inte för klassiska virtuella datorer).
- Stoppa virtuella datorer automatiskt baserat på [låg CPU-användning](#cpuutil).

I den här artikeln beskrivs hur du konfigurerar lösningen för att stödja dessa scenarier. Du kan också lära dig hur du utför andra vanliga konfigurationsinställningar för lösningen, till exempel:

* [Konfigurera e-postaviseringar](#configure-email-notifications)

* [Lägga till en virtuell dator](#add-a-vm)

* [Undanta en virtuell dator](#exclude-a-vm)

* [Ändra start- och avstängningsscheman](#modify-the-startup-and-shutdown-schedules)

## <a name="scenario-1-startstop-vms-on-a-schedule"></a><a name="schedule"></a>Scenario 1: Start-/stopp-virtuella datorer enligt ett schema

Det här scenariot är standardkonfigurationen när du först distribuerar lösningen. Du kan till exempel konfigurera den så att den stoppa alla virtuella datorer i en prenumeration när du lämnar arbetet på kvällen och starta dem på morgonen när du är tillbaka på kontoret. När du konfigurerar scheman **Schemalagd-StartVM** och **Schemalagd-StopVM** under distributionen startar och stoppar de riktade virtuella datorer. Konfigurera den här lösningen för att bara stoppa virtuella datorer stöds, se [Ändra start- och avstängningsscheman](#modify-the-startup-and-shutdown-schedules) för att lära dig hur du konfigurerar ett anpassat schema.

> [!NOTE]
> Tidszonen är din aktuella tidszon när du konfigurerar parametern för schematid. Den lagras dock i UTC-format i Azure Automation. Du behöver inte göra någon tidszonskonvertering eftersom detta hanteras under distributionen.

Du styr vilka virtuella datorer som omfattas genom att konfigurera följande variabler: **External_Start_ResourceGroupNames,** **External_Stop_ResourceGroupNames**och **External_ExcludeVMNames**.

Du kan aktivera antingen inriktning åtgärden mot en prenumeration och resursgrupp, eller inriktning på en viss lista över virtuella datorer, men inte båda.

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Rikta start- och stoppåtgärder mot en prenumerations- och resursgrupp

1. Konfigurera **variablerna External_Stop_ResourceGroupNames** och **External_ExcludeVMNames** för att ange mål-virtuella datorer.

2. Aktivera och uppdatera schemana **För schemalagd startVM** och **Schemalagd-StopVM.**

3. Kör **ScheduledStartStop_Parent** runbook med parametern ACTION inställd på **start** och parametern WHATIF inställd på **True** för att förhandsgranska ändringarna.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>Rikta in start- och stoppåtgärden efter VM-lista

1. Kör **ScheduledStartStop_Parent** runbook med parametern ACTION inställd **på**start, lägg till en kommaavgränsad lista över virtuella datorer i parametern *VMList* och ställ sedan in parametern WHATIF på **True**. Förhandsgranska ändringarna.

2. Konfigurera **parametern External_ExcludeVMNames** med en kommaavgränsad lista över virtuella datorer (VM1, VM2, VM3).

3. Det här scenariot respekterar inte **variablerna External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupnames.** I det här scenariot måste du skapa ett eget automationsschema. Mer information finns [i Schemalägga en runbook i Azure Automation](../automation/automation-schedules.md).

    > [!NOTE]
    > Värdet för **Mål ResourceGroup-namn** lagras som värde för både **External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupNames**. För ytterligare granularitet kan du ändra var och en av dessa variabler för att rikta olika resursgrupper. Använd **External_Start_ResourceGroupNames External_Stop_ResourceGroupNames**för startåtgärd och för stoppåtgärd **.** Virtuella datorer läggs automatiskt till i start- och stoppscheman.

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>Scenario 2: Starta/stoppa VMS i följd med hjälp av taggar

I en miljö som innehåller två eller flera komponenter på flera virtuella datorer som stöder en distribuerad arbetsbelastning är det viktigt att stödja den sekvens i vilken komponenter startas och stoppas i ordning. Du kan utföra det här scenariot genom att utföra följande steg:

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Rikta start- och stoppåtgärder mot en prenumerations- och resursgrupp

1. Lägg till en **sequencestart** och en **sequencestop-tagg** med ett positivt heltalsvärde i virtuella datorer som är inriktade **på External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupNames** variabler. Start- och stoppåtgärder utförs i stigande ordning. Mer information om hur du taggar en virtuell dator finns [i Tagga en virtuell Windows-dator i Azure](../virtual-machines/windows/tag.md) och [tagga en virtuell Linux-dator i Azure](../virtual-machines/linux/tag.md).

2. Ändra scheman **Sequenced-StartVM** och **Sequenced-StopVM** till det datum och den tid som uppfyller dina krav och aktivera schemat.

3. Kör **SequencedStartStop_Parent** runbook med parametern ACTION inställd på **start** och parametern WHATIF inställd på **True** för att förhandsgranska ändringarna.

4. Förhandsgranska åtgärden och gör nödvändiga ändringar innan du implementerar mot virtuella produktions-datorer. När du är klar kör du runbooken manuellt med parametern inställd på **Falskt**eller låt automationsschemat **Sequenced-StartVM** och **Sequenced-StopVM** köras automatiskt enligt det föreskrivna schemat.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>Rikta in start- och stoppåtgärden efter VM-lista

1. Lägg till en **sequencestart** och en **sequencestop-tagg** med ett positivt heltalsvärde i virtuella datorer som du planerar att lägga till i **parametern VMList.**

2. Kör **SequencedStartStop_Parent** runbook med parametern ACTION inställd **på**start, lägg till en kommaavgränsad lista över virtuella datorer i parametern *VMList* och ställ sedan in parametern WHATIF på **True**. Förhandsgranska ändringarna.

3. Konfigurera **parametern External_ExcludeVMNames** med en kommaavgränsad lista över virtuella datorer (VM1, VM2, VM3).

4. Det här scenariot respekterar inte **variablerna External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupnames.** I det här scenariot måste du skapa ett eget automationsschema. Mer information finns [i Schemalägga en runbook i Azure Automation](../automation/automation-schedules.md).

5. Förhandsgranska åtgärden och gör nödvändiga ändringar innan du implementerar mot virtuella produktions-datorer. När du är klar kör du automatiskt övervaknings-och diagnostik-/övervakning-action-groupsrunbook med parametern inställd på **Falskt**eller låt automationsschemat **Sequenced-StartVM** och **Sequenced-StopVM** köras automatiskt enligt ditt föreskrivna schema.

## <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>Scenario 3: Start/stopp automatiskt baserat på CPU-användning

Den här lösningen kan hjälpa till att hantera kostnaden för att köra virtuella Azure Resource Manager- och Classic-datorer i din prenumeration genom att utvärdera virtuella datorer som inte används under perioder som inte är hög, till exempel efter timmar, och automatiskt stänga av dem om processoranvändningen är mindre än en angiven procentsats.

Som standard är lösningen förkonfigurerad för att utvärdera procent cpu-måttet för att se om det genomsnittliga utnyttjandet är 5 procent eller mindre. Det här scenariot styrs av följande variabler och kan ändras om standardvärdena inte uppfyller dina krav:

* **External_AutoStop_MetricName**

* **External_AutoStop_Threshold**

* **External_AutoStop_TimeAggregationOperator**

* **External_AutoStop_TimeWindow**

* **External_AutoStop_Frequency**

* **External_AutoStop_Severity**

Du kan aktivera och rikta åtgärden mot en prenumerations- och resursgrupp eller inrikta dig på en specifik lista med virtuella datorer.

När du kör **AutoStop_CreateAlert_Parent** runbook verifierar den att den riktade prenumerationen, resursgrupperna och virtuella datorer finns. Om de virtuella datorerna finns anropas **AutoStop_CreateAlert_Child** runbook för varje verifierad virtuell dator av den överordnade runbooken. Den här underordnade runbooken utför följande:

* Skapar en måttaviseringsregel för varje verifierad virtuell dator.

* Utlöser **AutoStop_VM_Child** runbook för en viss virtuell dator om CPU-släppet under det konfigurerade tröskelvärdet för det angivna tidsintervallet. Den här runbooken försöker sedan stoppa den virtuella datorn.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-subscription"></a>Så här riktar du den automatiska stoppåtgärden mot alla virtuella datorer i en prenumeration

1. Se till **att External_Stop_ResourceGroupNames** variabeln är tom eller inställd på * (jokertecken).

2. [Valfritt steg] Om du vill utesluta vissa virtuella datorer från den automatiska avstängningen kan du lägga till en kommaavgränsad lista med VM-namn i **variabeln External_ExcludeVMNames.**

3. Aktivera **Schedule_AutoStop_CreateAlert_Parent** schema för att köras för att skapa de nödvändiga stop VM-måttvarningsreglerna för alla virtuella datorer i din prenumeration. Om du kör detta enligt ett schema kan du skapa nya måttaviseringsregler när nya virtuella datorer läggs till i prenumerationen.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-resource-groupmultiple-resource-groups"></a>Så här inriktar du dig på den automatiska stoppåtgärden mot alla virtuella datorer i en resursgrupp/flera resursgrupper

1. Lägg till en gemensam avgränsad lista med resursgruppnamn i **variabeln External_Stop_ResourceGroupNames.**

2. Om du vill utesluta några av de virtuella datorerna från den automatiska avstängningen kan du också lägga till en kommaavgränsad lista med VM-namn i **External_ExcludeVMNames** variabeln.

3. Aktivera **Schedule_AutoStop_CreateAlert_Parent** schema för att köras för att skapa de nödvändiga **stop VM-måttaviseringsreglerna** för alla virtuella datorer i resursgrupperna. Om du kör detta enligt ett schema kan du skapa nya måttaviseringsregler när nya virtuella datorer läggs till i resursgruppen/resursgrupperna.

### <a name="to-target-the-auto-stop-action-to-a-list-of-vms"></a>Så här inriktar du den automatiska stoppåtgärden på en lista över virtuella datorer

1. Skapa ett nytt [schema](shared-resources/schedules.md#creating-a-schedule) och länka det till **AutoStop_CreateAlert_Parent** runbook, lägga till en kommaavgränsad lista med VM-namn till **parametern VMList.**

2. Om du vill utesluta vissa virtuella datorer från den automatiska avstängningen kan du också lägga till en kommaavgränsad lista med VM-namn i **variabeln External_ExcludeVMNames.**

## <a name="configure-email-notifications"></a>Konfigurera e-postaviseringar

Om du vill ändra e-postmeddelanden när lösningen har distribuerats ändrar du åtgärdsgruppen som skapades under distributionen.  

> [!NOTE]
> Prenumerationer i Azure Government Cloud stöder inte e-postfunktionerna i den här lösningen.

1. I Azure-portalen navigerar du till Övervaka -> åtgärdsgrupper. Markera den åtgärdsgrupp med titeln **StartStop_VM_Notication**.

    ![Lösningssida för hantering av automatiseringsuppdatering](media/automation-solution-vm-management/azure-monitor.png)

2. Klicka på **Redigera information** under **Information**på sidan **StartStop_VM_Notification.** Då öppnas sidan **E-post/SMS/Push/Voice.** Uppdatera e-postadressen och klicka på **OK** för att spara ändringarna.

    ![Lösningssida för hantering av automatiseringsuppdatering](media/automation-solution-vm-management/change-email.png)

    Alternativt kan du lägga till ytterligare åtgärder i åtgärdsgruppen, lära dig mer om åtgärdsgrupper, se [åtgärdsgrupper](../azure-monitor/platform/action-groups.md)

Följande är ett exempel på e-post som skickas när lösningen stänger av virtuella datorer.

![Lösningssida för hantering av automatiseringsuppdatering](media/automation-solution-vm-management/email.png)

## <a name="addexclude-vms"></a><a name="add-exclude-vms"></a>Lägga till/exkluderar virtuella datorer

Lösningen ger möjlighet att lägga till virtuella datorer som ska riktas av lösningen eller specifikt utesluta maskiner från lösningen.

### <a name="add-a-vm"></a>Lägga till en virtuell dator

Det finns två alternativ som du kan använda för att se till att en virtuell dator ingår i Start/Stop-lösningen när den körs.

* Var och en av de överordnade [runbooks](automation-solution-vm-management.md#runbooks) av lösningen har en **VMList** parameter. Du kan skicka en kommaavgränsad lista över VM-namn till den här parametern när du schemalägger lämplig överordnad runbook för din situation och dessa virtuella datorer inkluderas när lösningen körs.

* Om du vill markera flera virtuella datorer anger du **External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupNames** med de resursgruppsnamn som innehåller de virtuella datorer som du vill starta eller stoppa. Du kan också ange `*`det här värdet till , så att lösningen körs mot alla resursgrupper i prenumerationen.

### <a name="exclude-a-vm"></a>Undanta en virtuell dator

Om du vill utesluta en virtuell dator från lösningen kan du lägga till den i **variabeln External_ExcludeVMNames.** Den här variabeln är en kommaavgränsad lista över specifika virtuella datorer som ska uteslutas från Start/Stop-lösningen. Den här listan är begränsad till 140 virtuella datorer. Om du lägger till fler än 140 virtuella datorer i den här kommaavgränsade listan kan virtuella datorer som är inställda på att uteslutas startas eller stoppas av misstag.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Ändra start- och avstängningsscheman

Hantera start- och avstängningsscheman i den här lösningen följer samma steg som beskrivs i [Schemalägga en runbook i Azure Automation](automation-schedules.md). Det måste finnas ett separat schema för att starta och stoppa virtuella datorer.

Det stöds att konfigurera lösningen för att bara stoppa virtuella datorer vid en viss tidpunkt. I det här fallet skapar du bara ett **stoppschema** och ingen motsvarande **Start** schemalagd. Om du vill göra det måste du:

1. Se till att du har lagt till resursgrupperna för de virtuella datorerna att stänga av i **variabeln External_Stop_ResourceGroupNames.**

2. Skapa ditt eget schema för den tid du vill stänga av de virtuella datorerna.

3. Navigera till **ScheduledStartStop_Parent** runbook och klicka på **Schema**. På så sätt kan du välja det schema som du skapade i föregående steg.

4. Välj **Parametrar och kör inställningar** och ange parametern **ACTION** till **Stop**.

5. Spara ändringarna genom att välja **OK**.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du felsöker start-/stopp-virtuella datorer under ledig tid finns i [Felsöka start-/stopp-virtuella datorer](troubleshoot/start-stop-vm.md).

* [Granska](automation-solution-vm-management-logs.md) automationsposterna som skrivits till Azure Monitor Logs och exempelloggsökfrågorna för att analysera statusen för Automation-körningsjobb från start-/stopp-virtuella datorer.
