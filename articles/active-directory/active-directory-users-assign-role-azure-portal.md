---
title: Tilldela administratörsroller i Azure Active Directory användare | Microsoft Docs
description: Beskriver hur du ändrar administrativa användarinformation i Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: article
ms.date: 01/08/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.openlocfilehash: 580c5e66bedbec00495fa5fd0a7632005fa7af2f
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713001"
---
# <a name="assign-a-user-to-administrator-roles-in-azure-active-directory"></a>Tilldela administratörsroller i Azure Active Directory en användare
Den här artikeln beskriver hur du tilldelar en administrativ roll till en användare i Azure Active Directory (AD Azure). Information om att lägga till nya användare i din organisation finns i [lägga till nya användare i Azure Active Directory](active-directory-users-create-azure-portal.md). Tillagda användare har inte administratörsbehörighet som standard, men du kan tilldela roller till dem när som helst.

## <a name="assign-a-role-to-a-user"></a>Tilldela en roll till en användare
1. Logga in på [Azure AD administratörscenter](https://aad.portal.azure.com) med ett konto som är en global administratör för katalogen.
2. Välj **användare och grupper**.

   ![Öppna användarhantering](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)
3. Välj **alla användare**.
  
  ![Öppna gruppen för alla användare](./media/active-directory-users-assign-role-azure-portal/create-users-open-users-blade.png)
4. Välj en användare i listan.
5. För den valda användaren väljer **Directory rollen** och tilldelar sedan användaren till en roll från den **Directory rollen** lista. Mer information om användar- och administratörsroller finns i [Tilldela administratörsroller i Azure AD](active-directory-assign-admin-roles-azure-portal.md).

      ![Tilldela en användare till en roll](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)
6. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg
* [Snabbstart: Lägg till eller ta bort användare i Azure Active Directory](add-users-azure-active-directory.md)
* [Hantera användarprofiler](active-directory-users-profile-azure-portal.md)
* [Lägg till gästanvändare från en annan katalog](active-directory-b2b-what-is-azure-ad-b2b.md) 
* [Tilldela en användare till en roll i din Azure AD](active-directory-users-assign-role-azure-portal.md)
* [Återställa en borttagen användare](active-directory-users-restore.md)
