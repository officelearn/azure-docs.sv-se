---
title: Lär dig hur du publicera uppdateringshantering, ändringsspårning och lager lösningar i Azure Automation
description: Lär dig hur att publicera ett Azure-virtuella datorn med uppdateringshantering, ändringsspårning och lager-lösningar som ingår i Azure Automation
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: conceptual
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 0174e2a3c0b14c52b5750e343932a5df39d18976
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833400"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>Publicera uppdateringshantering, ändringsspårning och lager-lösningar

Azure Automation ger lösningar för att hantera uppdateringar av operativsystemet säkerhet, spåra ändringar och inventera vad som är installerat på datorerna. Det finns flera sätt att publicera datorer, kan du publicera lösningen [från en virtuell dator](automation-onboard-solutions-from-vm.md), [från flera datorer-surfning](automation-onboard-solutions-from-browse.md), från ditt Automation-konto eller av [runbook](automation-onboard-solutions.md). Den här artikeln beskriver onboarding dessa lösningar från ditt Automation-konto.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in till Azure på https://portal.azure.com

## <a name="enable-solutions"></a>Aktivera lösningar

Navigera till ditt Automation-konto och välj antingen **inventering** eller **ändringsspårning** under **KONFIGURATIONSHANTERING**.

Välj Log analytics-arbetsyta och Automation-kontot och klicka på **aktivera** att aktivera lösningen. Det tar upp till 15 minuter att aktivera lösningen.

![Publicera lager-lösning](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

Lösningen Ändringsspårning och inventering ger dig möjlighet att [spåra ändringar](automation-vm-change-tracking.md) och [inventering](automation-vm-inventory.md) på dina virtuella datorer. I det här steget aktiverar du lösningen för en virtuell dator.

När meddelandet för lösning för publicering av ändringsspårning och inventering har slutförts klickar du på **Uppdateringshantering** under **Konfigurationshantering**.

Med uppdateringshanteringslösningen kan du hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer i Windows. Du kan utvärdera statusen för tillgängliga uppdateringar, schemalägga installation av nödvändiga uppdateringar och granska distributionsresultat för att verifiera att uppdateringarna har tillämpats på den virtuella datorn. Den här åtgärden aktivera lösningen för den virtuella datorn.

Välj **uppdateringshantering** under **UPPDATERINGSHANTERING**. Log Analytics-arbetsytan som är vald är samma arbetsyta som används i föregående steg. Klicka på **Aktivera** för att publicera lösningen för uppdateringshantering. Det tar upp till 15 minuter att aktivera lösningen.

![Publicera update lösning](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="scope-configuration"></a>Omfattningskonfiguration

Varje lösning använder en Scope-konfiguration på arbetsytan för att fokusera på de datorer som hämta lösningen. Konfigurationen av är en grupp med en eller flera sparade sökningar som används för att begränsa omfattningen av lösningen på specifika datorer. Åtkomst till Scope-konfigurationerna i ditt Automation-konto under **relaterade resurser**väljer **arbetsytan**. I arbetsytan under **ARBETSYTAN DATAKÄLLOR**väljer **omfång konfigurationer**.

Om den valda arbetsytan inte har skapa lösningar för hantering av uppdateringar eller ändringsspårning har följande omfång konfigurationer:

* **MicrosoftDefaultScopeConfig ChangeTracking**

* **MicrosoftDefaultScopeConfig uppdateringar**

Om den valda arbetsytan har redan lösningen. Lösningen distribueras inte igen och konfigurationen av läggs inte till den.

## <a name="saved-searches"></a>Sparade sökningar

När en dator läggs till i uppdateringshantering eller lösningar för spårning av ändringar och inventering, läggs de till en av två sparade sökningar på arbetsytan. Dessa sparade sökningar är frågor som innehåller de datorer som är mål för dessa lösningar.

Navigera till ditt Automation-konto och markera **sparade sökningar** under **allmänna**. Två sparade sökningar som används av dessa lösningar kan ses i följande tabell:

|Namn     |Kategori  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Uppdateringar        | Updates__MicrosoftDefaultComputerGroup         |

Välj antingen sparad sökning kan du se frågan som används för att fylla i gruppen. Följande bild visar frågan och dess resultat:

![Sparade sökningar](media/automation-onboard-solutions-from-automation-account/savedsearch.png)

## <a name="onboard-azure-vms"></a>Publicera virtuella Azure-datorer

Från ditt Automation-kontot väljer **inventering** eller **ändringsspårning** under **KONFIGURATIONSHANTERING**, eller **uppdateringshantering** under **UPPDATERINGSHANTERING**.

Klicka på **+ Lägg till virtuella datorer i Azure**, Välj en eller flera virtuella datorer i listan. Virtuella datorer som inte kan aktiveras är avmarkerad out och inte kan väljas. På den **aktivera uppdateringshantering** klickar du på **aktivera**. Detta lägger till de valda virtuella datorerna till i datorgruppen sparad sökning för lösningen.

![Aktivera virtuella Azure-datorer](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="onboard-a-non-azure-machine"></a>Publicera en virtuell dator utan Azure

Azure-datorer inte behöver läggas till manuellt. Från ditt Automation-kontot väljer **inventering** eller **ändringsspårning** under **KONFIGURATIONSHANTERING**, eller **uppdateringshantering** under **UPPDATERINGSHANTERING**.

Klicka på **Lägg till Azure-datorn**. Detta öppnar ett nytt webbläsarfönster med den [instruktioner om hur du installerar och konfigurerar Microsoft Monitoring Agent på datorn](../log-analytics/log-analytics-concept-hybrid.md) så att datorn kan rapportera till lösningen. Om du är onboarding en dator som för närvarande hanteras av System Center Operations Manager, en ny agent krävs inte, arbetsyteinformation har angetts i den befintliga agenten.

## <a name="onboard-machines-in-the-workspace"></a>Publicera datorer på arbetsytan

Manuellt installerade datorer eller datorer som redan rapporterar till arbetsytan behöver läggas till i Azure Automation för lösningen ska aktiveras. Från ditt Automation-kontot väljer **inventering** eller **ändringsspårning** under **KONFIGURATIONSHANTERING**, eller **uppdateringshantering** under **UPPDATERINGSHANTERING**.

Välj **hantera datorer**. Detta öppnar den **hantera datorer** sidan. Den här sidan kan du aktivera lösningen på en utvald grupp datorer, alla tillgängliga datorer eller aktivera lösning för alla aktuella datorer och aktivera det på alla framtida datorer.

![Sparade sökningar](media/automation-onboard-solutions-from-automation-account/managemachines.png)

### <a name="all-available-machines"></a>Alla tillgängliga datorer

Välj för att aktivera lösning för alla tillgängliga datorer **aktivera på alla tillgängliga datorer**. Detta inaktiverar kontrollen om du vill lägga till datorer i taget. Den här uppgiften lägger till alla namnen på de datorer som rapporterar till arbetsytan till i datorgruppen spara en sökfråga.

### <a name="all-available-and-future-machines"></a>Alla tillgängliga och framtida datorer

Välj för att aktivera lösning för alla tillgängliga datorer och alla framtida datorer **aktivera på alla tillgängliga och framtida datorer**. Det här alternativet tar bort de sparade sökningar och Scope konfigurationer från arbetsytan. Då öppnas lösningen till alla Azure och Azure-datorer som rapporterar till arbetsytan.

### <a name="selected-machines"></a>Valda datorer

Välj för att aktivera lösning för en eller flera datorer **aktivera på valda datorer** och på **lägga till** bredvid varje dator som du vill lägga till i lösningen. Den här uppgiften lägger till de valda datornamn datorgruppen sparade sökfråga för lösningen.

## <a name="next-steps"></a>Nästa steg

Fortsätta att självstudier i lösningar för att lära sig hur de används.

* [Självstudiekurs – hantera uppdateringar för den virtuella datorn](automation-tutorial-update-management.md)

* [Självstudiekurs – identifiera programvara på en virtuell dator](automation-tutorial-installed-software.md)

* [Kursen - felsöka ändringar på en virtuell dator](automation-tutorial-troubleshoot-changes.md)