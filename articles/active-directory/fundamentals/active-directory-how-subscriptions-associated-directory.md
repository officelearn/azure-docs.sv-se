---
title: Lägga till en befintlig Azure-prenumeration på din klientorganisation - Azure AD
description: Instruktioner om hur du lägger till en befintlig Azure-prenumeration i din Azure Active Directory-klientorganisation.
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
ms.openlocfilehash: 104bf51fb03d88ab0e5efd25ebebb0e3060bc264
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457934"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Associera eller lägga till en Azure-prenumeration till Azure Active Directory-klienten

En Azure-prenumeration har en förtroenderelation med Azure Active Directory (Azure AD). En prenumeration litar på Azure AD för att autentisera användare, tjänster och enheter.

Flera prenumerationer kan lita på samma Azure AD-katalog. Varje prenumeration kan bara lita på en enda katalog.

Om prenumerationen går ut förlorar du åtkomsten till alla andra resurser som är kopplade till prenumerationen. Azure AD-katalogen finns dock kvar i Azure. Du kan associera och hantera katalogen med en annan Azure-prenumeration.

Alla användare har en enda *arbetskatalog* för autentisering. Dina användare kan också vara gäster i andra kataloger. Du kan se både hem- och gästkataloger för varje användare i Azure AD.

> [!Important]
> När du associerar en prenumeration till en annan katalog förlorar användare som har roller som tilldelats med hjälp av [rollbaserad åtkomstkontroll (RBAC)](../../role-based-access-control/role-assignments-portal.md) åtkomst. Klassiska prenumerationsadministratörer, inklusive tjänstadministratörer och medadministratörer, förlorar också åtkomsten.
>
> Principtilldelningar tas också bort från en prenumeration när prenumerationen associeras med en annan katalog.
>
> Om du flyttar AKS-klustret (Azure Kubernetes Service) till en annan prenumeration, eller flyttar den klusterägande prenumerationen till en ny klientorganisation, förlorar klustret funktioner på grund av förlorade rolltilldelningar och tjänsthuvudnamns rättigheter. Mer information om AKS finns i [Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/).


## <a name="before-you-begin"></a>Innan du börjar

Innan du kan associera eller lägga till din prenumeration gör du följande:

- Läs följande lista över ändringar och hur du kan påverkas:

  - Användare som har tilldelats roller med RBAC förlorar sin åtkomst
  - Tjänstadministratör och medadministratörer förlorar åtkomst
  - Om du har några nyckelvalv, kommer de att vara otillgängliga och du måste fixa dem efter association
  - Om du har några hanterade identiteter för resurser som virtuella datorer eller logikappar måste du aktivera om eller återskapa dem efter associationen
  - Om du har en registrerad Azure Stack måste du registrera om den efter associationen

- Logga in med ett konto som:

  - Har [Owner](../../role-based-access-control/built-in-roles.md#owner) en ägarrolltilldelning för prenumerationen. Information om hur du tilldelar ägarrollen finns i [Hantera åtkomst till Azure-resurser med RBAC och Azure-portalen](../../role-based-access-control/role-assignments-portal.md).
  - Finns både i den aktuella katalogen och i den nya katalogen. Den aktuella katalogen är associerad med prenumerationen. Du ska associera den nya katalogen med prenumerationen. Mer information om hur du får åtkomst till en annan katalog finns [i Lägga till Azure Active Directory B2B-samarbetsanvändare i Azure-portalen](../b2b/add-users-administrator.md).

- Kontrollera att du inte använder en CSP-prenumeration (Azure Cloud Service Providers) (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), en Microsoft Internal-prenumeration (MS-AZR-0015P) eller en Microsoft Imagine-prenumeration (MS-AZR-0144P).

## <a name="associate-a-subscription-to-a-directory"></a>Associera en prenumeration till en katalog<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

Så här associerar du en befintlig prenumeration till din Azure AD-katalog:

1. Logga in och välj den prenumeration som du vill använda på [sidan Prenumerationer i Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Välj **Ändra katalog**.

    ![Sidan Prenumerationer med alternativet Ändra katalog markerat](media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png)

1. Granska alla varningar som visas och välj sedan **Ändra**.

    ![Ändra katalogsidan och visa katalogen för att ändra till](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    Katalogen ändras för prenumerationen och du får ett meddelande om lyckade filer.

    ![Meddelande om lyckad granskning om katalogändring](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)

Använd **Växla katalog** för att gå till den nya katalogen. Det kan ta flera timmar för allt att dyka upp ordentligt. Om det verkar ta för lång tid kontrollerar du det **globala prenumerationsfiltret**. Kontrollera att den flyttade prenumerationen inte är dold. Du kan behöva logga ut från Azure-portalen och logga in igen för att se den nya katalogen.

![Sidan Katalogväxlare med exempelinformation](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

Att ändra prenumerationskatalogen är en åtgärd på tjänstnivå, så det påverkar inte prenumerationsfaktureringsägande. Kontoadministratören kan fortfarande ändra serviceadministratören från [kontocentret](https://account.azure.com/subscriptions). Om du vill ta bort den ursprungliga katalogen måste du överföra ägarskapet för prenumerationsfakturering till en ny kontoadministratör. Mer information om hur du överför faktureringsägande finns i [Överföra ägarskap för en Azure-prenumeration till ett annat konto](../../cost-management-billing/manage/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Steg efter association

När du har associerat en prenumeration till en annan katalog kan du behöva göra följande för att återuppta åtgärderna:

- Om du har några nyckelvalv måste du ändra klient-ID:et för nyckelvalvet. Mer information finns i [Ändra ett klient-ID för nyckelvalv efter ett prenumerationssteg](../../key-vault/general/subscription-move-fix.md).

- Om du använde systemtilldelade hanterade identiteter för resurser måste du återaktivera dessa identiteter. Om du använde användartilldelade hanterade identiteter måste du återskapa dessa identiteter. När du har återskapat eller återskapat de hanterade identiteterna måste du återupprätta de behörigheter som tilldelats dessa identiteter. Mer information finns i [Vad är hanterade identiteter för Azure-resurser?](../managed-identities-azure-resources/overview.md).

- Om du har registrerat en Azure Stack med den här prenumerationen måste du registrera om. Mer information finns i [Registrera Azure Stack med Azure](/azure-stack/operator/azure-stack-registration).

## <a name="next-steps"></a>Nästa steg

- Information om hur du skapar en ny Azure AD-klient finns i [Snabbstart: Skapa en ny klient i Azure Active Directory](active-directory-access-create-new-tenant.md).

- Mer information om hur Microsoft Azure styr resursåtkomst finns i [Klassiska prenumerationsadministratörsroller, Azure RBAC-roller och Azure AD-administratörsroller](../../role-based-access-control/rbac-and-directory-admin-roles.md).

- Mer information om hur du tilldelar roller i Azure AD finns i [Tilldela administratörs- och icke-administratörsroller till användare med Azure Active Directory](active-directory-users-assign-role-azure-portal.md).
