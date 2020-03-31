---
title: Azure Security Baseline for Data Explorer
description: Azure Security Baseline for Data Explorer
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 92a607e4c17a16d07f07c97f626ed98a213d509b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289724"
---
# <a name="azure-security-baseline-for-data-explorer"></a>Azure Security Baseline for Data Explorer

Azure Security Baseline for Data Explorer innehåller rekommendationer som hjälper dig att förbättra säkerhetspositionen för distributionen.

Baslinjen för den här tjänsten hämtas från [Azure Security Benchmark version 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), som ger rekommendationer om hur du kan skydda dina molnlösningar på Azure med vår vägledning om bästa praxis.

Mer information finns i [översikt över Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Säkerhetskontroll: Nätverkssäkerhet](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Skydda resurser med nätverkssäkerhetsgrupper eller Azure-brandväggen i det virtuella nätverket

**Vägledning:** Azure Data Explorer stöder distribution av ett kluster till ett undernät i det virtuella nätverket. Med den här funktionen kan du tillämpa NSG-regler (Network Security Group) för din Azure Data Explorer-klustertrafik, ansluta ditt lokala nätverk till Azure Data Explorer-klustrets undernät och skydda dina dataanslutningskällor (Event Hub och Event Grid) med tjänstens slutpunkter.

Distribuera ditt Azure Data Explorer-kluster till ett virtuellt nätverk:https://docs.microsoft.com/azure/data-explorer/vnet-deployment


**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Övervaka och logga konfiguration och trafik av virtuella nätverk, undernät och NICS

**Vägledning**: Aktivera flödesloggar för nätverkssäkerhetsgrupper (NSG) och skicka loggar till ett lagringskonto för trafikgranskning.

Aktivera NSG-flödesloggar:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Förstå nätverkssäkerhet som tillhandahålls av Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations


**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="13-protect-critical-web-applications"></a>1.3: Skydda kritiska webbprogram

**Vägledning**: Ej tillämpligt. Rekommendationen är avsedd för webbprogram som körs på Azure App Service eller beräkningsresurser.

**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Aktivera Azure DDoS Protection Standard i det virtuella nätverket som skyddar dina Data Explorer-kluster för skydd mot DDoS-attacker. Använd Azure Security Center Integrated Threat Intelligence för att neka kommunikation med kända skadliga eller oanvända IP-adresser på Internet.

KonfigureraR du DDoS-skydd:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Förstå Azure Security Center Integrated Threat Intelligence:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Spela in nätverkspaket och flödesloggar

**Vägledning**: Aktivera flödesloggar i de nätverkssäkerhetsgrupper (NSG) som används för att skydda ditt Azure Data Explorer-kluster och skicka loggar till ett lagringskonto för trafikgranskning.

Aktivera NSG-flödesloggar:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6: Distribuera nätverksbaserade intrångsdetekterings-/intrångsskyddssystem

**Vägledning**: Ej tillämpligt. Den här kontrollen görs vid slutpunkten eller brandväggen.


**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7: Hantera trafik till dina webbapplikationer

**Vägledning**: Ej tillämpligt. Den här rekommendationen är avsedd för webbprogram som körs på Azure App Service eller beräkningsresurser.


**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimera komplexiteten och de administrativa omkostnaderna för nätverkssäkerhetsreglerna

**Vägledning**: Använd taggar för virtuella nätverkstjänst för att definiera nätverksåtkomstkontroller för nätverkssäkerhetsgrupper eller Azure-brandväggar som är associerade med dina Azure Data Explorer-kluster. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange tjänsttagnamnet (t.ex. ApiManagement) i lämpligt käll- eller målfält för en regel kan du tillåta eller neka trafik för motsvarande tjänst. Microsoft hanterar adressprefixen som omfattas av servicetag och uppdaterar automatiskt servicetag när adresserna ändras.

Förstå och använda tjänsttaggar:https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Konfigurationskrav för tjänsttaggar för Azure Data Explorer:https://docs.microsoft.com/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Underhåll standardsäkerhetskonfigurationer för nätverksenheter

**Vägledning**: Kunden för att definiera och implementera standardsäkerhetskonfigurationer för nätverksresurser med Azure Policy.

Kunden kan också använda Azure Blueprints för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljöartefakter, till exempel ARM-mallar, RBAC-kontroller och principer, i en enda skissdefinition. Använd enkelt skissen på nya prenumerationer och miljöer och finjustera kontroll och hantering genom versionshantering.

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här skapar du en Azure Blueprint:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Kund

### <a name="110-document-traffic-configuration-rules"></a>1.10: Konfigurationsregler för dokumenttrafik

**Vägledning**: Använd taggar för nätverkssäkerhetsgrupper (NSG) och andra resurser som är relaterade till nätverkssäkerhet och trafikflöde för dina Data Explorer-kluster. För enskilda NSG-regler använder du fältet "Beskrivning" för att ange affärsbehov och/eller varaktighet (etc.) för alla regler som tillåter trafik till/från ett nätverk.

Så här skapar och använder du taggar:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Använd automatiserade verktyg för att övervaka nätverksresurskonfigurationer och identifiera ändringar

**Vägledning**: Använd Azure Policy för att validera (och/eller åtgärda) konfiguration för nätverksresurser.

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [Säkerhetskontroll: Loggning och övervakning](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Använda godkända tidssynkroniseringskällor

**Vägledning:** Microsoft underhåller tidskällor för Azure-resurser, kunder kan uppdatera tidssynkronisering för beräkningsdistributioner som ägs av kunden.

Konfigurera tidssynkronisering för Azure-beräkningsresurser:https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurera hantering av centrala säkerhetsloggar

**Vägledning**: Azure Data Explorer använder diagnostikloggar för insikter om inmatningsframgångar och fel. Du kan exportera åtgärdsloggar till Azure Storage, Event Hub eller Log Analytics för att övervaka inmatningsstatus.

Så här övervakar du inmatningsåtgärder för Azure Data Explorer:

https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs

Så här får du plats och frågar övervakningsdata i Azure Data Explorer:

https://docs.microsoft.com/azure/data-explorer/ingest-data-no-code

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivera granskningsloggning för Azure-resurser

**Vägd:** Aktivera diagnostikinställningar för Azure Data Explorer för åtkomst och loggning till tjänstspecifika åtgärder och loggning. Azure Activity loggar i Azure Monitor, som innehåller hög nivå loggning om resursen är aktiverade som standard.

Så här övervakar du inmatningsåtgärder för Azure Data Explorer:https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs

Så här samlar du in plattformsloggar och mått med Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Översikt över Azure-plattformsloggar:https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview


**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="24-collect-security-logs-from-operating-system"></a>2.4: Samla in säkerhetsloggar från operativsystemet

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurera lagring av säkerhetslogglagring

**Vägledning**: Inom Azure Monitor anger du lagringsperioden för Logganalysarbetsyta enligt organisationens efterlevnadsregler. Använd Azure Storage-konton för långsiktig/arkiveringslagring.

Så här anger du loggkvarhållningsparametrar för Logganalysarbetsytor:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2.6: Övervaka och granska loggar

**Vägledning**: Analysera och övervaka loggar för avvikande beteenden och granska regelbundet resultat. När du har aktiverat diagnostikinställningar för Azure Data Explorer använder du Azure Monitors Log Analytics Workspace för att granska loggar och utföra frågor om loggdata.

Förstå logganalysarbetsyta:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Så här utför du anpassade frågor i Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Aktivera aviseringar för avvikande aktivitet

**Vägledning**: Ej tillämpligt. Azure Data Explorer har inte den här möjligheten.


**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralisera loggning mot skadlig kod

**Vägledning**: Ej tillämpligt. Azure Data Explorer bearbetar inte anti-malware loggning.

**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivera DNS-frågeloggning

**Vägledning**: Inte tillämplig: DNS-frågan är inte en funktion i Azure Data Explorer.


**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivera granskningsloggning på kommandoraden

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Ej tillämpligt

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Säkerhetskontroll: Identitets- och åtkomstkontroll](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1: Underhåll inventering av administrativa konton

**Vägd:** I Azure Data Explorer definierar säkerhetsroller vilka säkerhetsobjekt (användare och program) som har behörighet att fungera på en säker resurs, till exempel en databas eller en tabell, och vilka åtgärder som är tillåtna.  Du kan använda Kusto-frågan för att lista principer i administratörsrollen för Azure Data Explorer-kluster och databaser.
Hantering av säkerhetsroller i Azure Data Explorer med Kusto-fråga:https://docs.microsoft.com/azure/kusto/management/security-roles



**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändra standardlösenord där så är tillämpligt

**Vägledning**: Azure AD har inte begreppet standardlösenord. Andra Azure-resurser som kräver ett lösenord tvingar ett lösenord att skapas med komplexitetskrav och en minsta lösenordslängd, som skiljer sig beroende på tjänsten. Du är ansvarig för tredjepartsprogram och marknadsplatstjänster som kan använda standardlösenord.


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3.3: Se till att de särskilda administrativa räkenskaperna används

**Vägledning**: Kunden skapar standardrutiner kring användningen av särskilda administrativa konton. Använd Azure Security Center Identity and Access Management för att övervaka antalet administrativa konton.

Kunder kan också aktivera en Just-In-Time / Just-Enough-Access med hjälp av Azure AD Privileged Identity Management Privileged Roles för Microsoft Services och Azure ARM. 

Vad är Azure AD Privileged Identity Management?:https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3.4: Använda enkel inloggning (SSO) med Azure Active Directory

**Vägledning**: När det är möjligt kan kunden använda SSO med Azure Active Directory (Azure AD) i stället för att konfigurera enskilda fristående autentiseringsuppgifter per tjänst. Använd Azure Security Center Identity and Access Management-rekommendationer.

Förstå SSO med Azure AD:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Använd multifaktorautentisering för all Azure Active Directory-baserad åtkomst.

**Vägledning:** Aktivera MFA (Azure Active Directory) multifaktorautentisering (MFA) och följ Azure Security Center Identity and Access Management-rekommendationerna.

Aktivera MFA i Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted 

Så här övervakar du identitet och åtkomst i Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="36-use-of-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Användning av dedikerade maskiner (arbetsstationer för privilegierad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd PAWs (privilegierade åtkomstarbetsstationer) med MFA (Multifaktorautentisering) konfigurerade för att logga in på och konfigurera Azure-resurser.

Läs mer om arbetsstationer för privilegierad åtkomst:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Aktivera MFA i Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Kund

### <a name="37-log-and-alert-on-suspicious-activity-on-administrative-accounts"></a>3.7: Logga och varna för misstänkt aktivitet på administrativa konton

**Vägledning**: Använd Azure Active Directory (Azure AD) säkerhetsrapporter för generering av loggar och aviseringar när misstänkt eller osäker aktivitet inträffar i miljön. Använda Azure Security Center för att övervaka identitets- och åtkomstaktivitet

Identifiera Azure AD-användare som flaggats för riskfylld aktivitet:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Så här övervakar du användarnas identitets- och åtkomstaktivitet i Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8: Hantera Azure-resurs från endast godkända platser

**Vägledning**: Kunden använder namngivna platser för villkorlig åtkomst för att tillåta åtkomst från endast specifika logiska grupperingar av IP-adressintervall eller länder/regioner.

Konfigurera namngivna platser i Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="39-utilize-azure-active-directory"></a>3.9: Använd Azure Active Directory

**Vägledning:** Azure Active Directory (Azure AD) är den metod som föredras för att autentisera till Azure Data Explorer. Den stöder ett antal autentiseringsscenarier:

Användarautentisering (interaktiv inloggning): Används för att autentisera mänskliga huvudnamn.

Programautentisering (icke-interaktiv inloggning): Används för att autentisera tjänster och program som måste köras/autentisera utan att någon människa är närvarande.

Översikt över Åtkomstkontroll i Azure Data Explorer:https://docs.microsoft.com/azure/kusto/management/access-control

Autentisera med Azure Active Directory:https://docs.microsoft.com/azure/kusto/management/access-control/aad



**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelbundet granska och stämma av användaråtkomst

**Vägledning**: Azure Active Directory (Azure AD) innehåller loggar som hjälper dig att identifiera inaktuella konton. Dessutom kan du använda Azure Identity Access Reviews för att effektivt hantera gruppmedlemskap, åtkomst till företagsprogram och rolltilldelningar. Användarens åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst. 

Så här autentiserar du med Azure AD för Azure Data Explorer Access:https://docs.microsoft.com/azure/kusto/management/access-control/how-to-authenticate-with-aad

Azure AD-rapportering:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Så här använder du Azure Identity Access Reviews:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Övervaka försök att komma åt inaktiverade konton

**Vägledning**: Du kan använda Azure Active Directory (Azure AD) Logga in aktivitet, granskning och riskhändelseloggkällor för övervakning som gör att du kan integrera med alla siem-/övervakningsverktyg (Security Information and Event Management).

 Du kan effektivisera den här processen genom att skapa diagnostikinställningar för Azure Active Directory-användarkonton, skicka granskningsloggar och inloggningsloggar till en Logganalysarbetsyta. kunden att konfigurera önskade aviseringar i Log Analytics Workspace.

Så här integrerar du Azure Activity Logs i Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Varning på kontoinloggningsbeteendeavvikelse

**Vägledning**: Använd Azure Active Directory (Azure AD) Riskidentifieringar och identitetsskyddsfunktionen för att konfigurera automatiska svar på identifierade misstänkta åtgärder relaterade till användaridentiteter. Dessutom kan du matsätta data i Azure Sentinel för vidare undersökning.

Så här visar du riska 2-inloggningar för Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Konfigurera och aktivera riskprinciper för identitetsskydd:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ge Microsoft åtkomst till relevanta kunddata under supportscenarier

**Vägledning**: I supportscenarier där Microsoft behöver komma åt kunddata tillhandahåller Customer Lockbox ett gränssnitt där kunderna kan granska och godkänna eller avvisa begäranden om kunddataåtkomst.

Förstå Customer Lockbox:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Säkerhetskontroll: Dataskydd](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Upprätthålla en inventering av känslig information

**Vägledning**: Använd taggar för att hjälpa till att spåra Azure-resurser som lagrar eller bearbetar känslig information.

Så här skapar och använder du taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolera system som lagrar eller bearbetar känslig information

**Vägledning**: Implementera separata prenumerationer och/eller hanteringsgrupper för utveckling, test och produktion. Azure Data Explorer-kluster bör separeras från andra resurser via virtuellt nätverk/undernät, taggade på rätt sätt och skyddas inom en nätverkssäkerhetsgrupp (NSG) eller Azure-brandväggen. Data Explorer-kluster som lagrar eller bearbetar känsliga data bör vara tillräckligt isolerade.

Så här skapar du ytterligare Azure-prenumerationer:https://docs.microsoft.com/azure/billing/billing-create-subscription

Så här skapar du hanteringsgrupper:https://docs.microsoft.com/azure/governance/management-groups/create

Så här skapar och använder du taggar:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Distribuera ditt Azure Data Explorer-kluster till ett virtuellt nätverk:https://docs.microsoft.com/azure/data-explorer/vnet-deployment

Hur man skapar en NSG med en Security Config:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Övervaka och blockera obehörig överföring av känslig information

**Vägledning**: Ej tillämpligt. För den underliggande plattformen som hanteras av Microsoft behandlar Microsoft allt kundinnehåll som känsligt och gör stora ansträngningar för att skydda mot förlust av kunddata och exponering. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och underhållit en uppsättning robusta dataskyddskontroller och funktioner.

Förstå kunddataskyddet i Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Kryptera all känslig information i transit

**Vägledning**: Azure Data Explorer-kluster förhandlar tls 1.2 som standard. Se till att alla klienter som ansluter till dina Azure-resurser kan förhandla om TLS 1.2 eller senare.

**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Delat

### <a name="45-divuse-an-active-discovery-tool-to-identify-sensitive-datadiv"></a>4.5: <div>Använda ett aktivt identifieringsverktyg för att identifiera känsliga data</div>

**Vägledning**: Funktioner för dataidentifiering, klassificering och förlustförebyggande är ännu inte tillgängliga för Azure Data Explorer. Implementera tredjepartslösning om det behövs för efterlevnadsändamål.

För den underliggande plattformen som hanteras av Microsoft behandlar Microsoft allt kundinnehåll som känsligt och gör stora ansträngningar för att skydda mot förlust av kunddata och exponering. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och underhållit en uppsättning robusta dataskyddskontroller och funktioner.

Förstå kunddataskyddet i Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Kund

### <a name="46-divuse-azure-rbac-to-control-access-to-resourcesdiv"></a>4.6: <div>Använda Azure RBAC för att kontrollera åtkomsten till resurser</div>

**Vägledning**: Med Azure Data Explorer kan du styra åtkomsten till databaser och tabeller med hjälp av en RBAC-modell (Role-based Access Control). Under den här modellen mappas huvudnamn (användare, grupper och appar) till roller. Huvudnamn kan komma åt resurser enligt de roller de har tilldelats.

Lista över roller och behörigheter och instruktioner om hur du konfigurerar RBAC för Azure Data Explorer:https://docs.microsoft.com/azure/data-explorer/manage-database-permissions


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Använd värdbaserad dataförlustprevention för att genomdriva åtkomstkontroll

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

Microsoft hanterar den underliggande infrastrukturen för Azure Data Explorer och har implementerat strikta kontroller för att förhindra förlust eller exponering av kunddata.

Förstå kunddataskyddet i Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Kryptera känslig information i vila

**Vägledning**: Azure Disk Encryption hjälper till att skydda och skydda dina data för att uppfylla dina organisatoriska säkerhets- och efterlevnadsåtaganden. Det ger volymkryptering för operativsystem och datadiskar för dina kluster virtuella datorer. Den integreras också med Azure Key Vault som gör att vi kan styra och hantera diskkrypteringsnycklar och hemligheter, och se till att alla data på VM-diskarna krypteras i vila medan de är i Azure Storage.

Aktivera kryptering i vila för Azure Data Explorer-kluster:https://docs.microsoft.com/azure/data-explorer/manage-cluster-security


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logga och avisera om ändringar i kritiska Azure-resurser

**Vägledning**: Använd Azure Monitor med Azure Activity Log för att skapa aviseringar för när ändringar på resursnivå sker i dina Azure Data Explorer-kluster.

Så här skapar du aviseringar för Azure Activity Log-händelser:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Så här skapar du aviseringar för Azure Activity Log-händelser:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Säkerhetskontroll: Sårbarhetshantering](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Kör verktyg för automatisk sårbarhetsskanning

**Vägledning**: Följ rekommendationer från Azure Security Center för att skydda dina Azure Data Explorer-resurser.

Microsoft utför också sårbarhetshantering på de underliggande systemen som stöder Azure Data Explorer.

Förstå Azure Security Center-rekommendationer:https://docs.microsoft.com/azure/security-center/recommendations-reference

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Distribuera lösning för hantering av automatisk operativsystemskorrigering

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Distribuera automatisk lösning för hantering av programkorrigering från tredje part

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Jämför sårbarhetsskanningar från rygg mot rygg

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="55-utilize-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Använd en riskklassificeringsprocess för att prioritera reparation av upptäckta sårbarheter.

**Vägledning**: Använd standardriskklassificeringarna (Secure Score) som tillhandahålls av Azure Security Center.
Förstå Azure Security Center Secure Score:https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-utilize-azure-asset-discovery"></a>6.1: Utnyttja Identifiering av Azure Asset

**Vägledning**: Använd Azure Resource Graph för att fråga/identifiera alla resurser (till exempel beräkning, lagring, nätverk, portar och protokoll osv.) i dina prenumerationer. Kontrollera lämpliga (läs) behörigheter i din klientorganisation och räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via Resource Graph, rekommenderas det starkt att skapa och använda Azure Resource Manager-resurser framöver.

Så här skapar du frågor med Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Så här visar du dina Azure-prenumerationer:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Förstå Azure RBAC:https://docs.microsoft.com/azure/role-based-access-control/overview



**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Kund

### <a name="62-maintain-asset-metadata"></a>6.2: Underhåll metadata för tillgångar

**Vägledning**: Använd taggar på Azure-resurser som ger metadata för att logiskt ordna dem till en taxonomi.

Så här skapar och använder du taggar:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ta bort obehöriga Azure-resurser

**Vägledning**: Du kan använda lämpliga namngivningskonventioner, taggning, hanteringsgrupper eller separata prenumerationer, i förekommande fall, för att organisera och spåra tillgångar. Du kan använda Azure Resource Graph för att stämma av lager regelbundet och se till att obehöriga resurser tas bort från prenumerationen i tid. 

Så här skapar du ytterligare Azure-prenumerationer:https://docs.microsoft.com/azure/billing/billing-create-subscription

Så här skapar du hanteringsgrupper:https://docs.microsoft.com/azure/governance/management-groups/create

Så här skapar och använder du Taggar:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Så här skapar du frågor med Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Kund

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Underhåll inventering av godkända Azure-resurser och programvarutitlar.

**Vägledning**: Du måste skapa en inventering av godkända Azure-resurser och godkänd programvara för beräkningsresurser enligt dina organisationsbehov.  


**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Kund

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Övervaka för icke godkända Azure-resurser

**Vägledning**: Du kan använda Azure-principer för att begränsa vilken typ av resurser som kan skapas i kundprenumerationer med hjälp av följande inbyggda principdefinitioner:

    - Otillåtna resurstyper

    - Tillåtna resurstyper

Du kommer att kunna övervaka de principgenererade händelserna med hjälp av 

Aktivitetsloggar som kan övervakas med Azure Monitor.

Dessutom kan du använda Azure Resource Graph för att fråga/identifiera resurser inom prenumerationerna.

Självstudiekurs: Skapa och hantera principer för att upprätthålla efterlevnad:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Snabbstart: Kör din första Resource Graph-fråga med Utforskaren för Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Skapa, visa och hantera aktivitetsloggaviseringar med hjälp av Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Kund

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Övervaka icke godkända program i beräkningsresurser

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Ta bort icke godkända Azure-resurser och program

**Vägledning**: Ej tillämpligt. Den här rekommendationen är avsedd för beräkningsresurser och Azure som helhet.

**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="68-utilize-only-approved-applications"></a>6.8: Använd endast godkända ansökningar

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="69-utilize-only-approved-azure-services"></a>6.9: Använd endast godkända Azure-tjänster

**Vägledning**: Du kan använda Azure-principer för att begränsa vilken typ av resurser som kan skapas i kundprenumerationer med hjälp av följande inbyggda principdefinitioner:

    - Otillåtna resurstyper

    - Tillåtna resurstyper

Självstudiekurs: Skapa och hantera principer för att upprätthålla efterlevnad:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Exempel på Azure-princip:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Kund

### <a name="610-implement-approved-application-list"></a>6.10: Genomföra godkänd ansökningslista

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Begränsa användarnas möjlighet att interagera med Azure Resources Manager via skript

**Vägledning**: Använd Azure Villkorlig åtkomst för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "Blockera åtkomst" för "Microsoft Azure Management"-appen. Detta förhindrar att resurser skapas och ändras i dina Azure-prenumerationer. 

Hantera åtkomst till Azure-hantering med villkorlig åtkomst:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Begränsa användarnas förmåga att köra skript inom beräkningsresurser

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fysiskt eller logiskt segregera högriskapplikationer

**Vägledning**: Ej tillämpligt. Den här rekommendationen är avsedd för webbprogram som körs på Azure App Service eller beräkningsresurser.

**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Ej tillämpligt

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Säkerhetskontroll: Säker konfiguration](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: Använd Azure Policy-alias för att skapa anpassade principer för granskning eller genomdredning av konfigurationen av dina Azure-resurser. Du kan också använda inbyggda Azure-principdefinitioner.

Azure Resource Manager har också möjlighet att exportera mallen i JavaScript Object Notation (JSON), som bör granskas för att säkerställa att konfigurationerna uppfyller/ överskrider säkerhetskraven för din organisation.

Du kan också använda rekommendationer från Azure Security Center som en säker konfigurationsbaslinje för dina Azure-resurser.

Så här visar du tillgängliga Azure-principalias:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Självstudiekurs: Skapa och hantera principer för att upprätthålla efterlevnad:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Export med en och flera resurser till en mall i Azure-portalen:https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal

Säkerhetsrekommendationer - en referensguide:https://docs.microsoft.com/azure/security-center/recommendations-reference



**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Kund

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2: Upprätta säkra konfigurationer för ditt operativsystem

**Vägledning**: Ej tillämpligt. den här riktlinjen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3: Underhåll säkra konfigurationer för alla Azure-resurser

**Vägledning**: Använd Azure-principen [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar för dina Azure-resurser.  Du kan använda lösningar som ändringsspårning, instrumentpanel för principefterlevnad eller en anpassad lösning för att enkelt identifiera säkerhetsändringar i din miljö.

Förstå Azure-principeffekter:https://docs.microsoft.com/azure/governance/policy/concepts/effects

Skapa och hantera principer för att framtvinga efterlevnad:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Spåra ändringar i din miljö med lösningen Ändra spårning:https://docs.microsoft.com/azure/automation/change-tracking

Hämta efterlevnadsdata för Azure-resurser:https://docs.microsoft.com/azure/governance/policy/how-to/get-compliance-data



**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Kund

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4: Underhåll säkra konfigurationer för operativsystem

**Vägledning**: Ej tillämpligt. den här riktlinjen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Lagringskonfiguration på ett säkert sätt av Azure-resurser

**Vägledning:** Använd Azure Repos för att lagra och hantera din kod på ett säkert sätt, till exempel anpassade Azure-principer, Azure Resource Manager-mallar, skript för önskad tillståndskonfiguration etc. Om du vill komma åt de resurser du hanterar i Azure DevOps kan du bevilja eller neka behörigheter till specifika användare, inbyggda säkerhetsgrupper eller grupper som definierats i Azure Active Directory (Azure AD) om de är integrerade med Azure DevOps eller Active Directory om de är integrerade med TFS.

Så här lagrar du kod i Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Om behörigheter och grupper i Azure DevOps:https://docs.microsoft.com/azure/devops/organizations/security/about-permissions



**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Kund

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Lagra anpassade operativsystemavbildningar på ett säkert sätt

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Distribuera verktyg för hantering av systemkonfiguration

**Vägledning**: Definiera och implementera standardsäkerhetskonfigurationer för Azure-resurser med Azure-princip. Använd Azure Policy-alias för att skapa anpassade principer för granskning eller framtvingning av nätverkskonfigurationen för dina Azure-resurser. Du kan också använda inbyggda principdefinitioner som är relaterade till dina specifika resurser.  Dessutom kan du använda Azure Automation för att distribuera konfigurationsändringar.

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här använder du alias:https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases



**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Kund

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Distribuera verktyg för hantering av systemkonfigurationer för operativsystem

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementera automatisk konfigurationsövervakning för Azure Services

**Vägledning**: Använd Azure Policy-alias för att skapa anpassade principer för att avisera, granska och tillämpa systemkonfigurationer. Använd Azure-princip [granskning], [neka] och [distribuera om det inte finns] för att automatiskt framtvinga konfigurationer för dina Azure-resurser.

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementera automatisk konfigurationsövervakning för operativsystem

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="711-securely-manage-azure-secrets"></a>7.11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning:** Azure Disk Encryption tillhandahåller volymkryptering för operativsystem och datadiskar i dina virtuella Azure Data Explorer-klusterdatorer. Den integreras också med Azure Key Vault som gör att du kan styra och hantera diskkrypteringsnycklar och hemligheter, och se till att alla data på VM-diskarna krypteras i vila medan du är i Azure Storage.

Skydda klustret i Azure Data Explorer:https://docs.microsoft.com/azure/data-explorer/manage-cluster-security


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Microsoft

### <a name="712-securely-and-automatically-manage-identities"></a>7.12: Hantera identiteter på ett säkert och automatiskt sätt

**Vägledning**: Använd hanterade identiteter för att tillhandahålla Azure-tjänster med en automatiskt hanterad identitet i Azure AD. Med hanterade identiteter kan du autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan några autentiseringsuppgifter i koden. Konfigurerar hanterade identiteter:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Konfigurera hanterade identiteter för ditt Azure Data Explorer-kluster:https://docs.microsoft.com/azure/data-explorer/managed-identities


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: Implementera autentiseringsskanner för att identifiera autentiseringsuppgifter i koden. Autentiseringsläsare kommer också att uppmuntra flytta identifierade autentiseringsuppgifter till säkrare platser som Azure Key Vault. 

Så här konfigurerar du autentiseringsskanner:https://secdevtools.azurewebsites.net/helpcredscan.html

**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Kund

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-utilize-centrally-managed-anti-malware-software"></a>8.1: Utnyttja centralt hanterad programvara mot skadlig kod

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

Microsoft anti-malware är aktiverat på den underliggande värden som stöder Azure-tjänster (till exempel Azure Data Explorer), men det körs inte på kundinnehåll.

**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Förskansa filer som ska överföras till Azure-resurser som inte är beräkning

**Vägledning:** Microsoft anti-malware är aktiverat på den underliggande värden som stöder Azure-tjänster (till exempel Azure Data Explorer), men det körs inte på kundinnehåll.

Förskansa allt innehåll som överförs till azure-resurser som inte är beräkning, till exempel Azure Data Explorer, DataSjölagring, Blob Storage, Azure-databas för PostgreSQL osv. Microsoft kan inte komma åt dina data i dessa fall.



**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Kund

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Se till att programvara och signaturer mot skadlig kod uppdateras

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

Microsoft anti-malware är aktiverat på den underliggande värden som stöder Azure-tjänster, men det körs inte på kundinnehåll.

**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Ej tillämpligt

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Säkerhetskontroll: Dataåterställning](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Säkerställa regelbundna automatiska säkerhetskopieringar

**Vägd:** Data i ditt Microsoft Azure-lagringskonto som används av datautforskaren replikeras alltid för att säkerställa hållbarhet och hög tillgänglighet. Azure Storage kopierar dina data så att de skyddas från planerade och oplanerade händelser, inklusive tillfälliga maskinvarufel, nätverks- eller strömavbrott och omfattande naturkatastrofer. Du kan välja att replikera dina data inom samma datacenter, över zonbaserade datacenter inom samma region eller i geografiskt åtskilda områden.

Så här fungerar Azure Storage-redundans- och servicenivåavtal:https://docs.microsoft.com/azure/storage/common/storage-redundancy

Exportera data till lagring:https://docs.microsoft.com/azure/kusto/management/data-export/export-data-to-storage



**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Utföra fullständiga systemsäkerhetskopior och säkerhetskopiera alla hanterade kundnycklar

**Vägledning**: Azure Data Explorer krypterar alla data i ett lagringskonto i vila. Som standard krypteras data med Microsoft-hanterade nycklar. Om du vill ha ytterligare kontroll över krypteringsnycklar kan du ange kundhanterade nycklar som ska användas för datakryptering. Kundhanterade nycklar måste lagras i ett Azure Key Vault. 

Konfigurera kundhanterade nycklar med C# :https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Konfigurera kundhanterade nycklar med azure Resource Manager-mallen:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager

Så här säkerhetskopierar Du Azure Key Vault-certifikat:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate?view=azurermps-6.13.0


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validera alla säkerhetskopior inklusive kundhanterade nycklar

**Vägledning**: Testa regelbundet dataåterställning av dina Azure Key Vault-hemligheter.

Så här återställer du Azure Key Vault-certifikat:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0 

Konfigurera kundhanterade nycklar med C# :https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Konfigurera kundhanterade nycklar med azure Resource Manager-mallen:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager



**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Säkerställa skydd för säkerhetskopior och kundhanterade nycklar

**Vägledning**: Kunden för att aktivera Soft-Delete i Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning.  Du kan också aktivera rensningsskydd så att om ett valv eller ett objekt i det borttagna tillståndet, kan det inte rensas förrän kvarhållningsperioden har passerats.  

Aktivera skydd för mjuk borttagning och rensning i Key Vault:https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete

Konfigurera kundhanterade nycklar med C# :https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Konfigurera kundhanterade nycklar med azure Resource Manager-mallen:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager



**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Säkerhetskontroll: Incident response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-incident-response-guide"></a>10.1: Skapa hanteringsguide för incidenthantering

**Vägledning**: Skapa en incidenthanteringsguide för din organisation. Se till att det finns skriftliga incidenthanteringsplaner som definierar alla roller för personal samt faser av incidenthantering/hantering från identifiering till granskning efter incidenten.
    

    Guidance on building your own security incident response process: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

    

    Microsoft Security Response Center's Anatomy of an Incident: https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/

    

    Customer may also leverage NIST's Computer Security Incident Handling Guide to aid in the creation of their own incident response plan: https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final

    



**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Kund

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2: Skapa bedömnings- och prioriteringsprocedur för incidenten

**Vägledning**: Security Center tilldelar allvarlighetsgrad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som ska undersökas först. Allvarlighetsgraden baseras på hur säker Security Center är i att hitta eller analytiska används för att utfärda aviseringen samt den konfidensnivå som det fanns ont uppsåt bakom aktiviteten som ledde till aviseringen. 
    

    Additionally, clearly mark subscriptions (for ex. production, non-prod) using tags and create a naming system to clearly identify and categorize Azure resources, especially those processing sensitive data.  It is your responsibility to prioritize the remediation of alerts based on the criticality of the Azure resources and environment where the incident occurred.

    

    Security alerts in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

    

    Use tags to organize your Azure resources: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="103-test-security-response-procedures"></a>10.3: Testsäkerhetssvarsprocedurer

**Vägledning**: Utför övningar för att testa systemens funktioner för incidentsvar på en regelbunden kadens för att skydda dina Azure-resurser. Identifiera svaga punkter och luckor och revidera planen efter behov.
    

    Refer to NIST's publication: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf



**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10.4: Ange kontaktinformation om säkerhetsincidenter och konfigurera varningsmeddelanden &nbsp;för säkerhetsincidenter

**Vägledning**: Kontaktinformation för säkerhetsincidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en olaglig eller obehörig part. Granska incidenter i efterhand för att säkerställa att problemen löses.
    

Så här ställer du in säkerhetskontakten för Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Införliva säkerhetsvarningar i ditt incidenthanteringssystem

**Vägledning**: Exportera dina Azure Security Center-aviseringar och rekommendationer med hjälp av funktionen Kontinuerlig export för att identifiera risker för Azure-resurser. Kontinuerlig export gör att du kan exportera aviseringar och rekommendationer antingen manuellt eller på ett kontinuerligt sätt. Du kan använda Azure Security Center-dataanslutningen för att strömma aviseringarna till Azure Sentinel.
    

    How to configure continuous export: https://docs.microsoft.com/azure/security-center/continuous-export

    

    How to stream alerts into Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center



**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisera svaret på säkerhetsvarningar

**Vägledning**: Använd funktionen Automatisering av arbetsflöde i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" på säkerhetsaviseringar och rekommendationer för att skydda dina Azure-resurser.
    

    How to configure Workflow Automation and Logic Apps: https://docs.microsoft.com/azure/security-center/workflow-automation



**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [Säkerhetskontroll: Penetrationstester och röda teamövningar](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1: Utför regelbundna penetrationstester av dina Azure-resurser och se till att åtgärda alla viktiga säkerhetsresultat inom 60 dagar

**Vägledning**: Följ Microsofts insatsregler för att säkerställa att dina https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1penetrationstester inte bryter mot Microsofts policyer: .

Du hittar mer information om Microsofts strategi och genomförande av Red Teaming och penetrationstester på webbplatser på flera webbplatser mot Microsofts hanterade molninfrastruktur, tjänster och program, här:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Delat

## <a name="next-steps"></a>Nästa steg

- Se [Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Läs mer om [Azure Security-originalplaner](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
