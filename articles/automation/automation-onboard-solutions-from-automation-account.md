---
title: Lär dig hur du publicera lösningar för uppdateringshantering, ändringsspårning och inventering i Azure Automation
description: Lär dig hur att publicera en Azure-virtuella datorn med lösningar för uppdateringshantering, ändringsspårning och inventering som ingår i Azure Automation
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 10/16/2018
ms.topic: conceptual
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: c020d3c7513efda93d7ac5d3bdd79f21f8bc77dd
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56818485"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>Publicera lösningar för uppdateringshantering, ändringsspårning och inventering

Azure Automation tillhandahåller lösningar för att hantera säkerhet för operativsystemuppdateringar, spåra ändringar och inventering vad som är installerat på datorerna. Det finns många sätt att registrera datorer, du kan registrera lösningen [från en virtuell dator](automation-onboard-solutions-from-vm.md), [från flera datorer-surfning](automation-onboard-solutions-from-browse.md), från ditt Automation-konto eller av [runbook](automation-onboard-solutions.md). Den här artikeln beskrivs registrering dessa lösningar från ditt Automation-konto.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in i Azure på https://portal.azure.com

## <a name="enable-solutions"></a>Aktivera lösningar

Navigera till ditt Automation-konto och välj antingen **inventering** eller **ändringsspårning** under **KONFIGURATIONSHANTERING**.

Välj Log Analytics-arbetsytan och Automation-kontot och klicka på **aktivera** att aktivera lösningen. Det tar upp till 15 minuter att aktivera lösningen.

![Publicera inventeringslösningen](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

När du aktiverar lösningar stöds endast i vissa regioner för att länka en Log Analytics-arbetsyta och ett Automation-konto.

I följande tabell visas mappningarna som stöds:

|**Log Analytics arbetsytans Region**|**Azure Automation Region**|
|---|---|
|Sydöstra Australien|Sydöstra Australien|
|CanadaCentral|CanadaCentral|
|Indiencentrala|Indiencentrala|
|EastUS|EastUS2|
|JapanEast|JapanEast|
|SoutheastAsia|SoutheastAsia|
|WestCentralUS|WestCentralUS|
|Västeuropa|Västeuropa|
|Södrastorbritannien|Södrastorbritannien|
|USGovVirginia|USGovVirginia|
|EastUS2EUAP|CentralUSEUAP|

Lösningen Ändringsspårning och inventering ger dig möjlighet att [spåra ändringar](automation-vm-change-tracking.md) och [inventering](automation-vm-inventory.md) på dina virtuella datorer. I det här steget aktiverar du lösningen för en virtuell dator.

När meddelandet för lösning för publicering av ändringsspårning och inventering har slutförts klickar du på **Uppdateringshantering** under **Konfigurationshantering**.

Med uppdateringshanteringslösningen kan du hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer i Windows. Du kan utvärdera statusen för tillgängliga uppdateringar, schemalägga installation av nödvändiga uppdateringar och granska distributionsresultat för att verifiera att uppdateringarna har tillämpats på den virtuella datorn. Den här åtgärden aktivera lösningen för den virtuella datorn.

Välj **uppdateringshantering** under **UPPDATERINGSHANTERING**. Log Analytics-arbetsytan som valts är samma arbetsyta som används i föregående steg. Klicka på **Aktivera** för att publicera lösningen för uppdateringshantering. Det tar upp till 15 minuter att aktivera lösningen.

![Publicera uppdateringslösning](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="scope-configuration"></a>Omfattningskonfiguration

Varje lösning använder en Omfattningskonfigurationen i arbetsytan för att fokusera på de datorer som får lösningen. Konfigurationen är en grupp med en eller flera sparade sökningar som används för att begränsa omfattningen av lösningen till specifika datorer. Åtkomst till Omfattningskonfigurationer i ditt Automation-konto under **relaterade resurser**väljer **arbetsytan**. I arbetsytan under **DATAKÄLLOR för ARBETSYTA**väljer **Omfattningskonfigurationer**.

Om den valda arbetsytan inte ännu har lösningarna för uppdateringshantering eller ändringsspårning, skapas följande omfång konfigurationer:

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

Om den valda arbetsytan har redan lösningen kan lösningen inte igen distribuerade och konfigurationen har lagts till inte i den.

## <a name="saved-searches"></a>Sparade sökningar

När en dator läggs till i uppdateringshantering eller ändringsspårning och inventering lösningar, läggs de till en av två sparade sökningar i din arbetsyta. Dessa sparade sökningar är frågor som innehåller de datorer som är avsedda för dessa lösningar.

Gå till ditt Automation-konto och välj **sparade sökningar** under **Allmänt**. Två sparade sökningar som används av dessa lösningar kan ses i följande tabell:

|Namn     |Kategori  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Uppdateringar        | Updates__MicrosoftDefaultComputerGroup         |

Välj antingen sparad sökning kan du se frågan som används för att fylla i gruppen. Följande bild visar frågan och dess resultat:

![Sparade sökningar](media/automation-onboard-solutions-from-automation-account/savedsearch.png)

## <a name="onboard-azure-vms"></a>Publicera Azure virtuella datorer

Från ditt Automation-kontot väljer **inventering** eller **ändringsspårning** under **KONFIGURATIONSHANTERING**, eller **uppdateringshantering** under **UPPDATERINGSHANTERING**.

Klicka på **+ Lägg till virtuella Azure-datorer**, Välj en eller flera virtuella datorer i listan. Virtuella datorer som inte kan aktiveras är gråmarkerat och kan inte väljas. På den **Aktivera hantering av uppdateringar** klickar du på **aktivera**. Den här åtgärden lägger till de valda virtuella datorerna till i datorgruppen sparad sökning för lösningen.

![Aktivera virtuella Azure-datorer](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="onboard-a-non-azure-machine"></a>Publicera en virtuell dator utan Azure

Datorer inte i Azure måste läggas till manuellt. Från ditt Automation-kontot väljer **inventering** eller **ändringsspårning** under **KONFIGURATIONSHANTERING**, eller **uppdateringshantering** under **UPPDATERINGSHANTERING**.

Klicka på **Lägg till icke-Azure-datorn**. Den här åtgärden öppnar ett nytt webbläsarfönster med den [anvisningar om hur du installerar och konfigurerar Microsoft Monitoring Agent på datorn](../azure-monitor/platform/log-analytics-agent.md) så att datorn kan börja rapporterar till lösningen. Om du är onboarding en dator som för närvarande hanteras av System Center Operations Manager, en ny agent krävs inte, information om arbetsytan har angetts i den befintliga agenten.

## <a name="onboard-machines-in-the-workspace"></a>Publicera datorer i arbetsytan

Manuellt installerade datorer eller datorer som redan rapporterar till arbetsytan måste läggas till Azure Automation för lösningen aktiveras. Från ditt Automation-kontot väljer **inventering** eller **ändringsspårning** under **KONFIGURATIONSHANTERING**, eller **uppdateringshantering** under **UPPDATERINGSHANTERING**.

Välj **hantera datorer**. Den här åtgärden öppnar den **hantera datorer** sidan. Den här sidan kan du aktivera lösningen på en uppsättning datorer, alla tillgängliga datorer, eller aktivera lösningen för alla aktuella datorer och aktivera det på alla framtida datorer. Den **hantera datorer** knappen kan nedtonade om du tidigare har valt alternativet **aktivera på alla tillgängliga och framtida datorer**.

![Sparade sökningar](media/automation-onboard-solutions-from-automation-account/managemachines.png)

### <a name="all-available-machines"></a>Alla tillgängliga datorer

Välj för att aktivera lösningen för alla tillgängliga datorer **aktivera på alla tillgängliga datorer**. Den här åtgärden inaktiverar kontrollen för att lägga till datorer individuellt. Detta lägger till alla namnen på datorer som rapporterar till arbetsytan för att den datorgrupp som sparats sökfråga. När du väljer den här åtgärden inaktiverar den **hantera datorer** knappen.

### <a name="all-available-and-future-machines"></a>Alla tillgängliga och framtida datorer

Välj för att aktivera lösningen för alla tillgängliga och framtida datorer **aktivera på alla tillgängliga och framtida datorer**. Det här alternativet tar bort sparade sökningar och Omfattningskonfigurationer från arbetsytan. Den här åtgärden öppnar lösningen till alla Azure- och icke-Azure-datorer som rapporterar till arbetsytan. När du väljer den här åtgärden inaktiverar den **hantera datorer** knappen permanent eftersom det finns ingen Omfattningskonfiguration till vänster.

### <a name="selected-machines"></a>Valda datorer

Välj för att aktivera lösningen för en eller flera datorer **aktivera på valda datorer** och klicka på **lägga till** bredvid varje dator som du vill lägga till i lösningen. Den här uppgiften lägger till valda datornamnen datorgrupp som sparats sökfråga för lösningen.

## <a name="unlink-workspace"></a>Ta bort arbetsytans länk

Följande lösningar är beroende av en Log Analytics-arbetsyta:

* [Hantering av uppdateringar](automation-update-management.md)
* [Spårning av ändringar](automation-change-tracking.md)
* [Starta/Stoppa VM under kontorstid](automation-solution-vm-management.md)

Om du inte längre vill integrera ditt Automation-konto med en arbetsyta för Log Analytics kan du kan ta bort länken till ditt konto direkt från Azure-portalen.  Innan du fortsätter måste du först ta bort lösningar som tidigare nämnts, annars den här processen kommer inte att kunna fortsätta. Läsa artikeln för den lösning du har importerat för att förstå de steg som krävs för att ta bort den.

När du tar bort dessa lösningar kan slutföra du följande steg om du vill ta bort länken till ditt Automation-konto.

> [!NOTE]
> Vissa lösningar, inklusive tidigare versioner av Azure SQL-övervakningslösning kan ha skapat automation-tillgångar och kan också behöva tas bort innan du arbetsytans länk.

1. Öppna ditt Automation-konto från Azure-portalen och på Automation-konto väljer du sidan **länkade arbetsytan** under avsnittet märkta **relaterade resurser** till vänster.

2. På sidan Avlänka från arbetsytan **ta bort arbetsytans länk**.

   ![Avlänka arbetsytssidan](media/automation-onboard-solutions-from-automation-account/automation-unlink-workspace-blade.png).

   Du får ett meddelande som bekräftar att du vill fortsätta.

3. Medan Azure Automation försöker ta bort länken till konton som Log Analytics-arbetsytan, kan du följa förloppet under **meddelanden** på menyn.

Om du använder lösningen för uppdateringshantering, kan om du vill du ta bort följande objekt som inte längre behövs när du har tagit bort lösningen.

* Uppdatera schemalägger, var och en har namn som matchar de uppdateringsdistributioner du skapat)

* Hybrid worker-grupper som skapats för lösningen – var och en namnges på samma sätt till av typen machine1.contoso.com_9ceb8108 - 26 c 9-4051-b6b3-227600d715c8).

Om du har använt Start- och stoppa virtuella datorer vid låg belastning på nätverket lösning kan om du vill du ta bort följande objekt som inte längre behövs när du har tagit bort lösningen.

* Starta och stoppa scheman för VM-runbook
* Starta och stoppa Virtuella runbooks
* Variabler

## <a name="next-steps"></a>Nästa steg

Fortsätt till självstudierna om lösningarna som du vill veta hur de används.

* [Självstudie – hantera uppdateringar för din virtuella dator](automation-tutorial-update-management.md)

* [Självstudie – identifiera programvara på en virtuell dator](automation-tutorial-installed-software.md)

* [Självstudie – Felsök ändringar på en virtuell dator](automation-tutorial-troubleshoot-changes.md)
