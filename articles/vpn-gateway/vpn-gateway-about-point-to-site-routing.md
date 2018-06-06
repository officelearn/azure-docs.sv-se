---
title: Om Azure punkt-till-plats Routning | Microsoft Docs
description: Den här artikeln hjälper dig att förstå hur punkt-till-plats VPN-routning fungerar.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/16/2018
ms.author: anzaman
ms.openlocfilehash: a0576e00d22b731f7ee9de3a9b021c0f52fc8ef9
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34702184"
---
# <a name="about-point-to-site-vpn-routing"></a>Om VPN-routning från punkt till plats

Den här artikeln hjälper dig att förstå hur Azure punkt-till-plats VPN-routning fungerar. VPN-P2S dirigeringsbeteendet är beroende av klientens operativsystem, vilket protokoll som används för VPN-anslutningen och hur de virtuella nätverk (Vnet) är anslutna till varandra.

Azure stöder för närvarande två protokoll för fjärråtkomst, IKEv2- och SSTP. IKEv2 stöds på många klientoperativsystem inklusive Windows, Linux, MacOS, Android och iOS. SSTP stöds endast i Windows. Om du gör en ändring i topologin för ditt nätverk och har Windows VPN-klienter måste VPN-klientpaketet för Windows-klienter hämtas och installeras igen för att ändringarna ska tillämpas på klienten.

> [!NOTE]
> Den här artikeln gäller bara för IKEv2.
>

## <a name="diagrams"></a>Om diagram

Det finns ett antal olika diagrammen i den här artikeln. Varje avsnitt visar en annan topologi eller konfiguration. Vid tillämpningen av den här artikeln, plats-till-plats (S2S) och VNet-till-VNet-anslutningar att fungera på samma sätt som båda är IPsec-tunnlar. VPN-gatewayer i den här artikeln är route-baserade.

## <a name="isolatedvnet"></a>En isolerade virtuella nätverk

Punkt-till-plats VPN-anslutning för gateway i det här exemplet är för ett virtuellt nätverk som inte är ansluten eller peerkopplat med ett annat virtuellt nätverk (VNet1). I det här exemplet kan klienter som använder SSTP eller IKEv2 komma åt VNet1.

![isolerade virtuella nätverk routning](./media/vpn-gateway-about-point-to-site-routing/1.jpg "isolerade virtuella nätverk Routning")

### <a name="address-space"></a>Adressutrymme

* VNet1: 10.1.0.0/16

### <a name="routes-added"></a>Vägar som lagts till

* Vägar som lagts till i Windows-klienter: 10.1.0.0/16, 192.168.0.0/24

* Vägar som lagts till i Windows-klienter: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows-klienter kan komma åt VNet1

* Icke-Windows-klienter kan komma åt VNet1

## <a name="multipeered"></a>Flera peerkoppla Vnet

I det här exemplet är punkt-till-plats VPN-gateway-anslutningen för VNet1. VNet1 peerkopplat med VNet2. VNet-2 är peerkopplat med VNet3. VNet1 peerkopplat med VNet4. Det finns ingen direkt peering mellan VNet1 och VNet3. VNet1 har ”Tillåt gateway överföring” och VNet2 ”använda remote gateways” aktiverad.

Klienter som använder Windows kan komma åt direkt peerkoppla Vnet, men VPN-klienten måste hämtas igen om några ändringar har gjorts i VNet-peering eller nätverkets topologi. Icke-Windows-klienter kan komma åt direkt peerkoppla Vnet. Åtkomst är begränsad till endast direkt peerkoppla Vnet är inte transitiva.

![flera peerkoppla Vnet](./media/vpn-gateway-about-point-to-site-routing/2.jpg "flera peerkoppla Vnet")

### <a name="address-space"></a>Adressutrymmet:

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* VNet4: 10.4.0.0/16

### <a name="routes-added"></a>Vägar som lagts till

* Vägar som lagts till i Windows-klienter: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

* Vägar som lagts till i Windows-klienter: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows-klienter kan komma åt VNet1, VNet2 och VNet4, men VPN-klienten måste hämtas igen för att ändringarna topologi ska börja gälla.

* Icke-Windows-klienter kan komma åt VNet1, VNet2 och VNet4

## <a name="multis2s"></a>Flera Vnet som är anslutna via en S2S-VPN

I det här exemplet är punkt-till-plats VPN-gateway-anslutningen för VNet1. VNet1 är ansluten till VNet2 med hjälp av en plats-till-plats VPN-anslutning. VNet2 är ansluten till VNet3 med hjälp av en plats-till-plats VPN-anslutning. Det finns ingen direkt peering eller plats-till-plats VPN-anslutning mellan VNet1 och VNet3. Alla anslutningar för plats-till-plats körs inte BGP för routning.

Klienter som använder Windows eller en annan stödda operativsystem kan endast komma åt VNet1. BGP måste användas för att komma åt ytterligare Vnet.

![flera Vnet och S2S](./media/vpn-gateway-about-point-to-site-routing/3.jpg "flera Vnet och S2S")

### <a name="address-space"></a>Adressutrymme

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>Vägar som lagts till

* Vägar som lagts till i Windows-klienter: 10.1.0.0/16, 192.168.0.0/24

* Vägar till icke-Windows-klienter: 10.1.0.0/16, 10.2.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows-klienter kan endast komma åt VNet1

* Icke-Windows-klienter kan komma åt VNet1 endast

## <a name="multis2sbgp"></a>Flera Vnet som är anslutna via S2S VPN (BGP)

I det här exemplet är punkt-till-plats VPN-gateway-anslutningen för VNet1. VNet1 är ansluten till VNet2 med hjälp av en plats-till-plats VPN-anslutning. VNet2 är ansluten till VNet3 med hjälp av en plats-till-plats VPN-anslutning. Det finns ingen direkt peering eller plats-till-plats VPN-anslutning mellan VNet1 och VNet3. Alla anslutningar för plats-till-plats med BGP för routning.

Klienter som använder Windows eller en annan stödda operativsystem kan komma åt alla Vnet som är anslutna via en plats-till-plats VPN-anslutning, men vägarna till anslutna Vnet måste läggas till Windows-klienter manuellt.

![flera Vnet och S2S (BGP)](./media/vpn-gateway-about-point-to-site-routing/4.jpg "flera Vnet och BGP för S2S")

### <a name="address-space"></a>Adressutrymme

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>Vägar som lagts till

* Vägar som lagts till i Windows-klienter: 10.1.0.0/16

* Vägar till icke-Windows-klienter: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows-klienter kan komma åt VNet1, VNet2 och VNet3, men vägarna till VNet2 och VNet3 måste läggas till manuellt.

* Icke-Windows-klienter kan komma åt VNet1, VNet2 och VNet3

## <a name="vnetbranch"></a>Ett VNet och ett avdelningskontor

I det här exemplet är punkt-till-plats VPN-gateway-anslutningen för VNet1. VNet1 är inte ansluten / peerkopplat med virtuella nätverk, men är ansluten till en lokal plats via en plats-till-plats VPN-anslutning som inte kör BGP.

Windows-klienter kan komma åt VNet1 och avdelningskontor (Site1), men vägarna till Site1 måste läggas till manuellt till klienten. Icke-Windows-klienter kan komma åt VNet1, samt lokala Site1.

![routning med ett VNet och ett lokalkontor](./media/vpn-gateway-about-point-to-site-routing/5.jpg "routning med ett VNet och ett avdelningskontor")

### <a name="address-space"></a>Adressutrymme

* VNet1: 10.1.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Vägar som lagts till

* Vägar som lagts till i Windows-klienter: 10.1.0.0/16, 192.168.0.0/24

* Vägar till icke-Windows-klienter: 10.1.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows-klienter kan komma åt endast VNet1

* Icke-Windows-klienter kan komma åt VNet1 endast

## <a name="vnetbranchbgp"></a>Ett VNet och ett avdelningskontor (BGP)

I det här exemplet är punkt-till-plats VPN-gateway-anslutningen för VNet1. VNet1 är inte ansluten eller peerkopplat med virtuella nätverk, men är ansluten till en lokal plats (Site1) via ett plats-till-plats VPN-anslutning kör BGP.

Windows-klienter kan komma åt VNet och avdelningskontor (Site1), men vägarna till Site1 måste läggas till manuellt till klienten. Icke-Windows-klienter kan komma åt VNet samt lokala filialkontoret.

![ett VNet och ett avdelningskontor (BGP)](./media/vpn-gateway-about-point-to-site-routing/6.jpg "ett VNet och ett avdelningskontor")

### <a name="address-space"></a>Adressutrymme

* VNet1: 10.1.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Vägar som lagts till

* Vägar som lagts till i Windows-klienter: 10.1.0.0/16, 192.168.0.0/24

* Vägar till icke-Windows-klienter: 10.1.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows-klienter kan komma åt VNet1 och Site1, men vägarna till Site1 måste läggas till manuellt.

* Icke-Windows-klienter kan komma åt VNet1 och Site1.


## <a name="multivnets2sbranch"></a>Flera Vnet som är anslutna via S2S och ett avdelningskontor

I det här exemplet är punkt-till-plats VPN-gateway-anslutningen för VNet1. VNet1 är ansluten till VNet2 med hjälp av en plats-till-plats VPN-anslutning. VNet2 är ansluten till VNet3 med hjälp av en plats-till-plats VPN-anslutning. Det finns ingen direkt peering eller plats-till-plats VPN-tunnel mellan VNet1 och VNet3 nätverk. VNet3 är ansluten till ett filialkontor (Site1) med hjälp av en plats-till-plats VPN-anslutning. Alla VPN-anslutningar körs inte BGP.

Alla klienter kan komma åt VNet1 endast.

![Multi-VNet S2S och branch office](./media/vpn-gateway-about-point-to-site-routing/7.jpg "multi-VNet S2S och branch office")

### <a name="address-space"></a>Adressutrymme

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Vägar som lagts till

* Vägar lagts till klienter: 10.1.0.0/16, 192.168.0.0/24

* Vägar till icke-Windows-klienter: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows-klienter kan komma åt VNet1 endast

* Icke-Windows-klienter kan komma åt VNet1 endast

## <a name="multivnets2sbranchbgp"></a>Flera Vnet som är anslutna via S2S och ett avdelningskontor (BGP)

I det här exemplet är punkt-till-plats VPN-gateway-anslutningen för VNet1. VNet1 är ansluten till VNet2 med hjälp av en plats-till-plats VPN-anslutning. VNet2 är ansluten till VNet3 med hjälp av en plats-till-plats VPN-anslutning. Det finns ingen direkt peering eller plats-till-plats VPN-tunnel mellan VNet1 och VNet3 nätverk. VNet3 är ansluten till ett filialkontor (Site1) med hjälp av en plats-till-plats VPN-anslutning. Alla VPN-anslutningar använder BGP.

Klienter som använder Windows kan komma åt Vnet och platser som är anslutna via en plats-till-plats VPN-anslutning, men vägarna till VNet2, VNet3 och Site1 måste läggas till manuellt till klienten. Icke-Windows-klienter kan komma åt Vnet och platser som är anslutna via en plats-till-plats VPN-anslutning utan någon manuell åtgärd. Åtkomst är transitiva och klienter kan komma åt resurser i alla anslutna Vnet och platser (lokal).

![Multi-VNet S2S och branch office](./media/vpn-gateway-about-point-to-site-routing/8.jpg "multi-VNet S2S och branch office")

### <a name="address-space"></a>Adressutrymme

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Vägar som lagts till

* Vägar lagts till klienter: 10.1.0.0/16, 192.168.0.0/24

* Vägar till icke-Windows-klienter: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Windows-klienter kan komma åt VNet1, VNet2, VNet3 och Site1, men vägarna till VNet2, VNet3 och Site1 måste läggas till manuellt till klienten.

* Icke-Windows-klienter kan komma åt VNet1, Vnet2, VNet3 och Site1.

## <a name="next-steps"></a>Nästa steg

Se [skapa P2S VPN med hjälp av Azure portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md) att börja skapa P2S-VPN.
