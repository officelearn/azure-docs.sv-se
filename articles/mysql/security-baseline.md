---
title: Azure Security Baseline för Azure Database för MySQL
description: Azure Security Baseline för Azure Database för MySQL
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 362b35af191c1caebeb30149ce060a5366182ec4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289425"
---
# <a name="azure-security-baseline-for-azure-database-for-mysql"></a>Azure Security Baseline för Azure Database för MySQL

Azure Security Baseline för Azure Database for MySQL innehåller rekommendationer som hjälper dig att förbättra säkerhetspositionen för distributionen.

Baslinjen för den här tjänsten hämtas från [Azure Security Benchmark version 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), som ger rekommendationer om hur du kan skydda dina molnlösningar på Azure med vår vägledning om bästa praxis.

Mer information finns i [översikt över Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Säkerhetskontroll: Nätverkssäkerhet](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Skydda resurser med nätverkssäkerhetsgrupper eller Azure-brandväggen i det virtuella nätverket

**Vägledning:** Konfigurera privat länk för Azure-databas för MySQL med privata slutpunkter. Med Private Link kan du ansluta till olika PaaS-tjänster i Azure via en privat slutpunkt. Azure Private Link ger i huvudsak Azure-tjänster i ditt privata virtuella nätverk (VNet). Trafiken mellan det virtuella nätverket och MySQL-instansen färdas i Microsofts stamnätsnätverk.

Alternativt kan du använda slutpunkter för virtual network service för att skydda och begränsa nätverksåtkomsten till azure-databasen för MySQL-implementeringar. Virtuella nätverksregler är en brandväggssäkerhetsfunktion som styr om din Azure Database for MySQL-server accepterar kommunikation som skickas från vissa undernät i virtuella nätverk.

Du kan också skydda din Azure-databas för MySQL-server med brandväggsregler. Serverbrandväggen förhindrar all åtkomst till databasservern tills du anger vilka datorer som har behörighet. Du konfigurerar brandväggen genom att skapa brandväggsregler som anger intervall med godkända IP-adresser. Du kan skapa brandväggsregler på servernivå.

Konfigurera privat länk för Azure-databas för MySQL:https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-portal

Så här skapar och hanterar du slutpunkter för VNet-tjänst och VNet-regler i Azure Database för MySQL:https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

Konfigurerar Azure Database för MySQL-brandväggsregler:https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-portal

**Övervakning av Azure Security Center**: Inte tillgänglig

**Ansvar**: Kund

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Övervaka och logga konfiguration och trafik av virtuella nätverk, undernät och nätverkskort

**Vägledning:** När azure-databasen för MySQL-instansen är skyddad till en privat slutpunkt kan du distribuera virtuella datorer i samma virtuella nätverk. Du kan använda en nätverkssäkerhetsgrupp (NSG) för att minska risken för dataexfiltration. Aktivera NSG-flödesloggar och skicka loggar till ett lagringskonto för trafikgranskning. Du kan också skicka NSG-flödesloggar till en Log Analytics-arbetsyta och använda Traffic Analytics för att ge insikter om trafikflödet i ditt Azure-moln. Vissa fördelar med Traffic Analytics är möjligheten att visualisera nätverksaktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödesmönster och identifiera felkonfigurationer i nätverket.

Konfigurera privat länk för Azure-databas för MySQL:https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-portal

Aktivera NSG-flödesloggar:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Aktivera och använda Traffic Analytics:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="13-protect-critical-web-applications"></a>1.3: Skydda kritiska webbprogram

**Vägledning**: Ej tillämpligt. Den här rekommendationen är avsedd för webbprogram som körs på Azure App Service eller beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Använd avancerat skydd mot hot för Azure-databas för MySQL. Avancerat skydd mot hot upptäcker avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

Aktivera DDoS Protection Standard på de virtuella nätverk som är associerade med azure-databasen för MySQL-instanser för att skydda mot DDoS-attacker. Använd Azure Security Center Integrated Threat Intelligence för att neka kommunikation med kända skadliga eller oanvända IP-adresser på Internet.

Konfigurera avancerat skydd mot hot för Azure-databas för MySQL:https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

KonfigureraR du DDoS-skydd:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Spela in nätverkspaket och flödesloggar

**Vägledning:** När azure-databasen för MySQL-instansen är skyddad till en privat slutpunkt kan du distribuera virtuella datorer i samma virtuella nätverk. Du kan sedan konfigurera en nätverkssäkerhetsgrupp (NSG) för att minska risken för dataexfiltration. Aktivera NSG-flödesloggar och skicka loggar till ett lagringskonto för trafikgranskning. Du kan också skicka NSG-flödesloggar till en Log Analytics-arbetsyta och använda Traffic Analytics för att ge insikter om trafikflödet i ditt Azure-moln. Vissa fördelar med Traffic Analytics är möjligheten att visualisera nätverksaktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödesmönster och identifiera felkonfigurationer i nätverket.

Aktivera NSG-flödesloggar:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Aktivera och använda Traffic Analytics:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuera nätverksbaserade system för intrångsdetektering/intrångsförebyggande (IDS/IPS)

**Vägledning**: Använd avancerat skydd mot hot för Azure-databas för MySQL. Avancerat skydd mot hot upptäcker avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

Konfigurera avancerat skydd mot hot för Azure-databas för MySQL:https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Hantera trafik till webbapplikationer

**Vägledning**: Ej tillämpligt. Den här rekommendationen är avsedd för webbprogram som körs på Azure App Service eller beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimera komplexiteten och de administrativa omkostnaderna för nätverkssäkerhetsreglerna

**Vägledning**: Om du vill ha resurser som behöver åtkomst till Azure-databasen för MySQL-instanser använder du Taggar för virtuella nätverkstjänst för att definiera nätverksåtkomstkontroller i nätverkssäkerhetsgrupper eller Azure-brandväggen. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange tjänsttagnamnet (t.ex. WestUs) i lämpligt käll- eller målfält för en regel kan du tillåta eller neka trafik för motsvarande tjänst. Microsoft hanterar adressprefixen som omfattas av servicetag och uppdaterar automatiskt servicetag när adresserna ändras.

Azure Database för MySQL använder tjänsttaggarna "Microsoft.Sql".

Mer information om hur du använder tjänsttaggar:https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Förstå tjänsttag-användning för Azure Database för MySQL:https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet#terminology-and-description

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Underhåll standardsäkerhetskonfigurationer för nätverksenheter

**Vägledning**: Definiera och implementera standardsäkerhetskonfigurationer för nätverksinställningar och nätverksresurser som är associerade med dina Azure-databas för MySQL-instanser med Azure Policy. Använd Azure Policy-alias i namnområdena "Microsoft.DBforMySQL" och "Microsoft.Network" för att skapa anpassade principer för granskning eller framtvingande av nätverkskonfigurationen för dina Azure-databas för MySQL-instanser. Du kan också använda inbyggda principdefinitioner relaterade till nätverk eller dina Azure-databas för MySQL-instanser, till exempel:

- DDoS Protection Standard bör aktiveras

- Framtvinga SSL-anslutning bör aktiveras för MySQL-databasservrar

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Princip-exempel för nätverk:https://docs.microsoft.com/azure/governance/policy/samples/#network

Så här skapar du en Azure Blueprint:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regler för konfiguration av dokumenttrafik

**Vägledning**: Använd taggar för resurser som är relaterade till nätverkssäkerhet och trafikflöde för dina Azure-databas för MySQL-instanser för att tillhandahålla metadata och logisk organisation.

Använd någon av de inbyggda Azure-principdefinitionerna som är relaterade till taggning, till exempel "Kräv tagg och dess värde", för att säkerställa att alla resurser skapas med taggar och för att meddela dig om befintliga otaggade resurser.

Du kan använda Azure PowerShell eller Azure CLI för att slå upp eller utföra åtgärder på resurser baserat på deras taggar.

Så här skapar och använder du taggar:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Använd automatiserade verktyg för att övervaka nätverksresurskonfigurationer och identifiera ändringar

**Vägledning**: Använd Azure Activity Log för att övervaka nätverksresurskonfigurationer och identifiera ändringar för nätverksresurser relaterade till dina Azure-databas för MySQL-instanser. Skapa aviseringar i Azure Monitor som utlöses när ändringar av kritiska nätverksresurser sker.

Så här visar och hämtar du Azure Activity Log-händelser:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Så här skapar du aviseringar i Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [Säkerhetskontroll: Loggning och övervakning](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Använd godkända tidssynkroniseringskällor

**Vägledning**: Microsoft underhåller tidskällan som används för Azure-resurser, till exempel Azure Database for MySQL för tidsstämplar i loggarna.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurera hantering av centrala säkerhetsloggar

**Vägledning:** Aktivera diagnostikinställningar och serverloggar och intag av loggar till aggregerade säkerhetsdata som genereras av dina Azure-databas för MySQL-instanser. I Azure Monitor använder du Log Analytics Workspace(s) för att fråga och utföra analyser och använda Azure Storage-konton för långsiktig/arkiveringslagring. Alternativt kan du aktivera och ombord data till Azure Sentinel eller en tredje part SIEM.

Förstå serverloggar för Azure Database för MySQL:https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs

Så här går du ombord på Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Övervakning av Azure Security Center**: Inte tillgänglig

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivera granskningsloggning för Azure-resurser

**Vägledning**: Aktivera diagnostikinställningar i Azure-databasen för MySQL-instanser för åtkomst till gransknings-, långsamma fråge- och MySQL-måttloggar. Se till att du specifikt aktiverar MySQL-granskningsloggen. Aktivitetsloggar, som är automatiskt tillgängliga, inkluderar händelsekälla, datum, användare, tidsstämpel, källadresser, måladresser och andra användbara element. Du kan också aktivera diagnostikinställningar för Azure Activity Log och skicka loggarna till samma Log Analytics-arbetsyta eller lagringskonto.

Förstå serverloggar för Azure Database för MySQL:https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs

Konfigurera och komma åt långsamma frågeloggar för Azure Database för MySQL:https://docs.microsoft.com/azure/mysql/howto-configure-server-logs-in-portal

Konfigurera och komma åt granskningsloggar för Azure Database för MySQL:https://docs.microsoft.com/azure/mysql/howto-configure-audit-logs-portal

Konfigurerar diagnostikinställningar för Azure Activity Log:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Övervakning av Azure Security Center**: Inte tillgänglig

**Ansvar**: Kund

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Samla in säkerhetsloggar från operativsystem

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurera lagring av säkerhetslogglagring

**Vägledning**: Inom Azure Monitor anger du kvarhållningsperioden enligt organisationens efterlevnadsregler för logganalysarbetsytan som används för att lagra dina Azure-databas för MySQL-loggar. Använd Azure Storage-konton för långsiktig/arkiveringslagring.

Så här anger du loggkvarhållningsparametrar för Logganalysarbetsytor:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

Lagra resursloggar i ett Azure Storage-konto:https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2.6: Övervaka och granska loggar

**Vägledning**: Analysera och övervaka loggar från azure-databasen för MySQL-instanser för avvikande beteende. Använd Azure Monitor's Log Analytics för att granska loggar och utföra frågor om loggdata. Alternativt kan du aktivera och ombord data till Azure Sentinel eller en tredje part SIEM.

Så här går du ombord på Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Mer information om Logganalysen:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Så här utför du anpassade frågor i Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Aktivera aviseringar för avvikande aktivitet

**Vägledning**: Aktivera avancerat skydd mot hot för Azure-databas för MySQL. Avancerat skydd mot hot upptäcker avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

Dessutom kan du aktivera serverloggar och diagnostikinställningar för MySQL och skicka loggar till en Log Analytics-arbetsyta. Ombord på din Log Analytics-arbetsyta till Azure Sentinel eftersom den ger en sky-lösning (Security Orchestration Automated Response). Detta gör det möjligt att skapa spelböcker (automatiserade lösningar) för att åtgärda säkerhetsproblem.

Aktivera avancerat skydd mot hot för Azure Database för MySQL (förhandsversion):https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

Förstå serverloggar för Azure Database för MySQL:https://docs.microsoft.com/azure/mysql/concepts-monitoring#server-logs

Konfigurera och komma åt långsamma frågeloggar för Azure Database för MySQL:https://docs.microsoft.com/azure/mysql/howto-configure-server-logs-in-portal

Konfigurera och komma åt granskningsloggar för Azure Database för MySQL:https://docs.microsoft.com/azure/mysql/howto-configure-audit-logs-portal

Konfigurerar diagnostikinställningar för Azure Activity Log:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

Så här går du ombord på Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralisera loggning av skadlig kod

**Vägledning**: Ej tillämpligt. Azure Database for MySQL bearbetar eller producerar inte anti-malware relaterade loggar.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivera DNS-frågeloggning

**Vägledning**: Ej tillämpligt. Azure Database for MySQL bearbetar eller producerar inte DNS-relaterade loggar.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivera granskningsloggning på kommandoraden

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Säkerhetskontroll: Identitets- och åtkomstkontroll](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Föra en inventering av administrativa räkenskaper

**Vägledning**: Underhåll en inventering av användarkonton som har administrativ åtkomst till hanteringsplanet (t.ex. Azure-portalen) för din Azure-databas för MySQLinstances. Dessutom, upprätthålla en inventering av administrativa konton som har åtkomst till dataplanet (inom själva databasen) i din Azure-databas för MySQL-instanser. (När du skapar MySQL-servern anger du autentiseringsuppgifter för en administratörsanvändare. Den här administratören kan användas för att skapa ytterligare MySQL-användare.)

Azure Database for MySQL stöder inte inbyggd rollbaserad åtkomstkontroll, men du kan skapa anpassade roller baserat på specifika resursprovideralternativ.

Förstå anpassade roller för Azure-prenumeration:https://docs.microsoft.com/azure/role-based-access-control/custom-roles 

Förstå Azure Database för MySQL-resursprovideråtgärder:https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdbformysql

Förstå åtkomsthantering för Azure Database för MySQL:https://docs.microsoft.com/azure/mysql/concepts-security#access-management

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändra standardlösenord där så är tillämpligt

**Vägledning**: Azure AD har inte begreppet standardlösenord.

När Azure-databasen för MySQL-resurs skapas tvingar Azure skapandet av en administrativ användare med ett starkt lösenord. När MySQL-instansen har skapats kan du dock använda det första serveradministratörskontot som du skapade för att skapa ytterligare användare och bevilja administrativ åtkomst till dem. När du skapar dessa konton ska du se till att du konfigurerar ett annat, starkt lösenord för varje konto.

Så här skapar du ytterligare konton för Azure Database för MySQL:https://docs.microsoft.com/azure/mysql/howto-create-users

Så här uppdaterar du administratörslösenordet:https://docs.microsoft.com/azure/mysql/howto-create-manage-server-portal#update-admin-password

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Använd särskilda administrativa konton

**Vägledning**: Skapa standardrutiner kring användningen av dedikerade administrativa konton som har åtkomst till dina Azure-databas för MySQL-instanser. Använd Azure Security Center Identity och åtkomsthantering för att övervaka antalet administrativa konton.

Förstå Azure Security Center identitet och åtkomst:https://docs.microsoft.com/azure/security-center/security-center-identity-access

Förstå hur du skapar administratörsanvändare i Azure Database för MySQL:https://docs.microsoft.com/azure/mysql/howto-create-users

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Använd enkel inloggning (SSO) med Azure Active Directory

**Vägledning:** Att logga in i Azure Database for MySQL stöds både med användarnamn/lösenord som konfigurerats direkt i databasen, samt med hjälp av en AZURE Active Directory -identitet (AD) och använder en Azure AD-token för att ansluta. När du använder en Azure AD-token stöds olika metoder, till exempel en Azure AD-användare, en Azure AD-grupp eller ett Azure AD-program som ansluter till databasen.

Separat, kontroll plan tillgång för MySQL är tillgänglig via REST API och stöder SSO. Om du vill autentisera anger du auktoriseringshuvudet för dina begäranden till en JSON-webbtoken som du hämtar från Azure Active Directory.

Använd Azure Active Directory för autentisering med Azure Database för MySQL:https://docs.microsoft.com/azure/mysql/howto-configure-sign-in-azure-ad-authentication

Förstå Azure Database för MySQL REST API:https://docs.microsoft.com/rest/api/mysql/

Förstå SSO med Azure AD:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Använda multifaktorautentisering för all Azure Active Directory-baserad åtkomst

**Vägledning**: Aktivera MFA (Azure Active Directory Multi Factor Authentication) och följ Azure Security Center Identity and Access Management-rekommendationer. När du använder Azure AD-token för att logga in i databasen kan du med detta kräva multifaktorautentisering för databassignaringar.

Aktivera MFA i Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Använd Azure Active Directory för autentisering med Azure Database för MySQL:https://docs.microsoft.com/azure/mysql/howto-configure-sign-in-azure-ad-authentication

Så här övervakar du identitet och åtkomst i Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Använd dedikerade datorer (arbetsstationer för privilegierad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd privilegierade åtkomstarbetsstationer (PAWs) med MFA (MultiFaktor Authentication) konfigurerade för att logga in på och konfigurera Azure-resurser.

Läs mer om arbetsstationer för privilegierad åtkomst:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Aktivera MFA i Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Logga och varna för misstänkt aktivitet från administrativa konton

**Vägledning**: Aktivera avancerat skydd mot hot för Azure-databas för MySQL för att generera aviseringar för misstänkt aktivitet.

Dessutom kan du använda Azure AD Privileged Identity Management (PIM) för generering av loggar och aviseringar när misstänkt eller osäker aktivitet inträffar i miljön.

Använd Azure AD-riskidentifieringar för att visa aviseringar och rapporter om riskfyllda användarbeteenden.

Konfigurera avancerat skydd mot hot för Azure-databas för MySQL:https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

Så här distribuerar du PIM (Privilegierad identitetshantering):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Förstå Azure AD-riskidentifieringar:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Hantera Azure-resurser från endast godkända platser

**Vägledning**: Använd namngivna platser för villkorlig åtkomst för att tillåta åtkomst till portal och Azure Resource Manager från endast specifika logiska grupperingar av IP-adressintervall eller länder/regioner.

Konfigurera namngivna platser i Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="39-use-azure-active-directory"></a>3.9: Använd Azure Active Directory

**Vägledning**: Använd Azure Active Directory (AD) som det centrala autentiserings- och auktoriseringssystemet. Azure AD skyddar data genom att använda stark kryptering för data i vila och under överföring. Azure AD saltar också, hashar och lagrar användarautentiseringsuppgifter på ett säkert sätt.

För att logga in på Azure Database for MySQL rekommenderas att använda Azure AD och använda en Azure AD-token för att ansluta. När du använder en Azure AD-token stöds olika metoder, till exempel en Azure AD-användare, en Azure AD-grupp eller ett Azure AD-program som ansluter till databasen. 

Azure AD-autentiseringsuppgifter kan också användas för administration på hanteringsplansnivå (t.ex. Azure-portalen) för att styra MySQL-administratörskonton.

Använd Azure Active Directory för autentisering med Azure Database för MySQL:https://docs.microsoft.com/azure/mysql/howto-configure-sign-in-azure-ad-authentication

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelbundet granska och förena användaråtkomst

**Vägledning**: Granska Azure Active Directory-loggarna för att identifiera inaktuella konton som kan inkludera dem med Azure Database för MySQL-administrativa roller. Dessutom kan du använda Azure Identity Access Reviews för att effektivt hantera gruppmedlemskap, åtkomst till företagsprogram som kan användas för att komma åt Azure Database för MySQL och rolltilldelningar. Användaråtkomst bör granskas regelbundet, till exempel var 90:e dag för att se till att endast rätt användare har fortsatt åtkomst.

Förstå Azure AD-rapporteringhttps://docs.microsoft.com/azure/active-directory/reports-monitoring/

Så här använder du Azure Identity Access Reviews:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Övervaka försök att komma åt inaktiverade konton

**Vägledning**: Aktivera diagnostikinställningar för Azure Database för MySQL och Azure Active Directory, skicka alla loggar till en Log Analytics-arbetsyta. Konfigurera önskade aviseringar (till exempel misslyckade autentiseringsförsök) i Log Analytics.

Konfigurera och komma åt långsamma frågeloggar för Azure Database för MySQL:https://docs.microsoft.com/Azure/mysql/howto-configure-server-logs-in-portal

Konfigurera och komma åt granskningsloggar för Azure Database för MySQL:https://docs.microsoft.com/Azure/mysql/howto-configure-audit-logs-portal

Så här integrerar du Azure Activity Logs i Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Övervakning av Azure Security Center**: Inte tillgänglig

**Ansvar**: Kund

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Varning på kontots avvikelse för inloggningsbeteende

**Vägledning**: Aktivera avancerat skydd mot hot för Azure-databas för MySQL för att generera aviseringar för misstänkt aktivitet.

Använd Azure Active Directorys funktioner för identitetsskydd och riskidentifiering för att konfigurera automatiska svar på identifierade misstänkta åtgärder. Du kan aktivera automatiska svar via Azure Sentinel för att implementera organisationens säkerhetssvar.

Du kan också inta loggar till Azure Sentinel för vidare undersökning.

Konfigurera avancerat skydd mot hot för Azure-databas för MySQL:https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal

Översikt över Azure AD Identity Protection:https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection

Så här visar du riska 2-inloggningar för Azure AD:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Så här går du ombord på Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Övervakning av Azure Security Center**: Inte tillgänglig

**Ansvar**: Kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ge Microsoft åtkomst till relevanta kunddata under supportscenarier

**Vägledning**: Ej tillämpligt. Customer Lockbox stöds ännu inte för Azure Database for MySQL.

Lista över tjänster som stöds av Customer Lockbox:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Säkerhetskontroll: Dataskydd](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Föra en inventering av känslig information

**Vägledning**: Använd taggar för att hjälpa till att spåra Azure Database för MySQL-instanser eller relaterade resurser som lagrar eller bearbetar känslig information.

Så här skapar och använder du taggar:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolera system som lagrar eller bearbetar känslig information

**Vägledning**: Implementera separata prenumerationer och/eller hanteringsgrupper för utveckling, test och produktion. Använd en kombination av privata länk-, tjänstslutpunkter och/eller brandväggsregler för att isolera och begränsa nätverksåtkomsten till dina Azure-databas för MySQL-instanser.

Så här skapar du ytterligare Azure-prenumerationer:https://docs.microsoft.com/azure/billing/billing-create-subscription

Så här skapar du hanteringsgrupper:https://docs.microsoft.com/azure/governance/management-groups/create

Konfigurera privat länk för Azure-databas för MySQL:https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link

Så här skapar och hanterar du slutpunkter för VNet-tjänst och VNet-regler i Azure Database för MySQL:https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

Konfigurerar Azure Database för MySQL-brandväggsregler:https://docs.microsoft.com/azure/mysql/concepts-firewall-rules


**Övervakning av Azure Security Center**: Inte tillgänglig

**Ansvar**: Kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Övervaka och blockera obehörig överföring av känslig information

**Vägd:** När du använder virtuella Azure-datorer för att komma åt Azure Database för MySQL-instanser kan du använda privata länkar, MySQL-nätverkskonfigurationer, nätverkssäkerhetsgrupper och tjänsttaggar för att minska risken för dataexfiltration.

Microsoft hanterar den underliggande infrastrukturen för Azure Database för MySQL och har implementerat strikta kontroller för att förhindra förlust eller exponering av kunddata.

Så här minskar du dataexfiltration för Azure Database for MySQL:https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link#data-exfiltration-prevention

Förstå kunddataskyddet i Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Kryptera all känslig information under transport

**Vägledning**: Azure Database for MySQL stöder anslutning av MySQL-servern till klientprogram med SSL (Secure Sockets Layer). Framtvingande av SSL-anslutningar mellan databasservern och klientprogrammen hjälper till att skydda mot ”man in the middle”-attacker genom att kryptera dataströmmen mellan servern och programmet. I Azure-portalen, se till att "Framtvinga SSL-anslutning" är aktiverat för alla dina Azure-databas för MySQL-instanser som standard.

För närvarande är TLS-versionen som stöds för Azure Database for MySQL TLS 1.0, TLS 1.1, TLS 1.2.

Konfigurera kryptering under överföring för Azure Database för MySQL:https://docs.microsoft.com/azure/mysql/concepts-ssl-connection-security

**Övervakning av Azure Security Center**: Inte tillgänglig

**Ansvar**: Delat

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Använda ett aktivt identifieringsverktyg för att identifiera känsliga data

**Vägledning**: Funktioner för dataidentifiering, klassificering och förlustförebyggande är ännu inte tillgängliga för Azure Database for MySQL. Implementera tredjepartslösning om det behövs för efterlevnadsändamål.

För den underliggande plattformen som hanteras av Microsoft behandlar Microsoft allt kundinnehåll som känsligt och gör stora ansträngningar för att skydda mot förlust av kunddata och exponering. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och underhållit en uppsättning robusta dataskyddskontroller och funktioner.

Förstå kunddataskyddet i Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Övervakning av Azure Security Center**: Inte tillgänglig

**Ansvar**: Delat

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Använd Azure RBAC för att kontrollera åtkomsten till resurser

**Vägledning**: Använd Azure-rollbaserad åtkomstkontroll (RBAC) för att styra åtkomsten till Azure-databasen för MySQL-kontrollplan (t.ex. Azure-portal). För åtkomst till dataplan (i själva databasen) använder du SQL-frågor för att skapa användare och konfigurera användarbehörigheter. RBAC påverkar inte användarbehörigheter i databasen.

Konfigurera RBAC i Azure:https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Konfigurera användaråtkomst med SQL för Azure Database för MySQL:https://docs.microsoft.com/azure/mysql/howto-create-users

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Använd värdbaserad dataförlustprevention för att genomdriva åtkomstkontroll

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

Microsoft hanterar den underliggande infrastrukturen för Azure Database för MySQL och har implementerat strikta kontroller för att förhindra förlust eller exponering av kunddata.

Förstå kunddataskyddet i Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Kryptera känslig information i vila

**Vägledning**: Azure Database for MySQL-tjänsten använder FIPS 140-2-validerad kryptografisk modul för lagringskryptering av data i vila. Data, inklusive säkerhetskopior, krypteras på disk, med undantag för temporära filer som skapas när frågor körs. Tjänsten använder AES 256-bitars chiffer som ingår i Azure-lagringskryptering och nycklarna hanteras. Lagringskrypteringen är alltid igång och kan inte inaktiveras.

Datakryptering med kundhanterade nycklar för Azure Database för MySQL gör att du kan ta med din egen nyckel (BYOK) för dataskydd i vila. För närvarande måste du begära åtkomst för att kunna använda den här funktionen. För att göra det, kontakta:

AskAzureDBforMySQL@service.microsoft.com

Förstå kryptering i vila för Azure Database för MySQL:https://docs.microsoft.com/azure/mysql/concepts-security

Konfigurera kundhanterade nycklar för Azure Database för MySQL:https://docs.microsoft.com/azure/mysql/concepts-data-encryption-mysql

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logga och avisera om ändringar i kritiska Azure-resurser

**Vägledning**: Använd Azure Monitor med Azure Activity Log för att skapa aviseringar för när ändringar sker i produktionsinstanser av Azure Database för MySQL och andra kritiska eller relaterade resurser.

Så här skapar du aviseringar för Azure Activity Log-händelser:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Säkerhetskontroll: Sårbarhetshantering](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Kör automatiserade sårbarhetsskanningsverktyg

**Vägledning**: För närvarande inte tillgänglig; Azure Security Center stöder ännu inte sårbarhetsbedömning för Azure Database för MySQL.

Funktionstäckning för Azure PaaS-tjänster i Azure Security Center:https://docs.microsoft.com/azure/security-center/features-paas

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Distribuera en lösning för hantering av automatisk operativsystemets korrigering

**Vägledning**: Ej tillämpligt. den här riktlinjen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Distribuera automatisk lösning för hantering av programkorrigering från tredje part

**Vägledning**: Ej tillämpligt. den här riktlinjen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Jämför sårbarhetssökningar från rygg mot rygg

**Vägledning**: Ej tillämpligt. den här riktlinjen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Använd en riskklassificeringsprocess för att prioritera reparation av upptäckta sårbarheter

**Vägledning**: Microsoft utför sårbarhetshantering på de underliggande systemen som stöder Azure Database for MySQL.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Använd Identifiering av Azure Asset

**Vägledning**: Använd Azure Resource Graph för att fråga och identifiera alla resurser (inklusive Azure Database for MySQL-instanser) i dina prenumerationer. Se till att du har lämpliga (läs) behörigheter i din klientorganisation och kan räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Så här skapar du frågor med Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Så här visar du dina Azure-prenumerationer:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Förstå Azure RBAC:https://docs.microsoft.com/azure/role-based-access-control/overview

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="62-maintain-asset-metadata"></a>6.2: Underhåll metadata för tillgångar

**Vägledning**: Använda taggar i Azure Database för MySQL-instanser och andra relaterade resurser som ger metadata för att logiskt ordna dem till en taxonomi.

Så här skapar och använder du Taggar:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanteringsgrupper och separata prenumerationer, där så är lämpligt, för att organisera och spåra Azure Database för MySQL-instanser och relaterade resurser. Stämma av lager regelbundet och se till att obehöriga resurser tas bort från prenumerationen i tid.

Så här skapar du ytterligare Azure-prenumerationer:https://docs.microsoft.com/azure/billing/billing-create-subscription

Så här skapar du hanteringsgrupper:https://docs.microsoft.com/azure/governance/management-groups/create

Så här skapar och använder du taggar:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Underhåll en inventering av godkända Azure-resurser och programvarutitlar

**Vägledning**: Ej tillämpligt. Den här rekommendationen är avsedd för beräkningsresurser och Azure som helhet.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

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

**Ansvar**: Ej

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Ta bort icke godkända Azure-resurser och program

**Vägledning**: Ej tillämpligt. Den här rekommendationen är avsedd för beräkningsresurser och Azure som helhet.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej

### <a name="68-use-only-approved-applications"></a>6.8: Använd endast godkända ansökningar

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej

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

**Ansvar**: Ej

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Begränsa användarnas möjlighet att interagera med Azure Resources Manager via skript

**Vägledning**: Använd Azure Villkorlig åtkomst för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "Blockera åtkomst" för "Microsoft Azure Management"-appen. Detta kan förhindra att resurser skapas och ändras i en miljö med hög säkerhet, till exempel instanser av Azure Database för MySQL som innehåller känslig information.

Konfigurera villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Begränsa användarnas möjlighet att köra skript i beräkningsresurser

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fysiskt eller logiskt segregera högriskapplikationer

**Vägledning**: Ej tillämpligt. Den här rekommendationen är avsedd för webbprogram som körs på Azure App Service eller beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Säkerhetskontroll: Säker konfiguration](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: Definiera och implementera standardsäkerhetskonfigurationer för dina Azure-databas för MySQL-instanser med Azure Policy. Använd Azure Policy-alias i namnområdet "Microsoft.DBforMySQL" för att skapa anpassade principer för granskning eller framtvingande av nätverkskonfigurationen för dina Azure-databas för MySQL-instanser. Du kan också använda inbyggda principdefinitioner som är relaterade till dina Azure-databas för MySQL-instanser, till exempel:

Framtvinga SSL-anslutning bör aktiveras för MySQL-databasservrar

Så här visar du tillgängliga Azure-principalias:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Upprätta konfigurationer för säkra operativsystem

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Underhåll säkra Azure-resurskonfigurationer

**Vägledning**: Använd Azure-principen [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar för dina Azure-resurser.

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Förstå Azure-principeffekter:https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Underhåll konfigurationer av säkra operativsystem

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Säkert lagra konfiguration av Azure-resurser

**Vägledning:** Om du använder anpassade Azure-principdefinitioner för dina Azure-databas för MySQL-instanser och relaterade resurser använder du Azure Repos för att lagra och hantera din kod på ett säkert sätt.

Så här lagrar du kod i Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Dokumentation för Azure Repos:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Lagra anpassade operativsystemavbildningar på ett säkert sätt

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Distribuera verktyg för hantering av systemkonfigurationer

**Vägd:** Använd Azure Policy-alias i namnområdet "Microsoft.DBforMySQL" för att skapa anpassade principer för att avisera, granska och framtvinga systemkonfigurationer. Dessutom, utveckla en process och pipeline för att hantera principundantag.

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Distribuera verktyg för systemkonfigurationshantering för operativsystem

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementera automatisk konfigurationsövervakning för Azure-tjänster

**Vägd:** Använd Azure Policy-alias i namnområdet "Microsoft.DBforMySQL" för att skapa anpassade principer för att avisera, granska och framtvinga systemkonfigurationer. Använd Azure-princip [granskning], [neka] och [distribuera om det inte finns] för att automatiskt framtvinga konfigurationer för dina Azure-databas för MySQL-instanser och relaterade resurser.

Konfigurera och hantera Azure-princip:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementera automatisk konfigurationsövervakning för operativsystem

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej

### <a name="711-manage-azure-secrets-securely"></a>7.11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning:** För virtuella Azure-datorer eller webbprogram som körs på Azure App Service som används för att komma åt dina Azure-databas för MySQL-instanser använder du Hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda Azure Database for MySQL hemlig hantering. Kontrollera att Key Vault Soft Delete är aktiverat.

Så här integrerar du med Azure-hanterade identiteter:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Så här skapar du ett nyckelvalv:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Så här tillhandahåller du Key Vault-autentisering med en hanterad identitet:https://docs.microsoft.com/azure/key-vault/managed-identity

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Hantera identiteter på ett säkert och automatiskt sätt

**Vägledning:** Azure Database for MySQL-instans stöder Azure Active Directory-autentisering (i förhandsversion) för åtkomst till databaser.  När du skapar Azure-databasen för MySQL-instansen anger du autentiseringsuppgifter för en administratörsanvändare. Den här administratören kan användas för att skapa ytterligare databasanvändare.  

För virtuella Azure-datorer eller webbprogram som körs på Azure App Service som används för att komma åt dina Azure-databas för MySQL-instanser använder du Hanterad tjänstidentitet tillsammans med Azure Key Vault för att lagra och hämta autentiseringsuppgifter för Azure Database for MySQL Instans. Kontrollera att Key Vault Soft Delete är aktiverat.

Använd hanterade identiteter för att tillhandahålla Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory (AD). Med hanterade identiteter kan du autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan några autentiseringsuppgifter i koden.

Konfigurerar hanterade identiteter:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Så här integrerar du med Azure-hanterade identiteter:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

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

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

Microsoft anti-malware är aktiverat på den underliggande värden som stöder Azure-tjänster (till exempel Azure Database för SQL), men det körs inte på kundinnehåll.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Förskansa filer som ska överföras till Azure-resurser som inte är beräkning

**Vägledning**: Microsoft anti-malware är aktiverat på den underliggande värden som stöder Azure-tjänster (till exempel Azure Database for MySQL), men det körs inte på kundinnehåll.

Förskansa allt innehåll som överförs till azure-resurser som inte är beräkning, till exempel App Service, DataSjölagring, Blob Storage, Azure Database för MySQL osv. Microsoft kan inte komma åt dina data i dessa fall.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Delat

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Se till att programvara och signaturer mot skadlig kod uppdateras

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.

Microsoft anti-malware är aktiverat på den underliggande värden som stöder Azure-tjänster (till exempel Azure Database for MySQL), men det körs inte på kundinnehåll.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Säkerhetskontroll: Dataåterställning](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Se till att regelbundna automatiska säkerhetskopieringar

**Vägledning**: Azure Database for MySQL tar säkerhetskopior av datafilerna och transaktionsloggen. Beroende på den maximala lagringsstorleken som stöds tar vi antingen fullständiga och differentiella säkerhetskopior (4 TB max lagringsservrar) eller säkerhetskopiering av ögonblicksbilder (upp till 16 TB max lagringsservrar). Med dessa säkerhetskopior kan du återställa en server till valfri tidpunkt under den konfigurerade lagringsperioden för säkerhetskopiering. Standardlagringsperioden för säkerhetskopiering är sju dagar. Du kan också konfigurera den upp till 35 dagar. Alla säkerhetskopior krypteras med AES 256-bitars kryptering.

Förstå säkerhetskopior för Azure Database för MySQL:https://docs.microsoft.com/azure/mysql/concepts-backup

Förstå Azure Database för mySQL-första konfiguration:https://docs.microsoft.com/azure/mysql/tutorial-design-database-using-portal

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Delat

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Utför fullständiga systemsäkerhetskopior och säkerhetskopiera alla kundhanterade nycklar

**Vägledning**: Azure Database for MySQL skapar automatiskt säkerhetskopiering av servrar och lagrar dem i antingen lokalt redundant eller geo-redundant lagring, enligt användarens val. Säkerhetskopieringar kan användas för att återställa servern till en vald tidpunkt. Säkerhetskopiering och återställning är en viktig del av en strategi för affärskontinuitet eftersom de skyddar dina data från oavsiktlig skada eller borttagning. 

Om du använder Azure Key Vault för att lagra autentiseringsuppgifter för dina Azure-databas för MySQL-instanser, se till att regelbundna automatiska säkerhetskopior av dina nycklar. 

Förstå säkerhetskopior för Azure Database för MySQL:https://docs.microsoft.com/azure/mysql/howto-restore-server-portal 

Så här säkerhetskopierar du key vault-nycklar:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey


**Övervakning av Azure Security Center**: Ja

**Ansvar**: Delat

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validera alla säkerhetskopior inklusive kundhanterade nycklar

**Vägledning**: I Azure Database for MySQL skapar en återställning en ny server från den ursprungliga serverns säkerhetskopior. Det finns två typer av återställning: Point-in-time-återställning och Geo-återställning. Point-in-time-återställning är tillgänglig med antingen säkerhetskopieringsredundansalternativet och skapar en ny server i samma region som den ursprungliga servern. Geoåterställning är bara tillgängligt om du har konfigurerat servern för geoupptundsskinn och du kan återställa servern till en annan region.

Den beräknade återställningstiden beror på flera faktorer, inklusive databasstorlekar, transaktionsloggstorlek, nätverksbandbredd och det totala antalet databaser som återställs i samma region samtidigt. Återhämtningstiden är vanligtvis mindre än 12 timmar.

Testa regelbundet återställning av azure-databasen för MySQL-instanser.

Förstå säkerhetskopiering och återställning i Azure Database för MySQL:https://docs.microsoft.com/azure/mysql/concepts-backup

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Se till att säkerhetskopior och kundhanterade nycklar skyddas

**Vägledning**: Azure Database for MySQL tar fullständiga, differentiella och transaktionsloggsäkerheter. Med dessa säkerhetskopior kan du återställa en server till valfri tidpunkt under den konfigurerade lagringsperioden för säkerhetskopiering. Standardlagringsperioden för säkerhetskopiering är sju dagar. Du kan också konfigurera den upp till 35 dagar. Alla säkerhetskopior krypteras med AES 256-bitars kryptering. Kontrollera att Key Vault Soft Delete är aktiverat.

Förstå säkerhetskopiering och återställning i Azure Database för MySQL:https://docs.microsoft.com/azure/mysql/concepts-backup

**Övervakning av Azure Security Center**: Ja

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

**Vägledning**: Security Center tilldelar allvarlighetsgrad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som ska undersökas först. Allvarlighetsgraden baseras på hur säker Security Center är i att hitta eller analytiska används för att utfärda aviseringen samt den konfidensnivå som det fanns ont uppsåt bakom aktiviteten som ledde till aviseringen.

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
