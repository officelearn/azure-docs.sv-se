---
title: Fem steg för att skydda din identitets infrastruktur i Azure Active Directory
description: Det här dokumentet beskriver en lista över viktiga åtgärder som administratörer ska implementera för att skydda sin organisation med Azure AD-funktioner
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: security
ms.subservice: security-fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 06/18/2018
ms.author: martinco
ms.openlocfilehash: c5cdd12c3075d48ff32c40d686b32a650ec43d8e
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779786"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>Fem steg för att skydda din identitets infrastruktur

Om du läser det här dokumentet är du medveten om betydelsen av säkerhet. Du bär förmodligen redan ansvaret för att skydda din organisation. Om du behöver övertyga andra om vikten av säkerhet kan du skicka dem för att läsa den senaste [Microsoft Security Intelligence-rapporten](https://go.microsoft.com/fwlink/p/?linkid=2073747).

Med det här dokumentet får du en säkrare position med funktionerna i Azure Active Directory genom att använda en check lista för fem steg för att Inokulera din organisation mot cyberhot-attacker.

Den här check listan hjälper dig att snabbt distribuera kritiska rekommenderade åtgärder för att skydda din organisation omedelbart genom att förklara hur du:

* Förstärk dina autentiseringsuppgifter.
* Minska attack ytan.
* Automatisera hot svar.
* Öka din medvetenhet om granskning och övervakning.
* Aktivera mer förutsägbar och fullständig slut användar säkerhet med hjälp av självhjälp.

> [!NOTE]
> Många av rekommendationerna i det här dokumentet gäller endast för program som har kon figurer ATS för att använda Azure Active Directory som identitets leverantör. Genom att konfigurera appar för enkel inloggning ser du fördelarna med principer för autentiseringsuppgifter, Hot identifiering, granskning, loggning och andra funktioner som läggs till i dessa program. [Enkel inloggning via Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-sso) är grunden som alla dessa rekommendationer baseras på.

Rekommendationerna i det här dokumentet är justerade med [identitetens säkra Poäng](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score), en automatisk utvärdering av din Azure AD-klients identitets säkerhets konfiguration. Organisationer kan använda sidan för säker användning av identiteter på Azure AD-portalen för att hitta luckor i den aktuella säkerhets konfigurationen för att se till att de följer aktuella Microsofts bästa praxis för säkerhet. Genom att implementera varje rekommendation på sidan säker Poäng ökar du dina poäng och du kan spåra dina framsteg, samt hjälpa dig att jämföra din implementering mot andra liknande storleks organisationer eller din bransch.

![Identitetsäkerhetspoäng](./media/steps-secure-identity/azure-ad-sec-steps0.png)

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>Innan du börjar: Skydda privilegierade konton med MFA

Innan du börjar check listan, se till att du inte blir komprometterad när du läser den här check listan. Du måste först skydda dina privilegierade konton.

Angripare som får kontroll över privilegierade konton kan orsaka mycket allvarliga skador, så det är viktigt att skydda kontona först. Aktivera och Kräv [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) (MFA) för alla administratörer i din organisation med hjälp av [bas linje skydd](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-baseline-protection). Om du inte har implementerat MFA kan du göra det nu! Det är viktigt.

Har du angett? Vi börjar med check listan.

## <a name="step-1---strengthen-your-credentials"></a>Steg 1 – Förstärk dina autentiseringsuppgifter 

De flesta säkerhets överträdelser i företaget kommer att ha ett konto som har komprometterats med en fåtal av metoder, till exempel lösen ords spray, intrång, omuppspelning eller nätfiske. Läs mer om dessa angrepp i den här videon (45 min):
> [!VIDEO https://www.youtube.com/embed/uy0j1_t5Hd4]

### <a name="make-sure-your-organization-use-strong-authentication"></a>Kontrol lera att din organisation använder stark autentisering

Med hänsyn till frekvensen av lösen ord som gissas, phished, blir stulen med skadlig kod eller återanvänds, är det viktigt att du säkerhetskopierar lösen ordet med någon form av stark autentiseringsuppgift – Läs mer om [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication).

### <a name="start-banning-commonly-attacked-passwords-and-turn-off-traditional-complexity-and-expiration-rules"></a>Starta förbjuda ofta angrepp av lösen ord och inaktivera traditionell komplexitet och utgångs regler.

Många organisationer använder den traditionella komplexiteten (krav på specialtecken, siffror, versaler och gemener) och regler för lösen ordets giltighets tid. [Microsofts forskning](https://aka.ms/passwordguidance) har visat dessa principer som gör det lättare för användarna att välja lösen ord som är lättare att gissa.

Funktionen för [dynamiskt blockerade lösen ord](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad) i Azure AD använder nuvarande angrepps beteende för att hindra användare från att ange lösen ord som lätt kan gissas. Den här funktionen är alltid på när användare skapas i molnet, men är nu även tillgängligt för Hybrid organisationer när de distribuerar [Azure AD Password Protection för Windows Server Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises). Lösen ords skydd i Azure AD hindrar användare från att välja dessa vanliga lösen ord och kan utökas för att blockera lösen ord som innehåller anpassade nyckelord som du anger. Du kan till exempel hindra användarna från att välja lösen ord som innehåller företagets produkt namn eller ett lokalt sport team.

Microsoft rekommenderar att du antar följande modern lösen ords princip baserat på [NIST-vägledning](https://pages.nist.gov/800-63-3/sp800-63b.html):

1. Kräv lösen ord måste innehålla minst 8 tecken. Längre är inte nödvändigt vis bättre, eftersom de gör det möjligt för användarna att välja förutsägbara lösen ord, spara lösen ord i filer eller skriva ned dem.
2. Inaktivera utgångs regler, som gör att användarna lätt kan gissa lösen ord, till exempel **Spring2019!**
3. Inaktivera krav på tecken komposition och hindra användarna från att välja ofta angripna lösen ord, eftersom de gör det möjligt för användarna att välja förutsägbara tecken ersättningar i lösen ord.

Du kan använda [PowerShell för att förhindra att lösen ord upphör att gälla](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy) för användare om du skapar identiteter i Azure AD direkt. Hybrid organisationer bör implementera dessa principer med hjälp av [domän grup princip inställningar](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) eller [Windows PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy).

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>Skydda mot läckta autentiseringsuppgifter och Lägg till återhämtning mot avbrott

Om din organisation använder en hybrid identitets lösning med direktautentisering eller Federation, bör du aktivera lösen ords-hash-synkronisering av följande två orsaker:

* Rapporten [användare med läckta autentiseringsuppgifter](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events) i Azure AD-hanteringen varnar dig om användar namn och lösen ord, som har exponerats på den "mörka webben". En otrolig mängd lösen ord läcker genom nätfiske, skadlig kod och återkoppling av lösen ord på tredje parts webbplatser som senare strider mot varandra. Microsoft hittar många av dessa autentiseringsuppgifter och meddelar dig, i den här rapporten, om de stämmer överens med autentiseringsuppgifter i din organisation, men bara om du [aktiverar lösen ords synkronisering av lösen ord](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)!
* I händelse av ett lokalt avbrott (till exempel i en utpressnings attack) kan du växla över till att använda [molnbaserad autentisering med hjälp av hash-synkronisering av lösen ord](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn). Med den här metoden för säkerhets kopiering kan du fortsätta att komma åt appar som kon figurer ATS för autentisering med Azure Active Directory, inklusive Office 365. I det här fallet behöver IT-personalen inte använda personliga e-postkonton för att dela data förrän det lokala avbrottet har åtgärd ATS.

Lär dig mer om hur [synkronisering av lösen ord för hash](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization) fungerar.

> [!NOTE]
> Om du aktiverar hash-synkronisering av lösen ord och använder Azure AD Domain Services, kommer Kerberos (AES 256) hash-värden och alternativt NTLM (RC4, ingen salt) hashs att krypteras och synkroniseras till Azure AD. 

### <a name="implement-ad-fs-extranet-smart-lockout"></a>Implementera Smart utelåsning för AD FS extra nät

Organisationer, som konfigurerar program att autentisera direkt till Azure AD-förmånen från [Azure AD Smart utelåsning](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords). Om du använder AD FS i Windows Server 2012R2 implementerar du AD FS [extra näts utelåsning-skydd](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection). Om du använder AD FS på Windows Server 2016 implementerar du [extra näts Smart utelåsning](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). AD FS Smart extra nät utelåsning skyddar mot brute force-attacker, vilket riktar AD FS samtidigt som användarna förhindras att låsas i Active Directory.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>Dra nytta av de säkraste och enklare att använda autentiseringsuppgifter

Med hjälp av [Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)kan du ersätta lösen ord med stark tvåfaktorautentisering på datorer och mobila enheter. Den här autentiseringen består av en ny typ av användarautentiseringsuppgifter som är säkert bundna till en enhet och använder bio metrisk eller PIN-kod.

## <a name="step-2---reduce-your-attack-surface"></a>Steg 2 – minska din attack yta

Med tanke på den genomgripande risken för lösen ords angrepp är det viktigt att minimera angrepps ytan i din organisation. Att eliminera användningen av äldre, mindre säkra protokoll, begränsa åtkomst punkter och utnyttja mer betydande kontroll över administrativ åtkomst till resurser kan minska risken för attack ytan.

### <a name="block-legacy-authentication"></a>Blockera äldre autentisering

Appar som använder sina egna äldre metoder för att autentisera med Azure AD och få åtkomst till företags data, utgör en annan risk för organisationer. Exempel på appar som använder äldre autentisering är POP3-, IMAP4-eller SMTP-klienter. Äldre autentiserings-appar autentiseras för användarens räkning och förhindrar att Azure AD utför avancerade säkerhets utvärderingar. Den alternativa, modern autentiseringen, minskar säkerhets risken, eftersom den stöder Multi-Factor Authentication och villkorlig åtkomst. Vi rekommenderar följande tre åtgärder:

1. Blockera [äldre autentisering om du använder AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12).
2. Konfigurera [SharePoint Online och Exchange Online för att använda modern autentisering](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication).
3. Använd [principer för villkorlig åtkomst för att blockera äldre autentisering](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions).

### <a name="block-invalid-authentication-entry-points"></a>Blockera ogiltiga start punkter för autentisering

Genom att använda den ungefärliga kränkningen av intrång bör du minska effekten av komprometterade användarautentiseringsuppgifter när de inträffar. För varje app i din miljö kan du överväga giltiga användnings fall: vilka grupper, vilka nätverk, vilka enheter och andra element som har auktoriserats – och sedan blockera resten. Med [villkorlig åtkomst i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)kan du styra hur behöriga användare får åtkomst till sina appar och resurser baserat på särskilda villkor som du definierar.

### <a name="block-end-user-consent"></a>Blockera slut användar medgivande

Som standard tillåts alla användare i Azure AD att bevilja program som utnyttjar OAuth 2,0 och Microsoft Identity [medgivande Framework](https://docs.microsoft.com/azure/active-directory/develop/consent-framework) -behörigheter för att komma åt företagets data. Även om det är möjligt för användarna att enkelt förvärva användbara program som integrerar med Microsoft 365 och Azure, kan det representera en risk om den inte används och övervakas noggrant. Att [inaktivera alla framtida åtgärder för användar medgivande](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-removing-user-access) kan hjälpa till att minska din yta och minimera risken. Om Slutanvändarens medgivande inaktive ras kommer de tidigare medgivande bidragen fortfarande att bevaras, men alla framtida medgivande åtgärder måste utföras av en administratör. Innan du inaktiverar den här funktionen rekommenderar vi att du ser till att användarna förstår hur de begär administratörs godkännande för nya program. Detta bör hjälpa till att minska användar friktionen, minimera support volymen och se till att användarna inte registrerar sig för program som använder autentiseringsuppgifter som inte är Azure AD.

### <a name="implement-azure-ad-privileged-identity-management"></a>Implementera Azure AD Privileged Identity Management

En annan effekt av "antagen intrång" är behovet av att minimera sannolikheten att ett komprometterat konto kan användas med en privilegie rad roll. 

[Azure AD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) hjälper dig att minimera konto privilegier genom att hjälpa dig att:

* Identifiera och hantera användare som tilldelats administrativa roller.
* Lär dig mer om roller som inte används eller för hög behörighet.
* Upprätta regler för att se till att privilegierade roller skyddas av Multi-Factor Authentication.
* Upprätta regler för att se till att privilegierade roller bara beviljas tillräckligt länge för att kunna utföra den privilegierade uppgiften.

Aktivera Azure AD PIM och Visa sedan de användare som har tilldelats administrativa roller och ta bort onödiga konton i dessa roller. För återstående privilegierade användare flyttar du dem från permanent till berättigade. Upprätta sedan lämpliga principer för att se till att när de behöver få åtkomst till dessa privilegierade roller, kan de göra detta på ett säkert sätt med nödvändig ändrings kontroll.

Som en del av processen för att distribuera ditt privilegierat konto bör du följa [bästa praxis för att skapa minst två nöd konton](https://docs.microsoft.com/azure/active-directory/admin-roles-best-practices) för att se till att du har åtkomst till Azure AD om du låser dig.

## <a name="step-3---automate-threat-response"></a>Steg 3 – automatisera hot svar

Azure Active Directory har många funktioner som automatiskt fångar upp attacker, för att ta bort fördröjningen mellan identifiering och svar. Du kan minska kostnaderna och riskerna när du minskar tiden som cyberbrottslingarna används för att bädda in dem i din miljö. Här är de konkreta steg som du kan vidta.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Implementera säkerhets princip för användar risk med Azure AD Identity Protection

Användar risk anger sannolikheten för att en användares identitet har komprometterats och beräknas utifrån de [användar risk händelser](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) som är associerade med en användares identitet. En användar risk princip är en princip för villkorlig åtkomst som utvärderar risk nivån för en speciell användare eller grupp. Utifrån låg, medel, hög risk nivå kan en princip konfigureras för att blockera åtkomst eller kräva en säker ändring av lösen ord med hjälp av Multi-Factor Authentication. Microsofts rekommendation är att kräva en säker lösen ords ändring för användare med hög risk.

![Användare som har flaggats för risk](./media/steps-secure-identity/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Implementera inloggnings risk princip med Azure AD Identity Protection

Inloggnings risk är sannolikheten någon annan än konto ägaren försöker logga in med identiteten. En [princip för inloggnings risker](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) är en princip för villkorlig åtkomst som utvärderar risk nivån för en speciell användare eller grupp. Utifrån risk nivån (hög/medel/låg) kan en princip konfigureras för att blockera åtkomst eller tvinga Multi-Factor Authentication. Se till att du tvingar Multi-Factor Authentication på medium eller över risk inloggningar.

![Logga in från anonyma IP-adresser](./media/steps-secure-identity/azure-ad-sec-steps2.png)

## <a name="step-4---increase-your-awareness"></a>Steg 4 – öka medvetenheten

Granskning och loggning av säkerhetsrelaterade händelser och relaterade aviseringar är viktiga komponenter i en effektiv skydds strategi. Säkerhets loggar och-rapporter ger dig en elektronisk post med misstänkta aktiviteter och hjälper dig att identifiera mönster som kan indikera försök till eller lyckad extern inträngande av nätverket och interna attacker. Du kan använda granskning för att övervaka användar aktivitet, dokumentera regelefterlevnad, utföra kriminal tekniska analys med mera. Aviseringar ger meddelanden om säkerhets händelser.

### <a name="monitor-azure-ad"></a>Övervaka Azure AD

Microsoft Azure tjänster och funktioner ger dig möjlighet att konfigurera säkerhets gransknings-och loggnings alternativ som hjälper dig att identifiera luckor i dina säkerhets principer och mekanismer och åtgärda dessa luckor för att förhindra överträdelser. Du kan använda [Azure-loggning och granskning](https://docs.microsoft.com/azure/security/fundamentals/log-audit) och använda [gransknings aktivitets rapporter i Azure Active Directory-portalen](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs).

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>Övervaka Azure AD Connect Health i hybrid miljöer

[Genom att övervaka AD FS med Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs) får du bättre inblick i potentiella problem och synligheten för angrepp i din AD FS-infrastruktur. Azure AD Connect Health skickar aviseringar med information, lösnings steg och länkar till relaterad dokumentation; användnings analys för flera mått som rör autentiseringstrafik. prestanda övervakning och rapporter.

![Azure AD Connect Health](./media/steps-secure-identity/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Övervaka Azure AD Identity Protection händelser

[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) är ett meddelande, övervaknings-och rapporterings verktyg som du kan använda för att identifiera potentiella sårbarheter som påverkar organisationens identiteter. Den identifierar risk händelser, t. ex. läckta autentiseringsuppgifter, omöjliga resor och inloggningar från angripna enheter, anonyma IP-adresser, IP-adresser som är kopplade till den misstänkta aktiviteten och okända platser. Aktivera aviseringar om du vill få e-postaviseringar för användare med risk och/eller ett vecko sammandrag e-postmeddelande.

Azure AD Identity Protection ger två viktiga rapporter som du bör övervaka dagligen:
1. Riskfyllda inloggnings rapporter kan användas för användarnas inloggnings aktiviteter som du bör undersöka, den legitima ägaren kanske inte har utfört inloggningen.
2. Riskfyllda användar rapporter kommer att påverka användar konton som kan ha komprometterats, till exempel läckta autentiseringsuppgifter som identifierats eller användaren som är inloggad från olika platser och som orsakar en omöjlig resa-händelse. 

![Användare som har flaggats för risk](./media/steps-secure-identity/azure-ad-sec-steps3.png)

### <a name="audit-apps-and-consented-permissions"></a>Granska appar och samskickade behörigheter

Användare kan luras att navigera till en komprometterad webbplats eller appar som får åtkomst till profil information och användar data, till exempel e-post. En skadlig aktör kan använda de behörigheter som den tagit emot för att kryptera sitt post låda innehåll och begära en utpressnings tro för att återställa data från din post låda. [Administratörer bör granska och granska](https://docs.microsoft.com/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) de behörigheter som anges av användarna.

## <a name="step-5---enable-end-user-self-help"></a>Steg 5 – aktivera själv hjälp för slutanvändare

Så mycket som möjligt bör du utjämna säkerheten med produktiviteten. På samma sätt som du närmar dig resan med tänkesätt som du ställer in en grund för säkerhet i den långa körningen kan du ta bort friktion från din organisation genom att ge användarna möjlighet att göra återstående vigilant. 

### <a name="implement-self-service-password-reset"></a>Implementera återställning av lösen ord för självbetjäning

Azures självbetjäning för [återställning av lösen ord (SSPR)](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started) erbjuder ett enkelt sätt för IT-administratörer att tillåta användare att återställa eller låsa upp sina lösen ord eller konton utan Administratörs åtgärder. Systemet innehåller detaljerade rapporter för att spåra när användare använder systemet tillsammans med aviseringar som informerar om missbruk. 

### <a name="implement-self-service-group-management"></a>Implementera hantering av självbetjänings grupper

Azure AD ger möjlighet att hantera åtkomst till resurser med hjälp av säkerhets grupper och Office 365-grupper. Dessa grupper kan hanteras av grupp ägare i stället för IT-administratörer. Den här funktionen är känd som självbetjänings [grupp hantering](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)och gör det möjligt för grupp ägare som inte har tilldelats en administrativ roll att skapa och hantera grupper utan att behöva använda administratörer för att hantera sina begär Anden.

### <a name="implement-azure-ad-access-reviews"></a>Implementera åtkomst granskningar för Azure AD

Med [åtkomst granskningar i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview)kan du hantera grupp medlemskap, åtkomst till företags program och privilegierade roll tilldelningar för att se till att du upprätthåller en säkerhets standard som inte ger användare åtkomst under längre tid när de inte behöver det.

## <a name="summary"></a>Sammanfattning

Det finns många aspekter av en säker identitets infrastruktur, men den här check listan i fem steg hjälper dig att snabbt uppnå en säkrare och säker identitets infrastruktur:

* Förstärk dina autentiseringsuppgifter.
* Minska attack ytan.
* Automatisera hot svar.
* Öka din medvetenhet om granskning och övervakning.
* Aktivera mer förutsägbar och fullständig slut användar säkerhet med hjälp av självhjälp.

Vi uppskattar hur allvarligt du tar identitets säkerheten och hoppas att det här dokumentet är en bra översikt över en säkrare position för din organisation.

## <a name="next-steps"></a>Nästa steg
Om du behöver hjälp med att planera och distribuera rekommendationerna läser du distributions planerna för [Azure AD-projekt](https://aka.ms/deploymentplans) för att få hjälp.
