---
title: Migrera dina OMS-uppdaterings distributioner till Azure
description: Den här artikeln beskriver hur du migrerar befintliga OMS-uppdaterings distributioner till Azure
services: automation
ms.subservice: update-management
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: 2660e4a348d2ffd71f912ff80c36a5a9a3c9fe88
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75417784"
---
# <a name="migrate-your-oms-update-deployments-to-azure"></a>Migrera dina OMS-uppdaterings distributioner till Azure

Hanterings portalen för Operations Management Suite (OMS) är [inaktuell](../azure-monitor/platform/oms-portal-transition.md). Alla funktioner som var tillgängliga i OMS-portalen för Uppdateringshantering finns i Azure Portal. Den här artikeln innehåller den information som du behöver för att kunna migrera till Azure Portal.

## <a name="key-information"></a>Nyckelinformation

* Befintliga distributioner fortsätter att fungera. När du har återskapat distributionen i Azure kan du ta bort din gamla distribution från OMS.
* Alla befintliga funktioner i OMS är tillgängliga i Azure, mer information om Uppdateringshantering finns i [uppdateringshantering översikt](automation-update-management.md).

## <a name="access-the-azure-portal"></a>Åtkomst till Azure-portalen

Från OMS-arbetsytan klickar du på **Öppna i Azure**. Detta navigerar till den Log Analytics arbets yta som OMS använde.

![Öppna i Azure-OMS-portalen](media/migrate-oms-update-deployments/link-to-azure-portal.png)

I Azure Portal klickar du på **Automation-konto**

![Azure Monitor-loggar](media/migrate-oms-update-deployments/log-analytics.png)

I ditt Automation-konto klickar du på **uppdateringshantering** för att öppna uppdateringshantering.

![Hantering av uppdateringar](media/migrate-oms-update-deployments/azure-automation.png)

I framtiden kan du gå direkt till Azure Portal. under **alla tjänster**väljer du Automation- **konton** under **hanterings verktyg**, väljer lämpligt Automation-konto och klickar på **uppdateringshantering**.

## <a name="recreate-existing-deployments"></a>Återskapa befintliga distributioner

Alla uppdaterings distributioner som skapas i OMS-portalen har en [Sparad sökning](../azure-monitor/platform/computer-groups.md) som också kallas en dator grupp, med samma namn som den uppdaterings distribution som finns. Den sparade sökningen innehåller en lista över datorer som har schemalagts i uppdaterings distributionen.

![Hantering av uppdateringar](media/migrate-oms-update-deployments/oms-deployment.png)

Följ dessa steg om du vill använda den befintliga sparade sökningen:

Om du vill skapa en ny uppdaterings distribution går du till Azure Portal, väljer det Automation-konto som används och klickar på **uppdateringshantering**. Klicka på **Schemalägg uppdaterings distribution**.

![Schemalägg uppdateringsdistributioner](media/migrate-oms-update-deployments/schedule-update-deployment.png)

Fönstret **ny uppdaterings distribution** öppnas. Ange värden för egenskaperna som beskrivs i följande tabell och klicka sedan på **skapa**:

För datorer som ska uppdateras väljer du den sparade sökning som används av den befintliga OMS-distributionen.

| Egenskap | Beskrivning |
| --- | --- |
|Namn |Unikt namn som identifierar uppdateringsdistributionen. |
|Operativsystem| Välj **Linux** eller **Windows**.|
|Datorer som ska uppdateras |Välj en sparad sökning eller en importerad grupp, eller välj Dator i listrutan och välj enskilda datorer. Om du väljer **Datorer** visas beredskapen för datorn i kolumnen **Uppdatera agentberedskap**.</br> Information om de olika metoderna för att skapa datorgrupper i Azure Monitor-loggar finns i [datorgrupper i Azure Monitor-loggar](../azure-monitor/platform/computer-groups.md) |
|Uppdatera klassificeringar|Välj alla uppdaterings klassificeringar som du behöver. CentOS stöder inte detta.|
|Uppdateringar som ska undantas|Ange de uppdateringar som ska undantas. För Windows anger du KB-artikeln utan **KB** -prefixet. För Linux anger du paket namnet eller använder ett jokertecken.  |
|Schemainställningar|Välj tid för start och välj sedan **en gång** eller **återkommande** för upprepningen. | 
| Underhålls period |Antal minuter som har angetts för uppdateringar. Värdet kan inte vara mindre än 30 minuter eller mer än 6 timmar. |
| Starta om kontroll| Fastställer hur omstarter ska hanteras.</br>De tillgängliga alternativen är:</br>Starta om vid behov (standard)</br>Starta alltid om</br>Starta aldrig om</br>Endast omstart – uppdateringar installeras inte|

Klicka på **schemalagda uppdaterings distributioner** om du vill visa statusen för den nyligen skapade uppdaterings distributionen.

![ny uppdaterings distribution](media/migrate-oms-update-deployments/new-update-deployment.png)

Som tidigare nämnts kan de befintliga distributionerna tas bort från OMS-portalen när dina nya distributioner har kon figurer ATS via Azure Portal.

## <a name="next-steps"></a>Nästa steg

Mer information om Uppdateringshantering i Azure finns i [uppdateringshantering](automation-update-management.md)
