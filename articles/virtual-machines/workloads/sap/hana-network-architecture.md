---
title: Nätverks-arkitektur för SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Nätverksarkitektur att distribuera SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8794a93cecb50774f30746c22931db31a9fa9194
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66239576"
---
# <a name="sap-hana-large-instances-network-architecture"></a>Nätverksarkitektur för SAP HANA (stora instanser)

Arkitekturen i Azure network-tjänster är en viktig del av distributionen av SAP-program på stora HANA-instansen. SAP HANA på Azure (stora instanser) distributioner har vanligtvis en större SAP-landskap med flera olika SAP-lösningar med olika storlekar av databaser, resursförbrukning för CPU och minnesanvändning. Det är troligt att inte alla IT-system redan finns i Azure. Ditt SAP-landskap är ofta hybrid samt från en DBMS och SAP-program synvinkel med hjälp av en blandning av NetWeaver, och S/4HANA och SAP HANA och andra DBMS. Azure erbjuder olika tjänster som gör det möjligt att köra de olika DBMS, NetWeaver och S/4HANA-system i Azure. Azure även erbjudanden som du nätverks-teknik för att göra Azure se ut som ett virtuellt Datacenter till din lokala programvarudistributioner

Om inte din kompletta IT-system finns i Azure. Azure nätverksfunktioner används för att ansluta den lokala världen med dina Azure-resurser att hålla Azure se ut som ett virtuellt datacenter av alla storlekar. Funktionen Azure-nätverk som används är: 

- Azure-nätverk är anslutna till den [ExpressRoute](https://azure.microsoft.com/services/expressroute/) krets som ansluter till ditt lokala nätverk resurser.
- En ExpressRoute-krets som ansluter lokala till Azure ska ha en minsta bandbredd på [1 Gbit/s eller högre](https://azure.microsoft.com/pricing/details/expressroute/). Den här minimal bandbredd kan tillräcklig bandbredd för överföring av data mellan lokala system och system som körs på virtuella datorer. Det gör också tillräcklig bandbredd för anslutning till Azure-system från en lokal användare.
- Alla SAP-system i Azure är inställda i virtuella nätverk som kommunicerar med varandra.
- Active Directory och DNS-finns lokalt har utökats till Azure via ExpressRoute från en lokal plats eller kör fullständig i Azure.

För specifika fall av integrera stora HANA-instanser i Azure data center nätverksresurserna, används Azure ExpressRoute-tekniken också


> [!NOTE] 
> Endast en Azure-prenumeration kan länkas till endast en klient i en stämpel för stora HANA-instansen i en specifik Azure-region. Däremot kan en enda HANA stora instanser stämpel klient länkas till endast en Azure-prenumeration. Det här kravet är konsekvent med andra fakturerbara objekt i Azure.

Om SAP HANA på Azure (stora instanser) distribueras i flera olika Azure-regioner, distribueras en separat klient i stämpeln stora HANA-instansen. Du kan köra både under samma Azure-prenumeration, så länge dessa instanser är en del av samma SAP-landskap. 

> [!IMPORTANT] 
> Endast Azure Resource Manager-distributionsmetoden stöds med SAP HANA på Azure (stora instanser).

 

## <a name="additional-virtual-network-information"></a>Information om ytterligare virtuella nätverk

Om du vill ansluta ett virtuellt nätverk till ExpressRoute, måste du skapa en Azure ExpressRoute-gateway. Mer information finns i [om Expressroute-gatewayer för ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

En Azure ExpressRoute-gateway används med ExpressRoute till en infrastruktur utanför Azure eller till en stämpel med stora Azure-instanser. Du kan ansluta Azure ExpressRoute-gatewayen till högst fyra olika ExpressRoute-kretsar som anslutningarna kommer från olika Microsoft enterprise-gränsroutrar. Mer information finns i [SAP HANA (stora instanser) infrastruktur och anslutningsmöjlighet på Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> Det maximala dataflöde som du kan uppnå med en ExpressRoute-gateway är 10 Gbit/s med hjälp av en ExpressRoute-anslutning. Kopiera filer mellan en virtuell dator som finns i ett virtuellt nätverk och ett system på plats (som en enda kopia dataström) inte uppnår fullt genomflöde av olika gateway SKU: er. Använda flera strömmar för att använda fullständig bandbredden för ExpressRoute-gatewayen. Eller kopierar olika filer i parallella strömmar av en enda fil.


## <a name="networking-architecture-for-hana-large-instance"></a>Nätverksarkitekturen för stora HANA-instansen
Nätverk arkitekturen för stora HANA-instansen kan delas upp i fyra olika delar:

- Lokala nätverk och ExpressRoute-anslutning till Azure. Den här delen är kundens domän och är ansluten till Azure via ExpressRoute. Den här Expressroute-kretsen är fullt betalda av dig som kund. Bandbredden som bör vara tillräckligt stor för att hantera nätverkstrafik mellan dina lokala resurser och Azure-region du ansluter mot. Se längst ned till höger i följande bild.
- Azure network-tjänster, som tidigare diskuteras med virtuella nätverk som behöver ExpressRoute-gatewayer som har lagts till igen. Den här delen är ett område där du behöver hitta lämpliga Designer för programkrav-, säkerhets- och efterlevnadskrav. Om du använder stora HANA-instansen är en annan aspekt att tänka på när det gäller hur många virtuella nätverk och Azure-gateway SKU: er att välja bland. Finns i det övre högra hörnet i bilden.
- Anslutningar för stora HANA-instansen via ExpressRoute-teknik till Azure. Den här delen distribueras och hanteras av Microsoft. Allt du behöver göra är att tillhandahålla vissa IP-adressintervall efter distributionen av dina tillgångar i stora HANA-instansen ansluta ExpressRoute-kretsen till de virtuella nätverken. Mer information finns i [SAP HANA (stora instanser) infrastruktur och anslutningsmöjlighet på Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Det tillkommer ingen extra avgift för dig som kund för anslutningen mellan Azure data center nätverksinfrastruktur och enheter för stora HANA-instansen.
- Nätverk inom stämpeln stora HANA-instansen, som är mest transparent för dig.

![Virtuella nätverk som anslutits till SAP HANA på Azure (stora instanser) och lokala](./media/hana-overview-architecture/image1-architecture.png)

Kravet att dina lokala resurser måste ansluta via ExpressRoute till Azure ändras inte eftersom du använder stora HANA-instansen. Kravet på att en eller flera virtuella nätverk som kör de virtuella datorerna, som är värd för programnivån som ansluter till HANA-instanser på stora HANA-instansen enheter, även ändras inte. 

Skillnaderna för SAP-distributioner i Azure är:

- De stora HANA-instansen enheterna för din kundklient är anslutna via en annan ExpressRoute-krets i ditt virtuella nätverk. För att avgränsa belastningstillstånd, dela lokalt till Azure-nätverk ExpressRoute-kretsar och kretsar mellan Azure-nätverk och HANA stora instanser inte samma routrar.
- Profilen arbetsbelastningen mellan SAP-programnivån och den stora HANA-instansen är av olika slag, med många små begäranden och toppar som data som överförs (resultatmängder) från SAP HANA i programlagret.
- Arkitektur för SAP-program är mer känsliga för fördröjningar i nätverket än vanliga scenarier där data överförs mellan lokala och Azure.
- Azure ExpressRoute-gateway har minst två ExpressRoute-anslutningar. En kanal som är ansluten från en lokal och en som är ansluten från stora HANA-instanser. Detta lämnar bara utrymme för en annan två ytterligare kretsar från olika msee att ansluta till ExpressRoute-Gateway. Den här begränsningen är oberoende av användningen av ExpressRoute snabb sökväg. Alla anslutna kretsar dela den maximala bandbredden för inkommande data för ExpressRoute-gatewayen.

Nätverksfördröjning uppstod mellan virtuella datorer och stora HANA-instansen enheter kan vara högre än en typisk VM-till-VM network fördröjningen. Beroende på Azure-region, de mätvärdena kan överstiga 0,7-ms-fördröjningen klassificeras som under genomsnittet i [SAP Obs! #1100926 – vanliga frågor och svar: Nätverksprestanda](https://launchpad.support.sap.com/#/notes/1100926/E). Beroende på Azure-Region och verktyg för att mäta Nätverksfördröjningen mellan en virtuell Azure-dator och stora HANA-instansen enhet, uppmätt svarstiden kan vara upp till och runt 2 millisekunder. Kunder kan dock distribuera SAP HANA-baserade produktion SAP-program på stora SAP HANA-instansen. Kontrollera noggrant testa dina affärsprocesser i Azure HANA stora instanser. En ny funktion som kallas ExpressRoute snabb sökväg, kan avsevärt minska Nätverksfördröjningen mellan stora HANA-instanser och programnivån virtuella datorer i Azure (se nedan). 

SKU är viktiga för att tillhandahålla deterministisk Nätverksfördröjningen mellan virtuella datorer och HANA stora instanser, val av ExpressRoute-gatewayen. Mönster för trafik mellan virtuella datorer och stora HANA-instansen kan utveckla små men hög belastning med begäranden och data volymer som ska överföras till skillnad från trafikmönster mellan lokala och virtuella datorer. För att hantera sådana toppar och rekommenderar vi användning av SKU för UltraPerformance-gateway. Användningen av SKU för UltraPerformance-gateway som en ExpressRotue-gateway är obligatoriskt för Type II-klassen för HANA stora instans SKU: er.

> [!IMPORTANT] 
> Med den övergripande nätverkstrafiken mellan SAP-programmet och databasen lager kan som bara HighPerformance eller UltraPerformance gateway SKU: er för virtuella nätverk stöds för att ansluta till SAP HANA på Azure (stora instanser). För HANA stora instans typ II-SKU: er stöds endast UltraPerformance gateway-SKU som en ExpressRoute-gateway. Undantag gäller när du använder ExpressRoute snabb sökväg (se nedan)

### <a name="expressroute-fast-path"></a>ExpressRoute snabb sökväg
Om du vill minska svarstiden, ExpressRoute snabb sökväg har introduceras och ut i maj 2019 för specifika anslutning av stora HANA-instanser till Azure-nätverk som är värdar för de virtuella datorerna SAP-program. Den stora skillnaden i lösningen som distribueras hittills är att dataflöden mellan virtuella datorer och HANA stora instanser inte dirigeras via ExpressRoute-gatewayen längre. I stället kommunicerar de virtuella datorerna som tilldelats i undernät för virtuella Azure-nätverket direkt med den dedikerade edge routern. 

> [!IMPORTANT] 
> ExpressRoute snabb sökväg-funktioner kräver att i undernät som kör de virtuella datorerna SAP-program finns i samma Azure-nätverk som har anslutit till stora HANA-instanser. Virtuella dator i Azure-nätverk som peer-kopplade med Azure-nätverket anslutet direkt till stora HANA-instansen-enheter som inte deltar i ExpressRoute snabb sökväg. Därmed vanliga NAV och ekrar virtuella nätverkstrafik, där ExpressRoute-kretsar ansluter mot en hubbens virtuella nätverk och virtuella nätverk som innehåller SAP-programnivån (ekrar) komma peer-kopplade, optimering av ExpressRoute-Fast Sökvägen fungerar inte. Utöver stöder ExpressRoute snabb sökväg inte användardefinierade regler för routning (UDR) idag. Mer information finns i [ExpressRoute virtuella nätverks-gateway och FastPath](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways). 


För mer information om hur du konfigurerar ExpressRoute snabb sökväg, läsa dokumentet [ansluta ett virtuellt nätverk till stora HANA-instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).    

> [!NOTE]
> En UltraPerformance ExpressRoute-gateway krävs för att ha ExpressRoute snabb sökväg fungerande


## <a name="single-sap-system"></a>Enkel SAP-system

Den lokala infrastrukturen som visats tidigare är anslutet via ExpressRoute i Azure. ExpressRoute-krets som ansluter till en Microsoft enterprise edge-routern (MSEE). Mer information finns i [teknisk översikt över ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). När flödet har upprättats, ansluter den till Azures stamnätverk.

> [!NOTE] 
> Kör SAP-landskap i Azure genom att ansluta till enterprise gränsroutern agerar på Azure-regioner i SAP-landskap. Stamps för stora HANA-instansen är anslutna via dedikerade enterprise edge-router-enheter att minimera Nätverksfördröjningen mellan virtuella datorer i Azure IaaS och stora HANA-instansen stämplar.

ExpressRoute-gateway för virtuella datorer som är värdar för instanser av SAP-programmet är ansluten till en ExpressRoute-krets som ansluter till lokala. Samma virtuella nätverk är ansluten till en separat enterprise gränsrouter som är dedikerad att ansluta till stämplar med stor instans. Med hjälp av ExpressRoute snabb sökväg, dataflödet från stora HANA-instanser till SAP-programnivån virtuella datorer dirigeras inte via ExpressRoute-gatewayen längre och minska nätverkets avrunda resans svarstid med den.

Det här systemet är ett enkelt exempel på en enda SAP-system. SAP-programnivån ligger i Azure. SAP HANA-databas som körs på SAP HANA på Azure (stora instanser). Antas att bandbredden för ExpressRoute-gateway för 2 Gbit/s eller 10 Gbit/s genomströmning inte representerar en flaskhals.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Flera SAP-system eller stora SAP-system

Om flera SAP-system eller stora SAP-system distribueras för att ansluta till SAP HANA på Azure (stora instanser) är kan dataflödet för ExpressRoute-gatewayen bli en flaskhals. Eller om du vill isolera produktions- och icke-produktionssystem i olika Azure-nätverk. I detta fall är att dela programmet lager i flera virtuella nätverk. Du kan också skapa ett särskilt virtuellt nätverk som ansluter till stora HANA-instansen i de fall som:

- Säkerhetskopieringar direkt från HANA-instanser i stora HANA-instansen till en virtuell dator i Azure som är värd för NFS-resurser.
- Kopiera stora säkerhetskopieringar eller andra filer från enheter för stora HANA-instansen till diskutrymme som hanteras i Azure.

Använd ett separat virtuellt nätverk till virtuella datorer som hanterar lagring för massöverföring av data mellan stora HANA-instanser och Azure-värd. Den här ordningen undviker effekterna av stora filer eller dataöverföring från stora HANA-instansen till Azure på ExpressRoute-gatewayen som fungerar på virtuella datorer som kör SAP-programnivån. 

För en mer skalbart nätverksarkitektur:

- Använda flera virtuella nätverk för en enda, större SAP-programnivån.
- Distribuera ett separat virtuellt nätverk för alla SAP-system som har distribuerats, jämfört med att kombinera dessa SAP-system i olika undernät under samma virtuella nätverk.

  En mer skalbart nätverk arkitektur för SAP HANA på Azure (stora instanser):

![Distribuera SAP-programnivån över flera virtuella nätverk](./media/hana-overview-architecture/image4-networking-architecture.png)

Beroende på de regler och begränsningar, som du vill tillämpa mellan olika virtuella nätverk som är värd för virtuella datorer i olika SAP-system, bör du peerkoppla de virtuella nätverk. Läs mer om virtuell nätverkspeering [peerkoppling av virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


## <a name="routing-in-azure"></a>Routning i Azure

Tre routning Nätverksöverväganden är viktiga för SAP HANA på Azure (stora instanser) i standarddistribution:

* SAP HANA på Azure (stora instanser) kan bara nås via virtuella Azure-datorer och den dedikerade ExpressRoute-anslutningen inte direkt från en lokal plats. Direkt åtkomst från en lokal plats till stora HANA-instansen-enheter som levereras av Microsoft, inte är möjligt omedelbart. Transitiv routning begränsningarna är på grund av den aktuella Azure-nätverksarkitektur som används för stora SAP HANA-instansen. Vissa administration-klienter och program som behöver direkt åtkomst, till exempel SAP lösning Manager som körs lokalt, kan inte ansluta till SAP HANA-databas. Undantag finns i avsnittet ”Direktroutning till stora HANA-instanser'.

* Om du har stora HANA-instansen enheter som distribueras i två olika Azure-regioner för haveriberedskap, samma tillfälliga routning begränsningar som tillämpats på. IP-adresser för en enhet för stora HANA-instansen i en region (till exempel USA, västra) har med andra ord inte dirigeras till en enhet för stora HANA-instansen som distribueras i en annan region (till exempel USA, östra). Den här begränsningen har oberoende av användningen av Azure-nätverk peering mellan regioner eller cross-anslutning i ExpressRoute-kretsar som ansluter enheter för stora HANA-instansen till virtuella nätverk. En grafisk representation finns i bild i avsnittet ”använda HANA stora instanser units i flera regioner”. Den här begränsningen som medföljde distribuerad arkitektur förbjuden omedelbar användningen av HANA System Replication som funktioner för haveriberedskap. Leta upp avsnittet ”Använd HANA stora instanser enheter i flera regioner, för de senaste ändringarna. 

* SAP HANA på Azure (stora instanser) enheter har en tilldelad IP-adress från servern IP-pool-adressintervall som du skickade när du begär distributionen stora HANA-instansen. Mer information finns i [SAP HANA (stora instanser) infrastruktur och anslutningsmöjlighet på Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Den här IP-adressen är tillgänglig via Azure-prenumerationer och -krets som ansluter Azure-nätverk till stora HANA-instanser. IP-adress som tilldelats av server IP-adressintervall i poolen tilldelas direkt till enheten för maskinvaran. Den har *inte* tilldelad via NAT längre, som var fallet i första distributionen av den här lösningen. 

### <a name="direct-routing-to-hana-large-instances"></a>Direktroutning till stora HANA-instanser
Som standard fungerar transitiv routning mellan enheter för stora HANA-instansen och en lokal eller mellan stora HANA-instansen routning som distribueras i två olika regioner inte. Det finns flera alternativ för att aktivera sådana en transitiv routning.

- En omvänd proxy att dirigera data till och från. Exempelvis F5 BIG-IP, NGINX med Traffic Manager som distribueras i Azure-nätverket som ansluter till HANA stora instanser och till lokala som en brandvägg/trafik i virtuella routning lösning.
- Med hjälp av [IPTables-reglerna](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) i en Linux-VM för att aktivera routning mellan lokala platser och stora HANA-instansen enheter, eller mellan stora HANA-instansen enheter i olika regioner. Den virtuella datorn som kör IPTables måste distribueras i Azure-nätverk som ansluter till HANA stora instanser och en lokal. Den virtuella datorn måste vara storlek, så, att nätverkets genomflöde för den virtuella datorn är tillräcklig för den förväntade nätverkstrafiken. För information om Virtuella nätverkets bandbredd, kontrollerar artikeln [storlekar för Linux-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json).
- [Azure-brandväggen](https://azure.microsoft.com/services/azure-firewall/) skulle vara en annan lösning för att dirigera trafik mellan lokala och stora HANA-instans-enheter. 

All trafik för de här lösningarna dirigeras via Azure-nätverk och därför trafiken kan begränsas dessutom genom mjuk apparater eller genom Azure Nätverkssäkerhetsgrupper, så som vissa IP-adresser eller IP-adressintervall från en lokal eller kan vara blockerad uttryckligen tillåts åtkomst till stora HANA-instanser. 

> [!NOTE]  
> Tänk på att implementering och stöd för anpassade lösningar som inbegriper från tredje part nätverksutrustning eller IPTables inte tillhandahålls av Microsoft. Du måste tillhandahålla support av leverantören av den komponent som används eller Integratorn. 

#### <a name="express-route-global-reach"></a>Express Route Global räckvidd
Microsoft introducerade en ny funktion som kallas [ExpressRoute Global räckvidd](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach). Global räckvidd kan användas för stora HANA-instanser i två situationer:

- Aktivera direktåtkomst från lokalt till dina enheter för stora HANA-instansen som distribueras i olika regioner
- Aktivera direkt kommunikation mellan dina enheter för stora HANA-instansen som distribueras i olika regioner


##### <a name="direct-access-from-on-premise"></a>Direktåtkomst till en lokal
I Azure-regioner där Global räckvidd är tillgängliga, kan du begära att aktivera funktionen Global räckvidd för din ExpressRoute-krets som ansluter dina lokala nätverk till Azure-nätverk som ansluter till dina enheter för stora HANA-instansen också. Det finns vissa kostnaden konsekvenser för den lokala sidan för din ExpressRoute-kretsen. Priser för finns i priserna för [globala nå tillägg](https://azure.microsoft.com/pricing/details/expressroute/). Det finns inga ytterligare kostnader för dig relaterade till den krets som ansluter enhet(er) för stora HANA-instansen till Azure. 

> [!IMPORTANT]  
> När det gäller med Global räckvidd för att aktivera direktåtkomst mellan dina enheter för stora HANA-instansen och både lokala resurser, nätverket data- och flödet är **inte dirigeras via Azure-nätverk**, men direkt mellan Microsofts Enterprise exchange-routrar. Därmed alla NSG eller ASG regler eller någon annan typ av brandvägg, NVA eller proxyserver som du distribuerade i ett Azure-nätverk är inte komma använts de senaste. **Om du använder ExpressRoute Global räckvidd för att aktivera direktåtkomst från lokalt till stora HANA-instans enheter begränsningar och behörigheter att komma åt enheter för stora HANA-instans måste definieras i brandväggar på en lokal sida** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>Ansluta HANA stora instanser i olika Azure-regioner
På samma sätt för ExpressRoute Global räckvidd kan användas för att ansluta den lokala till stora HANA-instansen enheter den att ansluta dra innehavare stora HANA-instansen som är distribuerade åt dig i två olika regioner. Isolering är ExpressRoute-kretsar som stora HANA-instansen klienterna använder för att ansluta till Azure i båda regionerna. Det finns inga ytterligare avgifter för att ansluta två stora HANA-instansen klienter som distribueras i två olika regioner. 

> [!IMPORTANT]  
> Dataflöde och styra flödet av nätverkstrafik mellan de olika HANA stora instanser klienter inte ska dirigeras via azure-nätverk. Därför använda du inte Azure-funktioner eller nva: er för att tillämpa begränsningar för kommunikation mellan två HANA stora instanser klienterna. 

Mer information om hur du hämtar ExpressRoute Global räckvidd aktiverat läsa dokumentet [ansluta ett virtuellt nätverk till stora HANA-instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).


## <a name="internet-connectivity-of-hana-large-instance"></a>Ansluten till Internet för stora HANA-instansen
Stora HANA-instansen har *inte* har direkt Internetanslutning. Den här begränsningen kan exempelvis begränsa möjligheten att registrera den OS-avbildningen direkt med OS-leverantören. Du kan behöva arbeta med din lokala hanteringsverktyg för SUSE Linux Enterprise Server-prenumeration-servern eller Red Hat Enterprise Linux prenumeration Manager.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Kryptering av data mellan virtuella datorer och stora HANA-instansen
Data som överförs mellan stora HANA-instansen och virtuella datorer krypteras inte. Endast för exchange mellan HANA DBMS på klientsidan och JDBC/ODBC-baserade program, kan du aktivera kryptering av trafik. Mer information finns i [den här dokumentationen av SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Använd stora HANA-instansen enheter i flera regioner

För att använda disaster recovery set ups som du behöver ha enheter som SHANA stor instans i flera Azure-regioner. Även med hjälp av Azure [Global Vnet-Peering] kan fungerar som standard transitiv routning inte mellan stora HANA-instansen klienter i två olika regioner. Dock öppnas Global räckvidd kommunikationsvägen mellan stora HANA-instansen-enheter som du har etablerat i två olika regioner. Det här scenariot för användning av ExpressRoute Global räckvidd kan:

 - HANA System Replication utan ytterligare Proxys eller brandväggar
 - Kopiera säkerhetskopior mellan enheter för stora HANA-instansen i två olika regioner ska kopieras eller systemuppdateringar


![Virtuella nätverk som anslutits till stämplar med stora Azure-instanser i olika Azure-regioner](./media/hana-overview-architecture/image8-multiple-regions.png)

Bilden visar hur olika virtuella nätverk i båda regionerna är anslutna till två olika ExpressRoute-kretsar som används för att ansluta till SAP HANA på Azure (stora instanser) i både Azure-regioner (grå linjer). Orsaken till det här mellan två anslutningar är att skydda mot ett avbrott i msee på endera sidan. Flödet för kommunikation mellan två virtuella nätverk i två Azure-regioner ska hanteras via den [global peering](https://blogs.msdn.microsoft.com/azureedu/2018/04/24/how-to-setup-global-vnet-peering-in-azure/) av de två virtuella nätverk i två olika regioner (blå streckade linjen). Tjock röd linje beskriver anslutningen ExpressRoute Global räckvidd, vilket gör att de stora HANA-instansen enheterna för klienterna i två olika regioner kommer att kommunicera med varandra. 

> [!IMPORTANT] 
> Om du använder flera ExpressRoute-kretsar, ska AS Path-prepending och lokala inställningar BGP-inställningar användas för att se till att routningen av trafik.

**Nästa steg**
- Se [lagringsarkitektur för SAP HANA (stora instanser)](hana-storage-architecture.md)