---
title: Riktlinjer för betrodda Internet-anslutning för Azure
description: Betrodda vägledning för Internet-anslutning för Azure och SaaS-tjänster
services: security
author: dlapiduz
ms.assetid: 09511e03-a862-4443-81ac-ede815bdaf25
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: dlap
ms.openlocfilehash: 7b813500eecba3aa1902c28b9b7c56da6c4516b7
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967555"
---
# <a name="trusted-internet-connection-guidance"></a>Vägledning för betrodda Internet-anslutning

## <a name="background"></a>Bakgrund

Syftet med initiativet betrodda Internet anslutningar (RANKNINGSLISTA), är att optimera och standardisera säkerheten för enskilda externa anslutningar som används av federala myndigheter. Principen som beskrivs i OMB (Office för hantering och Budget) [PM M-08-05](https://georgewbush-whitehouse.archives.gov/omb/memoranda/fy2008/m08-05.pdf).

I November 2007 upprätta OMB ärende-program för att förbättra perimetersäkerhet för federala nätverk och incidenthantering funktioner. RANKNINGSLISTA har utformats för att analysera nätverk all inkommande och utgående .gov trafik att identifiera specifika signaturer och mönster-baserade data och identifiera avvikande användaraktiviteter, till exempel botnät aktivitet. Myndigheter har förordnat att konsolidera sina externa nätverksanslutningar och har all trafik dirigeras via intrång upptäcka och förhindra enheter (kallas EINSTEIN) som befann sig i ett begränsat antal network-slutpunkter (kallas betrodda Internet Anslutningar).

Enkelt uttryckt är syftet med RANKNINGSLISTA för myndigheter känna till:
- Vem är i nätverket (behörighet eller Ej behörig)?
- När nätverket används och varför?
- Vilka resurser används?

Idag alla agency externa anslutningar måste dirigeras via en godkänd OMB ärende. Federala myndigheter måste delta i programmet RANKNINGSLISTA antingen som en RANKNINGSLISTA åtkomst Provider (TICAP) eller genom att upphandlande tjänster med något av de viktigaste Internetleverantörer för nivå 1 kallas leverantörer av hanterade betrodda Internet Protocol Service (MTIPS).  RANKNINGSLISTA innehåller obligatoriska viktiga funktioner som utförs i dag av myndighet och MTIPS-providern. I den aktuella versionen av ärende, EINSTEIN version 2 intrångsidentifiering och EINSTEIN version 3 accelererat (3A) intrång dataförlustskydd enheter distribueras på varje TICAP och MTIPS och myndigheten upprättar ett samförståndsavtal med avdelning Hemland säkerhet (DHS) att distribuera EINSTEIN funktioner till federala system.

Som en del av dess ansvar att skydda nätverket .gov, kräver DHS rådata feeds agency Netflow data att kombinera incidenter i federal företaget och utföra analyser med hjälp av särskilda verktyg. DHS routrar ger möjlighet att samla in IP-nätverkstrafik när den anländer till eller lämnar ett gränssnitt. Genom att analysera flödesdata net kan kan en nätverksadministratör fastställa sådant som källa och mål för trafiken, klass av tjänsten. NET flödesdata betraktas som ”inte innehåll data” (till exempel sidhuvud, käll-IP, mål-IP, o.s.v.) och tillåter DHS veta information kring innehåll. det vill säga som gjorde vad och hur lång tid.

Initiativet innehåller också säkerhetsprinciper, riktlinjer och ramverk som förutsätter att den lokala infrastrukturen. När myndigheter går över till molnet som ger kostnadsbesparingar, operationell effektivitet och innovation, är kraven på implementering av ärende i vissa fall långsammare nätverkstrafik och begränsa hastighet och flexibilitet med vilka government användare kan åtkomst till sina molnbaserade data.

Den här artikeln beskriver hur myndigheter kan använda Microsoft Azure Government för att uppnå efterlevnad med principen för RANKNINGSLISTA i både IaaS och PaaS-tjänster.

## <a name="summary-of-azure-networking-options"></a>Sammanfattning av nätverksalternativ för Azure

Det finns tre huvudsakliga alternativ när du ansluter till Azure-tjänster:

1. Direkt Internet-anslutning: ansluta till Azure-tjänster direkt via en öppen Internetanslutning. Mediet är offentliga och anslutningen. Program- och transport filnivåkryptering åberopas för att bevara sekretessen. Bandbredd begränsas av en plats-anslutning till Internet och flera aktiva providers kan användas för att säkerställa återhämtning
1. Virtuellt privat nätverk: Ansluta till Azure Virtual Network privat med en VPN-Gateway.
Mediet är offentlig, som den inom en plats standard Internet-anslutning, men anslutningen krypteras i en tunnel för att bevara sekretessen. Bandbredd är begränsad beroende på VPN-enheter och den konfiguration som valts. Azure punkt-till-plats-anslutningar är vanligtvis begränsad till 100 Mbit/s medan plats-till-plats-anslutningar är begränsade till 1,25 Gbit/s.
1. Microsoft ExpressRoute: ExpressRoute är en direkt anslutning till Microsoft-tjänster. Eftersom anslutningen är via en isolerad fiber channel, kan anslutningen vara offentligt eller privat beroende på hur används. Bandbredden som är vanligtvis begränsad till högst 10 Gbit/s.

Det finns flera sätt att uppfylla kraven för betrodda Internet-anslutning bilaga H (Molnöverväganden), finns i den avdelning av Inrikessäkerhetsdepartements, ”betrodd Internet-anslutningar (RANKNINGSLISTA) arkitektur referensdokumentet, Version 2.0”. I det här dokumentet kommer DHS ärende vägledning hänvisas till som RANKNINGSLISTA 2.0.

Om du vill aktivera anslutningen från avdelning eller regeringsorgan (D/A) till Azure eller Office 365, utan att dirigera trafiken via D/A-ärende måste D/A använda en krypterad tunnel och/eller en dedikerad anslutning till molnet Service Provider (CSP). CSP-tjänster kan kontrollera anslutningen till D/A molntillgångar inte erbjuds till internet för direkta agency personal åtkomst.

O365 är kompatibel med RANKNINGSLISTA 2.0 bilaga tim med hjälp av antingen Express-Route med [Microsoft-Peering](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains) aktiverad eller internet-anslutning som krypterar all trafik med hjälp av TLS 1.2.  D/A slutanvändare i D/A-nätverket kan ansluta via sina agency nätverks- och RANKNINGSLISTA infrastruktur via internet. Alla fjärranslutna internet-åtkomst till O365 blockeras och dirigeras genom. D/A kan också ansluta till O365 via en Expressroute-anslutning med Microsoft-peering, vilket är en typ av offentlig Peering, aktiverat.  

För Azure uppfyller alternativ 2 (VPN) och 3 (ExpressRoute) dessa krav när de används tillsammans med tjänster som begränsar åtkomsten till Internet.

![Microsoft betrodda (RANKNINGSLISTA) Anslutningsdiagram](media/tic-diagram-a.png)

## <a name="how-azure-infrastructure-as-a-service-offerings-can-help-with-tic-compliance"></a>Hur Azure-infrastruktur som en Tjänsterbjudanden kan hjälpa till med ärende efterlevnad

Det är relativt enkelt att följa RANKNINGSLISTA princip med hjälp av IaaS eftersom Azure-kunder hantera sina egna virtuella nätverk som routning.

Viktigaste kravet för att garantera efterlevnad med referensarkitektur ärende är att säkerställa att ditt virtuella nätverk (VNet) blir en privat förlängning av avdelning eller myndighets-nätverket. Bli en _privata_ tillägg, principen kräver ingen trafik lämnar nätverket utom via lokala ärende nätverksanslutningen. Den här processen är känt som ”Tvingad tunneltrafik”, som under används för ärende efterlevnad, är processen att routning all trafik från alla system i CSP-miljö för att gå via en lokal gateway på en organisations nätverk ut till internet via ärende.

Efterlevnad i Azure IaaS-ärende kan delas i två viktiga steg:

1. Konfiguration
1. Granskning

### <a name="azure-iaas-tic-compliance-configuration"></a>Azure IaaS RANKNINGSLISTA Efterlevnadskonfiguration

Du måste först förhindra direkt Internetåtkomst till det virtuella nätverket och sedan tvingar Internettrafik via lokala nätverk om du vill konfigurera en arkitektur för ärende-kompatibla med Azure.

#### <a name="prevent-direct-internet-access"></a>Förhindra direkt Internetåtkomst

Azure IaaS-nätverk utförs via virtuella nätverk som består av undernät, som Network Interface domänkontrollanter (NIC) av virtuella datorer är kopplade.

Det enklaste scenariot som stöd för ärende efterlevnad är att säkerställa att en virtuell dator eller en samling i detta, det går inte att ansluta till alla externa resurser. Frånkoppling från externa nätverk kan vara säker på använda Nätverkssäkerhetsgrupper (NSG) som kan användas för att styra trafik till en eller flera nätverkskort eller undernät i det virtuella nätverket. En NSG innehåller åtkomstkontrollregler som tillåter eller nekar trafik baserat på trafikriktningen, protokollet, källadressen och -porten, samt måladressen och -porten. Reglerna för en NSG kan ändras när som helst och ändringar tillämpas på alla kopplade instanser.  Läs mer om hur du skapar en NSG, i den här artikeln [skapar en NSG](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal).

#### <a name="force-internet-traffic-through-on-premises-network"></a>Tvingar Internettrafik via lokala nätverk

Azure skapar automatiskt systemvägar och tilldelar vägarna till varje undernät i ett virtuellt nätverk. Du kan inte skapa systemvägar, eller kan du ta bort systemvägar, men du kan åsidosätta vissa systemvägar med anpassade vägar. Azure skapar standardsystemvägar för varje undernät och lägger till ytterligare valfria standardvägar till specifika undernät eller varje undernät när du använder specifika funktioner i Azure. Den här routning garanterar att trafik inom det virtuella nätverket stannar kvar i det virtuella nätverket, IANA avses privata adressutrymmen, t.ex. 10.0.0.0/8 ignoreras (såvida inte ingår i det virtuella nätverkets adressutrymme) och ”sista utväg” Routning av 0.0.0.0/0 till det virtuella nätverkets Internet-slutpunkten.

![RANKNINGSLISTA Tvingad tunneltrafik](media/tic-diagram-c.png)

För att säkerställa att all trafik passerar D/A ärende, måste all trafik som lämnar det virtuella nätverk dirigeras via den lokala anslutningen.  Du skapar anpassade vägar genom att antingen skapa användardefinierade vägar eller genom att utbyta border gateway protocol (BGP) vägar mellan din lokala nätverksgateway och en Azure virtuell nätverksgateway. Mer information om användardefinierade vägar finns på https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined. Mer information om Border Gateway Protocol hittar även vid https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol.

#### <a name="user-defined-routes"></a>Användardefinierade vägar

Om du använder en routningsbaserad virtuell nätverksgateway Tvingad tunneltrafik kan du göra i Azure genom att lägga till en användardefinierad väg (UDR) inställningen 0.0.0.0/0 trafik ska dirigeras till en ”nästa hopp” på din virtuella nätverksgateway. Azure prioriterar användaren användardefinierade vägar över användardefinierade vägar i systemet så att det skulle resultera i alla icke-VNet-trafik som skickas till din virtuella nätverks-Gateway, som sedan kan vidarebefordra det till den lokala. När du definierat användardefinierade vägen som är associerade med alla undernät som finns eller nyligen har skapat i alla virtuella nätverk i din Azure-miljö.

![användardefinierade vägar och ärende](media/tic-diagram-d.png)

#### <a name="border-gateway-protocol"></a>Border Gateway Protocol

Om du använder ExpressRoute eller Border Gateway Protocol (BGP) aktiverad virtuell nätverksgateway måste är BGP den rekommenderade metoden för att annonsera vägar. Med en BGP garanterar annonserade väg med 0.0.0.0/0, ExpressRoute och BGP medveten om virtuella Nätverksgatewayer att den här standardvägen tillämpas på alla undernät i ditt virtuella nätverk.

### <a name="azure-iaas-tic-compliance-auditing"></a>Azure IaaS-ärende efterlevnad granskning

Azure erbjuder flera sätt att granska ärende efterlevnad.

#### <a name="effective-routes"></a>Effektiva vägar

Du kan se ”gällande routningar” av en viss virtuell dator, ett specifikt nätverkskort eller en användardefinierade routningstabellen för att bekräfta att din standardväg har spridits. Detta kan göras via Azure portal enligt beskrivningen i https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal, eller via PowerShell enligt beskrivningen i https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-powershell. Bladet gällande routningar kan du se den relevanta användardefinierade vägar, BGP annonseras vägar och systemvägar som tillämpas på relevanta entiteten, som visas nedan.

![Skärmbild av vägar](media/tic-screen-1.png)

**Obs**: du kan inte visa de effektiva vägarna för ett nätverkskort om inte det som är associerade med en aktiv virtuell dator.

#### <a name="network-watcher"></a>Network Watcher

Azure Network Watcher erbjuder flera olika verktyg som kan användas för att granska RANKNINGSLISTA efterlevnad.  Mer information om Network Watcher på https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview.

##### <a name="network-security-groups-flow-logs"></a>Flödesloggar för Network Security grupper 

Azure Network Watcher ger möjlighet att samla in flödesloggar för nätverket som du anger metadata med IP-trafik. Förutom andra data innehåller flow nätverksloggar källa och mål-adresserna för mål. Detta, i kombination med loggar från den virtuella Nätverksgatewayen, lokala edge-enheter och/eller ärende, tillåter för att övervaka att all trafik verkligen håller routade lokalt. 

## <a name="how-azure-platform-as-a-service-offerings-can-help-with-tic-compliance"></a>Hur Azure-plattformen som en Tjänsterbjudanden kan hjälpa till med ärende efterlevnad

Azure PaaS-tjänster som Azure Storage är tillgängliga via en URL som kan nås av Internet. Vem som helst med godkända autentiseringsuppgifter kan komma åt resurs, till exempel ett lagringskonto varifrån som helst utan att gå igenom ett ärende. Därför ingå många myndigheter felaktigt att Azure PaaS-tjänster inte är kompatibla med principer för ärende. Många Azure PaaS-tjänster kan i själva verket vara kompatibel med RANKNINGSLISTA princip med samma arkitektur som en RANKNINGSLISTA-kompatibla IaaS-miljön som beskrivs ovan om de kan kopplas till ett virtuellt nätverk (VNet). Integrera Azure PaaS-tjänster med ett virtuellt Azure nätverk tillåter att tjänsten är privat kan nås från det virtuella nätverket och tillåter anpassad routning för 0.0.0.0/0 som ska användas via användardefinierade vägar eller BGP, säkerställer att alla Internet-bunden trafik routade lokalt till Bläddra bland ärende.  Vissa Azure-tjänster kan integreras i virtuella nätverk med hjälp av följande mönster:

- **Distribuera en dedikerad instans av tjänsten**: ett ökande antal PaaS-tjänster kan distribueras som dedikerade instanser med ett virtuellt nätverk kopplade slutpunkter. Till exempel kan en App Service Environment (ASE) distribueras i ”isolerade” läge så att dess nätverksslutpunkten att vara begränsad till ett virtuellt nätverk. Denna ASE kan sedan vara värd för många Azure PaaS-tjänster, till exempel Webbappar, API: er och funktioner.
- **VNet-tjänstslutpunkter**: ett ökande antal PaaS-tjänster tillåta att flytta sina slutpunkt till VNet privata IP-adress i stället för en offentlig adress.

Tjänster som stöder distribution av dedikerade instanser i ett virtuellt nätverk eller tjänstslutpunkter från och med maj 2018 finns nedan: * (tillgänglighet representerar kommersiella Azure Azure Government tillgänglighet väntande).

### <a name="service-endpoints"></a>Serviceslutpunkter

|Tjänst                   |Status            |
|--------------------------|------------------|
|Azure KeyVault            | Privat förhandsgranskning  |
|Cosmos DB                 | Privat förhandsgranskning  |
|Identitet                  | Privat förhandsgranskning  |
|Azure Data Lake           | Privat förhandsgranskning  |
|SQL Postgress/Mysql       | Privat förhandsgranskning  |
|Azure SQL Data Warehouse  | Offentlig förhandsversion   |
|Azure SQL                 | Allmän tillgänglighet (GA)               |
|Storage                   | Allmän tillgänglighet (GA)               |

### <a name="vnet-injection"></a>VNet-inmatning

|Tjänst                            |Status            |
|-----------------------------------|------------------|
|SQL-hanterad instans               | Offentlig förhandsversion   |
|Azure Container Service(AKS)       | Offentlig förhandsversion   |
|Service Fabric                     | Allmän tillgänglighet (GA)               |
|API Management                     | Allmän tillgänglighet (GA)               |
|Azure Active Directory             | Allmän tillgänglighet (GA)               |
|Azure Batch                        | Allmän tillgänglighet (GA)               |
|ASE                                | Allmän tillgänglighet (GA)               |
|Redis                              | Allmän tillgänglighet (GA)               |
|HDI                                | Allmän tillgänglighet (GA)               |
|Compute virtual machine scale Sets  | Allmän tillgänglighet (GA)               |
|Compute-molntjänst              | Allmän tillgänglighet (GA)               |

### <a name="vnet-integration-details"></a>Information om VNet-integrering

I nedanstående diagram går igenom allmänna nätverksflödet för åtkomst till PaaS-tjänster med hjälp av VNet-inmatning och Servicetunnlar.  Mer information om Network Service-gatewayer, nätverkets och Tjänsttaggar finns här https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags.

![PaaS anslutningsalternativ för ärende](media/tic-diagram-e.png)

1. Privat anslutning till Azure med ExpressRoute. ExpressRoute privat peering Tvingad tunneltrafik används för att tvinga all trafik som kundens virtuella nätverk via ExpressRoute tillbaka till den lokala. Microsoft-Peering är inte obligatoriskt.
2. Azure VPN-Gateway som används tillsammans med Microsoft för ExpressRoute-peering kan användas för att täcka över IPSec-kryptering för slutpunkt till slutpunkt mellan kundens virtuella nätverk och lokala edge. 
3. Nätverksanslutningen till kundens virtuella nätverk kontrolleras med hjälp av Nätverkssäkerhetsgrupper (NSG) så att kunder kan bevilja/neka baserat på IP, port och protokoll.
4. Kundens virtuella nätverk utökas till PaaS-tjänst genom att skapa en tjänstslutpunkt för kundens-tjänsten.
5. PaaS-tjänsteslutpunkt är skyddad till standard neka alla och endast tillåta åtkomst från angivna undernät i kundens virtuella nätverk.  Standard neka också innehåller anslutningar från Internet.
6. Andra Azure-tjänster som behöver åtkomst till resurser i kundens virtuella nätverk måste vara:  
  a. Distribueras direkt till det virtuella nätverket  
  b. Tillåten selektivt baserat på vägledningen från respektive Azure-tjänsten.

#### <a name="option-1-dedicated-instance-vnet-injection"></a>Alternativ 1: Dedikerade instansen ”VNet-inmatning”

Kunder kan selektivt distribuera dedikerade instanser av en viss Azure-tjänst, till exempel HDInsight, till sina egna virtuella nätverk med VNet-inmatning. Instanser av tjänsten distribueras i ett dedikerat undernät i en kunds VNet. VNet-inmatning kan tjänstresurser vara tillgänglig via de icke-dirigerbara Internetadresser.  Lokala instanser kan komma åt dessa instanser av tjänsten via VNet-adressutrymmet direkt via ExpressRoute eller VPN för plats-till-plats, i stället för att öppna brandväggar offentliga Internet-adressutrymme. Med en dedikerad instans som är kopplade till en slutpunkt, men samma strategier som används för IaaS-ärende efterlevnad kan användas, med standard routning säkerställer den Internet-bunden trafiken omdirigeras till en virtuell nätverksgateway som är bunden till en lokal. Inkommande och utgående åtkomst kan ytterligare kontrolleras via Nätverkssäkerhetsgrupper (NSG) för det angivna undernätet.

![Översiktsdiagram för VNet-inmatning](media/tic-diagram-f.png)

#### <a name="option-2-vnet-service-endpoints"></a>Alternativ 2: VNet-tjänstslutpunkter 

Ett ökande antal Azures tjänster för flera innehavare erbjuder ”tjänstslutpunkt”-funktion, en alternativ metod för att integrera till virtuella Azure-nätverk. Tjänstslutpunkter i virtuella nätverket Utöka ditt VNet IP-adressutrymme och identiteten för ditt VNet till tjänsten via en direktanslutning.  Trafik från det virtuella nätverket till Azure-tjänsten förblir alltid inom Azure-stamnätverket. När en tjänstslutpunkt har aktiverats för en tjänst, kan anslutningar till tjänsten vara begränsad till det virtuella nätverket via principer som exponeras av tjänsten. Åtkomstkontroller tillämpas i plattformen av Azure-tjänsten och åtkomst till låsta resurs beviljas endast om förfrågan kommer från de tillåtna virtuellt nätverk/undernät och/eller två IP-adresser som används för att identifiera den lokala trafiken om du använder ExpressRoute. Detta kan användas för att effektivt förhindra inkommande/utgående trafik från att lämna direkt PaaS Service.

![Tjänstslutpunkter Översiktsdiagram](media/tic-diagram-g.png)

## <a name="using-cloud-native-tools-for-network-situational-awareness"></a>Med hjälp av molnet inbyggda verktyg för nätverk situationsmedvetenhet

Azure tillhandahåller moln inbyggda verktyg för att säkerställa att du har situationsmedvetenhet som krävs för att förstå trafikflöde i nätverket. Det krävs inte följa RANKNINGSLISTA princip, men de kan avsevärt förbättra funktioner för säkerhet.

### <a name="azure-policy"></a>Azure Policy

Azure Policy (https://azure.microsoft.com/services/azure-policy/) är en Azure-tjänst som ger din organisation bättre möjlighet att granska och genomdriva efterlevnadsinitiativ.  För närvarande tillgängligt som offentlig förhandsversion i kommersiella Azure-moln, men ännu inte i Microsoft Azure för myndigheter, ärende säkert kunder kan starta planerings- och testa sina principregler för framtida kompatibilitet trygghet. Azure Policy är inriktad på prenumerationsnivå och tillhandahåller en centraliserad gränssnitt för att hantera initiativ, principdefinitioner, granskning och tvingande resultat och hantering av undantag. Förutom de många inbyggda Azure Policy-definitionerna kan administratörer definiera sina egna anpassade definitioner via enkla json-mall. Microsoft rekommenderar för många kunder prioriteringen av granskning över tvingande där det är möjligt.

Följande exempel principer kan vara användbart för ärende kompatibilitet scenarier:

|Princip  |Exempelscenario  |Starta mall  |
|---------|---------|---------|
|Framtvinga en användardefinierad routningstabell |     Se till att standardvägen på alla virtuella nätverk pekar mot en godkänd virtuella nätverkets Gateway för routning till lokal | https://docs.microsoft.com/azure/azure-policy/scripts/no-user-def-route-table |
|Granska om Network Watcher inte har aktiverats för Region  | Kontrollera att Network Watcher är aktiverat för alla regioner  | https://docs.microsoft.com/azure/azure-policy/scripts/net-watch-not-enabled |
|NSG x i varje undernät  | Se till att en NSG (eller uppsättning godkända NSG: er) med Internet-trafiken blockeras tillämpas på alla undernät i varje virtuellt nätverk | https://docs.microsoft.com/azure/azure-policy/scripts/nsg-on-subnet |
|NSG X på varje nätverkskort | Se till att en Nätverkssäkerhetsgrupp med Internet-trafiken blockeras tillämpas på alla nätverkskort på alla virtuella datorer. | https://docs.microsoft.com/azure/azure-policy/scripts/nsg-on-nic |
|Använd godkända virtuellt nätverk för VM-nätverksgränssnitt  | Kontrollera att alla nätverkskort som är på en godkänd VNet | https://docs.microsoft.com/azure/azure-policy/scripts/use-approved-vnet-vm-nics |
|Tillåtna platser | Se till att alla resurser som distribueras till regioner med kompatibla virtuella nätverk och Network Watcher-konfiguration  | https://docs.microsoft.com/azure/azure-policy/scripts/allowed-locs |
|Otillåtna resurstyper, till exempel PublicIPs  | Förhindra distribution av resurstyper som inte har en plan för efterlevnad. Exempelvis kan den här principen användas för att förhindra distribution av offentliga IP-adressresurser. NSG-regler kan användas för att effektivt blockera inkommande Internettrafik, minskar hindrar användning av offentliga IP-adresser ytterligare risken för angrepp.    | https://docs.microsoft.com/azure/azure-policy/scripts/not-allowed-res-type  |

### <a name="azure-traffic-analytics"></a>Azure trafikanalys

Azure Network Watcher-trafikanalys förbrukar loggdata för flödet och andra loggar att tillhandahålla översikt på hög nivå av nätverkstrafik. Dessa data kan vara användbart för att granska RANKNINGSLISTA efterlevnad och identifiera problemområden. En övergripande instrumentpanel kan användas för att snabbt skärmen som virtuella datorer kommunicerar med internet, vilket skulle ange en fokuserade lista för ärende routning.

![Traffic Analytics skärmbild](media/tic-traffic-analytics-1.png)

”Geomappningen” kan användas för att snabbt identifiera sannolikt fysiska mål för Internet-trafiken för dina virtuella datorer, så att du kan identifiera och testa misstänkta platser eller ändringar i mönster.

![Traffic Analytics skärmbild](media/tic-traffic-analytics-2.png)

En Nätverkskarta topologi kan användas för att snabbt undersöka prissättningen befintliga virtuella nätverk:

![Traffic Analytics skärmbild](media/tic-traffic-analytics-3.png)

### <a name="azure-network-watcher-next-hop"></a>Azure Network Watcher nästa hopp

Nätverk i regioner som övervakas av Network Watcher kan utföra ”nästa hopp” tester, smidig portalbaserad tillgång till skriver i en källa och mål för ett exempel network-flöde som löser Network Watcher ”nästa hopp”-mål. Detta kan användas mot virtuella datorer och exempel Internet-adresser så att den ”nästa hopp” är verkligen den virtuella Nätverksgatewayen.

![Network watcher nästa hopp](media/tic-network-watcher.png)

## <a name="conclusions"></a>Slutsatser

Microsoft Azure, Office 365 och Dynamics 365-åtkomst kan enkelt konfigureras för att bidra till att efterleva RANKNINGSLISTA 2.0 bilaga H vägledning som skriftligt och definierade i maj 2018.  Microsoft är medveten om att den här vägledningen kan ändras och kommer vinnlägga din att hjälpa kunder att uppfylla riktlinjerna i god tid när nya råd är tillgängliga.

## <a name="appendix-tic-patterns-for-common-workloads"></a>Bilaga: RANKNINGSLISTA mönster för vanliga arbetsbelastningar

| Kategori | Arbetsbelastning | IaaS | Dedikerad PaaS / VNet-inmatning  | Serviceslutpunkter  |
|---------|---------|---------|---------|--------|
| Compute | Virtuella Linux-datorer | Ja | | |
| Compute | Virtuella Windows-datorer | Ja | | |
| Compute | Virtual Machine Scale Sets | Ja | | |
| Compute | Azure Functions | | via App Service-miljö (ASE) | |
| Webb och mobilt | Interna webbprogram | | via App Service-miljö (ASE) | |
| Webb och mobilt | Interna mobila program | | via App Service-miljö (ASE) | |
| Webb och mobilt | API Apps | | via App Service-miljö (ASE) | |
| Behållare | Azure Container Service (ACS) | | | Ja |
| Behållare | Azure Container Service (AKS) * | | | Ja |
| Databas | SQL Database | | Azure SQL Database hanterad instans * | Azure SQL |
| Databas | Azure Database for MySQL | | | Ja |
| Databas | Azure Database for PostgreSQL | | | Ja |
| Databas | SQL Data Warehouse | | | Ja |
| Databas | Azure Cosmos DB | | | Ja |
| Databas | Redis Cache | | Ja | |
| Storage | Blobar | Ja | | |
| Storage | Filer | Ja | | |
| Storage | Köer | Ja | | |
| Storage | Tabeller | Ja | | |
| Storage | Diskar | Ja | | |

*: Offentlig förhandsversion i Azure Government från och med maj 2018  
**: Privat förhandsgranskning i Azure Government från och med maj 2018

