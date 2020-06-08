---
title: Azures säkerhets bas linje för Azure Data Factory
description: Azures säkerhets bas linje för Azure Data Factory
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 854d95f95d6ecf2fe35bcc93ddeaafdc9bafbf8f
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2020
ms.locfileid: "84485639"
---
# <a name="azure-security-baseline-for-azure-data-factory"></a>Azures säkerhets bas linje för Azure Data Factory

Azures säkerhets bas linje för Azure Data Factory innehåller rekommendationer som hjälper dig att förbättra säkerhets position för din distribution.

Bas linjen för den här tjänsten hämtas från [Azures prestandatest version 1,0](https://docs.microsoft.com/azure/security/benchmarks/overview), som ger rekommendationer om hur du kan skydda dina moln lösningar i Azure med våra bästa praxis rikt linjer.

Mer information finns i [Översikt över Azure Security-bas linjer](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets kontroll: nätverks säkerhet](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: skydda Azure-resurser i virtuella nätverk

**Vägledning**: när du skapar en Azure-SSIS integration Runtime (IR) har du möjlighet att ansluta den till ett virtuellt nätverk. Detta gör att Azure Data Factory kan skapa vissa nätverks resurser, till exempel en nätverks säkerhets grupp (NSG) och en belastningsutjämnare. Du kan också ange en egen statisk IP-adress eller skapa en egen statisk IP-adress eller Azure Data Factory skapa en åt dig. I NSG som skapas automatiskt av Azure Data Factory, är port 3389 öppen för all trafik som standard. Lås den här åtgärden för att säkerställa att endast dina administratörer har åtkomst.

Egen värdbaserade IRs kan distribueras på en lokal dator eller virtuell Azure-dator i ett virtuellt nätverk. Kontrol lera att distributionen av det virtuella nätverkets undernät har en NSG konfigurerad för att endast tillåta administrativ åtkomst. Azure-SSIS IR har otillåten port 3389 utgående som standard i Windows brand Väggs regel på varje IR-nod för skydd. Du kan skydda dina virtuella nätverks konfigurerade resurser genom att associera en NSG med under nätet och ställa in strikta regler.

När privat länk är tillgänglig använder du privata slut punkter för att skydda alla resurser som länkas till Azure Data Factory pipelinen, till exempel Azure SQL Server. Med privat länk passerar trafiken mellan ditt virtuella nätverk och tjänsten över Microsoft stamnät nätverket, vilket eliminerar exponering från det offentliga Internet.

* [Så här skapar du en Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)

* [Så här skapar och konfigurerar du en IR med egen värd](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)

* [Så här skapar du en Virtual Network](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [Så här skapar du en NSG med en säkerhets konfiguration](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

* [Ansluta en Azure-SSIS IR till ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network#virtual-network-configuration)

* [Förstå privat Azure-länk](https://docs.microsoft.com/azure/private-link/private-link-overview)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverkskort

**Vägledning**: Använd Azure Security Center och åtgärda rekommendationer för nätverks skydd för det virtuella nätverk och den nätverks säkerhets grupp som är kopplad till din integration runtime-distribution.

Dessutom ska du aktivera flödes loggar för nätverks säkerhets grupper (NSG) för NSG som skyddar din Integration Runtime distribution och skicka loggar till ett Azure Storage konto för trafik granskning.

Du kan också skicka NSG Flow-loggar till en Log Analytics arbets yta och använda Trafikanalys för att ge insikter i trafikflöde i Azure-molnet. Några av fördelarna med Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks problem.

* [Förstå nätverks säkerhet som tillhandahålls av Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

* [Så här aktiverar du NSG Flow-loggar](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Förstå nätverks säkerhet som tillhandahålls av Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

* [Så här aktiverar och använder du Trafikanalys](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för Azure Apps-tjänster eller data bearbetnings resurser som är värdar för webb program.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Aktivera DDoS Protection standard på de virtuella nätverk som är kopplade till din integration runtime-distribution för skydd från distribuerade DOS-attacker (Denial-of-Service). Använd Azure Security Center integrerad Hot information för att neka kommunikation med kända skadliga eller oanvända Internet-IP-adresser.

* [Så här konfigurerar du DDoS-skydd](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Förstå Azure Security Center integrerad Hot information](https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="15-record-network-packets"></a>1,5: registrera nätverks paket

**Vägledning**: Aktivera flödes loggar för nätverks säkerhets grupper (NSG) för NSG som skyddar din integration runtime distribution och skicka loggar till ett Azure Storage konto för trafik granskning.

Du kan också skicka NSG Flow-loggar till en Log Analytics arbets yta och använda Trafikanalys för att ge insikter i trafikflöde i Azure-molnet. Några av fördelarna med Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks problem.

* [Så här aktiverar du NSG Flow-loggar](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Förstå nätverks säkerhet som tillhandahålls av Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

* [Så här aktiverar och använder du Trafikanalys](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera nätverksbaserade intrångs identifiering/system för skydd mot intrång (ID/IP-adresser)

**Vägledning**: om du vill granska utgående trafik från Azure-SSIS IR kan du dirigera trafik som initieras från Azure-SSIS IR till lokal brand Väggs enhet via Azure ExpressRoute Force-tunnlar eller till en virtuell nätverks installation (NVA) från Azure Marketplace som stöder ID/IP-funktioner. Om intrångs identifiering och/eller skydd som baseras på nytto lasts granskning inte är ett krav kan du använda Azure-brandväggen med hot information.

* [Ansluta en Azure-SSIS Integration Runtime till ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

* [Så här distribuerar du Azure-brandvägg](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

* [Konfigurera aviseringar med Azure-brandväggen](https://docs.microsoft.com/azure/firewall/threat-intel)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="17-manage-traffic-to-web-applications"></a>1,7: hantera trafik till webb program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för Azure Apps-tjänster eller data bearbetnings resurser som är värdar för webb program.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: Använd taggar för virtuella nätverks tjänster för att definiera nätverks åtkomst kontroller i nätverks säkerhets gruppen (NSG) eller Azure-brandväggen. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange service tag-namnet (t. ex. DataFactoryManagement) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka inkommande trafik för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

* [Förstå och använda service märken](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

* [Förstå Azure Data Factory vissa service märken](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för nätverks inställningar och nätverks resurser som är kopplade till dina Azure Data Factory-instanser med Azure policy. Använd Azure Policy alias i namn områdena "Microsoft. DataFactory" och "Microsoft. Network" om du vill skapa anpassade principer för granskning eller framtvinga nätverks konfigurationen för dina Azure Data Factorys instanser. Du kan också använda inbyggda princip definitioner som är relaterade till nätverk eller dina Azure Data Factory-instanser, till exempel:
- DDoS Protection standard ska vara aktive rad

* [Så här konfigurerar och hanterar du Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy exempel för nätverk](https://docs.microsoft.com/azure/governance/policy/samples/#network)

* [Så här skapar du en Azure Blueprint](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: Använd taggar för resurser som rör nätverks säkerhets-och trafikflödet för dina Azure Data Factory-instanser för att tillhandahålla metadata och logisk organisation.

Använd någon av de inbyggda Azure Policy definitionerna som är relaterade till taggning, till exempel "Kräv tagg och dess värde", för att säkerställa att alla resurser skapas med taggar och meddela dig om befintliga otaggade resurser.

Du kan använda Azure PowerShell eller Azure CLI för att söka efter eller utföra åtgärder på resurser baserat på deras taggar.

* [Skapa och använda Taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka konfigurationer av nätverks resurser och identifiera ändringar för nätverks resurser som är relaterade till dina Azure Data Factory-instanser. Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska nätverks resurser sker.

* [Visa och hämta Azure aktivitets logg händelser](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Så här skapar du aviseringar i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets kontroll: loggning och övervakning](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Använd godkända tids källor för synkronisering

**Vägledning**: Microsoft hanterar den tids källa som används för Azure-resurser, till exempel Azure Data Factory för tidsstämplar i loggarna.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: mata in loggar via Azure Monitor för att samla in säkerhets data som genereras av Azure Data Factory. I Azure Monitor kan du fråga arbets ytan Log Analytics som har kon figurer ATS för att ta emot dina Azure Data Factory aktivitets loggar. Använd Azure Storage konton för långsiktig/Arkiverad logg lagring eller händelse nav för att exportera data till andra system.

Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel eller en säkerhets incident och händelse hantering från tredje part (SIEM). Du kan också integrera Azure Data Factory med Git för att utnyttja flera fördelar med käll kontroll, till exempel möjlighet att spåra/granska ändringar och möjlighet att återställa ändringar som introducerar buggar.

* [Konfigurera diagnostikinställningar](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-in-azure-portal)

* [Publicera Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Komma igång med Azure Monitor och SIEM-integrering från tredje part](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [Käll kontroll i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/source-control)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: för gransknings loggning i kontroll plan aktiverar du Azure aktivitets logg diagnostikinställningar och skickar loggarna till en Log Analytics arbets yta, Azure Event Hubs eller Azure Storage konto för arkivering. Med hjälp av Azures aktivitets logg data kan du avgöra vad, vem och när som helst för Skriv åtgärder (skicka, skicka och ta bort) på kontroll Plans nivån för dina Azure-resurser.

Använd diagnostikinställningar för att konfigurera diagnostikloggar för ej beräknings resurser i Azure Data Factory, till exempel mått och pipeline-körnings data. Azure Data Factory lagrar pipelinen – kör data i 45 dagar. Om du vill behålla dessa data under längre tid sparar du dina diagnostikloggar till ett lagrings konto för granskning eller manuell kontroll och anger Retentions tiden i dagar. Du kan också strömma loggarna till Azure Event Hubs eller skicka loggarna till en Log Analytics arbets yta för analys.

* [Så här aktiverar du diagnostikinställningar för Azure aktivitets logg](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Förstå Azure Data Factory diagnostikloggar](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: samla in säkerhets loggar från operativ system

**Vägledning**: om du kör integration runtime på en virtuell Azure-dator (VM) kan du använda Azure Monitor för att samla in data från den virtuella datorn. Genom att installera Log Analytics VM-tillägget kan Azure Monitor samla in data från dina virtuella Azure-datorer. Azure Security Center kan sedan tillhandahålla övervakning av säkerhets händelse loggen för Virtual Machines. Utifrån den data volym som säkerhets händelse loggen genererar, lagras den inte som standard.

Om din organisation vill behålla säkerhets händelse logg data, kan den lagras inom en data insamlings nivå, där den kan frågas i Log Analytics.

* [Samla in data från Azure Virtual Machines i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm)

* [Aktivera data insamling i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: Aktivera diagnostikinställningar för Azure Data Factory. Om du väljer att lagra loggar i en Log Analytics arbets yta, ställer du in Log Analytics arbets ytans kvarhållningsperiod enligt organisationens regler för efterlevnad. Använd Azure Storage konton för långsiktig/Arkiv lagring.

* [Så här aktiverar du diagnostikloggar i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#set-up-diagnostic-logs)

* [Ange logg lagrings parametrar för Log Analytics arbets ytor](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: Aktivera diagnostikinställningar för Azure Data Factory och skicka loggar till en Log Analytics arbets yta. Använd Log Analytics för att analysera och övervaka dina loggar för avvikande beteende och regelbundet granska resultaten. Se till att du även aktiverar diagnostikinställningar för alla data lager som är relaterade till dina Azure Data Factory-distributioner. Information om hur du aktiverar diagnostiska inställningar finns i varje tjänsts säkerhets bas linje.

Om du kör Integration Runtime på en virtuell Azure-dator (VM) aktiverar du även diagnostikinställningar för den virtuella datorn.

Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel eller en SIEM från tredje part.

* [Log Analytics schema](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#schema-of-logs-and-events)

* [Så här samlar du in data från en virtuell Azure-dator med Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm)

* [Publicera Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

**Vägledning**: du kan generera aviseringar om mått som stöds i Data Factory genom att gå till &amp; avsnittet aviserings mått i Azure Monitor.

Konfigurera diagnostikinställningar för Azure Data Factory och skicka loggar till en Log Analytics arbets yta. I arbets ytan Log Analytics konfigurerar du aviseringar så att de äger rum när en fördefinierad uppsättning villkor inträffar. Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel eller en SIEM från tredje part.

Se dessutom till att du aktiverar diagnostikinställningar för tjänster som är relaterade till dina data lager. Du kan se varje tjänsts säkerhets bas linje för vägledning.

* [Aviseringar i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/monitor-visually#alerts)

* [Sidan alla mått som stöds](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)

* [Så här konfigurerar du aviseringar i Log Analytics arbets yta](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod

**Vägledning**: om du kör integration runtime på en virtuell Azure-dator kan du använda Microsoft Antimalware för Azure Cloud Services och Virtual Machines och konfigurera dina virtuella datorer att logga händelser till ett Azure Storage konto. Konfigurera en Log Analytics arbets yta för att mata in händelser från lagrings kontona och skapa aviseringar där det är lämpligt. Följ rekommendationerna i Azure Security Center: "Compute &amp; Apps".

* [Så här konfigurerar du Microsoft Antimalware för Cloud Services och Virtual Machines](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

* [Aktivera övervakning på gästnivå för Virtual Machines](https://docs.microsoft.com/azure/cost-management/azure-vm-extended-metrics)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="29-enable-dns-query-logging"></a>2,9: Aktivera loggning av DNS-frågor

**Vägledning**: ej tillämpligt; Azure Data Factory bearbetar eller skapar inte DNS-relaterade loggar.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="210-enable-command-line-audit-logging"></a>2,10: Aktivera loggning av kommando rads granskning

**Vägledning**: om du kör integration runtime på en virtuell Azure-dator (VM) kan du aktivera loggning av kommando rads granskning. Azure Security Center tillhandahåller övervakning av säkerhets händelse logg för virtuella Azure-datorer. Security Center etablerar Microsoft Monitoring Agent på alla virtuella Azure-datorer som stöds och eventuella nya som skapas om automatisk etablering är aktiverat eller om du kan installera agenten manuellt. Agenten gör det möjligt för processen att skapa händelse 4688 och fältet kommandorad i händelse 4688. Nya processer som skapas på den virtuella datorn registreras av EventLog och övervakas av Security Centers identifierings tjänster.

* [Datainsamling i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [säkerhets kontroll: identitets-och åtkomst kontroll](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: i Azure Data Factory, se till att du regelbundet spårar och synkroniserar användar åtkomst. Om du vill skapa Data Factory-instanser måste det användarkonto du använder för att logga in på Azure vara medlem av rollerna deltagare eller ägare, eller vara administratör för Azure-prenumerationen.

På klient nivå har Azure Active Directory (AD) Dessutom inbyggda roller som måste tilldelas explicit och som kan frågas. Använd Azure AD PowerShell-modulen för att utföra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper som har administrativ åtkomst till kontroll planet för dina Azure Data Factory-instanser.

Även om Azure AD är den rekommenderade metoden för att administrera användar åtkomst, Tänk på att om du kör Integration Runtime på en virtuell Azure-dator (VM) kan den virtuella datorn ha lokala konton också. Både lokala konton och domän konton bör granskas och hanteras, vanligt vis med minsta möjliga storlek. Dessutom skulle vi meddela att Privileged Identity Manager granskas för just-in-Time-funktionen för att minska tillgängligheten för administrativa behörigheter.

* [Roller och behörigheter för Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-roles-permissions)

* [Information om Privileged Identity Manager](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

* [Så här hämtar du en katalog roll i Azure AD med PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [Information för lokala konton](https://docs.microsoft.com/azure/active-directory/devices/assign-local-admin#manage-the-device-administrator-role)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: Azure Data Factory använder Azure Active Directory (AD) för att ge åtkomst till Azure Portal samt Azure Data Factory-konsolen. Azure AD har inte begreppet standard lösen ord, men du är ansvarig för att ändra eller inte tillåta standard lösen ord för anpassade program och program från tredje part.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: skapa standard procedurer för användning av dedikerade administrativa konton för åtkomst till Azures kontroll plan (Azure Portal) samt Azure Data Factory-konsolen. Använd Azure Security Center identitets-och åtkomst hantering för att övervaka antalet administrativa konton i Azure AD.

För att hjälpa dig att hålla koll på dedikerade administrativa konton kan du dessutom använda rekommendationer från Azure Security Center eller inbyggda Azure-principer, t. ex.:
- Det bör finnas fler än en ägare som tilldelats din prenumeration
- Föråldrade konton med ägar behörigheter bör tas bort från din prenumeration
- Externa konton med ägar behörigheter bör tas bort från din prenumeration

Om du kör Integration Runtime på en virtuell Azure-dator kan administratörs kontona på Azure Virtual Machines också konfigureras med Azure Privileged Identity Manager (PIM). Azure Privileged Identity Manager innehåller flera alternativ, till exempel bara i tids ökning, Multi-Factor Authentication och Delegerings alternativ så att behörigheter bara är tillgängliga för vissa tids ramar och kräver att en andra person godkänner detta.

* [Förstå Azure Security Center identitet och åtkomst](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Använda Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Information om Privileged Identity Manager](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

* [Roller och behörigheter för Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-roles-permissions)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Använd enkel inloggning (SSO) med Azure Active Directory

**Vägledning**: Använd en Azure App-registrering (tjänstens huvud namn) för att hämta en token som programmet eller funktionen kan använda för att komma åt och interagera med dina Recovery Services-valv.

* [Så här anropar du Azure REST API: er](https://docs.microsoft.com/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

* [Registrera klient programmet (tjänstens huvud namn) med Azure AD](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad)

* [Information om Azure Recovery Services API](https://docs.microsoft.com/rest/api/recoveryservices/)

* [Information om REST API för Azure Data Factory](https://docs.microsoft.com/rest/api/datafactory/)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory baserad åtkomst

**Vägledning**: aktivera Azure Active Directory Multi-Factor Authentication (MFA) och följ rekommendationer för Azure Security Center identitets-och åtkomst hantering.

* [Så här aktiverar du MFA i Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Övervaka identitet och åtkomst i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd Paw (Privileged Access Workstation) med Multi-Factor Authentication (MFA) konfigurerat för att logga in på och konfigurera Azure-resurser.

* [Lär dig mer om arbets stationer med privilegie rad åtkomst](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Så här aktiverar du MFA i Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkta aktiviteter från administrativa konton

**Vägledning**: Använd Azure Active Directory säkerhets rapporter för att skapa loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön. Använd Azure Security Center för att övervaka identitets-och åtkomst aktiviteter.

Om du kör Integration Runtime på en virtuell Azure-dator (VM) kan du dessutom utföra den virtuella datorn till Azure Sentinel. Microsoft Azure Sentinel är en skalbar, molnbaserad, molnbaserad, SIEM (Security information Event Management) och SOAR-lösning (Security Orchestration autoresponse). Azure Sentinel ger intelligent säkerhets analys och hot information i hela företaget, vilket ger en enda lösning för aviserings identifiering, Hot synlighet, proaktiv jakt och hot svar.

* [Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [Övervaka användarnas identitets-och åtkomst aktiviteter i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Aktivera Azure-kontroll på kort](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: Använd villkorlig åtkomst med namngivna platser för att tillåta åtkomst från enbart vissa logiska grupperingar av IP-adressintervall eller länder/regioner.

* [Så här konfigurerar du namngivna platser i Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: en data fabrik kan associeras med en hanterad identitet för Azure-resurser som representerar den aktuella data fabriken. Du kan använda den här hanterade identiteten för Azure SQL Database autentisering. Den angivna fabriken kan komma åt och kopiera data från eller till databasen med hjälp av den här identiteten.

Om du kör Integration Runtime (IR) på en virtuell Azure-dator kan du använda hanterade identiteter för att autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan några autentiseringsuppgifter i din kod. Din kod som körs på en virtuell dator kan använda hanterad identitet för att begära åtkomsttoken för tjänster som stöder Azure AD-autentisering.

* [Så här skapar och konfigurerar du en Azure AD-instans](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

* [Vad är hanterade identiteter för Azure-resurser?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

* [Kopiera och transformera data i Azure SQL Database med Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#using-service-principal-authentication)

* [Konfigurera och hantera Azure Active Directory autentisering med Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: Azure Active Directory (AD) innehåller loggar för att hjälpa dig att identifiera inaktuella konton. Dessutom kan du använda Azure Identity Access-granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användar åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.

Om du kör din körnings integration på en virtuell Azure-dator måste du granska de lokala säkerhets grupperna och användarna för att se till att det inte finns några oväntade konton som kan äventyra systemet.

* [Så här använder du granskningar av Azure Identity Access](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [Förstå Azure AD repor ting](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Så här använder du granskningar av Azure Identity Access](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning**: du har åtkomst till Azure Active Directory (AD) inloggnings aktivitet, gransknings-och risk händelse logg källor, som gör att du kan integrera med alla Siem/övervaknings verktyg. Du kan effektivisera den här processen genom att skapa diagnostikinställningar för Azure AD-användarkonton och skicka gransknings loggar och inloggnings loggar till en Log Analytics-arbetsyta. Du kan konfigurera önskade logg aviseringar i Log Analytics.

Om du kör Integration Runtime på en virtuell Azure-dator (VM), finns det en inbyggd virtuell dator i Azure Sentinel. Microsoft Azure Sentinel är en skalbar, molnbaserad, molnbaserad, SIEM (Security information Event Management) och SOAR-lösning (Security Orchestration autoresponse). Azure Sentinel ger intelligent säkerhets analys och hot information i hela företaget, vilket ger en enda lösning för aviserings identifiering, Hot synlighet, proaktiv jakt och hot svar.

* [Så här integrerar du Azures aktivitets loggar i Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [Ge åtkomst till Event Hubs resurser med Azure Active Directory](https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory)

* [Aktivera Azure-kontroll på kort](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: avisering om beteende för beteende för konto inloggning

**Vägledning**: Använd Azure Active Directory (AD) som central-autentiserings-och auktoriserings system för dina Azure Data Factory resurser, till exempel Azure SQL Database eller Azure Virtual Machines. Använd Azure AD Identity Protection-och risk identifierings funktioner för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör användar identiteter för konto inloggnings beteende avvikelse i kontroll planet (Azure Portal). Du kan också mata in data i Azure Sentinel för ytterligare undersökning.

* [Visa Azure AD-riskfyllda inloggningar](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Så här konfigurerar och aktiverar du risk principer för identitets skydd](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Publicera Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Konfigurera och hantera Azure Active Directory-autentisering med SQL](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure?tabs=azure-powershell)

* [Aktivera Azure Active Directory-autentisering för Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: ge Microsoft åtkomst till relevant kund information under support scenarier

**Vägledning**: i support scenarier där Microsoft behöver åtkomst till kund information tillhandahåller Azure Customer lockbox ett gränssnitt som kunder kan använda för att granska och godkänna eller avvisa förfrågningar om kund data åtkomst. Observera att även om Azure lock inte är tillgängligt för Azure Data Factory sig själv, stöder Azure-databasen Azure SQL-databaser och Azure Virtual Machines.

* [Förstå Customer Lockbox](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [säkerhets kontroll: data skydd](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för att spåra Azure-resurser som lagrar eller bearbetar känslig information.

Använd funktionen för identifiering och klassificering av Azure SQL Database data. Data identifiering och klassificering innehåller avancerade funktioner som är inbyggda i Azure SQL Database för att upptäcka, klassificera och märka, &amp; skydda känsliga data i dina databaser.

* [Skapa och använda Taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Så här använder du data identifiering och klassificering för Azure SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera separata prenumerationer och/eller hanterings grupper för utveckling, testning och produktion. Integrerings körningar bör åtskiljas av Virtual Network (VNet)/Subnet och taggas på lämpligt sätt.

 Du kan också använda privata slut punkter för att utföra nätverks isolering. En privat Azure-slutpunkt är ett nätverks gränssnitt som ansluter privat och säkert till en tjänst som drivs av en privat Azure-länk. Privat slut punkt använder en privat IP-adress från ditt VNet, vilket effektivt tar tjänsten till ditt VNet.

* [Så här skapar du ytterligare Azure-prenumerationer](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Så här skapar du Hanteringsgrupper](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Skapa och använda Taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Förstå privat länk](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information

**Vägledning**: för data källor (till exempel Azure SQL Database) som lagrar eller bearbetar känslig information för din Azure Data Factory-distribution markerar du de relaterade resurserna som känsliga med taggar.

När privat länk är tillgänglig använder du privata slut punkter för att skydda alla resurser som länkas till Azure Data Factory pipelinen. Trafik mellan ditt virtuella nätverk och tjänsten passerar över Microsofts stamnätverk, vilket eliminerar exponering från det offentliga Internet. Du kan också minska risken för data exfiltrering genom att konfigurera en strikt uppsättning utgående regler i en nätverks säkerhets grupp (NSG) och associera NSG med ditt undernät.

* [Skapa och använda Taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Så här skapar du en NSG med en säkerhets konfiguration](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

* [Förstå privat Azure-länk](https://docs.microsoft.com/azure/private-link/private-link-overview)

* [Förstå kundens data skydd i Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: om data lagret för molnet stöder https eller TLS, är alla data överföringar mellan tjänster för data förflyttning i Data Factory och ett moln data lager via säker kanal-https eller TLS. Den TLS-version som används är 1,2.

Alla anslutningar till Azure SQL Database och Azure SQL Data Warehouse Kräv kryptering (SSL/TLS) medan data överförs till och från databasen. När du redigerar en pipeline med hjälp av JSON lägger du till egenskapen kryptering och anger den till true i anslutnings strängen. För Azure Storage kan du använda HTTPS i anslutnings strängen.

* [Förstå kryptering i överföring i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)

**Azure Security Center övervakning**: Ja

**Ansvars område**: delat

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: om du använder Azure Data Factory för att kopiera och transformera Azure SQL Database-instanser använder du funktionen Azure SQL Database data identifiering och klassificering. Data identifiering och klassificering innehåller avancerade funktioner som är inbyggda i Azure SQL Database för att upptäcka, klassificera och märka, &amp; skydda känsliga data i dina databaser.

Funktioner för identifiering och klassificering av data är inte tillgängliga för andra Azure-tjänster än.

* [Så här använder du data identifiering och klassificering för Azure SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: Använd rollbaserad åtkomst kontroll för att kontrol lera åtkomst till resurser

**Vägledning**: Använd Azure Active Directory (AD) rollbaserad åtkomst kontroll (RBAC) för att kontrol lera åtkomsten till Azure Data Factory kontroll planet (Azure Portal).

Om du vill skapa Data Factory-instanser måste det användarkonto du använder för att logga in på Azure vara medlem av rollerna deltagare eller ägare, eller vara administratör för Azure-prenumerationen.

För dina Data Factory data källor, till exempel Azure SQL Database, se säkerhets bas linjen för den tjänsten för mer information om RBAC.

* [Konfigurera RBAC i Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

* [Roller och behörigheter för Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-roles-permissions)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Använd värdbaserade data förlust skydd för att genomdriva åtkomst kontroll

**Vägledning**: funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för Azure Storage-eller beräknings resurser. Implementera en lösning från tredje part om det krävs för efterlevnad.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

* [Förstå kundens data skydd i Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvars område**: delat

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: Vi rekommenderar att du aktiverar data krypterings mekanismen för alla data lager som är relaterade till dina Azure Data Factory-distributioner. Du kan läsa säkerhets bas linjen för den tjänsten för mer information om kryptering av data i vila.

Om du kör Integration Runtime på en virtuell Azure-dator krypteras virtuella diskar på Virtuella Windows-datorer (VM) i vila med hjälp av antingen kryptering på Server sidan eller med hjälp av Azure Disk Encryption (ADE). Azure Disk Encryption utnyttjar BitLocker-funktionen i Windows för att kryptera hanterade diskar med Kundhanterade nycklar i den virtuella gäst datorn. Kryptering på Server sidan med Kundhanterade nycklar förbättrar på ADE genom att du kan använda alla OS-typer och avbildningar för dina virtuella datorer genom att kryptera data i lagrings tjänsten.

Du kan lagra autentiseringsuppgifter eller hemliga värden i en Azure Key Vault och använda dem under pipeline-körningen för att skicka till dina aktiviteter. Du kan också lagra autentiseringsuppgifter för data lager och beräkningar i en Azure Key Vault. Azure Data Factory hämtar autentiseringsuppgifterna när en aktivitet som använder data lagret/data bearbetningen körs.

* [Förstå kryptering i vila i Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)

* [Kryptering på Server sidan av Azure Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption)

* [Azure Disk Encryption för virtuella Windows-datorer](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-overview)

* [Använda Azure Key Vault hemligheter i pipeline-aktiviteter](https://docs.microsoft.com/azure/data-factory/how-to-use-azure-key-vault-secrets-pipeline-activities)

* [Information om autentiseringsuppgifter i Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets logg för att skapa aviseringar för när ändringar sker i Azure Data Factory och relaterade resurser.

* [Så här skapar du aviseringar för Azure aktivitets logg händelser](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

* [Så här skapar du aviseringar för Azure aktivitets logg händelser](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

* [Analysloggning i Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [säkerhets kontroll: sårbarhets hantering](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg

**Vägledning**: om du använder Azure SQL Database som ett data lager, aktiverar du avancerad data säkerhet för Azure SQL Database och följer rekommendationer från Azure Security Center om att utföra sårbarhets bedömningar på dina Azure SQL-servrar.

Om du kör Integration Runtime på en virtuell Azure-dator (VM) följer du rekommendationer från Azure Security Center om hur du utför sårbarhets bedömningar på dina virtuella datorer. Använd Azure Security-Rekommenderad eller en lösning från tredje part för att utföra sårbarhets bedömningar för dina virtuella datorer.

* [Så här kör du sårbarhets bedömningar i Azure SQL-databaser](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)

* [Så här aktiverar du avancerad data säkerhet](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [Så här implementerar du rekommendationer för Azure Security Center sårbarhets bedömning](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Distribuera automatiserad hanterings lösning för operativ system

**Vägledning**: om du kör integration runtime på en virtuell Azure-dator (VM) använder du Azure uppdateringshantering-lösningen för att hantera uppdateringar och korrigeringar för dina virtuella datorer. Uppdateringshantering använder den lokalt konfigurerade uppdaterings databasen för att korrigera Windows-system som stöds. Med verktyg som System Center Updates Publisher (Updates Publisher) kan du publicera anpassade uppdateringar i Windows Server Update Services (WSUS). Med det här scenariot kan Uppdateringshantering korrigera datorer som använder Configuration Manager som sitt uppdaterings lager med program vara från tredje part.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

* [Uppdateringshantering lösning i Azure](https://docs.microsoft.com/azure/automation/automation-update-management)

* [Hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer](https://docs.microsoft.com/azure/automation/automation-tutorial-update-management)

* [Förstå kundens data skydd i Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center övervakning**: Ja

**Ansvars område**: delat

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Distribuera automatiserad korrigerings hanterings lösning för program varu titlar från tredje part

**Vägledning**: om du kör integration runtime på en virtuell Azure-dator (VM) kan du använda en lösning för korrigerings hantering från tredje part. Du kan använda Azure Uppdateringshantering-lösningen för att hantera uppdateringar och korrigeringar för dina virtuella datorer. Uppdateringshantering använder den lokalt konfigurerade uppdaterings databasen för att korrigera Windows-system som stöds. Med verktyg som System Center Updates Publisher (Updates Publisher) kan du publicera anpassade uppdateringar i Windows Server Update Services (WSUS). Med det här scenariot kan Uppdateringshantering korrigera datorer som använder Configuration Manager som sitt uppdaterings lager med program vara från tredje part.

* [Uppdateringshantering lösning i Azure](https://docs.microsoft.com/azure/automation/automation-update-management)

* [Hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer](https://docs.microsoft.com/azure/automation/automation-tutorial-update-management)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: jämför sökningar efter säkerhets risker

**Vägledning**: om du kör integration runtime på en virtuell Azure-dator kan du exportera Sök resultat med jämna mellanrum och jämföra resultaten för att kontrol lera att säkerhets problemen har åtgärd ATS. När du använder rekommendationen för sårbarhets hantering som föreslås av Azure Security Center kan du pivotera in den valda lösningens Portal för att visa historiska skannings data.

* [Förstå inbyggd sårbarhets-skanner för virtuella datorer](https://docs.microsoft.com/azure/security-center/built-in-vulnerability-assessment)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker

**Vägledning**: om du kör integration runtime på en virtuell Azure-dator kan du använda den interna säkerhets skannern. Den sårbarhets-scanner som ingår i Azure Security Center drivs av Qualys. Qualys ' s Scanner är det ledande verktyget för real tids identifiering av sårbarheter i din Azure-Virtual Machines.

När Security Center identifierar sårbarheter visar den resultat och relaterad information som rekommendationer. Relaterad information innehåller åtgärds steg, relaterade CVEs, CVSS resultat med mera. Du kan visa identifierade sårbarheter för en eller flera prenumerationer eller för en enskild virtuell dator.

* [Integrerad sårbarhets-skanner för virtuella datorer](https://docs.microsoft.com/azure/security-center/built-in-vulnerability-assessment)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [säkerhets kontroll: inventering och till gångs hantering](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: Använd Azure Resource Graph för att fråga/identifiera alla resurser (t. ex. data bearbetning, lagring, nätverk, portar och protokoll osv.) i din prenumeration (er). Se till att du har rätt (Läs) behörigheter i din klient organisation och räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via resurs diagram, rekommenderar vi starkt att du skapar och använder Azure Resource Manager resurser som går framåt.

* [Så här skapar du frågor med Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Så här visar du dina Azure-prenumerationer](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Förstå Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar till Azure-resurser som ger metadata till att logiskt organisera dem i en taxonomi.

* [Skapa och använda Taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer om det behövs för att organisera och spåra Azure-resurser. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

Använd dessutom Azure Policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:
- Otillåtna resurstyper
- Tillåtna resurstyper

* [Så här skapar du ytterligare Azure-prenumerationer](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Så här skapar du Hanteringsgrupper](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Skapa och använda Taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla en inventering av godkända Azure-resurser

**Vägledning**: definiera godkända Azure-resurser och godkänd program vara för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i dina prenumerationer.

Använd Azure Resource Graph för att fråga/identifiera resurser i sina prenumerationer. Se till att alla Azure-resurser som finns i miljön är godkända.

* [Så här konfigurerar och hanterar du Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Så här skapar du frågor med Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: övervaka för program som inte godkänts i beräknings resurser

**Vägledning**: om du kör integration runtime på en virtuell Azure-dator (VM) kan du använda inventering av virtuella Azure-datorer för att automatisera insamling av information om all program vara på Virtual Machines. Azure Automation ger fullständig kontroll under distribution, drift och inaktivering av arbetsbelastningar och resurser.

Obs!: program varu namn, version, utgivare och uppdaterings tid är tillgängliga från Azure Portal. För att få åtkomst till installations datum och annan information, krävs kunden för att aktivera diagnostik på gästnivå och ta med Windows-händelseloggen till en Log Analytics-arbetsyta.

* [En introduktion till Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro)

* [Så här aktiverar du inventering av virtuella Azure-datorer](https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program

**Vägledning**: om du kör integration runtime på en virtuell Azure-dator ger Azure Automation fullständig kontroll under distribution, åtgärder och inaktive ring av arbets belastningar och resurser. Du kan använda Ändringsspårning för att identifiera all program vara som är installerad på Virtual Machines. Du kan implementera en egen process eller använda Azure Automation tillstånds konfiguration för att ta bort otillåten program vara.

* [En introduktion till Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro)

* [Spåra ändringar i din miljö med Ändringsspårning-lösningen](https://docs.microsoft.com/azure/automation/change-tracking)

* [Översikt över Azure Automation tillstånds konfiguration](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="68-use-only-approved-applications"></a>6,8: Använd endast godkända program

**Vägledning**: om du kör integration runtime på en virtuell Azure-dator (VM) använder du Azure Security Center adaptiva program kontroller för att säkerställa att endast auktoriserade program körs och all obehörig program vara blockeras från att köras på virtuella datorer.

* [Använda Azure Security Center adaptiva program kontroller](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:
- Otillåtna resurstyper
- Tillåtna resurstyper

* [Så här konfigurerar och hanterar du Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Så här nekar du en speciell resurs typ med Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: underhåll en inventering av godkända program varu titlar

**Vägledning**: anpassningsbar program kontroll är en intelligent, automatiserad lösning från slut punkt till slut punkt från Azure Security Center som hjälper dig att styra vilka program som kan köras på dina Azure-och icke-Azure-datorer (Windows och Linux). Implementera en lösning från tredje part om detta inte uppfyller organisationens krav.

Observera att detta endast gäller om din Integration Runtime körs på en virtuell Azure-dator.

* [Använda Azure Security Center adaptiva program kontroller](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Konfigurera villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

* [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: begränsa användarnas möjlighet att köra skript i beräknings resurser

**Vägledning**: om du kör din körnings integration på en virtuell Azure-dator, beroende på typen av skript, kan du använda konfigurationer för operativ system eller resurser från tredje part för att begränsa användarnas möjlighet att köra skript i Azure Compute-resurser. Du kan också använda Azure Security Center adaptiva program kontroller för att säkerställa att endast auktoriserade program körs och all obehörig program vara blockeras från att köras på Azure Virtual Machines.

* [Så här styr du körning av PowerShell-skript i Windows-miljöer](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

* [Använda Azure Security Center adaptiva program kontroller](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fysiskt eller logiskt särskiljande program med hög risk

**Vägledning**: program med hög risk som distribueras i din Azure-miljö kan isoleras med hjälp av virtuella nätverk, undernät, prenumerationer, hanterings grupper osv. och tillräckligt säkra med antingen en Azure-brandvägg, en brand vägg för webbaserade program (WAF) eller nätverks säkerhets grupp (NSG).

* [Virtuella nätverk och virtuella datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/network-overview)

* [Vad är Azure Firewall?](https://docs.microsoft.com/azure/firewall/overview)

* [Vad är en brand vägg för Azure Web Application?](https://docs.microsoft.com/azure/web-application-firewall/overview)

* [Nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/security-overview)

* [Vad är Azure Virtual Network?](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)

* [Ordna resurser med hanteringsgrupper i Azure](https://docs.microsoft.com/azure/governance/management-groups/overview)

* [Beslutsguide för prenumerationer](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [säkerhets kontroll: säker konfiguration](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för Azure Data Factory med Azure policy. Använd Azure Policy alias i namn området "Microsoft. DataFactory" om du vill skapa anpassade principer för granskning eller tillämpa konfigurationen för dina Azure Data Factory-instanser.

* [Visa tillgängliga Azure Policy alias](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Så här konfigurerar och hanterar du Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: upprätta säkra konfigurationer för operativ system

**Vägledning**: om du kör din körnings integration på en virtuell Azure-dator använder du Azure Security Center rekommendation [åtgärda sårbarheter i säkerhetskonfigurationer på din Virtual Machines] för att underhålla säkerhetskonfigurationer på alla beräknings resurser.

* [Så här övervakar du Azure Security Center rekommendationer](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

* [Så här åtgärdar du Azure Security Center rekommendationer](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure policy [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar i dina Azure-resurser.

* [Så här konfigurerar och hanterar du Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Förstå Azure Policys effekter](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

* [Information om hur du skapar Azure Resource Manager mallar](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: underhåll säkra konfigurationer för operativ system

**Vägledning**: om du kör integration runtime på en virtuell Azure-dator (VM), Observera att det finns flera alternativ för att underhålla en säker konfiguration för virtuella datorer för distribution:
- Azure Resource Manager mallar: dessa är JSON-baserade filer som används för att distribuera en virtuell dator från Azure Portal, och den anpassade mallen måste behållas. Microsoft utför underhållet på bas mallarna.
- Anpassad virtuell hård disk (VHD): i vissa fall kan det krävas att anpassade VHD-filer används, till exempel vid hantering av komplexa miljöer som inte kan hanteras på annat sätt. -Azure Automation tillstånds konfiguration: när det grundläggande operativ systemet har distribuerats kan du använda det för att få mer detaljerad kontroll över inställningarna och verkställas genom automatiserings ramverket.

I de flesta fall kan de virtuella bas-VM-mallarna tillsammans med Azure Automation önskad tillstånds konfiguration hjälpa till att möta och upprätthålla säkerhets kraven.

* [Information om hur du hämtar mallen för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

* [Information om hur du skapar Azure Resource Manager mallar](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

* [Ladda upp en anpassad virtuell hård disk till Azure](https://docs.microsoft.com/azure-stack/operator/azure-stack-add-vm-image?view=azs-1910)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: om du använder anpassade Azure policys definitioner använder du Azure DevOps eller Azure databaser för att lagra och hantera din kod på ett säkert sätt.

* [Så här lagrar du kod i Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Dokumentation om Azure databaser](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: lagra anpassade operativ Systems avbildningar på ett säkert sätt

**Vägledning**: om du använder anpassade avbildningar använder du rollbaserad åtkomst kontroll (RBAC) för att se till att endast behöriga användare får åtkomst till avbildningarna. För behållar avbildningar lagrar du dem i Azure Container Registry och använder RBAC för att se till att endast behöriga användare får åtkomst till avbildningarna.

Rollen Data Factory Contributor kan användas för att skapa och hantera data fabriker, samt underordnade resurser i dem.

* [Förstå RBAC i Azure](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)

* [Förstå RBAC för Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-roles)

* [Konfigurera RBAC i Azure](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

* [Roller och behörigheter för Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-roles-permissions)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Distribuera konfigurations hanterings verktyg för Azure-resurser

**Vägledning**: använd inbyggda Azure policy definitioner samt Azure policy alias i namn området "Microsoft. DataFactory" för att skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. Dessutom kan du utveckla en process och pipeline för att hantera princip undantag.

* [Så här konfigurerar och hanterar du Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Distribuera konfigurations hanterings verktyg för operativ system

**Vägledning**: den här rekommendationen kan gälla om din integration runtime körs på en virtuell Azure-dator. Azure Automation tillstånds konfiguration är en konfigurations hanterings tjänst för DSC-noder (Desired State Configuration) i alla moln eller lokala data Center. Det möjliggör skalbarhet på tusentals datorer snabbt och enkelt från en central, säker plats. Du kan enkelt publicera datorer, tilldela dem deklarativ konfigurationer och Visa rapporter som visar varje dators kompatibilitet med önskat tillstånd som du har angett.

* [Onboarding-datorer för hantering genom Azure Automation tillstånds konfiguration](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementera automatisk konfigurations övervakning för Azure-resurser

**Vägledning**: använd inbyggda Azure policy definitioner samt Azure policy alias i namn området "Microsoft. DataFactory" för att skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. Använd Azure Policy [audit], [neka] och [distribuera om det inte finns] för att automatiskt tillämpa konfigurationer för dina Azure-resurser.

* [Så här konfigurerar och hanterar du Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementera automatisk konfigurations övervakning för operativ system

**Vägledning**: den här rekommendationen kan gälla om din integration runtime körs på en virtuell Azure-dator. Azure Automation tillstånds konfiguration är en konfigurations hanterings tjänst för DSC-noder (Desired State Configuration) i alla moln eller lokala data Center. Det möjliggör skalbarhet på tusentals datorer snabbt och enkelt från en central, säker plats. Du kan enkelt publicera datorer, tilldela dem deklarativ konfigurationer och Visa rapporter som visar varje dators kompatibilitet med önskat tillstånd som du har angett.

* [Onboarding-datorer för hantering genom Azure Automation tillstånds konfiguration](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: Använd hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda hemlig hantering för dina moln program.

Du kan också lagra autentiseringsuppgifter eller hemliga värden i en Azure Key Vault och använda dem under pipeline-körningen för att skicka till dina aktiviteter. Se till att mjuk borttagning är aktiverat.

* [Integrera med Azure Managed Identities](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

* [Så här skapar du en Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

* [Så här ger Key Vault autentisering med en hanterad identitet](https://docs.microsoft.com/azure/key-vault/managed-identity)

* [Använda Azure Key Vault-hemligheter i pipeline-aktiviteter](https://docs.microsoft.com/azure/data-factory/how-to-use-azure-key-vault-secrets-pipeline-activities)

* [Mjuk borttagning i Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

**Vägledning**: när du skapar en data fabrik kan du skapa en hanterad identitet tillsammans med fabriks skapande. Den hanterade identiteten är ett hanterat program som är registrerat för Azure Active Directory och representerar den specifika data fabriken.

* [Hanterad identitet för Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Den här skannern uppmuntrar också att flytta identifierade autentiseringsuppgifter till säkrare platser som Azure Key Vault.

* [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [säkerhets kontroll: försvar mot skadlig kod](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Använd centralt hanterat program mot skadlig kod

**Vägledning**: om du kör integration runtime på en virtuell Azure-dator kan du använda Microsoft Antimalware för Azure virtuella Windows-datorer för att kontinuerligt övervaka och försvara dina resurser.

* [Så här konfigurerar du Microsoft Antimalware för Cloud Services och Virtual Machines](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: Microsoft Antimalware är aktiverat på den underliggande värden som stöder Azure-tjänster (till exempel Azure App Service), men det körs inte på ditt innehåll.

Genomsök alla filer som laddas upp till Azure-resurser som inte är Compute, till exempel App Service, Data Lake Storage, Blob Storage osv.

Använd Azure Security Center s hot identifiering för data tjänster för att identifiera skadlig kod som laddats upp till lagrings konton.

* [Förstå Microsoft Antimalware för Azure Cloud Services och Virtual Machines](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

* [Förstå Azure Security Centers hot identifiering för data tjänster](https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: se till att program vara och signaturer för skadlig program vara uppdateras

**Vägledning**: när du har distribuerat installeras automatiskt de senaste signatur-, plattforms-och motor uppdateringar som standard i Microsoft Antimalware för Azure. Följ rekommendationerna i Azure Security Center: "Compute &amp; Apps" för att se till att alla slut punkter är uppdaterade med de senaste signaturerna. Windows OS kan skyddas ytterligare med ytterligare säkerhet för att begränsa risken för virus-eller skadlig kodbaserade attacker med tjänsten Microsoft Defender Avancerat skydd som kan integreras med Azure Security Center.

* [Så här distribuerar du Microsoft Antimalware för Azure Cloud Services och Virtual Machines](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

* [Microsoft Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [säkerhets kontroll: Data återställning](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: se till att vanlig automatisk säkerhets kopiering UPS

**Vägledning**: om du kör integration runtime på en virtuell Azure-dator (VM) aktiverar du Azure Backup och konfigurerar den virtuella datorn, samt önskad frekvens och kvarhållningsperiod för automatisk säkerhets kopiering.

För alla dina data lager kan du se den här tjänstens säkerhets bas linje för rekommendationer om hur du utför regelbundna, automatiserade säkerhets kopieringar.

* [En översikt över säkerhets kopiering av virtuella Azure-datorer](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)

* [Säkerhetskopiera en virtuell Azure-dator från VM-inställningarna](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: om du kör integration runtime på en virtuell Azure-dator (VM) aktiverar du Azure Backup och virtuella Azure-datorer, samt önskad frekvens och kvarhållningsperiod. Säkerhetskopiera Kundhanterade nycklar inom Azure Key Vault.

För alla dina data lager kan du se den här tjänstens säkerhets bas linje för rekommendationer om hur du utför regelbundna, automatiserade säkerhets kopieringar.

* [En översikt över säkerhets kopiering av virtuella Azure-datorer](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)

* [Så här säkerhetskopierar du nyckel valv nycklar i Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: om du kör integration runtime på en virtuell Azure-dator, så se till att du regelbundet kan utföra Data återställning av innehåll i Azure Backup. Om det behövs kan du testa att återställa innehåll till ett isolerat VLAN. Testa regelbundet återställning av säkerhetskopierade nycklar som hanteras av kunden.

Information om hur du verifierar säkerhets kopior finns i tjänstens säkerhets bas linje för alla dina data lager.

* [Återställa filer från säkerhets kopiering av virtuella Azure-datorer](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm)

* [Återställa Key Vault-nycklar i Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: om du kör integration runtime på en virtuell Azure-dator (VM) och du säkerhetskopierar den virtuella datorn med Azure Backup, krypteras den virtuella datorn i vila med kryptering för lagringstjänst (SSE). Azure Backup kan också säkerhetskopiera virtuella Azure-datorer som krypteras med hjälp av Azure Disk Encryption. Azure Disk Encryption integreras med BitLocker-BEKs (-krypterings nycklar) som skyddas i ett nyckel valv som hemligheter. Azure Disk Encryption integreras också med Azure Key Vault Key Encryption Keys (KeyExchange). Aktivera mjuk borttagning i Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning.

* [Mjuk borttagning för virtuella datorer](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#soft-delete)

* [Översikt av mjuk borttagning för Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [säkerhets kontroll: incident svar](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: Bygg ut en incident svars guide för din organisation. Se till att det finns skriftliga svars planer för incidenter som definierar alla personal roller och faser för incident hantering/hantering från identifiering till granskning efter incidenten.

* [Konfigurera automatisering av arbets flöden i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

* [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Kunden kan också utnyttja NISTs hanterings guide för dator säkerhet för att hjälpa till med att skapa egna incident svars planer](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller det analytiska som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Dessutom är det tydligt att markera prenumerationer (t. ex. produktion, icke-Prod) och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser.

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems incident svars funktioner på en vanlig takt. Identifiera svaga punkter och luckor och ändra planen efter behov.

* [Se NIST: guide för test, utbildning och övnings program för IT-planer och funktioner](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att kundens data har öppnats av en olaglig eller obehörig part. Granska incidenter när du är säker på att problemen är lösta.

* [Så här ställer du in Azure Security Center säkerhets kontakt](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Azure Security Center Data Connector för att strömma aviserings indikatorn.

* [Så här konfigurerar du kontinuerlig export](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Strömma aviseringar till Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för automatisering av arbets flöden i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer.

* [Konfigurera automatisering av arbets flöden och Logic Apps](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstester och Red Team-tester

*Mer information finns i [säkerhets kontroll: inträngande tester och röda team övningar](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister

**Vägledning**: 

* [Följ Microsofts regler för engagemang för att se till att dina inträngande tester inte strider mot Microsofts principer](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Du hittar mer information om Microsofts strategi och körning av röda team indelning och inträngande av direktsända webbplatser mot Microsoft-hanterad moln infrastruktur, tjänster och program](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: delat

## <a name="next-steps"></a>Nästa steg

- Se [Azures säkerhets benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Läs mer om [Azures säkerhets bas linjer](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
