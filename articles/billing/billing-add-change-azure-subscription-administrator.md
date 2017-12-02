---
title: "Lägga till eller ändra Azure-prenumeration för administratörsroller | Microsoft Docs"
description: "Beskriver hur du lägger till eller ändrar Medadministratör för Azure, tjänstadministratören och kontoadministratör"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/30/2017
ms.author: genli
ms.openlocfilehash: d78174cd968c0f918a07027daf1e59665d6b6c1e
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2017
---
# <a name="add-or-change-azure-administrator-roles-that-manage-the-subscription-or-services"></a>Lägg till eller ändra Azure-administratörsroller som hanterar prenumerationen eller tjänster

Du kan ändra Azure administratören som hanterar din Azure-prenumeration eller hanterar Azure-tjänster används i din prenumeration. Om du vill visa faktureringsinformation för Azure och hantera prenumerationer, måste du logga in på mitt konto som kontoadministratör. 

<a name="add-an-admin-for-a-subscription"></a>

## <a name="add-an-rbac-owner-admin-for-a-subscription-in-azure-portal"></a>Lägg till administratör RBAC ägare för en prenumeration på Azure-portalen 

Om du vill lägga till en person som administratör för en prenumeration på Azure-portalen, rekommenderar vi ger dem ett [RBAC](../active-directory/role-based-access-control-configure.md) ägarrollen. Rollen som ägare kan hantera resurserna i prenumerationen du tilldelade och har inte behörighet att använda till andra prenumerationer. Ägare som du lägger till via den [Azure-portalen](https://portal.azure.com) kan inte hantera resurs i den [klassiska Azure-portalen](https://manage.windowsazure.com).

1. Logga in på den [prenumerationer visa i Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Välj den prenumeration som du vill att administratören att komma åt.
1. Välj **åtkomstkontroll (IAM)** på menyn.
1. Välj **lägga till** > **rollen** > **ägare**. Skriv e-postadressen för den användare som du vill lägga till som ägare, Välj användaren och välj sedan **spara**.

    ![Skärmbild som visar ägarrollen som valts](./media/billing-add-change-azure-subscription-administrator/add-role.png)

## <a name="add-or-change-co-administrator"></a>Lägga till eller ändra medadministratör

Endast en ägare kan läggas till som medadministratör. Andra användare med roller som deltagare och läsare kan inte läggas till som medadministratörer.

1. Om du inte redan gjort lägger du till någon som ägare följa anvisningarna ovan.
2. **Högerklicka på** ägare användaren du just lagt till och välj sedan **Lägg till som medadministratör**. Om du inte ser den **Lägg till som medadministratör** alternativet ny sidan eller försök med en annan webbläsare. 

     ![Skärmbild som lägger till medadministratör](./media/billing-add-change-azure-subscription-administrator/add-coadmin.png)

    >[!TIP]
    >Du måste lägga till kontot ”ägare” som medadministratör om användaren behöver hantera Azure-tjänster i [klassiska Azure-portalen](https://manage.windowsazure.com/).

    Ta bort behörigheten medadministratör **högerklickar du på** ”medadministratör” användar- och välj sedan **ta bort medadministratör**.

    ![Skärmbild som tar bort medadministratör](./media/billing-add-change-azure-subscription-administrator/remove-coadmin.png)

<a name="change-service-administrator-for-a-subscription"></a>

## <a name="change-the-service-administrator-for-an-azure-subscription"></a>Ändra tjänstadministratör för en Azure-prenumeration

Enbart administratörskontot kan ändra den tjänstadministratör för en prenumeration. Som standard när du registrerar dig, är administratören samma som kontoadministratör.

1. Se till att ditt scenario stöds genom att kontrollera den [gränser för att ändra tjänstadministratörer](#limits).
1. Logga in på [Kontocenter](https://account.windowsazure.com/subscriptions) som kontoadministratör.
1. Välj en prenumeration.
1. Välj höger, **redigera prenumerationsinformation**.

    ![Skärmbild som visar knappen Redigera prenumeration i Account Center](./media/billing-add-change-azure-subscription-administrator/editsub.png)
1. I den **TJÄNSTADMINISTRATÖR** ange e-postadressen för den nya tjänstadministratören.

    ![Skärmbild som visar rutan för att ändra tjänstadministratör e-postmeddelandet](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

<a name="limits"></a>

### <a name="limitations-for-changing-service-administrators"></a>Begränsningar för att ändra tjänstadministratörer

* Varje prenumeration är associerad med en Azure AD-katalog. Gå till för att hitta katalogen prenumerationen är associerad med den [klassiska Azure-portalen](https://manage.windowsazure.com/)väljer **inställningar** > **prenumerationer**. Kontrollera prenumerations-ID för att hitta katalogen.
* Om du har loggat in med ett arbets- eller Skol-konto kan du lägga till andra konton i din organisation som tjänstadministratör. Till exempel abby@contoso.com kan lägga till bob@contoso.com som tjänstadministratör men det går inte att lägga till john@notcontoso.com om john@notcontoso.com har finns i katalogen contoso.com. Användare som har loggat in med konton för arbetet eller skolan kan fortsätta att lägga till Account användare som tjänstadministratör.

  | Inloggningsmetod | Lägg till Microsoft Account user som SA? | Lägg till konto för arbetet eller skolan i samma organisation som SA? | Lägg till konto för arbetet eller skolan i annan organisation som SA? |
  | --- | --- | --- | --- |
  |  Microsoft-konto |Ja |Nej |Nej |
  |  Arbets- eller Skolkonto |Ja |Ja |Nej |

## <a name="change-the-account-administrator-for-an-azure-subscription"></a>Ändra kontoadministratör för en Azure-prenumeration

Om du vill ändra kontoadministratör för en prenumeration [överför ägarskapet för en Azure-prenumeration till ett annat konto](billing-subscription-transfer.md).

<a name="check-the-account-administrator-of-the-subscription"></a>

**Osäker som kontoadministratör?** Följ de här stegen:

1. Logga in på den [prenumerationer visa i Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Välj den prenumeration som du vill kontrollera och se **inställningar**.
1. Välj **egenskaper**. Kontoadministratören för prenumerationen visas i den **kontoadministratören** rutan.  

## <a name="types-of-azure-admin-accounts"></a>Typer av Azure administratörskonton

 Kontoadministratör tjänstadministratören och medadministratör finns tre typer av administratörsroller i Microsoft Azure. I följande tabell beskrivs skillnaden mellan dessa administrativa roller.

| Administrativ roll | Gräns | Beskrivning |
| --- | --- | --- |
| Kontoadministratör (AA) |1 per Azure-konto |Det här är den person som registrerade sig för eller har köpt Azure-prenumerationer och har behörighet att komma åt den [Kontocenter](https://account.azure.com/Subscriptions) och utföra olika hanteringsuppgifter. Dessa inkluderar att kunna skapa prenumerationer, avbryta prenumerationer, ändra faktureringen för en prenumeration och ändra tjänstadministratören. |
| Tjänsten systemadministratörens (SA) |1 per Azure-prenumeration |Den här rollen har behörighet att hantera tjänster i den [Azure-portalen](https://portal.azure.com). Kontoadministratören är också den tjänstadministratör som standard för en ny prenumeration. |
| Medadministratör (CA) i den [klassiska Azure-portalen](https://manage.windowsazure.com) |200 per prenumeration |Den här rollen har samma åtkomstbehörigheter som tjänstadministratören, men kan inte ändra associationen mellan prenumerationer och Azure-kataloger. |

Azure Active Directory-rollbaserad åtkomstkontroll (RBAC) kan användare som ska läggas till flera roller. Mer information finns i [Azure Active Directory-rollbaserad åtkomstkontroll](../active-directory/role-based-access-control-configure.md).


## <a name="learn-more-about-resource-access-control-and-active-directory"></a>Mer information om åtkomstkontroll till resurser och Active Directory

* Läs mer om hur resursåtkomsten hanteras i Microsoft Azure i [förstå resursåtkomst i Azure](../active-directory/active-directory-understanding-resource-access.md).
* Läs mer om Azure Active Directory [hur Azure-prenumerationer är associerade med Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md) och [Tilldela administratörsroller i Azure Active Directory](../active-directory/active-directory-assign-admin-roles-azure-portal.md).

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.

Om du fortfarande behöver hjälp [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) få snabbt lösa problemet.
