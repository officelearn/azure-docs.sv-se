---
title: Tilldela katalog roller till användare – Azure Active Directory | Microsoft Docs
description: Anvisningar om hur du tilldelar roller som administratör och icke-administratör till användare med Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2df52969ea79e5d1af132aa82c2ec1ceedb92b82
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75422914"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Tilldela roller som administratör och icke-administratör till användare med Azure Active Directory
Om en användare i din organisation behöver behörighet att hantera Azure Active Directory (Azure AD)-resurser måste du tilldela användaren en lämplig roll i Azure AD, baserat på de åtgärder som användaren behöver behörighet att utföra.

Mer information om tillgängliga roller finns [i tilldela administratörs roller i Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Mer information om hur du lägger till användare finns i [lägga till nya användare i Azure Active Directory](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Tilldela roller
Ett vanligt sätt att tilldela Azure AD-roller till en användare finns på sidan **katalog roll** för en användare.

Du kan också tilldela roller med Privileged Identity Management (PIM). Mer detaljerad information om hur du använder PIM finns [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management).

### <a name="to-assign-a-role-to-a-user"></a>Tilldela en roll till en användare
1. Gå till [Azure Portal](https://portal.azure.com/) och logga in med ett globalt administratörs konto för katalogen. 

2. Sök efter och välj **Azure Active Directory**.

      ![Azure Portal Sök efter Azure Active Directory](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)


3. Välj **Användare**.

4. Sök efter och välj den användare som ska få roll tilldelningen. Till exempel _Alain Charon_.

      ![Sidan alla användare – Välj användaren](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. På sidan **Alain Charon-Profile** väljer du **tilldelade roller**.

    Sidan **Alain Charon-Directory-roll** visas.

6. Välj **Lägg till tilldelning**, Välj den roll som du vill tilldela till Alain (till exempel _program administratör_) och välj sedan **Välj**.

    ![Sidan tilldelade roller – visar den valda rollen](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    Rollen program administratör tilldelas Alain Charon och visas på sidan **Alain Charon-Directory roll** .

## <a name="remove-a-role-assignment"></a>Ta bort en rolltilldelning
Om du behöver ta bort roll tilldelningen från en användare kan du också göra det från **Alain Charon-Directory roll-** sidan.

### <a name="to-remove-a-role-assignment-from-a-user"></a>Ta bort en roll tilldelning från en användare

1. Välj **Azure Active Directory**, Välj **användare**och Sök sedan efter och välj den användare som ska få roll tilldelningen borttagen. Till exempel _Alain Charon_.

2. Välj **tilldelade roller**, Välj **program administratör**och välj sedan **ta bort tilldelning**.

    ![Sidan tilldelade roller, som visar den valda rollen och alternativet ta bort](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    Rollen program administratör tas bort från Alain Charon och visas inte längre på sidan **Alain Charon-Directory roll** .

## <a name="next-steps"></a>Nästa steg
- [Lägga till eller ta bort användare](add-users-azure-active-directory.md)

- [Lägga till eller ändra profil information](active-directory-users-profile-azure-portal.md)

- [Lägga till gästanvändare från annan katalog](../b2b/what-is-b2b.md)

Du kan också utföra andra användar hanterings uppgifter, till exempel tilldela ombud, använda principer och dela användar konton. Mer information om andra tillgängliga åtgärder finns i [Azure Active Directory User Management-dokumentation](../users-groups-roles/index.yml).


