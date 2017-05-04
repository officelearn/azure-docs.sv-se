---
title: "Ansluta ditt lokala nätverk till ett virtuellt Azure-nätverk: VPN från plats till plats: CLI | Microsoft Docs"
description: "Steg för att skapa en IPsec-anslutning från ditt lokala nätverk till ett virtuellt Azure-nätverk via offentligt Internet. Dessa steg hjälper dig att skapa en plats-till-plats-anslutning med VPN-gateway med hjälp av CLI."
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
ms.date: 04/21/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: c3563f3a3fa46d40ba02fe97b3b0ebe3c45caddd
ms.lasthandoff: 04/25/2017


---
# <a name="create-a-virtual-network-with-a-site-to-site-vpn-connection-using-cli"></a>Skapa ett virtuellt nätverk med en VPN-anslutning från plats till plats med CLI

Den här artikeln visar hur du kan använda Azure CLI för att skapa en VPN-gatewayanslutning från plats till plats från ditt lokala nätverk till det virtuella nätverket. Anvisningarna i den här artikeln gäller för Resource Manager-distributionsmodellen. Du kan också skapa den här konfigurationen med ett annat distributionsverktyg eller en annan distributionsmodell genom att välja ett annat alternativ i listan nedan:

> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Resource Manager – CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Klassisk – Azure Portal](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [Klassisk – Klassisk portal](vpn-gateway-site-to-site-create.md)
> 
>


![Diagram över plats-till-plats-anslutning med VPN-gateway](./media/vpn-gateway-howto-site-to-site-resource-manager-cli/site-to-site-connection-diagram.png)

En VPN-gatewayanslutning från plats till plats används för att ansluta ditt lokala nätverk till ett virtuellt Azure-nätverk via en IPsec/IKE VPN-tunnel (IKEv1 eller IKEv2). Den här typen av anslutning kräver en lokal VPN-enhet som tilldelats till en extern offentlig IP-adress. Mer information om VPN-gatewayer finns i [Om VPN-gateway](vpn-gateway-about-vpngateways.md).

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har uppfyllt följande villkor innan du påbörjar konfigurationen:

* Verifiera att du vill arbeta med distributionsmodellen i Resource Manager. [!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 
* En kompatibel VPN-enhet och någon som kan konfigurera den. Se [Om VPN-enheter](vpn-gateway-about-vpn-devices.md) för mer information om kompatibla VPN-enheter och enhetskonfiguration.
* En extern offentlig IPv4-adress för VPN-enheten. Den här IP-adressen får inte finnas bakom en NAT.
* Om du inte vet vilka IP-adressintervaller som används i din lokala nätverkskonfiguration kontaktar du relevant person som kan ge dig den här informationen. När du skapar den här konfigurationen måste du ange prefix för IP-adressintervall som Azure dirigerar till den lokala platsen. Inget av undernäten i ditt lokala nätverk kan överlappa de virtuella nätverksundernät du vill ansluta till. 
* Den senaste versionen av CLI-kommandona (2.0 eller senare). Information om att installera CLI-kommandona finns i [Installera Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="example-values"></a>Exempelvärden

Du kan använda följande värden till att skapa en testmiljö eller hänvisa till dem för att bättre förstå exemplen i den här artikeln.

```
#Example values

VnetName                = TestVNet1 
ResourceGroup           = TestRG1 
Location                = eastus 
AddressSpace            = 10.12.0.0/16 
SubnetName              = Subnet1 
Subnet                  = 10.12.0.0/24 
GatewaySubnet           = 10.12.255.0/27 
LocalNetworkGatewayName = Site2 
LNG Public IP           = <VPN device IP address>
LocalAddrPrefix1        = 10.0.0.0/24 
LocalAddrPrefix2        = 20.0.0.0/24   
GatewayName             = VNet1GW 
PublicIP                = VNet1GWIP 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = VNet1toSite2
```

## <a name="Login"></a>1. Logga in på Azure

Logga in på Azure-prenumerationen med kommandot [az login](/cli/azure/#login) och följ anvisningarna på skärmen.

```azurecli
az login
```

Om du har fler än en Azure-prenumeration anger du prenumerationerna för kontot.

```azurecli
Az account list --all
```

Ange den prenumeration som du vill använda.

```azurecli
Az account set --subscription <replace_with_your_subscription_id>
```

## <a name="2-create-a-resource-group"></a>2. Skapa en resursgrupp

I följande exempel skapas en resursgrupp med namnet ”TestRG1” på platsen ”eastus”. Om du redan har en resursgrupp i regionen där du vill skapa ditt virtuella nätverk kan du använda den i stället.

```azurecli
az group create -n TestRG1 -l eastus
```

## <a name="VNet"></a>3. Skapa ett virtuellt nätverk

Om du inte redan har ett virtuellt nätverk, skapa ett. När du skapar ett virtuellt nätverk ska du kontrollera att de adressutrymmen du anger inte överlappar några adressutrymmen som du har i det lokala nätverket. 

I följande exempel skapas ett virtuellt nätverk med namnet ”TestVNet1” och ett undernät, ”Subnet1”.

```azurecli
az network vnet create -n TestVNet1 -g TestRG1 --address-prefix 10.12.0.0/16 -l eastus --subnet-name Subnet1 --subnet-prefix 10.12.0.0/24
```

## 4. <a name="gwsub"></a>Skapa gateway-undernätet

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

För den här konfigurationen behöver du också ett gateway-undernät. Den virtuella nätverksgatewayen använder ett gatewayundernät som innehåller de IP-adresser som VPN-gatewaytjänsterna använder. När du skapar ett gateway-undernät måste det få namnet ”GatewaySubnet”. Om du ger det något annat namn kan du skapa undernätet, men Azure behandlar det inte som ett gateway-undernät.

Storleken på gatewayundernätet du anger beror på konfigurationen av VPN-gatewayen du vill skapa. Även om det är möjligt att skapa ett gateway-undernät som är så litet som /29 så rekommenderar vi att du skapar ett större undernät som inkluderar fler adresser genom att välja /27 eller /28. Om du använder det större nätverksundernätet får du tillräckligt många IP-adresser för att hantera möjliga framtida konfigurationer.


```azurecli
az network vnet subnet create --address-prefix 10.12.255.0/27 -n GatewaySubnet -g TestRG1 --vnet-name TestVNet1
```

## <a name="localnet"></a>5. Skapa den lokala nätverksgatewayen

Den lokala nätverksgatewayen avser vanligtvis din lokala plats. Du namnger webbplatsen så att Azure kan referera till den och sedan anger du IP-adressen för den lokala VPN-enhet som du skapar en anslutning till. Du anger också IP-adressprefixen som ska dirigeras via VPN-gatewayen till VPN-enheten. Adressprefixen du anger är de prefix som finns på det lokala nätverket. Det är enkelt att uppdatera dessa prefix om det lokala nätverket ändras.

Ange följande värden:

* *--gateway-ip-address* är IP-adressen till den lokala VPN-enheten. VPN-enheten får inte finnas bakom en NAT.
* *--local-address-prefixes* är dina lokala adressutrymmen.

I följande exempel får du se hur du lägger till en lokal nätverksgateway med flera adressprefix:

```azurecli
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site2 -g TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

## <a name="PublicIP"></a>6. Begär en offentlig IP-adress

Begär en offentlig IP-adress som ska allokeras till din virtuella nätverks-VPN-gateway. Det här är IP-adressen du konfigurerar att VPN-enheten ska anslutas till.

Den virtuella nätverksgatewayen för Resource Manager-distributionsmodellen stöder för närvarande endast offentliga IP-adresser med hjälp av dynamisk fördelning. Detta betyder dock inte IP-adressen kan ändras. Den enda gången VPN-gatewayens IP-adress ändras är när gatewayen tas bort och återskapas. Gatewayens offentliga IP-adress ändras inte vid storleksändring, återställning eller annat internt underhåll/uppgraderingar av din Azure VPN-gateway. 

```azurecli
az network public-ip create -n VNet1GWIP -g TestRG1 --allocation-method Dynamic
```

## <a name="CreateGateway"></a>7. Skapa VPN-gatewayen

Skapa den virtuella VPN-nätverksgatewayen. Det kan ta upp till 45 minuter att skapa en VPN-gateway.

Ange följande värden:

* *--gateway-type* för en plats-till-plats-konfiguration är *Vpn*. Gateway-typen gäller alltid den konfiguration som du implementerar. Se [Gatewaytyper](vpn-gateway-about-vpn-gateway-settings.md#gwtype) för mer information
* *--vpn-type* kan vara *RouteBased* (kallas en dynamisk gateway i viss dokumentation) eller *PolicyBased* (kallas en statisk gateway i viss dokumentation). Inställningen gäller krav på den enhet som du ansluter till. Mer information om VPN-gatewaytyper finns i [Om konfigurationsinställningar för VPN-gateway](vpn-gateway-about-vpn-gateway-settings.md#vpntype).
* *--sku* kan vara Basic, Standard eller HighPerformance. Det finns konfigurationsbegränsningar för vissa SKU:er. Se [Gateway SKU:er](vpn-gateway-about-vpngateways.md#gateway-skus) för mer information.

När du har kört det här kommandot ser du ingen feedback eller några utdata. Det tar cirka 45 minuter att skapa en gateway.

```azurecli
az network vnet-gateway create -n VNet1GW --public-ip-address VNet1GWIP -g TestRG1 --vnet TestVNet1 --gateway-type Vpn --vpn-type RouteBased --sku Standard --no-wait 
```

## <a name="VPNDevice"></a>8. Konfigurera din VPN-enhet

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]
  Du hittar den offentliga IP-adressen för din virtuella nätverksgateway genom att ersätta värdena med dina egna värden i följande exempel. För att läsningen ska gå lätt är utdata formaterade för att visa listan över offentliga IP-adresser i tabellformat.

  ```azurecli
  az network public-ip list -g TestRG1 -o table
  ```

## <a name="CreateConnection"></a>9. Skapa VPN-anslutningen

Skapa VPN-anslutningen för plats-till-plats mellan din virtuella nätverksgateway och din lokala VPN-enhet. Var extra uppmärksam till värdet för den delade nyckeln, som måste matcha det konfigurerade värde för delad nyckel för din VPN-enhet.

```azurecli
az network vpn-connection create -n VNet1toSite2 -g TestRG1 --vnet-gateway1 VNet1GW -l eastus --shared-key abc123 --local-gateway2 Site2
```

Efter en kort stund har anslutningen upprättats.

## <a name="toverify"></a>10. Verifiera VPN-anslutningen

[!INCLUDE [verify connection](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)] 

Om du vill använda en annan metod för att verifiera anslutningen kan du läsa [Verifiera en anslutning till VPN-gateway](vpn-gateway-verify-connection-resource-manager.md).

## <a name="common-tasks"></a>Vanliga åtgärder

### <a name="to-view-local-network-gateways"></a>Visa lokala nätverksgatewayer

```azurecli
az network local-gateway list --resource-group TestRG1
```

### <a name="modify"></a>Så här ändrar du IP-adressprefixen för en lokal nätverksgateway
Använd följande anvisningar om du behöver ändra prefixen för din lokala nätverksgateway. Varje gång du gör en ändring måste hela listan med prefix specificeras, inte bara de prefix du vill ändra.

- **Om du har en specificerad anslutning** använder du exemplet nedan. Ange hela listan med prefix som består av befintliga prefix och dem du vill lägga till. I det här exemplet är 10.0.0.0/24 och 20.0.0.0/24 redan med. Vi lägger till prefixen 30.0.0.0/24 och 40.0.0.0/24.

  ```azurecli
  az network local-gateway update --local-address-prefixes 10.0.0.0/24 20.0.0.0/24 30.0.0.0/24 40.0.0.0/24 -n VNet1toSite2 -g TestRG1
  ```

- **Om du inte har någon angiven anslutning** använder du samma kommando som du använder för att skapa en lokal nätverksgateway. Du kan också använda det här kommandot för att uppdatera gatewayens IP-adress för VPN-enheten. Använd endast det här kommandot när du inte redan har en anslutning. I det här exemplet är 10.0.0.0/24, 20.0.0.0/24, 30.0.0.0/24 och 40.0.0.0/24 redan med. Vi anger endast de prefix vi vill behålla. I det här fallet 10.0.0.0/24 och 20.0.0.0/24.

  ```azurecli
  az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site2 -g TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
  ```

### <a name="modifygwipaddress"></a>Så här ändrar du gateway-IP-adressen för en lokal nätverksgateway

IP-adressen i den här konfigurationen är IP-adressen för den VPN-enhet du skapar en anslutning till. Om VPN-enhetens IP-adress ändras kan du justera värdet. IP-adressen kan ändras även om det finns en gatewayanslutning.

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 -n Site2 -g TestRG1
```

När du visar resultatet ska du kontrollera att IP-adressens prefix ingår.

  ```azurecli
  "localNetworkAddressSpace": { 
    "addressPrefixes": [ 
      "10.0.0.0/24", 
      "20.0.0.0/24", 
      "30.0.0.0/24" 
    ] 
  }, 
  "location": "eastus", 
  "name": "Site2", 
  "provisioningState": "Succeeded",  
  ```

### <a name="to-view-the-virtual-network-gateway-public-ip-address"></a>Visa offentliga IP-adressen för den virtuella nätverksgatewayen

Använd följande exempel för att hitta den offentliga IP-adressen till din virtuella nätverksgateway. För att läsningen ska gå lätt är utdata formaterade för att visa listan över offentliga IP-adresser i tabellformat.

```azurecli
az network public-ip list -g TestRG1 -o table
```

### <a name="to-verify-the-shared-key-values"></a>Kontrollera delade nyckelvärden

Kontrollera att värdet för den delade nyckeln är samma värde som du använde till VPN-enhetens konfiguration. Om inte ska du antingen köra anslutningen igen med värdet från enheten eller uppdatera enheten med värdet från resultatet. Värdena måste matcha.

```azurecli
az network vpn-connection shared-key show --connection-name VNet1toSite2 -g TestRG1
```

## <a name="next-steps"></a>Nästa steg

*  När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [Virtuella datorer](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
* Information om BGP finns i [BGP-översikt](vpn-gateway-bgp-overview.md) och [Så här konfigurerar du BGP](vpn-gateway-bgp-resource-manager-ps.md).
* Information om tvingad tunneltrafik finns i [Konfigurera forcerade tunnlar](vpn-gateway-forced-tunneling-rm.md).
* Om du vill ha en lista över Azure CLI-nätverkskommandon finns det i [Azure CLI](https://docs.microsoft.com/cli/azure/network).
