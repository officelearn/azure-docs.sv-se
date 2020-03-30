---
title: Lösenord för förbjudna lösenord dynamiskt - Azure Active Directory
description: Spärra svaga lösenord från din miljö med Azure AD förbjudna lösenord dynamiskt
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef127d120b32f5344bce0f68d79f48401087f0ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264003"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Eliminera felaktiga lösenord i din organisation

Branschledare säger åt dig att inte använda samma lösenord på flera ställen, för att göra det komplext och att inte göra det enkelt som "Password123". Hur kan organisationer garantera att användarna följer vägledningen för bästa praxis? Hur kan de se till att användarna inte använder svaga lösenord, eller ens variationer på svaga lösenord?

Det första steget i att få starkare lösenord är att ge vägledning till användarna. Microsofts aktuella vägledning om detta ämne finns på följande länk:

[Microsoft lösenordsvägledning](https://www.microsoft.com/research/publication/password-guidance)

Att ha bra vägledning är viktigt, men även med att vi vet att många användare fortfarande kommer att sluta välja svaga lösenord. Azure AD Password Protection skyddar din organisation genom att identifiera och blockera kända svaga lösenord och deras varianter, samt eventuellt blockera ytterligare svaga termer som är specifika för din organisation.

Mer information om aktuella säkerhetsinsatser finns i [Microsoft Security Intelligence Report](https://www.microsoft.com/security/operations/security-intelligence-report).

## <a name="global-banned-password-list"></a>Global lista över förbjudna lösenord

Azure AD Identity Protection-teamet analyserar ständigt Azure AD-säkerhetstelemetridata som letar efter vanliga svaga eller komprometterade lösenord, eller mer specifikt de svaga bastermer som ofta används som grund för svaga lösenord. När sådana svaga termer hittas läggs de till i den globala listan över förbjudna lösenord. Innehållet i den globala listan över förbjudna lösenord baseras inte på någon extern datakälla. Den globala listan över förbjudna lösenord baseras helt på de pågående resultaten av Azure AD-säkerhetstelemetri och analys.

När ett nytt lösenord ändras eller återställs för alla användare i alla innehavare i Azure AD används den aktuella versionen av den globala listan över förbjudna lösenord som nyckelinmatning när lösenordets styrka valideras. Den här valideringen resulterar i mycket starkare lösenord för alla Azure AD-kunder.

> [!NOTE]
> Cyberbrottslingar använder också liknande strategier i sina attacker. Microsoft publicerar därför inte innehållet i denna lista offentligt.

## <a name="custom-banned-password-list"></a>Anpassad lista över förbjudna lösenord

Vissa organisationer kanske vill förbättra säkerheten ytterligare genom att lägga till egna anpassningar ovanpå den globala listan över förbjudna lösenord i vad Microsoft kallar den anpassade listan över förbjudna lösenord. Microsoft rekommenderar att termer som läggs till i den här listan i första hand är inriktade på organisationsspecifika termer, till exempel:

- Varumärken
- Produktnamn
- Platser (till exempel företagets huvudkontor)
- Företagsspecifika interna termer
- Förkortningar som har specifik företagsmening.

När termer läggs till i den anpassade förbjudna lösenordslistan kombineras de med villkoren i den globala listan över förbjudna lösenord när lösenord valideras.

> [!NOTE]
> Den anpassade listan över förbjudna lösenord är begränsad till att ha högst 1000 termer. Den är inte avsedd för att blockera extremt stora listor över lösenord. För att till fullo utnyttja fördelarna med den anpassade listan över förbjudna lösenord rekommenderar Microsoft att du först granskar och förstår [lösenordsutvärderingsalgoritmen](concept-password-ban-bad.md#how-are-passwords-evaluated)(se Hur utvärderas lösenord ) innan du lägger till nya termer i den anpassade förbjudna listan. Om du förstår hur algoritmen fungerar kan ditt företag effektivt upptäcka och blockera ett stort antal svaga lösenord och deras varianter.

Till exempel: betrakta en kund som heter "Contoso", som är baserad i London, och som gör en produkt som heter "Widget". För en sådan kund skulle det vara slösaktigt och mindre säkert att försöka blockera specifika varianter av dessa termer såsom:

- "Contoso!1"
- "Contoso@London"
- "ContosoWidget"
- "! Contoso"
- "LondonHQ"
- ... Etc

Istället är det mycket effektivare och säkrare att blockera endast de viktigaste basvillkoren:

- ”Contoso”
- "London"
- "Widget"

Lösenordsvalideringsalgoritmen blockerar sedan automatiskt svaga varianter och kombinationer av ovanstående.

Den anpassade listan över förbjudna lösenord och möjligheten att aktivera lokal Active Directory-integrering hanteras med Azure-portalen.

![Ändra listan med anpassade förbjudna lösenord under autentiseringsmetoder](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>Lösenordssprayattacker och lösenordslistor från tredje part

En viktig Azure AD lösenordsskydd fördel är att hjälpa dig att försvara mot lösenord spray attacker. De flesta lösenord spray attacker inte försöker attackera ett visst enskilt konto mer än ett par gånger eftersom sådant beteende kraftigt ökar sannolikheten för upptäckt, antingen via kontoutelåsning eller andra medel. Majoriteten av lösenord spray attacker förlitar sig därför på att lämna in endast ett litet antal av de kända svagaste lösenord mot var och en av kontona i ett företag. Med den här tekniken kan angriparen snabbt söka efter ett konto som inte komprometterats samtidigt som potentiella identifieringströsklar undviks.

Azure AD lösenordsskydd är utformat för att effektivt blockera alla kända svaga lösenord som sannolikt kommer att användas i lösenordssprayattacker, baserat på verkliga säkerhetstelemetridata som ses av Azure AD.  Microsoft är medvetet om webbplatser från tredje part som räknar upp miljontals lösenord som har komprometterats i tidigare allmänt kända säkerhetsöverträdelser. Det är vanligt att tredje parts lösenord valideringsprodukter som ska baseras på brute-force jämförelse mot dessa miljontals lösenord. Microsoft anser att sådana tekniker inte är det bästa sättet att förbättra den totala lösenordsstyrkan med tanke på de typiska strategier som används av lösenordssprayangripare.

> [!NOTE]
> Microsofts globala lista över förbjudna lösenord baseras inte alls på några datakällor från tredje part, inklusive komprometterade lösenordslistor.

Även om Microsoft globala förbjudna listan är liten i jämförelse med vissa tredje part bulklistor, dess säkerhetseffekter förstärks av det faktum att det kommer från verkliga säkerhet telemetri på faktiska lösenord spray attacker, plus det faktum att Microsoft lösenordsvalideringsalgoritmen använder smarta fuzzy-matchningstekniker. Slutresultatet är att det effektivt kommer att upptäcka och blockera miljontals av de vanligaste svaga lösenord från att användas i ditt företag. Kunder som väljer att lägga till organisationsspecifika termer i den anpassade listan över förbjudna lösenord drar också nytta av samma algoritm.

Ytterligare information om lösenordsbaserade säkerhetsproblem kan granskas på [Din Pa $ $word spelar ingen roll](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

## <a name="on-premises-hybrid-scenarios"></a>Lokala hybridscenarier

Det är praktiskt att skydda molnkonton, men många organisationer underhåller hybridscenarier, inklusive lokala Active Directory i Windows Server. Säkerhetsfördelarna med Azure AD-lösenordsskydd kan också utökas till din Active Directory-miljö i Windows Server via installation av lokala agenter. Nu måste användare och administratörer som ändrar eller återställer lösenord i Active Directory följa samma lösenordsprincip som användare som endast är molnet.

## <a name="how-are-passwords-evaluated"></a>Hur utvärderas lösenord

När en användare ändrar eller återställer sitt lösenord kontrolleras det nya lösenordet efter styrka och komplexitet genom att validera det mot den kombinerade listan med termer från de globala och anpassade förbjudna lösenordslistorna (om det senare är konfigurerat).

Även om en användares lösenord innehåller ett förbjudet lösenord kan lösenordet fortfarande accepteras om lösenordet är tillräckligt starkt annars. Ett nyligen konfigurerat lösenord går igenom följande steg för att bedöma dess övergripande styrka för att avgöra om det ska accepteras eller avvisas.

### <a name="step-1-normalization"></a>Steg 1: Normalisering

Ett nytt lösenord går först igenom en normaliseringsprocess. Denna teknik gör det möjligt för en liten uppsättning förbjudna lösenord som ska mappas till en mycket större uppsättning potentiellt svaga lösenord.

Normalisering har två delar.  För det första ändras alla versaler till gemener.  För det andra utförs vanliga karaktärssubstitutioner, till exempel:  

| Originalbrev  | Ersatt brev |
| --- | --- |
| '0'  | "o" |
| '1'  | "l" |
| '$'  | "s" |
| '\@'  | "a" |

Exempel: anta att lösenordet "tomt" är förbjudet och enBl@nKanvändare försöker ändra sitt lösenord till " ". Även omBl@nk" " inte uttryckligen är förbjudet, omvandlar normaliseringsprocessen detta lösenord till "tom", vilket är ett förbjudet lösenord.

### <a name="step-2-check-if-password-is-considered-banned"></a>Steg 2: Kontrollera om lösenordet anses förbjudet

#### <a name="fuzzy-matching-behavior"></a>Luddigt matchande beteende

Fuzzy-matchning används på det normaliserade lösenordet för att identifiera om det innehåller ett lösenord som finns på antingen de globala eller anpassade förbjudna lösenordslistorna. Matchningsprocessen baseras på ett redigeringsavstånd på en (1) jämförelse.  

Exempel: anta att lösenordet "abcdef" är förbjudet och en användare försöker ändra sitt lösenord till något av följande:

'abcdeg' *(sista teckenet ändrades från 'f' till 'g')* 'abcdefg' *'(g' bifogad till slut)* 'abcde' *(avslutande 'f' togs bort från slutet)*

Var och en av ovanstående lösenord inte specifikt matchar det förbjudna lösenordet "abcdef". Men eftersom varje exempel är inom ett redigeringsavstånd på 1 av den förbjudna termen "abcdef", de är alla betraktas som en match till "abcdef".

#### <a name="substring-matching-on-specific-terms"></a>Matchning av delsträng (på specifika villkor)

Mellansträngsmatchning används på det normaliserade lösenordet för att söka efter användarens för- och efternamn samt klientnamnet (observera att klientnamnmatchning inte görs när lösenord valideras på en Active Directory-domänkontrollant).

Exempel: anta att vi har en användare, Pol, som vill återställa sitt lösenord till "P0l123fb". Efter normalisering, skulle detta lösenord bli "pol123fb". Mellanliggande matchning visar att lösenordet innehåller användarens förnamn "Pol". Även om "P0l123fb" inte var specifikt på antingen förbjudna lösenord lista, substring matchande hittade "Pol" i lösenordet. Därför skulle detta lösenord avvisas.

#### <a name="score-calculation"></a>Beräkning av poäng

Nästa steg är att identifiera alla instanser av förbjudna lösenord i användarens normaliserade nya lösenord. Sedan:

1. Varje förbjudna lösenord som finns i en användares lösenord ges en poäng.
2. Varje återstående unikt tecken får en poäng.
3. Ett lösenord måste vara minst fem (5) poäng för att det ska accepteras.

För de följande två exemplen, låt oss anta att Contoso använder Azure AD lösenordsskydd och har "contoso" på sin anpassade lista. Låt oss också anta att "tom" finns på den globala listan.

Exempel: en användare ändrar sitt lösenord till "C0ntos0Blank12"

Efter normalisering blir detta lösenord "contosoblank12". Matchningsprocessen visar att det här lösenordet innehåller två förbjudna lösenord: contoso och tom. Detta lösenord får sedan en poäng:

[contoso] + [blank] + [1] + [2] = 4 poäng Eftersom detta lösenord är under fem (5) punkter, kommer det att avvisas.

Exempel: en användare ändrarContoS0Bl@nkf9sitt lösenord till " !".

Efter normalisering blir detta lösenord "contosoblankf9!". Matchningsprocessen visar att det här lösenordet innehåller två förbjudna lösenord: contoso och tom. Detta lösenord får sedan en poäng:

[contoso] + [blank] + [f] + [9] + [!] = 5 poäng Eftersom detta lösenord är minst fem (5) punkter accepteras det.

   > [!IMPORTANT]
   > Observera att den förbjudna lösenordsalgoritmen tillsammans med den globala listan kan och ändras när som helst i Azure baserat på pågående säkerhetsanalys och forskning. För den lokala DC-agenttjänsten börjar uppdaterade algoritmer endast gälla när DC-agentprogramvaran har installerats på för ny byggda.

## <a name="license-requirements"></a>Licenskrav

|   | Azure AD lösenordsskydd med global lista över förbjudna lösenord | Azure AD lösenordsskydd med anpassad lista över förbjudna lösenord|
| --- | --- | --- |
| Användare endast för molnet | Azure AD Kostnadsfri | Azure AD Premium P1 eller P2 |
| Användare synkroniserade från lokal Active Directory för Windows Server | Azure AD Premium P1 eller P2 | Azure AD Premium P1 eller P2 |

> [!NOTE]
> Lokala Active Directory-användare för Windows Server som inte är synkroniserade med Azure Active Directory drar också nytta av Azure AD-lösenordsskydd baserat på befintlig licensiering för synkroniserade användare.

Ytterligare licensieringsinformation, inklusive kostnader, finns på [azure Active Directory-prisplatsen](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>Vad ser användarna

När en användare försöker återställa ett lösenord till något som skulle förbjudas visas följande felmeddelande:

Tyvärr innehåller ditt lösenord ett ord, en fras eller ett mönster som gör att ditt lösenord lätt kan gissas. Försök igen med ett annat lösenord.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera den anpassade listan över förbjudna lösenord](howto-password-ban-bad.md)
- [Aktivera Azure AD-lösenordsskyddsagenter lokalt](howto-password-ban-bad-on-premises-deploy.md)
