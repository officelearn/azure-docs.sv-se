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
ms.date: 03/23/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 80939bb48c29ba39e2d347cb80d6169d79329cfc
ms.lasthandoff: 03/25/2017


---
# <a name="create-a-site-to-site-connection-in-the-azure-portal"></a>Skapa en plats-till-plats-anslutning på Azure Portal
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Klassisk – Azure Portal](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [Klassisk – Klassisk portal](vpn-gateway-site-to-site-create.md)
>
>


En plats-till-plats-anslutning (S2S) för VPN-gateway är en anslutning via en VPN-tunnel med IPsec/IKE (IKEv1 eller IKEv2). Den här typen av anslutning kräver en lokal VPN-enhet som tilldelats en offentlig IP-adress och som inte finns bakom en NAT. Plats-till-plats-anslutningar kan användas för flera platser och hybridkonfigurationer.

Den här artikeln visar dig hur du skapar ett virtuellt nätverk och en VPN-gateway från plats till plats till ditt lokala nätverk, med hjälp av Azure Resource Manager-distributionsmodellen och Azure-portalen. 

![Diagram över plats-till-plats-anslutning med VPN-gateway](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Distributionsmodeller och metoder för plats-till-plats-anslutningar
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Följande tabell visar de tillgängliga distributionsmodellerna och -metoderna för plats-till-plats-konfigurationer. När det finns en artikel med konfigurationssteg tillgänglig länkar vi till den direkt från tabellen.

[!INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Ytterligare konfigurationer
Om du vill koppla ihop VNets, men inte skapar någon anslutning till en lokal plats, kan du läsa mer i [Konfigurera en VNet-till-VNet-anslutning](vpn-gateway-vnet-vnet-rm-ps.md). Information om att lägga till en plats-till-plats-anslutning till en VNet som redan har en anslutning finns i [Lägga till en S2S-anslutning till en VNet med en befintlig anslutning för VPN-gateway](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).

## <a name="before-you-begin"></a>Innan du börjar
Kontrollera att du har följande innan du påbörjar konfigurationen:

* En kompatibel VPN-enhet och någon som kan konfigurera den. Se [Om VPN-enheter](vpn-gateway-about-vpn-devices.md).
* Om du inte vet vilka IP-adressutrymmen som används i ditt lokala nätverk kontaktar du relevant person som kan ge dig den här informationen. Plats-till-plats-anslutningar kan inte ha överlappande adressutrymmen.
* En extern offentlig IP-adress för VPN-enheten. Den här IP-adressen får inte finnas bakom en NAT.
* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) eller registrera dig för ett [kostnadsfritt konto](http://azure.microsoft.com/pricing/free-trial).

### <a name="values"></a>Exempelvärden
När du följer dessa steg som en övning kan du använda följande exempelvärden:

* **VNet-namn:** TestVNet1
* **Adressutrymme:** 
    * 10.11.0.0/16
    * 10.12.0.0/16 (valfritt för den här övningen)
* **Undernät:**
  * FrontEnd: 10.11.0.0/24
  * BackEnd: 10.12.0.0/24 (valfritt för den här övningen)
  * GatewaySubnet: 10.11.255.0/27
* **Resursgrupp:** TestRG1
* **Plats:** Östra USA
* **DNS-server:** IP-adressen för din DNS-server
* **Namn på virtuell nätverksgateway:** VNet1GW
* **Offentlig IP:** VNet1GWIP
* **VPN-typ:** Routningsbaserad
* **Anslutningstyp:** Plats-till-plats (IPsec)
* **Gateway-typ:** VPN
* **Gateway-namn på lokalt nätverk:** Site2
* **Anslutningsnamn:** VNet1toSite2

## <a name="CreatVNet"></a>1. Skapa ett virtuellt nätverk

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-s2s-rm-portal-include.md)]

## <a name="dns"></a>2. Ange en DNS-server
DNS krävs inte för plats-till-plats-anslutningar. Om du vill använda namnmatchning för resurser som distribueras till ditt virtuella nätverk bör du dock ange en DNS-server. Med den här inställningen kan du ange vilken DNS-server du vill använda för namnmatchning för det här virtuella nätverket. Den skapar inte någon DNS-server.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="gatewaysubnet"></a>3. Skapa ett gateway-undernät
Du måste skapa ett gatewayundernät för din VPN-gateway. Gatewayundernätet innehåller de IP-adresser som VPN-gatewaytjänsterna använder. Om möjligt skapar du ett gatewayundernät med hjälp av CIDR-block /28 eller /27. På så sätt kan du vara säker på att du har tillräckligt med IP-adresser för framtida gatewaykonfigurationsbehov.

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-s2s-rm-portal-include.md)]

## <a name="VNetGateway"></a>4. Skapa en virtuell nätverksgateway

[!INCLUDE [vpn-gateway-add-gw-s2s-rm-portal](../../includes/vpn-gateway-add-gw-s2s-rm-portal-include.md)]

## <a name="LocalNetworkGateway"></a>5. Skapa en lokal nätverksgateway
Den lokala nätverksgatewayen syftar på din lokala plats. Inställningarna som du anger för den lokala nätverksgatewayen avgör vilka adressutrymmen som dirigeras till din lokala VPN-enhet.

[!INCLUDE [vpn-gateway-add-lng-s2s-rm-portal](../../includes/vpn-gateway-add-lng-s2s-rm-portal-include.md)]

## <a name="VPNDevice"></a>6. Konfigurera din VPN-enhet
[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="CreateConnection"></a>7. Skapa en VPN-anslutning från plats till plats

I det här steget ska du skapa VPN-anslutningen för plats-till-plats mellan din virtuella nätverksgateway och din lokala VPN-enhet. Kontrollera att din virtuella nätverksgateway och lokala nätverksgateway har skapats innan du påbörjar det här avsnittet.

[!INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include.md)]

## <a name="VerifyConnection"></a>8. Verifiera VPN-anslutningen

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="next-steps"></a>Nästa steg
*  När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [Virtuella datorer](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
*  Information om BGP finns i [BGP-översikt](vpn-gateway-bgp-overview.md) och [Så här konfigurerar du BGP](vpn-gateway-bgp-resource-manager-ps.md).


