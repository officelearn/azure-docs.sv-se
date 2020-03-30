---
title: Behåll IP-adresser efter redundans för Azure VM med Azure Site Recovery
description: Beskriver hur du behåller IP-adresser när du misslyckas med virtuella Azure-datorer för haveriberedskap till en sekundär region med Azure Site Recovery
ms.service: site-recovery
ms.date: 4/9/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 650fb7f0877a98ef53ed3868550f9c084ecb5885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257568"
---
# <a name="retain-ip-addresses-during-failover"></a>Behåll IP-adresser under redundans

[Azure Site Recovery](site-recovery-overview.md) möjliggör haveriberedskap för virtuella Azure-datorer genom att replikera virtuella datorer till en annan Azure-region, misslyckas om ett avbrott inträffar och misslyckas tillbaka till den primära regionen när saker och ting är tillbaka till det normala.

Under redundans kanske du vill behålla IP-adresseringen i målområdet som är identisk med källregionen:

- När du aktiverar haveriberedskap för virtuella Azure-datorer skapar site recovery som standard målresurser baserat på källresursinställningar. För virtuella Azure-datorer som konfigurerats med statiska IP-adresser försöker Site Recovery etablera samma IP-adress för måldatorn, om den inte används. En fullständig förklaring av hur Site Recovery hanterar adressering [finns i den här artikeln](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).
- För enkla program är standardkonfigurationen tillräcklig. För mer komplexa appar kan du behöva etablera ytterligare resurser för att se till att anslutningen fungerar som förväntat efter redundans.


Den här artikeln innehåller några exempel på hur du behåller IP-adresser i mer komplexa exempelscenarier. Exemplen är:

- Redundans för ett företag med alla resurser som körs i Azure
- Redundans för ett företag med en hybriddistribution och resurser som körs både lokalt och i Azure

## <a name="resources-in-azure-full-failover"></a>Resurser i Azure: fullständig redundans

Företag A har alla sina appar som körs i Azure.

### <a name="before-failover"></a>Före redundans

Här är arkitekturen före redundans.

- Företag A har identiska nätverk och undernät i käll- och mål azureregioner.
- För att minska återställningstiden mål (RTO), använder företaget replik noder för SQL Server Always On, domänkontrollanter, etc. Dessa repliknoder finns i ett annat virtuella nätverk i målregionen, så att de kan upprätta VPN-anslutning från plats till plats mellan käll- och målområdena. Detta är inte möjligt om samma IP-adressutrymme används i källan och målet.  
- Före redundans är nätverksarkitekturen följande:
    - Primär region är Azure Östasien
        - Östasien har ett VNet **(Source VNet)** med adressutrymme 10.1.0.0/16.
        - Östasien har arbetsbelastningar uppdelade på tre undernät i det virtuella nätverket:
            - **Undernät 1**: 10.1.1.0/24
            - **Undernät 2**: 10.1.2.0/24
            - **Undernät 3**: 10.1.3.0/24
    - Sekundär (mål)-region är Azure Sydostasien
        - Sydostasien har ett återställnings-VNet **(Recovery VNet)** identiskt med **Source VNet**.
        - Sydostasien har ytterligare ett virtuella nätverk (**Azure VNet**) med adressutrymme 10.2.0.0/16.
        - **Azure VNet** innehåller ett undernät (**Undernät 4**) med adressutrymme 10.2.4.0/24.
        - Repliknoder för SQL Server Always On, domänkontrollant etc. finns i **Undernät 4**.
    - **Källa VNet** och **Azure VNet** är anslutna med en VPN-anslutning för plats-till-plats.
    - **Återställnings-virtuellt nätverk** är inte anslutet till något annat virtuellt nätverk.
    - **Företag A** tilldelar/verifierar mål-IP-adresser för replikerade artiklar. Mål-IP är samma som käll-IP för varje virtuell dator.

![Resurser i Azure före fullständig redundans](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Efter redundans

Om ett regionfel för en källa inträffar kan företag A växla över alla sina resurser till målregionen.

- Med mål-IP-adresser redan på plats före redundansen kan företag A orkestrera redundans och automatiskt upprätta anslutningar efter redundans mellan **Återställnings-VNet** och **Azure VNet**. Detta illustreras i följande diagram..
- Beroende på appkrav kan anslutningar mellan de två virtuella nätverken (**Recovery VNet** och **Azure VNet**) i målregionen upprättas före, under (som ett mellanliggande steg) eller efter redundansen.
  - Företaget kan använda [återställningsplaner](site-recovery-create-recovery-plans.md) för att ange när anslutningar ska upprättas.
  - De kan ansluta mellan virtuella nätverk med hjälp av VNet peering eller plats-till-plats VPN.
      - VNET-peering använder ingen VPN-gateway och har även andra restriktioner.
      - VNet-peering-prissättning beräknas på ett annat sätt än [priser på](https://azure.microsoft.com/pricing/details/vpn-gateway)VNet-till-VNet VPN Gateway . [pricing](https://azure.microsoft.com/pricing/details/virtual-network) För redundans rekommenderar vi i allmänhet att använda samma anslutningsmetod som källnätverk, inklusive anslutningstypen, för att minimera oförutsägbara nätverksincidenter.

    ![Resurser i Azure fullständig redundans](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover2.png)



## <a name="resources-in-azure-isolated-app-failover"></a>Resurser i Azure: en isolerad app redundans

Du kan behöva växla över på appnivå. Om du till exempel vill växla över en viss app- eller appnivå som finns i ett dedikerat undernät.

- I det här fallet, även om du kan behålla IP-adressering, är det i allmänhet inte tillrådligt eftersom det ökar risken för konnektivitet inkonsekvenser. Du förlorar också nätanslutning till andra undernät inom samma Azure VNet.
- Ett bättre sätt att göra appundans på undernätsnivå är att använda olika mål-IP-adresser för redundans (om du behöver anslutning till andra undernät på vnet-källor) eller att isolera varje app i sitt eget dedikerade virtuella nätverk i källregionen. Med den senare metoden kan du upprätta anslutning mellan nätverk i källregionen och emulera samma beteende när du växlar över till målregionen.  

I det här exemplet placerar företag A appar i källregionen i dedikerade virtuella nätverk och upprättar anslutning mellan dessa virtuella nätverk. Med den här designen kan de utföra enstaka app redundans och behålla källans privata IP-adresser i målnätverket.

### <a name="before-failover"></a>Före redundans

Före redundans är arkitekturen följande:

- Virtuella programprogram finns i den primära regionen Azure East Asia:
    - **App1 (App1)** Virtuella datorer finns i VNet **Source VNet 1**: 10.1.0.0/16.
    - **App2 (App2)** Virtuella datorer finns i VNet **Source VNet 2**: 10.2.0.0/16.
    - **Källa VNet 1** har två undernät.
    - **Källa VNet 2** har två undernät.
- Sekundär (mål) region är Azure Sydostasien - Sydostasien har en återställning virtuella nätverk **(Recovery VNet 1** och **Recovery VNet 2)** som är identiska med **Source VNet 1** och **Source VNet 2**.
        - **Återställning VNet 1** och **Recovery VNet 2** har vardera två undernät som matchar undernäten i **Source VNet 1** och **Source VNet 2** - Sydostasien har ytterligare ett VNet **(Azure VNet)** med adressutrymme 10.3.0.0/16.
        - **Azure VNet** innehåller ett undernät (**Undernät 4**) med adressutrymme 10.3.4.0/24.
        - Repliknoder för SQL Server Always On, domänkontrollant etc. finns i **undernät 4**.
- Det finns ett antal VPN-anslutningar från plats till plats: 
    - **Källa VNet 1** och **Azure VNet**
    - **Källa VNet 2** och **Azure VNet**
    - **Källa VNet 1** och **Source VNet 2** är anslutna med VPN-plats-till-plats
- **Återställning VNet 1** och **Återställning VNet 2** är inte anslutna till några andra virtuella nätverk.
- **Företag A** konfigurerar VPN-gateways på **Recovery VNet 1** och **Recovery VNet 2**för att minska RTO.  
- **Återställning VNet1** och **Återställning VNet2** är inte anslutna till något annat virtuellt nätverk.
- För att minska återställningstidens mål (RTO) konfigureras VPN-gateways på **Recovery VNet1** och **Recovery VNet2** före redundans.

    ![Resurser i Azure före app redundans](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover2.png)

### <a name="after-failover"></a>Efter redundans

I händelse av ett avbrott eller problem som påverkar en enda app (i **Source VNet 2 i vårt exempel) kan företag A återställa den berörda appen enligt följande:


- Koppla från VPN-anslutningar mellan **Source VNet1** och **Source VNet2**och mellan **Source VNet2** och **Azure VNet** .
- Upprätta VPN-anslutningar mellan **Source VNet1** och **Recovery VNet2**och mellan **Återställning VNet2** och **Azure VNet**.
- Växla över virtuella datorer i **VNet2 källa** till **återställning VNet2**.

![Redundans för resurser i Azure-appen](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover2.png)


- Det här exemplet kan utökas så att det omfattar fler program och nätverksanslutningar. Rekommendationen är att följa en liknande anslutningsmodell, så långt det är möjligt, när man misslyckas över från källa till mål.
- VPN Gateways använder offentliga IP-adresser och gateway-hopp för att upprätta anslutningar. Om du inte vill använda offentliga IP-adresser, eller om du vill undvika extra hopp, kan du använda [Azure VNet-peering](../virtual-network/virtual-network-peering-overview.md) till virtuella peer-nätverk i [Azure-regioner som stöds](../virtual-network/virtual-network-manage-peering.md#cross-region).

## <a name="hybrid-resources-full-failover"></a>Hybridresurser: fullständig redundans

I det här fallet kör **företag B** en hybridverksamhet, med en del av programinfrastrukturen som körs på Azure och resten körs lokalt. 

### <a name="before-failover"></a>Före redundans

Så här ser nätverksarkitekturen ut innan redundans.

- Virtuella programprogram finns i Azure East Asia.
- Östasien har ett VNet **(Source VNet)** med adressutrymme 10.1.0.0/16.
  - Östasien har arbetsbelastningar uppdelade på tre undernät i **Source VNet:**
    - **Undernät 1**: 10.1.1.0/24
    - **Undernät 2**: 10.1.2.0/24
    - **Undernät 3:** 10.1.3.0/24, med hjälp av ett virtuellt Azure-nätverk med adressutrymme 10.1.0.0/16. Det här virtuella nätverket heter **Source VNet**
      - Den sekundära (mål)regionen är Azure Southeast Asia:
  - Sydostasien har ett återställnings-VNet **(Recovery VNet)** identiskt med **Source VNet**.
- Virtuella datorer i Östasien är anslutna till ett lokalt datacenter med Azure ExpressRoute eller plats-till-plats-VPN.
- För att minska RTO, företag B bestämmelser gateways på Recovery VNet i Azure Sydostasien före redundans.
- Företag B tilldelar/verifierar mål-IP-adresser för replikerade virtuella datorer. Mål-IP-adressen är samma som käll-IP-adress för varje virtuell dator.


![Anslutning till Azure lokalt i Azure före redundans](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Efter redundans


Om ett regionfel för en källa inträffar kan företag B växla över alla resurser till målregionen.

- Med mål-IP-adresser redan på plats före redundansen kan företag B orkestrera redundans och automatiskt upprätta anslutningar efter redundans mellan **Återställnings-VNet** och **Azure VNet**.
- Beroende på appkrav kan anslutningar mellan de två virtuella nätverken (**Recovery VNet** och **Azure VNet**) i målregionen upprättas före, under (som ett mellanliggande steg) eller efter redundansen. Företaget kan använda [återställningsplaner](site-recovery-create-recovery-plans.md) för att ange när anslutningar ska upprättas.
- Den ursprungliga anslutningen mellan Azure East Asia och det lokala datacentret bör kopplas från innan anslutningen upprättas mellan Azure Southeast Asia och lokala datacenter.
- Den lokala routningen konfigureras om så att den pekar på målregionen och gateways efter redundans.

![Anslutning till Azure lokalt i Azure efter redundans](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover2.png)

## <a name="hybrid-resources-isolated-app-failover"></a>Hybridresurser: en isolerad appväxling

Företag B kan inte växla över isolerade appar på undernätsnivå. Detta beror på att adressutrymmet på virtuella nätverk för källa och återställning är detsamma och den ursprungliga källan till den lokala anslutningen är aktiv.

 - För appåtersåtersåtersåterstärcens måste företag B placera varje app i sitt eget dedikerade Azure VNet.
 - Med varje app i ett separat virtuella nätverk kan företag B växla över isolerade appar och dirigera källanslutningar till målregionen.

## <a name="next-steps"></a>Nästa steg

Läs mer om [återställningsplaner](site-recovery-create-recovery-plans.md).
