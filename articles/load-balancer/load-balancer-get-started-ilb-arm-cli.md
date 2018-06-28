---
title: Skapa en intern belastningsutjämnare – Azurer CLI 2.0 | Microsoft Docs
description: Lär dig hur du skapar en intern belastningsutjämnare med hjälp av Azure CLI 2.0
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2017
ms.author: kumud
ms.openlocfilehash: a4093926ea2ea2bb0e477372a1ceb2dfbf22e8f0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "36330976"
---
# <a name="create-an-internal-load-balancer-to-load-balance-vms-using-azure-cli-20"></a>Skapa en intern belastningsutjämnare som belastningsutjämnar virtuella datorer med Azure CLI 2.0

Den här artikeln visar hur du skapar en intern belastningsutjämnare för att belastningsutjämna virtuella datorer. Om du vill testa belastningsutjämnaren så distribuerar du två virtuella datorer (VM:ar) som kör Ubuntu-server för att belastningsutjämna en webbapp.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Om du väljer att installera och använda CLI lokalt så kräver den här självstudiekursen att du kör Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med [az group create](https://docs.microsoft.com/cli/azure/group#create). En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras.

Följande exempel skapar en resursgrupp med namnet *myResourceGroupILB* på platsen *eastus*:

```azurecli-interactive
  az group create \
    --name myResourceGroupILB \
    --location eastus
```
## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med namnet *myVnet* med undernätet *mySubnet* i *myResourceGroup* med [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#create).

```azurecli-interactive
  az network vnet create \
    --name myVnet \
    --resource-group myResourceGroupILB \
    --location eastus \
    --subnet-name mySubnet
```
## <a name="create-basic-load-balancer"></a>Skapa en belastningsutjämnare

Det här avsnittet beskriver hur du gör för att skapa och konfigurera följande komponenter i belastningsutjämnaren:
  - en klientdels-IP-konfiguration som tar emot inkommande nätverkstrafik i belastningsutjämnaren.
  - en serverdels-IP-pool dit klientdelspoolen skickar den belastningsutjämnade nätverkstrafiken.
  - en hälsoavsökning som fastställer hälsan för serverdelens virtuella datorinstanser.
  - en belastningsutjämningsregel som definierar hur trafiken ska distribueras till de virtuella datorerna.

### <a name="create-the-load-balancer"></a>Skapa belastningsutjämnaren

Skapa en offentlig belastningsutjämnare med [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#create) med namnet **myLoadBalancer** som innehåller en klientdels-IP-konfiguration med namnet **myFrontEnd**, en serverdelspool med namnet **myBackEndPool** som är associerad med en offentlig IP-adress **10.0.0.7.

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupILB \
    --name myLoadBalancer \
    --frontend-ip-name myFrontEnd \
    --private-ip-address 10.0.0.7 \
    --backend-pool-name myBackEndPool \
    --vnet-name myVnet \
    --subnet mySubnet      
  ```
### <a name="create-the-health-probe"></a>Skapar hälsoavsökningen

En hälsoavsökning kontrollerar alla virtuella datorinstanser för att säkerställa att de kan ta emot nätverkstrafik. Den virtuella datorinstansen med misslyckad hälsoavsökning tas bort från belastningsutjämnaren tills den är tillbaka online och en avsökningskontroll visar att den är felfri. Skapa en hälsoavsökning med [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#create) så att du kan övervaka de virtuella datorernas hälsotillstånd. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupILB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Skapa belastningsutjämningsregeln

En belastningsutjämningsregel definierar klientdelens IP-konfiguration för inkommande trafik och serverdelens IP-pool för att ta emot trafiken, tillsammans med nödvändiga käll- och målportar. Skapa belastningsutjämningsregeln *myLoadBalancerRuleWeb* med [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#create) så att du kan lyssna på port 80 i klientdelspoolen *myFrontEndPool* och skicka belastningsutjämnad nätverkstrafik till serverdelsadresspoolen *myBackEndPool* som också använder port 80. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupILB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```

## <a name="create-servers-for-the-backend-address-pool"></a>Skapa servrar för serverdelens adresspool

Innan du distribuerar några virtuella datorer och testar din belastningsutjämnare, måste du skapa virtuella nätverksresurser som stöd.

###  <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp
Skapa en nätverkssäkerhetsgrupp så att du kan definiera inkommande anslutningar till det virtuella nätverket.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupILB \
    --name myNetworkSecurityGroup
```

### <a name="create-a-network-security-group-rule"></a>Skapa en regel för nätverkssäkerhetsgruppen

Skapa en regel för nätverkssäkerhetsgrupp som tillåter inkommande anslutningar via port 80.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupILB \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleHTTP \
    --protocol tcp \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 300
```
### <a name="create-nics"></a>Skapa nätverkskort

Skapa två nätverksgränssnitt med [az network nic create](/cli/azure/network/nic#az_network_nic_create) och koppla dem till den offentliga IP-adressen och nätverkssäkerhetsgruppen. 

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupILB \
    --name myNic$i \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
done
```

## <a name="create-backend-servers"></a>Skapa serverdelsservrar

I det här exemplet skapar du två virtuella datorer som ska användas som serverdelsservrar för belastningsutjämnaren. För att verifiera att belastningsutjämnaren har skapats, installerar du NGINX på de virtuella datorerna.

### <a name="create-an-availability-set"></a>Skapa en tillgänglighetsuppsättning

Skapa en tillgänglighetsuppsättning med [az vm availabilityset create](/cli/azure/network/nic#az_network_availabilityset_create)

 ```azurecli-interactive
  az vm availability-set create \
    --resource-group myResourceGroupILB \
    --name myAvailabilitySet
```

### <a name="create-two-virtual-machines"></a>Skapa två virtuella datorer

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
 
Skapa de virtuella datorerna med [az vm create](/cli/azure/vm#az_vm_create).

 ```azurecli-interactive
for i in `seq 1 2`; do
  az vm create \
    --resource-group myResourceGroupILB \
    --name myVM$i \
    --availability-set myAvailabilitySet \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
    done
```
Det kan ta några minuter för de virtuella datorerna att distribueras.

### <a name="create-a-vm-for-testing-the-load-balancer"></a>Skapa en virtuell dator för att testa belastningsutjämnaren

Skapa en virtuell dator för att testa belastningsutjämnaren *myVMTest* och koppla den till *myNic3*.

```azurecli-interactive
 az vm create \
    --resource-group myResourceGroupILB \
    --name myVMTest \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword123456!
```

## <a name="test-the-internal-load-balancer"></a>Testa den interna belastningsutjämnaren

Om du vill testa belastningsutjämnaren måste du först skaffa den privata IP-adressen för belastningsutjämnaren. Sedan loggar du in på den virtuella datorn myVMTest och skriver den privata IP-adressen i adressfältet i dess webbläsare.

Hämta den privata IP-adressen för belastningsutjämnaren med [az network lb show](/cli/azure/network/public-ip##az-network-lb-show). Kopiera den privata IP-adressen och klistra in den i adressfältet i en webbläsare på din virtuella dator – *myVMTest*.

```azurecli-interactive
  az network lb show \
    --name myLoadBalancer \
    --resource-group myResourceGroupILB
``` 
![Testa belastningsutjämnaren](./media/load-balancer-get-started-ilb-arm-cli/load-balancer-test.png)

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du ta bort resursgruppen, belastningsutjämnaren och alla relaterade resurser med kommandot [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive 
  az group delete --name myResourceGroupILB
```


## <a name="next-steps"></a>Nästa steg
I den här artikeln, skapade du en intern belastningsutjämnare, anslöt virtuella datorer till den, konfigurerade trafikregeln för belastningsutjämnaren, hälsoavsökning och testade därefter belastningsutjämnaren. Läs mer om belastningsutjämnare och deras associerade resurser i instruktionsartiklarna.
