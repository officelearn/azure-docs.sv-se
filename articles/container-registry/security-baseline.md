---
title: Azure Security Baseline för Azure-behållarregister
description: Azure Security Baseline för Azure-behållarregister
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 2970ebf42acb87e93f8b827b3687b219da5867c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244298"
---
# <a name="azure-security-baseline-for-azure-container-registry"></a>Azure Security Baseline för Azure-behållarregister

Azure Security Baseline for Azure Container Registry innehåller rekommendationer som hjälper dig att förbättra säkerhetspositionen för distributionen.

Baslinjen för den här tjänsten hämtas från [Azure Security Benchmark version 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), som ger rekommendationer om hur du kan skydda dina molnlösningar på Azure med vår vägledning om bästa praxis.

Mer information finns i [översikt över Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Säkerhetskontroll: Nätverkssäkerhet](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Skydda resurser med nätverkssäkerhetsgrupper eller Azure-brandväggen i det virtuella nätverket

**Vägledning**: Azure Virtual Network tillhandahåller säkra, privata nätverk för dina Azure- och lokala resurser. Genom att begränsa åtkomsten till ditt privata Azure-behållarregister från ett virtuellt Azure-nätverk ser du till att endast resurser i det virtuella nätverket får åtkomst till registret. För scenarier över flera lokaler kan du också konfigurera brandväggsregler så att registeråtkomst endast tillåts från specifika IP-adresser. Bakom en brandvägg konfigurerar du regler för brandväggsåtkomst och tjänsttaggar för att komma åt behållarregistret.

Begränsa åtkomsten till ett Azure-behållarregister med hjälp av en Azure-regler för virtuellt nätverk eller brandvägg:https://docs.microsoft.com/azure/container-registry/container-registry-vnet 

Konfigurera regler för åtkomst till ett Azure-behållarregister bakom en brandvägg:https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules


**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Övervaka och logga konfiguration och trafik av virtuella nätverk, undernät och nätverkskort

**Vägledning**: Använd Azure Security Center och åtgärda rekommendationer för nätverksskydd för att skydda dina nätverksresurser i Azure. Aktivera NSG-flödesloggar och skicka loggar till ett lagringskonto för trafikgranskning.

Så här aktiverar du NSG-flödesloggar:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Skydda dina nätverksresurser:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations



**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="13-protect-critical-web-applications"></a>1.3: Skydda kritiska webbprogram

**Vägledning**: Ej tillämpligt. Benchmark är avsett för Azure App Service eller beräkningsresurser som är värdar för webbprogram.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Aktivera DDoS Standard-skydd i dina virtuella nätverk för skydd mot DDoS-attacker. Använd Azure Security Center Integrated Threat Intelligence för att neka kommunikation med kända skadliga eller oanvända IP-adresser på Internet.  Distribuera Azure-brandväggen vid var och en av organisationens nätverksgränser med Threat Intelligence aktiverat och konfigurerat för att "Varna och neka" för skadlig nätverkstrafik.

Du kan använda Azure Security Center Just In Time Network access för att konfigurera NSG:er för att begränsa exponeringen av slutpunkter till godkända IP-adresser under en begränsad period. Använd också Azure Security Center Adaptive Network Hardening för att rekommendera NSG-konfigurationer som begränsar portar och käll-IPs baserat på faktisk trafik- och hotinformation.

KonfigureraR du DDoS-skydd:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection


Distribuerar Azure-brandväggen:https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Förstå Azure Security Center Integrated Threat Intelligence:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Förstå Azure Security Center Adaptive Network Hardening:https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Azure Security Center precis i tid nätverksåtkomstkontroll:https://docs.microsoft.com/azure/security-center/security-center-just-in-time


**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Spela in nätverkspaket och flödesloggar

**Vägledning**: Aktivera NSG-flödesloggar (Network Security Group) för NSG som är anslutna till undernätet som används för att skydda ditt Azure-behållarregister. Du kan registrera NSG-flödesloggarna till ett Azure Storage-konto för att generera flödesposter. Om det behövs för att undersöka avvikande aktivitet aktiverar du Azure Network Watcher-paketfångst.

Så här aktiverar du NSG-flödesloggar:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Aktivera Network Watcher:https://docs.microsoft.com/azure/network-watcher/network-watcher-create


**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuera nätverksbaserade system för intrångsdetektering/intrångsförebyggande (IDS/IPS)

**Vägledning**: Välj ett erbjudande från Azure Marketplace som stöder IDS/IPS-funktioner med funktioner för inspektion av nyttolast. Om intrångsidentifiering och/eller förebyggande baserat på nyttolastinspektion inte är ett krav kan Azure Firewall with Threat Intelligence användas. Azure Firewall Threat Intelligence-baserad filtrering kan varna och neka trafik till och från kända skadliga IP-adresser och domäner. IP-adresser och domäner kommer från Microsoft Threat Intelligence-feeden.

Distribuera den brandväggslösning som du väljer vid var och en av organisationens nätverksgränser för att identifiera och/eller neka skadlig trafik.

Azure Marketplace:https://azuremarketplace.microsoft.com/marketplace/?term=Firewall 

Distribuerar Azure-brandväggen:https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Konfigurera aviseringar med Azure-brandväggen:https://docs.microsoft.com/azure/firewall/threat-intel


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Hantera trafik till webbapplikationer

**Vägledning**: Ej tillämpligt. Benchmark är avsett för webbprogram som körs på Azure App Service eller beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimera komplexiteten och de administrativa omkostnaderna för nätverkssäkerhetsreglerna

**Vägd:** För resurser som behöver åtkomst till behållarregistret använder du taggar för virtuella nätverkstjänst för Azure Container Registry-tjänsten för att definiera nätverksåtkomstkontroller i nätverkssäkerhetsgrupper eller Azure-brandväggen. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange tjänsttagnamnet "AzureContainerRegistry" i lämpligt käll- eller målfält för en regel kan du tillåta eller neka trafik för motsvarande tjänst. Microsoft hanterar adressprefixen som omfattas av servicetag och uppdaterar automatiskt servicetag när adresserna ändras.

Tillåt åtkomst via tjänstmärke:https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules#allow-access-by-service-tag


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Underhåll standardsäkerhetskonfigurationer för nätverksenheter

**Vägledning**: Definiera och implementera standardsäkerhetskonfigurationer för nätverksresurser som är associerade med dina Azure-behållarregister med Azure Policy. Använd Azure Policy-alias i namnområdena "Microsoft.ContainerRegistry" och "Microsoft.Network" för att skapa anpassade principer för granskning eller framtvingning av nätverkskonfigurationen för behållarregister. 

Du kan använda Azure Blueprints för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljöartefakter, till exempel Azure Resource Manager-mallar, RBAC-kontroller och principer, i en enda skissdefinition. Använd enkelt skissen på nya prenumerationer och finjustera kontroll och hantering genom versionshantering.

Granska efterlevnad av Azure-behållarregister med Azure-princip:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Så här skapar du en Azure Blueprint:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regler för konfiguration av dokumenttrafik

**Vägledning**: Kunden kan använda Azure Blueprints för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljöartefakter, till exempel Azure Resource Manager-mallar, RBAC-kontroller och principer, i en enda skissdefinition. Använd enkelt skissen på nya prenumerationer och finjustera kontroll och hantering genom versionshantering.

Så här skapar du en Azure Blueprint:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal



**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Använd automatiserade verktyg för att övervaka nätverksresurskonfigurationer och identifiera ändringar

**Vägledning**: Använd Azure Activity Log för att övervaka nätverksresurskonfigurationer och identifiera ändringar för nätverksresurser relaterade till dina behållarregister. Skapa aviseringar i Azure Monitor som utlöses när ändringar av kritiska nätverksresurser sker.

Så här visar och hämtar du Azure Activity Log-händelser:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Så här skapar du aviseringar i Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [Säkerhetskontroll: Loggning och övervakning](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Använd godkända tidssynkroniseringskällor

**Vägledning**: Microsoft underhåller tidskällor för Azure-resurser, men du har möjlighet att hantera tidssynkroniseringsinställningarna för dina beräkningsresurser.

Konfigurera tidssynkronisering för Azure-beräkningsresurser:https://docs.microsoft.com/azure/virtual-machines/windows/time-sync


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurera hantering av centrala säkerhetsloggar

**Vägledning:** Ingest loggar via Azure Monitor för att aggregera säkerhetsdata som genereras av ett Azure-behållarregister. I Azure Monitor använder du Log Analytics Workspace(s) för att fråga och utföra analyser och använda Azure Storage-konton för långsiktig/arkiveringslagring.

Azure Container Registry loggar för diagnostikutvärdering och granskning:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs



**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivera granskningsloggning för Azure-resurser

**Vägledning**: Azure Monitor samlar in resursloggar (tidigare kallade diagnostikloggar) för användardrivna händelser i registret. Samla in och använda dessa data för att granska registerautentiseringshändelser och tillhandahålla ett fullständigt aktivitetsspår på registerartefakter som pull- och push-händelser så att du kan diagnostisera säkerhetsproblem med registret.

Azure Container Registry loggar för diagnostikutvärdering och granskning:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Samla in säkerhetsloggar från operativsystem

**Vägledning**: Ej tillämpligt. Benchmark är avsett för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurera lagring av säkerhetslogglagring

**Vägledning**: Inom Azure Monitor anger du lagringsperioden för Logganalysarbetsyta enligt organisationens efterlevnadsregler. Använd Azure Storage-konton för långsiktig/arkiveringslagring.

Så här anger du loggkvarhållningsparametrar för Logganalysarbetsytor:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2.6: Övervaka och granska loggar

**Vägledning**: Analysera och övervaka Azure Container Registry loggar för avvikande beteende och regelbundet granska resultat. Använd Azure Monitor's Log Analytics Workspace för att granska loggar och utföra frågor om loggdata.

Azure Container Registry loggar för diagnostikutvärdering och granskning:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Förstå logganalys arbetsyta:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Så här utför du anpassade frågor i Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Aktivera aviseringar för avvikande aktivitet

**Vägledning**: Använd Azure Log Analytics-arbetsytan för övervakning och avisering om avvikande aktiviteter i säkerhetsloggar och händelser relaterade till ditt Azure-behållarregister.

Azure Container Registry loggar för diagnostikutvärdering och granskning:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Så här varnar du för logganalysloggdata:https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralisera loggning av skadlig kod

**Vägledning**: Ej tillämpligt. Azure Container Registry bearbetar inte eller producerar anti-malware relaterade loggar.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivera DNS-frågeloggning

**Vägledning**: Ej tillämpligt. Azure Container Registry är en slutpunkt och initierar inte kommunikation och tjänsten frågar inte DNS.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivera granskningsloggning på kommandoraden

**Vägledning**: Ej tillämpligt. Benchmark är avsett för beräkningsresurser.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Säkerhetskontroll: Identitets- och åtkomstkontroll](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Föra en inventering av administrativa räkenskaper

**Vägledning**: Azure Active Directory (Azure AD) har inbyggda roller som uttryckligen måste tilldelas och kan ifrågasättas. Använd Azure AD PowerShell-modulen för att utföra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

För varje Azure-behållarregister kan du spåra om det inbyggda administratörskontot är aktiverat eller inaktiverat. Inaktivera kontot när det inte används.

Så här skaffar du en katalogroll i Azure AD med PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Så här hämtar du medlemmar i en katalogroll i Azure AD med PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Administratörskonto för Azure-behållarregister:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account


**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändra standardlösenord där så är tillämpligt

**Vägledning**: Azure Active Directory (Azure AD) har inte begreppet standardlösenord. Andra Azure-resurser som kräver ett lösenord tvingar ett lösenord att skapas med komplexitetskrav och en minsta lösenordslängd, som skiljer sig åt beroende på tjänsten. Du är ansvarig för program från tredje part och Marketplace-tjänster som kan använda standardlösenord.

Om standardadministratörskontot för ett Azure-behållarregister är aktiverat skapas komplexa lösenord automatiskt och bör roteras. Inaktivera kontot när det inte används.

Administratörskonto för Azure-behållarregister:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Använd särskilda administrativa konton

**Vägledning**: Skapa standardrutiner kring användningen av särskilda administrativa konton. Använd Azure Security Center Identity and Access Management för att övervaka antalet administrativa konton.

Skapa också procedurer för att aktivera det inbyggda administratörskontot för ett behållarregister. Inaktivera kontot när det inte används.

Förstå Azure Security Center identitet och åtkomst:https://docs.microsoft.com/azure/security-center/security-center-identity-access

Administratörskonto för Azure-behållarregister:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Använd enkel inloggning (SSO) med Azure Active Directory

**Vägledning**: Använd azure Active Directory SSO om möjligt i stället för att konfigurera enskilda fristående autentiseringsuppgifter per tjänst. Använd Azure Security Center Identity and Access Management-rekommendationer.

För individuell åtkomst till behållarregistret använder du individuell inloggning integrerad med Azure Active Directory.

Förstå SSO med Azure AD:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

Individuell inloggning till ett behållarregister:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#individual-login-with-azure-ad


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Använda multifaktorautentisering för all Azure Active Directory-baserad åtkomst

**Vägledning:** Aktivera MFA (Azure Active Directory) multifaktorautentisering (MFA) och följ Azure Security Center Identity and Access Management-rekommendationerna.

Aktivera MFA i Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Så här övervakar du identitet och åtkomst i Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Använd dedikerade datorer (arbetsstationer för privilegierad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd PAWs (privilegierade åtkomstarbetsstationer) med MFA konfigurerat för att logga in på och konfigurera Azure-resurser.

Läs mer om arbetsstationer för privilegierad åtkomst:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Aktivera MFA i Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Övervakning av Azure Security Center**: Saknas

**Ansvar**: Kund

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Logga och varna för misstänkt aktivitet från administrativa konton

**Vägledning**: Använd Azure Active Directory (Azure AD) säkerhetsrapporter för generering av loggar och aviseringar när misstänkt eller osäker aktivitet inträffar i miljön. Använd Azure Security Center för att övervaka identitets- och åtkomstaktivitet.

Identifiera Azure AD-användare som flaggats för riskfylld aktivitet:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Så här övervakar du användarnas identitets- och åtkomstaktivitet i Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Hantera Azure-resurser från endast godkända platser

**Vägledning**: Använd namngivna platser för villkorlig åtkomst för att tillåta åtkomst från endast specifika logiska grupperingar av IP-adressintervall eller länder/regioner.

Konfigurera namngivna platser i Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="39-use-azure-active-directory"></a>3.9: Använd Azure Active Directory

**Vägledning**: Använd Azure Active Directory (Azure AD) som det centrala autentiserings- och auktoriseringssystemet. Azure AD skyddar data genom att använda stark kryptering för data i vila och under överföring. Azure AD saltar också, hashar och lagrar användarautentiseringsuppgifter på ett säkert sätt.

Så här skapar och konfigurerar du en Azure AD-instans:https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelbundet granska och förena användaråtkomst

**Vägledning**: Azure Active Directory (Azure AD) innehåller loggar som hjälper dig att identifiera inaktuella konton. Dessutom kan du använda Azure Identity Access Reviews för att effektivt hantera gruppmedlemskap, åtkomst till företagsprogram och rolltilldelningar. Användaråtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.

Förstå Azure AD-rapportering:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Så här använder du Granskning av Azure Identity Access:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview



**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Övervaka försök att komma åt inaktiverade konton

**Vägledning**: Du har tillgång till Azure Active Directory (Azure AD) inloggningsaktivitet, granskning och riskhändelseloggkällor, som gör att du kan integrera med alla siem-verktyg (Security Information and Event Management).

Du kan effektivisera den här processen genom att skapa diagnostikinställningar för Azure Active Directory-användarkonton och skicka granskningsloggar och inloggningsloggar till en Logganalysarbetsyta. Du kan konfigurera önskade aviseringar i Log Analytics Workspace.

Så här integrerar du Azure Activity Logs i Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Varning på kontots avvikelse för inloggningsbeteende

**Vägledning**: Använd Azure Active Directory (Azure AD) Risk- och Identity Protection-funktioner för att konfigurera automatiska svar på identifierade misstänkta åtgärder relaterade till användaridentiteter. 

Så här visar du riska 2-inloggningar för Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Konfigurera och aktivera riskprinciper för identitetsskydd:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ge Microsoft åtkomst till relevanta kunddata under supportscenarier

**Vägledning**: Ej tillgänglig. Customer Lockbox stöds för närvarande inte för Azure Container Registry.

Lista över tjänster som stöds av Customer Lockbox:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Säkerhetskontroll: Dataskydd](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Föra en inventering av känslig information

**Vägledning**: Använd resurstaggar för att hjälpa till att spåra Azure-behållarregister som lagrar eller bearbetar känslig information.

Tagga och versionsbehållare eller andra artefakter i ett register och låsa avbildningar eller databaser för att hjälpa till att spåra bilder som lagrar eller bearbetar känslig information.

Så här skapar och använder du taggar:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Rekommendationer för taggning och versionshantering av behållaravbildningar:https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version

Låsa en behållaravbildning i ett Azure-behållarregister:https://docs.microsoft.com/azure/container-registry/container-registry-image-lock



**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolera system som lagrar eller bearbetar känslig information

**Vägd**: Implementera separata behållarregister, prenumerationer och/eller hanteringsgrupper för utveckling, test och produktion. Resurser som lagrar eller bearbetar känsliga data bör vara tillräckligt isolerade.

Resurser ska avgränsas med virtuellt nätverk eller undernät, taggas på rätt sätt och skyddas av en nätverkssäkerhetsgrupp (NSG) eller Azure-brandväggen.

Så här skapar du ytterligare Azure-prenumerationer:https://docs.microsoft.com/azure/billing/billing-create-subscription

Så här skapar du hanteringsgrupper:https://docs.microsoft.com/azure/governance/management-groups/create

Så här skapar och använder du taggar:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Begränsa åtkomsten till ett Azure-behållarregister med hjälp av en Azure-regler för virtuellt nätverk eller brandvägg:https://docs.microsoft.com/azure/container-registry/container-registry-vnet

Hur man skapar en NSG med en säkerhet config:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Distribuerar Azure-brandväggen:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Konfigurera avisering eller avisering och neka med Azure-brandväggen:

https://docs.microsoft.com/azure/firewall/threat-intel



**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Övervaka och blockera obehörig överföring av känslig information

**Vägledning**: Distribuera ett automatiserat verktyg på nätverksper perimeterer som övervakar för obehörig överföring av känslig information och blockerar sådana överföringar samtidigt som informationssäkerhetsexperter varnas.

För den underliggande plattformen som hanteras av Microsoft behandlar Microsoft allt kundinnehåll som känsligt och gör stora ansträngningar för att skydda mot förlust av kunddata och exponering. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och underhållit en uppsättning robusta dataskyddskontroller och funktioner.

Förstå kunddataskyddet i Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Delat

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Kryptera all känslig information under transport

**Vägledning**: Se till att alla klienter som ansluter till ditt Azure-behållarregister kan förhandla om TLS 1.2 eller senare. Microsoft Azure-resurser förhandlar om TLS 1.2 som standard.

Följ Azure Security Center-rekommendationer för kryptering i vila och kryptering under överföring, där så är tillämpligt.

Förstå kryptering under överföring med Azure:https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit



**Övervakning av Azure Security Center**: Ja

**Ansvar**: Delat

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Använda ett aktivt identifieringsverktyg för att identifiera känsliga data

**Vägledning**: Funktioner för dataidentifiering, klassificering och förlustförebyggande är ännu inte tillgängliga för Azure Container Registry. Implementera tredjepartslösning om det behövs för efterlevnadsändamål.

För den underliggande plattformen som hanteras av Microsoft behandlar Microsoft allt kundinnehåll som känsligt och gör stora ansträngningar för att skydda mot förlust av kunddata och exponering. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och underhållit en uppsättning robusta dataskyddskontroller och funktioner.

Förstå kunddataskyddet i Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Delat

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Använd Azure RBAC för att kontrollera åtkomsten till resurser

**Vägledning**: Använd Azure Active Directory (Azure AD) RBAC för att styra åtkomsten till data och resurser i ett Azure-behållarregister. 

Konfigurera RBAC i Azure:https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Azure Container Registry roller och behörigheter:https://docs.microsoft.com/azure/container-registry/container-registry-roles



**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Använd värdbaserad dataförlustprevention för att genomdriva åtkomstkontroll

**Vägd**: Om det behövs för efterlevnad av beräkningsresurser implementerar du ett verktyg från tredje part, till exempel en automatiserad värdbaserad lösning för att förhindra dataförlust, för att framtvinga åtkomstkontroller till data även när data kopieras från ett system.

För den underliggande plattformen som hanteras av Microsoft behandlar Microsoft allt kundinnehåll som känsligt och gör stora ansträngningar för att skydda mot förlust av kunddata och exponering. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och underhållit en uppsättning robusta dataskyddskontroller och funktioner.

Förstå kunddataskyddet i Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Delat

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Kryptera känslig information i vila

**Vägledning**: Använd kryptering i vila på alla Azure-resurser. Som standard krypteras alla data i ett Azure-behållarregister i vila med hjälp av Microsoft-hanterade nycklar.

Förstå kryptering i vila i Azure:https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Kundhanterade nycklar i Azure Container Registry:https://aka.ms/acr/cmk



**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logga och avisera om ändringar i kritiska Azure-resurser

**Vägledning**: Azure Monitor samlar in resursloggar (tidigare kallade diagnostikloggar) för användardrivna händelser i registret. Samla in och använda dessa data för att granska registerautentiseringshändelser och tillhandahålla ett fullständigt aktivitetsspår på registerartefakter som pull- och pull-händelser så att du kan diagnostisera driftproblem med registret.

Azure Container Registry loggar för diagnostikutvärdering och granskning:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Säkerhetskontroll: Sårbarhetshantering](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Kör automatiserade sårbarhetsskanningsverktyg

**Vägledning**: Följ rekommendationer från Azure Security Center om hur du utför sårbarhetsbedömningar på dina behållaravbildningar. Du kan också distribuera lösningar från tredje part från Azure Marketplace för att utföra bedömning av avbildningssäkerhetsproblem.

Så här implementerar du rekommendationer för bedömning av sårbarhetsbedömning av Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

Azure Container Registry-integrering med Security Center (förhandsversion):https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Distribuera en lösning för hantering av automatisk operativsystemets korrigering

**Vägledning**: Microsoft utför korrigeringshantering på underliggande system som stöder Azure Container Registry.

Automatisera uppdateringar av behållaravbildningar när uppdateringar av basavbildningar från operativsystem och andra korrigeringsfiler identifieras.

Om basavbildningsuppdateringar för Azure Container Registry-uppgifter:https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Distribuera automatisk lösning för hantering av programkorrigering från tredje part

**Vägledning**: Du kan använda tredjepartslösning för att korrigera programbilder.  Du kan också köra Azure Container Registry uppgifter för att automatisera uppdateringar till programavbildningar i ett behållarregister baserat på säkerhetskorrigeringar eller andra uppdateringar i basavbildningar.

Om basavbildningsuppdateringar för ACR-uppgifter:https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images



**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Jämför sårbarhetssökningar från rygg mot rygg

**Vägledning**: Integrera Azure Container Registry (ACR) med Azure Security Center för att möjliggöra regelbunden skanning av behållaravbildningar för säkerhetsproblem. Du kan också distribuera lösningar från tredje part från Azure Marketplace för att utföra regelbundna avbildningsssã¶ndesökningar.

Azure Container Registry-integrering med Security Center (förhandsversion):https://docs.microsoft.com/azure/security-center/azure-container-registry-integration


**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Använd en riskklassificeringsprocess för att prioritera reparation av upptäckta sårbarheter

**Vägledning**: Integrera Azure Container Registry (ACR) med Azure Security Center för att möjliggöra regelbunden skanning av behållaravbildningar för säkerhetsproblem och för att klassificera risker. Du kan också distribuera tredjepartslösningar från Azure Marketplace för att utföra regelbundna bildsår och riskklassificering.

Azure Container Registry-integrering med Security Center (förhandsversion):https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Övervakning av Azure Security Center**: Saknas

**Ansvar**: Kund

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Använd Identifiering av Azure Asset

**Vägledning**: Använd Azure Resource Graph för att fråga/identifiera alla resurser (till exempel beräkning, lagring, nätverk, portar och protokoll osv.) i dina prenumerationer.  Kontrollera lämpliga (läs) behörigheter i din klientorganisation och räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via Resource Graph, rekommenderas det starkt att skapa och använda Azure Resource Manager-resurser framöver.

Så här skapar du frågor med Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Så här visar du dina Azure-prenumerationer:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0 

Förstå Azure RBAC:https://docs.microsoft.com/azure/role-based-access-control/overview



**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="62-maintain-asset-metadata"></a>6.2: Underhåll metadata för tillgångar

**Vägledning**: Azure Container Registry underhåller metadata inklusive taggar och manifest för avbildningar i ett register. Följ rekommenderade metoder för att tagga artefakter.

Om register, databaser och bilder:https://docs.microsoft.com/azure/container-registry/container-registry-concepts

Rekommendationer för taggning och versionshantering av behållaravbildningar:https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ta bort obehöriga Azure-resurser

**Vägledning**: Azure Container Registry underhåller metadata inklusive taggar och manifest för avbildningar i ett register. Följ rekommenderade metoder för att tagga artefakter.

Om register, databaser och bilder:https://docs.microsoft.com/azure/container-registry/container-registry-concepts

Rekommendationer för taggning och versionshantering av behållaravbildningar:https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version



**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Underhåll en inventering av godkända Azure-resurser och programvarutitlar

**Vägledning**: Du måste skapa en inventering av godkända Azure-resurser enligt dina organisationsbehov.  

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Övervaka för icke godkända Azure-resurser

**Vägledning**: Använd Azure Policy för att sätta begränsningar för vilken typ av resurser som kan skapas i dina prenumerationer.

Använd Azure Resource Graph för att fråga/identifiera resurser i sina prenumerationer.  Se till att alla Azure-resurser som finns i miljön är godkända.

Granska efterlevnad av Azure-behållarregister med Azure-princip:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här skapar du frågor med Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Övervaka icke godkända program i beräkningsresurser

**Vägledning**: Analysera och övervaka Azure Container Registry loggar för avvikande beteende och regelbundet granska resultat. Använd Azure Monitor's Log Analytics Workspace för att granska loggar och utföra frågor om loggdata.

Azure Container Registry loggar för diagnostikutvärdering och granskning:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Förstå logganalys arbetsyta:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Så här utför du anpassade frågor i Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Ta bort icke godkända Azure-resurser och program

**Vägledning**: Azure Automation ger fullständig kontroll vid distribution, driftsättning och avveckling av arbetsbelastningar och resurser.  Du kan implementera din egen lösning för att ta bort obehöriga Azure-resurser. En introduktion till Azure Automation:https://docs.microsoft.com/azure/automation/automation-intro


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="68-use-only-approved-applications"></a>6.8: Använd endast godkända ansökningar

**Vägledning**: Ej tillämpligt. Benchmark är utformat för beräkningsresurser.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="69-use-only-approved-azure-services"></a>6.9: Använd endast godkända Azure-tjänster

**Vägledning**: Utnyttja Azure-principen för att begränsa vilka tjänster du kan etablera i din miljö.

Granska efterlevnad av Azure-behållarregister med Azure-princip:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här nekar du en viss resurstyp med Azure Policy:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="610-implement-approved-application-list"></a>6.10: Genomföra godkänd ansökningslista

**Vägledning**: Ej tillämpligt. Benchmark är utformat för beräkningsresurser.



**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: Begränsa användarnas möjlighet att interagera med AzureResources Manager via skript

**Vägledning**: Använd operativsystemspecifika konfigurationer eller resurser från tredje part för att begränsa användarnas möjlighet att köra skript i Azure-beräkningsresurser.

Konfigurera villkorlig åtkomst för att blockera åtkomst till Azure Resources Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Begränsa användarnas möjlighet att köra skript i beräkningsresurser

**Vägledning**: Använd operativsystemspecifika konfigurationer eller resurser från tredje part för att begränsa användarnas möjlighet att köra skript i Azure-beräkningsresurser.

Så här styr du exempelvis PowerShell-skriptkörning i Windows-miljöer:https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fysiskt eller logiskt segregera högriskapplikationer

**Vägledning**: Programvara som krävs för affärsverksamhet, men som kan medföra högre risk för organisationen, bör isoleras inom sin egen virtuella dator och/eller virtuella nätverk och vara tillräckligt säker med antingen en Azure-brandvägg eller nätverkssäkerhetsgrupp.

Så här skapar du ett virtuellt nätverk:https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Hur man skapar en NSG med en säkerhet config:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Säkerhetskontroll: Säker konfiguration](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: Använd Azure Policy eller Azure Security Center för att underhålla säkerhetskonfigurationer för alla Azure-resurser.

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Granska efterlevnad av Azure-behållarregister med Azure-princip:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Upprätta konfigurationer för säkra operativsystem

**Vägledning**: Använd Rekommendationen Azure Security Center "Åtgärda säkerhetsproblem i säkerhetskonfigurationer på dina virtuella datorer" för att upprätthålla säkerhetskonfigurationer på alla beräkningsresurser.

Så här övervakar du Rekommendationerna för Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-recommendations

Åtgärda Azure Security Center-rekommendationer:https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Underhåll säkra Azure-resurskonfigurationer

**Vägledning**: Använd Azure-principen [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar för dina Azure-resurser.

Granska efterlevnad av Azure-behållarregister med Azure-princip:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Förstå Azure-principeffekter:https://docs.microsoft.com/azure/governance/policy/concepts/effects



**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Underhåll konfigurationer av säkra operativsystem

**Vägledning**: Ej tillämpligt. Benchmark är avsett för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Delat

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Säkert lagra konfiguration av Azure-resurser

**Vägledning:** Om du använder anpassade Azure-principdefinitioner använder du Azure Repos för att lagra och hantera din kod på ett säkert sätt.

Så här lagrar du kod i Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Dokumentation för Azure Repos:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Lagra anpassade operativsystemavbildningar på ett säkert sätt

**Vägledning**: Ej tillämpligt. Benchmark gäller för beräkningsresurser.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Distribuera verktyg för hantering av systemkonfigurationer

**Vägledning**: Använd Azure-princip för att avisera, granska och tillämpa systemkonfigurationer. Dessutom, utveckla en process och pipeline för att hantera principundantag.

Granska efterlevnad av Azure-behållarregister med Azure-princip:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Distribuera verktyg för systemkonfigurationshantering för operativsystem

**Vägledning**: Ej tillämpligt. Benchmark gäller för beräkningsresurser.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementera automatisk konfigurationsövervakning för Azure-tjänster

**Vägledning**: Använd Azure Security Center för att utföra baslinjesökningar för dina Azure-resurser.

Använd Azure Policy för att sätta begränsningar för vilken typ av resurser som kan skapas i dina prenumerationer.

Åtgärda rekommendationer i Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

Granska efterlevnad av Azure-behållarregister med Azure-princip:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy



**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementera automatisk konfigurationsövervakning för operativsystem

**Vägledning**: Ej tillämpligt. Benchmark gäller för beräkningsresurser.


**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="711-manage-azure-secrets-securely"></a>7.11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: Använd hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda hemlig hantering för dina molnprogram.

Så här integrerar du med Azure-hanterade identiteter:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Så här skapar du ett nyckelvalv:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Så här tillhandahåller du Key Vault-autentisering med en hanterad identitet:https://docs.microsoft.com/azure/key-vault/managed-identity

Använda en Azure-hanterad identitet i Azure Container Registry uppgifter:https://docs.microsoft.com/azure/container-registry/container-registry-tasks-authentication-managed-identity


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Hantera identiteter på ett säkert och automatiskt sätt

**Vägledning**: Använd hanterade identiteter för att tillhandahålla Azure-tjänster med en automatiskt hanterad identitet i Azure AD. Med hanterade identiteter kan du autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan några autentiseringsuppgifter i koden.

Konfigurerar hanterade identiteter:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Använd en hanterad identitet för att autentisera till ett Azure-behållarregister:https://docs.microsoft.com/azure/container-registry/container-registry-authentication-managed-identity


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: Implementera autentiseringsskanner för att identifiera autentiseringsuppgifter i koden. Autentiseringsläsare kommer också att uppmuntra flytta identifierade autentiseringsuppgifter till säkrare platser som Azure Key Vault.

Så här konfigurerar du autentiseringsskanner:https://secdevtools.azurewebsites.net/helpcredscan.html


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Använd centralt hanterad programvara mot skadlig kod

**Vägledning**: Använd Microsoft Antimalware för Azure Cloud Services och virtuella datorer för att kontinuerligt övervaka och försvara dina resurser. För Linux använder du lösningen mot skadlig kod från tredje part.

Konfigurera Microsoft Antimalware för molntjänster och virtuella datorer:https://docs.microsoft.com/azure/security/fundamentals/antimalware


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Förskansa filer som ska överföras till Azure-resurser som inte är beräkning

**Vägledning**: Microsoft Antimalware är aktiverat på den underliggande värden som stöder Azure-tjänster (till exempel Azure Container Registry), men det körs inte på kundinnehåll.

Förskansa alla filer som överförs till azure-resurser som inte är beräkning, till exempel App Service, DataSjölagring, Blob Storage osv.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Se till att programvara och signaturer mot skadlig kod uppdateras

**Vägledning**: Ej tillämpligt. Benchmark är avsett för beräkningsresurser. Microsoft hanterar skadlig kod mot skadlig kod för underliggande plattform.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Säkerhetskontroll: Dataåterställning](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Se till att regelbundna automatiska säkerhetskopieringar

**Vägledning**: Data i microsoft Azure-behållarregistret replikeras alltid automatiskt för att säkerställa hållbarhet och hög tillgänglighet. Azure Container Registry kopierar dina data så att de skyddas från planerade och oplanerade händelser

Du kan också geo-replikera ett behållarregister för att underhålla registerrepliker i flera Azure-regioner. 

Geo-replikering i Azure Container Registry:https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication



**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Utför fullständiga systemsäkerhetskopior och säkerhetskopiera alla kundhanterade nycklar

**Vägledning**: Säkerhetskopierar även behållaravbildningar genom att importera från ett register till ett annat.

Säkerhetskopiera kundhanterade nycklar i Azure Key Vault med hjälp av Azure-kommandoradsverktyg eller SDK:er.

Importera behållaravbildningar till ett behållarregister:https://docs.microsoft.com/azure/container-registry/container-registry-import-images

Så här säkerhetskopierar du nyckelnycklar i Azure:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validera alla säkerhetskopior inklusive kundhanterade nycklar

**Vägledning**: Testa återställning av säkerhetskopierade kundhanterade nycklar i Azure Key Vault med hjälp av Azure-kommandoradsverktyg eller SDK:er.

Så här återställer du Azure Key Vault-nycklar i Azure:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Se till att säkerhetskopior och kundhanterade nycklar skyddas

**Vägledning**: Du kan aktivera Mjukborttagning i Azure Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning.

Aktivera Mjuk borttagning i Key Vault:https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Säkerhetskontroll: Incident response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Skapa en vägledning för incidenthantering

**Vägledning**: Skapa en incidenthanteringsguide för din organisation. Se till att det finns skriftliga incidenthanteringsplaner som definierar alla roller för personal samt faser av incidenthantering/hantering från identifiering till granskning efter incidenten.

Konfigurera arbetsflödesautomatiseringar i Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Vägledning om hur du bygger din egen process för hantering av säkerhetsincidenter:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Microsoft Security Response Center's Anatomy of an Incident:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Kunden kan också utnyttja NIST: s Computer Security Incident Handling Guide till stöd i skapandet av sin egen incidentresponsplan:https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf



**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Skapa en incidentbedömning och prioriteringsprocedur

**Vägledning**: Azure Security Center tilldelar en allvarlighetsgrad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som ska undersökas först. Allvarlighetsgraden baseras på hur säker Security Center är i att hitta eller analytiska används för att utfärda aviseringen samt den konfidensnivå som det fanns ont uppsåt bakom aktiviteten som ledde till aviseringen.

Dessutom tydligt markera abonnemang (för ex. produktion, icke-prod) och skapa ett namngivningssystem för att tydligt identifiera och kategorisera Azure-resurser.


**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="103-test-security-response-procedures"></a>10.3: Testsäkerhetssvarsprocedurer

**Vägledning**: Genomföra övningar för att testa systemens funktioner för incidenthantering på regelbunden kadens. Identifiera svaga punkter och luckor och revidera planen efter behov.

Se NIST:s publikation: Guide to Test, Training och Exercise Programs for IT Plans and Capabilities:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Ange kontaktinformation för säkerhetsincidenter och konfigurera varningsmeddelanden för säkerhetsincidenter

**Vägledning**: Kontaktinformation för säkerhetsincidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att kundens data har använts av en olaglig eller obehörig part.  Granska incidenter i efterhand för att säkerställa att problemen löses.

Så här ställer du in säkerhetskontakten för Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Införliva säkerhetsvarningar i ditt incidenthanteringssystem

**Vägledning**: Exportera dina Azure Security Center-aviseringar och rekommendationer med hjälp av funktionen Kontinuerlig export. Kontinuerlig export gör att du kan exportera aviseringar och rekommendationer antingen manuellt eller på ett kontinuerligt sätt. Du kan använda Azure Security Center-dataanslutningen för att strömma aviseringarna Sentinel.

Konfigurera kontinuerlig export:https://docs.microsoft.com/azure/security-center/continuous-export

Så här streamar du aviseringar till Azure Sentinel:https://docs.microsoft.com/azure/sentinel/connect-azure-security-center


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisera svaret på säkerhetsvarningar

**Vägledning**: Använd funktionen Automatisering av arbetsflöde i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" på säkerhetsaviseringar och rekommendationer.

Konfigurerar automatiserings- och logikappar för arbetsflöde:https://docs.microsoft.com/azure/security-center/workflow-automation


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [Säkerhetskontroll: Penetrationstester och röda teamövningar](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Utför regelbundna penetrationstester av dina Azure-resurser och säkerställer reparation av alla kritiska säkerhetsresultat inom 60 dagar

**Vägledning**: Följ Microsofts insatsregler för att säkerställa att dina penetrationstester inte bryter mot Microsofts policyer:https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Du hittar mer information om Microsofts strategi och genomförande av Red Teaming och penetrationstester på webbplatser på flera webbplatser mot Microsoft-hanterad molninfrastruktur, molntjänster och -program här:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e



**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Delat

## <a name="next-steps"></a>Nästa steg

- Se [Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Läs mer om [Azure Security-originalplaner](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
