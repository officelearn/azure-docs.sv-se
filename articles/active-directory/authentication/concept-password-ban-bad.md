---
title: Dynamiskt förbjudna lösenord i Azure AD
description: Förbjuda svaga lösenord från din miljö med Azure AD dynamiskt förbjudna-lösenord
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: rogoya
ms.openlocfilehash: 89cbe386d87c6ccb81df7fabd86b197bb69e41e1
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295612"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Ta bort felaktiga lösenord i din organisation

|     |
| --- |
| Azure AD-lösenordsskydd och lösenordslistan över anpassade förbjudna är förhandsversion funktioner i Azure Active Directory. Läs mer om förhandsgranskningar [kompletterande användningsvillkor för Microsoft Azure-förhandsgranskningar](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Branschledare anger att du inte ska använda samma lösenord på flera platser att komplex och inte gör det enkelt som Password123. Hur kan organisationer till garantera att användarna följer riktlinjer? Hur de ser till användare som inte använder vanliga lösenord eller lösenord som ska ingå i senaste dataintrång?

## <a name="global-banned-password-list"></a>Lista med globala förbjudna lösenord

Microsoft arbetar alltid för att hålla ett steg i cyber kriminella. Azure AD Identity Protection-teamet letar därför kontinuerligt efter vanligt förekommande och avslöjade lösenord. De kan sedan blockera de lösenord som bedöms för vanligt i något som kallas lösenordslistan globala förbjudna. Cyber kriminella också använda liknande strategier i sina attacker, därför Microsoft publicerar inte innehållet i den här listan offentligt. Dessa sårbara lösenord blockeras innan de blir ett verkliga hot mot Microsofts kunder. Mer information om den nuvarande säkerhet åtgärder finns i [Microsoft Security Intelligence-rapporten](https://www.microsoft.com/security/intelligence-report).

## <a name="preview-custom-banned-password-list"></a>Förhandsversion: Anpassad förbjudna lista med lösenord

Vissa organisationer vilja tar ytterligare ett säkerhetssteg genom att lägga till egna anpassningar ovanpå lösenordslistan globala förbjudna i vad Microsoft anropar lösenordslistan över anpassade förbjudna. Enterprise-kunder som Contoso kan sedan välja att blockera varianter av sina egna varumärken, företagsspecifik villkoren och andra objekt.

Anpassat förbjudna lista med lösenord och möjligheten att aktivera lokala Active Directory integration hanteras med Azure-portalen.

![Ändra listan anpassade förbjudna lösenord under autentiseringsmetoder i Azure-portalen](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="on-premises-hybrid-scenarios"></a>Lokala hybridmoln

Skydda endast molnbaserad konton är användbar men många organisationer behåller hybridscenarier inklusive lokala Windows Server Active Directory. Det är möjligt att installera Azure AD lösenordsskydd för Windows Server Active Directory (förhandsgranskning) agenter lokalt att utöka listorna förbjudna lösenord till din befintliga infrastruktur. Nu användare och administratörer som ändrar, ange eller återställa lösenord krävs lokalt att följa principen med samma lösenord som endast molnbaserad användare.

## <a name="how-does-the-banned-password-list-work"></a>Hur fungerar lösenordslistan förbjudna

Lösenordslistan över förbjudna matchar lösenord i listan genom att konvertera strängen till gemener och jämför till kända förbjudna lösenord i Redigera avståndet mellan 1 med fuzzy matchar.

Exempel: Word-lösenord har blockerats för en organisation
   - En användare försöker att ange sina lösenord till ”P@ssword” som konverteras till ”password” och eftersom det är en variant av lösenord är blockerad.
   - En administratör försöker att ange ett lösenord för användare att ”Password123”! som konverteras till ”password123”! och eftersom det är en variant av lösenord är blockerad.

Varje gång en användare återställer eller ändrar sina Azure AD-lösenord som den förs vidare via den här processen för att bekräfta att det inte är i lösenordslistan över förbjudna. Den här kontrollen ingår i scenarier med självbetjäning lösenord återställa hybrid, lösenordshashsynkronisering och direktautentisering.

## <a name="license-requirements"></a>Licenskrav

Fördelarna med lösenordslistan globala förbjudna gäller för alla användare av Azure Active Directory (AD Azure).

Lösenordslistan över anpassade förbjudna kräver Azure AD Basic licenser.

Azure AD-lösenordsskydd för Windows Server Active Directory kräver Azure AD Premium-licenser. 

Ytterligare licensinformation, inklusive kostnader, kan hittas på den [Azure Active Directory priser plats](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>Vad ser användarna

När en användare försöker att återställa ett lösenord till något som skulle vara förbjuden, visas följande felmeddelande:

Lösenordet innehåller tyvärr ett ord, en fras eller en mönster som gör att enkelt fjärråtkomstlösning ditt lösenord. Försök igen med ett annat lösenord.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera lösenordslistan med anpassade förbjudna](howto-password-ban-bad.md)
* [Aktivera Azure AD lösenord protection agents lokalt](howto-password-ban-bad-on-premises.md)
