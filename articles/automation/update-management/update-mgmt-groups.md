---
title: Använd dynamiska grupper med Azure Automation Uppdateringshantering
description: Den här artikeln beskriver hur du använder dynamiska grupper med Azure Automation Uppdateringshantering.
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: 61339a22fc3823325ff5356112c2df8a800010a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90985200"
---
# <a name="use-dynamic-groups-with-update-management"></a>Använd dynamiska grupper med Uppdateringshantering

Med Uppdateringshantering kan du rikta en dynamisk grupp med virtuella Azure-datorer eller icke-Azure-datorer för uppdaterings distributioner. Genom att använda en dynamisk grupp kan du behöva redigera distributionen för att uppdatera datorer.

> [!NOTE]
> Dynamiska grupper fungerar inte med klassiska virtuella datorer.

Du kan definiera dynamiska grupper för Azure-datorer eller icke-Azure-datorer från **uppdaterings hantering** i Azure Portal. Se [Hantera uppdateringar för virtuella datorer](update-mgmt-manage-updates-for-vm.md).

En dynamisk grupp definieras av en fråga som Azure Automation utvärderas vid distributions tiden. Även om en dynamisk grupp fråga hämtar ett stort antal datorer, kan Azure Automation bearbeta högst 1000 datorer i taget. Läs mer i [Azure-prenumeration och tjänstbegränsningar, kvoter och krav](../../azure-resource-manager/management/azure-subscription-service-limits.md#update-management).

> [!NOTE]
> Om du förväntar dig att uppdatera fler än 1000 datorer, rekommenderar vi att du delar upp uppdateringarna mellan flera uppdaterings scheman. 

## <a name="define-dynamic-groups-for-azure-machines"></a>Definiera dynamiska grupper för Azure-datorer

När du definierar en dynamisk grupp fråga för Azure-datorer kan du använda följande objekt för att fylla i den dynamiska gruppen:

* Prenumeration
* Resursgrupper
* Platser
* Taggar

![Välj grupper](./media/update-mgmt-groups/select-groups.png)

Klicka på **Förhandsgranska**om du vill förhandsgranska resultatet av din dynamiska grupp fråga. För hands versionen visar grupp medlemskapet vid aktuell tidpunkt. I exemplet ska vi söka efter datorer som har taggen `Role` för gruppen **BackendServer**. Om fler datorer har lagt till den här taggen läggs de till i alla framtida distributioner för den gruppen.

![för hands versions grupper](./media/update-mgmt-groups/preview-groups.png)

## <a name="define-dynamic-groups-for-non-azure-machines"></a>Definiera dynamiska grupper för datorer som inte är Azure-datorer

En dynamisk grupp för datorer som inte är Azure-datorer använder sparade sökningar, även kallade dator grupper. Information om hur du skapar en sparad sökning finns i [skapa en dator grupp](../../azure-monitor/platform/computer-groups.md#creating-a-computer-group). När din sparade sökning har skapats kan du välja den i listan över sparade sökningar i **uppdaterings hantering** i Azure Portal. Klicka på **Förhandsgranska** för att förhandsgranska datorerna i den sparade sökningen.

![Skärm bild som visar sidan Välj grupper för icke-Azure (för hands version) och förhands gransknings fönstret på höger sida.](./media/update-mgmt-groups/select-groups-2.png)

## <a name="next-steps"></a>Nästa steg

Du kan [fråga Azure Monitor loggar](update-mgmt-query-logs.md) för att analysera uppdaterings utvärderingar, distributioner och andra relaterade hanterings uppgifter. Den innehåller fördefinierade frågor som hjälper dig att komma igång.
