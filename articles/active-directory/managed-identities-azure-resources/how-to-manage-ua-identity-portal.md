---
title: Hantera en användartilldelad hanterad identitet i Azure-portalen - Azure AD
description: Steg för steg instruktioner om hur du skapar, lista, ta bort och tilldela en roll till en användartilldelad hanterad identitet.
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
ms.openlocfilehash: 10caa9e5e61dc1dd0c1062583f55a7357c643ce5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244139"
---
# <a name="create-list-delete-or-assign-a-role-to-a-user-assigned-managed-identity-using-the-azure-portal"></a>Skapa, lista, ta bort eller tilldela en roll till en användartilldelad hanterad identitet med Hjälp av Azure-portalen

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Hanterade identiteter för Azure-resurser ger Azure-tjänster en hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till tjänster som stöder Azure AD-autentisering, utan att behöva autentiseringsuppgifter i koden. 

I den här artikeln får du lära dig hur du skapar, listar, tar bort eller tilldelar en roll till en användartilldelad hanterad identitet med Hjälp av Azure Portal.

## <a name="prerequisites"></a>Krav

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [översiktsavsnittet](overview.md). **Var noga med att granska [skillnaden mellan en systemtilldelad och användartilldelad hanterad identitet](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

## <a name="create-a-user-assigned-managed-identity"></a>Skapa en användartilldelad hanterad identitet

Om du vill skapa en användartilldelad hanterad identitet behöver ditt konto rolltilldelningen [Managed Identity Contributor.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett konto som är associerat med Azure-prenumerationen för att skapa den användartilldelade hanterade identiteten.
2. Skriv *Hanterade identiteter i*sökrutan och klicka på **Hanterade identiteter**under **Tjänster**.
3. Klicka på **Lägg till** och ange värden i följande fält under Fönstret Skapa **användare tilldelad hanterad** identitet:
   - **Resursnamn**: Det här är namnet på din användartilldelade hanterade identitet, till exempel UAI1.
   - **Prenumeration**: Välj prenumeration för att skapa den användartilldelade hanterade identiteten under
   - **Resursgrupp**: Skapa en ny resursgrupp som innehåller den användartilldelade hanterade identiteten eller välj **Använd befintlig** för att skapa den användartilldelade hanterade identiteten i en befintlig resursgrupp.
   - **Plats**: Välj en plats för att distribuera den användartilldelade hanterade identiteten, till exempel **västra USA.**
4. Klicka på **Skapa**.

![Skapa en användartilldelad hanterad identitet](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)

## <a name="list-user-assigned-managed-identities"></a>Lista användartilldelade hanterade identiteter

Om du vill lista/läsa en användartilldelad hanterad identitet behöver ditt konto rolltilldelningen [Managed Identity Operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator) eller Managed Identity [Contributor.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett konto som är associerat med Azure-prenumerationen för att lista de användartilldelade hanterade identiteterna.
2. Skriv *Hanterade identiteter i*sökrutan och klicka på **Hanterade identiteter**under Tjänster .
3. En lista över användartilldelade hanterade identiteter för din prenumeration returneras.  Om du vill visa information om en användartilldelad hanterad identitet klickar du på dess namn.

![Lista användartilldelade hanterade identitet](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>Ta bort en användartilldelad hanterad identitet

Om du vill ta bort en användartilldelad hanterad identitet behöver kontot rolltilldelningen [Managed Identity Contributor.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

Om du tar bort en användartilldelad identitet tas den inte bort från den virtuella dator eller resurs som den tilldelats.  Om du vill ta bort den tilldelade användarens identitet från en virtuell dator finns [ta bort en användartilldelad hanterad identitet från en virtuell dator](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#remove-a-user-assigned-managed-identity-from-a-vm).

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett konto som är associerat med Azure-prenumerationen för att ta bort en användartilldelad hanterad identitet.
2. Markera den användartilldelade hanterade identiteten och klicka på **Ta bort**.
3. Under bekräftelserutan väljer du **Ja**.

![Ta bort användartilldelade hanterade identitet](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>Tilldela en roll till en användartilldelad hanterad identitet 

Om du vill tilldela en roll till en användartilldelad hanterad identitet behöver ditt konto rolltilldelningen [för administratörsadministratören](/azure/role-based-access-control/built-in-roles#user-access-administrator) för användare.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett konto som är associerat med Azure-prenumerationen för att lista de användartilldelade hanterade identiteterna.
2. Skriv *Hanterade identiteter i*sökrutan och klicka på **Hanterade identiteter**under Tjänster .
3. En lista över användartilldelade hanterade identiteter för din prenumeration returneras.  Välj den användartilldelade hanterade identitet som du vill tilldela en roll.
4. Välj **Åtkomstkontroll (IAM)** och välj sedan **Lägg till rolltilldelning**.

   ![Användartilldelade hanterade identitetsstart](./media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

5. Konfigurera följande värden i bladet Lägg till rolltilldelning och klicka sedan på **Spara:**
   - **Roll** - den roll att tilldela
   - **Tilldela åtkomst till** - resursen att tilldela den användartilldelade hanterade identiteten
   - **Välj** - den medlem som ska tilldelas åtkomst
   
   ![Användartilldelade hanterade identitet IAM](./media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)  
