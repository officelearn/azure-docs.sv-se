---
title: Standard Load Balancer med zonindelade offentlig IP-adress klient del – Azure CLI
titleSuffix: Azure Load Balancer
description: Lär dig hur du skapar en offentlig Standard Load Balancer med en offentlig IP-zonindelade med Azure CLI
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: allensu
ms.openlocfilehash: b9c3a88df6801566bc927cfc18fda0adfa05a5ae
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076034"
---
#  <a name="create-a-standard-load-balancer-with-zonal-public-ip-address-frontend-using-azure-cli"></a>Skapa en Standard Load Balancer med en offentlig IP-zonindelade med Azure CLI

Den här artikeln beskriver hur du skapar en offentlig [standard Load Balancer](https://aka.ms/azureloadbalancerstandard) med en zonindelade-frontend med en offentlig IP-standardadress. I det här scenariot anger du en viss zon för dina klientdels- och serverdelsinstanser för att justera din datasökväg och dina resurser med en viss zon.

Mer information om hur du använder tillgänglighetszoner med standardlastbalanserare finns i [Standard Load Balancer och tillgänglighet zoner](load-balancer-standard-availability-zones.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kontrollerar du att du har installerat senast [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) och är inloggad på ett Azure-konto med [az-inloggning](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest).

> [!NOTE]
>  Stöd för Tillgänglighetszoner är tillgänglig för väljer Azure-resurser och regioner och VM-storlekar. Mer information om hur du kommer igång och vilka Azure-resurser, regioner, och VM-storlekar som du kan prova tillgänglighetszoner med finns i [översikt över Tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-overview). Du kan få support via [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) eller genom att [öppna ett Azure-supportärende](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med [az group create](/cli/azure/group#az-group-create). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resurs grupp med namnet *myResourceGroupLB* på *westeurope* -platsen:

```azurecli-interactive
az group create \
--name myResourceGroupLB \
--location westeurope
```

## <a name="create-a-zonal-public-ip-standard"></a>Skapa en zonindelade offentlig IP-standard
För att kunna komma åt din app på Internet behöver du en offentlig IP-adress för lastbalanseraren. En offentlig IP-adress som skapas i en speciell zon finns alltid i den zonen. Det går inte att ändra zonen för en offentlig IP-adress.

Skapa en offentlig IP-adress med [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create). I följande exempel skapas en offentlig IP-zonindelade med namnet *myPublicIP* i resurs gruppen *myResourceGroupLoadBalancer* i zon 1.

```azurecli-interactive
az network public-ip create \
--resource-group myResourceGroupLB \
--name myPublicIP \
--sku Standard \
--zone 1
```

## <a name="create-azure-standard-load-balancer"></a>Skapa Azure-Standard Load Balancer
I det här avsnittet beskrivs hur du gör för att skapa och konfigurera följande komponenter i lastbalanseraren:
- en klientdels-IP-pool som tar emot inkommande nätverkstrafik i lastbalanseraren.
- en serverdels-IP-pool dit klientdelspoolen skickar den belastningsutjämnade nätverkstrafiken.
- en hälsoavsökning som fastställer hälsan för serverdelens virtuella datorinstanser.
- en lastbalanseringsregel som definierar hur trafiken ska distribueras till de virtuella datorerna.

### <a name="create-the-load-balancer"></a>Skapa lastbalanseraren
Skapa en Standard Load Balancer med [AZ Network lb Create](/cli/azure/network/lb#az-network-lb-create). Följande exempel skapar en belastningsutjämnare med namnet *myLoadBalancer* och tilldelar *myPublicIP* -adressen till klient delens IP-konfiguration.

```azurecli-interactive
az network lb create \
--resource-group myResourceGroupLB \
--name myLoadBalancer \
--public-ip-address myPublicIP \
--frontend-ip-name myFrontEndPool \
--backend-pool-name myBackEndPool \
--sku Standard
```

## <a name="create-health-probe-on-port-80"></a>Skapa hälso avsökning på port 80

En hälsoavsökning kontrollerar alla virtuella datorinstanser för att säkerställa att de kan skicka nätverkstrafik. Den virtuella datorinstansen med misslyckad hälsoavsökning tas bort från lastbalanseraren tills den är tillbaka online och en avsökningskontroll visar att den är felfri. Skapa en hälso avsökning med AZ Network lb PROBE Create för att övervaka hälso tillståndet för de virtuella datorerna. Skapa en TCP-hälsoavsökning med [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create). I följande exempel skapas en hälsoavsökning med namnet *myHealthProbe*:

```azurecli-interactive
az network lb probe create \
--resource-group myResourceGroupLB \
--lb-name myLoadBalancer \
--name myHealthProbe \
--protocol tcp \
--port 80
```

## <a name="create-load-balancer-rule-for-port-80"></a>Skapa belastnings Utjämnings regel för port 80
En lastbalanseringsregel definierar klientdelens IP-konfiguration för inkommande trafik och serverdelens IP-pool för att ta emot trafiken, tillsammans med nödvändiga käll- och målportar. Skapa lastbalanseringsregeln *myLoadBalancerRuleWeb* med [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) så att du kan lyssna på port 80 i klientdelspoolen *myFrontEndPool* och skicka lastbalanserad nätverkstrafik till serverdelsadresspoolen *myBackEndPool* som också använder port 80.

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
Innan du kan distribuera virtuella datorer och testa din lastbalanserare måste du skapa virtuella nätverksresurser.

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med namnet *myVnet* med ett undernät med namnet *mitt undernät* i myResourceGroup med [AZ Network VNet Create](/cli/azure/network/vnet#az-network-vnet-create).


```azurecli-interactive
az network vnet create \
--resource-group myResourceGroupLB \
--location westeurope \
--name myVnet \
--subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

Skapa nätverks säkerhets grupp med namnet *myNetworkSecurityGroup* för att definiera inkommande anslutningar till ditt virtuella nätverk med [AZ Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create).

```azurecli-interactive
az network nsg create \
--resource-group myResourceGroupLB \
--name myNetworkSecurityGroup
```

Skapa en regel för nätverks säkerhets grupp med namnet *myNetworkSecurityGroupRule* för port 80 med [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create).

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
Skapa tre virtuella nätverkskort med [AZ Network NIC Create](/cli/azure/network/nic#az-network-nic-create) och koppla dem till den offentliga IP-adressen och nätverks säkerhets gruppen. I följande exempel skapas tre virtuella nätverkskort. (Det vill säga ett virtuellt nätverkskort för varje virtuell dator som du skapar för din app i följande steg.) Du kan skapa ytterligare virtuella nätverkskort och virtuella datorer när du vill och lägga till dem i lastbalanseraren:

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
## <a name="create-backend-servers"></a>Skapa serverdelsservrar
I det här exemplet skapar du tre virtuella datorer som finns i zon 1 som ska användas som backend-servrar för belastningsutjämnaren. Du installerar även NGINX på de virtuella datorerna för att kontrol lera att belastningsutjämnaren har skapats.

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

### <a name="create-the-zonal-virtual-machines"></a>Skapa de virtuella zonindelade-datorerna
Skapa de virtuella [datorerna med AZ VM Create](/cli/azure/vm#az-vm-create). I följande exempel skapas tre virtuella datorer i zon 1 och genererar SSH-nycklar om de inte redan finns:

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

## <a name="test-the-load-balancer"></a>Testa lastbalanseraren
Hämta den offentliga IP-adressen för belastningsutjämnaren med [AZ Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 

Du kan sedan ange den offentliga IP-adressen i en webbläsare. Kom ihåg att det kan ta några minuter innan de virtuella datorerna är klara och lastbalanseraren börjar distribuera trafik till dem. Appen visas, inklusive värddatornamnet för den virtuella dator som lastbalanseraren distribuerade trafik till, som i följande exempel:

![Köra Node.js-app](./media/load-balancer-standard-public-zonal-cli/running-nodejs-app.png)

Om du vill se belastningsutjämnaren distribuerar trafik till virtuella datorer i zon 1 som kör din app, kan du framtvinga uppdatering av webbläsaren.

## <a name="next-steps"></a>Nästa steg
- Mer information finns i [Standard Load Balancer](./load-balancer-standard-overview.md).



