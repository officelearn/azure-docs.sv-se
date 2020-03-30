---
title: Tilldela katalogroller till användare - Azure Active Directory | Microsoft-dokument
description: Instruktioner om hur du tilldelar administratörs- och icke-administratörsroller till användare med Azure Active Directory.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422914"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Tilldela administratörs- och icke-administratörsroller till användare med Azure Active Directory
Om en användare i organisationen behöver behörighet för att hantera Azure Active Directory (Azure AD) resurser, måste du tilldela användaren en lämplig roll i Azure AD, baserat på de åtgärder som användaren behöver behörighet att utföra.

Mer information om tillgängliga roller finns i [Tilldela administratörsroller i Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Mer information om hur du lägger till användare finns i [Lägga till nya användare i Azure Active Directory](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Tilldela roller
Ett vanligt sätt att tilldela Azure AD-roller till en användare finns på **rollsidan katalog** för en användare.

Du kan också tilldela roller med pim (Privileged Identity Management). Mer detaljerad information om hur du använder PIM finns i [Privilegierad identitetshantering](https://docs.microsoft.com/azure/active-directory/privileged-identity-management).

### <a name="to-assign-a-role-to-a-user"></a>Så här tilldelar du en roll till en användare
1. Gå till [Azure-portalen](https://portal.azure.com/) och logga in med ett globalt administratörskonto för katalogen. 

2. Sök efter och välj **Azure Active Directory**.

      ![Azure portal sökning efter Azure Active Directory](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)


3. Välj **Användare**.

4. Sök efter och välj den användare som hämtar rolltilldelningen. Till exempel _Alain Charon_.

      ![Sidan Alla användare – välj användare](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. På sidan **Alain Charon - Profil** väljer du **Tilldelade roller**.

    Rollsidan **Alain Charon - Directory** visas.

6. Välj **Lägg till tilldelning**, välj den roll som ska tilldelas Alain (till exempel _Programadministratör)_ och välj sedan **Välj**.

    ![Sidan Tilldelade roller – som visar den valda rollen](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    Rollen Programadministratör tilldelas Alain Charon och visas på **rollsidan Alain Charon - Directory.**

## <a name="remove-a-role-assignment"></a>Ta bort en rolltilldelning
Om du behöver ta bort rolltilldelningen från en användare kan du också göra det från **rollsidan Alain Charon - Directory.**

### <a name="to-remove-a-role-assignment-from-a-user"></a>Så här tar du bort en rolltilldelning från en användare

1. Välj **Azure Active Directory**, välj **Användare**och sök sedan efter och välj den användare som får rolltilldelningen borttagen. Till exempel _Alain Charon_.

2. Välj **Tilldelade roller,** välj **Programadministratör**och välj sedan **Ta bort tilldelning**.

    ![Tilldelade roller sida, som visar den valda rollen och ta bort alternativet](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    Rollen Programadministratör tas bort från Alain Charon och visas inte längre på **rollsidan Alain Charon - Directory.**

## <a name="next-steps"></a>Nästa steg
- [Lägga till eller ta bort användare](add-users-azure-active-directory.md)

- [Lägga till eller ändra profilinformation](active-directory-users-profile-azure-portal.md)

- [Lägga till gästanvändare från annan katalog](../b2b/what-is-b2b.md)

Du kan också utföra andra användarhanteringsuppgifter, till exempel tilldela ombud, använda principer och dela användarkonton. Mer information om andra tillgängliga åtgärder finns i Dokumentationen för [Azure Active Directory-användarhantering](../users-groups-roles/index.yml).


