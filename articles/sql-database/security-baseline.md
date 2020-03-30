---
title: Azure Security Baseline för Azure SQL Database
description: Azure Security Baseline för Azure SQL Database
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 35d4d44f0b9f1b210f38a034575b589c7211d55c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246678"
---
# <a name="azure-security-baseline-for-azure-sql-database"></a>Azure Security Baseline för Azure SQL Database

Azure Security Baseline för Azure SQL Database innehåller rekommendationer som hjälper dig att förbättra säkerhetspositionen för distributionen.

Baslinjen för de här tjänsterna hämtas från [Azure Security Benchmark version 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), som ger rekommendationer om hur du kan skydda dina molnlösningar på Azure med vår vägledning om bästa praxis.

Mer information finns i [översikt över Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Säkerhetskontroll: Nätverkssäkerhet](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Skydda resurser med nätverkssäkerhetsgrupper eller Azure-brandväggen i det virtuella nätverket

**Vägledning:** Du kan aktivera Azure Private Link för att tillåta åtkomst till Azure PaaS-tjänster (till exempel SQL Database) och Azure-värdbaserade kund-/partnertjänster via en privat slutpunkt i ditt virtuella nätverk. Trafik mellan ditt virtuella nätverk och tjänsten passerar över Microsofts stamnätverk, vilket eliminerar exponering från det offentliga Internet. Om du vill tillåta trafik att nå Azure SQL Database använder du SQL-tjänsttaggarna för att tillåta utgående trafik via nätverkssäkerhetsgrupper.


Regler för virtuella nätverk gör det möjligt för Azure SQL Database att endast acceptera kommunikation som skickas från valda undernät i ett virtuellt nätverk.


Konfigurera privat länk för Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database


Så här använder du slutpunkter och regler för virtuella nätverkstjänster för databasservrar:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Övervaka och logga konfiguration och trafik av virtuella nätverk, undernät och nätverkskort

**Vägledning:** Använd Azure Security Center och åtgärda nätverksskyddsrekommendationer för det undernät som din Azure SQL Database Server distribueras till. För Azure Virtual Machines (VM) som ska ansluta till din Azure SQL Database Server-instans aktiverar du NSG-flödesloggar (Network Security Group) för NSGs som skyddar dessa virtuella datorer och skickar loggar till ett Azure Storage-konto för trafikgranskning. Du kan också skicka NSG-flödesloggar till en Log Analytics-arbetsyta och använda Traffic Analytics för att ge insikter om trafikflödet i ditt Azure-moln. Vissa fördelar med Traffic Analytics är möjligheten att visualisera nätverksaktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödesmönster och identifiera felkonfigurationer i nätverket.


Aktivera NSG-flödesloggar:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


Förstå nätverkssäkerhet som tillhandahålls av Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations


Aktivera och använda Traffic Analytics:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics


Förstå nätverkssäkerhet som tillhandahålls av Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="13-protect-critical-web-applications"></a>1.3: Skydda kritiska webbprogram

**Vägledning**: Ej tillämpligt. Den här rekommendationen är avsedd för Azure Apps Service eller beräkningsresurser som är värdar för webbprogram.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Aktivera DDoS Protection Standard på de virtuella nätverk som är associerade med SQL Server-instanserna för skydd mot distribuerade denial-of-service-attacker. Använd Azure Security Center Integrated Threat Intelligence för att neka kommunikation med kända skadliga eller oanvända IP-adresser på Internet.


KonfigureraR du DDoS-skydd:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection


Förstå Azure Security Center Integrated Threat Intelligence:

https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Spela in nätverkspaket och flödesloggar

**Vägledning:** För virtuella Azure-datorer (VIRTUELLA datorer) som ska ansluta till din Azure SQL-databasinstans aktiverar du NSG-flödesloggar (Network Security Group) för NSG som skyddar dessa virtuella datorer och skickar loggar till ett Azure Storage-konto för trafikgranskning. Om det behövs för att undersöka avvikande aktivitet aktiverar du insamling av nätverksbevakningspaket.


Aktivera NSG-flödesloggar:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


Aktivera Network Watcher:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuera nätverksbaserade system för intrångsdetektering/intrångsförebyggande (IDS/IPS)

**Vägledning**: Aktivera ATP (Advanced Threat Protection) för Azure SQL Database.  Användare får en avisering om misstänkta databasaktiviteter, potentiella sårbarheter och SQL-injektionsattacker, samt avvikande databasåtkomst och frågorsmönster. Avancerat skydd mot hot integrerar också aviseringar med Azure Security Center.

Förstå och använda avancerat skydd mot hot för Azure SQL Database:https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Hantera trafik till webbapplikationer

**Vägledning**: Ej tillämpligt. Den här rekommendationen är avsedd för Azure Apps Service eller beräkningsresurser som är värdar för webbprogram.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimera komplexiteten och de administrativa omkostnaderna för nätverkssäkerhetsreglerna

**Vägledning**: Använd taggar för virtuella nätverkstjänst för att definiera nätverksåtkomstkontroller för nätverkssäkerhetsgrupper eller Azure-brandväggen. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange tjänsttagnamnet (t.ex. ApiManagement) i lämpligt käll- eller målfält för en regel kan du tillåta eller neka trafik för motsvarande tjänst. Microsoft hanterar adressprefixen som omfattas av servicetag och uppdaterar automatiskt servicetag när adresserna ändras.


När du använder tjänstslutpunkter för Azure SQL Database krävs utgående till Azure SQL Database Public IP-adresser: NSG (Network Security Groups) måste öppnas till Azure SQL Database IP-adresser för att tillåta anslutning. Du kan göra detta genom att använda NSG-tjänsttaggar för Azure SQL Database.


Förstå tjänsttaggar med tjänstslutpunkter för Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations


Förstå och använda tjänsttaggar:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Underhåll standardsäkerhetskonfigurationer för nätverksenheter

**Vägledning**: Definiera och implementera nätverkssäkerhetskonfigurationer för dina Azure SQL Database-serverinstanser med Azure Policy. Du kan använda namnområdet "Microsoft.Sql" för att definiera anpassade principdefinitioner eller använda någon av de inbyggda principdefinitionerna som utformats för servernätverksskydd för Azure SQL Database. Ett exempel på en tillämplig inbyggd nätverkssäkerhetsprincip för Azure SQL Database-servern är: "SQL Server bör använda en slutpunkt för virtuella nätverkstjänster".
 

Använd Azure Blueprints för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljöartefakter, till exempel Azure Resource Management-mallar, rollbaserad åtkomstkontroll (RBAC) och principer, i en enda skissdefinition. Använd enkelt skissen på nya prenumerationer och miljöer och finjustera kontroll och hantering genom versionshantering.


Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


Så här skapar du en Azure Blueprint:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regler för konfiguration av dokumenttrafik

**Vägledning**: Använd taggar för nätverkssäkerhetsgrupper (NSG) och andra resurser relaterade till nätverkssäkerhet och trafikflöde. För enskilda NSG-regler använder du fältet "Beskrivning" för att ange affärsbehov och/eller varaktighet (etc.) för alla regler som tillåter trafik till/från ett nätverk.


Använd någon av de inbyggda Azure-principdefinitionerna som är relaterade till taggning, till exempel "Kräv tagg och dess värde" för att säkerställa att alla resurser skapas med taggar och för att meddela dig om befintliga otaggade resurser.


Du kan använda Azure PowerShell eller Azure CLI för att slå upp eller utföra åtgärder på resurser baserat på deras taggar.


Så här skapar och använder du taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Använd automatiserade verktyg för att övervaka nätverksresurskonfigurationer och identifiera ändringar

**Vägledning**: Använd Azure Activity Log för att övervaka nätverksresurskonfigurationer och identifiera ändringar för nätverksresurser relaterade till dina Azure SQL Database-serverinstanser. Skapa aviseringar i Azure Monitor som utlöses när ändringar av kritiska nätverksresurser sker.


Så här visar och hämtar du Azure Activity Log-händelser:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view


Så här skapar du aviseringar i Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [Säkerhetskontroll: Loggning och övervakning](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Använd godkända tidssynkroniseringskällor

**Vägledning**: Microsoft underhåller tidskällor för Azure-resurser. Du kan uppdatera tidssynkronisering för dina beräkningsdistributioner.



Konfigurera tidssynkronisering för Azure-beräkningsresurser:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurera hantering av centrala säkerhetsloggar

**Vägledning**: Aktivera granskning för Azure SQL Database för att spåra databashändelser och skriva dem till en granskningslogg i ditt Azure Storage-konto, Log Analytics-arbetsyta eller Event Hubs.


Dessutom kan du strömma Azure SQL-diagnostik telemetri till Azure SQL Analytics, en molnlösning som övervakar prestanda för Azure SQL-databaser, elastiska pooler och hanterade instanser i stor skala och över flera prenumerationer. Det kan hjälpa dig att samla in och visualisera Azure SQL Database prestandamått, och det har inbyggd intelligens för felsökning av prestanda.

Konfigurera granskning för din Azure SQL-databas:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing


Samla in mått och resursloggar med Azure Monitor:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging


Så här streamar du diagnostik till Azure SQL Analytics:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging#configure-the-streaming-export-of-diagnostic-telemetry

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivera granskningsloggning för Azure-resurser

**Vägledning**: Aktivera granskning på din Azure SQL Database-serverinstans och välj en lagringsplats för granskningsloggarna (Azure Storage, Log Analytics eller Event Hub).


Aktivera granskning för Azure SQL Server:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Samla in säkerhetsloggar från operativsystem

**Vägledning**: Ej tillämpligt. detta riktmärke är avsett för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurera lagring av säkerhetslogglagring

**Vägledning**: När du lagrar dina Azure SQL Database-loggar i en Log Analytics Workspace anger du logglagringsperioden enligt organisationens efterlevnadsregler.



Så här ställer du in loggkvarhållningsparametrar:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2.6: Övervaka och granska loggar

**Vägledning**: Analysera och övervaka loggar för avvikande beteenden och granska regelbundet resultat. Använd Azure Security Centers avancerade skydd för hot för att avisera ovanliga aktiviteter relaterade till din Azure SQL Database-instans. Du kan också konfigurera aviseringar baserat på måttvärden eller Azure Activity Log-poster som är relaterade till dina Azure SQL Database-instanser.


Förstå avancerat skydd mot hot och aviseringar för Azure SQL Server:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview


Konfigurera anpassade aviseringar för Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal?view=azps-1.4.0

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Aktivera aviseringar för avvikande aktivitet

**Vägledning**: Använd Azure Security Center Advanced Threat Protection för Azure SQL-databaser för övervakning och avisering om avvikande aktivitet. Aktivera avancerad datasäkerhet för dina SQL-databaser. Avancerad datasäkerhet omfattar funktioner för att upptäcka och klassificera känsliga data, visa och minska potentiella databassårbarheter och identifiera avvikande aktiviteter som kan tyda på ett hot mot databasen.



Förstå avancerat skydd mot hot och aviseringar för Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview



Aktivera avancerad datasäkerhet för Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security



Hantera aviseringar i Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralisera loggning av skadlig kod

**Vägledning**: Ej tillämpligt. För Azure SQL Server hanteras lösningen mot skadlig kod av Microsoft på den underliggande plattformen.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivera DNS-frågeloggning

**Vägledning**: Ej tillämpligt. DNS-loggning är inte tillämplig på Azure SQL Server.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivera granskningsloggning på kommandoraden

**Vägledning**: Ej tillämpligt. kommandoradsgranskning är inte tillämplig på Azure SQL Server.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Säkerhetskontroll: Identitets- och åtkomstkontroll](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Föra en inventering av administrativa räkenskaper

**Vägledning**: Azure Active Directory (AAD) har inbyggda roller som uttryckligen måste tilldelas och kan ifrågasättas. Använd AAD PowerShell-modulen för att utföra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.


Så här skaffar du en katalogroll i Azure AD med PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0


Så här hämtar du medlemmar i en katalogroll i Azure AD med PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändra standardlösenord där så är tillämpligt

**Vägledning**: Azure Active Directory har inte begreppet standardlösenord. När du etablerar en Azure SQL Database-instans rekommenderar vi att du väljer att integrera autentisering med Azure Active Directory.


Konfigurera och hantera Azure Active Directory-autentisering med Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Använd särskilda administrativa konton

**Vägledning**: Skapa policyer och rutiner kring användningen av särskilda administrativa konton. Använd Azure Security Center Identity and Access Management för att övervaka antalet administrativa konton.



Förstå Azure Security Center identitet och åtkomst:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Använd enkel inloggning (SSO) med Azure Active Directory

**Vägledning**: Ej tillämpligt. Medan du kan konfigurera Azure Active Directory-autentisering för att integrera med Azure SQL Server stöds inte enkel inloggning.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Använda multifaktorautentisering för all Azure Active Directory-baserad åtkomst

**Vägledning:** Aktivera MFA (Azure Active Directory) MultiFaktor Authentication (MFA) och följ Azure Security Center Identity and Access Management-rekommendationerna.


Aktivera MFA i Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


Så här övervakar du identitet och åtkomst i Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Använd dedikerade datorer (arbetsstationer för privilegierad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd en PAW (Privileged Access Workstation) med MFA för multifaktorautentisering konfigurerad för att logga in på och konfigurera Azure-resurser.


Läs mer om arbetsstationer för privilegierad åtkomst:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations


Aktivera MFA i Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Logga och varna för misstänkt aktivitet från administrativa konton

**Vägledning**: Använd Azure Active Directory-säkerhetsrapporter för generering av loggar och aviseringar när misstänkt eller osäker aktivitet inträffar i miljön.



Använd Avancerat skydd mot hot för Azure SQL Database för att identifiera avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.



Identifiera Azure AD-användare som flaggats för riskfylld aktivitet:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk



Så här övervakar du användarnas identitets- och åtkomstaktivitet i Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access



Granska avancerat skydd mot hot och potentiella varningar:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview#advanced-threat-protection-alerts


**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Hantera Azure-resurser från endast godkända platser

**Vägledning**: Använd namngivna platser för villkorlig åtkomst för att tillåta åtkomst till portal- och Azure Resource Management från endast specifika logiska grupperingar av IP-adressintervall eller länder/regioner.


Konfigurera namngivna platser i Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="39-use-azure-active-directory"></a>3.9: Använd Azure Active Directory

**Vägledning**: Skapa en AAD-administratör (Azure Active Directory) för dina Azure SQL Database-serverinstanser.


Konfigurera och hantera Azure Active Directory-autentisering med Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure


Så här skapar och konfigurerar du en AAD-instans:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelbundet granska och förena användaråtkomst

**Vägledning**: Azure Active Directory (AAD) tillhandahåller loggar som hjälper dig att upptäcka inaktuella konton. Dessutom kan du använda Azure Identity-åtkomstgranskningar för att effektivt hantera gruppmedlemskap, åtkomst till företagsprogram och rolltilldelningar. Användarnas åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.


Så här använder du Azure Identity Access Reviews:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Övervaka försök att komma åt inaktiverade konton

**Vägd:** Konfigurera Azure Active Directory (AAD) autentisering med Azure SQL och skapa diagnostikinställningar för Azure Active Directory-användarkonton, skicka granskningsloggar och inloggningsloggar till en Log Analytics-arbetsyta. Konfigurera önskade aviseringar i Log Analytics-arbetsytan.


Konfigurera och hantera Azure Active Directory-autentisering med Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure


Så här integrerar du Azure Activity Logs i Azure Monitor:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Varning på kontots avvikelse för inloggningsbeteende

**Vägledning**: Använd Azure Active Directory (AAD) Identity Protection och riskidentifieringar för att konfigurera automatiska svar på identifierade misstänkta åtgärder relaterade till användaridentiteter. Dessutom kan du matsätta data i Azure Sentinel för vidare undersökning.


Så här visar du Azure AD-risk inloggningar:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins


Konfigurera och aktivera riskprinciper för identitetsskydd:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ge Microsoft åtkomst till relevanta kunddata under supportscenarier

**Vägledning**: I supportscenarier där Microsoft behöver komma åt kunddata tillhandahåller Azure Customer Lockbox ett gränssnitt där kunder kan granska och godkänna eller avvisa begäranden om kunddataåtkomst.


Förstå Customer Lockbox:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

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

**Vägledning**: Implementera separata prenumerationer och/eller hanteringsgrupper för utveckling, test och produktion. Resurser ska avgränsas med Vnet/Undernät, taggas på rätt sätt och skyddas i en NSG- eller Azure-brandvägg. Resurser som lagrar eller bearbetar känsliga data bör isoleras. Använd privat länk; distribuera Azure SQL Server i ditt virtuella nätverk och ansluta privat med privata slutpunkter.



Så här skapar du ytterligare Azure-prenumerationer:

https://docs.microsoft.com/azure/billing/billing-create-subscription



Så här skapar du hanteringsgrupper:

https://docs.microsoft.com/azure/governance/management-groups/create



Så här skapar och använder du Taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



Konfigurera privat länk för Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Övervaka och blockera obehörig överföring av känslig information

**Vägledning**: För Azure SQL-databaser som lagrar eller bearbetar känslig information markerar du databasen och relaterade resurser som känsliga med taggar. Konfigurera privat länk tillsammans med network security group service-taggar på dina Azure SQL Database-instanser för att förhindra exfiltration av känslig information.



För den underliggande plattformen som hanteras av Microsoft behandlar Microsoft allt kundinnehåll som känsligt och gör stora ansträngningar för att skydda mot förlust av kunddata och exponering. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och underhållit en uppsättning robusta dataskyddskontroller och funktioner.



Konfigurera private link och NSG:er för att förhindra dataexfiltration på dina Azure SQL Database-instanser:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview



Förstå kunddataskyddet i Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Kryptera all känslig information under transport

**Vägledning**: Azure SQL Database skyddar dina data genom att kryptera data i rörelse med Transport Layer Security. SQL Server framtvingar kryptering (SSL/TLS) hela tiden för alla anslutningar. Detta säkerställer att alla data krypteras "under överföring" mellan klienten och servern oavsett inställningen av Encrypt eller TrustServerCertificate i anslutningssträngen.



Förstå Azure SQL-kryptering under överföring:

https://docs.microsoft.com/azure/sql-database/sql-database-security-overview#information-protection-and-encryption

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Använda ett aktivt identifieringsverktyg för att identifiera känsliga data

**Vägledning**: Använd funktionen för identifiering och klassificering av Azure SQL Database-data. Dataidentifiering och klassificering ger avancerade funktioner som är inbyggda &amp; i Azure SQL Database för att upptäcka, klassificera, märka skydda känsliga data i dina databaser.



Så här använder du dataidentifiering och klassificering för Azure SQL Server:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Använd Azure RBAC för att kontrollera åtkomsten till resurser

**Vägledning**: Använd Azure Active Directory (AAD) för att autentisera och kontrollera åtkomsten till Azure SQL Database-instanser.


Så här integrerar du Azure SQL Server med Azure Active Directory för autentisering:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication


Så här styr du åtkomsten i Azure SQL Server:

https://docs.microsoft.com/azure/sql-database/sql-database-control-access

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Använd värdbaserad dataförlustprevention för att genomdriva åtkomstkontroll

**Vägledning**: Microsoft hanterar den underliggande infrastrukturen för Azure SQL Database och har implementerat strikta kontroller för att förhindra förlust eller exponering av kunddata.

Förstå kunddataskyddet i Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Kryptera känslig information i vila

**Vägledning:** Transparent datakryptering (TDE) hjälper till att skydda Azure SQL Database, Azure SQL-hanterad instans och Azure Data Warehouse mot hotet om skadlig offlineaktivitet genom att kryptera data i vila. TDE utför realtidskryptering och realtidsdekryptering av databasen, tillhörande säkerhetskopior och transaktionsloggfiler i vila, utan att några ändringar krävs i programmet. Som standard är transparent datakryptering aktiverat för alla nyligen distribuerade Azure SQL-databaser. TDE-krypteringsnyckeln kan hanteras av antingen Microsoft eller kunden.


Så här hanterar du transparent datakryptering och använder dina egna krypteringsnycklar:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal#manage-transparent-data-encryption

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logga och avisera om ändringar i kritiska Azure-resurser

**Vägledning**: Använd Azure Monitor med Azure Activity Log för att skapa aviseringar för när ändringar sker i produktionsinstanser av Azure SQL-databaser och andra kritiska eller relaterade resurser.


Så här skapar du aviseringar för Azure Activity Log-händelser:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Säkerhetskontroll: Sårbarhetshantering](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Kör automatiserade sårbarhetsskanningsverktyg

**Vägledning**: Aktivera avancerad datasäkerhet för Azure SQL Database och följa rekommendationer från Azure Security Center om hur du utför sårbarhetsbedömningar på dina Azure SQL-servrar.



Kör sårbarhetsutvärderingar på dina Azure SQL-databaser:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment



Aktivera avancerad datasäkerhet:

https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security



Så här implementerar du rekommendationer för bedömning av sårbarhetsbedömning av Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Distribuera en lösning för hantering av automatisk operativsystemets korrigering

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Distribuera automatisk lösning för hantering av programkorrigering från tredje part

**Vägledning**: Ej tillämpligt. detta riktmärke är avsett för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Jämför sårbarhetssökningar från rygg mot rygg

**Vägledning**: Aktivera periodiska återkommande genomsökningar för dina Azure SQL Database-instanser. Detta konfigurerar en sårbarhetsbedömning för att automatiskt köra en genomsökning på databasen en gång i veckan. En sammanfattning av skanningsresultatet skickas till den e-postadress som du anger. Jämför resultaten för att kontrollera att säkerhetsproblem har åtgärdats.



Exporterar en rapport om sårbarhetsbedömning i Azure Security Center:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment#implement-vulnerability-assessment

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Använd en riskklassificeringsprocess för att prioritera reparation av upptäckta sårbarheter

**Vägledning**: Använd standardriskklassificeringarna (Secure Score) som tillhandahålls av Azure Security Center.

Förstå Azure Security Center Secure Score:https://docs.microsoft.com/azure/security-center/security-center-secure-score

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Använd Identifiering av Azure Asset

**Vägledning**: Använd Azure Resource Graph för att fråga och identifiera alla resurser (inklusive Azure SQL Server-instanser) i dina prenumerationer.  Se till att du har lämpliga (läs) behörigheter i din klientorganisation och kan räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.


Även om klassiska Azure-resurser kan identifieras via Azure Resource Graph, rekommenderas det starkt att skapa och använda Azure Resource Manager-resurser framöver.


Så här skapar du frågor med Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


Så här visar du dina Azure-prenumerationer:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0


Förstå Azure RBAC:https://docs.microsoft.com/azure/role-based-access-control/overview

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="62-maintain-asset-metadata"></a>6.2: Underhåll metadata för tillgångar

**Vägledning**: Använd taggar på Azure-resurser som ger metadata för att logiskt ordna dem till en taxonomi.



Så här skapar och använder du Taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanteringsgrupper och separata prenumerationer, där så är lämpligt, för att organisera och spåra tillgångar. Stämma av lager regelbundet och se till att obehöriga resurser tas bort från prenumerationen i tid.



Så här skapar du ytterligare Azure-prenumerationer:

https://docs.microsoft.com/azure/billing/billing-create-subscription



Så här skapar du hanteringsgrupper:

https://docs.microsoft.com/azure/governance/management-groups/create



Så här skapar och använder du Taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Underhåll en inventering av godkända Azure-resurser och programvarutitlar

**Vägledning**: Definiera lista över godkända Azure-resurser och godkänd programvara för dina beräkningsresurser

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Övervaka för icke godkända Azure-resurser

**Vägledning**: Använd Azure Policy för att sätta begränsningar för vilken typ av resurser som kan skapas i kundprenumerationer med hjälp av följande inbyggda principdefinitioner:

- Otillåtna resurstyper
- Tillåtna resurstyper

Använd Azure Resource Graph för att fråga/identifiera resurser i dina prenumerationer. Se till att alla Azure-resurser som finns i miljön är godkända.

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här skapar du frågor med Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Övervaka icke godkända program i beräkningsresurser

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Ta bort icke godkända Azure-resurser och program

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="68-use-only-approved-applications"></a>6.8: Använd endast godkända ansökningar

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="69-use-only-approved-azure-services"></a>6.9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure Policy för att begränsa vilken typ av resurser som kan skapas i kundprenumerationer med hjälp av följande inbyggda principdefinitioner:

- Otillåtna resurstyper
- Tillåtna resurstyper

Använd Azure Resource Graph för att fråga/identifiera resurser i dina prenumerationer. Se till att alla Azure-resurser som finns i miljön är godkända.

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här nekar du en viss resurstyp med Azure Policy:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="610-implement-approved-application-list"></a>6.10: Genomföra godkänd ansökningslista

**Vägledning**: Ej tillämpligt. Den här rekommendationen är avsedd för program som körs på beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Begränsa användarnas möjlighet att interagera med Azure Resources Manager via skript

**Vägledning**: Använd Azure Villkorlig åtkomst för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "Blockera åtkomst" för "Microsoft Azure Management"-appen.


Konfigurera villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Begränsa användarnas möjlighet att köra skript i beräkningsresurser

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fysiskt eller logiskt segregera högriskapplikationer

**Vägledning**: Ej tillämpligt. Den här rekommendationen är avsedd för App Service eller beräkningsresurser som är värdar för skrivbords- eller webbprogram.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Säkerhetskontroll: Säker konfiguration](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Upprätta säkra konfigurationer för alla Azure-resurser

**Vägd:** Använd Azure Policy- eller Azure Security Center-rekommendationer för Azure SQL-servrar/databaser för att underhålla säkerhetskonfigurationer för alla Azure-resurser.


Konfigurera och hantera Azure-princip:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Upprätta konfigurationer för säkra operativsystem

**Vägledning**: Ej tillämpligt. den här rekommendationen avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Underhåll säkra Azure-resurskonfigurationer

**Vägledning**: Använd Azure-principen [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar för dina Azure-resurser.



Konfigurera och hantera Azure-princip:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



Förstå Azure-principeffekter:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Underhåll konfigurationer av säkra operativsystem

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Säkert lagra konfiguration av Azure-resurser

**Vägledning:** Om du använder anpassade Azure-principdefinitioner använder du Azure DevOps eller Azure Repos för att lagra och hantera din kod på ett säkert sätt.



Så här lagrar du kod i Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops



Dokumentation för Azure Repos:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Lagra anpassade operativsystemavbildningar på ett säkert sätt

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Distribuera verktyg för hantering av systemkonfigurationer

**Vägd:** Använd Azure Policy-alias i namnområdet "Microsoft.SQL" för att skapa anpassade principer för att avisera, granska och framtvinga systemkonfigurationer. Dessutom, utveckla en process och pipeline för att hantera principundantag.



Konfigurera och hantera Azure-princip:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Distribuera verktyg för systemkonfigurationshantering för operativsystem

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementera automatisk konfigurationsövervakning för Azure-tjänster

**Vägledning:** Utnyttja Azure Security Center för att utföra baslinjegenomsökningar för dina Azure SQL-servrar och databaser.



Åtgärda rekommendationer i Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementera automatisk konfigurationsövervakning för operativsystem

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="711-manage-azure-secrets-securely"></a>7.11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: Använd Azure Key Vault för att lagra krypteringsnycklar för Azure SQL Database Transparent Data Encryption (TDE).



Så här skyddar du känsliga data som lagras i Azure SQL Server och lagrar krypteringsnycklarna i Azure Key Vault:

https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Hantera identiteter på ett säkert och automatiskt sätt

**Vägledning**: Använd hanterade identiteter för att tillhandahålla Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory (AAD). Med hanterade identiteter kan du autentisera till alla tjänster som stöder AAD-autentisering, inklusive Azure Key Vault, utan några autentiseringsuppgifter i koden.


Självstudiekurs: Använda en systemtilldelad windows VM-systemidentitet för att komma åt Azure SQL:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql


Konfigurerar hanterade identiteter:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

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

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser. Microsoft hanterar skadlig kod mot skadlig kod för underliggande plattform.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Förskansa filer som ska överföras till Azure-resurser som inte är beräkning

**Vägledning:** Microsoft anti-malware är aktiverat på den underliggande värden som stöder Azure-tjänster (till exempel Azure App Service), men det körs inte på kundinnehåll.


Förskansa allt innehåll som överförs till azure-resurser som inte är beräkning, till exempel App Service, DataSjölagring, Blob Storage, Azure SQL Server osv. Microsoft kan inte komma åt dina data i dessa fall.


Förstå Microsoft Antimalware för Azure Cloud Services och virtuella datorer:https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Se till att programvara och signaturer mot skadlig kod uppdateras

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser. Microsoft hanterar skadlig kod mot skadlig kod för underliggande plattform.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Säkerhetskontroll: Dataåterställning](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Se till att regelbundna automatiska säkerhetskopieringar

**Vägledning:** För att skydda ditt företag från dataförlust skapar Azure SQL Database automatiskt fullständiga säkerhetskopieringar av databaser varje vecka, differentiella säkerhetskopieringar av databaser var 12:e timme och säkerhetskopiering av transaktionsloggar var 5–10:e minut. Säkerhetskopiorna lagras i RA-GRS-lagring i minst 7 dagar för alla tjänstnivåer. Alla tjänstnivåer utom Grundläggande stöder konfigurerbar lagringsperiod för säkerhetskopiering för återställning i point-in-time, upp till 35 dagar.


För att uppfylla olika efterlevnadskrav kan du välja olika kvarhållningsperioder för veckovisa, månatliga och/eller årliga säkerhetskopior. Lagringsförbrukningen beror på den valda frekvensen av säkerhetskopior och kvarhållningsperioden.The storage consumption depends on the selected frequency of backups and the retention period(s).


Förstå säkerhetskopieringar och affärskontinuitet med Azure SQL Server:

https://docs.microsoft.com/azure/sql-database/sql-database-business-continuity

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Delat

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Utför fullständiga systemsäkerhetskopior och säkerhetskopiera alla kundhanterade nycklar

**Vägledning:** Azure SQL Database skapar automatiskt databassäkerhetskopior som sparas mellan 7 och 35 dagar och använder Azure read-access geo-redundant lagring (RA-GRS) för att säkerställa att de bevaras även om datacentret inte är tillgängligt. Dessa säkerhetskopior skapas automatiskt. Om det behövs aktiverar du långsiktiga geouppsagade säkerhetskopior för dina Azure SQL-databaser.


Om du använder kundhanterade nycklar för transparent datakryptering kontrollerar du att nycklarna säkerhetskopieras.


Förstå säkerhetskopior i Azure SQL Server:

https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups?tabs=single-database


Så här säkerhetskopierar du nyckelnycklar i Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validera alla säkerhetskopior inklusive kundhanterade nycklar

**Vägledning**: Säkerställ möjligheten att regelbundet utföra dataåterställning av innehåll i Azure Backup. Testa vid behov återställa innehållet till en isolerad VLAN. Testa återställning av säkerhetskopierade kundhanterade nycklar.


Så här återställer du nyckelvalvsnycklar i Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0


Återställa Azure SQL Database-säkerhetskopior med point-in-time-återställning:

https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#point-in-time-restore

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Se till att säkerhetskopior och kundhanterade nycklar skyddas

**Vägledning**: Aktivera mjuk borttagning i Azure Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning.


Aktivera mjuk borttagning i Key Vault:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Övervakning av Azure Security Center**: Ja

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
Säkerhetsaviseringar i Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-alerts-overview


**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="103-test-security-response-procedures"></a>10.3: Testsäkerhetssvarsprocedurer

**Vägledning**: Genomföra övningar för att testa systemens funktioner för incidenthantering på regelbunden kadens. Identifiera svaga punkter och luckor och revidera planen efter behov.



Du kan läsa NIST:s publikation: Guide till test-, utbildnings- och träningsprogram för IT-abonnemang och -funktioner:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Ange kontaktinformation för säkerhetsincidenter och konfigurera varningsmeddelanden för säkerhetsincidenter

**Vägledning**: Kontaktinformation för säkerhetsincidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en olaglig eller obehörig part.



Så här ställer du in säkerhetskontakten för Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Införliva säkerhetsvarningar i ditt incidenthanteringssystem

**Vägledning**: Exportera dina Azure Security Center-aviseringar och rekommendationer med hjälp av funktionen Kontinuerlig export. Kontinuerlig export gör att du kan exportera aviseringar och rekommendationer antingen manuellt eller på ett kontinuerligt sätt. Du kan använda Azure Security Center-dataanslutningen för att strömma aviseringarna till Sentinel.


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
