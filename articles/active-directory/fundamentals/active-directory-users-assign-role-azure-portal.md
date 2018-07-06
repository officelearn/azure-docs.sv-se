---
title: Tilldela administratörsroller till en användare i Azure Active Directory | Microsoft Docs
description: Så ändrar du användares administrativa information i Azure Active Directory
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 06/25/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.openlocfilehash: ec30f1507bfa45c29709a7f4b7dc1e91aa25ca57
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440756"
---
# <a name="assign-a-user-to-administrator-roles-in-azure-active-directory"></a>Tilldela administratörsroller till en användare i Azure Active Directory
Den här artikeln beskriver hur du tilldelar administratörsroller till en användare i Azure Active Directory (AD Azure). Information om hur du lägger till nya användare i din organisation finns i [Lägga till nya användare i Azure Active Directory](../add-users-azure-active-directory.md). Tillagda användare har inte administratörsbehörighet som standard, men du kan tilldela roller till dem när som helst.

## <a name="assign-a-role-to-a-user"></a>Tilldela en roll till en användare
1. Logga in på [Azure-portalen](https://portal.azure.com) med ett konto som är en global administratör eller en administratör med privilegierad roll för katalogen.

2. Välj **Azure Active Directory**, välj **Users** (Användare) och välj sedan en specifik användare i listan.

    ![Öppna användarhantering](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)

3. För den valda användaren väljer du **Directory role** (Katalogroll), **Add role**, (Lägg till roll) och sedan lämpliga administratörsroller i listan **Directory roles** (Katalogroll), till exempel **Conditional access administrator** (Administratör med villkorlig åtkomst). Mer information om administrativa roller finns i [Tilldela administratörsroller i Azure AD](../users-groups-roles/directory-assign-admin-roles.md). 

    ![Tilldela en användare till en roll](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)

1. Tryck på **Select** (Välj) för att spara.

## <a name="next-steps"></a>Nästa steg
* [Snabbstart: Lägg till eller ta bort användare i Azure Active Directory](add-users-azure-active-directory.md)
* [Hantera användarprofiler](active-directory-users-profile-azure-portal.md)
* [Lägga till gästanvändare från annan katalog](../b2b/what-is-b2b.md) 
* [Tilldela en användare till en roll i din Azure AD](active-directory-users-assign-role-azure-portal.md)
* [Återställa en borttagen användare](active-directory-users-restore.md)
