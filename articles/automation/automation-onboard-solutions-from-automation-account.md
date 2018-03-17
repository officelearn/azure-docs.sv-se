---
title: "Lär dig hur du publicera uppdateringshantering, ändringsspårning och lager lösningar i Azure Automation"
description: "Lär dig hur att publicera ett Azure-virtuella datorn med uppdateringshantering, ändringsspårning och lager-lösningar som ingår i Azure Automation"
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.devlang: na
ms.tgt_pltfrm: na
ms.custom: mvc
ms.openlocfilehash: 65bf0d98da8111e986d5dbdfd58f1692d40ee286
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>Publicera uppdateringshantering, ändringsspårning och lager-lösningar

Azure Automation ger lösningar för att hantera uppdateringar av operativsystemet säkerhet, spåra ändringar och inventera vad som är installerat på datorerna. Det finns flera sätt att publicera datorer, kan du publicera lösningen [från en virtuell dator](automation-onboard-solutions-from-vm.md), från ditt Automation-konto eller av [runbook](automation-onboard-solutions.md). Den här artikeln beskriver onboarding dessa lösningar från ditt Automation-konto.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure på https://portal.azure.com

## <a name="enable-solutions"></a>Aktivera lösningar

Navigera till ditt Automation-konto och välj antingen **inventering** eller **ändringsspårning** under **KONFIGURATIONSHANTERING**.

Välj Log analytics-arbetsyta och Automation-kontot och klicka på **aktivera** att aktivera lösningen. Det tar upp till 15 minuter att aktivera lösningen.

![Publicera lager-lösning](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

Lösningen Ändringsspårning och inventering ger dig möjlighet att [spåra ändringar](automation-vm-change-tracking.md) och [inventering](automation-vm-inventory.md) på dina virtuella datorer. I det här steget aktiverar du lösningen för en virtuell dator.

När meddelandet för lösning för publicering av ändringsspårning och inventering har slutförts klickar du på **Uppdateringshantering** under **Konfigurationshantering**.

Med uppdateringshanteringslösningen kan du hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer i Windows. Du kan utvärdera statusen för tillgängliga uppdateringar, schemalägga installation av nödvändiga uppdateringar och granska distributionsresultat för att verifiera att uppdateringarna har tillämpats på den virtuella datorn. Den här åtgärden aktivera lösningen för den virtuella datorn.

Välj **uppdateringshantering** under **UPPDATERINGSHANTERING**. Log Analytics-arbetsytan som är vald är samma arbetsyta som används i föregående steg. Klicka på **Aktivera** för att publicera lösningen för uppdateringshantering. Det tar upp till 15 minuter att aktivera lösningen.

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