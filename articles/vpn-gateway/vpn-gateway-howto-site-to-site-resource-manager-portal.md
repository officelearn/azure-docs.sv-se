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
ms.date: 05/02/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: ae91d49bf4f715847bcef5d6b00e3798e6a02500
ms.contentlocale: sv-se
ms.lasthandoff: 05/03/2017


---
# <a name="create-a-site-to-site-connection-in-the-azure-portal"></a>Skapa en plats-till-plats-anslutning på Azure Portal

Den här artikeln visar hur du kan använda Azure Portal för att skapa en VPN-gatewayanslutning från plats till plats från ditt lokala nätverk till det virtuella nätverket. Anvisningarna i den här artikeln gäller för Resource Manager-distributionsmodellen. Du kan också skapa den här konfigurationen med ett annat distributionsverktyg eller en annan distributionsmodell genom att välja ett annat alternativ i listan nedan:

> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Resource Manager – CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Klassisk – Azure Portal](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [Klassisk – Klassisk portal](vpn-gateway-site-to-site-create.md)
> 
>

![Diagram över plats-till-plats-anslutning med VPN-gateway](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png)

En VPN-gatewayanslutning från plats till plats används för att ansluta ditt lokala nätverk till ett virtuellt Azure-nätverk via en IPsec/IKE VPN-tunnel (IKEv1 eller IKEv2). Den här typen av anslutning kräver en lokal VPN-enhet som tilldelats till en extern offentlig IP-adress. Mer information om VPN-gatewayer finns i [Om VPN-gateway](vpn-gateway-about-vpngateways.md).

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har uppfyllt följande villkor innan du påbörjar konfigurationen:

* Verifiera att du vill arbeta med distributionsmodellen i Resource Manager. [!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 
* En kompatibel VPN-enhet och någon som kan konfigurera den. Se [Om VPN-enheter](vpn-gateway-about-vpn-devices.md) för mer information om kompatibla VPN-enheter och enhetskonfiguration.
* En extern offentlig IPv4 IP-adress för VPN-enheten. Den här IP-adressen får inte finnas bakom en NAT.
* Om du inte vet vilka IP-adressintervaller som används i din lokala nätverkskonfiguration kontaktar du relevant person som kan ge dig den här informationen. När du skapar den här konfigurationen måste du ange prefix för IP-adressintervall som Azure dirigerar till den lokala platsen. Inget av undernäten i ditt lokala nätverk kan överlappa de virtuella nätverksundernät du vill ansluta till. 

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

## <a name="gatewaysubnet"></a>3. Skapa gateway-undernätet

Den virtuella nätverksgatewayen använder ett gatewayundernät som innehåller de IP-adresser som VPN-gatewaytjänsterna använder. När du skapar ett gateway-undernät måste det få namnet ”GatewaySubnet”. Om du ger det något annat namn misslyckas konfigurationen av anslutningen.

Storleken på gatewayundernätet du anger beror på konfigurationen av VPN-gatewayen du vill skapa. Även om det är möjligt att skapa ett gateway-undernät som är så litet som /29 så rekommenderar vi att du skapar ett större undernät som inkluderar fler adresser genom att välja /27 eller /28. Om du använder det större nätverksundernätet får du tillräckligt många IP-adresser för att hantera möjliga framtida konfigurationer.

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-s2s-rm-portal-include.md)]


## <a name="VNetGateway"></a>4. Skapa VPN-gatewayen

[!INCLUDE [vpn-gateway-add-gw-s2s-rm-portal](../../includes/vpn-gateway-add-gw-s2s-rm-portal-include.md)]

## <a name="LocalNetworkGateway"></a>5. Skapa den lokala nätverksgatewayen

Den lokala nätverksgatewayen avser vanligtvis din lokala plats. Du namnger webbplatsen så att Azure kan referera till den och sedan anger du IP-adressen för den lokala VPN-enhet som du skapar en anslutning till. Du anger också IP-adressprefixen som ska dirigeras via VPN-gatewayen till VPN-enheten. Adressprefixen du anger är de prefix som finns på det lokala nätverket. Det är enkelt att uppdatera dessa prefix om det lokala nätverket ändras.

[!INCLUDE [vpn-gateway-add-lng-s2s-rm-portal](../../includes/vpn-gateway-add-lng-s2s-rm-portal-include.md)]

## <a name="VPNDevice"></a>6. Konfigurera din VPN-enhet
[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

Om du använder Azure Portal hittar du VPN-gatewayens offentliga IP-adress genom att gå till **Virtuella nätverksgatewayer** och klicka på namnet på din gateway.

## <a name="CreateConnection"></a>7. Skapa VPN-anslutningen

Skapa VPN-anslutningen för plats-till-plats mellan din virtuella nätverksgateway och din lokala VPN-enhet.

[!INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include.md)]

## <a name="VerifyConnection"></a>8. Verifiera VPN-anslutningen

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]


## <a name="next-steps"></a>Nästa steg

*  Information om BGP finns i [BGP-översikt](vpn-gateway-bgp-overview.md) och [Så här konfigurerar du BGP](vpn-gateway-bgp-resource-manager-ps.md).
*  Information om tvingad tunneltrafik finns i [Om forcerade tunnlar](vpn-gateway-forced-tunneling-rm.md)
*  Mer information om aktiv-aktiv-anslutningar med hög tillgänglighet finns i [Anslutning med hög tillgänglighet på flera platser och VNet-till-VNet-anslutning](vpn-gateway-highlyavailable.md).
