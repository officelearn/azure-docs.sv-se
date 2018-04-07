---
title: Privileged Identity Management resurser för Azure - identifiera och hantera Azure-resurser | Microsoft Docs
description: Beskriver hur du skyddar Azure-resurser.
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
ms.openlocfilehash: 78650e47ec92aa144e4ccc8c57f309240bf31ee3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="discover-and-manage-azure-resources"></a>Identifiera och hantera Azure-resurser

Om din organisation använder redan Azure AD PIM för att skydda administratörer i din katalog eller om du är en prenumerationsägaren vill skydda produktionsresurser är på rätt plats.

När du först aktivera PIM för Azure-resurser, måste du identifiera och markera resurser som ska skyddas med PIM. Det finns ingen gräns för antalet resurser som du kan hantera med PIM, men vi rekommenderar att börja med dina viktigaste resurser (produktion) först.

> [!Note]
> Endast prenumerationsresurser kan söka och valts för hantering. Om du väljer att hantera prenumerationer i PIM ska aktivera hantering för alla underordnade resurser samt.

## <a name="discover-resources"></a>Identifiera resurser

Gå till Azure AD PIM och välj Azure-resurser i avsnittet Hantera i den vänstra navigeringsmenyn.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

Om det här är första gången du behöver använder PIM för Azure-resurser du köra identifieringen för att hitta resurser för att hantera.
Klicka på knappen ”Identifiera resurser” i mitten av skärmen för att starta upplevelsen av identifieringen.

![](media/azure-pim-resource-rbac/aadpim_first_run_discovery.png)

Om en annan resurs eller directory administratör i din organisation redan hanterar en Azure-resurs med PIM eller om du har en berättigad rolltilldelning för en resurs, listvyn innehåller meddelandet: ”identifiera resurser eller aktivera en berättigad roll tilldelning fortsätta ”. 

![](media/azure-pim-resource-rbac/aadpim_discover_eligible_not_active.png)

När du väljer knappen i Åtgärdsfältet eller i mitten av skärmen för att identifiera resurser, visas en lista över tillgängliga för hantering av prenumerationer. Nu ser du markerade prenumerationer anger de skyddas av PIM.

> [!Note]
> Att förhindra att en annan resursadministratör tar bort PIM-inställningar, när en prenumeration hanteras den inte hanteras.

![](media/azure-pim-resource-rbac/aadpim_discovery_some_selected.png)

Hovra över en prenumeration som du vill skydda med PIM och markera rutan längst till vänster om raden. Du kan välja flera prenumerationer i taget.

![](media/azure-pim-resource-rbac/aadpim_discovery_all_selected.png)

Onboarding-processen att starta processen Klicka på ”Hantera resurs” i fältet högst upp på skärmen.

![](media/azure-pim-resource-rbac/aadpim_discovery_click_manage.png)

De markerade resurserna hanteras nu av PIM. Stäng skärmen identifiering med hjälp av ”X” i det övre högra hörnet på sidan och klicka på Uppdatera i fältet högst upp på skärmen för hantera Azure-resurser ska börja hantera PIM-inställningar och tilldela medlemmar.

![](media/azure-pim-resource-rbac/aadpim_discovery_resources_refresh.png)

## <a name="next-steps"></a>Nästa steg

[Konfigurera inställningar för serverrollen](pim-resource-roles-configure-role-settings.md)

[Tilldela roller i PIM](pim-resource-roles-assign-roles.md)
