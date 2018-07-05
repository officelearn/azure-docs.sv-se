---
title: Få insyn i klienttäckande för Azure Security Center | Microsoft Docs
description: Läs mer om att få klienttäckande insyn i Azure Security Center.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/22/2018
ms.author: terrylan
ms.openlocfilehash: 05504f94d72f39762672d0e79669dd50d8e6435e
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441523"
---
# <a name="gain-tenant-wide-visibility-for-azure-security-center"></a>Få insyn i klienttäckande för Azure Security Center
Den här artikeln hjälper dig att komma igång genom att göra flera åtgärder och maximerar fördelarna Azure Security Center tillhandahåller. Utför dessa åtgärder kan du få insyn i alla Azure-prenumerationer som är länkade till din Azure Active Directory-klient och effektivt sätt hantera din organisations säkerhetsposition i stor skala genom att tillämpa säkerhetsprinciper på flera prenumerationer på ett aggregative sätt.

## <a name="management-groups"></a>Hanteringsgrupper
Azure-hanteringsgrupper ger möjlighet att effektivt hantera åtkomst, principer och rapportering på grupper av prenumerationer samt hantera effektivt hela Azure-innehåll genom att utföra åtgärder på rot-hanteringsgruppen. Varje Azure AD-klient får en enskild översta hanteringsgrupp som kallas rot-hanteringsgruppen. Rothanteringsgruppen är inbyggd i hierarkin så att alla hanteringsgrupper och prenumerationer är dess underordnade element. Den här gruppen kan globala principer och RBAC-uppgifter som ska tillämpas på nivån för katalogen. 

Rot-hanteringsgruppen skapas automatiskt när du gör något av följande åtgärder: 
1. Välja för att använda Azure-hanteringsgrupper genom att gå till **Hanteringsgrupper** i den [Azure-portalen](https://portal.azure.com).
2. Skapa en hanteringsgrupp via ett API-anrop.
3. Skapa en hanteringsgrupp med PowerShell.

En detaljerad översikt över hanteringsgrupper finns i den [organisera dina resurser med Azure-hanteringsgrupper](../azure-resource-manager/management-groups-overview.md) artikeln.

## <a name="create-a-management-group-in-the-azure-portal"></a>Skapa en hanteringsgrupp i Azure portal
Du kan ordna prenumerationer till hanteringsgrupper och tillämpa styrningsprinciper för av hanteringsgrupper. Alla prenumerationer i en hanteringsgrupp ärver automatiskt de principer som tillämpas på hanteringsgruppen. Medan hanteringsgrupper inte behövs för att integrera Security Center, rekommenderar vi starkt att du skapar minst en hanteringsgrupp så hanteringsgruppen rot har skapats. När gruppen har skapats, kommer alla prenumerationer i Azure AD-klienten att länkas till den. Anvisningar för PowerShell och mer information finns i [skapa hanteringsgrupper för resurs- och organisation](../azure-resource-manager/management-groups-create.md).

 
1. Logga in på [Azure-portalen](http://portal.azure.com).
2. Välj **alla tjänster** > **hanteringsgrupper**.
3. Välj på huvudsidan **ny hanteringsgrupp.** 

    ![HUVUDGRUPP](./media/security-center-management-groups/main.png) 
4.  Fyll i hantering av grupp-ID-fältet. 
    - Den **Hanteringsgruppens ID** är den unika identifieraren för katalogen som används för att skicka kommandon på den här hanteringsgruppen. Den här identifieraren är inte redigerbara när du har skapat eftersom den används i hela Azure-systemet för att identifiera den här gruppen. 
    - Visningsnamnet är det namn som visas i Azure-portalen. Ett separat visningsnamn är ett valfritt fält när du skapar hanteringen gruppen och kan ändras när som helst.  

      ![Skapa](./media/security-center-management-groups/create_context_menu.png)  
5.  Välj **spara**

### <a name="view-management-groups-in-the-azure-portal"></a>Visa hantering av grupper i Azure portal
1. Logga in på den [Azure-portalen](http://portal.azure.com).
2. Om du vill visa hanteringsgrupper, Välj **alla tjänster** huvudmenyn i Azure.
3. Under **Allmänt**väljer **Hanteringsgrupper**.

    ![Skapa en hanteringsgrupp](./media/security-center-management-groups/all-services.png)

## <a name="grant-tenant-level-visibility-and-the-ability-to-assign-policies"></a>Bevilja på klientnivå synlighet och möjligheten att tilldela principer

Om du vill få en överblick över säkerhetstillståndet för alla prenumerationer som är registrerade i Azure AD-klient, krävs en RBAC-roll med tillräckligt med läsbehörigheter tilldelas på rot-hanteringsgruppen.

### <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Utöka behörighet för global administratör i Azure Active Directory
En Azure Active Directory-klientadministratör har inte direkt åtkomst till Azure-prenumerationer. De kan dock har rätt att höja sig själva till en roll som har åtkomst som en directory-administratör. En administratör för Azure AD-klient måste höja sig själv till administratör för användaråtkomst på rotnivå för management-grupp så att de kan tilldela RBAC-roller. PowerShell-instruktioner och mer information finns i [utöka behörighet för Global administratör i Azure Active Directory](../role-based-access-control/elevate-access-global-admin.md). 


1. Logga in på den [Azure-portalen](https://portal.azure.com) eller [Azure Active Directory Administrationscenter](https://aad.portal.azure.com).

2. I listan över navigeringsfönstret klickar du på **Azure Active Directory** och klicka sedan på **egenskaper**.

   ![Azure AD-egenskaper – skärmbild](./media/security-center-management-groups/aad-properties.png)

3. Under **Global administratör kan hantera Azure-prenumerationer och Hanteringsgrupper**, inställd växeln **Ja**.

   ![Global administratör kan hantera Azure-prenumerationer och Hanteringsgrupper – skärmbild](./media/security-center-management-groups/aad-properties-global-admin-setting.png)

   - Om du anger växeln till **Ja**, ditt globala administratörskonto (för tillfället inloggad användare) läggs till rollen Administratör för användaråtkomst i Azure RBAC vid rotscopet (`/`), vilket ger dig tillgång till Visa och rapportera på alla Azure-prenumerationer som är associerade med Azure AD-klienten.

   - Om du anger växeln till **nr**, ditt globala administratörskonto (för tillfället inloggad användare) tas bort från rollen Administratör för användaråtkomst i Azure RBAC. Du kan inte se alla Azure-prenumerationer som är associerade med Azure AD-klient, och du kan visa och hantera endast Azure-prenumerationer som du har beviljats åtkomst.

4. Klicka på **spara** att spara dina inställningar.

    - Den här inställningen gäller enbart för den inloggade användaren är inte en global egenskap.

5. Utföra uppgifter som du behöver göra på den utökade behörigheten. Ange växeln när du är klar, tillbaka till **nr**.

### <a name="assign-rbac-roles-to-users"></a>Tilldela RBAC-roller till användare
När en Innehavaradministratör förhöjd åtkomst, kan de tilldela RBAC-roll för relevanta användarna på rotnivå för management-grupp. Den rekommenderade rollen tilldelas är [ **läsare**](../role-based-access-control/built-in-roles.md#reader). Den här rollen krävs för att tillhandahålla på klientnivå synlighet. Den tilldelade rollen överförs automatiskt till alla hanteringsgrupper och -prenumerationer under rot-hanteringsgruppen. Mer information om RBAC-roller finns i [tillgängliga roller](../active-directory/users-groups-roles/directory-assign-admin-roles.md#available-roles).

1. [Installera Azure PowerShell](/powershell/azure/install-azurerm-ps).
2. Kör följande kommandon: 

    ```azurepowershell
    # Install Management Groups Powershell module
    Install-Module AzureRM.Resources
    
    # Login to Azure as a Global Administrator user
    Login-AzureRmAccount
    ```

3. När du uppmanas logga in med autentiseringsuppgifter som global administratör. 

    ![Logga in fråga skärmbild](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. Bevilja läsare behörigheter genom att köra följande kommando:

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzureRmRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. Om du vill ta bort rollen, använder du följande kommando: 

    ```azurepowershell
    Remove-AzureRmRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

<!-- Currently, PowerShell method only 6/26/18

1. Sign in to the [Azure portal](https://portal.azure.com). 
2. To view management groups, select **All services** under the Azure main menu then select **Management Groups**.
3.  Select a management group and click **details**.

    ![Management Groups details screenshot](./media/security-center-management-groups/management-group-details.PNG)
 
4. Click **Access control (IAM)** then **Add**.
5. Select the role to assign and the user, then click **Save**.  
   
   ![Add Security Reader role screenshot](./media/security-center-management-groups/asc-security-reader.png)
-->

### <a name="remove-elevated-access"></a>Ta bort utökad åtkomst 
När RBAC-roller har tilldelats till användarna, klientadministratören ska ta bort sig själv från administratörsrollen för användaråtkomst.

1. Logga in på den [Azure-portalen](https://portal.azure.com) eller [Azure Active Directory Administrationscenter](https://aad.portal.azure.com).

2. I listan över navigeringsfönstret klickar du på **Azure Active Directory** och klicka sedan på **egenskaper**.

3. Under **Global administratör kan hantera Azure-prenumerationer och Hanteringsgrupper**, inställd växeln **nr**.

4. Klicka på **spara** att spara dina inställningar.

### <a name="open-or-refresh-security-center"></a>Öppnar eller uppdaterar Security Center
När du har tilldelat RBAC-roller öppnar eller uppdaterar Azure Security Center för att kontrollera att du har insyn i alla prenumerationer under Azure AD-klienten. 

1. Logga in på [Azure Portal](https://portal.azure.com). 
2. Välj **alla tjänster** under Azure-huvudmenyn väljer **Security Center**.
3. I den **översikt**, det finns ett diagram för täckning av prenumerationen. 
    ![Prenumeration täckning diagrammet skärmbild](./media/security-center-management-groups/security-center-subscription-coverage.png)
4. Klicka på **täckning** att se listan över prenumerationer som omfattas. 
    ![Prenumeration täckning lista skärmbild](./media/security-center-management-groups/security-center-coverage.png)

## <a name="adding-subscriptions-to-a-management-groups"></a>Lägger till prenumerationer för en hanteringsgrupper
Du kan lägga till prenumerationer till den hanteringsgrupp som du skapade. De här stegen är inte obligatoriska för att få klienttäckande synlighet och global princip- och åtkomsthantering.

1. Under **Hanteringsgrupper**, Välj en hanteringsgrupp för att lägga till din prenumeration.

    ![Välj en hanteringsgrupp för att lägga till prenumeration](./media/security-center-management-groups/management-group-subscriptions.png)

2. Välj **Lägg till befintlig**.

    ![Lägg till befintlig](./media/security-center-management-groups/add-existing.png)

3. Ange prenumeration under **lägga till befintliga resurs** och klicka på **spara**.

4. Upprepa steg 1 till 3 tills du har lagt till alla prenumerationer i omfånget.

 > [!NOTE]
 > Av hanteringsgrupper kan innehålla både prenumerationer och underordnade hanteringsgrupper. När du tilldelar en användare en RBAC-roll till den överordnade gruppen för hantering av ärvs åtkomst av underordnade hanteringsgruppen prenumerationer. Principer som angetts på överordnad hanteringsgrupp är också ärvs av underordnade. 

## <a name="next-steps"></a>Nästa steg
I den här artikeln lärde du dig att få insyn i klienttäckande för Azure Security Center. I följande artiklar kan du lära dig mer om Security Center:

> [!div class="nextstepaction"]
> [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md)

> [!div class="nextstepaction"]
> [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md)

