---
title: Lägga till eller ändra Azure-prenumeration för administratörsroller | Microsoft Docs
description: Beskriver hur du lägger till eller ändra Azure delad administratör, tjänstadministratör och kontoadministratör
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 1/23/2019
ms.author: cwatson
ms.openlocfilehash: 4490db0c479abdda19957be98335edeefc08bb59
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54808756"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Lägga till eller ändra Azure-prenumerationsadministratörer

Du måste ha rätt administratörsroll för att hantera åtkomst till Azure-resurser. Den här artikeln beskriver hur du lägger till eller ändra administratörsrollen för en användare på prenumerationsnivån.

## <a name="what-administrator-role-do-i-use"></a>Vilka administratörsrollen kan jag använda?

Azure har flera olika roller. Du kan använda klassiska prenumeration-administratörsroller som tjänstadministratör och delad administratör eller en nyare auktoriseringssystem som kallas rollbaserad åtkomstkontroll (RBAC) för att hantera åtkomst till resurser. Att säkerställa bättre kontroll och för att förenkla hantering rekommenderar vi att du använder RBAC åtkomst hantera allt. Om det är möjligt rekommenderar vi att du konfigurerar om befintliga principer för åtkomst med RBAC. Mer information finns i [vad är rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md) och [förstå de olika rollerna i Azure](../role-based-access-control/rbac-and-directory-admin-roles.md).

<a name="add-an-admin-for-a-subscription"></a>

## <a name="add-an-rbac-owner-for-a-subscription-in-azure-portal"></a>Lägg till en RBAC-ägare för en prenumeration på Azure-portalen 

Om du vill lägga till någon som administratör för en Azure-prenumeration tilldelar du rollen som [ägare](../role-based-access-control/built-in-roles.md#owner) till personen (en RBAC-roll) i prenumerationsomfånget. Ägarrollen kan hantera resurser i den prenumeration som du tilldelat, men har inte åtkomstbehörighet till andra prenumerationer.

1. Besök [ **prenumerationer** i Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Välj den prenumeration som du vill ge åtkomst till.
3. Välj **Åtkomstkontroll (IAM)** i listan.
4. Välj **Lägg till rolltilldelning**.
   (Om knappen Lägg till rollen tilldelning saknas, du har inte behörighet att lägga till behörigheter.)
5. I rutan **Roll** väljer du **Ägare**. 
6. I den **tilldela åtkomst till** väljer **Azure AD-användare, grupp eller tjänstens huvudnamn**. 
7. I rutan **Välj** anger du e-postadressen till användaren du vill lägga till som ägare. Välj användaren och sedan **Spara**.

    ![Skärmbild som visar ägarrollen valt](./media/billing-add-change-azure-subscription-administrator/add-role.png)

Detta ger användaren fullständig åtkomst till alla resurser inklusive rätten att delegera åtkomst till andra. Om du vill ge åtkomst i ett annat omfång, t.ex. en resursgrupp, Besök den **åtkomstkontroll (IAM)** bladet för detta omfång.

## <a name="add-or-change-co-administrator"></a>Lägga till eller ändra medadministratör

Endast [ägare](../role-based-access-control/built-in-roles.md#owner) kan läggas till som medadministratörer. Andra användare med roller som [deltagare](../role-based-access-control/built-in-roles.md#contributor) eller [läsare](../role-based-access-control/built-in-roles.md#reader) kan inte läggas till som medadministratörer.

> [!TIP]
> Du behöver bara lägga till ägaren som en delad administratör om du behöver hantera klassiska Azure-distributioner. Vi rekommenderar att du använder RBAC för andra ändamål.

1. Om du inte redan gjort lägger du till någon som ägare har följt anvisningarna ovan.
2. **Högerklicka på** ägare användaren du just har lagt till och välj sedan **Lägg till som medadministratör**. Om du inte ser den **Lägg till som medadministratör** alternativet, uppdatera sidan eller prova en annan webbläsare. 

    ![Skärmbild som lägger till medadministratör](./media/billing-add-change-azure-subscription-administrator/add-coadmin.png)

    Ta bort behörigheten medadministratör **högerklickar du på** medadministratör användar- och välj sedan **ta bort medadministratör**.

    ![Skärmbild som tar bort medadministratör](./media/billing-add-change-azure-subscription-administrator/remove-coadmin.png)

### <a name="adding-a-guest-user-as-a-co-administrator"></a>Att lägga till en gästanvändare som en medadministratör

[Gästanvändare](../active-directory/b2b/b2b-quickstart-add-guest-users-portal.md) som har tilldelats medadministratörerna rollen kan se några skillnader jämfört med användare med rollen delad administratör. Föreställ dig följande:

- Användare A med ett Azure AD arbets- eller skolkonto är tjänstadministratör för en Azure-prenumeration.
- Användare B har ett Microsoft-konto.
- Användare A tilldelar rollen som medadministratör till användaren B.
- Användare B kan göra nästan allt, men inte kan registrera program eller leta upp användare i Azure AD-katalog.

Du kan förvänta dig som användare B kan hantera allt. Orsaken till den här skillnaden är att Microsoft-kontot har lagts till prenumerationen som gästanvändare i stället för en medlemsanvändare. Gästanvändare har olika standardbehörigheter i Azure AD jämfört med användare. Till exempel användare kan läsa andra användare i Azure AD och det går inte att gästanvändare. Användare kan registrera nya tjänstens huvudnamn i Azure AD och det går inte att gästanvändare. Om en gästanvändare behöver för att kunna utföra dessa uppgifter, en möjlig lösning är att tilldela specifikt Azure AD-administratörsroller gästanvändaren behöver. Till exempel i scenariot ovan tilldelar du den [Katalogläsare](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) roll att läsa andra användare och tilldela den [programutvecklare](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-developer) roll för att kunna skapa tjänstens huvudnamn. Läs mer om medlemmar och gästanvändare och deras behörigheter, [vad är standardbehörigheterna för användare i Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md). 

Observera att den [inbyggda roller för Azure-resurser](../role-based-access-control/built-in-roles.md) skiljer sig från den [Azure AD-administratörsroller](../active-directory/users-groups-roles/directory-assign-admin-roles.md). De inbyggda rollerna bevilja inte åtkomst till Azure AD. Mer information finns i [förstå de olika rollerna](../role-based-access-control/rbac-and-directory-admin-roles.md).

<a name="change-service-administrator-for-a-subscription"></a>

## <a name="change-the-service-administrator-for-an-azure-subscription"></a>Ändra tjänstadministratör för en Azure-prenumeration

Är bara kontoadministratören kan ändra tjänstadministratör för en prenumeration. Som standard när du registrerar dig, är tjänstadministratör samma som kontoadministratör. Om tjänstadministratören har ändrats till en annan användare, förlorar kontoadministratör åtkomst till Azure-portalen. Kontoadministratören kan dock alltid använda Kontocenter för att ändra tjänstadministratör tillbaka till sig själva.

1. Kontrollera ditt scenario stöds genom att markera den [gränser för att ändra tjänstadministratörer](#limits).
1. Logga in på [Kontocenter](https://account.windowsazure.com/subscriptions) som kontoadministratör.
1. Välj en prenumeration.
1. Till höger, Välj **redigera prenumerationsinformation**.

    ![Skärmbild som visar knappen Redigera prenumeration i Kontocenter](./media/billing-add-change-azure-subscription-administrator/editsub.png)
1. I den **TJÄNSTADMINISTRATÖR** anger du e-postadressen till den nya tjänstadministratören.

    ![Skärmbild som visar rutan för att ändra tjänstadministratör e-postmeddelandet](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

<a name="limits"></a>

### <a name="limitations-for-changing-service-administrators"></a>Begränsningar för att ändra tjänstadministratörer

* Varje prenumeration är associerad med en Azure AD-katalog. För att hitta katalogen som prenumerationen är associerad med, går du till [ **prenumerationer**](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), Välj en prenumeration på finns i katalogen.
* Om du är inloggad med ett konto för arbetet eller skolan, kan du lägga till andra konton i din organisation som tjänstadministratör. Till exempel abby@contoso.com kan lägga till bob@contoso.com som tjänsten administratör, men det går inte att lägga till john@notcontoso.com såvida inte john@notcontoso.com har närvaro i katalogen contoso.com. Användare som loggat in med konton för arbete eller din skola kan fortsätta att lägga till Account användare som tjänstadministratör.

  | Inloggningsmetod | Lägga till Microsoft Account user som tjänstadministratör? | Lägg till konto för arbetet eller skolan i samma organisation som tjänstadministratör? | Lägg till konto för arbetet eller skolan i annan organisation som tjänstadministratör? |
  | --- | --- | --- | --- |
  |  Microsoft-konto |Ja |Nej |Nej |
  |  Arbets- eller Skolkonto |Ja |Ja |Nej |

## <a name="change-the-account-administrator-for-an-azure-subscription"></a>Ändra kontoadministratör för en Azure-prenumeration

Kontoadministratören är användaren som ursprungligen registrerat dig för Azure-prenumeration och ansvarar som fakturering ägare till prenumerationen. Om du vill ändra kontoadministratör för en prenumeration, se [överföra ägarskap för en Azure-prenumeration till ett annat konto](billing-subscription-transfer.md).

<a name="check-the-account-administrator-of-the-subscription"></a>

**Osäker som är kontoadministratör?** Följ de här stegen:

1. Besök [ **prenumerationer** i Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Välj den prenumeration som du vill kontrollera och tittar sedan under **inställningar**.
1. Välj **egenskaper**. Kontoadministratör för prenumerationen visas i den **kontoadministratören** box.  

## <a name="learn-more-about-resource-access-control-and-active-directory"></a>Mer information om åtkomstkontroll till resurser och Active Directory

* Läs mer om RBAC i [vad är rollbaserad åtkomstkontroll (RBAC)?](../role-based-access-control/overview.md)
* Läs mer om alla roller i Azure i [förstå de olika rollerna i Azure](../role-based-access-control/rbac-and-directory-admin-roles.md).
* Läs mer om Azure Active Directory, [hur Azure-prenumerationer är associerade med Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md) och [Tilldela administratörsroller i Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).