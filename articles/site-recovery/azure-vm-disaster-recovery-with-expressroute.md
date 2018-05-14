---
title: Använda ExpressRoute med virtuella Azure-datorn katastrofåterställning | Microsoft Docs
description: Beskriver hur du använder Azure ExpressRoute med Azure Site Recovery för virtuell dator i Azure-katastrofåterställning
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/11/2018
ms.author: manayar
ms.openlocfilehash: 44ecbcc51cb53f4d7b68f5c5e24e7d81c5a4208c
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="using-expressroute-with-azure-virtual-machine-disaster-recovery"></a>Använda ExpressRoute med virtuella Azure-datorn katastrofåterställning

Microsoft Azure ExpressRoute låter dig utöka ditt lokala nätverk till Microsoft-molnet över en privat anslutning med hjälp av en anslutningsprovider. Den här artikeln beskriver hur du kan använda ExpressRoute med Site Recovery för katastrofåterställning av Azure virtuella datorer.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör du kontrollera att du förstår:
-   ExpressRoute [kretsar](../expressroute/expressroute-circuit-peerings.md)
-   ExpressRoute [routningsdomäner](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains)
-   Virtuella Azure-datorn [replikeringsarkitektur](azure-to-azure-architecture.md)
-   [Konfigurera replikering](azure-to-azure-tutorial-enable-replication.md) för virtuella Azure-datorer
-   [Redundansväxla](azure-to-azure-tutorial-failover-failback.md) virtuella Azure-datorer

## <a name="expressroute-and-azure-virtual-machine-replication"></a>ExpressRoute- och Azure replikering av virtuella datorer

När du skyddar virtuella Azure-datorer med Site Recovery replikeringsdata skickas till ett Azure Storage-konto eller replik hanterade disken på målet Azure-region beroende på om din Azure virtuella datorer används [Azure hanterade diskar](../virtual-machines/windows/managed-disks-overview.md). Även om replikering slutpunkter är offentliga replikeringstrafik för Virtuella Azure-replikering som standard inte sker via Internet, oavsett vilket Azure-region som det virtuella källnätverket finns i.

För Virtuella Azure-katastrofåterställning, som replikeringsdata inte lämnar kolumnrubrikens Azure krävs ExpressRoute inte för replikering. När virtuella datorer växla över till målet Azure-region, du kan komma åt dem med hjälp av [privat peering](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).

## <a name="replicating-azure-deployments"></a>Replikering av Azure-distributioner

En tidigare [artikel](site-recovery-retain-ip-azure-vm-failover.md#on-premises-to-azure-connectivity), beskrivs en enkel installation med ett virtuellt Azure-nätverk som är anslutet till kunden lokala datacenter via ExpressRoute. Vanliga företagsdistributioner har arbetsbelastningarna som delas mellan flera virtuella Azure-nätverk och en central anslutningen hubb upprättar extern anslutning till Internet och för lokala distributioner.

Det här exemplet beskrivs en nav och eker-topologi, vilket är vanligt i enterprise-distributioner:
-   Distribution är i den **Azure Östasien** har en ExpressRoute-krets anslutning via en partner kant i Hongkong region och lokala datacenter.
-   Program distribueras mellan två ekrar virtuella nätverk – **källa VNet1** med adressutrymme 10.1.0.0/24 och **källa VNet2** med adressutrymme 10.2.0.0/24.
-   Det virtuella nätverket i hubben **källa hubb VNet**, med adressutrymme 10.10.10.0/24 fungerar som gatekeeper. All kommunikation över undernät genomgår hubben.
-   Det virtuella nätverket hubben har två undernät – **NVA undernät** med adressutrymme 10.10.10.0/25 och **Gatewayundernät** med adressutrymme 10.10.10.128/25.
-   Den **NVA undernät** har en virtuell nätverksenhet med IP-adressen 10.10.10.10.
-   Den **Gatewayundernät** en ExpressRoute-gateway är ansluten till en ExpressRoute-anslutning som omdirigerar till kunden lokala datacenter via en privat Peering routningsdomän.
-   Varje ekrar virtuella nätverket är anslutet till det virtuella nätverket hubb och alla routning i det här nätverkets topologi styrs via Azure väg tabeller (UDR). All utgående trafik från ett VNet till andra VNet eller till det lokala datacentret dirigeras via en NVA.

![På-lokal-till-Azure med ExpressRoute före redundans](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

### <a name="hub-and-spoke-peering"></a>NAV och eker-peering

Ekrar till hubben peering har följande konfiguration:
-   Tillåt virtuella nätverksadressen: aktiverat
-   Tillåt vidarebefordrad trafik: aktiverat
-   Tillåt gateway överföring: inaktiverad
-   Använd ta bort gateways: aktiverat

 ![Pinnar till hubben peering konfiguration](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

Hubben till pinnar peering har följande konfiguration:
-   Tillåt virtuella nätverksadressen: aktiverat
-   Tillåt vidarebefordrad trafik: aktiverat
-   Tillåt gateway överföring: aktiverat
-   Använd ta bort gateways: inaktiverad

 ![NAV och pinnar peering konfiguration](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="enabling-replication-for-the-deployment"></a>Aktivera replikering för distributionen

För inställningarna ovan första [konfigurera katastrofåterställning](azure-to-azure-tutorial-enable-replication.md) för varje virtuell dator med Site Recovery. Site Recovery kan skapa repliken virtuella nätverk (inklusive undernät och gateway-undernät) på mål-region och skapa de nödvändiga mappningarna mellan käll- och virtuella nätverk. Du kan också skapa mål på serversidan nätverk och undernät och använder samma vid replikering.

Site Recovery replikerar inte vägtabeller virtuella nätverks-gateway, gateway-anslutningar för virtuella nätverk, virtuellt nätverk peering eller några andra nätverk resurser eller anslutningar. Dessa och andra resurser som tillhör inte den [replikeringen](azure-to-azure-architecture.md#replication-process) behöver skapas under eller före redundans och ansluten till relevanta resurser. Du kan använda Azure Site Recovery kraftfulla [återställningsplaner](site-recovery-create-recovery-plans.md) att automatisera att skapa och koppla ytterligare resurser med hjälp av automatiseringsskript.

Som standard lämnar replikeringstrafiken inte Azure gränsen. Normalt definiera NVA distributioner också en standardväg (0.0.0.0/0) som tvingar utgående Internet-trafiken flöda via en NVA. I det här fallet kan anordningen hämta begränsas om replikeringstrafiken som passerar genom en NVA. Samma gäller även när du använder standardvägar för routning all trafik i Virtuella Azure till lokala distributioner. Vi rekommenderar [skapar ett virtuellt nätverk tjänstslutpunkten](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) i ditt virtuella nätverk för ”lagring” så att replikeringstrafiken inte lämnar Azure gräns.

## <a name="failover-models-with-expressroute"></a>Redundans modeller med ExpressRoute

När Azure virtuella datorer har redundansväxlats till en annan region överförs befintlig ExpressRoute-anslutningen till det virtuella nätverket källa inte automatiskt till det virtuella målnätverket på regionen som recovery. En ny anslutning krävs för att ansluta ExpressRoute till det virtuella målnätverket.

Du kan replikera virtuella Azure-datorer till någon Azure-region inom samma geografiska kluster som detaljerad [här](azure-to-azure-support-matrix.md#region-support). Om det valda målet Azure-regionen inte är inom samma geopolitiska region som källa, måste du aktivera ExpressRoute Premium om du använder en enda ExpressRoute-krets för källan och målet region-anslutning. Mer information [ExpressRoute platser](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) och [ExpressRoute priser](https://azure.microsoft.com/pricing/details/expressroute/).

### <a name="two-expressroute-circuits-in-two-different-expressroute-peering-locations"></a>Två ExpressRoute-kretsar på två olika ExpressRoute-peering platser
-   Den här konfigurationen är användbar om du vill försäkra mot fel på den primära ExpressRoute-kretsen och mot storskaliga regionala katastrofer som också kan påverka ExpressRoute-peering platser och störa din primära ExpressRoute-kretsen.
-   Normalt används kretsen ansluten till produktionsmiljön som kretsen primära och sekundära kretsen är en felsäker och vanligtvis av låg bandbredd. Bandbredden för sekundärt kan ökas i en katastrof händelse när sekundärt måste tar över som den primära servern.
-   Med den här konfigurationen kan du upprätta anslutningar från din sekundära ExpressRoute-krets till det virtuella målnätverket efter växling vid fel eller har anslutningar etablerade och redo för en katastrofåterställning deklaration, vilket minskar den totala tiden för återställning. Med samtidiga anslutningar till både primär och målregionen Kontrollera virtuella nätverk, att dina lokala Routning använder den sekundära krets och anslutningen efter växling vid fel.
-   Käll- och virtuella nätverk för virtuella datorer som skyddas med Site Recovery kan ha samma eller olika IP-adresser under växling vid fel per dina behov. I båda fallen kan sekundära anslutningar upprättas före redundans.

### <a name="two-expressroute-circuits-in-the-same-expressroute-peering-location"></a>Två ExpressRoute-kretsar på samma plats för ExpressRoute-peering
-   Du kan försäkra mot fel på den primära ExpressRoute-kretsen med den här konfigurationen, men inte mot storskaliga regionala katastrofer som kan det påverka ExpressRoute-peering platser. Med denna kan både primära och sekundära kretsar hämta påverkas.
-   Andra villkor för IP-adresser och anslutningar förblir desamma som i fallet med tidigare. Du kan ha samtidiga anslutningar från lokalt datacenter till källan virtuellt nätverk med den primära kretsen och till det virtuella målnätverket med sekundära krets. Med samtidiga anslutningar till både primär och målregionen Kontrollera virtuella nätverk, att dina lokala Routning använder den sekundära krets och anslutningen efter växling vid fel.
-   Du kan inte ansluta både kretsar till samma virtuella nätverk när kretsar skapas på samma plats för peering.

### <a name="single-expressroute-circuit"></a>Enkel ExpressRoute-krets
-   Den här konfigurationen inte försäkra mot storskaliga regionala katastrofåterställning, vilket kan påverka peeringplatsen för ExpressRoute.
-   Du kan inte ansluta källa och mål virtuella nätverk samtidigt till krets om du använder samma IP-adressutrymmet på mål-region med en enda ExpressRoute-krets.
-   När du använder samma IP-adressutrymmet på mål-region, den sida anslutningen till datakällan ska kopplas och mål på serversidan ansluta därefter. Ändra den här anslutningen kan skriptas som en del av en återställningsplan.
-   Ett regionala fel, om den primära regionen är inte tillgänglig, kan koppla från åtgärden misslyckas. Sådana avbrott kan påverka anslutning skapas till målet region när du använder samma IP-adressutrymme i målet virtuellt nätverk.
-   Om anslutningen skapandet lyckas på målet och primär region återställer senare, kan du står inför paket således om två samtidiga anslutningar som försöker ansluta till samma adressutrymme. För att förhindra paket fall bör den primära anslutningen avslutas omedelbart. Efter återställning av virtuella datorer till den primära regionen igen primära anslutning kan upprättas när du kopplar från den sekundära anslutningen.
-   Om olika adressutrymmen används på det virtuella målnätverket, sedan du samtidigt ansluta käll- och mål virtuella nätverk från samma ExpressRoute-kretsen.

## <a name="recovering-azure-deployments"></a>Återställning av Azure-distributioner
Överväg att redundans-modell med två olika ExpressRoute-kretsar i två olika peering platser och lagring av den privata IP-adresser för de skyddade virtuella Azure-datorerna. Återställning målregionen är Azure Sydostasien sekundära ExpressRoute-kretsen finns en anslutning via en partner kant i Singapore.

För att automatisera återställning av hela distributionen, förutom att replikera virtuella datorer och virtuella nätverk, måste andra relevanta nätverksresurser och anslutningar också skapas. För tidigare NAV och ekrar nätverkstopologi följande ytterligare steg som krävs under eller efter den [redundans](azure-to-azure-tutorial-failover-failback.md) igen:
1.  Skapa Azure ExpressRoute-Gateway i det virtuella nätverket för målet region hubb. ExpressRoute-Gateway krävs för att ansluta det virtuella nätverket för mål-hubb till ExpressRoute-kretsen.
2.  Skapa virtuell nätverksanslutning från det virtuella nätverket för mål-hubb till målet ExpressRoute-kretsen.
3.  Konfigurera VNet peerkopplingar mellan den målregionen NAV och ekrar virtuella nätverk. Peering egenskaperna på målregionen blir desamma som på den käll-regionen.
4.  Ställ in udr: er i hubben VNet och två ekrar Vnet. Egenskaperna för målsidan udr: er är desamma som på sidan källa när använder samma IP-adresser. Med ett annat mål-IP-adresser, ska de udr: er anpassas.

Ovanstående steg kan skriptas som en del av en [återställningsplanen](site-recovery-create-recovery-plans.md). Beroende på programmet anslutning och återställning tidskrav kan ovanstående steg utföras innan du startar växling vid fel.

Efter återställning av virtuella datorer och andra anslutningen steg har slutförts, Återställningsmiljön i ser ut som följer: ![på-lokal-till-Azure med ExpressRoute efter växling vid fel](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)

Ett exempel på enkla topologi för Virtuella Azure-katastrofåterställning med enda ExpressRoute-kretsen med samma IP-adress på virtuella måldatorer detaljerad [här](site-recovery-retain-ip-azure-vm-failover.md#on-premises-to-azure-connectivity).

## <a name="recovery-time-objective-rto-considerations"></a>Tid mål för Återställningstid överväganden
För att minska den totala tiden för återställning för din distribution rekommenderar vi etablering och distribuera ytterligare målregionen [nätverkskomponenter](azure-vm-disaster-recovery-with-expressroute.md#enabling-replication-for-the-deployment) , till exempel virtuella nätverks-gateway i förväg. Ett litet driftstopp är associerad med att distribuera ytterligare resurser och den här driftstörningen kan påverka den totala tiden för återställning, om inte med i beräkningen när du planerar.

Vi rekommenderar att du kör regular [haveriberedskap](azure-to-azure-tutorial-dr-drill.md) för skyddade distributioner. Visa verifierar din replikeringsstrategi för utan dataförlust eller driftstopp och påverkar inte din produktionsmiljö. Kör en detaljerad undviker du också sista konfigurationsproblem som kan påverka mål negativt. Vi rekommenderar ett separat nätverk för virtuella Azure-datorn för att testa redundans, istället för standardnätverk som har ställts in när du har aktiverat replikering.

Om du använder en enda ExpressRoute-krets, bör du använda en annan IP-adressutrymmet för det virtuella målnätverket för att undvika problem med anslutningen anläggning under regionala katastrofer. Om med olika IP-adresser inte är möjligt för produktionsmiljön återställda ska disaster recovery ökad testa redundans göras på ett separat testnätverk med olika IP-adresser som du inte kan ansluta två virtuella nätverk med överlappande IP adressutrymme till samma ExpressRoute-kretsen.

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [ExpressRoute-kretsar](../expressroute/expressroute-circuit-peerings.md).
- Lär dig mer om [ExpressRoute routningsdomänerna](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains).
- Lär dig mer om [ExpressRoute platser](../expressroute/expressroute-locations.md).
- Lär dig mer om [återställningsplaner](site-recovery-create-recovery-plans.md) vill automatisera redundans för programmet.
