---
title: "Planera hänsynstaganden för Azure-stacken integrerat system | Microsoft Docs"
description: "Lär dig vad du kan göra för att planera nu och förbereda för Azure-Stack för flera noder."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 90f8fa1a-cace-4bfa-852b-5abe2b307615
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: twooley
ms.openlocfilehash: 8484f7947f23a00c05b34babf13cd75f9d227740
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2017
---
# <a name="planning-considerations-for-azure-stack-integrated-systems"></a>Planera hänsynstaganden för Azure-stacken integrerat system

*Gäller för: Azure Stack integrerat system*

Om du är intresserad av en Azure-stacken integrerat system ska du förstå några viktiga överväganden kring distribution och hur systemet passar in i ditt datacenter. Det här avsnittet innehåller en översikt över dessa överväganden.

Om du vill köpa ett integrerat system hjälper maskinvaruleverantören OEM-tillverkaren (OEM) hjälper dig att en stor del av planeringsprocessen i detalj. De kan också utföra verklig distribution.

## <a name="management-considerations"></a>Hanteringsanmärkningar

Azure-stacken är ett förseglat system där infrastrukturen som är låst både från en behörigheter och perspektiv. Nätverket åtkomstkontrollistor (ACL) används för att blockera all inkommande trafik som obehörig och all onödig kommunikation mellan infrastrukturkomponenter. Detta gör det svårare för obehöriga användare att komma åt systemet.

För dagliga hantering och åtgärder finns det ingen obegränsad administratörsåtkomst till infrastrukturen. Azure Stack-operatorer måste hantera systemet via administratörsportalen eller via Azure Resource Manager (via PowerShell eller REST API). Det finns ingen åtkomst till datorn av andra hanteringsverktyg, till exempel Hyper-V Manager eller hanteraren för redundanskluster. Program från andra tillverkare (till exempel agenter) kan inte installeras i komponenterna i Azure Stack-infrastruktur för att skydda datorn. Samverkan med externa hanterings- och säkerhetsprogramvara görs via PowerShell eller REST API.

När en högre nivå av åtkomst krävs för att felsöka problem som inte är löst aviseringen medling steg måste du arbeta med stöd för. Det finns en metod för att tillhandahålla tillfällig fullständig administratörsåtkomst till systemet för att utföra mer avancerade åtgärder via Support. 

## <a name="deploy-connected-or-disconnected"></a>Distribuera ansluten eller frånkopplad
 
Du kan välja att distribuera Azure-stacken ansluten till internet (och Azure) eller kopplas från. För att få största möjliga nytta av Azure-stacken, inklusive hybridscenarion mellan Azure-stacken och Azure, vill du distribuera ansluten till Azure. I följande tabell kan sammanfatta de större skillnaderna mellan distributionslägen för.

| Område | Anslutet läge | Frånkopplat läge |
| -------- | ------------- | ----------|
| Identitetsprovider | Azure Active Directory (AD Azure) eller Active Directory Federation Services (AD FS) | Endast AD FS |
| Marketplace-syndikeringsfeed | Hämta objekt direkt från Azure Marketplace till marketplace i Azure-stacken | Kräver manuell hantering av marketplace i Azure-stacken |
| Licensieringsmodell | Betala per-som-du-användning eller kapacitet-baserade | Kapacitet-baserade endast|
| Korrigeringar och uppdateringar  | Ladda ned uppdateringspaket direkt till Azure-stacken | Kräver flyttbart medium och en separat ansluten enhet |
| | | |

Du kan inte ändra distributionsläget senare utan att hela systemet omdistributionen.

## <a name="identity-considerations"></a>Identity-överväganden

### <a name="choose-identity-provider"></a>Välj identitetsleverantören.

Du behöver överväga vilka identitetsleverantör som du vill använda för distribution av Azure-stacken, Azure AD eller AD FS. Du kan inte växla identitetsleverantörer efter distributionen utan Omdistributionen för hela systemet.

Önskat identity-providern påverkar inte på virtuella datorer, identitet och konton som de använder, om de kan ansluta till en Active Directory-domän, osv. Detta är separat.

**Orsaker överväga att använda Azure AD**

- Du har redan befintliga investeringar i Azure AD (till exempel Azure eller Office 365).
- Du vill använda samma identitet mellan Azure och Azure-stacken moln.
- Du vill ge stöd för scenarier med flera innehavare, där du kan vara värd för olika organisationer i samma Azure Stack-instans.
- Du vill använda hantering av REST-baserade katalogen med Azure AD Graph att etablera användare, grupper, etc. via API: er.

> [!NOTE]
> Du kan inte ansluta en Azure-Stack-distribution till både en Azure AD-instans och en befintlig AD FS-instans på samma gång. Om du distribuerar med Azure AD och du vill använda en befintlig AD FS-instans, du kan federera dina lokala instans av AD FS med Azure AD.

**Orsaker överväga att använda AD FS**

- Det finns ingen eller endast delvis Internetanslutning.
- Det finns andra/suveränitet krav.
- Du vill använda din egen identitetssystem (till exempel företagets Active Directory) för operatorn och användarkonton. Om du vill göra detta måste federera du med en befintlig AD FS-instans (på Windows Server 2012, 2012 R2 eller 2016) som backas upp av Active Directory.
- Du har inga befintliga investeringar i Azure och du vill inte använda Azure AD.

> [!NOTE]
> Du kan federera Azure Stack bara med en annan instans av AD FS som backas upp av Active Directory. Andra identitetsleverantörer stöds inte. Om du har andra identitetsleverantörer som du vill använda med Azure-stacken, Överväg att använda Azure AD-baserad distribution i stället.

### <a name="ad-fs-and-graph-integration"></a>Integrering med AD FS och diagram

Om du väljer att distribuera Azure-stacken använder AD FS som identitetsleverantören måste du integrera instansen av AD FS på Azure-stacken med en befintlig instans av AD FS via ett federationsförtroende. Detta gör att identiteter i en befintlig Active Directory-skog för att autentisera med resurser i Azure-stacken.

Du kan också integrera Graph-tjänsten i Azure-stacken med befintliga Active Directory. På så sätt kan du hantera rollbaserad åtkomstkontroll (RBAC) i Azure-stacken. När åtkomst till en resurs har delegerats söker komponenten diagrammet användarkonto i den befintliga Active Directory-skogen med LDAP-protokollet.

Följande diagram visar integrerad trafikflöde för AD FS och diagram.
![Diagram över AD FS och diagram trafikflöde](media/azure-stack-planning-considerations/ADFSIntegration.PNG)

## <a name="licensing-model"></a>Licensieringsmodell

Du måste bestämma vilka licensieringsmodell som du vill använda. Du kan välja lön-som-du-användning eller kapacitet-baserade licensiering för en ansluten distribution. Betala per-som-du-användningen kräver en anslutning till Azure för att rapportera användningen, som sedan debiteras via Azure handel. Endast kapacitet-baserade licensiering stöds om du distribuerar frånkopplad från internet. Mer information om licensiering modeller finns [Microsoft Azure-stacken paketera och prissättning](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).

## <a name="naming-decisions"></a>Namnge beslut

Du behöver tänka på hur du vill planera Azure Stack namnområdet, särskilt regionnamn och extern domännamn. Distributionen av Azure-Stack för offentliga slutpunkter fullständigt kvalificerade domännamn (FQDN) är en kombination av dessa två namn &lt; *region*&gt;&lt;*external_FQDN*  &gt;, till exempel *east.cloud.fabrikam.com*. I det här exemplet skulle Azure Stack-portaler vara tillgängligt vid följande webbadresser:

- https://Portal.East.cloud.Fabrikam.com
- https://adminportal.East.cloud.Fabrikam.com

I följande tabell sammanfattas besluten domän namngivning.

| Namn | Beskrivning | 
| -------- | ------------- | 
|namn på område | Namnet på din första Azure Stack-region. Det här namnet används som en del av det fullständiga Domännamnet för de offentliga virtuella IP-adresser (VIP) som hanterar Azure-stacken. Normalt är regionnamn en identifierare för fysisk plats, till exempel en datacenter-plats. | 
| Externa domännamn | Namnet på zonen Domain Name System (DNS) för slutpunkter med externa virtuella IP-adresser. Används i det fullständiga Domännamnet för dessa offentliga VIP. | 
| Domännamn för privat (internt) | Namnet på domänen (och interna DNS-zon) skapas på Azure-Stack för infrastrukturhantering. 
| | |

## <a name="certificate-requirements"></a>Certifikatkrav

För distribution måste du tillhandahålla Secure Sockets Layer (SSL)-certifikat för offentliga slutpunkter. På en hög nivå har certifikat följande krav:

> [!IMPORTANT]
> Certifikatinformation i den här artikeln har angetts som en allmän vägledning. Innan du skaffar certifikat för Azure-Stack fungera med partnern OEM-maskinvara. De ger mer detaljerad vägledning för certifikat och krav.

- Du kan använda en enda jokerteckencertifikat eller du kan använda en uppsättning dedikerade certifikat och använder jokertecken endast för slutpunkter som lagring och Nyckelvalvet.
- Certifikaten måste utfärdas av en offentlig betrodda certifikatutfärdare (CA) eller en Certifikatutfärdare som hanteras av kunden.
 
I följande tabell visas de tjänster och antalet offentliga slutpunkter som kräver certifikat för den första Azure-stacken distributionen. 

| Används för | Slutpunkt 
| -------- | ------------- | 
| Azure Resource Manager (administratör) | adminmanagment. [region]. [external_domain] |
| Azure Resource Manager (användare) | hantering. [region]. [external_domain] |
| Portalen (administratör) | adminportal. [region]. [external_domain] |
| Portalen (användare) | portalen. [region]. [external_domain] |
| Key Vault (användare) | &#42;. valvet. [region]. [external_domain] |
| Key Vault (administratör) | &#42;. adminvault. [region]. [external_domain] |
| Lagring | &#42;. BLOB-objekt. [region]. [external_domain]<br>&#42;. tabell. [region]. [external_domain]<br>&#42;. kö. [region]. [external_domain]  |
| Diagrammet ** | diagram. [region]. [external_domain] |
| AD FS ** | AD FS. [region]. [external_domain] |
| | |

** Certifikat för diagram och AD FS-slutpunkter krävs endast för AD FS-distributioner.

Om du vill använda en enda jokerteckencertifikat måste totalt sex Alternativt ämnesnamn (SAN för den första Azure-stacken distributionen). Dessa SAN är: 

- &#42;. [region]. [external_domain]
- &#42;. valvet. [region]. [external_domain]
- &#42;. adminvault. [region]. [external_domain]
- &#42;. BLOB-objekt. [region]. [external_domain]
- &#42;. tabell. [region]. [external_domain] 
- &#42;. kö. [region]. [external_domain]

## <a name="time-synchronization"></a>Tidssynkronisering

Azure-stacken tidsserver måste du synkronisera med en extern tid-server som matchas via IP-adress. En tidsserver på företagsnätverket krävs för en frånkopplad distribution.

## <a name="network-connectivity"></a>Nätverksanslutning

### <a name="dns-integration"></a>DNS-integrering

För att lösa externa DNS-namn från Azure-stacken (till exempel www.bing.com), måste du ange DNS-servrar som Azure Stack kan använda för att vidarebefordra som Azure-stacken inte är auktoritära DNS-förfrågningar. Du måste ange minst två servrar som ska användas som DNS-vidarebefordrare för feltolerans som distributionen indata.

Om du vill matcha DNS-namn Azure Stack slutpunkter från utanför Azure-stacken (till exempel från företagets skogen), måste du integrera DNS-servrar som är värdar för externa DNS-zonen för Azure-stacken med DNS-servrar som är värdar för den överordnade zonen som du vill använda. Detta kräver att DNS-namnmatchningen mellan Azure-stacken och befintliga DNS-zoner i datacentret. För att åstadkomma detta ska du använda metoderna, till exempel villkorlig vidarebefordran eller zonen delegering. Vi rekommenderar villkorlig vidarebefordran om du har direkt kontroll över DNS-servrar som är värdar för den överordnade zonen för din Azure-stacken externt DNS-namnområde. (Om din externa Azure Stack DNS-zon visas som en underordnad domän till företagets domännamn (till exempel azurestack.contoso.com och contoso.com), måste du använda zondelegering i stället.

### <a name="network-infrastructure"></a>Nätverksinfrastruktur

Nätverkets infrastruktur för Azure-Stack består av flera logiska nätverk som har konfigurerats med växlar. Följande diagram visar dessa logiska nätverk och hur de samverkar med det top-of-racket (TOR) hanteringsstyrenhet för baskort (BMC) och border (kunden) nätverksväxlar.

![Logiska nätverksanslutningar för diagram och växel](media/azure-stack-planning-considerations/NetworkDiagram.png)

I följande tabell visas de logiska nätverken och associerade IPv4-undernät områden som måste du planera för.

| Logiskt nätverk | Beskrivning | Storlek | 
| -------- | ------------- | ------------ | 
| Offentliga VIP | Offentliga IP-adresser för en liten uppsättning Azure Stack-tjänster med resten används av virtuella datorer. Infrastrukturen i Azure-stacken använder 32-adresser från det här nätverket. Om du planerar att använda App Service och SQL-resursproviders använder detta 7 mer. | / 26 (62 värdar) - /22 (1022 värdar)<br><br>Rekommenderat = /24 (254 värdar) | 
| Växeln-infrastruktur | Point-to-Point IP-adresser för routning, dedikerad växla hanteringsgränssnitt och loopback-adresser som är tilldelad till växeln. | /26 | 
| Infrastruktur | Används för Azure-stacken interna komponenter för att kommunicera. | /24 |
| Privat | Används för lagringsnätverk och privata virtuella IP-adresser. | /24 | 
| BMC | Används för att kommunicera med bmc på de fysiska värdarna. | /27 | 
| | | |

### <a name="uplink-to-border-switches"></a>Överordnad länk på kantlinjen växlar

Du behöver en överordnad som konfigurerats på border växlar i datacentret. Du kan vidarebefordra trafiken Layer 3 från växlarna top-of-rack (TOR) som ingår i ett Azure-stacken integrerat system med någon av följande metoder:

- Border Gateway Protocol (BGP) 
- statisk routning

Vi rekommenderar BGP eftersom det låter automatisk uppdatering av vägar som publicerats av den programvara Load Balancing multiplexor (SLB MUX) till externa nätverk. Detta är viktigt om du lägger till offentliga IP-adressintervall efter distributionen. Om du gör BGP-peering från TOR-växlar till den sammanlagda växeln att TOR-växlar som är ansluten till, offentliga IP-adressintervall som du lägger till efter distributionen annonseras automatiskt till den sammanlagda växeln utan manuella åtgärder. Om du använder statisk routning måste du manuellt uppdatera vägar för nya adressintervall varje gång du lägger till en offentlig IP-adressintervall.

### <a name="proxy-server"></a>Proxyserver

Azure-stacken stöder endast transparent proxy-servrar. En transparent proxy fångar upp begäranden på nätverksnivå utan någon särskild klientkonfiguration.

### <a name="publish-azure-stack-services"></a>Publicera Azure Stack-tjänster

Du behöver göra Azure Stack tjänster tillgängliga för användare från utanför Azure-stacken. Azure-stacken ställer in olika slutpunkter för dess infrastrukturroller. Dessa slutpunkter tilldelas VIP-adresser från den offentliga IP-adresspoolen. En DNS-post skapas för varje slutpunkt i externa DNS-zonen som angavs vid tidpunkten för distribution. Användarportalen är t.ex DNS-värdpost av ”Företagsportalen. <*region*>. <*external_FQDN*>”. 

#### <a name="ports-and-urls"></a>URL: er och portar

Se Azure Stack-tjänster (till exempel portaler, Azure Resource Manager, DNS, osv) tillgängliga till externa nätverk, måste du tillåta inkommande trafik till dessa slutpunkter för specifika URL-adresser, portar och protokoll.
 
I en distribution där en transparent proxy överordnad länk till en traditionell proxyserver måste du tillåta specifika portar och URL: er för utgående kommunikation. Dessa inkluderar portar och URL: er för identitet, marketplace syndikering, korrigeringsfil och uppdateringen, registrering och användningsdata.

Mer information finns i:
- [Inkommande portar och protokoll](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-protocols-inbound)
- [Utgående portar och URL: er](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-urls-outbound)

### <a name="connect-azure-stack-to-azure"></a>Ansluta Azure Stack till Azure

Scenarier med hybrid cloud behöver du planera hur du vill ansluta Azure Stack till Azure. Det finns två sätt att ansluta virtuella nätverk i Azure-stacken till virtuella nätverk i Azure: 
- **Plats-till-plats**. Ett virtuellt privat nätverk (VPN)-anslutning via IPsec (IKE v1 och v2 för IKE). Den här typen av anslutning kräver en VPN-enhet eller Routning och fjärråtkomst (RRAS). Mer information om VPN-gatewayer i Azure finns [om VPN-Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Kommunikation via den här tunnel är krypterad och är säker. Dock begränsas bandbredd av maximalt dataflöde för tunneln (100 – 200 Mbit/s).
- **Utgående NAT**. Som standard kommer alla virtuella datorer i Azure-stacken är ansluten till externa nätverk via utgående NAT. Varje virtuellt nätverk som skapats i Azure-stacken hämtar en offentlig IP-adress som tilldelats. Om den virtuella datorn direkt har tilldelats en offentlig IP-adress, eller bakom en belastningsutjämnare med en offentlig IP-adress, har utgående åtkomst via utgående NAT med VIP för det virtuella nätverket. Detta fungerar endast för kommunikation som initieras av den virtuella datorn och är avsedd för externa nätverk (internet eller intranätet). Den kan inte användas för att kommunicera med den virtuella datorn från utanför.

#### <a name="hybrid-connectivity-options"></a>Hybrid anslutningsalternativ

Hybridanslutning är det viktigt att tänka på vilken typ av distribution du vill erbjuda och där det ska distribueras. Du behöver avgöra om du behöver isolera nätverkstrafiken per klient, och om du har ett intranät eller internet-distribution.

- **Stöd för en innehavare Azure Stack**. Ett Azure Stack-distribution som ser ut, minst ur nätverk som om det är en klient. Det kan finnas många klient prenumerationer, men som alla intranät-tjänster all trafik färdas i samma nätverk. Nätverkstrafik från en prenumeration färdas över samma nätverksanslutning som en annan prenumeration och behöver inte isoleras via en krypterad tunnel.

- **Flera innehavare Azure Stack**. En Azure-stacken distribution där varje innehavare prenumeration trafik som är bunden till nätverk som är externa för Azure-stacken måste isoleras från andra klienter nätverkstrafik.
 
- **Intranät distribution**. En Azure-Stack-distribution som placeras på en företags-intranät, vanligtvis på privata IP-adressutrymme och bakom en eller flera brandväggar. De offentliga IP-adresserna är inte helt offentliga som de inte kan dirigeras direkt via det offentliga internet.

- **Distribution av Internet**. Ett Azure Stack-distribution som är anslutet till offentligt internet och använder internet-dirigerbara offentliga IP-adresser för det offentliga VIP-adressintervallet. Distributionen kan fortfarande hamnar bakom en brandvägg, men det offentliga VIP-intervallet kan nås direkt från offentliga internet och Azure.
 
I följande tabell sammanfattas hybrid anslutningen scenarier med experter, nackdelar och användningsfall.

| Scenario | Anslutningsmetod | Tekniker | Nackdelar | Bra för |
| -- | -- | --| -- | --|
| Enkel klient Azure-stacken, intranät-distribution | Utgående NAT | Bättre bandbredd för överföring av snabbare. Enkel att implementera; Inga gateways som krävs. | Trafik som inte är krypterade; Ingen isolering eller kryptering utöver TOR. | Enterprise-distributioner där alla klienter är lika betrodda.<br><br>Företag som har en Azure ExpressRoute-krets till Azure. |
| Flera innehavare Azure stacken, intranät-distribution | Plats-till-plats VPN | Trafiken från innehavaren VNet till mål är säker. | Bandbredd begränsas av plats-till-plats VPN-tunnel.<br><br>Kräver en gateway i det virtuella nätverket och en VPN-enhet i målnätverket. | Enterprise-distributioner där vissa klienttrafik måste skyddas från andra klienter. |
| Enkel klient Azure-stacken, internet-distribution | Utgående NAT | Bättre bandbredd för överföring av snabbare. | Trafik som inte är krypterade; Ingen isolering eller kryptering utöver TOR. | Värdar för scenarier där klienten hämtar sina egna Azure Stack-distribution och en särskild krets Azure Stack-miljön. Till exempel ExpressRoute och Multiprotocol etikett växlar (MPLS).
| Flera innehavare Azure stacken, internet-distribution | Plats-till-plats VPN | Trafiken från innehavaren VNet till mål är säker. | Bandbredd begränsas av plats-till-plats VPN-tunnel.<br><br>Kräver en gateway i det virtuella nätverket och en VPN-enhet i målnätverket. | Värd för scenarier där providern vill erbjuda ett moln med flera innehavare, där klienterna inte förtroende för varandra och trafik måste vara krypterad.
|  |  |  |  |  |

#### <a name="using-expressroute"></a>Med hjälp av ExpressRoute

Du kan ansluta Azure Stack till Azure via [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) för både en klient intranät- och flera innehavare scenarier. Du behöver en etablerade ExpressRoute-krets via [en anslutning provider](https://docs.microsoft.com/azure/expressroute/expressroute-locations).

Följande diagram visar ExpressRoute för en enskild klient-scenario (där ”kundens anslutning” är ExpressRoute-kretsen).

![Diagram som visar en klient ExpressRoute scenario](media/azure-stack-planning-considerations/ExpressRouteSingleTenant.PNG)

Följande diagram visar ExpressRoute för ett scenario med flera innehavare.

![Diagram som visar flera innehavare ExpressRoute scenario](media/azure-stack-planning-considerations/ExpressRouteMultiTenant.PNG)

## <a name="external-monitoring"></a>Externa övervakning
Hämta en enda vy av alla aviseringar från dina Azure-stacken distribution och enheter och integrera aviseringar i befintliga IT service management-arbetsflöden för biljetter, kan du integrera Azure stacken med externa datacenter övervakningslösningar.

Maskinvara livscykel värden kan ingår i Azure Stack-lösningen, en dator utanför Azure-stacken som kör OEM-tillverkarens hanteringsverktyg för maskinvara. Du kan använda dessa verktyg eller andra lösningar som kan integreras direkt med befintliga lösningar övervakning i ditt datacenter.

I följande tabell sammanfattas i listan över tillgängliga alternativ.

| Område | Externa övervakningslösning |
| -- | -- |
| Azure Stack-programvara | - [Azure-stacken hanteringspaket för Operations Manager](https://azure.microsoft.com/blog/management-pack-for-microsoft-azure-stack-now-available/)<br>- [Nagios plugin-program](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)<br>-REST-baserad API-anrop | 
| Fysiska servrar (bmc via IPMI) | -Hanteringspaket för operations Manager-leverantör<br>-OEM tillverkarens maskinvarulösning<br>-Maskinvaruleverantören Nagios plugin-program | OEM-partner stöds övervakningslösning (ingår) | 
| Nätverksenheter (SNMP) | -Operations Manager identifiering av nätverksenheter<br>-OEM tillverkarens maskinvarulösning<br>-Nagios växla plugin-program |
| Hälsoövervakning för klienten prenumeration | - [System Center Management Pack för Windows Azure](https://www.microsoft.com/download/details.aspx?id=50013) | 
|  |  | 

Observera följande krav:
- Lösningen som du använder måste vara utan Agent. Du kan inte installera agenter från tredje part i Azure Stack-komponenter. 
- Om du vill använda System Center Operations Manager kräver Operations Manager 2012 R2 eller Operations Manager 2016.

## <a name="backup-and-disaster-recovery"></a>Säkerhetskopiering och katastrofåterställning

Planera för säkerhetskopiering och katastrofåterställning omfattar planering för både den underliggande Azure Stack infrastruktur som är värd för virtuella datorer för IaaS och PaaS-tjänster och för klient-program och data. Du måste planera för dessa separat.

### <a name="protect-infrastructure-components"></a>Skydda infrastrukturkomponenter

Azure-stacken säkerhetskopierar infrastrukturkomponenter till en resurs som du anger.

- Du behöver en extern SMB-filresurs på en befintlig Windows-baserad filserver eller en enhet från tredje part.
- Du bör använda samma resursen för säkerhetskopiering av nätverksväxlar och maskinvara livscykel värden. Maskinvaruleverantören OEM hjälper dig att ge vägledning för säkerhetskopiering och återställning av dessa komponenter som dessa är externa för Azure-stacken. Du är ansvarig för att köra säkerhetskopiering arbetsflöden baserat på en rekommendation för OEM-tillverkare.

Om oåterkallelig dataförlust uppstår, kan du använda infrastruktur för att säkerhetskopiera till Anger startvärde distribution data, till exempel distribution indata och identifierare, tjänstkonton Certifikatutfärdarens rotcertifikat, externa resurser (i frånkopplade distributioner), planer, erbjudanden prenumerationer, kvoter, RBAC principen och rollen tilldelningar och hemligheter i Nyckelvalvet.
 
### <a name="protect-tenant-applications-on-iaas-virtual-machines"></a>Skydda program för klienter på IaaS-virtuella datorer

Azure-stacken säkerhetskopierar inte upp klient program och data. Du måste planera för säkerhetskopiering och disaster recovery-skydd till ett mål som är externa för Azure-stacken. Klient-skydd är en klient-driven aktivitet. För IaaS-virtuella datorer använda klienter-gäst tekniker för att skydda mappar, programdata och systemtillstånd. Som en enterprise- eller service provider, kan du dock vill erbjuda en säkerhetskopierings- och återställningslösning i samma datacenter eller externt i ett moln.

Om du vill säkerhetskopiera Linux eller Windows IaaS virtuella datorer måste du använda säkerhetskopiering produkter med åtkomst till gästoperativsystemet för att skydda fil, mapp, operativsystemtillstånd och programdata. Du kan använda Azure-säkerhetskopiering, System Center Data Center Protection Manager eller produkter från tredje part som stöds.

För att replikera data till en sekundär plats och dirigera programmet redundans om en olycka inträffar, kan du använda Azure Site Recovery eller produkter från tredje part. (Vid den första versionen av integrerade system Azure Site Recovery inte stöd för återställning efter fel. Men kan du uppnå återställning via en manuell process.) Program som stöder intern replikering (till exempel Microsoft SQL Server) kan också replikera data till en annan plats där programmet körs.

> [!IMPORTANT]
> Vid den första versionen av integrerade system ska vi stöder skydd tekniker som arbetar på gästnivå för en virtuell IaaS-dator. Du kan inte installera agenter på underliggande infrastruktur-servrar.

## <a name="next-steps"></a>Nästa steg

- Information om användningsområden, köpa, partners och OEM maskinvaruleverantörer finns i [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) produktsidan.
- Information om plan och geo tillgänglighet för Azure-stacken integrerade system finns i faktabladet: [Azure stacken: ett tillägg för Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
