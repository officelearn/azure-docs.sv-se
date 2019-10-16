---
title: Publicera Uppdateringshantering-, Ändringsspårning-och inventerings lösningar från en virtuell Azure-dator
description: Lär dig att publicera en virtuell Azure-dator med Uppdateringshantering, Ändringsspårning och inventerings lösningar som ingår i Azure Automation.
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 03/20/2019
ms.topic: conceptual
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 0069d2e8ccd3b4f65ced8b6e18ce568689f81e14
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374413"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Publicera Uppdateringshantering-, Ändringsspårning-och inventerings lösningar från en virtuell Azure-dator

Azure Automation innehåller lösningar som hjälper dig att hantera säkerhets uppdateringar av operativ systemet, spåra ändringar och inventering av de som är installerade på datorerna. Det finns flera sätt att publicera datorer på. Du kan publicera lösningen från en virtuell dator, [från ditt Automation-konto](automation-onboard-solutions-from-automation-account.md), [från att bläddra flera datorer](automation-onboard-solutions-from-browse.md)eller genom att använda en [Runbook](automation-onboard-solutions.md). Den här artikeln beskriver hur du registrerar dessa lösningar från en virtuell Azure-dator.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="enable-the-solutions"></a>Aktivera lösningarna

Gå till en befintlig virtuell dator. Under **åtgärder**väljer du **uppdaterings hantering**, **inventering**eller **ändrings spårning**. Den virtuella datorn kan finnas i vilken region som helst, oavsett platsen för ditt Automation-konto. När du registrerar en lösning från en virtuell dator måste du ha behörigheten `Microsoft.OperationalInsights/workspaces/read` för att avgöra om den virtuella datorn har publicerats till en arbets yta. Om du vill veta mer om ytterligare behörigheter som krävs i allmänhet, se [behörigheter som behövs för att publicera datorer](automation-role-based-access-control.md#onboarding).

Om du bara vill aktivera lösningen för den virtuella datorn kontrollerar du att **Aktivera för den här virtuella datorn** är markerat. Om du vill publicera flera datorer i lösningen väljer du **Aktivera för virtuella datorer i den här prenumerationen**. Välj sedan **Klicka för att välja datorer som ska aktive ras**. Information om hur du kan publicera flera datorer samtidigt finns i [uppdateringshantering, ändringsspårning och inventerings lösningar](automation-onboard-solutions-from-automation-account.md).

Välj Azure Log Analytics-arbetsytan och automation-kontot och välj sedan **Aktivera** för att aktivera lösningen. Det tar upp till 15 minuter att aktivera lösningen.

![Publicera Uppdateringshantering-lösningen](media/automation-onboard-solutions-from-vm/onboard-solution.png)

Gå till de andra lösningarna och välj sedan **Aktivera**. List rutorna Log Analytics arbets yta och Automation-konto är inaktiverade eftersom dessa lösningar använder samma arbets yta och Automation-konto som den tidigare aktiverade lösningen.

> [!NOTE]
> I **ändrings spårning** och **inventering** används samma lösning. Om någon av dessa lösningar är aktive rad aktive ras även den andra.

## <a name="scope-configuration"></a>Omfattnings konfiguration

Varje lösning använder en omfattnings konfiguration på arbets ytan för att rikta in de datorer som hämtar lösningen. Omfattnings konfigurationen är en grupp med en eller flera sparade sökningar som används för att begränsa lösningens omfattning till vissa datorer. Om du vill komma åt konfigurationerna för omfattningar går du till ditt Automation-konto och väljer **arbets yta**under **relaterade resurser**. I arbets ytan, under **arbets ytans data källor**, väljer du **scope-konfigurationer**.

Om den valda arbets ytan inte redan har Uppdateringshantering-eller Ändringsspårning-lösningar skapas följande omfattningar:

* **MicrosoftDefaultScopeConfig – ChangeTracking**

* **MicrosoftDefaultScopeConfig – uppdateringar**

Om den valda arbets ytan redan har lösningen omdistribueras inte lösningen och omfattnings konfigurationen läggs inte till.

Välj ellipserna ( **...** ) på någon av konfigurationerna och välj sedan **Redigera**. I fönstret **Redigera omfattnings konfiguration** väljer du **Välj dator grupper**. I fönstret **dator grupper** visas sparade sökningar som används för att skapa omfattnings konfigurationen.

## <a name="saved-searches"></a>Sparade sökningar

När en dator läggs till i Uppdateringshantering, Ändringsspårning eller inventerings lösningar läggs datorn till i en av två sparade sökningar i din arbets yta. De sparade sökningarna är frågor som innehåller de datorer som är riktade till dessa lösningar.

Gå till din arbetsyta. Under **Allmänt**väljer du **sparade sökningar**. De två sparade sökningar som används av dessa lösningar visas i följande tabell:

|Namn     |Kategori  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Uppdateringar        | Updates__MicrosoftDefaultComputerGroup         |

Välj någon av de sparade sökningarna för att visa frågan som används för att fylla i gruppen. Följande bild visar frågan och dess resultat:

![Sparade sökningar](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="unlink-workspace"></a>Ta bort länk till arbets yta

Följande lösningar är beroende av en Log Analytics arbets yta:

* [Hantering av uppdateringar](automation-update-management.md)
* [Spårning av ändringar](automation-change-tracking.md)
* [Starta/stoppa virtuella datorer när de inte används](automation-solution-vm-management.md)

Om du inte längre vill integrera ditt Automation-konto med en Log Analytics arbets yta kan du ta bort länken till ditt konto direkt från Azure Portal.  Innan du fortsätter måste du först ta bort de lösningar som nämns ovan, annars kommer den här processen att förhindras från att fortsätta. Läs artikeln för den specifika lösning som du har importerat för att förstå de steg som krävs för att ta bort den.

När du har tagit bort dessa lösningar kan du utföra följande steg för att ta bort länken till ditt Automation-konto.

> [!NOTE]
> Vissa lösningar inklusive tidigare versioner av Azure SQL-övervaknings lösningen kan ha skapat Automation-till gångar och kan också behöva tas bort innan du tar bort länken till arbets ytan.

1. Öppna ditt Automation-konto från Azure Portal och välj **länkad arbets yta** under avsnittet **relaterade resurser** till vänster på sidan Automation-konto.

2. På sidan ta bort länk till arbets yta klickar du på **ta bort arbets ytan**.

   ![Sidan ta bort länk till arbets yta](media/automation-onboard-solutions-from-vm/automation-unlink-workspace-blade.png).

   Ett meddelande visas där du bekräftar att du vill fortsätta.

3. Medan Azure Automation försöker ta bort länken till kontot Log Analytics arbets ytan, kan du följa förloppet under **meddelanden** på menyn.

Om du använde Uppdateringshantering-lösningen kanske du vill ta bort följande objekt som inte längre behövs efter att du har tagit bort lösningen.

* Uppdaterings scheman-varje kommer att ha namn som matchar de uppdaterings distributioner som du har skapat)

* Hybrid Worker-grupper som har skapats för lösningen – var och en får samma namn som machine1. contoso. com _9ceb8108-26c9-4051-b6b3-227600d715c8).

Om du använde Starta/stoppa virtuella datorer när de inte används-lösningen kanske du vill ta bort följande objekt som inte längre behövs efter att du har tagit bort lösningen.

* Starta och stoppa virtuella dator Runbook-scheman
* Starta och stoppa VM-Runbooks
* Variabler

Alternativt kan du också ta bort länken till arbets ytan från ditt Automation-konto från din Log Analytics-arbetsyta. På arbets ytan väljer du **Automation-konto** under **relaterade resurser**. På sidan Automation-konto väljer du **ta bort länk till konto**.

## <a name="clean-up-resources"></a>Rensa resurser

Så här tar du bort en virtuell dator från Uppdateringshantering:

* I arbets ytan Log Analytics tar du bort den virtuella datorn från den sparade sökningen efter omfattnings konfigurationen `MicrosoftDefaultScopeConfig-Updates`. Sparade sökningar hittar du under **Allmänt** på arbets ytan.
* Ta bort [Microsoft Monitoring Agent](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) eller [Log Analytics agent för Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Nästa steg

Fortsätt till självstudierna för lösningarna och lär dig hur du använder dem:

* [Självstudie – hantera uppdateringar för din virtuella dator](automation-tutorial-update-management.md)
* [Självstudie – identifiera program vara på en virtuell dator](automation-tutorial-installed-software.md)
* [Självstudie – felsöka ändringar på en virtuell dator](automation-tutorial-troubleshoot-changes.md)
