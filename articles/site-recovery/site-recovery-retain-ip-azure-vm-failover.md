---
title: Behålla IP-adresser vid redundansväxling av Azure virtuella datorer med Azure Site Recovery | Microsoft Docs
description: Beskriver hur du behålla IP-adresser när redundansväxla virtuella datorer i Azure för haveriberedskap till en sekundär region med Azure Site Recovery
ms.service: site-recovery
ms.date: 4/9/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 618d60417aa6b582eaef94bf75dcf16c74750f83
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61277332"
---
# <a name="retain-ip-addresses-during-failover"></a>Behålla IP-adresser vid redundans

[Azure Site Recovery](site-recovery-overview.md) möjliggör haveriberedskap för virtuella Azure-datorer genom att replikera datorer till en annan Azure-region, redundansväxla om ett avbrott uppstår och växla tillbaka till den primära regionen när saker är tillbaka till normal.

Under redundansväxlingen, kanske du vill behålla den IP-adresser i målregionen som är identisk med källregionen:

- Som standard skapar Site Recovery target-resurser utifrån resurs-inställningar för datakälla när du aktiverar haveriberedskap för virtuella Azure-datorer. Site Recovery försöker etablera samma IP-adress för den Virtuella, måldatorn om den inte används för Azure-datorer som har konfigurerats med statiska IP-adresser. En fullständig förklaring på hur Site Recovery hanterar adressering, [läsa den här artikeln](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).
- För enkla program räcker standardkonfigurationen. För mer komplexa appar, kan du behöva etablera ytterligare en resurs som kontrollerar att anslutningen fungerar som förväntat efter en redundansväxling.


Den här artikeln innehåller några exempel för att bevara IP-adresser i mer avancerade scenarier. Exempel:

- Redundans för ett företag med alla resurser som körs i Azure
- Redundans för ett företag med en hybriddistribution och resurser som körs både lokalt och i Azure

## <a name="resources-in-azure-full-failover"></a>Resurser i Azure: fullständig växling vid fel

Företag A har alla appar som körs i Azure.

### <a name="before-failover"></a>Före redundans

Här är arkitekturen före redundans.

- Företagets A har identiska nätverk och undernät i datakällan och rikta in Azure-regioner.
- För att minska återställningstid (RTO), använder företagets repliken noder för SQL Server Always On, domänkontrollanter osv. Dessa noder för repliken är i ett annat virtuellt nätverk i målregionen, så att de kan upprätta VPN plats-till-plats-anslutning mellan källa och mål-regioner. Det är inte möjligt om du använder samma IP-adressutrymme i källan och målet.  
- Före redundans är nätverksarkitekturen följande:
    - Primära regionen är Azure Östasien
        - Östasien har ett virtuellt nätverk (**Källnätverk**) med adressen utrymme 10.1.0.0/16.
        - Östasien har arbetsbelastningar som delas upp i alla tre undernät i det virtuella nätverket:
            - **Undernät 1**: 10.1.1.0/24
            - **Undernät 2**: 10.1.2.0/24,
            - **Undernät 3**: 10.1.3.0/24
    - Sekundär (mål) regionen är Azure Sydostasien
        - Sydostasien har en återställning VNet (**Recovery VNet**) identisk **Källnätverk**.
        - Sydostasien har en ytterligare virtuella nätverk (**virtuella Azure-nätverket**) med adressen utrymme 10.2.0.0/16.
        - **Azure VNet** innehåller ett undernät (**undernät 4**) med adressen utrymme 10.2.4.0/24.
        - Repliken noder för SQL Server Always On, domänkontrollant osv finns i **undernät 4**.
    - **Datakällan VNet** och **virtuella Azure-nätverket** är anslutna med plats-till-plats VPN-anslutning.
    - **Recovery VNet** är inte kopplad till något virtuellt nätverk.
    - **Företagets A** tilldelar/verifierar mål-IP-adresser för replikerade objekt. Mål-IP är samma som käll-IP för varje virtuell dator.

![Resurser i Azure innan du fullständig redundans](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Efter en redundansväxling

Om det uppstår ett regionalt strömavbrott källa, kan företag A redundansväxla alla dess resurser till målregion.

- Med mål-IP-adresser redan på plats innan redundansen, företag A kan samordna redundans och upprätta anslutningar automatiskt efter en redundansväxling mellan **Recovery VNet** och **virtuella Azure-nätverket**. Detta illustreras i följande diagram...
- Beroende på krav för appar, anslutningar mellan de två virtuella nätverken (**Recovery VNet** och **virtuella Azure-nätverket**) i mål-region kan vara etablerad innan, under (som ett mellanliggande steg) eller efter redundansen.
  - Företaget kan använda [återställningsplaner](site-recovery-create-recovery-plans.md) att ange när anslutningar upprättas.
  - De kan ansluta mellan virtuella nätverk med VNet-peering eller plats-till-plats-VPN.
      - VNET-peering använder ingen VPN-gateway och har även andra restriktioner.
      - VNet-peering [priser](https://azure.microsoft.com/pricing/details/virtual-network) beräknas annorlunda än VNet-till-VNet VPN-gatewayen [priser](https://azure.microsoft.com/pricing/details/vpn-gateway). För växling vid fel rekommenderar vi Allmänt använda samma anslutningsmetod som käll-nätverk, inklusive typen av anslutning kan minimera oförutsägbara nätverk incidenter.

    ![Resurser i Azure fullständig växling vid fel](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover2.png)



## <a name="resources-in-azure-isolated-app-failover"></a>Resurser i Azure: isolerade app redundans

Du kan behöva redundansväxla på appnivå. Till exempel vill redundansväxla en viss app eller app-nivå finns i ett dedikerat undernät.

- I det här scenariot, även om du kan behålla IP-adresser, rekommenderas det inte Allmänt eftersom det ökar risken för anslutning inkonsekvenser. Du förlorar också undernäts-anslutningen till andra undernät i samma Azure VNet.
- Ett bättre sätt att göra på undernätsnivå app växling vid fel är att använda annat mål-IP-adresser för redundans (om du behöver ansluta till andra undernät på källan virtuellt nätverk) eller att isolera varje app i en egen dedikerade virtuella nätverk i källregionen. Med den senare metoden kan du upprätta en anslutning mellan nätverk i källregionen och emulera samma beteende när du växlar över till målregion.  

I det här exemplet A platser företagsappar i källregionen i dedikerade virtuella nätverk och upprättar anslutningen mellan de virtuella nätverk. Med den här designen de utför isolerad app redundans och behålla de privata IP-källadresserna i Målnätverk.

### <a name="before-failover"></a>Före redundans

Före redundans är arkitekturen följande:

- Programmet virtuella datorer finns i den primära regionen Asien Azure:
    - **App1** virtuella datorer finns i det virtuella nätverket **källa VNet 1**: 10.1.0.0/16.
    - **App2** virtuella datorer finns i det virtuella nätverket **källa VNet 2**: 10.2.0.0/16.
    - **Käll-virtuellt nätverk 1** har två undernät.
    - **Datakällan VNet 2** har två undernät.
- Sekundär (mål) regionen är Azure Asien, sydöstra – Sydostasien har en återställning av virtuella nätverk (**Recovery VNet 1** och **Recovery VNet 2**) som är identiska med **källa VNet 1** och **Source Vnet2**.
        - **Recovery VNet 1** och **Recovery VNet 2** var och en har två undernät som matchar undernäten i **källa VNet 1** och **källa VNet 2** -Sydostasien har en ytterligare virtuella nätverk (**virtuella Azure-nätverket**) med adressen utrymme 10.3.0.0/16.
        - **Azure VNet** innehåller ett undernät (**undernät 4**) med adressen utrymme 10.3.4.0/24.
        -Replik-noder för SQL Server Always On, domänkontrollant osv finns i **undernät 4**.
- Det finns ett antal plats-till-plats VPN-anslutningar: 
    - **Käll-virtuellt nätverk 1** och **Azure VNet**
    - **Käll-Vnet2** och **Azure VNet**
    - **Käll-virtuellt nätverk 1** och **källa VNet 2** är anslutna med VPN plats-till-plats
- **Recovery VNet 1** och **Recovery VNet 2** inte är ansluten till andra virtuella nätverk.
- **Företagets A** konfigurerar VPN-gatewayer på **Recovery VNet 1** och **Recovery VNet 2**, för att minska RTO.  
- **Recovery VNet1** och **Recovery VNet2** inte är anslutna med andra virtuella nätverket.
- För att minska återställningstid (RTO), VPN-gatewayer har konfigurerats på **Recovery VNet1** och **Recovery VNet2** före redundans.

    ![Resurser i Azure innan appen redundans](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover2.png)

### <a name="after-failover"></a>Efter en redundansväxling

I händelse av ett avbrott eller problem som påverkar en enda app (i ** källa VNet 2 i vårt exempel), företag A kan återställa appen påverkas på följande sätt:


- Koppla från VPN-anslutningar mellan **källa VNet1** och **källa VNet2**, och mellan **källa VNet2** och **virtuella Azure-nätverket** .
- Upprätta VPN-anslutningar mellan **källa VNet1** och **Recovery VNet2**, och mellan **Recovery VNet2** och **virtuella Azure-nätverket**.
- Växla över virtuella datorer i **Source VNet2** till **Recovery VNet2**.

![Resurser i Azure app-redundans](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover2.png)


- Det här exemplet kan utökas med fler program och nätverksanslutningar. Rekommendationen är att följa en liknande-liknande anslutning modell, så långt möjligt när redundansväxlingen från källan till målet.
- VPN-gatewayer använder offentliga IP-adresser och gateway hopp för att upprätta anslutningar. Om du inte vill använda offentliga IP-adresser, eller du vill undvika extra hopp kan du använda [Azure VNet-peering](../virtual-network/virtual-network-peering-overview.md) peer-koppla virtuella nätverk över [Azure-regioner som stöds](../virtual-network/virtual-network-manage-peering.md#cross-region).

## <a name="hybrid-resources-full-failover"></a>Hybrid-resurser: fullständig växling vid fel

I det här scenariot **företag B** körs en hybrid-företag med en del av den infrastruktur som körs på Azure och resten som körs lokalt. 

### <a name="before-failover"></a>Före redundans

Här är nätverksarkitekturen ser det ut före redundans.

- Programmet virtuella datorer finns i Azure Östasien.
- Östasien har ett virtuellt nätverk (**Källnätverk**) med adressen utrymme 10.1.0.0/16.
  - Östasien har arbetsbelastningar som delas upp i alla tre undernät i **Källnätverk**:
    - **Undernät 1**: 10.1.1.0/24
    - **Undernät 2**: 10.1.2.0/24,
    - **Undernät 3**: 10.1.3.0/24utilizing Azure-nätverk med adress utrymme 10.1.0.0/16. Det här virtuella nätverket har namnet **Källnätverk**
      - Regionen sekundär (mål) är Azure Sydostasien:
  - Sydostasien har en återställning VNet (**Recovery VNet**) identisk **Källnätverk**.
- Virtuella datorer i Östasien är anslutna till ett lokalt datacenter med Azure ExpressRoute eller plats-till-plats-VPN.
- För att minska RTO etablerar företag B gateways på Recovery VNet i Azure Sydostasien före redundans.
- Företag B verifierar tilldelar/mål-IP-adresser för replikerade virtuella datorer. Mål-IP-adressen är samma som IP-källadressen för varje virtuell dator.


![På-plats-till-Azure-anslutningen innan redundans](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Efter en redundansväxling


Om det uppstår ett regionalt strömavbrott källa, kan företag B redundansväxla alla dess resurser till målregion.

- Med mål-IP-adresser redan på plats innan redundansen, företag B kan samordna redundans och upprätta anslutningar automatiskt efter en redundansväxling mellan **Recovery VNet** och **virtuella Azure-nätverket**.
- Beroende på krav för appar, anslutningar mellan de två virtuella nätverken (**Recovery VNet** och **virtuella Azure-nätverket**) i mål-region kan vara etablerad innan, under (som ett mellanliggande steg) eller efter redundansen. Företaget kan använda [återställningsplaner](site-recovery-create-recovery-plans.md) att ange när anslutningar upprättas.
- Den ursprungliga anslutningen mellan Azure-Östasien och datacenter på plats ska kopplas bort innan du upprättar anslutningen mellan Azure, Sydostasien och lokala datacenter.
- Den lokala routning konfigureras för att peka till målregion och gatewayer efter redundans.

![På-plats-till-Azure-anslutning efter redundansväxling](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover2.png)

## <a name="hybrid-resources-isolated-app-failover"></a>Hybrid-resurser: isolerade app redundans

Företag B det går inte att redundansväxla isolerade appar på undernätverksnivån i. Detta är eftersom adressutrymmet på käll- och återställning av virtuella nätverk är samma och den ursprungliga källan till den lokala anslutningen är aktiv.

 - För återhämtning av app måste företag B placera varje app i en egen dedikerade virtuella Azure-nätverket.
 - Med varje app i ett separat virtuellt nätverk, företag B växlar över isolerad apparna och dirigera anslutningar till datakällor till målregionen.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [återställningsplaner](site-recovery-create-recovery-plans.md).
