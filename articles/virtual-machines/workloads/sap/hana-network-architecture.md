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
ms.date: 09/04/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 724a91b6ba0be030a2281bce366e4378892df59b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835812"
---
# <a name="sap-hana-large-instances-network-architecture"></a>Nätverksarkitektur för SAP HANA (stora instanser)

Arkitekturen i Azure network-tjänster är en viktig del av distributionen av SAP-program på stora HANA-instansen. SAP HANA på Azure (stora instanser) distributioner har vanligtvis en större SAP-landskap med flera olika SAP-lösningar med olika storlekar av databaser, resursförbrukning för CPU och minnesanvändning. Det är troligt att inte alla dessa SAP-system baseras på SAP HANA. Ditt SAP-landskap är förmodligen en hybridlösning som använder:

- Distribuera SAP-system på plats. På grund av storlek, kan inte systemen för närvarande finnas i Azure. Ett exempel är en SAP ERP-system som körs på SQL Server (som databasen) och kräver mer resurser som CPU eller minne än vad som virtuella datorer i produktionsmiljön.
- Distribuera SAP HANA-baserade SAP system på plats.
- Distribuerade SAP-system i virtuella datorer. Dessa system kan vara utveckling, testning, sandbox-miljön, eller produktion-instanser för någon av de SAP NetWeaver-baserade program som kan distribuera i Azure (på VM: ar), baserat på användnings- och resursbehov. Dessa system kan också baseras på databaser som SQL Server. Mer information finns i [SAP Support Obs! #1928533 – SAP-program i Azure: Produkter och typer av Azure virtuella datorer stöds](https://launchpad.support.sap.com/#/notes/1928533/E). Och dessa system kan baseras på databaser som SAP HANA. Mer information finns i [IaaS-plattformar för SAP HANA-certifierade](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).
- Distribuera SAP-programservrar i Azure (på virtuella datorer) som utnyttjar SAP HANA på Azure (stora instanser) i Azure stora instanser stämplar.

En SAP-hybridlandskap med fyra eller flera olika distributionsscenarier är vanligt. Det finns också många kundärenden av fullständig SAP-landskap som körs i Azure. När virtuella datorer blir mer kraftfulla, ökar antalet kunder som flyttar alla sina SAP-lösningar på Azure.

Azure-nätverk i samband med SAP-system som distribueras i Azure är inte svårt. Den baseras på följande principer:

- Azure-nätverk måste vara ansluten till ExpressRoute-krets som ansluter till ett lokalt nätverk.
- En ExpressRoute-krets som ansluter vanligtvis lokalt till Azure bör ha en bandbredd 1 Gbit/s eller högre. Den här minimal bandbredd kan tillräcklig bandbredd för överföring av data mellan lokala system och system som körs på virtuella datorer. Det gör också tillräcklig bandbredd för anslutning till Azure-system från en lokal användare.
- Alla SAP-system i Azure måste konfigureras i virtuella nätverk kan kommunicera med varandra.
- Active Directory och DNS finns lokalt har utökats till Azure via ExpressRoute från en lokal plats.


> [!NOTE] 
> Från en fakturering synvinkel kan endast en Azure-prenumeration länkas till endast en klient i en stor instans stämpel i en specifik Azure-region. Däremot kan en enda stor instans stämpel klient länkas till endast en Azure-prenumeration. Det här kravet är konsekvent med andra fakturerbara objekt i Azure.

Om SAP HANA på Azure (stora instanser) distribueras i flera olika Azure-regioner, distribueras en separat klient i stämpeln stor instans. Du kan köra både under samma Azure-prenumeration, så länge dessa instanser är en del av samma SAP-landskap. 

> [!IMPORTANT] 
> Endast Azure Resource Manager-distribution stöds med SAP HANA på Azure (stora instanser).

 

## <a name="additional-virtual-network-information"></a>Information om ytterligare virtuella nätverk

Om du vill ansluta ett virtuellt nätverk till ExpressRoute, måste du skapa en Azure-gateway. Mer information finns i [om virtuella nätverksgatewayer för ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

En Azure-gatewayen kan användas med ExpressRoute till en infrastruktur utanför Azure eller till en stämpel med stora Azure-instanser. En Azure-gateway kan också användas för att ansluta mellan virtuella nätverk. Mer information finns i [Konfigurera anslutning till ett nätverk till nätverk för Resource Manager med hjälp av PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Du kan ansluta Azure-gatewayen till högst fyra olika ExpressRoute-anslutningar så länge anslutningarna kommer från olika Microsoft enterprise-gränsroutrar. Mer information finns i [SAP HANA (stora instanser) infrastruktur och anslutningsmöjlighet på Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> Dataflödet som tillhandahåller en Azure-gateway skiljer sig till båda användningsfall. Mer information finns i [om VPN Gateway](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Det maximala dataflöde som du kan uppnå med en virtuell nätverksgateway är 10 Gbit/s med hjälp av en ExpressRoute-anslutning. Kopiera filer mellan en virtuell dator som finns i ett virtuellt nätverk och ett system på plats (som en enda kopia dataström) inte uppnår fullt genomflöde av olika gateway SKU: er. Använda flera strömmar för att använda fullständig bandbredden för den virtuella nätverksgatewayen. Eller kopierar olika filer i parallella strömmar av en enda fil.


## <a name="networking-architecture-for-hana-large-instance"></a>Nätverksarkitekturen för stora HANA-instansen
Nätverk arkitekturen för stora HANA-instansen kan delas upp i fyra olika delar:

- Lokala nätverk och ExpressRoute-anslutning till Azure. Den här delen är kundens domän och är ansluten till Azure via ExpressRoute. Se längst ned till höger i följande bild.
- Azure network-tjänster, som tidigare diskuteras med virtuella nätverk som har gatewayer igen. Den här delen är ett område där du behöver hitta lämpliga Designer för programkrav-, säkerhets- och efterlevnadskrav. Om du använder stora HANA-instansen är en annan aspekt att tänka på när det gäller hur många virtuella nätverk och Azure-gateway SKU: er att välja bland. Finns i det övre högra hörnet i bilden.
- Anslutningar för stora HANA-instansen via ExpressRoute-teknik till Azure. Den här delen distribueras och hanteras av Microsoft. Allt du behöver göra är att tillhandahålla vissa IP-adressintervall efter distributionen av dina tillgångar i stora HANA-instansen ansluta ExpressRoute-kretsen till de virtuella nätverken. Mer information finns i [SAP HANA (stora instanser) infrastruktur och anslutningsmöjlighet på Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- Nätverk i stora HANA-instansen, som är mest transparent för dig.

![Virtuella nätverk som anslutits till SAP HANA på Azure (stora instanser) och lokala](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Kravet att dina lokala resurser måste ansluta via ExpressRoute till Azure ändras inte eftersom du använder stora HANA-instansen. Kravet på att en eller flera virtuella nätverk som kör de virtuella datorerna, som är värd för programnivån som ansluter till HANA-instanser på stora HANA-instansen enheter, även ändras inte. 

Skillnaderna för SAP-distributioner i Azure är:

- De stora HANA-instansen enheterna för din kundklient är anslutna via en annan ExpressRoute-krets i ditt virtuella nätverk. För att avgränsa belastningstillstånd, dela lokalt virtuella nätverk ExpressRoute länkar och länkarna mellan virtuella nätverk och stora HANA-instansen inte samma routrar.
- Profilen arbetsbelastningen mellan SAP-programnivån och den stora HANA-instansen är av olika slag, med många små begäranden och toppar som data som överförs (resultatmängder) från SAP HANA i programlagret.
- Arkitektur för SAP-program är mer känsliga för fördröjningar i nätverket än vanliga scenarier där data överförs mellan lokala och Azure.
- Den virtuella nätverksgatewayen har minst två ExpressRoute-anslutningar. Båda anslutningarna dela den maximala bandbredden för inkommande data för den virtuella nätverksgatewayen.

Nätverksfördröjning uppstod mellan virtuella datorer och stora HANA-instansen enheter kan vara högre än en typisk VM-till-VM network fördröjningen. Beroende på Azure-region, de mätvärdena kan överstiga 0,7-ms-fördröjningen klassificeras som under genomsnittet i [SAP Obs! #1100926 – vanliga frågor och svar: Nätverksprestanda](https://launchpad.support.sap.com/#/notes/1100926/E). Beroende på Azure-Region och verktyg för att mäta Nätverksfördröjningen mellan en virtuell Azure-dator och stora HANA-instansen enhet, uppmätt svarstiden kan vara upp till och runt 2 millisekunder. Kunder kan dock distribuera SAP HANA-baserade produktion SAP-program på stora SAP HANA-instansen. Kontrollera noggrant testa dina affärsprocesser i Azure HANA stora instanser.
 
För att ge deterministisk Nätverksfördröjningen mellan virtuella datorer och HANA stora instanser, val av den virtuella nätverksgatewayen SKU är viktigt. Mönster för trafik mellan virtuella datorer och stora HANA-instansen kan utveckla små men hög belastning med begäranden och data volymer som ska överföras till skillnad från trafikmönster mellan lokala och virtuella datorer. För att hantera sådana toppar och rekommenderar vi användning av SKU för UltraPerformance-gateway. Användningen av SKU för UltraPerformance-gateway som en virtuell nätverksgateway är obligatoriskt för Type II-klassen för HANA stora instans SKU: er.

> [!IMPORTANT] 
> Med den övergripande nätverkstrafiken mellan SAP-programmet och databasen lager kan som bara HighPerformance eller UltraPerformance gateway SKU: er för virtuella nätverk stöds för att ansluta till SAP HANA på Azure (stora instanser). För HANA stora instans typ II-SKU: er stöds endast UltraPerformance gateway-SKU som en virtuell nätverksgateway.



## <a name="single-sap-system"></a>Enkel SAP-system

Den lokala infrastrukturen som visats tidigare är anslutet via ExpressRoute i Azure. ExpressRoute-krets som ansluter till en enterprise edge-routern. Mer information finns i [teknisk översikt över ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). När flödet har upprättats, ansluter den till Azures stamnätverk och alla Azure-regioner är tillgängliga.

> [!NOTE] 
> Kör SAP-landskap i Azure genom att ansluta till enterprise gränsroutern agerar på Azure-regioner i SAP-landskap. Azure stora instanser stämplar är anslutna via dedikerade enterprise edge-router-enheter att minimera Nätverksfördröjningen mellan virtuella datorer i Azure IaaS och stor instans stämplar.

Den virtuella nätverksgatewayen för de virtuella datorerna som är värdar för instanser av SAP-programmet är anslutet till ExpressRoute-kretsen. Samma virtuella nätverk är ansluten till en separat enterprise gränsrouter som är dedikerad att ansluta till stämplar med stor instans.

Det här systemet är ett enkelt exempel på en enda SAP-system. SAP-programnivån ligger i Azure. SAP HANA-databas som körs på SAP HANA på Azure (stora instanser). Antas att gateway-bandbredd för virtuellt nätverk med 2 Gbit/s eller 10 Gbit/s genomströmning inte representerar en flaskhals.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Flera SAP-system eller stora SAP-system

Om flera SAP-system eller stora SAP-system distribueras för att ansluta till SAP HANA på Azure (stora instanser) är kan dataflödet för den virtuella nätverksgatewayen bli en flaskhals. I detta fall är att dela programmet lager i flera virtuella nätverk. Du kan också skapa ett särskilt virtuellt nätverk som ansluter till stora HANA-instansen i de fall som:

- Säkerhetskopieringar direkt från HANA-instanser i stora HANA-instansen till en virtuell dator i Azure som är värd för NFS-resurser.
- Kopiera stora säkerhetskopieringar eller andra filer från enheter för stora HANA-instansen till diskutrymme som hanteras i Azure.

Använd ett separat virtuellt nätverk till virtuella datorer som hanterar lagring-värd. Den här ordningen undviker effekterna av stora filer eller dataöverföring från stora HANA-instansen till Azure på den virtuella nätverksgatewayen som fungerar på virtuella datorer som kör SAP-programnivån. 

För en mer skalbart nätverksarkitektur:

- Använda flera virtuella nätverk för en enda, större SAP-programnivån.
- Distribuera ett separat virtuellt nätverk för alla SAP-system som har distribuerats, jämfört med att kombinera dessa SAP-system i olika undernät under samma virtuella nätverk.

  En mer skalbart nätverk arkitektur för SAP HANA på Azure (stora instanser):

![Distribuera SAP-programnivån över flera virtuella nätverk](./media/hana-overview-architecture/image4-networking-architecture.png)

Bilden visar SAP-programnivån eller komponenter, distribuerat över flera virtuella nätverk. Den här konfigurationen introducerade oundvikligt med elasticitet som uppstod vid kommunikation mellan program som körs i dessa virtuella nätverk. Standardvärdet är nätverkstrafik mellan virtuella datorer finns i olika virtuella nätverk vidarebefordra via enterprise-gränsroutrar i den här konfigurationen. Sättet att optimera och minska svarstiden i kommunikationen mellan två virtuella nätverk är att peerkoppla virtuella nätverk inom samma region. Den här metoden fungerar även om de virtuella nätverk finns i olika prenumerationer. Med vnet-peering, använda kommunikationen mellan virtuella datorer i två olika virtuella nätverk till Azure-stamnätverket kan kommunicera direkt med varandra. Svarstid visas som om de virtuella datorerna är i samma virtuella nätverk. Trafik som åtgärdar IP-adressintervall som är anslutna via Azure vnet-gatewayen dirigeras via den enskilda virtuella nätverksgatewayen för det virtuella nätverket. 

Läs mer om virtuell nätverkspeering [peerkoppling av virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


## <a name="routing-in-azure"></a>Routning i Azure

Tre routning Nätverksöverväganden är viktiga för SAP HANA på Azure (stora instanser):

* SAP HANA på Azure (stora instanser) kan bara nås via virtuella datorer och den dedikerade ExpressRoute-anslutningen inte direkt från en lokal plats. Direkt åtkomst från en lokal plats till stora HANA-instansen-enheter som levereras av Microsoft, inte är möjligt omedelbart. Transitiv routning begränsningarna är på grund av den aktuella Azure-nätverksarkitektur som används för stora SAP HANA-instansen. Vissa administration-klienter och program som behöver direkt åtkomst, till exempel SAP lösning Manager som körs lokalt, kan inte ansluta till SAP HANA-databas.

* Om du har stora HANA-instansen enheter som distribueras i två olika Azure-regioner för disaster recovery gäller samma tillfälliga routning begränsningar. IP-adresser för en enhet för stora HANA-instansen i en region (till exempel USA, västra) är med andra ord inte dirigeras till en enhet för stora HANA-instansen som distribueras i en annan region (till exempel USA, östra). Den här begränsningen är oberoende av användningen av Azure-nätverk peering mellan regioner eller cross-anslutning i ExpressRoute-kretsar som ansluter enheter för stora HANA-instansen till virtuella nätverk. En grafisk representation finns i bild i avsnittet ”använda HANA stora instanser units i flera regioner”. Den här begränsningen som medföljer distribuerad arkitektur förbjuder omedelbar användningen av HANA System Replication som funktioner för haveriberedskap.

* SAP HANA på Azure (stora instanser) enheter har en tilldelad IP-adress från servern IP-pool-adressintervall som du skickade. Mer information finns i [SAP HANA (stora instanser) infrastruktur och anslutningsmöjlighet på Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Den här IP-adressen är tillgänglig via Azure-prenumerationer och ExpressRoute som ansluter virtuella nätverk till HANA på Azure (stora instanser). IP-adress som tilldelats av server IP-adressintervall i poolen tilldelas direkt till enheten för maskinvaran. Den har *inte* tilldelad via NAT längre, som var fallet i första distributionen av den här lösningen. 

> [!NOTE]
> Om du vill lösa begränsningen i tillfälliga routning som beskrivs i de första två listobjekt, använder du ytterligare komponenter för routning. Komponenter som kan användas för att övervinna begränsningen kan vara:
> 
> * En omvänd proxy att dirigera data till och från. Exempelvis F5 BIG-IP, NGINX med Traffic Manager som distribueras i Azure som en brandvägg/trafik i virtuella routning lösning.
> * Med hjälp av [IPTables-reglerna](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) i en Linux-VM för att aktivera routning mellan lokala platser och stora HANA-instansen enheter, eller mellan stora HANA-instansen enheter i olika regioner.
> 
> Tänk på att implementering och stöd för anpassade lösningar som inbegriper från tredje part nätverksutrustning eller IPTables inte tillhandahålls av Microsoft. Du måste tillhandahålla support av leverantören av den komponent som används eller Integratorn. 

## <a name="internet-connectivity-of-hana-large-instance"></a>Ansluten till Internet för stora HANA-instansen
Stora HANA-instansen har *inte* har direkt Internetanslutning. Den här begränsningen kan exempelvis begränsa möjligheten att registrera den OS-avbildningen direkt med OS-leverantören. Du kan behöva arbeta med din lokala hanteringsverktyg för SUSE Linux Enterprise Server-prenumeration-servern eller Red Hat Enterprise Linux prenumeration Manager.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Kryptering av data mellan virtuella datorer och stora HANA-instansen
Data som överförs mellan stora HANA-instansen och virtuella datorer krypteras inte. Endast för exchange mellan HANA DBMS på klientsidan och JDBC/ODBC-baserade program, kan du aktivera kryptering av trafik. Mer information finns i [den här dokumentationen av SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Använd stora HANA-instansen enheter i flera regioner

Du kanske skälen till att distribuera SAP HANA på Azure (stora instanser) i flera Azure-regioner utom för katastrofåterställning. Du kanske vill ansluta till stora HANA-instansen från var och en av de virtuella datorerna distribueras i olika virtuella nätverk i regioner. IP-adresser som hör till olika stora HANA-instansen enheterna sprids inte utöver de virtuella nätverk som är anslutna direkt via deras gateway till instanserna. Därför kan introduceras en liten ändring i designen för virtuellt nätverk. En virtuell nätverksgateway kan hantera fyra olika ExpressRoute-kretsar från olika enterprise-gränsroutrar. Varje virtuellt nätverk som är ansluten till en av stämplarna som stora instanser kan anslutas till stor instans stämpel i en annan Azure-region.

![Virtuella nätverk som anslutits till stämplar med stora Azure-instanser i olika Azure-regioner](./media/hana-overview-architecture/image8-multiple-regions.png)

Bilden visar hur olika virtuella nätverk i båda regionerna är anslutna till två olika ExpressRoute-kretsar som används för att ansluta till SAP HANA på Azure (stora instanser) i både Azure-regioner. De nyligen införda anslutningarna är rektangulär röda linjer. Med de här anslutningarna utanför de virtuella nätverk, virtuella datorer som körs i något av dessa virtuella nätverk har åtkomst till var och en av de olika stora HANA-instansen enheter som distribueras i två regioner. Så som visas i bilden, förutsätts att du har två ExpressRoute-anslutningar från en lokal plats till två Azure-regioner. Den här ordningen rekommenderas för disaster recovery orsaker.

> [!IMPORTANT] 
> Om du använder flera ExpressRoute-kretsar, ska AS Path-prepending och lokala inställningar BGP-inställningar användas för att se till att routningen av trafik.

**Nästa steg**
- Se [lagringsarkitektur för SAP HANA (stora instanser)](hana-storage-architecture.md)