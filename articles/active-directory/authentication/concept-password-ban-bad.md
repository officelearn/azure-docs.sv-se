---
title: Dynamiskt förbjudna lösen ord – Azure Active Directory
description: Förbjuda svaga lösen ord från din miljö med Azure ADs dynamiskt förbjudna lösen ord
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
ms.openlocfilehash: 27530b143e46acad4152e8333836cbe9c79fab17
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168091"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Eliminera Felaktiga lösen ord i din organisation

Bransch ledare berättar att du inte ska använda samma lösen ord på flera platser, för att göra det komplext och inte göra det enkelt som "Password123". Hur kan organisationer garantera att deras användare följer vägledningen för bästa praxis? Hur kan de se till att användarna inte använder svaga lösen ord eller till och med variationer i svaga lösen ord?

Det första steget med starkare lösen ord är att ge vägledning för dina användare. Microsofts aktuella vägledning om det här avsnittet finns på följande länk:

[Vägledning för Microsoft-lösenord](https://www.microsoft.com/research/publication/password-guidance)

Det är viktigt att ha bra vägledning, men även med att vi vet att många användare fortfarande kommer att använda svaga lösen ord. Azure AD Password Protection skyddar din organisation genom att identifiera och blockera kända svaga lösen ord och deras varianter, samt eventuellt blockera ytterligare svaga termer som är särskilda för din organisation.

Mer information om aktuella säkerhets åtgärder finns i [Microsoft Security Intelligence-rapporten](https://www.microsoft.com/security/operations/security-intelligence-report).

## <a name="global-banned-password-list"></a>Lista över globala förbjudna lösen ord

I Azure AD Identity Protections teamet analyseras hela Azure AD-säkerhetstelemetri som söker efter svaga eller komprometterade lösen ord, eller mer specifikt de svaga bas termer som ofta används som grund för svaga lösen ord. När sådana svaga villkor hittas läggs de till i listan globalt blockerade lösen ord. Innehållet i listan över globala förbjudna lösen ord baseras inte på externa data källor. Den globala listan över förbjudna lösen ord baseras helt på det löpande resultatet av Azure AD-säkerhetstelemetri och analys.

När ett nytt lösen ord ändras eller återställs för en användare i en klient i Azure AD, används den aktuella versionen av den globala listan över blockerade lösen ord som nyckel indata när du validerar lösen ordets styrka. Den här verifieringen resulterar i mycket starkare lösen ord för alla Azure AD-kunder.

> [!NOTE]
> Cyberhot-cyberbrottslingarna använder också liknande strategier i deras attacker. Därför publicerar Microsoft inte innehållet i den här listan offentligt.

## <a name="custom-banned-password-list"></a>Lista med anpassade förbjudna lösen ord

Vissa organisationer kanske vill förbättra säkerheten ytterligare genom att lägga till egna anpassningar ovanpå den globala listan över förbjudna lösen ord i vad Microsoft anropar den anpassade listan över blockerade lösen ord. Microsoft rekommenderar att termer som läggs till i listan främst fokuserar på organisatoriska villkor som:

- Märkes namn
- Produkt namn
- Platser (till exempel företags huvud kontor)
- Företagsspecifika interna villkor
- Förkortningar som har ett bestämt företags betydelse.

När du har lagt till villkoren i listan med anpassade förbjudna lösen ord, kombineras de med villkoren i listan globalt blockerade lösen ord när du validerar lösen ord.

> [!NOTE]
> Listan med anpassade förbjudna lösen ord är begränsad till högst 1000 villkor. Den är inte utformad för att blockera extremt stora listor med lösen ord. För att helt kunna utnyttja fördelarna med den anpassade listan över blockerade lösen ord rekommenderar Microsoft att du först granskar och förstår algoritmen för lösen ords utvärdering (se [hur är lösen ord utvärderas](concept-password-ban-bad.md#how-are-passwords-evaluated)) innan du lägger till nya villkor i den anpassade listan över förbjudna. Att förstå hur algoritmen fungerar gör det möjligt för företaget att effektivt identifiera och blockera stora mängder svaga lösen ord och deras varianter.

Exempel: Överväg en kund med namnet "contoso", som är baserad i London och som gör en produkt med namnet "widget". För en sådan kund är det både onödig och mindre säkert att försöka blockera specifika variationer av dessa villkor, t. ex.:

- "Contoso! 1"
- "Contoso@London"
- "ContosoWidget"
- "!Contoso"
- "LondonHQ"
- ...etcetera

I stället är det mycket mer effektivt och säkert att bara blockera nyckel bas villkoren:

- Contoso
- London
- Fält

Algoritmen för lösen ords verifiering blockerar sedan automatiskt svaga varianter och kombinationer av ovanstående.

Listan med anpassade förbjudna lösen ord och möjligheten att aktivera lokal Active Directory-integrering hanteras med hjälp av Azure Portal.

![Ändra listan med anpassade förbjudna lösen ord under autentiseringsmetoder](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>Ordlisteattacker och komprometterade lösen ords listor från tredje part

En viktig fördel med lösen ords skydd i Azure AD är att hjälpa dig att skydda dig mot attacker med lösen ords spridning. De flesta angrepp av lösen ord försöker inte angripa ett visst enskilt konto mer än några gånger eftersom beteendet avsevärt ökar sannolikheten för identifiering, antingen via konto utelåsning eller på annat sätt. Majoriteten av attacker med lösen ords spridning kräver därför att du bara skickar ett litet antal kända svaga lösen ord mot var och en av kontona i ett företag. Med den här tekniken kan angriparen snabbt söka efter ett lätt komprometterat konto samtidigt som de kan undvika tröskelvärden för potentiella identifieringar.

Lösen ords skydd i Azure AD är utformat för att effektivt blockera alla kända svaga lösen ord som troligen används vid ingrepp av lösen ord, baserat på verkliga säkerhetstelemetris data som visas i Azure AD.  Microsoft känner till webbplatser från tredje part som räknar upp miljon tals lösen ord som har komprometterats i tidigare offentligt kända säkerhets överträdelser. Det är vanligt att de produkter som används för att verifiera lösen ord från tredje part baseras på en jämförelse av den här miljonen av lösen ord. Microsoft anser att sådan teknik inte är det bästa sättet att förbättra den övergripande lösen ords styrkan med de typiska strategier som används vid ingrepp av lösen ord.

> [!NOTE]
> Microsoft Globals lista över blockerade lösen ord baseras inte på alla data källor från tredje part, inklusive listor över komprometterade lösen ord.

Även om den globala listan över blockerade Microsoft-listor är liten jämfört med vissa tredjeparts Mass listor, förstärks säkerhets effekterna av det faktum att den har sitt ursprung i verkliga säkerhetstelemetri för faktiska lösen ords attacker, plus det faktum att Microsoft algoritmen för lösen ords verifiering använder smarta, fuzzy-matchande tekniker. Slut resultatet är att det effektivt kan identifiera och blockera miljon tals vanligaste svaga lösen ord från att användas i företaget. Kunder som väljer att lägga till företagsspecifika termer i listan med anpassade förbjudna lösen ord kan också dra nytta av samma algoritm.

Ytterligare information om lösen ordsbaserade säkerhets problem kan ses över på [din pa $ $Word spelar ingen roll](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

## <a name="on-premises-hybrid-scenarios"></a>Lokala hybrid scenarier

Att skydda moln konton är användbart men många organisationer har kvar hybrid scenarier, inklusive lokala Windows Server-Active Directory. Säkerhets fördelarna med Azure AD Password Protection kan också utökas till Windows Server Active Directory-miljön via installationen av lokala agenter. Nu måste användare och administratörer som ändrar eller återställer lösen ord i Active Directory följa samma lösen ords princip som enbart moln användare.

## <a name="how-are-passwords-evaluated"></a>Hur utvärderas lösen ord

När en användare ändrar eller återställer sitt lösen ord, kontrol leras det nya lösen ordet för att styrka och komplexitet genom att verifiera den mot den kombinerade listan med termer från listorna globala och anpassade lösen ord (om den senare är konfigurerad).

Även om användarens lösen ord innehåller ett blockerat lösen ord, kan lösen ordet fortfarande accepteras om det övergripande lösen ordet är tillräckligt starkt. Ett nyligen konfigurerat lösen ord går igenom följande steg för att bedöma den övergripande styrkan för att avgöra om det ska godkännas eller avvisas.

### <a name="step-1-normalization"></a>Steg 1: normalisering

Ett nytt lösen ord går först igenom normaliserings processen. Den här tekniken gör det möjligt för en liten uppsättning blockerade lösen ord att mappas till en mycket större uppsättning potentiellt svaga lösen ord.

Normalisering har två delar.  För det första ändras alla versaler till gemener.  Andra, vanliga tecken ersättningar utförs, till exempel:  

| Ursprunglig bokstav  | Ersatt bokstav |
| --- | --- |
| 0,0  | a |
| 81.1  | l |
| '$'  | formulär |
| '\@'  | en |

Exempel: Antag att lösen ordet "Tom" är förbjudet och att en användare försöker ändra sitt lösen ord till "Bl@nK". Även om "Bl@nk" inte är särskilt blockerad konverterar normaliserings processen detta lösen ord till "tomt", vilket är ett blockerat lösen ord.

### <a name="step-2-check-if-password-is-considered-banned"></a>Steg 2: kontrol lera om lösen ordet anses vara förbjudet

#### <a name="fuzzy-matching-behavior"></a>Partiell matchnings beteende

Fuzzy Matching används på det normaliserade lösen ordet för att identifiera om det innehåller ett lösen ord som finns på antingen den globala eller den anpassade listan över blockerade lösen ord. Den matchande processen baseras på ett redigerings avstånd på en (1) jämförelse.  

Exempel: Antag att lösen ordet "ABCDEF" är förbjudet och att en användare försöker ändra sitt lösen ord till något av följande:

' abcdeg ' *(sista tecknet ändrades från ' f ' till ' g ')* ' ABCDEFG ' *' (g ' appendd to end)* ' ABCD ' *(efterföljande ' f ' togs bort från slutet)*

Vart och ett av lösen orden ovan matchar inte det förbjudna lösen ordet "ABCDEF". Men eftersom varje exempel ligger inom ett redigerings avstånd på 1 av den förbjudna termen "ABCDEF" betraktas de som en matchning till "ABCDEF".

#### <a name="substring-matching-on-specific-terms"></a>Matchning av del strängar (utifrån vissa villkor)

Del Strängs matchning används på det normaliserade lösen ordet för att kontrol lera användarens första och efter namn samt klient namnet (Observera att klient namns matchning inte görs när lösen ord verifieras på en Active Directory domänkontrollant).

Exempel: Antag att vi har en användare, pol, som vill återställa sina lösen ord till "P0l123fb". Efter normaliseringen skulle det här lösen ordet bli "pol123fb". Del Strängs matchning söker efter att lösen ordet innehåller användarens förnamn "pol". Även om "P0l123fb" inte var särskilt i listan över blockerade lösen ord påträffades en del sträng som matchar "pol" i lösen ordet. Därför skulle det här lösen ordet nekas.

#### <a name="score-calculation"></a>Poäng beräkning

Nästa steg är att identifiera alla instanser av förbjudna lösen ord i användarens normaliserade nya lösen ord. Sedan:

1. Varje blockerat lösen ord som finns i en användares lösen ord får en punkt.
2. Varje återstående unikt tecken får en punkt.
3. Ett lösen ord måste vara minst fem (5) punkter för att det ska godkännas.

I de kommande två exemplen antar vi att Contoso använder lösen ords skydd i Azure AD och har "contoso" i den anpassade listan. Vi antar också att "tomt" finns på den globala listan.

Exempel: en användare ändrar sitt lösen ord till "C0ntos0Blank12"

Efter normalisering blir det här lösen ordet "contosoblank12". Matchnings processen upptäcker att det här lösen ordet innehåller två förbjudna lösen ord: Contoso och tomt. Det här lösen ordet tilldelas sedan en poäng:

[contoso] + [blank] + [1] + [2] = 4 punkter eftersom det här lösen ordet är under fem (5) punkter kommer det att avvisas.

Exempel: en användare ändrar sitt lösen ord till "ContoS0Bl@nkf9!".

Efter normalisering blir det här lösen ordet "contosoblankf9!". Matchnings processen upptäcker att det här lösen ordet innehåller två förbjudna lösen ord: Contoso och tomt. Det här lösen ordet tilldelas sedan en poäng:

[contoso] + [blank] + [f] + [9] + [!] = 5 punkter eftersom det här lösen ordet är minst fem (5) punkter, accepteras det.

   > [!IMPORTANT]
   > Observera att den förbjudna lösen ords algoritmen tillsammans med den globala listan kan och gör ändringar när som helst i Azure baserat på pågående säkerhets analyser och forskning. För den lokala DC-agenttjänsten börjar de uppdaterade algoritmerna gälla först när DC-agentens program vara har installerats om.

## <a name="license-requirements"></a>Licenskrav

|   | Lista med lösen ords skydd i Azure AD med global lista över förbjudna lösen ord | Lista med lösen ords skydd i Azure AD med anpassad lista över blockerade lösen ord|
| --- | --- | --- |
| Endast molnbaserade användare | Azure AD Kostnadsfri | Azure AD Premium P1 eller P2 |
| Användare som synkroniseras från lokala Windows Server-Active Directory | Azure AD Premium P1 eller P2 | Azure AD Premium P1 eller P2 |

> [!NOTE]
> Lokala Windows Server-Active Directory användare som inte är synkroniserade till Azure Active Directory också fördelar med Azure AD Password Protection baserat på befintliga licenser för synkroniserade användare.

Ytterligare licens information, inklusive kostnader, finns på [Azure Active Directory prissättnings webbplats](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>Vad ser användarna

När en användare försöker återställa ett lösen ord till något som ska blockeras visas följande fel meddelande:

Ditt lösen ord innehåller tyvärr ett ord, en fras eller ett mönster som gör ditt lösen ord lätt att gissa. Försök igen med ett annat lösen ord.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera listan anpassat blockerade lösen ord](howto-password-ban-bad.md)
- [Aktivera Azure AD-lösenord för lösen ords skydd lokalt](howto-password-ban-bad-on-premises-deploy.md)
