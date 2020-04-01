---
title: 'Snabbstart: Skapa en NAT-gateway - Azure CLI'
titlesuffix: Azure Virtual Network NAT
description: Den här snabbstarten visar hur du skapar en NAT-gateway med Azure CLI
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumundD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 9402960927f56092e226ab81bd3e6ede0cf6a52d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79202206"
---
# <a name="quickstart-create-a-nat-gateway-using-azure-cli"></a>Snabbstart: Skapa en NAT-gateway med Azure CLI

Den här snabbstarten visar hur du använder Azure Virtual Network NAT-tjänst. Du skapar en NAT-gateway för att tillhandahålla utgående anslutning för en virtuell dator i Azure. 


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Du kan slutföra den här självstudien med Azure Cloud Shell eller köra respektive kommandon lokalt.  Om du aldrig har använt Azure Cloud Shell [loggar du in nu](https://shell.azure.com) för att gå igenom den första installationen.
Om du väljer att köra dessa kommandon lokalt måste du installera CLI.  Den här självstudien kräver att du kör en version av Azure CLI version 2.0.71 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).


## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med [az group create](https://docs.microsoft.com/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resursgrupp med namnet **myResourceGroupNAT** på **platsen eastus2:**

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>Skapa NAT-gatewayen

### <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress

För att komma åt det offentliga Internet behöver du en eller flera offentliga IP-adresser för NAT-gatewayen. Använd [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) för att skapa en offentlig IP-adressresurs med namnet **myPublicIP** i **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard
```

### <a name="create-a-public-ip-prefix"></a>Skapa ett offentligt IP-prefix

Du kan använda en eller flera offentliga IP-adressresurser, offentliga IP-prefix eller båda med NAT-gateway. Vi lägger till en offentlig IP-prefixresurs i det här scenariot för att demonstrera.   Använd [az network public-ip prefix create](https://docs.microsoft.com/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create) för att skapa en offentlig IP-prefixresurs med namnet **myPublicIPprefix** i **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPprefix \
    --length 31
```

### <a name="create-a-nat-gateway-resource"></a>Skapa en NAT-gatewayresurs

I det här avsnittet beskrivs hur du kan skapa och konfigurera följande komponenter i NAT-tjänsten med hjälp av NAT-gatewayresursen:
  - En offentlig IP-pool och offentligt IP-prefix som ska användas för utgående flöden som översätts av NAT-gatewayresursen.
  - Ändra tidsgränsen för inaktiv tid från standardvärdet 4 minuter till 10 minuter.

Skapa en global Azure NAT-gateway med [az network nat gateway create](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) named **myNATgateway**. Kommandot använder både den offentliga IP-adressen **myPublicIP** och det offentliga IP-prefixet **myPublicIPprefix**. Kommandot ändrar tidsgränsen för inaktiv tid till **10** minuter.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --public-ip-prefixes myPublicIPprefix \
    --idle-timeout 10       
  ```

Nu fungerar NAT-gatewayen och allt som saknas är att konfigurera vilka undernät i ett virtuellt nätverk som ska använda den.

## <a name="configure-virtual-network"></a>Konfigurera ett virtuellt nätverk

Innan du distribuerar en virtuell dator och kan använda din NAT-gateway måste vi skapa det virtuella nätverket.

Skapa ett virtuellt nätverk med namnet **myVnet** med ett undernät med namnet **mySubnet** i **myResourceGroupNAT** med [az-nätverksvnät skapa](https://docs.microsoft.com/cli/azure/network/vnet).  IP-adressutrymmet för det virtuella nätverket är **192.168.0.0/16**. Undernätet i det virtuella nätverket är **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>Konfigurera NAT-tjänst för källundernät

Vi konfigurerar källundernätet **mySubnet** i virtuellt nätverk **myVnet** för att använda en specifik NAT gateway-resurs **myNATgateway** med [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet).  Det här kommandot aktiverar NAT-tjänsten i det angivna undernätet.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

All utgående trafik till Internet-destinationer använder nu NAT-gatewayen.  Det är inte nödvändigt att konfigurera en UDR.

## <a name="create-a-vm-to-use-the-nat-service"></a>Skapa en virtuell dator för att använda NAT-tjänsten

Vi ska nu skapa en virtuell dator för att använda NAT-tjänsten.  Den här virtuella datorn har en offentlig IP att använda som en offentlig IP på instansnivå så att du kan komma åt den virtuella datorn.  NAT-tjänsten är flödesriktningsmedveten och ersätter standardmålet för Internet i undernätet. Den virtuella datorns offentliga IP-adress används inte för utgående anslutningar.

### <a name="create-public-ip-for-source-vm"></a>Skapa offentlig IP för käll-VM

Vi skapar en offentlig IP som ska användas för att komma åt den virtuella datorn.  Använd [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) för att skapa en offentlig IP-adressresurs med namnet **myPublicIPVM** i **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --sku standard
```

### <a name="create-an-nsg-for-vm"></a>Skapa en NSG för vm

Eftersom standard offentliga IP-adresser är "säkra som standard", måste vi skapa en NSG för att tillåta inkommande åtkomst för ssh-åtkomst. Använd [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) för att skapa en NSG-resurs med namnet **myNSG** i **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSG 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>Exponera SSH-slutpunkt på käll-VM

Vi skapar en regel i NSG för SSH-åtkomst till källdatorn. Använd [az network nsg-regel skapa](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) för att skapa en NSG-regel med namnet **ssh** i NSG med namnet **myNSG** i **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSG \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="create-nic-for-vm"></a>Skapa nätverkskort för virtuell dator

Skapa ett nätverksgränssnitt med [az network nic skapa](/cli/azure/network/nic#az-network-nic-create) och associera med den offentliga IP-adressen och nätverkssäkerhetsgruppen. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIPVM \
    --network-security-group myNSG
```

### <a name="create-vm"></a>Skapa en virtuell dator

Skapa den virtuella datorn med [az vm skapa](/cli/azure/vm#az-vm-create).  Vi genererar ssh nycklar för den här virtuella datorn och lagra den privata nyckeln att använda senare.

 ```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Vänta tills den virtuella datorn ska distribuera och fortsätt sedan med resten av stegen.

## <a name="discover-the-ip-address-of-the-vm"></a>Upptäck DEN virtuella datorns IP-adress

Först måste vi upptäcka IP-adressen för den virtuella datorn som du har skapat. Om du vill hämta den offentliga [IP-adressen](/cli/azure/network/public-ip#az-network-public-ip-show)för den virtuella datorn använder du az network public ip show . 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Kopiera den offentliga IP-adressen och klistra sedan in den i ett anteckningsblock så att du kan använda den för att komma åt den virtuella datorn.

### <a name="sign-in-to-vm"></a>Logga in på virtuell dator

SSH-autentiseringsuppgifterna ska lagras i ditt Cloud Shell från den föregående åtgärden.  Öppna ett [Azure Cloud Shell](https://shell.azure.com) i webbläsaren. Använd IP-adressen som hämtades i föregående steg till SSH till den virtuella datorn.

```bash
ssh <ip-address-destination>
```

Du är nu redo att använda NAT-tjänsten.

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs kan du använda kommandot [az-gruppborttagning](/cli/azure/group#az-group-delete) för att ta bort resursgruppen och alla resurser som finns i.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en NAT-gateway och en virtuell dator för att använda den. 

Granska mått i Azure Monitor för att se nat-tjänsten fungera. Diagnostisera problem som resursutmattning av tillgängliga SNAT-portar.  Resursutmattning av SNAT-portar åtgärdas genom att lägga till ytterligare offentliga IP-adressresurser eller offentliga IP-prefixresurser eller båda.


- Lär dig mer om [AZURE Virtual Network NAT](./nat-overview.md)
- Lär dig mer om [NAT-gatewayresurs](./nat-gateway-resource.md).
- Snabbstart för distribution av [NAT-gatewayresurs med Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Snabbstart för distribution av [NAT-gatewayresurs med Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Snabbstart för distribution av [NAT-gatewayresurs med Azure-portalen](./quickstart-create-nat-gateway-portal.md).
> [!div class="nextstepaction"]

