---
title: Fem steg för att skydda din identitetsinfrastruktur i Azure Active Directory
description: Det här dokumentet innehåller en lista med viktiga åtgärder som administratörer bör tillämpa för att hjälpa dem att skydda sin organisation med hjälp av funktionerna i Azure AD
services: active-directory
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 06/18/2018
ms.author: martincoetzer
ms.openlocfilehash: b15fff6e868bfac973f9d2a7277f0fac1e29d845
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938521"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>Fem steg för att skydda din identitetsinfrastruktur

Om du läser det här dokumentet du är medveten om betydelsen av säkerhet. Du förmodligen har redan ansvar för att skydda din organisation. Om du behöver övertyga andra vikten av säkerhet kan du skicka dem att läsa senast [Microsoft Security Intelligence-rapporten](https://www.microsoft.com/security/intelligence-report).

Det här dokumentet hjälper dig att få en säkrare hållningsdata med funktionerna i Azure Active Directory med hjälp av en checklista för fem steg för att Inokulera organisationen mot cyber-attacker.

Den här checklistan hjälper dig att snabbt distribuera kritiska rekommenderade åtgärder för att skydda din organisation omedelbart genom att förklara hur du:

* Förbättra dina autentiseringsuppgifter.
* Minska din angreppsytan.
* Automatisera hot svar.
* Öka din medvetenhet om granskning och övervakning.
* Aktivera mer förutsägbara och klar för slutanvändaren med självhjälp.

> [!NOTE]
> Många av rekommendationerna i det här dokumentet gäller endast för program som är konfigurerade för att använda Azure Active Directory som deras identitetsleverantör. Konfigurera appar för enkel inloggning säkerställer fördelarna med autentiseringsuppgifter principer, hotidentifiering, granskning, loggning och andra funktioner Lägg till dessa program. [Enkel inloggning via Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-sso) är grunden - rekommendationerna baseras på där alla.

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>Innan du börjar: skydda Privilegierade konton med MFA

Innan du börjar den här checklistan måste du kontrollera att du inte blir kompromissade medan du läser den här checklistan. Först måste du skydda dina Privilegierade konton.

Angripare som får kontroll över Privilegierade konton kan göra enorm skada, så det är viktigt att skydda dessa konton först. Aktivera och kräver [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) (MFA) för alla administratörer i din organisation genom att använda [baslinjen skydd](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-baseline-protection). Om du inte har implementerat MFA måste du göra det nu! Det är viktigt att.

Allt klart? Nu sätter vi igång på checklistan.

## <a name="step-1---strengthen-your-credentials"></a>Steg 1 – stärka dina autentiseringsuppgifter 

De flesta företag säkerhetsintrång kommer med ett konto komprometteras med något av en handfull metoder som lösenord besprutningsmedel, brott replay eller phishing. Läs mer om dessa attacker i den här videon:
> [!VIDEO https://channel9.msdn.com/events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3016/player]

Om användare i systemet identitet med svaga lösenord och inte förstärkning dem med multifaktorautentisering, det inte finns några om eller när du blir kompromissade – bara ”hur ofta”.

### <a name="make-sure-your-organization-use-strong-authentication"></a>Kontrollera att din organisation använda stark autentisering

Angivna frekvensen för att gissa lösenord phished, blir stulen med skadlig kod eller återanvänds, är det viktigt att säkerhetskopiera lösenordet med någon form av starka autentiseringsuppgifter – Lär dig mer om [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication).

### <a name="turn-off-traditional-complexity-expiration-rules-and-start-banning-commonly-attacked-passwords-instead"></a>Inaktivera traditionella komplexitet, giltighetstid regler och starta förbjuda ofta angripna lösenord i stället

Många företag använder traditionell komplexiteten (till exempel specialtecken) och regler för lösenord upphör att gälla. Microsofts forskning har visat dessa principer är skadliga orsakar att välja lösenord som är enklare att gissa.

Microsofts rekommendationer konsekvent med [NIST vägledning](https://pages.nist.gov/800-63-3/sp800-63b.html), är att implementera följande tre:

1. Kräv lösenord har minst 8 tecken. Längre är inte nödvändigtvis bättre, eftersom de orsakar användare väljer förutsägbar lösenord, spara lösenord i filer eller Skriv ned dem.
2. Inaktivera giltighetstid regler som enheten användare att enkelt att gissa lösenord som **Summer2018!**.
3. Inaktivera tecken sammansättning krav och förhindra användare från att välja ofta angripna lösenord som de orsakar användare att välja ersättningar förutsägbar tecken i lösenord.

Du kan använda [PowerShell för att förhindra att lösenord upphör](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy) på användare om du skapar identiteter i Azure AD direkt. Organisationer som använder lokala AD med Azure AD Connect att synkronisera identiteter till Azure AD (även kallat en hybriddistribution), implementera lokala [intelligent lösenordsprinciper](https://aka.ms/passwordguidance) med [domängrupprincip inställningar för](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) eller [Windows PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy).

Azure Active Directorys [dynamiska förbjudna lösenord](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords) funktionen använder angripare sätt för att hindra användare från att ange lösenord som kan enkelt att gissa sig till. Den här funktionen är alltid på och organisationer med en distribution kan dra nytta av den här funktionen genom att aktivera [tillbakaskrivning av lösenord](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-writeback) eller de kan distribuera [skyddet av Azure AD-lösenord för Windows Server Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises). Azure AD-lösenordsskydd hindrar användare från att välja vanliga lösenord i allmänhet och anpassade lösenord som du kan konfigurera.

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>Skydda mot läckta autentiseringsuppgifter och Lägg till återhämtning mot avbrott

Om organisationen använder en hybrididentitetslösning, bör du aktivera synkronisering av lösenords-hash för av följande skäl:

* Den [användare med läckta autentiseringsuppgifter](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events) rapporten i Azure AD-hantering varnar dig för användarnamn och lösenord-par, som har exponerats på ”mörkt webben”. En otrolig volym av lösenord är känd via nätfiske och skadlig kod lösenord återanvändning på tredje parters webbplatser som har brutit mot senare. Microsoft hittar många av dessa läcka ut autentiseringsuppgifter och meddelar dig, i den här rapporten om de matchar autentiseringsuppgifter i din organisation –, men bara om du [Aktivera synkronisering av lösenords-hash-](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)!
* Vid ett avbrott för lokalt (till exempel i en är en utpressningstrojan som attack) kommer du att kunna växla till [autentisering i molnet med hjälp av Lösenordssynkronisering för hash-](https://docs.microsoft.com/azure/security/azure-ad-choose-authn). Den här autentiseringsmetoden för säkerhetskopiering kan du fortsätta använda apparna som konfigurerats för autentisering med Azure Active Directory, inklusive Office 365.

Läs mer om hur [synkronisering av lösenords-hash](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization) fungerar.

### <a name="implement-ad-fs-extranet-lockout"></a>Implementera AD FS extranät-låsning

Organisationer som konfigurerar autentisera direkt till Azure AD-program utnyttja [smart Azure AD-låsning](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords). Om du använder AD FS kan implementera AD FS [extranät-låsning](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-lockout-protection). Extranät-låsning skyddar mot brute force-attacker, vilka mål AD FS medan hindrar användare från att låsas i Active Directory.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>Dra nytta av är säkra, enklare att använda autentiseringsuppgifter

Med hjälp av [Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification), kan du ersätta ett lösenord med starka tvåfaktorsautentisering på datorer och mobila enheter. Den här autentiseringen består av en ny typ av autentiseringsuppgifter för användare som är kopplad till en enhet och använder en biometriska eller PIN-kod.

## <a name="step-2---reduce-your-attack-surface"></a>Steg 2 – minska risken för angrepp

Genomträngning att lösenordet avslöjas är, minimera risken för angrepp i din organisation kritiska. Eliminera användning av äldre, mindre säkra protokoll, begränsa åtkomstposten pekar och utöva större kontroll över administrativ åtkomst till resurser kan minska angreppsytan.

### <a name="block-legacy-authentication"></a>Blockera äldre autentisering

Appar som använder sina egna äldre metoder för att autentisera med Azure AD och få åtkomst till företagsdata, utgör en annan risk för organisationer. Exempel på appar som använder äldre autentisering är POP3-, IMAP4 eller SMTP-klienter. Äldre autentisering appar autentisera för användarens räkning och förhindra detta avancerad säkerhet utvärderingar Azure AD. Alternativa, modern autentisering, minskar säkerhetsriskerna, eftersom den har stöd för multifaktorautentisering och villkorlig åtkomst. Vi rekommenderar följande tre åtgärder:

1. Blockera [äldre autentisering om du använder AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12).
2. Installationsprogrammet [SharePoint Online och Exchange Online till använder modern autentisering](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication).
3. Använd [principer för villkorlig åtkomst blockera äldre autentisering](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication).

### <a name="block-invalid-authentication-entry-points"></a>Blockera ogiltig autentisering startpunkter

Med anta intrång mentality kan minska effekten av avslöjade autentiseringsuppgifter innan de inträffar. Överväg att giltiga användningsområden för varje app i din miljö: vilka grupper, vilket nätverk, vilka enheter och andra element tillåts – och sedan blockera resten. Var noga med för att begränsa användningen av [mycket Privilegierade eller tjänsten konton](https://docs.microsoft.com/azure/active-directory/admin-roles-best-practices). Med [villkorlig åtkomst i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal), du kan styra hur behöriga användare åtkomst till sina appar och resurser baserat på specifika villkor som du definierar.

Titta närmare tjänstkonton (konton som används för att utföra aktiviteter i obevakat läge). Använder villkorlig åtkomst kan se du till sådana konton kan bara köras mot tjänsten från IP-Adressen, och tid på dagen som är lämpliga.

### <a name="implement-azure-ad-privileged-identity-management"></a>Implementera Azure AD Privileged Identity Management

En annan effekten av ”förutsätter intrång” är behovet av att minimera sannolikheten komprometterat konto kan arbeta med en privilegierad roll. 

[Azure AD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) hjälper du minimerat behörighet genom att hjälpa dig:

* Identifiera och hantera användare som tilldelats administrativa roller.
* Förstå oanvända eller överdriven privilegium roller bör du ta bort.
* Skapa regler för att se till att Privilegierade roller skyddas av multifaktorautentisering.
* Skapa regler för att kontrollera Privilegierade roller beviljas endast tillräckligt länge för att utföra Privilegierade uppgiften.

Aktivera Azure AD PIM och visa de användare som tilldelas administrativa roller och ta bort onödiga konton i dessa roller. För återstående Privilegierade användare, flyttar du dem från permanent till berättigade. Slutligen upprätta lämpliga principer för att kontrollera när de behöver för att få åtkomst till de Privilegierade rollerna, de kan göra det på ett säkert sätt.

Som del av distributionen av processen privilegierat konto följer den [bästa praxis att skapa minst två nödfall konton](https://docs.microsoft.com/azure/active-directory/admin-roles-best-practices) och kontrollera att du har åtkomst till Azure AD om du låser dig.

## <a name="step-3---automate-threat-response"></a>Steg 3 – automatisera hot svar

Azure Active Directory har många funktioner som automatiskt intercept attacker, för att ta bort fördröjningen mellan identifiering och svar. Du kan minska kostnaderna och risker, när du minska tiden kriminella använder för att bädda in sig i din miljö. Här följer de konkreta steg som du kan vidta.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Implementera säkerhetsprinciper för användaren risken med Azure AD Identity Protection

Användaren risk anger sannolikheten för en användares identitet har komprometterats och beräknas baserat på de [användaren riskhändelser](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) som är kopplade till en användarens identitet. En användarprincip risk är en princip för villkorlig åtkomst som utvärderar risknivå till en specifik användare eller grupp. Baserat på låg, konfigureras medelhög och hög risk-nivå, en princip för att blockera åtkomst eller en säker lösenordet måste ändras med hjälp av multifaktorautentisering. Microsofts rekommendation är att kräva en säker lösenordsändring för användare på hög risk.

![Användare som har flaggats för risk](media/azure-ad/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Implementera inloggning riskprincipen med hjälp av Azure AD Identity Protection

Logga in risken är sannolikheten någon än kontoägaren försöker logga in och använda identitet. En [inloggning riskprincipen](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) är en princip för villkorlig åtkomst som utvärderar risknivå till en specifik användare eller grupp. Baserat på risknivå (hög/medium/låg), konfigureras en princip för att blockera åtkomst eller framtvinga multifaktorautentisering. Kontrollera att du framtvinga multifaktorautentisering på mediet eller högre risk inloggningar.

![Logga in från anonyma IP-adresser](media/azure-ad/azure-ad-sec-steps2.png)

## <a name="step-4---increase-your-awareness"></a>Steg 4 – öka din medvetenhet

Granskning och loggning av säkerhetsrelaterade händelser och relaterade aviseringar är viktiga komponenter av en strategi för effektivt skydd. Säkerhetsloggar och rapporterna ger dig en elektronisk post av misstänkta aktiviteter och hjälp dig att upptäcka mönster som kan indikera försök eller lyckad externa intrång i nätverket och interna attacker. Du kan använda granskning för att övervaka användaraktivitet, dokumentet regelefterlevnad, utföra kriminaltekniska analyser och mycket mer. Meddelanden om säkerhetshändelser med aviseringar.

### <a name="monitor-azure-ad"></a>Övervaka Azure AD

Microsoft Azure-tjänster och funktioner ger dig konfigurerbara säkerhet granskning och loggning alternativ som hjälper dig att identifiera brister i säkerhetsprinciper och mekanismer och åtgärda dessa luckor om du vill undvika överträdelser. Du kan använda [Azure loggning och granskning](https://docs.microsoft.com/azure/security/azure-log-audit) och använda [granska aktivitetsrapporter i Azure Active Directory-portalen](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs).

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>Övervaka Azure AD Connect Health i hybridmiljöer

[Övervakning av AD FS med Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs) ger dig bättre inblick i potentiella problem och synlighet för attacker i din AD FS-infrastruktur. Azure AD Connect Health levererar aviseringar med information, Lösningssteg och länkar till relaterad dokumentation. Användningsanalys för flera mätvärden som är relaterade till autentiseringstrafik. övervakning av programprestanda och rapporter.

![Azure AD Connect Health](media/azure-ad/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Övervaka händelser i Azure AD Identity Protection

[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) är ett meddelande, övervakning och rapportering verktyg som du kan använda för att identifiera potentiella problem som påverkar din organisations identiteter. Upptäcks av riskhändelser, till exempel läckta autentiseringsuppgifter, omöjlig resa och inloggningar från infekterade enheter, anonyma IP-adresser, IP-adresser som är associerade med misstänkt aktivitet och okända platser. Aktivera avisering aviseringar att ta emot e-postadress till användare i riskzonen och/eller en vecka sammanfattad e-post.

![Användare som har flaggats för risk](media/azure-ad/azure-ad-sec-steps3.png)

## <a name="step-5---enable-end-user-self-help"></a>Steg 5 – aktivera slutanvändarens självhjälp

Så mycket som möjligt ska du väga säkerhet med produktivitet. Samma riktlinjer för närmar sig din resa med tänkesätt att du håller på att en grund för säkerhet på lång sikt du ta bort friktion från din organisation genom att ge användarna möjlighet när återstående vaksam. 

### <a name="implement-self-service-password-reset"></a>Implementera lösenordsåterställning via självbetjäning

Azures [Självbetjäning för lösenordsåterställning (SSPR)](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started) erbjuder ett enkelt sätt för IT-administratörer att tillåta användare att återställa eller låsa upp sitt lösenord eller konton utan inblandning av administratören. Systemet innehåller detaljerade rapporter för att spåra när användare använder systemet tillsammans med aviseringar som informerar om missbruk. 

### <a name="implement-self-service-group-management"></a>Implementera grupphantering via självbetjäning

Azure AD ger möjlighet att hantera åtkomst till resurser med hjälp av säkerhetsgrupper och Office 365-grupper. Dessa grupper kan hanteras av gruppen ägare i stället för IT-administratörer. Kallas [grupphantering via självbetjäning](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management), den här funktionen kan gruppen ägare som inte har tilldelats en administrativ roll för att skapa och hantera grupper utan administratörer att hantera sina begäranden.

### <a name="implement-azure-ad-access-reviews"></a>Implementera Azure AD åtkomst granskningar

Med [åtkomst till Azure AD granskar](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview), kan du hantera gruppmedlemskap, åtkomst till företagsprogram, och Privilegierade rolltilldelningar för att kontrollera att det finns en säkerhetsstandard som inte ger användare åtkomst för utökad perioder med tid när de inte behövs.

## <a name="summary"></a>Sammanfattning

Det finns flera olika aspekter att en säker identitetsinfrastruktur, men checklistan fem steg hjälper dig att snabbt utföra en säkrare och säkra identitetsinfrastrukturen:

* Förbättra dina autentiseringsuppgifter.
* Minska din angreppsytan.
* Automatisera hot svar.
* Öka din medvetenhet om granskning och övervakning.
* Aktivera mer förutsägbara och klar för slutanvändaren med självhjälp.

Vi uppskattar hur allvarligt du vidta identitet säkerhet och hoppas det här dokumentet är en användbar översikt över en säkrare hållningsdata för din organisation.

## <a name="next-steps"></a>Nästa steg
Om du behöver hjälp att planera och distribuera rekommendationerna finns i [Azure AD projektet distribution planer](http://aka.ms/deploymentplans) för hjälp.
