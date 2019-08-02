---
title: ta med fil
description: ta med fil
services: active-directory
author: rolyon
ms.service: active-directory
ms.topic: include
ms.date: 05/16/2019
ms.author: rolyon
ms.custom: include file
ms.openlocfilehash: da4bc51cdd8cdfad8212ee5a288f03874f673c2c
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68678202"
---
### <a name="policy-for-users-in-your-directory"></a>Princip: För användare i din katalog

Följ dessa steg om du vill att principen ska vara för användare i din katalog som kan begära det här åtkomst paketet.  **Användare i katalogen** avser både interna användare och externa användare som tidigare har bjudits in till katalogen, antingen genom att begära hantering av rättigheter med ett annat Access-paket eller bjudas in med Azure AD B2B. När du definierar principen kan du ange enskilda användare eller flera vanliga grupper av användare. Din organisation kanske redan har en grupp, till exempel **alla anställda**.  Om gruppen läggs till i principen för användare som kan begära åtkomst, kan alla medlemmar i gruppen sedan begära åtkomst.

1. I avsnittet **användare som kan begära åtkomst** väljer du **för användare i din katalog**.

1. I avsnittet **Välj användare och grupper** klickar du på **Lägg till användare och grupper**.

1. I fönstret Välj användare och grupper väljer du de användare och grupper som du vill lägga till.

    ![Åtkomst paket-princip – Välj användare och grupper](./media/active-directory-entitlement-management-policy/policy-select-users-groups.png)

1. Klicka på **Välj** för att lägga till användare och grupper.

1. Hoppa ned till [principen: Avsnittet](#policy-request) om begäran.

### <a name="policy-for-users-not-in-your-directory"></a>Princip: För användare som inte är i din katalog

Följ dessa steg om du vill att principen ska vara för användare som inte tillhör din katalog som kan begära det här åtkomst paketet. **Användare som inte finns i katalogen** avser användare som finns i en annan Azure AD-katalog och har kanske inte bjudits in till din katalog ännu.  Kataloger måste konfigureras för att tillåtas i inställningarna för **samarbets begränsningar för organisations relationer** .

> [!NOTE]
> Ett externt gäst användar konto skapas för en användare som ännu inte finns i katalogen vars förfrågan godkänns eller godkänns automatiskt. Gästen kommer att bjudas in, men får ingen inbjudan via e-post. I stället får de ett e-postmeddelande när deras tilldelning av åtkomst paket levereras. Senare när gäst användaren inte längre har några åtkomst paket tilldelningar, eftersom deras senaste tilldelning har gått ut eller avbrutits, kommer gäst användar kontot att blockeras från att logga in och sedan tas bort. Om du vill att gäst användare ska finnas kvar i din katalog på obestämd tid, även om de inte har några åtkomst paket, kan du ändra inställningarna för konfiguration av rättighets hantering.

1. I avsnittet **användare som kan begära åtkomst** väljer du **för användare som inte finns i din katalog**.

1. I avsnittet **Välj extern Azure AD-katalog** klickar du på **Lägg till kataloger**.

1. Ange ett domän namn och Sök efter en Azure AD-katalog med det domän namnet.

1. Kontrol lera att det är rätt katalog med det angivna katalog namnet och den första domänen.

    > [!NOTE]
    > Alla användare från katalogen kommer att kunna begära det här åtkomst paketet. Detta inkluderar användare från alla under domäner som är associerade med katalogen, inte bara den domän som används i sökningen.

    ![Åtkomst paket – princip – Välj kataloger](./media/active-directory-entitlement-management-policy/policy-select-directories.png)

1. Klicka på **Lägg till** för att lägga till katalogen.

1. Upprepa det här steget om du vill lägga till fler kataloger.

1. När du har lagt till alla kataloger som du vill inkludera i principen klickar du på **Välj**.

1. Hoppa ned till [principen: Avsnittet](#policy-request) om begäran.

### <a name="policy-none-administrator-direct-assignments-only"></a>Princip: Ingen (endast administratörs direkta tilldelningar)

Följ de här stegen om du vill att principen ska kringgå åtkomst begär Anden och låta administratörer direkt tilldela vissa användare till åtkomst paketet. Användarna behöver inte begära åtkomst paketet. Du kan fortfarande ange inställningar för förfallo datum, men det finns inga inställningar för begäran.

1. I avsnittet **användare som kan begära åtkomst** väljer du **Ingen (endast administratörers direkta tilldelningar**.

    När du har skapat åtkomst paketet kan du direkt tilldela vissa interna och externa användare till Access-paketet. Om du anger en extern användare så skapas ett gäst användar konto i din katalog.

1. Hoppa ned till [principen: Förfallo](#policy-expiration) avsnittet.

### <a name="policy-request"></a>Princip: Förfrågan

I avsnittet begäran anger du godkännande inställningar när användare begär åtkomst paketet.

1. Om du vill kräva godkännande för förfrågningar från de valda användarna anger du alternativet **Kräv godkännande** för att växla till **Ja**. Om du vill att begär Anden ska godkännas automatiskt anger du växla till **Nej**.

1. Om du kräver godkännande klickar du på **Lägg till god kännare**i avsnittet **Välj god kännare** .

1. I fönstret Välj god kännare väljer du en eller flera användare och/eller grupper som ska vara god kännare.

    Endast en av de valda god kännarna måste godkänna en begäran. Godkännande från alla god kännare krävs inte. Godkännande beslutet baseras på vilken god kännare som granskar begäran först.

    ![Åtkomst paket-princip-Välj god kännare](./media/active-directory-entitlement-management-policy/policy-select-approvers.png)

1. Klicka på **Välj** för att lägga till god kännarna.

1. Klicka på **Visa avancerade inställningar för begäran** för att visa ytterligare inställningar.

    ![Åtkomst paket – princip – Välj kataloger](./media/active-directory-entitlement-management-policy/policy-advanced-request.png)

1. Om du vill kräva att användarna anger en motivering för att begära åtkomst paketet, ställer du in **Kräv motivering** till **Ja**.

1. Om du vill att god kännaren ska kunna ge en motivering för att godkänna en begäran om åtkomst paketet, ställer du in **Kräv god kännare-justering** på **Ja**.

1. I rutan **tids gräns för godkännande av begäran (dagar)** anger du hur lång tid god kännarna måste granska en begäran. Om ingen god kännare granskar den under det här antalet dagar upphör begäran att gälla och användaren måste skicka en annan begäran om åtkomst paketet.

### <a name="policy-expiration"></a>Princip: Utgångsdatum

I avsnittet förfallo datum anger du när en användares tilldelning till åtkomst paketet upphör att gälla.

1. I avsnittet **förfallo** datum ställer du in **åtkomst paket som upphör att gälla** **på datum**, **antal dagar**eller **aldrig**.

    För **på datum**väljer du ett förfallo datum i framtiden.

    Ange ett tal mellan 0 och 3660 dagar i **antal dagar**.

    Baserat på ditt val förfaller en användares tilldelning till åtkomst paketet ett visst datum, ett visst antal dagar efter att de har godkänts eller aldrig.

1. Klicka på **Visa avancerade inställningar för förfallo datum** för att visa ytterligare inställningar.

1. Om du vill tillåta att användare utökar sina tilldelningar ställer du in **Tillåt att användare utökar åtkomsten** till **Ja**.

    Om tillägg är tillåtna i principen får användaren ett e-postmeddelande 14 dagar och även en dag innan tilldelningen av åtkomst paket är inställd på att förfalla och uppmanas att förlänga tilldelningen.

    ![Åtkomst paket-princip-inställningar för förfallo datum](./media/active-directory-entitlement-management-policy/policy-expiration.png)

### <a name="policy-enable-policy"></a>Princip: Aktivera princip

1. Om du vill att Access-paketet ska göras omedelbart tillgängligt för användare i principen klickar du på **Ja** för att aktivera principen.

    Du kan alltid aktivera det i framtiden när du är klar med att skapa åtkomst paketet.

    ![Åtkomst paket-princip-aktivera princip inställning](./media/active-directory-entitlement-management-policy/policy-enable.png)

1. Klicka på **Nästa** eller **skapa**.
