---
title: "Ansluta ett virtuellt Azure-nätverk till ett annat VNet: portalen | Microsoft Docs"
description: "Skapa en VPN-gateway-anslutning mellan virtuella nätverk med hjälp av Resource Manager och Azure Portal."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a7015cfc-764b-46a1-bfac-043d30a275df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/27/2017
ms.author: cherylmc
ms.openlocfilehash: a660e8e83220d77f2b55020fade0732b3a140c54
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-the-azure-portal"></a>Konfigurera en VPN-gatewayanslutning mellan virtuella nätverk med hjälp av Azure Portal

Den här artikeln visar hur du ansluter virtuella nätverk via VNet-till-VNet-anslutningstypen. De virtuella nätverken kan finnas i samma eller olika regioner och i samma eller olika prenumerationer. När du ansluter virtuella nätverk från olika prenumerationer, behöver inte prenumerationerna vara associerade med samma Active Directory-klient. 

Anvisningarna i den här artikeln gäller för Resource Manager-distributionsmodellen och användning av Azure-portalen. Du kan också skapa den här konfigurationen med ett annat distributionsverktyg eller en annan distributionsmodell genom att välja ett annat alternativ i listan nedan:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure Portal (klassisk)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Ansluta olika distributionsmodeller – Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Ansluta olika distributionsmodeller – PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![v2v-diagram](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

## <a name="about"></a>Om att ansluta virtuella nätverk

Du ansluter ett virtuellt nätverk till ett annat virtuellt nätverk med VNet-till-VNet-anslutningstypen på nästan samma sätt som du skapar en IPsec-anslutning till en lokal plats. Båda typerna av anslutning använder en VPN-gateway för att få en säker tunnel med IPsec/IKE, och båda fungerar på samma sätt vid kommunikation. Skillnaden mellan anslutningstyperna är hur den lokala nätverksgatewayen har konfigurerats. När du skapar en anslutning mellan virtuella nätverk ser du inte adressutrymmet för den lokala nätverksgatewayen. Den skapas och fylls i automatiskt. Om du uppdaterar adressutrymmer för ett VNet dirigerar det andra VNet-nätverket automatiskt till det uppdaterade adressutrymmet.

Om du arbetar med en komplicerad konfiguration kanske du föredrar att använda IPsec-anslutningstypen i stället för VNet-till-VNet. På så sätt kan du ange ytterligare adressutrymme för den lokala nätverksgatewayen för att dirigera trafik. Om du ansluter till dina virtuella nätverk med IPsec-anslutningstypen måste du skapa och konfigurera den lokala nätverksgatewayen manuellt. Mer information finns i [Site-to-Site configurations](vpn-gateway-howto-site-to-site-resource-manager-portal.md) (Plats-till-plats-konfigurationer).

Om dina VNets dessutom finns i samma region kan det vara bättre att ansluta dem med hjälp av VNet-peering. VNet-peering använder ingen VPN-gateway och prissättningen och funktionaliteten är lite annorlunda. Mer information finns i [VNet peering (Vnet-peering)](../virtual-network/virtual-network-peering-overview.md).

### <a name="why"></a>Varför ska jag skapa en anslutning mellan virtuella nätverk?

Du kan vilja ansluta virtuella nätverk av följande skäl:

* **Geografisk redundans i flera regioner och geografisk närvaro**

  * Du kan ange din egna geografiska replikering eller synkronisering med en säker anslutning, utan att passera några Internet-slutpunkter.
  * Med Azure Traffic Manager och Load Balancer kan du konfigurera arbetsbelastning med hög tillgänglighet och geografisk redundans över flera Azure-regioner. Ett viktigt exempel är att konfigurera att SQL alltid är aktiverat med tillgänglighetsgrupper som är spridda över flera Azure-regioner.
* **Regionala flernivåprogram med isolering eller administrativa gränser**

  * Inom samma region kan du konfigurera flernivåprogram med flera virtuella nätverk som är anslutna till varandra på grund av isolering eller administrativa krav.

VNet-till-VNet-kommunikation kan kombineras med konfigurationer för flera platser. Därmed kan du etablera nätverkstopologier som kombinerar anslutningar mellan olika anläggningar med virtuell nätverksanslutning enligt följande diagram:

![Om anslutningar](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "Om anslutningar")

I den här artikeln får du hjälp med att ansluta virtuella nätverk med anslutningstypen VNet-till-VNet. När du följer dessa steg som en övning kan du använda följande exempelinställningsvärden. I det här exemplet är de virtuella nätverken i samma prenumeration, men i olika resursgrupper. Observera att du inte skapar anslutningen i portalen om dina virtuella nätverk finns i olika prenumerationer. Du kan använda [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) eller [CLI](vpn-gateway-howto-vnet-vnet-cli.md). Mer information om anslutningar mellan virtuella nätverk finns i [Vanliga frågor om VNet-till-VNet](#faq) i slutet av den här artikeln.

### <a name="values"></a>Exempelinställningar

**Värden för TestVNet1:**

* VNet-namn: TestVNet1
* Adressutrymme: 10.11.0.0/16
* Prenumeration: Välj den prenumeration som du vill använda
* Resursgrupp: TestRG1
* Plats: Östra USA
* Undernätsnamn: FrontEnd
* Adressintervall för undernätet: 10.11.0.0/24
* Namn för gateway-undernät: GatewaySubnet (anges automatiskt i portalen)
* Adressintervall för gateway-undernätet: 10.11.255.0/27
* DNS-Server: Använd IP-adressen för din DNS-server
* Namn för det virtuella nätverkets gateway: TestVNet1GW
* Gateway-typ: VPN
* VPN-typ: Routningsbaserad
* SKU: Välj den gateway-SKU som du vill använda
* Offentligt IP-adressnamn: TestVNet1GWIP
* Anslutningsnamn: TestVNet1toTestVNet4
* Delad nyckel: Du kan skapa den delade nyckeln själv. I det här exemplet använder vi abc123. Det viktiga är att värdet matchar när du skapar anslutningen mellan de virtuella nätverken.

**Värden för TestVNet4:**

* VNet-namn: TestVNet4
* Adressutrymme: 10.41.0.0/16
* Prenumeration: Välj den prenumeration som du vill använda
* Resursgrupp: TestRG4
* Plats: Västra USA
* Undernätsnamn: FrontEnd
* Adressintervall för undernätet: 10.41.0.0/24
* Namn för gateway-undernät: GatewaySubnet (anges automatiskt i portalen)
* Adressintervall för gateway-undernätet: 10.41.255.0/27
* DNS-Server: Använd IP-adressen för din DNS-server
* Namn för det virtuella nätverkets gateway: TestVNet4GW
* Gateway-typ: VPN
* VPN-typ: Routningsbaserad
* SKU: Välj den gateway-SKU som du vill använda
* Offentligt IP-adressnamn: TestVNet4GWIP
* Anslutningsnamn: TestVNet4toTestVNet1
* Delad nyckel: Du kan skapa den delade nyckeln själv. I det här exemplet använder vi abc123. Det viktiga är att värdet matchar när du skapar anslutningen mellan de virtuella nätverken.

## <a name="CreatVNet"></a>1. Skapa och konfigurera TestVNet1
Om du redan har ett VNet, kontrollerar du att inställningarna är kompatibla med din VPN-gatewaydesign. Var särskilt noga med alla undernät som överlappar med andra nätverk. Om du har överlappande undernät fungerar inte anslutningen ordentligt. Om ditt VNet är konfigurerat med de korrekta inställningarna, kan du börja med stegen i avsnittet [Ange en DNS-server](#dns).

### <a name="to-create-a-virtual-network"></a>Så här skapar du ett virtuellt nätverk
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="subnets"></a>2. Lägg till ytterligare adressutrymmen och skapa undernät
Du kan lägga till ytterligare adressutrymme och skapa undernät när ditt virtuella nätverk har skapats.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="gatewaysubnet"></a>3. Skapa ett gateway-undernät
Innan du ansluter det virtuella nätverket till en gateway, måste du skapa gateway-undernätet för det virtuella nätverk som du vill ansluta till. Om möjligt är det bäst att skapa ett gateway-undernät med CIDR-block av /28 eller /27 för att tillhandahålla tillräckligt med IP-adresser för att hantera ytterligare framtida konfigurationskrav.

Om du skapar den här konfigurationen för att öva dig, kan du hänvisa till de här [exempelvärdena](#values) när du skapar gateway-undernätet.

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-gateway-subnet"></a>Så här skapar du ett gateway-undernät
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="dns"></a>4. Ange en DNS-server (valfritt)
DNS krävs inte för VNet-till-VNet-anslutningar. Om du vill använda namnmatchning för resurser som distribueras till ditt virtuella nätverk bör du dock ange en DNS-server. Med den här inställningen kan du ange vilken DNS-server du vill använda för namnmatchning för det här virtuella nätverket. Den skapar inte någon DNS-server.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="VNetGateway"></a>5. Skapa en virtuell nätverksgateway
I det här steget ska du skapa den virtuella nätverksgatewayen för ditt virtuella nätverk. Att skapa en gateway kan ofta ta 45 minuter eller mer, beroende på vald gateway-SKU. Om du skapar den här konfigurationen för att öva dig kan du se [exempelinställningarna](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Så här skapar du en virtuell nätverksgateway
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="CreateTestVNet4"></a>6. Skapa och konfigurera TestVNet4
När du har konfigurerat TestVNet1 kan du skapa TestVNet4 genom att upprepa föregående steg och ersätta värdena med de för TestVNet4. Du behöver inte vänta tills den virtuella nätverksgatewayen för TestVNet1 har skapats innan du konfigurerar TestVNet4. Om du använder egna värden måste du kontrollera att adressutrymmena inte överlappar några av de virtuella nätverk som du vill ansluta till.

## <a name="TestVNet1Connection"></a>7. Konfigurera TestVNet1-gatewayanslutningen
När de virtuella nätverksgatewayerna för både TestVNet1 och TestVNet4 har slutförts kan du skapa gateway-anslutningar för de virtuella nätverken. I det här avsnittet skapar du en anslutning från VNet1 till VNet4. De här stegen fungerar endast för olika VNet i samma prenumeration. Om dina VNet finns i olika prenumerationer, måste du använda PowerShell för att ansluta. Mer information finns i [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)-artikeln. Om dina VNets är i olika resursgrupper i samma prenumeration kan du emellertid ansluta dem med hjälp av portalen.

1. I **Alla resurser** navigerar du till den virtuella nätverksgatewayen för ditt virtuella nätverk. Till exempel **TestVNet1GW**. Klicka på **TestVNet1GW** för att öppna sidan för den virtuella nätverksgatewayen.

  ![Sidan Anslutningar](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/1to4connect2.png "Sidan Anslutningar")
2. Klicka på **+ Lägg till** att öppna sidan **Lägg till anslutning**.

  ![Lägg till anslutning](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add.png "Lägg till en anslutning")
3. I namnfältet på sidan **Lägg till anslutning** anger du ett namn för anslutningen. Till exempel **TestVNet1toTestVNet4**.
4. Som **anslutningstyp** väljer du **VNet-to-VNet** i den nedrullningsbara listan.
5. Fältet **Första virtuella nätverksgateway** fylls i automatiskt eftersom du skapar den här anslutningen från den angivna virtuella nätverksgatewayen.
6. Fältet **Andra virtuella nätverksgateway** är den virtuella nätverksgatewayen för det virtuella nätverk som du vill skapa en anslutning till. Klicka på **Välj en annan virtuell nätverksgateway** för att öppna sidan **Välj en virtuell nätverksgateway**.
7. Visa de virtuella nätverksgatewayer som anges på den här sidan. Observera att endast virtuella nätverksgatewayer som ingår i din prenumeration visas. Om du vill ansluta till en virtuell nätverksgateway som inte ingår i din prenumeration kan du läsa [PowerShell-artikeln](vpn-gateway-vnet-vnet-rm-ps.md).
8. Klicka på den virtuella nätverksgatewayen som du vill ansluta till.
9. I fältet **Delad nyckel** anger du en delad nyckel för anslutningen. Du kan generera eller skapa den här nyckeln själv. I en plats-till-plats-anslutning är nyckeln du använder exakt densamma som för din lokala enhet och anslutningen via din virtuella nätverksgateway. Konceptet är i princip samma här, förutom att du istället för att ansluta till en VPN-enhet ansluter till en annan virtuell nätverksgateway.
10. Klicka på **OK** längst ned på sidan för att spara ändringarna.

## <a name="TestVNet4Connection"></a>8. Konfigurera TestVNet4-gatewayanslutningen
Skapa sedan en anslutning från TestVNet4 till TestVNet1. I portalen letar du upp den virtuella nätverksgateway som är associerad med TestVNet4. Följ stegen i föregående avsnitt och ersätt värdena för att skapa en anslutning från TestVNet4 till TestVNet1. Kontrollera att du använder samma delad nyckel.

## <a name="VerifyConnection"></a>9. Verifiera dina anslutningar

Leta rätt på den virtuella nätverksgatewayen i portalen. På sidan för den virtuella nätverksgatewayen klickar du på **Anslutningar** för att visa anslutningssidan för den virtuella nätverksgatewayen. När anslutningen har skapats kan du se att statusvärdena ändras till **Lyckades** och **Ansluten**. Du kan dubbelklicka på en anslutning för att öppna sidan **Essentials** och visa mer information.

![Lyckades](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "Lyckades")

När data börjar flöda ser du värdena för Data in och Data ut.

![Information](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "Information")

## <a name="to-add-additional-connections"></a>Lägga till ytterligare anslutningar

Om du vill lägga till ytterligare anslutningar går du till den virtuella nätverksgatewayen som du vill skapa anslutningen från och klickar på **Anslutningar**. Du kan skapa ytterligare en VNet-till-VNet-anslutning, eller skapa en IPsec Plats-till-plats-anslutning till en lokal plats. Se till att justera **Anslutningstyp** så att den överensstämmer med den anslutningstyp du vill skapa. Innan du skapar fler anslutningar bör du kontrollera att adressutrymmet för ditt virtuella nätverk inte överlappar med något av de andra adressutrymmena du vill ansluta till. Instruktioner om att skapa en Plats-till-plats-anslutning finns i [Skapa en plats-till-plats-anslutning](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

## <a name="faq"></a>Vanliga frågor och svar om VNet-till-VNet
Visa vanliga frågor och svar om du vill ha mer information om anslutningar mellan virtuella nätverk.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om hur du begränsar nätverkstrafiken till resurser i ett virtuellt nätverk finns i [Nätverkssäkerhet](../virtual-network/security-overview.md).

Mer information om hur Azure dirigerar trafik mellan Azure, lokala och Internet-resurser finns i [Trafikdirigering i virtuella nätverk](../virtual-network/virtual-networks-udr-overview.md).