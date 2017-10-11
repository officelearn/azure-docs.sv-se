---
title: Elementen i e-postinbjudan Azure Active Directory B2B-samarbete | Microsoft Docs
description: Azure Active Directory B2B-samarbete inbjudan e-postmall
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/23/2017
ms.author: sasubram
ms.openlocfilehash: 458a2cab13b7e83f120e0926a95d454070181dfb
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email"></a>Elementen i e-postinbjudan B2B-samarbete

E-postmeddelanden för inbjudan är en kritisk komponent återinföra partners på tåget som B2B-samarbete användare i Azure AD. Du kan använda dem för att öka mottagarens förtroende. Du kan lägga till är giltiga och sociala bevis till e-post, se till mottagaren känns bekväm med att välja den **Kom igång** för att tacka ja till inbjudan. Förtroendet är en nyckel som innebär att minska delning friktion. Och du vill se e-postmeddelandet ser bra ut!

![Azure AD B2b inbjudan e-post](media/active-directory-b2b-invitation-email/invitation-email.png)

## <a name="explaining-the-email"></a>Förklarar e-postmeddelandet
Nu ska vi titta på några elementen i e-postmeddelandet så att du vet hur du bäst för att använda deras funktioner.

### <a name="subject"></a>Ämne
Ämnet för e-postmeddelandet efter följande mönster: du inbjuds att den &lt;tenantname&gt; organisation

### <a name="from-address"></a>Från-adress
Vi använder ett LinkedIn-liknande mönster för från-adressen.  Du bör vara tydlig som avsändaren av inbjudan och e-postadress som företagets och även tydliggöra att e-postmeddelandet kommer från Microsoft. Formatet är: &lt;visningsnamnet för bjuder in&gt; från &lt;tenantname&gt; (via Microsoft) <invites@microsoft.com&gt;

### <a name="reply-to"></a>Svara på
Svara till e-postmeddelandet har angetts till den bjuder in e-post när det är tillgängligt, så att svara på e-postmeddelandet skickar ett e-postmeddelande till avsändaren av inbjudan.

### <a name="branding"></a>Anpassning
E-postmeddelanden för inbjudan från din klient användning företagsanpassning som du kanske har ställt in för din klient. Om du vill dra nytta av den här funktionen [här](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) finns information om hur du konfigurerar den. Banderollslogotypen visas i e-postmeddelandet. Följ bildstorleken och kvalitet instruktioner [här](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) för bästa resultat. Dessutom visas företagsnamnet även i anropet till åtgärd.

### <a name="call-to-action"></a>Anrop till åtgärd
Anropet till åtgärd består av två delar: förklarar varför mottagaren har tagit emot e-postmeddelandet och vad mottagaren blir ombedd att göra om den.
- Avsnittet ”Varför” kan åtgärdas med följande mönster: du har bjudits komma åt program i den &lt;tenantname&gt; organisation

- Och den ”vad du att uppmanas att göra” avsnittet anges av förekomst av den **Kom igång** knappen. När mottagaren har lagts utan att behöva inbjudningar kan visas den här knappen inte.

### <a name="inviters-information"></a>Bjuder INS information
Visningsnamn för den bjuder in ingår i e-postmeddelandet. Och dessutom om du har konfigurerat en profilbild för Azure AD-kontot, bjuda in e-postmeddelandet tas samt bilden. Båda är avsedda att öka mottagarens förtroende för e-postmeddelandet.

Om du ännu inte har konfigurerat din profilbild visas en ikon med den bjuder in initialer i stället för bilden:

  ![Visa den bjuder in initialer](media/active-directory-b2b-invitation-email/inviters-initials.png)

### <a name="body"></a>Innehåll
Texten innehåller meddelandet som avsändaren av inbjudan composes eller skickas via inbjudan API. Det är ett textområde så inte bearbetar HTML-taggar av säkerhetsskäl.

### <a name="footer-section"></a>Sidfotsavsnittet
Sidfoten innehåller Microsoft företags varumärke och gör att mottagaren om e-postmeddelandet skickades från meddelandet. Särskilda fall:

- Avsändaren av inbjudan har inte en e-postadress i bjuda in innehavare

  ![Bild av bjuder in har inte en e-postadress i bjuda in innehavare](media/active-directory-b2b-invitation-email/inviter-no-email.png)


- Mottagaren behöver inte lösa inbjudan

  ![När mottagaren inte behöver lösa inbjudan](media/active-directory-b2b-invitation-email/when-recipient-doesnt-redeem.png)


## <a name="next-steps"></a>Nästa steg

Läs andra artiklar om Azure AD B2B-samarbete:

* [Vad är Azure AD B2B-samarbete](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Hur lägger Azure Active Directory-administratörer till B2B-samarbete användare?](active-directory-b2b-admin-add-users.md)
* [Hur lägger informationsarbetare till B2B-samarbete användare?](active-directory-b2b-iw-add-users.md)
* [B2B-samarbete inbjudan inlösning](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B-samarbete och licensiering](active-directory-b2b-licensing.md)
* [Felsökning av Azure Active Directory B2B-samarbete](active-directory-b2b-troubleshooting.md)
* [Vanliga och frågor svar om Azure Active Directory B2B-samarbete](active-directory-b2b-faq.md)
* [Azure Active Directory B2B-samarbete API och anpassning](active-directory-b2b-api.md)
* [Multi-Factor Authentication för användare av B2B-samarbete](active-directory-b2b-mfa-instructions.md)
* [Lägg till B2B-samarbete användare utan inbjudan](active-directory-b2b-add-user-without-invite.md)
* [Artikelindex för programhantering i Azure Active Directory](active-directory-apps-index.md)
