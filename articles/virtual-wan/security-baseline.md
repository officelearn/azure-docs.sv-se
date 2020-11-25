---
title: Azures säkerhets bas linje för virtuellt WAN
description: Den virtuella WAN-säkerhetslösningen ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 7f75f764f378118f9a34c1eee467e78ac279e19c
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96029055"
---
# <a name="azure-security-baseline-for-virtual-wan"></a>Azures säkerhets bas linje för virtuellt WAN

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark-version 2,0](../security/benchmarks/overview.md) för att Microsoft Azure virtuella WAN. Azures säkerhets benchmark ger rekommendationer om hur du kan skydda dina moln lösningar på Azure. Innehållet grupperas efter de **säkerhets kontroller** som definierats av Azures säkerhets benchmark och relaterade rikt linjer som gäller för virtuella WAN-nätverk. **Kontroller** som inte gäller för virtuellt WAN har uteslutits.

Om du vill se hur det virtuella WAN-nätverket mappas fullständigt till Azures säkerhets benchmark, se den [fullständiga virtuella WAN-filen för bas linje mappning](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets principen för Azure-säkerhet: nätverks säkerhet](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementera säkerhet för intern trafik

**Vägledning**: Microsoft Azure virtuella WAN-nätverk tillhandahåller anpassade routningsfunktioner och ger kryptering för din ExpressRoute-trafik. All väg hantering tillhandahålls av den virtuella Hub-routern, som också möjliggör överförings anslutning mellan virtuella nätverk. Kryptering av din ExpressRoute-trafik med Virtual WAN tillhandahåller en krypterad överföring mellan de lokala nätverken och virtuella Azure-nätverk över ExpressRoute, utan att gå över det offentliga Internet eller använda offentliga IP-adresser. 

- [Kryptering av ExpressRoute-trafik](virtual-wan-about.md#encryption)

- [Använd privat länk i virtuellt WAN](howto-private-link.md)

- [Scenarier för anpassad routning](scenario-any-to-any.md)

- [Dokumentation om anpassad routningstabell](how-to-virtual-hub-routing.md)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="ns-2-connect-private-networks-together"></a>NS-2: Anslut privata nätverk till varandra 

**Vägledning**: Microsoft Azure ExpressRoute erbjuder privat anslutning till Azure Virtual WAN. Eftersom ExpressRoute-anslutningarna inte går via det offentliga Internet, ger ExpressRoute mer tillförlitlighet, snabbare hastighet och lägre fördröjning än vanliga Internet anslutningar. Du kan också använda ett virtuellt privat nätverk för att ansluta till Azure via antingen plats-till-plats (S2S) VPN eller punkt-till-plats (P2S) VPN.

- [ExpressRoute i virtuellt WAN](virtual-wan-expressroute-portal.md)

- [Översikt över webbplats-till-plats-VPN](virtual-wan-site-to-site-portal.md)

- [Översikt över peka till plats-VPN](virtual-wan-point-to-site-portal.md)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvars område**: delat

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: skydda program och tjänster från externa nätverks attacker

**Vägledning**: Virtual WAN exponerar inte några slut punkter för externa nätverk som kräver att de skyddas med konventionella nätverks skydd. Du är kostnads fri att skydda resurser i ekrar virtuella nätverk (alla virtuella nätverk som är anslutna till en virtuell hubb) med hjälp av virtuella nätverks skydds tjänster. 

Använd Azure-brandväggen för att skydda program och tjänster mot potentiellt skadlig trafik från Internet och andra externa platser. 

Välj Azure-tillhandahållen DDoS Protection för att skydda dina till gångar mot attacker på dina virtuella Azure-nätverk. Använd Azure Security Center för att identifiera risker för felaktig konfiguration som är relaterade till dina nätverks resurser.

- [Dokumentation om Azure Firewall](/azure/firewall)

- [Hantera Azure DDoS Protection standard med hjälp av Azure Portal](/azure/virtual-network/manage-ddos-protection) 

- [Rekommendationer för Azure Security Center](../security-center/recommendations-reference.md#recs-network)

**Azure Security Center övervakning**: Ja

**Ansvars område**: delat

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: Distribuera intrångs identifiering/intrångs skydd system (ID/IP-adresser)

**Vägledning**: Virtual WAN är en Microsoft-hanterad tjänst. Den erbjuder inte inbyggd intrångs identifiering eller funktioner för intrångs skydd. Det finns dock säkerhetsfunktioner som tillhandahålls för virtuella WAN-nätverk via Azure-brandväggen för att möjliggöra en enhetlig punkt för princip kontroll. Du kan skapa en Azure Firewall-princip och länka principen till en virtuell WAN-hubb så att den befintliga virtuella WAN-hubben fungerar som en säker virtuell hubb, med de nödvändiga Azure Firewall-resurserna distribuerade.

- [Konfigurera Azure-brandväggen i en virtuell WAN-hubb](howto-firewall.md)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: förenkla nätverks säkerhets regler

**Vägledning**: förenkla nätverks säkerhets regler genom att använda Virtual Network Service-taggar för att definiera nätverks åtkomst kontroller i nätverks säkerhets grupper eller Azure-brandvägg. Service märken kan användas i stället för vissa IP-adresser när du skapar säkerhets regler. Genom att ange namnet på service tag i fältet käll-eller mål för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

- [Förstå och använda service märken](../virtual-network/service-tags-overview.md)

- [Förstå och använda program säkerhets grupper](/azure/virtual-network/security-overview#application-security-groups)

- [Dokumentation om Azure Firewall](/azure/firewall/)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: säker Domain Name Service (DNS)

**Vägledning**: säkra DNS-funktioner tillhandahålls för virtuella WAN-nätverk med Azure-brandväggen. Konfigurera Azure-brandväggen så att den fungerar som en DNS-proxy som blir mellanhand för DNS-begäranden från virtuella klient datorer till en DNS-server. För anpassade DNS-serverkonfigurationer aktiverar du DNS-proxy för att undvika matchning av DNS-matchning och möjliggör fullständigt kvalificerat domän namns filtrering i nätverks reglerna. 

- [Dokumentation om Azure Firewall](/azure/firewall/)

- [DNS-inställningar för Azure-brandväggen](/azure/firewall/dns-settings)

- [Använd Azure-brandväggen som en DNS-vidarebefordrare med privat länk](https://github.com/adstuart/azure-privatelink-dns-azurefirewall)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

## <a name="identity-management"></a>Identitetshantering

*Mer information finns i [Azure Security benchmark: Identity Management](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: standardisera Azure Active Directory som system för central identitets-och autentisering

**Vägledning**: Azure Active Directory (Azure AD) är standard tjänsten för identitets-och åtkomst hantering för Azure-tjänster. inklusive virtuellt WAN. Standardisera Azure AD för att styra organisationens identitets-och åtkomst hantering i:

- Microsoft Cloud resurser, till exempel Azure Portal, Azure Storage, Azure Virtual Machines (Linux och Windows), Azure Key Vault, plattform som en tjänst (PaaS) och program vara som en tjänst (SaaS).
- Organisationens resurser, till exempel program i Azure eller företagets nätverks resurser

Skydda Azure AD som en hög prioritet i din organisations säkerhets praxis för molnet. Utvärdera dina identitets-och säkerhets position med säkerhets poängen från Security Center för att mäta hur nära konfigurationen matchar Microsofts rekommendationer för bästa praxis. Vid behov implementerar du Microsofts rekommendationer för bästa praxis för förbättringar av din säkerhets position.

Azure AD stöder också externa identiteter, vilket gör det möjligt för användare utan Microsoft-konto att logga in på sina program och resurser med sin externa identitet. 

Läs informationen om hur du använder Azure AD i punkt-till-plats-VPN-scenarier på de refererade länkarna.

- [Skapa en Azure Active Directory-klient (AD) för P2S OpenVPN-protokoll anslutningar](openvpn-azure-ad-tenant-multi-app.md)

- [Konfigurera Azure Active Directory autentisering för användar-VPN](virtual-wan-point-to-site-azure-ad.md)

- [Innehav i Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Så här skapar och konfigurerar du en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center övervakning**: Ja

**Ansvars område**: delat

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Använd starka verifierings kontroller för alla Azure Active Directory-baserade åtkomst

**Vägledning**: för närvarande tillhandahålls Azure Active Directory (Azure AD)-autentisering via integrering med virtuella WAN-platser med virtuella WAN-nätverk.

Azure Active Directory (Azure AD) är standard tjänsten för identitets-och åtkomst hantering för Azure-tjänster. Azure AD stöder starka verifierings kontroller med multifaktorautentisering och starka metoder för lösen ords kryptering.

Azure AD rekommenderar följande för starka verifierings kontroller:

- Multifaktorautentisering – aktivera Azure AD multifaktor-autentisering och följ rekommendationer för identitets-och åtkomst hantering i Azure Security Center för rekommenderade säkerhets metoder. Tillämpa multifaktorautentisering på alla, Välj användare eller per användar nivå baserat på inloggnings villkor och risk faktorer

- Autentisering med lösen ord – tre alternativ för lösen ords avföljer är tillgängliga. Dessa inkluderar, Windows Hello för företag, Microsoft Authenticator app och lokala autentiseringsmetoder som smartkort

Se till att den högsta nivån av metoden stark autentisering används för administratörer och privilegierade användare, följt av en samlad princip för en stark autentiseringsprincip för andra användare.

- [Så här aktiverar du MFA i P2S VPN för virtuellt WAN](openvpn-azure-ad-mfa.md)

**Azure Security Center övervakning**: Ja

**Ansvars område**: delat

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: begränsa åtkomsten till Azure-resursen baserat på villkor

**Vägledning**: Aktivera Azure Active Directory (Azure AD) multifaktorautentisering för VPN-användare (punkt-till-plats) med hjälp av Azure AD-autentisering. Konfigurera multifaktorautentisering på basis av varje användare eller utnyttja multifaktorautentisering med villkorlig åtkomst. Villkorlig åtkomst ger en detaljerad kontroll över hur en andra faktor ska höjas. Den kan tillåta tilldelning av multifaktorautentisering till endast VPN och utesluta andra program som är kopplade till Azure AD-klienten. 

Observera att Azure AD-autentisering endast är tillgängligt för gateways med OpenVPN-protokoll och klienter som kör Windows.

- [Vad är villkorsstyrd åtkomst](../active-directory/conditional-access/overview.md)

- [Konfigurera Azure Active Directory autentisering för användar-VPN](virtual-wan-point-to-site-azure-ad.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: plats-till-plats-VPN i Virtual WAN använder i förväg delade nycklar (PSK) som identifieras, skapas och hanteras av kunden i sina Azure Key Vault. Implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Den här skannern uppmuntrar också att flytta identifierade autentiseringsuppgifter till säkrare platser som Azure Key Vault.

För GitHub kan du använda intern hemlighet-genomsökning för att identifiera autentiseringsuppgifter eller annan form av hemligheter i koden.

- [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html) 

- [GitHub Secret-genomsökning](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

## <a name="privileged-access"></a>Privilegie rad åtkomst

*Mer information finns i [Azure Security benchmark: privilegie rad åtkomst](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: begränsa administrativ åtkomst till verksamhets kritiska system

**Vägledning**: Azure Virtual WAN använder Azures rollbaserade åtkomst kontroller (Azure RBAC) för att isolera åtkomst till affärs kritiska system genom att begränsa vilka konton som beviljas privilegie rad åtkomst till de prenumerationer och hanterings grupper som de finns i.

Begränsa också åtkomsten till hanterings-, identitets-och säkerhets system som har administrativ åtkomst till din affärs kritiska åtkomst, till exempel Active Directory-domän kontrollanter, säkerhets verktyg och system hanterings verktyg med agenter som är installerade på affärs kritiska system. Angripare som angriper dessa hanterings-och säkerhets system kan omedelbart weaponize dem för att kompromettera affärs kritiska till gångar.

Alla typer av åtkomst kontroller bör justeras mot din strategi för företags segmentering för att säkerställa en konsekvent åtkomst kontroll.

- [Azure-komponenter och referens modell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Åtkomst till hanterings grupp](../governance/management-groups/overview.md#management-group-access) 

- [Administratörer för Azure-prenumeration](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security benchmark: Data Protection](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: kryptera känslig information under överföring

**Vägledning**: använda punkt-till-plats-VPN, plats-till-plats-VPN och krypterad Express väg med virtuellt WAN för dina anslutnings krav. VPN-kryptering skyddar data under överföring från "out of band"-attacker (till exempel trafik avbildning) för att säkerställa att angripare inte kan läsa eller ändra data. 

- [Punkt-till-plats-VPN](virtual-wan-point-to-site-portal.md)

- [Plats-till-plats-VPN](virtual-wan-site-to-site-portal.md)

- [Krypterad ExpressRoute](vpn-over-expressroute.md)

**Azure Security Center övervakning**: Ja

**Ansvars område**: delat

## <a name="asset-management"></a>Tillgångshantering

*Mer information finns i [Azure Security benchmark: Asset Management](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: se till att säkerhets teamet har insyn i risker för till gångar

**Vägledning**: se till att säkerhets team beviljas behörigheter för säkerhets läsare i din Azure-klient och prenumerationer så att de kan övervaka säkerhets risker med hjälp av Azure Security Center. 

Beroende på hur säkerhets teamets ansvars områden är strukturerade kan övervakning av säkerhets risker vara ansvaret för en central säkerhets grupp eller ett lokalt team. Med detta måste säkerhets insikter och risker alltid aggregeras centralt inom en organisation. 

Behörigheter för säkerhets läsare kan tillämpas i stort sett till en hel klient organisation (rot hanterings grupp) eller omfattas av hanterings grupper eller vissa prenumerationer. 

Obs: ytterligare behörigheter kan krävas för att få insyn i arbets belastningar och tjänster. 

- [Översikt över rollen säkerhets läsare](../role-based-access-control/built-in-roles.md#security-reader)

- [Översikt över Azure Hanteringsgrupper](../governance/management-groups/overview.md)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: se till att säkerhets teamet har åtkomst till till gångs inventering och metadata

**Vägledning**: Använd taggar för dina Azure-resurser, resurs grupper och prenumerationer för att logiskt organisera dem i en taxonomi. Varje tagg består av ett namn och ett värde-par. Du kan till exempel använda namnet ”Miljö” och värdet ”Produktion” för alla resurser i produktionsmiljön. Azure Virtual WAN stöder även Azure Resource Manager-baserade resurs distributioner med vilka du kan exportera till gångs mallar. 

- [Beslutsguide för namngivning och taggning av resurser](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

- [Azure Security Center till gångs lager hantering](../security-center/asset-inventory.md)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure Monitor för att skapa regler för att utlösa aviseringar när en icke-godkänd tjänst upptäcks. Det virtuella WAN-nätverket ger tillsammans många funktioner för nätverk, säkerhet och routning för att tillhandahålla ett enda drift gränssnitt. Virtuella WAN-gatewayer, ExpressRoute-gatewayer och Azure-brandväggen har loggning och mått som är tillgängliga via Azure Monitor. 
 

- [Virtuella WAN-loggar och-mått](logs-metrics.md)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Använd villkorlig Azure-åtkomst för att begränsa användarnas möjlighet att interagera med Azures resurs hanterare genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

## <a name="logging-and-threat-detection"></a>Loggning och hot identifiering

*Mer information finns i [säkerhets benchmark för Azure: loggning och hot identifiering](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Aktivera hot identifiering för Azure-resurser

**Vägledning**: punkt-till-plats-VPN med virtuellt WAN är integrerat med Azure Active Directory (Azure AD). Azure AD innehåller följande användar loggar som kan visas i Azure AD repor ting eller integreras med Azure Monitor, Azure Sentinel, SIEM eller övervaknings verktyg för mer avancerade hot övervakning och analys användnings fall. Dessa är:

- Logga in – inloggnings rapporten innehåller information om användningen av hanterade program och användar inloggnings aktiviteter.
- Granskningsloggar – Ger spårbarhet via loggar för alla ändringar som gjorts via olika funktioner i Azure AD. Exempel på gransknings loggar är ändringar som har gjorts i alla resurser i Azure AD, till exempel att lägga till eller ta bort användare, appar, grupper, roller och principer.
- Riskfylld inloggning – ett riskfylldt inloggning är en indikator för ett inloggnings försök som kan ha utförts av någon som inte är den legitima ägaren till ett användar konto.
- Användare som har flaggats för risk – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats.

Använd Azure Security Center för att skapa aviseringar för vissa misstänkta aktiviteter, till exempel ett ovanligt antal misslyckade autentiseringsförsök, inklusive föråldrade konton i prenumerationen. Förutom den grundläggande säkerhetsövervakningen kan du använda Threat Protection-modulen från Security Center för att samla in mer djupgående säkerhets aviseringar från enskilda Azure Compute-resurser (virtuella datorer, behållare, App Service), data resurser (SQL DB och lagring) och Azure Service-lager. Med den här funktionen kan du få insyn i konto avvikelser i de enskilda resurserna.

- [Granska aktivitets rapporter i Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Aktivera Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Konfigurera Azure-brandväggen i en virtuell WAN-hubb](howto-firewall.md)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvars område**: delat

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Aktivera hot identifiering för Azure Identity and Access Management

**Vägledning**: punkt-till-plats-VPN med virtuellt WAN är integrerat med Azure Active Directory (Azure AD). Azure AD innehåller följande användar loggar som kan visas i Azure AD repor ting eller integreras med Azure Monitor, Azure Sentinel, SIEM eller övervaknings verktyg för mer avancerade hot övervakning och analys användnings fall. Dessa är:

- Logga in – inloggnings rapporten innehåller information om användningen av hanterade program och användar inloggnings aktiviteter.
- Granskningsloggar – Ger spårbarhet via loggar för alla ändringar som gjorts via olika funktioner i Azure AD. Exempel på gransknings loggar är ändringar som har gjorts i alla resurser i Azure AD, till exempel att lägga till eller ta bort användare, appar, grupper, roller och principer.
- Riskfylld inloggning – ett riskfylldt inloggning är en indikator för ett inloggnings försök som kan ha utförts av någon som inte är den legitima ägaren till ett användar konto.
- Användare som har flaggats för risk – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats.

Använd Azure Security Center för att skapa aviseringar för vissa misstänkta aktiviteter, till exempel ett ovanligt antal misslyckade autentiseringsförsök, inklusive föråldrade konton i prenumerationen. Förutom den grundläggande säkerhetsövervakningen kan du använda Threat Protection-modulen från Security Center för att samla in mer djupgående säkerhets aviseringar från enskilda Azure Compute-resurser (virtuella datorer, behållare, App Service), data resurser (SQL DB och lagring) och Azure Service-lager. Med den här funktionen kan du få insyn i konto avvikelser i de enskilda resurserna.

- [Granska aktivitets rapporter i Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Aktivera Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Konfigurera Azure-brandväggen i en virtuell WAN-hubb](howto-firewall.md)

**Azure Security Center övervakning**: Ja

**Ansvars område**: delat

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Aktivera loggning för Azure nätverks aktiviteter

**Vägledning**: övervaka Azure Virtual WAN med Azure Monitor. Det virtuella WAN-nätverket ger tillsammans många funktioner för nätverk, säkerhet och routning för att tillhandahålla ett enda drift gränssnitt. Virtuella WAN-gatewayer, ExpressRoute-gatewayer och Azure-brandväggen har loggning och mått som är tillgängliga via Azure Monitor. Aktivitets logg poster samlas in som standard och kan visas i Azure Portal. Du kan använda Azure aktivitets loggar (tidigare kallade drift loggar och gransknings loggar) för att visa alla åtgärder som skickats till din Azure-prenumeration.

En rad olika diagnostikloggar är också tillgängliga för virtuella WAN-nätverk och kan konfigureras för den virtuella WAN-resursen med Azure Portal.  Du kan välja att skicka till Log Analytics, strömma till en händelsehubben eller att bara arkivera till ett lagrings konto. 
 

- [Virtuella WAN-loggar och-mått](logs-metrics.md)

- [Azure Firewall-loggar och mått](/azure/firewall/logs-and-metrics)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Aktivera loggning för Azure-resurser

**Vägledning**: Azure aktivitets loggar, aktive ras automatiskt, innehåller alla Skriv åtgärder (placera, skicka, ta bort) för dina virtuella Azure-resurser förutom Läs åtgärder (get). Aktivitets loggar kan användas för att hitta ett fel under fel sökning eller för att övervaka hur en användare i organisationen ändrade en resurs.

Aktivera Azures resurs loggar för virtuellt WAN-nätverk. Du kan använda Azure Security Center och Azure Policy för att aktivera resurs loggar och insamling av logg data. Dessa loggar kan vara kritiska för senare utredning av säkerhets incidenter och utföra kriminal tekniska övningar.

- [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Förstå loggning och olika logg typer i Azure](../azure-monitor/platform/platform-logs-overview.md) 

- [Förstå Azure Security Center insamling av data](../security-center/security-center-enable-data-collection.md)

**Azure Security Center övervakning**: Ja

**Ansvars område**: delat

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: centralisera hantering av säkerhets loggar och analys

**Vägledning**: Aktivera säkerhets loggning för virtuellt WAN-nätverk med Azure Monitor. Det virtuella WAN-nätverket ger tillsammans många funktioner för nätverk, säkerhet och routning för att tillhandahålla ett enda drift gränssnitt. Virtuella WAN-gatewayer, ExpressRoute-gatewayer och Azure-brandväggen har loggning och mått som är tillgängliga via Azure Monitor. Aktivitets logg poster samlas in som standard och kan visas i Azure Portal. Du kan använda Azure aktivitets loggar (tidigare kallade drift loggar och gransknings loggar) för att visa alla åtgärder som skickats till din Azure-prenumeration. 

En rad olika diagnostikloggar är också tillgängliga för virtuella WAN-nätverk och kan konfigureras för den virtuella WAN-resursen med Azure Portal. Skicka till Log Analytics, strömma till en händelsehubben eller att bara arkivera till ett lagrings konto. Du kan också aktivera och publicera data till Azure Sentinel eller en säkerhets informations-och händelse hanterings lösning från tredje part. 
 

- [Virtuella WAN-loggar och-mått](logs-metrics.md)

- [Azure Firewall-loggar och mått](/azure/firewall/logs-and-metrics)

Azure Virtual WAN-säkerhet tillhandahålls via Azure-brandväggen. 

- [Dokumentation om Azure Firewall](/azure/firewall/overview)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvars område**: delat

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurera kvarhållning av logg lagring

**Vägledning**: Konfigurera logg kvarhållning enligt dina krav på efterlevnad, reglering och affärs verksamhet. I Azure Monitor kan du Log Analytics ange arbets ytans lagrings period enligt organisationens regler för efterlevnad. Använd Azure Storage-, Data Lake-eller Log Analytics arbets ytans konton för långsiktig lagring och arkivering.

- [Ändra data lagrings perioden i Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Konfigurera bevarande princip för Azure Storage konto loggar](../storage/common/storage-monitor-storage-account.md#configure-logging)

- [Azure Security Center aviseringar och rekommendationer exportera](../security-center/continuous-export.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: delat

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security benchmark: incident svar](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: förberedelse – uppdatera incident svars process för Azure

**Vägledning**: se till att din organisation har processer som svarar på säkerhets incidenter, har uppdaterat de här processerna för Azure och utnyttjar dem regelbundet för att säkerställa beredskap.

- [Implementera säkerhet i företags miljön](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Referens guide för incident svar](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: förberedelse – installations incident meddelande

**Vägledning**: Konfigurera säkerhets incident kontakt information i Azure Security Center. Den här kontakt informationen används av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har öppnats av en olagligt eller obehörig part. Du har också alternativ för att anpassa incident aviseringar och meddelanden i olika Azure-tjänster baserat på dina incident svars behov. 

- [Så här ställer du in Azure Security Center säkerhets kontakt](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: identifiering och analys – skapa incidenter baserat på aviseringar med hög kvalitet

**Vägledning**: se till att du har en process för att skapa aviseringar med hög kvalitet och mäta kvaliteten på aviseringar. På så sätt kan du lära dig lektioner från tidigare incidenter och prioritera aviseringar för analytiker, så att de inte slösar tid på falska positiva identifieringar. 

Aviseringar med hög kvalitet kan byggas utifrån erfarenhet från tidigare incidenter, validerade community-källor och verktyg som utformats för att generera och rensa aviseringar från fusing och korrelera olika signal källor. 

Azure Security Center ger aviseringar med hög kvalitet över flera Azure-tillgångar. Du kan använda ASC data Connector för att strömma aviseringarna till Azure Sentinel. Med Azure Sentinel kan du skapa avancerade aviserings regler för att generera incidenter automatiskt för en undersökning. 

Exportera dina Azure Security Center-aviseringar och rekommendationer med hjälp av export funktionen för att identifiera risker med Azure-resurser. Exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö.

- [Så här konfigurerar du export](../security-center/continuous-export.md)

- [Strömma aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: identifiering och analys – Undersök en incident

**Vägledning**: se till att analytiker kan fråga och använda olika data källor när de undersöker potentiella incidenter, för att bygga en fullständig vy över vad som hände. Diverse loggar bör samlas in för att spåra aktiviteter för en potentiell angripare över Kill-kedjan för att undvika att fläckar.  Du bör också se till att insikter och kunskaper fångas upp för andra analytiker och för framtida historiska referenser.  

Data källorna för undersökningen innehåller de centraliserade loggnings källor som redan har samlats in från tjänster som omfattas och som kör system, men kan även innehålla:

- Nätverks data – Använd Nätverks säkerhets grupper flödes loggar, Azure Network Watcher och Azure Monitor för att avbilda nätverks flödes loggar och annan analys information. 

- Ögonblicks bilder av system som körs: 

    - Använd ögonblicks bild funktionen i Azure Virtual Machine för att skapa en ögonblicks bild av den aktiva system disken. 

    - Använd operativ systemets interna minnes dumpnings funktion för att skapa en ögonblicks bild av det aktiva systemets minne.

    - Använd ögonblicks bild funktionen i Azure-tjänsterna eller din program varu funktion för att skapa ögonblicks bilder av de system som körs.

Azure Sentinel tillhandahåller omfattande data analyser i praktiskt taget alla logg källor och en ärende hanterings Portal för att hantera den fullständiga livs cykeln för incidenter. Informations information under en undersökning kan associeras med en incident för spårning och rapportering. 

- [Ögonblicks bild av en Windows-dators disk](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Ögonblicks bild av en Linux-dators disk](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure stöd för diagnostikinformation och insamling av minnes dum par](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Undersök incidenter med Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: identifiering och analys – prioritera incidenter

**Vägledning**: Tillhandahåll sammanhang för att analysera vilka incidenter som ska fokusera på den första baserat på aviseringens allvarlighets grad och till gångens känslighet. 

Azure Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller analysera det som används för att utfärda aviseringen, samt den konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Markera dessutom resurser med taggar och skapa ett namngivnings system för att identifiera och kategorisera Azure-resurser, särskilt för bearbetning av känsliga data.  Det är ditt ansvar att prioritera reparationen av aviseringar baserat på allvarlighets graden för de Azure-resurser och den miljö där incidenten inträffade.

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Använda taggar för att organisera dina Azure-resurser](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: inne slutning, utrotning och återställning – automatisera incident hanteringen

**Vägledning**: automatisera manuella återkommande uppgifter för att påskynda svars tiden och minska belastningen på analytiker. Manuella uppgifter tar längre tid att köra, vilket saktar ned varje incident och minskar hur många incidenter en analytiker kan hantera. Manuella uppgifter ökar också analytikerns utmattning, vilket ökar risken för mänskligt fel som orsakar fördröjningar och minskar risken för att analytikerna ska kunna fokusera på komplexa uppgifter på ett effektivt sätt. Använd automatiserings funktioner för arbets flöde i Azure Security Center och Azure Sentinel för att automatiskt utlösa åtgärder eller köra en Spelbok för att svara på inkommande säkerhets aviseringar. Spelbok tar åtgärder, till exempel att skicka meddelanden, inaktivera konton och isolera problematiska nätverk. 

- [Konfigurera automatisering av arbets flöde i Security Center](../security-center/workflow-automation.md)

- [Konfigurera automatiska hot svar i Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Konfigurera automatiska hot svar i Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

## <a name="posture-and-vulnerability-management"></a>Position och sårbarhets hantering

*Mer information finns i [Azure Security benchmark: position and sårbarhet Management](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: utför regelbunden attack simulering

**Vägledning**: vid behov kan du genomföra inträngande tester eller röda team aktiviteter på dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister.
Följ test reglerna för Microsoft Cloud inträngande för att se till att dina inträngande tester inte strider mot Microsofts principer. Använd Microsofts strategi och körning av röda team indelning och inträngande av direktsända webbplatser mot Microsoft-hanterad moln infrastruktur, tjänster och program.

- [Inträngande testning i Azure](../security/fundamentals/pen-testing.md)

- [Inträngande test regler för engagemang](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud röd team indelning](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: delat

## <a name="endpoint-security"></a>Slut punkts säkerhet

*Mer information finns i [säkerhets principen för Azure-säkerhet: slut punkts säkerhet](/azure/security/benchmarks/security-controls-v2-endpoint-security).*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: Använd slut punkts identifiering och-svar (EDR)

**Vägledning**: kunder har inte uttryckligen tillåtelse att konfigurera inställningar för slut punkts identifiering och-svar. Virtual Machines som används i Azure Virtual WAN-erbjudandet använder dock dessa funktioner. Läs mer om de här allmänna funktionerna på de länkar som refereras till. 

- [Översikt över Microsoft Defender Avancerat skydd](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Microsoft Defender ATP-tjänsten för Windows-servrar](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Microsoft Defender ATP-tjänsten för icke-Windows-servrar](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Azure Security Center övervakning**: Ja

**Ansvars område**: delat

## <a name="governance-and-strategy"></a>Styrning och strategi

*Mer information finns i [Azure Security benchmark: styrning och strategi](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: definiera strategi för till gångs hantering och data skydd 

**Vägledning**: se till att dokumentera och förmedla en tydlig strategi för kontinuerlig övervakning och skydd av system och data. Prioritera identifiering, utvärdering, skydd och övervakning av affärs kritiska data och system. 

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

-   Data klassificerings standard i enlighet med affärs riskerna

-   Insyn i säkerhets organisationen för risker och till gångs inventering 

-   Godkännande av säkerhets organisation för Azure-tjänster för användning 

-   Säkerhet för till gångar via deras livs cykel

-   Nödvändig åtkomst kontroll strategi i enlighet med organisationens data klassificering

-   Användning av data skydds funktioner i Azure Native och tredje part

-   Krav på data kryptering för överförings-och rest-användnings fall

-   Lämpliga kryptografiska standarder

Mer information finns i följande referenser:
- [Rekommendation för Azure Security Architecture – lagring, data och kryptering](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Grundläggande Azure-säkerhet – Azure Data Security, kryptering och lagring](../security/fundamentals/encryption-overview.md)

- [Ramverk för moln införande – metod tips för Azure Data Security och kryptering](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Hantering av Azure Security-inventarier](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Azure Security benchmark – data skydd](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: definiera strategi för företags segmentering 

**Vägledning**: upprätta en företagsomfattande strategi för att segmentera åtkomst till till gångar med en kombination av identitet, nätverk, program, prenumeration, hanterings grupp och andra kontroller.

Du bör noggrant balansera behovet av säkerhets separation med behovet av att möjliggöra daglig drift av de system som behöver kommunicera med varandra och komma åt data.

Se till att segmenterings strategin implementeras konsekvent över kontroll typer, inklusive nätverks säkerhet, identitets-och åtkomst modeller samt program behörighet/åtkomst modeller och kontroller för mänsklig process.

- [Vägledning om segment strategi i Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Vägledning om segment strategi i Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Justera nätverks segmentering med strategi för företags segmentering](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: definiera säkerhets position hanterings strategi

**Vägledning**: ständigt mäta och minimera risker för enskilda till gångar och miljön som de finns i. Prioritera höga värde till gångar och högexponerade attack ytor, till exempel publicerade program, ingångs-och utgångs punkter, användar-och administratörs slut punkter osv.

- [Azure Security benchmark – position och sårbarhets hantering](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: justera organisations roller, ansvars områden och Accountabilities

**Vägledning**: se till att dokumentera och förmedla en tydlig strategi för roller och ansvar i din säkerhets organisation. Prioritera att tillhandahålla tydliga ansvar för säkerhets beslut, utbilda alla på den delade ansvars modellen och utbilda tekniska team om teknik för att skydda molnet.

- [Bästa praxis för Azure-säkerhet 1 – personer: utbilda team om molnets säkerhets resa](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Bästa praxis för Azure-säkerhet 2 – personer: utbilda team om moln säkerhets teknik](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Bästa praxis för Azure Security 3 – process: tilldela ansvar för moln säkerhets beslut](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="gs-5-define-network-security-strategy"></a>GS-5: definiera nätverks säkerhets strategi

**Vägledning**: upprätta en Azure Network Security-metod som en del av din organisations övergripande strategi för säkerhets åtkomst kontroll.  

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

-   Centraliserad nätverks hantering och säkerhets ansvar

-   Modell för virtuella nätverks segment justerade med strategin för företags segmentering

-   Reparations strategi i olika hot och angrepps scenarier

-   Strategi för Internet Edge och inkommande och utgående

-   Hybrid moln och lokal strategi för samanslutning

-   Aktuella nätverks säkerhets artefakter (t. ex. nätverks diagram, referens nätverks arkitektur)

Mer information finns i följande referenser:
- [Azure-säkerhet Best Practice 11-Architecture. Enhetlig säkerhets strategi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Säkerhet för Azure-säkerhet](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Översikt över Azure Network Security](../security/fundamentals/network-overview.md)

- [Arkitektur strategi för företags nätverk](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: definiera identitets-och privilegie rad åtkomst strategi

**Vägledning**: upprätta en Azure Identity-och Privileged Access-metod som en del av din organisations övergripande strategi för säkerhets åtkomst kontroll.  

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

-   Ett centraliserat identitets-och autentiserings system och dess samanslutning med andra interna och externa identitets system

-   Starka autentiseringsmetoder i olika användnings fall och villkor

-   Skydd av privilegierade användare

-   Avvikande användar aktiviteter, övervakning och hantering  

-   Process för användar identitets-och åtkomst granskning och avstämning

Mer information finns i följande referenser:

- [Azure-säkerhet för prestandatest – identitets hantering](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure Security benchmark – privilegie rad åtkomst](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Azure-säkerhet Best Practice 11-Architecture. Enhetlig säkerhets strategi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Översikt över Azure Identity Management-säkerhet](../security/fundamentals/identity-management-overview.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: definiera strategi för loggning och hot svar

**Vägledning**: upprätta en strategi för loggning och hot svar för att snabbt upptäcka och åtgärda hot samtidigt som de uppfyller kraven för efterlevnad. Prioritera att ge analytiker med hög kvalitets varningar och sömlösa upplevelser så att de kan fokusera på hot i stället för integrering och manuella steg. 

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

-   Organisationens roll och ansvars områden för säkerhets åtgärder (SecOps) 

-   En väldefinierad incident svars process som justeras med NIST eller något annat bransch ramverk 

-   Insamling och kvarhållning av loggar för att stödja hot identifiering, incident svar och krav på efterlevnad

-   Centraliserad synlighet och korrelations information om hot, med SIEM, interna Azure-funktioner och andra källor 

-   Kommunikation och meddelande plan med dina kunder, leverantörer och offentliga intressen

-   Användning av inbyggda Azure-och tredjeparts-plattformar för incident hantering, till exempel loggning och hot identifiering, data utredning och angrepp och utrotning

-   Processer för hantering av incidenter och aktiviteter efter incidenter, till exempel lektioner som lärts och underlags kvarhållning

Mer information finns i följande referenser:

- [Azure-säkerhet för benchmark-loggning och hot identifiering](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure-säkerhet benchmark – incident svar](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure Security Best Practice 4 – process. Uppdatera incident svars processer för molnet](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Besluts guide för Azure införande Framework, loggning och rapportering](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure Enterprise Scale, Management och Monitoring](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="next-steps"></a>Nästa steg

- Se [Översikt över Azure Security benchmark v2](/azure/security/benchmarks/overview)
- Läs mer om [Azures säkerhets bas linjer](/azure/security/benchmarks/security-baselines-overview)
