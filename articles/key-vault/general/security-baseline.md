---
title: Azure Security Baseline för Nyckelvalv
description: Azure Security Baseline för Nyckelvalv
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 46fa0160dd8b37e89cdd77ba8acdae294fddbefe
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616896"
---
# <a name="azure-security-baseline-for-key-vault"></a>Azure Security Baseline för Nyckelvalv

Azure Security Baseline for Key Vault innehåller rekommendationer som hjälper dig att förbättra säkerhetspositionen för distributionen.

Baslinjen för den här tjänsten hämtas från [Azure Security Benchmark version 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), som ger rekommendationer om hur du kan skydda dina molnlösningar på Azure med vår vägledning om bästa praxis.

Mer information finns i [översikt över Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Säkerhetskontroll: Nätverkssäkerhet](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Skydda resurser med nätverkssäkerhetsgrupper eller Azure-brandväggen i det virtuella nätverket

**Vägledning**: Integrera Azure Key Vault med Azure Private Link. 

Azure Private Link Service kan du komma åt Azure Services (till exempel Azure Key Vault) och Azure värd kund / partner tjänster över en privat slutpunkt i ditt virtuella nätverk.

En Azure Private Endpoint är ett nätverksgränssnitt som ansluter dig privat och säkert till en tjänst som drivs av Azure Private Link. Den privata slutpunkten använder en privat IP-adress från ditt virtuella nätverk, vilket effektivt för in tjänsten i ditt virtuella nätverk. All trafik till tjänsten kan dirigeras via den privata slutpunkten, så inga gateways, NAT-enheter, ExpressRoute- eller VPN-anslutningar eller offentliga IP-adresser behövs. Trafik mellan ditt virtuella nätverk och tjänsten passerar över Microsofts stamnätverk, vilket eliminerar exponering från det offentliga Internet. Du kan ansluta till en instans av en Azure-resurs, vilket ger dig den högsta detaljnivån i åtkomstkontrollen.

Så här integrerar du Key Vault med Azure Private Link:

https://docs.microsoft.com/azure/key-vault/private-link-service


**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Övervaka och logga konfiguration och trafik av virtuella nätverk, undernät och nätverkskort

**Vägledning**: Använd Azure Security Center och följ nätverksskyddsrekommendationerna för att skydda dina Key Vault-konfigurerade resurser i Azure. 

Mer information om nätverkssäkerheten som tillhandahålls av Azure Security Center: 

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="13-protect-critical-web-applications"></a>1.3: Skydda kritiska webbprogram

**Vägledning**: Ej tillämpligt. Den här rekommendationen är avsedd för webbprogram som körs på Azure App Service eller beräkningsresurser.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Aktivera Azure DDoS Protection Standard på Azure Virtual Networks som är associerade med dina Key Vault-instanser för skydd mot distribuerade denial-of-service-attacker. Använd Azure Security Center Integrated Threat Intelligence för att neka kommunikation med kända skadliga eller oanvända IP-adresser på Internet.

 
Hantera Azure DDoS Protection Standard med Azure-portalen:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Hotidentifiering för Azure-tjänstlagret i Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Spela in nätverkspaket och flödesloggar

**Vägledning**: Azure Key Vault använder inte NSG -grupper (Network Security Groups) och flödesloggar för Azure Key Vault fångas inte in. Använd i stället Azure Private Link för att skydda dina Azure Key Vault-instanser och aktivera diagnostikinställningar för att registrera mått och granskningshändelser.

Integrera nyckelvalv med Privat Azure-länk:

https://docs.microsoft.com/azure/key-vault/private-link-service

Azure Key Vault loggning:https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuera nätverksbaserade system för intrångsdetektering/intrångsförebyggande (IDS/IPS)

**Vägledning**: Det här kravet kan uppfyllas genom att konfigurera avancerat hotskydd (ATP) för Azure Key Vault. ATP tillhandahåller ytterligare ett lager av säkerhetsinformation. Det här verktyget identifierar potentiellt skadliga försök att komma åt eller utnyttja Azure Key Vault-konton.

När Azure Security Center identifierar avvikande aktivitet visas aviseringar. Det skickar också e-post till prenumerationsadministratören med information om den misstänkta aktiviteten och rekommendationer för hur du undersöker och åtgärdar de identifierade hoten.

Konfigurera avancerat hotskydd för Azure Key Vault:

https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault



**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Hantera trafik till webbapplikationer

**Vägledning**: Ej tillämpligt. Den här rekommendationen är avsedd för webbprogram som körs på Azure App Service eller beräkningsresurser.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimera komplexiteten och de administrativa omkostnaderna för nätverkssäkerhetsreglerna

**Vägledning:** För resurser som behöver åtkomst till dina Azure Key Vault-instanser använder du Azure-tjänsttaggar för Azure Key Vault för att definiera nätverksåtkomstkontroller i nätverkssäkerhetsgrupper eller Azure-brandväggen. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange tjänsttagnamnet (t.ex. ApiManagement) i lämpligt käll- eller målfält för en regel kan du tillåta eller neka trafik för motsvarande tjänst. Microsoft hanterar adressprefixen som omfattas av servicetag och uppdaterar automatiskt servicetag när adresserna ändras.

Översikt över Azure-tjänsttaggar:https://docs.microsoft.com/azure/virtual-network/service-tags-overview


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Underhåll standardsäkerhetskonfigurationer för nätverksenheter

**Vägledning**: Definiera och implementera standardsäkerhetskonfigurationer för nätverksresurser som är associerade med dina Azure Key Vault-instanser med Azure Policy. Använd Azure Policy-alias i namnområdena "Microsoft.KeyVault" och "Microsoft.Network" för att skapa anpassade principer för granskning eller framtvingande av nätverkskonfigurationen för dina Azure Key Vault-instanser. Du kan också använda inbyggda principdefinitioner relaterade till Azure Key Vault, till exempel:

Key Vault bör använda en slutpunkt för virtuella nätverkstjänster

Självstudiekurs: Skapa och hantera principer för att upprätthålla efterlevnad:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Exempel på Azure-princip:

https://docs.microsoft.com/azure/governance/policy/samples/#networ

Snabbstart: Definiera och tilldela en skiss i portalen:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regler för konfiguration av dokumenttrafik

**Vägledning**: Använd taggar för resurser som är relaterade till nätverkssäkerhet och trafikflöde för dina Azure Key Vault-instanser för att tillhandahålla metadata och logisk organisation.

Använd någon av de inbyggda Azure-principdefinitionerna som är relaterade till taggning, till exempel "Kräv tagg och dess värde" för att säkerställa att alla resurser skapas med taggar och för att meddela dig om befintliga otaggade resurser.

Du kan använda Azure PowerShell eller Azure CLI för att slå upp eller utföra åtgärder på resurser baserat på deras taggar.

Använd taggar för att ordna dina Azure-resurser:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Använd automatiserade verktyg för att övervaka nätverksresurskonfigurationer och identifiera ändringar

**Vägledning**: Använd Azure Activity Log för att övervaka nätverksresurskonfigurationer och identifiera ändringar för nätverksresurser relaterade till dina Azure Key Vault-instanser. Skapa aviseringar i Azure Monitor som utlöses när ändringar av kritiska nätverksresurser sker.

Visa och hämta Azure Activity Log-händelser:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Skapa, visa och hantera aktivitetsloggaviseringar med hjälp av Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [Säkerhetskontroll: Loggning och övervakning](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Använd godkända tidssynkroniseringskällor

**Vägledning**: Ej tillämpligt. Microsoft underhåller tidskällan som används för Azure-resurser, till exempel Azure Key Vault, för tidsstämplar i loggarna.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurera hantering av centrala säkerhetsloggar

**Vägledning:** Ingest loggar via Azure Monitor för att aggregera säkerhetsdata som genereras av Azure Key Vault. I Azure Monitor använder du Azure Log Analytics-arbetsytan för att fråga och utföra analyser och använda Azure Storage-konton för långsiktig/arkiveringslagring. Alternativt kan du aktivera och ombord data till Azure Sentinel eller en tredje part SIEM. 

Azure Key Vault loggning:

https://docs.microsoft.com/azure/key-vault/key-vault-logging

Snabbstart: Så här går du ombord på Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivera granskningsloggning för Azure-resurser

**Vägd:** Aktivera diagnostikinställningar i azure key vault-instanser för åtkomst till gransknings-, säkerhets- och diagnostikloggar. Aktivitetsloggar, som är automatiskt tillgängliga, inkluderar händelsekälla, datum, användare, tidsstämpel, källadresser, måladresser och andra användbara element.

Azure Key Vault loggning:

https://docs.microsoft.com/azure/key-vault/key-vault-logging


**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Samla in säkerhetsloggar från operativsystem

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurera lagring av säkerhetslogglagring

**Vägledning**: Inom Azure Monitor anger du kvarhållningsperioden enligt organisationens efterlevnadsregler för logganalysarbetsytan som används för att lagra dina Azure Key Vault-loggar. Använd Azure Storage-konton för långsiktig/arkiveringslagring.

Ändra lagringsperioden för data:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2.6: Övervaka och granska loggar

**Vägledning**: Analysera och övervaka loggar för avvikande beteende och granska regelbundet resultat för dina Azure Key Vault-skyddade resurser. Använd Azure Monitors Log Analytics-arbetsyta för att granska loggar och utföra frågor om loggdata. Alternativt kan du aktivera och ombord data till Azure Sentinel eller en tredje part SIEM. 

Snabbstart: Ombord Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Kom igång med Logganalys i Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Kom igång med loggfrågor i Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Aktivera aviseringar för avvikande aktivitet

**Vägledning**: Aktivera avancerat skydd mot hot (ATP) för Key Vault i Azure Security Center. Aktivera diagnostikinställningar i Azure Key Vault och skicka loggar till en Log Analytics-arbetsyta. Ombord på din Log Analytics-arbetsyta till Azure Sentinel eftersom den ger en sky-lösning (Security Orchestration Automated Response). Detta gör det möjligt att skapa spelböcker (automatiserade lösningar) för att åtgärda säkerhetsproblem.

Snabbstart: Ombord Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Hantera och svara på säkerhetsaviseringar i Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Svara på händelser med Azure Monitor-aviseringar:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralisera loggning av skadlig kod

**Vägledning**: Ej tillämpligt. Azure Key Vault bearbetar inte eller producerar anti-malware relaterade loggar.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivera DNS-frågeloggning

**Vägledning**: Ej tillämpligt. Azure Key Vault bearbetar eller producerar inte DNS-relaterade loggar.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivera granskningsloggning på kommandoraden

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Säkerhetskontroll: Identitets- och åtkomstkontroll](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Föra en inventering av administrativa räkenskaper

**Vägledning**: Underhåll en inventering av dina Azure Active Directory-registrerade program samt alla användarkonton som har åtkomst till dina Azure Key Vault-nycklar, hemligheter och certifikat. Du kan använda antingen Azure-portalen eller PowerShell för att fråga och stämma av åtkomst till Key Vault. Så här visar du åtkomst i PowerShell:

(Get-AzResource -ResourceId [KeyVaultResourceID]). Egenskaper.AccessPolicies

Registrera ett program med Azure Active Directory:

https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory

Säker åtkomst till ett nyckelvalv:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändra standardlösenord där så är tillämpligt

**Vägledning**: Ej tillämpligt. Azure Key Vault har inte begreppet standardlösenord eftersom autentisering tillhandahålls av Active Directory och skyddas med rollbaserad åtkomstkontroll.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Använd särskilda administrativa konton

**Vägledning**: Skapa standardrutiner kring användningen av dedikerade administrativa konton som har åtkomst till dina Azure Key Vault-instanser. Använd Azure Security Center Identity and Access Management (för närvarande i förhandsversion) för att övervaka antalet aktiva administrativa konton.

Övervaka identitet och åtkomst (förhandsgranskning):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Använd enkel inloggning (SSO) med Azure Active Directory

**Vägledning**: Använd ett Azure-tjänsthuvudnamn tillsammans med AppId, TenantID och ClientSecret för att sömlöst autentisera ditt program och hämta token som ska användas för att komma åt dina Azure Key Vault-hemligheter.

Tjänst-till-tjänst-autentisering till Azure Key Vault med .NET:

https://docs.microsoft.com/azure/key-vault/service-to-service-authentication



**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Använda multifaktorautentisering för all Azure Active Directory-baserad åtkomst

**Vägledning:** Aktivera Azure Active Directory MultiFaktor-autentisering och följ Azure Security Center Identity and Access Management (för närvarande i förhandsversion) för att skydda dina Event Hub-aktiverade resurser.

Planera en molnbaserad Azure Multi-Factor-autentiseringsdistribution:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Övervaka identitet och åtkomst (förhandsgranskning):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Använd dedikerade datorer (arbetsstationer för privilegierad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd en PAW (Privileged Access Workstation) med Azure Multi-Factor Authentication (MFA) konfigurerad för att logga in på och konfigurera Key Vault-aktiverade resurser. 

Arbetsstationer för privilegierad åtkomst:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations 

Planera en molnbaserad Azure Multi-Factor-autentiseringsdistribution:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted



**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Logga och varna för misstänkt aktivitet från administrativa konton

**Vägledning**: Använd Azure Active Directory (AAD) Privilegierad identitetshantering (PIM) för generering av loggar och aviseringar när misstänkt eller osäker aktivitet inträffar i miljön. Använd AAD-riskidentifieringar för att visa aviseringar och rapporter om riskfyllda användarbeteenden. Om du vill logga ytterligare skickar du riskidentifieringsaviseringar för Azure Security Center i Azure Monitor och konfigurerar anpassade aviseringar/meddelanden med hjälp av åtgärdsgrupper.

Aktivera avancerat skydd mot hot (ATP) för Azure Key Vault för att generera aviseringar för misstänkt aktivitet.

Distribuera Azure AD-privilegierad identitetshantering (PIM):https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Konfigurera avancerat hotskydd för Azure Key Vault (förhandsversion):https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault

Aviseringar för Azure Key Vault (förhandsversion):https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv

Azure Active Directory-riskidentifieringar:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Skapa och hantera åtgärdsgrupper i Azure-portalen:https://docs.microsoft.com/azure/azure-monitor/platform/action-groups



**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Hantera Azure-resurser från endast godkända platser

**Vägledning**: Konfigurera platsvillkoret för en princip för villkorlig åtkomst och hantera dina namngivna platser. Med namngivna platser kan du skapa logiska grupperingar av IP-adressintervall eller länder och regioner. Du kan begränsa åtkomsten till känsliga resurser, till exempel dina Key Vault-hemligheter, till dina konfigurerade namngivna platser.

Vad är platsvillkoret i Azure Active Directory Villkorlig åtkomst?:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations



**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="39-use-azure-active-directory"></a>3.9: Använd Azure Active Directory

**Vägledning**: Använd Azure Active Directory (AAD) som det centrala autentiserings- och auktoriseringssystemet för Azure-resurser som Key Vault. Detta gör det möjligt för rollbaserad åtkomstkontroll (RBAC) att administrera känsliga resurser.

 

Snabbstart: Skapa en ny klient i Azure Active Directory:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelbundet granska och förena användaråtkomst

**Vägledning**: Granska Azure Active Directory (AAD) loggar för att identifiera inaktuella konton med Azure Key Vault administrativa roller. Dessutom kan du använda AAD-åtkomstgranskningar för att effektivt hantera gruppmedlemskap, åtkomst till företagsprogram som kan användas för att komma åt Azure Key Vault och rolltilldelningar. Användaråtkomst bör granskas regelbundet, till exempel var 90:e dag för att se till att endast rätt användare har fortsatt åtkomst.

Azure Active Directory-rapporter och övervakningsdokumentation:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Vad är Azure AD-åtkomstgranskningar?:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Övervaka försök att komma åt inaktiverade konton

**Vägledning**: Aktivera diagnostikinställningar för Azure Key Vault och Azure Active Directory och skicka alla loggar till en Log Analytics-arbetsyta. Konfigurera önskade aviseringar (till exempel försök att komma åt inaktiverade hemligheter) i Log Analytics.

Integrera Azure AD-loggar med Azure Monitor-loggar:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Migrera från den gamla Key Vault-lösningen:https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution



**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Varning på kontots avvikelse för inloggningsbeteende

**Vägledning**: Använd Azure Active Directorys funktioner för identitetsskydd och riskidentifiering för att konfigurera automatiska svar på identifierade misstänkta åtgärder relaterade till dina azure key vault-skyddade resurser. Du bör aktivera automatiska svar via Azure Sentinel för att implementera organisationens säkerhetssvar. 

Riskarisk inloggningsrapport i Azure Active Directory-portalen:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins 

Så här konfigurerar och aktiverar du riskprinciper:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Så här går du ombord på Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ge Microsoft åtkomst till relevanta kunddata under supportscenarier

**Vägledning**: Ej tillämpligt. Customer Lockbox stöds inte för Azure Key Vault.

Tjänster och scenarier som stöds i allmän tillgänglighet:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Säkerhetskontroll: Dataskydd](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Föra en inventering av känslig information

**Vägledning**: Använd taggar för att hjälpa till att spåra Azure-resurser som lagrar eller bearbetar känslig information på Azure Key Vault-aktiverade resurser. 

Använd taggar för att ordna dina Azure-resurser:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolera system som lagrar eller bearbetar känslig information

**Vägledning**: Du kan skydda åtkomsten till Azure Key Vault genom att använda slutpunkter för virtuella nätverkstjänster som konfigurerats för att begränsa åtkomsten till specifika undernät.

När brandväggsregler har gällande kan du bara utföra Azure Key Vault-dataplansåtgärder när din begäran kommer från tillåtna undernät eller IP-adressintervall. Detta gäller även för Azure Key Vault-åtkomst i Azure-portalen. Även om du kan bläddra till ett nyckelvalv från Azure-portalen kanske du inte kan lista nycklar, hemligheter eller certifikat om klientdatorn inte finns med i listan över tillåtna. Detta påverkar också Azure Key Vault Picker och andra Azure-tjänster. Du kanske kan se listor över nyckelvalv, men inte listnycklar, om brandväggsregler hindrar klientdatorn från att göra det.

Konfigurera Azure Key Vault-brandväggar och virtuella nätverk:https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Slutpunkter för virtuella nätverkstjänster för Azure Key Vault:https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints



**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Övervaka och blockera obehörig överföring av känslig information

**Vägledning**: Alla data som lagras i Azure Key Vault anses vara känsliga. Använd Azure Key Vault-dataplanåtkomstkontroller för att styra åtkomsten till Azure Key Vault-hemligheter. Du kan också använda Key Vaults inbyggda brandvägg för att styra åtkomsten i nätverkslagret. Om du vill övervaka åtkomsten till Azure Key Vault aktiverar du diagnostikinställningar för Key Vault och skickar loggar till ett Azure Storage-konto eller Log Analytics-arbetsyta.

Säker åtkomst till ett nyckelvalv:https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault

Konfigurera Azure Key Vault-brandväggar och virtuella nätverk:https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Azure Key Vault loggning:https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Kryptera all känslig information under transport

**Vägledning**: All trafik till Azure Key Vault för autentisering, hantering och åtkomst till dataplan krypteras och överskrids av HTTPS: port 443. (Det kommer dock ibland att finnas HTTP [port 80] trafik för CRL.) 

Öppna Azure Key Vault bakom en brandvägg:https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall



**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Använda ett aktivt identifieringsverktyg för att identifiera känsliga data

**Vägledning**: Ej tillämpligt. Alla data i Azure Key Vault (hemligheter, nycklar och certifikat) anses vara känsliga.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Använd Azure RBAC för att kontrollera åtkomsten till resurser

**Vägledning**: Säker åtkomst till hanterings- och dataplanet för dina Azure Key Vault-instanser.

Säker åtkomst till ett nyckelvalv:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Övervakning av Azure Security Center:** Ej tillämpligt

**Ansvar**: Kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Använd värdbaserad dataförlustprevention för att genomdriva åtkomstkontroll

**Vägledning**: Microsoft hanterar den underliggande infrastrukturen för Azure Key Vault och har implementerat strikta kontroller för att förhindra förlust eller exponering av kunddata.

Vad är Azure Key Vault?

https://docs.microsoft.com/azure/key-vault/key-vault-overview

Azure-kunddataskydd:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Kryptera känslig information i vila

**Vägledning**: Alla hanterade objekt (nyckel, certifikat och hemligheter) krypteras i vila i Azure Key Vault.

Dokumentationsunderlag:

- [Krypteringsmodell och nyckelhanteringstabell](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#encryption-model-and-key-management-table)


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logga och avisera om ändringar i kritiska Azure-resurser

**Vägledning**: Använd Azure Key Vault Analytics-lösningen i Azure Monitor för att granska granskningshändelseloggar för Azure Key Vault.

Azure Key Vault Analytics-lösning i Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault



**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Säkerhetskontroll: Sårbarhetshantering](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Kör automatiserade sårbarhetsskanningsverktyg

**Vägledning**: Microsoft utför sårbarhetshantering på underliggande system som stöder Azure Key Vault.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Distribuera en lösning för hantering av automatisk operativsystemets korrigering

**Vägledning**: Saknas. Microsoft utför korrigeringshantering på de underliggande system som stöder Key Vault.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Distribuera automatisk lösning för hantering av programkorrigering från tredje part

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Jämför sårbarhetssökningar från rygg mot rygg

**Vägledning**: Microsoft utför sårbarhetshantering på de underliggande system som stöder Key Vault.

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Använd en riskklassificeringsprocess för att prioritera reparation av upptäckta sårbarheter

**Vägledning**: Använd standardriskklassificeringarna (Secure Score) som tillhandahålls av Azure Security Center.

Förbättra din säkra poäng i Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Använd Identifiering av Azure Asset

**Vägledning**: Använd Azure Resource Graph för att fråga och identifiera alla resurser (inklusive Azure Key Vault-instanser) i din prenumeration. Se till att du har lämpliga (läs) behörigheter i din klientorganisation och kan räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Snabbstart: Kör din första Resource Graph-fråga med Utforskaren för Azure Resource Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Hämta prenumerationer som det aktuella kontot kan komma åt.:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Vad är rollbaserad åtkomstkontroll (RBAC) för Azure-resurser?

https://docs.microsoft.com/azure/role-based-access-control/overview

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="62-maintain-asset-metadata"></a>6.2: Underhåll metadata för tillgångar

**Vägledning**: Använd taggar på Azure Key Vault-resurser som ger metadata för att logiskt ordna dem till en taxonomi.

Så här skapar och använder du Taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanteringsgrupper och separata prenumerationer, där så är lämpligt, för att organisera och spåra Azure Key Vault-instanser och relaterade resurser. Stämma av lager regelbundet och se till att obehöriga resurser tas bort från prenumerationen i tid.

Skapa ytterligare en Azure-prenumeration:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Skapa hanteringsgrupper för resursorganisation och resurshantering:

https://docs.microsoft.com/azure/governance/management-groups/create

Använd taggar för att ordna dina Azure-resurser:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Underhåll en inventering av godkända Azure-resurser och programvarutitlar

**Vägledning**: Definiera lista över godkända Azure-resurser och godkänd programvara för dina beräkningsresurser

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Övervaka för icke godkända Azure-resurser

**Vägledning**: Använd Azure-principer för att begränsa vilken typ av resurser som kan skapas i kundprenumerationer med hjälp av följande inbyggda principdefinitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

Dessutom kan du använda Azure Resource Graph för att fråga/identifiera resurser inom prenumerationerna.

Självstudiekurs: Skapa och hantera principer för att upprätthålla efterlevnad:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Snabbstart: Kör din första Resource Graph-fråga med Utforskaren för Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Övervaka icke godkända program i beräkningsresurser

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Ta bort icke godkända Azure-resurser och program

**Vägledning**: Ej tillämpligt. Den här rekommendationen är avsedd för Azure som helhet samt beräkningsresurser.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="68-use-only-approved-applications"></a>6.8: Använd endast godkända ansökningar

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="69-use-only-approved-azure-services"></a>6.9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure-principer för att begränsa vilken typ av resurser som kan skapas i kundprenumerationer med hjälp av följande inbyggda principdefinitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

Självstudiekurs: Skapa och hantera principer för att upprätthålla efterlevnad:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Exempel på Azure-princip:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="610-implement-approved-application-list"></a>6.10: Genomföra godkänd ansökningslista

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: Begränsa användarnas möjlighet att interagera med AzureResources Manager via skript

**Vägledning**: Använd Azure Villkorlig åtkomst för att begränsa användarnas möjlighet att interagera med Azure Resource Manager (ARM) genom att konfigurera "Blockera åtkomst" för "Microsoft Azure Management"-appen. Detta kan förhindra att resurser skapas och ändras i en miljö med hög säkerhet, till exempel de med konfigurationen av Key Vault.

Hantera åtkomst till Azure-hantering med villkorlig åtkomst:

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

**Vägd:** Använd Azure-principalias i namnområdet "Microsoft.KeyVault" för att skapa anpassade principer för granskning eller framtvingande av konfigurationen av dina Azure Key Vault-instanser. Du kan också använda inbyggda Azure-principdefinitioner för Azure Key Vault, till exempel:

Key Vault-objekt ska kunna återställas

Distribuera diagnostikinställningar för Key Vault till Log Analytics-arbetsytan

Diagnostikloggar i Key Vault ska aktiveras

Key Vault bör använda en slutpunkt för virtuella nätverkstjänster

Distribuera diagnostikinställningar för Nyckelvalv till eventhubb

Använd rekommendationer från Azure Security Center som en säker konfigurationsbaslinje för dina Azure Key Vault-instanser.

Så här visar du tillgängliga Azure-principalias:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Självstudiekurs: Skapa och hantera principer för att upprätthålla efterlevnad:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Upprätta konfigurationer för säkra operativsystem

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Underhåll säkra Azure-resurskonfigurationer

**Vägledning**: Använd Azure-principen [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar i dina Azure Key Vault-aktiverade resurser. 

Självstudiekurs: Skapa och hantera principer för att upprätthålla efterlevnad:

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

**Vägledning:** Om du använder anpassade Azure-principdefinitioner för dina Azure Key Vault-aktiverade resurser använder du Azure Repos för att lagra och hantera din kod på ett säkert sätt.

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

**Vägd:** Använd Azure Policy-alias i namnområdet "Microsoft.KeyVault" för att skapa anpassade principer för aviseringar, granskning och genomdämning av systemkonfigurationer. Dessutom, utveckla en process och pipeline för att hantera principundantag.

Konfigurera och hantera Azure-princip:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Distribuera verktyg för systemkonfigurationshantering för operativsystem

**Vägledning**: Ej tillämpligt. den här rekommendationen är avsedd för beräkningsresurser.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementera automatisk konfigurationsövervakning för Azure-tjänster

**Vägledning**: Använda Azure Security Center för att utföra baslinjesökningar för dina Azure Key Vault-skyddade resurser 

  

Åtgärda rekommendationer i Azure Security Center: 

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementera automatisk konfigurationsövervakning för operativsystem

**Vägledning**: Ej tillämpligt. detta riktmärke är avsett för beräkningsresurser.


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Ej tillämpligt

### <a name="711-manage-azure-secrets-securely"></a>7.11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: Använd hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda hemlig hantering för dina molnprogram. Kontrollera att Azure Key Vault mjuk borttagning är aktiverat.

Så här integrerar du med Azure-hanterade identiteter:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Så här skapar du ett nyckelvalv:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

Så här tillhandahåller du Key Vault-autentisering med en hanterad identitet: 

https://docs.microsoft.com/azure/key-vault/managed-identity

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Hantera identiteter på ett säkert och automatiskt sätt

**Vägledning**: Använd hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda hemlig hantering för dina molnprogram. 

  

Så här integrerar du med Azure-hanterade identiteter: 

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity  

Så här skapar du ett nyckelvalv: 

https://docs.microsoft.com/azure/key-vault/quick-create-portal    

Så här tillhandahåller du Key Vault-autentisering med en hanterad identitet:  
https://docs.microsoft.com/azure/key-vault/managed-identity

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

**Vägledning:** Microsoft anti-malware är aktiverat på den underliggande värden som stöder Azure-tjänster (till exempel Azure Key Vault), men det körs inte på kundinnehåll.

Förskansa allt innehåll som överförs eller skickas till azure-resurser som inte är beräkningsresurser, till exempel Azure Key Vault. Microsoft kan inte komma åt dina data i dessa fall.

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

**Vägledning**: Se till att regelbundet automatiska säkerhetskopior av dina Key Vault-certifikat, nycklar, hanterade lagringskonton och hemligheter, med följande PowerShell-kommandon:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Du kan också lagra säkerhetskopior av Key Vault i Azure Backup.

Så här säkerhetskopierar du key vault-certifikat:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Så här säkerhetskopierar du key vault-nycklar:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Så här säkerhetskopierar du hanterade lagringskonton för Key Vault:https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Så här säkerhetskopierar du Key Vault Secrets:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Aktivera Azure Backup:https://docs.microsoft.com/azure/backup



**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Utför fullständiga systemsäkerhetskopior och säkerhetskopiera alla kundhanterade nycklar

**Vägledning**: Utför säkerhetskopior av certifikat, nycklar, hanterade lagringskonton och hemligheter med följande PowerShell-kommandon:

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Backup-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

Du kan också lagra säkerhetskopior av Key Vault i Azure Backup.

Så här säkerhetskopierar du key vault-certifikat:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Så här säkerhetskopierar du key vault-nycklar:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Så här säkerhetskopierar du hanterade lagringskonton för Key Vault:https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Så här säkerhetskopierar du Key Vault Secrets:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Aktivera Azure Backup:https://docs.microsoft.com/azure/backup



**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Validera alla säkerhetskopior inklusive kundhanterade nycklar

**Vägledning**: Utför regelbundet dataåterställning av dina Key Vault-certifikat, nycklar, hanterade lagringskonton och hemligheter, med följande PowerShell-kommandon:

- Återställ-AzKeyVaultCertificate

- Återställ-AzKeyVaultKey

- Restore-AzKeyVaultManagedStorageAccount

- Återställ-AzKeyVaultSecret

Så här återställer du certifikat för nyckelvalv:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0

Så här återställer du nycklar för nyckelvalv:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0 

Så här återställer du Key Vault-hanterade lagringskonton:https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount

Så här återställer du Key Vault Secrets:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0


**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Se till att säkerhetskopior och kundhanterade nycklar skyddas

**Vägledning**: Se till att mjuk borttagning är aktiverat för Azure Key Vault. Med mjuk borttagning kan du återställa borttagna nyckelvalv och valvobjekt som nycklar, hemligheter och certifikat. 

Så här använder du Azure Key Vault's Soft Delete: 

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Säkerhetskontroll: Incident response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Skapa en vägledning för incidenthantering

**Vägledning**: Skapa en incidenthanteringsguide för din organisation. Se till att det finns skriftliga incidenthanteringsplaner som definierar alla roller för personal samt faser av incidenthantering/hantering från identifiering till granskning efter incidenten. Dessa processer bör ha fokus på att skydda känsliga system, till exempel de som använder Key Vault hemligheter.

Konfigurera arbetsflödesautomatiseringar i Azure Security Center: 

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide   

Vägledning om hur du bygger din egen process för hantering av säkerhetsincidenter:  

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Microsoft Security Response Center's Anatomy of an Incident:   

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process   

Kunden kan också utnyttja NIST: s Computer Security Incident Handling Guide till stöd i skapandet av sin egen incidentresponsplan: 

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Skapa en incidentbedömning och prioriteringsprocedur

**Vägledning**: Security Center tilldelar allvarlighetsgrad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som ska undersökas först. Allvarlighetsgraden baseras på hur säker Security Center är i att hitta eller analytiska används för att utfärda aviseringen samt den konfidensnivå som det fanns ont uppsåt bakom aktiviteten som ledde till aviseringen. Dessutom tydligt markera abonnemang (för ex. produktion, icke-prod) och skapa ett namngivningssystem för att tydligt identifiera och kategorisera Azure-resurser, särskilt de som bearbetar känsliga data som Azure Key Vault hemligheter.


**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="103-test-security-response-procedures"></a>10.3: Testsäkerhetssvarsprocedurer

**Vägledning**: Utför övningar för att testa systemens funktioner för incidentsvar på en regelbunden kadens för att skydda dina Azure Key Vault-instanser och relaterade resurser. Identifiera svaga punkter och luckor och revidera planen efter behov.

Se NIST:s publikation: Guide to Test, Training och Exercise Programs for IT Plans and Capabilities: 

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Ange kontaktinformation för säkerhetsincidenter och konfigurera varningsmeddelanden för säkerhetsincidenter

**Vägledning**: Kontaktinformation för säkerhetsincidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en olaglig eller obehörig part.  Granska incidenter i efterhand för att säkerställa att problemen löses.

Så här ställer du in säkerhetskontakten för Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Övervakning av Azure Security Center**: Ja

**Ansvar**: Kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Införliva säkerhetsvarningar i ditt incidenthanteringssystem

**Vägledning**: Exportera dina Azure Security Center-aviseringar och rekommendationer med hjälp av funktionen Kontinuerlig export för att identifiera risker för Azure Key Vault-aktiverade resurser. Kontinuerlig export gör att du kan exportera aviseringar och rekommendationer antingen manuellt eller på ett kontinuerligt sätt.  Du kan använda Azure Security Center-dataanslutningen för att strömma aviseringarna till Azure Sentinel. 

 

Konfigurera kontinuerlig export: 

https://docs.microsoft.com/azure/security-center/continuous-export 

  

Så här streamar du aviseringar till Azure Sentinel: 

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisera svaret på säkerhetsvarningar

**Vägledning**: Använd funktionen Automatisering av arbetsflöde i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" på säkerhetsaviseringar och rekommendationer för att skydda dina Azure Key Vault-skyddade resurser. 

 

Konfigurerar automatiserings- och logikappar för arbetsflöde: 

https://docs.microsoft.com/azure/security-center/workflow-automation

**Övervakning av Azure Security Center**: För närvarande inte tillgänglig

**Ansvar**: Kund

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [Säkerhetskontroll: Penetrationstester och röda teamövningar](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Utför regelbundna penetrationstester av dina Azure-resurser och säkerställer reparation av alla kritiska säkerhetsresultat inom 60 dagar

**Vägledning**: Du får inte utföra penntestning på Azure Key Vault-tjänsten direkt, men det uppmuntras att testa dina Azure-resurser som använder Key Vault för att säkerställa säkerheten för hemligheterna.

Du måste följa Microsofts insatsregler för att säkerställa att dina penetrationstester inte bryter mot Microsofts policyer:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Du hittar mer information om Microsofts strategi och genomförande av Red Teaming och penetrationstester på webbplatser på flera webbplatser mot Microsoft-hanterad molninfrastruktur, molntjänster och -program här: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Övervakning av Azure Security Center**: Ej tillämpligt

**Ansvar**: Delat

## <a name="next-steps"></a>Nästa steg

- Se [Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Läs mer om [Azure Security-originalplaner](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
