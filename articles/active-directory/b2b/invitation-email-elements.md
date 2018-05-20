---
title: Elementen i B2B-samarbete e-postinbjudan - Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B-samarbete inbjudan e-postmall
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/23/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: e8285779154914bd09513c057d8e5ae0b6388831
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>Elementen i B2B-samarbete e-postinbjudan - Azure Active Directory

E-postmeddelanden för inbjudan är en kritisk komponent återinföra partners på tåget som B2B-samarbete användare i Azure AD. Du kan använda dem för att öka mottagarens förtroende. Du kan lägga till är giltiga och sociala bevis till e-post, se till mottagaren känns bekväm med att välja den **Kom igång** för att tacka ja till inbjudan. Förtroendet är en nyckel som innebär att minska delning friktion. Och du vill se e-postmeddelandet ser bra ut!

![Azure AD B2b inbjudan e-post](media/invitation-email-elements/invitation-email.png)

## <a name="explaining-the-email"></a>Förklarar e-postmeddelandet
Nu ska vi titta på några elementen i e-postmeddelandet så att du vet hur du bäst för att använda deras funktioner.

### <a name="subject"></a>Ämne
Ämnet för e-postmeddelandet efter följande mönster: du inbjuds att den &lt;tenantname&gt; organisation

### <a name="from-address"></a>Från adress
Vi använder ett LinkedIn-liknande mönster för från-adressen.  Du bör vara tydlig som avsändaren av inbjudan och e-postadress som företagets och även tydliggöra att e-postmeddelandet kommer från Microsoft. Formatet är: &lt;visningsnamnet för bjuder in&gt; från &lt;tenantname&gt; (via Microsoft) <invites@microsoft.com>

### <a name="reply-to"></a>Svara
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

  ![Visa den bjuder in initialer](media/invitation-email-elements/inviters-initials.png)

### <a name="body"></a>Innehåll
Texten innehåller meddelandet som avsändaren av inbjudan composes eller skickas via inbjudan API. Det är ett textområde så inte bearbetar HTML-taggar av säkerhetsskäl.

### <a name="footer-section"></a>Sidfotsavsnittet
Sidfoten innehåller Microsoft företags varumärke och gör att mottagaren om e-postmeddelandet skickades från meddelandet. Särskilda fall:

- Avsändaren av inbjudan har inte en e-postadress i bjuda in innehavare

  ![Bild av bjuder in har inte en e-postadress i bjuda in innehavare](media/invitation-email-elements/inviter-no-email.png)


- Mottagaren behöver inte lösa inbjudan

  ![När mottagaren inte behöver lösa inbjudan](media/invitation-email-elements/when-recipient-doesnt-redeem.png)


## <a name="next-steps"></a>Nästa steg

Se följande artiklar om Azure AD B2B-samarbete:

- [Vad är Azure AD B2B-samarbete](what-is-b2b.md)
- [Hur lägger Azure Active Directory-administratörer till B2B-samarbete användare?](add-users-administrator.md)
- [Hur lägger informationsarbetare till B2B-samarbete användare?](add-users-information-worker.md)
- [B2B-samarbete inbjudan inlösning](redemption-experience.md)
- [Lägg till B2B-samarbete användare utan inbjudan](add-user-without-invite.md)
