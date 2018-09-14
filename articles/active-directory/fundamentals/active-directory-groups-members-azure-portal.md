---
title: Lägga till eller ta bort medlemmar med hjälp av Azure Active Directory | Microsoft Docs
description: Hur du lägger till eller ta bort användare och enheter från en grupp med Azure Active Directory.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: lizross
ms.custom: it-pro
ms.reviewer: krbain
ms.openlocfilehash: f9244e1285396a2d5de40b596d47e311efa50b83
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45574241"
---
# <a name="how-to-add-or-remove-group-members-using-azure-active-directory"></a>Så här: lägga till eller ta bort medlemmar med hjälp av Azure Active Directory
Med Azure Active Directory kan fortsätta du att lägga till och ta bort medlemmar.

## <a name="to-add-group-members"></a>Att lägga till medlemmar i gruppen

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som Global administratör för katalogen.

2. Välj **Azure Active Directory**, och välj sedan **grupper**.

3. Från den **grupper – alla grupper** , söka efter och välj den grupp du vill lägga till medlem till. I det här fallet använder våra tidigare skapad grupp **MDM princip – västra**.

    ![Grupper – alla gruppsidan gruppnamn markerat](media/active-directory-groups-members-azure-portal/group-all-groups-screen.png)

4. Från den **MDM princip – västra översikt** väljer **medlemmar** från den **hantera** området.

    ![Princip för MDM – västra översiktssidan med medlemmar alternativet markerat](media/active-directory-groups-members-azure-portal/group-overview-blade.png)

5. Välj **lägga till medlemmar**, och sedan söka efter och väljer du de medlemmar som du vill lägga till i gruppen och välj sedan **Välj**.

    Du får ett felmeddelande om att medlemmar lades till.

    ![Lägg till medlemmar sida med söks igenom efter medlemmar som visas](media/active-directory-groups-members-azure-portal/update-members.png)

6. Uppdatera skärmen för att se alla medlemsnamn som lagts till i gruppen.

## <a name="to-remove-group-members"></a>Att ta bort medlemmar

1. Från den **grupper – alla grupper** , söka efter och välj den grupp du vill ta bort medlemmen från. Igen vi använder **MDM princip – västra**.

2. Välj **medlemmar** från den **hantera** området, Sök efter och välj namnet på medlemmen som ska ta bort och välj sedan **ta bort**.

    ![Sidan för medlemmen information, med alternativet för att ta bort](media/active-directory-groups-members-azure-portal/remove-members-from-group.png)

## <a name="next-steps"></a>Nästa steg

- [Visa dina grupper och medlemmar](active-directory-groups-view-azure-portal.md)

- [Redigera din gruppinställningar](active-directory-groups-settings-azure-portal.md)

- [Hantera åtkomst till resurser med hjälp av grupper](active-directory-manage-groups.md)

- [Hantera dynamiska regler för användare i en grupp](../users-groups-roles/groups-create-rule.md)

- [Koppla eller lägga till en Azure-prenumeration i Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
