---
title: Lösenord som förbjuds dynamiskt i Azure AD
description: Förbjud svaga lösenord från din miljö med Azure AD som förbjuds dynamiskt-lösenord
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca079fa2ec41f85c365102fbd81ffde23e97e2c4
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2019
ms.locfileid: "56990500"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Eliminera felaktiga lösenord i din organisation

|     |
| --- |
| Azure AD-lösenordsskydd och listan över anpassade förbjudna lösenord är funktioner i offentlig förhandsversion av Azure Active Directory. Mer information om förhandsversioner finns [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Branschledare berättar inte ska använda samma lösenord på flera platser, så att de blir komplexa och att inte göra det enkelt som/Password123. Hur kan organisationer till garanterar att användarna följer vägledning? Hur ser de till användarna inte använder vanliga lösenord eller lösenord som är kända som ska ingå i de senaste dataintrång?

## <a name="global-banned-password-list"></a>Lista med globala förbjudna lösenord

Microsoft försöker alltid ligga steget före cyberbrottslingarna. Azure AD Identity Protection-teamet letar därför kontinuerligt efter vanligt förekommande och komprometterade lösenord. De kan sedan blockera dessa lösenord som bedöms för vanligt i något som kallas listan globala förbjudna lösenord. Cyberbrottslingar använder också liknande strategier i sina attacker, därför Microsoft publicerar inte innehållet i den här listan offentligt. Dessa sårbara lösenord blockeras innan de blir ett verkligt hot för Microsofts kunder. Mer information om säkerhetsarbete finns i den [Microsoft Security Intelligence Report](https://www.microsoft.com/security/operations/security-intelligence-report).

## <a name="preview-custom-banned-password-list"></a>Förhandsversion: Lista över anpassade förbjudna lösenord

Vissa organisationer vilja ta security ett steg längre genom att lägga till egna anpassningar utöver listan globala förbjudna lösenord inom det som Microsoft kallar listan över anpassade förbjudna lösenord. Enterprise-kunder som Contoso kan sedan välja att blockera varianter av sina egna varumärken, företagsspecifik villkor eller andra objekt.

Anpassat förbjuden lösenordslista och möjligheten att aktivera en lokal Active Directory integration hanteras med hjälp av Azure portal.

![Ändra den anpassade lista med förbjudna lösenord under autentiseringsmetoder i Azure portal](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="on-premises-hybrid-scenarios"></a>Lokala hybridscenarier

Skyddar molnbaserad konton är användbar men många organisationer behåller hybridscenarier, inklusive lokala Windows Server Active Directory. Det är möjligt att installera Azure AD-lösenordsskydd för Windows Server Active Directory (förhandsversion) agenter lokalt att utöka listor med förbjudna lösenord till din befintliga infrastruktur. Nu användare och administratörer som ändrar, ange eller återställa lösenord krävs lokala att följa principen med samma lösenord som molnexklusiva användare.

## <a name="how-are-passwords-evaluated"></a>Hur utvärderas lösenord

När en användare ändrar eller återställer sitt lösenord, kontrolleras det nya lösenordet för styrka och komplexiteten genom att verifiera mot den globala och anpassade förbjudna lösenord listan (om det senare är konfigurerat).

Även om en användares lösenord innehåller en förbjudna lösenord, godtas fortfarande lösenordet om det övergripande lösenordet är starkt nog annars. Ett nyligen konfigurerade lösenord går igenom följande steg för att utvärdera den övergripande styrkan för att avgöra om det ska godkännas eller avvisas.

### <a name="step-1-normalization"></a>Steg 1: Normalisering

Ett nytt lösenord först går igenom en normaliseringsprocessen. Det möjliggör en liten uppsättning med förbjudna lösenord som ska mappas till ett mycket större antal potentiellt svaga lösenord.

Normalisering består av två delar.  Första, versaler bokstäver ändras till gemener.  Andra, vanliga tecken ersättningar som utförs, till exempel:  

| Ursprungliga bokstav  | Ersatta bokstav |
| --- | --- |
| '0'  | ' formatmönster |
| '1'  | 'l' |
| '$'  | 's' |
| '@'  | ”a” |

Exempel: Anta att lösenordet ”blank” är bannlyst och en användare försöker att ändra sina lösenord till ”Bl@nK”. Även om ”Bl@nk” är inte specifikt förbjuden normaliseringsprocessen konverterar det här lösenordet till ”blank”, som är förbjudna lösenord.

### <a name="step-2-check-if-password-is-considered-banned"></a>Steg 2: Kontrollera om lösenordet betraktas som bannlyst

#### <a name="fuzzy-matching-behavior"></a>Fuzzy matchande beteende

Partiell matchning används på normaliserad lösenord för att identifiera om den innehåller ett lösenord som finns på antingen den globala eller ett anpassat förbjudna lösenordslistor. Matchningsprocessen baseras på en redigera avståndet från ett (1) jämförelse.  

Exempel: Anta att lösenordet ”abcdef” är bannlyst och en användare försöker att ändra sina lösenord till något av följande:

'abcdeg'    *(senaste tecken har ändrats från ”f” till ”g”)* 'innehållet abcdefg'   *”(g' tillagda slutet)* 'abcde'     *(avslutande f har tagits bort från slutet)*

Var och en av de ovanstående lösenorden matchar inte specifikt förbjudna lösenord ”abcdef”. Men eftersom varje exempel är inom ett redigera avståndet från 1 i den förbjudna token ”abcdef”, betraktas de alla som en matchning för att ”abcdef”.

#### <a name="substring-matching-on-specific-terms"></a>Delsträngen som matchar (på specifika villkor)

Delsträngen matchar används på normaliserad lösenordet för att kontrollera om användarens första och sista namnet samt klientnamnet (Observera att klienten namn matchar inte görs vid verifiering av lösenord på en Active Directory-domänkontrollant).

Exempel: Anta att vi har en användare John Berg som vill återställa sitt lösenord till ”J0hn123fb”. Efter normalisering blir det här lösenordet ”john123fb”. Delsträngen matchar söker du efter att lösenordet innehåller användarens förnamn ”John”. Även om ”J0hn123fb” inte var särskilt på antingen lista med förbjudna lösenord, hitta delsträngen matchar ”John” i lösenordet. Det här lösenordet skulle därför avvisas.

#### <a name="score-calculation"></a>Poängberäkningen

Nästa steg är att identifiera alla instanser av förbjudna lösenord i användarens normaliserade nytt lösenord. Sedan:

1. Varje förbjudna lösenord som finns i en användares lösenord får en punkt.
2. Varje återstående unika tecken får en punkt.
3. Ett lösenord måste vara minst 5 punkter för att det ska godkännas.

I de två exemplen antar vi att Contoso använder Azure AD-lösenordsskydd och har ”contoso” på sina anpassade listor. Vi antar också att ”tom” finns på den globala listan.

Exempel: en användare ändrar sina lösenord till ”C0ntos0Blank12”

Efter normalisering blir det här lösenordet ”contosoblank12”. Matchningsprocessen hittar att det här lösenordet innehåller två förbjudna lösenord: contoso och tom. Det här lösenordet sedan får en poäng:

[contoso] + [tomma] + [1] + [2] = 4 punkter eftersom det här lösenordet används under 5 punkter, avvisas.

Exempel: en användare ändrar sina lösenord för att ”ContoS0Bl@nkf9”!.

Efter normalisering blir det här lösenordet ”contosoblankf9”!. Matchningsprocessen hittar att det här lösenordet innehåller två förbjudna lösenord: contoso och tom. Det här lösenordet sedan får en poäng:

[contoso] + [tomma] + [f] + [9] + [!] = 5 punkter eftersom det här lösenordet är minst 5 punkter kan accepteras.

   > [!IMPORTANT]
   > Observera att algoritmen förbjudna lösenord tillsammans med den globala listan kan och ändra när som helst i Azure baserat på pågående säkerhetsanalyser och forskning. För en lokal DC agent-tjänsten träder uppdaterade algoritmer endast i kraft när DC-agentprogramvaran är installerades.

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
