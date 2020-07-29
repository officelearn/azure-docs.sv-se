---
title: 'Ansluta ditt lokala nätverk till ett virtuellt Azure-nätverk: VPN från plats till plats (klassisk): Portal | Microsoft Docs'
description: Skapa en IPsec-anslutning från ditt lokala nätverk till ett klassiskt virtuellt Azure-nätverk via offentligt Internet.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/11/2020
ms.author: cherylmc
ms.openlocfilehash: 1f096993645aca6999667af88c91d3f55f79d914
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84983061"
---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>Skapa en plats-till-plats-anslutning med hjälp av Azure-portalen (klassisk)


Den här artikeln visar hur du kan använda Azure Portal för att skapa en VPN-gatewayanslutning från plats till plats från ditt lokala nätverk till det virtuella nätverket. Stegen i den här artikeln gäller den klassiska distributions modellen och gäller inte för den aktuella distributions modellen, Resource Manager. Du kan också skapa den här konfigurationen med ett annat distributionsverktyg eller en annan distributionsmodell genom att välja ett annat alternativ i listan nedan:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure Portal (klassisk)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>

En VPN-gatewayanslutning från plats till plats används för att ansluta ditt lokala nätverk till ett virtuellt Azure-nätverk via en IPsec/IKE VPN-tunnel (IKEv1 eller IKEv2). Den här typen av anslutning kräver en lokal VPN-enhet som tilldelats till en extern offentlig IP-adress. Mer information om VPN-gatewayer finns i [Om VPN-gateway](vpn-gateway-about-vpngateways.md).

![Diagram över plats-till-plats-anslutning med VPN Gateway](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)

## <a name="before-you-begin"></a><a name="before"></a>Innan du börjar

Kontrollera att du har uppfyllt följande villkor innan du påbörjar konfigurationen:

* Bekräfta att du vill arbeta i den klassiska distributionsmodellen. Om du vill arbeta i Resource Manager-distributionsmodellen hittar du information i [Skapa en plats-till-plats-anslutning (Resource Manager)](vpn-gateway-howto-site-to-site-resource-manager-portal.md). Om det är möjligt rekommenderar vi att du använder Resource Manager-distributionsmodellen.
* Kontrollera att du har en kompatibel VPN-enhet och någon som kan konfigurera den. Se [Om VPN-enheter](vpn-gateway-about-vpn-devices.md) för mer information om kompatibla VPN-enheter och enhetskonfiguration.
* Kontrollera att du har en extern offentlig IPv4-adress för VPN-enheten.
* Om du inte vet vilka IP-adressintervaller som används i din lokala nätverkskonfiguration kontaktar du relevant person som kan ge dig den här informationen. När du skapar den här konfigurationen måste du ange prefix för IP-adressintervall som Azure dirigerar till den lokala platsen. Inget av undernäten i ditt lokala nätverk kan överlappa de virtuella nätverksundernät du vill ansluta till.
* PowerShell krävs för att kunna ange den delade nyckeln och skapa VPN gateway-anslutningen. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="sample-configuration-values-for-this-exercise"></a><a name="values"></a>Exempel på konfigurationsvärden för övningen

Vi använder följande värden i exemplen. Du kan använda värdena till att skapa en testmiljö eller hänvisa till dem för att bättre förstå exemplen i den här artikeln.

* **VNet-namn:** TestVNet1
* **Adress utrymme:** 
  * 10.11.0.0/16
  * 10.12.0.0/16 (valfritt för den här övningen)
* **Undernät**
  * FrontEnd: 10.11.0.0/24
  * BackEnd: 10.12.0.0/24 (valfritt för den här övningen)
* **GatewaySubnet:** 10.11.255.0/27
* **Resursgrupp:** TestRG1
* **Plats:** USA, östra
* **DNS-server:** 10.11.0.3 (valfritt för den här övningen)
* **Namn på lokal plats:** Site2
* **Klientadressutrymme:** Adressutrymmet som finns på din lokala plats.

## <a name="1-create-a-virtual-network"></a><a name="CreatVNet"></a>1. skapa ett virtuellt nätverk

När du skapar ett virtuellt nätverk som ska användas med en S2S-anslutning måste du se till att adressutrymmena som du anger inte överlappar eventuella klientadressutrymmen för de lokala platser som du vill ansluta till. Om du har överlappande undernät fungerar inte anslutningen ordentligt.

* Om du redan har ett VNet, kontrollerar du att inställningarna är kompatibla med din VPN-gatewaydesign. Var särskilt noga med alla undernät som överlappar med andra nätverk. 

* Om du inte redan har ett virtuellt nätverk, skapa ett. Skärmbilderna anges som exempel. Se till att ersätta värdena med dina egna.

### <a name="to-create-a-virtual-network"></a>Så här skapar du ett virtuellt nätverk

1. Navigera till [Azure-portalen](https://portal.azure.com) från en webbläsare och logga in med ditt Azure-konto vid behov.
2. Klicka på **+ skapa en resurs*. Skriv "Virtual Network" i fältet **Sök på Marketplace** . Leta upp **Virtual Network** från den returnerade listan och klicka för att öppna sidan **Virtual Network** .
3. Klicka på **(ändra till klassiskt)** och klicka sedan på **skapa**.
4. Konfigurera VNet-inställningarna på sidan **Skapa virtuellt nätverk (klassisk)**. På den här sidan lägger du till ditt första adressutrymme och ett enda adressintervall för ett undernät. När du har skapat det virtuella nätverket kan du gå tillbaka och lägga till ytterligare undernät och adress utrymmen.

   ![Sidan Skapa virtuellt nätverk](./media/vpn-gateway-howto-site-to-site-classic-portal/createvnet.png "Sidan Skapa virtuellt nätverk")
5. Verifiera att **Prenumeration** är korrekt. Du kan ändra prenumerationer i listrutan.
6. Klicka på **Resursgrupp** och välj en befintlig resursgrupp eller skapa en ny genom att ange ett namn. Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](../azure-resource-manager/management/overview.md#resource-groups).
7. Välj därefter **Plats**-inställningar för ditt VNet. Platsen avgör var resurserna som du distribuerar till detta VNet kommer att placeras.
8. Klicka på **Skapa** för att skapa ditt VNet.
9. När du har klickat på ”Skapa”, visas en ikon på instrumentpanelen som visar framstegen för ditt VNet. Panelen ändras när VNet skapas.

## <a name="2-add-additional-address-space"></a><a name="additionaladdress"></a>2. Lägg till ytterligare adress utrymme

När du har skapat ditt virtuella nätverk kan du lägga till ytterligare adressutrymme. Det är inte obligatoriskt att lägga till ytterligare adressutrymme för en S2S-konfiguration, men om du behöver flera adressutrymmen gör du så här:

1. Leta upp det virtuella nätverket i portalen.
2. Klicka på **Adressutrymme** under avsnittet **Inställningar** på sidan för ditt virtuella nätverk.
3. På sidan Adressutrymme klickar du på **+Lägg till** och anger ytterligare adressutrymme.

## <a name="3-specify-a-dns-server"></a><a name="dns"></a>3. Ange en DNS-Server

Det är inte obligatoriskt med DNS-inställningar för en S2S-konfiguration, men DNS krävs om du vill använda namnmatchning. Ingen ny DNS-server skapas när du anger ett värde. Den angivna IP-adressen för DNS-servern måste vara en DNS-server som kan matcha namnen för de resurser som du ansluter till. I exempelinställningarna har vi använt en privat IP-adress. IP-adressen som vi använder är förmodligen inte IP-adressen till din DNS-server. Använd dina egna värden.

När du har skapat ditt virtuella nätverk kan du lägga till IP-adressen för en DNS-server för att hantera namnmatchning. Öppna inställningarna för det virtuella nätverket, klicka på DNS-servrar och lägg till IP-adressen för den DNS-server du vill använda för namnmatchning.

1. Leta upp det virtuella nätverket i portalen.
2. Klicka på **DNS-servrar** under avsnittet **Inställningar** på sidan för ditt virtuella nätverk.
3. Lägg till en DNS-server.
4. Klicka på **Spara** överst på sidan för att spara dina inställningar.

## <a name="4-configure-the-local-site"></a><a name="localsite"></a>4. Konfigurera den lokala platsen

Den lokala platsen avser vanligtvis din lokala plats. Den innehåller IP-adressen för den VPN-enhet som du skapar en anslutning till och IP-adressintervallen som ska dirigeras via VPN-gatewayen till VPN-enheten.

1. På sidan för ditt VNet, under **Inställningar**, klickar du på **diagram**.
1. På sidan **VPN-anslutningar** klickar **du på du har inga befintliga VPN-anslutningar. Kom igång genom att klicka här**.
1. Lämna **plats-till-plats-** vald för **Anslutnings typ**.
4. Klicka på **Lokal plats - Konfigurera obligatoriska inställningar** för att öppna sidan **Lokal plats**. Konfigurera inställningarna och klicka sedan på **OK** för att spara inställningarna.
   - **Namn:** Ange ett namn för den lokala platsen så att du enkelt kan identifiera den.
   - **IP-adress till VPN-gateway:** Det här är den offentliga IP-adressen till VPN-enheten för ditt lokala nätverk. VPN-enheten måste ha en offentlig IP-adress (IPv4). Ange en giltig offentlig IP-adress för VPN-enheten som du vill ansluta till. Det måste kunna kontaktas av Azure. Om du inte vet VPN-enhetens IP-adress kan du använda ett platshållarvärde (i formatet för en giltig offentlig IP-adress) och ändra det senare.
   - **Klientadressutrymme:** Visar IP-adressintervall som du vill dirigera till det lokala nätverket via denna gateway. Du kan lägga till flera adressintervall. Se till att intervallen du anger här inte överlappar intervallen för andra nätverk som ditt virtuella nätverk ansluter till, eller överlappar adressintervallen för det virtuella nätverket.

   ![Lokal plats](./media/vpn-gateway-howto-site-to-site-classic-portal/localnetworksite.png "Konfigurera lokal plats")

Stäng sidan lokal webbplats genom att klicka på **OK** . **Klicka inte på OK för att stänga sidan ny VPN-anslutning**.

## <a name="5-configure-the-gateway-subnet"></a><a name="gatewaysubnet"></a>5. Konfigurera Gateway-undernätet

Du måste skapa ett gatewayundernät för din VPN-gateway. Gatewayundernätet innehåller de IP-adresser som VPN-gatewaytjänsterna använder.


1. Markera kryssrutan **Skapa gateway omedelbart** på sidan **Ny VPN-anslutning**. Sidan Valfri gateway-konfiguration visas. Om du inte markerar kryssrutan visas inte sidan för att konfigurera gatewayundernätet.

   ![Gateway-konfiguration-undernät, storlek, typ av Routning](./media/vpn-gateway-howto-site-to-site-classic-portal/optional.png "Gateway-konfiguration-undernät, storlek, typ av Routning")
2. Klicka på **Valfri gateway-konfiguration - Undernät, storlek och routningstyp** för att öppna sidan **Gateway-konfiguration**.
3. Klicka på **Undernät - Konfigurera obligatoriska inställningar** på sidan **Gateway-konfiguration** för att öppna sidan **Lägg till undernät**. När du är färdig med konfigurationen av inställningarna klickar du på **OK**.

   ![Gateway-konfiguration – Gateway-undernät](./media/vpn-gateway-howto-site-to-site-classic-portal/subnetrequired.png "Gateway-konfiguration – Gateway-undernät")
4. Lägg till gatewayundernätet på sidan **Lägg till undernät**. Storleken på gatewayundernätet du anger beror på konfigurationen av VPN-gatewayen du vill skapa. Även om det går att skapa ett gatewayundernät som är så litet som /29 rekommenderar vi att du använder /27 eller /28. Då skapas ett större undernät som innehåller fler adresser. Om du använder det större nätverksundernätet får du tillräckligt många IP-adresser för att hantera möjliga framtida konfigurationer.

   ![Lägg till gateway-undernät](./media/vpn-gateway-howto-site-to-site-classic-portal/addgwsubnet.png "Lägg till gateway-undernät")

## <a name="6-specify-the-sku-and-vpn-type"></a><a name="sku"></a>6. Ange SKU och VPN-typ

1. Välj gateway-**storlek**. Det här är gateway-SKU:n som du använder för att skapa din virtuella nätverksgateway. Klassiska VPN-gatewayer använder de gamla (äldre) gateway-SKU:erna. Mer information om de äldre gateway-SKU:erna finns i [Working with virtual network gateway SKUs (old SKUs)](vpn-gateway-about-skus-legacy.md) (Arbeta med SKU:er för virtuella nätverksgatewayer (gamla SKU:er)).

   ![Välj SKU och VPN-typ](./media/vpn-gateway-howto-site-to-site-classic-portal/sku.png "Välj SKU och VPN-typ")
2. Välj **Routningstyp** för din gateway. Detta kallas även för VPN-typ. Det är viktigt att välja rätt typ, eftersom du inte kan konvertera gatewayen från en typ till en annan. VPN-enheten måste vara kompatibel med den routningstyp som du väljer. Mer information om Dirigerings typ finns i [om VPN Gateway inställningar](vpn-gateway-about-vpn-gateway-settings.md#vpntype). Läs artiklarna om VPN-typerna RouteBased och PolicyBased. ”Dynamiska” motsvarar RouteBased och ”statiska” motsvarar PolicyBased.
3. Spara inställningarna genom att klicka på **OK**.
4. På sidan **ny VPN-anslutning** klickar du på **OK** längst ned på sidan för att börja distribuera den virtuella Nätverksgatewayen. Beroende på vilken SKU du väljer kan det ta upp till 45 minuter att skapa en virtuell nätverksgateway.

## <a name="7-configure-your-vpn-device"></a><a name="vpndevice"></a>7. Konfigurera VPN-enheten

Plats-till-plats-anslutningar till ett lokalt nätverk kräver en VPN-enhet. I det här steget konfigurerar du VPN-enheten. När du konfigurerar VPN-enheten behöver du följande:

- En delad nyckel. Det här är samma delade nyckel som du anger när du skapar VPN-anslutningen för plats-till-plats. I vårt exempel använder vi en enkel delad nyckel. Vi rekommenderar att du skapar och använder en mer komplex nyckel.
- Den offentliga IP-adressen för din virtuella nätverksgateway. Du kan visa den offentliga IP-adressen genom att använda Azure Portal, PowerShell eller CLI.

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="8-create-the-connection"></a><a name="CreateConnection"></a>8. skapa anslutningen
I det här steget anger du den delade nyckeln och skapar anslutningen. Nyckeln som du anger måste vara samma nyckel som används i konfigurationen för VPN-enheten.

> [!NOTE]
> Det här steget är för närvarande inte tillgängligt i Azure Portal. Du måste använda SM-versionen (Service Management) av Azure PowerShell-cmdletarna. Se [innan du börjar](#before) för information om hur du installerar dessa cmdlets.
>

### <a name="step-1-connect-to-your-azure-account"></a>Steg 1. Anslut till ditt Azure-konto

Du måste köra dessa kommandon lokalt med PowerShell Service Management-modulen. 

1. Öppna PowerShell-konsolen med utökade rättigheter. Använd följande kommando för att växla till Service Management:

   ```powershell
   azure config mode asm
   ```
2. Anslut till ditt konto. Använd följande exempel för att ansluta:

   ```powershell
   Add-AzureAccount
   ```
3. Kontrollera prenumerationerna för kontot.

   ```powershell
   Get-AzureSubscription
   ```
4. Om du har mer än en prenumeration väljer du den du vill använda.

   ```powershell
   Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_ID"
   ```

### <a name="step-2-set-the-shared-key-and-create-the-connection"></a>Steg 2. Ange den delade nyckeln och skapa anslutningen

När du skapar ett klassiskt VNet i portalen (inte använder PowerShell) lägger Azure till resurs grupp namnet till det korta namnet. Till exempel, enligt Azure, är namnet på det VNet som du skapade för den här övningen "Group TestRG1 TestVNet1", inte "TestVNet1". PowerShell kräver det fullständiga namnet på det virtuella nätverket, inte det korta namnet som visas i portalen. Det långa namnet är inte synligt i portalen. Med följande steg kan du exportera nätverks konfigurations filen för att hämta de exakta värdena för det virtuella nätverks namnet. 

1. Skapa en katalog på datorn och exportera sedan nätverkskonfigurationsfilen till katalogen. I det här exemplet exporteras nätverkskonfigurationsfilen till C:\AzureNet.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
2. Öppna nätverkskonfigurationsfilen med en XML-redigerare och kontrollera värdena för 'LocalNetworkSite name' och 'VirtualNetworkSite name'. Ändra exemplet för den här övningen så att det återspeglar värdena i XML-filen. När du anger ett namn som innehåller blanksteg ska du ange värdet inom enkla citattecken.

3. Ange den delade nyckeln och skapa anslutningen. '-SharedKey' är ett värde som du vill genererar och anger. I det här exemplet använder vi 'abc123', men du bör generera något mer komplext. Det är viktigt att värdet du anger här är samma värde som du angav när du konfigurerade VPN-enheten.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
   -LocalNetworkSiteName 'D1BFC9CB_Site2' -SharedKey abc123
   ```
   När anslutningen har skapats visas resultatet: **Status: Lyckades**.

## <a name="9-verify-your-connection"></a><a name="verify"></a>9. kontrol lera anslutningen

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

Om du har problem med att ansluta läser du **felsökningsavsnittet** som du hittar i innehållsförteckningen till vänster.

## <a name="how-to-reset-a-vpn-gateway"></a><a name="reset"></a>Återställa en VPN-gateway

Du kan behöva återställa en Azure VPN-gateway om VPN-anslutningen mellan flera platser i en eller flera VPN-tunnlar för plats-till-plats bryts. I det här fallet fungerar de lokala VPN-enheterna korrekt, men de kan inte upprätta IPSec-tunnlar med Azures VPN-gatewayer. Stegvisa anvisningar finns i [Återställa en VPN-gateway](vpn-gateway-resetgw-classic.md#resetclassic).

## <a name="how-to-change-a-gateway-sku"></a><a name="changesku"></a>Ändra en gateway-SKU

Stegvisa anvisningar som beskriver hur du ändrar en gateway-SKU finns i [Resize a gateway SKU](vpn-gateway-about-SKUS-legacy.md#classicresize) (Ändra storlek på en gateway-SKU).

## <a name="next-steps"></a>Nästa steg

* När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [Virtuella datorer](https://docs.microsoft.com/azure/).
* Information om Tvingad tunnel trafik finns i [om Tvingad tunnel trafik](vpn-gateway-about-forced-tunneling.md).
