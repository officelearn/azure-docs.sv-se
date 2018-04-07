---
title: Privileged Identity Management för Azure-resurser - aktivera prenumerationshantering | Microsoft Docs
description: Lär dig hur globala administratörer kan hantera prenumerationer i klienten.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/27/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 4c6ae3da34fe5157314b8ea422591f7ecbd2a667
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="enable-subscription-management"></a>Aktivera prenumerationshantering av

Du kan inte ha åtkomst till alla prenumerationsresurser i din klient som en Global administratör i din katalog. Den här artikeln kommer beskriver stegen för att ge åtkomst till alla prenumerationer i din klient och en rekommendation att återstående kompatibel med eventuella säkerhetsåtgärder som din organisation kräver när du har fått åtkomst.

## <a name="who-can-enable-management-of-subscriptions-in-my-directory"></a>Vem kan aktivera hantering av prenumerationer i min katalog?

Varje användare som har tilldelats rollen Global administratör måste följa stegen nedan för att aktivera prenumerationshantering av. När du har aktiverat prenumerationshantering själv kan du lägga till andra globala administratörer som kan behöva åtkomst till företagsresurser samt. Det finns ingen inställning för katalogen som ger åtkomst för alla medlemmar i rollen Global administratör.

## <a name="log-on-to-the-azure-portal"></a>Logga in på Azure-portalen

Börja med att logga in på Azure-portalen med ett konto som är en berättigad eller aktiv medlem i rollen Global administratör. Om kontot är berättigad Global administratör måste du först aktivera rollen innan du går vidare till nästa steg.

## <a name="access-the-azure-ad-admin-center"></a>Öppna Azure AD Administrationscenter

Nu när du är inloggad på Azure-portalen som Global administratör kan du redigera inställningar som ger åtkomst till Azure-prenumerationer. Gå till Administrationscenter för Azure AD, leta upp och välj fliken Egenskaper i det vänstra navigeringsfönstret.

![](media/azure-pim-resource-rbac/aad_properties.png)

Ändra alternativet ”Global administratör kan hantera Azure-prenumerationer” ”Yes” i listan med egenskaper.

![](media/azure-pim-resource-rbac/aad_properties_save.png)

## <a name="navigate-to-azure-ad-pim"></a>Gå till Azure AD PIM

Med det här alternativet aktiverat, läggs ditt konto automatiskt till rollen ”Administratör för användaråtkomst” för varje prenumeration resurs i klienten. Här kan gå till Azure AD PIM och välj Azure-resurser under avsnittet Hantera i det vänstra navigeringsfönstret.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

## <a name="manage-and-discover-resources"></a>Hantera och identifiera resurser

Om din organisation använder redan Azure AD PIM för att skydda administratörer i Azure Active Directory visas en lista över prenumerationer när bladet läses in.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

> [!NOTE]
> Kontrollera om du inte ser några resurser:
>- Din rollen Global administratör har inte upphört att gälla 
>- Din organisation har en Azure-prenumeration

![](media/azure-pim-resource-rbac/aadpim_rbac_empty_resource_list.png)

## <a name="configure-assignments"></a>Konfigurera tilldelningar

Välj en prenumeration i listan och tilldela dig själv (eller en grupp som du är medlem i) som en berättigad ägare av resursen. 
[Läs mer om hur du tilldelar roller](pim-resource-roles-assign-roles.md).

Upprepa proceduren för varje resurs innan du fortsätter till nästa steg.

## <a name="clean-up-standing-access"></a>Rensa stående åtkomst

Nu när du har berättigade tilldelningar för viktiga prenumerationer i din organisation kan du rensar bort stående åtkomst genom att inaktivera alternativet i directory egenskaper:

![](media/azure-pim-resource-rbac/aad_properties_no.png)

## <a name="next-steps"></a>Nästa steg

[Identifiera resurser](pim-resource-roles-discover-resources.md)

[Konfigurera inställningar för serverrollen](pim-resource-roles-configure-role-settings.md)








