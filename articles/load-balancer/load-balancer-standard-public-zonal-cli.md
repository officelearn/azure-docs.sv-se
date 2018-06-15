---
title: Skapa en offentlig belastningen belastningsutjämnaren Standard med zonal klientdel för offentlig IP-adress med hjälp av Azure CLI | Microsoft Docs
description: Lär dig hur du skapar en offentlig belastningen belastningsutjämnaren Standard med zonal klientdel för offentlig IP-adress med hjälp av Azure CLI
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: kumud
ms.openlocfilehash: 0932195bb95ab9610f723245bfed7fedb01001f9
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
ms.locfileid: "30324098"
---
#  <a name="create-a-public-load-balancer-standard-with-zonal-frontend-using-azure-cli"></a>Skapa en offentlig belastningen belastningsutjämnaren Standard med zonal klientdel med Azure CLI

Den här artikeln går igenom hur du skapar en offentlig [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) med en zonal klientdel med en Standard offentliga IP-adress. I det här scenariot kan du ange en viss zon för dina frontend och backend-instanser, justera dina datasökväg och resurser med en viss zon.

Mer information om hur du använder tillgänglighet zoner med Standard belastningsutjämnaren finns [Standard belastningsutjämnare och tillgänglighet zoner](load-balancer-standard-availability-zones.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt, kontrollerar du att du har installerat senast [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) och är inloggad på ett Azure-konto med [az inloggningen](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az_login).

> [!NOTE]
 Stöd för tillgänglighet zoner är tillgänglig för väljer Azure-resurser och regioner och familjer för VM-storlek. Mer information om hur du kommer igång och vilka Azure-resurser, regioner, och Virtuella storlek familjer kan du försöka tillgänglighet zoner med finns [översikt av tillgänglighet zoner](https://docs.microsoft.com/azure/availability-zones/az-overview). Du kan få support via [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) eller genom att [öppna ett Azure-supportärende](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med [az group create](/cli/azure/group#az_group_create). En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resursgrupp med namnet *myResourceGroupLB* i den *westeurope* plats:

```azurecli-interactive
az group create \
--name myResourceGroupLB \
--location westeurope
```

## <a name="create-a-zonal-public-ip-standard"></a>Skapa en zonal offentliga IP-Standard
För att kunna komma åt din app på Internet behöver du en offentlig IP-adress för belastningsutjämnaren. Det finns en offentlig IP-adress som skapas i en viss zon alltid endast i zonen. Det går inte att ändra zonen av en offentlig IP-adress.

Skapa en offentlig IP-adress med [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). I följande exempel skapas en zonal offentliga IP-adress med namnet *myPublicIP* i den *myResourceGroupLoadBalancer* resursgrupp i zonen 1.

```azurecli-interactive
az network public-ip create \
--resource-group myResourceGroupLB \
--name myPublicIP \
--sku Standard \
--zone 1
```

## <a name="create-azure-load-balancer-standard"></a>Skapa Azure Load Balancer Standard
Det här avsnittet beskrivs hur du kan skapa och konfigurera belastningsutjämnaren följande komponenter:
- klientdelens IP-poolen som tar emot inkommande nätverkstrafik på belastningsutjämnaren.
- en backend IP-adresspool om poolen klientdel skickar belastningen belastningsutjämnade trafik.
- en hälsoavsökningen som avgör hälsotillståndet för serverdelens VM-instanser.
- en regel för belastningsutjämnare som definierar hur trafiken distribueras till de virtuella datorerna.

### <a name="create-the-load-balancer"></a>Skapa belastningsutjämnaren
Skapa en Standard belastningsutjämnare med [az nätverket lb skapa](/cli/azure/network/lb#az_network_lb_create). I följande exempel skapas en belastningsutjämnare med namnet *myLoadBalancer* och tilldelar den *myPublicIP* adressen till frontend IP-konfigurationen.

```azurecli-interactive
az network lb create \
--resource-group myResourceGroupLB \
--name myLoadBalancer \
--public-ip-address myPublicIP \
--frontend-ip-name myFrontEndPool \
--backend-pool-name myBackEndPool \
--sku Standard
```

## <a name="create-health-probe-on-port-80"></a>Skapa hälsoavsökningen på port 80

En hälsoavsökning kontrollerar alla virtuella datorinstanser för att säkerställa att de kan skicka nätverkstrafik. Den virtuella datorinstansen med misslyckad hälsoavsökning tas bort från belastningsutjämnaren tills den är tillbaka online och en avsökningskontroll visar att den är felfri. Skapa en hälsoavsökningen med az nätverket lb avsökningen skapa för att övervaka hälsotillståndet hos de virtuella datorerna. Skapa en TCP-hälsoavsökning med [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create). I följande exempel skapas en hälsoavsökning med namnet *myHealthProbe*:

```azurecli-interactive
az network lb probe create \
--resource-group myResourceGroupLB \
--lb-name myLoadBalancer \
--name myHealthProbe \
--protocol tcp \
--port 80
```

## <a name="create-load-balancer-rule-for-port-80"></a>Skapa regel för belastningsutjämnare för port 80
En regel för belastningsutjämnare definierar frontend IP-konfiguration för inkommande trafik och backend-IP-adresspool för att ta emot trafik, tillsammans med nödvändig käll- och port. Skapa en regel för belastningsutjämnare *myLoadBalancerRuleWeb* med [az nätverket lb regeln skapa](/cli/azure/network/lb/rule#az_network_lb_rule_create) för att lyssna på port 80 i poolen klientdel *myFrontEndPool* och skicka belastningsutjämnad trafik till backend-adresspool *myBackEndPool* också använder port 80.

```azurecli-interactive
az network lb rule create \
--resource-group myResourceGroupLB \
--lb-name myLoadBalancer \
--name myLoadBalancerRuleWeb \
--protocol tcp \
--frontend-port 80 \
--backend-port 80 \
--frontend-ip-name myFrontEndPool \
--backend-pool-name myBackEndPool \
--probe-name myHealthProbe
```

## <a name="configure-virtual-network"></a>Konfigurera ett virtuellt nätverk
Innan du distribuerar vissa virtuella datorer och testa din belastningsutjämnare, skapa stödresurser för virtuellt nätverk.

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med namnet *myVnet* med ett undernät med namnet *mySubnet* i myResourceGroup med [az network vnet skapa](/cli/azure/network/vnet#az_network_vnet_create).


```azurecli-interactive
az network vnet create \
--resource-group myResourceGroupLB \
--location westeurope \
--name myVnet \
--subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

Skapa säkerhetsgrupp för nätverk med namnet *myNetworkSecurityGroup* att definiera inkommande anslutningar till det virtuella nätverket med [az nätverket nsg skapa](/cli/azure/network/nsg#az_network_nsg_create).

```azurecli-interactive
az network nsg create \
--resource-group myResourceGroupLB \
--name myNetworkSecurityGroup
```

Skapa en grupp nätverkssäkerhetsregeln med namnet *myNetworkSecurityGroupRule* för port 80 med [az nätverket nsg regeln skapa](/cli/azure/network/nsg/rule#az_network_nsg_rule_create).

```azurecli-interactive
az network nsg rule create \
--resource-group myResourceGroupLB \
--nsg-name myNetworkSecurityGroup \
--name myNetworkSecurityGroupRule \
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
Skapa tre virtuella nätverkskort med [az nätverket nic skapa](/cli/azure/network/nic#az_network_nic_create) och koppla dem till den offentliga IP-adressen och nätverkssäkerhetsgruppen. I följande exempel skapas tre virtuella nätverkskort. (Det vill säga ett virtuellt nätverkskort för varje virtuell dator som du skapar för din app i följande steg). Du kan skapa ytterligare virtuella nätverkskort och virtuella datorer när du vill och lägga till dem i belastningsutjämnaren:

```azurecli-interactive
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupLB \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```
## <a name="create-backend-servers"></a>Skapa backend-servrar
I det här exemplet kan du skapa tre virtuella datorer finns i zonen 1 som ska användas som backend-servrar för belastningsutjämnaren. Du kan också installera NGINX på de virtuella datorerna för att kontrollera att belastningsutjämnaren har skapats.

### <a name="create-cloud-init-config"></a>Skapa en cloud-init-konfiguration

Du kan använda en konfigurationsfil för molnet init för att installera NGINX och köra en ”Hello World” Node.js-app på en Linux-dator. Skapa en fil med namnet molnet init.txt och kopiera och klistra in följande konfiguration i gränssnittet i din aktuella shell. Kontrollera att du kopierar hela molnet init filen korrekt, särskilt den första raden:

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

### <a name="create-the-zonal-virtual-machines"></a>Skapa zonal virtuella datorer
Skapa de virtuella datorerna med [az vm skapa](/cli/azure/vm#az_vm_create). I följande exempel skapas tre virtuella datorer i zonen 1 och genererar SSH-nycklar, om de inte redan finns:

```azurecli-interactive
for i in `seq 1 3`; do
 az vm create \
--resource-group myResourceGroupLB \
--name myVM$i \
--nics myNic$i \
--image UbuntuLTS \
--generate-ssh-keys \
--zone 1 \
--custom-data cloud-init.txt
done
```

## <a name="test-the-load-balancer"></a>Testa belastningsutjämnaren
Hämta offentlig IP-adressen för belastningsutjämnaren belastningen med [az nätverket offentliga ip-visa](/cli/azure/network/public-ip#az_network_public_ip_show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 

Du kan sedan ange den offentliga IP-adressen i en webbläsare. Kom ihåg - det tar några minuter för de virtuella datorerna ska bli klar innan belastningsutjämnaren börjar distribuera trafiken till dem. Appen visas, inklusive värddatornamnet för den virtuella dator som belastningsutjämnaren distribuerade trafik till, som i följande exempel:

![Köra Node.js-app](./media/load-balancer-standard-public-zonal-cli/running-nodejs-app.png)

Om du vill se belastningsutjämnaren distribuera trafiken till virtuella datorer i zonen 1 som kör appen du kan force-uppdatera webbläsaren.

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [Standard belastningsutjämnaren](./load-balancer-standard-overview.md).



