---
title: Använd dynamiska grupper med Azure Automation Uppdateringshantering
description: Den här artikeln beskriver hur du använder dynamiska grupper med Azure Automation Uppdateringshantering.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 792f69c900853d76dc260ad1de2e635e47bb2b4a
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744390"
---
# <a name="use-dynamic-groups-with-update-management"></a>Använd dynamiska grupper med Uppdateringshantering

Med Uppdateringshantering kan du rikta en dynamisk grupp med virtuella Azure-datorer eller icke-Azure-datorer för uppdaterings distributioner. Genom att använda en dynamisk grupp kan du behöva redigera distributionen för att uppdatera datorer.

> [!NOTE]
> Dynamiska grupper fungerar inte med klassiska virtuella datorer.

Du kan definiera dynamiska grupper för Azure-datorer eller icke-Azure-datorer från **uppdaterings hantering** i Azure Portal. Se [Hantera uppdateringar för flera virtuella Azure-datorer](manage-update-multi.md).

En dynamisk grupp definieras av en fråga som Azure Automation utvärderas vid distributions tiden. Även om en dynamisk grupp fråga hämtar ett stort antal datorer, kan Azure Automation bearbeta högst 1000 datorer i taget. Läs mer i [Azure-prenumeration och tjänstbegränsningar, kvoter och krav](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#update-management). 

> [!NOTE]
> Om du förväntar dig att uppdatera fler än 1000 datorer, rekommenderar vi att du delar upp uppdateringarna mellan flera uppdaterings scheman. 

## <a name="define-dynamic-groups-for-azure-machines"></a>Definiera dynamiska grupper för Azure-datorer

När du definierar en dynamisk grupp fråga för Azure-datorer kan du använda följande objekt för att fylla i den dynamiska gruppen:

* Prenumeration
* Resursgrupper
* Platser
* Taggar

![Välj grupper](./media/automation-update-management/select-groups.png)

Klicka på **Förhandsgranska**om du vill förhandsgranska resultatet av din dynamiska grupp fråga. För hands versionen visar grupp medlemskapet vid aktuell tidpunkt. I exemplet ska vi söka efter datorer som har taggen `Role` för gruppen **BackendServer**. Om fler datorer har lagt till den här taggen läggs de till i alla framtida distributioner för den gruppen.

![för hands versions grupper](./media/automation-update-management/preview-groups.png)

## <a name="define-dynamic-groups-for-non-azure-machines"></a>Definiera dynamiska grupper för datorer som inte är Azure-datorer

En dynamisk grupp för datorer som inte är Azure-datorer använder sparade sökningar, även kallade dator grupper. Information om hur du skapar en sparad sökning finns i [skapa en dator grupp](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). När din sparade sökning har skapats kan du välja den i listan över sparade sökningar i **uppdaterings hantering** i Azure Portal. Klicka på **Förhandsgranska** för att förhandsgranska datorerna i den sparade sökningen.

![Välj grupper](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Nästa steg

[Hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer](automation-tutorial-update-management.md)
