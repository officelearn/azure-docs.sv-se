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
ms.date: 10/25/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2f61792235d13ef801cffb3a69c32df54f058a9
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073581"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Koppla eller lägga till en Azure-prenumeration till din Azure Active Directory-klient

En Azure-prenumeration har en förtroende relation med Azure Active Directory (Azure AD). En prenumeration litar på Azure AD för att autentisera användare, tjänster och enheter.

Flera prenumerationer kan lita på samma Azure AD-katalog. Varje prenumeration kan bara lita på en enda katalog.

Om din prenumeration går ut kan du förlora åtkomsten till alla de övriga resurser som är associerade med prenumerationen. Azure AD-katalogen finns dock kvar i Azure. Du kan associera och hantera katalogen med en annan Azure-prenumeration.

Alla användare har en enda *arbets* katalog för autentisering. Dina användare kan också vara gäster i andra kataloger. Du kan se hem och Gäst kataloger för varje användare i Azure AD.

> [!Important]
> När du kopplar en prenumeration till en annan katalog förlorar användare som har roller som tilldelats med [rollbaserad åtkomst kontroll (RBAC)](../../role-based-access-control/role-assignments-portal.md) sin åtkomst. Klassiska prenumerations administratörer, inklusive tjänst administratör och medadministratörer, förlorar också åtkomsten.
>
> Princip tilldelningar tas också bort från en prenumeration när prenumerationen är associerad med en annan katalog.
>
> Om du flyttar ditt Azure Kubernetes service-kluster (AKS) till en annan prenumeration eller flyttar klustrets ägande prenumeration till en ny klient kan klustret förlora funktioner på grund av förlorade roll tilldelningar och tjänstens huvud namn. Mer information om AKS finns i [Azure Kubernetes service (AKS)](https://docs.microsoft.com/azure/aks/).


## <a name="before-you-begin"></a>Innan du börjar

Innan du kan koppla eller lägga till din prenumeration ska du utföra följande uppgifter:

- Granska följande lista över ändringar och hur du kan påverkas:

  - Användare som har tilldelats roller med RBAC förlorar sin åtkomst
  - Tjänst administratören och medadministratörer kommer att förlora åtkomst
  - Om du har nyckel valv kommer de inte att vara tillgängliga och du måste åtgärda dem efter kopplingen
  - Om du har några hanterade identiteter för resurser som Virtual Machines eller Logic Apps måste du återaktivera eller återskapa dem efter associationen
  - Om du har en registrerad Azure Stack måste du registrera den igen efter kopplingen

- Logga in med ett konto som:

  - Har en [ägar](../../role-based-access-control/built-in-roles.md#owner) roll tilldelning för prenumerationen. Information om hur du tilldelar ägar rollen finns i [Hantera åtkomst till Azure-resurser med RBAC och Azure Portal](../../role-based-access-control/role-assignments-portal.md).
  - Finns både i den aktuella katalogen och i den nya katalogen. Den aktuella katalogen är kopplad till prenumerationen. Du kopplar den nya katalogen till prenumerationen. Mer information om hur du får åtkomst till en annan katalog finns i [lägga till Azure Active Directory B2B-samarbets användare i Azure Portal](../b2b/add-users-administrator.md).

- Kontrollera att du inte använder en Azure Cloud Service Providers (CSP)-prenumeration (MS-AZR - 0145P, MS - AZR - 0146P, MS - AZR - 159P), en Microsoft Internal-prenumeration (MS-AZR - 0015P) eller en Microsoft Imagine-prenumeration (MS-AZR - 0144P).

## Koppla en prenumeration till en katalog<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

Följ dessa steg om du vill associera en befintlig prenumeration till din Azure AD-katalog:

1. Logga in och välj den prenumeration som du vill använda från den [prenumerationssidan i Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Välj **ändra katalog**.

    ![Prenumerationssidan med ändringen directory alternativet är markerat](media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png)

1. Granska alla varningar som visas och välj sedan **ändra**.

    ![Ändra katalogsidan, som visar att ändra till katalogen](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    Katalogen ändras för prenumerationen och du får ett meddelande.

    ![Meddelande om katalog ändring har slutförts](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)

Använd **växlings katalog** för att gå till din nya katalog. Det kan ta flera timmar innan allting visas korrekt. Om det verkar ta för lång tid, kontrol lera det **globala prenumerations filtret**. Kontrol lera att den flyttade prenumerationen inte är dold. Du kan behöva logga ut från Azure Portal och logga in igen för att se den nya katalogen.

![Sidan katalog växlaren med exempel information](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

Ändra prenumerationskatalogen är en åtgärd på servicenivå, så att det inte påverkar faktureringsägarskapet för prenumerationen. Kontoadministratören kan ändå ändra tjänstadministratör från den [Kontocenter](https://account.azure.com/subscriptions). Om du vill ta bort den ursprungliga katalogen måste du överföra prenumerations fakturerings ägarskapet till en ny konto administratör. Mer information om hur du överför fakturerings ägarskap finns i [överföra ägarskap för en Azure-prenumeration till ett annat konto](../../billing/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Steg efter associationen

När du har associerat en prenumeration till en annan katalog kan du behöva utföra följande uppgifter för att återuppta åtgärder:

- Om du har några nyckel valv måste du ändra klient-ID för nyckel valvet. Mer information finns i [ändra ett klient-ID för Key Vault efter att prenumerationen har flyttats](../../key-vault/key-vault-subscription-move-fix.md).

- Om du använde systemtilldelade hanterade identiteter för resurser måste du återaktivera dessa identiteter. Om du använde användarspecifika hanterade identiteter måste du återskapa dessa identiteter. När du har aktiverat eller återskapat de hanterade identiteterna måste du återupprätta de behörigheter som tilldelats dessa identiteter igen. Mer information finns i [Vad är hanterade identiteter för Azure-resurser?](../managed-identities-azure-resources/overview.md).

- Om du har registrerat en Azure Stack som använder den här prenumerationen måste du registrera dig igen. Mer information finns i [registrera Azure Stack med Azure](/azure-stack/operator/azure-stack-registration).

## <a name="next-steps"></a>Nästa steg

- Information om hur du skapar en ny Azure AD-klient finns i [snabb start: skapa en ny klient i Azure Active Directory](active-directory-access-create-new-tenant.md).

- Mer information om hur Microsoft Azure kontrollerar resurs åtkomsten finns i [klassiska prenumerationer på administratörs roller, RBAC-roller i Azure och Azure AD-administratörer](../../role-based-access-control/rbac-and-directory-admin-roles.md).

- Mer information om hur du tilldelar roller i Azure AD finns i [tilldela administratörs roller och icke-administratörer roller till användare med Azure Active Directory](active-directory-users-assign-role-azure-portal.md).
