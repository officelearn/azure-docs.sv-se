---
title: Lägga till en befintlig prenumeration i din klient - Azure Active Directory | Microsoft Docs
description: Anvisningar om hur du lägger till en befintlig prenumeration till din Azure Active Directory-klient.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06b6fdf7a3d21a6b7dc84c4347824d4eaeac046f
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64918400"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Koppla eller lägga till en Azure-prenumeration till din Azure Active Directory-klient

En Azure-prenumeration har en förtroenderelation med Azure Active Directory (Azure AD), vilket innebär att prenumerationen litar på Azure AD för att autentisera användare, tjänster och enheter. Flera prenumerationer kan lita på samma Azure AD-katalog, men varje prenumeration kan bara lita på en enskild katalog.

Om din prenumeration går ut kan du förlora åtkomsten till alla de övriga resurser som är associerade med prenumerationen. Azure AD-katalog finns dock kvar i Azure, så att du kan associera och hantera kataloganvändarna med Azure-prenumeration.

Alla användare har en enda *home* katalogen för autentisering. Användarna kan även vara gäster i andra kataloger. Du kan se hem och Gäst kataloger för varje användare i Azure AD.

> [!Important]
> När du associerar en prenumeration på en annan katalog, användare som har tilldelats med hjälp av roller [rollbaserad åtkomstkontroll (RBAC)](../../role-based-access-control/role-assignments-portal.md) förlorar sin åtkomst. Klassiska prenumerationens administratörer (tjänstadministratören och Medadministratörer) kommer också att förlora åtkomst.
> 
> Flytta ditt kluster i Azure Kubernetes Service (AKS) till en annan prenumeration eller flytta ägande av kluster-prenumeration till en ny klient gör dessutom att klustret förlorar funktionalitet på grund av förlorad rolltilldelningar och tjänstens huvudnamn rättigheter. Läs mer om AKS [Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/).

## <a name="before-you-begin"></a>Innan du börjar

Innan du kan koppla eller lägga till din prenumeration, måste du utföra följande uppgifter:

1. Granska följande lista över ändringar och hur du kan påverkas:

    - Användare som har tilldelats med hjälp av RBAC förlorar sin åtkomst
    - Tjänstadministratören och Medadministratörer förlorar åtkomst
    - Om du har några nyckelvalv, de är otillgänglig och du behöver åtgärda dem när detta har gjorts
    - Om du har en registrerad Azure Stack, måste du omregistrera när detta har gjorts

1. Logga in med ett konto som:
    - Har en [ägare](../../role-based-access-control/built-in-roles.md#owner) rolltilldelning för prenumerationen. Information om hur du tilldelar rollen ägare finns i [hantera åtkomst till Azure-resurser med RBAC och Azure portal](../../role-based-access-control/role-assignments-portal.md).
    - Det finns i både den aktuella katalogen som är associerat med prenumerationen och i den nya katalogen som är där du vill associera prenumerationen framöver. Läs mer om att få åtkomst till en annan katalog [hur lägger Azure Active Directory-administratörer till B2B-samarbetare?](../b2b/add-users-administrator.md).

1. Kontrollera att du inte använder en Azure Cloud Service Providers (CSP)-prenumeration (MS-AZR - 0145P, MS - AZR - 0146P, MS - AZR - 159P), en Microsoft Internal-prenumeration (MS-AZR - 0015P) eller en Microsoft Imagine-prenumeration (MS-AZR - 0144P).
    
## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Så här lägger du till en befintlig prenumeration i din Azure AD-katalog

1. Logga in och välj den prenumeration som du vill använda från den [prenumerationssidan i Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Välj **ändra katalog**.

    ![Prenumerationssidan med ändringen directory alternativet är markerat](media/active-directory-how-subscriptions-associated-directory/change-directory-button.png)

3. Granska alla varningar som visas och välj sedan **ändra**.

    ![Ändra katalogsidan, som visar att ändra till katalogen](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    Katalogen ändras för prenumerationen och du får ett meddelande.

    ![Meddelande om katalogändring](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)    
4. Använd den **katalogväxlaren** att gå till den nya katalogen. Det kan ta upp till 10 minuter innan allt visas korrekt.

    ![Katalogsidan mellan aktiviteter med exempelinformation](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

Ändra prenumerationskatalogen är en åtgärd på servicenivå, så att det inte påverkar faktureringsägarskapet för prenumerationen. Kontoadministratören kan ändå ändra tjänstadministratör från den [Kontocenter](https://account.azure.com/subscriptions). Om du vill ta bort den ursprungliga katalogen måste du överför prenumerationsfaktureringen ägarskap till en ny kontoadministratör Mer information om att överföra faktureringsägarskapet finns i [Överför ägarskapet för en Azure-prenumeration till ett annat konto](../../billing/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Association steg efter
När du har associerat en prenumeration på en annan katalog, kan det finnas ytterligare steg som du måste utföra om du vill återuppta åtgärder.

1. Om du har alla viktiga valv, måste du ändra nyckelvalv klient-ID. Mer information finns i [ändra en nyckelvalvsklient-ID efter en prenumerationsflytt](../../key-vault/key-vault-subscription-move-fix.md).

2. Om du har registrerat ett Azure Stack med hjälp av den här prenumerationen måste du omregistrera. Mer information finns i [registrera Azure Stack med Azure](/azure-stack/operator/azure-stack-registration).



## <a name="next-steps"></a>Nästa steg

- Skapa en ny Azure AD-klient, finns i [åtkomst till Azure Active Directory för att skapa en ny klient](active-directory-access-create-new-tenant.md)

- Mer information om hur resursåtkomsten hanteras i Microsoft Azure finns i [Förstå resursåtkomst i Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- Mer information om hur du tilldelar roller i Azure AD finns [tilldela directory-roller till användare med Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
