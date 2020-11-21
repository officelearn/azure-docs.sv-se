---
title: Azures säkerhets bas linje för Azure App konfiguration
description: Säkerhets bas linjen för Azure App konfiguration ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 4d74c3607610372fa6cd276dd0d72fa5ba37f391
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025848"
---
# <a name="azure-security-baseline-for-azure-app-configuration"></a>Azures säkerhets bas linje för Azure App konfiguration

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark-version 2,0](../security/benchmarks/overview.md) för att Azure App konfiguration. Azures säkerhets benchmark ger rekommendationer om hur du kan skydda dina moln lösningar på Azure. Innehållet grupperas efter de **säkerhets kontroller** som definieras av Azures säkerhets benchmark och relaterade rikt linjer för Azure App konfiguration. **Kontroller** som inte gäller för Azure App konfiguration har uteslutits.

Om du vill se hur Azure App-konfigurationen är fullständigt mappad till Azures säkerhets benchmark, se den [fullständiga Azure App konfigurations filen för konfigurations säkerhets bas linje](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets principen för Azure-säkerhet: nätverks säkerhet](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementera säkerhet för intern trafik

**Vägledning**: Azure App konfiguration distribuerar inte några resurser direkt till ett virtuellt nätverk. Eftersom tjänsten inte har distribuerats till ett virtuellt nätverk kan du inte utnyttja vissa nätverksfunktioner för att skydda tjänstens interna trafik, t. ex. nätverks säkerhets grupper, väg tabeller eller andra nätverks enheter, till exempel en Azure-brandvägg. Med appens konfiguration kan du dock använda privata slut punkter för att ansluta säkert till Azure App konfiguration från ett virtuellt nätverk.

Använd Azure Sentinel för att identifiera användningen av äldre oskyddade protokoll som SSL/TLSv1, SMBv1, LM/NTLMv1, wDigest, osignerade LDAP-bindningar och svaga chiffer i Kerberos.

- [Använda privata slut punkter för Azure App konfiguration](concept-private-endpoint.md)

- [Arbets bok för Azure Sentinel-oskyddade protokoll](../sentinel/quickstart-get-visibility.md#use-built-in-workbooks)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: delat

### <a name="ns-2-connect-private-networks-together"></a>NS-2: Anslut privata nätverk till varandra

**Vägledning**: Azure App konfiguration stöder användning av privata slut punkter för att på ett säkert sätt skicka data via en privat länk. Använd Azure ExpressRoute eller Azure Virtual Private Network (VPN) för att skapa privata anslutningar mellan Azure-datacenter och lokal infrastruktur i en miljö med en valfri plats. ExpressRoute-anslutningar går inte via det offentliga Internet och de ger högre tillförlitlighet, snabbare hastigheter och lägre fördröjning än vanliga Internet anslutningar. För punkt-till-plats-VPN och plats-till-plats-VPN kan du ansluta lokala enheter eller nätverk till ett virtuellt nätverk med valfri kombination av de här VPN-alternativen och Azure-ExpressRoute.

Om du vill ansluta två eller flera virtuella nätverk i Azure tillsammans använder du peering för virtuella nätverk. Nätverks trafik mellan peer-kopplat virtuella nätverk är privat och lagras i Azures stamnät nätverk.

- [Vad är ExpressRoute-anslutnings modeller](../expressroute/expressroute-connectivity-models.md)

- [Översikt över Azure VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [Peering för virtuella nätverk](../virtual-network/virtual-network-peering-overview.md)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: upprätta privat nätverks åtkomst till Azure-tjänster

**Vägledning**: Använd Azures privata länk för att ge privat åtkomst till Azure App konfiguration från dina virtuella nätverk utan att passera Internet.

Privat åtkomst är ett ytterligare skydd i djupgående mått utöver autentisering och trafik säkerhet som erbjuds av Azure-tjänster.

- [Förstå privat Azure-länk](../private-link/private-link-overview.md)

- [Konfigurera en privat länk i Azure App konfiguration](concept-private-endpoint.md)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: skydda program och tjänster från externa nätverks attacker

**Vägledning**: när du har åtkomst till konfigurations värden via ett virtuellt nätverk skyddar du dina resurser mot attacker från externa nätverk, inklusive DDoS-attacker (distributed denial of Service), programspecifika attacker och oönskad och potentiellt skadlig Internet trafik. Använd Azure-brandväggen för att skydda program och tjänster mot potentiellt skadlig trafik från Internet och andra externa platser. Skydda dina till gångar mot DDoS-attacker genom att aktivera DDoS standard skydd på dina virtuella Azure-nätverk. Använd Azure Security Center för att identifiera problem med felaktig konfiguration som rör dina nätverks relaterade resurser.

Azure App-konfigurationen är inte avsedd att köra webb program, men den innehåller konfigurationen för dessa webb program. Du behöver inte konfigurera några ytterligare inställningar eller distribuera några extra nätverks tjänster för att skydda den från externa nätverks attacker som mål webb program.

- [Dokumentation om Azure Firewall](/azure/firewall/)

- [Hantera Azure DDoS Protection standard med hjälp av Azure Portal](/azure/virtual-network/manage-ddos-protection)

- [Rekommendationer för Azure Security Center](../security-center/recommendations-reference.md#recs-network)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: Distribuera intrångs identifiering/intrångs skydd system (ID/IP-adresser)

**Vägledning**: Använd Azure-brandvägg med hot information-baserad filtrering för att varna vid och/eller blockera trafik till och från kända skadliga IP-adresser och domäner. IP-adresserna och domänerna hämtas från Microsoft Threat Intelligence-flödet. När nytto lasten är nödvändig kan du distribuera en tredjeparts ID/IP-lösning från Azure Marketplace med funktioner för nytto Last kontroll. Alternativt kan du välja att använda värdbaserade ID: n/IP-adresser eller en värdbaserad lösning för slut punkts identifiering och-svar (EDR) tillsammans med eller i stället för nätverksbaserade ID: n/IP-adresser.

Obs! Om du har en regel eller annat krav för ID/IP-adresser kan du se till att det alltid är anpassat för att tillhandahålla aviseringar med hög kvalitet till din SIEM-lösning.

- [Så här distribuerar du Azure-brandvägg](/azure/firewall/tutorial-firewall-deploy-portal)

- [Azure Marketplace innehåller funktioner från tredje part-ID](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [Microsoft Defender ATP-EDR-kapacitet](/windows/security/threat-protection/microsoft-defender-atp/overviewendpoint-detection-response)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: förenkla nätverks säkerhets regler

**Vägledning**: använd Azure Virtual Network Service-taggar för att definiera nätverks åtkomst kontroller i nätverks säkerhets grupper eller Azure-brandvägg som kon figurer ATS för dina konfigurations resurser för appar. Du kan använda tjänst tag gen "AppConfiguration" i stället för specifika IP-adresser när du skapar säkerhets regler för utgående trafik i ditt program nätverk. Genom att ange namnet på service tag i ett lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

- [Förstå och använda service märken](../virtual-network/service-tags-overview.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="identity-management"></a>Identitetshantering

*Mer information finns i [Azure Security benchmark: Identity Management](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: standardisera Azure Active Directory som system för central identitets-och autentisering

**Vägledning**: Azure App konfiguration är integrerad med Azure Active Directory (Azure AD) som är Azures standard tjänst för identitets-och åtkomst hantering. Du bör standardisera Azure AD för att styra organisationens identitets-och åtkomst hantering i:
- Microsoft Cloud resurser, till exempel Azure Portal, Azure Storage, virtuell Azure-dator (Linux och Windows), Azure Key Vault, PaaS och SaaS program.
- Organisationens resurser, till exempel program i Azure eller företagets nätverks resurser.

Att skydda Azure AD bör vara en hög prioritet i din organisations säkerhets praxis för molnet. Azure AD ger en identitets säker Poäng som hjälper dig att utvärdera position för identiteter i relation till Microsofts rekommendationer för bästa praxis. Använd poängen för att mäta hur nära konfigurationen matchar rekommendationerna för bästa praxis och för att göra förbättringar i din säkerhets position.

Azure tillhandahåller följande inbyggda Azure-roller för att auktorisera åtkomst till konfigurations data för appar med hjälp av Azure AD och OAuth:

- Data ägare för app Configuration: Använd den här rollen för att ge Läs-/skriv-/borttagnings åtkomst till konfigurations data för appar. Detta ger inte åtkomst till appens konfigurations resurs.

- Konfigurations data läsare för appar: Använd den här rollen för att ge Läs åtkomst till konfigurations data för appar. Detta ger inte åtkomst till appens konfigurations resurs.

- Deltagare: Använd den här rollen för att hantera appens konfigurations resurs. Även om konfigurations data för appar kan nås med hjälp av åtkomst nycklar ger den här rollen ingen direkt åtkomst till data med hjälp av Azure AD.

- Läsare: Använd den här rollen för att ge Läs behörighet till appens konfigurations resurs. Detta ger inte åtkomst till resursens åtkomst nycklar eller data som är lagrade i app-konfigurationen.

Mer information finns i följande referenser:

- [Så här skapar och konfigurerar du en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Vad är säkerhets poängen för identiteter i Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

- [Ge åtkomst till Azure App konfiguration med hjälp av Azure AD](concept-enable-rbac.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: hantera program identiteter säkert och automatiskt

**Vägledning**: Använd Azure-hanterade identiteter för att få åtkomst till Azure App konfiguration från icke-mänskliga konton, till exempel andra Azure-tjänster. Vi rekommenderar att du använder Azure-hanterad identitets funktion i stället för att skapa ett mer kraftfullt humant konto för att få åtkomst till eller köra resurser för att begränsa behovet av att hantera ytterligare autentiseringsuppgifter. Azure App konfiguration kan också tilldelas en hanterad identitet i sig för att internt autentisera till andra Azure-tjänster/-resurser som stöder Azure AD-autentisering. Detta kan vara användbart för att ge enkel åtkomst från App-konfigurationen till Azure Key Vault när du hämtar hemligheter. När du använder hanterade identiteter hanteras identiteten av Azure-plattformen och du behöver inte tillhandahålla eller rotera några hemligheter.

Azure App-konfigurationen har stöd för att ditt program har beviljats två typer av identiteter:
- En systemtilldelad identitet är kopplad till konfigurations resursen. Den tas bort om konfigurations resursen tas bort. En konfigurations resurs kan bara ha en tilldelad identitet.
- En användardefinierad identitet är en fristående Azure-resurs som kan tilldelas till konfigurations resursen. En konfigurations resurs kan ha flera användare tilldelade identiteter.

När hanterade identiteter inte kan utnyttjas skapar du ett huvud namn för tjänsten med begränsad behörighet på Azure App konfigurations resurs nivå. Konfigurera tjänstens huvud namn med autentiseringsuppgifter för certifikatet och bara återgå till klient hemligheter. I båda fallen kan Azure Key Vault användas tillsammans med Azure Managed identiteter, så att körnings miljön (t. ex. en Azure-funktion) kan hämta autentiseringsuppgiften från nyckel valvet.

- [Använda hanterade identiteter för Azure App konfiguration](overview-managed-identity.md)

- [Hanterade Azure-identiteter](../active-directory/managed-identities-azure-resources/overview.md)

- [Tjänster som stöder hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Använda hanterade identiteter för att få åtkomst till App Configuration](howto-integrate-azure-managed-service-identity.md)

- [Huvudnamn för Azure-tjänsten](/powershell/azure/create-azure-service-principal-azureps) 

- [Skapa ett huvud namn för tjänsten med certifikat](../active-directory/develop/howto-authenticate-service-principal-powershell.md) 

- [Använd Azure Key Vault för registrering av säkerhets objekt](../key-vault/general/authentication.md#app-identity-and-security-principals)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Använd enkel inloggning för Azure AD (SSO) för program åtkomst

**Vägledning**: Azure App konfiguration använder Azure Active Directory (Azure AD) för att tillhandahålla identitets-och åtkomst hantering för Azure-resurser, moln program och lokala program. Detta inkluderar företags identiteter som anställda, samt externa identiteter som partner, leverantörer och leverantörer. Azure AD möjliggör enkel inloggning (SSO) för att hantera konfigurations tjänsten för appar via Azure Portal med hjälp av eventuella synkroniserade företags Active Directory identiteter. Anslut alla användare, program och enheter till Azure AD för sömlös, säker åtkomst och bättre synlighet och kontroll.

- [Förstå Application SSO med Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Använd starka verifierings kontroller för alla Azure Active Directory-baserade åtkomst

**Vägledning**: Azure App konfiguration använder Azure Active Directory som stöder starka verifierings kontroller med Multi-Factor Authentication (MFA) och starka metoder för lösen ords kryptering.
- Multi-Factor Authentication – aktivera Azure AD MFA och följ Azure Security Center rekommendationer för identitets-och åtkomst hantering för några rekommenderade metoder i MFA-installationen. MFA kan tillämpas på alla, välja användare eller på nivån per användare baserat på inloggnings villkor och riskfaktorer.
- Lösen ords kryptering – tre alternativ för lösen ords kryptering är tillgängliga: Windows Hello för företag, Microsoft Authenticator app och lokala autentiseringsmetoder som smartkort.

För administratörer och privilegierade användare ser du till att den högsta nivån av stark autentiseringsmetod används, följt av lämplig princip för stark autentisering för andra användare.

Obs! MFA kan tillämpas på de användar konton som har åtkomst till och hanterar konfiguration av appar, men inte på programmatiska tjänst konton. Använd lösenordsskyddad autentisering, till exempel hanterade identiteter där det är möjligt, och Använd MFA på alla användar konton.

- [Så här aktiverar du MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Introduktion till alternativ för lösen ords lös autentisering för Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: övervaka och Varna vid konto avvikelser

**Vägledning**: Azure App konfiguration är integrerad med Azure Active Directory i som tillhandahåller följande data Källor:

-   Inloggningar – inloggnings rapporten innehåller information om användningen av hanterade program och användar inloggnings aktiviteter.

-   Gransknings loggar – ger spårning genom loggar för alla ändringar som görs via olika funktioner i Azure AD. Exempel på gransknings loggar för loggade ändringar omfattar att lägga till eller ta bort användare, appar, grupper, roller och principer.

-   Riskfyllda inloggningar – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare.

-   Användare som har flaggats för risk – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats.

Dessa data källor kan integreras med Azure Monitor, Azure Sentinel eller SIEM system från tredje part.

Azure Security Center kan även Varna vid vissa misstänkta aktiviteter, till exempel ett högt antal misslyckade autentiseringsförsök och föråldrade konton i prenumerationen. 

Azure Advanced Threat Protection (ATP) är en säkerhetslösning som kan använda lokala Active Directory signaler för att identifiera, identifiera och undersöka avancerade hot, komprometterade identiteter och skadliga Insider-åtgärder.

- [Granska aktivitets rapporter i Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Visa Azure AD-riskfyllda inloggningar](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Övervaka användarnas identitets-och åtkomst aktiviteter i Azure Security Center](../security-center/security-center-identity-access.md)

- [Aviseringar i Azure Security Center hot Intelligence Protection-modulen](../security-center/alerts-reference.md)

- [Så här integrerar du Azures aktivitets loggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Anslut data från Azure AD Identity Protection](../sentinel/connect-azure-ad-identity-protection.md)

- [Azure Advanced Threat Protection](/azure-advanced-threat-protection/what-is-atp)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: begränsa åtkomsten till Azure-resursen baserat på villkor

**Vägledning**: Azure App konfiguration stöder Azure Active Directory (Azure AD) villkorlig åtkomst för en mer detaljerad åtkomst kontroll baserat på användardefinierade villkor, t. ex. användar inloggningar från vissa IP-intervall måste använda MFA för inloggning. Detaljerad hanterings princip för autentisering kan också användas för olika användnings fall. Dessa principer för villkorlig åtkomst gäller endast för användar konton som autentiserar till Azure AD för att få åtkomst till och hantera konfigurations tjänsten för appar, men de gäller inte för tjänstens huvud namn eller anslutnings strängar som ansluter till konfigurations resursen.

- [Översikt över villkorlig Azure-åtkomst](../active-directory/conditional-access/overview.md)

- [Vanliga principer för villkorlig åtkomst](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Konfigurera hantering av autentisering med villkorlig åtkomst](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: med Azure App-konfiguration kan kunder lagra konfigurationer som potentiellt kan innehålla identiteter eller hemligheter. Vi rekommenderar att du implementerar autentiseringsuppgifterna för autentisering för att identifiera autentiseringsuppgifter i konfigurationer. Den här skannern uppmuntrar också att flytta identifierade autentiseringsuppgifter till säkrare platser som Azure Key Vault.

Använd Azure App konfigurations tjänsten tillsammans med Azure Key Vault. Lagra autentiseringsuppgifter i Key Vault och länka sedan till dessa autentiseringsuppgifter genom att skapa en Key Vault referens i din app Configuration-resurs. När app-konfigurationen skapar dessa referenser lagrar den URI: erna för Key Vault värden i stället för själva värdena. Program kan ansluta till app-konfigurationen för att hämta autentiseringsuppgifter från Key Vault.

För GitHub kan du använda intern hemlighet-genomsökning för att identifiera autentiseringsuppgifter eller annan form av hemligheter i koden.

- [Självstudie för att använda Key Vault referenser i en ASP.NET Core-app](use-key-vault-references-dotnet-core.md)

- [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [GitHub Secret-genomsökning](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="privileged-access"></a>Privilegie rad åtkomst

*Mer information finns i [Azure Security benchmark: privilegie rad åtkomst](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: skydda och begränsa privilegierade användare

**Vägledning**: begränsa antalet privilegierade konton eller roller och skydda dessa konton på en utökad nivå eftersom användare med den här behörigheten kan direkt eller indirekt läsa och ändra alla resurser i din Azure-miljö.

Du kan aktivera just-in-Time (JIT) privilegie rad åtkomst till Azure-resurser och Azure AD med hjälp av Azure AD Privileged Identity Management (PIM). JIT beviljar temporära behörigheter för att utföra privilegierade uppgifter endast när användarna behöver det. PIM kan också generera säkerhets aviseringar när det finns misstänkt eller osäker aktivitet i din Azure AD-organisation.

Åtkomst nycklar är mycket privilegierade och bör roteras regelbundet som en säkerhets rutin. Åtkomst nycklar innehåller anslutnings strängar som innehåller autentiseringsinformation och betraktas som hemligheter. Dessa hemligheter måste lagras i Azure Key Vault och din kod måste autentiseras för att Key Vault ska kunna hämta dem. Åtkomst nycklar kan ge Läs-och Skriv behörighet till ett program. Kontrol lera att rätt typ av åtkomst nyckel har utfärdats för att förhindra obehörig åtkomst. För att vara säkrare använder du funktionen hanterade identiteter i Azure AD. Detta kräver bara att program har slut punkts konfigurations-URL för att få åtkomst till konfigurations värden.

- [Metod tips för app-konfiguration](howto-best-practices.md#app-configuration-bootstrap)

- [Använda hanterade identiteter för att få åtkomst till App Configuration](howto-integrate-azure-managed-service-identity.md)
- [Administratörs roll behörigheter i Azure AD](/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

- [Använd Azure Privileged Identity Management säkerhets aviseringar](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Skydda privilegierad åtkomst för hybrid- och molndistributioner i Azure AD](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: begränsa administrativ åtkomst till verksamhets kritiska system

**Vägledning**: Azure App konfiguration använder Azure RBAC för att isolera åtkomst till affärs kritiska system genom att begränsa vilka konton som beviljas privilegie rad åtkomst. Azure RBAC stöds av app-konfigurationen på resurs nivån. För att säkert silo affärs kritiska konfigurationer lagrar du den här informationen i en egen app Configuration-resurs. I en resurs är detaljerad åtkomst också tillgänglig via skrivskyddade åtkomst konton eller nycklar, samt etiketter och taggning.

Alla typer av åtkomst kontroller bör justeras mot din strategi för företags segmentering för att säkerställa en konsekvent åtkomst kontroll.

- [Åtkomst till hanterings grupp](../governance/management-groups/overview.md#management-group-access)

- [Administratörer för Azure-prenumeration](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [Integrera RBAC med Azure AD med app-konfiguration](concept-enable-rbac.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: granska och Stäm av användar åtkomst regelbundet

**Vägledning**: Azure App konfiguration använder Azure Active Directory-konton (Azure AD) för att hantera sina resurser, granska användar konton och åtkomst tilldelningen regelbundet för att säkerställa att kontona och deras åtkomst är giltiga. 

Azure tillhandahåller följande inbyggda Azure-roller för att auktorisera åtkomst till konfigurations data för appar med hjälp av Azure AD och OAuth:

- Data ägare för app Configuration: Använd den här rollen för att ge Läs-/skriv-/borttagnings åtkomst till konfigurations data för appar. Detta ger inte åtkomst till appens konfigurations resurs.

- Konfigurations data läsare för appar: Använd den här rollen för att ge Läs åtkomst till konfigurations data för appar. Detta ger inte åtkomst till appens konfigurations resurs

Du kan använda åtkomst granskningar i Azure AD för att granska grupp medlemskap, åtkomst till företags program och roll tilldelningar, till exempel rollerna för program konfiguration ovan. Azure AD repor ting kan tillhandahålla loggar för att identifiera inaktuella konton. Du kan också använda Azure AD Privileged Identity Management för att skapa åtkomst gransknings rapportens arbets flöde för att under lätta gransknings processen.

Obs! hanterade identiteter föreslås där det är möjligt att autentisera till app-konfigurationen från en annan tjänst eller ett annat program. Du måste hantera alla tjänst huvud namn eller anslutnings strängar som kon figurer ATS med åtkomst till app-konfigurationen separat när de används.

- [Skapa en åtkomst granskning av Azure Resource roles i Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Använda Azure AD-identitets-och åtkomst granskningar](/azure/active-directory/governance/access-reviews-overvie)

- [Ge åtkomst till Azure App konfiguration med hjälp av Azure AD](concept-enable-rbac.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Konfigurera nöd åtkomst i Azure AD

**Vägledning**: Azure App konfiguration är integrerad med Azure Active Directory för att hantera sina resurser. För att förhindra oavsiktligt låst av din Azure AD-organisation konfigurerar du ett konto för nöd åtkomst för åtkomst när normala administrativa konton inte kan användas. Konton för nöd åtkomst är ofta mycket privilegierade och de bör inte tilldelas vissa individer. Konton för nöd åtkomst är begränsade till nöd situationer eller "Break glas"-scenarier där normala administrativa konton inte kan användas.

Du bör se till att autentiseringsuppgifterna (till exempel lösen ord, certifikat eller smartkort) för konton för nöd åtkomst hålls skyddade och endast kända för personer som har behörighet att använda dem endast i nödfall.

- [Hantera konton för nöd åtkomst i Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatisera rättighets hantering 

**Vägledning**: Azure App konfiguration är integrerad med Azure Active Directory för att hantera sina resurser. Använd funktioner för hantering av Azure AD-rättighet för att automatisera åtkomst till begär ande arbets flöden, inklusive åtkomst tilldelningar, recensioner och förfallo datum. Det finns också stöd för godkännande med dubbla eller flera steg.

- [Vad är åtkomst granskningar för Azure AD](/azure/active-directory/governance/access-reviews-overview)

- [Vad är hantering av Azure AD-berättigande](../active-directory/governance/entitlement-management-overview.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Använd arbets stationer med privilegie rad åtkomst

**Vägledning**: skyddade, isolerade arbets stationer är mycket viktiga för att skydda känsliga roller som administratörer, utvecklare och kritiska tjänst operatörer. Använd hög säkra användar arbets stationer och/eller Azure-skydds för administrativa uppgifter relaterade till app-konfigurationen. Använd Azure Active Directory, Microsoft Defender Avancerat skydd (ATP) och/eller Microsoft Intune för att distribuera en säker och hanterad användar arbets station för administrativa uppgifter. De skyddade arbets stationerna kan hanteras centralt för att upprätthålla säker konfiguration, inklusive stark autentisering, program vara och maskin varu bas linjer, begränsad logisk och nätverks åtkomst.

- [Förstå arbets stationer med privilegie rad åtkomst](../active-directory/devices/concept-azure-managed-workstation.md) 

- [Distribuera en privilegie rad åtkomst arbets Station](../active-directory/devices/howto-azure-managed-workstation.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Följ bara tillräckligt med administration (princip för minsta behörighet) 

**Vägledning**: Azure App konfiguration är integrerad med rollbaserad åtkomst kontroll (RBAC) i Azure för att hantera dess resurser. Med Azure RBAC kan du hantera åtkomst till Azure-resurser via roll tilldelningar. Du kan tilldela dessa roller till användare, grupper tjänstens huvud namn och hanterade identiteter. Det finns fördefinierade inbyggda roller för Azure App konfiguration och dessa roller kan inventeras eller frågas via verktyg som Azure CLI, Azure PowerShell eller Azure Portal. De behörigheter som du tilldelar resurser via Azure RBAC bör alltid vara begränsade till vad som krävs av rollerna. Detta kompletterar JIT-metoden (just in Time) för Azure AD Privileged Identity Management (PIM) och bör granskas regelbundet.

Azure tillhandahåller följande inbyggda Azure-roller för att auktorisera åtkomst till konfigurations data för appar med hjälp av Azure AD och OAuth:
- Data ägare för app Configuration: Använd den här rollen för att ge Läs-/skriv-/borttagnings åtkomst till konfigurations data för appar. Detta ger inte åtkomst till appens konfigurations resurs.
- Konfigurations data läsare för appar: Använd den här rollen för att ge Läs åtkomst till konfigurations data för appar. Detta ger inte åtkomst till appens konfigurations resurs.

Använd inbyggda roller för att allokera behörighet och endast skapa anpassade roller vid behov. 

App-konfigurationen stöder lagring av konfiguration av flera program i en konfigurations resurs för en app. Om du vill begränsa informations åtkomsten mellan program skapar du en konfigurations resurs för appar per program och konfigurerar Azure RBAC därefter.

- [Vad är Azure rollbaserad åtkomst kontroll (Azure RBAC)](../role-based-access-control/overview.md)

- [Konfigurera RBAC i Azure](../role-based-access-control/role-assignments-portal.md)

- [Använda Azure AD-identitets-och åtkomst granskningar](/azure/active-directory/governance/access-reviews-overview)

- [Ge åtkomst till Azure App konfiguration med hjälp av Azure AD](concept-enable-rbac.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: Välj godkännande process för Microsoft Support  

**Vägledning**: implementera en organisations godkännande process för support scenarier där Microsoft kan behöva åtkomst till dina program konfigurations data. Customer Lockbox är för närvarande inte tillgängligt för support scenarier för program konfiguration.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security benchmark: Data Protection](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-1-discover-classify-and-label-sensitive-data"></a>DP-1: identifiera, klassificera och märk känsliga data

**Vägledning**: identifiera, klassificera och märk känsliga data så att du kan utforma lämpliga kontroller för att se till att känslig information lagras, bearbetas och överförs säkert av organisationens teknik system. Etiketter för känslig information i form av taggning stöds för konfigurations resurser för appar, men inte för konfigurations värden som finns i dem. När ett program har Läs-eller Läs-/Skriv behörighet till ett konfigurations lager har den fullständig åtkomst till alla konfigurationer i det arkivet.

- [Tagga känslig information med hjälp av Azure Information Protection](/azure/information-protection/what-is-information-protection)

- [Tagga data klassificeringar i Azure](/azure/cloud-adoption-framework/govern/policy-compliance/data-classification#tagging-data-classification-in-azure)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="dp-2-protect-sensitive-data"></a>DP-2: skydda känsliga data

**Vägledning**: för den underliggande plattformen, som hanteras av Microsoft, behandlar Microsoft allt kund innehåll som känsligt och skyddar mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat vissa standard kontroller och funktioner för data skydd. Se till att du regelbundet roterar åtkomst nycklarna till dina program konfigurations resurser. Autentiseringsinformation från anslutnings strängar kan lagras i Azure Key Vault och koden måste autentiseras för att Key Vault ska kunna hämta dem. Åtkomst nycklar kan ge Läs-och Skriv behörighet till ett program. Kontrol lera att rätt typ av åtkomst nyckel har utfärdats för att förhindra obehörig åtkomst. För att vara säkrare använder du funktionen hanterade identiteter i Azure AD. Detta kräver bara att program har slut punkts konfigurations-URL för att få åtkomst till konfigurations värden.

Begränsa åtkomst med hjälp av rollbaserad åtkomst kontroll i Azure (Azure RBAC):

- Separera känsliga data till en egen konfigurations resurs för appar och allokera sedan RBAC-principer enligt detta så att endast auktoriserad åtkomst har Aktiver ATS 

- Använda nätverksbaserade åtkomst kontroller

- Vissa kontroller i Azure-tjänster (t. ex. kryptering i SQL och andra databaser) och säkerställer konsekvent åtkomst kontroll bör alla typer av åtkomst kontroll justeras mot din strategi för företags segmentering.

- Strategin för företags segmentering bör också informeras om platsen för känsliga eller affärs kritiska data och system.

Mer information finns i följande referenser:

- [Ge åtkomst till Azure App konfiguration med Azure Active Directory](concept-enable-rbac.md)

- [Data kryptering för app-konfiguration](faq.md#does-app-configuration-encrypt-my-data)

- [Rollbaserad Access Control i Azure (RBAC)](../role-based-access-control/overview.md) 

- [Förstå kundens data skydd i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: delat

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: kryptera känslig information under överföring

**Vägledning**: för att komplettera åtkomst kontroller bör data i överföring skyddas mot "out of band"-attacker med kryptering. Detta säkerställer att angripare inte enkelt kan läsa eller ändra data.

Azure App konfiguration använder TLS-kryptering för alla HTTP-begäranden. Azure-infrastrukturen ger ett tillagt lager med kryptering på nätverks nivå för alla förfrågningar mellan Azure-datacenter. Se till att HTTP-trafik som alla klienter som ansluter till dina program konfigurations resurser kan förhandla TLS v 1.2 eller senare.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: delat

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Kryptera känsliga data i vila

**Vägledning**: för att komplettera åtkomst kontroller bör data i vila skyddas mot "out of band"-attacker (till exempel åtkomst till underliggande lagring) med hjälp av kryptering. Detta säkerställer att angripare inte enkelt kan läsa eller ändra data.

Azure tillhandahåller data i rest-kryptering som standard. För mycket känsliga data har du alternativ för att implementera ytterligare kryptering i vila på alla Azure-resurser där det är tillgängligt. Azure hanterar dina krypterings nycklar som standard, men Azure ger möjlighet att hantera dina egna nycklar (Kundhanterade nycklar) för Azure App konfiguration.

- [Använd kundens hanterade nycklar för att kryptera dina data i Azure App konfiguration](concept-customer-managed-keys.md)

- [Förstå kryptering i vila i Azure](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Krypterings modell och nyckel hanterings tabell](../security/fundamentals/encryption-atrest.md#azure-resource-providers-encryption-model-support)

- [Data i vila dubbel kryptering i Azure](../security/fundamentals/double-encryption.md#data-at-rest)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

## <a name="asset-management"></a>Tillgångshantering

*Mer information finns i [Azure Security benchmark: Asset Management](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: se till att säkerhets teamet har insyn i risker för till gångar

**Vägledning**: se till att säkerhets team beviljas behörigheter för säkerhets läsare i din Azure-klient och prenumerationer så att de kan övervaka säkerhets risker med hjälp av Azure Security Center. 

Beroende på hur säkerhets teamets ansvars områden är strukturerade kan övervakning av säkerhets risker vara ansvaret för en central säkerhets grupp eller ett lokalt team. Detta innebär att säkerhets insikter och risker alltid måste aggregeras centralt inom en organisation. 

Behörigheter för säkerhets läsare kan tillämpas i stort sett till en hel klient organisation (rot hanterings grupp) eller omfattas av hanterings grupper eller vissa prenumerationer. 

Obs: ytterligare behörigheter kan krävas för att få insyn i arbets belastningar och tjänster. 

- [Översikt över rollen säkerhets läsare](../role-based-access-control/built-in-roles.md#security-reader)

- [Översikt över Azure Hanteringsgrupper](../governance/management-groups/overview.md)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: se till att säkerhets teamet har åtkomst till till gångs inventering och metadata

**Vägledning**: se till att säkerhets teamen har åtkomst till en kontinuerligt uppdaterad inventering av till gångar på Azure, till exempel Azure App konfiguration. Säkerhets teamen behöver ofta den här inventeringen för att utvärdera organisationens potentiella exponering för nya risker och som informerar om löpande säkerhets förbättringar. Skapa en Azure Active Directory grupp som ska innehålla organisationens auktoriserade säkerhets team och tilldela dem Läs behörighet till alla Azure App konfigurations resurser. Detta kan för enklas med en enda roll tilldelning på hög nivå i din prenumeration.

Azure Security Center Inventory-funktionen och Azure Resource Graph kan fråga efter och identifiera alla resurser i dina prenumerationer, inklusive Azure-tjänster, program och nätverks resurser.

Använd taggar för dina Azure-resurser, resurs grupper och prenumerationer för att logiskt organisera dem i en taxonomi. Varje tagg består av ett namn och ett värde-par. Du kan till exempel använda namnet ”Miljö” och värdet ”Produktion” för alla resurser i produktionsmiljön.

- [Så här skapar du frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Azure Security Center till gångs lager hantering](../security-center/asset-inventory.md)

- [Mer information om att tagga till gångar finns i besluts guiden för resurs namn givning och taggning](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Använd endast godkända Azure-tjänster

**Vägledning**: Azure App konfiguration stöder Azure Resource Manager baserade distributioner och konfigurations tvång med hjälp av Azure policy. Använd Azure Policy för att granska och begränsa vilka tjänster som användare kan etablera i din miljö. Använd Azure Resource Graph för att fråga efter och identifiera resurser i prenumerationerna. Du kan också använda Azure Monitor för att skapa regler för att utlösa aviseringar när en icke-godkänd tjänst upptäcks.

- [Så här konfigurerar och hanterar du Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en speciell resurs typ med Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Så här skapar du frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: se till att till gångens livs cykel hantering är säker

**Vägledning**: upprätta eller uppdatera säkerhets principer som behandlar hanterings processer för till gångs livs cykeln för potentiellt höga konsekvenser. Ändringarna omfattar ändringar, men är inte begränsade till: identitets leverantörer och åtkomst, data känslighet, nätverks konfiguration och tilldelning av administrativa privilegier.

Ta bort Azure-resurser när de inte längre behövs. Se till att administratörer regelbundet roterar åtkomst nycklar för att säkerställa att endast autentiserade användare har åtkomst till sina konfigurations resurser.

- [Rotera krypterings nycklar som används för program konfiguration](concept-customer-managed-keys.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Använd villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azures resurs hanterare genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azures resurs hanterare](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="logging-and-threat-detection"></a>Loggning och hot identifiering

*Mer information finns i [säkerhets benchmark för Azure: loggning och hot identifiering](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Aktivera hot identifiering för Azure Identity and Access Management

**Vägledning**: appens konfiguration integreras med Azure Active Directory (Azure AD). Detta ger följande användar loggar som kan visas i Azure AD-rapportering eller integreras med Azure Monitor, Azure Sentinel eller andra SIEM/övervaknings verktyg för mer avancerade övervaknings-och analys användnings fall:
- Inloggningar – inloggnings rapporten innehåller information om användningen av hanterade program och användar inloggnings aktiviteter.
- Granskningsloggar – Ger spårbarhet via loggar för alla ändringar som gjorts via olika funktioner i Azure AD. Exempel på gransknings loggar är ändringar som har gjorts i alla resurser i Azure AD, till exempel att lägga till eller ta bort användare, appar, grupper, roller och principer.
- Riskfyllda inloggningar – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare.
- Användare som har flaggats för risk – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats.

Azure Security Center kan också Avisera om vissa misstänkta aktiviteter, till exempel ett högt antal misslyckade autentiseringsförsök, föråldrade konton i prenumerationen. Utöver den grundläggande övervakningen av säkerhets övervakning kan Azure Security Centerens skydds modul även samla in mer detaljerade säkerhets aviseringar i Azure Service Layers. Den här funktionen ger dig insyn i konto avvikelser i de enskilda resurserna.

En annan metod för att få åtkomst till konfigurations resursen för din app-konfiguration är att använda åtkomst nycklar. Dessa måste roteras regelbundet för att förhindra att obehöriga agenter får åtkomst till konfigurations resursen. Genom att rotera dem kan du göra det direkt i portalen under "åtkomst nycklar".

- [Så här tillåter Azure App-konfiguration åtkomst till andra Azure AD-skyddade resurser med en hanterad identitet](overview-managed-identity.md)

- [Använda hanterade identiteter med Azure App konfiguration](howto-integrate-azure-managed-service-identity.md)

- [Granska aktivitets rapporter i Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Aktivera Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Auktorisera åtkomst till Azure App konfiguration med hjälp av Azure AD](concept-enable-rbac.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Aktivera loggning för Azure nätverks aktiviteter

**Vägledning**: Azure App konfiguration distribuerar inte några resurser direkt till ett virtuellt nätverk. Med appens konfiguration kan du dock använda privata slut punkter för att ansluta säkert till Azure App konfiguration från ett virtuellt nätverk. Azure App-konfigurationen skapar eller bearbetar inte heller DNS-frågemeddelanden som behöver aktive ras.

Aktivera loggning på dina konfigurerade privata slut punkter för konfiguration av appar till avbildning:
- Data som bearbetas av den privata slut punkten (IN/ut)
- Data som bearbetas av tjänsten för privat länk (IN/ut)
- Tillgänglighet för NAT-Port

Mer information finns i följande referenser:

- [Azures övervakning av privata länkar](../private-link/private-link-overview.md#logging-and-monitoring)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Aktivera loggning för Azure-resurser

**Vägledning**: aktivitets loggar, som är automatiskt tillgängliga, innehåller alla Skriv åtgärder (placera, skicka, ta bort) för dina konfigurations resurser för appar förutom Läs åtgärder (get). Aktivitets loggar kan användas för att hitta ett fel när du felsöker eller övervakar hur en användare i organisationen ändrade en resurs. För konfiguration av appar är aktivitets loggar bara tillgängliga i kontroll planet och visas i Azure Resource Manager (ARM). Det finns för närvarande inte stöd för kundriktad data Plans loggning för app-konfiguration. Azures resurs loggar är inte heller tillgängliga för konfiguration.

- [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Förstå loggning och olika logg typer i Azure](../azure-monitor/platform/platform-logs-overview.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: centralisera hantering av säkerhets loggar och analys

**Vägledning**: centralisera loggning av lagring och analys för att aktivera korrelation. För varje logg källa ser du till att du har tilldelat en data ägare, åtkomst vägledning, lagrings plats, vilka verktyg som används för att bearbeta och komma åt data samt krav för data lagring.

Se till att du integrerar Azures aktivitets loggar i den centrala loggningen. Mata in loggar via Azure Monitor för att samla in säkerhets data som genererats av slut punkts enheter, nätverks resurser och andra säkerhets system. I Azure Monitor använder du Log Analytics arbets ytor för att fråga och utföra analyser och använda Azure Storage konton för långsiktig lagring.

Du kan också aktivera och publicera data till Azure Sentinel eller en SIEM från tredje part. Många organisationer väljer att använda Azure Sentinel för "heta" data som används ofta och Azure Storage för "kall" data som används mindre ofta.

- [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurera kvarhållning av logg lagring

**Vägledning**: kontrol lera att lagrings konton eller Log Analytics arbets ytor som används för att lagra konfigurations loggar för appar har den logg kvarhållningsperiod som anges enligt organisationens regler för efterlevnad. Använd Azure Storage-, Data Lake-eller Log Analytics arbets ytans konton för långsiktig lagring och arkivering.

I Azure Monitor kan du Log Analytics ange arbets ytans lagrings period enligt organisationens regler för efterlevnad.

- [Så här konfigurerar du Log Analytics bevarande period för arbets ytor](../azure-monitor/platform/manage-cost-storage.md)

- [Lagra resurs loggar i ett Azure Storage konto](/azure/azure-monitor/platform/resource-logs-collect-storage)

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

Azure Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center är i sökningen eller analysen som används för att utfärda aviseringen, samt den konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

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

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: upprätta säkra konfigurationer för Azure-tjänster 

**Vägledning**: Azure App konfiguration stöder följande tjänstspecifika principer som är tillgängliga i Azure Security Center för att granska och tillämpa konfigurationer för dina Azure-resurser. Detta kan konfigureras i Azure Security Center eller Azure Policy initiativ.
- App-konfigurationen bör använda en kundhanterad nyckel: Kundhanterade nycklar ger förbättrat data skydd genom att låta dig hantera dina krypterings nycklar. Detta krävs ofta för att uppfylla kraven för efterlevnad.
- App-konfigurationen ska använda en privat länk: anslutningar för privata slut punkter tillåter klienter i ett virtuellt nätverk att säkert komma åt Azure App konfiguration via en privat länk.

Du kan använda Azure-ritningar för att automatisera distribution och konfiguration av tjänster och program miljöer, inklusive Azures resurser Manager-mallar, Azure RBAC-kontroller och-principer, i en enda skiss definition.

- [Mer information om konfigurations principer för appar](../governance/policy/samples/built-in-policies.md#app-configuration)

- [Arbeta med säkerhets principer i Azure Security Center](../security-center/tutorial-security-policy.md)

- [Illustration av guardrails-implementering i landnings zon för företags skala](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Självstudie: skapa och hantera principer för att genomdriva efterlevnad](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint](../governance/blueprints/overview.md)

**Azure Security Center övervakning**: Ja

**Ansvars område**: delat

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: upprätthålla säkra konfigurationer för Azure-tjänster

**Vägledning**: Använd Azure Security Center för att övervaka konfigurations bas linjen och tillämpa användningen av Azure policy. Azure Policy för konfiguration av appar innehåller: 
- App-konfigurationen bör använda en kundhanterad nyckel: Kundhanterade nycklar ger förbättrat data skydd genom att låta dig hantera dina krypterings nycklar. Detta krävs ofta för att uppfylla kraven för efterlevnad.
- App-konfigurationen ska använda en privat länk: anslutningar för privata slut punkter tillåter klienter i ett virtuellt nätverk att säkert komma åt Azure App konfiguration via en privat länk.

- [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md) 

- [Skapa och hantera principer för att använda kompatibilitet](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center övervakning**: Ja

**Ansvars område**: delat

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: utför regelbunden attack simulering

**Vägledning**: vid behov kan du genomföra inträngande tester eller röda team aktiviteter på dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister.
Följ test reglerna för Microsoft Cloud inträngande för att se till att dina inträngande tester inte strider mot Microsofts principer. Använd Microsofts strategi och körning av röda team indelning och inträngande av direktsända webbplatser mot Microsoft-hanterad moln infrastruktur, tjänster och program.

- [Inträngande testning i Azure](../security/fundamentals/pen-testing.md)

- [Inträngande test regler för engagemang](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud röd team indelning](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: delat

## <a name="backup-and-recovery"></a>Säkerhetskopiering och återställning

*Mer information finns i [säkerhets benchmark för Azure: säkerhets kopiering och återställning](/azure/security/benchmarks/security-controls-v2-backup-recovery).*

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: minska risken för förlorade nycklar

**Vägledning**: se till att du har mått på plats för att förhindra och återställa från förlust av nycklar. Aktivera mjuk borttagning och tömning av skydd i Azure Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning.

- [Så här aktiverar du mjuk borttagning och tömning av skydd i Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

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

**Vägledning**: upprätta en strategi för loggning och hot svar för att snabbt upptäcka och åtgärda hot samtidigt som de uppfyller kraven för efterlevnad. Prioritera att ge analytiker med hög kvalitets varningar och sömlösa upplevelser så att de kan fokusera på hot i stället för integrering och manuella steg. 

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
