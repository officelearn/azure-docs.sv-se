---
title: Inbjudan om inlösning i B2B-samarbete – Azure AD
description: Beskriver Azure AD B2B-samarbets Inbjudnings upplevelsen för slutanvändare, inklusive avtalets Sekretess villkor.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e85b0ae298589c0e0e051a24e5db89eae81db62
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74272140"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Azure Active Directory B2B-samarbete för inbjudan

I den här artikeln beskrivs de olika sätt som gäst användare kan komma åt resurser och medgivande processen som de stöter på. Om du skickar ett e-postmeddelande med inbjudan till gästen innehåller inbjudan en länk som gästen kan lösa in för att få åtkomst till din app eller Portal. E-postinbjudan är bara ett av de sätt som gäster kan få åtkomst till dina resurser. Alternativt kan du lägga till gäster till katalogen och ge dem en direkt länk till portalen eller appen som du vill dela. Oavsett vilken metod de använder, vägleds gästerna genom en process för godkännande av första gången. Den här processen säkerställer att dina gäster samtycker till sekretess villkoren och accepterar de [användnings villkor](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) som du har konfigurerat.

När du lägger till en gäst användare i din katalog har gäst användar kontot en medgivande status (synlig i PowerShell) som inlednings vis är inställt på **PendingAcceptance**. Den här inställningen är kvar tills gästen accepterar din inbjudan och accepterar din sekretess policy och användnings villkor. Därefter ändras medgivande statusen till **godkänd**och medgivande sidorna visas inte längre för gästen.

## <a name="redemption-through-the-invitation-email"></a>Inlösen via e-postinbjudan

När du lägger till en gäst användare till din katalog med [hjälp av Azure Portal](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal)skickas ett e-postmeddelande med inbjudan till gästen i processen. Du kan också välja att skicka e-postinbjudningar när du [använder PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) för att lägga till gäst användare i din katalog. Här är en beskrivning av gästens upplevelse när de löser in länken i e-postmeddelandet.

1. Gästen får ett [e-postmeddelande om inbjudan](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) som skickas från **Microsofts inbjudningar**.
2. Gästen väljer **Kom igång** i e-postmeddelandet.
3. Om gästen inte har ett Azure AD-konto, ett Microsoft-konto (MSA) eller ett e-postkonto i en federerad organisation, uppmanas de att skapa en MSA (om inte funktionen [eng ång slö sen ord](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) är aktive rad, vilket inte kräver en MSA).
4. Gästen vägleds genom den [godkännande upplevelse](#consent-experience-for-the-guest) som beskrivs nedan.

## <a name="redemption-through-a-direct-link"></a>Inlösen via en direkt länk

Som ett alternativ till e-postinbjudan kan du ge en gäst en direkt länk till din app eller Portal. Du måste först lägga till gäst användaren till din katalog via [Azure Portal](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) eller [PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell). Sedan kan du använda något av de [anpassningsbara sätten för att distribuera program till användare](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences), inklusive direkt inloggnings länkar. När en gäst använder en direkt länk i stället för e-postinbjudan, kommer de fortfarande att guidas genom den första gången.

> [!IMPORTANT]
> Den direkta länken måste vara klient-/regionsspecifika. Med andra ord måste det innehålla ett klient-ID eller verifierad domän så att gästen kan autentiseras i din klient, där den delade appen finns. En vanlig URL som https://myapps.microsoft.com fungerar inte för en gäst eftersom den kommer att omdirigeras till sin hem klient för autentisering. Här följer några exempel på direkta länkar med klient kontext:
 > - Åtkomst panel för appar: https://myapps.microsoft.com/?tenantid=&lt; klient-ID&gt; 
 > - Åtkomst panelen för appar för en verifierad domän: https://myapps.microsoft.com/&lt; verifierad domän&gt;
 > - Azure Portal: https://portal.azure.com/&lt; klient-ID&gt;
 > - Enskild app: se så här använder du en [direkt inloggnings länk](../manage-apps/end-user-experiences.md#direct-sign-on-links)

Det finns vissa fall där e-postinbjudan rekommenderas över en direkt länk. Om dessa specialfall är viktiga för din organisation rekommenderar vi att du bjuder in användare genom att använda metoder som fortfarande skickar e-postinbjudan:
 - Användaren har inget Azure AD-konto, ett MSA eller ett e-postkonto i en federerad organisation. Om du inte använder funktionen för eng ång slö sen ord måste gästen lösa in inbjudan via e-post för att vägleda dig genom stegen för att skapa en MSA.
 - Ibland kanske det inbjudna användarobjektet inte har en e-postadress på grund av en konflikt med ett kontakt objekt (till exempel ett Outlook-kontakt objekt). I det här fallet måste användaren klicka på inlösnings-URL: en i e-postinbjudan.
 - Användaren kan logga in med ett alias för den e-postadress som bjudits in. (Ett alias är en ytterligare e-postadress som associeras med ett e-postkonto.) I det här fallet måste användaren klicka på inlösnings-URL: en i e-postinbjudan.

## <a name="consent-experience-for-the-guest"></a>Godkännande upplevelse för gästen

När en gäst loggar in för att komma åt resurser i en partner organisation för första gången, går de igenom följande sidor. 

1. Gästen granskar sidan **gransknings behörigheter** som beskriver den bjudande organisationens sekretess policy. En användare måste **acceptera** användningen av informationen i enlighet med den bjudande organisationens sekretess policy för att kunna fortsätta.

   ![Skärm bild som visar sidan gransknings behörigheter](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Information om hur du som klient organisations administratör kan länka till din organisations sekretess policy finns i [How-to: Lägg till din organisations sekretess information i Azure Active Directory](https://aka.ms/adprivacystatement).

2. Om användnings villkoren har kon figurer ATS öppnas gästen och granskar användnings villkoren och väljer sedan **acceptera**. 

   ![Skärm bild som visar nya användnings villkor](media/redemption-experience/terms-of-use-accept.png) 

   > [!NOTE]
   > Du kan konfigurera se [användnings villkoren](../governance/active-directory-tou.md) i **Hantera** > **organisations relationer** > **användningsvillkor**.

3. Om inget annat anges omdirigeras gästen till appens åtkomst panel, som innehåller en lista över de program som gästen har åtkomst till.

   ![Skärm bild som visar åtkomst panelen för appar](media/redemption-experience/myapps.png) 

I din katalog har gästens **inbjudan godkänt** värde ändrats till **Ja**. Om en MSA skapades, visar gästens **källa** Microsoft- **konto**. Mer information om egenskaper för gäst användar konton finns i [Egenskaper för en Azure AD B2B-samarbets användare](user-properties.md). 

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [Lägg till Azure Active Directory B2B-samarbets användare i Azure Portal](add-users-administrator.md)
- [Hur kan informations arbetare lägga till B2B-samarbets användare för att Azure Active Directory?](add-users-information-worker.md)
- [Lägga till Azure Active Directory B2B-samarbets användare med hjälp av PowerShell](customize-invitation-api.md#powershell)
- [Lämna en organisation som gäst användare](leave-the-organization.md)
