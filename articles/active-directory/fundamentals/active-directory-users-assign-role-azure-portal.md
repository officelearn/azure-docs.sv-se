---
title: Tilldela Azure AD-roller till användare – Azure Active Directory | Microsoft Docs
description: Anvisningar om hur du tilldelar roller som administratör och icke-administratör till användare med Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 06/15/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a468b5f43e8b024dd1da6e382049f8cce6de295
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/05/2020
ms.locfileid: "87797373"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Tilldela roller som administratör och icke-administratör till användare med Azure Active Directory

Om en av dina användare behöver behörighet för att hantera Azure AD-resurser i Azure Active Directory (Azure AD) måste du tilldela dem till en roll som tillhandahåller de behörigheter som de behöver. Information om vilka roller som hanterar Azure-resurser och vilka roller som hanterar Azure AD-resurser finns i [klassisk prenumerations administratörs roller, Azure-roller och Azure AD-roller](../../role-based-access-control/rbac-and-directory-admin-roles.md).

Mer information om tillgängliga Azure AD-roller finns [i tilldela administratörs roller i Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Information om hur du lägger till användare finns i [lägga till nya användare i Azure Active Directory](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Tilldela roller

Ett vanligt sätt att tilldela Azure AD-roller till en användare finns på sidan **tilldelade roller** för en användare. Du kan också använda den för att få en högre inaktivitet i en roll med hjälp av Privileged Identity Management (PIM). Mer information om hur du använder PIM finns [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management).

> [!Note]
> Om du har en Azure AD Premium P2-licens plan och redan använder PIM utförs alla roll hanterings aktiviteter i [Privileged Identity Managements upplevelsen](../users-groups-roles/directory-manage-roles-portal.md).
>
> ![Azure AD-roller som hanteras i PIM för användare som redan använder PIM och har en Premium P2-licens](./media/active-directory-users-assign-role-azure-portal/pim-manages-roles-for-p2.png)

## <a name="assign-a-role-to-a-user"></a>Tilldela en användare en roll

1. Gå till [Azure Portal](https://portal.azure.com/) och logga in med ett globalt administratörs konto för katalogen.

2. Sök efter och välj **Azure Active Directory**.

      ![Azure Portal Sök efter Azure Active Directory](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)

3. Välj **Användare**.

4. Sök efter och välj den användare som ska få roll tilldelningen. Till exempel _Alain Charon_.

      ![Sidan alla användare – Välj användaren](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. På sidan **Alain Charon-Profile** väljer du **tilldelade roller**.

    Sidan **Alain Charon-administrativa roller** visas.

6. Välj **Lägg till tilldelningar**, Välj den roll som du vill tilldela till Alain (till exempel _program administratör_) och välj sedan **Välj**.

    ![Sidan tilldelade roller – visar den valda rollen](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    Rollen program administratör tilldelas Alain Charon och visas på sidan **Alain Charon-administrativa roller** .

## <a name="remove-a-role-assignment"></a>Ta bort en rolltilldelning

Om du behöver ta bort roll tilldelningen från en användare kan du också göra det från sidan **Alain Charon-administrativa roller** .

### <a name="to-remove-a-role-assignment-from-a-user"></a>Ta bort en roll tilldelning från en användare

1. Välj **Azure Active Directory**, Välj **användare**och Sök sedan efter och välj den användare som ska få roll tilldelningen borttagen. Till exempel _Alain Charon_.

2. Välj **tilldelade roller**, Välj **program administratör**och välj sedan **ta bort tilldelning**.

    ![Sidan tilldelade roller, som visar den valda rollen och alternativet ta bort](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    Rollen program administratör tas bort från Alain Charon och visas inte längre på sidan **Alain Charon-administrativa roller** .

## <a name="next-steps"></a>Nästa steg

- [Lägga till eller ta bort användare](add-users-azure-active-directory.md)

- [Lägga till eller ändra profil information](active-directory-users-profile-azure-portal.md)

- [Lägga till gästanvändare från annan katalog](../b2b/what-is-b2b.md)

Andra användar hanterings uppgifter som du kan checka ut finns i [Azure Active Directory User Management-dokumentation](../users-groups-roles/index.yml).
