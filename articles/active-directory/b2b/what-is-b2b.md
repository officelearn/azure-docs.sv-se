---
title: Vad är B2B-samarbete i Azure Active Directory?
description: Azure Active Directory B2B-samarbete stöder gästanvändaråtkomst så att du på ett säkert sätt kan dela resurser och samarbeta med externa partner.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89e1868d8e9a346d5a791c9fa8ae7682ca2b6807
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2020
ms.locfileid: "84905144"
---
# <a name="what-is-guest-user-access-in-azure-active-directory-b2b"></a>Vad är gästanvändaråtkomst i Azure Active Directory B2B?

Med samarbete i Azure Active Directory (Azure AD) B2B (företag till företag) kan du på ett säkert sätt dela företagets program och tjänster med gästanvändare från valfri organisation, samtidigt som du behåller kontrollen över företagets egna data. Arbeta tryggt och säkert med externa partners, stora som små, även om de inte har Azure AD eller någon IT-avdelning. Tack vare en enkel process för inbjudan och inlösen kan dessa partners använda sina egna autentiseringsuppgifter för att få åtkomst till företagets resurser. Utvecklare kan använda Azure AD B2Bs API:er för att anpassa inbjudningsprocessen eller skriva program som självbetjäningsportaler för registrering.

Titta på videon och lär dig hur du på ett säkert sätt kan samarbeta med gästanvändare genom att bjuda in dem till att logga in på företagets appar och tjänster med hjälp av sina egna identiteter.

Följande videoklipp ger en bra översikt.

>[!VIDEO https://www.youtube.com/embed/AhwrweCBdsc]

   > [!IMPORTANT]
   > Från och med den **31 mars 2021**kommer Microsoft inte längre att stödja inlösen av inbjudningar genom att skapa ohanterade Azure AD-konton och klienter för B2B-samarbets scenarier. Vi rekommenderar att kunderna väljer [autentisering med e-post med eng ång slö sen ord](one-time-passcode.md). Vi välkomnar din feedback om den här offentliga för hands versionen och är glada att skapa ännu fler sätt att samar beta.

## <a name="collaborate-with-any-partner-using-their-identities"></a>Samarbeta med alla partners med hjälp av deras identiteter

Med Azure AD B2B använder dina partners sina egna lösningar för identitetshantering, vilket innebär att inte tillkommer några externa administrativa kostnader för din organisation.

- Dina partners använder sina egna identiteter och autentiseringsuppgifter. Azure AD är inte obligatoriskt.
- Du behöver inte hantera externa konton eller lösenord.
- Du behöver inte synkronisera konton eller hantera kontolivscykler.  

![Skärm bild som visar sidan Lägg till medlemmar](media/what-is-b2b/add-member.png)

## <a name="invite-guest-users-with-a-simple-invitation-and-redemption-process"></a>Bjud in gästanvändare med en enkel process för inbjudan och inlösen

Gästanvändarna loggar in på dina appar och tjänster med sina egna arbets- eller skolidentiteter eller sociala identiteter. Om gäst användaren inte har ett Microsoft-konto eller ett Azure AD-konto, skapas ett för dem när de löser in sin inbjudan. 

- Bjud in gästanvändare med valfri e-postidentitet.
- Skicka en direktlänk till en app eller skicka en inbjudan till gästanvändaren egen åtkomstpanel.
- Gästanvändarna loggar in genom några enkla inlösningssteg.

![Skärm bild som visar sidan gransknings behörigheter](media/what-is-b2b/consentscreen.png)

## <a name="use-policies-to-securely-share-your-apps-and-services"></a>Använda principer för att på ett säkert sätt dela dina appar och tjänster

Du kan använda Auktoriseringsprinciper för att skydda ditt företags innehåll. Principer för villkorlig åtkomst, till exempel Multi-Factor Authentication, kan tillämpas:

- På klientorganisationsnivå.
- På programnivå.
- För specifika gästanvändare för att skydda företagets appar och data.

![Skärm bild som visar alternativet för villkorlig åtkomst](media/what-is-b2b/tutorial-mfa-policy-2.png)


## <a name="easily-add-guest-users-in-the-azure-ad-portal"></a>Lägg enkelt till gästanvändare i Azure AD-portalen

Som administratör kan du enkelt lägga till gästanvändare för din organisation i Azure Portal.

- Skapa en ny gästanvändare i Azure AD på liknande sätt som du lägger till en ny användare.
- Gästanvändaren får direkt en anpassningsbar inbjudan som låter hen logga in på sin åtkomstpanel.
- Gästanvändare i katalogen kan tilldelas appar eller grupper.  

![Skärm bild som visar post sidan ny gäst användar inbjudan](media/what-is-b2b/add-a-b2b-user-to-azure-portal.png)

## <a name="let-application-and-group-owners-manage-their-own-guest-users"></a>Låt program och gruppägare hantera sina egna gästanvändare

Du kan delegera hanteringen av gästanvändarna till programägarna, så att de kan lägga till gästanvändare direkt till de program de vill dela, oavsett om det är ett Microsoft-program eller inte.

- Administratörer konfigurerar självbetjäningapp och grupphantering.
- Icke-administratörer använda sina [åtkomstpanelet](https://myapps.microsoft.com) för att lägga till gästanvändare till program eller grupper.

![Skärm bild som visar åtkomst panelen för en gäst användare](media/what-is-b2b/access-panel-manage-app.png)

## <a name="customize-the-onboarding-experience-for-b2b-guest-users"></a>Anpassa onboarding-upplevelsen för B2B-gäst användare

Ta med dina externa partner på ett sätt som anpassas efter din organisations behov.

- Använd [hantering av Azure AD-rättighet](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) för att konfigurera principer som [hanterar åtkomst för externa användare](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users#how-access-works-for-external-users).
- Använd [API: er för inbjudan med B2B-samarbete](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) för att anpassa dina onboarding-upplevelser.

## <a name="integrate-with-identity-providers"></a>Integrera med identitets leverantörer

Azure AD stöder externa identitets leverantörer som Facebook, Microsoft-konton, Google-eller Enterprise Identity-leverantörer. Du kan konfigurera Federation med identitets leverantörer så att dina externa användare kan logga in med sina befintliga sociala konton eller företags konton i stället för att skapa ett nytt konto för ditt program. Läs mer om identitets leverantörer för externa identiteter.

![Skärm bild som visar sidan med identitets leverantörer](media/what-is-b2b/identity-providers.png)


## <a name="create-a-self-service-sign-up-user-flow-preview"></a>Skapa ett användar flöde för självbetjänings registrering (förhands granskning)

Med ett användar flöde för självbetjänings registrering kan du skapa en registrerings miljö för externa användare som vill ha åtkomst till dina appar. Som en del av registrerings flödet kan du ange alternativ för olika sociala eller företags identitets leverantörer och samla in information om användaren. Lär dig om självbetjänings [registrering och hur du konfigurerar det](self-service-sign-up-overview.md).

Du kan också använda [API-kopplingar](api-connectors-overview.md) för att integrera dina självbetjänings registrerings användar flöden med externa moln system. Du kan ansluta med anpassade arbets flöden för godkännande, utföra identitets verifiering, validera information från användare och mycket annat.

![Skärm bild som visar sidan användar flöden](media/what-is-b2b/self-service-sign-up-user-flow-overview.png)
<!--TODO: Add screenshot with API connectors -->

## <a name="next-steps"></a>Nästa steg

- [Licensieringsguide för Azure AD B2B-samarbete](licensing-guidance.md)
- [Lägg till gästanvändare för B2B-samarbete i portalen](add-users-administrator.md)
- [Förstå inlösningsprocessen för inbjudningar](redemption-experience.md)
- Och som alltid kan du kontakta produktgruppen med eventuell feedback och förslag via vår [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
