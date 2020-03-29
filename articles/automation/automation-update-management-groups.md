---
title: Använda dynamiska grupper med Azure Update Management
description: I den här artikeln beskrivs hur dynamiska grupper fungerar med Azure Automation Update Management.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 678b3f361e4456a2c482896f7d7dc20d530b917b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75420400"
---
# <a name="use-dynamic-groups-with-update-management"></a>Använd dynamiska grupper med Uppdateringshantering

Uppdateringshantering ger dig möjlighet att rikta in sig på en dynamisk grupp av virtuella Azure- eller icke-Azure-datorer för uppdateringsdistributioner. Dessa grupper utvärderas vid distributionen så att du inte behöver redigera distributionen för att lägga till datorer.

## <a name="azure-machines"></a>Azure-datorer

Dessa grupper definieras av en fråga, när en uppdateringsdistribution börjar utvärderas medlemmarna i den gruppen. Dynamiska grupper fungerar inte med klassiska virtuella datorer. När du definierar frågan kan följande objekt användas tillsammans för att fylla i den dynamiska gruppen:

* Prenumeration
* Resursgrupper
* Platser
* Taggar

![Välj grupper](./media/automation-update-management/select-groups.png)

Om du vill förhandsgranska resultatet av en dynamisk grupp klickar du på knappen **Förhandsgranska.** Den här förhandsversionen visar gruppmedlemskapet vid den tidpunkten, i **Role** det här exemplet söker vi efter datorer med taggrollen är lika med **BackendServer**. Om fler datorer har lagt till den här taggen läggs de till i alla framtida distributioner mot den gruppen.

![förhandsgranska grupper](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Datorer som inte är Azure

För datorer som inte är Azure används sparade sökningar även i datorgrupper för att skapa den dynamiska gruppen. Mer information om hur du skapar en sparad sökning finns i [Skapa en datorgrupp](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). När din grupp har skapats kan du välja den i listan över sparade sökningar. Klicka på **Förhandsgranska** om du vill förhandsgranska datorerna i den sparade sökningen vid den tidpunkten.

![Välj grupper](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Nästa steg

När du har skapat en dynamisk grupp kan du [skapa en uppdateringsdistribution](automation-tutorial-update-management.md)
