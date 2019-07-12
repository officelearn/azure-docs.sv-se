---
title: Lösenord – Azure Active Directory som förbjuds dynamiskt
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
ms.openlocfilehash: c043b2ed1a626e362d7edd1a83429aa14046f8ac
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67703064"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Eliminera felaktiga lösenord i din organisation

Branschledare berättar inte ska använda samma lösenord på flera platser, så att de blir komplexa och att inte göra det enkelt som ”/ Password123”. Hur kan organisationer till garanterar att användarna följer Metodtips? Hur ser de till användarna inte använder svaga lösenord eller även varianter på svaga lösenord?

Det första steget i att starkare lösenord är att ge vägledning till användarna. Microsofts aktuella vägledning för det här avsnittet finns på följande länk:

[Lösenordet för Microsoft-vägledning](https://www.microsoft.com/research/publication/password-guidance)

Med bra vägledning är viktiga, men även med att vi vet att många användare kommer fortfarande till slut väljer svaga lösenord. Azure AD-lösenordsskydd skyddar din organisation genom att identifiera och blockera kända svaga lösenord och deras varianter, samt om du vill blockera ytterligare svaga villkor som är specifika för din organisation.

Mer information om säkerhetsarbete finns i den [Microsoft Security Intelligence Report](https://www.microsoft.com/security/operations/security-intelligence-report).

## <a name="global-banned-password-list"></a>Lista med globala förbjudna lösenord

Azure AD Identity Protection-teamet analyserar kontinuerligt Azure AD security telemetri data söker för vanliga svaga eller skadade lösenord eller mer specifikt, weak basera termer som ofta används som grund för svaga lösenord. När villkoren svaga hittas, läggs de till i listan med globala förbjudna lösenord. Innehållet i listan över globala förbjudna lösenord baseras inte på någon extern datakälla. Lista med globala förbjudna lösenord baseras helt på pågående resultat av Azure AD security telemetri och analys.

När ett nytt lösenord har ändrats eller återställa för alla användare i alla klient i Azure AD, används den aktuella versionen av listan över globala förbjudna lösenord som nyckeln indata vid verifiering av styrkan i lösenordet. Den här verifieringen resulterar i mycket starkare lösenord för alla Azure AD-kunder.

> [!NOTE]
> Cyberbrottslingar använder också liknande strategier i sina attacker. Därför publicerar Microsoft inte innehållet i den här listan offentligt.

## <a name="custom-banned-password-list"></a>Anpassad förbjuden lista med lösenord

Vissa organisationer vilja öka säkerheten ytterligare genom att lägga till egna anpassningar utöver listan globala förbjudna lösenord inom det som Microsoft kallar listan över anpassade förbjudna lösenord. Microsoft rekommenderar att villkoren läggs till i listan är fokuserar främst på organisationsspecifika villkor som:

- Varumärken
- Produktnamn
- Platser (till exempel, till exempel företagets huvudkontor)
- Företagsspecifika interna villkor
- Förkortningarna som har visst företag, vilket innebär att.

När villkoren har lagts till i listan med anpassade förbjudna lösenord, läggs de till i listan med globala förbjudna lösenord vid verifiering av lösenord.

> [!NOTE]
> Lista med anpassade förbjudna lösenord är begränsad till att ha högst 1000 villkor. Det är inte avsedd för blockering av mycket stora listor av lösenord. För att fullständigt utnyttja fördelarna med listan över anpassade förbjudna lösenord, Microsoft rekommenderar att du först granska och förstå algoritmen lösenord utvärdering (se [hur utvärderas lösenord](concept-password-ban-bad.md#how-are-passwords-evaluated)) innan du lägger till nya allmänna till den anpassad förbjudna lista. För att förstå hur algoritmen fungerar kan företaget för att effektivt identifiera och blockera stort antal svaga lösenord och deras varianter.

Till exempel: Överväg att en kund med namnet ”Contoso”, som är baserade i London och som gör att en produkt med namnet ”Widget”. För en kund, skulle det vara slösaktig samt mindre säkert att blockera specifika varianter av dessa villkor som:

- "Contoso!1"
- "Contoso@London"
- "ContosoWidget"
- "!Contoso"
- "LondonHQ"
- ...etcetera

I stället är det mycket mer effektivt och säkert att blockera endast de viktigaste grundläggande begreppen:

- "Contoso"
- "London"
- "Widget"

Lösenord verifieringsalgoritm blockerar sedan automatiskt svaga varianter och kombinationer av ovanstående.

Anpassat förbjuden lösenordslista och möjligheten att aktivera en lokal Active Directory integration hanteras med hjälp av Azure portal.

![Ändra listan anpassade förbjudna lösenord under autentiseringsmetoder](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>Lösenord besprutningsmedel attacker och listor med tredje parts olämpligt lösenord

En nyckel Azure AD-lösenord protection förmånen är att hjälpa dig skydda mot lösenord besprutningsmedel attacker. De flesta lösenord besprutningsmedel attacker försöker inte attackera alla angivna enskilda konton mer än ett par gånger eftersom sådant beteende ökar sannolikheten för identifiering, antingen via kontoutelåsning eller på annat sätt. Flesta lösenord besprutningsmedel attacker därför förlitar sig på skickar endast ett litet antal kända svagaste lösenorden mot varje konto i ett företag. Den här tekniken kan angriparen att snabbt söka efter ett enkelt komprometterat konto samtidigt undvika potentiella tröskelvärden för identifiering.

Azure AD-lösenordsskydd har utformats för att effektivt blockera alla kända svaga lösenord som sannolikt kommer att användas i lösenordet besprutningsmedel attacker, baserat på verkliga security telemetridata som visas av Azure AD.  Microsoft är medveten om tredjepartswebbplatser som räknar upp miljontals lösenord som har komprometterats i föregående offentligt kända säkerhetsproblem. Det är vanligt för tredje parts lösenord verifiering produkter ska väljas utifrån brute force-jämförelse mot dessa miljoner lösenord. Microsoft anser att sådana tekniker som inte är det bästa sättet att förbättra övergripande lösenordssäkerhet får de vanliga strategier som används av lösenord besprutningsmedel angripare.

> [!NOTE]
> Microsofts globala förbjudna lösenord inte är baserad helst på alla data från tredje part datakällor, inklusive olämpligt lösenordslistor.

Även om Microsoft global förbjudna lista är liten jämfört med vissa tredjeparts-bulk-listor, dess säkerhet påverkan är framhävas av det faktum att den kommer från verkliga security telemetri på riktiga lösenordet besprutningsmedel attacker, samt faktumet som Microsofts lösenord verifieringsalgoritm använder smart partiell matchning. Slutresultatet är att den effektivt identifiera och blockera miljontals vanligaste svaga lösenord används i företaget. Kunder som väljer att lägga till specifika termer i listan över anpassade förbjudna lösenord kan också dra nytta av samma algoritm.

## <a name="on-premises-hybrid-scenarios"></a>Lokala hybridscenarier

Skyddar molnbaserad konton är användbar men många organisationer behåller hybridscenarier, inklusive lokala Windows Server Active Directory. Det är möjligt att installera Azure AD-lösenordsskydd för Windows Server Active Directory agenter lokalt att utöka listor med förbjudna lösenord till din befintliga infrastruktur. Nu användare och administratörer som ändrar, ange eller återställa lösenord krävs lokala att följa principen med samma lösenord som molnexklusiva användare.

## <a name="how-are-passwords-evaluated"></a>Hur utvärderas lösenord

När en användare ändrar eller återställer sitt lösenord, kontrolleras det nya lösenordet för styrka och komplexiteten genom att verifiera mot den globala och anpassade förbjudna lösenord listan (om det senare är konfigurerat).

Även om en användares lösenord innehåller en förbjudna lösenord, godtas fortfarande lösenordet om det övergripande lösenordet är starkt nog annars. Ett nyligen konfigurerade lösenord går igenom följande steg för att utvärdera den övergripande styrkan för att avgöra om det ska godkännas eller avvisas.

### <a name="step-1-normalization"></a>Steg 1: Normalisering

Ett nytt lösenord först går igenom en normaliseringsprocessen. Den här tekniken möjliggör en liten uppsättning med förbjudna lösenord som ska mappas till ett mycket större antal potentiellt svaga lösenord.

Normalisering består av två delar.  Första, versaler bokstäver ändras till gemener.  Andra, vanliga tecken ersättningar som utförs, till exempel:  

| Ursprungliga bokstav  | Ersatta bokstav |
| --- | --- |
| '0'  | ' formatmönster |
| '1'  | 'l' |
| '$'  | 's' |
| '\@'  | ”a” |

Exempel: Anta att lösenordet ”blank” är bannlyst och en användare försöker att ändra sina lösenord till ”Bl@nK”. Även om ”Bl@nk” är inte specifikt förbjuden normaliseringsprocessen konverterar det här lösenordet till ”blank”, som är förbjudna lösenord.

### <a name="step-2-check-if-password-is-considered-banned"></a>Steg 2: Kontrollera om lösenordet betraktas som bannlyst

#### <a name="fuzzy-matching-behavior"></a>Fuzzy matchande beteende

Partiell matchning används på normaliserad lösenord för att identifiera om den innehåller ett lösenord som finns på antingen den globala eller ett anpassat förbjudna lösenordslistor. Matchningsprocessen baseras på en redigera avståndet från ett (1) jämförelse.  

Exempel: Anta att lösenordet ”abcdef” är bannlyst och en användare försöker att ändra sina lösenord till något av följande:

'abcdeg'    *(senaste tecken har ändrats från ”f” till ”g”)* 'innehållet abcdefg'   *”(g' tillagda slutet)* 'abcde'     *(avslutande f har tagits bort från slutet)*

Var och en av de ovanstående lösenorden matchar inte specifikt förbjudna lösenord ”abcdef”. Men eftersom varje exempel är inom ett redigera avståndet från 1 av förbjudna termen ”abcdef”, betraktas de alla som en matchning för att ”abcdef”.

#### <a name="substring-matching-on-specific-terms"></a>Delsträngen som matchar (på specifika villkor)

Delsträngen matchar används på normaliserad lösenordet för att kontrollera om användarens första och sista namnet samt klientnamnet (Observera att klienten namn matchar inte görs vid verifiering av lösenord på en Active Directory-domänkontrollant).

Exempel: Anta att vi har en användare, Pol, som vill återställa sina lösenord till ”P0l123fb”. Efter normalisering blir det här lösenordet ”pol123fb”. Delsträngen matchar söker du efter att lösenordet innehåller användarens förnamn ”Pol”. Även om ”P0l123fb” inte var särskilt på antingen lista med förbjudna lösenord, hitta delsträngen matchar ”Pol” i lösenordet. Det här lösenordet skulle därför avvisas.

#### <a name="score-calculation"></a>Poängberäkningen

Nästa steg är att identifiera alla instanser av förbjudna lösenord i användarens normaliserade nytt lösenord. Sedan:

1. Varje förbjudna lösenord som finns i en användares lösenord får en punkt.
2. Varje återstående unika tecken får en punkt.
3. Ett lösenord måste vara minst fem (5) datapunkter för att det ska godkännas.

I de två exemplen antar vi att Contoso använder Azure AD-lösenordsskydd och har ”contoso” på sina anpassade listor. Vi antar också att ”tom” finns på den globala listan.

Exempel: en användare ändrar sina lösenord till ”C0ntos0Blank12”

Efter normalisering blir det här lösenordet ”contosoblank12”. Matchningsprocessen hittar att det här lösenordet innehåller två förbjudna lösenord: contoso och tom. Det här lösenordet sedan får en poäng:

[contoso] + [tomma] + [1] + [2] = 4 punkter eftersom det här lösenordet används under fem (5) punkter, avvisas.

Exempel: en användare ändrar sina lösenord för att ”ContoS0Bl@nkf9”!.

Efter normalisering blir det här lösenordet ”contosoblankf9”!. Matchningsprocessen hittar att det här lösenordet innehåller två förbjudna lösenord: contoso och tom. Det här lösenordet sedan får en poäng:

[contoso] + [tomma] + [f] + [9] + [!] = 5 punkter eftersom det här lösenordet är minst fem (5) punkter, accepteras.

   > [!IMPORTANT]
   > Observera att algoritmen förbjudna lösenord tillsammans med den globala listan kan och ändra när som helst i Azure baserat på pågående säkerhetsanalyser och forskning. För en lokal DC agent-tjänsten träder uppdaterade algoritmer endast i kraft när DC-agentprogramvaran är installerades.

## <a name="license-requirements"></a>Licenskrav

|   | Azure AD-lösenordsskydd med globala förbjudna lösenordslista | Azure AD-lösenordsskydd med anpassade förbjudna lösenordslista|
| --- | --- | --- |
| Molnexklusiva användare | Azure AD Kostnadsfri | Azure AD Premium P1 eller P2 |
| Användare som synkroniseras från den lokala Windows Server Active Directory | Azure AD Premium P1 eller P2 | Azure AD Premium P1 eller P2 |

> [!NOTE]
> Fördelarna med Azure AD-lösenordsskydd baserat på befintliga licenser för synkroniserade användare har även ta del av den lokala Windows Server Active Directory-användare som inte synkroniseras med Azure Active Directory.

Ytterligare information om licenser går inklusive kostnader, kan hittas på den [priser platsen för Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>Vad ser användarna

När en användare försöker att återställa ett lösenord till något som skulle vara förbjuden, visas följande felmeddelande visas:

Tyvärr innehåller ditt lösenord ett ord, en fras eller ett mönster som gör det enkelt att gissa ditt lösenord. Försök igen med ett annat lösenord.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera listan med anpassade förbjudna lösenord](howto-password-ban-bad.md)
- [Aktivera Azure AD lösenord protection agenter lokalt](howto-password-ban-bad-on-premises-deploy.md)
