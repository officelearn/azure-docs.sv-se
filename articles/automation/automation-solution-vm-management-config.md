---
title: Azure Automation konfigurera starta/stoppa virtuella datorer under låg tid
description: I den här artikeln beskrivs hur du konfigurerar Starta/stoppa virtuella datorer när de inte används-lösningen så att den stöder olika användnings fall eller scenarier.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 9842a736cf922e0490f2b0c8acb1d2e5833f3d6c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81604767"
---
# <a name="how-to-configure-startstop-vms-during-off-hours-solution"></a>Så här konfigurerar du en lösning för att starta/stoppa virtuella datorer under låg tid

Med lösningen **Starta/stoppa virtuella datorer under låg tid** kan du:

- [Schemalägg virtuella datorer för start och stopp](#schedule).
- Schemalägg virtuella datorer att starta och stoppa i stigande ordning med [hjälp av Azure-Taggar](#tags) (stöds inte för klassiska virtuella datorer).
- Stoppa virtuella datorer automatiskt baserat på [låg CPU-användning](#cpuutil).

Den här artikeln beskriver hur du konfigurerar lösningen så att den stöder dessa scenarier. Du kan också lära dig hur du utför andra vanliga konfigurations inställningar för lösningen, till exempel:

* [Konfigurera e-postaviseringar](#configure-email-notifications)

* [Lägga till en virtuell dator](#add-a-vm)

* [Undanta en virtuell dator](#exclude-a-vm)

* [Ändra scheman för start och avstängning](#modify-the-startup-and-shutdown-schedules)

## <a name="scenario-1-startstop-vms-on-a-schedule"></a><a name="schedule"></a>Scenario 1: Starta/stoppa virtuella datorer enligt ett schema

Det här scenariot är standard konfigurationen när du först distribuerar lösningen. Du kan till exempel konfigurera det för att stoppa alla virtuella datorer i en prenumeration när du lämnar jobbet på kvällen och starta dem på morgon när du är tillbaka på kontoret. När du konfigurerar scheman för **schemalagda-StartVM** och **schemalagda StopVM** under distributionen, startar och slutar riktade virtuella datorer. Det finns stöd för att konfigurera den här lösningen till att bara stoppa virtuella datorer, se [ändra scheman för start och avstängning](#modify-the-startup-and-shutdown-schedules) för att lära dig hur du konfigurerar ett anpassat schema.

> [!NOTE]
> Tids zonen är din aktuella tidszon när du konfigurerar parametern för schema tid. Den lagras dock i UTC-format i Azure Automation. Du behöver inte göra någon tids zons konvertering eftersom detta hanteras under distributionen.

Du styr vilka virtuella datorer som omfattas genom att konfigurera följande variabler: **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**och **External_ExcludeVMNames**.

Du kan aktivera antingen åtgärden för en prenumeration och en resurs grupp, eller rikta in en speciell lista över virtuella datorer, men inte båda.

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Rikta in dig på Start-och stopp åtgärder mot en prenumeration och resurs grupp

1. Konfigurera `External_Stop_ResourceGroupNames` variablerna `External_ExcludeVMNames` och för att ange de virtuella mål datorerna.

2. Aktivera och uppdatera **schemalagda StartVM** -och **schemalagda StopVM-** scheman.

3. Kör **ScheduledStartStop_Parent** Runbook med **Åtgärds** parameter fältet inställt på **Start** och fältet **WHATIF** parameter är inställt på sant för att förhandsgranska ändringarna.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>Ange start-och stopp åtgärd per VM-lista

1. Kör **ScheduledStartStop_Parent** Runbook med **åtgärd** inställd på **Starta**, Lägg till en kommaavgränsad lista med virtuella datorer i parameter fältet **VMList** och ange sedan värdet true för parametern **WHATIF** . Förhandsgranska ändringarna.

2. Konfigurera `External_ExcludeVMNames` variabeln med en kommaavgränsad lista över virtuella datorer (VM1, VM2, VM3).

3. Det här scenariot följer inte `External_Start_ResourceGroupNames` variablerna och `External_Stop_ResourceGroupnames` . I det här scenariot måste du skapa ett eget Automation-schema. Mer information finns i [Schemalägga en Runbook i Azure Automation](../automation/automation-schedules.md).

    > [!NOTE]
    > Värdet för **mål ResourceGroup namn** lagras som värdet för både `External_Start_ResourceGroupNames` och. `External_Stop_ResourceGroupNames` För ytterligare granularitet kan du ändra var och en av dessa variabler för att rikta in sig på olika resurs grupper. Använd `External_Start_ResourceGroupNames`och Använd `External_Stop_ResourceGroupNames` för åtgärden stoppa för att starta. Virtuella datorer läggs automatiskt till i Start-och stopp scheman.

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>Scenario 2: Starta/stoppa virtuella datorer i följd med hjälp av Taggar

I en miljö som innehåller två eller flera komponenter på flera virtuella datorer som har stöd för en distribuerad arbets belastning, är det viktigt med stöd för den sekvens i vilken komponenter startas och stoppas i ordning. Du kan utföra det här scenariot genom att utföra följande steg:

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Rikta in dig på Start-och stopp åtgärder mot en prenumeration och resurs grupp

1. Lägg till `sequencestart` en och `sequencestop` en-tagg med ett positivt heltals värde för virtuella datorer `External_Start_ResourceGroupNames` som `External_Stop_ResourceGroupNames` är riktade till-och variabler. Start-och stopp åtgärderna utförs i stigande ordning. Information om hur du taggar en virtuell dator finns i [tagga en virtuell Windows-dator i Azure](../virtual-machines/windows/tag.md) och [tagga en virtuell Linux-dator i Azure](../virtual-machines/linux/tag.md).

2. Ändra schemana **Sequenced-StartVM** och **Sequenced-StopVM** till det datum och den tid som uppfyller dina krav och aktivera schemat.

3. Kör den **SequencedStartStop_Parent** Runbook med **åtgärd** inställd på **Starta** och **WHATIF** inställd på sant för att förhandsgranska ändringarna.

4. Förhandsgranska åtgärden och gör nödvändiga ändringar innan du implementerar de virtuella datorerna i produktionen. När du är klar kan du köra runbooken manuellt med parametern inställt på **falskt**, eller låta `Sequenced-StartVM` Automation `Sequenced-StopVM` -schemat köras automatiskt efter ditt schema.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>Ange start-och stopp åtgärd per VM-lista

1. Lägg till `sequencestart` en och `sequencestop` en-tagg med ett positivt heltals värde för de virtuella datorer som `VMList` du planerar att lägga till i parametern.

2. Kör **SequencedStartStop_Parent** Runbook med **åtgärd** inställd på **Starta**, Lägg till en kommaavgränsad lista med virtuella datorer i parameter fältet **VMList** och ange sedan **WHATIF** till true. Förhandsgranska ändringarna.

3. Konfigurera `External_ExcludeVMNames` variabeln med en kommaavgränsad lista över virtuella datorer (VM1, VM2, VM3).

4. Det här scenariot följer inte `External_Start_ResourceGroupNames` variablerna och `External_Stop_ResourceGroupnames` . I det här scenariot måste du skapa ett eget Automation-schema. Mer information finns i [Schemalägga en Runbook i Azure Automation](../automation/automation-schedules.md).

5. Förhandsgranska åtgärden och gör nödvändiga ändringar innan du implementerar de virtuella datorerna i produktionen. När du är klar kan du köra **övervakning-och-diagnostik/Monitor-Action-groupsrunbook** med parametern inställt på **falskt**. Alternativt kan du låta Automation- `Sequenced-StartVM` schemat `Sequenced-StopVM` köras automatiskt efter ditt schema.

## <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>Scenario 3: Starta/stoppa automatiskt baserat på processor användning

Den här lösningen kan hjälpa till att hantera kostnaden för att köra Azure Resource Manager och klassiska virtuella datorer i din prenumeration genom att utvärdera virtuella datorer som inte används under perioder med låg belastning, t. ex. efter timmar, och sedan stänga ned dem automatiskt om processor belastningen är lägre än en angiven procent andel.

Som standard är lösningen förkonfigurerad för att utvärdera procent andelen av processor mått för att se om den genomsnittliga användningen är 5 procent eller mindre. Det här scenariot styrs av följande variabler och kan ändras om standardvärdena inte uppfyller dina krav:

* `External_AutoStop_MetricName`
* `External_AutoStop_Threshold`
* `External_AutoStop_TimeAggregationOperator`
* `External_AutoStop_TimeWindow`
* `External_AutoStop_Frequency`
* `External_AutoStop_Severity`

Du kan aktivera och rikta åtgärden mot en prenumeration och resurs grupp, eller rikta en speciell lista över virtuella datorer.

När du kör **AutoStop_CreateAlert_Parent** Runbook verifierar den att mål prenumerationen, resurs grupperna och de virtuella datorerna finns. Om de virtuella datorerna finns anropar runbooken **AutoStop_CreateAlert_Child** Runbook för varje verifierad virtuell dator med den överordnade runbooken. Denna underordnade Runbook utför följande:

* Skapar en mått varnings regel för varje verifierad virtuell dator.

* Utlöser **AutoStop_VM_Child** Runbook för en viss virtuell dator om processorn sjunker under det konfigurerade tröskelvärdet för angivet tidsintervall. Denna Runbook försöker sedan stoppa den virtuella datorn.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-subscription"></a>För att rikta den automatiska stopp åtgärden mot alla virtuella datorer i en prenumeration

1. Se till att `External_Stop_ResourceGroupNames` variabeln är tom eller anges till * (jokertecken).

2. [Valfritt steg] Om du vill undanta vissa virtuella datorer från automatisk avstängning kan du lägga till en kommaavgränsad lista med namn på virtuella datorer i `External_ExcludeVMNames` variabeln.

3. Aktivera `Schedule_AutoStop_CreateAlert_Parent` schemat så att det körs för att skapa nödvändiga aviserings regler för stoppa virtuell dator för alla virtuella datorer i din prenumeration. Genom att köra den här typen av schema kan du skapa nya mått regler för aviseringar när nya virtuella datorer läggs till i prenumerationen.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-resource-group-or-multiple-resource-groups"></a>För att rikta den automatiska stopp åtgärden mot alla virtuella datorer i en resurs grupp eller flera resurs grupper

1. Lägg till en kommaavgränsad lista över resurs grupp namn till `External_Stop_ResourceGroupNames` variabeln.

2. Om du vill utesluta några av de virtuella datorerna från den automatiska avstängningen kan du lägga till en kommaavgränsad lista med namn på virtuella `External_ExcludeVMNames` datorer i variabeln.

3. Aktivera **Schedule_AutoStop_CreateAlert_Parent** schema för att köra för att skapa de nödvändiga varnings reglerna för att stoppa virtuell dator för alla virtuella datorer i dina resurs grupper. Genom att köra den här åtgärden enligt ett schema kan du skapa nya mått regler som nya virtuella datorer läggs till i resurs grupperna.

### <a name="to-target-the-autostop-action-to-a-list-of-vms"></a>Så här riktar du in åtgärden för autostoppa till en lista över virtuella datorer

1. Skapa ett nytt [schema](shared-resources/schedules.md#creating-a-schedule) och länka det till **AutoStop_CreateAlert_Parent** Runbook och Lägg till en kommaavgränsad lista över namn på virtuella datorer i `VMList` parametern.

2. Om du vill undanta vissa virtuella datorer från den automatiska avstängningen kan du lägga till en kommaavgränsad lista över namn på virtuella datorer i `External_ExcludeVMNames` variabeln.

## <a name="configure-email-notifications"></a>Konfigurera e-postaviseringar

Ändra den åtgärds grupp som skapades under distributionen om du vill ändra e-postaviseringar när lösningen har distribuerats.  

> [!NOTE]
> Prenumerationer i Azure Government molnet har inte stöd för den här lösningens e-postfunktioner.

1. I Azure Portal går du till **övervaka**och sedan **Åtgärds grupper**. Välj den åtgärds grupp som heter **StartStop_VM_Notication**.

    ![Sidan Automation Uppdateringshantering-lösning](media/automation-solution-vm-management/azure-monitor.png)

2. På sidan **StartStop_VM_Notification** klickar du på **Redigera information** under **information**. Då öppnas sidan **e-post/SMS/push/Voice** . Uppdatera e-postadressen och spara ändringarna genom att klicka på **OK** .

    ![Sidan Automation Uppdateringshantering-lösning](media/automation-solution-vm-management/change-email.png)

    Alternativt kan du lägga till ytterligare åtgärder i åtgärds gruppen och läsa mer om åtgärds grupper i [Åtgärds grupper](../azure-monitor/platform/action-groups.md)

Följande är ett exempel på ett e-postmeddelande som skickas när lösningen stänger av virtuella datorer.

![Sidan Automation Uppdateringshantering-lösning](media/automation-solution-vm-management/email.png)

## <a name="addexclude-vms"></a><a name="add-exclude-vms"></a>Lägg till/exkludera virtuella datorer

Lösningen ger möjlighet att lägga till virtuella datorer som ska omfattas av lösningen eller undanta datorer från lösningen.

### <a name="add-a-vm"></a>Lägga till en virtuell dator

Det finns två alternativ som du kan använda för att se till att en virtuell dator ingår i starta/stoppa-lösningen när den körs.

* Varje överordnad [Runbooks](automation-solution-vm-management.md#runbooks) i lösningen har en `VMList` -parameter. Du kan skicka en kommaavgränsad lista över namn på virtuella datorer till den här parametern när du schemalägger lämplig överordnad Runbook för din situation och de virtuella datorerna kommer att inkluderas när lösningen körs.

* Om du vill välja flera virtuella `External_Start_ResourceGroupNames` datorer `External_Stop_ResourceGroupNames` anger du och med namnen på de resurs grupper som innehåller de virtuella datorer som du vill starta eller stoppa. Du kan också ställa in variablerna till värdet för `*` att lösningen ska köras mot alla resurs grupper i prenumerationen.

### <a name="exclude-a-vm"></a>Undanta en virtuell dator

Om du vill utesluta en virtuell dator från lösningen kan du lägga till den `External_ExcludeVMNames` i variabeln. Den här variabeln är en kommaavgränsad lista med vissa virtuella datorer som ska undantas från start-/stopp lösningen. Den här listan är begränsad till 140 virtuella datorer. Om du lägger till fler än 140 virtuella datorer i den här kommaseparerade listan kan virtuella datorer som är inställda på att undantas oavsiktligt startas eller stoppas.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Ändra scheman för start och avstängning

Hantering av start-och avstängnings scheman i den här lösningen följer samma steg som beskrivs i [schemaläggning av en Runbook i Azure Automation](automation-schedules.md). Det måste finnas ett separat schema för att starta och stoppa virtuella datorer.

Det finns stöd för att konfigurera lösningen till att bara stoppa virtuella datorer vid en viss tid. I det här scenariot skapar du bara ett **stopp** schema och inget motsvarande **Start** schema. Om du vill göra det måste du:

1. Se till att du har lagt till resurs grupperna för de virtuella datorerna som `External_Stop_ResourceGroupNames` ska stängas av i variabeln.

2. Skapa ett eget schema för den tid som du vill stänga av de virtuella datorerna.

3. Navigera till **ScheduledStartStop_Parent** Runbook och klicka på **Schemalägg**. På så sätt kan du välja det schema som du skapade i föregående steg.

4. Välj **parametrar och kör inställningar** och Ställ in **Åtgärds** fältet på **stoppa**.

5. Spara ändringarna genom att välja **OK**.

## <a name="next-steps"></a>Nästa steg

* Information om hur du felsöker Starta/stoppa virtuella datorer när de inte används finns i [Felsöka starta/stoppa virtuella datorer](troubleshoot/start-stop-vm.md).

* [Granska](automation-solution-vm-management-logs.md) automatiserings posterna som skrivits till Azure Monitor loggar och Sök frågor i loggen för att analysera statusen för Automation Runbook-jobb från starta/stoppa virtuella datorer.
