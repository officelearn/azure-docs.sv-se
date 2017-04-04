---
title: "Ansluta ditt lokala nätverk till ett virtuellt Azure-nätverk: VPN från plats till plats (klassisk): Portal | Microsoft Docs"
description: "Steg för att skapa en IPsec-anslutning från ditt lokala nätverk till ett virtuellt Azure-nätverk via offentligt Internet. Dessa steg hjälper dig att skapa en plats-till-plats-anslutning med VPN-gateway med hjälp av portalen."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 619ea430b13c16e8e4338413613d5798f36458ba
ms.lasthandoff: 03/25/2017


---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>Skapa en plats-till-plats-anslutning med hjälp av Azure-portalen (klassisk)
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Klassisk – Azure Portal](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [Klassisk – Klassisk portal](vpn-gateway-site-to-site-create.md)
>
>


En plats-till-plats-anslutning (S2S) för VPN-gateway är en anslutning via en VPN-tunnel med IPsec/IKE (IKEv1 eller IKEv2). Den här typen av anslutning kräver en lokal VPN-enhet som tilldelats en offentlig IP-adress och som inte finns bakom en NAT. Plats-till-plats-anslutningar kan användas för flera platser och hybridkonfigurationer.

Den här artikeln visar hur du skapar ett virtuellt nätverk och en VPN-gateway från plats till plats till ditt lokala nätverk med hjälp av den klassiska distributionsmodellen och Azure Portal. 

![Diagram över plats-till-plats-anslutning med VPN-gateway](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Distributionsmodeller och metoder för plats-till-plats-anslutningar
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Följande tabell visar de tillgängliga distributionsmodellerna och -metoderna för plats-till-plats-konfigurationer. När det finns en artikel med konfigurationssteg tillgänglig länkar vi till den direkt från tabellen.

[!INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Ytterligare konfigurationer
Om du vill koppla ihop VNets, men inte skapar någon anslutning till en lokal plats, kan du läsa mer i [Konfigurera en VNet-till-VNet-anslutning](virtual-networks-configure-vnet-to-vnet-connection.md). Information om att lägga till en plats-till-plats-anslutning till en VNet som redan har en anslutning finns i [Lägga till en S2S-anslutning till en VNet med en befintlig anslutning för VPN-gateway](vpn-gateway-multi-site.md).

## <a name="before-you-begin"></a>Innan du börjar
Kontrollera att du har följande innan du påbörjar konfigurationen:

* En kompatibel VPN-enhet och någon som kan konfigurera den. Se [Om VPN-enheter](vpn-gateway-about-vpn-devices.md). Om du inte vet hur man konfigurerar VPN-enheten eller inte känner till IP-adressintervallen i din lokala nätverkskonfiguration måste du vända dig till någon som kan ge den informationen till dig.
* En extern offentlig IPv4 IP-adress för VPN-enheten. Den här IP-adressen får inte finnas bakom en NAT.
* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) eller registrera dig för ett [kostnadsfritt konto](http://azure.microsoft.com/pricing/free-trial).
* För närvarande krävs PowerShell för att ange den delade nyckeln och skapa VPN-gatewayanslutningen. Installera den senaste versionen av Azure Service Management (SM) PowerShell-cmdletar. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs). När du arbetar med PowerShell i den här konfigurationen ska du kontrollera att du kör som administratör. 

> [!NOTE]
> När du konfigurerar en plats-till-plats-anslutning krävs en offentlig IPv4-adress för VPN-enheten.                                                                                                                                                                               
>
>

### <a name="values"></a>Exempel på konfigurationsvärden för övningen
När du använder stegen som en övning kan du använda följande exempel på konfigurationsvärden:

* **VNet-namn:** TestVNet1
* **Adressutrymme:** 
    * 10.11.0.0/16
    * 10.12.0.0/16 (valfritt för den här övningen)
* **Undernät:**
  * FrontEnd: 10.11.0.0/24
  * BackEnd: 10.12.0.0/24 (valfritt för den här övningen)
* **GatewaySubnet:** 10.11.255.0/27
* **Resursgrupp:** TestRG1
* **Plats:** Östra USA
* **DNS-server:** 8.8.8.8 (valfritt för den här övningen)
* **Namn på lokal plats:** Site2

## <a name="CreatVNet"></a>1. Skapa ett virtuellt nätverk

När du skapar ett virtuellt nätverk som ska användas med en S2S-anslutning måste du se till att adressutrymmena som du anger inte överlappar eventuella klientadressutrymmen för de lokala platser som du vill ansluta till. Om du har överlappande undernät fungerar inte anslutningen ordentligt.

* Om du redan har ett VNet, kontrollerar du att inställningarna är kompatibla med din VPN-gatewaydesign. Var särskilt noga med alla undernät som överlappar med andra nätverk. 

* Om du inte redan har ett virtuellt nätverk, skapa ett. Skärmbilderna anges som exempel. Se till att ersätta värdena med dina egna.

### <a name="to-create-a-virtual-network"></a>Så här skapar du ett virtuellt nätverk

1. Navigera till [Azure-portalen](http://portal.azure.com) från en webbläsare och logga in med ditt Azure-konto vid behov.
2. Klicka på **Ny**. Skriv ”Virtuella nätverk” i fältet **Sök på marketplace**. Leta upp **Virtuellt nätverk** bland sökresultaten och klicka för att öppna bladet **Virtuellt nätverk**.

    ![Sök efter virtuella nätverksblad](./media/vpn-gateway-howto-site-to-site-classic-portal/newvnetportal700.png)
3. Nästan längst ned på bladet virtuellt nätverk, från listan **Välj en distributionsmodell**, väljer du **Klassisk** och klickar sedan på **Skapa**.

    ![Välj distributionsmodell](./media/vpn-gateway-howto-site-to-site-classic-portal/selectmodel.png)
4. Konfigurera VNet-inställningarna på bladet **Skapa virtuellt nätverk**. På det här bladet lägger du till ditt första adressutrymme och ett enda adressintervall för ett undernät. När du har skapat ditt VNet, kan du gå tillbaka och lägga till ytterligare undernät och adressutrymmen.

    ![Bladet Skapa virtuellt nätverk](./media/vpn-gateway-howto-site-to-site-classic-portal/createvnet.png "Bladet Skapa virtuellt nätverk")
5. Verifiera att **Prenumeration** är korrekt. Du kan ändra prenumerationer i listrutan.
6. Klicka på **Resursgrupp** och välj antingen en befintlig resursgrupp, eller skapa en ny genom att ange ett namn för din nya resursgrupp. Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).
7. Välj därefter **Plats**-inställningar för ditt VNet. Platsen avgör var resurserna som du distribuerar till detta VNet kommer att placeras.
8. Välj **Fäst vid instrumentpanelen** om du vill kunna hitta ditt VNet på ett enkelt sätt på instrumentpanelen och klicka sedan på **Skapa**.

    ![Fäst på instrumentpanelen](./media/vpn-gateway-howto-site-to-site-classic-portal/pintodashboard150.png "Fäst på instrumentpanelen")
9. När du klickar på Skapa visas en ikon på instrumentpanelen som visar förloppet för ditt VNet. Panelen ändras när VNet skapas.

    ![Ikonen Skapa ett virtuell nätverk](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png "Skapar det virtuella nätverket")

När du har skapat det virtuella nätverket visas **Skapad** under **Status** på nätverkssidan på den klassiska Azure-portalen.

## <a name="additionaladdress"></a>2. Lägg till ytterligare adressutrymme

När du har skapat ditt virtuella nätverk kan du lägga till ytterligare adressutrymme. Det är inte obligatoriskt att lägga till ytterligare adressutrymme för en S2S-konfiguration, men om du behöver flera adressutrymmen gör du så här:

1. Leta reda på de virtuella nätverken i portalen.
2. Klicka på **Adressutrymme** under avsnittet **Inställningar** på bladet för ditt virtuella nätverk.
3. På bladet Adressutrymme klickar du på **+Lägg till** och anger ytterligare adressutrymme.
 
## <a name="dns"></a>3. Ange en DNS-server
Det är inte obligatoriskt med DNS-inställningar för en S2S-konfiguration, men DNS krävs om du vill använda namnmatchning.

När du har skapat din virtuella nätverk kan du lägga till IP-adressen för en DNS-server för att hantera namnmatchning. Öppna inställningarna för det virtuella nätverket, klicka på DNS-servrar och lägg till IP-adressen för den DNS-server du vill använda för namnmatchning. Du skapar inte en DNS-server med den här inställningen. I exempelinställningarna använder vi en offentlig DNS-server. Vanligtvis vill du använda en privat DNS-server. Se till att lägga till en DNS-server som dina resurser kan kommunicera med.

1. Leta reda på de virtuella nätverken i portalen.
2. Klicka på **DNS-servrar** under avsnittet **Inställningar** på bladet för ditt virtuella nätverk.
3. Lägg till en DNS-server.
4. Klicka på **Spara** överst på sidan för att spara dina inställningar.
 
## <a name="localsite"></a>4. Konfigurera den lokala platsen

Den lokala platsen avser vanligtvis din lokala plats. Den innehåller IP-adressen för den VPN-enhet som du skapar en anslutning till och IP-adressintervallen som ska dirigeras via VPN-gatewayen till VPN-enheten.

1. I portalen, går du till det virtuella nätverket som du vill skapa en gateway för.
2. På bladet **Översikt** på bladet för ditt virtuella nätverk klickar du på **Gateway** i avsnittet VPN-anslutningar för att öppna bladet **Ny VPN-anslutning**.

    ![Klicka för att konfigurera gatewayinställningar](./media/vpn-gateway-howto-site-to-site-classic-portal/beforegw125.png "Klicka för att konfigurera gatewayinställningar")
3. Välj **Plats-till-plats** på bladet **Ny VPN-anslutning**.

    ![Klicka på Plats-till-plats](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site.png "plats-till-plats")
4. Klicka på **Lokal plats - Konfigurera obligatoriska inställningar** för att öppna bladet **Lokal plats**. Konfigurera inställningarna och klicka sedan på **OK** för att spara inställningarna.
    - **Namn:** Ange ett namn för den lokala platsen så att du enkelt kan identifiera den.
    - **IP-adress till VPN-gateway:** Det här är den offentliga IP-adressen till VPN-enheten för ditt lokala nätverk. VPN-enheten måste ha en offentlig IP-adress (IPv4). Ange en giltig offentlig IP-adress för VPN-enheten som du vill ansluta till. Den får inte vara bakom en NAT och måste kunna nås av Azure.
    - **Klientadressutrymme:** Visar IP-adressintervall som du vill dirigera till det lokala nätverket via denna gateway. Du kan lägga till flera adressintervall. Se till att intervallen du anger här inte överlappar intervallen för andra nätverk som ditt virtuella nätverk ansluter till, eller överlappar adressintervallen för det virtuella nätverket.

    ![Lokal plats](./media/vpn-gateway-howto-site-to-site-classic-portal/localnetworksite.png "Konfigurera lokal plats")

## <a name="gatewaysubnet"></a>5. Konfigurera gatewayundernätet

Du måste skapa ett gatewayundernät för din VPN-gateway. Gatewayundernätet innehåller de IP-adresser som VPN-gatewaytjänsterna använder.

1. Markera kryssrutan **Skapa gateway omedelbart** på bladet **Ny VPN-anslutning**. Bladet Valfri gateway-konfiguration visas. Om du inte markerar kryssrutan visas inte bladet för att konfigurera gatewayundernätet.

    ![Gateway-konfiguration - Undernät, storlek, routningstyp](./media/vpn-gateway-howto-site-to-site-classic-portal/optional.png "Gateway-konfiguration - Undernät, storlek, routningstyp")
2. Klicka på **Valfri gateway-konfiguration - Undernät, storlek och routningstyp** för att öppna bladet **Gateway-konfiguration**.
3. Klicka på **Undernät - Konfigurera obligatoriska inställningar** på bladet **Gateway-konfiguration** för att öppna bladet **Lägg till undernät**.

    ![Gateway-konfiguration - Gatewayundernät](./media/vpn-gateway-howto-site-to-site-classic-portal/subnetrequired.png "Gateway-konfiguration - Gatewayundernät")
4. Lägg till gatewayundernätet på bladet **Lägg till undernät**. När du lägger till gatewayundernätet rekommenderar vi att du om möjligt skapar ett gatewayundernät med CIDR-block på /28 eller /27. På så sätt ser du till att du har tillräckligt med IP-adresser för framtida konfigurationsbehov.  Spara inställningarna genom att klicka på **OK**.

    ![Lägg till gatewayundernät](./media/vpn-gateway-howto-site-to-site-classic-portal/addgwsubnet.png "Lägg till gatewayundernät")

## <a name="sku"></a>6. Ange SKU- och VPN-typ
1. Välj gateway-**storlek**. Det här är gateway-SKU:n som du använder för att skapa din virtuella nätverksgateway. I portalen är standard-SKU:n = **Basic**. För mer information om gateway-SKU:er, kan du se [Om VPN-gatewayinställningar](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

    ![Välj SKU- och VPN-typ](./media/vpn-gateway-howto-site-to-site-classic-portal/sku.png "Välj SKU- och VPN-typ")
2. Välj **Routningstyp** för din gateway. Detta kallas även för VPN-typ. Det är viktigt att välja rätt gatewaytyp eftersom du inte kan konvertera gatewayen från en typ till en annan. VPN-enheten måste vara kompatibel med den routningstyp som du väljer. Mer information om VPN-typ finns i [Om VPN-gatewayinställningar](vpn-gateway-about-vpn-gateway-settings.md#vpntype). Läs artiklarna om VPN-typerna RouteBased och PolicyBased. ”Dynamiska” motsvarar RouteBased och ”statiska” motsvarar PolicyBased.
3. Spara inställningarna genom att klicka på **OK**.
4. På bladet **Ny VPN-anslutning**, klickar du på **Ok** längst ned på bladet för att börja skapa din virtuella nätverksgateway. Det här steget kan ta upp till 45 minuter.

## <a name="vpndevice"></a>7. Konfigurera din VPN-enhet

Jobba med din enhetstillverkare för att få specifik konfigurationsinformation och konfigurera din enhet. Referera till [VPN-enheter](vpn-gateway-about-vpn-devices.md) för mer information om VPN-enheter som fungerar bra tillsammans med Azure. Kontrollera även om det finns [kända kompatibilitetsproblem](vpn-gateway-about-vpn-devices.md#known) för den VPN-enhet som du vill använda. 

När du konfigurerar VPN-enheten behöver du IP-adressen för den VPN-gateway som du skapade. Du hittar den på bladet **Översikt** för det virtuella nätverket.

## <a name="CreateConnection"></a>8. Skapa anslutningen
I det här steget anger du den delade nyckeln och skapar anslutningen. Nyckeln som du anger måste vara samma nyckel som används i konfigurationen för VPN-enheten.

> [!NOTE]
> Det här steget är för närvarande inte tillgängligt i Azure Portal. Du måste använda SM-versionen (Service Management) av Azure PowerShell-cmdletarna.                                                                                                                                                                             
>
>

### <a name="step-1-connect-to-your-azure-account"></a>Steg 1. Anslut till ditt Azure-konto

1. Öppna PowerShell-konsolen med utökade rättigheter och anslut till ditt konto. Använd följande exempel för att ansluta:

        Login-AzureRmAccount
2. Kontrollera prenumerationerna för kontot.

        Get-AzureRmSubscription
3. Om du har mer än en prenumeration väljer du den du vill använda.

        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
4. Lägg till SM-versionen av PowerShell-cmdletarna.

        Add-AzureAccount

### <a name="step-2-set-the-shared-key-and-create-the-connection"></a>Steg 2. Ange den delade nyckeln och skapa anslutningen

När du arbetar med PowerShell och den klassiska distributionsmodellen kan resurserna i portalen ibland ha oväntade namn för Azure vid användning av PowerShell. Med följande anvisningar kan du exportera nätverkskonfigurationsfilen så att du får exakta värden för namnen.

1. Skapa en katalog på datorn och exportera sedan nätverkskonfigurationsfilen till katalogen. I det här exemplet exporteras nätverkskonfigurationsfilen till C:\AzureNet.

         Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
2. Öppna nätverkskonfigurationsfilen med en XML-redigerare och kontrollera värdena för 'LocalNetworkSite name' och 'VirtualNetworkSite name'. Gör ändringar så att värdena återspeglas i exemplet. När du anger ett namn som innehåller blanksteg ska du ange värdet inom enkla citattecken.

3. Ange den delade nyckeln och skapa anslutningen. '-SharedKey' är ett värde som du vill genererar och anger. I det här exemplet använder vi 'abc123', men du bör generera något mer komplext. Det är viktigt att värdet du anger här är samma värde som du angav när du konfigurerade VPN-enheten.

        Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
        -LocalNetworkSiteName 'D1BFC9CB_Site2' -SharedKey abc123
När anslutningen har skapats visas resultatet: **Status: Lyckades**.

## <a name="verify"></a>9. Verifiera din anslutning

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="next-steps"></a>Nästa steg
När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [Virtuella datorer](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).


