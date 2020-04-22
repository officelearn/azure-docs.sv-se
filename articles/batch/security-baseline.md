---
title: Azure Security Baseline för batch
description: Azure Security Baseline för batch
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8bda712523faa90f32a12fe7eecce27dccf3a55c
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756633"
---
# <a name="azure-security-baseline-for-batch"></a>Azure Security Baseline för batch

Azure Security Baseline for Batch innehåller rekommendationer som hjälper dig att förbättra säkerhetspositionen för distributionen.

Baslinjen för den här tjänsten hämtas från [Azure Security Benchmark version 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), som ger rekommendationer om hur du kan skydda dina molnlösningar på Azure med vår vägledning om bästa praxis.

Mer information finns i [översikt över Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Säkerhetskontroll: Nätverkssäkerhet](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Skydda resurser med nätverkssäkerhetsgrupper eller Azure-brandväggen i det virtuella nätverket

**Vägledning**: Distribuera Azure Batch-pooler i virtuella nätverk. Om du vill att poolberäkningsnoder ska kunna kommunicera säkert med andra virtuella datorer eller med ett lokalt nätverk kan du etablera poolen i ett undernät till ett virtuellt Azure-nätverk. Distribuera din pool inom ett virtuellt nätverk ger dig också kontroll över nätverkssäkerhetsgruppen (NSG) som används för att skydda de enskilda nodernas nätverksgränssnitt (NIC), samt undernätet. Konfigurera NSG så att trafik från endast betrodda IP/platser på Internet tillåts.

Så här skapar du en Azure Batch Pool i ett virtuellt nätverk:

https://docs.microsoft.com/azure/batch/batch-virtual-network

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Övervaka och logga konfiguration och trafik av virtuella nätverk, undernät och NICS

**Vägledning**: Använd Azure Security Center och åtgärda rekommendationer för nätverksskydd som är relaterade till den virtuella nätverks- och nätverkssäkerhetsgruppen (NSG) som är associerad med batch-poolen. Aktivera flödesloggar på NSG som används för att skydda din batchpool och skicka loggar till ett Azure Storage-konto för trafikgranskning. Du kan också skicka NSG-flödesloggar till en Azure Log Analytics-arbetsyta och använda Azure Traffic Analytics för att ge insikter om trafikflödet i ditt Azure-moln. Vissa fördelar med Azure Traffic Analytics är möjligheten att visualisera nätverksaktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödesmönster och identifiera felkonfigurationer för nätverk.

Aktivera NSG-flödesloggar:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Aktivera och använda Traffic Analytics:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Förstå nätverkssäkerhet som tillhandahålls av Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="13-protect-critical-web-applications"></a>1.3: Skydda kritiska webbprogram

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Aktivera Azure DDoS (distribuerat denial-of-service) Standardskydd i det virtuella nätverket som skyddar din Azure Batch-pool för skydd mot DDoS-attacker. Använd Azure Security Center Integrated Threat Intelligence för att neka kommunikation med kända skadliga eller oanvända IP-adresser på Internet.

KonfigureraR du DDoS-skydd:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Förstå Azure Security Center Integrated Threat Intelligence:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Spela in nätverkspaket och flödesloggar

**Vägledning**: Aktivera flödesloggar i nätverkssäkerhetsgruppen (NSG) som används för att skydda din Azure Batch-pool och skicka loggar till ett Azure Storage-konto för trafikgranskning.

Aktivera NSG-flödesloggar:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6: Distribuera nätverksbaserade intrångsdetekterings-/intrångsskyddssystem

**Vägledning:** Om det behövs för efterlevnadsändamål väljer du en virtuell nätverksinstallation från Azure Marketplace som stöder intrångsidentifieringssystem (IDS) och IPS-funktioner (Intrusion Prevention Systems) med funktioner för inspektion av nyttolast.

Om intrångsidentifiering och/eller förebyggande baserat på nyttolastinspektion inte är ett krav kan Azure-brandväggen med hotinformation användas. Azure Firewall threat intelligence-baserad filtrering kan varna och neka trafik till och från kända skadliga IP-adresser och domäner. IP-adresser och domäner kommer från Microsoft Threat Intelligence-feeden.

Distribuera Azure-brandväggen med en offentlig IP-adress i samma virtuella nätverk som dina Azure Batch Pool-noder. Konfigurera NAT-regler (Network Address Translation) mellan betrodda platser på Internet och de privata IP-adresserna för dina enskilda poolnoder. Konfigurera "Alert och neka" för att blockera och blockera trafik till/från kända skadliga IP-adresser och domäner under Hot Intelligence på Azure-brandväggen. IP-adresser och domäner kommer från Microsoft Threat Intelligence-feeden och endast högsta förtroendeposter inkluderas. 

Så här skapar du en Azure Batch Pool i ett virtuellt nätverk:

https://docs.microsoft.com/azure/batch/batch-virtual-network

Distribuerar Azure-brandväggen:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7: Hantera trafik till dina webbapplikationer

**Vägledning**: Benchmark är inte tillämpligt, Benchmark är avsett för webbprogram som körs på Azure App Service- eller IaaS-instanser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimera komplexiteten och de administrativa omkostnaderna för nätverkssäkerhetsreglerna

**Vägledning**: Använd taggar för virtuella nätverkstjänst för att definiera nätverksåtkomstkontroller för nätverkssäkerhetsgrupper eller Azure-brandväggar som är associerade med dina Azure Batch-pooler. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange tjänsttagnamnet (t.ex. ApiManagement) i lämpligt käll- eller målfält för en regel kan du tillåta eller neka trafik för motsvarande tjänst. Microsoft hanterar adressprefixen som omfattas av servicetag och uppdaterar automatiskt servicetag när adresserna ändras.

Förstå och använda tjänsttaggar:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Underhåll standardsäkerhetskonfigurationer för nätverksenheter

**Vägledning**: Definiera och implementera standardsäkerhetskonfigurationer för nätverksresurser som är associerade med dina Azure Batch-pooler med Azure Policy. Använd Azure Policy-alias i namnområdena "Microsoft.Batch" och "Microsoft.Network" för att skapa anpassade principer för granskning eller framtvingning av nätverkskonfigurationen för dina Azure Batch-pooler.

Konfigurera och hantera Azure-princip:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="110-document-traffic-configuration-rules"></a>1.10: Konfigurationsregler för dokumenttrafik

**Vägd:** Använd taggar för nätverkstjänstgrupper (NSG) och andra resurser som är relaterade till nätverkssäkerhet och trafikflöde som är associerade med dina Azure-batchpooler. För enskilda NSG-regler använder du fältet "Beskrivning" för att ange affärsbehov och/eller varaktighet (etc.) för alla regler som tillåter trafik till/från ett nätverk.

Använd någon av de inbyggda Azure-principdefinitionerna som är relaterade till taggning, till exempel "Kräv tagg och dess värde" för att säkerställa att alla resurser skapas med taggar och för att meddela dig om befintliga otaggade resurser.

Du kan använda Azure PowerShell eller Azure CLI för att slå upp eller utföra åtgärder på resurser baserat på deras taggar.

Så här skapar och använder du taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Så här skapar du ett virtuellt nätverk:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Så här skapar du en NSG:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Använd automatiserade verktyg för att övervaka nätverksresurskonfigurationer och identifiera ändringar

**Vägledning**: Använd Azure Activity Log för att övervaka nätverksresurskonfigurationer och identifiera ändringar för nätverksresurser relaterade till dina Azure Batch-pooler. Skapa aviseringar i Azure Monitor som utlöses när ändringar av kritiska nätverksresurser sker.

Så här visar och hämtar du Azure Activity Log-händelser:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view 

Så här skapar du aviseringar i Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [Säkerhetskontroll: Loggning och övervakning](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Använda godkända tidssynkroniseringskällor

**Vägledning**: För Azure Batch tillhandahåller Microsoft som standard tidssynkronisering. Om du har specifika krav på tidssynkronisering kan du implementera ändringarna.

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurera hantering av centrala säkerhetsloggar

**Vägledning:** Inbyggt Azure Batch-konto till Azure Monitor för att aggregera säkerhetsdata som genereras av klusterenheterna. Utnyttja anpassade frågor för att identifiera och svara på hot i miljön.  För övervakning på Azure Batch-resursnivå använder du batch-API:erna för att övervaka eller fråga status för dina resurser, inklusive jobb, aktiviteter, noder och pooler.

Så här går du till ett Azure Batch-konto till Azure Monitor:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivera granskningsloggning för Azure-resurser

**Vägledning:** För övervakning av Azure Batch-kontonivå övervakar du varje batchkonto med hjälp av funktioner i Azure Monitor. Azure Monitor samlar in mått och eventuellt diagnostikloggar för resurser som omfattas av ett batchkonto, till exempel pooler, jobb och uppgifter. Samla in och använda dessa data manuellt eller programmässigt för att övervaka aktiviteter i ditt Batch-konto och för att diagnostisera problem.

För övervakning på Azure Batch-resursnivå använder du Azure Batch-API:erna för att övervaka eller fråga status för dina resurser, inklusive jobb, uppgifter, noder och pooler.

Konfigurera övervakning och loggning på Azure Batch-kontonivå:

https://docs.microsoft.com/azure/batch/monitoring-overview

Förstå övervakning på batchresursnivå:

https://docs.microsoft.com/azure/batch/monitoring-overview#batch-resource-monitoring

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="24-collect-security-logs-from-operating-system"></a>2.4: Samla in säkerhetsloggar från operativsystemet

**Vägledning**: Azure Monitor samlar in mått och diagnostikloggar för resurser i ditt Azure Batch-konto. Samla in och använda dessa data på en mängd olika sätt för att övervaka ditt Azure Batch-konto och diagnostisera problem. Du kan också konfigurera måttaviseringar så att du får meddelanden när ett mått når ett angivet värde.

Om det behövs kan du ansluta till dina enskilda poolnoder via Secured Shell (SSH) eller Remote Desktop Protocol (RDP) för att komma åt lokala operativsystemloggar.

Så här samlar du in diagnostikloggar från ditt Azure Batch-konto:

https://docs.microsoft.com/azure/batch/batch-diagnostics#batch-diagnostics

Så här fjärranslutning till dina Azure Batch-poolnoder:

https://docs.microsoft.com/azure/batch/batch-api-basics#error-handling

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurera lagring av säkerhetslogglagring

**Vägledning:** Inbyggd Azure Batch-konto till Azure Monitor. Se till att arbetsytan Azure Log Analytics som används har logglagringsperioden inställd enligt organisationens efterlevnadsregler

Konfigurera övervakning och loggning av Azure Batch:

https://docs.microsoft.com/azure/batch/monitoring-overview

Konfigurerar Azure Log Analytics-lagringsperiod för arbetsyta:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2.6: Övervaka och granska loggar

**Vägledning**: Skapa Azure Batch-måttaviseringar som utlöser när värdet för ett angivet mått överskrider ett visst tröskelvärde.

Konfigurera Azure Batch-måttaviseringar:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Aktivera aviseringar för avvikande aktivitet

**Vägledning**: Skapa Azure Batch-måttaviseringar som utlöser när värdet för ett angivet mått överskrider ett visst tröskelvärde.

Konfigurera Azure Batch-måttaviseringar:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralisera loggning mot skadlig kod

**Vägledning**: Använd Windows Defender på dina enskilda batchnoder när det gäller Windows-operativsystem, eller ange din egen anti-malware-lösning om du använder Linux.

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivera DNS-frågeloggning

**Vägledning**: Implementera tredjepartslösning för dns-loggning

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivera granskningsloggning på kommandoraden

**Vägledning**: Konfigurera konsolloggning och PowerShell-transkription manuellt per nod.

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Säkerhetskontroll: Identitets- och åtkomstkontroll](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1: Underhåll inventering av administrativa konton

**Vägledning**: Underhåll posten för det lokala administrativa konto som skapas under etableringen av Azure Batch-poolen samt alla andra konton som du skapar. Om Azure Active Directory -integrering (AAD) används har AAD dessutom inbyggda roller som uttryckligen måste tilldelas och därför kan ifrågasättas. Använd AAD PowerShell-modulen för att utföra adhoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

Dessutom kan du använda Azure Security Center Identity and Access Management rekommendationer.

Så här skaffar du en katalogroll i AAD med PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Så här skaffar du medlemmar i en katalogroll i AAD med PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Så här övervakar du identitet och åtkomst med Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändra standardlösenord där så är tillämpligt

**Vägledning**: När du etablerar en Azure Batch-pool får du möjlighet att skapa lokala datorkonton. Det finns inga standardlösenord att ändra, men du kan ange olika lösenord för åtkomst till Säkrat skal (SSH) och RDP (Remote Desktop Protocol). När Azure Batch Pool har konfigurerats kan du generera en slumpmässig användare för enskilda noder i Azure-portalen eller via Azure Resource Manager API.

Så här lägger du till en användare i specifik beräkningsnod:

https://docs.microsoft.com/rest/api/batchservice/computenode/adduser

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3.3: Se till att de särskilda administrativa räkenskaperna används

**Vägledning**: Integrera autentisering för Azure Batch-program med Azure Active Directory. Skapa principer och procedurer kring användningen av dedikerade administrativa konton.

Dessutom kan du använda Azure Security Center Identity and Access Management rekommendationer.

Så här autentiserar du batchprogram med Azure Active Directory:

https://docs.microsoft.com/azure/batch/batch-aad-auth

Så här övervakar du identitet och åtkomst med Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3.4: Använda enkel inloggning (SSO) med Azure Active Directory

**Vägledning**: Inte tillämpligt, Medan Azure Batch stöder Azure AD-autentisering stöds inte enkel inloggning.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Använd multifaktorautentisering för all Azure Active Directory-baserad åtkomst.

**Vägledning**: Integrera autentisering för Azure Batch-program med Azure Active Directory (AAD). Aktivera MFA (Multi Factor Authentication) (AAD multifaktor authentication) och följ Azure Security Center Identity and Access Management-rekommendationerna.

 

Aktivera MFA i Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Så här övervakar du identitet och åtkomst i Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Använd dedikerade datorer (arbetsstationer för privilegierad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd PAWs (privilegierade åtkomstarbetsstationer) med MFA (Multifaktor authentication) konfigurerade för att logga in på och konfigurera dina Azure Batch-resurser.

Läs mer om arbetsstationer för privilegierad åtkomst:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Aktivera MFA i Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Logga och varna för misstänkt aktivitet från administrativa konton

**Vägd:** Om du har integrerad autentisering för Azure Batch-program med Azure Active Directory (AAD) använder du Azure Active Directory-säkerhetsrapporter för generering av loggar och aviseringar när misstänkt eller osäker aktivitet inträffar i miljön. Använd Azure Security Center för att övervaka identitets- och åtkomstaktivitet.

Identifiera Azure AD-användare som flaggats för riskfylld aktivitet:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Så här övervakar du användarnas identitets- och åtkomstaktivitet i Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8: Hantera Azure-resurs från endast godkända platser

**Vägledning:** Om du har integrerad autentisering för Azure Batch-program med Azure Active Directory kan du använda namngivna platser för villkorlig åtkomst för att tillåta åtkomst från endast specifika logiska grupperingar av IP-adressintervall eller länder/regioner.

Konfigurera namngivna platser i Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="39-use-azure-active-directory"></a>3.9: Använd Azure Active Directory

**Vägledning**: Använd Azure Active Directory (AAD) som det centrala autentiserings- och auktoriseringssystemet och integrera autentisering för Azure Batch-program med AAD. AAD skyddar data genom att använda stark kryptering för data i vila och under överföring. AAD saltar också, hashar och lagrar användaruppgifter på ett säkert sätt.

Så här skapar och konfigurerar du en AAD-instans:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

Så här autentiserar du Batch-program med AAD:

https://docs.microsoft.com/azure/batch/batch-aad-auth

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelbundet granska och stämma av användaråtkomst

**Vägledning**: Azure Active Directory(AAD) tillhandahåller loggar som hjälper dig att identifiera inaktuella konton. Dessutom kan du använda Azure Identity Access Reviews för att effektivt hantera gruppmedlemskap, åtkomst till företagsprogram och rolltilldelningar. Användarnas åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.

Så här använder du Azure Identity Access Reviews:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Övervaka försök att komma åt inaktiverade konton

**Vägledning**: Skapa diagnostikinställningar för Azure Active Directory-användarkonton, skicka granskningsloggar och inloggningsloggar till en Azure Log Analytics-arbetsyta. Konfigurera önskade aviseringar i Azure Log Analytics-arbetsytan.

Så här integrerar du Azure Activity Logs i Azure Monitor:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Varning på kontoinloggningsbeteendeavvikelse

**Vägledning**: Använd Azure Active Directory (AAD) Riskidentifieringar och identitetsskyddsfunktionen för att konfigurera automatiska svar på identifierade misstänkta åtgärder relaterade till användaridentiteter. Dessutom kan du matsätta data i Azure Sentinel för vidare undersökning.

Så här visar du riskfyllda AAD-inloggningar:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Konfigurera och aktivera riskprinciper för identitetsskydd:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Så här går du ombord på Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="313-divprovide-microsoft-with-access-to-relevant-customer-data-during-support-scenariosbrdiv"></a>3.13: <div>Ge Microsoft åtkomst till relevanta kunddata under supportscenarier<br></div>

**Vägledning**: Ej tillgänglig. Customer Lockbox stöds ännu inte för Azure Batch.
 
Lista över tjänster som stöds av Customer Lockbox:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Säkerhetskontroll: Dataskydd](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Föra en inventering av känslig information

**Vägledning**: Använd taggar för att hjälpa till att spåra Azure-resurser som lagrar eller bearbetar känslig information.

Så här skapar och använder du taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolera system som lagrar eller bearbetar känslig information

**Vägledning**: Implementera separata prenumerationer och/eller hanteringsgrupper för utveckling, test och produktion. Azure Batch Pools ska avgränsas med virtuellt nätverk/undernät, taggas på rätt sätt och skyddas med en nätverkssäkerhetsgrupper (NSG). Azure Batch-data ska finnas i ett skyddat Azure Storage-konto.

Så här skapar du en Azure Batch Pool i ett virtuellt nätverk:

https://docs.microsoft.com/azure/batch/batch-virtual-network

Så här skyddar du Azure Storage-konton:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Övervaka och blockera obehörig överföring av känslig information.

**Vägledning:** För Azure Storage-konton som är associerade med dina Azure Batch-pooler som innehåller känslig information markerar du dem som känsliga med taggar och skyddar dem med bästa praxis för Azure.

Dataidentifiering, klassificering och förlustförebyggande funktioner är ännu inte tillgängliga för Azure Storage eller beräkningsresurser. Implementera tredjepartslösning om det behövs för efterlevnadsändamål.

För den underliggande plattformen som hanteras av Microsoft behandlar Microsoft allt kundinnehåll som känsligt och gör stora ansträngningar för att skydda mot förlust av kunddata och exponering. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och underhållit en uppsättning robusta dataskyddskontroller och funktioner.

Förstå kunddataskyddet i Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

Så här skyddar du Azure Storage-konton:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Delat

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Kryptera all känslig information under transport

**Vägledning**: Kryptera all känslig information under transport. Microsoft Azure-resurser förhandlar som standard om TLS 1.2. Se till att alla klienter som ansluter till dina Azure Batch-pooler eller datalager (Azure Storage Accounts) kan förhandla om TLS 1.2 eller senare.

Kontrollera att HTTPS krävs för att komma åt lagringskontot som innehåller dina Azure Batch-data.

Förstå Azure Storage-kontokryptering under överföring:

https://docs.microsoft.com/azure/storage/common/storage-security-guide#encryption-in-transit

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Delat

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Använda ett aktivt identifieringsverktyg för att identifiera känsliga data

**Vägledning:** För Azure Storage-konton som är associerade med dina Azure Batch-pooler som innehåller känslig information markerar du dem som känsliga med taggar och skyddar dem med bästa praxis för Azure.

Dataidentifiering, klassificering och förlustförebyggande funktioner är ännu inte tillgängliga för Azure Storage eller beräkningsresurser. Implementera tredjepartslösning om det behövs för efterlevnadsändamål.

För den underliggande plattformen som hanteras av Microsoft behandlar Microsoft allt kundinnehåll som känsligt och gör stora ansträngningar för att skydda mot förlust av kunddata och exponering. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och underhållit en uppsättning robusta dataskyddskontroller och funktioner.

Förstå kunddataskyddet i Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

Så här skyddar du Azure Storage-konton:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Delat

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Använd Azure RBAC för att kontrollera åtkomsten till resurser

**Vägd:** Använd Azure Active Directory(AAD) Rollbaserad åtkomstkontroll (RBAC) för att styra åtkomsten till hanteringsplanet för Azure-resurser, inklusive batchkonto, batch-pooler och lagringskonton.

Förstå Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

Konfigurera RBAC i Azure:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Använd värdbaserad dataförlustprevention för att genomdriva åtkomstkontroll

**Vägledning**: Funktioner för dataidentifiering, klassificering och förlustförebyggande är ännu inte tillgängliga för Azure Storage- eller beräkningsresurser. Implementera tredjepartslösning om det behövs för efterlevnadsändamål.

För den underliggande plattformen som hanteras av Microsoft behandlar Microsoft allt kundinnehåll som känsligt och gör stora ansträngningar för att skydda mot förlust av kunddata och exponering. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och underhållit en uppsättning robusta dataskyddskontroller och funktioner.

Förstå kunddataskyddet i Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Delat

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Kryptera känslig information i vila

**Vägledning:** För lagringskonton som är associerade med ditt Azure Batch-konto rekommenderar vi att Microsoft kan hantera krypteringsnycklar, men du har möjlighet att hantera dina egna nycklar om det behövs.

Hantera krypteringsnycklar för Azure Storage-konton:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logga och avisera om ändringar i kritiska Azure-resurser

**Vägledning**: Använd Azure Monitor med Azure Activity Log för att skapa aviseringar för när ändringar sker till kritiska Azure-resurser relaterade till eller associerade med dina Azure Batch-konton/-pooler.

Konfigurera diagnostikinställningar för lagringskonton som är associerade med Azure Batch Pool för att övervaka och logga alla CRUD-åtgärder mot pooldata.

Så här skapar du aviseringar för Azure Activity Log-händelser:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Aktivera ytterligare loggning/granskning för ett Azure Storage-konto:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Säkerhetskontroll: Sårbarhetshantering](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Kör verktyg för automatisk sårbarhetsskanning

**Vägledning**: För Azure Batch Pool-noder ansvarar du för att hantera sårbarhetshanteringslösningen.

Om du har en Rapid7-, Qualys- eller någon annan prenumeration på sårbarhetshanteringsplattformen kan du eventuellt installera sårbarhetsbedömningsagenter manuellt på batch-poolnoder och hantera noder via respektive portal.

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Distribuera lösning för hantering av automatisk operativsystemskorrigering

**Vägledning**: Microsoft för att underhålla och uppdatera bas-Nodavbildningar för Azure Batch Pool. Kontrollera att Azure Batch Pool-noders operativsystem förblir korrigerat under hela klustrets livstid, vilket kan kräva att automatiska uppdateringar aktiveras, övervakar noderna eller utför periodiska omstarter.


**Övervakning av Azure Security Center**: Ja

**Ansvar**: Delat

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Distribuera automatisk lösning för hantering av programkorrigering från tredje part

**Vägledning**: Se till att Azure Batch Pool-noders program från tredje part förblir korrigerade under hela klustrets livstid, vilket kan kräva att du aktiverar automatiska uppdateringar, övervakar noderna eller utför periodiska omstarter.


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Jämför sårbarhetsskanningar från rygg mot rygg

**Vägledning**: Om du har en Rapid7-, Qualys- eller någon annan prenumeration på sårbarhetshanteringsplattformen kan du använda leverantörens portal för att visa och jämföra sårbarhetssökningar från rygg mot rygg.

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Använd en riskklassificeringsprocess för att prioritera reparation av identifierade säkerhetsproblem.

**Vägledning**: Använd ett vanligt riskbedömningsprogram (t.ex. gemensamt system för bedömning av sårbarhet) eller standardriskklassificeringarna som tillhandahålls av ditt skanningsverktyg från tredje part.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Använd Identifiering av Azure Asset

**Vägledning**: Använd Azure Resource Graph för att fråga/identifiera alla resurser (till exempel beräkning, lagring, nätverk osv.) i dina prenumerationer. Se till att du har lämpliga (läs) behörigheter i din klientorganisation och kan räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via Resource Graph, rekommenderas det starkt att skapa och använda Azure Resource Manager (ARM) resurser framöver.

Så här skapar du frågor med Azure Resource Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Så här visar du dina Azure-prenumerationer:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Förstå Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="62-maintain-asset-metadata"></a>6.2: Underhåll metadata för tillgångar

**Vägledning**: Använd taggar på Azure-resurser som ger metadata för att logiskt ordna dem till en taxonomi.

Så här skapar och använder du taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanteringsgrupper och separata prenumerationer, där så är lämpligt, för att organisera och spåra tillgångar. Stämma av lager regelbundet och se till att obehöriga resurser tas bort från prenumerationen i tid.

Så här skapar du ytterligare Azure-prenumerationer:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Så här skapar du hanteringsgrupper:

https://docs.microsoft.com/azure/governance/management-groups/create

Så här skapar och användare Taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Underhåll inventering av godkända Azure-resurser och programvarutitlar.

**Vägledning**: Definiera lista över godkända Azure-resurser och godkänd programvara för beräkningsresurser


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Övervaka för icke godkända Azure-resurser

**Vägledning**: Använd Azure-principen för att begränsa vilken typ av resurser som kan skapas i kundprenumerationer med hjälp av följande inbyggda principdefinitioner:

- Otillåtna resurstyper
- Tillåtna resurstyper

Använd Azure Resource Graph för att fråga/identifiera resurser i dina prenumerationer. Se till att alla Azure-resurser som finns i miljön är godkända.

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här skapar du frågor med Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Övervaka icke godkända program i beräkningsresurser

**Vägledning:** För Azure Batch Pool-noder implementerar du en tredjepartslösning för att övervaka klusternoder för icke godkända program.


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Ta bort icke godkända Azure-resurser och program

**Vägledning:** För Azure Batch Pool-noder implementerar du en tredjepartslösning för att övervaka klusternoder för icke godkända program.


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="68-use-only-approved-applications"></a>6.8: Använd endast godkända ansökningar

**Vägledning**: För Azure Batch Pool-noder implementerar du en tredjepartslösning för att förhindra att obehörig programvara körs.


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="69-use-only-approved-azure-services"></a>6.9: Använd endast godkända Azure Services

**Vägledning**: Använd Azure-principen för att begränsa vilken typ av resurser som kan skapas i kundprenumerationer med hjälp av följande inbyggda principdefinitioner:

- Otillåtna resurstyper
- Tillåtna resurstyper

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här nekar du en viss resurstyp med Azure Policy:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="610-implement-approved-application-list"></a>6.10: Genomföra godkänd ansökningslista

**Vägledning:** För Azure Batch Pool-noder implementerar du en tredjepartslösning för att förhindra att obehöriga filtyper körs.


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>Begränsa användarnas möjlighet att interagera med Azure Resource Manager via skript</div>

**Vägledning**: Använd Azure Villkorlig åtkomst för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "Blockera åtkomst" för "Microsoft Azure Management"-appen.

Konfigurera villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Begränsa användarnas förmåga att köra skript inom beräkningsresurser

**Vägledning**: Ej tillämpligt.

Detta gäller inte för Azure Batch, eftersom användare (icke-administratörer) i Azure Batch-poolerna inte behöver åtkomst till de enskilda noderna för att köra jobb. Klusteradministratören har redan root-åtkomst till alla noder.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fysiskt eller logiskt segregera högriskapplikationer

**Vägledning**: Benchmark är inte tillämpligt, Benchmark är avsett för webbprogram som körs på Azure App Service- eller IaaS-instanser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Säkerhetskontroll: Säker konfiguration](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Upprätta säkra konfigurationer för alla Azure-resurser

**Vägd:** Använd Azure Policy-alias i namnområdet "Microsoft.Batch" för att skapa anpassade principer för granskning eller framtvingning av konfigurationen av dina Azure Batch-konton och -pooler.

Så här visar du tillgängliga Azure-principalias:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Konfigurera och hantera Azure-princip:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2: Upprätta säkra konfigurationer för ditt operativsystem

**Vägledning**: Upprätta säkra konfigurationer för batchpoolnodernas operativsystem.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3: Underhåll säkra konfigurationer för alla Azure-resurser

**Vägledning**: Använd Azure-principen [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar för Azure-resurser som är relaterade till ditt batch-konto och pooler (till exempel virtuella nätverk, undernät, Azure-brandväggar, Azure Storage-konton osv.). Du kan använda Azure Policy-alias från följande namnområden för att skapa anpassade principer:

- Microsoft.Batch

- Microsoft.Storage

- Microsoft.Network

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Förstå Azure-principeffekter:https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4: Underhåll säkra konfigurationer för operativsystem

**Vägledning**: Azure Batch Pool Operativsystem avbildningar hanteras och underhålls av Microsoft. Du är ansvarig för att implementera tillståndskonfiguration på OS-nivå.

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Delat

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Lagringskonfiguration på ett säkert sätt av Azure-resurser

**Vägd:** Om du använder anpassade Azure-principdefinitioner för dina Azure Batch-konton, pooler eller relaterade resurser använder du Azure Repos för att lagra och hantera din kod på ett säkert sätt.

Så här lagrar du kod i Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Dokumentation för Azure Repos:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Lagra anpassade operativsystemavbildningar på ett säkert sätt

**Vägledning:** Om du använder anpassade avbildningar för dina Azure Batch-pooler använder du ROLLBASERAD ÅTKOMSTKONTROLL (RBAC) för att säkerställa att endast behöriga användare kan komma åt avbildningarna.

Förstå RBAC i Azure:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

Konfigurera RBAC i Azure:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Distribuera verktyg för hantering av systemkonfiguration

**Vägledning**: Använd inbyggda Azure-principdefinitioner för att avisera, granska och framtvinga Azure Batch-relaterade resurskonfigurationer.  Använd Azure Policy-alias i namnområdet "Microsoft.Batch" för att skapa anpassade principer för dina Azure Batch-konton och -pooler. Dessutom, utveckla en process och pipeline för att hantera principundantag.

Konfigurera och hantera Azure-princip:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Distribuera verktyg för hantering av systemkonfigurationer för operativsystem

**Vägledning**: Implementera en tredjepartslösning för att upprätthålla önskat tillstånd för azure batch-poolnodernas operativsystem.


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementera automatisk konfigurationsövervakning för Azure Services

**Vägd:** Använd Azure Policy-alias i namnområdet "Microsoft.Batch" för att skapa anpassade principer för granskning eller framtvingning av konfigurationen av din Azure Batch-instans. Du kan också använda alla inbyggda principer som skapats specifikt för Azure Batch eller de resurser som används av Azure Batch, till exempel:

- Undernät ska associeras med en nätverkssäkerhetsgrupp

-Lagringskonton bör använda en slutpunkt för virtuella nätverkstjänster

- Diagnostikloggar i batchkonton ska aktiveras

Så här visar du tillgängliga Azure-principalias:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementera automatisk konfigurationsövervakning för operativsystem

**Vägledning**: Implementera en tredjepartslösning för att övervaka tillståndet för azure batch-poolnodernas operativsystem.


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="711-securely-manage-azure-secrets"></a>7.11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning:** Azure Key Vault kan användas med Azure Batch-distributioner för att hantera nycklar för poollagring i Azure Storage Accounts.

Så här integrerar du med Azure-hanterade identiteter:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Så här skapar du ett Azure Key Vault:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

Så här tillhandahåller du Key Vault-autentisering med en hanterad identitet:

https://docs.microsoft.com/azure/key-vault/managed-identity

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="712-securely-and-automatically-manage-identities"></a>7.12: Hantera identiteter på ett säkert och automatiskt sätt

**Vägledning:** Inte tillgänglig, hanterad tjänstidentitet stöds inte av Azure Batch


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: Implementera autentiseringsskanner för att identifiera autentiseringsuppgifter i koden. Autentiseringsläsare kommer också att uppmuntra flytta identifierade autentiseringsuppgifter till säkrare platser som Azure Key Vault. 

Så här konfigurerar du autentiseringsskanner:https://secdevtools.azurewebsites.net/helpcredscan.html

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Använd centralt hanterad programvara mot skadlig kod

**Vägledning**: Använd Windows Defender på dina enskilda Azure Batch-poolnoder när det gäller Windows-operativsystem, eller ange din egen anti-malware-lösning om du använder Linux.


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Förskansa filer som ska överföras till Azure-resurser som inte är beräkning

**Vägledning**: Microsoft Antimalware är aktiverat på den underliggande värden som stöder Azure-tjänster (till exempel Azure Batch), men det körs inte på kundinnehåll.

Förskansa alla filer som överförs till azure-resurser som inte är beräkning, till exempel App Service, DataSjölagring, Blob Storage osv. Microsoft kan inte komma åt kunddata i dessa fall.

Förstå Microsoft Antimalware för Azure Cloud Services och virtuella datorer:

https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Delat

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Se till att programvara och signaturer mot skadlig kod uppdateras

**Vägledning**: Använd Windows Defender på dina enskilda Azure Batch-poolnoder när det gäller Windows-operativsystem och se till att automatisk uppdatering är aktiverad. Ge din egen anti-malware lösning om du använder Linux.


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Säkerhetskontroll: Dataåterställning](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Säkerställa regelbundna automatiska säkerhetskopieringar

**Vägledning:** När du använder ett Azure Storage-konto för Azure Batch Pool-datalagring väljer du lämpligt redundansalternativ (LRS, ZRS, GRS, RA-GRS). 

Konfigurera lagringsredundans för Azure Storage-konton:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Utföra fullständiga systemsäkerhetskopior och säkerhetskopiera alla hanterade kundnycklar

**Vägledning:** När du använder ett Azure Storage-konto för Azure Batch Pool-datalagring väljer du lämpligt redundansalternativ (LRS, ZRS, GRS, RA-GRS).  Om du använder Azure Key Vault för någon del av din Azure Batch-distribution, se till att dina nycklar säkerhetskopieras.

Konfigurera lagringsredundans för Azure Storage-konton:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Så här säkerhetskopierar du nyckelnycklar i Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validera alla säkerhetskopior inklusive kundhanterade nycklar

**Vägledning:** Om du hanterar dina egna nycklar för Azure Storage-konton eller någon annan resurs som är relaterad till din Azure Batch-implementering testar du regelbundet återställning av säkerhetskopierade nycklar.

Så här säkerhetskopierar du nyckelnycklar i Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

Så här återställer du en kundhanterad nyckel med PowerShell:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Säkerställa skydd för säkerhetskopior och kundhanterade nycklar

**Vägledning:** Om Azure Key Vault används för att lagra nycklar som är relaterade till Azure Batch Pool Storage Accounts aktiverar du Soft-Delete i Azure Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning.

Aktivera mjuk borttagning i Azure Key Vault:

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Säkerhetskontroll: Incident response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-incident-response-guide"></a>10.1: Skapa hanteringsguide för incidenthantering

**Vägledning**: Se till att det finns skriftliga incidenthanteringsplaner som definierar personalens roller samt faser av incidenthantering/hantering.

Konfigurera arbetsflödesautomatiseringar i Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2: Skapa bedömnings- och prioriteringsprocedur för incidenten

**Vägledning**: Security Center tilldelar aviseringar en allvarlighetsgrad för att hjälpa dig att prioritera den ordning i vilken du deltar i varje avisering, så att när en resurs äventyras kan du komma åt den direkt. Allvarlighetsgraden baseras på hur säker Security Center är i att hitta eller analytiska används för att utfärda aviseringen samt den konfidensnivå som det fanns ont uppsåt bakom aktiviteten som ledde till aviseringen.

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="103-test-security-response-procedures"></a>10.3: Testsäkerhetssvarsprocedurer

**Vägledning**: Genomföra övningar för att testa systemens funktioner för incidenthantering på regelbunden kadens. Identifiera svaga punkter och luckor och revidera planen efter behov.

Se NIST:s publikation: Guide to Test, Training och Exercise Programs for IT Plans and Capabilities:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10.4: Ange kontaktinformation om säkerhetsincidenter och konfigurera varningsmeddelanden &nbsp;för säkerhetsincidenter

**Vägledning**: Kontaktinformation för säkerhetsincidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en olaglig eller obehörig part.

Så här ställer du in säkerhetskontakten för Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Införliva säkerhetsvarningar i ditt incidenthanteringssystem

**Vägledning**: Exportera dina Azure Security Center-aviseringar och rekommendationer med hjälp av funktionen Kontinuerlig export. Kontinuerlig export gör att du kan exportera aviseringar och rekommendationer antingen manuellt eller på ett kontinuerligt sätt. Du kan använda Azure Security Center-dataanslutningen för att strömma aviseringarna till Azure Sentinel.

Konfigurera kontinuerlig export:

https://docs.microsoft.com/azure/security-center/continuous-export

Så här streamar du aviseringar till Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisera svaret på säkerhetsvarningar

**Vägledning**: Använd funktionen Automatisering av arbetsflöde i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" på säkerhetsaviseringar och rekommendationer.

Konfigurerar automatiserings- och logikappar för arbetsflöde:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [Säkerhetskontroll: Penetrationstester och röda teamövningar](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1: Utför regelbundna penetrationstester av dina Azure-resurser och se till att åtgärda alla viktiga säkerhetsresultat inom 60 dagar.

**Vägledning**: Följ Microsofts insatsregler för att säkerställa att dina penetrationstester inte bryter mot Microsofts policyer:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

Du hittar mer information om Microsofts strategi och genomförande av Red Teaming och penetrationstester på webbplatser på flera webbplatser mot Microsofts hanterade molninfrastruktur, tjänster och program, här: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Delat

## <a name="next-steps"></a>Nästa steg

- Se [Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Läs mer om [Azure Security-originalplaner](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
