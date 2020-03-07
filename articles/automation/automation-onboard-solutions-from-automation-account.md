---
title: Lär dig hur du integrerar Uppdateringshantering-, Ändringsspårning-och inventerings lösningar i Azure Automation
description: Lär dig att publicera en virtuell Azure-dator med Uppdateringshantering, Ändringsspårning och inventerings lösningar som ingår i Azure Automation
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: cf82dddf281e8e6f1348884702e32330dee4781b
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372953"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>Publicera Uppdateringshantering, Ändringsspårning och inventerings lösningar

Azure Automation innehåller lösningar för att hantera säkerhets uppdateringar av operativ systemet, spåra ändringar och inventering som är installerade på datorerna. Det finns många sätt att publicera datorer på, du kan publicera lösningen [från en virtuell dator](automation-onboard-solutions-from-vm.md), [från att bläddra bland flera datorer](automation-onboard-solutions-from-browse.md), från ditt Automation-konto eller via en [Runbook](automation-onboard-solutions.md). Den här artikeln beskriver hur du registrerar dessa lösningar från ditt Automation-konto.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in i Azure på https://portal.azure.com

## <a name="enable-solutions"></a>Aktivera lösningar

Navigera till ditt Automation-konto och välj antingen **inventering** eller **ändrings spårning** under **konfigurations hantering**.

Välj Log Analytics arbets yta och Automation-konto och klicka på **Aktivera** för att aktivera lösningen. Det tar upp till 15 minuter att aktivera lösningen.

![Publicera inventerings lösning](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

> [!NOTE]
> När du aktiverar lösningar går det endast att länka en Log Analytics-arbetsyta och ett Automation-konto i vissa regioner.
>
> En lista över mappnings par som stöds finns i [region mappning för Automation-konto och Log Analytics-arbetsyta](how-to/region-mappings.md).

Lösningen Ändringsspårning och inventering ger dig möjlighet att [spåra ändringar](automation-vm-change-tracking.md) och [inventering](automation-vm-inventory.md) på dina virtuella datorer. I det här steget aktiverar du lösningen för en virtuell dator.

När meddelandet ändrings spårning och inventerings lösning har slutförts väljer du **uppdaterings hantering** under **uppdaterings hantering**.

Med Uppdateringshantering-lösningen kan du hantera uppdateringar och korrigeringar för dina virtuella Azure-och hybrid datorer. Du kan bedöma status för tillgängliga uppdateringar, schemalägga installation av nödvändiga uppdateringar och granska distributions resultat för att kontrol lera att uppdateringarna har tillämpats korrekt.

På sidan Aktivera lösning är Log Analytics arbets ytan som är vald samma arbets yta som användes i föregående steg. Klicka på **Aktivera** för att publicera uppdateringshantering-lösningen. Det tar upp till 15 minuter att aktivera lösningen.

![Publicera uppdaterings lösning](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="scope-configuration"></a>Omfattnings konfiguration

Varje lösning använder en omfattnings konfiguration i arbets ytan för att rikta in sig på de datorer som hämtar lösningen. Omfattnings konfigurationen är en grupp med en eller flera sparade sökningar som används för att begränsa lösningens omfattning till vissa datorer. Om du vill komma åt konfigurationerna för omfattningar går du till Automation-kontot under **relaterade resurser**och väljer **arbets yta**. Gå sedan till arbets ytan under arbets ytans **data källor**och välj **scope-konfigurationer**.

Om den valda arbets ytan inte har Uppdateringshantering-eller Ändringsspårnings lösningar, skapas följande omfattningar:

* **MicrosoftDefaultScopeConfig – ChangeTracking**

* **MicrosoftDefaultScopeConfig – uppdateringar**

Om den valda arbets ytan redan har lösningen omdistribueras inte lösningen och omfattnings konfigurationen läggs inte till i den.

## <a name="saved-searches"></a>Sparade sökningar

När en dator läggs till i Uppdateringshantering eller Ändringsspårning-och inventerings lösningarna läggs de till i en av två sparade sökningar i din arbets yta. De här sparade sökningarna är frågor som innehåller de datorer som är riktade till dessa lösningar.

Navigera till din Log Analytics arbets yta och välj **sparade sökningar** under **Allmänt**. De två sparade sökningar som används av dessa lösningar kan visas i följande tabell:

|Namn     |Kategori  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Uppdateringar        | Updates__MicrosoftDefaultComputerGroup         |

Välj antingen Sparad sökning för att visa frågan som används för att fylla i gruppen. Följande bild visar frågan och dess resultat:

![Sparade sökningar](media/automation-onboard-solutions-from-automation-account/savedsearch.png)

## <a name="onboard-azure-vms"></a>Publicera virtuella Azure-datorer

Från ditt Automation-konto väljer du **inventering** eller **ändrings spårning** under **konfigurations hantering**eller **uppdaterings hantering** under **uppdaterings hantering**.

Klicka på **+ Lägg till virtuella Azure-datorer**, Välj en eller flera virtuella datorer i listan. Virtuella datorer som inte kan aktive ras är nedtonade och kan inte väljas. Virtuella Azure-datorer kan finnas i vilken region som helst, oavsett platsen för ditt Automation-konto. På sidan **aktivera uppdateringshantering** klickar du på **Aktivera**. Den här åtgärden lägger till de valda virtuella datorerna i dator gruppens sparade sökning efter lösningen.

![Aktivera virtuella Azure-datorer](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="onboard-a-non-azure-machine"></a>Publicera en virtuell dator utan Azure

Datorer som inte i Azure måste läggas till manuellt. Från ditt Automation-konto väljer du **inventering** eller **ändrings spårning** under **konfigurations hantering**eller **uppdaterings hantering** under **uppdaterings hantering**.

Klicka på **Lägg till icke-Azure-dator**. Den här åtgärden öppnar ett nytt webbläsarfönster med [anvisningar om hur du installerar och konfigurerar Microsoft Monitoring Agent på datorn](../azure-monitor/platform/log-analytics-agent.md) så att datorn kan börja rapportera till lösningen. Om du registrerar en dator som för närvarande hanteras av System Center Operations Manager, krävs ingen ny agent, informationen i arbets ytan anges i den befintliga agenten.

## <a name="onboard-machines-in-the-workspace"></a>Publicera datorer på arbets ytan

Manuellt installerade datorer eller datorer som redan rapporterar till din arbets yta måste läggas till Azure Automation för att lösningen ska vara aktive rad. Från ditt Automation-konto väljer du **inventering** eller **ändrings spårning** under **konfigurations hantering**eller **uppdaterings hantering** under **uppdaterings hantering**.

Välj **hantera datorer**. Den här åtgärden öppnar sidan **hantera datorer** . På den här sidan kan du aktivera lösningen på en Välj uppsättning datorer, alla tillgängliga datorer eller aktivera lösningen för alla aktuella datorer och aktivera den på alla framtida datorer. Knappen **hantera datorer** kan vara nedtonad om du tidigare har valt alternativet **Aktivera på alla tillgängliga och framtida datorer**.

![Sparade sökningar](media/automation-onboard-solutions-from-automation-account/managemachines.png)

### <a name="all-available-machines"></a>Alla tillgängliga datorer

Om du vill aktivera lösningen för alla tillgängliga datorer väljer du **Aktivera på alla tillgängliga datorer**. Den här åtgärden inaktiverar kontrollen för att lägga till datorer individuellt. Den här uppgiften lägger till alla namn på de datorer som rapporterar till arbets ytan till den dator grupp som sparade Sök frågan. När det här alternativet är markerat inaktive ras knappen **hantera datorer** .

### <a name="all-available-and-future-machines"></a>Alla tillgängliga och framtida datorer

Om du vill aktivera lösningen för alla tillgängliga datorer och framtida datorer väljer du **Aktivera på alla tillgängliga och framtida datorer**. Det här alternativet tar bort sparade sökningar och omfattnings konfiguration från arbets ytan. Den här åtgärden öppnar lösningen för alla Azure-och icke-Azure-datorer som rapporterar till arbets ytan. När du väljer det här alternativet inaktive ras knappen **hantera datorer** permanent eftersom det inte finns någon omfattnings konfiguration kvar.

Du kan lägga till omfångs konfigurationerna genom att lägga till de ursprungliga sparade sökningarna igen. Mer information finns i [sparade sökningar](#saved-searches).

### <a name="selected-machines"></a>Valda datorer

Om du vill aktivera lösningen för en eller flera datorer väljer du **Aktivera på valda datorer** och klickar på **Lägg till** bredvid varje dator som du vill lägga till i lösningen. Den här uppgiften lägger till de valda dator namnen i den dator grupps sparade Sök frågan för lösningen.

## <a name="unlink-workspace"></a>Ta bort länk till arbets yta

Följande lösningar är beroende av en Log Analytics arbets yta:

* [Hantering av uppdateringar](automation-update-management.md)
* [Spårning av ändringar](automation-change-tracking.md)
* [Starta/stoppa virtuella datorer när de inte används](automation-solution-vm-management.md)

Om du inte längre vill integrera ditt Automation-konto med en Log Analytics arbets yta kan du ta bort länken till ditt konto direkt från Azure Portal.  Innan du fortsätter måste du först ta bort de lösningar som nämns ovan, annars kommer den här processen att förhindras från att fortsätta. Läs artikeln för den specifika lösning som du har importerat för att förstå de steg som krävs för att ta bort den.

När du har tagit bort dessa lösningar kan du utföra följande steg för att ta bort länken till ditt Automation-konto.

> [!NOTE]
> Vissa lösningar inklusive tidigare versioner av Azure SQL-övervaknings lösningen kan ha skapat Automation-till gångar och kan också behöva tas bort innan du tar bort länken till arbets ytan.

1. Öppna ditt Automation-konto från Azure Portal och välj **länkad arbets yta** under avsnittet med etiketten **relaterade resurser** till vänster på sidan Automation-konto.

2. På sidan ta bort länk till arbets yta klickar du på **ta bort arbets ytan**.

   ![Sidan ta bort länk till arbets yta](media/automation-onboard-solutions-from-automation-account/automation-unlink-workspace-blade.png).

   Du får ett meddelande som bekräftar att du vill fortsätta.

3. Medan Azure Automation försöker ta bort länken till kontot Log Analytics arbets ytan, kan du följa förloppet under **meddelanden** på menyn.

Om du använde Uppdateringshantering-lösningen kanske du vill ta bort följande objekt som inte längre behövs efter att du har tagit bort lösningen.

* Uppdaterings scheman – varje har namn som matchar de uppdaterings distributioner som du har skapat.

* Hybrid Worker-grupper som har skapats för lösningen – var och en får samma namn som machine1. contoso. com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Om du använde lösningen starta och stoppa virtuella datorer vid låg belastnings tider kanske du vill ta bort följande objekt som inte längre behövs när du har tagit bort lösningen.

* Starta och stoppa virtuella dator Runbook-scheman
* Starta och stoppa VM-Runbooks
* Variabler

Alternativt kan du också ta bort länken till arbets ytan från ditt Automation-konto från din Log Analytics-arbetsyta. På arbets ytan väljer du **Automation-konto** under **relaterade resurser**. På sidan Automation-konto väljer du **ta bort länk till konto**.

## <a name="clean-up-resources"></a>Rensa resurser

Så här tar du bort en virtuell dator från Uppdateringshantering:

* I arbets ytan Log Analytics tar du bort den virtuella datorn från den sparade sökningen för omfattnings konfigurationen `MicrosoftDefaultScopeConfig-Updates`. Sparade sökningar hittar du under **Allmänt** på arbets ytan.
* Ta bort [Microsoft Monitoring Agent](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) eller [Log Analytics agent för Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Nästa steg

Fortsätt till självstudierna för lösningarna och lär dig hur du använder dem.

* [Självstudie – hantera uppdateringar för din virtuella dator](automation-tutorial-update-management.md)

* [Självstudie – identifiera program vara på en virtuell dator](automation-tutorial-installed-software.md)

* [Självstudie – felsöka ändringar på en virtuell dator](automation-tutorial-troubleshoot-changes.md)
