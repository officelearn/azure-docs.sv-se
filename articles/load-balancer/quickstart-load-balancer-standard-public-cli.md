---
title: Snabbstart:Skapa en offentlig belastningsutjämnare - Azure CLI
titleSuffix: Azure Load Balancer
description: Den här snabbstarten visar hur du skapar en offentlig lastbalanserare med Azure CLI
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
tags: azure-resource-manager
Customer intent: I want to create a Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: a8bcdd88-f94c-4537-8143-c710eaa86818
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 1f6a05fdfc28adf412ffbd1402e37b69d1c51634
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79477773"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-using-azure-cli"></a>Snabbstart: Skapa en standardbelastningsutjämnare för att ladda fördelning virtuella datorer med Azure CLI

Den här snabbstarten visar hur du skapar en offentlig belastningsutjämnare. Om du vill testa lastbalanseraren så distribuera två virtuella datorer (VM) som kör Ubuntu-servern och lastbalansera en webbapp mellan de två virtuella datorerna.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Om du väljer att installera och använda CLI lokalt så kräver den här självstudiekursen att du kör Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med [az group create](https://docs.microsoft.com/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resursgrupp med namnet *myResourceGroupSLB* på platsen *eastus*:

```azurecli-interactive
  az group create \
    --name myResourceGroupSLB \
    --location eastus
```

## <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress

För att du ska kunna komma åt din webbapp på Internet behöver du en offentlig IP-adress för lastbalanseraren. Använd [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) för att skapa en redundant offentlig IP-adress med namnet *MyPublicIP* i *myResourceGroupSLB*.

```azurecli-interactive
  az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku standard
```

Så här skapar du en offentlig IP-adress i zon 1:

```azurecli-interactive
  az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku standard --zone 1
```

Används `-SKU Basic` för att skapa en grundläggande offentlig IP. Grundläggande offentliga IPs är **Standard** inte kompatibla med standardbelastningsutjämnare. Microsoft rekommenderar att du använder **Standard** för produktionsarbetsbelastningar.

> [!IMPORTANT]
> Resten av den här snabbstarten förutsätter att **Standard** SKU väljs under SKU-urvalsprocessen ovan.

## <a name="create-azure-load-balancer"></a>Skapa Azure Load Balancer

I det här avsnittet beskrivs hur du gör för att skapa och konfigurera följande komponenter i lastbalanseraren:
  - en klientdels-IP-pool som tar emot inkommande nätverkstrafik i lastbalanseraren.
  - en serverdels-IP-pool dit klientdelspoolen skickar den belastningsutjämnade nätverkstrafiken.
  - en hälsoavsökning som fastställer hälsan för serverdelens virtuella datorinstanser.
  - en lastbalanseringsregel som definierar hur trafiken ska distribueras till de virtuella datorerna.

### <a name="create-the-load-balancer"></a>Skapa lastbalanseraren

Skapa en offentlig Azure Load Balancer med [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) med namnet **myLoadBalancer** som innehåller en klientdelspool med namnet **myFrontEnd**, och en serverdelspool med namnet **myBackEndPool** som är associerad med den offentliga IP-adressen **myPublicIP** som du skapade i föregående steg. Används `--sku basic` för att skapa en grundläggande offentlig IP. Microsoft rekommenderar Standard SKU för produktionsarbetsbelastningar.

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupSLB \
    --name myLoadBalancer \
    --sku standard \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

> [!IMPORTANT]
> Resten av den här snabbstarten förutsätter att **Standard** SKU väljs under SKU-urvalsprocessen ovan.

### <a name="create-the-health-probe"></a>Skapar hälsoavsökningen

En hälsoavsökning kontrollerar alla virtuella datorinstanser för att säkerställa att de kan skicka nätverkstrafik. Den virtuella datorinstansen med misslyckad hälsoavsökning tas bort från lastbalanseraren tills den är tillbaka online och en avsökningskontroll visar att den är felfri. Skapa en hälsoavsökning med [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) så att du kan övervaka de virtuella datorernas hälsotillstånd. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupSLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Skapa lastbalanseringsregeln

En lastbalanseringsregel definierar klientdelens IP-konfiguration för inkommande trafik och serverdelens IP-pool för att ta emot trafiken, tillsammans med nödvändiga käll- och målportar. Skapa lastbalanseringsregeln *myLoadBalancerRuleWeb* med [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) så att du kan lyssna på port 80 i klientdelspoolen *myFrontEnd* och skicka lastbalanserad nätverkstrafik till serverdelsadresspoolen *myBackEndPool* som också använder port 80. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupSLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```

## <a name="configure-virtual-network"></a>Konfigurera ett virtuellt nätverk

Innan du kan distribuera virtuella datorer och testa din lastbalanserare måste du skapa virtuella nätverksresurser.

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa det virtuella nätverket *myVnet* med undernätet *mySubnet* i *myResourceGroup* med [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet).

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupSLB \
    --location eastus \
    --name myVnet \
    --subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

För en Standard Load Balancer krävs de virtuella datorerna i serverdelsadressen för att kunna ha nätverkskort som tillhör en nätverkssäkerhetsgrupp. Skapa en nätverkssäkerhetsgrupp så att du kan definiera inkommande anslutningar till det virtuella nätverket.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupSLB \
    --name myNetworkSecurityGroup
```

### <a name="create-a-network-security-group-rule"></a>Skapa en regel för nätverkssäkerhetsgruppen

Skapa en regel för nätverkssäkerhetsgrupp som tillåter inkommande anslutningar via port 80.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupSLB \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleHTTP \
    --protocol tcp \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

### <a name="create-nics"></a>Skapa nätverkskort

Skapa tre nätverksgränssnitt med [az network nic create](/cli/azure/network/nic#az-network-nic-create) och koppla dem till den offentliga IP-adressen och nätverkssäkerhetsgruppen. 

```azurecli-interactive

  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNicVM1 \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool

  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
  
  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNicVM3 \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool

```

## <a name="create-backend-servers"></a>Skapa serverdelsservrar

I det här exemplet skapar du tre virtuella datorer som ska användas som serverdelsservrar för lastbalanseraren. För att verifiera att lastbalanseraren har skapats, installerar du NGINX på de virtuella datorerna.

Om du skapar en grundläggande belastningsutjämnare med en grundläggande offentlig IP måste du skapa en tillgänglighetsuppsättning med[(az vm-tillgänglighetsuppsättning skapa](/cli/azure/network/nic) för att lägga till dina virtuella datorer i. Standardbelastningsbesaldon kräver inte det här ytterligare steget. Microsoft rekommenderar att du använder Standard.

### <a name="create-three-virtual-machines"></a>Skapa tre virtuella datorer

Du kan använda en konfigurationsfil för cloud-init för att installera NGINX och köra en Hello World Node.js-app på en virtuell Linux-dator. Skapa en fil med namnet cloud-init.txt i ditt nuvarande gränssnitt och kopiera och klistra in följande konfiguration i gränssnittet. Se till att kopiera hela cloud-init-filen korrekt, särskilt den första raden:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

Skapa de virtuella datorerna med [az vm create](/cli/azure/vm#az-vm-create).

```azurecli-interactive

  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM1 \
    --availability-set myAvailabilitySet \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --no-wait
   
  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM2 \
    --availability-set myAvailabilitySet \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --no-wait

   az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM3 \
    --availability-set myAvailabilitySet \
    --nics myNicVM3 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --no-wait

```

Det kan ta några minuter för de virtuella datorerna att distribueras.

## <a name="test-the-load-balancer"></a>Testa lastbalanseraren

Hämta lastbalanserarens offentliga IP-adress med [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält.

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```

   ![Testa lastbalanseraren](./media/load-balancer-standard-public-cli/running-nodejs-app.png)

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du ta bort resursgruppen, lastbalanseraren och alla relaterade resurser med kommandot [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive
  az group delete --name myResourceGroupSLB
```

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten skapade du en standardbelastningsutjämningsapparat, kopplade virtuella datorer till den, konfigurerade trafikregeln lastutjämning, hälsoavsökning och testade sedan belastningsutjämnaren. Om du vill veta mer om Azure Load Balancer fortsätter du till [Azure Load Balancer-självstudier](tutorial-load-balancer-standard-public-zone-redundant-portal.md).

Läs mer om [belastningsutjämnare och tillgänglighetszoner](load-balancer-standard-availability-zones.md).