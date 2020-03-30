---
title: Få klientomfattande synlighet för Azure Security Center | Microsoft-dokument
description: I den här artikeln beskrivs hur du hanterar din säkerhetsposition i stor skala genom att tillämpa principer på alla prenumerationer som är länkade till din Azure Active Directory-klientorganisation.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2018
ms.author: memildin
ms.openlocfilehash: 734876380d22f5d4d6dae0dd375b238fd5f6ffed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74559351"
---
# <a name="gain-tenant-wide-visibility-for-azure-security-center"></a>Få klientomfattande synlighet för Azure Security Center
I den här artikeln beskrivs hur du hanterar organisationens säkerhetsposition i stor skala genom att tillämpa säkerhetsprinciper på alla Azure-prenumerationer som är länkade till din Azure Active Directory-klientorganisation.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="management-groups"></a>Hanteringsgrupper
Azure-hanteringsgrupper ger möjlighet att effektivt hantera åtkomst, principer och rapportering om grupper av prenumerationer, samt effektivt hantera hela Azure-egendomen genom att utföra åtgärder i rothanteringsgruppen. Varje Azure AD-klient får en enda hanteringsgrupp på den högsta nivån som kallas rothanteringsgruppen. Rothanteringsgruppen är inbyggd i hierarkin så att alla hanteringsgrupper och prenumerationer är dess underordnade element. Med den här gruppen kan globala principer och RBAC-tilldelningar tillämpas på katalognivå. 

Rothanteringsgruppen skapas automatiskt när du utför någon av följande åtgärder: 
1. Välj att använda Azure-hanteringsgrupper genom att navigera till **hanteringsgrupper** i [Azure-portalen](https://portal.azure.com).
2. Skapa en hanteringsgrupp via ett API-anrop.
3. Skapa en hanteringsgrupp med PowerShell.

En detaljerad översikt över hanteringsgrupper finns i artikeln [Ordna dina resurser med Azure-hanteringsgrupper.](../azure-resource-manager/management-groups-overview.md)

## <a name="create-a-management-group-in-the-azure-portal"></a>Skapa en hanteringsgrupp i Azure-portalen
Du kan ordna prenumerationer i hanteringsgrupper och tillämpa styrningsprinciperna på hanteringsgrupperna. Alla prenumerationer i en hanteringsgrupp ärver automatiskt de principer som tillämpas på hanteringsgruppen. Hanteringsgrupper behöver inte vara inbyggda i Security Center, men vi rekommenderar starkt att du skapar minst en hanteringsgrupp så att rothanteringsgruppen skapas. När gruppen har skapats länkas alla prenumerationer under din Azure AD-klient till den. Instruktioner för PowerShell och mer information finns i [Skapa hanteringsgrupper för resurs- och organisationshantering](../azure-resource-manager/management-groups-create.md).

 
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj Alla > **tjänsthanteringsgrupper**. **All services**
3. Välj **Ny hanteringsgrupp** på huvudsidan. 

    ![Huvudgrupp](./media/security-center-management-groups/main.png) 
4.  Fyll i fältet hanteringsgrupp-ID. 
    - **Hanteringsgrupp-ID** är den katalogunika identifierare som används för att skicka kommandon i den här hanteringsgruppen. Den här identifieraren kan inte redigeras när den har skapats eftersom den används i hela Azure-systemet för att identifiera den här gruppen. 
    - Fältet visningsnamn är det namn som visas i Azure-portalen. Ett separat visningsnamn är ett valfritt fält när hanteringsgruppen skapas och kan ändras när som helst.  

      ![Skapa](./media/security-center-management-groups/create_context_menu.png)  
5.  Välj **Spara**

### <a name="view-management-groups-in-the-azure-portal"></a>Visa hanteringsgrupper i Azure-portalen
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Om du vill visa hanteringsgrupper väljer du **Alla tjänster** under Huvudmenyn i Azure.
3. Under **Allmänt**väljer du **Hanteringsgrupper**.

    ![Skapa en hanteringsgrupp](./media/security-center-management-groups/all-services.png)

## <a name="grant-tenant-level-visibility-and-the-ability-to-assign-policies"></a>Bevilja synlighet på klientnivå och möjlighet att tilldela principer

För att få insyn i säkerhetspositionen för alla prenumerationer som är registrerade i Azure AD-klienten måste en RBAC-roll med tillräcklig läsbehörighet tilldelas i rothanteringsgruppen.

### <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Öka åtkomsten för en global administratör i Azure Active Directory
En Azure Active Directory-klientadministratör har inte direkt åtkomst till Azure-prenumerationer. Men som katalogadministratör har de rätt att höja sig till en roll som har åtkomst. En Azure AD-klientadministratör måste höja sig till användaråtkomstadministratör på rothanteringsgruppsnivå så att de kan tilldela RBAC-roller. Instruktioner för PowerShell och ytterligare information finns i [Öka åtkomsten för en global administratör i Azure Active Directory](../role-based-access-control/elevate-access-global-admin.md). 


1. Logga in på [Azure-portalen](https://portal.azure.com) eller [Administrationscentret](https://aad.portal.azure.com)för Azure Active Directory .

2. Klicka på Azure **Active Directory** i navigeringslistan och klicka sedan på **Egenskaper**.

   ![Azure AD-egenskaper - skärmdump](./media/security-center-management-groups/aad-properties.png)

3. Under **Åtkomsthantering för Azure-resurser**anger du växeln till **Ja**.

   ![Global administratör kan hantera Azure-prenumerationer och hanteringsgrupper – skärmbild](./media/security-center-management-groups/aad-properties-global-admin-setting.png)

   - När du ställer in växeln till Ja tilldelas du rollen Administratör för användaråtkomst i Azure RBAC i rotomfattningen (/). Detta ger dig behörighet att tilldela roller i alla Azure-prenumerationer och hanteringsgrupper som är associerade med den här Azure AD-katalogen. Den här växeln är endast tillgänglig för användare som har tilldelats rollen Global administratör i Azure AD.

   - När du anger växeln till Nej tas rollen Administratör för användaråtkomst i Azure RBAC bort från ditt användarkonto. Du kan inte längre tilldela roller i alla Azure-prenumerationer och hanteringsgrupper som är associerade med den här Azure AD-katalogen. Du kan bara visa och hantera de Azure-prenumerationer och hanteringsgrupper som du har beviljats åtkomst till.

4. Klicka på **Spara** om du vill spara inställningen.

    - Den här inställningen är inte en global egenskap och gäller endast för den användare som för närvarande är inloggad.

5. Utför de uppgifter du behöver göra vid den förhöjda åtkomsten. När du är klar ställer du tillbaka växeln till **Nej**.


### <a name="assign-rbac-roles-to-users"></a>Tilldela RBAC-roller till användare
För att få synlighet för alla prenumerationer måste klientadministratörer tilldela lämplig RBAC-roll till alla användare som de vill ge klientomfattande synlighet, inklusive sig själva, på rothanteringsgruppsnivå. De rekommenderade rollerna som ska tilldelas är antingen **Säkerhetsadministratör eller** **Säkerhetsläsare**. I allmänhet krävs rollen Säkerhetsadministratör för att tillämpa principer på rotnivå, medan Säkerhetsläsaren räcker för att ge synlighet på klientnivå. Mer information om de behörigheter som beviljas av dessa roller finns [i den inbyggda rollbeskrivningen för säkerhetsadministratör](../role-based-access-control/built-in-roles.md#security-admin) eller den [inbyggda rollbeskrivningen för Säkerhetsläsaren](../role-based-access-control/built-in-roles.md#security-reader).


#### <a name="assign-rbac-roles-to-users-through-the-azure-portal"></a>Tilldela RBAC-roller till användare via Azure-portalen: 

1. Logga in på [Azure-portalen](https://portal.azure.com). 
1. Om du vill visa hanteringsgrupper väljer du **Alla tjänster** under Huvudmenyn i Azure och väljer sedan **Hanteringsgrupper**.
1.  Välj en hanteringsgrupp och klicka på **information**.

    ![Skärmbild av information om hanteringsgrupper](./media/security-center-management-groups/management-group-details.PNG)
 
1. Klicka på **Åtkomstkontroll (IAM)** och sedan **rolltilldelningar**.

1. Klicka på **Lägg till rolltilldelning**.

1. Välj den roll som ska tilldelas och användaren och klicka sedan på **Spara**.  
   
   ![Skärmbild av rollen Lägg till säkerhetsläsare](./media/security-center-management-groups/asc-security-reader.png)


#### <a name="assign-rbac-roles-to-users-with-powershell"></a>Tilldela RBAC-roller till användare med PowerShell: 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Installera [Azure PowerShell](/powershell/azure/install-az-ps).
2. Kör följande kommandon: 

    ```azurepowershell
    # Login to Azure as a Global Administrator user
    Connect-AzAccount
    ```

3. Logga in med globala administratörsuppgifter när du uppmanas att göra det. 

    ![Logga in snabb skärmdump](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. Bevilja rollbehörigheter för läsare genom att köra följande kommando:

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. Om du vill ta bort rollen använder du följande kommando: 

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

### <a name="open-or-refresh-security-center"></a>Öppna eller uppdatera Säkerhetscenter
När du har förhöjd åtkomst öppnar eller uppdaterar du Azure Security Center för att verifiera att du har insyn i alla prenumerationer under din Azure AD-klientorganisation. 

1. Logga in på [Azure-portalen](https://portal.azure.com). 
2. Se till att du väljer alla prenumerationer i prenumerationsväljaren som du vill visa i Security Center.

    ![Skärmdump av prenumerationsväljare](./media/security-center-management-groups/subscription-selector.png)

1. Välj **Alla tjänster** under Huvudmenyn i Azure och välj sedan Security **Center**.
2. I **översikten**finns ett prenumerationstäckningsdiagram.

    ![Skärmdump av diagram för prenumerationstäckning](./media/security-center-management-groups/security-center-subscription-coverage.png)

3. Klicka på **Täckning** för att se listan över prenumerationer som omfattas. 

    ![Skärmdump av listan över prenumerationstäckning](./media/security-center-management-groups/security-center-coverage.png)

### <a name="remove-elevated-access"></a>Ta bort förhöjd åtkomst 
När RBAC-rollerna har tilldelats användarna bör klientadministratören ta bort sig själv från administratörsrollen för användaråtkomst.

1. Logga in på [Azure-portalen](https://portal.azure.com) eller [Administrationscentret](https://aad.portal.azure.com)för Azure Active Directory .

2. Klicka på Azure **Active Directory** i navigeringslistan och klicka sedan på **Egenskaper**.

3. Under **Global administratör kan hantera Azure-prenumerationer och hanteringsgrupper**anger du växeln till **Nr**.

4. Klicka på **Spara** om du vill spara inställningen.



## <a name="adding-subscriptions-to-a-management-group"></a>Lägga till prenumerationer i en hanteringsgrupp
Du kan lägga till prenumerationer i hanteringsgruppen som du skapade. De här stegen är inte obligatoriska för att få klientomfattande synlighet och global princip- och åtkomsthantering.

1. Under **Hanteringsgrupper**väljer du en hanteringsgrupp som du vill lägga till prenumerationen i.

    ![Välj en hanteringsgrupp som ska läggas till prenumeration i](./media/security-center-management-groups/management-group-subscriptions.png)

2. Välj **Lägg till befintlig**.

    ![Lägg till befintliga](./media/security-center-management-groups/add-existing.png)

3. Ange prenumeration under **Lägg till befintlig resurs** och klicka på **Spara**.

4. Upprepa steg 1 till och med 3 tills du har lagt till alla prenumerationer i omfånget.

   > [!NOTE]
   > Hanteringsgrupper kan innehålla både prenumerationer och underordnade hanteringsgrupper. När du tilldelar en användare en RBAC-roll till den överordnade hanteringsgruppen ärvs åtkomsten av den underordnade hanteringsgruppens prenumerationer. Principer som anges i den överordnade hanteringsgruppen ärvs också av barnen. 

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du får klientomfattande synlighet för Azure Security Center. I följande artiklar kan du lära dig mer om Security Center:

> [!div class="nextstepaction"]
> [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md)

> [!div class="nextstepaction"]
> [Hantera och besvara säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md)

