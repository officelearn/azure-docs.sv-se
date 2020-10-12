---
title: Azures säkerhets bas linje för batch
description: Azures säkerhets bas linje för batch
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 1eb24871817f365efe58b8e687563727df74493c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89400984"
---
# <a name="azure-security-baseline-for-batch"></a>Azures säkerhets bas linje för batch

Azures säkerhets bas linje för batch innehåller rekommendationer som hjälper dig att förbättra säkerhets position för din distribution.

Bas linjen för den här tjänsten hämtas från [Azures prestandatest version 1,0](../security/benchmarks/overview.md), som ger rekommendationer om hur du kan skydda dina moln lösningar i Azure med våra bästa praxis rikt linjer.

Mer information finns i [Översikt över Azure Security-bas linjer](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets kontroll: nätverks säkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: skydda resurser med hjälp av nätverks säkerhets grupper eller Azure-brandvägg på din Virtual Network

**Vägledning**: Distribuera Azure Batch pool (er) i det virtuella nätverket. Om du vill tillåta att datornoder kommunicerar säkert med andra virtuella datorer eller med ett lokalt nätverk kan du etablera poolen i ett undernät i ett virtuellt Azure-nätverk. Genom att distribuera poolen i ett virtuellt nätverk får du också kontroll över nätverks säkerhets gruppen (NSG) som används för att skydda enskilda noders nätverks gränssnitt (NIC), samt under nätet. Konfigurera NSG så att trafik från endast betrodda IP-/locations på Internet tillåts.

Så här skapar du en Azure Batch pool i en Virtual Network:

https://docs.microsoft.com/azure/batch/batch-virtual-network

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverkskort

**Vägledning**: Använd Azure Security Center och åtgärda nätverks skydds rekommendationer som är relaterade till den virtuella nätverks-/nätverks säkerhets gruppen (NSG) som är associerad med din batch-pool. Aktivera flödes loggar på NSG som används för att skydda batch-poolen och skicka loggar till ett Azure Storage konto för trafik granskning. Du kan också skicka NSG Flow-loggar till en Azure Log Analytics-arbetsyta och använda Azure Trafikanalys för att ge insikter om trafikflöde i Azure-molnet. Några av fördelarna med Azure Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks fel konfigurationer.

Så här aktiverar du NSG Flow-loggar:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Så här aktiverar och använder du Trafikanalys:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Förstå nätverks säkerhet som tillhandahålls av Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Aktivera standard skydd för Azure-DDoS (distributed denial-of-Service) på det virtuella nätverket som skyddar Azure Batch-poolen för skydd mot DDoS-attacker. Använd Azure Security Center integrerad Hot information för att neka kommunikation med kända skadliga eller oanvända Internet-IP-adresser.

Så här konfigurerar du DDoS-skydd:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Förstå Azure Security Center integrerad Hot information:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: registrera nätverks paket och flödes loggar

**Vägledning**: Aktivera flödes loggar i nätverks säkerhets gruppen (NSG) som används för att skydda Azure Batch-poolen och skicka loggar till ett Azure Storage konto för trafik granskning.

Så här aktiverar du NSG Flow-loggar:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1,6: Distribuera Network-baserad intrångs identifiering/system för skydd mot intrång

**Vägledning**: om det behövs för kompatibilitet väljer du en virtuell nätverks installation från Azure Marketplace som stöder intrångs identifierings system (ID) och IP-funktioner (intrångs skydd system) med funktioner för nytto lasts granskning.

Om intrångs identifiering och/eller skydd som baseras på nytto lasts granskning inte är ett krav kan du använda Azure-brandväggen med hot information. Azure Firewall Threat Intelligence-baserad filtrering kan varna och neka trafik till och från kända skadliga IP-adresser och domäner. IP-adresserna och domänerna hämtas från Microsoft Threat Intelligence-flödet.

Distribuera Azure-brandväggen med en offentlig IP-adress i samma virtuella nätverk som Azure Batch pool-noderna. Konfigurera Network Address Translation (NAT) regler mellan betrodda platser på Internet och de privata IP-adresserna för dina noder i poolen. I Azure-brandväggen, under hot information, konfigurerar du "varning och neka" för att blockera aviseringar och blockera trafik till/från kända skadliga IP-adresser och domäner. IP-adresserna och domänerna kommer från Microsoft Threat Intelligence-flödet och endast de högsta konfidens posterna ingår. 

Så här skapar du en Azure Batch pool i en Virtual Network:

https://docs.microsoft.com/azure/batch/batch-virtual-network

Så här distribuerar du Azure-brand väggen:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="17-manage-traffic-to-your-web-applications"></a>1,7: hantera trafik till dina webb program

**Vägledning**: inte tillämpligt, Benchmark är avsett för webb program som körs på Azure App Service-eller IaaS-instanser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: Använd taggar för virtuella nätverks tjänster för att definiera nätverks åtkomst kontroller för nätverks säkerhets grupper eller Azure-brandväggar som är associerade med Azure Batch pool (er). Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange service tag-namnet (t. ex. API Management) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

Förstå och använda service märken:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: definiera och implementera standardkonfigurationer för nätverks resurser som är kopplade till Azure Batch pool (er) med Azure policy. Använd Azure Policy alias i namn områdena "Microsoft.BatCH" och "Microsoft. Network" om du vill skapa anpassade principer för granskning eller framtvinga nätverks konfigurationen för dina Azure Batch-pooler.

Så här konfigurerar och hanterar du Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: Använd taggar för nätverks tjänst grupper (NSG: er) och andra resurser som rör nätverks säkerhets-och trafikflöden som är associerade med dina Azure Batch-pooler. För enskilda NSG-regler använder du fältet Beskrivning för att ange affärs behov och/eller varaktighet (osv.) för alla regler som tillåter trafik till/från ett nätverk.

Använd någon av de inbyggda Azure Policy definitionerna som är relaterade till taggning, till exempel "Kräv tagg och dess värde" för att säkerställa att alla resurser skapas med taggar och meddela dig om befintliga otaggade resurser.

Du kan använda Azure PowerShell eller Azure CLI för att söka efter eller utföra åtgärder på resurser baserat på deras taggar.

Skapa och använda Taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Så här skapar du ett virtuellt nätverk:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Så här skapar du en NSG:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka konfigurationer av nätverks resurser och identifiera ändringar för nätverks resurser som är relaterade till Azure Batch pooler. Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska nätverks resurser sker.

Så här visar och hämtar du Azure aktivitets logg händelser: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view 

Så här skapar du aviseringar i Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets kontroll: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Använd godkända tids källor för synkronisering

**Vägledning**: för Azure Batch är Microsoft som standard tidssynkronisering. Men om du har särskilda krav för tidssynkronisering kan du implementera dessa ändringar.

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: onboard Azure Batch-konto som ska Azure Monitors för att samla in säkerhets data som genereras av kluster enheterna. Använd anpassade frågor för att identifiera och svara på hot i miljön.  För Azure Batch övervakning på resurs nivå använder du batch-API: erna för att övervaka eller fråga efter status för dina resurser, inklusive jobb, aktiviteter, noder och pooler.

Så här registrerar du ett Azure Batch-konto för att Azure Monitor:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: om du vill övervaka Azure Batch på konto nivå övervakar du varje batch-konto med hjälp av funktionerna i Azure Monitor. Azure Monitor samlar in Mät värden och eventuella diagnostiska loggar för resurser som omfattas på samma nivå som ett batch-konto, till exempel pooler, jobb och uppgifter. Samla in och Använd dessa data manuellt eller program mässigt för att övervaka aktiviteter i ditt batch-konto och för att diagnosticera problem.

För övervakning av Azure Batch på resurs nivå använder du Azure Batch-API: er för att övervaka eller fråga efter status för dina resurser, inklusive jobb, aktiviteter, noder och pooler.

Så här konfigurerar du Azure Batch övervakning och loggning på konto nivå:

https://docs.microsoft.com/azure/batch/monitoring-overview

Förstå batch-övervakning på resurs nivå:

https://docs.microsoft.com/azure/batch/monitoring-overview#batch-resource-monitoring

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="24-collect-security-logs-from-operating-system"></a>2,4: samla in säkerhets loggar från operativ systemet

**Vägledning**: Azure Monitor samlar in Mät värden och diagnostikloggar för resurser i ditt Azure Batch-konto. Samla in och använda dessa data på flera olika sätt för att övervaka ditt Azure Batch-konto och diagnostisera problem. Du kan också konfigurera mått varningar så att du får meddelanden när ett mått når ett angivet värde.

Om det behövs kan du ansluta till dina noder för enskilda pooler via SSH (Secure Shell) eller Remote Desktop Protocol (RDP) för att få åtkomst till lokala operativ system loggar.

Så här samlar du in diagnostikloggar från ditt Azure Batch-konto:

https://docs.microsoft.com/azure/batch/batch-diagnostics#batch-diagnostics

Så här ansluter du till Azure Batch pool-noder:

https://docs.microsoft.com/azure/batch/batch-api-basics#error-handling

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: publicera Azure Batch konto till Azure Monitor. Kontrol lera att den Azure Log Analytics-arbetsyta som används har den logg kvarhållningsperiod som angetts enligt organisationens regler för efterlevnad

Så här konfigurerar du Azure Batch övervakning och loggning:

https://docs.microsoft.com/azure/batch/monitoring-overview

Så här konfigurerar du lagrings perioden för Azure Log Analytics Workspace:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: Skapa Azure Batch mått varningar som utlöses när värdet för ett angivet mått överskrider ett visst tröskelvärde.

Så här konfigurerar du Azure Batch mått aviseringar:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: aktivera aviseringar för avvikande aktivitet

**Vägledning**: Skapa Azure Batch mått varningar som utlöses när värdet för ett angivet mått överskrider ett visst tröskelvärde.

Så här konfigurerar du Azure Batch mått aviseringar:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod

**Vägledning**: Använd Windows Defender på dina individuella batch-noder när det gäller Windows-operativsystem eller ange en egen lösning för program mot skadlig kod om du använder Linux.

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="29-enable-dns-query-logging"></a>2,9: Aktivera loggning av DNS-frågor

**Vägledning**: implementera en lösning från tredje part för DNS-loggning

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="210-enable-command-line-audit-logging"></a>2,10: Aktivera loggning av kommando rads granskning

**Vägledning**: Konfigurera konsol loggning och PowerShell-avskrift manuellt på varje nod-basis.

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [säkerhets kontroll: identitets-och Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3,1: underhåll inventering av administrativa konton

**Vägledning**: underhålla posten för det lokala administratörs kontot som skapas vid etablering av Azure Batch-poolen samt andra konton som du skapar. Om Azure Active Directory-integrering (AAD) används, innehåller AAD dessutom inbyggda roller som måste tilldelas explicit och som därför kan frågas. Använd AAD PowerShell-modulen för att utföra adhoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

Dessutom kan du använda rekommendationer för Azure Security Center identitets-och åtkomst hantering.

Så här hämtar du en katalog roll i AAD med PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Så här hämtar du medlemmar i en katalog roll i AAD med PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Så här övervakar du identitet och åtkomst med Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: när du konfigurerar en Azure Batch pool får du möjlighet att skapa lokala dator konton. Det finns inga standard lösen ord att ändra, men du kan ange olika lösen ord för SSH (Secure Shell) och Remote Desktop Protocol (RDP)-åtkomst. När Azure Batch poolen har kon figurer ATS kan du generera en slumpmässig användare för enskilda noder i Azure Portal eller via Azure Resource Manager API.

Så här lägger du till en användare till en speciell Compute-nod:

https://docs.microsoft.com/rest/api/batchservice/computenode/adduser

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3,3: se till att dedikerade administrativa konton används

**Vägledning**: integrera autentisering för Azure Batch program med Azure Active Directory. Skapa principer och procedurer kring användningen av dedikerade administrativa konton.

Dessutom kan du använda rekommendationer för Azure Security Center identitets-och åtkomst hantering.

Så här autentiserar du batch-program med Azure Active Directory:

https://docs.microsoft.com/azure/batch/batch-aad-auth

Så här övervakar du identitet och åtkomst med Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3,4: Använd enkel Sign-On (SSO) med Azure Active Directory

**Vägledning**: inte tillämpligt, medan Azure Batch stöder Azure AD-autentisering stöds inte enkel inloggning.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd multifaktor-autentisering för all Azure Active Directory baserad åtkomst.

**Vägledning**: integrera autentisering för Azure Batch program med Azure Active Directory (AAD). Aktivera AAD Multi-Factor Authentication (MFA) och följ rekommendationer för Azure Security Center identitets-och åtkomst hantering.

 

Så här aktiverar du MFA i Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Övervaka identitet och åtkomst i Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd Paw (Privileged Access Workstation) med Multi-Factor Authentication (MFA) konfigurerat för att logga in på och konfigurera dina Azure Batch-resurser.

Lär dig mer om arbets stationer med privilegie rad åtkomst:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Så här aktiverar du MFA i Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkt aktivitet från administrativa konton

**Vägledning**: om du har integrerat autentisering för Azure Batch program med Azure Active Directory (AAD) använder du Azure Active Directory säkerhets rapporter för att skapa loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön. Använd Azure Security Center för att övervaka identitets-och åtkomst aktiviteter.

Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Så här övervakar du användarnas identitets-och åtkomst aktiviteter i Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: om du har integrerat autentisering för Azure Batch program med Azure Active Directory kan du använda villkorlig åtkomst med namngivna platser för att tillåta åtkomst från enbart vissa logiska grupperingar av IP-adressintervall eller länder/regioner.

Så här konfigurerar du namngivna platser i Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: Använd Azure Active Directory (AAD) som central autentiserings-och auktoriserings system och integrera autentisering för Azure Batch program med AAD. AAD skyddar data genom att använda stark kryptering för data i vila och under överföring. AAD är också salter, hashar och lagrar användarautentiseringsuppgifter på ett säkert sätt.

Så här skapar och konfigurerar du en AAD-instans:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

Så här autentiserar du batch-program med AAD:

https://docs.microsoft.com/azure/batch/batch-aad-auth

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: Azure Active Directory (AAD) innehåller loggar som hjälper till att identifiera inaktuella konton. Dessutom kan du använda Azure Identity Access-granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användarnas åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.

Så här använder du granskningar av Azure Identity Access:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: övervaka försök att komma åt inaktiverade konton

**Vägledning**: skapa diagnostikinställningar för Azure Active Directory användar konton, skicka gransknings loggar och inloggnings loggar till en Azure Log Analytics-arbetsyta. Konfigurera önskade aviseringar i Azure Log Analytics-arbetsytan.

Så här integrerar du Azure-aktivitets loggar i Azure Monitor:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: avisering om beteende för beteende för konto inloggning

**Vägledning**: använda Azure Active Directory (AAD) risk identifiering och identitets skydds funktion för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör användar identiteter. Dessutom kan du mata in data i Azure Sentinel för ytterligare undersökning.

Så här visar du AAD-riskfyllda inloggningar:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Så här konfigurerar och aktiverar du principer för identitets skydds risker:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Publicera Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="313-divprovide-microsoft-with-access-to-relevant-customer-data-during-support-scenariosbrdiv"></a>3,13: <div>Ge Microsoft åtkomst till relevant kund information under support scenarier<br></div>

**Vägledning**: inte tillgänglig; Customer Lockbox ännu inte stöd för Azure Batch.
 
Lista över Customer Lockbox tjänster som stöds: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [säkerhets kontroll: data skydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för att spåra Azure-resurser som lagrar eller bearbetar känslig information.

Skapa och använda Taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera separata prenumerationer och/eller hanterings grupper för utveckling, testning och produktion. Azure Batch pooler bör åtskiljas av ett virtuellt nätverk/undernät, taggas på lämpligt sätt och skyddas med nätverks säkerhets grupper (NSG). Azure Batch data ska finnas i ett skyddat Azure Storage-konto.

Så här skapar du en Azure Batch pool i en Virtual Network:

https://docs.microsoft.com/azure/batch/batch-virtual-network

Skydda Azure Storage-konton:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information.

**Vägledning**: för Azure Storage konton som är kopplade till dina Azure Batch-pooler som innehåller känslig information, markerar du dem som känsliga med taggar och skyddar dem med Azures bästa praxis.

Funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för Azure Storage-eller beräknings resurser. Implementera en lösning från tredje part om det krävs för efterlevnad.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

Förstå kundens data skydd i Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

Skydda Azure Storage-konton:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvars område**: delat

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: kryptera all känslig information under överföring. Microsoft Azure resurser förhandlar TLS 1,2 som standard. Se till att alla klienter som ansluter till Azure Batch pooler eller data lager (Azure Storage-konton) kan förhandla TLS 1,2 eller senare.

Se till att HTTPS krävs för åtkomst till det lagrings konto som innehåller dina Azure Batch data.

Förstå Azure Storage konto kryptering i överföring:

https://docs.microsoft.com/azure/storage/common/storage-security-guide#encryption-in-transit

**Azure Security Center övervakning**: Ja

**Ansvars område**: delat

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: för Azure Storage konton som är kopplade till dina Azure Batch-pooler som innehåller känslig information, markerar du dem som känsliga med taggar och skyddar dem med Azures bästa praxis.

Funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för Azure Storage-eller beräknings resurser. Implementera en lösning från tredje part om det krävs för efterlevnad.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

Förstå kundens data skydd i Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

Skydda Azure Storage-konton:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvars område**: delat

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Använd Azure RBAC för att kontrol lera åtkomsten till resurser

**Vägledning**: Använd rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att kontrol lera åtkomsten till hanterings planet för Azure-resurser, inklusive batch-konto, batch-pool (-er) och lagrings konton.

Förstå Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

Så här konfigurerar du Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Använd värdbaserade data förlust skydd för att genomdriva åtkomst kontroll

**Vägledning**: funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för Azure Storage-eller beräknings resurser. Implementera en lösning från tredje part om det krävs för efterlevnad.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

Förstå kundens data skydd i Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvars område**: delat

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: för lagrings konton som är kopplade till ditt Azure Batch-konto rekommenderas att Microsoft kan hantera krypterings nycklar, men du har möjlighet att hantera dina egna nycklar om det behövs.

Hantera krypterings nycklar för Azure Storage-konton:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets logg för att skapa aviseringar för när ändringar sker i kritiska Azure-resurser som är relaterade till eller kopplade till dina Azure Batch konton/pooler.

Konfigurera diagnostikinställningar för lagrings konton som är kopplade till Azure Batch pool för att övervaka och logga alla CRUD-åtgärder mot data från poolen.

Så här skapar du aviseringar för Azure aktivitets logg händelser:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Så här aktiverar du ytterligare loggning/granskning för ett Azure Storage-konto:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [säkerhets kontroll: sårbarhets hantering](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg

**Vägledning**: för Azure Batch pool-noder ansvarar du för hantering av lösningen för sårbarhets hantering.

Om du har en Rapid7, Qualys eller någon annan plattforms prenumeration för sårbarhets hantering kan du manuellt installera sårbarhets bedömnings agenter på noder i batch-pooler och hantera noder via respektive Portal.

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Distribuera automatiserad hanterings lösning för operativ system

**Vägledning**: Microsoft för att underhålla och uppdatera Base Azure Batch pool Node-avbildningar. Se till att operativ systemet för Azure Batch pooler fortfarande har uppdaterats under giltighets tiden för klustrets livstid, vilket kan kräva att automatiska uppdateringar, övervakning av noderna eller regelbunden omstarter utförs.


**Azure Security Center övervakning**: Ja

**Ansvars område**: delat

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Distribuera automatisk lösning för Third Party Software korrigerings hantering

**Vägledning**: se till att Azure Batch Pools program från tredje part fortfarande har uppdaterats under giltighets tiden för klustrets livs längd, vilket kan kräva att automatiska uppdateringar, övervakning av noder eller regelbunden omstarter utförs.


**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: jämför sökningar efter säkerhets risker

**Vägledning**: om du har en Rapid7, Qualys eller någon annan plattforms prenumeration för sårbarhets hantering kan du använda leverantörens Portal för att visa och jämföra säkerhets risker för säkerhets kopiering.

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker.

**Vägledning**: Använd ett gemensamt risk bedömnings program (t. ex. vanliga sårbarhets bedömnings system) eller standard risk klassificeringarna som tillhandahålls av genomsöknings verktyget från tredje part.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [säkerhets kontroll: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Använd Azure Asset Discovery

**Vägledning**: Använd Azure Resource Graph för att fråga/identifiera alla resurser (t. ex. data bearbetning, lagring, nätverk osv.) i din prenumeration (er). Se till att du har rätt (Läs) behörigheter i din klient och kan räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via resurs diagram, rekommenderar vi starkt att du skapar och använder Azure Resource Manager ARM-resurser som går framåt.

Så här skapar du frågor med Azure Resource Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Så här visar du dina Azure-prenumerationer:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Förstå Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar till Azure-resurser som ger metadata till att logiskt organisera dem i en taxonomi.

Skapa och använda Taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer, vid behov, för att organisera och spåra till gångar. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

Så här skapar du ytterligare Azure-prenumerationer:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Så här skapar du Hanteringsgrupper:

https://docs.microsoft.com/azure/governance/management-groups/create

Skapa och använda Taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: underhåll av inventering av godkända Azure-resurser och program varu titlar.

**Vägledning**: definiera en lista över godkända Azure-resurser och godkänd program vara för beräknings resurser


**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper
- Tillåtna resurstyper

Använd Azure Resource Graph för att fråga/identifiera resurser i dina prenumerationer. Se till att alla Azure-resurser som finns i miljön är godkända.

Så här konfigurerar och hanterar du Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här skapar du frågor med Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: övervaka för program som inte godkänts i beräknings resurser

**Vägledning**: för Azure Batch pool-noder implementerar du en lösning från tredje part för att övervaka klusternoder för program som inte godkänts.


**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program

**Vägledning**: för Azure Batch pool-noder implementerar du en lösning från tredje part för att övervaka klusternoder för program som inte godkänts.


**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="68-use-only-approved-applications"></a>6,8: Använd endast godkända program

**Vägledning**: för Azure Batch pool-noder implementerar du en lösning från tredje part för att förhindra att obehöriga program körs.


**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper
- Tillåtna resurstyper

Så här konfigurerar och hanterar du Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här nekar du en speciell resurs typ med Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="610-implement-approved-application-list"></a>6,10: implementera lista över godkända program

**Vägledning**: för Azure Batch pool-noder implementerar du en lösning från tredje part för att förhindra att obehöriga filtyper körs.


**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6,11: <div>Begränsa användarnas möjlighet att interagera med Azure Resource Manager via skript</div>

**Vägledning**: Använd villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: begränsa användarnas möjlighet att köra skript i beräknings resurser

**Vägledning**: ej tillämpligt,

Detta gäller inte för Azure Batch, eftersom användare (icke-administratörer) av Azure Batch pooler inte behöver åtkomst till de enskilda noderna för att köra jobb. Kluster administratören har redan rot åtkomst till alla noder.


**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fysiskt eller logiskt särskiljande program med hög risk

**Vägledning**: inte tillämpligt, Benchmark är avsett för webb program som körs på Azure App Service-eller IaaS-instanser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [säkerhets kontroll: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: Använd Azure policy alias i namn rymden "Microsoft.BatCH" om du vill skapa anpassade principer för att granska eller tillämpa konfigurationen av dina Azure Batch-konton och pooler.

Så här visar du tillgängliga Azure Policy alias:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Så här konfigurerar och hanterar du Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7,2: upprätta säkra konfigurationer för ditt operativ system

**Vägledning**: upprätta säkra konfigurationer för dina noder i batch-poolen.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7,3: underhåll säkra konfigurationer för alla Azure-resurser

**Vägledning**: Använd Azure policy [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar för Azure-resurser som är relaterade till ditt batch-konto och pooler (till exempel virtuella nätverk, undernät, Azure-brandväggar, Azure Storage-konton osv.). Du kan använda Azure Policy alias från följande namn rymder för att skapa anpassade principer:

- Microsoft.Batch

- Microsoft.Storage

- Microsoft.Network

Så här konfigurerar och hanterar du Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Förstå Azure Policys effekter: https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7,4: underhåll säkra konfigurationer för operativ system

**Vägledning**: Azure Batch pool operativ Systems avbildningar som hanteras och underhålls av Microsoft. Du ansvarar för att implementera tillstånds konfigurationen på operativ system nivå.

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvars område**: delat

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: om du använder anpassade Azure policys definitioner för dina Azure Batch konton, pooler eller relaterade resurser använder du Azure-databaser för att lagra och hantera din kod på ett säkert sätt.

Så här lagrar du kod i Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Dokumentation om Azure databaser:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: lagra anpassade operativ Systems avbildningar på ett säkert sätt

**Vägledning**: om du använder anpassade avbildningar för Azure Batch pooler använder du rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att se till att endast behöriga användare får åtkomst till avbildningarna.

Förstå Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

Så här konfigurerar du Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: Distribuera hanterings verktyg för system konfiguration

**Vägledning**: använd inbyggda Azure policy definitioner för att varna, granska och genomdriva Azure Batch-relaterade datorkonfigurationer.  Använd Azure Policy alias i namn rymden "Microsoft.BatCH" för att skapa anpassade principer för dina Azure Batch-konton och pooler. Dessutom kan du utveckla en process och pipeline för att hantera princip undantag.

Så här konfigurerar och hanterar du Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: distribuera system konfigurations hanterings verktyg för operativ system

**Vägledning**: implementera en lösning från tredje part för att bibehålla önskat tillstånd för dina Azure Batch-poolens noders operativ system.


**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: implementera automatisk konfigurations övervakning för Azure-tjänster

**Vägledning**: Använd Azure policy alias i namn rymden "Microsoft.BatCH" för att skapa anpassade principer som ska granskas eller tillämpa konfigurationen för din Azure Batch-instans. Du kan också använda inbyggda principer som skapats specifikt för Azure Batch eller de resurser som används av Azure Batch, till exempel:

- Undernät ska associeras med en nätverks säkerhets grupp

-Lagrings konton ska använda en tjänst slut punkt för virtuellt nätverk

- Diagnostikloggar i batch-konton måste vara aktiverade

Så här visar du tillgängliga Azure Policy alias: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Så här konfigurerar och hanterar du Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementera automatisk konfigurations övervakning för operativ system

**Vägledning**: implementera en lösning från tredje part för att övervaka status för dina Azure Batch-poolens noders operativ system.


**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="711-securely-manage-azure-secrets"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: Azure Key Vault kan användas med Azure Batch distributioner för att hantera nycklar för lagring av pooler inom Azure Storage konton.

Så här integrerar du med Azure Managed Identities:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Så här skapar du en Key Vault:

https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Så här autentiserar du till Key Vault:

https://docs.microsoft.com/azure/key-vault/general/authentication

Så här tilldelar du en Key Vault åtkomst princip:

https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="712-securely-and-automatically-manage-identities"></a>7,12: säker och hantera identiteter automatiskt

**Vägledning**: inte tillgänglig, hanterad tjänstidentitet stöds inte av Azure Batch


**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Den här skannern uppmuntrar också att flytta identifierade autentiseringsuppgifter till säkrare platser som Azure Key Vault. 

Konfigurera inloggnings skannern: https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [säkerhets kontroll: försvar mot skadlig kod](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Använd centralt hanterat program mot skadlig kod

**Vägledning**: Använd Windows Defender på dina individuella Azure Batch pool-noder i fallet med Windows-operativsystem eller ange en egen lösning för program mot skadlig kod om du använder Linux.


**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: Microsoft Antimalware är aktiverat på den underliggande värden som stöder Azure-tjänster (till exempel Azure Batch), men det körs inte på kund innehåll.

Genomsök alla filer som laddas upp till Azure-resurser som inte är Compute, till exempel App Service, Data Lake Storage, Blob Storage osv. Microsoft kan inte komma åt kund information i dessa instanser.

Förstå Microsoft Antimalware för Azure Cloud Services och Virtual Machines:

https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: delat

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: se till att program vara och signaturer för skadlig program vara uppdateras

**Vägledning**: Använd Windows Defender på dina individuella Azure Batch pool-noder när det gäller Windows-operativsystem och se till att automatisk uppdatering är aktiverat. Ange din egen lösning för program mot skadlig kod om du använder Linux.


**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [säkerhets kontroll: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: se till att vanlig automatisk säkerhets kopiering UPS

**Vägledning**: när du använder ett Azure Storage konto för data lagret för Azure Batch-poolen väljer du lämpligt alternativ för REDUNDANS (LRS, ZRS, GRS, RA-GRS). 

Så här konfigurerar du redundans för Azure Storage-konton:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: när du använder ett Azure Storage konto för data lagret för Azure Batch-poolen väljer du lämpligt alternativ för REDUNDANS (LRS, ZRS, GRS, RA-GRS).  Se till att nycklarna säkerhets kopie ras om du använder Azure Key Vault för någon del av din Azure Batch-distribution.

Så här konfigurerar du redundans för Azure Storage-konton:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Så här säkerhetskopierar du nyckel valv nycklar i Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: om du hanterar dina egna nycklar för Azure Storage konton eller andra resurser som är relaterade till din Azure Batch implementering, kan du regelbundet testa återställning av säkerhetskopierade nycklar.

Så här säkerhetskopierar du nyckel valv nycklar i Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

Så här återställer du en kund hanterad nyckel med PowerShell:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: om Azure Key Vault används för att lagra nycklar som är relaterade till Azure Batch pool lagrings konton, aktiverar du Soft-Delete i Azure Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning.

Så här aktiverar du mjuk borttagning i Azure Key Vault:

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [säkerhets kontroll: incident svar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-incident-response-guide"></a>10,1: Skapa incident svars guide

**Vägledning**: kontrol lera att det finns skriftliga svars planer för incidenter som definierar personal roller och faser för incident hantering/hantering.

Konfigurera automatisering av arbets flöden i Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10,2: Skapa incident bedömnings-och prioriterings procedur

**Vägledning**: Security Center tilldelar en allvarlighets grad till aviseringar, som hjälper dig att prioritera i vilken ordning du deltar i varje avisering, så att när en resurs komprometteras kan du komma åt den direkt. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller det analytiska som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems incident svars funktioner på en vanlig takt. Identifiera svaga punkter och luckor och ändra planen efter behov.

Se NIST: guide för test, utbildning och övnings program för IT-planer och funktioner: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar &nbsp; för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en olaglig eller obehörig part.

Så här ställer du in Azure Security Center säkerhets kontakt:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Azure Security Center Data Connector för att strömma aviseringarna till Azure Sentinel.

Så här konfigurerar du kontinuerlig export:

https://docs.microsoft.com/azure/security-center/continuous-export

Strömma aviseringar till Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för automatisering av arbets flöden i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer.

Konfigurera automatisering av arbets flöde och Logic Apps:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [säkerhets kontroll: inträngande tester och röda team övningar](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att åtgärda alla kritiska säkerhets resultat inom 60 dagar.

**Vägledning**: Följ Microsofts regler för engagemang för att se till att dina inträngande tester inte strider mot Microsofts principer:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

Du hittar mer information om Microsofts strategi och körning av röda team indelning och inträngande av direktsända webbplatser mot Microsoft Managed Cloud Infrastructure, tjänster och program, här: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: delat

## <a name="next-steps"></a>Nästa steg

- Se [Azures säkerhets benchmark](../security/benchmarks/overview.md)
- Läs mer om [Azures säkerhets bas linjer](../security/benchmarks/security-baselines-overview.md)
