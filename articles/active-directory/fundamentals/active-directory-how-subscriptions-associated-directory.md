---
title: Hur du lägger till en befintlig prenumeration till din Azure Active Directory-klient | Microsoft Docs
description: Lär dig hur du lägger till en befintlig prenumeration till din Azure Active Directory-klient.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: dd62b22eca40a214c5b08a9bc48815e40fe90e47
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "45984094"
---
# <a name="how-to-associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Så här: Koppla eller lägga till en Azure-prenumeration i Azure Active Directory
Azure-prenumerationen har en förtroenderelation med Azure Active Directory (Azure AD), vilket innebär att prenumerationen litar på Azure AD för att autentisera användare, tjänster och enheter. Flera prenumerationer kan lita på samma Azure AD-katalog, men varje prenumeration kan bara lita på en enskild katalog.

Om din prenumeration går ut kan du förlora åtkomsten till alla de övriga resurser som är associerade med prenumerationen. Azure AD-katalog finns dock kvar i Azure, så att du kan associera och hantera kataloganvändarna med Azure-prenumeration.

Alla användare har en enda ”” arbetskatalog för autentisering. Användarna kan även vara gäster i andra kataloger. Du kan se hem och Gäst kataloger för varje användare i Azure AD.

>[!Important]
>Alla [rollbaserad åtkomstkontroll (RBAC)](../../role-based-access-control/role-assignments-portal.md) användare med tilldelad åtkomst, tillsammans med alla prenumerationsadministratörer förlorar åtkomst när prenumerationen katalogändringar. Dessutom om du har några nyckelvalv, kommer de också att påverkas av prenumeration flytten. Om du vill åtgärda som du måste [ändra klient-ID för nyckelvalvet](../../key-vault/key-vault-subscription-move-fix.md) innan du återuppta åtgärder.


## <a name="before-you-begin"></a>Innan du börjar
Innan du kan koppla eller lägga till din prenumeration, måste du utföra följande uppgifter:

- Logga in med ett konto som:
    - Har **RBAC ägare** åtkomst till prenumerationen.

    - Det finns i både den aktuella katalogen som är associerat med prenumerationen och i den nya katalogen som är där du vill associera prenumerationen framöver. Läs mer om att få åtkomst till en annan katalog [hur lägger Azure Active Directory-administratörer till B2B-samarbetare?](../b2b/add-users-administrator.md).

- Kontrollera att du inte använder en Azure Cloud Service Providers (CSP)-prenumeration (MS-AZR - 0145P, MS - AZR - 0146P, MS - AZR - 159P), en Microsoft Internal-prenumeration (MS-AZR - 0015P) eller en Microsoft Imagine-prenumeration (MS-AZR - 0144P).
    
## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Så här lägger du till en befintlig prenumeration i din Azure AD-katalog
1. Logga in och välj den prenumeration som du vill använda från den [prenumerationssidan i Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Välj **ändra katalog**.

    ![Prenumerationssidan med ändringen directory alternativet är markerat](media/active-directory-how-subscriptions-associated-directory/change-directory-button.png)

3. Granska alla varningar som visas och välj sedan **ändra**.

    ![Ändra katalogsidan, som visar att ändra till katalogen](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    Katalogen ändras för prenumerationen och du får ett meddelande.

    ![Meddelande om att något har slutförts](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)    

4. Använd katalogväxlaren för att gå till den nya katalogen. Det kan ta upp till 10 minuter innan allt visas korrekt.

    ![Katalogsidan mellan aktiviteter](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

Ändra prenumerationskatalogen är en åtgärd på servicenivå, så att det inte påverkar faktureringsägarskapet för prenumerationen. Kontoadministratören kan ändå ändra tjänstadministratör från den [Kontocenter](https://account.azure.com/subscriptions). Om du vill ta bort den ursprungliga katalogen måste du överför prenumerationsfaktureringen ägarskap till en ny kontoadministratör Mer information om att överföra faktureringsägarskapet finns i [Överför ägarskapet för en Azure-prenumeration till ett annat konto](../../billing/billing-subscription-transfer.md). 

## <a name="next-steps"></a>Nästa steg

- Skapa en ny Azure AD-klient, finns i [åtkomst till Azure Active Directory för att skapa en ny klient](active-directory-access-create-new-tenant.md)

- Mer information om hur resursåtkomsten hanteras i Microsoft Azure finns i [Förstå resursåtkomst i Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- Mer information om hur du tilldelar roller i Azure AD finns [tilldela directory-roller till användare med Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
