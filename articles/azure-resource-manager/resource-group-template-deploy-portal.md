---
title: Distribuera Azure-resurser med hjälp av Azure portal | Microsoft Docs
description: Använd Azure-portalen och Azure Resource Manager för att distribuera dina resurser.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: tfitzmac
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: tomfitz
ms.openlocfilehash: c16d584f17aa2c209c9c0ec94d35f6fe78ba1907
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494099"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Distribuera resurser med Resource Manager-mallar och Azure Portal

Den här artikeln visar hur du använder den [Azure-portalen](https://portal.azure.com) med [Azure Resource Manager](resource-group-overview.md) att distribuera dina Azure-resurser. Läs om hur du hanterar dina resurser i [hantera Azure-resurser via portalen](resource-group-portal.md).

## <a name="create-resource-group"></a>Skapa resursgrupp

1. Om du vill skapa en tom resursgrupp, Välj **resursgrupper**.

   ![Välj resursgrupper](./media/resource-group-template-deploy-portal/select-resource-groups.png)

1. Välj under resursgrupper, **Lägg till**.

   ![Lägg till resursgrupp](./media/resource-group-template-deploy-portal/add-resource-group.png)

1. Ge den ett namn och plats, och om det behövs väljer du en prenumeration. Du måste ange en plats för resursgruppen eftersom resursgruppen lagrar metadata om resurserna. Av kompatibilitetsskäl, kanske du vill ange var metadatan ska lagras. I allmänhet rekommenderar vi att du anger en plats där de flesta av dina resurser ska placeras. Med hjälp av samma plats kan förenkla din mall.

   ![Ange gruppvärden](./media/resource-group-template-deploy-portal/set-group-properties.png)

   När du är klar med att ange egenskaperna väljer **skapa**.

1. Om du vill se din nya resursgrupp **uppdatera**.

   ![Uppdatera resursgrupper](./media/resource-group-template-deploy-portal/refresh-resource-groups.png)

## <a name="deploy-resources-from-marketplace"></a>Distribuera resurser från Marketplace

När du har skapat en resursgrupp kan du distribuera resurser till det från Marketplace. Marketplace innehåller fördefinierade lösningar för vanliga scenarier.

1. Om du vill starta en distribution väljer **skapa en resurs**.

   ![Ny resurs](./media/resource-group-template-deploy-portal/new-resources.png)

1. Hitta typ av resurs som du vill distribuera.

   ![Välj resurstyp](./media/resource-group-template-deploy-portal/select-resource-type.png)

1. Om du inte ser viss lösning som du vill distribuera, kan du söka på Marketplace efter den. Till exempel börja skriva om du vill hitta en Wordpress-lösning, **Wordpress** och välj önskat alternativ.

   ![Sök på marketplace](./media/resource-group-template-deploy-portal/search-resource.png)

1. Beroende på vilken typ av markerade resursen har du en samling relevanta egenskaper att ställa in före distributionen. Du måste välja en mål-resursgrupp för alla typer. Följande bild visar hur du skapar en webbapp och distribuera den till den resursgrupp du skapade.

   ![Skapa resursgrupp](./media/resource-group-template-deploy-portal/select-existing-group.png)

   Du kan också välja att skapa en resursgrupp när du distribuerar dina resurser. Välj **Skapa nytt** och ge resursgruppen ett namn.

   ![Skapa ny resursgrupp](./media/resource-group-template-deploy-portal/select-new-group.png)

1. Distributionen börjar. Distributionen kan ta några minuter. När distributionen är klar visas ett meddelande.

   ![Visa meddelande](./media/resource-group-template-deploy-portal/view-notification.png)

1. När du har distribuerat dina resurser, du kan lägga till fler resurser till resursgruppen genom att välja **Lägg till**.

   ![Lägg till resurs](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Distribuera resurser från anpassad mall

Om du vill köra en distribution, men inte använda någon av mallarna i Marketplace, kan du skapa en anpassad mall som definierar infrastrukturen för lösningen. Läs om hur du skapar mallar i [förstå strukturen och syntaxen för Azure Resource Manager-mallar](resource-group-authoring-templates.md).

> [!NOTE]
> Portalgränssnittet stöder inte refererar till en [från ett Key Vault](resource-manager-keyvault-parameter.md). Använd i stället [PowerShell](resource-group-template-deploy.md) eller [Azure CLI](resource-group-template-deploy-cli.md) att distribuera mallen lokalt eller från en extern URI.

1. Om du vill distribuera en anpassad mall via portalen, Välj **skapa en resurs**, och Sök efter **Malldistributionen** tills du kan välja bland alternativ.

   ![Sök malldistribution](./media/resource-group-template-deploy-portal/search-template.png)

1. Välj **Skapa**.

   ![Välj Skapa](./media/resource-group-template-deploy-portal/show-template-option.png)

1. Du ser flera alternativ för att skapa en mall. Välj **skapa din egen mall i redigeringsprogrammet**.

   ![Visa alternativ](./media/resource-group-template-deploy-portal/see-options.png)

1. Du har en tom mall som är tillgänglig för att anpassa.

   ![Skapa mallen](./media/resource-group-template-deploy-portal/blank-template.png)

1. Du kan redigera JSON-syntaxen manuellt eller välj en redan skapad mall från den [Snabbstart mallgalleriet](https://azure.microsoft.com/resources/templates/). För den här artikeln får du dock använda den **Lägg till resurs** alternativet.

   ![Redigera mall](./media/resource-group-template-deploy-portal/select-add-resource.png)

1. Välj **lagringskonto** och ange ett namn. När du är klar och ange värden väljer **OK**.

   ![Välj lagringskonto](./media/resource-group-template-deploy-portal/add-storage-account.png)

1. Redigeraren lägger automatiskt till JSON för resurstypen. Observera att den innehåller en parameter för att definiera typ av storage-konto. Välj **Spara**.

   ![Visa mall](./media/resource-group-template-deploy-portal/show-json.png)

1. Nu har möjlighet att distribuera de resurser som definierats i mallen. Att distribuera, godkänner du villkoren och markera **köp**.

   ![Distribuera mallen](./media/resource-group-template-deploy-portal/provide-custom-template-values.png)

## <a name="deploy-resources-from-a-template-saved-to-your-account"></a>Distribuera resurser från en mall som sparats till ditt konto

På portalen kan du spara en mall på Azure-kontot och distribuera om den senare. Mer information om mallar finns i [skapa och distribuera din första Azure Resource Manager-mall](resource-manager-create-first-template.md).

1. Om du vill hitta din sparade mallar, Välj **fler tjänster**.

   ![Fler tjänster](./media/resource-group-template-deploy-portal/more-services.png)

1. Sök efter **mallar** och väljer det alternativet.

   ![Sök efter mallar](./media/resource-group-template-deploy-portal/find-templates.png)

1. Välj den som du vill arbeta med i listan med mallar som sparas i ditt konto.

   ![Sparade mallar](./media/resource-group-template-deploy-portal/saved-templates.png)

1. Välj **distribuera** att distribuera om den här sparade mallen.

   ![Distribuera sparade mallen](./media/resource-group-template-deploy-portal/deploy-saved-template.png)

## <a name="next-steps"></a>Nästa steg
* Om du vill visa granskningsloggar, se [granskningsåtgärder med Resource Manager](resource-group-audit.md).
* Felsökning av vanliga fel beskrivs [visa distributionsåtgärder](resource-manager-deployment-operations.md).
* Om du vill hämta en mall från en distribution eller en resursgrupp, se [exportera Azure Resource Manager-mall från befintliga resurser](resource-manager-export-template.md).
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](/azure/architecture/cloud-adoption-guide/subscription-governance).
