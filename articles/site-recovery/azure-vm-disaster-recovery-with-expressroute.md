---
title: Med hjälp av ExpressRoute med haveriberedskap i Azure-dator | Microsoft Docs
description: Beskriver hur du använder Azure ExpressRoute med Azure Site Recovery för katastrofåterställning i Azure-dator
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: manayar
ms.openlocfilehash: 73514b524f554affb9730ba63ccd608491497af2
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2018
ms.locfileid: "37920478"
---
# <a name="using-expressroute-with-azure-virtual-machine-disaster-recovery"></a>Med hjälp av ExpressRoute med haveriberedskap i Azure-dator

Microsoft Azure ExpressRoute låter dig utöka ditt lokala nätverk till Microsoft-molnet över en privat anslutning med hjälp av en anslutningsprovider. Den här artikeln beskrivs hur du kan använda ExpressRoute med Site Recovery för haveriberedskap för virtuella Azure-datorer.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar måste du kontrollera att du förstår:
-   ExpressRoute [kretsar](../expressroute/expressroute-circuit-peerings.md)
-   ExpressRoute [routningsdomäner](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains)
-   Azure-dator [replikeringsarkitektur](azure-to-azure-architecture.md)
-   [Hur du konfigurerar replikering](azure-to-azure-tutorial-enable-replication.md) för Azure-datorer
-   [Redundansväxla](azure-to-azure-tutorial-failover-failback.md) Azure-datorer

## <a name="expressroute-and-azure-virtual-machine-replication"></a>ExpressRoute- och Azure VM-replikering

När du skyddar Azure-datorer med Site Recovery replikeringsdata skickas till ett Azure Storage-konto eller en replik med hanterade diskar på mål-Azure-region beroende på om din Azure virtuella datorer använda [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md). Även om replikering slutpunkterna är offentliga sker replikeringstrafik för Virtuella Azure-replikering, som standard inte via Internet, oavsett vilket Azure-region det virtuella nätverket i källan finns i.

För haveriberedskap för virtuella Azure-datorn, eftersom replikeringsdata inte lämna Azure gräns, krävs ExpressRoute inte för replikering. När virtuella datorer som växlar över till mål-Azure-region, du kan komma åt dem med hjälp av [privat peering](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).

## <a name="replicating-azure-deployments"></a>Replikering av Azure-distributioner

En tidigare [artikeln](site-recovery-retain-ip-azure-vm-failover.md#on-premises-to-azure-connectivity), beskrivs en enkel installation med ett virtuellt Azure-nätverk som är anslutet till kunden, lokala datacenter via ExpressRoute. Vanliga företagsdistributioner har arbetsbelastningar som delas mellan flera virtuella Azure-nätverk och en central anslutning hubb upprättar extern anslutning, både Internet och lokala distributioner.

Det här exemplet beskriver en topologi för NAV och ekrar, vilket är vanligt i enterprise-distributioner:
-   Distributionen är i den **Azure Östasien** region och datacenter på plats har en ExpressRoute-krets-anslutning via en partner edge i Hongkong.
-   Program distribueras över två virtuella ekernätverk – **källa VNet1** med adress utrymme 10.1.0.0/24 och **källa VNet2** med adress utrymme 10.2.0.0/24.
-   Det virtuella navnätverket, **källa Hubbnätverk**, med adressutrymme 10.10.10.0/24 fungerar som gatekeepern. All kommunikation över undernät går igenom hubben.
-   Det virtuella navnätverket har två undernät – **NVA-undernätet** med adress utrymme 10.10.10.0/25 och **Gatewayundernätet** med adress utrymme 10.10.10.128/25.
-   Den **NVA-undernätet** har en virtuell nätverksinstallation med IP-adressen 10.10.10.10.
-   Den **Gatewayundernätet** har en ExpressRoute-gateway är anslutna till en ExpressRoute-anslutning som dirigerar till kunden, lokala datacenter via en privat Peering routningsdomän.
-   Varje eker virtuella nätverket är anslutet till det virtuella navnätverket och alla routning i det här nätverkets topologi styrs via Azure vägen tabeller (UDR). All utgående trafik från ett virtuellt nätverk till det andra virtuella nätverket eller till det lokala datacentret dirigeras via NVA.

![På-lokalt till Azure med ExpressRoute före redundans](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

### <a name="hub-and-spoke-peering"></a>NAV och eker-peering

Eker till hubb peering har följande konfiguration:
-   Tillåt virtuell nätverksadress: aktiverat
-   Tillåt vidarebefordrad trafik: aktiverat
-   Tillåt gatewayöverföring: inaktiverad
-   Använd ta bort gateways: aktiverat

 ![Ekrar till hubben peering-konfigurationen](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

NAV och ekrar peering har följande konfiguration:
-   Tillåt virtuell nätverksadress: aktiverat
-   Tillåt vidarebefordrad trafik: aktiverat
-   Tillåt gatewayöverföring: aktiverat
-   Använd ta bort gateways: inaktiverad

 ![NAV och ekrar peering-konfigurationen](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="enabling-replication-for-the-deployment"></a>Aktiverar replikering för distributionen

För inställningarna ovan första [Ställ in katastrofåterställning](azure-to-azure-tutorial-enable-replication.md) för varje virtuell dator med hjälp av Site Recovery. Site Recovery kan skapa repliken virtuella nätverk (inklusive undernät och gateway-undernät) på målregion och skapa de nödvändiga mappningarna mellan käll- och virtuella nätverk. Du kan också skapa mål på serversidan nätverk och undernät och använder samma vid aktivering av replikering.

Site Recovery replikerar inte routningstabeller virtuella nätverksgatewayer, gateway-anslutningar från virtuellt nätverk, virtuell nätverkspeering eller några andra nätverk resurser eller anslutningar. Dessa och andra resurser som är inte en del av den [replikeringsprocessen](azure-to-azure-architecture.md#replication-process) måste vara under eller före redundans och ansluter dem till relevanta resurser. Du kan använda Azure Site Recoverys kraftfulla [återställningsplaner](site-recovery-create-recovery-plans.md) att automatisera skapa och ansluta ytterligare resurser med hjälp av automatiserade skript.

Som standard lämnar replikeringstrafiken inte Azure gränsen. NVA-distributioner vanligtvis definiera också en standardväg (0.0.0.0/0) som tvingar utgående Internet-trafiken kan flöda via NVA. I det här fallet kan installationen begränsas om all replikeringstrafik som passerar genom NVA. Detsamma gäller också när du använder standardvägar för routning av alla Virtuella Azure-trafik till lokala distributioner. Vi rekommenderar att [skapar en tjänstslutpunkt för virtuellt nätverk](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) i ditt virtuella nätverk för ”Storage” så att replikeringstrafiken inte lämnar Azure-gränsen.

## <a name="failover-models-with-expressroute"></a>Redundans modeller med ExpressRoute

När virtuella datorer i Azure växlas över till en annan region, överförs den befintliga ExpressRoute-anslutningen till det virtuella nätverket i källan inte automatiskt till målets virtuella nätverk på regionen som recovery. En ny anslutning krävs för att ansluta ExpressRoute till målets virtuella nätverk.

Du kan replikera virtuella Azure-datorer till valfri Azure-region inom samma geografiska klustret enligt anvisningarna [här](azure-to-azure-support-matrix.md#region-support). Om den valda Azure-målregion inte är inom samma geopolitiska region som källa, måste du aktivera ExpressRoute Premium om du använder en enda ExpressRoute-krets för källa och mål-region-anslutning. Mer information hittar du [ExpressRoute-platser](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) och [prisinformation för ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

### <a name="two-expressroute-circuits-in-two-different-expressroute-peering-locations"></a>Två ExpressRoute-kretsar i två olika ExpressRoute-peeringplatser
-   Den här konfigurationen är användbart om du vill försäkra mot fel på den primära ExpressRoute-kretsen och mot storskaliga regionala problem som kan också påverka ExpressRoute-peeringplatser och störa primära ExpressRoute-kretsen.
-   Kretsen är anslutna till produktionsmiljön används normalt som den primära kretsen och den sekundära kretsen är en felsäker och vanligtvis med begränsad bandbredd. På sekundärt bandbredd kan ökas i ett haveri när sekundärt måste ta över som primärt.
-   Med den här konfigurationen kan du upprätta anslutningar från sekundära ExpressRoute-kretsen till målets virtuella nätverk efter redundans eller har anslutningar etablerade och redo för en katastrofåterställning deklaration, minska övergripande återställningstiden. Se till att dina lokala Routning använder den sekundära kretsen och anslutningen bara efter en redundansväxling med samtidiga anslutningar till både primära servern och virtuella nätverk för mål-region.
-   Käll- och virtuella nätverk för virtuella datorer som skyddas med Site Recovery kan ha samma eller olika IP-adresser vid en redundansväxling per dina behov. I båda fallen kan sekundära anslutningar upprättas före redundans.

### <a name="two-expressroute-circuits-in-the-same-expressroute-peering-location"></a>Två ExpressRoute-kretsar på samma plats för ExpressRoute-peering
-   Med den här konfigurationen måste du vara säker på mot fel på den primära ExpressRoute-kretsen, men inte mot storskaliga regionala problem, vilket kan påverka ExpressRoute-peeringplatser. Med denna kan både primära och sekundära kretsar påverkas av den.
-   Övriga villkor för IP-adresser och anslutningar är desamma som i tidigare fallet. Du kan ha samtidiga anslutningar från lokala datacenter till källan virtuella nätverket med den primära kretsen och målets virtuella nätverk med den sekundära kretsen. Se till att dina lokala Routning använder den sekundära kretsen och anslutningen bara efter en redundansväxling med samtidiga anslutningar till både primära servern och virtuella nätverk för mål-region.
-   Du kan inte ansluta både kretsar till samma virtuella nätverk när kretsar som skapas på samma plats för peering.

### <a name="single-expressroute-circuit"></a>Enda ExpressRoute-krets
-   Den här konfigurationen inte att se till mot storskaliga regionalt haveri, vilket kan påverka ExpressRoute-peering-plats.
-   Du kan inte ansluta källa och mål virtuella nätverk samtidigt till krets om du använder samma IP-adressutrymmet på målregion med en enda ExpressRoute-krets.
-   När samma IP-adressutrymme används i målregionen, sida källanslutning bör vara frånkopplad och mål på serversidan anslutningen upprättats därefter. Den här anslutningen ändringen kan skriptas som en del av en återställningsplan.
-   I ett regionala fel om den primära regionen är otillgänglig, misslyckas åtgärden koppla från. Sådana ett avbrott kan påverka skapa en anslutning till målregion när samma IP-adressutrymme används på målets virtuella nätverk.
-   Om anslutningen skapandet lyckas på måldatorn och primära regionen återställer senare, kan du möter paket släpper om två samtidiga anslutningar som försöker ansluta till samma-adressutrymme. För att förhindra förlorade meddelanden paket, bör den primära anslutningen avslutas direkt. Efter återställning efter fel för virtuella datorer till den primära regionen, den primära anslutningen går det återigen upprättas när du kopplar från den sekundära anslutningen.
-   Om olika adressutrymmen används på målets virtuella nätverk, sedan du samtidigt ansluta till källan och rikta in virtuella nätverk från samma ExpressRoute-krets.

## <a name="recovering-azure-deployments"></a>Återställa Azure-distributioner
Överväg att redundans-modell med två olika ExpressRoute-kretsar i två olika peering-platser och förvaring av privata IP-adresser för de skyddade Azure-datorerna. Målregion för återställning är Azure Sydostasien och sekundära ExpressRoute-kretsen finns en anslutning via en partner edge i Singapore.

För att automatisera återställning av hela distributionen, förutom replikering av virtuella datorer och virtuella nätverk, måste andra relevanta nätverksresurser och anslutningar också skapas. För tidigare NAV och ekrar nätverkstopologi följande ytterligare steg måste utföras under eller efter den [redundans](azure-to-azure-tutorial-failover-failback.md) igen:
1.  Skapa Azure ExpressRoute-Gateway i mål-region hubbens virtuella nätverk. ExpressRoute-Gateway krävs för att ansluta det virtuella navnätverket för målet till ExpressRoute-kretsen.
2.  Skapa virtuell nätverksanslutning från det virtuella navnätverket för målet till målet ExpressRoute-krets.
3.  Konfigurera VNet-peering mellan målregion NAV och eker-nätverk. Peering-egenskaperna i målregionen att vara samma som de på källregionen.
4.  Konfigurera udr: er i det virtuella hubbnätverket och de två virtuella ekernätverken. Egenskaperna målsidan udr: er är samma som på käll-sida när med samma IP-adresser. Med annat mål-IP-adresser, ska sedan udr-vägarna anpassas.

Stegen ovan kan skriptas som en del av en [återställningsplanen](site-recovery-create-recovery-plans.md). Beroende på program-anslutning och krav för tid, kan ovanstående steg utföras innan du startar redundans.

Efter återställning av virtuella datorer och slutförande av andra steg i anslutningen, Återställningsmiljö ser ut så här: ![på plats-till Azure med ExpressRoute efter redundans](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)

En enkel topologi som exempel för Azure VM-haveriberedskap med en enda ExpressRoute-krets med samma IP-adress på virtuella måldatorer, beskrivs [här](site-recovery-retain-ip-azure-vm-failover.md#on-premises-to-azure-connectivity).

## <a name="recovery-time-objective-rto-considerations"></a>Mål för återställningstid (RTO) överväganden
För att minska den totala tiden för återställning för din distribution, rekommenderar vi att etablera och distribuera ytterligare målregionen [nätverkskomponenter](azure-vm-disaster-recovery-with-expressroute.md#enabling-replication-for-the-deployment) , till exempel virtuella nätverksgatewayer i förväg. Ett litet driftstopp är associerad med att distribuera ytterligare resurser och det här driftstoppet kan påverka den totala tiden för återställning, om inte utgjorde under planering.

Vi rekommenderar att du kör regular [programåterställningstest](azure-to-azure-tutorial-dr-drill.md) för skyddade distributioner. Visa verifierar din replikeringsstrategi utan dataförlust eller driftstopp och påverkar inte din produktionsmiljö. Kör en detaljerad undviker också sista konfigurationsproblem som kan påverka återställningstid negativt. Du rekommenderas att använda ett separat nätverk för virtuella Azure-datorn för att testa redundans, i stället för det standardnätverk som skapades när du aktiverade replikeringen.

Om du använder en enda ExpressRoute-krets, bör du använda en annan IP-adressutrymmet för det virtuella målnätverket för att undvika problem med anslutningen upprätta under regionala katastrofer. Om med olika IP-adresser inte är möjligt för produktionsmiljön återställda ska disaster recovery-test testa redundans göras på ett separat testnätverk med olika IP-adresser som du inte kan ansluta två virtuella nätverk med överlappande IP adressutrymme till samma ExpressRoute-krets.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [ExpressRoute-kretsar](../expressroute/expressroute-circuit-peerings.md).
- Läs mer om [ExpressRoute-routningsdomäner](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains).
- Läs mer om [ExpressRoute-platser](../expressroute/expressroute-locations.md).
- Läs mer om [återställningsplaner](site-recovery-create-recovery-plans.md) vill automatisera redundans i programmet.
