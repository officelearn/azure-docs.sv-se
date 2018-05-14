---
title: Lägga till eller ändra Azure-prenumeration för administratörsroller | Microsoft Docs
description: Beskriver hur du lägger till eller ändrar Medadministratör för Azure, tjänstadministratören och kontoadministratör
services: ''
documentationcenter: ''
author: genlin
manager: jlian
editor: ''
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 09c2badf6116dd36add6cccc82486d7f5b8f8697
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="add-or-change-azure-subscription-administrators"></a>Lägga till eller ändra Azure-prenumerationsadministratörer

Azure klassiska prenumerationsadministratörer och Azure [rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md) är två system för att hantera åtkomst till Azure-resurser:

* Klassiska prenumerationer administratörsroller erbjuder grundläggande hantering och inkludera kontoadministratör tjänstadministratören och Medadministratörer.
    * När du registrerar dig för en ny Azure-prenumeration, har ditt konto angetts som både kontoadministratör och tjänstadministratör som standard.
    * Medadministratörer kan läggas till efter logga.
* RBAC är ett nyare system som ger tillgång till ingående åtkomsthantering med många inbyggda roller, möjlighet att scope och anpassade roller.
    * Användare med endast RBAC-roller och inga administratörsroller för klassiska prenumerationer kan emellertid inte hantera Azure klassiska distributioner.

Att säkerställa bättre kontroll och för att förenkla åtkomsthantering rekommenderar vi att du använder RBAC hantera allt åtkomst. Om möjligt bör du konfigurera om befintliga principer för åtkomst med RBAC. 

<a name="add-an-admin-for-a-subscription"></a>

## <a name="add-an-rbac-owner-admin-for-a-subscription-in-azure-portal"></a>Lägg till administratör RBAC ägare för en prenumeration på Azure-portalen 

Om du vill lägga till en person som administratör för Azure-prenumeration tjänsten administration du ge dem en roll för RBAC ägare till prenumerationen. Rollen som ägare kan hantera resurserna i prenumerationen du tilldelade och har inte behörighet att använda till andra prenumerationer.

1. Besök [ **prenumerationer** i Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Välj den prenumeration som du vill ge åtkomst.
3. Välj **Lägg till**  
   (Om knappen Lägg till saknas kan du inte har behörighet att lägga till behörigheter.)
4. Välj **åtkomstkontroll (IAM)** på menyn.
5. I den **rollen** väljer **ägare**. 
6. I den **bevilja åtkomst till** väljer **Azure AD-användare, grupp eller programmet**. 
7. I den **Välj** Skriv e-postadressen för den användare som du vill lägga till som ägare. Välj användaren och välj sedan **spara**.

    ![Skärmbild som visar ägarrollen som valts](./media/billing-add-change-azure-subscription-administrator/add-role.png)

Detta ger användaren full åtkomst till alla resurser som bland annat att delegera åtkomst till andra. Om du vill ge åtkomst till ett annat omfång, t.ex. en resursgrupp, kan du besöka IAM-menyn för detta omfång. 

## <a name="add-or-change-co-administrator"></a>Lägga till eller ändra medadministratör

Endast en ägare kan läggas till som medadministratör. Andra användare med roller som deltagare och läsare kan inte läggas till som medadministratörer.

> [!TIP]
> Du behöver bara lägga till kontot ”ägare” som medadministratör om användaren behöver hantera Azure klassiska distributioner. Vi rekommenderar att du använder RBAC för andra ändamål.

1. Om du inte redan gjort lägger du till någon som ägare följa anvisningarna ovan.
2. **Högerklicka på** ägare användaren du just lagt till och välj sedan **Lägg till som medadministratör**. Om du inte ser den **Lägg till som medadministratör** alternativ, uppdatera sidan eller försök med en annan webbläsare. 

    ![Skärmbild som lägger till medadministratör](./media/billing-add-change-azure-subscription-administrator/add-coadmin.png)

    Ta bort behörigheten medadministratör **högerklickar du på** ”medadministratör” användar- och välj sedan **ta bort medadministratör**.

    ![Skärmbild som tar bort medadministratör](./media/billing-add-change-azure-subscription-administrator/remove-coadmin.png)

<a name="change-service-administrator-for-a-subscription"></a>

## <a name="change-the-service-administrator-for-an-azure-subscription"></a>Ändra tjänstadministratör för en Azure-prenumeration

Enbart administratörskontot kan ändra den tjänstadministratör för en prenumeration. Som standard när du registrerar dig, är administratören samma som kontoadministratör. Om tjänstens ändras till en annan användare förlorar åtkomst till Azure-portalen med kontoadministratör. Kontoadministratören kan dock alltid använda Kontocenter ändra tjänstens tillbaka till sig själva.

1. Se till att ditt scenario stöds genom att kontrollera den [gränser för att ändra tjänstadministratörer](#limits).
1. Logga in på [Kontocenter](https://account.windowsazure.com/subscriptions) som kontoadministratör.
1. Välj en prenumeration.
1. Välj höger, **redigera prenumerationsinformation**.

    ![Skärmbild som visar knappen Redigera prenumeration i Account Center](./media/billing-add-change-azure-subscription-administrator/editsub.png)
1. I den **TJÄNSTADMINISTRATÖR** ange e-postadressen för den nya tjänstadministratören.

    ![Skärmbild som visar rutan för att ändra tjänstadministratör e-postmeddelandet](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

<a name="limits"></a>

### <a name="limitations-for-changing-service-administrators"></a>Begränsningar för att ändra tjänstadministratörer

* Varje prenumeration är associerad med en Azure AD-katalog. Gå till för att hitta katalogen prenumerationen är associerad med [ **prenumerationer**](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), Välj en prenumeration på katalogen.
* Om du har loggat in med ett arbets- eller Skol-konto kan du lägga till andra konton i din organisation som tjänstadministratör. Till exempel abby@contoso.com kan lägga till bob@contoso.com som tjänstadministratör men det går inte att lägga till john@notcontoso.com om john@notcontoso.com har finns i katalogen contoso.com. Användare som har loggat in med konton för arbetet eller skolan kan fortsätta att lägga till Account användare som tjänstadministratör.

  | Inloggningsmetod | Lägg till Microsoft Account user som SA? | Lägg till konto för arbetet eller skolan i samma organisation som SA? | Lägg till konto för arbetet eller skolan i annan organisation som SA? |
  | --- | --- | --- | --- |
  |  Microsoft-konto |Ja |Nej |Nej |
  |  Arbets- eller Skolkonto |Ja |Ja |Nej |

## <a name="change-the-account-administrator-for-an-azure-subscription"></a>Ändra kontoadministratör för en Azure-prenumeration

Kontoadministratören är den användare som ursprungligen registrerat dig för Azure-prenumerationen och ansvarar som fakturering ägare till prenumerationen. Om du vill ändra kontoadministratör för en prenumeration [överför ägarskapet för en Azure-prenumeration till ett annat konto](billing-subscription-transfer.md).

<a name="check-the-account-administrator-of-the-subscription"></a>

**Osäker som kontoadministratör?** Följ de här stegen:

1. Besök [ **prenumerationer** i Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Välj den prenumeration som du vill kontrollera och se **inställningar**.
1. Välj **egenskaper**. Kontoadministratören för prenumerationen visas i den **kontoadministratören** rutan.  

## <a name="types-of-classic-subscription-admins"></a>Typer av klassiska prenumerationsadministratörer

 Kontoadministratör tjänstadministratören och medadministratör finns tre typer av klassiska prenumerationer administratörsroller i Azure. Det konto som används för att registrera dig för Azure anges automatiskt som både kontoadministratör och tjänstadministratör. Sedan kan ytterligare Medadministratörer läggas till. I följande tabell beskrivs exakt skillnaderna mellan dessa administrativa roller. 

> [!TIP]
> För bättre kontroll och detaljerad åtkomsthantering bör du använda Azure rollbaserad åtkomstkontroll (RBAC), vilket gör att användare som ska läggas till flera roller. Läs mer i [Azure Active Directory-rollbaserad åtkomstkontroll](../role-based-access-control/overview.md).

| Klassisk prenumerationsadministratör | Gräns | Beskrivning |
| --- | --- | --- |
| Kontoadministratör (AA) |1 per Azure-konto |Detta är den användare som registrerat dig för Azure-prenumerationen och har behörighet att komma åt den [Kontocenter](https://account.azure.com/Subscriptions) och utföra olika hanteringsuppgifter. Dessa inkluderar att kunna skapa nya prenumerationer, avbryta prenumerationer, ändra faktureringen för en prenumeration och ändra tjänstadministratören. Kontoadministratören är begreppsmässigt fakturering ägaren av prenumerationen. Kontoadministratören tilldelas inte RBAC, en roll.|
| Tjänsten systemadministratörens (SA) |1 per Azure-prenumeration |Den här rollen har behörighet att hantera tjänster i den [Azure-portalen](https://portal.azure.com). Kontoadministratören är också den tjänstadministratör som standard för en ny prenumeration. I RBAC ges rollen som ägare till tjänstadministratör för prenumerationen omfånget.|
| Medadministratör (CA) |200 per prenumeration |Den här rollen har samma åtkomstbehörigheter som tjänstadministratören, men kan inte ändra associationen mellan prenumerationer och Azure-kataloger. I RBAC ges rollen som ägare till Medadministratör för prenumerationen omfånget.|

## <a name="learn-more-about-resource-access-control-and-active-directory"></a>Mer information om åtkomstkontroll till resurser och Active Directory

* Läs mer om hur resursåtkomsten hanteras i Microsoft Azure i [förstå resursåtkomst i Azure](../role-based-access-control/rbac-and-directory-admin-roles.md).
* Läs mer om Azure Active Directory [hur Azure-prenumerationer är associerade med Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md) och [Tilldela administratörsroller i Azure Active Directory](../active-directory/active-directory-assign-admin-roles-azure-portal.md).

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.

Om du fortfarande behöver hjälp [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) få snabbt lösa problemet.
