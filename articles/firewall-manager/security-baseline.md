---
title: Azures säkerhets bas linje för Azure Firewall Manager
description: Säkerhets bas linjen för Azure Firewall Manager ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8d3288ddde87468f3b6382fab77b0097e350dde2
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96171049"
---
# <a name="azure-security-baseline-for-azure-firewall-manager"></a>Azures säkerhets bas linje för Azure Firewall Manager

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark-version 2,0](../security/benchmarks/overview.md) till Azure Firewall Manager. Benchmark för Azure-säkerhet innehåller rekommendationer för hur du kan skydda dina molnlösningar på Azure. Innehållet grupperas efter de **säkerhets kontroller** som definieras av Azures säkerhets benchmark och relaterade rikt linjer som gäller för Azure Firewall Manager. **Kontroller** som inte är tillämpliga på Azure Firewall Manager har uteslutits.

Om du vill se hur Azure Firewall Manager fullständigt mappar till Azures säkerhets benchmark, se den [fullständiga mappnings filen för Azure Firewall Manager säkerhets bas linje](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-management"></a>Identitetshantering

*Mer information finns i [Benchmark för Azure-säkerhet: Identitetshantering](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardisera Azure Active Directory som centralt identitets- och autentiseringssystem

**Vägledning**: Azure Firewall Manager använder Azure Active Directory (Azure AD) som standard tjänst för identitets-och åtkomst hantering. Du bör standardisera Azure AD för att styra organisationens identitets-och åtkomst hantering i:

- Microsoft Cloud resurser, till exempel Azure Portal, Azure Storage, virtuell Azure-dator (Linux och Windows), Azure Key Vault, PaaS och SaaS program.

- Organisationens resurser, till exempel program i Azure eller företagets nätverksresurser.

Att skydda Azure AD bör vara en hög prioritet i din organisations rutiner för molnsäkerhet. Azure AD ger en identitets säker Poäng som hjälper dig att utvärdera position för identiteter i relation till Microsofts rekommendationer för bästa praxis. Använd poängen för att mäta hur nära konfigurationen matchar rekommendationerna för bästa praxis och för att göra förbättringar i din säkerhetsstatus.

Azure AD stöder extern identitet som gör det möjligt för användare utan Microsoft-konto att logga in på sina program och resurser med sin externa identitet.

- [Innehav i Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md)

- [Så skapar och konfigurerar du en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Använda externa identitets leverantörer för program](/azure/active-directory/b2b/identity-providers)

- [Vad är säkerhets poängen för identiteter i Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Använda enkel inloggning för Azure AD (SSO) för programåtkomst

**Vägledning**: Azure Firewall Manager använder Azure Active Directory för att tillhandahålla identitets-och åtkomst hantering för Azure-resurser, moln program och lokala program. Här ingår företagsidentiteter som anställda, samt externa identiteter som partner, säljare och leverantörer. Det här möjliggör enkel inloggning (SSO) för att hantera och skydda åtkomsten till din organisations data och resurser lokalt och i molnet. Anslut alla användare, program och enheter till Azure AD för sömlös, säker åtkomst och bättre synlighet och kontroll.

- [Förstå enkel programinloggning (Application SSO) med Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Använda starka autentiseringskontroller för all Azure Active Directory-baserad åtkomst

**Vägledning**: Azure Firewall Manager använder Azure Active Directory som stöder starka verifierings kontroller med Multi-Factor Authentication (MFA) och starka metoder för lösen ords kryptering.
- Multifaktorautentisering (MFA) – aktivera Azure AD MFA och följ Azure Security Centers rekommendationer för identitets- och åtkomsthantering för vissa rekommenderade metoder i din MFA-installation. MFA kan tillämpas på alla, på utvalda användare eller på per användare-nivå baserat på inloggningsvillkor och riskfaktorer.
- Lösenordsfri autentisering – det finns tre alternativ för lösenordsfri autentisering: Windows Hello for Business, Microsoft Authenticator-appen och lokala autentiseringsmetoder som smartkort.

För administratörer och privilegierade användare bör du se till att den högsta nivån för metoden stark autentisering används, följt av lämplig princip för stark autentisering för andra användare.

- [Så här aktiverar du MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Introduktion till alternativ för lösenordsfri autentisering för Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

- [Standardprincip för lösenord för Azure AD](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Eliminera felaktiga lösen ord med Azure AD-lösenordsskydd](../active-directory/authentication/concept-password-ban-bad.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Övervaka och varna vid kontoavvikelser

**Vägledning**: Azure Firewall Manager är integrerat med Azure Active Directory i som tillhandahåller följande data Källor:
- Inloggningsaktiviteter – Rapporten för inloggningsaktiviteter ger information om användningen av hanterade program och användares inloggningsaktiviteter.
- Granskningsloggar – Ger spårbarhet via loggar för alla ändringar som gjorts via olika funktioner i Azure AD. Exempel på granskningsloggar är de resursändringar som görs i Azure AD, som att lägga till eller ta bort användare, appar, grupper, roller och principer.
- Riskfyllda inloggningar – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare.
- Användare som har flaggats för risk – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats.

Dessa data källor kan integreras med Azure Monitor, Azure Sentinel eller SIEM system från tredje part.

För närvarande fungerar åtgärder som omgivande brand Väggs princip regel samlings grupper inte stöds av aktivitets loggen, det här är ett känt problem och behandlas i framtida uppdateringar.

Azure Security Center kan också avisera om vissa misstänkta aktiviteter, till exempel ett högt antal misslyckade autentiseringsförsök eller föråldrade konton i prenumerationen.

Azure Advanced Threat Protection (ATP) är en säkerhetslösning som kan använda Active Directory-signaler för att identifiera, upptäcka och undersöka avancerade hot, komprometterade identiteter och skadliga Insider-åtgärder.

- [Granska aktivitetsrapporter i Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Så visar du riskfyllda inloggningar för Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Så här övervakar du användarnas identitets- och åtkomstrelaterade aktiviteter i Azure Security Center](../security-center/security-center-identity-access.md)

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Kända problem med Azure Firewall Manager](overview.md#known-issues)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: begränsa åtkomsten till Azure-resursen baserat på villkor

**Vägledning**: Azure Firewall Manager stöder Azure Active Directory (Azure AD) villkorlig åtkomst för en mer detaljerad åtkomst kontroll baserat på användardefinierade villkor, till exempel användar inloggningar från vissa IP-intervall som behöver använda MFA för inloggning. Detaljerad hanterings princip för autentisering kan också användas för olika användnings fall.

- [Översikt över villkorlig Azure-åtkomst](../active-directory/conditional-access/overview.md)

- [Vanliga principer för villkorlig åtkomst](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Konfigurera hantering av autentisering med villkorlig åtkomst](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="privileged-access"></a>Privilegierad åtkomst

*Mer information finns i [Benchmark för Azure-säkerhet: Privilegierad åtkomst](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Skydda och begränsa användare med höga privilegier

**Vägledning**: Azure Firewall Manager använder Azure Active Directory (Azure AD) för identitet och åtkomst. De mest kritiska inbyggda rollerna är Azure AD global administratör och den privilegierade roll administratören när användare som tilldelats dessa två roller kan delegera administratörs roller:
- Global administratör/företags administratör: användare med den här rollen har åtkomst till alla administrativa funktioner i Azure AD, samt tjänster som använder Azure AD-identiteter.
- Privilegie rad roll administratör: användare med den här rollen kan hantera roll tilldelningar i Azure AD, samt i Azure AD Privileged Identity Management (PIM). Dessutom tillåter den här rollen hantering av alla aspekter av PIM och administrativa enheter.

Du kan ha andra viktiga roller som behöver regleras om du använder anpassade roller med vissa privilegierade behörigheter tilldelade. Och du kanske också vill använda liknande kontroller för administratörs kontot för kritiska företags till gångar.

Du kan aktivera just-in-time (JIT)-privilegierad åtkomst till Azure-resurser och Azure AD med hjälp av Azure AD Privileged Identity Management (PIM). JIT beviljar temporära behörigheter för att utföra privilegierade uppgifter endast när användarna behöver det. PIM kan också generera säkerhetsaviseringar när det finns misstänkt eller osäker aktivitet i din Azure AD-organisation.

- [Behörigheter för administratörsrollen i Azure AD](/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

- [Använda säkerhetsaviseringar från Privileged Identity Management i Azure](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Skydda privilegierad åtkomst för hybrid- och molndistributioner i Azure AD](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: begränsa administrativ åtkomst till verksamhets kritiska system

**Vägledning**: Använd anpassade Azure RBAC-roller för att isolera åtkomst till regel samlings grupper för Azure brand Väggs principer. Använd en anpassad roll definition i Azure för att förhindra oavsiktlig grund policy borttagning och ge selektiv åtkomst till regel samlings grupper i en prenumeration eller resurs grupp.

Se till att du också begränsar åtkomsten till hanterings-, identitets-och säkerhets system som har administrativ åtkomst till dina affärs kritiska system. Angripare som angriper dessa hanterings-och säkerhets system kan omedelbart weaponize dem för att kompromettera affärs kritiska till gångar.

Alla typer av åtkomst kontroller bör justeras mot din strategi för företags segmentering för att säkerställa en konsekvent åtkomst kontroll.

- [Använd Azures brand Väggs princip för att definiera en regel-hierarki](rule-hierarchy.md)

- [Azure-komponenter och referens modell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Åtkomst till hanterings grupp](../governance/management-groups/overview.md#management-group-access)

- [Administratörer för Azure-prenumeration](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Granska och stäm av användaråtkomsten regelbundet

**Vägledning**: Azure Firewall Manager använder Azure Active Directory-konton (Azure AD) för att hantera sina resurser. Granska användar konton och åtkomst tilldelningen regelbundet för att säkerställa att kontona och deras åtkomst är giltiga. Du kan använda åtkomst granskningar i Azure AD för att granska grupp medlemskap, åtkomst till företags program och roll tilldelningar. Azure AD repor ting kan tillhandahålla loggar för att identifiera inaktuella konton. Du kan också använda Azure AD Privileged Identity Management för att skapa åtkomst gransknings rapportens arbets flöde för att under lätta gransknings processen.

Dessutom kan Azure Privileged Identity Management också konfigureras för att varna när ett högt antal administratörs konton skapas och för att identifiera administratörs konton som är inaktuella eller felaktigt konfigurerade.

Vissa Azure-tjänster har stöd för lokala användare och roller som inte hanteras via Azure AD. Du måste hantera dessa användare separat.

- [Skapa en åtkomst granskning av Azure Resource roles i Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Så här använder du identitets- och åtkomstgranskningar i Azure AD](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Konfigurera nöd åtkomst i Azure AD

**Vägledning**: Azure Firewall Manager använder Azure Active Directory för att autentisera användare som hanterar tjänsten. För att förhindra oavsiktligt låst av din Azure AD-organisation konfigurerar du ett konto för nöd åtkomst för åtkomst när normala administrativa konton inte kan användas. Konton för nöd åtkomst är ofta mycket privilegierade och de bör inte tilldelas vissa individer. Konton för nöd åtkomst är begränsade till nöd situationer eller "Break glas"-scenarier där normala administrativa konton inte kan användas.

Du bör se till att autentiseringsuppgifterna (till exempel lösen ord, certifikat eller smartkort) för konton för nöd åtkomst hålls skyddade och endast kända för personer som har behörighet att använda dem endast i nödfall.

- [Hantera konton för nöd åtkomst i Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatisera rättighets hantering 

**Vägledning**: Azure Firewall Manager är integrerat med Azure Active Directory för att hantera sina resurser. Använd funktioner för hantering av Azure AD-rättighet för att automatisera åtkomst till begär ande arbets flöden, inklusive åtkomst tilldelningar, recensioner och förfallo datum. Det finns också stöd för godkännande med dubbla eller flera steg.

- [Vad är åtkomst granskningar för Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Vad är hantering av Azure AD-berättigande](../active-directory/governance/entitlement-management-overview.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Använd arbets stationer med privilegie rad åtkomst

**Vägledning**: skyddade, isolerade arbets stationer är mycket viktiga för att skydda känsliga roller som administratörer, utvecklare och kritiska tjänst operatörer. Använd hög säkra användar arbets stationer för att utföra administrativa hanterings uppgifter med dina Azure Firewall Manager-resurser i produktions miljöer. Använd Azure Active Directory, Microsoft Defender Avancerat skydd (ATP) och/eller Microsoft Intune för att distribuera en säker och hanterad användar arbets station för administrativa uppgifter. De skyddade arbets stationerna kan hanteras centralt för att upprätthålla säker konfiguration, inklusive stark autentisering, program vara och maskin varu bas linjer och begränsad logisk och nätverks åtkomst.

- [Förstå arbets stationer med privilegie rad åtkomst](../active-directory/devices/concept-azure-managed-workstation.md)

- [Distribuera en privilegie rad åtkomst arbets Station](../active-directory/devices/howto-azure-managed-workstation.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Följ JEA (Just Enough Administration, precis tillräcklig administration) (principen om minsta behörighet) 

**Vägledning**: Azure Firewall Manager är integrerat med rollbaserad åtkomst kontroll (RBAC) i Azure för att hantera resurserna. Med Azure RBAC kan du hantera åtkomst till Azure-resurser via rolltilldelningar. Du kan tilldela dessa roller till användare, grupper tjänstens huvud namn och hanterade identiteter. Det finns fördefinierade inbyggda roller för vissa resurser och dessa roller kan inventeras eller frågas via verktyg som Azure CLI, Azure PowerShell eller Azure-portalen. De behörigheter som du tilldelar resurser via Azure RBAC bör alltid vara begränsade till vad som krävs av rollerna. Det här är ett komplement till JIT-metoden (just in time) för Azure AD Privileged Identity Management (PIM) och bör granskas regelbundet.

Använd inbyggda roller för att allokera behörighet och skapa bara en anpassad roll vid behov.

- [Vad är Azure rollbaserad åtkomst kontroll (Azure RBAC)](../role-based-access-control/overview.md) 

- [Så här konfigurerar du RBAC i Azure](../role-based-access-control/role-assignments-portal.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Benchmark för Azure-säkerhet: Dataskydd](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Skydda känsliga data

**Vägledning**: skydda känsliga data som konfigurations data för Azure Firewall-principen genom att begränsa åtkomsten med hjälp av Azure Role-baserad Access Control (Azure RBAC), nätverksbaserade åtkomst kontroller och vissa kontroller i Azure-tjänster.

För att säkerställa konsekvent åtkomstkontroll bör alla typer av åtkomstkontroll justeras mot din strategi för företagssegmentering. Strategin för företagssegmentering bör också informeras om platsen för känsliga eller affärskritiska data och system.

När det gäller den underliggande plattformen, som hanteras av Microsoft, behandlar Microsoft allt kundinnehåll som känsligt och skyddar det mot förlust och exponering av kunddata. För att säkerställa att kunddata i Azure alltid är skyddade har Microsoft implementerat vissa standardkontroller och funktioner för dataskydd.

- [Rollbaserad åtkomstkontroll (RBAC) i Azure](../role-based-access-control/overview.md)

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Delad

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: Övervaka för obehörig överföring av känsliga data

**Vägledning**: Azure Firewall policy-resurser kan endast nås av autentiserade användare. Kunderna måste se till att endast behöriga användare har åtkomst till data.

- [Skapa anpassade roller för åtkomst till regel samlings grupper](rule-hierarchy.md#create-custom-roles-to-access-the-rule-collection-groups)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: kryptera känslig information under överföring

**Vägledning**: för att komplettera åtkomst kontroller bör data i överföring skyddas mot "out of band"-attacker (till exempel trafik avbildningar) med hjälp av kryptering för att säkerställa att angripare inte kan läsa eller ändra data.

Azure Firewall Manager stöder data kryptering i överföring med TLS v 1.2 eller senare.

Även om det är valfritt för trafik i privata nätverk, är detta avgörande för trafik på externa och offentliga nätverk. För HTTP-trafik ser du till att alla klienter som ansluter till dina Azure-resurser kan förhandla TLS v 1.2 eller senare. För fjärrhantering använder du SSH (för Linux) eller RDP/TLS (för Windows) i stället för ett okrypterat protokoll. Inaktuella SSL-, TLS-och SSH-versioner och-protokoll och svaga chiffer bör inaktive ras.

Som standard tillhandahåller Azure kryptering för data i överföring mellan Azures Data Center.

- [Förstå kryptering i överföring med Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

- [Information om TLS-säkerhet](/security/engineering/solving-tls1-problem) 

- [Double Encryption för Azure-data under överföring](../security/fundamentals/double-encryption.md#data-in-transit)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Delad

## <a name="asset-management"></a>Tillgångshantering

*Mer information finns i [Benchmark för Azure-säkerhet: Tillgångshantering](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Se till att säkerhetsteamet har insyn i risker för tillgångar

**Riktlinjer**: Se till att säkerhetsteamen beviljas behörigheter för säkerhetsläsare i din Azure-klient och prenumerationer som gör att de kan övervaka säkerhetsrisker med hjälp av Azure Security Center. 

Beroende på hur säkerhetsteamets ansvarsområden är strukturerade kan ansvaret för övervakning av säkerhetsrisker ligga hos en central säkerhetsgrupp eller ett lokalt team. Detta innebär att säkerhetsinsikter och -risker alltid måste samlas centralt inom en organisation. 

Behörigheter för säkerhetsläsare kan tillämpas brett över en hel klientorganisation (rothanteringsgrupp) eller omfattas av hanteringsgrupper eller specifika prenumerationer. 

Ytterligare behörigheter kan krävas för att få insyn i arbets belastningar och tjänster. 

- [Översikt över säkerhetsläsarrollen](../role-based-access-control/built-in-roles.md#security-reader)

- [Översikt över Azure-hanteringsgrupper](../governance/management-groups/overview.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: se till att säkerhets teamet har åtkomst till till gångs inventering och metadata

**Vägledning**: se till att säkerhets teamen har åtkomst till en kontinuerligt uppdaterad inventering av Azure Firewall Manager-tillgångar i Azure. De kan använda Azure Resource Graph för att fråga och identifiera alla Azure Firewall-resurser i dina prenumerationer, inklusive Azure-tjänster, program och nätverks resurser.

Använd taggar för dina Azure-resurser, resurs grupper och prenumerationer för att logiskt organisera dem i en taxonomi. Varje tagg består av ett namn och ett värde-par. Du kan till exempel använda namnet ”Miljö” och värdet ”Produktion” för alla resurser i produktionsmiljön.

- [Så här skapar du frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) 

- [Azure Security Center till gångs lager hantering](../security-center/asset-inventory.md) 

- [Mer information om att tagga till gångar finns i besluts guiden för resurs namn givning och taggning](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att granska och begränsa vilka tjänster som användare kan etablera i din miljö, däribland att kunna tillåta eller neka distributioner av Azure Firewall-resurser. Använd Azure Resource Graph för att fråga efter och identifiera resurser i prenumerationerna. Du kan också använda Azure Monitor för att skapa regler för att utlösa aviseringar när en icke-godkänd tjänst upptäcks.

- [Så här konfigurerar och hanterar du Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Så här nekar du en speciell resurs typ med Azure Policy](../governance/policy/samples/built-in-policies.md#general) 

- [Så här skapar du frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Garantera säker livscykelhantering för tillgångar

**Vägledning**: ta bort Azure Firewall Manager-resurser när de inte längre behövs för att minimera attack ytan. Användare kan hantera sina Azure Firewall Manager-resurser via Azure Portal-, CLI-eller REST-API: er.

- [CLI för Azure Firewall-princip](https://docs.microsoft.com/cli/azure/ext/azure-firewall/network/firewall/policy?view=azure-cli-latest&amp;preserve-view=true)

- [Azure Network CLI](https://docs.microsoft.com/powershell/module/az.network/?view=azps-5.1.0#networking&amp;preserve-view=true)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Azure Firewall Manager är integrerat med Azure Active Directory (Azure AD) för identitet och autentisering. Du kan använda villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azures resurser Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azures resurs hanterare](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

## <a name="logging-and-threat-detection"></a>Loggning och hotidentifiering

*Mer information finns i [Benchmark för Azure-säkerhet: Loggning och hotidentifiering](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Aktivera hot identifiering för Azure-resurser

**Vägledning**: vanliga aktivitets loggar som skapats av eller som relateras till brand Väggs principen till din Siem som kan användas för att konfigurera anpassade hot identifieringar. Se till att du övervakar olika typer av Azure-tillgångar för potentiella hot och avvikelser. Fokusera på att få aviseringar med hög kvalitet för att minska antalet falska positiva identifieringar för analytiker som ska sorteras. Aviseringar kan hämtas från loggdata, agenter eller andra data.

- [Skapa anpassade analys regler för att identifiera hot](../sentinel/tutorial-detect-threats-custom.md) 

- [Cyberhot Hot information med Azure Sentinel](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

- [Azure Firewall-loggar och mått](../firewall/firewall-diagnostics.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Aktivera hotidentifiering för identitets- och åtkomsthantering i Azure

**Riktlinjer**: Azure AD innehåller följande användarloggar som kan visas i Azure AD- rapportering eller integreras med Azure Monitor, Azure Sentinel eller andra SIEM/övervakningsverktyg för mer avancerade användningsfall med övervakning och analys:
- Inloggningsaktiviteter – Rapporten för inloggningsaktiviteter ger information om användningen av hanterade program och användares inloggningsaktiviteter.

- Granskningsloggar – Ger spårbarhet via loggar för alla ändringar som gjorts via olika funktioner i Azure AD. Exempel på granskningsloggar är de resursändringar som görs i Azure AD, som att lägga till eller ta bort användare, appar, grupper, roller och principer.
- Riskfyllda inloggningar – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare.
- Användare som har flaggats för risk – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats.

Azure Security Center kan också avisera om vissa misstänkta aktiviteter, till exempel ett högt antal misslyckade autentiseringsförsök eller föråldrade konton i prenumerationen. Utöver den grundläggande övervakningen av säkerhetsrutiner kan hotskyddsmodulen i Azure Security Center även samla in mer detaljerade säkerhetsaviseringar från enskilda Azure Compute-resurser (virtuella datorer, containrar, App Service), data resurser (SQL DB och lagring) och lager i Azure-tjänsten. Den här funktionen ger dig insyn i konto avvikelser i de enskilda resurserna.

För närvarande fungerar åtgärder som omgivande brand Väggs princip regel samlings grupper inte stöds av aktivitets loggen, det här är ett känt problem och behandlas i framtida uppdateringar.

- [Granska aktivitetsrapporter i Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Aktivera Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Skydd mot hot i Azure Security Center](/azure/security-center/threat-protection)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Aktivera loggning för Azure-resurser

**Vägledning**: aktivitets loggar, som är automatiskt tillgängliga, innehåller alla Skriv åtgärder (placera, skicka, ta bort) för brand Väggs principens resurser förutom Läs åtgärder (get). Aktivitets loggar kan användas för att hitta ett fel när du felsöker eller övervakar hur en användare i organisationen ändrade en resurs.

För närvarande fungerar åtgärder som omgivande brand Väggs princip regel samlings grupper inte stöds av aktivitets loggen, det här är ett känt problem och behandlas i framtida uppdateringar.

- [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Förstå loggning och olika logg typer i Azure](../azure-monitor/platform/platform-logs-overview.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: centralisera hantering av säkerhets loggar och analys

**Vägledning**: centralisera loggning av lagring och analys för att aktivera korrelation. För varje logg källa ser du till att du har tilldelat en data ägare, åtkomst vägledning, lagrings plats, vilka verktyg som används för att bearbeta och komma åt data samt krav för data lagring.

Se till att du integrerar Azures aktivitets loggar i den centrala loggningen. Mata in loggar via Azure Monitor för att samla in säkerhets data som genererats av slut punkts enheter, nätverks resurser och andra säkerhets system. I Azure Monitor använder du Log Analytics arbets ytor för att fråga och utföra analyser och använda Azure Storage konton för långsiktig lagring.

Du kan också aktivera och publicera dina loggdata till Azure Sentinel eller en SIEM från tredje part.

Många organisationer väljer att använda Azure Sentinel för "heta" data som används ofta och Azure Storage för "kall" data som används mindre ofta.

- [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurera kvarhållning av logg lagring

**Vägledning**: kontrol lera att lagrings konton eller Log Analytics arbets ytor som används för att lagra brand Väggs princip loggar har den logg kvarhållningsperiod som angetts i enlighet med organisationens regler för efterlevnad.

I Azure Monitor kan du Log Analytics ange arbets ytans lagrings period enligt organisationens regler för efterlevnad. Använd Azure Storage-, Data Lake-eller Log Analytics arbets ytans konton för långsiktig lagring och arkivering.

- [Så här konfigurerar du Log Analytics bevarande period för arbets ytor](../azure-monitor/platform/manage-cost-storage.md)

- [Lagra resurs loggar i ett Azure Storage konto](/azure/azure-monitor/platform/resource-logs-collect-storage)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Benchmark för Azure-säkerhet: Incidenthantering](/azure/security/benchmarks/security-controls-v2-incident-response).*

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

**Vägledning**: se till att du har en process för att skapa aviseringar med hög kvalitet och mäta kvaliteten på aviseringar. På så sätt kan du dra slutsatser från tidigare incidenter och prioritera aviseringar för analytiker, så att de inte slösar tid på falska positiva identifieringar. 

Aviseringar med hög kvalitet kan byggas utifrån erfarenhet från tidigare incidenter, validerade community-källor och verktyg som utformats för att skapa och rensa aviseringar från fusing och korrelera olika signal källor. 

Azure Security Center ger aviseringar med hög kvalitet över flera Azure-tillgångar. Du kan använda anslutningsprogrammet för ASC-data för att strömma aviseringarna till Azure Sentinel. Med Azure Sentinel kan du skapa avancerade aviseringsregler för att generera incidenter automatiskt för en undersökning. 

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

Azure Security Center tilldelar en allvarlighetsgrad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center är i sökningen eller analysen som används för att utfärda aviseringen, samt den konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Markera även resurser med taggar och skapa ett namngivningssystem för att identifiera och kategorisera Azure-resurser, i synnerhet sådana som används för bearbetning av känsliga data.  Det är ditt ansvar att prioritera åtgärdandet av aviseringar baserat på allvarlighetsgraden för de Azure-resurser och den miljö där incidenten inträffade.

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Använda taggar för att organisera dina Azure-resurser](/azure/azure-resource-manager/resource-group-using-tags)

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

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: upprätta säkra konfigurationer för Azure-tjänster 

**Vägledning**: automatisera och skydda distributionen och konfigurationen av Azure Firewall Manager-resurser i dina miljöer med hjälp av mekanismer som: Azure Resource Manager mallar, Azure RBAC-kontroller och Azure policy. Definiera säkra konfigurationer för Azure Firewall Manager-resurser vid distributions tillfället, granska och genomdriva dessa konfigurationer genom att definiera anpassade Azure Policy definitioner med hjälp av alias i namn området Azure. Network.

- [Referens för Azure Firewall-principmall](/azure/templates/microsoft.network/firewallpolicies)

- [CLI för Azure Firewall-princip](https://docs.microsoft.com/cli/azure/ext/azure-firewall/network/firewall/policy?view=azure-cli-latest&amp;preserve-view=true)

- [Bild av guardrails-implementering i netscale-landnings zon](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Skapa och hantera principer för att använda kompatibilitet](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: upprätthålla säkra konfigurationer för Azure-tjänster

**Vägledning**: Azure Firewall Manager stöder Azure Resource Manager baserade mallar och verk ställandet av konfigurations inställningar via Azure policy. Definiera anpassade Azure Policys definitioner som ska granskas och upprätthålla resurs konfigurationerna för Azure Firewall Manager med alias i namn området "Azure. Network".

- [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md)

- [Referens för Azure Firewall-principmall](/azure/templates/microsoft.network/firewallpolicies)

- [Bild av guardrails-implementering i netscale-landnings zon](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Skapa och hantera principer för att använda kompatibilitet](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: upprätta säkra konfigurationer för beräknings resurser

**Vägledning**: ej tillämpligt; Azure Firewall Manager är hanterings tjänsten för brand Väggs kontroll och visar inte den underliggande tjänstens beräknings infrastruktur för de kunder som ska konfigureras.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Genomför regelbunden attacksimulering

**Riktlinjer**: Genomför vid behov genomslagstestning eller Red Team-aktiviteter på dina Azure-resurser och se till att alla kritiska säkerhetsbrister åtgärdas.
Följ Microsoft Clouds deltagarregler för genomslagstestning för att se till att genomslagstesterna inte strider mot Microsofts principer. Använd Microsofts strategi för och verkställande av Red Team-indelning och genomslagstester för live-webbplatser mot Microsoft-hanterad molninfrastruktur, tjänster och program.

- [Genomslagstestning i Azure](../security/fundamentals/pen-testing.md)

- [Deltagarregler för genomslagstestning](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Red Team-indelning i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Delad

## <a name="backup-and-recovery"></a>Säkerhetskopiering och återställning

*Mer information finns i [säkerhets benchmark för Azure: säkerhets kopiering och återställning](/azure/security/benchmarks/security-controls-v2-backup-recovery).*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: Säkerställ vanliga automatiserade säkerhets kopieringar

**Vägledning**: Azure Firewall Manager har inte begreppet kunders system för säkerhets kopiering, den underliggande infrastrukturen hanteras av Microsoft.

För resurs konfigurations säkerhets kopieringar använder du Azure Resource Manager för att exportera brand Väggs principer och relaterade resurser i en JavaScript Object Notation (JSON)-mall som kan användas som en konfigurations säkerhets kopia. Du kan också exportera konfiguration av brand Väggs principer med hjälp av funktionen Exportera mall i Azure-brandväggen från Azure Portal. Använd Azure Automation för att köra anpassade skript för säkerhets kopiering för att avbilda resurs konfigurationerna för dina Azure Firewall Manager-resurser automatiskt.

- [Distribuera säker virtuell hubb med mall](quick-secure-virtual-hub.md)

- [Referens för Microsoft Firewall-principmall](/azure/templates/microsoft.network/firewallpolicies)

- [Om Azure Automation](../automation/automation-intro.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Delad

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: Azure Firewall Manager har inte begreppet kunder som skyddar systemets säkerhets kopieringar. För alla exporterade resurs mallar för Azure Firewall Manager utför regelbundet återställning med dessa Azure Resource Manager mallfiler.

- [Distribuera säker virtuell hubb med Azure Resource Manager mallar](quick-secure-virtual-hub.md)

- [Referens för Microsoft Firewall-principmall](/azure/templates/microsoft.network/firewallpolicies)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

## <a name="governance-and-strategy"></a>Styrning och strategi

*Mer information finns i [Benchmark för Azure-säkerhet: Styrning och strategi](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definiera strategin för tillgångshantering och dataskydd 

**Riktlinjer**: Se till att dokumentera och förmedla en tydlig strategi för kontinuerlig övervakning och skydd av system och data. Prioritera upptäckt, utvärdering, skydd och övervakning av affärskritiska data och system. 

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

-   Standard för klassificering av data i enlighet med affärsriskerna

-   Insyn för säkerhetsorganisationen i risker och tillgångsinventering 

-   Säkerhetsorganisations godkännande av Azure-tjänster att använda 

-   Säkerhet för tillgångar via deras livscykel

-   Strategi för nödvändig åtkomstkontroll i enlighet med organisationens dataklassificering

-   Användning av data skydds funktioner i Azure Native och tredje part

-   Krypteringskrav för användningsfall med data under transport och i vila

-   Lämpliga kryptografiska standarder

Mer information finns i följande resurser:
- [Rekommendation för Azure Security Architecture – lagring, data och kryptering](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Grundläggande Azure-säkerhet – säkerhet, kryptering och lagring av data i Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework – bästa praxis för datasäkerhet och kryptering i Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Benchmark för Azure-säkerhet – tillgångshantering](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Benchmark för Azure-säkerhet – dataskydd](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definiera strategi för företagssegmentering 

**Riktlinjer**: Upprätta en företagsomfattande strategi för att segmentera åtkomst till tillgångar med en kombination av identitet, nätverk, program, prenumeration, hanteringsgrupp och andra kontroller.

Du bör noggrant balansera behovet av säkerhetsseparation med behovet av att möjliggöra daglig drift av de system som behöver kommunicera med varandra och komma åt data.

Se till att segmenteringsstrategin implementeras konsekvent över olika kontrolltyper, inklusive nätverkssäkerhet, identitets- och åtkomstmodeller samt modeller för programbehörighet/-åtkomst och kontroller för mänskliga processer.

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

**Riktlinjer**: Upprätta en metod för Azure-identitet och priviligierad åtkomst som en del av organisationens övergripande strategi för säker åtkomstkontroll.  

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

-   Ett centraliserat identitets- och autentiseringssystem och dess anslutningar till andra interna och externa identitetssystem

-   Starka autentiseringsmetoder i olika användnings fall och förhållanden

-   Skydd av användare med höga privilegier

-   Övervakning och hantering av avvikande användaraktiviteter  

-   Process för granskning och avstämning av användaridentitet och -åtkomst

Mer information finns i följande resurser:

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

- Se [Översikt över Benchmark för Azure-säkerhet V2](/azure/security/benchmarks/overview)
- Läs mer om [säkerhetsbaslinjer för Azure](/azure/security/benchmarks/security-baselines-overview)
