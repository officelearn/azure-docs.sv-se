---
title: Klassiska Azure-prenumerationsadministratörer | Microsoft-dokument
description: Beskriver hur du lägger till eller ändrar Azure-medadministratörs- och tjänstadministratörsrollerna och hur du visar kontoadministratören.
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
ms.date: 01/22/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 2493e893f9afda0642bd838c94538dd0b984bce5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243489"
---
# <a name="azure-classic-subscription-administrators"></a>Klassiska Azure-prenumerationsadministratörer

Microsoft rekommenderar att du hanterar åtkomst till Azure-resurser med hjälp av rollbaserad åtkomstkontroll (RBAC). Men om du fortfarande använder den klassiska distributionsmodellen måste du använda en klassisk administratörsroll för prenumeration: Tjänstadministratör och medadministratör. Mer information finns i [Azure Resource Manager jämfört med klassisk distribution](../azure-resource-manager/management/deployment-models.md).

I den här artikeln beskrivs hur du lägger till eller ändrar rollerna medadministratör och tjänstadministratör och hur du visar kontoadministratören.

## <a name="add-a-co-administrator"></a>Lägga till en medadministratör

> [!TIP]
> Du behöver bara lägga till en medadministratör om användaren behöver hantera klassiska Azure-distributioner med hjälp av [Azure Service Management PowerShell Module](https://docs.microsoft.com/powershell/module/servicemanagement/azure). Om användaren bara använder Azure-portalen för att hantera de klassiska resurserna behöver du inte lägga till den klassiska administratören för användaren.

1. Logga in på [Azure-portalen](https://portal.azure.com) som tjänstadministratör eller medadministratör.

1. Öppna [Prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) och välj en prenumeration.

    Medadministratörer kan endast tilldelas i prenumerationsomfånget.

1. Klicka på **Åtkomstkontroll (IAM)**.

1. Klicka på fliken **Klassiska administratörer.**

    ![Skärmbild som öppnar Klassiska administratörer](./media/classic-administrators/classic-administrators.png)

1. Klicka på **Lägg till** > **medadministratör** för att öppna fönstret Lägg till medadministratörer.

    Om alternativet Lägg till medadministratör är inaktiverat har du inga behörigheter.

1. Markera den användare som du vill lägga till och klicka på **Lägg till**.

    ![Skärmbild som lägger till medadministratör](./media/classic-administrators/add-coadmin.png)

## <a name="add-a-guest-user-as-a-co-administrator"></a>Lägga till en gästanvändare som medadministratör

Om du vill lägga till en gästanvändare som medadministratör följer du samma steg som i föregående [lägg till en medadministratör.](#add-a-co-administrator) Gästanvändaren måste uppfylla följande kriterier:

- Gästanvändaren måste ha en närvaro i katalogen. Det innebär att användaren blev inbjuden till din katalog och accepterade inbjudan.

Mer information om hur du lägger till en gästanvändare i katalogen finns [i Lägga till Azure Active Directory B2B-samarbetsanvändare i Azure-portalen](../active-directory/b2b/add-users-administrator.md).

### <a name="differences-for-guest-users"></a>Skillnader för gästanvändare

Gästanvändare som har tilldelats rollen Medadministratör kan se vissa skillnader jämfört med medlemsanvändare med rollen Medadministratör. Föreställ dig följande scenario:

- Användare A med ett Azure AD-konto (arbets- eller skolkonto) är tjänstadministratör för en Azure-prenumeration.
- Användare B har ett Microsoft-konto.
- Användare A tilldelar rollen medadministratör till användare B.
- Användare B kan göra nästan allt, men kan inte registrera program eller slå upp användare i Azure AD-katalogen.

Du förväntar dig att användare B kan hantera allt. Anledningen till den här skillnaden är att Microsoft-kontot läggs till i prenumerationen som gästanvändare i stället för en medlemsanvändare. Gästanvändare har olika standardbehörigheter i Azure AD jämfört med medlemsanvändare. Medlemsanvändare kan till exempel läsa andra användare i Azure AD och gästanvändare kan inte. Medlemsanvändare kan registrera nya tjänsthuvudnamn i Azure AD och gästanvändare kan inte.

Om en gästanvändare behöver kunna utföra dessa uppgifter är en möjlig lösning att tilldela de specifika Azure AD-administratörsrollerna som gästanvändaren behöver. I föregående scenario kan du till exempel tilldela rollen [Katalogläsare](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) för att läsa andra användare och tilldela rollen [Programutvecklare](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-developer) för att kunna skapa tjänsthuvudnamn. Mer information om medlems- och gästanvändare och deras behörigheter finns [i Vilka är standardanvändarbehörigheterna i Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md) Mer information om hur du beviljar åtkomst för gästanvändare finns i [Hantera åtkomst till Azure-resurser för externa gästanvändare med RBAC](role-assignments-external-users.md).

Observera att de [inbyggda rollerna för Azure-resurser](../role-based-access-control/built-in-roles.md) skiljer sig från [Azure AD-administratörsrollerna](../active-directory/users-groups-roles/directory-assign-admin-roles.md). De inbyggda rollerna ger inte någon åtkomst till Azure AD. Mer information finns i [Förstå de olika rollerna](../role-based-access-control/rbac-and-directory-admin-roles.md).

Information som jämför medlemsanvändare och gästanvändare finns [i Vilka är standardanvändarbehörigheterna i Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md)

## <a name="remove-a-co-administrator"></a>Ta bort en medadministratör

1. Logga in på [Azure-portalen](https://portal.azure.com) som tjänstadministratör eller medadministratör.

1. Öppna [Prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) och välj en prenumeration.

1. Klicka på **Åtkomstkontroll (IAM)**.

1. Klicka på fliken **Klassiska administratörer.**

1. Lägg till en bock bredvid den medadministratör som du vill ta bort.

1. Klicka på **Ta bort**.

1. Klicka på **Ja**i meddelanderutan som visas .

    ![Skärmbild som tar bort medadministratören](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>Ändra tjänstadministratör

Endast kontoadministratören kan ändra tjänstadministratören för en prenumeration. När du registrerar dig för en Azure-prenumeration är serviceadministratören som standard samma som kontoadministratören. Användaren med rollen Kontoadministratör har ingen åtkomst till Azure-portalen. Användaren med rollen Tjänstadministratör har fullständig åtkomst till Azure-portalen. Om kontoadministratören och tjänstadministratören är samma användare och du ändrar tjänstadministratören till en annan användare förlorar kontoadministratören åtkomsten till Azure-portalen. Kontoadministratören kan dock alltid använda Account Center för att ändra tillbaka tjänstadministratören till sig själva.

Så här ändrar du tjänstadministratören i **Account Center**.

### <a name="account-center"></a>Kontocenter

1. Kontrollera att ditt scenario stöds genom att kontrollera [begränsningarna för att ändra tjänstadministratören](#limitations-for-changing-the-service-administrator).

1. Logga in [på Kontocenter](https://account.windowsazure.com/subscriptions) som kontoadministratör.

1. Klicka på en prenumeration.

1. Klicka på Redigera **prenumerationsinformation**till höger.

    ![Skärmbild som visar knappen Redigera prenumeration i Kontocenter](./media/classic-administrators/editsub.png)

1. Ange den nya tjänstadministratörens e-postadress i rutan **TJÄNSTADMINISTRATÖR.**

    ![Skärmbild som visar rutan för att ändra e-postmeddelandet med serviceadministratör](./media/classic-administrators/change-service-admin.png)

1. Klicka på bocken för att spara ändringen.

### <a name="limitations-for-changing-the-service-administrator"></a>Begränsningar för att ändra tjänstadministratören

Det kan bara finnas en tjänstadministratör per Azure-prenumeration. Om du ändrar tjänstadministratören fungerar du annorlunda beroende på om kontoadministratören är ett Microsoft-konto eller om det är ett Azure AD-konto (arbets- eller skolkonto).

| Kontoadministratörskonto | Kan du ändra tjänstadministratören till ett annat Microsoft-konto? | Kan du ändra tjänstadministratören till ett Azure AD-konto i samma katalog? | Kan du ändra tjänstadministratören till ett Azure AD-konto i en annan katalog? |
| --- | --- | --- | --- |
| Microsoft-konto | Ja | Inga | Inga |
| Azure AD-konto | Ja | Ja | Inga |

Om kontoadministratören är ett Azure AD-konto kan du ändra tjänstadministratören till ett Azure AD-konto i samma katalog, men inte i en annan katalog. Kan till abby@contoso.com exempel ändra tjänstadministratören till bob@contoso.com, men john@notcontoso.com kan john@notcontoso.com inte ändra tjänstadministratören till om inte den finns i contoso.com katalogen.

Mer information om Microsoft-konton och Azure AD-konton finns i [Vad är Azure Active Directory?](../active-directory/fundamentals/active-directory-whatis.md).

## <a name="view-the-account-administrator"></a>Visa kontoadministratören

Kontoadministratören är den användare som ursprungligen registrerade sig för Azure-prenumerationen och ansvarar som faktureringsägare för prenumerationen. Information om hur du ändrar kontoadministratören för en prenumeration finns i [Överföra ägarskap för en Azure-prenumeration till ett annat konto](../cost-management-billing/manage/billing-subscription-transfer.md).

Följ dessa steg för att visa kontoadministratören.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Öppna [Prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) och välj en prenumeration.

1. Klicka på **Egenskaper**.

    Prenumerationens kontoadministratör visas i rutan **Kontoadministratör.**

    ![Skärmbild som visar kontoadministratören](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>Nästa steg

* [Förstå de olika rollerna i Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Hantera åtkomst till Azure-resurser med hjälp av RBAC och Azure-portalen](../role-based-access-control/role-assignments-portal.md)
* [Lägga till eller ändra Azure-prenumerationsadministratörer](../cost-management-billing/manage/add-change-subscription-administrator.md)
