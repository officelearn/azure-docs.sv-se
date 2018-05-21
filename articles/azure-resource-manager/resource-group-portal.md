---
title: Använda Azure-portalen för att hantera Azure-resurser | Microsoft Docs
description: Använd Azure-portalen och Azure Resource Manager för att hantera dina resurser. Visar hur du arbetar med instrumentpaneler för att övervaka resurser.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 0725bbf2-5913-4c07-af6e-24e11d957fbc
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2016
ms.author: tomfitz
ms.openlocfilehash: fda195c03c589361b8a798166b0ae4d93611bcd0
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="manage-azure-resources-through-portal"></a>Hantera Azure-resurser via portalen

Den här artikeln visar hur du använder den [Azure-portalen](https://portal.azure.com) med [Azure Resource Manager](resource-group-overview.md) hantera Azure-resurser. Läs om hur du distribuerar resurser via portalen i [distribuera resurser med Resource Manager-mallar och Azure-portalen](resource-group-template-deploy-portal.md).

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="manage-resource-groups"></a>Hantera resursgrupper

En resursgrupp är en behållare som innehåller relaterade resurser för en Azure-lösning. Resursgruppen kan innehålla alla resurser för lösningen, eller endast de resurser som du vill hantera som en grupp. Du bestämmer hur du vill allokera resurser till resursgrupper baserat på vad som är lämpligast för din organisation. I allmänhet ska du lägga till resurser som delar samma livscykel i samma resursgrupp så att du enkelt kan distribuera, uppdatera och ta bort dem som en grupp. 

Resursgruppen lagrar metadata om resurserna. När du anger en plats för resursgruppen anger du därför var metadatan ska lagras. På grund av regelefterlevnadsskäl kan du behöva säkerställa att din data lagras inom en viss region.

1. Om du vill se alla resursgrupper i din prenumeration, Välj **resursgrupper**.
   
    ![Bläddra resursgrupper](./media/resource-group-portal/browse-groups.png)
2. Om du vill skapa en tom resursgrupp **Lägg till**.
   
    ![Lägg till resursgrupp](./media/resource-group-portal/add-resource-group.png)
3. Ange ett namn och plats för den nya resursgruppen. Välj **Skapa**.
   
    ![Skapa resursgrupp](./media/resource-group-portal/create-empty-group.png)
4. Du kan behöva välja **uppdatera** att se den nyligen skapade resursgruppen.
   
    ![Uppdatera resursgruppen.](./media/resource-group-portal/refresh-resource-groups.png)
5. Om du vill anpassa den information som visas för dina resursgrupper, Välj **kolumner**.
   
    ![Anpassa kolumner](./media/resource-group-portal/select-columns.png)
6. Markera kolumnerna som du vill lägga till och välj sedan **uppdatering**.
   
    ![lägga till kolumner](./media/resource-group-portal/add-columns.png)
7. Läs om hur du distribuerar resurser till din nya resursgrupp i [distribuera resurser med Resource Manager-mallar och Azure-portalen](resource-group-template-deploy-portal.md).
8. Du kan fästa resursgruppen på instrumentpanelen för snabb åtkomst till en resursgrupp.
   
    ![resursgruppen för PIN-kod](./media/resource-group-portal/pin-group.png)
9. Instrumentpanelen visar resursgruppen och dess resurser. Du kan välja antingen resursgrupper eller någon av dess resurser att navigera till objektet.
   
    ![resursgruppen för PIN-kod](./media/resource-group-portal/show-resource-group-dashboard.png)

## <a name="tag-resources"></a>Taggen resurser
Du kan lägga till taggar till resursgrupper och resurser och organisera dem logiskt dina tillgångar. Information om hur du arbetar med taggar finns [med taggar för att organisera dina Azure-resurser](resource-group-using-tags.md).

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="monitor-resources"></a>Övervaka resurser
När du väljer en resurs anger portalen standard diagram och tabeller för att övervaka den resurstypen.

1. Välj en resurs och ett meddelande i **övervakning** avsnitt. Den omfattar diagram som är relevanta för resurstypen. Följande bild visar standardinställningar för övervakning av data för ett lagringskonto.
   
    ![Visa övervakning](./media/resource-group-portal/show-monitoring.png)
2. Du kan fästa ett avsnitt på instrumentpanelen genom att välja ellips (...) ovanför avsnittet. Du kan också anpassa storleken avsnittet eller ta bort den helt. Följande bild visar hur fästa, anpassa eller ta bort avsnittet CPU och minne.
   
    ![avsnittet för PIN-kod](./media/resource-group-portal/pin-cpu-section.png)
3. Efter fästning avsnittet till instrumentpanelen visas en sammanfattning på instrumentpanelen. Och välja det tar dig direkt till mer information om data.
   
    ![visa instrumentpanelen](./media/resource-group-portal/view-startboard.png)
4. För att helt anpassa de data som du övervakar via portalen, navigera till din standardinstrumentpanel och markera **ny instrumentpanel**.
   
    ![instrumentpanel](./media/resource-group-portal/dashboard.png)
5. Namnge din nya instrumentpanel och dra paneler till instrumentpanelen. Panelerna filtreras efter de olika alternativ.
   
    ![instrumentpanel](./media/resource-group-portal/create-dashboard.png)
   
     Läs om hur du arbetar med instrumentpaneler i [skapa och dela instrumentpaneler i Azure portal](../azure-portal/azure-portal-dashboards.md).

## <a name="manage-resources"></a>Hantera resurser
När du visar en resurs i portalen visas alternativ för att hantera just den resursen.

![Hantera resurser](./media/resource-group-portal/manage-resources.png)

Du kan utföra åtgärder som till exempel starta och stoppa en virtuell dator och konfigurera om egenskaperna för den virtuella datorn från dessa alternativ.

## <a name="move-resources"></a>Flytta resurser
Om du behöver flytta resurser till en annan resursgrupp eller en annan prenumeration finns [flytta resurser till en ny resursgrupp eller prenumeration](resource-group-move-resources.md).

## <a name="lock-resources"></a>Lås resurser
Du kan låsa en prenumeration, resursgrupp eller resurs för att förhindra andra användare i din organisation av misstag tas bort eller ändra viktiga resurser. Mer information finns i [Låsa resurser med Azure Resource Manager](resource-group-lock-resources.md).

[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="view-your-subscription-and-costs"></a>Visa din prenumeration och kostnader
Du kan visa information om din prenumeration och upplyfta kostnader för alla resurser. Välj **prenumerationer** och den prenumeration som du vill se. Du kan bara ha en prenumeration för att välja.

![prenumeration](./media/resource-group-portal/select-subscription.png)

Du kan se bränna hastighet.

![bränna hastighet](./media/resource-group-portal/burn-rate.png)

Och en uppdelning av kostnader efter resurstyp.

![resurskostnader](./media/resource-group-portal/cost-by-resource.png)

## <a name="export-template"></a>Exportera mall
När du har installerat din resursgrupp, kanske du vill visa Resource Manager-mallen för resursgruppen. Exportera mallen har två fördelar:

1. Du kan enkelt automatisera framtida distributioner av lösningen eftersom mallen innehåller hela infrastrukturen.
2. Du kan bekanta dig med mallens syntax genom att titta på den JavaScript Object Notation (JSON) som representerar din lösning.

Detaljerade anvisningar finns [exportera Azure Resource Manager-mall från befintliga resurser](resource-manager-export-template.md).

## <a name="delete-resource-group-or-resources"></a>Ta bort resursgruppen eller resurser
Tar bort en resursgrupp alla resurser som ingår i den. Du kan också ta bort enskilda resurser inom en resursgrupp. Var försiktig när du tar bort en resursgrupp. Resursgruppen kan innehålla resurser som är beroende av andra resursgrupper.

![Ta bort grupp](./media/resource-group-portal/delete-group.png)

## <a name="next-steps"></a>Nästa steg
* Om du vill visa aktivitetsloggar finns [granskningsåtgärder med Resource Manager](resource-group-audit.md).
* Om du vill visa information om en distribution finns [visa distributionsåtgärder](resource-manager-deployment-operations.md).
* För att distribuera resurser via portalen finns [distribuera resurser med Resource Manager-mallar och Azure-portalen](resource-group-template-deploy-portal.md).
* För att hantera åtkomst till resurser, se [använda rolltilldelningar för att hantera åtkomst till resurserna i Azure-prenumeration](../role-based-access-control/role-assignments-portal.md).
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](resource-manager-subscription-governance.md).

