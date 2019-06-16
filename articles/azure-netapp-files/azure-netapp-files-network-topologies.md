---
title: Riktlinjer för Azure NetApp Files network planera | Microsoft Docs
description: Beskriver riktlinjer som hjälper dig att utforma en effektiv nätverksarkitektur med hjälp av Azure NetApp-filer.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
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
ms.openlocfilehash: fa2de14ada5d24531dfecc7f2f709a87f39ea6cb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65826462"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Riktlinjer för Azure NetApp Files-nätverksplanering

Planering av arkitekturen är en viktig komponent i alla programinfrastruktur kan utformas. Den här artikeln hjälper dig utforma en effektiv nätverksarkitektur för dina arbetsbelastningar kan dra nytta av de avancerade funktionerna i Azure NetApp-filer.

Azure NetApp filer volymer är utformade för att ingå i ett särskilt ändamål undernät med namnet [delegerad undernät](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) inom ditt virtuella Azure-nätverk. Du kan därför åtkomst till volymerna direkt från ditt virtuella nätverk, från peer-kopplade virtuella nätverk i samma region eller från en lokal plats via en virtuell nätverksgateway (ExpressRoute eller VPN-Gateway) efter behov. Undernätet är dedikerad till Azure NetApp Files och det finns ingen nätverksanslutning till andra Azure-tjänster eller internet.

## <a name="considerations"></a>Överväganden  

När du planerar för Azure NetApp filer, nätverk, bör du förstå några saker.

### <a name="constraints"></a>Villkor

Nedanstående funktioner finns för närvarande inte stöd för Azure NetApp-filer: 

* Nätverkssäkerhetsgrupper (NSG) på undernät
* Användardefinierade vägar (Udr) med nästa hopp som Azure NetApp filer undernät
* Azure-principer (till exempel anpassade namngivningsprinciper) i Azure NetApp Files-gränssnitt
* Belastningsutjämnare för Azure NetApp Files trafik

Det gäller följande nätverksbegränsningar till Azure NetApp-filer:

* Hur många virtuella datorer som kan ansluta till en volym (med ett virtuellt nätverk eller mellan peerkopplade virtuella nätverk) får inte överskrida 1000.
* Endast ett undernät kan delegeras till Azure NetApp Files i varje Azure Virtual Network (VNet).


### <a name="supported-network-topologies"></a>Nätverkstopologier som stöds

I följande tabell beskrivs nätverkstopologier som stöds av Azure NetApp filer.  Här beskrivs också lösningar för topologier med stöds inte. 

|    Topologies    |    Det finns stöd för    |     Lösning:    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    Anslutningen till volym i ett lokalt virtuellt nätverk    |    Ja    |         |
|    Anslutningen till volymen i en peer-kopplade virtuella nätverket (samma region)    |    Ja    |         |
|    Anslutningen till volymen i en peer-kopplade virtuella nätverk (mellan region eller global peering)    |    Nej    |    Ingen    |
|    Anslutning till en volym via ExpressRoute-gateway    |    Ja    |         |
|    Anslutningen från en lokal plats till en volym i ett virtuellt ekernätverk över ExpressRoute-gateway och VNet-peering med gateway-överföring    |    Nej    |    Skapa en delegerad undernät i det virtuella hubbnätverket (Azure VNet med Gateway)    |
|    Anslutningen från en lokal plats till en volym i ett virtuellt ekernätverk över VPN gateway    |    Ja    |         |
|    Anslutningen från en lokal plats till en volym i ett virtuellt ekernätverk över VPN-gateway och VNet-peering med gateway-överföring    |    Ja    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Virtuellt nätverk för Azure NetApp Files volymer

Det här avsnittet förklarar begrepp som hjälper dig med att planera för virtuella nätverk.

### <a name="azure-virtual-networks"></a>Azures virtuella nätverk

Innan du etablerar en Azure NetApp Files volym som du behöver skapa ett Azure-nätverk (VNet) eller använda en som redan finns i din prenumeration. Det virtuella nätverket definierar nätverksgräns för volymen.  Mer information om hur du skapar virtuella nätverk finns i den [dokumentation om Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

### <a name="subnets"></a>Undernät

Undernät segmentera det virtuella nätverket i separata adressutrymmen som kan användas av Azure-resurser i dessa.  Azure NetApp filer volymer som finns i ett undernät för särskilda ändamål som kallas [delegerad undernät](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet). 

Undernät delegering ger explicit behörighet till Azure NetApp Files-tjänsten för att skapa tjänstspecifika resurser i undernätet.  Den använder en unik identifierare i distribuera tjänsten. I det här fallet skapas ett nätverksgränssnitt för att aktivera anslutning till Azure NetApp-filer.

Om du använder ett nytt virtuellt nätverk kan du skapa ett undernät och delegera undernätet för att Azure NetApp filer genom att följa instruktionerna i [Delegera ett undernät till Azure NetApp Files](azure-netapp-files-delegate-subnet.md). Du kan också delegera ett befintligt tomt undernät som inte redan har delegerats till andra tjänster.

Om det virtuella nätverket är peerkopplat med ett annat virtuellt nätverk, kan du expandera virtuella nätverkets adressutrymme. Därför finns måste det nya delegerade undernätet skapas i virtuella nätverkets adressutrymme. Om du behöver utöka adressutrymmet måste du ta bort VNet-peering innan du expanderar adressutrymmet.

### <a name="udrs-and-nsgs"></a>Udr och NSG: er

Nätverkssäkerhetsgrupper (NSG) med ett nexthop kan inte användas som delegerad undernät för Azure NetApp-filer. På samma sätt stöds användardefinierade vägar (Udr) inte också. 

Du kan använda NSG: er för andra undernät som antingen tillåter eller nekar trafik till och från Azure NetApp Files delegerad undernätet som en lösning.  

## <a name="azure-native-environments"></a>Intern Azure-miljöer

Följande diagram illustrerar en Azure-intern-miljö:

![Azure-intern nätverksmiljö](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>Local VNet

Ett enkelt scenario är att skapa eller Anslut till en Azure NetApp Files-volym från en virtuell dator (VM) i samma virtuella nätverk. För virtuellt nätverk 2 i diagrammet ovan, volym 1 skapas i en delegerad undernät och kan monteras i VM 1 i standardundernät.

### <a name="vnet-peering"></a>VNET-peering

Om du har ytterligare virtuella nätverk i samma region som behöver åtkomst till varandras resurser kan de virtuella nätverken kan anslutas med hjälp av [VNet-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) för säkra anslutningar via Azure-infrastrukturen. 

Överväg att VNet 2 och 3 för virtuellt nätverk i diagrammet ovan. Om VM 1 behöver ansluta till VM 2 och volym 2, eller om VM 2 behöver ansluta till VM-1 eller volym 1, måste du aktivera VNet-peering mellan virtuella nätverk 2 och 3 för virtuellt nätverk. 

Överväg även ett scenario där VNet 1 peer-kopplas med VNet-2 och VNet 2 peer-kopplas med VNet-3 i samma region. Resurser från 1 virtuellt nätverk kan ansluta till resurser i VNet-2, men den inte kan ansluta till resurser i VNet 3, såvida inte VNet 1 och 3 för virtuellt nätverk är peer-kopplade. 

I diagrammet ovan men VM 3 kan ansluta till volym 1, VM 4 kan inte ansluta till volymen 2.  Det beror på att de virtuella ekernätverken inte peer-kopplade, och _överföringsroutning stöds inte över VNet-peering_.

## <a name="hybrid-environments"></a>Hybridmiljöer

Följande diagram illustrerar en hybridmiljö: 

![Hybrid nätverksmiljö](../media/azure-netapp-files/azure-netapp-files-networ-hybrid-environment.png)

I ett hybridscenario behöver program från lokala Datacenter åtkomst till resurser i Azure.  Detta gäller oavsett om du vill utöka ditt datacenter till Azure, eller om du vill använda interna Azure-tjänster eller för katastrofåterställning. Se [VPN Gateway planning alternativ](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable) om hur du ansluter flera resurser lokalt till resurser i Azure via en plats-till-plats-VPN eller en ExpressRoute.

I en hybrid hub-spoke fungerar det virtuella hubbnätverket i Azure som en central plats för anslutning till det lokala nätverket. Ekrarna är virtuella nätverk som peer-kopplas med hubben och de kan användas till att isolera arbetsbelastningar.

Beroende på konfiguration. Du kan ansluta resurser från en lokal plats till resurser i hubben och ekrarna.

Det lokala nätverket är anslutet till ett virtuellt nätverk i Azure-hubb i topologin som visas ovan, och det finns 2 eker virtuella nätverk som peer-kopplas med det virtuella hubbnätverket.  I det här scenariot är de anslutningsalternativ som stöds för Azure NetApp Files volymer på följande sätt:

* Lokala resurser VM 1 och 2 för virtuell dator kan ansluta till volym 1 i hubben via plats-till-plats VPN eller en ExpressRoute. 
* Lokala resurser VM 1 och 2 för virtuell dator kan ansluta till volymen 2 eller 3 för volymen.
* VM-3 i hubben virtuellt nätverk kan ansluta till volymen 2 i eker 1 virtuellt nätverk och volym 3 i eker 2 för virtuella nätverk.
* VM 4 från eker 1 virtuellt nätverk och VM-5 från eker 2 för virtuella nätverk kan ansluta till volym 1 i det virtuella hubbnätverket.

VM-4 i eker 1 virtuellt nätverk kan inte ansluta till volymen 3 i eker 2 för virtuella nätverk. Dessutom VM 5 i eker VNet2 kan inte ansluta till volymen 2 i eker 1 virtuellt nätverk. Detta är fallet, eftersom de virtuella ekernätverken inte peer-kopplade och _överföringsroutning stöds inte över VNet-peering_.

## <a name="next-steps"></a>Nästa steg

[Delegera ett undernät till Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
