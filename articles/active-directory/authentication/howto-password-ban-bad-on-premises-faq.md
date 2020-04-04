---
title: Vanliga frågor och svar om skydd av Azure AD-lösenord
description: Granska vanliga frågor och svar om Azure AD-lösenordsskydd i en lokal Active Directory Domain Services-miljö
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16537ba72eb9f11abd8de47256150818c121a140
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652647"
---
# <a name="azure-ad-password-protection-on-premises-frequently-asked-questions"></a>Azure AD Lösenordsskydd lokalt vanliga frågor och svar

Det här avsnittet innehåller svar på många vanliga frågor om Azure AD Password Protection.

## <a name="general-questions"></a>Allmänna frågor

**F: Vilken vägledning bör användarna ges om hur man väljer ett säkert lösenord?**

Microsofts aktuella vägledning om detta ämne finns på följande länk:

[Microsoft lösenordsvägledning](https://www.microsoft.com/research/publication/password-guidance)

**F: Stöds lokalt Azure AD-lösenordsskydd i icke-offentliga moln?**

Nej - lokalt Azure AD-lösenordsskydd stöds endast i det offentliga molnet. Inget datum har meddelats för icke-offentlig molntillgänglighet.

Azure AD-portalen tillåter ändring av den lokala specifika konfigurationen "Lösenordsskydd för Windows Server Active Directory" även i icke-offentliga moln. sådana förändringar kommer att kvarstå, men annars kommer aldrig att träda i kraft. Registrering av lokala proxyagenter eller skogar stöds inte när icke-offentliga molnautentiseringsuppgifter används, och sådana registreringsförsök misslyckas alltid.

**F: Hur kan jag använda Azure AD Password Protection-förmåner på en delmängd av mina lokala användare?**

Stöds inte. När Azure AD-lösenordsskydd har distribuerats och aktiverats diskrimineras de inte – alla användare får lika säkerhetsfördelar.

**F: Vad är skillnaden mellan en lösenordsändring och en lösenordsuppsättning (eller återställning)?**

En lösenordsändring är när en användare väljer ett nytt lösenord efter att ha bevisat att de har kunskap om det gamla lösenordet. En lösenordsändring är till exempel vad som händer när en användare loggar in i Windows och sedan uppmanas att välja ett nytt lösenord.

En lösenordsuppsättning (kallas ibland för återställning av lösenord) är när en administratör ersätter lösenordet på ett konto med ett nytt lösenord, till exempel med hjälp av hanteringsverktyget för Användare och datorer i Active Directory. Den här åtgärden kräver en hög behörighetsnivå (vanligtvis Domänadministratör) och den person som utför åtgärden har vanligtvis inte kunskap om det gamla lösenordet. Help-desk scenarier utför ofta lösenordsuppsättningar, till exempel när hjälpa en användare som har glömt sitt lösenord. Du kommer också att se lösenordsuppsättningshändelser när ett helt nytt användarkonto skapas för första gången med ett lösenord.

Principen för lösenordsverifiering fungerar på samma sätt oavsett om en lösenordsändring eller uppsättning görs. Azure AD Password Protection DC Agent-tjänsten loggar olika händelser för att informera dig om en lösenordsändring eller uppsättningsåtgärd har utförts.  Se [Övervakning och loggning av Azure AD-lösenordsskydd](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-monitor).

**F: Varför loggas dubbletthändelser för lösenord när du försöker ange ett svagt lösenord med snapin-modulen Hantering av Active Directory och datorer?**

Snapin-modulen För hantering av Active Directory - och datorer försöker först ange det nya lösenordet med Kerberos-protokollet. Vid fel gör snapin-modulen ett andra försök att ange lösenordet med ett äldre (SAM RPC) protokoll (de specifika protokoll som används är inte viktiga). Om det nya lösenordet anses vara svagt av Azure AD Password Protection, resulterar det här snapin-funktionen i två uppsättningar av avslag på lösenordsåterställning som loggas.

**F: Varför loggas lösenordsverifieringshändelser för Lösenordsverifiering av Azure AD-lösenord med ett tomt användarnamn?**

Active Directory stöder möjligheten att testa ett lösenord för att se om den klarar domänens aktuella krav på lösenordskomplexitet, till exempel med hjälp av [NetValidatePasswordPolicy-api:et.](https://docs.microsoft.com/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) När ett lösenord valideras på det här sättet innehåller testningen även validering av lösenordsfilter-dll-baserade produkter som Azure AD Password Protection - men användarnamnen som skickas till ett visst lösenordsfilter dll kommer att vara tomma. I det här fallet azure AD lösenordsskydd kommer fortfarande att validera lösenordet med hjälp av den aktuella in-effect lösenordsprincipen och kommer att utfärda ett händelseloggmeddelande för att fånga resultatet, men händelseloggmeddelandet kommer att ha tomma användarnamnsfält.

**F: Stöds det att installera Azure AD Password Protection sida vid sida med andra lösenordsfilterbaserade produkter?**

Ja. Stöd för dll-korts för flera registrerade lösenordsfilter är en grundläggande Windows-funktion och inte specifik för Azure AD Password Protection. Alla registrerade lösenordsfilter dlls måste godkänna innan ett lösenord accepteras.

**F: Hur kan jag distribuera och konfigurera Azure AD-lösenordsskydd i active directory-miljön utan att använda Azure?**

Stöds inte. Azure AD Password Protection är en Azure-funktion som stöder utökas till en lokal Active Directory-miljö.

**F: Hur ändrar jag innehållet i principen på Active Directory-nivå?**

Stöds inte. Principen kan endast administreras med Azure AD-portalen. Se även föregående fråga.

**F: Varför krävs DFSR för sysvol-replikering?**

FRS (den föregående tekniken till DFSR) har många kända problem och stöds helt inte i nyare versioner av Active Directory i Windows Server. Noll testning av Azure AD Password Protection görs på FRS-konfigurerade domäner.

Mer information finns i följande artiklar:

[Fallet för migrering av sysvol-replikering till DFSR](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[Slutet är nära för FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

Om din domän inte redan använder DFSR måste du migrera den för att använda DFSR innan du installerar Azure AD Password Protection. Mer information finns på följande länk:

[GUIDEN MIGRERING AV SYSVOL-replikering: FRS till DFS Replication](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

> [!WARNING]
> Azure AD Password Protection DC Agent-programvaran installeras för närvarande på domänkontrollanter i domäner som fortfarande använder FRS för sysvol-replikering, men programvaran fungerar INTE korrekt i den här miljön. Ytterligare negativa biverkningar inkluderar enskilda filer som inte replikeras, och sysvol återställningsprocedurer som verkar lyckas men tyst misslyckas med att replikera alla filer. Du bör migrera domänen för att använda DFSR så snart som möjligt, både för DFSR:s inneboende fördelar och för att häva blockeringen av Azure AD Password Protection. Framtida versioner av programvaran inaktiveras automatiskt när den körs i en domän som fortfarande använder FRS.

**F: Hur mycket diskutrymme kräver funktionen på domänen sysvol-resursen?**

Den exakta utrymmesanvändningen varierar eftersom den beror på faktorer som antalet och längden på de förbjudna token i Microsofts globala förbjudna lista och anpassad lista per klient, plus krypteringskostnader. Innehållet i dessa listor kommer sannolikt att växa i framtiden. Med detta i åtanke är en rimlig förväntan att funktionen kommer att behöva minst fem (5) megabyte utrymme på domänen sysvol aktie.

**F: Varför krävs en omstart för att installera eller uppgradera DC-agentprogramvaran?**

Det här kravet orsakas av windows-kärnbeteende.

**F: Finns det något sätt att konfigurera en DC-agent för att använda en viss proxyserver?**

Nej. Eftersom proxyservern är tillståndslös är det inte viktigt vilken specifik proxyserver som används.

**F: Är det okej att distribuera Azure AD Password Protection Proxy-tjänsten sida vid sida med andra tjänster som Azure AD Connect?**

Ja. Azure AD Password Protection Proxy-tjänsten och Azure AD Connect bör aldrig stå i direkt konflikt med varandra.

Tyvärr har en inkompatibilitet hittats mellan den version av Microsoft Azure AD Connect Agent Updater-tjänsten som installeras av Azure AD Password Protection Proxy-programvaran och den version av tjänsten som installeras av [Azure Active Directory Application Proxy-programvaran.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) Den här inkompatibiliteten kan leda till att agentuppdateringstjänsten inte kan kontakta Azure för programuppdateringar. Det rekommenderas inte att installera Azure AD Password Protection Proxy och Azure Active Directory Application Proxy på samma dator.

**F: I vilken ordning ska DC-agenter och proxyservrar installeras och registreras?**

Alla orderingar av Proxy agent installation, DC agent installation, skogsregistrering och Proxy registrering stöds.

**F: Bör jag vara orolig för prestandaträffen på mina domänkontrollanter från att distribuera den här funktionen?**

Dc-agenttjänsten för Azure AD-lösenordsskydd bör inte påverka domänkontrollantens prestanda i en befintlig felfri Active Directory-distribution.

För de flesta åtgärder för ändring av lösenord i Active Directory är lösenordsändringsåtgärder en liten del av den totala arbetsbelastningen på en viss domänkontrollant. Som ett exempel kan du tänka dig en Active Directory-domän med 10000 användarkonton och en MaxPasswordAge-princip inställd på 30 dagar. I genomsnitt kommer den här domänen att se 10000/30=~333 lösenordsändringsåtgärder varje dag, vilket är ett mindre antal åtgärder för även en enda domänkontrollant. Tänk på ett potentiellt värsta scenario: anta att dessa ~ 333 lösenordsändringar på en enda DOMÄNKONTROLL GJORDES under en enda timme. Det här scenariot kan till exempel uppstå när många anställda alla kommer till jobbet på en måndag morgon. Även i så fall är vi fortfarande tittar på ~ 333/60 minuter = sex lösenordsändringar per minut, vilket återigen inte är en betydande belastning.

Men om dina nuvarande domänkontrollanter redan körs på prestandabegränsade nivåer (till exempel maxade ut med avseende på CPU, diskutrymme, disk-I/O, etc.), är det lämpligt att lägga till ytterligare domänkontrollanter eller utöka tillgängligt diskutrymme innan du distribuerar den här funktionen. Se även frågan ovan om sysvol diskutrymme användning ovan.

**F: Jag vill testa Azure AD Password Protection på bara några domäner i domänen. Är det möjligt att tvinga ändringar av användarlösenordet att använda dessa specifika DC?**

Nej. Windows-klientoperativsystemet styr vilken domänkontrollant som används när en användare ändrar sitt lösenord. Domänkontrollanten väljs baserat på faktorer som Active Directory-plats- och undernätstilldelningar, miljöspecifik nätverkskonfiguration osv. Azure AD Password Protection styr inte dessa faktorer och kan inte påverka vilken domänkontrollant som har valts för att ändra en användares lösenord.

Ett sätt att delvis nå det här målet är att distribuera Azure AD Password Protection på alla domänkontrollanter på en viss Active Directory-plats. Den här metoden ger rimlig täckning för De Windows-klienter som har tilldelats den webbplatsen, och därför även för de användare som loggar in på dessa klienter och ändrar sina lösenord.

**F: Om jag installerar DC-agenten För skydd av Azure AD-lösenordsskydd på bara PDC (Primary Domain Controller), skyddas även alla andra domänkontrollanter i domänen?**

Nej. När en användares lösenord ändras på en viss icke-PDC-domänkontrollant skickas aldrig lösenordet för klartext till PDC (den här idén är en vanlig feluppfattning). När ett nytt lösenord har accepterats på en viss domänkontrollant använder den klienten lösenordet för att skapa de olika autentiseringsprotokollspecifika hasharna för lösenordet och sedan kvarstår dessa hashar i katalogen. Lösenordet för klartexten sparas inte. De uppdaterade hashar replikeras sedan till PDC. Användarlösenord kan i vissa fall ändras direkt på PDC, återigen beroende på olika faktorer som nätverkstopologi och Active Directory-platsdesign. (Se föregående fråga.)

Sammanfattningsvis krävs distribution av Azure AD Password Protection DC Agent-tjänsten på PDC för att nå 100 % säkerhetstäckning för funktionen i domänen. Distribuera funktionen på PDC endast ger inte Azure AD lösenordsskydd säkerhetsfördelar för andra domänen.

**F: Varför fungerar inte anpassad smart utelåsning ens efter att agenterna har installerats i min lokala Active Directory-miljö?**

Anpassad smart utelåsning stöds endast i Azure AD. Ändringar av anpassade inställningar för smart utelåsning i Azure AD-portalen har ingen effekt på den lokala Active Directory-miljön, även med agenterna installerade.

**F: Är ett systemcenter operations manager-hanteringspaket tillgängligt för Azure AD Password Protection?**

Nej.

**F: Varför avvisar Azure AD fortfarande svaga lösenord trots att jag har konfigurerat principen så att den är i granskningsläge?**

Granskningsläge stöds bara i den lokala Active Directory-miljön. Azure AD är implicit alltid i "framtvinga"-läge när det utvärderar lösenord.

**F: Mina användare ser det traditionella Windows-felmeddelandet när ett lösenord avvisas av Azure AD Password Protection. Är det möjligt att anpassa det här felmeddelandet så att användarna vet vad som egentligen hände?**

Nej. Felmeddelandet som användarna ser när ett lösenord avvisas av en domänkontrollant styrs av klientdatorn, inte av domänkontrollanten. Det här problemet beror på om ett lösenord avvisas av standardprinciperna för Active Directory-lösenord eller av en lösenordsfilterbaserad lösning som Azure AD Password Protection.

## <a name="additional-content"></a>Ytterligare innehåll

Följande länkar är inte en del av azure AD-lösenordsskydd, men kan vara en användbar källa till ytterligare information om funktionen.

[Azure AD lösenordsskydd är nu allmänt tillgänglig!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[Guiden för nätfiske i e-post – Del 15: Implementera Microsoft Azure AD Password Protection Service (även för lokalt!)](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Azure AD lösenordsskydd och smart lockout är nu i offentlig förhandsversion!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>Supportutbildning för Microsoft Premier\Unified är tillgänglig

Om du är intresserad av att lära dig mer om Azure AD-lösenordsskydd och distribuera det i din miljö kan du dra nytta av en proaktiv tjänst från Microsoft som är tillgänglig för de kunder som har ett Premier- eller Unified-supportavtal. Tjänsten kallas Azure Active Directory: Password Protection. Kontakta din tekniska kontohanterare för mer information.

## <a name="next-steps"></a>Nästa steg

Om du har en lokal Azure AD-lösenordsskyddsfråga som inte besvaras här skickar du ett feedbackobjekt nedan – tack!

[Distribuera Azure AD-lösenordsskydd](howto-password-ban-bad-on-premises-deploy.md)
