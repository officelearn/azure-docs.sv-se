---
title: Delar av e-postmeddelandet med B2B-inbjudan – Azure Active Directory | Microsoft-dokument
description: E-postmall för e-postmall för azure Active Directory B2B-samarbetsanbjudan
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f93586d46aa01116990f8f02f344c6952d3c1b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "65768372"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>Elementen i e-postmeddelandet med inbjudan till B2B-samarbete - Azure Active Directory

E-postmeddelanden med inbjudningar är en viktig komponent för att få med partner som B2B-samarbetsanvändare i Azure AD. Du kan använda dem för att öka mottagarens förtroende. Du kan lägga till legitimitet och sociala bevis i e-postmeddelandet för att se till att mottagaren känner sig bekväm med att välja knappen **Kom igång** för att acceptera inbjudan. Detta förtroende är ett viktigt sätt att minska delningsfriktionen. Och du vill också göra e-post ser bra ut!

![Skärmbild som visar e-postmeddelandet med B2B-inbjudan](media/invitation-email-elements/invitation-email.png)

## <a name="explaining-the-email"></a>Förklara e-postmeddelandet
Låt oss titta på några delar av e-postmeddelandet så att du vet hur du bäst använder deras funktioner.

### <a name="subject"></a>Subjekt
Ämnet för e-postmeddelandet följer följande mönster: Du &lt;är&gt; inbjuden till klientnamnsorganisationen

### <a name="from-address"></a>Avsändaradress
Vi använder ett LinkedIn-liknande mönster för Från-adressen.  Du bör vara tydlig med vem inbjudna är och från vilket företag, och även klargöra att e-postmeddelandet kommer från en Microsoft-e-postadress. Formatet <invites@microsoft.com> är: Microsoft-inbjudningar eller&gt; &lt;&gt; <invites@microsoft.com> &lt;Visningsnamn för inbjudna från klientnamn (via Microsoft) .

### <a name="reply-to"></a>Svara på
Svarsmeddelandet är inställt på inbjudnas e-post när den är tillgänglig, så att svaret på e-postmeddelandet skickar ett e-postmeddelande tillbaka till inbjudna.

### <a name="branding"></a>Anpassning
Inbjudningsmeddelandena från din klient använder företagets varumärke som du kan ha ställt in för din klient. Om du vill dra nytta av den här funktionen, [här](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) är information om hur du konfigurerar den. Bannerlogotypen visas i e-postmeddelandet. Följ bildstorleken och kvalitetsinstruktionerna [här](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) för bästa resultat. Dessutom dyker företagsnamnet också upp i uppmaningen.

### <a name="call-to-action"></a>Uppmaning till handling
Uppmaningen består av två delar: att förklara varför mottagaren har fått posten och vad mottagaren ombeds att göra åt det.
- Avsnittet "varför" kan åtgärdas med följande mönster: Du har blivit &lt;inbjuden&gt; att komma åt program i klientnamnsorganisationen

- Och avsnittet "vad du blir ombedd att göra" visas med knappen **Kom igång.** När mottagaren har lagts till utan att behöva inbjudningar visas inte den här knappen.

### <a name="inviters-information"></a>Inbjudnas information
Inbjudnas visningsnamn ingår i e-postmeddelandet. Och om du har konfigurerat en profilbild för ditt Azure AD-konto innehåller det inbjudande e-postmeddelandet även den bilden. Båda är avsedda att öka mottagarens förtroende för e-postmeddelandet.

Om du ännu inte har konfigurerat din profilbild visas en ikon med inbjudnas initialer i stället för bilden:

  ![Skärmbild som visar inbjudan med inbjudna initialer som visas](media/invitation-email-elements/inviters-initials.png)

### <a name="body"></a>Innehåll
Brödtexten innehåller meddelandet som inbjudna skriver när [en gästanvändare bjuder in till katalogen, gruppen eller appen](add-users-administrator.md) eller med hjälp av [inbjudnings-API:et](customize-invitation-api.md). Det är ett textområde, så det bearbetar inte HTML-taggar av säkerhetsskäl.

  ![Skärmbild som visar brödtexten i e-postmeddelandet för inbjudan](media/invitation-email-elements/invitation-email-body.png)

### <a name="footer-section"></a>Sidfot avsnitt
Sidfoten innehåller Microsofts företagsvarumärke och meddelar mottagaren om e-postmeddelandet skickades från ett oövervakat alias. 

Särskilda fall:

- Inbjudna har ingen e-postadress i den inbjudande hyresrätt

  ![Skärmbild när en inbjudna spelare inte har e-post i den inbjudande hyressättningen](media/invitation-email-elements/inviter-no-email.png)


- Mottagaren behöver inte lösa in inbjudan

  ![Skärmbild när mottagaren inte behöver lösa in inbjudan](media/invitation-email-elements/when-recipient-doesnt-redeem.png)

## <a name="how-the-language-is-determined"></a>Hur språket bestäms
Språket som presenteras för gästanvändaren i e-postmeddelandet med inbjudan bestäms av följande inställningar. Dessa inställningar visas i prioritetsordning. Om en inställning inte är konfigurerad avgör nästa inställning i listan språket. 
- **Egenskapen messageLanguage** för det [inbjudnaUserMessageInfo-objektet](https://docs.microsoft.com/graph/api/resources/invitedusermessageinfo?view=graph-rest-1.0) om API:et Skapa inbjudan används
-   Den **önskade Språkegenskapen** som anges i gästens [användarobjekt](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0)
-   **Meddelandespråket** som anges i egenskaperna för gästanvändarens hemklient (endast för Azure AD-klienter)
-   **Meddelandespråket** som anges i resursklientens egenskaper

Om ingen av dessa inställningar är konfigurerade är språket som standard engelska (USA).

## <a name="next-steps"></a>Nästa steg

Se följande artiklar om Azure AD B2B-samarbete:

- [Vad är Azure AD B2B-samarbete](what-is-b2b.md)
- [Hur lägger Azure Active Directory-administratörer till B2B-samarbetsanvändare?](add-users-administrator.md)
- [Hur lägger informationsarbetare till B2B-samarbetsanvändare?](add-users-information-worker.md)
- [B2B samarbete inbjudan inlösen](redemption-experience.md)
- [Lägga till B2B-samarbetsanvändare utan inbjudan](add-user-without-invite.md)
