---
title: Azures säkerhets bas linje för Azure Defender för IoT
description: Säkerhets bas linjen för Azure Defender för IoT ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: defender-for-iot
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ad10195c50d3de49ce89c3917ebac5ba76ca4194
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94974974"
---
# <a name="azure-security-baseline-for-azure-defender-for-iot"></a>Azures säkerhets bas linje för Azure Defender för IoT

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark-version 2,0](../security/benchmarks/overview.md) till Microsoft Azure Defender för IoT. Azures säkerhets benchmark ger rekommendationer om hur du kan skydda dina moln lösningar på Azure. Innehållet grupperas efter **säkerhets kontrollerna** som definieras av Azures säkerhets benchmark och relaterade rikt linjer som gäller för Azure Defender för IoT. **Kontroller** som inte är tillämpliga på Azure Defender för IoT har uteslutits.

Om du vill se hur Azure Defender för IoT fullständigt mappar till Azures säkerhets benchmark, se den [fullständiga mappnings filen för Azure Defender för IoT-säkerhet](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-management"></a>Identitetshantering

*Mer information finns i [Azure Security benchmark: Identity Management](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: standardisera Azure Active Directory som system för central identitets-och autentisering

**Vägledning**: Azure Defender för IoT är integrerat med Azure Active Directory (Azure AD), Azures standard tjänst för identitets-och åtkomst hantering. Du bör standardisera Azure AD för att styra organisationens identitets-och åtkomst hantering i:

- Microsoft Cloud resurser, till exempel Azure Portal, Azure Storage, virtuell Azure-dator (Linux och Windows), Azure Key Vault, PaaS och SaaS program.

- Organisationens resurser, till exempel program i Azure eller företagets nätverks resurser.

Att skydda Azure AD bör vara en hög prioritet i din organisations säkerhets praxis för molnet. Azure AD ger en identitets säker Poäng som hjälper dig att utvärdera position för identiteter i relation till Microsofts rekommendationer för bästa praxis. Använd poängen för att mäta hur nära konfigurationen matchar rekommendationerna för bästa praxis och för att göra förbättringar i din säkerhets position.

Azure AD stöder extern identitet som gör det möjligt för användare utan Microsoft-konto att logga in på sina program och resurser med sin externa identitet.

- [Innehav i Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Så här skapar och konfigurerar du en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Använda externa identitets leverantörer för program](/azure/active-directory/b2b/identity-providers) 

- [Vad är säkerhets poängen för identiteter i Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Använd enkel inloggning för Azure AD (SSO) för program åtkomst

**Vägledning**: Azure Defender för IoT använder Azure Active Directory för att tillhandahålla identitets-och åtkomst hantering för Azure-resurser, moln program och lokala program. Detta inkluderar företags identiteter som anställda, samt externa identiteter som partner, leverantörer och leverantörer. Detta möjliggör enkel inloggning (SSO) för att hantera och skydda åtkomsten till din organisations data och resurser lokalt och i molnet. Anslut alla användare, program och enheter till Azure AD för sömlös, säker åtkomst och bättre synlighet och kontroll.

- [Förstå Application SSO med Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Använd starka verifierings kontroller för alla Azure Active Directory-baserade åtkomst

**Vägledning**: Azure Defender för IoT använder Azure Active Directory som stöder starka verifierings kontroller med Multi-Factor Authentication (MFA), och starka metoder för lösen ords kryptering.

- Multi-Factor Authentication – aktivera Azure AD MFA och följ Azure Security Center rekommendationer för identitets-och åtkomst hantering för några rekommenderade metoder i MFA-installationen. MFA kan tillämpas på alla, välja användare eller på nivån per användare baserat på inloggnings villkor och riskfaktorer.
- Lösen ords kryptering – tre alternativ för lösen ords kryptering är tillgängliga: Windows Hello för företag, Microsoft Authenticator app och lokala autentiseringsmetoder som smartkort.

För administratörer och privilegierade användare bör du se till att den högsta nivån för metoden stark autentisering används, följt av lämplig princip för stark autentisering för andra användare.

- [Så här aktiverar du MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Introduktion till alternativ för lösen ords lös autentisering för Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md) 

- [Standard lösen ords princip för Azure AD](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Eliminera Felaktiga lösen ord med Azure AD Password Protection](../active-directory/authentication/concept-password-ban-bad.md)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: övervaka och Varna vid konto avvikelser

**Vägledning**: Azure Defender för IoT är integrerat med Azure Active Directory som tillhandahåller följande data Källor:

Inloggningar – rapporten inloggningar innehåller information om användningen av hanterade program och användar inloggnings aktiviteter.

Granskningsloggar – Ger spårbarhet via loggar för alla ändringar som gjorts via olika funktioner i Azure AD. Exempel på gransknings loggar är ändringar som har gjorts i alla resurser i Azure AD, till exempel att lägga till eller ta bort användare, appar, grupper, roller och principer.

Riskfyllda inloggningar – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare.

Användare som har flaggats för risk – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats.

Dessa data källor kan integreras med Azure Monitor, Azure Sentinel eller SIEM system från tredje part.

Azure Security Center kan också Avisera om vissa misstänkta aktiviteter, till exempel ett högt antal misslyckade autentiseringsförsök, föråldrade konton i prenumerationen.

Azure Advanced Threat Protection (ATP) är en säkerhetslösning som kan använda Active Directory signaler för att identifiera, identifiera och undersöka avancerade hot, komprometterade identiteter och skadliga Insider-åtgärder.

- [Granska aktivitets rapporter i Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Visa Azure AD-riskfyllda inloggningar](/azure/active-directory/reports-monitoring/concept-risky-sign-ins) 

- [Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet](/azure/active-directory/reports-monitoring/concept-user-at-risk) 

- [Övervaka användarnas identitets-och åtkomst aktiviteter i Azure Security Center](../security-center/security-center-identity-access.md) 

- [Aviseringar i Azure Security Center hot Intelligence Protection-modulen](../security-center/alerts-reference.md) 

- [Så här integrerar du Azures aktivitets loggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

## <a name="privileged-access"></a>Privilegie rad åtkomst

*Mer information finns i [Azure Security benchmark: privilegie rad åtkomst](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: begränsa administrativ åtkomst till verksamhets kritiska system

**Vägledning**: Azure Defender för IoT använder Azure RBAC för att isolera åtkomst till affärs kritiska system genom att begränsa vilka konton som har privilegie rad åtkomst till de prenumerationer och hanterings grupper som de finns i.

Se till att du också begränsar åtkomsten till hanterings-, identitets-och säkerhets system som har administrativ åtkomst till din affärs kritiska åtkomst, till exempel Active Directory-domän controllers (DCs), säkerhets verktyg och system hanterings verktyg med agenter som är installerade på affärs kritiska system. Angripare som angriper dessa hanterings-och säkerhets system kan omedelbart weaponize dem för att kompromettera affärs kritiska till gångar.

Alla typer av åtkomst kontroller bör justeras mot din strategi för företags segmentering för att säkerställa en konsekvent åtkomst kontroll.

- [Azure-komponenter och referens modell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Åtkomst till hanterings grupp](../governance/management-groups/overview.md#management-group-access) 

- [Administratörer för Azure-prenumeration](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Konfigurera nöd åtkomst i Azure AD

**Vägledning**: Azure Defender för IoT är integrerat med Azure Active Directory för att hantera sina resurser. För att förhindra oavsiktligt låst av din Azure AD-organisation konfigurerar du ett konto för nöd åtkomst för åtkomst när normala administrativa konton inte kan användas. Konton för nöd åtkomst är ofta mycket privilegierade och de bör inte tilldelas vissa individer. Konton för nöd åtkomst är begränsade till nöd situationer eller "Break glas"-scenarier där normala administrativa konton inte kan användas.

Du bör se till att autentiseringsuppgifterna (till exempel lösen ord, certifikat eller smartkort) för konton för nöd åtkomst hålls skyddade och endast kända för personer som har behörighet att använda dem endast i nödfall.

- [Hantera konton för nöd åtkomst i Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatisera rättighets hantering 

**Vägledning**: Azure Defender för IoT är integrerat med Azure Active Directory för att hantera sina resurser. Använd funktioner för hantering av Azure AD-rättighet för att automatisera åtkomst till begär ande arbets flöden, inklusive åtkomst tilldelningar, recensioner och förfallo datum. Det finns också stöd för godkännande med dubbla eller flera steg.

- [Vad är åtkomst granskningar för Azure AD](../active-directory/governance/access-reviews-overview.md) 

- [Vad är hantering av Azure AD-berättigande](../active-directory/governance/entitlement-management-overview.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Följ bara tillräckligt med administration (princip för minsta behörighet) 

**Vägledning**: Azure Defender för IoT är integrerat med rollbaserad åtkomst kontroll (RBAC) i Azure för att hantera dess resurser. Med Azure RBAC kan du hantera åtkomst till Azure-resurser via roll tilldelningar. Du kan tilldela dessa roller till användare, grupper tjänstens huvud namn och hanterade identiteter. Det finns fördefinierade inbyggda roller för vissa resurser och dessa roller kan inventeras eller frågas via verktyg som Azure CLI, Azure PowerShell eller Azure Portal. De behörigheter som du tilldelar resurser via Azure RBAC bör alltid vara begränsade till vad som krävs av rollerna. Detta kompletterar JIT-metoden (just in Time) för Azure AD Privileged Identity Management (PIM) och bör granskas regelbundet.

Använd inbyggda roller för att allokera behörighet och endast skapa en anpassad roll vid behov.

- [Vad är Azure rollbaserad åtkomst kontroll (Azure RBAC)](../role-based-access-control/overview.md) 

- [Konfigurera RBAC i Azure](../role-based-access-control/role-assignments-portal.md) 

- [Använda Azure AD-identitets-och åtkomst granskningar](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security benchmark: Data Protection](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: identifiering, klassificera och märk känsliga data

**Vägledning**: identifiera, klassificera och märk känsliga data så att du kan utforma lämpliga kontroller för att se till att känslig information lagras, bearbetas och överförs säkert av organisationens teknik system. 

Använd Azure Information Protection (och dess associerade skannings verktyg) för känslig information i Office-dokument på Azure, lokalt, på Office 365 och på andra platser. 

Du kan använda Azure SQL-Information Protection för att hjälpa till med klassificering och märkning av information som lagras i Azure SQL-databaser.

- [Tagga känslig information med hjälp av Azure Information Protection](/azure/information-protection/what-is-information-protection) 

- [Implementera identifiering av Azure SQL-data](/azure/sql-database/sql-database-data-discovery-and-classification)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="dp-2-protect-sensitive-data"></a>DP-2: skydda känsliga data

**Vägledning**: skydda känsliga data genom att begränsa åtkomsten med hjälp av Azure Role-baserad Access Control (Azure RBAC), nätverksbaserade åtkomst kontroller och vissa kontroller i Azure-tjänster (till exempel kryptering i SQL och andra databaser). 

För att säkerställa konsekvent åtkomst kontroll bör alla typer av åtkomst kontroll justeras mot din strategi för företags segmentering. Strategin för företags segmentering bör också informeras om platsen för känsliga eller affärs kritiska data och system.

För den underliggande plattformen, som hanteras av Microsoft, behandlar Microsoft allt kund innehåll som känsligt och skyddar mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat vissa standard kontroller och funktioner för data skydd.

- [Rollbaserad Access Control i Azure (RBAC)](../role-based-access-control/overview.md)

- [Förstå kundens data skydd i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: kryptera känslig information under överföring

**Vägledning**: för att komplettera åtkomst kontroller bör data i överföring skyddas mot "out of band"-attacker (t. ex. trafik insamling) som använder kryptering för att säkerställa att angripare inte kan läsa eller ändra data. 

Även om det är valfritt för trafik i privata nätverk, är detta avgörande för trafik på externa och offentliga nätverk. För HTTP-trafik ser du till att alla klienter som ansluter till dina Azure-resurser kan förhandla TLS v 1.2 eller senare. För fjärrhantering använder du SSH (för Linux) eller RDP/TLS (för Windows) i stället för ett okrypterat protokoll. Inaktuella SSL-, TLS-och SSH-versioner och-protokoll och svaga chiffer bör inaktive ras.  

Som standard tillhandahåller Azure kryptering för data i överföring mellan Azures Data Center. 

- [Förstå kryptering i överföring med Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Information om TLS-säkerhet](/security/engineering/solving-tls1-problem)

- [Double Encryption för Azure-data under överföring](../security/fundamentals/double-encryption.md#data-in-transit)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

## <a name="asset-management"></a>Tillgångshantering

*Mer information finns i [Azure Security benchmark: Asset Management](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: se till att säkerhets teamet har insyn i risker för till gångar

**Vägledning**: se till att säkerhets team beviljas behörigheter för säkerhets läsare i din Azure-klient och prenumerationer så att de kan övervaka säkerhets risker med hjälp av Azure Security Center. 

Beroende på hur säkerhets teamets ansvars områden är strukturerade kan övervakning av säkerhets risker vara ansvaret för en central säkerhets grupp eller ett lokalt team. Detta innebär att säkerhets insikter och risker alltid måste aggregeras centralt inom en organisation. 

Behörigheter för säkerhets läsare kan tillämpas i stort sett till en hel klient organisation (rot hanterings grupp) eller omfattas av hanterings grupper eller vissa prenumerationer. 

Ytterligare behörigheter kan krävas för att få insyn i arbets belastningar och tjänster. 

- [Översikt över rollen säkerhets läsare](../role-based-access-control/built-in-roles.md#security-reader)

- [Översikt över Azure Hanteringsgrupper](../governance/management-groups/overview.md)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att granska och begränsa vilka tjänster som användare kan etablera i din miljö. Använd Azure Resource Graph för att fråga efter och identifiera resurser i prenumerationerna. Du kan också använda Azure Monitor för att skapa regler för att utlösa aviseringar när en icke-godkänd tjänst upptäcks.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en speciell resurs typ med Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Så här skapar du frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: se till att till gångens livs cykel hantering är säker

**Vägledning**: upprätta eller uppdatera säkerhets principer som behandlar hanterings processer för till gångs livs cykeln för potentiellt höga konsekvenser. Dessa ändringar innehåller ändringar av: identitets leverantörer och åtkomst, data känslighet, nätverks konfiguration och tilldelning av administrativa privilegier.

Ta bort Azure-resurser när de inte längre behövs.

- [Ta bort resurs grupp och resurs för Azure](../azure-resource-manager/management/delete-resource-group.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Använd villkorlig åtkomst i Azure AD för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azures resurs hanterare](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security benchmark: incident svar](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: förberedelse – uppdatera incident svars process för Azure

**Vägledning**: se till att din organisation har processer som svarar på säkerhets incidenter, har uppdaterat de här processerna för Azure och utnyttjar dem regelbundet för att säkerställa beredskap.

- [Implementera säkerhet i företags miljön](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Referens guide för incident svar](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: förberedelse – installations incident meddelande

**Vägledning**: Konfigurera säkerhets incident kontakt information i Azure Security Center. Den här kontakt informationen används av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har öppnats av en olagligt eller obehörig part. Du har också alternativ för att anpassa incident aviseringar och meddelanden i olika Azure-tjänster baserat på dina incident svars behov. 

- [Så här ställer du in Azure Security Center säkerhets kontakt](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: identifiering och analys – skapa incidenter baserat på aviseringar med hög kvalitet 

**Vägledning**: se till att du har en process för att skapa aviseringar med hög kvalitet och mäta kvaliteten på aviseringar. På så sätt kan du lära dig lektioner från tidigare incidenter och prioritera aviseringar för analytiker, så att de inte slösar tid på falska positiva identifieringar. 

Aviseringar med hög kvalitet kan byggas utifrån erfarenhet från tidigare incidenter, validerade community-källor och verktyg som utformats för att skapa och rensa aviseringar från fusing och korrelera olika signal källor. 

Azure Security Center ger aviseringar med hög kvalitet över flera Azure-tillgångar. Du kan använda ASC data Connector för att strömma aviseringarna till Azure Sentinel. Med Azure Sentinel kan du skapa avancerade aviserings regler för att generera incidenter automatiskt för en undersökning. 

Exportera dina Azure Security Center-aviseringar och rekommendationer med hjälp av export funktionen för att identifiera risker med Azure-resurser. Exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö.

- [Så här konfigurerar du export](../security-center/continuous-export.md)

- [Strömma aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: identifiering och analys – Undersök en incident

**Vägledning**: se till att analytiker kan fråga och använda olika data källor när de undersöker potentiella incidenter, för att bygga en fullständig vy över vad som hände. Diverse loggar bör samlas in för att spåra aktiviteter för en potentiell angripare över Kill-kedjan för att undvika att fläckar.  Du bör också se till att insikter och kunskaper fångas upp för andra analytiker och för framtida historiska referenser.  

Data källorna för undersökningen innehåller de centraliserade loggnings källor som redan har samlats in från tjänster som omfattas och som kör system, men kan även innehålla:

- Nätverks data – Använd Nätverks säkerhets grupper flödes loggar, Azure Network Watcher och Azure Monitor för att avbilda nätverks flödes loggar och annan analys information. 

- Ögonblicks bilder av system som körs: 

    - Använd ögonblicks bild funktionen i Azure Virtual Machine för att skapa en ögonblicks bild av den aktiva system disken. 

    - Använd operativ systemets interna minnes dumpnings funktion för att skapa en ögonblicks bild av det aktiva systemets minne.

    - Använd ögonblicks bild funktionen i Azure-tjänsterna eller din program varu funktion för att skapa ögonblicks bilder av de system som körs.

Azure Sentinel tillhandahåller omfattande data analyser i praktiskt taget alla logg källor och en ärende hanterings Portal för att hantera den fullständiga livs cykeln för incidenter. Informations information under en undersökning kan associeras med en incident för spårning och rapportering. 

- [Ögonblicks bild av en Windows-dators disk](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Ögonblicks bild av en Linux-dators disk](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure stöd för diagnostikinformation och insamling av minnes dum par](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Undersök incidenter med Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: identifiering och analys – prioritera incidenter

**Vägledning**: Tillhandahåll sammanhang för att analysera vilka incidenter som ska fokusera på den första baserat på aviseringens allvarlighets grad och till gångens känslighet. 

Azure Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig vid sökning eller analys som används för att utfärda aviseringen, samt den konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Markera dessutom resurser med taggar och skapa ett namngivnings system för att identifiera och kategorisera Azure-resurser, särskilt för bearbetning av känsliga data.  Det är ditt ansvar att prioritera reparationen av aviseringar baserat på allvarlighets graden för de Azure-resurser och den miljö där incidenten inträffade.

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Använda taggar för att organisera dina Azure-resurser](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: inne slutning, utrotning och återställning – automatisera incident hanteringen

**Vägledning**: automatisera manuella återkommande uppgifter för att påskynda svars tiden och minska belastningen på analytiker. Manuella uppgifter tar längre tid att köra, vilket saktar ned varje incident och minskar hur många incidenter en analytiker kan hantera. Manuella uppgifter ökar också analytikerns utmattning, vilket ökar risken för mänskligt fel som orsakar fördröjningar och minskar risken för att analytikerna ska kunna fokusera på komplexa uppgifter på ett effektivt sätt. Använd automatiserings funktioner för arbets flöde i Azure Security Center och Azure Sentinel för att automatiskt utlösa åtgärder eller köra en Spelbok för att svara på inkommande säkerhets aviseringar. Spelbok tar åtgärder, till exempel att skicka meddelanden, inaktivera konton och isolera problematiska nätverk. 

- [Konfigurera automatisering av arbets flöde i Security Center](../security-center/workflow-automation.md)

- [Konfigurera automatiska hot svar i Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Konfigurera automatiska hot svar i Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

## <a name="posture-and-vulnerability-management"></a>Position och sårbarhets hantering

*Mer information finns i [Azure Security benchmark: position and sårbarhet Management](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: utför regelbunden attack simulering

**Vägledning**: vid behov kan du genomföra inträngande tester eller röda team aktiviteter på dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister.
Följ test reglerna för Microsoft Cloud inträngande för att se till att dina inträngande tester inte strider mot Microsofts principer. Använd Microsofts strategi och körning av röda team indelning och inträngande av direktsända webbplatser mot Microsoft-hanterad moln infrastruktur, tjänster och program.

- [Inträngande testning i Azure](../security/fundamentals/pen-testing.md)

- [Inträngande test regler för engagemang](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud röd team indelning](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: delat

## <a name="governance-and-strategy"></a>Styrning och strategi

*Mer information finns i [Azure Security benchmark: styrning och strategi](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: definiera strategi för till gångs hantering och data skydd 

**Vägledning**: se till att dokumentera och förmedla en tydlig strategi för kontinuerlig övervakning och skydd av system och data. Prioritera identifiering, utvärdering, skydd och övervakning av affärs kritiska data och system. 

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

-   Data klassificerings standard i enlighet med affärs riskerna

-   Insyn i säkerhets organisationen för risker och till gångs inventering 

-   Godkännande av säkerhets organisation för Azure-tjänster för användning 

-   Säkerhet för till gångar via deras livs cykel

-   Nödvändig åtkomst kontroll strategi i enlighet med organisationens data klassificering

-   Användning av data skydds funktioner i Azure Native och tredje part

-   Krav på data kryptering för överförings-och rest-användnings fall

-   Lämpliga kryptografiska standarder

Mer information finns i följande referenser:
- [Rekommendation för Azure Security Architecture – lagring, data och kryptering](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Grundläggande Azure-säkerhet – Azure Data Security, kryptering och lagring](../security/fundamentals/encryption-overview.md)

- [Ramverk för moln införande – metod tips för Azure Data Security och kryptering](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Hantering av Azure Security-inventarier](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Azure Security benchmark – data skydd](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: definiera strategi för företags segmentering 

**Vägledning**: upprätta en företagsomfattande strategi för att segmentera åtkomst till till gångar med en kombination av identitet, nätverk, program, prenumeration, hanterings grupp och andra kontroller.

Du bör noggrant balansera behovet av säkerhets separation med behovet av att möjliggöra daglig drift av de system som behöver kommunicera med varandra och komma åt data.

Se till att segmenterings strategin implementeras konsekvent över kontroll typer, inklusive nätverks säkerhet, identitets-och åtkomst modeller samt program behörighet/åtkomst modeller och kontroller för mänsklig process.

- [Vägledning om segment strategi i Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Vägledning om segment strategi i Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Justera nätverks segmentering med strategi för företags segmentering](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: definiera säkerhets position hanterings strategi

**Vägledning**: ständigt mäta och minimera risker för enskilda till gångar och miljön som de finns i. Prioritera höga värde till gångar och högexponerade attack ytor, till exempel publicerade program, ingångs-och utgångs punkter, användar-och administratörs slut punkter osv.

- [Azure Security benchmark – position och sårbarhets hantering](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: justera organisations roller, ansvars områden och Accountabilities

**Vägledning**: se till att dokumentera och förmedla en tydlig strategi för roller och ansvar i din säkerhets organisation. Prioritera att tillhandahålla tydliga ansvar för säkerhets beslut, utbilda alla på den delade ansvars modellen och utbilda tekniska team om teknik för att skydda molnet.

- [Bästa praxis för Azure-säkerhet 1 – personer: utbilda team om molnets säkerhets resa](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Bästa praxis för Azure-säkerhet 2 – personer: utbilda team om moln säkerhets teknik](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Bästa praxis för Azure Security 3 – process: tilldela ansvar för moln säkerhets beslut](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="gs-5-define-network-security-strategy"></a>GS-5: definiera nätverks säkerhets strategi

**Vägledning**: upprätta en Azure Network Security-metod som en del av din organisations övergripande strategi för säkerhets åtkomst kontroll.  

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

-   Centraliserad nätverks hantering och säkerhets ansvar

-   Modell för virtuella nätverks segment justerade med strategin för företags segmentering

-   Reparations strategi i olika hot och angrepps scenarier

-   Strategi för Internet Edge och inkommande och utgående

-   Hybrid moln och lokal strategi för samanslutning

-   Aktuella nätverks säkerhets artefakter (t. ex. nätverks diagram, referens nätverks arkitektur)

Mer information finns i följande referenser:
- [Azure-säkerhet Best Practice 11-Architecture. Enhetlig säkerhets strategi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Säkerhet för Azure-säkerhet](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Översikt över Azure Network Security](../security/fundamentals/network-overview.md)

- [Arkitektur strategi för företags nätverk](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: definiera identitets-och privilegie rad åtkomst strategi

**Vägledning**: upprätta en Azure Identity-och Privileged Access-metod som en del av din organisations övergripande strategi för säkerhets åtkomst kontroll.  

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

-   Ett centraliserat identitets-och autentiserings system och dess samanslutning med andra interna och externa identitets system

-   Starka autentiseringsmetoder i olika användnings fall och villkor

-   Skydd av privilegierade användare

-   Avvikande användar aktiviteter, övervakning och hantering  

-   Process för användar identitets-och åtkomst granskning och avstämning

Mer information finns i följande referenser:

- [Azure-säkerhet för prestandatest – identitets hantering](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure Security benchmark – privilegie rad åtkomst](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Azure-säkerhet Best Practice 11-Architecture. Enhetlig säkerhets strategi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Översikt över Azure Identity Management-säkerhet](../security/fundamentals/identity-management-overview.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: definiera strategi för loggning och hot svar

**Vägledning**: upprätta en strategi för loggning och hot svar för att snabbt upptäcka och åtgärda hot samtidigt som de uppfyller kraven för efterlevnad. Prioritera att ge analytiker med högkvalitativa varningar och sömlösa upplevelser så att de kan fokusera på hot i stället för integrering och manuella steg. 

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

-   Organisationens roll och ansvars områden för säkerhets åtgärder (SecOps) 

-   En väldefinierad incident svars process som justeras med NIST eller något annat bransch ramverk 

-   Insamling och kvarhållning av loggar för att stödja hot identifiering, incident svar och krav på efterlevnad

-   Centraliserad synlighet och korrelations information om hot, med SIEM, interna Azure-funktioner och andra källor 

-   Kommunikation och meddelande plan med dina kunder, leverantörer och offentliga intressen

-   Användning av inbyggda Azure-och tredjeparts-plattformar för incident hantering, till exempel loggning och hot identifiering, data utredning och angrepp och utrotning

-   Processer för hantering av incidenter och aktiviteter efter incidenter, till exempel lektioner som lärts och underlags kvarhållning

Mer information finns i följande referenser:

- [Azure-säkerhet för benchmark-loggning och hot identifiering](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure-säkerhet benchmark – incident svar](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure Security Best Practice 4 – process. Uppdatera incident svars processer för molnet](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Besluts guide för Azure införande Framework, loggning och rapportering](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure Enterprise Scale, Management och Monitoring](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="next-steps"></a>Nästa steg

- Se [Översikt över Azure Security benchmark v2](/azure/security/benchmarks/overview)
- Läs mer om [Azures säkerhets bas linjer](/azure/security/benchmarks/security-baselines-overview)
