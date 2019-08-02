---
title: Styra utgående trafik i Azure Australien
description: Viktiga element för att kontrol lera utgående trafik i Azure för att uppfylla de australiska myndighets kraven för säkra Internet-gatewayer
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: grgale
ms.openlocfilehash: 7922846d161b7a0cbda32101b6bbb40a1741b323
ms.sourcegitcommit: 15f7b641a67f3d6cf4fb4b4c11eaee18cf335923
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602080"
---
# <a name="controlling-egress-traffic-in-azure-australia"></a>Styra utgående trafik i Azure Australien

En grundläggande komponent i att skydda IKT-system är att kontrol lera nätverks trafiken. Att begränsa kommunikationen till enbart den trafik som krävs för att ett system ska fungera minskar risken för problem. Synlighet och kontroll över de externa system som dina program och tjänster kommunicerar med hjälper till att identifiera komprometterade system, och försök att utföra eller lyckas med data exfiltrering. Den här artikeln innehåller information om hur utgående nätverks trafik fungerar i Azure och ger rekommendationer för att implementera nätverks säkerhets kontroller för ett Internet-anslutet system som överensstämmer med det australiska cyberhot Security Center (ACSC) Konsument vägledning och avsikten med ACSC information Security manual (ISM).

## <a name="requirements"></a>Krav

De övergripande säkerhets kraven för samväldet samordnings system definieras i ISM-systemet. För att under lätta samverkande entiteter vid implementering av nätverks _säkerhet har ACSC publicerat ACSC Protect: Att implementera nätverks segmentering och särskiljande_, och för att hjälpa till med att skydda system i moln miljöer, har ACSC publicerat _Cloud Computing Security för klienter_.

ACSC-dokumenten innehåller en översikt över hur du implementerar nätverks säkerhet och kontrollerar trafiken och ger praktiska rekommendationer för nätverks design och-konfiguration.

Följande viktiga krav för att kontrol lera utgående trafik i Azure har identifierats i ACSC-dokumenten.

Beskrivning|Source
--------------- |------------------
**Implementera nätverks segmentering och uppdelning**, till exempel, använda en arkitektur på n-nivå, med värdbaserade brand väggar och nätverks åtkomst kontroller för att begränsa inkommande och utgående nätverks anslutning till endast de portar och protokoll som krävs.| [Molnbaserad data behandling för klienter](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)
**Implementera tillräcklig hög bandbredd, låg latens, tillförlitlig nätverks anslutning** mellan klient organisationen (inklusive klientens fjärran vändare) och moln tjänsten för att uppfylla klientens tillgänglighets krav  | [ACSC skydda: Implementera nätverks segmentering och åtskillnad](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Använd teknik på mer än bara nätverks nivån**. Varje värd och nätverk bör vara segmenterade och åtskiljda, där det är möjligt, på den lägsta nivån som kan hanteras i praktiken. I de flesta fall gäller detta från data länk skiktet upp till och inklusive program lagret. i känsliga miljöer kan den fysiska isoleringen emellertid vara lämplig. Värdbaserade och nätverks omfattande åtgärder bör distribueras på ett kompletterande sätt och övervakas centralt. Du behöver bara implementera en brand vägg eller säkerhetsinstallation eftersom det enda säkerhets måttet inte räcker. |[ACSC skydda: Implementera nätverks segmentering och åtskillnad](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Använd principerna för minsta behörighet och behöver gång till för att gång till veta**. Om en värd, tjänst eller nätverk inte behöver kommunicera med en annan värd, tjänst eller nätverk bör den inte vara tillåten. Om en värd, tjänst eller nätverk bara behöver kommunicera med en annan värd, tjänst eller nätverk på en speciell port eller ett protokoll, bör den begränsas till endast dessa portar och protokoll. Genom att använda dessa principer i ett nätverk kompletterar du om användar behörigheterna minimeras och den övergripande säkerhets position i miljön ökar avsevärt. |[ACSC skydda: Implementera nätverks segmentering och åtskillnad](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Separera värdar och nätverk utifrån deras känslighet eller kritiskhet för affärs åtgärder**. Detta kan omfatta användning av annan maskin vara eller olika plattformar beroende på olika säkerhets klassificeringar, säkerhets domäner eller tillgänglighets-/integritets krav för vissa värdar eller nätverk. I synnerhet separata hanterings nätverk och Överväg att fysiskt isolera out-of-band-hanterings nätverk för känsliga miljöer. |[ACSC skydda: Implementera nätverks segmentering och åtskillnad](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Identifiera, autentisera och auktorisera åtkomst av alla entiteter till alla andra entiteter**. Alla användare, värdar och tjänster bör ha åtkomst till alla andra användare, värdar och tjänster som är begränsade till dem som krävs för att utföra sina angivna uppgifter eller funktioner. Alla äldre eller lokala tjänster som kringgår eller nedgraderar styrkan hos identifierings-, autentiserings-och godkännande tjänster bör inaktive ras när det är möjligt och ha en välövervakad användning. |[ACSC skydda: Implementera nätverks segmentering och åtskillnad](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Implementera Tillåt lista över nätverks trafik i stället för neka-lista**. Tillåt endast åtkomst för känd bra nätverks trafik (trafik som identifieras, autentiseras och auktoriseras), i stället för att neka åtkomst till känd felaktig nätverks trafik (till exempel blockera en viss adress eller tjänst). Tillåt listor resulterar i en överlägsen säkerhets princip för att neka listor, och förbättra kapaciteten avsevärt för att identifiera och utvärdera potentiella nätverks intrång. |[ACSC skydda: Implementera nätverks segmentering och åtskillnad](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
Genom **att definiera en lista över tillåtna webbplatser och blockera alla webbplatser** som inte har listats tar du bort en av de vanligaste data leverans-och exfiltrering-teknikerna som används av en angripare. Om användarna har ett legitimt krav på åtkomst till flera webbplatser eller en snabb ändring av listan över webbplatser. Du bör tänka på kostnaderna för en sådan implementering. Även en lista med relativt tillåtelse ger bättre säkerhet än att förlita sig på neka listor eller inga begränsningar alls, samtidigt som implementerings kostnaderna minskar. Ett exempel på en Tillåt-lista kan möjliggöra hela den australiska under domänen, det vill säga *. au, eller tillåta de översta 1 000-platserna från Alexa-plats rangordningen (efter filtrering av dynamiska Domain Name System (DDNS) domäner och andra olämpliga domäner).| [Australisk myndighets informations säkerhets hand bok (ISM)](https://www.cyber.gov.au/ism)
|

Den här artikeln innehåller information och rekommendationer om hur nätverks trafik som lämnar din Azure-miljö kontrol leras. Den täcker system som distribueras i Azure med både IaaS (Infrastructure as a Service) och PaaS (Platform as a Service).

Trafiken inriktar sig på [trafik](gateway-ingress-traffic.md) artikeln tar itu med nätverks trafik som går in i din Azure-miljö och tillhör den här artikeln för fullständig nätverks kontroll.

## <a name="architecture"></a>Arkitektur

När du utformar och implementerar nätverks säkerhet på rätt sätt måste du först förstå hur utgående nätverks trafik fungerar i Azure över både IaaS och PaaS. Det här avsnittet innehåller en översikt över hur utgående trafik som genereras av en resurs som finns i Azure bearbetas och vilka säkerhets kontroller som är tillgängliga för att begränsa och kontrol lera trafiken.

### <a name="architecture-components"></a>Arkitektur komponenter

Det arkitektur diagram som visas här beskriver de möjliga Sök vägarna som nätverks trafiken kan utföra när du avslutar ett system som distribueras till ett undernät i ett virtuellt nätverk. Trafik i ett virtuellt nätverk hanteras och regleras på en under näts nivå, med regler för Routning och säkerhet som tillämpas på de resurser som finns i. Komponenterna som rör utgående trafik är indelade i system, effektiva vägar, nästa hopp typer, säkerhets kontroller och PaaS utgående.

![Arkitektur](media/egress-traffic.png)

### <a name="systems"></a>Flerprocessorsystem

System är Azure-resurser och relaterade komponenter som genererar utgående trafik i ett IP-undernät som ingår i ett virtuellt nätverk.

| Komponent | Beskrivning |
| --- | ---|
|Virtual Network (VNet) | Ett VNet är en grundläggande resurs i Azure som tillhandahåller en plattform och en gränser för att distribuera resurser och aktivera kommunikation. VNet finns i en Azure-region och definierar IP-adressutrymmet och nätverks gränser för inbyggda VNet-resurser som Virtual Machines.|
|Subnet | Ett undernät är ett IP-adressintervall som skapas inom ett VNet. Flera undernät kan skapas i ett VNet för nätverks segmentering.|
|Nätverksgränssnitt| Ett nätverks gränssnitt är en resurs som finns i Azure. Den är kopplad till en virtuell dator och har tilldelats en privat, icke-Internet-dirigerad IP-adress från det undernät som den är associerad med. Den här IP-adressen är dynamiskt eller statiskt tilldelad via Azure Resource Manager.|
|Offentliga IP-adresser| En offentlig IP-adress är en resurs som reserverar en av Microsofts ägda offentliga IP-adresser från Internet från det angivna området för användning i det virtuella nätverket. Den kan kopplas till ett särskilt nätverks gränssnitt eller en PaaS-resurs, vilket gör att resursen kan kommunicera med Internet, ExpressRoute och till andra PaaS-system.|
|

### <a name="routes"></a>Vägar

Sökvägen som utgående trafik tar är beroende av de effektiva vägarna för den resursen, vilket är den resultat uppsättning som bestäms av kombinationen av vägar från alla möjliga källor och programmet för Azure-routningsmetod.

| Komponent | Beskrivning |
|--- | ---|
|Systemvägar| Azure skapar automatiskt systemvägar och tilldelar vägarna till varje undernät i ett virtuellt nätverk. Det går inte att skapa eller ta bort system vägar, men vissa kan åsidosättas med anpassade vägar. Azure skapar standard system vägar för varje undernät och lägger till ytterligare valfria standard vägar till vissa undernät, eller varje undernät, när vissa Azure-funktioner utnyttjas.|
|Serviceslutpunkter| Tjänst slut punkter tillhandahåller en direkt, privat utgående anslutning från ett undernät till en speciell PaaS-funktion. Tjänst slut punkter, som endast är tillgängliga för en delmängd PaaS-funktioner, ger bättre prestanda och säkerhet för resurser i ett VNet-åtkomst till PaaS.|
|Routningstabeller| En routningstabell är en resurs i Azure som kan skapas för att ange användardefinierade vägar (UDR) som kan användas för att komplettera eller åsidosätta system vägar eller vägar i Läs-Border Gateway Protocol. Varje UDR anger ett nätverk, en nätverks mask och ett nästa hopp. En routningstabell kan kopplas till ett undernät och samma routningstabell kan associeras med flera undernät, men ett undernät kan bara ha noll eller en routningstabell.|
|Border Gateway Protocol (BGP)| BGP är ett inter-autonomt system Dirigerings protokoll. Ett autonomt system är ett nätverk eller en grupp med nätverk under en gemensam administration och med gemensamma principer för routning. BGP används för att utbyta routningsinformation mellan autonoma system. BGP kan integreras i virtuella nätverk via virtuella nätverks-gatewayer.|
|

### <a name="next-hop-types-defined"></a>Nästa hopp typer som definieras

Varje väg i Azure inkluderar nätverks intervallet och den tillhör ande nät masken och nästa hopp, vilket avgör hur trafiken bearbetas.

Nästa hopptyp | Beskrivning
---- | ----
**Virtual Network** | Dirigerar trafik mellan adress intervall inom det virtuella nätverkets adress utrymme. Azure skapar en väg med ett adressprefix som motsvarar varje adressintervall som definierats inom adressutrymmet för ett virtuellt nätverk. Om det finns flera definierade adress intervall för det virtuella nätverkets adress utrymme skapar Azure en enskild väg för varje adress intervall. Azure dirigerar automatiskt trafik mellan undernät i ett VNet med de vägar som skapats för varje adress intervall.
**VNet-peering** | När en peering för virtuellt nätverk skapas mellan två virtuella nätverk läggs en väg till för varje adress intervall för varje virtuellt nätverk till det virtuella nätverk som det är peer-kopplat till. Trafiken dirigeras mellan peer-kopplat virtuella nätverk på samma sätt som undernät i ett virtuellt nätverk.
**Virtuell nätverksgateway** | En eller flera vägar med en virtuell nätverksgateway som anges som nästa hopp typ läggs till när en virtuell nätverksgateway läggs till i ett virtuellt nätverk. De vägar som ingår är de som kon figurer ATS i den lokala nätverks-Gateway-resursen och eventuella vägar som lär sig via BGP.
**Virtuell installation** | En virtuell installation kör vanligt vis ett nätverks program, till exempel en brand vägg. Den virtuella installationen gör att ytterligare bearbetning av trafiken kan ske, till exempel filtrering, inspektion eller adress översättning. Varje väg med hopp typen för den virtuella enheten måste också ange en IP-adress för nästa hopp.
**VirtualNetworkServiceEndpoint** | De offentliga IP-adresserna för en speciell tjänst läggs till som vägar till ett undernät med nästa hopp i VirtualNetworkServiceEndpoint när en tjänst slut punkt aktive ras. Tjänst slut punkter är aktiverade för enskilda tjänster i enskilda undernät i ett virtuellt nätverk. Azure-tjänsters offentliga IP-adresser ändras regelbundet. Azure hanterar adresserna i routningstabellen automatiskt när adresserna ändras.
**E** | Trafik med nästa hopp till Internet avslutar det virtuella nätverket och översätts automatiskt till en offentlig IP-adress, antingen från en dynamisk pool som är tillgänglig i den associerade Azure-regionen, eller genom att använda en offentlig IP-adress som kon figurer ATS för resursen. Om mål adressen är för en av Azures tjänster dirigeras trafiken direkt till tjänsten via Azures stamnät nätverk, i stället för att dirigera trafiken till Internet. Trafik mellan Azure-tjänster sker inte via Internet, oavsett vilken Azure-region det virtuella nätverket finns i eller vilken Azure-region en instans av Azure-tjänsten har distribuerats i.
**Alternativet** | Trafik med ett nästa hopp från ingen bryts. Azure skapar system standard vägar för reserverade adressprefix med ingen som nästa hopp typ. Vägar med nästa hopp kan också läggas till med hjälp av routningstabeller för att förhindra att trafik dirigeras till vissa nätverk.
|

### <a name="security-controls"></a>Säkerhets kontroller

Kontroll | Beskrivning
----- | -----
**Nätverks säkerhets grupper (NSG: er)** | NSG: er styr trafik till och från virtuella nätverks resurser i Azure. NSG: er tillämpar regler för de trafikflöde som tillåts eller nekas, vilket omfattar trafik i Azure och mellan Azure och externa nätverk, till exempel lokalt eller Internet. NSG: er tillämpas på undernät i ett virtuellt nätverk eller enskilda nätverks gränssnitt.
**Azure-brandvägg** | Azure Firewall är en hanterad, molnbaserad nätverks säkerhets tjänst som skyddar Azures virtuella nätverks resurser. Det är en helt tillståndskänslig brandvägg som en tjänst med inbyggd hög tillgänglighet och obegränsad molnskalbarhet. Azure-brandväggen kan konfigureras med traditionella regler för nätverks filtrering baserat på IP-adresser, protokoll och portar, men stöder även filtrering baserat på fullständigt kvalificerade domän namn (FQDN), service märken och inbyggd Hot information.
**Virtuell nätverks installation (NVA)** | Virtuella nätverks installationer är virtuella dator medier som kan ge nätverk, säkerhet och andra funktioner till Azure. NVA stöder nätverks funktioner och tjänster i form av virtuella datorer i virtuella nätverk och distributioner. NVA kan användas för att åtgärda särskilda krav, integrera med hanterings-och drift verktyg, eller för att tillhandahålla konsekvens med befintliga produkter. Azure har stöd för en omfattande lista med virtuell nätverksutrustning från tredje part, bland annat brandväggar för webbaserade program (WAF – Web Application Firewall), brandväggar, gatewayer/routrar, ADC (Application Delivery Controller) och WAN-optimerare.
**Principer för tjänst slut punkt (för hands version)** | Med tjänst slut punkts principer för virtuella nätverk kan du filtrera virtuell nätverks trafik till Azure-tjänster, så att endast vissa Azure-tjänst resurser tillåts, över tjänst slut punkter. Slutpunktsprinciper ger detaljerad åtkomstkontroll för trafik i virtuella nätverk till Azure-tjänster.
**Azure Policy** | Azure Policy är en tjänst i Azure för att skapa, tilldela och hantera principer. Dessa principer använder regler för att styra vilka typer av resurser som kan distribueras och konfigurationen av dessa resurser. Principer kan användas för att genomdriva efterlevnad genom att förhindra att resurser distribueras om de inte uppfyller kraven eller kan användas för övervakning för att rapportera om kompatibilitetsstatus.
|

### <a name="paas-egress"></a>Utgående PaaS

Majoriteten av PaaS-resurserna genererar inte utgående trafik, utan svarar antingen på inkommande begär Anden (till exempel Application Gateway, lagring, SQL Database osv.) eller relä data från andra resurser (till exempel Service Bus och Azure Relay). Nätverks kommunikationen flödar mellan PaaS-resurser som App Services till lagring eller SQL-databaser styrs och ingår i Azure och skyddas via identitets-och andra resurs konfigurations kontroller i stället för nätverks segmentering eller uppdelning.

PaaS-resurser som distribueras till ett virtuellt nätverk får dedikerade IP-adresser och omfattas av alla vägvals kontroller och NSG: er på samma sätt som andra resurser i det virtuella nätverket. PaaS-resurser som inte finns inom ett virtuellt nätverk använder en pool med IP-adresser som delas mellan alla instanser av resursen, som antingen publiceras via Microsoft-dokumentationen eller kan fastställas genom Azure Resource Manager.

## <a name="general-guidance"></a>Allmänna riktlinjer

För att utforma och skapa säkra lösningar i Azure är det viktigt att förstå och kontrol lera nätverks trafiken så att endast identifierad och auktoriserad kommunikation kan ske. Syftet med den här vägledningen och den specifika komponent vägledningen i senare avsnitt är att beskriva de verktyg och tjänster som kan användas för att tillämpa de principer som beskrivs i [ACSC-skydd: Implementera nätverks segmentering och åtskillnad](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm) mellan Azure-arbetsbelastningar. Här finns information om hur du skapar en virtuell arkitektur för att skydda resurser när det inte går att tillämpa samma traditionella fysiska och nätverks kontroller som är möjliga i en lokal miljö.

### <a name="guidance"></a>Riktlinjer

* Begränsa antalet utgående punkter för virtuella nätverk
* Åsidosätt systemets standard väg för alla undernät som inte behöver direkt utgående kommunikation till Internet
* Utforma och implementera en komplett nätverks arkitektur för att identifiera och kontrol lera alla ingående och utgående punkter i Azure-resurser
* Överväg att använda en nav-och Ekrars nätverks design för virtuella nätverk som beskrivs i Microsoft Virtual Data Center (VDC)-dokumentationen
* Använda produkter med inbyggda säkerhetsfunktioner för utgående anslutningar till Internet (till exempel Azure-brandvägg, nätverkets virtuella apparater eller Webbproxyservrar)
* Använd identitets kontroller som rollbaserad åtkomst, villkorlig åtkomst och Multi-Factor Authentication (MFA) för att begränsa nätverks konfigurations privilegier
* Implementera lås för att förhindra ändringar eller borttagning av viktiga delar av nätverks konfigurationen
* Distribuera PaaS i en VNet-integrerad konfiguration för ökad uppdelning och kontroll
* Implementera ExpressRoute för anslutning med lokala nätverk
* Implementera VPN för integrering med externa nätverk
* Använda Azure Policy för att begränsa regionerna och resurserna till enbart de som är nödvändiga för systemfunktionen
* Använd Azure Policy för att framtvinga säkerhets konfiguration av bas linjen för resurser
* Utnyttja Network Watcher och Azure Monitor för loggning, granskning och synlighet för nätverks trafik i Azure

### <a name="resources"></a>Resurser

Objekt | Länka
-----------| ---------
_Australiska lagar och principer för regelefterlevnad, inklusive konsument vägledning_ | [https://aka.ms/au-irap](https://aka.ms/au-irap)
_Azure Virtual Data Center_ | [https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)
_ACSC nätverks segmentering_ | [https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
_ACSC Cloud Security för klienter_ | [https://acsc.gov.au/publications/protect/cloud-security-tenants.htm](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)
_ACSC information säkerhets hand bok_ | [https://acsc.gov.au/infosec/ism/index.htm](https://acsc.gov.au/infosec/ism/index.htm)
|

## <a name="component-guidance"></a>Vägledning för komponenter

Det här avsnittet innehåller ytterligare information om de enskilda komponenter som är relevanta för utgående trafik för system som distribueras i Azure. I varje avsnitt beskrivs syftet med den specifika komponenten med länkar till dokumentation och konfigurations guider som kan användas för att hjälpa till med design-och build-aktiviteter.

### <a name="systems-security"></a>System säkerhet

All kommunikation till resurser i Azure passerar genom Microsofts underhålls nätverks infrastruktur, som tillhandahåller anslutnings-och säkerhetsfunktioner. En mängd skydd placeras automatiskt på plats av Microsoft för att skydda Azure-plattformen och nätverks infrastrukturen och ytterligare funktioner är tillgängliga som tjänster i Azure för att kontrol lera nätverks trafiken och upprätta nätverks segmentering och avgränsning.

### <a name="virtual-network-vnet"></a>Virtual Network (VNet)

Virtuella nätverk är ett av de grundläggande Bygg stenarna för nätverk i Azure. Virtuella nätverk definierar ett IP-adressutrymme och en avgränsning av vägar som ska användas i olika system. Virtuella nätverk är indelade i undernät och alla undernät inom en Virtual Network har en direkt nätverks väg till varandra. Med hjälp av virtuella nätverksgateway (ExpressRoute eller VPN) kan system i ett virtuellt nätverk integreras med lokala och externa miljöer och via Azure som tillhandahålls NAT (Network Address Translation) och offentlig IP-adressallokering, system kan Anslut till Internet eller andra Azure-regioner och-tjänster. Att förstå virtuella nätverk och tillhör ande konfigurations parametrar och routning är avgörande för att förstå och kontrol lera utgående nätverks trafik.

I takt med att virtuella nätverk utgör bas adress utrymmet och vägens gränser i Azure, kan det användas som ett primärt Bygg block av nätverks segment och åtskillnad.

| Resource | Länka |
| --- | --- |
| *Översikt över virtuella nätverk* | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) |
| *Planera Virtual Networks instruktions guide*  | [https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) |
| *Skapa en snabb start för Virtual Network* | [https://docs.microsoft.com/azure/virtual-network/quick-create-portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)
|

### <a name="subnet"></a>Subnet

Undernät är en viktig komponent för nätverks segmentering och uppdelning i Azure. Undernät kan användas för att ge åtskillnad mellan system. Ett undernät är en resurs i ett virtuellt nätverk där NSG: er, väg tabeller och tjänst slut punkter används. Undernät kan användas som både käll-och mål adresser för brand Väggs regler och åtkomst kontrol listor.

Undernät i ett virtuellt nätverk bör planeras för att uppfylla kraven för arbets belastningar och system. Individer som är involverade i utformningen eller implementeringen av undernät bör referera till ACSC-rikt linjerna för nätverks segmentering för att avgöra hur systemen ska grupperas tillsammans i ett undernät.

|Resource|Länka|
|---|---|
|*Lägga till, ändra eller ta bort ett virtuellt nätverksundernät* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
|

### <a name="network-interface"></a>Nätverksgränssnitt

Nätverks gränssnitt är källan för all utgående trafik från en virtuell dator. Nätverks gränssnitt möjliggör konfiguration av IP-adressering och kan användas för att tillämpa NSG: er eller för att dirigera trafik via en NVA. Nätverks gränssnitten för virtuella datorer bör planeras och konfigureras på lämpligt sätt för att anpassas efter övergripande nätverks segment och ansvars mål.

|Resource|Länka|
|---|---|
|*Skapa, ändra eller ta bort ett nätverks gränssnitt* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface) |
|*IP-adressering för nätverks gränssnitt*               | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)
|

### <a name="vnet-integrated-paas"></a>Inbyggt VNet-PaaS

PaaS kan ge förbättrade funktioner och tillgänglighet och minska hanterings kostnaderna men måste skyddas på lämpligt sätt. För att öka kontrollen, tvinga nätverks segmentering eller tillhandahålla en säker utgångs punkt för program och tjänster, kan många PaaS-funktioner integreras med ett virtuellt nätverk.

Med PaaS som en integrerad del av system-eller program arkitekturen tillhandahåller Microsoft flera mekanismer för att distribuera PaaS till ett virtuellt nätverk. Distributions metoden kan hjälpa till att begränsa åtkomsten vid anslutning och integrering med interna system och program. Exempel på detta är App Service miljöer, SQL-hanterade instanser med mera.

När du distribuerar PaaS till ett virtuellt nätverk där routing-och NSG-kontroller har implementerats, är det viktigt att förstå resursens särskilda kommunikations krav, inklusive hanterings åtkomst från Microsoft-tjänster och sökvägen som kommunikations trafiken tas vid svar på inkommande begär Anden från de här tjänsterna.

| Resource  | Länka  |
| --- | --- |
| *Integrering av virtuella nätverk för Azure-tjänster* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services) |
| *Integrera din app med en Azure Virtual Network instruktions guide* | [https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
|

### <a name="public-ip"></a>Offentlig IP

Offentliga IP-adresser används vid kommunikation utanför ett virtuellt nätverk. Detta inkluderar PaaS-resurser och alla vägar med nästa hopp på Internet. Samväldet samväldet entiteter bör planera tilldelningen av offentliga IP-adresser noggrant och bara tilldela dem till resurser där det finns ett äkta krav. Som en allmän utformning bör offentliga IP-adresser tilldelas till kontrollerade utgående punkter för det virtuella nätverket, till exempel Azure-brandväggen, VPN Gateway eller virtuella nätverks installationer.

|Resource|Länka|
|---|---|
|*Översikt över offentliga IP-adresser*  | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses) |
|*Skapa, ändra eller ta bort en offentlig IP-adress* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
|

## <a name="effective-routes"></a>Effektiva routningar

Effektiva vägar är de resultat uppsättningar som bestäms av kombinationen av system vägar, tjänst slut punkter, väg tabeller och BGP och programmet för Azure-routningsmetod. När utgående trafik skickas från ett undernät väljer Azure en väg baserat på mål-IP-adressen med matchningsalgoritmen med det längsta prefixet. Om flera vägar innehåller samma adressprefix väljer Azure vägtyp utifrån följande prioritet:

1. Användardefinierad väg
2. BGP-väg
3. Systemväg

Det är viktigt att Observera att system vägar för trafik som är relaterade till virtuella nätverk, peering av virtuella nätverk eller tjänst slut punkter för virtuella nätverk är prioriterade vägar, även om BGP-vägar är mer exakta.

Personer som är involverade i utformningen eller implementeringen av topologier för routning i Azure bör förstå hur Azure dirigerar trafik och utvecklar en arkitektur som balanserar de nödvändiga funktionerna i system med nödvändig säkerhet och synlighet. Det bör vara viktigt att planera miljön på lämpligt sätt för att undvika alltför stora ingrepp och undantag för att dirigera beteenden eftersom detta ökar komplexiteten och kan göra fel sökning och fel sökning efter svårare och tids krävande.

| Resource | Länka  |
| --- | --- |
| *Visa effektiva vägar* | <https://docs.microsoft.com/azure/virtual-network/manage-route-table#view-effective-routes> |
|

### <a name="system-routes"></a>Systemvägar

För [system vägar](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#system-routes)bör individer som är involverade i design eller implementering av virtuella nätverk förstå standard system vägar och de alternativ som är tillgängliga för att komplettera eller åsidosätta dessa vägar.

### <a name="service-endpoints"></a>Tjänstens slutpunkter

När du aktiverar [tjänstens slut punkter](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) i ett undernät får du en direkt kommunikations väg till den associerade PaaS-resursen. Detta kan ge ökad prestanda och säkerhet genom att begränsa den tillgängliga kommunikations vägen till bara den tjänsten. Användningen av tjänst slut punkter introducerar en potentiell data exfiltrering-sökväg som standard konfiguration ger åtkomst till alla instanser av PaaS-tjänsten snarare än de specifika instanser som krävs för ett program eller system.

Samväldet samväldet-enheter bör utvärdera den risk som är kopplad till att ge direkt åtkomst till PaaS-resursen, inklusive sannolikheten och konsekvensen av sökvägen som används.

För att minska potentiella risker som är kopplade till tjänst slut punkter, implementera service slut punkts principer där det är möjligt eller Överväg att aktivera tjänstens slut punkter i en Azure-brandvägg eller ett NVA-undernät och dirigera trafik från vissa undernät genom den där ytterligare filtrering, övervakning eller inspektion kan tillämpas.

|Resource|Länka|
|---|---|
|*Självstudier: Begränsa nätverks åtkomsten till PaaS-resurser med tjänst slut punkter för virtuella nätverk med hjälp av Azure Portal* |[https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources)|
|

### <a name="route-tables"></a>Routningstabeller

Med routningstabeller får du en administratörs konfigurerad mekanism för att kontrol lera nätverks trafiken i Azure. Routningstabeller kan användas för att vidarebefordra trafik genom till en Azure-brandvägg eller NVA, ansluta direkt till externa resurser eller för att åsidosätta Azures system vägar. Routningstabeller kan också användas för att förhindra att nätverk kan läsas genom en virtuell nätverksgateway som görs tillgängliga för resurser i ett undernät genom att inaktivera spridning av väg för virtuell nätverks-Gateway.

|Resource|Länka|
|---|---|
|*Routa koncept – anpassade vägar* |[https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes)|
|*Självstudier: Dirigera nätverks trafik* |[https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal)|
|

### <a name="border-gateway-protocol-bgp"></a>Border Gateway Protocol (BGP)

BGP kan användas av virtuella nätverks-gatewayer för att dynamiskt utbyta routningsinformation med lokala eller andra externa nätverk. BGP gäller för ett virtuellt nätverk när det konfigureras via en ExpressRoute virtuell nätverksgateway över ExpressRoute privata peering och när det är aktiverat på en Azure-VPN Gateway.

Personer som är involverade i utformningen eller implementeringen av virtuella nätverk och virtuella nätverksgateway i Azure bör ta tid att förstå de uppträdande-och konfigurations alternativ som finns tillgängliga för BGP i Azure.

|Resource|Länka|
|---|---|
|*Routa koncept: BGP* | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#next-hop-types-across-azure-tools](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#next-hop-types-across-azure-tools)|
|*Krav för ExpressRoute-routning* | [https://docs.microsoft.com/azure/expressroute/expressroute-routing](https://docs.microsoft.com/azure/expressroute/expressroute-routing)|
|*Om BGP med Azure VPN Gateway* |[https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview)|
|*Självstudier: Konfigurera en plats-till-plats-VPN via ExpressRoute Microsoft-peering* |[https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering](https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering)|
|

## <a name="next-hop-types"></a>Nästa hopp typer

### <a name="virtual-network"></a>Virtuellt nätverk

Vägar med nästa hopp Virtual Network läggs automatiskt till som system vägar, men kan också läggas till i användardefinierade vägar för att dirigera trafik tillbaka till det virtuella nätverket i instanser där system vägen har åsidosatts.

### <a name="vnet-peering"></a>VNET-peering

VNet-peering möjliggör kommunikation mellan två olika virtuella nätverk. Konfiguration av VNet-peering måste vara aktiverat på varje virtuellt nätverk, men de virtuella nätverken behöver inte finnas i samma region, prenumeration eller kopplade till samma Azure Active Directory-klient (Azure AD).

När du konfigurerar VNet-peering är det viktigt att personer som är involverade i designen eller implementeringen av VNet-peering förstår de fyra associerade konfigurations parametrarna och hur de gäller för varje sida av motparten:

1. **Tillåt åtkomst till virtuellt nätverk:** Välj **aktive rad** (standard) om du vill aktivera kommunikation mellan de två virtuella nätverken. Genom att aktivera kommunikation mellan virtuella nätverk kan resurser som är anslutna till ett virtuellt nätverk kommunicera med varandra med samma bandbredd och latens som om de var anslutna till samma virtuella nätverk.
2. **Tillåt vidarebefordrad trafik:** Markera den här kryss rutan om du vill tillåta trafik som vidarebefordras av en nätverks trafik som inte härstammar från det virtuella nätverket – för att flöda till det virtuella nätverket via en peering. Den här inställningen är grundläggande för att implementera en nätverkstopologi för nav och ekrar.
3. **Tillåt Gateway-överföring:** Markera den här kryss rutan om du vill att det peer-kopplade virtuella nätverket ska använda den virtuella Nätverksgatewayen som är kopplad till det virtuella nätverket. *Tillåt Gateway-överföring* har Aktiver ATS på det virtuella nätverket med resursen för den virtuella Nätverksgatewayen, men gäller endast om *Använd* fjärrgatewayer är aktiverat på det andra virtuella nätverket.
4. **Använd fjärrgatewayer:** Markera den här kryss rutan om du vill tillåta att trafik från det här virtuella nätverket flödar genom en virtuell nätverksgateway som är kopplad till det virtuella nätverk som peer-kopplas med. *Använd* fjärrgatewayer har Aktiver ATS på det virtuella nätverket utan en virtuell nätverksgateway och gäller endast om alternativet *Tillåt Gateway-överföring* är aktiverat på det andra virtuella nätverket.

|Resource|Länka|
|---|---|
| Koncept: Virtuell nätverkspeering                   | [https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) |
| Skapa, ändra eller ta bort en virtuell nätverks-peering | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering)|
|

### <a name="virtual-network-gateway"></a>Virtuell nätverksgateway

Virtuella nätverksgateway tillhandahåller en mekanism för att integrera virtuella nätverk med externa nätverk, till exempel lokala miljöer, partner miljöer och andra moln distributioner. De två typerna av virtuell nätverksgateway är ExpressRoute och VPN.

#### <a name="expressroute-gateway"></a>ExpressRoute-Gateway

ExpressRoute-gatewayer ger en utgångs punkt från det virtuella nätverket till en lokal miljö och bör distribueras för att uppfylla säkerhets-, tillgänglighets-, finans-och prestanda krav. ExpressRoute-gatewayer ger en definierad nätverks bandbredd och skapar användnings kostnader efter distributionen. Virtuella nätverk kan bara ha en ExpressRoute-Gateway, men den kan anslutas till flera ExpressRoute-kretsar och kan utnyttjas av flera virtuella nätverk via VNet-peering, vilket gör att bandbredd och anslutning kan delas. Var försiktig när du konfigurerar routning mellan lokala miljöer och virtuella nätverk med hjälp av ExpressRoute-gatewayer för att säkerställa anslutning från slut punkt till slut punkt med kända, kontrollerade nätverks utgående punkter. Samväldet samväldet entiteter som använder ExpressRoute-Gateway över ExpressRoute privat peering måste också distribuera virtuella nätverks installationer (NVA) för att upprätta en VPN-anslutning till den lokala miljön för att uppfylla kraven på ACSC-konsument vägledning.

Det är viktigt att Observera att ExpressRoute-gatewayer har begränsningar för adress intervall, communities och andra angivna konfigurations objekt som utbyts via BGP.

| Resource  | Länka  |
|---|---|
| Översikt över ExpressRoute Gateway | [https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) |
| Konfigurera en virtuell nätverksgateway för ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager)
|

#### <a name="vpn-gateway"></a>VPN Gateway

Azure VPN Gateway ger en utgångs punkt från det virtuella nätverket till ett externt nätverk för säker plats-till-plats-anslutning. VPN-gatewayer ger en definierad nätverks bandbredd och skapar användnings kostnader efter distributionen. Samväldet samväldet entiteter som använder VPN Gateway bör se till att de konfigureras i enlighet med ACSC-konsument vägledningen. Virtuella nätverk kan bara ha en VPN Gateway, men detta kan konfigureras med flera tunnlar och kan utnyttjas av flera virtuella nätverk via VNet-peering, vilket gör att flera virtuella nätverk kan dela bandbredd och anslutning. VPN-gatewayer kan upprättas via Internet eller via ExpressRoute via Microsoft-peering.

| Resource  | Länka |
| --- | --- |
| Översikt över VPN Gateway| [https://docs.microsoft.com/azure/vpn-gateway](https://docs.microsoft.com/azure/vpn-gateway)|
| Planering och design för VPN Gateway | [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)|
| Azure VPN Gateway i Azure Australien | [Azure VPN Gateway i Azure Australien](vpn-gateway.md)
|

### <a name="next-hop-of-virtual-appliance"></a>Nästa hopp för virtuell installation

Nästa hopp för virtuell installation ger möjlighet att bearbeta nätverks trafik utanför Azure-nätverk och routning-topologi som tillämpas på virtuella nätverk. Virtuella installationer kan använda säkerhets regler, översätta adresser, upprätta VPN, proxyautentisering eller en mängd andra funktioner. Nästa hopp för virtuell installation tillämpas via UDR i en routningstabell och kan användas för att dirigera trafik till en Azure-brandvägg, enskilda NVA eller Azure Load Balancer tillhandahålla tillgänglighet över flera NVA. Om du vill använda en virtuell installation för routning måste de associerade nätverks gränssnitten vara aktiverade för IP-vidarebefordring.

| Resource  | Länka |
| --- | ---|
| Routa koncept: Anpassade vägar | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) |
| Aktivera eller inaktivera IP-vidarebefordring | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#enable-or-disable-ip-forwarding](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#enable-or-disable-ip-forwarding)
|

### <a name="next-hop-of-virtualnetworkserviceendpoint"></a>Nästa hopp för VirtualNetworkServiceEndpoint

Vägar med en nästa hopp typ av VirtualNetworkServiceEndpoint läggs bara till när en tjänst slut punkt konfigureras i ett undernät och inte kan konfigureras manuellt via routningstabeller.

### <a name="next-hop-of-internet"></a>Nästa hopp på Internet

Nästa hopp-Internet används för att komma åt resurser som använder en offentlig IP-adress, inklusive Internet samt PaaS och Azure-tjänster i Azure-regioner. Nästa hopp kräver ingen standard väg (0.0.0.0/0) som täcker alla nätverk, men kan användas för att aktivera vägar till vägar till vissa offentliga tjänster. Nästa hopp för Internet ska användas för att lägga till vägar till auktoriserade tjänster och funktioner som krävs för systemfunktioner, till exempel Microsoft Management-adresser.

Exempel på tjänster som kan läggas till med nästa hopp på Internet:

1. Nyckel hanterings tjänster för Windows-aktivering
2. App Service-miljön hantering

|Resource|Länka|
|---|---|
| Utgående anslutningar i Azure | [https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) |
| Använd Azure-anpassade vägar för att aktivera KMS-aktivering | [https://docs.microsoft.com/bs-latn-ba/azure/virtual-machines/troubleshooting/custom-routes-enable-kms-activation](https://docs.microsoft.com/bs-latn-ba/azure/virtual-machines/troubleshooting/custom-routes-enable-kms-activation) |
| Låsa en App Service-miljön  | [https://docs.microsoft.com/azure/app-service/environment/firewall-integration](https://docs.microsoft.com/azure/app-service/environment/firewall-integration) |
|

### <a name="next-hop-of-none"></a>Nästa hopp om ingen

Nästa hopp för ingen kan användas för att förhindra kommunikation till ett speciellt nätverk. I motsats till en NSG, som styr om trafiken tillåts eller nekas från att passera en tillgänglig nätverks Sök väg, tar ett nästa hopp med ingen bort nätverks Sök vägen helt. Var försiktig när du skapar vägar med nästa hopp att ingen, särskilt när du tillämpar den på en standard väg på 0.0.0.0/0 eftersom detta kan ha oönskade konsekvenser och kan göra fel sökning av system problem komplex och tids krävande.

## <a name="security"></a>Säkerhet

Att implementera nätverks segmenterings-och särskiljation-kontroller på IaaS-och PaaS-funktionerna uppnås genom att skydda funktionerna och genom att implementera kontrollerade kommunikations vägar från de system som ska kommunicera med funktionen.

Att utforma och skapa lösningar i Azure är en process för att skapa en logisk arkitektur för att förstå, kontrol lera och övervaka nätverks resurser i hela Azure-närvaron. Den här logiska arkitekturen är program vara som definierats inom Azure-plattformen och som är platsen för en fysisk nätverkstopologi som implementeras i traditionella nätverks miljöer.

Den logiska arkitektur som skapas måste ge de funktioner som behövs för användbarhet, men de måste också ge den synlighet och kontroll som krävs för säkerhet och integritet.

Att uppnå detta resultat baseras på implementering av nödvändiga nätverks segment och anslags verktyg, men även för att skydda och tvinga nätverks sto pol Ogin och implementeringen av dessa verktyg.

### <a name="network-security-groups-nsgs"></a>Nätverks säkerhets grupper (NSG: er)

NSG: er används för att ange inkommande och utgående trafik som tillåts för ett undernät eller ett särskilt nätverks gränssnitt. När du konfigurerar NSG: er bör samväldet entiteter använda en vit listning metod där regler har kon figurer ATS för att tillåta nödvändig trafik med en standard regel som kon figurer ATS för att neka all trafik som inte matchar en speciell Permit-instruktion. När du planerar och konfigurerar NSG: er måste du se till att all nödvändig inkommande och utgående trafik samlas in på rätt sätt. Detta omfattar att identifiera och förstå alla privata IP-adressintervall som används i virtuella nätverk och i den lokala miljön, samt särskilda Microsoft-tjänster, till exempel Azure Load Balancer-och PaaS hanterings krav. Individer som är involverade i utformningen och implementeringen av NSG: er bör också förstå användningen av service märken och program säkerhets grupper för att skapa detaljerade, service-och programspecifika säkerhets regler.

Det är viktigt att Observera att standard konfigurationen för en NSG tillåter utgående trafik till alla adresser inom det virtuella nätverket och alla offentliga IP-adresser.

|Resource|Länka|
|---|---|
|Översikt över nätverks säkerhet | [https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)|
|Skapa, ändra eller ta bort en nätverks säkerhets grupp | [https://docs.microsoft.com/azure/virtual-network/manage-network-security-group](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)|
|

### <a name="azure-firewall"></a>Azure Firewall

Azure-brandväggen kan utnyttjas för att skapa en nätverkstopologi för nav och ekrar och tillämpa centraliserade nätverks säkerhets kontroller. Azure-brandväggen kan användas för att uppfylla de nödvändiga kraven i ISM-funktionen för utgående trafik genom att implementera en lista över tillåtna program där endast de IP-adresser, protokoll, portar och FQDN som krävs för systemfunktioner är tillåtna. Samväldet entiteter bör ta en riskfylld metod för att avgöra om de säkerhetsfunktioner som tillhandahålls av Azure-brandväggen är tillräckliga för deras krav. För scenarier där ytterligare säkerhetsfunktioner utöver de som tillhandahållits av Azure Firewall krävs bör du överväga att implementera NVA.

|Resource|Länka|
|---|---|
|*Dokumentation om Azure Firewall* | [https://docs.microsoft.com/azure/firewall](https://docs.microsoft.com/azure/firewall)|
|*Självstudier: Distribuera och konfigurera Azure-brandväggen i ett hybrid nätverk med Azure PowerShell* | [https://docs.microsoft.com/azure/firewall/tutorial-hybrid-ps](https://docs.microsoft.com/azure/firewall/tutorial-hybrid-ps)|
|

### <a name="network-virtual-appliances-nvas"></a>Virtuella nätverks enheter (NVA)

NVA kan användas för att skapa en topologi för nav och ekrar, tillhandahålla förbättrade eller kompletterande nätverksfunktioner eller kan användas som ett alternativ till Azures nätverks mekanismer för välbekant och konsekvent support och hantering med lokala nätverks tjänster. NVA kan distribueras för att uppfylla särskilda säkerhets krav som; scenarier där det finns ett krav på identitets medvetenhet kopplad till nätverks trafik, HTTPS-dekryptering, innehålls granskning, filtrering eller andra säkerhets funktioner. NVA bör distribueras i en konfiguration med hög tillgänglighet och personer som är involverade i utformningen eller implementeringen av NVA bör se lämplig leverantörs dokumentation för rikt linjer för distribution i Azure.

|Resource|Länka|
|---|---|
|*Distribuera virtuella nätverks enheter med hög tillgänglighet* | [https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha)|
|

### <a name="service-endpoint-policies-preview"></a>Principer för tjänst slut punkt (för hands version)

Konfigurera tjänst slut punkts principer baserat på tillgängligheten för tjänsten och en säkerhets risk bedömning av sannolikheten och effekten av data exfiltrering. Tjänst slut punkts principer bör övervägas för Azure Storage och hanteras på grund av fall för andra tjänster baserat på den associerade risk profilen.

| Resource | Länka  |
| --- | --- |
| *Översikt över tjänst slut punkts principer* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview) |
| *Skapa, ändra eller ta bort tjänstens slut punkts princip med hjälp av Azure Portal* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-portal](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-portal)
|

### <a name="azure-policy"></a>Azure Policy

Azure Policy är en viktig komponent för att genomdriva och upprätthålla integriteten för Azure-miljöns logiska arkitektur. Det finns en mängd olika tjänster och utgående nätverks trafik Sök vägar tillgängliga via Azure-tjänster. Det är viktigt att samväldet entiteter är medvetna om de resurser som finns i deras miljö och de tillgängliga utgångs punkterna för nätverket. För att säkerställa att ej auktoriserade nätverks utgående punkter inte skapas i Azure-miljön bör du använda Azure Policy för att kontrol lera vilka typer av resurser som kan distribueras och konfigurationen av dessa resurser. Praktiska exempel är att begränsa resurser till enbart de som godkänts och godkänts för användning och som kräver att NSG: er läggs till i undernät.

|Resource | Länka|
|---|---|
|*Översikt över Azure Policy* | [https://docs.microsoft.com/azure/governance/policy/overview](https://docs.microsoft.com/azure/governance/policy/overview)|
|*Exempel princip för tillåtna resurs typer* | [https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types](https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types)|
|*Tvinga NSG i en exempel princip för undernät*| [https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet](https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet)|
|

## <a name="paas-egress-capabilities"></a>PaaS utgående funktioner

PaaS-funktioner ger möjligheter till ökad funktionalitet och förenklad hantering, men introducerar komplicerade kunskaper om adresserings krav för nätverks segmentering och uppdelning. PaaS-funktioner konfigureras vanligt vis med offentliga IP-adresser och är tillgängliga från Internet.  Om du använder PaaS-funktioner i dina system och lösningar bör du tänka på att identifiera kommunikations flöden mellan komponenter och skapa nätverks säkerhets regler för att endast tillåta denna kommunikation. Som en del av en skydds-och djupgående metod för säkerhet ska PaaS-funktioner konfigureras med kryptering, autentisering och lämpliga åtkomst kontroller och behörigheter.  

### <a name="public-ip-for-paas"></a>Offentlig IP för PaaS

Offentliga IP-adresser för PaaS-funktioner allokeras baserat på den region där tjänsten är värdbaserad eller distribuerad. Det krävs en förståelse för offentlig IP-adressallokering och regioner om du ska bygga lämpliga nätverks säkerhets regler och topologi för nätverks segmentering och uppdelning av virtuella Azure-nätverk, PaaS och ExpressRoute och Internet anslutning. Azure allokerar IP-adresser från en pool som allokerats till varje Azure-region. Microsoft gör de adresser som används i varje region som är tillgängliga för hämtning, vilket uppdateras i ett regelbundet och kontrollerat sätt. Tjänsterna som är tillgängliga i varje region kan också ofta ändras när nya tjänster släpps eller tjänster distribueras mer sällan. Samväldet entiteter bör regelbundet granska detta material och kan använda Automation för att underhålla system efter behov. Vissa IP-adresser för vissa tjänster som finns i varje region kan erhållas genom att kontakta Microsoft support.

| Resource | Länka |
| --- | --- |
| *Microsoft Azure Datacenter IP-intervall*                   | [https://www.microsoft.com/download/details.aspx?id=41653](https://www.microsoft.com/download/details.aspx?id=41653) |
| *Azure-tjänster per region*                              | [https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional, Australien – centrala, Australien-centrala-2, Australien, östra, Australien, sydöstra & Products = all](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all) |
| *Inkommande och utgående IP-adresser i Azure App Service* | [https://docs.microsoft.com/azure/app-service/overview-inbound-outbound-ips](https://docs.microsoft.com/azure/app-service/overview-inbound-outbound-ips)
|

## <a name="next-steps"></a>Nästa steg

Jämför din övergripande arkitektur och design med publicerade [skyddade ritningar för IaaS-och PaaS-webbprogram](https://aka.ms/au-protected).
