---
title: Lägg till en befintlig Azure-prenumeration till din klient organisation – Azure Active Directory | Microsoft Docs
description: Anvisningar om hur du lägger till en befintlig Azure-prenumeration i Azure Active Directory-klienten.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6bce05b99c14fb464cd1a17587d9cf254909d87
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473409"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Associera eller lägga till en Azure-prenumeration till din Azure Active Directory-klient

En Azure-prenumeration har en förtroende relation med Azure Active Directory (Azure AD), vilket innebär att prenumerationen litar på Azure AD för att autentisera användare, tjänster och enheter. Flera prenumerationer kan lita på samma Azure AD-katalog, men varje prenumeration kan bara lita på en enda katalog.

Om din prenumeration går ut förlorar du åtkomsten till alla andra resurser som är associerade med prenumerationen. Azure AD-katalogen finns dock kvar i Azure, vilket gör att du kan associera och hantera katalogen med en annan Azure-prenumeration.

Alla användare har en enda *arbets* katalog för autentisering. Användarna kan dock också vara gäster i andra kataloger. Du kan se både hem-och gäst kataloger för varje användare i Azure AD.

> [!Important]
> När du kopplar en prenumeration till en annan katalog kommer användare som har roller som tilldelats med [rollbaserad åtkomst kontroll (RBAC)](../../role-based-access-control/role-assignments-portal.md) att förlora sin åtkomst. Klassiska prenumerations administratörer (tjänst administratör och medadministratörer) kommer också att förlora åtkomst. 
>
> Princip tilldelningar tas också bort från en prenumeration när prenumerationen är associerad med en annan katalog.
> 
> Om du flyttar ditt Azure Kubernetes service-kluster (AKS) till en annan prenumeration eller flyttar klustrets ägande prenumeration till en ny klient kan klustret förlora funktioner på grund av förlorade roll tilldelningar och tjänst huvud namns rättigheter. Mer information om AKS finns i [Azure Kubernetes service (AKS)](https://docs.microsoft.com/azure/aks/).

## <a name="before-you-begin"></a>Innan du börjar

Innan du kan koppla eller lägga till din prenumeration måste du utföra följande uppgifter:

1. Granska följande lista över ändringar och hur du kan påverkas:

    - Användare som har tilldelats roller med RBAC förlorar sin åtkomst
    - Tjänst administratören och medadministratörer kommer att förlora åtkomst
    - Om du har nyckel valv kommer de inte att vara tillgängliga och du måste åtgärda dem efter kopplingen
    - Om du har några hanterade identiteter för resurser som Virtual Machines eller Logic Apps måste du återaktivera eller återskapa dem efter associationen
    - Om du har en registrerad Azure Stack måste du registrera den igen efter kopplingen

1. Logga in med ett konto som:
    - Har en [ägar](../../role-based-access-control/built-in-roles.md#owner) roll tilldelning för prenumerationen. Information om hur du tilldelar ägar rollen finns i [Hantera åtkomst till Azure-resurser med RBAC och Azure Portal](../../role-based-access-control/role-assignments-portal.md).
    - Finns både i den aktuella katalogen som är associerad med prenumerationen och i den nya katalogen där du vill associera prenumerationen. Mer information om hur du får åtkomst till en annan katalog finns i [hur Azure Active Directory administratörer lägga till B2B-samarbets användare?](../b2b/add-users-administrator.md).

1. Se till att du inte använder en Azure Cloud Service Providers (CSP)-prenumeration (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), en intern Microsoft-prenumeration (MS-AZR-0015P) eller en Microsoft Imagine-prenumeration (MS-AZR-0144P).
    
## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Så här lägger du till en befintlig prenumeration i din Azure AD-katalog

1. Logga in och välj den prenumeration som du vill använda från [sidan prenumerationer i Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Välj **ändra katalog**.

    ![Sidan prenumerationer med alternativet för att ändra katalog är markerat](media/active-directory-how-subscriptions-associated-directory/change-directory-button.png)

3. Granska eventuella varningar som visas och välj sedan **ändra**.

    ![Ändra katalog sidan, som visar katalogen som ska ändras till](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    Katalogen ändras för prenumerationen och du får ett meddelande om att det lyckades.

    ![Meddelande om katalog ändring har slutförts](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)
4. Använd **katalogen växlaren** för att gå till din nya katalog. Det kan ta flera timmar innan allting visas korrekt. Om det verkar ta för lång tid, kontrollerar du det **globala prenumerations filtret** för den flyttade prenumerationen för att kontrol lera att den inte är dold. Du kan behöva logga ut från Azure Portal och logga in igen för att kunna se den nya katalogen. 

    ![Sidan katalog växlaren med exempel information](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

Att ändra prenumerations katalogen är en åtgärd på tjänst nivå, så den påverkar inte prenumerationens fakturerings ägande. Konto administratören kan fortfarande ändra tjänst administratör från [konto Center](https://account.azure.com/subscriptions). Om du vill ta bort den ursprungliga katalogen måste du överföra prenumerations fakturerings ägarskapet till en ny konto administratör. Mer information om hur du överför fakturerings ägarskap finns i [överföra ägarskap för en Azure-prenumeration till ett annat konto](../../billing/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Publicera kopplings steg
När du har associerat en prenumeration till en annan katalog kan det finnas ytterligare steg som du måste utföra för att återuppta åtgärder.

1. Om du har några nyckel valv måste du ändra klient-ID för nyckel valvet. Mer information finns i [ändra ett klient-ID för Key Vault efter att prenumerationen har flyttats](../../key-vault/key-vault-subscription-move-fix.md).

2. Om du använde systemtilldelade hanterade identiteter för resurser måste du aktivera dem igen. Om du använde användarspecifika hanterade identiteter måste du skapa dem på nytt. När du har aktiverat eller återskapat de hanterade identiteterna måste du återupprätta de behörigheter som tilldelats dessa identiteter igen. Mer information finns i [Vad är hanterade identiteter för Azure-resurser?](../managed-identities-azure-resources/overview.md).

3. Om du har registrerat en Azure Stack med hjälp av den här prenumerationen måste du registrera dig igen. Mer information finns i [registrera Azure Stack med Azure](/azure-stack/operator/azure-stack-registration).



## <a name="next-steps"></a>Nästa steg

- Om du vill skapa en ny Azure AD-klient läser du [åtkomst Azure Active Directory för att skapa en ny klient](active-directory-access-create-new-tenant.md)

- Mer information om hur resursåtkomsten hanteras i Microsoft Azure finns i [Förstå resursåtkomst i Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- Mer information om hur du tilldelar roller i Azure AD finns i [så här tilldelar du katalog roller till användare med Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
