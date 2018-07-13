---
title: 'Ansluta ditt lokala nätverk till ett virtuellt Azure-nätverk: VPN från plats till plats (klassisk): Portal | Microsoft Docs'
description: Skapa en IPsec-anslutning från ditt lokala nätverk till ett klassiskt virtuellt Azure-nätverk via offentligt Internet.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: cherylmc
ms.openlocfilehash: 4e9736adfce83fc449b68a7448441ecee481ad2a
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38477904"
---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>Skapa en plats-till-plats-anslutning med hjälp av Azure-portalen (klassisk)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Den här artikeln visar hur du kan använda Azure Portal för att skapa en VPN-gatewayanslutning från plats till plats från ditt lokala nätverk till det virtuella nätverket. Anvisningarna i den här artikeln gäller den klassiska distributionsmodellen. Du kan också skapa den här konfigurationen med ett annat distributionsverktyg eller en annan distributionsmodell genom att välja ett annat alternativ i listan nedan:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure Portal (klassisk)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>

En VPN-gatewayanslutning från plats till plats används för att ansluta ditt lokala nätverk till ett virtuellt Azure-nätverk via en IPsec/IKE VPN-tunnel (IKEv1 eller IKEv2). Den här typen av anslutning kräver en lokal VPN-enhet som tilldelats till en extern offentlig IP-adress. Mer information om VPN-gatewayer finns i [Om VPN-gateway](vpn-gateway-about-vpngateways.md).

![Diagram över plats-till-plats-anslutning med VPN Gateway](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har uppfyllt följande villkor innan du påbörjar konfigurationen:

* Bekräfta att du vill arbeta i den klassiska distributionsmodellen. Om du vill arbeta i Resource Manager-distributionsmodellen hittar du information i [Skapa en plats-till-plats-anslutning (Resource Manager)](vpn-gateway-howto-site-to-site-resource-manager-portal.md). Om det är möjligt rekommenderar vi att du använder Resource Manager-distributionsmodellen.
* Kontrollera att du har en kompatibel VPN-enhet och någon som kan konfigurera den. Se [Om VPN-enheter](vpn-gateway-about-vpn-devices.md) för mer information om kompatibla VPN-enheter och enhetskonfiguration.
* Kontrollera att du har en extern offentlig IPv4-adress för VPN-enheten. Den här IP-adressen får inte finnas bakom en NAT.
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
* **DNS-server:** 10.11.0.3 (valfritt för den här övningen)
* **Namn på lokal plats:** Site2
* **Klientadressutrymme:** Adressutrymmet som finns på din lokala plats.

## <a name="CreatVNet"></a>1. Skapa ett virtuellt nätverk

När du skapar ett virtuellt nätverk som ska användas med en S2S-anslutning måste du se till att adressutrymmena som du anger inte överlappar eventuella klientadressutrymmen för de lokala platser som du vill ansluta till. Om du har överlappande undernät fungerar inte anslutningen ordentligt.

* Om du redan har ett VNet, kontrollerar du att inställningarna är kompatibla med din VPN-gatewaydesign. Var särskilt noga med alla undernät som överlappar med andra nätverk. 

* Om du inte redan har ett virtuellt nätverk, skapa ett. Skärmbilderna anges som exempel. Glöm inte att byta ut värdena mot dina egna.

### <a name="to-create-a-virtual-network"></a>Så här skapar du ett virtuellt nätverk

1. Navigera till [Azure-portalen](http://portal.azure.com) från en webbläsare och logga in med ditt Azure-konto vid behov.
2. Klicka på **+**. Skriv ”Virtuella nätverk” i fältet **Sök på marketplace**. Leta upp **Virtuellt nätverk** bland sökresultaten och klicka för att öppna sidan **Virtuellt nätverk**.

  ![Sidan Sök efter virtuellt nätverk](./media/vpn-gateway-howto-site-to-site-classic-portal/newvnetportal700.png)
3. Välj **Klassisk** i listrutan **Välj en distributionsmodell** nästan längst ned på sidan Virtuellt nätverk och klicka sedan på **Skapa**.

  ![Välj distributionsmodell](./media/vpn-gateway-howto-site-to-site-classic-portal/selectmodel.png)
4. Konfigurera VNet-inställningarna på sidan **Skapa virtuellt nätverk (klassisk)**. På den här sidan lägger du till ditt första adressutrymme och ett enda adressintervall för ett undernät. När du har skapat ditt VNet, kan du gå tillbaka och lägga till ytterligare undernät och adressutrymmen.

  ![Sidan Skapa virtuellt nätverk](./media/vpn-gateway-howto-site-to-site-classic-portal/createvnet.png "Sidan Skapa virtuellt nätverk")
5. Verifiera att **Prenumeration** är korrekt. Du kan ändra prenumerationer i listrutan.
6. Klicka på **Resursgrupp** och välj en befintlig resursgrupp eller skapa en ny genom att ange ett namn. Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).
7. Välj därefter **Plats**-inställningar för ditt VNet. Platsen avgör var resurserna som du distribuerar till detta VNet kommer att placeras.
8. Välj **Fäst vid instrumentpanelen** om du vill kunna hitta ditt VNet på ett enkelt sätt på instrumentpanelen. Klicka på **Skapa** för att skapa ditt VNet.

  ![Fäst på instrumentpanelen](./media/vpn-gateway-howto-site-to-site-classic-portal/pintodashboard150.png "Fäst på instrumentpanelen")
9. När du har klickat på ”Skapa”, visas en ikon på instrumentpanelen som visar framstegen för ditt VNet. Panelen ändras när VNet skapas.

  ![Ikonen Skapa ett virtuell nätverk](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png "Skapar det virtuella nätverket")

## <a name="additionaladdress"></a>2. Lägg till ytterligare adressutrymme

När du har skapat ditt virtuella nätverk kan du lägga till ytterligare adressutrymme. Det är inte obligatoriskt att lägga till ytterligare adressutrymme för en S2S-konfiguration, men om du behöver flera adressutrymmen gör du så här:

1. Leta reda på de virtuella nätverken i portalen.
2. Klicka på **Adressutrymme** under avsnittet **Inställningar** på sidan för ditt virtuella nätverk.
3. På sidan Adressutrymme klickar du på **+Lägg till** och anger ytterligare adressutrymme.

## <a name="dns"></a>3. Ange en DNS-server

Det är inte obligatoriskt med DNS-inställningar för en S2S-konfiguration, men DNS krävs om du vill använda namnmatchning. Ingen ny DNS-server skapas när du anger ett värde. Den angivna IP-adressen för DNS-servern måste vara en DNS-server som kan matcha namnen för de resurser som du ansluter till. I exempelinställningarna har vi använt en privat IP-adress. IP-adressen som vi använder är förmodligen inte IP-adressen till din DNS-server. Använd dina egna värden.

När du har skapat ditt virtuella nätverk kan du lägga till IP-adressen för en DNS-server för att hantera namnmatchning. Öppna inställningarna för det virtuella nätverket, klicka på DNS-servrar och lägg till IP-adressen för den DNS-server du vill använda för namnmatchning.

1. Leta reda på de virtuella nätverken i portalen.
2. Klicka på **DNS-servrar** under avsnittet **Inställningar** på sidan för ditt virtuella nätverk.
3. Lägg till en DNS-server.
4. Klicka på **Spara** överst på sidan för att spara dina inställningar.

## <a name="localsite"></a>4. Konfigurera den lokala platsen

Den lokala platsen avser vanligtvis din lokala plats. Den innehåller IP-adressen för den VPN-enhet som du skapar en anslutning till och IP-adressintervallen som ska dirigeras via VPN-gatewayen till VPN-enheten.

1. I portalen, går du till det virtuella nätverket som du vill skapa en gateway för.
2. På sidan **Översikt** på sidan för ditt virtuella nätverk klickar du på **Gateway** i avsnittet VPN-anslutningar för att öppna sidan **Ny VPN-anslutning**.

  ![Klicka för att konfigurera gatewayinställningar](./media/vpn-gateway-howto-site-to-site-classic-portal/beforegw125.png "Klicka för att konfigurera gatewayinställningar")
3. Välj **Plats-till-plats** på sidan **Ny VPN-anslutning**.
4. Klicka på **Lokal plats - Konfigurera obligatoriska inställningar** för att öppna sidan **Lokal plats**. Konfigurera inställningarna och klicka sedan på **OK** för att spara inställningarna.
  - **Namn:** Ange ett namn för den lokala platsen så att du enkelt kan identifiera den.
  - **IP-adress till VPN-gateway:** Det här är den offentliga IP-adressen till VPN-enheten för ditt lokala nätverk. VPN-enheten måste ha en offentlig IP-adress (IPv4). Ange en giltig offentlig IP-adress för VPN-enheten som du vill ansluta till. Den får inte vara bakom en NAT och måste kunna nås av Azure. Om du inte vet VPN-enhetens IP-adress kan du använda ett platshållarvärde (i formatet för en giltig offentlig IP-adress) och ändra det senare.
  - **Klientadressutrymme:** Visar IP-adressintervall som du vill dirigera till det lokala nätverket via denna gateway. Du kan lägga till flera adressintervall. Se till att intervallen du anger här inte överlappar intervallen för andra nätverk som ditt virtuella nätverk ansluter till, eller överlappar adressintervallen för det virtuella nätverket.

  ![Lokal plats](./media/vpn-gateway-howto-site-to-site-classic-portal/localnetworksite.png "Konfigurera lokal plats")

## <a name="gatewaysubnet"></a>5. Konfigurera gatewayundernätet

Du måste skapa ett gatewayundernät för din VPN-gateway. Gatewayundernätet innehåller de IP-adresser som VPN-gatewaytjänsterna använder.

1. Markera kryssrutan **Skapa gateway omedelbart** på sidan **Ny VPN-anslutning**. Sidan Valfri gateway-konfiguration visas. Om du inte markerar kryssrutan visas inte sidan för att konfigurera gatewayundernätet.

  ![Gateway-konfiguration - Undernät, storlek, routningstyp](./media/vpn-gateway-howto-site-to-site-classic-portal/optional.png "Gateway-konfiguration - Undernät, storlek, routningstyp")
2. Klicka på **Valfri gateway-konfiguration - Undernät, storlek och routningstyp** för att öppna sidan **Gateway-konfiguration**.
3. Klicka på **Undernät - Konfigurera obligatoriska inställningar** på sidan **Gateway-konfiguration** för att öppna sidan **Lägg till undernät**.

  ![Gateway-konfiguration - Gatewayundernät](./media/vpn-gateway-howto-site-to-site-classic-portal/subnetrequired.png "Gateway-konfiguration - Gatewayundernät")
4. Lägg till gatewayundernätet på sidan **Lägg till undernät**. Storleken på gatewayundernätet du anger beror på konfigurationen av VPN-gatewayen du vill skapa. Även om det går att skapa ett gatewayundernät som är så litet som /29 rekommenderar vi att du använder /27 eller /28. Då skapas ett större undernät som innehåller fler adresser. Om du använder det större nätverksundernätet får du tillräckligt många IP-adresser för att hantera möjliga framtida konfigurationer.

  ![Lägg till gatewayundernät](./media/vpn-gateway-howto-site-to-site-classic-portal/addgwsubnet.png "Lägg till gatewayundernät")

## <a name="sku"></a>6. Ange SKU- och VPN-typ

1. Välj gateway-**storlek**. Det här är gateway-SKU:n som du använder för att skapa din virtuella nätverksgateway. I portalen är standard-SKU:n = **Basic**. Klassiska VPN-gatewayer använder de gamla (äldre) gateway-SKU:erna. Mer information om de äldre gateway-SKU:erna finns i [Working with virtual network gateway SKUs (old SKUs)](vpn-gateway-about-skus-legacy.md) (Arbeta med SKU:er för virtuella nätverksgatewayer (gamla SKU:er)).

  ![Välj SKU- och VPN-typ](./media/vpn-gateway-howto-site-to-site-classic-portal/sku.png "Välj SKU- och VPN-typ")
2. Välj **Routningstyp** för din gateway. Detta kallas även för VPN-typ. Det är viktigt att välja rätt gatewaytyp eftersom du inte kan konvertera gatewayen från en typ till en annan. VPN-enheten måste vara kompatibel med den routningstyp som du väljer. Mer information om VPN-typ finns i [Om VPN Gateway-inställningar](vpn-gateway-about-vpn-gateway-settings.md#vpntype). Läs artiklarna om VPN-typerna RouteBased och PolicyBased. ”Dynamiska” motsvarar RouteBased och ”statiska” motsvarar PolicyBased.
3. Spara inställningarna genom att klicka på **OK**.
4. På sidan **Ny VPN-anslutning** klickar du på **OK** längst ned på sidan för att börja skapa din virtuella nätverksgateway. Beroende på vilken SKU du väljer kan det ta upp till 45 minuter att skapa en virtuell nätverksgateway.

## <a name="vpndevice"></a>7. Konfigurera din VPN-enhet

Plats-till-plats-anslutningar till ett lokalt nätverk kräver en VPN-enhet. I det här steget konfigurerar du VPN-enheten. När du konfigurerar VPN-enheten behöver du följande:

- En delad nyckel. Det här är samma delade nyckel som du anger när du skapar VPN-anslutningen för plats-till-plats. I vårt exempel använder vi en enkel delad nyckel. Vi rekommenderar att du skapar och använder en mer komplex nyckel.
- Den offentliga IP-adressen för din virtuella nätverksgateway. Du kan visa den offentliga IP-adressen genom att använda Azure Portal, PowerShell eller CLI.

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="CreateConnection"></a>8. Skapa anslutningen
I det här steget anger du den delade nyckeln och skapar anslutningen. Nyckeln som du anger måste vara samma nyckel som används i konfigurationen för VPN-enheten.

> [!NOTE]
> Det här steget är för närvarande inte tillgängligt i Azure Portal. Du måste använda SM-versionen (Service Management) av Azure PowerShell-cmdletarna.
>

### <a name="step-1-connect-to-your-azure-account"></a>Steg 1. Anslut till ditt Azure-konto

1. Öppna PowerShell-konsolen med utökade rättigheter och anslut till ditt konto. Använd följande exempel för att ansluta:

  ```powershell
  Add-AzureAccount
  ```
2. Kontrollera prenumerationerna för kontot.

  ```powershell
  Get-AzureSubscription
  ```
3. Om du har mer än en prenumeration väljer du den du vill använda.

  ```powershell
  Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_ID"
  ```

### <a name="step-2-set-the-shared-key-and-create-the-connection"></a>Steg 2. Ange den delade nyckeln och skapa anslutningen

När du arbetar med PowerShell och den klassiska distributionsmodellen kan resurserna i portalen ibland ha oväntade namn för Azure vid användning av PowerShell. Med följande anvisningar kan du exportera nätverkskonfigurationsfilen så att du får exakta värden för namnen.

1. Skapa en katalog på datorn och exportera sedan nätverkskonfigurationsfilen till katalogen. I det här exemplet exporteras nätverkskonfigurationsfilen till C:\AzureNet.

  ```powershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
2. Öppna nätverkskonfigurationsfilen med en XML-redigerare och kontrollera värdena för 'LocalNetworkSite name' och 'VirtualNetworkSite name'. Ändra exemplet med de värden du behöver. När du anger ett namn som innehåller blanksteg ska du ange värdet inom enkla citattecken.

3. Ange den delade nyckeln och skapa anslutningen. '-SharedKey' är ett värde som du vill genererar och anger. I det här exemplet använder vi 'abc123', men du bör generera något mer komplext. Det är viktigt att värdet du anger här är samma värde som du angav när du konfigurerade VPN-enheten.

  ```powershell
  Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
  -LocalNetworkSiteName 'D1BFC9CB_Site2' -SharedKey abc123
  ```
När anslutningen har skapats visas resultatet: **Status: Lyckades**.

## <a name="verify"></a>9. Verifiera din anslutning

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

Om du har problem med att ansluta läser du **felsökningsavsnittet** som du hittar i innehållsförteckningen till vänster.

## <a name="reset"></a>Återställa en VPN-gateway

Du kan behöva återställa en Azure VPN-gateway om VPN-anslutningen mellan flera platser i en eller flera VPN-tunnlar för plats-till-plats bryts. I det här fallet fungerar de lokala VPN-enheterna korrekt, men de kan inte upprätta IPSec-tunnlar med Azures VPN-gatewayer. Stegvisa anvisningar finns i [Återställa en VPN-gateway](vpn-gateway-resetgw-classic.md).

## <a name="changesku"></a>Ändra en gateway-SKU

Stegvisa anvisningar som beskriver hur du ändrar en gateway-SKU finns i [Resize a gateway SKU](vpn-gateway-about-SKUS-legacy.md) (Ändra storlek på en gateway-SKU).

## <a name="next-steps"></a>Nästa steg

* När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [Virtuella datorer](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
* Information om tvingad tunneltrafik finns i [Om forcerade tunnlar](vpn-gateway-about-forced-tunneling.md).