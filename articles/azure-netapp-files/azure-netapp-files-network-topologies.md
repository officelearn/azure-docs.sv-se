---
title: Riktlinjer för nätverksplanering för Azure NetApp-filer | Microsoft-dokument
description: I artikeln beskrivs riktlinjer som kan hjälpa dig att utforma en effektiv nätverksarkitektur med hjälp av Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: ram-kakani
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: b-juche
ms.openlocfilehash: 8e6a1c3472c6b20b27cf181edbeeb96ab71eb58d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73242491"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Riktlinjer för Azure NetApp Files-nätverksplanering

Planering av nätverksarkitektur är en viktig del av utformningen av alla programinfrastrukturer. Den här artikeln hjälper dig att utforma en effektiv nätverksarkitektur för dina arbetsbelastningar för att dra nytta av de omfattande funktionerna i Azure NetApp Files.

Azure NetApp-filers volymer är utformade för att finnas i ett specialnät som kallas ett [delegerat undernät](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) i ditt virtuella Azure-nätverk. Därför kan du komma åt volymerna direkt från ditt virtuella nätverk, från peered-virtuella nätverk i samma region eller från lokala via en Virtuell nätverksgateway (ExpressRoute eller VPN Gateway) efter behov. Undernätet är dedikerat till Azure NetApp-filer och det finns ingen anslutning till andra Azure-tjänster eller Internet.

## <a name="considerations"></a>Överväganden  

Du bör förstå några saker när du planerar för Azure NetApp Files-nätverk.

### <a name="constraints"></a>Villkor

Funktionerna nedan stöds för närvarande inte för Azure NetApp-filer: 

* Nätverkssäkerhetsgrupper (NSG) som tillämpas på det delegerade undernätet
* Användardefinierade vägar (UDRs) med adressprefix som Azure NetApp-filer undernät
* Azure-principer (till exempel anpassade namngivningsprinciper) i Azure NetApp Files-gränssnittet
* Belastningsutjämnare för Azure NetApp-filer trafik

Följande nätverksbegränsningar gäller för Azure NetApp-filer:

* Antalet IPs som används i ett virtuella nätverk med Azure NetApp-filer (inklusive peer-virtuella nätverk) får inte överstiga 1000. Vi arbetar för att öka denna gräns för att möta kundernas krav skala. Under tiden, om du behöver för fler IPs, nå ut till vårt supportteam med ditt användningsfall och obligatorisk gräns.
* Endast ett undernät kan delegeras till Azure NetApp Files i varje Azure Virtual Network (VNet).


### <a name="supported-network-topologies"></a>Nätverkstopologier som stöds

I följande tabell beskrivs de nätverkstopologier som stöds av Azure NetApp-filer.  Den beskriver också lösningarna för topologier som inte stöds. 

|    Topologier    |    Stöds    |     Lösning    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    Anslutning till volym i ett lokalt virtuella nätverk    |    Ja    |         |
|    Anslutning till volym i ett peer-nätverk (samma region)    |    Ja    |         |
|    Anslutning till volym i ett peer-nätverk (region över eller globalt peering)    |    Inga    |    Inget    |
|    Anslutning till en volym via ExpressRoute-gateway    |    Ja    |         |
|    Anslutning från lokalt till en volym i ett eker-VNet via ExpressRoute-gateway och VNet-peering med gatewaytransas    |    Ja    |        |
|    Anslutning från lokalt till en volym i ett eker-VNet via VPN-gateway    |    Ja    |         |
|    Anslutning från lokalt till en volym i ett eker-VNet via VPN-gateway och VNet-peering med gatewaytransas    |    Ja    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Virtuella nätverk för Azure NetApp-filer volymer

I det här avsnittet beskrivs begrepp som hjälper dig med virtuell nätverksplanering.

### <a name="azure-virtual-networks"></a>Virtuella Azure-nätverk

Innan du etablerar en Azure NetApp-filvolym måste du skapa ett virtuellt Azure-nätverk (VNet) eller använda ett som redan finns i din prenumeration. Det virtuella nätverket definierar volymens nätverksgräns.  Mer information om hur du skapar virtuella nätverk finns i [dokumentationen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)till Azure Virtual Network .

### <a name="subnets"></a>Undernät

Subnets segment the virtual network into separate address spaces that are usable by the Azure resources in them.  Azure NetApp-filer finns i ett specialbaserat undernät som kallas ett [delegerat undernät](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet). 

Undernätsdelegering ger explicit behörighet till Azure NetApp Files-tjänsten för att skapa tjänstspecifika resurser i undernätet.  Den använder en unik identifierare för att distribuera tjänsten. I det här fallet skapas ett nätverksgränssnitt för att aktivera anslutning till Azure NetApp-filer.

Om du använder ett nytt virtuella nätverk kan du skapa ett undernät och delegera undernätet till Azure NetApp-filer genom att följa instruktionerna i [Delegera ett undernät till Azure NetApp-filer](azure-netapp-files-delegate-subnet.md). Du kan också delegera ett befintligt tomt undernät som inte redan har delegerats till andra tjänster.

Om det virtuella nätverket är peered med ett annat virtuella nätverk kan du inte expandera VNet-adressutrymmet. Därför måste det nya delegerade undernätet skapas inom VNet-adressutrymmet. Om du behöver utöka adressutrymmet måste du ta bort VNet-peering innan du expanderar adressutrymmet.

### <a name="udrs-and-nsgs"></a>UDR och NSG

Användardefinierade vägar (UDRs) och NSG (Network) stöds inte på delegerade undernät för Azure NetApp-filer.

Som en lösning kan du använda NSG:er på andra undernät som antingen tillåter eller nekar trafik till och från det delegerade undernätet för Azure NetApp-filer.  

## <a name="azure-native-environments"></a>Inbyggda Azure-miljöer

Följande diagram illustrerar en Azure-inbyggd miljö:

![Inbyggd nätverksmiljö för Azure](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>Lokalt VNet

Ett grundläggande scenario är att skapa eller ansluta till en Azure NetApp-filvolym från en virtuell dator (VM) i samma virtuella nätverk. För VNet 2 i diagrammet ovan skapas volym 1 i ett delegerat undernät och kan monteras på VM 1 i standardundernätet.

### <a name="vnet-peering"></a>VNet-peering

Om du har ytterligare virtuella nätverk i samma region som behöver åtkomst till varandras resurser kan virtuella nätverk anslutas med hjälp av [VNet-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) för att möjliggöra säker anslutning via Azure-infrastrukturen. 

Tänk på VNet 2 och VNet 3 i diagrammet ovan. Om VM 1 behöver ansluta till VM 2 eller Volym 2, eller om VM 2 behöver ansluta till VM 1 eller Volym 1, måste du aktivera VNet-peering mellan VNet 2 och VNet 3. 

Dessutom bör du tänka på ett scenario där VNet 1 är peered med VNet 2 och VNet 2 är peered med VNet 3 i samma region. Resurserna från VNet 1 kan ansluta till resurser i VNet 2 men det går inte att ansluta till resurser i VNet 3, såvida inte VNet 1 och VNet 3 är peered. 

I diagrammet ovan, även om VM 3 kan ansluta till volym 1, kan VM 4 inte ansluta till volym 2.  Anledningen till detta är att eker-virtuella nätverk inte är peered och _transitroutning stöds inte via VNet-peering_.

## <a name="hybrid-environments"></a>Hybridmiljöer

Följande diagram illustrerar en hybridmiljö: 

![Hybridnätverksmiljö](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

I hybridscenariot behöver program från lokala datacenter åtkomst till resurserna i Azure.  Detta är fallet oavsett om du vill utöka ditt datacenter till Azure, eller om du vill använda Azure native-tjänster eller för haveriberedskap. Se alternativ för [VPN Gateway-planering](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable) för information om hur du ansluter flera lokala resurser till resurser i Azure via ett VPN-program för plats eller en ExpressRoute.

I en hybridhubb-ekertopologi fungerar navet VNet i Azure som en central anslutningspunkt till ditt lokala nätverk. Ekrarna är virtuella nätverk som är peered med navet, och de kan användas för att isolera arbetsbelastningar.

Beroende på konfigurationen kan du ansluta lokala resurser till resurser i navet och ekrarna.

I topologin som visas ovan är det lokala nätverket anslutet till ett nav-VNet i Azure och det finns 2 ekrar virtuella nätverk i samma region som peer-datorer med hubben VNet.  I det här fallet är anslutningsalternativen som stöds för Azure NetApp Files volymer följande:

* Lokala resurser VM 1 och VM 2 kan ansluta till volym 1 i navet över en plats-till-plats VPN eller ExpressRoute krets. 
* Lokala resurser VM 1 och VM 2 kan ansluta till volym 2 eller volym 3 via en plats-till-plats VPN och regional Vnet-peering.
* VM 3 i navet VNet kan ansluta till volym 2 i eker VNet 1 och volym 3 i eker VNet 2.
* VM 4 från eker VNet 1 och VM 5 från eker VNet 2 kan ansluta till volym 1 i navet VNet.

VM 4 i eker VNet 1 kan inte ansluta till volym 3 i eker VNet 2. Inte heller VM 5 i ekrar VNet2 kan inte ansluta till volym 2 i eker VNet 1. Detta beror på att eker-virtuella nätverk inte är peered och _transitroutning stöds inte via VNet-peering_.

## <a name="next-steps"></a>Nästa steg

[Delegera ett undernät till Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
