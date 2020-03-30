---
title: Nätverksarkitektur för SAP HANA på Azure (stora instanser) | Microsoft-dokument
description: Nätverksarkitektur för hur du distribuerar SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 33684a6292d7e51c04f6bacc7c49ee5986dbec10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502398"
---
# <a name="sap-hana-large-instances-network-architecture"></a>SAP HANA-nätverksarkitektur (stora instanser)

Arkitekturen för Azure-nätverkstjänster är en viktig komponent i den lyckade distributionen av SAP-program på HANA Large Instance. Sap HANA på Azure-distributioner (Stora instanser) har vanligtvis ett större SAP-landskap med flera olika SAP-lösningar med varierande storlekar av databaser, CPU-resursförbrukning och minnesanvändning. Det är troligt att inte alla IT-system redan finns i Azure. Ditt SAP-landskap är ofta hybrid samt från en DBMS punkt och SAP ansökan synvinkel med hjälp av en blandning av NetWeaver, och S/4HANA och SAP HANA och andra DBMS. Azure erbjuder olika tjänster som gör att du kan köra de olika DBMS-, NetWeaver- och S/4HANA-systemen i Azure. Azure erbjuder dig också nätverksteknik för att få Azure att se ut som ett virtuellt datacenter till dina lokala programvarudistributioner

Om inte dina kompletta IT-system finns i Azure. Azure-nätverksfunktioner används för att ansluta den lokala världen med dina Azure-resurser för att få Azure att se ut som ett virtuellt datacenter för dig. De Azure-nätverksfunktioner som används är: 

- Virtuella Azure-nätverk är anslutna till [ExpressRoute-kretsen](https://azure.microsoft.com/services/expressroute/) som ansluter till dina lokala nätverksresurser.
- En ExpressRoute-krets som ansluter lokalt till Azure bör ha en minsta bandbredd på [1 Gbit/s eller högre](https://azure.microsoft.com/pricing/details/expressroute/). Denna minimala bandbredd möjliggör tillräcklig bandbredd för överföring av data mellan lokala system och system som körs på virtuella datorer. Det möjliggör också tillräcklig bandbredd för anslutning till Azure-system från lokala användare.
- Alla SAP-system i Azure är konfigurerade i virtuella nätverk för att kommunicera med varandra.
- Active Directory och DNS-värd lokalt utökas till Azure via ExpressRoute från lokalt eller körs komplett i Azure.

För det specifika fallet med att integrera STORA HANA-instanser i Azure-nätverksinfrastrukturen för datacenter används Azure ExpressRoute-teknik också


> [!NOTE] 
> Endast en Azure-prenumeration kan bara länkas till en klient i en HANA-stämpel för stor instans i en viss Azure-region. Omvänt kan en enda HANA Stor instans stämpelklient vara länkad till endast en Azure-prenumeration. Det här kravet är förenligt med andra fakturerbara objekt i Azure.

Om SAP HANA på Azure (stora instanser) distribueras i flera olika Azure-regioner distribueras en separat klient i stämpeln HANA Large Instance. Du kan köra båda under samma Azure-prenumeration så länge dessa instanser ingår i samma SAP-liggande. 

> [!IMPORTANT] 
> Endast Azure Resource Manager-distributionsmetoden stöds med SAP HANA på Azure (Stora instanser).

 

## <a name="additional-virtual-network-information"></a>Ytterligare information om virtuella nätverk

Om du vill ansluta ett virtuellt nätverk till ExpressRoute måste en Azure ExpressRoute-gateway skapas. Mer information finns i [Om Expressroute-gateways för ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

En Azure ExpressRoute-gateway används med ExpressRoute till en infrastruktur utanför Azure eller till en Azure Large Instance-stämpel. Du kan ansluta Azure ExpressRoute-gatewayen till högst fyra olika ExpressRoute-kretsar så länge dessa anslutningar kommer från olika Microsoft-företagskantroutrar. Mer information finns i [SAP HANA-infrastruktur (Stora instanser) och anslutning på Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> Det maximala dataflödet du kan uppnå med en ExpressRoute-gateway är 10 Gbit/s med hjälp av en ExpressRoute-anslutning. Att kopiera filer mellan en virtuell dator som finns i ett virtuellt nätverk och ett lokalt system (som en enda kopieringsström) uppnår inte det fullständiga dataflödet för de olika gateway-SKU:erna. Använd flera strömmar för att utnyttja den fullständiga bandbredden för ExpressRoute-gatewayen. Du måste också kopiera olika filer i parallella strömmar från en enda fil.


## <a name="networking-architecture-for-hana-large-instance"></a>Nätverksarkitektur för HANA Large Instance
Nätverksarkitekturen för HANA Large Instance kan delas upp i fyra olika delar:

- Lokal nätverks- och ExpressRoute-anslutning till Azure. Den här delen är kundens domän och är ansluten till Azure via ExpressRoute. Denna Expressroute-krets betalas fullt ut av dig som kund. Bandbredden bör vara tillräckligt stor för att hantera nätverkstrafiken mellan dina lokala resurser och den Azure-region som du ansluter mot. Se den nedre högra i följande bild.
- Azure-nätverkstjänster, som tidigare diskuterats, med virtuella nätverk, som återigen behöver ExpressRoute-gateways tillagda. Den här delen är ett område där du måste hitta lämpliga konstruktioner för dina applikationskrav, säkerhet och efterlevnadskrav. Oavsett om du använder HANA Large Instance är en annan punkt att tänka på när det gäller antalet virtuella nätverk och Azure gateway SKU:er att välja mellan. Se den övre högra i figuren.
- Anslutning av HANA Large Instance via ExpressRoute-teknik till Azure. Den här delen distribueras och hanteras av Microsoft. Allt du behöver göra är att ange vissa IP-adressintervall efter distributionen av dina tillgångar i HANA Large Instance ansluter ExpressRoute-kretsen till de virtuella nätverken. Mer information finns i [SAP HANA-infrastruktur (Stora instanser) och anslutning på Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Det finns ingen extra avgift för dig som kund för anslutningen mellan Azure-datacenternätverksinfrastrukturen och HANA Large Instance-enheterna.
- Nätverk inom hana stora instans stämpel, som är mestadels transparent för dig.

![Virtuellt nätverk som är anslutet till SAP HANA på Azure (stora instanser) och lokalt](./media/hana-overview-architecture/image1-architecture.png)

Kravet på att dina lokala resurser måste ansluta via ExpressRoute till Azure ändras inte eftersom du använder HANA Large Instance. Kravet på att ha ett eller flera virtuella nätverk som kör de virtuella datorerna, som är värd för programlagret som ansluter till HANA-instanserna som finns i HANA Large Instance-enheter, ändras inte heller. 

Skillnaderna till SAP-distributioner i Azure är:

- HANA Large Instance-enheterna för din kundklient är anslutna via en annan ExpressRoute-krets till dina virtuella nätverk. För att separera belastningsvillkor delar de lokala till Azure virtuella nätverket ExpressRoute-kretsar och kretsarna mellan virtuella Azure-nätverk och STORA HANA-instanser inte samma routrar.
- Arbetsbelastningsprofilen mellan SAP-programlagret och HANA Large Instance är av en annan natur, med många små begäranden och bursts som dataöverföringar (resultatuppsättningar) från SAP HANA till programlagret.
- SAP-programarkitekturen är mer känslig för nätverksfördröjning än typiska scenarier där data utbyts mellan lokala och Azure.
- Azure ExpressRoute-gatewayen har minst två ExpressRoute-anslutningar. En krets som är ansluten från lokala och en som är ansluten från HANA Stora instanser. Detta ger bara plats för ytterligare två kretsar från olika MSEEs att ansluta till på ExpressRoute Gateway. Den här begränsningen är oberoende av användningen av ExpressRoute Fast Path. Alla anslutna kretsar delar maximal bandbredd för inkommande data för ExpressRoute-gatewayen.

Med Revision 3 av HANA Large Instance-stämplar kan nätverksfördröjningen mellan virtuella datorer och STORA HANA-instansenheter vara högre än en typisk svarstid för VM-till-VM-nätverk. Beroende på Azure-regionen kan de uppmätta värdena överskrida svarstiden på 0,7 ms tur och retur som klassificeras som lägre än genomsnittet i [SAP Note #1100926 - Vanliga frågor och svar: Nätverksprestanda](https://launchpad.support.sap.com/#/notes/1100926/E). Beroende på Azure-region och verktyg för att mäta svarstid för nätverk tur och retur mellan en Azure VM och HANA Large Instance-enhet kan den uppmätta svarstiden vara upp till och cirka 2 millisekunder. Kunder distribuerar dock SAP HANA-baserade SAP-program för produktion på SAP HANA Large Instance. Se till att du testar dina affärsprocesser noggrant i Azure HANA Large Instance. En ny funktion, som kallas ExpressRoute Fast Path, kan minska nätverksfördröjningen mellan HANA Stora instanser och virtuella programlager virtuella datorer i Azure avsevärt (se nedan). 

Med Revision 4 av HANA Large Instance-stämplar har nätverksfördröjningen mellan virtuella Azure-datorer som distribueras i närheten av stämpeln HANA Large Instance erfarenhet av att uppfylla den genomsnittliga eller bättre än genomsnittliga klassificeringen som dokumenteras i [SAP Note #1100926 - Vanliga frågor och svar: Nätverksprestanda](https://launchpad.support.sap.com/#/notes/1100926/E) om Azure ExpressRoute Fast Path är konfigurerad (se nedan). För att distribuera virtuella Azure-datorer i närheten av HANA Large Instance-enheter i Revision 4 måste du använda [Azure Proximity Placement Groups](https://docs.microsoft.com/azure/virtual-machines/linux/co-location). Hur närhetsplaceringsgrupper kan användas för att hitta SAP-programlagret i samma Azure-datacenter som Revision 4-värdbaserade HANA-enheter för stor instans beskrivs i [Azure Proximity Placement Groups för optimal nätverksfördröjning med SAP-program](sap-proximity-placement-scenarios.md).

För att ge deterministisk nätverksfördröjning mellan virtuella datorer och HANA-stor instans är valet av ExpressRoute gateway SKU viktigt. Till skillnad från trafikmönstren mellan lokala och virtuella datorer kan trafikmönstret mellan virtuella datorer och STORA HANA-instansen utveckla små men höga skurar av begäranden och datavolymer som ska överföras. För att hantera sådana skurar väl rekommenderar vi starkt användning av UltraPerformance gateway SKU. För klassen Type II för SKU:er för stora instanser av typen II är det obligatoriskt att använda UltraPerformance gateway SKU som en ExpressRoute-gateway.

> [!IMPORTANT] 
> Med tanke på den totala nätverkstrafiken mellan SAP-programmet och databaslager, stöds endast HighPerformance eller UltraPerformance gateway SKU:er för virtuella nätverk för anslutning till SAP HANA på Azure (stora instanser). För SKU:er av typen HANA-instans II stöds endast UltraPerformance gateway SKU som en ExpressRoute-gateway. Undantag gäller när expressroute snabbsökväg (se nedan)

### <a name="expressroute-fast-path"></a>Snabb sökväg i ExpressRoute
För att sänka svarstiden introducerades ExpressRoute Fast Path i maj 2019 för specifik anslutning av HANA Stora instanser till virtuella Azure-nätverk som är värdar för virtuella SAP-program virtuella datorer. Den största skillnaden mot lösningen som hittills har distribuerats är att dataflödena mellan virtuella datorer och STORA HANA-instanser inte längre dirigeras via ExpressRoute-gatewayen. I stället kommunicerar de virtuella datorer som har tilldelats i undernäten i det virtuella Azure-nätverket direkt med den dedikerade företagsgränsroutern. 

> [!IMPORTANT] 
> ExpressRoute Fast Path-funktionen kräver att undernäten som kör virtuella SAP-program-datorer finns i samma virtuella Azure-nätverk som har anslutits till DEA Stora instanserna. Virtuella datorer som finns i virtuella Azure-nätverk som är peered med det virtuella Azure-nätverket som är anslutet direkt till HANA-enheter för stora instanser drar inte nytta av ExpressRoute Fast Path. Som ett resultat typiska nav och ekrade virtuella nätverk design, där ExpressRoute kretsar ansluter mot ett nav virtuellt nätverk och virtuella nätverk som innehåller SAP-programskiktet (ekrar) blir peered, optimering av ExpressRoute Fast Sökvägen fungerar inte. I tillägg stöder ExpressRoute Fast Path inte användardefinierade routningsregler (UDR) idag. Mer information finns i [ExpressRoutes virtuella nätverksgateway och FastPath](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways). 


Mer information om hur du konfigurerar Snabb sökväg för ExpressRoute finns i dokumentet [Anslut ett virtuellt nätverk till STORA HANA-instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).    

> [!NOTE]
> En UltraPerformance ExpressRoute-gateway krävs för att ExpressRoute Fast Path ska fungera


## <a name="single-sap-system"></a>Enda SAP-system

Den lokala infrastruktur som tidigare visats är ansluten via ExpressRoute till Azure. ExpressRoute-kretsen ansluts till en MSEE (Microsoft Enterprise Edge Router). Mer information finns i [Den tekniska översikten för ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). När vägen har upprättats ansluter den till Azure-stamnätet.

> [!NOTE] 
> Om du vill köra SAP-landskap i Azure ansluter du till företagets kantrouter närmast Azure-regionen i SAP-landskapet. Hana Large Instance-stämplar är anslutna via dedikerade routerenheter för företagskant för att minimera nätverksfördröjningen mellan virtuella datorer i Azure IaaS- och HANA Large Instance-stämplar.

ExpressRoute-gatewayen för de virtuella datorerna som är värdar för SAP-programinstanser är anslutna till en ExpressRoute-krets som ansluter till lokala. Samma virtuella nätverk är anslutet till en separat router för företagskant som är dedikerad till anslutning till stora instansstämplar. Med Hjälp av Snabb sökväg i ExpressRoute dirigeras dataflödet från STORA HANA-instanser till virtuella SAP-programlager inte längre via ExpressRoute-gatewayen och med det minskar nätverksfördröjningsfördröjningen.

Detta system är ett enkelt exempel på ett enda SAP-system. SAP-programlagret finns i Azure. SAP HANA-databasen körs på SAP HANA på Azure (Stora instanser). Antagandet är att ExpressRoute gateway bandbredd på 2-Gbps eller 10-Gbps dataflöde inte representerar en flaskhals.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Flera SAP-system eller stora SAP-system

Om flera SAP-system eller stora SAP-system distribueras för att ansluta till SAP HANA på Azure (stora instanser), kan dataflödet för ExpressRoute-gatewayen bli en flaskhals. Eller så vill du isolera produktions- och icke-produktionssystem i olika virtuella Azure-nätverk. I så fall delar du upp programlagren i flera virtuella nätverk. Du kan också skapa ett särskilt virtuellt nätverk som ansluter till HANA Large Instance för ärenden som:

- Utför säkerhetskopior direkt från HANA-instanserna i HANA Large Instance till en virtuell dator i Azure som är värd för NFS-resurser.
- Kopiera stora säkerhetskopior eller andra filer från HANA Large Instance-enheter till diskutrymme som hanteras i Azure.

Använd ett separat virtuellt nätverk som värd för virtuella datorer som hanterar lagring för massöverföring av data mellan HANA Stora instanser och Azure. Det här arrangemanget undviker effekterna av stor fil- eller dataöverföring från HANA Large Instance till Azure på ExpressRoute-gatewayen som betjänar de virtuella datorerna som kör SAP-programlagret. 

För en mer skalbar nätverksarkitektur:

- Utnyttja flera virtuella nätverk för ett enda, större SAP-programlager.
- Distribuera ett separat virtuellt nätverk för varje SAP-system som distribueras, jämfört med att kombinera dessa SAP-system i separata undernät under samma virtuella nätverk.

  En mer skalbar nätverksarkitektur för SAP HANA på Azure (stora instanser):

![Distribuera SAP-programlager över flera virtuella nätverk](./media/hana-overview-architecture/image4-networking-architecture.png)

Beroende på regler och begränsningar, du vill tillämpa mellan de olika virtuella nätverk som är värd virtuella datorer i olika SAP-system, bör du peer dessa virtuella nätverk. Mer information om virtuell nätverks peering finns i [Virtuell nätverks peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


## <a name="routing-in-azure"></a>Routning i Azure

Som standarddistribution är tre nätverksroutningsöverväganden viktiga för SAP HANA på Azure (Stora instanser):

* SAP HANA på Azure (Stora instanser) kan endast nås via virtuella Azure-datorer och den dedikerade ExpressRoute-anslutningen, inte direkt från lokala. Direkt åtkomst från lokalt till HANA Large Instance-enheter, som levereras av Microsoft till dig, är inte möjligt omedelbart. De transitiva routningsbegränsningarna beror på den aktuella Azure-nätverksarkitekturen som används för SAP HANA Large Instance. Vissa administrationsklienter och alla program som behöver direkt åtkomst, till exempel SAP Solution Manager som körs lokalt, kan inte ansluta till SAP HANA-databasen. För undantag finns i avsnittet Direkt routning till stora HANA-instanser.

* Om du har hana stora instansenheter som distribueras i två olika Azure-regioner för haveriberedskap, samma tillfälliga routningsbegränsningar som tillämpats tidigare. Med andra ord dirigerades inte IP-adresser för en HANA-enhet för stora instanser i en region (till exempel US West) till en HANA-enhet för stor instans som distribuerades i en annan region (till exempel US East). Den här begränsningen var oberoende av användningen av Azure-nätverks peering mellan regioner eller korsanslutning av ExpressRoute-kretsar som ansluter HANA-enheter för stora instanser till virtuella nätverk. En grafisk representation finns i avsnittet "Använd hana-enheter för stora instanser i flera regioner". Den här begränsningen, som följde med den distribuerade arkitekturen, förbjöd omedelbar användning av HANA System Replication som funktioner för haveriberedskap. För de senaste ändringarna slår du upp avsnittet Använd HANA-enheter för stora instanser i flera regioner. 

* SAP HANA på Azure-enheter (Stora instanser) har en tilldelad IP-adress från serverns IP-pooladressintervall som du skickade när du begär hana-distributionen för stora instanser. Mer information finns i [SAP HANA-infrastruktur (Stora instanser) och anslutning på Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Den här IP-adressen är tillgänglig via Azure-prenumerationer och -krets som ansluter virtuella Azure-nätverk till STORA HANA-instanser. IP-adressen som tilldelats utanför server-IP-pooladressintervallet tilldelas maskinvaraenheten direkt. Det är *inte* tilldelat via NAT längre, vilket var fallet i de första distributionerna av den här lösningen. 

### <a name="direct-routing-to-hana-large-instances"></a>Direkt routning till STORA HANA-instanser

Som standard fungerar inte transitiv routning i följande scenarier:

* Mellan HANA Large Instance-enheter och en lokal distribution.

* Mellan HANA Large Instance-routning som distribueras i två olika regioner.

Det finns tre sätt att aktivera transitiv routning i dessa scenarier:

- En omvänd proxy för att dirigera data till och från. Till exempel F5 BIG-IP, NGINX med Traffic Manager distribueras i Azure virtuella nätverk som ansluter till HANA stora instanser och till lokala som en virtuell brandvägg / trafik routning lösning.
- Använda [IPTables-regler](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) i en virtuell Linux-dator för att aktivera routning mellan lokala platser och HANA-enheter för stora instanser, eller mellan HANA-enheter för stora instanser i olika regioner. Den virtuella datorn som kör IPTables måste distribueras i det virtuella Azure-nätverket som ansluter till STORA HANA-instanser och till lokala. Den virtuella datorn måste dimensioneras i enlighet med detta, så att nätverksdataflödet för den virtuella datorn är tillräckligt för den förväntade nätverkstrafiken. Mer information om vm-nätverksbandbredd finns i artikeln [Storlekar på virtuella Linux-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json).
- [Azure-brandväggen](https://azure.microsoft.com/services/azure-firewall/) skulle vara en annan lösning för att aktivera direkt trafik mellan lokala och HANA Large-instansenheter. 

All trafik av dessa lösningar skulle dirigeras via ett virtuellt Azure-nätverk och som sådan kan trafiken dessutom begränsas av de mjuka enheter som används eller av Azure Network Security Groups, så att vissa IP-adresser eller IP-adresser sträcker sig från lokala kan blockeras eller uttryckligen tillåtas komma åt STORA HANA-instanser. 

> [!NOTE]  
> Tänk på att implementering och support för anpassade lösningar som involverar nätverksinstallationer från tredje part eller IPTables inte tillhandahålls av Microsoft. Support måste tillhandahållas av leverantören av den komponent som används eller integratören. 

#### <a name="express-route-global-reach"></a>Express Route global räckvidd
Microsoft introducerade en ny funktion som kallas [ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach). Global Reach kan användas för STORA HANA-instanser i två scenarier:

- Aktivera direkt åtkomst från lokalt till dina STORA HANA-instansenheter som distribueras i olika regioner
- Aktivera direkt kommunikation mellan dina STORA HANA-instansenheter som distribueras i olika regioner


##### <a name="direct-access-from-on-premises"></a>Direkt åtkomst från lokala
I Azure-regioner där Global Reach erbjuds kan du begära att aktivera global räckviddsfunktioner för din ExpressRoute-krets som ansluter ditt lokala nätverk till det virtuella Azure-nätverket som också ansluter till dina HANA-enheter för stora instanser. Det finns vissa kostnadskonsekvenser för den lokala sidan av din ExpressRoute-krets. För priser, kontrollera priserna för [Global Reach Add-On](https://azure.microsoft.com/pricing/details/expressroute/). Det finns inga extra kostnader för dig som är relaterad till kretsen som ansluter HANA Large Instance-enheterna till Azure. 

> [!IMPORTANT]  
> Om du använder Global Reach för att aktivera direkt åtkomst mellan dina HANA-enheter för stora instanser och lokala resurser dirigeras inte nätverksdata och kontrollflöde **via virtuella Azure-nätverk,** utan direkt mellan Microsofts företagsutbytesroutrar. Därför blir inga NSG- eller ASG-regler, eller någon typ av brandvägg, NVA eller proxy som du har distribuerat i ett virtuellt Azure-nätverk, inte rörd. **Om du använder ExpressRoute Global Reach för att möjliggöra direkt åtkomst från lokalt till HANA Stora instansenheter måste begränsningar och behörigheter för åtkomst till stora HANA-instansenheter definieras i brandväggar på den lokala sidan** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>Ansluta STORA HANA-instanser i olika Azure-regioner
På samma sätt som ExpressRoute Global Reach kan användas för att ansluta lokalt till HANA-enheter för stora instanser, kan den användas för att ansluta två HANA Large Instance-klienter som distribueras åt dig i två olika regioner. Isoleringen är ExpressRoute-kretsar som dina HANA Large Instance-klienter använder för att ansluta till Azure i båda regionerna. Det finns inga ytterligare avgifter för anslutning av två HANA-klienter för stora instanser som distribueras i två olika regioner. 

> [!IMPORTANT]  
> Dataflödet och kontrollflödet för nätverkstrafiken mellan de olika HANA Large-instansenordningarna dirigeras inte via azure-nätverk. Därför kan du inte använda Azure-funktioner eller NVA:er för att tillämpa kommunikationsbegränsningar mellan dina två HANA-klienter för stora instanser. 

Mer information om hur du aktiverar ExpressRoute Global Reach finns i dokumentet [Anslut ett virtuellt nätverk till STORA HANA-instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).


## <a name="internet-connectivity-of-hana-large-instance"></a>Internetanslutning för HANA Large Instance
HANA Large Instance har *ingen* direkt internetanslutning. Som ett exempel kan den här begränsningen begränsa din möjlighet att registrera OS-avbildningen direkt med OS-leverantören. Du kan behöva arbeta med din lokala SUSE Linux Enterprise Server Subscription Management Tool server eller Red Hat Enterprise Linux Subscription Manager.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Datakryptering mellan virtuella datorer och STOR HANA-instans
Data som överförs mellan HANA Large Instance och virtuella datorer är inte krypterade. Men enbart för utbytet mellan HANA DBMS-sidan och JDBC/ODBC-baserade program kan du aktivera kryptering av trafik. Mer information finns i [den här dokumentationen från SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Använda HANA-stora instansenheter i flera regioner

För att realisera katastrofåterställningsuppsättningar måste du ha SHANA-enheter för stora instanser i flera Azure-regioner. Även med användning av Azure [Global Vnet Peering] fungerar den transitiva routningen som standard inte mellan HANA Large Instance-klienter i två olika regioner. Global Reach öppnar dock kommunikationsvägen mellan de stora HANA-instansenheter som du har etablerat i två olika regioner. Det här användningsscenariot för ExpressRoute Global Reach gör det möjligt:

 - HANA SystemReplikering utan ytterligare proxyservrar eller brandväggar
 - Kopiera säkerhetskopior mellan HANA-enheter för stora instanser i två olika regioner för att utföra systemkopior eller systemuppdatering


![Virtuella nätverk som är anslutna till Azure Large Instance-stämplar i olika Azure-regioner](./media/hana-overview-architecture/image8-multiple-regions.png)

Figuren visar hur de olika virtuella nätverken i båda regionerna är anslutna till två olika ExpressRoute-kretsar som används för att ansluta till SAP HANA på Azure (Stora instanser) i båda Azure-regionerna (grå linjer). Orsaken till detta två korsanslutningar är att skydda mot ett avbrott i MSEEs på båda sidor. Kommunikationsflödet mellan de två virtuella nätverken i de två Azure-regionerna ska hanteras över den [globala peeringen](https://blogs.msdn.microsoft.com/azureedu/2018/04/24/how-to-setup-global-vnet-peering-in-azure/) av de två virtuella nätverken i de två olika regionerna (blå prickad linje). Den tjocka röda linjen beskriver ExpressRoute Global Reach-anslutningen, som gör att HANA-enheter för stora instanser av dina klienter i två olika regioner kan kommunicera med varandra. 

> [!IMPORTANT] 
> Om du har använt flera ExpressRoute-kretsar bör BGP-inställningar för as-sökväg och BGP-inställningar för lokal inställningar användas för att säkerställa korrekt routning av trafik.

**Nästa steg**
- Referera [till SAP HANA-lagringsarkitektur (stora instanser)](hana-storage-architecture.md)
