---
title: Betrodda Internetanslutningar riktlinjer för Azure
description: Betrodda Internetanslutningar vägledning för Azure och SaaS-tjänster
services: security
author: dlapiduz
ms.assetid: 09511e03-a862-4443-81ac-ede815bdaf25
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: dlap
ms.openlocfilehash: bb186ab2700b147bee3a7dd81474409ccafb76fc
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2019
ms.locfileid: "56341733"
---
# <a name="trusted-internet-connections-guidance"></a>Vägledning för betrodda Internet-anslutningar

Den här artikeln beskriver hur myndigheter kan använda Microsoft Azure Government för att uppnå efterlevnad med initiativet betrodda Internet anslutningar (ärende). Artikeln beskriver hur du använder Azure Government över Azure-infrastruktur som en tjänst (IaaS) och Azure-plattformen som en tjänst (PaaS)-erbjudanden.

## <a name="trusted-internet-connections-overview"></a>Översikt över betrodda Internet-anslutningar

Syftet med initiativet ärende är att optimera och standardisera säkerheten för enskilda externa anslutningar som används av federala myndigheter. Principen som beskrivs i den [Office för hantering och Budget (OMB) PM M-08-05](https://georgewbush-whitehouse.archives.gov/omb/memoranda/fy2008/m08-05.pdf).

I November 2007 upprätta OMB ärende-program för att förbättra perimetersäkerhet för federala nätverk och incidenthantering funktioner. RANKNINGSLISTA har utformats för att analysera nätverk all inkommande och utgående .gov trafik att identifiera specifika signaturer och mönster-baserade data. RANKNINGSLISTA ger avvikande användaraktiviteter, till exempel botnät aktivitet. Myndigheter förordnat att konsolidera sina externa nätverksanslutningar och dirigera all trafik via intrång upptäcka och förhindra enheter som kallas EINSTEIN. Enheterna som finns på ett begränsat antal nätverksslutpunkter som kallas _betrodda Internetanslutningar_.

Målet med ärende är för myndigheter känna till:
- Vem är i nätverket (behörighet eller Ej behörig)?
- När mitt nätverk öppnas och varför?
- Vilka resurser?

Alla agency externa anslutningar måste för närvarande vidarebefordra via en godkänd OMB ärende. Federala myndigheter krävs för att delta i programmet RANKNINGSLISTA som en RANKNINGSLISTA åtkomst Provider (TICAP) eller genom att upphandlande tjänster med en högre nivå 1-Internetleverantörer. Dessa providers kallas för leverantörer av hanterade betrodda Internet Protocol Service (MTIPS). RANKNINGSLISTA innehåller obligatoriska viktiga funktioner som utförs av myndighet och MTIPS-providern. I den aktuella versionen av ärende, EINSTEIN version 2 intrångsidentifiering och EINSTEIN version 3 accelererat (3A) distribueras intrång dataförlustskydd enheter vid varje TICAP och MTIPS. Myndigheten upprättar ett ”samförståndsavtal” med den avdelning av hemland säkerhet (DHS) att distribuera EINSTEIN funktioner till federala system.

Som en del av dess ansvar att skydda nätverket .gov, kräver DHS rådata-feed för myndighet net flödesdata att kombinera incidenter i federal företaget och utföra analyser med hjälp av särskilda verktyg. DHS routrar ger möjlighet att samla in IP-nätverkstrafik när den anländer till eller lämnar ett gränssnitt. Nätverksadministratörer kan analysera informationen net flöde för att fastställa källan och målet för trafiken, tjänsteklass, och så vidare. NET flödesdata anses vara ”icke-content data” liknar rubrik, käll-IP, mål-IP och så vidare. Icke-innehåll kan DHS vill veta mer om innehållet: vem gjorde vad och hur lång tid.

Initiativet innehåller också säkerhetsprinciper, riktlinjer och ramverk som förutsätter att den lokala infrastrukturen. När myndigheter går över till molnet som ger kostnadsbesparingar, operationell effektivitet och innovation, kan implementering kraven för ärende sakta ned nätverkstrafik. Den snabba och smidiga med vilka government användare kan komma åt sina data med molnbaserad är begränsad som ett resultat.

## <a name="azure-networking-options"></a>Azure Nätverksalternativ

Det finns tre huvudsakliga alternativ för att ansluta till Azure-tjänster:

- Direkt internet-anslutning: Anslut till Azure-tjänster direkt via en öppen Internetanslutning. Mediet och anslutningen är offentliga. Program- och transport filnivåkryptering åberopas för att bevara sekretessen. Bandbredd begränsas av en plats-anslutning till internet. Använda flera aktiva provider för att säkerställa återhämtning.
- Virtuellt privat nätverk (VPN): Ansluta till din Azure-nätverk privat med hjälp av en VPN-gateway.
Mediet är offentlig eftersom den inom en plats standard internet-anslutning, men anslutningen krypteras i en tunnel för att bevara sekretessen. Bandbredd är begränsad beroende på VPN-enheter och den konfiguration som valts. Azure punkt-till-plats-anslutningar är vanligtvis begränsad till 100 Mbit/s och plats-till-plats-anslutningar är begränsade till 1,25 Gbit/s.
- Azure ExpressRoute: ExpressRoute är en direkt anslutning till Microsoft-tjänster. Eftersom anslutningen är via en isolerad fiber channel, kan anslutningen vara offentligt eller privat beroende på den konfiguration som används. Bandbredden som är vanligtvis begränsad till högst 10 Gbit/s.

Det finns flera sätt att uppfylla kraven RANKNINGSLISTA bilaga H (Molnöverväganden) som anges i den avdelning av Säkerhetskravs, ”arkitektur för tillförlitliga Internet-anslutningar (RANKNINGSLISTA) referensdokumentet, Version 2.0”. I den här artikeln DHS ärende vägledning kallas **RANKNINGSLISTA 2.0**.

Aktivera anslutningen från den **avdelning eller regeringsorgan (D/A)** till Azure eller Office 365, utan att dirigera trafiken via D/A-ärende D/A måste använda en krypterad tunnel eller en dedikerad anslutning till molntjänstleverantör (CSP). CSP-tjänster kan kontrollera anslutningen till D/A molntillgångar inte erbjuds till det offentliga internet för direkta agency personal åtkomst.

Office 365 är kompatibla med RANKNINGSLISTA 2.0 bilaga H genom att använda antingen ExpressRoute med [Microsoft-Peering](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings) aktiverad eller internet-anslutning som krypterar all trafik med hjälp av TLS 1.2. D/A slutanvändare i D/A-nätverket kan ansluta via sina agency nätverks- och RANKNINGSLISTA infrastruktur via internet. Alla fjärranslutna internet-åtkomst till Office 365 blockeras och dirigerar genom. D/A kan också ansluta till Office 365 via en ExpressRoute-anslutning med Microsoft-Peering (en typ av offentlig peering) aktiverat.  

För Azure, kan andra alternativet (VPN) och tredje alternativet (ExpressRoute) uppfylla dessa krav när de används tillsammans med tjänster som begränsar åtkomsten till internet.

![Microsoft betrodda internet-anslutning (RANKNINGSLISTA)](media/tic-diagram-a.png)

## <a name="azure-infrastructure-as-a-service-offerings"></a>Azure-infrastruktur som en Tjänsterbjudanden

Kompatibilitet med RANKNINGSLISTA princip med hjälp av Azure IaaS är relativt enkla eftersom Azure-kunder hantera sina egna virtuella nätverk som routning.

Viktigaste kravet för att garantera efterlevnad med referensarkitektur ärende är att se till att det virtuella nätverket är ett privat nätverk för D/A-tillägg. Ska vara en _privata_ tillägg, principen kräver ingen trafik lämnar nätverket utom via lokalt ärende nätverksanslutning. Den här processen kallas _Tvingad tunneltrafik_. RANKNINGSLISTA efterlevnad dirigerar processen all trafik från alla system i CSP-miljö via en lokal gateway på en organisations nätverk ut till internet via ärende.

Efterlevnad i Azure IaaS-ärende är uppdelad i två viktiga steg:

- Steg 1: Konfiguration.
- Steg 2: Granskning.

### <a name="azure-iaas-tic-compliance-configuration"></a>Azure IaaS-ärende efterlevnad: Konfiguration

Om du vill konfigurera en arkitektur för ärende-kompatibla med Azure måste du först förhindra direkt Internetåtkomst till det virtuella nätverket och sedan tvingar Internettrafik via det lokala nätverket.

#### <a name="prevent-direct-internet-access"></a>Förhindra direktåtkomst till internet

Azure IaaS-nätverk utförs via virtuella nätverk som består av undernät som network interface domänkontrollanter (NIC) av virtuella datorer är kopplade.

Det enklaste scenariot som stöd för ärende efterlevnad är att säkerställa att en virtuell dator eller en samling av virtuella datorer och det går inte att ansluta till alla externa resurser. Garantera frånkoppling från externa nätverk med hjälp av nätverkssäkerhetsgrupper (NSG). Använda Nätverkssäkerhetsgrupper för trafiken till en eller flera nätverkskort eller undernät i det virtuella nätverket. En NSG innehåller åtkomstkontrollregler som tillåter eller nekar trafik baserat på trafikriktningen, protokollet, källadressen och -porten, samt måladressen och -porten. Du kan ändra reglerna för en NSG när som helst och ändringar tillämpas på alla kopplade instanser. Mer information om hur du skapar en NSG finns [filtrera nätverkstrafik med en nätverkssäkerhetsgrupp](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal).

#### <a name="force-internet-traffic-through-an-on-premises-network"></a>Tvinga Internettrafik via ett lokalt nätverk

Azure skapar automatiskt systemvägar och tilldelar vägarna till varje undernät i ett virtuellt nätverk. Du kan inte skapa eller ta bort systemvägar, men du kan åsidosätta vissa systemvägar med anpassade vägar. Azure skapar standardsystemvägar för varje undernät. Azure lägger till valfria standardvägar till specifika undernät eller varje undernät när du använder specifika funktioner i Azure. Det ser till att den här typen av Routning:
- Trafik inom det virtuella nätverket stannar kvar i det virtuella nätverket.
- Angivet IANA privata adressutrymmen som 10.0.0.0/8 ignoreras om inte de finns med i virtuella nätverkets adressutrymme.
- ”Sista utväg” routning av 0.0.0.0/0 till internet-slutpunkt för virtuellt nätverk.

![RANKNINGSLISTA Tvingad tunneltrafik](media/tic-diagram-c.png)

All trafik som lämnar det virtuella nätverket måste dirigera via den lokala anslutningen, så att all trafik passerar D/A ärende. Du kan skapa anpassade vägar genom att skapa användardefinierade vägar eller genom att utbyta vägar för Border Gateway Protocol (BGP) mellan din lokala nätverksgateway och en Azure VPN-gateway. Mer information om användardefinierade vägar finns i [trafikdirigering i virtuella nätverk: Användardefinierade vägar](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined). Mer information om BGP finns i [trafikdirigering i virtuella nätverk: Border Gateway Protocol](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol).

#### <a name="add-user-defined-routes"></a>Lägg till användardefinierade vägar

Om du använder en routningsbaserad virtuell nätverksgateway kan tvinga du tunneltrafik i Azure. Lägg till en användardefinierad väg (UDR) som anger 0.0.0.0/0 trafik för dirigering till en **nästa hopp** för din virtuella nätverksgateway. Azure prioriterar användardefinierade vägar via systemdefinierade vägar. Alla icke-virtuell nätverkstrafik skickas till din virtuella nätverksgateway, som sedan kan vidarebefordra trafiken till den lokala. När du har definierat den användardefinierade vägen kan du associera vägen med befintliga undernät eller nya undernät i alla virtuella nätverk i Azure-miljön.

![användardefinierade vägar och ärende](media/tic-diagram-d.png)

#### <a name="use-the-border-gateway-protocol"></a>Använd Border Gateway Protocol

Om du använder ExpressRoute eller en BGP-aktiverad virtuell nätverksgateway är BGP den rekommenderade metoden för att annonsera vägar. För en BGP annonserade väg med 0.0.0.0/0 Kontrollera ExpressRoute- och BGP-anpassade virtuella nätverksgatewayer standardvägen gäller för alla undernät i ditt virtuella nätverk.

### <a name="azure-iaas-tic-compliance-auditing"></a>Azure IaaS-ärende efterlevnad: Granskning

Azure erbjuder flera olika sätt att granska ärende efterlevnad.

#### <a name="view-effective-routes"></a>Visa effektiva vägar

Bekräfta att din standardvägen sprids genom att följa den _gällande routningar_ för en viss virtuell dator, ett specifikt nätverkskort eller en användardefinierad routningstabell i [Azure-portalen](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal#diagnose-using-azure-portal) eller i [ Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-powershell#diagnose-using-powershell). Den **gällande routningar** visa relevanta användardefinierade vägar som BGP annonseras vägar och systemvägar som tillämpas på relevanta entiteten, enligt följande bild:

![Gällande routningar](media/tic-screen-1.png)

> [!NOTE]
> Du kan inte visa de effektiva vägarna för ett nätverkskort om nätverkskortet som är associerad med en aktiv virtuell dator.

#### <a name="use-azure-network-watcher"></a>Använd Azure Network Watcher

Azure Network Watcher erbjuder flera verktyg för att granska RANKNINGSLISTA efterlevnad. Mer information finns i [översikten om Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).

##### <a name="capture-network-security-group-flow-logs"></a>Samla in loggar för nätverkssäkerhetsgrupper flöde 

Använda Network Watcher för att avbilda flödesloggar för nätverket som anger de metadata som omger IP-trafik. Flödesloggar för nätverk innehåller källans och målets adresser av mål och andra data. Du kan använda dessa data med loggar från den virtuella nätverksgatewayen, lokala edge-enheter eller ärende, att övervaka att all trafik dirigerar lokalt. 

## <a name="azure-platform-as-a-service-offerings"></a>Azure-plattformen som en Tjänsterbjudanden

Azure PaaS-tjänster, till exempel Azure Storage, är tillgängliga via en URL som kan nås av internet. Vem som helst med godkända autentiseringsuppgifter kan komma åt resurs, till exempel ett lagringskonto varifrån som helst utan att gå igenom ett ärende. Därför ingå många myndigheter felaktigt att Azure PaaS-tjänster inte är kompatibla med principer för ärende. Många Azure PaaS-tjänster kan vara kompatibel med RANKNINGSLISTA princip. En tjänst är kompatibel om arkitekturen är samma som ärende-kompatibla IaaS-miljön ([enligt beskrivningen ovan](https://docs.microsoft.com/azure/security/compliance/compliance-tic#azure-infrastructure-as-a-service-offerings)) och tjänsten är ansluten till ett Azure-nätverk.

När Azure PaaS-tjänster är integrerade med ett virtuellt nätverk, är tjänsten privat kan nås från det virtuella nätverket. Du kan tillämpa anpassad routning för 0.0.0.0/0 via användardefinierade vägar eller BGP. Anpassad routning säkerställer att alla internet-bunden trafik dirigerar lokalt för att bläddra i ärende. Integrera Azure-tjänster i virtuella nätverk med hjälp av följande mönster:

- **Distribuera en dedikerad instans av en tjänst**: Ett ökande antal PaaS-tjänster kan distribueras som dedikerade instanser med virtuell nätverksansluten slutpunkter. Du kan distribuera en App Service Environment för PowerApps i ”isolerad” läge för att tillåta nätverksslutpunkten att vara begränsad till ett virtuellt nätverk. App Service Environment kan sedan vara värd för många Azure PaaS-tjänster, till exempel Azure Web Apps, Azure API Management och Azure Functions.
- **Använda virtuella nätverksslutpunkter**: Ett ökande antal PaaS-tjänster tillåta att flytta sina slutpunkt till virtuellt privat IP-adress i stället för en offentlig adress.

Tjänster som stöder distribution av dedikerade instanser till ett virtuellt nätverk eller användning av Tjänsteslutpunkter, från och med maj 2018, visas i följande tabeller.

> [!Note]
> Tillgänglighetsstatus motsvarar de Azure-tjänster som är kommersiellt tillgängligt. Tillgänglighetsstatus för Azure-tjänster i Azure Government är väntande.

### <a name="support-for-service-endpoints"></a>Stöd för tjänstslutpunkter

|Tjänst                        |Tillgänglighet      |
|-------------------------------|------------------|
|Azure Key Vault                | Privat förhandsgranskning  |
|Azure Cosmos DB                | Privat förhandsgranskning  |
|Identitetstjänster              | Privat förhandsgranskning  |
|Azure Data Lake                | Privat förhandsgranskning  |
|Azure Database for PostgreSQL  | Privat förhandsgranskning  |
|Azure Database for MySQL       | Privat förhandsgranskning  |
|Azure SQL Data Warehouse       | Offentlig förhandsversion   |
|Azure SQL Database             | Allmän tillgänglighet (GA) |
|Azure Storage                  | Allmän tillgänglighet (GA)               |

### <a name="support-for-virtual-network-injection"></a>Stöd för virtuellt nätverk-inmatning

|Tjänst                               |Tillgänglighet      |
|--------------------------------------|------------------|
|Hanterad Azure SQL Database-instans   | Offentlig förhandsversion   |
|Azure Kubernetes Service (AKS)        | Offentlig förhandsversion   |
|Azure Service Fabric                  | Allmän tillgänglighet (GA)               |
|Azure API Management                  | Allmän tillgänglighet (GA)               |
|Azure Active Directory                | Allmän tillgänglighet (GA)               |
|Azure Batch                           | Allmän tillgänglighet (GA)               |
|App Service Environment               | Allmän tillgänglighet (GA)               |
|Azure Cache for Redis                     | Allmän tillgänglighet (GA)               |
|Azure HDInsight                       | Allmän tillgänglighet (GA)               |
|Skaluppsättning för virtuella datorer             | Allmän tillgänglighet (GA)               |
|Azure Cloud Services                  | Allmän tillgänglighet (GA)               |


### <a name="virtual-network-integration-details"></a>Information om integrering av virtuellt nätverk

Följande diagram visar den allmänna nätverksflödet för åtkomst till PaaS-tjänster. Åtkomst visas från virtuellt nätverk-inmatning och vnet-tjänstetunnlar. Läs mer om network service-gatewayer, virtuella nätverk och tjänsttaggar [nätverk och säkerhetsgrupper för program: Tjänsttaggar](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

![PaaS-anslutningsalternativ för ärende](media/tic-diagram-e.png)

1. En privat anslutning görs till Azure med hjälp av ExpressRoute. ExpressRoute privat peering Tvingad tunneltrafik används för att tvinga alla virtuella nätverkstrafiken via ExpressRoute och tillbaka till den lokala. Microsoft-Peering krävs inte.
2. Azure VPN-Gateway, när de används tillsammans med ExpressRoute och Microsoft-Peering kan täcka över IPSec-kryptering för slutpunkt till slutpunkt mellan kundens virtuella nätverk och lokala edge. 
3. Nätverksanslutningen till kundens virtuella nätverk kontrolleras med hjälp av NSG: er som gör att kunderna kan bevilja/neka baserat på IP, port och protokoll.
4. Kundens virtuella nätverk utökar till PaaS-tjänst genom att skapa en tjänstslutpunkt för kundens-tjänsten.
5. PaaS-tjänsteslutpunkt är skyddad till **standard neka alla** och att endast tillåta åtkomst från angivna undernät i kundens virtuella nätverk. Standard nekar också innehåller anslutningar som kommer från internet.
6. Andra Azure-tjänster som behöver åtkomst till resurser i kundens virtuella nätverk måste vara:  
   - Distribuera direkt till det virtuella nätverket.
   - Selektivt tillåts, baserat på vägledningen från respektive Azure-tjänsten.

#### <a name="option-a-deploy-a-dedicated-instance-of-a-service-virtual-network-injection"></a>Alternativ A: Distribuera en dedikerad instans av en tjänst (virtuellt nätverk-inmatning)

Inmatning av virtuellt nätverk gör att kunderna kan distribuera selektivt dedikerade instanser av en viss Azure-tjänst, till exempel HDInsight, till sina egna virtuella nätverk. Instanser av tjänsten distribueras i ett dedikerat undernät i kundens virtuella nätverk. Inmatning av virtuellt nätverk ger åtkomst till tjänstresurser via icke-internet-dirigerbara adresser. Lokala instanser använder ExpressRoute eller VPN för plats-till-plats för direkt åtkomst till instanser av tjänsten via virtuella nätverkets adressutrymme, i stället för att öppna en brandvägg offentliga internet-adressutrymme. Du kan använda men samma strategier för efterlevnad för IaaS-ärende när en dedikerad instans är ansluten till en slutpunkt. Standardroutning säkerställer internet-bunden trafik dirigeras till en virtuell nätverksgateway som är bunden till en lokal. Du kan ytterligare styra inkommande och utgående åtkomst via NSG: er för det angivna undernätet.

![Översikt över Virtual network-inmatning](media/tic-diagram-f.png)

#### <a name="option-b-use-virtual-network-service-endpoints-service-tunnel"></a>Alternativ B: Använda tjänstslutpunkter i virtuella nätverk (service-tunnel)

Ett ökande antal Azure-tjänster som multitenant erbjuder ”Tjänsteslutpunkter”. Tjänstslutpunkter är en alternativ metod för att integrera till Azure-nätverk. Tjänstslutpunkter i virtuella nätverk kan du utöka din virtuella IP-adressutrymme och identiteten för ditt virtuella nätverk till tjänsten via en direktanslutning. Trafik från det virtuella nätverket till Azure-tjänsten förblir alltid inom Azure-stamnätverket. 

När du har aktiverat en tjänstslutpunkt för en tjänst kan du använda principer som exponeras av tjänsten för att begränsa anslutningar för tjänsten till det virtuella nätverket. Åtkomstkontroller tillämpas i plattformen av Azure-tjänsten. Åtkomst till en låst resurs beviljas endast om förfrågan kommer från det tillåtna virtuella nätverk eller undernät, eller från två IP-adresser som används för att identifiera din lokala-trafik om du använder ExpressRoute. Använd den här metoden för att effektivt förhindra inkommande/utgående trafik från att lämna direkt PaaS-tjänst.

![Översikt över tjänstslutpunkter](media/tic-diagram-g.png)

## <a name="cloud-native-tools-for-network-situational-awareness"></a>Molnbaserade verktyg för nätverk situationsmedvetenhet

Azure tillhandahåller molnbaserade verktyg för att säkerställa att du har situationsmedvetenhet som krävs för att förstå trafikflöde i nätverket. Verktygen inte behövs för kompatibilitet med RANKNINGSLISTA princip. Verktygen kan avsevärt förbättra funktioner för säkerhet.

### <a name="azure-policy"></a>Azure Policy

[Azure Policy](../../governance/policy/overview.md) är en Azure-tjänst som ger din organisation bättre möjlighet att granska och genomdriva efterlevnadsinitiativ. Kunder kan planera och testa sina Azure Policy-regler nu om du vill säkerställa framtida kompatibilitet för ärende.

Azure Policy är avsett för prenumerationsnivån. Tjänsten ger ett centraliserat gränssnitt där du kan utföra efterlevnad aktiviteter, inklusive:
- Hantera initiativ
- Konfigurera principdefinitioner
- Granska efterlevnad
- Framtvinga efterlevnad
- Hantera undantag

Tillsammans med många inbyggda definitioner, kan administratörer definiera sina egna anpassade definitioner genom att använda enkla JSON-mallar. Microsoft rekommenderar prioriteringen av granskning över tvingande, där det är möjligt.

Följande exempel principer kan användas för ärende kompatibilitet scenarier:

|Princip  |Exempelscenario  |Mall  |
|---------|---------|---------|
|Framtvinga en användardefinierad routningstabell. | Se till att standardvägen på alla virtuella nätverk som pekar på en godkänd virtuell nätverksgateway för routning till den lokala.    | Kom igång med den här [mallen](../../governance/policy/samples/no-user-defined-route-table.md). |
|Granska om Network Watcher har inte aktiverats för en region.  | Kontrollera att Network Watcher är aktiverat för alla regioner.  | Kom igång med den här [mallen](../../governance/policy/samples/network-watcher-not-enabled.md). |
|NSG x i varje undernät.  | Se till att en NSG (eller en uppsättning godkända NSG: er) med internet-trafiken blockeras tillämpas på alla undernät i varje virtuellt nätverk. | Kom igång med den här [mallen](../../governance/policy/samples/nsg-on-subnet.md). |
|NSG x på varje nätverkskort. | Se till att en Nätverkssäkerhetsgrupp med internet-trafiken blockeras tillämpas på alla nätverkskort på alla virtuella datorer. | Kom igång med den här [mallen](../../governance/policy/samples/nsg-on-nic.md). |
|Använda ett godkända virtuellt nätverk för virtuella datorers nätverksgränssnitt.  | Se till att alla nätverkskort i ett godkända-nätverk. | Kom igång med den här [mallen](../../governance/policy/samples/use-approved-vnet-vm-nics.md). |
|Tillåtna platser. | Se till att alla resurser som distribueras till regioner med kompatibla virtuella nätverk och Network Watcher-konfigurationen.  | Kom igång med den här [mallen](../../governance/policy/samples/allowed-locations.md). |
|Otillåtna resurstyper, till exempel **PublicIPs**. | Förhindra distribution av resurstyper som inte har en plan för efterlevnad. Använd den här principen för att förhindra distribution av offentliga IP-adress-resurser. NSG-regler kan användas för att effektivt blockera inkommande Internettrafik, minskar hindrar användning av offentliga IP-adresser ytterligare risken för angrepp.   | Kom igång med den här [mallen](../../governance/policy/samples/not-allowed-resource-types.md).  |

### <a name="network-watcher-traffic-analytics"></a>Network Watcher-trafikanalys

Network Watcher [traffic analytics](https://azure.microsoft.com/blog/traffic-analytics-in-preview/) använda flow loggdata och andra loggar att ge en översikt av nätverkstrafik. Informationen är användbar för granskning ärende efterlevnad och för att identifiera problemområden. Du kan använda instrumentpanelen på hög nivå för att snabbt skärmen de virtuella datorer som kommunicerar med internet och få en fokuserade lista för ärende routning.

![Trafikanalys](media/tic-traffic-analytics-1.png)

Använd den **Geomappningen** att snabbt identifiera sannolika fysiska mål för internet-trafiken för dina virtuella datorer. Du kan identifiera och testa misstänkta platser eller ändringar i mönster:

![GEO-mappning](media/tic-traffic-analytics-2.png)

Använd den **virtuella nätverk-topologin** för att snabbt undersöka prissättningen befintliga virtuella nätverk:

![Nätverket topologisk karta](media/tic-traffic-analytics-3.png)

### <a name="network-watcher-next-hop-tests"></a>Network Watcher nästa hopp-tester

Nätverk i regioner som övervakas av Network Watcher kan utföra tester för nästa hopp. Du kan ange en källa och mål för ett exempel network-flöde för Network Watcher att lösa nexthop-mål i Azure-portalen. Kör det här testet mot virtuella datorer och exempel Internetadresser så nexthop-mål är de förväntade virtuella nätverksgatewayen.

![Nästa hopp-tester](media/tic-network-watcher.png)

## <a name="conclusions"></a>Slutsatser

Du kan enkelt konfigurera åtkomst för Microsoft Azure, Office 365 och Dynamics 365 att bidra till att efterleva RANKNINGSLISTA 2.0 bilaga H vägledning som skriftligt och definierade maj 2018. Microsoft kan identifiera att ärende riktlinjerna kan komma att ändras. Microsoft lycka att hjälpa kunder att uppfylla riktlinjerna i god tid när nya råd är tillgängliga.

## <a name="appendix-trusted-internet-connections-patterns-for-common-workloads"></a>Tillägg: Betrodda Internetanslutningar mönster för vanliga arbetsbelastningar

| Kategori | Arbetsbelastning | IaaS | Dedikerad PaaS / inmatning i virtuella nätverk  | Tjänstslutpunkter  |
|---------|---------|---------|---------|--------|
| Compute | Virtuella Azure Linux-datorer | Ja | | |
| Compute | Virtuella Azure Windows-datorer | Ja | | |
| Compute | Skalningsuppsättningar för virtuella datorer | Ja | | |
| Compute | Azure Functions | | App Service Environment | |
| Webb och mobil | Interna webbprogram | | App Service Environment| |
| Webb och mobil | Interna mobila program | | App Service Environment | |
| Webb och mobil | API-program | | App Service Environment | |
| Containrar | Azure Container Service | | | Ja |
| Containrar | Azure Kubernetes Service (AKS) \* | | | Ja |
| Databas | Azure SQL Database | | Azure SQL Database Managed Instance \* | Azure SQL |
| Databas | Azure Database for MySQL | | | Ja |
| Databas | Azure Database for PostgreSQL | | | Ja |
| Databas | Azure SQL Data Warehouse | | | Ja |
| Databas | Azure Cosmos DB | | | Ja |
| Databas | Azure Cache for Redis | | Ja | |
| Storage | Azure Blob Storage | Ja | | |
| Storage | Azure Files | Ja | | |
| Storage | Azure-kölagring | Ja | | |
| Storage | Azure Table Storage | Ja | | |
| Storage | Azure Disk storage | Ja | | |

\* Offentlig förhandsversion i Azure Government, maj 2018.
