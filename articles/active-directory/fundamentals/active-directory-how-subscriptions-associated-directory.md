---
title: Lägga till en befintlig prenumeration i din klient - Azure Active Directory | Microsoft Docs
description: Anvisningar om hur du lägger till en befintlig prenumeration till din Azure Active Directory-klient.
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
ms.openlocfilehash: fb4fa92d8b3c174cdf9b3695f8564cc11c1ad291
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68851751"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Koppla eller lägga till en Azure-prenumeration till din Azure Active Directory-klient

En Azure-prenumeration har en förtroende relation med Azure Active Directory (Azure AD), vilket innebär att prenumerationen litar på Azure AD för att autentisera användare, tjänster och enheter. Flera prenumerationer kan lita på samma Azure AD-katalog, men varje prenumeration kan bara lita på en enskild katalog.

Om din prenumeration går ut kan du förlora åtkomsten till alla de övriga resurser som är associerade med prenumerationen. Azure AD-katalog finns dock kvar i Azure, så att du kan associera och hantera kataloganvändarna med Azure-prenumeration.

Alla användare har en enda *arbets* katalog för autentisering. Användarna kan även vara gäster i andra kataloger. Du kan se hem och Gäst kataloger för varje användare i Azure AD.

> [!Important]
> När du kopplar en prenumeration till en annan katalog kommer användare som har roller som tilldelats med [rollbaserad åtkomst kontroll (RBAC)](../../role-based-access-control/role-assignments-portal.md) att förlora sin åtkomst. Klassiska prenumerations administratörer (tjänst administratör och medadministratörer) kommer också att förlora åtkomst.
> 
> Om du flyttar ditt Azure Kubernetes service-kluster (AKS) till en annan prenumeration eller flyttar klustrets ägande prenumeration till en ny klient kan klustret förlora funktioner på grund av förlorade roll tilldelningar och tjänst huvud namns rättigheter. Mer information om AKS finns i [Azure Kubernetes service (AKS)](https://docs.microsoft.com/azure/aks/).

## <a name="before-you-begin"></a>Innan du börjar

Innan du kan koppla eller lägga till din prenumeration, måste du utföra följande uppgifter:

1. Granska följande lista över ändringar och hur du kan påverkas:

    - Användare som har tilldelats roller med RBAC förlorar sin åtkomst
    - Tjänst administratören och medadministratörer kommer att förlora åtkomst
    - Om du har nyckel valv kommer de inte att vara tillgängliga och du måste åtgärda dem efter kopplingen
    - Om du har några hanterade identiteter för resurser som Virtual Machines eller Logic Apps måste du återaktivera eller återskapa dem efter associationen
    - Om du har en registrerad Azure Stack måste du registrera den igen efter kopplingen

1. Logga in med ett konto som:
    - Har en [ägar](../../role-based-access-control/built-in-roles.md#owner) roll tilldelning för prenumerationen. Information om hur du tilldelar ägar rollen finns i [Hantera åtkomst till Azure-resurser med RBAC och Azure Portal](../../role-based-access-control/role-assignments-portal.md).
    - Det finns i både den aktuella katalogen som är associerat med prenumerationen och i den nya katalogen som är där du vill associera prenumerationen framöver. Läs mer om att få åtkomst till en annan katalog [hur lägger Azure Active Directory-administratörer till B2B-samarbetare?](../b2b/add-users-administrator.md).

1. Kontrollera att du inte använder en Azure Cloud Service Providers (CSP)-prenumeration (MS-AZR - 0145P, MS - AZR - 0146P, MS - AZR - 159P), en Microsoft Internal-prenumeration (MS-AZR - 0015P) eller en Microsoft Imagine-prenumeration (MS-AZR - 0144P).
    
## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Så här lägger du till en befintlig prenumeration i din Azure AD-katalog

1. Logga in och välj den prenumeration som du vill använda från den [prenumerationssidan i Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Välj **ändra katalog**.

    ![Prenumerationssidan med ändringen directory alternativet är markerat](media/active-directory-how-subscriptions-associated-directory/change-directory-button.png)

3. Granska alla varningar som visas och välj sedan **ändra**.

    ![Ändra katalogsidan, som visar att ändra till katalogen](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    Katalogen ändras för prenumerationen och du får ett meddelande.

    ![Meddelande om katalog ändring har slutförts](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)
4. Använd **katalogen växlaren** för att gå till din nya katalog. Det kan ta flera timmar innan allting visas korrekt. Om det verkar ta för lång tid, kontrollerar du det **globala prenumerations filtret** för den flyttade prenumerationen för att kontrol lera att den inte är dold. Du kan behöva logga ut från Azure Portal och logga in igen för att kunna se den nya katalogen. 

    ![Sidan katalog växlaren med exempel information](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

Ändra prenumerationskatalogen är en åtgärd på servicenivå, så att det inte påverkar faktureringsägarskapet för prenumerationen. Kontoadministratören kan ändå ändra tjänstadministratör från den [Kontocenter](https://account.azure.com/subscriptions). Om du vill ta bort den ursprungliga katalogen måste du överför prenumerationsfaktureringen ägarskap till en ny kontoadministratör Mer information om att överföra faktureringsägarskapet finns i [Överför ägarskapet för en Azure-prenumeration till ett annat konto](../../billing/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Publicera kopplings steg
När du har associerat en prenumeration till en annan katalog kan det finnas ytterligare steg som du måste utföra för att återuppta åtgärder.

1. Om du har några nyckel valv måste du ändra klient-ID för nyckel valvet. Mer information finns i [ändra ett klient-ID för Key Vault efter](../../key-vault/key-vault-subscription-move-fix.md)att prenumerationen har flyttats.

2. Om du använde systemtilldelade hanterade identiteter för resurser måste du aktivera dem igen. Om du använde användarspecifika hanterade identiteter måste du skapa dem på nytt. När du har aktiverat eller återskapat de hanterade identiteterna måste du återupprätta de behörigheter som tilldelats dessa identiteter igen. Mer information finns i [Vad är hanterade identiteter för Azure-resurser?](../managed-identities-azure-resources/overview.md).

3. Om du har registrerat en Azure Stack med hjälp av den här prenumerationen måste du registrera dig igen. Mer information finns i [registrera Azure Stack med Azure](/azure-stack/operator/azure-stack-registration).



## <a name="next-steps"></a>Nästa steg

- Skapa en ny Azure AD-klient, finns i [åtkomst till Azure Active Directory för att skapa en ny klient](active-directory-access-create-new-tenant.md)

- Mer information om hur resursåtkomsten hanteras i Microsoft Azure finns i [Förstå resursåtkomst i Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- Mer information om hur du tilldelar roller i Azure AD finns [tilldela directory-roller till användare med Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
