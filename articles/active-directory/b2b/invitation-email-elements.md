---
title: Delar av e-postmeddelandet med B2B-inbjudan – Azure Active Directory | Microsoft-dokument
description: E-postmall för e-postmall för azure Active Directory B2B-samarbetsanbjudan
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0429cfb62c319675806d76b4759b776a7b32dbcb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81407179"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>Elementen i e-postmeddelandet med inbjudan till B2B-samarbete - Azure Active Directory

E-postmeddelanden med inbjudningar är en viktig komponent för att få med partner som B2B-samarbetsanvändare i Azure AD. Även om det [inte krävs att du skickar ett e-postmeddelande för att bjuda in någon som använder B2B-samarbete,](add-user-without-invite.md)ger detta användaren all information de behöver för att fatta ett beslut om huruvida du ska acceptera din inbjudan. Det ger dem också en länk som de alltid kan hänvisa till i framtiden när de behöver återvända till dina resurser.

![Skärmbild som visar e-postmeddelandet med B2B-inbjudan](media/invitation-email-elements/invitation-email.png)

> [!NOTE]
> Den här nya e-postmallen distribueras fortfarande till alla klienter, så vissa klienter använder fortfarande en äldre design. I slutet av maj 2020 kommer inbjudningar från alla klienter att använda den här mallen.

## <a name="explaining-the-email"></a>Förklara e-postmeddelandet

Låt oss titta på några delar av e-postmeddelandet så att du vet hur du bäst använder deras funktioner.

### <a name="subject"></a>Subjekt

Ämnet för e-postmeddelandet följer detta mönster:

&lt;användarnamnet&gt; uppmanade dig att komma åt ansökningar inom organisationen.

### <a name="from-address"></a>Avsändaradress

Vi använder ett LinkedIn-liknande mönster för Från-adressen. Detta mönster bör göra det klart invites@microsoft.comatt även om e-postmeddelandet kommer från , inbjudan är från en annan organisation. Formatet är: Microsoft-inbjudningar <invites@microsoft.com> eller &lt;Microsoft-inbjudningar för klientnamn&gt; <invites@microsoft.com>. 

### <a name="reply-to"></a>Svara på

Svarsmeddelandet är inställt på inbjudnas e-post när den är tillgänglig, så att svaret på e-postmeddelandet skickar ett e-postmeddelande tillbaka till inbjudna.

### <a name="phishing-warning"></a>Varning för nätfiske

E-postmeddelandet börjar med en kort varning till användaren om nätfiske och varnar dem om att de bara ska acceptera inbjudningar som de förväntar sig. Det är god praxis att se till att de partner du bjuder in inte blir förvånade över din inbjudan genom att nämna det för dem i förväg.

![Bild av nätfiskevarningen i e-postmeddelandet](media/invitation-email-elements/phishing-warning.png)

### <a name="inviters-information"></a>Inbjudnas information

E-postmeddelandet innehåller information om inbjudna och organisationen de skickar inbjudan från. Detta inkluderar avsändarens namn och e-postadress, samt namn och primär domän som är associerad med organisationen. All denna information bör hjälpa den inbjudna att fatta ett välgrundat beslut om att acceptera inbjudan.

![Bild av inbjudnas information i e-postmeddelandet](media/invitation-email-elements/inviters-information.png)

### <a name="invitation-message"></a>Meddelande om inbjudan

Om inbjudna innehåller ett meddelande som en del av sin inbjudan när de [bjuder in en gästanvändare till katalogen, gruppen eller appen](add-users-administrator.md) eller när de [använder inbjudnings-API:et](customize-invitation-api.md)markeras meddelandet i huvudavsnittet i e-postmeddelandet. Dessutom ingår inbjudnas namn och profilbild om de har ställt in en. Själva meddelandet är ett textområde, så av säkerhetsskäl bearbetas html-taggar inte.

![Bild av inbjudningsmeddelandet i e-postmeddelandet](media/invitation-email-elements/invitation-message.png)

### <a name="accept-button-and-redirect-url"></a>Acceptera knapp- och omdirigerings-URL

Nästa avsnitt i e-postmeddelandet innehåller information om var den inbjudna kommer att tas efter att de har accepterat inbjudan, samt en knapp för att göra det.  I framtiden kan den inbjudna alltid använda den här länken för att återgå till dina resurser direkt.

![Bild av knappen acceptera och omdirigera URL i e-postmeddelandet](media/invitation-email-elements/accept-button.png)

### <a name="footer-section"></a>Sidfot avsnitt

Sidfoten innehåller mer information om inbjudan som skickas. Det finns alltid ett alternativ för den inbjudna att blockera framtida inbjudningar. Om organisationen har [angett en sekretesspolicy](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-properties-area)visas länken till satsen här.  Annars anger en anteckning att organisationen inte har angett någon sekretesspolicy.

![Bild av sidfotsavsnittet i e-postmeddelandet](media/invitation-email-elements/footer-section.png)
 
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
