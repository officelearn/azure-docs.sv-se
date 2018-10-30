---
title: Integrera Azure ExpressRoute med haveriberedskap för virtuella Azure-datorer med hjälp av tjänsten Azure Site Recovery | Microsoft Docs
description: Beskriver hur du konfigurerar haveriberedskap för virtuella Azure-datorer med hjälp av Azure Site Recovery och Azure ExpressRoute
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: mayg
ms.openlocfilehash: e7c63fba892f70ca0e18c1bffcda0782316b7ada
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2018
ms.locfileid: "50211561"
---
# <a name="integrate-azure-expressroute-with-disaster-recovery-for-azure-vms"></a>Integrera Azure ExpressRoute med haveriberedskap för virtuella Azure-datorer


Den här artikeln beskriver hur du integrerar Azure ExpressRoute med [Azure Site Recovery](site-recovery-overview.md), när du konfigurerar haveriberedskap för virtuella Azure-datorer till en sekundär Azure-region.

Site Recovery kan haveriberedskap för virtuella Azure-datorer genom att replikera Virtuella Azure-data till Azure.

- Om virtuella Azure-datorer använder [Azure hanterade diskar](../virtual-machines/windows/managed-disks-overview.md), VM-data replikeras till en replikerad hanterad disk i den sekundära regionen.
- Om virtuella Azure-datorer inte använder hanterade diskar, replikeras VM-data till ett Azure storage-konto.
- Slutpunkter för replikering är offentliga men replikeringstrafik för virtuella Azure-datorer mellan inte internet.

ExpressRoute kan du utöka lokala nätverk till Microsoft Azure-molnet via en privat anslutning med hjälp av en anslutningsprovider. Om du har konfigurerat ExpressRoute, den kan integreras med Site Recovery på följande sätt:

- **När de replikeras mellan Azure-regioner**: replikeringstrafik för haveriberedskap för virtuella Azure-datorn är endast inom Azure och ExpressRoute inte behövs eller som används för replikering. Men om du ansluter från en lokal plats till Azure virtuella datorer på den primära platsen för Azure, finns det ett antal problem vara medveten om när du konfigurerar haveriberedskap för de virtuella Azure-datorer.
- **Växling mellan Azure-regioner**: vid avbrott så växlar du över virtuella Azure-datorer från primär till sekundär Azure-region. Det finns ett antal steg du ska vidta för att komma åt virtuella Azure-datorer i den sekundära regionen som använder ExpressRoute efter redundansväxling till en sekundär region.


## <a name="before-you-begin"></a>Innan du börjar

Innan du börjar måste du kontrollera att du förstår begreppen som följande:

- ExpressRoute [kretsar](../expressroute/expressroute-circuit-peerings.md)
- ExpressRoute [routningsdomäner](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains)
- ExpressRoute [platser](../expressroute/expressroute-locations.md).
- Azure VM [replikeringsarkitektur](azure-to-azure-architecture.md)
- Så här [Konfigurera replikering](azure-to-azure-tutorial-enable-replication.md) för virtuella Azure-datorer.
- Så här [redundansväxla](azure-to-azure-tutorial-failover-failback.md) virtuella Azure-datorer.


## <a name="general-recommendations"></a>Allmänna rekommendationer

Bästa praxis, samt att säkerställa effektiv mål för återställningstid (RTO) för katastrofåterställning, rekommenderar vi att du gör följande när du ställer in Site Recovery för att integrera med ExpressRoute:

- Etablera nätverkskomponenter före redundansväxling till en sekundär region:
    - När du aktiverar replikering för virtuella datorer i Azure distribuera Site Recovery automatiskt nätverksresurser, till exempel nätverk, undernät och gateways i målets Azure-region, baserat på käll-nätverksinställningar.
    - Site Recovery kan inte automatiskt konfigurera nätverksresurser, till exempel VNet-gatewayer.
    - Vi rekommenderar att du etablerar de här ytterligare nätverksresurser före redundans. Ett litet driftstopp är associerad med den här distributionen och den påverkar den övergripande återställningstiden om du inte ta hänsyn till den vid distributionsplanering.
- Köra tester av regelbundna haveriberedskap:
    - Testet verifierar din replikeringsstrategi utan dataförlust eller driftstopp och påverkar inte din produktionsmiljö. Det hjälper dig att undvika sista konfigurationsproblem som kan påverka RTO negativt.
    - När du kör ett redundanstest för detaljerade, rekommenderar vi att du använder ett separat nätverk för virtuella Azure-datorer, i stället för standardnätverk som har ställts in när du aktiverar replikering.
- Använd olika IP-adressutrymmen om du har en enda ExpressRoute-krets.
    - Vi rekommenderar att du använder en annan IP-adressutrymmet för det virtuella målnätverket. På så sätt undviker problem när upprättar anslutningar under regionala avbrott.
    - Om du inte kan använda ett separat adressutrymme, måste du köra redundanstest för disaster recovery-test på en separat testnätverket med olika IP-adresser. Du kan inte ansluta två virtuella nätverk med överlappande IP-adressutrymme till samma ExpressRoute-kretsen.

## <a name="replicate-azure-vms-when-using-expressroute"></a>Replikera virtuella Azure-datorer när du använder ExpressRoute


Om du vill konfigurera replikering för virtuella Azure-datorer på en primär plats och du ansluter till dessa virtuella datorer från din lokala plats via ExpressRoute, är här vad du behöver göra:

1. [Aktivera replikering](azure-to-azure-tutorial-enable-replication.md) för varje virtuell dator i Azure.
2. Du kan också låta Site Recovery som Konfigurera nätverk:
    - När du konfigurerar och aktiverar replikering, ställer Site Recovery in nätverk, undernät och gateway-undernät i mål-Azure-region, så att de matchar de som finns i källregionen. Site Recovery mappar också mellan käll- och virtuella nätverk.
    - Om du inte vill Site Recovery för att göra detta automatiskt skapa nätverksresurser som på målsidan innan du aktiverar replikering.
3. Skapa andra nätverksobjekt:
    - Site Recovery skapar inte routningstabeller, VNet-gatewayer, anslutningar för VNet-gateway, VNet-peering, eller andra nätverk resurser och anslutningar i den sekundära regionen.
    - Du behöver skapa dessa ytterligare nätverkselement i den sekundära regionen som helst innan du kör en redundans från den primära regionen.
    - Du kan använda [återställningsplaner](site-recovery-create-recovery-plans.md) och automatiserade skript för att konfigurera och ansluta dessa nätverksresurser.
1. Observera att om du har en virtuell nätverksinstallation (NVA) för att styra flödet av nätverkstrafik:
    - Azures standardsystemväg för Azure VM-replikering är 0.0.0.0/0.
    - NVA-distributioner vanligtvis definiera också en standardväg (0.0.0.0/0) som tvingar utgående Internet-trafiken kan flöda via NVA. Standardvägen används när ingen specifik väg konfiguration kan hittas.
    - Om så är fallet kan vara överbelastad NVA om all replikeringstrafik passerar via NVA.
    - Samma begränsningar gäller även när du använder standardvägar för routning av alla Virtuella Azure-trafik till lokala distributioner.
    - I det här scenariot rekommenderar vi att du [skapa en tjänstslutpunkt för nätverket](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) i det virtuella nätverket för Microsoft.Storage-tjänsten så att replikeringstrafiken inte lämnar Azure-gränsen.

## <a name="replication-example"></a>Exempel för replikering

Enterprise-distributioner har vanligtvis arbetsbelastningar delas upp på flera Azure Vnet med en central anslutning hubb för extern anslutning till internet och lokala platser. En topologi med nav och ekrar används vanligen tillsammans med ExpressRoute.

![På-lokalt till Azure med ExpressRoute före redundans](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

- **Region**. Appar som distribueras i Östasien för Azure-region.
- **Virtuella ekernätverk**. Appar som distribueras i två virtuella eker-nätverk:
    - **Källan vNet1**: 10.1.0.0/24.
    - **Källan vNet2**: 10.2.0.0/24.
    - Varje eker virtuellt nätverk är ansluten till **Hubbnätverk**.
- **Hubbnätverk**. Det finns en Hubbnätverk **källa Hubbnätverk**: 10.10.10.0/24.
    - Den här Hubbnätverk fungerar som gatekeepern.
    - All kommunikation över undernät igenom den här hubben.
 - Hub vNet undernät **. Hubbnätverk har två undernät:
     - **NVA-undernätet**: 10.10.10.0/25. Det här undernätet innehåller en NVA (10.10.10.10).
     - **Gateway-undernätet**: 10.10.10.128/25. Det här undernätet innehåller en ExpressRoute-gateway som är anslutna till en ExpressRoute-anslutning som dirigerar till den lokala platsen via en privat peering routningsdomän.
- Datacenter på plats har en ExpressRoute-krets-anslutning via en partner edge i Hongkong.
- Alla routning styrs via Azure-routningstabeller (UDR).
- All utgående trafik mellan virtuella nätverk eller till det lokala datacentret dirigeras via NVA.

### <a name="hub-and-spoke-peering-settings"></a>NAV och ekrar peering inställningar

#### <a name="spoke-to-hub"></a>Spoke till hub

**Riktning** | **Inställning** | **tillstånd**
--- | --- | ---
Spoke till hub | Tillåt virtuell nätverksadress | Enabled
Spoke till hub | Tillåt vidarebefordrad trafik | Enabled
Spoke till hub | Tillåt gatewayöverföring | Disabled
Spoke till hub | Använd ta bort gateways | Enabled

 ![Ekrar till hubben peering-konfigurationen](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

#### <a name="hub-to-spoke"></a>Hub till spoke

**Riktning** | **Inställning** | **tillstånd**
--- | --- | ---
Hub till spoke | Tillåt virtuell nätverksadress | Enabled
Hub till spoke | Tillåt vidarebefordrad trafik | Enabled
Hub till spoke | Tillåt gatewayöverföring | Enabled
Hub till spoke | Använd ta bort gateways | Disabled

 ![NAV och ekrar peering-konfigurationen](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="example-steps"></a>Exempelbeskrivningar

I vårt exempel, följande som ska hända när du aktiverar replikering för virtuella Azure-datorer i källnätverket:

1. Du [Aktivera replikering](azure-to-azure-tutorial-enable-replication.md) för en virtuell dator.
2. Site Recovery skapar repliken virtuella nätverk, undernät och gateway-undernät i målregionen.
3. Mappningar mellan käll-nätverk och målnätverken repliken skapas skapar site Recovery.
4. Du skapa manuellt virtuella nätverksgatewayer gatewayanslutningar för virtuella nätverk, virtuell nätverkspeering eller några andra nätverk resurser eller anslutningar.


## <a name="fail-over-azure-vms-when-using-expressroute"></a>Växla över virtuella datorer i Azure när du använder ExpressRoute

När du redundansväxlar virtuella Azure-datorer till målet med hjälp av Site Recovery, du kan komma åt dem med hjälp av ExpressRoute [privat peering](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).

- Du måste ansluta ExpressRoute till mål-vNet med en ny anslutning. Den befintliga ExpressRoute-anslutningen överförs inte automatiskt.
- Hur där du konfigurerar din ExpressRoute-anslutning till målet vNet beror på din ExpressRoute-topologi.


### <a name="access-with-two-circuits"></a>Åtkomst med två kretsar

#### <a name="two-circuits-with-two-peering-locations"></a>Två kretsar med två peering-platser

Den här konfigurationen hjälper att skyddar ExpressRoute-kretsar mot regionalt haveri. Om din primära peering loation slutar fungera kan fortsätta anslutningar från den andra platsen.

- Kretsen är anslutna till produktionsmiljön är vanligtvis primärt. Den sekundära kretsen har normalt lägre bandbredd, vilket kan ökas om en olycka inträffar.
- Du kan upprätta anslutningar från den sekundära ExpressRoute-kretsen till mål-vNet efter redundansväxlingen. Du kan också ha anslutningar som har förberetts och kan nu händelse av katastrof, för att minska övergripande återställningstid.
- Med samtidiga anslutningar till både primära servern och målets virtuella nätverk kan du se till att dina lokala routning bara använder den sekundära kretsen och anslutning efter redundansväxling.
- De virtuella nätverken för källa och mål kan ta emot nya IP-adresser, eller Behåll de samma efter en redundansväxling. I båda fallen kan sekundära anslutningar upprättas före redundans.


#### <a name="two-circuits-with-single-peering-location"></a>Två kretsar med enkel peering-plats

Den här konfigurationen skyddar mot fel på den primära ExpressRoute-kretsen, men inte om den enda peering ExpressRoute-platsen slutar fungera kan påverka både kretsar.

- Du kan ha samtidiga anslutningar från lokala datacenter till källan vNEt med den primära kretsen och till mål-vNet med den sekundära kretsen.
- Kontrollera att som en lokal routning endast använder sekundära kretsen och anslutning efter redundansväxling med samtidiga anslutningar till primära och målet.
-   Du kan inte ansluta både kretsar till samma vNet när kretsar som skapas på samma plats för peering.

### <a name="access-with-a-single-circuit"></a>Åtkomst med en enda krets

I den här konfigurationen finns endast en Expressroute-krets. Även om kretsen har en redundant anslutning om en kraschar, ger en enda route-kretsen inte flexibilitet om din peering region stängs av. Tänk på följande:

- Du kan replikera virtuella Azure-datorer till alla Azure-regioner i den [samma geografiska plats](azure-to-azure-support-matrix.md#region-support). Om det inte är på samma plats som källa i Azure-målregion, måste du aktivera ExpressRoute Premium om du använder en enda ExpressRoute-krets. Lär dig mer om [ExpressRoute-platser](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) och [prisinformation för ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).
- Du kan inte ansluta käll- och virtuella nätverk samtidigt till kretsen om du använder samma IP-adressutrymmet på målregion. I det här scenariot:    
    -  Koppla från anslutningen till datakällan på klientsidan och sedan ansluta mål på serversidan. Den här anslutningen ändringen kan skriptas som en del av en återställningsplan för Site Recovery. Tänk på följande:
        - I ett regionala fel om den primära regionen är otillgänglig, misslyckas åtgärden koppla från. Detta kan påverka skapa en anslutning till målregion.
        - Om du skapade anslutningen i målregionen, och den primära regionen återställer senare, kan det uppstå paket förlorade meddelanden om två samtidiga anslutningar som försöker ansluta till samma-adressutrymme.
        - Om du vill förhindra detta genom att avsluta den primära anslutningen omedelbart.
        - Efter återställning av virtuell dator till den primära regionen, kan den primära igen att ansluta, när du kopplar från den sekundära anslutningen.
-   Om du använder en annan adressutrymmen på mål-vNet, kan du samtidigt ansluta till käll- och virtuella nätverk från samma ExpressRoute-krets.


## <a name="failover-example"></a>Exempel för redundans

I vårt exempel använder vi följande topologi:

- Två olika ExpressRoute-kretsar i två olika peeringplatser.
- Behålla privata IP-adresser i virtuella Azure-datorer efter redundansväxling.
- Målregion för återställning är Azure Sydostasien.
- Sekundära finns ExpressRoute-kretsen en anslutning via en partner edge i Singapore.

För en enkel topologi som använder en enda ExpressRoute-krets med samma IP-adress efter redundansväxling [läsa den här artikeln](site-recovery-retain-ip-azure-vm-failover.md#on-premises-to-azure-connectivity).

### <a name="example-steps"></a>Exempelbeskrivningar
Automatisera återställning i det här exemplet härs vad du behöver göra:

1. Följ stegen för att [Konfigurera replikering](#azure-vm-replication-steps).
2. [Växla över virtuella Azure-datorer](azure-to-azure-tutorial-failover-failback.md), med dessa ytterligare steg under eller efter redundans.

    a. Skapa Azure ExpressRoute-Gateway i mål-region hubbens virtuella nätverk. Detta är måste du ansluta target-Hubbnätverk till ExpressRoute-kretsen.

    b. Skapa anslutningen från mål-Hubbnätverk till målet ExpressRoute-krets.

    c. Konfigurera VNet-peering mellan målregion NAV och eker-nätverk. Peering-egenskaperna i målregionen att vara samma som de på källregionen.

    d. Konfigurera udr: er i det virtuella hubbnätverket och de två virtuella ekernätverken.

    - Egenskaperna målsidan udr: er är samma som på käll-sida när med samma IP-adresser.
    - Med annat mål-IP-adresser, ska sedan udr-vägarna anpassas.


Stegen ovan kan skriptas som en del av en [återställningsplanen](site-recovery-create-recovery-plans.md). Beroende på program-anslutning och krav för tid, kan ovanstående steg utföras innan du startar redundans.

#### <a name="after-recovery"></a>Efter återställning

När du återställer de virtuella datorerna och slutföra anslutningen, är Återställningsmiljön som följer.

![På-lokalt till Azure med ExpressRoute efter redundans](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)



## <a name="next-steps"></a>Nästa steg

Läs mer om hur du använder [återställningsplaner](site-recovery-create-recovery-plans.md) vill automatisera redundans för appen.
