---
title: Azures säkerhets bas linje för Azure HPC-cache
description: Azure HPC cache Security-bas linjen ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 3e040146673ba16c07856f30b88771d0063713e9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95896891"
---
# <a name="azure-security-baseline-for-azure-hpc-cache"></a>Azures säkerhets bas linje för Azure HPC-cache

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark-version 2,0](../security/benchmarks/overview.md) till Microsoft Azure HPC-cache. Azures säkerhets benchmark ger rekommendationer om hur du kan skydda dina moln lösningar på Azure. Innehållet grupperas efter **säkerhets kontrollerna** som definieras av Azures säkerhets benchmark och relaterade rikt linjer för Azure HPC cache. **Kontroller** som inte gäller Azure HPC cache har uteslutits.

Om du vill se hur Azure HPC cache är fullständigt mappat till Azures säkerhet kan du läsa mer i den [fullständiga Azure-mappnings filen för HPC-säkerhet](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets principen för Azure-säkerhet: nätverks säkerhet](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementera säkerhet för intern trafik

**Vägledning**: när du distribuerar Azure HPC cache-resurser måste du skapa eller använda ett befintligt virtuellt nätverk. 

Se till att alla virtuella Azure-nätverk följer en företags segment princip som passar affärs riskerna. Alla system som kan ådra sig högre risk för organisationen bör isoleras i det egna virtuella nätverket och tillräckligt säkra med antingen en nätverks säkerhets grupp (NSG) och/eller Azure-brandvägg.

Två nätverksrelaterade krav bör ställas in innan du kan använda din cache: 

- Ett dedikerat undernät för Azure HPC cache-instansen

- DNS-stöd så att cachen kan komma åt lagring och andra resurser

Azure HPC-cachen behöver ett dedikerat undernät med följande kvaliteter: 

- Under nätet måste ha minst 64 IP-adresser tillgängliga.

- Under nätet kan inte vara värd för andra virtuella datorer, även för relaterade tjänster som klient datorer.

- Om du använder flera Azure HPC cache-instanser behöver var och en av sina egna undernät.

Det bästa sättet är att skapa ett nytt undernät för varje cache. Du kan skapa ett nytt virtuellt nätverk och undernät som en del av att skapa cachen.

Om du vill använda HPC cache med en lokal NAS-lagring måste du se till att vissa portar i det lokala nätverket tillåter obegränsad trafik från Azure HPC-cachens undernät. 

- [Konfigurera portar för NFS-lagrings åtkomst](hpc-cache-prerequisites.md#nfs-storage-requirements)

- [Så här skapar du en nätverks säkerhets grupp med säkerhets regler](../virtual-network/tutorial-filter-network-traffic.md)

 

- [Distribuera och konfigurera Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="ns-2-connect-private-networks-together"></a>NS-2: Anslut privata nätverk till varandra

**Vägledning**: Använd Azure ExpressRoute eller Azure Virtual Private Network (VPN) för att skapa privata anslutningar mellan Azure-datacenter och lokal infrastruktur i en samplacerings miljö. ExpressRoute-anslutningar går inte via det offentliga Internet och de ger högre tillförlitlighet, snabbare hastigheter och lägre fördröjning än vanliga Internet anslutningar. För punkt-till-plats-VPN och plats-till-plats-VPN kan du ansluta lokala enheter eller nätverk till ett virtuellt nätverk med valfri kombination av de här VPN-alternativen och Azure-ExpressRoute. 

Om du vill ansluta två eller flera virtuella nätverk i Azure tillsammans använder du peering för virtuella nätverk. Nätverks trafik mellan peer-kopplat virtuella nätverk är privat och lagras i Azures stamnät nätverk.

- [Vad är ExpressRoute-anslutnings modeller](../expressroute/expressroute-connectivity-models.md) 

- [Översikt över Azure VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 

- [Peering för virtuella nätverk](../virtual-network/virtual-network-peering-overview.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: upprätta privat nätverks åtkomst till Azure-tjänster

**Vägledning**: använd Azure Virtual Network-tjänstens slut punkter för att ge säker åtkomst till HPC-cache. Tjänst slut punkter är en optimerad väg över Azure stamnät nätverket utan att passera Internet. 

HPC cache stöder inte användning av en privat Azure-länk för att skydda dess hanterings slut punkter till ett privat nätverk. 

Privat åtkomst är ytterligare ett djupgående mått, förutom autentiserings-och trafik säkerhet som erbjuds av Azure-tjänster.

- [Förstå Virtual Network tjänstens slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md) 

- [Förstå hur du monterar Azure HPC cache](hpc-cache-mount.md)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: skydda program och tjänster från externa nätverks attacker

**Vägledning**: skydda dina HPC cache-resurser mot attacker från externa nätverk, inklusive DDoS-attacker (distributed denial of Service), programspecifika attacker och oönskad och potentiellt skadlig Internet trafik. 

Azure innehåller inbyggda funktioner för det här skyddet: 

- Använd Azure-brandväggen för att skydda program och tjänster mot potentiellt skadlig trafik från Internet och andra externa platser. 
- Skydda dina till gångar mot DDoS-attacker genom att aktivera DDoS standard skydd på dina virtuella Azure-nätverk. 
- Använd Azure Security Center för att identifiera problem med felaktig konfiguration som är relaterade till dina nätverks resurser.

Azure HPC cache är inte avsett att köra webb program och kräver inte att du konfigurerar några ytterligare inställningar eller distribuerar några extra nätverks tjänster för att skydda dem från externa nätverks attacker som riktar sig mot webb program.

- [Dokumentation om Azure Firewall](/azure/firewall/) 

- [Hantera Azure DDoS Protection standard med hjälp av Azure Portal](/azure/virtual-network/manage-ddos-protection) 

- [Rekommendationer för Azure Security Center](../security-center/recommendations-reference.md#recs-network)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: Distribuera intrångs identifiering/intrångs skydd system (ID/IP-adresser)

**Vägledning**: Använd Azure-brandvägg med hot information-baserad filtrering för att varna vid och/eller blockera trafik till och från kända skadliga IP-adresser och domäner. IP-adresserna och domänerna hämtas från Microsoft Threat Intelligence-flödet. 

När nytto lasten är nödvändig kan du distribuera en tredjeparts intrångs identifiering/intrång förhindra system (ID/IP-adresser) från Azure Marketplace med funktioner för nytto Last kontroll. Alternativt kan du välja att använda värdbaserade ID: n/IP-adresser eller en värdbaserad lösning för slut punkts identifiering och-svar (EDR) tillsammans med eller i stället för nätverksbaserade ID: n/IP-adresser.

Obs! Om du har en regel eller annat krav för ID/IP-adresser kan du se till att det alltid är anpassat för att tillhandahålla aviseringar med hög kvalitet till din SIEM-lösning.

- [Så här distribuerar du Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md) 

- [Azure Marketplace ID-funktioner från tredje part](https://azuremarketplace.microsoft.com/marketplace?search=IDS) 

- [Microsoft Defender ATP-EDR-kapacitet](/windows/security/threat-protection/microsoft-defender-atp/overviewendpoint-detection-response)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: förenkla nätverks säkerhets regler

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för erbjudanden som kan distribueras till virtuella Azure-nätverk eller ha möjlighet att definiera grupperingar av tillåtna IP-intervall för effektiv hantering. HPC cache stöder för närvarande inte service märken. 

Det bästa sättet är att skapa ett nytt undernät för varje cache. Du kan skapa ett nytt virtuellt nätverk och undernät som en del av att skapa cachen.

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: säker Domain Name Service (DNS)

**Vägledning**: Följ de bästa metoderna för DNS-säkerhet för att undvika vanliga attacker som Dangling DNS, DNS-utlösnings attacker, DNS-förgiftning och förfalskning och andra.

Azure HPC-cachen behöver DNS för att få åtkomst till resurser utanför cacheminnets privata virtuella nätverk. Om arbets flödet innehåller resurser utanför Azure måste du konfigurera och skydda din egen DNS-server, förutom att använda Azure DNS.

- Använd Azure DNS för att få åtkomst till Azure Blob Storage-slutpunkter, Azure-baserade klient datorer eller andra Azure-resurser.
- Om du vill komma åt lokal lagring eller ansluta till cacheminnet från klienter utanför Azure måste du skapa en anpassad DNS-server som kan matcha dessa värdnamn.
- Om arbets flödet innehåller både interna och externa resurser konfigurerar du den anpassade DNS-servern så att den vidarebefordrar Azure-vissa matchnings begär anden till Azure DNS-servern. 

När Azure DNS används som auktoritativ DNS-tjänst kontrollerar du att DNS-zoner och-poster skyddas från oavsiktlig eller skadlig ändring genom att använda Azure RBAC-och resurs lås.

Om du konfigurerar en egen DNS-server måste du följa dessa rikt linjer för säkerhet:

- [Distributions guide för säker Domain Name System (DNS)](https://csrc.nist.gov/publications/detail/sp/800-81/2/final) 

- [Förhindra Dangling DNS-poster och Undvik under domän övertag Ande](../security/fundamentals/subdomain-takeover.md) 

- [Läs mer om krav för DNS-åtkomst för HPC-cache](hpc-cache-prerequisites.md#dns-access)

- [Översikt över Azure DNS](../dns/dns-overview.md)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

## <a name="identity-management"></a>Identitetshantering

*Mer information finns i [Azure Security benchmark: Identity Management](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: standardisera Azure Active Directory som system för central identitets-och autentisering

**Vägledning**: Azure HPC cache är inte integrerat med Azure Active Directory för interna åtgärder. Azure AD kan dock användas för att autentisera användare i Azure Portal eller CLI för att skapa, Visa och hantera HPC-cache-distributioner och relaterade komponenter.

Azure Active Directory (Azure AD) är standard tjänsten för identitets-och åtkomst hantering i Azure. Du bör standardisera Azure AD för att styra organisationens identitets-och åtkomst hantering i:

- Microsoft Cloud resurser, till exempel Azure Portal, Azure Storage, virtuell Azure-dator (Linux och Windows), Azure Key Vault, PaaS och SaaS program.

- Organisationens resurser, till exempel program i Azure eller företagets nätverks resurser.

Att skydda Azure AD bör vara en hög prioritet i din organisations säkerhets praxis för molnet. Azure AD ger en identitets säker Poäng som hjälper dig att utvärdera position för identiteter i relation till Microsofts rekommendationer för bästa praxis. Använd poängen för att mäta hur nära konfigurationen matchar rekommendationerna för bästa praxis och för att göra förbättringar i din säkerhets position.

Obs! Azure AD stöder extern identitet som gör det möjligt för användare utan Microsoft-konto att logga in på sina program och resurser med sin externa identitet.

- [Innehav i Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Så här skapar och konfigurerar du en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Använda externa identitets leverantörer för ett program](/azure/active-directory/b2b/identity-providers) 

- [Vad är säkerhets poängen för identiteter i Azure Active Directory](../active-directory/fundamentals/identity-secure-score.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: hantera program identiteter säkert och automatiskt

**Vägledning**: HPC cache använder Azure-hanterade identiteter för icke-mänskliga konton som tjänster eller automatisering. Vi rekommenderar att du använder Azures funktion för hanterad identitet i stället för att skapa ett mer kraftfullt humant konto för att komma åt eller köra dina resurser. 

HPC cache kan autentiseras internt i Azure-tjänster/-resurser som stöder Azure AD-autentisering via fördefinierade regler för åtkomst beviljande. Detta gör att du inte behöver använda hårdkodade autentiseringsuppgifter i källkod eller konfigurationsfiler.

- [Azure-hanterade identiteter](../active-directory/managed-identities-azure-resources/overview.md) 

- [Tjänster som stöder hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Använd enkel inloggning för Azure AD (SSO) för program åtkomst

**Vägledning**: Azure HPC-cachen integreras inte med Azure AD för interna åtgärder. Azure AD kan dock användas för att autentisera användare i Azure Portal eller CLI för att skapa, Visa och hantera HPC-cache-distributioner och relaterade komponenter.

Azure Active Directory ger identitets-och åtkomst hantering för Azure-resurser, moln program och lokala program. Detta inkluderar företags identiteter som anställda, samt externa identiteter som partner, leverantörer och leverantörer. Detta möjliggör enkel inloggning (SSO) för att hantera och skydda åtkomsten till din organisations data och resurser lokalt och i molnet. Anslut alla användare, program och enheter till Azure AD för sömlös, säker åtkomst och bättre synlighet och kontroll.

- [Förstå Application SSO med Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Använd starka verifierings kontroller för alla Azure Active Directory-baserade åtkomst

**Vägledning**: även om Azure HPC cache inte integreras med Azure AD för interna åtgärder kan Azure AD användas för att autentisera användare i Azure Portal eller CLI för att skapa, Visa och hantera HPC-cache-distributioner och relaterade komponenter.  

Azure AD stöder starka verifierings kontroller med Multi-Factor Authentication (MFA) och starka metoder för lösen ords kryptering.

- Multi-Factor Authentication: Aktivera Azure AD MFA och följ Azure Security Center rekommendationer för identitets-och åtkomst hantering för några rekommenderade metoder i MFA-installationen. MFA kan tillämpas på alla användare, välja användare eller på nivån per användare baserat på inloggnings villkor och riskfaktorer.
- Lösen ords kryptering – tre alternativ för lösen ords kryptering är tillgängliga: Windows Hello för företag, Microsoft Authenticator app och lokala autentiseringsmetoder som smartkort.

För administratörer och privilegierade användare måste du se till att använda den högsta nivån av metoden stark autentisering. Distribuera lämplig princip för stark autentisering till andra användare.

Azure HPC cache stöder även äldre lösenordsbaserad autentisering för klient system som ansluter till cacheminnet. Dessa typer av anslutningar är enbart moln konton (användar konton som skapats direkt i Azure) som har en bas linje för lösen ords principer eller hybrid konton (användar konton som kommer från lokala Active Directory) som följer de lokala lösen ords principerna. 

När du använder lösenordsbaserad autentisering ger Azure AD en funktion för lösen ords skydd som hindrar användare från att ange lösen ord som är lätta att gissa. Microsoft tillhandahåller en global lista över förbjudna lösen ord som uppdateras utifrån telemetri, och kunderna kan förbättra listan utifrån deras behov (till exempel med varumärkes-, kultur referenser och så vidare). Detta lösen ords skydd kan endast användas för moln-och hybrid konton.

Obs! autentisering baserat på enbart autentiseringsuppgifter för lösen ord är känsligt för populära angrepps metoder. För högre säkerhet använder du stark autentisering, till exempel MFA och en princip för starka lösen ord. Om du använder program från tredje part och Marketplace-tjänster som har standard lösen ord, anger du ett nytt, säkert lösen ord första gången du konfigurerar tjänsten. 

- [Så här aktiverar du MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Introduktion till alternativ för lösen ords lös autentisering för Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md)

- [Standard lösen ords princip för Azure AD](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Eliminera Felaktiga lösen ord med Azure AD Password Protection](../active-directory/authentication/concept-password-ban-bad.md)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: övervaka och Varna vid konto avvikelser

**Vägledning**: Azure HPC-cache kan använda Azure Active Directory för användar hantering från Azure Portal.  Azure Active Directory tillhandahåller följande data Källor:

- Inloggningar – rapporten inloggningar innehåller information om användningen av hanterade program och användar inloggnings aktiviteter.

- Granskningsloggar – Ger spårbarhet via loggar för alla ändringar som gjorts via olika funktioner i Azure AD. Exempel på gransknings loggar är ändringar som har gjorts i alla resurser i Azure AD, till exempel att lägga till eller ta bort användare, appar, grupper, roller och principer.

- Riskfyllda inloggningar – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare.

- Användare som har flaggats för risk – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats.

Dessa data källor kan integreras med Azure Monitor, Azure Sentinel eller SIEM system från tredje part.

Azure Security Center kan också Avisera om vissa misstänkta aktiviteter, till exempel ett ovanligt antal misslyckade autentiseringsförsök, och på inaktuella konton i prenumerationen.

Azure Advanced Threat Protection (ATP) är en säkerhetslösning som kan använda Active Directory signaler för att identifiera, identifiera och undersöka avancerade hot, komprometterade identiteter och skadliga Insider-åtgärder.

- [Granska aktivitets rapporter i Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Visa Azure AD-riskfyllda inloggningar](/azure/active-directory/reports-monitoring/concept-risky-sign-ins) 

- [Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet](/azure/active-directory/reports-monitoring/concept-user-at-risk) 

- [Övervaka användarnas identitets-och åtkomst aktiviteter i Azure Security Center](../security-center/security-center-identity-access.md)

 

- [Aviseringar i Azure Security Center hot Intelligence Protection-modulen](../security-center/alerts-reference.md) 

- [Så här integrerar du Azures aktivitets loggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center övervakning**: Ja

**Ansvars område**: delat

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: ej tillämpligt; HPC cache tillåter inte att kunder distribuerar sparade data i den miljö som körs.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

## <a name="privileged-access"></a>Privilegie rad åtkomst

*Mer information finns i [Azure Security benchmark: privilegie rad åtkomst](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: begränsa administrativ åtkomst till verksamhets kritiska system

**Vägledning**: HPC cache använder Azure RBAC för att isolera åtkomst till affärs kritiska system genom att begränsa vilka konton som har privilegie rad åtkomst till de prenumerationer och hanterings grupper som de finns i.

Skapa standard operativa procedurer kring användningen av dedikerade administrativa konton. För att skapa ett cacheminne kräver HPC-cache att användarna har tillräcklig behörighet i prenumerationen för att skapa nätverkskort. Om du använder Blob Storage krävs Azure-rollerna lagrings konto deltagare och lagrings data deltagare för HPC-cache för att få åtkomst till lagringen. 

Se till att du också begränsar åtkomsten till hanterings-, identitets-och säkerhets system som har administrativ åtkomst till dina affärs kritiska till gångar, till exempel Active Directory-domän controllers (DCs), säkerhets verktyg och system hanterings verktyg med agenter som är installerade på affärs kritiska system. Angripare som angriper dessa hanterings-och säkerhets system kan omedelbart weaponize dem för att kompromettera affärs kritiska till gångar.

Alla typer av åtkomst kontroller bör justeras mot din strategi för företags segmentering för att säkerställa en konsekvent åtkomst kontroll.

- [RBAC-behörigheter för Azure HPC-cache](hpc-cache-prerequisites.md#permissions)

- [Azure-komponenter och referens modell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Åtkomst till hanterings grupp](../governance/management-groups/overview.md#management-group-access)

- [Administratörer för Azure-prenumeration](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: granska och Stäm av användar åtkomst regelbundet

**Vägledning**: granska användar konton och åtkomst tilldelningen regelbundet för att säkerställa att kontona och deras åtkomst nivåer är giltiga. 

Azure HPC cache kan använda Azure Active Directory-konton (Azure AD) för att hantera användar åtkomst via Azure Portal och relaterade gränssnitt. Azure AD ger åtkomst granskningar som hjälper dig att granska grupp medlemskap, åtkomst till företags program och roll tilldelningar. Azure AD repor ting kan tillhandahålla loggar för att identifiera inaktuella konton. Du kan också använda Azure AD Privileged Identity Management för att skapa åtkomst gransknings rapportens arbets flöde för att under lätta gransknings processen.

Dessutom kan Azure Privileged Identity Management konfigureras för avisering när ett högt antal administratörs konton skapas och för att identifiera administratörs konton som är inaktuella eller felaktigt konfigurerade.

Obs! vissa Azure-tjänster har stöd för lokala användare och roller som inte hanteras via Azure AD. Du måste hantera dessa användare separat.

När du använder NFS-lagrings mål måste du arbeta med dina nätverks administratörer och brand Väggs hanterare för att verifiera åtkomst inställningar och se till att Azure HPC-cachen kan kommunicera med NFS-lagrings systemen.

- [En lista över HPC cache-behörigheter finns i krav för Azure HPC-cache](hpc-cache-prerequisites.md) 

- [Skapa en åtkomst granskning av Azure Resource roles i Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Använda Azure AD-identitets-och åtkomst granskningar](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Konfigurera nöd åtkomst i Azure AD

**Vägledning**: HPC cache kan använda Azure Active Directory för att hantera resurs åtkomst via Azure Portal. För att förhindra oavsiktligt låst av din Azure AD-organisation konfigurerar du ett konto för nöd åtkomst för åtkomst när normala administrativa konton inte kan användas. Konton för nöd åtkomst är ofta mycket privilegierade och de bör inte tilldelas vissa individer. Konton för nöd åtkomst är begränsade till nöd situationer eller "Break glas"-scenarier där normala administrativa konton inte kan användas.

Du bör se till att autentiseringsuppgifterna (till exempel lösen ord, certifikat eller smartkort) för konton för nöd åtkomst hålls skyddade och endast kända för personer som har behörighet att använda dem endast i nödfall.

- [Hantera konton för nöd åtkomst i Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatisera rättighets hantering 

**Vägledning**: HPC cache kan använda Azure Active Directory för att hantera åtkomst till cache-resurser via Azure Portal. 

Använd funktioner för hantering av Azure AD-rättighet för att automatisera åtkomst till begär ande arbets flöden, inklusive åtkomst tilldelningar, recensioner och förfallo datum. Det finns också stöd för godkännande med dubbla eller flera steg. 

- [Vad är åtkomst granskningar för Azure AD](../active-directory/governance/access-reviews-overview.md) 

- [Vad är hantering av Azure AD-berättigande](../active-directory/governance/entitlement-management-overview.md)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Följ bara tillräckligt med administration (princip för minsta behörighet) 

**Vägledning**: HPC cache är integrerat med rollbaserad åtkomst kontroll (RBAC) i Azure för att hantera dess resurser. Med Azure RBAC kan du hantera åtkomst till Azure-resurser via roll tilldelningar. Du kan tilldela dessa roller till användare, grupper tjänstens huvud namn och hanterade identiteter. Det finns fördefinierade inbyggda roller för vissa resurser och dessa roller kan inventeras eller frågas via verktyg som Azure CLI, Azure PowerShell eller Azure Portal. 

De behörigheter som du tilldelar resurser via Azure RBAC bör alltid vara begränsade till vad som krävs av rollerna. Detta kompletterar just-in-Time-metoden (JIT) för Azure AD Privileged Identity Management (PIM) och bör granskas regelbundet.

Använd inbyggda roller för att allokera behörighet och endast skapa en anpassad roll vid behov.

- [Vad är Azure rollbaserad åtkomst kontroll (Azure RBAC)](../role-based-access-control/overview.md) 

- [Konfigurera RBAC i Azure](../role-based-access-control/role-assignments-portal.md) 

- [Använda Azure AD-identitets-och åtkomst granskningar](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security benchmark: Data Protection](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-1-discover-classify-and-label-sensitive-data"></a>DP-1: identifiera, klassificera och märk känsliga data 

**Vägledning**: HPC cache hanterar känsliga data men har inte möjlighet att upptäcka, klassificera och märka känsliga data.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: delat

### <a name="dp-2-protect-sensitive-data"></a>DP-2: skydda känsliga data

**Vägledning**: skydda känsliga data genom att begränsa åtkomsten med hjälp av Azure Role-baserad Access Control (Azure RBAC), nätverksbaserade åtkomst kontroller och vissa kontroller i Azure-tjänster (till exempel kryptering i SQL och andra databaser).

För att säkerställa konsekvent åtkomst kontroll bör alla typer av åtkomst kontroll justeras mot din strategi för företags segmentering. Strategin för företags segmentering bör också informeras om platsen för känsliga eller affärs kritiska data och system.

För den underliggande plattformen, som hanteras av Microsoft, behandlar Microsoft allt kund innehåll som känsligt och skyddar mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat vissa standard kontroller och funktioner för data skydd.

- [Rollbaserad Access Control i Azure (RBAC)](../role-based-access-control/overview.md) 

- [Förstå kundens data skydd i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: delat

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: övervaka för obehörig överföring av känsliga data

**Vägledning**: HPC-cachen överför känsliga data men har inte stöd för övervakning av obehörig överföring av känsliga data.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: delat

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: kryptera känslig information under överföring

**Vägledning**: HPC cache stöder data kryptering i överföring med TLS v 1.2 eller senare.

Även om det är valfritt för trafik i privata nätverk, är detta avgörande för trafik på externa och offentliga nätverk. För HTTP-trafik ser du till att alla klienter som ansluter till dina Azure-resurser kan förhandla TLS v 1.2 eller senare. För fjärrhantering använder du SSH (för Linux) eller RDP/TLS (för Windows) i stället för ett okrypterat protokoll. Inaktuella SSL-, TLS-och SSH-versioner och-protokoll och svaga chiffer bör inaktive ras.

Som standard tillhandahåller Azure kryptering för data i överföring mellan Azures Data Center.

- [Förstå kryptering i överföring med Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

- [Information om TLS-säkerhet](/security/engineering/solving-tls1-problem) 

- [Double Encryption för Azure-data under överföring](../security/fundamentals/double-encryption.md#data-in-transit)

**Azure Security Center övervakning**: Ja

**Ansvars område**: delat

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Kryptera känsliga data i vila

**Vägledning**: för att komplettera åtkomst kontroller bör data i vila skyddas mot "out of band"-attacker (till exempel åtkomst till underliggande lagring) med hjälp av kryptering. Detta säkerställer att angripare inte enkelt kan läsa eller ändra data.

Azure tillhandahåller data i rest-kryptering som standard. För mycket känsliga data har du alternativ för att implementera ytterligare kryptering i vila på alla Azure-resurser där det är tillgängligt. Azure hanterar dina krypterings nycklar som standard, men Azure tillhandahåller alternativ för att hantera dina egna nycklar (Kundhanterade nycklar) för vissa Azure-tjänster.

Alla data som lagras i Azure, inklusive på cache-diskarna, krypteras i vila med hjälp av Microsoft-hanterade nycklar som standard. Du behöver bara anpassa inställningarna för Azure HPC-cachen om du vill hantera nycklarna som används för att kryptera dina data.

Om det krävs för att göra beräknings resurser implementerar du ett verktyg från tredje part, till exempel en automatiserad värdbaserade lösning för data förlust skydd, för att genomdriva åtkomst kontroller till data även när data kopieras av ett system.

- [Använda Kundhanterade krypterings nycklar med Azure HPC cache](https://docs.microsoft.com/azure/hpc-cache/hpc-cache-create?tabs=azure-portal#enable-azure-key-vault-encryption-optional)

- [Förstå kryptering i vila i Azure](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Så här konfigurerar du Kundhanterade krypterings nycklar](/azure/storage/common/storage-encryption-keys-portal) 

- [Krypterings modell och nyckel hanterings tabell](../security/fundamentals/encryption-atrest.md)

 

- [Data i vila dubbel kryptering i Azure](../security/fundamentals/double-encryption.md#data-at-rest)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: delat

## <a name="asset-management"></a>Tillgångshantering

*Mer information finns i [Azure Security benchmark: Asset Management](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: se till att säkerhets teamet har insyn i risker för till gångar

**Vägledning**: se till att säkerhets team beviljas behörigheter för säkerhets läsare i din Azure-klient och prenumerationer så att de kan övervaka säkerhets risker med hjälp av Azure Security Center. 

Beroende på hur säkerhets teamets ansvars områden är strukturerade kan övervakning av säkerhets risker vara ansvaret för en central säkerhets grupp eller ett lokalt team. Detta innebär att säkerhets insikter och risker alltid måste aggregeras centralt inom en organisation. 

Behörigheter för säkerhets läsare kan tillämpas i stort sett till en hel klient organisation (rot hanterings grupp) eller omfattas av hanterings grupper eller vissa prenumerationer. 

Obs: ytterligare behörigheter kan krävas för att få insyn i arbets belastningar och tjänster. 

- [Översikt över rollen säkerhets läsare](../role-based-access-control/built-in-roles.md#security-reader)

- [Översikt över Azure Hanteringsgrupper](../governance/management-groups/overview.md)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: se till att säkerhets teamet har åtkomst till till gångs inventering och metadata

**Vägledning**: Azure HPC-cache stöder användning av taggar. Använd taggar för dina Azure-resurser, resurs grupper och prenumerationer för att logiskt organisera dem i en taxonomi. Varje tagg består av ett namn och ett värde-par. 

Du kan till exempel använda namnet ”Miljö” och värdet ”Produktion” för alla resurser i produktionsmiljön. Du kan lägga till taggar när du skapar en cache och när cachen har distribuerats. 

Använd inventering av virtuella Azure-datorer för att automatisera insamling av information om program vara på Virtual Machines. Program varu namn, version, utgivare och uppdaterings tid är tillgängliga från Azure Portal. Du kan få åtkomst till installations datum och annan information genom att aktivera diagnostik på gästnivå och ta Windows-händelseloggen till en Log Analytics-arbetsyta.
HPC cache tillåter inte körning av program eller installation av program vara på sina resurser. 

- [Så här skapar du frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) 

- [Azure Security Center till gångs lager hantering](../security-center/asset-inventory.md) 

- [Beslutsguide för namngivning och taggning av resurser](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json) 

- [Så här aktiverar du inventering av virtuella Azure-datorer](../automation/automation-tutorial-installed-software.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Använd endast godkända Azure-tjänster

**Vägledning**: HPC Cache stöder Azure Resource Manager distributioner. Använd Azure Policy för att granska och begränsa vilka tjänster som användare kan etablera i din miljö. Använd Azure Resource Graph för att fråga efter och identifiera resurser i prenumerationerna. Du kan också använda Azure Monitor för att skapa regler för att utlösa aviseringar när en icke-godkänd tjänst upptäcks.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Så här nekar du en speciell resurs typ med Azure Policy](/azure/governance/policy/samples/not-allowed-resource-types) 

- [Så här skapar du frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: se till att till gångens livs cykel hantering är säker

**Vägledning**: inte tillämpligt. Det går inte att använda Azure HPC cache för att garantera säkerheten för till gångar i en livs cykel hanterings process. Det är kundens ansvar att underhålla attribut och nätverkskonfigurationer för till gångar som betraktas som hög påverkan. 

Vi rekommenderar att kunden skapar en process för att avbilda attribut och ändringar i nätverks konfigurationen, mäter ändrings effekten och skapa reparations uppgifter som tillämpliga.

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

## <a name="logging-and-threat-detection"></a>Loggning och hot identifiering

*Mer information finns i [säkerhets benchmark för Azure: loggning och hot identifiering](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Aktivera hot identifiering för Azure-resurser

**Vägledning**: Använd Azure Security Center inbyggd hot identifierings funktion och aktivera Azure Defender (formellt Azure Avancerat skydd) för dina HPC cache-resurser. Azure Defender för HPC-cache ger ytterligare ett lager med säkerhets information som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja dina cachelagrade resurser.

Vidarebefordra alla loggar från HPC cache till din SIEM som kan användas för att konfigurera anpassade hot identifieringar. Se till att du övervakar olika typer av Azure-tillgångar för potentiella hot och avvikelser. Fokusera på att få aviseringar med hög kvalitet för att minska antalet falska positiva identifieringar för analytiker som ska sorteras. Aviseringar kan hämtas från loggdata, agenter eller andra data.

- [Skydd mot hot i Azure Security Center](/azure/security-center/threat-protection) 

- [Referens guide för Azure Security Center säkerhets varningar](../security-center/alerts-reference.md) 

- [Skapa anpassade analys regler för att identifiera hot](../sentinel/tutorial-detect-threats-custom.md) 

- [Cyberhot Hot information med Azure Sentinel](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Aktivera hot identifiering för Azure Identity and Access Management

**Vägledning**: Azure AD tillhandahåller följande användar loggar som kan visas i Azure AD repor ting eller integreras med Azure Monitor, Azure Sentinel eller andra Siem/övervaknings verktyg för mer avancerade övervaknings-och analys användnings fall:
- Inloggningar – inloggnings rapporten innehåller information om användningen av hanterade program och användar inloggnings aktiviteter.

- Gransknings loggar – ger spårning via loggar för alla ändringar som gjorts av olika funktioner i Azure AD. Exempel på gransknings loggar är ändringar som har gjorts i alla resurser i Azure AD, till exempel att lägga till eller ta bort användare, appar, grupper, roller och principer.

- Riskfyllda inloggningar – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare.

- Användare som har flaggats för risk – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats.

Azure Security Center kan också Avisera om vissa misstänkta aktiviteter, till exempel ett högt antal misslyckade autentiseringsförsök eller inaktuella konton i prenumerationen. Utöver den grundläggande övervakningen av säkerhetsövervakning kan Azure Security Centerens skydds modul även samla in mer detaljerade säkerhets aviseringar från enskilda Azure Compute-resurser (virtuella datorer, behållare, App Service), data resurser (SQL DB och lagring) och Azure Service-lager. Med den här funktionen kan du få insyn i konto avvikelser i de enskilda resurserna.

- [Granska aktivitets rapporter i Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Aktivera Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) 

- [Skydd mot hot i Azure Security Center](/azure/security-center/threat-protection)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Aktivera loggning för Azure nätverks aktiviteter

**Vägledning**: du kan använda VPN-gatewayer och deras paket insamlings funktioner utöver de vanligaste verktygen för paket insamling för att registrera nätverks paket som reser mellan dina virtuella nätverk.

Distribuera en nätverks säkerhets grupp i nätverket där dina Azure HPC cache-resurser distribueras. Aktivera flödes loggar för nätverks säkerhets grupper i dina nätverks säkerhets grupper för trafik granskning.

Flödes Loggarna behålls i ett lagrings konto. Aktivera Trafikanalys-lösningen för att bearbeta och skicka flödes loggarna till en Log Analytics arbets yta. Trafikanalys ger ytterligare insikter i trafikflödet för dina Azure-nätverk. Trafikanalys kan hjälpa dig att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks fel konfiguration.

Cachen behöver DNS för att få åtkomst till resurser utanför det virtuella nätverket. Beroende på vilka resurser du använder kan du behöva konfigurera en anpassad DNS-server och konfigurera vidarebefordran mellan servern och Azure DNS servrar. 

Implementera en lösning från tredje part från Azure Marketplace för DNS-loggning av lösningar enligt organisationens behov.

- [Konfigurera paket avbildningar för VPN-gatewayer](../vpn-gateway/packet-capture.md) 

- [Så här aktiverar du flödes loggar för nätverks säkerhets grupper](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Så här aktiverar och använder du Trafikanalys](../network-watcher/traffic-analytics.md) 

- [Förstå nätverks säkerhet som tillhandahålls av Azure Security Center](../security-center/security-center-network-recommendations.md) 

- [Läs mer om DNS-förutsättningarna](hpc-cache-prerequisites.md)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Aktivera loggning för Azure-resurser

**Vägledning**: Azure HPC cache-resurser skapar automatiskt aktivitets loggar. Dessa loggar innehåller alla Skriv åtgärder (placera, skicka, ta bort) men omfattar inte Läs åtgärder (GET). Aktivitets loggar kan användas för att hitta ett fel vid fel sökning eller för att övervaka hur en användare i organisationen har ändrat en resurs.

Du kan också använda Azure Security Center och Azure Policy för att aktivera Azures resurs loggar för HPC-cache och för att logga data insamling. Dessa loggar kan vara kritiska för senare utredning av säkerhets incidenter och utföra kriminal tekniska övningar.

- [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Förstå loggning och olika logg typer i Azure](../azure-monitor/platform/platform-logs-overview.md) 

- [Förstå Azure Security Center insamling av data](../security-center/security-center-enable-data-collection.md)

**Azure Security Center övervakning**: Ja

**Ansvars område**: delat

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: centralisera hantering av säkerhets loggar och analys

**Vägledning**: centralisera loggning av lagring och analys för att aktivera korrelation. För varje logg källa ser du till att du har tilldelat en data ägare, åtkomst vägledning, lagrings plats, vilka verktyg som används för att bearbeta och komma åt data samt krav för data lagring.

Se till att du integrerar Azures aktivitets loggar i den centrala loggningen. Mata in loggar via Azure Monitor för att samla in säkerhets data som genererats av slut punkts enheter, nätverks resurser och andra säkerhets system. I Azure Monitor använder du Log Analytics arbets ytor för att fråga och utföra analyser och använda Azure Storage konton för långsiktig lagring.

Du kan också aktivera och publicera data till Azure Sentinel eller en SIEM från tredje part.

Många organisationer väljer att använda Azure Sentinel för "heta" data som används ofta och Azure Storage för "kall" data som används mindre ofta.

- [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security benchmark: incident svar](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: förberedelse – uppdatera incident svars process för Azure

**Vägledning**: se till att din organisation har processer som svarar på säkerhets incidenter, har uppdaterat de här processerna för Azure och utnyttjar dem regelbundet för att säkerställa beredskap.

- [Implementera säkerhet i företags miljön](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Referens guide för incident svar](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="ir-2-preparation--set-up-incident-notification"></a>IR-2: förberedelse – konfigurera incident meddelande 

**Vägledning**: Konfigurera säkerhets incident kontakt information i Azure Security Center. Den här kontakt informationen används av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har öppnats av en olagligt eller obehörig part. Du har också alternativ för att anpassa incident aviseringar och meddelanden i olika Azure-tjänster baserat på dina incident svars behov. 

- [Så här ställer du in Azure Security Center säkerhets kontakt](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center övervakning**: Ja

**Ansvar**: kund

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: identifiering och analys – skapa incidenter baserat på aviseringar med hög kvalitet

**Vägledning**: se till att du har en process för att skapa aviseringar med hög kvalitet och mäta kvaliteten på aviseringar. På så sätt kan du lära dig lektioner från tidigare incidenter och prioritera aviseringar för analytiker, så att de inte slösar tid på falska positiva identifieringar. 

Aviseringar med hög kvalitet kan byggas utifrån erfarenhet från tidigare incidenter, validerade community-källor och verktyg som utformats för att skapa och rensa aviseringar från fusing och korrelera olika signal källor. 

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

Azure Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig vid sökning eller analys som används för att utfärda aviseringen, samt den konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

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

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: upprätta säkra konfigurationer för beräknings resurser

**Vägledning**: Använd Azure Security Center och Azure policy för att upprätta säkra konfigurationer för alla beräknings resurser, inklusive virtuella datorer, behållare och andra.

- [Så här övervakar du Azure Security Center rekommendationer](../security-center/security-center-recommendations.md) 

- [Säkerhetsrekommendationer – en referensguide](../security-center/recommendations-reference.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: utför regelbunden attack simulering

**Vägledning**: vid behov kan du genomföra inträngande tester eller röda team aktiviteter på dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister.
Följ test reglerna för Microsoft Cloud inträngande för att se till att dina inträngande tester inte strider mot Microsofts principer. Använd Microsofts strategi och körning av röda team indelning och inträngande av direktsända webbplatser mot Microsoft-hanterad moln infrastruktur, tjänster och program.

- [Inträngande testning i Azure](../security/fundamentals/pen-testing.md)

- [Inträngande test regler för engagemang](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud röd team indelning](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: delat

## <a name="backup-and-recovery"></a>Säkerhetskopiering och återställning

*Mer information finns i [säkerhets benchmark för Azure: säkerhets kopiering och återställning](/azure/security/benchmarks/security-controls-v2-backup-recovery).*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: Säkerställ vanliga automatiserade säkerhets kopieringar

**Vägledning**: eftersom Azure HPC cache är en lösning för cachelagring och inte ett lagrings system, fokuserar vi på att säkerställa att data i lagrings målen regelbundet säkerhets kopie ras. Följ standard procedurerna för Azure Blob-behållare och för att säkerhetskopiera alla lokala lagrings mål. 

För att minimera störningar i händelse av ett regionalt avbrott kan du vidta åtgärder för att säkerställa data åtkomst över flera regioner.  

Varje Azure HPC cache-instans körs inom en viss prenumeration och i en region. Det innebär att ditt cache-arbetsflöde kanske inte kan störas om regionen har ett fullständigt avbrott. För att minimera detta avbrott bör organisationen använda backend-lagring som är tillgänglig från flera regioner. Lagringen kan antingen vara ett lokalt NAS-system med lämpligt DNS-stöd eller Azure Blob Storage som finns i en annan region än cachen.

När arbets flödet fortsätter i din primära region, sparas data i den långsiktiga lagringen utanför regionen. Om cache-regionen blir otillgänglig kan du skapa en dubblett av Azure HPC-instansen i en sekundär region, ansluta till samma lagrings utrymme och återuppta arbetet från det nya cacheminnet.

- [Läs mer om regional redundans](hpc-region-recovery.md)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="br-2-encrypt-backup-data"></a>BR-2: kryptera säkerhets kopierings data

**Vägledning**: se till att säkerhets kopiorna skyddas mot attacker. Detta bör innefatta kryptering av säkerhets kopiorna för att skydda mot förlust av konfidentialitet.

För lokal säkerhets kopiering med Azure Backup tillhandahålls kryptering vid vila med hjälp av den lösen fras som du anger. För vanlig säkerhets kopiering av Azure-tjänsten krypteras säkerhetskopierade data automatiskt med hjälp av Azure Platform-hanterade nycklar. Du kan välja att kryptera säkerhets kopian med Kundhanterade nycklar. I det här fallet kontrollerar du att den här Kundhanterade nyckeln i nyckel valvet också finns i säkerhets kopierings omfånget.

Azure HPC cache skyddas också av värd kryptering för virtuella datorer på de hanterade diskar som innehåller cachelagrade data, även om du lägger till en kund nyckel för cache-diskarna. Genom att lägga till en kundhanterad nyckel för Double Encryption får du en extra säkerhets nivå för kunder med höga säkerhets krav. Mer information finns i kryptering på Server sidan av Azure disk Storage.

Använd rollbaserad åtkomst kontroll i Azure Backup, Azure Key Vault eller andra resurser för att skydda säkerhets kopior och Kundhanterade nycklar. Dessutom kan du aktivera avancerade säkerhetsfunktioner så att du kan kräva MFA innan du kan ändra eller ta bort säkerhets kopior.

- [Kryptering av VM-värd](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)

- [Kryptering på Server sidan för Azure disk Storage](../virtual-machines/disk-encryption.md)

- [Översikt över säkerhetsfunktioner i Azure Backup](../backup/security-overview.md) 

- [Kryptering av säkerhets kopierings data med Kundhanterade nycklar](../backup/encryption-at-rest-with-cmk.md)  

- [Säkerhetskopiera Key Vault nycklar i Azure](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-5.1.0&amp;preserve-view=true)

**Azure Security Center övervakning**: för närvarande inte tillgängligt

**Ansvar**: kund

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: du måste regelbundet se till att du kan återställa säkerhetskopierade nycklar som hanteras av kunden.

- [Så här återställer du Key Vault nycklar i Azure](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-5.1.0&amp;preserve-view=true)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: minska risken för förlorade nycklar

**Vägledning**: se till att du har mått på plats för att förhindra och återställa från förlust av nycklar. Aktivera mjuk borttagning och tömning av skydd i Azure Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning.

- [Så här aktiverar du mjuk borttagning och tömning av skydd i Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: kund

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

**Vägledning**: upprätta en strategi för loggning och hot svar för att snabbt upptäcka och åtgärda hot samtidigt som de uppfyller kraven för efterlevnad. Prioritera att ge analytiker med högkvalitativa varningar och sömlösa upplevelser så att de kan fokusera på hot i stället för integrering och manuella steg. 

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
