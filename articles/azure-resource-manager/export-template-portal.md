---
title: Exportera mall i Azure Portal
description: Använd Azure Portal för att exportera en Azure Resource Manager mall från resurser i din prenumeration.
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 6e05a8bff27e56bc5942a7c16ab63921c489f44c
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149865"
---
# <a name="single-and-multi-resource-export-to-a-template-in-azure-portal"></a>Exportera en och flera resurser till en mall i Azure Portal

För att hjälpa till med att skapa Azure Resource Manager mallar kan du exportera en mall från befintliga resurser. Den exporterade mallen hjälper dig att förstå JSON-syntaxen och de egenskaper som distribuerar dina resurser. Om du vill automatisera framtida distributioner börjar du med den exporterade mallen och ändrar den för ditt scenario.

Med Resource Manager kan du välja en eller flera resurser för att exportera till en mall. Du kan fokusera på exakt de resurser som du behöver i mallen.

Den här artikeln visar hur du exporterar mallar via portalen. Du kan också använda [Azure CLI](manage-resource-groups-cli.md#export-resource-groups-to-templates), [Azure PowerShell](manage-resource-groups-powershell.md#export-resource-groups-to-templates)eller [REST API](/rest/api/resources/resourcegroups/exporttemplate).

## <a name="choose-the-right-export-option"></a>Välj rätt export alternativ

Det finns två sätt att exportera en mall:

* **Exportera från resurs grupp eller resurs**. Med det här alternativet skapas en ny mall från befintliga resurser. Den exporterade mallen är en "ögonblicks bild" av resurs gruppens aktuella tillstånd. Du kan exportera en hel resurs grupp eller vissa resurser inom den resurs gruppen.

* **Exportera före distribution eller från historik**. Med det här alternativet hämtas en exakt kopia av en mall som används för distribution.

De exporterade mallarna har olika kvalitet beroende på vilket alternativ du väljer.

| Från resurs grupp eller resurs | Före distribution eller från historik |
| --------------------- | ----------------- |
| Mallen är en ögonblicks bild av resursens aktuella tillstånd. Den innehåller alla manuella ändringar som du har gjort efter distributionen. | Mall visar endast tillstånd för resurser vid tidpunkten för distributionen. Eventuella manuella ändringar som du har gjort efter distributionen ingår inte. |
| Du kan välja vilka resurser från en resurs grupp som ska exporteras. | Alla resurser för en speciell distribution ingår. Du kan inte välja en delmängd av dessa resurser eller lägga till resurser som har lagts till vid en annan tidpunkt. |
| Mallen innehåller alla egenskaper för resurserna, inklusive vissa egenskaper som du normalt sett angav under distributionen. Du kanske vill ta bort eller rensa dessa egenskaper innan du återanvänder mallen. | Mallen innehåller bara de egenskaper som krävs för distributionen. Mallen är klar att användas. |
| Mallen innehåller antagligen inte alla parametrar som du behöver för åter användning. De flesta egenskaps värden är hårdkodade i mallen. Om du vill distribuera om mallen i andra miljöer måste du lägga till parametrar som ökar möjligheten att konfigurera resurserna. | Mallen innehåller parametrar som gör det enkelt att omdistribuera i olika miljöer. |

Exportera mallen från en resurs grupp eller resurs när:

* Du måste samla in ändringar av de resurser som gjorts efter den ursprungliga distributionen.
* Du vill välja vilka resurser som ska exporteras.

Exportera mallen före distribution eller från historiken när:

* Du vill ha en lätt att återanvända mall.
* Du behöver inte inkludera ändringar som du har gjort efter den ursprungliga distributionen.

## <a name="export-template-from-a-resource-group"></a>Exportera mall från en resurs grupp

Så här exporterar du en eller flera resurser från en resurs grupp:

1. Välj den resurs grupp som innehåller de resurser som du vill exportera.

1. Välj en eller flera resurser genom att markera kryss rutorna.  Markera kryss rutan till vänster om du vill markera **alla.** Meny alternativet **Exportera mall** aktive ras bara när du har valt minst en resurs.

   ![Exportera alla resurser](./media/export-template-portal/select-all-resources.png)

    På skärm bilden är bara lagrings kontot markerat.
1. Välj **Exportera mall**.

1. Den exporterade mallen visas och är tillgänglig för hämtning och distribution.

   ![Visa mall](./media/export-template-portal/show-template.png)

## <a name="export-template-from-a-resource"></a>Exportera mall från en resurs

Så här exporterar du en resurs:

1. Välj den resurs grupp som innehåller den resurs som du vill exportera.

1. Välj den resurs som du vill exportera för att öppna resursen.

1. För resursen väljer du **Exportera mall** i det vänstra fönstret.

   ![Exportera resurs](./media/export-template-portal/export-single-resource.png)

1. Den exporterade mallen visas och är tillgänglig för hämtning och distribution. Mallen innehåller bara den enskilda resursen.

## <a name="export-template-before-deployment"></a>Exportera mall före distribution

1. Välj den Azure-tjänst som du vill distribuera.

1. Fyll i värdena för den nya tjänsten.

1. När du har överfört verifieringen, men innan du påbörjar distributionen, väljer du **Ladda ned en mall för automatisering**.

   ![Ladda ned mall](./media/export-template-portal/download-before-deployment.png)

1. Mallen visas och är tillgänglig för hämtning och distribution.


## <a name="export-template-after-deployment"></a>Exportera mall efter distribution

Du kan exportera den mall som användes för att distribuera befintliga resurser. Mallen som du hämtar är exakt den som användes för distributionen.

1. Välj den resurs grupp som du vill exportera.

1. Välj länken under **distributioner**.

   ![Välj distributions historik](./media/export-template-portal/select-deployment-history.png)

1. Välj en av distributionerna från distributions historiken.

   ![Välj distribution](./media/export-template-portal/select-details.png)

1. Välj **mall**. Mallen som används för den här distributionen visas och är tillgänglig för hämtning.

   ![Välj mall](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du exporterar mallar med [Azure CLI](manage-resource-groups-cli.md#export-resource-groups-to-templates), [Azure PowerShell](manage-resource-groups-powershell.md#export-resource-groups-to-templates)eller [REST API](/rest/api/resources/resourcegroups/exporttemplate).
- Information om syntaxen för Resource Manager-mallar finns i [förstå strukturen och syntaxen för Azure Resource Manager mallar](./resource-group-authoring-templates.md).
- Information om hur du utvecklar mallar finns i de [stegvisa självstudierna](/azure/azure-resource-manager/).
- Om du vill visa scheman för Azure Resource Manager mallar, se [referens för mallar](/azure/templates/).
