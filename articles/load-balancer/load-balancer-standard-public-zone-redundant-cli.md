---
title: Belastningsutjämna zonredundant virtuella datorer med Azure CLI | Microsoft Docs
description: Lär dig hur du skapar en offentlig Load Balancer Standard med zonen redundant klientdel med Azure CLI
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
ms.date: 03/09/2018
ms.author: kumud
ms.openlocfilehash: dbefe5324acb699abb0e06b8f3f464a91a6fa2e2
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431138"
---
#  <a name="load-balance-vms-across-all-availability-zones-using-azure-cli"></a>Belastningsutjämna virtuella datorer mellan alla tillgänglighetszoner med Azure CLI

Den här artikeln visar hur du skapar en offentlig [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) med en zonredundant klientdel att uppnå zonredundans utan beroenden på flera DNS-poster. En frontend IP-adress är automatiskt zonredundant.  Med en zon redundant klientdel för en belastningsutjämnare, med en enda IP-adress kan du nu nå virtuella datorer i ett virtuellt nätverk inom en region som ligger över alla Tillgänglighetszoner. Använd tillgänglighetszoner för att skydda dina appar och data från ett osannolikt fel eller förlust av ett helt datacenter.

Mer information om hur du använder tillgänglighetszoner med standardlastbalanserare finns i [Standard Load Balancer och tillgänglighet zoner](load-balancer-standard-availability-zones.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Om du väljer att installera och använda CLI lokalt måste den här självstudien kräver att du kör Azure CLI version 2.0.17 eller högre.  Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

> [!NOTE]
> Stöd för Tillgänglighetszoner är tillgänglig för väljer Azure-resurser och regioner och VM-storlekar. Mer information om hur du kommer igång och vilka Azure-resurser, regioner, och VM-storlekar som du kan prova tillgänglighetszoner med finns i [översikt över Tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-overview). Du kan få support via [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) eller genom att [öppna ett Azure-supportärende](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med [az group create](/cli/azure/group#az-group-create). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resursgrupp med namnet *myResourceGroupSLB* i den *westeurope* plats:

```azurecli-interactive
az group create \
--name myResourceGroupSLB \
--location westeurope
```

## <a name="create-a-zone-redundant-public-ip-standard"></a>Skapa en zonredundant offentlig IP-Standard
För att kunna komma åt din app på Internet behöver du en offentlig IP-adress för lastbalanseraren. En zonredundant frontend hanteras av alla tillgänglighetszoner i en region samtidigt. Skapa en zon redundant offentlig IP-adress med [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). När du skapar en offentlig IP-adress är zonredundant som standard.

I följande exempel skapas en zonen redundant offentliga IP-adress med namnet *myPublicIP* i den *myResourceGroupLoadBalancer* resursgrupp.

```azurecli-interactive
az network public-ip create \
--resource-group myResourceGroupSLB \
--name myPublicIP \
--sku Standard
```

## <a name="create-azure-load-balancer-standard"></a>Skapa Azure Load Balancer Standard
I det här avsnittet beskrivs hur du gör för att skapa och konfigurera följande komponenter i lastbalanseraren:
- en klientdels-IP-pool som tar emot inkommande nätverkstrafik i lastbalanseraren.
- en serverdels-IP-pool om klientdelspoolen skickar den belastningsutjämnade nätverkstrafiken.
- en hälsoavsökning som fastställer hälsan för serverdelens virtuella datorinstanser.
- en lastbalanseringsregel som definierar hur trafiken ska distribueras till de virtuella datorerna.

### <a name="create-the-load-balancer"></a>Skapa lastbalanseraren
Skapa en standardbelastningsutjämnare med [az network lb skapa](/cli/azure/network/lb#az-network-lb-create). I följande exempel skapas en belastningsutjämnare med namnet *myLoadBalancer* och tilldelar den *myPublicIP* adress till IP-konfigurationen.

```azurecli-interactive
az network lb create \
--resource-group myResourceGroupSLB \
--name myLoadBalancer \
--public-ip-address myPublicIP \
--frontend-ip-name myFrontEnd \
--backend-pool-name myBackEndPool \
--sku Standard
```

## <a name="create-health-probe-on-port-80"></a>Skapa en hälsoavsökning på port 80

En hälsoavsökning kontrollerar alla virtuella datorinstanser för att säkerställa att de kan skicka nätverkstrafik. Den virtuella datorinstansen med misslyckad hälsoavsökning tas bort från lastbalanseraren tills den är tillbaka online och en avsökningskontroll visar att den är felfri. Skapa en hälsoavsökning med az network lb probe skapa för att övervaka hälsotillståndet för de virtuella datorerna. Skapa en TCP-hälsoavsökning med [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create). I följande exempel skapas en hälsoavsökning med namnet *myHealthProbe*:

```azurecli-interactive
az network lb probe create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myHealthProbe \
--protocol tcp \
--port 80
```

## <a name="create-load-balancer-rule-for-port-80"></a>Skapa regel för belastningsutjämnaren för port 80
En lastbalanseringsregel definierar klientdelens IP-konfiguration för inkommande trafik och serverdelens IP-pool för att ta emot trafiken, tillsammans med nödvändiga käll- och målportar. Skapa lastbalanseringsregeln *myLoadBalancerRuleWeb* med [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) så att du kan lyssna på port 80 i klientdelspoolen *myFrontEndPool* och skicka lastbalanserad nätverkstrafik till serverdelsadresspoolen *myBackEndPool* som också använder port 80.

```azurecli-interactive
az network lb rule create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myLoadBalancerRuleWeb \
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

Skapa ett virtuellt nätverk med namnet *myVnet* med ett undernät med namnet *mySubnet* i myResourceGroup med [az network vnet skapa](/cli/azure/network/vnet#az-network-vnet-create).


```azurecli-interactive
az network vnet create \
--resource-group myResourceGroupSLB \
--location westeurope \
--name myVnet \
--subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

Skapa en nätverkssäkerhetsgrupp med namnet *myNetworkSecurityGroup* att definiera inkommande anslutningar till det virtuella nätverket med [az network nsg skapa](/cli/azure/network/nsg#az-network-nsg-create).

```azurecli-interactive
az network nsg create \
--resource-group myResourceGroupSLB \
--name myNetworkSecurityGroup
```

Skapa en nätverkssäkerhetsgruppregel med namnet *myNetworkSecurityGroupRule* för port 80 med [az network nsg-regel skapar](/cli/azure/network/nsg/rule#az-network-nsg-rule-create).

```azurecli-interactive
az network nsg rule create \
--resource-group myResourceGroupSLB \
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
Skapa tre virtuella nätverkskort med [az network nic skapa](/cli/azure/network/nic#az-network-nic-create) och koppla dem till offentliga IP-adressen och nätverkssäkerhetsgruppen. I följande exempel skapas sex virtuella nätverkskort. (Det vill säga ett virtuellt nätverkskort för varje virtuell dator som du skapar för din app i följande steg.) Du kan skapa ytterligare virtuella nätverkskort och virtuella datorer när du vill och lägga till dem i lastbalanseraren:

```azurecli-interactive
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupSLB \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```
## <a name="create-backend-servers"></a>Skapa serverdelsservrar
I det här exemplet skapar du tre virtuella datorer som finns i zon 1, zon 2 och zon 3 som ska användas som serverdelsservrar för belastningsutjämnaren. Du kan även installera NGINX på de virtuella datorerna för att verifiera att belastningsutjämnaren har skapats.

### <a name="create-cloud-init-config"></a>Skapa en cloud-init-konfiguration

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

### <a name="create-the-zonal-virtual-machines"></a>Skapa zonindelad virtuella datorer
Skapa de virtuella datorerna med [az vm skapa](/cli/azure/vm#az-vm-create) i zon 1, zon 2 och zon 3. I följande exempel skapas en virtuell dator i varje zon och genererar SSH-nycklar om de inte redan finns:

Skapa en virtuell dator i varje zon (zon 1, zon 2 och zon 3) för den *westeurope* plats.

```azurecli-interactive
for i in `seq 1 3`; do
  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --zone $i \
    --custom-data cloud-init.txt
done
```
## <a name="test-the-load-balancer"></a>Testa lastbalanseraren

Hämta den offentliga IP-adressen av load balancer med [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
Du kan sedan ange den offentliga IP-adressen i en webbläsare. Kom ihåg - det tar några minuter för de virtuella datorerna ska bli klar innan belastningsutjämnaren börjar distribuera trafiken till dem. Appen visas, inklusive värddatornamnet för den virtuella dator som lastbalanseraren distribuerade trafik till, som i följande exempel:

![Köra Node.js-app](./media/load-balancer-standard-public-zone-redundant-cli/running-nodejs-app.png)

Om du vill se hur belastningsutjämnaren distribuerar trafik över virtuella datorer i alla tre tillgänglighetszoner som kör din app, kan du stoppa en virtuell dator i en viss zon och uppdatera din webbläsare.

## <a name="next-steps"></a>Nästa steg
- Mer information finns i [Standard Load Balancer](./load-balancer-standard-overview.md)



