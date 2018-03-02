---
title: "Lär dig hur du publicera uppdateringshantering, ändringsspårning och lager lösningar i Azure Automation"
description: "Lär dig hur att publicera ett Azure-virtuella datorn med uppdateringshantering, ändringsspårning och lager-lösningar som ingår i Azure Automation"
services: automation
keywords: 
author: georgewallace
ms.author: gwallace
ms.date: 02/28/2018
ms.topic: article
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 670a0c2a11ebfe09406233ab4b454b2e9c2ba0e0
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>Publicera uppdateringshantering, ändringsspårning och lager-lösningar

Azure Automation ger lösningar för att hantera uppdateringar av operativsystemet säkerhet, spåra ändringar och inventera vad som är installerat på datorerna. Det finns flera sätt att publicera datorer, kan du publicera lösningen [från en virtuell dator](automation-onboard-solutions-from-vm.md), från ditt Automation-konto eller av [runbook](automation-onboard-solutions.md). Den här artikeln beskriver onboarding dessa lösningar från ditt Automation-konto.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure på https://portal.azure.com

## <a name="enable-solutions"></a>Aktivera lösningar

Navigera till ditt Automation-konto och välj antingen **inventering** eller **ändringsspårning** under **KONFIGURATIONSHANTERING**.

Välj Log analytics-arbetsyta och Automation-kontot och klicka på **aktivera** att aktivera lösningen. Lösningen tar upp till 15 minuter att aktivera.

![Publicera lager-lösning](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

Ändringsspårning och lager lösningen ger möjlighet att [spåra ändringar](automation-vm-change-tracking.md) och [lager](automation-vm-inventory.md) på virtuella datorer. I det här steget kan aktivera du lösning på en virtuell dator.

När spårning och lager lösning onboarding ändringsmeddelanden är klar klickar du på **uppdateringshantering** under **KONFIGURATIONSHANTERING**.

Uppdatera hanteringslösningen kan du hantera uppdateringar och korrigeringsfiler för virtuella datorerna i Windows Azure. Du kan kontrollera statusen för uppdateringar, schemalägga installationen av obligatoriska uppdateringar och granska agentdistributionresultaten att verifiera uppdateringar har använts på den virtuella datorn. Den här åtgärden aktivera lösningen för den virtuella datorn.

Välj **uppdateringshantering** under **UPPDATERINGSHANTERING**. Log analytics-arbetsyta som valts är i samma arbetsyta som används i föregående steg. Klicka på **aktivera** publicera hanteringslösning för uppdateringen. Lösningen tar upp till 15 minuter att aktivera.

![Publicera update lösning](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="scope-configuration"></a>Konfiguration av scope

Varje lösning använder en Scope-konfiguration på arbetsytan för att fokusera på de datorer som hämta lösningen. Konfigurationen av är en grupp med en eller flera sparade sökningar som används för att begränsa omfattningen av lösningen på specifika datorer. Åtkomst till Scope-konfigurationerna i ditt Automation-konto under **relaterade resurser**väljer **arbetsytan**. I arbetsytan under **ARBETSYTAN DATAKÄLLOR**väljer **omfång konfigurationer**.

Två Scope-konfigurationer som skapas som standard **MicrosoftDefaultScopeConfig ChangeTracking** och **MicrosoftDefaultScopeConfig uppdateringar**.

## <a name="saved-searches"></a>Sparade sökningar

När en dator läggs till i uppdateringshantering eller lösningar för spårning av ändringar och inventering, läggs de till en av två sparade sökningar på arbetsytan. Dessa sparade sökningar är frågor som innehåller de datorer som är mål för dessa lösningar.

Navigera till ditt Automation-konto och markera **sparade sökningar** under **allmänna**. Två sparade sökningar som används av dessa lösningar kan ses i följande tabell:

|Namn     |Kategori  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Uppdateringar        | Updates__MicrosoftDefaultComputerGroup         |

Välj antingen sparad sökning kan du se frågan som används för att fylla i gruppen. Följande bild visar frågan och dess resultat:

![Sparade sökningar](media/automation-onboard-solutions-from-automation-account/savedsearch.png)

## <a name="onboard-an-azure-machine"></a>Publicera en dator i Azure

Från ditt Automation-kontot väljer **inventering** eller **ändringsspårning** under **KONFIGURATIONSHANTERING**, eller **uppdateringshantering** under **UPPDATERINGSHANTERING**.

Klicka på **+ Lägg till Azure VM**, Välj en virtuell dator i listan. På den **uppdateringshantering** klickar du på **aktivera**. Den aktuella virtuella datorn läggs till i datorgruppen sparad sökning för lösningen.

## <a name="onboard-a-non-azure-machine"></a>Publicera en Azure-dator

Från ditt Automation-kontot väljer **inventering** eller **ändringsspårning** under **KONFIGURATIONSHANTERING**, eller **uppdateringshantering** under **UPPDATERINGSHANTERING**.

Klicka på **Lägg till Azure-datorn**. Detta öppnar ett nytt webbläsarfönster med instruktioner om hur du installerar och konfigurerar Microsoft Monitoring Agent på datorn så att datorn kan rapportera till lösningen. Om du är onboarding en dator som för närvarande hanteras av System Center Operations Manager, en ny agent krävs inte, arbetsyteinformation har angetts i den befintliga agenten.

## <a name="onboard-machines-in-the-workspace"></a>Publicera datorer på arbetsytan

Från ditt Automation-kontot väljer **inventering** eller **ändringsspårning** under **KONFIGURATIONSHANTERING**, eller **uppdateringshantering** under **UPPDATERINGSHANTERING**.

Välj **hantera datorer**. Detta öppnar den **hantera datorer** sidan. Den här sidan kan du aktivera lösningen på en utvald grupp datorer, alla tillgängliga datorer eller aktivera lösning för alla aktuella datorer och aktivera det på alla framtida datorer.

![Sparade sökningar](media/automation-onboard-solutions-from-automation-account/managemachines.png)

### <a name="selected-machines"></a>Valda datorer

Välj för att aktivera lösning för en eller flera datorer **aktivera på valda datorer** och på **lägga till** bredvid varje dator som du vill lägga till i lösningen. Den här uppgiften lägger till de valda datornamn datorgruppen sparade sökfråga för lösningen.

### <a name="all-available-machines"></a>Alla tillgängliga datorer

Välj för att aktivera lösning för alla tillgängliga datorer **aktivera på alla tillgängliga datorer**. Detta inaktiverar kontrollen om du vill lägga till datorer i taget. Den här uppgiften lägger till alla namnen på de datorer som rapporterar till arbetsytan till i datorgruppen spara en sökfråga.

### <a name="all-available-and-future-machines"></a>Alla tillgängliga och framtida datorer

Välj för att aktivera lösning för alla tillgängliga datorer och alla framtida datorer **aktivera på alla tillgängliga och framtida datorer**. Det här alternativet tar bort de sparade sökningar och Scope konfigurationer från arbetsytan. Då öppnas lösningen till alla Azure och Azure-datorer som rapporterar till arbetsytan.

## <a name="next-steps"></a>Nästa steg

Fortsätta att självstudier i lösningar för att lära sig hur de används.

* [Självstudiekurs – hantera uppdateringar för den virtuella datorn](automation-tutorial-update-management.md)

* [Självstudiekurs – identifiera programvara på en virtuell dator](automation-tutorial-installed-software.md)

* [Kursen - felsöka ändringar på en virtuell dator](automation-tutorial-troubleshoot-changes.md)