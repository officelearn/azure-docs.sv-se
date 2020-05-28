---
title: Azures säkerhets bas linje för Azure Databricks
description: Azures säkerhets bas linje för Azure Databricks
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 229485b5aa1bf3a97429c8d82c4c962cd9fab5c7
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84016346"
---
# <a name="azure-security-baseline-for-azure-databricks"></a>Azures säkerhets bas linje för Azure Databricks

Azures säkerhets bas linje för Azure Databricks innehåller rekommendationer som hjälper dig att förbättra säkerhets position för din distribution.

Bas linjen för den här tjänsten hämtas från [Azures prestandatest version 1,0](https://docs.microsoft.com/azure/security/benchmarks/overview), som ger rekommendationer om hur du kan skydda dina moln lösningar i Azure med våra bästa praxis rikt linjer.

Mer information finns i [Översikt över Azure Security-bas linjer](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets kontroll: nätverks säkerhet](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: skydda resurser med hjälp av nätverks säkerhets grupper eller Azure-brandvägg på din Virtual Network

**Vägledning**: Distribuera Azure Databricks i ditt eget virtuella Azure-nätverk (VNet). Standard distributionen av Azure Databricks är en fullständigt hanterad tjänst på Azure: alla data Plans resurser, inklusive ett VNet som alla kluster kommer att associeras med, distribueras till en låst resurs grupp. Om du behöver anpassning av nätverk kan du dock Distribuera Azure Databricks data Plans resurser i ditt eget virtuella nätverk (VNet-insprutning), så att du kan implementera anpassade nätverkskonfigurationer. Du kan använda din egen nätverks säkerhets grupp (NSG) med anpassade regler för specifika begränsningar för utgående trafik.

Dessutom kan du konfigurera NSG-regler för att ange begränsningar för utgående trafik på det undernät som Azure Databricks-instansen distribueras till. Azure-brandväggen kan konfigureras för VNET-inmatade arbets ytor.

* [Så här distribuerar du Azure Databricks till dina egna Virtual Network](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Så här hanterar du NSG: er](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverkskort

**Vägledning**: Distribuera Azure Databricks i ditt eget virtuella Azure-nätverk (VNet). En nätverks säkerhets grupp (NSG) skapas inte automatiskt. Du måste skapa en bas linje NSG med endast Azures standard regler. När du distribuerar en arbets yta läggs regler som krävs av Databricks till. Du kan också starta med en tom NSG och rätt regler läggs till automatiskt. Aktivera NSG Flow-loggar och skicka loggar till ett lagrings konto för trafik granskningar. Du kan också skicka flödes loggar till en Log Analytics arbets yta och använda Trafikanalys för att ge insikter i trafikflödet i Azure-molnet. Några av fördelarna med Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks problem.

* [Så här distribuerar du Azure Databricks till dina egna Virtual Network](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Så här aktiverar du NSG Flow-loggar](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Så här aktiverar och använder du Trafikanalys](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Så här aktiverar du Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Aktivera Azure DDoS Protection standard på de virtuella Azure-nätverk som är kopplade till dina Azure Databricks instanser för skydd mot distribuerade DOS-attacker (Denial-of-Service). Använd Azure Security Center integrerad Hot information för att neka kommunikation med kända skadliga eller oanvända offentliga IP-adresser.

* [Hantera Azure DDoS Protection standard med hjälp av Azure Portal](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Förstå hot skydd för Azure Network Layer i Azure Security Center](https://docs.microsoft.com/azure/security-center/threat-protection#threat-protection-for-azure-network-layer-)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: registrera nätverks paket och flödes loggar

**Vägledning**: Distribuera Azure Databricks i ditt eget virtuella Azure-nätverk (VNet). En nätverks säkerhets grupp (NSG) skapas inte automatiskt. Du måste skapa en bas linje NSG med endast Azures standard regler. När du distribuerar en arbets yta läggs regler som krävs av Databricks till. Aktivera NSG Flow-loggar och skicka loggar till ett lagrings konto för trafik granskning. Du kan också skicka flödes loggar till en Log Analytics arbets yta och använda Trafikanalys för att ge insikter i trafikflödet i Azure-molnet. Några av fördelarna med Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks problem.

* [Så här distribuerar du Azure Databricks till dina egna Virtual Network](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Så här aktiverar du NSG Flow-loggar](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Så här aktiverar och använder du Trafikanalys](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Så här aktiverar du Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera Network-baserad intrångs identifiering/intrångs skydd system (ID/IP-adresser)

**Vägledning**: implementera en IDP-/IP-kompatibel virtuell nätverks installation (NVA) från Azure Marketplace för att skapa en virtuell nätverks integrerad arbets yta där alla Azure Databricks-kluster har en enda IP-utgående adress. Den enskilda IP-adressen kan användas som ett extra säkerhets lager med andra Azure-tjänster och-program som tillåter åtkomst baserat på vissa IP-adresser. Du kan använda en Azure-brandvägg eller andra verktyg från tredje part som NVA för att hantera ingress och utgående trafik.

Om intrångs identifiering och/eller skydd som baseras på nytto lasts granskning inte är ett krav kan du använda Azure-brandväggen med hot information. Azure Firewall Threat Intelligence-baserad filtrering kan varna och neka trafik till och från kända skadliga IP-adresser och domäner. IP-adresserna och domänerna är källor från Microsoft Threat Intelligence-flödet.

* [Så tilldelar du en enskild offentlig IP-adress för VNet-inmatade arbetsytor med hjälp av Azure Firewall](https://docs.microsoft.com/azure/databricks/kb/cloud/azure-vnet-single-ip)

* [Så här skapar du en NVA](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal)

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="17-manage-traffic-to-web-applications"></a>1,7: hantera trafik till webb program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: Använd service märken för att definiera nätverks åtkomst kontroller för nätverks säkerhets grupper som är kopplade till de undernät som din Azure Databricks instans är associerad med. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange service tag-namnet (t. ex. API Management) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras. Service märken stöds inte med icke-inmatade VNET-arbetsytor.

* [Förstå service märken](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: definiera och implementera nätverks säkerhets konfigurationerna för dina Azure Databricks-instanser med Azure policy. Du kan använda Azure Policy alias i namn området "Microsoft. Databricks" för att definiera anpassade princip definitioner. Principer gäller inte för resurser i den hanterade resurs gruppen.

Du kan också använda Azure-ritningar för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljö artefakter, till exempel Azure Resource Management-mallar, rollbaserad åtkomst kontroll (RBAC) och principer, i en enda skiss definition. Använd enkelt skissen på nya prenumerationer och miljöer och finjustera kontroll och hantering genom versions hantering.

* [Så här konfigurerar och hanterar du Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Förstå Azure Policy alias och definitions struktur](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure)

* [Så här skapar du en Azure Blueprint](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: Använd taggar för NSG: er och andra resurser som rör nätverks säkerhets-och trafikflöde som är associerade med din Azure Databricks-instans. För enskilda NSG-regler använder du fältet Beskrivning för att ange affärs behov och/eller varaktighet (osv.) för alla regler som tillåter trafik till/från ett nätverk.

* [Skapa och använda Taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka konfigurationer av nätverks resurser och identifiera ändringar för nätverks resurser som är relaterade till dina Azure Databricks-instanser. Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska nätverks resurser sker.

* [Visa och hämta Azure aktivitets logg händelser](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Så här skapar du aviseringar i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets kontroll: loggning och övervakning](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Använd godkända tids källor för synkronisering

**Vägledning**: Microsoft hanterar tids källor för Azure-resurser, men du har möjlighet att hantera tidssynkroniserings inställningarna för dina beräknings resurser. Eftersom Azure Databricks är en PaaS-tjänst har du inte direkt åtkomst till de virtuella datorerna i ett Azure Databricks kluster och kan inte ställa in tidssynkroniserings inställningar.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: mata in loggar via Azure Monitor för att samla in säkerhets data som genereras av Azure Databricks. I Azure Monitor kan du fråga arbets ytan Log Analytics som har kon figurer ATS för att ta emot dina Databricks och diagnostikloggar. Använd Azure Storage konton för långsiktig/Arkiverad logg lagring eller händelse nav för att exportera data till andra system. Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel eller en säkerhets incident och händelse hantering från tredje part (SIEM).

Obs: Azure Databricks diagnostikloggar kräver Azure Databricks Premiums plan

* [Konfigurera diagnostikinställningar](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-in-azure-portal)

* [Publicera Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Komma igång med Azure Monitor och SIEM-integrering från tredje part](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: Aktivera diagnostiska inställningar i Azure aktivitets logg och skicka loggarna till en Log Analytics-arbetsyta, Azure Event Hub eller Azure Storage-konto för arkivering. Med hjälp av Azures aktivitets logg data kan du avgöra vad, vem och när som helst för Skriv åtgärder (skicka, skicka och ta bort) på kontroll Plans nivån för dina Azure-resurser.

För gransknings loggning tillhandahåller Azure Databricks omfattande diagnostiska loggar från slut punkt till slut punkt av aktiviteter som utförs av Azure Databricks användare, så att företaget kan övervaka detaljerade Azure Databricks användnings mönster.

Obs: Azure Databricks diagnostikloggar kräver Azure Databricks Premiums plan

* [Så här aktiverar du diagnostikinställningar för Azure aktivitets logg](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Så här aktiverar du diagnostikinställningar för Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: samla in säkerhets loggar från operativ system

**Vägledning**: Azure Databricks ger omfattande diagnostiska loggar från slut punkt till slut punkt av aktiviteter som utförs av Azure Databricks användare, så att företaget kan övervaka detaljerade Azure Databricks användnings mönster.

Obs! Azure Databricks diagnostikloggar kräver Azure Databricks Premiums planen. Säkerhets loggning för operativ system är inte tillgängligt.

* [Så här aktiverar du diagnostikinställningar för Azure aktivitets logg](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Så här aktiverar du diagnostikinställningar för Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: Aktivera diagnostikinställningar för Azure Databricks. Om du väljer att lagra loggar i en Log Analytics arbets yta, ställer du in Log Analytics arbets ytans kvarhållningsperiod enligt organisationens regler för efterlevnad. Använd Azure Storage konton för långsiktig/Arkiv lagring. Säkerhetsrelaterade aktiviteter spåras i Databricks gransknings loggar.

Obs: Azure Databricks diagnostikloggar kräver Azure Databricks Premiums plan

* [Så här aktiverar du diagnostikinställningar i Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [Ange logg lagrings parametrar för Log Analytics arbets ytor](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: Aktivera diagnostikinställningar för Azure Databricks och skicka loggar till en Log Analytics arbets yta. Använd arbets ytan Log Analytics för att analysera och övervaka dina Azure Databrickss loggar för avvikande beteende och regelbundet granska resultaten.

Alternativt kan du aktivera och fordonsbaserad information till Azure Sentinel eller en SIEM från tredje part.

Obs: Azure Databricks diagnostikloggar kräver Azure Databricks Premiums plan

* [Så här aktiverar du diagnostikinställningar i Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [Så här frågar du Azure Databricks loggar som skickas till Log Analytics-arbetsytan](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#analyze-diagnostic-logs)

* [Publicera Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: aktivera aviseringar för avvikande aktivitet

**Vägledning**: Konfigurera diagnostikinställningar för Azure Databricks-instansen och skicka loggar till Log Analytics-arbetsytan. I arbets ytan Log Analytics konfigurerar du aviseringar så att de äger rum när en fördefinierad uppsättning villkor inträffar.

Alternativt kan du aktivera och fordonsbaserad information till Azure Sentinel eller en SIEM från tredje part.

Obs: Azure Databricks diagnostikloggar kräver Azure Databricks Premiums plan

* [Så här skickar du Azure Databricks loggar till Log Analytics-arbetsytan](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#configure-diagnostic-log-delivery)

* [Så här konfigurerar du aviseringar i Log Analytics arbets yta](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod

**Vägledning**: inte tillämpligt.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="29-enable-dns-query-logging"></a>2,9: Aktivera loggning av DNS-frågor

**Vägledning**: ej tillämpligt; Azure Databricks bearbetar eller skapar inte tillgängliga DNS-relaterade loggar för användare.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="210-enable-command-line-audit-logging"></a>2,10: Aktivera loggning av kommando rads granskning

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [säkerhets kontroll: identitets-och åtkomst kontroll](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: du kan använda Azure Databricks scim-API: er för att hantera användare i en Azure Databricks arbets yta och bevilja administratörs behörighet till angivna användare. Du kan också hantera dem via Azure Databricks gränssnittet.

* [Använda SCIM-API: er](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Lägga till och ta bort användare i Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: Azure Databricks använder Azure Active Directory (AD) för att ge åtkomst till Azure Portal samt Azure Databricks administratörs konsolen. Azure AD har inte begreppet standard lösen ord, men du är ansvarig för att ändra eller inte tillåta standard lösen ord för anpassade program och program från tredje part.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: du kan använda Azure Databricks scim-API: er för att lägga till användare med administratörs behörighet i en Azure Databricks. Du kan också hantera dem via Azure Databricks gränssnittet.

* [Använda SCIM-API: er](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Lägga till och ta bort användare i Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Använd enkel inloggning (SSO) med Azure Active Directory

**Vägledning**: Azure Databricks konfigureras automatiskt för att använda Azure Active Directory enkel inloggning för att autentisera användare. Användare utanför organisationen måste slutföra Inbjudnings processen och läggas till i Active Directory klient organisationen innan de kan logga in på Azure Databricks via enkel inloggning. Du kan implementera SCIM för att automatisera etablering och avetablering av användare från arbets ytor.

* [Använda SCIM-API: er](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Förstå enkel inloggning för Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [Så här bjuder du in användare till Azure AD-klienten](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Microsoft

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory-baserad åtkomst.

**Vägledning**: Aktivera Azure AD MFA och följ Azure Security Center rekommendationer för identitets-och åtkomst hantering.

* [Så här aktiverar du MFA i Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Övervaka identitet och åtkomst i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd Paw (arbets stationer med privilegie rad åtkomst) med MFA konfigurerat för att logga in på och konfigurera Azure-resurser.

* [Lär dig mer om arbets stationer med privilegie rad åtkomst](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Så här aktiverar du MFA i Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkt aktivitet från administrativa konton

**Vägledning**: Använd Azure Active Directory säkerhets rapporter för att skapa loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön. Använd Azure Security Center för att övervaka identitets-och åtkomst aktiviteter. Dessutom kan du utnyttja Azure Databricks diagnostikloggar.

* [Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [Övervaka användarnas identitets-och åtkomst aktiviteter i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: Använd villkorlig åtkomst med namngivna platser för att tillåta åtkomst från enbart vissa logiska grupperingar av IP-adressintervall eller länder/regioner.

* [Så här konfigurerar du namngivna platser i Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: Azure Databricks konfigureras automatiskt för att använda Azure Active Directory enkel inloggning för att autentisera användare. Användare utanför organisationen måste slutföra Inbjudnings processen och läggas till i Active Directory klient organisationen innan de kan logga in på Azure Databricks via enkel inloggning.

* [Förstå enkel inloggning för Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [Så här bjuder du in användare till Azure AD-klienten](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Microsoft

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: Azure AD innehåller loggar som hjälper till att identifiera inaktuella konton. Dessutom kan du använda Azure Identity Access-granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användar åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst. Du kan också implementera SCIM-API: er och Azure Databricks diagnostikloggar för att granska användar åtkomst. Du kan också använda SCIM-API: er och Azure Databricks diagnostikloggar för att granska användar åtkomst.

Dessutom kan du regelbundet granska och hantera användar åtkomst i Azure Databricks administratörs konsolen.

* [Azure AD-rapportering](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Så här använder du granskningar av Azure Identity Access](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [Hantera användar åtkomst i Azure Databricks administratörs konsolen](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: övervaka försök att komma åt inaktiverade konton

**Vägledning**: du har till gång till inloggnings aktiviteter, gransknings-och risk händelse logg källor i Azure AD, vilket gör att du kan integrera med alla Siem/övervaknings verktyg. Du kan också använda Azure Databricks diagnostikloggar för att granska användar åtkomst aktiviteten.

Du kan effektivisera processen genom att skapa diagnostikinställningar för Azure Active Directory användar konton och skicka gransknings loggar och inloggnings loggar till en Log Analytics arbets yta. Du kan konfigurera önskade aviseringar i Log Analytics arbets ytan.

* [Använda SCIM-API: er](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Så här integrerar du Azures aktivitets loggar i Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: avisering om beteende för beteende för konto inloggning

**Vägledning**: Använd funktioner i Azure AD-risk och identitets skydd för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör användar identiteter. Du kan också mata in data i Azure Sentinel för ytterligare undersökning. Du kan också använda Azure Databricks diagnostikloggar för att granska användar åtkomst aktiviteten.

* [Visa Azure AD-riskfyllda inloggningar](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Så här konfigurerar och aktiverar du risk principer för identitets skydd](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Publicera Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: ge Microsoft åtkomst till relevant kund information under support scenarier

**Vägledning**: när support ärenden är öppna ber kund service och support tekniker dig att få åtkomst till relevanta kund uppgifter.

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvars område**: inte tillgängligt för tillfället

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [säkerhets kontroll: data skydd](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för att spåra Azure Databricks instanser som bearbetar känslig information.

* [Skapa och använda Taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera separata prenumerationer och/eller hanterings grupper för utveckling, testning och produktion. Standard distributionen av Azure Databricks är en fullständigt hanterad tjänst som distribueras i ett eget virtuellt nätverk. Om du behöver anpassning av nätverk kan du Distribuera Azure Databricks i ditt eget virtuella nätverk. Det är en bra idé att skapa separata Azure Databricks arbets ytor för olika affärs team eller avdelningar.

* [Så här distribuerar du Azure Databricks till dina egna Virtual Network](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Så här skapar du ytterligare Azure-prenumerationer](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Så här skapar du Hanteringsgrupper](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Så här skapar du en Virtual Network](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information.

**Vägledning**: Följ Databricks "exfiltrering Protection Architecture" för att minimera risken för data exfiltrering.

* [Data exfiltrering Protection med Azure Databricks](https://databricks.com/blog/2020/03/27/data-exfiltration-protection-with-azure-databricks.html)

Microsoft hanterar den underliggande infrastrukturen för Azure Databricks och har implementerat strikta kontroller för att förhindra förlust eller exponering av kund information.

* [Förstå kundens data skydd i Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvars område**: inte tillgängligt för tillfället

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: Microsoft förhandlar TLS 1,2 som standard när du administrerar din Azure Databricks-instans via Azure Portal eller Azure Databricks-konsolen. Databricks-webbappen stöder TLS 1,3.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: inte tillgänglig för tillfället. funktionerna för data identifiering, klassificering och förlust av förlust är inte tillgängliga för Azure Databricks. Tagga Azure Databricks instanser och relaterade resurser som kan bearbeta känslig information som sådan och implementera en lösning från tredje part om det krävs för efterlevnad.

Databricks-plattformen är enbart beräknings-och alla data lagras på andra Azure-datatjänster. För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

* [Förstå kundens data skydd i Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvars område**: inte tillgängligt för tillfället

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Använd Azure RBAC för att kontrol lera åtkomsten till resurser

**Vägledning**: i Azure Databricks kan du använda åtkomst kontrol listor (ACL: er) för att konfigurera behörighet att komma åt data tabeller, kluster, pooler, jobb och arbets ytans objekt som antecknings böcker, experiment och mappar. Alla administratörs användare kan hantera åtkomst kontrol listor, som kan användare som har fått delegerade behörigheter att hantera åtkomst kontrol listor. Du kan använda Azure RBAC för att ange behörigheter på arbets ytan Azure Databricks.

Obs!: tabell, kluster, pool, jobb och åtkomst till arbets yta är bara tillgängliga i Azure Databricks Premium planen

* [Hantera åtkomst kontroll i Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/access-control/)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Använd värdbaserade data förlust skydd för att genomdriva åtkomst kontroll

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

Microsoft hanterar den underliggande infrastrukturen för Azure Databricks och har implementerat strikta kontroller för att förhindra förlust eller exponering av kund information.

* [Förstå kundens data skydd i Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: en Azure Databricks arbets yta består av ett hanterings plan som finns i ett Azure Databricks hanterat virtuellt nätverk och ett data plan som distribueras i ett kundhanterat virtuellt nätverk. Kontroll planet lagrar alla användares antecknings böcker och tillhör ande Notebook-resultat i en databas. Som standard krypteras alla antecknings böcker och resultat i vila med en annan krypterings nyckel.

Databricks File System (DBFS) är ett distribuerat fil system som monteras i en Azure Databricks-arbetsyta och är tillgängligt på Azure Databricks-kluster. DBFS implementeras som ett lagrings konto i din Azure Databricks arbets ytans hanterade resurs grupp. Som standard krypteras lagrings kontot med Microsoft-hanterade nycklar. Dina data lagras i Azure-datatjänster som du äger och du kan välja att kryptera den. Det rekommenderas inte att använda DBFS för att lagra produktions data

Obs! de här funktionerna är inte tillgängliga för alla Azure Databricks prenumerationer. Kontakta din Microsoft-eller Databricks-ansvarige för att begära åtkomst.

* [Aktivera Kundhanterade nycklar för Azure Databricks antecknings böcker](https://docs.microsoft.com/azure/databricks/security/customer-managed-key-notebook)

* [Aktivera Kundhanterade nycklar för Azure Databricks fil system](https://docs.microsoft.com/azure/databricks/security/customer-managed-keys-dbfs)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: delat

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets logg för att skapa aviseringar för när ändringar sker i kritiska Azure Databricks arbets ytor.

* [Så här skapar du aviseringar för Azure aktivitets logg händelser](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [säkerhets kontroll: sårbarhets hantering](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg

**Vägledning**: implementera en sårbarhets hanterings lösning från tredje part.

Om du har en plattforms prenumeration för sårbarhets hantering kan du använda Azure Databricks initierings skript för att installera sårbarhets bedömnings agenter på dina Azure Databricks klusternoder och hantera noderna via respektive Portal. Observera att alla lösningar från tredje part fungerar annorlunda.

* [Så här installerar du Rapid7-agenten manuellt](https://insightagent.help.rapid7.com/docs/install)

* [Så här installerar du Qualys-agenten manuellt](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Azure Databricks initierings skript](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Distribuera automatiserad hanterings lösning för operativ system

**Vägledning**: Microsoft underhåller Azure Databricks Base-avbildningar för klusternoder, men du ansvarar för att se till att dina klusternoder förblir korrigerade. Om du vill lägga till en underhålls uppdatering i ett befintligt kluster som körs måste du starta om klustret.

* [Förstå uppdateringar för körnings underhåll för Azure Databricks](https://docs.microsoft.com/azure/databricks/release-notes/runtime/maintenance-updates)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Distribuera automatisk hanterings lösning för program uppdatering från tredje part

**Vägledning**: Microsoft underhåller Azure Databricks Base-avbildningar för klusternoder, men du ansvarar för att se till att program från tredje part som du installerar förblir korrigerade.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: jämför sökningar efter säkerhets risker

**Vägledning**: implementera en sårbarhets hanterings lösning från tredje part som har möjlighet att jämföra sårbara genomsökningar över tid. Om du har en sårbarhets hanterings prenumeration kan du använda leverantörens Portal för att visa och jämföra genomsökningar med säkerhets risker från säkerhets kopiering. Observera att alla lösningar från tredje part fungerar annorlunda.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker.

**Vägledning**: Använd ett gemensamt risk bedömnings program (t. ex. vanliga sårbarhets bedömnings system) eller standard risk klassificeringarna som tillhandahålls av genomsöknings verktyget från tredje part.

**Azure Security Center övervakning**: ej tillämpligt

**Ansvar**: kund

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [säkerhets kontroll: inventering och till gångs hantering](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Använd Azure Asset Discovery

**Vägledning**: Använd Azure Resource Graph för att fråga/identifiera alla resurser (t. ex. data bearbetning, lagring, nätverk, portar och protokoll osv.) i din prenumeration (er). Se till att det finns tillräckliga (Läs) behörigheter i din klient organisation och räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

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

Använd dessutom Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:
- Otillåtna resurstyper
- Tillåtna resurstyper

* [Så här skapar du ytterligare Azure-prenumerationer](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Så här skapar du Hanteringsgrupper](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Skapa och använda Taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: underhåll av inventering av godkända Azure-resurser och program varu titlar.

**Vägledning**: definiera godkända Azure-resurser och godkänd program vara för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:
- Otillåtna resurstyper
- Tillåtna resurstyper

Använd Azure Resource Graph för att fråga/identifiera resurser i sina prenumerationer. Se till att alla Azure-resurser som finns i miljön är godkända.

* [Så här konfigurerar och hanterar du Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Så här skapar du frågor med Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: övervaka för program som inte godkänts i beräknings resurser

**Vägledning**: ej tillämpligt; Azure Databricks är en PaaS-tjänst har kunderna inte direkt åtkomst till de virtuella datorerna i ett Azure Databricks-kluster.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program

**Vägledning**: Använd Azure Resource Graph för att fråga/identifiera alla resurser (t. ex. data bearbetning, lagring, nätverk, portar och protokoll osv.), inklusive Azure Databricks instanser, i dina prenumerationer. Ta bort alla ej godkända Azure-resurser som du identifierar. För Azure Databricks klusternoder implementerar du en lösning från tredje part för att ta bort eller varna på ej godkänd program vara.

* [Så här skapar du frågor med Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="68-use-only-approved-applications"></a>6,8: Använd endast godkända program

**Vägledning**: ej tillämpligt; Azure Databricks är en PaaS-tjänst har du inte direkt åtkomst till de virtuella datorerna i ett Azure Databricks-kluster.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:
- Otillåtna resurstyper
- Tillåtna resurstyper

* [Så här konfigurerar och hanterar du Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Så här nekar du en speciell resurs typ med Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="610-implement-approved-application-list"></a>6,10: implementera lista över godkända program

**Vägledning**: ej tillämpligt; Azure Databricks är en PaaS-tjänst har du inte direkt åtkomst till de virtuella datorerna i ett Azure Databricks-kluster.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6,11: <div>Begränsa användarnas möjlighet att interagera med Azure Resource Manager via skript</div>

**Vägledning**: Använd villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

* [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: begränsa användarnas möjlighet att köra skript i beräknings resurser

**Vägledning**: ej tillämpligt; Detta gäller inte för Azure Databricks som användare (icke-administratörer) av klustret behöver inte åtkomst till de enskilda noderna för att köra jobb. Kluster administratören har rot åtkomst till alla klusternoder som standard.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fysiskt eller logiskt särskiljande program med hög risk

**Vägledning**: ej tillämpligt; Benchmark är avsett för Azure Apps-tjänster eller data bearbetnings resurser som är värdar för webb program.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [säkerhets kontroll: säker konfiguration](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: definiera och implementera standardkonfigurationer för dina Azure Databricks-instanser med Azure policy. Använd Azure Policy alias i namn området "Microsoft. Databricks" om du vill skapa anpassade principer för granskning eller tillämpa konfigurationen för dina Azure Databricks-instanser. Observera att alla principer som tillämpas inte fungerar i den hanterade resurs gruppen Databricks.

* [Visa tillgängliga Azure Policy alias](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Så här konfigurerar och hanterar du Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: upprätta säkra konfigurationer för operativ system

**Vägledning**: ej tillämpligt; Azure Databricks är en PaaS-tjänst har du inte direkt åtkomst till de virtuella datorerna i ett Azure Databricks-kluster.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: definiera och implementera standardkonfigurationer för dina Azure Databricks-instanser med Azure policy. Använd Azure Policy alias i namn området "Microsoft. Databricks" om du vill skapa anpassade principer för granskning eller tillämpa konfigurationen för dina Azure Databricks-instanser. Använd Azure policy [Deny] och [distribuera om det inte finns] för att genomdriva säkra inställningar i dina Azure-resurser.

* [Så här konfigurerar och hanterar du Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Förstå Azure Policys effekter](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: underhåll säkra konfigurationer för operativ system

**Vägledning**: Azure Databricks operativ Systems avbildningar som hanteras och underhålls av Microsoft. Du ansvarar för att implementera tillstånds konfigurationen på operativ system nivå.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: delat

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: om du använder anpassade definitioner av Azure-principer använder du Azure DevOps eller Azure databaser för att lagra och hantera din kod på ett säkert sätt.

* [Så här lagrar du kod i Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Dokumentation om Azure databaser](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: lagra anpassade operativ Systems avbildningar på ett säkert sätt

**Vägledning**: om du använder anpassade avbildningar för Azure Databricks klusternoder kan du skicka den anpassade bas avbildningen till ett Docker-register som Azure Container Registry (ACR).

* [Anpassa behållare med Databricks Container Services](https://docs.microsoft.com/azure/databricks/clusters/custom-containers)

* [Förstå Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: Distribuera hanterings verktyg för system konfiguration

**Vägledning**: definiera och implementera standardkonfigurationer för dina Azure Databricks-instanser med Azure policy. Använd Azure Policy alias i namn området "Microsoft. Databricks" om du vill skapa anpassade principer för granskning eller tillämpa konfigurationen för dina Azure Databricks-instanser.

* [Så här konfigurerar och hanterar du Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Visa tillgängliga Azure Policy alias](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Så här konfigurerar och hanterar du Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: distribuera system konfigurations hanterings verktyg för operativ system

**Vägledning**: ej tillämpligt; Azure Databricks är en PaaS-tjänst har du inte direkt åtkomst till de virtuella datorerna i ett Azure Databricks-kluster.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: implementera automatisk konfigurations övervakning för Azure-tjänster

**Vägledning**: definiera och implementera standardkonfigurationer för dina Azure Databricks-instanser med Azure policy. Använd Azure Policy alias i namn området "Microsoft. Databricks" om du vill skapa anpassade principer för granskning eller tillämpa konfigurationen för dina Azure Databricks-instanser.

* [Visa tillgängliga Azure Policy alias](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Så här konfigurerar och hanterar du Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementera automatisk konfigurations övervakning för operativ system

**Vägledning**: implementera en lösning från tredje part för att övervaka status för Azure Databricks-klusternodens operativ system.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: Använd Azure Key Vault med en Azure Databricks hemlig omfattning för att hantera och använda hemligheter på ett säkert sätt.

* [Använda Azure Key Vault med Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/store-secrets-azure-key-vault)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

**Vägledning**: inte tillgänglig för tillfället. Hanterade identiteter är för närvarande inte tillgängliga för Azure Databricks

* [Tjänster som stöder hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvars område**: inte tillgängligt för tillfället

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Den här skannern uppmuntrar också att flytta identifierade autentiseringsuppgifter till säkrare platser som Azure Key Vault.

* [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [säkerhets kontroll: försvar mot skadlig kod](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Använd centralt hanterat program mot skadlig kod

**Vägledning**: om du har en plattforms prenumeration för sårbarhets hantering kan du använda Azure Databricks initierings skript för att installera sårbarhets bedömnings agenter på dina Azure Databricks klusternoder och hantera noderna via respektive Portal. Observera att alla lösningar från tredje part fungerar annorlunda.

* [Så här installerar du Rapid7-agenten manuellt](https://insightagent.help.rapid7.com/docs/install)

* [Så här installerar du Qualys-agenten manuellt](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Azure Databricks initierings skript](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: Microsoft Antimalware är aktiverat på den underliggande värden som stöder Azure-tjänster (till exempel Azure App Service), men det körs inte på ditt innehåll.

Skanna alla filer som överförs till Azure Databricks klusternoder eller relaterade resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: se till att program vara och signaturer för skadlig program vara uppdateras

**Vägledning**: om du har en plattforms prenumeration för sårbarhets hantering kan du använda Azure Databricks initierings skript för att installera sårbarhets bedömnings agenter på dina Azure Databricks klusternoder och hantera noderna via respektive Portal. Observera att alla lösningar från tredje part fungerar annorlunda.

* [Så här installerar du Rapid7-agenten manuellt](https://insightagent.help.rapid7.com/docs/install)

* [Så här installerar du Qualys-agenten manuellt](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Azure Databricks initierings skript](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [säkerhets kontroll: Data återställning](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: se till att vanlig automatisk säkerhets kopiering UPS

**Vägledning**: för dina Azure Databricks data källor kontrollerar du att du har konfigurerat en lämplig nivå av dataredundans för ditt användnings fall. Om du till exempel använder ett Azure Storage konto för din Azure Databricks data lager väljer du lämpligt alternativ för redundans (LRS, ZRS, GRS, RA-GRS).

* [Data källor för Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-connect-to-data-sources#data-sources-for-azure-databricks)

* [Regional haveri beredskap för Azure Databricks kluster](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: säkerhetskopiera alla Kundhanterade nycklar som är relaterade till dina Azure Databricks implementeringar inom Azure Key Vault. Du kan också använda REST API och CLI för att skapa en daglig säkerhets kopiering av Databricks-konfigurationer. Du kan också använda REST API/CLI för att skapa en daglig säkerhets kopiering av Databricks-konfigurationer.

* [Så här säkerhetskopierar du nyckel valv nycklar i Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: testa återställning av säkerhetskopierade Kundhanterade nycklar som rör dina Azure Databricks-implementeringar.

* [Återställa Key Vault-nycklar i Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: se till att mjuk borttagning är aktiverat i Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning.

* [Så här aktiverar du mjuk borttagning i Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [säkerhets kontroll: incident svar](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: Bygg ut en incident svars guide för din organisation. Se till att det finns skriftliga svars planer för incidenter som definierar alla personal roller och faser för incident hantering/hantering från identifiering till granskning efter incidenten.

* [Konfigurera automatisering av arbets flöden i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

* [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Du kan också använda NISTs hanterings guide för dator säkerhet för att hjälpa till med att skapa egna incident svars planer](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller det analytiska som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Dessutom är det tydligt att markera prenumerationer (t. ex. produktion, icke-Prod) och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems incident svars funktioner på en vanlig takt. Identifiera svaga punkter och luckor och ändra planen efter behov.

* [Se NIST: guide för test, utbildning och övnings program för IT-planer och funktioner](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att kunddata har använts av en olaglig eller obehörig part. Granska incidenter när du är säker på att problemen är lösta.

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

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att åtgärda alla kritiska säkerhets brister inom 60 dagar.

**Vägledning**: * [Följ Microsofts regler för engagemang för att se till att dina inträngande tester inte strider mot Microsofts principer](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Du hittar mer information om Microsofts strategi och körning av röda team indelning och inträngande av direktsända webbplatser mot Microsoft-hanterad moln infrastruktur, tjänster och program](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: delat

## <a name="next-steps"></a>Nästa steg

- Se [Azures säkerhets benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Läs mer om [Azures säkerhets bas linjer](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
