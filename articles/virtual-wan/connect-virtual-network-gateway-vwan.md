---
title: Ansluta en virtuell nätverksgateway till ett Virtuellt AZURE WAN
description: Den här artikeln hjälper dig att ansluta en Azure-virtual network gateway till en Azure Virtual WAN VPN-gateway
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: cherylmc
ms.openlocfilehash: 688183bc07aa14d5e5df182d7de0897cec93f0b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066236"
---
# <a name="connect-a-vpn-gateway-virtual-network-gateway-to-virtual-wan"></a>Ansluta en VPN-gateway (virtuell nätverksgateway) till VirtuellT WAN

Den här artikeln hjälper dig att konfigurera anslutning från en Azure VPN Gateway (virtuell nätverksgateway) till en Azure Virtual WAN (VPN-gateway). Att skapa en anslutning från en VPN-gateway (virtuell nätverksgateway) till en Virtuell WAN (VPN-gateway) liknar att konfigurera anslutning till ett virtuellt WAN från vpn-platser för filialer.

För att minimera eventuell sammanblandning mellan två funktioner kommer vi att förordet porten med namnet på den funktion som vi hänvisar till. Till exempel VPN Gateway virtuell nätverksgateway och Virtual WAN VPN-gateway.

## <a name="before-you-begin"></a>Innan du börjar

Innan du börjar skapar du följande resurser:

Azure Virtual WAN

* [Skapa ett virtuellt WAN](virtual-wan-site-to-site-portal.md#openvwan).
* [Skapa ett nav](virtual-wan-site-to-site-portal.md#hub). Den virtuella hubben innehåller Virtual WAN VPN-gatewayen.

Azure Virtual Network

* Skapa ett virtuellt nätverk utan virtuella nätverksgateways. Kontrollera att inget av undernäten i dina lokala nätverk överlappar de virtuella nätverk som du vill ansluta till. Information om hur du skapar ett virtuellt nätverk i Azure-portalen finns i [Snabbstart](../virtual-network/quick-create-portal.md).

## <a name="1-create-an-azure-virtual-network-gateway"></a><a name="vnetgw"></a>1. Skapa en azure-gateway för virtuella nätverk

Skapa en virtuell VPN Gateway-gateway för ditt virtuella nätverk i aktivt aktivt läge för det virtuella nätverket. När du skapar gatewayen kan du antingen använda befintliga offentliga IP-adresser för de två instanserna av gatewayen eller skapa nya offentliga IP-adresser. Du använder dessa offentliga IP-adresser när du konfigurerar virtuella WAN-platser. Mer information om aktivt-aktivt läge finns i [Konfigurera aktiva-aktiva anslutningar](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md#aagateway).

### <a name="active-active-mode-setting"></a><a name="active-active"></a>Inställning av aktivt aktivt läge

![aktiv-aktiv](./media/connect-virtual-network-gateway-vwan/active.png "aktiv-aktiv")

### <a name="bgp-setting"></a><a name="BGP"></a>BGP-inställning

BGP ASN kan inte vara 65515. 66515 kommer att användas av Azure Virtual WAN.

![BGP](./media/connect-virtual-network-gateway-vwan/bgp.png "Bgp")

### <a name="public-ip-addresses"></a><a name="pip"></a>Offentliga IP-adresser

När gatewayen skapas navigerar du till sidan **Egenskaper.** Egenskaperna och konfigurationsinställningarna liknar följande exempel. Lägg märke till de två offentliga IP-adresser som används för gatewayen.

![Egenskaper](./media/connect-virtual-network-gateway-vwan/publicip.png "properties")

## <a name="2-create-virtual-wan-vpn-sites"></a><a name="vwansite"></a>2. Skapa virtuella WAN VPN-webbplatser

Om du vill skapa virtuella WAN VPN-platser navigerar du till ditt virtuella WAN och väljer **VPN-platser**under **Anslutning.** I det här avsnittet skapar du två virtuella WAN VPN-platser som motsvarar de virtuella nätverksgateways som du skapade i föregående avsnitt.

1. Välj **+Skapa webbplats**.
2. Skriv följande värden på sidan **Skapa VPN-platser:**

   * **Region** - (samma region som azure VPN Gateway virtuell nätverksgateway)
   * **Enhetsleverantör** - Ange enhetsleverantören (valfritt namn)
   * **Privat adressutrymme** - (Ange ett värde eller lämna tomt när BGP är aktiverat)
   * **Border Gateway Protocol** - (Set to **Enable** om azure VPN Gateway virtuell nätverksgateway har BGP aktiverat)
   * **Anslut till hubbar** (Välj hubb som du skapade i förutsättningarna från listrutan)
3. Under **Länkar**anger du följande värden:

   * **Providernamn** - Ange ett länknamn och ett leverantörsnamn (valfritt namn)
   * **Hastighet** - Hastighet (valfritt tal)
   * **IP-adress** - Ange IP-adress (samma som den första offentliga IP-adressen som visas under (VPN Gateway) egenskaper för virtuell nätverksgateway)
   * **BGP-adress** och **ASN** - BGP-adress och ASN. Dessa måste vara samma som en av BGP-peer IP-adresser, och ASN från VPN Gateway virtuellt nätverk gateway som du konfigurerat i [steg 1](#vnetgw).
4. Granska och välj **Bekräfta** om du vill skapa webbplatsen.
5. Upprepa föregående steg för att skapa den andra platsen för att matcha med den andra instansen av VPN Gateway virtuellt nätverk gateway. Du behåller samma inställningar, förutom att använda den andra offentliga IP-adressen och den andra BGP-peer-IP-adressen från VPN Gateway-konfigurationen.
6. Du har nu två platser som har etablerats och kan gå vidare till nästa avsnitt för att hämta konfigurationsfiler.

## <a name="3-download-the-vpn-configuration-files"></a><a name="downloadconfig"></a>3. Ladda ner VPN-konfigurationsfiler

I det här avsnittet hämtar du VPN-konfigurationsfilen för var och en av de platser som du skapade i föregående avsnitt.

1. Högst upp på sidan Virtual WAN **VPN-platser** väljer du **platsen**och väljer sedan **Ladda ned plats-till-plats-VPN-konfiguration**. Azure skapar en konfigurationsfil med inställningarna.

   ![ladda ner konfigurationsfil](./media/connect-virtual-network-gateway-vwan/download.png "ladda ned")
2. Hämta och öppna konfigurationsfilen.
3. Upprepa dessa steg för den andra platsen. När du har båda konfigurationsfilerna öppna kan du gå vidare till nästa avsnitt.

## <a name="4-create-the-local-network-gateways"></a><a name="createlocalgateways"></a>4. Skapa de lokala nätverksgatewayerna

I det här avsnittet skapar du två lokala azure VPN Gateway-gateways. Konfigurationsfilerna från föregående steg innehåller konfigurationsinställningarna för gatewayen. Använd de här inställningarna för att skapa och konfigurera azure VPN Gateway lokala nätverksgatewayer.

1. Skapa den lokala nätverksgatewayen med hjälp av dessa inställningar. Information om hur du skapar en vpn gateway-gateway för lokalt nätverk finns i vpn gateway-artikeln [Skapa en lokal nätverksgateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway).

   * **IP-adress** - Använd IP-adressen för instans0 som visas för *gatewaykonfigurering* från konfigurationsfilen.
   * **BGP** - Om anslutningen är över BGP väljer du **Konfigurera BGP-inställningar** och anger ASN '65515'. Ange BGP-peer-IP-adressen. Använd "Instance0 BgpPeeringAddresses" för *gatewayconfiguration* från konfigurationsfilen.
   * **Prenumeration, resursgrupp och plats** är samma som för den virtuella WAN-hubben.
2. Granska och skapa den lokala nätverksgatewayen. Din lokala nätverksgateway bör se ut ungefär som det här exemplet.

   ![ladda ner konfigurationsfil](./media/connect-virtual-network-gateway-vwan/lng1.png "instans0")
3. Upprepa dessa steg för att skapa en annan lokal nätverksgateway, men den här gången använder du "Instance1"-värdena i stället för förekomst0-värden från konfigurationsfilen.

   ![ladda ner konfigurationsfil](./media/connect-virtual-network-gateway-vwan/lng2.png "instans1")

## <a name="5-create-connections"></a><a name="createlocalgateways"></a>5. Skapa anslutningar

I det här avsnittet skapar du en anslutning mellan VPN Gateway lokala nätverksgateways och virtuell nätverksgateway. Steg om hur du skapar en VPN Gateway-anslutning finns i [Konfigurera en anslutning](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#CreateConnection).

1. Navigera till din virtuella nätverksgateway i portalen och klicka på **Anslutningar**. Klicka på **+Lägg till** överst på sidan Anslutningar och öppna sidan **Lägg till anslutning**.
2. Konfigurera följande värden för anslutningen på sidan **Lägg till anslutning:**

   * **Namn:** Namnge din anslutning.
   * **Anslutningstyp:** Välj **Plats till plats(IPSec)**
   * **Virtuell nätverksgateway:** Värdet är låst eftersom du ansluter från den här gatewayen.
   * **Lokal nätverksgateway:** Den här anslutningen ansluter den virtuella nätverksgatewayen till den lokala nätverksgatewayen. Välj en av de lokala nätverksgatewayer som du skapade tidigare.
   * **Delad nyckel:** Ange en delad nyckel.
   * **IKE-protokollet:** Välj IKE-protokollet.
   * **BGP:** Välj **Aktivera BGP** om anslutningen är över BGP.
3. Klicka på **OK** för att skapa din anslutning.
4. Anslutningen visas på sidan **Anslutningar** för den virtuella nätverksgatewayen.

   ![Anslutning](./media/connect-virtual-network-gateway-vwan/connect.png "anslutning")
5. Upprepa föregående steg för att skapa en andra anslutning. För den andra anslutningen väljer du den andra lokala nätverksgatewayen som du skapade.

## <a name="6-test-connections"></a><a name="test"></a>6. Testanslutningar

Du kan testa anslutningen genom att skapa två virtuella datorer, en på sidan av VPN Gateway virtuellt nätverk gateway, och en i ett virtuellt nätverk för virtual WAN, och sedan pinga de två virtuella datorer.

1. Skapa en virtuell dator i det virtuella nätverket (Test1-VNet) för Azure VPN Gateway (Test1-VNG). Skapa inte den virtuella datorn i GatewaySubnet.
2. Skapa ett annat virtuellt nätverk för att ansluta till det virtuella WAN.Create another virtual network to connect to the virtual WAN. Skapa en virtuell dator i ett undernät till det här virtuella nätverket. Det här virtuella nätverket kan inte innehålla några virtuella nätverksgateways. Du kan snabbt skapa ett virtuellt nätverk med hjälp av PowerShell-stegen i anslutningsartikeln [för plats till plats.](virtual-wan-site-to-site-portal.md#vnet) Var noga med att ändra värdena innan du kör cmdlets.
3. Anslut VNet till virtual WAN-hubben. På sidan för det virtuella WAN-programmet väljer du **Virtuella nätverksanslutningar**och **+Lägg till anslutning**. Fyll i följande fält på sidan **Lägg till anslutning**:

    * **Anslutningsnamn** – Namnge anslutningen.
    * **Hubbar** – Välj den hubb du vill koppla till anslutningen.
    * **Prenumeration** – Kontrollera prenumerationen.
    * **Virtuellt nätverk** – Välj det virtuella nätverk du vill ansluta till hubben. Det virtuella nätverket får inte ha någon befintlig gateway för virtuellt nätverk.
4. Klicka på **OK** om du vill skapa den virtuella nätverksanslutningen.
5. Anslutningen är nu inställd mellan de virtuella datorerna. Du bör kunna pinga den ena virtuella datorn från den andra, såvida det inte finns brandväggar eller andra principer som blockerar kommunikationen.

## <a name="next-steps"></a>Nästa steg

Steg för att konfigurera en anpassad IPsec-princip finns i [Konfigurera en anpassad IPsec-princip för Virtuell WAN](virtual-wan-custom-ipsec-portal.md).
Mer information om VirtuellT WAN finns i [Om Azure Virtual WAN](virtual-wan-about.md) och vanliga frågor och svar om Azure Virtual [WAN](virtual-wan-faq.md).