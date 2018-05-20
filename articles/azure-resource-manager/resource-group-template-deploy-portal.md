---
title: Använda Azure-portalen för att distribuera Azure-resurser | Microsoft Docs
description: Använd Azure-portalen och Azure Resource Manager för att distribuera dina resurser.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: tomfitz
ms.openlocfilehash: 7da04f5561615c2e05821d5ef52a3653cb08fda5
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Distribuera resurser med Resource Manager-mallar och Azure Portal

Det här avsnittet visar hur du använder den [Azure-portalen](https://portal.azure.com) med [Azure Resource Manager](resource-group-overview.md) att distribuera Azure-resurser. Läs om hur du hanterar dina resurser i [hantera Azure-resurser via portalen](resource-group-portal.md).

## <a name="create-resource-group"></a>Skapa resursgrupp

1. Om du vill skapa en tom resursgrupp **resursgrupper**.

   ![Välj resursgrupper](./media/resource-group-template-deploy-portal/select-resource-groups.png)

1. Välj under resursgrupper **Lägg till**.

   ![Lägg till resursgrupp](./media/resource-group-template-deploy-portal/add-resource-group.png)

1. Ge det ett namn och en plats och, om det behövs väljer du en prenumeration. Du måste ange en plats för resursgruppen eftersom resursgruppen lagras metadata om resurserna. Av kompatibilitetsskäl, kanske du vill ange var den metadata lagras. I allmänhet rekommenderar vi att du anger en plats där de flesta av dina resurser ska placeras. Med hjälp av samma plats kan förenkla din mall.

   ![gruppvärden](./media/resource-group-template-deploy-portal/set-group-properties.png)

   När du är klar med att ange egenskaperna väljer **skapa**.

1. Om du vill se din nya resursgrupp, Välj **uppdatera**.

   ![Uppdatera resursgrupper](./media/resource-group-template-deploy-portal/refresh-resource-groups.png)

## <a name="deploy-resources-from-marketplace"></a>Distribuera resurser från Marketplace

När du har skapat en resursgrupp kan du distribuera resurser till den från Marketplace. Marketplace innehåller fördefinierade lösningar för vanliga scenarier.

1. Om du vill starta en distribution väljer **skapar du en resurs**.

   ![Ny resurs](./media/resource-group-template-deploy-portal/new-resources.png)

1. Hitta typ av resurs som du vill distribuera.

   ![Välj resurstyp](./media/resource-group-template-deploy-portal/select-resource-type.png)

1. Om du inte ser den lösning som du vill distribuera, kan du söka Marketplace för den. Till exempel börja skriva om du vill söka efter en Wordpress-lösning, **Wordpress** och välj önskat alternativ.

   ![Sök på marketplace](./media/resource-group-template-deploy-portal/search-resource.png)

1. Beroende på vilken typ av markerade resursen har en samling relevanta egenskaper för att ange före distributionen. Du måste välja en resursgrupp för målet för alla typer. Följande bild visar hur du skapar en webbapp och distribuera den till den resursgrupp som du skapade.

   ![Skapa resursgrupp](./media/resource-group-template-deploy-portal/select-existing-group.png)

   Du kan också välja att skapa en resursgrupp när du distribuerar dina resurser. Välj **Skapa nytt** och namnge resursgruppen.

   ![Skapa ny resursgrupp](./media/resource-group-template-deploy-portal/select-new-group.png)

1. Börjar din distribution. Distributionen kan ta några minuter. När distributionen är klar visas ett meddelande.

   ![Visa meddelande](./media/resource-group-template-deploy-portal/view-notification.png)

1. När du har distribuerat dina resurser, du kan lägga till fler resurser till resursgruppen genom att välja **Lägg till**.

   ![Lägg till resurs](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Distribuera resurser från anpassad mall

Om du vill köra en distribution utan att använda någon av mallar i Marketplace, kan du skapa en anpassad mall som definierar infrastrukturen för lösningen. Läs om hur du skapar mallar i [förstå struktur och syntaxen för Azure Resource Manager-mallar](resource-group-authoring-templates.md).

1. Om du vill distribuera en anpassad mall via portalen, Välj **skapar du en resurs**, och Sök efter **malldistribution** tills du kan välja bland alternativ.

   ![Sök malldistribution](./media/resource-group-template-deploy-portal/search-template.png)

1. Välj **Skapa**.

   ![Välj Skapa](./media/resource-group-template-deploy-portal/show-template-option.png)

1. Du ser flera alternativ för att skapa en mall. Välj **skapa egna mallar i redigeraren**.

   ![Visa alternativ](./media/resource-group-template-deploy-portal/see-options.png)

1. Du har en tom mall som är tillgänglig för att anpassa.

   ![Skapa mallen](./media/resource-group-template-deploy-portal/blank-template.png)

1. Du kan redigera JSON-syntax manuellt eller välj en förskapad mall från den [mallen snabbstartsgalleriet](https://azure.microsoft.com/resources/templates/). För den här artikeln får du dock använda den **lägga till en resurs** alternativet.

   ![Redigera mall](./media/resource-group-template-deploy-portal/select-add-resource.png)

1. Välj **lagringskonto** och ange ett namn. När du är klar att tillhandahålla värden väljer **OK**.

   ![Välj lagringskonto](./media/resource-group-template-deploy-portal/add-storage-account.png)

1. Redigeraren för lägger automatiskt till JSON för resurstypen. Observera att det innehåller en parameter för att definiera typ av lagringskonto. Välj **Spara**.

   ![Visa mall](./media/resource-group-template-deploy-portal/show-json.png)

1. Nu har möjlighet att distribuera de resurser som definierats i mallen. Att distribuera, samtycker till villkoren och välj **inköp**.

   ![Distribuera mallen](./media/resource-group-template-deploy-portal/provide-custom-template-values.png)

## <a name="deploy-resources-from-a-template-saved-to-your-account"></a>Distribuera resurser från en mall som sparats i ditt konto

Portalen kan du spara en mall i Azure-konto och distribuera den senare. Mer information om hur du arbetar med dessa sparade mallar, [Kom igång med privata mallar på Azure portal](../marketplace-consumer/mytemplates-getstarted.md).

1. Om du vill hitta dina sparade mallar, Välj **fler tjänster**.

   ![Fler tjänster](./media/resource-group-template-deploy-portal/more-services.png)

1. Sök efter **mallar** och väljer det alternativet.

   ![Sök efter mallar](./media/resource-group-template-deploy-portal/find-templates.png)

1. Välj det du vill arbeta med i listan över mallar som sparas i ditt konto.

   ![sparade mallar](./media/resource-group-template-deploy-portal/saved-templates.png)

1. Välj **distribuera** distribuera sparade mallen.

   ![distribuera sparade mallen](./media/resource-group-template-deploy-portal/deploy-saved-template.png)

## <a name="next-steps"></a>Nästa steg
* Om du vill visa granskningsloggarna finns [granskningsåtgärder med Resource Manager](resource-group-audit.md).
* Om du vill felsöka distribution, se [visa distributionsåtgärder](resource-manager-deployment-operations.md).
* Om du vill hämta en mall från en distribution eller resursgruppen finns [exportera Azure Resource Manager-mall från befintliga resurser](resource-manager-export-template.md).
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](resource-manager-subscription-governance.md).
