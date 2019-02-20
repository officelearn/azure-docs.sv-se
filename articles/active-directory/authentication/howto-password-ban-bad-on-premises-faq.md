---
title: Den lokala Azure AD lösenordsskydd vanliga frågor och svar
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
ms.openlocfilehash: 8b29e4f6e6e877a3d03ded9edd9e282e0960e177
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2019
ms.locfileid: "56414643"
---
# <a name="preview-azure-ad-password-protection-on-premises---frequently-asked-questions"></a>Förhandsversion: Azure AD-lösenordsskydd lokalt – vanliga frågor och svar

|     |
| --- |
| Azure AD-lösenordsskydd är en funktion i offentliga förhandsversionen av Azure Active Directory. Mer information om förhandsversioner finns [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="general-questions"></a>Allmänna frågor

**F: När uppnår allmän tillgänglighet (GA) i Azure AD-lösenordsskydd?**

Allmän tillgänglighet planeras Q1 CY2019 (före slutet av mars 2019). Tack för alla som har skickat feedback om funktionen på datum - vi uppskattar den!

**F: Vilken vägledning bör användare ges om hur du väljer ett säkert lösenord?**

Microsofts aktuella vägledning för det här avsnittet finns på följande länk:

[Lösenordet för Microsoft-vägledning](https://www.microsoft.com/en-us/research/publication/password-guidance)

**F: Är den lokala Azure AD-lösenord Protection stöds i icke-offentlig moln?**

Nej, lokala Azure AD-lösenord skydd stöds endast i det offentliga molnet. Inget datum har tillkännagivits för tillgänglighet på icke-offentliga moln.

**F: Hur kan jag använda Azure AD-lösenordsskydd fördelar till en delmängd av Mina lokala användare?**

Stöds ej. När distribuerats och aktiverats, Azure AD-lösenordsskydd skilja inte – alla användare får lika säkerhetsfördelarna.

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

Exakt utrymmesanvändningen kan inte anges korrekt eftersom det beror på faktorer som antalet och längden förbjudna lösenord token i listan Microsoft global förbjudna lösenord och anpassat per klient förbjuden lösenordslista plus omkostnader för kryptering. Innehållet i de här listorna är sannolikt att växa i framtiden. Med det i åtanke är en rimlig aktuella tumregel att funktionen måste minst fem (5) MB ledigt utrymme på domänen sysvol-resursen.

**F: Varför är en omstart krävs för att installera eller uppgradera DC-agentprogramvaran?**

Det här kravet orsakas av core Windows beteende.

**F: Finns det något sätt att konfigurera en DC-agenten för att använda en särskild proxyserver?**

Nej. Observera att eftersom proxyservern är tillståndslösa, det inte är viktigt vilken specifik proxy-server används.

**F: Är det bra att distribuera den Azure AD-lösenord Protection Proxy service sida-vid-sidan på en dator med andra tjänster som Azure AD Connect?**

Ja. Azure AD-lösenord Protection Proxy-tjänst och Azure AD Connect bör aldrig står i konflikt med varandra.

**F: Bör jag vara bekymrade över prestandan nåddes på domänkontrollanter från att distribuera den här funktionen?**

Azure AD-lösenord DC Protection Agent-tjänsten är avsedd att vara så effektivt som möjligt och bör inte påverka domänkontrollantens prestanda i en befintlig Active Directory-distribution som redan tillräckligt resurstilldelas.

Det är bra att tänka på att ändra åtgärder för de flesta lösenord för Active Directory-distributioner är en liten del av den övergripande arbetsbelastningen på någon viss domänkontrollant. Till exempel anta att en Active Directory-domän med 10000 användarkonton och en princip för MaxPasswordAge 30 dagar. I genomsnitt är den här domänen visas 10000/30 = ~ 333 lösenordsändringar varje dag, vilket är ett mindre antal åtgärder för även en enda domänkontrollant. Push-överföra exemplet till en potentiell sämsta användningsfall anta att dessa ~-333 lösenordsändringar på en Domänkontrollant har utförts under en enda timme (till exempel det här scenariot kan inträffa när ett stort antal alla anställda kommer att fungera på en måndag morgon); även i detta fall använder vi letar fortfarande efter ~333/60 minuter = sex lösenordsändringar per minut, vilket igen inte är en avsevärd belastning.

Med den sägs om domänkontrollanterna aktuella redan körs på begränsade prestanda-nivåer (till exempel överutnyttjade ut med avseende på processor, diskutrymme, disk-i/o osv), skulle det vara en bra idé att distribuera ytterligare domänkontrollanter och/eller expandera ledigt utrymme innan du distribuerar Azure AD-lösenordsskydd. Du kan också se fråga ovan om sysvol diskutrymmesanvändning ovan.

**F: Jag vill testa Azure AD-lösenord skydd på bara några få domänkontrollanter i min domän. Är det möjligt att tvinga lösenordsändringar för användare att använda dessa specifika domänkontrollanter?**

Nej. När en användare ändrar sina lösenord, väljs den domänkontrollant som används av Windows-klientoperativsystem baserat på faktorer som Active Directory-platser och undernät tilldelningar, miljöspecifika nätverkskonfiguration, osv. Azure AD-lösenordsskydd som inte påverkar eller kontrollera de här faktorerna och därför kan påverka vilken domänkontrollant som har valts för en viss användare lösenordsändring.

Med den SA, är en metod som ligger nära simulera detta mål att distribuera Azure AD-lösenord skydd på alla domänkontrollanter i en viss Active Directory-plats. Den här metoden ger rimligen allt vanligare täckning för Windows-klienter som är kopplade till den platsen, och därför även för användarna som loggar in på dessa klienter och ändra sina lösenord.

**F: Om jag installerar Azure AD-lösenord Protection DC-agenttjänsten på bara den primär domänkontrollant (PDC), kommer alla andra domänkontrollanter i domänen också skyddas?**

Nej. När en användares lösenord ändras för en viss icke-PDC-domänkontrollant, skickas klartext lösenordet aldrig till den primära domänkontrollanten (det här är en gemensam felkonfigurerat perception). I stället när ett nytt lösenord accepteras på en viss Domänkontrollant, den DC använder lösenordet för att skapa de olika autentisering protokollspecifika hash-värden för lösenordet och sedan fortsätter dessa hashvärden i katalogen. De uppdaterade hashvärden replikeras sedan till den primära domänkontrollanten - men Azure AD-lösenordsskydd ingår inte i den här processen. Lösenord kan i vissa fall ändras direkt på den primära domänkontrollanten igen beroende på olika faktorer, till exempel nätverkstopologi och design för Active Directory-plats. (Se föregående fråga).

Sammanfattningsvis: distribution av Azure AD lösenord DC skyddsagenttjänsten på den primära domänkontrollanten krävs för att nå 100% säkerhetstäckning av funktionen, men som påverkar i sig inte tillhandahåller Azure AD-lösenordsskydd säkerhetsfördelarna för alla andra domänkontrollanter i domänen.

**F: Finns ett hanteringspaket för System Center Operations Manager (SCOM) för Azure AD-lösenordsskydd?**

Nej.

## <a name="additional-content"></a>Ytterligare innehåll

Följande länkar är inte en del av grundläggande dokumentation för Azure AD-lösenord Protection men kan vara en användbar källa för mer information om funktionen.

[E-guiden för nätfiske skydd – del 15: Implementera Microsoft Azure AD-lösenord Protection Service (för lokala för!)](https://blogs.technet.microsoft.com/cloudready/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Azure AD-lösenordsskydd och Smart kontoutelåsning finns nu i offentlig förhandsversion!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="next-steps"></a>Nästa steg

Om du har en lokal Azure AD-lösenordsskydd fråga som inte besvaras här kan du skicka in ett feedbackobjekt nedan – tack!

[Distribuera Azure AD-lösenordsskydd](howto-password-ban-bad-on-premises-deploy.md)
