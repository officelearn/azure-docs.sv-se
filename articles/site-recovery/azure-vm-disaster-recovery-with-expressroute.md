---
title: Integrera Azure ExpressRoute Azure VM-haveriberedskap med Azure Site Recovery
description: Beskriver hur du konfigurerar haveriberedskap för virtuella Azure-datorer med Azure Site Recovery och Azure ExpressRoute
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: bf12a5b7850a56d945e1082be6c522c31738669c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954086"
---
# <a name="integrate-expressroute-with-disaster-recovery-for-azure-vms"></a>Integrera ExpressRoute med haveriberedskap för virtuella Azure-datorer


I den här artikeln beskrivs hur du integrerar Azure ExpressRoute med [Azure Site Recovery](site-recovery-overview.md)när du konfigurerar haveriberedskap för virtuella Azure-datorer till en sekundär Azure-region.

Site Recovery möjliggör haveriberedskap av Virtuella Azure-datorer genom att replikera Azure VM-data till Azure.

- Om Virtuella Azure-datorer använder [Azure-hanterade diskar](../virtual-machines/windows/managed-disks-overview.md)replikeras VM-data till en replikerad hanterad disk i den sekundära regionen.
- Om virtuella Azure-datorer inte använder hanterade diskar replikeras VM-data till ett Azure-lagringskonto.
- Replikeringsslutpunkter är offentliga, men replikeringstrafik för virtuella Azure-datorer korsar inte internet.

Med ExpressRoute kan du utöka lokala nätverk till Microsoft Azure-molnet via en privat anslutning, med hjälp av en anslutningsleverantör. Om du har Konfigurerat ExpressRoute integreras den med Site Recovery enligt följande:

- **Under replikering mellan Azure-regioner**: Replikeringstrafik för Azure VM-haveriberedskap är endast inom Azure och ExpressRoute behövs inte eller används för replikering. Men om du ansluter från en lokal plats till virtuella Azure-datorer på den primära Azure-platsen finns det ett antal problem att vara medveten om när du konfigurerar haveriberedskap för de virtuella Azure-datorerna.
- **Redundans mellan Azure-regioner**: När avbrott inträffar växlar du över virtuella Azure-datorer från den primära till sekundära Azure-regionen. När du har misslyckats med en sekundär region finns det ett antal steg att vidta för att komma åt virtuella Azure-datorer i den sekundära regionen med ExpressRoute.


## <a name="before-you-begin"></a>Innan du börjar

Innan du börjar måste du förstå följande begrepp:

- ExpressRoute [kretsar](../expressroute/expressroute-circuit-peerings.md)
- [Routningsdomäner](../expressroute/expressroute-circuit-peerings.md#routingdomains) i ExpressRoute
- ExpressRoute [platser](../expressroute/expressroute-locations.md).
- Azure [VM-replikeringsarkitektur](azure-to-azure-architecture.md)
- Konfigurera [replikering](azure-to-azure-tutorial-enable-replication.md) för virtuella Azure-datorer.
- Så här [växlar du över](azure-to-azure-tutorial-failover-failback.md) virtuella Azure-datorer.


## <a name="general-recommendations"></a>Allmänna rekommendationer

För bästa praxis och för att säkerställa effektiva återställningstidsmål (RTOs) för haveriberedskap rekommenderar vi att du gör följande när du konfigurerar Site Recovery för att integrera med ExpressRoute:

- Etablera nätverkskomponenter före redundans till en sekundär region:
    - När du aktiverar replikering för virtuella Azure-datorer kan site recovery automatiskt distribuera nätverksresurser som nätverk, undernät och gateways i azure-regionen för mål, baserat på källnätverksinställningar.
    - Site Recovery kan inte automatiskt ställa in nätverksresurser som VNet-gateways.
    - Vi rekommenderar att du etablerar dessa ytterligare nätverksresurser före redundans. Ett litet driftstopp är associerat med den här distributionen och det kan påverka den totala återställningstiden om du inte tog hänsyn till den under distributionsplaneringen.
- Kör regelbundna haveriberedskapsövningar:
    - Testet verifierar din replikeringsstrategi utan dataförlust eller driftstopp och påverkar inte din produktionsmiljö. Det hjälper till att undvika konfigurationsproblem i sista minuten som kan påverka RTO negativt.
    - När du kör en testväxling för övningen rekommenderar vi att du använder ett separat Azure VM-nätverk i stället för det standardnätverk som konfigureras när du aktiverar replikering.
- Använd olika IP-adressutrymmen om du har en enda ExpressRoute-krets.
    - Vi rekommenderar att du använder ett annat IP-adressutrymme för det virtuella målnätverket. På så sätt undviker du problem vid upprättande av anslutningar vid regionala avbrott.
    - Om du inte kan använda ett separat adressutrymme måste du köra redundanstestet för haveriberedskap på ett separat testnätverk med olika IP-adresser. Du kan inte ansluta två virtuella nätverk med överlappande IP-adressutrymme till samma ExpressRoute-krets.

## <a name="replicate-azure-vms-when-using-expressroute"></a>Replikera virtuella Azure-datorer när du använder ExpressRoute


Om du vill konfigurera replikering för virtuella Azure-datorer på en primär plats och ansluter till dessa virtuella datorer från din lokala plats via ExpressRoute, behöver du göra följande:

1. [Aktivera replikering](azure-to-azure-tutorial-enable-replication.md) för varje Azure-virtuell dator.
2. Du kan också låta Site Recovery konfigurera nätverk:
    - När du konfigurerar och aktiverar replikering konfigurerar och aktiverar Site Recovery nätverk, undernät och gateway-undernät i Azure-regionen för mål, så att de matchar dem i källregionen. Site Recovery kartor också mellan källan och mål virtuella nätverk.
    - Om du inte vill att Site Recovery ska göra detta automatiskt skapar du nätverksresurserna på målsidan innan du aktiverar replikering.
3. Skapa andra nätverkselement:
    - Site Recovery skapar inte vägtabeller, VNet-gateways, VNet gateway-anslutningar, VNet-peering eller andra nätverksresurser och anslutningar i den sekundära regionen.
    - Du måste skapa dessa ytterligare nätverkselement i den sekundära regionen, när som helst innan du kör en redundans från den primära regionen.
    - Du kan använda [återställningsplaner](site-recovery-create-recovery-plans.md) och automatiseringsskript för att konfigurera och ansluta dessa nätverksresurser.
1. Om du har distribuerat en virtuell nätverksinstallation (NVA) för att styra flödet av nätverkstrafik bör du tänka på följande:
    - Azures standardsystemväg för Azure VM-replikering är 0.0.0.0/0.
    - Vanligtvis definierar NVA-distributioner också en standardväg (0.0.0.0/0) som tvingar utgående Internettrafik att flöda genom NVA. Standardvägen används när ingen annan specifik vägkonfiguration kan hittas.
    - Om så är fallet kan NVA överbelastas om all replikeringstrafik passerar genom NVA.
    - Samma begränsning gäller även när du använder standardvägar för routning av all Azure VM-trafik till lokala distributioner.
    - I det här fallet rekommenderar vi att du [skapar en slutpunkt](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) för nätverkstjänst i ditt virtuella nätverk för Tjänsten Microsoft.Storage, så att replikeringstrafiken inte lämnar Azure-gränsen.

## <a name="replication-example"></a>Exempel på replikering

Vanligtvis har företagsdistributioner arbetsbelastningar som delas över flera Virtuella Azure-nätverk, med en central anslutningsnav för extern anslutning till Internet och till lokala webbplatser. Ett nav och ekertopologi används vanligtvis tillsammans med ExpressRoute.

![Lokalt-till-Azure med ExpressRoute före redundans](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

- **Region**. Appar distribueras i regionen Azure East Asia.
- **Talade virtuella nätverk**. Appar distribueras i två ekrar virtuella nätverk:
    - **Källa vNet1**: 10.1.0.0/24.
    - **Källa vNet2**: 10.2.0.0/24.
    - Varje eker virtuellt nätverk är ansluten till **Hub vNet**.
- **Hub vNet**. Det finns en hubb vNet **Source Hub vNet:** 10.10.10.0/24.
  - Detta nav vNet fungerar som gatekeeper.
  - All kommunikation mellan undernät går igenom det här navet.
    - **Hub vNet-undernät**. Navet vNet har två undernät:
    - **NVA-undernät:** 10.10.10.0/25. Det här undernätet innehåller en NVA (10.10.10.10).
    - **Gateway undernät:** 10.10.10.128/25. Det här undernätet innehåller en ExpressRoute-gateway som är ansluten till en ExpressRoute-anslutning som dirigeras till den lokala platsen via en privat peering-routningsdomän.
- Det lokala datacentret har en ExpressRoute-kretsanslutning via en partnerkant i Hong Kong.
- All routning styrs via Azure-vägtabeller (UDR).
- All utgående trafik mellan virtuella nätverk eller till det lokala datacentret dirigeras via NVA.

### <a name="hub-and-spoke-peering-settings"></a>Inställningar för hubb- och eker peering

#### <a name="spoke-to-hub"></a>Spoke till hub

**Riktning** | **Inställning** | **Status**
--- | --- | ---
Spoke till hub | Tillåt virtuell nätverksadress | Enabled
Spoke till hub | Tillåt vidarebefordrad trafik | Enabled
Spoke till hub | Tillåt gatewaytransit | Disabled
Spoke till hub | Använda ta bort gateways | Enabled

 ![Konfiguration av peer-konfiguration för eektalt vid hubb](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

#### <a name="hub-to-spoke"></a>Hub till spoke

**Riktning** | **Inställning** | **Status**
--- | --- | ---
Hub till spoke | Tillåt virtuell nätverksadress | Enabled
Hub till spoke | Tillåt vidarebefordrad trafik | Enabled
Hub till spoke | Tillåt gatewaytransit | Enabled
Hub till spoke | Använda ta bort gateways | Disabled

 ![Hubb till eker peering-konfiguration](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="example-steps"></a>Exempelsteg

I vårt exempel bör följande inträffa när replikering för virtuella Azure-datorer aktiveras i källnätverket:

1. Du [aktiverar replikering](azure-to-azure-tutorial-enable-replication.md) för en virtuell dator.
2. Site Recovery skapar repliknät, undernät och gateway-undernät i målregionen.
3. Site Recovery skapar mappningar mellan källnätverken och replikmålsnätverken som skapas.
4. Du skapar manuellt virtuella nätverksgatewayer, virtuella nätverksgatewayanslutningar, virtuell nätverks peering eller andra nätverksresurser eller anslutningar.


## <a name="fail-over-azure-vms-when-using-expressroute"></a>Växla över virtuella Azure-datorer när du använder ExpressRoute

När du har misslyckats med virtuella Azure-datorer till azure-regionen för målet med hjälp av Site Recovery kan du komma åt dem med Hjälp av [ExpressRoute-privat peering](../expressroute/expressroute-circuit-peerings.md#privatepeering).

- Du måste ansluta ExpressRoute till målvnet med en ny anslutning. Den befintliga ExpressRoute-anslutningen överförs inte automatiskt.
- Hur du ställer in din ExpressRoute-anslutning till målvnet beror på din ExpressRoute-topologi.


### <a name="access-with-two-circuits"></a>Åtkomst med två kretsar

#### <a name="two-circuits-with-two-peering-locations"></a>Två kretsar med två peering platser

Den här konfigurationen skyddar ExpressRoute-kretsar mot regional katastrof. Om din primära peering-plats går ner kan anslutningarna fortsätta från den andra platsen.

- Kretsen som är ansluten till produktionsmiljön är vanligtvis den primära. Den sekundära kretsen har vanligtvis lägre bandbredd, vilket kan ökas om en katastrof inträffar.
- Efter redundans kan du upprätta anslutningar från den sekundära ExpressRoute-kretsen till målvnet. Alternativt kan du ha anslutningar inställda och redo i händelse av katastrof, för att minska den totala återställningstiden.
- Med samtidiga anslutningar till både primära och målbaserade virtuella nätverk, se till att din lokala routning endast använder den sekundära kretsen och anslutningen efter redundans.
- Käll- och målvnäten kan ta emot nya IP-adresser, eller behålla samma, efter redundans. I båda fallen kan de sekundära anslutningarna upprättas före redundans.


#### <a name="two-circuits-with-single-peering-location"></a>Två kretsar med en enda peering-plats

Den här konfigurationen hjälper till att skydda mot fel på den primära ExpressRoute-kretsen, men inte om den enda ExpressRoute-peering-platsen går ner, vilket påverkar båda kretsarna.

- Du kan ha samtidiga anslutningar från det lokala datacentret till källa vNEt med den primära kretsen och till målvnet med sekundärkretsen.
- Med samtidiga anslutningar till primär och mål, se till att lokal routning endast använder sekundär krets och anslutning efter redundans.
-   Du kan inte ansluta båda kretsarna till samma virtuella nätverk när kretsar skapas på samma peering-plats.

### <a name="access-with-a-single-circuit"></a>Åtkomst med en enda krets

I den här konfigurationen finns det bara en Expressroute-krets. Även om kretsen har en redundant anslutning om en går ner, kommer en enda ruttkrets inte att ge motståndskraft om din peering-region går ner. Tänk på följande:

- Du kan replikera virtuella Azure-datorer till alla Azure-regioner på [samma geografiska plats](azure-to-azure-support-matrix.md#region-support). Om Azure-regionen för målet inte är på samma plats som källan måste du aktivera ExpressRoute Premium om du använder en enda ExpressRoute-krets. Läs mer om [ExpressRoute-platser](../expressroute/expressroute-locations.md) och [ExpressRoute-priser](https://azure.microsoft.com/pricing/details/expressroute/).
- Du kan inte ansluta käll- och målvnät samtidigt till kretsen om samma IP-adressutrymme används i målregionen. I det här scenariot:    
    -  Koppla från källsidans anslutning och upprätta sedan målsidans anslutning. Den här anslutningsändringen kan skriptas som en del av en återställningsplan för webbplatsåterställning. Tänk på följande:
        - Om den primära regionen är otillgänglig kan det misslyckas med frånkopplingsåtgärden om den primära regionen är otillgänglig. Detta kan påverka skapandet av anslutning till målregionen.
        - Om du skapade anslutningen i målregionen och den primära regionen återställs senare kan det uppstå paketdroppar om två samtidiga anslutningar försöker ansluta till samma adressutrymme.
        - För att förhindra detta, avsluta den primära anslutningen omedelbart.
        - Efter att den virtuella datorn har misslyckats med den primära regionen kan den primära anslutningen upprättas igen, när du har kopplat från den sekundära anslutningen.
-   Om ett annat adressutrymmen används på målvnet kan du samtidigt ansluta till käll- och målvnäten från samma ExpressRoute-krets.


## <a name="failover-example"></a>Exempel på redundans

I vårt exempel använder vi följande topologi:

- Två olika ExpressRoute-kretsar på två olika peering-platser.
- Behåll privata IP-adresser för virtuella Azure-datorer efter redundans.
- Målåterställningsregionen är Azure SouthEast Asia.
- En sekundär ExpressRoute-kretsanslutning upprättas via en partnerkant i Singapore.

För en enkel topologi som använder en enda ExpressRoute-krets, med samma IP-adress efter redundans, [granska den här artikeln](site-recovery-retain-ip-azure-vm-failover.md#hybrid-resources-full-failover).

### <a name="example-steps"></a>Exempelsteg
Om du vill automatisera återställningen i det här exemplet behöver du göra så här:

1. Följ stegen för att ställa in replikering.
2. [Redundans över Azure-virtuella datorer](azure-to-azure-tutorial-failover-failback.md), med dessa ytterligare steg under eller efter redundansen.

    a. Skapa Azure ExpressRoute Gateway i målområdeshubben VNet. Detta är behovet av att ansluta målhubben vNet till ExpressRoute-kretsen.

    b. Skapa anslutningen från målhubben vNet till målet ExpressRoute-kretsen.

    c. Ställ in VNet-peerings mellan målregionens nav och eker virtuella nätverk. Peering-egenskaperna för målregionen kommer att vara desamma som i källregionen.

    d. Ställ in UDR:erna i hubnet VNet och de två eker-virtuella nätverken.

    - Egenskaperna för UDR:erna på målsidan är desamma som på källsidan när du använder samma IP-adresser.
    - Med olika mål-IP-adresser bör UDR:erna ändras i enlighet med detta.


Ovanstående steg kan skript som en del av en [återställningsplan](site-recovery-create-recovery-plans.md). Beroende på kraven för programanslutning och återställningstid kan ovanstående steg också slutföras innan redundansen startas.

#### <a name="after-recovery"></a>Efter återhämtning

När du har återställt de virtuella datorerna och slutfört anslutningen är återställningsmiljön följande.

![Lokalt-till-Azure med ExpressRoute efter redundans](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)



## <a name="next-steps"></a>Nästa steg

Läs mer om hur du använder [återställningsplaner](site-recovery-create-recovery-plans.md) för att automatisera appundans.
