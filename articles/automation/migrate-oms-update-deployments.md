---
title: Migrera dina distributioner av OMS till Azure
description: Den här artikeln beskriver hur du migrerar din befintliga OMS distributioner till Azure
services: automation
ms.service: automation
ms.subservice: update-management
author: georgewallace
ms.author: gwallace
ms.date: 07/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9a897cdb10506939232f667250dfb0dbaf196bc0
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56822233"
---
# <a name="migrate-your-oms-update-deployments-to-azure"></a>Migrera dina distributioner av OMS till Azure

Operations Management Suite (OMS)-portalen som [inaktuell](../azure-monitor/platform/oms-portal-transition.md). Alla funktioner som fanns i OMS-portalen för hantering av uppdateringar är tillgängliga i Azure-portalen. Den här artikeln innehåller information som du behöver för att migrera till Azure-portalen.

## <a name="key-information"></a>Viktig information

* Befintliga distributioner fortsätter att fungera. När du har återskapat distribution i Azure, kan du ta bort gamla distributionen från OMS.
* Alla befintliga funktioner i OMS finns tillgängliga i Azure, mer information om uppdateringshantering finns [översikt över uppdateringshantering](automation-update-management.md).

## <a name="access-the-azure-portal"></a>Tillgång till Azure portal

OMS-arbetsytan klickar du på **öppna i Azure**. Navigerar till Log Analytics-arbetsytan som används av OMS.

![Öppna i Azure – OMS-portalen](media/migrate-oms-update-deployments/link-to-azure-portal.png)

I Azure-portalen klickar du på **Automation-konto**

![Azure Monitor-loggar](media/migrate-oms-update-deployments/log-analytics.png)

I ditt Automation-konto klickar du på **uppdateringshantering** så att du öppnar uppdateringshantering.

![Uppdateringshantering](media/migrate-oms-update-deployments/azure-automation.png)

I framtiden går du direkt till Azure-portalen under **alla tjänster**väljer **Automationskonton** under **hanteringsverktyg**, Välj lämplig Automation Kontot och klicka på **uppdateringshantering**.

## <a name="recreate-existing-deployments"></a>Återskapa befintliga distributioner

Alla distributioner av uppdateringar som skapats i OMS-portalen har en [sparad sökning](../azure-monitor/platform/computer-groups.md) även känd som en datorgrupp med samma namn som uppdateringsdistributionen som finns. Den sparade sökningen innehåller listan över datorer som har schemalagts i uppdateringsdistributionen.

![Uppdateringshantering](media/migrate-oms-update-deployments/oms-deployment.png)

Följ dessa steg om du vill använda den här befintliga sparad sökning:

Du vill skapa en ny uppdatering distribution, gå till Azure-portalen väljer Automation-kontot som används och klickar på **uppdateringshantering**. Klicka på **distribution av schemauppdatering**.

![Distribution av schemauppdatering](media/migrate-oms-update-deployments/schedule-update-deployment.png)

Den **ny Uppdateringsdistribution** öppnas fönstret. Ange värden för de egenskaper som beskrivs i följande tabell och klicka sedan på **skapa**:

För datorer som ska uppdateras, väljer du den sparade sökningen som används av den befintliga OMS-distributionen.

| Egenskap  | Beskrivning |
| --- | --- |
|Namn |Unikt namn som identifierar uppdateringsdistributionen. |
|Operativsystem| Välj **Linux** eller **Windows**.|
|Datorer som ska uppdateras |Välj en sparad sökning eller en importerad grupp, eller välj Dator i listrutan och välj enskilda datorer. Om du väljer **Datorer** visas beredskapen för datorn i kolumnen **Uppdatera agentberedskap**.</br> Läs om de olika metoderna för att skapa datorgrupper i Azure Monitor-loggar i [datorgrupper i Azure Monitor-loggar](../azure-monitor/platform/computer-groups.md) |
|Uppdatera klassificeringar|Välj de uppdateringsklassificeringar som du behöver. CentOS stöder inte det direkt.|
|Uppdateringar som ska uteslutas|Ange uppdateringarna som ska uteslutas. För Windows, ange KB-artikel utan den **KB** prefix. Ange paketnamnet för Linux, eller Använd ett jokertecken.  |
|Schemainställningar|Välj tid att starta och välj sedan antingen **när** eller **återkommande** för upprepningen.|| Underhållsperiod |Antal minuter som angetts för uppdateringar. Värdet får inte vara mindre än 30 minuter eller mer än 6 timmar. |
| Underhållsperiod |Antal minuter som angetts för uppdateringar. Värdet kan inte vara mindre än 30 minuter och högst 6 timmar |
| Starta om kontroll| Detemines hur omstarter ska hanteras.</br>De tillgängliga alternativen är:</br>Starta om vid behov (standard)</br>Starta alltid om</br>Starta aldrig om</br>Endast omstart – uppdateringar installeras inte|

Klicka på **schemalagda uppdateringsdistributioner** att visa status för den nyligen skapade uppdateringsdistributionen.

![ny uppdateringsdistribution](media/migrate-oms-update-deployments/new-update-deployment.png)

Som tidigare nämnts, när din nya distributioner konfigureras via Azure-portalen kan befintliga distributioner tas bort från OMS-portalen.

## <a name="next-steps"></a>Nästa steg

Mer information om uppdateringshantering i Azure finns [uppdateringshantering](automation-update-management.md)
