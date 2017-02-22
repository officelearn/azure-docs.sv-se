---
title: "Ansluta ditt lokala nätverk till ett virtuellt Azure-nätverk: VPN från plats till plats: Portal | Microsoft Docs"
description: "Steg för att skapa en IPsec-anslutning från ditt lokala nätverk till ett virtuellt Azure-nätverk via offentligt Internet. Dessa steg hjälper dig att skapa en plats-till-plats-anslutning med VPN-gateway med hjälp av portalen."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 827a4db7-7fa5-4eaf-b7e1-e1518c51c815
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: eea00841708212789e14fa8717d83dd81d472bac
ms.openlocfilehash: c99788aded849933289555dd133b8146d6b2bdf2


---
# <a name="create-a-vnet-with-a-site-to-site-connection-using-the-azure-portal"></a>Skapa ett VNet med en anslutning från plats till plats med Azure Portal
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Klassisk – Klassisk portal](vpn-gateway-site-to-site-create.md)
> 
> 

Den här artikeln visar dig hur du skapar ett virtuellt nätverk och en VPN-gateway från plats till plats till ditt lokala nätverk, med hjälp av Azure Resource Manager-distributionsmodellen och Azure-portalen. Plats-till-plats-anslutningar kan användas för flera platser och hybridkonfigurationer.

![Diagram över plats-till-plats-anslutning med VPN-gateway](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Distributionsmodeller och metoder för plats-till-plats-anslutningar
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Följande tabell visar de tillgängliga distributionsmodellerna och -metoderna för plats-till-plats-konfigurationer. När det finns en artikel med konfigurationssteg tillgänglig länkar vi till den direkt från tabellen.

[!INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Ytterligare konfigurationer
Om du vill koppla ihop VNets, men inte skapar någon anslutning till en lokal plats, kan du läsa mer i [Konfigurera en VNet-till-VNet-anslutning](vpn-gateway-vnet-vnet-rm-ps.md). Information om att lägga till en plats-till-plats-anslutning till en VNet som redan har en anslutning finns i [Lägga till en S2S-anslutning till en VNet med en befintlig anslutning för VPN-gateway](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).

## <a name="before-you-begin"></a>Innan du börjar
Kontrollera att du har följande innan du påbörjar konfigurationen:

* En kompatibel VPN-enhet och någon som kan konfigurera den. Se [Om VPN-enheter](vpn-gateway-about-vpn-devices.md). Om du inte vet hur man konfigurerar VPN-enheten eller inte känner till IP-adressintervallen i din lokala nätverkskonfiguration måste du vända dig till någon som kan ge den informationen till dig.
* En extern offentlig IP-adress för VPN-enheten. Den här IP-adressen får inte finnas bakom en NAT.
* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) eller registrera dig för ett [kostnadsfritt konto](http://azure.microsoft.com/pricing/free-trial).

### <a name="a-namevaluesasample-configuration-values-for-this-exercise"></a><a name="values"></a>Exempel på konfigurationsvärden för övningen
När du använder stegen som en övning kan du använda följande exempel på konfigurationsvärden:

* **VNet-namn:** TestVNet1
* **Adressutrymme:** 10.11.0.0/16 och 10.12.0.0/16
* **Undernät:**
  * FrontEnd: 10.11.0.0/24
  * BackEnd: 10.12.0.0/24
  * GatewaySubnet: 10.12.255.0/27
* **Resursgrupp:** TestRG1
* **Plats:** Östra USA
* **DNS-server:** 8.8.8.8
* **Gateway-namn:** VNet1GW
* **Offentlig IP:** VNet1GWIP
* **VPN-typ:** Routningsbaserad
* **Anslutningstyp:** Plats-till-plats (IPsec)
* **Gateway-typ:** VPN
* **Gateway-namn på lokalt nätverk:** Site2
* **Anslutningsnamn:** VNet1toSite2

## <a name="a-namecreatvneta1-create-a-virtual-network"></a><a name="CreatVNet"></a>1. Skapa ett virtuellt nätverk
Om du redan har ett VNet, kontrollerar du att inställningarna är kompatibla med din VPN-gatewaydesign. Var särskilt noga med alla undernät som överlappar med andra nätverk. Om du har överlappande undernät fungerar inte anslutningen ordentligt. Om ditt VNet är konfigurerat med de korrekta inställningarna, kan du börja med stegen i avsnittet [Ange en DNS-server](#dns).

### <a name="to-create-a-virtual-network"></a>Så här skapar du ett virtuellt nätverk
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="a-namesubnetsa2-add-additional-address-space-and-subnets"></a><a name="subnets"></a>2. Lägg till ytterligare adressutrymmen och undernät
Du kan lägga till ytterligare adressutrymme och undernät till ditt VNet när det har skapats.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="a-namednsa3-specify-a-dns-server"></a><a name="dns"></a>3. Ange en DNS-server
### <a name="to-specify-a-dns-server"></a>Så här anger du en DNS-server
[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="a-namegatewaysubneta4-create-a-gateway-subnet"></a><a name="gatewaysubnet"></a>4. Skapa ett gateway-undernät
Innan du ansluter det virtuella nätverket till en gateway, måste du skapa gateway-undernätet för det virtuella nätverk som du vill ansluta till. Om möjligt är det bäst att skapa ett gateway-undernät med CIDR-block av /28 eller /27 för att tillhandahålla tillräckligt med IP-adresser för att hantera ytterligare framtida konfigurationskrav.

Om du skapar den här konfigurationen som en övning, ser du följande [värden](#values) när du skapar gateway-undernätet.

### <a name="to-create-a-gateway-subnet"></a>Så här skapar du ett gateway-undernät
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="a-namevnetgatewaya5-create-a-virtual-network-gateway"></a><a name="VNetGateway"></a>5. Skapa en virtuell nätverksgateway
Om du skapar den här konfigurationen som en övning, ser du följande [exempelvärden för konfiguration](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Så här skapar du en virtuell nätverksgateway
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="a-namelocalnetworkgatewaya6-create-a-local-network-gateway"></a><a name="LocalNetworkGateway"></a>6. Skapa en lokal nätverksgateway
Den ”lokala nätverksgatewayen” avser vanligtvis din lokala plats. Ge den lokala nätverksgatewayen ett namn som Azure kan referera till. 

Om du skapar den här konfigurationen som en övning, ser du följande [exempelvärden för konfiguration](#values).

### <a name="to-create-a-local-network-gateway"></a>Så här skapar du en lokal nätverksgateway
[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="a-namevpndevicea7-configure-your-vpn-device"></a><a name="VPNDevice"></a>7. Konfigurera din VPN-enhet
[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="a-namecreateconnectiona8-create-a-site-to-site-vpn-connection"></a><a name="CreateConnection"></a>8. Skapa en VPN-anslutning från plats till plats
Skapa VPN-anslutningen från plats till plats mellan din virtuella nätverksgateway och din VPN-enhet. Glöm inte att byta ut värdena mot dina egna. Den delade nyckeln måste överensstämma med värdet som du använde vid din konfiguration av VPN-enheten. 

Kontrollera att din virtuella nätverksgateway och lokala nätverksgateway har skapats innan du påbörjar det här avsnittet. Om du skapar den här konfigurationen som en övning, ser du följande [värden](#values) när du skapar anslutningen.

### <a name="to-create-the-vpn-connection"></a>Så här skapar du VPN-anslutningen
[!INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-rm-portal-include.md)]

## <a name="a-nameverifyconnectiona9-verify-the-vpn-connection"></a><a name="VerifyConnection"></a>9. Verifiera VPN-anslutningen
Du kan verifiera VPN-anslutningen i portalen eller med hjälp av PowerShell.

[!INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="next-steps"></a>Nästa steg
*  När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [Virtuella datorer](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
*  Information om BGP finns i [BGP-översikt](vpn-gateway-bgp-overview.md) och [Så här konfigurerar du BGP](vpn-gateway-bgp-resource-manager-ps.md).




<!--HONumber=Jan17_HO4-->


