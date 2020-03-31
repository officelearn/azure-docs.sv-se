---
title: Azure Security Baseline för Azure Cache för Redis
description: Azure Security Baseline för Azure Cache för Redis
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 364b87e4d64b8cc65fdf293032f4340bddec957c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479142"
---
# <a name="azure-security-baseline-for-azure-cache-for-redis"></a>Azure Security Baseline för Azure Cache för Redis

Azure Security Baseline för Azure Cache för Redis innehåller rekommendationer som hjälper dig att förbättra säkerhetspositionen för distributionen.

Baslinjen för den här tjänsten hämtas från [Azure Security Benchmark version 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), som ger rekommendationer om hur du kan skydda dina molnlösningar på Azure med vår vägledning om bästa praxis.

Mer information finns i [översikt över Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Säkerhetskontroll: Nätverkssäkerhet](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Skydda resurser med nätverkssäkerhetsgrupper eller Azure-brandväggen i det virtuella nätverket

**Vägledning**: Distribuera din Azure-cache för Redis-instans i ett virtuellt nätverk (VNet). Ett virtuella nätverk är ett privat nätverk i molnet. När en Azure Cache for Redis-instans är konfigurerad med ett virtuellt nätverk är den inte offentligt adresserbar och kan endast nås från virtuella datorer och program inom det virtuella nätverket.

Du kan också ange brandväggsregler med ett start- och slut-IP-adressintervall. När brandväggsregler har konfigurerats kan endast klientanslutningar från de angivna IP-adressintervallen ansluta till cachen.

Konfigurera stöd för virtuellt nätverk för en Premium Azure-cache för Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Konfigurera Azure Cache for Redis-brandväggsregler:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#firewall

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Övervaka och logga konfiguration och trafik av virtuella nätverk, undernät och nätverkskort

**Vägledning:** När virtuella datorer distribueras i samma virtuella nätverk som azure-cache för Redis-instans kan du använda NSG (Network Security Groups) för att minska risken för dataexfiltration. Aktivera NSG-flödesloggar och skicka loggar till ett Azure Storage-konto för trafikgranskning. Du kan också skicka NSG-flödesloggar till en Log Analytics-arbetsyta och använda Traffic Analytics för att ge insikter om trafikflödet i ditt Azure-moln. Vissa fördelar med Traffic Analytics är möjligheten att visualisera nätverksaktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödesmönster och identifiera felkonfigurationer i nätverket.

Aktivera NSG-flödesloggar:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Aktivera och använda Traffic Analytics:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="13-protect-critical-web-applications"></a>1.3: Skydda kritiska webbprogram

**Vägledning**: Ej tillämpligt. Den här rekommendationen är avsedd för webbprogram som körs på Azure App Service eller beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Neka kommunikation med kända skadliga IP-adresser

**Vägledning:** Azure Virtual Network (VNet) distribution ger förbättrad säkerhet och isolering för din Azure Cache för Redis, samt undernät, principer för åtkomstkontroll och andra funktioner för att ytterligare begränsa åtkomsten. När azure Cache for Redis distribueras i ett virtuellt nätverk är det inte offentligt adresserbart och kan endast nås från virtuella datorer och program i det virtuella nätverket.

Aktivera DDoS Protection Standard på de virtuella nätverk som är associerade med dina Azure-cache för Redis-instanser för att skydda mot ddos-attacker (Distributed Denial-of-Service). Använd Azure Security Center Integrated Threat Intelligence för att neka kommunikation med kända skadliga eller oanvända IP-adresser på Internet.

Konfigurera stöd för virtuellt nätverk för en Premium Azure-cache för Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Hantera Azure DDoS Protection Standard med Azure-portalen:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Spela in nätverkspaket och flödesloggar

**Vägledning:** När virtuella datorer distribueras i samma virtuella nätverk som azure-cache för Redis-instans kan du använda NSG (Network Security Groups) för att minska risken för dataexfiltration. Aktivera NSG-flödesloggar och skicka loggar till ett Azure Storage-konto för trafikgranskning. Du kan också skicka NSG-flödesloggar till en Log Analytics-arbetsyta och använda Traffic Analytics för att ge insikter om trafikflödet i ditt Azure-moln. Vissa fördelar med Traffic Analytics är möjligheten att visualisera nätverksaktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödesmönster och identifiera felkonfigurationer i nätverket.

Aktivera NSG-flödesloggar:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Aktivera och använda Traffic Analytics:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuera nätverksbaserade system för intrångsdetektering/intrångsförebyggande (IDS/IPS)

**Vägd:** När du använder Azure Cache for Redis med dina webbprogram som körs på Azure App Service eller beräkningsinstanser distribuerar du alla resurser inom ett Virtuellt Azure-nätverk (VNet) och skyddar med en Azure Web Application Firewall (WAF) på Web Application Gateway. Konfigurera WAF så att den körs i "Förebyggande läge". Förebyggande läge blockerar intrång och attacker som reglerna upptäcker. Angriparen får ett undantag för "403 obehörig åtkomst" och anslutningen stängs. Förebyggande läge registrerar sådana attacker i WAF-loggarna.

Alternativt kan du välja ett erbjudande från Azure Marketplace som stöder IDS/IPS-funktioner med funktioner för inspektion av nyttolast och/eller avvikelseidentifiering.

Förstå Azure WAF-funktioner:

https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview

Distribuerar Azure WAF:

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Hantera trafik till webbapplikationer

**Vägledning**: Ej tillämpligt. Den här rekommendationen är avsedd för webbprogram som körs på Azure App Service eller beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimera komplexiteten och de administrativa omkostnaderna för nätverkssäkerhetsreglerna

**Vägledning**: Använd taggar för virtuella nätverkstjänst för att definiera nätverksåtkomstkontroller för nätverkssäkerhetsgrupper (NSG) eller Azure-brandväggen. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange tjänsttagnamnet (t.ex. ApiManagement) i lämpligt käll- eller målfält för en regel kan du tillåta eller neka trafik för motsvarande tjänst. Microsoft hanterar adressprefixen som omfattas av servicetag och uppdaterar automatiskt servicetag när adresserna ändras.

Du kan också använda programsäkerhetsgrupper (ASG) för att förenkla komplex säkerhetskonfiguration. ASG-grupper kan du konfigurera nätverkssäkerhet som en naturlig förlängning av ett programs struktur, så att du kan gruppera virtuella datorer och definiera nätverkssäkerhetsprinciper baserat på dessa grupper.

Taggar för virtuella nätverkstjänster:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Säkerhetsgrupper för program:

https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Underhåll standardsäkerhetskonfigurationer för nätverksenheter

**Vägledning**: Definiera och implementera standardsäkerhetskonfigurationer för nätverksresurser relaterade till dina Azure-cache för Redis-instanser med Azure Policy. Använd Azure Policy-alias i namnområdena "Microsoft.Cache" och "Microsoft.Network" för att skapa anpassade principer för granskning eller framtvingning av nätverkskonfigurationen för dina Azure-cache för Redis-instanser. Du kan också använda dig av inbyggda principdefinitioner som:

Endast säkra anslutningar till Redis-cachen ska aktiveras

DDoS Protection Standard bör aktiveras

Du kan också använda Azure Blueprints för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljöartefakter, till exempel AZURE Resource Manager -mallar (ARM), rollbaserade åtkomstkontroll (RBAC) och principer, i en enda skissdefinition. Använd enkelt skissen på nya prenumerationer och miljöer och finjustera kontroll och hantering genom versionshantering.

Konfigurera och hantera Azure-princip:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här skapar du en Azure Blueprint:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regler för konfiguration av dokumenttrafik

**Vägledning**: Använd taggar för nätverksresurser som är associerade med Azure Cache for Redis-distributionen för att logiskt ordna dem till en taxonomi.

Så här skapar och använder du taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Använd automatiserade verktyg för att övervaka nätverksresurskonfigurationer och identifiera ändringar

**Vägd:** Använd Azure Activity-loggen för att övervaka nätverksresurskonfigurationer och identifiera ändringar för nätverksresurser relaterade till dina Azure-cache för Redis-instanser. Skapa aviseringar i Azure Monitor som utlöses när ändringar av kritiska nätverksresurser sker.

Så här visar och hämtar du Azure Activity Log-händelser:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Så här skapar du aviseringar i Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [Säkerhetskontroll: Loggning och övervakning](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Använd godkända tidssynkroniseringskällor

**Vägledning**: Microsoft underhåller tidskällan som används för Azure-resurser som Azure Cache for Redis för tidsstämplar i loggarna.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurera hantering av centrala säkerhetsloggar

**Vägledning**: Aktivera diagnostikinställningar för Azure Activity Log och skicka loggarna till en Log Analytics-arbetsyta, Azure-händelsehubb eller Azure-lagringskonto för arkiv. Aktivitetsloggar ger insikt i de åtgärder som utfördes på dina Azure-cache för Redis-instanser på kontrollplansnivå. Med hjälp av Azure Activity Log-data kan du bestämma vilka, vem och när för alla skrivåtgärder (PUT, POST, DELETE) som utförs på kontrollplansnivå för dina Azure-cache för Redis-instanser.

Aktivera diagnostikinställningar för Azure Activity Log:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivera granskningsloggning för Azure-resurser

**Vägledning**: Aktivera diagnostikinställningar för Azure Activity Log och skicka loggarna till en Log Analytics-arbetsyta, Azure-händelsehubb eller Azure-lagringskonto för arkiv. Aktivitetsloggar ger insikt i de åtgärder som utfördes på dina Azure-cache för Redis-instanser på kontrollplansnivå. Med hjälp av Azure Activity Log-data kan du bestämma vilka, vem och när för alla skrivåtgärder (PUT, POST, DELETE) som utförs på kontrollplansnivå för dina Azure-cache för Redis-instanser.

Även om mått är tillgängliga genom att aktivera diagnostikinställningar, är granskningsloggning på dataplanet ännu inte tillgängligt för Azure Cache för Redis.

Aktivera diagnostikinställningar för Azure Activity Log:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Samla in säkerhetsloggar från operativsystem

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurera lagring av säkerhetslogglagring

**Vägledning**: I Azure Monitor anger du loggkvarhållningsperiod för Log Analytics-arbetsytor som är associerade med dina Azure-cache för Redis-instanser enligt organisationens efterlevnadsregler.

Observera att granskningsloggning på dataplanet ännu inte är tillgängligt för Azure Cache för Redis.

Så här ställer du in loggkvarhållningsparametrar:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2.6: Övervaka och granska loggar

**Vägledning**: Aktivera diagnostikinställningar för Azure Activity Log och skicka loggarna till en Log Analytics-arbetsyta. Utför frågor i Logganalys för att söka efter termer, identifiera trender, analysera mönster och ge många andra insikter baserat på aktivitetsloggdata som kan ha samlats in för Azure Cache för Redis.

Observera att granskningsloggning på dataplanet ännu inte är tillgängligt för Azure Cache för Redis.

Aktivera diagnostikinställningar för Azure Activity Log:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

Samla in och analysera Azure-aktivitetsloggar i Log Analytics-arbetsytan i Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-collect

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Aktivera aviseringar för avvikande aktivitet

**Vägledning**: Du kan konfigurera för att ta emot aviseringar baserat på mått och aktivitetsloggar som är relaterade till dina Azure-cache för Redis-instanser. Med Azure Monitor kan du konfigurera en avisering för att skicka ett e-postmeddelande, ringa en webhook eller anropa en Azure Logic App.

Även om mått är tillgängliga genom att aktivera diagnostikinställningar, är granskningsloggning på dataplanet ännu inte tillgängligt för Azure Cache för Redis.

Konfigurera aviseringar för Azure Cache för Redis:https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-monitor#alerts

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralisera loggning av skadlig kod

**Vägledning**: Ej tillämpligt. Azure Cache för Redis bearbetar inte eller producerar anti-malware relaterade loggar.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivera DNS-frågeloggning

**Vägledning**: Ej tillämpligt. Azure Cache för Redis bearbetar inte eller producerar DNS-relaterade loggar.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivera granskningsloggning på kommandoraden

**Vägledning**: Ej tillämpligt. den här riktlinjen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Säkerhetskontroll: Identitets- och åtkomstkontroll](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Föra en inventering av administrativa räkenskaper

**Vägledning**: Azure Active Directory (AD) har inbyggda roller som uttryckligen måste tilldelas och kan ifrågasättas. Använd Azure AD PowerShell-modulen för att utföra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

Så här skaffar du en katalogroll i Azure AD med PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Så här hämtar du medlemmar i en katalogroll i Azure AD med PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändra standardlösenord där så är tillämpligt

**Vägledning**: Kontrollplanåtkomst till Azure Cache för Redis styrs via Azure Active Directory (AD). Azure AD har inte begreppet standardlösenord. 

Dataplanåtkomst till Azure Cache för Redis styrs via åtkomstnycklar. Dessa nycklar används av klienterna som ansluter till cacheminnet och kan återskapas när som helst.

Vi rekommenderar inte att du skapar standardlösenord i ditt program. I stället kan du lagra dina lösenord i Azure Key Vault och sedan använda Azure Active Directory för att hämta dem.

Så här återskapar du Azure Cache for Redis-åtkomstnycklar:https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#settings

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Delat

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Använd särskilda administrativa konton

**Vägledning**: Skapa standardrutiner kring användningen av särskilda administrativa konton. Använd Azure Security Center Identity and Access Management för att övervaka antalet administrativa konton.

Dessutom kan du använda rekommendationer från Azure Security Center eller inbyggda Azure-principer för att hjälpa dig att hålla reda på dedikerade administrativa kontona, till exempel:

- Det bör finnas fler än en ägare som tilldelats din prenumeration

- Inaktuella konton med ägarbehörigheter ska tas bort från din prenumeration

- Externa konton med ägarbehörigheter ska tas bort från din prenumeration

Så här använder du Azure Security Center för att övervaka identitet och åtkomst (förhandsversion):https://docs.microsoft.com/azure/security-center/security-center-identity-access

Så här använder du Azure Policy:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Använd enkel inloggning (SSO) med Azure Active Directory

**Vägledning**: Azure Cache för Redis använder åtkomstnycklar för att autentisera användare och stöder inte enkel inloggning (SSO) på dataplansnivå. Åtkomst till kontrollplanet för Azure Cache för Redis är tillgänglig via REST API och stöder SSO. Om du vill autentisera anger du auktoriseringshuvudet för dina begäranden till en JSON-webbtoken som du hämtar från Azure Active Directory.

Förstå Azure Cache för Redis REST API:https://docs.microsoft.com/rest/api/redis/

Förstå SSO med Azure AD:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Använda multifaktorautentisering för all Azure Active Directory-baserad åtkomst

**Vägledning:** Aktivera MFA (Azure Active Directory) MultiFaktor Authentication (MFA) och följ Azure Security Center Identity and Access Management-rekommendationerna.

Aktivera MFA i Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Så här övervakar du identitet och åtkomst i Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Använd dedikerade datorer (arbetsstationer för privilegierad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd paw (Privileged Access Workstations) med MFA (MultiFaktor Authentication) konfigurerat för att logga in på och konfigurera Azure-resurser.

Läs mer om arbetsstationer för privilegierad åtkomst:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Aktivera MFA i Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Logga och varna för misstänkt aktivitet från administrativa konton

**Vägledning**: Använd AZURE Active Directory (AD) Privilegierad identitetshantering (PIM) för generering av loggar och aviseringar när misstänkt eller osäker aktivitet inträffar i miljön.

Dessutom kan du använda Azure AD-riskidentifieringar för att visa aviseringar och rapporter om riskfyllda användarbeteenden.

Så här distribuerar du PIM (Privilegierad identitetshantering):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Förstå Azure AD-riskidentifieringar:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Hantera Azure-resurser från endast godkända platser

**Vägledning**: Konfigurera namngivna platser i Azure Active Directory (AD) Villkorlig åtkomst så att åtkomst tillåts från endast specifika logiska grupperingar av IP-adressintervall eller länder/regioner.

Konfigurera namngivna platser i Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="39-use-azure-active-directory"></a>3.9: Använd Azure Active Directory

**Vägledning**: Använd Azure Active Directory (AD) som det centrala autentiserings- och auktoriseringssystemet. Azure AD skyddar data genom att använda stark kryptering för data i vila och under överföring. Azure AD saltar också, hashar och lagrar användarautentiseringsuppgifter på ett säkert sätt.

Azure AD-autentisering kan inte användas för direkt åtkomst till Azure Cache for Redis dataplan, men Azure AD-autentiseringsuppgifter kan användas för administration på kontrollplansnivå (d.v.s. Azure-portalen) för att styra Azure Cache för Redis-åtkomstnycklar.


**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelbundet granska och förena användaråtkomst

**Vägledning**: Azure Active Directory (AD) innehåller loggar som hjälper dig att upptäcka inaktuella konton. Dessutom kan du använda Azure Identity Access Reviews för att effektivt hantera gruppmedlemskap, åtkomst till företagsprogram och rolltilldelningar. Användaråtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst. 

Förstå Azure AD-rapportering:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Så här använder du Azure Identity Access Reviews:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Övervaka försök att komma åt inaktiverade konton

**Vägledning**: Du har tillgång till Azure Active Directory (AD) inloggningsaktivitet, gransknings- och riskhändelseloggkällor, vilket gör att du kan integrera med Azure Sentinel eller en tredje part SIEM.

Du kan effektivisera den här processen genom att skapa diagnostikinställningar för Azure AD-användarkonton och skicka granskningsloggar och inloggningsloggar till en Log Analytics-arbetsyta. Du kan konfigurera önskade loggaviseringar i Log Analytics.

Så här integrerar du Azure Activity Logs i Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Så här går du ombord på Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Varning på kontots avvikelse för inloggningsbeteende

**Vägledning:** För avvikelse från kontoinloggningsbeteende på kontrollplanet använder du Azure Active Directory (AD) Identity Protection och riskidentifieringsfunktioner för att konfigurera automatiska svar på identifierade misstänkta åtgärder relaterade till användaridentiteter. Du kan också ange data i Azure Sentinel för vidare undersökning.

Så här visar du riska 2-inloggningar för Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Konfigurera och aktivera riskprinciper för identitetsskydd:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Så här går du ombord på Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ge Microsoft åtkomst till relevanta kunddata under supportscenarier

**Vägledning**: Ännu inte tillgänglig. Customer Lockbox stöds ännu inte för Azure Cache för Redis.

Lista över tjänster som stöds av Kundens låsbox:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Ej tillämpligt

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Säkerhetskontroll: Dataskydd](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Föra en inventering av känslig information

**Vägledning**: Använd taggar för att hjälpa till att spåra Azure-resurser som lagrar eller bearbetar känslig information.

Så här skapar och använder du taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolera system som lagrar eller bearbetar känslig information

**Vägledning**: Implementera separata prenumerationer och/eller hanteringsgrupper för utveckling, test och produktion. Azure Cache för Redis-instanser bör avgränsas med virtuellt nätverk/undernät och taggas på rätt sätt. Du kan också använda Azure Cache for Redis-brandväggen för att definiera regler så att endast klientanslutningar från angivna IP-adressintervall kan ansluta till cachen.

Så här skapar du ytterligare Azure-prenumerationer:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Så här skapar du hanteringsgrupper:

https://docs.microsoft.com/azure/governance/management-groups/create

Distribuera Azure-cache för Redis till ett Vnet:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Konfigurera Azure Cache for Redis-brandväggsregler:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#firewall

Så här skapar och använder du taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Övervaka och blockera obehörig överföring av känslig information

**Vägledning**: Ännu inte tillgänglig. Dataidentifiering, klassificering och förlustförebyggande funktioner är ännu inte tillgängliga för Azure Cache för Redis.

Microsoft hanterar den underliggande infrastrukturen för Azure Cache för Redis och har implementerat strikta kontroller för att förhindra förlust eller exponering av kunddata.

Förstå kunddataskyddet i Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Delat

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Kryptera all känslig information under transport

**Vägledning**: Azure Cache för Redis kräver TLS-krypterad kommunikation som standard. TLS-versionerna 1.0, 1.1 och 1.2 stöds för närvarande. TLS 1.0 och 1.1 är dock på väg att utfasa branschomfattande, så använd TLS 1.2 om det alls är möjligt. Om ditt klientbibliotek eller verktyg inte stöder TLS kan du aktivera okrypterade anslutningar via Azure-portalen eller hanterings-API:erna. I sådana fall där krypterade anslutningar inte är möjliga rekommenderas att cache- och klientprogrammet placeras i ett virtuellt nätverk.

Förstå kryptering under överföring för Azure Cache för Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-best-practices

Förstå nödvändiga portar som används i Vnet-cachescenarier:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet#outbound-port-requirements

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Delat

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Använda ett aktivt identifieringsverktyg för att identifiera känsliga data

**Vägledning**: Funktioner för dataidentifiering, klassificering och förlustförebyggande är ännu inte tillgängliga för Azure Cache för Redis. Tagga instanser som innehåller känslig information som sådan och implementera tredjepartslösning om det behövs för efterlevnadsändamål.

För den underliggande plattformen som hanteras av Microsoft behandlar Microsoft allt kundinnehåll som känsligt och gör stora ansträngningar för att skydda mot förlust av kunddata och exponering. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och underhållit en uppsättning robusta dataskyddskontroller och funktioner.

Förstå kunddataskyddet i Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Använd Azure RBAC för att kontrollera åtkomsten till resurser

**Vägledning**: Använd Azure Active Directory (AAD) rollbaserad åtkomstkontroll (RBAC) för att styra åtkomsten till Azure Cache for Redis-kontrollplanet (dvs. Azure-portalen). 

Konfigurera RBAC i Azure:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Använd värdbaserad dataförlustprevention för att genomdriva åtkomstkontroll

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

Microsoft hanterar den underliggande infrastrukturen för Azure Cache för Redis och har implementerat strikta kontroller för att förhindra förlust eller exponering av kunddata.

Förstå kunddataskyddet i Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Kryptera känslig information i vila

**Vägledning**: Azure Cache för Redis lagrar kunddata i minnet, och medan den är starkt skyddad av många kontroller som implementeras av Microsoft krypteras inte minnet som standard. Om det behövs av din organisation, kryptera innehåll innan du lagrar i Azure Cache för Redis.

Om du använder Azure Cache for Redis-funktionen "Redis Data Persistens" skickas data till ett Azure Storage-konto som du äger och hanterar. Du kan konfigurera persistens från bladet "Ny Azure-cache för Redis" när cacheminnet skapas och på resursmenyn för befintliga premiumcachen.

Data i Azure Storage krypteras och dekrypteras transparent med 256-bitars AES-kryptering, ett av de starkaste blockchiffer som finns tillgängliga och är FIPS 140-2-kompatibel. Azure Storage-kryptering kan inte inaktiveras. Du kan lita på Microsoft-hanterade nycklar för kryptering av ditt lagringskonto eller hantera kryptering med dina egna nycklar.

Konfigurera persistens i Azure Cache för Redis:https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Förstå kryptering för Azure Storage-konton:https://docs.microsoft.com/azure/storage/common/storage-service-encryption

Förstå Azure-kunddataskydd:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Delat

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logga och avisera om ändringar i kritiska Azure-resurser

**Vägledning**: Använd Azure Monitor med Azure Activity-loggen för att skapa aviseringar för när ändringar sker i produktionsinstanser av Azure Cache för Redis och andra kritiska eller relaterade resurser.

Så här skapar du aviseringar för Azure Activity Log-händelser:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Säkerhetskontroll: Sårbarhetshantering](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Kör automatiserade sårbarhetsskanningsverktyg

**Vägledning**: Följ rekommendationer från Azure Security Center för att skydda din Azure-cache för Redis-instanser och relaterade resurser.

Microsoft utför sårbarhetshantering på de underliggande systemen som stöder Azure Cache för Redis.

Förstå Azure Security Center-rekommendationer:https://docs.microsoft.com/azure/security-center/recommendations-reference

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Delat

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Distribuera en lösning för hantering av automatisk operativsystemets korrigering

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Distribuera automatisk lösning för hantering av programkorrigering från tredje part

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Jämför sårbarhetssökningar från rygg mot rygg

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Använd en riskklassificeringsprocess för att prioritera reparation av upptäckta sårbarheter

**Vägledning**: Microsoft utför sårbarhetshantering på de underliggande systemen som stöder Azure Cache för Redis.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

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

**Vägledning**: Använd taggar på Azure-resurser som ger metadata för att logiskt ordna dem till en taxonomi.

Så här skapar och använder du taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ta bort obehöriga Azure-resurser

**Vägd:** Använd taggning, hanteringsgrupper och separata prenumerationer, där så är lämpligt, för att organisera och spåra Azure-cache för Redis-instanser och relaterade resurser. Stämma av lager regelbundet och se till att obehöriga resurser tas bort från prenumerationen i tid.

Dessutom kan du använda Azure-principen för att begränsa vilken typ av resurser som kan skapas i kundprenumerationer med hjälp av följande inbyggda principdefinitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

Så här skapar du ytterligare Azure-prenumerationer:https://docs.microsoft.com/azure/billing/billing-create-subscription

Så här skapar du hanteringsgrupper:https://docs.microsoft.com/azure/governance/management-groups/create

Så här skapar och använder du Taggar:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Underhåll en inventering av godkända Azure-resurser och programvarutitlar

**Vägledning**: Ej tillämpligt. Den här rekommendationen är avsedd för beräkningsresurser och Azure som helhet.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Övervaka för icke godkända Azure-resurser

**Vägledning**: Använd Azure-principen för att begränsa vilken typ av resurser som kan skapas i kundprenumerationer med hjälp av följande inbyggda principdefinitioner:

Otillåtna resurstyper

Tillåtna resurstyper

Dessutom kan du använda Azure Resource Graph för att fråga/identifiera resurser inom prenumerationerna.

Konfigurera och hantera Azure-princip:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här skapar du frågor med Azure Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Övervaka icke godkända program i beräkningsresurser

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Ta bort icke godkända Azure-resurser och program

**Vägledning**: Ej tillämpligt. Den här rekommendationen är avsedd för beräkningsresurser och Azure som helhet.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="68-use-only-approved-applications"></a>6.8: Använd endast godkända ansökningar

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="69-use-only-approved-azure-services"></a>6.9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure Policy för att sätta begränsningar för vilken typ av resurser som kan skapas i kundprenumerationer med hjälp av följande inbyggda principdefinitioner:

Otillåtna resurstyper

Tillåtna resurstyper

Konfigurera och hantera Azure-princip:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här nekar du en viss resurstyp med Azure Policy:

https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="610-implement-approved-application-list"></a>6.10: Genomföra godkänd ansökningslista

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Begränsa användarnas möjlighet att interagera med Azure Resources Manager via skript

**Vägledning**: Konfigurera Azure Villkorlig åtkomst för att begränsa användarnas möjlighet att interagera med Azure Resource Manager (ARM) genom att konfigurera "Blockera åtkomst" för "Microsoft Azure Management"-appen.

Konfigurera villkorlig åtkomst för att blockera åtkomst till ARM:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Begränsa användarnas möjlighet att köra skript i beräkningsresurser

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fysiskt eller logiskt segregera högriskapplikationer

**Vägledning**: Ej tillämpligt. Den här rekommendationen är avsedd för webbprogram som körs på Azure App Service eller beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Säkerhetskontroll: Säker konfiguration](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: Definiera och implementera standardsäkerhetskonfigurationer för dina Azure-cache för Redis-instanser med Azure Policy. Använd Azure Policy-alias i namnområdet "Microsoft.Cache" för att skapa anpassade principer för granskning eller framtvingning av konfigurationen av dina Azure-cache för Redis-instanser. Du kan också använda inbyggda principdefinitioner som är relaterade till dina Azure-cache för Redis-instanser, till exempel:

Endast säkra anslutningar till Redis-cachen ska aktiveras

Så här visar du tillgängliga Azure-principalias:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Upprätta konfigurationer för säkra operativsystem

**Vägledning**: Ej tillämpligt. den här riktlinjen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Underhåll säkra Azure-resurskonfigurationer

**Vägledning**: Använd Azure-principen [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar för dina Azure-resurser.

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Förstå Azure-principeffekter:https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Underhåll konfigurationer av säkra operativsystem

**Vägledning**: Ej tillämpligt. den här riktlinjen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Säkert lagra konfiguration av Azure-resurser

**Vägd:** Om du använder anpassade Azure-principdefinitioner eller Azure Resource Manager-mallar för dina Azure-cache för Redis-instanser och relaterade resurser använder du Azure Repos för att lagra och hantera din kod på ett säkert sätt.

Så här lagrar du kod i Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Dokumentation för Azure Repos:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Lagra anpassade operativsystemavbildningar på ett säkert sätt

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Distribuera verktyg för hantering av systemkonfigurationer

**Vägd:** Använd Azure Policy-alias i namnområdet "Microsoft.Cache" för att skapa anpassade principer för att avisera, granska och framtvinga systemkonfigurationer. Dessutom, utveckla en process och pipeline för att hantera principundantag.

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Distribuera verktyg för systemkonfigurationshantering för operativsystem

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementera automatisk konfigurationsövervakning för Azure-tjänster

**Vägd:** Använd Azure Policy-alias i namnområdet "Microsoft.Cache" för att skapa anpassade principer för att avisera, granska och framtvinga systemkonfigurationer. Använd Azure-princip [granskning], [neka] och [distribuera om det inte finns] för att automatiskt framtvinga konfigurationer för dina Azure-cache för Redis-instanser och relaterade resurser.

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementera automatisk konfigurationsövervakning för operativsystem

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="711-manage-azure-secrets-securely"></a>7.11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning:** För virtuella Azure-datorer eller webbprogram som körs på Azure App Service som används för att komma åt dina Azure-cache för Redis-instanser använder du Hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda Azure Cache för Redis hemlig hantering. Kontrollera att key vault mjuk borttagning är aktiverat.

Så här integrerar du med Azure-hanterade identiteter:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Så här skapar du ett nyckelvalv:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

Så här tillhandahåller du Key Vault-autentisering med en hanterad identitet:

https://docs.microsoft.com/azure/key-vault/managed-identity

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Hantera identiteter på ett säkert och automatiskt sätt

**Vägledning:** För virtuella Azure-datorer eller webbprogram som körs på Azure App Service som används för att komma åt dina Azure-cache för Redis-instanser använder du Hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda Azure Cache för Redis hemlig hantering. Kontrollera att Key Vault Soft Delete är aktiverat.

Använd hanterade identiteter för att tillhandahålla Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Med hanterade identiteter kan du autentisera till alla tjänster som stöder AAD-autentisering, inklusive Azure Key Vault, utan några autentiseringsuppgifter i koden.

Konfigurerar hanterade identiteter:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Så här integrerar du med Azure-hanterade identiteter:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: Implementera autentiseringsskanner för att identifiera autentiseringsuppgifter i koden. Autentiseringsläsare kommer också att uppmuntra flytta identifierade autentiseringsuppgifter till säkrare platser som Azure Key Vault.

Så här konfigurerar du autentiseringsskanner:https://secdevtools.azurewebsites.net/helpcredscan.html

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Använd centralt hanterad programvara mot skadlig kod

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

Microsoft anti-malware är aktiverat på den underliggande värden som stöder Azure-tjänster (till exempel Azure App Service), men det körs inte på kundinnehåll.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Förskansa filer som ska överföras till Azure-resurser som inte är beräkning

**Vägledning**: Microsoft anti-malware är aktiverat på den underliggande värden som stöder Azure-tjänster (till exempel Azure Cache för Redis), men det körs inte på kundinnehåll.

Förskansa allt innehåll som överförs till azure-resurser som inte är beräkning, till exempel App Service, DataSjölagring, Blob Storage, Azure Database för PostgreSQL osv. Microsoft kan inte komma åt dina data i dessa fall.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Se till att programvara och signaturer mot skadlig kod uppdateras

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

Microsoft anti-malware är aktiverat på den underliggande värden som stöder Azure-tjänster (till exempel Azure Cache för Redis), men det körs inte på kundinnehåll.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Säkerhetskontroll: Dataåterställning](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Se till att regelbundna automatiska säkerhetskopieringar

**Vägledning**: Aktivera Redis persistens. Redis persistens kan du beständiga data som lagras i Redis. Du kan också ta ögonblicksbilder och säkerhetskopiera data, som du kan läsa in i händelse av ett maskinvarufel. Detta är en stor fördel jämfört med basic- eller standardnivå där alla data lagras i minnet och det kan finnas potentiell dataförlust vid ett fel där cachenoder är nere.

Du kan också använda Azure Cache för Redis Export. Med export kan du exportera data som lagras i Azure Cache for Redis till Redis-kompatibla RDB-filer. Du kan använda den här funktionen för att flytta data från en Azure Cache for Redis-instans till en annan eller till en annan Redis-server. Under exportprocessen skapas en temporär fil på den virtuella datorn som är värd för Azure Cache for Redis-serverinstansen och filen överförs till det angivna lagringskontot. När exportåtgärden slutförs med antingen status som lyckad eller misslyckad tas den temporära filen bort.

Så här aktiverar du Redis persistens:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Så här använder du Azure Cache för Redis Export:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Utför fullständiga systemsäkerhetskopior och säkerhetskopiera alla kundhanterade nycklar

**Vägledning**: Aktivera Redis persistens. Redis persistens kan du beständiga data som lagras i Redis. Du kan också ta ögonblicksbilder och säkerhetskopiera data, som du kan läsa in i händelse av ett maskinvarufel. Detta är en stor fördel jämfört med basic- eller standardnivå där alla data lagras i minnet och det kan finnas potentiell dataförlust vid ett fel där cachenoder är nere.

Du kan också använda Azure Cache för Redis Export. Med export kan du exportera data som lagras i Azure Cache for Redis till Redis-kompatibla RDB-filer. Du kan använda den här funktionen för att flytta data från en Azure Cache for Redis-instans till en annan eller till en annan Redis-server. Under exportprocessen skapas en temporär fil på den virtuella datorn som är värd för Azure Cache for Redis-serverinstansen och filen överförs till det angivna lagringskontot. När exportåtgärden slutförs med antingen status som lyckad eller misslyckad tas den temporära filen bort.

Om du använder Azure Key Vault för att lagra autentiseringsuppgifter för dina Azure-cache för Redis-instanser, säkerställer du regelbundna automatiska säkerhetskopior av dina nycklar.

Så här aktiverar du Redis persistens:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Så här använder du Azure Cache för Redis Export:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

Så här säkerhetskopierar du key vault-nycklar:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validera alla säkerhetskopior inklusive kundhanterade nycklar

**Vägledning**: Använd Azure Cache för Redis Import. Import kan användas för att få Redis-kompatibla RDB-filer från alla Redis-servrar som körs i alla moln eller miljöer, inklusive Redis som körs på Linux, Windows eller någon molnleverantör som Amazon Web Services och andra. Att importera data är ett enkelt sätt att skapa en cache med förifyllda data. Under importen läses RDB-filerna från Azure-lagringen in i minnet och infogar sedan nycklarna i cacheminnet.

Testa regelbundet återställning av data av dina Azure Key Vault-hemligheter.

Så här använder du Azure Cache för Redis Import:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

Så här återställer du Key Vault Secrets:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Se till att säkerhetskopior och kundhanterade nycklar skyddas

**Vägledning**: Azure Cache for Redis-säkerhetskopior från Redis Export och Redis persistens lagras i ditt valda Azure Storage-konto. Data i Azure Storage krypteras och dekrypteras transparent med 256-bitars AES-kryptering, ett av de starkaste blockchiffer som finns tillgängliga och är FIPS 140-2-kompatibel. Azure Storage-kryptering kan inte inaktiveras. Du kan lita på Microsoft-hanterade nycklar för kryptering av ditt lagringskonto eller hantera kryptering med dina egna nycklar.

Förstå kryptering för Azure Storage-konton:https://docs.microsoft.com/azure/storage/common/storage-service-encryption

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Microsoft

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Säkerhetskontroll: Incident response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Skapa en vägledning för incidenthantering

**Vägledning**: Skapa en incidenthanteringsguide för din organisation. Se till att det finns skriftliga incidenthanteringsplaner som definierar alla roller för personal samt faser av incidenthantering/hantering från identifiering till granskning efter incidenten.

Konfigurera arbetsflödesautomatiseringar i Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Vägledning om hur du bygger din egen process för hantering av säkerhetsincidenter:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Microsoft Security Response Center's Anatomy of an Incident:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Kunden kan också utnyttja NIST: s Computer Security Incident Handling Guide till stöd i skapandet av sin egen incidentresponsplan:

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Skapa en incidentbedömning och prioriteringsprocedur

**Vägledning**: Security Center tilldelar allvarlighetsgrad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som ska undersökas först. Allvarlighetsgraden baseras på hur säker Security Center är i att hitta eller analytiska används för att utfärda aviseringen samt den konfidensnivå som det fanns ont uppsåt bakom aktiviteten som ledde till aviseringen.

Dessutom tydligt markera abonnemang (för ex. produktion, icke-prod) och skapa ett namngivningssystem för att tydligt identifiera och kategorisera Azure-resurser.

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="103-test-security-response-procedures"></a>10.3: Testsäkerhetssvarsprocedurer

**Vägledning**: Genomföra övningar för att testa systemens funktioner för incidenthantering på regelbunden kadens. Identifiera svaga punkter och luckor och revidera planen efter behov.

Se NIST:s publikation: Guide to Test, Training och Exercise Programs for IT Plans and Capabilities:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Ange kontaktinformation för säkerhetsincidenter och konfigurera varningsmeddelanden för säkerhetsincidenter

**Vägledning**: Kontaktinformation för säkerhetsincidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att kundens data har använts av en olaglig eller obehörig part.  Granska incidenter i efterhand för att säkerställa att problemen löses.

Så här ställer du in säkerhetskontakten för Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Införliva säkerhetsvarningar i ditt incidenthanteringssystem

**Vägledning**: Exportera dina Azure Security Center-aviseringar och rekommendationer med hjälp av funktionen Kontinuerlig export. Kontinuerlig export gör att du kan exportera aviseringar och rekommendationer antingen manuellt eller på ett kontinuerligt sätt. Du kan använda Azure Security Center-dataanslutningen för att strömma aviseringarna Sentinel.

Konfigurera kontinuerlig export:

https://docs.microsoft.com/azure/security-center/continuous-export

Så här streamar du aviseringar till Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisera svaret på säkerhetsvarningar

**Vägledning**: Använd funktionen Automatisering av arbetsflöde i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" på säkerhetsaviseringar och rekommendationer.

Konfigurerar automatiserings- och logikappar för arbetsflöde:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [Säkerhetskontroll: Penetrationstester och röda teamövningar](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Utför regelbundna penetrationstester av dina Azure-resurser och säkerställer reparation av alla kritiska säkerhetsresultat inom 60 dagar

**Vägledning**: Följ Microsofts insatsregler för att säkerställa att dina penetrationstester inte bryter mot Microsofts policyer:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Du hittar mer information om Microsofts strategi och genomförande av Red Teaming och penetrationstester på webbplatser på flera webbplatser mot Microsoft-hanterad molninfrastruktur, molntjänster och -program här: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Delat

## <a name="next-steps"></a>Nästa steg

- Se [Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Läs mer om [Azure Security-originalplaner](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
