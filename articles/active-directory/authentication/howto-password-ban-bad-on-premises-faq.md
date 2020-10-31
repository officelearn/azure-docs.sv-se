---
title: Vanliga frågor och svar om lösen ords skydd i Azure AD
description: Läs vanliga frågor och svar om lösen ords skydd i Azure AD i en lokal Active Directory Domain Servicess miljö
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27075af51c8deb886a060c81927f30bb70b5f638
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93077667"
---
# <a name="azure-ad-password-protection-on-premises-frequently-asked-questions"></a>Vanliga frågor och svar om Azure AD Password Protection på plats

Det här avsnittet innehåller svar på många vanliga frågor om lösen ords skydd i Azure AD.

## <a name="general-questions"></a>Allmänna frågor

**F: vilken vägledning ska användarna ges om hur man väljer ett säkert lösen ord?**

Microsofts aktuella vägledning om det här avsnittet finns på följande länk:

[Vägledning för Microsoft-lösenord](https://www.microsoft.com/research/publication/password-guidance)

**F: finns ett lokalt Azure AD-lösenord som stöds i icke-offentliga moln?**

Lokal Azure AD-lösenords skydd stöds i det offentliga molnet och Arlington-molnet. Inget datum har presenter ATS för tillgänglighet i andra moln.

Azure AD-portalen tillåter ändringar av konfigurationen lokalt "lösen ords skydd för Windows Server Active Directory" även i moln som inte stöds. sådana ändringar kommer att bevaras, men annars börjar detta aldrig gälla. Registrering av lokala proxy agenter eller skogar stöds inte i moln som inte stöds, och alla sådana registrerings försök kommer alltid att Miss lyckas.

**F: Hur kan jag använda fördelarna med lösen ords skydd i Azure AD på en delmängd av mina lokala användare?**

Stöds inte. När den har distribuerats och Aktiver ATS kan inte Azure AD-lösenordet diskrimineras – alla användare får samma säkerhets fördelar.

**F: Vad är skillnaden mellan en lösen ords ändring och en lösen ords uppsättning (eller återställning)?**

En lösen ords ändring är när en användare väljer ett nytt lösen ord när de har fått kännedom om det gamla lösen ordet. En lösen ords ändring är till exempel vad som händer när en användare loggar in i Windows och sedan uppmanas att välja ett nytt lösen ord.

En lösen ords uppsättning (kallas ibland återställning av lösen ord) är när en administratör ersätter lösen ordet för ett konto med ett nytt lösen ord, till exempel med hjälp av verktyget Active Directory användare och datorer hantering. Den här åtgärden kräver en hög behörighets nivå (vanligt vis domän administratören) och den person som utför åtgärden har vanligt vis inte kunskap om det gamla lösen ordet. Support scenarier utför ofta lösen ords uppsättningar, till exempel för att hjälpa en användare som har glömt sitt lösen ord. Du kan också se händelser för lösen ords uppsättning när ett helt nytt användar konto skapas för första gången med ett lösen ord.

Principen för lösen ords verifiering beter sig på samma sätt oavsett om en ändring eller uppsättning av lösen ord görs. Azure AD Password Protection DC Agent-tjänsten loggar olika händelser för att meddela dig om ett lösen ords ändring eller en åtgärd har gjorts.  Se [övervakning och loggning av lösen ords skydd i Azure AD](./howto-password-ban-bad-on-premises-monitor.md).

**F: bekräftar Azure AD Password Protection befintliga lösen ord när de har installerats?**

No – Azure AD Password Protection kan endast tillämpa lösen ords principen på lösen ord med klartext under en lösen ords ändring eller ange en åtgärd. När ett lösen ord har accepterats av Active Directory sparas endast autentiserings-protokoll-Specific hash-värden för lösen ordet. Lösen ordet för klartext sparas aldrig, och därför kan inte lösen ords skyddet i Azure AD validera befintliga lösen ord.

Efter den första distributionen av lösen ords skyddet i Azure AD kommer alla användare och konton slutligen att börja använda ett lösen ord som verifierats i Azure AD, eftersom deras befintliga lösen ord upphör att gälla normalt över tid. Om du vill kan du påskynda den här processen genom att använda ett engångs manuellt förfallo datum för användar kontots lösen ord.

Konton som kon figurer ATS med "lösen ordet upphör aldrig att gälla" kommer aldrig att tvingas att ändra sina lösen ord om inte manuell förfallo datum har gjorts.

**F: Varför loggas dubbletter av lösen ords avvisande vid försök att ange ett svagt lösen ord med hjälp av snapin-modulen Active Directory användare och datorer hantering?**

Snapin-modulen Active Directory användare och datorer hantering försöker först ange det nya lösen ordet med hjälp av Kerberos-protokollet. Vid misslyckade försök att använda snapin-modulen för att ange lösen ordet med ett äldre (SAM RPC)-protokoll (de protokoll som används är inte viktiga). Om det nya lösen ordet anses vara svagt av lösen ords skyddet i Azure AD, kommer den här snapin-modulen att leda till att två uppsättningar med avvisnings händelser för lösen ords återställning loggas.

**F: Varför loggas händelser för lösen ords validering i Azure AD med ett tomt användar namn?**

Active Directory har stöd för möjligheten att testa ett lösen ord för att se om det uppfyller kraven på den aktuella lösen ords komplexiteten, till exempel med hjälp av [NetValidatePasswordPolicy](/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) -API: et. När ett lösen ord verifieras på det här sättet innehåller testningen även validering av lösen ords filter-DLL-baserade produkter som Azure AD Password Protection, men användar namnen som skickas till en angiven DLL-fil för lösen ords filter är tomma. I det här scenariot kommer Azure AD Password Protection fortfarande att verifiera lösen ordet med den aktuella lösen ords principen och utfärdar ett händelse logg meddelande för att avbilda resultatet, men händelse logg meddelandet har tomma användar namns fält.

**F: finns det stöd för att installera Azure AD Password Protection sida vid sida med andra lösen ords filter baserade produkter?**

Ja. Stöd för flera registrerade DLL-DLL: er för lösen ords filter är en grundläggande Windows-funktion och inte bara för Azure AD Password Protection. Alla registrerade lösen ords filter-DLL: er måste samtycka innan ett lösen ord accepteras.

**F: Hur kan jag distribuera och konfigurera lösen ords skydd i Azure AD i min Active Directory-miljö utan att använda Azure?**

Stöds inte. Azure AD Password Protection är en Azure-funktion som stöder utökas till en lokal Active Directorys miljö.

**F: Hur kan jag ändra innehållet i principen på Active Directory nivå?**

Stöds inte. Principen kan bara administreras med Azure AD-portalen. Se även föregående fråga.

**F: Varför krävs DFSR för SYSVOL-replikering?**

FRS (föregående teknik till DFSR) har många kända problem och stöds helt och hållet inte i nyare versioner av Windows Server Active Directory. Ingen testning av lösen ords skydd i Azure AD görs på FRS-konfigurerade domäner.

Mer information finns i följande artiklar:

[Fallet vid migrering av SYSVOL-replikering till DFSR](/archive/blogs/askds/the-case-for-migrating-sysvol-to-dfsr)

[Slutet är Nigh för FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

Om din domän inte redan använder DFSR måste du migrera den till att använda DFSR innan du installerar lösen ords skyddet för Azure AD. Mer information finns i följande länk:

[Migreringsguiden för SYSVOL-replikering: FRS till DFS Replication](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

> [!WARNING]
> Azure AD Password Protection DC Agent-programvaran installeras för närvarande på domänkontrollanter i domäner som fortfarande använder FRS för SYSVOL-replikering, men program varan fungerar inte som den ska i den här miljön. Ytterligare negativa sido effekter är enskilda filer som Miss lyckas att replikera, och SYSVOL-återställnings procedurer visas som slutförda men tyst Miss lyckas för att replikera alla filer. Du bör migrera din domän så att DFSR kan använda DFSR så snart som möjligt, både för DFSR: s inbyggda fördelar och även för att avblockera distributionen av lösen ords skydd i Azure AD. Framtida versioner av program varan kommer att inaktive ras automatiskt när de körs i en domän som fortfarande använder FRS.

**F: hur mycket disk utrymme kräver funktionen på domän resursen SYSVOL?**

Den exakta utrymmes användningen varierar eftersom den är beroende av faktorer som t. ex. antal och längd på de förbjudna token i listan över Microsoft Global-blockerade listor och den anpassade listan per klient, plus krypterings kostnader. Innehållet i dessa listor kommer förmodligen att växa i framtiden. Med detta i åtanke är en rimlig förväntan att funktionen måste ha minst fem (5) MEGABYTE utrymme på domänens SYSVOL-resurs.

**F: Varför krävs en omstart för att installera eller uppgradera program varan för DC-agenten?**

Detta krav orsakas av Windows-funktioner i kärnan.

**F: finns det något sätt att konfigurera en DC-agent för att använda en angiven proxyserver?**

Nej. Eftersom proxyservern är tillstånds lös är det inte viktigt vilken speciell proxyserver som används.

**F: är det bra att distribuera Azure AD-proxyn för lösen ords skydd sida vid sida med andra tjänster som Azure AD Connect?**

Ja. Azure AD-proxyn för lösen ords skydd och Azure AD Connect bör aldrig stå i konflikt direkt med varandra.

Tyvärr har en inkompatibilitet identifierats mellan versionen av Microsoft Azure AD Connect agent Updateer-tjänsten som installeras av proxy-programvaran för Azure AD-lösenordet och den version av tjänsten som installeras av [Azure Active Directory-programproxy](../manage-apps/application-proxy.md) -programvaran. Den här inkompatibiliteten kan resultera i att agent uppdaterings tjänsten inte kan kontakta Azure för program uppdateringar. Vi rekommenderar inte att du installerar Azure AD Password Protection proxy och Azure Active Directory-programproxy på samma dator.

**F: i vilken ordning ska DC-agenter och-proxyservrar installeras och registreras?**

Alla beställningar av proxy-agent installation, installation av DC-agent, skogs registrering och proxykonfiguration stöds.

**F: bör jag bekymra dig om prestanda träffen på mina domänkontrollanter från att distribuera den här funktionen?**

Tjänsten Azure AD Password Protection DC agent bör inte påverka domänkontrollantens prestanda avsevärt i en befintlig Active Directory distribution.

För de flesta Active Directory distributioner är lösen ords ändrings åtgärder en liten del av den övergripande arbets belastningen på en viss domänkontrollant. Anta till exempel att en Active Directory domän med 10000 användar konton och en MaxPasswordAge-princip har angetts till 30 dagar. I genomsnitt visas 10 000/30 = ~ 333 lösen ords ändrings åtgärder varje dag, vilket är ett mindre antal åtgärder för även en enda domänkontrollant. Överväg ett potentiellt värsta fall scenario: Antag att dessa ~ 333-lösenords ändringar på en enda DOMÄNKONTROLLANT har utförts under en enda timme. Det här scenariot kan till exempel inträffa när många medarbetare kommer att arbeta på en måndag morgon. Även i detta fall tittar vi fortfarande på ~ 333/60 minuter = sex lösen ords ändringar per minut, vilket återigen inte är en betydande belastning.

Om dina aktuella domänkontrollanter redan körs på prestanda nivå (till exempel överutnyttjade ut med avseende på CPU, disk utrymme, disk-I/O osv.), är det lämpligt att lägga till ytterligare domänkontrollanter eller expandera tillgängligt disk utrymme innan du distribuerar den här funktionen. Se även fråga ovan om användning av SYSVOL-disk utrymme ovan.

**F: Jag vill testa lösen ords skyddet i Azure AD på bara några domänkontrollanter i min domän. Är det möjligt att framtvinga ändringar av användar lösen ord för att använda dessa domänkontrollanter?**

Nej. Windows-klientens operativ system styr vilken domänkontrollant som används när en användare ändrar sitt lösen ord. Domänkontrollanten väljs utifrån faktorer som Active Directory plats-och under näts tilldelningar, miljö bestämd nätverks konfiguration osv. Azure AD Password Protection styr inte dessa faktorer och kan inte påverka vilken domänkontrollant som väljs för att ändra en användares lösen ord.

Ett sätt att delvis uppnå det här målet är att distribuera Azure AD Password Protection på alla domänkontrollanter på en viss Active Directory plats. Den här metoden ger rimlig täckning för de Windows-klienter som är tilldelade till den platsen, och därför även för de användare som loggar in på dessa klienter och ändrar sina lösen ord.

**F: om jag installerar Azure AD Password Protection DC-agenttjänsten på bara den primära domänkontrollanten (PDC), kommer alla andra domänkontrollanter i domänen också att skyddas?**

Nej. När en användares lösen ord ändras på en specifik domänkontrollant som inte är PDC-domänkontrollant skickas aldrig lösen ordet för klartext till den primära domänkontrollanten (den här idén är en vanlig genom gång). När ett nytt lösen ord har accepterats på en angiven DOMÄNKONTROLLANT använder den DOMÄNKONTROLLANTen lösen ordet för att skapa de olika autentiserings-Protocol-specifikt hasharna för lösen ordet och sparar dessa hash-värden i katalogen. Lösen ordet för klartext är inte beständigt. De uppdaterade hasharna replikeras sedan till den primära domänkontrollanten. Användar lösen ord kan i vissa fall ändras direkt på den primära domänkontrollanten, beroende på olika faktorer som nätverkstopologi och Active Directory plats design. (Se föregående fråga.)

I sammanfattning krävs distributionen av tjänsten Azure AD Password Protection DC agent på PDC: n för att uppnå 100% säkerhets täckning över domänen. Att distribuera funktionen på PDC ger inte säkerhet för lösen ords skydd i Azure AD för alla andra domänkontrollanter i domänen.

**F: Varför fungerar inte anpassad Smart utelåsning trots att agenterna har installerats i min lokala Active Directorys miljö?**

Anpassad Smart utelåsning stöds bara i Azure AD. Ändringar av anpassade inställningar för smart utelåsning i Azure AD-portalen har ingen påverkan på den lokala Active Directorys miljön, även om agenterna är installerade.

**F: är ett System Center Operations Manager hanterings paket tillgängligt för Azure AD Password Protection?**

Nej.

**F: Varför nekar Azure AD fortfarande svaga lösen ord, trots att jag har konfigurerat principen som gransknings läge?**

Gransknings läget stöds bara i den lokala Active Directorys miljön. Azure AD är implicit alltid i "framtvinga" läge när det utvärderar lösen ord.

**F: mina användare ser det traditionella Windows fel meddelandet när ett lösen ord avvisas av lösen ords skyddet i Azure AD. Är det möjligt att anpassa det här fel meddelandet så att användarna vet vad som verkligen hände?**

Nej. Fel meddelandet som visas av användare när ett lösen ord avvisas av en domänkontrollant styrs av klient datorn, inte av domänkontrollanten. Det här problemet uppstår om ett lösen ord avvisas av standard Active Directory lösen ords principer eller av en metod för lösen ords filtrering, till exempel Azure AD Password Protection.

## <a name="additional-content"></a>Ytterligare innehåll

Följande länkar är inte en del av dokumentationen för lösen ords skydd i Azure AD, men kan vara en användbar källa till ytterligare information om funktionen.

[Azure AD Password Protection är nu allmänt tillgänglig!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[E-postskydds guide för e-post – del 15: implementera Microsoft Azure AD lösen ords skydds tjänsten (för lokalt!)](http://kmartins.com/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Azure AD Password Protection och smart utelåsning finns nu i den offentliga för hands versionen!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>Support utbildning för Microsoft Premier\Unified är tillgänglig

Om du är intresse rad av att lära dig mer om lösen ords skydd i Azure AD och distribuera det i din miljö kan du dra nytta av en Microsoft proactive-tjänst som är tillgänglig för kunderna med ett Premier-eller Unified Support-kontrakt. Tjänsten heter Azure Active Directory: lösen ords skydd. Kontakta din tekniska konto ansvarig för mer information.

## <a name="next-steps"></a>Nästa steg

Om du har en lokal Azure AD-fråga för lösen ords skydd som inte besvaras här skickar du ett feedback-objekt nedan – tack!

[Distribuera Azure AD-lösenordsskydd](howto-password-ban-bad-on-premises-deploy.md)