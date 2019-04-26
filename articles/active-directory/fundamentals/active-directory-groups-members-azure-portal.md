---
title: Lägga till eller ta bort medlemmar – Azure Active Directory | Microsoft Docs
description: Anvisningar om hur du lägger till eller ta bort medlemmar från en grupp med Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: lizross
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3dafdf4c67e8d3d74109b3879fb0deacd79b1774
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60249110"
---
# <a name="add-or-remove-group-members-using-azure-active-directory"></a>Lägga till eller ta bort medlemmar med hjälp av Azure Active Directory
Med Azure Active Directory kan fortsätta du att lägga till och ta bort medlemmar.

## <a name="to-add-group-members"></a>Att lägga till medlemmar i gruppen

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett Globalt administratörskonto för katalogen.

2. Välj **Azure Active Directory**, och välj sedan **grupper**.

3. Från den **grupper – alla grupper** , söka efter och välj den grupp du vill lägga till medlem till. I det här fallet använder våra tidigare skapad grupp **MDM princip – västra**.

    ![Grupper – alla gruppsidan gruppnamn markerat](media/active-directory-groups-members-azure-portal/group-all-groups-screen.png)

4. På **översiktssidan för MDM policy – West** väljer du **Medlemmar** i området **Hantera**.

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

- [Redigera dina gruppinställningar](active-directory-groups-settings-azure-portal.md)

- [Hantera åtkomst till resurser med grupper](active-directory-manage-groups.md)

- [Hantera dynamiska regler för användare i en grupp](../users-groups-roles/groups-create-rule.md)

- [Associera eller lägg till en Azure-prenumeration till Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
