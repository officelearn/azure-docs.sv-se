---
title: Använda dynamiska grupper med Azure Automation Update Management
description: I den här artikeln beskrivs hur dynamiska grupper fungerar med Azure Automation Update Management.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 29a72eb1fe7b8be18cd2160fc63160e408378585
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617447"
---
# <a name="use-dynamic-groups-with-update-management"></a>Använd dynamiska grupper med Uppdateringshantering

Med Uppdateringshantering kan du rikta in dig på en dynamisk grupp av virtuella Azure- eller icke-Azure-datorer för uppdateringsdistributioner. Dessa grupper, som definieras av frågor, utvärderas vid distributionen så att du inte behöver redigera distributionen för att lägga till datorer.

## <a name="azure-machines"></a>Azure-datorer

Dynamiska grupper fungerar inte med klassiska virtuella datorer. När du definierar frågan kan följande objekt användas tillsammans för att fylla i en dynamisk grupp:

* Prenumeration
* Resursgrupper
* Platser
* Taggar

![Välj grupper](./media/automation-update-management/select-groups.png)

Om du vill förhandsgranska resultatet av en dynamisk grupp klickar du på **Förhandsgranska**. Förhandsgranskningen visar gruppmedlemskapet vid den aktuella tidpunkten. I exemplet söker vi efter datorer som `Role` har taggen för gruppen **BackendServer**. Om fler datorer har lagt till den här taggen läggs de till i alla framtida distributioner mot den gruppen.

![förhandsgranska grupper](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Datorer som inte är Azure

För datorer som inte är Azure används sparade sökningar, även kallade datorgrupper, för att skapa den dynamiska gruppen. Mer information om hur du skapar en sparad sökning finns i [Skapa en datorgrupp](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). När gruppen har skapats kan du välja den i listan över sparade sökningar. Klicka på **Förhandsgranska** om du vill förhandsgranska datorerna i den sparade sökningen vid den tidpunkten.

![Välj grupper](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Nästa steg

När du har skapat en dynamisk grupp kan du [skapa en uppdateringsdistribution](automation-tutorial-update-management.md).
