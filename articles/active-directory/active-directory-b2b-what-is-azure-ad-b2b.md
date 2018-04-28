---
title: Vad är Azure Active Directory B2B-samarbete? | Microsoft Docs
description: Företagsomfattande relationer genom att tilldela affärspartner selektiv åtkomst till företagets program har stöd för Azure Active Directory B2B-samarbete.
services: active-directory
documentationcenter: ''
author: twooley
manager: mtillman
editor: ''
tags: ''
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 04/26/2018
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: b8bc25028a3356d00c06692d90271221eee44651
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="what-is-azure-ad-b2b-collaboration"></a>Vad är Azure AD B2B-samarbete?

Azure Active Directory (AD Azure) business-to-business (B2B) funktioner kan en organisation som använder Azure AD för att fungera på ett säkert sätt och på ett säkert sätt med användare från någon annan organisation små eller stora. Dessa organisationer kan vara med eller utan Azure AD och behöver även inte ha en IT-avdelning.

Organisationer som använder Azure AD kan ge åtkomst till dokument, resurser och program till sina partner samtidigt fullständig kontroll över företagets data. Utvecklare kan använda Azure AD business-to-business API: er för att skriva program som samordnar två organisationer i mer på ett säkert sätt. Dessutom är det enkelt för användare att navigera.

Följande videoklipp ger en bra översikt.
>[!VIDEO https://www.youtube.com/embed/AhwrweCBdsc]

## <a name="key-benefits-of-azure-ad-b2b-collaboration"></a>Viktiga fördelar med Azure AD B2B-samarbete

### <a name="work-with-any-user-from-any-partner"></a>Arbeta med alla användare från någon part

- Partners använda sina egna autentiseringsuppgifter
- Inga krav att använda Azure AD-partner
- Inga externa kataloger eller komplexa installation krävs

### <a name="simple-and-secure-collaboration"></a>Enkelt och säkert samarbete

- Ge åtkomst till företagets app eller data, samtidigt som du använder avancerade, Azure AD-påslagen auktoriseringsprinciper
- Enkelt för användare
- Säkerhet i företagsklass för appar och data

### <a name="no-management-overhead"></a>Inga hanteringskostnader

- Ingen extern hantering av konto eller lösenord
- Ingen synkronisering eller manuell livscykel kontohantering
- Inga externa administrativa kostnader

## <a name="easily-add-b2b-collaboration-users"></a>Lägg enkelt till B2B-samarbete användare

Som administratör kan du kan enkelt lägga till användare för B2B-samarbete (Gäst) för din organisation i den [Azure-portalen](https://portal.azure.com).

![Lägg till gästanvändare](media/active-directory-b2b-what-is-azure-ad-b2b/adding-b2b-users-admin.png)

### <a name="enable-your-collaborators-to-bring-their-own-identity"></a>Aktivera din medarbetare att ta sina egna identitet

B2B medarbetare kan logga in med en identitet efter eget val. Om användaren inte har ett microsoftkonto eller ett Azure AD-katalogen skapas för dem sömlöst vid tidpunkten för erbjudande inlösning.

### <a name="delegate-to-application-and-group-owners"></a>Delegera till programmet och gruppen ägare

Som ett program eller gruppägare kan du lägga till B2B användare direkt till alla program som intresserar dig, om det är ett Microsoft-program eller inte. Administratörer kan delegera behörighet att lägga till B2B användare till icke-administratörer. Icke-administratörer kan använda den [åtkomstpanelen för Azure AD-program](https://myapps.microsoft.com) att lägga till program eller grupper B2B-samarbete användare.

![åtkomstpanelen](media/active-directory-b2b-what-is-azure-ad-b2b/access-panel.png)

![lägga till medlem](media/active-directory-b2b-what-is-azure-ad-b2b/add-member.png)

### <a name="authorization-policies-protect-your-corporate-content"></a>Auktoriseringsprinciper skyddar företagets innehållet

Principer för villkorlig åtkomst, till exempel multifaktorautentisering, kan tillämpas:
- På nivån för klient
- På programnivå
- För vissa användare att skydda företagets appar och data

### <a name="use-apis-and-sample-code-to-easily-build-applications-to-onboard"></a>Använd API: er och exempelkod enkelt kan skapa program ska publiceras

Ta din externa partners på sätt som är anpassade till din organisations behov.

Använd den [B2B-samarbetsinbjudan API: er](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) att anpassa din onboarding uppstår, inklusive att skapa registrering självbetjäningsportaler. Vi tillhandahåller exempelkod för en självbetjäningsportal [på Github](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

![registreringsportalen](media/active-directory-b2b-what-is-azure-ad-b2b/sign-up-portal.png)

Du kan hämta alla fördelar med Azure AD att skydda din partnerrelationer på ett sätt som slutanvändarna hitta enkelt och intuitivt med Azure AD B2B-samarbete.

## <a name="next-steps"></a>Nästa steg

- [Hur lägger Azure Active Directory-administratörer till B2B-samarbete användare?](active-directory-b2b-admin-add-users.md)
- [Hur lägger informationsarbetare till B2B-samarbete användare?](active-directory-b2b-iw-add-users.md)
- [B2B-samarbete inbjudan inlösning](active-directory-b2b-redemption-experience.md)
- [Azure AD B2B-samarbete och licensiering](active-directory-b2b-licensing.md)
- Och som alltid ansluta med Produktteamet för feedback, diskussioner och förslag till våra [Microsoft teknisk Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).