---
title: Tilldela directory roller till användare – Azure Active Directory | Microsoft Docs
description: Anvisningar om hur du tilldelar rollerna Administratör och icke-administratör för användare med Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.openlocfilehash: 067362796ec64e7060a055144729e2b0f7e53c99
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2019
ms.locfileid: "55097901"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Tilldela användare med Azure Active Directory rollerna Administratör och icke-administratör
Om en användare i din organisation behöver behörighet att hantera resurser i Azure Active Directory (AD Azure), måste du tilldela användaren en lämplig roll i Azure AD, beroende på vilka åtgärder som användaren behöver behörighet att utföra.

Mer information om tillgängliga roller finns i [Tilldela administratörsroller i Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Mer information om att lägga till användare finns i [lägga till nya användare till Azure Active Directory](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Tilldela roller
Ett vanligt sätt att tilldela en användare med Azure AD-roller finns på den **katalogroll** för en användare.

Du kan också tilldela roller med hjälp av Privileged Identity Management (PIM). Mer information om hur du använder PIM finns [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management).

### <a name="to-assign-a-role-to-a-user"></a>Tilldela en roll till en användare
1. Logga in på [Azure-portalen](https://portal.azure.com/) med ett Globalt administratörskonto för katalogen.

2. Välj **Azure Active Directory**väljer **användare**, och sök sedan efter och välj den användare som kom rolltilldelningen. Till exempel _Alain Charon_.

3. På den **Alain Charon - profil** väljer **katalogroll**.

    Den **Alain Charon - katalogroll** visas.

4. Välj **Lägg till rollen**, Välj roll att tilldela Alain (till exempel _programadministratör_), och välj sedan **Välj**.

    ![Katalogsidan för roller, som visar den valda rollen](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    En administratör för program som har tilldelats Alain Charon och den visas på den **Alain Charon - katalogroll** sidan.

## <a name="remove-a-role-assignment"></a>Ta bort en rolltilldelning
Om du vill ta bort rolltilldelningen från en användare kan du också göra det från den **Alain Charon - katalogroll** sidan.

### <a name="to-remove-a-role-assignment-from-a-user"></a>Ta bort en rolltilldelning från en användare

1. Välj **Azure Active Directory**väljer **användare**, och sök sedan efter och välj den användare som kom rolltilldelningen har tagits bort. Till exempel _Alain Charon_.

2. Välj **katalogroll**väljer **programadministratör**, och välj sedan **ta bort rollen**.

    ![Katalogsidan för roller, som visar den valda rollen och alternativet Ta bort](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    En administratör för programmet tas bort från Alain Charon och den inte längre visas på den **Alain Charon - katalogroll** sidan.

## <a name="next-steps"></a>Nästa steg
- [Lägga till eller ta bort användare](add-users-azure-active-directory.md)

- [Lägga till eller ändra profilinformation](active-directory-users-profile-azure-portal.md)

- [Lägga till gästanvändare från annan katalog](../b2b/what-is-b2b.md)

Eller du kan utföra andra uppgifter för användare, till exempel tilldela ombud, med hjälp av principer och dela användarkonton. Läs mer om andra tillgängliga åtgärder, [Azure Active Directory management supportdokumentation](../users-groups-roles/index.yml).


