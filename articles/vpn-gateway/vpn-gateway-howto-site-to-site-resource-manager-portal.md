<properties
   pageTitle="Skapa ett virtuellt nätverk med en VPN-anslutning från plats till plats med Azure Resource Manager och Azure Portal | Microsoft Azure"
   description="Hur du skapar ett VNet med hjälp av Resource Manager-distributionen och ansluter det till ditt lokala nätverk via en S2S VPN Gateway-anslutning."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="cherylmc"/>

# Skapa ett VNet med en anslutning från plats till plats med Azure Portal

> [AZURE.SELECTOR]
- [Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Klassisk Azure-portal](vpn-gateway-site-to-site-create.md)
- [PowerShell – Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)


Den här artikeln visar dig hur du skapar ett virtuellt nätverk och en VPN-anslutning från plats till plats till ditt lokala nätverk, med hjälp av **Azure Resource Manager-distributionsmodellen** och Azure-portalen. Plats-till-plats-anslutningar kan användas för flera platser och hybridkonfigurationer.

![Diagram](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/s2srmportal.png)



### Distributionsmodeller och verktyg för plats-till-plats-anslutningar

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

[AZURE.INCLUDE [vpn-gateway-table-site-to-site-table](../../includes/vpn-gateway-table-site-to-site-include.md)] 

Om du vill koppla ihop VNets, men inte skapar någon anslutning till en lokal plats, kan du läsa mer i [Konfigurera en VNet-till-VNet-anslutning](vpn-gateway-vnet-vnet-rm-ps.md).

## Innan du börjar

Kontrollera att du har följande innan du påbörjar konfigurationen:

- En kompatibel VPN-enhet och någon som kan konfigurera den. Se [Om VPN-enheter](vpn-gateway-about-vpn-devices.md). Om du inte vet hur man konfigurerar VPN-enheten eller inte känner till IP-adressintervallen i din lokala nätverkskonfiguration måste du vända dig till någon som kan ge den informationen till dig.

- En extern offentlig IP-adress för VPN-enheten. Den här IP-adressen får inte finnas bakom en NAT.
    
- En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](http://azure.microsoft.com/pricing/free-trial/).

### <a name="values"></a>Exempel på konfigurationsvärden för övningen


När du använder stegen som en övning kan du använda följande exempel på konfigurationsvärden:

- VNet-namn: TestVNet1
- Adressutrymme: 10.11.0.0/16 och 10.12.0.0/16
- Undernät: 
    - FrontEnd: 10.11.0.0/24
    - BackEnd: 10.12.0.0/24
    - GatewaySubnet: 10.12.255.0/27
- Resursgrupp: TestRG1
- Plats: Östra USA
- DNS-server: 8.8.8.8
- Gateway-namn: VNet1GW
- Offentlig IP: VNet1GWIP
- VPN-typ: Routningsbaserad
- Anslutningstyp: Plats-till-plats (IPsec)
- Gateway-typ: VPN
- Gateway-namn på lokalt nätverk: Site2
- Anslutningsnamn: VNet1toSite2



## 1. Skapa ett virtuellt nätverk 

Om du redan har ett VNet, kontrollerar du att inställningarna är kompatibla med din VPN-gatewaydesign. Var särskilt noga med alla undernät som överlappar med andra nätverk. Om du har överlappande undernät fungerar inte anslutningen ordentligt. Om ditt VNet är konfigurerat med de korrekta inställningarna, kan du börja med stegen i avsnittet [Ange en DNS-server](#dns).

### Så här skapar du ett virtuellt nätverk

[AZURE.INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]  

## 2. Lägg till ytterligare adressutrymmen och undernät

Du kan lägga till ytterligare adressutrymme och undernät till ditt VNet när det har skapats.

[AZURE.INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)] 

## <a name="dns"></a>3. Ange en DNS-server

Om du skapar den här konfigurationen som en övning, ser du följande [värden](#values) när du anger DNS-servern.

### Så här anger du en DNS-server

[AZURE.INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## 4. Skapa ett gateway-undernät

Innan du ansluter det virtuella nätverket till en gateway, måste du skapa gateway-undernätet för det virtuella nätverk som du vill ansluta till. Gateway-undernätet som du skapar måste ha namnet *GatewaySubnet* för att fungera korrekt. 

Gateway-undernätsprefixet för vissa konfigurationer kräver ett undernät på /28 eller större för att få plats med det antal IP-adresser som behövs i poolen. Det innebär att gatewayens undernätsprefix måste vara /28, /27, /26 osv. Du kanske vill skapa ett större undernät för att få plats med eventuella framtida konfigurationstillägg.

Om du skapar den här konfigurationen som en övning, ser du följande [värden](#values) när du skapar gateway-undernätet.

### Så här skapar du ett gateway-undernät

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)] 

[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## 5. Skapa en virtuell nätverksgateway

Om du skapar den här konfigurationen som en övning, ser du följande [värden](#values) när du skapar gatewayen.

### Så här skapar du en virtuell nätverksgateway

[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## 6. Skapa en lokal nätverksgateway

Den *lokala nätverksgatewayen* avser vanligtvis dina lokala plats. Ge den lokala nätverksgatewayen ett namn som Azure kan referera till. 

Om du skapar den här konfigurationen som en övning, ser du följande [värden](#values) när du lägger till din lokala plats.

### Så här skapar du en lokal nätverksgateway

[AZURE.INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## 7. Konfigurera din VPN-enhet

[AZURE.INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## 8. Skapa en VPN-anslutning från plats till plats

Skapa VPN-anslutningen från plats till plats mellan din virtuella nätverksgateway och din VPN-enhet. Glöm inte att byta ut värdena mot dina egna. Den delade nyckeln måste överensstämma med värdet som du använde vid din konfiguration av VPN-enheten. 

Kontrollera att din virtuella nätverksgateway och lokala nätverksgateway har skapats innan du påbörjar det här avsnittet. Om du skapar den här konfigurationen som en övning, ser du följande [värden](#values) när du skapar anslutningen.

### Så här skapar du VPN-anslutningen


[AZURE.INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-rm-portal-include.md)]

## 9. Verifiera VPN-anslutningen

Du kan verifiera VPN-anslutningen i portalen eller med hjälp av PowerShell.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## Nästa steg

- När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Se de virtuella datorernas [utbildningsväg](https://azure.microsoft.com/documentation/learning-paths/virtual-machines) för mer information.

- Information om BGP finns i [BGP-översikt](vpn-gateway-bgp-overview.md) och [Så här konfigurerar du BGP](vpn-gateway-bgp-resource-manager-ps.md).



<!--HONumber=sep16_HO1-->


