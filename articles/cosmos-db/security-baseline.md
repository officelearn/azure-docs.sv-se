---
title: Azure Security Baseline för Cosmos DB
description: Azure Security Baseline för Cosmos DB
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: fd2706b6a6880d7c9454619e1315b2b5b5404561
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244281"
---
# <a name="azure-security-baseline-for-cosmos-db"></a>Azure Security Baseline för Cosmos DB

Azure Security Baseline for Cosmos DB innehåller rekommendationer som hjälper dig att förbättra säkerhetspositionen för distributionen.

Baslinjen för den här tjänsten hämtas från [Azure Security Benchmark version 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), som ger rekommendationer om hur du kan skydda dina molnlösningar på Azure med vår vägledning om bästa praxis.

Mer information finns i [översikt över Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Säkerhetskontroll: Nätverkssäkerhet](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Skydda resurser med nätverkssäkerhetsgrupper eller Azure-brandväggen i det virtuella nätverket

**Vägledning:** Genom att använda Azure Private Link kan du ansluta till ett Azure Cosmos-konto via en privat slutpunkt. Trafik mellan ditt virtuella nätverk och tjänsten passerar över Microsofts stamnätverk, vilket eliminerar exponering från det offentliga Internet. Du kan också minska risken för dataexfiltration genom att konfigurera en strikt uppsättning utgående regler för en nätverkssäkerhetsgrupp (NSG) och associera NSG med ditt undernät.

Du kan också använda tjänstslutpunkter för att skydda ditt Azure Cosmos-konto. Genom att aktivera en tjänstslutpunkt kan du konfigurera Azure Cosmos-konton så att åtkomst från endast ett specifikt undernät i ett virtuellt Azure-nätverk. När Slutpunkten för Azure Cosmos DB-tjänsten är aktiverad kan du begränsa åtkomsten till ett Azure Cosmos-konto med anslutningar från ett undernät i ett virtuellt nätverk.

Du kan också skydda data som lagras i ditt Azure Cosmos-konto med hjälp av IP-brandväggar. Azure Cosmos DB stöder IP-baserade åtkomstkontroller för stöd för inkommande brandvägg. Du kan ange en IP-brandvägg för Azure Cosmos-kontot med hjälp av Azure-portalen, Azure Resource Manager-mallarna eller via Azure CLI eller Azure PowerShell.

Översikt över Azure Private Link:https://docs.microsoft.com/azure/private-link/private-link-overview

Konfigurera en privat slutpunkt för Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints 

Så här skapar du en nätverkssäkerhetsgrupp med en säkerhetskonfiguration:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Konfigurerar IP-brandväggen i Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-firewall

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Övervaka och logga konfiguration och trafik av virtuella nätverk, undernät och nätverkskort

**Vägledning**: Använd Azure Security Center och följ nätverksskyddsrekommendationerna för att skydda nätverksresurser relaterade till ditt Azure Cosmos-konto.

När virtuella datorer distribueras i samma virtuella nätverk som ditt Azure Cosmos-konto kan du använda en nätverkssäkerhetsgrupp (NSG) för att minska risken för dataexfiltration. Aktivera NSG-flödesloggar och skicka loggar till ett Azure Storage-konto för trafikgranskningar. Du kan också skicka NSG-flödesloggar till en Log Analytics-arbetsyta och använda Traffic Analytics för att ge insikter om trafikflödet i ditt Azure-moln. Vissa fördelar med Traffic Analytics är möjligheten att visualisera nätverksaktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödesmönster och identifiera felkonfigurationer i nätverket.

Förstå nätverkssäkerhet som tillhandahålls av Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

Aktivera NSG-flödesloggar:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Aktivera och använda Traffic Analytics:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="13-protect-critical-web-applications"></a>1.3: Skydda kritiska webbprogram

**Vägledning**: Använd cors-funktionen (Cross-Origin Resource Sharing) för att göra det möjligt för ett webbprogram som körs under en domän att komma åt resurser i en annan domän. Webbläsare implementerar en säkerhetsbegränsning som kallas samma princip som förhindrar att en webbsida anropar API:er i en annan domän. CORS är dock ett säkert sätt att tillåta ursprungsdomänen att anropa API:er i en annan domän. När du har aktiverat CORS-supporten för ditt Azure Cosmos-konto utvärderas endast autentiserade begäranden för att avgöra om de tillåts enligt de regler som du har angett.

Konfigurera resursdelning mellan ursprung:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-cross-origin-resource-sharing

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Använd Advanced Threat Protection (ATP) för Azure Cosmos DB . ATP för Azure Cosmos DB tillhandahåller ytterligare ett lager av säkerhetsinformation som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja Azure Cosmos-konton. Med det här skyddsskiktet kan du hantera hot och integrera dem med centrala säkerhetsövervakningssystem.

Aktivera DDoS Protection Standard på de virtuella nätverk som är associerade med dina Azure Cosmos DB-instanser för att skydda mot DDoS-attacker. Använd Azure Security Center Integrated Threat Intelligence för att neka kommunikation med kända skadliga eller oanvända IP-adresser på Internet.

Så här konfigurerar duAzure Cosmos DB Advanced Threat Protection:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

KonfigureraR du DDoS-skydd:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Förstå Azure Security Center Integrated Threat Intelligence:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Spela in nätverkspaket och flödesloggar

**Vägledning**: Aktivera flödesloggar för nätverkssäkerhetsgrupper (NSG) och skicka loggar till ett lagringskonto för trafikgranskning. Du kan skicka NSG-flödesloggar till en Log Analytics-arbetsyta och använda Traffic Analytics för att ge insikter om trafikflödet i ditt Azure-moln. Vissa fördelar med Traffic Analytics är möjligheten att visualisera nätverksaktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödesmönster och identifiera felkonfigurationer i nätverket.

Aktivera NSG-flödesloggar:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Aktivera och använda Traffic Analytics:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuera nätverksbaserade system för intrångsdetektering/intrångsförebyggande (IDS/IPS)

**Vägledning**: Använd Advanced Threat Protection (ATP) för Azure Cosmos DB. ATP för Azure Cosmos DB tillhandahåller ytterligare ett lager av säkerhetsinformation som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja Azure Cosmos-konton. Med det här skyddsskiktet kan du hantera hot och integrera dem med centrala säkerhetsövervakningssystem. 

Konfigurerar Cosmos DB Advanced Threat Protection:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Hantera trafik till webbapplikationer

**Vägledning**: Ej tillämpligt. rekommendationen är avsedd för webbprogram som körs på Azure App Service eller beräkningsresurser.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimera komplexiteten och de administrativa omkostnaderna för nätverkssäkerhetsreglerna

**Vägledning:** För resurser som behöver åtkomst till ditt Azure Cosmos-konto använder du tjänsttaggar för virtuella nätverk för att definiera nätverksåtkomstkontroller på nätverkssäkerhetsgrupper eller Azure-brandväggen. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange tjänsttagnamnet (t.ex. Microsoft hanterar adressprefixen som omfattas av servicetag och uppdaterar automatiskt servicetag när adresserna ändras.

Mer information om hur du använder tjänsttaggar:https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Underhåll standardsäkerhetskonfigurationer för nätverksenheter

**Vägledning**: Definiera och implementera standardsäkerhetskonfigurationer för nätverksresurser med Azure Policy. Använd Azure Policy-alias i namnområdena "Microsoft.DocumentDB" och "Microsoft.Network" för att skapa anpassade principer för granskning eller framtvingning av nätverkskonfigurationen för dina Azure Cosmos DB-instanser. Du kan också använda inbyggda principdefinitioner för Azure Cosmos DB, till exempel:

- Distribuera avancerat skydd mot skydd mot Cosmos DB-konton

- Cosmos DB bör använda en slutpunkt för virtuella nätverkstjänster

Du kan också använda Azure Blueprints för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljöartefakter, till exempel Azure Resource Manager-mallar, rollbaserad åtkomstkontroll (RBAC) och principer i en enda skissdefinition. Du kan enkelt använda skissen på nya prenumerationer, miljöer och finjustera kontroll och hantering via versionshantering.

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här skapar du en Azure Blueprint:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regler för konfiguration av dokumenttrafik

**Vägledning**: Använd taggar för nätverksresurser som är associerade med din Azure Cosmos DB-distribution för att logiskt ordna dem till en taxonomi.

Så här skapar och använder du taggar:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Använd automatiserade verktyg för att övervaka nätverksresurskonfigurationer och identifiera ändringar

**Vägledning**: Använd Azure Activity Log för att övervaka nätverksresurskonfigurationer och identifiera ändringar för nätverksresurser relaterade till dina Azure Cosmos DB-instanser. Skapa aviseringar i Azure Monitor som utlöses när ändringar av kritiska nätverksresurser sker. 

Så här visar och hämtar du Azure Activity Log-händelser:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Så här skapar du aviseringar i Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [Säkerhetskontroll: Loggning och övervakning](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Använd godkända tidssynkroniseringskällor

**Vägledning**: Microsoft underhåller tidskällan som används för Azure-resurser som Azure Cosmos DB för tidsstämplar i loggarna.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurera hantering av centrala säkerhetsloggar

**Vägledning:** Ingest loggar via Azure Monitor för att aggregera säkerhetsdata som genereras av Azure Cosmos DB. I Azure Monitor använder du Log Analytics-arbetsytor för att fråga och utföra analyser och använda lagringskonton för långsiktig/arkiveringslagring. Du kan också aktivera och ombord data till Azure Sentinel eller en tredje parts Security Incident and Event Management (SIEM). 

Aktivera diagnostikloggar för Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/logging

Så här går du ombord på Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivera granskningsloggning för Azure-resurser

**Vägledning**: Aktivera diagnostikinställningar för Azure Cosmos DB och skicka loggarna till en Log Analytics-arbetsyta eller ett lagringskonto. Diagnostikinställningar i Azure Cosmos DB används för att samla in resursloggar. Dessa loggar fångas in per begäran och de kallas även "dataplanloggar". Några exempel på dataplanåtgärderna är borttagning, infoga och läsa. Du kan också aktivera diagnostikinställningar för Azure Activity Log och skicka dem till samma Logganalysarbetsyta.

Aktivera diagnostikinställningar för Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/logging

Aktivera diagnostikinställningar för Azure Activity Log:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Samla in säkerhetsloggar från operativsystem

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurera lagring av säkerhetslogglagring

**Vägledning**: I Azure Monitor anger du logglagringsperioden för Log Analytics-arbetsytor som är associerade med dina Azure Cosmos DB-instanser enligt organisationens efterlevnadsregler.

Så här ställer du in loggkvarhållningsparametrar:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2.6: Övervaka och granska loggar

**Vägledning**: Du kan utföra frågor i Log Analytics en arbetsyta för att söka efter termer, identifiera trender, analysera mönster och ge många andra insikter baserat på Azure Cosmos DB-loggar som du har samlat in.

Så här utför du frågor för Azure Cosmos DB i Logganalysarbetsytor:https://docs.microsoft.com/azure/cosmos-db/monitor-cosmos-db

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Aktivera aviseringar för avvikande aktivitet

**Vägledning**: I Azure Security Center aktiverar du Avancerat skydd mot hot för Azure Cosmos DB för att övervaka avvikande aktiviteter i säkerhetsloggar och händelser. Aktivera diagnostikinställningar i Azure Cosmos DB och skicka loggar till en Log Analytics-arbetsyta.

 

Du kan också gå in på din Log Analytics-arbetsyta till Azure Sentinel eftersom den tillhandahåller en sky-lösning (Security Orchestration Automated Response). Detta gör det möjligt att skapa spelböcker (automatiserade lösningar) för att åtgärda säkerhetsproblem. Dessutom kan du skapa anpassade loggaviseringar på din Log Analytics-arbetsyta med Azure Monitor.

Lista över hotskyddsaviseringar för Azure Cosmos DB:https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos

Så här går du ombord på Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Skapa, visa och hantera loggaviseringar med Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralisera loggning av skadlig kod

**Vägledning**: Ej tillämpligt. Azure Cosmos DB bearbetar inte eller producerar anti-malware relaterade loggar.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivera DNS-frågeloggning

**Vägledning**: Ej tillämpligt. Azure Cosmos DB bearbetar inte eller producerar DNS-relaterade loggar.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivera granskningsloggning på kommandoraden

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Säkerhetskontroll: Identitets- och åtkomstkontroll](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Föra en inventering av administrativa räkenskaper

**Vägledning**: Du kan använda iam-fönstret (Identity and Access control) i Azure-portalen för att konfigurera rollbaserad åtkomstkontroll (RBAC) och underhålla inventering på Azure Cosmos DB-resurser. Rollerna tillämpas på användare, grupper, tjänsthuvudnamn och hanterade identiteter i Active Directory. Du kan använda inbyggda roller eller anpassade roller för individer och grupper.

Azure Cosmos DB tillhandahåller inbyggd RBAC för vanliga hanteringsscenarier i Azure Cosmos DB. En person som har en profil i Azure Active Directory (AD) kan tilldela dessa RBAC-roller till användare, grupper, tjänsthuvudnamn eller hanterade identiteter för att bevilja eller neka åtkomst till resurser och åtgärder på Azure Cosmos DB-resurser.

Du kan också använda Azure AD PowerShell-modulen för att utföra adhoc-frågor för att identifiera konton som är medlemmar i administrativa grupper. 

Dessutom kan vissa åtgärder i Azure Cosmos DB styras med Azure Active Directory och kontospecifika huvudnycklar.  Använd kontoinställningen disableKeyBasedMetadataWriteAccess för att kontrollera nyckelåtkomst.

Förstå rollbaserad åtkomstkontroll i Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

Skapa egna anpassade roller med Azure Cosmos DB-åtgärder (Microsoft.DocumentDB-namnområde):https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdocumentdb

Skapa en ny roll i Azure Active Directory:https://docs.microsoft.com/azure/role-based-access-control/custom-roles

Hämta en katalogroll i Azure Active Directory med PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Så här hämtar du medlemmar i en katalogroll i Azure Active Directory med PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Begränsa endast användaråtkomst till dataåtgärder:https://docs.microsoft.com/azure/cosmos-db/how-to-restrict-user-data

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändra standardlösenord där så är tillämpligt

**Vägledning**: Begreppet standard- eller tomma lösenord finns inte i förhållande till Azure AD eller Azure Cosmos DB. I stället använder Azure Cosmos DB två typer av nycklar för att autentisera användare och ge åtkomst till dess data och resurser. huvudnycklar och resurstokens. Nycklarna kan återskapas när som helst.

Så här fungerar säker åtkomst till data i Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data

Så här återskapar du Azure Cosmos DB-nycklar:https://docs.microsoft.com/azure/cosmos-db/manage-with-powershell#regenerate-keys

Så här kommer du in på programmatiskt nycklar med Azure Active Directory:https://docs.microsoft.com/azure/cosmos-db/certificate-based-authentication

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Delat

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Använd särskilda administrativa konton

**Vägledning**: Ej tillämpligt. Azure Cosmos DB stöder inte administratörskonton.  All åtkomst är integrerad med Azure Active Directory och Azure rollbaserad åtkomstkontroll (RBAC).



**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Använd enkel inloggning (SSO) med Azure Active Directory

**Vägledning**: Azure Cosmos DB använder två typer av nycklar för att auktorisera användare och stöder inte SSO (Single Sign-On) på dataplansnivå. Åtkomst till kontrollplanet för Cosmos DB är tillgängligt via REST API och stöder SSO. Om du vill autentisera anger du auktoriseringshuvudet för dina begäranden till en JSON-webbtoken som du hämtar från Azure Active Directory.

Förstå Azure Database for Cosmos DB REST API:https://docs.microsoft.com/rest/api/cosmos-db/

Förstå SSO med Azure Active Directory:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Använda multifaktorautentisering för all Azure Active Directory-baserad åtkomst

**Vägledning**: Aktivera Azure Active Directory MultiFaktor-autentisering och följ Azure Security Center Identity and Access Management-rekommendationer.

Aktivera MFA i Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Så här övervakar du identitet och åtkomst i Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Använd dedikerade datorer (arbetsstationer för privilegierad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd PAW (Privileged Access Workstations) med multifaktorautentisering konfigurerad för att logga in på och konfigurera Azure-resurser.

Läs mer om arbetsstationer för privilegierad åtkomst:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Aktivera MFA i Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Logga och varna för misstänkt aktivitet från administrativa konton

**Vägledning**: Använd Advanced Threat Protection (ATP) för Azure Cosmos DB. ATP för Azure Cosmos DB tillhandahåller ytterligare ett lager av säkerhetsinformation som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja Azure Cosmos-konton. Med det här skyddsskiktet kan du hantera hot och integrera dem med centrala säkerhetsövervakningssystem. 

Dessutom kan du använda AZURE Active Directory (AD) Privileged Identity Management (PIM) för generering av loggar och aviseringar när misstänkt eller osäker aktivitet inträffar i miljön.

Använd Azure AD-riskidentifieringar för att visa aviseringar och rapporter om riskfyllda användarbeteenden.

Så här distribuerar du PIM (Privilegierad identitetshantering):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Förstå Azure AD-riskidentifieringar:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Hantera Azure-resurser från endast godkända platser

**Vägledning**: Konfigurera platsvillkoret för en princip för villkorlig åtkomst och hantera dina namngivna platser. Med namngivna platser kan du skapa logiska grupperingar av IP-adressintervall eller länder och regioner. Du kan begränsa åtkomsten till känsliga resurser, till exempel dina Azure Cosmos DB-instanser, till dina konfigurerade namngivna platser.

Konfigurera namngivna platser i Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="39-use-azure-active-directory"></a>3.9: Använd Azure Active Directory

**Vägledning**: Använd Azure Active Directory (AD) som det centrala autentiserings- och auktoriseringssystemet. Azure AD skyddar data genom att använda stark kryptering för data i vila och under överföring. Azure AD saltar också, hashar och lagrar användarautentiseringsuppgifter på ett säkert sätt.

Så här skapar och konfigurerar du en Active Directory-instans i Azure:https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

Konfigurera och hantera Azure Active Directory-autentisering med Azure SQL:https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelbundet granska och förena användaråtkomst

**Vägledning**: Azure Active Directory innehåller loggar för att identifiera inaktuella konton. Dessutom kan du använda Azure Identity Access Reviews för att effektivt hantera gruppmedlemskap, åtkomst till företagsprogram och rolltilldelningar. Användarens åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.

Så här använder du Azure Identity Access Reviews:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Övervaka försök att komma åt inaktiverade konton

**Vägd:** Du kan skapa diagnostikinställningar för Azure Active Directory-användarkonton, skicka granskningsloggar och inloggningsloggar till en Log Analytics-arbetsyta där du kan konfigurera önskade aviseringar.

Så här integrerar du Azure Activity Logs i Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Varning på kontots avvikelse för inloggningsbeteende

**Vägledning**: Använd Advanced Threat Protection (ATP) för Azure Cosmos DB. ATP för Azure Cosmos DB tillhandahåller ytterligare ett lager av säkerhetsinformation som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja Azure Cosmos-konton. Med det här skyddsskiktet kan du hantera hot och integrera dem med centrala säkerhetsövervakningssystem. 

Du kan också använda azure AD Identity Protection och riskidentifieringsfunktionen för att konfigurera automatiska svar på identifierade misstänkta åtgärder relaterade till användaridentiteter. Dessutom kan du inta loggar in i Azure Sentinel för vidare undersökning.

Så här visar du riskfyllda inloggningar i Azure Active Directory:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Konfigurera och aktivera riskprinciper för identitetsskydd:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Så här går du ombord på Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ge Microsoft åtkomst till relevanta kunddata under supportscenarier

**Vägledning**: För närvarande inte tillgänglig; Customer Lockbox stöds ännu inte för Azure Database for Cosmos DB.

Lista över tjänster som stöds av Customer Lockbox:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Ej tillämpligt

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Säkerhetskontroll: Dataskydd](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Föra en inventering av känslig information

**Vägledning**: Använd taggar för att hjälpa till att spåra Azure Cosmos DB-instanser som lagrar eller bearbetar känslig information.

Så här skapar och använder du taggar:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolera system som lagrar eller bearbetar känslig information

**Vägledning**: Implementera separata prenumerationer och/eller hanteringsgrupper för utveckling, test och produktion. Azure Cosmos DB-instanser avgränsas av virtuellt nätverk/undernät, taggade på rätt sätt och skyddas inom en nätverkssäkerhetsgrupp (NSG) eller Azure-brandväggen. Azure Cosmos DB-instanser som lagrar känsliga data bör isoleras. Genom att använda Azure Private Link kan du ansluta till ett Azure Cosmos DB-instanskonto via en privat slutpunkt. Den privata slutpunkten är en uppsättning privata IP-adresser i ett undernät i det virtuella nätverket. Du kan sedan begränsa åtkomsten till de valda privata IP-adresserna. 

Så här skapar du ytterligare Azure-prenumerationer:https://docs.microsoft.com/azure/billing/billing-create-subscription

Så här skapar du hanteringsgrupper:https://docs.microsoft.com/azure/governance/management-groups/create

Så här skapar och använder du taggar:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Konfigurera en privat slutpunkt för Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints

Så här skapar du en nätverkssäkerhetsgrupp med en säkerhetskonfiguration:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Övervaka och blockera obehörig överföring av känslig information

**Vägledning**: Du kan distribuera avancerat skydd mot hot för Azure Cosmos DB, som identifierar avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser. Det kan för närvarande utlösa följande aviseringar:

- Åtkomst från ovanliga platser

- Ovanlig dataextrahering

När du använder virtuella datorer för att komma åt dina Azure Cosmos DB-instanser kan du dessutom använda private link, brandvägg, nätverkssäkerhetsgrupper och tjänsttaggar för att minska risken för dataexfiltration. Microsoft hanterar den underliggande infrastrukturen för Azure Cosmos DB och har implementerat strikta kontroller för att förhindra förlust eller exponering av kunddata.

Konfigurerar Cosmos DB Advanced Threat Protection:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

Förstå kunddataskyddet i Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Delat

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Kryptera all känslig information under transport

**Vägledning**: Alla anslutningar till Azure Cosmos DB stöder HTTPS. Azure Cosmos DB stöder också TLS1.2. Det är möjligt att framtvinga en minsta TLS-version server-sida. För att göra [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com)det, vänligen kontakta .

Översikt över Cosmos DB Security:https://docs.microsoft.com/azure/cosmos-db/database-security

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Använda ett aktivt identifieringsverktyg för att identifiera känsliga data

**Vägledning**: Funktioner för automatisk dataidentifiering, klassificering och förlustförebyggande är ännu inte tillgängliga för Azure Cosmos DB. Du kan dock använda Azure Cognitive Search-integreringen för klassificering och dataanalys. Du kan också implementera en tredjepartslösning om det behövs för efterlevnadsändamål.

För den underliggande plattformen som hanteras av Microsoft behandlar Microsoft allt kundinnehåll som känsligt och gör stora ansträngningar för att skydda mot förlust av kunddata och exponering. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och underhållit en uppsättning robusta dataskyddskontroller och funktioner.

Index Azure Cosmos DB-data https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb?toc=/azure/cosmos-db/toc.json&ampmed Azure Cognitive Search: ;bc=/azure/cosmos-db/breadcrumb/toc.json

Förstå kunddataskyddet i Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Delat

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Använd Azure RBAC för att kontrollera åtkomsten till resurser

**Vägledning**: Azure Cosmos DB tillhandahåller inbyggd rollbaserad åtkomstkontroll (RBAC) för vanliga hanteringsscenarier i Azure Cosmos DB. En person som har en profil i Azure Active Directory kan tilldela dessa RBAC-roller till användare, grupper, tjänsthuvudnamn eller hanterade identiteter för att bevilja eller neka åtkomst till resurser och åtgärder på Azure Cosmos DB-resurser. Rolltilldelningar är begränsade till endast åtkomst på styrplan, vilket inkluderar åtkomst till Azure Cosmos-konton, databaser, behållare och erbjudanden (dataflöde).

Implementera RBAC i Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Använd värdbaserad dataförlustprevention för att genomdriva åtkomstkontroll

**Vägledning**: Ej tillämpligt. den här riktlinjen är avsedd för beräkningsresurser.

Microsoft hanterar den underliggande infrastrukturen för Cosmos DB och har implementerat strikta kontroller för att förhindra förlust eller exponering av kunddata.

Förstå kunddataskyddet i Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Kryptera känslig information i vila

**Vägledning**: Alla användardata som lagras i Cosmos DB krypteras i vila som standard. Det finns inga kontroller för att stänga av den. Azure Cosmos DB använder AES-256-kryptering på alla regioner där kontot körs.

Som standard hanterar Microsoft de nycklar som används för att kryptera data i ditt Azure Cosmos-konto. Du kan också välja att lägga till ett andra lager kryptering med dina egna nycklar.

Förstå kryptering i vila med Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Förstå nyckelhantering för kryptering i vila med Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-security-controls

Konfigurera kundhanterade nycklar för ditt Azure Cosmos DB-konto:https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Delat

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logga och avisera om ändringar i kritiska Azure-resurser

**Vägledning**: Använd Azure Monitor med Azure Activity Log för att skapa aviseringar för när ändringar sker i produktionsinstanser av Azure Cosmos DB.

Så här skapar du aviseringar för Azure Activity Log-händelser:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Så här skapar du aviseringar för Azure Activity Log-händelser:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Säkerhetskontroll: Sårbarhetshantering](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Kör automatiserade sårbarhetsskanningsverktyg

**Vägledning**: Följ rekommendationer från Azure Security Center för dina Azure Cosmos DB-instanser. 

Microsoft utför systemkorrigering och sårbarhetshantering på underliggande värdar som stöder dina Azure Cosmos DB-instanser. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och underhållit en uppsättning robusta dataskyddskontroller och funktioner.

Funktioner som stöds i Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Delat

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Distribuera en lösning för hantering av automatisk operativsystemets korrigering

**Vägledning**: Ej tillämpligt. den här riktlinjen är avsedd för beräkningsresurser.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Distribuera automatisk lösning för hantering av programkorrigering från tredje part

**Vägledning**: Ej tillämpligt. den här riktlinjen är avsedd för beräkningsresurser.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Jämför sårbarhetssökningar från rygg mot rygg

**Vägledning**: Ej tillämpligt. den här riktlinjen är avsedd för beräkningsresurser.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Använd en riskklassificeringsprocess för att prioritera reparation av upptäckta sårbarheter

**Vägledning**: Ej tillämpligt. den här riktlinjen är avsedd för beräkningsresurser.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Använd Identifiering av Azure Asset

**Vägd:** Använd Azure-portalen eller Azure Resource Graph för att identifiera alla resurser (inte begränsat till Azure Cosmos DB, men även resurser som beräkning, annan lagring, nätverk, portar och protokoll osv.) i dina prenumerationer.  Se till att du har rätt behörighet i din klientorganisation och kan räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via Resource Graph, rekommenderas det starkt att skapa och använda Azure Resource Manager-resurser framöver.

Så här skapar du frågor med Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Så här visar du dina Azure-prenumerationer:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Så här fungerar Azure rollbaserad åtkomstkontroll:https://docs.microsoft.com/azure/role-based-access-control/overview

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="62-maintain-asset-metadata"></a>6.2: Underhåll metadata för tillgångar

**Vägledning**: Använd taggar på dina Azure Cosmos DB-instanser och relaterade resurser med metadata för att logiskt ordna dem till en taxonomi.

Så här skapar och använder du taggar:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Vilka Azure Cosmos DB-resurser stöder taggar:https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support#microsoftdocumentdb

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanteringsgrupper och separata prenumerationer, där så är lämpligt, för att organisera och spåra tillgångar, inklusive men inte begränsat till Azure Cosmos DB-resurser. Stämma av lager regelbundet och se till att obehöriga resurser tas bort från prenumerationen i tid.

Så här skapar du ytterligare Azure-prenumerationer:https://docs.microsoft.com/azure/billing/billing-create-subscription

Så här skapar du hanteringsgrupper:https://docs.microsoft.com/azure/governance/management-groups/create

Så här skapar och använder du Taggar:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Underhåll en inventering av godkända Azure-resurser och programvarutitlar

**Vägledning**: Ej tillämpligt. Den här riktlinjen är avsedd för beräkningsresurser och Azure som helhet.

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

**Vägledning**: Ej tillämpligt. den här originalplanen är avsedd för beräkningsresurser.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Ta bort icke godkända Azure-resurser och program

**Vägledning**: Ej tillämpligt. Den här riktlinjen är avsedd för beräkningsresurser och Azure som helhet.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="68-use-only-approved-applications"></a>6.8: Använd endast godkända ansökningar

**Vägledning**: Ej tillämpligt. den här riktlinjen är avsedd för beräkningsresurser.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="69-use-only-approved-azure-services"></a>6.9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure Policy för att sätta begränsningar för vilken typ av resurser som kan skapas i kundprenumerationer med hjälp av följande inbyggda principdefinitioner:

- Otillåtna resurstyper 

- Tillåtna resurstyper

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här nekar du en viss resurstyp med Azure Policy:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="610-implement-approved-application-list"></a>6.10: Genomföra godkänd ansökningslista

**Vägledning**: Ej tillämpligt. den här riktlinjen är avsedd för beräkningsresurser.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: Begränsa användarnas möjlighet att interagera med AzureResources Manager via skript

**Vägledning**: Använd Azure Villkorlig åtkomst för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "Blockera åtkomst" för "Microsoft Azure Management"-appen. Detta kan förhindra att resurser skapas och ändras i en miljö med hög säkerhet.

Konfigurera villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Begränsa användarnas möjlighet att köra skript i beräkningsresurser

**Vägledning**: Ej tillämpligt. den här riktlinjen är avsedd för beräkningsresurser.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fysiskt eller logiskt segregera högriskapplikationer

**Vägledning**: Ej tillämpligt. Den här riktlinjen är avsedd för webbprogram som körs på Azure App Service eller beräkningsresurser.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Säkerhetskontroll: Säker konfiguration](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: Definiera och implementera standardsäkerhetskonfigurationer för dina Cosmos DB-instanser med Azure Policy. Använd Azure Policy-alias i namnområdet "Microsoft.DocumentDB" för att skapa anpassade principer för granskning eller framtvingning av konfigurationen av dina Cosmos DB-instanser. Du kan också använda inbyggda principdefinitioner för Azure Cosmos DB, till exempel:

- Distribuera avancerat skydd mot skydd mot Cosmos DB-konton

- Cosmos DB bör använda en slutpunkt för virtuella nätverkstjänster

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

**Vägledning:** Om du använder anpassade Azure-principdefinitioner för din Cosmos DB eller relaterade resurser använder du Azure Repos för att lagra och hantera din kod på ett säkert sätt.

Dokumentation för Azure https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops Repos:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Lagra anpassade operativsystemavbildningar på ett säkert sätt

**Vägledning**: Ej tillämpligt. den här riktlinjen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Distribuera verktyg för hantering av systemkonfigurationer

**Vägd:** Använd Azure Policy-alias i namnområdet "Microsoft.DocumentDB" för att skapa anpassade principer för aviseringar, granskning och genomdämning av systemkonfigurationer. Dessutom, utveckla en process och pipeline för att hantera principundantag.

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Distribuera verktyg för systemkonfigurationshantering för operativsystem

**Vägledning**: Ej tillämpligt. den här riktlinjen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementera automatisk konfigurationsövervakning för Azure-tjänster

**Vägd:** Använd Azure Policy-alias i namnområdet "Microsoft.DocumentDB" för att skapa anpassade principer för aviseringar, granskning och genomdämning av systemkonfigurationer. Använd Azure-princip [granskning], [neka] och [distribuera om det inte finns] för att automatiskt framtvinga konfigurationer för dina Azure Cosmos DB-instanser och relaterade resurser. 

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementera automatisk konfigurationsövervakning för operativsystem

**Vägledning**: Ej tillämpligt. den här riktlinjen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="711-manage-azure-secrets-securely"></a>7.11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning:** För virtuella Azure-datorer eller webbprogram som körs på Azure App Service som används för att komma åt dina Azure Cosmos DB-instanser använder du Hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda Azure Cosmos DB hemlig hantering. Kontrollera att Key Vault Soft Delete är aktiverat.

Så här integrerar du med Azure-hanterade identiteter:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Så här skapar du ett nyckelvalv:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Så här tillhandahåller du Key Vault-autentisering med en hanterad identitet:https://docs.microsoft.com/azure/key-vault/managed-identity

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Hantera identiteter på ett säkert och automatiskt sätt

**Vägledning:** För virtuella Azure-datorer eller webbprogram som körs på Azure App Service som används för att komma åt dina Azure Cosmos DB-instanser använder du Hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda Azure Cosmos DB hemlig hantering.

Använd hanterade identiteter för att tillhandahålla Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory (AD). Med hanterade identiteter kan du autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan några autentiseringsuppgifter i koden.

Konfigurerar hanterade identiteter:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Så här integrerar du med Azure-hanterade identiteter:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: Implementera autentiseringsskanner för att identifiera autentiseringsuppgifter i koden. Autentiseringsläsare kommer också att uppmuntra flytta identifierade autentiseringsuppgifter till säkrare platser som Azure Key Vault.

Konfigurera autentiseringsskanner:https://secdevtools.azurewebsites.net/helpcredscan.html

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Använd centralt hanterad programvara mot skadlig kod

**Vägledning**: Ej tillämpligt. den här riktlinjen är avsedd för beräkningsresurser. Microsoft Antimalware är aktiverat på den underliggande värden som stöder Azure-tjänster (till exempel Azure App Service), men det körs inte på kundinnehåll.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Förskansa filer som ska överföras till Azure-resurser som inte är beräkning

**Vägledning**: Microsoft Antimalware är aktiverat på den underliggande värden som stöder Azure-tjänster (till exempel Azure App Service), men det körs inte på kundinnehåll.

Det är ditt ansvar att försöka alla filer som överförs till azure-resurser som inte är beräkning, inklusive Azure Cosmos DB. Microsoft kan inte komma åt kunddata och kan därför inte utföra sökningar mot skadlig kod av kundinnehåll för din räkning.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Se till att programvara och signaturer mot skadlig kod uppdateras

**Vägledning**: Ej tillämpligt. referensvärdet är avsett för beräkningsresurser. Microsoft Antimalware är aktiverat på den underliggande värden som stöder Azure-tjänster, men det körs inte på kundinnehåll.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Säkerhetskontroll: Dataåterställning](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Se till att regelbundna automatiska säkerhetskopieringar

**Vägledning**: Azure Cosmos DB tar ögonblicksbilder av dina data var fjärde timme. Alla säkerhetskopior lagras separat i en lagringstjänst och dessa säkerhetskopior replikeras globalt för återhämtning mot regionala katastrofer. Vid varje given tidpunkt behålls endast de två sista ögonblicksbilderna. Men om containern eller databasen tas bort, behåller Azure Cosmos DB befintliga ögonblicksbilder av en specifik container eller databas i 30 dagar. Kontakta Azure Support för att återställa från en säkerhetskopia.

Förstå Azure Cosmos DB-automatiska säkerhetskopior:https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Utför fullständiga systemsäkerhetskopior och säkerhetskopiera alla kundhanterade nycklar

**Vägledning**: Azure Cosmos DB tar automatiskt säkerhetskopior av dina data med jämna mellanrum. Om databasen eller behållaren tas bort kan du lämna in en supportbiljett eller ringa Azure-stöd för att återställa data från automatiska onlinesäkerhetskopior. Azure-support är endast tillgänglig för valda planer, till exempel Standard, Utvecklare och planer som är högre än dem. För att återställa en specifik ögonblicksbild av säkerhetskopian kräver Azure Cosmos DB att data är tillgängliga under hela säkerhetskopieringscykeln för den ögonblicksbilden. 

Om du använder Key Vault för att lagra autentiseringsuppgifter för dina Cosmos DB-instanser kontrollerar du att dina nycklar regelbundet säkerhetskopior av dina nycklar.

Förstå Azure Cosmos DB-automatiska säkerhetskopior:https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Så här återställer du data i Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Så här säkerhetskopierar du key vault-nycklar:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Delat

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validera alla säkerhetskopior inklusive kundhanterade nycklar

**Vägd:** Om databas eller behållare tas bort kan du lämna in en supportbiljett eller ringa Azure-stöd för att återställa data från automatiska onlinesäkerhetskopior. Azure-support är endast tillgänglig för valda planer, till exempel Standard, Utvecklare och planer som är högre än dem. För att återställa en specifik ögonblicksbild av säkerhetskopian kräver Azure Cosmos DB att data är tillgängliga under hela säkerhetskopieringscykeln för den ögonblicksbilden.

Testa återställning av dina hemligheter som lagras i Azure Key Vault med PowerShell. Cmdlet restore-AzureKeyVaultKey skapar en nyckel i det angivna nyckelvalvet. Den här nyckeln är en replik av den säkerhetskopierade nyckeln i indatafilen och har samma namn som den ursprungliga nyckeln.

Förstå Azure Cosmos DB-automatiska säkerhetskopior:

https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Så här återställer du data i Azure Cosmos DB:

https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Så här återställer du Hemligheter för Azure Key Vault:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Delat

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Se till att säkerhetskopior och kundhanterade nycklar skyddas

**Vägledning**: Eftersom alla användardata som lagras i Cosmos DB krypteras i vila och under transport behöver du inte vidta några åtgärder. Ett annat sätt att uttrycka detta är att kryptering i vila är "på" som standard. Det finns inga kontroller för att stänga av eller slå på den. Azure Cosmos DB använder AES-256-kryptering på alla regioner där kontot körs.

Aktivera Mjuk borttagning i Nyckelvalvet för att skydda nycklar mot oavsiktlig eller skadlig borttagning.

Förstå datakryptering i Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Aktivera Mjuk borttagning i Key Vault:https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Delat

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Säkerhetskontroll: Incident response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Skapa en vägledning för incidenthantering

**Vägledning**: Skapa en incidenthanteringsguide för din organisation. Se till att det finns skriftliga incidenthanteringsplaner som definierar alla roller för personal samt faser av incidenthantering/hantering från identifiering till granskning efter incidenten.

Du kan också utnyttja NIST:s guide för hantering av datasäkerhetsincidenter för att underlätta skapandet av en egen incidenthanteringsplan:https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

Konfigurera arbetsflödesautomatiseringar i Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Vägledning om hur du bygger din egen process för hantering av säkerhetsincidenter:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Microsoft Security Response Center's Anatomy of an Incident:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Skapa en incidentbedömning och prioriteringsprocedur

**Vägledning**: Security Center tilldelar allvarlighetsgrad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som ska undersökas först. Allvarlighetsgraden baseras på hur säker Security Center är på att hitta eller den analys som används för att utfärda aviseringen samt den konfidensnivå som det fanns ont uppsåt bakom aktiviteten som ledde till aviseringen.

Dessutom tydligt markera abonnemang (för ex. produktion, icke-prod) och skapa ett namngivningssystem för att tydligt identifiera och kategorisera Azure-resurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

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

**Vägledning**: Följ Microsofts insatsregler för att säkerställa att dina penetrationstester inte bryter mot Microsofts policyer:https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Du hittar mer information om Microsofts strategi och genomförande av Red Teaming och penetrationstester på webbplatser på flera webbplatser mot Microsoft-hanterad molninfrastruktur, molntjänster och -program här:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Delat

## <a name="next-steps"></a>Nästa steg

- Se [Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Läs mer om [Azure Security-originalplaner](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
