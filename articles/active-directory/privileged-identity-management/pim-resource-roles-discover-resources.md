---
title: Identifiera och hantera Azure-resurser med hjälp av Privileged Identity Management | Microsoft Docs
description: Beskriver hur du skyddar Azure-resurser med hjälp av PIM.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: c5b26c01028e2a5746132939a2058cacdcad859f
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622110"
---
# <a name="discover-and-manage-azure-resources-by-using-privileged-identity-management"></a>Identifiera och hantera Azure-resurser med hjälp av Privileged Identity Management

Lär dig att identifiera och hantera Azure-resurser när du använder Privileged Identity Management (PIM) i Azure Active Directory (AD Azure). Den här informationen kan vara användbart att organisationer som redan använder PIM för att skydda resurser som administratör och prenumerationsägare som vill skydda produktionsresurser.

När du först ställa in PIM för Azure-resurser, måste du identifiera och välj resurser som ska skyddas med PIM. Det finns ingen gräns för hur många resurser som du kan hantera med PIM. Vi rekommenderar dock att börja med dina viktigaste resurser (produktion).

> [!NOTE]
> Du kan bara söka efter och välja hantera med hjälp av PIM-prenumerationsresurser. Du kan också hantera underordnade resurser i prenumerationen när du hanterar en prenumeration i PIM.

## <a name="discover-resources"></a>Identifiera resurser

I Azure-portalen går du till den **Privileged Identity Management** fönstret. I den vänstra menyn i den **hantera** väljer **Azure-resurser**.

![”Privileged Identity Management - Azure-resurser” fönstret](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

Om det här är första gången du använder PIM för Azure-resurser bör du först kör identifiering för att hitta resurser för att hantera. I den **Identifieringsresurser** väljer den **identifiera resurser** knappen för att starta upplevelsen av identifieringen.

![Fönstret ”identifiera resurser”](media/azure-pim-resource-rbac/aadpim_first_run_discovery.png)

Om en annan resurs eller directory-administratör i din organisation redan hanterar en Azure-resurs med hjälp av PIM, eller om du har en berättigad rolltilldelning för en resurs, listvyn visas meddelandet **identifiera resurser eller aktivera en berättigad rolltilldelning för att fortsätta**. 

![Knappen ”Identifiera resurser” i ”Privileged Identity Manager - Azure-resurser” fönstret](media/azure-pim-resource-rbac/aadpim_discover_eligible_not_active.png)

När du väljer den **Identifieringsresurser** knapp, från den översta menyn eller mitt i fönstret visas en lista över prenumerationer som du kan hantera. Prenumerationer som är markerade skyddas redan av PIM.

> [!NOTE]
> Om du vill förhindra att en annan resursadministratör att ta bort PIM-inställningar, efter en prenumeration är inställd på hanterade får inte prenumerationen vara ohanterade.

![”Azure-resurser – identifiering” fönstret](media/azure-pim-resource-rbac/aadpim_discovery_some_selected.png)

I den **RESOURCE** kolumnen placera muspekaren över en prenumeration som du vill skydda med PIM. Markera kryssrutan till vänster om resursnamnet. Du kan välja flera prenumerationer i taget.

![Listan över resurser i ”Azure-resurser – identifiering” fönstret](media/azure-pim-resource-rbac/aadpim_discovery_all_selected.png)

Om du vill initiera onboarding-processen på den översta menyn, Välj **hantera resurs**.

![Knappen ”Hantera resurs” i ”Azure-resurser – identifiering” fönstret](media/azure-pim-resource-rbac/aadpim_discovery_click_manage.png)

De markerade resurserna som nu hanteras av PIM. Om du vill stänga i det övre högra hörnet på skärmen identifiering Välj **X**. Börja hantera PIM-inställningar och tilldela medlemmar på menyn överst på den **Privileged Identity Management - Azure-resurser** väljer den **uppdatera** knappen.

![Knappen ”Uppdatera” i den övre menyn i ”Privileged Identity Management - Azure-resurser” fönstret](media/azure-pim-resource-rbac/aadpim_discovery_resources_refresh.png)

## <a name="next-steps"></a>Nästa steg

- [Konfigurera rollinställningar](pim-resource-roles-configure-role-settings.md)
- [Tilldela roller i PIM](pim-resource-roles-assign-roles.md)
