---
title: Lokala Azure AD lösenordsskydd vanliga frågor och svar – Azure Active Directory
description: Den lokala Azure AD lösenordsskydd vanliga frågor och svar
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50ab4d3c9134e94b60c4dd3eeb5603f450b036c3
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58794044"
---
# <a name="azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Azure AD-lösenordsskydd lokalt – vanliga frågor och svar

## <a name="general-questions"></a>Allmänna frågor

**F: Vilken vägledning bör användare ges om hur du väljer ett säkert lösenord?**

Microsofts aktuella vägledning för det här avsnittet finns på följande länk:

[Lösenordet för Microsoft-vägledning](https://www.microsoft.com/en-us/research/publication/password-guidance)

**F: Är den lokala Azure AD-lösenord Protection stöds i icke-offentlig moln?**

Nej, lokala Azure AD-lösenord skydd stöds endast i det offentliga molnet. Inget datum har tillkännagivits för tillgänglighet på icke-offentliga moln.

**F: Hur kan jag använda Azure AD-lösenordsskydd fördelar till en delmängd av Mina lokala användare?**

Stöds ej. När distribuerats och aktiverats, Azure AD-lösenordsskydd skilja inte – alla användare får lika säkerhetsfördelarna.

**F: Vad är skillnaden mellan en lösenordsändring och en lösenordsändring (eller återställning)?**

Ändra lösenord är när en användare väljer ett nytt lösenord efter bevisar att de har kunskap om det gamla lösenordet. Det här är till exempel vad som händer när en användare loggar in på Windows och sedan uppmanas att välja ett nytt lösenord.

Ett lösenord (kallas ibland en lösenordsåterställning) är när en administratör ersätter lösenordet till ett konto med ett nytt lösenord, till exempel med hjälp av verktyget för Active Directory-användare och datorer. Den här åtgärden kräver en hög behörighetsnivå (vanligtvis domänadministratören) och den person som utför åtgärden vanligtvis har inte kunskap om det gamla lösenordet. Helpdesk-scenarier ofta göra detta, exempelvis när hjälpa en användare som har glömt sitt lösenord. Du kan även se lösenord in händelser när ett helt nytt konto skapas för första gången med ett lösenord.

Princip för verifiering av lösenord fungerar på samma sätt oavsett om en ändring av lösenord eller en uppsättning görs. Azure AD-lösenord Protection DC-agenttjänsten loggar olika händelser för att informera dig om en lösenordsändring eller set-åtgärd utfördes.  Se [Azure AD lösenordsskydd övervakning och loggning](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-password-ban-bad-on-premises-monitor).

**F: Finns det stöd för att installera Azure AD-lösenord Protection sida vid sida med andra filter-lösenordsbaserade produkter?**

Ja. Stöd för flera filter DLL-filer för registrerade lösenord är en core Windows-funktion och inte specifikt för Azure AD-lösenordsskydd. Alla registrerade lösenord filter DLL: er måste godkänna innan ett lösenord har godkänts.

**F: Hur kan jag distribuera och konfigurera Azure AD-lösenordsskydd i min Active Directory-miljö utan att använda Azure?**

Stöds ej. Azure AD-lösenordsskydd är en funktion i Azure som har stöd för klasstillägget till en lokal Active Directory-miljö.

**F: Hur kan jag ändra innehållet i principen på nivån Active Directory?**

Stöds ej. Principen kan bara fjärradministreras med hjälp av Azure AD-hanteringsportalen. Du kan också se föregående fråga.

**F: Varför krävs DFSR för sysvol-replikering?**

FRS (föregångare till DFSR) har många kända problem och är helt och hållet stöds inte i senare versioner av Windows Server Active Directory. Noll testning av Azure AD-lösenordsskydd görs på FRS-konfigurerade domäner.

Mer information finns i följande artiklar:

[Migrera sysvol-replikering till DFSR fallet](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[Slutet är Nigh för FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

**F: Hur mycket diskutrymme kräver funktionen på domänen sysvol-resursen?**

Exakt utrymmesanvändningen varierar eftersom det beror på faktorer som antalet och längden förbjudna token i listan Microsoft global förbjudna och anpassad lista i per klient plus kryptering omkostnader. Innehållet i de här listorna är sannolikt att växa i framtiden. Med det i åtanke är rimligen kan antas att funktionen måste minst fem (5) MB ledigt utrymme på domänen sysvol-resursen.

**F: Varför är en omstart krävs för att installera eller uppgradera DC-agentprogramvaran?**

Det här kravet orsakas av core Windows beteende.

**F: Finns det något sätt att konfigurera en DC-agenten för att använda en särskild proxyserver?**

Nej. Eftersom proxyservern är tillståndslösa, är det inte viktigt vilken specifik proxy-server används.

**F: Är det bra att distribuera tjänsten för Azure AD-lösenord Protection Proxy sida vid sida med andra tjänster som Azure AD Connect?**

Ja. Azure AD-lösenord Protection Proxy-tjänst och Azure AD Connect bör aldrig står i konflikt med varandra.

**F: I vilken ordning bör DC-agenter och proxyservrar installeras och registreras?**

Några sorteringen av Proxy agentinstallation, DC-agentinstallationen, skog registrering och Proxyregistrering stöds.

**F: Bör jag vara bekymrade över prestandan nåddes på domänkontrollanter från att distribuera den här funktionen?**

Azure AD-lösenord Protection DC-agenttjänsten bör inte påverka domänkontrollantens prestanda i en befintlig felfri Active Directory-distribution.

För de flesta Active Directory-distributioner lösenord är ändringen en liten del av den övergripande arbetsbelastningen på någon viss domänkontrollant. Till exempel anta att en Active Directory-domän med 10000 användarkonton och en princip för MaxPasswordAge 30 dagar. I genomsnitt är den här domänen visas 10000/30 = ~ 333 lösenordsändringar varje dag, vilket är ett mindre antal åtgärder för även en enda domänkontrollant. Överväg ett potentiella värsta scenario: Anta att dessa ~-333 lösenordsändringar på en Domänkontrollant har utförts under en timme. Det här scenariot kan exempelvis uppstå när många anställda alla kommer att fungera på en måndag morgon. Även i den fallet vi letar fortfarande efter ~333/60 minuter = sex lösenordsändringar per minut, vilket igen inte är en avsevärd belastning.

Men om din aktuella domänkontrollanter redan körs på begränsade prestanda-nivåer (t.ex, överutnyttjade ut med avseende på processor, diskutrymme, disk-i/o, etc.), är det lämpligt att lägga till ytterligare domänkontrollanter eller expandera ledigt diskutrymme innan distribuera den här funktionen. Du kan också se fråga ovan om sysvol diskutrymmesanvändning ovan.

**F: Jag vill testa Azure AD-lösenord skydd på bara några få domänkontrollanter i min domän. Är det möjligt att tvinga lösenordsändringar för användare att använda dessa specifika domänkontrollanter?**

Nej. Windows-klientoperativsystem styr vilken domänkontrollant som ska användas när en användare ändrar sina lösenord. Domänkontrollanten är valt baserat på faktorer som Active Directory-platser och undernät tilldelningar, miljöspecifika nätverkskonfiguration, osv. Azure AD-lösenordsskydd styr inte de här faktorerna och det går inte att påverka vilken domänkontrollant har valts för att ändra en användares lösenord.

Ett sätt att delvis nå detta mål är att distribuera Azure AD-lösenord Protection på alla domänkontrollanter i en viss Active Directory-plats. Den här metoden ger rimliga täckning för Windows-klienter som är tilldelade till platsen, och därför även för användarna som loggar in på dessa klienter och ändra sina lösenord.

**F: Om jag installerar Azure AD-lösenord Protection DC-agenttjänsten på bara den primär domänkontrollant (PDC), kommer alla andra domänkontrollanter i domänen också skyddas?**

Nej. När en användares lösenord ändras för en viss icke-PDC-domänkontrollant, skickas klartext lösenordet aldrig till den primära domänkontrollanten (det här är en gemensam felkonfigurerat perception). När ett nytt lösenord accepteras på en viss Domänkontrollant, den DC använder lösenordet för att skapa de olika autentisering protokollspecifika hash-värden för lösenordet och sedan fortsätter dessa hashvärden i katalogen. Okrypterade lösenord är inte beständiga. Uppdaterade hashvärdena replikeras sedan till den primära domänkontrollanten. Lösenord kan i vissa fall ändras direkt på den primära domänkontrollanten igen beroende på olika faktorer, till exempel nätverkstopologi och design för Active Directory-plats. (Se föregående fråga).

Sammanfattningsvis krävs distribution av Azure AD-lösenord DC Protection Agent-tjänsten på den primära domänkontrollanten för att nå 100% säkerhetstäckning av funktionen i hela domänen. Distributionen av funktionen på den primära domänkontrollanten bara ger inte Azure AD-lösenordsskydd säkerhetsfördelarna för alla andra domänkontrollanter i domänen.

**F: Finns ett hanteringspaket för System Center Operations Manager för Azure AD-lösenordsskydd?**

Nej.

**F: Varför Azure fortfarande avvisar svaga lösenord trots att jag har konfigurerat principen för att vara i granskningsläge?**

Granskningsläge stöds endast i den lokala Active Directory-miljö. Azure är implicit alltid i ”tvinga” läge när det utvärderar lösenord.

## <a name="additional-content"></a>Ytterligare innehåll

Följande länkar är inte en del av grundläggande dokumentation för Azure AD-lösenord Protection men kan vara en användbar källa för mer information om funktionen.

[E-guiden för nätfiske skydd – del 15: Implementera Microsoft Azure AD-lösenord Protection Service (för lokala för!)](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Azure AD-lösenordsskydd och Smart kontoutelåsning finns nu i offentlig förhandsversion!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>Microsoft Premier\Unified supportutbildning som är tillgängliga

Om du är intresserad av att lära dig mer om Azure AD-lösenordsskydd och distribuerar det i din miljö kan dra du nytta av en Microsoft proaktiv tjänsten är tillgänglig för kunder med ett Premier eller Unified support-kontrakt. Tjänsten kallas Azure Active Directory: Lösenordsskydd. Kontakta Teknikkontoansvarig om du vill ha mer information.

## <a name="next-steps"></a>Nästa steg

Om du har en lokal Azure AD-lösenordsskydd fråga som inte besvaras här kan du skicka in ett feedbackobjekt nedan – tack!

[Distribuera Azure AD-lösenordsskydd](howto-password-ban-bad-on-premises-deploy.md)
