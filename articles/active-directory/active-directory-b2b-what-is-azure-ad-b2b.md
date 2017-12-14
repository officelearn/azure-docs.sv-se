---
title: "Vad är Azure Active Directory B2B-samarbete? | Microsoft Docs"
description: "Företagsomfattande relationer genom att tilldela affärspartner selektiv åtkomst till företagets program har stöd för Azure Active Directory B2B-samarbete."
services: active-directory
documentationcenter: 
author: sasubram
manager: mtillman
editor: 
tags: 
ms.assetid: 1464387b-ee8b-4c7c-94b3-2c5567224c27
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 06/27/2017
ms.author: curtand
ms.custom: aaddev
ms.reviewer: sasubram
ms.openlocfilehash: eef95f80f8107c13cd057c56eb5219a353acc854
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="what-is-azure-ad-b2b-collaboration"></a>Vad är Azure AD B2B-samarbete?

<iframe width="560" height="315" src="https://www.youtube.com/embed/AhwrweCBdsc" frameborder="0" allowfullscreen></iframe>

Azure AD business-to-business (B2B) funktioner kan en organisation som använder Azure AD för att fungera på ett säkert sätt och på ett säkert sätt med användare från någon annan organisation små eller stora. Dessa organisationer kan vara med Azure AD eller utan, eller med en IT-organisation eller utan. 

Organisationer som använder Azure AD kan ge åtkomst till dokument, resurser och program till sina partner samtidigt fullständig kontroll över företagets data. Utvecklare kan använda Azure AD business-to-business API: er för att skriva program som samordnar två organisationer i mer på ett säkert sätt. Det är också praktiskt för slutanvändare att navigera.

97% av våra kunder bett oss om Azure AD B2B-samarbete är mycket viktigt att dem.

![cirkeldiagram](media/active-directory-b2b-what-is-azure-ad-b2b/97-percent-support.png)

Vi hade cirka 3 miljoner användare använder redan Azure AD B2B-samarbetesfunktioner från och med tidig April 2017. Och mer än 23% av Azure AD-organisationer som har fler än 10 användare drar nytta redan av dessa funktioner.

## <a name="the-key-benefits-of-azure-ad-b2b-collaboration-to-your-organization"></a>De främsta fördelarna med Azure AD B2B-samarbete för din organisation

### <a name="work-with-any-user-from-any-partner"></a>Arbeta med alla användare från någon part

* Partners använda sina egna autentiseringsuppgifter

* Inga krav att använda Azure AD-partner

* Inga externa kataloger eller komplexa installation krävs

### <a name="simple-and-secure-collaboration"></a>Enkelt och säkert samarbete

* Ge åtkomst till företagets app eller data, samtidigt som du använder avancerade, Azure AD-påslagen auktoriseringsprinciper

* Enkelt för användare

* Säkerhet i företagsklass för appar och data

### <a name="no-management-overhead"></a>Inga hanteringskostnader

* Ingen extern hantering av konto eller lösenord

* Ingen synkronisering eller manuell livscykel kontohantering

* Inga externa administrativa kostnader

## <a name="you-can-easily-add-b2b-collaboration-users-to-your-organization"></a>Du kan enkelt lägga till B2B-samarbete användare för din organisation

Administratörer kan lägga till B2B-samarbete (Gäst) användare i den [Azure-portalen](https://portal.azure.com).

![Lägg till gästanvändare](media/active-directory-b2b-what-is-azure-ad-b2b/adding-b2b-users-admin.png)

### <a name="enable-your-collaborators-to-bring-their-own-identity"></a>Aktivera din medarbetare att ta sina egna identitet

B2B medarbetare kan logga in med en identitet efter eget val. Om användaren inte har ett microsoftkonto eller ett Azure AD-katalogen skapas för dem sömlöst vid tidpunkten för erbjudande inlösning.

![logga in identitet val](media/active-directory-b2b-what-is-azure-ad-b2b/sign-in-identity-choice.png)

### <a name="delegate-to-application-and-group-owners"></a>Delegera till programmet och gruppen ägare 
Program- och gruppen ägare kan lägga till B2B användare direkt till alla program som intresserar dig, om det är ett Microsoft-program eller inte. Administratörer kan delegera behörighet att lägga till icke-administratörer B2B-användare. Icke-administratörer kan använda den [åtkomstpanelen för Azure AD-program](https://myapps.microsoft.com) att lägga till program eller grupper B2B-samarbete användare.

![åtkomstpanelen](media/active-directory-b2b-what-is-azure-ad-b2b/access-panel.png)

![lägga till medlem](media/active-directory-b2b-what-is-azure-ad-b2b/add-member.png)

### <a name="authorization-policies-protect-your-corporate-content"></a>Auktoriseringsprinciper skyddar företagets innehållet

Principer för villkorlig åtkomst, till exempel multifaktorautentisering, kan tillämpas:
- På nivån för klient
- På programnivå
- För vissa användare att skydda företagets appar och data

![lägga till medlem](media/active-directory-b2b-what-is-azure-ad-b2b/add-member.png)

### <a name="use-our-apis-and-sample-code-to-easily-build-applications-to-onboard"></a>Använd våra API: er och exempelkod enkelt kan skapa program ska publiceras
Ta din externa partners på sätt som är anpassade till din organisations behov.

Med hjälp av den [B2B-samarbetsinbjudan API: er](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation), du kan anpassa dina onboarding-upplevelser, inklusive att skapa registrering självbetjäningsportaler. Vi tillhandahåller exempelkod för en självbetjäningsportal [på Github](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

![registreringsportalen](media/active-directory-b2b-what-is-azure-ad-b2b/sign-up-portal.png)

Du kan hämta alla fördelar med Azure AD att skydda din partnerrelationer på ett sätt som slutanvändarna hitta enkelt och intuitivt med Azure AD B2B-samarbete. Alltså måste ansluta till tusentals organisationer som redan använder Azure AD B2B för sina externt samarbete!

## <a name="next-steps"></a>Nästa steg

* Administratören upplevelser finns i den [Azure-portalen](https://portal.azure.com).

* Information worker upplevelser är tillgängliga i den [åtkomstpanelen](https://myapps.microsoft.com).

* Och som alltid ansluta med Produktteamet för feedback, diskussioner och förslag till våra [Microsoft teknisk Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).

Läs andra artiklar om Azure AD B2B-samarbete:

* [Hur lägger Azure Active Directory-administratörer till B2B-samarbete användare?](active-directory-b2b-admin-add-users.md)
* [Hur lägger informationsarbetare till B2B-samarbete användare?](active-directory-b2b-iw-add-users.md)
* [Elementen i e-postinbjudan B2B-samarbete](active-directory-b2b-invitation-email.md)
* [B2B-samarbete inbjudan inlösning](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B-samarbete och licensiering](active-directory-b2b-licensing.md)
* [Felsökning av Azure Active Directory B2B-samarbete](active-directory-b2b-troubleshooting.md)
* [Vanliga och frågor svar om Azure Active Directory B2B-samarbete](active-directory-b2b-faq.md)
* [Azure Active Directory B2B-samarbete API och anpassning](active-directory-b2b-api.md)
* [Multi-Factor Authentication för användare av B2B-samarbete](active-directory-b2b-mfa-instructions.md)
* [Lägg till B2B-samarbete användare utan inbjudan](active-directory-b2b-add-user-without-invite.md)
* [B2B-samarbete användaren granskning och rapportering](active-directory-b2b-auditing-and-reporting.md)
* [Artikelindex för programhantering i Azure Active Directory](active-directory-apps-index.md)
