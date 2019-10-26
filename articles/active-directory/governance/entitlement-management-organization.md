---
title: Lägg till en ansluten organisation i Azure AD-hantering av rättigheter (för hands version) – Azure Active Directory
description: Lär dig hur du tillåter att personer utanför organisationen kan begära åtkomst paket så att du kan samar beta med projekt.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/24/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23d45db1f62b370a2692ed932572e16c247b8903
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952490"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management-preview"></a>Lägga till en ansluten organisation i Azure AD-hantering av rättigheter (för hands version)

Hantering av Azure AD-berättigande ger dig möjlighet att samar beta med personer utanför organisationen. Om du ofta samarbetar med användare i en extern Azure AD-katalog eller domän kan du lägga till dem som en ansluten organisation. Den här artikeln beskriver hur du lägger till en ansluten organisation så att du kan tillåta användare utanför organisationen att begära resurser i din katalog.

## <a name="what-is-a-connected-organization"></a>Vad är en ansluten organisation?

En ansluten organisation är en extern Azure AD-katalog eller domän som du har en relation med.

Anta till exempel att du arbetar på Sparbanken och vill samar beta med två externa organisationer: Graphic Design Institute och contoso. Du har fått anvisningar från din kontakt i Graphic Design Institute att de använder Azure AD, och att grafik Design Institute-användare har ett User Principal Name som slutar med `graphicdesigninstitute.com`. Och du har fått din kontakt på Contoso att de inte använder Azure AD än, men att Contosos användare har ett User Principal Name som slutar med `contoso.com`.

Du kan konfigurera två anslutna organisationer – en för Graphic Design Institute med domän `graphicdesigninstitute.com`och en för Contoso med domän `contoso.com`. Om du sedan lägger till dessa två anslutna organisationer i en princip kan användare från varje organisation som har en User Principal Name som matchar principen begära åtkomst paket. Dessutom, eftersom Graphic Design Institute har identifierats som att använda Azure AD, och om Graphic Design Institute sedan har under domäner, till exempel `graphicdesigninstitute.example`, kommer användare med den User Principal Name också att kunna begära åtkomst paket med hjälp av samma princip.

![Exempel på ansluten organisation](./media/entitlement-management-organization/connected-organization-example.png)

Hur användare från Azure AD-katalogen eller domänen kommer att autentiseras beror på autentiseringstypen. Typerna av autentisering för anslutna organisationer är följande:

| Autentiseringstyp | Status |
| --- | --- |
| Azure AD | Stöds |
| [Direkt Federation](../b2b/direct-federation.md) | Förhandsversion |
| [Eng ång slö sen ord](../b2b/one-time-passcode.md) (domän) | Förhandsversion |

## <a name="add-a-connected-organization"></a>Lägg till en ansluten organisation

Följ dessa steg om du vill lägga till en extern Azure AD-katalog eller domän som en ansluten organisation.

**Nödvändig roll:** Global administratör, användar administratör eller gäst deltagare

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. Klicka på **anslutna organisationer** på den vänstra menyn och klicka sedan på **Lägg till ansluten organisation**.

    ![Identitets styrning – anslutna organisationer – Lägg till ansluten organisation](./media/entitlement-management-organization/connected-organization.png)

1. På fliken **grundläggande** anger du ett visnings namn och en beskrivning av organisationen.

    ![Lägg till ansluten organisation – fliken grunder](./media/entitlement-management-organization/organization-basics.png)

1. På fliken **katalog + domän** klickar du på **Lägg till katalog + domän** för att öppna fönstret Välj kataloger + domäner.

1. Ange ett domän namn för att söka efter Azure AD-katalogen eller-domänen. Du måste ange hela domän namnet.

1. Kontrol lera att det är rätt organisation med det tillhandahållna namnet och autentiseringstypen. Hur användarna ska logga in beror på autentiseringstypen.

    ![Lägg till ansluten organisation – Välj kataloger + domäner](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. Klicka på **Lägg till** för att lägga till Azure AD-katalogen eller-domänen. För närvarande kan du bara lägga till en Azure AD-katalog eller-domän per ansluten organisation.

    > [!NOTE]
    > Alla användare från Azure AD-katalogen eller-domänen kan begära det här åtkomst paketet. Detta inkluderar användare i Azure AD från alla under domäner som är associerade med katalogen, om inte dessa domäner blockeras av listan Tillåt eller neka i Azure B2B. Mer information finns i [tillåta eller blockera inbjudningar till B2B-användare från vissa organisationer](../b2b/allow-deny-list.md).

1. När du har lagt till Azure AD-katalogen eller-domänen klickar du på **Välj**.

    Organisationen visas i listan.

    ![Lägg till ansluten organisation – fliken kataloger](./media/entitlement-management-organization/organization-directory-domain.png)

1. På fliken **sponsorer** lägger du till ytterligare sponsorer för den här anslutna organisationen.

    Sponsorer är interna eller externa användare som redan finns i din katalog som är kontakt punkten för relationen med den här anslutna organisationen. Interna sponsorer är medlems användare i din katalog. Externa sponsorer är gäst användare från den anslutna organisationen som tidigare bjudits in och redan finns i din katalog. Sponsorer kan användas som god kännare när användare i den här anslutna organisationen begär åtkomst till det här åtkomst paketet. Information om hur du bjuder in en gäst användare till din katalog finns i [lägga till Azure Active Directory B2B-samarbets användare i Azure Portal](../b2b/add-users-administrator.md).

    När du klickar på **Lägg till/ta bort**visas ett fönster där du kan välja interna eller externa sponsorer. I fönstret visas en ofiltrerad lista över användare och grupper i din katalog.

    ![Åtkomst paket – princip – Lägg till ansluten organisation – fliken sponsorer](./media/entitlement-management-organization/organization-sponsors.png)

1. På fliken **Granska + skapa** granskar du organisations inställningarna och klickar sedan på **skapa**.

    ![Åtkomst paket – princip – Lägg till ansluten organisation – Granska + fliken Skapa](./media/entitlement-management-organization/organization-review-create.png)

## <a name="delete-a-connected-organization"></a>Ta bort en ansluten organisation

Om du inte längre har en relation med en extern Azure AD-katalog eller-domän kan du ta bort den anslutna organisationen.

**Nödvändig roll:** Global administratör, användar administratör eller gäst deltagare

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. Klicka på **anslutna organisationer** på den vänstra menyn och klicka sedan för att öppna den anslutna organisationen.

1. På sidan Översikt klickar du på **ta bort** för att ta bort den anslutna organisationen.

    För närvarande kan du bara ta bort en ansluten organisation om det inte finns några anslutna användare.

    ![Identitets styrning – anslutna organisationer – ta bort ansluten organisation](./media/entitlement-management-organization/organization-delete.png)

## <a name="next-steps"></a>Nästa steg

- [Styr åtkomsten för externa användare](entitlement-management-organization.md)
- [För användare som inte är i din katalog](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
