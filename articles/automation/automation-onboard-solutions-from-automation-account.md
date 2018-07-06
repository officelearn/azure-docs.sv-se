---
title: Lär dig hur du publicera lösningar för uppdateringshantering, ändringsspårning och inventering i Azure Automation
description: Lär dig hur att publicera en Azure-virtuella datorn med lösningar för uppdateringshantering, ändringsspårning och inventering som ingår i Azure Automation
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: conceptual
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 8649b96c9cf95e4a25b24dedf447aef133ef299a
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37865411"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>Publicera lösningar för uppdateringshantering, ändringsspårning och inventering

Azure Automation tillhandahåller lösningar för att hantera säkerhet för operativsystemuppdateringar, spåra ändringar och inventering vad som är installerat på datorerna. Det finns flera sätt att registrera datorer, du kan registrera lösningen [från en virtuell dator](automation-onboard-solutions-from-vm.md), [från flera datorer-surfning](automation-onboard-solutions-from-browse.md), från ditt Automation-konto eller av [runbook](automation-onboard-solutions.md). Den här artikeln beskrivs registrering dessa lösningar från ditt Automation-konto.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in till Azure på https://portal.azure.com

## <a name="enable-solutions"></a>Aktivera lösningar

Navigera till ditt Automation-konto och välj antingen **inventering** eller **ändringsspårning** under **KONFIGURATIONSHANTERING**.

Välj Log analytics-arbetsytan och Automation-kontot och klicka på **aktivera** att aktivera lösningen. Det tar upp till 15 minuter att aktivera lösningen.

![Publicera inventeringslösningen](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

Lösningen Ändringsspårning och inventering ger dig möjlighet att [spåra ändringar](automation-vm-change-tracking.md) och [inventering](automation-vm-inventory.md) på dina virtuella datorer. I det här steget aktiverar du lösningen för en virtuell dator.

När meddelandet för lösning för publicering av ändringsspårning och inventering har slutförts klickar du på **Uppdateringshantering** under **Konfigurationshantering**.

Med uppdateringshanteringslösningen kan du hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer i Windows. Du kan utvärdera statusen för tillgängliga uppdateringar, schemalägga installation av nödvändiga uppdateringar och granska distributionsresultat för att verifiera att uppdateringarna har tillämpats på den virtuella datorn. Den här åtgärden aktivera lösningen för den virtuella datorn.

Välj **uppdateringshantering** under **UPPDATERINGSHANTERING**. Log Analytics-arbetsytan som är vald är samma arbetsyta som används i föregående steg. Klicka på **Aktivera** för att publicera lösningen för uppdateringshantering. Det tar upp till 15 minuter att aktivera lösningen.

![Publicera uppdateringslösning](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="scope-configuration"></a>Omfattningskonfiguration

Varje lösning använder en Omfattningskonfigurationen i arbetsytan för att fokusera på de datorer som får lösningen. Konfigurationen är en grupp med en eller flera sparade sökningar som används för att begränsa omfattningen av lösningen till specifika datorer. Åtkomst till Omfattningskonfigurationer i ditt Automation-konto under **relaterade resurser**väljer **arbetsytan**. I arbetsytan under **DATAKÄLLOR för ARBETSYTA**väljer **Omfattningskonfigurationer**.

Om den valda arbetsytan har inte lösningarna för uppdateringshantering eller ändringsspårning än följande omfattningskonfigurationer skapas:

* **MicrosoftDefaultScopeConfig ChangeTracking**

* **MicrosoftDefaultScopeConfig-uppdateringar**

Om det finns redan i lösningen för den valda arbetsytan. Lösningen har inte distribuerats igen och omfattningskonfigurationen läggs inte till den.

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

Klicka på **+ Lägg till virtuella Azure-datorer**, Välj en eller flera virtuella datorer i listan. Virtuella datorer som inte kan aktiveras är gråmarkerat och kan inte väljas. På den **Aktivera hantering av uppdateringar** klickar du på **aktivera**. Detta lägger till de valda virtuella datorerna till i datorgruppen sparad sökning för lösningen.

![Aktivera virtuella Azure-datorer](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="onboard-a-non-azure-machine"></a>Publicera en virtuell dator utan Azure

Datorer inte i Azure måste läggas till manuellt. Från ditt Automation-kontot väljer **inventering** eller **ändringsspårning** under **KONFIGURATIONSHANTERING**, eller **uppdateringshantering** under **UPPDATERINGSHANTERING**.

Klicka på **Lägg till icke-Azure-datorn**. Gör det öppnas ett nytt webbläsarfönster med den [anvisningar om hur du installerar och konfigurerar Microsoft Monitoring Agent på datorn](../log-analytics/log-analytics-concept-hybrid.md) så att datorn kan börja rapporterar till lösningen. Om du är en dator som för närvarande hanteras av System Center Operations Manager, en ny agent är inte obligatoriskt, information om arbetsytan har angetts i den befintliga agenten.

## <a name="onboard-machines-in-the-workspace"></a>Publicera datorer i arbetsytan

Manuellt installerade datorer eller datorer som redan rapporterar till arbetsytan behöver läggas till i Azure Automation för lösningen aktiveras. Från ditt Automation-kontot väljer **inventering** eller **ändringsspårning** under **KONFIGURATIONSHANTERING**, eller **uppdateringshantering** under **UPPDATERINGSHANTERING**.

Välj **hantera datorer**. Gör det öppnas den **hantera datorer** sidan. Den här sidan kan du aktivera lösningen på en uppsättning datorer, alla tillgängliga datorer, eller aktivera lösningen för alla aktuella datorer och aktivera det på alla framtida datorer.

![Sparade sökningar](media/automation-onboard-solutions-from-automation-account/managemachines.png)

### <a name="all-available-machines"></a>Alla tillgängliga datorer

Välj för att aktivera lösningen för alla tillgängliga datorer **aktivera på alla tillgängliga datorer**. Detta inaktiverar kontrollen för att lägga till datorer individuellt. Detta lägger till alla namnen på datorer som rapporterar till arbetsytan för att den datorgrupp som sparats sökfråga.

### <a name="all-available-and-future-machines"></a>Alla tillgängliga och framtida datorer

Välj för att aktivera lösningen för alla tillgängliga och alla framtida datorer **aktivera på alla tillgängliga och framtida datorer**. Det här alternativet tar bort sparade sökningar och Omfattningskonfigurationer från arbetsytan. Då öppnas lösningen till alla Azure- och icke-Azure-datorer som rapporterar till arbetsytan.

### <a name="selected-machines"></a>Valda datorer

Välj för att aktivera lösningen för en eller flera datorer **aktivera på valda datorer** och klicka på **lägga till** bredvid varje dator som du vill lägga till i lösningen. Den här uppgiften lägger till valda datornamnen datorgrupp som sparats sökfråga för lösningen.

## <a name="next-steps"></a>Nästa steg

Fortsätt till självstudierna om lösningarna som du vill veta hur de används.

* [Självstudie – hantera uppdateringar för din virtuella dator](automation-tutorial-update-management.md)

* [Självstudie – identifiera programvara på en virtuell dator](automation-tutorial-installed-software.md)

* [Självstudie – Felsök ändringar på en virtuell dator](automation-tutorial-troubleshoot-changes.md)