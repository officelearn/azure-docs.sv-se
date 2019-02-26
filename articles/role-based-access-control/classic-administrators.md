---
title: Azure klassiska prenumerationsadministratörer | Microsoft Docs
description: Beskriver hur du lägger till eller ändrar rollerna delad administratör för Azure och tjänstadministratör och hur du visar kontoadministratör.
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
ms.openlocfilehash: 3babcf0562a2f817f6ebd1fcc66df6d655d905bb
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2019
ms.locfileid: "56807158"
---
# <a name="azure-classic-subscription-administrators"></a>Azure klassiska prenumerationsadministratörer

Microsoft rekommenderar att du hanterar åtkomst till Azure-resurser med hjälp av rollbaserad åtkomstkontroll (RBAC). Om du fortfarande använder den klassiska distributionsmodellen, ska du dock behöva använda en administratörsroll för klassiska prenumerationer: Tjänstadministratör och delad administratör. Mer information finns i [Azure Resource Manager och klassisk distribution](../azure-resource-manager/resource-manager-deployment-model.md).

Den här artikeln beskrivs hur du lägger till eller ändrar rollerna delad administratör och administratör för tjänsten och hur du visar kontoadministratör.

## <a name="add-a-co-administrator"></a>Lägg till en Medadministratör

> [!TIP]
> Du behöver bara lägga till en delad administratör om användaren behöver hantera klassiska Azure-distributioner med [Azure Service Management PowerShell-modulen](https://docs.microsoft.com/en-us/powershell/module/servicemanagement/azure). Om användaren använder endast Azure-portalen för att hantera klassiska resurser, behöver du inte lägga till klassisk administratör för användaren.

1. Logga in på den [Azure-portalen](https://portal.azure.com) som en tjänstadministratör.

1. Öppna [prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) och välja en prenumeration.

    Medadministratörer kan endast tilldelas prenumerationsområde.

1. Klicka på **Åtkomstkontroll (IAM)**.

1. Klicka på den **klassiska administratörer** fliken.

    ![Skärmbild som öppnas klassiska administratörer](./media/classic-administrators/classic-administrators.png)

1. Klicka på **Lägg till** > **Lägg till delad administratör** att öppna fönstret Lägg till medadministratörer.

    Om alternativet Lägg till delad administratör har inaktiverats kan behörighet du inte.

1. Välj den användare som du vill lägga till och klickar på **Lägg till**.

    ![Skärmbild som lägger till medadministratör](./media/classic-administrators/add-coadmin.png)

### <a name="adding-a-guest-user-as-a-co-administrator"></a>Att lägga till en gästanvändare som en Medadministratör

[Gästanvändare](../active-directory/b2b/b2b-quickstart-add-guest-users-portal.md) som har tilldelats Medadministratörerna rollen kan se några skillnader jämfört med användare med rollen delad administratör. Föreställ dig följande:

- Användare A med ett Azure AD arbets- eller skolkonto är tjänstadministratör för en Azure-prenumeration.
- Användare B har ett Microsoft-konto.
- Användare A tilldelar rollen som Medadministratör till användaren B.
- Användare B kan göra nästan allt, men inte kan registrera program eller leta upp användare i Azure AD-katalog.

Du kan förvänta dig som användare B kan hantera allt. Orsaken till den här skillnaden är att Microsoft-kontot har lagts till prenumerationen som gästanvändare i stället för en medlemsanvändare. Gästanvändare har olika standardbehörigheter i Azure AD jämfört med användare. Till exempel användare kan läsa andra användare i Azure AD och det går inte att gästanvändare. Användare kan registrera nya tjänstens huvudnamn i Azure AD och det går inte att gästanvändare. Om en gästanvändare behöver för att kunna utföra dessa uppgifter, en möjlig lösning är att tilldela specifikt Azure AD-administratörsroller gästanvändaren behöver. Till exempel i scenariot ovan tilldelar du den [Katalogläsare](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) roll att läsa andra användare och tilldela den [programutvecklare](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-developer) roll för att kunna skapa tjänstens huvudnamn. Läs mer om medlemmar och gästanvändare och deras behörigheter, [vad är standardbehörigheterna för användare i Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

Observera att den [inbyggda roller för Azure-resurser](../role-based-access-control/built-in-roles.md) skiljer sig från den [Azure AD-administratörsroller](../active-directory/users-groups-roles/directory-assign-admin-roles.md). De inbyggda rollerna bevilja inte åtkomst till Azure AD. Mer information finns i [förstå de olika rollerna](../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="remove-a-co-administrator"></a>Ta bort Medadministratör

1. Logga in på den [Azure-portalen](https://portal.azure.com) som en tjänstadministratör.

1. Öppna [prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) och välja en prenumeration.

1. Klicka på **Åtkomstkontroll (IAM)**.

1. Klicka på den **klassiska administratörer** fliken.

1. Lägg till en bock bredvid Medadministratör som du vill ta bort.

1. Klicka på **ta bort**.

1. I meddelanderutan som visas klickar du på **Ja**.

    ![Skärmbild som tar bort medadministratör](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>Ändra tjänstadministratör

Är bara kontoadministratören kan ändra tjänstadministratör för en prenumeration. Som standard när du registrerar dig för en Azure-prenumeration är tjänstadministratören samma som kontoadministratör. Användare med rollen Administratör för kontot har ingen åtkomst till Azure-portalen. Användare med rollen som tjänstadministratör har fullständig åtkomst till Azure-portalen. Om du ändrar tjänstadministratör till en annan användare, kontoadministratören och tjänstadministratören är samma användare förlorar åtkomst till Azure portal med kontoadministratör. Kontoadministratören kan dock alltid använda Kontocenter för att ändra tjänstadministratör tillbaka till sig själva.

Det finns två sätt att ändra tjänstadministratör. Du kan ändra i den **Azure-portalen** eller **Kontocenter**.

### <a name="azure-portal"></a>Azure Portal

1. Kontrollera ditt scenario stöds genom att markera den [begränsningar för att ändra tjänstadministratörer](#limits).

1. Logga in på den [Azure-portalen](https://portal.azure.com) som kontoadministratör.

1. Öppna [prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) och välja en prenumeration.

1. Klicka på **Egenskaper**.

    ![Skärmbild som visar kontoadministratör](./media/classic-administrators/account-admin.png)

1. Överst på sidan klickar du på **tjänstadministratören** att öppna fönstret Service-administratör.

    Om knappen tjänstadministratören har inaktiverats kan behörighet du inte. Endast den användare som är kontoadministratör kan ändra tjänstadministratör.

1. Välj en ny tjänstadministratör och klicka sedan på **spara**.

### <a name="account-center"></a>Kontocenter

1. Kontrollera ditt scenario stöds genom att markera den [begränsningar för att ändra tjänstadministratörer](#limits).

1. Logga in på [Kontocenter](https://account.windowsazure.com/subscriptions) som kontoadministratör.

1. Klicka på en prenumeration.

1. Till höger klickar du på **redigera prenumerationsinformation**.

    ![Skärmbild som visar knappen Redigera prenumeration i Kontocenter](./media/classic-administrators/editsub.png)

1. I den **TJÄNSTADMINISTRATÖR** anger du e-postadressen för den nya tjänstadministratör.

    ![Skärmbild som visar rutan för att ändra tjänstadministratör e-postmeddelandet](./media/classic-administrators/change-service-admin.png)

1. Klicka på bockmarkeringen för att spara ändringen.

### <a name="limitations-for-changing-the-service-administrator"></a>Begränsningar för att ändra tjänstadministratör

Varje prenumeration är associerad med en Azure AD-katalog. För att hitta katalogen som prenumerationen är associerad med kan öppna **prenumerationer** i Azure-portalen och välj sedan en prenumeration på finns i katalogen.

Om du har loggat in med ett arbets- eller skolkonto konto kan du lägga till andra konton i din organisation som tjänstadministratör. Till exempel abby@contoso.com kan lägga till bob@contoso.com som tjänstadministratören, men det går inte att lägga till john@notcontoso.com såvida inte john@notcontoso.com har närvaro i katalogen contoso.com. Användare som loggat in med arbets- eller skolkonton kan fortsätta att lägga till användare av Microsoft-konton som tjänstadministratör.

  | Inloggningsmetod | Lägga till Microsoft-kontot som en tjänstadministratör? | Lägg till arbets-eller skolkonto i samma organisation som en tjänstadministratör? | Lägg till arbets-eller skolkonto i annan organisation som en tjänstadministratör? |
  | --- | --- | --- | --- |
  |  Microsoft-konto |Ja |Nej |Nej |
  |  Arbets- eller skolkonto |Ja |Ja |Nej |

## <a name="view-the-account-administrator"></a>Visa kontoadministratör

Kontoadministratören är användaren som ursprungligen registrerat dig för Azure-prenumeration och ansvarar som fakturering ägare till prenumerationen. Om du vill ändra kontoadministratör för en prenumeration, se [överföra ägarskap för en Azure-prenumeration till ett annat konto](../billing/billing-subscription-transfer.md).

Följ dessa steg om du vill visa kontoadministratör.

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Öppna [prenumerationer](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) och välja en prenumeration.

1. Klicka på **Egenskaper**.

    Kontoadministratör för prenumerationen visas i den **kontoadministratören** box.

    ![Skärmbild som visar kontoadministratör](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>Nästa steg

* [Förstå de olika rollerna i Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Hantera åtkomst till Azure-resurser med RBAC och Azure portal](../role-based-access-control/role-assignments-portal.md)
* [Lägg till eller ändra Azure-prenumerationsadministratörer](../billing/billing-add-change-azure-subscription-administrator.md)
