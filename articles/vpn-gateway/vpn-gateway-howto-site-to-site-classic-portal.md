---
title: "Ansluta ditt lokala nätverk till ett virtuellt Azure-nätverk: VPN från plats till plats (klassisk): Portal | Microsoft Docs"
description: "Steg för att skapa en IPsec-anslutning från ditt lokala nätverk till ett virtuellt Azure-nätverk via offentligt Internet. Dessa steg hjälper dig att skapa en plats-till-plats-anslutning med VPN-gateway med hjälp av portalen."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/24/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: e5dcf957ea88175be02bce21929c43151417d0e3
ms.lasthandoff: 05/02/2017


---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>Skapa en plats-till-plats-anslutning med hjälp av Azure-portalen (klassisk)

Den här artikeln visar hur du kan använda Azure Portal för att skapa en VPN-gatewayanslutning från plats till plats från ditt lokala nätverk till det virtuella nätverket. Anvisningarna i den här artikeln gäller den klassiska distributionsmodellen. Du kan också skapa den här konfigurationen med ett annat distributionsverktyg eller en annan distributionsmodell genom att välja ett annat alternativ i listan nedan:

> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Resource Manager – CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Klassisk – Azure Portal](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [Klassisk – Klassisk portal](vpn-gateway-site-to-site-create.md)
> 
>

![Diagram över plats-till-plats-anslutning med VPN-gateway](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)


En VPN-gatewayanslutning från plats till plats används för att ansluta ditt lokala nätverk till ett virtuellt Azure-nätverk via en IPsec/IKE VPN-tunnel (IKEv1 eller IKEv2). Den här typen av anslutning kräver en lokal VPN-enhet som tilldelats till en extern offentlig IP-adress. Mer information om VPN-gatewayer finns i [Om VPN-gateway](vpn-gateway-about-vpngateways.md).

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har uppfyllt följande villkor innan du påbörjar konfigurationen:

* Verifiera att du vill arbeta med den klassiska distributionsmodellen. [!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 
* En kompatibel VPN-enhet och någon som kan konfigurera den. Se [Om VPN-enheter](vpn-gateway-about-vpn-devices.md) för mer information om kompatibla VPN-enheter och enhetskonfiguration.
* En extern offentlig IPv4 IP-adress för VPN-enheten. Den här IP-adressen får inte finnas bakom en NAT.
* Om du inte vet vilka IP-adressintervaller som används i din lokala nätverkskonfiguration kontaktar du relevant person som kan ge dig den här informationen. När du skapar den här konfigurationen måste du ange prefix för IP-adressintervall som Azure dirigerar till den lokala platsen. Inget av undernäten i ditt lokala nätverk kan överlappa de virtuella nätverksundernät du vill ansluta till.
* För närvarande krävs PowerShell för att ange den delade nyckeln och skapa VPN-gatewayanslutningen. Installera den senaste versionen av Azure Service Management (SM) PowerShell-cmdletar. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview). När du arbetar med PowerShell i den här konfigurationen ska du kontrollera att du kör som administratör. 

### <a name="values"></a>Exempel på konfigurationsvärden för övningen

Vi använder följande värden i exemplen. Du kan använda värdena till att skapa en testmiljö eller hänvisa till dem för att bättre förstå exemplen i den här artikeln.

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
9. När du har klickat på ”Skapa”, visas en ikon på instrumentpanelen som visar framstegen för ditt VNet. Panelen ändras när VNet skapas.

    ![Ikonen Skapa ett virtuell nätverk](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png "Skapar det virtuella nätverket")

När du har skapat det virtuella nätverket visas **Skapad** under **Status** på nätverkssidan på den klassiska Azure-portalen.

## <a name="additionaladdress"></a>2. Lägg till ytterligare adressutrymme

När du har skapat ditt virtuella nätverk kan du lägga till ytterligare adressutrymme. Det är inte obligatoriskt att lägga till ytterligare adressutrymme för en S2S-konfiguration, men om du behöver flera adressutrymmen gör du så här:

1. Leta reda på de virtuella nätverken i portalen.
2. Klicka på **Adressutrymme** under avsnittet **Inställningar** på bladet för ditt virtuella nätverk.
3. På bladet Adressutrymme klickar du på **+Lägg till** och anger ytterligare adressutrymme.
 
## <a name="dns"></a>3. Ange en DNS-server
Det är inte obligatoriskt med DNS-inställningar för en S2S-konfiguration, men DNS krävs om du vill använda namnmatchning.

När du har skapat ditt virtuella nätverk kan du lägga till IP-adressen för en DNS-server för att hantera namnmatchning. Öppna inställningarna för det virtuella nätverket, klicka på DNS-servrar och lägg till IP-adressen för den DNS-server du vill använda för namnmatchning. Du skapar inte en DNS-server med den här inställningen. I exempelinställningarna använder vi en offentlig DNS-server. Vanligtvis vill du använda en privat DNS-server. Se till att lägga till en DNS-server som dina resurser kan kommunicera med.

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
4. Lägg till gatewayundernätet på bladet **Lägg till undernät**. Storleken på gatewayundernätet du anger beror på konfigurationen av VPN-gatewayen du vill skapa. Även om det är möjligt att skapa ett gateway-undernät som är så litet som /29 så rekommenderar vi att du skapar ett större undernät som inkluderar fler adresser genom att välja /27 eller /28. Om du använder det större nätverksundernätet får du tillräckligt många IP-adresser för att hantera möjliga framtida konfigurationer.

    ![Lägg till gatewayundernät](./media/vpn-gateway-howto-site-to-site-classic-portal/addgwsubnet.png "Lägg till gatewayundernät")

## <a name="sku"></a>6. Ange SKU- och VPN-typ
1. Välj gateway-**storlek**. Det här är gateway-SKU:n som du använder för att skapa din virtuella nätverksgateway. I portalen är standard-SKU:n = **Basic**. För mer information om gateway-SKU:er, kan du se [Om VPN-gatewayinställningar](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

    ![Välj SKU- och VPN-typ](./media/vpn-gateway-howto-site-to-site-classic-portal/sku.png "Välj SKU- och VPN-typ")
2. Välj **Routningstyp** för din gateway. Detta kallas även för VPN-typ. Det är viktigt att välja rätt gatewaytyp eftersom du inte kan konvertera gatewayen från en typ till en annan. VPN-enheten måste vara kompatibel med den routningstyp som du väljer. Mer information om VPN-typ finns i [Om VPN-gatewayinställningar](vpn-gateway-about-vpn-gateway-settings.md#vpntype). Läs artiklarna om VPN-typerna RouteBased och PolicyBased. ”Dynamiska” motsvarar RouteBased och ”statiska” motsvarar PolicyBased.
3. Spara inställningarna genom att klicka på **OK**.
4. På bladet **Ny VPN-anslutning**, klickar du på **Ok** längst ned på bladet för att börja skapa din virtuella nätverksgateway. Det här steget kan ta upp till 45 minuter.

## <a name="vpndevice"></a>7. Konfigurera din VPN-enhet

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="CreateConnection"></a>8. Skapa anslutningen
I det här steget anger du den delade nyckeln och skapar anslutningen. Nyckeln som du anger måste vara samma nyckel som används i konfigurationen för VPN-enheten.

> [!NOTE]
> Det här steget är för närvarande inte tillgängligt i Azure Portal. Du måste använda SM-versionen (Service Management) av Azure PowerShell-cmdletarna.
>

### <a name="step-1-connect-to-your-azure-account"></a>Steg 1. Anslut till ditt Azure-konto

1. Öppna PowerShell-konsolen med utökade rättigheter och anslut till ditt konto. Använd följande exempel för att ansluta:

  ```powershell
  Login-AzureRmAccount
  ```
2. Kontrollera prenumerationerna för kontot.

  ```powershell
  Get-AzureRmSubscription
  ```
3. Om du har mer än en prenumeration väljer du den du vill använda.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
4. Lägg till SM-versionen av PowerShell-cmdletarna.

  ```powershell
  Add-AzureAccount
  ```

### <a name="step-2-set-the-shared-key-and-create-the-connection"></a>Steg 2. Ange den delade nyckeln och skapa anslutningen

När du arbetar med PowerShell och den klassiska distributionsmodellen kan resurserna i portalen ibland ha oväntade namn för Azure vid användning av PowerShell. Med följande anvisningar kan du exportera nätverkskonfigurationsfilen så att du får exakta värden för namnen.

1. Skapa en katalog på datorn och exportera sedan nätverkskonfigurationsfilen till katalogen. I det här exemplet exporteras nätverkskonfigurationsfilen till C:\AzureNet.

  ```powershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
2. Öppna nätverkskonfigurationsfilen med en XML-redigerare och kontrollera värdena för 'LocalNetworkSite name' och 'VirtualNetworkSite name'. Gör ändringar så att värdena återspeglas i exemplet. När du anger ett namn som innehåller blanksteg ska du ange värdet inom enkla citattecken.

3. Ange den delade nyckeln och skapa anslutningen. '-SharedKey' är ett värde som du vill genererar och anger. I det här exemplet använder vi 'abc123', men du bör generera något mer komplext. Det är viktigt att värdet du anger här är samma värde som du angav när du konfigurerade VPN-enheten.

  ```powershell
  Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
  -LocalNetworkSiteName 'D1BFC9CB_Site2' -SharedKey abc123
  ```
När anslutningen har skapats visas resultatet: **Status: Lyckades**.

## <a name="verify"></a>9. Verifiera din anslutning

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="next-steps"></a>Nästa steg
När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [Virtuella datorer](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).


