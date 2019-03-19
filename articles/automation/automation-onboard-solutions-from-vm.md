---
title: Publicera lösningar för uppdateringshantering, ändringsspårning och inventering från en Azure-dator
description: Lär dig hur du publicerar Azure-datorer med lösningar som uppdateringshantering, ändringsspårning och inventering som ingår i Azure Automation.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: conceptual
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 70d9957ae5f0ec43269d371c96e3722e52edb26d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57837770"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Publicera lösningar från Azure-datorer som uppdateringshantering, ändringsspårning och inventering

Azure Automation tillhandahåller lösningar som hjälper dig hantera säkerhetsuppdateringar för operativsystemet, spåra ändringar och inventering vad som är installerat på datorerna. Det finns flera sätt att registrera datorer. Du kan registrera lösningen från en virtuell dator, [från ditt Automation-konto](automation-onboard-solutions-from-automation-account.md), [från flera datorer-surfning](automation-onboard-solutions-from-browse.md), eller genom att använda en [runbook](automation-onboard-solutions.md). Den här artikeln beskrivs registrering dessa lösningar från Azure-datorer.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="enable-the-solutions"></a>Aktivera lösningar

Gå till en befintlig virtuell dator. Under **OPERATIONS**väljer **uppdateringshantering**, **inventering**, eller **ändringsspårning**. Den virtuella datorn kan finnas i valfri region oavsett platsen för ditt Automation-konto.

Se till att aktivera lösningen för den virtuella datorn endast **aktivera för den här virtuella datorn** har valts. Att publicera flera datorer i lösningen, Välj **aktivera för virtuella datorer i den här prenumerationen**, och välj sedan **klickar du på datorer för att aktivera**. Mer information hur du publicera flera datorer på samma gång finns [publicera uppdateringshantering, ändringsspårning och inventering lösningar](automation-onboard-solutions-from-automation-account.md).

Välj den Azure Log Analytics-arbetsytan och Automation-konto och välj sedan **aktivera** att aktivera lösningen. Det tar upp till 15 minuter att aktivera lösningen.

![Publicera lösningen för uppdateringshantering](media/automation-onboard-solutions-from-vm/onboard-solution.png)

Gå till de andra lösningarna och välj sedan **aktivera**. Log Analytics-arbetsytan och Automation-konto listrutor har inaktiverats eftersom de här lösningarna använder samma arbetsyta och Automation-kontot till den tidigare aktiverade lösningen.

> [!NOTE]
> **Ändringsspårning** och **inventering** använder samma lösning. När någon av följande lösningar är aktiverad, aktiveras den andra också.

## <a name="scope-configuration"></a>Omfattningskonfigurationen

Varje lösning använder en omfattningskonfigurationen i arbetsytan för att fokusera på de datorer som får lösningen. Konfigurationen är en grupp med en eller flera sparade sökningar som används för att begränsa omfattningen av lösningen till specifika datorer. Åtkomst till omfattningskonfigurationer i ditt Automation-konto under **relaterade resurser**väljer **arbetsytan**. I arbetsytan under **DATAKÄLLOR för ARBETSYTA**väljer **Omfattningskonfigurationer**.

Om den valda arbetsytan inte redan har lösningarna för uppdateringshantering eller ändringsspårning, skapas följande omfång konfigurationer:

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

Om den valda arbetsytan har redan lösningen kan distribueras om inte lösningen och konfigurationen har lagts till inte.

Välj ellipserna (**...** ) på alla konfigurationer och välj sedan **redigera**. I den **redigera omfattningskonfiguration** väljer **Välj datorgrupper**. Den **datorgrupper** visar sparade sökningar som används för att skapa omfattningskonfigurationen.

## <a name="saved-searches"></a>Sparade sökningar

När en dator läggs till uppdateringshantering, ändringsspårning eller lösningar för inventering, har datorn lagts till en av två sparade sökningar i din arbetsyta. Sparade sökningar är frågor som innehåller de datorer som är avsedda för dessa lösningar.

Gå till din arbetsyta. Under **Allmänt**väljer **sparade sökningar**. Två sparade sökningar som används av dessa lösningar visas i följande tabell:

|Namn     |Kategori  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Uppdateringar        | Updates__MicrosoftDefaultComputerGroup         |

Välj något av de sparade sökningarna kan du se frågan som används för att fylla i gruppen. Följande bild visar frågan och dess resultat:

![Sparade sökningar](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="unlink-workspace"></a>Ta bort arbetsytans länk

Följande lösningar är beroende av en Log Analytics-arbetsyta:

* [Hantering av uppdateringar](automation-update-management.md)
* [Spårning av ändringar](automation-change-tracking.md)
* [Starta/Stoppa VM under kontorstid](automation-solution-vm-management.md)

Om du inte längre vill integrera ditt Automation-konto med en arbetsyta för Log Analytics kan du kan ta bort länken till ditt konto direkt från Azure-portalen.  Innan du fortsätter måste du först ta bort lösningar som tidigare nämnts, annars den här processen kommer inte att kunna fortsätta. Läsa artikeln för den lösning du har importerat för att förstå de steg som krävs för att ta bort den.

När du tar bort dessa lösningar kan utföra du följande steg om du vill ta bort länken till ditt Automation-konto.

> [!NOTE]
> Vissa lösningar, inklusive tidigare versioner av Azure SQL-övervakningslösning kan ha skapat automation-tillgångar och kan också behöva tas bort innan du arbetsytans länk.

1. Öppna ditt Automation-konto från Azure-portalen och på Automation-konto väljer du sidan **länkade arbetsytan** under avsnittet **relaterade resurser** till vänster.

1. På sidan Avlänka från arbetsytan **ta bort arbetsytans länk**.

   ![Avlänka arbetsytssidan](media/automation-onboard-solutions-from-vm/automation-unlink-workspace-blade.png).

   Ett meddelande visas där du bekräftar att du vill fortsätta.

1. Medan Azure Automation försöker ta bort länken till konton som Log Analytics-arbetsytan, kan du följa förloppet under **meddelanden** på menyn.

Om du använder lösningen för uppdateringshantering, kan om du vill du ta bort följande objekt som inte längre behövs när du har tagit bort lösningen.

* Uppdatera schemalägger, var och en har namn som matchar de uppdateringsdistributioner du skapat)

* Hybrid worker-grupper som skapats för lösningen – var och en namnges på samma sätt till av typen machine1.contoso.com_9ceb8108 - 26 c 9-4051-b6b3-227600d715c8).

Om du har använt Starta/stoppa virtuella datorer vid låg belastning på nätverket lösning kan om du vill du ta bort följande objekt som inte längre behövs när du har tagit bort lösningen.

* Starta och stoppa scheman för VM-runbook
* Starta och stoppa Virtuella runbooks
* Variabler

## <a name="next-steps"></a>Nästa steg

Fortsätt till självstudierna för lösningar och lär dig att använda dem:

* [Självstudie – hantera uppdateringar för din virtuella dator](automation-tutorial-update-management.md)
* [Självstudie – identifiera programvara på en virtuell dator](automation-tutorial-installed-software.md)
* [Självstudie – Felsök ändringar på en virtuell dator](automation-tutorial-troubleshoot-changes.md)
