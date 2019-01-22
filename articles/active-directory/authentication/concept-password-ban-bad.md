---
title: Lösenord som förbjuds dynamiskt i Azure AD
description: Förbjud svaga lösenord från din miljö med Azure AD som förbjuds dynamiskt-lösenord
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.openlocfilehash: 7cb1acace3dd8605d7506013a6f1c0273dafa32f
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54421444"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Eliminera felaktiga lösenord i din organisation

|     |
| --- |
| Azure AD-lösenordsskydd och listan över anpassade förbjudna lösenord är funktioner i offentlig förhandsversion av Azure Active Directory. Mer information om förhandsversioner finns [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Branschledare berättar inte ska använda samma lösenord på flera platser, så att de blir komplexa och att inte göra det enkelt som/Password123. Hur kan organisationer till garanterar att användarna följer vägledning? Hur ser de till användarna inte använder vanliga lösenord eller lösenord som är kända som ska ingå i de senaste dataintrång?

## <a name="global-banned-password-list"></a>Lista med globala förbjudna lösenord

Microsoft försöker alltid ligga steget före cyberbrottslingarna. Azure AD Identity Protection-teamet letar därför kontinuerligt efter vanligt förekommande och komprometterade lösenord. De kan sedan blockera dessa lösenord som bedöms för vanligt i något som kallas listan globala förbjudna lösenord. Cyberbrottslingar använder också liknande strategier i sina attacker, därför Microsoft publicerar inte innehållet i den här listan offentligt. Dessa sårbara lösenord blockeras innan de blir ett verkligt hot för Microsofts kunder. Mer information om säkerhetsarbete finns i den [Microsoft Security Intelligence Report](https://www.microsoft.com/security/intelligence-report).

## <a name="preview-custom-banned-password-list"></a>Förhandsversion: Lista över anpassade förbjudna lösenord

Vissa organisationer vilja ta security ett steg längre genom att lägga till egna anpassningar utöver listan globala förbjudna lösenord inom det som Microsoft kallar listan över anpassade förbjudna lösenord. Enterprise-kunder som Contoso kan sedan välja att blockera varianter av sina egna varumärken, företagsspecifik villkor eller andra objekt.

Anpassat förbjuden lösenordslista och möjligheten att aktivera en lokal Active Directory integration hanteras med hjälp av Azure portal.

![Ändra den anpassade lista med förbjudna lösenord under autentiseringsmetoder i Azure portal](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="on-premises-hybrid-scenarios"></a>Lokala hybridscenarier

Skyddar molnbaserad konton är användbar men många organisationer behåller hybridscenarier, inklusive lokala Windows Server Active Directory. Det är möjligt att installera Azure AD-lösenordsskydd för Windows Server Active Directory (förhandsversion) agenter lokalt att utöka listor med förbjudna lösenord till din befintliga infrastruktur. Nu användare och administratörer som ändrar, ange eller återställa lösenord krävs lokala att följa principen med samma lösenord som molnexklusiva användare.

## <a name="how-does-the-banned-password-list-work"></a>Hur fungerar lista med förbjudna lösenord

Lista med förbjudna lösenord matchar lösenord i listan genom att konvertera strängen till gemener och jämförelse till kända förbjudna lösenorden inom en redigera avståndet från 1 med partiell matchning.

Exempel: Word-lösenordet är blockerad för en organisation
   - En användare försöker att ange sitt lösenord till ”P@ssword” som konverteras till ”lösenord” och eftersom det är en variant av lösenord är blockerad.
   - En administratör försöker ställa in en användares lösenord kan ”/ Password123”! som konverteras till ”/ password123”! och eftersom det är en variant av lösenord är blockerad.

Varje gång en användare återställer eller ändrar deras Azure AD-lösenord som den förs vidare via den här processen för att bekräfta att det inte finns på listan med förbjudna lösenord. Den här kontrollen ingår i hybrid scenarier med självbetjäning återställa lösenordets hash-synkronisering och direktautentisering.

## <a name="license-requirements"></a>Licenskrav

|   | Azure AD-lösenordsskydd med globala förbjudna lösenordslista | Azure AD-lösenordsskydd med anpassade förbjudna lösenordslista|
| --- | --- | --- |
| Molnexklusiva användare | Azure AD Kostnadsfri | Azure AD Basic |
| Användare som synkroniseras från den lokala Windows Server Active Directory | Azure AD Premium P1 eller P2 | Azure AD Premium P1 eller P2 |

Ytterligare information om licenser går inklusive kostnader, kan hittas på den [priser platsen för Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>Vad ser användarna

När en användare försöker att återställa ett lösenord till något som skulle vara förbjuden, visas följande felmeddelande visas:

Tyvärr innehåller ditt lösenord ett ord, en fras eller ett mönster som gör det enkelt att gissa ditt lösenord. Försök igen med ett annat lösenord.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera listan med anpassade förbjudna lösenord](howto-password-ban-bad.md)
* [Aktivera Azure AD lösenord protection agenter lokalt](howto-password-ban-bad-on-premises-deploy.md)
