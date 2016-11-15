---
title: "Skapa ett virtuellt nätverk med en VPN-anslutning från plats till plats med den klassiska Azure-portalen | Microsoft Docs"
description: "Skapa ett VNet med en S2S VPN Gateway-anslutning mellan flera platser och hybridkonfigurationer med hjälp av den klassiska distributionsmodellen."
services: vpn-gateway
documentationcenter: 
author: cherylmc
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 024ecb29-64de-4ff1-84f1-1a45a8595f0b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: cc377f80fa6b9df41081b13a51ff15482b2ba8bc


---
# <a name="create-a-vnet-with-a-sitetosite-connection-using-the-azure-classic-portal"></a>Skapa ett VNet med en anslutning från plats till plats med den klassiska Azure-portalen
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Klassisk – Klassisk portal](vpn-gateway-site-to-site-create.md)
> 
> 

Den här artikeln visar dig hur du skapar ett virtuellt nätverk och en VPN-gateway från plats till plats till ditt lokala nätverk med hjälp av den klassiska distributionsmodellen och den klassiska portalen. Plats-till-plats-anslutningar kan användas för flera platser och hybridkonfigurationer.

![Plats-till-plats-diagram](./media/vpn-gateway-site-to-site-create/site2site.png "site-to-site")

### <a name="deployment-models-and-methods-for-sitetosite-connections"></a>Distributionsmodeller och metoder för plats-till-plats-anslutningar
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Följande tabell visar de tillgängliga distributionsmodellerna och -metoderna för plats-till-plats-konfigurationer. När det finns en artikel med konfigurationssteg tillgänglig länkar vi till den direkt från tabellen.

[!INCLUDE [vpn-gateway-table-site-to-site-table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Ytterligare konfigurationer
Om du vill koppla ihop VNets kan du läsa mer i [Konfigurera en VNet-till-VNet-anslutning för den klassiska distributionsmodellen](virtual-networks-configure-vnet-to-vnet-connection.md). Information om att lägga till en plats-till-plats-anslutning till en VNet som redan har en anslutning finns i [Lägga till en S2S-anslutning till en VNet med en befintlig anslutning för VPN-gateway](vpn-gateway-multi-site.md).

## <a name="before-you-begin"></a>Innan du börjar
Kontrollera att du har följande innan du påbörjar konfigurationen.

* En kompatibel VPN-enhet och någon som kan konfigurera den. Se [Om VPN-enheter](vpn-gateway-about-vpn-devices.md). Om du inte vet hur man konfigurerar VPN-enheten eller inte känner till IP-adressintervallen i din lokala nätverkskonfiguration måste du vända dig till någon som kan ge den informationen till dig.
* En extern offentlig IP-adress för VPN-enheten. Den här IP-adressen får inte finnas bakom en NAT.
* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="a-namecreatevnetacreate-your-virtual-network"></a><a name="CreateVNet"></a>Skapa ditt virtuella nätverk
1. Logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com/).
2. I det nedre vänstra hörnet på skärmen klickar du på **Nytt**. I navigeringsfönstret klickar du på **Nätverkstjänster** och sedan på **Virtuellt nätverk**. Klicka på **Skapa anpassade** för att starta konfigurationsguiden.
3. Ange konfigurationsinställningarna på följande sidor för att skapa din VNet:

## <a name="a-namedetailsavirtual-network-details-page"></a><a name="Details"></a>Sidan Virtuell nätverksinformation
Ange följande information:

* **Namn**: Ge ett namn till det virtuella nätverket. Till exempel *EastUSVNet*. Du använder det här virtuella nätverksnamnet när du distribuerar dina virtuella datorer och PaaS-instanser, så du bör inte göra namnet för komplicerat. 
* **Plats**: Platsen är direkt relaterad till den fysiska platsen (region) där du vill att resurserna (VM) ska finnas. Om du exempelvis vill att de virtuella datorer som du distribuerar till det här virtuella nätverket ska finnas fysiskt i *östra USA*, väljer du den platsen. Du kan inte ändra den region som är associerad med det virtuella nätverket när du har skapat det.

## <a name="a-namednsadns-servers-and-vpn-connectivity-page"></a><a name="DNS"></a>Sidan DNS-servrar och VPN-anslutning
Ange följande information och klicka sedan på pilen Nästa nere till höger.

* **DNS-servrar**: Ange DNS-servernamnet och IP-adressen, eller välj en tidigare registrerad DNS-server i snabbmenyn. Du skapar inte en DNS-server med den här inställningen. Den låter dig ange vilken DNS-server du vill använda för namnmatchning för det här virtuella nätverket.
* **Konfigurera VPN för plats-till-plats**: Markera kryssrutan för **Konfigurera VPN för plats-till-plats**.
* **Lokalt nätverk**: Ett lokalt nätverk representerar den fysiska lokala platsen. Du kan välja ett lokalt nätverk som du tidigare har skapat, eller skapa ett nytt lokalt nätverk. Men om du väljer att använda ett lokalt nätverk som du skapat tidigare ska du gå till konfigurationssidan **Lokala nätverk** och kontrollera att VPN-enhetens IP-adress (offentlig IPv4-adress) för VPN-enheten är korrekt.

## <a name="a-nameconnectivityasitetosite-connectivity-page"></a><a name="Connectivity"></a>Sidan Plats-till-plats-anslutning
Om du skapar ett nytt lokalt nätverk visas sidan **Plats-till-plats-anslutning**. Om du vill använda ett lokalt nätverk som du skapat tidigare, visas inte den här sidan i guiden och du kan gå vidare till nästa avsnitt.

Ange följande information och klicka sedan på pilen Nästa.

* **Namn**: Namnet som du valt för din lokala nätverksplats.
* **IP-adress för VPN-enhet**: Den offentliga IPv4-adressen för din lokala VPN-enhet som du använder för att ansluta till Azure. VPN-enheten får inte finnas bakom en NAT.
* **Adressutrymme**: Ta med inledande IP-adress och CIDR (antal adresser). Du anger de adressintervall som du vill ska skickas via den virtuella nätverksgatewayen till din lokala plats. Om målets IP-adress inte är inom det intervall som du anger här dirigeras den via den virtuella nätverksgatewayen.
* **Lägg till adressutrymme**: Om du har flera adressintervall som du vill ska skickas via den virtuella nätverksgatewayen anger du varje ytterligare adressintervall. Du kan lägga till eller ta bort intervall senare på sidan **Lokalt nätverk**.

## <a name="a-nameaddressavirtual-network-address-spaces-page"></a><a name="Address"></a>Sidan Adressutrymmen för virtuella nätverk
Ange det adressintervall som du vill använda i det virtuella nätverket. Detta är de dynamiska IP-adresser (DIPS) som ska tilldelas de virtuella datorerna och andra rollinstanser som du distribuerar till det här virtuella nätverket.

Det är särskilt viktigt att välja ett intervall som inte överlappar med något av de intervall som används i det lokala nätverket. Kontakta nätverksadministratören för att få mer information. Nätverksadministratören kan behöva dela ut ett intervall med IP-adresser från ditt lokala nätverksadressutrymme som du kan använda i det virtuella nätverket.

Ange följande information och klicka sedan på bockmarkeringen längst ned till höger för att konfigurera nätverket.

* **Adressutrymme**: Ta med inledande IP-adress och antal adresser. Kontrollera att de adressutrymmen du anger inte överlappar några adressutrymmen som du har i det lokala nätverket.
* **Lägg till undernät**: Ta med inledande IP-adress och antal adresser. Fler undernät krävs inte, men du kanske vill skapa ett separat undernät för virtuella datorer med statiska DIPS. Du kan också vilja ha dina virtuella datorer i ett undernät som är avgränsat från dina andra rollinstanser.
* **Lägg till gateway-undernät**: Klicka för att lägga till gateway-undernätet. Gateway-undernätet används bara för den virtuella nätverksgatewayen och krävs för den här konfigurationen.

Klicka på bockmarkeringen längst ned på sidan. Det virtuella nätverket börjar att skapas. När det är slutfört visas **Skapad** under **Status** på sidan **Nätverk** i den klassiska Azure-portalen. När du har skapat VNet kan du konfigurera din virtuella nätverksgateway.

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="a-namevnetgatewayaconfigure-your-virtual-network-gateway"></a><a name="VNetGateway"></a>Konfigurera den virtuella nätverksgatewayen
Konfigurera den virtuella nätverksgatewayen för att skapa en säker plats-till-plats-anslutning. Se [Konfigurera en virtuell nätverksgateway i den klassiska Azure-portalen](vpn-gateway-configure-vpn-gateway-mp.md).

## <a name="next-steps"></a>Nästa steg
När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i dokumentationen för [Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/).




<!--HONumber=Nov16_HO2-->


