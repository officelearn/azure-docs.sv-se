---
title: Använd dynamiska grupper med Azure Automation Uppdateringshantering
description: Den här artikeln beskriver hur dynamiska grupper fungerar med Azure Automation Uppdateringshantering.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 29a72eb1fe7b8be18cd2160fc63160e408378585
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617447"
---
# <a name="use-dynamic-groups-with-update-management"></a>Använd dynamiska grupper med Uppdateringshantering

Med Uppdateringshantering kan du rikta en dynamisk grupp med virtuella Azure-datorer eller icke-Azure-datorer för uppdaterings distributioner. De här grupperna, som definieras av frågor, utvärderas vid distributions tiden så att du inte behöver redigera distributionen för att lägga till datorer.

## <a name="azure-machines"></a>Azure-datorer

Dynamiska grupper fungerar inte med klassiska virtuella datorer. När du definierar din fråga kan följande objekt användas tillsammans för att fylla i en dynamisk grupp:

* Prenumeration
* Resursgrupper
* Platser
* Taggar

![Välj grupper](./media/automation-update-management/select-groups.png)

Klicka på **Förhandsgranska**om du vill förhandsgranska resultatet av en dynamisk grupp. För hands versionen visar grupp medlemskapet vid aktuell tidpunkt. I exemplet ska vi söka efter datorer som har taggen `Role` för gruppen **BackendServer**. Om fler datorer har lagt till den här taggen läggs de till i alla framtida distributioner för den gruppen.

![för hands versions grupper](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Datorer som inte är Azure-datorer

För datorer som inte är Azure-datorer används sparade sökningar, även kallade dator grupper, för att skapa den dynamiska gruppen. Information om hur du skapar en sparad sökning finns i [skapa en dator grupp](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). När gruppen har skapats kan du välja den i listan över sparade sökningar. Klicka på **Förhandsgranska** för att förhandsgranska datorerna i den sparade sökningen vid detta tillfälle.

![Välj grupper](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Nästa steg

När du har skapat en dynamisk grupp kan du [skapa en uppdaterings distribution](automation-tutorial-update-management.md).
