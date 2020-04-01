---
title: 'Självstudiekurs: Skapa och testa en NAT-gateway – Azure CLI'
titlesuffix: Azure Virtual Network NAT
description: Den här självstudien visar hur du skapar en NAT-gateway med Hjälp av Azure CLI och testar NAT-tjänsten
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: b3e10b3abbe5c9815e51ce67786882dbd294df3f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79202252"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-cli-and-test-the-nat-service"></a>Självstudiekurs: Skapa en NAT-gateway med Azure CLI och testa NAT-tjänsten

I den här självstudien skapar du en NAT-gateway för att tillhandahålla utgående anslutning för virtuella datorer i Azure. Om du vill testa NAT-gatewayen distribuerar du en virtuell dator för källa och mål. Du ska testa NAT-gatewayen genom att skapa utgående anslutningar till en offentlig IP-adress. Dessa anslutningar kommer från källan till den virtuella måldatorn. Den här självstudien distribuerar källa och mål i två olika virtuella nätverk i samma resursgrupp endast för enkelhetens skull.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Du kan slutföra den här självstudien med Azure Cloud Shell eller köra respektive kommandon lokalt.  Om du inte har använt Azure Cloud Shell bör du [logga in nu](https://shell.azure.com).

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

För att komma åt det offentliga Internet behöver du en eller flera offentliga IP-adresser för NAT-gatewayen. Använd [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) för att skapa en offentlig IP-adressresurs med namnet **myPublicIPsource** i **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPsource \
  --sku standard
```

### <a name="create-a-public-ip-prefix"></a>Skapa ett offentligt IP-prefix

Du kan använda en eller flera offentliga IP-adressresurser, offentliga IP-prefix eller båda med NAT-gateway. Vi lägger till en offentlig IP-prefixresurs i det här scenariot för att demonstrera.   Använd [az network public-ip prefix create](/cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-create) för att skapa en offentlig IP-prefixresurs med namnet **myPublicIPprefixsource** i **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip prefix create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPprefixsource \
  --length 31
```

### <a name="create-a-nat-gateway-resource"></a>Skapa en NAT-gatewayresurs

I det här avsnittet beskrivs hur du kan skapa och konfigurera följande komponenter i NAT-tjänsten med hjälp av NAT-gatewayresursen:
  - En offentlig IP-pool och offentligt IP-prefix som ska användas för utgående flöden som översätts av NAT-gatewayresursen.
  - Ändra tidsgränsen för inaktiv tid från standardvärdet 4 minuter till 10 minuter.

Skapa en global Azure NAT-gateway med [az network nat gateway create](https://docs.microsoft.com/cli/azure/network/nat?view=azure-cli-latest) named **myNATgateway**. Kommandot använder både den offentliga IP-adressen **myPublicIP** och det offentliga IP-prefixet **myPublicIPprefix**. Kommandot ändrar också tidsgränsen för inaktiv tid till 10 minuter.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIPsource \
    --public-ip-prefixes myPublicIPprefixsource \
    --idle-timeout 10       
  ```

Nu fungerar NAT-gatewayen och allt som saknas är att konfigurera vilka undernät i ett virtuellt nätverk som ska använda den.

## <a name="prepare-the-source-for-outbound-traffic"></a>Förbereda källan för utgående trafik

Vi guidar dig genom installation av en fullständig testmiljö. Du konfigurerar ett test med verktyg med öppen källkod för att verifiera NAT-gatewayen. Vi börjar med källan, som kommer att använda NAT gateway vi skapade tidigare.

### <a name="configure-virtual-network-for-source"></a>Konfigurera virtuellt nätverk för källa

Innan du distribuerar en virtuell dator och kan testa din NAT-gateway måste vi skapa det virtuella nätverket.

Skapa ett virtuellt nätverk med namnet **myVnetsource** med ett undernät med namnet **mySubnetsource** i **myResourceGroupNAT** med [az-nätverket Microsoft Azure Virtual Network create](https://docs.microsoft.com/cli/azure/network/vnet).  IP-adressutrymmet för det virtuella nätverket är **192.168.0.0/16**. Undernätet i det virtuella nätverket är **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnetsource \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetsource \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>Konfigurera NAT-tjänst för källundernät

Konfigurera källundernätet **mySubnetsource** i virtuellt nätverk **myVnetsource** för att använda en specifik NAT-gatewayresurs **myNATgateway** med uppdatering av [az-nätverket Microsoft Azure Virtual Network](https://docs.microsoft.com/cli/azure/network/vnet/subnet). Det här kommandot aktiverar NAT-tjänsten i det angivna undernätet.

```azurecli-interactive
    az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnetsource \
    --name mySubnetsource \
    --nat-gateway myNATgateway
```

All utgående trafik till Internet-destinationer använder nu NAT-tjänsten.  Det är inte nödvändigt att konfigurera en UDR.

Innan vi kan testa NAT-gatewayen måste vi skapa en käll-VM.  Vi tilldelar en offentlig IP-adressresurs som en offentlig IP-ip på instansnivå för att komma åt den här virtuella datorn utifrån. Den här adressen används endast för att komma åt den för testet.  Vi visar hur NAT-tjänsten har företräde framför andra utgående alternativ.

Du kan också skapa den här virtuella datorn utan en offentlig IP och skapa en annan virtuell dator som ska användas som en jumpbox utan en offentlig IP som en övning.

### <a name="create-public-ip-for-source-vm"></a>Skapa offentlig IP för käll-VM

Vi skapar en offentlig IP som ska användas för att komma åt källdatorn. Använd [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) för att skapa en offentlig IP-adressresurs med namnet **myPublicIPsourceVM** i **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --sku standard
```

### <a name="create-an-nsg-for-source-vm"></a>Skapa en virtuell dator med NSG för käll-

Eftersom vanliga offentliga IP-adresser är "säkra som standard", måste vi skapa en NSG för att tillåta inkommande åtkomst för ssh-åtkomst.  Azure NAT-tjänsten är flödesriktningsmedveten. Den här NSG-funktionen används inte för utgående när NAT-gatewayen har konfigurerats i samma undernät. Använd [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) för att skapa en NSG-resurs med namnet **myNSGsource** i **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGsource 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>Exponera SSH-slutpunkt på käll-VM

Vi skapar en regel i NSG för SSH-åtkomst till källdatorn. Använd [az network nsg-regel skapa](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) för att skapa en NSG-regel med namnet **ssh**. Den här regeln skapas i NSG med namnet **myNSGsource** i resursgruppen **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGsource \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="create-nic-for-source-vm"></a>Skapa nätverkskort för käll-VM

Skapa ett nätverksgränssnitt med [az network nic skapa](/cli/azure/network/nic#az-network-nic-create) och associera med den offentliga IP-adressen och nätverkssäkerhetsgruppen. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicsource \
    --vnet-name myVnetsource \
    --subnet mySubnetsource \
    --public-ip-address myPublicIPSourceVM \
    --network-security-group myNSGsource
```

### <a name="create-a-source-vm"></a>Skapa en virtuell källa

Skapa den virtuella datorn med [az vm skapa](/cli/azure/vm#az-vm-create).  Vi genererar ssh nycklar för den här virtuella datorn och lagra den privata nyckeln att använda senare.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMsource \
    --nics myNicsource \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
```

Medan kommandot kommer tillbaka omedelbart, kan det ta några minuter för den virtuella datorn att få distribueras.

## <a name="prepare-destination-for-outbound-traffic"></a>Förbered destination för utgående trafik

Vi skapar nu en destination för den utgående trafik som översätts av NAT-tjänsten så att du kan testa den.

### <a name="configure-virtual-network-for-destination"></a>Konfigurera virtuellt nätverk för mål

 Vi måste skapa ett virtuellt nätverk där målet virtuell maskin kommer att vara.  Dessa kommandon är samma steg som för källdatorn med små ändringar för att exponera målslutpunkten.

Skapa ett virtuellt nätverk med namnet **myVnetdestination** med ett undernät med namnet **mySubnetdestination** i **myResourceGroupNAT** med [az-nätverket Microsoft Azure Virtual Network create](https://docs.microsoft.com/cli/azure/network/vnet).  IP-adressutrymmet för det virtuella nätverket är **192.168.0.0/16**. Undernätet i det virtuella nätverket är **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location westus \
    --name myVnetdestination \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetdestination \
    --subnet-prefix 192.168.0.0/24
```

### <a name="create-public-ip-for-destination-vm"></a>Skapa offentlig IP för mål-VM

Vi skapar en offentlig IP som ska användas för att komma åt källdatorn. Använd [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) för att skapa en offentlig IP-adressresurs med namnet **myPublicIPdestinationVM** i **myResourceGroupNAT**. 

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPdestinationVM \
  --sku standard \
  --location westus
```

### <a name="create-an-nsg-for-destination-vm"></a>Skapa en NSG för mål-VM

Standard offentliga IP-adresser är "säkra som standard", måste du skapa en NSG för att tillåta inkommande åtkomst för ssh. Azure NAT-tjänsten är flödesriktningsmedveten. Den här NSG-funktionen används inte för utgående när NAT-gatewayen har konfigurerats i samma undernät. Använd [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) för att skapa en NSG-resurs med namnet **myNSGdestination** i **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGdestination \
    --location westus
```

### <a name="expose-ssh-endpoint-on-destination-vm"></a>Exponera SSH-slutpunkt på mål-VM

Vi skapar en regel i NSG för SSH-åtkomst till måldas. Använd [az network nsg-regel skapa](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) för att skapa en NSG-regel med namnet **ssh**. Den här regeln skapas i NSG med namnet **myNSGdestination** i resursgruppen **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="expose-http-endpoint-on-destination-vm"></a>Exponera HTTP-slutpunkt på mål-VM

Vi skapar en regel i NSG för HTTP-åtkomst till måldas. Använd [az network nsg-regel skapa](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create) för att skapa en NSG-regel som heter **http** i NSG som heter **myNSGdestination** i **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 101 \
    --name http \
    --description "HTTP access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 80
```

### <a name="create-nic-for-destination-vm"></a>Skapa nätverkskort för mål-VM

Skapa ett nätverksgränssnitt med [az nätverk nic skapa](/cli/azure/network/nic#az-network-nic-create) och associera med den offentliga IP-adressen **myPublicIPdestinationVM** och nätverkssäkerhetsgruppen **myNSGdestination**. 

```azurecli-interactive
    az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicdestination \
    --vnet-name myVnetdestination \
    --subnet mySubnetdestination \
    --public-ip-address myPublicIPdestinationVM \
    --network-security-group myNSGdestination \
    --location westus
```

### <a name="create-a-destination-vm"></a>Skapa en virtuell måldator

Skapa den virtuella datorn med [az vm skapa](/cli/azure/vm#az-vm-create).  Vi genererar ssh nycklar för den här virtuella datorn och lagra den privata nyckeln att använda senare.

 ```azurecli-interactive
    az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMdestination \
    --nics myNicdestination \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait \
    --location westus
```
Medan kommandot kommer tillbaka omedelbart, kan det ta några minuter för den virtuella datorn att få distribueras.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Förbereda en webbserver och testa nyttolasten på måldatorn

Först måste vi upptäcka IP-adressen för målet VM.  Om du vill hämta den offentliga [IP-adressen](/cli/azure/network/public-ip#az-network-public-ip-show)för den virtuella måldatorn använder du az network public ip show . 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPdestinationVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Kopiera den offentliga IP-adressen och klistra sedan in den i ett anteckningsblock så att du kan använda den i efterföljande steg. Ange att det här är den virtuella måldatorn.

### <a name="sign-in-to-destination-vm"></a>Logga in på mål-VM

SSH-autentiseringsuppgifterna ska lagras i ditt Cloud Shell från den föregående åtgärden.  Öppna ett [Azure Cloud Shell](https://shell.azure.com) i webbläsaren. Använd IP-adressen som hämtades i föregående steg till SSH till den virtuella datorn. 

```bash
ssh <ip-address-destination>
```

Kopiera och klistra in följande kommandon när du har loggat in.  

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100
```

Dessa kommandon uppdaterar din virtuella dator, installerar nginx och skapar en 100-KBytes-fil. Den här filen hämtas från källdatorn med NAT-tjänsten.

Stäng SSH-sessionen med den virtuella måldatorn.

## <a name="prepare-test-on-source-vm"></a>Förbereda test på käll-VM

Först måste vi upptäcka IP-adressen för käll-VM.  Om du vill hämta den offentliga [IP-adressen](/cli/azure/network/public-ip#az-network-public-ip-show)för källdatorn använder du az network public ip show . 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Kopiera den offentliga IP-adressen och klistra sedan in den i ett anteckningsblock så att du kan använda den i efterföljande steg. Ange att det här är den virtuella datorn för källan.

### <a name="sign-in-to-source-vm"></a>Logga in på käll-VM

Återigen lagras SSH-autentiseringsuppgifterna i Cloud Shell. Öppna en ny flik för [Azure Cloud Shell](https://shell.azure.com) i webbläsaren.  Använd IP-adressen som hämtades i föregående steg till SSH till den virtuella datorn. 

```bash
ssh <ip-address-source>
```

Kopiera och klistra in följande kommandon för att förbereda för testning av NAT-tjänsten.

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

Det här kommandot uppdaterar din virtuella dator, installerar go, installerar [hey](https://github.com/rakyll/hey) från GitHub och uppdaterar skalmiljön.

Du är nu redo att testa NAT-tjänsten.

## <a name="validate-nat-service"></a>Validera NAT-tjänsten

När du är inloggad på källdatorn kan du använda **curl** and **hey** för att generera begäranden till mål-IP-adressen.

Använd curl för att hämta filen 100-KBytes.  Ersätt ** \<ip-adress-mål>** i exemplet nedan med den mål-IP-adress som du tidigare har kopierat.  Parametern **--output** anger att den hämtade filen kommer att ignoreras.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

Du kan också generera en serie begäranden med **hey**. Återigen, ersätta ** \<ip-adress-mål>** med målet IP-adress som du tidigare har kopierat.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Det här kommandot genererar 100 begäranden, 10 samtidigt, med en timeout på 30 sekunder. TCP-anslutningen kommer inte att återanvändas.  Varje begäran hämtar 100 Kbyte.  I slutet av körningen, **hey** kommer att rapportera en del statistik om hur bra NAT-tjänsten gjorde.

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs kan du använda kommandot [az-gruppborttagning](/cli/azure/group#az-group-delete) för att ta bort resursgruppen och alla resurser som finns i.

```azurecli-interactive 
  az group delete --name myResourceGroupNAT
```

## <a name="next-steps"></a>Nästa steg
I den här självstudien skapade du en NAT-gateway, skapade en käll- och mål-VM och testade sedan NAT-gatewayen.

Granska mått i Azure Monitor för att se nat-tjänsten fungera. Diagnostisera problem som resursutmattning av tillgängliga SNAT-portar.  Resursutmattning av SNAT-portar åtgärdas enkelt genom att lägga till ytterligare offentliga IP-adressresurser eller offentliga IP-prefixresurser eller båda.

- Lär dig mer om [NAT för virtuellt nätverk](./nat-overview.md)
- Lär dig mer om [NAT-gatewayresurs](./nat-gateway-resource.md).
- Snabbstart för distribution av [NAT-gatewayresurs med Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Snabbstart för distribution av [NAT-gatewayresurs med Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Snabbstart för distribution av [NAT-gatewayresurs med Azure-portalen](./quickstart-create-nat-gateway-portal.md).

> [!div class="nextstepaction"]

