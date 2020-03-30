---
title: Skydda din Azure AD-identitetsinfrastruktur
titleSuffix: Azure Active Directory
description: Det här dokumentet beskriver en lista över viktiga åtgärder som administratörer bör implementera för att skydda sin organisation med Hjälp av Azure AD-funktioner
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: security
ms.subservice: security-fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: martinco
ms.openlocfilehash: e0db8edfdfa380697a1d8d7e262a7a84da2fb7d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77565544"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>Fem steg för att skydda din identitetsinfrastruktur

Om du läser det här dokumentet är du medveten om vikten av säkerhet. Du har förmodligen redan ansvaret för att säkra din organisation. Om du behöver övertyga andra om vikten av säkerhet, skicka dem för att läsa den senaste [Microsoft Security Intelligence rapport](https://go.microsoft.com/fwlink/p/?linkid=2073747).

Det här dokumentet hjälper dig att få en säkrare hållning med hjälp av funktionerna i Azure Active Directory genom att använda en checklista i fem steg för att vaccinera din organisation mot cyberattacker.

Den här checklistan hjälper dig att snabbt distribuera kritiska rekommenderade åtgärder för att skydda din organisation omedelbart genom att förklara hur du:

* Stärka dina meriter.
* Minska angreppsytan.
* Automatisera hotrespons.
* Använd molnintelligens.
* Aktivera slutanvändarens självbetjäning.

Se till att du håller reda på vilka funktioner och steg som är klara när du läser den här checklistan.

> [!NOTE]
> Många av rekommendationerna i det här dokumentet gäller endast program som är konfigurerade för att använda Azure Active Directory som identitetsprovider. Om du konfigurerar appar för enkel inloggning säkerställer fördelarna med principer för autentiseringsuppgifter, identifiering av hot, granskning, loggning och andra funktioner som lägger till dessa program. [Enkel inloggning via Azure Active Directory](../../active-directory/manage-apps/configure-single-sign-on-non-gallery-applications.md) är grunden - som alla dessa rekommendationer baseras på.

Rekommendationerna i det här dokumentet är anpassade till [Identity Secure Score](../../active-directory/fundamentals/identity-secure-score.md), en automatiserad bedömning av din Azure AD-klients identitetssäkerhetskonfiguration. Organisationer kan använda sidan Identity Secure Score i Azure AD-portalen för att hitta luckor i sin nuvarande säkerhetskonfiguration för att säkerställa att de följer microsofts aktuella [metodtips](identity-management-best-practices.md) för säkerhet. Genom att implementera varje rekommendation på sidan Säker poäng ökar du dina poäng och gör att du kan spåra dina framsteg, plus att du kan jämföra implementeringen med andra organisationer av liknande storlek eller din bransch.

![Identitetssäker poäng](./media/steps-secure-identity/azure-ad-sec-steps0.png)

> [!NOTE]
> Många av de funktioner som beskrivs här kräver en Azure AD Premium-prenumeration, medan vissa är gratis. Läs vår [Azure Active Directory-prissättning](https://azure.microsoft.com/pricing/details/active-directory/) och [Azure AD-distributionschecklistan](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2) för mer information.

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>Innan du börjar: Skydda privilegierade konton med MFA

Innan du börjar den här checklistan, se till att du inte blir komprometterade medan du läser den här checklistan. Du måste först skydda dina privilegierade konton.

Angripare som får kontroll över privilegierade konton kan göra enorma skador, så det är viktigt att skydda dessa konton först. Aktivera och kräva [MFA (Azure Multi-Factor Authentication)](../../active-directory/authentication/multi-factor-authentication.md) för alla administratörer i organisationen som använder [Azure AD-säkerhetsstandarder](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md) eller [villkorlig åtkomst](../../active-directory/conditional-access/plan-conditional-access.md). Om du inte har genomfört MFA, gör det nu! Det är så viktigt.

Är allt klart? Låt oss komma igång med checklistan.

## <a name="step-1---strengthen-your-credentials"></a>Steg 1 - Stärka dina autentiseringsuppgifter

De flesta säkerhetsöverträdelser i företaget har sitt ursprung i ett konto som har komprometterats med en handfull metoder, till exempel lösenordsspray, uppspelning av brott eller nätfiske. Läs mer om dessa attacker i den här videon (45 min):
> [!VIDEO https://www.youtube.com/embed/uy0j1_t5Hd4]

### <a name="make-sure-your-organization-uses-strong-authentication"></a>Se till att din organisation använder stark autentisering

Med tanke på hur ofta lösenord som gissas, phished, stulna med skadlig kod, eller återanvändas, det är viktigt att backa lösenordet med någon form av stark autentiseringsuppgifter - läs mer om [Azure Multi-Factor Autentisering](../../active-directory/authentication/multi-factor-authentication.md).

Om du enkelt vill aktivera den grundläggande nivån för identitetssäkerhet kan du använda aktiveringen med ett klick med [Azure AD-säkerhetsstandarder](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md). Säkerhetsstandarder tillämpar Azure MFA för alla användare i en klient och blockerar inloggningar från äldre protokoll som omfattar hela klienten.

### <a name="start-banning-commonly-attacked-passwords-and-turn-off-traditional-complexity-and-expiration-rules"></a>Börja förbjuda ofta attackerade lösenord och inaktivera traditionella komplexitets- och utgångsdatumsregler.

Många organisationer använder den traditionella komplexiteten (som kräver specialtecken, siffror, versaler och gemener) och lösenordsförfalloregler. [Microsofts forskning](https://aka.ms/passwordguidance) har visat dessa principer orsaka användare att välja lösenord som är lättare att gissa.

Azure [AD:s dynamiska förbjudna lösenordsfunktion](../../active-directory/authentication/concept-password-ban-bad.md) använder aktuellt angripares beteende för att förhindra att användare anger lösenord som lätt kan gissas. Den här funktionen är alltid aktiverad när användare skapas i molnet, men är nu också tillgänglig för hybridorganisationer när de distribuerar [Azure AD-lösenordsskydd för Windows Server Active Directory](../../active-directory/authentication/concept-password-ban-bad-on-premises.md). Azure AD lösenordsskydd blockerar användare från att välja dessa vanliga lösenord och kan utökas för att blockera lösenord som innehåller anpassade sökord som du anger. Du kan till exempel hindra användarna från att välja lösenord som innehåller företagets produktnamn eller ett lokalt sportteam.

Microsoft rekommenderar att du antar följande moderna lösenordspolicy baserad på [NIST-vägledning:](https://pages.nist.gov/800-63-3/sp800-63b.html)

1. Kräv lösenord har minst 8 tecken. Längre är inte nödvändigtvis bättre, eftersom de orsakar användare att välja förutsägbara lösenord, spara lösenord i filer eller skriva ner dem.
2. Inaktivera utgångsreglerna, som driver användare att enkelt gissa lösenord som **Spring2019!**
3. Inaktivera krav på teckensammansättning och hindra användare från att välja vanliga lösenord, eftersom de får användare att välja förutsägbara teckenbyten i lösenord.

Du kan använda [PowerShell för att förhindra att lösenord upphör att gälla](../../active-directory/authentication/concept-sspr-policy.md) för användare om du skapar identiteter i Azure AD direkt. Hybridorganisationer bör implementera dessa principer med [domängrupprincipinställningar](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) eller [Windows PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy).

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>Skydda mot läckta autentiseringsuppgifter och lägg till motståndskraft mot avbrott

Om din organisation använder en hybrididentitetslösning med direktautentisering eller federation bör du aktivera synkronisering av lösenordshã¤nde av följande två skäl:

* Rapporten [Användare med läckta autentiseringsuppgifter](../../active-directory/reports-monitoring/concept-risk-events.md) i Azure AD-hanteringen varnar dig om användarnamn och lösenordspar, som har exponerats på den "mörka webben". En otrolig mängd lösenord läcker via nätfiske, skadlig kod och återanvändning av lösenord på webbplatser från tredje part som senare bryts. Microsoft hittar många av dessa läckta autentiseringsuppgifter och kommer i den här rapporten att berätta om de matchar autentiseringsuppgifterna i organisationen – men bara om du [aktiverar synkronisering av lösenord hash](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)!
* I händelse av ett lokalt avbrott (till exempel i en ransomware-attack) kan du växla över till att använda [molnautentisering med hjälp av lösenordshagesynkronisering](choose-ad-authn.md). Med den här autentiseringsmetoden för säkerhetskopiering kan du fortsätta att komma åt appar som konfigurerats för autentisering med Azure Active Directory, inklusive Office 365. I det här fallet behöver IT-personal inte tillgripa personliga e-postkonton för att dela data förrän det lokala avbrottet har lösts.

Läs mer om hur [synkronisering av lösenord hash](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md) fungerar.

> [!NOTE]
> Om du aktiverar synkronisering av lösenord hash och använder Azure AD-domäntjänster, kommer Kerberos (AES 256) hashar och eventuellt NTLM (RC4, inga salta) hashar också krypteras och synkroniseras till Azure AD.

### <a name="implement-ad-fs-extranet-smart-lockout"></a>Implementera AD FS extranät smart lockout

Organisationer som konfigurerar program för att autentisera direkt till Azure AD drar nytta av [smart utelåsning](../../active-directory/authentication/concept-sspr-howitworks.md)i Azure AD . Om du använder AD FS i Windows Server 2012R2 implementerar du AD FS [extranätsutelåsningsskydd](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection). Om du använder AD FS på Windows Server 2016 implementerar du [smart utelåsning av extranät](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). AD FS Smart Extranet-utelåsning skyddar mot brute force-attacker, som riktar sig mot AD FS samtidigt som användare hindras från att låsas ute i Active Directory.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>Dra nytta av egensäkra, enklare att använda autentiseringsuppgifter

Med [Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)kan du ersätta lösenord med stark tvåfaktorsautentisering på datorer och mobila enheter. Den här autentiseringen består av en ny typ av användarautentiseringsuppgifter som är säkert kopplade till en enhet och använder en biometrisk eller PIN-kod.

## <a name="step-2---reduce-your-attack-surface"></a>Steg 2 - Minska din attackyta

Med tanke på den genomgripande lösenord kompromiss, minimera attacken ytan i din organisation är avgörande. Genom att eliminera användningen av äldre, mindre säkra protokoll, begränsa åtkomståtkomstpunkter och utöva större kontroll över administrativ åtkomst till resurser kan du minska angreppsytan.

### <a name="block-legacy-authentication"></a>Blockera äldre autentisering

Appar som använder sina egna äldre metoder för att autentisera med Azure AD och komma åt företagsdata utgör en annan risk för organisationer. Exempel på appar som använder äldre autentisering är POP3-, IMAP4- eller SMTP-klienter. Äldre autentiseringsappar autentiserar för användarens räkning och hindrar Azure AD från att göra avancerade säkerhetsutvärderingar. Den alternativa, moderna autentiseringen minskar säkerhetsrisken eftersom den stöder multifaktorautentisering och villkorlig åtkomst. Vi rekommenderar följande tre åtgärder:

1. Blockera [äldre autentisering om du använder AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12).
2. Konfigurera [SharePoint Online och Exchange Online för att använda modern autentisering](../../active-directory/conditional-access/conditional-access-for-exo-and-spo.md).
3. Om du har Azure AD Premium använder du principer för [villkorlig åtkomst](../../active-directory/conditional-access/overview.md) för att blockera äldre autentisering, annars använd Azure AD [Security Defaults](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md).

### <a name="block-invalid-authentication-entry-points"></a>Blockera ogiltiga autentiseringsinmatningspunkter

Med hjälp av anta brott mentalitet, bör du minska effekten av komprometterade användaruppgifter när de inträffar. För varje app i din miljö bör du tänka på giltiga användningsfall: vilka grupper, vilka nätverk, vilka enheter och andra element som är auktoriserade – blockera resten. Med [Azure AD Conditional Access](../../active-directory/conditional-access/overview.md)kan du styra hur behöriga användare kommer åt sina appar och resurser baserat på specifika villkor som du definierar.

### <a name="restrict-user-consent-operations"></a>Begränsa åtgärder för användarsamtycke

Det är viktigt att förstå de olika [Azure AD-programmedgivande upplevelser,](https://docs.microsoft.com/azure/active-directory/develop/application-consent-experience) [vilka typer av behörigheter och samtycke](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent), och deras konsekvenser för din organisations säkerhetsposition. Som standard kan alla användare i Azure AD bevilja program som utnyttjar Microsofts identitetsplattform för att komma åt organisationens data. Samtidigt som användare kan samtycka själva tillåter användare att enkelt skaffa användbara program som integreras med Microsoft 365, Azure och andra tjänster, kan det utgöra en risk om den inte används och övervakas noggrant.

Microsoft rekommenderar att [du inaktiverar framtida åtgärder](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-removing-user-access#i-want-to-disable-all-future-user-consent-operations-to-any-application) för användargodkännande för att minska din yta och minska den här risken. Om slutanvändarens samtycke inaktiveras kommer tidigare medgivandebidrag fortfarande att respekteras, men alla framtida samtyckesåtgärder måste utföras av en administratör. Administratörsmedgivande kan begäras av användare via ett integrerat [arbetsflöde för administratörsföryndebegäran](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow) eller genom dina egna supportprocesser. Innan du inaktiverar slutanvändarens medgivande använder du våra [rekommendationer](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-consent-requests) för att planera den här ändringen i din organisation. För program som du vill tillåta alla användare att komma åt, överväg att [bevilja samtycke för alla användare](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent), se till att användare som ännu inte har samtyckt individuellt kommer att kunna komma åt appen. Om du inte vill att dessa program ska vara tillgängliga för alla användare i alla scenarier använder du [programtilldelning](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups) och [villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) för att begränsa användarnas åtkomst till appar.

Se till att användarna kan begära administratörsgodkännande för nya program för att minska användarfriktionen, minimera supportvolymen och hindra användare från att registrera sig för program med icke-Azure AD-autentiseringsuppgifter. När du har reglerat dina samtyckesåtgärder bör administratörer granska appar och medgivande behörigheter regelbundet.


### <a name="implement-azure-ad-privileged-identity-management"></a>Implementera Azure AD-privilegierad identitetshantering

En annan effekt av "anta brott" är behovet av att minimera sannolikheten för ett komprometterat konto kan fungera med en privilegierad roll.

[Azure AD Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) hjälper dig att minimera kontobehörigheter genom att hjälpa dig:

* Identifiera och hantera användare som tilldelats administrativa roller.
* Förstå oanvända eller överdrivna behörighetsroller som du bör ta bort.
* Upprätta regler för att se till att privilegierade roller skyddas av multifaktorautentisering.
* Upprätta regler för att se till att privilegierade roller endast beviljas tillräckligt länge för att utföra den privilegierade uppgiften.

Aktivera Azure AD PIM och visa sedan de användare som har tilldelats administrativa roller och ta bort onödiga konton i dessa roller. För återstående privilegierade användare flyttar du dem från permanenta till berättigade. Slutligen upprätta lämpliga principer för att se till att när de behöver få tillgång till dessa privilegierade roller, kan de göra det på ett säkert sätt, med nödvändig ändringskontroll.

Som en del av distributionen av din privilegierade kontoprocess följer du [den bästa metoden för att skapa minst två nödkonton](../../active-directory/users-groups-roles/directory-admin-roles-secure.md) för att se till att du fortfarande har åtkomst till Azure AD om du låser dig ute.

## <a name="step-3---automate-threat-response"></a>Steg 3 - Automatisera hothantering

Azure Active Directory har många funktioner som automatiskt fångar upp attacker, för att ta bort svarstiden mellan identifiering och svar. Du kan minska kostnaderna och riskerna, när du minskar den tid brottslingar använder för att bädda in sig i din miljö. Här är de konkreta åtgärder du kan ta.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Implementera säkerhetsprinciper för användarrisker med Azure AD Identity Protection

Användarrisk anger sannolikheten för att en användares identitet har komprometterats och beräknas baserat på [de användarriskidentifieringar](../../active-directory/identity-protection/overview.md) som är associerade med en användares identitet. En användarriskprincip är en princip för villkorlig åtkomst som utvärderar risknivån till en viss användare eller grupp. Baserat på låg, medelhög, hög risknivå kan en princip konfigureras för att blockera åtkomst eller kräva en säker lösenordsändring med multifaktorautentisering. Microsofts rekommendation är att kräva en säker lösenordsändring för användare med hög risk.

![Användare som har flaggats för risk](./media/steps-secure-identity/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Implementera inloggningsriskprincip med Azure AD Identity Protection

Inloggningsrisk är sannolikheten för att någon annan än kontoägaren försöker logga in med identiteten. En [inloggningsriskprincip](../../active-directory/identity-protection/overview.md) är en princip för villkorlig åtkomst som utvärderar risknivån till en viss användare eller grupp. Baserat på risknivån (hög/medel/låg) kan en princip konfigureras för att blockera åtkomst eller tvinga fram multifaktorautentisering. Se till att du tvingar multifaktorautentisering på medium eller högre risk inloggningar.

![Logga in från anonyma IPs](./media/steps-secure-identity/azure-ad-sec-steps2.png)

## <a name="step-4---utilize-cloud-intelligence"></a>Steg 4 - Utnyttja molnintelligens

Granskning och loggning av säkerhetsrelaterade händelser och relaterade registreringar är viktiga komponenter i en effektiv skyddsstrategi. Säkerhetsloggar och rapporter ger dig en elektronisk post över misstänkta aktiviteter och hjälper dig att identifiera mönster som kan tyda på försök eller lyckad extern penetration av nätverket och interna attacker. Du kan använda granskning för att övervaka användaraktivitet, dokumentefterlevnad, göra kriminalteknisk analys med mera. Aviseringar ger meddelanden om säkerhetshändelser.

### <a name="monitor-azure-ad"></a>Övervaka Azure AD

Microsoft Azure-tjänster och -funktioner ger dig konfigurerbara säkerhetsgransknings- och loggningsalternativ som hjälper dig att identifiera luckor i dina säkerhetsprinciper och säkerhetsmekanismer och åtgärda dessa luckor för att förhindra överträdelser. Du kan använda [Azure Logging and Audit och](log-audit.md) använda [granskningsaktivitetsrapporter i Azure Active Directory-portalen](../../active-directory/reports-monitoring/concept-audit-logs.md).

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>Övervaka Azure AD Connect-hälsotillstånd i hybridmiljöer

[Genom att övervaka AD FS med Azure AD Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md) får du större insikt i potentiella problem och synlighet för attacker på din AD FS-infrastruktur. Azure AD Connect Health levererar aviseringar med information, stegvisa upplösning och länkar till relaterad dokumentation. användningsanalys för flera mått relaterade till autentiseringstrafik. övervakning och rapporter om prestanda.

![Azure AD Connect Health](./media/steps-secure-identity/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Övervaka Azure AD-identitetsskyddshändelser

[Azure AD Identity Protection](../../active-directory/identity-protection/overview.md) är ett meddelande, övervaknings- och rapporteringsverktyg som du kan använda för att identifiera potentiella sårbarheter som påverkar organisationens identiteter. Den identifierar riskidentifieringar, till exempel läckta autentiseringsuppgifter, omöjliga resor och inloggningar från infekterade enheter, anonyma IP-adresser, IP-adresser som är associerade med den misstänkta aktiviteten och okända platser. Aktivera aviseringar om aviseringar för att ta emot e-post för användare i riskzonen och/eller ett veckosvart e-postmeddelande.

Azure AD Identity Protection innehåller två viktiga rapporter som du bör övervaka dagligen:
1. Riskfyllda inloggningsrapporter kommer att visa användarloggningsaktiviteter som du bör undersöka, den legitima ägaren kanske inte har utfört inloggningen.
2. Riskfyllda användarrapporter kommer att visa användarkonton som kan ha komprometterats, till exempel läckt autentiseringsuppgifter som upptäcktes eller användaren loggade in från olika platser som orsakar en omöjlig resehändelse.

![Användare som har flaggats för risk](./media/steps-secure-identity/azure-ad-sec-steps3.png)

### <a name="audit-apps-and-consented-permissions"></a>Granska appar och behörigheter med godkänt godkännande

Användare kan luras att navigera till en komprometterad webbplats eller appar som får tillgång till deras profilinformation och användardata, till exempel deras e-post. En skadlig aktör kan använda de medgivande behörigheter som den fått för att kryptera deras postlådeinnehåll och kräva en lösensumma för att återfå postlådedata. [Administratörer bör granska och granska](https://docs.microsoft.com/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) de behörigheter som ges av användare eller inaktivera användarnas möjlighet att ge sitt samtycke som standard.

Förutom att granska de behörigheter som ges av användare kan du [hitta riskfyllda eller oönskade OAuth-program](https://docs.microsoft.com/cloud-app-security/investigate-risky-oauth) i premiummiljöer.

## <a name="step-5---enable-end-user-self-service"></a>Steg 5 - Aktivera slutanvändarens självbetjäning

Så mycket som möjligt vill du balansera säkerheten med produktiviteten. I samma riktning mot att närma sig din resa med tankesättet att du lägger en grund för säkerhet på lång sikt kan du ta bort friktionen från din organisation genom att ge användarna möjlighet samtidigt som du är vaksam.

### <a name="implement-self-service-password-reset"></a>Implementera återställning av lösenord för självbetjäning

Azure [AD:s självbetjäningslösenordsåterställning (SSPR)](../../active-directory/authentication/quickstart-sspr.md) är ett enkelt sätt för IT-administratörer att tillåta användare att återställa eller låsa upp sina lösenord eller konton utan hjälpdesk eller administratörsingripande. Systemet innehåller detaljerad rapportering som spårar när användare har återställt sina lösenord, tillsammans med meddelanden för att varna dig för missbruk eller missbruk.

### <a name="implement-self-service-group-and-application-access"></a>Implementera självbetjäningsgrupp och programåtkomst

Azure AD ger möjlighet att hantera åtkomst till resurser med hjälp av säkerhetsgrupper, Office 365-grupper, programroller och åtkomstpaketkataloger.  [Grupphantering med självbetjäning](../../active-directory/users-groups-roles/groups-self-service-management.md) gör det möjligt för gruppägare att hantera sina egna grupper utan att behöva tilldelas en administrativ roll. Användare kan också skapa och hantera Office 365-grupper utan att förlita sig på administratörer för att hantera sina begäranden och oanvända grupper upphör att gälla automatiskt.  [Azure AD-berättigandehantering](../../active-directory/governance/entitlement-management-overview.md) möjliggör ytterligare delegering och synlighet, med omfattande arbetsflöden för åtkomstbegäran och automatisk förfallodatum.  Du kan delegera möjligheten att konfigurera egna åtkomstpaket för grupper, teams, program och SharePoint Online-webbplatser som de äger till icke-administratörer, med anpassade principer för vem som måste godkänna åtkomst, inklusive att konfigurera medarbetarens chefer och partnersponsorer som godkännare.

### <a name="implement-azure-ad-access-reviews"></a>Implementera Azure AD-åtkomstgranskningar

Med [Azure AD-åtkomstgranskningar](../../active-directory/governance/access-reviews-overview.md)kan du hantera åtkomstpaket- och gruppmedlemskap, åtkomst till företagsprogram och privilegierade rolltilldelningar för att se till att du upprätthåller en säkerhetsstandard.  Regelbunden tillsyn av användarna själva, resursägare och andra granskare säkerställer att användarna inte behåller åtkomsten under längre tidsperioder när de inte längre behöver den.

## <a name="summary"></a>Sammanfattning

Det finns många aspekter på en säker identitetsinfrastruktur, men den här checklistan i fem steg hjälper dig att snabbt skapa en säkrare och säkrare identitetsinfrastruktur:

* Stärka dina meriter.
* Minska angreppsytan.
* Automatisera hotrespons.
* Använd molnintelligens.
* Aktivera mer förutsägbar och fullständig slutanvändarsäkerhet med självhjälp.

Vi uppskattar hur allvarligt du tar Identity Security och hoppas att detta dokument är en användbar färdplan för en säkrare hållning för din organisation.

## <a name="next-steps"></a>Nästa steg

Om du behöver hjälp med att planera och distribuera rekommendationerna läser du [Azure AD-projektdistributionsplanerna](https://aka.ms/deploymentplans) för hjälp.

Om du är säker på att alla dessa steg är slutförd använder du Microsofts [Identity Secure Score](../../active-directory/fundamentals/identity-secure-score.md), som håller dig uppdaterad med de [senaste bästa metoderna](identity-management-best-practices.md) och säkerhetshoten.
