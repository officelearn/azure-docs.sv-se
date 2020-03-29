---
title: Migrera dina OMS-uppdateringsdistributioner till Azure
description: I den här artikeln beskrivs hur du migrerar dina befintliga OMS-uppdateringsdistributioner till Azure
services: automation
ms.subservice: update-management
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: 2660e4a348d2ffd71f912ff80c36a5a9a3c9fe88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75417784"
---
# <a name="migrate-your-oms-update-deployments-to-azure"></a>Migrera dina OMS-uppdateringsdistributioner till Azure

Oms-portalen (Operations Management Suite) är [inaktuell.](../azure-monitor/platform/oms-portal-transition.md) Alla funktioner som var tillgängliga i OMS-portalen för uppdateringshantering är tillgängliga i Azure-portalen. Den här artikeln innehåller den information du behöver för att migrera till Azure-portalen.

## <a name="key-information"></a>Viktig information

* Befintliga distributioner fortsätter att fungera. När du har återskapat distributionen i Azure kan du ta bort din gamla distribution från OMS.
* Alla befintliga funktioner som du hade i OMS är tillgängliga i Azure, om du vill veta mer om uppdateringshantering, se [Översikt över uppdateringshantering](automation-update-management.md).

## <a name="access-the-azure-portal"></a>Åtkomst till Azure-portalen

Klicka på Öppna i **Azure**på din OMS-arbetsyta . Detta navigerar till log analytics arbetsyta som OMS används.

![Öppna i Azure - OMS-portal](media/migrate-oms-update-deployments/link-to-azure-portal.png)

Klicka på **Automation-konto** i Azure-portalen

![Azure Monitor-loggar](media/migrate-oms-update-deployments/log-analytics.png)

Öppna **uppdateringshantering** i ditt Automation-konto.

![Uppdateringshantering](media/migrate-oms-update-deployments/azure-automation.png)

I framtiden kan du gå direkt till Azure-portalen under **Alla tjänster,** välj **Automation-konton** under **Hanteringsverktyg,** välja lämpligt Automation-konto och klicka på **Uppdatera hantering**.

## <a name="recreate-existing-deployments"></a>Återskapa befintliga distributioner

Alla uppdateringsdistributioner som skapas i OMS-portalen har en [sparad sökning](../azure-monitor/platform/computer-groups.md) som också kallas en datorgrupp, med samma namn som den uppdateringsdistribution som finns. Den sparade sökningen innehåller en lista över datorer som har schemalagts i uppdateringsdistributionen.

![Uppdateringshantering](media/migrate-oms-update-deployments/oms-deployment.png)

Så här använder du den här sparade sökningen:

Om du vill skapa en ny uppdateringsdistribution går du till Azure-portalen, väljer det Automation-konto som används och klickar på **Uppdatera hantering**. Klicka på **Schemalägg uppdateringsdistribution**.

![Schemalägg distribution av uppdateringar](media/migrate-oms-update-deployments/schedule-update-deployment.png)

Fönstret **Ny uppdateringsdistribution** öppnas. Ange värden för de egenskaper som beskrivs i följande tabell och klicka sedan på **Skapa:**

För datorer som ska uppdateras väljer du den sparade sökning som används av den befintliga OMS-distributionen.

| Egenskap | Beskrivning |
| --- | --- |
|Namn |Unikt namn som identifierar uppdateringsdistributionen. |
|Operativsystem| Välj **Linux** eller **Windows**.|
|Maskiner för att uppdatera |Välj en sparad sökning eller en importerad grupp, eller välj Dator i listrutan och välj enskilda datorer. Om du väljer **Datorer** visas beredskapen för datorn i kolumnen **Uppdatera agentberedskap**.</br> Information om de olika metoderna för att skapa datorgrupper i Azure Monitor-loggar finns i [datorgrupper i Azure Monitor-loggar](../azure-monitor/platform/computer-groups.md) |
|Klassificering av uppdateringar|Markera alla uppdateringsklassificeringar som du behöver. CentOS stöder inte detta ur lådan.|
|Uppdateringar för att utesluta|Ange uppdateringarna som ska uteslutas. För Windows anger du KB-artikeln utan **KB-prefixet.** För Linux anger du paketnamnet eller använder ett jokertecken.  |
|Schemalägg inställningar|Välj den tid du ska börja och välj sedan **En gång** eller **Återkommande** för upprepningen. | 
| Fönstret Underhåll |Antal minuter som ställts in för uppdateringar. Värdet får inte vara mindre än 30 minuter eller mer än 6 timmar. |
| Omstartskontroll| Bestämmer hur omstarter ska hanteras.</br>De tillgängliga alternativen är:</br>Starta om vid behov (standard)</br>Starta alltid om</br>Starta aldrig om</br>Endast omstart – uppdateringar installeras inte|

Klicka på **Schemalagda uppdateringsdistributioner** om du vill visa status för den nyligen skapade uppdateringsdistributionen.

![ny uppdateringsdistribution](media/migrate-oms-update-deployments/new-update-deployment.png)

Som tidigare nämnts kan de befintliga distributionerna tas bort från OMS-portalen när de nya distributionerna har konfigurerats via Azure-portalen.

## <a name="next-steps"></a>Nästa steg

Mer information om uppdateringshantering i Azure finns i [Uppdatera hantering](automation-update-management.md)
