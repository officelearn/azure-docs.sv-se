---
title: Azures säkerhets bas linje för Azure Advisor
description: Azure Advisor säkerhets bas linje ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: advisor
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 24935f1b10ec11bb546cb08e06430d1e0b5c31fa
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183336"
---
# <a name="azure-security-baseline-for-azure-advisor"></a>Azures säkerhets bas linje för Azure Advisor

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark-version 2,0](../security/benchmarks/overview.md) till Azure Advisor. Benchmark för Azure-säkerhet innehåller rekommendationer för hur du kan skydda dina molnlösningar på Azure. Innehållet grupperas efter de **säkerhets kontroller** som definieras av Azures säkerhets benchmark och relaterade rikt linjer som gäller för Azure Advisor. **Kontroller** som inte är tillämpliga på Azure Advisor har uteslutits.

Om du vill se hur Azure Advisor helt mappar till Azures säkerhets mätning, se den [fullständiga Azure Advisor mappnings filen för säkerhets bas linjen](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-management"></a>Identitetshantering

*Mer information finns i [Benchmark för Azure-säkerhet: Identitetshantering](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardisera Azure Active Directory som centralt identitets- och autentiseringssystem

**Vägledning**: Azure Advisor använder Azure Active Directory (Azure AD) som standard tjänst för identitets-och åtkomst hantering. Standardisera Azure AD för att styra organisationens identitets-och åtkomst hantering i:

- Microsoft Cloud resurser, till exempel Azure Portal, Azure Storage, virtuell Azure-dator (Linux och Windows), Azure Key Vault, PaaS och SaaS-program
- Organisationens resurser, till exempel program i Azure eller företagets nätverks resurser

Se till att Azure AD skyddas med hög prioritet i din organisations säkerhets praxis för molnet. Azure AD ger också en identitets säker Poäng som hjälper dig att utvärdera position för identiteter i relation till Microsofts rekommendationer för bästa praxis. Använd poängen för att mäta hur nära konfigurationen matchar rekommendationerna för bästa praxis och för att göra förbättringar i din säkerhetsstatus.

Observera att Azure AD stöder externa identiteter, vilket gör det möjligt för användare utan Microsoft-konto att logga in på sina program och resurser med sin externa identitet.

- [Innehav i Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Så skapar och konfigurerar du en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Använda externa identitets leverantörer för program](../active-directory/external-identities/identity-providers.md) 

- [Vad är säkerhets poängen för identiteter i Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Använda enkel inloggning för Azure AD (SSO) för programåtkomst

**Vägledning**: Azure Advisor använder Azure Active Directory (Azure AD) för att tillhandahålla identitets-och åtkomst hantering för Azure-resurser, moln program och lokala program. Här ingår företagsidentiteter som anställda, samt externa identiteter som partner, säljare och leverantörer. 

Använd enkel inloggning för att hantera och skydda åtkomsten till din organisations data och resurser lokalt och i molnet. Anslut alla användare, program och enheter till Azure AD för sömlös, säker åtkomst och bättre synlighet och kontroll.

- [Förstå enkel programinloggning (Application SSO) med Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Använda starka autentiseringskontroller för all Azure Active Directory-baserad åtkomst

**Vägledning**: Azure Advisor använder Azure Active Directory (Azure AD), som stöder starka verifierings kontroller via multifaktorautentisering och starka metoder för lösen ords kryptering.
- Multifaktorautentisering – aktivera Azure AD multifaktor-autentisering och följ Azure Security Center rekommendationer för identitets-och åtkomst hantering för vissa rekommenderade metoder i konfigurationen av multifaktorautentisering. Multifaktorautentisering kan tillämpas på alla, välja användare eller på nivån per användare baserat på inloggnings villkor och risk faktorer.
- Autentisering med lösen ord – tre alternativ för lösen ords avföljer är tillgängliga. De är, Windows Hello för företag, Microsoft Authenticator app och lokala autentiseringsmetoder som smartkort.

För administratörer och privilegierade användare bör du se till att den högsta nivån för metoden stark autentisering används, och introducera därefter lämplig princip för stark autentisering för andra användare.

- [Så här aktiverar du MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Introduktion till alternativ för lösenordsfri autentisering för Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Övervaka och varna vid kontoavvikelser

**Vägledning**: Azure Advisor är integrerat med Azure Active Directory (Azure AD) i som tillhandahåller följande data Källor:
- Logga in – inloggnings rapporten innehåller information om användningen av hanterade program och användar inloggnings aktiviteter.
- Granskningsloggar – Ger spårbarhet via loggar för alla ändringar som gjorts via olika funktioner i Azure AD. Exempel på granskningsloggar är de resursändringar som görs i Azure AD, som att lägga till eller ta bort användare, appar, grupper, roller och principer.
- Riskfylld inloggning – ett riskfylldt inloggning är en indikator för ett inloggnings försök som kan ha utförts av någon som inte är den legitima ägaren till ett användar konto.
- Användare som har flaggats för risk – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats.

Använd dessa data källor för att integrera med Azure Monitor, Azure Sentinel eller SIEM system från tredje part. Installations aviseringar för Azure Security Center för vissa misstänkta aktiviteter, till exempel ett ovanligt antal misslyckade autentiseringsförsök, föråldrade konton i prenumerationen.

Azure Advanced Threat Protection (ATP) är en säkerhetslösning som kan använda Active Directory-signaler för att identifiera, upptäcka och undersöka avancerade hot, komprometterade identiteter och skadliga Insider-åtgärder.

- [Granska aktivitetsrapporter i Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Så här övervakar du användarnas identitets- och åtkomstrelaterade aktiviteter i Azure Security Center](../security-center/security-center-identity-access.md) 

- [Aviseringar i skyddsmodulen för hotinformation i Azure Security Center](../security-center/alerts-reference.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: begränsa åtkomsten till Azure-resursen baserat på villkor

**Vägledning**: Azure Advisor stöder Azure Active Directorys funktion för villkorlig åtkomst i Azure AD för en mer detaljerad åtkomst kontroll baserat på användardefinierade villkor. Användar inloggningar från vissa IP-intervall måste till exempel använda Multi-Factor Authentication för inloggning. Detaljerad hanterings princip för autentisering kan också användas för olika användnings fall.

- [Översikt över villkorlig Azure-åtkomst](../active-directory/conditional-access/overview.md) 

- [Vanliga principer för villkorlig åtkomst](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Konfigurera hantering av autentisering med villkorlig åtkomst](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

## <a name="privileged-access"></a>Privilegierad åtkomst

*Mer information finns i [Benchmark för Azure-säkerhet: Privilegierad åtkomst](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: begränsa administrativ åtkomst till verksamhets kritiska system

**Vägledning**: Azure Advisor använder rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att isolera åtkomst till affärs kritiska system genom att begränsa vilka konton som beviljas privilegierade åtkomst till prenumerationer och hanterings grupper som de tillhör.

Begränsa åtkomsten till hanterings-, identitets-och säkerhets system som har administrativ åtkomst till din affärs kritiska åtkomst, till exempel Active Directory-domän kontrollanter (DCs), säkerhets verktyg och system hanterings verktyg med agenter installerade på affärs kritiska system. Angripare som angriper dessa hanterings-och säkerhets system kan omedelbart weaponize dem för att kompromettera affärs kritiska till gångar.

Alla typer av åtkomst kontroller bör justeras mot din strategi för företags segmentering för att säkerställa en konsekvent åtkomst kontroll.

- [Azure-komponenter och referens modell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Åtkomst till hanterings grupp](../governance/management-groups/overview.md#management-group-access)

- [Administratörer för Azure-prenumeration](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Granska och stäm av användaråtkomsten regelbundet

**Vägledning**: Azure Advisor använder Azure Active Directory-(Azure AD)-konton för att hantera sina resurser, granska användar konton och åtkomst tilldelningen regelbundet för att säkerställa att kontona och deras åtkomst är giltiga. Implementera åtkomst granskningar för Azure AD för att granska grupp medlemskap, åtkomst till företags program och roll tilldelningar. Azure AD repor ting kan tillhandahålla loggar för att identifiera inaktuella konton. 

Använd dessutom Azure ADs Privileged Identity Management funktioner för att skapa åtkomst gransknings rapportens arbets flöde för att under lätta gransknings processen. Privileged Identity Management kan också konfigureras att avisera när ett högt antal administratörs konton skapas och för att identifiera administratörs konton som är inaktuella eller felaktigt konfigurerade.

Observera att vissa Azure-tjänster har stöd för lokala användare och roller som inte hanteras via Azure AD. Kunderna måste hantera dessa användare separat.

- [Skapa en åtkomst granskning av Azure Resource roles i Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Så här använder du identitets- och åtkomstgranskningar i Azure AD](/azure/active-directory/governance/access-reviews-overvie)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Använd arbets stationer med privilegie rad åtkomst

**Vägledning**: skyddade, isolerade arbets stationer är mycket viktiga för att skydda känsliga roller som administratörer, utvecklare och kritiska tjänst operatörer. 

Använd hög säkra användar arbets stationer och/eller Azure-skydds för administrativa uppgifter. Välj Azure Active Directory (Azure AD), Microsoft Defender Avancerat skydd (ATP), inklusive Microsoft Intune för att distribuera en säker och hanterad användar arbets station för administrativa uppgifter. 

Hantera de skyddade arbets stationerna centralt för att framtvinga säker konfiguration, inklusive stark autentisering, program vara och maskin vara, begränsad logisk och nätverks åtkomst.

- [Förstå arbets stationer med privilegie rad åtkomst](../active-directory/devices/concept-azure-managed-workstation.md) 
 
- [Distribuera en privilegie rad åtkomst arbets Station](../active-directory/devices/howto-azure-managed-workstation.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Följ JEA (Just Enough Administration, precis tillräcklig administration) (principen om minsta behörighet) 

**Vägledning**: Azure Advisor är integrerat med rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att hantera resurserna. Använd Azure RBAC för att hantera Azure Resource Access via roll tilldelningar. 

Tilldela roller till användare, gruppera tjänstens huvud namn och hanterade identiteter. Det finns fördefinierade inbyggda roller för vissa resurser som kan inventeras eller frågas via verktyg som Azure CLI, Azure PowerShell eller Azure Portal. De privilegier som tilldelas resurser via Azure RBAC bör alltid vara begränsade till vad som krävs av rollerna. Det här är ett komplement till JIT-metoden (just in time) för Azure AD Privileged Identity Management (PIM) och bör granskas regelbundet.

Använd inbyggda roller för att allokera behörighet och skapa bara en anpassad roll vid behov.

Vad är Azure-baserad åtkomstkontroll (Azure RBAC) ../role-based-access-control/overview.md 

- [Så här konfigurerar du RBAC i Azure](../role-based-access-control/role-assignments-portal.md) 

- [Så här använder du identitets- och åtkomstgranskningar i Azure AD](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

## <a name="asset-management"></a>Tillgångshantering

*Mer information finns i [Benchmark för Azure-säkerhet: Tillgångshantering](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Se till att säkerhetsteamet har insyn i risker för tillgångar

**Riktlinjer**: Se till att säkerhetsteamen beviljas behörigheter för säkerhetsläsare i din Azure-klient och prenumerationer som gör att de kan övervaka säkerhetsrisker med hjälp av Azure Security Center. 

Beroende på hur säkerhetsteamets ansvarsområden är strukturerade kan ansvaret för övervakning av säkerhetsrisker ligga hos en central säkerhetsgrupp eller ett lokalt team. Detta innebär att säkerhetsinsikter och -risker alltid måste samlas centralt inom en organisation. 

Behörigheter för säkerhetsläsare kan tillämpas brett över en hel klientorganisation (rothanteringsgrupp) eller omfattas av hanteringsgrupper eller specifika prenumerationer. 

Obs! Ytterligare behörigheter kan krävas för att få insyn i arbetsbelastningar och tjänster. 

- [Översikt över säkerhetsläsarrollen](../role-based-access-control/built-in-roles.md#security-reader)

- [Översikt över Azure-hanteringsgrupper](../governance/management-groups/overview.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Azure Advisor använder Azure Active Directory (Azure AD) för identitet och autentisering, medan Azure Portal och Azure Resource Manager används för att hantera Advisor. 

Använd villkorlig åtkomst i Azure för att begränsa en användares möjlighet att interagera med Azures resurs hanterare genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering", efter behov baserat på affärs krav. 

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azures resurs hanterare](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

## <a name="logging-and-threat-detection"></a>Loggning och hotidentifiering

*Mer information finns i [Benchmark för Azure-säkerhet: Loggning och hotidentifiering](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Aktivera loggning för Azure-resurser

**Vägledning**: aktivitets loggar är automatiskt tillgängliga och innehåller alla Skriv åtgärder (placera, skicka och ta bort) för dina Azure Advisor resurser förutom Läs åtgärder (get). 

Aktivitets loggar kan användas för att hitta ett fel när du felsöker eller övervakar hur en användare i organisationen ändrade en resurs.

- [Förstå loggning och olika logg typer i Azure](../azure-monitor/platform/platform-logs-overview.md)

- [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: centralisera hantering av säkerhets loggar och analys

**Vägledning**: centralisera loggning av lagring och analys för att aktivera korrelation. För varje logg källa ser du till att du har tilldelat en data ägare, åtkomst vägledning, lagrings plats, vilka verktyg som används för att bearbeta och komma åt data samt krav för data lagring.

Se till att du integrerar Azures aktivitets loggar i den centrala loggningen. Mata in loggar via Azure Monitor för att samla in säkerhets data som genererats av slut punkts enheter, nätverks resurser och andra säkerhets system. I Azure Monitor använder du Log Analytics arbets ytor för att fråga och utföra analyser och använda Azure Storage konton för långsiktig lagring.

Du kan också aktivera och publicera data till Azure Sentinel eller en SIEM från tredje part. Många organisationer väljer att använda Azure Sentinel för "heta" data som används ofta och Azure Storage för "kall" data som används mindre ofta.

- [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurera kvarhållning av logg lagring

**Vägledning**: kontrol lera att lagrings konton eller Log Analytics arbets ytor som används för att lagra Azure Advisor loggar har den logg kvarhållningsperiod som anges enligt organisationens regler för efterlevnad.
I Azure Monitor kan du Log Analytics ange arbets ytans lagrings period enligt organisationens regler för efterlevnad. Använd Azure Storage-, Data Lake-eller Log Analytics arbets ytans konton för långsiktig lagring och arkivering.

- [Så här konfigurerar du Log Analytics bevarande period för arbets ytor](../azure-monitor/platform/manage-cost-storage.md) 

- [Lagra resurs loggar i ett Azure Storage konto](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Benchmark för Azure-säkerhet: Incidenthantering](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Förberedelse – uppdatera incidenthanteringsprocessen för Azure

**Riktlinjer**: Se till att din organisation har processer för att hantera säkerhetsincidenter, har uppdaterat dessa processer för Azure och utnyttjar dem regelbundet för att säkerställa beredskap.

- [Implementera säkerhet i företagsmiljön](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Referensguide för incidenthantering](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Förberedelser – konfigurera incidentmeddelande

**Riktlinjer**: Konfigurera kontaktinformation för säkerhetsincidenter i Azure Security Center. Microsoft använder kontaktinformationen för att kontakta dig om Microsoft Security Response Center (MRSC) upptäcker att en obehörig part har kommit åt dina data. Du har också alternativ för att anpassa incidentaviseringar och -meddelanden i olika Azure-tjänster baserat på dina behov av incidenthantering. 

- [Så här ställer du in säkerhetskontakt i Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Identifiering och analys – skapa incidenter baserat på aviseringar med hög kvalitet

**Riktlinjer**: Se till att du har en process för att skapa aviseringar med hög kvalitet och att mäta kvaliteten på aviseringar. På så sätt kan du dra slutsatser från tidigare incidenter och prioritera aviseringar för analytiker, så att de inte slösar tid på falska positiva identifieringar. 

Aviseringar med hög kvalitet kan byggas utifrån erfarenhet från tidigare incidenter, validerade community-källor och verktyg som utformats för att generera och rensa aviseringar genom att slå samman och korrelera olika signalkällor. 

Azure Security Center tillhandahåller aviseringar med hög kvalitet för flera olika typer av Azure-tillgångar. Du kan använda anslutningsprogrammet för ASC-data för att strömma aviseringarna till Azure Sentinel. Med Azure Sentinel kan du skapa avancerade aviseringsregler för att generera incidenter automatiskt för en undersökning. 

Exportera dina Azure Security Center-aviseringar och -rekommendationer med hjälp av exportfunktionen för att identifiera risker med Azure-resurser. Exportera aviseringar och rekommendationer antingen manuellt eller löpande.

- [Så här konfigurerar du export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Identifiering och analys – undersöka en incident

**Riktlinjer**: Se till att analytiker kan fråga och använda olika datakällor när de undersöker potentiella incidenter, så att de kan skapa en fullständig överblick över vad som hände. Diverse loggar bör samlas in för att spåra en potentiell angripares aktiviteter över hela händelsekedjan för att undvika att blinda fläckar.  Du bör också se till att insikter och kunskaper fångas upp för andra analytiker och för framtida historiska referenser.  

Datakällorna för undersökningen innehåller de centraliserade loggningskällor som redan har samlats in från tjänster som omfattas och system som körs, men kan även innehålla:

- Nätverksdata – Använd nätverkssäkerhetsgruppers flödesloggar, Azure Network Watcher och Azure Monitor för att avbilda nätverksflödesloggar och annan analysinformation. 

- Ögonblicksbilder av system som körs: 

    - Använd funktionen för ögonblicksbilder på den virtuella Azure-datorn för att skapa en ögonblicksbild av det aktiva systemets disk. 

    - Använd operativsystemets interna minnesdumpningsfunktion för att skapa en ögonblicksbild av det aktiva systemets minne.

    - Använd funktionen för ögonblicksbilder i Azure-tjänsterna eller din programvaras egna funktion för att skapa ögonblicksbilder av de system som körs.

Azure Sentinel tillhandahåller omfattande dataanalyser i praktiskt taget alla loggkällor och en ärendehanteringsportal för att hantera hela livscykeln för incidenter. Affärsinformation under en undersökning kan associeras med en incident för spårning och rapportering. 

- [Ögonblicksbild av en Windows-dators disk](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Ögonblicksbild av en Linux-dators disk](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure har stöd för diagnostikinformation och insamling av minnesdumpar](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Undersöka incidenter med Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Identifiering och analys – prioritera incidenter

**Riktlinjer**: Tillhandahålla en kontext till analytiker om vilka incidenter att fokusera på först baserat på aviseringens allvarlighetsgrad och tillgångens känslighet. 

Azure Security Center tilldelar en allvarlighetsgrad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighetsgraden baseras på hur tillförlitligt Security Center är i sökandet eller på den analys som användes för att utfärda aviseringen, samt konfidensnivån att det fanns en skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Markera även resurser med taggar och skapa ett namngivningssystem för att identifiera och kategorisera Azure-resurser, i synnerhet sådana som används för bearbetning av känsliga data.  Det är ditt ansvar att prioritera åtgärdandet av aviseringar baserat på allvarlighetsgraden för de Azure-resurser och den miljö där incidenten inträffade.

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Använda taggar för att organisera dina Azure-resurser](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Inneslutning, utrotning och återställning – automatisera incidenthanteringen

**Riktlinjer**: Automatisera manuella återkommande uppgifter för att förkorta svarstiden och minska belastningen på analytiker. Manuella uppgifter tar längre tid att köra, vilket saktar ned varje incident och minskar antalet incidenter som en analytiker kan hantera. Manuella uppgifter gör dessutom analytikern mer utmattad, vilket ökar risken för mänskliga fel som i sin tur orsakar fördröjningar, samt minskar analytikernas förmåga att fokusera på komplexa uppgifter på ett effektivt sätt. Använd automatiseringsfunktioner för arbetsflöde i Azure Security Center och Azure Sentinel för att automatiskt utlösa åtgärder eller köra en spelbok för att svara på inkommande säkerhetsaviseringar. Spelboken vidtar åtgärder som att skicka meddelanden, inaktivera konton och isolera problematiska nätverk. 

- [Konfigurera arbetsflödesautomation i Security Center](../security-center/workflow-automation.md)

- [Konfigurera automatiska svar på hot i Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Konfigurera automatiska svar på hot i Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="posture-and-vulnerability-management"></a>Status- och sårbarhetshantering

*Mer information finns i [Benchmark för Azure-säkerhet: Status- och sårbarhetshantering](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Genomför regelbunden attacksimulering

**Riktlinjer**: Genomför vid behov genomslagstestning eller Red Team-aktiviteter på dina Azure-resurser och se till att alla kritiska säkerhetsbrister åtgärdas.
Följ Microsoft Clouds deltagarregler för genomslagstestning för att se till att genomslagstesterna inte strider mot Microsofts principer. Använd Microsofts strategi för och verkställande av Red Team-indelning och genomslagstester för live-webbplatser mot Microsoft-hanterad molninfrastruktur, tjänster och program.

- [Genomslagstestning i Azure](../security/fundamentals/pen-testing.md)

- [Deltagarregler för genomslagstestning](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Red Team-indelning i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Delad

## <a name="governance-and-strategy"></a>Styrning och strategi

*Mer information finns i [Benchmark för Azure-säkerhet: Styrning och strategi](../security/benchmarks/security-controls-v2-governance-strategy.md).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definiera strategin för tillgångshantering och dataskydd 

**Riktlinjer**: Se till att dokumentera och förmedla en tydlig strategi för kontinuerlig övervakning och skydd av system och data. Prioritera upptäckt, utvärdering, skydd och övervakning av affärskritiska data och system. 

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

-   Standard för klassificering av data i enlighet med affärsriskerna

-   Insyn för säkerhetsorganisationen i risker och tillgångsinventering 

-   Säkerhetsorganisations godkännande av Azure-tjänster att använda 

-   Säkerhet för tillgångar via deras livscykel

-   Strategi för nödvändig åtkomstkontroll i enlighet med organisationens dataklassificering

-   Användning av inbyggda säkerhetsfunktioner för Azure- och tredje partsdata

-   Krypteringskrav för användningsfall med data under transport och i vila

-   Lämpliga kryptografiska standarder

Mer information finns i följande resurser:
- [Rekommendation för Azure Security Architecture – lagring, data och kryptering](/azure/architecture/framework/security/storage-data-encryption?amp;bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Grundläggande Azure-säkerhet – säkerhet, kryptering och lagring av data i Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework – bästa praxis för datasäkerhet och kryptering i Azure](../security/fundamentals/data-encryption-best-practices.md?amp;bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Benchmark för Azure-säkerhet – tillgångshantering](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Benchmark för Azure-säkerhet – dataskydd](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definiera strategi för företagssegmentering 

**Vägledning**: upprätta en övergripande strategi för att segmentera åtkomst till till gångar med en kombination av identitet, nätverk, program, prenumeration, hanterings grupp, bland andra kontroller.

Du bör noggrant balansera behovet av säkerhetsseparation med behovet av att möjliggöra daglig drift av de system som behöver kommunicera med varandra och komma åt data.

Se till att segmenterings strategin implementeras konsekvent över kontroll typer, inklusive nätverks säkerhet, identitets-och åtkomst modeller samt program behörighet eller åtkomst modeller samt mänskliga process kontroller.

- [Vägledning om segmenteringsstrategi i Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Vägledning om segmenteringsstrategi i Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Justera nätverkssegmentering med strategi för företagssegmentering](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definiera strategi för hantering av säkerhetsstatus

**Riktlinjer**: Mät och minimera kontinuerligt risker för enskilda tillgångar och den miljö som de finns i. Prioritera tillgångar med högt värde och högexponerade attackytor, till exempel publicerade program, ingångs- och utgångspunkter för nätverk, slutpunkter för användare och administratörer osv.

- [Benchmark för Azure-säkerhet – status- och sårbarhetshantering](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Justera roller, ansvarsområden och ansvar

**Riktlinjer**: Se till att dokumentera och förmedla en tydlig strategi för roller och ansvarsområden i din säkerhetsorganisation. Prioritera att presentera tydligt ansvar för säkerhetsbeslut, utbilda alla i den delade ansvarsmodellen och utbilda tekniska team om teknik för att skydda molnet.

- [Bästa praxis för Azure-säkerhet 1 – personer: Utbilda team i resan mot molnsäkerhet](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Bästa praxis för Azure-säkerhet 2 – personer: Utbilda team i teknik för molnsäkerhet](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Bästa praxis för Azure-säkerhet 3 – process: Tilldela ansvar för beslut kring molnsäkerhet](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definiera en strategi för nätverkssäkerhet

**Riktlinjer**: Upprätta en metod för Azure-nätverkssäkerhet som en del av din organisations övergripande strategi för säker åtkomstkontroll.  

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

-   Centraliserad nätverkshantering och säkerhetsansvar

-   Modell för virtuell nätverkssegmentering i linje med företagets segmenteringsstrategi

-   Åtgärdsstrategi i olika hot- och attackscenarier

-   Strategi för Internets kanter, in- och utgångspunkter

-   Strategi för hybridmoln och lokala anslutningar

-   Aktuella artefakter för nätverkssäkerhet (t.ex. nätverksdiagram, arkitektur för referensnätverk)

Mer information finns i följande resurser:
- [Bästa praxis för Azure-säkerhet 11 – arkitektur. En enhetlig säkerhetsstrategi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Benchmark för Azure-säkerhet – nätverkssäkerhet](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Översikt över Azure-nätverkssäkerhet](../security/fundamentals/network-overview.md)

- [Arkitekturstrategi för företagsnätverk](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definiera strategi för identiteter och privilegierad åtkomst

**Vägledning**: upprätta en metod för Azure Identity och Privileged Access som en del av organisationens övergripande strategi för företags åtkomst kontroll.  

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

-   Ett centraliserat identitets- och autentiseringssystem och dess anslutningar till andra interna och externa identitetssystem

-   Starka autentiseringsmetoder i olika användnings fall och förhållanden

-   Skydd av användare med höga privilegier

-   Övervakning och hantering av avvikande användaraktiviteter  

-   Process för granskning och avstämning av användaridentitet och -åtkomst

Granska de refererade länkarna om du vill ha mer information.

- [Benchmark för Azure-säkerhet – identitetshantering](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Benchmark för Azure-säkerhet – privilegierad åtkomst](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Bästa praxis för Azure-säkerhet 11 – arkitektur. En enhetlig säkerhetsstrategi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Säkerhetsöversikt för identitetshantering i Azure](../security/fundamentals/identity-management-overview.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definiera strategi för loggning och hothantering

**Riktlinjer**: Upprätta en strategi för loggning och hothantering för att snabbt upptäcka och åtgärda hot och samtidigt uppfylla kraven för efterlevnad. Prioritera att ge analytiker högkvalitativa varningar och sömlösa upplevelser så att de kan fokusera på hot i stället för på integrering och manuella åtgärder. 

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

-   SecOps-organisationens roll och ansvarsområden 

-   En väldefinierad process för incidenthantering i linje med NIST eller annat branschramverk 

-   Insamling och kvarhållning av loggar för att stödja hotidentifiering, incidentsvar och krav på efterlevnad

-   Centraliserad insyn i och korrelationsuppgifter om hot med hjälp av Säkerhetsinformation och händelsehantering, inbyggda Azure-funktioner och andra källor 

-   Kommunikations- och aviseringsplan för dina kunder, leverantörer och offentliga intressenter

-   Användning av inbyggda Azure- och tredjeparts-plattformar för incidenthantering, till exempel loggning och hotidentifiering, analys och attackåtgärder och -utrotning

-   Processer för hantering av incidenter och aktiviteter efter incidenter, till lärdomar och kvarhållning av bevis

Mer information finns i följande resurser:

- [Benchmark för Azure-säkerhet – loggning och hotidentifiering](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Benchmark för Azure-säkerhet – incidenthantering](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Bästa praxis för Azure-säkerhet 4 – process. Uppdatera incidenthanteringsprocesser för molnet](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Beslutsguide för ramverk för Azure-införande, loggning och rapportering](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure i företagsskala, hantering och övervakning](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

## <a name="next-steps"></a>Nästa steg

- Se [Översikt över Benchmark för Azure-säkerhet V2](../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../security/benchmarks/security-baselines-overview.md)