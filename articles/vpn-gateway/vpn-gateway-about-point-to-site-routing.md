---
title: Om Azure punkt-till-plats-Routning | Microsoft Docs
description: Den här artikeln hjälper dig att förstå hur punkt-till-plats VPN-routning fungerar.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/28/2019
ms.author: anzaman
ms.openlocfilehash: 486a910226db5dc7b36aaf873e7bb8115eb78805
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55189663"
---
# <a name="about-point-to-site-vpn-routing"></a>Om VPN-routning från punkt till plats

Den här artikeln hjälper dig att förstå hur Azure punkt-till-plats VPN-routning fungerar. P2S VPN-routning beteende beror på klientoperativsystem, protokollet som används för VPN-anslutningen och hur de virtuella nätverk (Vnet) är anslutna till varandra.

Azure stöder för närvarande två protokoll för fjärråtkomst, IKEv2 och SSTP. IKEv2 stöds på många klientoperativsystem, inklusive Windows, Linux, MacOS, Android och iOS. SSTP stöds endast på Windows. Om du gör en ändring i topologin för ditt nätverk och har Windows VPN-klienter, måste VPN-klientpaketet för Windows-klienter hämtas och installeras igen för att ändringarna ska tillämpas på klienten.

> [!NOTE]
> Den här artikeln gäller bara för IKEv2.
>

## <a name="diagrams"></a>Om diagrammen

Det finns ett antal olika diagrammen i den här artikeln. Varje avsnitt visar en annan topologi eller konfiguration. För den här artikeln, plats-till-plats (S2S) och VNet-till-VNet-anslutningar att fungera på samma sätt, som båda är IPsec-tunnlar. Alla VPN-gatewayer i den här artikeln är routningsbaserad.

## <a name="isolatedvnet"></a>Ett isolerat virtuellt nätverk

Punkt-till-plats VPN gateway-anslutning i det här exemplet är för ett virtuellt nätverk som inte är ansluten eller peer-kopplat med ett annat virtuellt nätverk (VNet1). I det här exemplet kan klienter som använder SSTP eller IKEv2 åtkomst till VNet1.

![isolerat virtuellt nätverk routning](./media/vpn-gateway-about-point-to-site-routing/1.jpg "isolerat virtuellt nätverk Routning")

### <a name="address-space"></a>Adressutrymme

* VNet1: 10.1.0.0/16

### <a name="routes-added"></a>Vägar som har lagts till

* Vägar som lagts till i Windows-klienter: 10.1.0.0/16, 192.168.0.0/24

* Vägar som lagts till i icke-Windows-klienter: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows-klienter kan komma åt VNet1

* Icke-Windows-klienter kan komma åt VNet1

## <a name="multipeered"></a>Flera peer-kopplade virtuella nätverk

I det här exemplet är punkt-till-plats-VPN-gatewayanslutning för VNet1. VNet1 är peerkopplat med VNet2. 2 för virtuella nätverk är peerkopplat med VNet3. VNet1 är peerkopplat med VNet4. Det finns ingen direkt peering mellan VNet1 och VNet3. VNet1 har ”Tillåt gatewayöverföring” och VNet2 har ”Använd fjärrgateway” aktiverat.

Klienter som använder Windows kan komma åt direkt peerkopplade virtuella nätverk, men den VPN-klienten måste laddas ned igen om några ändringar görs i VNet-peering eller nätverkets topologi. Icke-Windows-klienter kan komma åt direkt peerkopplade virtuella nätverk. Åtkomst är inte transitiva och är begränsat till endast direkt peer-kopplade virtuella nätverk.

![flera peer-kopplade virtuella nätverk](./media/vpn-gateway-about-point-to-site-routing/2.jpg "flera peer-kopplade virtuella nätverk")

### <a name="address-space"></a>Adressutrymme:

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* VNet4: 10.4.0.0/16

### <a name="routes-added"></a>Vägar som har lagts till

* Vägar som lagts till i Windows-klienter: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

* Vägar som lagts till i icke-Windows-klienter: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows-klienter kan komma åt VNet1, VNet2 och VNet4, men den VPN-klienten måste laddas ned igen för alla ändringar i nätverkstopologin ska börja gälla.

* Icke-Windows-klienter kan komma åt VNet1, VNet2 och VNet4

## <a name="multis2s"></a>Flera virtuella nätverk som är anslutna via en S2S-VPN

I det här exemplet är punkt-till-plats-VPN-gatewayanslutning för VNet1. Ansluts VNet1 till VNet2 med hjälp av en plats-till-plats-VPN-anslutning. VNet2 är ansluten till VNet3 med hjälp av en plats-till-plats-VPN-anslutning. Det finns ingen direkt peering eller plats-till-plats-VPN-anslutning mellan VNet1 och VNet3. Alla anslutningar för plats-till-plats körs inte BGP för routning.

Klienter som använder Windows eller ett annat operativsystem som stöds, kan bara åtkomst till VNet1. BGP måste användas för att komma åt ytterligare virtuella nätverk.

![flera virtuella nätverk och S2S](./media/vpn-gateway-about-point-to-site-routing/3.jpg "flera virtuella nätverk och S2S")

### <a name="address-space"></a>Adressutrymme

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>Vägar som har lagts till

* Vägar som lagts till i Windows-klienter: 10.1.0.0/16, 192.168.0.0/24

* Vägar som lagts till i icke-Windows-klienter: 10.1.0.0/16, 10.2.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows-klienter kan endast komma åt VNet1

* Icke-Windows-klienter kan komma åt VNet1 endast

## <a name="multis2sbgp"></a>Flera virtuella nätverk som är anslutna via en S2S VPN (BGP)

I det här exemplet är punkt-till-plats-VPN-gatewayanslutning för VNet1. Ansluts VNet1 till VNet2 med hjälp av en plats-till-plats-VPN-anslutning. VNet2 är ansluten till VNet3 med hjälp av en plats-till-plats-VPN-anslutning. Det finns ingen direkt peering eller plats-till-plats-VPN-anslutning mellan VNet1 och VNet3. Alla anslutningar för plats-till-plats med BGP för routning.

Klienter som använder Windows eller ett annat operativsystem som stöds, kan komma åt alla virtuella nätverk som är anslutna via en plats-till-plats-VPN-anslutning, men vägar till anslutna virtuella nätverk måste läggas till manuellt på Windows-klienter.

![flera virtuella nätverk och S2S (BGP)](./media/vpn-gateway-about-point-to-site-routing/4.jpg "flera virtuella nätverk och BGP för S2S")

### <a name="address-space"></a>Adressutrymme

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>Vägar som har lagts till

* Vägar som lagts till i Windows-klienter: 10.1.0.0/16

* Vägar som lagts till i icke-Windows-klienter: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows-klienter kan komma åt VNet1, VNet2 och VNet3, men vägar till VNet2 och VNet3 måste läggas till manuellt.

* Icke-Windows-klienter kan komma åt VNet1, VNet2 och VNet3

## <a name="vnetbranch"></a>Ett virtuellt nätverk och ett avdelningskontor

I det här exemplet är punkt-till-plats-VPN-gatewayanslutning för VNet1. VNet1 är inte ansluten / peer-kopplat med ett annat virtuellt nätverk, men den är ansluten till en lokal plats via en plats-till-plats-VPN-anslutning som inte kör BGP.

Windows- och icke-Windows-klienter kan endast få åtkomst till VNet1.

![routning med ett virtuellt nätverk och ett avdelningskontor](./media/vpn-gateway-about-point-to-site-routing/5.jpg "routning med ett virtuellt nätverk och ett avdelningskontor")

### <a name="address-space"></a>Adressutrymme

* VNet1: 10.1.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Vägar som har lagts till

* Vägar som lagts till i Windows-klienter: 10.1.0.0/16, 192.168.0.0/24

* Vägar som lagts till i icke-Windows-klienter: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows-klienter kan komma åt endast VNet1

* Icke-Windows-klienter kan komma åt VNet1 endast

## <a name="vnetbranchbgp"></a>Ett virtuellt nätverk och ett avdelningskontor (BGP)

I det här exemplet är punkt-till-plats-VPN-gatewayanslutning för VNet1. VNet1 är inte ansluten eller peer-kopplat med ett annat virtuellt nätverk, men den är ansluten till en lokal plats (Site1) via en plats-till-plats-VPN-anslutning med BGP.

Windows-klienter kan komma åt det virtuella nätverket och avdelningskontor (Site1), men vägarna till Site1 måste läggas till manuellt till klienten. Icke-Windows-klienter kan komma åt det virtuella nätverket, samt lokala filialkontoret.

![ett virtuellt nätverk och ett avdelningskontor (BGP)](./media/vpn-gateway-about-point-to-site-routing/6.jpg "ett virtuellt nätverk och ett avdelningskontor")

### <a name="address-space"></a>Adressutrymme

* VNet1: 10.1.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Vägar som har lagts till

* Vägar som lagts till i Windows-klienter: 10.1.0.0/16, 192.168.0.0/24

* Vägar som lagts till i icke-Windows-klienter: 10.1.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows-klienter kan komma åt VNet1 och Site1, men vägar till Site1 måste läggas till manuellt.

* Icke-Windows-klienter kan komma åt VNet1 och Site1.


## <a name="multivnets2sbranch"></a>Flera virtuella nätverk som är anslutna via S2S och ett avdelningskontor

I det här exemplet är punkt-till-plats-VPN-gatewayanslutning för VNet1. Ansluts VNet1 till VNet2 med hjälp av en plats-till-plats-VPN-anslutning. VNet2 är ansluten till VNet3 med hjälp av en plats-till-plats-VPN-anslutning. Det finns ingen direkt peering eller plats-till-plats-VPN-tunnel mellan VNet1 och VNet3 nätverk. VNet3 är ansluten till ett filialkontor (Site1) med hjälp av en plats-till-plats-VPN-anslutning. Alla VPN-anslutningar inte igång BGP.

Alla klienter kan komma åt VNet1 endast.

![Multi-VNet S2S och grenen office](./media/vpn-gateway-about-point-to-site-routing/7.jpg "multi-VNet S2S och grenen office")

### <a name="address-space"></a>Adressutrymme

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Vägar som har lagts till

* Vägar har lagts till klienter: 10.1.0.0/16, 192.168.0.0/24

* Vägar som lagts till i icke-Windows-klienter: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows-klienter kan komma åt VNet1 endast

* Icke-Windows-klienter kan komma åt VNet1 endast

## <a name="multivnets2sbranchbgp"></a>Flera virtuella nätverk som är anslutna via S2S och ett avdelningskontor (BGP)

I det här exemplet är punkt-till-plats-VPN-gatewayanslutning för VNet1. Ansluts VNet1 till VNet2 med hjälp av en plats-till-plats-VPN-anslutning. VNet2 är ansluten till VNet3 med hjälp av en plats-till-plats-VPN-anslutning. Det finns ingen direkt peering eller plats-till-plats-VPN-tunnel mellan VNet1 och VNet3 nätverk. VNet3 är ansluten till ett filialkontor (Site1) med hjälp av en plats-till-plats-VPN-anslutning. Alla VPN-anslutningar använder BGP.

Klienter som använder Windows kan komma åt virtuella nätverk och platser som är anslutna via en plats-till-plats-VPN-anslutning, men vägarna till VNet2, VNet3 och Site1 måste läggas till manuellt till klienten. Icke-Windows-klienter kan komma åt virtuella nätverk och platser som är anslutna via en plats-till-plats-VPN-anslutning utan några manuella åtgärder. Åtkomst är transitiva och klienterna kan komma åt resurser i alla anslutna virtuella nätverk och platser (lokalt).

![Multi-VNet S2S och grenen office](./media/vpn-gateway-about-point-to-site-routing/8.jpg "multi-VNet S2S och grenen office")

### <a name="address-space"></a>Adressutrymme

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Vägar som har lagts till

* Vägar har lagts till klienter: 10.1.0.0/16, 192.168.0.0/24

* Vägar som lagts till i icke-Windows-klienter: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows-klienter kan komma åt VNet1, VNet2, VNet3 och Site1, men vägar till VNet2, VNet3 och Site1 måste läggas till manuellt till klienten.

* Icke-Windows-klienter kan komma åt VNet1, Vnet2, VNet3 och Site1.

## <a name="next-steps"></a>Nästa steg

Se [skapa en P2S VPN-anslutning med Azure portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md) att börja skapa dina P2S-VPN.
