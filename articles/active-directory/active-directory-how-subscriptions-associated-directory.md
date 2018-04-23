---
title: Så här lägger du till en befintlig prenumeration i din Azure AD-katalog | Microsoft Docs
description: Så här lägger du till en befintlig prenumeration i din Azure AD-katalog
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.assetid: bc4773c2-bc4a-4d21-9264-2267065f0aea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/12/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 1e258473614e01ca35eaa5970d02544844da6972
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Så här associerar du eller lägger till en prenumeration i din Azure Active Directory

Det här avsnittet innehåller information om relationen mellan en Azure-prenumeration och Azure AD (Active Directory Azure) och hur du lägger till en befintlig prenumeration till din Azure Active Directory-katalog. Din Azure-prenumeration har en förtroenderelation med Azure AD, vilket innebär att den har förtroende för katalogen när det gäller att autentisera användare, tjänster och enheter. Flera prenumerationer kan ha förtroende för samma katalog, men varje prenumeration har bara förtroende för en katalog. 

Förtroendet som en prenumeration har för en katalog skiljer sig från relationen som den har med andra resurser i Azure (webbplatser, databaser och så vidare). Om en prenumeration går ut stoppas även åtkomsten till de övriga resurser som är associerade med prenumerationen. Men en Azure Active Directory-katalog finns kvar i Azure och du kan associera en annan prenumeration med katalogen och hantera kataloganvändarna med den nya prenumerationen.

Alla användare har en enda arbetskatalog som autentiserar dem, men de kan även vara gäster i andra kataloger. I Azure Active Directory kan du bara se de kataloger som ditt användarkonto är medlem eller gäst i.

## <a name="before-you-begin"></a>Innan du börjar

* Du måste logga in med kontot som har RBAC-ägaråtkomst till prenumerationen.
* Du måste logga in med ett konto som finns i både den aktuella katalogen som prenumerationen är associerad till och i den katalog som du vill lägga till den i. Läs mer om att få åtkomst till en annan katalog i [Hur lägger Azure Active Directory-administratörer till B2B-samarbetare?](active-directory-b2b-admin-add-users.md)
* Den här funktionen är inte tillgänglig för prenumerationer av CSP (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P) och Microsoft Imagine (MS-AZR-0144P).

## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Så här lägger du till en befintlig prenumeration i din Azure AD-katalog

1. Logga in och välj en prenumeration från [prenumerationssidan i Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Klicka på **Ändra katalog**.

    ![Skärmbild som visar knappen Byt katalog](./media/active-directory-how-subscriptions-associated-directory/edit-directory-button.PNG)
3. Granska varningarna. All användare med [Rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/role-assignments-portal.md) med tilldelad åtkomst och alla prenumerationsadministratörer förlorar åtkomst när prenumerationskatalogen ändras.
4. Välj en katalog.

    ![Skärmbild som visar användargränssnitt för den ändrade katalogen](./media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.PNG)
5. Klicka på **Ändra**.
6. Lyckades! Använd katalogväxlaren för att gå till den nya katalogen. Det kan ta upp till 10 minuter innan allt visas korrekt.

    ![Skärmbild som visar meddelande om att katalogändringen lyckats](./media/active-directory-how-subscriptions-associated-directory/edit-directory-success.PNG)

    ![Skärmbild som visar växlaren](./media/active-directory-how-subscriptions-associated-directory/directory-switcher.PNG)


Att ändra prenumerationskatalogen är en åtgärd på servicenivå. Det påverkar inte faktureringsägarskap för prenumerationen och kontoadministratören kan ändå ändra tjänstadministratör med hjälp av [Kontocentret](https://account.azure.com/subscriptions). Om du vill ta bort den ursprungliga katalogen måste du överföra faktureringsägarskapet för prenumerationen till en ny kontoadministratör. Mer information om att överföra faktureringsägarskapet finns i [Överför ägarskapet för en Azure-prenumeration till ett annat konto](../billing/billing-subscription-transfer.md). 

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du kostnadsfritt skapar en ny Azure Active Directory-katalog finns i [Skaffa en Azure Active Directory-klient](develop/active-directory-howto-tenant.md)
* Mer information om att överföra faktureringsägarskapet för en Azure-prenumeration finns i [Överför ägarskapet för en Azure-prenumeration till ett annat konto](../billing/billing-subscription-transfer.md)
* Mer information om hur resursåtkomsten hanteras i Microsoft Azure finns i [Förstå resursåtkomst i Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)
* Mer information om hur du tilldelar roller i Azure AD finns i [Tilldela administratörsroller i Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md)

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG
