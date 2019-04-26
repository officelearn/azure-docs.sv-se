---
title: 'Anslut ditt lokala nätverk till ett virtuellt Azure-nätverk: Plats-till-plats-VPN: Portal | Microsoft Docs'
description: Steg för att skapa en IPsec-anslutning från ditt lokala nätverk till ett virtuellt Azure-nätverk via offentligt Internet. Dessa steg hjälper dig att skapa en plats-till-plats-anslutning med VPN Gateway med hjälp av portalen.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: cherylmc
ms.openlocfilehash: 032b6a4f5147d06a4613a827a0372437dca47f47
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60407753"
---
# <a name="create-a-site-to-site-connection-in-the-azure-portal"></a>Skapa en plats-till-plats-anslutning på Azure Portal

Den här artikeln visar hur du kan använda Azure Portal för att skapa en VPN-gatewayanslutning från plats till plats från ditt lokala nätverk till det virtuella nätverket. Anvisningarna i den här artikeln gäller för Resource Manager-distributionsmodellen. Du kan också skapa den här konfigurationen med ett annat distributionsverktyg eller en annan distributionsmodell genom att välja ett annat alternativ i listan nedan:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure Portal (klassisk)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>

En VPN-gatewayanslutning från plats till plats används för att ansluta ditt lokala nätverk till ett virtuellt Azure-nätverk via en IPsec/IKE VPN-tunnel (IKEv1 eller IKEv2). Den här typen av anslutning kräver en lokal VPN-enhet som tilldelats till en extern offentlig IP-adress. Mer information om VPN-gatewayer finns i [Om VPN-gateway](vpn-gateway-about-vpngateways.md).

![Diagram över plats-till-plats-anslutning med VPN Gateway](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png)

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har uppfyllt följande villkor innan du påbörjar konfigurationen:

* Kontrollera att du har en kompatibel VPN-enhet och någon som kan konfigurera den. Se [Om VPN-enheter](vpn-gateway-about-vpn-devices.md) för mer information om kompatibla VPN-enheter och enhetskonfiguration.
* Kontrollera att du har en extern offentlig IPv4-adress för VPN-enheten.
* Om du inte vet vilka IP-adressintervaller som används i din lokala nätverkskonfiguration kontaktar du relevant person som kan ge dig den här informationen. När du skapar den här konfigurationen måste du ange prefix för IP-adressintervall som Azure dirigerar till den lokala platsen. Inget av undernäten i ditt lokala nätverk kan överlappa de virtuella nätverksundernät du vill ansluta till. 

### <a name="values"></a>Exempelvärden

Vi använder följande värden i exemplen. Du kan använda värdena till att skapa en testmiljö eller hänvisa till dem för att bättre förstå exemplen i den här artikeln. Mer information om VPN-gatewayinställningar finns i [Om VPN Gateway-inställningar](vpn-gateway-about-vpn-gateway-settings.md).

* **VNet-namn:** TestVNet1
* **Adressutrymme:** 10.1.0.0/16
* **Prenumeration:** Den prenumeration som du vill använda
* **Resursgrupp:** TestRG1
* **Plats:** Östra USA
* **Undernät:** FrontEnd: 10.1.0.0/24, BackEnd: 10.1.1.0/24 (valfritt för den här övningen)
* **Namn på gateway-undernät:** GatewaySubnet (det här kommer Autofyll i portalen)
* **Adressintervall för Gatewayundernät:** 10.1.255.0/27
* **DNS-server:** 8.8.8.8 – valfritt. IP-adressen för din DNS-server.
* **Det virtuella nätverkets Gateway Name:** VNet1GW
* **Offentlig IP-adress:** VNet1GWIP
* **VPN-typ:** Routningsbaserad
* **Anslutningstyp:** Plats-till-plats (IPsec)
* **Gateway-typ:** VPN
* **Lokala Gateway-namn:** Site1
* **Anslutningsnamn:** VNet1toSite1
* **Delad nyckel:** I det här exemplet använder vi abc123. Men du kan använda det som är kompatibelt med din VPN-maskinvara. Huvudsaken är att värdena matchar på båda sidorna av anslutningen.

## <a name="CreatVNet"></a>1. Skapa ett virtuellt nätverk

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-create-virtual-network-portal-include.md)]

## <a name="dns"></a>2. Ange en DNS-server

DNS krävs inte för att skapa plats-till-plats-anslutningar. Om du vill använda namnmatchning för resurser som distribueras till ditt virtuella nätverk bör du dock ange en DNS-server. Med den här inställningen kan du ange vilken DNS-server du vill använda för namnmatchning för det här virtuella nätverket. Den skapar inte någon DNS-server. Mer information om namnmatchning finns i [Namnmatchning för virtuella datorer](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

[!INCLUDE [Specify a dns server - optional](../../includes/vpn-gateway-specify-dns-portal-include.md)]

## <a name="gatewaysubnet"></a>3. Skapa gateway-undernätet

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-include.md)]

[!INCLUDE [Add a gateway subnet](../../includes/vpn-gateway-add-gateway-subnet-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="VNetGateway"></a>4. Skapa VPN gateway

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gateway-portal-include.md)]

## <a name="LocalNetworkGateway"></a>5. Skapa den lokala nätverksgatewayen

Den lokala nätverksgatewayen avser vanligtvis din lokala plats. Du namnger webbplatsen så att Azure kan referera till den och sedan anger du IP-adressen för den lokala VPN-enhet som du skapar en anslutning till. Du anger också IP-adressprefixen som ska dirigeras via VPN-gatewayen till VPN-enheten. Adressprefixen du anger är de prefix som finns på det lokala nätverket. Om ditt lokala nätverk ändras eller om du behöver ändra den offentliga IP-adressen för VPN-enheten, kan du enkelt uppdatera värden senare.

[!INCLUDE [Add a local network gateway](../../includes/vpn-gateway-add-local-network-gateway-portal-include.md)]

## <a name="VPNDevice"></a>6. Konfigurera din VPN-enhet

Plats-till-plats-anslutningar till ett lokalt nätverk kräver en VPN-enhet. I det här steget konfigurerar du VPN-enheten. När du konfigurerar VPN-enheten behöver du följande:

- En delad nyckel. Det här är samma delade nyckel som du anger när du skapar VPN-anslutningen för plats-till-plats. I vårt exempel använder vi en enkel delad nyckel. Vi rekommenderar att du skapar och använder en mer komplex nyckel.
- Den offentliga IP-adressen för din virtuella nätverksgateway. Du kan visa den offentliga IP-adressen genom att använda Azure Portal, PowerShell eller CLI. Om du använder Azure Portal hittar du VPN-gatewayens offentliga IP-adress genom att gå till **Virtuella nätverksgatewayer** och klicka på namnet på din gateway.

[!INCLUDE [Configure a VPN device](../../includes/vpn-gateway-configure-vpn-device-include.md)]

## <a name="CreateConnection"></a>7. Skapa VPN-anslutningen

Skapa VPN-anslutningen för plats-till-plats mellan din virtuella nätverksgateway och din lokala VPN-enhet.

[!INCLUDE [Add a site-to-site connection](../../includes/vpn-gateway-add-site-to-site-connection-portal-include.md)]

## <a name="VerifyConnection"></a>8. Verifiera VPN-anslutningen

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="connectVM"></a>Ansluta till en virtuell dator

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-s2s-include.md)]

## <a name="reset"></a>Återställa en VPN-gateway

Du kan behöva återställa en Azure VPN-gateway om VPN-anslutningen mellan flera platser i en eller flera VPN-tunnlar för plats-till-plats bryts. I det här fallet fungerar de lokala VPN-enheterna korrekt, men de kan inte upprätta IPSec-tunnlar med Azures VPN-gatewayer. Stegvisa anvisningar finns i [Återställa en VPN-gateway](vpn-gateway-resetgw-classic.md).

## <a name="resize"></a>Ändra en gateway-SKU (ändra storlek på en gateway)

Anvisningar som beskriver hur du ändrar en gateway-SKU finns i [Gateway-SKU:er](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

## <a name="addconnect"></a>Lägga till ytterligare en anslutning till en VPN-gateway

Du kan lägga till ytterligare anslutningar, förutsatt att ingen av adressutrymmena överlappar mellan anslutningar.

1. Om du vill lägga till ytterligare en anslutning går du till VPN-gatewayen och klickar du sedan på **Anslutningar** för att öppna sidan Anslutningar.
2. Klicka på **+Lägg till** för att lägga till din anslutning. Justera anslutningstyp för att återspegla antingen VNet-to-VNet (om du ansluter till en annan VNet-gateway) eller Plats-till-plats.
3. Om du ansluter med Plats-till-plats och inte redan har skapat en lokal nätverksgateway för webbplatsen du vill ansluta till kan du skapa en ny.
4. Ange den delade nyckeln som du vill använda och klicka sedan på **OK** för att skapa anslutningen.

## <a name="next-steps"></a>Nästa steg

* Information om BGP finns i [BGP-översikt](vpn-gateway-bgp-overview.md) och [Så här konfigurerar du BGP](vpn-gateway-bgp-resource-manager-ps.md).
* Information om tvingad tunneltrafik finns i [Om forcerade tunnlar](vpn-gateway-forced-tunneling-rm.md).
* Mer information om aktiv-aktiv-anslutningar med hög tillgänglighet finns i [Anslutning med hög tillgänglighet på flera platser och VNet-till-VNet-anslutning](vpn-gateway-highlyavailable.md).
* Mer information om hur du begränsar nätverkstrafiken till resurser i ett virtuellt nätverk finns i [Nätverkssäkerhet](../virtual-network/security-overview.md).
* Mer information om hur Azure dirigerar trafik mellan Azure, lokala och Internet-resurser finns i [Trafikdirigering i virtuella nätverk](../virtual-network/virtual-networks-udr-overview.md).
* Information om hur du skapar en VPN-anslutning från plats till plats med en Azure Resource Manager-mall finns i [Skapa en plats-till-plats-anslutning via VPN](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/).
* Information om hur du skapar en VPN-anslutning mellan två virtuella nätverk med en Azure Resource Manager-mall finns i [Distribuera HBase-georeplikering](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/).
