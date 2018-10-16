---
title: Fem steg för att skydda din infrastruktur för Identitetshantering i Azure Active Directory
description: Det här dokumentet innehåller en lista över viktiga åtgärder som administratörer bör implementera för att hjälpa dem att skydda organisationen med hjälp av Azure AD-funktioner
services: active-directory
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 06/18/2018
ms.author: martincoetzer
ms.openlocfilehash: 94d96cab28f738984b3d05d5eee0754e8c5e75b6
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341590"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>Fem steg för att skydda din infrastruktur för Identitetshantering

Om du läser det här dokumentet, är medveten om betydelsen av säkerhet. Du förmodligen har redan ansvar för att skydda din organisation. Om du behöver övertyga andra om vikten av säkerhet kan du skicka dem att läsa senast [Microsoft Security Intelligence report](https://www.microsoft.com/security/intelligence-report).

Det här dokumentet hjälper dig att få en mer säkerhetsställning med funktionerna i Azure Active Directory med hjälp av en checklista för fem steg till Inokulera organisationen mot cyberattacker.

Den här checklistan hjälper dig att snabbt distribuera kritiska rekommenderade åtgärder för att skydda din organisation omedelbart genom att förklara hur du:

* Stärk dina autentiseringsuppgifter.
* Minska dina angreppsytan.
* Automatisera svar på hot.
* Öka dina kunskaper om granskning och övervakning.
* Möjliggör mer förutsägbara och fullständig slutanvändarens säkerhet med självhjälp.

> [!NOTE]
> Många av rekommendationerna i det här dokumentet gäller endast för program som är konfigurerade för att använda Azure Active Directory som deras identitetsprovider. Konfigurera appar för enkel inloggning säkerställer fördelarna med principer för autentiseringsuppgifter, hotidentifiering, granskning, loggning och andra funktioner lägga till i dessa program. [Enkel inloggning via Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-sso) är grunden - där alla de här rekommendationerna är baserad.

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>Innan du börjar: skydda Privilegierade konton med MFA

Innan du börjar den här checklistan måste du kontrollera att du inte blir kompromissade medan du läser den här checklistan. Först måste du skydda din Privilegierade konton.

Angripare som får kontroll över Privilegierade konton kan göra enorma skador, så det är viktigt att skydda dessa konton först. Aktivera och kräver [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) (MFA) för alla administratörer i din organisation med hjälp av [skydd vid baslinjen](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-baseline-protection). Om du inte har implementerat MFA, gör du det nu! Det är så viktigt.

Allt klart? Nu sätter vi igång på checklistan.

## <a name="step-1---strengthen-your-credentials"></a>Steg 1 – Stärk dina autentiseringsuppgifter 

De flesta enterprise säkerhetsöverträdelser som drabbar kommer med ett konto komprometteras med någon av några olika metoder, till exempel lösenord sprutar, brott repetitionsattacker eller nätfiske. Läs mer om dessa attacker i den här videon (1t 15 miljoner):
> [!VIDEO https://channel9.msdn.com/events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3016/player]

Om användare i identitetssystemet med hjälp av svaga lösenord och stärka dem inte med Multi-Factor authentication, det är inte bara några om eller när du blivit komprometterade – bara ”hur ofta”.

### <a name="make-sure-your-organization-use-strong-authentication"></a>Kontrollera att din organisation använda stark autentisering

Angivna frekvensen för att gissa lösenord phished, blir stulen med skadlig kod eller återanvänds, det är viktigt att bakåt lösenord med någon form av starka autentiseringsuppgifter – Lär dig mer om [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication).

### <a name="turn-off-traditional-complexity-expiration-rules-and-start-banning-commonly-attacked-passwords-instead"></a>Inaktivera traditionella komplexitet, upphör att gälla regler och starta förbjuda vanliga angripna lösenord i stället

Många organisationer använder traditionella komplexiteten (till exempel specialtecken) och regler för lösenord upphör att gälla. Microsofts forskning har visat dessa principer är skadliga, vilket gör att välja lösenord som är lättare att gissa.

Microsofts rekommendationer, konsekvent med [NIST: S vägledning](https://pages.nist.gov/800-63-3/sp800-63b.html), är att implementera de följande tre:

1. Kräv lösenord har minst 8 tecken. Längre är inte nödvändigtvis bättre, eftersom de orsakar användare att välja förutsägbara lösenord, spara lösenord i filer eller Skriv ned dem.
2. Inaktivera upphör att gälla regler som få användarna att enkelt att gissa lösenord som **Summer2018!**.
3. Inaktivera tecken sammansättning krav och hindra användare från att välja ofta angripna lösenord, eftersom de orsakar användare att välja ersättningar för förutsägbar tecken i lösenord.

Du kan använda [PowerShell för att förhindra att lösenord upphör att gälla](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy) på användare om du skapar identiteter i Azure AD direkt. Organisationer som använder den lokala AD med Azure AD Connect att synkronisera identiteter till Azure AD (även kallat en hybriddistribution), bör implementera en lokal [intelligent lösenordsprinciper](https://aka.ms/passwordguidance) med [Grupprincip för domänen inställningar för](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) eller [Windows PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy).

Azure Active Directorys [dynamisk förbjudna lösenord](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords) funktionen använder den aktuella angripare beteendet för att hindra användare från att ange lösenord som enkelt kan gissas. Den här funktionen är alltid aktiverad och organisationer med en hybriddistribution kan dra nytta av den här funktionen genom att aktivera [tillbakaskrivning av lösenord](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-writeback) eller de kan distribuera [Azure AD-lösenordsskydd för Windows Server Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises). Azure AD-lösenordsskydd blockerar användare från att välja vanliga lösenord i allmänhet och anpassade lösenord som du kan konfigurera.

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>Skydda mot läckta autentiseringsuppgifter och lägga till återhämtning mot avbrott

Om din organisation använder en hybrididentitetslösning, bör du aktivera synkronisering av lösenordshash för av följande skäl:

* Den [användare med läckta autentiseringsuppgifter](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events) rapporten i Azure AD-hantering varnar dig om användarnamn och lösenord-par, som har gjorts tillgänglig på ”mörka Internet”. En fantastiska volym av lösenord har läckts via nätfiske och skadlig kod lösenord återanvändning på tredje parters webbplatser som har brutit mot senare. Microsoft hittar många av dessa läcka ut autentiseringsuppgifter och talar om för dig, i den här rapporten om de matchar autentiseringsuppgifter i din organisation –, men bara om du [aktivera lösenordshashsynkronisering](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)!
* Vid ett avbrott för lokala (till exempel i en utpressningstrojan-attack) kommer du att kunna växla till [autentisering i molnet med hjälp av synkronisering av lösenordshash](https://docs.microsoft.com/azure/security/azure-ad-choose-authn). Den här autentiseringsmetoden för säkerhetskopiering kan du fortsätta använda appar som har konfigurerats för autentisering med Azure Active Directory, inklusive Office 365.

Läs mer om hur [lösenordshashsynkronisering](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization) fungerar.

### <a name="implement-ad-fs-extranet-lockout"></a>Implementera AD FS extranätsutelåsning

Organisationer som konfigurerar program för att autentisera direkt till Azure AD dra nytta av [smart Azure AD-kontoutelåsning](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords). Om du använder AD FS i Windows Server 2012 R2 måste implementera AD FS [extranät-kontoutelåsningsskydd](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection). Om du använder AD FS i Windows Server 2016 måste implementera [smart extranätsutelåsning](https://support.microsoft.com/en-us/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). AD FS Smart extranät kontoutelåsning skyddar mot brute force-attacker, vilka mål AD FS samtidigt som användarna från att bli utelåsta i Active Directory.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>Dra nytta av är säker, lättanvänd autentiseringsuppgifter

Med hjälp av [Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification), du kan ersätta lösenord med stark tvåfaktorsautentisering på datorer och mobila enheter. Den här autentiseringen består av en ny typ av autentiseringsuppgifter som är kopplad till en enhet och använder en biometriska eller PIN-kod.

## <a name="step-2---reduce-your-attack-surface"></a>Steg 2 – minska din attackyta

Med styrkan i lösenordet avslöjas kan är minimera risken för angrepp i din organisation viktigt. Eliminera användning av äldre, mindre säkert protokoll, begränsa åtkomstinlägg pekar och passa på att använda mer betydande kontroll av administrativ åtkomst till resurser som kan minska angreppsytan.

### <a name="block-legacy-authentication"></a>Blockera äldre autentisering

Appar som använder sina egna äldre metoder för att autentisera med Azure AD och få åtkomst till företagsdata, utgöra ett annat risken för organisationer. Exempel på appar som använder äldre autentisering är POP3, IMAP4 eller SMTP-klienter. Äldre appar autentisera användarens räkning och förhindra detta advanced security utvärderingar av Azure AD. Alternativa, modern autentisering, minskar säkerhetsriskerna, eftersom den har stöd för multifaktorautentisering och villkorlig åtkomst. Vi rekommenderar följande tre åtgärder:

1. Blockera [äldre autentisering om du använder AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12).
2. Installationsprogrammet [SharePoint Online och Exchange Online till använder modern autentisering](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication).
3. Använd [principer för villkorlig åtkomst blockera äldre](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication).

### <a name="block-invalid-authentication-entry-points"></a>Blockera ogiltig autentisering startpunkter

Använda förutsätt intrång mentalitet kan minska effekten av avslöjade autentiseringsuppgifter när de inträffar. Överväg att giltig användningsfall för varje app i din miljö: vilka grupper, vilka nätverk, vilka enheter och andra element auktoriseras – och sedan blockera resten. Var noga med för att begränsa användningen av [mycket Privilegierade eller tjänsten konton](https://docs.microsoft.com/azure/active-directory/admin-roles-best-practices). Med [Azure AD villkorsstyrd åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal), du kan styra hur behöriga användare åtkomst till sina appar och resurser baserat på specifika villkor som du definierar.

Särskilt uppmärksam på tjänstkonton (konton som används för att utföra uppgifter i obevakat läge). Använder villkorlig åtkomst måste se du till sådana konton kan bara köras mot tjänsten från IP-adress, och tid på dagen, som är lämpliga.

### <a name="implement-azure-ad-privileged-identity-management"></a>Implementera Azure AD Privileged Identity Management

En annan effekten av ”förutsätta intrång” uppstår ofta behovet av att minimera risken ett komprometterat konto kan arbeta med en privilegierad roll. 

[Azure AD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) hjälper till att du minimeras behörighet genom att hjälpa dig:

* Identifiera och hantera användare tilldelas administrativa roller.
* Förstå oanvända eller för stora behörighetsroller bör du ta bort.
* Upprätta regler för att se till att Privilegierade roller är skyddade med multifaktorautentisering.
* Upprätta regler för att se till att Privilegierade roller beviljas endast tillräckligt länge för att utföra Privilegierade uppgiften.

Aktivera Azure AD PIM och visa de användare som tilldelas administrativa roller och ta bort onödiga konton i dessa roller. Återstående Privilegierade användare, flytta dem från permanent till berättigade. Slutligen kan upprätta lämpliga principer för att se till att när de behöver för att få åtkomst till dessa Privilegierade roller, de kan göra det på ett säkert sätt.

Som en del av distributionen privilegierat konto-processen, följer du de [bästa praxis att skapa minst två nödfall konton](https://docs.microsoft.com/azure/active-directory/admin-roles-best-practices) att kontrollera att du har åtkomst till Azure AD om du låser dig.

## <a name="step-3---automate-threat-response"></a>Steg 3 – automatisera svar på hot

Azure Active Directory har många funktioner som automatiskt intercept attacker för att ta bort fördröjning mellan identifiering och åtgärder. Du kan minska kostnaderna och riskerna när du minska tid cyberbrotten använder för att bädda in sig i din miljö. Här följer de konkreta steg som du kan vidta.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Implementera riskprincip med hjälp av Azure AD Identity Protection

Användarrisk anger sannolikheten för en användares identitet har komprometterats och beräknas utifrån den [användarriskhändelser](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) som är associerade med en användares identitet. En princip för användarrisk är en princip för villkorlig åtkomst som utvärderar risknivå till en specifik användare eller grupp. Baserat på låg, kan Medium, hög risknivå, en princip konfigureras för att blockera åtkomst eller kräver en säker lösenordsändring med multifaktorautentisering. Microsofts rekommendation är att kräva en säker lösenordsändring för användare på hög risk.

![Användare som har flaggats för risk](media/azure-ad/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Implementera inloggningsrisk princip med hjälp av Azure AD Identity Protection

Inloggningsrisk är sannolikheten för någon annan än Kontoägare om du försöker logga in och använda identiteten. En [riskprincip för inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) är en princip för villkorlig åtkomst som utvärderar risknivå till en specifik användare eller grupp. Baserat på risknivå (hög/medium/låg) kan kan en princip konfigureras för att blockera åtkomst eller tvinga fram multifaktorautentisering. Kontrollera att tvinga fram multifaktorautentisering på Medium eller senare risk inloggningar.

![Logga in från anonyma IP-adresser](media/azure-ad/azure-ad-sec-steps2.png)

## <a name="step-4---increase-your-awareness"></a>Steg 4 – öka dina kunskaper

Granskning och loggning av säkerhetsrelaterade händelser och relaterade aviseringar är viktiga komponenter i en strategi för att effektivt skydda. Säkerhetsloggar och rapporter ger dig en elektronisk post av misstänkta aktiviteter och hjälper dig att du upptäcka mönster som kan indikera ett försök har gjorts eller lyckad externa penetrationstester av nätverket och interna attacker. Du kan använda granskning för att övervaka användaraktivitet, dokumentet regelefterlevnad, utföra kriminalteknisk analys med mera. Aviseringar tillhandahåller meddelanden av säkerhetshändelser.

### <a name="monitor-azure-ad"></a>Övervaka Azure AD

Microsoft Azure-tjänster och funktioner ger dig konfigurerbara säkerhet, granskning och loggning alternativ för att hjälpa dig att identifiera brister i säkerhetsprinciper och mekanismer och åtgärda dessa luckor om du vill undvika överträdelser. Du kan använda [Azure-loggning och granskning](https://docs.microsoft.com/azure/security/azure-log-audit) och använda [granska aktivitetsrapporter i Azure Active Directory-portalen](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs).

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>Övervaka Azure AD Connect Health i hybridmiljöer

[Övervakning av AD FS med Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs) får du bättre inblick i potentiella problem och synligheten för attacker på din AD FS-infrastruktur. Azure AD Connect Health ger aviseringar med information, Lösningssteg och länkar till relaterade dokument. Användningsanalys för flera prestandamått autentiseringstrafik; övervakning av programprestanda och rapporter.

![Azure AD Connect Health](media/azure-ad/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Övervaka Azure AD Identity Protection-händelser

[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) är ett meddelande, övervakning och rapportering verktyg som du kan använda för att identifiera potentiella problem som påverkar organisationens identiteter. Den identifierar riskhändelser, till exempel läckta autentiseringsuppgifter, omöjlig resa och inloggningar från smittade enheter, anonyma IP-adresser, IP-adresser som är associerade med misstänkt aktivitet och okända platser. Aktivera aviseringar för meddelandet att ta emot e-postadress till användare i farozonen och/eller en veckovis sammanfattad e-post.

![Användare som har flaggats för risk](media/azure-ad/azure-ad-sec-steps3.png)

## <a name="step-5---enable-end-user-self-help"></a>Steg 5: aktivera slutanvändarens självhjälp

Så mycket som möjligt bör du balansera säkerhet med möjliga produktivitet. Samma riktlinjer för närmar sig din resa med tänkesätt att du konfigurerar en grund för säkerhet på lång sikt, kan du ta bort friktionen från din organisation genom att förse dina användare samtidigt som de behåller vaksam. 

### <a name="implement-self-service-password-reset"></a>Implementera lösenordsåterställning via självbetjäning

Azures [lösenordsåterställning via självbetjäning (SSPR)](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started) erbjuder ett enkelt sätt för IT-administratörer att tillåta användare att återställa eller låsa upp sina lösenord eller sina konton utan inblandning av administratören. Systemet innehåller detaljerade rapporter för att spåra när användare använder systemet tillsammans med aviseringar som informerar om missbruk. 

### <a name="implement-self-service-group-management"></a>Implementera grupphantering

Azure AD tillhandahåller möjligheten att hantera åtkomst till resurser med hjälp av säkerhetsgrupper och Office 365-grupper. Dessa grupper kan hanteras av gruppägare i stället för IT-administratörer. Kallas även [självbetjäningsgrupphantering](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management), den här funktionen kan gruppägare som inte har tilldelats en administrativ roll för att skapa och hantera grupper utan att behöva administratörer att hantera sina begäranden.

### <a name="implement-azure-ad-access-reviews"></a>Implementera Azure AD-åtkomstgranskningar

Med [Azure AD-åtkomstgranskningar](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview), du kan hantera gruppmedlemskap, åtkomst till företagsprogram, och Priviligierade rolltilldelningar för att kontrollera att det finns en säkerhetsstandard som inte ger användare åtkomst för utökade perioder med tid när de inte behövs.

## <a name="summary"></a>Sammanfattning

Det finns flera olika aspekter att en säker identitetsinfrastruktur, men checklistan fem steg hjälper dig att snabbt utföra en säkrare och säker identitetsinfrastrukturen:

* Stärk dina autentiseringsuppgifter.
* Minska dina angreppsytan.
* Automatisera svar på hot.
* Öka dina kunskaper om granskning och övervakning.
* Möjliggör mer förutsägbara och fullständig slutanvändarens säkerhet med självhjälp.

Vi uppskattar hur allvarligt du ta Identitetssäkerhet och hoppas det här dokumentet är en bra översikt över en säkrare efterlevnadsstatus för din organisation.

## <a name="next-steps"></a>Nästa steg
Om du behöver hjälp att planera och distribuera rekommendationerna som avser den [Azure AD distribution projektplaner](http://aka.ms/deploymentplans) om du behöver hjälp.
