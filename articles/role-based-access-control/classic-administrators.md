---
title: Administratörer för klassisk Azure-prenumeration | Microsoft Docs
description: Beskriver hur du lägger till eller ändrar Azure-rollen som medadministratör och tjänst administratör och hur du visar konto administratören.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/19/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: c2096b6c5ddb72c8ab5c5d3203a05c94db51f6c5
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444336"
---
# <a name="azure-classic-subscription-administrators"></a>Administratörer för klassiska Azure-prenumerationer

Microsoft rekommenderar att du hanterar åtkomst till Azure-resurser med hjälp av rollbaserad åtkomst kontroll (RBAC). Men om du fortfarande använder den klassiska distributions modellen måste du använda en klassisk prenumerations administratörs roll: Tjänst administratör och delad administratör. Mer information finns i [Azure Resource Manager vs. Classic-distribution](../azure-resource-manager/resource-manager-deployment-model.md).

I den här artikeln beskrivs hur du lägger till eller ändrar rollen som medadministratör och tjänst administratör och hur du visar konto administratören.

## <a name="add-a-co-administrator"></a>Lägg till en gemensam administratör

> [!TIP]
> Du behöver bara lägga till en medadministratör om användaren behöver hantera klassiska Azure-distributioner med hjälp av [Azure Service Management PowerShell-modulen](https://docs.microsoft.com/powershell/module/servicemanagement/azure). Om användaren bara använder Azure Portal för att hantera de klassiska resurserna behöver du inte lägga till den klassiska administratören för användaren.

1. Logga in på [Azure Portal](https://portal.azure.com) som tjänst administratör.

1. Öppna [prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) och välj en prenumeration.

    Medadministratörer kan bara tilldelas i prenumerations omfånget.

1. Klicka på **åtkomstkontroll (IAM)** .

1. Klicka på fliken **klassiska administratörer** .

    ![Skärm bild som öppnar klassiska administratörer](./media/classic-administrators/classic-administrators.png)

1. Klicka på **Lägg** > till**Lägg till gemensam administratör** för att öppna fönstret Lägg till samadministratörer.

    Om alternativet Lägg till samadministratör är inaktiverat har du inte behörighet.

1. Välj den användare som du vill lägga till och klicka på **Lägg till**.

    ![Skärm bild som lägger till gemensam administratör](./media/classic-administrators/add-coadmin.png)

### <a name="adding-a-guest-user-as-a-co-administrator"></a>Lägga till en gäst användare som en medadministratör

[Gäst användare](../active-directory/b2b/b2b-quickstart-add-guest-users-portal.md) som har tilldelats rollen som medadministratör kan se vissa skillnader jämfört med medlems användare med rollen medadministratör. Tänk dig följande scenario:

- Användare A med ett arbets-eller skol konto för Azure AD är en tjänst administratör för en Azure-prenumeration.
- Användare B har en Microsoft-konto.
- Användare A tilldelar rollen som medadministratör till användare B.
- Användare B kan göra nästan allt, men kan inte registrera program eller söka efter användare i Azure AD-katalogen.

Du förväntar dig att användare B kan hantera allt. Orsaken till den här skillnaden är att Microsoft-konto läggs till i prenumerationen som gäst användare i stället för en medlems användare. Gäst användare har olika standard behörigheter i Azure AD jämfört med medlems användare. Medlems användare kan till exempel läsa andra användare i Azure AD och gäst användare kan inte. Medlems användare kan registrera nya tjänst huvud namn i Azure AD-och gäst användare kan inte. Om en gäst användare behöver kunna utföra dessa uppgifter, är en möjlig lösning att tilldela de Azure AD-administratörer som gäst användaren behöver. I det föregående scenariot kan du till exempel tilldela rollen [katalog läsare](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) för att läsa andra användare och tilldela rollen programutvecklare att kunna skapa tjänstens huvud namn. [](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-developer) Mer information om medlemmar och gäst användare och deras behörigheter finns i [Vad är standard användar behörigheter i Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

Observera att de [inbyggda rollerna för Azure-resurser](../role-based-access-control/built-in-roles.md) skiljer sig från administratörs rollerna för [Azure AD](../active-directory/users-groups-roles/directory-assign-admin-roles.md). De inbyggda rollerna beviljar inte åtkomst till Azure AD. Mer information finns i [förstå de olika rollerna](../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="remove-a-co-administrator"></a>Ta bort en gemensam administratör

1. Logga in på [Azure Portal](https://portal.azure.com) som tjänst administratör.

1. Öppna [prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) och välj en prenumeration.

1. Klicka på **åtkomstkontroll (IAM)** .

1. Klicka på fliken **klassiska administratörer** .

1. Lägg till en bock bredvid den medadministratör som du vill ta bort.

1. Klicka på **Ta bort**.

1. Klicka på **Ja**i meddelande rutan som visas.

    ![Skärm bild som tar bort gemensam administratör](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>Ändra tjänstadministratör

Endast konto administratören kan ändra tjänst administratör för en prenumeration. När du registrerar dig för en Azure-prenumeration är tjänst administratören som standard samma som konto administratören. Användaren med rollen konto administratör har ingen åtkomst till Azure Portal. Användaren med rollen tjänst administratör har fullständig åtkomst till Azure Portal. Om konto administratören och tjänst administratören är samma användare och du ändrar tjänst administratören till en annan användare förlorar konto administratören åtkomst till Azure Portal. Konto administratören kan dock alltid använda konto Center för att ändra tjänst administratören tillbaka till sig själva.

Det finns två sätt att ändra tjänstadministratör. Du kan ändra i **Azure Portal** eller **konto Center**.

### <a name="azure-portal"></a>Azure Portal

1. Kontrol lera att ditt scenario stöds genom att kontrol lera begränsningarna för att ändra tjänst administratörer.

1. Logga in på [Azure Portal](https://portal.azure.com) som konto administratör.

1. Öppna [prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) och välj en prenumeration.

1. Klicka på **Egenskaper**.

    ![Skärm bild som visar konto administratören](./media/classic-administrators/account-admin.png)

1. Klicka på **tjänst administratör** högst upp för att öppna fönstret tjänst administratör.

    Om knappen tjänst administratör är inaktive rad har du inte behörighet. Endast den användare som är konto administratör kan ändra tjänst administratören.

1. Välj en ny tjänst administratör och klicka sedan på **Spara**.

### <a name="account-center"></a>Kontocenter

1. Kontrol lera att ditt scenario stöds genom att kontrol lera begränsningarna för att ändra tjänst administratörer.

1. Logga in på [konto Center](https://account.windowsazure.com/subscriptions) som konto administratör.

1. Klicka på en prenumeration.

1. Klicka på **Redigera prenumerations information**på höger sida.

    ![Skärm bild som visar knappen Redigera prenumeration i konto Center](./media/classic-administrators/editsub.png)

1. I rutan **tjänst administratör** anger du e-postadressen för den nya tjänst administratören.

    ![Skärm bild som visar rutan för att ändra tjänst administratörens e-postadress](./media/classic-administrators/change-service-admin.png)

1. Klicka på bock markeringen för att spara ändringen.

### <a name="limitations-for-changing-the-service-administrator"></a>Begränsningar för att ändra tjänst administratör

Varje prenumeration är associerad med en Azure AD-katalog. Du hittar katalogen som prenumerationen är kopplad till genom att  öppna prenumerationer i Azure Portal och sedan välja en prenumeration för att se katalogen.

Om du är inloggad med ett arbets-eller skol konto kan du lägga till andra konton i organisationen som tjänst administratör. abby@contoso.com Kan till exempel lägga till bob@contoso.com som tjänst administratör, men kan inte john@notcontoso.com lägga john@notcontoso.com till om den inte har närvaro i contoso.com-katalogen. Användare som är inloggade med arbets-eller skol konton kan fortsätta att lägga till Microsoft-konto användare som tjänst administratör. Det kan bara finnas en tjänst administratör per Azure-prenumeration.

  | Inloggnings metod | Vill du lägga till Microsoft-konto användare som tjänst administratör? | Vill du lägga till ett arbets-eller skol konto i samma organisation som tjänst administratör? | Vill du lägga till ett arbets-eller skol konto i en annan organisation som tjänst administratör? |
  | --- | --- | --- | --- |
  |  Microsoft-konto |Ja |Nej |Nej |
  |  Arbets- eller skolkonto |Ja |Ja |Nej |

## <a name="view-the-account-administrator"></a>Visa konto administratören

Konto administratören är den användare som ursprungligen registrerade sig för Azure-prenumerationen och som ansvarar för prenumerationens fakturerings ägare. Information om hur du ändrar konto administratören för en prenumeration finns i [överföra ägarskapet för en Azure-prenumeration till ett annat konto](../billing/billing-subscription-transfer.md).

Följ dessa steg om du vill visa konto administratören.

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Öppna [prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) och välj en prenumeration.

1. Klicka på **Egenskaper**.

    Konto administratören för prenumerationen visas i rutan **konto administratör** .

    ![Skärm bild som visar konto administratören](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>Nästa steg

* [Förstå de olika rollerna i Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Hantera åtkomst till Azure-resurser med hjälp av RBAC och Azure-portalen](../role-based-access-control/role-assignments-portal.md)
* [Lägg till eller ändra Azure-prenumerationsadministratörer](../billing/billing-add-change-azure-subscription-administrator.md)
