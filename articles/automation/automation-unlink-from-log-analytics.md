---
title: Ta bort länk till Azure Automation-konto från Log Analytics
description: Den här artikeln innehåller en översikt över hur du avlänkar Azure Automation-konto från logganalys-arbetsytan.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f51103045f6a0cac1b1ed4f32200eaf7bef9cf24
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34193884"
---
# <a name="how-to-unlink-your-automation-account-from-a-log-analytics-workspace"></a>Hur du avlänkar ditt Automation-konto från logganalys-arbetsytan

Azure Automation kan integreras med logganalys som inte bara stöd för övervakning av runbook-jobb över alla Automation-konton, men den krävs också när du importerar följande lösningar som är beroende av logganalys:

* [Hantering av uppdateringar](../operations-management-suite/oms-solution-update-management.md)
* [Spårning av ändringar](../log-analytics/log-analytics-change-tracking.md)
* [Starta/Stoppa VMs kontorstid](automation-solution-vm-management.md)

Om du inte längre vill integrera ditt Automation-konto med logganalys Avlänka du ditt konto direkt från Azure-portalen.  Innan du fortsätter måste du först ta bort de lösningar som tidigare nämnts, annars kommer den här processen hindras från att du fortsätter. Granska i avsnittet för en viss lösning som du har importerat för att förstå de steg som krävs för att ta bort den.

När du tar bort dessa lösningar kan du utföra följande steg om du vill Avlänka Automation-konto.

> [!NOTE]
> Vissa lösningar inklusive tidigare versioner av Azure SQL-övervakningslösning kan ha skapat automation tillgångar och kan även behöva tas bort innan du bryta länken till arbetsytan.

## <a name="unlink-workspace"></a>Avlänka arbetsytan

1. Öppna ditt Automation-konto från Azure-portalen och på automatisering konto sidan Välj **Avlänka arbetsytan** under avsnittet **relaterade resurser** till vänster.

   ![Avlänka arbetsyta](media/automation-unlink-from-log-analytics/automation-unlink-workspace-option.png)

1. På sidan Avlänka från arbetsytan **Avlänka arbetsytan**.

   ![Avlänka arbetsytssidan](media/automation-unlink-from-log-analytics/automation-unlink-workspace-blade.png).

   Ett meddelande visas där du bekräftar att du vill fortsätta.

1. Medan Azure Automation försöker Avlänka kontot logganalys-arbetsytan, du kan följa förloppet under **meddelanden** på menyn.

Om du har använt lösning för hantering av uppdateringar kanske (valfritt) du vill ta bort följande objekt som inte längre behövs när du tar bort lösningen.

* Uppdatera scheman.  Varje har namn som matchar uppdateringsdistributioner som du skapade)

* Hybrid worker grupper som skapats för lösningen.  Varje får namnet på samma sätt att machine1.contoso.com_9ceb8108 - 26 c 9-4051-b6b3-227600d715c8).

Om du använde Starta/stoppa virtuella datorer under låg belastning på nätverket lösning kanske eventuellt du vill ta bort följande objekt som inte längre behövs när du tar bort lösningen.

* Starta och stoppa scheman för VM-runbook
* Starta och stoppa runbooks för VM
* Variabler

## <a name="next-steps"></a>Nästa steg

Om du vill konfigurera om ditt Automation-konto för att integrera med Log Analytics finns [vidarebefordra jobbstatus och jobbet strömmar från Automation till logganalys](automation-manage-send-joblogs-log-analytics.md).