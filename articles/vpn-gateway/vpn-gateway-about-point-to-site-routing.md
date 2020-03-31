---
title: 'Azure VPN Gateway: Om P2S-routning'
description: Den här artikeln hjälper dig att förstå hur Punkt-till-plats VPN-routning beter sig.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: anzaman
ms.openlocfilehash: 4821f2eb694a36cf0570008b3e62ce39999c58d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239753"
---
# <a name="about-point-to-site-vpn-routing"></a>Om VPN-routning från punkt till plats

Den här artikeln hjälper dig att förstå hur Azure Point-to-Site VPN-routning fungerar. P2S VPN-routningsbeteende är beroende av klientoperativsystemet, det protokoll som används för VPN-anslutningen och hur virtuella nätverk (VNets) är anslutna till varandra.

Azure stöder för närvarande två protokoll för fjärråtkomst, IKEv2 och SSTP. IKEv2 stöds på många klientoperativsystem, inklusive Windows, Linux, MacOS, Android och iOS. SSTP stöds bara i Windows. Om du gör en ändring av topologin för nätverket och har Windows VPN-klienter måste VPN-klientpaketet för Windows-klienter hämtas och installeras igen för att ändringarna ska kunna tillämpas på klienten.

> [!NOTE]
> Den här artikeln gäller endast IKEv2.
>

## <a name="about-the-diagrams"></a><a name="diagrams"></a>Om diagrammen

Det finns ett antal olika diagram i den här artikeln. Varje avsnitt visar en annan topologi eller konfiguration. I den här artikeln fungerar S2S- och VNet-till-VNet-anslutningar på samma sätt, eftersom båda är IPsec-tunnlar. Alla VPN-gateways i den här artikeln är ruttbaserade.

## <a name="one-isolated-vnet"></a><a name="isolatedvnet"></a>Ett isolerat virtuella nätverk

Vpn-gateway-anslutningen för punkt till plats i det här exemplet är för ett virtuellt nätverk som inte är anslutet eller peered med något annat virtuellt nätverk (VNet1). I det här exemplet kan klienter komma åt VNet1.

![isolerad VNet-routning](./media/vpn-gateway-about-point-to-site-routing/1.jpg "isolerad VNet-routning")

### <a name="address-space"></a>Adressutrymme

* VNet1: 10.1.0.0/16

### <a name="routes-added"></a>Tillagd väg

* Rutter tillagda i Windows-klienter: 10.1.0.0/16, 192.168.0.0/24

* Rutter tillagda i klienter som inte är Windows-klienter: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Åtkomst

* Windows-klienter kan komma åt VNet1

* Icke-Windows-klienter kan komma åt VNet1

## <a name="multiple-peered-vnets"></a><a name="multipeered"></a>Flera peer-virtuella nätverk

I det här exemplet är vpn-gateway-anslutningen för punkt till plats för VNet1. VNet1 är peered med VNet2. VNet 2 är peered med VNet3. VNet1 är peered med VNet4. Det finns ingen direkt peering mellan VNet1 och VNet3. VNet1 har "Tillåt gateway transit" och VNet2 och VNet4 har "Använd fjärrgateways" aktiverat.

Klienter som använder Windows kan komma åt direkt peered virtuella nätverk, men VPN-klienten måste hämtas igen om några ändringar görs i VNet-peering eller nätverkstopologin. Icke-Windows-klienter kan komma åt direkt peered virtuella nätverk. Åtkomst är inte transitiv och är begränsad till endast direkt peered virtuella nätverk.

![flera peered virtuella nätverk](./media/vpn-gateway-about-point-to-site-routing/2.jpg "flera peered virtuella nätverk")

### <a name="address-space"></a>Adressutrymme:

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* VNet4: 10.4.0.0/16

### <a name="routes-added"></a>Tillagd väg

* Rutter tillagda i Windows-klienter: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

* Rutter som lagts till i klienter som inte är Windows-klienter: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

### <a name="access"></a>Åtkomst

* Windows-klienter kan komma åt VNet1, VNet2 och VNet4, men VPN-klienten måste hämtas igen för att alla topologiändringar ska börja gälla.

* Klienter som inte är Windows-klienter kan komma åt VNet1, VNet2 och VNet4

## <a name="multiple-vnets-connected-using-an-s2s-vpn"></a><a name="multis2s"></a>Flera virtuella nätverk anslutna med en S2S VPN

I det här exemplet är vpn-gateway-anslutningen för punkt till plats för VNet1. VNet1 är anslutet till VNet2 med hjälp av en VPN-anslutning från plats till plats. VNet2 är anslutet till VNet3 med hjälp av en VPN-anslutning från plats till plats. Det finns ingen direkt peering eller Plats-till-plats VPN-anslutning mellan VNet1 och VNet3. Alla plats-till-plats-anslutningar kör inte BGP för routning.

Klienter som använder Windows, eller ett annat operativsystem som stöds, kan bara komma åt VNet1. För att komma åt ytterligare virtuella nätverk måste BGP användas.

![flera virtuella nätverk och S2S](./media/vpn-gateway-about-point-to-site-routing/3.jpg "flera virtuella nätverk och S2S")

### <a name="address-space"></a>Adressutrymme

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>Tillagd väg

* Rutter tillagda i Windows-klienter: 10.1.0.0/16, 192.168.0.0/24

* Rutter som lagts till i klienter som inte är Windows-klienter: 10.1.0.0/16, 10.2.0.0/16, 192.168.0.0/24

### <a name="access"></a>Åtkomst

* Windows-klienter kan bara komma åt VNet1

* Klienter som inte är Windows-klienter kan endast komma åt VNet1

## <a name="multiple-vnets-connected-using-an-s2s-vpn-bgp"></a><a name="multis2sbgp"></a>Flera virtuella nätverk anslutna med en S2S VPN (BGP)

I det här exemplet är vpn-gateway-anslutningen för punkt till plats för VNet1. VNet1 är anslutet till VNet2 med hjälp av en VPN-anslutning från plats till plats. VNet2 är anslutet till VNet3 med hjälp av en VPN-anslutning från plats till plats. Det finns ingen direkt peering eller Plats-till-plats VPN-anslutning mellan VNet1 och VNet3. Alla plats-till-plats-anslutningar kör BGP för routning.

Klienter som använder Windows, eller ett annat operativsystem som stöds, kan komma åt alla virtuella nätverk som är anslutna med hjälp av en PLATS-till-plats-VPN-anslutning, men vägar till anslutna virtuella nätverk måste läggas till manuellt i Windows-klienterna.

![flera virtuella nätverk och S2S (BGP)](./media/vpn-gateway-about-point-to-site-routing/4.jpg "flera virtuella nätverk och S2S BGP")

### <a name="address-space"></a>Adressutrymme

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>Tillagd väg

* Rutter tillagda i Windows-klienter: 10.1.0.0/16

* Rutter som lagts till i klienter som inte är Windows-klienter: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 192.168.0.0/24

### <a name="access"></a>Åtkomst

* Windows-klienter kan komma åt VNet1, VNet2 och VNet3, men vägar till VNet2 och VNet3 måste läggas till manuellt.

* Klienter som inte är Windows-klienter kan komma åt VNet1, VNet2 och VNet3

## <a name="one-vnet-and-a-branch-office"></a><a name="vnetbranch"></a>Ett virtuella nätverk och ett filialkontor

I det här exemplet är vpn-gateway-anslutningen för punkt till plats för VNet1. VNet1 är inte anslutet/peered med något annat virtuellt nätverk, men är anslutet till en lokal plats via en VPN-anslutning från plats till plats som inte kör BGP.

Windows- och icke-Windows-klienter kan bara komma åt VNet1.

![routning med ett VNet och ett filialkontor](./media/vpn-gateway-about-point-to-site-routing/5.jpg "routning med ett VNet och ett filialkontor")

### <a name="address-space"></a>Adressutrymme

* VNet1: 10.1.0.0/16

* Plats1: 10.101.0.0/16

### <a name="routes-added"></a>Tillagd väg

* Rutter tillagda i Windows-klienter: 10.1.0.0/16, 192.168.0.0/24

* Rutter tillagda i klienter som inte är Windows-klienter: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Åtkomst

* Windows-klienter kan bara komma åt VNet1

* Klienter som inte är Windows-klienter kan endast komma åt VNet1

## <a name="one-vnet-and-a-branch-office-bgp"></a><a name="vnetbranchbgp"></a>Ett virtuella nätverk och ett filialkontor (BGP)

I det här exemplet är vpn-gateway-anslutningen för punkt till plats för VNet1. VNet1 är inte anslutet eller peered med något annat virtuellt nätverk, men är ansluten till en lokal plats (Site1) via en Plats-till-plats VPN-anslutning som kör BGP.

Windows-klienter kan komma åt det virtuella nätverket och filialkontoret (Site1), men vägarna till Plats1 måste läggas till manuellt i klienten. Icke-Windows-klienter kan komma åt det virtuella nätverket och det lokala filialkontoret.

![ett virtuella nätverk och ett filialkontor (BGP)](./media/vpn-gateway-about-point-to-site-routing/6.jpg "ett virtuella nätverk och ett filialkontor")

### <a name="address-space"></a>Adressutrymme

* VNet1: 10.1.0.0/16

* Plats1: 10.101.0.0/16

### <a name="routes-added"></a>Tillagd väg

* Rutter tillagda i Windows-klienter: 10.1.0.0/16, 192.168.0.0/24

* Rutter tillagda i klienter som inte är Windows-klienter: 10.1.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Åtkomst

* Windows-klienter kan komma åt VNet1 och Site1, men vägar till plats1 måste läggas till manuellt.

* Icke-Windows-klienter kan komma åt VNet1 och Site1.


## <a name="multiple-vnets-connected-using-s2s-and-a-branch-office"></a><a name="multivnets2sbranch"></a>Flera virtuella nätverk anslutna med S2S och ett filialkontor

I det här exemplet är vpn-gateway-anslutningen för punkt till plats för VNet1. VNet1 är anslutet till VNet2 med hjälp av en VPN-anslutning från plats till plats. VNet2 är anslutet till VNet3 med hjälp av en VPN-anslutning från plats till plats. Det finns ingen direkt peering eller Plats-till-plats VPN-tunnel mellan VNet1- och VNet3-nätverken. VNet3 är anslutet till ett filialkontor (Site1) med hjälp av en VPN-anslutning från plats till plats. Alla VPN-anslutningar kör inte BGP.

Alla klienter kan endast komma åt VNet1.

![multi-VNet S2S och filialkontor](./media/vpn-gateway-about-point-to-site-routing/7.jpg "multi-VNet S2S och filialkontor")

### <a name="address-space"></a>Adressutrymme

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Plats1: 10.101.0.0/16

### <a name="routes-added"></a>Tillagd väg

* Rutter tillagda i Windows-klienter: 10.1.0.0/16, 192.168.0.0/24

* Rutter som lagts till klienter som inte är Windows-klienter: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Åtkomst

* Windows-klienterna kan endast komma åt VNet1

* Klienter som inte är Windows-klienter kan endast komma åt VNet1

## <a name="multiple-vnets-connected-using-s2s-and-a-branch-office-bgp"></a><a name="multivnets2sbranchbgp"></a>Flera virtuella nätverk anslutna med S2S och ett filialkontor (BGP)

I det här exemplet är vpn-gateway-anslutningen för punkt till plats för VNet1. VNet1 är anslutet till VNet2 med hjälp av en VPN-anslutning från plats till plats. VNet2 är anslutet till VNet3 med hjälp av en VPN-anslutning från plats till plats. Det finns ingen direkt peering eller Plats-till-plats VPN-tunnel mellan VNet1- och VNet3-nätverken. VNet3 är anslutet till ett filialkontor (Site1) med hjälp av en VPN-anslutning från plats till plats. Alla VPN-anslutningar kör BGP.

Klienter som använder Windows kan komma åt virtuella nätverk och webbplatser som är anslutna med en PLATS-till-plats-VPN-anslutning, men vägarna till VNet2, VNet3 och Site1 måste läggas till manuellt i klienten. Icke-Windows-klienter kan komma åt virtuella nätverk och webbplatser som är anslutna med hjälp av en VPN-anslutning från plats till plats utan att göra några manuella åtgärder. Åtkomsten är transitiv och klienter kan komma åt resurser i alla anslutna virtuella nätverk och platser (lokalt).

![multi-VNet S2S och filialkontor](./media/vpn-gateway-about-point-to-site-routing/8.jpg "multi-VNet S2S och filialkontor")

### <a name="address-space"></a>Adressutrymme

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Plats1: 10.101.0.0/16

### <a name="routes-added"></a>Tillagd väg

* Rutter tillagda i Windows-klienter: 10.1.0.0/16, 192.168.0.0/24

* Rutter som lagts till klienter som inte är Windows-klienter: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Åtkomst

* Windows-klienterna kan komma åt VNet1, VNet2, VNet3 och Site1, men vägar till VNet2, VNet3 och Site1 måste läggas till manuellt i klienten.

* Klienter som inte är Windows kan komma åt VNet1, Vnet2, VNet3 och Site1.

## <a name="next-steps"></a>Nästa steg

Se [Skapa ett P2S VPN med Azure-portalen](vpn-gateway-howto-point-to-site-resource-manager-portal.md) för att börja skapa din P2S VPN.
