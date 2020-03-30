---
title: Lär dig hur du 2019 går med i lösningar för uppdateringshantering, ändringsspårning och lager i Azure Automation
description: Lär dig hur du ar ombord på en virtuell Azure-dator med lösningar för uppdateringshantering, ändringsspårning och lager som ingår i Azure Automation
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: cf82dddf281e8e6f1348884702e32330dee4781b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278680"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>Inbyggd uppdateringshantering, ändringsspårning och lagerlösningar

Azure Automation tillhandahåller lösningar för att hantera säkerhetsuppdateringar för operativsystemet, spåra ändringar och inventera vad som är installerat på dina datorer. Det finns många sätt att gå ombord på datorer, du kan gå in på lösningen [från en virtuell dator,](automation-onboard-solutions-from-vm.md) [från att bläddra flera datorer,](automation-onboard-solutions-from-browse.md)från ditt Automation-konto eller genom [runbook](automation-onboard-solutions.md). Den här artikeln beskriver introduktion av dessa lösningar från ditt Automation-konto.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in i Azure på https://portal.azure.com

## <a name="enable-solutions"></a>Aktivera lösningar

Navigera till ditt Automation-konto och välj antingen **Lager-** eller **ändringsspårning** under **Konfigurationshantering**.

Välj log analytics-arbetsytan och automationskontot och klicka på **Aktivera** för att aktivera lösningen. Det tar upp till 15 minuter att aktivera lösningen.

![Inbyggd lagerlösning](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

> [!NOTE]
> När du aktiverar lösningar går det endast att länka en Log Analytics-arbetsyta och ett Automation-konto i vissa regioner.
>
> En lista över mappningspar som stöds finns i [Regionmappning för Automation-konto och Log Analytics-arbetsyta](how-to/region-mappings.md).

Lösningen Ändringsspårning och inventering ger dig möjlighet att [spåra ändringar](automation-vm-change-tracking.md) och [inventering](automation-vm-inventory.md) på dina virtuella datorer. I det här steget aktiverar du lösningen för en virtuell dator.

När meddelanden om ändringsspårning och lagerlösning är klar väljer du **Uppdatera hantering** under **Uppdateringshantering**.

Med lösningen Update Management kan du hantera uppdateringar och korrigeringar för dina virtuella Azure- och hybrid-datorer. Du kan bedöma status för tillgängliga uppdateringar, schemalägga installation av nödvändiga uppdateringar och granska distributionsresultat för att verifiera att uppdateringar har tillämpats på dem.

På sidan Aktivera lösning är den valda logganalysarbetsytan samma arbetsyta som används i föregående steg. Klicka på **Aktivera** för att gå in på lösning för uppdateringshantering. Det tar upp till 15 minuter att aktivera lösningen.

![Uppdaterad lösning](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="scope-configuration"></a>Konfiguration av scope

Varje lösning använder en scopekonfiguration inom arbetsytan för att rikta in sig på de datorer som hämtar lösningen. Scope-konfigurationen är en grupp med en eller flera sparade sökningar som används för att begränsa lösningens omfattning till specifika datorer. Om du vill komma åt scopekonfigurationerna väljer du **Workspace**i ditt Automation-konto under **Relaterade resurser**. Välj **scopekonfigurationer**på arbetsytan under **Arbetsytedatakällor**.

Om den valda arbetsytan inte har lösningarna Uppdateringshantering eller Ändringsspårning ännu skapas följande scopekonfigurationer:

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-uppdateringar**

Om den valda arbetsytan redan har lösningen distribueras inte lösningen och scopekonfigurationen läggs inte till i den.

## <a name="saved-searches"></a>Sparade sökningar

När en dator läggs till i lösningarna för uppdateringshantering eller ändringsspårning och lager läggs de till i en av två sparade sökningar på arbetsytan. Dessa sparade sökningar är frågor som innehåller de datorer som är avsedda för dessa lösningar.

Navigera till arbetsytan Logganalys och välj **Sparade sökningar** under **Allmänt**. De två sparade sökningar som används av dessa lösningar kan ses i följande tabell:

|Namn     |Kategori  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  Ändra Spåra       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Uppdateringar        | Updates__MicrosoftDefaultComputerGroup         |

Välj antingen sparad sökning om du vill visa frågan som används för att fylla i gruppen. Följande bild visar frågan och dess resultat:

![Sparade sökningar](media/automation-onboard-solutions-from-automation-account/savedsearch.png)

## <a name="onboard-azure-vms"></a>Inbyggd Virtuella Azure-datorer

Välj **Lager-** eller **ändringsspårning** under **Konfigurationshantering**i ditt Automation-konto eller **Uppdatera hantering** under **Uppdateringshantering**.

Klicka på **+ Lägg till virtuella Azure-datorer**, välj en eller flera virtuella datorer i listan. Virtuella datorer som inte kan aktiveras är nedtonade och kan inte väljas. Virtuella Azure-datorer kan finnas i valfri region oavsett var ditt Automation-konto finns. Klicka på **Aktivera**på sidan **Aktivera uppdateringshantering.** Den här åtgärden lägger till de valda virtuella datorerna i den datorgrupp som sparats efter lösningen.

![Aktivera virtuella Azure-datorer](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="onboard-a-non-azure-machine"></a>Publicera en virtuell dator utan Azure

Datorer som inte finns i Azure måste läggas till manuellt. Välj **Lager-** eller **ändringsspårning** under **Konfigurationshantering**i ditt Automation-konto eller **Uppdatera hantering** under **Uppdateringshantering**.

Klicka på **Lägg till en icke-Azure-dator**. Den här åtgärden öppnar ett nytt webbläsarfönster med [instruktioner om hur du installerar och konfigurerar Microsoft Monitoring Agent på datorn](../azure-monitor/platform/log-analytics-agent.md) så att datorn kan börja rapportera till lösningen. Om du lägger till en dator som för närvarande hanteras av System Center Operations Manager krävs inte en ny agent, anges arbetsytans information i den befintliga agenten.

## <a name="onboard-machines-in-the-workspace"></a>Ombord maskiner i arbetsytan

Manuellt installerade datorer eller datorer som redan rapporterar till din arbetsyta måste läggas till i Azure Automation för att lösningen ska kunna aktiveras. Välj **Lager-** eller **ändringsspårning** under **Konfigurationshantering**i ditt Automation-konto eller **Uppdatera hantering** under **Uppdateringshantering**.

Välj **Hantera datorer**. Den här åtgärden öppnar sidan **Hantera datorer.** På den här sidan kan du aktivera lösningen på en utvald uppsättning datorer, alla tillgängliga maskiner, eller aktivera lösningen för alla aktuella datorer och aktivera den på alla framtida datorer. Knappen **Hantera maskiner** kan vara nedtonad om du tidigare valde alternativet Aktivera på alla tillgängliga och framtida **datorer**.

![Sparade sökningar](media/automation-onboard-solutions-from-automation-account/managemachines.png)

### <a name="all-available-machines"></a>Alla tillgängliga maskiner

Om du vill aktivera lösningen för alla tillgängliga datorer väljer du **Aktivera på alla tillgängliga datorer**. Den här åtgärden inaktiverar kontrollen för att lägga till datorer individuellt. Den här uppgiften lägger till alla namn på datorer som rapporterar till arbetsytan i den datorgrupp som sparats i sökfrågan. När du väljer det här alternativet inaktiveras knappen **Hantera datorer.**

### <a name="all-available-and-future-machines"></a>Alla tillgängliga och framtida maskiner

Om du vill aktivera lösningen för alla tillgängliga maskiner och framtida datorer väljer du **Aktivera på alla tillgängliga och framtida datorer**. Med det här alternativet tas de sparade sökningarna och scopekonfigurationerna bort från arbetsytan. Den här åtgärden öppnar lösningen för alla Azure- och icke-Azure-datorer som rapporterar till arbetsytan. När du väljer det här alternativet inaktiveras knappen **Hantera datorer** permanent eftersom det inte finns någon scopekonfiguration kvar.

Du kan lägga till scopekonfigurationerna igen genom att lägga till de första sparade sökningarna tillbaka. Mer information finns i [Sparade sökningar](#saved-searches).

### <a name="selected-machines"></a>Utvalda maskiner

Om du vill aktivera lösningen för en eller flera datorer väljer du **Aktivera på valda datorer** och klickar på **Lägg** till bredvid varje dator som du vill lägga till i lösningen. Den här uppgiften lägger till de markerade datornamnen i den datorgrupp som sparats i sökfrågan för lösningen.

## <a name="unlink-workspace"></a>Ta bort arbetsytans länk

Följande lösningar är beroende av en Log Analytics-arbetsyta:

* [Uppdateringshantering](automation-update-management.md)
* [Spårning av ändringar](automation-change-tracking.md)
* [Start/stoppa virtuella datorer under ledig tid](automation-solution-vm-management.md)

Om du bestämmer dig för att du inte längre vill integrera ditt Automation-konto med en Log Analytics-arbetsyta kan du ta bort länken till ditt konto direkt från Azure-portalen.  Innan du fortsätter måste du först ta bort de lösningar som nämndes tidigare, annars förhindras den här processen från att fortsätta. Läs artikeln för den specifika lösning som du har importerat för att förstå de steg som krävs för att ta bort den.

När du har tagit bort dessa lösningar kan du slutföra följande steg för att ta bort länken till ditt Automation-konto.

> [!NOTE]
> Vissa lösningar, inklusive tidigare versioner av Azure SQL-övervakningslösningen, kan ha skapat automatiseringsresurser och kan också behöva tas bort innan arbetsytan tas bort.

1. Öppna ditt Automation-konto på sidan Automation-konto på sidan Automation-konto **under** avsnittet **Relaterade resurser** till vänster.

2. Klicka på **Ta bort länk arbetsyta**på sidan Ta bort länk.

   ![Ta bort länkning av arbetsyta](media/automation-onboard-solutions-from-automation-account/automation-unlink-workspace-blade.png).

   Du kommer att få en uppmaning som verifierar att du vill fortsätta.

3. Medan Azure Automation försöker ta bort länken till kontot din Log Analytics-arbetsyta kan du spåra förloppet under **Meddelanden** från menyn.

Om du använde lösningen Update Management kanske du vill ta bort följande objekt som inte längre behövs när du har tagit bort lösningen.

* Uppdateringsscheman - Var och en kommer att ha namn som matchar de uppdateringsdistributioner som du har skapat.

* Hybridarbetaregrupper som skapats för lösningen - Var och en namnges på samma sätt som machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Om du har använt start- och stoppa virtuella datorer under ledig tid kanske du vill ta bort följande objekt som inte längre behövs när du har tagit bort lösningen.

* Starta och stoppa VM-runbookscheman
* Starta och stoppa VM-runbooks
* Variabler

Du kan också ta bort länken till arbetsytan från ditt Automation-konto från logganalysarbetsytan. På arbetsytan väljer du **Automation-konto** under **Relaterade resurser**. På sidan Automation-konto väljer du **Ta bort länkkonto**.

## <a name="clean-up-resources"></a>Rensa resurser

Så här tar du bort en virtuell dator från uppdateringshantering:

* I logganalysarbetsytan tar du bort den virtuella datorn `MicrosoftDefaultScopeConfig-Updates`från den sparade sökningen efter scopekonfigurationen . Sparade sökningar hittar du under **Allmänt** på arbetsytan.
* Ta bort [Microsoft Monitoring-agenten](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) eller [Log Analytics-agenten för Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Nästa steg

Fortsätt till självstudierna om lösningarna för att lära dig hur du använder dem.

* [Självstudiekurs - Hantera uppdateringar för din virtuella dator](automation-tutorial-update-management.md)

* [Självstudiekurs - Identifiera programvara på en virtuell dator](automation-tutorial-installed-software.md)

* [Självstudiekurs - Felsöka ändringar på en virtuell dator](automation-tutorial-troubleshoot-changes.md)
