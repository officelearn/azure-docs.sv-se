---
title: Migrera dina OMS-uppdaterings distributioner till Azure
description: Den här artikeln beskriver hur du migrerar befintliga OMS-uppdaterings distributioner till Azure
services: automation
ms.subservice: update-management
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: 910f284eedbf50be5b58b6c18f02e50adda35e9a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81680009"
---
# <a name="migrate-your-oms-update-deployments-to-azure"></a>Migrera dina OMS-uppdaterings distributioner till Azure

Hanterings portalen för Operations Management Suite (OMS) är [inaktuell](../azure-monitor/platform/oms-portal-transition.md). Alla funktioner som var tillgängliga i OMS-portalen för Uppdateringshantering finns i Azure Portal, via Azure Monitor loggar. Den här artikeln innehåller den information du behöver för att migrera till Azure Portal.

## <a name="key-information"></a>Viktig information

* Befintliga distributioner fortsätter att fungera. När du har återskapat distributionen i Azure kan du ta bort din gamla distribution från OMS.
* Alla befintliga funktioner i OMS är tillgängliga i Azure, mer information om Uppdateringshantering finns i [uppdateringshantering översikt](automation-update-management.md).

## <a name="access-the-azure-portal"></a>Åtkomst till Azure-portalen

Från OMS-arbetsytan klickar du på **Öppna i Azure**. Det här valet går till Log Analytics arbets ytan som OMS har använt.

![Öppna i Azure-OMS-portalen](media/migrate-oms-update-deployments/link-to-azure-portal.png)

I Azure Portal klickar du på **Automation-konto**

![Azure Monitor-loggar](media/migrate-oms-update-deployments/log-analytics.png)

Klicka på **uppdateringshantering**i ditt Automation-konto.

![Uppdateringshantering](media/migrate-oms-update-deployments/azure-automation.png)

I Azure Portal väljer du **Automation-konton** under **alla tjänster**. 

Under **hanterings verktyg**väljer du lämpligt Automation-konto och klickar på **uppdateringshantering**.

## <a name="recreate-existing-deployments"></a>Återskapa befintliga distributioner

Alla uppdaterings distributioner som skapas i OMS-portalen har en [Sparad sökning](../azure-monitor/platform/computer-groups.md) som också kallas en dator grupp, med samma namn som den uppdaterings distribution som finns. Den sparade sökningen innehåller en lista över datorer som har schemalagts i uppdaterings distributionen.

![Uppdateringshantering](media/migrate-oms-update-deployments/oms-deployment.png)

Följ dessa steg om du vill använda den befintliga sparade sökningen:

Om du vill skapa en ny uppdaterings distribution går du till Azure Portal, väljer det Automation-konto som används och klickar på **uppdateringshantering**. Klicka på **Schemalägg uppdaterings distribution**.

![Schemalägg uppdaterings distribution](media/migrate-oms-update-deployments/schedule-update-deployment.png)

Fönstret ny uppdaterings distribution öppnas. Ange värden för egenskaperna som beskrivs i följande tabell och klicka sedan på **skapa**:

För **datorer som ska uppdateras**väljer du den sparade sökning som används av den befintliga OMS-distributionen.

| Egenskap | Beskrivning |
| --- | --- |
|Name |Unikt namn som identifierar uppdateringsdistributionen. |
|Operativsystem| Välj **Linux** eller **Windows**.|
|Datorer som ska uppdateras |Välj en sparad sökning eller en importerad grupp, eller välj Dator i listrutan och välj enskilda datorer. Om du väljer **Datorer** visas beredskapen för datorn i kolumnen **Uppdatera agentberedskap**.</br> Information om de olika metoderna för att skapa datorgrupper i Azure Monitor-loggar finns i [datorgrupper i Azure Monitor-loggar](../azure-monitor/platform/computer-groups.md) |
|Klassificering av uppdateringar|Välj alla uppdaterings klassificeringar som du behöver. CentOS stöder inte detta.|
|Uppdateringar som ska undantas|Ange de uppdateringar som ska undantas. För Windows anger du KB-artikeln utan **KB** -prefixet. För Linux anger du paket namnet eller använder ett jokertecken.  |
|Schema inställningar|Välj tid för start och välj sedan **en gång** eller **återkommande** för upprepningen. | 
| Underhålls period |Antal minuter som har angetts för uppdateringar. Värdet kan inte vara mindre än 30 minuter eller mer än 6 timmar. |
| Starta om kontroll| Fastställer hur omstarter ska hanteras.</br>De tillgängliga alternativen är:</br>Starta om vid behov (standard)</br>Starta alltid om</br>Starta aldrig om</br>Endast omstart – uppdateringar installeras inte|

Klicka på **schemalagda uppdaterings distributioner** om du vill visa statusen för den nyligen skapade uppdaterings distributionen.

![ny uppdaterings distribution](media/migrate-oms-update-deployments/new-update-deployment.png)

Som tidigare nämnts kan de befintliga distributionerna tas bort från OMS-portalen när dina nya distributioner har kon figurer ATS via Azure Portal.

## <a name="next-steps"></a>Nästa steg

Mer information om Uppdateringshantering i Azure finns [uppdateringshantering](automation-update-management.md).
