---
title: Hantera en användare som tilldelats en hanterad identitet i Azure Portal – Azure AD
description: Steg för steg-anvisningar om hur du skapar, visar, tar bort och tilldelar en roll till en användardefinierad hanterad identitet.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79244139"
---
# <a name="create-list-delete-or-assign-a-role-to-a-user-assigned-managed-identity-using-the-azure-portal"></a>Skapa, Visa, ta bort eller tilldela en roll till en användare som tilldelats en hanterad identitet med hjälp av Azure Portal

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till tjänster som stöder Azure AD-autentisering, utan att behöva ange autentiseringsuppgifter i koden. 

I den här artikeln får du lära dig hur du skapar, visar, tar bort eller tilldelar en roll till en användardefinierad hanterad identitet med hjälp av Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [avsnittet Översikt](overview.md). **Se till att granska [skillnaden mellan en tilldelad och användardefinierad hanterad identitet](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

## <a name="create-a-user-assigned-managed-identity"></a>Skapa en användartilldelad hanterad identitet

För att skapa en användardefinierad hanterad identitet måste ditt konto ha roll tilldelningen [hanterad identitets deltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto som är kopplat till Azure-prenumerationen för att skapa den tilldelade hanterade identiteten.
2. I rutan Sök skriver du *hanterade identiteter*och under **tjänster**klickar du på **hanterade identiteter**.
3. Klicka på **Lägg till** och ange värden i följande fält under **skapa användare tilldelade hanterade** identitets fönster:
   - **Resurs namn**: det här är namnet på den användar tilldelnings hanterade identiteten, till exempel UAI1.
   - **Prenumeration**: Välj den prenumeration som du vill skapa den användarspecifika hanterade identiteten under
   - **Resurs grupp**: skapa en ny resurs grupp som innehåller din användarspecifika hanterade identitet eller Välj **Använd befintlig** för att skapa den tilldelade hanterade identiteten i en befintlig resurs grupp.
   - **Plats**: Välj en plats där du vill distribuera den tilldelade hanterade identiteten, till exempel **västra USA**.
4. Klicka på **Skapa**.

![Skapa en användartilldelad hanterad identitet](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)

## <a name="list-user-assigned-managed-identities"></a>Lista användare-tilldelade hanterade identiteter

Om du vill visa en lista över/läsa en användardefinierad hanterad identitet måste ditt konto ha roll tilldelningen [hanterad identitet](/azure/role-based-access-control/built-in-roles#managed-identity-operator) eller [hanterad identitets deltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto som är associerat med Azure-prenumerationen för att visa en lista över användare som tilldelats hanterade identiteter.
2. I rutan Sök skriver du *hanterade identiteter*och under tjänster klickar du på **hanterade identiteter**.
3. En lista över de användare tilldelade hanterade identiteterna för din prenumeration returneras.  Om du vill se information om en användardefinierad hanterad identitet klickar du på dess namn.

![Lista användare-tilldelad hanterad identitet](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>Ta bort en användare som tilldelats en hanterad identitet

För att ta bort en användardefinierad hanterad identitet måste ditt konto ha roll tilldelningen [hanterad identitets deltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

Om du tar bort en tilldelad identitet tas den inte bort från den virtuella datorn eller resursen som den tilldelades.  Ta bort den användare som tilldelats identiteten från en virtuell dator genom att [ta bort en användardefinierad hanterad identitet från en virtuell dator](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#remove-a-user-assigned-managed-identity-from-a-vm).

1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto som är kopplat till Azure-prenumerationen för att ta bort en tilldelad hanterad identitet.
2. Välj den användare-tilldelade hanterade identiteten och klicka på **ta bort**.
3. Välj **Ja**under bekräftelse rutan.

![Ta bort användardefinierad hanterad identitet](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>Tilldela en roll till en användare som tilldelats en hanterad identitet 

För att tilldela en roll till en användardefinierad hanterad identitet måste ditt konto ha roll tilldelningen [administratör för användar åtkomst](/azure/role-based-access-control/built-in-roles#user-access-administrator) .

1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto som är associerat med Azure-prenumerationen för att visa en lista över användare som tilldelats hanterade identiteter.
2. I rutan Sök skriver du *hanterade identiteter*och under tjänster klickar du på **hanterade identiteter**.
3. En lista över de användare tilldelade hanterade identiteterna för din prenumeration returneras.  Välj den användar tilldelnings hanterade identitet som du vill tilldela en roll.
4. Välj **åtkomst kontroll (IAM)** och välj sedan **Lägg till roll tilldelning**.

   ![Användare som tilldelats hanterad identitets start](./media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

5. Konfigurera följande värden i bladet Lägg till roll tilldelning och klicka sedan på **Spara**:
   - **Roll** – rollen som ska tilldelas
   - **Tilldela åtkomst till** -resursen för att tilldela den användare tilldelade hanterade identiteten
   - **Välj** -medlemmen att tilldela åtkomst
   
   ![Användarens tilldelade hanterade identitet-IAM](./media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)  
