---
title: Ta bort länk till Azure Automation-konto från Log Analytics
description: Den här artikeln innehåller en översikt över hur du ta bort länken till ditt Azure Automation-konto från Log Analytics-arbetsytan.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1328ce8c306188c32bce5385f58f118a63c08deb
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39426541"
---
# <a name="how-to-unlink-your-automation-account-from-a-log-analytics-workspace"></a>Hur du ta bort länken till ditt Automation-konto från en Log Analytics-arbetsyta

Azure Automation kan integreras med Log Analytics för att inte bara har stöd för övervakning av runbookjobb för alla dina Automation-konton, men krävs även när du importerar följande lösningar som är beroende av Log Analytics:

* [Hantering av uppdateringar](../operations-management-suite/oms-solution-update-management.md)
* [Spårning av ändringar](../log-analytics/log-analytics-change-tracking.md)
* [Starta/Stoppa VM under kontorstid](automation-solution-vm-management.md)

Om du inte längre vill integrera ditt Automation-konto med Log Analytics kan du kan ta bort länken till ditt konto direkt från Azure-portalen.  Innan du fortsätter måste du först ta bort lösningar som tidigare nämnts, annars den här processen kommer inte att kunna fortsätta. Läsa artikeln för den lösning du har importerat för att förstå de steg som krävs för att ta bort den.

När du tar bort dessa lösningar kan utföra du följande steg om du vill ta bort länken till ditt Automation-konto.

> [!NOTE]
> Vissa lösningar, inklusive tidigare versioner av Azure SQL-övervakningslösning kan ha skapat automation-tillgångar och kan också behöva tas bort innan du arbetsytans länk.

## <a name="unlink-workspace"></a>Ta bort arbetsytans länk

1. Öppna ditt Automation-konto från Azure-portalen och på Automation-konto väljer du sidan **länkade arbetsytan** under avsnittet **relaterade resurser** till vänster.

1. På sidan Avlänka från arbetsytan **ta bort arbetsytans länk**.

   ![Avlänka arbetsytssidan](media/automation-unlink-from-log-analytics/automation-unlink-workspace-blade.png).

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

Om du vill konfigurera om ditt Automation-konto för att integrera med Log Analytics, se [vidarebefordrar jobbstatus och jobbströmmar från Automation till Log Analytics](automation-manage-send-joblogs-log-analytics.md).