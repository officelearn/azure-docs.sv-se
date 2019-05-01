---
title: ta med fil
description: ta med fil
services: active-directory
author: rolyon
ms.service: active-directory
ms.topic: include
ms.date: 04/16/2019
ms.author: rolyon
ms.custom: include file
ms.openlocfilehash: ef8155cda6c31a63204af80ca091a13bb16a687d
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866440"
---
### <a name="policy-for-users-in-your-directory"></a>Princip: För användare i din katalog

Följ dessa steg om du vill att principen för användare och grupper i katalogen som kan begära det här åtkomst-paketet.

1. I den **användare som kan begära åtkomst** väljer **för användare i katalogen**.

1. I den **Välj användare och grupper** klickar du på **lägga till användare och grupper**.

1. I Välj användare och grupper fönstret, väljer du de användare och grupper som du vill lägga till.

    ![Åtkomst paket - princip - Välj användare och grupper](./media/active-directory-entitlement-management-policy/policy-select-users-groups.png)

1. Klicka på **Välj** att lägga till användare och grupper.

1. Hoppa över ned till den [princip: Begär](#policy-request) avsnittet.

### <a name="policy-for-users-not-in-your-directory"></a>Princip: För användare inte i din katalog

Följ dessa steg om du vill att principen ska vara för användare inte i katalogen som kan begära det här åtkomst-paketet. Kataloger måste konfigureras för att tillåtas i den **organisationens relationer samarbetesbegränsningar** inställningar.

1. I den **användare som kan begära åtkomst** väljer **för användare inte i katalogen**.

1. I den **väljer externa Azure AD directory** klickar du på **lägger till kataloger**.

1. Ange ett domännamn och Sök efter en extern Azure AD-katalog.

1. Kontrollera att det är rätt katalog genom att det angivna katalognamnet och initial domän.

    > [!NOTE]
    > Alla användare från katalog kommer att kunna begära det här åtkomst-paketet. Detta omfattar även användare från alla underdomäner som är kopplade till katalogen, inte bara den domän som används i sökningen.

    ![Paket för åtkomst - princip - Välj kataloger](./media/active-directory-entitlement-management-policy/policy-select-directories.png)

1. Klicka på **Lägg till** Lägg till katalogen.

1. Upprepa det här steget för att lägga till några flera kataloger.

1. När du har lagt till alla kataloger som ska ingå i principen, klickar du på **Välj**.

1. Hoppa över ned till den [princip: Begär](#policy-request) avsnittet.

### <a name="policy-none-administrator-direct-assignments-only"></a>Princip: Ingen (administratör direkt tilldelningar endast)

Följ dessa steg om du vill att din princip för att kringgå förfrågningar och gör att administratörer kan tilldela specifika användare direkt till paketet åtkomst. Användare inte att begära åtkomst till paketet. Du kan ange inställningar för giltighetstid fortfarande, men det finns inga inställningar för begäran.

1. I den **användare som kan begära åtkomst** väljer **None (administratör direkt tilldelningar**.

    När du skapar paketet åtkomst kan tilldela du direkt specifika interna och externa användare till åtkomst-paketet. Om du anger en extern användare skapas ett gästkonto för användare i din katalog.

1. Hoppa över ned till den [princip: Upphör att gälla](#policy-expiration) avsnittet.

### <a name="policy-request"></a>Princip: Förfrågan

I avsnittet begäran anger du inställningar för godkännande när användare begär åtkomst till paketet.

1. För att kräva godkännande för begäranden från de valda användarna att ange den **kräver godkännande** växla till **Ja**. Om du vill att begäranden godkänns automatiskt, Ställ in växlaren **nr**.

1. Om du kräver godkännande, i den **Välj godkännare** klickar du på **Lägg till godkännare**.

1. Välj en eller flera användare och/eller grupper vara godkännare i fönstret Välj godkännare.

    Endast en av de valda godkännarna måste godkänna en begäran. Godkännande från alla godkännare krävs inte. Beslutet om godkännande är baserad på godkännaren har granskat begäran först.

    ![Paket för åtkomst - princip - Välj godkännare](./media/active-directory-entitlement-management-policy/policy-select-approvers.png)

1. Klicka på **Välj** att lägga till godkännarna.

1. Klicka på **visa avancerade inställningar för tjänstbegäran** att visa ytterligare inställningar.

    ![Paket för åtkomst - princip - Välj kataloger](./media/active-directory-entitlement-management-policy/policy-advanced-request.png)

1. Så att användare måste ange en anledning för att begära åtkomst-paketet, ange **Kräv motivering** till **Ja**.

1. För att kräva godkännaren att ange en anledning för att godkänna en begäran om åtkomst-paketet, ange **Kräv godkännarmotivering** till **Ja**.

1. I den **tidsgränsen för begäran om godkännande (dagar)** anger hur lång tid som godkännarna behöva granska en begäran. Om inga godkännare gå igenom det i detta antal dagar, förfrågan upphör att gälla och användaren måste du skicka en annan begäran om åtkomst-paketet.

### <a name="policy-expiration"></a>Princip: Utgångsdatum

I avsnittet upphör att gälla anger du när en användare tilldelas åtkomst paketet upphör att gälla.

1. I den **upphör att gälla** anger **åtkomst paketet upphör att gälla** till **på datum**, **antalet dagar**, eller **aldrig**.

    För **på datum**, Välj ett förfallodatum i framtiden.

    För **antalet dagar**, ange ett tal mellan 0 och 3660 dagar.

    Baserat på ditt val Användartilldelning till paketet åtkomst går ut på ett specifikt datum, ett visst antal dagar efter att de har godkänts eller aldrig.

1. Klicka på **visa avancerade inställningar för giltighetstid** att visa ytterligare inställningar.

1. Om du vill tillåta användare att utöka deras tilldelningar kan ange **Tillåt användare att utöka åtkomst** till **Ja**.

    Om tillägg är tillåtna i principen, användaren får ett e-postmeddelande 14 dagar och även 1 dag innan deras åtkomst pakettilldelningar upphör att gälla ber dem att utöka tilldelningen.

    ![Paket för åtkomst - inställningar för princip - giltighetstid](./media/active-directory-entitlement-management-policy/policy-expiration.png)

### <a name="policy-enable-policy"></a>Princip: Aktivera princip

1. Om du vill att paketets åtkomst ska vara tillgängliga omedelbart för användare i principen, klickar du på **Ja** att aktivera principen.

    Du kan alltid lägga det i framtiden när du har skapat åtkomst-paketet.

    ![Paket för åtkomst - princip – aktivera principinställningen](./media/active-directory-entitlement-management-policy/policy-enable.png)

1. Klicka på **nästa** eller **skapa**.
