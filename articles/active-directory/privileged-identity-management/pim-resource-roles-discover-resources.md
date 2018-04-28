---
title: Identifiera och hantera Azure-resurser med hjälp av Privileged Identity Management | Microsoft Docs
description: Beskriver hur du skyddar Azure-resurser med hjälp av PIM.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: billmath
ms.openlocfilehash: 51a10ea164e8bd7650ad2823281d9ed6a4c91915
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="discover-and-manage-azure-resources-by-using-privileged-identity-management"></a>Identifiera och hantera Azure-resurser med hjälp av Privileged Identity Management

Lär dig att identifiera och hantera Azure-resurser när du använder Privileged Identity Management (PIM) i Azure Active Directory (AD Azure). Den här informationen kan vara användbart att organisationer som redan använder PIM för att skydda resurser som administratör och prenumeration ägare som är ute efter för att skydda produktionsresurser.

När du först ställa in PIM för Azure-resurser, måste du identifiera och markera resurser som ska skyddas med PIM. Det finns ingen gräns för antalet resurser som du kan hantera med PIM. Vi rekommenderar dock från och med dina viktigaste resurser (produktion).

> [!NOTE]
> Du kan bara söka efter och välja prenumerationsresurser hantera med hjälp av PIM. När du hanterar en prenumeration i PIM kan du också hantera underordnade resurser i prenumerationen.

## <a name="discover-resources"></a>Identifiera resurser

I Azure-portalen går du till den **Privileged Identity Management** fönstret. I den vänstra menyn i den **hantera** väljer **Azure-resurser**.

![”Privileged Identity Management - Azure-resurser” fönstret](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

Om det här är första gången du använder PIM för Azure-resurser först kör discovery för att hitta resurser för att hantera. I den **Identifieringsresurser** väljer den **identifiera resurser** knappen för att starta upplevelsen av identifieringen.

![Fönstret ”identifiera resurser”](media/azure-pim-resource-rbac/aadpim_first_run_discovery.png)

Om en annan resurs eller directory administratör i din organisation redan hanterar en Azure-resurs med hjälp av PIM, eller om du har en berättigad rolltilldelning för en resurs, listvyn visas meddelandet **identifiera resurser eller aktivera en berättigad rolltilldelning fortsätta**. 

![Knappen ”Identifiera resurser” i ”Privileged Identity Manager - Azure-resurser” fönstret](media/azure-pim-resource-rbac/aadpim_discover_eligible_not_active.png)

När du väljer den **Identifieringsresurser** knappen, från den översta menyn eller i mitten av fönstret visas en lista över prenumerationer som du kan hantera. Prenumerationer som är markerade skyddas redan av PIM.

> [!NOTE]
> Om du vill förhindra att en annan resursadministratör att ta bort PIM-inställningar, efter en prenumeration är inställd på hanterade kan inte prenumerationen hanteras.

![”Azure-resurser - identifiering” fönstret](media/azure-pim-resource-rbac/aadpim_discovery_some_selected.png)

I den **resurs** kolumn, placera pekaren över en prenumeration som du vill skydda med PIM. Markera kryssrutan till vänster om resursnamnet. Du kan välja flera prenumerationer i taget.

![Listan över resurser i ”Azure-resurser - identifiering” fönstret](media/azure-pim-resource-rbac/aadpim_discovery_all_selected.png)

Om du vill initiera onboarding-processen på den översta menyn, Välj **hantera resurs**.

![Knappen ”Hantera resurs” i ”Azure-resurser - identifiering” fönstret](media/azure-pim-resource-rbac/aadpim_discovery_click_manage.png)

De markerade resurserna hanteras nu av PIM. Om du vill stänga i det övre högra hörnet på skärmen identifiering Välj **X**. Börja hantera PIM-inställningar och tilldela medlemmar på menyn överst i den **Privileged Identity Management - Azure-resurser** väljer den **uppdatera** knappen.

![Knappen ”Uppdatera” i den översta menyn för ”Privileged Identity Management - Azure-resurser” fönstret](media/azure-pim-resource-rbac/aadpim_discovery_resources_refresh.png)

## <a name="next-steps"></a>Nästa steg

- [Konfigurera rollinställningar](pim-resource-roles-configure-role-settings.md)
- [Tilldela roller i PIM](pim-resource-roles-assign-roles.md)
