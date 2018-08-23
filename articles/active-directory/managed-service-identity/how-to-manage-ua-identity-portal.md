---
title: Så här hanterar du en användare tilldelas hanterad identitet med hjälp av Azure portal
description: Steg för steg tilldelas instruktioner om hur du skapar, lista och ta bort en användare hanterad identitet.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: bd6327aa5c16d57c550025667659f9245a5b0b65
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "42061516"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-the-azure-portal"></a>Skapa, visa eller ta bort en Användartilldelad identitet med hjälp av Azure portal

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Hanterad tjänstidentitet ger Azure-tjänster med en hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till tjänster som stöder Azure AD-autentisering, utan att behöva autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig skapa, visa och ta bort en Användartilldelad identitet med hjälp av Azure-portalen.

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterad tjänstidentitet kan ta en titt på [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan en systemtilldelad och Användartilldelad identitet](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Ditt konto måste följande rolltilldelningar för att utföra vilka hanteringsåtgärder i den här artikeln:
    - [Hanterad Identitetsdeltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) rollen kan skapa, läsa (lista), uppdatera och ta bort en Användartilldelad identitet.
    - [Hanterade Identitetsoperatör](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roll att läsa (lista) egenskaperna för en Användartilldelad identitet.

## <a name="create-a-user-assigned-managed-identity"></a>Skapa en användare som tilldelats hanterad identitet

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumeration för att skapa användaren tilldelas hanterad identitet.
2. I sökrutan skriver *hanterade identiteter*, och under **Services**, klickar du på **hanterade identiteter**.
3. Klicka på **Lägg till** och ange värden i följande fält under **skapa användartilldelade hanteras** identity-fönstret:
   - **Resursnamnet**: Detta är namnet för dina användare som tilldelats hanterade identitet, till exempel UAI1.
   - **Prenumeration**: Välj prenumerationen för att skapa användaren som har tilldelats hanterad identitet under
   - **Resursgrupp**: skapa en ny resursgrupp för att innehålla din Användartilldelad identitet eller välj **Använd befintlig** att skapa användaren tilldelas hanterad identitet i en befintlig resursgrupp.
   - **Plats**: Välj en plats för att distribuera användaren som har tilldelats hanterad identitet, till exempel **västra USA**.
4. Klicka på **Skapa**.

![Skapa en användare som tilldelats hanterad identitet](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)

## <a name="list-user-assigned-identities"></a>Lista användartilldelade identiteter

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumerationen till användaren tilldelas hanterade identiteter.
2. I sökrutan skriver *hanterade identiteter*, och under tjänster klickar du på **hanterade identiteter**.
3. En lista över hanterade identiteter för din prenumeration för användartilldelade returneras.  Om du vill se information om en användare som tilldelats på dess namn.

![Lista över användare som tilldelats hanterad identitet](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-identity"></a>Ta bort Användartilldelad identitet

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är associerade med Azure-prenumeration att ta bort en användare tilldelas hanterad identitet.
2. Välj Användartilldelad identitet och klicka på **ta bort**.
3. Under dialogrutan väljer du, **Ja**.

![Ta bort användartilldelad hanterad identitet](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)