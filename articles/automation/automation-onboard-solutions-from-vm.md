---
title: Inbyggd uppdateringshantering, ändringsspårning och lagerlösningar från en Virtuell Azure-dator
description: Lär dig hur du ar ombord på en virtuell Azure-dator med lösningar för uppdateringshantering, ändringsspårning och lager som ingår i Azure Automation.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 621b429f5dc3a6b6620e4d41ad46763e1d4fa226
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299542"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Inbyggda uppdateringshanterings-, ändringsspårnings- och lagerlösningar från en virtuell Azure-dator

Azure Automation tillhandahåller lösningar som hjälper dig att hantera säkerhetsuppdateringar för operativsystemet, spåra ändringar och inventera det som är installerat på dina datorer. Det finns flera sätt att omborda maskiner. Du kan gå in på lösningen från en virtuell dator, [från ditt Automation-konto,](automation-onboard-solutions-from-automation-account.md) [från att bläddra flera datorer](automation-onboard-solutions-from-browse.md)eller med hjälp av en [runbook](automation-onboard-solutions.md). Den här artikeln beskriver introduktion av dessa lösningar från en virtuell Azure-dator.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="enable-the-solutions"></a>Aktivera lösningarna

Aktivera först en eller alla tre lösningarna på den virtuella datorn:

1. I [Azure-portalen](https://portal.azure.com)väljer du **virtuella datorer** i den vänstra rutan eller söker efter och väljer **Virtuella datorer** på **startsidan.**
2. Välj den virtuella dator som du vill aktivera en lösning för.
3. Välj **Uppdatera hantering,** **Lager**eller **Ändra spårning**under **Operationer**på sidan Virtuell dator. Den virtuella datorn kan finnas i valfri region oavsett var ditt Automation-konto finns. När du ã¤kar en lösning frÃ¥n en virtuell dator måste du ha `Microsoft.OperationalInsights/workspaces/read` behörighet att avgöra om den virtuella datorn är 900 till en arbetsyta. Mer information om ytterligare behörigheter som krävs finns i [behörigheter som behövs för att registrera datorer](automation-role-based-access-control.md#onboarding).

Mer information om hur du ar ombord på flera datorer samtidigt finns [i Lösningar för uppdateringshantering, ändringsspårning och lager.](automation-onboard-solutions-from-automation-account.md)

Välj arbetsytan Azure Log Analytics och Automation-kontot och välj sedan **Aktivera** för att aktivera lösningen. Det tar upp till 15 minuter att aktivera lösningen.

![Ombord på lösning för uppdateringshantering](media/automation-tutorial-update-management/manageupdates-update-enable.png)

Gå till de andra lösningarna och välj sedan **Aktivera**. Listrutorna Log Analytics-arbetsyta och Automation-konto är inaktiverade eftersom dessa lösningar använder samma arbetsyta och Automation-konto som den tidigare aktiverade lösningen.

> [!NOTE]
> **Ändringsspårning** **och Lager** använder samma lösning. När en av dessa lösningar är aktiverad aktiveras även den andra.

## <a name="scope-configuration"></a>Konfiguration av scope

Varje lösning använder en scopekonfiguration på arbetsytan för att rikta in sig på de datorer som hämtar lösningen. Scopekonfigurationen är en grupp med en eller flera sparade sökningar som används för att begränsa lösningens omfattning till specifika datorer. Om du vill komma åt scopekonfigurationerna väljer du **Arbetsyta**i ditt Automation-konto under **Relaterade resurser**. Välj **Scopekonfigurationer**under **Datakällor**på arbetsytan på arbetsytan .

Om den valda arbetsytan inte redan har lösningarna Uppdateringshantering eller Ändringsspårning skapas följande scopekonfigurationer:

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-uppdateringar**

Om den valda arbetsytan redan har lösningen distribueras inte lösningen om och scopekonfigurationen läggs inte till.

Välj ellipserna (**...**) på någon av konfigurationerna och välj sedan **Redigera**. Välj **Välj datorgrupper**i **konfigurationsfönstret Redigera scope** . I fönstret **Datorgrupper** visas de sparade sökningar som används för att skapa scopekonfigurationen.

## <a name="saved-searches"></a>Sparade sökningar

När en dator läggs till i lösningarna Uppdateringshantering, Ändringsspårning eller Lager läggs datorn till i en av två sparade sökningar på arbetsytan. De sparade sökningarna är frågor som innehåller de datorer som är avsedda för dessa lösningar.

Gå till din arbetsyta. Under **Allmänt**väljer du **Sparade sökningar**. De två sparade sökningar som används av dessa lösningar visas i följande tabell:

|Namn     |Kategori  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  Ändra Spåra       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Uppdateringar        | Updates__MicrosoftDefaultComputerGroup         |

Välj någon av de sparade sökningarna om du vill visa frågan som används för att fylla i gruppen. Följande bild visar frågan och dess resultat:

![Sparade sökningar](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="unlink-workspace"></a>Ta bort arbetsytans länk

Följande lösningar är beroende av en Log Analytics-arbetsyta:

* [Uppdateringshantering](automation-update-management.md)
* [Spårning av ändringar](automation-change-tracking.md)
* [Start/stoppa virtuella datorer under ledig tid](automation-solution-vm-management.md)

Om du bestämmer dig för att du inte längre vill integrera ditt Automation-konto med en Log Analytics-arbetsyta kan du ta bort länken till ditt konto direkt från Azure-portalen.  Innan du fortsätter måste du först ta bort de lösningar som nämndes tidigare, annars förhindras den här processen från att fortsätta. Läs artikeln för den specifika lösning som du har importerat för att förstå de steg som krävs för att ta bort den.

När du har tagit bort dessa lösningar kan du utföra följande steg för att ta bort länken till ditt Automation-konto.

> [!NOTE]
> Vissa lösningar, inklusive tidigare versioner av Azure SQL-övervakningslösningen, kan ha skapat automatiseringsresurser och kan också behöva tas bort innan arbetsytan tas bort.

1. Öppna ditt Automation-konto på sidan Automation-konto på sidan Automation-konto **under** avsnittet **Relaterade resurser** till vänster.

2. Klicka på **Ta bort länk arbetsyta**på sidan Ta bort länk.

   ![Ta bort länkning av arbetsyta](media/automation-onboard-solutions-from-vm/automation-unlink-workspace-blade.png).

   Ett meddelande visas där du bekräftar att du vill fortsätta.

3. Medan Azure Automation försöker ta bort länken till kontot din Log Analytics-arbetsyta kan du spåra förloppet under **Meddelanden** från menyn.

Om du använde lösningen Update Management kanske du vill ta bort följande objekt som inte längre behövs när du har tagit bort lösningen.

* Uppdateringsscheman - Var och en kommer att ha namn som matchar de uppdateringsdistributioner som du har skapat.

* Hybridarbetaregrupper som skapats för lösningen - Var och en namnges på samma sätt som machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Om du använde start-/stopp-virtuella datorer under ledig tid kanske du vill ta bort följande objekt som inte längre behövs när du har tagit bort lösningen.

* Starta och stoppa VM-runbookscheman
* Starta och stoppa VM-runbooks
* Variabler

Du kan också ta bort länken till arbetsytan från ditt Automation-konto från logganalysarbetsytan. På arbetsytan väljer du **Automation-konto** under **Relaterade resurser**. På sidan Automation-konto väljer du **Ta bort länkkonto**.

## <a name="clean-up-resources"></a>Rensa resurser

Så här tar du bort en virtuell dator från uppdateringshantering:

* I logganalysarbetsytan tar du bort den virtuella datorn `MicrosoftDefaultScopeConfig-Updates`från den sparade sökningen efter scopekonfigurationen . Sparade sökningar hittar du under **Allmänt** på arbetsytan.
* Ta bort [Microsoft Monitoring-agenten](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) eller [Log Analytics-agenten för Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Nästa steg

Fortsätt till självstudierna för lösningarna för att lära dig hur du använder dem:

* [Självstudiekurs - Hantera uppdateringar för din virtuella dator](automation-tutorial-update-management.md)

* [Självstudiekurs - Identifiera programvara på en virtuell dator](automation-tutorial-installed-software.md)

* [Självstudiekurs - Felsöka ändringar på en virtuell dator](automation-tutorial-troubleshoot-changes.md)
