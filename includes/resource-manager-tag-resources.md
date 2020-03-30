---
title: ta med fil
description: ta med fil
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 03/19/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: a00291182059506aeab9cde965fa4cbd5177ecf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132220"
---
Om en användare inte har den åtkomst som krävs för att använda taggar kan du tilldela rollen **Taggdeltagare** till användaren. Mer information finns i [Självstudiekurs: Bevilja en användare åtkomst till Azure-resurser med RBAC och Azure-portalen](../articles/role-based-access-control/quickstart-assign-role-user-portal.md).

1. Om du vill visa taggarna för en resurs eller en resursgrupp letar du efter befintliga taggar i översikten. Om du inte tidigare har tillämpar taggar är listan tom.

   ![Visa taggar för resurs- eller resursgrupp](./media/resource-manager-tag-resources/view-tags.png)

1. Om du vill lägga till en tagg väljer du **Klicka här om du vill lägga till taggar**.

1. Ange ett namn och värde.

   ![Lägg till tagg](./media/resource-manager-tag-resources/add-tag.png)

1. Fortsätt att lägga till taggar efter behov. Välj **Spara** när du är klar.

   ![Spara taggar](./media/resource-manager-tag-resources/save-tags.png)

1. Taggarna visas nu i översikten.

   ![Visa taggar](./media/resource-manager-tag-resources/view-new-tags.png)

1. Om du vill lägga till eller ta bort en tagg markerar du **ändra**.

1. Om du vill ta bort en tagg markerar du papperskorgen. Välj sedan **Spara**.

   ![Ta bort tagg](./media/resource-manager-tag-resources/delete-tag.png)

Så här masstilldelar du taggar till flera resurser:

1. Markera kryssrutan för de resurser som du vill tilldela taggen i en lista över resurser. Välj sedan **Tilldela taggar**.

   ![Välj flera resurser](./media/resource-manager-tag-resources/select-multiple-resources.png)

1. Lägg till namn och värden. Välj **Spara** när du är klar.

   ![Välj tilldela](./media/resource-manager-tag-resources/select-assign.png)

Så här visar du alla resurser med en tagg:

1. Sök efter **taggar**på Azure-portalmenyn . Välj det bland de tillgängliga alternativen.

   ![Sök efter tagg](./media/resource-manager-tag-resources/find-tags-general.png)

1. Välj taggen för visning av resurser.

   ![Välj tagg](./media/resource-manager-tag-resources/select-tag.png)

1. Alla resurser med den taggen visas.

   ![Visa resurser efter tagg](./media/resource-manager-tag-resources/view-resources-by-tag.png)
