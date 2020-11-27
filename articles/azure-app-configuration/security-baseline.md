---
title: Azures säkerhets bas linje för Azure App konfiguration
description: Säkerhets bas linjen för Azure App konfiguration ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: a885f027250a915347fea758c7c7978af0f45f49
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302516"
---
# <a name="azure-security-baseline-for-azure-app-configuration"></a>Azures säkerhets bas linje för Azure App konfiguration

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark-version 2,0](../security/benchmarks/overview.md) för att Azure App konfiguration. Benchmark för Azure-säkerhet innehåller rekommendationer för hur du kan skydda dina molnlösningar på Azure. Innehållet grupperas efter de **säkerhets kontroller** som definieras av Azures säkerhets benchmark och relaterade rikt linjer för Azure App konfiguration. **Kontroller** som inte gäller för Azure App konfiguration har uteslutits.

Om du vill se hur Azure App-konfigurationen är fullständigt mappad till Azures säkerhets benchmark, se den [fullständiga Azure App konfigurations filen för konfigurations säkerhets bas linje](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets principen för Azure-säkerhet: nätverks säkerhet](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementera säkerhet för intern trafik

**Vägledning**: Azure App konfiguration distribuerar inte några resurser direkt till ett virtuellt nätverk. Eftersom tjänsten inte har distribuerats till ett virtuellt nätverk kan du inte utnyttja vissa nätverksfunktioner för att skydda tjänstens interna trafik, t. ex. nätverks säkerhets grupper, väg tabeller eller andra nätverks enheter, till exempel en Azure-brandvägg. Med appens konfiguration kan du dock använda privata slut punkter för att ansluta säkert till Azure App konfiguration från ett virtuellt nätverk.

Använd Azure Sentinel för att identifiera användningen av äldre oskyddade protokoll som SSL/TLSv1, SMBv1, LM/NTLMv1, wDigest, osignerade LDAP-bindningar och svaga chiffer i Kerberos.

- [Använda privata slut punkter för Azure App konfiguration](concept-private-endpoint.md)

- [Arbets bok för Azure Sentinel-oskyddade protokoll](../sentinel/quickstart-get-visibility.md#use-built-in-workbooks)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Delad

### <a name="ns-2-connect-private-networks-together"></a>NS-2: Anslut privata nätverk till varandra

**Vägledning**: Azure App konfiguration stöder användning av privata slut punkter för att på ett säkert sätt skicka data via en privat länk. Använd Azure ExpressRoute eller Azure Virtual Private Network (VPN) för att skapa privata anslutningar mellan Azure-datacenter och lokal infrastruktur i en miljö med en valfri plats. ExpressRoute-anslutningar går inte via det offentliga Internet och de ger högre tillförlitlighet, snabbare hastigheter och lägre fördröjning än vanliga Internet anslutningar. För punkt-till-plats-VPN och plats-till-plats-VPN kan du ansluta lokala enheter eller nätverk till ett virtuellt nätverk med valfri kombination av de här VPN-alternativen och Azure-ExpressRoute.

Om du vill ansluta två eller flera virtuella nätverk i Azure tillsammans använder du peering för virtuella nätverk. Nätverks trafik mellan peer-kopplat virtuella nätverk är privat och lagras i Azures stamnät nätverk.

- [Vad är ExpressRoute-anslutnings modeller](../expressroute/expressroute-connectivity-models.md)

- [Översikt över Azure VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [Peering för virtuella nätverk](../virtual-network/virtual-network-peering-overview.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: upprätta privat nätverks åtkomst till Azure-tjänster

**Vägledning**: Använd Azures privata länk för att ge privat åtkomst till Azure App konfiguration från dina virtuella nätverk utan att passera Internet.

Privat åtkomst är ett ytterligare skydd i djupgående mått utöver autentisering och trafik säkerhet som erbjuds av Azure-tjänster.

- [Förstå privat Azure-länk](../private-link/private-link-overview.md)

- [Konfigurera en privat länk i Azure App konfiguration](concept-private-endpoint.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: skydda program och tjänster från externa nätverks attacker

**Vägledning**: när du har åtkomst till konfigurations värden via ett virtuellt nätverk skyddar du dina resurser mot attacker från externa nätverk, inklusive DDoS-attacker (distributed denial of Service), programspecifika attacker och oönskad och potentiellt skadlig Internet trafik. Använd Azure-brandväggen för att skydda program och tjänster mot potentiellt skadlig trafik från Internet och andra externa platser. Skydda dina till gångar mot DDoS-attacker genom att aktivera DDoS standard skydd på dina virtuella Azure-nätverk. Använd Azure Security Center för att identifiera problem med felaktig konfiguration som rör dina nätverks relaterade resurser.

Azure App-konfigurationen är inte avsedd att köra webb program, men den innehåller konfigurationen för dessa webb program. Du behöver inte konfigurera några ytterligare inställningar eller distribuera några extra nätverks tjänster för att skydda den från externa nätverks attacker som mål webb program.

- [Dokumentation om Azure Firewall](../firewall/index.yml)

- [Hantera Azure DDoS Protection standard med hjälp av Azure Portal](../ddos-protection/manage-ddos-protection.md)

- [Rekommendationer för Azure Security Center](../security-center/recommendations-reference.md#recs-network)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: Distribuera intrångs identifiering/intrångs skydd system (ID/IP-adresser)

**Vägledning**: Använd Azure-brandvägg med hot information-baserad filtrering för att varna vid och/eller blockera trafik till och från kända skadliga IP-adresser och domäner. IP-adresserna och domänerna hämtas från Microsoft Threat Intelligence-flödet. När nytto lasten är nödvändig kan du distribuera en tredjeparts ID/IP-lösning från Azure Marketplace med funktioner för nytto Last kontroll. Alternativt kan du välja att använda värdbaserade ID: n/IP-adresser eller en värdbaserad lösning för slut punkts identifiering och-svar (EDR) tillsammans med eller i stället för nätverksbaserade ID: n/IP-adresser.

Obs! Om du har en regel eller annat krav för ID/IP-adresser kan du se till att det alltid är anpassat för att tillhandahålla aviseringar med hög kvalitet till din SIEM-lösning.

- [Så här distribuerar du Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Marketplace innehåller funktioner från tredje part-ID](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [Microsoft Defender ATP-EDR-kapacitet](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: förenkla nätverks säkerhets regler

**Vägledning**: använd Azure Virtual Network Service-taggar för att definiera nätverks åtkomst kontroller i nätverks säkerhets grupper eller Azure-brandvägg som kon figurer ATS för dina konfigurations resurser för appar. Du kan använda tjänst tag gen "AppConfiguration" i stället för specifika IP-adresser när du skapar säkerhets regler för utgående trafik i ditt program nätverk. Genom att ange namnet på service tag i ett lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

- [Förstå och använda service märken](../virtual-network/service-tags-overview.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

## <a name="identity-management"></a>Identitetshantering

*Mer information finns i [Benchmark för Azure-säkerhet: Identitetshantering](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardisera Azure Active Directory som centralt identitets- och autentiseringssystem

**Vägledning**: Azure App konfiguration är integrerad med Azure Active Directory (Azure AD) som är Azures standard tjänst för identitets-och åtkomst hantering. Du bör standardisera Azure AD för att styra organisationens identitets-och åtkomst hantering i:
- Microsoft Cloud resurser, till exempel Azure Portal, Azure Storage, virtuell Azure-dator (Linux och Windows), Azure Key Vault, PaaS och SaaS program.
- Organisationens resurser, till exempel program i Azure eller företagets nätverksresurser.

Att skydda Azure AD bör vara en hög prioritet i din organisations rutiner för molnsäkerhet. Azure AD ger en identitets säker Poäng som hjälper dig att utvärdera position för identiteter i relation till Microsofts rekommendationer för bästa praxis. Använd poängen för att mäta hur nära konfigurationen matchar rekommendationerna för bästa praxis och för att göra förbättringar i din säkerhetsstatus.

Azure tillhandahåller följande inbyggda Azure-roller för att auktorisera åtkomst till konfigurations data för appar med hjälp av Azure AD och OAuth:

- Data ägare för app Configuration: Använd den här rollen för att ge Läs-/skriv-/borttagnings åtkomst till konfigurations data för appar. Detta ger inte åtkomst till appens konfigurations resurs.

- Konfigurations data läsare för appar: Använd den här rollen för att ge Läs åtkomst till konfigurations data för appar. Detta ger inte åtkomst till appens konfigurations resurs.

- Deltagare: Använd den här rollen för att hantera appens konfigurations resurs. Även om konfigurations data för appar kan nås med hjälp av åtkomst nycklar ger den här rollen ingen direkt åtkomst till data med hjälp av Azure AD.

- Läsare: Använd den här rollen för att ge Läs behörighet till appens konfigurations resurs. Detta ger inte åtkomst till resursens åtkomst nycklar eller data som är lagrade i app-konfigurationen.

Mer information finns i följande resurser:

- [Så skapar och konfigurerar du en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Vad är säkerhets poängen för identiteter i Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

- [Ge åtkomst till Azure App konfiguration med hjälp av Azure AD](concept-enable-rbac.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

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

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Använda enkel inloggning för Azure AD (SSO) för programåtkomst

**Vägledning**: Azure App konfiguration använder Azure Active Directory (Azure AD) för att tillhandahålla identitets-och åtkomst hantering för Azure-resurser, moln program och lokala program. Här ingår företagsidentiteter som anställda, samt externa identiteter som partner, säljare och leverantörer. Azure AD möjliggör enkel inloggning (SSO) för att hantera konfigurations tjänsten för appar via Azure Portal med hjälp av eventuella synkroniserade företags Active Directory identiteter. Anslut alla användare, program och enheter till Azure AD för sömlös, säker åtkomst och bättre synlighet och kontroll.

- [Förstå enkel programinloggning (Application SSO) med Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Använda starka autentiseringskontroller för all Azure Active Directory-baserad åtkomst

**Vägledning**: Azure App konfiguration använder Azure Active Directory som stöder starka verifierings kontroller med Multi-Factor Authentication (MFA) och starka metoder för lösen ords kryptering.
- Multifaktorautentisering (MFA) – aktivera Azure AD MFA och följ Azure Security Centers rekommendationer för identitets- och åtkomsthantering för vissa rekommenderade metoder i din MFA-installation. MFA kan tillämpas på alla, på utvalda användare eller på per användare-nivå baserat på inloggningsvillkor och riskfaktorer.
- Lösenordsfri autentisering – det finns tre alternativ för lösenordsfri autentisering: Windows Hello for Business, Microsoft Authenticator-appen och lokala autentiseringsmetoder som smartkort.

För administratörer och privilegierade användare ser du till att den högsta nivån av stark autentiseringsmetod används, följt av lämplig princip för stark autentisering för andra användare.

Obs! MFA kan tillämpas på de användar konton som har åtkomst till och hanterar konfiguration av appar, men inte på programmatiska tjänst konton. Använd lösenordsskyddad autentisering, till exempel hanterade identiteter där det är möjligt, och Använd MFA på alla användar konton.

- [Så här aktiverar du MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Introduktion till alternativ för lösenordsfri autentisering för Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Övervaka och varna vid kontoavvikelser

**Vägledning**: Azure App konfiguration är integrerad med Azure Active Directory i som tillhandahåller följande data Källor:

-   Inloggningsaktiviteter – Rapporten för inloggningsaktiviteter ger information om användningen av hanterade program och användares inloggningsaktiviteter.

-   Gransknings loggar – ger spårning genom loggar för alla ändringar som görs via olika funktioner i Azure AD. Exempel på gransknings loggar för loggade ändringar omfattar att lägga till eller ta bort användare, appar, grupper, roller och principer.

-   Riskfyllda inloggningar – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare.

-   Användare som har flaggats för risk – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats.

Dessa data källor kan integreras med Azure Monitor, Azure Sentinel eller SIEM system från tredje part.

Azure Security Center kan även Varna vid vissa misstänkta aktiviteter, till exempel ett högt antal misslyckade autentiseringsförsök och föråldrade konton i prenumerationen. 

Azure Advanced Threat Protection (ATP) är en säkerhetslösning som kan använda lokala Active Directory signaler för att identifiera, identifiera och undersöka avancerade hot, komprometterade identiteter och skadliga Insider-åtgärder.

- [Granska aktivitets rapporter i Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Så visar du riskfyllda inloggningar för Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här övervakar du användarnas identitets- och åtkomstrelaterade aktiviteter i Azure Security Center](../security-center/security-center-identity-access.md)

- [Aviseringar i skyddsmodulen för hotinformation i Azure Security Center](../security-center/alerts-reference.md)

- [Så här integrerar du Azures aktivitets loggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Anslut data från Azure AD Identity Protection](../sentinel/connect-azure-ad-identity-protection.md)

- [Azure Advanced Threat Protection](/azure-advanced-threat-protection/what-is-atp)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: begränsa åtkomsten till Azure-resursen baserat på villkor

**Vägledning**: Azure App konfiguration stöder Azure Active Directory (Azure AD) villkorlig åtkomst för en mer detaljerad åtkomst kontroll baserat på användardefinierade villkor, t. ex. användar inloggningar från vissa IP-intervall måste använda MFA för inloggning. Detaljerad hanterings princip för autentisering kan också användas för olika användnings fall. Dessa principer för villkorlig åtkomst gäller endast för användar konton som autentiserar till Azure AD för att få åtkomst till och hantera konfigurations tjänsten för appar, men de gäller inte för tjänstens huvud namn eller anslutnings strängar som ansluter till konfigurations resursen.

- [Översikt över villkorlig Azure-åtkomst](../active-directory/conditional-access/overview.md)

- [Vanliga principer för villkorlig åtkomst](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Konfigurera hantering av autentisering med villkorlig åtkomst](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: med Azure App-konfiguration kan kunder lagra konfigurationer som potentiellt kan innehålla identiteter eller hemligheter. Vi rekommenderar att du implementerar autentiseringsuppgifterna för autentisering för att identifiera autentiseringsuppgifter i konfigurationer. Den här skannern uppmuntrar också att flytta identifierade autentiseringsuppgifter till säkrare platser som Azure Key Vault.

Använd Azure App konfigurations tjänsten tillsammans med Azure Key Vault. Lagra autentiseringsuppgifter i Key Vault och länka sedan till dessa autentiseringsuppgifter genom att skapa en Key Vault referens i din app Configuration-resurs. När app-konfigurationen skapar dessa referenser lagrar den URI: erna för Key Vault värden i stället för själva värdena. Program kan ansluta till app-konfigurationen för att hämta autentiseringsuppgifter från Key Vault.

För GitHub kan du använda intern hemlighet-genomsökning för att identifiera autentiseringsuppgifter eller annan form av hemligheter i koden.

- [Självstudie för att använda Key Vault referenser i en ASP.NET Core-app](use-key-vault-references-dotnet-core.md)

- [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [GitHub Secret-genomsökning](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

## <a name="privileged-access"></a>Privilegierad åtkomst

*Mer information finns i [Benchmark för Azure-säkerhet: Privilegierad åtkomst](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Skydda och begränsa användare med höga privilegier

**Vägledning**: begränsa antalet privilegierade konton eller roller och skydda dessa konton på en utökad nivå eftersom användare med den här behörigheten kan direkt eller indirekt läsa och ändra alla resurser i din Azure-miljö.

Du kan aktivera just-in-time (JIT)-privilegierad åtkomst till Azure-resurser och Azure AD med hjälp av Azure AD Privileged Identity Management (PIM). JIT beviljar temporära behörigheter för att utföra privilegierade uppgifter endast när användarna behöver det. PIM kan också generera säkerhetsaviseringar när det finns misstänkt eller osäker aktivitet i din Azure AD-organisation.

Åtkomst nycklar är mycket privilegierade och bör roteras regelbundet som en säkerhets rutin. Åtkomst nycklar innehåller anslutnings strängar som innehåller autentiseringsinformation och betraktas som hemligheter. Dessa hemligheter måste lagras i Azure Key Vault och din kod måste autentiseras för att Key Vault ska kunna hämta dem. Åtkomst nycklar kan ge Läs-och Skriv behörighet till ett program. Kontrol lera att rätt typ av åtkomst nyckel har utfärdats för att förhindra obehörig åtkomst. För att vara säkrare använder du funktionen hanterade identiteter i Azure AD. Detta kräver bara att program har slut punkts konfigurations-URL för att få åtkomst till konfigurations värden.

- [Metod tips för app-konfiguration](howto-best-practices.md#app-configuration-bootstrap)

- [Använda hanterade identiteter för att få åtkomst till App Configuration](howto-integrate-azure-managed-service-identity.md)
- [Behörigheter för administratörsrollen i Azure AD](../active-directory/roles/permissions-reference.md)

- [Använda säkerhetsaviseringar från Privileged Identity Management i Azure](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Skydda privilegierad åtkomst för hybrid- och molndistributioner i Azure AD](../active-directory/roles/security-planning.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: begränsa administrativ åtkomst till verksamhets kritiska system

**Vägledning**: Azure App konfiguration använder Azure RBAC för att isolera åtkomst till affärs kritiska system genom att begränsa vilka konton som beviljas privilegie rad åtkomst. Azure RBAC stöds av app-konfigurationen på resurs nivån. För att säkert silo affärs kritiska konfigurationer lagrar du den här informationen i en egen app Configuration-resurs. I en resurs är detaljerad åtkomst också tillgänglig via skrivskyddade åtkomst konton eller nycklar, samt etiketter och taggning.

Alla typer av åtkomst kontroller bör justeras mot din strategi för företags segmentering för att säkerställa en konsekvent åtkomst kontroll.

- [Åtkomst till hanterings grupp](../governance/management-groups/overview.md#management-group-access)

- [Administratörer för Azure-prenumeration](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [Integrera RBAC med Azure AD med app-konfiguration](concept-enable-rbac.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Granska och stäm av användaråtkomsten regelbundet

**Vägledning**: Azure App konfiguration använder Azure Active Directory-konton (Azure AD) för att hantera sina resurser, granska användar konton och åtkomst tilldelningen regelbundet för att säkerställa att kontona och deras åtkomst är giltiga. 

Azure tillhandahåller följande inbyggda Azure-roller för att auktorisera åtkomst till konfigurations data för appar med hjälp av Azure AD och OAuth:

- Data ägare för app Configuration: Använd den här rollen för att ge Läs-/skriv-/borttagnings åtkomst till konfigurations data för appar. Detta ger inte åtkomst till appens konfigurations resurs.

- Konfigurations data läsare för appar: Använd den här rollen för att ge Läs åtkomst till konfigurations data för appar. Detta ger inte åtkomst till appens konfigurations resurs

Du kan använda åtkomst granskningar i Azure AD för att granska grupp medlemskap, åtkomst till företags program och roll tilldelningar, till exempel rollerna för program konfiguration ovan. Azure AD repor ting kan tillhandahålla loggar för att identifiera inaktuella konton. Du kan också använda Azure AD Privileged Identity Management för att skapa åtkomst gransknings rapportens arbets flöde för att under lätta gransknings processen.

Obs! hanterade identiteter föreslås där det är möjligt att autentisera till app-konfigurationen från en annan tjänst eller ett annat program. Du måste hantera alla tjänst huvud namn eller anslutnings strängar som kon figurer ATS med åtkomst till app-konfigurationen separat när de används.

- [Skapa en åtkomst granskning av Azure Resource roles i Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Så här använder du identitets- och åtkomstgranskningar i Azure AD](/azure/active-directory/governance/access-reviews-overview)

- [Ge åtkomst till Azure App konfiguration med hjälp av Azure AD](concept-enable-rbac.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Konfigurera nöd åtkomst i Azure AD

**Vägledning**: Azure App konfiguration är integrerad med Azure Active Directory för att hantera sina resurser. För att förhindra oavsiktligt låst av din Azure AD-organisation konfigurerar du ett konto för nöd åtkomst för åtkomst när normala administrativa konton inte kan användas. Konton för nöd åtkomst är ofta mycket privilegierade och de bör inte tilldelas vissa individer. Konton för nöd åtkomst är begränsade till nöd situationer eller "Break glas"-scenarier där normala administrativa konton inte kan användas.

Du bör se till att autentiseringsuppgifterna (till exempel lösen ord, certifikat eller smartkort) för konton för nöd åtkomst hålls skyddade och endast kända för personer som har behörighet att använda dem endast i nödfall.

- [Hantera konton för nöd åtkomst i Azure AD](../active-directory/roles/security-emergency-access.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatisera rättighets hantering 

**Vägledning**: Azure App konfiguration är integrerad med Azure Active Directory för att hantera sina resurser. Använd funktioner för hantering av Azure AD-rättighet för att automatisera åtkomst till begär ande arbets flöden, inklusive åtkomst tilldelningar, recensioner och förfallo datum. Det finns också stöd för godkännande med dubbla eller flera steg.

- [Vad är åtkomst granskningar för Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Vad är hantering av Azure AD-berättigande](../active-directory/governance/entitlement-management-overview.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Använd arbets stationer med privilegie rad åtkomst

**Vägledning**: skyddade, isolerade arbets stationer är mycket viktiga för att skydda känsliga roller som administratörer, utvecklare och kritiska tjänst operatörer. Använd hög säkra användar arbets stationer och/eller Azure-skydds för administrativa uppgifter relaterade till app-konfigurationen. Använd Azure Active Directory, Microsoft Defender Avancerat skydd (ATP) och/eller Microsoft Intune för att distribuera en säker och hanterad användar arbets station för administrativa uppgifter. De skyddade arbets stationerna kan hanteras centralt för att upprätthålla säker konfiguration, inklusive stark autentisering, program vara och maskin varu bas linjer, begränsad logisk och nätverks åtkomst.

- [Förstå arbets stationer med privilegie rad åtkomst](../active-directory/devices/concept-azure-managed-workstation.md) 

- [Distribuera en privilegie rad åtkomst arbets Station](../active-directory/devices/howto-azure-managed-workstation.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Följ JEA (Just Enough Administration, precis tillräcklig administration) (principen om minsta behörighet) 

**Vägledning**: Azure App konfiguration är integrerad med rollbaserad åtkomst kontroll (RBAC) i Azure för att hantera dess resurser. Med Azure RBAC kan du hantera åtkomst till Azure-resurser via rolltilldelningar. Du kan tilldela dessa roller till användare, grupper tjänstens huvud namn och hanterade identiteter. Det finns fördefinierade inbyggda roller för Azure App konfiguration och dessa roller kan inventeras eller frågas via verktyg som Azure CLI, Azure PowerShell eller Azure Portal. De behörigheter som du tilldelar resurser via Azure RBAC bör alltid vara begränsade till vad som krävs av rollerna. Det här är ett komplement till JIT-metoden (just in time) för Azure AD Privileged Identity Management (PIM) och bör granskas regelbundet.

Azure tillhandahåller följande inbyggda Azure-roller för att auktorisera åtkomst till konfigurations data för appar med hjälp av Azure AD och OAuth:
- Data ägare för app Configuration: Använd den här rollen för att ge Läs-/skriv-/borttagnings åtkomst till konfigurations data för appar. Detta ger inte åtkomst till appens konfigurations resurs.
- Konfigurations data läsare för appar: Använd den här rollen för att ge Läs åtkomst till konfigurations data för appar. Detta ger inte åtkomst till appens konfigurations resurs.

Använd inbyggda roller för att allokera behörighet och endast skapa anpassade roller vid behov. 

App-konfigurationen stöder lagring av konfiguration av flera program i en konfigurations resurs för en app. Om du vill begränsa informations åtkomsten mellan program skapar du en konfigurations resurs för appar per program och konfigurerar Azure RBAC därefter.

- [Vad är Azure rollbaserad åtkomst kontroll (Azure RBAC)](../role-based-access-control/overview.md)

- [Så här konfigurerar du RBAC i Azure](../role-based-access-control/role-assignments-portal.md)

- [Så här använder du identitets- och åtkomstgranskningar i Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Ge åtkomst till Azure App konfiguration med hjälp av Azure AD](concept-enable-rbac.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: Välj godkännande process för Microsoft Support  

**Vägledning**: implementera en organisations godkännande process för support scenarier där Microsoft kan behöva åtkomst till dina program konfigurations data. Customer Lockbox är för närvarande inte tillgängligt för support scenarier för program konfiguration.

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Benchmark för Azure-säkerhet: Dataskydd](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-1-discover-classify-and-label-sensitive-data"></a>DP-1: identifiera, klassificera och märk känsliga data

**Vägledning**: identifiera, klassificera och märk känsliga data så att du kan utforma lämpliga kontroller för att se till att känslig information lagras, bearbetas och överförs säkert av organisationens teknik system. Etiketter för känslig information i form av taggning stöds för konfigurations resurser för appar, men inte för konfigurations värden som finns i dem. När ett program har Läs-eller Läs-/Skriv behörighet till ett konfigurations lager har den fullständig åtkomst till alla konfigurationer i det arkivet.

- [Tagga känslig information med Azure Information Protection](/azure/information-protection/what-is-information-protection)

- [Tagga data klassificeringar i Azure](/azure/cloud-adoption-framework/govern/policy-compliance/data-classification#tagging-data-classification-in-azure)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Skydda känsliga data

**Vägledning**: för den underliggande plattformen, som hanteras av Microsoft, behandlar Microsoft allt kund innehåll som känsligt och skyddar mot kund data förlust och exponering. För att säkerställa att kunddata i Azure alltid är skyddade har Microsoft implementerat vissa standardkontroller och funktioner för dataskydd. Se till att du regelbundet roterar åtkomst nycklarna till dina program konfigurations resurser. Autentiseringsinformation från anslutnings strängar kan lagras i Azure Key Vault och koden måste autentiseras för att Key Vault ska kunna hämta dem. Åtkomst nycklar kan ge Läs-och Skriv behörighet till ett program. Kontrol lera att rätt typ av åtkomst nyckel har utfärdats för att förhindra obehörig åtkomst. För att vara säkrare använder du funktionen hanterade identiteter i Azure AD. Detta kräver bara att program har slut punkts konfigurations-URL för att få åtkomst till konfigurations värden.

Begränsa åtkomst med hjälp av rollbaserad åtkomst kontroll i Azure (Azure RBAC):

- Separera känsliga data till en egen konfigurations resurs för appar och allokera sedan RBAC-principer enligt detta så att endast auktoriserad åtkomst har Aktiver ATS 

- Använda nätverksbaserade åtkomst kontroller

- Vissa kontroller i Azure-tjänster (t. ex. kryptering i SQL och andra databaser) och säkerställer konsekvent åtkomst kontroll bör alla typer av åtkomst kontroll justeras mot din strategi för företags segmentering.

- Strategin för företagssegmentering bör också informeras om platsen för känsliga eller affärskritiska data och system.

Mer information finns i följande resurser:

- [Ge åtkomst till Azure App konfiguration med Azure Active Directory](concept-enable-rbac.md)

- [Data kryptering för app-konfiguration](faq.md#does-app-configuration-encrypt-my-data)

- [Rollbaserad åtkomstkontroll (RBAC) i Azure](../role-based-access-control/overview.md) 

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Delad

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: kryptera känslig information under överföring

**Vägledning**: för att komplettera åtkomst kontroller bör data i överföring skyddas mot "out of band"-attacker med kryptering. Detta säkerställer att angripare inte enkelt kan läsa eller ändra data.

Azure App konfiguration använder TLS-kryptering för alla HTTP-begäranden. Azure-infrastrukturen ger ett tillagt lager med kryptering på nätverks nivå för alla förfrågningar mellan Azure-datacenter. Se till att HTTP-trafik som alla klienter som ansluter till dina program konfigurations resurser kan förhandla TLS v 1.2 eller senare.

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Delad

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Kryptera känsliga vilande data

**Vägledning**: för att komplettera åtkomst kontroller bör data i vila skyddas mot "out of band"-attacker (till exempel åtkomst till underliggande lagring) med hjälp av kryptering. Detta säkerställer att angripare inte enkelt kan läsa eller ändra data.

Azure tillhandahåller data i rest-kryptering som standard. För mycket känsliga data har du alternativ för att implementera ytterligare kryptering i vila på alla Azure-resurser där det är tillgängligt. Azure hanterar dina krypterings nycklar som standard, men Azure ger möjlighet att hantera dina egna nycklar (Kundhanterade nycklar) för Azure App konfiguration.

- [Använd kundens hanterade nycklar för att kryptera dina data i Azure App konfiguration](concept-customer-managed-keys.md)

- [Förstå kryptering vid vila i Azure](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Krypterings modell och nyckel hanterings tabell](../security/fundamentals/encryption-atrest.md#azure-resource-providers-encryption-model-support)

- [Data i vila dubbel kryptering i Azure](../security/fundamentals/double-encryption.md#data-at-rest)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="asset-management"></a>Tillgångshantering

*Mer information finns i [Benchmark för Azure-säkerhet: Tillgångshantering](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Se till att säkerhetsteamet har insyn i risker för tillgångar

**Riktlinjer**: Se till att säkerhetsteamen beviljas behörigheter för säkerhetsläsare i din Azure-klient och prenumerationer som gör att de kan övervaka säkerhetsrisker med hjälp av Azure Security Center. 

Beroende på hur säkerhetsteamets ansvarsområden är strukturerade kan ansvaret för övervakning av säkerhetsrisker ligga hos en central säkerhetsgrupp eller ett lokalt team. Detta innebär att säkerhetsinsikter och -risker alltid måste samlas centralt inom en organisation. 

Behörigheter för säkerhetsläsare kan tillämpas brett över en hel klientorganisation (rothanteringsgrupp) eller omfattas av hanteringsgrupper eller specifika prenumerationer. 

Obs! Ytterligare behörigheter kan krävas för att få insyn i arbetsbelastningar och tjänster. 

- [Översikt över säkerhetsläsarrollen](../role-based-access-control/built-in-roles.md#security-reader)

- [Översikt över Azure-hanteringsgrupper](../governance/management-groups/overview.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: se till att säkerhets teamet har åtkomst till till gångs inventering och metadata

**Vägledning**: se till att säkerhets teamen har åtkomst till en kontinuerligt uppdaterad inventering av till gångar på Azure, till exempel Azure App konfiguration. Säkerhets teamen behöver ofta den här inventeringen för att utvärdera organisationens potentiella exponering för nya risker och som informerar om löpande säkerhets förbättringar. Skapa en Azure Active Directory grupp som ska innehålla organisationens auktoriserade säkerhets team och tilldela dem Läs behörighet till alla Azure App konfigurations resurser. Detta kan för enklas med en enda roll tilldelning på hög nivå i din prenumeration.

Azure Security Center Inventory-funktionen och Azure Resource Graph kan fråga efter och identifiera alla resurser i dina prenumerationer, inklusive Azure-tjänster, program och nätverks resurser.

Använd taggar för dina Azure-resurser, resurs grupper och prenumerationer för att logiskt organisera dem i en taxonomi. Varje tagg består av ett namn och ett värde-par. Du kan till exempel använda namnet ”Miljö” och värdet ”Produktion” för alla resurser i produktionsmiljön.

- [Så här skapar du frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Azure Security Center till gångs lager hantering](../security-center/asset-inventory.md)

- [Mer information om att tagga till gångar finns i besluts guiden för resurs namn givning och taggning](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Använd endast godkända Azure-tjänster

**Vägledning**: Azure App konfiguration stöder Azure Resource Manager baserade distributioner och konfigurations tvång med hjälp av Azure policy. Använd Azure Policy för att granska och begränsa vilka tjänster som användare kan etablera i din miljö. Använd Azure Resource Graph för att fråga efter och identifiera resurser i prenumerationerna. Du kan också använda Azure Monitor för att skapa regler för att utlösa aviseringar när en icke-godkänd tjänst upptäcks.

- [Så här konfigurerar och hanterar du Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en speciell resurs typ med Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Så här skapar du frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Garantera säker livscykelhantering för tillgångar

**Riktlinjer**: Upprätta eller uppdatera säkerhetsprinciper som kan användas för att hantera processer för livscykelhantering för ändringar med potentiellt stora konsekvenser. Ändringarna omfattar ändringar, men är inte begränsade till: identitets leverantörer och åtkomst, data känslighet, nätverks konfiguration och tilldelning av administrativa privilegier.

Ta bort Azure-resurser när de inte längre behövs. Se till att administratörer regelbundet roterar åtkomst nycklar för att säkerställa att endast autentiserade användare har åtkomst till sina konfigurations resurser.

- [Rotera krypterings nycklar som används för program konfiguration](concept-customer-managed-keys.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Använd villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azures resurs hanterare genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azures resurs hanterare](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

## <a name="logging-and-threat-detection"></a>Loggning och hotidentifiering

*Mer information finns i [Benchmark för Azure-säkerhet: Loggning och hotidentifiering](/azure/security/benchmarks/security-controls-v2-logging-threat-detection).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Aktivera hotidentifiering för identitets- och åtkomsthantering i Azure

**Vägledning**: appens konfiguration integreras med Azure Active Directory (Azure AD). Detta ger följande användar loggar som kan visas i Azure AD-rapportering eller integreras med Azure Monitor, Azure Sentinel eller andra SIEM/övervaknings verktyg för mer avancerade övervaknings-och analys användnings fall:
- Inloggningsaktiviteter – Rapporten för inloggningsaktiviteter ger information om användningen av hanterade program och användares inloggningsaktiviteter.
- Granskningsloggar – Ger spårbarhet via loggar för alla ändringar som gjorts via olika funktioner i Azure AD. Exempel på granskningsloggar är de resursändringar som görs i Azure AD, som att lägga till eller ta bort användare, appar, grupper, roller och principer.
- Riskfyllda inloggningar – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare.
- Användare som har flaggats för risk – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats.

Azure Security Center kan också avisera om vissa misstänkta aktiviteter, till exempel ett högt antal misslyckade autentiseringsförsök eller föråldrade konton i prenumerationen. Utöver den grundläggande övervakningen av säkerhets övervakning kan Azure Security Centerens skydds modul även samla in mer detaljerade säkerhets aviseringar i Azure Service Layers. Den här funktionen ger dig insyn i konto avvikelser i de enskilda resurserna.

En annan metod för att få åtkomst till konfigurations resursen för din app-konfiguration är att använda åtkomst nycklar. Dessa måste roteras regelbundet för att förhindra att obehöriga agenter får åtkomst till konfigurations resursen. Genom att rotera dem kan du göra det direkt i portalen under "åtkomst nycklar".

- [Så här tillåter Azure App-konfiguration åtkomst till andra Azure AD-skyddade resurser med en hanterad identitet](overview-managed-identity.md)

- [Använda hanterade identiteter med Azure App konfiguration](howto-integrate-azure-managed-service-identity.md)

- [Granska aktivitetsrapporter i Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Aktivera Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Auktorisera åtkomst till Azure App konfiguration med hjälp av Azure AD](concept-enable-rbac.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Aktivera loggning för Azure nätverks aktiviteter

**Vägledning**: Azure App konfiguration distribuerar inte några resurser direkt till ett virtuellt nätverk. Med appens konfiguration kan du dock använda privata slut punkter för att ansluta säkert till Azure App konfiguration från ett virtuellt nätverk. Azure App-konfigurationen skapar eller bearbetar inte heller DNS-frågemeddelanden som behöver aktive ras.

Aktivera loggning på dina konfigurerade privata slut punkter för konfiguration av appar till avbildning:
- Data som bearbetas av den privata slut punkten (IN/ut)
- Data som bearbetas av tjänsten för privat länk (IN/ut)
- Tillgänglighet för NAT-Port

Mer information finns i följande resurser:

- [Azures övervakning av privata länkar](../private-link/private-link-overview.md#logging-and-monitoring)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Aktivera loggning för Azure-resurser

**Vägledning**: aktivitets loggar, som är automatiskt tillgängliga, innehåller alla Skriv åtgärder (placera, skicka, ta bort) för dina konfigurations resurser för appar förutom Läs åtgärder (get). Aktivitets loggar kan användas för att hitta ett fel när du felsöker eller övervakar hur en användare i organisationen ändrade en resurs. För konfiguration av appar är aktivitets loggar bara tillgängliga i kontroll planet och visas i Azure Resource Manager (ARM). Det finns för närvarande inte stöd för kundriktad data Plans loggning för app-konfiguration. Azures resurs loggar är inte heller tillgängliga för konfiguration.

- [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Förstå loggning och olika logg typer i Azure](../azure-monitor/platform/platform-logs-overview.md)

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

**Vägledning**: kontrol lera att lagrings konton eller Log Analytics arbets ytor som används för att lagra konfigurations loggar för appar har den logg kvarhållningsperiod som anges enligt organisationens regler för efterlevnad. Använd Azure Storage-, Data Lake-eller Log Analytics arbets ytans konton för långsiktig lagring och arkivering.

I Azure Monitor kan du Log Analytics ange arbets ytans lagrings period enligt organisationens regler för efterlevnad.

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

*Mer information finns i [Benchmark för Azure-säkerhet: Status- och sårbarhetshantering](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management).*

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

**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: upprätthålla säkra konfigurationer för Azure-tjänster

**Vägledning**: Använd Azure Security Center för att övervaka konfigurations bas linjen och tillämpa användningen av Azure policy. Azure Policy för konfiguration av appar innehåller: 
- App-konfigurationen bör använda en kundhanterad nyckel: Kundhanterade nycklar ger förbättrat data skydd genom att låta dig hantera dina krypterings nycklar. Detta krävs ofta för att uppfylla kraven för efterlevnad.
- App-konfigurationen ska använda en privat länk: anslutningar för privata slut punkter tillåter klienter i ett virtuellt nätverk att säkert komma åt Azure App konfiguration via en privat länk.

- [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md) 

- [Skapa och hantera principer för att använda kompatibilitet](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Genomför regelbunden attacksimulering

**Riktlinjer**: Genomför vid behov genomslagstestning eller Red Team-aktiviteter på dina Azure-resurser och se till att alla kritiska säkerhetsbrister åtgärdas.
Följ Microsoft Clouds deltagarregler för genomslagstestning för att se till att genomslagstesterna inte strider mot Microsofts principer. Använd Microsofts strategi för och verkställande av Red Team-indelning och genomslagstester för live-webbplatser mot Microsoft-hanterad molninfrastruktur, tjänster och program.

- [Genomslagstestning i Azure](../security/fundamentals/pen-testing.md)

- [Deltagarregler för genomslagstestning](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Red Team-indelning i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Delad

## <a name="backup-and-recovery"></a>Säkerhetskopiering och återställning

*Mer information finns i [säkerhets benchmark för Azure: säkerhets kopiering och återställning](../security/benchmarks/security-controls-v2-backup-recovery.md).*

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: minska risken för förlorade nycklar

**Vägledning**: se till att du har mått på plats för att förhindra och återställa från förlust av nycklar. Aktivera mjuk borttagning och tömning av skydd i Azure Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning.

- [Så här aktiverar du mjuk borttagning och tömning av skydd i Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

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

-   Användning av data skydds funktioner i Azure Native och tredje part

-   Krypteringskrav för användningsfall med data under transport och i vila

-   Lämpliga kryptografiska standarder

Mer information finns i följande resurser:
- [Rekommendation för Azure Security Architecture – lagring, data och kryptering](/azure/architecture/framework/security/storage-data-encryption?amp;bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Grundläggande Azure-säkerhet – säkerhet, kryptering och lagring av data i Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework – bästa praxis för datasäkerhet och kryptering i Azure](../security/fundamentals/data-encryption-best-practices.md?amp;bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Benchmark för Azure-säkerhet – tillgångshantering](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Benchmark för Azure-säkerhet – dataskydd](/azure/security/benchmarks/security-controls-v2-data-protection)

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

- [Benchmark för Azure-säkerhet – status- och sårbarhetshantering](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

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

- [Benchmark för Azure-säkerhet – nätverkssäkerhet](/azure/security/benchmarks/security-controls-v2-network-security)

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

- [Benchmark för Azure-säkerhet – identitetshantering](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Benchmark för Azure-säkerhet – privilegierad åtkomst](/azure/security/benchmarks/security-controls-v2-privileged-access)

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

- [Benchmark för Azure-säkerhet – loggning och hotidentifiering](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Benchmark för Azure-säkerhet – incidenthantering](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Bästa praxis för Azure-säkerhet 4 – process. Uppdatera incidenthanteringsprocesser för molnet](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Beslutsguide för ramverk för Azure-införande, loggning och rapportering](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure i företagsskala, hantering och övervakning](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

## <a name="next-steps"></a>Nästa steg

- Se [Översikt över Benchmark för Azure-säkerhet V2](../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../security/benchmarks/security-baselines-overview.md)