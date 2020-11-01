---
title: 'Ansluta lokalt nätverk till Azure Virtual Network: plats-till-plats-VPN: Portal'
description: Skapa en IPsec plats-till-plats-VPN Gateway anslutning från ditt lokala nätverk till ett virtuellt Azure-nätverk via det offentliga Internet via portalen.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/22/2020
ms.author: cherylmc
ms.openlocfilehash: b8c12048283cb8cc4220cb325762b272cde4a68f
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2020
ms.locfileid: "92541323"
---
# <a name="create-a-site-to-site-connection-in-the-azure-portal"></a>Skapa en plats-till-plats-anslutning på Azure Portal

Den här artikeln visar hur du kan använda Azure Portal för att skapa en VPN-gatewayanslutning från plats till plats från ditt lokala nätverk till det virtuella nätverket. Anvisningarna i den här artikeln gäller för Resource Manager-distributionsmodellen. Du kan också skapa den här konfigurationen med ett annat distributionsverktyg eller en annan distributionsmodell genom att välja ett annat alternativ i listan nedan:

> [!div class="op_single_selector"]
> * [Azure-portalen](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure Portal (klassisk)](vpn-gateway-howto-site-to-site-classic-portal.md)
>

En VPN-gatewayanslutning från plats till plats används för att ansluta ditt lokala nätverk till ett virtuellt Azure-nätverk via en IPsec/IKE VPN-tunnel (IKEv1 eller IKEv2). Den här typen av anslutning kräver en lokal VPN-enhet som tilldelats till en extern offentlig IP-adress. Mer information om VPN-gatewayer finns i [Om VPN-gateway](vpn-gateway-about-vpngateways.md).

:::image type="content" source="./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png" alt-text="Diagram över plats-till-plats-anslutning med VPN Gateway":::

## <a name="prerequisites"></a>Förutsättningar

Kontrollera att du har uppfyllt följande villkor innan du påbörjar konfigurationen:

* Ett Azure-konto med en aktiv prenumeration. Om du inte har ett kan du [skapa ett kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Kontrollera att du har en kompatibel VPN-enhet och någon som kan konfigurera den. Se [Om VPN-enheter](vpn-gateway-about-vpn-devices.md) för mer information om kompatibla VPN-enheter och enhetskonfiguration.
* Kontrollera att du har en extern offentlig IPv4-adress för VPN-enheten.
* Om du inte vet vilka IP-adressintervaller som används i din lokala nätverkskonfiguration kontaktar du relevant person som kan ge dig den här informationen. När du skapar den här konfigurationen måste du ange prefix för IP-adressintervall som Azure dirigerar till den lokala platsen. Inget av undernäten i ditt lokala nätverk kan överlappa de virtuella nätverksundernät du vill ansluta till.

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk (VNet) med följande värden:

* **Resurs grupp:** TestRG1
* **Namn:** VNet1
* **Region:** (USA) USA, östra
* **IPv4-adress utrymme:** 10.1.0.0/16
* **Under näts namn:** Delen
* **Adress utrymme för undernät:** 10.1.0.0/24

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-a-vpn-gateway"></a><a name="VNetGateway"></a>Skapa en VPN-gateway

I det här steget ska du skapa den virtuella nätverksgatewayen för ditt virtuella nätverk. Att skapa en gateway kan ofta ta 45 minuter eller mer, beroende på vald gateway-SKU.

### <a name="about-the-gateway-subnet"></a>Om gateway-undernätet

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="create-the-gateway"></a>Skapa gatewayen

Skapa en VPN-gateway med följande värden:

* **Namn:** VNet1GW
* **Region:** USA, östra
* **Gateway-typ:** Konfigurera
* **VPN-typ:** Route-baserad
* **SKU:** VpnGw1
* **Generation:** Generation1
* **Virtuellt nätverk:** VNet1
* **Adress intervall för Gateway-under nätet:** 10.1.255.0/27
* **Offentlig IP-adress:** Skapa ny
* **Namn på offentlig IP-adress:** VNet1GWpip
* **Aktivera aktivt-aktivt läge:** Inaktiverats
* **Konfigurera BGP:** Inaktiverats

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="create-a-local-network-gateway"></a><a name="LocalNetworkGateway"></a>Skapa en lokal nätverksgateway

Den lokala Nätverksgatewayen är ett objekt som representerar din lokala plats (platsen) för routning. Du namnger webbplatsen så att Azure kan referera till den och sedan anger du IP-adressen för den lokala VPN-enhet som du skapar en anslutning till. Du anger också IP-adressprefixen som ska dirigeras via VPN-gatewayen till VPN-enheten. Adressprefixen du anger är de prefix som finns på det lokala nätverket. Om ditt lokala nätverk ändras eller om du behöver ändra den offentliga IP-adressen för VPN-enheten, kan du enkelt uppdatera värden senare.

Skapa en lokal nätverksgateway med följande värden:

* **Namn:** Site1
* **Resursgrupp:** TestRG1
* **Plats:** USA, östra

[!INCLUDE [Add a local network gateway](../../includes/vpn-gateway-add-local-network-gateway-portal-include.md)]

## <a name="configure-your-vpn-device"></a><a name="VPNDevice"></a>Konfigurera din VPN-enhet

Plats-till-plats-anslutningar till ett lokalt nätverk kräver en VPN-enhet. I det här steget konfigurerar du VPN-enheten. När du konfigurerar VPN-enheten behöver du följande värden:

* En delad nyckel. Det här är samma delade nyckel som du anger när du skapar VPN-anslutningen för plats-till-plats. I vårt exempel använder vi en enkel delad nyckel. Vi rekommenderar att du skapar och använder en mer komplex nyckel.
* Den offentliga IP-adressen för din virtuella nätverksgateway. Du kan visa den offentliga IP-adressen genom att använda Azure Portal, PowerShell eller CLI. Om du vill hitta den offentliga IP-adressen för din VPN-gateway med hjälp av Azure Portal, navigerar du till **virtuella nätverksgateway** och väljer sedan namnet på din gateway.

[!INCLUDE [Configure a VPN device](../../includes/vpn-gateway-configure-vpn-device-include.md)]

## <a name="create-a-vpn-connection"></a><a name="CreateConnection"></a>Skapa en VPN-anslutning

Skapa VPN-anslutningen för plats-till-plats mellan din virtuella nätverksgateway och din lokala VPN-enhet.

Skapa en anslutning med följande värden:

* **Namn på lokal nätverksgateway:** Site1
* **Anslutnings namn:** VNet1toSite1
* **Delad nyckel:** I det här exemplet använder vi abc123. Men du kan använda det som är kompatibelt med din VPN-maskinvara. Huvudsaken är att värdena matchar på båda sidorna av anslutningen.

[!INCLUDE [Add a site-to-site connection](../../includes/vpn-gateway-add-site-to-site-connection-portal-include.md)]

## <a name="verify-the-vpn-connection"></a><a name="VerifyConnection"></a>Verifiera VPN-anslutningen

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="how-to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Så här ansluter du till en virtuell dator

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

## <a name="how-to-reset-a-vpn-gateway"></a><a name="reset"></a>Återställa en VPN-gateway

Du kan behöva återställa en Azure VPN-gateway om VPN-anslutningen mellan flera platser i en eller flera VPN-tunnlar för plats-till-plats bryts. I det här fallet fungerar de lokala VPN-enheterna korrekt, men de kan inte upprätta IPSec-tunnlar med Azures VPN-gatewayer. Stegvisa anvisningar finns i [Återställa en VPN-gateway](reset-gateway.md).

## <a name="how-to-change-a-gateway-sku-resize-a-gateway"></a><a name="resize"></a>Ändra en gateway-SKU (ändra storlek på en gateway)

Anvisningar som beskriver hur du ändrar en gateway-SKU finns i [Gateway-SKU:er](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

## <a name="how-to-add-an-additional-connection-to-a-vpn-gateway"></a><a name="addconnect"></a>Lägga till ytterligare en anslutning till en VPN-gateway

Du kan lägga till ytterligare anslutningar, förutsatt att ingen av adressutrymmena överlappar mellan anslutningar.

1. Om du vill lägga till ytterligare en anslutning navigerar du till VPN-gatewayen och väljer sedan **anslutningar** för att öppna sidan anslutningar.
1. Välj **+ Lägg** till för att lägga till din anslutning. Justera anslutningstyp för att återspegla antingen VNet-to-VNet (om du ansluter till en annan VNet-gateway) eller Plats-till-plats.
1. Om du ansluter med Plats-till-plats och inte redan har skapat en lokal nätverksgateway för webbplatsen du vill ansluta till kan du skapa en ny.
1. Ange den delade nyckel som du vill använda och välj sedan **OK** för att skapa anslutningen.

## <a name="next-steps"></a>Nästa steg

* Information om BGP finns i [BGP-översikt](vpn-gateway-bgp-overview.md) och [Så här konfigurerar du BGP](vpn-gateway-bgp-resource-manager-ps.md).
* Information om Tvingad tunnel trafik finns i [om Tvingad tunnel trafik](vpn-gateway-forced-tunneling-rm.md).
* Mer information om aktiv-aktiv-anslutningar med hög tillgänglighet finns i [Anslutning med hög tillgänglighet på flera platser och VNet-till-VNet-anslutning](vpn-gateway-highlyavailable.md).
* Mer information om hur du begränsar nätverkstrafiken till resurser i ett virtuellt nätverk finns i [Nätverkssäkerhet](../virtual-network/security-overview.md).
* Mer information om hur Azure dirigerar trafik mellan Azure, lokala och Internet-resurser finns i [Trafikdirigering i virtuella nätverk](../virtual-network/virtual-networks-udr-overview.md).
* Information om hur du skapar en VPN-anslutning från plats till plats med en Azure Resource Manager-mall finns i [Skapa en plats-till-plats-anslutning via VPN](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/).
* Information om hur du skapar en VPN-anslutning mellan virtuella nätverk med hjälp av Azure Resource Manager mall finns i [distribuera HBase geo-replikering](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/).
