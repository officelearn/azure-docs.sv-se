---
title: Azure Security Baseline för HDInsight
description: Azure Security Baseline för HDInsight
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: eddfcacd01a67fffa8e3e992e021ed1771d25944
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471344"
---
# <a name="azure-security-baseline-for-hdinsight"></a>Azure Security Baseline för HDInsight

Azure Security Baseline för HDInsight innehåller rekommendationer som hjälper dig att förbättra säkerhetspositionen för distributionen.

Baslinjen för de här tjänsterna hämtas från [Azure Security Benchmark version 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), som ger rekommendationer om hur du kan skydda dina molnlösningar på Azure med vår vägledning om bästa praxis.

Mer information finns i [översikt över Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Säkerhetskontroll: Nätverkssäkerhet](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Skydda resurser med nätverkssäkerhetsgrupper eller Azure-brandväggen i det virtuella nätverket

**Vägledning**: Perimetersäkerhet i Azure HDInsight uppnås via virtuella nätverk. En företagsadministratör kan skapa ett kluster i ett virtuellt nätverk och använda en nätverkssäkerhetsgrupp (NSG) för att begränsa åtkomsten till det virtuella nätverket. Endast tillåtna IP-adresser i de inkommande reglerna för nätverkssäkerhetsgruppen kan kommunicera med Azure HDInsight-klustret. Den här konfigurationen ger perimetersäkerhet. Alla kluster som distribueras i ett virtuellt nätverk har också en privat slutpunkt som matchar till en privat IP-adress i det virtuella nätverket för privat HTTP-åtkomst till klustergateways.


Distribuera Azure HDInsight i ett virtuellt nätverk och skydda med en nätverkssäkerhetsgrupp:

https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Övervaka och logga konfiguration och trafik av virtuella nätverk, undernät och nätverkskort

**Vägledning**: Använd Azure Security Center och åtgärda nätverksskyddsrekommendationer för det virtuella nätverket, undernätet och nätverkssäkerhetsgruppen som används för att skydda ditt Azure HDInsight-kluster. Aktivera NSG-flödesloggar (Network Security Group) och skicka loggar till ett Azure Storage-konto till trafikgranskning. Du kan också skicka NSG-flödesloggar till en Azure Log Analytics Workspace och använda Azure Traffic Analytics för att ge insikter om trafikflödet i ditt Azure-moln. Vissa fördelar med Azure Traffic Analytics är möjligheten att visualisera nätverksaktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödesmönster och identifiera felkonfigurationer för nätverk.


Aktivera NSG-flödesloggar:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


Aktivera och använda Azure Traffic Analytics:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics


Förstå nätverkssäkerhet som tillhandahålls av Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="13-protect-critical-web-applications"></a>1.3: Skydda kritiska webbprogram

**Vägledning**: Ej tillämpligt. benchmark är avsett för Azure Apps Service eller beräkningsresurser som är värd för webbprogram.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Neka kommunikation med kända skadliga IP-adresser

**Vägledning**: För skydd mot DDoS-attacker aktiverar du Azure DDoS Standard-skydd i det virtuella nätverket där din Azure HDInsight distribueras. Använd Azure Security Center-integrerad hotinformation för att neka kommunikation med kända skadliga eller oanvända IP-adresser på Internet.


KonfigureraR du DDoS-skydd:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection


Förstå Azure Security Center Integrated Threat Intelligence:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Spela in nätverkspaket och flödesloggar

**Vägledning**: Aktivera NSG-flödesloggar (Network Security Group) för NSG som är anslutna till undernätet som används för att skydda ditt Azure HDInsight-kluster. Registrera NSG-flödesloggarna i ett Azure Storage-konto för att generera flödesposter. Om det behövs för att undersöka avvikande aktivitet aktiverar du Azure Network Watcher-paketfångst.


Aktivera NSG-flödesloggar:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


Aktivera Network Watcher:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuera nätverksbaserade system för intrångsdetektering/intrångsförebyggande (IDS/IPS)

**Vägledning**: Krav kan uppfyllas Azure security control ID 1.1; Distribuera Azure HDInsight-kluster till ett virtuellt nätverk och skydda med en nätverkssäkerhetsgrupp (NSG).

Det finns flera beroenden för Azure HDInsight som kräver inkommande trafik. Den inkommande hanteringstrafiken kan inte skickas via en brandväggsenhet. Källadresserna för nödvändig hanteringstrafik är kända och publicerade. Skapa regler för nätverkssäkerhetsgruppen med den här informationen om du vill tillåta trafik från endast betrodda platser, vilket skyddar inkommande trafik till klustren.

Distribuera HDInsight i ett virtuellt nätverk och skydda med en nätverkssäkerhetsgrupp:https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

Förstå HDInsight-beroenden och brandväggsanvändning:https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

IP-adresser för HDInsight-hantering:https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Hantera trafik till webbapplikationer

**Vägledning**: Ej tillämpligt. benchmark är avsett för Azure Apps Service eller beräkningsresurser som är värd för webbprogram.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimera komplexiteten och de administrativa omkostnaderna för nätverkssäkerhetsreglerna

**Vägledning**: Använd taggar för virtuella nätverkstjänst för att definiera nätverksåtkomstkontroller för nätverkssäkerhetsgrupper (NSG) som är kopplade till undernätet som ditt Azure HDInsight-kluster distribueras i. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange tjänsttagnamnet (t.ex. ApiManagement) i lämpligt käll- eller målfält för en regel kan du tillåta eller neka trafik för motsvarande tjänst. Microsoft hanterar adressprefixen som omfattas av servicetag och uppdaterar automatiskt servicetag när adresserna ändras.


Förstå och använda tjänsttaggar för Azure HDInsight:

https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Underhåll standardsäkerhetskonfigurationer för nätverksenheter

**Vägledning**: Definiera och implementera standardsäkerhetskonfigurationer för nätverksresurser som är relaterade till azure HDInsight-klustret. Använd Azure Policy-alias i namnområdena "Microsoft.HDInsight" och "Microsoft.Network" för att skapa anpassade principer för granskning eller framtvingning av nätverkskonfigurationen för ditt Azure HDInsight-kluster.


Du kan också använda Azure Blueprints för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljöartefakter, till exempel Azure Resource Manager-mallar, RBAC-kontroller och principer, i en enda skissdefinition. Använd enkelt skissen på nya prenumerationer och miljöer och finjustera kontroll och hantering genom versionshantering.


Så här visar du tillgängliga Azure-principalias:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0


Konfigurera och hantera Azure-princip:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


Så här skapar du en Azure Blueprint:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regler för konfiguration av dokumenttrafik

**Vägledning**: Använd taggar för nätverkssäkerhetsgrupp (NSG) och andra resurser som är relaterade till nätverkssäkerhet och trafikflöde som är associerade med ditt Azure HDInsight-kluster. För enskilda NSG-regler använder du fältet "Beskrivning" för att ange affärsbehov och/eller varaktighet (etc.) för alla regler som tillåter trafik till/från ett nätverk.


Använd någon av de inbyggda Azure-principdefinitionerna som är relaterade till taggning, till exempel "Kräv tagg och dess värde" för att säkerställa att alla resurser skapas med taggar och för att meddela dig om befintliga otaggade resurser.


Du kan använda AZURE PowerShell eller Azure command-line interface (CLI) för att söka efter eller utföra åtgärder på resurser baserat på deras taggar.


Så här skapar och använder du Taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


Så här skapar du ett virtuellt nätverk:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal


Hur man skapar en NSG med en Security Config:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Använd automatiserade verktyg för att övervaka nätverksresurskonfigurationer och identifiera ändringar

**Vägledning**: Använd Azure Activity Log för att övervaka nätverksresurskonfigurationer och identifiera ändringar för nätverksresurser relaterade till dina Azure HDInsight-distributioner. Skapa aviseringar i Azure Monitor som utlöses när ändringar av kritiska nätverksresurser sker.


Så här visar och hämtar du Azure Activity Log-händelser:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view


Så här skapar du aviseringar i Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [Säkerhetskontroll: Loggning och övervakning](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Använd godkända tidssynkroniseringskällor

**Vägledning:** Microsoft underhåller tidskällor för Azure HDInsight-klusterkomponenter, du kan uppdatera tidssynkronisering för dina beräkningsdistributioner.


Konfigurera tidssynkronisering för Azure-beräkningsresurser:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurera hantering av centrala säkerhetsloggar

**Vägledning:** Du kan gå in på ditt Azure HDInsight-kluster till Azure Monitor för att aggregera säkerhetsdata som genereras av klustret. Utnyttja anpassade frågor för att identifiera och svara på hot i miljön. 


Så här går du till ett Azure HDInsight-kluster till Azure Monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial


Så här skapar du anpassade frågor för ett Azure HDInsight-kluster:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivera granskningsloggning för Azure-resurser

**Vägledning**: Aktivera Azure Monitor för HDInsight-klustret, dirigera den till en Log Analytics-arbetsyta. Detta loggar relevant klusterinformation och OS-mått för alla Azure HDInsight-klusternoder.


Aktivera loggning för HDInsight-kluster:

 https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial


Så här frågar du HDInsight-loggar:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Samla in säkerhetsloggar från operativsystem

**Vägledning:** Inbyggd Azure HDInsight-kluster till Azure Monitor. Kontrollera att arbetsytan Log Analytics som används har logglagringsperioden inställd enligt organisationens efterlevnadsregler.


Så här går du till ett Azure HDInsight-kluster till Azure Monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial


Konfigurerar lagringsperiod för logganalysarbetsyta:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurera lagring av säkerhetslogglagring

**Vägledning:** Inbyggd Azure HDInsight-kluster till Azure Monitor. Kontrollera att arbetsytan Azure Log Analytics som används har logglagringsperioden inställd enligt organisationens efterlevnadsregler.


Så här går du till ett Azure HDInsight-kluster till Azure Monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial


Konfigurerar lagringsperiod för logganalysarbetsyta:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2.6: Övervaka och granska loggar

**Vägledning**: Använda Azure Log Analytics arbetsytefrågor för att fråga Azure HDInsight-loggar:


Skapa anpassade frågor för Azure HDInsight-kluster:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Aktivera aviseringar för avvikande aktivitet

**Vägledning**: Använd Azure Log Analytics-arbetsytan för övervakning och avisering om avvikande aktiviteter i säkerhetsloggar och händelser relaterade till ditt Azure HDInsight-kluster.


Hantera aviseringar i Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts


Så här varnar du för logganalysloggdata:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralisera loggning av skadlig kod

**Vägledning:** Azure HDInsight levereras med Clamscan förinstallerad och aktiverad för klusternodavbildningar, men du måste hantera programvaran och manuellt samla in/övervaka alla loggar Clamscan producerar.


Förstå Clamscan:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivera DNS-frågeloggning

**Vägledning**: Implementera tredjepartslösning för dns-loggning.

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivera granskningsloggning på kommandoraden

**Vägledning**: Konfigurera konsolloggning manuellt per nod.

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Säkerhetskontroll: Identitets- och åtkomstkontroll](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Föra en inventering av administrativa räkenskaper

**Vägledning**: Underhåll posten för det lokala administrativa konto som skapas under klusteretablering av Azure HDInsight-kluster samt alla andra konton som du skapar. Om Azure AD-integrering används har Azure AD dessutom inbyggda roller som uttryckligen måste tilldelas och därför kan ifrågasättas. Använd Azure AD PowerShell-modulen för att utföra adhoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.


Dessutom kan du använda Azure Security Center Identity and Access Management rekommendationer.


Så här skaffar du en katalogroll i Azure AD med PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0


Så här hämtar du medlemmar i en katalogroll i Azure AD med PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0


Så här övervakar du identitet och åtkomst med Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändra standardlösenord där så är tillämpligt

**Vägd:** När du etablerar ett kluster kräver Azure att du skapar nya lösenord för webbportalen och SSH-åtkomsten (Secure Shell). Det finns inga standardlösenord att ändra, men du kan ange olika lösenord för SSH- och webbportalåtkomst.


Ange lösenord när du etablerar ett Azure HDInsight-kluster:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Använd särskilda administrativa konton

**Vägledning**: Integrera autentisering för Azure HDInsight-kluster med Azure Active Directory. Skapa principer och procedurer kring användningen av dedikerade administrativa konton.


Dessutom kan du använda Azure Security Center Identity and Access Management rekommendationer.


Så här integrerar du Azure HDInsight-autentisering med Azure Active Directory:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds


Så här övervakar du identitet och åtkomst med Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Använd enkel inloggning (SSO) med Azure Active Directory

**Vägledning**: Använd Azure HDInsight ID Broker för att logga in på ESP-kluster (Enterprise Security Package) med hjälp av Multifaktorautentisering, utan att ange några lösenord. Om du redan har loggat in på andra Azure-tjänster, till exempel Azure-portalen, kan du logga in på ditt Azure HDInsight-kluster med en enkel inloggningsupplevelse (SSO).


Aktivera Azure HDInsight ID Broker:

https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker#enable-hdinsight-id-broker

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Använda multifaktorautentisering för all Azure Active Directory-baserad åtkomst

**Vägledning**: Aktivera Azure AD MFA och följ Azure Security Center Identity and Access Management-rekommendationerna. Azure HDInsight-kluster med Enterprise Security Package som konfigurerats kan anslutas till en domän så att domänanvändare kan använda sina domänautentiseringsuppgifter för att autentisera med klustren och köra stordatajobb. När du autentiserar med MFA (Multi Factor Authentication) aktiverat kommer användarna att utmanas att tillhandahålla en andra autentiseringsfaktor.


Aktivera MFA i Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


Så här övervakar du identitet och åtkomst i Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Använd dedikerade datorer (arbetsstationer för privilegierad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd PAWs (privilegierade åtkomstarbetsstationer) med MFA (Multifaktor authentication) konfigurerade för att logga in på och konfigurera Dina Azure HDInsight-kluster och relaterade resurser.


Läs mer om arbetsstationer för privilegierad åtkomst:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations


Aktivera MFA i Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Logga och varna för misstänkt aktivitet från administrativa konton

**Vägledning:** Azure HDInsight-kluster med Enterprise Security Package som konfigurerats kan anslutas till en domän så att domänanvändare kan använda sina domänautentiseringsuppgifter för att autentisera. Du kan använda Säkerhetsrapporter för Azure Active Directory (AAD) för generering av loggar och aviseringar när misstänkt eller osäker aktivitet inträffar i AAD-miljön. Använd Azure Security Center för att övervaka identitets- och åtkomstaktivitet.


Så här identifierar du AAD-användare som flaggats för riskfylld aktivitet:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk


Så här övervakar du användarnas identitets- och åtkomstaktivitet i Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Hantera Azure-resurser från endast godkända platser

**Vägledning:** Azure HDInsight-kluster med Enterprise Security Package som konfigurerats kan anslutas till en domän så att domänanvändare kan använda sina domänautentiseringsuppgifter för att autentisera. Använd namngivna platser för villkorlig åtkomst för att tillåta åtkomst från endast specifika logiska grupperingar av IP-adressintervall eller länder/regioner.


Konfigurera namngivna platser i Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="39-use-azure-active-directory"></a>3.9: Använd Azure Active Directory

**Vägledning**: Använd Azure Active Directory (AAD) som det centrala autentiserings- och auktoriseringssystemet. AAD skyddar data genom att använda stark kryptering för data i vila och under överföring. AAD saltar också, hashar och lagrar användaruppgifter på ett säkert sätt.

Azure HDInsight-kluster med ESP (Enterprise Security Package) konfigurerat kan anslutas till en domän så att domänanvändare kan använda sina domänautentiseringsuppgifter för att autentisera med klustren.


Så här skapar och konfigurerar du en AAD-instans:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


Konfigurera Enterprise Security Package med Azure Active Directory Domain Services i Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelbundet granska och förena användaråtkomst

**Vägledning**: Använd Azure Active Directory (AAD) autentisering med ditt Azure HDInsight-kluster. AAD tillhandahåller loggar som hjälper dig att upptäcka inaktuella konton. Dessutom kan du använda Azure Identity Access Reviews för att effektivt hantera gruppmedlemskap, åtkomst till företagsprogram och rolltilldelningar. Användarens åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst. 


Så här använder du Azure Identity Access Reviews:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Övervaka försök att komma åt inaktiverade konton

**Vägledning**: Använd Azure Active Directory (AAD) Inloggnings- och granskningsloggar för att övervaka för försök att komma åt inaktiverade konton. dessa loggar kan integreras i alla siem/övervakningsverktyg från tredje part.


Du kan effektivisera den här processen genom att skapa diagnostikinställningar för AAD-användarkonton, skicka granskningsloggar och inloggningsloggar till en Azure Log Analytics-arbetsyta. Konfigurera önskade aviseringar i Azure Log Analytics-arbetsytan.


Så här integrerar du Azure Activity Logs i Azure Monitor:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Varning på kontots avvikelse för inloggningsbeteende

**Vägledning:** Azure HDInsight-kluster med ESP (Enterprise Security Package) konfigurerat kan anslutas till en domän så att domänanvändare kan använda sina domänautentiseringsuppgifter för att autentisera med klustren.  Använd Azure Active Directory (AAD) Riskidentifieringar och identitetsskyddsfunktionen för att konfigurera automatiska svar på identifierade misstänkta åtgärder relaterade till användaridentiteter. Dessutom kan du matsätta data i Azure Sentinel för vidare undersökning.


Så här visar du riskfyllda AAD-inloggningar:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins


Konfigurera och aktivera riskprinciper för identitetsskydd:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ge Microsoft åtkomst till relevanta kunddata under supportscenarier

**Vägledning**: Ej tillgänglig. Customer Lockbox stöds ännu inte för Azure HDInsight.

Lista över tjänster som stöds av Customer Lockbox:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Säkerhetskontroll: Dataskydd](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Föra en inventering av känslig information

**Vägledning**: Använd taggar på resurser som är relaterade till dina Azure HDInsight-distributioner för att hjälpa till att spåra Azure-resurser som lagrar eller bearbetar känslig information.


Så här skapar och använder du taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolera system som lagrar eller bearbetar känslig information

**Vägledning**: Implementera separata prenumerationer och/eller hanteringsgrupper för utveckling, test och produktion. Azure HDInsight-kluster och alla associerade lagringskonton bör avgränsas med virtuellt nätverk/undernät, taggas på rätt sätt och skyddas inom en nätverkssäkerhetsgrupp (NSG) eller Azure-brandvägg. Klusterdata bör finnas i ett skyddat Azure Storage-konto eller Azure Data Lake Storage (Gen1 eller Gen2).


Välj lagringsalternativ för Ditt Azure HDInsight-kluster:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options


Så här skyddar du Azure Data Lake Storage:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview


Så här skyddar du Azure Storage-konton:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Övervaka och blockera obehörig överföring av känslig information

**Vägledning**: För Azure HDInsight-kluster som lagrar eller bearbetar känslig information markerar du klustret och relaterade resurser som känsliga med hjälp av taggar. Om du vill minska risken för dataförlust via exfiltration begränsar du utgående nätverkstrafik för Azure HDInsight-kluster med Azure-brandväggen.


För den underliggande plattformen som hanteras av Microsoft behandlar Microsoft allt kundinnehåll som känsligt och gör stora ansträngningar för att skydda mot förlust av kunddata och exponering. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och underhållit en uppsättning robusta dataskyddskontroller och funktioner.


Så här begränsar du utgående trafik för Azure HDInsight-kluster med Azure-brandväggen:

https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic


Förstå kunddataskyddet i Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Delat

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Kryptera all känslig information under transport

**Vägledning**: Kryptera all känslig information under transport. Kontrollera att alla klienter som ansluter till ditt Azure HDInsight-kluster- eller klusterdatalager (Azure Storage Accounts eller Azure Data Lake Storage Gen1/Gen2) kan förhandla fram TLS 1.2 eller senare. Microsoft Azure-resurser förhandlar som standard om TLS 1.2. 


Förstå Azure Data Lake Storage-kryptering under överföringen:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview


Förstå Azure Storage-kontokryptering under överföring:

https://docs.microsoft.com/azure/storage/blobs/security-recommendations

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Delat

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Använda ett aktivt identifieringsverktyg för att identifiera känsliga data

**Vägledning**: Funktioner för dataidentifiering, klassificering och förlustförebyggande är ännu inte tillgängliga för Azure Storage- eller beräkningsresurser. Implementera tredjepartslösning om det behövs för efterlevnadsändamål.



För den underliggande plattformen som hanteras av Microsoft behandlar Microsoft allt kundinnehåll som känsligt och gör stora ansträngningar för att skydda mot förlust av kunddata och exponering. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och underhållit en uppsättning robusta dataskyddskontroller och funktioner.



Förstå kunddataskyddet i Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Delat

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Använd Azure RBAC för att kontrollera åtkomsten till resurser

**Vägledning**: Med Azure HDInsight Enterprise Security Package (ESP) kan du använda Apache Ranger för att skapa och hantera detaljerade åtkomstkontroll- och datafördunklingsprinciper för data som lagras i filer, mappar, databaser, tabeller och rader/kolumner. Hadoop admin kan konfigurera rollbaserad åtkomstkontroll (RBAC) för att säkra Apache Hive, HBase, Kafka och Spark med hjälp av dessa plugins i Apache Ranger.

Genom att konfigurera RBAC-principer med Apache Ranger kan du associera behörigheter med en roll i organisationen. Detta lager av abstraktion gör det lättare att se till att människor bara har de behörigheter som behövs för att utföra sina arbetsuppgifter.

Konfigurationer av företagssäkerhetspaket med Azure Active Directory Domain Services i HDInsight:https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

Översikt över företagssäkerhet i Azure HDInsight:https://docs.microsoft.com/azure/hdinsight/domain-joined/hdinsight-security-overview



**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Använd värdbaserad dataförlustprevention för att genomdriva åtkomstkontroll

**Vägledning**: För Azure HDInsight-kluster som lagrar eller bearbetar känslig information markerar du klustret och relaterade resurser som känsliga med hjälp av taggar. Dataidentifiering, klassificering och förlustförebyggande funktioner är ännu inte tillgängliga för Azure Storage eller beräkningsresurser. Implementera tredjepartslösning om det behövs för efterlevnadsändamål.


För den underliggande plattformen som hanteras av Microsoft behandlar Microsoft allt kundinnehåll som känsligt och gör stora ansträngningar för att skydda mot förlust av kunddata och exponering. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och underhållit en uppsättning robusta dataskyddskontroller och funktioner.


Förstå kunddataskyddet i Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Delat

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Kryptera känslig information i vila

**Vägledning:** Om du använder Azure SQL Database för att lagra Apache Hive- och Apache Oozie-metadata, se till att SQL-data förblir krypterade hela tiden. För Azure Storage-konton och DataSjölagring (Gen1 eller Gen2) rekommenderas att Microsoft kan hantera dina krypteringsnycklar, men du har möjlighet att hantera dina egna nycklar.



Hantera krypteringsnycklar för Azure Storage-konton:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal



Skapa Azure Data Lake Storage med kundhanterade krypteringsnycklar:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal



Förstå kryptering för Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview#data-encryption



Konfigurera transparent datakryptering för SQL Database med hjälp av kundhanterade nycklar:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Delat

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logga och avisera om ändringar i kritiska Azure-resurser

**Vägledning**: Konfigurera diagnostikinställningar för Azure Storage-konton som är associerade med Azure HDInsight-kluster för att övervaka och logga alla CRUD-åtgärder mot klusterdata. Aktivera granskning för alla lagringskonton eller datasjölager som är associerade med Azure HDInsight-klustret.


Aktivera ytterligare loggning/granskning för ett Azure Storage-konto:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account


Aktivera ytterligare loggning/granskning för Azure Data Lake Storage:

https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Säkerhetskontroll: Sårbarhetshantering](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Kör automatiserade sårbarhetsskanningsverktyg

**Vägledning**: Implementera en lösning för sårbarhetshantering från tredje part.


Om du har en Rapid7-, Qualys- eller någon annan prenumeration på säkerhetsproblemhanteringsplattformen kan du använda skriptåtgärder för att installera sårbarhetsbedömningsagenter på dina Azure HDInsight-klusternoder och hantera noderna via respektive portal.


Så här installerar du Rapid7 Agent manuellt:

https://insightvm.help.rapid7.com/docs/azure-security-center


Så här installerar du Qualys Agent manuellt:

https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf


Så här använder du skriptåtgärder:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Distribuera en lösning för hantering av automatisk operativsystemets korrigering

**Vägledning:** Automatiska systemuppdateringar har aktiverats för klusternodavbildningar, men du måste regelbundet starta om klusternoder för att säkerställa att uppdateringar tillämpas.


Microsoft för att underhålla och uppdatera bas Azure HDInsight nod avbildningar.


Konfigurerar korrigeringsschemat för OPERATIVSYSTEM för HDInsight-kluster:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Delat

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Distribuera automatisk lösning för hantering av programkorrigering från tredje part

**Vägledning**: Använd skriptåtgärder eller andra mekanismer för att korrigera dina Azure HDInsight-kluster. Nyskapade kluster kommer alltid att ha de senaste tillgängliga uppdateringarna, inklusive de senaste säkerhetskorrigeringarna.


Konfigurera operativsystemets korrigeringsschema för Linux-baserade Azure HDInsight-kluster:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching


Så här använder du skriptåtgärder:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Jämför sårbarhetssökningar från rygg mot rygg

**Vägledning**: Implementera en lösning för sårbarhetshantering från tredje part som kan jämföra sårbarhetssökningar över tid. Om du har en Rapid7- eller Qualys-prenumeration kan du använda leverantörens portal för att visa och jämföra sårbarhetssökningar från rygg mot rygg.

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Använd en riskklassificeringsprocess för att prioritera reparation av upptäckta sårbarheter

**Vägledning**: Använd ett vanligt riskbedömningsprogram (t.ex. gemensamt system för bedömning av sårbarhet) eller standardriskklassificeringarna som tillhandahålls av ditt skanningsverktyg från tredje part.

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Använd Identifiering av Azure Asset

**Vägledning**: Använd Azure Resource Graph för att fråga/identifiera alla resurser (till exempel beräkning, lagring, nätverk, portar och protokoll osv.), inklusive Azure HDInsight-kluster, inom dina prenumerationer.  Se till att du har lämpliga (läs) behörigheter i din klientorganisation och kan räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.


Även om klassiska Azure-resurser kan identifieras via Azure Resource Graph, rekommenderas det starkt att skapa och använda Azure Resource Manager-resurser framöver.


Så här skapar du frågor med Azure Resource Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


Så här visar du dina Azure-prenumerationer:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0


Förstå Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

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


Så här skapar och använder du taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Underhåll en inventering av godkända Azure-resurser och programvarutitlar

**Vägledning**: Definiera lista över godkända Azure-resurser och godkänd programvara för dina beräkningsresurser

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Övervaka för icke godkända Azure-resurser

**Vägledning**: Använd Azure-principen för att begränsa vilken typ av resurser som kan skapas i kundprenumerationer med hjälp av följande inbyggda principdefinitioner:

- Otillåtna resurstyper
- Tillåtna resurstyper

Använd Azure Resource Graph för att fråga/identifiera resurser i dina prenumerationer. Se till att alla Azure-resurser som finns i miljön är godkända.

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här skapar du frågor med Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Övervaka icke godkända program i beräkningsresurser

**Vägledning**: Implementera en tredjepartslösning för att övervaka klusternoder för icke godkända program.

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Ta bort icke godkända Azure-resurser och program

**Vägledning**: Använd Azure Resource Graph för att fråga/identifiera alla resurser (till exempel beräkning, lagring, nätverk, portar och protokoll osv.), inklusive Azure HDInsight-kluster, inom dina prenumerationer.  Ta bort alla icke godkända Azure-resurser som du upptäcker. För Azure HDInsight-klusternoder implementerar du en tredjepartslösning för att ta bort eller avisera på icke godkänd programvara.


Så här skapar du frågor med Azure Resource Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="68-use-only-approved-applications"></a>6.8: Använd endast godkända ansökningar

**Vägledning**: För Azure HDInsight-klusternoder implementerar du en tredjepartslösning för att förhindra att obehörig programvara körs.

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="69-use-only-approved-azure-services"></a>6.9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure-principen för att begränsa vilken typ av resurser som kan skapas i kundprenumerationer med hjälp av följande inbyggda principdefinitioner:

- Otillåtna resurstyper
- Tillåtna resurstyper


Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


Så här nekar du en viss resurstyp med Azure Policy:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="610-implement-approved-application-list"></a>6.10: Genomföra godkänd ansökningslista

**Vägledning**: För Azure HDInsight-klusternoder implementerar du en tredjepartslösning för att förhindra att obehöriga filtyper körs.

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Begränsa användarnas möjlighet att interagera med Azure Resources Manager via skript

**Vägledning**: Använd Azure Villkorlig åtkomst för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "Blockera åtkomst" för "Microsoft Azure Management"-appen.


Konfigurera villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Begränsa användarnas möjlighet att köra skript i beräkningsresurser

**Vägledning**: Ej tillämpligt. Detta gäller inte för Azure HDInsight eftersom användare (icke-administratörer) i klustret inte behöver åtkomst till de enskilda noderna för att köra jobb. Klusteradministratören har root-åtkomst till alla klusternoder.

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Ej tillämpligt

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fysiskt eller logiskt segregera högriskapplikationer

**Vägledning**: Ej tillämpligt. benchmark är avsett för Azure Apps Service eller beräkningsresurser som är värd för webbprogram.

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Ej tillämpligt

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Säkerhetskontroll: Säker konfiguration](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: Använd Azure Policy-alias i namnområdet "Microsoft.HDInsight" för att skapa anpassade principer för granskning eller framtvingning av nätverkskonfigurationen för ditt HDInsight-kluster.


Så här visar du tillgängliga Azure-principalias:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0


Konfigurera och hantera Azure-princip:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Upprätta konfigurationer för säkra operativsystem

**Vägledning**: Azure HDInsight Operativsystem avbildningar hanteras och underhålls av Microsoft. Kunden som ansvarar för att implementera säkra konfigurationer för klusternodernas operativsystem. 

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Underhåll säkra Azure-resurskonfigurationer

**Vägledning**: Använd Azure-principen [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar för dina Azure HDInsight-kluster och relaterade resurser.


Konfigurera och hantera Azure-princip:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


Förstå Azure-principeffekter:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Underhåll konfigurationer av säkra operativsystem

**Vägledning**: Azure HDInsight Operativsystem avbildningar hanteras och underhålls av Microsoft. Kunden som ansvarar för att implementera tillståndskonfiguration på OS-nivå.

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Delat

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Säkert lagra konfiguration av Azure-resurser

**Vägledning:** Om du använder anpassade Azure-principdefinitioner använder du Azure DevOps eller Azure Repos för att lagra och hantera din kod på ett säkert sätt.



Så här lagrar du kod i Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops



Dokumentation för Azure Repos:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Lagra anpassade operativsystemavbildningar på ett säkert sätt

**Vägledning**: Ej tillämpligt. anpassade avbildningar som inte är tillämpliga på Azure HDInsight.

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Ej tillämpligt

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Distribuera verktyg för hantering av systemkonfigurationer

**Vägd:** Använd Azure Policy-alias i namnområdet "Microsoft.HDInsight" för att skapa anpassade principer för aviseringar, granskning och genomdämning av systemkonfigurationer. Dessutom, utveckla en process och pipeline för att hantera principundantag.



Konfigurera och hantera Azure-princip:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Distribuera verktyg för systemkonfigurationshantering för operativsystem

**Vägledning**: Implementera en tredjepartslösning för att upprätthålla önskat tillstånd för klusternodens operativsystem.

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementera automatisk konfigurationsövervakning för Azure-tjänster

**Vägledning**: Använd Azure Policy-alias i namnområdet "Microsoft.HDInsight" för att skapa anpassade principer för granskning eller framtvingning av konfigurationen av ditt HDInsight-kluster.


Så här visar du tillgängliga Azure-principalias:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0


Konfigurera och hantera Azure-princip:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementera automatisk konfigurationsövervakning för operativsystem

**Vägledning**: Implementera en tredjepartslösning för att övervaka tillståndet för klusternodens operativsystem.

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="711-manage-azure-secrets-securely"></a>7.11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning:** Azure HDInsight innehåller BYOK-stöd (Bring Your Own Key) för Apache Kafka. Med den här funktionen kan du äga och hantera de nycklar som används för att kryptera data i vila.


Alla hanterade diskar i Azure HDInsight skyddas med Azure Storage Service Encryption (SSE). Som standard krypteras data på dessa diskar med Microsoft-hanterade nycklar. Om du aktiverar BYOK tillhandahåller du krypteringsnyckeln för Azure HDInsight för att använda och hantera den med Azure Key Vault.


Key Vault kan också användas med Azure HDInsight-distributioner för att hantera nycklar för klusterlagring (Azure Storage-konton och Azure Data Lake Storage)


Så här tar du med din egen nyckel till Apache Kafka på Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok


Hantera krypteringsnycklar för Azure Storage-konton:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Hantera identiteter på ett säkert och automatiskt sätt

**Vägledning**: Hanterade identiteter kan användas i Azure HDInsight så att dina kluster kan komma åt Azure Active Directory-domäntjänster, komma åt Azure Key Vault eller komma åt filer i Azure Data Lake Storage Gen2.


Förstå hanterade identiteter med Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-managed-identities

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning:** Om du använder någon kod som är relaterad till din Azure HDInsight-distribution kan du implementera autentiseringsläsare för att identifiera autentiseringsuppgifter i kod. Autentiseringsläsare kommer också att uppmuntra flytta identifierade autentiseringsuppgifter till säkrare platser som Azure Key Vault. 


Så här konfigurerar du autentiseringsskanner:

https://secdevtools.azurewebsites.net/helpcredscan.html

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Använd centralt hanterad programvara mot skadlig kod

**Vägledning:** Azure HDInsight levereras med Clamscan förinstallerad och aktiverad för klusternodavbildningar, men du måste hantera programvaran och manuellt samla in/övervaka alla loggar Clamscan producerar.


Förstå Clamscan för Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Förskansa filer som ska överföras till Azure-resurser som inte är beräkning

**Vägledning**: Microsoft Antimalware är aktiverat på den underliggande värden som stöder Azure-tjänster, men det körs inte på kundinnehåll.


Förskansa alla filer som överförs till alla Azure-resurser som är relaterade till din Azure HDInsight-klusterdistribution, till exempel DataSjölagring, Blob Storage osv. Microsoft kan inte komma åt kunddata i dessa fall.


Förstå Microsoft Antimalware för Azure Cloud Services och virtuella datorer:

 https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Delat

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Se till att programvara och signaturer mot skadlig kod uppdateras

**Vägledning**: Azure HDInsight levereras med Clamscan förinstallerad och aktiverad för klusternodavbildningar. Clamscan kommer att utföra motor- och definitionsuppdateringar automatiskt, men aggregering och hantering av loggar måste utföras manuellt.


Förstå Clamscan för Azure Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Säkerhetskontroll: Dataåterställning](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Se till att regelbundna automatiska säkerhetskopieringar

**Vägledning**: När du använder ett Azure Storage-konto för HDInsight-klusterdatalagret väljer du lämpligt redundansalternativ (LRS, ZRS, GRS, RA-GRS).  När du använder en Azure SQL-databas för Azure HDInsight-klusterdatalagring konfigurerar du Active Geo-replication.


Konfigurera lagringsredundans för Azure Storage-konton:

https://docs.microsoft.com/azure/storage/common/storage-redundancy


Konfigurera redundans för Azure SQL-databaser:

https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Utför fullständiga systemsäkerhetskopior och säkerhetskopiera alla kundhanterade nycklar

**Vägledning:** När du använder ett Azure Storage-konto för Azure HDInsight-klusterdatalagret väljer du lämpligt redundansalternativ (LRS, ZRS, GRS, RA-GRS). Om du använder Azure Key Vault för någon del av din Azure HDInsight-distribution, se till att dina nycklar säkerhetskopieras.


Välj lagringsalternativ för Ditt Azure HDInsight-kluster:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options


Konfigurera lagringsredundans för Azure Storage-konton:

https://docs.microsoft.com/azure/storage/common/storage-redundancy


Så här säkerhetskopierar du Key Vault-nycklar i Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validera alla säkerhetskopior inklusive kundhanterade nycklar

**Vägledning**: Om Azure Key Vault används med din Azure HDInsight-distribution testar du återställning av säkerhetskopierade kundhanterade nycklar.


Så här tar du med din egen nyckel till Apache Kafka på Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok


Så här återställer du nyckelvalvsnycklar i Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Se till att säkerhetskopior och kundhanterade nycklar skyddas

**Vägledning:** Om Azure Key Vault används med din Azure HDInsight-distribution aktiverar du Mjukborttagning i Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning.


Aktivera Mjuk borttagning i Azure Key Vault:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Säkerhetskontroll: Incident response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Skapa en vägledning för incidenthantering

**Vägledning**: Se till att det finns skriftliga incidenthanteringsplaner som definierar personalens roller samt faser av incidenthantering/hantering.



Konfigurera arbetsflödesautomatiseringar i Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Skapa en incidentbedömning och prioriteringsprocedur

**Vägledning**: Security Center tilldelar aviseringar en allvarlighetsgrad för att hjälpa dig att prioritera den ordning i vilken du deltar i varje avisering, så att när en resurs äventyras kan du komma åt den direkt. Allvarlighetsgraden baseras på hur säker Security Center är i att hitta eller analytiska används för att utfärda aviseringen samt den konfidensnivå som det fanns ont uppsåt bakom aktiviteten som ledde till aviseringen.

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="103-test-security-response-procedures"></a>10.3: Testsäkerhetssvarsprocedurer

**Vägledning**: Genomföra övningar för att testa systemens funktioner för incidenthantering på regelbunden kadens. Identifiera svaga punkter och luckor och revidera planen efter behov. Se NIST:s publikation: Guide to Test, Training och Exercise Programs for IT Plans and Capabilities:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Ange kontaktinformation för säkerhetsincidenter och konfigurera varningsmeddelanden för säkerhetsincidenter

**Vägledning**: Kontaktinformation för säkerhetsincidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en olaglig eller obehörig part.



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

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Utför regelbundna penetrationstester av dina Azure-resurser och säkerställer reparation av alla kritiska säkerhetsresultat inom 60 dagar

**Vägledning**: Följ Microsofts insatsregler för att säkerställa att dina penetrationstester inte bryter mot Microsofts policyer:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.



Du hittar mer information om Microsofts strategi och genomförande av Red Teaming och penetrationstester på webbplatser på flera webbplatser mot Microsofts hanterade molninfrastruktur, tjänster och program, här:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Delat

## <a name="next-steps"></a>Nästa steg

- Se [Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Läs mer om [Azure Security-originalplaner](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
