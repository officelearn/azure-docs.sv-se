---
title: Så här hanterar du en Användartilldelad hanterad identitet med hjälp av Azure portal
description: Steg för steg instruktioner om hur du kan skapa, visa, ta bort och tilldela en roll till en hanterad Användartilldelad identitet.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18a15b8039322fc5e43a2b9dfed8a9bd3fc8b5fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60441644"
---
# <a name="create-list-delete-or-assign-a-role-to-a-user-assigned-managed-identity-using-the-azure-portal"></a>Skapa, visa, ta bort eller tilldela en roll till en Användartilldelad hanterad identitet med hjälp av Azure portal

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till tjänster som stöder Azure AD-autentisering, utan att behöva autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig skapa, visa, ta bort eller tilldela en roll till en Användartilldelad hanterad identitet med hjälp av Azure-portalen.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Om du är bekant med hanterade identiteter för Azure-resurser kan du kolla den [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan en hanterad identitet systemtilldelade och användartilldelade](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

## <a name="create-a-user-assigned-managed-identity"></a>Skapa en användartilldelad hanterad identitet

Om du vill skapa en hanterad Användartilldelad identitet, ditt konto måste den [hanterad Identitetsdeltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) rolltilldelning.

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumeration för att skapa den hanterade Användartilldelad identitet.
2. I sökrutan skriver *hanterade identiteter*, och under **Services**, klickar du på **hanterade identiteter**.
3. Klicka på **Lägg till** och ange värden i följande fält under **skapa användartilldelade hanteras** identity-fönstret:
   - **Resursnamn**: Det här är namnet på din användartilldelade hanterad identitet, till exempel UAI1.
   - **Prenumeration**: Välj den prenumeration för att skapa hanterade Användartilldelad identitet under
   - **Resursgrupp**: Skapa en ny resursgrupp för att innehålla din hanterade Användartilldelad identitet eller välj **Använd befintlig** att skapa den hanterade Användartilldelad identitet i en befintlig resursgrupp.
   - **Plats**: Välj en plats för att distribuera användartilldelade hanterad identitet, till exempel **västra USA**.
4. Klicka på **Skapa**.

![Skapa en användartilldelad hanterad identitet](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)

## <a name="list-user-assigned-managed-identities"></a>Lista användartilldelade hanterade identiteter

Om du vill lista/läsa en hanterad Användartilldelad identitet, ditt konto måste den [hanterade Identitetsoperatör](/azure/role-based-access-control/built-in-roles#managed-identity-operator) eller [hanterad Identitetsdeltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) rolltilldelning.

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumeration för att lista de hanterade användartilldelade identiteterna.
2. I sökrutan skriver *hanterade identiteter*, och under tjänster klickar du på **hanterade identiteter**.
3. En lista över hanterade användartilldelade identiteter för din prenumeration returneras.  För att se information om en hanterad Användartilldelad identitet klickar du på namnet.

![Lista användartilldelade hanterad identitet](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>Ta bort en hanterad Användartilldelad identitet

Om du vill ta bort en hanterad Användartilldelad identitet måste ditt konto måste den [hanterad Identitetsdeltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) rolltilldelning.

Tar bort en Användartilldelad identitet tar det inte bort från den virtuella datorn eller en resurs som tilldelades till.  Ta bort Användartilldelad identitet från en virtuell dator finns i [ta bort en hanterad Användartilldelad identitet från en virtuell dator](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#remove-a-user-assigned-managed-identity-from-a-vm).

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumeration att ta bort en hanterad Användartilldelad identitet.
2. Välj den hanterade Användartilldelad identitet och klicka på **ta bort**.
3. Under dialogrutan väljer du, **Ja**.

![Ta bort Användartilldelad hanterad identitet](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>Tilldela en roll till en hanterad Användartilldelad identitet 

Om du vill tilldela en roll till en hanterad Användartilldelad identitet, ditt konto måste den [administratör för användaråtkomst](/azure/role-based-access-control/built-in-roles#user-access-administrator) rolltilldelning.

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumeration för att lista de hanterade användartilldelade identiteterna.
2. I sökrutan skriver *hanterade identiteter*, och under tjänster klickar du på **hanterade identiteter**.
3. En lista över hanterade användartilldelade identiteter för din prenumeration returneras.  Välj den användartilldelade hanterade identitet som du vill tilldela en roll.
4. Välj **åtkomstkontroll (IAM)** och välj sedan **Lägg till rolltilldelning**.

   ![Användartilldelade hanterad identitet start](./media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

5. Konfigurera följande värden i bladet Lägg till rollen tilldelning och klicka sedan på **spara**:
   - **Rollen** -roll att tilldela
   - **Tilldela åtkomst till** -resursen för att tilldela den användartilldelade hanterad identitet
   - **Välj** -medlemmen att tilldela åtkomst
   
   ![Användartilldelade hanterad identitet IAM](./media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)  
