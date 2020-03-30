---
title: Exportera mall i Azure-portalen
description: Använd Azure-portalen för att exportera en Azure Resource Manager-mall från resurser i din prenumeration.
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 8cdba58a7a2ba998bac7fc0225ff957047cd69b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273740"
---
# <a name="single-and-multi-resource-export-to-a-template-in-azure-portal"></a>Export med en och flera resurser till en mall i Azure-portalen

Om du vill hjälpa till med att skapa Azure Resource Manager-mallar kan du exportera en mall från befintliga resurser. Den exporterade mallen hjälper dig att förstå JSON-syntaxen och egenskaperna som distribuerar dina resurser. Om du vill automatisera framtida distributioner börjar du med den exporterade mallen och ändrar den för ditt scenario.

Med Resurshanteraren kan du välja en eller flera resurser för export till en mall. Du kan fokusera på exakt de resurser du behöver i mallen.

Den här artikeln visar hur du exporterar mallar via portalen. Du kan också använda [Azure CLI,](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates) [Azure PowerShell](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)eller [REST API](/rest/api/resources/resourcegroups/exporttemplate).

## <a name="choose-the-right-export-option"></a>Välj rätt exportalternativ

Det finns två sätt att exportera en mall:

* **Exportera från resursgrupp eller resurs**. Det här alternativet genererar en ny mall från befintliga resurser. Den exporterade mallen är en "ögonblicksbild" av resursgruppens aktuella tillstånd. Du kan exportera en hel resursgrupp eller specifika resurser inom resursgruppen.

* **Exportera före distribution eller från historik**. Det här alternativet hämtar en exakt kopia av en mall som används för distribution.

Beroende på vilket alternativ du väljer har de exporterade mallarna olika egenskaper.

| Från resursgrupp eller resurs | Före distribution eller från historik |
| --------------------- | ----------------- |
| Mallen är ögonblicksbild av resursernas aktuella tillstånd. Den innehåller alla manuella ändringar som du har gjort efter distributionen. | Mallen visar endast resurstillstånd vid tidpunkten för distributionen. Alla manuella ändringar som du har gjort efter distributionen ingår inte. |
| Du kan välja vilka resurser från en resursgrupp som ska exporteras. | Alla resurser för en viss distribution ingår. Du kan inte välja en delmängd av dessa resurser eller lägga till resurser som har lagts till vid en annan tidpunkt. |
| Mallen innehåller alla egenskaper för resurserna, inklusive vissa egenskaper som du normalt inte anger under distributionen. Du kanske vill ta bort eller rensa dessa egenskaper innan du återanvänder mallen. | Mallen innehåller endast de egenskaper som behövs för distributionen. Mallen är klar att använda. |
| Mallen innehåller förmodligen inte alla parametrar som du behöver för återanvändning. De flesta egenskapsvärden är hårdkodade i mallen. Om du vill distribuera om mallen i andra miljöer måste du lägga till parametrar som ökar möjligheten att konfigurera resurserna.  Du kan avmarkera **Inkludera parametrar** så att du kan skapa egna parametrar. | Mallen innehåller parametrar som gör det enkelt att distribuera om i olika miljöer. |

Exportera mallen från en resursgrupp eller resurs när:

* Du måste samla in ändringar i de resurser som gjordes efter den ursprungliga distributionen.
* Du vill välja vilka resurser som ska exporteras.

Exportera mallen före distributionen eller från historiken, när:

* Du vill ha en enkel att återanvända mall.
* Du behöver inte inkludera ändringar som du har gjort efter den ursprungliga distributionen.

## <a name="limitations"></a>Begränsningar

När du exporterar från en resursgrupp eller resurs genereras den exporterade mallen från de [publicerade schemana](https://github.com/Azure/azure-resource-manager-schemas/tree/master/schemas) för varje resurstyp. Ibland har schemat inte den senaste versionen för en resurstyp. Kontrollera den exporterade mallen för att se till att den innehåller de egenskaper du behöver. Om det behövs redigerar du den exporterade mallen för att använda den API-version du behöver.

Funktionen för exportmall stöder inte export av Azure Data Factory-resurser. Mer information om hur du kan exportera datafabriksresurser finns i [Kopiera eller klona en datafabrik i Azure Data Factory](https://aka.ms/exportTemplateViaAdf).

Om du vill exportera resurser som skapats via den klassiska distributionsmodellen måste du [migrera dem till resurshanterarens distributionsmodell](https://aka.ms/migrateclassicresourcetoarm).

## <a name="export-template-from-a-resource-group"></a>Exportera mall från en resursgrupp

Så här exporterar du en eller flera resurser från en resursgrupp:

1. Markera den resursgrupp som innehåller de resurser som du vill exportera.

1. Markera en eller flera resurser genom att markera kryssrutorna.  Markera alla genom att markera kryssrutan till vänster om **Namnet**. Menyalternativet **Exportera mall** aktiveras bara när du har valt minst en resurs.

   ![Exportera alla resurser](./media/export-template-portal/select-all-resources.png)

    På skärmbilden är det bara lagringskontot som valts.
1. Välj **exportera mall**.

1. Den exporterade mallen visas och är tillgänglig för hämtning och distribution.

   ![Visa mall](./media/export-template-portal/show-template.png)

   **Inkludera parametrar** är markerat som standard.  När du väljer det här alternativet inkluderas alla mallparametrar när mallen genereras. Om du vill skapa egna parametrar växlar du den här kryssrutan så att den inte inkluderar dem.

## <a name="export-template-from-a-resource"></a>Exportera mall från en resurs

Så här exporterar du en resurs:

1. Markera resursgruppen som innehåller den resurs som du vill exportera.

1. Välj den resurs som du vill exportera för att öppna resursen.

1. För den **resursen** väljer du Exportera mall i den vänstra rutan.

   ![Exportera resurs](./media/export-template-portal/export-single-resource.png)

1. Den exporterade mallen visas och är tillgänglig för hämtning och distribution. Mallen innehåller bara den enda resursen. **Inkludera parametrar** är markerat som standard.  När du väljer det här alternativet inkluderas alla mallparametrar när mallen genereras. Om du vill skapa egna parametrar växlar du den här kryssrutan så att den inte inkluderar dem.

## <a name="export-template-before-deployment"></a>Exportera mall före distribution

1. Välj den Azure-tjänst som du vill distribuera.

1. Fyll i värdena för den nya tjänsten.

1. När du har passerat valideringen, men innan du startar distributionen väljer du **Hämta en mall för automatisering**.

   ![Ladda ned mall](./media/export-template-portal/download-before-deployment.png)

1. Mallen visas och är tillgänglig för hämtning och distribution.


## <a name="export-template-after-deployment"></a>Exportera mall efter distribution

Du kan exportera mallen som användes för att distribuera befintliga resurser. Mallen du får är exakt den som användes för distribution.

1. Markera den resursgrupp som du vill exportera.

1. Välj länken under **Distributioner**.

   ![Välj distributionshistorik](./media/export-template-portal/select-deployment-history.png)

1. Välj en av distributionerna från distributionshistoriken.

   ![Välj distribution](./media/export-template-portal/select-details.png)

1. Välj **mall**. Mallen som används för den här distributionen visas och är tillgänglig för hämtning.

   ![Välj mall](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du exporterar mallar med [Azure CLI,](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates) [Azure PowerShell](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)eller [REST API](/rest/api/resources/resourcegroups/exporttemplate).
- Information om hur du lär dig mallsyntaxen för Resurshanteraren finns [i Förstå strukturen och syntaxen för Azure Resource Manager-mallar](template-syntax.md).
- Mer information om hur du utvecklar mallar finns i [steg-för-steg-självstudier](/azure/azure-resource-manager/).
- Information om hur du visar mallscheman för Azure Resource Manager finns i [mallreferens](/azure/templates/).
