---
title: Lägga till eller ta bort rolltilldelningar för externa användare med RBAC och Azure-portalen
description: Lär dig hur du beviljar åtkomst till Azure-resurser för användare som är externa till en organisation med hjälp av Azure-rollbaserad åtkomstkontroll (RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: 1c440b85f792ac5bb1336f4d20f930aafc38ad7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245647"
---
# <a name="add-or-remove-role-assignments-for-external-guest-users-using-azure-rbac-and-the-azure-portal"></a>Lägga till eller ta bort rolltilldelningar för externa gästanvändare med Azure RBAC och Azure-portalen

[Azure rollbaserad åtkomstkontroll (RBAC)](overview.md) möjliggör bättre säkerhetshantering för stora organisationer och för små och medelstora företag som arbetar med externa medarbetare, leverantörer eller frilansare som behöver åtkomst till specifika resurser i din miljö, men inte nödvändigtvis till hela infrastrukturen eller eventuella faktureringsrelaterade scope. Du kan använda funktionerna i [Azure Active Directory B2B](../active-directory/b2b/what-is-b2b.md) för att samarbeta med externa gästanvändare och du kan använda RBAC för att bara bevilja de behörigheter som gästanvändare behöver i din miljö.

## <a name="prerequisites"></a>Krav

Om du vill lägga till eller ta bort rolltilldelningar måste du ha:

- `Microsoft.Authorization/roleAssignments/write`och `Microsoft.Authorization/roleAssignments/delete` behörigheter, till exempel [administratör för användaråtkomst](built-in-roles.md#user-access-administrator) eller [ägare](built-in-roles.md#owner)

## <a name="when-would-you-invite-guest-users"></a>När skulle du bjuda in gästanvändare?

Här är ett par exempelscenarier när du kan bjuda in gästanvändare till din organisation och bevilja behörigheter:

- Tillåt en extern leverantör som är egenföretagare och som bara har ett e-postkonto för att komma åt dina Azure-resurser för ett projekt.
- Tillåt en extern partner att hantera vissa resurser eller en hel prenumeration.
- Tillåt supporttekniker som inte finns i din organisation (till exempel Microsoft-support) att tillfälligt komma åt din Azure-resurs för att felsöka problem.

## <a name="permission-differences-between-member-users-and-guest-users"></a>Behörighetsskillnader mellan medlemsanvändare och gästanvändare

Inbyggda medlemmar i en katalog (medlemsanvändare) har andra behörigheter än användare som bjudits in från en annan katalog som B2B-samarbetsgäst (gästanvändare). Medlemmar kan till exempel läsa nästan all kataloginformation medan gästanvändare har begränsade katalogbehörigheter. Mer information om medlemsanvändare och gästanvändare finns [i Vilka är standardanvändarbehörigheterna i Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md)

## <a name="add-a-guest-user-to-your-directory"></a>Lägga till en gästanvändare i din katalog

Följ dessa steg för att lägga till en gästanvändare i din katalog med hjälp av Sidan Azure Active Directory.

1. Kontrollera att organisationens inställningar för externt samarbete är konfigurerade så att du får bjuda in gäster. Mer information finns i [Aktivera B2B externt samarbete och hantera vem som kan bjuda in gäster](../active-directory/b2b/delegate-invitations.md).

1. Klicka på Azure Active Directory**Users** > **New gästanvändare**i Azure **Portal.** > 

    ![Ny gästanvändarfunktion i Azure portal](./media/role-assignments-external-users/invite-guest-user.png)

1. Följ stegen för att lägga till en ny gästanvändare. Mer information finns [i Lägga till Azure Active Directory B2B-samarbetsanvändare i Azure-portalen](../active-directory/b2b/add-users-administrator.md#add-guest-users-to-the-directory).

När du har lagt till en gästanvändare i katalogen kan du antingen skicka en direktlänk till gästanvändaren till en delad app eller gästanvändaren kan klicka på inlösen-URL:en i e-postmeddelandet för inbjudan.

![Bjuda in e-post till gästanvändare](./media/role-assignments-external-users/invite-email.png)

För att gästanvändaren ska kunna komma åt din katalog måste de slutföra inbjudningsprocessen.

![Gästanvändares granskningsbehörighet](./media/role-assignments-external-users/invite-review-permissions.png)

Mer information om inbjudningsprocessen finns i [Azure Active Directory B2B-inlösen av samarbetsinbjudan](../active-directory/b2b/redemption-experience.md).

## <a name="add-a-role-assignment-for-a-guest-user"></a>Lägga till en rolltilldelning för en gästanvändare

I RBAC, för att bevilja åtkomst, tilldelar du en roll. Om du vill lägga till en rolltilldelning för en gästanvändare följer du [samma steg](role-assignments-portal.md#add-a-role-assignment) som för en medlemsanvändare, grupp, tjänsthuvudnamn eller hanterad identitet. Följ dessa steg lägga till en rolltilldelning för en gästanvändare med olika omfattningar.

1. Klicka på **Alla tjänster** i Azure-portalen.

1.  Välj den uppsättning resurser som åtkomsten gäller för, även kallat scope. Du kan till exempel välja **Hanteringsgrupper,** **Prenumerationer,** **Resursgrupper**eller en resurs.

1. Klicka på den specifika resursen.

1. Klicka på **Åtkomstkontroll (IAM)**.

    Följande skärmbild visar ett exempel på bladet Åtkomstkontroll (IAM) för en resursgrupp. Om du gör några ändringar i åtkomstkontrollen här gäller de bara för resursgruppen.

    ![IAM-blad (Access Control) för en resursgrupp](./media/role-assignments-external-users/access-control-resource-group.png)

1. Klicka på fliken **Rolltilldelningar** om du vill visa alla rolltilldelningar i det här scopet.

1. Klicka på **Lägg** > **till till rolltilldelning** för att öppna fönstret Lägg till rolltilldelning.

    Om du inte har behörighet att tilldela roller är alternativet Lägg till rolltilldelning inaktiverat.

    ![Menyn Lägg till](./media/role-assignments-external-users/add-menu.png)

1. I listrutan **Roll** väljer du en roll, till exempel **Virtuell datordeltagare**.

1. Välj gästanvändaren i listan **Välj.** Om du inte ser användaren i listan kan du skriva i rutan **Välj** för att söka i katalogen efter visningsnamn, e-postadresser och objektidentifierare.

   ![Fönsterrutan Lägg till rolltilldelning](./media/role-assignments-external-users/add-role-assignment.png)

1. Klicka på **Spara** om du vill tilldela rollen i det valda scopet.

    ![Rolltilldelning för deltagare i virtuella datorer](./media/role-assignments-external-users/access-control-role-assignments.png)

## <a name="add-a-role-assignment-for-a-guest-user-not-yet-in-your-directory"></a>Lägga till en rolltilldelning för en gästanvändare som ännu inte finns i katalogen

Om du vill lägga till en rolltilldelning för en gästanvändare följer du [samma steg](role-assignments-portal.md#add-a-role-assignment) som för en medlemsanvändare, grupp, tjänsthuvudnamn eller hanterad identitet.

Om gästanvändaren ännu inte finns i katalogen kan du bjuda in användaren direkt från fönstret Lägg till rolltilldelning.

1. Klicka på **Alla tjänster** i Azure-portalen.

1.  Välj den uppsättning resurser som åtkomsten gäller för, även kallat scope. Du kan till exempel välja **Hanteringsgrupper,** **Prenumerationer,** **Resursgrupper**eller en resurs.

1. Klicka på den specifika resursen.

1. Klicka på **Åtkomstkontroll (IAM)**.

1. Klicka på fliken **Rolltilldelningar** om du vill visa alla rolltilldelningar i det här scopet.

1. Klicka på **Lägg** > **till till rolltilldelning** för att öppna fönstret Lägg till rolltilldelning.

    ![Menyn Lägg till](./media/role-assignments-external-users/add-menu.png)

1. I listrutan **Roll** väljer du en roll, till exempel **Virtuell datordeltagare**.

1. I listan **Välj** skriver du e-postadressen till den person som du vill bjuda in och väljer den personen.

   ![Bjuda in gästanvändare i fönstret Lägg till rolltilldelning](./media/role-assignments-external-users/add-role-assignment-new-guest.png)

1. Klicka på **Spara** om du vill lägga till gästanvändaren i katalogen, tilldela rollen och skicka en inbjudan.

    Efter en stund visas en avisering om rolltilldelningen och information om inbjudan.

    ![Rolltilldelning och inbjudna användarmeddelanden](./media/role-assignments-external-users/invited-user-notification.png)

1. Om du vill bjuda in gästanvändaren manuellt högerklickar du och kopierar inbjudningslänken i meddelandet. Klicka inte på inbjudningslänken eftersom inbjudningsprocessen startas.

    Inbjudningslänken har följande format:

    `https://invitations.microsoft.com/redeem/...`

1. Skicka inbjudan till gästanvändaren för att slutföra inbjudningsprocessen.

    Mer information om inbjudningsprocessen finns i [Azure Active Directory B2B-inlösen av samarbetsinbjudan](../active-directory/b2b/redemption-experience.md).

## <a name="remove-a-guest-user-from-your-directory"></a>Ta bort en gästanvändare från katalogen

Innan du tar bort en gästanvändare från en katalog bör du först ta bort alla rolltilldelningar för gästanvändaren. Följ dessa steg för att ta bort en gästanvändare från en katalog.

1. Öppna **åtkomstkontroll (IAM)** i ett scope, till exempel hanteringsgrupp, prenumeration, resursgrupp eller resurs, där gästanvändaren har en rolltilldelning.

1. Klicka på fliken **Rolltilldelningar** om du vill visa alla rolltilldelningar.

1. Lägg till en bock bredvid gästanvändaren med den rolltilldelning som du vill ta bort i listan över rolltilldelningar.

   ![Ta bort rolltilldelning](./media/role-assignments-external-users/remove-role-assignment-select.png)

1. Klicka på **Ta bort**.

   ![Ta bort rolltilldelningsmeddelande](./media/role-assignments-external-users/remove-role-assignment.png)

1. I meddelandet om att ta bort rolltilldelningen klickar du på **Ja**.

1. Klicka på **Azure Active Directory** > **Users**i det vänstra navigeringsfältet .

1. Klicka på den gästanvändare som du vill ta bort.

1. Klicka på **Ta bort**.

   ![Ta bort gästanvändare](./media/role-assignments-external-users/delete-guest-user.png)

1. Klicka på **Ja**i borttagningsmeddelandet som visas .

## <a name="troubleshoot"></a>Felsöka

### <a name="guest-user-cannot-browse-the-directory"></a>Gästanvändaren kan inte bläddra i katalogen

Gästanvändare har begränsade katalogbehörigheter. Gästanvändare kan till exempel inte bläddra i katalogen och kan inte söka efter grupper eller program. Mer information finns [i Vilka är standardanvändarbehörigheterna i Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md)

![Gästanvändaren kan inte bläddra bland användare i en katalog](./media/role-assignments-external-users/directory-no-users.png)

Om en gästanvändare behöver ytterligare behörigheter i katalogen kan du tilldela gästanvändaren en katalogroll. Om du verkligen vill att en gästanvändare ska ha fullständig läsbehörighet till din katalog kan du lägga till gästanvändaren i rollen [Katalogläsare](../active-directory/users-groups-roles/directory-assign-admin-roles.md) i Azure AD. Mer information finns i [Bevilja behörigheter till användare från partnerorganisationer i din Azure Active Directory-klient](../active-directory/b2b/add-guest-to-role.md).

![Tilldela rollen Katalogläsare](./media/role-assignments-external-users/directory-roles.png)

### <a name="guest-user-cannot-browse-users-groups-or-service-principals-to-assign-roles"></a>Gästanvändare kan inte bläddra bland användare, grupper eller tjänsthuvudnamn för att tilldela roller

Gästanvändare har begränsade katalogbehörigheter. Även om en gästanvändare är [en ägare](built-in-roles.md#owner) i ett scope, om de försöker lägga till en rolltilldelning för att ge någon annan åtkomst, kan de inte bläddra i listan över användare, grupper eller tjänsthuvudnamn.

![Gästanvändare kan inte bläddra i säkerhetsobjekt för att tilldela roller](./media/role-assignments-external-users/directory-no-browse.png)

Om gästanvändaren känner till någons exakta inloggningsnamn i katalogen kan de bevilja åtkomst. Om du verkligen vill att en gästanvändare ska ha fullständig läsbehörighet till din katalog kan du lägga till gästanvändaren i rollen [Katalogläsare](../active-directory/users-groups-roles/directory-assign-admin-roles.md) i Azure AD. Mer information finns i [Bevilja behörigheter till användare från partnerorganisationer i din Azure Active Directory-klient](../active-directory/b2b/add-guest-to-role.md).

### <a name="guest-user-cannot-register-applications-or-create-service-principals"></a>Gästanvändare kan inte registrera program eller skapa tjänsthuvudnamn

Gästanvändare har begränsade katalogbehörigheter. Om en gästanvändare behöver kunna registrera program eller skapa tjänsthuvudnamn kan du lägga till gästanvändaren i rollen [Programutvecklare](../active-directory/users-groups-roles/directory-assign-admin-roles.md) i Azure AD. Mer information finns i [Bevilja behörigheter till användare från partnerorganisationer i din Azure Active Directory-klient](../active-directory/b2b/add-guest-to-role.md).

![Gästanvändare kan inte registrera program](./media/role-assignments-external-users/directory-access-denied.png)

### <a name="guest-user-does-not-see-the-new-directory"></a>Gästanvändaren ser inte den nya katalogen

Om en gästanvändare har beviljats åtkomst till en katalog, men de inte ser den nya katalogen som anges i Azure-portalen när de försöker växla i sin **katalog + prenumerationsfönster,** kontrollerar du att gästanvändaren har slutfört inbjudningsprocessen. Mer information om inbjudningsprocessen finns i [Azure Active Directory B2B-inlösen av samarbetsinbjudan](../active-directory/b2b/redemption-experience.md).

### <a name="guest-user-does-not-see-resources"></a>Gästanvändaren ser inte resurser

Om en gästanvändare har beviljats åtkomst till en katalog, men de inte ser de resurser som de har beviljats åtkomst till i Azure-portalen, kontrollerar du att gästanvändaren har valt rätt katalog. En gästanvändare kan ha åtkomst till flera kataloger. Om du vill byta kataloger klickar du på **Katalog + prenumeration**längst upp till vänster och klickar sedan på lämplig katalog.

![Fönstret Kataloger + Prenumerationer i Azure-portalen](./media/role-assignments-external-users/directory-subscription.png)

## <a name="next-steps"></a>Nästa steg

- [Lägga till Azure Active Directory B2B-samarbetsanvändare i Azure-portalen](../active-directory/b2b/add-users-administrator.md)
- [Egenskaper för en Azure Active Directory B2B-samarbetsanvändare](../active-directory/b2b/user-properties.md)
- [Elementen i e-postmeddelandet med inbjudan till B2B-samarbete - Azure Active Directory](../active-directory/b2b/invitation-email-elements.md)
- [Lägga till en gästanvändare som medadministratör](classic-administrators.md#add-a-guest-user-as-a-co-administrator)