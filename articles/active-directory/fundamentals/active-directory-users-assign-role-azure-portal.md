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
ms.openlocfilehash: d071ff071c13637b15479d86a699d0b368119196
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73742573"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Tilldela roller som administratör och icke-administratör till användare med Azure Active Directory
Om en användare i din organisation behöver behörighet att hantera Azure Active Directory (Azure AD)-resurser måste du tilldela användaren en lämplig roll i Azure AD, baserat på de åtgärder som användaren behöver behörighet att utföra.

Mer information om tillgängliga roller finns [i tilldela administratörs roller i Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Mer information om hur du lägger till användare finns i [lägga till nya användare i Azure Active Directory](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Tilldela roller
Ett vanligt sätt att tilldela Azure AD-roller till en användare finns på sidan **katalog roll** för en användare.

Du kan också tilldela roller med Privileged Identity Management (PIM). Mer detaljerad information om hur du använder PIM finns [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management).

### <a name="to-assign-a-role-to-a-user"></a>Tilldela en roll till en användare
1. Logga in på [Azure-portalen](https://portal.azure.com/) med ett Globalt administratörskonto för katalogen.

2. Välj **Azure Active Directory**, Välj **användare**och Sök sedan efter och välj den användare som ska hämta roll tilldelningen. Till exempel _Alain Charon_.

      ![Sidan alla användare – Välj användaren](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

3. På sidan **Alain Charon-Profile** väljer du **tilldelade roller**.

    Sidan **Alain Charon-Directory-roll** visas.

4. Välj **Lägg till tilldelning**, Välj den roll som du vill tilldela till Alain (till exempel _program administratör_) och välj sedan **Välj**.

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


