---
title: Azure Security Baseline för eventhubbar
description: Azure Security Baseline för eventhubbar
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 10fc822f9f36512405dd4e3b5aba6270b53e163f
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549033"
---
# <a name="azure-security-baseline-for-event-hubs"></a>Azure Security Baseline för eventhubbar

Azure Security Baseline for Event Hubs innehåller rekommendationer som hjälper dig att förbättra säkerhetspositionen för distributionen.

Baslinjen för den här tjänsten hämtas från [Azure Security Benchmark version 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), som ger rekommendationer om hur du kan skydda dina molnlösningar på Azure med vår vägledning om bästa praxis.

Mer information finns i [översikt över Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Säkerhetskontroll: Nätverkssäkerhet](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Skydda resurser med nätverkssäkerhetsgrupper eller Azure-brandväggen i det virtuella nätverket

**Vägledning**: Integreringen av händelsehubbar med slutpunkter för virtuella nätverkstjänstar möjliggör säker åtkomst till meddelandefunktioner från arbetsbelastningar som virtuella datorer som är bundna till virtuella nätverk, där sökvägen för nätverkstrafik skyddas i båda ändar.

När den är bunden till minst en slutpunkt för virtuella nätverksundernätstjänsten accepterar respektive namnområde för eventhubbar inte längre trafik från var som helst men auktoriserade undernät i virtuella nätverk. Från det virtuella nätverksperspektivet konfigurerar bindning av namnområdet Event Hubs till en tjänstslutpunkt en isolerad nätverkstunnel från det virtuella nätverksundernätet till meddelandetjänsten. 

Du kan också skapa en privat slutpunkt, som är ett nätverksgränssnitt som ansluter dig privat och säkert till Azure Event Hubs-tjänsten med hjälp av Azure Private Link-tjänsten. Den privata slutpunkten använder en privat IP-adress från ditt virtuella nätverk, vilket effektivt för in tjänsten i ditt virtuella nätverk. All trafik till tjänsten kan dirigeras via den privata slutpunkten, så inga gateways, NAT-enheter, ExpressRoute- eller VPN-anslutningar eller offentliga IP-adresser behövs. 

Du kan också skydda ditt Namnområde för Azure Event Hubs med hjälp av brandväggar. Azure Event Hubs stöder IP-baserade åtkomstkontroller för stöd för inkommande brandvägg. Du kan ange brandväggsregler med hjälp av Azure-portalen, Azure Resource Manager-mallarna eller via Azure CLI eller Azure PowerShell.

Så här använder du slutpunkter för virtuella nätverkstjänster med Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

Mer information finns i Integrera Azure Event Hubs med Azure Private Link: https://docs.microsoft.com/azure/event-hubs/private-link-service.

Aktivera integrering och brandväggar för virtuella nätverk i namnområdet För händelsehubbar:https://docs.microsoft.com/azure/event-hubs/event-hubs-tutorial-virtual-networks-firewalls

Konfigurera IP-brandväggsregler för Azure Event Hubs-namnområden:https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Övervaka och logga konfiguration och trafik av virtuella nätverk, undernät och nätverkskort

**Vägledning**: Använd Azure Security Center och följ nätverksskyddsrekommendationerna för att skydda dina eventhubbarresurser i Azure. Om du använder virtuella Azure-datorer för att komma åt dina händelsehubbar aktiverar du NSG-flödesloggar (Network Security Group) och skickar loggar till ett lagringskonto för trafikgranskning.

Aktivera NSG-flödesloggar:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Förstå nätverkssäkerhet som tillhandahålls av Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="13-protect-critical-web-applications"></a>1.3: Skydda kritiska webbprogram

**Vägledning**: Ej tillämpligt. Den här rekommendationen är avsedd för webbprogram som körs på Azure App Service eller beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Aktivera DDoS Protection Standard i de virtuella nätverk som är associerade med dina händelsehubbar för att skydda mot distribuerade DDoS-attacker (Denial-of-Service). Använd Azure Security Center Integrated Threat Intelligence för att neka kommunikation med kända skadliga eller oanvända IP-adresser på Internet.

KonfigureraR du DDoS-skydd:[https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](/azure/virtual-network/manage-ddos-protection)

Mer information om Azure Security Center Integrated Threat Intelligence:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Spela in nätverkspaket och flödesloggar

**Vägledning:** Om du använder virtuella Azure-datorer för att komma åt dina händelsehubbar aktiverar du NSG-flödesloggar (Network Security Group) och skickar loggar till ett lagringskonto för trafikgranskning. Du kan också skicka NSG-flödesloggar till en Log Analytics-arbetsyta och använda Traffic Analytics för att ge insikter om trafikflödet i ditt Azure-moln. Vissa fördelar med Traffic Analytics är möjligheten att visualisera nätverksaktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödesmönster och identifiera felkonfigurationer i nätverket.

Om det behövs för att undersöka avvikande aktivitet aktiverar du insamling av nätverksbevakningspaket.

Aktivera NSG-flödesloggar:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Aktivera och använda Traffic Analytics:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Aktivera Network Watcher:https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuera nätverksbaserade system för intrångsdetektering/intrångsförebyggande (IDS/IPS)

**Vägledning:** Om du använder virtuella Azure-datorer för att komma åt dina händelsehubbar väljer du ett erbjudande från Azure Marketplace som stöder IDS/IPS-funktioner med funktioner för nyttolastinspektion. Om intrångsidentifiering och/eller förebyggande baserat på nyttolastinspektion inte krävs för din organisation kan du använda Azure Event Hubs inbyggda brandväggsfunktion. Du kan begränsa åtkomsten till namnområdet Event Hubs för ett begränsat antal IP-adresser eller en specifik IP-adress med hjälp av brandväggsregler.

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

Så här lägger du till en brandväggsregel i händelsehubbar för en angiven IP-adress:

 https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Övervakning av Azure Security Center**: Ännu inte tillgänglig

**Ansvar**: Kund

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Hantera trafik till webbapplikationer

**Vägledning**: Ej tillämpligt. Den här rekommendationen är avsedd för webbprogram som körs på Azure App Service eller beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimera komplexiteten och de administrativa omkostnaderna för nätverkssäkerhetsreglerna

**Vägledning**: Den här rekommendationen är inte tillämplig, den här rekommendationen är avsedd för webbprogram som körs på Azure App Service eller beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Underhåll standardsäkerhetskonfigurationer för nätverksenheter

**Vägledning**: Definiera och implementera standardsäkerhetskonfigurationer för nätverksresurser som är associerade med dina Azure Event Hubs-namnområden med Azure Policy. Använd Azure Policy-alias i namnområdena "Microsoft.EventHub" och "Microsoft.Network" för att skapa anpassade principer för granskning eller framtvingning av nätverkskonfigurationen för namnområdena Händelserhubrar. Du kan också använda inbyggda principdefinitioner som är relaterade till Azure Event Hubs, till exempel:

- Event Hub bör använda en slutpunkt för virtuella nätverkstjänsten.

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure inbyggd princip för event hubbar namnområde:https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub



Azure Princip-exempel för nätverk:https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network



Så här skapar du en Azure Blueprint:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regler för konfiguration av dokumenttrafik

**Vägledning**: Använd taggar för virtuella nätverk och andra resurser som är relaterade till nätverkssäkerhet och trafikflöde som är associerade med dina händelsehubbar.

Så här skapar och använder du taggar:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Använd automatiserade verktyg för att övervaka nätverksresurskonfigurationer och identifiera ändringar

**Vägledning**: Använd Azure Activity Log för att övervaka nätverksresurskonfigurationer och identifiera ändringar för nätverksresurser relaterade till Azure Event Hubs. Skapa aviseringar i Azure Monitor som utlöses när ändringar av kritiska nätverksresurser sker.

Så här visar och hämtar du Azure Activity Log-händelser:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Så här skapar du aviseringar i Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [Säkerhetskontroll: Loggning och övervakning](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Använd godkända tidssynkroniseringskällor

**Vägledning**: Ej tillämpligt. Microsoft underhåller tidskällan som används för Azure-resurser, till exempel Azure Event Hubs, för tidsstämplar i loggarna.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurera hantering av centrala säkerhetsloggar

**Vägd:** Konfigurera loggar relaterade till händelsehubbar i diagnostikinställningarna för aktivitetslogg och eventhubb för att skicka loggar till en Log Analytics-arbetsyta som ska efterfrågas eller till ett lagringskonto för långsiktig arkiveringslagring.

Konfigurera diagnostikinställningar för Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Förstå Azure-aktivitetsloggen:https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivera granskningsloggning för Azure-resurser

**Vägledning**: Aktivera diagnostikinställningar för namnområdet Azure Event Hubs. Det finns tre kategorier av diagnostikinställningar för Azure Event Hubs: Arkivloggar, driftloggar och loggar för automatisk skalning. Aktivera driftloggar för att samla in information om vad som händer under event hubs-åtgärder, särskilt operationstypen, inklusive skapande av händelsehubben, resurser som används och åtgärdens status.

Dessutom kan du aktivera diagnostikinställningar för Azure Activity log och skicka dem till ett Azure Storage-konto, händelsenav eller en Log Analytics-arbetsyta. Aktivitetsloggar ger insikt i de åtgärder som utfördes på dina Azure Event Hubs och andra resurser. Med hjälp av aktivitetsloggar kan du bestämma vilka, vem och när för alla skrivåtgärder (PUT, POST, DELETE) som tas på dina Azure Event Hubs-namnområden.

Aktivera diagnostikinställningar för Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Aktivera diagnostikinställningar för Azure Activity Log:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Samla in säkerhetsloggar från operativsystem

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurera lagring av säkerhetslogglagring

**Vägledning**: Inom Azure Monitor anger du lagringsperioden för Logganalysarbetsyta enligt organisationens efterlevnadsregler för att samla in och granska händelserhub-relaterade incidenter.

Så här anger du loggkvarhållningsparametrar för Log Analytics-arbetsytor:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2.6: Övervaka och granska loggar

**Vägledning**: Analysera och övervaka loggar för avvikande beteende och granska regelbundet resultat relaterade till dina händelsehubbar. Använd Azure Monitor's Log Analytics för att granska loggar och utföra frågor om loggdata. Alternativt kan du aktivera och ombord data till Azure Sentinel eller en tredje part SIEM.
 

Mer information om log analytics-arbetsytan finns:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Så här utför du anpassade frågor i Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

Så här går du ombord på Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Aktivera aviseringar för avvikande aktivitet

**Vägd:** Konfigurera loggar relaterade till Azure Event Hubs i aktivitetsloggen och diagnostikinställningar för händelsehubbar i Azure Monitor för att skicka loggar till en Log Analytics-arbetsyta som ska efterfrågas eller till ett lagringskonto för långsiktig arkiveringslagring. Använd Log Analytics-arbetsytan för att skapa aviseringar för avvikande aktivitet som finns i säkerhetsloggar och händelser.

Alternativt kan du aktivera och ombord data till Azure Sentinel. 

Förstå Azure-aktivitetsloggen:https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

Konfigurera diagnostikinställningar för Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Så här varnar du för logganalysarbetsytloggdata:https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

Så här går du ombord på Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Övervakning av Azure Security Center**: Ännu inte tillgänglig

**Ansvar**: Kund

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralisera loggning av skadlig kod

**Vägledning**: Ej tillämpligt. Event Hub bearbetar inte anti-malware loggning.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivera DNS-frågeloggning

**Vägledning**: Ej tillämpligt. Event Hubs bearbetar eller producerar inte DNS-relaterade loggar.

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

**Vägledning**: Kontrollplanåtkomst till eventhubbar styrs via Azure Active Directory (AD). Azure AD har inte begreppet standardlösenord.

Dataplanåtkomst till eventhubbar styrs via Azure AD med hanterade identiteter eller appregistreringar samt signaturer för delad åtkomst. Signaturer för delad åtkomst används av klienter som ansluter till dina händelsehubbar och kan återskapas när som helst.

Förstå signaturer för delad åtkomst för händelsehubbar:https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

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

**Vägledning**: Microsoft Azure tillhandahåller integrerad hantering av åtkomstkontroll för resurser och program baserade på Azure Active Directory (AD). En viktig fördel med att använda Azure AD med Azure Event Hubs är att du inte behöver lagra dina autentiseringsuppgifter i koden längre. I stället kan du begära en OAuth 2.0-åtkomsttoken från Microsoft Identity-plattformen. Resursnamnet som begär en https://eventhubs.azure.net/token är . Azure AD autentiserar säkerhetsobjektet (en användare, grupp eller tjänsthuvudnamn) som kör programmet. Om autentiseringen lyckas returnerar Azure AD en åtkomsttoken till programmet och programmet kan sedan använda åtkomsttoken för att auktorisera begäran till Azure Event Hubs-resurser.

Så här autentiserar du ett program med Azure AD för åtkomst till eventhubbar-resurser:https://docs.microsoft.com/azure/event-hubs/authenticate-application

Förstå SSO med Azure AD:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Använda multifaktorautentisering för all Azure Active Directory-baserad åtkomst

**Vägledning:** Aktivera MFA (Azure Active Directory Multi Factor Authentication) och följ Azure Security Center Identity and Access Management recommendations för att skydda dina Event Hub-aktiverade resurser.

Aktivera MFA i Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Så här övervakar du identitet och åtkomst i Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Använd dedikerade datorer (arbetsstationer för privilegierad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd paw -funktioner (Privileged Access Workstations) med MFA (MultiFaktor Authentication) konfigurerade för att logga in på och konfigurera eventhubbaktiverade resurser.

Läs mer om arbetsstationer för privilegierad åtkomst:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Aktivera MFA i Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Logga och varna för misstänkt aktivitet från administrativa konton

**Vägledning**: Använd AZURE Active Directory (AD) Privilegierad identitetshantering (PIM) för generering av loggar och aviseringar när misstänkt eller osäker aktivitet inträffar i miljön. Använd Azure AD-riskidentifieringar för att visa aviseringar och rapporter om riskfyllda användarbeteenden. Om du vill logga ytterligare skickar du riskidentifieringsaviseringar för Azure Security Center i Azure Monitor och konfigurerar anpassade aviseringar/meddelanden med hjälp av åtgärdsgrupper.

Så här distribuerar du PIM (Privilegierad identitetshantering):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Förstå Azure AD-riskidentifieringar:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Konfigurera åtgärdsgrupper för anpassad avisering och avisering:https://docs.microsoft.com/azure/azure-monitor/platform/action-groups

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Hantera Azure-resurser från endast godkända platser

**Vägledning**: Använd namngivna platser för villkorlig åtkomst för att tillåta åtkomst från endast specifika logiska grupperingar av IP-adressintervall eller länder/regioner.



Konfigurera namngivna platser i Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="39-use-azure-active-directory"></a>3.9: Använd Azure Active Directory

**Vägledning**: Använd Azure Active Directory (AD) som det centrala autentiserings- och auktoriseringssystemet för Azure-resurser som eventhubbar. Detta möjliggör rollbaserad åtkomstkontroll (RBAC) till administrativa känsliga resurser.

 Så här skapar och konfigurerar du en Azure AD-instans:https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

Mer information om hur Azure Event Hubs integreras med Azure Active Directory (AAD) finns i AUKtorisera åtkomst till eventhubbar-resurser med Azure Active Directory:https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelbundet granska och förena användaråtkomst

**Vägledning**: Azure Active Directory (AD) innehåller loggar som hjälper dig att upptäcka inaktuella konton. Dessutom kan du använda Azure Identity Access Reviews för att effektivt hantera gruppmedlemskap, åtkomst till företagsprogram och rolltilldelningar. Användaråtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.

Rotera dessutom regelbundet dina eventhubbars signaturer för delad åtkomst.

Förstå Azure AD-rapportering:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Så här använder du Azure Identity Access Reviews:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

Förstå signaturer för delad åtkomst för händelsehubbar:https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Övervaka försök att komma åt inaktiverade konton

**Vägledning**: Du har tillgång till azure Active Directory (AD) inloggningsaktivitet, gransknings- och riskhändelseloggkällor, vilket gör att du kan integrera med alla SIEM/Monitoring-verktyg.

Du kan effektivisera den här processen genom att skapa diagnostikinställningar för Azure AD-användarkonton och skicka granskningsloggar och inloggningsloggar till en Log Analytics-arbetsyta. Du kan konfigurera önskade loggaviseringar i Log Analytics.

Så här integrerar du Azure Activity Logs i Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Auktorisera åtkomst till eventhubbar-resurser med Azure Active Directory:https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Varning på kontots avvikelse för inloggningsbeteende

**Vägledning**: Använd Azure Active Directorys funktioner för identitetsskydd och riskidentifiering för att konfigurera automatiska svar på identifierade misstänkta åtgärder relaterade till dina eventhubbaraktiverade resurser. Du bör aktivera automatiska svar via Azure Sentinel för att implementera organisationens säkerhetssvar.

Så här visar du riska 2-inloggningar för Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Konfigurera och aktivera riskprinciper för identitetsskydd:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Så här går du ombord på Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ge Microsoft åtkomst till relevanta kunddata under supportscenarier

**Vägledning**: För närvarande inte tillgänglig; Customer Lockbox stöds ännu inte för eventhubbar.

Lista över tjänster som stöds av Kundens låsbox:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: För närvarande inte tillgänglig

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Säkerhetskontroll: Dataskydd](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Föra en inventering av känslig information

**Vägledning**: Använd taggar på resurser som är relaterade till dina eventhubbar för att hjälpa till att spåra Azure-resurser som lagrar eller bearbetar känslig information.

Så här skapar och använder du Taggar:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolera system som lagrar eller bearbetar känslig information

**Vägledning**: Implementera separata prenumerationer och/eller hanteringsgrupper för utveckling, test och produktion. Namnområden för eventhubbar bör avgränsas med virtuella nätverk med tjänstslutpunkter aktiverade och taggade på rätt sätt.

Du kan också skydda ditt Namnområde för Azure Event Hubs med hjälp av brandväggar. Azure Event Hubs stöder IP-baserade åtkomstkontroller för stöd för inkommande brandvägg. Du kan ange brandväggsregler med hjälp av Azure-portalen, Azure Resource Manager-mallarna eller via Azure CLI eller Azure PowerShell.

Så här skapar du ytterligare Azure-prenumerationer:https://docs.microsoft.com/azure/billing/billing-create-subscription

Så här skapar du hanteringsgrupper:https://docs.microsoft.com/azure/governance/management-groups/create

Konfigurera IP-brandväggsregler för Azure Event Hubs-namnområden:https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Så här skapar och använder du taggar:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Så här skapar du ett virtuellt nätverk:https://docs.microsoft.com/azure/virtual-network/quick-create-portal

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Övervaka och blockera obehörig överföring av känslig information

**Vägledning:** När du använder virtuella datorer för att komma åt dina händelsehubbar kan du använda virtuella nätverk, tjänstslutpunkter, brandväggen för eventhubbar, nätverkssäkerhetsgrupper och tjänsttaggar för att minska risken för dataexfiltration.

Microsoft hanterar den underliggande infrastrukturen för Azure Event Hubs och har implementerat strikta kontroller för att förhindra förlust eller exponering av kunddata.

Konfigurera IP-brandväggsregler för Azure Event Hubs-namnområden:https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Förstå slutpunkter för virtual network service med Azure-händelsehubbar:https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

Integrera Azure-händelsehubbar med Azure Private Link:https://docs.microsoft.com/azure/event-hubs/private-link-service

Förstå nätverkssäkerhetsgrupper och tjänsttaggar:https://docs.microsoft.com/azure/virtual-network/security-overview

Förstå kunddataskyddet i Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Kryptera all känslig information under transport

**Vägledning**: Azure Event Hubs tillämpar TLS-krypterad kommunikation som standard. TLS-versionerna 1.0, 1.1 och 1.2 stöds för närvarande. TLS 1.0 och 1.1 är dock på väg att utfasa branschomfattande, så använd TLS 1.2 om det alls är möjligt.

Information om hur du förstår säkerhetsfunktionerna i händelsehubbar finns i Nätverkssäkerhet:https://docs.microsoft.com/azure/event-hubs/network-security

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Använda ett aktivt identifieringsverktyg för att identifiera känsliga data

**Vägledning**: Funktioner för dataidentifiering, klassificering och förlustförebyggande är ännu inte tillgängliga för Azure Event Hubs. Implementera tredjepartslösning om det behövs för efterlevnadsändamål.

För den underliggande plattformen som hanteras av Microsoft behandlar Microsoft allt kundinnehåll som känsligt och gör stora ansträngningar för att skydda mot förlust av kunddata och exponering. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och underhållit en uppsättning robusta dataskyddskontroller och funktioner.

Förstå kunddataskyddet i Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Delat

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Använd Azure RBAC för att kontrollera åtkomsten till resurser

**Vägledning**: Azure Event Hubs stöder användning av Azure Active Directory (AD) för att auktorisera begäranden till eventhubbar-resurser. Med Azure AD kan du använda rollbaserad åtkomstkontroll (RBAC) för att bevilja behörigheter till ett säkerhetsobjekt, som kan vara en användare, eller ett programtjänsthuvudnamn.

Förstå Azure AD RBAC och tillgängliga roller för Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Använd värdbaserad dataförlustprevention för att genomdriva åtkomstkontroll

**Vägledning**: Ej tillämpligt. den här riktlinjen är avsedd för beräkningsresurser.

Microsoft hanterar den underliggande infrastrukturen för Event Hubs och har implementerat strikta kontroller för att förhindra förlust eller exponering av kunddata.

Förstå kunddataskyddet i Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Kryptera känslig information i vila

**Vägledning:** Azure Event Hubs stöder möjligheten att kryptera data i vila med antingen Microsoft-hanterade nycklar eller kundhanterade nycklar. Med den här funktionen kan du skapa, rotera, inaktivera och återkalla åtkomsten till de kundhanterade nycklar som används för att kryptera Azure Event Hubs-data i vila.

Konfigurera kundhanterade nycklar för kryptering av Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logga och avisera om ändringar i kritiska Azure-resurser

**Vägledning**: Använd Azure Monitor med Azure Activity-loggen för att skapa aviseringar för när ändringar sker i produktionsinstanser av Azure Event Hubs och andra kritiska eller relaterade resurser.

Så här skapar du aviseringar för Azure Activity Log-händelser:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Säkerhetskontroll: Sårbarhetshantering](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Kör automatiserade sårbarhetsskanningsverktyg

**Vägledning**: Ej tillämpligt. Microsoft utför sårbarhetshantering på de underliggande system som stöder Event Hubs.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Distribuera en lösning för hantering av automatisk operativsystemets korrigering

**Vägledning**: Ej tillämpligt. Microsoft utför korrigeringshantering på underliggande system som stöder eventhubbar.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Distribuera automatisk lösning för hantering av programkorrigering från tredje part

**Vägledning**: Ej tillämpligt. referensvärdet är avsett för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Jämför sårbarhetssökningar från rygg mot rygg

**Vägledning**: Ej tillämpligt. Microsoft utför sårbarhetshantering på de underliggande system som stöder Event Hubs.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Använd en riskklassificeringsprocess för att prioritera reparation av upptäckta sårbarheter

**Vägledning**: Ej tillämpligt. Microsoft utför sårbarhetshantering på de underliggande system som stöder Event Hubs.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Använd Identifiering av Azure Asset

**Vägledning**: Använd Azure Resource Graph för att fråga och identifiera alla resurser (inklusive Azure Event Hubs-namnområden) i dina prenumerationer. Se till att du har lämpliga (läs) behörigheter i din klientorganisation och kan räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Så här skapar du frågor med Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Så här visar du dina Azure-prenumerationer:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Förstå Azure RBAC:https://docs.microsoft.com/azure/role-based-access-control/overview

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="62-maintain-asset-metadata"></a>6.2: Underhåll metadata för tillgångar

**Vägledning**: Använd taggar på Azure-resurser som ger metadata för att logiskt ordna dem till en taxonomi.

Så här skapar och använder du taggar:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanteringsgrupper och separata prenumerationer, där så är lämpligt, för att organisera och spåra Azure Event Hubs namnområden och relaterade resurser. Stämma av lager regelbundet och se till att obehöriga resurser tas bort från prenumerationen i tid.

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

- Otillåtna resurstyper

- Tillåtna resurstyper

Dessutom kan du använda Azure Resource Graph för att fråga/identifiera resurser inom prenumerationerna.

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här skapar du frågor med Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

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

**Vägledning**: Använd Azure-principen för att begränsa vilken typ av resurser som kan skapas i kundprenumerationer med hjälp av följande inbyggda principdefinitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här nekar du en viss resurstyp med Azure Policy:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="610-implement-approved-application-list"></a>6.10: Genomföra godkänd ansökningslista

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>Begränsa användarnas möjlighet att interagera med Azure Resource Manager via skript</div>

**Vägledning**: Konfigurera Azure Villkorlig åtkomst för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "Blockera åtkomst" för "Microsoft Azure Management"-appen.

Konfigurera villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

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

**Vägledning**: Definiera och implementera standardsäkerhetskonfigurationer för azure Event Hubs-distributioner. Använd Azure Policy-alias i namnområdet "Microsoft.EventHub" för att skapa anpassade principer för granskning eller framtving av konfigurationer. Du kan också använda inbyggda principdefinitioner för Azure Event Hubs, till exempel:

- Diagnostikloggar i Event Hub ska aktiveras

- Event Hub bör använda en slutpunkt för virtuella nätverkstjänster

Azure inbyggd princip för event hubbar namnområde:https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub

Så här visar du tillgängliga Azure-principalias:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Upprätta konfigurationer för säkra operativsystem

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Underhåll säkra Azure-resurskonfigurationer

**Vägledning**: Använd Azure-principen [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar i dina Event Hubs-aktiverade resurser. 

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

 
Mer information om Azure-principeffekterna:https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Underhåll konfigurationer av säkra operativsystem

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Säkert lagra konfiguration av Azure-resurser

**Vägledning:** Om du använder anpassade Azure-principdefinitioner för dina eventhubbar eller relaterade resurser använder du Azure Repos för att lagra och hantera din kod på ett säkert sätt.

Så här lagrar du kod i Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Dokumentation för Azure Repos:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Lagra anpassade operativsystemavbildningar på ett säkert sätt

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Distribuera verktyg för hantering av systemkonfigurationer

**Vägd:** Använd Azure Policy-alias i namnområdet "Microsoft.EventHub" för att skapa anpassade principer för att avisera, granska och framtvinga systemkonfigurationer. Dessutom, utveckla en process och pipeline för att hantera principundantag.

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Distribuera verktyg för systemkonfigurationshantering för operativsystem

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementera automatisk konfigurationsövervakning för Azure-tjänster

**Vägd:** Använd Azure Policy-alias i namnområdet "Microsoft.EventHub" för att skapa anpassade principer för att avisera, granska och framtvinga systemkonfigurationer. Använd Azure-princip [granskning], [neka] och [distribuera om det inte finns] för att automatiskt framtvinga konfigurationer för dina Azure Event Hubs-distributioner och relaterade resurser.

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementera automatisk konfigurationsövervakning för operativsystem

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="711-manage-azure-secrets-securely"></a>7.11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning:** För virtuella Azure-datorer eller webbprogram som körs på Azure App Service som används för att komma åt dina händelsehubbar använder du Hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda hantering av signatur för delad åtkomst för dina Azure Event Hubs-distributioner. Kontrollera att Key Vault mjuk-borttagning är aktiverat.

Autentisera en hanterad identitet med Azure Active Directory för att komma åt eventhubbar-resurser:https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest

Konfigurera kundhanterade nycklar för eventhubbar:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

Så här integrerar du med Azure-hanterade identiteter:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Så här skapar du ett nyckelvalv:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Så här tillhandahåller du Key Vault-autentisering med en hanterad identitet:https://docs.microsoft.com/azure/key-vault/managed-identity

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Hantera identiteter på ett säkert och automatiskt sätt

**Vägledning:** För virtuella Azure-datorer eller webbprogram som körs på Azure App Service som används för att komma åt dina händelsehubbar använder du Hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda Azure Event Hubs. Kontrollera att Key Vault mjuk-borttagning är aktiverat.

Använd hanterade identiteter för att tillhandahålla Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory (AD). Med hanterade identiteter kan du autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Azure Key Vault, utan några autentiseringsuppgifter i koden.

Autentisera en hanterad identitet med Azure Active Directory för att komma åt eventhubbarresurser:https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest 

Konfigurera kundhanterade nycklar för eventhubbar:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

Konfigurerar hanterade identiteter:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Så här integrerar du med Azure-hanterade identiteter:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

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

**Vägledning:** Förskansa allt innehåll som överförs till azure-resurser som inte är beräkning, till exempel Azure Event Hubs, App Service, DataSjölagring, Blob Storage, Azure-databas för PostgreSQL osv. Microsoft kan inte komma åt dina data i dessa fall.

Microsoft anti-malware är aktiverat på den underliggande värden som stöder Azure-tjänster (till exempel Azure Cache för Redis), men det körs inte på kundinnehåll.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Se till att programvara och signaturer mot skadlig kod uppdateras

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Säkerhetskontroll: Dataåterställning](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Se till att regelbundna automatiska säkerhetskopieringar

**Vägledning**: Konfigurera återställning av geokatastrofer för Azure-händelsehubbar. När hela Azure-regioner eller datacenter (om inga tillgänglighetszoner används) upplever driftstopp är det viktigt för databearbetning att fortsätta att fungera i en annan region eller datacenter. Geo-disaster recovery och Geo-replication är därför viktiga funktioner för alla företag. Azure Event Hubs stöder både geo-haveriberedskap och geo-replikering, på namnområdesnivå. 

Förstå återställning av geokatastrofer för Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Utför fullständiga systemsäkerhetskopior och säkerhetskopiera alla kundhanterade nycklar

**Vägledning:** Azure Event Hubs tillhandahåller kryptering av data i vila med Azure Storage Service Encryption (Azure SSE). Event Hubs förlitar sig på Azure Storage för att lagra data och som standard krypteras alla data som lagras med Azure Storage med Microsoft-hanterade nycklar. Om du använder Azure Key Vault för att lagra kundhanterade nycklar kontrollerar du regelbundna automatiska säkerhetskopior av dina nycklar.

Se till att regelbundna automatiska säkerhetskopior av dina Key Vault-hemligheter med följande PowerShell-kommando: Backup-AzKeyVaultSecret

Konfigurera kundhanterade nycklar för kryptering av Azure Event Hubs-data i vila:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Så här säkerhetskopierar du Key Vault Secrets:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validera alla säkerhetskopior inklusive kundhanterade nycklar

**Vägledning**: Testa återställning av säkerhetskopierade kundhanterade nycklar.

 

Så här återställer du nyckelvalvsnycklar i Azure:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Se till att säkerhetskopior och kundhanterade nycklar skyddas

**Vägledning**: Aktivera mjukborttagning i Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning. Azure Event Hubs kräver kundhanterade nycklar för att ha Mjuk borttagning och Rensa inte konfigurerade.

Konfigurera mjuk borttagning för Azure Storage-konto som används för att hämta händelsehubbardata. Observera att den här funktionen inte stöds för Azure Data Lake Storage Gen 2 ännu.

Aktivera mjuk borttagning i Key Vault:https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

Konfigurera ett nyckelvalv med nycklar:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Mjuk borttagning för Azure Storage-blobbar:https://docs.microsoft.com//azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Säkerhetskontroll: Incident response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Skapa en vägledning för incidenthantering

**Vägledning**: Se till att det finns skriftliga incidenthanteringsplaner som definierar personalens roller samt faser av incidenthantering/hantering.

Konfigurera arbetsflödesautomatiseringar i Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide



**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Skapa en incidentbedömning och prioriteringsprocedur

**Vägledning**: Security Center tilldelar aviseringar en allvarlighetsgrad för att hjälpa dig att prioritera den ordning i vilken du deltar i varje avisering, så att när en resurs äventyras kan du komma åt den direkt. Allvarlighetsgraden baseras på hur säker Security Center är i att hitta eller analytiska används för att utfärda aviseringen samt den konfidensnivå som det fanns ont uppsåt bakom aktiviteten som ledde till aviseringen.

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

**Övervakning av Azure Security Center**: Ja

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

**Vägledning**: Följ Microsofts insatsregler för att säkerställa att dina https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1penetrationstester inte bryter mot Microsofts policyer: .
Du hittar mer information om Microsofts strategi och genomförande av Red Teaming och penetrationstester på webbplatser på flera webbplatser mot Microsofts hanterade molninfrastruktur, tjänster och program, här:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

## <a name="next-steps"></a>Nästa steg

- Se [Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Läs mer om [Azure Security-originalplaner](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
