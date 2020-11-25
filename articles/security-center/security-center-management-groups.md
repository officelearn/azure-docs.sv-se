---
title: Få överblick över hela organisationen för Azure Security Center | Microsoft Docs
description: Den här artikeln förklarar hur du hanterar din säkerhets position i stor skala genom att tillämpa principer för alla prenumerationer som är kopplade till din Azure Active Directory-klient.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2018
ms.author: memildin
ms.openlocfilehash: 7252a6ccd77212f75f5db54e5f3fcad7aa2df50a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013810"
---
# <a name="gain-tenant-wide-visibility-for-azure-security-center"></a>Få överblick över hela organisationen för Azure Security Center
Den här artikeln förklarar hur du hanterar din organisations säkerhets position i stor skala genom att använda säkerhets principer för alla Azure-prenumerationer som är kopplade till din Azure Active Directory-klient.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="management-groups"></a>Hanteringsgrupper
Med Azures hanterings grupper kan du effektivt hantera åtkomst, principer och rapportering av prenumerations grupper, samt hantera hela Azure-egendomen på ett effektivt sätt genom att utföra åtgärder på rot hanterings gruppen. Varje Azure AD-klient får en enda toppnivå hanterings grupp som kallas för rot hanterings gruppen. Rothanteringsgruppen är inbyggd i hierarkin så att alla hanteringsgrupper och prenumerationer är dess underordnade element. Med den här gruppen kan globala principer och Azure Role-tilldelningar tillämpas på katalog nivå. 

Rot hanterings gruppen skapas automatiskt när du gör någon av följande åtgärder: 
1. Välj att använda Azure-hanterings grupper genom att navigera till **hanteringsgrupper** i [Azure Portal](https://portal.azure.com).
2. Skapa en hanterings grupp via ett API-anrop.
3. Skapa en hanterings grupp med PowerShell.

En detaljerad översikt över hanterings grupper finns i artikeln [organisera dina resurser med Azures hanterings grupper](../governance/management-groups/overview.md) .

## <a name="create-a-management-group-in-the-azure-portal"></a>Skapa en hanterings grupp i Azure Portal
Du kan organisera prenumerationer i hanterings grupper och tillämpa dina styrnings principer i hanterings grupperna. Alla prenumerationer i en hanteringsgrupp ärver automatiskt de principer som tillämpas på hanteringsgruppen. Även om hanterings grupper inte krävs för att publicera Security Center, rekommenderar vi starkt att du skapar minst en hanterings grupp så att rot hanterings gruppen skapas. När gruppen har skapats kommer alla prenumerationer i Azure AD-klienten att länkas till den. Instruktioner för PowerShell och mer information finns i [skapa hanterings grupper för resurs-och organisations hantering](../governance/management-groups/create-management-group-portal.md).

 
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **alla tjänst**  >  **hanterings grupper**.
3. På huvud sidan väljer du **ny hanterings grupp.** 

    ![Huvud grupp](./media/security-center-management-groups/main.png) 
4.  Fyll i fältet hanterings grupp-ID. 
    - **Hanterings gruppens ID** är katalogens unika identifierare som används för att skicka kommandon i den här hanterings gruppen. Den här identifieraren kan inte redige ras när den används i hela Azure-systemet för att identifiera den här gruppen. 
    - Fältet visnings namn är det namn som visas i Azure Portal. Ett separat visnings namn är ett valfritt fält när du skapar hanterings gruppen och kan ändras när som helst.  

      ![Skapa](./media/security-center-management-groups/create_context_menu.png)  
5.  Välj **Spara**

### <a name="view-management-groups-in-the-azure-portal"></a>Visa hanterings grupper i Azure Portal
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Om du vill visa hanterings grupper väljer du **alla tjänster** på huvud menyn i Azure.
3. Under **Allmänt** väljer du **hanteringsgrupper**.

    ![Skapa en hanteringsgrupp](./media/security-center-management-groups/all-services.png)

## <a name="grant-tenant-level-visibility-and-the-ability-to-assign-policies"></a>Bevilja synlighet på klient nivå och möjlighet att tilldela principer

För att få insyn i säkerhets position för alla prenumerationer som registrerats i Azure AD-klienten, måste en Azure-roll med tillräcklig Läs behörighet tilldelas till rot hanterings gruppen.

### <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Öka åtkomsten för en global administratör i Azure Active Directory
En Azure Active Directory klient organisations administratör har inte direkt åtkomst till Azure-prenumerationer. Men som katalog administratör har de rätt att höja sig själva till en roll som har åtkomst. En Azure AD-innehavaradministratör måste utökas till användarens åtkomst administratör på rot hanterings gruppens nivå så att de kan tilldela Azure-roller. PowerShell-instruktioner och ytterligare information finns [i öka åtkomsten för en global administratör i Azure Active Directory](../role-based-access-control/elevate-access-global-admin.md). 


1. Logga in på [Azure Portal](https://portal.azure.com) eller [Azure Active Directory administrations Center](https://aad.portal.azure.com).

2. Klicka på **Azure Active Directory** i navigerings listan och klicka sedan på **Egenskaper**.

   ![Egenskaper för Azure AD – skärm bild](./media/security-center-management-groups/aad-properties.png)

3. Under **åtkomst hantering för Azure-resurser** ställer du in växeln på **Ja**.

   ![Åtkomst hantering för Azure-resurser – skärm bild](./media/security-center-management-groups/aad-properties-global-admin-setting.png)

   - När du ställer in växeln på Ja tilldelas du rollen administratör för användar åtkomst i Azure RBAC i rot omfånget (/). Detta ger dig behörighet att tilldela roller i alla Azure-prenumerationer och hanterings grupper som är kopplade till den här Azure AD-katalogen. Den här växeln är bara tillgänglig för användare som har tilldelats rollen som global administratör i Azure AD.

   - När du ställer in växeln på Nej tas rollen administratör för användar åtkomst i Azure RBAC bort från ditt användar konto. Du kan inte längre tilldela roller i alla Azure-prenumerationer och hanterings grupper som är associerade med den här Azure AD-katalogen. Du kan bara visa och hantera de Azure-prenumerationer och hanterings grupper som du har beviljats åtkomst till.

4. Spara inställningen genom att klicka på **Spara** .

    - Den här inställningen är inte en global egenskap och gäller endast för den inloggade användaren.

5. Utför de uppgifter du behöver för att göra den utökade åtkomsten. När du är klar väljer du växla tillbaka till **Nej**.


### <a name="assign-azure-roles-to-users"></a>Tilldela Azure-roller till användare
För att få insyn i alla prenumerationer måste klient organisations administratörerna tilldela rätt Azure-roll till alla användare som de vill bevilja en synlig klient för närvarande, även på rot hanterings gruppens nivå. De rekommenderade rollerna som ska tilldelas är antingen **säkerhets administratör** eller **säkerhets läsare**. I allmänhet krävs rollen som säkerhets administratör för att tillämpa principer på rotnivån, medan säkerhets läsaren räcker för att tillhandahålla synligheten på klient nivå. Mer information om de behörigheter som har beviljats av de här rollerna finns i beskrivningen av den [inbyggda rollen säkerhets administratör](../role-based-access-control/built-in-roles.md#security-admin) eller den [inbyggda rollen säkerhets läsare](../role-based-access-control/built-in-roles.md#security-reader).


#### <a name="assign-azure-roles-to-users-through-the-azure-portal"></a>Tilldela Azure-roller till användare via Azure Portal: 

1. Logga in på [Azure-portalen](https://portal.azure.com). 
1. Om du vill visa hanterings grupper väljer du **alla tjänster** på huvud menyn i Azure och väljer sedan **hanteringsgrupper**.
1.  Välj en hanterings grupp och klicka på **information**.

    ![Skärm bild för Hanteringsgrupper information](./media/security-center-management-groups/management-group-details.PNG)
 
1. Klicka på **åtkomst kontroll (IAM)** och sedan **roll tilldelningar**.

1. Klicka på **Lägg till roll tilldelning**.

1. Välj den roll som ska tilldelas och användaren och klicka sedan på **Spara**.  
   
   ![Skärm bild för Lägg till säkerhets läsar roll](./media/security-center-management-groups/asc-security-reader.png)


#### <a name="assign-azure-roles-to-users-with-powershell"></a>Tilldela Azure-roller till användare med PowerShell: 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Installera [Azure PowerShell](/powershell/azure/install-az-ps).
2. Kör följande kommandon: 

    ```azurepowershell
    # Login to Azure as a Global Administrator user
    Connect-AzAccount
    ```

3. Logga in med autentiseringsuppgifter för global administratör när du uppmanas till det. 

    ![Skärm bild för inloggnings varning](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. Ge läsar roll behörighet genom att köra följande kommando:

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. Om du vill ta bort rollen använder du följande kommando: 

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

### <a name="open-or-refresh-security-center"></a>Öppna eller uppdatera Security Center
När du har förhöjd åtkomst, öppna eller uppdatera Azure Security Center för att kontrol lera att du har insyn i alla prenumerationer under Azure AD-klienten. 

1. Logga in på [Azure-portalen](https://portal.azure.com). 
2. Se till att du väljer alla prenumerationer i prenumerations väljaren som du vill visa i Security Center.

    ![Skärm bild för prenumerations väljare](./media/security-center-management-groups/subscription-selector.png)

1. Välj **alla tjänster** på huvud menyn i Azure och välj sedan **Security Center**.
2. I **översikten** finns ett prenumerations täcknings diagram.

    ![Skärm bild av prenumerationens täcknings diagram](./media/security-center-management-groups/security-center-subscription-coverage.png)

3. Klicka på **disponering** för att se en lista över prenumerationer som omfattas. 

    ![Skärm bild av prenumerations täcknings lista](./media/security-center-management-groups/security-center-coverage.png)

### <a name="remove-elevated-access"></a>Ta bort utökad åtkomst 
När Azure-rollerna har tilldelats till användarna bör klient administratören ta bort sig själv från rollen som administratör för användar åtkomst.

1. Logga in på [Azure Portal](https://portal.azure.com) eller [Azure Active Directory administrations Center](https://aad.portal.azure.com).

2. Klicka på **Azure Active Directory** i navigerings listan och klicka sedan på **Egenskaper**.

3. Under **åtkomst hantering för Azure-resurser** anger du växeln till **Nej**.

4. Spara inställningen genom att klicka på **Spara** .



## <a name="adding-subscriptions-to-a-management-group"></a>Lägga till prenumerationer i en hanterings grupp
Du kan lägga till prenumerationer i hanterings gruppen som du skapade. De här stegen är inte obligatoriska för att få insyn i hela klienten och global princip-och åtkomst hantering.

1. Under **hanteringsgrupper** väljer du en hanterings grupp som du vill lägga till din prenumeration i.

    ![Välj en hanterings grupp för att lägga till prenumerationen](./media/security-center-management-groups/management-group-subscriptions.png)

2. Välj **Lägg till befintlig**.

    ![Lägg till befintlig](./media/security-center-management-groups/add-existing.png)

3. Ange prenumeration under **Lägg till befintlig resurs** och klicka på **Spara**.

4. Upprepa steg 1 till 3 tills du har lagt till alla prenumerationer i omfånget.

   > [!NOTE]
   > Hanterings grupper kan innehålla både prenumerationer och underordnade hanterings grupper. När du tilldelar en användare en Azure-roll till den överordnade hanterings gruppen ärvs åtkomsten av prenumerationerna på den underordnade hanterings gruppen. Principer som anges i den överordnade hanterings gruppen ärvs också av de underordnade. 

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du får insyn i hela organisationen för Azure Security Center. I följande artiklar kan du lära dig mer om Security Center:

> [!div class="nextstepaction"]
> [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md)

> [!div class="nextstepaction"]
> [Hantera och besvara säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md)