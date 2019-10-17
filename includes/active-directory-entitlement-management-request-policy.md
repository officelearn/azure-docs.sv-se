---
title: ta med fil
description: ta med fil
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 10/15/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: aba3dd34383edbfb555adcc2063e4a2b76af1959
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389386"
---
## <a name="for-users-in-your-directory"></a>För användare i din katalog

Följ dessa steg om du vill tillåta att användare i katalogen kan begära det här åtkomst paketet. När du definierar en princip för begäran kan du ange enskilda användare eller flera vanliga grupper av användare. Din organisation kanske redan har en grupp, till exempel **alla anställda**.  Om gruppen läggs till i principen för användare som kan begära åtkomst, kan alla medlemmar i gruppen sedan begära åtkomst.

1. I listan **användare som kan begära åtkomst** väljer du **för användare i din katalog**.

    När du väljer det här alternativet visas nya alternativ för att ytterligare finjustera vem i din katalog som kan begära det här åtkomst paketet.

    ![Åtkomst till paket-begär Anden – för användare i din katalog](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. Välj ett av följande alternativ:

    |  |  |
    | --- | --- |
    | **Vissa användare och grupper** | Välj det här alternativet om du vill att endast de användare och grupper i din katalog som du anger ska kunna begära det här åtkomst paketet. |
    | **Alla medlemmar (exklusive gäster)** | Välj det här alternativet om du vill att alla medlems användare i din katalog ska kunna begära det här åtkomst paketet. Det här alternativet omfattar inte några gäst användare som du kanske har bjudit in till din katalog. |
    | **Alla användare (inklusive gäster)** | Välj det här alternativet om du vill att alla medlems användare och gäst användare i din katalog ska kunna begära det här åtkomst paketet. |

    Gäst användare refererar till externa användare som har bjudits in till din katalog med [Azure AD B2B](../articles/active-directory/b2b/what-is-b2b.md). Information om skillnaderna mellan medlems användare och gäst användare finns i [Vad är standard användar behörigheter i Azure Active Directory?](../articles/active-directory/fundamentals/users-default-permissions.md).

1. Om du har valt **vissa användare och grupper**klickar du på **Lägg till användare och grupper**.

1. I fönstret Välj användare och grupper väljer du de användare och grupper som du vill lägga till.

    ![Åtkomst till paket-begär Anden – Välj användare och grupper](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. Klicka på **Välj** för att lägga till användare och grupper.

1. Hoppa ned till [godkännande](#approval) avsnittet.

## <a name="for-users-not-in-your-directory"></a>För användare som inte är i din katalog

Följ dessa steg om du vill tillåta att användare som inte är i din katalog kan begära det här åtkomst paketet. **Användare som inte finns i din katalog** refererar till användare som finns i en annan Azure AD-katalog eller domän och har kanske inte bjudits in till din katalog ännu. Azure AD-kataloger måste konfigureras för att tillåta inbjudningar i **samarbets begränsningar**. Mer information finns i [Aktivera externt samarbete i B2B och hantera vem som kan bjuda in gäster](../articles/active-directory/b2b/delegate-invitations.md).

> [!NOTE]
> Ett gäst användar konto skapas för en användare som ännu inte finns i katalogen vars förfrågan godkänns eller godkänns automatiskt. Gästen kommer att bjudas in, men får ingen inbjudan via e-post. I stället får de ett e-postmeddelande när deras tilldelning av åtkomst paket levereras. Senare när gäst användaren inte längre har några åtkomst paket tilldelningar, eftersom deras senaste tilldelning har gått ut eller avbrutits, kommer gäst användar kontot att blockeras från att logga in och sedan tas bort. Om du vill att gäst användare ska finnas kvar i din katalog på obestämd tid, även om de inte har några åtkomst paket, kan du ändra inställningarna för konfiguration av rättighets hantering. Mer information om gäst användar objekt finns i [Egenskaper för en Azure Active Directory B2B-samarbets användare](../articles/active-directory/b2b/user-properties.md).

1. I listan **användare som kan begära åtkomst** väljer du **för användare som inte finns i din katalog**.

    När du väljer det här alternativet visas nya alternativ.

    ![Åtkomst till paket-begär Anden – för användare som inte är i din katalog](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. Välj ett av följande alternativ:

    |  |  |
    | --- | --- |
    | **Vissa anslutna organisationer** | Välj det här alternativet om du vill välja från en lista över organisationer som administratören har lagt till tidigare. Alla användare från de valda organisationerna kommer att kunna begära det här åtkomst paketet. |
    | **Alla anslutna organisationer** | Välj det här alternativet om du vill att alla användare från alla anslutna organisationer ska kunna begära det här åtkomst paketet. |

    En ansluten organisation är en extern Azure AD-katalog eller domän som du ofta samarbetar med.

1. Om du har valt **vissa anslutna organisationer**klickar du på **Lägg till kataloger** för att välja från en lista över anslutna organisationer som administratören har lagt till tidigare.

1. Ange ett domän namn om du vill söka efter en ansluten organisation med det domän namnet.

    ![Åtkomst till paket-begär Anden – Välj kataloger](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    Om den organisation som du vill samar beta med inte finns med i listan kan du be administratören att lägga till den som en ansluten organisation. 

1. När du har valt alla anslutna organisationer klickar du på **Välj**.

    > [!NOTE]
    > Alla användare från de valda anslutna organisationerna kommer att kunna begära det här åtkomst paketet. Detta inkluderar användare från alla under domäner som är associerade med de anslutna organisationerna.

1. Hoppa ned till [godkännande](#approval) avsnittet.

## <a name="none-administrator-direct-assignments-only"></a>Ingen (endast administratörs direkta tilldelningar)

Följ de här stegen om du vill kringgå åtkomst begär Anden och låta administratörer direkt tilldela vissa användare till det här Access-paketet. Användarna behöver inte begära åtkomst paketet. Du kan fortfarande ange livs cykel inställningar, men det finns inga inställningar för begäran.

1. I listan **användare som kan begära åtkomst** väljer du **Ingen (endast administratörers direkta tilldelningar**.

    ![Åtkomst till paket-begär Anden-inga enbart administratörs direkta tilldelningar](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    När du har skapat åtkomst paketet kan du direkt tilldela vissa interna och externa användare till Access-paketet. Om du anger en extern användare så skapas ett gäst användar konto i din katalog. Information om hur du tilldelar en användare direkt finns i [Visa och ändra tilldelningar för ett Access-paket](../articles/active-directory/governance/entitlement-management-access-package-assignments.md).

1. Hoppa nedåt till avsnittet [Aktivera begär Anden](#enable-requests) .

## <a name="approval"></a>Godkännande

I avsnittet godkännande anger du om ett godkännande krävs när användare begär det här åtkomst paketet. Godkännande inställningarna fungerar på följande sätt:

- Endast en av de valda god kännarna eller reserv god kännaren måste godkänna en begäran. Godkännande från alla god kännare krävs inte.
- Godkännande beslutet baseras på vilken god kännare som granskar begäran först.

1. Om du vill kräva godkännande för förfrågningar från de valda användarna anger du alternativet **Kräv godkännande** för att växla till **Ja**. Om du vill att begär Anden ska godkännas automatiskt anger du växla till **Nej**.

    ![Åtkomst till paket-begär Anden – godkännande inställningar](./media/active-directory-entitlement-management-request-policy/approval.png)

1. Om du vill kräva att användarna anger en motivering för att begära åtkomst paketet, ställer du in **justeringen Kräv ändring av begär ande** för att växla till **Ja**.

1. Ta reda på om begäran kräver ett enda eller ett godkännande med flera steg. Ange **hur många steg** som ska växlas till **1** för en enskild fas.

1. För god kännare väljer du **chef som god kännare** eller **vissa god kännare**.

    Chefen bestäms av attributet **Manager** i användarens profil för Azure AD. Mer information finns i [lägga till eller uppdatera en användares profil information med hjälp av Azure Active Directory](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md).

    ![Azure Active Directory användar profil-Manager-attribut](./media/active-directory-entitlement-management-request-policy/profile-manager.png)

1. Om du har valt hanterare som god kännare, klickar du på **Lägg till reserv** för att välja en eller flera användare eller grupper i din katalog som en reserv god kännare. det går inte att hitta chefen.

1. Om du har valt Välj en viss god kännare klickar du på **Lägg till god kännare** för att välja en eller flera användare eller grupper i din katalog som ska vara god kännare.

1. I **beslut måste fattas i hur många dagar?** anger du det antal dagar som en god kännare måste granska en begäran om det här åtkomst paketet.

    Om en begäran inte godkänns inom den här tids perioden kommer den automatiskt att nekas. Användaren måste skicka en annan begäran om åtkomst paketet.

1. Om du vill kräva att användarna anger en motivering för att begära åtkomst paketet, ställer du in **Kräv motivering** till **Ja**.

    En motivering är synlig för andra god kännare och begär Ande.

## <a name="enable-requests"></a>Aktivera begär Anden

1. Om du vill att Access-paketet ska göras omedelbart tillgängligt för användare i begär ande principen att begära, klickar du på **Ja** för att aktivera.

    Du kan alltid aktivera det i framtiden när du är klar med att skapa åtkomst paketet.

    Om du har valt **Ingen (endast administratörers direkta tilldelningar)** och du ställer in Tillåt till **Nej**kommer administratörer inte att kunna tilldela det här åtkomst paketet direkt.

    ![Åtkomst paket-princip-aktivera princip inställning](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. Klicka på **Next**.
