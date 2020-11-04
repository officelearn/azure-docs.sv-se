---
title: Azures säkerhets bas linje för virtuellt WAN
description: Den virtuella WAN-säkerhetslösningen ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 409c8d163b52e56a1eef68a23e4db79956a68722
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93328727"
---
# <a name="azure-security-baseline-for-virtual-wan"></a>Azures säkerhets bas linje för virtuellt WAN

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark-version 2,0](../security/benchmarks/overview.md) för att Microsoft Azure virtuella WAN. Azures säkerhets benchmark ger rekommendationer om hur du kan skydda dina moln lösningar på Azure. Innehållet grupperas efter de **säkerhets kontroller** som definierats av Azures säkerhets benchmark och relaterade rikt linjer som gäller för virtuella WAN-nätverk. **Kontroller** som inte gäller för virtuellt WAN har uteslutits.

Om du vill se hur det virtuella WAN-nätverket mappas fullständigt till Azures säkerhets benchmark, se den [fullständiga virtuella WAN-filen för bas linje mappning](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets principen för Azure-säkerhet: nätverks säkerhet](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementera säkerhet för intern trafik

**Vägledning** : Azure Virtual WAN stöder inte distribution direkt till ett virtuellt nätverk. Du kan dock fortfarande använda regler för nätverks säkerhets grupper på eker-resurser, använda Azures brand Väggs skydd eller skapa anpassade väg tabeller för att förhindra eller tillåta privat trafik.

- [Scenarier för anpassad routning](scenario-any-to-any.md)

**Azure Security Center övervakning** : för närvarande inte tillgängligt

**Ansvar** : kund

### <a name="ns-2-connect-private-networks-together"></a>NS-2: Anslut privata nätverk till varandra 

**Vägledning** : Använd Azure ExpressRoute eller Azure Virtual Private Network (VPN) för att skapa privata anslutningar mellan Azure-datacenter och lokal infrastruktur i en samplacerings miljö. ExpressRoute-anslutningar går inte via offentligt Internet, ger mer tillförlitlighet, snabbare hastigheter och lägre fördröjning än vanliga Internet anslutningar. 

För punkt-till-plats-VPN och plats-till-plats-VPN ansluter du lokala enheter eller nätverk till ett virtuellt nätverk med valfri kombination av de här VPN-alternativen och ExpressRoute. Använd Virtual Network peering om du vill ansluta två eller flera virtuella nätverk i Azure tillsammans. Nätverks trafik mellan peer-kopplat virtuella nätverk är privat och lagras i Azures stamnät nätverk.

- [ExpressRoute i virtuellt WAN](virtual-wan-expressroute-portal.md)

- [Översikt över webbplats-till-plats-VPN](virtual-wan-site-to-site-portal.md)

- [Översikt över peka till plats-VPN](virtual-wan-point-to-site-portal.md)

- [Private Link](howto-private-link.md)

**Azure Security Center övervakning** : för närvarande inte tillgängligt

**Ansvars område** : delat

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: upprätta privat nätverks åtkomst till Azure-tjänster

**Vägledning** : Använd Azures privata länk om du vill aktivera privat åtkomst till Azure Virtual WAN från dina virtuella nätverk utan att passera Internet. Privat åtkomst är ett annat mått för djupgående djupgående förutom autentiserings-och trafik säkerhet som erbjuds av Azure-tjänster.

- [Förstå privat Azure-länk](../private-link/private-link-overview.md)

- [Privat virtuell WAN-länk](howto-private-link.md)

**Azure Security Center övervakning** : för närvarande inte tillgängligt

**Ansvar** : kund

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: skydda program och tjänster från externa nätverks attacker

**Vägledning** : skydda dina virtuella Azure-WAN-resurser mot attacker från externa nätverk, inklusive DDoS-attacker (distributed denial of Service), programspecifika attacker och oönskad och potentiellt skadlig Internet trafik. 

Använd Azure-brandväggen för att skydda program och tjänster mot potentiellt skadlig trafik från Internet och andra externa platser. Välj Azure DDoS Protection för dina till gångar mot attacker på dina virtuella Azure-nätverk. Använd Azure Security Center för att identifiera problem med felaktig konfiguration som rör dina nätverks relaterade resurser.

- [Dokumentation om Azure Firewall](/azure/firewall)

- [Hantera Azure DDoS Protection standard med hjälp av Azure Portal](/azure/virtual-network/manage-ddos-protection) 

- [Rekommendationer för Azure Security Center](../security-center/recommendations-reference.md#recs-network)

**Azure Security Center övervakning** : Ja

**Ansvars område** : delat

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: Distribuera intrångs identifiering/intrångs skydd system (ID/IP-adresser)

**Vägledning** : det bästa sättet att distribuera intrångs identifiering eller skydds system i Azure Virtual WAN använder en virtuell nätverks installation i ekrar som är anslutna till den virtuella hubben.  Mer information finns på cirkulations scenarierna vid de länkar som refereras till. 

- [Scenario – Dirigera trafik via NVA](scenario-route-through-nva.md)

**Azure Security Center övervakning** : för närvarande inte tillgängligt

**Ansvar** : kund

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: förenkla nätverks säkerhets regler

**Vägledning** : använd Azure Virtual Network Service-taggar för att definiera nätverks åtkomst kontroller i Azure nätverks säkerhets grupper eller Azure-brandvägg som kon figurer ATS för dina virtuella WAN-resurser. 

Använd tjänst Taggar i stället för vissa IP-adresser när du skapar säkerhets regler. Genom att ange namnet på service tag-koden (till exempel {VirtualWAN: Virtual Network}) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

**Azure Security Center övervakning** : för närvarande inte tillgängligt

**Ansvar** : kund

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: säker Domain Name Service (DNS)

**Vägledning** : Azure Virtual WAN erbjuder anpassade DNS-servrar för punkt-till-plats-VPN-gatewayer. 

Följ de rekommenderade metoderna för DNS-säkerhet för att undvika vanliga attacker som Dangling DNS, DNS-utlösnings attacker, DNS-förgiftning och förfalskning och så vidare.

När Azure DNS används som auktoritativ DNS-tjänst kontrollerar du att DNS-zoner och-poster skyddas från oavsiktlig eller skadlig ändring med hjälp av rollbaserad åtkomst kontroll i Azure (Azure RBAC) och resurs lås.

- [Översikt över Azure DNS](../dns/dns-overview.md) 

- [Distributions guide för säker Domain Name System (DNS)](https://csrc.nist.gov/publications/detail/sp/800-81/2/final) 

- [Förhindra Dangling DNS-poster och Undvik under domän övertag Ande](../security/fundamentals/subdomain-takeover.md)

**Azure Security Center övervakning** : för närvarande inte tillgängligt

**Ansvar** : kund

## <a name="identity-management"></a>Identitetshantering

*Mer information finns i [Azure Security benchmark: Identity Management](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: standardisera Azure Active Directory som system för central identitets-och autentisering

**Vägledning** : Azure Virtual WAN: s punkt-till-plats-VPN är integrerat med Azure Active Directory (Azure AD), som är Azures standard tjänst för identitets-och åtkomst hantering. Du bör standardisera Azure AD för att styra organisationens identitets-och åtkomst hantering i:

- Microsoft Cloud resurser, till exempel Azure Portal, Azure Storage, Azure Virtual Machines (Linux och Windows), Azure Key Vault, PaaS och SaaS-program.
- Organisationens resurser, till exempel program i Azure eller företagets nätverks resurser.

Skydda Azure AD med hög prioritet i din organisations säkerhets praxis för molnet. Utvärdera dina identitets-och säkerhets position med Azure Security Center säkerhets poängen för att mäta hur nära konfigurationen matchar rekommendationerna för bästa praxis. Implementera Microsofts rekommendationer för bästa praxis för förbättringar av din säkerhets position.

Azure AD stöder också externa identiteter, vilket gör det möjligt för användare utan Microsoft-konto att logga in på sina program och resurser med sin externa identitet. Läs informationen om hur du använder Azure AD i punkt-till-plats-VPN-scenarier på de refererade länkarna.

- [Skapa en Azure Active Directory-klient (AD) för P2S OpenVPN-protokoll anslutningar](openvpn-azure-ad-tenant-multi-app.md)

- [Konfigurera Azure Active Directory autentisering för användar-VPN](virtual-wan-point-to-site-azure-ad.md)

- [Innehav i Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Så här skapar och konfigurerar du en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Använda externa identitets leverantörer för program](/azure/active-directory/b2b/identity-providers)

**Azure Security Center övervakning** : Ja

**Ansvars område** : delat

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Använd starka verifierings kontroller för alla Azure Active Directory-baserade åtkomst

**Vägledning** : Azure Virtual WAN: s punkt-till-plats-VPN är integrerat med Azure Active Directory (Azure AD), som stöder starka verifierings kontroller med multifaktorautentisering och starka lösen ords lös metoder.

- Multifaktorautentisering – aktivera Azure AD multifaktor-autentisering och följ Azure Security Center rekommendationer för identitets-och åtkomst hantering för bästa praxis för konfigurationen av multifaktorautentisering. Multifaktorautentisering kan tillämpas på alla, välja användare eller på nivån per användare baserat på inloggnings villkor och risk faktorer.

- Autentisering med lösen ord – tre alternativ för lösen ords avföljer är tillgängliga. Dessa omfattar, Windows Hello för företag, Microsoft Authenticator app och lokala autentiseringsmetoder som smartkort.

För administratörer och privilegierade användare ser du till att den högsta nivån av metoden stark autentisering används, följt av en sammanslagning av lämplig princip för stark autentisering till andra användare.

- [Så här aktiverar du MFA i P2S VPN för virtuellt WAN](openvpn-azure-ad-mfa.md)

**Azure Security Center övervakning** : Ja

**Ansvars område** : delat

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: begränsa åtkomsten till Azure-resursen baserat på villkor

**Vägledning** : Azure Virtual WAN: s punkt-till-plats-VPN har stöd för villkorlig åtkomst i Azure AD för en mer detaljerad åtkomst kontroll baserat på användardefinierade villkor. Till exempel måste användar inloggningar från vissa IP-intervall använda multifaktorautentisering för att logga in. Detaljerad hanterings princip för autentisering kan också användas för olika användnings fall.

- [Vanliga principer för villkorlig åtkomst](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Konfigurera hantering av autentisering med villkorlig åtkomst](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

- [Villkorlig åtkomst för Virtual WAN P2S VPN](openvpn-azure-ad-mfa.md#conditional)

**Azure Security Center övervakning** : för närvarande inte tillgängligt

**Ansvar** : kund

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning** : implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Den här skannern uppmuntrar också att flytta identifierade autentiseringsuppgifter till säkrare platser som Azure Key Vault.

För GitHub kan du använda intern hemlighet-genomsökning för att identifiera autentiseringsuppgifter eller annan form av hemligheter i koden.

- [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html) 

- [GitHub Secret-genomsökning](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Azure Security Center övervakning** : för närvarande inte tillgängligt

**Ansvar** : kund

## <a name="privileged-access"></a>Privilegie rad åtkomst

*Mer information finns i [Azure Security benchmark: privilegie rad åtkomst](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: begränsa administrativ åtkomst till verksamhets kritiska system

**Vägledning** : Azure Virtual WAN använder Azures rollbaserade åtkomst kontroller (Azure RBAC) för att isolera åtkomst till affärs kritiska system genom att begränsa vilka konton som beviljas privilegie rad åtkomst till de prenumerationer och hanterings grupper som de finns i.

Begränsa också åtkomsten till hanterings-, identitets-och säkerhets system som har administrativ åtkomst till din affärs kritiska åtkomst, till exempel Active Directory-domän kontrollanter, säkerhets verktyg och system hanterings verktyg med agenter som är installerade på affärs kritiska system. Angripare som angriper dessa hanterings-och säkerhets system kan omedelbart weaponize dem för att kompromettera affärs kritiska till gångar.

Alla typer av åtkomst kontroller bör justeras mot din strategi för företags segmentering för att säkerställa en konsekvent åtkomst kontroll.

- [Azure-komponenter och referens modell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Åtkomst till hanterings grupp](../governance/management-groups/overview.md#management-group-access) 

- [Administratörer för Azure-prenumeration](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Security Center övervakning** : för närvarande inte tillgängligt

**Ansvar** : kund

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Använd arbets stationer med privilegie rad åtkomst

**Vägledning** : skyddade, isolerade arbets stationer är mycket viktiga för att skydda känsliga roller som administratörer, utvecklare och kritiska tjänst operatörer. Använd hög säkra användar arbets stationer eller Azure-skydds för administrativa uppgifter. Använd Azure Active Directory (Azure AD), Microsoft Defender Avancerat skydd (ATP) eller Microsoft Intune för att distribuera en säker och hanterad användar arbets station för administrativa uppgifter. De skyddade arbets stationerna kan hanteras centralt för att upprätthålla säker konfiguration, inklusive stark autentisering, program vara och maskin varu bas linjer, begränsad logisk och nätverks åtkomst.

- [Förstå arbets stationer med privilegie rad åtkomst](../active-directory/devices/concept-azure-managed-workstation.md)

- [Distribuera en privilegie rad åtkomst arbets Station](../active-directory/devices/howto-azure-managed-workstation.md)

**Azure Security Center övervakning** : för närvarande inte tillgängligt

**Ansvar** : kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security benchmark: Data Protection](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: kryptera känslig information under överföring

**Vägledning** : kryptering är avgörande för trafik på externa och offentliga nätverk.

- Använd åtkomst kontroller. data som överförs bör skyddas mot "out of band"-attacker (t. ex. trafik avbildning) med kryptering för att säkerställa att angripare inte kan läsa eller ändra data.

- Se till att alla klienter som ansluter till dina Azure-resurser kan förhandla TLS v 1.2 eller mer med HTTP-trafik.

- För fjärrhantering använder du SSH (för Linux) eller RDP/TLS (för Windows) i stället för okrypterat protokoll. Inaktuella SSL/TLS/SSH-versioner, protokoll och svaga chiffer bör inaktive ras.

- I den underliggande infrastrukturen tillhandahåller Azure Data överförings kryptering som standard för data trafik mellan Azure-datacenter.

I allmänhet tillhandahåller vi kryptering på säkra Microsoft-stamnät och-möjligheter att kryptera trafik i plats-till-plats-VPN, plats-till-plats-VPN över Azure ExpressRoute och användare med punkt-till-plats-VPN.

- [Förstå kryptering i överföring med Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Information om TLS-säkerhet](/security/engineering/solving-tls1-problem)

- [Double Encryption för Azure-data under överföring](../security/fundamentals/double-encryption.md#data-in-transit)

**Azure Security Center övervakning** : Ja

**Ansvars område** : delat

## <a name="asset-management"></a>Tillgångshantering

*Mer information finns i [Azure Security benchmark: Asset Management](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: se till att säkerhets teamet har åtkomst till till gångs inventering och metadata

**Vägledning** : Använd taggar för dina Azure-resurser, resurs grupper och prenumerationer för att logiskt organisera dem i en taxonomi. Varje tagg består av ett namn och ett värde-par. Du kan till exempel använda namnet ”Miljö” och värdet ”Produktion” för alla resurser i produktionsmiljön.

Azure Virtual WAN stöder också Azure Resource Manager baserade resurs distributioner och Azure Resource Graph-frågor. 

- [Mer information om att tagga till gångar finns i besluts guiden för resurs namn givning och taggning](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

- [Så här skapar du frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) 

- [Azure Security Center till gångs lager hantering](../security-center/asset-inventory.md)

**Azure Security Center övervakning** : Ja

**Ansvar** : kund

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Använd endast godkända Azure-tjänster

**Vägledning** : Använd Azure policy för att begränsa tjänster som kan tillhandahållas i din miljö. Fråga och identifiera resurser med Azure Resource Graph i dina prenumerationer och Använd Azure Monitor för att skapa regler för att utlösa aviseringar när en icke-godkänd tjänst upptäcks.

- [Så här konfigurerar och hanterar du Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en speciell resurs typ med Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Så här skapar du frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Azure Security Center övervakning** : för närvarande inte tillgängligt

**Ansvar** : kund

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning** : Använd villkorlig Azure-åtkomst för att begränsa användarnas möjlighet att interagera med Azures resurs hanterare genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center övervakning** : för närvarande inte tillgängligt

**Ansvar** : kund

## <a name="logging-and-threat-detection"></a>Loggning och hot identifiering

*Mer information finns i [säkerhets benchmark för Azure: loggning och hot identifiering](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Aktivera hot identifiering för Azure Identity and Access Management

**Vägledning** : Azure Active Directory (Azure AD) innehåller följande användar loggar som kan visas i Azure AD repor ting eller integreras med Azure Monitor, Azure SENTINEL, Siem eller övervaknings verktyg för mer avancerade övervaknings-och analys användnings fall. Dessa är:

- Logga in – inloggnings rapporten innehåller information om användningen av hanterade program och användar inloggnings aktiviteter.
- Granskningsloggar – Ger spårbarhet via loggar för alla ändringar som gjorts via olika funktioner i Azure AD. Exempel på gransknings loggar är ändringar som har gjorts i alla resurser i Azure AD, till exempel att lägga till eller ta bort användare, appar, grupper, roller och principer.
- Riskfylld inloggning – ett riskfylldt inloggning är en indikator för ett inloggnings försök som kan ha utförts av någon som inte är den legitima ägaren till ett användar konto.
- Användare som har flaggats för risk – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats.

Använd Azure Security Center för att skapa aviseringar för vissa misstänkta aktiviteter, till exempel ett ovanligt antal misslyckade autentiseringsförsök, inklusive föråldrade konton i prenumerationen. Förutom den grundläggande säkerhetsövervakningen av säkerhet använder du Security Center s skydds modul för att samla in mer detaljerade säkerhets aviseringar från enskilda Azure Compute-resurser (virtuella datorer, behållare, App Service), data resurser (SQL DB och lagring) och Azure Service-lager. Med den här funktionen kan du få insyn i konto avvikelser i de enskilda resurserna.

- [Granska aktivitets rapporter i Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Aktivera Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

**Azure Security Center övervakning** : Ja

**Ansvars område** : delat

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Aktivera loggning för Azure nätverks aktiviteter

**Vägledning** : använda verktyg för VPN-pakets insamling för att registrera nätverks paket som överförs mellan dina virtuella Azure-resurser i WAN. Detta kan hjälpa till med fel söknings processen som är relaterad till ditt hybrid nätverk. Även om du inte kan distribuera en nätverks säkerhets grupp på ett virtuellt WAN-nätverk kan du distribuera den till dina eker-resurser för virtuella nätverk.

Aktivera flödes loggar för nätverks säkerhets grupper i dina nätverks säkerhets grupper för trafik granskning.

Aktivera Azure Trafikanalys-funktionen för att bearbeta flödes loggar som finns kvar i lagrings kontot och skicka dem sedan till en Log Analytics arbets yta. Trafikanalys ger ytterligare insikter i trafikflödet för dina Azure-nätverk. Några av fördelarna med Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks problem.

- [Så här aktiverar du flödes loggar för nätverks säkerhets grupper](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Så här aktiverar och använder du Trafikanalys](../network-watcher/traffic-analytics.md) 

Virtual WAN skapar eller bearbetar inte DNS-frågemeddelanden som behöver aktive ras.

**Azure Security Center övervakning** : för närvarande inte tillgängligt

**Ansvar** : kund

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Aktivera loggning för Azure-resurser

**Vägledning** : Azure aktivitets loggar, aktive ras automatiskt, innehåller alla Skriv åtgärder (placera, skicka, ta bort) för dina virtuella Azure-resurser förutom Läs åtgärder (get). Aktivitets loggar kan användas för att hitta ett fel under fel sökning eller för att övervaka hur en användare i organisationen ändrade en resurs. Virtuella WAN-nätverk skapar dock inte Azure-resurs loggar.

- [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 
- [Förstå loggning och olika logg typer i Azure](../azure-monitor/platform/platform-logs-overview.md)

**Azure Security Center övervakning** : Ja

**Ansvars område** : delat

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: centralisera hantering av säkerhets loggar och analys

**Vägledning** : centralisera loggning av lagring och analys för att aktivera korrelation. För varje logg källa ser du till att du har tilldelat en data ägare, åtkomst vägledning, lagrings plats, vilka verktyg som används för att bearbeta och komma åt data samt krav för data lagring. Se till att du integrerar Azures aktivitets loggar i dina centrala loggnings system. 

Mata in loggar via Azure Monitor för att samla in säkerhets data som genererats av slut punkts enheter, nätverks resurser och andra säkerhets system. I Azure Monitor använder du Log Analytics arbets ytor för att fråga och utföra analyser och använda Azure Storage-konton för långsiktig lagring. Du kan också aktivera och publicera data till Azure Sentinel eller en SIEM från tredje part.

Många organisationer väljer att använda Azure Sentinel för "heta" data som används ofta och Azure Storage för "kall" data som används mindre ofta.

- [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center övervakning** : för närvarande inte tillgängligt

**Ansvars område** : delat

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security benchmark: incident svar](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: förberedelse – uppdatera incident svars process för Azure

**Vägledning** : se till att din organisation har processer som svarar på säkerhets incidenter, har uppdaterat de här processerna för Azure och utnyttjar dem regelbundet för att säkerställa beredskap. Se till att tjänst erbjudandet ingår i incident svars processen, beroende på vad som är tillämpligt.

- [Implementera säkerhet i företags miljön](https://aka.ms/AzSec4) 
- [Referens guide för incident svar](https://aka.ms/IRRG)

**Azure Security Center övervakning** : för närvarande inte tillgängligt

**Ansvar** : kund

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: förberedelse – installations incident meddelande

**Vägledning** : Konfigurera säkerhets incident kontakt information i Azure Security Center. Den här kontakt informationen används av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har öppnats av en olagligt eller obehörig part. Du har också alternativ för att anpassa incident aviseringar och meddelanden i olika Azure-tjänster baserat på dina incident svars behov.

Kontakt information om säkerhets incidenter används av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har öppnats av en olagligt eller obehörig part. Granska incidenter när du har för att säkerställa att problem har lösts fullständigt.

- [Så här ställer du in Azure Security Center säkerhets kontakt](../security-center/security-center-provide-security-contact-details.md) 

- [Så här ställer du in Azure Security Center säkerhets kontakt](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center övervakning** : Ja

**Ansvar** : kund

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: identifiering och analys – skapa incidenter baserat på aviseringar med hög kvalitet

**Vägledning** : Azure Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller analysera den som används för att utfärda aviseringen samt den konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Dessutom kan du tydligt markera prenumerationer (till exempel produktion, icke-produktion) och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser.

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md) 

- [Använda taggar för att organisera dina Azure-resurser](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center övervakning** : Ja

**Ansvar** : kund

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: identifiering och analys – Undersök en incident

**Vägledning** : Bygg ut en incident svars guide för din organisation. Genomför övningar för att testa dina Systems incident svars funktioner på en vanlig takt. Identifiera svaga punkter och luckor och ändra planen efter behov.

Se till att det finns skriftliga svars planer för incidenter som definierar alla personal roller och faser för incident hantering/hantering från identifiering till granskning efter incidenten.

- [Konfigurera automatisering av arbets flöden i Azure Security Center](../security-center/security-center-planning-and-operations-guide.md) 

- [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process) 

- [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process) 

- [Se NIST: guide för test, utbildning och övnings program för IT-planer och funktioner](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center övervakning** : för närvarande inte tillgängligt

**Ansvar** : kund

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: identifiering och analys – prioritera incidenter

**Vägledning** : Azure Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller det analytiska som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Dessutom kan du tydligt markera prenumerationer (till exempel produktion, icke-produktion) och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser.

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md) 

- [Använda taggar för att organisera dina Azure-resurser](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center övervakning** : Ja

**Ansvars område** : delat

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: inne slutning, utrotning och återställning – automatisera incident hanteringen

**Vägledning** : Använd funktionen för automatisering av arbets flöden i Azure Security Center för att automatiskt utlösa svar via "Azure Logic Apps" i säkerhets aviseringar och rekommendationer.

- [Konfigurera automatisering av arbets flöden och Logic Apps](../security-center/workflow-automation.md)

**Azure Security Center övervakning** : Ja

**Ansvar** : kund

## <a name="posture-and-vulnerability-management"></a>Position och sårbarhets hantering

*Mer information finns i [Azure Security benchmark: position and sårbarhet Management](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: utför regelbunden attack simulering

**Vägledning** : enligt dina krav kan du utföra inträngande tester eller röda team aktiviteter på dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister.

Följ test reglerna för Microsoft Cloud inträngande för att se till att dina inträngande tester inte strider mot Microsofts principer. Använd Microsofts strategi, körning av röda team indelning och inträngande av direktsända webbplatser för att testa Microsoft-hanterad moln infrastruktur, tjänster och program.

- [Inträngande testning i Azure](../security/fundamentals/pen-testing.md)

- [Inträngande test regler för engagemang](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud röd team indelning](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center övervakning** : inte tillämpligt

**Ansvar** : kund

## <a name="endpoint-security"></a>Slut punkts säkerhet

*Mer information finns i [säkerhets principen för Azure-säkerhet: slut punkts säkerhet](/azure/security/benchmarks/security-controls-v2-endpoint-security).*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: Använd slut punkts identifiering och-svar (EDR)

**Vägledning** : kunder har inte uttryckligen tillåtelse att konfigurera inställningar för slut punkts identifiering och-svar. Virtual Machines som används i Azure Virtual WAN-erbjudandet använder dock dessa funktioner. Läs mer om de här allmänna funktionerna på de länkar som refereras till. 

- [Översikt över Microsoft Defender Avancerat skydd](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Microsoft Defender ATP-tjänsten för Windows-servrar](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Microsoft Defender ATP-tjänsten för icke-Windows-servrar](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Azure Security Center övervakning** : Ja

**Ansvars område** : delat

## <a name="governance-and-strategy"></a>Styrning och strategi

*Mer information finns i [Azure Security benchmark: styrning och strategi](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: definiera strategi för till gångs hantering och data skydd 

**Vägledning** : se till att dokumentera och förmedla en tydlig strategi för kontinuerlig övervakning och skydd av system och data. Prioritera identifiering, utvärdering, skydd och övervakning av affärs kritiska data och system. 

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

- Data klassificerings standard i enlighet med affärs riskerna
- Insyn i säkerhets organisationen för risker och till gångs inventering 
- Godkännande av säkerhets organisation för Azure-tjänster för användning 
- Säkerhet för till gångar via deras livs cykel
- Nödvändig åtkomst kontroll strategi i enlighet med organisationens data klassificering
- Användning av data skydds funktioner i Azure Native och tredje part
- Krav på data kryptering för överförings-och rest-användnings fall
- Lämpliga kryptografiska standarder

Granska ytterligare information som är tillgänglig på de länkar som refereras.

- [Rekommendation för Azure Security Architecture – lagring, data och kryptering](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Grundläggande Azure-säkerhet – Azure Data Security, kryptering och lagring](../security/fundamentals/encryption-overview.md)

- [Ramverk för moln införande – metod tips för Azure Data Security och kryptering](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

**Azure Security Center övervakning** : för närvarande inte tillgängligt

**Ansvar** : kund

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: definiera strategi för företags segmentering 

**Vägledning** : upprätta en företagsomfattande strategi för att segmentera åtkomst till till gångar med en kombination av identitet, nätverk, program, prenumeration, hanterings grupp och andra kontroller. Du bör noggrant balansera behovet av säkerhets separation med behovet av att möjliggöra daglig drift av de system som behöver kommunicera med varandra och komma åt data.

Se till att segmenterings strategin implementeras konsekvent över kontroll typer, inklusive nätverks säkerhet, identitets-och åtkomst modeller samt program behörighet eller åtkomst modeller, samt mänskliga process kontroller.

- [Vägledning om segment strategi i Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Vägledning om segment strategi i Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Justera nätverks segmentering med strategi för företags segmentering](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center övervakning** : för närvarande inte tillgängligt

**Ansvar** : kund

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: definiera säkerhets position hanterings strategi

**Vägledning** : ständigt mäta och minimera risker för enskilda till gångar och till miljön där de finns. Prioritera höga värde till gångar och högexponerade attack ytor, till exempel publicerade program, ingångs-och utgångs punkter, användar-och administratörs slut punkter och så vidare.

- [Azure Security benchmark – position och sårbarhets hantering](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Azure Security Center övervakning** : för närvarande inte tillgängligt

**Ansvar** : kund

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: justera organisations roller, ansvars områden och Accountabilities

**Vägledning** : se till att dokumentera och förmedla en tydlig strategi för roller och ansvar i din säkerhets organisation. Prioritera dina ansträngningar baserat på tydliga ansvar för säkerhets beslut, utbilda alla på den delade ansvars modellen och utbilda tekniska team om teknik för att skydda molnet.

- [Bästa praxis för Azure-säkerhet 1 – personer: utbilda team om molnets säkerhets resa](https://aka.ms/AzSec1)

- [Bästa praxis för Azure-säkerhet 2 – personer: utbilda team om moln säkerhets teknik](https://aka.ms/AzSec2)

- [Bästa praxis för Azure Security 3 – process: tilldela ansvar för moln säkerhets beslut](https://aka.ms/AzSec3)

**Azure Security Center övervakning** : för närvarande inte tillgängligt

**Ansvar** : kund

### <a name="gs-5-define-network-security-strategy"></a>GS-5: definiera nätverks säkerhets strategi

**Vägledning** : upprätta en Azure Network Security-metod som en del av din organisations övergripande strategi för säkerhets åtkomst kontroll. Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

- Centraliserad nätverks hantering och säkerhets ansvar
- Modell för virtuella nätverks segment justerade med strategin för företags segmentering
- Reparations strategi i olika hot och angrepps scenarier
- Strategi för Internet Edge och inkommande och utgående
- Hybrid moln och lokal strategi för samanslutning
- Aktuella nätverks säkerhets artefakter (t. ex. nätverks diagram, referens nätverks arkitektur)

Granska ytterligare information som är tillgänglig på de länkar som refereras.

- [Azure-säkerhet Best Practice 11-Architecture. Enhetlig säkerhets strategi](https://aka.ms/AzSec11)

- [Säkerhet för Azure-säkerhet](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Översikt över Azure Network Security](../security/fundamentals/network-overview.md)

- [Arkitektur strategi för företags nätverk](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Security Center övervakning** : för närvarande inte tillgängligt

**Ansvar** : kund

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: definiera identitets-och privilegie rad åtkomst strategi

**Vägledning** : upprätta metoder för Azure Identity och Privileged Access som en del av organisationens övergripande strategi för säkerhets åtkomst kontroll. Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

- Ett centraliserat identitets-och autentiserings system och dess samanslutning med andra interna och externa identitets system
- Starka autentiseringsmetoder i olika användnings fall och villkor
- Skydd av privilegierade användare
- Avvikande användar aktiviteter, övervakning och hantering  
- Process för användar identitets-och åtkomst granskning och avstämning

Granska ytterligare information som är tillgänglig på de länkar som refereras.

- [Azure-säkerhet för prestandatest – identitets hantering](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure Security benchmark – privilegie rad åtkomst](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Azure-säkerhet Best Practice 11-Architecture. Enhetlig säkerhets strategi](https://aka.ms/AzSec11)

- [Översikt över Azure Identity Management-säkerhet](../security/fundamentals/identity-management-overview.md)

**Azure Security Center övervakning** : för närvarande inte tillgängligt

**Ansvar** : kund

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: definiera strategi för loggning och hot svar

**Vägledning** : upprätta en strategi för loggning och hot svar för att snabbt upptäcka och åtgärda hot samtidigt som de uppfyller kraven för efterlevnad. Prioritera att ge analytiker med hög kvalitets varningar och sömlösa upplevelser så att de kan fokusera på hot i stället för integrering och manuella steg. 

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

- Organisationens roll och ansvars områden för säkerhets åtgärder (SecOps)
- En väldefinierad incident svars process som justeras med NIST eller något annat bransch ramverk
- Insamling och kvarhållning av loggar för att stödja hot identifiering, incident svar och krav på efterlevnad
- Centraliserad synlighet och korrelations information om hot, användning av SIEM, interna Azure-funktioner och andra informations-och aviserings planer hos kunder, leverantörer och offentliga intressen
- Användning av inbyggda Azure-och tredjeparts-plattformar för incident hantering, till exempel loggning och hot identifiering, data utredning och angrepp och utrotning
- Processer för hantering av incidenter och aktiviteter efter incidenter, till exempel lektioner som lärts och underlags kvarhållning

Granska ytterligare information som är tillgänglig på de länkar som refereras.
- [Azure-säkerhet för benchmark-loggning och hot identifiering](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure-säkerhet benchmark – incident svar](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure Security Best Practice 4 – process. Uppdatera incident svars processer för molnet](https://aka.ms/AzSec4)

- [Besluts guide för Azure införande Framework, loggning och rapportering](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

**Azure Security Center övervakning** : för närvarande inte tillgängligt

**Ansvar** : kund

## <a name="next-steps"></a>Nästa steg

- Se [Översikt över Azure Security benchmark v2](/azure/security/benchmarks/overview)
- Läs mer om [Azures säkerhets bas linjer](/azure/security/benchmarks/security-baselines-overview)
