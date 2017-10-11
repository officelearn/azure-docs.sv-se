---
title: "Tilldela administratörsroller i Azure Active Directory användare | Microsoft Docs"
description: "Beskriver hur du ändrar administrativa användarinformation i Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: a1ca1a53-50d8-4bf0-ae8f-73fa1253e2d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.openlocfilehash: bfadf133154488f9827cfbeaa98ddb0eb84b52f6
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="assign-a-user-to-administrator-roles-in-azure-active-directory"></a>Tilldela administratörsroller i Azure Active Directory en användare
Den här artikeln beskriver hur du tilldelar en administrativ roll till en användare i Azure Active Directory (AD Azure). Information om att lägga till nya användare i din organisation finns i [lägga till nya användare i Azure Active Directory](active-directory-users-create-azure-portal.md). Tillagda användare har inte administratörsbehörighet som standard, men du kan tilldela roller till dem när som helst.

## <a name="assign-a-role-to-a-user"></a>Tilldela en roll till en användare
1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är en global administratör för katalogen.
2. Välj **fler tjänster**, ange **användare och grupper** i textrutan och välj sedan **RETUR**.

   ![Öppna användarhantering](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)
3. På den **användare och grupper** bladet väljer **alla användare**.

   ![Öppna bladet för alla användare](./media/active-directory-users-assign-role-azure-portal/create-users-open-users-blade.png)
4. På den **användare och grupper – alla användare** bladet Välj en användare i listan.
5. På bladet för den valda användaren väljer **Directory rollen**, och tilldelar sedan användaren till en roll från den **Directory rollen** lista. Mer information om användar- och administratörsroller finns i [Tilldela administratörsroller i Azure AD](active-directory-assign-admin-roles.md).

      ![Tilldela en användare till en roll](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)
6. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg
* [Lägga till en användare](active-directory-users-create-azure-portal.md)
* [Återställa en användares lösenord i den nya Azure-portalen](active-directory-users-reset-password-azure-portal.md)
* [Ändra en användares arbetsinformation](active-directory-users-work-info-azure-portal.md)
* [Hantera användarprofiler](active-directory-users-profile-azure-portal.md)
* [Tar bort en användare i din Azure AD](active-directory-users-delete-user-azure-portal.md)
