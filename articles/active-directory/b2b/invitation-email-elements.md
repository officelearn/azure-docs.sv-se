---
title: Element i B2B e-postinbjudan - Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B-samarbete inbjudan e-postmall
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43fa8148fff1389982d967b2e69f4a9425841c91
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58014914"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>Element i B2B-samarbete e-postinbjudan - Azure Active Directory

Postinbjudningar är en kritisk komponent för att ge partner ombord som användare i B2B-samarbetet i Azure AD. Du kan använda dem för att öka mottagarens förtroende. Du kan lägga till giltighet och sociala bevis till e-post att kontrollera att mottagaren känns bekväm med att välja den **börjar** knappen för att tacka ja till inbjudan. Det här förtroendet är en nyckel som innebär att minska friktionen för delning. Och du vill se e-postmeddelandet se bra ut!

![Inbjudan av e-post med Azure AD B2b](media/invitation-email-elements/invitation-email.png)

## <a name="explaining-the-email"></a>Förklarar e-postmeddelandet
Nu ska vi titta på några få av e-postmeddelandet så att du vet hur du bäst för att använda deras funktioner.

### <a name="subject"></a>Subjekt
Ämnet för e-postmeddelandet följer följande mönster: Du är inbjuden till den &lt;tenantname&gt; organisation

### <a name="from-address"></a>Från adress
Vi använder ett LinkedIn-liknande mönster för från-adressen.  Du bör vara tydlig som avsändaren är och från företagets vilket också förtydliga att e-postmeddelandet kommer från en Microsoft e-postadress. Formatet är: Microsoft Invitations <invites@microsoft.com> eller &lt;visningsnamnet för inbjudaren&gt; från &lt;tenantname&gt; (via Microsoft) <invites@microsoft.com>.

### <a name="reply-to"></a>Svara
Svara till e-postmeddelandet är inställd på inbjudaren e-postmeddelande när det är tillgängligt, så att svara på e-postmeddelandet skickar ett e-postmeddelande till avsändaren.

### <a name="branding"></a>Anpassning
E-postmeddelanden för inbjudan från din klient användning företagsanpassning som du kanske har konfigurerat för din klient. Om du vill dra nytta av den här funktionen [här](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) finns information om hur du konfigurerar den. Banderollslogotypen visas i e-postmeddelandet. Följ bildstorleken och kvalitet instruktioner [här](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) för bästa resultat. Dessutom visas företagsnamnet också i anropet till åtgärden.

### <a name="call-to-action"></a>Anrop till åtgärd
Din uppmaning till består av två delar: förklarar varför mottagaren har tagit emot e-postmeddelandet och vad mottagaren har ombetts att göra om den.
- Avsnittet ”Varför” kan lösas med hjälp av följande mönster: Du har blivit inbjuden att använda program i den &lt;tenantname&gt; organisation

- Och den ”vad du ska ange att göra” avsnittet indikeras av förekomst av den **börjar** knappen. När mottagaren har lagts till utan att behöva inbjudningar, visas den här knappen inte.

### <a name="inviters-information"></a>Bjuder INS information
Visningsnamn för den inbjudaren ingår i e-postmeddelandet. Och dessutom om du har konfigurerat en profilbild för din Azure AD-konto, bjuder in e-postmeddelandet tas bilden även. Båda är avsedda att öka förtroende för mottagarens e-postmeddelandet.

Om du ännu inte har konfigurerat din profilbild visas en ikon med den inbjudaren initialer i stället för bilden:

  ![Visa den inbjudaren initialer](media/invitation-email-elements/inviters-initials.png)

### <a name="body"></a>Innehåll
Texten innehåller meddelandet att avsändaren composes när [bjuda in en gästanvändare till den katalog, en grupp eller en app](add-users-administrator.md) eller [med hjälp av inbjudan API](customize-invitation-api.md). Det är ett textområde så att den inte bearbetar HTML-taggar av säkerhetsskäl.

  ![Bild av e-postmeddelandets brödtext](media/invitation-email-elements/invitation-email-body.png)

### <a name="footer-section"></a>Sidfotsavsnittet
Sidfoten innehåller Microsoft företagets varumärke och gör att mottagaren kan veta om e-postmeddelandet har skickats från ett oövervakat alias. 

Specialfall:

- Avsändaren har inte en e-postadress i bjuder in innehavare

  ![Bild av inbjudaren har inte en e-postadress i bjuder in innehavare](media/invitation-email-elements/inviter-no-email.png)


- Mottagaren behöver inte lösa in inbjudan

  ![När mottagaren inte behöver lösa in inbjudan](media/invitation-email-elements/when-recipient-doesnt-redeem.png)

## <a name="how-the-language-is-determined"></a>Hur bestäms språket
Det språk som presenteras för gästanvändaren i e-postinbjudan bestäms av följande inställningar. De här inställningarna visas i prioritetsordning. Om en inställning inte är konfigurerat anger Nästa inställning i listan över språk. 
- Den **messageLanguage** egenskapen för den [invitedUserMessageInfo](https://docs.microsoft.com/graph/api/resources/invitedusermessageinfo?view=graph-rest-1.0) objekt om skapa inbjudan API används
-   Den **preferredLanguage** egenskapen som angetts i gästens [användarobjekt](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0)
-   Den **aviseringsspråk** ange i egenskaperna för gästanvändarens startklientorganisation (för Azure AD-klienter)
-   Den **aviseringsspråk** ange i egenskaperna för resurs-klient

Om ingen av de här inställningarna är konfigurerade som standard språket till engelska (US).

## <a name="next-steps"></a>Nästa steg

Se följande artiklar på Azure AD B2B-samarbete:

- [Vad är Azure AD B2B-samarbete](what-is-b2b.md)
- [Hur lägger Azure Active Directory-administratörer till B2B-samarbetare?](add-users-administrator.md)
- [Hur lägger informationsarbetare till användare i B2B-samarbetet?](add-users-information-worker.md)
- [B2B-samarbete inlösning av inbjudan](redemption-experience.md)
- [Lägga till B2B-samarbete användare utan inbjudan](add-user-without-invite.md)
