---
title: Exportera Azure Resource Manager-mall med hjälp av Azure-portalen
description: Använd Azure-portalen för att exportera en Azure Resource Manager-mall från resurser i din prenumeration.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: tomfitz
ms.openlocfilehash: ea9499da3dac67635a48704f439f6592c6ed467e
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65515391"
---
# <a name="single-and-multi-resource-export-to-template-in-azure-portal"></a>Enkel och flera resurs export till mallen i Azure-portalen

Du kan exportera en mall från befintliga resurser för att hjälpa med att skapa Azure Resource Manager-mallar. Den exporterade mallen hjälper dig att förstå JSON-syntax och egenskaper som distribuerar dina resurser. Börja med den exporterade mallen för att automatisera framtida distributioner, och modifiera den efter ditt scenario.

Resource Manager kan du välja en eller flera resurser för att exportera till en mall. Du kan fokusera på exakt de resurser du behöver i mallen.

## <a name="choose-the-right-export-option"></a>Välj rätt Exportalternativet

Det finns två sätt att exportera en mall:

* **Exportera från resursgruppen eller resursen**. Det här alternativet skapar en ny mall från befintliga resurser. Den exporterade mallen är en ”ögonblicksbild” av det aktuella tillståndet för resursgruppen. Du kan exportera en hela resursgruppen eller specifika resurser i resursgruppen.

* **Exportera före distributionen eller från historiken över**. Det här alternativet hämtar en exakt kopia av en mall som används för distribution.

Beroende på vilket alternativ som du väljer har de exporterade mallarna olika egenskaper.

| Från resursgruppen eller resursen | Före distributionen eller från historiken |
| --------------------- | ----------------- |
| Mall för ögonblicksbild av resursens aktuella tillstånd. Den innehåller alla manuella ändringar efter distributionen. | Mallen visas endast för resurser vid tidpunkten för distributionen. Eventuella manuella ändringar som gjorts efter distribution inkluderas inte. |
| Du kan välja vilka resurser från en resursgrupp att exportera. | Alla resurser för en specifik distribution ingår. Du kan inte välja en delmängd av dessa resurser eller lägga till resurser som har lagts till på en annan tid. |
| Mallen innehåller alla egenskaper för resurser, inklusive vissa egenskaper som du normalt skulle ange under distributionen. Du kanske vill ta bort eller rensa upp de här egenskaperna innan du återanvänder mallen. | Mallen innehåller bara de egenskaper som behövs för att distribuera. Mallen är redo att användas. |
| Mallen innehålla förmodligen inte alla de parametrar du behöver för återanvändning. De flesta egenskapsvärden är hårdkodad i mallen. Du måste lägga till parametrar som ökar möjligheten att konfigurera resurser för att distribuera om mallen i andra miljöer. | Mallen innehåller parametrar som gör det enkelt att distribuera i olika miljöer. |

Exportera mallen från en resursgrupp eller resurs, när:

* Du behöver samla in ändringar till de resurser som har gjorts efter den ursprungliga distributionen.
* Du vill välja vilka resurser som ska exporteras.

Exportera mallen före distributionen eller från tidigare när:

* Vill du en mall för enkelt till återanvändning.
* Du behöver inte inkludera ändringar efter den ursprungliga distributionen.

## <a name="export-template-from-resource-group"></a>Exportera mallen från resursgruppen

Så här exporterar du en eller flera resurser från en resursgrupp:

1. Välj den resursgrupp som innehåller de resurser som du vill exportera.

1. Markera alla om du vill exportera alla resurser i resursgruppen och sedan **exportmallen**. Den **exportmallen** alternativet endast aktiveras när du har valt minst en resurs.

   ![Exportera alla resurser](./media/export-template-portal/select-all-resources.png)

1. Markera kryssrutorna bredvid de här resurserna för att välja specifika resurser för export. Välj **exportmallen**.

   ![Välj resurser som ska exporteras](./media/export-template-portal/select-resources.png)

1. Den exporterade mallen visas och är tillgänglig för hämtning.

   ![Visa mall](./media/export-template-portal/show-template.png)

## <a name="export-template-from-resource"></a>Exportera mallen från resurs

Så här exporterar du en resurs:

1. Välj den resursgrupp som innehåller den resurs du vill exportera.

1. Välj resursen att exportera.

   ![Välj resurs](./media/export-template-portal/select-link-resource.png)

1. För den resursen väljer **exportmallen** i den vänstra rutan.

   ![Exportera resource](./media/export-template-portal/export-single-resource.png)

1. Den exporterade mallen visas och är tillgänglig för hämtning. Mallen innehåller endast enskild resurs.

## <a name="export-template-before-deployment"></a>Exportera mall före distributionen

1. Välj den Azure-tjänst du vill distribuera.

1. Fyll i värden för den nya tjänsten.

1. När skicka verifiering, men innan du påbörjar distribueringen, Välj **ladda ned en mall för automatisering**.

   ![Ladda ned mall](./media/export-template-portal/download-before-deployment.png)

1. Mallen visas och är tillgänglig för hämtning.

   ![Visa mall](./media/export-template-portal/show-template-before-deployment.png)

## <a name="export-template-after-deployment"></a>Exportera mall efter distributionen

Du kan exportera den mall som användes för att distribuera befintliga resurser. Mallen som du får är exakt det som användes för distributionen.

1. Välj den resursgrupp som du vill exportera.

1. Klicka på länken under **distributioner**.

   ![Välj distributionshistoriken](./media/export-template-portal/select-deployment-history.png)

1. Välj en av distributionerna från distributionshistoriken.

   ![Välj distributionen](./media/export-template-portal/select-details.png)

1. Välj **mallen**. Mallen som används för den här distributionen visas och är tillgänglig för hämtning.

   ![Välj mall](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>Nästa steg

- Läs Azure Resource Manager i [översikt över Azure Resource Manager](./resource-group-overview.md).
- Läs Resource Manager-mallens syntax i [förstå strukturen och syntaxen för Azure Resource Manager-mallar](./resource-group-authoring-templates.md).
- Läs hur du utvecklar mallar i den [stegvisa självstudier](/azure/azure-resource-manager/).
- Mallscheman för Azure Resource Manager-finns [mallreferensen](/azure/templates/).